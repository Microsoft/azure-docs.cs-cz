---
title: Azure Service Bus pravidla brány firewall | Microsoft Docs
description: Jak používat pravidla brány firewall k povolení Azure Service Bus Připojení z konkrétních IP adres.
services: service-bus
documentationcenter: ''
author: axisc
manager: timlt
editor: spelluru
ms.service: service-bus
ms.devlang: na
ms.topic: article
ms.date: 04/23/2019
ms.author: aschhab
ms.openlocfilehash: 45415af479c9581ee04b97af4fb5297d09c5769d
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2019
ms.locfileid: "73496342"
---
# <a name="use-firewall-rules"></a>Použít pravidla brány firewall

V případě scénářů, ve kterých je Azure Service Bus dostupné jenom z určitých dobře známých lokalit, pravidla brány firewall umožňují konfigurovat pravidla pro příjem provozu pocházejících z konkrétních IPv4 adres. Například tyto adresy můžou být firemní bránou NAT.

## <a name="when-to-use"></a>When to use

Pokud chcete nastavit Service Bus tak, že by měl přijímat přenosy jenom ze zadaného rozsahu IP adres, a zamítnout všechno ostatní, můžete *bránu firewall* použít k blokování Service Bus koncových bodů z jiných IP adres. Například používáte Service Bus se službou [Azure Express Route][express-route] k vytváření privátních připojení k místní infrastruktuře. 

## <a name="how-filter-rules-are-applied"></a>Jak se používají pravidla filtru

Pravidla filtru IP jsou použita na úrovni oboru názvů Service Bus. Proto se pravidla vztahují na všechna připojení z klientů pomocí libovolného podporovaného protokolu.

Všechny pokusy o připojení z IP adresy, které neodpovídají povolenému pravidlu IP v oboru názvů Service Bus, se odmítnou jako neoprávněné. Odpověď nezmiňuje pravidlo protokolu IP.

## <a name="default-setting"></a>Výchozí nastavení

Ve výchozím nastavení je mřížka **filtru IP** na portálu pro Service Bus prázdná. Toto výchozí nastavení znamená, že váš obor názvů přijímá připojení libovolné IP adresy. Toto výchozí nastavení odpovídá pravidlu, které přijímá rozsah IP adres 0.0.0.0/0.

## <a name="ip-filter-rule-evaluation"></a>Vyhodnocení pravidla filtru IP

Pravidla filtru IP se aplikují v pořadí a první pravidlo, které odpovídá IP adrese, určuje akci přijmout nebo odmítnout.

>[!WARNING]
> Implementace pravidel brány firewall může zabránit ostatním službám Azure v interakci s Service Bus.
>
> Důvěryhodné služby společnosti Microsoft nejsou podporovány, pokud je implementováno filtrování IP adres (pravidla brány firewall) a bude k dispozici brzy.
>
> Běžné scénáře Azure, které nefungují s filtrováním IP adres (Všimněte si, že seznam **není vyčerpávající)** –
> - Azure Monitor
> - Azure Stream Analytics
> - Integrace s Azure Event Grid
> - Trasy k Azure IoT Hub
> - Device Explorer Azure IoT
>
> Níže uvedené služby společnosti Microsoft musí být ve virtuální síti.
> - Azure App Service
> - Funkce Azure

### <a name="creating-a-virtual-network-and-firewall-rule-with-azure-resource-manager-templates"></a>Vytvoření virtuální sítě a pravidla brány firewall pomocí šablon Azure Resource Manager

> [!IMPORTANT]
> Brány firewall a virtuální sítě se podporují jenom v Service Bus úrovně **Premium** .

Následující šablona Správce prostředků umožňuje přidání pravidla virtuální sítě do existujícího oboru názvů Service Bus.

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
      "servicebusNamespaceName": {
        "type": "string",
        "metadata": {
          "description": "Name of the Service Bus namespace"
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
        "apiVersion": "2018-01-01-preview",
        "name": "[variables('namespaceNetworkRuleSetName')]",
        "type": "Microsoft.ServiceBus/namespaces/networkruleset",
        "dependsOn": [
          "[concat('Microsoft.ServiceBus/namespaces/', parameters('servicebusNamespaceName'))]"
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

Informace o omezení přístupu k Service Bus k virtuálním sítím Azure najdete na následujícím odkazu:

- [Virtual Network koncové body služby pro Service Bus][lnk-vnet]

<!-- Links -->

[lnk-deploy]: ../azure-resource-manager/resource-group-template-deploy.md
[lnk-vnet]: service-bus-service-endpoints.md
[express-route]:  /azure/expressroute/expressroute-faqs#supported-services
