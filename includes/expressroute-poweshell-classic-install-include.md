---
author: cherylmc
ms.date: 12/06/2019
ms.service: expressroute
ms.topic: include
ms.author: cherylmc
ms.openlocfilehash: f04861d55c9cea3c79f4983f7be2e1f5a3c6d864
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "96018143"
---
Nainstalujte nejnovější verze modulů Azure Service Management (SM) PowerShell a modulu ExpressRoute. Nemůžete použít prostředí Azure Cloudshellu ke spouštění modulů SM.

1. Pomocí pokynů v článku [Instalace modulu správy služeb](/powershell/azure/servicemanagement/install-azure-ps) nainstalujte modul Azure Service Management. Pokud máte modul AZ nebo RM nainstalovaný už, nezapomeňte použít-AllowClobber.
2. Naimportujte nainstalované moduly. Když použijete následující příklad, upravte cestu tak, aby odrážela umístění a verzi nainstalovaných modulů PowerShellu.

   ```powershell
   Import-Module 'C:\Program Files\WindowsPowerShell\Modules\Azure\5.3.0\Azure.psd1'
   Import-Module 'C:\Program Files\WindowsPowerShell\Modules\Azure\5.3.0\ExpressRoute\ExpressRoute.psd1'
   ```
3. Pokud se chcete přihlásit ke svému účtu Azure, otevřete konzolu PowerShellu se zvýšenými právy a připojte se ke svému účtu. Použijte následující příklad, který vám umožní připojit se pomocí modulu pro správu služeb:

   ```powershell
   Add-AzureAccount
   ```