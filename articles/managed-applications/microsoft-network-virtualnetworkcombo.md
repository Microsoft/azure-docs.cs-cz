---
title: Azure elementu VirtualNetworkCombo uživatelského rozhraní | Microsoft Docs
description: Popisuje element Microsoft.Network.VirtualNetworkCombo uživatelského rozhraní pro portál Azure.
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
ms.openlocfilehash: 2c2553d9ffb1dfbe032385fb77e234a8b96cb239
ms.sourcegitcommit: 5a7f13ac706264a45538f6baeb8cf8f30c662f8f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/29/2018
ms.locfileid: "37110061"
---
# <a name="microsoftnetworkvirtualnetworkcombo-ui-element"></a>Microsoft.Network.VirtualNetworkCombo UI element
Skupina ovládacích prvků pro výběr nový nebo existující virtuální síť.

## <a name="ui-sample"></a>Ukázka uživatelského rozhraní
Když uživatel vybere nové virtuální sítě, uživatel můžete přizpůsobit název každé podsítě a předponu adresy. Konfigurace podsítí je volitelné.

![Microsoft.Network.VirtualNetworkCombo nový](./media/managed-application-elements/microsoft.network.virtualnetworkcombo-new.png)

Když uživatel vybere existující virtuální síť, musí uživatel mapovat každou podsíť, kterou vyžaduje nasazení šablony do existující podsítí. Podsítě v takovém případě se vyžaduje konfigurace.

![Existující Microsoft.Network.VirtualNetworkCombo](./media/managed-application-elements/microsoft.network.virtualnetworkcombo-existing.png)

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

## <a name="remarks"></a>Poznámky
- -Li zadána, první nepřekrývají adres předpony velikosti `defaultValue.addressPrefixSize` je určen automaticky v závislosti na existující virtuální sítě v rámci předplatného uživatele.
- Výchozí hodnota pro `defaultValue.name` a `defaultValue.addressPrefixSize` je **null**.
- `constraints.minAddressPrefixSize` musí být zadán. Žádné existující virtuální sítě s adresním prostorem menší než zadaná hodnota jsou k dispozici pro výběr.
- `subnets` musí být zadán, a `constraints.minAddressPrefixSize` pro každou podsíť musí být zadána.
- Při vytváření nové virtuální sítě, předpona adresy každou podsíť je vypočtena automaticky na základě předponu adresy virtuální sítě a příslušné `addressPrefixSize`.
- Při použití existující virtuální sítě, podsítě, menší než příslušných `constraints.minAddressPrefixSize` jsou k dispozici pro výběr. Kromě toho-li zadána, podsítě, které nemají alespoň `minAddressCount` dostupné adresy jsou k dispozici pro výběr. Výchozí hodnota je **0**. K zajištění, že jsou k dispozici adresy souvislé, zadejte **true** pro `requireContiguousAddresses`. Výchozí hodnota je **true**.
- Vytvoření podsítě v existující virtuální síť se nepodporuje.
- Pokud `options.hideExisting` je **true**, uživatel nemůže vybrat existující virtuální síť. Výchozí hodnota je **false**.

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

## <a name="next-steps"></a>Další postup
* Úvod do vytváření definic uživatelského rozhraní, najdete v části [Začínáme s CreateUiDefinition](create-uidefinition-overview.md).
* Popis společných vlastností v prvky uživatelského rozhraní najdete v tématu [CreateUiDefinition elementy](create-uidefinition-elements.md).
