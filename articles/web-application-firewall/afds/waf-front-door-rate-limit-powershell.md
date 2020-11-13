---
title: Nakonfigurovat pravidlo limitu WAF frekvence pro front-Azure PowerShell
description: Naučte se konfigurovat pravidlo pro omezení přenosové rychlosti pro existující koncový bod přední dveře.
author: vhorne
ms.service: web-application-firewall
ms.topic: article
services: web-application-firewall
ms.date: 02/26/2020
ms.author: victorh
ms.openlocfilehash: 23b893bad591af5f1e923b68e8d30453f859792b
ms.sourcegitcommit: 04fb3a2b272d4bbc43de5b4dbceda9d4c9701310
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/12/2020
ms.locfileid: "94563473"
---
# <a name="configure-a-web-application-firewall-rate-limit-rule-using-azure-powershell"></a>Konfigurace pravidla omezení četnosti brány firewall webových aplikací pomocí Azure PowerShell
Pravidlo omezení četnosti brány firewall webových aplikací (WAF) pro službu Azure front-dveře řídí počet požadavků povolených od klientů během minutového trvání.
V tomto článku se dozvíte, jak nakonfigurovat pravidlo omezení četnosti WAF, které řídí počet požadavků povolených klienty na webovou aplikaci, která obsahuje */promo* v adrese URL pomocí Azure PowerShell.

Pokud ještě předplatné Azure nemáte, vytvořte si napřed [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

> [!NOTE]
> Pro každou IP adresu klienta se aplikují omezení přenosové rychlosti. Pokud máte několik klientů, kteří přistupují k vašim předním dveřím z různých IP adres, budou se použít vlastní omezení přenosové rychlosti.

## <a name="prerequisites"></a>Požadavky
Než začnete s nastavením zásady omezení četnosti, nastavte prostředí PowerShell a vytvořte profil front-dveří.
### <a name="set-up-your-powershell-environment"></a>Nastavení prostředí PowerShell
Prostředí Azure PowerShell poskytuje sadu rutin, které ke správě vašich prostředků Azure využívají model [Azure Resource Manager](../../azure-resource-manager/management/overview.md). 

[Azure PowerShell](/powershell/azure/) můžete nainstalovat na místní počítač a používat v jakékoli relaci PowerShellu. Postupujte podle pokynů na stránce, přihlaste se pomocí přihlašovacích údajů Azure a nainstalujte AZ PowerShell Module.

#### <a name="connect-to-azure-with-an-interactive-dialog-for-sign-in"></a>Připojení k Azure pomocí interaktivního dialogu pro přihlášení
```
Connect-AzAccount

```
Před instalací modulu front-dveří se ujistěte, že je nainstalovaná aktuální verze nástroje PowerShellGet. Spusťte následující příkaz a znovu otevřete PowerShell.

```
Install-Module PowerShellGet -Force -AllowClobber
``` 

#### <a name="install-azfrontdoor-module"></a>Instalace AZ. FrontDoor Module 

```
Install-Module -Name Az.FrontDoor
```
### <a name="create-a-front-door-profile"></a>Vytvoření profilu front-dveří
Vytvořte profil front-dveří podle pokynů popsaných v tématu [rychlý Start: vytvoření profilu front-dveří.](../../frontdoor/quickstart-create-front-door.md)

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

Vyhledejte název skupiny prostředků, která obsahuje profil front-dveří pomocí `Get-AzureRmResourceGroup` . V dalším kroku nakonfigurujte zásadu zabezpečení s vlastním pravidlem omezení četnosti pomocí [New-AzFrontDoorWafPolicy](/powershell/module/az.frontdoor/new-azfrontdoorwafpolicy) v zadané skupině prostředků, která obsahuje profil front-dveří.

V níže uvedeném příkladu se používá název skupiny prostředků *myResourceGroupFD1* s předpokladem, že jste vytvořili profil front-dveří pomocí pokynů uvedených v [rychlém startu: Vytvoření článku na předních dveřích](../../frontdoor/quickstart-create-front-door.md) pomocí [New-AzFrontDoorWafPolicy](/powershell/module/az.frontdoor/new-azfrontdoorwafpolicy).

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

V níže uvedeném příkladu se používá název skupiny prostředků *myResourceGroupFD1* s předpokladem, že jste vytvořili profil front-dveří pomocí pokynů uvedených v [rychlém startu: vytvořit článek na předních dveřích](../../frontdoor/quickstart-create-front-door.md) . V následujícím příkladu nahraďte $frontDoorName názvem vašeho profilu front-dveří. 

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

- Přečtěte si další informace o [front-dveřích](../../frontdoor/front-door-overview.md).