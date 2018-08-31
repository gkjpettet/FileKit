`FileSystem` is a Xojo module for robustly handling classic framework `FolderItem` copying, deletion and moving. I created it because the native `FolderItem` (and `Xojo.IO.FolderItem` for that matter) copy, move and delete methods are buggy. This is particularly true on Windows (especially Windows 10).  This module uses the `cp` and `mv` shell commands on Unix systems (macOS and Linux) and the `move` and `xcopy` commands on Windows.

The repo contains a Xojo project with a demonstration window containing the `FileSystem` module.

## Usage
Firstly copy the `FileSystem` module in the downloaded project to your own project. The module supports Mac, Windows, Linux and ARM web, desktop and console builds. It does **not** work on iOS.

Before you use any of the module methods, you must call `FileSystem.Initialise`.

The module includes a boolean property (`safeMode`) which defaults to `True`. When `True`, the module will **not** delete a number of special folders. The list of the protected folders depends on the platform. See [Appendix 1](#app1) below for the list. If you set `FileSystem.safeMode = False` then the module will delete any folder or file on the drive (including the `root` folder) if requested. Be very careful when using this mode!	

### Copying a file or folder
To copy a file or folder you can use either the module method or the `FolderItem` extension:

```language-xojo
result As FileSystem.Error = FileSystem.CopyTo(source As FolderItem, destination As FolderItem, overwrite As Boolean)
result As FileSystem.Error = folderItemVariable.CopyTo(destination As FolderItem, overwrite As Boolean)
```

The method returns an error code in the form of a `FileSystem.Error` enumeration. The list of error codes can be found in the [errors section](#errors).

`source` may be either a file or a folder. `destination` must be a folder and must exist. `destination` refers to the folder that you want the copied file or folder to end up as a child of. 

The `overwrite` parameter specifies what happens in the following scenarios:

1. If `source` is a file and an identically named file exists as a child of the `destination` folder then the existing file in `destination` is deleted first and then the `source` file is copied to `destination` if `overwrite` is `True`. If `overwrite` is `False` then the copy is aborted.
2. If `source` is a folder and an identically named folder exists as a child of the `destination` folder then the existing folder in `destination` is deleted first and the `source` folder (and its contents) are recursively copied into `destination` if `overwrite` is `True`. If `overwrite` is `False` then the copy is aborted.

### Moving a file or folder
To move a file or folder you can use either the module method or the `FolderItem` extension:

```language-xojo
result As FileSystem.Error = FileSystem.MoveTo(source As FolderItem, destination As FolderItem, overwrite As Boolean)
result As FileSystem.Error = folderItemVariable.MoveTo(destination As FolderItem, overwrite As Boolean)
```

The method returns an error code in the form of a `FileSystem.Error` enumeration. The list of error codes can be found in the [errors section](#errors).

`source` may be either a file or a folder. `destination` must be a folder and must exist. `destination` refers to the folder that you want to `source` to be moved **into**.

The `overwrite` parameter specifies what happens in the following scenarios:

1. If `source` is a file and an identically named file exists as a child of the `destination` folder then the existing file in `destination` is deleted first and then the `source` file is moved to `destination` if `overwrite` is `True`. If `overwrite` is `False` then the move is aborted.
2. If `source` is a folder and an identically named folder exists as a child of the `destination` folder then the existing folder in `destination` is deleted first and the `source` folder (and its contents) are moved into `destination` if `overwrite` is `True`. If `overwrite` is `False` then the move is aborted.

### Deleting a file or folder
To delete a file or folder you can use either the module method or the `FolderItem` extension:

```language-xojo
result As Integer = FileSystem.ReallyDelete(what As FolderItem, safeMode As Boolean = True)
result As Integer = folderItemVariable.ReallyDelete(what As FolderItem, safeMode As Boolean = True)
``` 

The method returns the Xojo `FolderItem.LastErrorCode` for the deletion attempt. If everything goes smoothly then it will return `FolderItem.NoError`.

If the optional `safeMode` is `True` then the method will **not** delete a number of special folders. The list of the protected folders depends on the platform. See [Appendix 1](#app1) below for the list. Setting this method parameter has no effect on the `FileSystem.safeMode` property.

## <a id="errors">Errors</a>

## <a id="app1">Appendix 1</a>
Below a list of the system folders that will **never** be deleted by the `FileSystem` module if `FileSystem.safeMode = True`. 

### macOS

`ApplicationData`, `Applications`, `Bin`, `Desktop`, `Documents`, `Etc`, `Favourites`, `Fonts`, `Home`, `Library`, `Mount`, `Movies`, `Music`, `Pictures`, `Preferences`, `Printers`, `SBin`, `SharedApplicationData`, `SharedDocuments`, `SharedPreferences`, `System`, `UserBin`, `UserHome`, `UserLibrary`, `UsersBin`

### Windows

`ApplicationData`, `Applications`, `Desktop`, `Documents`, `Extensions`, `Favourites`, `Fonts`, `Movies`, `Music`, `Pictures`, `Printers`, `SharedApplicationData`, `SharedDocuments`, `System`, `UserHome`, `Windows`

### Linux

`ApplicationData`, `Bin`, `Desktop`, `Documents`, `Etc`, `Home`, `Library`, `Mount`, `Movies`, `Music`, `Pictures`, `SBin`, `UserBin`, `UserHome`, `UserLibrary`, `UsersBin`
