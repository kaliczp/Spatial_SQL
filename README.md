# Spatial SQL SpatiaLite programmal
Spatial SQL bevezető tanfolyam SpatiaLite használatával.

**Szerzők:** [Michele Tobias](https://github.com/MicheleTobias) & [Naomi Kalman](https://github.com/nbkalman)

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

## Szoftver

**A SpatiaLite GUI telepítése:**  A SpatiaLite GUI telepítési útmutatója operációs rendszerenként eltérő.  Ehhez adunk segítséget [a telepítési útmutatóban](https://github.com/kaliczp/Spatial_SQL/blob/Hun/Install.md). A tanfolyam anyaga a Spatialite 5.0.0 és Spatialite GUI 2.1.0-beta1 verziójához készült.

**A QGIS telepítése:** Telepítenie kellett a QGIS aktuális verzióját a [QGIS.org](https://qgis.org/en/site/forusers/download.html) webhelyről. A QGIS régebbi verziói is elegendőek lesznek. QGIS-t fogunk használni a SpatiaLite adatbázisunkba importált téradattáblázataink megjelenítésére.

# Fogalmak

## Mi az adatbázis?
Az adatbázis olyan táblázatokban lévő adatok halmaza, amelyek valamilyen módon kapcsolódnak egymáshoz. Azaz; **Az adatbázis kapcsolódó táblák gyűjteménye.**

Általában minden tábla összekapcsolható egy másik táblával egy olyan oszlop segítségével, amellyel mindkét tábla rendelkezik, és amely tárolja a sorok egyeztetéséhez szükséges információkat. Ezt az oszlopot **kulcsnak** nevezik. Például hallgatók vagy alkalmazottak azonosítószáma az egyetemeken gyakran használt kulcs.

Ha térinformatikai rendszerben dolgozunk, akkor már adatbázist használunk. A shapefile lényegében csak egy kidíszitett táblázat. És ha összekapcsolunk egy .csv táblázatot egy shapefájllal, akkor egy összekapcsolás (join) nevű adatbázis műveletet végzünk. Ha több térbeli adatkészletet használunk egy térinformatikai rendszerben, akkor az az adatokat táblázat helyett koordináta szerint kapcsolja össze.

## Mi az a térbeli adatbázis?
A térbeli adatbázis egy közönséges adatbázis (azaz kapcsolódó táblák halmaza), de legalább az egyik táblának van egy oszlopa, amely tartalmazza a térbeli információkat, amelyeket általában "geometriának" neveznek. A geometriai információkat a rendszer bináris nagy objektumként (Binary Large Object = BLOB) tárolja. A geometria lehetővé teszi a táblázatok adatainak összekapcsolását azok térbeli elhelyezkedése alapján, valamint lehetővé teszik az adatok térbeli elemzését.

Az alábbiakban egy példa látható egy adatbázis-entitás-kapcsolati diagramra (elképzelt, mégis valószerű) térbeli minta-adatbázis. Minden táblázatban legalább egy oszlop egy másik táblázat oszlopához kapcsolódik (a diagramon a két oszlop közé húzott vonal jelzi). Egy adatbázis ilyen diagrammal történő dokumentálása általános gyakorlat. Gyors vizuális hivatkozást biztosít az egyes táblákban található adatokhoz és a táblázatok egymáshoz való viszonyához. A város (`city`) tábla egy térbeli táblázat. A geometria (`geometry`) oszlop azokat a térinformációkat tartalmazza, amelyek lehetővé teszik a várost leíró poligonok leképezését. A többi táblázat olyan táblázatos adat, amely a térbeli táblához a kulccsal kapcsolható, jelen esetben egy kóddal (Federal Information Processing System = FIPS), egy olyan rendszerrel, amely egyedi numerikus kódokat rendel a földrajzi entitásokhoz, például városokhoz, megyékhez és államokhoz. Ezeknek a FIPS kódoknak a segítségével egy olyan egyértelmű kulcsot alkalmazhatunk, amely a névváltozatokból eredő ellentmondásokat (például „Davis” és „City of Davis”) kezeli.

![image](/images/ERD_Example.png)

## Mi az a térbeli SQL?
Az SQL a "structured query language = strukturált lekérdezési nyelv" rövidítése, egy olyan nyelvé, amellyel kérdéseket tehet fel egy adatbázissal kapcsolatban. A térbeli SQL normál SQL, de néhány további funkcióval is rendelkezik, amelyekkel térbeli elemzést végezhetünk. A térbeli SQL-függvények általában a geometria oszlopon működnek.

Ha attribútumlekérdezést használunk ArcGIS-ben vagy QGIS-ben, akkor SQL-lel dolgozunk. Egy egyszerű példa szavakkal leírva: Kedves térinformatikai program, kérem, jelölje ki az attribútumtáblámban az összes olyan rekordot, amelynél a "megye" oszlopban "Yolo" szerepel! SQL-ben azt írnánk, hogy `SELECT * FROM city WHERE megye = 'Yolo';` Valójában gyorsabb a lekérdezés megírása, mint a GIS-ben lévő lekérdező-felület kitöltése.

A térbeli összekapcsolás (join) jó példa térbeli műveletre, amelyet egy térinformatikai rendszerben is, és SQL használatával is végrehajtható. Például, ha van egy térbeli adatbázisunk üzletekről, akkor lekérdezhetjük, hogy mely vállalkozások vannak a város határain belül. `SELECT * from businesses, cities WHERE ST_Intersects(businesses.geometry, city.geometry);` Sokkal gyorsabb a lekérdezés megírása, mint a GIS program térbeli összekapcsolási felületének kitöltése.

## Miért érdemes megtanulni téradatbázisokkal és térbeli SQL-lel dolgozni?
* Ez egy jó módja annak, hogy nagy mennyiségű adattal dolgozzon
* Általában gyorsabban fut egy folyamat egy térbeli adatbázisban, mint egy asztali GIS programban
* Sok adat tárolása (hasonlítsa össze a shapefile 70 méteres sorkorlátjával)
* Egy adatbázisfájl sok-sok táblát tárol --> könnyebb adatkezelés
* Írjon lekérdezést ahelyett, hogy új fájlt készítene (nincs szükséges a közbenső eredmények exportálása alakfájlba!)

## Mi teszi ezt kihívást?
Ha Ön GIS-felhasználó, valószínűleg hozzászokott a grafikus felhasználói felülethez (GUI), ahol láthatja adatait, irányított felülettel rendelkező eszközökkel rendelkezik, és azonnal láthatja a feldolgozás eredményét. Ezek nem olyan dolgok, amiket egy tipikus adatbázis-kezelő eszközzel kapsz, de összekapcsolhatjuk adatbázisunkat a QGIS-sel, így láthatjuk az eredményeinket, és gyakorlással megszokhatod a tipikus munkafolyamatot, és látni fogod, hogy minden nem lesz így. szükséges.

## A Spatial SQL-t támogató adatbáziskezelők:
* Oracle
* MySQL
* SQL Server
* SpatiaLite
* PostGRES PostGIS kiterjesztéssel
* és sok további rendszer!

A tanfolyam során SpatialLite-al fogunk dolgozin, mert minden általánosan elterjedt operációs renszeren működik és a telepítése egyszerű. Miután az alapokat megismertük, később kiválaszthatjuk azt az adatbáziskezelő renszert, amely legjobban illeszkedik a feladathoz.


---------------------------------------------
# Gyakorlati útmutató


## Adatok

Az adatbázis, amellyel dolgozunk, a San Francisco-öböl vízgyűjtőjének vízrajzi adatait tartalmazza.

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

Ez a lekérdezés azokra a sorokra korlátozza az eredményt, ahol az *ftype* oszlop értéke 460, ami a természetes folyóknak és patakoknak (nem csatornáknak) felel meg.  A "where" (ahol) ebben az esetben **NEM** helyszínt, hanem egy feltételt közvetít.

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

### Terület

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

### Vetület beállítása
Amikor importáltuk az adatokat a SpatiaLite-ba, a rendszer rákérdezett, hogy mi a hozzájuk tartozó SRID (EPSG kód).  Ezt a lépést könnyen figyelmen kívül hagyhatjuk, vagy rosszat adatot adhatunk meg nagy sietségünkben.  Ha kiderül, hogy hiányzik vagy hibás ez az adat, nem kell újra importálni a táblázatot; egy frissítési paranccsal beállíthatjuk az SRID-t a megfelelő vetületre.  

A vetület ellenőrzéséhez megkérhetjük az adatbázist, hogy nézzen bele valamelyik belső táblájába. Az adatainkkal ez a következőképpen néz ki:

```SQL
SELECT * FROM geometry_columns
WHERE f_table_name = 'flowlines';
```

A vetület beállítása a következőképpen néz ki: 

```SQL
UPDATE flowlines SET geometry = SetSRID(geometry, 3310);
```

Ez a lekérdezés lecseréli a *geometry* oszlop tartalmát a SetSRID parancsban megadottra.  A mi esetünkben ez nem igazán jelent semminemű újítást, mivel a vetületet megfelelően állítottuk be, de elvégeztük az utasítást, hogy megismerhessük, hogyan kell.

### Vetülettranszformáció
Az adatkészlet vetületének megváltoztatásához a `Transform` vagy `ST_Transform` parancsot kell használnunk.

Transzformáljuk át a vízgyűjtők adatait UTM Zone 10 North vetületbe, ahová San Francisco is tartozik.

Először egy lekérdezéssel kezdjük (amely nem hoz létre új táblázatot):

```SQL
SELECT pk_uid, huc8, name, geometry FROM watersheds;
```

Van tehát a lekérdezés eredményeként egy táblázatunk, ami az eredeti, vízgyűjtőket tartalmazó táblázat oszlopainak egy részhalmaza.  Most következik a *geom* oszlop átalakítása.  A *geometry* oszlophoz egy függvényt rendelünk az adatok vetülettranszformációjához.  Az UTM Zone 10 North vetület SRID száma 26910.

```SQL
SELECT pk_uid, huc8, name, ST_Transform(geometry, 26910) FROM watersheds;
```

Úgy tűnhet, semmi sem történt, de a *geometria* oszlop oszlopfejlécének meg kellett változnia.  Végül tennünk kell valamit a transzofrmált adatok megőrzéséért.  Ne feledjük, hogy a SELECT utasítás csak információt jelenít meg, de azt nem menti el.  Két lehetőségünk van.  (1) Felülírhatjuk a *watersheds* tábla *geometry* oszlopát, de így a vetület nem fog egyezni a többi adattal.  (2) A másik lehetőség az, hogy új táblázatot készítünk az új vetülettel.  Ezt úgy tehetjük meg, hogy a már meglévő lekérdezéshez hozzáadunk egy CREATE TABLE utasítást (AS utasítással a geometria oszlopot töltjük fel):

```SQL
CREATE TABLE watersheds_utm_10n AS 
SELECT pk_uid, huc8, name, ST_Transform(geometry, 26910) AS geometry 
FROM watersheds;
```

Ahhoz, hogy az új táblázat megjelenjen a listában, frissítenünk kell az adatbázisunkat.  A bal oldali panelen kattintsunk jobb gombbal a "User Data" ("Felhasználói adatok") elemre, és válasszuk a *Refresh* (Frissítés) lehetőséget.  Most már láthatjuk a táblát, de ez csak egy tábla.  Az ikon eltér a többi táblától.  Úgy tűnik, hogy az adatbázis nem tudja, hogy a tábla valójában poligonok összessége.

```SELECT * FROM watersheds_utm_10n;``` Mutatja, hogy az összes oszlop, amit lekértünk, beleértve a *geometria* oszlopot is, létezik.  Mi történik?  Helyre kell állítanunk a geometriai oszlopot, hogy az adatbázis térbeli táblaként ismerje fel a táblát.

```SQL
SELECT RecoverGeometryColumn('watersheds_utm_10n', 'geometry', 26910, 'MULTIPOLYGON', 'XY');
```

Ez a lekérdezés egyetlen oszlopot és sort ad vissza, a cellában 1-es számmal.  Ha 0-t ad vissza, akkor a lekérdezés nem működött.  

Most ki kell porszívózni az adatbázist (igen, ez kicsit furcsán hangzik).  Futtassuk a ```VACUUM;``` utasítást az SQL Query panelben.  Ezzel kiküszöböljük az adatbázison végzett módosítások által okozott problémákat.

Előfordulhat, hogy újra frissítenünk kell az adatbázislistát, hogy megváltozzon az ikon.

## Térbeli csatolás
A térbeli csatolások lehetővé teszik számunkra, hogy az egyes rekordokhoz társított hely alapján egyesítsük az egyik táblából származó információkat egy másik tábla rekordjaival.  Amikor két vagy több táblát tartalmazó lekérdezést írunk, meg kell adnunk, hogy az oszlopnevek melyik táblából származnak. Ezt úgy tehetjük meg, hogy az oszlop neve előtt megadjuk a tábla nevét  - ```tábla.oszlop``` .  Lássuk, le tudjuk-e kérni, hogy az egyes áramlási vonalaink melyik vízgyűjtő területén helyezkednek el:

```SQL
SELECT flowlines.*, watersheds.name AS watershed_name
FROM flowlines, watersheds
WHERE ST_Contains(watersheds.geometry, flowlines.geometry);
```

A táblázatnak ugyanúgy kell kinéznie, mint a *flowlines* táblának, de hozzáadtuk a *NAME* oszlopot a *watersheds* táblázatunkból (de "Watershed_Name"-nek elnevezve, mert így több értelme lesz, ha később fel kell használnunk ezeket az adatokat, és nem emlékeznénk, honnan származik ez az információ). 

ST_Contains megmondja, hogy egy vonal teljesen egy adott vízgyűjtő poligonon belül van-e.  Hogyan változtatnánk meg ezt a lekérdezést annak meghatározására, hogy az egyes vonalak melyik vízgyűjtőt *metszik*, nem pedig *tartalmazzák*? Segítség: [SpatiaLite Function Reference List](http://www.gaia-gis.it/gaia-sins/spatialite-sql-4.2.0.html) 

## Nézetek vs. Táblák
Volt már szó arról, hogy az adatbázis használatának egyik fő oka az, hogy nem kell menteni egy csomó köztes adatterméket.  Csak táblázatokban tároljuk az adatokat, és lekérdezéseket írunk, hogy különféle módon kombináljuk őket.  De mi van akkor, ha gyakran szükségünk van egy adott lekérdezés eredményeire?  Mentünk egy táblát?  Van egy másik lehetőség: a nézetek.  Egy nézet olyan, mint egy elmentett lekérdezés.  Úgy viselkedik, mint egy tábla, így más lekérdezésekben is meghívhatjuk, vagy megtekinthetjük a QGIS-ben, de a tábla rekordjai egy lekérdezés futtatásával jönnek létre, ahelyett, hogy ugyanazokat az adatokat újra eltárolnánk.

Készítsünk egy nézetet! Tegyük fel, hogy szeretnénk egy olyan nézetet, amely az összes olyan folyót tartalmazza (tehát FTYPE = 460, mint eddig), amelyek a Tomales-Drake Bays vízgyűjtő területén folynak, és tegyük be a vízgyűjtő nevét is a táblázatba.

Először is végezzünk egy lekérdezést, hogy megbizonyosodjunk arról, hogy a kívánt információkat kapjuk-e meg.

```SQL
SELECT flowlines.*, watersheds.name AS watershed_name
FROM flowlines, watersheds
WHERE ST_Contains(watersheds.geometry, flowlines.geometry)
AND ftype = 460
AND watersheds.name LIKE 'Tomales-Drake Bays';
```

Ez a lekérdezés úgy néz ki, mint amilyet szeretnénk.  Csináljunk belőle egy nézetet.  A nézet létrehozásának szintaxisa nagyon hasonlít a táblázat létrehozásához:

```SQL
CREATE VIEW rivers_tomales AS
SELECT flowlines.*, watersheds.name AS watershed_name
FROM flowlines, watersheds
WHERE ST_Contains(watersheds.geometry, flowlines.geometry)
AND ftype = 460
AND watersheds.name LIKE 'Tomales-Drake Bays';
```

Frissítsük a táblázatlistát, majd ellenőrizzük, hogy megjelent-e a képernyő bal oldalán.  
Ez egy nem térbeli nézet.  Ahhoz, hogy az adatbázis térbeli nézetként ismerje fel, szükségünk van még egy lépésre, amely regisztrálja a nézetet a táblázatban, amely a térbeli nézeteket tartalmazza.  (Ha ez bonyolultnak hangzik, egyszerűen vessük el ezt a lépést).

```SQL
INSERT INTO views_geometry_columns
(view_name, view_geometry, view_rowid, f_table_name, f_geometry_column, read_only)
VALUES ('rivers_tomales', 'geometry', 'pk_uid', 'flowlines', 'geometry', 0);
```

Mit csinál ez a lekérdezés?  Íme.

```INSERT INTO views_geometry_columns``` utasítja az adatbázist, hogy helyezze az adatokat a *views_geometry_columns* táblába.  Ez egy olyan tábla, amely a SpatiaLite adatbázisok részét képezik.  Már akkor ott volt, amikor elkészítettük az adatbázist.  Ez a tábla közli az adatbázis többi részével, hogy mely nézetek térbeliek.  Van egy hasonló tábla a térbeli táblázatok listázásához.  

```(view_name, view_geometry, view_rowid, f_table_name, f_geometry_column, read_only)``` azoknak az oszlopoknak a listája, amelyekbe információkat szeretnénk elhelyezni a *views_geometry_columns* táblában.  Egy új adatsort hozunk létre a táblázatban, és ezek azok az oszlopok, amelyekbe az adatok kerülnek.

```VALUES ('rivers_tomales', 'geometry', 'pk_uid', 'flowlines', 'geometry', 0)``` 

*VALUES* közli az adatbázissal, hogy "itt van azoknak az elemeknek a listája, amelyeket a fentebb felsorolt oszlopokba kell illeszteni", majd a zárójelbe kerülnek a konkrét értékek.  Tehát a 'rivers_tomales' (az új táblázat neve) a 'view_name' mezőbe kerül. A 'geometry' a geometria oszlopa a nézetnek, tehát ez a táblázat 'view_geometry' mezőjébe kerül.  A 'flowlines' az a táblázat, amelyből a 'rivers_tomales' nézet megörökli a térbeli adatokat, van egy 'geometry' nevű oszlopa is.  Vegül a 'read_only' mező értéke vagy 0, hogy a táblázat csak olvasható legyen, vagy 1, hogy írható is legyen.  Az írásvédettség jó választás ebben a helyzetben, mivel nem adunk hozzá semmit, vagy módosítjuk a nézet tartalmát.

Frissítsük a táblázatok listáját, így látható lesz, hogy a rivers_tomales immmár egy térbeli nézet.  

```SELECT * FROM rivers_tomales;``` hogy láthassuk az új nézetet.  Úgy működik, mint egy táblázat.


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
