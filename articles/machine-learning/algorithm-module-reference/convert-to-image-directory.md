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
ms.date: 10/09/2020
ms.openlocfilehash: 2e597299c9b157d79a5317c97550fc30820636d6
ms.sourcegitcommit: 541bb46e38ce21829a056da880c1619954678586
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2020
ms.locfileid: "91940346"
---
# <a name="convert-to-image-directory"></a>Převod do adresáře obrázků

Tento článek popisuje, jak použít modul adresář pro převod do bitové kopie, který umožňuje převést datovou sadu obrázku na datový typ datového *adresáře* , který je standardizovaným formátem dat v úkolech souvisejících s obrázky, jako je například klasifikace obrázků v Návrháři Azure Machine Learning.

## <a name="how-to-use-convert-to-image-directory"></a>Jak použít převod na adresář imagí  

1. Nejprve Připravte datovou sadu obrázku. 

    Pro účely učení pod dohledem je nutné zadat popisek datové sady školení. Soubor datové sady imagí by měl být v následující struktuře:
    
    ```
    Your_image_folder_name/Category_1/xxx.png
    Your_image_folder_name/Category_1/xxy.jpg
    Your_image_folder_name/Category_1/xxz.jpeg
    
    Your_image_folder_name/Category_2/123.png
    Your_image_folder_name/Category_2/nsdf3.png
    Your_image_folder_name/Category_2/asd932_.png
    ```
    
    Ve složce datové sady imagí existuje více podsložek. Každá podsložka obsahuje obrázky jedné kategorie. Názvy podsložek se považují za popisky pro úlohy, jako je například klasifikace obrázku. Další informace najdete v tématu [torchvision DataSets](https://pytorch.org/docs/stable/torchvision/datasets.html#imagefolder) .

    > [!WARNING]
    > Aktuálně označené datové sady, které jsou vyexportovány z popisků dat, nejsou v Návrháři podporovány.

    Obrázky s těmito příponami (malými písmeny) jsou podporované: ". jpg", ". jpeg", ". png", ". ppm", ". bmp", ". PGM", ". tif", ". TIFF", ". webp". V jedné složce můžete mít také více typů obrázků. V každé složce kategorií není nutné obsahovat stejný počet imagí.

    Můžete buď použít složku nebo komprimovaný soubor s příponou. zip, ". tar", ". gz" a ". bz2". **Pro lepší výkon se doporučuje komprimovaných souborů.** 
    
    ![Ukázková datová sada obrázku](./media/module/image-sample-dataset.png)

    Pro účely bodování musí složka sady dat image obsahovat jenom neklasifikované image.

1. [Zaregistrujte datovou sadu obrázku jako datovou sadu](https://docs.microsoft.com/azure/machine-learning/how-to-create-register-datasets) v pracovním prostoru, protože vstup pro modul adresáře pro převod do bitové kopie musí být **Souborová sada**.

1. Přidejte do plátna datovou sadu registrovaných obrázků. Registrovanou datovou sadu můžete najít v kategorii **DataSets** v seznamu modul v levé části plátna. Aktuálně Návrhář nepodporuje sadu dat vizualizace obrázku.

    > [!WARNING]
    > Modul **Import dat** **nelze** použít pro import datové sady imagí, protože výstupní typ modulu **importu dat** je adresář datového rámce, který obsahuje pouze řetězec cesty k souboru.

1. Přidejte do plátna modul **adresáře převést do obrázku** . Tento modul můžete najít v kategorii "Počítačové zpracování obrazu/Image Transformation data" v seznamu modul. Připojte ho k datové sadě obrázků.
    
3.  Odešlete kanál. Tento modul se dá spustit buď pro GPU, nebo pro procesor.

## <a name="results"></a>Výsledky

Výstup modulu **pro výpis adresářů imagí** je ve formátu **adresáře obrázků** a může být připojen k jiným modulům souvisejícím s obrázky, z nichž je formát vstupního portu také adresářem obrázku.

![Převést na výstup adresáře obrázků](./media/module/convert-to-image-directory-output.png)

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
