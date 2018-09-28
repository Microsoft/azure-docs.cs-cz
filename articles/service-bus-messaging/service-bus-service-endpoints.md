---
title: Koncové body služeb virtuální sítě a pravidel pro Azure Service Bus | Dokumentace Microsoftu
description: Přidáte koncový bod služby Microsoft.ServiceBus k virtuální síti.
services: event-hubs
documentationcenter: ''
author: clemensv
manager: timlt
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.date: 09/05/2018
ms.author: clemensv
ms.openlocfilehash: 05930dfce64378d792213ccaefa3d15057bd5dfd
ms.sourcegitcommit: b7e5bbbabc21df9fe93b4c18cc825920a0ab6fab
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/27/2018
ms.locfileid: "47404993"
---
# <a name="use-virtual-network-service-endpoints-with-azure-service-bus"></a>Koncové body služeb virtuální sítě pomocí Azure Service Bus

Integrace služby Service Bus pomocí [koncové body služeb virtuální sítě (VNet)] [ vnet-sep] umožňuje zabezpečený přístup k možnosti zasílání zpráv z úloh, jako jsou virtuální počítače, které jsou vázány na virtuální sítě , síťová cesta provoz se zabezpečují na obou koncích. 

Po nakonfigurování navázat na koncový bod služby podsítě virtuální sítě alespoň jeden příslušných obor názvů služby Service Bus už přijme přenos z libovolného místa, ale oprávnění virtuálních sítí. Z pohledu virtuální sítě vazba oboru názvů služby Service Bus na koncový bod služby nakonfiguruje izolované sítě tunelové propojení z podsítě virtuální sítě ke službě zasílání zpráv.

Výsledkem je privátní a izolované relaci mezi úlohami, které jsou vázány na podsíť a odpovídající oboru názvů Service Bus, přestože pozorovatelných síťovou adresu na zasílání zpráv služby koncového bodu, který v rozsahu veřejných IP.

## <a name="enable-service-endpoints-with-service-bus"></a>Povolení koncových bodů služby pomocí služby Service Bus

Virtuální sítě jsou podporovány pouze v [úroveň Premium](service-bus-premium-messaging.md) obory názvů služby Service Bus. 

Což je důležité při používání koncových bodů služby virtuální sítě pomocí služby Service Bus je, že by neměla být povolena těchto koncových bodů v aplikacích, které kombinovat názvů služby Service Bus úrovně Standard a Premium. Protože standardní úroveň nepodporuje virtuální sítě, je omezen na obory názvů úrovně Premium pouze koncový bod. Virtuální síť bude blokovat provoz na obor názvů Standard. 

## <a name="advanced-security-scenarios-enabled-by-vnet-integration"></a>Pokročilé zabezpečení scénáře povolené ve integrace virtuální sítě 

Řešení, které vyžadují vysoké a zpřehlednění zabezpečení a kde podsítě virtuální sítě poskytuje segmentace mezi těmito službami compartmentalized obecně stále potřebovat komunikační trasy mezi službami, které se nacházejí v těchto oddílech.

Žádné okamžité IP trasy mezi oddíly, včetně těch, které může přenos HTTPS přes protokol TCP/IP, přináší riziko zneužití slabých míst z síťové vrstvy v provozu. Zasílání zpráv služby poskytují zcela izolované komunikační cesty, kde se zprávy i zapisují na disk po jejich přechod mezi stranami. Úlohy ve dvou různých virtuálních sítích, které jsou vázány na stejnou instanci služby Service Bus může komunikovat efektivně a spolehlivě prostřednictvím zprávy, zatímco je zajištěná integrita hranice izolace příslušné síti.
 
To znamená, že zabezpečení citlivých Cloudová řešení nejen získat přístup k Azure špičkové spolehlivou a škálovatelnou asynchronní možnosti zasílání zpráv, ale můžete nyní použít zasílání zpráv a vytvořit komunikační trasy mezi zabezpečené řešení, která compartments jsou ze své podstaty bezpečnější než s libovolném jiném režimu komunikace peer-to-peer, včetně protokolů HTTPS a jiné protokoly TLS zabezpečené soketu.

## <a name="binding-service-bus-to-virtual-networks"></a>Vazby služby Service Bus k virtuálním sítím

*Pravidla virtuální sítě* jsou funkce zabezpečení brány firewall, která určuje, jestli váš server Azure Service Bus akceptuje připojení z konkrétní virtuální sítě podsíť.

Vazba oboru názvů služby Service Bus k virtuální síti je dvoustupňový proces. Je nejprve potřeba vytvořit **koncový bod služby virtuální sítě** na podsíti virtuální sítě a povolit ho pro "Microsoft.ServiceBus", jako je vysvětleno v [přehled koncových bodů služby] [ vnet-sep]. Po přidání koncového bodu služby svážete oboru názvů služby Service Bus přes *pravidlo virtuální sítě*.

Pravidlo virtuální sítě je pojmenovaný přidružení oboru názvů služby Service Bus k podsíti virtuální sítě. Přestože existuje pravidlo, všechny úlohy, které jsou vázány na podsíť je udělen přístup k oboru názvů služby Service Bus. Service Bus samotné nikdy vytvoří odchozí připojení, není potřeba získat přístup a je proto nikdy udělen přístup k vaší podsítě tím, že toto pravidlo.

### <a name="creating-a-virtual-network-rule-with-azure-resource-manager-templates"></a>Vytvoření pravidla virtuální sítě pomocí šablon Azure Resource Manageru

Následující šablony Resource Manageru umožňuje do existujícího oboru názvů služby Service Bus přidáte pravidlo virtuální sítě.

Parametry šablony:

* **namespaceName**: obor názvů služby Service Bus.
* **vnetRuleName**: název pravidla virtuální sítě má být vytvořen.
* **virtualNetworkingSubnetId**: plně kvalifikovanou cestu Resource Manageru pro podsíť virtuální sítě, například `/subscriptions/{id}/resourceGroups/{rg}/providers/Microsoft.Network/virtualNetworks/{vnet}/subnets/default` pro výchozí podsíť virtuální sítě.

Šablona:

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
            "type":"Microsoft.ServiceBus/namespaces/VirtualNetworkRules",           
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
- [Azure Service Bus IP filtrování][ip-filtering]

[vnet-sep]: ../virtual-network/virtual-network-service-endpoints-overview.md
[lnk-deploy]: ../azure-resource-manager/resource-group-template-deploy.md
[ip-filtering]: service-bus-ip-filtering.md