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

 - Adobe_AdditionalMetadata
 - Adobe_faceProperties
 - Adobe_imageDevelopBeforeSettings
 - Adobe_imageDevelopSettings
 - Adobe_imageProofSettings
 - Adobe_imageProperties
 - Adobe_images
 - Adobe_libraryImageDevelop3DLUTColorTable
 - Adobe_libraryImageDevelopHistoryStep
 - Adobe_libraryImageDevelopSnapshot
 - Adobe_libraryImageFaceProcessHistory
 - Adobe_namedIdentityPlate
 - Adobe_variables
 - Adobe_variablesTable
 - AgDNGProxyInfo
 - AgDNGProxyInfoUpdater
 - AgDeletedOzAlbumAssetIds
 - AgDeletedOzAlbumIds
 - AgDeletedOzAssetIds
 - AgDeletedOzSpaceIds
 - AgFolderContent
 - AgHarvestedDNGMetadata
 - AgHarvestedExifMetadata
 - AgHarvestedIptcMetadata
 - AgHarvestedMetadataWorklist
 - AgInternedExifCameraModel
 - AgInternedExifCameraSN
 - AgInternedExifLens
 - AgInternedIptcCity
 - AgInternedIptcCountry
 - AgInternedIptcCreator
 - AgInternedIptcIsoCountryCode
 - AgInternedIptcJobIdentifier
 - AgInternedIptcLocation
 - AgInternedIptcState
 - AgLastCatalogExport
 - AgLibraryCollection
 - AgLibraryCollectionChangeCounter
 - AgLibraryCollectionContent
 - AgLibraryCollectionCoverImage
 - AgLibraryCollectionImage
 - AgLibraryCollectionImageChangeCounter
 - AgLibraryCollectionImageOzAlbumAssetIds
 - AgLibraryCollectionImageOzSortOrder
 - AgLibraryCollectionLabel
 - AgLibraryCollectionOzAlbumIds
 - AgLibraryCollectionStack
 - AgLibraryCollectionStackData
 - AgLibraryCollectionStackImage
 - AgLibraryCollectionSyncedAlbumData
 - AgLibraryCollectionTrackedAssets
 - AgLibraryFace
 - AgLibraryFaceCluster
 - AgLibraryFaceData
 - AgLibraryFile
 - AgLibraryFileAssetMetadata
 - AgLibraryFolder
 - AgLibraryFolderFavorite
 - AgLibraryFolderLabel
 - AgLibraryFolderStack
 - AgLibraryFolderStackData
 - AgLibraryFolderStackImage
 - AgLibraryIPTC
 - AgLibraryImageChangeCounter
 - AgLibraryImageOzAssetIds
 - AgLibraryImageSearchData
 - AgLibraryImageSyncedAssetData
 - AgLibraryImageXMPUpdater
 - AgLibraryImport
 - AgLibraryImportImage
 - AgLibraryKeyword
 - AgLibraryKeywordCooccurrence
 - AgLibraryKeywordFace
 - AgLibraryKeywordImage
 - AgLibraryKeywordPopularity
 - AgLibraryKeywordSynonym
 - AgLibraryOzCommentIds
 - AgLibraryOzFavoriteIds
 - AgLibraryOzFeedbackInfo
 - AgLibraryPublishedCollection
 - AgLibraryPublishedCollectionContent
 - AgLibraryPublishedCollectionImage
 - AgLibraryPublishedCollectionLabel
 - AgLibraryRootFolder
 - AgLibraryUpdatedImages
 - AgMRULists
 - AgMetadataSearchIndex
 - AgOutputImageAsset
 - AgOzSpaceAlbumIds
 - AgOzSpaceIds
 - AgPendingOzAlbumAssetIds
 - AgPendingOzAssetBinaryDownloads
 - AgPendingOzAssets
 - AgPhotoComment
 - AgPhotoProperty
 - AgPhotoPropertyArrayElement
 - AgPhotoPropertySpec
 - AgPublishListenerWorklist
 - AgRemotePhoto
 - AgSearchablePhotoProperty
 - AgSearchablePhotoPropertyArrayElement
 - AgSourceColorProfileConstants
 - AgSpecialSourceContent
 - AgTempImages
 - AgUnsupportedOzAssets
 - AgVideoInfo
 - LrMobileSyncChangeCounter
 - MigratedCollectionImages
 - MigratedCollections
 - MigratedImages
 - MigratedInfo
 - MigrationSchemaVersion


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

**95014	07FDAC5B-9E68-49FA-A001-96092AF60BCD	2019/2019-06-02/	446919**
-----
32458	0A66049E-85BB-4E22-B037-97B98901558A	2018/2018-12-25/	446919
612894	0C96891D-AC5D-480C-AB20-07C909CEB5BB	2020/2020-02-21/	56756
4746	0CBC88E1-41C2-41B7-A64D-876AE5EC8EAF	2018/2018-08-25/	446919

```
CREATE TABLE AgLibraryFolder (
    id_local INTEGER PRIMARY KEY,
    id_global UNIQUE NOT NULL,
    pathFromRoot NOT NULL DEFAULT '',
    rootFolder INTEGER NOT NULL DEFAULT 0
)
```

