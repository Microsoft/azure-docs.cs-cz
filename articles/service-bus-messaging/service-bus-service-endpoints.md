---
title: Koncové body služby virtuální sítě a pravidla pro Azure Service Bus | Microsoft Docs
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
ms.openlocfilehash: 7716ff503bd492cc4b5d510758cb20d74eb82a4f
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/27/2018
ms.locfileid: "37035983"
---
# <a name="use-virtual-network-service-endpoints-with-azure-service-bus"></a>Koncové body služby virtuální sítě pomocí služby Azure Service Bus

Integrace služby Service Bus s [koncové body služby Virtual Network (VNet)] [ vnet-sep] umožňuje zabezpečit přístup do funkce pro zasílání zpráv z úloh, jako jsou virtuální počítače, které jsou vázány na virtuální sítě , síťová cesta provoz probíhá na obou koncích zabezpečené. 

Po nakonfigurování bylo vázané na koncový bod služby podsíť virtuální sítě alespoň jeden příslušných oboru názvů Service Bus nebude přijímat přenosy z libovolného místa, ale oprávnění virtuální sítě. Z pohledu virtuální sítě vazby oboru názvů Service Bus pro koncový bod služby nakonfiguruje izolované sítě tunelového propojení z podsítě virtuální sítě pro službu zasílání zpráv.

Výsledkem je, privátní a izolované vztah mezi zatížení vázaných na podsíť a příslušných oboru názvů Service Bus, přestože pozorovatelné síťovou adresu na zasílání zpráv service koncový bod, který v rozsahu veřejné IP.

## <a name="advanced-security-scenarios-enabled-by-vnet-integration"></a>Scénáře pokročilým zabezpečením ve virtuální síti integrace povoleno 

Řešení, které vyžadují úzkou a zpřehlednění zabezpečení a kde podsítě virtuální sítě zadejte segmentace mezi compartmentalized služby obecně stále nutné komunikační trasy mezi službami, které se nacházejí v těchto oddílech.

Všechny okamžitou trasy IP mezi oddílů, včetně těch, které provedení HTTPS přes TCP/IP, představuje riziko zneužití slabých míst z síťovou vrstvou v nahoru. Zasílání zpráv služby poskytují zcela izolované komunikační cesty, kde jsou zprávy i zapsat na disk jako jejich přechod mezi stranami. Úlohy ve dvou různých virtuálních sítích, které jsou vázány na stejnou instanci služby Service Bus může komunikovat efektivně a spolehlivě prostřednictvím zprávy, když je zajištěná integrita hranic izolace odpovídající síťové.
 
To znamená, že compartments citlivé cloudového řešení nejen získat přístup k špičkový spolehlivou a škálovatelnou asynchronní zasílání zpráv možnosti Azure, ale můžou teď použít zasílání zpráv k vytvoření cest komunikace mezi bezpečné řešení zabezpečení, který jsou ze své podstaty bezpečnější než co je možná s libovolném režimu komunikace peer-to-peer, včetně protokolů HTTPS a jiné protokoly TLS zabezpečené soketu.

## <a name="binding-service-bus-to-virtual-networks"></a>Služba Service Bus vazby virtuální sítě

*Pravidla pro virtuální sítě* jsou funkce zabezpečení brány firewall, která určuje, zda váš server Azure Service Bus akceptuje připojení z konkrétní virtuální podsíti.

Vazba k virtuální síti obor názvů sběrnice je dvoustupňový proces. Je nutné nejprve vytvořit **koncový bod služby virtuální sítě** v podsíti virtuální sítě a povolit pro "Microsoft.ServiceBus" jako podrobně [Přehled koncový bod služby] [ vnet-sep]. Po přidání koncového bodu služby, můžete k němu s vazby oboru názvů Service Bus *pravidlo virtuální sítě*.

Je pravidlo virtuální síť s názvem přidružení oboru názvů Service Bus s podsítí virtuální sítě. Pravidlo existuje, jsou všechny úlohy, které jsou vázány na podsíť udělen přístup k oboru názvů Service Bus. Service Bus samotné nikdy vytvoří odchozí připojení, není potřeba získat přístup a je proto nikdy udělený přístup ke podsíť povolení tohoto pravidla.

### <a name="creating-a-virtual-network-rule-with-azure-resource-manager-templates"></a>Vytvoření pravidla virtuální sítě pomocí šablony Azure Resource Manager

Následující šablony Resource Manager umožňuje přidat pravidlo virtuální sítě do existujícího oboru názvů Service Bus.

Parametry šablony:

* **Parametr namespaceName**: oboru názvů Service Bus.
* **vnetRuleName**: název pravidla virtuální sítě má být vytvořen.
* **virtualNetworkingSubnetId**: plně kvalifikované cesty správce prostředků pro podsítě virtuální sítě; například `/subscriptions/{id}/resourceGroups/{rg}/providers/Microsoft.Network/virtualNetworks/{vnet}/subnets/default` pro výchozí podsíť virtuální sítě.

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

Pokud chcete nasadit šablonu, postupujte podle pokynů pro [Azure Resource Manager][lnk-deploy].

## <a name="next-steps"></a>Další postup

Další informace o virtuálních sítích najdete v následujících tématech:

- [Koncové body služby virtuální síť Azure][vnet-sep]
- [Filtrování Azure Service Bus IP][ip-filtering]

[vnet-sep]: ../virtual-network/virtual-network-service-endpoints-overview.md
[lnk-deploy]: ../azure-resource-manager/resource-group-template-deploy.md
[ip-filtering]: service-bus-ip-filtering.md