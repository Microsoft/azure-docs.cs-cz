---
title: Azure prvek uživatelského rozhraní PublicIpAddressCombo | Dokumentace Microsoftu
description: Popisuje element Microsoft.Network.PublicIpAddressCombo uživatelského rozhraní pro Azure portal.
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
ms.openlocfilehash: c3e8c99f6648f0f4927140f3215978566afb9eb8
ms.sourcegitcommit: 0bb8db9fe3369ee90f4a5973a69c26bff43eae00
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/08/2018
ms.locfileid: "48868900"
---
# <a name="microsoftnetworkpublicipaddresscombo-ui-element"></a>Prvek uživatelského rozhraní Microsoft.Network.PublicIpAddressCombo
Skupina ovládacích prvků pro výběr nového nebo existujícího veřejnou IP adresu.

## <a name="ui-sample"></a>Ukázka uživatelského rozhraní
![Microsoft.Network.PublicIpAddressCombo](./media/managed-application-elements/microsoft.network.publicipaddresscombo.png)

- Pokud si uživatel vybere "Žádný" pro veřejnou IP adresu, do textového pole popisku název domény je skrytá.
- Pokud uživatel vybere stávající veřejnou IP adresu, do textového pole popisku název domény je zakázaný. Jeho hodnota je Popisek názvu domény pro vybranou IP adresu.
- Aktualizace pro přípony (například westus.cloudapp.azure.com) název se domény, automaticky podle vybraného umístění.

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
- Pokud `constraints.required.domainNameLabel` je nastavena na **true**, uživatel musí zadat popisek názvu domény, při vytváření novou veřejnou IP adresu. Existující veřejné IP adresy bez popisku nejsou k dispozici pro výběr.
- Pokud `options.hideNone` je nastavena na **true**, klikněte možnost vybrat si **žádný** pro veřejnou IP adresu skryté. Výchozí hodnota je **false**.
- Pokud `options.hideDomainNameLabel` je nastavena na **true**, textové pole pro popisek názvu domény je skrytý. Výchozí hodnota je **false**.
- Pokud `options.hideExisting` má hodnotu true, pak uživatel nemůže vybrat stávající veřejnou IP adresu. Výchozí hodnota je **false**.
- Pro `zone`, pouze veřejné IP adresy pro zadané zóny nebo zóny odolné veřejné IP adresy jsou k dispozici.

## <a name="sample-output"></a>Ukázkový výstup
Pokud si uživatel vybere žádná veřejná IP adresa, ovládací prvek vrátí následující výstup:

```json
{
  "newOrExistingOrNone": "none"
}
```

Pokud si uživatel vybere nový nebo existující IP adresu, ovládací prvek vrátí následující výstup:

```json
{
  "name": "ip01",
  "resourceGroup": "rg01",
  "domainNameLabel": "mydomain",
  "publicIPAllocationMethod": "Dynamic",
  "sku": "Basic",
  "newOrExistingOrNone": "new"
}
```

- Když `options.hideNone` je zadán jako **true**, `newOrExistingOrNone` budou mít pouze hodnotu **nové** nebo **existující**.
- Když `options.hideDomainNameLabel` je zadán jako **true**, `domainNameLabel` nedeklarovaný.

## <a name="next-steps"></a>Další postup
* Úvod do vytváření definic uživatelského rozhraní, naleznete v tématu [Začínáme s funkcí CreateUiDefinition](create-uidefinition-overview.md).
* Popis společné vlastnosti v prvcích uživatelského rozhraní, naleznete v tématu [CreateUiDefinition prvky](create-uidefinition-elements.md).
