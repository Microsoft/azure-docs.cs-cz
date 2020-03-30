---
title: Přizpůsobení pravidel pomocí příkazového příkazového příkazu – brána firewall pro webové aplikace Azure
description: Tento článek obsahuje informace o tom, jak přizpůsobit pravidla brány firewall webových aplikací v bráně aplikace pomocí azure CLI.
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.date: 11/14/2019
ms.author: victorh
ms.topic: article
ms.openlocfilehash: 8e8aaa9458619bc937c5bb11c450f3197b92f451
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74048535"
---
# <a name="customize-web-application-firewall-rules-using-the-azure-cli"></a>Přizpůsobení pravidel brány firewall webových aplikací pomocí příkazového příkazového příkazu Azure

Brána webových aplikací Azure Application Gateway (WAF) poskytuje ochranu webových aplikací. Tyto ochrany jsou poskytovány základní sadou pravidel projektu Zabezpečení otevřených webových aplikací (OWASP) (CRS). Některá pravidla mohou způsobit falešně pozitivní výsledky a blokovat skutečný provoz. Z tohoto důvodu aplikace Gateway poskytuje možnost přizpůsobit skupiny pravidel a pravidla. Další informace o konkrétních skupinách pravidel a pravidlech naleznete v [tématu Seznam skupin a pravidel souborů crs](application-gateway-crs-rulegroups-rules.md)brány FIREWALL webové aplikace .

## <a name="view-rule-groups-and-rules"></a>Zobrazení skupin pravidel a pravidel

Následující příklady kódu ukazují, jak zobrazit pravidla a skupiny pravidel, které lze konfigurovat.

### <a name="view-rule-groups"></a>Zobrazit skupiny pravidel

Následující příklad ukazuje, jak zobrazit skupiny pravidel:

```azurecli-interactive
az network application-gateway waf-config list-rule-sets --type OWASP
```

Následující výstup je zkrácená odpověď z předchozího příkladu:

```json
[
  {
    "id": "/subscriptions//resourceGroups//providers/Microsoft.Network/applicationGatewayAvailableWafRuleSets/",
    "location": null,
    "name": "OWASP_3.0",
    "provisioningState": "Succeeded",
    "resourceGroup": "",
    "ruleGroups": [
      {
        "description": "",
        "ruleGroupName": "REQUEST-910-IP-REPUTATION",
        "rules": null
      },
      ...
    ],
    "ruleSetType": "OWASP",
    "ruleSetVersion": "3.0",
    "tags": null,
    "type": "Microsoft.Network/applicationGatewayAvailableWafRuleSets"
  },
  {
    "id": "/subscriptions//resourceGroups//providers/Microsoft.Network/applicationGatewayAvailableWafRuleSets/",
    "location": null,
    "name": "OWASP_2.2.9",
    "provisioningState": "Succeeded",
    "resourceGroup": "",
   "ruleGroups": [
      {
        "description": "",
        "ruleGroupName": "crs_20_protocol_violations",
        "rules": null
      },
      ...
    ],
    "ruleSetType": "OWASP",
    "ruleSetVersion": "2.2.9",
    "tags": null,
    "type": "Microsoft.Network/applicationGatewayAvailableWafRuleSets"
  }
]
```

### <a name="view-rules-in-a-rule-group"></a>Zobrazení pravidel ve skupině pravidel

Následující příklad ukazuje, jak zobrazit pravidla v zadané skupině pravidel:

```azurecli-interactive
az network application-gateway waf-config list-rule-sets --group "REQUEST-910-IP-REPUTATION"
```

Následující výstup je zkrácená odpověď z předchozího příkladu:

```json
[
  {
    "id": "/subscriptions//resourceGroups//providers/Microsoft.Network/applicationGatewayAvailableWafRuleSets/",
    "location": null,
    "name": "OWASP_3.0",
    "provisioningState": "Succeeded",
    "resourceGroup": "",
    "ruleGroups": [
      {
        "description": "",
        "ruleGroupName": "REQUEST-910-IP-REPUTATION",
        "rules": [
          {
            "description": "Rule 910011",
            "ruleId": 910011
          },
          ...
        ]
      }
    ],
    "ruleSetType": "OWASP",
    "ruleSetVersion": "3.0",
    "tags": null,
    "type": "Microsoft.Network/applicationGatewayAvailableWafRuleSets"
  }
]
```

## <a name="disable-rules"></a>Zakázání pravidel

Následující příklad zakáže pravidla `910018` a `910017` na bráně aplikace:

```azurecli-interactive
az network application-gateway waf-config set --resource-group AdatumAppGatewayRG --gateway-name AdatumAppGateway --enabled true --rule-set-version 3.0 --disabled-rules 910018 910017
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

Po konfiguraci zakázaných pravidel se můžete dozvědět, jak zobrazit protokoly WAF. Další informace naleznete v tématu [Diagnostika aplikační brány](../../application-gateway/application-gateway-diagnostics.md#diagnostic-logging).

[fig1]: ./media/application-gateway-customize-waf-rules-portal/1.png
[1]: ./media/application-gateway-customize-waf-rules-portal/figure1.png
[2]: ./media/application-gateway-customize-waf-rules-portal/figure2.png
[3]: ./media/application-gateway-customize-waf-rules-portal/figure3.png
