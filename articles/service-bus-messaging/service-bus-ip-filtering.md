---
title: Pravidla brány Firewall služby Azure Service Bus | Dokumentace Microsoftu
description: Jak používat pravidla brány Firewall pro povolení připojení z konkrétní IP adresy na služby Azure Service Bus.
services: service-bus
documentationcenter: ''
author: clemensv
manager: timlt
ms.service: service-bus
ms.devlang: na
ms.topic: article
ms.date: 09/26/2018
ms.author: clemensv
ms.openlocfilehash: f8771be9a96ae188a9610a1b19dfd6cbd49ba277
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/12/2018
ms.locfileid: "53270429"
---
# <a name="use-firewall-rules"></a>Pomocí pravidel brány Firewall

Pro scénáře, ve kterých Azure Service Bus je přístupný jenom z některé známé servery pravidla brány Firewall umožňují nakonfigurovat pravidla pro příjem dat pocházejících z konkrétní adresy IPv4. Tyto adresy může být například těch, které podnikové bráně překladu adres.

## <a name="when-to-use"></a>Kdy je použít

Pokud chcete k instalaci služby Service Bus, která by měla přijímat provoz pouze ze zadaného rozsahu IP adres a odmítnout všechno ostatní, a pak můžete využít *brány Firewall* blokování koncové body služby Service Bus z jiných IP adres. Například, že používáte služby Service Bus pomocí [Azure Express Route] [ express-route] můžete vytvářet privátní připojení k vaší místní infrastruktuře. 

## <a name="how-filter-rules-are-applied"></a>Jak se používají pravidla filtru

Pravidla filtru IP se použijí na úrovni oboru názvů služby Service Bus. Takže pravidla se vztahují na všechna připojení od klientů pomocí libovolného protokolu pro podporované.

Jakékoli pokusy o připojení z IP adresy, která se neshoduje s povolenou pravidlo protokolu IP v Service Bus, obor názvů byl odmítnut jako neoprávněný přístup. Odpověď není zmiňuje pravidlo protokolu IP.

## <a name="default-setting"></a>Výchozí nastavení

Ve výchozím nastavení **filtr IP** mřížky na portálu pro Service Bus je prázdný. Toto výchozí nastavení znamená, že váš obor názvů akceptuje připojení jakékoli IP adresy. Toto výchozí nastavení je ekvivalentní pravidlo, které přijímá tento rozsah IP adres 0.0.0.0/0.

## <a name="ip-filter-rule-evaluation"></a>Vyhodnocení pravidla filtru IP

Pravidla filtru IP jsou použity v zadaném pořadí a první pravidlo, které odpovídá IP adrese Určuje akci, přijmout nebo odmítnout.

>[!WARNING]
> Implementace pravidla brány Firewall může zabránit interakci se službou Service Bus dalšími službami Azure.
>
> Důvěryhodné služby Microsoftu nejsou podporovány při filtrování protokolu IP (pravidla brány Firewall) jsou implementovány a budou brzy dostupné.
>
> Běžné scénáře služby Azure, které nefungují s filtrování protokolu IP (Všimněte si, že je seznam **není** vyčerpávající)-
> - Azure Monitor
> - Azure Stream Analytics
> - Integrace s Azure Event Grid
> - Směruje Azure IoT Hub
> - Azure IoT Device Explorer
> - Průzkumník dat Azure
>
> Níže Microsoft services musí být ve virtuální síti
> - Azure Web Apps
> - Azure Functions

### <a name="creating-a-virtual-network-and-firewall-rule-with-azure-resource-manager-templates"></a>Vytváří se pravidlo virtuální sítě a brány firewall pomocí šablon Azure Resource Manageru

> [!IMPORTANT]
> Virtuální sítě jsou podporovány pouze **premium** úroveň služby Service Bus.

Následující šablony Resource Manageru umožňuje do existujícího oboru názvů služby Service Bus přidáte pravidlo virtuální sítě.

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
          "name": "Standard",
          "tier": "Standard"
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

Pokud chcete nasadit šablonu, postupujte podle pokynů pro [Azure Resource Manageru][lnk-deploy].

## <a name="next-steps"></a>Další postup

Omezující přístup k Service Bus k virtuálním sítím Azure naleznete na následující odkaz:

- [Koncové body služeb virtuální sítě pro Service Bus][lnk-vnet]

<!-- Links -->

[lnk-deploy]: ../azure-resource-manager/resource-group-template-deploy.md
[lnk-vnet]: service-bus-service-endpoints.md
[express-route]:  /azure/expressroute/expressroute-faqs#supported-services