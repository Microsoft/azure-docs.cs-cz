---
title: Koncové body služby virtuální sítě a pravidla pro Azure Event Hubs | Microsoft Docs
description: Přidání koncového bodu služby Microsoft.ServiceBus k virtuální síti.
services: event-hubs
documentationcenter: ''
author: clemensv
manager: timlt
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.date: 06/26/2018
ms.author: clemensv
ms.openlocfilehash: a23e5414cd3c60192badfee65b14c49cd5e96f4e
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/27/2018
ms.locfileid: "37035784"
---
# <a name="use-virtual-network-service-endpoints-with-azure-event-hubs"></a>Koncové body služby virtuální sítě pomocí Azure Event Hubs

Integrace služby Event Hubs s [koncové body služby Virtual Network (VNet)] [ vnet-sep] umožňuje zabezpečit přístup do funkce pro zasílání zpráv z úlohy, jako například virtuální počítače, které jsou vázané na virtuální sítě, síťová cesta provoz probíhá na obou koncích zabezpečené. 

Po nakonfigurování bylo vázané na koncový bod služby podsíť alespoň jeden virtuální sítě už odpovídajících názvů Event Hubs přijímá provoz z libovolného místa, ale oprávnění virtuální sítě. Z pohledu virtuální sítě vazby na obor názvů služby Event Hubs pro koncový bod služby nakonfiguruje izolované sítě tunelového propojení z podsítě virtuální sítě pro službu zasílání zpráv.

Výsledkem je, privátní a izolované vztah mezi zatížení vázaných na podsíť a příslušných oboru názvů služby Event Hubs, přestože pozorovatelné síťovou adresu na zasílání zpráv service koncový bod, který v rozsahu veřejné IP.

## <a name="advanced-security-scenarios-enabled-by-vnet-integration"></a>Scénáře pokročilým zabezpečením ve virtuální síti integrace povoleno 

Řešení, které vyžadují úzkou a zpřehlednění zabezpečení a kde podsítě virtuální sítě zadejte segmentace mezi compartmentalized služby obecně stále nutné komunikační trasy mezi službami, které se nacházejí v těchto oddílech.

Všechny okamžitou trasy IP mezi oddílů, včetně těch, které provedení HTTPS přes TCP/IP, představuje riziko zneužití slabých míst z síťovou vrstvou v nahoru. Zasílání zpráv služby poskytují zcela izolované komunikační cesty, kde jsou zprávy i zapsat na disk jako jejich přechod mezi stranami. Úlohy ve dvou různých virtuálních sítích, které jsou vázány na stejnou instanci služby Event Hubs může komunikovat efektivně a spolehlivě prostřednictvím zprávy, když je zajištěná integrita hranic izolace odpovídající síťové.
 
To znamená, že compartments citlivé cloudového řešení nejen získat přístup k špičkový spolehlivou a škálovatelnou asynchronní zasílání zpráv možnosti Azure, ale můžou teď použít zasílání zpráv k vytvoření cest komunikace mezi bezpečné řešení zabezpečení, který jsou ze své podstaty bezpečnější než co je možná s libovolném režimu komunikace peer-to-peer, včetně protokolů HTTPS a jiné protokoly TLS zabezpečené soketu.

## <a name="bind-event-hubs-to-virtual-networks"></a>Vytvoření vazby služby Event Hubs k virtuálním sítím

*Pravidla pro virtuální sítě* jsou funkce zabezpečení brány firewall, která určuje, zda váš server Azure Event Hubs akceptuje připojení z konkrétní virtuální podsíti.

Vytvoření vazby na obor názvů služby Event Hubs k virtuální síti je dvoustupňový proces. Je nutné nejprve vytvořit **koncový bod služby virtuální sítě** v podsíti virtuální sítě a povolit pro "Microsoft.ServiceBus" jako podrobně [Přehled koncový bod služby] [ vnet-sep]. Po přidání koncového bodu služby, lze vázat na její obor názvů služby Event Hubs *pravidlo virtuální sítě*.

Je pravidlo virtuální síť s názvem přidružení oboru názvů služby Event Hubs s podsítí virtuální sítě. Existuje pravidlo, všechny úlohy, které jsou vázány na podsíť je uděleno oprávnění k oboru názvů služby Event Hubs. Služba Event Hubs samostatně nikdy vytvoří odchozí připojení, není potřeba získat přístup a je proto nikdy udělený přístup ke podsíť povolení tohoto pravidla.

### <a name="create-a-virtual-network-rule-with-azure-resource-manager-templates"></a>Vytvoření pravidla virtuální sítě pomocí šablony Azure Resource Manager

Následující šablony Resource Manager umožňuje přidat pravidlo virtuální sítě do existujícího oboru názvů služby Event Hubs.

Parametry šablony:

* **Parametr namespaceName**: obor názvů služby Event Hubs.
* **vnetRuleName**: název pravidla virtuální sítě má být vytvořen.
* **virtualNetworkingSubnetId**: plně kvalifikované cesty správce prostředků pro podsítě virtuální sítě; například `subscriptions/{id}/resourceGroups/{rg}/providers/Microsoft.Network/virtualNetworks/{vnet}/subnets/default` pro výchozí podsíť virtuální sítě.

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

Pokud chcete nasadit šablonu, postupujte podle pokynů pro [Azure Resource Manager][lnk-deploy].

## <a name="next-steps"></a>Další postup

Další informace o virtuálních sítích najdete v následujících tématech:

- [Koncové body služby virtuální síť Azure][vnet-sep]
- [Filtrování Azure Event Hubs IP][ip-filtering]

[vnet-sep]: ../virtual-network/virtual-network-service-endpoints-overview.md
[lnk-deploy]: ../azure-resource-manager/resource-group-template-deploy.md
[ip-filtering]: event-hubs-ip-filtering.md