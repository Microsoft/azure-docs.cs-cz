---
title: Virtuální síť koncové body služby – Azure Event Hubs | Dokumentace Microsoftu
description: Tento článek poskytuje informace o tom, jak přidat koncový bod služby Microsoft. EventHub do virtuální sítě.
services: event-hubs
documentationcenter: ''
author: ShubhaVijayasarathy
manager: timlt
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.date: 11/26/2019
ms.author: shvija
ms.openlocfilehash: 2ac89444bde4e2efc918aced9d76c099eb792557
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/15/2020
ms.locfileid: "75966003"
---
# <a name="use-virtual-network-service-endpoints-with-azure-event-hubs"></a>Koncové body služeb virtuální sítě pomocí Azure Event Hubs

Integrace Event Hubs s [koncovými body služby Virtual Network (VNET)][vnet-sep] umožňuje zabezpečenému přístupu k funkcím zasílání zpráv z úloh, jako jsou virtuální počítače, které jsou svázané s virtuálními sítěmi, a cestu síťového provozu, která je zabezpečená na obou koncích.

Po navázání vazby na alespoň jeden koncový bod služby virtuální sítě musí příslušný obor názvů Event Hubs nadále přijímat provoz z libovolného místa, ale z nich budou ověřeny podsítě ve virtuálních sítích. Z pohledu virtuální sítě nakonfiguruje vazby obor názvů služby Event Hubs do koncového bodu služby izolované sítě tunelové propojení z podsítě virtuální sítě ke službě zasílání zpráv. 

Výsledkem je privátní a izolované relaci mezi úlohami, které jsou vázány na podsíť a odpovídající obor názvů Event Hubs, přestože pozorovatelných síťovou adresu na zasílání zpráv služby koncového bodu, který v rozsahu veřejných IP. K tomuto chování existuje výjimka. Povolení koncového bodu služby ve výchozím nastavení povolí pravidlo denyall v bráně firewall protokolu IP přidružené k virtuální síti. Můžete přidat konkrétní IP adresy v bráně firewall protokolu IP a povolit tak přístup ke veřejnému koncovému bodu centra událostí. 


>[!WARNING]
> Implementace integrace virtuálních sítí může ostatním službám Azure zabránit v interakci s Event Hubs.
>
> Důvěryhodné služby společnosti Microsoft nejsou podporovány, pokud jsou implementovány virtuální sítě.
>
> Běžné scénáře Azure, které nefungují s virtuálními sítěmi (Všimněte si, že seznam **není vyčerpávající)** –
> - Integrace s Azure Monitor. Diagnostické protokoly nelze z **jiných** služeb Azure streamovat do Event Hubs. Diagnostické protokoly Azure ale můžete povolit na samotném centru událostí. Je to stejný případ, když máte zapnutou bránu firewall (filtrování IP).
> - Azure Stream Analytics
> - Integrace s Azure Event Grid
> - Trasy k Azure IoT Hub
> - Device Explorer Azure IoT
>
> Níže uvedené služby společnosti Microsoft musí být ve virtuální síti.
> - Azure Web Apps
> - Funkce Azure

> [!IMPORTANT]
> Virtuální sítě jsou podporovány v **standardní** a **vyhrazené** úrovně služby Event Hubs. Není podporována v úrovni basic.

## <a name="advanced-security-scenarios-enabled-by-vnet-integration"></a>Pokročilé zabezpečení scénáře povolené ve integrace virtuální sítě 

Řešení, která vyžadují těsné a compartmentalized zabezpečení a kde podsítě virtuální sítě poskytují segmentaci mezi službami compartmentalized, stále potřebují komunikační cesty mezi službami, které se nacházejí v těchto oddílech.

Žádné okamžité IP trasy mezi oddíly, včetně těch, které může přenos HTTPS přes protokol TCP/IP, přináší riziko zneužití slabých míst z síťové vrstvy v provozu. Zasílání zpráv služby poskytují zcela izolované komunikační cesty, kde se zprávy i zapisují na disk po jejich přechod mezi stranami. Úlohy ve dvou různých virtuálních sítích, které jsou vázány na stejnou instanci služby Event Hubs může komunikovat efektivně a spolehlivě prostřednictvím zprávy, zatímco je zajištěná integrita hranice izolace příslušné síti.
 
To znamená, že zabezpečení citlivých Cloudová řešení nejen získat přístup k Azure špičkové spolehlivou a škálovatelnou asynchronní možnosti zasílání zpráv, ale můžete nyní použít zasílání zpráv a vytvořit komunikační trasy mezi zabezpečené řešení, která compartments jsou ze své podstaty bezpečnější než s libovolném jiném režimu komunikace peer-to-peer, včetně protokolů HTTPS a jiné protokoly TLS zabezpečené soketu.

## <a name="bind-event-hubs-to-virtual-networks"></a>Vytvoření vazby služby Event Hubs k virtuálním sítím

*Pravidla virtuální sítě* jsou funkce zabezpečení brány firewall, která určuje, zda váš obor názvů Azure Event Hubs akceptuje připojení z konkrétní virtuální síť podsíť.

Obor názvů služby Event Hubs vazba k virtuální síti je dvoustupňový proces. Nejprve musíte vytvořit **koncový bod služby Virtual Network** v podsíti Virtual Network a povolit ho pro "Microsoft. EventHub", jak je vysvětleno v tématu [Přehled koncového bodu služby][vnet-sep]. Po přidání koncového bodu služby, můžete vytvořit vazbu oboru názvů Event Hubs přes *pravidlo virtuální sítě*.

Pravidlo virtuální sítě je přidružení oboru názvů Event Hubs k podsíti virtuální sítě. Přestože existuje pravidlo, všechny úlohy, které jsou vázány na podsíť je udělen přístup k oboru názvů Event Hubs. Event Hubs samostatně nikdy vytvoří odchozí připojení, není potřeba získat přístup a je proto nikdy udělen přístup k vaší podsítě tím, že toto pravidlo.

### <a name="create-a-virtual-network-rule-with-azure-resource-manager-templates"></a>Vytvořit pravidlo virtuální sítě pomocí šablon Azure Resource Manageru

Následující šablony Resource Manageru umožňuje do existujícího oboru názvů služby Event Hubs přidáte pravidlo virtuální sítě.

Parametry šablony:

* **namespaceName**: obor názvů Event Hubs.
* **vnetRuleName**: název pravidla virtuální sítě má být vytvořen.
* **virtualNetworkingSubnetId**: plně kvalifikovanou cestu Resource Manageru pro podsíť virtuální sítě, například `/subscriptions/{id}/resourceGroups/{rg}/providers/Microsoft.Network/virtualNetworks/{vnet}/subnets/default` pro výchozí podsíť virtuální sítě.

> [!NOTE]
> I když nejsou možná žádná pravidla odepření, má šablona Azure Resource Manager výchozí akci nastavenou na **Povolit** , což neomezuje připojení.
> Při vytváření pravidel pro Virtual Network nebo brány firewall je nutné změnit ***"defaultAction"*** .
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

Pokud chcete nasadit šablonu, postupujte podle pokynů [Azure Resource Manager][lnk-deploy].

## <a name="next-steps"></a>Další kroky

Další informace o virtuálních sítích najdete v následujících tématech:

- [Koncové body služby virtuální sítě Azure][vnet-sep]
- [Filtrování IP Event Hubs Azure][ip-filtering]

[vnet-sep]: ../virtual-network/virtual-network-service-endpoints-overview.md
[lnk-deploy]: ../azure-resource-manager/templates/deploy-powershell.md
[ip-filtering]: event-hubs-ip-filtering.md
