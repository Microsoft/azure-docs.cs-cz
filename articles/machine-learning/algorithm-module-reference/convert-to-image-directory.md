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
ms.date: 09/28/2020
ms.openlocfilehash: 9f5f4b2b069ebc65430fba4bc31a9891ed61fedf
ms.sourcegitcommit: 3792cf7efc12e357f0e3b65638ea7673651db6e1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/29/2020
ms.locfileid: "91450111"
---
# <a name="convert-to-image-directory"></a>Převod do adresáře obrázků

Tento článek popisuje, jak pomocí modulu příkazového adresáře převést na obrázek převést datovou sadu na datový typ "adresář obrázků", což je standardizovaný formát dat v úkolech souvisejících s obrázky, jako je například klasifikace obrázku v Návrháři Azure Machine Learning.

## <a name="how-to-use-convert-to-image-directory"></a>Jak použít převod na adresář imagí  

1.  Přidejte do plátna modul **adresáře převést do obrázku** . Tento modul můžete najít v kategorii "Počítačové zpracování obrazu/Image Transformation data" v seznamu modul. 

2.  Vstup pro modul **adresáře pro převod do bitové kopie** musí být Souborová sada. [Zaregistrujte datovou sadu obrázku](https://docs.microsoft.com/azure/machine-learning/how-to-create-register-datasets) a připojte ji ke vstupnímu portu modulu. Ujistěte se prosím, že ve vstupní datové sadě je obrázek. Aktuálně Návrhář nepodporuje sadu dat vizualizace obrázku.
 
    Podporovány jsou následující formáty datové sady:

    - Komprimovaný soubor v těchto rozšířeních:. zip,. tar,. gz,. bz2
    - Složka obsahující obrázky. **Velmi doporučujeme kompresi takových složek nejprve použít komprimovaný soubor jako datovou sadu**.

    > [!WARNING]
    > Modul **Import dat** **nelze** použít pro import datové sady imagí, protože výstupní typ modulu **importu dat** je adresář datového rámce, který obsahuje pouze řetězec cesty k souboru.
    

    > [!NOTE]
    > - Pokud používáte datovou sadu obrázků v dohledovém učení, je nutné zadat popisek datové sady školení.
    > - V případě úlohy klasifikace obrázku může být popisek vygenerován jako obrázek "kategorie" ve výstupu modulu, pokud je tato datová sada obrázku uspořádána ve formátu torchvision ImageFolder. V opačném případě se bez popisku uloží pouze obrázky. Následuje příklad, jak můžete uspořádat datovou sadu obrázků a získat popisek, použít kategorii obrázku jako název podsložky. 
    > - Nemusíte nahrávat stejný počet imagí do každé složky kategorií.
    > - Obrázky s těmito příponami (malými písmeny) jsou podporované: ". jpg", ". jpeg", ". png", ". ppm", ". bmp", ". PGM", ". tif", ". TIFF", ". webp". V jedné složce můžete mít také více typů obrázků.    
    > - Další informace najdete v tématu [torchvision DataSets](https://pytorch.org/docs/stable/torchvision/datasets.html#imagefolder) .
    >
    > ```
    > Your_image_folder_name/Category_1/xxx.png
    > Your_image_folder_name/Category_1/xxy.jpg
    > Your_image_folder_name/Category_1/xxz.jpeg
    >
    > Your_image_folder_name/Category_2/123.png
    > Your_image_folder_name/Category_2/nsdf3.png
    > Your_image_folder_name/Category_2/asd932_.png
    > ```
    > - Pokud použijete pro bodování datovou sadu obrázků, musí datová sada vstupního souboru tohoto modulu obsahovat neklasifikované image.
    
3.  Odešlete kanál. Tento modul se dá spustit buď pro GPU, nebo pro procesor.

## <a name="results"></a>Výsledky

Výstup do adresářového **adresáře převést na Image** je ve formátu adresáře image a dá se připojit k ostatním modulům souvisejícím s imagemi, ve kterých je formát vstupního portu také adresářem obrázků.

## <a name="technical-notes"></a>Technické poznámky 

###  <a name="expected-inputs"></a>Očekávané vstupy  

| Název          | Typ                  | Popis   |
| ------------- | --------------------- | ------------- |
| Vstupní datová sada | AnyDirectory, podřízený ZipFile | Vstupní datová sada |

###  <a name="output"></a>Výstup  

| Název                   | Typ           | Popis            |
| ---------------------- | -------------- | ---------------------- |
| Adresář výstupních imagí | ImageDirectory | Adresář výstupních imagí |

## <a name="next-steps"></a>Další kroky

Podívejte se na [sadu modulů, které jsou k dispozici](module-reference.md) pro Azure Machine Learning. 
