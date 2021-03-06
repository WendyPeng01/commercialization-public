---
author: kpacquer
Description: Create Windows Universal OEM Packages
MSHAttr: 'PreferredLib:/library'
title: Create Windows Universal OEM Packages
ms.author: kenpacq
ms.date: 10/04/2017
ms.topic: article
ms.prod: windows-hardware
ms.technology: windows-oem
---

# Create Windows Universal OEM Packages

The Windows Universal OEM packaging standard is supported on Windows IoT Core, version 1709.

This new packaging schema is built to be compatible with more types of devices in the future. If you've built packages for IoT Core devices using the legacy packaging standard (pkg.xml), and you'd like to use them on IoT devices, you can [convert them to the new packaging standard](#convert_packages).

## Packages
Packages are the logical building blocks used to create IoT Core images.

*  **Everything you add is packaged**. Every driver, library, registry setting, system file, and customization that you add to the device is included in a package. The contents and location of each item are listed in a package definition file (*.wm.xml). 
*  **Packages can be updated by trusted partners**. Every package on your device is signed by you or a trusted partner. This allows OEMs, ODMs, developers, and Microsoft work together to help deliver security and feature updates to your devices without stomping on each other's work. 
*  **Packages are versioned**. This helps make updates easier and makes system restores more reliable.

![A sample package file (sample_pkg.cab) includes a package definition file, package contents like apps, drivers, and files, plus a signature file and a version number](images/oempackages.png)

Packages fall into three main categories:
* **OS kit packages** contain the core Windows operating system
* **SoC vendor prebuilt packages** contain drivers and firmware that support the chipset
* **OEM packages** contain device-specific drivers and customizations

[Learn about how to combine these packages into images for devices.](iot-core-manufacturing-guide.md)

## Start by creating a new empty package

1.  Install Windows ADK for Windows 10, version 1709, as well as the other tools and test certificates described in [Get the tools needed to customize Windows IoT Core](set-up-your-pc-to-customize-iot-core.md) and [Lab 1a: Create a basic image](create-a-basic-image.md).

2.  Use a text editor to create a new package definition file (also called a Windows Manifest file) based on the following template. Save the file using the ***.wm.xml** extension. 

    ```xml
    <?xml version='1.0' encoding='utf-8' standalone='yes'?>
    <identity
      xmlns="urn:Microsoft.CompPlat/ManifestSchema.v1.00"
      xmlns:xsd="http://www.w3.org/2001/XMLSchema"
      xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
      name="MediaService"
      namespace="Media"
      owner="OEM"
      >
    </identity>
    ```

3.  Create the empty package file (*.cab). The filename created is based on the owner, namespace, and name from the file.

    ```
    c:\oemsample>pkggen myPackage.wm.xml /universalbsp

    Directory of c:\oemsample
    
    04/03/2017  05:56 PM    <DIR>          .
    04/03/2017  05:56 PM    <DIR>          ..
    04/03/2017  05:43 PM               333 myPackage.wm.xml
    04/03/2017  05:56 PM             8,239 OEM-Media-MediaService.cab
    ```

## Add content to a package

The contents of a package are organized as a list of XML elements in the package definition file. 

The following example demonstrates how to add some files and registry settings to a package. This example defines a variable (_RELEASEDIR) that can be updated each time you generate the package.

```xml
<?xml version='1.0' encoding='utf-8' standalone='yes'?>
<identity
    xmlns="urn:Microsoft.CompPlat/ManifestSchema.v1.00"
    xmlns:xsd="http://www.w3.org/2001/XMLSchema"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    name="MediaService"
    namespace="Media"
    owner="OEM"    
    >
  <files>
    <file source="$(_RELEASEDIR)\MediaService.dll"/>
  </files>
  <regKeys>
    <regKey keyName="$(hklm.software)\OEMName\MediaService">
      <regValue
          name="StringValue"
          type="REG_SZ"
          value="MediaService"
          />
      <regValue
          name="DWordValue"
          type="REG_DWORD"
          value="0x00000020"
          />
    </regKey>
  </regKeys>
</identity>
```

```text
c:\oemsample>pkggen myPackage.wm.xml /universalbsp /variables:"_RELEASEDIR=c:\release"
```

## Run the pkggen.exe tool

PkgGen.exe [project] /universalbsp ...

```
  [project]··········· Full path to input file : .wm.xml, .pkg.xml, .man
                       Values:<Free Text> Default=NULL

  [universalbsp]······ Convert wm.xml BSP package to cab
                       Values:<true | false> Default=False

  [variables]········· Additional variables used in the project file,syntax:<name>=<value>;<name>=<value>;....
                       Values:<Free Text> Default=NULL

  [cpu]··············· CPU type. Values: (x86|arm|arm64|amd64)
                       Values:<Free Text> Default="arm"

  [languages]········· Supported language identifier list, separated by ';'
                       Values:<Free Text> Default=NULL

  [version]··········· Version string in the form of <major>.<minor>.<qfe>.<build>
                       Values:<Free Text> Default="1.0.0.0"

  [output]············ Output directory for the CAB(s).
                       Values:<Free Text> Default="CurrentDir"
```

## View the contents of a package

Packages use Windows cabinet file technology to store a set of files. Double-click the CAB file to view and extract its contents. Use notepad.exe to view update.mum, this describes how the files are installed onto the device.

Sample contents of **OEM-Media-MediaService.cab**:
```
arm_dual_media.inf_31bf3856ad364e35_1.0.0.0_none_75dfa724a55b489d
arm_dual_media.inf_31bf3856ad364e35_1.0.0.0_none_75dfa724a55b489d.manifest
arm_dual_sample.inf_31bf3856ad364e35_1.0.0.0_none_a7012ffdafd1caf1
arm_oem-featurearea-feature_31bf3856ad364e35_1.0.0.0_none_9e7ea37811ad5588
arm_oem-media-mediaserv..ployment-deployment_31bf3856ad364e35_1.0.0.0_none_6127250c45e77506.manifest
arm_oem-media-mediaservice_31bf3856ad364e35_1.0.0.0_none_7307c54952eeb87a
arm_oem-media-mediaservice_31bf3856ad364e35_1.0.0.0_none_7307c54952eeb87a.manifest
update.cat
update.mum
```

**Sample contents of update.mum:**
```xml
<?xml version="1.0" encoding="utf-8" standalone="yes"?>
<assembly manifestVersion="1.0" xmlns="urn:schemas-microsoft-com:asm.v3">
  <assemblyIdentity name="OEM-Media-MediaService" version="1.0.0.0" processorArchitecture="arm" language="neutral" publicKeyToken="31bf3856ad364e35" buildType="release" versionScope="nonSxS" />
  <package identifier="OEM-Media-MediaService" releaseType="Feature Pack" binaryPartition="false" targetPartition="MainOS">
    <customInformation>
      <phoneInformation phoneRelease="Production" phoneOwner="OEM" phoneOwnerType="OEM" phoneComponent="OEM-Media-MediaService" phoneSubComponent="" phoneGroupingKey="" />
      <file name="update.mum" size="823" staged="600" compressed="600" sourcePackage="" cabpath="update.mum" />
      <file name="$(runtime.system32)\catroot\{F750E6C3-38EE-11D1-85E5-00C04FC295EE}\update.cat" size="910" staged="713" compressed="713" sourcePackage="" cabpath="update.cat" />
      <file name="arm_oem-media-mediaserv..ployment-deployment_31bf3856ad364e35_1.0.0.0_none_6127250c45e77506.manifest" size="689" staged="560" compressed="560" sourcePackage="" cabpath="arm_oem-media-mediaserv..ployment-deployment_31bf3856ad364e35_1.0.0.0_none_6127250c45e77506.manifest" />
      <file name="arm_oem-media-mediaservice_31bf3856ad364e35_1.0.0.0_none_7307c54952eeb87a.manifest" size="423" staged="423" compressed="423" sourcePackage="" cabpath="arm_oem-media-mediaservice_31bf3856ad364e35_1.0.0.0_none_7307c54952eeb87a.manifest" />
    </customInformation>
    <update name="OEM-Media-MediaService-Deployment-Deployment">
      <component>
        <assemblyIdentity name="OEM-Media-MediaService-Deployment-Deployment" version="1.0.0.0" processorArchitecture="arm" language="neutral" publicKeyToken="31bf3856ad364e35" buildType="release" versionScope="nonSxS" />
      </component>
    </update>
  </package>
</assembly>
```

## Add a driver component

In the package definition file, use the **driver** element to inject drivers. We recommend using relative paths, as it's typically the simplest way to describe the INF source path.

```xml
  <drivers>
    <driver>
      <inf source="$(_RELEASEDIR)\Media.inf"/>
    </driver>
  </drivers>
```

If the default file import path is not equal to the INF source path, you can use the defaultImportPath attribute. In the following example, the INF is in the current directory, but the files to be imported are relative to $(_RELEASEDIR).  

```xml
  <drivers>
    <driver defaultImportPath="$(_RELEASEDIR)">
      <inf source="Media.inf"/>
    </driver>
  </drivers>
```

If files to be imported are not relative to how they are defined in the INF, file overrides can be applied. This is not recommended, but is available for special cases.

```xml
<syntaxhighlight lang="xml">
  <drivers>
    <driver>
      <inf source="Media.inf"/>
      <files>
         <file name="mdr.sys" source="$(_RELEASEDIR)\path1\mdr.sys" />
         <file name="mdr.dll" source="$(_RELEASEDIR)\path2\mdr.dll" />
      </files>
    </driver>
  </drivers>
</syntaxhighlight>
```

## Add a service component
In the package definition file, use the **service** element (and its child elements and attributes) to define and package a system service.

```xml
   <service
      dependOnService="AudioSrv;AccountProvSvc"
      description="@%SystemRoot%\system32\MediaService.dll,-201"
      displayName="@%SystemRoot%\system32\MediaService.dll,-200"
      errorControl="normal"
      imagePath="%SystemRoot%\system32\svchost.exe -k netsvcs"
      name="MediaService"
      objectName="LocalSystem"
      requiredPrivileges="SeChangeNotifyPrivilege,SeCreateGlobalPrivilege"
      sidType="unrestricted"
      start="delayedAuto"
      startAfterInstall="none"
      type="win32UserShareProcess"
      >
```

## Build and Filter WOW Packages
To build Guest or WOW packages (32 bit packages to run on 64 bit devices) add the buildWow="true" attribute to myPackage.wm.wml

```xml
<identity
    xmlns="urn:Microsoft.CompPlat/ManifestSchema.v1.00"
    xmlns:xsd="http://www.w3.org/2001/XMLSchema"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    name="MediaService"
    namespace="Media"
    owner="OEM"
    buildWow="true"
    >
```

Running PkgGen.exe with now generate one WOW package for each host package.

```text
04/05/2017  07:59 AM            11,870 OEM-Media-MediaService.cab
04/05/2017  07:59 AM             8,750 OEM-Media-MediaService.Resources_Lang_en-us.cab
04/05/2017  08:00 AM             8,810 OEM-Media-MediaService.Resources_Lang_en-us_Wow_arm64.arm.cab
04/05/2017  08:00 AM             8,754 OEM-Media-MediaService.Resources_Lang_fr-fr.cab
04/05/2017  08:00 AM             8,812 OEM-Media-MediaService.Resources_Lang_fr-fr_Wow_arm64.arm.cab
04/05/2017  07:59 AM            10,021 OEM-Media-MediaService_Wow_arm64.arm.cab
```

Typically, the 64 bit device will get its Host 64 bit package and its Guest 32 bit or WOW package, both generated from myPackage.wm.xml.  To avoid resource conflicts between the two packages build filters are used:

```xml
<syntaxhighlight lang="xml">
  <regKeys buildFilter="not build.isWow and build.arch = arm" >
    <regKey keyName="$(hklm.software)\OEMName\MediaService">
      <regValue
          name="StringValue"
          type="REG_SZ"
          value="MediaService"
          />
    </regKey>
</syntaxhighlight>
```

In this case, the registry keys are exclusive to the Host 32 bit ARM package.  The CPU switch is used to set build.arch, and build.isWow is set by PkgGen to false when building the 32 bit Host Package, then true when building the 32 bit Guest or WOW package.

```text
[cpu]··············· CPU type. Values: (x86|arm|arm64|amd64)
                     Values:<Free Text> Default="arm"
```

## <a href="" id="convert_packages"></a> Converting Windows Universal OEM Packages

If you've created packages using the pkg.xml packaging model, and you want to use them on Windows IoT Core, version 1709, you'll need to either recreate your packages, or convert them using the pkggen.exe tool. 

After you convert the packages, you may need to modify the wm.xml file to make sure that it follows the [schema](package-schema.md).

```text
c:\oemsample>pkggen.exe "filename.pkg.xml" /convert:pkg2wm

