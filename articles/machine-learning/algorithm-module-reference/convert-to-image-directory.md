---
title: Převod do adresáře obrázků
titleSuffix: Azure Machine Learning
description: Naučte se používat modul pro převod dat na image pro převod datové sady na formát adresáře obrázků.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 05/26/2020
ms.openlocfilehash: 677cf60ff3e614fd1486445786154fbf026b7cd9
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/22/2020
ms.locfileid: "90898687"
---
# <a name="convert-to-image-directory"></a>Převod do adresáře obrázků

Tento článek popisuje, jak pomocí modulu příkazového adresáře převést na obrázek převést datovou sadu na datový typ "adresář obrázků", což je standardizovaný formát dat v úkolech souvisejících s obrázky, jako je například klasifikace obrázku v Návrháři Azure Machine Learning.

## <a name="how-to-use-convert-to-image-directory"></a>Jak použít převod na adresář imagí  

1.  Přidejte do experimentu modul **adresář pro převod do bitové kopie** . Tento modul můžete najít v kategorii "Počítačové zpracování obrazu/Image Transformation data" v seznamu modul. 

2.  [Zaregistrujte datovou sadu obrázku](https://docs.microsoft.com/azure/machine-learning/how-to-create-register-datasets) a připojte ji ke vstupnímu portu modulu. Ujistěte se prosím, že ve vstupní datové sadě je obrázek. 
    Podporovány jsou následující formáty datové sady:

    - Komprimovaný soubor v těchto rozšířeních:. zip,. tar,. gz,. bz2
    - Složka obsahující obrázky. **Velmi doporučujeme kompresi takových složek nejprve použít komprimovaný soubor jako datovou sadu**.

    > [!WARNING]
    > Modul **Import dat** **nelze** použít pro import datové sady imagí, protože výstupní typ modulu **importu dat** je adresář datového rámce, který obsahuje pouze řetězec cesty k souboru.
    

    > [!NOTE]
    > Pokud používáte datovou sadu obrázků v dohledovém učení, je popisek povinný.
    > V případě úlohy klasifikace obrázku může být popisek vygenerován jako obrázek "kategorie" ve výstupu modulu, pokud je tato datová sada obrázku uspořádána ve formátu torchvision ImageFolder. V opačném případě se bez popisku uloží pouze obrázky. Tady je příklad, jak můžete organizovat datovou sadu obrázků a získat popisek, použít kategorii obrázku jako název podsložky. Další informace najdete v tématu [torchvision DataSets](https://pytorch.org/docs/stable/torchvision/datasets.html#imagefolder) .
    >
    > ```
    > root/dog/xxx.png
    > root/dog/xxy.png
    > root/dog/xxz.png
    >
    > root/cat/123.png
    > root/cat/nsdf3.png
    > root/cat/asd932_.png
    > ```

3.  Odešlete kanál.

## <a name="results"></a>Výsledky

Výstup do adresářového **adresáře převést na Image** je ve formátu adresáře image a dá se připojit k ostatním modulům souvisejícím s imagemi, ve kterých je formát vstupního portu také adresářem obrázků.

## <a name="technical-notes"></a>Technické poznámky 

###  <a name="expected-inputs"></a>Očekávané vstupy  

| Název          | Typ                  | Description   |
| ------------- | --------------------- | ------------- |
| Vstupní datová sada | AnyDirectory, podřízený ZipFile | Vstupní datová sada |

###  <a name="output"></a>Výstup  

| Název                   | Typ           | Description            |
| ---------------------- | -------------- | ---------------------- |
| Adresář výstupních imagí | ImageDirectory | Adresář výstupních imagí |

## <a name="next-steps"></a>Další kroky

Podívejte se na [sadu modulů, které jsou k dispozici](module-reference.md) pro Azure Machine Learning. 
