---
title: Přepsání hlavičky protokolu HTTP v existující aplikační bráně Azure
description: Tento článek obsahuje informace o tom, jak přepsat hlavičky protokolu HTTP v existující aplikační bráně Azure pomocí Azure Powershellu
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 12/20/2018
ms.author: absha
ms.openlocfilehash: 1044a29e30d664ae8bb88d55508756934d495c3e
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/22/2019
ms.locfileid: "54428151"
---
# <a name="rewrite-http-headers-in-an-existing-application-gateway"></a>Přepsání hlavičky protokolu HTTP v existující aplikační bráně

Prostředí Azure PowerShell můžete použít ke konfiguraci [pravidla pro přepsání hlavičky požadavku a odpovědi HTTP](rewrite-http-headers.md) v existujícím [automatické škálování a zónově redundantní služba application gateway SKU](https://docs.microsoft.com/azure/application-gateway/application-gateway-autoscaling-zone-redundant)

> [!IMPORTANT] 
> Skladová položka automaticky škálované a zónově redundantní Application Gateway je aktuálně ve verzi Public Preview. Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje pro úlohy v produkčním prostředí. Některé funkce nemusí být podporované nebo můžou mít omezené možnosti. Podrobnosti najdete v [dodatečných podmínkách použití systémů Microsoft Azure Preview](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). 

V tomto kurzu se naučíte:

> [!div class="checklist"]
>
> * Načtení konfigurace stávající aplikační brány
> * Zadat záhlaví http přepsat konfiguraci pravidel
> * Aktualizovat application gateway v konfiguraci uvedené výš pro přepsání hlavičky protokolu http

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Tento kurz vyžaduje místní použití Azure PowerShellu. Musí mít Az modulu verze 1.0.0 nebo novější. Spustit `Import-Module Az` a potom`Get-Module Az` k vyhledání verze. Pokud potřebujete upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](https://docs.microsoft.com/powershell/azure/azurerm/install-azurerm-ps). Po ověření verze PowerShellu spusťte příkaz `Login-AzAccount`, abyste vytvořili připojení k Azure.

## <a name="sign-in-to-azure"></a>Přihlásit se k Azure

```azurepowershell
Connect-AzAccount
Select-AzSubscription -Subscription "<sub name>"
```

## <a name="specify-your-http-header-rewrite-rule-configuration"></a>**Zadat záhlaví http přepsat konfiguraci pravidel**

Konfigurace nové objekty požadované pro přepsání hlavičky protokolu http:

- **RequestHeaderConfiguration**: Tento objekt se používá k určení, které chcete přepsat pole hlavičky požadavku a nové hodnoty, které musí být přepsány, aby původní záhlaví.
- **ResponseHeaderConfiguration**: Tento objekt se používá k určení, které chcete přepsat pole hlavičky odpovědi a nové hodnoty, které musí být přepsány, aby původní záhlaví.
- **ActionSet**: Tento objekt obsahuje konfigurace z výše uvedených hlaviček žádostí a odpovědí. 
- **RewriteRule**: Tento objekt obsahuje všechny *actionSets* uvedeno výše. 
- **RewriteRuleSet**– tento objekt obsahuje všechny *rewriteRules* a musí být připojené k pravidlo směrování požadavku – basic nebo na základě cest.

```azurepowershell
$requestHeaderConfiguration = New-AzApplicationGatewayRewriteRuleHeaderConfiguration -HeaderName "X-isThroughProxy" -HeaderValue "True"
$responseHeaderConfiguration = New-AzApplicationGatewayRewriteRuleHeaderConfiguration -HeaderName "Strict-Transport-Security" -HeaderValue "max-age=31536000"
$actionSet = New-AzApplicationGatewayRewriteRuleActionSet -RequestHeaderConfiguration $requestHeaderConfiguration -ResponseHeaderConfiguration $responseHeaderConfiguration    
$rewriteRule = New-AzApplicationGatewayRewriteRule -Name rewriteRule1 -ActionSet $actionSet    
$rewriteRuleSet = New-AzApplicationGatewayRewriteRuleSet -Name rewriteRuleSet1 -RewriteRule $rewriteRule
```

## <a name="retrieve-configuration-of-your-existing-application-gateway"></a>Načíst konfiguraci vaší existující aplikační bráně

```azurepowershell
$appgw1 = Get-AzApplicationGateway -Name "AutoscalingAppGw" -ResourceGroupName "<rg name>"
```

## <a name="retrieve-configuration-of-your-existing-request-routing-rule"></a>Načtení konfigurace stávající pravidlo směrování požadavku

```azurepowershell
$reqRoutingRule = Get-AzApplicationGatewayRequestRoutingRule -Name Rule1 -ApplicationGateway $appgw
```

## <a name="update-the-application-gateway-with-the-configuration-for-rewriting-http-headers"></a>Aktualizovat application gateway s konfigurací pro přepsání hlavičky protokolu http

```azurepowershell
$appgw1 = Add-AzApplicationGatewayRewriteRuleSet -ApplicationGateway $appgw -Name rewriteRuleSet1 -RewriteRule $rewriteRuleSet.RewriteRules
$appgw2 = Set-AzApplicationGatewayRequestRoutingRule -ApplicationGateway $appgw -Name rule1 -RuleType $reqRoutingRule.RuleType -BackendHttpSettingsId $reqRoutingRule.BackendHttpSettings.Id -HttpListenerId $reqRoutingRule.HttpListener.Id -BackendAddressPoolId $reqRoutingRule.BackendAddressPool.Id -RewriteRuleSetId $rewriteRuleSet.Id
$appgw3 =  Set-AzApplicationGateway -ApplicationGateway $appgw2
```

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Vytvoření aplikační brány s pravidly směrování založenými na cestě URL](./tutorial-url-route-powershell.md)
