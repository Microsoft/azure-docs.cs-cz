---
title: Azure Service Bus IP připojení filtry | Dokumentace Microsoftu
description: Použití filtru bloku připojení z konkrétní IP adresy IP adresy služby Azure Service Bus.
services: service-bus
documentationcenter: ''
author: clemensv
manager: timlt
ms.service: service-bus
ms.devlang: na
ms.topic: article
ms.date: 09/26/2018
ms.author: clemensv
ms.openlocfilehash: c6e9eef762d4a9eb95685d94c61ce10d499bb155
ms.sourcegitcommit: 55952b90dc3935a8ea8baeaae9692dbb9bedb47f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2018
ms.locfileid: "48884799"
---
# <a name="use-ip-filters"></a>Pomocí filtrů IP adres

Pro scénáře, ve kterých Azure Service Bus je přístupný jenom z některé známé servery *filtr IP* funkce umožňuje konfigurovat pravidla pro odmítnutí nebo přijímá přenos dat pocházejících z konkrétní adresy IPv4. Tyto adresy může být například těch, které podnikové bráně překladu adres.

## <a name="when-to-use"></a>Kdy je použít

Existují dva případy konkrétní použití, ve kterých je užitečné pro blokování koncové body služby Service Bus pro určité IP adresy:

- Service Bus, by měla přijímat provoz pouze ze zadaného rozsahu IP adres a odmítnout všechno ostatní. Například, že používáte služby Service Bus pomocí [Azure Express Route] [ express-route] můžete vytvářet privátní připojení k vaší místní infrastruktuře.
- Budete muset odmítnout provoz z IP adresy, které byly identifikovány jako podezřelou Správce služby Service Bus.

## <a name="how-filter-rules-are-applied"></a>Jak se používají pravidla filtru

Pravidla filtru IP se použijí na úrovni oboru názvů služby Service Bus. Takže pravidla se vztahují na všechna připojení od klientů pomocí libovolného protokolu pro podporované.

Žádné pokus o připojení z IP adresy, která odpovídá předávaní pravidlo protokolu IP v oboru názvů služby Service Bus je odmítnuty jako neověřené. Odpověď není zmiňuje pravidlo protokolu IP.

## <a name="default-setting"></a>Výchozí nastavení

Ve výchozím nastavení **filtr IP** mřížky na portálu pro Service Bus je prázdný. Toto výchozí nastavení znamená, že váš obor názvů akceptuje připojení jakékoli IP adresy. Toto výchozí nastavení je ekvivalentní pravidlo, které přijímá tento rozsah IP adres 0.0.0.0/0.

## <a name="ip-filter-rule-evaluation"></a>Vyhodnocení pravidla filtru IP

Pravidla filtru IP jsou použity v zadaném pořadí a první pravidlo, které odpovídá IP adrese Určuje akci, přijmout nebo odmítnout.

Například pokud chcete adresy v rozsahu 70.37.104.0/24 přijmout nebo odmítnout všechno ostatní, by měla přijímat prvního pravidla v mřížce 70.37.104.0/24 rozsah adres. Další pravidlo by měl odmítnout všechny adresy pomocí rozsahu 0.0.0.0/0.

> [!NOTE]
> Odmítnutí IP adresy můžete zabránit komunikaci se Service Bus dalšími službami Azure (jako je Azure Stream Analytics, Azure Virtual Machines nebo Device Explorer na portálu).

### <a name="creating-a-virtual-network-rule-with-azure-resource-manager-templates"></a>Vytvoření pravidla virtuální sítě pomocí šablon Azure Resource Manageru

> ! [DŮLEŽITÉ] Virtuální sítě jsou podporovány pouze **premium** úroveň služby Service Bus.

Následující šablony Resource Manageru umožňuje do existujícího oboru názvů služby Service Bus přidáte pravidlo virtuální sítě.

Parametry šablony:

- **ipFilterRuleName** musí být jedinečný, velká a malá písmena, alfanumerický řetězec až 128 znaků.
- **ipFilterAction** je buď **odmítnout** nebo **přijmout** jako akci, která se použije pro pravidla filtru IP.
- **ipMask** je jedna IPv4 adresa nebo blok IP adres v zápisu CIDR. Například v CIDR notation 70.37.104.0/24 představuje 256 adresy IPv4 z 70.37.104.0 70.37.104.255 s 24 označující počet bitů významné předpona pro rozsah.

```json
{  
   "$schema":"http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
   "contentVersion":"1.0.0.0",
   "parameters":{     
          "namespaceName":{  
             "type":"string",
             "metadata":{  
                "description":"Name of the namespace"
             }
          },
          "ipFilterRuleName":{  
             "type":"string",
             "metadata":{  
                "description":"Name of the Authorization rule"
             }
          },
          "ipFilterAction":{  
             "type":"string",
             "allowedValues": ["Reject", "Accept"],
             "metadata":{  
                "description":"IP Filter Action"
             }
          },
          "IpMask":{  
             "type":"string",
             "metadata":{  
                "description":"IP Mask"
             }
          }
      },
    "resources": [
        {
            "apiVersion": "2018-01-01-preview",
            "name": "[concat(parameters('namespaceName'), '/', parameters('ipFilterRuleName'))]",
            "type": "Microsoft.ServiceBus/Namespaces/IPFilterRules",
            "properties": {
                "FilterName":"[parameters('ipFilterRuleName')]",
                "Action":"[parameters('ipFilterAction')]",              
                "IpMask": "[parameters('IpMask')]"
            }
        } 
    ]
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