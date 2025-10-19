# Geo-processing ZIP Code Tabulation Area (ZCTA) Data to Add to the Massachusetts Geo-Database

This folder will hold processing scripts and outputs for preparing Census ZCTA data for the Massachusetts geo-database.

Student Name: Krishna C. Mummadi
Date 

Objective: For this assignment a Methodological approach is followed which means the data being processed in a structured, systematic way that follwos plan andpurpose for collecting, cleaning, structuring and transforming data to ensure accuracy and reliability for analysis. Multiple geographies are integrated together, forming new geometries for the purspose of calculating geographic inforamtion on distinct land types, which are the urbanized and non-urbanized areas within a larger geography. Key operations performed are spatial ovelays, joins, usage of field calculator, transfer data between attribute tables. 

Data:
mass_trac_crashes_2010_aeac84

mass_eot_roads_aeac84

new_england_urbanized_areas_2010_aeac84

mass_zctas_2010_aeac84

mass_townships_2010_aeac84

mass_state_2010_aeac84

## Tasks
Complete the geo-processing of the Massachusetts ZIP Code Tabulation Areas (ZCTA) layer that was geometrically matched to the Township layer. The geo- processing of this single layer uses several tools and follows the method demonstrated in class. The final results with be a production ready ZCTA layer that contains the 
- (i) urbanized & non-urbanized areas, 
- (ii) five collision group counts, 
- (iii) and road lengths, with each of ii and iii be by urbanized and non-urbanized area


## Part I: Integrating Geographies and Calculating Area Types

### Geo-Process the ZCTA layer match the Massachusetts Township Layer

Tool: Clip
- Input: mass_zctas_2010_aeac84
- Clip features: mass_townships_2010_aeac84
- Output: mass_zctas_2010_aeac84_c1
- Output description: The ZCTA layer matches the township layer, the ZCTA layer has holes that are preserved in this process. When checked the holes are waterbodies, park area. As it is a zipcode layer, it may only have the servicable locations, that could be the reason for not having the data.

### Integrate the New England Urbanized Area layer into the Massachusetts ZCTA layer
Tool: Identity
- input: mass_zctas_2010_aeac84_c1
- Identity: new_england_urbanized_areas_2010_aeac84
- output: mass_zctas_2010_aeac84_idty

### Create two new fields that hold the amount of each area and calculate (transfer) the corresponding area into those new fields.
In the mass_zctas_2010_aeac84_idty layer
- Add two fields
- AREA_URB   Double
- AREA_NURB  Double

###  Calculate the amount of area that is urbanized and non-urbanized area. 
Select by attributes for Non-Urban Areas
- FID_new_england_urbanized_areas_2010_aeac84 is equal to -1


Tool: Calculate Field
- Input table: mass_zctas_2010_aeac84_idty
- Use the selected records: 454
- Field Name: AREA_NURB
Expression:
- AREA_NURB = !Shape_Area!
- Output description: It copies the shape area of all the non urban areas

For populating the shape areas of the Urban areas no need to use select by attributes again, but from present selection click switch, it switches the selction to the rows which value is not -1. The values that are not -1 are the urban areas.

Tool: Calculate Field:
- Input table: mass_zctas_2010_aeac84_idty
- Use the selected records: 544
- Field Name: AREA_URB
Expression:
- AREA_URB = !Shape_Area!
- Output description: It populates the AREA_URB with the shape_area rows. Basically It copies the urban area values in it.


There are null's in both the AREA_URB AND AREA_NURB fields they need to be converted to 0

Tool: Select by attributes
- Input Rows: mass_zctas_2010_aeac84_idty
- Where
- AREA_URB  is null


Tool: Calculate Field:
- Input table: mass_zctas_2010_aeac84_idty
- Use the selected rows: 454
Expression:
- AREA_URB = 0


Repeat the same for the AREA_NURB Field, first perform the select by attributes to check the rows that are null then.

Tool:  Calculate Field:
- Input table: mass_zctas_2010_aeac84_idty
- Use the selected rows: 544
Expression:
- AREA_NURB = 0


### Summarize –i.e. aggregate by geography– the integrated ZCTA layer on the urbanized and non-urbanized area to create a table that contains one record for each ZCTA

Tool: Summary Statistics
- Input table: mass_zctas_2010_aeac84_idty
- output table: mass_zctas_2010_aeac84_idty_smry
Statistis fields
- Field:
- AREA_NURB   SUM
- AREA_URB    SUM
Case Fields:
- ZCTA5CE10
It is the common field between the integratede layer and the ZCTA layer which represents the geography. It gives the Urban and Non-Urban area for each geography

### Join the summarized urban/non-urban area table to the ZCTA layer and make the join permanent

Tool: Join field
- Input table: mass_zctas_2010_aeac84_c1
- Input field: ZCTA5CE10
- Join table: mass_zctas_2010_aeac84_idty_smry
- Join field: ZCTA5CE10
Transfer fields:
- FREQUENCY
- SUM_AREA_URB
- SUM_AREA_NURB
- Output description: It permanently joins the summary fields to the ZCTAS layer that matches the township layer, where for each geogrpahy the Sum urbanized and Non-Urbaned areas is obtained.

### Properly name all new fields in the updated ZCTA layer for the land types. That is, give the fields meaningful names so that a user can readily determine what data are in those fields.

Tool: Alter Field
- Input field: mass_zctas_2010_aeac84_c1
- Field Name: FREQUENCY
- New Field Name: CNT_AREA_PIECES
- New Field Alias: CNT_AREA_PIECES

For SUM_AREA_URB
The layer is in Albers Equal Area Conic projection, so the numerical unit is in meters
- Field Name: SUM_AREA_URB
- New Field Name: URBAN_AREA_M2
- New Field ALias: URBAN_AREA_M2

For SUM_AREA_NURB
- Field Name: SUM_AREA_NURB
- New Field Name: NON_URBAN_AREA_M2
- New Field ALias: NON_URBAN_AREA_M2


### Clean up the attribute table in the updated ZCTA layer of all unnecessary fields created during (i) geo-processing, (ii) permanent attribute table joins, or (iii) temporarily created from field calculations. 

Unnecessary fields removed 
Layer: mass_zctas_2010_aeac84_idty
- OBJECTID
- Shape_Leng from the new_england_urbanized_areas which is not required

In the layer mass_zctas_2010_aeac84_c1, the fields are filtered in the Join Field operation, it looks clean


## Part II: Spatially Joining the Collision Groups.

### Spatially join and summarize the collision to the Integrated ZCTA and urbanized area layer (from Part 1, Step 1) for the five collision type groups to the urbanized and non-urbanized area. The summarization should only include the counts for each collision group. 
































