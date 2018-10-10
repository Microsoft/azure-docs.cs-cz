---
title: Koncové body služeb virtuální sítě a pravidel pro Azure Event Hubs | Dokumentace Microsoftu
description: Přidáte koncový bod služby Microsoft.EventHub k virtuální síti.
services: event-hubs
documentationcenter: ''
author: ShubhaVijayasarathy
manager: timlt
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.date: 08/16/2018
ms.author: shvija
ms.openlocfilehash: ff0ebbb140627caaaa71c5d09d0a4078eca86055
ms.sourcegitcommit: 55952b90dc3935a8ea8baeaae9692dbb9bedb47f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2018
ms.locfileid: "48888068"
---
# <a name="use-virtual-network-service-endpoints-with-azure-event-hubs"></a>Koncové body služeb virtuální sítě pomocí Azure Event Hubs

Integrace Event Hubs s využitím [koncové body služeb virtuální sítě (VNet)] [ vnet-sep] umožňuje zabezpečený přístup k možnosti zasílání zpráv z úloh, jako jsou virtuální počítače, které jsou vázány na virtuální sítě, síťová cesta provoz se zabezpečují na obou koncích. 

> [!IMPORTANT]
> Virtuální sítě jsou podporovány v **standardní** a **vyhrazené** úrovně služby Event Hubs. Není podporována v úrovni basic. 

Po nakonfigurování navázat na koncový bod služby podsítě alespoň jednu virtuální síť, příslušný obor názvů Event Hubs již přijímá provoz z libovolného místa, ale oprávnění virtuálních sítí. Z pohledu virtuální sítě nakonfiguruje vazby obor názvů služby Event Hubs do koncového bodu služby izolované sítě tunelové propojení z podsítě virtuální sítě ke službě zasílání zpráv.

Výsledkem je privátní a izolované relaci mezi úlohami, které jsou vázány na podsíť a odpovídající obor názvů Event Hubs, přestože pozorovatelných síťovou adresu na zasílání zpráv služby koncového bodu, který v rozsahu veřejných IP.

## <a name="advanced-security-scenarios-enabled-by-vnet-integration"></a>Pokročilé zabezpečení scénáře povolené ve integrace virtuální sítě 

Řešení, které vyžadují vysoké a zpřehlednění zabezpečení a kde podsítě virtuální sítě poskytuje segmentace mezi těmito službami compartmentalized obecně stále potřebovat komunikační trasy mezi službami, které se nacházejí v těchto oddílech.

Žádné okamžité IP trasy mezi oddíly, včetně těch, které může přenos HTTPS přes protokol TCP/IP, přináší riziko zneužití slabých míst z síťové vrstvy v provozu. Zasílání zpráv služby poskytují zcela izolované komunikační cesty, kde se zprávy i zapisují na disk po jejich přechod mezi stranami. Úlohy ve dvou různých virtuálních sítích, které jsou vázány na stejnou instanci služby Event Hubs může komunikovat efektivně a spolehlivě prostřednictvím zprávy, zatímco je zajištěná integrita hranice izolace příslušné síti.
 
To znamená, že zabezpečení citlivých Cloudová řešení nejen získat přístup k Azure špičkové spolehlivou a škálovatelnou asynchronní možnosti zasílání zpráv, ale můžete nyní použít zasílání zpráv a vytvořit komunikační trasy mezi zabezpečené řešení, která compartments jsou ze své podstaty bezpečnější než s libovolném jiném režimu komunikace peer-to-peer, včetně protokolů HTTPS a jiné protokoly TLS zabezpečené soketu.

## <a name="bind-event-hubs-to-virtual-networks"></a>Vytvoření vazby služby Event Hubs k virtuálním sítím

*Pravidla virtuální sítě* jsou funkce zabezpečení brány firewall, která určuje, jestli váš server Azure Event Hubs akceptuje připojení z konkrétní virtuální síť podsíť.

Obor názvů služby Event Hubs vazba k virtuální síti je dvoustupňový proces. Je nejprve potřeba vytvořit **koncový bod služby virtuální sítě** na podsíti virtuální sítě a povolit ho pro "Microsoft.EventHub", jako je vysvětleno v [přehled koncových bodů služby] [ vnet-sep]. Po přidání koncového bodu služby, můžete vytvořit vazbu oboru názvů Event Hubs přes *pravidlo virtuální sítě*.

Pravidlo virtuální sítě je pojmenovaný přidružení oboru názvů služby Event Hubs s podsítí virtuální sítě. Přestože existuje pravidlo, všechny úlohy, které jsou vázány na podsíť je udělen přístup k oboru názvů Event Hubs. Event Hubs samostatně nikdy vytvoří odchozí připojení, není potřeba získat přístup a je proto nikdy udělen přístup k vaší podsítě tím, že toto pravidlo.

### <a name="create-a-virtual-network-rule-with-azure-resource-manager-templates"></a>Vytvořit pravidlo virtuální sítě pomocí šablon Azure Resource Manageru

Následující šablony Resource Manageru umožňuje do existujícího oboru názvů služby Event Hubs přidáte pravidlo virtuální sítě.

Parametry šablony:

* **namespaceName**: obor názvů Event Hubs.
* **vnetRuleName**: název pravidla virtuální sítě má být vytvořen.
* **virtualNetworkingSubnetId**: plně kvalifikovanou cestu Resource Manageru pro podsíť virtuální sítě, například `subscriptions/{id}/resourceGroups/{rg}/providers/Microsoft.Network/virtualNetworks/{vnet}/subnets/default` pro výchozí podsíť virtuální sítě.

```json
{  
   "$schema":"http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
   "contentVersion":"1.0.0.0",
   "parameters":{     
          "namespaceName":{  
             "type":"string",
             "metadata":{  
                "description":"Name of the namespace"
             }
          },
          "vnetRuleName":{  
             "type":"string",
             "metadata":{  
                "description":"Name of the Authorization rule"
             }
          },
          "virtualNetworkSubnetId":{  
             "type":"string",
             "metadata":{  
                "description":"subnet Azure Resource Manager ID"
             }
          }
      },
    "resources": [
        {
            "apiVersion": "2018-01-01-preview",
            "name": "[concat(parameters('namespaceName'), '/', parameters('vnetRuleName'))]",
            "type":"Microsoft.EventHub/namespaces/VirtualNetworkRules",         
            "properties": {             
                "virtualNetworkSubnetId": "[parameters('virtualNetworkSubnetId')]"  
            }
        } 
    ]
}
```

Pokud chcete nasadit šablonu, postupujte podle pokynů pro [Azure Resource Manageru][lnk-deploy].

## <a name="next-steps"></a>Další postup

Další informace o virtuálních sítích najdete v následujících tématech:

- [Koncové body služby virtuální sítě Azure][vnet-sep]
- [Azure Event Hubs IP filtrování][ip-filtering]

[vnet-sep]: ../virtual-network/virtual-network-service-endpoints-overview.md
[lnk-deploy]: ../azure-resource-manager/resource-group-template-deploy.md
[ip-filtering]: event-hubs-ip-filtering.md