---
title: Konfigurace vlastních odpovědí pro Firewall webových aplikací (WAF) pomocí front-dveří Azure
description: Zjistěte, jak nakonfigurovat vlastní kód odpovědi a zprávu, když WAF zablokuje požadavek.
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.topic: article
ms.date: 06/10/2020
ms.author: victorh
ms.reviewer: tyao
ms.openlocfilehash: a995460793686d8293d77965e74e2cbf916925a0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87005595"
---
# <a name="configure-a-custom-response-for-azure-web-application-firewall-waf"></a>Konfigurace vlastní odpovědi pro Firewall webových aplikací Azure (WAF)

Ve výchozím nastavení, když WAF zablokuje požadavek z důvodu spárovaného pravidla, vrátí stavový kód 403 s **požadavkem na zablokování** zprávy. Výchozí zpráva obsahuje také řetězec sledovacích odkazů, který lze použít k propojení na [položky protokolu](https://docs.microsoft.com/azure/web-application-firewall/afds/waf-front-door-monitor) pro požadavek.  Můžete nakonfigurovat vlastní kód stavu odpovědi a vlastní zprávu s referenčním řetězcem pro váš případ použití. Tento článek popisuje, jak nakonfigurovat vlastní stránku odpovědi, když WAF zablokuje požadavek.

## <a name="configure-custom-response-status-code-and-message-use-portal"></a>Konfigurace vlastního kódu stavu odpovědí a portálu pro používání zpráv

V části nastavení zásad můžete na portálu WAF nakonfigurovat vlastní kód stavu odpovědi a text.

:::image type="content" source="../media/waf-front-door-configure-custom-response-code/custom-response-settings.png" alt-text="Nastavení zásad WAF":::

Ve výše uvedeném příkladu jsme kód odpovědi zavedli jako 403 a nakonfigurovali krátký symbol "kontaktujte nás", jak je znázorněno na následujícím obrázku:

:::image type="content" source="../media/waf-front-door-configure-custom-response-code/custom-response.png" alt-text="Nastavení zásad WAF":::

{{Azure-ref}} vloží do těla odpovědi jedinečný referenční řetězec. Hodnota odpovídá poli TrackingReference v `FrontdoorAccessLog` `FrontdoorWebApplicationFirewallLog` protokolech a.

## <a name="configure-custom-response-status-code-and-message-use-powershell"></a>Konfigurace vlastního kódu stavu odpovědi a zprávy použití PowerShellu

### <a name="set-up-your-powershell-environment"></a>Nastavení prostředí PowerShell

Prostředí Azure PowerShell poskytuje sadu rutin, které ke správě vašich prostředků Azure využívají model [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview). 

[Azure PowerShell](https://docs.microsoft.com/powershell/azure/) můžete nainstalovat na místní počítač a používat v jakékoli relaci PowerShellu. Postupujte podle pokynů na stránce, přihlaste se pomocí přihlašovacích údajů Azure a nainstalujte modul AZ PowerShell.

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

### <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

V Azure přidělíte související prostředky skupině prostředků. Tady vytvoříme skupinu prostředků pomocí [New-AzResourceGroup](/powershell/module/Az.resources/new-Azresourcegroup).

```azurepowershell-interactive
New-AzResourceGroup -Name myResourceGroupWAF
```

### <a name="create-a-new-waf-policy-with-custom-response"></a>Vytvoření nové zásady WAF s vlastní odpovědí 

Níže je uveden příklad vytvoření nové zásady WAF s vlastním stavovým kódem odpovědi nastaveným na 405 a zpráva, **kterou jste zablokovali.** použijte [New-AzFrontDoorWafPolicy](/powershell/module/az.frontdoor/new-azfrontdoorwafpolicy) .

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
-CustomBlockResponseBody "<html><head><title>Forbidden</title></head><body>{{azure-ref}}</body></html>"
```

## <a name="next-steps"></a>Další kroky
- Další informace o [firewallu webových aplikací s využitím front-dveří Azure](../afds/afds-overview.md)