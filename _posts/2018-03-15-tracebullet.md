---
layout: post
title: Trace bullets and Petri dishes
---

It's alive! I’ve finally had some fun programming last weekend, enough of  graph concepts and modeling for now.  I’ve put together just a few lines of code, but I was able to build a trace bullet. From a Geodatabase to Graph Database, features to nodes, topological relations to edges.

![_config.yml]({{ site.baseurl }}/images/blulletsANDdishes.png)

For the ones not used with the “trace bullet” meaning when it comes to programming, it is a quick and not meant to production piece of code that proves one concept from the beginning to the end (1). I’m proposing to read features and topological relations from a Spatial Database and transform it into nodes and edges in a Graph Database. And that's exactly what this little program does, completely agnostic and oblivious to transactions, exception handling, parametrization, reuse, and others programming best practices and patterns. As I've said before, not meant to production environments. It is just to make sure it can be done and as a Petri dish for concepts and risk mitigations.

These first lines of code, I’m sharing it on GitHub under my Geo2graph repository, along with the necessary sample shapefiles, in a Jupyter Notebook format.  In a few weeks from now it will blossom into a beautiful Tool for Arc ToolBox. It requires a ArcGIS Desktop Advanced license, to use the Near Geoprocessing Tool, and a Neo4j (2) Database.

This first sample subject is the proximity relations between pipelines and earthquakes in Canada.  For the entry data we have a point shapefile with geographical locations, depth  and magnitude of earthquakes occurrences from 2010 to 2017 and pipeline segments on a line geometry shapefile with type, status and company. The goal is to build a graph database to answer two simple questions: which earthquake occurrences has posed as a threat to more than one pipeline? And, considering the historical records which pipeline has the highest exposition to earthquakes? To be able to get these questions answered this simple code performs the following steps:

1. Creates the Near relation between earthquake, with magnitude 3 or higher, and pipelines within 1.5 km maximum distance from the earthquake.
```python
arcpy.GenerateNearTable_analysis(in_features, [near_features], out_table, search_radius, 
                                 location, angle, closest, closest_count)
```
1. Iterates Earthquakes spatial dataset and for each earthquake row creates a new Node in the graph database labeled as Threat Mechanism;
```python
cursor = arcpy.SearchCursor(in_features,
                            where_clause="magnitude  >= 3",
                            fields="FID; depth; magnitude")
for row in cursor:
    create_earthquake_cypher = "CREATE (e:Threat_Mechanism " \
                               "{fid:$fid, depth:$depth, magnitude:$magnitude} )"
        
    with driver.session() as session:
        fid = row.getValue("FID")
        depth = row.getValue("depth")
        magnitude = row.getValue("magnitude")
        
        session.run(create_earthquake_cypher, fid=fid, depth=depth, magnitude=magnitude)'''
```
1. Iterates Pipeline spatial dataset and for each pipeline row creates a new Node in the graph database labeled as Pipeline;
```python
cursor = arcpy.SearchCursor(near_features,
                            fields="FID; PROJECT_NU; SEGMENT_NU; LINE_TYPE_; STATUS; PROPONENT")
for row in cursor:
    create_pipeline_cypher = "CREATE (p:Pipeline " \
                             "{fid:$fid, project_id:$project_id, segment_id:$segment_id, " \
                             " line_type:$line_type, status:$status, operator:$operator} )"
        
    with driver.session() as session:
        fid = row.getValue("FID")
        project_id = row.getValue("PROJECT_NU")
        segment_id = row.getValue("SEGMENT_NU")
        line_type = row.getValue("LINE_TYPE_")
        status = row.getValue("STATUS")
        operator = row.getValue("PROPONENT")
        
        session.run(create_pipeline_cypher, fid=fid, project_id=project_id, 
                    segment_id=segment_id, line_type=line_type, status=status, 
                    operator=operator)```
1. Iterates Near relations (step 1) and for each near relation between earthquake and pipeline gets the respective Pipeline and Earthquake Nodes and create an Edge with distance as an attribute;
```python
cursor = arcpy.SearchCursor(out_table)
for row in cursor:
    create_threat_cypher = "MATCH (e:Threat_Mechanism {fid:$e_fid}) " \
                           "MATCH (p:Pipeline {fid:$p_fid}) " \
                           "CREATE (e)-[:Threats {distance:$distance}]->(p)"
        
    with driver.session() as session:
        e_fid = row.getValue("IN_FID")
        p_fid = row.getValue("NEAR_FID")
        distance = row.getValue("NEAR_DIST")
        
        session.run(create_threat_cypher, e_fid=e_fid, p_fid=p_fid, distance=distance)```
1. Deletes all Earthquakes Nodes without connections to Pipelines.
```python
driver.session().run("MATCH (n:Threat_Mechanism) WHERE size((n)--())=0 DELETE (n)")
```

The reader can find the complete code [here](https://github.com/carloseduardotoledo/geo2graph/blob/master/geo2graph_arcgistool/geo2graph_sandbox.ipynb) 

Take care!

(1) In real life trace bullets are used by shooters to  visually follow the bullet trajectory at night from shooter to target and get a better aim.
(2) It's worth noting how easy was to get Neo4j up and running in my micro AWS EC2 instance.  After I've got Docker installed, I've pulled the Neo4j image into Docker, started it and I was good to go.
