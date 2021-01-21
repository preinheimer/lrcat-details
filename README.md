# lrcat-details
Detailed information, and maybe eventually scripts to work with the Lightroom Catalog file

## .lrcat Information

The file is a [SQLite](https://sqlite.org/index.html) database, and can be opened without modification by any SQLite tool. The command line tools work well, [DB Browser for SQLite](https://sqlitebrowser.org/) also works well. 

The file itself can be quite large, mine (amateur photographer) is ~189MB. 

The database contains numerous tables. This document shall attempt to indicate their purpose, and other key information.

## Warning

Messing up your lightroom catalog is a fantastic way to start from scratch. Back it up multiple places, never edit the file while Lightroom is open. 


### Quick look at the database

After installing SQLite, you can open the file with the `.open` command, giving it the filename of your Lightroom Catalog, for me that's `.open "Lightroom Catalog.lrcat"`. You need to quote the filename as there's a space in it. 

Pauls-MBP-1675:Lightroom paulreinheimer$ /usr/local/opt/sqlite/bin/sqlite3
SQLite version 3.34.0 2020-12-01 16:14:00
Enter ".help" for usage hints.
Connected to a transient in-memory database.
Use ".open FILENAME" to reopen on a persistent database.
sqlite> .open "Lightroom Catalog.lrcat"

SQLite can give you a list of tables using the `.tables` command. 


### Tables 

There are 111 tables in the database as of Lightroom Classic 8.4.1 They are:



## AgLibraryRootFolder

This table stores details about the various "root" folders for your Lightroom catalog. All folders within AgLibraryFolder will identify one of these as being a root folder, and store their path relative to this root. 

```
CREATE TABLE AgLibraryRootFolder (
    id_local INTEGER PRIMARY KEY,
    id_global UNIQUE NOT NULL,
    absolutePath UNIQUE NOT NULL DEFAULT '',
    name NOT NULL DEFAULT '',
    relativePathFromCatalog
)

```

## AgLibraryFolder

This table identifies every folder that contains images in your Lightroom catalog. The `rootFolder` item refers to the `id_local` from the AgLibraryRootFolder table. `pathFromRoot` shows the path from the root folder to the image. 

**id_local**|**id_global**|**pathFromRoot**|**rootFolder**
-----|-----|-----|-----
32458|0A66049E-85BB-4E22-B037-97B98901558A|2018/2018-12-25/|446919
612894|0C96891D-AC5D-480C-AB20-07C909CEB5BB|2020/2020-02-21/|56756
4746|0CBC88E1-41C2-41B7-A64D-876AE5EC8EAF|2018/2018-08-25/|446919


**id_local**|**id_global**|**absolutePath**|**Name**|**relativePathFromCatalog**
-----|-----|-----|-----|-----
56756|2E7D798A-1F70-4C42-883C-86D77E1CF35C|/Users/paulreinheimer/Pictures/|Pictures|../
97393|2DAC0F1E-0095-44B1-A0EA-E8F01F284511|/Users/paulreinheimer/Dropbox/Camera Uploads/|Camera Uploads|../../Dropbox/Camera Uploads/
446919|E0E0F8AC-5B43-40DD-9D56-4E2219A9A3E3|/Users/paulreinheimer/Desktop/|Desktop|../../Desktop/
520597|713C3600-8034-43A1-83B3-B59EC801E596|/Users/paulreinheimer/Downloads/|Downloads|../../Downloads/

```
CREATE TABLE AgLibraryFolder (
    id_local INTEGER PRIMARY KEY,
    id_global UNIQUE NOT NULL,
    pathFromRoot NOT NULL DEFAULT '',
    rootFolder INTEGER NOT NULL DEFAULT 0
)
```

