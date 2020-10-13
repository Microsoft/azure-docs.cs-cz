---
title: SizeSelector – element uživatelského rozhraní
description: Popisuje prvek uživatelského rozhraní Microsoft. Compute. SizeSelector pro Azure Portal. Používá se pro výběr velikosti virtuálního počítače.
author: tfitzmac
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: tomfitz
ms.openlocfilehash: d6408f8c08694ae681d302ae35f5778894091733
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87063641"
---
# <a name="microsoftcomputesizeselector-ui-element"></a>Microsoft. Compute. SizeSelector – element uživatelského rozhraní

Ovládací prvek pro výběr velikosti pro jednu nebo více instancí virtuálního počítače.

## <a name="ui-sample"></a>Ukázka uživatelského rozhraní

Uživatel uvidí selektor s výchozími hodnotami z definice elementu.

![Microsoft.Compute.SizeSelector](./media/managed-application-elements/microsoft-compute-sizeselector.png)

Po výběru ovládacího prvku se uživateli zobrazí rozšířené zobrazení dostupných velikostí.

![Rozbalený Microsoft. Compute. SizeSelector](./media/managed-application-elements/microsoft-compute-sizeselector-expanded.png)

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

- `recommendedSizes` musí mít aspoň jednu velikost. První doporučená velikost se používá jako výchozí. Seznam dostupných velikostí není seřazen podle doporučeného stavu. Uživatel může vybrat tento sloupec pro řazení podle doporučeného stavu.
- Pokud není doporučená velikost ve vybraném umístění dostupná, velikost se automaticky přeskočí. Místo toho se použije další doporučená velikost.
- `constraints.allowedSizes` a `constraints.excludedSizes` jsou zároveň volitelné, ale nelze je použít současně. Seznam dostupných velikostí lze určit voláním [seznamu dostupné velikosti virtuálních počítačů pro předplatné](/rest/api/compute/virtualmachines/virtualmachines-list-sizes-region). Libovolná velikost, která není určená v, `constraints.allowedSizes` je skrytá a zobrazí se libovolná velikost, která není určená v `constraints.excludedSizes` .
- `osPlatform` musí být zadáno a může být buď **Windows** , nebo **Linux**. Slouží k určení nákladů na hardware pro virtuální počítače.
- `imageReference` je vynechán pro bitové kopie první strany, ale je k dispozici pro Image třetích stran. Slouží k určení nákladů na software virtuálních počítačů.
- `count` slouží k nastavení vhodného násobitele pro element. Podporuje statickou hodnotu, například **2**, nebo dynamickou hodnotu z jiného elementu, například `[steps('step1').vmCount]` . Výchozí hodnota je **1**.
- `numAvailabilityZonesRequired`Může být 1, 2 nebo 3.
- Ve výchozím nastavení `hideDiskTypeFilter` má **hodnotu false**. Filtr typ disku umožňuje uživateli zobrazit všechny typy disků nebo pouze SSD.

## <a name="next-steps"></a>Další kroky

* Úvod k vytváření definic uživatelského rozhraní najdete v tématu [Začínáme s CreateUiDefinition](create-uidefinition-overview.md).
* Popis běžných vlastností v prvcích uživatelského rozhraní naleznete v tématu [CreateUiDefinition Elements](create-uidefinition-elements.md).
