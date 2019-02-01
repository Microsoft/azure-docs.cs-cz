---
title: Pravidla brány Firewall služby Azure Event Hubs | Dokumentace Microsoftu
description: Použijte pravidla brány Firewall pro povolení připojení z konkrétní IP adresy do služby Azure Event Hubs.
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
ms.openlocfilehash: ccb2fa7b0805b332957513c52c0c1051d068d2cc
ms.sourcegitcommit: fea5a47f2fee25f35612ddd583e955c3e8430a95
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/31/2019
ms.locfileid: "55507531"
---
# <a name="use-firewall-rules"></a>Pomocí pravidel brány Firewall

Pro scénáře, ve kterých Azure Event Hubs by měly být pouze přístupné z některé známé servery pravidla brány firewall umožňují nakonfigurovat pravidla pro příjem dat pocházejících z konkrétní adresy IPv4. Tyto adresy může být například těch, které podnikové bráně překladu adres.

## <a name="when-to-use"></a>Kdy je použít

Pokud chcete nastavit váš obor názvů služby Event Hubs, které by měla přijímat provoz ze zadaného rozsahu IP adres a odmítnout všechno ostatní, pak můžete využít *pravidlo brány Firewall* blokování koncové body centra událostí z ostatní IP adresy. Například, pokud používáte Event Hubs s využitím [Azure Express Route][express-route], můžete vytvořit *pravidlo brány Firewall* omezit přenosy z IP adresy vaší místní infrastruktury adresy.

## <a name="how-filter-rules-are-applied"></a>Jak se používají pravidla filtru

Pravidla filtru IP se použijí na úrovni oboru názvů služby Event Hubs. Takže pravidla se vztahují na všechna připojení od klientů pomocí libovolného protokolu pro podporované.

Jakékoli pokusy o připojení z IP adresy, která se neshoduje s povolenou pravidlo protokolu IP v centrech událostí oboru názvů je byl odmítnut jako neoprávněný přístup. Odpověď není zmiňuje pravidlo protokolu IP.

## <a name="default-setting"></a>Výchozí nastavení

Ve výchozím nastavení **filtr IP** mřížky na portálu pro službu Event Hubs je prázdný. Toto výchozí nastavení znamená, že vaše Centrum událostí přijme připojení z libovolné IP adresy. Toto výchozí nastavení je ekvivalentní pravidlo, které přijímá tento rozsah IP adres 0.0.0.0/0.

## <a name="ip-filter-rule-evaluation"></a>Vyhodnocení pravidla filtru IP

Pravidla filtru IP jsou použity v zadaném pořadí a první pravidlo, které odpovídá IP adrese Určuje akci, přijmout nebo odmítnout.

>[!WARNING]
> Implementace brány firewall může zabránit interakci s centry událostí dalšími službami Azure.
>
> Důvěryhodné služby Microsoftu nejsou podporovány při filtrování protokolu IP (brány firewall) jsou implementovány a budou brzy dostupné.
>
> Běžné scénáře služby Azure, které nefungují s filtrování protokolu IP (Všimněte si, že je seznam **není** vyčerpávající)-
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

### <a name="creating-a-firewall-rule-with-azure-resource-manager-templates"></a>Vytvoření pravidla brány Firewall pomocí šablon Azure Resource Manageru

> [!IMPORTANT]
> Pravidla brány firewall jsou podporovány v **standardní** a **vyhrazené** úrovně služby Event Hubs. Není podporována v úrovni basic.

Následující šablony Resource Manageru umožňuje přidání pravidlo filtru IP do existujícího oboru názvů služby Event Hubs.

Parametry šablony:

- **ipMask** je jedna IPv4 adresa nebo blok IP adres v zápisu CIDR. Například v CIDR notation 70.37.104.0/24 představuje 256 adresy IPv4 z 70.37.104.0 70.37.104.255 s 24 označující počet bitů významné předpona pro rozsah.

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

Pokud chcete nasadit šablonu, postupujte podle pokynů pro [Azure Resource Manageru][lnk-deploy].

## <a name="next-steps"></a>Další postup

Omezující přístup do služby Event Hubs k virtuálním sítím Azure naleznete na následující odkaz:

- [Koncové body služeb virtuální sítě pro službu Event Hubs][lnk-vnet]

<!-- Links -->

[express-route]:  /azure/expressroute/expressroute-faqs#supported-services
[lnk-deploy]: ../azure-resource-manager/resource-group-template-deploy.md
[lnk-vnet]: event-hubs-service-endpoints.md
