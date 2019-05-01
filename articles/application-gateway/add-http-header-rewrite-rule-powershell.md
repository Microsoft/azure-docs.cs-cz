---
title: Přepsání hlavičky protokolu HTTP ve službě Azure Application Gateway
description: Tento článek obsahuje informace o tom, jak přepsat hlavičky protokolu HTTP ve službě Azure Application Gateway pomocí Azure Powershellu
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 04/12/2019
ms.author: absha
ms.openlocfilehash: 47fe6a5247622e3ad3b3720955068580e0329913
ms.sourcegitcommit: ed66a704d8e2990df8aa160921b9b69d65c1d887
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/30/2019
ms.locfileid: "64947188"
---
# <a name="rewrite-http-request-and-response-headers-with-azure-application-gateway---azure-powershell"></a>Přepište hlaviček žádostí a odpovědí HTTP pomocí Azure Application Gateway – Azure PowerShell

Tento článek popisuje, jak pomocí Azure Powershellu ke konfiguraci [SKU v2 Application Gateway](<https://docs.microsoft.com/azure/application-gateway/application-gateway-autoscaling-zone-redundant>) instance přepsání hlavičky protokolu HTTP v požadavky a odpovědi.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="before-you-begin"></a>Než začnete

- Budete muset spustit prostředí Azure PowerShell místně, k dokončení kroků v tomto článku. Budete také potřebovat Az modulu verze 1.0.0 nebo novější. Spustit `Import-Module Az` a potom `Get-Module Az` určit verzi, kterou jste nainstalovali. Pokud potřebujete upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps). Po ověření verze PowerShellu spusťte příkaz `Login-AzAccount`, abyste vytvořili připojení k Azure.
- Musíte mít instanci SKU v2 Application Gateway. Přepsání hlavičky se nepodporuje v v1 SKU. Pokud nemáte k dispozici v2 SKU, vytvořte [SKU v2 Application Gateway](https://docs.microsoft.com/azure/application-gateway/tutorial-autoscale-ps) instance před zahájením.

## <a name="create-required-objects"></a>Vytvoření požadovaných objektů

Pokud chcete nakonfigurovat přepsání hlavičky protokolu HTTP, budete muset dokončit tyto kroky.

1. Vytvořte objekty, které jsou požadovány pro přepsání hlavičky protokolu HTTP:

   - **RequestHeaderConfiguration**: Slouží k určení, které chcete přepsat pole hlavičky požadavku a novou hodnotu hlavičky.

   - **ResponseHeaderConfiguration**: Slouží k určení, které chcete přepsat pole hlavičky odpovědi a novou hodnotu hlavičky.

   - **ActionSet**: Obsahuje konfigurace hlaviček žádostí a odpovědí zadali dřív.

   - **Podmínka**: Volitelná konfigurace. Přepište podmínky vyhodnotí obsahu žádosti HTTP (S) a odpovědi. Akce revize se vrátí požadavku HTTP (S) nebo odpovědi odpovídá podmínce revize.

     Pokud přiřadíte více než jednu podmínku akci, dojde k akci pouze v případě, že jsou splněné všechny podmínky. Operace je jinými slovy, logické a operace.

   - **RewriteRule**: Obsahuje více akce přepsání / přepisování kombinace podmínku.

   - **RuleSequence**: Volitelná konfigurace, která pomáhá určit pořadí, ve kterém pravidla pro přepis adres spustit. Tato konfigurace je užitečný, pokud máte více pravidla pro přepis adres v sadě revize. Pravidlo pro přepis adres, který má menší hodnotu pořadí pravidlo spustí první. Pokud přiřadíte stejnou hodnotu pořadí pravidlo na dvě pravidla pro přepis, pořadí spouštění je Nedeterministický.

     Pokud nezadáte RuleSequence explicitně, je nastavena výchozí hodnota je 100.

   - **RewriteRuleSet**: Obsahuje více pravidla pro přepis adres, které budou přidružené pravidlo směrování požadavku.

2. Připojte RewriteRuleSet pravidel směrování. Konfigurace přepsání je připojen k zdroj naslouchací proces prostřednictvím pravidla směrování. Při použití základních pravidel směrování, konfigurace přepsání hlavičky souvisí s naslouchacím procesem zdroj a je globální záhlaví revize. Při použití pravidel směrování na základě cest, přepište Konfigurace hlavičky je definován na mapě cestu adresy URL. V takovém případě platí jenom pro konkrétní cesty oblasti lokality.

Můžete vytvořit více sad přepsání hlavičky protokolu HTTP a použije každé revize nastavena na víc naslouchacích procesů. Můžete ale použít pouze jeden přepsat nastavenou na konkrétní naslouchací proces.

## <a name="sign-in-to-azure"></a>Přihlásit se k Azure

```azurepowershell
Connect-AzAccount
Select-AzSubscription -Subscription "<sub name>"
```

## <a name="specify-the-http-header-rewrite-rule-configuration"></a>Zadejte hlavičku protokolu HTTP přepsat konfiguraci pravidel

V tomto příkladu upravíme adresy URL přesměrování pomocí přepsání hlavičky location v odpovědi HTTP pokaždé, když se hlavička umístění obsahuje odkaz na azurewebsites.net. Chcete-li to provést, přidáme podmínku k vyhodnocení, jestli hlavičky location v odpovědi obsahuje azurewebsites.net. Použijeme vzor `(https?):\/\/.*azurewebsites\.net(.*)$`. Využijeme `{http_resp_Location_1}://contoso.com{http_resp_Location_2}` jako hodnotu záhlaví. Tato hodnota nahradí *azurewebsites.net* s *contoso.com* hlavičky location.

```azurepowershell
$responseHeaderConfiguration = New-AzApplicationGatewayRewriteRuleHeaderConfiguration -HeaderName "Location" -HeaderValue "{http_resp_Location_1}://contoso.com{http_resp_Location_2}"
$actionSet = New-AzApplicationGatewayRewriteRuleActionSet -RequestHeaderConfiguration $requestHeaderConfiguration -ResponseHeaderConfiguration $responseHeaderConfiguration
$condition = New-AzApplicationGatewayRewriteRuleCondition -Variable "http_resp_Location" -Pattern "(https?):\/\/.*azurewebsites\.net(.*)$" -IgnoreCase
$rewriteRule = New-AzApplicationGatewayRewriteRule -Name LocationHeader -ActionSet $actionSet
$rewriteRuleSet = New-AzApplicationGatewayRewriteRuleSet -Name LocationHeaderRewrite -RewriteRule $rewriteRule
```

## <a name="retrieve-the-configuration-of-your-application-gateway"></a>Načtení konfigurace služby application gateway

```azurepowershell
$appgw = Get-AzApplicationGateway -Name "AutoscalingAppGw" -ResourceGroupName "<rg name>"
```

## <a name="retrieve-the-configuration-of-your-request-routing-rule"></a>Načíst konfiguraci svoje pravidlo směrování požadavku

```azurepowershell
$reqRoutingRule = Get-AzApplicationGatewayRequestRoutingRule -Name rule1 -ApplicationGateway $appgw
```

## <a name="update-the-application-gateway-with-the-configuration-for-rewriting-http-headers"></a>Aktualizovat application gateway s konfigurací pro přepsání hlavičky protokolu HTTP

```azurepowershell
Add-AzApplicationGatewayRewriteRuleSet -ApplicationGateway $appgw -Name LocationHeaderRewrite -RewriteRule $rewriteRuleSet.RewriteRules
Set-AzApplicationGatewayRequestRoutingRule -ApplicationGateway $appgw -Name rule1 -RuleType $reqRoutingRule.RuleType -BackendHttpSettingsId $reqRoutingRule.BackendHttpSettings.Id -HttpListenerId $reqRoutingRule.HttpListener.Id -BackendAddressPoolId $reqRoutingRule.BackendAddressPool.Id -RewriteRuleSetId $rewriteRuleSet.Id
Set-AzApplicationGateway -ApplicationGateway $appgw
```

## <a name="delete-a-rewrite-rule"></a>Odstranit pravidlo pro přepis adres

```azurepowershell
$appgw = Get-AzApplicationGateway -Name "AutoscalingAppGw" -ResourceGroupName "<rg name>"
Remove-AzApplicationGatewayRewriteRuleSet -Name "LocationHeaderRewrite" -ApplicationGateway $appgw
$requestroutingrule= Get-AzApplicationGatewayRequestRoutingRule -Name "rule1" -ApplicationGateway $appgw
$requestroutingrule.RewriteRuleSet= $null
set-AzApplicationGateway -ApplicationGateway $appgw
```

## <a name="next-steps"></a>Další postup

Další informace o tom, jak nastavit některé běžné případy použití, naleznete v tématu [společné hlavičky revize scénáře](https://docs.microsoft.com/azure/application-gateway/rewrite-http-headers).