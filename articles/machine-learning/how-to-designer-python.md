---
title: Python
titleSuffix: Azure Machine Learning
description: Naučte se používat Python v Návrháři Azure Machine Learning k transformaci dat.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
author: peterclu
ms.author: peterlu
ms.date: 02/28/2020
ms.openlocfilehash: a2bd9845cd29c7d139e2042f39b4697847639207
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79455787"
---
# <a name="execute-python-code-in-azure-machine-learning-designer"></a>Spuštění kódu Pythonu v návrháři Azure Machine Learning

V tomto článku se dozvíte, jak pomocí modulu [Spouštět Python Script](algorithm-module-reference/execute-python-script.md) přidat vlastní logiku do návrháře Azure Machine Learning. V následujícím návodu pomocí knihovny Pand můžete provést jednoduché technické znalosti.

Vestavěný editor kódu můžete použít k rychlému přidání jednoduché logiky Pythonu. Pokud chcete přidat složitější kód nebo nahrát další knihovny Pythonu, měli byste použít metodu zip file.

Výchozí spuštění prostředí používá distribuci Anacondas Pythonu. Úplný seznam předinstalovaných balíčků najdete na stránce s odkazem na [modul Spouštět skript pythonu.](algorithm-module-reference/execute-python-script.md)

![Spustit vstupní mapu Pythonu](media/how-to-designer-python/execute-python-map.png)

## <a name="execute-python-written-in-the-designer"></a>Spustit Python napsaný v návrháři

### <a name="add-the-execute-python-script-module"></a>Přidání modulu Spustit skript Pythonu

1. Najděte modul **Spustit skript Pythonu** v paletě návrháře. Najdete ji v sekci **Jazyk Pythonu.**

1. Přetáhněte modul na plátno potrubí.

### <a name="connect-input-datasets"></a>Připojení vstupních datových sad

Tento článek používá ukázkovou datovou **sadu, data o cenách automobilu (Raw).** 

1. Přetáhněte datovou sadu na plátno kanálu.

1. Připojte výstupní port datové sady k levému hornímu vstupnímu portu modulu **Execute Python Script.** Návrhář zpřístupňuje vstup jako parametr skriptu vstupního bodu.
    
    Pravý vstupní port je vyhrazen pro knihovny pythonů zip.

    ![Připojení datových sad](media/how-to-designer-python/connect-dataset.png)
        

1. Poznamenejte si, který vstupní port používáte. Návrhář přiřadí levý vstupní port `dataset1` proměnné a prostřední `dataset2`vstupní port . 

Vstupní moduly jsou volitelné, protože můžete generovat nebo importovat data přímo v modulu **Spustit skript Pythonu.**

### <a name="write-your-python-code"></a>Napište kód Pythonu

Návrhář poskytuje počáteční vstupní bod skript pro úpravu a zadání vlastního kódu Pythonu. 

V tomto příkladu použijete pandy ke zkombinování dvou sloupců nalezených v datové sadě **automobilu Price** and **Horsepower**a vytvoření nového sloupce **Dolary za koňskou sílu**. Tento sloupec představuje, kolik zaplatíte za každý koňský výkon, což by mohlo být užitečnou funkcí při rozhodování, zda je auto za peníze dobré. 

1. Vyberte modul **Spustit skript Pythonu.**

1. V podokně, které se zobrazí vpravo od plátna, vyberte textové pole **skriptu Pythonu.**

1. Zkopírujte a vložte následující kód do textového pole.

    ```python
    import pandas as pd
    
    def azureml_main(dataframe1 = None, dataframe2 = None):
        dataframe1['Dollar/HP'] = dataframe1.price / dataframe1.horsepower
        return dataframe1
    ```
    Kanál by měl vypadat na následujícím obrázku:
    
    ![Spuštění kanálu Pythonu](media/how-to-designer-python/execute-python-pipeline.png)

    Skript vstupního bodu musí `azureml_main`obsahovat funkci . Existují dva parametry funkce, které se mapují na dva vstupní porty pro modul **Execute Python Script.**

    Vrácená hodnota musí být Pandas Dataframe. Jako výstupy modulu můžete vrátit až dva datové rámce.
    
1. Odešlete potrubí.

Nyní máte datovou sadu s novou funkcí **Dolarů / HP**, které by mohly být užitečné při školení auto doporučující. Toto je příklad extrakce prvků a zmenšení rozměrnosti. 

## <a name="next-steps"></a>Další kroky

Zjistěte, jak [importovat vlastní data](how-to-designer-import-data.md) v návrháři Azure Machine Learning.