---
title: 'Obnovení se nezdařilo - okruhu ExpressRoute: PowerShell: Azure | Dokumentace Microsoftu'
description: Tento článek pomůže obnovit okruh ExpressRoute, který je ve stavu selhání.
services: expressroute
author: anzaman
ms.service: expressroute
ms.topic: article
ms.date: 11/28/2018
ms.author: anzaman
ms.custom: seodec18
ms.openlocfilehash: e94ba2f7f6981355f4736683b891fbdced001d03
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/22/2019
ms.locfileid: "54425335"
---
# <a name="reset-a-failed-expressroute-circuit"></a>Resetování okruhu ExpressRoute, který selhal

Při operaci v okruhu ExpressRoute nepodaří úspěšně vyřídit, okruh může přejít do stavu "selhání". Tento článek pomáhá resetování okruhu Azure ExpressRoute, který selhal.

## <a name="reset-a-circuit"></a>Resetování okruhu

1. Nainstalujte nejnovější verzi rutin PowerShellu pro Azure Resource Manager. Další informace najdete v článku [Instalace a konfigurace Azure PowerShellu](/powershell/azure/azurerm/install-azurerm-ps).

2. Otevřete konzolu PowerShellu se zvýšenými oprávněními a připojte se ke svému účtu. Připojení vám usnadní následující ukázka:

  ```azurepowershell-interactive
  Connect-AzureRmAccount
  ```
3. Pokud máte více předplatných Azure, zkontrolujte předplatná pro daný účet.

  ```azurepowershell-interactive
  Get-AzureRmSubscription
  ```
4. Určete předplatné, které chcete použít.

  ```azurepowershell-interactive
  Select-AzureRmSubscription -SubscriptionName "Replace_with_your_subscription_name"
  ```
5. Spusťte následující příkazy k resetování okruhu, který je v chybovém stavu:

  ```azurepowershell-interactive
  $ckt = Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

  Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt
  ```

Okruh by měl být nyní jsou v pořádku. Vytvořit lístek podpory s [podpory Microsoftu](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) Pokud okruh je stále v chybovém stavu.

## <a name="next-steps"></a>Další postup

Vytvořit lístek podpory s [podpory Microsoftu](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) Pokud stále dochází k problémům.
