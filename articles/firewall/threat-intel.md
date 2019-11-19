---
title: Azure Firewall filtrování na základě analýzy hrozeb
description: Pro bránu firewall můžete povolit filtrování na základě analýzy hrozeb, které bude upozorňovat na provoz směřující z nebo do známých škodlivých IP adres nebo domén a odepírat takový provoz.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 11/19/2019
ms.author: victorh
ms.openlocfilehash: c291dbe9c1eb37e68174a2353e296a376c7d0896
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/19/2019
ms.locfileid: "74168668"
---
# <a name="azure-firewall-threat-intelligence-based-filtering"></a>Azure Firewall filtrování na základě logiky hrozeb

Pro bránu firewall můžete povolit filtrování na základě analýzy hrozeb, které bude upozorňovat na provoz směřující z nebo do známých škodlivých IP adres nebo domén a odepírat takový provoz. Tyto IP adresy a domény se přebírají z informačního kanálu analýzy hrozeb Microsoftu. [Intelligent Security Graph](https://www.microsoft.com/en-us/security/operations/intelligence) využívá Microsoft Threat Intelligence a používá ho víc služeb, včetně Azure Security Center.

![Analýza hrozeb v bráně firewall](media/threat-intel/firewall-threat.png)

Pokud jste povolili filtrování založené na hrozbách, budou přidružená pravidla zpracována před všemi pravidly NAT, síťovými pravidly nebo pravidly aplikací.

Můžete zvolit, že při aktivaci pravidla stačí protokolovat výstrahu, nebo můžete zvolit možnost výstraha a odepřít režim.

V režimu výstrahy je ve výchozím nastavení povolené filtrování na základě logiky hrozeb. Tuto funkci nemůžete vypnout nebo změnit režim, dokud nebude v oblasti k dispozici rozhraní portálu.

![Rozhraní portálu pro filtrování na základě analýzy hrozeb](media/threat-intel/threat-intel-ui.png)

## <a name="logs"></a>Protokoly

Následující výpis protokolu ukazuje aktivované pravidlo:

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

- **Odchozí testování** – výstrahy odchozího provozu by měly být vzácná událost, protože to znamená, že došlo k ohrožení zabezpečení vašeho prostředí. Aby bylo možné otestovat odchozí výstrahy, je vytvořen testovací plně kvalifikovaný název domény, který aktivuje výstrahu. Pro odchozí testy použijte **testmaliciousdomain.eastus.cloudapp.Azure.com** .

- **Příchozí testování** – Pokud jsou v bráně firewall nakonfigurovaná pravidla DNAT, můžete očekávat, že se výstrahy budou zobrazovat na příchozím provozu. To platí i v případě, že je v pravidle DNAT povolené jenom konkrétní zdroje a provoz se jinak zamítl. Azure Firewall neupozorní na všechny známé skenery portů; jenom na skenerech, u kterých se ví, že se taky zapojí škodlivá aktivita.

## <a name="next-steps"></a>Další kroky

- [Příklady ukázek Azure Firewall Log Analytics](log-analytics-samples.md)
- Zjistěte, jak [nasadit a nakonfigurovat Azure firewall](tutorial-firewall-deploy-portal.md) .
- Kontrola [sestavy Microsoft Security Intelligence](https://www.microsoft.com/en-us/security/operations/security-intelligence-report)