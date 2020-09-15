---
title: Kurz – konfigurace geografického filtrování zásad WAF – přední dveře Azure
description: V tomto kurzu se naučíte vytvořit zásadu geografického filtrování a přidružit ji k vašemu stávajícímu hostiteli front-endu.
services: frontdoor
documentationcenter: ''
author: duongau
ms.service: frontdoor
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 09/14/2020
ms.author: duau
ms.openlocfilehash: 20aa038e15b1ae5734ad6f463c6f450368617119
ms.sourcegitcommit: 07166a1ff8bd23f5e1c49d4fd12badbca5ebd19c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/15/2020
ms.locfileid: "90090022"
---
# <a name="tutorial-how-to-set-up-a-geo-filtering-waf-policy-for-your-front-door"></a>Kurz: jak nastavit geograficky filtrovací WAF zásady pro vaše přední dveře
V tomto kurzu se dozvíte, jak pomocí Azure PowerShellu vytvořit ukázkovou zásadu geografického filtrování a přidružit ji k existujícímu hostiteli front-endu služby Front Door. Tato ukázková zásada geografického filtrování bude blokovat žádosti ze všech ostatních zemí nebo oblastí kromě USA.

V tomto kurzu se naučíte:
> [!div class="checklist"]
> - Definujte podmínku shody geografického filtrování.
> - Přidejte podmínku shody geografického filtrování do pravidla.
> - Přidejte pravidla do zásady.
> - Připojte zásady WAF k hostiteli FrontDoor front-endu.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Požadavky
* Než začnete s nastavením zásad geografického filtru, nastavte prostředí PowerShell a vytvořte profil front-dveří.
* Vytvořte si přední dveře podle pokynů popsaných v tématu [rychlý Start: vytvoření profilu front-dveří](quickstart-create-front-door.md).

## <a name="define-geo-filtering-match-condition"></a>Definovat podmínku shody geografického filtrování

Vytvořte podmínku pro porovnávání vzorků, která vybere žádosti nepocházející z "US" pomocí příkazu [New-AzFrontDoorWafMatchConditionObject](/powershell/module/az.frontdoor/new-azfrontdoorwafmatchconditionobject) u parametrů při vytváření podmínky shody. Pro mapování země nebo oblasti se [tady zobrazí](front-door-geo-filtering.md)dvě písmena.

```azurepowershell-interactive
$nonUSGeoMatchCondition = New-AzFrontDoorWafMatchConditionObject `
-MatchVariable RemoteAddr `
-OperatorProperty GeoMatch `
-NegateCondition $true `
-MatchValue "US"
```
## <a name="add-geo-filtering-match-condition-to-a-rule-with-action-and-priority"></a>Přidání podmínky shody geografického filtrování do pravidla s parametry Action a Priority

Vytvořte objekt CustomRule `nonUSBlockRule` na základě podmínky shody, akce a priority pomocí [New-AzFrontDoorWafCustomRuleObject](/powershell/module/az.frontdoor/new-azfrontdoorwafcustomruleobject).  Objekt CustomRule může mít více podmínek shody MatchCondition.  V tomto příkladu se parametr Action nastaví na Block a parametr Priority na 1, což je nejvyšší priorita.

```
$nonUSBlockRule = New-AzFrontDoorWafCustomRuleObject `
-Name "geoFilterRule" `
-RuleType MatchRule `
-MatchCondition $nonUSGeoMatchCondition `
-Action Block `
-Priority 1
```
## <a name="add-rules-to-a-policy"></a>Přidání pravidel do zásad
Vyhledejte název skupiny prostředků, která obsahuje profil front-dveří pomocí `Get-AzResourceGroup` . V dalším kroku vytvořte `geoPolicy` objekt zásad obsahující `nonUSBlockRule`  pomocí [New-AzFrontDoorWafPolicy](/powershell/module/az.frontdoor/new-azfrontdoorwafpolicy) v zadané skupině prostředků, která obsahuje profil front-dveří. Je nutné zadat jedinečný název pro zásady geografického filtrování. 

Následující příklad používá název skupiny prostředků *FrontDoorQS_rg0* s předpokladem, že jste vytvořili profil front-dveří pomocí pokynů uvedených v [rychlém startu: vytvořit článek na předních dveřích](quickstart-create-front-door.md) . V následujícím příkladu nahraďte název zásady *geoPolicyAllowUSOnly* jedinečným názvem zásady.

```
$geoPolicy = New-AzFrontDoorWafPolicy `
-Name "geoPolicyAllowUSOnly" `
-resourceGroupName FrontDoorQS_rg0 `
-Customrule $nonUSBlockRule  `
-Mode Prevention `
-EnabledState Enabled
```
## <a name="link-waf-policy-to-a-front-door-frontend-host"></a>Propojit zásady WAF s hostitelskou frontou front-endu
Propojte objekt zásad WAF s existujícím hostitelem front-endu front-endu a aktualizujte vlastnosti front-dveří. 

Uděláte to tak, že nejdřív načtěte objekt přední dveře pomocí [Get-AzFrontDoor](/powershell/module/az.frontdoor/get-azfrontdoor). 

```
$geoFrontDoorObjectExample = Get-AzFrontDoor -ResourceGroupName FrontDoorQS_rg0
$geoFrontDoorObjectExample[0].FrontendEndpoints[0].WebApplicationFirewallPolicyLink = $geoPolicy.Id
```
Dále nastavte vlastnost front-end WebApplicationFirewallPolicyLink na hodnotu resourceId `geoPolicy` pomocí [set-AzFrontDoor](/powershell/module/az.frontdoor/set-azfrontdoor).

```
Set-AzFrontDoor -InputObject $geoFrontDoorObjectExample[0]
```

> [!NOTE] 
> Stačí nastavit vlastnost WebApplicationFirewallPolicyLink jenom jednou, aby se propojí zásada WAF k hostiteli front-endu front-endu. Další aktualizace zásad se automaticky aplikují na hostitele front-endu.

## <a name="clean-up-resources"></a>Vyčištění prostředků

V předchozích krocích jste nakonfigurovali pravidlo geografického filtrování, které je přidružené k zásadám WAF. Pak jste tyto zásady propojili s hostitelem front-endu vaší přední dveře. Pokud už nepotřebujete pravidlo geografického filtrování nebo zásadu WAF, musíte nejdřív zrušit přidružení zásady od hostitele front-endu, aby bylo možné odstranit zásady WAF.

:::image type="content" source="media/front-door-geo-filtering/front-door-disassociate-policy.png" alt-text="Zrušení přidružení zásady WAF":::

## <a name="next-steps"></a>Další kroky

Pokud se chcete dozvědět, jak nakonfigurovat Firewall webových aplikací pro vaše přední dveře, přejděte k dalšímu kurzu.

> [!div class="nextstepaction"]
> [Firewall webových aplikací a Front Door](front-door-waf.md)
