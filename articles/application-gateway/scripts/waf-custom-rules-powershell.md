---
title: Ukázka skriptu Azure PowerShell – vytvoření vlastních pravidel WAF
description: Ukázka skriptu Azure PowerShell – vytvoření vlastních pravidel brány firewall webových aplikací
author: vhorne
ms.service: application-gateway
ms.topic: sample
ms.date: 6/7/2019
ms.author: victorh
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 8bf19052c11301412a299c31a2cd73ed2f9ffc7d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "89079893"
---
# <a name="create-web-application-firewall-waf-custom-rules-with-azure-powershell"></a>Vytváření vlastních pravidel firewallu webových aplikací (WAF) pomocí Azure PowerShell

Tento skript vytvoří bránu firewall webových aplikací Application Gateway, která používá vlastní pravidla. Vlastní pravidlo blokuje provoz, pokud hlavička žádosti obsahuje User-Agent *evilbot*.

## <a name="prerequisites"></a>Požadavky

### <a name="azure-powershell-module"></a>Modul Azure PowerShellu

Pokud se rozhodnete nainstalovat a používat Azure PowerShell místně, vyžaduje tento skript verzi modulu Azure PowerShell 2.1.0 nebo novější.

1. Verzi zjistíte spuštěním příkazu `Get-Module -ListAvailable Az`. Pokud potřebujete upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/install-az-ps).
2. Pokud chcete vytvořit připojení k Azure, spusťte `Connect-AzAccount` .

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
| [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig) | Vytvoří konfiguraci podsítě. |
| [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) | Vytvoří virtuální síť pomocí konfigurace podsítě. |
| [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) | Vytvoří veřejnou IP adresu aplikační brány. |
| [New-AzApplicationGatewayIPConfiguration](/powershell/module/az.network/new-azapplicationgatewayipconfiguration) | Vytvoří konfiguraci, která k aplikační bráně přidruží podsíť. |
| [New-AzApplicationGatewayFrontendIPConfig](/powershell/module/az.network/new-azapplicationgatewayfrontendipconfig) | Vytvoří konfiguraci, která k aplikační bráně přiřadí veřejnou IP adresu. |
| [New-AzApplicationGatewayFrontendPort](/powershell/module/az.network/new-azapplicationgatewayfrontendport) | Přiřadí port, který se bude používat k přístupu k aplikační bráně. |
| [New-AzApplicationGatewayBackendAddressPool](/powershell/module/az.network/new-azapplicationgatewaybackendaddresspool) | Vytvoří back-endový fond pro aplikační bránu. |
| [New-AzApplicationGatewayBackendHttpSettings](/powershell/module/az.network/new-azapplicationgatewaybackendhttpsetting) | Nakonfiguruje nastavení pro back-endový fond. |
| [New-AzApplicationGatewayHttpListener](/powershell/module/az.network/new-azapplicationgatewayhttplistener) | Vytvoří naslouchací proces. |
| [New-AzApplicationGatewayRequestRoutingRule](/powershell/module/az.network/new-azapplicationgatewayrequestroutingrule) | Vytvoří pravidlo směrování. |
| [New-AzApplicationGatewaySku](/powershell/module/az.network/new-azapplicationgatewaysku) | Určí úroveň a kapacitu brány Application Gateway. |
| [New-AzApplicationGateway](/powershell/module/az.network/new-azapplicationgateway) | Vytvoří aplikační bránu. |
|[Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Odebere skupinu prostředků a všechny prostředky, které obsahuje. |
|[New-AzApplicationGatewayAutoscaleConfiguration](/powershell/module/az.network/New-AzApplicationGatewayAutoscaleConfiguration)|Vytvoří konfiguraci automatického škálování pro Application Gateway.|
|[New-AzApplicationGatewayFirewallMatchVariable](/powershell/module/az.network/New-AzApplicationGatewayFirewallMatchVariable)|Vytvoří proměnnou shody pro podmínku brány firewall.|
|[New-AzApplicationGatewayFirewallCondition](/powershell/module/az.network/New-AzApplicationGatewayFirewallCondition)|Vytvoří podmínku shody pro vlastní pravidlo.|
|[New-AzApplicationGatewayFirewallCustomRule](/powershell/module/az.network/New-AzApplicationGatewayFirewallCustomRule)|Vytvoří nové vlastní pravidlo pro zásady brány firewall služby Application Gateway.|
|[New-AzApplicationGatewayFirewallPolicy](/powershell/module/az.network/New-AzApplicationGatewayFirewallPolicy)|Vytvoří zásadu brány firewall aplikační brány.|
|[New-AzApplicationGatewayWebApplicationFirewallConfiguration](/powershell/module/az.network/New-AzApplicationGatewayWebApplicationFirewallConfiguration)|Vytvoří konfiguraci WAF pro aplikační bránu.|

## <a name="next-steps"></a>Další kroky

- Další informace o vlastních pravidlech WAF najdete v tématu [vlastní pravidla pro Firewall webových aplikací](../custom-waf-rules-overview.md) .
- Další informace o modulu Azure PowerShellu najdete v [dokumentaci k Azure PowerShellu](/powershell/azure/).
- Další ukázkové skripty PowerShellu pro aplikační brány najdete v [dokumentaci služby Azure Application Gateway](../powershell-samples.md).
