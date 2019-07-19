---
title: Konfigurace zásad firewallu webových aplikací (WAF) s vlastními pravidly a výchozí Ruse sadou pro front-Azure PowerShell
description: Naučte se konfigurovat zásady WAF, které se skládají z vlastních i spravovaných pravidel pro existující koncový bod služby front-dveří.
services: frontdoor
documentationcenter: ''
author: KumudD
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/21/2019
ms.author: kumud
ms.reviewer: tyao
ms.openlocfilehash: e9509172ac96a601235cc16e0d6d83c9b2f51902
ms.sourcegitcommit: fa45c2bcd1b32bc8dd54a5dc8bc206d2fe23d5fb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/12/2019
ms.locfileid: "67849120"
---
# <a name="configure-a-web-application-firewall-policy-using-azure-powershell"></a>Konfigurace zásad brány firewall webových aplikací pomocí Azure PowerShell
Zásady brány firewall webových aplikací (WAF) Azure definují kontroly vyžadované při doručení žádosti do Frontních dveří.
Tento článek popisuje, jak nakonfigurovat zásady WAF, které se skládají z některých vlastních pravidel a s povolenou výchozí Ruse sadou spravované v Azure.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky
Než začnete s nastavením zásady omezení četnosti, nastavte prostředí PowerShell a vytvořte profil front-dveří.
### <a name="set-up-your-powershell-environment"></a>Nastavení prostředí PowerShell
Prostředí Azure PowerShell poskytuje sadu rutin, které ke správě vašich prostředků Azure využívají model [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview). 

[Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) můžete nainstalovat na místní počítač a používat v jakékoli relaci PowerShellu. Postupujte podle pokynů na stránce, přihlaste se pomocí přihlašovacích údajů Azure a nainstalujte AZ PowerShell Module.

#### <a name="sign-in-to-azure"></a>Přihlášení k Azure
```
Connect-AzAccount

```
Před instalací modulu Front Door se ujistěte, že máte nainstalovanou aktuální verzi modulu PowerShellGet. Spusťte následující příkaz a znovu otevřete PowerShell.

```
Install-Module PowerShellGet -Force -AllowClobber
``` 

#### <a name="install-azfrontdoor-module"></a>Instalace AZ. FrontDoor Module 

```
Install-Module -Name Az.FrontDoor
```
### <a name="create-a-front-door-profile"></a>Vytvoření profilu front-dveří
Pomocí pokynů popsaných v [tématu rychlý Start vytvořte profil front-dveří: Vytvoření profilu front-dveří](quickstart-create-front-door.md)

## <a name="custom-rule-based-on-http-parameters"></a>Vlastní pravidlo založené na parametrech http

Následující příklad ukazuje, jak nakonfigurovat vlastní pravidlo se dvěma podmínkami shody pomocí [New-AzFrontDoorWafMatchConditionObject](/powershell/module/az.frontdoor/new-azfrontdoorwafmatchconditionobject). Požadavky jsou ze zadané lokality podle definice odkazujícího serveru a řetězec dotazu neobsahuje "heslo". 

```powershell-interactive
$referer = New-AzFrontDoorWafMatchConditionObject -MatchVariable RequestHeader -OperatorProperty Equal -Selector "Referer" -MatchValue "www.mytrustedsites.com/referpage.html"
$password = New-AzFrontDoorWafMatchConditionObject -MatchVariable QueryString -OperatorProperty Contains -MatchValue "password"
$AllowFromTrustedSites = New-AzFrontDoorWafCustomRuleObject -Name "AllowFromTrustedSites" -RuleType MatchRule -MatchCondition $referer,$password -Action Allow -Priority 1
```

## <a name="custom-rule-based-on-http-request-method"></a>Vlastní pravidlo na základě metody požadavku HTTP
Vytvořte pravidlo blokující metodu "PUT" pomocí příkazu [New-AzFrontDoorWafCustomRuleObject](/powershell/module/az.frontdoor/new-azfrontdoorwafcustomruleobject) následujícím způsobem:

```powershell-interactive
$put = New-AzFrontDoorWafMatchConditionObject -MatchVariable RequestMethod -OperatorProperty Equal -MatchValue PUT
$BlockPUT = New-AzFrontDoorWafCustomRuleObject -Name "BlockPUT" -RuleType MatchRule -MatchCondition $put -Action Block -Priority 2
```

## <a name="create-a-custom-rule-based-on-size-constraint"></a>Vytvoření vlastního pravidla založeného na omezení velikosti

Následující příklad vytvoří pravidlo blokující požadavky s adresou URL, která je delší než 100 znaků pomocí Azure PowerShell:
```powershell-interactive
$url = New-AzFrontDoorWafMatchConditionObject -MatchVariable RequestUri -OperatorProperty GreaterThanOrEqual -MatchValue 100
$URLOver100 = New-AzFrontDoorWafCustomRuleObject -Name "URLOver100" -RuleType MatchRule -MatchCondition $url -Action Block -Priority 3
```
## <a name="add-managed-default-rule-set"></a>Přidat spravovanou výchozí sadu pravidel

Následující příklad vytvoří spravovanou výchozí sadu pravidel pomocí Azure PowerShell:
```powershell-interactive
$managedRules =  New-AzFrontDoorWafManagedRuleObject -Type DefaultRuleSet -Version 1.0
```
## <a name="configure-a-security-policy"></a>Konfigurace zásad zabezpečení

Vyhledejte název skupiny prostředků, která obsahuje profil front-dveří pomocí `Get-AzResourceGroup`. Dále nakonfigurujte zásadu zabezpečení s vytvořenými pravidly v předchozích krocích pomocí [New-AzFrontDoorWafPolicy](/powershell/module/az.frontdoor/new-azfrontdoorwafpolicy) v zadané skupině prostředků, která obsahuje profil front-dveří.

```powershell-interactive
$myWAFPolicy=New-AzFrontDoorWafPolicy -Name $policyName -ResourceGroupName $resourceGroupName -Customrule $AllowFromTrustedSites,$BlockPUT,$URLOver100 -ManagedRule $managedRules -EnabledState Enabled -Mode Prevention
```

## <a name="link-policy-to-a-front-door-front-end-host"></a>Odkazování zásady na front-end hostitele front-endu
Propojte objekt zásad zabezpečení s existujícím koncovým hostitelem front-end a aktualizujte vlastnosti front-endu. Nejdřív načtěte objekt front-dveří pomocí [Get-AzFrontDoor](/powershell/module/Az.FrontDoor/Get-AzFrontDoor).
Dále nastavte vlastnost front-end *WebApplicationFirewallPolicyLink* na *resourceId* pro "$myWAFPolicy $" vytvořenou v předchozím kroku pomocí [set-AzFrontDoor](/powershell/module/Az.FrontDoor/Set-AzFrontDoor). 

V níže uvedeném příkladu se používá název skupiny prostředků *myResourceGroupFD1* s předpokladem, že jste vytvořili profil front-dveří pomocí pokynů uvedených v [rychlém startu: Vytvořte si článek na](quickstart-create-front-door.md) předním dveř. V následujícím příkladu nahraďte $frontDoorName názvem vašeho profilu front-dveří. 

```powershell-interactive
   $FrontDoorObjectExample = Get-AzFrontDoor `
     -ResourceGroupName myResourceGroupFD1 `
     -Name $frontDoorName
   $FrontDoorObjectExample[0].FrontendEndpoints[0].WebApplicationFirewallPolicyLink = $myWAFPolicy.Id
   Set-AzFrontDoor -InputObject $FrontDoorObjectExample[0]
 ```

> [!NOTE]
> Stačí nastavit vlastnost *WebApplicationFirewallPolicyLink* jenom jednou, aby se propojí zásada zabezpečení s front-end front-endu. Následné aktualizace zásad se automaticky aplikují na front-end.

## <a name="next-steps"></a>Další postup

- Další informace o [front](front-door-overview.md) -dveřích 
- Další informace o [WAF pro přední dveře](waf-overview.md)
