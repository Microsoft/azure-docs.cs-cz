---
title: Koncové body služby Virtual Network – Azure Event Hubs | Microsoft Docs
description: Tento článek poskytuje informace o tom, jak přidat koncový bod služby Microsoft. EventHub do virtuální sítě.
services: event-hubs
documentationcenter: ''
author: ShubhaVijayasarathy
manager: timlt
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.date: 03/12/2019
ms.author: shvija
ms.openlocfilehash: 5a1b293d4d7f652c0cdd95226113ec3ce8f8222c
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2019
ms.locfileid: "73466138"
---
# <a name="use-virtual-network-service-endpoints-with-azure-event-hubs"></a>Použití koncových bodů služby Virtual Network s využitím Azure Event Hubs

Integrace Event Hubs s [koncovými body služby Virtual Network (VNET)][vnet-sep] umožňuje zabezpečenému přístupu k funkcím zasílání zpráv z úloh, jako jsou virtuální počítače, které jsou svázané s virtuálními sítěmi, a cestu síťového provozu, která je zabezpečená v obou. přípon.

Po navázání vazby na alespoň jeden koncový bod služby virtuální sítě musí příslušný obor názvů Event Hubs nadále přijímat provoz z libovolného místa, ale z nich budou ověřeny podsítě ve virtuálních sítích. Z perspektivy virtuální sítě naváže obor názvů Event Hubs na koncový bod služby a nakonfiguruje izolovaný síťový tunel z podsítě virtuální sítě do služby zasílání zpráv. 

Výsledkem je privátní a izolovaný vztah mezi úlohami vázanými na podsíť a odpovídajícím oborem názvů Event Hubs, a to i přes pozorovatelnou síťovou adresu koncového bodu služby zasílání zpráv ve veřejném rozsahu IP adres. K tomuto chování existuje výjimka. Povolení koncového bodu služby ve výchozím nastavení povolí pravidlo denyall v bráně firewall protokolu IP přidružené k virtuální síti. Můžete přidat konkrétní IP adresy v bráně firewall protokolu IP a povolit tak přístup ke veřejnému koncovému bodu centra událostí. 


>[!WARNING]
> Implementace integrace virtuálních sítí může ostatním službám Azure zabránit v interakci s Event Hubs.
>
> Důvěryhodné služby společnosti Microsoft nejsou podporovány, pokud jsou implementovány virtuální sítě.
>
> Běžné scénáře Azure, které nefungují s virtuálními sítěmi (Všimněte si, že seznam **není vyčerpávající)** –
> - Azure Monitor
> - Azure Stream Analytics
> - Integrace s Azure Event Grid
> - Trasy k Azure IoT Hub
> - Device Explorer Azure IoT
>
> Níže uvedené služby společnosti Microsoft musí být ve virtuální síti.
> - Azure Web Apps
> - Funkce Azure

> [!IMPORTANT]
> Virtuální sítě jsou podporovány ve **standardních** a **vyhrazených** úrovních Event Hubs. Na úrovni Basic se nepodporuje.

## <a name="advanced-security-scenarios-enabled-by-vnet-integration"></a>Pokročilé scénáře zabezpečení povolené integrací virtuální sítě 

Řešení, která vyžadují těsné a compartmentalized zabezpečení a kde podsítě virtuální sítě poskytují segmentaci mezi službami compartmentalized, stále potřebují komunikační cesty mezi službami, které se nacházejí v těchto oddílech.

Veškerá okamžitá trasa IP mezi oddíly, včetně těch, které přenáší HTTPS přes protokol TCP/IP, přináší riziko zneužití chyb zabezpečení z síťové vrstvy. Služby zasílání zpráv poskytují zcela izolované komunikační cesty, ve kterých se při přechodu mezi stranami na disk zapisují i zprávy. Úlohy ve dvou různých virtuálních sítích, které jsou vázané na stejnou instanci Event Hubs, mohou komunikovat efektivně a spolehlivě prostřednictvím zpráv, zatímco příslušná integrita hranice izolace sítě zůstane zachovaná.
 
To znamená, že vaše cloudová řešení citlivá na zabezpečení nezískají přístup k špičkovým spolehlivým a škálovatelným funkcím asynchronního zasílání zpráv v oboru Azure, ale teď můžou pomocí zasílání zpráv vytvořit cesty komunikace mezi oddíly zabezpečeného řešení, které jsou ze své podstaty bezpečnější než u libovolného režimu komunikace peer-to-peer, včetně protokolu HTTPS a dalších protokolů soketu zabezpečených pro TLS.

## <a name="bind-event-hubs-to-virtual-networks"></a>Vytvoření vazby Event Hubs k virtuálním sítím

*Pravidla virtuální sítě* jsou funkcí zabezpečení brány firewall, která určuje, jestli váš obor názvů Azure Event Hubs akceptuje připojení z konkrétní podsítě virtuální sítě.

Vytvoření vazby oboru názvů Event Hubs k virtuální síti je proces se dvěma kroky. Nejprve musíte vytvořit **koncový bod služby Virtual Network** v podsíti Virtual Network a povolit ho pro "Microsoft. EventHub", jak je vysvětleno v tématu [Přehled koncového bodu služby][vnet-sep]. Po přidání koncového bodu služby navážete obor názvů Event Hubs s *pravidlem virtuální sítě*.

Pravidlo virtuální sítě je přidružení oboru názvů Event Hubs k podsíti virtuální sítě. I když toto pravidlo existuje, budou mít všechny úlohy vázané na podsíť přístup k oboru názvů Event Hubs. Event Hubs sám o sobě nenavazuje odchozí připojení, nemusí získat přístup, a proto nikdy neudělí přístup k podsíti tím, že toto pravidlo povolí.

### <a name="create-a-virtual-network-rule-with-azure-resource-manager-templates"></a>Vytvoření pravidla virtuální sítě pomocí šablon Azure Resource Manager

Následující šablona Správce prostředků umožňuje přidání pravidla virtuální sítě do existujícího oboru názvů Event Hubs.

Parametry šablony:

* **obor názvů**: Event Hubs obor názvů.
* **vnetRuleName**: název pro pravidlo Virtual Network, které se má vytvořit.
* **virtualNetworkingSubnetId**: plně kvalifikovaná cesta správce prostředků pro podsíť virtuální sítě; například `/subscriptions/{id}/resourceGroups/{rg}/providers/Microsoft.Network/virtualNetworks/{vnet}/subnets/default` pro výchozí podsíť virtuální sítě.

> [!NOTE]
> I když nejsou možná žádná pravidla odepření, má šablona Azure Resource Manager výchozí akci nastavenou na **Povolit** , což neomezuje připojení.
> Při vytváření pravidel pro Virtual Network nebo brány firewall je nutné změnit ***"defaultAction"*** .
> 
> Výsledkem
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

Další informace o virtuálních sítích najdete v následujících odkazech:

- [Koncové body služby virtuální sítě Azure][vnet-sep]
- [Filtrování IP Event Hubs Azure][ip-filtering]

[vnet-sep]: ../virtual-network/virtual-network-service-endpoints-overview.md
[lnk-deploy]: ../azure-resource-manager/resource-group-template-deploy.md
[ip-filtering]: event-hubs-ip-filtering.md
