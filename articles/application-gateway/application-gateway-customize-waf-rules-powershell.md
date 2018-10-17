---
title: Přizpůsobení pravidel firewallu webových aplikací ve službě Azure Application Gateway – PowerShell | Dokumentace Microsoftu
description: Tento článek obsahuje informace o tom, jak přizpůsobení pravidel firewallu webových aplikací ve službě Application Gateway pomocí Powershellu.
documentationcenter: na
services: application-gateway
author: vhorne
manager: jpconnock
editor: tysonn
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.custom: ''
ms.workload: infrastructure-services
ms.date: 07/26/2017
ms.author: victorh
ms.openlocfilehash: b341bdbe6611187b158f353d00077d33f317f374
ms.sourcegitcommit: 6361a3d20ac1b902d22119b640909c3a002185b3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/17/2018
ms.locfileid: "49365420"
---
# <a name="customize-web-application-firewall-rules-through-powershell"></a>Přizpůsobení pravidel firewallu webových aplikací pomocí prostředí PowerShell

> [!div class="op_single_selector"]
> * [Azure Portal](application-gateway-customize-waf-rules-portal.md)
> * [PowerShell](application-gateway-customize-waf-rules-powershell.md)
> * [Azure CLI](application-gateway-customize-waf-rules-cli.md)

Firewall webových aplikací (WAF) Azure Application Gateway chrání webové aplikace. Tyto ochrany jsou k dispozici ve Open Web Application zabezpečení projektu (OWASP) základní pravidlo nastavte (CRS). Některá pravidla může způsobit, že počet falešně pozitivních výsledků a blokovat skutečný provoz. Z tohoto důvodu Application Gateway poskytuje schopnost přizpůsobit skupin pravidel a pravidla. Další informace o příslušné pravidlo skupiny a pravidel, naleznete v tématu [seznam skupin pravidel CRS brány firewall webových aplikací a pravidla](application-gateway-crs-rulegroups-rules.md).

## <a name="view-rule-groups-and-rules"></a>Zobrazení skupin pravidel a pravidla

Následující příklady kódu ukazují, jak zobrazení pravidel a skupin pravidel, které se dají konfigurovat na povoleným waf služby application gateway.

### <a name="view-rule-groups"></a>Zobrazit pravidla skupiny

Následující příklad ukazuje, jak zobrazit skupiny pravidel:

```powershell
Get-AzureRmApplicationGatewayAvailableWafRuleSets
```

Následující výstup je zkrácený odpovědi z předchozího příkladu:

```
OWASP (Ver. 3.0):

    REQUEST-910-IP-REPUTATION:
        Description:
            
        Rules:
            RuleId     Description
            ------     -----------
            910011     Rule 910011
            910012     Rule 910012
            ...        ...

    REQUEST-911-METHOD-ENFORCEMENT:
        Description:
            
        Rules:
            RuleId     Description
            ------     -----------
            911011     Rule 911011
            ...        ...

OWASP (Ver. 2.2.9):

    crs_20_protocol_violations:
        Description:
            
        Rules:
            RuleId     Description
            ------     -----------
            960911     Invalid HTTP Request Line
            981227     Apache Error: Invalid URI in Request.
            960000     Attempted multipart/form-data bypass
            ...        ...
```

## <a name="disable-rules"></a>Zákaz pravidel

Následující příklad zakazuje pravidla `910018` a `910017` ve službě application gateway:

```powershell
$disabledrules=New-AzureRmApplicationGatewayFirewallDisabledRuleGroupConfig -RuleGroupName REQUEST-910-IP-REPUTATION -Rules 910018,910017
Set-AzureRmApplicationGatewayWebApplicationFirewallConfiguration -ApplicationGateway $gw -Enabled $true -FirewallMode Detection -RuleSetVersion 3.0 -RuleSetType OWASP -DisabledRuleGroups $disabledrules
Set-AzureRmApplicationGateway -ApplicationGateway $gw
```

## <a name="next-steps"></a>Další postup

Po dokončení konfigurace zakázaná pravidla se dozvíte, jak si chcete zobrazit protokoly WAF. Další informace najdete v tématu [diagnostiku brány aplikace](application-gateway-diagnostics.md#diagnostic-logging).

[fig1]: ./media/application-gateway-customize-waf-rules-portal/1.png
[1]: ./media/application-gateway-customize-waf-rules-portal/figure1.png
[2]: ./media/application-gateway-customize-waf-rules-portal/figure2.png
[3]: ./media/application-gateway-customize-waf-rules-portal/figure3.png
