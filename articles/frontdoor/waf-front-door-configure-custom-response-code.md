---
title: Konfigurace vlastní odpovědi pro Firewall webových aplikací v Azure front-dveřích
description: Zjistěte, jak nakonfigurovat vlastní kód odpovědi a zprávu, když Firewall webových aplikací (WAF) blokuje požadavek.
services: frontdoor
author: KumudD
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/21/2019
ms.author: kumud
ms.reviewer: tyao
ms.openlocfilehash: 657dc3a43302d16bc403d790bf2c34c2d147dd6c
ms.sourcegitcommit: fa45c2bcd1b32bc8dd54a5dc8bc206d2fe23d5fb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/12/2019
ms.locfileid: "67846371"
---
# <a name="configure-a-custom-response-for-azure-web-application-firewall"></a>Konfigurace vlastní odpovědi pro Firewall webových aplikací Azure

Ve výchozím nastavení platí, že když Firewall webových aplikací Azure (WAF) s frontou Azure zablokuje požadavek z důvodu spárovaného pravidla, vrátí stavový kód 403 s **požadavkem na** zablokování zprávy. Tento článek popisuje, jak nakonfigurovat vlastní kód stavu odpovědi a zprávu s odpovědí, když je požadavek zablokován nástrojem WAF.

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

## <a name="next-steps"></a>Další postup
- Další informace o [front](front-door-overview.md) -dveřích