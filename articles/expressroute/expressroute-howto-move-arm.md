---
title: 'Přesun okruhů ExpressRoute z classic do Resource Manager: prostředí PowerShell: Azure | Microsoft Docs'
description: Tato stránka popisuje, jak přesunout classic okruhu do modelu nasazení Resource Manager pomocí prostředí PowerShell.
documentationcenter: na
services: expressroute
author: ganesr
manager: timlt
editor: ''
tags: azure-resource-manager
ms.assetid: 08152836-23e7-42d1-9a56-8306b341cd91
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/03/2017
ms.author: ganesr;cherylmc
ms.openlocfilehash: 37db218010288912bf75d00fd5868aab171aef71
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/19/2018
---
# <a name="move-expressroute-circuits-from-the-classic-to-the-resource-manager-deployment-model-using-powershell"></a>Přesun okruhů ExpressRoute z classic do modelu nasazení Resource Manager pomocí prostředí PowerShell

Pokud chcete použít okruhu ExpressRoute pro classic i modelech nasazení Resource Manager, je třeba přesunout okruh do modelu nasazení Resource Manager. V následujících částech můžete přesunout váš okruh pomocí prostředí PowerShell.

## <a name="before-you-begin"></a>Než začnete

* Ověřte, že máte nejnovější verzi modulů prostředí Azure PowerShell (minimálně verze 1.0). Další informace najdete v tématu [Instalace a konfigurace Azure PowerShellu](/powershell/azure/overview).
* Ujistěte se, že jste si přečetli [požadavky](expressroute-prerequisites.md), [požadavky na směrování](expressroute-routing.md), a [pracovních](expressroute-workflows.md) před zahájením konfigurace.
* Zkontrolujte informace, které jsou poskytovány na základě [přesun okruhu ExpressRoute z classic do Resource Manager](expressroute-move.md). Ujistěte se, že rozumíte plně limity a omezení.
* Ověřte, že je okruh v modelu nasazení classic plně funkční.
* Ujistěte se, že máte skupinu prostředků, který byl vytvořen v modelu nasazení Resource Manager.

## <a name="move-an-expressroute-circuit"></a>Přesun okruhu ExpressRoute

### <a name="step-1-gather-circuit-details-from-the-classic-deployment-model"></a>Krok 1: Shromáždění podrobností okruh z modelu nasazení classic

Přihlaste se k prostředí Azure classic a shromáždit klíč služby.

1. Přihlaste se k účtu Azure.

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

4. Použijte následující rutinu k získání klíče služby pro všechny vaše okruhy ExpressRoute. Po načtení klíče, zkopírujte **klíč služby** okruhu, který chcete přesunout do modelu nasazení Resource Manager.

  ```powershell
  Get-AzureDedicatedCircuit
  ```

### <a name="step-2-sign-in-and-create-a-resource-group"></a>Krok 2: Přihlaste se a vytvořte skupinu prostředků

Přihlaste se k prostředí Resource Manager a vytvořte novou skupinu prostředků.

1. Přihlaste se do prostředí Azure Resource Manager.

  ```powershell
  Connect-AzureRmAccount
  ```

2. Vyberte příslušné předplatné Azure.

  ```powershell
  Get-AzureRmSubscription -SubscriptionName "<Enter Subscription Name here>" | Select-AzureRmSubscription
  ```

3. Chcete-li vytvořit novou skupinu prostředků, pokud ještě nemáte skupinu prostředků fragment upravte.

  ```powershell
  New-AzureRmResourceGroup -Name "DemoRG" -Location "West US"
  ```

### <a name="step-3-move-the-expressroute-circuit-to-the-resource-manager-deployment-model"></a>Krok 3: Přesunout okruh ExpressRoute do modelu nasazení Resource Manager

Nyní jste připraveni pro přesun okruhu ExpressRoute z modelu nasazení classic do modelu nasazení Resource Manager. Než budete pokračovat, přečtěte si informace uvedené v [přesun okruhu ExpressRoute z klasického modelu nasazení Resource Manager](expressroute-move.md).

Chcete-li přesunout váš okruh, upravit a spusťte následující fragment kódu:

```powershell
Move-AzureRmExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "DemoRG" -Location "West US" -ServiceKey "<Service-key>"
```

> [!NOTE]
> Po dokončení přesunu nový název, který je uvedený v předchozí rutiny bude používat k adresování prostředku. Okruhu bude v podstatě přejmenovat.
> 

## <a name="modify-circuit-access"></a>Úprava okruhu přístup

### <a name="to-enable-expressroute-circuit-access-for-both-deployment-models"></a>Chcete-li povolit přístup k okruhu ExpressRoute pro oba modely nasazení

Po přesunutí classic okruhu ExpressRoute do modelu nasazení Resource Manager, můžete povolit přístup k oběma modelům nasazení. Spusťte následující rutiny a povolte tak přístup k oběma modelům nasazení:

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

4. Spusťte následující rutiny můžete získat podrobnosti o okruhu ExpressRoute. Musí umět klíč služby uvedené v tématu.

  ```powershell
  get-azurededicatedcircuit
  ```

5. Teď můžete spravovat odkazy na okruh ExpressRoute pomocí příkazů modelu nasazení classic pro virtuální sítě classic a Resource Manager příkazy pro virtuální sítě Resource Manager. Tyto články vám pomůžou spravovat odkazy na okruh ExpressRoute:

    * [Propojení virtuální sítě k okruhu ExpressRoute v modelu nasazení Resource Manager](expressroute-howto-linkvnet-arm.md)
    * [Propojení virtuální sítě k okruhu ExpressRoute v modelu nasazení classic](expressroute-howto-linkvnet-classic.md)

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

3. Aktualizujte okruh. Po této operaci byl úspěšně dokončen, nebudete moci zobrazit okruh v modelu nasazení classic.

  ```powershell
Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt
  ```

## <a name="next-steps"></a>Další postup

* [Vytvoření a úprava směrování pro okruhu ExpressRoute](expressroute-howto-routing-arm.md)
* [Propojení virtuální sítě k okruhu ExpressRoute](expressroute-howto-linkvnet-arm.md)
