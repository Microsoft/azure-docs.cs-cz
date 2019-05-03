---
title: 'Spusťte skript Pythonu: Odkaz na modul'
titleSuffix: Azure Machine Learning service
description: Další informace o použití modulu Execute Python Script ve službě Azure Machine Learning pro spuštění kódu Pythonu.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 6e61ed5a18e69b107b78bf2042de21d14cd6beb5
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/02/2019
ms.locfileid: "65029127"
---
# <a name="execute-python-script-module"></a>Spusťte modul skriptu Pythonu

Tento článek popisuje modulu rozhraní visual (preview) pro službu Azure Machine Learning.

Tento modul slouží ke spuštění kódu Pythonu. Další informace o architektuře a designu Principy jazyka Python, naleznete v tématu [v následujícím článku.](https://docs.microsoft.com/azure/machine-learning/machine-learning-execute-python-scripts)

S využitím Pythonu můžete provádět úlohy, které nejsou aktuálně podporovány existující moduly, například:

+ Vizualizace dat pomocí `matplotlib`
+ Zobrazení výčtu datových sad a modelů v pracovním prostoru pomocí knihovny jazyka Python
+ Čtení, načítání a manipulace s daty ze zdroje nejsou podporované [Import dat](./import-data.md) modulu
+ Spuštění vlastní hloubkového učení kódu 


Azure Machine Learning používá distribuci Anaconda Python, který obsahuje řadu běžných nástrojů pro zpracování dat. Anaconda verze budeme aktualizovat automaticky. Aktuální verze je:
 -  Anaconda 4.5 + distribuce pro Python 3.6 

Předinstalované balíčky jsou:
-  asn1crypto==0.24.0
- attrs==19.1.0
- Azure běžné == 1.1.18
- Azure storage blob == 1.5.0
- azure-storage-common==1.4.0
- certifi==2019.3.9
- cffi == 1.12.2
- chardet==3.0.4
- cryptography==2.6.1
- distro==1.4.0
- idna==2.8
- jsonschema==3.0.1
- lightgbm==2.2.3
- more-itertools==6.0.0
- numpy == 1.16.2
- pandas==0.24.2
- Poduškový == 6.0.0
- pip==19.0.3
- pyarrow == 0.12.1
- pycparser == 2.19
- pycryptodomex==3.7.3
- pyrsistent==0.14.11
- python-dateutil==2.8.0
- pytz==2018.9
- požadavky == 2.21.0
- scikit-informace == 0.20.3
- scipy==1.2.1
- setuptools == 40.8.0
- šest == 1.12.0
- torch==1.0.1.post2
- torchvision==0.2.2.post3
- urllib3 == 1.24.1
- kolečko == 0.33.1 

 K instalaci dalších balíčků není v seznamu předinstalované například *scikit různé*, přidejte následující kód do skriptu: 

 ```python
import os
os.system(f"pip install scikit-misc")
```

## <a name="how-to-use"></a>Způsob použití

**Execute Python Script** modul obsahuje ukázkový kód Pythonu, který můžete použít jako výchozí bod. Ke konfiguraci **Execute Python Script** modulu, poskytují sadu vstupů a spuštění v kódu Pythonu **skript Pythonu** textového pole.

1. Přidat **Execute Python Script** modulu do experimentu.

2. Přidání a připojení na **Dataset1** žádné datové sady z rozhraní, které chcete použít pro vstup. Odkazovat na tuto datovou sadu ve svém skriptu Pythonu jako **DataFrame1**.

    Použití datové sady je volitelné, pokud chcete vytvořit data s využitím Pythonu, nebo můžete importovat data přímo do modulu kódu Pythonu.

    Tento modul podporuje přidání druhé datové sady na **Dataset2**. Odkaz na druhý datovou sadu ve svém skriptu Pythonu jako DataFrame2.

    Datové sady uložené ve službě Azure Machine Learning se automaticky převedou na **pandas** data.frames při načtení pomocí tohoto modulu.

    ![Spuštění mapy vstupní Pythonu](media/module/python-module.png)

4. Aby byly nové balíčky Pythonu nebo kódu, přidejte komprimovaný soubor obsahující tyto vlastní prostředky na **skript sady**. Vstup do **skript sady** musí být soubor již odeslány do pracovního prostoru. 

    Všechny soubory obsažené v odeslané komprimovaný archiv je možné během provádění testu. Pokud archiv obsahuje adresářovou strukturu, struktura se zachovají, ale musí předřaďte adresář s názvem **src** k cestě.

5. V **skript Pythonu** textového pole zadejte nebo vložte platný skript Pythonu.

    **Skript Pythonu** textové pole je předem vyplněna pomocí některé pokyny v komentáře a ukázky kódu pro přístup k datům a výstup. **Musíte upravit ani nahradit tento kód.** Ujistěte se, že odpovídají konvencím Python o odsazení a malých a velkých písmen.

    + Skript může obsahovat funkci s názvem `azureml_main` jako vstupní bod pro tento modul.
    + Funkci vstupního bodu může obsahovat až dva vstupní argumenty: `Param<dataframe1>` a `Param<dataframe2>`
    + Komprimovaný soubory připojené ke vstupnímu portu třetí odblokujte a uloženy v adresáři, `.\Script Bundle`, který je taky přidaný ke Pythonu `sys.path`. 

    Proto pokud soubor zip obsahuje `mymodule.py`, importujte ho pomocí `import mymodule`.

    + Dvě datové sady mohou být vráceny do rozhraní, které musí být posloupnost typ `pandas.DataFrame`. Můžete vytvořit další výstupy v kódu Pythonu a zapisovat přímo do úložiště Azure.

6. Spusťte experiment, nebo vyberte modul a klikněte na tlačítko **spustit vybrané** spustit skript Pythonu.

    Všechna data a kód je načteno do virtuálního počítače a spouštět pomocí zadaného prostředí Pythonu.

## <a name="results"></a>Výsledky

Výsledky všechny výpočty se provádí pomocí vloženého kódu Python musí být uvedeny jako balíčky pandas. Datový rámec, který je automaticky převedena na formát datové sady Azure Machine Learning, tak, aby výsledky můžete používat s ostatními moduly do experimentu.

V modulu vrátí dvě datové sady:  
  
+ **Výsledky 1 datovou sadu**, určené první vrácené pandas dataframe ve skriptu Pythonu

+ **Dojít k datové sadě 2**, určené druhý vrácené pandas dataframe ve skriptu Pythonu


## <a name="next-steps"></a>Další postup

Zobrazit [sada modulů, které jsou k dispozici](module-reference.md) do služby Azure Machine Learning. 