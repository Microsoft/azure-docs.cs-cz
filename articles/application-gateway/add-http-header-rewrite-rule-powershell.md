---
title: Přepsání hlavičky protokolu HTTP ve službě Azure Application Gateway
description: Tento článek obsahuje informace o tom, jak přepsat hlavičky protokolu HTTP ve službě Azure Application Gateway pomocí Azure Powershellu
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 04/12/2019
ms.author: absha
ms.openlocfilehash: bfafc74cbcb97f28cc085196a2cbaf4e9bf2e871
ms.sourcegitcommit: 031e4165a1767c00bb5365ce9b2a189c8b69d4c0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/13/2019
ms.locfileid: "59548315"
---
# <a name="rewrite-http-request-and-response-headers-with-azure-application-gateway---azure-powershell"></a>Přepište hlaviček žádostí a odpovědí HTTP pomocí Azure Application Gateway – Azure PowerShell

V tomto článku se dozvíte, jak pomocí Azure Powershellu ke konfiguraci [SKU v2 Application Gateway](<https://docs.microsoft.com/azure/application-gateway/application-gateway-autoscaling-zone-redundant>) přepsání hlavičky protokolu HTTP v požadavky a odpovědi.

> [!IMPORTANT]
> Skladová položka automaticky škálované a zónově redundantní Application Gateway je aktuálně ve verzi Public Preview. Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje pro úlohy v produkčním prostředí. Některé funkce nemusí být podporované nebo můžou mít omezené možnosti. Podrobnosti najdete v [dodatečných podmínkách použití systémů Microsoft Azure Preview](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

- Tento kurz vyžaduje místní použití Azure PowerShellu. Musí mít Az modulu verze 1.0.0 nebo novější. Spustit `Import-Module Az` a potom`Get-Module Az` k vyhledání verze. Pokud potřebujete upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps). Po ověření verze PowerShellu spusťte příkaz `Login-AzAccount`, abyste vytvořili připojení k Azure.
- Musíte mít v2 s Application Gateway, skladovou Položku, protože možnost přepisování záhlaví není podporována pro v1 SKU. Pokud nemáte k dispozici v2 SKU, vytvořte [SKU v2 Application Gateway](https://docs.microsoft.com/azure/application-gateway/tutorial-autoscale-ps>) předtím, než začnete.

## <a name="what-is-required-to-rewrite-a-header"></a>Co je potřeba přepsat hlavičku

Pokud chcete nakonfigurovat přepsání hlavičky protokolu HTTP, budete muset:

1. Vytvořte nové objekty požadované pro přepsání hlavičky protokolu http:

   - **RequestHeaderConfiguration**: Tento objekt se používá k určení, které chcete přepsat pole hlavičky požadavku a nové hodnoty, které musí být přepsány, aby původní záhlaví.

   - **ResponseHeaderConfiguration**: Tento objekt se používá k určení, které chcete přepsat pole hlavičky odpovědi a nové hodnoty, které musí být přepsány, aby původní záhlaví.

   - **ActionSet**: Tento objekt obsahuje konfigurace z výše uvedených hlaviček žádostí a odpovědí.

   - **Podmínka**: To je volitelné konfigurace. Když je přidána podmínka revize, se vyhodnotí obsahu žádosti HTTP (S) a odpovědi. Rozhodnutí o přepsání akci spojené s podmínkou revize se budou zakládat, zda požadavek HTTP (S) nebo odpověď shoda s podmínkou revize. 

     Pokud více než jedné podmínky jsou přidružené k akci a potom akci spustí jenom v případě, že jsou splněny všechny podmínky, to znamená, logické a operace se provede.

   - **RewriteRule**: obsahuje více akce přepsání – kombinace podmínku revize.

   - **RuleSequence**: Toto je volitelná konfigurace. Pomáhá určit pořadí, ve kterém pravidlech přepisování různých provedou. To je užitečné, pokud existuje více pravidla pro přepis adres v sadě revize. Pravidlo pro přepis adres s nižší hodnotou pořadí pravidlo se nejprve provede. Pokud poskytují stejnou sekvenci pravidlo na dvě pravidla pro přepis adres budou pořadí provádění není deterministický.

     Pokud explicitně neurčíte RuleSequence, nastaví se výchozí hodnota je 100.

   - **RewriteRuleSet**: Tento objekt obsahuje více přepsání pravidla, která bude přidružen k pravidlo směrování požadavku.

2. Budete muset připojit rewriteRuleSet pomocí pravidel směrování. Je to proto, že konfigurace přepsání je připojen k zdroj naslouchací proces prostřednictvím pravidla směrování. Při použití základních pravidel směrování, konfigurace přepsání hlavičky je přidružený zdroj naslouchací proces a je globální záhlaví revize. Při použití pravidel směrování na základě cest, přepište Konfigurace hlavičky je definován na mapě cestu adresy URL. Ano platí jen pro konkrétní cesty oblasti lokality.

Můžete vytvořit více sad přepsání hlavičky protokolu http a každou sadu přepsání lze použít na víc naslouchacích procesů. Můžete však použít jen jeden přepsat nastavenou na konkrétním posluchačem.

## <a name="sign-in-to-azure"></a>Přihlásit se k Azure

```azurepowershell
Connect-AzAccount
Select-AzSubscription -Subscription "<sub name>"
```

## <a name="specify-your-http-header-rewrite-rule-configuration"></a>**Zadat záhlaví http přepsat konfiguraci pravidel**

V tomto příkladu upravíme přesměrovací adresu URL pomocí přepsání hlavičky location v odpovědi http pokaždé, když se hlavička umístění obsahuje odkaz na "azurewebsites.net". Chcete-li to provést, přidáme podmínku k vyhodnocení, jestli hlavičky location v odpovědi obsahuje azurewebsites.net pomocí vzoru `(https?):\/\/.*azurewebsites\.net(.*)$`. Použijeme `{http_resp_Location_1}://contoso.com{http_resp_Location_2}` jako hodnotu záhlaví. Tato operace nahradí *azurewebsites.net* s *contoso.com* hlavičky location.

```azurepowershell
$responseHeaderConfiguration = New-AzApplicationGatewayRewriteRuleHeaderConfiguration -HeaderName "Location" -HeaderValue "{http_resp_Location_1}://contoso.com{http_resp_Location_2}"
$actionSet = New-AzApplicationGatewayRewriteRuleActionSet -RequestHeaderConfiguration $requestHeaderConfiguration -ResponseHeaderConfiguration $responseHeaderConfiguration
$condition = New-AzApplicationGatewayRewriteRuleCondition -Variable "http_resp_Location" -Pattern "(https?):\/\/.*azurewebsites\.net(.*)$" -IgnoreCase
$rewriteRule = New-AzApplicationGatewayRewriteRule -Name LocationHeader -ActionSet $actionSet
$rewriteRuleSet = New-AzApplicationGatewayRewriteRuleSet -Name LocationHeaderRewrite -RewriteRule $rewriteRule
```

## <a name="retrieve-configuration-of-your-existing-application-gateway"></a>Načíst konfiguraci vaší existující aplikační bráně

```azurepowershell
$appgw = Get-AzApplicationGateway -Name "AutoscalingAppGw" -ResourceGroupName "<rg name>"
```

## <a name="retrieve-configuration-of-your-existing-request-routing-rule"></a>Načtení konfigurace stávající pravidlo směrování požadavku

```azurepowershell
$reqRoutingRule = Get-AzApplicationGatewayRequestRoutingRule -Name rule1 -ApplicationGateway $appgw
```

## <a name="update-the-application-gateway-with-the-configuration-for-rewriting-http-headers"></a>Aktualizovat application gateway s konfigurací pro přepsání hlavičky protokolu http

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

Další informace o konfiguraci potřebných k provedení některé nejběžnější případy použití, naleznete v tématu [společné hlavičky revize scénáře](https://docs.microsoft.com/azure/application-gateway/rewrite-http-headers).