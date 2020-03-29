---
title: Přehled protokolů a metrik Azure Firewall
description: Bránu Azure Firewall můžete monitorovat pomocí protokolů brány firewall. K auditu operací na prostředcích brány Azure Firewall můžete také použít protokoly aktivit.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 01/22/2020
ms.author: victorh
ms.openlocfilehash: 89c6700d5df3bcef1332121c3cf7d8f720fe054c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76315027"
---
# <a name="azure-firewall-logs-and-metrics"></a>Metriky a protokoly Azure Firewallu

Bránu Azure Firewall můžete monitorovat pomocí protokolů brány firewall. K auditu operací na prostředcích brány Azure Firewall můžete také použít protokoly aktivit.

Některé z těchto protokolů jsou přístupné z webu Azure Portal. Protokoly se můžou odesílat do [protokolů Azure Monitoru](../azure-monitor/insights/azure-networking-analytics.md), úložiště a centra událostí a analyzovat je v protokolech Azure Monitoru nebo pomocí různých nástrojů, jako je Excel a Power BI.

Metriky jsou zjednodušené a mohou podporovat scénáře téměř v reálném čase, takže jsou užitečné pro upozorňování a rychlé zjišťování problémů.

## <a name="diagnostic-logs"></a>Diagnostické protokoly

 Pro bránu Azure Firewall jsou k dispozici následující diagnostické protokoly:

* **Protokol pravidel aplikace**

   Protokol pravidel aplikace se uloží do účtu úložiště, streamuje se do center událostí a/nebo se odesílá do protokolů Azure Monitoru, jenom když jste ho povolili pro každou bránu Azure Firewall. Každé nové připojení, které odpovídá jednomu z vašich nakonfigurovaných pravidel aplikace, vytvoří pro dané přijaté nebo odepřené připojení protokol. Jak je vidět v následujícím příkladu, data se protokolují ve formátu JSON:

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

   Protokol pravidel sítě se uloží do účtu úložiště, streamuje se do center událostí a/nebo se odesílá do protokolů Azure Monitoru, jenom když jste ho povolili pro každou bránu Azure Firewall. Každé nové připojení, které odpovídá jednomu z vašich nakonfigurovaných pravidel aplikace, vytvoří pro dané přijaté nebo odepřené připojení protokol. Jak je vidět v následujícím příkladu, data se protokolují ve formátu JSON:

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

* **Učet úložiště**: Účty úložiště jsou nejvhodnější pro ukládání protokolů na delší dobu, které budete kontrolovat pouze v případě potřeby.
* **Centra událostí**: Centra událostí jsou skvělou volbou pro integrování protokolů s jinými nástroji správy akcí a informací o zabezpečení (SEIM), abyste o svých prostředcích získávali upozornění.
* **Protokoly Azure Monitor**: Protokoly Azure Monitor se nejlépe používá pro obecné monitorování vaší aplikace v reálném čase nebo při pohledu na trendy.

## <a name="activity-logs"></a>Protokoly aktivit

   Položky protokolu aktivit se ve výchozím nastavení shromažďují a můžete si je zobrazit na webu Azure Portal.

   Protokoly [aktivit Azure](../azure-resource-manager/management/view-activity-logs.md) (dříve označované jako provozní protokoly a protokoly auditu) můžete použít k zobrazení všech operací odeslaných do předplatného Azure.

## <a name="metrics"></a>Metriky

Metriky v Azure Monitoru jsou číselné hodnoty, které popisují některé aspekty systému v určitém čase. Metriky jsou shromažďovány každou minutu a jsou užitečné pro výstrahy, protože mohou být vzorkovány často. Výstraha může být aktivována rychle s relativně jednoduchou logikou.

Pro Azure Firewall jsou k dispozici následující metriky:

- **Počet přístupů pravidel aplikace** – počet přístupů pravidla aplikace.

    Jednotka: počet

- **Počet přístupů k síťovým pravidlům** – počet přístupů k pravidlům sítě.

    Jednotka: počet

- **Zpracovaná data** - Množství dat procházejících bránou firewall.

    Jednotka: bajty

- **Stav brány firewall** – označuje stav brány firewall na základě dostupnosti portu SNAT.

    Jednotka: procento

   Tato metrika má dvě dimenze:
  - Stav: Možné hodnoty jsou *V pořádku*, *Degradované*, *Není v pořádku*.
  - Důvod: Označuje důvod odpovídajícího stavu brány firewall. 

     Pokud jsou porty SNAT používány > 95%, jsou považovány za vyčerpané a stav je 50% se stavem =**Degradován** a důvod =**SNAT port**. Brána firewall udržuje zpracování přenosů a stávající připojení nejsou ovlivněny. Nová připojení však nemusí být navázána přerušovaně.

     Pokud se porty SNAT používají < 95 %, brána firewall je považována za zdravou a stav je zobrazen jako 100 %.

     Pokud není hlášeno žádné využití portů SNAT, stav se zobrazí jako 0 %. 

- **Využití portů SNAT** – procento portů SNAT, které byly využity bránou firewall.

    Jednotka: procento

   Přidáte-li do brány firewall další veřejné IP adresy, bude k dispozici více portů SNAT, což sníží využití portů SNAT. Navíc při škálování brány firewall z různých důvodů (například procesornebo propustnost) další porty SNAT také k dispozici. Tak efektivně, dané procento využití portů SNAT může jít dolů bez přidání jakékoli veřejné IP adresy, jen proto, že služba škálovat. Můžete přímo řídit počet veřejných IP adres, které jsou k dispozici pro zvýšení portů dostupných v bráně firewall. Ale nemůžete přímo řídit změnu velikosti firewallu. V současné době jsou porty SNAT přidány pouze pro prvních pět veřejných IP adres.   


## <a name="next-steps"></a>Další kroky

- Informace o monitorování protokolů a metrik azure firewall najdete [v tématu Sledování protokolů brány Azure .](tutorial-diagnostics.md)

- Další informace o metrikách v Azure Monitoru najdete [v tématu Metriky v Azure Monitoru](../azure-monitor/platform/data-platform-metrics.md).
