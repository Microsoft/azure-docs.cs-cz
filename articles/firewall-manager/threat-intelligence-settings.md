---
title: Azure Firewall konfigurace analýzy hrozeb
description: Přečtěte si, jak nakonfigurovat filtrování na základě logiky hrozeb pro zásady Azure Firewall pro upozornění a zamítnutí provozu z a do známých škodlivých IP adres a domén.
services: firewall-manager
author: vhorne
ms.service: firewall-manager
ms.topic: article
ms.date: 06/30/2020
ms.author: victorh
ms.openlocfilehash: a663c5f3bcf3492c4a9bc74fe93c6ed6a86137ee
ms.sourcegitcommit: 03662d76a816e98cfc85462cbe9705f6890ed638
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/15/2020
ms.locfileid: "90530637"
---
# <a name="azure-firewall-threat-intelligence-configuration"></a>Azure Firewall konfigurace analýzy hrozeb

Filtrování na základě logiky hrozeb lze nakonfigurovat pro zásady Azure Firewall pro upozornění a zamítnutí provozu z a do známých škodlivých IP adres a domén. Zdrojem těchto IP adres a domén je kanál analýzy hrozeb Microsoftu. [Intelligent Security Graph](https://www.microsoft.com/security/operations/intelligence) využívá Microsoft Threat Intelligence a používá ho víc služeb, včetně Azure Security Center.<br>

Pokud jste nakonfigurovali filtrování na základě logiky hrozeb, přidružená pravidla se zpracují před všemi pravidly NAT, síťovými pravidly nebo pravidly aplikací.

:::image type="content" source="media/threat-intelligence-settings/threat-intelligence-policy.png" alt-text="Zásady analýzy hrozeb":::

## <a name="threat-intelligence-mode"></a>Režim analýzy hrozeb

Můžete se rozhodnout, že chcete protokolovat pouze výstrahu, když je pravidlo aktivováno, nebo můžete zvolit možnost výstraha a odepřít režim.

V režimu výstrahy je ve výchozím nastavení povolené filtrování na základě logiky hrozeb.

## <a name="allowed-list-addresses"></a>Povolené adresy seznamu

Můžete nakonfigurovat seznam povolených IP adres, aby Analýza hrozeb nefiltroval žádné z adres, rozsahů ani podsítí, které zadáte.



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

- Kontrola [sestavy Microsoft Security Intelligence](https://www.microsoft.com/en-us/security/operations/security-intelligence-report)