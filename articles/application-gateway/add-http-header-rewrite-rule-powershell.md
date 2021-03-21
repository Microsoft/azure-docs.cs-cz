---
title: Přepis hlaviček protokolu HTTP v Azure Application Gateway
description: Tento článek poskytuje informace o tom, jak přepsat hlavičky HTTP v Azure Application Gateway pomocí Azure PowerShell
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: how-to
ms.date: 04/12/2019
ms.author: absha
ms.openlocfilehash: 29ca3aff7d75c7a14bf7b325719924936762d191
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "101711684"
---
# <a name="rewrite-http-request-and-response-headers-with-azure-application-gateway---azure-powershell"></a>Přepsání hlaviček požadavků a odpovědí HTTP pomocí Azure Application Gateway-Azure PowerShell

Tento článek popisuje, jak pomocí Azure PowerShell nakonfigurovat instanci [SKU Application Gateway v2](./application-gateway-autoscaling-zone-redundant.md) , aby v žádostech a odpovědích přepsala hlavičky HTTP.

Pokud ještě nemáte předplatné Azure, vytvořte si napřed [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="before-you-begin"></a>Než začnete

- Abyste mohli dokončit kroky v tomto článku, musíte spustit Azure PowerShell místně. Musíte také mít nainstalované AZ Module verze 1.0.0 nebo novější. Spusťte `Import-Module Az` a pak `Get-Module Az` Určete verzi, kterou jste nainstalovali. Pokud potřebujete upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/install-az-ps). Po ověření verze PowerShellu spusťte příkaz `Login-AzAccount`, abyste vytvořili připojení k Azure.
- Musíte mít instanci SKU Application Gateway v2. Přepisování hlaviček není v SKU v1 podporováno. Pokud nemáte SKU verze 2, vytvořte před zahájením instanci [sku Application Gateway v2](./tutorial-autoscale-ps.md) .

## <a name="create-required-objects"></a>Vytvořit požadované objekty

Chcete-li nakonfigurovat přepsání hlaviček protokolu HTTP, je nutné provést tyto kroky.

1. Vytvořte objekty, které jsou požadovány pro přepsání hlaviček protokolu HTTP:

   - **RequestHeaderConfiguration**: slouží k zadání polí hlavičky požadavku, která chcete přepsat, a nové hodnoty hlaviček.

   - **ResponseHeaderConfiguration**: slouží k zadání polí hlavičky odpovědi, která mají být přepsána, a nové hodnoty hlaviček.

   - **ActionSet**: obsahuje konfigurace dříve zadaných hlaviček požadavků a odpovědí.

   - **Podmínka**: volitelná konfigurace. Podmínky přepisu vyhodnocují obsah požadavků a odpovědí HTTP (S). Akce přepisu nastane, pokud požadavek nebo odpověď HTTP (S) odpovídá podmínce přepsání.

     Pokud k akci přiřadíte více než jednu podmínku, bude akce provedena pouze v případě, že jsou splněny všechny podmínky. Jinými slovy, operace je logická a operace.

   - **RewriteRule**: obsahuje více kombinací podmínka pro přepis a akci přepsání.

   - **RuleSequence**: volitelná konfigurace, která pomáhá určit pořadí, ve kterém se pravidla přepisu spouštějí. Tato konfigurace je užitečná, když máte více pravidel pro přepsání v sadě přepsání. Jako první se spustí pravidlo pro přepsání s nižší hodnotou pořadí pravidel. Pokud přiřadíte stejnou hodnotu pořadí pravidel pro dvě pravidla přepisu, pořadí spuštění je nedeterministické.

     Pokud nezadáte RuleSequence explicitně, je nastavena výchozí hodnota 100.

   - **RewriteRuleSet**: obsahuje více pravidel přepisu, která budou přidružena k pravidlu směrování požadavků.

2. Připojte RewriteRuleSet k pravidlu směrování. Konfigurace přepsání je připojena ke zdroji naslouchání prostřednictvím pravidla směrování. Použijete-li pravidlo základního směrování, je konfigurace přepsání hlaviček přidružena ke zdrojovému naslouchacího procesu a je přepsána globální hlavičkou. Když použijete pravidlo směrování na základě cesty, konfigurace opětovného zápisu hlaviček se definuje na mapě cesty URL. V takovém případě platí pouze pro konkrétní oblast cesty lokality.

Můžete vytvořit více sad pro přepsání hlaviček protokolu HTTP a použít každou sadu přepsání na více posluchačů. Můžete ale použít jenom jednu sadu přepsaného zápisu na konkrétní naslouchací proces.

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

```azurepowershell
Connect-AzAccount
Select-AzSubscription -Subscription "<sub name>"
```

## <a name="specify-the-http-header-rewrite-rule-configuration"></a>Zadejte konfiguraci pravidla přepisu hlaviček protokolu HTTP.

V tomto příkladu změníme adresu URL přesměrování tak, že přepíšeme hlavičku umístění v odpovědi HTTP vždy, když hlavička Location obsahuje odkaz na azurewebsites.net. K tomu přidáme podmínku pro vyhodnocení, zda hlavička umístění v odpovědi obsahuje azurewebsites.net. Použijeme tento vzor `(https?)://.*azurewebsites.net(.*)$` . A použijeme `{http_resp_Location_1}://contoso.com{http_resp_Location_2}` jako hodnotu hlavičky. Tato hodnota nahradí *azurewebsites.NET* hodnotou *contoso.com* v hlavičce umístění.

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

## <a name="retrieve-the-configuration-of-your-request-routing-rule"></a>Načtěte konfiguraci pravidla směrování žádostí.

```azurepowershell
$reqRoutingRule = Get-AzApplicationGatewayRequestRoutingRule -Name rule1 -ApplicationGateway $appgw
```

## <a name="update-the-application-gateway-with-the-configuration-for-rewriting-http-headers"></a>Aktualizace služby Application Gateway s konfigurací pro přepis hlaviček protokolu HTTP

```azurepowershell
Add-AzApplicationGatewayRewriteRuleSet -ApplicationGateway $appgw -Name LocationHeaderRewrite -RewriteRule $rewriteRuleSet.RewriteRules
Set-AzApplicationGatewayRequestRoutingRule -ApplicationGateway $appgw -Name rule1 -RuleType $reqRoutingRule.RuleType -BackendHttpSettingsId $reqRoutingRule.BackendHttpSettings.Id -HttpListenerId $reqRoutingRule.HttpListener.Id -BackendAddressPoolId $reqRoutingRule.BackendAddressPool.Id -RewriteRuleSetId $rewriteRuleSet.Id
Set-AzApplicationGateway -ApplicationGateway $appgw
```

## <a name="delete-a-rewrite-rule"></a>Odstranění pravidla přepsání

```azurepowershell
$appgw = Get-AzApplicationGateway -Name "AutoscalingAppGw" -ResourceGroupName "<rg name>"
Remove-AzApplicationGatewayRewriteRuleSet -Name "LocationHeaderRewrite" -ApplicationGateway $appgw
$requestroutingrule= Get-AzApplicationGatewayRequestRoutingRule -Name "rule1" -ApplicationGateway $appgw
$requestroutingrule.RewriteRuleSet= $null
set-AzApplicationGateway -ApplicationGateway $appgw
```

## <a name="next-steps"></a>Další kroky

Další informace o tom, jak nastavit některé běžné případy použití, najdete v tématu [běžné scénáře přepisování hlaviček](./rewrite-http-headers.md).