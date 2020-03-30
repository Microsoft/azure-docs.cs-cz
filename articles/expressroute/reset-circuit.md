---
title: 'Obnovení neúspěšného okruhu – ExpressRoute: PowerShell: Azure | Dokumenty společnosti Microsoft'
description: Tento článek vám pomůže obnovit okruh ExpressRoute, který je ve stavu selhání.
services: expressroute
author: anzaman
ms.service: expressroute
ms.topic: article
ms.date: 11/28/2018
ms.author: anzaman
ms.custom: seodec18
ms.openlocfilehash: deeb1c65cae7e3a5b42230dbda1ad8efa717ba0b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "73748100"
---
# <a name="reset-a-failed-expressroute-circuit"></a>Resetování okruhu ExpressRoute, který selhal

Pokud operace na okruhu ExpressRoute není úspěšně dokončena, okruh může přejít do stavu "selhání". Tento článek vám pomůže obnovit neúspěšný okruh Azure ExpressRoute.

[!INCLUDE [updated-for-az](../../includes/hybrid-az-ps.md)]

## <a name="reset-a-circuit"></a>Obnovení obvodu

1. Nainstalujte nejnovější verzi rutin PowerShellu pro Azure Resource Manager. Další informace najdete v článku [Instalace a konfigurace Azure PowerShellu](/powershell/azure/install-az-ps).

2. Otevřete konzolu PowerShellu se zvýšenými oprávněními a připojte se ke svému účtu. Připojení vám usnadní následující ukázka:

   ```azurepowershell-interactive
   Connect-AzAccount
   ```
3. Pokud máte více předplatných Azure, zkontrolujte předplatná pro daný účet.

   ```azurepowershell-interactive
   Get-AzSubscription
   ```
4. Určete předplatné, které chcete použít.

   ```azurepowershell-interactive
   Select-AzSubscription -SubscriptionName "Replace_with_your_subscription_name"
   ```
5. Chcete-li obnovit okruh, který se nezdařil, spusťte následující příkazy:

   ```azurepowershell-interactive
   $ckt = Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

   Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
   ```

Okruh by nyní měl být zdravý. Pokud je okruh stále ve stavu selhání, otevřete lístek podpory s [podporou společnosti Microsoft.](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)

## <a name="next-steps"></a>Další kroky

Pokud stále dochází k problémům, otevřete lístek podpory s [podporou Microsoftu.](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)
