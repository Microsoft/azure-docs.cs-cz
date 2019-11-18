---
title: Element uživatelského rozhraní Azure VirtualNetworkCombo | Microsoft Docs
description: Popisuje prvek uživatelského rozhraní Microsoft. Network. VirtualNetworkCombo pro Azure Portal.
services: managed-applications
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.service: managed-applications
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/28/2018
ms.author: tomfitz
ms.openlocfilehash: d325230f603be4ccfe4fe42f1b58c6ad892fdb2c
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/17/2019
ms.locfileid: "74151475"
---
# <a name="microsoftnetworkvirtualnetworkcombo-ui-element"></a>Microsoft.Network.VirtualNetworkCombo UI element

Skupina ovládacích prvků pro výběr nové nebo existující virtuální sítě.

## <a name="ui-sample"></a>Ukázka uživatelského rozhraní

Když uživatel vybere novou virtuální síť, uživatel může přizpůsobit název a předponu adresy každé podsítě. Konfigurace podsítí je volitelná.

![Microsoft. Network. VirtualNetworkCombo New](./media/managed-application-elements/microsoft.network.virtualnetworkcombo-new.png)

Když uživatel vybere existující virtuální síť, musí namapovat každou podsíť, kterou šablona nasazení vyžaduje pro existující podsíť. Konfigurace podsítí v tomto případě je povinná.

![Microsoft.Network.VirtualNetworkCombo existing](./media/managed-application-elements/microsoft.network.virtualnetworkcombo-existing.png)

## <a name="schema"></a>Schéma

```json
{
  "name": "element1",
  "type": "Microsoft.Network.VirtualNetworkCombo",
  "label": {
    "virtualNetwork": "Virtual network",
    "subnets": "Subnets"
  },
  "toolTip": {
    "virtualNetwork": "",
    "subnets": ""
  },
  "defaultValue": {
    "name": "vnet01",
    "addressPrefixSize": "/16"
  },
  "constraints": {
    "minAddressPrefixSize": "/16"
  },
  "options": {
    "hideExisting": false
  },
  "subnets": {
    "subnet1": {
      "label": "First subnet",
      "defaultValue": {
        "name": "subnet-1",
        "addressPrefixSize": "/24"
      },
      "constraints": {
        "minAddressPrefixSize": "/24",
        "minAddressCount": 12,
        "requireContiguousAddresses": true
      }
    },
    "subnet2": {
      "label": "Second subnet",
      "defaultValue": {
        "name": "subnet-2",
        "addressPrefixSize": "/26"
      },
      "constraints": {
        "minAddressPrefixSize": "/26",
        "minAddressCount": 8,
        "requireContiguousAddresses": true
      }
    }
  },
  "visible": true
}
```

## <a name="sample-output"></a>Ukázkový výstup

```json
{
  "name": "vnet01",
  "resourceGroup": "rg01",
  "addressPrefixes": ["10.0.0.0/16"],
  "newOrExisting": "new",
  "subnets": {
    "subnet1": {
      "name": "subnet-1",
      "addressPrefix": "10.0.0.0/24",
      "startAddress": "10.0.0.1"
    },
    "subnet2": {
      "name": "subnet-2",
      "addressPrefix": "10.0.1.0/26",
      "startAddress": "10.0.1.1"
    }
  }
}
```

## <a name="remarks"></a>Poznámky

- Je-li tento parametr zadán, je první překrývající se předpona adresy `defaultValue.addressPrefixSize` určena automaticky v závislosti na stávajících virtuálních sítích v rámci předplatného uživatele.
- Výchozí hodnota pro `defaultValue.name` a `defaultValue.addressPrefixSize` je **null**.
- je nutné zadat `constraints.minAddressPrefixSize`. Všechny existující virtuální sítě s adresním prostorem menším než zadaná hodnota nejsou k dispozici pro výběr.
- je nutné zadat `subnets` a pro každou podsíť musí být zadán `constraints.minAddressPrefixSize`.
- Při vytváření nové virtuální sítě se předpona adresy každé podsítě počítá automaticky na základě předpony adresy virtuální sítě a příslušné `addressPrefixSize`.
- Při použití existující virtuální sítě nebudou pro výběr k dispozici žádné podsítě menší než příslušné `constraints.minAddressPrefixSize`. Pokud je tato možnost zadána, nebudou pro výběr k dispozici podsítě, které nemají alespoň `minAddressCount` dostupné adresy. Výchozí hodnota je **0**. Pokud chcete zajistit, aby byly dostupné adresy souvislé, zadejte pro `requireContiguousAddresses`**hodnotu true** . Výchozí hodnota je **true (pravda**).
- Vytváření podsítí v existující virtuální síti se nepodporuje.
- Pokud má `options.hideExisting` **hodnotu true**, nemůže uživatel zvolit existující virtuální síť. Výchozí hodnota je **false**.

## <a name="next-steps"></a>Další kroky

* Úvod k vytváření definic uživatelského rozhraní najdete v tématu [Začínáme s CreateUiDefinition](create-uidefinition-overview.md).
* Popis běžných vlastností v prvcích uživatelského rozhraní naleznete v tématu [CreateUiDefinition Elements](create-uidefinition-elements.md).
