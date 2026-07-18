<b>About:</b> This tool was inspired by the huge amount of readily available data distributed across municipal and county REST endpoints. Recognizing the immense value of this data if properly packaged and delivered, I developed a proof-of-concept script to scrape public Esri REST directories and pipe the data directly into a personal Postgres/PostGIS database.

<b>Method:</b>
This script uses a recursive method to identify all available layers with a REST directory and add them to a dictionary. This dictionary is then used as an input for ETL from geojson directly into PostGIS. 

```python
import pandas as pd
import geopandas
import requests
import math
import sqlalchemy
import time
```


```python
def directory_crawl(root, service_dict = None):
    """
    Function that takes a REST directory root like
    https://website.domain/arcgis/rest/services,
    scrapes it, and returns a dictionary of every available
    service layer in the format {layer name : layer url}
    """
    time.sleep(0.5)
    if service_dict is None:
        service_dict = {}  
    json_directory = f"{root}?f=json"
    try:
        directory = requests.get(json_directory).json()
    except Exception as e:          
        print(f"Request for {root} failed:{e}")
        return service_dict

    json_walk(directory, root, service_dict)
    return service_dict


def json_walk(json_file, root, service_dict):
    """
    The main logic that parses JSON versions of each page
    Only configured to consume vector based feature and map services.
    """        
    if isinstance(json_file, dict):
            # Checks service page for map servives and appends them to root.
        if json_file.get("type") == "MapServer":
            servicename = json_file.get("name")
            service = servicename.split("/")[-1]
            directory_crawl(f"{root}/{service}/MapServer", service_dict)
            
        # Checks service page for feature servives and appends them to root.
        elif json_file.get("type") == "FeatureServer":                    
            servicename = json_file.get("name")
            service = servicename.split("/")[-1] 
            directory_crawl(fr"{root}/{service}/FeatureServer", service_dict)

        # Checks if the dictionary is a layer and adds final created url into output dict. 
        if isinstance(json_file.get("id"), int):
            service_dict[json_file.get("name")] = f"{root}/{json_file.get('id')}"
            print(f"{root}/{json_file.get('id')}")

        # Navigates the site root as well as the outer keys of the folder and service pages. 
        for key, val in json_file.items():
            if key.lower() == "folders" and "layers" not in json_file.keys():
                for foldername in val:                        
                    root_w_folder = fr'{root}/{foldername}'
                    directory_crawl(root_w_folder, service_dict)
            elif key.lower() == "services":
                json_walk(val, root, service_dict)               
            elif key.lower() == "layers":
                json_walk(val, root, service_dict)
        
    elif isinstance(json_file, list):
        for each in json_file:
            json_walk(each, root, service_dict)
```


```python
def service_to_postgis(url, name, db_schema, db_engine):
    chunk_list = []
    try:
        count = requests.get(f"{url}/query?where=1=1&returnCountonly=true&f=json").json()
    except Exception as e:          
        print(f"Request for {url} at count stage failed:{e}")
        return
    recordcount = count["count"]
    print(f'Service {name} has {recordcount} records')
    i = math.ceil(recordcount/1000)
    # Consider requesting PBF and deserializing into geojson
    try:
        initchunk = geopandas.read_file(
            f'{url}/query?where=1=1&f=geojson&outFields=*&resultRecordCount=1000&resultOffset=0&outSR=4326')
    except Exception as e:          
        print(f"Request for {url} at initial chunk stage failed:{e}")
    chunk_list.append(initchunk)
    for step in range(1, i):
        time.sleep(.25)
        print(step)
        try:
            chunk = geopandas.read_file(
                f'{url}/query?where=1=1&f=geojson&outFields=*&resultRecordCount=1000&resultOffset={iter*1000}&outSR=4326')
        except Exception as e:          
            print(f"Request for {url} at iteration stage failed:{e}")
            continue
        chunk_list.append(chunk)
    table = pd.concat(chunk_list, ignore_index = True)
    try:
        table.to_postgis(f"{name}", db_engine, if_exists="fail", schema = db_schema)
    except Exception as e:
        print(f' Failed to add table to database: {e}')
    print(f'{recordcount} records successfully added to database from {name}')   

```


```python
def scrape_rest_directory(root_url:str, db_schema:str, db_user:str, 
                         db_pass:str, host:str, port:int, db:str):
    directory_dict = directory_crawl(root_url)
    postgis_engine = sqlalchemy.create_engine(f"postgresql+psycopg2://{db_user}:{db_pass}@{host}:{port}/{db}")
    for service_name, service_url in directory_dict.items():
        try:
            service_to_postgis(service_url, service_name, db_schema, postgis_engine)
        except Exception as e:          
            print(f"Request for {service_url} failed:{e}")
            continue
    
```
