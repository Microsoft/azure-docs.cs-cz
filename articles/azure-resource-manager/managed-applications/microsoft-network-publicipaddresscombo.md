---
title: PublicIpAddressCombo Prvek ui
description: Popisuje prvek ui microsoft.network.publicipaddresscombo pro portál Azure.
author: tfitzmac
ms.topic: conceptual
ms.date: 06/28/2018
ms.author: tomfitz
ms.openlocfilehash: 0393673663df8f3ca580ff34e16bee910b955f8d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75651914"
---
# <a name="microsoftnetworkpublicipaddresscombo-ui-element"></a>Prvek ui rozhraní Microsoft.Network.PublicIpAddressCombo

Skupina ovládacích prvků pro výběr nové nebo existující veřejné IP adresy.

## <a name="ui-sample"></a>Ukázka ui

![Microsoft.Network.PublicIpAddressCombo](./media/managed-application-elements/microsoft.network.publicipaddresscombo.png)

- Pokud uživatel vybere "Žádný" pro veřejnou IP adresu, textové pole popisku názvu domény je skryté.
- Pokud uživatel vybere existující veřejnou IP adresu, textové pole popisek názvu domény je zakázáno. Jeho hodnota je název domény vybrané IP adresy.
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

Pokud uživatel vybere žádnou veřejnou IP adresu, ovládací prvek vrátí následující výstup:

```json
{
  "newOrExistingOrNone": "none"
}
```

Pokud uživatel vybere novou nebo existující adresu IP, ovládací prvek vrátí následující výstup:

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

- Pokud `options.hideNone` je **true**zadán `newOrExistingOrNone` jako true , bude mít pouze hodnotu **nové** nebo **existující**.
- Pokud `options.hideDomainNameLabel` je **true**zadán `domainNameLabel` jako true , je nedeklarovaný.

## <a name="remarks"></a>Poznámky

- Pokud `constraints.required.domainNameLabel` je nastavena hodnota **true**, musí uživatel při vytváření nové veřejné IP adresy zadat popisek názvu domény. Existující veřejné IP adresy bez popisku nejsou k dispozici pro výběr.
- Pokud `options.hideNone` je nastavena na **hodnotu true**, pak je možnost vybrat **žádný** pro veřejnou IP adresu skrytá. Výchozí hodnota je **false** (nepravda).
- Pokud `options.hideDomainNameLabel` je nastavena hodnota **true**, je textové pole pro popisek názvu domény skryté. Výchozí hodnota je **false** (nepravda).
- Pokud `options.hideExisting` je true, pak uživatel není schopen vybrat existující veřejnou IP adresu. Výchozí hodnota je **false** (nepravda).
- Pro `zone`, jsou k dispozici pouze veřejné IP adresy pro zadanou zónu nebo zónu odolné veřejné IP adresy.

## <a name="next-steps"></a>Další kroky

* Úvod k vytváření definic ui naleznete [v tématu Začínáme s CreateUiDefinition](create-uidefinition-overview.md).
* Popis běžných vlastností v prvcích uživatelského rozhraní naleznete v [tématu CreateUiDefinition elements](create-uidefinition-elements.md).
