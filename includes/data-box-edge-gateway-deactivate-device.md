---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 03/05/2019
ms.author: alkohli
ms.openlocfilehash: 21c19027d21a87e199d74644cfc5c8f3cd52ba4c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79128615"
---
Chcete-li zařízení resetovat, musíte bezpečně vymazat všechna data na datovém disku a spouštěcím disku zařízení. 

Pomocí `Reset-HcsAppliance` rutiny vymažte datové disky i spouštěcí disk nebo pouze datové disky. `ClearData` Přepínače `BootDisk` a přepínače umožňují vymazat datové disky a spouštěcí disk.

Přepínač `BootDisk` vymaže spouštěcí disk a způsobí, že zařízení bude nepoužitelné. Měl by být používán pouze v případě, že zařízení musí být vráceno společnosti Microsoft. Další informace naleznete [v tématu Return the device to Microsoft](https://docs.microsoft.com/azure/databox-online/data-box-edge-return-device).

Pokud používáte resetování zařízení v místním webovém uživatelském rozhraní, budou bezpečně vymazány pouze datové disky, ale spouštěcí disk je zachován beze změny. Spouštěcí disk obsahuje konfiguraci zařízení.

1. [Připojte se k rozhraní prostředí PowerShell](#connect-to-the-powershell-interface).
2. Na příkazovém řádku zadejte:

    `Reset-HcsAppliance -ClearData -BootDisk`

    Následující příklad ukazuje, jak používat tuto rutinu:

    ```powershell
    [10.128.24.33]: PS>Reset-HcsAppliance -ClearData -BootDisk

    Confirm
    Are you sure you want to perform this action?
    Performing the operation "Reset-HcsAppliance" on target "ShouldProcess appliance".
    [Y] Yes  [A] Yes to All  [N] No  [L] No to All  [?] Help (default is "Y"): N
    ```
