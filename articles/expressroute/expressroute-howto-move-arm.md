---
title: 'Přesun okruhů z modelu nasazení classic do Resource Manageru – ExpressRoute: prostředí PowerShell: Azure | Dokumentace Microsoftu'
description: Tato stránka popisuje přesun okruhu klasického modelu nasazení Resource Manageru pomocí Powershellu.
services: expressroute
author: ganesr
ms.service: expressroute
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: ganesr;cherylmc
ms.custom: seodec18
ms.openlocfilehash: fae5ad4a9045115c0e7a68b0164593f639824073
ms.sourcegitcommit: 78ec955e8cdbfa01b0fa9bdd99659b3f64932bba
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/10/2018
ms.locfileid: "53141488"
---
# <a name="move-expressroute-circuits-from-classic-to-resource-manager-deployment-model-using-powershell"></a>Přesun okruhů ExpressRoute z klasického modelu nasazení Resource Manageru pomocí Powershellu

Pro účely okruhu ExpressRoute i klasické modely nasazení a modely nasazení Resource Manager, musíte přesunout okruh k modelu nasazení Resource Manager. V následujících částech můžete přesunout okruh pomocí prostředí PowerShell.

## <a name="before-you-begin"></a>Před zahájením

* Ověřte, že máte nejnovější verzi modulů prostředí Azure PowerShell (minimálně verze 1.0). Další informace najdete v tématu [Instalace a konfigurace Azure PowerShellu](/powershell/azure/overview).
* Ujistěte se, že jste si prohlédli [požadavky](expressroute-prerequisites.md), [požadavky směrování](expressroute-routing.md), a [pracovních postupů](expressroute-workflows.md) předtím, než začnete s konfigurací.
* Projděte si informace, které není k dispozici [přesunu okruh ExpressRoute z modelu nasazení classic do Resource Manageru](expressroute-move.md). Ujistěte se, že plně chápete limity a omezení.
* Ověřte, že je okruh v modelu nasazení classic plně funkční.
* Ujistěte se, že máte skupinu prostředků, který byl vytvořen v modelu nasazení Resource Manager.

## <a name="move-an-expressroute-circuit"></a>Přesun okruhu ExpressRoute

### <a name="step-1-gather-circuit-details-from-the-classic-deployment-model"></a>Krok 1: Shromáždění podrobností okruhu z modelu nasazení classic

Přihlaste se k prostředí Azure classic a získat klíč služby.

1. Přihlaste se ke svému účtu Azure.

  ```powershell
  Add-AzureAccount
  ```

2. Vyberte příslušné předplatné Azure.

  ```powershell
  Select-AzureSubscription "<Enter Subscription Name here>"
  ```

3. Importujte další moduly Powershellu pro Azure a ExpressRoute.

  ```powershell
  Import-Module 'C:\Program Files (x86)\Microsoft SDKs\Azure\PowerShell\ServiceManagement\Azure\Azure.psd1'
  Import-Module 'C:\Program Files (x86)\Microsoft SDKs\Azure\PowerShell\ServiceManagement\Azure\ExpressRoute\ExpressRoute.psd1'
  ```

4. Použijte následující rutinu se získat klíče služby u všech okruhů ExpressRoute. Po načtení klíčů, zkopírujte **klíč služby** okruhu, který chcete přesunout do modelu nasazení Resource Manager.

  ```powershell
  Get-AzureDedicatedCircuit
  ```

### <a name="step-2-sign-in-and-create-a-resource-group"></a>Krok 2: Přihlaste se a vytvořte skupinu prostředků

Přihlaste se k Resource Manageru prostředí a vytvořit novou skupinu prostředků.

1. Přihlaste se k prostředí Azure Resource Manageru.

  ```powershell
  Connect-AzureRmAccount
  ```

2. Vyberte příslušné předplatné Azure.

  ```powershell
  Get-AzureRmSubscription -SubscriptionName "<Enter Subscription Name here>" | Select-AzureRmSubscription
  ```

3. Upravte fragment kódu níže můžete vytvořit novou skupinu prostředků, pokud ještě nemáte skupinu prostředků.

  ```powershell
  New-AzureRmResourceGroup -Name "DemoRG" -Location "West US"
  ```

### <a name="step-3-move-the-expressroute-circuit-to-the-resource-manager-deployment-model"></a>Krok 3: Přesunutí okruhu ExpressRoute do modelu nasazení Resource Manageru

Nyní jste připraveni na přesun okruhů ExpressRoute z modelu nasazení classic do modelu nasazení Resource Manageru. Než budete pokračovat, přečtěte si informace uvedené v [přesun okruhu ExpressRoute z klasického modelu nasazení Resource Manageru](expressroute-move.md).

Přesun okruhů, úpravu a spusťte následující fragment kódu:

```powershell
Move-AzureRmExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "DemoRG" -Location "West US" -ServiceKey "<Service-key>"
```

Okruh ExpressRoute v klasickém režimu, nemá koncept se spojené s konkrétní oblastí. Však v Resource Manageru každý prostředek musí být namapovaný na oblasti Azure. Zadanou v rutině Move-AzureRmExpressRouteCircuit oblast technicky může být libovolné oblasti. Pro účely organizace můžete vybrat oblast, která úzce představuje vaše umístění partnerského vztahu.

> [!NOTE]
> Po dokončení přesunu nový název, který je uveden v předchozí rutiny se použije k vyřešení zdroje. Okruh se v podstatě přejmenovat.
> 

## <a name="modify-circuit-access"></a>Upravit přístup k okruhu

### <a name="to-enable-expressroute-circuit-access-for-both-deployment-models"></a>K povolení přístupu k okruhu ExpressRoute pro oba modely nasazení

Po přesunutí klasický okruh ExpressRoute do modelu nasazení Resource Manageru, můžete povolit přístup k oběma modelům nasazení. Spuštěním následující rutiny umožňující přístup k oběma modelům nasazení:

1. Získání podrobností o okruhu.

  ```powershell
  $ckt = Get-AzureRmExpressRouteCircuit -Name "DemoCkt" -ResourceGroupName "DemoRG"
  ```

2. Nastavení "Povolit klasické operace" na hodnotu TRUE.

  ```powershell
  $ckt.AllowClassicOperations = $true
  ```

3. Aktualizujte okruh. Po úspěšném dokončení této operace bude moci zobrazit okruh v modelu nasazení classic.

  ```powershell
  Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt
  ```

4. Spuštěním následující rutiny pro získání podrobností o okruhu ExpressRoute. Musíte být vidět uvedený klíč služby.

  ```powershell
  get-azurededicatedcircuit
  ```

5. Teď můžete spravovat odkazy k okruhu ExpressRoute pomocí příkazů modelu nasazení classic pro klasické virtuální sítě a příkazy Resource Manageru pro virtuální sítě Resource Manageru. Tyto články vám pomohou spravovat odkazy na okruh ExpressRoute:

    * [Propojení virtuální sítě pro váš okruh ExpressRoute v modelu nasazení Resource Manager](expressroute-howto-linkvnet-arm.md)
    * [Propojení virtuální sítě pro váš okruh ExpressRoute v modelu nasazení classic](expressroute-howto-linkvnet-classic.md)

### <a name="to-disable-expressroute-circuit-access-to-the-classic-deployment-model"></a>Chcete-li zakázat přístup k okruhu ExpressRoute do modelu nasazení classic

Spusťte následující rutiny můžete zakázat přístup k modelu nasazení classic.

1. Získáte podrobnosti o okruhu ExpressRoute.

  ```powershell
  $ckt = Get-AzureRmExpressRouteCircuit -Name "DemoCkt" -ResourceGroupName "DemoRG"
  ```

2. Nastavení "Povolit klasické operace" na hodnotu FALSE.

  ```powershell
  $ckt.AllowClassicOperations = $false
  ```

3. Aktualizujte okruh. Po úspěšném dokončení této operace nebudou moct zobrazit okruh v modelu nasazení classic.

  ```powershell
Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt
  ```

## <a name="next-steps"></a>Další postup

* [Vytvoření a úprava směrování pro okruh ExpressRoute](expressroute-howto-routing-arm.md)
* [Propojení virtuální sítě pro váš okruh ExpressRoute](expressroute-howto-linkvnet-arm.md)
