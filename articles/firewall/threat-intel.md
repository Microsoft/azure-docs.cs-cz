---
title: Filtrování založené na analýzách hrozeb azure firewall
description: Filtrování založené na technologii threat intelligence může být povoleno, aby brána firewall upozorňovala a odepírala provoz ze známých škodlivých IP adres a domén.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 11/19/2019
ms.author: victorh
ms.openlocfilehash: c291dbe9c1eb37e68174a2353e296a376c7d0896
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74168668"
---
# <a name="azure-firewall-threat-intelligence-based-filtering"></a>Filtrování založené na analýzách hrozeb azure firewall

Filtrování založené na technologii threat intelligence může být povoleno, aby brána firewall upozorňovala a odepírala provoz ze známých škodlivých IP adres a domén. IP adresy a domény jsou získávány z informačního kanálu Microsoft Threat Intelligence. [Inteligentní graf zabezpečení](https://www.microsoft.com/en-us/security/operations/intelligence) pohání microsoftovou analýzu hrozeb a používá ho několik služeb včetně Azure Security Center.

![Analýza hrozeb brány firewall](media/threat-intel/firewall-threat.png)

Pokud jste povolili filtrování založené na technologii threat intelligence, jsou přidružená pravidla zpracována před libovolnými pravidly NAT, síťovými pravidly nebo pravidly aplikací.

Můžete se rozhodnout pouze přihlásit výstrahu při aktivaci pravidla, nebo můžete zvolit režim výstrahy a odepření.

Ve výchozím nastavení je filtrování založené na inteligenci hrozeb povoleno v režimu výstrah. Tuto funkci nelze vypnout ani změnit režim, dokud nebude ve vaší oblasti k dispozici rozhraní portálu.

![Rozhraní portálu filtrování založenéna analýzy hrozeb](media/threat-intel/threat-intel-ui.png)

## <a name="logs"></a>Protokoly

Následující výňatek protokolu ukazuje aktivované pravidlo:

```
{
    "category": "AzureFirewallNetworkRule",
    "time": "2018-04-16T23:45:04.8295030Z",
    "resourceId": "/SUBSCRIPTIONS/{subscriptionId}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/AZUREFIREWALLS/{resourceName}",
    "operationName": "AzureFirewallThreatIntelLog",
    "properties": {
         "msg": "HTTP request from 10.0.0.5:54074 to somemaliciousdomain.com:80. Action: Alert. ThreatIntel: Bot Networks"
    }
}
```

## <a name="testing"></a>Testování

- **Odchozí testování** – výstrahy odchozího provozu by měly být vzácným výskytem, protože to znamená, že vaše prostředí bylo ohroženo. Chcete-li pomoci otestovat odchozí výstrahy fungují, byl vytvořen test FQDN, který aktivuje výstrahu. Pro odchozí testy použijte **testmaliciousdomain.eastus.cloudapp.azure.com.**

- **Příchozí testování** – můžete očekávat, že se zobrazí výstrahy na příchozí provoz, pokud jsou pravidla DNAT nakonfigurována na bráně firewall. To platí i v případě, že jsou povoleny pouze určité zdroje na pravidlo DNAT a provoz je jinak odepřen. Azure Firewall neupozorňuje na všechny známé skenery portů. pouze na skenerech, o kterých je známo, že se také zabývají škodlivou činností.

## <a name="next-steps"></a>Další kroky

- Viz [ukázky Azure Firewall Log Analytics](log-analytics-samples.md)
- Přečtěte si, jak [nasadit a nakonfigurovat bránu Azure Firewall.](tutorial-firewall-deploy-portal.md)
- Kontrola [sestavy microsoft security intelligence](https://www.microsoft.com/en-us/security/operations/security-intelligence-report)