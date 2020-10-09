---
title: Azure Kinect Firmware Tool
description: Seznamte se s postupy dotazování a aktualizace firmwaru zařízení pomocí nástroje pro firmware Azure Kinect.
author: tesych
ms.author: tesych
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: conceptual
keywords: Kinect, firmware, aktualizace
ms.openlocfilehash: f8b89751d27fb5a4b18d635f45f63f4f36bd05f6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "85277163"
---
# <a name="azure-kinect-dk-firmware-tool"></a>Nástroj pro firmware Azure Kinect DK

Nástroj pro firmware Azure Kinect se dá použít k dotazování a aktualizaci firmwaru zařízení ve službě Azure Kinect DK.

## <a name="list-connected-devices"></a>Vypsat připojená zařízení

Seznam připojených zařízení můžete získat pomocí možnosti-l.  `AzureKinectFirmwareTool.exe -l`

```console
 == Azure Kinect DK Firmware Tool ==
Found 2 connected devices:
0: Device "000036590812"
1: Device "000274185112"
```

## <a name="check-device-firmware-version"></a>Ověřit verzi firmwaru zařízení

Aktuální verzi firmwaru prvního připojeného zařízení můžete zjistit pomocí možnosti-q, například `AzureKinectFirmwareTool.exe -q` .

```console
 == Azure Kinect DK Firmware Tool ==
Device Serial Number: 000036590812
Current Firmware Versions:
    RGB camera firmware:      1.5.92
    Depth camera firmware:    1.5.66
    Depth config file:        6109.7
    Audio firmware:           1.5.14
    Build Config:             Production
    Certificate Type:         Microsoft
```

Pokud je k dispozici více než jedno zařízení, můžete určit, které zařízení chcete zadat dotaz, přidáním úplného sériového čísla do příkazu, například:

`AzureKinectFirmwareTool.exe -q 000036590812`

## <a name="update-device-firmware"></a>Aktualizace firmwaru zařízení

Nejběžnějším použitím tohoto nástroje je aktualizace firmwaru zařízení. Proveďte aktualizaci voláním nástroje pomocí `-u` Možnosti. Aktualizace firmwaru může trvat několik minut, v závislosti na tom, které soubory firmwaru se musí aktualizovat.

Podrobné pokyny k aktualizaci firmwaru najdete v článku [aktualizace firmwaru Azure Kinect](update-device-firmware.md).  

`AzureKinectFirmwareTool.exe -u firmware\AzureKinectDK_Fw_1.5.926614.bin`

Pokud je k dispozici více než jedno zařízení, můžete určit, které zařízení chcete zadat dotaz, přidáním úplného sériového čísla do příkazu.

`AzureKinectFirmwareTool.exe -u firmware\AzureKinectDK_Fw_1.5.926614.bin 000036590812`

## <a name="reset-device"></a>Resetování zařízení

Připojená možnost Azure Kinect DK se dá resetovat pomocí možnosti-r, pokud musíte zařízení dostat do známého stavu.

Pokud je k dispozici více než jedno zařízení, můžete určit, které zařízení chcete zadat dotaz, přidáním úplného sériového čísla do příkazu.

`AzureKinectFirmwareTool.exe -r 000036590812`

## <a name="inspect-firmware"></a>Zkontrolovat firmware

Kontrola firmwaru vám umožní získat informace o verzi ze souboru s firmwarem firmwaru před aktualizací skutečného zařízení.

`AzureKinectFirmwareTool.exe -i firmware\AzureKinectDK_Fw_1.5.926614.bin`

```console
 == Azure Kinect DK Firmware Tool ==
Loading firmware package ..\tools\updater\firmware\AzureKinectDK_Fw_1.5.926614.bin.
File size: 1228844 bytes
This package contains:
  RGB camera firmware:      1.5.92
  Depth camera firmware:    1.5.66
  Depth config files: 6109.7 5006.27
  Audio firmware:           1.5.14
  Build Config:             Production
  Certificate Type:         Microsoft
  Signature Type:           Microsoft
```

## <a name="firmware-update-tool-options"></a>Možnosti nástroje pro aktualizaci firmwaru

```console
 == Azure Kinect DK Firmware Tool ==
* Usage Info *
    AzureKinectFirmwareTool.exe <Command> <Arguments>

Commands:
    List Devices: -List, -l
    Query Device: -Query, -q
        Arguments: [Serial Number]
    Update Device: -Update, -u
        Arguments: <Firmware Package Path and FileName> [Serial Number]
    Reset Device: -Reset, -r
        Arguments: [Serial Number]
    Inspect Firmware: -Inspect, -i
        Arguments: <Firmware Package Path and FileName>

    If no Serial Number is provided, the tool will just connect to the first device.

Examples:
    AzureKinectFirmwareTool.exe -List
    AzureKinectFirmwareTool.exe -Update c:\data\firmware.bin 0123456
```

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
>[Podrobné pokyny k aktualizaci firmwaru zařízení](update-device-firmware.md)
