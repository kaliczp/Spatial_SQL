# A Spatialite GUI (Graphical User Interface = grafikus felhasználói felület) telepítési útmutatója

## Windows

  * Töltse le és telepítse a [7-zip archiváló programot](https://www.7-zip.org/) A 64 bites verzió a legtöbb ember számára működik. A 7-zip fájlok tömörítésére és kibontására szolgáló szoftver, amely a beépített Windows tömörítőnél nagyobb tudású.
  * Töltse le a [Spatialite Installer](http://www.gaia-gis.it/gaia-sins/windows-bin-amd64/) `spatialite-tools-5.0.1-win-amd64.7z` nevű tömörített fájlt.
  * Töltse le a [Spatialite GUI](https://ucdavis.box.com/s/4vgb1uq6v6bxw94blqoo7ubfv7lvtu4n) `spatialite_gui.zip` nevű fájlt.
  * A letöltött fájlok kicsomagolásához használja a 7-zip-et. A fájlkezelőben a fájl nevére jobb gombbal kattintva a megfelelő helyre. A kicsomagolt mappáknak ugyanazon a szinten kell lenniük a fájlböngészőben (azaz ennek a két mappának ugyanabban a mappában kell lennie). Windows 11 esetén előfordulhat, hogy először a „További beállítások megjelenítése” lehetőséget kell választania. Válassza a 7-Zip-et, majd a "Fájlok kibontása..." lehetőséget, majd válassza ki, hogy hova helyezze a kibontandó fájlokat.
  * Kattintson a spatialite_gui.exe fájlra a munka megkezdéséhez.
 
  
  *Fent a közvetlenül használható fájlok linkjeit találja, hogy ne kelljen őket keresni.*

A Spatialite GUI könnyű használatára további lehetőség a [MSYS2 csomagkezelő Windowshoz](https://packages.msys2.org/package/mingw-w64-x86_64-spatialite-gui?repo=mingw64) telepítése.

## Mac

  * Nyissa meg a Terminalt!
  * Telepítse a [Homebrew-et](https://brew.sh/)!
  
    ``` /usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)" ```
  
  * Telepítse a Spatialite-t és Spatialite GUI-t!
  
   ```
       brew install spatialite-tools
       brew install gdal
       brew install spatialite-gui
   ```
   * A fenti útmutató Ryan Peeknek köszönhető, további információkért tekintse meg a [megjegyzéseit](https://github.com/ryanpeek/spatiallite_demo)!*

## Linux

 * A csomagkezelőben keresse meg a spatialite-gui csomagot!
 * A forráskódból történő telepítéshez töltse le [innen](https://www.gaia-gis.it/fossil/spatialite_gui/index)!


**Visszaléphet a [tanfolyamhoz](README.md)**
