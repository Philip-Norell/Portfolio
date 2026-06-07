---
layout: default
title: Philip Norell's Portfolio
---

# About me

I'm an early career GIS professional specializing in Enterprise and Web GIS. I enjoy solving difficult problems with a bit of research, a bit of code, and a bit of elbow grease!

I'm looking to contribute my skills to a forward-thinking organization I can be proud of as an employee. Please feel free to explore the tabs below.

<style>
/* Container Adjustments */
#main_content_wrap.outer {
  max-width: 4000px;
  width: 100%;
  margin-left: auto;
  margin-right: auto;
  background-image: url(); 
  background-size: cover;
  background-position: center;
  background-repeat: no-repeat;
  background-attachment: fixed;
}

#main_content.inner {
  max-width: 1000px;
  width: 95%;
  margin-left: auto;
  margin-right: auto;

  background-color: rgba(255, 255, 255, 0.85);
}

/* Container Adjustments */
#header_wrap.outer {
  max-width: 4000px;
  width: 100%;
  margin-left: auto;
  margin-right: auto;
}

#header_wrap .inner {
  max-width: 1000px;
  width: 95%;
  margin-left: auto;
  margin-right: auto;
}


/* Tabs styling */
.tabs {
  display: flex;
  border-bottom: 2px solid #ddd;
  margin-bottom: 1rem;
}

.tab-button {
  padding: 10px 20px;
  cursor: pointer;
  border: none;
  background: none;
  font-weight: bold;
  font-family: inherit;
  font-size: 1rem;
}

.tab-button.active {
  border-bottom: 3px solid #4CAF50;
  color: #4CAF50;
}

.tab-content {
  display: none;
  padding-top: 10px;
}

.tab-content.active {
  display: block;
}

/* Map specific styling */
.map-frame {
  border: 1px solid #ddd;
  border-radius: 8px;
  overflow: hidden;
  box-shadow: 0 4px 12px rgba(0,0,0,0.1);
  background: #fff;
}
</style>

<div class="tabs">
  <button class="tab-button active" onclick="openTab(event, 'tab1')">Esri REST Service ETL Pipeline</button>
  <button class="tab-button" onclick="openTab(event, 'tab2')">Data Governance Sample Script</button>
  <button class="tab-button" onclick="openTab(event, 'tab3')">Personal Interests</button>
  <button class="tab-button" onclick="openTab(event, 'tab4')">Other Examples of Work</button>
</div>

<div id="tab1" class="tab-content active"> 
  {% capture notebook %}
  {% include Rest_Endpoint_Data_Pipeline.md %}
  {% endcapture %}
  {{ notebook | markdownify }}
</div>

<div id="tab2" class="tab-content">
  {% capture notebook %}
  {% include PNorell_Dependency_Automator.md %}
  {% endcapture %}
  {{ notebook | markdownify }}
</div>


<div id="tab3" class="tab-content">
  <strong>I enjoy learning about and visualizing planning and urban design concepts, like in this map that displays data on</strong>
    <strong>zoning restrictiveness in over 2,800 municipalities across the United States.</strong>
    <p></p>
    <p>Click a municipality to view more information.</p>  
  <div class="map-frame">
    <iframe 
      src="zri_index_map.html" 
      width="100%" 
      height="750px" 
      style="border:none; display:block;" 
      loading="lazy">
    </iframe>
    </div>  
  <strong> I also extend my passion for mapping into the realm of fantasy!</strong>
  <div class="map-frame" style="width: 100%; max-width: 100%;">
    <img 
      src="FantasyMap-1.webp" 
      style="width: 100%; min-width: 100%; height: auto; display: block; border: none;"
    >
  </div>
</div>

<div id="tab4" class="tab-content">
  <strong> City of Billings Zoning Information Map </strong>
  <img src="Zoning info.png" style="width: 100%; min-width: 100%; height: auto; display: block; border: none;"
    >
  <a href = "https://experience.arcgis.com/experience/9e1c23b36950488687bc3478798fce95" target="_blank"><u>Click to View Full Map</u></a>
  <br> 
  <br>
  <strong> City of Billings Bike Lane and Multi Use Trail Map </strong>
  <img src="Bikemap.png" style="width: 100%; min-width: 100%; height: auto; display: block; border: none;"
    >
  <a href = "https://experience.arcgis.com/experience/9a1ccac651374d4081b5e5ad3996c064" target="_blank"><u>Click to View Full Map</u></a>
  <br> 
  <br>
  <strong> City of Billings Traffic Count Map </strong>
  <img src="TrafficCountMap.png" style="width: 100%; min-width: 100%; height: auto; display: block; border: none;"
    >
  <a href = "https://www.billingsmt.gov/DocumentCenter/View/55565/2025-Traffic-Count-Map" target="_blank"><u>Click to View Full Map</u></a>
  
</div>

<script>
function openTab(evt, tabId) {
  var contents = document.querySelectorAll('.tab-content');
  var buttons = document.querySelectorAll('.tab-button');

  contents.forEach(c => c.classList.remove('active'));
  buttons.forEach(b => b.classList.remove('active'));

  document.getElementById(tabId).classList.add('active');
  evt.currentTarget.classList.add('active');
}
</script>
