---
author: cherylmc
ms.date: 12/06/2019
ms.service: expressroute
ms.topic: include
ms.author: cherylmc
ms.openlocfilehash: f04861d55c9cea3c79f4983f7be2e1f5a3c6d864
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "74926229"
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