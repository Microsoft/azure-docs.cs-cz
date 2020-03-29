---
title: Přepsání hlaviček HTTP v bráně Azure Application Gateway
description: Tento článek obsahuje informace o tom, jak přepsat hlavičky HTTP v Azure Application Gateway pomocí Azure PowerShellu
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 04/12/2019
ms.author: absha
ms.openlocfilehash: 47fe6a5247622e3ad3b3720955068580e0329913
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "64947188"
---
# <a name="rewrite-http-request-and-response-headers-with-azure-application-gateway---azure-powershell"></a>Přepisování hlaviček požadavků HTTP a odpovědí pomocí Azure Application Gateway – Azure PowerShell

Tento článek popisuje, jak pomocí Azure PowerShellu nakonfigurovat instanci [sku soupoložkou aplikační brány v2](<https://docs.microsoft.com/azure/application-gateway/application-gateway-autoscaling-zone-redundant>) k přepsání hlaviček HTTP v požadavcích a odpovědích.

Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) než začnete.

## <a name="before-you-begin"></a>Než začnete

- K dokončení kroků v tomto článku je potřeba spustit Azure PowerShell místně. Musíte mít také nainstalován modul Az verze 1.0.0 nebo novější. `Import-Module Az` Spusťte `Get-Module Az` a potom určete verzi, kterou jste nainstalovali. Pokud potřebujete upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps). Po ověření verze PowerShellu spusťte příkaz `Login-AzAccount`, abyste vytvořili připojení k Azure.
- Musíte mít instanci sku brány aplikace v2. Přepisování záhlaví není podporováno ve skladové jednotce v1. Pokud nemáte skladovou položku v2, vytvořte před zahájením instanci [sku brány aplikace v2.](https://docs.microsoft.com/azure/application-gateway/tutorial-autoscale-ps)

## <a name="create-required-objects"></a>Vytvoření požadovaných objektů

Chcete-li nakonfigurovat přepsání hlavičky HTTP, je třeba provést tyto kroky.

1. Vytvořte objekty, které jsou požadovány pro přepsání hlavičky HTTP:

   - **RequestHeaderConfiguration**: Slouží k určení polí hlavičky požadavku, která chcete přepsat, a nové hodnoty pro záhlaví.

   - **ResponseHeaderConfiguration**: Slouží k určení polí hlavičky odpovědi, která chcete přepsat, a nové hodnoty záhlaví.

   - **ActionSet**: Obsahuje konfigurace hlavičky požadavku a odpovědi zadané dříve.

   - **Podmínka**: Volitelná konfigurace. Podmínky přepsání vyhodnocují obsah požadavků http(S) a odpovědí. Akce přepsání dojde, pokud požadavek HTTP(S) nebo odpověď odpovídá podmínce přepsání.

     Pokud přidružíte více než jednu podmínku k akci, akce nastane pouze v případě, že jsou splněny všechny podmínky. Jinými slovy, operace je logická operace AND.

   - **Přepsání:** Obsahuje více kombinací rewrite akce / přepsání podmínek.

   - **RuleSequence**: Volitelná konfigurace, která pomáhá určit pořadí, ve kterém se pravidla přepisusí spouštějí. Tato konfigurace je užitečná, pokud máte více pravidel pro přepsání v sadě přepsání. Pravidlo přepsání, které má nižší hodnotu pořadí pravidel, je spuštěno jako první. Pokud přiřadíte stejnou hodnotu pořadí pravidel dvěma pravidlům pro přepsání, pořadí provádění je nedeterministické.

     Pokud explicitně nezadáte RuleSequence, je nastavena výchozí hodnota 100.

   - **RewriteRuleSet**: Obsahuje více pravidel přepsání, která budou přidružena k pravidlu směrování požadavku.

2. Připojte rewriteRuleSet k pravidlu směrování. Konfigurace přepsání je připojena ke zdrojovému naslouchací procesu prostřednictvím pravidla směrování. Při použití základního pravidla směrování je konfigurace přepsání záhlaví přidružena ke zdrojovému naslouchací procesu a je přepsáním globální hlavičky. Při použití pravidla směrování založeného na cestě je konfigurace přepsání záhlaví definována na mapě cesty URL. V takovém případě se vztahuje pouze na konkrétní oblast cesty lokality.

Můžete vytvořit více sad přepisů hlaviček PROTOKOLU HTTP a použít každou sadu přepsání na více naslouchacích procesech. Ale můžete použít pouze jeden přepsání nastavit na konkrétní naslouchací proces.

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

```azurepowershell
Connect-AzAccount
Select-AzSubscription -Subscription "<sub name>"
```

## <a name="specify-the-http-header-rewrite-rule-configuration"></a>Určení konfigurace pravidla přepisu záhlaví PROTOKOLU HTTP

V tomto příkladu změníme adresu URL přesměrování přepsáním hlavičky umístění v odpovědi HTTP vždy, když záhlaví umístění obsahuje odkaz na azurewebsites.net. Chcete-li to provést, přidáme podmínku k vyhodnocení, zda záhlaví umístění v odpovědi obsahuje azurewebsites.net. Použijeme vzor. `(https?):\/\/.*azurewebsites\.net(.*)$` A použijeme `{http_resp_Location_1}://contoso.com{http_resp_Location_2}` jako hodnotu záhlaví. Tato hodnota nahradí *azurewebsites.net* *contoso.com* v záhlaví umístění.

```azurepowershell
$responseHeaderConfiguration = New-AzApplicationGatewayRewriteRuleHeaderConfiguration -HeaderName "Location" -HeaderValue "{http_resp_Location_1}://contoso.com{http_resp_Location_2}"
$actionSet = New-AzApplicationGatewayRewriteRuleActionSet -RequestHeaderConfiguration $requestHeaderConfiguration -ResponseHeaderConfiguration $responseHeaderConfiguration
$condition = New-AzApplicationGatewayRewriteRuleCondition -Variable "http_resp_Location" -Pattern "(https?):\/\/.*azurewebsites\.net(.*)$" -IgnoreCase
$rewriteRule = New-AzApplicationGatewayRewriteRule -Name LocationHeader -ActionSet $actionSet
$rewriteRuleSet = New-AzApplicationGatewayRewriteRuleSet -Name LocationHeaderRewrite -RewriteRule $rewriteRule
```

## <a name="retrieve-the-configuration-of-your-application-gateway"></a>Načtení konfigurace aplikační brány

```azurepowershell
$appgw = Get-AzApplicationGateway -Name "AutoscalingAppGw" -ResourceGroupName "<rg name>"
```

## <a name="retrieve-the-configuration-of-your-request-routing-rule"></a>Načtení konfigurace pravidla směrování požadavku

```azurepowershell
$reqRoutingRule = Get-AzApplicationGatewayRequestRoutingRule -Name rule1 -ApplicationGateway $appgw
```

## <a name="update-the-application-gateway-with-the-configuration-for-rewriting-http-headers"></a>Aktualizace aplikační brány pomocí konfigurace pro přepisování hlaviček HTTP

```azurepowershell
Add-AzApplicationGatewayRewriteRuleSet -ApplicationGateway $appgw -Name LocationHeaderRewrite -RewriteRule $rewriteRuleSet.RewriteRules
Set-AzApplicationGatewayRequestRoutingRule -ApplicationGateway $appgw -Name rule1 -RuleType $reqRoutingRule.RuleType -BackendHttpSettingsId $reqRoutingRule.BackendHttpSettings.Id -HttpListenerId $reqRoutingRule.HttpListener.Id -BackendAddressPoolId $reqRoutingRule.BackendAddressPool.Id -RewriteRuleSetId $rewriteRuleSet.Id
Set-AzApplicationGateway -ApplicationGateway $appgw
```

## <a name="delete-a-rewrite-rule"></a>Odstranění pravidla přepisu

```azurepowershell
$appgw = Get-AzApplicationGateway -Name "AutoscalingAppGw" -ResourceGroupName "<rg name>"
Remove-AzApplicationGatewayRewriteRuleSet -Name "LocationHeaderRewrite" -ApplicationGateway $appgw
$requestroutingrule= Get-AzApplicationGatewayRequestRoutingRule -Name "rule1" -ApplicationGateway $appgw
$requestroutingrule.RewriteRuleSet= $null
set-AzApplicationGateway -ApplicationGateway $appgw
```

## <a name="next-steps"></a>Další kroky

Další informace o nastavení některých běžných případů použití naleznete v [běžných scénářích přepsání záhlaví](https://docs.microsoft.com/azure/application-gateway/rewrite-http-headers).