---
title: Pravidla brány firewall pro Azure Event Hubs | Microsoft Docs
description: Pomocí pravidel brány firewall povolte připojení z konkrétních IP adres do Azure Event Hubs.
services: event-hubs
documentationcenter: ''
author: spelluru
manager: timlt
ms.service: event-hubs
ms.devlang: na
ms.custom: seodec18
ms.topic: article
ms.date: 12/20/2019
ms.author: spelluru
ms.openlocfilehash: a988fbb089bd94456e0b91b377574ab27a67617f
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/25/2019
ms.locfileid: "75437196"
---
# <a name="azure-event-hubs---use-firewall-rules"></a>Azure Event Hubs – použití pravidel brány firewall

Ve scénářích, ve kterých by měl být Azure Event Hubs dostupný jenom z určitých dobře známých lokalit, vám pravidla brány firewall umožní nakonfigurovat pravidla pro příjem provozu pocházejících z konkrétních IPv4 adres. Tyto adresy může být například těch, které podnikové bráně překladu adres.

## <a name="when-to-use"></a>When to use

Pokud chcete nastavit obor názvů Event Hubs tak, že by měl přijímat přenosy jenom z určeného rozsahu IP adres a odmítat všechno ostatní, můžete použít *pravidlo brány firewall* pro blokování koncových bodů centra událostí z jiných IP adres. Pokud například používáte Event Hubs s využitím [Azure Express Route][express-route], můžete vytvořit *pravidlo brány firewall* , které omezí provoz z vašich místních IP adres infrastruktury.

## <a name="how-filter-rules-are-applied"></a>Jak se používají pravidla filtru

Pravidla filtru IP se použijí na úrovni oboru názvů služby Event Hubs. Takže pravidla se vztahují na všechna připojení od klientů pomocí libovolného protokolu pro podporované.

Všechny pokusy o připojení z IP adresy, které neodpovídají povolenému pravidlu IP v oboru názvů Event Hubs, se odmítnou jako neoprávněné. Odpověď není zmiňuje pravidlo protokolu IP.

## <a name="default-setting"></a>Výchozí nastavení

Ve výchozím nastavení **filtr IP** mřížky na portálu pro službu Event Hubs je prázdný. Toto výchozí nastavení znamená, že vaše Centrum událostí přijme připojení z libovolné IP adresy. Toto výchozí nastavení je ekvivalentní pravidlo, které přijímá tento rozsah IP adres 0.0.0.0/0.

## <a name="ip-filter-rule-evaluation"></a>Vyhodnocení pravidla filtru IP

Pravidla filtru IP jsou použity v zadaném pořadí a první pravidlo, které odpovídá IP adrese Určuje akci, přijmout nebo odmítnout.

>[!WARNING]
> Implementace bran firewall může zabránit interakci s Event Hubs jinými službami Azure.
>
> Důvěryhodné služby společnosti Microsoft nejsou podporovány, pokud jsou implementovány filtrování IP adres (brány firewall) a budou brzy k dispozici.
>
> Běžné scénáře Azure, které nefungují s filtrováním IP adres (Všimněte si, že seznam **není vyčerpávající)** –
> - Azure Stream Analytics
> - Integrace s Azure Event Grid
> - Trasy k Azure IoT Hub
> - Device Explorer Azure IoT
>
> Níže uvedené služby společnosti Microsoft musí být ve virtuální síti.
> - Azure Web Apps
> - Funkce Azure

### <a name="creating-a-firewall-rule-with-azure-resource-manager-templates"></a>Vytvoření pravidla brány firewall pomocí Azure Resource Manager šablon

> [!IMPORTANT]
> Pravidla brány firewall jsou podporovaná na úrovni **Standard** a **vyhrazené** Event Hubs. Není podporována v úrovni basic.

Následující šablony Resource Manageru umožňuje přidání pravidlo filtru IP do existujícího oboru názvů služby Event Hubs.

Parametry šablony:

- **ipMask** je jedna IPv4 adresa nebo blok IP adres v zápisu CIDR. Například v CIDR notation 70.37.104.0/24 představuje 256 adresy IPv4 z 70.37.104.0 70.37.104.255 s 24 označující počet bitů významné předpona pro rozsah.

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
      "location": {
        "type": "string",
        "metadata": {
          "description": "Location for Namespace"
        }
      }
    },
    "variables": {
      "namespaceNetworkRuleSetName": "[concat(parameters('eventhubNamespaceName'), concat('/', 'default'))]",
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
        "apiVersion": "2018-01-01-preview",
        "name": "[variables('namespaceNetworkRuleSetName')]",
        "type": "Microsoft.EventHub/namespaces/networkruleset",
        "dependsOn": [
          "[concat('Microsoft.EventHub/namespaces/', parameters('eventhubNamespaceName'))]"
        ],
        "properties": {
          "virtualNetworkRules": [<YOUR EXISTING VIRTUAL NETWORK RULES>],
          "ipRules": 
          [
            {
                "ipMask":"10.1.1.1",
                "action":"Allow"
            },
            {
                "ipMask":"11.0.0.0/24",
                "action":"Allow"
            }
          ],
          "defaultAction": "Deny"
        }
      }
    ],
    "outputs": { }
  }
```

Pokud chcete nasadit šablonu, postupujte podle pokynů [Azure Resource Manager][lnk-deploy].

## <a name="next-steps"></a>Další kroky

Omezující přístup do služby Event Hubs k virtuálním sítím Azure naleznete na následující odkaz:

- [Virtual Network koncové body služby pro Event Hubs][lnk-vnet]

<!-- Links -->

[express-route]:  /azure/expressroute/expressroute-faqs#supported-services
[lnk-deploy]: ../azure-resource-manager/resource-group-template-deploy.md
[lnk-vnet]: event-hubs-service-endpoints.md
