---
title: Konfigurace vlastních pravidel WAF & výchozí sadě pravidel pro azure front door
description: Zjistěte, jak nakonfigurovat zásady WAF se skládají z vlastních i spravovaných pravidel pro existující koncový bod předních dveří.
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.topic: article
ms.date: 09/05/2019
ms.author: victorh
ms.openlocfilehash: 493ed1a31a23366a90b80d3ab510218c8dce0e9c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74186631"
---
# <a name="configure-a-web-application-firewall-policy-using-azure-powershell"></a>Konfigurace zásad brány firewall webových aplikací pomocí Azure PowerShellu

Zásady Azure Web Application Firewall (WAF) definují kontroly požadované při doručení požadavku na front door.
Tento článek ukazuje, jak nakonfigurovat zásady WAF, která se skládá z některých vlastních pravidel a s povolenou výchozí sadou pravidel spravované Azure.

Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) než začnete.

## <a name="prerequisites"></a>Požadavky

Než začnete nastavovat zásady omezení rychlosti, nastavte prostředí Prostředí PowerShellu a vytvořte profil předních dveří.

### <a name="set-up-your-powershell-environment"></a>Nastavení prostředí PowerShell

Prostředí Azure PowerShell poskytuje sadu rutin, které ke správě vašich prostředků Azure využívají model [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview). 

[Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) můžete nainstalovat na místní počítač a používat v jakékoli relaci PowerShellu. Podle pokynů na stránce se přihlaste pomocí přihlašovacích údajů Azure a nainstalujte modul Az PowerShell.

#### <a name="sign-in-to-azure"></a>Přihlášení k Azure

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
### <a name="create-a-front-door-profile"></a>Vytvoření profilu předních dveří

Vytvoření profilu předních dveří podle pokynů popsaných v [úvodním programu: Vytvoření profilu předních dveří](../../frontdoor/quickstart-create-front-door.md)

## <a name="custom-rule-based-on-http-parameters"></a>Vlastní pravidlo založené na parametrech http

Následující příklad ukazuje, jak nakonfigurovat vlastní pravidlo se dvěma podmínkami shody pomocí [new-azfrontdoorwafMatchConditionObject](/powershell/module/az.frontdoor/new-azfrontdoorwafmatchconditionobject). Požadavky pocházejí ze zadaného webu, jak je definováno odkazem, a řetězec dotazu neobsahuje "heslo". 

```powershell-interactive
$referer = New-AzFrontDoorWafMatchConditionObject -MatchVariable RequestHeader -OperatorProperty Equal -Selector "Referer" -MatchValue "www.mytrustedsites.com/referpage.html"
$password = New-AzFrontDoorWafMatchConditionObject -MatchVariable QueryString -OperatorProperty Contains -MatchValue "password"
$AllowFromTrustedSites = New-AzFrontDoorWafCustomRuleObject -Name "AllowFromTrustedSites" -RuleType MatchRule -MatchCondition $referer,$password -Action Allow -Priority 1
```

## <a name="custom-rule-based-on-http-request-method"></a>Vlastní pravidlo založené na metodě požadavku http

Vytvořte metodu "PUT" blokování pravidla pomocí [New-AzFrontDoorWafCustomRuleObject](/powershell/module/az.frontdoor/new-azfrontdoorwafcustomruleobject) následujícím způsobem:

```powershell-interactive
$put = New-AzFrontDoorWafMatchConditionObject -MatchVariable RequestMethod -OperatorProperty Equal -MatchValue PUT
$BlockPUT = New-AzFrontDoorWafCustomRuleObject -Name "BlockPUT" -RuleType MatchRule -MatchCondition $put -Action Block -Priority 2
```

## <a name="create-a-custom-rule-based-on-size-constraint"></a>Vytvoření vlastního pravidla na základě omezení velikosti

Následující příklad vytvoří požadavky na blokování pravidel s adresou URL, která je delší než 100 znaků pomocí Azure PowerShellu:
```powershell-interactive
$url = New-AzFrontDoorWafMatchConditionObject -MatchVariable RequestUri -OperatorProperty GreaterThanOrEqual -MatchValue 100
$URLOver100 = New-AzFrontDoorWafCustomRuleObject -Name "URLOver100" -RuleType MatchRule -MatchCondition $url -Action Block -Priority 3
```
## <a name="add-managed-default-rule-set"></a>Přidat spravovanou výchozí sadu pravidel

Následující příklad vytvoří spravovanou výchozí sadu pravidel pomocí Azure PowerShellu:
```powershell-interactive
$managedRules =  New-AzFrontDoorWafManagedRuleObject -Type DefaultRuleSet -Version 1.0
```
## <a name="configure-a-security-policy"></a>Konfigurace zásad zabezpečení

Vyhledejte název skupiny prostředků, která obsahuje `Get-AzResourceGroup`profil Přední dveře pomocí aplikace . Dále nakonfigurujte zásady zabezpečení s vytvořenými pravidly v předchozích krocích pomocí [new-azfrontdoorwafPolicy](/powershell/module/az.frontdoor/new-azfrontdoorwafpolicy) v zadané skupině prostředků, která obsahuje profil Přední dveře.

```powershell-interactive
$myWAFPolicy=New-AzFrontDoorWafPolicy -Name $policyName -ResourceGroupName $resourceGroupName -Customrule $AllowFromTrustedSites,$BlockPUT,$URLOver100 -ManagedRule $managedRules -EnabledState Enabled -Mode Prevention
```

## <a name="link-policy-to-a-front-door-front-end-host"></a>Propojení zásad s front-endovým hostitelem předních dveří

Propojte objekt zásad zabezpečení s existujícím front-end hostitelem předních dveří a aktualizujte vlastnosti předních dveří. Nejprve načtěte objekt Front Door pomocí [Get-AzFrontDoor](/powershell/module/Az.FrontDoor/Get-AzFrontDoor).
Dále nastavte vlastnost front-end *WebApplicationFirewallPolicyLink* na *id "$myWAFPolicy$"* vytvořeného v předchozím kroku pomocí [Set-AzFrontDoor](/powershell/module/Az.FrontDoor/Set-AzFrontDoor). 

Níže uvedený příklad používá název skupiny prostředků *myResourceGroupFD1* s předpokladem, že jste vytvořili profil Přední dveře podle pokynů uvedených v [článku Rychlý start: Vytvoření předních dveří.](../../frontdoor/quickstart-create-front-door.md) V níže uvedeném příkladu také nahraďte $frontDoorName názvem profilu předních dveří. 

```powershell-interactive
   $FrontDoorObjectExample = Get-AzFrontDoor `
     -ResourceGroupName myResourceGroupFD1 `
     -Name $frontDoorName
   $FrontDoorObjectExample[0].FrontendEndpoints[0].WebApplicationFirewallPolicyLink = $myWAFPolicy.Id
   Set-AzFrontDoor -InputObject $FrontDoorObjectExample[0]
 ```

> [!NOTE]
> Chcete-li propojit zásady zabezpečení s front-endem předních dveří, stačí nastavit vlastnost *WebApplicationFirewallPolicyLink* pouze jednou. Následné aktualizace zásad jsou automaticky použity na front-end.

## <a name="next-steps"></a>Další kroky

- Další informace o [předních dveřích](../../frontdoor/front-door-overview.md) 
- Další informace o [WAF s předními dveřmi](afds-overview.md)
