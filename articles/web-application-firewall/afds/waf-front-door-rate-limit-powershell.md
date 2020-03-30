---
title: Konfigurace pravidla limitu rychlosti WAF pro přední dveře – Azure PowerShell
description: Přečtěte si, jak nakonfigurovat pravidlo omezení rychlosti pro existující koncový bod předních dveří.
author: vhorne
ms.service: web-application-firewall
ms.topic: article
services: web-application-firewall
ms.date: 02/26/2020
ms.author: victorh
ms.openlocfilehash: b034159c3d12927f6425b3dc3c5b5609af9b0b76
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77649360"
---
# <a name="configure-a-web-application-firewall-rate-limit-rule-using-azure-powershell"></a>Konfigurace pravidla omezení rychlosti brány firewall webových aplikací pomocí Azure PowerShellu
Pravidlo limitu rychlosti brány Azure Web Application Firewall (WAF) pro Azure Front Door řídí počet požadavků povolených od klientů během jedné minuty trvání.
Tento článek ukazuje, jak nakonfigurovat pravidlo omezení rychlosti WAF, které řídí počet požadavků povolených od klientů do webové aplikace, která obsahuje */promo* v adrese URL pomocí Azure PowerShell.

Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) než začnete.

## <a name="prerequisites"></a>Požadavky
Než začnete nastavovat zásady omezení rychlosti, nastavte prostředí Prostředí PowerShellu a vytvořte profil předních dveří.
### <a name="set-up-your-powershell-environment"></a>Nastavení prostředí PowerShell
Prostředí Azure PowerShell poskytuje sadu rutin, které ke správě vašich prostředků Azure využívají model [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview). 

[Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) můžete nainstalovat na místní počítač a používat v jakékoli relaci PowerShellu. Podle pokynů na stránce se přihlaste pomocí přihlašovacích údajů Azure a nainstalujte modul Az PowerShell.

#### <a name="connect-to-azure-with-an-interactive-dialog-for-sign-in"></a>Připojení k Azure pomocí interaktivního dialogového okna pro přihlášení
```
Connect-AzAccount

```
Před instalací modulu Přední dveře zkontrolujte, zda je nainstalovaná aktuální verze PowerShellGet. Spusťte následující příkaz a znovu otevřete prostředí PowerShell.

```
Install-Module PowerShellGet -Force -AllowClobber
``` 

#### <a name="install-azfrontdoor-module"></a>Instalace modulu Az.FrontDoor 

```
Install-Module -Name Az.FrontDoor
```
### <a name="create-a-front-door-profile"></a>Vytvoření profilu předních dveří
Vytvoření profilu předních dveří podle pokynů popsaných v [úvodním programu: Vytvoření profilu předních dveří](../../frontdoor/quickstart-create-front-door.md)

## <a name="define-url-match-conditions"></a>Definovat podmínky shody adres URL
Definujte podmínku shody adresy URL (adresa URL obsahuje /promo) pomocí [new-azfrontdoorwafmatchconditionobject](/powershell/module/az.frontdoor/new-azfrontdoorwafmatchconditionobject).
Následující příklad odpovídá */promo* jako hodnotu proměnné *RequestUri:*

```powershell-interactive
   $promoMatchCondition = New-AzFrontDoorWafMatchConditionObject `
     -MatchVariable RequestUri `
     -OperatorProperty Contains `
     -MatchValue "/promo"
```
## <a name="create-a-custom-rate-limit-rule"></a>Vytvoření pravidla vlastního limitu rychlosti
Nastavte limit rychlosti pomocí [New-AzFrontDoorWafCustomRuleObject](/powershell/module/az.frontdoor/new-azfrontdoorwafcustomruleobject). V následujícím příkladu je limit nastaven na 1000. Požadavky od libovolného klienta na promo stránku přesahující 1000 během jedné minuty jsou blokovány až do začátku další minuty.

```powershell-interactive
   $promoRateLimitRule = New-AzFrontDoorWafCustomRuleObject `
     -Name "rateLimitRule" `
     -RuleType RateLimitRule `
     -MatchCondition $promoMatchCondition `
     -RateLimitThreshold 1000 `
     -Action Block -Priority 1
```


## <a name="configure-a-security-policy"></a>Konfigurace zásad zabezpečení

Vyhledejte název skupiny prostředků, která obsahuje `Get-AzureRmResourceGroup`profil Přední dveře pomocí aplikace . Dále nakonfigurujte zásady zabezpečení s vlastním pravidlem omezení rychlosti pomocí [new-azfrontdoorwafPolicy](/powershell/module/az.frontdoor/new-azfrontdoorwafpolicy) v zadané skupině prostředků, která obsahuje profil Přední dveře.

Níže uvedený příklad používá název skupiny prostředků *myResourceGroupFD1* s předpokladem, že jste vytvořili profil Přední dveře podle pokynů uvedených v [článku Rychlý start: Vytvoření předních dveří.](../../frontdoor/quickstart-create-front-door.md)

 pomocí [New-AzFrontDoorWafPolicy](/powershell/module/az.frontdoor/new-azfrontdoorwafpolicy).

```powershell-interactive
   $ratePolicy = New-AzFrontDoorWafPolicy `
     -Name "RateLimitPolicyExamplePS" `
     -resourceGroupName myResourceGroupFD1 `
     -Customrule $promoRateLimitRule `
     -Mode Prevention `
     -EnabledState Enabled
```
## <a name="link-policy-to-a-front-door-front-end-host"></a>Propojení zásad s front-endovým hostitelem předních dveří
Propojte objekt zásad zabezpečení s existujícím front-end hostitelem předních dveří a aktualizujte vlastnosti předních dveří. Nejprve načtěte objekt Front Door pomocí [příkazu Get-AzFrontDoor.](/powershell/module/Az.FrontDoor/Get-AzFrontDoor)
Dále nastavte vlastnost front-end *WebApplicationFirewallPolicyLink* na *id "$ratePolicy"* vytvořenév předchozím kroku pomocí [příkazu Set-AzFrontDoor.](/powershell/module/Az.FrontDoor/Set-AzFrontDoor) 

Níže uvedený příklad používá název skupiny prostředků *myResourceGroupFD1* s předpokladem, že jste vytvořili profil Přední dveře podle pokynů uvedených v [článku Rychlý start: Vytvoření předních dveří.](../../frontdoor/quickstart-create-front-door.md) V níže uvedeném příkladu také nahraďte $frontDoorName názvem profilu předních dveří. 

```powershell-interactive
   $FrontDoorObjectExample = Get-AzFrontDoor `
     -ResourceGroupName myResourceGroupFD1 `
     -Name $frontDoorName
   $FrontDoorObjectExample[0].FrontendEndpoints[0].WebApplicationFirewallPolicyLink = $ratePolicy.Id
   Set-AzFrontDoor -InputObject $FrontDoorObjectExample[0]
 ```

> [!NOTE]
> Chcete-li propojit zásady zabezpečení s front-endem předních dveří, stačí nastavit vlastnost *WebApplicationFirewallPolicyLink* pouze jednou. Následné aktualizace zásad jsou automaticky použity na front-end.

## <a name="next-steps"></a>Další kroky

- Další informace o [předních dveřích](../../frontdoor/front-door-overview.md). 


