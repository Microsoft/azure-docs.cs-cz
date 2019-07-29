---
title: Vytvoření virtuálního počítače Windows datové vědy
titleSuffix: Azure
description: Konfigurace a vytvořit virtuální počítač pro datové vědy v Azure pro účely analýzy a strojového učení.
services: machine-learning
documentationcenter: ''
author: vijetajo
manager: cgronlun
ms.custom: seodec18
ms.assetid: e1467c0f-497b-48f7-96a0-7f806a7bec0b
ms.service: machine-learning
ms.subservice: data-science-vm
ms.workload: data-services
ms.devlang: na
ms.topic: quickstart
ms.date: 02/22/2019
ms.author: vijetaj
ms.openlocfilehash: dfb3e9f6390d4c80b8f3c37b87f2659c671fa823
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/29/2019
ms.locfileid: "68591818"
---
# <a name="provision-a-windows-data-science-virtual-machine-on-azure"></a>Zřízení Data Science Virtual Machine Windows v Azure

Microsoft Windows Data Science Virtual Machine (DSVM) je image virtuálního počítače s Windows serverem 2016 v Azure, která je předinstalována a nakonfigurovaná pomocí nástrojů pro analýzu dat a strojového učení.

## <a name="included-data-science-tools"></a>Zahrnuté nástroje pro datové vědy

DSVM jsou součástí těchto nástrojů:

* [Služba Azure Machine Learning](../service/index.yml) Python SDK
* [Microsoft Machine Learning Server](https://docs.microsoft.com/machine-learning-server/index) Developer Edition
* Anacondu, distribuci jazyka Python
* Jupyter Notebook s jádry R, Python a PySpark
* Microsoft Visual Studio Community
* Power BI Desktop Microsoftu
* Edice Microsoft SQL Server 2017 Developer Edition
* Samostatná instance Apache Spark pro místní vývoj a testování
* [JuliaPro](https://juliacomputing.com/products/juliapro.html)
* Machine learning a datové analýzy nástroje:
  * Architektury hloubkového učení – pro virtuální počítač jsou k dispozici bohatou sadu rozhraní AI: [Microsoft Cognitive Toolkit](https://www.microsoft.com/en-us/cognitive-toolkit/), [TensorFlow](https://www.tensorflow.org/), [chainer](https://chainer.org/), mxNet a Keras
  * [Pro dostupné](https://github.com/JohnLangford/vowpal_wabbit) – rychlý systém strojového učení, který podporuje techniky jako online hashing, allreduce, snižování, learning2search a aktivní a interaktivní učení
  * [XGBoost](https://xgboost.readthedocs.org/en/latest/) – nástroj, který poskytuje rychlou a přesnou zesílenou implementaci stromu.
  * [Rattle](https://togaware.com/rattle/) – analytický nástroj R, který vám pomůže začít s analýzou dat a strojovým učením v R. Zahrnuje zkoumání a modelování dat na základě grafického uživatelského rozhraní s automatickým generováním kódu R.
  * [Weka](https://www.cs.waikato.ac.nz/ml/weka/) – vizuální dolování dat a software strojového učení v jazyce Java
  * [Přejít](https://drill.apache.org/) na web Apache – Nástroj pro dotaz SQL bez schématu pro Apache Hadoop, NoSQL a cloudové úložiště. Podporuje rozhraní ODBC a JDBC pro dotazování NoSQL a soubory ze standardních nástrojů BI, jako je Power BI, Microsoft Excelu a Tableau.
* Knihovny v R a Python pro použití v Azure Machine Learning a dalšími službami Azure
* Git, včetně Git Bash pro práci s úložišť zdrojového kódu, včetně Githubu a Azure DevOps. Git poskytuje několik oblíbených Linux nástroje příkazového řádku, které jsou dostupné i na Git Bash a příkazový řádek. Příklady jsou awk sed, perl, grep, hledání, wget a curl.

### <a name="about-data-science"></a>O datové vědy

Vědecké zpracování dat zahrnuje iterace v pořadí úloh:

1. Hledání, načítání a předzpracování dat
1. Modely sestavení a testování
1. Nasazení modelů pro spotřebu v inteligentních aplikacích

Odborníci přes data použít několik nástrojů pro tyto úlohy. Může být časově najít odpovídající verze softwaru a pak si stáhnout a nainstalovat je. DSVM šetří čas tím, že poskytuje image připravená k použití, která se dá zřídit v Azure s několika oblíbenými nástroji, které jsou předinstalované a nakonfigurované.

DSVMový odkaz – spustí projekt analýzy. Můžete pracovat na úkoly v různých jazycích, včetně R, Python, SQL a C#. Visual Studio nabízí snadno použitelné integrované vývojové prostředí (IDE) pro vývoj a testování kódu. Sada Azure SDK je součástí virtuálního počítače, takže můžete sestavovat aplikace pomocí různých služeb na cloudové platformě Microsoftu.

Neúčtují žádné poplatky softwaru pro image virtuálního počítače Toto datové vědy. Platíte jenom poplatky za využití Azure. Jsou závislé na velikosti virtuálního počítače, které zřizujete. Další podrobnosti o poplatky za výpočetní prostředky jsou v **podrobnosti o cenách** části na [virtuální počítač pro datové vědy](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.dsvm-windows) stránky.

### <a name="other-dsvm-versions"></a>Další verze DSVM

* [Ubuntu](dsvm-ubuntu-intro.md) bitové kopie. Obsahuje celou řadu nástrojů, které jsou podobné pro datové VĚDY a navíc několik dalších hloubkového učení architektury.
* A [Linux CentOS](linux-dsvm-intro.md) bitové kopie.
* [Edice systému Windows Server 2012](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.dsvm-windows) o virtuální počítač pro datové vědy. Několik nástroje jsou k dispozici pouze v edici Windows serveru 2016. V opačném případě v tomto článku platí také pro edice Windows serveru 2012.

## <a name="prerequisite"></a>Požadavek

Chcete-li vytvořit virtuální počítač Microsoft datové vědy, musíte mít předplatné Azure. Viz [Získání bezplatné zkušební verze Azure](https://azure.com/free).

## <a name="create-your-dsvm"></a>Vytvoření DSVM

Vytvoření instance DSVM:

1. Přejít na virtuální počítač výpisu na [Azure Portal](https://portal.azure.com/#create/microsoft-dsvm.dsvm-windowsserver-2016). Může se zobrazit výzva, abyste se přihlásili ke svému účtu Azure, pokud ještě nejste přihlášení.
1. V dolní části vyberte tlačítko **vytvořit** .

   ![Konfigurace data vědy vm](./media/provision-vm/configure-data-science-virtual-machine.png)

1. Je nutné zadat následující informace ke konfiguraci každého z kroků zobrazených v pravém podokně snímku obrazovky:

   1. **Základy**:
      * **Name (název**): název DSVM
      * **Typ disku virtuálního počítače**: **SSD** nebo **HDD**. Instance NC_v1 GPU jako NVidia Tesla K80 na základě zvolte **HDD** jako typ disku.
      * **Uživatelské jméno**: ID účtu správce
      * **Heslo**: heslo účtu správce
      * **Předplatné**: Pokud máte více předplatných, vyberte ten, ve které je vytvořené a fakturuje počítač.
      * **Skupina prostředků**. Vytvořit nové nebo použijte existující skupinu.
      * **Umístění**. Vyberte datové centrum, které je nejvhodnější. Nejrychlejší přístup k síti je většina vašich dat nebo je nejblíže vašemu fyzickému umístění datového centra.
   1. **Velikost**: Vyberte jeden z typů serveru, které splňuje požadavky na funkce a omezení nákladů. Další možnosti velikostí virtuálních počítačů, vyberte **zobrazení všech**.
   1. **Nastavení**:  
      * **Použití spravovaných disků**. Zvolte **spravované** Pokud chcete Azure ke správě disků pro virtuální počítač. Pokud ne, je třeba zadat účet nového nebo existujícího úložiště.  
      * **Další parametry**. Můžete použít výchozí hodnoty. Pokud chcete použít jiné než výchozí hodnoty, najeďte na informační odkaz a podrobnější informace o konkrétním poli.
   1. **Souhrn**: Ověřte, zda všechny informace, které jste zadali správný. Vyberte **Vytvořit**.

> [!NOTE]
> * U virtuálního počítače se neúčtují žádné další poplatky nad výpočetní náklady na velikost serveru, kterou jste zvolili v kroku **Velikost** .
> * Zřizování trvá asi 10 až 20 minut. Stav virtuálního počítače můžete zobrazit na Azure Portal.

## <a name="how-to-access-the-dsvm"></a>Jak přistupovat k DSVM

Po vytvoření a zřízení virtuálního počítače je možné vzdálené plochy do něj pomocí přihlašovacích údajů účtu správce, které jste nakonfigurovali v předchozím **Základy** oddílu. Jste připraveni začít používat nástroje, které jsou nainstalované a nakonfigurované na virtuálním počítači. Mnohé z těchto nástrojů jsou dostupné prostřednictvím dlaždic v nabídce Start a ikon na ploše.

K Azure Notebooks můžete také připojit Data Science VM ke spuštění poznámkových bloků Jupyter na virtuálním počítači a obejít omezení úrovně bezplatné služby. Další informace najdete v tématu [Správa a konfigurace projektů poznámkových bloků – výpočetní vrstva](../../notebooks/configure-manage-azure-notebooks-projects.md#compute-tier).

## <a name="tools-installed-on-the-microsoft-data-science-virtual-machine"></a>Nástroje nainstalované na Microsoft virtuální počítač pro datové vědy

Přečtěte si další informace o nástrojích, které jsou nainstalované na DSVM:

### <a name="microsoft-machine-learning-server-developer-edition"></a>Microsoft Machine Learning Server Developer edition

Microsoft Enterprise Library pro škálovatelné R nebo Python můžete použít pro analýzu protože Machine Learning Server Developer edition je nainstalovaný na virtuálním počítači. Dříve označované jako Microsoft R Server Machine Learning Server je široce nasazená analytická platforma na podnikové úrovni. Je k dispozici pro R i Python. Je to také škálovatelné, komerčně podporované a zabezpečené.

Machine Learning Server podporuje různé statistiky pro velké objemy dat, prediktivní modelování a strojové učení úlohy. Podporuje široké spektrum analytických: zkoumání, analýzu, vizualizaci a modelování. Díky používání a rozšiřování open-source jazyka R a Python, Machine Learning Server je kompatibilní se skripty R a Python a funkce. Je také kompatibilní s CRAN, pip a balíčků Conda, k analýze dat v podnikovém měřítku.

Machine Learning Server řeší přidáním paralelní a blokového zpracování dat v paměti omezení open-source systému R. To znamená, že můžete provádět analýzy mnohem většího množství dat, než kolik jich zapadá do hlavní paměti. Visual Studio Community je zahrnutá ve virtuálním počítači. Má rozšíření R Tools for Visual Studio a Python Tools for Visual Studio (PTVS), která poskytují úplné integrované vývojové prostředí (IDE) pro práci s R nebo Pythonem. Poskytujeme také další prostředí IDE, jako jsou [RStudio](https://www.rstudio.com) a [PyCharm Community edition](https://www.jetbrains.com/pycharm/) na virtuálním počítači.

### <a name="python"></a>Python

Pro vývoj pomocí Pythonu jsou nainstalované distribuce Pythonu Anaconda 2,7 a 3,6. Tyto distribuce mít základní Python spolu s přibližně 300 nejoblíbenějších balíčků analytics matematické, inženýrství a data. Slouží k PTVS, který je nainstalovaný v rámci Visual Studio Community 2017. Nebo můžete použít jednu ze součástí Anaconda jako nečinný nebo Spyder integrovanými vývojovými prostředími. Vyhledejte a spusťte jeden z těchto balíčků (Win + S).

> [!NOTE]
> Tak, aby odkazoval nástroje Pythonu pro Visual Studio na Anaconda Python 2.7, je potřeba vytvořit vlastní prostředí pro každou verzi. Chcete-li nastavit tyto cesty prostředí Visual Studio 2017 Community, přejděte na **nástroje** > **nástroje Python Tools** > **prostředí Pythonu**. Potom vyberte **+ vlastní**.

Anaconda Python 3.6 je nainstalované v **C:\Anaconda**. Anaconda Python 2.7 je nainstalované v **c:\Anaconda\envs\python2**. Podrobné pokyny najdete v článku [dokumentace k PTVS](https://docs.microsoft.com/visualstudio/python/installing-python-interpreters).

### <a name="the-jupyter-notebook"></a>Poznámkový blok Jupyter

Anaconda distribuce také součástí Poznámkový blok Jupyter, prostředí pro sdílení kódu a analýzy. Python 2.7 Python 3.x, PySpark, Julia a R jádrech je předem nakonfigurovány aplikace Jupyter Notebook server. Pokud chcete spustit server Jupyter a spustit prohlížeč pro přístup k poznámkovému serveru, použijte ikonu **Jupyter notebook** Desktop.

Balíček jsme několik ukázkové poznámkové bloky v Pythonu a R. Po získání přístupu Jupyter poznámkových bloků ukazují, jak pracovat s tyto technologie:

* Machine Learning Server
* SQL Server Machine Learning Services, in-Database Analytics
* Python
* Sada nástrojů Microsoft pro rozpoznávání
* Tensorflow
* Další technologie Azure

Odkaz na ukázky na domovské stránce poznámkového bloku najdete po ověření k Jupyter Notebook pomocí hesla, které jste vytvořili v předchozím kroku.

### <a name="visual-studio-community-2017"></a>Visual Studio Community 2017

DSVM zahrnuje Visual Studio Community. Toto je bezplatná verze oblíbeného integrovaného vývojového prostředí (IDE) od Microsoftu, kterou můžete použít pro účely testování a pro malé týmy. Zobrazit [licenční podmínky](https://www.visualstudio.com/support/legal/mt171547).

Otevřete Visual Studio pomocí ikony na ploše nebo nabídky **Start** . Hledání pro programy (Win + S), za nímž následuje **sady Visual Studio**. Odtud můžete vytvářet projekty v jazycích C#, Python, R a node.js. Nainstalovaných modulů plug-in usnadnit práci s těmito službami Azure:

* Katalog dat Azure
* Azure HDInsight Hadoop a Spark
* Azure Data Lake

K dispozici je také modul plug-in s názvem **Azure Machine Learning pro Visual Studio Code** , který se bez problémů integruje do Azure Machine Learning a pomůže vám rychle vytvářet aplikace AI.

> [!NOTE]
> Může se zobrazit zpráva, že vypršela platnost vašeho zkušebního období. Zadejte svoje přihlašovací údaje účtu Microsoft. Nebo vytvořte nový bezplatný účet, abyste získali přístup k Visual Studio Community.

### <a name="sql-server-2017-developer-edition"></a>SQL Server 2017 Developer edition

DSVM obsahuje vývojářskou verzi SQL Server 2017 s Machine Learning Services. Tato edice SQL Server přichází v R nebo Pythonu a může běžet v analýze databáze. Služby Machine Learning poskytuje platformu pro vývoj a nasazování inteligentních aplikací. Tyto jazyky a mnoho balíčků od komunity slouží k vytváření modelů a generovat předpovědi pro data systému SQL Server. Analytics blízko data můžete zachovat, protože služby Machine Learning, v databázi, integruje jazyky R i Python v rámci SQL serveru. Tato integrace se eliminují náklady a bezpečnostní rizika spojená s přesuny dat.

> [!NOTE]
> SQL Server Developer edition je pouze pro účely vývoje a testování. Budete potřebovat licenci k použití v produkčním prostředí.

SQL Server dostanete spuštěním Microsoft SQL Server Management Studio. Název vašeho virtuálního počítače je vyplněna jako **název serveru**. Windows ověřování použijte, když se přihlásíte jako správce na Windows. Až budete v aplikaci SQL Server Management Studio, vytvořit další uživatele, vytvářet databáze, umožňuje importovat data a spouštět dotazy SQL.

Povolit analýzu v databázi s využitím služeb Machine Learning SQL, spusťte následující příkaz jako jednorázová akce v aplikaci SQL Server Management Studio po přihlášení jako správce serveru:

```
CREATE LOGIN [%COMPUTERNAME%\SQLRUserGroup] FROM WINDOWS
```

Nahraďte `%COMPUTERNAME%` názvem svého virtuálního počítače.

### <a name="azure"></a>Azure

Několik nástrojů pro Azure jsou nainstalované na virtuálním počítači:

* Zástupce na ploše přejde na dokumentaci k sadě Azure SDK.
* Pomocí **AzCopy** můžete zkopírovat data z účtu Azure Storage a z něj. Pokud chcete zobrazit využití, zadejte **Azcopy** z příkazového řádku.
* Použití **Průzkumníka služby Azure Storage** procházet objekty, které ukládáte ve svém účtu úložiště Azure. Také kopíruje data do a z Azure Storage. Chcete-li získat přístup k tomuto nástroji,  zadejte do vyhledávacího pole **Průzkumník služby Storage** . Nebo ho vyhledejte v nabídce **Start** systému Windows.
* **AdlCopy** kopíruje data do Azure Data Lake. Pokud chcete zobrazit využití, zadejte **adlcopy** v příkazovém řádku.
* **dtui** kopíruje data do a z Azure Cosmos DB databáze NoSQL v cloudu. Zadejte **dtui** v příkazovém řádku.
* **Azure Data Factory Integration runtime** kopíruje data mezi místními zdroji dat a cloudem. Používá se v rámci nástroje, jako je Azure Data Factory.
* Pomocí **Microsoft Azure PowerShell** můžete spravovat prostředky Azure ve skriptovacím jazyce PowerShellu. Také se nainstaluje na váš virtuální počítač.

### <a name="power-bi"></a>Power BI

DSVM se dodává s nainstalovanou **Power BI Desktop** , která vám pomůžou vytvářet řídicí panely a vizualizace. Pomocí tohoto nástroje k získání dat z různých zdrojů, vytvářet řídicí panely a sestavy a publikovat je do cloudu. Další informace najdete v tématu [Power BI](https://powerbi.microsoft.com) lokality. Můžete najít Power BI Desktop v nabídce **Start** .

> [!NOTE]
> Budete potřebovat účet Microsoft Office 365 pro přístup k Power BI.

### <a name="azure-machine-learning-service-python-sdk"></a>Služba Azure Machine Learning Python SDK

Odborníci přes data a vývojáři AI používají Azure Machine Learning SDK pro Python k sestavování a spouštění pracovních postupů strojového učení pomocí [služby Azure Machine Learning](../service/overview-what-is-azure-ml.md). Můžete komunikovat se službou v poznámkových blocích Jupyter nebo jiném integrovaném vývojovém prostředí Pythonu s využitím open source platforem, jako je TensorFlow a scikit-učení.

Chcete-li začít používat sadu Python SDK, přečtěte si téma [použití Pythonu k seznámení s Azure Machine Learning](../service/quickstart-create-workspace-with-python.md).

Python SDK je předinstalován v Microsoftu virtuální počítač pro datové vědy.

## <a name="more-microsoft-development-tools"></a>Další nástroje pro vývoj Microsoft

Pomocí [Instalace webové platformy Microsoft](https://www.microsoft.com/web/downloads/platform.aspx) můžete najít a stáhnout další vývojové nástroje Microsoftu. Je také zástupce nástroje na webu Microsoft Data Science Virtual Machine Desktop.  

## <a name="important-directories-on-the-vm"></a>Důležité adresáře na virtuálním počítači

| Položka | Adresář |
| --- | --- |
| Konfigurace serveru Poznámkový blok Jupyter | C:\ProgramData\jupyter |
| Domovský adresář pro ukázky Poznámkový blok Jupyter | C:\dsvm\notebooks a c:\Users\\< UserName\>\notebooks |
| Další ukázky | C:\dsvm\samples |
| Anaconda, výchozí: Python 3,6 | C:\Anaconda |
| Anaconda Python 2.7 prostředí | C:\Anaconda\envs\python2 |
| Python (samostatně) Server Microsoft Machine Learning | C:\Program Files\Microsoft\ML Server\PYTHON_SERVER |
| Výchozí instance R, Server Machine Learning (samostatně) | C:\Program Files\Microsoft\ML Server\R_SERVER |
| Adresář databáze v instanci SQL služby Machine Learning | C:\Program Files\Microsoft SQL Server\MSSQL14.MSSQLSERVER |
| Různé nástroje | C:\dsvm\tools |

> [!NOTE]
> V edici systému Windows Server 2012, edice DSVM a Windows serveru 2016 před březnem 2018 je výchozí prostředí Anaconda Python 2.7. Sekundární prostředí je Python 3,5, který se nachází na adrese **C:\Anaconda\envs\py35**.

## <a name="next-steps"></a>Další kroky

* Prozkoumejte nástroje pro datové vědy virtuálního počítače tak, že vyberete **Start** nabídky.
* Další informace o službě Azure Machine Learning najdete [co je služba Azure Machine Learning?](../service/overview-what-is-azure-ml.md) a vyzkoušejte si [rychlých startů a kurzů](../service/index.yml) , které jsou k dispozici.
* V Průzkumníku souborů přejděte do složky **C:\Program Files\Microsoft\ML Server\R_SERVER\library\RevoScaleR\demoScripts** , kde najdete ukázky, které používají knihovnu RevoScaleR v jazyce R, která podporuje analýzu dat v rámci podnikové škály.  
* Přečtěte si článek [deset věcí, které můžete provést na virtuální počítač pro datovou vědu](https://aka.ms/dsvmtenthings).
* Další informace o vytváření analytická řešení začátku do konce systematicky pomocí [vědecké zpracování týmových dat](../team-data-science-process/index.yml).
* Přejděte [galerii Azure AI](https://gallery.cortanaintelligence.com) služeb machine learning a datové analýzy ukázek, které využívají Azure Machine Learning a související data v Azure. Připravili jsme vám také ikona pro tuto galerii na **Start** nabídky a ploše virtuálního počítače.
