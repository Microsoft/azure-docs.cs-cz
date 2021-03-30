---
title: Přizpůsobení pravidel pomocí PowerShellu
titleSuffix: Azure Web Application Firewall
description: Tento článek poskytuje informace o tom, jak přizpůsobit pravidla firewallu webových aplikací v Application Gateway pomocí prostředí PowerShell.
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.date: 11/14/2019
ms.author: victorh
ms.topic: article
ms.openlocfilehash: 55eea15da8c3a10b0421ff1576082d6b42fc7c56
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "74048508"
---
# <a name="customize-web-application-firewall-rules-using-powershell"></a>Přizpůsobení pravidel firewallu webových aplikací pomocí PowerShellu

Firewall webových aplikací (WAF) služby Azure Application Gateway poskytuje ochranu pro webové aplikace. Tyto ochrany poskytuje základní sadu pravidel OWASP (Open Web Application Security) (počítačový systém). Některá pravidla můžou způsobit falešně pozitivní a blokují skutečný provoz. Z tohoto důvodu Application Gateway poskytuje možnost přizpůsobit skupiny pravidel a pravidla. Další informace o konkrétních skupinách pravidel a pravidlech najdete v tématu [seznam skupin pravidel a pravidel pro Firewall webových aplikací](application-gateway-crs-rulegroups-rules.md).

## <a name="view-rule-groups-and-rules"></a>Zobrazení skupin pravidel a pravidel

Následující příklady kódu ukazují, jak zobrazit pravidla a skupiny pravidel, které lze konfigurovat v bráně aplikace s podporou WAF.

### <a name="view-rule-groups"></a>Zobrazit skupiny pravidel

Následující příklad ukazuje, jak zobrazit skupiny pravidel:

```powershell
Get-AzApplicationGatewayAvailableWafRuleSets
```

Následující výstup je zkrácená odpověď z předchozího příkladu:

```
OWASP (Ver. 3.0):

    General:
        Description:

        Rules:
            RuleId     Description
            ------     -----------
            200004     Possible Multipart Unmatched Boundary.

    REQUEST-911-METHOD-ENFORCEMENT:
        Description:

        Rules:
            RuleId     Description
            ------     -----------
            911011     Rule 911011
            911012     Rule 911012
            911100     Method is not allowed by policy
            911013     Rule 911013
            911014     Rule 911014
            911015     Rule 911015
            911016     Rule 911016
            911017     Rule 911017
            911018     Rule 911018

    REQUEST-913-SCANNER-DETECTION:
        Description:

        Rules:
            RuleId     Description
            ------     -----------
            913011     Rule 913011
            913012     Rule 913012
            913100     Found User-Agent associated with security scanner
            913110     Found request header associated with security scanner
            913120     Found request filename/argument associated with security scanner
            913013     Rule 913013
            913014     Rule 913014
            913101     Found User-Agent associated with scripting/generic HTTP client
            913102     Found User-Agent associated with web crawler/bot
            913015     Rule 913015
            913016     Rule 913016
            913017     Rule 913017
            913018     Rule 913018

            ...        ...
```

## <a name="disable-rules"></a>Zakázání pravidel

Následující příklad zakáže pravidla `911011` a `911012` bránu Application Gateway:

```powershell
$disabledrules=New-AzApplicationGatewayFirewallDisabledRuleGroupConfig -RuleGroupName REQUEST-911-METHOD-ENFORCEMENT -Rules 911011,911012
Set-AzApplicationGatewayWebApplicationFirewallConfiguration -ApplicationGateway $gw -Enabled $true -FirewallMode Detection -RuleSetVersion 3.0 -RuleSetType OWASP -DisabledRuleGroups $disabledrules
Set-AzApplicationGateway -ApplicationGateway $gw
```

## <a name="mandatory-rules"></a>Povinná pravidla

Následující seznam obsahuje podmínky, které způsobí, že WAF zablokuje požadavek v režimu prevence (v režimu detekce jsou protokolovány jako výjimky). Nedají se nakonfigurovat nebo zakázat:

* Při analýze textu žádosti dojde k chybě v blokovaném požadavku, pokud není kontrola těla vypnuta (XML, JSON, data formuláře).
* Text požadavku (bez souborů) Délka dat je větší než nakonfigurovaný limit.
* Text žádosti (včetně souborů) je větší, než je limit.
* V modulu WAF došlo k vnitřní chybě.

Specifické pro počítačový počítač 3. x:

* Skóre příchozí anomálie překročilo prahovou hodnotu.

## <a name="next-steps"></a>Další kroky

Po konfiguraci zakázaných pravidel se můžete dozvědět, jak zobrazit protokoly WAF. Další informace najdete v tématu [diagnostika Application Gateway](../../application-gateway/application-gateway-diagnostics.md#diagnostic-logging).

[fig1]: ./media/application-gateway-customize-waf-rules-portal/1.png
[1]: ./media/application-gateway-customize-waf-rules-portal/figure1.png
[2]: ./media/application-gateway-customize-waf-rules-portal/figure2.png
[3]: ./media/application-gateway-customize-waf-rules-portal/figure3.png
