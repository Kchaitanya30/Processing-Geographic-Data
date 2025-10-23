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

- There are some holes inside the ZCTA orginal layer they need to filled and the idea is to give the township shape to the ZCTA layer but it misses few slivers and few polygons. Hence those missing parts and slivers needs to be added tot he clipped ZCTA so that it will match the township layer.

Tool: Erase
Input: mass_townships_2010_aeac84
Erase: mass_zctas_2010_aeac84_c1
Output: mass_townships_2010_aeac84_e1
Output description: The output has the portions of the input features( township ) falling outside the erase features ( ZCTA Clip ) 

- In the Erased layer, all the portions of the township layer are not divided, they need to be seperated.
Tool: Multipart to Single part
Purpose: To crate a feature class of single part features by separating multipart input features
Input features: mass_townships_2010_aeac84_e1
Output: mass_townships_2010_aeac84_mts
Output: It divides all the multipart features to the single part means it makes each polygons seperate.



Tool: Near
Purpose: First vertices of the one polygon and first vertices of the another polygon will be identified
Input: mass_townships_2010_aeac84_mts
Near features: mass_zctas_2010_aeac84_c1
Check Location
Distance type: Meters ( As the layer's linear unit is Meters)
Field Name>
Feature ID: NEAR_FID
Distance: NEAR_DIST
Location x-coordinate: Near_X
Location y-coordinate: Near_Y
Output description: This adds the NEAR_FID, NEAR_DIST, NEAR_X, and NEAR_Y fields to the mass_townships_2010_aeac84_mts layer
It checks the nearest distance from the clip layer to the multipart layer

Now all the slivers got its nearest polygons NEAR_FID, now mts table needs to joined to the clip layer so that it can be merged

Tool: Join
Purpose: To add the fields of the mass_townships_2010_aeac84_mts layers, so that it is helpful for merging. In the merge operation it does not add the fields of the both layers so it would be an issue while doing dissolve
Input: mass_zctas_2010_aeac84_c1
Input: OBJECTID
Join table: mass_townships_2010_aeac84_mts
Join field: NEAR_FID
Transfer fields:
Select All fields are selected expect shape_area and shape_length, as they are redundant
Output Description: In the mass_zctas_2010_aeac84_c1 layer there are many nulls. The cells with nulls means the polygons are not attached to any township pieces. 


Tool: Merge
Purpose: To combine multiple polygons so that it fills all the missing parts in the clipped ZCTA layer
Input: mass_zctas_2010_aeac84_c1
mass_townships_2010_aeac84_mts
output: mass_zctas_2010_aeac84_m1
Output Description: It merged both the polygons and it also has all the fileds from the input clip ZCTA layer. 


Tool: Check Geometry
Purpose: It generates a repost fo geometry problems in a feature class
input: mass_zctas_2010_aeac84_m1
Output Description: Warning 000442: self intersections at 171 in mass_zctas_2010_aeac84_m1 . It is showing error that mean at 171 it got looped itself


Tool: Repair Geometry
Purpose: It inspects features for geometry problems and repairs them. 
input: mass_zctas_2010_aeac84_m1
Check delete features with null geometry
Enable undo, for our safety if the layer get corrupted
Risk is when we try to do it, it may corrupt the layer
Output description: It repairs the geometry issues and give a clear layer. Check the geometry again to confirm if the error is solved


we have a situation now, When we joined the Clip layer and Multipointtosinglepoint layer, it added nulls for the NEAR_FID. They need to be populated with the OBJECTID so that it helps in dissolve

Tool: Select by attributes
Purpose: To select the rows that are null
input: mass_zctas_2010_aeac84_m1
Where  NEAR_FID = NULL
Ouput description: It selects all the rows of the NEAR_FID that are null

Layer: mass_zctas_2010_aeac84_m1
Right click on the NEAR_FID
Tool: Calcualte Field
NEAR_FID = !OBJECTID!
Output Description: It populates all the values of the OBJECTID to the nulls in the NEAR_FID field. It helps in dissolve process, if this is not done it dissolves all the null values in the NEAR_FID, this proces avoids it


Tool: Dissolve
Purpose: Aggregates features based on specified attributes, the plan is to dissolve all the slivers to the ZCTA layer
input: mass_zctas_2010_aeac84_m1
Dissolve Fields: NEAR_FID
Check Create multipart feature
Don’t check Unsplit — it could create slivers
Based on a common field or identifier, it dissolves features
Output: mass_zctas_2010_aeac84_v2
Output description: It is a one-to-many relationship, dissolves all the rows that are similar. Finally it matches the township layer. But it doesnot have all the fields

Tool: Join
Purpose: To permanently join the contents of two tables based on a common attribute
Input: mass_zctas_2010_aeac84_v2
Input field: NEAR_FID
Join Table: mass_zctas_2010_aeac84
Join field: OBJECTID
Output description: It joins all the fields of the join table to the input table, It matches the township layer shape and it has all the fields of the ZCTA layer


### Integrate the New England Urbanized Area layer into the Massachusetts ZCTA layer
Tool: Identity
Purpose: To create a geometric intersection of the input features and identity features. The input featues or portions thereof that overlap identity features will get the attributes of those identity features
- input: mass_zctas_2010_aeac84_v2
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
- Output description: It populates all the nulls in the AREA_URB field with 0

Repeat the same for the AREA_NURB Field, first perform the select by attributes to check the rows that are null then.

Tool:  Calculate Field:
- Input table: mass_zctas_2010_aeac84_idty
- Use the selected rows: 544
Expression:
- AREA_NURB = 0
- Output description: It populates all the nulls with 0

### Summarize –i.e. aggregate by geography– the integrated ZCTA layer on the urbanized and non-urbanized area to create a table that contains one record for each ZCTA

Tool: Summary Statistics
- Purpose: To calculate summary statistics for fields in a table
- Input table: mass_zctas_2010_aeac84_idty
- output table: mass_zctas_2010_aeac84_idty_smry
Statistis fields
- Field:
- AREA_NURB   SUM
- AREA_URB    SUM
Case Fields:
- ZCTA5CE10
It is the common field between the integratede layer and the ZCTA layer which represents the geography. It gives the Urban and Non-Urban area for each geography
-Output description: It gives the sum area of urban and non-urban areas inthe layer and also give the frequency of each row.

### Join the summarized urban/non-urban area table to the ZCTA layer and make the join permanent

Tool: Join field
- Purpose: To permanently join the two tables
- Input table: mass_zctas_2010_aeac84_v2
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
- Purpose: It renames fields and fields aliases without changing its position in the table
- Input table: mass_zctas_2010_aeac84_v2
- Field Name: FREQUENCY
- New Field Name: CNT_AREA_PIECES
- New Field Alias: CNT_AREA_PIECES

For SUM_AREA_URB
The layer is in Albers Equal Area Conic projection, so the numerical unit is in meters
- Purpose: It renames fields and fields aliases without changing its position in the table
- Field Name: SUM_AREA_URB
- New Field Name: URBAN_AREA_M2
- New Field ALias: URBAN_AREA_M2

For SUM_AREA_NURB
- Purpose: It renames fields and fields aliases without changing its position in the table
- Field Name: SUM_AREA_NURB
- New Field Name: NON_URBAN_AREA_M2
- New Field ALias: NON_URBAN_AREA_M2


### Clean up the attribute table in the updated ZCTA layer of all unnecessary fields created during (i) geo-processing, (ii) permanent attribute table joins, or (iii) temporarily created from field calculations. 

Unnecessary fields removed 
Layer: mass_zctas_2010_aeac84_idty
- OBJECTID
- Shape_Leng from the new_england_urbanized_areas which is not required

- Layer: mass_zctas_2010_aeac84_v2
- Delete - NEAR_FID, It is a duplicate of the OBJECID


## Part II: Spatially Joining the Collision Groups.

### Spatially join and summarize the collision to the Integrated ZCTA and urbanized area layer (from Part 1, Step 1) for the five collision type groups to the urbanized and non-urbanized area. The summarization should only include the counts for each collision group. 


#### Assigning Collision Types to Areal Geographies
Mask the data and make only visible the observations that are required

Tool: Defination query
Purpose: To separate the locations so that each collision type can be processed individually.
Layer: mass_trac_crashes_2010_aeac84
Procedure: From the Layer Table of Contents, select the Properties option to open the dialog box. In it, choose New Definition Query
Where: MannerColl_Grp is equal to ANGL
Outcome Description: Filters and displays only the records corresponding to the ANGL collision type.

Once the Definition Query has been applied, the selected locations need to be aggregated using a spatial join. Aggregate the individual and grouped collision types with a Spatial Join from the mass_trac_crashes_2010_aeac84 layer to the Integrated ZCTA and urbanized area layer (mass_zctas_2010_aeac84_idty).


Spatially joining Angel collision type and Adding the summarized collision count to the Integrated ZCTA layer

Tool: Spatial Join
Purpose: To join the township_and_urbanized layer with the crash points. This operation performs a point-in-polygon process that calculates the count of all crash locations within each township polygon.

Target Features: mass_zctas_2010_aeac84_idty
Join Features: mass_trac_crashes_2010_aeac84
Use the filtered records: 31,631
Join Operation: Join one to one
Match Option: Intersect
Output: mass_zctas_2010_aeac84_idty_ANGL
Field Selection: Delete all fields from the join layer except OBJECTID, ID, CityTown, MannerColl, and MannerCol_ctgy. OBJECTID and other redundant fields can be removed later if needed.
Output Description: The resulting layer contains all ANGL-filtered crash points joined with the Integrated ZCTA layer. In output layer delete Target_FID it is the Objectid of the joined layer, which is redundantL


Alter Field
Purpose: To change the name of the field without changing its position in the table

Input table: mass_zctas_2010_aeac84_idty_ANGL
Field Name: Join_Count
New field Name: CNT_ANGL
New Field Alias;CNT_ANGL
Output Description: The field name was successfully changed to CNT_ANGL.

In the mass_zctas_2010_aeac84_idty_ANGL layer's Attribute table
Add two new fields

Tool: Add Field
Purpose: To create new fields in the attribute table for storing counts of urban and non-urban collisions.
Field Name: CNT_ANGL_URB
Alias: CNT_ANGL_URB
Data Type: long
Create another field named CNT_ANGL_NURB using the same parameters.

Tool: Calcualte Field
purpose: To populate all the null values in the fields to the 0 so that it would be easy to copy other values in it.
input layer: mass_zctas_2010_aeac84_idty_ANGL
For both CNT_ANGL_URB AND CNT_ANGL_NURB 
Convert all nulls to 0


#### Calculating ANGL Collision Counts for Urban and Non-Urban Areas
Select by attributes
Purpose: To isolate polygons representing non-urbanized areas.

Input Rows: mass_zctas_2010_aeac84_idty_ANGL
Where: FID_new_england_urbanized_areas_2010_aeac84 is equal to -1
Outcome Description: Selects all rows where FID_new_england_urbanized_areas_2010_aeac84 equals -1, identifying polygons that represent non-urbanized areas.
Calculate Field:
Right Click on CNT_ANGL_NURB and choose Calculate field
Input table: mass_zctas_2010_aeac84_idty_ANGL
Use the selected records: 454
Field Name: CNT_ANGL_NURB
Expression:
Then in it select CNT_ANGL_NURB = !CNT_ANGL! Outcome desciption: It populates all the values that are selected in the CNT_ANGL to the CNT_ANGL_NURB
By this the Count of ANGL collisions that occured inthe Non urban areas is acquired
** Use Switch in the Attribute table, it selects all the rows that are not -1, which means the polygons that are urbanized **

Calcualte Field
Right Click on CNT_ANGL_URB and choose Calculate field

Input table: new_england_urbanized_areas_township_idty_massTrac_ANGL
Use the selected records: 544
Field Name: CNT_ANGL_URB
Expression:
Then in it select CNT_ANGL_URB = !CNT_ANGL! Output description: It populates the Count of collisions in the urbanized areas

** In the same way, the process is carried out for the remaining collision types. The procedure is repetitive and follows the same sequence of steps. The detailed process for each collision type is provided below **



Spatially joining End to End collision type and Adding the summarized collision count to the Integrated ZCTA layer

Tool: Spatial Join
Purpose: To join the township_and_urbanized layer with the crash points. This operation performs a point-in-polygon process that calculates the count of all crash locations within each township polygon.

Target Features: mass_zctas_2010_aeac84_idty_ANGL
Join Features: mass_trac_crashes_2010_aeac84
Use the filtered records: 35,213
Join Operation: Join one to one
Match Option: Intersect
Output: mass_zctas_2010_aeac84_idty_ANGL_ETE
Field Selection: Delete all fields from the join layer except  MannerCol_ctgy1
Output Description: The resulting layer contains all End to End -filtered crash points joined with the Integrated ZCTA polygons. In the output layer delete Target_FID it is the Objectid of the joined layer, which is redundant


Alter Field
Purpose: To change the name of the field without changing its position in the table

Input table: mass_zctas_2010_aeac84_idty_ANGL_ETE
Field Name: Join_Count
New field Name: CNT_ETE
New Field Alias: CNT_ETE
Output Description: The field name was successfully changed to CNT_ETE.

In the mass_zctas_2010_aeac84_idty_ANGL_ETE layer's Attribute table
Add two new fields

Tool: Add Field
Purpose: To create new fields in the attribute table for storing counts of urban and non-urban collisions.
Field Name: CNT_ETE_URB
Alias: CNT_ETE_URB
Data Type: long
Create another field named CNT_ETE_NURB using the same parameters.

Tool: Calcualte Field
purpose: To populate all the null values in the fields to the 0 so that it would be easy to copy other values in it.
input layer: mass_zctas_2010_aeac84_idty_ANGL_ETE
For both CNT_ETE_URB AND CNT_ETE_NURB 
Convert all nulls to 0


#### Calculating ETE Collision Counts for Urban and Non-Urban Areas
Select by attributes
Purpose: To isolate polygons representing non-urbanized areas.

Input Rows: mass_zctas_2010_aeac84_idty_ANGL_ETE
Where: FID_new_england_urbanized_areas_2010_aeac84 is equal to -1
Outcome Description: Selects all rows where FID_new_england_urbanized_areas_2010_aeac84 equals -1, identifying polygons that represent non-urbanized areas.
Calculate Field:
Right Click on CNT_ETE_NURB and choose Calculate field
Input table: mass_zctas_2010_aeac84_idty_ANGL_ETE
Use the selected records: 454
Field Name: CNT_ETE_NURB
Expression:
Then in it select CNT_ETE_NURB = !CNT_ETE! Outcome desciption: It populates all the values that are selected in the CNT_ETE to the CNT_ETE_NURB
By this the Count of ETE collisions that occured inthe Non urban areas is acquired
** Use Switch in the Attribute table, it selects all the rows that are not -1, which means the polygons that are urbanized **


Calcualte Field
Right Click on CNT_ETE_URB and choose Calculate field

Input table: mass_zctas_2010_aeac84_idty_ANGL_ETE
Use the selected records: 544
Field Name: CNT_ETE_URB
Expression:
Then in it select CNT_ETE_URB = !CNT_ETE! Output description: It populates the Count of collisions in the urbanized areas






Spatially joining Sideswipe collision type and Adding the summarized collision count to the Integrated ZCTA layer

Tool: Spatial Join
Purpose: To join the township_and_urbanized layer with the crash points. This operation performs a point-in-polygon process that calculates the count of all crash locations within each township polygon.

Target Features: mass_zctas_2010_aeac84_idty_ANGL_ETE_OV
Join Features: mass_trac_crashes_2010_aeac84
Use the filtered records: 12,709
Join Operation: Join one to one
Match Option: Intersect
Output: mass_zctas_2010_aeac84_idty_ANGL_ETE_OV_OTHR_SWP
Field Selection: Delete all fields from the join layer except  MannerCol_ctgy1
Output Description: The resulting layer contains all End to End -filtered crash points joined with the Integrated ZCTA polygons. In the output layer delete Target_FID it is the Objectid of the joined layer, which is redundant


Alter Field
Purpose: To change the name of the field without changing its position in the table

Input table: mass_zctas_2010_aeac84_idty_ANGL_ETE_OV_OTHR_SWP
Field Name: Join_Count
New field Name: CNT_SWP
New Field Alias: CNT_SWP
Output Description: The field name was successfully changed to CNT_SWP.

In the mass_zctas_2010_aeac84_idty_ANGL_ETE_OV_OTHR_SWP layer's Attribute table
Add two new fields

Tool: Add Field
Purpose: To create new fields in the attribute table for storing counts of urban and non-urban collisions.
Field Name: CNT_SWP_URB
Alias: CNT_SWP_URB
Data Type: long
Create another field named CNT_SWP_NURB using the same parameters.

Tool: Calcualte Field
purpose: To populate all the null values in the fields to the 0 so that it would be easy to copy other values in it.
input layer: mass_zctas_2010_aeac84_idty_ANGL_ETE_OV_OTHR_SWP
For both CNT_SWP_URB AND CNT_SWP_NURB 
Convert all nulls to 0


#### Calculating Side Swipe Collision Counts for Urban and Non-Urban Areas
Select by attributes
Purpose: To isolate polygons representing non-urbanized areas.

Input Rows: mass_zctas_2010_aeac84_idty_ANGL_ETE_OV_OTHR_SWP
Where: FID_new_england_urbanized_areas_2010_aeac84 is equal to -1
Outcome Description: Selects all rows where FID_new_england_urbanized_areas_2010_aeac84 equals -1, identifying polygons that represent non-urbanized areas.
Calculate Field:
Right Click on CNT_SWP_NURB and choose Calculate field
Input table: mass_zctas_2010_aeac84_idty_ANGL_ETE_OV_OTHR_SWP
Use the selected records: 454
Field Name: CNT_SWP_NURB
Expression:
Then in it select CNT_SWP_NURB = !CNT_SWP! Outcome desciption: It populates all the values that are selected in the CNT_SWP to the CNT_SWP_NURB
By this the Count of SWP collisions that occured inthe Non urban areas is acquired
** Use Switch in the Attribute table, it selects all the rows that are not -1, which means the polygons that are urbanized **


Calcualte Field
Right Click on CNT_SWP_URB and choose Calculate field

Input table: mass_zctas_2010_aeac84_idty_ANGL_OV_SWP
Use the selected records: 544
Field Name: CNT_SWP_URB
Expression:
Then in it select CNT_SWP_URB = !CNT_SWP! Output description: It populates the Count of collisions in the urbanized areas








Add field
Layer: mass_trac_crashes_2010_aeac84
Field name: Offland
Alias: Offland
Datatype: short
Number format: Numeric
output: It adds a new field called offland
In the newly added field there will be nulls


Recode
Right click on the Offland

Calculate field:
Input: mass_trac_crashes_2010_aeac84
Expression: Offland= 0
Output description: The nulls inthe newly created field will be changed to 0

Select by location
input: mass_trac_crashes_2010_aeac84
Relationship: Intersect
Selecting Features: new_england_urbanized_areas_townships_2010_aeac84_idty
Check Invert Spatial Relationship
Outcome description: It selects the points that are outside the polygons
Calculate field
Input table: mass_trac_crashes_2010_aeac84

Use the selected records: 154
Expression:
Offland = 1
Outcome description: It replaces the 0 with 1 in the selected rows for the Offland field.
Save the off-land collisions to a separate layer because the geo-processing will be easier

Export Crash points layer
Purpose: To create a new layer containing only the off-land collision points for easier geoprocessing.

Input features: mass_trac_crashes_2010_aeac84
output: mass_trac_crashes_2010_aeac84_Offland
Output Description: The result will be a layer with 154 rows representing off-land collision points.
Near
Purpose: To identify the nearest polygon for each off-land collision point and record its location coordinates.

Input: mass_trac_crashes_2010_aeac84_Offland
Near features: new_england_urbanized_areas_townships_2010_aeac84_idty
Check location: it adds NEAR_X AND NEAR_Y to the table
Output Description: Adds NEAR_X and NEAR_Y fields to the attribute table, representing the coordinates of the nearest polygon.



Purpose: To incorporate the Offland collision data identified in previous steps into the integrated final layer for determining the total count of each collision category.

Tool: Summary Statistics

Input table: mass_trac_crashes_2010_aeac84_Offland

Output table: mass_trac_crashes_2010_aeac84_Offland_smry Statistics fields:

Field: MannerCol_ctg Statistic Type: Count

Case Field: NEAR_FID, IS_URBAN, CityTown, MannerCol_ctgy

Outcome desciption: It gives a summary with count of Different MannerCor_ctgy collisions in the offland locations. Apart from that I have used CityTown, IS_URBAN( URBAN is 1 and Non-Urban is 2) and it also has NEAR_FID to join the count to the other layers. For this summary Count statistic type is choosen to get the count of the collision for each Collision category. It has 57 rows because I have used CityTown layer so that it is classified accordingly.



Pivoting (Transposing) the Summarized Off-land Collison Counts
Purpose: To convert the summarized offland collision data from a vertical (long) format to a horizontal (wide) format for easier integration with township data.

Tool: Pivot table
Input table: mass_trac_crashes_2010_aeac84_Offland_smry
Input fields: IS_URBAN, CityTown, NEAR_FID
Pivot Field: MannerCol_ctgy
Value Field: FREQUENCY
output table: mass_trac_crashes_2010_aeac84_Offland_pvt
Outcome description: The result is a transposed table where each collision category becomes a separate column, showing counts of offland collisions by township and urbanization type.

Add Join
Input: township_idty_massTrac_ANGL_ETED_OTHR_SSWP_SVHL

Input field: OBJECTID_1
Join table: mass_trac_crashes_2010_aeac84_Offland_pvt
Join field: NEAR_FID
Outcome description: This creates a temporary join that allows the calculation of the total collision counts for each collision category by combining on-land and offland data.


Create 5 New fields in the integrated layer

Layer: township_idty_massTrac_ANGL_ETED_OTHR_SSWP_SVHL
New field: TOTAL_CNT_ANGL LONG NUMERIC
New field: TOTAL_CNT_ETED LONG NUMERIC
New field: TOTAL_CNT_SSWP LONG NUMERIC
New field: TOTAL_CNT_SVHL LONG NUMERIC
New field: TOTAL_CNT_OTHR LONG NUMERIC


Calculate field and covert all the Null's inthe fields to 0 for calculation purposes

Populate the total values
Tool: Calculate field
Input table: township_idty_massTrac_ANGL_ETED_OTHR_SSWP_SVHL
Field Name: TOTAL_CNT_ANGL
Expression: TOTAL_CNT_ANGL = !CNT_ANGL! + !ANGL!
Outcome description: This step calculates the total number of ANGL collisions, combining polygon (on-land) and offland values. However, an error occurred during execution, which needs to be reviewed before proceeding. WARNING 002858: Certain rows set to NULL due to error while evaluating python expression: typeError: unsupported operand type +: and 'NoneType'
checked in the chatgpt, it says that this is a common ArcPro error, adds that one of the fields has null in it, I have rechecked both the fields and both fields have no nulls. Not sure about this error
