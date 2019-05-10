---
title: Kurz – konfigurace geografické filtrování webové aplikace zásady brány firewall pro službu Azure branou
description: V tomto kurzu se dozvíte, jak vytvořit jednoduchou zásadu geografického filtrování a přidružit ji k existujícímu hostiteli front-endu služby Front Door.
services: frontdoor
documentationcenter: ''
author: KumudD
manager: twooley
editor: ''
ms.service: frontdoor
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/21/2019
ms.author: kumud;tyao
ms.openlocfilehash: af1846f66996ded553a95188df958e9592ec68a2
ms.sourcegitcommit: bb85a238f7dbe1ef2b1acf1b6d368d2abdc89f10
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/10/2019
ms.locfileid: "65523792"
---
# <a name="how-to-set-up-a-geo-filtering-waf-policy-for-your-front-door"></a>Jak nastavit zásadu geografické filtrování WAF pro vaše branou
V tomto kurzu se dozvíte, jak pomocí Azure PowerShellu vytvořit ukázkovou zásadu geografického filtrování a přidružit ji k existujícímu hostiteli front-endu služby Front Door. Tato ukázka geografické filtrování zásada bude blokovat požadavky ze všech jiných zemích nebo oblastech s výjimkou Spojených států.

Pokud ještě nemáte předplatné Azure, vytvořte si teď [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Požadavky
Před zahájením nastavení zásady geografického filtrování, nastavte si prostředí PowerShell a vytvořte profil branou.
### <a name="set-up-your-powershell-environment"></a>Nastavení prostředí PowerShell
Prostředí Azure PowerShell poskytuje sadu rutin, které ke správě vašich prostředků Azure využívají model [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview). 

[Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) můžete nainstalovat na místní počítač a používat v jakékoli relaci PowerShellu. Na stránce, přihlaste se pomocí přihlašovacích údajů Azure, postupujte podle pokynů a nainstalujte modul prostředí PowerShell Az.

#### <a name="connect-to-azure-with-an-interactive-dialog-for-sign-in"></a>Připojení k Azure pomocí interaktivního dialogové okno pro přihlášení
```
Connect-AzAccount
Install-Module -Name Az
```
Ujistěte se, že máte aktuální verzi modul PowerShellGet nainstalovaný. Spusťte následující příkaz a znovu otevřete PowerShell.

```
Install-Module PowerShellGet -Force -AllowClobber
``` 
#### <a name="install-azfrontdoor-module"></a>Instalace modulu Az.FrontDoor 

```
Install-Module -Name Az.FrontDoor
```

### <a name="create-a-front-door-profile"></a>Vytvoření profilu branou
Vytvoření profilu branou podle pokynů v tématu [rychlý start: Vytvoření profilu branou](quickstart-create-front-door.md).

## <a name="define-geo-filtering-match-condition"></a>Definování geografické filtrování odpovídají podmínce

Vytvořit podmínku shody vzorku, který vybere není než dorazí požadavky od "USA" pomocí [New-AzFrontDoorWafMatchConditionObject](/powershell/module/az.frontdoor/new-azfrontdoorwafmatchconditionobject) na parametry při vytváření podmínku shody. Jsou k dispozici dva kódy zemí písmeno země mapování [tady](front-door-geo-filtering.md).

```azurepowershell-interactive
$nonUSGeoMatchCondition = New-AzFrontDoorWafMatchConditionObject `
-MatchVariable RemoteAddr `
-OperatorProperty GeoMatch `
-NegateCondition $true `
-MatchValue "US"
```
 
## <a name="add-geo-filtering-match-condition-to-a-rule-with-action-and-priority"></a>Přidání podmínky shody geografického filtrování do pravidla s parametry Action a Priority

Vytvořte objekt CustomRule `nonUSBlockRule` na základě podmínky shody, akci a prioritu pomocí [New-AzFrontDoorCustomRuleObject](/powershell/module/az.frontdoor/new-azfrontdoorwafcustomruleobject).  Objekt CustomRule může mít více podmínek shody MatchCondition.  V tomto příkladu se parametr Action nastaví na Block a parametr Priority na 1, což je nejvyšší priorita.

```
$nonUSBlockRule = New-AzFrontDoorCustomRuleObject `
-Name "geoFilterRule" `
-RuleType MatchRule `
-MatchCondition $nonUSGeoMatchCondition `
-Action Block `
-Priority 1
```

## <a name="add-rules-to-a-policy"></a>Přidání pravidel pro zásadu
Najít název skupiny prostředků, která obsahuje pomocí profilu branou `Get-AzResourceGroup`. Dále vytvořte `geoPolicy` zásady objekt obsahující `nonUSBlockRule` pomocí [New-AzFrontDoorWafPolicy](/powershell/module/az.frontdoor/new-azfrontdoorwafpolicy) v zadané skupině prostředků, který obsahuje profil branou. Musíte zadat jedinečný název pro zásadu geo. 

Následujícím příkladu používá název skupiny prostředků *myResourceGroupFD1* za předpokladu, že jste vytvořili branou profilována za použití podle pokynů [rychlý start: Vytvoření branou](quickstart-create-front-door.md) článku. V následujícím příkladu nahraďte název zásady *geoPolicyAllowUSOnly* s jedinečný název zásady.

```
$geoPolicy = New-AzFrontDoorWafPolicy `
-Name "geoPolicyAllowUSOnly" `
-resourceGroupName myResourceGroupFD1 `
-Customrule $nonUSBlockRule  `
-Mode Prevention `
-EnabledState Enabled
```

## <a name="link-waf-policy-to-a-front-door-frontend-host"></a>Zásady odkaz WAF pro hostování front-endu branou
Propojit existujícího hostitele front-endu branou objektu zásad WAF a aktualizovat vlastnosti branou. 

Uděláte to tak, nejdřív načtěte pomocí objektu branou [Get-AzFrontDoor](/powershell/module/az.frontdoor/get-azfrontdoor). 

```
$geoFrontDoorObjectExample = Get-AzFrontDoor -ResourceGroupName myResourceGroupFD1
$geoFrontDoorObjectExample[0].FrontendEndpoints[0].WebApplicationFirewallPolicyLink = $geoPolicy.Id
```

Dále nastavte vlastnost WebApplicationFirewallPolicyLink front-endu na ID prostředku z `geoPolicy`pomocí [Set-AzFrontDoor](/powershell/module/az.frontdoor/set-azfrontdoor).

```
Set-AzFrontDoor -InputObject $geoFrontDoorObjectExample[0]
```

> [!NOTE] 
> Stačí nastavit vlastnost WebApplicationFirewallPolicyLink jednou propojení zásadu WAF pro hostování front-endu branou. Další zásady aktualizace automaticky nainstalují do hostitele front-endu.

## <a name="next-steps"></a>Další postup

- Přečtěte si o [zabezpečení aplikační vrstvy pomocí služby Front Door](front-door-application-security.md).
- Přečtěte si, jak [vytvořit službu Front Door](quickstart-create-front-door.md).
