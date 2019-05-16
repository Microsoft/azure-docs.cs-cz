---
title: Konfigurace vlastní odpovědi za firewall webových aplikací v Azure branou
description: Zjistěte, jak nakonfigurovat vlastní odpovědi a zprávy, když blokuje firewall webových aplikací (WAF) žádost o.
services: frontdoor
author: KumudD
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/08/2019
ms.author: tyao;kumud
ms.openlocfilehash: 7a167deb511347798fa609e2aca2a19f8bf12d21
ms.sourcegitcommit: bb85a238f7dbe1ef2b1acf1b6d368d2abdc89f10
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/10/2019
ms.locfileid: "65523722"
---
# <a name="configure-a-custom-response-for-azure-web-application-firewall"></a>Konfigurace vlastní odpovědi pro brány firewall webových aplikací

Ve výchozím nastavení, pokud firewall webových aplikací (WAF) s Azure branou blokuje požadavek z důvodu odpovídající pravidlo, vrátí 403 stavový kód s **požadavek je blokován** zprávy. Tento článek popisuje, jak nakonfigurovat stavový kód vlastní odpovědi a zprávy s odpovědí, když požadavek je blokován WAF.

## <a name="set-up-your-powershell-environment"></a>Nastavení prostředí PowerShell
Prostředí Azure PowerShell poskytuje sadu rutin, které ke správě vašich prostředků Azure využívají model [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview). 

[Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) můžete nainstalovat na místní počítač a používat v jakékoli relaci PowerShellu. Na stránce, přihlaste se pomocí přihlašovacích údajů Azure, postupujte podle pokynů a nainstalujte modul prostředí PowerShell Az.

### <a name="connect-to-azure-with-an-interactive-dialog-for-sign-in"></a>Připojení k Azure pomocí interaktivního dialogové okno pro přihlášení
```
Connect-AzAccount
Install-Module -Name Az
```
Ujistěte se, že máte aktuální verzi modul PowerShellGet nainstalovaný. Spusťte následující příkaz a znovu otevřete PowerShell.

```
Install-Module PowerShellGet -Force -AllowClobber
``` 
### <a name="install-azfrontdoor-module"></a>Instalace modulu Az.FrontDoor 

```
Install-Module -Name Az.FrontDoor
```

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

V Azure přidělení související prostředky do skupiny prostředků. V tomto příkladu vytvoříte skupinu prostředků pomocí [New-AzResourceGroup](/powershell/module/Az.resources/new-Azresourcegroup).

```azurepowershell-interactive
New-AzResourceGroup -Name myResourceGroupWAF
```

## <a name="create-a-new-waf-policy-with-custom-response"></a>Vytvořit novou zásadu WAF s vlastní odpovědi 

Níže je příklad vytvoření nové zásady WAF se stavovým kódem vlastní odpovědi nastavena na 405 a zpráva, která má **jsou zablokované.** pomocí [New-AzFrontDoorWafPolicy](/powershell/module/az.frontdoor/new-azfrontdoorwafpolicy).

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

Upravit vlastní odpovědi nebo nastavení tělo odpovědi u existující zásady WAF, pomocí [Set-AzFrontDoor](/powershell/module/az.frontdoor/set-azfrontdoor).

```azurepowershell
# modify WAF response code
Set-AzFrontDoorFireWallPolicy `
-Name myWAFPolicy `
-ResourceGroupName myResourceGroupWAF `
-EnabledState enabled `
-Mode Detection `
-CustomBlockResponseStatusCode 403
```

```azurepowershell
# modify WAF response body
Set-AzFrontDoorFireWallPolicy `
-Name myWAFPolicy `
-ResourceGroupName myResourceGroupWAF `
-CustomBlockResponseBody "<html><head><title> Forbidden</title></head><body></body></html>"
```

## <a name="next-steps"></a>Další postup
- Další informace o [branou](front-door-overview.md)