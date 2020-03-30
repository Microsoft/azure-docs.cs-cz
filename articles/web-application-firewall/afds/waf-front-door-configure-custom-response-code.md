---
title: Konfigurace vlastní odpovědi pro WAF pomocí azure front door
description: Přečtěte si, jak nakonfigurovat vlastní kód odpovědi a zprávu, když brána WAF (Web Application Firewall) blokuje požadavek.
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.topic: article
ms.date: 08/21/2019
ms.author: victorh
ms.reviewer: tyao
ms.openlocfilehash: 215d4058937ad5fded6bef7a36e873b52a1b5ae9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74185345"
---
# <a name="configure-a-custom-response-for-azure-web-application-firewall"></a>Konfigurace vlastní odpovědi pro bránu Firewall webových aplikací Azure

Ve výchozím nastavení, když Azure Web Application Firewall (WAF) s Azure Front Door blokuje požadavek z důvodu odpovídající pravidlo, vrátí stavový kód 403 s **požadavek je blokován** a zpráva. Tento článek popisuje, jak nakonfigurovat vlastní kód stavu odpovědi a zprávu odpovědi, když je požadavek blokován waf.

## <a name="set-up-your-powershell-environment"></a>Nastavení prostředí PowerShell
Prostředí Azure PowerShell poskytuje sadu rutin, které ke správě vašich prostředků Azure využívají model [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview). 

[Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) můžete nainstalovat na místní počítač a používat v jakékoli relaci PowerShellu. Podle pokynů na stránce se přihlaste pomocí přihlašovacích údajů Azure a nainstalujte modul Az PowerShell.

### <a name="connect-to-azure-with-an-interactive-dialog-for-sign-in"></a>Připojení k Azure pomocí interaktivního dialogového okna pro přihlášení
```
Connect-AzAccount
Install-Module -Name Az
```
Ujistěte se, že máte nainstalovanou aktuální verzi PowerShellGet. Spusťte následující příkaz a znovu otevřete PowerShell.

```
Install-Module PowerShellGet -Force -AllowClobber
``` 
### <a name="install-azfrontdoor-module"></a>Instalace modulu Az.FrontDoor 

```
Install-Module -Name Az.FrontDoor
```

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

V Azure přidělujete související prostředky skupině prostředků. V tomto příkladu vytvoříte skupinu prostředků pomocí [new-azresourcegroup](/powershell/module/Az.resources/new-Azresourcegroup).

```azurepowershell-interactive
New-AzResourceGroup -Name myResourceGroupWAF
```

## <a name="create-a-new-waf-policy-with-custom-response"></a>Vytvoření nové zásady WAF s vlastní odpovědí 

Níže je uveden příklad vytvoření nové zásady WAF s vlastním kódem stavu odpovědi nastaveným na 405 a zprávou **Na Vás jsou blokovány.** pomocí [New-AzFrontDoorWafPolicy](/powershell/module/az.frontdoor/new-azfrontdoorwafpolicy).

```azurepowershell
# WAF policy setting
New-AzFrontDoorWafPolicy `
-Name myWAFPolicy `
-ResourceGroupName myResourceGroupWAF `
-EnabledState enabled `
-Mode Detection `
-CustomBlockResponseStatusCode 405 `
-CustomBlockResponseBody "<html><head><title>You are blocked.</title></head><body></body></html>"
```

Upravte vlastní nastavení kódu odpovědi nebo těla odpovědi existující zásady WAF pomocí [update-AzFrontDoorFireWallPolicy](/powershell/module/az.frontdoor/Update-AzFrontDoorWafPolicy).

```azurepowershell
# modify WAF response code
Update-AzFrontDoorFireWallPolicy `
-Name myWAFPolicy `
-ResourceGroupName myResourceGroupWAF `
-EnabledState enabled `
-Mode Detection `
-CustomBlockResponseStatusCode 403
```

```azurepowershell
# modify WAF response body
Update-AzFrontDoorFireWallPolicy `
-Name myWAFPolicy `
-ResourceGroupName myResourceGroupWAF `
-CustomBlockResponseBody "<html><head><title> Forbidden</title></head><body></body></html>"
```

## <a name="next-steps"></a>Další kroky
- Další informace o [bráně firewall webových aplikací s Azure Front Door](../afds/afds-overview.md)