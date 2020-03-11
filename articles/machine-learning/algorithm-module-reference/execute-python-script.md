---
title: 'Spustit skript jazyka Python: odkaz na modul'
titleSuffix: Azure Machine Learning
description: Naučte se používat modul spuštění skriptu Pythonu v Azure Machine Learning ke spouštění kódu Pythonu.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 03/10/2020
ms.openlocfilehash: 52eb3bdb463389d075421661610b5ee94d14d77d
ms.sourcegitcommit: b8d0d72dfe8e26eecc42e0f2dbff9a7dd69d3116
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/10/2020
ms.locfileid: "79037081"
---
# <a name="execute-python-script-module"></a>Spustit modul Python Script

Tento článek popisuje modul v Návrháři Azure Machine Learning (Preview).

Tento modul použijte ke spuštění kódu Pythonu. Další informace o architektuře a zásadách návrhu Pythonu najdete v [následujícím článku](https://docs.microsoft.com/azure/machine-learning/machine-learning-execute-python-scripts).

Pomocí Pythonu můžete provádět úlohy, které aktuálně nejsou podporované existujícími moduly, například:

+ Vizualizace dat pomocí `matplotlib`
+ Vytvoření výčtu datových sad a modelů v pracovním prostoru pomocí knihoven Pythonu
+ Čtení, načítání a manipulace s daty ze zdrojů, které nepodporují modul [Import dat](./import-data.md)
+ Spusťte si vlastní kód hloubkového učení. 


Azure Machine Learning používá Anaconda distribuci Pythonu, která zahrnuje mnoho běžných nástrojů pro zpracování dat. Verze Anaconda se aktualizuje automaticky. Aktuální verze je:
 -  Anaconda 4.5 + distribuce pro Python 3,6 

Předem nainstalované balíčky jsou:
-  asn1crypto = = 0.24.0
- attrs==19.1.0
- Azure – běžné = = 1.1.18
- Azure-Storage-BLOB = = 1.5.0
- azure-storage-common==1.4.0
- certifi = = 2019.3.9
- cffi = = 1.12.2
- chardet = = 3.0.4
- kryptografie = = 2.6.1
- distro==1.4.0
- IDNA = = 2.8
- jsonschema==3.0.1
- lightgbm = = 2.2.3
- more-itertools==6.0.0
- numpy = = 1.16.2
- PANDAS = = 0.24.2
- Pillow = = 6.0.0
- pip==19.0.3
- pyarrow = = 0.12.1
- pycparser = = 2.19
- pycryptodomex==3.7.3
- pyrsistent = = 0.14.11
- python-dateutil==2.8.0
- pytz==2018.9
- požadavky = = 2.21.0
- scikit-učení = = 0.20.3
- scipy = = 1.2.1
- setuptools = = 40.8.0
- šest = = 1.12.0
- Torch = = 1.0.1. post2
- torchvision = = 0.2.2. post3
- urllib3 = = 1.24.1
- kolo = = 0.33.1 

 Pokud chcete nainstalovat další balíčky, které nejsou v seznamu předem nainstalovaných, například *scikit-misc*, přidejte do skriptu následující kód: 

 ```python
import os
os.system(f"pip install scikit-misc")
```

## <a name="upload-files"></a>Nahrání souborů
**Skript spouštějící Python** podporuje nahrávání souborů pomocí [Azure Machine Learning Python SDK](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py#upload-file-name--path-or-stream-).

Následující příklad ukazuje, jak nahrát soubor obrázku do modulu **spouštění skriptu Pythonu** :

```Python

# The script MUST contain a function named azureml_main
# which is the entry point for this module.

# imports up here can be used to
import pandas as pd

# The entry point function can contain up to two input arguments:
#   Param<dataframe1>: a pandas.DataFrame
#   Param<dataframe2>: a pandas.DataFrame
def azureml_main(dataframe1 = None, dataframe2 = None):

    # Execution logic goes here
    print(f'Input pandas.DataFrame #1: {dataframe1}')

    from matplotlib import pyplot as plt
    plt.plot([1, 2, 3, 4])
    plt.ylabel('some numbers')
    img_file = "line.png"
    plt.savefig(img_file)

    from azureml.core import Run
    run = Run.get_context(allow_offline=True)
    run.upload_file(f"graphics/{img_file}", img_file)

    # Return value must be of a sequence of pandas.DataFrame
    # E.g.
    #   -  Single return value: return dataframe1,
    #   -  Two return values: return dataframe1, dataframe2
    return dataframe1,
}
```

Po úspěšném odeslání kanálu můžete zobrazit náhled obrázku v pravém panelu modulu ![nahraný obrázek](media/module/upload-image-in-python-script.png)

## <a name="how-to-configure-execute-python-script"></a>Jak nakonfigurovat skript spustit v Pythonu

Modul **spuštění skriptu Pythonu** obsahuje ukázkový kód Pythonu, který můžete použít jako výchozí bod. Pokud chcete nakonfigurovat modul **spouštění skriptu Pythonu** , zadáte sadu vstupů a kódů Pythonu, které se spustí v textovém poli **skript Pythonu** .

1. Přidejte modul **spuštění skriptu Pythonu** do vašeho kanálu.

2. Přidejte a připojte se k **DataSet1.** datových sad z návrháře, který chcete použít pro vstup. Odkazujte tuto datovou sadu ve skriptu Pythonu jako **DataFrame1**.

    Použití datové sady je volitelné, pokud chcete generovat data pomocí Pythonu, nebo použít kód Pythonu k importu dat přímo do modulu.

    Tento modul podporuje přidání druhé datové sady na **Dataset2**. Odkaz na druhou datovou sadu ve skriptu Pythonu jako DataFrame2.

    Datové sady, které jsou uložené v Azure Machine Learning, se automaticky převedou na data **PANDAS** . po načtení do tohoto modulu se snímky načítají.

    ![Spustit vstupní mapu Pythonu](media/module/python-module.png)

4. Pokud chcete zahrnout nové balíčky nebo kód v Pythonu, přidejte do **svazku skriptu**soubor zip, který obsahuje tyto vlastní prostředky. **Sada prostředků skriptu** musí být soubor zip odeslaný do vašeho pracovního prostoru jako datová sada typu souboru. 

    Během provádění kanálu lze použít jakýkoli soubor obsažený v odeslaném archivu zip. Pokud archiv obsahuje adresářovou strukturu, struktura se zachová, ale musíte do cesty předřadit adresář **Src** .

5. Do textového pole **skript Pythonu** zadejte nebo vložte platný skript Pythonu.

    Textové pole **skriptu Pythonu** se předem vyplní některými pokyny v komentářích a vzorový kód pro přístup k datům a výstup. Tento kód musíte upravit nebo nahradit. Nezapomeňte dodržovat konvence Pythonu týkající se odsazení a používání velkých a malých písmen.

    + Skript musí obsahovat funkci s názvem `azureml_main` jako vstupní bod pro tento modul.
    + Funkce vstupního bodu může obsahovat až dva vstupní argumenty: `Param<dataframe1>` a `Param<dataframe2>`
    + Soubory zip připojené k třetímu vstupnímu portu jsou komprimovány a uloženy v adresáři, `.\Script Bundle`, což je také přidáno do `sys.path`Pythonu. 

    Proto pokud váš soubor zip obsahuje `mymodule.py`, importujte ho pomocí `import mymodule`.

    + Do návrháře lze vrátit dvě datové sady, které musí být sekvence typu `pandas.DataFrame`. V kódu Pythonu můžete vytvořit další výstupy a zapsat je přímo do služby Azure Storage.

6. Spusťte kanál, nebo vyberte modul a kliknutím na **Spustit vybraný** spusťte jenom skript Pythonu.

    Všechna data a kód se načtou do virtuálního počítače a spustí se pomocí zadaného prostředí Pythonu.

## <a name="results"></a>Výsledky

Výsledky jakýchkoli výpočtů provedených integrovaným kódem Pythonu musí být poskytnuty jako PANDAS. Datový rámec, který je automaticky převeden do formátu datové sady Azure Machine Learning, abyste mohli výsledky použít s jinými moduly v kanálu.

Modul vrací dvě datové sady:  
  
+ **Datová sada výsledků 1**definovaná prvním vráceným pandasm datovým rámcem ve skriptu Pythonu

+ **Výsledná datová sada 2**definovaná druhým vráceným pandasm datovým snímkem ve skriptu Pythonu


## <a name="next-steps"></a>Další kroky

Podívejte se na [sadu modulů, které jsou k dispozici](module-reference.md) pro Azure Machine Learning. 