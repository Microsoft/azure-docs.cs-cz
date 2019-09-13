---
title: Podporované jazyky
titleSuffix: Azure Data Science Virtual Machine
description: Podporované jazyky programu a související nástroje, které jsou předem nainstalované v Data Science Virtual Machine.
keywords: nástroje pro datové vědy, virtuální počítač pro datové vědy, datové vědy pro linux
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: vijetajo
ms.author: vijetaj
ms.topic: conceptual
ms.date: 09/11/2017
ms.openlocfilehash: 0cedc9ede43d18d0b94b8a516170db53e3a27910
ms.sourcegitcommit: 7c5a2a3068e5330b77f3c6738d6de1e03d3c3b7d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/11/2019
ms.locfileid: "70885628"
---
# <a name="languages-supported-on-the-data-science-virtual-machine"></a>Jazyky podporované na virtuální počítač pro datové vědy 

Data Science Virtual Machine (DSVM) obsahuje několik předem připravených jazyků a vývojářských nástrojů pro vytváření aplikací pro umělou Intelligence (AI). Tady jsou některé z významných.

## <a name="python-windows-server-2016-edition"></a>Python (edice Windows serveru 2016)

|    |           |
| ------------- | ------------- |
| Podporované jazykové verze | Python 2,7 a 3,6 |
| Podporované edice DSVM      | Windows Server 2016     |
| Jak ho nakonfigurovat či nainstalovaná na datové VĚDY?  | Vytvoří se `conda` dvě globální prostředí: <br /> * Prostředí v `/anaconda/` umístění je Python 3,6. `root` <br/> * Prostředí v `/anaconda/envs/python2` umístění je Python 2,7. `python2`       |
| Odkazy na ukázky      | Zahrnuje ukázky Jupyter poznámkových bloků pro Python.     |
| Související nástroje na DSVM      | PySpark, R, Helena.      |

> [!NOTE]
> Sestavení systému Windows Server 2016 vytvořená před březen 2018 obsahují Python 3,5 a Python 2,7. Python 2,7 je conda **kořenové** prostředí a **Py35** je prostředí Python 3,5.

### <a name="how-to-use-and-run-it"></a>Jak použít a spustit    

* Spusťte příkaz na příkazovém řádku:

  Otevřete příkazový řádek a v závislosti na verzi Pythonu, kterou chcete spustit, použijte jednu z následujících metod:

    ```
    # To run Python 2.7
    activate python2
    python --version
    
    # To run Python 3.6
    activate 
    python --version 
    ```
    
* Použití v rozhraní IDE:

  Použijte Python Tools for Visual Studio (PTVS), které jsou nainstalované v edici Visual Studio Community Edition. Ve výchozím nastavení je jediným prostředím nastaveným automaticky v PTVS Python 3,6. 

    > [!NOTE]
    > Pokud chcete PTVS v Pythonu 2,7, musíte v PTVS vytvořit vlastní prostředí. Pokud chcete nastavit tuto cestu prostředí v edici Visual Studio Community Edition, navštivte **nástroje** -> **Python Tools** -> **Python prostředí** a vyberte **+ vlastní**. Pak nastavte umístění na **c:\anaconda\envs\python2** a vyberte **Automatické rozpoznávání**.

* Použít v Jupyter:

  Otevřete Jupyter a vyberte **Nový** pro vytvoření nového poznámkového bloku. Typ jádra můžete nastavit jako _Python [conda root]_ pro Python 3,6 a _Python [conda ENV: Python2]_ pro Python 2,7.

* Instalovat balíčky Pythonu:

  Výchozí prostředí Pythonu v DSVM jsou globální prostředí, která jsou čitelná pro všechny uživatele. Ale jenom správci můžou zapisovat a instalovat globální balíčky. Pokud chcete balíčky nainstalovat do globálního prostředí, aktivujte ji pomocí `activate` příkazu jako správce v kořenovém nebo python2 prostředí. Pak můžete použít Správce balíčků jako `conda` nebo `pip` k instalaci nebo aktualizaci balíčků.

## <a name="python-linux-and-windows-server-2012-edition"></a>Python (edice Linux a Windows Server 2012)

|    |           |
| ------------- | ------------- |
| Podporované jazykové verze | Python 2,7 a 3,5 |
| Podporované edice DSVM      | Linux, Windows Server 2012    |
| Jak ho nakonfigurovat či nainstalovaná na datové VĚDY?  | Vytvoří se `conda` dvě globální prostředí: <br /> * `root`prostředí umístěné na `/anaconda/` adrese je Python 2,7. <br/> * `py35`prostředí umístěné na `/anaconda/envs/py35`adrese je Python 3,5.       |
| Odkazy na ukázky      | Zahrnuje ukázky Jupyter poznámkových bloků pro Python.     |
| Související nástroje na DSVM      | PySpark, R, Julia      |
### <a name="how-to-use-and-run-it"></a>Jak použít a spustit    

**Linux**
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

  Výchozí prostředí Pythonu na datové VĚDY jsou globální prostředí číst všichni uživatelé. Ale jenom správci můžou zapisovat a instalovat globální balíčky. Pokud chcete balíčky nainstalovat do globálního prostředí, aktivujte ji pomocí `source activate` příkazu jako správce nebo jako uživatel s oprávněním sudo. Pak můžete použít Správce balíčků jako `conda` nebo `pip` k instalaci nebo aktualizaci balíčků.

**Windows 2012**
* Spusťte příkaz na příkazovém řádku:

  Otevřete příkazový řádek a proveďte jednu z následujících akcí v závislosti na verzi Pythonu, kterou chcete spustit:

     ```
    # To run Python 2.7
    activate 
    python --version
    
    # To run Python 3.5
    activate py35
    python --version
    
    ```
* Použití v rozhraní IDE:

  Použití nástrojů Python pro Visual Studio (PTVS) nainstalované v aplikaci Visual Studio Community edition. Jediné prostředí, které je nastavené automaticky v PTVS, je Python 2,7.
    > [!NOTE]
    > K PTVS v Pythonu 3,5 je nutné vytvořit vlastní prostředí v PTVS. Pokud chcete nastavit tuto cestu prostředí v edici Visual Studio Community Edition, navštivte **nástroje** -> **Python Tools** -> **Python prostředí** a vyberte **+ vlastní**. Pak nastavte umístění na `c:\anaconda\envs\py35` a vyberte _Automatické rozpoznávání_.

* Použít v Jupyter:

  Otevřete Jupyter a vyberte **Nový** pro vytvoření nového poznámkového bloku. Typ jádra můžete nastavit jako **Python [conda root]** pro Python 2,7 a **Python [conda ENV: Py35]** pro Python 3,5. 

* Instalovat balíčky Pythonu:

  Výchozí prostředí Pythonu v DSVM jsou globální prostředí, která jsou čitelná pro všechny uživatele. Ale jenom správci můžou zapisovat a instalovat globální balíčky. Pokud chcete balíčky nainstalovat do globálního prostředí, aktivujte ji pomocí `activate` příkazu jako správce v kořenovém nebo py35 prostředí. Pak můžete použít Správce balíčků jako `conda` nebo `pip` k instalaci nebo aktualizaci balíčků.

## <a name="r"></a>R

|    |           |
| ------------- | ------------- |
| Podporované jazykové verze | Microsoft R Open 3. x (100% kompatibilní s CRAN-R)<br /> Microsoft R Server 9. x Developer Edition (škálovatelná platforma R připravená pro podniky)|
| Podporované edice DSVM      | Linux, Windows     |
| Jak ho nakonfigurovat či nainstalovaná na datové VĚDY?  | Windows: `C:\Program Files\Microsoft\ML Server\R_SERVER` <br />Linux: `/usr/lib64/microsoft-r/3.3/lib64/R`    |
| Odkazy na ukázky      | Zahrnuje ukázky Jupyter poznámkových bloků pro R.     |
| Související nástroje na DSVM      | SparkR, Python, Julia      |
### <a name="how-to-use-and-run-it"></a>Jak použít a spustit    

**Windows:**

* Spusťte příkaz na příkazovém řádku:

  Otevřete příkazový řádek a zadejte `R`.

* Použití v rozhraní IDE:

  Použití RTools pro Visual Studio (RTVS) nainstalované v aplikaci Visual Studio Community edition nebo RStudio. Jsou k dispozici v nabídce Start nebo jako ikona na ploše. 

* Použití v Jupyter

  Otevřete Jupyter a vyberte **Nový** pro vytvoření nového poznámkového bloku. Pokud chcete použít jádro Jupyter R (IRKernel), můžete nastavit typ jádra na **r** .

* Nainstalovat balíčky R:

  Jazyk R je nainstalovaný na DSVM v globálním prostředí, které je čitelné pro všechny uživatele. Ale jenom správci můžou zapisovat a instalovat globální balíčky. Chcete-li nainstalovat balíčky do globálního prostředí, spusťte R pomocí jedné z výše uvedených metod. Pak můžete spustit Správce `install.packages()` balíčků R a nainstalovat nebo aktualizovat balíčky.

**Linux:**

* Spustit v terminálu:

  Otevřete terminál a spusťte příkaz `R`.  

* Použití v rozhraní IDE:

  Použijte RStudio, který je nainstalovaný na Linux DSVM.  

* Použít v Jupyter:

  Otevřete Jupyter a vyberte **Nový** pro vytvoření nového poznámkového bloku. Pokud chcete použít jádro Jupyter R (IRKernel), můžete nastavit typ jádra na **r** . 

* Nainstalovat balíčky R:

  Jazyk R je nainstalovaný na DSVM v globálním prostředí, které je čitelné pro všechny uživatele. Ale jenom správci můžou zapisovat a instalovat globální balíčky. Chcete-li nainstalovat balíčky do globálního prostředí, spusťte R pomocí jedné z výše uvedených metod. Pak můžete spustit Správce `install.packages()` balíčků R a nainstalovat nebo aktualizovat balíčky.


## <a name="julia"></a>Helena

|    |           |
| ------------- | ------------- |
| Podporované jazykové verze | 0.6 |
| Podporované edice DSVM      | Linux, Windows     |
| Jak ho nakonfigurovat či nainstalovaná na datové VĚDY?  | Windows: Nainstalováno na`C:\JuliaPro-VERSION`<br /> Linux: Nainstalováno na`/opt/JuliaPro-VERSION`    |
| Odkazy na ukázky      | Zahrnuje ukázky Jupyter poznámkových bloků pro Helena.     |
| Související nástroje na DSVM      | Python, R      |
### <a name="how-to-use-and-run-it"></a>Jak použít a spustit    

**Windows:**

* Spustit na příkazovém řádku

  Otevřete příkazový řádek a spusťte `julia`příkaz.
* Použití v rozhraní IDE:

  Použijte `Juno` s prostředím Helena, které je nainstalované na DSVM a je dostupné jako zástupce na ploše.

* Použít v Jupyter:

  Otevřete Jupyter a vyberte **Nový** pro vytvoření nového poznámkového bloku. Typ jádra můžete nastavit jako **Helena verze**.

* Instalovat balíčky Helena:

  Výchozí umístění Helena je globální prostředí, které je čitelné pro všechny uživatele. Ale jenom správci můžou zapisovat a instalovat globální balíčky. Pro instalaci balíčků do globálního prostředí spusťte Helena pomocí jedné z předchozích metod. Pak můžete spustit příkazy správce balíčků Helena, jako `Pkg.add()` je instalace nebo aktualizace balíčků.


**Linux:**
* Spusťte v terminálu:

  Otevřete terminál a spusťte příkaz `julia`.
* Použití v rozhraní IDE:

  Použijte `Juno`, s použitím rozhraní IDE pro Helena nainstalovaného na DSVM a dostupných jako zástupce nabídky **aplikace** .

* Použít v Jupyter:

  Otevřete Jupyter a vyberte **Nový** pro vytvoření nového poznámkového bloku. Typ jádra můžete nastavit jako **Helena verze**.

* Instalovat balíčky Helena:

  Výchozí umístění Helena je globální prostředí, které je čitelné pro všechny uživatele. Ale jenom správci můžou zapisovat a instalovat globální balíčky. Pro instalaci balíčků do globálního prostředí spusťte Helena pomocí jedné z předchozích metod. Pak můžete spustit příkazy správce balíčků Helena, jako `Pkg.add()` je instalace nebo aktualizace balíčků.

## <a name="other-languages"></a>Jiné jazyky

**C#** : K dispozici ve Windows a přístupné prostřednictvím sady Visual Studio Community Edition nebo `Developer Command Prompt for Visual Studio`v, kde můžete `csc` spustit příkaz.

**Java**: OpenJDK je k dispozici v edicích systému Linux i Windows pro DSVM a je nastavena v cestě. Chcete-li použít jazyk Java `javac` , `java` zadejte příkaz nebo na příkazovém řádku ve Windows nebo v prostředí bash v systému Linux.

**Node.js**: Node. js je k dispozici v edicích systému Linux i Windows pro DSVM a je nastavená v cestě. Chcete-li získat přístup k Node. `node` js `npm` , zadejte příkaz nebo na příkazovém řádku ve Windows nebo v prostředí bash v systému Linux. V systému Windows je k dispozici rozšíření sady Visual Studio pro nástroje Node. js, které poskytuje grafické rozhraní IDE pro vývoj aplikace Node. js.

**F#** : K dispozici ve Windows a přístupné prostřednictvím sady Visual Studio Community Edition nebo `Developer Command Prompt for Visual Studio`na, kde můžete `fsc` spustit příkaz.
