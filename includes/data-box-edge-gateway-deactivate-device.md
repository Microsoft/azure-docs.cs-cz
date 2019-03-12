---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 03/05/2019
ms.author: alkohli
ms.openlocfilehash: aebb82690a7a49aba071ed64349d37d516208cca
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/07/2019
ms.locfileid: "57554974"
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
