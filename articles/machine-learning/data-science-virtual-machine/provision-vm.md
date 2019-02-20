---
title: Vytvoření virtuálního počítače Windows datové vědy
titleSuffix: Azure
description: Konfigurace a vytvořit virtuální počítač pro datové vědy v Azure pro účely analýzy a strojového učení.
services: machine-learning
documentationcenter: ''
author: gopitk
manager: cgronlun
ms.custom: seodec18
ms.assetid: e1467c0f-497b-48f7-96a0-7f806a7bec0b
ms.service: machine-learning
ms.subservice: data-science-vm
ms.workload: data-services
ms.devlang: na
ms.topic: article
ms.date: 12/04/2018
ms.author: gokuma
ms.openlocfilehash: 8ee424412c9c54dadef7391084e3ab976da9ad7f
ms.sourcegitcommit: 79038221c1d2172c0677e25a1e479e04f470c567
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/19/2019
ms.locfileid: "56417444"
---
# <a name="provision-a-windows-data-science-virtual-machine-on-azure"></a>Zřízení virtuálního počítače Windows datové vědy v Azure

Microsoft Windows Data Science virtuálního počítače (datové VĚDY) je image virtuálního počítače (VM) Windows serveru 2016 v Azure, která se dodává jako předinstalované a nakonfigurovaná s nástroji pro analýzu dat a strojové učení.

## <a name="included-data-science-tools"></a>Zahrnutá data science nástroje

Tyto nástroje jsou součástí DSVM:

* [Služba Azure Machine Learning](../service/index.yml) Python SDK
* [Microsoft Machine Learning Server](https://docs.microsoft.com/machine-learning-server/index) Developer edition
* Anacondu, distribuci jazyka Python
* S R, Python nebo PySpark jádra aplikace Jupyter Notebook
* Microsoft Visual Studio Community
* Microsoft Power BI Desktopu
* Microsoft SQL Server 2017 Developer edition
* Samostatná instance Apache Spark pro místní vývoj a testování
* [JuliaPro](https://juliacomputing.com/products/juliapro.html)
* Machine learning a datové analýzy nástroje:
  * Architektury obsáhlého learningu - celou řadu architektur AI jsou zahrnuty ve virtuálním počítači: [Microsoft Cognitive Toolkit](https://www.microsoft.com/en-us/cognitive-toolkit/), [TensorFlow](https://www.tensorflow.org/), [Chainer](https://chainer.org/), mxNet, Keras a
  * [Vowpal Wabbit](https://github.com/JohnLangford/vowpal_wabbit) – rychlé strojového učení systém, který podporuje postupů, jako jsou online algoritmu hash, allreduce, snížení, learning2search a aktivní a interaktivní učení
  * [XGBoost](https://xgboost.readthedocs.org/en/latest/) – nástroj, který poskytuje rychlý a přesné Posílený stromu implementaci
  * [Rattle](https://togaware.com/rattle/) -R analytického nástroje, který vám pomůže začít s analýzy dat a strojové učení v jazyce R. Zahrnuje zkoumání a modelování s automatické generování kódu R dat využívající grafické rozhraní.
  * [Weka](http://www.cs.waikato.ac.nz/ml/weka/) -dolování vizuálních dat a strojové učení softwaru v jazyce Java
  * [Apache Drill](https://drill.apache.org/) – bez schémat modul dotazů SQL pro Apache Hadoop, NoSQL a cloudového úložiště. Podporuje rozhraní ODBC a JDBC pro dotazování NoSQL a soubory ze standardních nástrojů BI, jako je Power BI, Microsoft Excelu a Tableau.
* Knihovny v R a Python pro použití v Azure Machine Learning a dalšími službami Azure
* Git, včetně Git Bash pro práci s úložišť zdrojového kódu, včetně Githubu a Azure DevOps. Git poskytuje několik oblíbených Linux nástroje příkazového řádku, které jsou dostupné i na Git Bash a příkazový řádek. Příklady jsou awk sed, perl, grep, hledání, wget a curl.

### <a name="about-data-science"></a>O datové vědy

Vědecké zpracování dat zahrnuje iterace v pořadí úloh:

1. Najít, načíst a předzpracování dat
1. Sestavení a otestování modelů
1. Nasazení modelů pro použití v inteligentních aplikacích

Odborníci přes data použít několik nástrojů pro tyto úlohy. Může být časově najít odpovídající verze softwaru a pak si stáhnout a nainstalovat je. Datové VĚDY šetří čas tím, že poskytuje bitovou kopii připravené k použití, která se dá zřídit v Azure s několika oblíbenými nástroji, které jsou předem nainstalován a nakonfigurován.

Datové VĚDY jump-starts analytického projektu. Můžete pracovat na úkoly v různých jazycích, včetně R, Python, SQL a C#. Visual Studio nabízí snadno použitelné integrované vývojové prostředí (IDE) pro vývoj a testování kódu. Sady Azure SDK je součástí virtuálního počítače, tak při sestavování aplikací s využitím různých služeb na cloudové platformě Microsoftu.

Neúčtují žádné poplatky softwaru pro image virtuálního počítače Toto datové vědy. Platíte jenom poplatky za využití Azure. Jsou závislé na velikosti virtuálního počítače, které zřizujete. Další podrobnosti o poplatky za výpočetní prostředky jsou v **podrobnosti o cenách** části na [virtuální počítač pro datové vědy](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-ads.windows-data-science-vm?tab=PlansAndPrice) stránky.

### <a name="other-dsvm-versions"></a>Jiné verze DSVM

* [Ubuntu](dsvm-ubuntu-intro.md) bitové kopie. Obsahuje celou řadu nástrojů, které jsou podobné pro datové VĚDY a navíc několik dalších hloubkového učení architektury.
* A [Linux CentOS](linux-dsvm-intro.md) bitové kopie.
* [Edice systému Windows Server 2012](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-ads.standard-data-science-vm) o virtuální počítač pro datové vědy. Několik nástroje jsou k dispozici pouze v edici Windows serveru 2016. V opačném případě v tomto článku platí také pro edice Windows serveru 2012.

## <a name="prerequisite"></a>Požadavek

Chcete-li vytvořit virtuální počítač Microsoft datové vědy, musíte mít předplatné Azure. Viz [Získání bezplatné zkušební verze Azure](http://azure.com/free).

## <a name="create-your-dsvm"></a>Vytvoření vašeho DSVM

Chcete-li vytvořit instanci DSVM:

1. Přejděte na virtuální počítač na [webu Azure portal](https://portal.azure.com/#create/microsoft-dsvm.dsvm-windowsserver-2016). Můžete být vyzváni k přihlášení ke svému účtu Azure, pokud ještě nejste přihlášeni.
1. Vyberte **vytvořit** tlačítko dole.

   ![Konfigurace data vědy vm](./media/provision-vm/configure-data-science-virtual-machine.png)

1. Budete vyzváni k zadání následujících informací nakonfigurujte všechny kroky uvedené v pravém podokně na snímku obrazovky:

   1. **Základy**:
      * **Název**: název datové VĚDY
      * **Typ disku virtuálního počítače**: buď **SSD** nebo **HDD**. Instance NC_v1 GPU jako NVidia Tesla K80 na základě zvolte **HDD** jako typ disku.
      * **Uživatelské jméno**: ID účtu správce
      * **Heslo**: heslo účtu správce
      * **Předplatné**: Pokud máte více předplatných, vyberte ten, ve které je vytvořené a fakturuje počítač.
      * **Skupina prostředků**. Vytvořit nové nebo použijte existující skupinu.
      * **Umístění**. Vyberte datové centrum, které je nejvhodnější. Nejrychlejší přístup k síti je většina vašich dat nebo je nejblíže vašemu fyzickému umístění datového centra.
   1. **Velikost**: Vyberte jeden z typů serveru, které splňuje požadavky na funkce a omezení nákladů. Další možnosti velikostí virtuálních počítačů, vyberte **zobrazení všech**.
   1. **Nastavení**:  
      * **Použití spravovaných disků**. Zvolte **spravované** Pokud chcete Azure ke správě disků pro virtuální počítač. Pokud ne, je třeba zadat účet nového nebo existujícího úložiště.  
      * **Další parametry**. Můžete použít výchozí hodnoty. Pokud chcete použít jiné než výchozí hodnoty, najeďte myší na informační odkaz nápovědy pro konkrétní pole.
   1. **Souhrn**: Ověřte, zda všechny informace, které jste zadali správný. Vyberte **Vytvořit**.

> [!NOTE]
> * Virtuální počítač není žádné další poplatky za výpočetní náklady pro velikost serveru, kterou jste zvolili v **velikost** kroku.
> * Zřizování trvá asi 10 až 20 minut. Stav virtuálního počítače můžete zobrazit na portálu Azure portal.

## <a name="how-to-access-the-dsvm"></a>Jak získat přístup k datové VĚDY

Po vytvoření a zřízení virtuálního počítače je možné vzdálené plochy do něj pomocí přihlašovacích údajů účtu správce, které jste nakonfigurovali v předchozím **Základy** oddílu. Jste připraveni začít používat nástroje, které jsou nainstalované a nakonfigurované na virtuálním počítači. Mnoho nástrojů je přístupný prostřednictvím dlaždic nabídky start a ikony na ploše.

## <a name="tools-installed-on-the-microsoft-data-science-virtual-machine"></a>Nástroje nainstalované na Microsoft virtuální počítač pro datové vědy

Další informace o nástrojích, které jsou nainstalované na datové VĚDY:

### <a name="microsoft-machine-learning-server-developer-edition"></a>Microsoft Machine Learning Server Developer edition

Microsoft Enterprise Library pro škálovatelné R nebo Python můžete použít pro analýzu protože Machine Learning Server Developer edition je nainstalovaný na virtuálním počítači. Dříve označované jako Microsoft R Server, Machine Learning Server je široce nasaditelné, analytická platforma podnikové třídy. Je k dispozici pro R a Python. Také je škálovatelný, komerčně podporované a zabezpečený.

Machine Learning Server podporuje různé statistiky pro velké objemy dat, prediktivní modelování a strojové učení úlohy. Podporuje široké spektrum analytických: zkoumání, analýzu, vizualizaci a modelování. Díky používání a rozšiřování open-source jazyka R a Python, Machine Learning Server je kompatibilní se skripty R a Python a funkce. Je také kompatibilní s CRAN, pip a balíčků Conda, k analýze dat v podnikovém měřítku.

Machine Learning Server řeší přidáním paralelní a blokového zpracování dat v paměti omezení open-source systému R. To znamená, že můžete spouštět analýzy na množství dat větší, než se vejde do paměti. Visual Studio Community je zahrnutá ve virtuálním počítači. Obsahuje nástroje R pro Visual Studio a nástroji Python Tools pro Visual Studio (PTVS) rozšíření, které poskytují kompletního integrovaného vývojového prostředí pro práci s R nebo Python. Poskytujeme také další prostředí IDE, jako jsou [RStudio](http://www.rstudio.com) a [PyCharm Community edition](https://www.jetbrains.com/pycharm/) na virtuálním počítači.

### <a name="python"></a>Python

Pro vývoj s použitím jazyka Python jsou nainstalovány distribuce Anaconda Python 2.7 i 3.6. Tyto distribuce mít základní Python spolu s přibližně 300 nejoblíbenějších balíčků analytics matematické, inženýrství a data. Slouží k PTVS, který je nainstalovaný v rámci Visual Studio Community 2017. Nebo můžete použít jednu ze součástí Anaconda jako nečinný nebo Spyder integrovanými vývojovými prostředími. Vyhledejte a spusťte jeden z těchto balíčků (Win + S).

> [!NOTE]
> Tak, aby odkazoval nástroje Pythonu pro Visual Studio na Anaconda Python 2.7, je potřeba vytvořit vlastní prostředí pro každou verzi. Chcete-li nastavit tyto cesty prostředí Visual Studio 2017 Community, přejděte na **nástroje** > **nástroje Python Tools** > **prostředí Pythonu**. Potom vyberte **+ vlastní**.

Anaconda Python 3.6 je nainstalované v **C:\Anaconda**. Anaconda Python 2.7 je nainstalované v **c:\Anaconda\envs\python2**. Podrobné pokyny najdete v článku [dokumentace k PTVS](https://docs.microsoft.com/visualstudio/python/installing-python-interpreters).

### <a name="the-jupyter-notebook"></a>Poznámkový blok Jupyter

Anaconda distribuce také součástí Poznámkový blok Jupyter, prostředí pro sdílení kódu a analýzy. Python 2.7 Python 3.x, PySpark, Julia a R jádrech je předem nakonfigurovány aplikace Jupyter Notebook server. Chcete-li spustit Jupyter server a spustit prohlížeč pro přístup k serveru poznámkového bloku, použijte **Poznámkový blok Jupyter** ikony na ploše.

Balíček jsme několik ukázkové poznámkové bloky v Pythonu a R. Po získání přístupu Jupyter poznámkových bloků ukazují, jak pracovat s tyto technologie:

* Machine Learning Server
* SQL Server Machine Learning Services, analýza v databázi
* Python
* Microsoft Cognitive ToolKit
* Tensorflow
* Jiné technologie Azure

Odkaz na ukázky najdete na domovské stránce Poznámkový blok po ověření do poznámkového bloku Jupyter s použitím hesla, které jste vytvořili v dřívějším kroku.

### <a name="visual-studio-community-2017"></a>Visual Studio Community 2017

Datové VĚDY zahrnuje Visual Studio Community. Toto je bezplatná verze Oblíbené integrované vývojové prostředí společnosti Microsoft, který používáte pro účely vyhodnocení a malé týmy. Zobrazit [licenční podmínky](https://www.visualstudio.com/support/legal/mt171547).

Otevřít Visual Studio pomocí ikony desktopových nebo **Start** nabídky. Hledání pro programy (Win + S), za nímž následuje **sady Visual Studio**. Odtud můžete vytvářet projekty v jazycích C#, Python, R a node.js. Nainstalovaných modulů plug-in usnadnit práci s těmito službami Azure:

* Katalog dat Azure
* Azure HDInsight Hadoop a Spark
* Azure Data Lake

K dispozici je také modul plug-in volá **Azure Machine Learning pro Visual Studio Code** , který hladce integruje do Azure Machine Learning a umožňuje rychle sestavovat aplikace AI.

> [!NOTE]
> Může se zobrazit zpráva, že vypršela platnost vašeho zkušebního období. Zadejte svoje přihlašovací údaje účtu Microsoft. Nebo vytvořte nový bezplatný účet, abyste získali přístup k Visual Studio Community.

### <a name="sql-server-2017-developer-edition"></a>Edice SQL Server 2017 Developer

Datové VĚDY se dodává s verzí SQL serveru 2017 pomocí služby Machine Learning pro vývojáře. Tato edice systému SQL Server je k dispozici ve R nebo Python a spouštět analýzy v databázi. Služby Machine Learning poskytuje platformu pro vývoj a nasazování inteligentních aplikací. Tyto jazyky a mnoho balíčků od komunity slouží k vytváření modelů a generovat předpovědi pro data systému SQL Server. Analytics blízko data můžete zachovat, protože služby Machine Learning, v databázi, integruje jazyky R i Python v rámci SQL serveru. Tato integrace se eliminují náklady a bezpečnostní rizika spojená s přesuny dat.

> [!NOTE]
> SQL Server Developer edition je pouze pro účely vývoje a testování. Budete potřebovat licenci k použití v produkčním prostředí.

SQL Server dostanete spuštěním Microsoft SQL Server Management Studio. Název vašeho virtuálního počítače je vyplněna jako **název serveru**. Windows ověřování použijte, když se přihlásíte jako správce na Windows. Až budete v aplikaci SQL Server Management Studio, vytvořit další uživatele, vytvářet databáze, umožňuje importovat data a spouštět dotazy SQL.

Povolit analýzu v databázi s využitím služeb Machine Learning SQL, spusťte následující příkaz jako jednorázová akce v aplikaci SQL Server Management Studio po přihlášení jako správce serveru:

```
CREATE LOGIN [%COMPUTERNAME%\SQLRUserGroup] FROM WINDOWS
```

Nahraďte `%COMPUTERNAME%` názvem vašeho virtuálního počítače.

### <a name="azure"></a>Azure

Několik nástrojů pro Azure jsou nainstalované na virtuálním počítači:

* Zástupce na ploše přejde na dokumentaci k sadě Azure SDK.
* Použití **AzCopy** ke kopírování dat do a z účtu služby Azure Storage. Pokud chcete zobrazit využití, zadejte **Azcopy** z příkazového řádku.
* Použití **Průzkumníka služby Azure Storage** procházet objekty, které ukládáte ve svém účtu úložiště Azure. Také zkopíruje data do a z Azure Storage. Chcete-li tento nástroj používat, zadejte **Průzkumníka služby Storage** v **hledání** pole. Nebo ji najít v Windows **Start** nabídky.
* **Adlcopy** kopíruje data do služby Azure Data Lake. Pokud chcete zobrazit využití, zadejte **adlcopy** v příkazovém řádku.
* **dtui** kopíruje data do a ze služby Azure Cosmos DB, databázi NoSQL v cloudu. Zadejte **dtui** v příkazovém řádku.
* **Azure Data Factory Integration Runtime** kopíruje data mezi místním zdrojům dat a cloudu. Používá se v rámci nástroje, jako je Azure Data Factory.
* Použití **prostředí Azure PowerShell** ke správě vašich prostředků Azure v skriptovacím jazyku prostředí PowerShell. Také se nainstaluje na váš virtuální počítač.

### <a name="power-bi"></a>Power BI

Datové VĚDY se dodává s **Power BI Desktopu** nainstalované, které vám pomůžou vytvářet řídicí panely a vizualizace. Pomocí tohoto nástroje k získání dat z různých zdrojů, vytvářet řídicí panely a sestavy a publikovat je do cloudu. Další informace najdete v tématu [Power BI](https://powerbi.microsoft.com) lokality. Power BI Desktopu můžete najít na **Start** nabídky.

> [!NOTE]
> Budete potřebovat účet Microsoft Office 365 pro přístup k Power BI.

### <a name="azure-machine-learning-service-python-sdk"></a>Služba Azure Machine Learning Python SDK

Odborníci přes data a vývojářům AI pomocí sady Azure Machine Learning SDK pro Python k sestavení a spuštění pracovních postupů machine learning s [služby Azure Machine Learning](../service/overview-what-is-azure-ml.md). Můžete pracovat se službou v poznámkových blocích Jupyter nebo jiné integrované vývojové prostředí Pythonu s využitím open source architektur, jako je TensorFlow a scikit-informace.

Chcete-li začít používat sadu Python SDK, přečtěte si téma [použití Pythonu k seznámení s Azure Machine Learning](../service/quickstart-create-workspace-with-python.md).

Python SDK je předinstalován v Microsoftu virtuální počítač pro datové vědy.

## <a name="more-microsoft-development-tools"></a>Další nástroje pro vývoj Microsoft

Můžete použít [instalačního programu webové platformy Microsoft](https://www.microsoft.com/web/downloads/platform.aspx) vyhledat a stáhnout jiných vývojových nástrojů společnosti Microsoft. Je také zástupce na ploše virtuálního počítače Microsoft Data Science nástroj.  

## <a name="important-directories-on-the-vm"></a>Důležité adresáře na virtuálním počítači

| Položka | Adresář |
| --- | --- |
| Konfigurace serveru Poznámkový blok Jupyter | C:\ProgramData\jupyter |
| Domovský adresář pro ukázky Poznámkový blok Jupyter | C:\dsvm\notebooks a c:\users\\< uživatelské jméno\>\notebooks |
| Další ukázky | c:\dsvm\samples |
| Anaconda, výchozí: Python 3.6 | C:\Anaconda |
| Anaconda Python 2.7 prostředí | C:\Anaconda\envs\python2 |
| Python (samostatně) Server Microsoft Machine Learning | C:\Program Files\Microsoft\ML Server\PYTHON_SERVER |
| Výchozí instance R, Server Machine Learning (samostatně) | C:\Program Files\Microsoft\ML Server\R_SERVER |
| Adresář databáze v instanci SQL služby Machine Learning | C:\Program Files\Microsoft SQL Server\MSSQL14.MSSQLSERVER |
| Různé nástroje | C:\dsvm\tools |

> [!NOTE]
> V edici systému Windows Server 2012, edice DSVM a Windows serveru 2016 před březnem 2018 je výchozí prostředí Anaconda Python 2.7. Sekundární prostředí je Python 3.5, umístění **C:\Anaconda\envs\py35**.

## <a name="next-steps"></a>Další postup

* Prozkoumejte nástroje pro datové vědy virtuálního počítače tak, že vyberete **Start** nabídky.
* Další informace o službě Azure Machine Learning najdete [co je služba Azure Machine Learning?](../service/overview-what-is-azure-ml.md) a vyzkoušejte si [rychlých startů a kurzů](../service/index.yml) , které jsou k dispozici.
* V Průzkumníku souborů přejděte do **C:\Program Files\Microsoft\ML Server\R_SERVER\library\RevoScaleR\demoScripts** pro ukázky, které používají RevoScaleR knihovny v jazyce R, který podporuje analýzy dat v podnikovém měřítku.  
* Přečtěte si článek [deset věcí, které můžete provést na virtuální počítač pro datovou vědu](https://aka.ms/dsvmtenthings).
* Další informace o vytváření analytická řešení začátku do konce systematicky pomocí [vědecké zpracování týmových dat](../team-data-science-process/index.yml).
* Přejděte [galerii Azure AI](http://gallery.cortanaintelligence.com) služeb machine learning a datové analýzy ukázek, které využívají Azure Machine Learning a související data v Azure. Připravili jsme vám také ikona pro tuto galerii na **Start** nabídky a ploše virtuálního počítače.
