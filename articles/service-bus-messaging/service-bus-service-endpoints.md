---
title: Koncové body služby virtuální sítě – Azure Service Bus
description: Přidejte koncový bod služby Microsoft. ServiceBus do virtuální sítě.
services: service-bus
documentationcenter: ''
author: axisc
editor: spelluru
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.date: 10/22/2018
ms.author: aschhab
ms.openlocfilehash: 99a705c3923821739ddc1dedd8f7c079dc534a1a
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/21/2019
ms.locfileid: "74277304"
---
# <a name="use-virtual-network-service-endpoints-with-azure-service-bus"></a>Použití koncových bodů služby Virtual Network s Azure Service Bus

Integrace Service Bus s [koncovými body služby Virtual Network (VNET)][vnet-sep] umožňuje zabezpečenému přístupu k funkcím zasílání zpráv z úloh, jako jsou virtuální počítače, které jsou svázané s virtuálními sítěmi, a cestu síťového provozu, která je zabezpečená na obou koncích.

Po navázání vazby na alespoň jeden koncový bod služby virtuální sítě nebude příslušný obor názvů Service Bus nadále přijímat přenosy z odkudkoli, ale z autorizovaných virtuálních sítí. Z perspektivy virtuální sítě naváže obor názvů Service Bus na koncový bod služby a nakonfiguruje izolovaný síťový tunel z podsítě virtuální sítě do služby zasílání zpráv.

Výsledkem je privátní a izolovaný vztah mezi úlohami vázanými na podsíť a odpovídajícím oborem názvů Service Bus, a to i přes pozorovatelnou síťovou adresu koncového bodu služby zasílání zpráv ve veřejném rozsahu IP adres.

>[!WARNING]
> Implementace integrace virtuálních sítí může ostatním službám Azure zabránit v interakci s Service Bus.
>
> Důvěryhodné služby společnosti Microsoft nejsou podporovány, pokud jsou implementovány virtuální sítě.
>
> Běžné scénáře Azure, které nefungují s virtuálními sítěmi (Všimněte si, že seznam **není vyčerpávající)** –
> - Azure Stream Analytics
> - Integrace s Azure Event Grid
> - Trasy k Azure IoT Hub
> - Device Explorer Azure IoT
>
> Níže uvedené služby společnosti Microsoft musí být ve virtuální síti.
> - Azure App Service
> - Azure Functions

> [!IMPORTANT]
> Virtuální sítě se podporují jenom v oborech názvů Service Bus [úrovně Premium](service-bus-premium-messaging.md) .

## <a name="enable-service-endpoints-with-service-bus"></a>Povolit koncové body služby s Service Bus

Důležitým aspektem při použití koncových bodů služby virtuální sítě s Service Bus je, že byste neměli povolit tyto koncové body v aplikacích, které přibývají obory názvů Service Bus úrovně Standard a Premium. Protože standardní vrstva nepodporuje virtuální sítě, koncový bod je omezený jenom na obory názvů úrovně Premium.

## <a name="advanced-security-scenarios-enabled-by-vnet-integration"></a>Pokročilé zabezpečení scénáře povolené ve integrace virtuální sítě 

Řešení, které vyžadují vysoké a zpřehlednění zabezpečení a kde podsítě virtuální sítě poskytuje segmentace mezi těmito službami compartmentalized obecně stále potřebovat komunikační trasy mezi službami, které se nacházejí v těchto oddílech.

Žádné okamžité IP trasy mezi oddíly, včetně těch, které může přenos HTTPS přes protokol TCP/IP, přináší riziko zneužití slabých míst z síťové vrstvy v provozu. Zasílání zpráv služby poskytují zcela izolované komunikační cesty, kde se zprávy i zapisují na disk po jejich přechod mezi stranami. Úlohy ve dvou různých virtuálních sítích, které jsou vázané na stejnou instanci Service Bus, mohou komunikovat efektivně a spolehlivě prostřednictvím zpráv, zatímco příslušná integrita hranice izolace sítě zůstane zachovaná.
 
To znamená, že vaše cloudová řešení citlivá na zabezpečení nezískají přístup k špičkovým spolehlivým a škálovatelným funkcím asynchronního zasílání zpráv v oboru Azure, ale teď můžou pomocí zasílání zpráv vytvořit cesty komunikace mezi oddíly zabezpečeného řešení, které jsou ze své podstaty bezpečnější než u libovolného režimu komunikace peer-to-peer, včetně protokolu HTTPS a dalších protokolů soketu zabezpečených pro TLS.

## <a name="binding-service-bus-to-virtual-networks"></a>Vytvoření vazby Service Bus k virtuálním sítím

*Pravidla virtuální sítě* jsou funkcí zabezpečení brány firewall, která určuje, jestli Server Azure Service Bus akceptuje připojení z konkrétní podsítě virtuální sítě.

Vytvoření vazby oboru názvů Service Bus k virtuální síti je proces se dvěma kroky. Nejprve musíte vytvořit **koncový bod služby Virtual Network** v podsíti Virtual Network a povolit ho pro "Microsoft. ServiceBus", jak je vysvětleno v tématu [Přehled koncového bodu služby][vnet-sep]. Po přidání koncového bodu služby navážete obor názvů Service Bus s *pravidlem virtuální sítě*.

Pravidlo virtuální sítě je přidružení oboru názvů Service Bus k podsíti virtuální sítě. I když toto pravidlo existuje, budou mít všechny úlohy vázané na podsíť přístup k oboru názvů Service Bus. Service Bus sám o sobě nenavazuje odchozí připojení, nemusí získat přístup, a proto nikdy neudělí přístup k podsíti tím, že toto pravidlo povolí.

### <a name="creating-a-virtual-network-rule-with-azure-resource-manager-templates"></a>Vytvoření pravidla virtuální sítě pomocí šablon Azure Resource Manager

Následující šablona Správce prostředků umožňuje přidání pravidla virtuální sítě do existujícího oboru názvů Service Bus.

Parametry šablony:

* **obor názvů**: Service Bus obor názvů.
* **virtualNetworkingSubnetId**: plně kvalifikovaná cesta správce prostředků pro podsíť virtuální sítě; například `/subscriptions/{id}/resourceGroups/{rg}/providers/Microsoft.Network/virtualNetworks/{vnet}/subnets/default` pro výchozí podsíť virtuální sítě.

> [!NOTE]
> I když nejsou možná žádná pravidla odepření, má šablona Azure Resource Manager výchozí akci nastavenou na **Povolit** , což neomezuje připojení.
> Při vytváření pravidel pro Virtual Network nebo brány firewall je nutné změnit ***"defaultAction"*** .
> 
> from
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

Pokud chcete nasadit šablonu, postupujte podle pokynů [Azure Resource Manager][lnk-deploy].

## <a name="next-steps"></a>Další kroky

Další informace o virtuálních sítích najdete v následujících tématech:

- [Koncové body služby virtuální sítě Azure][vnet-sep]
- [Azure Service Bus filtrování IP adres][ip-filtering]

[vnet-sep]: ../virtual-network/virtual-network-service-endpoints-overview.md
[lnk-deploy]: ../azure-resource-manager/resource-group-template-deploy.md
[ip-filtering]: service-bus-ip-filtering.md
