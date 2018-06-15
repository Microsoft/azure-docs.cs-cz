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
ms.date: 04/30/2018
ms.author: tomfitz
ms.openlocfilehash: bf0ef5be609fba14ab12e1e6f9f97bc63f032aae
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/17/2018
ms.locfileid: "34260556"
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
Pokud uživatel vybere žádné veřejnou IP adresu, se předpokládá, že následující výstup:
```json
{
  "newOrExistingOrNone": "none"
}
```

Pokud uživatel vybere nový nebo existující IP adresu, se předpokládá, že následující výstup:
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
