---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 10/15/2020
ms.author: alkohli
ms.openlocfilehash: ca060e75e50a3e2327fc0516c3cfc9550afbf90f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "96581801"
---
1. [Připojte se k rozhraní PowerShell](#connect-to-the-powershell-interface).
2. `Get-HcsApplianceInfo`Informace pro vaše zařízení získáte pomocí.

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

    Tady je tabulka shrnující některé důležité informace o zařízení:

    | Parametr | Popis |
    |-----------|-------------|
    | FriendlyName                   | Popisný název zařízení nakonfigurovaný prostřednictvím místního webového uživatelského rozhraní během nasazování zařízení. Výchozím popisným názvem je sériové číslo zařízení.  |
    | SerialNumber                   | Sériové číslo zařízení je jedinečné číslo přiřazené v továrně.                                                                             |
    | Modelování                          | Model pro vaše zařízení. Model je virtuální pro Data Box Gateway.                   |
    | FriendlySoftwareVersion        | Popisný řetězec, který odpovídá verzi softwaru zařízení. V systému, ve kterém je spuštěný náhled, by se měla používat popisná verze softwaru Data Box Edge 1902. |
    | HcsVersion                     | Verze softwaru klientovi HCS spuštěná na vašem zařízení. Například verze softwaru klientovi HCS, která odpovídá Data Box Edge 1902, je 1.4.771.324.            |
    | LocalCapacityInMb              | Celková místní kapacita zařízení v megabajtech.                                                                                                        |
    | Registrováno                   | Tato hodnota označuje, jestli je zařízení aktivované službou.                                                                                         |


