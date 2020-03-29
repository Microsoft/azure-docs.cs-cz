---
author: cherylmc
ms.date: 12/06/2019
ms.service: expressroute
ms.topic: include
ms.author: cherylmc
ms.openlocfilehash: f04861d55c9cea3c79f4983f7be2e1f5a3c6d864
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "74926229"
---
Nainstalujte nejnovější verze modulů PowerShellu azure service managementu (SM) a modulu ExpressRoute. Prostředí Azure CloudShell nelze použít ke spuštění modulů SM.

1. Pomocí pokynů v článku [Instalace modulu Správa služeb](/powershell/azure/servicemanagement/install-azure-ps) nainstalujte modul správy služeb Azure. Pokud máte již nainstalovaný modul Az nebo RM, nezapomeňte použít '-AllowClobber'.
2. Importujte nainstalované moduly. Při použití následujícího příkladu upravte cestu tak, aby odrážela umístění a verzi nainstalovaných modulů prostředí PowerShell.

   ```powershell
   Import-Module 'C:\Program Files\WindowsPowerShell\Modules\Azure\5.3.0\Azure.psd1'
   Import-Module 'C:\Program Files\WindowsPowerShell\Modules\Azure\5.3.0\ExpressRoute\ExpressRoute.psd1'
   ```
3. Pokud se chcete přihlásit ke svému účtu Azure, otevřete si konzolu PowerShell se zvýšenými právy a připojte se ke svému účtu. Pomocí následujícího příkladu můžete připojit pomocí modulu Správa služeb:

   ```powershell
   Add-AzureAccount
   ```