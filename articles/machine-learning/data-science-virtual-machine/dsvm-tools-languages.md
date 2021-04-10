---
title: Podporované jazyky
titleSuffix: Azure Data Science Virtual Machine
description: Podporované jazyky programu a související nástroje, které jsou předem nainstalované v Data Science Virtual Machine.
keywords: nástroje pro datové vědy, virtuální počítač pro datové vědy, datové vědy pro linux
services: machine-learning
ms.service: data-science-vm
ms.custom: devx-track-python
author: lobrien
ms.author: laobri
ms.topic: conceptual
ms.date: 12/12/2019
ms.openlocfilehash: 42110eb896a751080044247932770f37617174c9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "100516426"
---
# <a name="languages-supported-on-the-data-science-virtual-machine"></a>Jazyky podporované v Data Science Virtual Machine 

Data Science Virtual Machine (DSVM) obsahuje několik předem připravených jazyků a vývojářských nástrojů pro vytváření aplikací pro umělou Intelligence (AI). Tady jsou některé z významných.

## <a name="python-windows-server-2016-edition"></a>Python (edice Windows serveru 2016)

| Kategorie | Hodnota |
| ------------- | ------------- |
| Podporované jazykové verze | Python 2,7 a 3,7 |
| Podporované edice DSVM      | Windows Server 2016     |
| Jak je nakonfigurovaná nebo nainstalovaná na DSVM?  | `conda`Vytvoří se dvě globální prostředí: <br /> * `root` Prostředí v umístění `/anaconda/` je Python 3,7. <br/> * `python2` Prostředí v umístění `/anaconda/envs/python2` je Python 2,7.       |
| Odkazy na ukázky      | Zahrnuje ukázky Jupyter poznámkových bloků pro Python.     |
| Související nástroje na DSVM      | PySpark, R, Helena.      |

> [!NOTE]
> Sestavení systému Windows Server 2016 vytvořená před březen 2018 obsahují Python 3,5 a Python 2,7. Python 2,7 je conda **kořenové** prostředí a **Py37** je prostředí Python 3,7.

### <a name="how-to-use-and-run-it"></a>Jak použít a spustit    

* Spusťte příkaz na příkazovém řádku:

  Otevřete příkazový řádek a v závislosti na verzi Pythonu, kterou chcete spustit, použijte jednu z následujících metod:

    ```
    # To run Python 2.7
    activate python2
    python --version
    
    # To run Python 3.7
    activate 
    python --version 
    ```
    
* Použití v rozhraní IDE:

  Použijte Python Tools for Visual Studio (PTVS), které jsou nainstalované v edici Visual Studio Community Edition. Ve výchozím nastavení je jediným prostředím nastaveným automaticky v PTVS Python 3,6. 

    > [!NOTE]
    > Pokud chcete PTVS v Pythonu 2,7, musíte v PTVS vytvořit vlastní prostředí. Pokud chcete nastavit tuto cestu prostředí v edici Visual Studio Community Edition, navštivte **nástroje**  ->  **Python Tools**  ->  **Python prostředí** a vyberte **+ vlastní**. Pak nastavte umístění na **c:\anaconda\envs\python2** a vyberte **Automatické rozpoznávání**.

* Použít v Jupyter:

  Otevřete Jupyter a vyberte **Nový** pro vytvoření nového poznámkového bloku. Typ jádra můžete nastavit jako _Python [conda root]_ pro Python 3,7 a _Python [conda ENV: Python2]_ pro Python 2,7.

* Instalovat balíčky Pythonu:

  Výchozí prostředí Pythonu v DSVM jsou globální prostředí, která jsou čitelná pro všechny uživatele. Ale jenom správci můžou zapisovat a instalovat globální balíčky. Pokud chcete balíčky nainstalovat do globálního prostředí, aktivujte ji pomocí příkazu jako správce v kořenovém nebo python2 prostředí `activate` . Pak můžete použít Správce balíčků jako `conda` nebo `pip` k instalaci nebo aktualizaci balíčků.

## <a name="python-linux-edition"></a>Python (edice Linux)

| Kategorie | Hodnota |
| ------------- | ------------- |
| Podporované jazykové verze | Python 2,7 a 3,5 |
| Podporované edice DSVM      | Linux   |
| Jak je nakonfigurovaná nebo nainstalovaná na DSVM?  | `conda`Vytvoří se dvě globální prostředí: <br /> * `root` prostředí umístěné na adrese `/anaconda/` je Python 2,7. <br/> * `py35` prostředí umístěné na adrese `/anaconda/envs/py35` je Python 3,5.       |
| Odkazy na ukázky      | Zahrnuje ukázky Jupyter poznámkových bloků pro Python.     |
| Související nástroje na DSVM      | PySpark, R, Helena      |

### <a name="how-to-use-and-run-it"></a>Jak použít a spustit    

* Spusťte v terminálu:

  Otevřete terminál a proveďte jednu z následujících akcí v závislosti na verzi Pythonu, kterou chcete spustit:

    ```
    # To run Python 2.7
    source activate 
    python --version
    
    # To run Python 3.5
    source activate py35
    python --version
    
    ```
* Použití v rozhraní IDE:

  Použijte PyCharm, nainstalovaný v edici Visual Studio Community Edition. 

* Použít v Jupyter:

  Otevřete Jupyter a vyberte **Nový** pro vytvoření nového poznámkového bloku. Typ jádra můžete nastavit jako **Python [conda root]** pro Python 2,7 a **Python [conda ENV: py35]** pro prostředí Python 3,5. 

* Instalovat balíčky Pythonu:

  Výchozí prostředí Pythonu v DSVM jsou globální prostředí, která jsou čitelná pro všechny uživatele. Ale jenom správci můžou zapisovat a instalovat globální balíčky. Pokud chcete balíčky nainstalovat do globálního prostředí, aktivujte ji pomocí `source activate` příkazu jako správce nebo jako uživatel s oprávněním sudo. Pak můžete použít Správce balíčků jako `conda` nebo `pip` k instalaci nebo aktualizaci balíčků.


## <a name="r"></a>R

| Kategorie | Hodnota |
| ------------- | ------------- |
| Podporované jazykové verze | Microsoft R Open 3. x (100% kompatibilní s CRAN-R)<br /> Microsoft R Server 9. x Developer Edition (škálovatelná platforma R připravená pro podniky)|
| Podporované edice DSVM      | Linux, Windows     |
| Jak je nakonfigurovaná nebo nainstalovaná na DSVM?  | Windows: `C:\Program Files\Microsoft\ML Server\R_SERVER` <br />Linux: `/usr/lib64/microsoft-r/3.3/lib64/R`    |
| Odkazy na ukázky      | Zahrnuje ukázky Jupyter poznámkových bloků pro R.     |
| Související nástroje na DSVM      | Spark, Python, Helena      |

### <a name="how-to-use-and-run-it"></a>Jak použít a spustit    

**Windows**:

* Spusťte příkaz na příkazovém řádku:

  Otevřete příkazový řádek a zadejte příkaz `R`.

* Použití v rozhraní IDE:

  Použijte RTools for Visual Studio (RTVS), která je nainstalovaná v edici Visual Studio Community Edition nebo RStudio. Jsou k dispozici v nabídce Start nebo jako ikona na ploše. 

* Použití v Jupyter

  Otevřete Jupyter a vyberte **Nový** pro vytvoření nového poznámkového bloku. Pokud chcete použít jádro Jupyter R (IRKernel), můžete nastavit typ jádra na **r** .

* Nainstalovat balíčky R:

  Jazyk R je nainstalovaný na DSVM v globálním prostředí, které je čitelné pro všechny uživatele. Ale jenom správci můžou zapisovat a instalovat globální balíčky. Chcete-li nainstalovat balíčky do globálního prostředí, spusťte R pomocí jedné z výše uvedených metod. Pak můžete spustit Správce balíčků R `install.packages()` a nainstalovat nebo aktualizovat balíčky.

**Linux**:

* Spustit v terminálu:

  Otevřete terminál a spusťte příkaz `R` .  

* Použití v rozhraní IDE:

  Použijte RStudio, který je nainstalovaný na Linux DSVM.  

* Použít v Jupyter:

  Otevřete Jupyter a vyberte **Nový** pro vytvoření nového poznámkového bloku. Pokud chcete použít jádro Jupyter R (IRKernel), můžete nastavit typ jádra na **r** . 

* Nainstalovat balíčky R:

  Jazyk R je nainstalovaný na DSVM v globálním prostředí, které je čitelné pro všechny uživatele. Ale jenom správci můžou zapisovat a instalovat globální balíčky. Chcete-li nainstalovat balíčky do globálního prostředí, spusťte R pomocí jedné z výše uvedených metod. Pak můžete spustit Správce balíčků R `install.packages()` a nainstalovat nebo aktualizovat balíčky.


## <a name="julia"></a>Julia

| Kategorie | Hodnota |
| ------------- | ------------- |
| Podporované jazykové verze | 0.6 |
| Podporované edice DSVM      | Linux, Windows     |
| Jak je nakonfigurovaná nebo nainstalovaná na DSVM?  | Windows: nainstalováno na `C:\JuliaPro-VERSION`<br /> Linux: nainstalováno na `/opt/JuliaPro-VERSION`    |
| Odkazy na ukázky      | Zahrnuje ukázky Jupyter poznámkových bloků pro Helena.     |
| Související nástroje na DSVM      | Python, R      |

### <a name="how-to-use-and-run-it"></a>Jak použít a spustit    

**Windows**:

* Spustit na příkazovém řádku

  Otevřete příkazový řádek a spusťte příkaz `julia` .
* Použití v rozhraní IDE:

  Použijte `Juno` s prostředím Helena, které je nainstalované na DSVM a je dostupné jako zástupce na ploše.

* Použít v Jupyter:

  Otevřete Jupyter a vyberte **Nový** pro vytvoření nového poznámkového bloku. Typ jádra můžete nastavit jako **Helena verze**.

* Instalovat balíčky Helena:

  Výchozí umístění Helena je globální prostředí, které je čitelné pro všechny uživatele. Ale jenom správci můžou zapisovat a instalovat globální balíčky. Pro instalaci balíčků do globálního prostředí spusťte Helena pomocí jedné z předchozích metod. Pak můžete spustit příkazy správce balíčků Helena, jako je `Pkg.add()` instalace nebo aktualizace balíčků.


**Linux**:
* Spusťte v terminálu:

  Otevřete terminál a spusťte příkaz `julia` .
* Použití v rozhraní IDE:

  Použijte `Juno` , s použitím rozhraní IDE pro Helena nainstalovaného na DSVM a dostupných jako zástupce nabídky **aplikace** .

* Použít v Jupyter:

  Otevřete Jupyter a vyberte **Nový** pro vytvoření nového poznámkového bloku. Typ jádra můžete nastavit jako **Helena verze**.

* Instalovat balíčky Helena:

  Výchozí umístění Helena je globální prostředí, které je čitelné pro všechny uživatele. Ale jenom správci můžou zapisovat a instalovat globální balíčky. Pro instalaci balíčků do globálního prostředí spusťte Helena pomocí jedné z předchozích metod. Pak můžete spustit příkazy správce balíčků Helena, jako je `Pkg.add()` instalace nebo aktualizace balíčků.

## <a name="other-languages"></a>Další jazyky

**C#**: je k dispozici ve Windows a přístupný prostřednictvím sady Visual Studio Community Edition nebo v `Developer Command Prompt for Visual Studio` , kde můžete spustit `csc` příkaz.

**Java**: OpenJDK je k dispozici v edicích systému Linux i Windows pro DSVM a je nastavena v cestě. Chcete-li použít jazyk Java, zadejte `javac` příkaz nebo na příkazovém `java` řádku ve Windows nebo v prostředí bash v systému Linux.

**Node.js**: Node.js je k dispozici v edicích systému Linux i Windows pro DSVM a je nastavena v cestě. Pokud chcete získat přístup k Node.js, zadejte `node` příkaz nebo na příkazovém `npm` řádku ve Windows nebo v prostředí bash v systému Linux. V systému Windows je nainstalováno rozšíření sady Visual Studio pro nástroje pro Node.js, které poskytuje grafické rozhraní IDE pro vývoj Node.js aplikace.

**F #**: je k dispozici ve Windows a přístupný prostřednictvím sady Visual Studio Community Edition nebo na `Developer Command Prompt for Visual Studio` , kde můžete spustit `fsc` příkaz.
