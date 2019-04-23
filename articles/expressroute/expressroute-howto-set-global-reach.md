---
title: 'Konfigurace globální dosah – ExpressRoute: Azure | Dokumentace Microsoftu'
description: Tento článek vám pomůže propojit okruhy ExpressRoute tvořit privátní sítě mezi místními sítěmi a povolit globální dosah.
services: expressroute
author: jaredr80
ms.service: expressroute
ms.topic: conceptual
ms.date: 02/25/2019
ms.author: jaredro
ms.custom: seodec18
ms.openlocfilehash: 8ea3b3580cb70d0453a5ec6a38f6063788ebf7f4
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60364741"
---
# <a name="configure-expressroute-global-reach"></a>Konfigurace služby ExpressRoute Global Reach

Tento článek vám pomůže nakonfigurovat ExpressRoute globální dosah pomocí Powershellu. Další informace najdete v tématu [ExpressRouteRoute globální dosah](expressroute-global-reach.md).

 ## <a name="before-you-begin"></a>Než začnete

Před zahájením konfigurace, zkontrolujte následující body:

* Rozumíte zřizování okruhů ExpressRoute [pracovních postupů](expressroute-workflows.md).
* Vaše okruhy ExpressRoute jsou ve stavu zřízeno.
* Soukromý partnerský vztah Azure je nakonfigurovaná na okruhů ExpressRoute.
* Pokud chcete spustit prostředí PowerShell místně, ověřte, že je ve vašem počítači nainstalovaná nejnovější verze Azure powershellu.

### <a name="working-with-azure-powershell"></a>Práce s využitím Azure Powershellu

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [expressroute-cloudshell](../../includes/expressroute-cloudshell-powershell-about.md)]

## <a name="identify-circuits"></a>Identifikujte okruhy

1. Spustit konfiguraci, přihlaste se ke svému účtu Azure a vyberte předplatné, pro kterou chcete použít.

   [!INCLUDE [sign in](../../includes/expressroute-cloud-shell-connect.md)]
2. Identifikujte okruhy ExpressRoute, kterou chcete použít. Můžete povolit globální dosah ExpressRoute mezi jakékoli dva okruhy ExpressRoute nacházejí v podporované země a byly vytvořeny v různých umístění partnerského vztahu. 

   * Pokud vaše předplatné vlastní obou okruzích, můžete buď okruh a spustit konfiguraci v následujících částech.
   * Pokud dva okruhy jsou v různých předplatných Azure, je nutné autorizace z jednoho předplatného Azure. Pak můžete předat autorizační klíč při spuštění příkazu konfigurace v rámci předplatného Azure.

## <a name="enable-connectivity"></a>Povolení připojení

Povolte připojení mezi místními sítěmi. Existují samostatné sady instrukcí pro okruhů, které jsou ve stejném předplatném Azure a okruhů, které jsou v různých předplatných.

### <a name="expressroute-circuits-in-the-same-azure-subscription"></a>Okruhy ExpressRoute v rámci stejného předplatného Azure

1. Použijte následující příkazy k získání okruh 1 a 2 okruhu. Dva okruhy jsou ve stejném předplatném.

   ```azurepowershell-interactive
   $ckt_1 = Get-AzExpressRouteCircuit -Name "Your_circuit_1_name" -ResourceGroupName "Your_resource_group"
   $ckt_2 = Get-AzExpressRouteCircuit -Name "Your_circuit_2_name" -ResourceGroupName "Your_resource_group"
   ```
2. Spusťte následující příkaz okruh 1 a předat ID privátní partnerský vztah okruhu 2. Při spuštění příkazu, vezměte na vědomí následující:

   * ID privátní partnerský vztah vypadá podobně jako v následujícím příkladu: 

     ```
     /subscriptions/{your_subscription_id}/resourceGroups/{your_resource_group}/providers/Microsoft.Network/expressRouteCircuits/{your_circuit_name}/peerings/AzurePrivatePeering
     ```
   * *-AddressPrefix* musí být IPv4 je/29 podsítě, například "10.0.0.0/29". K navázání připojení mezi dva okruhy ExpressRoute používáme IP adres v této podsíti. Adresy byste neměli používat v této podsíti ve virtuálním sítím Azure nebo v místní síti.

     ```azurepowershell-interactive
     Add-AzExpressRouteCircuitConnectionConfig -Name 'Your_connection_name' -ExpressRouteCircuit $ckt_1 -PeerExpressRouteCircuitPeering $ckt_2.Peerings[0].Id -AddressPrefix '__.__.__.__/29'
     ```
3. Uložte konfiguraci v okruhu 1 následujícím způsobem:

   ```azurepowershell-interactive
   Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt_1
   ```

Po dokončení předchozí operace, budou mít připojení mezi vaší místní sítě na obou stranách prostřednictvím dvou okruhů ExpressRoute.

### <a name="expressroute-circuits-in-different-azure-subscriptions"></a>Okruhy ExpressRoute v různých předplatných Azure

Pokud dva okruhy nejsou ve stejném předplatném Azure, musíte autorizaci. V následující konfiguraci autorizace je vygenerována v rámci předplatného k okruhu 2 a autorizační klíč je předán okruh 1.

1. Generovat autorizačního klíče.

   ```azurepowershell-interactive
   $ckt_2 = Get-AzExpressRouteCircuit -Name "Your_circuit_2_name" -ResourceGroupName "Your_resource_group"
   Add-AzExpressRouteCircuitAuthorization -ExpressRouteCircuit $ckt_2 -Name "Name_for_auth_key"
   Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt_2
   ```

   ID privátní partnerský vztah okruhu 2, stejně jako autorizační klíč si poznamenejte.
2. Spusťte následující příkaz okruh 1. Předejte ID privátní partnerský vztah okruhu 2 a autorizačního klíče.

   ```azurepowershell-interactive
   Add-AzExpressRouteCircuitConnectionConfig -Name 'Your_connection_name' -ExpressRouteCircuit $ckt_1 -PeerExpressRouteCircuitPeering "circuit_2_private_peering_id" -AddressPrefix '__.__.__.__/29' -AuthorizationKey '########-####-####-####-############'
   ```
3. Uložte konfiguraci v okruhu 1.

   ```azurepowershell-interactive
   Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt_1
   ```

Po dokončení předchozí operace, budou mít připojení mezi vaší místní sítě na obou stranách prostřednictvím dvou okruhů ExpressRoute.

## <a name="verify-the-configuration"></a>Ověření konfigurace

Použijte následující příkaz k ověření konfigurace na okruh, kde byla provedena konfigurace (například 1 okruh v předchozím příkladu).
```azurepowershell-interactive
$ckt1 = Get-AzExpressRouteCircuit -Name "Your_circuit_1_name" -ResourceGroupName "Your_resource_group"
```

Pokud spustíte jednoduše *$ckt1* v prostředí PowerShell, uvidíte *CircuitConnectionStatus* ve výstupu. Zjistíte, jestli je připojení vytvořeno, "Připojeno" nebo "Odpojené". 

## <a name="disable-connectivity"></a>Zakázat připojení

Chcete-li zakázat připojení mezi vaší místní sítě, spusťte příkazy proti okruh, kde byla provedena konfigurace (například okruh 1 v předchozím příkladu).

```azurepowershell-interactive
$ckt1 = Get-AzExpressRouteCircuit -Name "Your_circuit_1_name" -ResourceGroupName "Your_resource_group"
Remove-AzExpressRouteCircuitConnectionConfig -Name "Your_connection_name" -ExpressRouteCircuit $ckt_1
Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt_1
```

Můžete spustit operaci Get, abyste ověřili stav.

Po dokončení předchozí operace už nebude mít připojení mezi vaší místní síti prostřednictvím okruhů ExpressRoute.

## <a name="next-steps"></a>Další postup
1. [Další informace o ExpressRoute globální dosah](expressroute-global-reach.md)
2. [Ověření připojení ExpressRoute](expressroute-troubleshooting-expressroute-overview.md)
3. [Propojení okruhu ExpressRoute ke službě Azure virtual network](expressroute-howto-linkvnet-arm.md)
