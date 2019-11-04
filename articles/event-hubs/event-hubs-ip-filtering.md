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
ms.date: 12/06/2018
ms.author: spelluru
ms.openlocfilehash: f96c25dbb85ed92141636487f10d861a8c5e5f28
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2019
ms.locfileid: "73468436"
---
# <a name="use-firewall-rules"></a>Použít pravidla brány firewall

Ve scénářích, ve kterých by měl být Azure Event Hubs dostupný jenom z určitých dobře známých lokalit, vám pravidla brány firewall umožní nakonfigurovat pravidla pro příjem provozu pocházejících z konkrétních IPv4 adres. Například tyto adresy můžou být firemní bránou NAT.

## <a name="when-to-use"></a>When to use

Pokud chcete nastavit obor názvů Event Hubs tak, že by měl přijímat přenosy jenom z určeného rozsahu IP adres a odmítat všechno ostatní, můžete použít *pravidlo brány firewall* pro blokování koncových bodů centra událostí z jiných IP adres. Pokud například používáte Event Hubs s využitím [Azure Express Route][express-route], můžete vytvořit *pravidlo brány firewall* , které omezí provoz z vašich místních IP adres infrastruktury.

## <a name="how-filter-rules-are-applied"></a>Jak se používají pravidla filtru

Pravidla filtru IP jsou použita na úrovni oboru názvů Event Hubs. Proto se pravidla vztahují na všechna připojení z klientů pomocí libovolného podporovaného protokolu.

Všechny pokusy o připojení z IP adresy, které neodpovídají povolenému pravidlu IP v oboru názvů Event Hubs, se odmítnou jako neoprávněné. Odpověď nezmiňuje pravidlo protokolu IP.

## <a name="default-setting"></a>Výchozí nastavení

Ve výchozím nastavení je mřížka **filtru IP** na portálu pro Event Hubs prázdná. Toto výchozí nastavení znamená, že vaše centrum událostí akceptuje připojení z libovolné IP adresy. Toto výchozí nastavení odpovídá pravidlu, které přijímá rozsah IP adres 0.0.0.0/0.

## <a name="ip-filter-rule-evaluation"></a>Vyhodnocení pravidla filtru IP

Pravidla filtru IP se aplikují v pořadí a první pravidlo, které odpovídá IP adrese, určuje akci přijmout nebo odmítnout.

>[!WARNING]
> Implementace bran firewall může zabránit interakci s Event Hubs jinými službami Azure.
>
> Důvěryhodné služby společnosti Microsoft nejsou podporovány, pokud jsou implementovány filtrování IP adres (brány firewall) a budou brzy k dispozici.
>
> Běžné scénáře Azure, které nefungují s filtrováním IP adres (Všimněte si, že seznam **není vyčerpávající)** –
> - Azure Monitor
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
> Pravidla brány firewall jsou podporovaná na úrovni **Standard** a **vyhrazené** Event Hubs. Na úrovni Basic se nepodporuje.

Následující šablona Správce prostředků umožňuje přidat pravidlo filtru IP do existujícího oboru názvů Event Hubs.

Parametry šablony:

- **ipMask** je jedna adresa IPv4 nebo blok IP adres v zápisu CIDR. Například v zápisu CIDR 70.37.104.0/24 představuje 256 IPv4 adres z 70.37.104.0 do 70.37.104.255, přičemž 24 značí počet významných bitů předpony pro daný rozsah.

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

Informace o omezení přístupu k Event Hubs k virtuálním sítím Azure najdete na následujícím odkazu:

- [Virtual Network koncové body služby pro Event Hubs][lnk-vnet]

<!-- Links -->

[express-route]:  /azure/expressroute/expressroute-faqs#supported-services
[lnk-deploy]: ../azure-resource-manager/resource-group-template-deploy.md
[lnk-vnet]: event-hubs-service-endpoints.md
