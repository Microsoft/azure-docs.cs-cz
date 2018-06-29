---
title: Azure elementu SizeSelector uživatelského rozhraní | Microsoft Docs
description: Popisuje element Microsoft.Compute.SizeSelector uživatelského rozhraní pro portál Azure.
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
ms.openlocfilehash: 9009d29e281ace179ad1dd2021c7cf35e3dc611a
ms.sourcegitcommit: d1eefa436e434a541e02d938d9cb9fcef4e62604
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/28/2018
ms.locfileid: "37084800"
---
# <a name="microsoftcomputesizeselector-ui-element"></a>Element Microsoft.Compute.SizeSelector uživatelského rozhraní
Ovládací prvek pro výběr velikost pro jeden nebo více instancí virtuálního počítače.

## <a name="ui-sample"></a>Ukázka uživatelského rozhraní

Uživateli se zobrazí selektor s výchozími hodnotami z definice elementu.

![Microsoft.Compute.SizeSelector](./media/managed-application-elements/microsoft.compute.sizeselector.png)

Po výběru ovládacího prvku, uživatel uvidí rozbalené zobrazení si dostupné velikosti.

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
- `recommendedSizes` musí mít alespoň jeden velikost. První doporučená velikost je použita jako výchozí. Seznam dostupných velikostí není seřazen podle doporučených stavu. Uživatele můžete vybrat tento sloupec seřadit podle doporučených stavu.
- Pokud doporučená velikost není k dispozici ve vybraném umístění, velikost automaticky přeskočen. Místo toho se používá další doporučená velikost.
- `constraints.allowedSizes` a `constraints.excludedSizes` obě jsou nepovinné, ale nelze používat současně. Seznam dostupných velikostí se dá určit pomocí volání [seznam dostupných velikostí virtuálních počítačů pro předplatné](/rest/api/compute/virtualmachines/virtualmachines-list-sizes-region). Jakékoli velikosti, nebyly zadány v `constraints.allowedSizes` skryt a jakékoli velikosti, nebyly zadány v `constraints.excludedSizes` se zobrazí.
- `osPlatform` musí být zadán, a může být buď **Windows** nebo **Linux**. Slouží k určení náklady na hardware virtuálních počítačů.
- `imageReference` pro první strany Image, ale zadaná pro třetí strany bitové kopie je vynechán. Slouží k určení náklady na software virtuálních počítačů.
- `count` slouží k nastavení odpovídající multiplikátor pro element. Podporuje statické hodnoty, jako je třeba **2**, nebo jako dynamické hodnoty z jiný element `[steps('step1').vmCount]`. Výchozí hodnota je **1**.
- `numAvailabilityZonesRequired` Může být 1, 2 nebo 3.
- Ve výchozím nastavení `hideDiskTypeFilter` je **false**. Filtr typu disku umožňuje uživatelům zobrazit všechny typy disků nebo pouze SSD.

## <a name="sample-output"></a>Ukázkový výstup
```json
"Standard_D1"
```

## <a name="next-steps"></a>Další postup
* Úvod do vytváření definic uživatelského rozhraní, najdete v části [Začínáme s CreateUiDefinition](create-uidefinition-overview.md).
* Popis společných vlastností v prvky uživatelského rozhraní najdete v tématu [CreateUiDefinition elementy](create-uidefinition-elements.md).
