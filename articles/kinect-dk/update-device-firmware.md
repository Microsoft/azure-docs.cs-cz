---
title: Aktualizovat firmware Azure Kinect DK
description: Naučte se aktualizovat firmware zařízení Azure Kinect DK pomocí nástroje pro firmware Azure Kinect.
author: tesych
ms.author: tesych
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: conceptual
keywords: Kinect, firmware, aktualizace, obnovení
ms.openlocfilehash: 71557798a584635356cc47fd7c4a0309df4d018a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "94356219"
---
# <a name="update-azure-kinect-dk-firmware"></a>Aktualizovat firmware Azure Kinect DK

Tento dokument poskytuje pokyny k aktualizaci firmwaru zařízení ve službě Azure Kinect DK.

Azure Kinect DK neaktualizuje firmware automaticky. Pomocí [nástroje Azure Kinect firmware](azure-kinect-firmware-tool.md) můžete ručně aktualizovat firmware na nejnovější dostupnou verzi.

## <a name="prepare-for-firmware-update"></a>Příprava na aktualizaci firmwaru

1. [Stáhněte si sadu SDK](sensor-sdk-download.md).
2. Nainstalujte sadu SDK.
3. V umístění instalace sady SDK v části (umístění instalace sady SDK) \tools\ byste měli najít:

    - AzureKinectFirmwareTool.exe
    - Soubor firmware. bin ve složce firmwaru, například *AzureKinectDK_Fw_1.5.926614. bin*.

4. Připojte své zařízení k hostitelskému počítači a taky ho zapněte.

> [!IMPORTANT]
> Při aktualizaci firmwaru ponechte připojení USB a napájecího zdroje. Odebrání připojení během aktualizace může zablokovat firmware do poškozeného stavu.

## <a name="update-device-firmware"></a>Aktualizace firmwaru zařízení

1. Otevřete příkazový řádek v \tools\ složce (umístění instalace sady SDK).
2. Aktualizujte firmware pomocí nástroje Kinect firmware Azure.

    `AzureKinectFirmwareTool.exe -u <device_firmware_file.bin>`

    Příklad:

    `AzureKinectFirmwareTool.exe -u firmware\AzureKinectDK_Fw_1.5.926614.bin`

3. Počkejte, než se dokončí aktualizace firmwaru. Může to trvat několik minut v závislosti na velikosti obrázku.

### <a name="verify-device-firmware-version"></a>Ověřit verzi firmwaru zařízení

1. Ověřte, jestli je firmware aktualizovaný.

    `AzureKinectFirmwareTool.exe -q`

2. Podívejte se na následující příklad.

    ```console
       >AzureKinectFirmwareTool.exe -q
       == Azure Kinect DK Firmware Tool ==
        Device Serial Number: 000805192412
        Current Firmware Versions:
        RGB camera firmware:      1.6.102
        Depth camera firmware:    1.6.75
        Depth config file:        6109.7
        Audio firmware:           1.6.14
        Build Config:             Production
        Certificate Type:         Microsoft
    ```

3. Pokud vidíte výše uvedený výstup, váš firmware se aktualizuje.

4. Po aktualizaci firmwaru můžete spustit [Azure Kinect Viewer](azure-kinect-viewer.md) a ověřit, jestli všechny senzory fungují podle očekávání.

## <a name="troubleshooting"></a>Poradce při potížích

Aktualizace firmwaru mohou selhat z několika důvodů. Při neúspěšné aktualizaci firmwaru vyzkoušejte následující kroky pro zmírnění rizika:

1. Zkuste spustit příkaz pro aktualizaci firmwaru podruhé.

2. Potvrďte, že je zařízení pořád připojené k dotazování na verzi firmwaru.        AzureKinectFirmareTool.exe

3. Pokud všechny ostatní selžou, postupujte podle kroků [obnovení](https://support.microsoft.com/help/4494277/reset-azure-kinect-dk) a vraťte se do firmwaru továrny a zkuste to znovu.

Všechny další problémy najdete na [stránkách podpory Microsoftu](./index.yml) .

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
>[Nástroj pro firmware Azure Kinect](azure-kinect-firmware-tool.md)