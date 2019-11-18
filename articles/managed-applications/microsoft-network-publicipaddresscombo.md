---
title: Element uživatelského rozhraní Azure PublicIpAddressCombo | Microsoft Docs
description: Popisuje prvek uživatelského rozhraní Microsoft. Network. PublicIpAddressCombo pro Azure Portal.
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
ms.openlocfilehash: 069a8ee1f019d1b21be996084e4902f94076fbf7
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/17/2019
ms.locfileid: "74151499"
---
# <a name="microsoftnetworkpublicipaddresscombo-ui-element"></a>Microsoft. Network. PublicIpAddressCombo – element uživatelského rozhraní

Skupina ovládacích prvků pro výběr nové nebo existující veřejné IP adresy.

## <a name="ui-sample"></a>Ukázka uživatelského rozhraní

![Microsoft.Network.PublicIpAddressCombo](./media/managed-application-elements/microsoft.network.publicipaddresscombo.png)

- Pokud uživatel vybere možnost None pro veřejnou IP adresu, bude textové pole Popisek názvu domény skryté.
- Pokud uživatel vybere existující veřejnou IP adresu, bude textové pole Popisek názvu domény zakázané. Jeho hodnota je popisek názvu domény vybrané IP adresy.
- Přípona názvu domény (například westus.cloudapp.azure.com) se automaticky aktualizuje na základě vybraného umístění.

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

## <a name="sample-output"></a>Ukázkový výstup

Pokud uživatel nevybere žádnou veřejnou IP adresu, ovládací prvek vrátí následující výstup:

```json
{
  "newOrExistingOrNone": "none"
}
```

Pokud uživatel vybere novou nebo existující IP adresu, vrátí ovládací prvek následující výstup:

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

- Pokud je parametr `options.hideNone` zadán jako **true**, `newOrExistingOrNone` bude mít pouze hodnotu **New** nebo **existed**.
- Pokud je parametr `options.hideDomainNameLabel` zadán jako **true**, `domainNameLabel` je nedeklarovaný.

## <a name="remarks"></a>Poznámky

- Pokud je `constraints.required.domainNameLabel` nastaveno na **hodnotu true**, při vytváření nové veřejné IP adresy musí uživatel zadat popisek názvu domény. Stávající veřejné IP adresy bez popisku nejsou k dispozici pro výběr.
- Pokud je parametr `options.hideNone` nastaven na **hodnotu true**, možnost pro volbu **žádná** pro veřejnou IP adresu nebude skrytá. Výchozí hodnota je **false**.
- Pokud je `options.hideDomainNameLabel` nastaveno na **hodnotu true**, pak je textové pole pro popisek názvu domény skryté. Výchozí hodnota je **false**.
- Pokud má `options.hideExisting` hodnotu true, uživatel nebude moci zvolit stávající veřejnou IP adresu. Výchozí hodnota je **false**.
- Pro `zone`jsou k dispozici pouze veřejné IP adresy pro zadanou zónu nebo odolné veřejné IP adresy.

## <a name="next-steps"></a>Další kroky

* Úvod k vytváření definic uživatelského rozhraní najdete v tématu [Začínáme s CreateUiDefinition](create-uidefinition-overview.md).
* Popis běžných vlastností v prvcích uživatelského rozhraní naleznete v tématu [CreateUiDefinition Elements](create-uidefinition-elements.md).
