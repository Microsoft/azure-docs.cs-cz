---
title: Převést na adresář obrázků
titleSuffix: Azure Machine Learning
description: Naučte se používat modul pro převod dat na image pro převod datové sady na formát adresáře obrázků.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 05/26/2020
ms.openlocfilehash: dc40e0a644f692b397b1f2107b27b1d940d2b284
ms.sourcegitcommit: 0a5bb9622ee6a20d96db07cc6dd45d8e23d5554a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/05/2020
ms.locfileid: "84450630"
---
# <a name="convert-to-image-directory"></a>Převést na adresář obrázků

Tento článek popisuje, jak pomocí modulu příkazového adresáře převést na obrázek převést datovou sadu na datový typ "adresář obrázků", což je standardizovaný formát dat v úkolech souvisejících s obrázky, jako je například klasifikace obrázku v Návrháři Azure Machine Learning (Preview).

## <a name="how-to-use-convert-to-image-directory"></a>Jak použít převod na adresář imagí  

1.  Přidejte do experimentu modul **adresář pro převod do bitové kopie** . Tento modul můžete najít v kategorii "Počítačové zpracování obrazu/Image Transformation data" v seznamu modul. 

2.  Připojte jako vstup datovou sadu obrázku. Ujistěte se prosím, že ve vstupní datové sadě je obrázek.
    Podporovány jsou následující formáty datové sady:

    - Komprimovaný soubor v těchto rozšířeních:. zip,. tar,. gz,. bz2
    - Složka obsahující 1 komprimovaný soubor ve výše platných rozšířeních. 
    - Složka obsahující obrázky.

    > [!NOTE]
    > Kategorie obrázku se dá zaznamenat ve výstupu modulu, pokud je tato datová sada obrázku uspořádaná ve formátu torchvision ImageFolder, další informace najdete v tématu [torchvision DataSets](https://pytorch.org/docs/stable/torchvision/datasets.html#imagefolder) . V opačném případě jsou uloženy pouze obrázky.

3.  Odešlete kanál.

## <a name="results"></a>Výsledky

Výstup do adresářového **adresáře převést na Image** je ve formátu adresáře image a dá se připojit k ostatním modulům souvisejícím s imagemi, ve kterých je formát vstupního portu také adresářem obrázků.

## <a name="technical-notes"></a>Technické poznámky 

###  <a name="expected-inputs"></a>Očekávané vstupy  

| Name          | Typ                  | Description   |
| ------------- | --------------------- | ------------- |
| Vstupní datová sada | AnyDirectory, podřízený ZipFile | Vstupní datová sada |

###  <a name="output"></a>Výstup  

| Name                   | Typ           | Description            |
| ---------------------- | -------------- | ---------------------- |
| Adresář výstupních imagí | ImageDirectory | Adresář výstupních imagí |

## <a name="next-steps"></a>Další kroky

Podívejte se na [sadu modulů, které jsou k dispozici](module-reference.md) pro Azure Machine Learning. 
