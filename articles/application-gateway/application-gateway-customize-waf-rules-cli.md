---
title: Přizpůsobení pravidel firewallu webových aplikací ve službě Azure Application Gateway – rozhraní příkazového řádku Azure
description: Tento článek obsahuje informace o tom, jak přizpůsobení pravidel firewallu webových aplikací ve službě Application Gateway pomocí Azure CLI.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.date: 2/22/2019
ms.author: victorh
ms.openlocfilehash: 5e364c597b8c524e95297f279003462f2d16abe1
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/23/2019
ms.locfileid: "56726257"
---
# <a name="customize-web-application-firewall-rules-through-the-azure-cli"></a>Přizpůsobení pravidel firewallu webových aplikací pomocí rozhraní příkazového řádku Azure

Firewall webových aplikací (WAF) Azure Application Gateway chrání webové aplikace. Tyto ochrany jsou k dispozici ve Open Web Application zabezpečení projektu (OWASP) základní pravidlo nastavte (CRS). Některá pravidla může způsobit, že počet falešně pozitivních výsledků a blokovat skutečný provoz. Z tohoto důvodu Application Gateway poskytuje schopnost přizpůsobit skupin pravidel a pravidla. Další informace o příslušné pravidlo skupiny a pravidel, naleznete v tématu [seznam skupin pravidel CRS firewallu webových aplikací a pravidla](application-gateway-crs-rulegroups-rules.md).

## <a name="view-rule-groups-and-rules"></a>Zobrazení skupin pravidel a pravidla

Následující příklady kódu ukazují, jak zobrazení pravidel a skupin pravidel, které se dají konfigurovat.

### <a name="view-rule-groups"></a>Zobrazit pravidla skupiny

Následující příklad ukazuje, jak zobrazit skupiny pravidel:

```azurecli-interactive
az network application-gateway waf-config list-rule-sets --type OWASP
```

Následující výstup je zkrácený odpovědi z předchozího příkladu:

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

### <a name="view-rules-in-a-rule-group"></a>Zobrazení pravidel skupiny pravidla

Následující příklad ukazuje, jak zobrazit pravidel ve skupině a zadané pravidlo:

```azurecli-interactive
az network application-gateway waf-config list-rule-sets --group "REQUEST-910-IP-REPUTATION"
```

Následující výstup je zkrácený odpovědi z předchozího příkladu:

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

## <a name="disable-rules"></a>Zákaz pravidel

Následující příklad zakazuje pravidla `910018` a `910017` ve službě application gateway:

```azurecli-interactive
az network application-gateway waf-config set --resource-group AdatumAppGatewayRG --gateway-name AdatumAppGateway --enabled true --rule-set-version 3.0 --disabled-rules 910018 910017
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

Po dokončení konfigurace zakázaná pravidla se dozvíte, jak si chcete zobrazit protokoly WAF. Další informace najdete v tématu [diagnostice služby Application Gateway](application-gateway-diagnostics.md#diagnostic-logging).

[fig1]: ./media/application-gateway-customize-waf-rules-portal/1.png
[1]: ./media/application-gateway-customize-waf-rules-portal/figure1.png
[2]: ./media/application-gateway-customize-waf-rules-portal/figure2.png
[3]: ./media/application-gateway-customize-waf-rules-portal/figure3.png
