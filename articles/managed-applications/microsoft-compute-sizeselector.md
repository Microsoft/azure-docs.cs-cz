---
title: Element uživatelského rozhraní Azure SizeSelector | Microsoft Docs
description: Popisuje prvek uživatelského rozhraní Microsoft. Compute. SizeSelector pro Azure Portal. Používá se pro výběr velikosti virtuálního počítače.
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
ms.openlocfilehash: f3a8426856e1345306acff69946beb4860d5f905
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/17/2019
ms.locfileid: "74151530"
---
# <a name="microsoftcomputesizeselector-ui-element"></a>Microsoft. Compute. SizeSelector – element uživatelského rozhraní

Ovládací prvek pro výběr velikosti pro jednu nebo více instancí virtuálního počítače.

## <a name="ui-sample"></a>Ukázka uživatelského rozhraní

Uživatel uvidí selektor s výchozími hodnotami z definice elementu.

![Microsoft.Compute.SizeSelector](./media/managed-application-elements/microsoft.compute.sizeselector.png)

Po výběru ovládacího prvku se uživateli zobrazí rozšířené zobrazení dostupných velikostí.

![Rozbalený Microsoft. Compute. SizeSelector](./media/managed-application-elements/microsoft.compute.sizeselector-expanded.png)

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
- `constraints.allowedSizes` a `constraints.excludedSizes` jsou obě volitelné, ale nejde je použít současně. Seznam dostupných velikostí lze určit voláním [seznamu dostupné velikosti virtuálních počítačů pro předplatné](/rest/api/compute/virtualmachines/virtualmachines-list-sizes-region). Libovolná velikost, která není zadaná v `constraints.allowedSizes`, je skrytá a zobrazí se jakákoli velikost, která není určená v `constraints.excludedSizes`.
- je nutné zadat `osPlatform` a může být buď **Windows** , nebo **Linux**. Slouží k určení nákladů na hardware pro virtuální počítače.
- `imageReference` je u imagí z první strany vynechána, ale jsou k dispozici pro Image třetích stran. Slouží k určení nákladů na software virtuálních počítačů.
- `count` slouží k nastavení vhodného násobitele pro element. Podporuje statickou hodnotu, například **2**, nebo dynamickou hodnotu z jiného prvku, například `[steps('step1').vmCount]`. Výchozí hodnota je **1**.
- `numAvailabilityZonesRequired` může být 1, 2 nebo 3.
- Ve výchozím nastavení `hideDiskTypeFilter` má **hodnotu false**. Filtr typ disku umožňuje uživateli zobrazit všechny typy disků nebo pouze SSD.

## <a name="next-steps"></a>Další kroky

* Úvod k vytváření definic uživatelského rozhraní najdete v tématu [Začínáme s CreateUiDefinition](create-uidefinition-overview.md).
* Popis běžných vlastností v prvcích uživatelského rozhraní naleznete v tématu [CreateUiDefinition Elements](create-uidefinition-elements.md).
