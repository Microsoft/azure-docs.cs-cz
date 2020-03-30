---
title: 'Azure ExpressRoute: Přesunutí klasických okruhů do Správce prostředků'
description: Tato stránka popisuje, jak přesunout klasický okruh do modelu nasazení Správce prostředků pomocí prostředí PowerShell.
services: expressroute
author: ganesr
ms.service: expressroute
ms.topic: conceptual
ms.date: 02/25/2019
ms.author: cherylmc
ms.openlocfilehash: 4e49a3bc803733f5e78207fa3573c93395924d6a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74080157"
---
# <a name="move-expressroute-circuits-from-classic-to-resource-manager-deployment-model-using-powershell"></a>Přesunutí okruhů ExpressRoute z klasického modelu nasazení správce prostředků do modelu nasazení Správce prostředků pomocí prostředí PowerShell

Chcete-li použít okruh ExpressRoute pro klasické modely nasazení i pro správce prostředků, musíte jej přesunout do modelu nasazení Správce prostředků. Následující části vám pomohou přesunout okruh pomocí prostředí PowerShell.

## <a name="before-you-begin"></a>Než začnete

[!INCLUDE [updated-for-az](../../includes/hybrid-az-ps.md)]

* Ověřte, zda jste do počítače nainstalovali klasické moduly a moduly Az Azure PowerShell místně. Další informace najdete v tématu [Instalace a konfigurace Azure PowerShellu](/powershell/azure/overview).
* Před zahájením konfigurace zkontrolujte [požadavky](expressroute-prerequisites.md), [požadavky na směrování](expressroute-routing.md)a pracovní [postupy.](expressroute-workflows.md)
* Zkontrolujte informace, které jsou k dispozici v části [Přesunutí okruhu ExpressRoute z klasického do Správce prostředků](expressroute-move.md). Ujistěte se, že plně rozumíte limitům a omezením.
* Ověřte, zda je okruh plně funkční v modelu klasického nasazení.
* Ujistěte se, že máte skupinu prostředků, která byla vytvořena v modelu nasazení Resource Manager.

## <a name="move-an-expressroute-circuit"></a>Přesunutí okruhu ExpressRoute

### <a name="step-1-gather-circuit-details-from-the-classic-deployment-model"></a>Krok 1: Shromáždění podrobností o obvodu z klasického modelu nasazení

Přihlaste se ke klasickému prostředí Azure a shromážděte klíč služby.

1. Přihlaste se ke svému účtu Azure.

   ```powershell
   Add-AzureAccount
   ```

2. Vyberte příslušné předplatné Azure.

   ```powershell
   Select-AzureSubscription "<Enter Subscription Name here>"
   ```

3. Importujte moduly PowerShellu pro Azure a ExpressRoute.

   ```powershell
   Import-Module 'C:\Program Files\WindowsPowerShell\Modules\Azure\5.1.1\Azure\Azure.psd1'
   Import-Module 'C:\Program Files\WindowsPowerShell\Modules\Azure\5.1.1\ExpressRoute\ExpressRoute.psd1'
   ```

4. Pomocí níže uvedené rutiny získáte servisní klíče pro všechny okruhy ExpressRoute. Po načtení klíčů zkopírujte **klíč služby** okruhu, který chcete přesunout do modelu nasazení Správce prostředků.

   ```powershell
   Get-AzureDedicatedCircuit
   ```

### <a name="step-2-sign-in-and-create-a-resource-group"></a>Krok 2: Přihlášení a vytvoření skupiny prostředků

Přihlaste se do prostředí Správce prostředků a vytvořte novou skupinu prostředků.

1. Přihlaste se do prostředí Azure Resource Manager.

   ```powershell
   Connect-AzAccount
   ```

2. Vyberte příslušné předplatné Azure.

   ```powershell
   Get-AzSubscription -SubscriptionName "<Enter Subscription Name here>" | Select-AzSubscription
   ```

3. Upravte níže uvedený fragment a vytvořte novou skupinu prostředků, pokud ještě nemáte skupinu prostředků.

   ```powershell
   New-AzResourceGroup -Name "DemoRG" -Location "West US"
   ```

### <a name="step-3-move-the-expressroute-circuit-to-the-resource-manager-deployment-model"></a>Krok 3: Přesunutí okruhu ExpressRoute do modelu nasazení Správce prostředků

Nyní jste připraveni přesunout okruh ExpressRoute z klasického modelu nasazení do modelu nasazení Správce prostředků. Než budete pokračovat, zkontrolujte informace uvedené v [přesunutí okruhu ExpressRoute z klasického do modelu nasazení Resource Manageru](expressroute-move.md).

Chcete-li okruh přesunout, upravte a spusťte následující úryvek:

```powershell
Move-AzExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "DemoRG" -Location "West US" -ServiceKey "<Service-key>"
```

V klasickém režimu okruh ExpressRoute nemá koncept svázané s oblastí. Ve Správci prostředků však musí být každý prostředek mapován na oblast Azure. Oblast zadaná v rutině Move-AzExpressRouteCircuit může být technicky libovolná oblast. Pro organizační účely můžete zvolit oblast, která úzce představuje umístění partnerského vztahu.

> [!NOTE]
> Po dokončení přesunu bude nový název uvedený v předchozí rutině použit k adresování prostředku. Obvod bude v podstatě přejmenován.
> 

## <a name="modify-circuit-access"></a>Změnit přístup k okruhu

### <a name="to-enable-expressroute-circuit-access-for-both-deployment-models"></a>Povolení přístupu k okruhům ExpressRoute pro oba modely nasazení

Po přesunutí klasického okruhu ExpressRoute do modelu nasazení Správce prostředků můžete povolit přístup k oběma modelům nasazení. Spusťte následující rutiny, abyste povolili přístup k oběma modelům nasazení:

1. Získejte detaily obvodu.

   ```powershell
   $ckt = Get-AzExpressRouteCircuit -Name "DemoCkt" -ResourceGroupName "DemoRG"
   ```

2. Nastavte hodnotu "Povolit klasické operace" na hodnotu TRUE.

   ```powershell
   $ckt.AllowClassicOperations = $true
   ```

3. Aktualizujte obvod. Po úspěšném dokončení této operace budete moci zobrazit okruh v modelu klasického nasazení.

   ```powershell
   Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
   ```

4. Spusťte následující rutinu, abyste získali podrobnosti o okruhu ExpressRoute. Musíte být schopni zobrazit uvedený klíč služby.

   ```powershell
   get-azurededicatedcircuit
   ```

5. Teď můžete spravovat odkazy na okruh ExpressRoute pomocí klasických příkazů modelu nasazení pro klasické virtuální sítě a příkazů Správce prostředků pro virtuální sítě Správce prostředků. Následující články vám pomohou spravovat odkazy na okruh ExpressRoute:

    * [Propojení virtuální sítě s okruhem ExpressRoute v modelu nasazení Správce prostředků](expressroute-howto-linkvnet-arm.md)
    * [Propojení virtuální sítě s okruhem ExpressRoute v klasickém modelu nasazení](expressroute-howto-linkvnet-classic.md)

### <a name="to-disable-expressroute-circuit-access-to-the-classic-deployment-model"></a>Zakázání přístupu okruhu ExpressRoute ke klasickému modelu nasazení

Spusťte následující rutiny a zakažte přístup ke klasickému modelu nasazení.

1. Získejte podrobnosti o okruhu ExpressRoute.

   ```powershell
   $ckt = Get-AzExpressRouteCircuit -Name "DemoCkt" -ResourceGroupName "DemoRG"
   ```

2. Nastavte "Povolit klasické operace" na HODNOTU NEPRAVDA.

   ```powershell
   $ckt.AllowClassicOperations = $false
   ```

3. Aktualizujte obvod. Po úspěšném dokončení této operace nebude možné zobrazit okruh v modelu klasického nasazení.

   ```powershell
   Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
   ```

## <a name="next-steps"></a>Další kroky

* [Vytvoření a úprava směrování okruhu ExpressRoute](expressroute-howto-routing-arm.md)
* [Propojení virtuální sítě s okruhem ExpressRoute](expressroute-howto-linkvnet-arm.md)
