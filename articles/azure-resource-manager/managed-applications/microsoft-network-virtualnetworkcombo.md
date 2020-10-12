---
title: VirtualNetworkCombo – element uživatelského rozhraní
description: Popisuje prvek uživatelského rozhraní Microsoft. Network. VirtualNetworkCombo pro Azure Portal.
author: tfitzmac
ms.topic: conceptual
ms.date: 06/28/2018
ms.author: tomfitz
ms.openlocfilehash: 711f5293b205c1f500c6d9e08154342285ef959b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87033192"
---
# <a name="microsoftnetworkvirtualnetworkcombo-ui-element"></a>Microsoft. Network. VirtualNetworkCombo – element uživatelského rozhraní

Skupina ovládacích prvků pro výběr nové nebo existující virtuální sítě.

## <a name="ui-sample"></a>Ukázka uživatelského rozhraní

Když uživatel vybere novou virtuální síť, uživatel může přizpůsobit název a předponu adresy každé podsítě. Konfigurace podsítí je volitelná.

![Microsoft. Network. VirtualNetworkCombo New](./media/managed-application-elements/microsoft-network-virtualnetworkcombo-new.png)

Když uživatel vybere existující virtuální síť, musí namapovat každou podsíť, kterou šablona nasazení vyžaduje pro existující podsíť. Konfigurace podsítí v tomto případě je povinná.

![Microsoft. Network. VirtualNetworkCombo – existující](./media/managed-application-elements/microsoft-network-virtualnetworkcombo-existing.png)

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
- `constraints.minAddressPrefixSize` je nutné zadat. Všechny existující virtuální sítě s adresním prostorem menším než zadaná hodnota nejsou k dispozici pro výběr.
- `subnets` musí se zadat a `constraints.minAddressPrefixSize` musí se zadat pro každou podsíť.
- Při vytváření nové virtuální sítě se předpona adresy každé podsítě počítá automaticky na základě předpony adresy virtuální sítě a příslušné `addressPrefixSize` .
- Při použití existující virtuální sítě `constraints.minAddressPrefixSize` nejsou pro výběr k dispozici žádné podsítě, které jsou menší než příslušné. Pokud je tato možnost zadána, `minAddressCount` nebudou pro výběr k dispozici podsítě, které nemají alespoň dostupné adresy. Výchozí hodnota je **0**. Chcete-li zajistit, aby byly dostupné adresy souvislé, zadejte **hodnotu true** pro `requireContiguousAddresses` . Výchozí hodnota je **true (pravda**).
- Vytváření podsítí v existující virtuální síti se nepodporuje.
- Pokud `options.hideExisting` má **hodnotu true**, uživatel nemůže zvolit existující virtuální síť. Výchozí hodnota je **false** (nepravda).

## <a name="next-steps"></a>Další kroky

* Úvod k vytváření definic uživatelského rozhraní najdete v tématu [Začínáme s CreateUiDefinition](create-uidefinition-overview.md).
* Popis běžných vlastností v prvcích uživatelského rozhraní naleznete v tématu [CreateUiDefinition Elements](create-uidefinition-elements.md).
