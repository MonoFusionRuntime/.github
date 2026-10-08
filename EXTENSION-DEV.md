# MonoFusion Extensions API
This currently will only cover the json files, as the actual coding portion should be very straight forward, and it'd be a lot of work to write it out.

If you don't understand how to develop a custom extension for MonoFusion, take a look at the Template extension. If you are porting a DarkEdif-based extension, check out the DarkEdif_Template extension.

## JSON Format
Every extension requires a json for it to be detected and properly implemented by the exporter.

`extensionClassName`
-
This is the name of your class, as some extension names would be invalid class names, this allows for differing names between the class name and the extension file name.

*For example; 'INI++15.mfx' would normal look for 'CRunINI++15' in other runtimes, which is an invalid class name. In MonoFusion, you can specify `extensionClassName` as 'CRunIniPlusPlus' and have no issues, as long as the json is named INI++15.json*

`compatibility`
-
Compatibility defines which platforms your extension works on. Whether you don't feel like implementing the file system for Android, or if you just cannot make an extension work on a specific platform; you can specify this to ensure they will not be detected as valid extensions for non-listed platforms

*Valid platforms are:*
- Windows
- Mac
- Linux
- Android

`contentFiles`
-
This field allows you to specify files that you'd like to have placed inside the Content.mgcb aka the MonoGame Content Builder. Files listed here should be placed inside of 'Content/ExtensionName'

The format for entries in this array look like this
```json
"contentFiles": [
    {
        "name": "fileName.ext",
        "importer": "ImporterName",
        "processor": "ProcessorName",
        "processorParams": [
            {
                "key": "ParameterName",
                "value": "ParameterValue"
            }
        ]
    }
]
```
This example would output into the MGCB as such;
```mgcb
#------------------------------- ExtensionName -----------------------------#

#begin ExtensionName/fileName.ext
/importer:ImporterName
/processor:ProcessorName
/processorParam:ParameterName=ParameterValue
/build:ExtensionName/fileName.ext
```

*For more information about MonoGame Content Builder and it's format, check out [this article](https://docs.monogame.net/articles/getting_started/tools/mgcb.html) from the official MonoGame documentation*

`contentImporters`
-
Files included in the MGCB require an Importer and a Processor. MonoGame comes with some standard choices, and MonoFusion includes it's own BinaryImporter/BinaryProcessor, but you can also add your own. You may place these inside of 'Importers/ExtensionName'

The format for entries in this array are a simple array of strings, which look like this
```json
"contentImporters": [
    "fileName"
]
```
*The .dll file extension is assumed*

This example would output into the MGCB as such;
```mgcb
#-------------------------------- References --------------------------------#

/reference:Importers/ExtensionName/fileName.dll
```

*For more information about custom impoters and processors, check out [this article](https://badecho.com/index.php/2022/08/17/extending-pipeline/) by Matt Weber*

`nugetPackages`
-
C# allows for referencing packages from the NuGet Package Manager, and will automatically download and reference these for you. When developing an extension for MonoFusion, you may end up needing a NuGet Package to be referenced. To do this; you may use this field.

However, with multiple separate extensions having this ability, there may come conflictions. Say ExtensionA references version 1.1.0 of a package, and ExtensionB references version 1.1.2 of that same package. In this case, a warning will be shown to the user, intended for them to inform the developer of ExtensionA of the confliction so that they can update their package. The application will compile regardless, but the latest version between the two (or more) conflicts will be chosen. In this case 1.1.2, and any breaking changes between 1.1.0 and 1.1.2, if any, will cause the compilation to fail. However this entire situation is unlikely.

That being said, the format for entries in this array look like this
```json
"nugetPackages": [
    {
        "packageName": "NuGetPackageName",
        "packageVersion": "1.2.3-whatever"
    }
]
```
*The standard of NuGet versioning allows for version such as '1.0.0', '1.0.0.0', or '1.0.0-alpha.69' All of these will work fine, and will not effect the choosing of the latest version discussed above.*

`commonPackages`
-
There are many cases where multiple extensions would like to reference the same code, but having that shared code duplicated into both extensions would cause errors upon compilation. This parameter allows you to define code stored in the 'Shared' folder, which will only get copied into the project once if 1 or more extensions reference it.

Common Packages also can have optional json files, similar to extensions, however these json files can only utilize `contentFiles`, `contentImporters`, and `nugetPackages`, none of which are required.

The format for entries in this array are a simple array of strings, which look like this
```json
"commonPackages": [
    "CommonLib"
]
```
*Items defined in this parameter should be **folder names**, for example 'Shared/Code/CommonLib' where all the code you want copied would be inside of that folder, alongside an optional 'Shared/CommonLib.json'. Inside the solution this will become 'Runtime/Extensions/CommonLib' and your namespaces should match that, although not required.*

# MonoFusion Movement API
This currently will only cover the json files, as the actual coding portion should be very straight forward, and it'd be a lot of work to write it out.

If you don't understand how to develop a custom movement for MonoFusion, take a look at the pre-existing movements.

## JSON Format
Every movement requires a json for it to be detected and properly implemented by the exporter, although the json itself may be empty.

Movements currently have less functionality than extensions, this may or may not change in the future.

`movementClassName`

This is the name of your class, as some movement names would be invalid class names, or just plain ugly, this allows for differing names between the class name and the movement file name.

*For example; 'clickteam_dragdrop.mvx' would normal look for 'clickteam_dragdrop' in other runtimes, which is just ugly. In MonoFusion, you can specify `movementClassName` as 'CRunMvtDragDrop' and have no issues, as long as the json is named clickteam_dragdrop.json*