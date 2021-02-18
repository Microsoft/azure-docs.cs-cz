---
title: Azure Firewall konfigurace analýzy hrozeb
description: Přečtěte si, jak nakonfigurovat filtrování na základě logiky hrozeb pro zásady Azure Firewall pro upozornění a zamítnutí provozu z a do známých škodlivých IP adres a domén.
services: firewall-manager
author: vhorne
ms.service: firewall-manager
ms.topic: article
ms.date: 06/30/2020
ms.author: victorh
ms.openlocfilehash: 7ede1c917bb44dd31aa59855a0b7c83eb478700a
ms.sourcegitcommit: 227b9a1c120cd01f7a39479f20f883e75d86f062
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/18/2021
ms.locfileid: "100651713"
---
# <a name="azure-firewall-threat-intelligence-configuration"></a>Azure Firewall konfigurace analýzy hrozeb

Filtrování na základě logiky hrozeb lze nakonfigurovat pro zásady Azure Firewall pro upozornění a zamítnutí provozu z a do známých škodlivých IP adres a domén. Zdrojem těchto IP adres a domén je kanál analýzy hrozeb Microsoftu. [Intelligent Security Graph](https://www.microsoft.com/security/operations/intelligence) využívá Microsoft Threat Intelligence a používá ho víc služeb, včetně Azure Security Center.<br>

Pokud jste nakonfigurovali filtrování na základě logiky hrozeb, přidružená pravidla se zpracují před všemi pravidly NAT, síťovými pravidly nebo pravidly aplikací.

:::image type="content" source="media/threat-intelligence-settings/threat-intelligence-policy.png" alt-text="Zásady analýzy hrozeb":::

## <a name="threat-intelligence-mode"></a>Režim analýzy hrozeb

V jednom ze tří režimů, které jsou popsány v následující tabulce, můžete nakonfigurovat analýzu hrozeb. V režimu výstrahy je ve výchozím nastavení povolené filtrování na základě logiky hrozeb.

|Režim |Description  |
|---------|---------|
|`Off`     | Funkce Analýza hrozeb není pro bránu firewall povolená. |
|`Alert only`     | Budete dostávat vysoce spolehlivé výstrahy pro provoz procházející bránou firewall nebo ze známých škodlivých IP adres a domén. |
|`Alert and deny`     | Provoz se zablokuje a při zjištění, že se při pokusu o přechod přes bránu firewall do nebo ze známých škodlivých IP adres a domén dojde k detekci výstrah, budete dostávat vysoce spolehlivá upozornění. |

> [!NOTE]
> Režim analýzy hrozeb je zděděný od nadřazených zásad k podřízeným zásadám. Podřízená zásada musí být nakonfigurovaná se stejným nebo přísnějším režimem než nadřazená zásada.

## <a name="allowlist-addresses"></a>Povolených adresy

Analýza hrozeb může aktivovat falešně pozitivní a zablokovat provoz, který je ve skutečnosti platný. Můžete nakonfigurovat seznam povolených IP adres, aby Analýza hrozeb nefiltroval žádné z adres, rozsahů ani podsítí, které zadáte.  

![Povolených adresy](media/threat-intelligence-settings/allow-list.png)

Povolených můžete aktualizovat pomocí několika záznamů najednou tak, že nahrajete soubor CSV. Soubor CSV může obsahovat jenom IP adresy a rozsahy. Soubor nemůže obsahovat záhlaví.

> [!NOTE]
> Adresy povolených Intelligence pro analýzu hrozeb se dědí z nadřazených zásad do podřízených zásad. Všechny IP adresy nebo rozsahy přidané do nadřazených zásad se uplatní i pro všechny podřízené zásady.

## <a name="logs"></a>Protokoly

Následující výpis protokolu ukazuje aktivované pravidlo pro odchozí provoz na škodlivý web:

```json
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
