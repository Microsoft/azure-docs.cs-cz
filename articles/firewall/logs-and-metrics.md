---
title: Přehled protokolů Azure Firewall a metriky
description: Bránu Azure Firewall můžete monitorovat pomocí protokolů brány firewall. K auditu operací na prostředcích brány Azure Firewall můžete také použít protokoly aktivit.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 02/16/2021
ms.author: victorh
ms.openlocfilehash: 637ef56ca79dd333a587d38ed6a685664c7566ca
ms.sourcegitcommit: 5a999764e98bd71653ad12918c09def7ecd92cf6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/16/2021
ms.locfileid: "100547039"
---
# <a name="azure-firewall-logs-and-metrics"></a>Metriky a protokoly Azure Firewallu

Bránu Azure Firewall můžete monitorovat pomocí protokolů brány firewall. K auditu operací na prostředcích brány Azure Firewall můžete také použít protokoly aktivit.

Některé z těchto protokolů jsou přístupné z webu Azure Portal. Protokoly je možné odeslat do [Azure monitor protokolů](../azure-monitor/insights/azure-networking-analytics.md), úložiště a Event Hubs a analyzovat v protokolech Azure monitor nebo pomocí různých nástrojů, jako je Excel a Power BI.

Metriky jsou odlehčené a můžou podporovat scénáře téměř v reálném čase, které jsou užitečné při upozorňování a rychlé detekci problémů.

## <a name="diagnostic-logs"></a>Diagnostické protokoly

 Pro bránu Azure Firewall jsou k dispozici následující diagnostické protokoly:

* **Protokol pravidel aplikace**

   Protokol pravidel aplikací je uložený v účtu úložiště, streamované do Center událostí nebo odeslaný do Azure Monitor protokolů jenom v případě, že jste ho povolili pro každý Azure Firewall. Každé nové připojení, které odpovídá jednomu z vašich nakonfigurovaných pravidel aplikace, vytvoří pro dané přijaté nebo odepřené připojení protokol. Data jsou protokolována ve formátu JSON, jak je znázorněno v následujících příkladech:

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

   ```json
   {
     "category": "AzureFirewallApplicationRule",
     "time": "2018-04-16T23:45:04.8295030Z",
     "resourceId": "/SUBSCRIPTIONS/{subscriptionId}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/AZUREFIREWALLS/{resourceName}",
     "operationName": "AzureFirewallApplicationRuleLog",
     "properties": {
         "msg": "HTTPS request from 10.11.2.4:53344 to www.bing.com:443. Action: Allow. Rule Collection: ExampleRuleCollection. Rule: ExampleRule. Web Category: SearchEnginesAndPortals"
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

* **Protokol proxy serveru DNS**

   Protokol proxy serveru DNS je uložený v účtu úložiště, streamování do Center událostí nebo odeslaný do Azure Monitor protokolů jenom v případě, že jste ho povolili pro každý Azure Firewall. Tento protokol sleduje zprávy DNS na serveru DNS nakonfigurovaném pomocí proxy serveru DNS. Data jsou protokolována ve formátu JSON, jak je znázorněno v následujících příkladech:


   ```
   Category: DNS proxy logs.
   Time: log timestamp.
   Properties: currently contains the full message.
   note: this field will be parsed to specific fields in the future, while maintaining backward compatibility with the existing properties field.
   ```

   Nástup
   ```json
   {
     "category": "AzureFirewallDnsProxy",
     "time": "2020-09-02T19:12:33.751Z",
     "resourceId": "/SUBSCRIPTIONS/{subscriptionId}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/AZUREFIREWALLS/{resourceName}",
     "operationName": "AzureFirewallDnsProxyLog",
     "properties": {
         "msg": "DNS Request: 11.5.0.7:48197 – 15676 AAA IN md-l1l1pg5lcmkq.blob.core.windows.net. udp 55 false 512 NOERROR - 0 2.000301956s"
     }
   }
   ```

   Nepovedlo se

   ```json
   {
     "category": "AzureFirewallDnsProxy",
     "time": "2020-09-02T19:12:33.751Z",
     "resourceId": "/SUBSCRIPTIONS/{subscriptionId}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/AZUREFIREWALLS/{resourceName}",
     "operationName": "AzureFirewallDnsProxyLog",
     "properties": {
         "msg": " Error: 2 time.windows.com.reddog.microsoft.com. A: read udp 10.0.1.5:49126->168.63.129.160:53: i/o timeout”
     }
   }
   ```

   Formát zprávy:

   `[client’s IP address]:[client’s port] – [query ID] [type of the request] [class of the request] [name of the request] [protocol used] [request size in bytes] [EDNS0 DO (DNSSEC OK) bit set in the query] [EDNS0 buffer size advertised in the query] [response CODE] [response flags] [response size] [response duration]`

Protokoly můžete ukládat třemi způsoby:

* **Učet úložiště**: Účty úložiště jsou nejvhodnější pro ukládání protokolů na delší dobu, které budete kontrolovat pouze v případě potřeby.
* **Centra událostí**: Centra událostí jsou skvělou volbou pro integrování protokolů s jinými nástroji správy akcí a informací o zabezpečení (SEIM), abyste o svých prostředcích získávali upozornění.
* **Protokoly Azure monitor**: Azure monitor protokoly se nejlépe používají pro obecné monitorování vaší aplikace v reálném čase nebo při prohlížení trendů.

## <a name="activity-logs"></a>Protokoly aktivit

   Položky protokolu aktivit se ve výchozím nastavení shromažďují a můžete si je zobrazit na webu Azure Portal.

   K zobrazení všech operací odeslaných do vašeho předplatného Azure můžete použít [protokoly aktivit Azure](../azure-resource-manager/management/view-activity-logs.md) (dřív označované jako operační protokoly a protokoly auditu).

## <a name="metrics"></a>Metriky

Metriky v Azure Monitor jsou numerické hodnoty, které popisují určitý aspekt systému v určitou dobu. Metriky se shromažďují každou minutu a jsou užitečné pro upozorňování, protože je možné je často vzorkovat. Výstraha se dá rychle aktivovat pomocí relativně jednoduché logiky.

Pro Azure Firewall jsou k dispozici následující metriky:

- **Počet průchodů pravidel aplikací** – počet přístupů k pravidlu aplikace.

    Jednotka: počet

- **Počet průchodů síťových pravidel** – počet přístupů k síťovému pravidlu.

    Jednotka: počet

- **Zpracovaná data** – součet dat procházejí bránou firewall v daném časovém intervalu.

    Jednotka: bajtů

- **Propustnost** dat procházejí bránou firewall za sekundu.

    Jednotka: bity za sekundu

- **Stav brány firewall** – určuje stav brány firewall na základě dostupnosti portu SNAT.

    Jednotka: procenta

   Tato metrika má dvě dimenze:
  - Stav: možné hodnoty jsou *v pořádku*, *snížené*, *není v pořádku.*
  - Důvod: označuje důvod pro odpovídající stav brány firewall. 

     Pokud se používají porty SNAT > 95%, považují se za vyčerpané a stav je 50% se stavem =**degradované** a důvod =**SNAT port**. Brána firewall dál zpracovává provoz a stávající připojení nejsou ovlivněná. Občas však není možné navazovat nová připojení.

     Pokud se používají porty SNAT < 95%, považuje se brána firewall za v pořádku a stav se zobrazí jako 100%.

     Pokud se nehlásí žádné využití portů SNAT, zobrazí se 0% stav. 

- **Využití portů SNAT** – procento portů SNAT využívaných bránou firewall.

    Jednotka: procenta

   Když do brány firewall přidáte další veřejné IP adresy, zvýší se počet dostupných portů SNAT a sníží se jejich využití. Další porty SNAT budou k dispozici také po škálování brány firewall na více instancí z jiných důvodů (například kvůli procesoru nebo propustnosti). Vzhledem k tomu, že procento využití portů SNAT může být efektivní, aniž byste museli přidávat žádné veřejné IP adresy, a to jenom proto, že se služba škáluje. Můžete přímo řídit počet dostupných veřejných IP adres a zvýšit tak porty, které jsou k dispozici v bráně firewall. Škálování brány firewall ale nemůžete přímo ovládat.


## <a name="next-steps"></a>Další kroky

- Informace o tom, jak monitorovat protokoly Azure Firewall a metriky, najdete v tématu [kurz: monitorování protokolů Azure firewall](./firewall-diagnostics.md).

- Další informace o metrikách v Azure Monitor najdete v tématu [metriky v Azure monitor](../azure-monitor/platform/data-platform-metrics.md).