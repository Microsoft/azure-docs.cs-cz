---
title: Podporované jazyky
titleSuffix: Azure Data Science Virtual Machine
description: Podporované jazyky programů a související nástroje předinstalované ve virtuálním počítači pro datové vědy.
keywords: nástroje pro datové vědy, virtuální počítač pro datové vědy, datové vědy pro linux
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: lobrien
ms.author: laobri
ms.topic: conceptual
ms.date: 12/12/2019
ms.openlocfilehash: e7b32579712e89c0d5595303ee7e03d8b2462607
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79283651"
---
# <a name="languages-supported-on-the-data-science-virtual-machine"></a>Jazyky podporované ve virtuálním počítači pro datové vědy 

Virtuální počítač pro datové vědy (DSVM) je dodáván s několika předem vytvořenými jazyky a vývojovými nástroji pro vytváření aplikací umělé inteligence (AI). Zde jsou některé z pozoruhodných.

## <a name="python-windows-server-2016-edition"></a>Python (edice Windows Server 2016)

|    |           |
| ------------- | ------------- |
| Podporované jazykové verze | Python 2.7 a 3.7 |
| Podporované edice DSVM      | Windows Server 2016     |
| Jak je konfigurován / nainstalován na DSVM?  | Jsou `conda` vytvořena dvě globální prostředí: <br /> * `root` Prostředí se `/anaconda/` nachází na pythonu 3.7. <br/> * `python2` Prostředí se `/anaconda/envs/python2` nachází na python2.7.       |
| Odkazy na ukázky      | Ukázkové poznámkové bloky Jupyter pro Python jsou zahrnuty.     |
| Související nástroje na DSVM      | PySpark, R, Julia.      |

> [!NOTE]
> Sestavení Systému Windows Server 2016, která byla vytvořena před březnem 2018, obsahují Python 3.5 a Python 2.7. Python 2.7 je **kořenové** prostředí conda a **py37** je prostředí Pythonu 3.7.

### <a name="how-to-use-and-run-it"></a>Jak ji používat a spouštět    

* Spustit na příkazovém řádku:

  Otevřete příkazový řádek a v závislosti na verzi Pythonu, kterou chcete spustit, použijte jednu z následujících metod:

    ```
    # To run Python 2.7
    activate python2
    python --version
    
    # To run Python 3.7
    activate 
    python --version 
    ```
    
* Použití v ide:

  Použijte nástroje Pythonu pro Visual Studio (PTVS), nainstalované v edici Visual Studio Community. Ve výchozím nastavení je jediným prostředím, které je nastaveno automaticky v PTVS, Python 3.6. 

    > [!NOTE]
    > Chcete-li bod PTVS na Python 2.7, musíte vytvořit vlastní prostředí v PTVS. Chcete-li nastavit tuto cestu prostředí v edici Visual Studio Community Edition, přejděte na **Tools** -> **Python Tools** -> **Python Environments** a vyberte **+ Custom**. Potom nastavte umístění na **c:\anaconda\envs\python2** a vyberte **automaticky rozpoznat**.

* Použití v Jupyteru:

  Otevřete Jupyter a vyberte **Nový,** abyste vytvořili nový poznámkový blok. Typ jádra můžete nastavit jako _Python [Conda Root]_ pro Python 3.7 a _Python [Conda env:python2]_ pro Python 2.7.

* Instalace balíčků Pythonu:

  Výchozí prostředí Pythonu na DSVM jsou globální prostředí, která jsou čitelná pro všechny uživatele. Globální balíčky však mohou psát a instalovat pouze správci. Chcete-li nainstalovat balíčky do globálního prostředí, aktivujte `activate` do kořenového prostředí nebo pythonu2 pomocí příkazu jako správce. Potom můžete použít správce balíčků, jako `conda` je nebo `pip` nainstalovat nebo aktualizovat balíčky.

## <a name="python-linux-edition"></a>Python (linuxová edice)

|    |           |
| ------------- | ------------- |
| Podporované jazykové verze | Python 2.7 a 3.5 |
| Podporované edice DSVM      | Linux   |
| Jak je konfigurován / nainstalován na DSVM?  | Jsou `conda` vytvořena dvě globální prostředí: <br /> * `root`prostředí se `/anaconda/` nachází na je Python 2.7. <br/> * `py35`prostředí se `/anaconda/envs/py35`nachází na je Python 3.5.       |
| Odkazy na ukázky      | Ukázkové poznámkové bloky Jupyter pro Python jsou zahrnuty.     |
| Související nástroje na DSVM      | PySpark, R, Julia      |
### <a name="how-to-use-and-run-it"></a>Jak ji používat a spouštět    

* Běh v terminálu:

  Otevřete terminál a udělejte jednu z následujících akcí v závislosti na verzi Pythonu, kterou chcete spustit:

    ```
    # To run Python 2.7
    source activate 
    python --version
    
    # To run Python 3.5
    source activate py35
    python --version
    
    ```
* Použití v ide:

  Použijte PyCharm, nainstalovaný v edici Visual Studio Community. 

* Použití v Jupyteru:

  Otevřete Jupyter a vyberte **Nový,** abyste vytvořili nový poznámkový blok. Typ jádra můžete nastavit jako **Python [Conda Root]** pro Python 2.7 a **Python [Conda env:py35]** pro prostředí Pythonu 3.5. 

* Instalace balíčků Pythonu:

  Výchozí prostředí Pythonu na DSVM jsou globální prostředí čitelná všemi uživateli. Globální balíčky však mohou psát a instalovat pouze správci. Chcete-li nainstalovat balíčky do globálního prostředí, aktivujte do `source activate` kořenového prostředí nebo prostředí py35 pomocí příkazu jako správce nebo jako uživatel s oprávněními sudo. Potom můžete použít správce balíčků, jako `conda` je nebo `pip` nainstalovat nebo aktualizovat balíčky.


## <a name="r"></a>R

|    |           |
| ------------- | ------------- |
| Podporované jazykové verze | Microsoft R Open 3.x (100% kompatibilní s CRAN-R)<br /> Microsoft R Server 9.x Developer Edition (škálovatelná platforma R připravená pro podniky)|
| Podporované edice DSVM      | Linux, Windows     |
| Jak je konfigurován / nainstalován na DSVM?  | Windows:`C:\Program Files\Microsoft\ML Server\R_SERVER` <br />Linux:`/usr/lib64/microsoft-r/3.3/lib64/R`    |
| Odkazy na ukázky      | Ukázkové notebooky Jupyter pro R jsou zahrnuty.     |
| Související nástroje na DSVM      | SparkR, Python, Julia      |
### <a name="how-to-use-and-run-it"></a>Jak ji používat a spouštět    

**Windows**:

* Spustit na příkazovém řádku:

  Otevřete příkazový řádek a zadejte příkaz `R`.

* Použití v ide:

  Použijte Nástroje RTools pro Visual Studio (RTVS) nainstalované v edici Visual Studio Community nebo RStudio. Ty jsou k dispozici v nabídce Start nebo jako ikona na ploše. 

* Použití v Jupyteru

  Otevřete Jupyter a vyberte **Nový,** abyste vytvořili nový poznámkový blok. Můžete nastavit typ jádra jako **R** pro použití jádra Jupyter R (IRKernel).

* Instalace balíčků R:

  R je nainstalován na DSVM v globálním prostředí, které je čitelné pro všechny uživatele. Globální balíčky však mohou psát a instalovat pouze správci. Chcete-li nainstalovat balíčky do globálního prostředí, spusťte R pomocí jedné z předchozích metod. Potom můžete spustit Správce `install.packages()` balíčků R k instalaci nebo aktualizaci balíčků.

**Linux**:

* Běh v terminálu:

  Otevřete terminál `R`a spusťte .  

* Použití v ide:

  Použijte RStudio, nainstalované na Linux U DSVM.  

* Použití v Jupyteru:

  Otevřete Jupyter a vyberte **Nový,** abyste vytvořili nový poznámkový blok. Můžete nastavit typ jádra jako **R** pro použití jádra Jupyter R (IRKernel). 

* Instalace balíčků R:

  R je nainstalován na DSVM v globálním prostředí, které je čitelné pro všechny uživatele. Globální balíčky však mohou psát a instalovat pouze správci. Chcete-li nainstalovat balíčky do globálního prostředí, spusťte R pomocí jedné z předchozích metod. Potom můžete spustit Správce `install.packages()` balíčků R k instalaci nebo aktualizaci balíčků.


## <a name="julia"></a>Julia

|    |           |
| ------------- | ------------- |
| Podporované jazykové verze | 0.6 |
| Podporované edice DSVM      | Linux, Windows     |
| Jak je konfigurován / nainstalován na DSVM?  | Windows: Instalováno na`C:\JuliaPro-VERSION`<br /> Linux: Instalováno na`/opt/JuliaPro-VERSION`    |
| Odkazy na ukázky      | Ukázkové poznámkové bloky Jupyter pro Julii jsou zahrnuty.     |
| Související nástroje na DSVM      | Python, R      |
### <a name="how-to-use-and-run-it"></a>Jak ji používat a spouštět    

**Windows**:

* Spustit na příkazovém řádku

  Otevřete příkazový `julia`řádek a spusťte .
* Použití v ide:

  Použijte `Juno` s Julia IDE nainstalován na DSVM a k dispozici jako zástupce na ploše.

* Použití v Jupyteru:

  Otevřete Jupyter a vyberte **Nový,** abyste vytvořili nový poznámkový blok. Typ jádra můžete nastavit jako **Julia VERSION**.

* Nainstalujte balíčky Julia:

  Výchozí umístění Julia je globální prostředí, které je čitelné pro všechny uživatele. Globální balíčky však mohou psát a instalovat pouze správci. Chcete-li nainstalovat balíčky do globálního prostředí, spusťte Julii pomocí jedné z předchozích metod. Potom můžete spustit příkazy správce `Pkg.add()` balíčků Julia, jako je instalace nebo aktualizace balíčků.


**Linux**:
* Běh v terminálu:

  Otevřete terminál `julia`a spusťte .
* Použití v ide:

  Použití `Juno`s julia ide nainstalován na DSVM a k dispozici jako zástupce nabídky **aplikace.**

* Použití v Jupyteru:

  Otevřete Jupyter a vyberte **Nový,** abyste vytvořili nový poznámkový blok. Typ jádra můžete nastavit jako **Julia VERSION**.

* Nainstalujte balíčky Julia:

  Výchozí umístění Julia je globální prostředí, které je čitelné pro všechny uživatele. Globální balíčky však mohou psát a instalovat pouze správci. Chcete-li nainstalovat balíčky do globálního prostředí, spusťte Julii pomocí jedné z předchozích metod. Potom můžete spustit příkazy správce `Pkg.add()` balíčků Julia, jako je instalace nebo aktualizace balíčků.

## <a name="other-languages"></a>Další jazyky

**C#**: K dispozici v systému Windows a `Developer Command Prompt for Visual Studio`přístupné prostřednictvím `csc` edice Visual Studio Community nebo na , kde můžete spustit příkaz.

**Java**: OpenJDK je k dispozici jak na Linuxu a Windows edice DSVM a je nastaven na cestě. Chcete-li použít `javac` Javu, zadejte příkaz nebo `java` příkaz na příkazovém řádku v systému Windows nebo na bash shellu v Linuxu.

**Node.js**: Node.js je k dispozici na linuxové i windowseded DSVM a je nastaven na cestě. Chcete-li získat přístup k `node` souboru Node.js, zadejte příkaz nebo `npm` příkaz na příkazovém řádku v systému Windows nebo v prostředí bash v Linuxu. V systému Windows je nainstalováno rozšíření sady Visual Studio pro nástroje Node.js, které poskytuje grafické prostředí IDE pro vývoj aplikace Node.js.

**F#**: K dispozici v systému Windows a `Developer Command Prompt for Visual Studio`přístupné prostřednictvím `fsc` edice Visual Studio Community nebo na , kde můžete spustit příkaz.
