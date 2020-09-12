---
title: 'Azure ExpressRoute: přesunutí klasických okruhů do Správce prostředků'
description: Tato stránka popisuje, jak přesunout klasický okruh do modelu nasazení Správce prostředků pomocí prostředí PowerShell.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 02/25/2019
ms.author: duau
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 2b88fb32fd4bc07bbaaaf8834646e8d585491dc6
ms.sourcegitcommit: 5a3b9f35d47355d026ee39d398c614ca4dae51c6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/02/2020
ms.locfileid: "89395685"
---
# <a name="move-expressroute-circuits-from-classic-to-resource-manager-deployment-model-using-powershell"></a>Přesun okruhů ExpressRoute z modelu nasazení Classic do Správce prostředků pomocí prostředí PowerShell

Chcete-li použít okruh ExpressRoute pro model nasazení Classic i Správce prostředků, je nutné přesunout okruh do modelu nasazení Správce prostředků. Následující části vám pomůžou přesunout okruh pomocí PowerShellu.

## <a name="before-you-begin"></a>Než začnete

[!INCLUDE [updated-for-az](../../includes/hybrid-az-ps.md)]

* Ověřte, že jsou v počítači nainstalovány moduly Classic a AZ Azure PowerShell místně. Další informace najdete v tématu [Instalace a konfigurace Azure PowerShellu](/powershell/azure/).
* Než začnete s [konfigurací, ujistěte](expressroute-prerequisites.md)se, že jste zkontrolovali požadavky, [požadavky na směrování](expressroute-routing.md)a [pracovní postupy](expressroute-workflows.md) .
* Přečtěte si informace, které jsou k dispozici v části [přesunutí okruhu ExpressRoute z modelu Classic na správce prostředků](expressroute-move.md). Ujistěte se, že plně rozumíte omezením a omezením.
* Ověřte, že okruh je plně funkční v modelu nasazení Classic.
* Ujistěte se, že máte skupinu prostředků vytvořenou v modelu nasazení Správce prostředků.

## <a name="move-an-expressroute-circuit"></a>Přesunutí okruhu ExpressRoute

### <a name="step-1-gather-circuit-details-from-the-classic-deployment-model"></a>Krok 1: shromáždění podrobností o obvodu z modelu nasazení Classic

Přihlaste se k prostředí Azure Classic a Shromážděte klíč služby.

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

4. K získání klíčů služby pro všechny okruhy ExpressRoute použijte následující rutinu. Po načtení klíčů zkopírujte **klíč služby** okruhu, který chcete přesunout do modelu nasazení Správce prostředků.

   ```powershell
   Get-AzureDedicatedCircuit
   ```

### <a name="step-2-sign-in-and-create-a-resource-group"></a>Krok 2: přihlášení a vytvoření skupiny prostředků

Přihlaste se k prostředí Správce prostředků a vytvořte novou skupinu prostředků.

1. Přihlaste se ke svému Azure Resource Manager prostředí.

   ```powershell
   Connect-AzAccount
   ```

2. Vyberte příslušné předplatné Azure.

   ```powershell
   Get-AzSubscription -SubscriptionName "<Enter Subscription Name here>" | Select-AzSubscription
   ```

3. Pokud ještě nemáte skupinu prostředků, upravte následující fragment kódu, aby se vytvořila nová skupina prostředků.

   ```powershell
   New-AzResourceGroup -Name "DemoRG" -Location "West US"
   ```

### <a name="step-3-move-the-expressroute-circuit-to-the-resource-manager-deployment-model"></a>Krok 3: přesunutí okruhu ExpressRoute do modelu nasazení Správce prostředků

Nyní jste připraveni přesunout okruh ExpressRoute z modelu nasazení Classic do modelu nasazení Správce prostředků. Než budete pokračovat, přečtěte si informace uvedené v části [přesunutí okruhu ExpressRoute z modelu nasazení Classic na model nasazení Správce prostředků](expressroute-move.md).

Chcete-li přesunout okruh, upravte a spusťte následující fragment kódu:

```powershell
Move-AzExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "DemoRG" -Location "West US" -ServiceKey "<Service-key>"
```

V klasickém režimu nemá okruh ExpressRoute žádný pojem spojený s oblastí. V Správce prostředků ale musí být každý prostředek namapovaný na oblast Azure. Oblast zadaná v rutině Move-AzExpressRouteCircuit může být technicky jakákoli oblast. Pro účely organizace můžete zvolit oblast, která bude úzce představovat vaše umístění partnerského vztahu.

> [!NOTE]
> Po dokončení přesunu se k řešení tohoto prostředku použije nový název, který je uvedený v předchozí rutině. Okruh bude v podstatě přejmenován.
> 

## <a name="modify-circuit-access"></a>Upravit přístup okruhu

### <a name="to-enable-expressroute-circuit-access-for-both-deployment-models"></a>Povolení přístupu okruhu ExpressRoute pro oba modely nasazení

Po přesunu klasického okruhu ExpressRoute do modelu nasazení Správce prostředků můžete povolit přístup k oběma modelům nasazení. Spusťte následující rutiny pro povolení přístupu do obou modelů nasazení:

1. Získat podrobnosti o okruhu.

   ```powershell
   $ckt = Get-AzExpressRouteCircuit -Name "DemoCkt" -ResourceGroupName "DemoRG"
   ```

2. Nastavte hodnotu "povolení klasických operací" na hodnotu TRUE.

   ```powershell
   $ckt.AllowClassicOperations = $true
   ```

3. Aktualizujte okruh. Po úspěšném dokončení této operace budete moct zobrazit okruh v modelu nasazení Classic.

   ```powershell
   Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
   ```

4. Spusťte následující rutinu, abyste získali podrobnosti o okruhu ExpressRoute. Musíte být schopni zobrazit uvedený klíč služby.

   ```powershell
   get-azurededicatedcircuit
   ```

5. Teď můžete spravovat odkazy na okruh ExpressRoute pomocí příkazů klasického modelu nasazení pro klasický virtuální sítě a příkazů Správce prostředků pro Správce prostředků virtuální sítě. Následující články vám pomůžou spravovat odkazy na okruh ExpressRoute:

    * [Propojení virtuální sítě s okruhem ExpressRoute v modelu nasazení Správce prostředků](expressroute-howto-linkvnet-arm.md)
    * [Propojení virtuální sítě s okruhem ExpressRoute v modelu nasazení Classic](expressroute-howto-linkvnet-classic.md)

### <a name="to-disable-expressroute-circuit-access-to-the-classic-deployment-model"></a>Zakázání přístupu okruhu ExpressRoute k modelu nasazení Classic

Pokud chcete zakázat přístup k modelu nasazení Classic, spusťte následující rutiny.

1. Získejte podrobnosti o okruhu ExpressRoute.

   ```powershell
   $ckt = Get-AzExpressRouteCircuit -Name "DemoCkt" -ResourceGroupName "DemoRG"
   ```

2. Nastavte hodnotu "Allow Classic Operations" na FALSE (NEPRAVDA).

   ```powershell
   $ckt.AllowClassicOperations = $false
   ```

3. Aktualizujte okruh. Po úspěšném dokončení této operace nebudete moci zobrazit okruh v modelu nasazení Classic.

   ```powershell
   Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
   ```

## <a name="next-steps"></a>Další kroky

* [Vytvoření a úprava směrování pro okruh ExpressRoute](expressroute-howto-routing-arm.md)
* [Propojení virtuální sítě s okruhem ExpressRoute](expressroute-howto-linkvnet-arm.md)
