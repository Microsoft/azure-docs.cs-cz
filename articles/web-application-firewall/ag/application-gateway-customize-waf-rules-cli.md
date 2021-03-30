---
title: Přizpůsobení pravidel pomocí rozhraní příkazového řádku CLI – Azure Web Application firewall
description: Tento článek poskytuje informace o tom, jak přizpůsobit pravidla firewallu webových aplikací v Application Gateway pomocí Azure CLI.
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.date: 11/14/2019
ms.author: victorh
ms.topic: article
ms.openlocfilehash: 8e8aaa9458619bc937c5bb11c450f3197b92f451
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "74048535"
---
# <a name="customize-web-application-firewall-rules-using-the-azure-cli"></a>Přizpůsobení pravidel firewallu webových aplikací pomocí rozhraní příkazového řádku Azure

Firewall webových aplikací (WAF) služby Azure Application Gateway poskytuje ochranu pro webové aplikace. Tyto ochrany poskytuje základní sadu pravidel OWASP (Open Web Application Security) (počítačový systém). Některá pravidla můžou způsobit falešně pozitivní a blokují skutečný provoz. Z tohoto důvodu Application Gateway poskytuje možnost přizpůsobit skupiny pravidel a pravidla. Další informace o konkrétních skupinách pravidel a pravidlech najdete v tématu [seznam skupin pravidel a pravidel pro Firewall webových aplikací](application-gateway-crs-rulegroups-rules.md).

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

### <a name="view-rules-in-a-rule-group"></a>Zobrazit pravidla ve skupině pravidel

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

Následující příklad zakáže pravidla `910018` a `910017` bránu Application Gateway:

```azurecli-interactive
az network application-gateway waf-config set --resource-group AdatumAppGatewayRG --gateway-name AdatumAppGateway --enabled true --rule-set-version 3.0 --disabled-rules 910018 910017
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
