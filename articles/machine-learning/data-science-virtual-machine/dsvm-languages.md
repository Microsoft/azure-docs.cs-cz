---
title: Podporované jazyky pro virtuální počítač pro datové vědy
titleSuffix: Azure
description: Další informace o programu jazyků a související nástroje, které jsou předem nainstalované na virtuální počítač pro datové vědy.
keywords: nástroje pro datové vědy, virtuální počítač pro datové vědy, datové vědy pro linux
services: machine-learning
documentationcenter: ''
author: gopitk
manager: cgronlun
ms.custom: seodec18
ms.assetid: ''
ms.service: machine-learning
ms.component: data-science-vm
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/11/2017
ms.author: gokuma
ms.openlocfilehash: 8cc5d1a2d78179624ee1ba17482e9d1892625d6f
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/08/2018
ms.locfileid: "53104284"
---
# <a name="languages-supported-on-the-data-science-virtual-machine"></a>Jazyky podporované na virtuální počítač pro datové vědy 

Na Data virtuálního počítače VĚDY obsahuje několik předdefinovaných jazyky a vývojářské nástroje pro sestavování aplikací AI. Tady jsou některé důležité z nich. 

## <a name="python-windows-server-2016-edition"></a>Python (edice systému Windows Server 2016)

|    |           |
| ------------- | ------------- |
| Podporované jazykové verze | 2.7 i 3.6 |
| Podporované DSVM edice      | Windows Server 2016     |
| Jak ho nakonfigurovat či nainstalovaná na datové VĚDY?  | Dvě globální `conda` prostředí vytváří. <br /> * `root` umístění prostředí `/anaconda/` je Python 3.6. <br/> * `python2` umístění prostředí `/anaconda/envs/python2`se Python 2.7       |
| Odkazy na ukázky      | Jsou zahrnuty ukázkové poznámkové bloky Jupyter pro Python     |
| Související nástroje na datové VĚDY      | PySpark, R, Julia      |

> [!NOTE]
> Windows Server 2016 vytvořené před březnem 2018 obsahuje Python 3.5 a použije se Python 2.7. Python 2.7 je také conda **kořenové** prostředí a **py35** je prostředí Python 3.5. 

### <a name="how-to-use--run-it"></a>Jak používat nebo ji spustit?    

* Spuštění v příkazovém řádku

Otevřete příkazový řádek a proveďte kroky v závislosti na verzi jazyka Python, kterou chcete spustit. 

```
# To run Python 2.7
activate python2
python --version

# To run Python 3.6
activate 
python --version

```
* Použití v rozhraní IDE

Použití nástrojů Python pro Visual Studio (PTVS) nainstalované v aplikaci Visual Studio Community edition. Nastavení prostředí privonly automaticky v PTVS ve výchozím nastavení je Python 3.6. 

> [!NOTE]
> Tak, aby odkazoval na Python 2.7 PTVS, je potřeba vytvořit vlastní prostředí v PTVS. Nastavení tohoto prostředí cestách ve Visual Studio Community Edition, přejděte na **nástroje** -> **nástroje Python Tools** -> **prostředí Pythonu** a pak klikněte na tlačítko **+ vlastní**. Potom nastavte umístění `c:\anaconda\envs\python2` a potom klikněte na tlačítko _automaticky rozpoznat_. 

* Použití v Jupyter

Otevřete Jupyter a klikněte `New` pro vytvoření nového poznámkového bloku. V tomto okamžiku můžete zvolit typ jádra jako _Python [Conda kořenové]_ pro Python 3.6 a _Python [Conda env:python2]_ prostředí Python 2.7. 

* Instalace balíčků Pythonu

Výchozí prostředí Pythonu na datové VĚDY jsou globální prostředí číst všichni uživatelé. Ale můžete napsat / install globálních balíčků jenom správci. Pokud chcete balíček nainstalovat do globálního prostředí, aktivujte na kořenový server WSUS nebo pomocí prostředí python2 `activate` příkaz jako správce. Můžete použít Správce balíčků, jako je `conda` nebo `pip` pro instalaci nebo aktualizaci balíčků. 

## <a name="python-linux-and-windows-server-2012-edition"></a>Python (Linux a Windows Server 2012 Edition)

|    |           |
| ------------- | ------------- |
| Podporované jazykové verze | 2.7 a 3.5 |
| Podporované DSVM edice      | Linux, Windows Server 2012    |
| Jak ho nakonfigurovat či nainstalovaná na datové VĚDY?  | Dvě globální `conda` prostředí vytváří. <br /> * `root` umístění prostředí `/anaconda/` se Python 2.7. <br/> * `py35` umístění prostředí `/anaconda/envs/py35`je Python 3.5       |
| Odkazy na ukázky      | Jsou zahrnuty ukázkové poznámkové bloky Jupyter pro Python     |
| Související nástroje na datové VĚDY      | PySpark, R, Julia      |
### <a name="how-to-use--run-it"></a>Jak používat nebo ji spustit?    

**Linux**
* Spuštění v terminálu

Otevřete terminál a proveďte kroky v závislosti na verzi jazyka Python, kterou chcete spustit. 

```
# To run Python 2.7
source activate 
python --version

# To run Python 3.5
source activate py35
python --version

```
* Použití v rozhraní IDE

Použití PyCharm nainstalované v aplikaci Visual Studio Community edition. 

* Použití v Jupyter

Otevřete Jupyter a klikněte `New` pro vytvoření nového poznámkového bloku. V tomto okamžiku můžete zvolit typ jádra jako _Python [Conda kořenové]_ for Python 2.7 a _Python [Conda env:py35]_ prostředí Python 3.5. 

* Instalace balíčků Pythonu

Výchozí prostředí Pythonu na datové VĚDY jsou globální prostředí číst všichni uživatelé. Ale můžete napsat / install globálních balíčků jenom správci. Pokud chcete balíček nainstalovat do globálního prostředí, aktivovat do kořenového adresáře nebo pomocí prostředí py35 `source activate` příkaz jako správce nebo uživatel s oprávněními sudo. Můžete použít Správce balíčků, jako je `conda` nebo `pip` pro instalaci nebo aktualizaci balíčků. 

**Windows 2012**
* Spuštění v příkazovém řádku

Otevřete příkazový řádek a proveďte kroky v závislosti na verzi jazyka Python, kterou chcete spustit. 

```
# To run Python 2.7
activate 
python --version

# To run Python 3.5
activate py35
python --version

```
* Použití v rozhraní IDE

Použití nástrojů Python pro Visual Studio (PTVS) nainstalované v aplikaci Visual Studio Community edition. Nastavení prostředí privonly automaticky v PTVS v případě Pythonu 2.7. 
> [!NOTE]
> Tak, aby odkazoval PTVS na Python 3.5, je potřeba vytvořit vlastní prostředí v PTVS. Nastavení tohoto prostředí cestách ve Visual Studio Community Edition, přejděte na **nástroje** -> **nástroje Python Tools** -> **prostředí Pythonu** a pak klikněte na tlačítko **+ vlastní**. Potom nastavte umístění `c:\anaconda\envs\py35` a potom klikněte na tlačítko _automaticky rozpoznat_. 

* Použití v Jupyter

Otevřete Jupyter a klikněte `New` pro vytvoření nového poznámkového bloku. V tomto okamžiku můžete zvolit typ jádra jako _Python [Conda kořenové]_ for Python 2.7 a _Python [Conda env:py35]_ prostředí Python 3.5. 

* Instalace balíčků Pythonu

Výchozí prostředí Pythonu na datové VĚDY jsou globální prostředí číst všichni uživatelé. Ale můžete napsat / install globálních balíčků jenom správci. Pokud chcete balíček nainstalovat do globálního prostředí, aktivovat do kořenového adresáře nebo pomocí prostředí py35 `activate` příkaz jako správce. Můžete použít Správce balíčků, jako je `conda` nebo `pip` pro instalaci nebo aktualizaci balíčků. 

## <a name="r"></a>R

|    |           |
| ------------- | ------------- |
| Podporované jazykové verze | Microsoft R Open 3.x (100 % kompatibilní s CRAN r.<br /> Microsoft R Server 9.x Developer edition (A škálovatelné podnikové platformy pro připravený R)|
| Podporované DSVM edice      | Linux, Windows     |
| Jak ho nakonfigurovat či nainstalovaná na datové VĚDY?  | Windows: `C:\Program Files\Microsoft\ML Server\R_SERVER` <br />Linux: ` /usr/lib64/microsoft-r/3.3/lib64/R`    |
| Odkazy na ukázky      | Jsou zahrnuty ukázkové poznámkové bloky Jupyter pro R     |
| Související nástroje na datové VĚDY      | SparkR, Python, Julia      |
### <a name="how-to-use--run-it"></a>Jak používat nebo ji spustit?    

**Windows:**

* Spuštění v příkazovém řádku

Otevřete příkazový řádek a zadejte `R`.

* Použití v rozhraní IDE

Použití RTools pro Visual Studio (RTVS) nainstalované v aplikaci Visual Studio Community edition nebo RStudio. Toto jsou k dispozici v nabídce start nebo jako ikony na ploše. 

* Použití v Jupyter

Otevřete Jupyter a klikněte `New` pro vytvoření nového poznámkového bloku. V tomto okamžiku můžete zvolit typ jádra jako _R_ použití jádra Jupyter R (IRKernel). 

* Instalace balíčků R

R je nainstalována na datové VĚDY v globálním prostředí čitelné všichni uživatelé. Ale můžete napsat / install globálních balíčků jenom správci. Pokud chcete balíček nainstalovat do globálního prostředí, spusťte R pomocí jedné z metod uvedených výše. Potom spustíte Správce balíčků R `install.packages()` pro instalaci nebo aktualizaci balíčků. 

**Linux:**

* Spuštění v terminálu

Otevřete terminál a právě spuštění `R`.  

* Použití v rozhraní IDE

Použijte RStudio nainstalovaná na datové VĚDY pro Linux.  

* Použití v Jupyter

Otevřete Jupyter a klikněte `New` pro vytvoření nového poznámkového bloku. V tomto okamžiku můžete zvolit typ jádra jako _R_ použití jádra Jupyter R (IRKernel). 

* Instalace balíčků R

R je nainstalována na datové VĚDY v globálním prostředí čitelné všichni uživatelé. Ale můžete napsat / install globálních balíčků jenom správci. Pokud chcete balíček nainstalovat do globálního prostředí, spusťte R pomocí jedné z metod uvedených výše. Potom spustíte Správce balíčků R `install.packages()` pro instalaci nebo aktualizaci balíčků. 


## <a name="julia"></a>Helena

|    |           |
| ------------- | ------------- |
| Podporované jazykové verze | 0.6 |
| Podporované DSVM edice      | Linux, Windows     |
| Jak ho nakonfigurovat či nainstalovaná na datové VĚDY?  | Windows: Při instalaci `C:\JuliaPro-VERSION`<br /> Linux: Nainstalovaným v `/opt/JuliaPro-VERSION`    |
| Odkazy na ukázky      | Ukázkové poznámkové bloky Jupyter pro Julie jsou zahrnuté     |
| Související nástroje na datové VĚDY      | Python, R      |
### <a name="how-to-use--run-it"></a>Jak používat nebo ji spustit?    

**Windows:**

* Spuštění v příkazovém řádku

Otevřete příkazový řádek a stačí spustit `julia`. 
* Použití v rozhraní IDE

Použití `Juno` Julie IDE nainstalované na datové VĚDY a k dispozici jako zástupce na ploše.

* Použití v Jupyter

Otevřete Jupyter a klikněte `New` pro vytvoření nového poznámkového bloku. V tomto okamžiku můžete zvolit typ jádra jako `Julia VERSION` 

* Instalace balíčků Julia

Helena umístění výchozí hodnota je globální prostředí číst všichni uživatelé. Ale můžete napsat / install globálních balíčků jenom správci. Pokud chcete balíček nainstalovat do globálního prostředí, spusťte Julie pomocí jedné z metod uvedených výše. Potom spustíte Julie balíček správce příkazů, jako jsou `Pkg.add()` pro instalaci nebo aktualizaci balíčků. 


**Linux:**
* Spuštění v terminálu.

Otevřete terminál a právě spuštění `julia`. 
* Použití v rozhraní IDE

Použití `Juno` Julie IDE nainstalované na datové VĚDY a k dispozici jako místní nabídku aplikace.

* Použití v Jupyter

Otevřete Jupyter a klikněte `New` pro vytvoření nového poznámkového bloku. V tomto okamžiku můžete zvolit typ jádra jako `Julia VERSION` 

* Instalace balíčků Julia

Helena umístění výchozí hodnota je globální prostředí číst všichni uživatelé. Ale můžete napsat / install globálních balíčků jenom správci. Pokud chcete balíček nainstalovat do globálního prostředí, spusťte Julie pomocí jedné z metod uvedených výše. Potom spustíte Julie balíček správce příkazů, jako jsou `Pkg.add()` pro instalaci nebo aktualizaci balíčků. 

## <a name="other-languages"></a>Jiné jazyky

**C#**: K dispozici na Windows a přístupné přes Visual Studio Community edition nebo na `Developer Command Prompt for Visual Studio` kde lze pouze spustit `csc` příkazu. 

**Java**: OpenJDK je k dispozici v Linuxu i Windows edice DSVM a nastavte na cestě. Můžete zadat `javac` nebo `java` příkazu na příkazovém řádku ve Windows nebo prostředí bash v Linuxu pomocí Javy. 

**Node.js**: platforma node.js je k dispozici v Linuxu i Windows edition DSVM a sada na cestě. Můžete zadat `node` nebo `npm` příkazu na příkazovém řádku ve Windows nebo prostředí bash v systému Linux pro přístup k node.js. Na Windows je nainstalován Node.js tools pro rozšíření sady Visual Studio k poskytování grafické integrovaného vývojového prostředí pro vývoj aplikace node.js. 

**F#**: K dispozici na Windows a přístupné přes Visual Studio Community edition nebo na `Developer Command Prompt for Visual Studio` kde lze pouze spustit `fsc` příkazu. 


