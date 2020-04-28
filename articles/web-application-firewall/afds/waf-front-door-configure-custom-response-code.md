---
title: Konfigurace vlastní odpovědi pro WAF s využitím front-dveří Azure
description: Zjistěte, jak nakonfigurovat vlastní kód odpovědi a zprávu, když Firewall webových aplikací (WAF) blokuje požadavek.
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.topic: article
ms.date: 08/21/2019
ms.author: victorh
ms.reviewer: tyao
ms.openlocfilehash: 215d4058937ad5fded6bef7a36e873b52a1b5ae9
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "74185345"
---
# <a name="configure-a-custom-response-for-azure-web-application-firewall"></a>Konfigurace vlastní odpovědi pro Firewall webových aplikací Azure

Ve výchozím nastavení platí, že když Firewall webových aplikací Azure (WAF) s frontou Azure zablokuje požadavek z důvodu spárovaného pravidla, vrátí stavový kód 403 s **požadavkem na zablokování** zprávy. Tento článek popisuje, jak nakonfigurovat vlastní kód stavu odpovědi a zprávu s odpovědí, když je požadavek zablokován nástrojem WAF.

## <a name="set-up-your-powershell-environment"></a>Nastavení prostředí PowerShell
Prostředí Azure PowerShell poskytuje sadu rutin, které ke správě vašich prostředků Azure využívají model [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview). 

[Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) můžete nainstalovat na místní počítač a používat v jakékoli relaci PowerShellu. Postupujte podle pokynů na stránce, přihlaste se pomocí přihlašovacích údajů Azure a nainstalujte modul AZ PowerShell.

### <a name="connect-to-azure-with-an-interactive-dialog-for-sign-in"></a>Připojení k Azure pomocí interaktivního dialogu pro přihlášení
```
Connect-AzAccount
Install-Module -Name Az
```
Ujistěte se, že máte nainstalovanou aktuální verzi PowerShellGet. Spusťte následující příkaz a znovu otevřete PowerShell.

```
Install-Module PowerShellGet -Force -AllowClobber
``` 
### <a name="install-azfrontdoor-module"></a>Instalace AZ. FrontDoor Module 

```
Install-Module -Name Az.FrontDoor
```

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

V Azure přidělíte související prostředky skupině prostředků. V tomto příkladu vytvoříte skupinu prostředků pomocí [New-AzResourceGroup](/powershell/module/Az.resources/new-Azresourcegroup).

```azurepowershell-interactive
New-AzResourceGroup -Name myResourceGroupWAF
```

## <a name="create-a-new-waf-policy-with-custom-response"></a>Vytvoření nové zásady WAF s vlastní odpovědí 

Níže je uveden příklad vytvoření nové zásady WAF s vlastním stavovým kódem odpovědi nastaveným na 405 a zpráva, **kterou jste zablokovali.** pomocí [New-AzFrontDoorWafPolicy](/powershell/module/az.frontdoor/new-azfrontdoorwafpolicy).

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

Pomocí [Update-AzFrontDoorFireWallPolicy](/powershell/module/az.frontdoor/Update-AzFrontDoorWafPolicy)upravte vlastní kód odpovědi nebo nastavení těla zprávy odpovědi pro existující zásady WAF.

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
- Další informace o [firewallu webových aplikací s využitím front-dveří Azure](../afds/afds-overview.md)