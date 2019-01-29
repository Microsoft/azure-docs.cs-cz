---
title: Koncové body služeb virtuální sítě a pravidel pro Azure Service Bus | Dokumentace Microsoftu
description: Přidáte koncový bod služby Microsoft.ServiceBus k virtuální síti.
services: service-bus
documentationcenter: ''
author: axisc
manager: timlt
editor: spelluru
ms.service: service-bus
ms.devlang: na
ms.topic: article
ms.date: 09/05/2018
ms.author: aschhab
ms.openlocfilehash: 8a5832433d1d8c81c22c828ec4659c10951f680a
ms.sourcegitcommit: eecd816953c55df1671ffcf716cf975ba1b12e6b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/28/2019
ms.locfileid: "55103628"
---
# <a name="use-virtual-network-service-endpoints-with-azure-service-bus"></a>Koncové body služeb virtuální sítě pomocí Azure Service Bus

Integrace služby Service Bus pomocí [koncové body služeb virtuální sítě (VNet)] [ vnet-sep] umožňuje zabezpečený přístup k možnosti zasílání zpráv z úloh, jako jsou virtuální počítače, které jsou vázány na virtuální sítě , síťová cesta provoz se zabezpečují na obou koncích.

Po nakonfigurování navázat na koncový bod služby podsítě virtuální sítě alespoň jeden příslušných obor názvů služby Service Bus už přijme přenos z libovolného místa, ale oprávnění virtuálních sítí. Z pohledu virtuální sítě vazba oboru názvů služby Service Bus na koncový bod služby nakonfiguruje izolované sítě tunelové propojení z podsítě virtuální sítě ke službě zasílání zpráv.

Výsledkem je privátní a izolované relaci mezi úlohami, které jsou vázány na podsíť a odpovídající oboru názvů Service Bus, přestože pozorovatelných síťovou adresu na zasílání zpráv služby koncového bodu, který v rozsahu veřejných IP.

>[!WARNING]
> Implementace integrace virtuální sítě můžete zabránit komunikaci se Service Bus dalšími službami Azure.
>
> Důvěryhodné Microsoft services nejsou podporovány, pokud virtuální sítě jsou implementovány a budou brzy dostupné.
>
> Běžné scénáře služby Azure, které nefungují s virtuálními sítěmi (Všimněte si, že je seznam **není** vyčerpávající)-
> - Azure Monitor
> - Azure Stream Analytics
> - Integrace s Azure Event Grid
> - Azure IoT Hub Routes
> - Azure IoT Device Explorer
> - Průzkumník dat Azure
>
> Níže Microsoft services musí být ve virtuální síti
> - Azure App Service
> - Azure Functions

> [!IMPORTANT]
> Virtuální sítě jsou podporovány pouze v [úroveň Premium](service-bus-premium-messaging.md) obory názvů služby Service Bus.

## <a name="enable-service-endpoints-with-service-bus"></a>Povolení koncových bodů služby pomocí služby Service Bus

Což je důležité při používání koncových bodů služby virtuální sítě pomocí služby Service Bus je, že by neměla být povolena těchto koncových bodů v aplikacích, které kombinovat názvů služby Service Bus úrovně Standard a Premium. Protože standardní úroveň nepodporuje virtuální sítě, je omezen na obory názvů úrovně Premium pouze koncový bod. Virtuální síť bude blokovat provoz na obor názvů Standard.

## <a name="advanced-security-scenarios-enabled-by-vnet-integration"></a>Pokročilé zabezpečení scénáře povolené ve integrace virtuální sítě 

Řešení, které vyžadují vysoké a zpřehlednění zabezpečení a kde podsítě virtuální sítě poskytuje segmentace mezi těmito službami compartmentalized obecně stále potřebovat komunikační trasy mezi službami, které se nacházejí v těchto oddílech.

Žádné okamžité IP trasy mezi oddíly, včetně těch, které může přenos HTTPS přes protokol TCP/IP, přináší riziko zneužití slabých míst z síťové vrstvy v provozu. Zasílání zpráv služby poskytují zcela izolované komunikační cesty, kde se zprávy i zapisují na disk po jejich přechod mezi stranami. Úlohy ve dvou různých virtuálních sítích, které jsou vázány na stejnou instanci služby Service Bus může komunikovat efektivně a spolehlivě prostřednictvím zprávy, zatímco je zajištěná integrita hranice izolace příslušné síti.
 
To znamená, že zabezpečení citlivých Cloudová řešení nejen získat přístup k Azure špičkové spolehlivou a škálovatelnou asynchronní možnosti zasílání zpráv, ale můžete nyní použít zasílání zpráv a vytvořit komunikační trasy mezi zabezpečené řešení, která compartments jsou ze své podstaty bezpečnější než s libovolném jiném režimu komunikace peer-to-peer, včetně protokolů HTTPS a jiné protokoly TLS zabezpečené soketu.

## <a name="binding-service-bus-to-virtual-networks"></a>Vazby služby Service Bus k virtuálním sítím

*Pravidla virtuální sítě* jsou funkce zabezpečení brány firewall, která určuje, jestli váš server Azure Service Bus akceptuje připojení z konkrétní virtuální sítě podsíť.

Vazba oboru názvů služby Service Bus k virtuální síti je dvoustupňový proces. Je nejprve potřeba vytvořit **koncový bod služby virtuální sítě** na podsíti virtuální sítě a povolit ho pro "Microsoft.ServiceBus", jako je vysvětleno v [přehled koncových bodů služby] [ vnet-sep]. Po přidání koncového bodu služby svážete oboru názvů služby Service Bus přes *pravidlo virtuální sítě*.

Pravidlo virtuální sítě je přidružené k oboru názvů služby Service Bus podsíti virtuální sítě. Přestože existuje pravidlo, všechny úlohy, které jsou vázány na podsíť je udělen přístup k oboru názvů služby Service Bus. Service Bus samotné nikdy vytvoří odchozí připojení, není potřeba získat přístup a je proto nikdy udělen přístup k vaší podsítě tím, že toto pravidlo.

### <a name="creating-a-virtual-network-rule-with-azure-resource-manager-templates"></a>Vytvoření pravidla virtuální sítě pomocí šablon Azure Resource Manageru

Následující šablony Resource Manageru umožňuje do existujícího oboru názvů služby Service Bus přidáte pravidlo virtuální sítě.

Parametry šablony:

* **namespaceName**: Obor názvů služby Service Bus.
* **virtualNetworkingSubnetId**: Plně kvalifikovaná cesta Resource Manageru pro virtuální síť podsíť; například `/subscriptions/{id}/resourceGroups/{rg}/providers/Microsoft.Network/virtualNetworks/{vnet}/subnets/default` pro výchozí podsíť virtuální sítě.

> [!NOTE]
> Nejsou žádná pravidla odepřít je to možné, šablony Azure Resource Manageru je nastavena na výchozí akce **"Povolit"** který nepodporuje omezení připojení.
> Při vytváření pravidla virtuální sítě a brány firewall, musíte Změníme ***"defaultAction"***
> 
> od
> ```json
> "defaultAction": "Allow"
> ```
> na
> ```json
> "defaultAction": "Deny"
> ```
>

Šablona:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
      "servicebusNamespaceName": {
        "type": "string",
        "metadata": {
          "description": "Name of the Service Bus namespace"
        }
      },
      "virtualNetworkName": {
        "type": "string",
        "metadata": {
          "description": "Name of the Virtual Network Rule"
        }
      },
      "subnetName": {
        "type": "string",
        "metadata": {
          "description": "Name of the Virtual Network Sub Net"
        }
      },
      "location": {
        "type": "string",
        "metadata": {
          "description": "Location for Namespace"
        }
      }
    },
    "variables": {
      "namespaceNetworkRuleSetName": "[concat(parameters('servicebusNamespaceName'), concat('/', 'default'))]",
      "subNetId": "[resourceId('Microsoft.Network/virtualNetworks/subnets/', parameters('virtualNetworkName'), parameters('subnetName'))]"
    },
    "resources": [
      {
        "apiVersion": "2018-01-01-preview",
        "name": "[parameters('servicebusNamespaceName')]",
        "type": "Microsoft.ServiceBus/namespaces",
        "location": "[parameters('location')]",
        "sku": {
          "name": "Premium",
          "tier": "Premium"
        },
        "properties": { }
      },
      {
        "apiVersion": "2017-09-01",
        "name": "[parameters('virtualNetworkName')]",
        "location": "[parameters('location')]",
        "type": "Microsoft.Network/virtualNetworks",
        "properties": {
          "addressSpace": {
            "addressPrefixes": [
              "10.0.0.0/23"
            ]
          },
          "subnets": [
            {
              "name": "[parameters('subnetName')]",
              "properties": {
                "addressPrefix": "10.0.0.0/23",
                "serviceEndpoints": [
                  {
                    "service": "Microsoft.ServiceBus"
                  }
                ]
              }
            }
          ]
        }
      },
      {
        "apiVersion": "2018-01-01-preview",
        "name": "[variables('namespaceNetworkRuleSetName')]",
        "type": "Microsoft.ServiceBus/namespaces/networkruleset",
        "dependsOn": [
          "[concat('Microsoft.ServiceBus/namespaces/', parameters('servicebusNamespaceName'))]"
        ],
        "properties": {
          "virtualNetworkRules": 
          [
            {
              "subnet": {
                "id": "[variables('subNetId')]"
              },
              "ignoreMissingVnetServiceEndpoint": false
            }
          ],
          "ipRules":[<YOUR EXISTING IP RULES>],
          "defaultAction": "Deny"
        }
      }
    ],
    "outputs": { }
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
