---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 03/05/2019
ms.author: alkohli
ms.openlocfilehash: 21c19027d21a87e199d74644cfc5c8f3cd52ba4c
ms.sourcegitcommit: f97d3d1faf56fb80e5f901cd82c02189f95b3486
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/11/2020
ms.locfileid: "79128615"
---
Pokud chcete resetovat zařízení, musíte bezpečně vymazat všechna data na datovém a spouštěcím disku zařízení. 

Pomocí rutiny `Reset-HcsAppliance` můžete vymazat datové disky i spouštěcí disk nebo pouze datové disky. Přepínače `ClearData` umožňuje vymazat datové disky a přepínač `BootDisk` umožňuje vymazat spouštěcí disk.

Přepínač `BootDisk` vymaže spouštěcí disk a zařízení bude nepoužitelné. Měl by se použít pouze v případě, že je potřeba vrátit zařízení do Microsoftu. Další informace najdete v tématu [Vrácení zařízení do Microsoftu](https://docs.microsoft.com/azure/databox-online/data-box-edge-return-device).

Pokud využijete možnost resetování zařízení v místním webovém uživatelském rozhraní, bezpečně se vymažou pouze datové disky a spouštěcí disk zůstane nedotčený. Spouštěcí disk obsahuje konfiguraci zařízení.

1. [Připojte se k rozhraní PowerShellu](#connect-to-the-powershell-interface).
2. Na příkazovém řádku zadejte:

    `Reset-HcsAppliance -ClearData -BootDisk`

    Příklad použití této rutiny najdete v následujícím příkladu:

    ```powershell
    [10.128.24.33]: PS>Reset-HcsAppliance -ClearData -BootDisk

    Confirm
    Are you sure you want to perform this action?
    Performing the operation "Reset-HcsAppliance" on target "ShouldProcess appliance".
    [Y] Yes  [A] Yes to All  [N] No  [L] No to All  [?] Help (default is "Y"): N
    ```
