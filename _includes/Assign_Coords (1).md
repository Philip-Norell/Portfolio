```python
import arcpy
from collections import defaultdict
```


```python
# Loading files
billings_fishnet = r"\\PWU-W12k07\GIS_Files\Mapping_Projects\Projects\PRPL\2025\RFI_1064_Noxious_Weed_App\RFI_1064_Noxious_Weed_App\RFI_1064_Noxious_Weed_App.gdb\Billings_Parks_Fishnet"
county_fishnet = r"\\PWU-W12k07\GIS_Files\Mapping_Projects\Projects\PRPL\2025\RFI_1064_Noxious_Weed_App\RFI_1064_Noxious_Weed_App\RFI_1064_Noxious_Weed_App.gdb\YCO_Parks_Fishnet"
consolidated_fishnet = r"\\PWU-W12k07\GIS_Files\Mapping_Projects\Projects\PRPL\2025\RFI_1064_Noxious_Weed_App\RFI_1064_Noxious_Weed_App\RFI_1064_Noxious_Weed_App.gdb\Billings_YCO_Parks_Consolidated"

  

fields = ["QuadNS", "QuadEW", "Easting", "Northing", "ParkName"]
```


```python
parkname_list = []
parkname_unique = []
easting_list = []
northing_list = []

# Creating lists of to order and sort by in later cursors. 
with arcpy.da.UpdateCursor(billings_fishnet, fields, sql_clause = (None, "ORDER BY Easting ASC")) as cursor:  
    for row in cursor: 
        parkname_list.append(row[4])
        easting_list.append(row[2])
for name in parkname_list:
    if name not in parkname_unique:
        parkname_unique.append(name)
        

```


```python
def make_alphabet(n): # AI gen alphabet generator function. 
    labels = []
    while len(labels) < n:
        num = len(labels)
        label = ""
        while True:
            num, rem = divmod(num, 26)
            label = chr(65 + rem) + label
            if num == 0:
                break
            num -= 1
        labels.append(label)
    return labels

alphabet = make_alphabet(10400)


# Creates a default dict that assigns the next value in a list when it encounters a new easting or northing. 

for each in parkname_unique:
    alphabet_iter = iter(alphabet)
    easting_dict = defaultdict(lambda: next(alphabet_iter))
    number_iter = iter(range(1, 1000))
    northing_dict = defaultdict(lambda: next(number_iter))
    
    # Opens a cursor for the current park and assigns alphabet values for each column in the fishnet. 
    # Values start at "A" for the west most column and progress through the alphabet as the cursor moves east. 
    with arcpy.da.UpdateCursor(billings_fishnet, fields, where_clause = f"ParkName = '{each}'", sql_clause = (None, "ORDER BY Easting ASC")) as cursor:
        for row in cursor:
                
            Vert = row[0]
            Horiz = row[1]
            Easting = row[2]
            Northing = row[3]
            if Easting:
                standard_easting = round(Easting, 4)
                row[1] = easting_dict[standard_easting]
                cursor.updateRow(row)
            else:
                row[1] = None
                cursor.updateRow(row)

    # Same as above but with northing and rows.  
    # Values start at 1 for the north most row and increase as the cursor moves east.
    with arcpy.da.UpdateCursor(billings_fishnet, fields, where_clause = f"ParkName = '{each}'", sql_clause = (None, "ORDER BY Northing DESC")) as cursor:
        for row in cursor:
                
            Vert = row[0]
            Horiz = row[1]
            Easting = row[2]
            Northing = row[3]
            if Northing:
                standard_northing = round(Northing, 4)
                row[0] = northing_dict[Northing]
                cursor.updateRow(row)
            else:
                row[0] = None
                cursor.updateRow(row)           
```


```python
county_parkname_list = []
county_parkname_unique = []
county_easting_list = []
county_northing_list = []

with arcpy.da.UpdateCursor(county_fishnet, fields, sql_clause = (None, "ORDER BY Easting ASC")) as cursor:
    for row in cursor:
        county_parkname_list.append(row[4])
        county_easting_list.append(row[2])
    for name in county_parkname_list:
        if name not in county_parkname_unique:
            county_parkname_unique.append(name)

```


```python
for each in county_parkname_unique:
    alphabet_iter = iter(alphabet)
    easting_dict = defaultdict(lambda: next(alphabet_iter))
    number_iter = iter(range(1, 10000))
    northing_dict = defaultdict(lambda: next(number_iter))
    

    with arcpy.da.UpdateCursor(county_fishnet, fields, where_clause = f"ParkName = '{each}'", sql_clause = (None, "ORDER BY Easting ASC")) as cursor:
        for row in cursor:
                
            Vert = row[0]
            Horiz = row[1]
            Easting = row[2]
            Northing = row[3]
            ParkName = row[4]
            if Easting:
                standard_easting = round(Easting, 4)
                row[1] = easting_dict[standard_easting]
                cursor.updateRow(row)
            else:
                row[1] = None
                cursor.updateRow(row)
                
    with arcpy.da.UpdateCursor(county_fishnet, fields, where_clause = f"ParkName = '{each}'", sql_clause = (None, "ORDER BY Northing DESC")) as cursor:
        for row in cursor:
                
            Vert = row[0]
            Horiz = row[1]
            Easting = row[2]
            Northing = row[3]
            if Northing:
                standard_northing = round(Northing, 4)
                row[0] = northing_dict[standard_northing]
                cursor.updateRow(row)
            else:
                row[0] = None
                cursor.updateRow(row)           

```

   
    


```python
consolidated_parkname_list = []
consolidated_parkname_lower = []
consolidated_parkname_unique = []

with arcpy.da.UpdateCursor(consolidated_fishnet, fields, sql_clause = (None, "ORDER BY Easting ASC")) as cursor:
    for row in cursor:
        consolidated_parkname_list.append(row[4])
        
    for each in consolidated_parkname_list:
        consolidated_parkname_lower.append(each.lower())
        
    
    for name in consolidated_parkname_lower:
        if name not in consolidated_parkname_unique:
            consolidated_parkname_unique.append(name)

```


```python

for each in consolidated_parkname_unique:
    alphabet_iter = iter(alphabet)
    easting_dict = defaultdict(lambda: next(alphabet_iter))
    number_iter = iter(range(1, 1000))
    northing_dict = defaultdict(lambda: next(number_iter))
    
    
    with arcpy.da.UpdateCursor(consolidated_fishnet, fields, where_clause = f"ParkName = '{each}'", sql_clause = (None, "ORDER BY Easting ASC")) as cursor:
        for row in cursor:
                
            Vert = row[0]
            Horiz = row[1]
            Easting = row[2]
            Northing = row[3]
            ParkName = row[4]
            if Easting:
                standard_easting = round(Easting, 4)
                row[1] = easting_dict[standard_easting]
                cursor.updateRow(row)
            else:
                row[1] = None
                cursor.updateRow(row)
                

for each in consolidated_parkname_unique:
    alphabet_iter = iter(alphabet)
    easting_dict = defaultdict(lambda: next(alphabet_iter))
    number_iter = iter(range(1, 1000))
    northing_dict = defaultdict(lambda: next(number_iter))
                
    with arcpy.da.UpdateCursor(consolidated_fishnet, fields, where_clause = f"ParkName = '{each}'", sql_clause = (None, "ORDER BY Northing DESC")) as cursor:
        for row in cursor:
                
            Vert = row[0]
            Horiz = row[1]
            Easting = row[2]
            Northing = row[3]
            if Northing:
                standard_northing = round(Northing, 4)
                row[0] = northing_dict[standard_northing]
                cursor.updateRow(row)
            else:
                row[0] = None
                cursor.updateRow(row) 
             
            
with arcpy.da.UpdateCursor(consolidated_fishnet, fields) as cursor:
    for row in cursor:
        ParkName = row[4]
        if ParkName:
            row[4] = ParkName.title()
            cursor.updateRow(row)
        else: 
            row[4] = None
            cursor.updateRow(row)

```


```python
def parkid(Name):
    split_name = Name.split()
    for i in range(1):
        letter_list = []
        for each in split_name:
            first_letter = each[0]
            letter_list.append(first_letter)
            id_string = ", ".join(letter_list)
    return id_string
    
    
    
name = "Blue Creek Park"
x = parkid(name)
print(x)
```


```python

```
