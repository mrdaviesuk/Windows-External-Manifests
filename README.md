# Windows-External-Manifests
External Manifest Files for Common Windows Applications which alter the Default User Account Control (UAC) Elevation Behavior 

The intention of using the external manifest is to allow an alternative UAC behaviour to be configured - for example preventing UAC from prompting for elevation when launching applications which have the 'highestAvailable', by replacing this with the 'asInvoker' parameter, allowing them to run without being elevated and without prompting for elevation. Where elevation is required, it is still possible to request elevation using the right-click 'Run as administrator' option provided by Windows. 

## Creating an External Application Manifest

Application Manifest files are an XML file, typically embedded into a PE binary which describe runtime SxS dependancies, as well as file metadata - including paramters which control the User Account Control (UAC) elevation behaviour of the application. 

Whilst internal manifest are the most common, external manifests which sit outside the binary but alongside it in the filesystem are also supported, but support is not enabled by default (see [Configuring Windows to Use External Application Manifests](#Configuring-Windows-to-Use-External-Application-Manifests) for details).  

External application manifests are just XML files named to match the target application (e.g., 'example.exe.manifest' where the target application is 'example.exe') 

I have created the examples contained with this repository by extracting the manifest files from applicatiosn running on a 64-bit Windows 10 system (22H2 - Build 19045.3208). 

They were extracted using a mixture of Resoruce Hacker and 7-Zip, where the latter provides by far the easiest option simply by opening the target file and browsing to the Manifest "subfolder" (e.g., C:\Windows\System32\Taskmgr.exe\.rsrc\MANIFEST) and extracting the '1' file contained within it. This can then be adjusted as required and renamed to match the target application. 

![Resource Hacker Reading Regedit.exe's Manifest](https://github.com/mrdaviesuk/Windows-External-Manifests/blob/769e5d8e8551a57ffb50e51f23f4fdf97ad12672/reshacker-regedit.jpg)

You can learn more about the fun and exiting world of manifest files, and specifically applciation manifest on [Microsoft's Learn website](https://learn.microsoft.com/en-us/windows/win32/sbscs/application-manifests) 

## Manifest UAC Setting Options

The choices for /MANIFESTUAC:level are as follows:

* level='asInvoker': The application runs at the same permission level as the process that started it. You can elevate the application to a higher permission level by selecting Run as Administrator.

* level='highestAvailable': The application runs at the highest permission level that it can. If the user who starts the application is a member of the Administrators group, this option is the same as level='requireAdministrator'. If the highest available permission level is higher than the level of the opening process, the system prompts for credentials.

* level='requireAdministrator': The application runs using administrator permissions. The user who starts the application must be a member of the Administrators group. If the opening process isn't running with administrative permissions, the system prompts for credentials.

## Configuring Windows to Use External Application Manifests

> Note: As ever, before making any changes to the registry it's good practive to take a backup first!  

1. Open 'regedit.exe' with elevated/admin rights
2. Navigate to the following subkey:
   * HKEY_LOCAL_MACHINE > SOFTWARE > Microsoft > Windows > CurrentVersion > SideBySide
3. Right-click, select New > DWORD (32 bit) Value 
4. Type 'PreferExternalManifest', and then press ENTER
5. Right-click the newly created 'PreferExternalManifest', and then click Modify
6. Enter Value Data '1' and ensure Decimal is selected
7. Click 'OK'

There are .reg files included the repository, to add and remove this entry. 

