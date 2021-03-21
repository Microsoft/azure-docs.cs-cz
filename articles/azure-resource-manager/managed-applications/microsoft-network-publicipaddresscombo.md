---
title: PublicIpAddressCombo – element uživatelského rozhraní
description: Popisuje prvek uživatelského rozhraní Microsoft. Network. PublicIpAddressCombo pro Azure Portal.
author: tfitzmac
ms.topic: conceptual
ms.date: 06/28/2018
ms.author: tomfitz
ms.openlocfilehash: 5def6db9d551b3882204c9f997f164a0df7ac223
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "87063281"
---
# <a name="microsoftnetworkpublicipaddresscombo-ui-element"></a>Microsoft. Network. PublicIpAddressCombo – element uživatelského rozhraní

Skupina ovládacích prvků pro výběr nové nebo existující veřejné IP adresy.

## <a name="ui-sample"></a>Ukázka uživatelského rozhraní

![Microsoft.Network.PublicIpAddressCombo](./media/managed-application-elements/microsoft-network-publicipaddresscombo.png)

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

- Pokud `options.hideNone` je zadána hodnota **true**, `newOrExistingOrNone` bude mít pouze hodnotu **New** nebo **existující**.
- `options.hideDomainNameLabel`Je-li parametr zadán jako **true**, `domainNameLabel` je nedeklarovaný.

## <a name="remarks"></a>Poznámky

- Pokud `constraints.required.domainNameLabel` je nastavená **hodnota true**, při vytváření nové veřejné IP adresy musí uživatel zadat popisek názvu domény. Stávající veřejné IP adresy bez popisku nejsou k dispozici pro výběr.
- Pokud `options.hideNone` je parametr nastaven na **hodnotu true**, možnost pro volbu **žádná** pro veřejnou IP adresu nebude skrytá. Výchozí hodnota je **false** (nepravda).
- Pokud `options.hideDomainNameLabel` je nastaveno na **hodnotu true**, bude textové pole pro popisek názvu domény skryté. Výchozí hodnota je **false** (nepravda).
- Pokud `options.hideExisting` je hodnota true, pak uživatel nebude moci zvolit stávající veřejnou IP adresu. Výchozí hodnota je **false** (nepravda).
- Pro jsou `zone` k dispozici pouze veřejné IP adresy pro zadanou zónu nebo odolné veřejné IP adresy.

## <a name="next-steps"></a>Další kroky

* Úvod k vytváření definic uživatelského rozhraní najdete v tématu [Začínáme s CreateUiDefinition](create-uidefinition-overview.md).
* Popis běžných vlastností v prvcích uživatelského rozhraní naleznete v tématu [CreateUiDefinition Elements](create-uidefinition-elements.md).
