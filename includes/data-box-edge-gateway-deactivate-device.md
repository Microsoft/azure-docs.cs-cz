---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 03/05/2019
ms.author: alkohli
ms.openlocfilehash: 21c19027d21a87e199d74644cfc5c8f3cd52ba4c
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "79128615"
---
Pokud chcete resetovat zařízení, musíte bezpečně vymazat všechna data na datovém a spouštěcím disku zařízení. 

Pomocí `Reset-HcsAppliance` rutiny můžete vymazat datové disky i spouštěcí disk nebo jenom datové disky. `ClearData`Přepínače a `BootDisk` umožňují vymazat datové disky a spouštěcí disk v uvedeném pořadí.

`BootDisk`Přepínač vymaže spouštěcí disk a zařízení bude nepoužitelné. Měl by se použít pouze v případě, že je potřeba vrátit zařízení do Microsoftu. Další informace najdete v tématu o [vrácení zařízení do Microsoftu](https://docs.microsoft.com/azure/databox-online/data-box-edge-return-device).

Pokud využijete možnost resetování zařízení v místním webovém uživatelském rozhraní, bezpečně se vymažou pouze datové disky a spouštěcí disk zůstane nedotčený. Spouštěcí disk obsahuje konfiguraci zařízení.

1. [Připojte se k rozhraní PowerShell](#connect-to-the-powershell-interface).
2. Na příkazovém řádku zadejte:

    `Reset-HcsAppliance -ClearData -BootDisk`

    Následující příklad ukazuje, jak použít tuto rutinu:

    ```powershell
    [10.128.24.33]: PS>Reset-HcsAppliance -ClearData -BootDisk

    Confirm
    Are you sure you want to perform this action?
    Performing the operation "Reset-HcsAppliance" on target "ShouldProcess appliance".
    [Y] Yes  [A] Yes to All  [N] No  [L] No to All  [?] Help (default is "Y"): N
    ```
