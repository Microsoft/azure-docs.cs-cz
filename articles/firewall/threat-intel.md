---
title: Azure Firewall filtrování na základě analýzy hrozeb
description: Pro bránu firewall můžete povolit filtrování na základě analýzy hrozeb, které umožňuje upozorňovat na provoz ze známých škodlivých IP adres a domén nebo z nich a zamítat ho.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 05/12/2020
ms.author: victorh
ms.openlocfilehash: e51cc8905a7b4a88bb7f7dabaf24bb30159ff86c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "83655094"
---
# <a name="azure-firewall-threat-intelligence-based-filtering"></a>Azure Firewall filtrování na základě logiky hrozeb

Pro bránu firewall můžete povolit filtrování na základě analýzy hrozeb, které umožňuje upozorňovat na provoz ze známých škodlivých IP adres a domén nebo z nich a zamítat ho. Zdrojem těchto IP adres a domén je kanál analýzy hrozeb Microsoftu. [Intelligent Security Graph](https://www.microsoft.com/security/operations/intelligence) využívá Microsoft Threat Intelligence a používá ho víc služeb, včetně Azure Security Center.<br>
<br>

:::image type="content" source="media/threat-intel/firewall-threat.png" alt-text="Analýza hrozeb v bráně firewall" border="false":::

Pokud jste povolili filtrování založené na hrozbách, budou přidružená pravidla zpracována před všemi pravidly NAT, síťovými pravidly nebo pravidly aplikací.

Můžete zvolit, že při aktivaci pravidla stačí protokolovat výstrahu, nebo můžete zvolit možnost výstraha a odepřít režim.

V režimu výstrahy je ve výchozím nastavení povolené filtrování na základě logiky hrozeb. Tuto funkci nemůžete vypnout nebo změnit režim, dokud nebude v oblasti k dispozici rozhraní portálu.

:::image type="content" source="media/threat-intel/threat-intel-ui.png" alt-text="Analýza hrozeb v bráně firewall":::

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