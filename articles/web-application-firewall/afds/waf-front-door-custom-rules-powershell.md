---
title: Konfigurace vlastních pravidel WAF & sada výchozích pravidel pro Azure front-dveří
description: Naučte se konfigurovat zásady WAF, které se skládají z vlastních i spravovaných pravidel pro existující koncový bod služby front-dveří.
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.topic: article
ms.date: 09/05/2019
ms.author: victorh
ms.openlocfilehash: 40f0195ced375da09891eb577bd81f36b2f37cba
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87005544"
---
# <a name="configure-a-web-application-firewall-policy-using-azure-powershell"></a>Konfigurace zásad brány firewall webových aplikací pomocí Azure PowerShell

Zásady brány firewall webových aplikací (WAF) Azure definují kontroly vyžadované při doručení žádosti do Frontních dveří.
V tomto článku se dozvíte, jak nakonfigurovat zásady WAF, které se skládají z některých vlastních pravidel a s povolenou výchozí sadou pravidel spravované v Azure.

Pokud ještě předplatné Azure nemáte, vytvořte si napřed [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Požadavky

Než začnete s nastavením zásady omezení četnosti, nastavte prostředí PowerShell a vytvořte profil front-dveří.

### <a name="set-up-your-powershell-environment"></a>Nastavení prostředí PowerShell

Prostředí Azure PowerShell poskytuje sadu rutin, které ke správě vašich prostředků Azure využívají model [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview). 

[Azure PowerShell](https://docs.microsoft.com/powershell/azure/) můžete nainstalovat na místní počítač a používat v jakékoli relaci PowerShellu. Postupujte podle pokynů na stránce, přihlaste se pomocí přihlašovacích údajů Azure a nainstalujte AZ PowerShell Module.

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

Vytvořte profil front-dveří podle pokynů popsaných v tématu [rychlý Start: vytvoření profilu front-dveří.](../../frontdoor/quickstart-create-front-door.md)

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

Vyhledejte název skupiny prostředků, která obsahuje profil front-dveří pomocí `Get-AzResourceGroup` . Dále nakonfigurujte zásadu zabezpečení s vytvořenými pravidly v předchozích krocích pomocí [New-AzFrontDoorWafPolicy](/powershell/module/az.frontdoor/new-azfrontdoorwafpolicy) v zadané skupině prostředků, která obsahuje profil front-dveří.

```powershell-interactive
$myWAFPolicy=New-AzFrontDoorWafPolicy -Name $policyName -ResourceGroupName $resourceGroupName -Customrule $AllowFromTrustedSites,$BlockPUT,$URLOver100 -ManagedRule $managedRules -EnabledState Enabled -Mode Prevention
```

## <a name="link-policy-to-a-front-door-front-end-host"></a>Odkazování zásady na front-end hostitele front-endu

Propojte objekt zásad zabezpečení s existujícím koncovým hostitelem front-end a aktualizujte vlastnosti front-endu. Nejdřív načtěte objekt front-dveří pomocí [Get-AzFrontDoor](/powershell/module/Az.FrontDoor/Get-AzFrontDoor).
Dále nastavte vlastnost front-end *WebApplicationFirewallPolicyLink* na *resourceId* pro "$myWAFPolicy $" vytvořenou v předchozím kroku pomocí [set-AzFrontDoor](/powershell/module/Az.FrontDoor/Set-AzFrontDoor). 

V níže uvedeném příkladu se používá název skupiny prostředků *myResourceGroupFD1* s předpokladem, že jste vytvořili profil front-dveří pomocí pokynů uvedených v [rychlém startu: vytvořit článek na předních dveřích](../../frontdoor/quickstart-create-front-door.md) . V následujícím příkladu nahraďte $frontDoorName názvem vašeho profilu front-dveří. 

```powershell-interactive
   $FrontDoorObjectExample = Get-AzFrontDoor `
     -ResourceGroupName myResourceGroupFD1 `
     -Name $frontDoorName
   $FrontDoorObjectExample[0].FrontendEndpoints[0].WebApplicationFirewallPolicyLink = $myWAFPolicy.Id
   Set-AzFrontDoor -InputObject $FrontDoorObjectExample[0]
 ```

> [!NOTE]
> Stačí nastavit vlastnost *WebApplicationFirewallPolicyLink* jenom jednou, aby se propojí zásada zabezpečení s front-end front-endu. Následné aktualizace zásad se automaticky aplikují na front-end.

## <a name="next-steps"></a>Další kroky

- Další informace o [front-dveřích](../../frontdoor/front-door-overview.md) 
- Další informace o [WAF s front-dvířky](afds-overview.md)
