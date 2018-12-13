---
title: 'Obnovení se nezdařilo - okruhu ExpressRoute: prostředí PowerShell: Azure | Dokumentace Microsoftu'
description: Tento článek pomůže obnovit okruh ExpressRoute, který je ve stavu selhání.
services: expressroute
author: anzaman
ms.service: expressroute
ms.topic: article
ms.date: 11/28/2018
ms.author: anzaman
ms.custom: seodec18
ms.openlocfilehash: 7b88ba6e00cbec05263fe5bc8e795cda95beee04
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/08/2018
ms.locfileid: "53093661"
---
# <a name="reset-a-failed-expressroute-circuit"></a>Resetování okruhu ExpressRoute, který selhal

Při operaci v okruhu ExpressRoute nepodaří úspěšně vyřídit, okruh může přejít do stavu "selhání". Tento článek pomáhá resetování okruhu Azure ExpressRoute, který selhal.

## <a name="reset-a-circuit"></a>Resetování okruhu

1. Nainstalujte nejnovější verzi rutin PowerShellu pro Azure Resource Manager. Další informace najdete v článku [Instalace a konfigurace Azure PowerShellu](/powershell/azure/install-azurerm-ps).

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
