---
title: 'Rychlý start: Vytvoření Windows'
titleSuffix: Azure Data Science Virtual Machine
description: Konfigurace a vytvořit virtuální počítač pro datové vědy v Azure pro účely analýzy a strojového učení.
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: vijetajo
ms.author: vijetaj
ms.topic: quickstart
ms.date: 02/22/2019
ms.openlocfilehash: 6c0abc20775e604f9ea1c6ba882e5a9ff1d89e90
ms.sourcegitcommit: 532335f703ac7f6e1d2cc1b155c69fc258816ede
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/30/2019
ms.locfileid: "70195298"
---
# <a name="quickstart-set-up-a-windows-data-science-virtual-machine-on-azure"></a>Rychlý start: Nastavení Data Science Virtual Machine Windows v Azure

Microsoft Windows Data Science Virtual Machine (DSVM) je image virtuálního počítače (VM) Windows serveru 2016 v Azure. Je součástí předinstalovaného a nakonfigurovaného pomocí nástrojů pro analýzu dat a strojové učení.

## <a name="included-data-science-tools"></a>Zahrnuté nástroje pro datové vědy

DSVM jsou součástí těchto nástrojů:

* Python SDK pro [službu Azure Machine Learning](../service/index.yml).
* [Microsoft Machine Learning Server](https://docs.microsoft.com/machine-learning-server/index) Developer edition.
* Anaconda distribuci jazyka Python.
* Poznámkový blok Jupyter s R, Python nebo PySpark jádrech.
* Microsoft Visual Studio Community.
* Microsoft Power BI Desktop.
* Microsoft SQL Server 2017 Developer edition.
* Apache Spark samostatná pro místní vývoj a testování.
* [JuliaPro](https://juliacomputing.com/products/juliapro.html).
* Machine learning a datové analýzy nástroje:
  * Architektury hloubkového učení: Do virtuálního počítače jsou zahrnuté architektury [TensorFlow](https://www.tensorflow.org/), [chainer](https://chainer.org/), MXNet a Keras AI.
  * [Pro dostupné](https://github.com/JohnLangford/vowpal_wabbit): Rychlé strojového učení systém, který podporuje postupů, jako jsou online algoritmu hash, allreduce, snížení, learning2search a aktivní a interaktivní učení.
  * [XGBoost](https://xgboost.readthedocs.org/en/latest/): Nástroj, který poskytuje rychlý a přesné Posílený stromu implementaci.
  * [Rattle](https://togaware.com/rattle/). Analytický nástroj R, který vám pomůže začít s analýzou dat a strojovým učením v R. Zahrnuje zkoumání a modelování dat na základě grafického uživatelského rozhraní s automatickým generováním kódu R.
  * [Weka](https://www.cs.waikato.ac.nz/ml/weka/): Dolování vizuálních dat a strojové učení softwaru v jazyce Java.
  * [Podrobnosti o Apache](https://drill.apache.org/): Bez schémat modul dotazů SQL pro Apache Hadoop, NoSQL a cloudového úložiště. Podporuje rozhraní ODBC a JDBC pro dotazování NoSQL a soubory ze standardních nástrojů BI, jako je Power BI, Microsoft Excelu a Tableau.
* Knihovny v R a Python pro použití v Azure Machine Learning a dalšími službami Azure.
* Git, včetně Git bash, pro práci s úložišti zdrojového kódu, jako je GitHub a Azure DevOps. Git nabízí několik oblíbených nástrojů příkazového řádku pro Linux, které jsou přístupné v Gitu bash i v příkazovém řádku. Příklady jsou awk sed, perl, grep, hledání, wget a curl.
* Vývojové nástroje a editory (RStudio, PyCharm).

### <a name="about-data-science"></a>O datové vědy

Vědecké zpracování dat zahrnuje iterace v pořadí úloh:

1. Najít a načíst předběžně zpracovat data.
1. Sestavení a otestování modelů.
1. Nasazení modelů pro použití v inteligentních aplikacích.

Odborníci přes data použít několik nástrojů pro tyto úlohy. Může být časově najít odpovídající verze softwaru a pak si stáhnout a nainstalovat je. DSVM šetří čas tím, že poskytuje image připravená k použití, kterou je možné zřídit v Azure a několik oblíbených nástrojů, které jsou předinstalované a nakonfigurované.

DSVMový odkaz – spustí projekt analýzy. Můžete pracovat na úkolech v různých jazycích, včetně R, Pythonu, SQL a C#. Visual Studio nabízí snadno použitelné integrované vývojové prostředí (IDE) pro vývoj a testování kódu. Sada Azure SDK je součástí virtuálního počítače, takže můžete sestavovat aplikace pomocí služeb na platformě Microsoft Cloud Platform.

Pro tuto DSVM image se neúčtují žádné poplatky za software. Platíte jenom poplatky za využití Azure. Závisí na velikosti virtuálního počítače, který zřídíte. Další informace najdete na [stránce Data Science Virtual Machine](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.dsvm-windows).

### <a name="other-dsvm-versions"></a>Další verze DSVM

* [Ubuntu](dsvm-ubuntu-intro.md) bitové kopie. Obsahuje nástroje, které jsou podobné DSVM a navíc několik architektur hloubkového učení.
* A [Linux CentOS](linux-dsvm-intro.md) bitové kopie.
* [Edice systému Windows Server 2012](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.dsvm-windows) o virtuální počítač pro datové vědy. Několik nástroje jsou k dispozici pouze v edici Windows serveru 2016. V opačném případě v tomto článku platí také pro edice Windows serveru 2012.

## <a name="prerequisite"></a>Požadavek

Chcete-li vytvořit virtuální počítač Microsoft datové vědy, musíte mít předplatné Azure. Viz [Získání bezplatné zkušební verze Azure](https://azure.com/free).

## <a name="create-your-dsvm"></a>Vytvoření DSVM

Vytvoření instance DSVM:

1. Přejít na virtuální počítač výpisu na [Azure Portal](https://portal.azure.com/#create/microsoft-dsvm.dsvm-windowsserver-2016). Může se zobrazit výzva, abyste se přihlásili ke svému účtu Azure, pokud ještě nejste přihlášení.
1. V dolní části vyberte tlačítko **vytvořit** .

   ![Výpis virtuálního počítače na Azure Portal s tlačítkem vytvořit](./media/provision-vm/configure-data-science-virtual-machine.png)

1. Zadejte následující informace, chcete-li nakonfigurovat každý z kroků zobrazených v pravém podokně snímku obrazovky:

   1. **Základy**:
      * **Název**: Zadejte název DSVM.
      * **Typ disku virtuálního počítače**: Vyberte buď **SSD** , nebo **HDD**. V případě instance GPU NC_v1, jako je například NVIDIA Tesla K80, vyberte jako typ disku **HDD** .
      * **Uživatelské jméno**: Zadejte ID účtu správce.
      * **Heslo**: Zadejte heslo účtu správce.
      * **Předplatné:** Pokud máte více než jedno předplatné, vyberte ten, na kterém se bude počítač vytvářet a účtují.
      * **Skupina prostředků**: Vytvořte novou skupinu nebo použijte existující.
      * **Umístění**: Vyberte odpovídající datové centrum. Pro nejrychlejší přístup k síti je to datové centrum, které má většinu vašich dat nebo je nejblíže vašemu fyzickému umístění.
   1. **Velikost**: Vyberte typ serveru, který splňuje vaše funkční požadavky a omezení nákladů. Další možnosti velikostí virtuálních počítačů, vyberte **zobrazení všech**.
   1. **Nastavení**:  
      * **Použití spravovaných disků**. Zvolte **spravované** Pokud chcete Azure ke správě disků pro virtuální počítač. Pokud ne, je třeba zadat účet nového nebo existujícího úložiště.  
      * **Další parametry**. Můžete použít výchozí hodnoty. Pokud chcete použít jiné než výchozí hodnoty, najeďte na informační odkaz a podrobnější informace o konkrétním poli.
   1. **Souhrn**: Ověřte, zda všechny informace, které jste zadali správný. Vyberte **Vytvořit**.

> [!NOTE]
> * U virtuálního počítače se neúčtují žádné další poplatky nad výpočetní náklady na velikost serveru, kterou jste zvolili v kroku **Velikost** .
> * Zřizování trvá 10 až 20 minut. Stav virtuálního počítače můžete zobrazit na Azure Portal.

## <a name="access-the-dsvm"></a>Přístup k DSVM

Až se virtuální počítač vytvoří a zřídí, můžete k němu přistupovat přes připojení ke vzdálené ploše. Použijte přihlašovací údaje účtu správce, které jste nakonfigurovali v kroku **základní informace** o vytvoření virtuálního počítače. 

Jste připraveni začít používat nástroje, které jsou nainstalované a nakonfigurované na virtuálním počítači. Mnohé z těchto nástrojů jsou dostupné prostřednictvím dlaždic v nabídce **Start** a ikon na ploše.

K Azure Notebooks také můžete připojit DSVM a spustit poznámkové bloky Jupyter na virtuálním počítači a obejít omezení bezplatné úrovně služby. Další informace najdete v tématu [Správa a konfigurace projektů poznámkových bloků](../../notebooks/configure-manage-azure-notebooks-projects.md#compute-tier).

<a name="tools"></a>

## <a name="tools-installed-on-the-dvsm"></a>Nástroje nainstalované na DVSM

V následujících částech se dozvíte víc o nástrojích, které jsou nainstalované na Data Science Virtual Machine.

### <a name="microsoft-machine-learning-server-developer-edition"></a>Microsoft Machine Learning Server Developer edition

Pro analýzu můžete použít Microsoft Enterprise Library, protože na virtuálním počítači je nainstalovaná verze Machine Learning Server Developer Edition. Dříve označované jako Microsoft R Server Machine Learning Server je široce nasazená analytická platforma. Je škálovatelná a komerčně podporovaná.

Machine Learning Server podporuje různé statistiky pro velké objemy dat, prediktivní modelování a strojové učení úlohy. Podporuje široké spektrum analytických: zkoumání, analýzu, vizualizaci a modelování. Díky používání a rozšiřování open-source jazyka R a Python, Machine Learning Server je kompatibilní se skripty R a Python a funkce. Je také kompatibilní s CRAN, pip a balíčků Conda, k analýze dat v podnikovém měřítku.

Machine Learning Server řeší přidáním paralelní a blokového zpracování dat v paměti omezení open-source systému R. To znamená, že můžete provádět analýzy mnohem většího množství dat, než kolik jich zapadá do hlavní paměti. 

Visual Studio Community je zahrnutá ve virtuálním počítači. Má rozšíření R Tools for Visual Studio a Python Tools for Visual Studio (PTVS), která poskytují úplné integrované vývojové prostředí (IDE) pro práci s R nebo Pythonem. Na virtuálním počítači poskytujeme také další prostředí pro IDEs, jako je [RStudio](https://www.rstudio.com) a [PyCharm Community Edition](https://www.jetbrains.com/pycharm/) .

### <a name="python"></a>Python

Pro vývoj pomocí Pythonu jsou nainstalované distribuce Pythonu Anaconda 2,7 a 3,6. Tyto distribuce mít základní Python spolu s přibližně 300 nejoblíbenějších balíčků analytics matematické, inženýrství a data. Můžete použít PTVS, který je nainstalován v aplikaci Visual Studio Community 2017. Nebo můžete použít jeden ze svazků se systémem Anaconda, jako je nečinný nebo Spyder. Vyhledejte a otevřete jeden z těchto balíčků (klávesa S logem Windows + S).

> [!NOTE]
> Tak, aby odkazoval nástroje Pythonu pro Visual Studio na Anaconda Python 2.7, je potřeba vytvořit vlastní prostředí pro každou verzi. Pokud chcete nastavit tyto cesty prostředí v komunitě Visual Studio 2017, přečtěte si v **nabídce nástroje** > **Python Tools** > **Python prostředí**. Potom vyberte **+ vlastní**.

Anaconda Python 3,6 je nainstalován v C:\Anaconda. Anaconda Python 2,7 je nainstalován v C:\Anaconda\envs\python2. Podrobný postup najdete v [dokumentaci k PTVS](https://docs.microsoft.com/visualstudio/python/installing-python-interpreters).

### <a name="the-jupyter-notebook"></a>Poznámkový blok Jupyter

Anaconda distribuce také přináší Jupyter Notebook, prostředí pro sdílení kódu a analýzy. Python 2.7 Python 3.x, PySpark, Julia a R jádrech je předem nakonfigurovány aplikace Jupyter Notebook server. Pokud chcete spustit server Jupyter a otevřít prohlížeč pro přístup k poznámkovému serveru, použijte ikonu **Jupyter notebook** Desktop.

Balíček jsme několik ukázkové poznámkové bloky v Pythonu a R. Po získání přístupu Jupyter poznámkových bloků ukazují, jak pracovat s tyto technologie:

* Machine Learning Server
* SQL Server Machine Learning Services, in-Database Analytics
* Python
* Microsoft Cognitive Toolkit
* TensorFlow
* Další technologie Azure

Odkaz na ukázky na domovské stránce poznámkového bloku najdete po ověření k Jupyter Notebook pomocí hesla, které jste vytvořili dříve.

### <a name="visual-studio-community-2017"></a>Visual Studio Community 2017

DSVM zahrnuje Visual Studio Community. Toto je bezplatná verze oblíbeného integrovaného vývojového prostředí (IDE) od Microsoftu, kterou můžete použít pro účely testování a pro malé týmy. Přečtěte si [licenční smlouvy k softwaru společnosti Microsoft](https://www.visualstudio.com/support/legal/mt171547).

Otevřete Visual Studio pomocí ikony na ploše nebo nabídky **Start** . Vyhledejte programy (klávesu S logem Windows + S) a potom **Visual Studio**. Odtud můžete vytvářet projekty v jazycích, jako C#je, Python, R a Node. js. Nainstalovaných modulů plug-in usnadnit práci s těmito službami Azure:

* Katalog dat Azure
* Azure HDInsight pro Hadoop a Spark
* Azure Data Lake

Modul plug-in s názvem Azure Machine Learning pro Visual Studio Code se také integruje s Azure Machine Learning a pomáhá rychle sestavovat aplikace AI.

> [!NOTE]
> Může se zobrazit zpráva, že vypršela platnost vašeho zkušebního období. Zadejte svoje přihlašovací údaje účtu Microsoft. Nebo vytvořte nový bezplatný účet, abyste získali přístup k Visual Studio Community.

### <a name="sql-server-2017-developer-edition"></a>SQL Server 2017 Developer edition

DSVM obsahuje vývojářskou verzi SQL Server 2017 s Machine Learning Services. Tato edice SQL Server přichází v R nebo Pythonu a může běžet v analýze databáze. 

Služby Machine Learning poskytuje platformu pro vývoj a nasazování inteligentních aplikací. Tyto jazyky a mnoho balíčků od komunity slouží k vytváření modelů a generovat předpovědi pro data systému SQL Server. Analytics blízko data můžete zachovat, protože služby Machine Learning, v databázi, integruje jazyky R i Python v rámci SQL serveru. Tato integrace se eliminují náklady a bezpečnostní rizika spojená s přesuny dat.

> [!NOTE]
> SQL Server Developer edition je pouze pro účely vývoje a testování. Budete potřebovat licenci k použití v produkčním prostředí.

K SQL Server můžete přistupovat otevřením Microsoft SQL Server Management Studio. Název virtuálního počítače se naplní jako **název serveru**. Windows ověřování použijte, když se přihlásíte jako správce na Windows. Až budete v aplikaci SQL Server Management Studio, vytvořit další uživatele, vytvářet databáze, umožňuje importovat data a spouštět dotazy SQL.

Pokud chcete povolit analýzu v databázi pomocí SQL Server Machine Learning Services, spusťte následující příkaz jako jednorázovou akci v SQL Server Management Studio po přihlášení jako správce serveru:

```
CREATE LOGIN [%COMPUTERNAME%\SQLRUserGroup] FROM WINDOWS
```

Nahraďte `%COMPUTERNAME%` názvem svého virtuálního počítače.

### <a name="azure"></a>Azure

Několik nástrojů pro Azure jsou nainstalované na virtuálním počítači:

* Zástupce na ploše přejde na dokumentaci k sadě Azure SDK.
* Pomocí AzCopy můžete kopírovat data z účtu služby Azure Storage a z něj. Pokud chcete zobrazit využití, zadejte **Azcopy** z příkazového řádku.
* Pomocí Průzkumník služby Azure Storage můžete procházet objekty, které ukládáte v účtu úložiště Azure. Také kopíruje data do a z Azure Storage. Chcete-li získat přístup k tomuto nástroji, zadejte do vyhledávacího pole **Průzkumník služby Storage** . Nebo vyhledejte ji na Windows **Start** nabídky.
* AdlCopy kopíruje data do Azure Data Lake. Pokud chcete zobrazit využití, zadejte **adlcopy** v příkazovém řádku.
* Nástroj dtui kopíruje data do a z Azure Cosmos DB databáze NoSQL v cloudu. Zadejte **dtui** v příkazovém řádku.
* Prostředí Integration runtime kopíruje data mezi místními zdroji dat a cloudem. Používá se v rámci nástroje, jako je Azure Data Factory.
* Pomocí Azure PowerShell můžete spravovat prostředky Azure ve skriptovacím jazyce PowerShellu. Také se nainstaluje na váš virtuální počítač.

### <a name="power-bi"></a>Power BI

DSVM se dodává s nainstalovanou Power BI Desktop, která vám pomůžou vytvářet řídicí panely a vizualizace. Pomocí tohoto nástroje k získání dat z různých zdrojů, vytvářet řídicí panely a sestavy a publikovat je do cloudu. Další informace najdete na [webu Power BI](https://powerbi.microsoft.com). Můžete najít Power BI Desktop v nabídce **Start** .

> [!NOTE]
> Budete potřebovat účet Microsoft Office 365 pro přístup k Power BI.

### <a name="azure-machine-learning-sdk-for-python"></a>Sada SDK Azure Machine Learning pro Python

Odborníci přes data a vývojáři AI používají Azure Machine Learning SDK pro Python k sestavování a spouštění pracovních postupů strojového učení pomocí [služby Azure Machine Learning](../service/overview-what-is-azure-ml.md). Můžete komunikovat se službou v poznámkových blocích Jupyter nebo jiném integrovaném vývojovém prostředí Pythonu s využitím open source platforem, jako je TensorFlow a scikit-učení.

Python SDK je předinstalován v Microsoftu virtuální počítač pro datové vědy. Pokud chcete začít používat sadu Python SDK, přečtěte si téma [použití Pythonu a začněte s Azure Machine Learning](../service/quickstart-create-workspace-with-python.md).

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
| Microsoft Machine Learning Server (samostatný) pro Python | C:\Program Files\Microsoft\ML Server\PYTHON_SERVER |
| Výchozí instance jazyka R, Machine Learning Server (samostatně) | C:\Program Files\Microsoft\ML Server\R_SERVER |
| Adresář instance databáze SQL Server Machine Learning Services | C:\Program Files\Microsoft SQL Server\MSSQL14.MSSQLSERVER |
| Různé nástroje | C:\dsvm\tools |

> [!NOTE]
> V systému Windows Server 2012 verze DSVM a edice Windows Server 2016 do března 2018 je výchozím prostředím Anaconda Python 2,7. Sekundární prostředí je Python 3,5, který se nachází na adrese C:\Anaconda\envs\py35.

## <a name="next-steps"></a>Další postup

* Prozkoumejte nástroje na DSVM otevřením nabídky **Start** .
* Přečtěte si o službě Azure Machine Learning, kterou si přečtete, [co je Azure Machine Learning služba?](../service/overview-what-is-azure-ml.md) a vyzkoušíte [rychlé starty a kurzy](../service/index.yml).
* V Průzkumníku souborů přejděte do složky C:\Program Files\Microsoft\ML Server\R_SERVER\library\RevoScaleR\demoScripts, kde najdete ukázky, které používají knihovnu RevoScaleR v jazyce R, která podporuje analýzu dat v podnikovém měřítku. 
* Přečtěte si článek [deset věcí, které můžete provádět na data Science Virtual Machine](https://aka.ms/dsvmtenthings).
* Další informace o vytváření analytická řešení začátku do konce systematicky pomocí [vědecké zpracování týmových dat](../team-data-science-process/index.yml).
* Přejděte [galerii Azure AI](https://gallery.cortanaintelligence.com) služeb machine learning a datové analýzy ukázek, které využívají Azure Machine Learning a související data v Azure. K dispozici jsme také ikonu pro tuto galerii v nabídce **Start** a na ploše virtuálního počítače.
