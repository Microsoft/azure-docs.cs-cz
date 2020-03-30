---
title: Prvek ui VirtualNetworkCombo
description: Popisuje prvek ui rozhraní Microsoft.Network.VirtualNetworkCombo pro portál Azure.
author: tfitzmac
ms.topic: conceptual
ms.date: 06/28/2018
ms.author: tomfitz
ms.openlocfilehash: 53c9653b44a6c9d26d49d37b351cf6000676e2d4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75651966"
---
# <a name="microsoftnetworkvirtualnetworkcombo-ui-element"></a>Prvek ui rozhraní Microsoft.Network.VirtualNetworkCombo

Skupina ovládacích prvků pro výběr nové nebo existující virtuální sítě.

## <a name="ui-sample"></a>Ukázka ui

Když uživatel vybere novou virtuální síť, může přizpůsobit název a předponu každé podsítě. Konfigurace podsítí je volitelná.

![Microsoft.Network.VirtualNetworkCombo nový](./media/managed-application-elements/microsoft.network.virtualnetworkcombo-new.png)

Když uživatel vybere existující virtuální síť, musí namapovat každou podsíť, kterou šablona nasazení vyžaduje, na existující podsíť. V tomto případě je nutná konfigurace podsítí.

![Microsoft.Network.VirtualNetworkCombo existující](./media/managed-application-elements/microsoft.network.virtualnetworkcombo-existing.png)

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

- Pokud je zadán, první nepřekrývající se `defaultValue.addressPrefixSize` adresa předpona velikosti je určena automaticky na základě existujících virtuálních sítí v předplatném uživatele.
- Výchozí hodnota `defaultValue.name` pro `defaultValue.addressPrefixSize` a má **hodnotu null**.
- `constraints.minAddressPrefixSize`musí být specifikována. Všechny existující virtuální sítě s adresním prostorem menším, než je zadaná hodnota, nejsou pro výběr k dispozici.
- `subnets`musí být zadána a `constraints.minAddressPrefixSize` musí být zadána pro každou podsíť.
- Při vytváření nové virtuální sítě se předpona adresy každé podsítě vypočítá automaticky na základě `addressPrefixSize`předpony adresy virtuální sítě a příslušného předčíslí .
- Při použití existující virtuální sítě, všechny podsítě `constraints.minAddressPrefixSize` menší než příslušné nejsou k dispozici pro výběr. Navíc, pokud je zadán, podsítě, které `minAddressCount` nemají alespoň dostupné adresy nejsou k dispozici pro výběr. Výchozí hodnota je **0**. Chcete-li zajistit, aby dostupné adresy byly souvislé, zadejte hodnotu **true** for `requireContiguousAddresses`. Výchozí hodnota je **true**.
- Vytváření podsítí v existující virtuální síti není podporováno.
- Pokud `options.hideExisting` je **true**, uživatel nemůže vybrat existující virtuální síť. Výchozí hodnota je **false** (nepravda).

## <a name="next-steps"></a>Další kroky

* Úvod k vytváření definic ui naleznete [v tématu Začínáme s CreateUiDefinition](create-uidefinition-overview.md).
* Popis běžných vlastností v prvcích uživatelského rozhraní naleznete v [tématu CreateUiDefinition elements](create-uidefinition-elements.md).
