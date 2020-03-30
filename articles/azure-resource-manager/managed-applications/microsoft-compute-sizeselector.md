---
title: Prvek ui voliče voliče voliče
description: Popisuje prvek ui Microsoft.Compute.SizeSelector pro portál Azure. Slouží k výběru velikosti virtuálního počítače.
author: tfitzmac
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: tomfitz
ms.openlocfilehash: d98ddbb09ac4f1f933237b3288db7a0cb78380b5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75652044"
---
# <a name="microsoftcomputesizeselector-ui-element"></a>Element ui microsoft.compute.sizeselector

Ovládací prvek pro výběr velikosti pro jednu nebo více instancí virtuálních strojů.

## <a name="ui-sample"></a>Ukázka ui

Uživatel vidí volič s výchozími hodnotami z definice prvku.

![Microsoft.Compute.SizeSelector](./media/managed-application-elements/microsoft.compute.sizeselector.png)

Po výběru ovládacího prvku se uživateli zobrazí rozšířené zobrazení dostupných velikostí.

![Microsoft.Compute.SizeSelector rozbalen](./media/managed-application-elements/microsoft.compute.sizeselector-expanded.png)

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

## <a name="sample-output"></a>Ukázkový výstup

```json
"Standard_D1"
```

## <a name="remarks"></a>Poznámky

- `recommendedSizes`by měl mít alespoň jednu velikost. První doporučená velikost se používá jako výchozí. Seznam dostupných velikostí není seřazen podle doporučeného stavu. Uživatel může vybrat tento sloupec pro řazení podle doporučeného stavu.
- Pokud doporučená velikost není ve vybraném umístění k dispozici, velikost se automaticky přeskočí. Místo toho se použije další doporučená velikost.
- `constraints.allowedSizes`a `constraints.excludedSizes` jsou volitelné, ale nelze je použít současně. Seznam dostupných velikostí lze určit voláním [Seznam dostupných velikostí virtuálních strojů pro předplatné](/rest/api/compute/virtualmachines/virtualmachines-list-sizes-region). Je zobrazena jakákoli `constraints.allowedSizes` velikost, která není zadána v písmenu a), a je zobrazena jakákoli velikost, která není zadána v `constraints.excludedSizes` písmenu a).
- `osPlatform`musí být zadána a může být buď **Windows** nebo **Linux**. Používá se k určení nákladů na hardware virtuálních počítačů.
- `imageReference`je vynechán pro obrázky první strany, ale je k dispozici pro obrázky třetích stran. Používá se k určení nákladů na software virtuálních počítačů.
- `count`se používá k nastavení příslušného násobiče prvku. Podporuje statickou hodnotu, například **2**nebo dynamickou `[steps('step1').vmCount]`hodnotu z jiného prvku, například . Výchozí hodnota je **1**.
- Může `numAvailabilityZonesRequired` být 1, 2 nebo 3.
- Ve výchozím `hideDiskTypeFilter` nastavení je **false**. Filtr typu disku umožňuje uživateli zobrazit všechny typy disků nebo pouze Disk SSD.

## <a name="next-steps"></a>Další kroky

* Úvod k vytváření definic ui naleznete [v tématu Začínáme s CreateUiDefinition](create-uidefinition-overview.md).
* Popis běžných vlastností v prvcích uživatelského rozhraní naleznete v [tématu CreateUiDefinition elements](create-uidefinition-elements.md).
