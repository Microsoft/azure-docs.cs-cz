---
title: Azure elementu PublicIpAddressCombo uživatelského rozhraní | Microsoft Docs
description: Popisuje element Microsoft.Network.PublicIpAddressCombo uživatelského rozhraní pro portál Azure.
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
ms.openlocfilehash: d06a450595a53fdc65fba74791345abe3a1b3db4
ms.sourcegitcommit: 5a7f13ac706264a45538f6baeb8cf8f30c662f8f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/29/2018
ms.locfileid: "37109565"
---
# <a name="microsoftnetworkpublicipaddresscombo-ui-element"></a>Element Microsoft.Network.PublicIpAddressCombo uživatelského rozhraní
Skupina ovládacích prvků pro výběr nový nebo existující veřejnou IP adresu.

## <a name="ui-sample"></a>Ukázka uživatelského rozhraní
![Microsoft.Network.PublicIpAddressCombo](./media/managed-application-elements/microsoft.network.publicipaddresscombo.png)

- Pokud uživatel vybere 'None' pro veřejnou IP adresu, název domény popisek textového pole Skrytá.
- Pokud uživatel vybere stávající veřejnou IP adresu, textového pole Popisek názvu domény je zakázané. Jeho hodnota může být popisek názvu domény vybraného IP adresy.
- Aktualizace pro přípony (například westus.cloudapp.azure.com) název se domény, automaticky v závislosti na vybraném umístění.

## <a name="schema"></a>Schéma
```json
{
  "name": "element1",
  "type": "Microsoft.Network.PublicIpAddressCombo",
  "label": {
    "publicIpAddress": "Public IP address",
    "domainNameLabel": "Domain name label"
  },
  "toolTip": {
    "publicIpAddress": "",
    "domainNameLabel": ""
  },
  "defaultValue": {
    "publicIpAddressName": "ip01",
    "domainNameLabel": "mydomain"
  },
  "constraints": {
    "required": {
      "domainNameLabel": true
    }
  },
  "options": {
    "hideNone": false,
    "hideDomainNameLabel": false,
    "hideExisting": false,
    "zone": 3
  },
  "visible": true
}
```

## <a name="remarks"></a>Poznámky
- Pokud `constraints.required.domainNameLabel` je nastaven na **true**, musí uživatel zadat popisek názvu domény, při vytváření nové veřejnou IP adresu. Existující veřejné IP adresy bez štítek nejsou k dispozici pro výběr.
- Pokud `options.hideNone` je nastaven na **true**, pak možnost vybrat **žádné** pro veřejnou IP adresu skryt. Výchozí hodnota je **false**.
- Pokud `options.hideDomainNameLabel` je nastaven na **true**, textové pole pro popisek názvu domény je skrytý. Výchozí hodnota je **false**.
- Pokud `options.hideExisting` má hodnotu true, pak uživatel není možné vybrat stávající veřejnou IP adresu. Výchozí hodnota je **false**.
- Pro `zone`, pouze veřejné IP adresy zadané zóny, nebo zónu odolné veřejné IP adresy jsou k dispozici.

## <a name="sample-output"></a>Ukázkový výstup
Pokud uživatel vybere žádné veřejnou IP adresu, ovládacího prvku vrátí následující výstup:

```json
{
  "newOrExistingOrNone": "none"
}
```

Pokud uživatel vybere nový nebo existující IP adresu, ovládacího prvku vrátí následující výstup:

```json
{
  "name": "ip01",
  "resourceGroup": "rg01",
  "domainNameLabel": "mydomain",
  "publicIPAllocationMethod": "Dynamic",
  "newOrExistingOrNone": "new"
}
```

- Když `options.hideNone` je zadán jako **true**, `newOrExistingOrNone` budou mít pouze hodnotu **nové** nebo **existující**.
- Když `options.hideDomainNameLabel` je zadán jako **true**, `domainNameLabel` není deklarován.

## <a name="next-steps"></a>Další postup
* Úvod do vytváření definic uživatelského rozhraní, najdete v části [Začínáme s CreateUiDefinition](create-uidefinition-overview.md).
* Popis společných vlastností v prvky uživatelského rozhraní najdete v tématu [CreateUiDefinition elementy](create-uidefinition-elements.md).
