---
title: 'Spustit skript jazyka Python: odkaz na modul'
titleSuffix: Azure Machine Learning service
description: Naučte se používat modul spuštění skriptu Pythonu ve službě Azure Machine Learning ke spouštění kódu Pythonu.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ms.openlocfilehash: ac68239c12fb284dd3cb5179b5719f3d36acbd34
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/21/2019
ms.locfileid: "72693794"
---
# <a name="execute-python-script-module"></a>Spustit modul Python Script

Tento článek popisuje modul vizuálního rozhraní (Preview) pro službu Azure Machine Learning.

Tento modul použijte ke spuštění kódu Pythonu. Další informace o architektuře a zásadách návrhu Pythonu najdete v [následujícím článku.](https://docs.microsoft.com/azure/machine-learning/machine-learning-execute-python-scripts)

Pomocí Pythonu můžete provádět úlohy, které aktuálně nejsou podporované existujícími moduly, například:

+ Vizualizace dat pomocí `matplotlib`
+ Vytvoření výčtu datových sad a modelů v pracovním prostoru pomocí knihoven Pythonu
+ Čtení, načítání a manipulace s daty ze zdrojů, které nepodporují modul [Import dat](./import-data.md)
+ Spusťte si vlastní kód hloubkového učení. 


Azure Machine Learning používá Anaconda distribuci Pythonu, která zahrnuje mnoho běžných nástrojů pro zpracování dat. Verze Anaconda se aktualizuje automaticky. Aktuální verze je:
 -  Anaconda 4.5 + distribuce pro Python 3,6 

Předem nainstalované balíčky jsou:
-  asn1crypto = = 0.24.0
- attrs = = 19.1.0
- Azure – běžné = = 1.1.18
- Azure-Storage-BLOB = = 1.5.0
- Azure-Storage-Common = = 1.4.0
- certifi = = 2019.3.9
- cffi = = 1.12.2
- chardet = = 3.0.4
- kryptografie = = 2.6.1
- distribuce = = 1.4.0
- IDNA = = 2.8
- jsonschema = = 3.0.1
- lightgbm = = 2.2.3
- more – itertools = = 6.0.0
- numpy = = 1.16.2
- PANDAS = = 0.24.2
- Pillow = = 6.0.0
- PIP = = 19.0.3
- pyarrow = = 0.12.1
- pycparser = = 2.19
- pycryptodomex = = 3.7.3
- pyrsistent = = 0.14.11
- Python – dateutil = = 2.8.0
- pytz = = 2018.9
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

## <a name="how-to-use"></a>Způsob použití

Modul **spuštění skriptu Pythonu** obsahuje ukázkový kód Pythonu, který můžete použít jako výchozí bod. Pokud chcete nakonfigurovat modul **spouštění skriptu Pythonu** , zadáte sadu vstupů a kódů Pythonu, které se spustí v textovém poli **skript Pythonu** .

1. Přidejte modul **spuštění skriptu Pythonu** do vašeho kanálu.

2. Přidejte a připojte se k **DataSet1.** datových sad z rozhraní, které chcete použít pro vstup. Odkazujte tuto datovou sadu ve skriptu Pythonu jako **DataFrame1**.

    Použití datové sady je volitelné, pokud chcete generovat data pomocí Pythonu, nebo použít kód Pythonu k importu dat přímo do modulu.

    Tento modul podporuje přidání druhé datové sady na **Dataset2**. Odkaz na druhou datovou sadu ve skriptu Pythonu jako DataFrame2.

    Datové sady, které jsou uložené v Azure Machine Learning, se automaticky převedou na data **PANDAS** . po načtení do tohoto modulu se snímky načítají.

    ![Spustit vstupní mapu Pythonu](media/module/python-module.png)

4. Pokud chcete zahrnout nové balíčky nebo kód v Pythonu, přidejte do **svazku skriptu**soubor zip, který obsahuje tyto vlastní prostředky. **Sada prostředků skriptu** musí být soubor zip, který je už nahraný do vašeho pracovního prostoru. 

    Během provádění kanálu lze použít jakýkoli soubor obsažený v odeslaném archivu zip. Pokud archiv obsahuje adresářovou strukturu, struktura se zachová, ale musíte do cesty předřadit adresář **Src** .

5. Do textového pole **skript Pythonu** zadejte nebo vložte platný skript Pythonu.

    Textové pole **skriptu Pythonu** se předem vyplní některými pokyny v komentářích a vzorový kód pro přístup k datům a výstup. **Tento kód musíte upravit nebo nahradit.** Nezapomeňte dodržovat konvence Pythonu týkající se odsazení a používání velkých a malých písmen.

    + Skript musí obsahovat funkci s názvem `azureml_main` jako vstupní bod pro tento modul.
    + Funkce vstupního bodu může obsahovat až dva vstupní argumenty: `Param<dataframe1>` a `Param<dataframe2>`
    + Soubory zip připojené k třetímu vstupnímu portu jsou komprimovány a uloženy v adresáři, `.\Script Bundle`, což je také přidáno do `sys.path` Pythonu. 

    Proto pokud váš soubor zip obsahuje `mymodule.py`, importujte ho pomocí `import mymodule`.

    + Do rozhraní se dají vracet dvě datové sady, které musí být sekvence typu `pandas.DataFrame`. V kódu Pythonu můžete vytvořit další výstupy a zapsat je přímo do služby Azure Storage.

6. Spusťte kanál, nebo vyberte modul a kliknutím na **Spustit vybraný** spusťte jenom skript Pythonu.

    Všechna data a kód se načtou do virtuálního počítače a spustí se pomocí zadaného prostředí Pythonu.

## <a name="results"></a>Výsledky

Výsledky jakýchkoli výpočtů provedených integrovaným kódem Pythonu musí být poskytnuty jako PANDAS. Datový rámec, který je automaticky převeden do formátu datové sady Azure Machine Learning, abyste mohli výsledky použít s jinými moduly v kanálu.

Modul vrací dvě datové sady:  
  
+ **Datová sada výsledků 1**definovaná prvním vráceným pandasm datovým rámcem ve skriptu Pythonu

+ **Výsledná datová sada 2**definovaná druhým vráceným pandasm datovým snímkem ve skriptu Pythonu


## <a name="next-steps"></a>Další kroky

Podívejte se na [sadu modulů, které jsou k dispozici](module-reference.md) pro Azure Machine Learning služby. 