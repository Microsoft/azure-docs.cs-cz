---
title: Kurz – konfigurace geografického filtrování zásad WAF – přední dveře Azure
description: V tomto kurzu se naučíte, jak vytvořit zásadu geografického filtrování a přidružit ji k vašemu stávajícímu hostiteli front-endu.
services: frontdoor
documentationcenter: ''
author: duongau
ms.service: frontdoor
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/21/2019
ms.author: duau
ms.openlocfilehash: 31892232d5483bd2cb99d27c4672dbf347b904ef
ms.sourcegitcommit: 5a3b9f35d47355d026ee39d398c614ca4dae51c6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/02/2020
ms.locfileid: "89399017"
---
# <a name="how-to-set-up-a-geo-filtering-waf-policy-for-your-front-door"></a>Nastavení zásad geografického filtrování WAF pro službu Front Door
V tomto kurzu se dozvíte, jak pomocí Azure PowerShellu vytvořit ukázkovou zásadu geografického filtrování a přidružit ji k existujícímu hostiteli front-endu služby Front Door. Tato ukázková zásada geografického filtrování bude blokovat žádosti ze všech ostatních zemí nebo oblastí kromě USA.

Pokud ještě nemáte předplatné Azure, vytvořte si teď [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Předpoklady
Než začnete s nastavením zásad geografického filtru, nastavte prostředí PowerShell a vytvořte profil front-dveří.
### <a name="set-up-your-powershell-environment"></a>Nastavení prostředí PowerShell
Prostředí Azure PowerShell poskytuje sadu rutin, které ke správě vašich prostředků Azure využívají model [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview). 

[Azure PowerShell](https://docs.microsoft.com/powershell/azure/) můžete nainstalovat na místní počítač a používat v jakékoli relaci PowerShellu. Postupujte podle pokynů na stránce, přihlaste se pomocí přihlašovacích údajů Azure a nainstalujte modul AZ PowerShell.

#### <a name="connect-to-azure-with-an-interactive-dialog-for-sign-in"></a>Připojení k Azure pomocí interaktivního dialogu pro přihlášení
```
Install-Module -Name Az
Connect-AzAccount
```
Ujistěte se, že máte nainstalovanou aktuální verzi PowerShellGet. Spusťte následující příkaz a znovu otevřete PowerShell.

```
Install-Module PowerShellGet -Force -AllowClobber
``` 
#### <a name="install-azfrontdoor-module"></a>Instalace AZ. FrontDoor Module 

```
Install-Module -Name Az.FrontDoor
```

### <a name="create-a-front-door-profile"></a>Vytvoření profilu front-dveří
Vytvořte profil front-dveří podle pokynů popsaných v tématu [rychlý Start: vytvoření profilu front-dveří](quickstart-create-front-door.md).

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

V níže uvedeném příkladu se používá název skupiny prostředků *myResourceGroupFD1* s předpokladem, že jste vytvořili profil front-dveří pomocí pokynů uvedených v [rychlém startu: vytvořit článek na předních dveřích](quickstart-create-front-door.md) . V následujícím příkladu nahraďte název zásady *geoPolicyAllowUSOnly* jedinečným názvem zásady.

```
$geoPolicy = New-AzFrontDoorWafPolicy `
-Name "geoPolicyAllowUSOnly" `
-resourceGroupName myResourceGroupFD1 `
-Customrule $nonUSBlockRule  `
-Mode Prevention `
-EnabledState Enabled
```

## <a name="link-waf-policy-to-a-front-door-frontend-host"></a>Propojit zásady WAF s hostitelskou frontou front-endu
Propojte objekt zásad WAF s existujícím hostitelem front-endu front-endu a aktualizujte vlastnosti front-dveří. 

Uděláte to tak, že nejdřív načtěte objekt přední dveře pomocí [Get-AzFrontDoor](/powershell/module/az.frontdoor/get-azfrontdoor). 

```
$geoFrontDoorObjectExample = Get-AzFrontDoor -ResourceGroupName myResourceGroupFD1
$geoFrontDoorObjectExample[0].FrontendEndpoints[0].WebApplicationFirewallPolicyLink = $geoPolicy.Id
```

Dále nastavte vlastnost front-end WebApplicationFirewallPolicyLink na hodnotu resourceId `geoPolicy` pomocí [set-AzFrontDoor](/powershell/module/az.frontdoor/set-azfrontdoor).

```
Set-AzFrontDoor -InputObject $geoFrontDoorObjectExample[0]
```

> [!NOTE] 
> Stačí nastavit vlastnost WebApplicationFirewallPolicyLink jenom jednou, aby se propojí zásada WAF k hostiteli front-endu front-endu. Další aktualizace zásad se automaticky aplikují na hostitele front-endu.

## <a name="next-steps"></a>Další kroky
- Přečtěte si o [bráně firewall webových aplikací Azure](waf-overview.md).
- Přečtěte si, jak [vytvořit Front Door](quickstart-create-front-door.md).
