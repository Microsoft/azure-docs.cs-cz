---
title: Přizpůsobení pravidel pomocí PowerShellu
titleSuffix: Azure Web Application Firewall
description: Tento článek obsahuje informace o přizpůsobení pravidel brány firewall webových aplikací v application gateway pomocí prostředí PowerShell.
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.date: 11/14/2019
ms.author: victorh
ms.topic: article
ms.openlocfilehash: 55eea15da8c3a10b0421ff1576082d6b42fc7c56
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74048508"
---
# <a name="customize-web-application-firewall-rules-using-powershell"></a>Přizpůsobení pravidel brány firewall webových aplikací pomocí prostředí PowerShell

Brána webových aplikací Azure Application Gateway (WAF) poskytuje ochranu webových aplikací. Tyto ochrany jsou poskytovány základní sadou pravidel projektu Zabezpečení otevřených webových aplikací (OWASP) (CRS). Některá pravidla mohou způsobit falešně pozitivní výsledky a blokovat skutečný provoz. Z tohoto důvodu aplikace Gateway poskytuje možnost přizpůsobit skupiny pravidel a pravidla. Další informace o konkrétních skupinách pravidel a pravidlech naleznete v [tématu Seznam skupin a pravidel souborů a pravidel brány FIREWALL brány firewall webových aplikací](application-gateway-crs-rulegroups-rules.md).

## <a name="view-rule-groups-and-rules"></a>Zobrazení skupin pravidel a pravidel

Následující příklady kódu ukazují, jak zobrazit pravidla a skupiny pravidel, které lze konfigurovat na aplikační bráně s podporou WAF.

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

Následující příklad zakáže pravidla `911011` a `911012` na bráně aplikace:

```powershell
$disabledrules=New-AzApplicationGatewayFirewallDisabledRuleGroupConfig -RuleGroupName REQUEST-911-METHOD-ENFORCEMENT -Rules 911011,911012
Set-AzApplicationGatewayWebApplicationFirewallConfiguration -ApplicationGateway $gw -Enabled $true -FirewallMode Detection -RuleSetVersion 3.0 -RuleSetType OWASP -DisabledRuleGroups $disabledrules
Set-AzApplicationGateway -ApplicationGateway $gw
```

## <a name="mandatory-rules"></a>Závazná pravidla

Následující seznam obsahuje podmínky, které způsobují WAF blokovat požadavek v režimu prevence (v režimu detekce jsou zaznamenány jako výjimky). Nelze je konfigurovat ani zakázat:

* Pokud se nepodaří analyzovat tělo požadavku, bude požadavek blokován, pokud není vypnutá kontrola těla (XML, JSON, data formuláře)
* Tělo požadavku (bez souborů) délka dat je větší než nakonfigurovaný limit
* Tělo požadavku (včetně souborů) je větší než limit
* V motoru WAF došlo k vnitřní chybě.

Specifické pro CRS 3.x:

* Překročení limitu vstupní anomálie

## <a name="next-steps"></a>Další kroky

Po konfiguraci zakázaných pravidel se můžete dozvědět, jak zobrazit protokoly WAF. Další informace naleznete v [tématu Diagnostika aplikační brány](../../application-gateway/application-gateway-diagnostics.md#diagnostic-logging).

[fig1]: ./media/application-gateway-customize-waf-rules-portal/1.png
[1]: ./media/application-gateway-customize-waf-rules-portal/figure1.png
[2]: ./media/application-gateway-customize-waf-rules-portal/figure2.png
[3]: ./media/application-gateway-customize-waf-rules-portal/figure3.png
