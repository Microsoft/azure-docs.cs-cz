---
title: Kurz – Konfigurace geografického filtrování na doméně pro Azure Front Door Service | Microsoft Docs
description: V tomto kurzu se dozvíte, jak vytvořit jednoduchou zásadu geografického filtrování a přidružit ji k existujícímu hostiteli front-endu služby Front Door.
services: frontdoor
documentationcenter: ''
author: sharad4u
editor: ''
ms.service: frontdoor
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 09/20/2018
ms.author: sharadag
ms.openlocfilehash: 68da9a0255cde6cbad5c675901c80193888bf255
ms.sourcegitcommit: e7312c5653693041f3cbfda5d784f034a7a1a8f1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/11/2019
ms.locfileid: "54214874"
---
# <a name="how-to-set-up-a-geo-filtering-policy-for-your-front-door"></a>Nastavení zásady geografického filtrování pro službu Front Door
V tomto kurzu se dozvíte, jak pomocí Azure PowerShellu vytvořit ukázkovou zásadu geografického filtrování a přidružit ji k existujícímu hostiteli front-endu služby Front Door. Tato ukázková zásada geografického filtrování bude blokovat požadavky ze všech zemí s výjimkou USA.

## <a name="1-set-up-your-powershell-environment"></a>1. Nastavení prostředí PowerShell
Prostředí Azure PowerShell poskytuje sadu rutin, které ke správě vašich prostředků Azure využívají model [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview). 

[Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) můžete nainstalovat na místní počítač a používat v jakékoli relaci PowerShellu. Podle pokynů na stránce se přihlaste pomocí svých přihlašovacích údajů Azure a nainstalujte AzureRM.
```
# Connect to Azure with an interactive dialog for sign-in
Connect-AzureRmAccount
Install-Module -Name AzureRM
```
> [!NOTE]
>  Brzy bude k dispozici podpora služby [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview).

Před instalací modulu Front Door se ujistěte, že máte nainstalovanou aktuální verzi modulu PowerShellGet. Spusťte následující příkaz a znovu otevřete PowerShell.

```
Install-Module PowerShellGet -Force -AllowClobber
``` 

Nainstalujte modul AzureRM.FrontDoor. 

```
Install-Module -Name AzureRM.FrontDoor -AllowPrerelease
```

## <a name="2-define-geo-filtering-match-conditions"></a>2. Definice podmínek shody geografického filtrování
Nejprve vytvořte ukázkovou podmínku shody, která vybere požadavky, které nepochází z oblasti USA. Informace o parametrech při vytváření podmínky shody najdete v [příručce](https://docs.microsoft.com/azure/frontdoor/new-azurermfrontdoormatchconditionobject) k PowerShellu. Dvoupísmenné kódy zemí pro mapování země najdete [tady](front-door-geo-filtering.md).

```
$nonUSGeoMatchCondition = New-AzureRmFrontDoorMatchConditionObject -MatchVariable RemoteAddr -OperatorProperty GeoMatch -NegateCondition $true -MatchValue "US"
```
 
## <a name="3-add-geo-filtering-match-condition-to-a-rule-with-action-and-priority"></a>3. Přidání podmínky shody geografického filtrování do pravidla s parametry Action a Priority

Dále vytvořte objekt CustomRule `nonUSBlockRule` na základě podmínky shody a parametrů Action a Priority.  Objekt CustomRule může mít více podmínek shody MatchCondition.  V tomto příkladu se parametr Action nastaví na Block a parametr Priority na 1, což je nejvyšší priorita.

```
$nonUSBlockRule = New-AzureRmFrontDoorCustomRuleObject -Name "geoFilterRule" -RuleType MatchRule -MatchCondition $nonUSGeoMatchCondition -Action Block -Priority 1
```

Informace o parametrech při vytváření objektu CustomRuleObject najdete v [příručce](https://docs.microsoft.com/azure/frontdoor/new-azurermfrontdoorcustomruleobject) k PowerShellu.

## <a name="4-add-rules-to-a-policy"></a>4. Přidání pravidel do zásady
V tomto kroku se v zadané skupině prostředků vytvoří objekt zásad `geoPolicy` obsahující pravidlo `nonUSBlockRule` z předchozího kroku. Pomocí rutiny `Get-AzureRmResourceGroup` vyhledejte hodnotu $resourceGroup pro parametr ResourceGroupName.

```
$geoPolicy = New-AzureRmFrontDoorFireWallPolicy -Name "geoPolicyAllowUSOnly" -resourceGroupName $resourceGroup -Customrule $nonUSBlockRule  -Mode Prevention -EnabledState Enabled
```

Informace o parametrech při vytváření zásady najdete v [příručce](https://docs.microsoft.com/azure/frontdoor/new-azurermfrontdoorfirewallpolicy) k PowerShellu.

## <a name="5-link-policy-to-a-front-door-frontend-host"></a>5. Propojení zásady s hostitelem front-endu služby Front Door
Posledními kroky je propojení objektu zásad ochrany s existujícím hostitelem front-endu služby Front Door a aktualizace vlastností služby Front Door. Nejprve pomocí rutiny [Get-AzureRmFrontDoor](https://docs.microsoft.com/azure/frontdoor/get-azurermfrontdoor) načtete objekt vaší služby Front Door a pak nastavíte vlastnost WebApplicationFirewallPolicyLink jejího front-endu na ID prostředku `geoPolicy`.

```
$geoFrontDoorObjectExample = Get-AzureRmFrontDoor -ResourceGroupName $resourceGroup
$geoFrontDoorObjectExample[0].FrontendEndpoints[0].WebApplicationFirewallPolicyLink = $geoPolicy.Id
```

Pomocí následujícího [příkazu](https://docs.microsoft.com/azure/frontdoor/set-azurermfrontdoor) aktualizujte objekt vaší služby Front Door.

```
Set-AzureRmFrontDoor -InputObject $geoFrontDoorObjectExample[0]
```

> [!NOTE] 
> K propojení zásady ochrany s hostitelem front-endu služby Front Door stačí nastavit vlastnost WebApplicationFirewallPolicyLink pouze jednou. Další aktualizace zásad se budou u hostitele front-endu uplatňovat automaticky.

## <a name="next-steps"></a>Další postup

- Přečtěte si o [zabezpečení aplikační vrstvy pomocí služby Front Door](front-door-application-security.md).
- Přečtěte si, jak [vytvořit službu Front Door](quickstart-create-front-door.md).
