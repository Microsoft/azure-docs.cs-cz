---
title: Virtuální síť koncové body služby – Azure Event Hubs | Dokumentace Microsoftu
description: Tento článek obsahuje informace o tom, jak adda Microsoft.EventHub koncového bodu služby virtuální sítě.
services: event-hubs
documentationcenter: ''
author: ShubhaVijayasarathy
manager: timlt
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.date: 12/06/2018
ms.author: shvija
ms.openlocfilehash: 077202e65c9e63c8ca5ea1a555ccd70bf27028c6
ms.sourcegitcommit: de81b3fe220562a25c1aa74ff3aa9bdc214ddd65
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2019
ms.locfileid: "56232599"
---
# <a name="use-virtual-network-service-endpoints-with-azure-event-hubs"></a>Koncové body služeb virtuální sítě pomocí Azure Event Hubs

Integrace Event Hubs s využitím [koncové body služeb virtuální sítě (VNet)] [ vnet-sep] umožňuje zabezpečený přístup k možnosti zasílání zpráv z úloh, jako jsou virtuální počítače, které jsou vázány na virtuální sítě, síťová cesta provoz se zabezpečují na obou koncích.

Po nakonfigurování navázat na koncový bod služby podsítě alespoň jednu virtuální síť, příslušný obor názvů Event Hubs již přijímá provoz z libovolného místa, ale oprávnění podsítí ve virtuálních sítích. Z pohledu virtuální sítě nakonfiguruje vazby obor názvů služby Event Hubs do koncového bodu služby izolované sítě tunelové propojení z podsítě virtuální sítě ke službě zasílání zpráv.

Výsledkem je privátní a izolované relaci mezi úlohami, které jsou vázány na podsíť a odpovídající obor názvů Event Hubs, přestože pozorovatelných síťovou adresu na zasílání zpráv služby koncového bodu, který v rozsahu veřejných IP.

>[!WARNING]
> Implementace integrace virtuální sítě můžete zabránit interakci s centry událostí dalšími službami Azure.
>
> Důvěryhodné Microsoft services nejsou podporovány, pokud jsou implementovány virtuální sítě.
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
> - Azure Web Apps
> - Azure Functions

> [!IMPORTANT]
> Virtuální sítě jsou podporovány v **standardní** a **vyhrazené** úrovně služby Event Hubs. Není podporována v úrovni basic.

## <a name="advanced-security-scenarios-enabled-by-vnet-integration"></a>Pokročilé zabezpečení scénáře povolené ve integrace virtuální sítě 

Řešení, které vyžadují vysoké a zpřehlednění zabezpečení a kde podsítě virtuální sítě poskytuje segmentace mezi těmito službami compartmentalized obecně stále potřebovat komunikační trasy mezi službami, které se nacházejí v těchto oddílech.

Žádné okamžité IP trasy mezi oddíly, včetně těch, které může přenos HTTPS přes protokol TCP/IP, přináší riziko zneužití slabých míst z síťové vrstvy v provozu. Zasílání zpráv služby poskytují zcela izolované komunikační cesty, kde se zprávy i zapisují na disk po jejich přechod mezi stranami. Úlohy ve dvou různých virtuálních sítích, které jsou vázány na stejnou instanci služby Event Hubs může komunikovat efektivně a spolehlivě prostřednictvím zprávy, zatímco je zajištěná integrita hranice izolace příslušné síti.
 
To znamená, že zabezpečení citlivých Cloudová řešení nejen získat přístup k Azure špičkové spolehlivou a škálovatelnou asynchronní možnosti zasílání zpráv, ale můžete nyní použít zasílání zpráv a vytvořit komunikační trasy mezi zabezpečené řešení, která compartments jsou ze své podstaty bezpečnější než s libovolném jiném režimu komunikace peer-to-peer, včetně protokolů HTTPS a jiné protokoly TLS zabezpečené soketu.

## <a name="bind-event-hubs-to-virtual-networks"></a>Vytvoření vazby služby Event Hubs k virtuálním sítím

*Pravidla virtuální sítě* jsou funkce zabezpečení brány firewall, která určuje, zda váš obor názvů Azure Event Hubs akceptuje připojení z konkrétní virtuální síť podsíť.

Obor názvů služby Event Hubs vazba k virtuální síti je dvoustupňový proces. Je nejprve potřeba vytvořit **koncový bod služby virtuální sítě** na podsíti virtuální sítě a povolit ho pro "Microsoft.EventHub", jako je vysvětleno v [přehled koncových bodů služby] [ vnet-sep]. Po přidání koncového bodu služby, můžete vytvořit vazbu oboru názvů Event Hubs přes *pravidlo virtuální sítě*.

Pravidlo virtuální sítě je přidružené k oboru názvů služby Event Hubs podsíti virtuální sítě. Přestože existuje pravidlo, všechny úlohy, které jsou vázány na podsíť je udělen přístup k oboru názvů Event Hubs. Event Hubs samostatně nikdy vytvoří odchozí připojení, není potřeba získat přístup a je proto nikdy udělen přístup k vaší podsítě tím, že toto pravidlo.

### <a name="create-a-virtual-network-rule-with-azure-resource-manager-templates"></a>Vytvořit pravidlo virtuální sítě pomocí šablon Azure Resource Manageru

Následující šablony Resource Manageru umožňuje do existujícího oboru názvů služby Event Hubs přidáte pravidlo virtuální sítě.

Parametry šablony:

* **namespaceName**: Obor názvů služby Event Hubs.
* **vnetRuleName**: Název pravidla virtuální sítě má být vytvořen.
* **virtualNetworkingSubnetId**: Plně kvalifikovaná cesta Resource Manageru pro virtuální síť podsíť; například `subscriptions/{id}/resourceGroups/{rg}/providers/Microsoft.Network/virtualNetworks/{vnet}/subnets/default` pro výchozí podsíť virtuální sítě.

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

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
      "eventhubNamespaceName": {
        "type": "string",
        "metadata": {
          "description": "Name of the Event Hubs namespace"
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
      "namespaceNetworkRuleSetName": "[concat(parameters('eventhubNamespaceName'), concat('/', 'default'))]",
      "subNetId": "[resourceId('Microsoft.Network/virtualNetworks/subnets/', parameters('virtualNetworkName'), parameters('subnetName'))]"
    },
    "resources": [
      {
        "apiVersion": "2018-01-01-preview",
        "name": "[parameters('eventhubNamespaceName')]",
        "type": "Microsoft.EventHub/namespaces",
        "location": "[parameters('location')]",
        "sku": {
          "name": "Standard",
          "tier": "Standard"
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
                    "service": "Microsoft.EventHub"
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
        "type": "Microsoft.EventHub/namespaces/networkruleset",
        "dependsOn": [
          "[concat('Microsoft.EventHub/namespaces/', parameters('eventhubNamespaceName'))]"
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
- [Azure Event Hubs IP filtrování][ip-filtering]

[vnet-sep]: ../virtual-network/virtual-network-service-endpoints-overview.md
[lnk-deploy]: ../azure-resource-manager/resource-group-template-deploy.md
[ip-filtering]: event-hubs-ip-filtering.md
