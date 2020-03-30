---
title: Konfigurace zásad brány firewall webové aplikace pro filtrování geografických filtrů pro službu Azure Front Door
description: V tomto kurzu se dozvíte, jak vytvořit zásady geografického filtrování a přidružit zásady k stávajícímu hostiteli front-endu front-endu
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.topic: conceptual
ms.date: 03/10/2020
ms.author: victorh
ms.reviewer: tyao
ms.openlocfilehash: abcef61d478eccb4e979b60eb845ac8d398a49f9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79135866"
---
# <a name="set-up-a-geo-filtering-waf-policy-for-your-front-door"></a>Nastavení zásad WAF pro filtrování pro přední dveře

V tomto kurzu se dozvíte, jak pomocí Azure PowerShellu vytvořit ukázkovou zásadu geografického filtrování a přidružit ji k existujícímu hostiteli front-endu služby Front Door. Tato ukázková zásada geografického filtrování bude blokovat požadavky ze všech ostatních zemí nebo oblastí s výjimkou Spojených států.

Pokud ještě nemáte předplatné Azure, vytvořte si teď [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Požadavky

Než začnete nastavovat zásady geografického filtru, nastavte prostředí Prostředí PowerShellu a vytvořte profil předních dveří.
### <a name="set-up-your-powershell-environment"></a>Nastavení prostředí PowerShell
Prostředí Azure PowerShell poskytuje sadu rutin, které ke správě vašich prostředků Azure využívají model [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview). 

[Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) můžete nainstalovat na místní počítač a používat v jakékoli relaci PowerShellu. Podle pokynů na stránce se přihlaste pomocí přihlašovacích údajů Azure a nainstalujte modul Az PowerShell.

#### <a name="connect-to-azure-with-an-interactive-dialog-for-sign-in"></a>Připojení k Azure pomocí interaktivního dialogového okna pro přihlášení

```
Install-Module -Name Az
Connect-AzAccount
```
Ujistěte se, že máte nainstalovanou aktuální verzi PowerShellGet. Spusťte následující příkaz a znovu otevřete PowerShell.

```
Install-Module PowerShellGet -Force -AllowClobber
``` 
#### <a name="install-azfrontdoor-module"></a>Instalace modulu Az.FrontDoor 

```
Install-Module -Name Az.FrontDoor
```

### <a name="create-a-front-door-profile"></a>Vytvoření profilu předních dveří

Vytvořte profil předních dveří podle pokynů popsaných v [úvodním panelu: Vytvoření profilu předních dveří](../../frontdoor/quickstart-create-front-door.md).

## <a name="define-geo-filtering-match-condition"></a>Definovat podmínku shody geografického filtrování

Vytvořte podmínku vzorové shody, která vybere požadavky, které nepocházejí z "US" pomocí [New-AzFrontDoorWafMatchConditionObject](/powershell/module/az.frontdoor/new-azfrontdoorwafmatchconditionobject) na parametry při vytváření podmínky shody. Dva kódy zemí s písmeny k mapování zemí jsou uvedeny v části [Co je geografické filtrování v doméně pro Azure Front Door?](waf-front-door-geo-filtering.md).

```azurepowershell-interactive
$nonUSGeoMatchCondition = New-AzFrontDoorWafMatchConditionObject `
-MatchVariable RemoteAddr `
-OperatorProperty GeoMatch `
-NegateCondition $true `
-MatchValue "US"
```
 
## <a name="add-geo-filtering-match-condition-to-a-rule-with-action-and-priority"></a>Přidání podmínky shody geografického filtrování do pravidla s parametry Action a Priority

Vytvořte objekt `nonUSBlockRule` CustomRule na základě podmínky shody, akce a priority pomocí [new-azFrontDoorWafCustomRuleObject](/powershell/module/az.frontdoor/new-azfrontdoorwafcustomruleobject).  Objekt CustomRule může mít více podmínek shody MatchCondition.  V tomto příkladu se parametr Action nastaví na Block a parametr Priority na 1, což je nejvyšší priorita.

```
$nonUSBlockRule = New-AzFrontDoorWafCustomRuleObject `
-Name "geoFilterRule" `
-RuleType MatchRule `
-MatchCondition $nonUSGeoMatchCondition `
-Action Block `
-Priority 1
```

## <a name="add-rules-to-a-policy"></a>Přidání pravidel do zásady

Vyhledejte název skupiny prostředků, která obsahuje `Get-AzResourceGroup`profil Přední dveře pomocí aplikace . Dále vytvořte `geoPolicy` objekt zásad `nonUSBlockRule` obsahující pomocí [New-AzFrontDoorWafPolicy](/powershell/module/az.frontdoor/new-azfrontdoorwafpolicy) v zadané skupině prostředků, která obsahuje profil Front Door. Je nutné zadat jedinečný název zásad zeměpisné oblasti. 

Následující příklad používá název skupiny prostředků *myResourceGroupFD1* s předpokladem, že jste vytvořili profil Přední dveře podle pokynů uvedených v [článku Rychlý start: Vytvoření předních dveří.](../../frontdoor/quickstart-create-front-door.md) V níže uvedeném příkladu nahraďte název *zásady geoPolicyAllowUSOnly* jedinečným názvem zásad.

```
$geoPolicy = New-AzFrontDoorWafPolicy `
-Name "geoPolicyAllowUSOnly" `
-resourceGroupName myResourceGroupFD1 `
-Customrule $nonUSBlockRule  `
-Mode Prevention `
-EnabledState Enabled
```

## <a name="link-waf-policy-to-a-front-door-frontend-host"></a>Propojení zásad WAF s hostitelem frontendů front-endu

Propojte objekt zásad WAF s existujícím hostitelem front-endu front-end u vchodu a aktualizujte vlastnosti předních dveří. 

Chcete-li tak učinit, nejprve načíst objekt přední dveře pomocí [Get-AzFrontDoor](/powershell/module/az.frontdoor/get-azfrontdoor). 

```
$geoFrontDoorObjectExample = Get-AzFrontDoor -ResourceGroupName myResourceGroupFD1
$geoFrontDoorObjectExample[0].FrontendEndpoints[0].WebApplicationFirewallPolicyLink = $geoPolicy.Id
```

Dále nastavte vlastnost frontend WebApplicationFirewallPolicyLink na id `geoPolicy`resourceId using [Set-AzFrontDoor](/powershell/module/az.frontdoor/set-azfrontdoor).

```
Set-AzFrontDoor -InputObject $geoFrontDoorObjectExample[0]
```

> [!NOTE] 
> Chcete-li propojit zásadu WAF s front-endem front-endového hostitele, stačí nastavit vlastnost WebApplicationFirewallPolicyLink pouze jednou. Následné aktualizace zásad jsou automaticky použity na hostitele front-endu.

## <a name="next-steps"></a>Další kroky

- Přečtěte si o [bráně firewall webových aplikací Azure](../overview.md).
- Přečtěte si, jak [vytvořit Front Door](../../frontdoor/quickstart-create-front-door.md).
