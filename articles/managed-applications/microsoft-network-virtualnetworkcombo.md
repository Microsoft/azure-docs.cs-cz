---
title: Azure prvek uživatelského rozhraní VirtualNetworkCombo | Dokumentace Microsoftu
description: Popisuje element Microsoft.Network.VirtualNetworkCombo uživatelského rozhraní pro Azure portal.
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
ms.openlocfilehash: b0437338b403ff19761173d08be3938d07f13f55
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60252516"
---
# <a name="microsoftnetworkvirtualnetworkcombo-ui-element"></a>Microsoft.Network.VirtualNetworkCombo UI element
Skupina ovládacích prvků pro výběr nové nebo existující virtuální sítě.

## <a name="ui-sample"></a>Ukázka uživatelského rozhraní
Když uživatel vybere nové virtuální sítě, může uživatel přizpůsobit název každé podsítě a předponu adresy. Konfigurace podsítě je volitelné.

![Microsoft.Network.VirtualNetworkCombo new](./media/managed-application-elements/microsoft.network.virtualnetworkcombo-new.png)

Když uživatel vybere existující virtuální síť, uživatel musí být namapovaný každou podsíť, kterou vyžaduje nasazení šablony do existující podsítě. Konfigurace podsítě v tomto případě je povinný.

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

## <a name="remarks"></a>Poznámky
- Je-li zadána, první překrývat Předpona velikosti adresy `defaultValue.addressPrefixSize` je určen automaticky v závislosti na existující virtuální sítě v předplatném uživatele.
- Výchozí hodnota pro `defaultValue.name` a `defaultValue.addressPrefixSize` je **null**.
- `constraints.minAddressPrefixSize` je třeba zadat. Všechny existující virtuální sítě s adresním prostorem menší než zadaná hodnota je k dispozici pro výběr.
- `subnets` musí být zadána, a `constraints.minAddressPrefixSize` musí být zadán pro každou podsíť.
- Při vytváření nové virtuální sítě, předpona adresy každé podsíti se vypočítá automaticky podle předponu adresy virtuální sítě a funkcím `addressPrefixSize`.
- Při použití existující virtuální sítě, podsítě menší než příslušné `constraints.minAddressPrefixSize` jsou k dispozici pro výběr. Kromě toho pokud zadán, podsítě, které nemají alespoň `minAddressCount` jsou k dispozici pro výběr dostupných adres. Výchozí hodnota je **0**. Ujistěte se, že jsou dostupné adresy souvislé, zadejte **true** pro `requireContiguousAddresses`. Výchozí hodnota je **true**.
- Vytvoření podsítě v existující virtuální sítě se nepodporuje.
- Pokud `options.hideExisting` je **true**, uživatel nemůže vybrat stávající virtuální síť. Výchozí hodnota je **false**.

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
* Úvod do vytváření definic uživatelského rozhraní, naleznete v tématu [Začínáme s funkcí CreateUiDefinition](create-uidefinition-overview.md).
* Popis společné vlastnosti v prvcích uživatelského rozhraní, naleznete v tématu [CreateUiDefinition prvky](create-uidefinition-elements.md).
