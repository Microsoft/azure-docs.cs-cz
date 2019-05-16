---
title: Konfigurace zásad brány firewall (WAF) webové aplikace s vlastní pravidla a výchozí nastavení Ruse pro branou – Azure PowerShell
description: Další informace o konfiguraci brány WAF zásad se skládají z spravovaných i vlastní pravidla pro existující koncový bod branou.
services: frontdoor
documentationcenter: ''
author: KumudD
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/08/2019
ms.author: kumud;tyao
ms.openlocfilehash: 414869833b894e2688505a91fed8fafe0c912b73
ms.sourcegitcommit: bb85a238f7dbe1ef2b1acf1b6d368d2abdc89f10
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/10/2019
ms.locfileid: "65523740"
---
# <a name="configure-a-web-application-firewall-policy-using-azure-powershell"></a>Konfigurace zásad brány firewall webových aplikací pomocí Azure Powershellu
Zásady brány firewall (WAF) Azure webové aplikace definuje kontroly požadováno, když dorazí požadavek na branou.
Tento článek ukazuje, jak nakonfigurovat zásady WAF, která obsahuje některá vlastní pravidla a spravovanými Azure výchozí Ruse nastavení povolena.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky
Před zahájením nastavení zásady omezení četnosti, nastavte si prostředí PowerShell a vytvořit profil branou.
### <a name="set-up-your-powershell-environment"></a>Nastavení prostředí PowerShell
Prostředí Azure PowerShell poskytuje sadu rutin, které ke správě vašich prostředků Azure využívají model [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview). 

[Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) můžete nainstalovat na místní počítač a používat v jakékoli relaci PowerShellu. Na stránce, přihlaste se pomocí přihlašovacích údajů Azure, postupujte podle pokynů a nainstalujte modul prostředí PowerShell Az.

#### <a name="sign-in-to-azure"></a>Přihlásit se k Azure
```
Connect-AzAccount

```
Před instalací modulu Front Door se ujistěte, že máte nainstalovanou aktuální verzi modulu PowerShellGet. Spusťte následující příkaz a znovu otevřete PowerShell.

```
Install-Module PowerShellGet -Force -AllowClobber
``` 

#### <a name="install-azfrontdoor-module"></a>Instalace modulu Az.FrontDoor 

```
Install-Module -Name Az.FrontDoor
```
### <a name="create-a-front-door-profile"></a>Vytvoření profilu branou
Vytvoření profilu branou podle pokynů v tématu [rychlý start: Vytvoření profilu branou](quickstart-create-front-door.md)

## <a name="custom-rule-based-on-http-parameters"></a>Vlastní pravidlo na základě parametrů http

Následující příklad ukazuje, jak nakonfigurovat dvě podmínky shody pomocí vlastního pravidla [New-AzFrontDoorWafMatchConditionObject](/powershell/module/az.frontdoor/new-azfrontdoorwafmatchconditionobject). Požadavky jsou z určené lokality dle odkazující server a řetězec dotazu neobsahuje "password". 

```powershell-interactive
$referer = New-AzFrontDoorWafMatchConditionObject -MatchVariable RequestHeader -OperatorProperty Equal -Selector "Referer" -MatchValue "www.mytrustedsites.com/referpage.html"
$password = New-AzFrontDoorWafMatchConditionObject -MatchVariable QueryString -OperatorProperty Contains -MatchValue "password"
$AllowFromTrustedSites = New-AzFrontDoorCustomRuleObject -Name "AllowFromTrustedSites" -RuleType MatchRule -MatchCondition $referer,$password -Action Allow -Priority 1
```

## <a name="custom-rule-based-on-http-request-method"></a>Vlastní pravidlo podle metody požadavku http
Vytvořit pravidlo blokuje "Vložit" pomocí metody [New-AzFrontDoorCustomRuleObject](/powershell/module/az.frontdoor/new-azfrontdoorwafcustomruleobject) následujícím způsobem:

```powershell-interactive
$put = New-AzFrontDoorWafMatchConditionObject -MatchVariable RequestMethod -OperatorProperty Equal -MatchValue PUT
$BlockPUT = New-AzFrontDoorCustomRuleObject -Name "BlockPUT" -RuleType MatchRule -MatchCondition $put -Action Block -Priority 2
```

## <a name="create-a-custom-rule-based-on-size-constraint"></a>Vytvoření vlastního pravidla podle omezení velikosti

Následující příklad vytvoří pravidlo blokovat požadavky s adresou Url, která je delší než 100 znaků pomocí Azure Powershellu:
```powershell-interactive
$url = New-AzFrontDoorWafMatchConditionObject -MatchVariable RequestUri -OperatorProperty GreaterThanOrEqual -MatchValue 100
$URLOver100 = New-AzFrontDoorCustomRuleObject -Name "URLOver100" -RuleType MatchRule -MatchCondition $url -Action Block -Priority 3
```
## <a name="add-managed-default-rule-set"></a>Přidat spravovaný výchozí sadu pravidel

Následující příklad vytvoří spravované výchozí pravidlo sady pomocí Azure Powershellu:
```powershell-interactive
$managedRules = New-AzFrontDoorManagedRuleObject -Type DefaultRuleSet -Version "preview-0.1"
```
## <a name="configure-a-security-policy"></a>Konfigurace zásad zabezpečení

Najít název skupiny prostředků, která obsahuje pomocí profilu branou `Get-AzResourceGroup`. V dalším kroku nakonfigurujte zásadu zabezpečení pravidlům vytvořený v předchozích krocích pomocí [New-AzFrontDoorWafPolicy](/powershell/module/az.frontdoor/new-azfrontdoorwafpolicy) v zadané skupině prostředků, který obsahuje profil branou.

```powershell-interactive
$myWAFPolicy=New-AzFrontDoorWafPolicy -Name $policyName -ResourceGroupName $resourceGroupName -Customrule $AllowFromTrustedSites,$BlockPUT,$URLOver100 -ManagedRule $managedRules -EnabledState Enabled -Mode Prevention
```

## <a name="link-policy-to-a-front-door-front-end-host"></a>Odkaz zásad na hostitele front-endu branou
Propojit existujícího hostitele front-endu branou objektu zásad zabezpečení a aktualizovat vlastnosti branou. Nejdřív, načtěte objekt branou pomocí [Get-AzFrontDoor](/powershell/module/Az.FrontDoor/Get-AzFrontDoor).
Dále nastavte front-endu *WebApplicationFirewallPolicyLink* vlastnost *resourceId* "$myWAFPolicy $" vytvořili v předchozím kroku, použijte [Set-AzFrontDoor](/powershell/module/Az.FrontDoor/Set-AzFrontDoor). 

Následujícím příkladu používá název skupiny prostředků *myResourceGroupFD1* za předpokladu, že jste vytvořili branou profilována za použití podle pokynů [rychlý start: Vytvoření branou](quickstart-create-front-door.md) článku. Také v následujícím příkladu nahraďte názvem vašeho profilu branou $frontDoorName. 

```powershell-interactive
   $FrontDoorObjectExample = Get-AzFrontDoor `
     -ResourceGroupName myResourceGroupFD1 `
     -Name $frontDoorName
   $FrontDoorObjectExample[0].FrontendEndpoints[0].WebApplicationFirewallPolicyLink = $myWAFPolicy.Id
   Set-AzFrontDoor -InputObject $FrontDoorObjectExample[0]
 ```

> [!NOTE]
> Je potřeba nastavit *WebApplicationFirewallPolicyLink* vlastnost jednou propojit zásady zabezpečení s branou front-endu. Další zásady aktualizace automaticky nainstalují do front-endu.

## <a name="next-steps"></a>Další postup

- Další informace o [branou](front-door-overview.md) 
- Další informace o [WAF pro branou](waf-overview.md)
