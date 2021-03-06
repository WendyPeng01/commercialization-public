---
title: Present Validation 2 - GammaPresent
description: Present Validation 2 - GammaPresent
MSHAttr:
- 'PreferredSiteName:MSDN'
- 'PreferredLib:/library/windows/hardware'
ms.assetid: 149aad34-08f4-42a5-9a23-8de170fa9395
---

# <span id="p_hlk_test.d0deb0b6-c8e7-4328-ad3b-e56daa2aaf9b"></span>Present Validation 2 - GammaPresent


This automated test validates the **Present()** method.

Specifically, this test performs the following tasks:

-   Shrinks or stretches the height

-   Shrinks or stretches the width

-   Clips to the source area

-   Clips to the destination area

-   Overrides the destination window

The test performs these tasks individually and in combination. It then verifies the resulting output for correctness.

This topic applies to the following test jobs:

-   Present Validation 2

-   Present Validation 2 (WoW64)

-   Present Validation 2 - ColorConverting

-   Present Validation 2 - ColorConverting (WoW64)

-   Present Validation 2 - GammaPresent

-   Present Validation 2 - GammaPresent (WoW64)

-   Present Validation 2 - Present

-   Present Validation 2 - Present (WoW64)

## <span id="Test_details"></span><span id="test_details"></span><span id="TEST_DETAILS"></span>Test details


<table>
<colgroup>
<col width="50%" />
<col width="50%" />
</colgroup>
<tbody>
<tr class="odd">
<td><strong>Specifications</strong></td>
<td><ul>
<li>Device.Graphics.AdapterRender.MinimumDirectXLevel</li>
</ul></td>
</tr>
<tr class="even">
<td><strong>Platforms</strong></td>
<td><ul>
<li>Windows 10 for desktop editions (Home, Pro, Enterprise, and Education) x86</li>
<li>Windows 10 for desktop editions x64</li>
<li>Windows Server 2016 x64</li>
</ul></td>
</tr>
<tr class="odd">
<td><strong>Supported Releases</strong></td>
<td><ul>
<li>Windows 10</li>
<li>Windows 10, version 1511</li>
<li>Windows 10, version 1607</li>
<li>Windows 10, version 1703</li>
</ul></td>
</tr>
<tr class="even">
<td><strong>Expected run time (in minutes)</strong></td>
<td>11</td>
</tr>
<tr class="odd">
<td><strong>Category</strong></td>
<td>Compatibility</td>
</tr>
<tr class="even">
<td><strong>Timeout (in minutes)</strong></td>
<td>660</td>
</tr>
<tr class="odd">
<td><strong>Requires reboot</strong></td>
<td>false</td>
</tr>
<tr class="even">
<td><strong>Requires special configuration</strong></td>
<td>false</td>
</tr>
<tr class="odd">
<td><strong>Type</strong></td>
<td>automatic</td>
</tr>
</tbody>
</table>

 

## <span id="Additional_documentation"></span><span id="additional_documentation"></span><span id="ADDITIONAL_DOCUMENTATION"></span>Additional documentation


Tests in this feature area might have additional documentation, including prerequisites, setup, and troubleshooting information, that can be found in the following topic(s):

-   [Device.Graphics additional documentation](device-graphics-additional-documentation.md)

## <span id="Running_the_test"></span><span id="running_the_test"></span><span id="RUNNING_THE_TEST"></span>Running the test


Before you run the test, complete the test setup as described in the test requirements: [Graphic Adapter or Chipset Testing Prerequisites](graphic-adapter-or-chipset-testing-prerequisites.md).

## <span id="Troubleshooting"></span><span id="troubleshooting"></span><span id="TROUBLESHOOTING"></span>Troubleshooting


For generic troubleshooting of HLK test failures, see [Troubleshooting Windows HLK Test Failures](..\user\troubleshooting-windows-hlk-test-failures.md).

For troubleshooting information, see [Troubleshooting Device.Graphics Testing](troubleshooting-devicegraphics-testing.md).

## <span id="More_information"></span><span id="more_information"></span><span id="MORE_INFORMATION"></span>More information


This test is similar to the standard Present Validation test. The difference is that Present Validation 2 resets the device into a state where the back buffer and the front buffer have different formats (if the call to the **CheckDeviceFormatConversion** method was successful). The test performs a comparison with a reference image that the Microsoft® Direct3D® API generates. The driver must perform the color conversion between those two formats. (Direct3D does not perform any software emulation.)

This test uses the **IDirect3DSwapChain9::Present** method with the **D3DPRESENT\_LINEAR\_CONTENT** option.

If the driver supports gamma presentation (that is, the driver exposes the D3DCAPS3 capabilities option **D3DCAPS3\_LINEAR\_TO\_SRGB\_PRESENTATION**), a gamma-corrected presentation should occur. This test verifies the correct output by post-processing the reference image via gamma 2.2 correction. If the driver claims that gamma presentation is not supported, the test verifies that no gamma correction occurs.

The test performs the following tasks:

-   Scales the color channels to `[0..1)`

-   Calculates `Channel = pow( Channel, 1 / Gamma )`

-   Scales the result back to `int [0..256)`

>[!WARNING]
>  
Gamma presentation is supported only on a desktop that has a 32-bit color depth.

 

### <span id="Command_syntax"></span><span id="command_syntax"></span><span id="COMMAND_SYNTAX"></span>Command syntax

<table>
<colgroup>
<col width="50%" />
<col width="50%" />
</colgroup>
<thead>
<tr class="header">
<th>Command option</th>
<th>Description</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td><p><strong>Present2.exe -M:1 -dx9 -whql -logclean</strong></p></td>
<td><p>Runs the Present Validation 2 test job.</p></td>
</tr>
<tr class="even">
<td><p><strong>Present2.exe -M:1 -whql -logclean</strong></p></td>
<td><p>Runs the Present Validation 2 (WoW64) test job.</p></td>
</tr>
<tr class="odd">
<td><p><strong>Present2.exe -ColorConverting -src:ref -M:1 -whql -logclean</strong></p></td>
<td><p>Runs both the Present Validation 2 - ColorConverting test job and the Present Validation 2 - ColorConverting (WoW64) test job.</p></td>
</tr>
<tr class="even">
<td><p><strong>Present2.exe -GammaPresent -src:ref -M:1 -whql -logclean</strong></p></td>
<td><p>Runs both the Present Validation 2 - GammaPresent test job and the Present Validation 2 - GammaPresent (WoW64) test job.</p></td>
</tr>
<tr class="odd">
<td><p><strong>Present2.exe -Present -src:ref -M:1 -whql -logclean</strong></p></td>
<td><p>Runs both the Present Validation 2 - Present test job and the Present Validation 2 - Present (WoW64) test job.</p></td>
</tr>
</tbody>
</table>

>[!NOTE]
>  
For command-line help for this test binary, type **/?**.

 

### <span id="File_list"></span><span id="file_list"></span><span id="FILE_LIST"></span>File list

<table>
<colgroup>
<col width="50%" />
<col width="50%" />
</colgroup>
<thead>
<tr class="header">
<th>File</th>
<th>Location</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td><p>Configdisplay.exe</p></td>
<td><p><em>&lt;[testbinroot]&gt;</em>\nttest\windowstest\tools\</p></td>
</tr>
<tr class="even">
<td><p>D3d10ref.dll</p></td>
<td><p><em>&lt;[testbinroot]&gt;</em>\nttest\graphics\d3d\support\</p></td>
</tr>
<tr class="odd">
<td><p>D3d11ref.dll</p></td>
<td><p><em>&lt;[testbinroot]&gt;</em>\nttest\windowstest\graphics\d3d\support\</p></td>
</tr>
<tr class="even">
<td><p>D3dcompiler_test.dll</p></td>
<td><p><em>&lt;[testbinroot]&gt;</em>\nttest\windowstest\graphics\d3d\support\</p></td>
</tr>
<tr class="odd">
<td><p>D3dref9.dll</p></td>
<td><p><em>&lt;[testbinroot]&gt;</em>\nttest\windowstest\graphics\d3d\support</p></td>
</tr>
<tr class="even">
<td><p>D3dref8.dll</p></td>
<td><p><em>&lt;[testbinroot]&gt;</em>\nttest\windowstest\graphics\d3d\support\</p></td>
</tr>
<tr class="odd">
<td><p>D3dx10_test.dll</p></td>
<td><p><em>&lt;[testbinroot]&gt;</em>\nttest\windowstest\graphics\d3d\support\</p></td>
</tr>
<tr class="even">
<td><p>D3dx11_TEST.dll</p></td>
<td><p><em>&lt;[testbinroot]&gt;</em>\nttest\windowstest\graphics\d3d\support\</p></td>
</tr>
<tr class="odd">
<td><p>D3dx9_TEST.dll</p></td>
<td><p><em>&lt;[testbinroot]&gt;</em>\nttest\windowstest\graphics\d3d\support\</p></td>
</tr>
<tr class="even">
<td><p>D3dx8d.dll</p></td>
<td><p><em>&lt;[testbinroot]&gt;</em>\nttest\windowstest\graphics\d3d\support\</p></td>
</tr>
<tr class="odd">
<td><p>Fpstate.dll</p></td>
<td><p><em>&lt;[testbinroot]&gt;</em>\nttest\windowstest\graphics\d3d\utility\</p></td>
</tr>
<tr class="even">
<td><p>Modechange.exe</p></td>
<td><p><em>&lt;[testbinroot]&gt;</em>\nttest\windowstest\graphics\d3d\utility\</p></td>
</tr>
<tr class="odd">
<td><p>TDRWatch.exe</p></td>
<td><p><em>&lt;[testbinroot]&gt;</em>\nttest\windowstest\graphics\</p></td>
</tr>
<tr class="even">
<td><p>Vbswap.x</p></td>
<td><p><em>&lt;[testbinroot]&gt;</em>\nttest\windowstest\graphics\d3d\conf\</p></td>
</tr>
</tbody>
</table>

 

### <span id="Parameters"></span><span id="parameters"></span><span id="PARAMETERS"></span>Parameters

| Parameter name               | Parameter description                                 |
|------------------------------|-------------------------------------------------------|
| **MODIFIEDCMDLINE**          | Additional command line arguments for test executable |
| **LLU\_NetAccessOnly**       | LLU Name of net user                                  |
| **MONITOR**                  | Display device to test                                |
| **ConfigDisplayCommandLine** | Custom Command Line for ConfigDisplay. Default: logo  |
| **TDRArgs**                  | /get or /set                                          |

 

 

 






