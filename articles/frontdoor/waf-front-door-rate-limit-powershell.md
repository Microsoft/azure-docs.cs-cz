---
title: Konfigurovat pravidlo omezení míry brány firewall webových aplikací pro front-Azure PowerShell
description: Naučte se konfigurovat pravidlo pro omezení přenosové rychlosti pro existující koncový bod přední dveře.
services: frontdoor
documentationcenter: ''
author: KumudD
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/31/2019
ms.author: kumud
ms.reviewer: tyao
ms.openlocfilehash: 99af39e996aaadd572603f63d019ff929b679550
ms.sourcegitcommit: fa45c2bcd1b32bc8dd54a5dc8bc206d2fe23d5fb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/12/2019
ms.locfileid: "67846244"
---
# <a name="configure-a-web-application-firewall-rate-limit-rule-using-azure-powershell"></a>Konfigurace pravidla omezení četnosti brány firewall webových aplikací pomocí Azure PowerShell
Pravidlo omezení četnosti firewallu webových aplikací (WAF) v Azure pro front-end řídí počet požadavků povolených z jedné IP adresy klienta během doby trvání 1 minuty.
V tomto článku se dozvíte, jak nakonfigurovat pravidlo omezení četnosti WAF, které řídí počet požadavků povolených z jednoho klienta na webovou aplikaci, která obsahuje */promo* v adrese URL pomocí Azure PowerShell.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky
Než začnete s nastavením zásady omezení četnosti, nastavte prostředí PowerShell a vytvořte profil front-dveří.
### <a name="set-up-your-powershell-environment"></a>Nastavení prostředí PowerShell
Prostředí Azure PowerShell poskytuje sadu rutin, které ke správě vašich prostředků Azure využívají model [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview). 

[Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) můžete nainstalovat na místní počítač a používat v jakékoli relaci PowerShellu. Postupujte podle pokynů na stránce, přihlaste se pomocí přihlašovacích údajů Azure a nainstalujte AZ PowerShell Module.

#### <a name="connect-to-azure-with-an-interactive-dialog-for-sign-in"></a>Připojení k Azure pomocí interaktivního dialogu pro přihlášení
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

## <a name="define-url-match-conditions"></a>Definování podmínek shody adresy URL
Definujte podmínku shody adresy URL (adresa URL obsahuje/promo) pomocí [New-AzFrontDoorWafMatchConditionObject](/powershell/module/az.frontdoor/new-azfrontdoorwafmatchconditionobject).
Následující příklad porovnává */promo* jako hodnotu proměnné *RequestUri* :

```powershell-interactive
   $promoMatchCondition = New-AzFrontDoorWafMatchConditionObject `
     -MatchVariable RequestUri `
     -OperatorProperty Contains `
     -MatchValue "/promo"
```
## <a name="create-a-custom-rate-limit-rule"></a>Vytvoření vlastního pravidla omezení četnosti
Nastavte omezení přenosové rychlosti pomocí [New-AzFrontDoorWafCustomRuleObject](/powershell/module/az.frontdoor/new-azfrontdoorwafcustomruleobject). V následujícím příkladu je limit nastaven na 1000. Žádosti od libovolného klienta na propagační stránku vyšší než 1000 během jedné minuty se zablokují, dokud se nespustí další minuta.

```powershell-interactive
   $promoRateLimitRule = New-AzFrontDoorWafCustomRuleObject `
     -Name "rateLimitRule" `
     -RuleType RateLimitRule `
     -MatchCondition $promoMatchCondition `
     -RateLimitThreshold 1000 `
     -Action Block -Priority 1
```


## <a name="configure-a-security-policy"></a>Konfigurace zásad zabezpečení

Vyhledejte název skupiny prostředků, která obsahuje profil front-dveří pomocí `Get-AzureRmResourceGroup`. V dalším kroku nakonfigurujte zásadu zabezpečení s vlastním pravidlem omezení četnosti pomocí [New-AzFrontDoorWafPolicy](/powershell/module/az.frontdoor/new-azfrontdoorwafpolicy) v zadané skupině prostředků, která obsahuje profil front-dveří.

V níže uvedeném příkladu se používá název skupiny prostředků *myResourceGroupFD1* s předpokladem, že jste vytvořili profil front-dveří pomocí pokynů uvedených v [rychlém startu: Vytvořte si článek na](quickstart-create-front-door.md) předním dveř.

 pomocí [New-AzFrontDoorWafPolicy](/powershell/module/az.frontdoor/new-azfrontdoorwafpolicy).

```powershell-interactive
   $ratePolicy = New-AzFrontDoorWafPolicy `
     -Name "RateLimitPolicyExamplePS" `
     -resourceGroupName myResourceGroupFD1 `
     -Customrule $promoRateLimitRule `
     -Mode Prevention `
     -EnabledState Enabled
```
## <a name="link-policy-to-a-front-door-front-end-host"></a>Odkazování zásady na front-end hostitele front-endu
Propojte objekt zásad zabezpečení s existujícím koncovým hostitelem front-end a aktualizujte vlastnosti front-endu. Nejdřív načtěte objekt přední dveře pomocí příkazu [Get-AzFrontDoor](/powershell/module/Az.FrontDoor/Get-AzFrontDoor) .
Dále nastavte vlastnost front-end *WebApplicationFirewallPolicyLink* na *resourceId* pro "$ratePolicy" vytvořenou v předchozím kroku pomocí příkazu [set-AzFrontDoor](/powershell/module/Az.FrontDoor/Set-AzFrontDoor) . 

V níže uvedeném příkladu se používá název skupiny prostředků *myResourceGroupFD1* s předpokladem, že jste vytvořili profil front-dveří pomocí pokynů uvedených v [rychlém startu: Vytvořte si článek na](quickstart-create-front-door.md) předním dveř. V následujícím příkladu nahraďte $frontDoorName názvem vašeho profilu front-dveří. 

```powershell-interactive
   $FrontDoorObjectExample = Get-AzFrontDoor `
     -ResourceGroupName myResourceGroupFD1 `
     -Name $frontDoorName
   $FrontDoorObjectExample[0].FrontendEndpoints[0].WebApplicationFirewallPolicyLink = $ratePolicy.Id
   Set-AzFrontDoor -InputObject $FrontDoorObjectExample[0]
 ```

> [!NOTE]
> Stačí nastavit vlastnost *WebApplicationFirewallPolicyLink* jenom jednou, aby se propojí zásada zabezpečení s front-end front-endu. Následné aktualizace zásad se automaticky aplikují na front-end.

## <a name="next-steps"></a>Další kroky

- Další informace o [front](front-door-overview.md) -dveřích 


