---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 03/04/2019
ms.author: alkohli
ms.openlocfilehash: d5af557a62f4bd35c242d334c28a38c3d632f7cf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "67175467"
---
1. [Připojte se k rozhraní prostředí PowerShell](#connect-to-the-powershell-interface).
2. Použijte `Get-HcsApplianceInfo` k získání informací pro vaše zařízení.

    Následující příklad ukazuje použití této rutiny:

    ```
    [10.100.10.10]: PS>Get-HcsApplianceInfo
    
    Id                            : b2044bdb-56fd-4561-a90b-407b2a67bdfc
    FriendlyName                  : DBE-NBSVFQR94S6
    Name                          : DBE-NBSVFQR94S6
    SerialNumber                  : HCS-NBSVFQR94S6
    DeviceId                      : 40d7288d-cd28-481d-a1ea-87ba9e71ca6b
    Model                         : Virtual
    FriendlySoftwareVersion       : Data Box Gateway 1902
    HcsVersion                    : 1.4.771.324
    IsClustered                   : False
    IsVirtual                     : True
    LocalCapacityInMb             : 1964992
    SystemState                   : Initialized
    SystemStatus                  : Normal
    Type                          : DataBoxGateway
    CloudReadRateBytesPerSec      : 0
    CloudWriteRateBytesPerSec     : 0
    IsInitialPasswordSet          : True
    FriendlySoftwareVersionNumber : 1902
    UploadPolicy                  : All
    DataDiskResiliencySettingName : Simple
    ApplianceTypeFriendlyName     : Data Box Gateway
    IsRegistered                  : False
    ```

    Zde je tabulka shrnující některé důležité informace o zařízení:
    
    | Parametr                             | Popis                                                                                                                                                  |   |
    |--------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------|---|
    | Friendlyname                   | Popisný název zařízení nakonfigurovaný prostřednictvím místního webového uživatelského rozhraní během nasazení zařízení. Výchozí popisný název je sériové číslo zařízení.  |   |
    | SerialNumber                   | Sériové číslo zařízení je jedinečné číslo přiřazené z výroby.                                                                             |   |
    | Model                          | Model pro zařízení Data Box Edge nebo Data Box Gateway. Model je virtuální pro bránu datové schránky a fyzické pro data box edge.                   |   |
    | Verze FriendlySoftwareVersion        | Přátelský řetězec, který odpovídá verzi softwaru zařízení. Pro systém se spuštěnou předběžnou verzi by přátelská verze softwaru byla Data Box Edge 1902. |   |
    | HcsVersion                     | Verze softwaru HCS spuštěná na vašem zařízení. Například verze softwaru HCS odpovídající Data Box Edge 1902 je 1.4.771.324.            |   |
    | Místní kapacitainmb              | Celková místní kapacita zařízení v Megabits.                                                                                                        |   |
    | Jeregistrován                   | Tato hodnota označuje, zda je zařízení aktivováno službou.                                                                                         |   |


