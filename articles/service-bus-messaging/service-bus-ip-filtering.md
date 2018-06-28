---
title: Filtry připojení Azure Service Bus IP | Microsoft Docs
description: Jak používat IP filtrování bloku připojení z konkrétní IP adresy k Azure Service Bus.
services: service-bus
documentationcenter: ''
author: clemensv
manager: timlt
ms.service: service-bus
ms.devlang: na
ms.topic: article
ms.date: 06/26/2018
ms.author: clemensv
ms.openlocfilehash: e009bb9120fafc6edf60b68fab3336b9d1add507
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/27/2018
ms.locfileid: "37035779"
---
# <a name="use-ip-filters"></a>Použití filtrů IP

Pro scénáře, ve kterých Azure Service Bus je k dispozici pouze z určitých známých webů *filtr IP* funkce umožňuje konfigurovat pravidla odmítat nebo přijímat přenosy pocházející z konkrétní adresy IPv4. Tyto adresy může být například těch, které podnikové brány NAT.

## <a name="when-to-use"></a>Kdy je použít

Existují dva případy konkrétní použití, ve kterých je užitečný pro blokování koncové body Service Bus pro určité IP adresy:

- Service Bus by měl přijímat přenosy pouze ze zadaného rozsahu IP adres a všem ostatním odmítnout. Například používáte Service Bus s [Azure Express trasy] [ express-route] vytvářet privátní připojení k místní infrastrukturu.
- Budete muset odmítnout provoz z IP adresy, které byly identifikovány jako podezřelou Správce služby Service Bus.

## <a name="how-filter-rules-are-applied"></a>Používání pravidel filtru

Pravidla filtr IP se používají na úrovni oboru názvů Service Bus. Proto pravidla použijí pro všechna připojení od klientů pomocí libovolný podporovaný protokol.

Všechny pokus o připojení z IP adresy, která odpovídá předávaní pravidlo IP v oboru názvů Service Bus je odmítnuta jako Neautorizováno. Odpověď není zmiňuje pravidlo IP.

## <a name="default-setting"></a>Výchozí nastavení

Ve výchozím nastavení **filtr IP** mřížky na portálu Service Bus je prázdný. Toto výchozí nastavení znamená, že váš obor názvů přijímá připojení jakékoli IP adresy. Toto výchozí nastavení je ekvivalentní pravidlo, které přijímá 0.0.0.0/0 rozsah IP adres.

## <a name="ip-filter-rule-evaluation"></a>Vyhodnocení pravidla filtru IP

Pravidla filtru IP jsou použity v zadaném pořadí a první pravidlo, které odpovídá IP adrese Určuje akci, přijměte nebo odmítněte.

Například pokud chcete přijmout adresy v rozsahu 70.37.104.0/24 a všem ostatním odmítnout, musí přijmout prvního pravidla v mřížce 70.37.104.0/24 rozsah adres. Další pravidlo by měl zamítnout všechny adresy pomocí 0.0.0.0/0 rozsahu.

> [!NOTE]
> Odmítat IP adresy můžete zabránit interakci s Service Bus jinými službami Azure (například Azure Stream Analytics, virtuální počítače Azure nebo Průzkumníka zařízení na portálu).

### <a name="creating-a-virtual-network-rule-with-azure-resource-manager-templates"></a>Vytvoření pravidla virtuální sítě pomocí šablony Azure Resource Manager

Následující šablony Resource Manager umožňuje přidat pravidlo virtuální sítě do existujícího oboru názvů Service Bus.

Parametry šablony:

- **ipFilterRuleName** musí být jedinečný, velká a malá písmena, alfanumerické řetězec až 128 znaků.
- **ipFilterAction** je buď **odmítnout** nebo **přijmout** jako akce, která platí pro pravidlo filtru IP.
- **ipMask** je jedna adresa IPv4 nebo blok IP adres v notaci CIDR. Například v CIDR zápis 70.37.104.0/24 představuje 256 adresy IPv4 z 70.37.104.0 70.37.104.255 s 24, která určuje počet bitů významné předponu pro rozsah.

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

Pokud chcete nasadit šablonu, postupujte podle pokynů pro [Azure Resource Manager][lnk-deploy].

## <a name="next-steps"></a>Další postup

Omezení přístupu k Service Bus do virtuálních sítí Azure najdete na následující odkaz:

- [Koncové body služby virtuální sítě pro Service Bus][lnk-vnet]

<!-- Links -->

[lnk-deploy]: ../azure-resource-manager/resource-group-template-deploy.md
[lnk-vnet]: service-bus-service-endpoints.md
[express-route]:  /azure/expressroute/expressroute-faqs#supported-services