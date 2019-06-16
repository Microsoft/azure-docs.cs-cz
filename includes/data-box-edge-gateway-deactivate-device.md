---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 03/05/2019
ms.author: alkohli
ms.openlocfilehash: aebb82690a7a49aba071ed64349d37d516208cca
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/23/2019
ms.locfileid: "67125611"
---
Postup resetování zařízení, budete muset bezpečně vymazat veškerá data na datový disk a spouštěcí disk vašeho zařízení. 

Použití `Reset-HcsAppliance` rutiny vymazat datové disky a spouštěcí disk nebo jenom datové disky. `ClearData` a `BootDisk` přepínače umožňují vymazat datové disky a spouštěcí disk v uvedeném pořadí.

Pokud používáte zařízení obnovit v místním webovém uživatelském rozhraní, datových disků jsou bezpečně vymazáno, ale spouštěcí disk, zůstane beze změny. Spouštěcí disk obsahuje konfiguraci zařízení.

1. [Připojení k rozhraní PowerShell](#connect-to-the-powershell-interface).
2. Na příkazovém řádku zadejte:

    `Reset-HcsAppliance -ClearData -BootDisk`

    Následující příklad ukazuje, jak použít tuto rutinu:
    ```
    [10.128.24.33]: PS>Reset-HcsAppliance -ClearData -BootDisk
    
    Confirm
    Are you sure you want to perform this action?
    Performing the operation "Reset-HcsAppliance" on target "ShouldProcess appliance".
    [Y] Yes  [A] Yes to All  [N] No  [L] No to All  [?] Help (default is "Y"): N
    ```
