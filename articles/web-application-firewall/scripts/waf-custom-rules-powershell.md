---
title: Ukázka skriptu Prostředí Azure – vytvoření vlastních pravidel WAF
description: Ukázka skriptu Azure PowerShell – vytvoření brány firewall webové aplikace na vlastních pravidlech brány aplikace
author: vhorne
ms.service: web-application-firewall
services: web-application-firewall
ms.topic: sample
ms.date: 09/30/2019
ms.author: victorh
ms.openlocfilehash: 950f71c284268a9aa2773eb57213e266622d85bd
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "73501572"
---
# <a name="create-waf-custom-rules-with-azure-powershell"></a>Vytváření vlastních pravidel WAF pomocí Azure PowerShellu

Tento skript vytvoří bránu firewall webové aplikace brány aplikace, která používá vlastní pravidla. Vlastní pravidlo blokuje provoz, pokud hlavička požadavku obsahuje user-agent *evilbot*.

## <a name="prerequisites"></a>Požadavky

### <a name="azure-powershell-module"></a>Modul Azure PowerShellu

Pokud se rozhodnete nainstalovat a používat Azure PowerShell místně, tento skript vyžaduje modul Azure PowerShell verze 2.1.0 nebo novější.

1. Verzi zjistíte spuštěním příkazu `Get-Module -ListAvailable Az`. Pokud potřebujete upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/install-az-ps).
2. Chcete-li vytvořit připojení `Connect-AzAccount`k Azure, spusťte .

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Ukázkový skript

[!code-powershell[main](../../../powershell_scripts/application-gateway/waf-rules/waf-custom-rules.ps1 "Custom WAF rules")]

## <a name="clean-up-deployment"></a>Vyčištění nasazení

Pokud chcete skupinu prostředků, aplikační bránu a všechny související prostředky odebrat, spusťte následující příkaz.

```powershell
Remove-AzResourceGroup -Name CustomRulesTest
```

## <a name="script-explanation"></a>Vysvětlení skriptu

Tento skript pomocí následujících příkazů vytvoří nasazení. Každá položka v tabulce odkazuje na příslušnou část dokumentace.

| Příkaz | Poznámky |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Vytvoří skupinu prostředků, ve které se ukládají všechny prostředky. |
| [Nová-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig) | Vytvoří konfiguraci podsítě. |
| [Nová virtuální síť Az](/powershell/module/az.network/new-azvirtualnetwork) | Vytvoří virtuální síť pomocí konfigurace podsítě. |
| [Nová adresa AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) | Vytvoří veřejnou IP adresu aplikační brány. |
| [Nová konfigurace AzApplicationGatewayIP](/powershell/module/az.network/new-azapplicationgatewayipconfiguration) | Vytvoří konfiguraci, která k aplikační bráně přidruží podsíť. |
| [Nová-AzApplicationGatewayFrontendIPConfig](/powershell/module/az.network/new-azapplicationgatewayfrontendipconfig) | Vytvoří konfiguraci, která k aplikační bráně přiřadí veřejnou IP adresu. |
| [Nový-AzApplicationGatewayFrontendPort](/powershell/module/az.network/new-azapplicationgatewayfrontendport) | Přiřadí port, který se bude používat k přístupu k aplikační bráně. |
| [New-AzApplicationGatewayBackendAddressPool](/powershell/module/az.network/new-azapplicationgatewaybackendaddresspool) | Vytvoří back-endový fond pro aplikační bránu. |
| [New-AzApplicationGatewayBackendHttpSettings](/powershell/module/az.network/new-azapplicationgatewaybackendhttpsetting) | Nakonfiguruje nastavení pro back-endový fond. |
| [Nový azaplikační httplistener](/powershell/module/az.network/new-azapplicationgatewayhttplistener) | Vytvoří naslouchací proces. |
| [New-AzApplicationGatewayRequestRoutingRule](/powershell/module/az.network/new-azapplicationgatewayrequestroutingrule) | Vytvoří pravidlo směrování. |
| [Nový-AzApplicationGatewaySku](/powershell/module/az.network/new-azapplicationgatewaysku) | Určí úroveň a kapacitu brány Application Gateway. |
| [Nová azaplikační brána](/powershell/module/az.network/new-azapplicationgateway) | Vytvoří aplikační bránu. |
|[Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Odebere skupinu prostředků a všechny prostředky, které obsahuje. |
|[Nová-AzApplicationGatewayAutoscaleKonfigurace](/powershell/module/az.network/New-AzApplicationGatewayAutoscaleConfiguration)|Vytvoří konfiguraci automatického škálování pro aplikační bránu.|
|[Proměnná New-AzApplicationGatewayFirewallMatch](/powershell/module/az.network/New-AzApplicationGatewayFirewallMatchVariable)|Vytvoří proměnnou shody pro podmínku brány firewall.|
|[Nový-AzApplicationGatewayFirewallCondition](/powershell/module/az.network/New-AzApplicationGatewayFirewallCondition)|Vytvoří podmínku shody pro vlastní pravidlo.|
|[Nové pravidlo AzApplicationGatewayFirewallCustomRule](/powershell/module/az.network/New-AzApplicationGatewayFirewallCustomRule)|Vytvoří nové vlastní pravidlo pro zásady brány firewall brány aplikace.|
|[New-AzApplicationGatewayFirewallPolicy](/powershell/module/az.network/New-AzApplicationGatewayFirewallPolicy)|Vytvoří zásadu brány firewall brány aplikace.|
|[Nová-AzApplicationGatewayWebApplicationFirewallKonfigurace](/powershell/module/az.network/New-AzApplicationGatewayWebApplicationFirewallConfiguration)|Vytvoří konfiguraci WAF pro aplikační bránu.|

## <a name="next-steps"></a>Další kroky

- Další informace o vlastních pravidlech WAF naleznete [v tématu Vlastní pravidla brány firewall webových aplikací](../ag/custom-waf-rules-overview.md)
- Další informace o modulu Azure PowerShellu najdete v [dokumentaci k Azure PowerShellu](/powershell/azure/overview).
