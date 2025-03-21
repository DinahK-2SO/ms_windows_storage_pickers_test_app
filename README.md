# What to do this this page?
* verify if the expected behaviors for UWP pickers are correct.
* verify if the behavior of new pickers matches with UWP pickers.
* check if there're undefined behaviors


# The Constructors

### Common Points of UWP and New Pickers
0. Implemented In FileOpenPicker, FileSavePicker, FolderPicker

### UWP
1. requires a initialized window handle
    
    The existing pickers are constructed and initialized below:
    ```csharp
    using Windows.Storage.Pickers;

    var hwnd = WinRT.Interop.WindowNative.GetWindowHandle(this);
    var picker = new FileOpenPicker();
    WinRT.Interop.InitializeWithWindow.Initialize(picker, hwnd);
    var file = await picker.PickerSingleFileAsync();
    string path = file.Path;
    ```
1. Doesn't work when running as admin
1. Doesn't work when running in container

### New Pickers
1. requires a window id

    The new pickers has streamlined code for construction and initialization.
    ```csharp
    using Microsoft.Windows.Storage.Pickers;

    var picker = new FileOpenPicker(this.AppWindow.Id);
    var result = await picker.PickerSingleFileAsync();
    string path = result.Path;
    ```
1. works when running as admin
1. works when running in win32-isolation ??? all files?

# The Properties

## CommitButtonText
### Common Points of UWP and New Pickers
0. Implemented in FileOpenPicker, FileSavePicker, FolderPicker
1. CommitButtonText decides the text displayed on the commit button.

## SuggestedStartLocation
### Common Points of UWP and New Pickers
0. Implemented in FileOpenPicker, FileSavePicker, FolderPicker
1. SuggestedStartLocation specify the initial location
    
    (the folder opened first time when this app opens a folder).

1. if set to PickerLocationId.Unspecified, the picker falls back to the system default.
1. If not specified, using PickerLocationId.Unspecified by default.
1. if the specified location doesn't exist on end user's machine, falls back to the document library
 
    unless the document library doesn't exist neither, falls back to the system default.
1. This attribute only makes effects when there's no historical location.
    
    Once a file's picked from a certain folder, the picker opens this specific folder next time.
1. To clear this "memory", uninstall and reinstall the app.
1. 2 buttons in one same process (one same app) shares this memory

    This means: if one button picked a file from folder C:\Temp, then launch an open folder from another button, it opens the folder C:\Temp

## SettingsIdentifier
0. Implemented in FileOpenPicker, FileSavePicker, FolderPicker
1. It creates an individual settings for the picker

    Once running with its own settings identifier, the picker object won't be impacted by the "memory" of other picker objects.
1. the SettingsIdentifier can be shared among pickers

## FileTypeFilter
### Common Points of UWP and New Pickers
0. Implemented in FileOpenPicker and FolderPicker.
FileSavePicker doesn't have this attribute.

### UWP
1. when not specified, throw compile error
1. accept * as filter type, doesn't accept other wildcards

### New Pickers
1. when not specified, default to be "*"
1. accept wildcard configuration

    <!-- @Xiang Hong, it might be long ago. Have we got approval for this change? -->


## ViewMode
### Common Behaviors of UWP and New Pickers
0. Implemented In FileOpenPicker, FolderPicker
    
    FileSavePicker doesn't have this property.

1. Display the files in dialog in List/Thumbnail mode based on ViewMode config
    
    If set asList, should display the files in list mode
    
    If set asThumbnail, should display the files in mode.

1. If not specified, ???

## SuggestedFileName
### Common Behaviors of UWP and New Pickers
0. Implemented In FileSavePicker
1. Decides the file name auto-filled when the file dialog opens
1. When not specified, no file name auto filled

## DefaultFileExtension
### Common Behaviors of UWP and New Pickers
0. Implemented In FileSavePicker
1. Decides the extension auto-filled (after the suggested file name) when the file dialog opens
1. When not specified, no file extension auto filled

## SuggestedSaveFile
0. Implemented In FileSavePicker
<!-- The expected behavior of this property is to be decided. -->

## FileTypeChoices
### Common Behaviors of UWP and New Pickers
0. Implemented In FileSavePicker
1. The acceptable file extensions when user picking or creating the file to save
1. Force file extension to be one of the FileTypeChoices

    When the file extension defined by end user is not in the FileTypeChoices, automatically attach the first extension in the current choice group.

    This behavior is verified in UWP pickers.

    bug - the new pickers didn't implement this behavior

    https://microsoft.visualstudio.com/OS/_workitems/edit/56776751: FileSavePicker - When the file extension defined by end user is not in the FileTypeChoices, automatically attach the first extension in the current choice group.

1. if the file doesn't exist  ???


# FileOpenPicker
## PickSingleFileAsync

### UWP
1. returns a StorageFile

    The Windows.Storage.StorageFile

### New Pickers
1. returns a PickFileResult

    The Microsoft.Windows.Storage.PickFileResult

## PickMultipleFilesAsync
### UWP
1. returns a readonly vector of StorageFile
### New Pickers
1. returns a readonly vector of PickFileResult

# FileSavePicker
## PickSaveFileAsync
### Common Points of UWP and New Pickers
1. If the file doesn't exist, create the file.

    If the file doesn't exist (user defined filename cannot find a file), create the file before returning result.

### UWP
1. returns a StorageFile

    The Windows.Storage.StorageFile

### New Pickers
1. returns a PickFileResult

    The Microsoft.Windows.Storage.PickFileResult

# FolderPicker
## PickSingleFolderAsync

### UWP
1. returns a StorageFolder

    The Windows.Storage.StorageFolder

### New Pickers
1. returns a PickFolderResult

    The Microsoft.Windows.Storage.PickFolderResult
