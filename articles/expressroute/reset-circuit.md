---
title: 'Resetování neúspěšného okruhu – ExpressRoute: PowerShell: Azure | Microsoft Docs'
description: Tento článek vám pomůže resetovat okruh ExpressRoute, který je ve stavu selhání.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 11/28/2018
ms.author: duau
ms.custom: seodec18
ms.openlocfilehash: 7df96f34ee408c0a6d26b27adbac7351c9ab937f
ms.sourcegitcommit: 5a3b9f35d47355d026ee39d398c614ca4dae51c6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/02/2020
ms.locfileid: "89393084"
---
# <a name="reset-a-failed-expressroute-circuit"></a>Resetování okruhu ExpressRoute, který selhal

Po úspěšném dokončení operace na okruhu ExpressRoute může okruh přejít do stavu "neúspěch". Tento článek vám pomůže resetovat neúspěšný okruh Azure ExpressRoute.

[!INCLUDE [updated-for-az](../../includes/hybrid-az-ps.md)]

## <a name="reset-a-circuit"></a>Resetování okruhu

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
5. Spuštěním následujících příkazů resetujete okruh, který je ve stavu selhání:

   ```azurepowershell-interactive
   $ckt = Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

   Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
   ```

Okruh by teď měl být v pořádku. Pokud je okruh stále ve stavu selhání, otevřete lístek podpory s [podporou Microsoftu](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) .

## <a name="next-steps"></a>Další kroky

Pokud stále dochází k problémům, otevřete lístek podpory s [podporou Microsoftu](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) .
