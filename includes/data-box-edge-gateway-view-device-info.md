---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 03/04/2019
ms.author: alkohli
ms.openlocfilehash: d5af557a62f4bd35c242d334c28a38c3d632f7cf
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/23/2019
ms.locfileid: "66161185"
---
1. [Připojení k rozhraní PowerShell](#connect-to-the-powershell-interface).
2. Použití `Get-HcsApplianceInfo` získat informace o zařízení.

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

    Tady je tabulka shrnuje některé z informací o důležité zařízení:
    
    | Parametr                             | Popis                                                                                                                                                  |   |
    |--------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------|---|
    | FriendlyName                   | Popisný název zařízení podle konfigurace prostřednictvím místního webového uživatelského rozhraní během nasazování zařízení. Popisný název výchozí je sériové číslo zařízení.  |   |
    | sériové číslo                   | Sériové číslo zařízení je jedinečné číslo přiřazené výrobcem.                                                                             |   |
    | Model                          | Model pro vaše zařízení Data Box Edge nebo brána dat pole. Model je pro bránu dat pole virtuálními a fyzickými Edge Data Box.                   |   |
    | FriendlySoftwareVersion        | Popisný řetězec, který odpovídá verzi softwaru zařízení. U systému, který používá ve verzi preview bude verze popisný softwaru 1902 okraj pole Data. |   |
    | HcsVersion                     | Verze softwaru HCS, která běží na vašem zařízení. Například verze softwaru HCS odpovídající Data Box Edge 1902 je 1.4.771.324.            |   |
    | LocalCapacityInMb              | Celková kapacita místní zařízení v MB.                                                                                                        |   |
    | IsRegistered                   | Tato hodnota označuje, pokud vaše zařízení se aktivuje ve službě.                                                                                         |   |


