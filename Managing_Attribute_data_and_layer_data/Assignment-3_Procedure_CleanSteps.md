1
Assignment-3: Creating New Attributes & Transferring Geographic Data across Layers

Date: 10/05/2025
Objective
In this assignment, using the layers from previous tasks, I will be aggregating collision category
counts based on urban and non-urban areas. The goal is to also identify and count offland
collisions. This process involves data cleaning, spatial joins, and analytical operations to
transform the data into a clean usable form for spatial analysis.
Original Data 
Used
mass_trac_crashes_2010_aeac84
Source: Massachusetts Registry of Motor Vehicles (RMV)
Description: The Crash records for Massachusetts, the data is broken down into Crashes,
Vehicles and Persons (Drivers, Passengers and Non- Motorists) datasets.
new_england_urbanized_areas_township_idty
Source: Massachusetts Traffic Records Analysis Center (MassTRAC)
Description: This layer is one of the outputs of the assignment 2, It is an integrated layer
containing the Massachusetts township and urbanized polygon data.
Stage III - Spatially Aggregating Geographic Data
Generating Collision Type Counts Using 
Summary Statistics
Summary Statistics
Purpose: To generate aggregate count of the collision type
Purpose: To generate an aggregate count of collision types.
Input: mass_trac_crashes_2010_aeac84 (point data)
Field: Mannercoll
Statistic Type: Count
Case 
Field: Mannercoll
Output: mass_trac_crashes_2010_aeac84_smry
2
Output Description: This tool groups the data by the case field and provides the total count for
each unique value in the Mannercoll field.
Recoding of data
Create a new field in massTract_crashes2010 layer and group the data into larger categories
based on the idea of balancing the frequencies with a logical grouping of the crash types. This is
called “recoding” and these new collision type categories will be used to spatially join the crash
locations as general groups to the Massachusetts Township and Census Tract layers. The new
MassTRAC collision categories for spatially joining to the integrated township layer should be:
a. Angle
b. Side-swipes (Sideswipe Opposite Direction, Sideswipe Same Direction)
c. Single Vehicle (Single Vehicle, Not Collision Between Two Vehicles in Traffic)
d. End to End (Head On, Rear End, Rear-to-Rear)
e. Unknown (Unknown, Reported but Invalid, Not Reported, Missing)
Adding a 
Field to Store Short Codes for Collision Types
Purpose: To add a new field to the attribute table for storing short values representing collision
types
Tool: Add 
Field
Layer: mass_trac_crashes_2010_aeac84
Tool: Add 
Field
Purpose: To add a new field to the attribute table.
Field Name: MannerColl_ctgy
Alias: MannerColl_ctgy
Data Type: Text
Length: 6 (assign the maximum number considered suitable)
Output Description: A new text field named MannerColl_ctgy is added to the attribute table,
which will be used to store short codes for each collision type.
For the series of steps done for 5 collision types, the steps are mentioned for each collision
type. These steps will be repeated under the thick line below.
For Recoding ANGL
Select by Attributes
Purpose: To select all desired rows within the specified column.
Tool: 
Select by Attributes
3
Purpose: To select all desired rows within the specified column.
Input: mass_trac_crashes_2010_aeac84
Where: MannerColl is equal to Angle
Output Description: 
Selects all rows in the MannerColl field where the value is Angle.
Note: 
Use “is equal to” when selecting a single value and “includes” when selecting multiple
values.
Calculate 
Field
Input: mass_trac_crashes_2010_aeac84
Check: 
Use the selected rows (31,631)
Field Name: MannerColl_ctgy
Expression: MannerColl_ctgy = ANGL
Output Description: 
Populates all selected rows in the MannerColl_ctgy field with the text
ANGL.
Note: When entering text values in the expression, enclose them in quotation marks since the
field type is text.
For Recoding SVHL
Select by Attributes
Tool: 
Select by Attributes
Purpose: To select all the desired rows within the specified column.
Input: mass_trac_crashes_2010_aeac84
Where: MannerColl includes the values Single Vehicle, Not Collision Between Two Vehicles in
Traffic
Output Description: 
Selects all rows in the MannerColl field that contain the values Single
Vehicle and Not Collision Between Two Vehicles in Traffic.
Calculate 
Field
Input: mass_trac_crashes_2010_aeac84
Check: 
Use the selected rows (23,409)
Field Name: MannerColl_ctgy
Expression: MannerColl_ctgy = SVHL
Output Description: 
Populates all selected rows in the MannerColl_ctgy field with the text
SVHL.
For Recoding SSWP
Select by Attributes
Tool: 
Select by Attributes
Purpose: To select all the desired rows within the specified column.
Input: mass_trac_crashes_2010_aeac84
4
Where: MannerColl includes the values Sideswipe Opposite Direction and Sideswipe Same
Direction
Output Description: 
Selects all rows in the MannerColl field that contain the values Sideswipe
Opposite Direction and Sideswipe Same Direction.
Calculate 
Field
Input: mass_trac_crashes_2010_aeac84
Check: 
Use the selected rows (12,709)
Field Name: MannerColl_ctgy
Expression: MannerColl_ctgy = SSWP
Output Description: 
Populates all selected rows in the MannerColl_ctgy field with the text
SSWP.
For Recoding ETED
Select by Attributes
Tool: 
Select by Attributes
Purpose: To select all the desired rows within the specified column.
Input: mass_trac_crashes_2010_aeac84
Where: MannerColl includes the values Head On, Rear End, and Rear-to-Rear
Output Description: 
Selects all rows in the MannerColl field that contain the values Head On,
Rear End, and Rear-to-Rear.
Calculate 
Field
Input: mass_trac_crashes_2010_aeac84
Check: 
Use the selected rows (35,213)
Field Name: MannerColl_ctgy
Expression: MannerColl_ctgy = ETED
Output Description: 
Populates all selected rows in the MannerColl_ctgy field with the text
ETED.
For Recoding OTHR
Select by Attributes
Tool: 
Select by Attributes
Purpose: To select all the desired rows within the specified column.
Input: mass_trac_crashes_2010_aeac84
Where: MannerColl includes the values Unknown, Reported but Invalid, Not Reported, and
Missing
Output Description: 
Selects all rows in the MannerColl field that contain the values Unknown,
Reported but Invalid, Not Reported, and Missing.
5
Calculate 
Field
Input: mass_trac_crashes_2010_aeac84
Check: 
Use the selected rows (3,636)
Field Name: MannerColl_ctgy
Expression: MannerColl_ctgy = OTHR
Output Description: 
Populates all selected rows in the MannerColl_ctgy field with the text
OTHR.
Assigning Collision Types to Areal Geographies
- Mask the data and make only visible the observations that are required
Definition query
Purpose: To separate the locations so that each collision type can be processed individually.
Procedure: From the Layer Table of Contents, select the Properties option to open the dialog
box. In it, choose New 
Definition Query.
Where: MannerColl_ctgy is equal to ANGL
Outcome Description: Filters and displays only the records corresponding to the ANGL collision
type.
Once the 
Definition Query has been applied, the selected locations need to be aggregated using
a spatial join.
Aggregate the individual and grouped collision types with a 
Spatial Join from the
massTRAC_crashes2010 layer to the integrated township and urbanized area layer
(new_england_urbanized_areas_townships_2010_aeac84_idty).
Spatial Join
Purpose: To join the township_and_urbanized layer with the crash points. This operation
performs a point-in-polygon process that calculates the count of all crash locations within each
township polygon.
Target Features: new_england_urbanized_areas_townships_2010_aeac84_idty
Join Features: massTRAC_crashes2010
Join Operation: Join one to one
Match Option: Intersect
6
Output: new_england_urbanized_areas_township_idty_massTrac_ANGL
Field 
Selection: Delete all fields from the join layer except OBJECTID, ID, CityTown, MannerColl,
Latitude, Longitude, and MannerCol_ctgy. OBJECTID and other redundant fields can be removed
later if needed.
Output Description: The resulting layer contains all ANGL-filtered crash points joined with the
township polygons.
From attribute table, delete Target_FID it is the Objectid of the joined layer, which is redundant
To Change the name of the Join_count to CTN_ANGL
Alter 
Field
Purpose: To change the name of a field without altering its position in the attribute table.
Input Table: new_england_urbanized_areas_township_idty_massTrac_ANGL
Field Name: Join_Count
New 
Field Name: CNT_ANGL
New 
Field 
Alias: CNT_ANGL
Output Description: The field name was successfully changed to CNT_ANGL.
In the attribute table of the layer
new_england_urbanized_areas_township_idty_massTrac_ANGL
Add two new fields
Add 
Field
Tool: Add 
Field
Purpose: To create new fields in the attribute table for storing counts of urban and non-urban
collisions.
Field Name: CNT_ANGL_URB
Alias: CNT_ANGL_URB
Data Type: Double
- Create another field named CNT_ANGL_NURB using the same parameters.
Calculating ANGL Collision Counts for Urban and Non-Urban Areas
Select by Attributes
Purpose: To isolate polygons representing non-urbanized areas.
Input Rows: new_england_urbanized_areas_township_idty_massTrac_ANGL
Where: FID_new_england_urbanized_areas_2010_aeac84 is equal to -1
Outcome Description: 
Selects all rows where FID_new_england_urbanized_areas_2010_aeac84
equals -1, identifying polygons that represent non-urbanized areas.
7
Calculate 
Field
Right-click on CNT_ANGL_NURB and choose Calculate 
Field.
Input Table: new_england_urbanized_areas_township_idty_massTrac_ANGL
Use the selected records: 309
Field Name: CNT_ANGL_NURB
Expression: CNT_ANGL_NURB = !CNT_ANGL!
Outcome Description: 
Populates the CNT_ANGL_NURB field with the values from CNT_ANGL for
the selected non-urban polygons, providing the count of ANGL collisions in non-urban areas.
Use Switch in the attribute table to select all rows that are not -1, representing urbanized
polygons.
Calculate 
Field
Right-click on CNT_ANGL_NURB and choose Calculate 
Field.
Input Table: new_england_urbanized_areas_township_idty_massTrac_ANGL
Use the selected records: 340
Field Name: CNT_ANGL_URB
Expression: CNT_ANGL_URB = !CNT_ANGL!
Output Description: 
Populates the CNT_ANGL_URB field with the count of ANGL collisions in
urbanized areas.
- Null values in CNT_ANGL_NURB and CNT_ANGL_URB were replaced with -1 for
calculation purposes.
In the same way, the process is carried out for the remaining collision types. The procedure is
repetitive and follows the same sequence of steps. The detailed process for each collision
type is provided below, under the thick lines.
Spatial Join for ETED
Input: new_england_urbanized_areas_township_idty_massTrac_ANGL
Join: massTRAC_crashes2010
Output: township_idty_massTrac_ANGL_ETED
Match option: Intersect
Fields: Remove all the fields from the join layer except (ID_1, CityTown_1, Latitude_1,
Longitude_1, MannerCol_ctgy_1)
In the newly created layer, perform the following edits:
Open the attribute table.
8
Alter 
Field
Purpose: To change the name of the field without changing its position in the table.
Input table: township_idty_massTrac_ANGL_ETED
Field Name: Join_Count
New 
Field Name: CNT_ETED
New 
Field 
Alias: CNT_ETED
Output description: The field name was changed successfully.
Add 
Field
Field Name: CNT_ETED_URB
Alias: CNT_ETED_URB
Data Type: Long
Number Format: Numeric

• Create another field named CNT_ETED_NURB using the same instructions.
Calculate 
Field

• Right-click on CNT_ETED_NURB and choose Calculate 
Field.

• 
Input table: township_idty_massTrac_ANGL_ETED

• 
Use the selected records: 309

• 
Field Name: CNT_ETED_NURB

• 
Expression: CNT_ETED_NURB = !CNT_ETED!
Outcome description: 
Populates all the selected values from CNT_ETED into
CNT_ETED_NURB.

• This provides the count of ETED collisions that occurred in the non-urban areas.
Use Switch in the Attribute Table to select all rows that are not -1, which represent urbanized
polygons.
Calculate 
Field

• Right-click on CNT_ETED_NURB and choose Calculate 
Field.

• 
Input table: township_idty_massTrac_ANGL_ETED

• 
Use the selected records: 340

• 
Field Name: CNT_ETED_URB

• 
Expression: CNT_ETED_URB = !CNT_ETED!
Output description: 
Populates the count of ETED collisions that occurred in the
urbanized areas.
9
Spatial Join for OTHR
Input: township_idty_massTrac_ANGL_ETED
Join: massTRAC_crashes2010_aeac84
Output: township_idty_massTrac_ANGL_ETED_OTHR
Match option: Intersect
Fields: Remove all the fields from the join layer except (ID_12, CityTown_12, Latitude_12,
Longitude_12, MannerCol_ctgy_12).
In the newly created layer, perform the following edits:
Open the attribute table.
Alter 
Field
Purpose: To change the name of the field without changing its position in the table.
Input table: township_idty_massTrac_ANGL_ETED_OTHR
Field Name: Join_Count
New 
Field Name: CNT_OTHR
New 
Field 
Alias: CNT_OTHR
Output description: The field name was changed successfully.
Add 
Field
Field Name: CNT_ETED_OTHR
Alias: CNT_ETED_OTHR
Data Type: Long
Number Format: Numeric

• Create another field named CNT_OTHR_NURB with the same settings.
Calculate 
Field

• Right-click on CNT_ETED_NURB and choose Calculate 
Field.

• 
Input table: township_idty_massTrac_ANGL_ETED_OTHR

• 
Use the selected records: 309

• 
Field Name: CNT_OTHR_NURB

• 
Expression: CNT_OTHR_NURB = !CNT_OTHR!
Outcome description: 
Populates all the selected CNT_OTHR values into
CNT_OTHR_NURB.

• This provides the count of OTHR collisions that occurred in the non-urban areas.
Use Switch in the Attribute Table to select all rows that are not -1, which represent urbanized
polygons.
Calculate 
Field
10

• Right-click on CNT_ETED_NURB and choose Calculate 
Field.

• 
Input table: township_idty_massTrac_ANGL_ETED_OTHR

• 
Use the selected records: 340

• 
Field Name: CNT_OTHR_URB

• 
Expression: CNT_OTHR_URB = !CNT_OTHR!
Output description: 
Populates the count of OTHR collisions that occurred in the
urbanized areas.
Spatial Join for OTHR
Input: township_idty_massTrac_ANGL_ETED
Join: massTRAC_crashes2010_aeac84
Output: township_idty_massTrac_ANGL_ETED_OTHR
Match option: Intersect
Fields: Remove all the fields from the join layer except (ID_12, CityTown_12, Latitude_12,
Longitude_12, MannerCol_ctgy_12)
In the newly created layer, perform the following edits
Open attribute table
Alter 
Field
Purpose: To change the name of the field without changing its position in the table
Input table: township_idty_massTrac_ANGL_ETED_OTHR
Field Name: Join_Count
New field Name: CNT_OTHR
New 
Field 
Alias: CNT_OTHR
Output description: It changed the field name
Add 
Field
Field Name: CNT_ETED_OTHR
Alias: CNT_ETED_OTHR
Data type: Long
Number Format: Numeric

• Create another field named CNT_OTHR_NURB with the same instructions
Calculate 
Field

• Right-click on CNT_ETED_NURB and choose Calculate 
Field

• 
Input table: township_idty_massTrac_ANGL_ETED_OTHR

• 
Use the selected records: 309

• 
Field Name: CNT_OTHR_NURB
11

• 
Expression: CNT_OTHR_NURB = !CNT_OTHR!
Outcome description: It populates all the values that are selected in the CNT_OTHR to
the CNT_OTHR_NURB

• By this, the count of OTHR collisions that occurred in the non-urban areas is acquired
Use Switch in the Attribute Table, it selects all the rows that are not -1, which means the
polygons that are urbanized
Calculate 
Field

• Right-click on CNT_ETED_NURB and choose Calculate 
Field

• 
Input table: township_idty_massTrac_ANGL_ETED_OTHR

• 
Use the selected records: 340

• 
Field Name: CNT_OTHR_URB

• 
Expression: CNT_OTHR_URB = !CNT_OTHR!
Output description: It populates the count of collisions in the urbanized areas
Spatial Join for SSWP
Input: township_idty_massTrac_ANGL_ETED
Join: massTRAC_crashes2010_aeac84
Output: township_idty_massTrac_ANGL_ETED_OTHR_SSWP
Match option: Intersect
Fields: Remove all the fields from the join layer except (ID_12, CityTown_12, Latitude_12,
Longitude_12, MannerCol_ctgy_12)
In the newly created layer, perform the following edits
Open attribute table
Alter 
Field
Purpose: To change the name of the field without changing its position in the table
Input table: township_idty_massTrac_ANGL_ETED_OTHR_SSWP
Field Name: Join_Count
New field Name: CNT_SSWP
New 
Field 
Alias: CNT_SSWP
Output description: It changed the field name
Add 
Field
Field Name: CNT_SSWP_URB
Alias: CNT_SSWP_URB
Data type: Long
Number Format: Numeric
12

• Create another field named CNT_SSWP_NURB with the same instructions
Calculate 
Field

• Right-click on CNT_ETED_NURB and choose Calculate 
Field

• 
Input table: township_idty_massTrac_ANGL_ETED_OTHR_SSWP

• 
Use the selected records: 309

• 
Field Name: CNT_SSWP_NURB

• 
Expression: CNT_SSWP_NURB = !CNT_SSWP!
Outcome description: It populates all the values that are selected in the CNT_SSWP to
the CNT_SSWP_NURB

• By this, the count of SSWP collisions that occurred in the non-urban areas is acquired
Use Switch in the Attribute Table, it selects all the rows that are not -1, which means the
polygons that are urbanized
Calculate 
Field

• Right-click on CNT_ETED_NURB and choose Calculate 
Field

• 
Input table: township_idty_massTrac_ANGL_ETED_OTHR_SSWP

• 
Use the selected records: 340

• 
Field Name: CNT_SSWP_URB

• 
Expression: CNT_SSWP_URB = !CNT_SSWP!
Output description: It populates the count of End to end collisions in the urbanized
areas
Spatial Join for SVHL
Input: township_idty_massTrac_ANGL_ETED_OTHR_SSWP
Join: massTRAC_crashes2010_aeac84
Output: township_idty_massTrac_ANGL_ETED_OTHR_SSWP_SVHL
Match option: Intersect
Fields: Remove all the fields from the join layer except (ID_12, CityTown_12, Latitude_12,
Longitude_12, MannerCol_ctgy_12)
In the newly created layer, do the following edits
Open attribute table
Alter 
Field
Purpose: To change the name of the field without changing its position in the table
Input table: township_idty_massTrac_ANGL_ETED_OTHR_SSWP_SVHL
Field Name: Join_Count
13
New field Name: CNT_SVHL
New 
Field 
Alias: CNT_SVHL
Output description: It changed the field name
Add 
Field
Field Name: CNT_SVHL_URB
Alias: CNT_SVHL_URB
Data type: Long
Number Format: Numeric

• Create another field named CNT_SVHL_NURB with the same instructions
Calculate 
Field

• Right-click on CNT_ETED_NURB and choose Calculate 
Field

• 
Input table: township_idty_massTrac_ANGL_ETED_OTHR_SSWP_SVHL

• 
Use the selected records: 309

• 
Field Name: CNT_SVHL_NURB

• 
Expression: CNT_SVHL_NURB = !CNT_SVHL!
Outcome description: It populates all the values that are selected in the CNT_SVHL to
the CNT_SVHL_NURB

• By this, the count of SVHL collisions that occurred in the non-urban areas is acquired
Use Switch in the Attribute Table, it selects all the rows that are not -1, which means the
polygons that are urbanized
Calculate 
Field

• Right-click on CNT_ETED_NURB and choose Calculate 
Field

• 
Input table: township_idty_massTrac_ANGL_ETED_OTHR_SSWP_SVHL

• 
Use the selected records: 340

• 
Field Name: CNT_SVHL_URB

• 
Expression: CNT_SVHL_URB = !CNT_SVHL!
Output description: It populates the count of collisions in the urbanized areas
II. Geo-processing Non-overlapping Collisions
Identification of Off-Land Collisions
In the data, there are a few collisions that occurred off the land or outside the polygon data,
such as bridges. Even that data is important. Below are the steps to identify the collisions that
happened off-land and assign their nearest polygon information to add to the total count.
14
Add 
Field
Layer: mass_trac_crashes_2010_aeac84
Field Name: Offland
Alias: Offland
Data Type: Short
Number Format: Numeric
Output: It adds a new field called Offland.
In the newly added field, there will be null values.
Recode
Right-click on the Offland field.
Calculate 
Field:
Input: mass_trac_crashes_2010_aeac84
Expression: Offland = 0
Output Description: The null values in the newly created field will be changed to 0.
Select by Location
Input: mass_trac_crashes_2010_aeac84
Relationship: Intersect
Selecting Features: new_england_urbanized_areas_townships_2010_aeac84_idty
Check: Invert Spatial Relationship
Outcome Description: It selects the points that are outside the polygons.
Calculate 
Field
Input Table: mass_trac_crashes_2010_aeac84
Use the 
Selected Records: 154
Expression: Offland = 1
Outcome Description: It replaces 0 with 1 in the selected rows for the Offland field.
Save the off-land collisions to a separate layer because the geo-processing will be easier.
Export Crash points layer
Purpose: To create a new layer containing only the off-land collision points for easier
geoprocessing.
Input Features: mass_trac_crashes_2010_aeac84
Output: mass_trac_crashes_2010_aeac84_Offland
Output Description: The result will be a layer with 154 rows representing off-land collision
points.
Near
15
Purpose: To identify the nearest polygon for each off-land collision point and record its location
coordinates.
Input: mass_trac_crashes_2010_aeac84_Offland
Near Features: new_england_urbanized_areas_townships_2010_aeac84_idty
Output Description: Adds NEAR_X and NEAR_Y fields to the attribute table, representing the
coordinates of the nearest polygon.
IS_URBAN recoding
Purpose: To segregate collision points into urbanized and non-urbanized categories by creating
an IS_URBAN field and assigning each record to its respective area based on proximity and
characteristics.
Join Table
Input: new_england_urbanized_areas_township_2010_aeac84_idty
Input 
Field: OBJECTID_1
Join Table: mass_trac_crashes_2010_aeac84_Offland
Join 
Field: NEAR_FID
Transfer 
Fields: id, CityTown, Town, MannerColl, MannerCol_ctgy, Offland, IS_URBAN,
NEAR_FID, NEAR_DIST
Calculate 
Field
Layer: new_england_urbanized_areas_township_2010_aeac84_idty
Field: IS_URBAN
Expression: IS_URBAN = Offland
Output Description: Assigns Offland values to the IS_URBAN field for classification purposes.
Definition Query
Condition: IS_URBAN equals 1 and FID_new_england_urbanized_areas_2010_aeac84 equals -1
Outcome Description: 
Selects polygons representing urbanized areas and the points falling in
the Offland regions.
Calculate 
Field
Field: IS_URBAN
Expression: IS_URBAN = 2
Outcome Description: 
Recodes the IS_URBAN field with a value of 2 for Offland collisions near
non-urban township areas and retains 1 for those near urban townships. Null values are
converted to 0 to represent non-urban areas.
Joining IS_URBAN field to the Offland layer
Purpose: To attach the IS_URBAN classification from the polygon layer to the Offland collision
point layer for further analysis.
16
Tool: Join 
Field
Input Table: mass_trac_crashes_2010_aeac84_Offland
Input 
Field: NEAR_FID
Join Table: new_england_urbanized_areas_township_2010_aeac84_idty
Join 
Field: OBJECTID
Transfer 
Fields: IS_URBAN
Outcome Description: The IS_URBAN field is successfully joined to the Offland collision points
layer, enabling urban and non-urban classification in subsequent spatial analyses.
III. Summarizing and Adding the Additional Data to the Townships
Purpose: To incorporate the Offland collision data identified in previous steps into the
integrated final layer for determining the total count of each collision category.
Tool: 
Summary Statistics
Input Table: mass_trac_crashes_2010_aeac84_Offland
Output Table: mass_trac_crashes_2010_aeac84_Offland_smry
Statistics 
Fields:
Field: MannerCol_ctgy Statistic Type: Count
Case 
Fields:
NEAR_FID, IS_URBAN, CityTown, MannerCol_ctgy
Outcome Description: The process generates a summary table showing the count of different
MannerCol_ctgy collisions occurring in Offland locations. The inclusion of CityTown and
IS_URBAN (with 1 representing Urban and 2 representing Non-Urban) allows for classification
based on the type of area. The NEAR_FID field is retained for linking the summary counts back
to other layers. The output contains 57 rows because the CityTown field was included to
categorize the data accordingly.
Summary statistics
Purpose: To summarize the offland collision data by counting the number of collisions for each
collision category and township (NEAR_FID), preparing it for integration with the main township
layer.
Tool: 
Summary Statistics
17
Input table: mass_trac_crashes_2010_aeac84_Offland
Output table: mass_trac_crashes_2010_aeac84_Offland_smry2
Statistics fields:
Field: MannerCol_ctgy
Statistic Type: Count
Case fields:
NEAR_FID, MannerCol_ctgy
Outcome description:
This summary produced 56 rows as only two case fields were used, grouping the count
accordingly.
The summarized table is in a “vertical” format (multiple records for each unique ID). To join it
with the integrated township layer, the data must be converted to a “horizontal” format, where
each unique township has one record with separate columns for each collision category.
Pivoting (Transposing) the Summarized Off-land Collision Counts
Purpose: To convert the summarized offland collision data from a vertical (long) format to a
horizontal (wide) format for easier integration with township data.
Tool: Pivot Table
Input table: mass_trac_crashes_2010_aeac84_Offland_smry
Input fields: IS_URBAN, CityTown, NEAR_FID
Pivot field: MannerCol_ctgy
Value field: FREQUENCY
Output table: mass_trac_crashes_2010_aeac84_Offland_pvt
Outcome description:
The result is a transposed table where each collision category becomes a separate column,
showing counts of offland collisions by township and urbanization type.
Converting Null in the fields to 0 for calculation
Select by attributes
18
I have selected only nulls in the ANGL, ETED, SSWP, SVHL, OTHR fields and using the calculate
field I have given 0 to them.
Joining the Pivot table to the Integrated layer
Purpose: To temporarily join the pivoted offland collision data to the integrated township layer
for calculating the final count of each collision category.
Tool: Add Join
Input: township_idty_massTrac_ANGL_ETED_OTHR_SSWP_SVHL
Input field: OBJECTID_1
Join table: mass_trac_crashes_2010_aeac84_Offland_pvt
Join field: NEAR_FID
Outcome description:
This creates a temporary join that allows the calculation of the total collision counts for each
collision category by combining on-land and offland data.
Create 5 New fields in the integrated layer
Layer: township_idty_massTrac_ANGL_ETED_OTHR_SSWP_SVHL
New field: TOTAL_CNT_ANGL LONG NUMERIC
New field: TOTAL_CNT_ETED LONG NUMERIC
New field: TOTAL_CNT_SSWP LONG NUMERIC
New field: TOTAL_CNT_SVHL LONG NUMERIC
New field: TOTAL_CNT_OTHR LONG NUMERIC
Calculate field and convert all the Null's in the fields to 0 for calculation purposes
Populate the total values
Purpose: To calculate the total count of ANGL collisions by summing the on-land and offland
collision values.
Tool: Calculate 
Field
Input table: township_idty_massTrac_ANGL_ETED_OTHR_SSWP_SVHL
19
Field Name: TOTAL_CNT_ANGL
Expression: TOTAL_CNT_ANGL = !CNT_ANGL! + !ANGL!
Outcome description:
This step calculates the total number of ANGL collisions, combining polygon (on-land) and
offland values. However, an error occurred during execution, which needs to be reviewed
before proceeding.
WARNING 002858: Certain rows set to NULL due to error while evaluating python expression:
typeError: unsupported operand type +: and 'NoneType'
- checked in the chatgpt, it says that this is a common ArcPro error, adds that one of the fields
has null in it, I have rechecked both the fields and both fields have no nulls. Not sure about this
error
Calculating the total individual collision type count including the offland
Purpose: To calculate the total count of individual collision types, including both on-land and
off-land data.
Tool: Calculate 
Field
Input table: township_idty_massTrac_ANGL_ETED_OTHR_SSWP_SVHL
Field Name: TOTAL_CNT_OTHR
Expression:
TOTAL_CNT_OTHR = !CNT_OTHR! + !OTHR!
Outcome: It gives the total number of OTHR collisions within the polygons and offland areas.
Tool: Calculate 
Field
Input table: township_idty_massTrac_ANGL_ETED_OTHR_SSWP_SVHL
Field Name: TOTAL_CNT_ETED
Expression:
TOTAL_CNT_ETED = !CNT_ETED! + !ETED!
Outcome: It gives the total number of ETED collisions within the polygons and offland areas.
Tool: Calculate 
Field
Input table: township_idty_massTrac_ANGL_ETED_OTHR_SSWP_SVHL
Field Name: TOTAL_CNT_SSWP
Expression:
TOTAL_CNT_SSWP = !CNT_SSWP! + !SSWP!
Outcome: It gives the total number of SSWP collisions within the polygons and offland areas.
20
Tool: Calculate 
Field
Input table: township_idty_massTrac_ANGL_ETED_OTHR_SSWP_SVHL
Field Name: TOTAL_CNT_SVHL
Expression:
TOTAL_CNT_SVHL = !CNT_SVHL! + !SVHL!
Outcome: It gives the total number of SVHL collisions within the polygons and offland areas.
Remove all joins
Purpose: To remove all joins after completing the calculations, as they are no longer needed.
Right-click on: township_idty_massTrac_ANGL_ETED_OTHR_SSWP_SVHL
Select: Joins and Relates → Remove All Joins
Outcome description: It removes all pivot table joins from the final integrated urbanized layer,
ensuring the dataset remains clean and optimized for further analysis.
Data cleaning:
To clean up the attribute table by removing redundant and repetitive fields that are no longer
required after data integration and processing.
In the layer: township_idty_massTrac_ANGL_ETED_OTHR_SSWP_SVHL
Remove the following fields:

• TARGETFID

• OBJECTID (from joins)

• CityTown (from joins)

• MannerColl

• Latitude and Longitude

• MannerCol_ctgy

• Shapearea(Blank)
Outcome description: This retains only relevant fields, ensuring efficiency and clarity for final
analysis.
Final Data Inventory

• township_idty_massTrac_ANGL_ETED_OTHR_SSWP_SVHL :
21
o description: This is the final integrated layer where spatial joins for all five
collision types are completed, and the off-land counts are added. It includes
CNT_URB and CNT_NURB fields for each collision category.
o source: Massachusetts Traffic Records Analysis Center (MassTRAC)
o number of features: 649

• mass_trac_crashes_2010_aeac84_Offland:
o Description: This layer contains the Offland collision point data that occurred off
the land areas.
NEAR_FID (useful to join with integrated layer),
CityTown: For summary to know which Town the collision belongs to
MannerCol_ctgy: Category type of the collision
Offland: This field has a value of 1, indicating that the collision occurred
off land.
IS_URBAN: This field identifies whether the nearest polygon to the
collision point is Urban or Non-Urban. Urban areas are represented by 1,
and Non-Urban areas are represented by 2. This information is useful for
performing summaries and analyses.
o Source: Massachusetts Registry of Motor Vehicles (RMV)
o Number of features: 154

• mass_trac_crashes_2010_aeac84:
o Description: This layer contains Massachusetts crash data. The MannerCol_ctgy
field represents the categorized or aggregated type of collision, while the Offland
field identifies points located outside the township polygons.
o Source: Massachusetts Registry of Motor Vehicles (RMV)
o Number of features: 1,06,598

• mass_trac_crashes_2010_aeac84_Offland_smry2: Table
o Description: This table summarizes the count of all collision categories, organized
by NEAR_FID and MannerCol_ctgy.
o Source: Massachusetts Registry of Motor Vehicles (RMV)
22
o Number of features: 56

• mass_trac_crashes_2010_aeac84_Offland_smry:
o Description: In this summary is grouped by CityTown, IS_URBAN and
MannorCol_ctgy. It helps identify the collision locations and determine whether
each location is urban or non-urban, along with the count of collisions.
o Source: Massachusetts Registry of Motor Vehicles (RMV)
o Number of features: 57

• mass_trac_crashes_2010_aeac84_Offland: Table
o Description: The collision data has been transposed from a single vertical column
into individual columns. This structure helps in understanding the crash count
along with its corresponding NEAR_FID and Town.
o Source: Massachusetts Registry of Motor Vehicles (RMV)
o Number of features: 31
Apart from them the sandbox has other layer and tables which are formed during spatial joins
and from tables, layers previous tasks. There is a duplicate for the final layer for backup. Kindly
ignore them.
Results
The final dataset is an integrated layer containing both township and urbanized polygons. Its
attribute table combines information from both layers. In addition, it includes counts of all
collision types, counts for urban and non-urban areas, and the final totals for each crash
category.
The dataset was cleaned by removing redundant and unnecessary fields. Determining the
IS_URBAN field to classify whether the offland points were urbanized or non-urbanized was
somewhat challenging, but its summary provided valuable insights.
This analysis meets the objective of identifying the counts of all collision types and categorizing
them by area. It is useful for analyzing where collisions occur most frequently in both urban and
non-urban areas, what types of collisions are most common, and which towns have the highest
or lowest numbers. This helps in detecting spatial patterns or statistical significance, and the
data can be overlaid with traffic and route information to find potential alternative solutions.
Overall, this was a valuable exercise to extract, organize, and optimize the data for efficient
analysis.
23
I have added the Assignment instructions and then my documentation in chatgpt to
check and review it.
GenAI Review Summary
Your documentation is thorough, organized, and clearly written. It successfully meets the
objectives of the assignment by detailing each geoprocessing step, from data preparation to
integration and analysis. The workflow is reproducible, with well-defined purposes and
outcomes for every tool used.
Strengths

• Comprehensive explanation of all stages and tools.

• Logical organization and consistent workflow.

• Clear integration of off-land and urban/non-urban classifications.

• Strong final data inventory and summary analysis.
Areas for Improvement

• Include mean longitude and latitude calculations for each collision type to fully meet
the task requirements.

• 
Use null-safe expressions in field calculations to avoid errors.

• Standardize field names and correct minor typos (e.g., MannerColl_ctgy, urbanized).

• Replace “−1” with 0 for null counts.
Overall Assessment:
Excellent documentation—detailed, methodical, and aligned with assignment goals. With small
refinements to mean coordinate calculations and naming consistency, it will be submission-
ready.


dont change anything

I want to add it in the git

I want to add in the procedure file, idk what file is good like md or anything else

give me the above text in that format, dont change anything, just give me in that format thats it
thanks you
