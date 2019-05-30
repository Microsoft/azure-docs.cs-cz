---
title: Konfigurace pravidla limit frekvence brány firewall webové aplikace pro branou – Azure PowerShell
description: Zjistěte, jak nakonfigurovat limit pravidlo frekvence pro existující koncový bod branou.
services: frontdoor
documentationcenter: ''
author: KumudD
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/16/2019
ms.author: kumud;tyao
ms.openlocfilehash: 99b0cab3fd277f90a675f0e6087d572853053a08
ms.sourcegitcommit: 3d4121badd265e99d1177a7c78edfa55ed7a9626
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/30/2019
ms.locfileid: "66387336"
---
# <a name="configure-a-web-application-firewall-rate-limit-rule-using-azure-powershell"></a>Konfigurace webové aplikace pravidlo brány firewall míra limit pomocí Azure Powershellu
Azure web application firewall (WAF) frekvence limit pravidlo pro Azure branou určuje počet požadavků umožněných z IP adresy konkrétního klienta po dobu jedné minuty.
Tento článek popisuje, jak nakonfigurovat pravidlo limit frekvence WAF, která určuje počet požadavků umožněných z jednoho klienta pro webovou aplikaci, která obsahuje */promo* v adrese URL, pomocí Azure Powershellu.

> [!IMPORTANT]
> Funkci pravidla limitu WAF kurz pro Azure branou je aktuálně ve verzi public preview.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky
Před zahájením nastavení zásady omezení četnosti, nastavte si prostředí PowerShell a vytvořit profil branou.
### <a name="set-up-your-powershell-environment"></a>Nastavení prostředí PowerShell
Prostředí Azure PowerShell poskytuje sadu rutin, které ke správě vašich prostředků Azure využívají model [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview). 

[Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) můžete nainstalovat na místní počítač a používat v jakékoli relaci PowerShellu. Na stránce, přihlaste se pomocí přihlašovacích údajů Azure, postupujte podle pokynů a nainstalujte modul prostředí PowerShell Az.

#### <a name="connect-to-azure-with-an-interactive-dialog-for-sign-in"></a>Připojení k Azure pomocí interaktivního dialogové okno pro přihlášení
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

## <a name="define-url-match-conditions"></a>Definovat podmínky shody adresy url
Definujte podmínku shody adresy URL (adresa URL obsahuje /promo) pomocí [New-AzFrontDoorWafMatchConditionObject](/powershell/module/az.frontdoor/new-azfrontdoorwafmatchconditionobject).
Následující příklad porovnává */promo* jako hodnotu *RequestUri* proměnné:

```powershell-interactive
   $promoMatchCondition = New-AzFrontDoorWafMatchConditionObject `
     -MatchVariable RequestUri `
     -OperatorProperty Contains `
     -MatchValue "/promo"
```
## <a name="create-a-custom-rate-limit-rule"></a>Vytvořit pravidlo limit vlastní míry
Nastavit limit frekvence pomocí [New-AzFrontDoorWafCustomRuleObject](/powershell/module/az.frontdoor/new-azfrontdoorwafcustomruleobject). V následujícím příkladu je omezení nastaveno na hodnotu 1000. Požadavky z libovolného klienta na stránce propagační vyšší než 1 000 během jedné minuty jsou blokovány, dokud nezačne další minutu.

```powershell-interactive
   $promoRateLimitRule = New-AzFrontDoorWafCustomRuleObject `
     -Name "rateLimitRule" `
     -RuleType RateLimitRule `
     -MatchCondition $promoMatchCondition `
     -RateLimitThreshold 1000 `
     -Action Block -Priority 1
```


## <a name="configure-a-security-policy"></a>Konfigurace zásad zabezpečení

Najít název skupiny prostředků, která obsahuje pomocí profilu branou `Get-AzureRmResourceGroup`. V dalším kroku nakonfigurujte zásady zabezpečení s pravidlo limit vlastní míry pomocí [New-AzFrontDoorWafPolicy](/powershell/module/az.frontdoor/new-azfrontdoorwafpolicy) v zadané skupině prostředků, který obsahuje profil branou.

Následujícím příkladu používá název skupiny prostředků *myResourceGroupFD1* za předpokladu, že jste vytvořili branou profilována za použití podle pokynů [rychlý start: Vytvoření branou](quickstart-create-front-door.md) článku.

 pomocí [New-AzFrontDoorWafPolicy](/powershell/module/az.frontdoor/new-azfrontdoorwafpolicy).

```powershell-interactive
   $ratePolicy = New-AzFrontDoorWafPolicy `
     -Name "RateLimitPolicyExamplePS" `
     -resourceGroupName myResourceGroupFD1 `
     -Customrule $promoRateLimitRule `
     -Mode Prevention `
     -EnabledState Enabled
```
## <a name="link-policy-to-a-front-door-front-end-host"></a>Odkaz zásad na hostitele front-endu branou
Propojit existujícího hostitele front-endu branou objektu zásad zabezpečení a aktualizovat vlastnosti branou. Nejdřív načtěte pomocí objektu branou [Get-AzFrontDoor](/powershell/module/Az.FrontDoor/Get-AzFrontDoor) příkazu.
Dále nastavte front-endu *WebApplicationFirewallPolicyLink* vlastnost *resourceId* z "$ratePolicy" vytvořili v předchozím kroku, použijte [Set-AzFrontDoor](/powershell/module/Az.FrontDoor/Set-AzFrontDoor) příkaz. 

Následujícím příkladu používá název skupiny prostředků *myResourceGroupFD1* za předpokladu, že jste vytvořili branou profilována za použití podle pokynů [rychlý start: Vytvoření branou](quickstart-create-front-door.md) článku. Také v následujícím příkladu nahraďte názvem vašeho profilu branou $frontDoorName. 

```powershell-interactive
   $FrontDoorObjectExample = Get-AzFrontDoor `
     -ResourceGroupName myResourceGroupFD1 `
     -Name $frontDoorName
   $FrontDoorObjectExample[0].FrontendEndpoints[0].WebApplicationFirewallPolicyLink = $ratePolicy.Id
   Set-AzFrontDoor -InputObject $FrontDoorObjectExample[0]
 ```

> [!NOTE]
> Je potřeba nastavit *WebApplicationFirewallPolicyLink* vlastnost jednou propojit zásady zabezpečení s branou front-endu. Další zásady aktualizace automaticky nainstalují do front-endu.

## <a name="next-steps"></a>Další postup

- Další informace o [branou](front-door-overview.md) 


