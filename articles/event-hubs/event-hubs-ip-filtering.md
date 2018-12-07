---
title: Omezení přístupu k Azure Event Hubs pomocí filtrů IP adres | Dokumentace Microsoftu
description: Použijte IP adresu filtrování se můžete blokovat připojení z konkrétní IP adresy do služby Azure Event Hubs.
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
ms.openlocfilehash: 5ed952421186210e55d6065cee51f4d54b4c0d46
ms.sourcegitcommit: 698ba3e88adc357b8bd6178a7b2b1121cb8da797
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/07/2018
ms.locfileid: "53014391"
---
# <a name="restrict-access-to-azure-event-hubs-using-ip-filters"></a>Omezení přístupu k Azure Event Hubs pomocí filtrů IP adres
Pro scénáře, ve kterých Azure Event Hubs by měly být pouze přístupné z některé známé servery *filtr IP* funkce umožňuje konfigurovat pravidla pro odmítnutí nebo přijímá přenos dat pocházejících z konkrétní adresy IPv4. Tyto adresy může být například těch, které podnikové bráně překladu adres.

## <a name="when-to-use"></a>Kdy je použít

Použít dvě důležité případy, ve kterých je užitečné pro blokování služby Event Hubs pro určité IP adresy jsou následující:

- Vaše služby event hubs by měla přijímat provoz pouze ze zadaného rozsahu IP adres a odmítnout všechno ostatní. Například používáte Event Hubs s využitím [Azure Express Route] [ express-route] můžete vytvářet privátní připojení k vaší místní infrastruktuře. 
- Budete muset odmítnout provoz z IP adresy, které byly identifikovány jako podezřelou správcem služby Event Hubs.

## <a name="how-filter-rules-are-applied"></a>Jak se používají pravidla filtru

Pravidla filtru IP se použijí na úrovni oboru názvů služby Event Hubs. Takže pravidla se vztahují na všechna připojení od klientů pomocí libovolného protokolu pro podporované.

Žádné pokus o připojení z IP adresy, která odpovídá předávaní pravidlo protokolu IP v oboru názvů služby Event Hubs je odmítnuty jako neověřené. Odpověď není zmiňuje pravidlo protokolu IP.

## <a name="default-setting"></a>Výchozí nastavení

Ve výchozím nastavení **filtr IP** mřížky na portálu pro službu Event Hubs je prázdný. Toto výchozí nastavení znamená, že vaše Centrum událostí přijme připojení z libovolné IP adresy. Toto výchozí nastavení je ekvivalentní pravidlo, které přijímá tento rozsah IP adres 0.0.0.0/0.

## <a name="ip-filter-rule-evaluation"></a>Vyhodnocení pravidla filtru IP

Pravidla filtru IP jsou použity v zadaném pořadí a první pravidlo, které odpovídá IP adrese Určuje akci, přijmout nebo odmítnout.

Například pokud chcete adresy v rozsahu 70.37.104.0/24 přijmout nebo odmítnout všechno ostatní, by měla přijímat prvního pravidla v mřížce 70.37.104.0/24 rozsah adres. Další pravidlo by měl odmítnout všechny adresy pomocí rozsahu 0.0.0.0/0.

> [!NOTE]
> Odmítnutí IP adresy můžete zabránit interakci s centry událostí dalšími službami Azure (jako je Azure Stream Analytics, Azure Virtual Machines nebo Device Explorer na portálu).

### <a name="creating-an-ip-filter-rule-with-azure-resource-manager-templates"></a>Vytvoření pravidla filtru IP pomocí šablon Azure Resource Manageru

> [!IMPORTANT]
> Virtuální sítě jsou podporovány v **standardní** a **vyhrazené** úrovně služby Event Hubs. Není podporována v úrovni basic. 

Následující šablony Resource Manageru umožňuje přidání pravidlo filtru IP do existujícího oboru názvů služby Event Hubs.

Parametry šablony:

- **ipFilterRuleName** musí být jedinečný, velká a malá písmena, alfanumerický řetězec do 128 znaků.
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
            "type": "Microsoft.EventHub/Namespaces/IPFilterRules",
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

Omezující přístup do služby Event Hubs k virtuálním sítím Azure naleznete na následující odkaz:

- [Koncové body služeb virtuální sítě pro službu Event Hubs][lnk-vnet]

<!-- Links -->

[express-route]:  /azure/expressroute/expressroute-faqs#supported-services
[lnk-deploy]: ../azure-resource-manager/resource-group-template-deploy.md
[lnk-vnet]: event-hubs-service-endpoints.md