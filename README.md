# Spatial SQL SpatiaLite programmal
Spatial SQL bevezető tanfolyam SpatiaLite használatával.

**Szerzők:** [Michele Tobias](https://github.com/MicheleTobias) & [Naomi Kalman](https://github.com/nbkalman)

## Workshop Description
This workshop is intended to give participants an introduction to working with **spatial data** using SQL. We will work with a graphical user interface (GUI) and explore some examples of common analysis processes as well as present participants with resources for continued learning.  This workshop will give participants a solid foundation on which to build further learning.

## Learning Objectives
By the end of this workshop, participants will be able to
* Import data into an spatialite database
* Write queries to answer questions about spatial data
* Understand the difference between attribute queries and geometry queries
* View spatial tables and views in QGIS

## A tanfolyamról
A tanfolyam célja, hogy a résztvevők megismerkedjenek a **téradatokkal** való munkavégzéssel SQL használatával. Grafikus felhasználói felülettel (GUI) fogunk dolgozni, és megismerünk néhány példát a gyakori elemzési folyamatokra, valamint forrásokat biztosítunk a résztvevőknek a folyamatos tanuláshoz. Ez a tanfolyam jó kiindulási alapot ad a résztvevőknek a továbblépéshez.

## Tanulási célok
A tanfolyam végére a résztvevők képesek lesznek
* Adatok importálása térbeli adatbázisba
* Téradatokkal kapcsolatos kérdések megválaszolásához lekérdezések írására 
* Különbséget tenni az attribútumlekérdezések és a geometriai lekérdezések között
* Térbeli táblázatok és nézetek megtekintésére a QGIS-ben

## Előkövetelmények
Az SQL bevezető ismerete ajánlott, de nem kötelező. Például a SELECT utasítások összeállításának ismerete SQL használatával és a táblák összekapcsolásának általános ismerete jó segítség lehet. Az SQL alapismeretekkel nem rendelkező tanulók számára javasoljuk, hogy tekintsék meg a DataLab [Introduction to Databases and Data Storage Technologies](https://ucdavisdatalab.github.io/workshop_intro_to_data_storage/) anyagait, amely bemutatja az adatbázisok fogalmát, és [ Intro to SQL for Querying Databases](https://ucdavisdatalab.github.io/workshop_intro_to_sql/), amely megtanítja az adatok SQL használatával történő lekérdezésének alapjait.

## Software

**Install SpatiaLite GUI:**  The instructions for intalling SpatiaLite GUI varies depending on your operating sytsem.  We've provided a [guide to installing the software](https://github.com/MicheleTobias/Spatial_SQL/blob/master/Install.md). These workshop materials were most recently written for Spatialite version 5.0.0 and Spatialite GUI 2.1.0-beta1.

**Install QGIS:** You should have installed QGIS version 3.24 from [QGIS.org](https://qgis.org/en/site/forusers/download.html). Recent older versions of QGIS will also suffice.  We'll be using QGIS to visualize our spatial data tables that we import into our SpatiaLite database.


# Concepts

## What is a database?
A database is a set of data in tables that are related to each other in some way.  That's it.  **A database is just a collection of related tables.**

Generally each table can be connected to another table by a column that both tables have that store the information to match up the rows.  This column is called a **key**.  For example, your student or employee ID number is a key commonly used on campus.

If you've done GIS, you already use a database.  A shapefile is essentially just a fancy a table. And if you're ever joined a .csv table to a shapefile, you've performed a database process called a join. Using multiple spatial datasets in a GIS relates the data by location, if not by tabular data.

## What is a Spatial Database?
A spatial database is a normal database (i.e. a set of related tables) but at least one of the tables has a column that holds the spatial information commonly called the "geometry".  The geometry information is stored as a Binary Large Object (BLOB).  The geometry information allows us to relate the tables to each other based on their location and also to perform spatial analysis on our data.

Below is an example of a database entity relationship diagram for tables in an example (imaginary, yet plausible) spatial database.  At least one column in each table relates to a column in another table (indicated in this diagram by a line drawn between the two columns).  Documenting a database with a diagram like this is common practice.  It provides a quick visual reference to the data contained in each table and how the tables relate to each other. The `city` table is a spatial table. The `geometry` column provides the spatial information that allows the city polygons to be mapped.  The other tables are tabular data that can be joined to the spatial table using the key, in this case, the Federal Information Processing System (FIPS) Code, a system that assigns unique numeric codes to geographic entities such as cities, counties, and states. FIPS codes help us have a standardized key that prevents mismatches from variations in names (such as "Davis" vs. "City of Davis").

![image](/images/ERD_Example.png)

## What is Spatial SQL?
SQL stands for "structured query language" and it's a language that allows you to ask questions of a database.  Spatial SQL is regular SQL but with some additional functions that perform spatial analysis.  Spatial SQL functions typically work on the geometry column.

If you've ever written an attribute query in ArcGIS or QGIS, you've worked with SQL.  Example: Hey GIS program, please highlight all the records in my attribute table that have "Yolo" in the "county" column!  In SQL, we would write `SELECT * FROM city WHERE county = 'Yolo';`  It's actually quicker to write that query than to fill out the interface in the GIS.

A spatial join is an example of a spatial operation that you may have performed in a GIS that can also be performed using SQL. For example, if we had a dataset of business locations, we might query which businesses are inside of city boundaries. `SELECT * from businesses, cities WHERE ST_Intersects(businesses.geometry, city.geometry);` It's much quicker to write the query than fill out the spatial join interface.

## Why should you learn to work with spatial databases and spatial SQL?
* It's a good way to work with large amounts of data
* Typically faster to run a process in a spatial database than in a desktop GIS program
* Store lots of data (compare with shapefile's 70m row limit)
* One database file stores many, many tables --> easier data management
* Write a query instead of making a new file (no exporting of intermediate results to shapefile necessary!)

## What makes this challenging?
If you're a GIS user, you're probably used to a graphical user interface (GUI) where you can see your data, have tools with guided interfaces, and can see the results of your processing immediately.  These aren't things you get with a typical database manager tool, however, we can connect our database to QGIS so we can see our results and, with practice, you will get used to the typical workflow and seeing everything won't be so necessary.

## Databases that support Spatial SQL:
* Oracle
* MySQL
* SQL Server
* SpatiaLite
* PostGRES with the PostGIS extension
* Others too!

We'll be working with SpatiaLite, because it works on all the common computer operating systems and is fairly easy to install.  Once you learn the basics, you can choose the database program that best fits your needs.


---------------------------------------------
# Gyakorlati útmutató


## Adatok

Az adatok, amelyekkel dolgozunk, a San Francisco-öböl vízgyűjtőjére vonatkozó hidrológiával kapcsolatosak.

A következő adatfájlokat kell letölteni az adatmappából, vagy [innen](https://ucdavis.box.com/s/wb5ui86q8z7ppylvcqb8o8pqqawpsj43) (kattintsunk a letöltés gombra a jobb felső sarokban az összes adat .zip fájblan való letöltéséhez):

* Watershed Boundaries (vízgyűjtő határok)(poligon)
* Watershed Centroids (vízgyűjtő súlypontok) (pont)
* Rivers (folyók) (vonal)

Ha a .zip fájlt lett letöltve, használat előtt ki kell csomagolni.

![alt text](https://github.com/MicheleTobias/Spatial_SQL/blob/master/images/DataPreview.png)


![alt text](https://github.com/MicheleTobias/Spatial_SQL/blob/master/images/DataPreview.png)


## A SpatiaLite elindítása
Indítsuk el a SpatiaLite programot:

1. A számítógép fájlkezelőjében keressük meg a mappát, ahová letöltöttük a telepítési fájlokat.
1. Indítsuk el a SpatiaLite programot a `spatialite_gui.exe` fájlra kattintva.


## Adatázis létrehozása
Az adatbázis felfogható úgy is, mint egy mappa, amely az egymáshoz kapcsolódó táblákat tartalmazza.  Ebbe az adatbázisba nem töltünk fel olyan adatokat, amelyek nem kapcsolódnak a többi adathoz (lehetne, de nem ez a lényege egy adatbázisnak).

Létrehozunk egy új, üres adatbázist, melyben tárolhatjuk az adattáblákat:

1. Kattintsunk a "Creating a new (empty) SQLite Database" ("Új (üres) SQLite adatbázis létrehozása") ikonra ![alt text](https://github.com/MicheleTobias/Spatial_SQL/blob/master/images/SpatiaLite_Button_NewDB.PNG)  *Megjegyzés: néhány gombnak hasonló nevei vannak.  NE készítsünk Memory-DB adatbázist. Nem ugyanaz.*
1. Keressük ki a mappát, ahová menteni szeretnénk az adatbázisunkat, például a mappa, ahová letöltöttük a mintaadatokat.  Nevezzük el az adatbázist a következőre: *sfbay.sqlite*, mivel a San Francisco Bay (San Francisco-öböl) adataival fogunk dolgozni.  Igen, mind kisbetűvel. Kattintsunk az OK gombra.

![alt text](https://github.com/MicheleTobias/Spatial_SQL/blob/master/images/SpatiaLite_Inteface_ConnectedDB.PNG)

A Spatialite egyszerre egy adatbázishoz tud csatlakozni, ezért ha egy másik adatbázissal kívánunk dolgozni, előbb le kell csatlakozni a jelenlegiről.


## Az adatbázis feltöltése adatokkal

Adjunk hozzá adatokat az adatbázisunkhoz:

1. Kattintsunk a "Load Shapefile" ("Shapefile betöltése") gombra. ![alt text](https://github.com/MicheleTobias/Spatial_SQL/blob/master/images/SpatiaLite_Button_LoadShapefile.PNG)  *Megjegyzés: NE virtuális shapefile-t töltsünk be.*
1. Keressük ki a mappát, ahová letöltöttük az adatokat.
1. Válasszuk ki a *Flowlines.shp* fájlt és kattintsunk az *Open* gombra.
1. A *Table Name* ("Táblázat megnevezése") szövegdobozba írjuk be: *flowlines* ("áramlási vonalak"). Ez lesz az adattáblánk neve.
1. Az *SRID* szövegdobozba beíradnó szám 3310, mert az adatok California Albers (NAD38) vetületi rendszerben vannak megadva, melynek SRID azonosítója 3310.
2. A *Column Names* ("Oszlopok megnevezése") résznél válasszuk ki a "Convert to Lowercase" ("Konvertálás kisbetűssé") opciót, így minden oszlop megnevezése kisbetűs lesz.
3. A *DBF DATE Values* ("DBF dátumértékek") résznél válasszuk az "as PlainText strings" ("Egyszerű karakterláncok") opciót, így a dátumok emberileg olvashatók lesznek.
4. A *Primary Key Column* ("Elsődleges kulcs oszlop") résznél válasszuk az "Automatic" ("Automatikus") lehetőséget. Ez készíteni fog egy növekvő pk_uid oszlopot annak érdekében, hogy biztosan legyen elsődleges kulcs az adattáblánkban.
5. Tekintsük meg az egyéb beállításokat, de az alapértelmezett értékek maradhatnak.  Ha végeztünk, kattintsunk az *OK* gombra.

A fenti lépéseket megismételve hozzáadhatjuk a másik két rend adatot.  Nevezzük el a *WBDHU8_Points_SF* táblát *centroids*-nak ("centroidok") és a *WBDHU8_SF* táblát *watersheds*-nek ("vízgyűjtők"). A többi paraméter megegyezik a flowlines tábla paramétereivel.

A bal oldali panelben így már három táblázatot kell látnunk.  Gratulálunk!  Van egy adatbázunk, mely a SanFrancisco-öböllel kapcsolatos adatokat tartalmaz.


## Az SQL ablak

Készen állunk arra, hogy elemzést végezzünk adatbázisunkkal.  Előfordulhat, hogy ki kell bontanunk a Spatialite ablakot az ablak jobb alsó sarkának kihúzásával, hogy minden látható legyen.

Egy **query** ("lekérdezés") lényegében információk lekérése az adatbázisból.  A lekérdezéseket az ablak jobb felső sarkában található nagy üres mezőbe kell beírni.  

A lekérdezés futtatásához kattintsunk az *Execute SQL Statement* ("SQL utasítás végrehajtása") gombra a lekérdezés mezőtől jobbra.

A lekérdezés eredménye az alsó mezőben jelenik meg.  Az eredmény lehet egy táblázat, vagy egy üzenet.


## Nem térbeli lekérdezések

A lekérdezéseknek adott a szerkezete. A leginkább használt a "select" ("kiválasztás") parancs.  Ezek a SELECT paranccsal kezdődnek, ezt követi a lekérdezni kívánt információ, majd annak a táblának a neve, amelyből lekérdezzük, végül (opcionálisan) egyéb paraméterek, amelyek korlátozzák az eredményt, vagy fontos figyelmeztetéseket tartalmaznak.  Minden lekérdezést pontosvesszővel kell lezárni.

A nem térbeli lekérdezések olyan lekérdezések, amelyek nem tartalmazzák a táblázatunk geometriai tulajdonságokat tartalmazó oszlopát (azaz a koordinátákat).

Kezdjük a *flowlines* adatainak vizsgálatával. Az *flowlines* tábla lineáris elemeket tartalmaz, amelyek a vizet az egyik helyről a másikra szállítják. Némely ezek közül természetes vízfolyás, például folyók vagy patakok, mások pedig ember alkotta, mint például a csatornák.

### Nézzük a teljes táblázatot:

```SQL 
SELECT * FROM flowlines; 
```

A csillag (\*) azt jelenti, hogy "minden", vagyis "kérem az összes oszlopot".  A lekérdezés olasható úgy is, hogy "Válasszunk ki mindent a *flowlines* táblából."

Az eredménynek úgy kell kinéznie, mint egy szokványos, grafikus GIS-ben látott attribútumtáblázat, egy további oszloppal.  A beolvasási folyamat hozzáadott egy *geometry* mezőt.  A *geometry* mező olyan információkat tartalmaz, amelyek közlik az adatbáziskezelővel, hogy hol kell elhelyezkednie az adott objektumnak a térben, de sajnos emberileg nehezen érthető.  A későbbiekben többet megtudhatunk erről az adatmezőről.

Megjegyzés: hagyományosan a szintaxisban részt vevő szavak, például a `SELECT', csupa nagybetűvel íródnak, míg a táblázatok és oszlopok nevei kisbetűvel, azonban ha nem tartjuk be ezeket a szabályokat, a kód úgy is működni fog.

### WHERE záradék hozzáadása: 

```SQL
SELECT * FROM flowlines WHERE ftype = 460; 
``` 

Ez a lekérdezés azokra a sorokra korlátozza az eredményt, ahol az *ftype* oszlop értéke 460, ami a természetes folyóknak és patakoknak (nem csatornáknak) felel meg..  A "where" (ahol) ebben az esetben **NEM** helyszínt, hanem egy feltételt közvetít.

### Függvény hozzáadása: 

```SQL
SELECT COUNT(pk_uid) FROM flowlines WHERE ftype = 460; 
``` 

Itt hozzáadtuk a COUNT() (számol) függvényt.  Megkértük az adatbáziskezelőt, hogy számolja meg az összes ID-t, ahol az ftype értéke 460.

### Adatok összegzése

What if we wanted to know how many lines there were of each *ftype*?

```SQL
SELECT ftype, COUNT(pk_uid) FROM flowlines GROUP BY ftype;
```

Itt lekértük egy táblázatból az *ftype* oszlopot és az összes *pk_uid* darabszámát, végül pedig csoportosítjuk (group by) az *ftype* értékei szerint.

Ha nem tetszik az automatikusan generált oszlopmegnevezés - `COUNT(pk_uid)` - az AS paranccsal ezt megváltoztathatjuk:

```SQL 
SELECT ftype, COUNT(pk_uid) AS count_lines FROM flowlines GROUP BY ftype; 
```

Ez különösen akkor hasznos, ha olyan emberek számára készítünk táblázatot, akik nem ismerik az adatokat vagy az SQL-t, vagy ha az oszlop konkrét névvel kell rendelkezzen.

Az előbbiekben megvizsgáltunk néhány lehetőséget a nem térbeli adatok lekérdezésére.  Bővebb információkért kattints a következő linkre: [Intro to SQL Workshop](https://github.com/MicheleTobias/Workshop-SQL).

## Példák alapvető térbeli lekérdezésekre:

A következőkben megismerjük a térbeli lekérdezéseket.  A térbeli lekérdezések jellemzően a tábla geometriai oszlopán működnek.  Néhány esetben az oszlop megnevezése "geometry helyett" egyszerűen csak "geom", de nem ez lényeges, hanem a benne tárolt adatok.

### Geometria megtekintése
Kezdjük a térbeli lekérdezések megértését a geometria oszlop megtekintésével: 

```SQL 
SELECT geometry FROM flowlines;
``` 

Ez az eredmény nem mond túl sok információt, mivel a BLOB emberileg nem értelmezhető.  Ezért nézzük meg egyszerű szövegben, hogy el értelmezni tudjuk:

```SQL 
SELECT ST_AsText(geometry) FROM flowlines;
``` 

A *ST_AsText()* egy függvény, amely a geometria oszlopon hívható meg annak érdekében, hogy a geometriai karakterláncot ember által is értelmezhető formátumban láthassuk.  Ez legtöbbször nem túl hasznos, de talán megnyugtató a tudat, hogy létezik.  *Megjegyzés: Az eredménytáblázat oszlopai átméretezhetők, ha az egérmutatót az oszlop széle fölé helyezzük, és a bővítő kurzor megjelenése után kihúzzuk. (két ellentétes irányba mutató nyílként jelenik meg).*

### Hossz
Csináljunk egy elemzést, amivel gyakran találkozhatunk.  Kérjük le a *flowlines* adattábla elemeinek hosszát: 
```SQL
SELECT pk_uid, ST_Length(geometry) FROM flowlines;
``` 

Mi a hossz lekérdezésének mértékegysége?  Méter, mivel az alkalmazott vetületnek (California Albers; SRID 3310) is méter az alapmértékegysége.

Ezzel megkaptuk a *flowlines* elemeinek hosszát.  Ez nem volt túl informatív lekérdezés.  Hasznosabb lenne tudni, mekkora a vízfolyások teljes hossza az fcode mező összegzése alapján.  

```SQL
SELECT fcode, SUM(ST_Length(geometry)) FROM flowlines GROUP BY fcode;
```

### Terüelt

Először is vessünk egy pillantást a watershed poligon táblázatunkra:

```SQL
SELECT * FROM watersheds;
```

Most nézzünk egy példát a *watershed* poligonok területének lekérdezésére: 

```SQL
SELECT name, ST_Area(geometry) FROM watersheds;
``` 

Ne feledjük, hogy ha nem tetszenek az eredményül kapott oszlopmegnevezések, akkor az *AS* záradékkal átnevezhetjük őket.. Azonban tartsuk észben azt, hogy ez csak a lekérdezés eredményében módosítja az oszlop nevét, az alapul szolgáló táblában nem.

```SQL
SELECT name, ST_Area(geometry) AS area FROM watersheds;
``` 

## Vetületek
Mivel térbeli adatokkal dolgozunk, tudnunk kell kezelni a vetületeket.

### Set the Projection
When you imported your data into Spatialite, it asked you what the SRID (EPSG Code) was for your data.  It's easy to forget to do this or to put in the wrong one if you're in a hurry.  If you discover that you've made a mistake, you don't need to re-import the table; you can set the SRID to the correct projection with an update command.  

To check your projection, you can ask the database to look in one of its internal tables. For our data, it looks like this:

```SQL
SELECT * FROM geometry_columns
WHERE f_table_name = 'flowlines';
```

For our data, setting the projection would look like this: 

```SQL
UPDATE flowlines SET geometry = SetSRID(geometry, 3310);
```

This query replaces the contents of the *geometry* column with the results of the SetSRID command.  In our case, it doesn't really do anything new since we had our projection set correctly, but you should know how to do this, so we did.

### Reproject
To change the projection of a dataset, you need to use the `Transform` or `ST_Transform` command.

Let's transform our watershed data into UTM Zone 10 North, the zone that San Francisco falls into.

First, we'll start with a query that results in a returning information (but doesn't make a new table):

```SQL
SELECT pk_uid, huc8, name, geometry FROM watersheds;
```

We have a table with a subset of the columns from the original watersheds table.  Now let's work on transforming the *geom* column.  We'll add a function around the *geometry* column to reproject the data.  26910 is the SRID for UTM Zone 10 N.

```SQL
SELECT pk_uid, huc8, name, ST_Transform(geometry, 26910) FROM watersheds;
```

It may look like nothing happened, but the column heading on the *geometry* column should have changed.  Finally, we'll want do something to keep this data.  Remember that a SELECT statement just returns information, it doesn't save it.  We have two options.  (1) We could overwrite the *geometry* column of the *watersheds* table, but that will mean the projection won't match the other data we have.  (2) The other option is to make a new table with a different projection.  We can do this by adding a CREATE TABLE statement to the query we already have (adding an alias to the geometry column):

```SQL
CREATE TABLE watersheds_utm_10n AS 
SELECT pk_uid, huc8, name, ST_Transform(geometry, 26910) AS geometry 
FROM watersheds;
```

To see the new table in the list, we'll need to refresh our database.  On the left panel, right click on "User Data" and select *Refresh*.  Now we can see the table, but it's just a table.  Notice that the icon is different from the other tables.  The database doesn't seem to know the table is actually polygons.

```SELECT * FROM watersheds_utm_10n;``` Shows that all the columns we asked for, including the *geometry* column are there.  What's going on?  We need to recover the geometry column so the database will recognize the table as a spatial table.

```SQL
SELECT RecoverGeometryColumn('watersheds_utm_10n', 'geometry', 26910, 'MULTIPOLYGON', 'XY');
```

This query will return a single column and row with the number 1 in the only cell.  If it returns 0, the query didn't work.  

Now we need to vacuum the database (yes, that sounds a little odd).  Run the command ```VACUUM;``` in the SQL Query panel.  This will clean up any issues created by all the changes we just made to the database.

You may need to refresh your database list again before the icon will change.

## Spatial Join
Spatial joins allow us to combine information from one table with another based on the location associated with each record.  When we write a query involving two or more tables, we need to specify which table any column names come from.  We do this by giving the table name before the column - ```table.column``` .  Let's see if we can figure out which watershed each of our flowlines is in:

```SQL
SELECT flowlines.*, watersheds.name AS watershed_name
FROM flowlines, watersheds
WHERE ST_Contains(watersheds.geometry, flowlines.geometry);
```

Your table should look just like your *flowlines* table, but we've added the *NAME* column from our *watersheds* table (but called it "Watershed_Name" because this will make more sense if we needed to use the this data later and didn't remember where this information came from). 

ST_Contains tells us if a line is completely within a particular watersheds polygon.  How would you change this query to identify which watershed each line *intersects* rather than is *contained by*? Hint: [SpatiaLite Function Reference List](http://www.gaia-gis.it/gaia-sins/spatialite-sql-4.2.0.html) 

## Views vs. Tables
We've discussed that one of the big reasons to use a database is not needing to save a bunch of intermediate data products.  You just keep your data in tables and you write queries to combine them in various ways.  But what if you find yourself needing the results a particular query quite often?  Do you save a table?  There's another option: the view.  A view is like a saved query.  It behaves like a table so you can call it in other queries or view it in QGIS, but the records in the table are generated by running a query rather than storing the same data over again.

Let's make a view!  Let's say we want to have a view that contains all the rivers (so, FTYPE = 460, like before) that are in the Tomales-Drake Bays watershed and let's put the watershed name into the table as well.

First, let's make a query to make sure we're getting the information we want.

```SQL
SELECT flowlines.*, watersheds.name AS watershed_name
FROM flowlines, watersheds
WHERE ST_Contains(watersheds.geometry, flowlines.geometry)
AND ftype = 460
AND watersheds.name LIKE 'Tomales-Drake Bays';
```

That query looks like what we want.  Let's make it into a view.  View creation sytax is really similar to the way you make a table:

```SQL
CREATE VIEW rivers_tomales AS
SELECT flowlines.*, watersheds.name AS watershed_name
FROM flowlines, watersheds
WHERE ST_Contains(watersheds.geometry, flowlines.geometry)
AND ftype = 460
AND watersheds.name LIKE 'Tomales-Drake Bays';
```

Refresh your table list to see if on the left side of the screen.  This is a non-spatial view.  To have the database recognize it as a spatial view, we need one more step that registers the view in the table that keeps track of which views are spatial.  (If this sounds complicated, just file this away as a step that needs to get done.)

```SQL
INSERT INTO views_geometry_columns
(view_name, view_geometry, view_rowid, f_table_name, f_geometry_column, read_only)
VALUES ('rivers_tomales', 'geometry', 'pk_uid', 'flowlines', 'geometry', 0);
```

What does this query do?  I'll break it down.

```INSERT INTO views_geometry_columns``` tells the databse to put data into the *views_geometry_columns* table.  This is a table that comes standard with Spatialite databases.  It was there as soon as we made the database.  It tells the rest of the databse which views are spatial.  There's a similar table for listing spatial tables.  

```(view_name, view_geometry, view_rowid, f_table_name, f_geometry_column, read_only)``` is the list of columns we want to put information into in the *views_geometry_columns* table.  We'll be making a new row of data in this table, and these are the columns where that data is going to go.

```VALUES ('rivers_tomales', 'geometry', 'pk_uid', 'flowlines', 'geometry', 0)``` 

*VALUES* says to the database, "here is the list of items to put in the columns I told you about in the last line", then we put the list of things in parentheses.  So 'rivers_tomales' (the new table name) will go into the 'view_name' field. 'geometry' is the geometry column for the view so it goes into the 'view_geometry' field of the table. 'flowlines' is the table that our 'rivers_tomales' view inherits its spatial data from and it has a geometry column called 'geometry' as well.  Finally, 'read_only' takes either a 0 to make the table read-only or 1 to make it writable.  Read-only is a good choice here since we won't be adding or changing the view's contents.

Refresh your table list to see that rivers_tomales is now a spatial view. 

```SELECT * FROM rivers_tomales;``` to see your new view.  It will function just like a table.


## Táblák megjelenítése QGIS-ben

A táblázatok és nézetek hasznosak, de ezek térbeli adatok, így sokkal szemléletesebb, ha térkép formájában tekintjük az adatokat. A térbeli táblázatainkat és nézeteinket közvetlenül a QGIS-ben tekinthetjük meg. Nézzünk néhány táblázatot és nézetet:

1. Nyissuk meg a QGIS-t és hozzunk létre egy új projektet.
1. A Browser Panelben (Böngésző panel) (általában a bal oldalon... adjuk hozzá, ha hiányzik: *View* (Nézet) menü -> *Panels* (Panelek) -> *Browser Panel* (Böngésző)) görgessünk le a SpatiaLite ikonhoz.  Ha korábban már csatlakoztattunk SpatiaLite táblákat, akkor egy kis háromszög jelenik meg a lista kibontásához.  Ha nem, akkor csak az ikon és a szövegcím.
1. Jobb klikk a SpatiaLite szakaszra, majd válasszuk ki a *New connection* (Új kapcsolat) opciót.
1. Keressük meg és válasszuk ki a *sfbay.sqlite* adatbázist.  Kattintsunk az *Open* (Megnyitás) gombra.
1. Nyissuk ki a *sfbay*.sqlite adatbázist, hogy lássuk a táblákat és a nézeteket.  Ez listázza az általunk létrehozott táblákat, valamint az alapértelmezett táblákat, amelyek az adatbázishoz tartoznak, és amelyek miatt nem kell aggódnunk.
1. Kattintsunk duplán a táblázatokra vagy nézetekre a térképnézethez való hozzáadáshoz.

**Megjegyzés:** A sok rekordot tartalmazó táblázatok és (főleg) nézetek betöltése eltart egy ideig.  Ha egy párbeszédpanel a használni kívánt CRS-transzformációra kérdez rá, válasszuk ki az adatoknak megfelelő opciót, majd kattintsunk az *OK* gombra. Érdemes egyesével hozzáadni a táblakat, majd menteni a QGIS-projektet minden tábla hozzáadása után, így elkerülhetők a program esetleges összeomlásából adódó kellemetlenségek.

Mostantól elérhetők a szimbólumok, címkék és egyéb szabványos eszközök a *Layer Properties* (Tulajdonságok) fülön, csakúgy, mint bármely más térbeli adatkészlethez a QGIS-ben.

**Megjegyzés:**  A QGIS DB Manager eszköze a Spatialite alternatívája, amely a QGIS alapfelszereltségéhez tartozik, és számos geoadatbázishoz képes csatlakozni.


## További térbeli elemzések:
Nem meglepő, hogy a geoadatbázisok segítségével többet is tehetünk, mint a meglévő geometriák hosszának és területeinek lekérését, vagy a vetületek megváltoztatását. Térjünk vissza a Spatialite-hoz, és fedezzünk fel néhány további térelemzési lehetőséget.


### Távolság
Derítsük ki, hogy melyik vízgyűjtő van a legközelebb San Francisco városához. Ezt többféleképpen is megtehetnénk, de nézzük meg a távolságot a város középpontja és az egyes vízgyűjtők súlypontja között:

```SQL
SELECT ST_Distance(MakePoint(37.7749, -122.4194, 3310), centroids.geometry) 
FROM centroids;
```

Itt a `MakePoint()` segítségével a szélességi/hosszúsági koordináták halmazát olyan formátumra alakítottuk, amelyet az adatbázis-kezelő értelmezni tud a CA Albers vetületben (EPSG 3310), majd az eredményeket elhelyezi a `Distance()` függvényben.

Hogyan tehetnénk ezt a táblázatot még informatívabbá? Hozzá tudnánk adni vagy átnevezni néhány oszlopot?
 

### Védőzóna & egymásba ágyazott függvények
Az SQL egyik érdekessége, hogy a függvényeket egymásba ágyazhatjuk, hogy egy lekérdezés függvények sorozatát hajtsa végre, ahogyan azt fentebb is látható volt, azonban ez egyre bonyolultabb lehet. Például szeretnénk megtudni az összes *áramlási vonal* 1 kilométeres körzetében elhelyezkedő területet (négyzetkilométerben).

```SQL
SELECT sum(ST_Area(ST_Buffer(geometry, 1000)))/1000000 FROM flowlines;
```

Itt vesszük az 1000 méteres védőzónák területének összegét, majd az egészet elosztjuk 1 000 000-rel, hogy a négyzetmétert négyzetkilométerré alakítsuk át. Nahát. Ez elég bonyolult. De nem kellett egy csomó köztes fájlt készítenünk és oszlopokat hozzáadnunk egy attribútumtáblához, lementenünk egy CSV-fájlt, majd mindezt Excelben összegeznünk. Most melyik opció hangzik őrültebben? Talán kezdjük már érezni a térbeli SQL erejét.


# Záró gondolatok
Ebben a cikkben bevezetőt kaptunk a geoadatbázisok és a térbeli SQL világába. Ez természetesen nem minden, amit ezekkel az eszközökkel megtehetünk; még csak alig-alig kapargattuk a felszínt! Az alábbi linkeken további információk érhetők el a geoadatbázisok lehetőségeiről. Ezenkívül egy jól kidolgozott Google-keresés hasznos információkat és oktatóanyagokat nyújthat az új készségek elsajátításához.



# Egyéb források:

## Térbeli SQL-lel kapcsolatos linkek:

[SpatiaLite Function Reference List](http://www.gaia-gis.it/gaia-sins/spatialite-sql-4.2.0.html)

[SpatiaLite Cookbook](https://www.gaia-gis.it/spatialite-3.0.0-BETA/spatialite-cookbook/index.html)

## Általános diasorok & Oktatóanyagok:

[Todd Barr's Slides](https://www.slideshare.net/gis_todd/foss4g-2017-spatial-sql-for-rookies)

[Mike Miller's Tutorial](http://millermountain.com/geospatialblog/2017/10/23/qgis-and-spatialite/)
