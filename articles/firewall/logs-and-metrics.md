---
title: Přehled protokolů Brána Firewall služby Azure
description: Tento článek představuje přehled diagnostických protokolů Brána Firewall služby Azure.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 9/24/2018
ms.author: victorh
ms.openlocfilehash: c129c394f3d694b832722287027c1f9e58028a33
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "61065848"
---
# <a name="azure-firewall-logs"></a>Azure protokoly brány Firewall

Bránu Azure Firewall můžete monitorovat pomocí protokolů brány firewall. K auditu operací na prostředcích brány Azure Firewall můžete také použít protokoly aktivit.

Některé z těchto protokolů jsou přístupné z webu Azure Portal. Je možné odeslat protokoly [protokoly Azure monitoru](../azure-monitor/insights/azure-networking-analytics.md), úložiště a centra událostí a analyzovat v protokolech Azure Monitor nebo prostřednictvím různých nástrojů, jako je Excel a Power BI.

## <a name="diagnostic-logs"></a>Diagnostické protokoly

 Pro bránu Azure Firewall jsou k dispozici následující diagnostické protokoly:

* **Protokol pravidel aplikace**

   Pravidlo protokolu je uložen na účet úložiště, Streamovat do centra událostí a/nebo odesílat protokoly Azure monitoru pouze v případě, že je povolená pro každou Brána Firewall služby Azure. Každé nové připojení, které odpovídá jednomu z vašich nakonfigurovaných pravidel aplikace, vytvoří pro dané přijaté nebo odepřené připojení protokol. Jak je vidět v následujícím příkladu, data se protokolují ve formátu JSON:

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

   Protokol sítě pravidlo se ukládá do účtu úložiště, Streamovat do centra událostí a/nebo odesílat protokoly Azure monitoru pouze v případě, že je povolená pro každou Brána Firewall služby Azure. Každé nové připojení, které odpovídá jednomu z vašich nakonfigurovaných pravidel aplikace, vytvoří pro dané přijaté nebo odepřené připojení protokol. Jak je vidět v následujícím příkladu, data se protokolují ve formátu JSON:

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

* **Účet úložiště**: Účty úložiště jsou nejvhodnější pro protokoly protokoly ukládání delší dobu a zkontrolovat v případě potřeby.
* **Služba Event hubs**: Služba Event hubs jsou skvělou možností pro integraci s dalšími nástroji pro správu (SEIM) informace a události zabezpečení, abyste mohli dostávat upozornění na prostředky.
* **Protokoly Azure monitoru**: Protokoly služby Azure Monitor je nejvhodnější pro obecné v reálném čase sledování vaší aplikace nebo si chcete prohlédnout trendy.

## <a name="activity-logs"></a>Protokoly aktivit

   Položky protokolu aktivit se ve výchozím nastavení shromažďují a můžete si je zobrazit na webu Azure Portal.

   Všechny operace odeslané do předplatného Azure si můžete zobrazit v [protokolech aktivit Azure](../azure-resource-manager/resource-group-audit.md) (dříve operační protokoly a protokoly auditu).


## <a name="next-steps"></a>Další postup

Další informace o monitorování metrik a protokolování Brána Firewall služby Azure, najdete v článku [kurzu: Monitorujte protokoly brány Firewall Azure](tutorial-diagnostics.md).