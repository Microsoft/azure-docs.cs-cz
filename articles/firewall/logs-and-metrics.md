---
title: Přehled protokolů Azure Firewall a metriky
description: Tento článek představuje přehled diagnostických protokolů a metrik Azure Firewall.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 08/22/2019
ms.author: victorh
ms.openlocfilehash: fea00358fc21cf6f57673e14ebd0feafe532b620
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/21/2019
ms.locfileid: "69876562"
---
# <a name="azure-firewall-logs-and-metrics"></a>Protokoly Azure Firewall a metriky

Bránu Azure Firewall můžete monitorovat pomocí protokolů brány firewall. K auditu operací na prostředcích brány Azure Firewall můžete také použít protokoly aktivit.

Některé z těchto protokolů jsou přístupné z webu Azure Portal. Protokoly je možné odeslat do [Azure monitor protokolů](../azure-monitor/insights/azure-networking-analytics.md), úložiště a Event Hubs a analyzovat v protokolech Azure monitor nebo pomocí různých nástrojů, jako je Excel a Power BI.

Metriky jsou odlehčené a můžou podporovat scénáře téměř v reálném čase, které jsou užitečné při upozorňování a rychlé detekci problémů. 

## <a name="diagnostic-logs"></a>Diagnostické protokoly

 Pro bránu Azure Firewall jsou k dispozici následující diagnostické protokoly:

* **Protokol pravidel aplikace**

   Protokol pravidel aplikací je uložený v účtu úložiště, streamované do Center událostí nebo odeslaný do Azure Monitor protokolů jenom v případě, že jste ho povolili pro každý Azure Firewall. Každé nové připojení, které odpovídá jednomu z vašich nakonfigurovaných pravidel aplikace, vytvoří pro dané přijaté nebo odepřené připojení protokol. Jak je vidět v následujícím příkladu, data se protokolují ve formátu JSON:

   ```
   Category: application rule logs.
   Time: log timestamp.
   Properties: currently contains the full message. 
   note: this field will be parsed to specific fields in the future, while maintaining backward compatibility with the existing properties field.
   ```

   ```json
   {
    "category": "AzureFirewallApplicationRule",
    "time": "2018-04-16T23:45:04.8295030Z",
    "resourceId": "/SUBSCRIPTIONS/{subscriptionId}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/AZUREFIREWALLS/{resourceName}",
    "operationName": "AzureFirewallApplicationRuleLog",
    "properties": {
        "msg": "HTTPS request from 10.1.0.5:55640 to mydestination.com:443. Action: Allow. Rule Collection: collection1000. Rule: rule1002"
    }
   }
   ```

* **Protokol pravidel sítě**

   Protokol síťových pravidel je uložený v účtu úložiště, streamování do Center událostí nebo odeslaný do Azure Monitor protokolů jenom v případě, že jste ho povolili pro každý Azure Firewall. Každé nové připojení, které odpovídá jednomu z vašich nakonfigurovaných pravidel aplikace, vytvoří pro dané přijaté nebo odepřené připojení protokol. Jak je vidět v následujícím příkladu, data se protokolují ve formátu JSON:

   ```
   Category: network rule logs.
   Time: log timestamp.
   Properties: currently contains the full message. 
   note: this field will be parsed to specific fields in the future, while maintaining backward compatibility with the existing properties field.
   ```

   ```json
  {
    "category": "AzureFirewallNetworkRule",
    "time": "2018-06-14T23:44:11.0590400Z",
    "resourceId": "/SUBSCRIPTIONS/{subscriptionId}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/AZUREFIREWALLS/{resourceName}",
    "operationName": "AzureFirewallNetworkRuleLog",
    "properties": {
        "msg": "TCP request from 111.35.136.173:12518 to 13.78.143.217:2323. Action: Deny"
    }
   }

   ```

Protokoly můžete ukládat třemi způsoby:

* **Účet úložiště**: Účty úložiště se nejlépe používají pro protokoly, když jsou protokoly uložené po delší dobu a v případě potřeby se přezkoumávají.
* **Centra událostí**: Centra událostí jsou skvělou možností pro integraci s dalšími nástroji SEIM (Security Information and Event Management), které vám umožní získat upozornění na vaše prostředky.
* **Protokoly Azure monitor**: Protokoly Azure Monitor se nejlépe používají pro obecné monitorování vaší aplikace v reálném čase nebo při prohlížení trendů.

## <a name="activity-logs"></a>Protokoly aktivit

   Položky protokolu aktivit se ve výchozím nastavení shromažďují a můžete si je zobrazit na webu Azure Portal.

   K zobrazení všech operací odeslaných do vašeho předplatného Azure můžete použít [protokoly aktivit Azure](../azure-resource-manager/resource-group-audit.md) (dřív označované jako operační protokoly a protokoly auditu).

## <a name="metrics"></a>Metriky

Metriky v Azure Monitor jsou numerické hodnoty, které popisují určitý aspekt systému v určitou dobu. Metriky se shromažďují každou minutu a jsou užitečné pro upozorňování, protože je možné je často vzorkovat. Výstraha se dá rychle aktivovat pomocí relativně jednoduché logiky.

Pro Azure Firewall jsou k dispozici následující metriky:

- **Počet průchodů pravidel aplikací** – počet přístupů k pravidlu aplikace.

    Jednotka: počet

- **Počet průchodů síťových pravidel** – počet přístupů k síťovému pravidlu.

    Jednotka: počet

- **Zpracovaná data** – množství dat, která procházejí bránou firewall.

    Jednotka: bajtů

- **Stav brány firewall** – určuje stav brány firewall.

    Jednotka: procenta

   Tato metrika má dvě dimenze:
  - **Stav**: Možné hodnoty jsou *v pořádku*, jsou *degradovány*, nejsou v pořádku.
  - **Důvod**: Určuje důvod pro odpovídající stav brány firewall. Například může označovat *porty SNAT* , pokud je stav brány firewall degradován nebo není v pořádku.





- **Využití portů SNAT** – procento portů SNAT využívaných bránou firewall.

    Jednotka: procenta

   Když do brány firewall přidáte další veřejné IP adresy, budou k dispozici další porty SNAT, čímž se sníží využití portů SNAT. Kromě toho, když se brána firewall škáluje z různých důvodů (například CPU nebo propustnost), budou k dispozici i další porty SNAT. Vzhledem k tomu, že procento využití portů SNAT může být efektivní, aniž byste museli přidávat žádné veřejné IP adresy, a to jenom proto, že se služba škáluje. Můžete přímo řídit počet dostupných veřejných IP adres a zvýšit tak porty, které jsou k dispozici v bráně firewall. Škálování brány firewall ale nemůžete přímo ovládat. V současné době se porty SNAT přidávají jenom pro prvních pět veřejných IP adres.   


## <a name="next-steps"></a>Další kroky

- Informace o tom, jak monitorovat protokoly Azure firewall a metriky [, najdete v tématu Kurz: Monitorujte protokoly](tutorial-diagnostics.md)Azure firewall.

- Další informace o metrikách v Azure Monitor najdete v tématu [metriky v Azure monitor](../azure-monitor/platform/data-platform-metrics.md).