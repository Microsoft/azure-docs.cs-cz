---
title: Přizpůsobení pravidel firewallu webových aplikací ve službě Azure Application Gateway – PowerShell
description: Tento článek obsahuje informace o tom, jak přizpůsobení pravidel firewallu webových aplikací ve službě Application Gateway pomocí Powershellu.
services: application-gateway
author: vhorne
ms.service: application-gateway
origin.date: 02/22/2019
ms.date: 03/11/2019
ms.author: v-junlch
ms.openlocfilehash: f96395a54f66b787878faeee057f02818f956ade
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60831448"
---
# <a name="customize-web-application-firewall-rules-through-powershell"></a>Přizpůsobení pravidel firewallu webových aplikací pomocí prostředí PowerShell

Firewall webových aplikací (WAF) Azure Application Gateway chrání webové aplikace. Tyto ochrany jsou k dispozici ve Open Web Application zabezpečení projektu (OWASP) základní pravidlo nastavte (CRS). Některá pravidla může způsobit, že počet falešně pozitivních výsledků a blokovat skutečný provoz. Z tohoto důvodu Application Gateway poskytuje schopnost přizpůsobit skupin pravidel a pravidla. Další informace o příslušné pravidlo skupiny a pravidel, naleznete v tématu [seznam skupin pravidel CRS brány firewall webových aplikací a pravidla](application-gateway-crs-rulegroups-rules.md).

## <a name="view-rule-groups-and-rules"></a>Zobrazení skupin pravidel a pravidla

Následující příklady kódu ukazují, jak zobrazení pravidel a skupin pravidel, které se dají konfigurovat na povoleným waf služby application gateway.

### <a name="view-rule-groups"></a>Zobrazit pravidla skupiny

Následující příklad ukazuje, jak zobrazit skupiny pravidel:

```powershell
Get-AzApplicationGatewayAvailableWafRuleSets
```

Následující výstup je zkrácený odpovědi z předchozího příkladu:

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

## <a name="disable-rules"></a>Zákaz pravidel

Následující příklad zakazuje pravidla `911011` a `911012` ve službě application gateway:

```powershell
$disabledrules=New-AzApplicationGatewayFirewallDisabledRuleGroupConfig -RuleGroupName REQUEST-911-METHOD-ENFORCEMENT -Rules 911011,911012
Set-AzApplicationGatewayWebApplicationFirewallConfiguration -ApplicationGateway $gw -Enabled $true -FirewallMode Detection -RuleSetVersion 3.0 -RuleSetType OWASP -DisabledRuleGroups $disabledrules
Set-AzApplicationGateway -ApplicationGateway $gw
```

## <a name="mandatory-rules"></a>Povinné pravidla

Následující seznam obsahuje podmínky, které způsobí WAF blokování požadavku v režimu ochrany před únikem informací (v režimu detekce, kterému jsou přihlášeni jako výjimky). Tyto nelze nakonfigurované nebo zakázané:

* Nepodařilo se analyzovat datovou část požadavku výsledkem požadavku blokován, pokud je text kontroly nevypnuli (XML, JSON, data formuláře)
* Délka dat požadavku textu (spolu s žádné soubory) je větší než nakonfigurovaný limit
* Text (včetně souborů) je větší než limit požadavku
* Došlo k vnitřní chybě v modulu WAF

CRS 3.x konkrétní:

* Příchozí anomálií skóre překročil prahovou hodnotu

## <a name="next-steps"></a>Další postup

Po dokončení konfigurace zakázaná pravidla se dozvíte, jak si chcete zobrazit protokoly WAF. Další informace najdete v tématu [diagnostiku brány aplikace](application-gateway-diagnostics.md#diagnostic-logging).

[fig1]: ./media/application-gateway-customize-waf-rules-portal/1.png
[1]: ./media/application-gateway-customize-waf-rules-portal/figure1.png
[2]: ./media/application-gateway-customize-waf-rules-portal/figure2.png
[3]: ./media/application-gateway-customize-waf-rules-portal/figure3.png

<!-- Update_Description: code update -->