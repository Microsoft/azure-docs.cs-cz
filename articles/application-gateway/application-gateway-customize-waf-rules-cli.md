---
title: Přizpůsobení pravidel brány firewall webových aplikací v Azure Application Gateway - 2.0 rozhraní příkazového řádku Azure | Microsoft Docs
description: Tento článek obsahuje informace o tom, jak přizpůsobit pravidla brány firewall webových aplikací v Application Gateway pomocí Azure CLI 2.0.
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
ms.openlocfilehash: b0bd79bb7ce584a9abaffbb6c30d6fbfe64f87c2
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/04/2018
ms.locfileid: "33204203"
---
# <a name="customize-web-application-firewall-rules-through-the-azure-cli-20"></a>Přizpůsobení pravidla brány firewall webových aplikací pomocí Azure CLI 2.0

> [!div class="op_single_selector"]
> * [Azure Portal](application-gateway-customize-waf-rules-portal.md)
> * [PowerShell](application-gateway-customize-waf-rules-powershell.md)
> * [Azure CLI 2.0](application-gateway-customize-waf-rules-cli.md)

Azure Application Gateway brány firewall webových aplikací (firewall webových aplikací) poskytuje ochranu pro webové aplikace. Tyto ochrany jsou poskytovány pomocí aplikace otevřete webový projekt zabezpečení (OWASP) základní pravidlo nastavit CRS (). Některá pravidla můžete způsobit falešně pozitivních zjištění a blokování skutečné provozu. Z tohoto důvodu Application Gateway poskytuje schopnost přizpůsobit skupiny pravidla a pravidla. Další informace o konkrétní pravidlo skupiny a pravidel najdete v tématu [seznam webových aplikací brány firewall řádku pravidlo skupiny a pravidel](application-gateway-crs-rulegroups-rules.md).

## <a name="view-rule-groups-and-rules"></a>Zobrazit skupiny pravidla a pravidla

Následující příklady kódu ukazují, jak zobrazit pravidla a pravidla skupiny, které se dají konfigurovat.

### <a name="view-rule-groups"></a>Zobrazení pravidla skupiny

Následující příklad ukazuje, jak chcete-li zobrazit skupiny pravidel:

```azurecli-interactive
az network application-gateway waf-config list-rule-sets --type OWASP
```

Tento výstup je oříznuta odpovědí z předchozího příkladu:

```
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

Následující příklad ukazuje, jak chcete zobrazit pravidla ve skupině zadaným pravidlem:

```azurecli-interactive
az network application-gateway waf-config list-rule-sets --group "REQUEST-910-IP-REPUTATION"
```

Tento výstup je oříznuta odpovědí z předchozího příkladu:

```
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

## <a name="disable-rules"></a>Zakázání pravidla

Následující příklad zakazuje pravidla `910018` a `910017` na aplikační brány:

```azurecli-interactive
az network application-gateway waf-config set --resource-group AdatumAppGatewayRG --gateway-name AdatumAppGateway --enabled true --rule-set-version 3.0 --disabled-rules 910018 910017
```

## <a name="next-steps"></a>Další postup

Po dokončení konfigurace zakázaná pravidla, můžete naučit k zobrazení protokolů firewall webových aplikací. Další informace najdete v tématu [diagnostics Application Gateway](application-gateway-diagnostics.md#diagnostic-logging).

[fig1]: ./media/application-gateway-customize-waf-rules-portal/1.png
[1]: ./media/application-gateway-customize-waf-rules-portal/figure1.png
[2]: ./media/application-gateway-customize-waf-rules-portal/figure2.png
[3]: ./media/application-gateway-customize-waf-rules-portal/figure3.png
