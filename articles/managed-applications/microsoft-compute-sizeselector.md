---
title: Azure prvek uživatelského rozhraní SizeSelector | Dokumentace Microsoftu
description: Popisuje element Microsoft.Compute.SizeSelector uživatelského rozhraní pro Azure portal.
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
ms.date: 06/27/2018
ms.author: tomfitz
ms.openlocfilehash: e5be5635964ebeedc7be4d1d1f5403e4d281b55c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "64722343"
---
# <a name="microsoftcomputesizeselector-ui-element"></a>Prvek uživatelského rozhraní Microsoft.Compute.SizeSelector
Ovládací prvek pro výběr velikosti pro jeden nebo více instancí virtuálního počítače.

## <a name="ui-sample"></a>Ukázka uživatelského rozhraní

Uživateli se zobrazí selektor s výchozími hodnotami z definice prvku.

![Microsoft.Compute.SizeSelector](./media/managed-application-elements/microsoft.compute.sizeselector.png)

Uživateli se zobrazí po výběru ovládacího prvku rozbalené zobrazení dostupných velikostí.

![Microsoft.Compute.SizeSelector rozšířit](./media/managed-application-elements/microsoft.compute.sizeselector-expanded.png)

## <a name="schema"></a>Schéma
```json
{
  "name": "element1",
  "type": "Microsoft.Compute.SizeSelector",
  "label": "Size",
  "toolTip": "",
  "recommendedSizes": [
    "Standard_D1",
    "Standard_D2",
    "Standard_D3"
  ],
  "constraints": {
    "allowedSizes": [],
    "excludedSizes": [],
    "numAvailabilityZonesRequired": 3,
    "zone": "3"
  },
  "options": {
    "hideDiskTypeFilter": false
  },
  "osPlatform": "Windows",
  "imageReference": {
    "publisher": "MicrosoftWindowsServer",
    "offer": "WindowsServer",
    "sku": "2012-R2-Datacenter"
  },
  "count": 2,
  "visible": true
}
```

## <a name="remarks"></a>Poznámky
- `recommendedSizes` musí mít velikost alespoň jednu. První doporučená velikost je použito jako výchozí. Seznam dostupných velikostí není řazen dle doporučené stavu. Uživatel může vybrat tento sloupec seřadit podle doporučených stavu.
- Pokud doporučená velikost není k dispozici ve vybraném umístění, velikost se automaticky přeskočí. Místo toho se používá další doporučenou velikost.
- `constraints.allowedSizes` a `constraints.excludedSizes` jsou volitelné, ale nelze používat současně. Seznam dostupných velikostí můžete určit pomocí volání [seznam dostupných velikostí virtuálních počítačů pro předplatné](/rest/api/compute/virtualmachines/virtualmachines-list-sizes-region). Libovolné velikosti není zadáno v `constraints.allowedSizes` je skrytá a libovolné velikosti není zadáno v `constraints.excludedSizes` se zobrazí.
- `osPlatform` musí být zadán, a může být buď **Windows** nebo **Linux**. Používá se k určení náklady na hardware virtuálních počítačů.
- `imageReference` je vynechána, pro vlastní Image, ale k dispozici pro Image třetích stran. Používá se k určení náklady na software virtuálních počítačů.
- `count` slouží k nastavení odpovídající multiplikátor pro element. Statická hodnota, jako je třeba podporuje **2**, nebo dynamickou hodnotu z jiných prvků, jako je `[steps('step1').vmCount]`. Výchozí hodnota je **1**.
- `numAvailabilityZonesRequired` Může být 1, 2 nebo 3.
- Ve výchozím nastavení `hideDiskTypeFilter` je **false**. Filtr typu disku umožňuje uživatelům zobrazit všechny typy disků nebo jen SSD.

## <a name="sample-output"></a>Ukázkový výstup
```json
"Standard_D1"
```

## <a name="next-steps"></a>Další postup
* Úvod do vytváření definic uživatelského rozhraní, naleznete v tématu [Začínáme s funkcí CreateUiDefinition](create-uidefinition-overview.md).
* Popis společné vlastnosti v prvcích uživatelského rozhraní, naleznete v tématu [CreateUiDefinition prvky](create-uidefinition-elements.md).
