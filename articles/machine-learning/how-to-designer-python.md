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
ms.custom: designer
ms.openlocfilehash: f37fca21b4553d7ce6892e21d8ae0470873e111f
ms.sourcegitcommit: b55d1d1e336c1bcd1c1a71695b2fd0ca62f9d625
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/04/2020
ms.locfileid: "84433370"
---
# <a name="execute-python-code-in-azure-machine-learning-designer"></a>Spouštění kódu v Pythonu v Návrháři Azure Machine Learning

V tomto článku se dozvíte, jak pomocí modulu [spouštěného skriptu Pythonu](algorithm-module-reference/execute-python-script.md) přidat vlastní logiku do návrháře Azure Machine Learning. V následujícím postupu můžete pomocí knihovny PANDAS provádět jednoduché technologie funkcí.

Můžete použít vestavěný editor kódu k rychlému přidání jednoduché logiky Pythonu. Pokud chcete přidat složitější kód nebo nahrát další knihovny Pythonu, měli byste použít metodu souboru ZIP.

Výchozí prostředí pro spouštění používá Anacondasou distribuci Pythonu. Úplný seznam předinstalovaných balíčků najdete na stránce [spuštění referenčního modulu skriptu Pythonu](algorithm-module-reference/execute-python-script.md) .

![Spustit vstupní mapu Pythonu](media/how-to-designer-python/execute-python-map.png)

## <a name="execute-python-written-in-the-designer"></a>Spuštění jazyka Python napsaného v Návrháři

### <a name="add-the-execute-python-script-module"></a>Přidat modul spuštění skriptu Pythonu

1. V paletě návrháře Najděte modul **spouštění skriptu Pythonu** . Najdete ho v části **jazyk Pythonu** .

1. Přetáhněte modul na plátno kanálu.

### <a name="connect-input-datasets"></a>Připojení vstupních datových sad

Tento článek používá ukázkovou datovou sadu, **údaje o cenách automobilu (RAW)**. 

1. Přetáhněte datovou sadu na plátno kanálu.

1. Připojte výstupní port datové sady ke vstupnímu portu pro **spuštění modulu skriptu Pythonu** v levém horním rohu. Návrhář zpřístupňuje vstup jako parametr pro skript vstupního bodu.
    
    Správný vstupní port je vyhrazen pro knihovny zip Python.

    ![Připojení datových sad](media/how-to-designer-python/connect-dataset.png)
        

1. Poznamenejte si, který vstupní port používáte. Návrhář přiřadí levý vstupní port k proměnné `dataset1` a střednímu vstupnímu portu `dataset2` . 

Vstupní moduly jsou volitelné, protože data můžete generovat nebo importovat přímo v modulu **spouštění skriptu Pythonu** .

### <a name="write-your-python-code"></a>Psaní kódu Pythonu

Návrhář poskytuje počáteční skript vstupního bodu, který můžete upravit a zadat vlastní kód Pythonu. 

V tomto příkladu použijete PANDAS ke kombinování dvou sloupců, které se nacházejí v datové sadě automobilů, **Price** a **koňské síly**, k vytvoření nového sloupce na základě **počtu dolarů**. Tento sloupec představuje, kolik platíte za jednotlivé síly, což může být užitečnou funkcí pro rozhodování o tom, jestli je automobil pro peníze dobrým řešením. 

1. Vyberte modul **spouštění skriptu Pythonu** .

1. V podokně, které se zobrazí napravo od plátna, vyberte textové pole **skript Pythonu** .

1. Zkopírujte a vložte následující kód do textového pole.

    ```python
    import pandas as pd
    
    def azureml_main(dataframe1 = None, dataframe2 = None):
        dataframe1['Dollar/HP'] = dataframe1.price / dataframe1.horsepower
        return dataframe1
    ```
    Váš kanál by měl vypadat jako na následujícím obrázku:
    
    ![Spustit kanál Pythonu](media/how-to-designer-python/execute-python-pipeline.png)

    Skript vstupního bodu musí obsahovat funkci `azureml_main` . K dispozici jsou dva parametry funkcí, které se mapují na dva vstupní porty pro modul **spouštění skriptu Pythonu** .

    Vrácená hodnota musí být PANDAS dataframe. Jako výstupy modulu můžete vracet až dva datarámce.
    
1. Odešlete kanál.

Teď máte datovou sadu s novou funkcí **dolar/HP**, která by mohla být užitečná při výuce doporučení pro automobil. Toto je příklad extrakce funkcí a snížení rozměru. 

## <a name="next-steps"></a>Další kroky

Naučte se [importovat vlastní data](how-to-designer-import-data.md) v Návrháři Azure Machine Learning.