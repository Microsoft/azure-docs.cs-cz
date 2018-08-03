---
title: Zřídit Windows virtuální počítač pro datové vědy v Azure | Dokumentace Microsoftu
description: Konfigurace a vytvořit virtuální počítač pro datové vědy v Azure pro účely analýzy a strojového učení.
services: machine-learning
documentationcenter: ''
author: gopitk
manager: cgronlun
ms.assetid: e1467c0f-497b-48f7-96a0-7f806a7bec0b
ms.service: machine-learning
ms.component: data-science-vm
ms.workload: data-services
ms.devlang: na
ms.topic: article
ms.date: 09/10/2017
ms.author: gokuma
ms.openlocfilehash: b749d8a904bc40eba3346cc03d9274236380c80d
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/02/2018
ms.locfileid: "39449754"
---
# <a name="provision-the-windows-data-science-virtual-machine-on-azure"></a>Zřídit Windows virtuální počítač pro datové vědy v Azure
Virtuální počítač Microsoft datové vědy je image virtuálního počítače (VM) Windows Azure předem nainstalovaná a nakonfigurovaná s několika oblíbenými nástroji, které se běžně používají pro analýzu dat a strojové učení. Nástroje sady jsou:

* [Azure Machine Learning](../service/index.yml) aplikaci Workbench
* [Microsoft Machine Learning Server](https://docs.microsoft.com/machine-learning-server/index) Developer Edition
* Anacondu, distribuci jazyka Python
* Poznámkový blok Jupyter (s jazykem R, Python, jádra PySpark)
* Visual Studio Community Edition
* Power BI Desktop
* SQL Server 2017 Developer Edition
* Spark samostatné instance pro místní vývoj a testování
* [JuliaPro](https://juliacomputing.com/products/juliapro.html)
* Machine learning a nástroje pro analýzu dat
  * Obsáhlý Learning architektury: celou řadu architektur AI včetně [Microsoft Cognitive Toolkit](https://www.microsoft.com/en-us/cognitive-toolkit/), [TensorFlow](https://www.tensorflow.org/), [Chainer](https://chainer.org/), mxNet, Keras jsou zahrnuty ve virtuálním počítači.
  * [Vowpal Wabbit](https://github.com/JohnLangford/vowpal_wabbit): rychlé strojového učení systému, který podporuje techniky, jako je online, hash, allreduce, snížení, learning2search, aktivní a interaktivního vzdělávacího.
  * [XGBoost](https://xgboost.readthedocs.org/en/latest/): nástroj poskytuje rychlý a přesné Posílený stromu implementace.
  * [Rattle](http://rattle.togaware.com/) (R analytické nástroje pro další snadno): nástroj, který umožňuje Začínáme se službou analýzy dat a strojové učení v jazyce R snadné. Zahrnuje zkoumání a modelování s automatické generování kódu R dat využívající grafické rozhraní.
  * [Weka](http://www.cs.waikato.ac.nz/ml/weka/) : dolování vizuálních dat a strojové učení softwaru v jazyce Java.
  * [Apache Drill](https://drill.apache.org/): bez schémat modul dotazů SQL pro Hadoop, NoSQL a cloudového úložiště.  Podporuje rozhraní ODBC a JDBC a umožňuje dotazování NoSQL a soubory ze standardních nástrojů BI, jako je Power BI, Excel, Tableau.
* Knihovny v R a Python pro použití v Azure Machine Learning a dalšími službami Azure
* Git, včetně Git Bash pro práci s úložišť zdrojového kódu, včetně Githubu a Visual Studio Team Services a poskytuje několik oblíbených Linux nástroje příkazového řádku (včetně awk, sed, perl, grep, hledání, wget, curl, atd.) dostupný na git bash a příkaz řádek. 

Datových věd zahrnuje iterace v pořadí úloh:

1. Vyhledání, načítání a předzpracování dat
1. Vytváření a testování modely
1. Nasazení modelů pro použití v inteligentních aplikacích

Odborníci přes data pomocí různých nástrojů k provedení těchto úloh. Může trvat poměrně dlouho najít odpovídající verze softwaru a pak si stáhnout a nainstalovat je. Virtuální počítač Microsoft datové vědy můžete usnadňují tato zatížení zadáním bitovou kopii připravené k použití, která se dá zřídit v Azure pomocí všech oblíbených nástrojů předem nainstalovaná a nakonfigurovaná. 

Virtuální počítač Microsoft Data Science jump-starts analytického projektu. Umožňuje vám pro práci na úkolech v různých jazycích, včetně R, Python, SQL a C#. Visual Studio poskytuje integrované vývojové prostředí pro vývoj a testování kódu, který se snadno používá. Zahrnuté ve virtuálním počítači Azure SDK umožňuje vytvářet aplikace pomocí různých služeb na cloudové platformě Microsoftu. 

Neúčtují žádné poplatky softwaru pro image virtuálního počítače Toto datové vědy. Platíte jenom za poplatky za využití Azure které závisí na velikosti virtuálního počítače, které zřizujete. Další podrobnosti o poplatky za výpočetní prostředky můžete najít v části Podrobnosti o cenách na [virtuální počítač pro datové vědy](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-ads.windows-data-science-vm?tab=PlansAndPrice) stránky. 

## <a name="other-versions-of-the-data-science-virtual-machine"></a>Jiné verze virtuální počítač pro datové vědy
[Ubuntu](dsvm-ubuntu-intro.md) image je k dispozici také s mnoha podobné nástroje a několik dalších hloubkového učení architektury. A [CentOS](linux-dsvm-intro.md) image je také k dispozici. Nabízíme také [edice systému Windows Server 2012](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-ads.standard-data-science-vm) z virtuálního počítače pro datové vědy ale několik nástroje jsou k dispozici pouze v edici Windows serveru 2016.  V opačném případě v tomto článku platí také pro edice Windows serveru 2012.

## <a name="prerequisites"></a>Požadavky
Než vytvoříte virtuální počítač Microsoft datové vědy, musíte mít následující:

* **Předplatné Azure**: ho získat, najdete v článku [získání bezplatné zkušební verze Azure](http://azure.com/free).


## <a name="create-your-microsoft-data-science-virtual-machine"></a>Vytvoření virtuálního počítače s Microsoft Data Science
K vytvoření instance z Microsoft virtuální počítač pro datové vědy, postupujte podle těchto kroků:

1. Přejděte k virtuálnímu počítači na [webu Azure portal](https://portal.azure.com/#create/microsoft-ads.windows-data-science-vmwindows2016).
1. Vyberte **vytvořit** tlačítko v dolní části mají být provedeny do průvodce.![ Konfigurace data vědy vm](./media/provision-vm/configure-data-science-virtual-machine.png)
1. Vyžaduje průvodce slouží k vytvoření virtuálního počítače Microsoft Data Science **vstupy** pro každou **čtyři kroky** uvedené na pravé straně tohoto obrázku. Tady jsou vstupy potřebné ke konfiguraci každý z těchto kroků:
   
   1. **Základy**
      
      1. **Název**: název serveru datové vědy vytváříte.
      1. **Typ disku virtuálního počítače**: výběr mezi SSD nebo pevný disk. Pro NC_v1 GPU instance (NVidia Tesla K80 na základě), zvolte **HDD** jako typ disku. 
      1. **Uživatelské jméno**: id přihlášení účtu správce.
      1. **Heslo**: heslo účtu správce.
      1. **Předplatné**: Pokud máte více předplatných, vyberte ten, ve které je vytvořené a fakturuje počítač.
      1. **Skupina prostředků**: vytvořit nové nebo použijte existující skupinu.
      1. **Umístění**: Vyberte datové centrum, které je nejvhodnější. Obvykle se většina dat nebo je nejblíže vašemu fyzickému umístění pro nejrychlejší přístup k síti datového centra.
   1. **Velikost**: Vyberte jeden z typů serveru, které splňuje požadavek na funkční a náklady na omezení. Další možnosti velikostí virtuálních počítačů můžete získat tak, že vyberete "Zobrazit vše".
   1. **Nastavení**:
      
      1. **Používat službu Managed Disks**: Zvolte spravovat, pokud chcete Azure ke správě disků pro virtuální počítač.  Jinak budete muset zadat účet nového nebo existujícího úložiště. 
      1. **Další parametry**: obvykle stačí použít výchozí hodnoty. Pokud chcete zvažte použití jiné než výchozí hodnoty, najeďte myší na informační odkaz nápovědy pro konkrétní pole.
    a. **Souhrn**: Ověřte správnost všechny informace, které jste zadali a klikněte na tlačítko **vytvořit**. **Poznámka:**: virtuální počítač nemá žádné další poplatky za výpočetní prostředky pro velikost serveru, kterou jste zvolili v **velikost** kroku. 

> [!NOTE]
> Zřizování by měla trvat asi 10-20 minut. Stav zřizování se zobrazí na portálu Azure portal.
> 
> 

## <a name="how-to-access-the-microsoft-data-science-virtual-machine"></a>Jak získat přístup k Microsoft Data Science virtuálního počítače
Po vytvoření virtuálního počítače můžete do něj pomocí přihlašovacích údajů účtu správce, které jste nakonfigurovali v předchozím vzdálené plochy **Základy** oddílu. 

Jakmile váš virtuální počítač je vytvořen a zřízené, jste připraveni začít používat nástroje, které jsou nainstalované a nakonfigurované na něm. Existují dlaždic nabídky start a ikony na ploše pro celou řadu nástrojů. 


## <a name="tools-installed-on-the-microsoft-data-science-virtual-machine"></a>Nástroje nainstalované na Microsoft virtuální počítač pro datové vědy



### <a name="microsoft-ml-server-developer-edition"></a>Microsoft ML Server Developer Edition
Pokud chcete používat Microsoft enterprise knihovny pro škálovatelné R nebo Python pro analýzu, má virtuální počítač Microsoft ML Server Developer edition (dříve označovanou jako Microsoft R Server) nainstalovány. Microsoft ML Server je široce nasaditelný podnikové analytická platforma pro R i Python k dispozici a je škálovatelný, komerčně podporované a zabezpečený. Podporující celou řadu statistiky pro velké objemy dat, prediktivní modelování a schopnosti strojového učení, ML Server podporuje široké spektrum analytických – zkoumání, analýzu, vizualizaci a modelování. Díky používání a rozšiřování open source jazyka R a Python, Microsoft ML Server je plně kompatibilní s jazykem R a Python skripty, funkce a CRAN / nástroje pip snížit nebo navýšit balíčků Conda, k analýze dat v podnikovém. Také řeší omezení Open Source systému R v paměti tak, že přidáte paralelního a blokového zpracování dat. To umožňuje provádět analýzu na datech mnohem větší, než se vejde do paměti.  Visual Studio Community Edition na virtuální počítač obsahuje nástroje R pro Visual Studio a Python tools pro rozšíření sady Visual Studio, která poskytuje kompletního integrovaného vývojového prostředí pro práci s R nebo Python. Poskytujeme také jiná Integrovaná vývojová prostředí, stejně jako [RStudio](http://www.rstudio.com) a [PyCharm Community edition](https://www.jetbrains.com/pycharm/) na virtuálním počítači. 

### <a name="python"></a>Python
Pro vývoj pomocí Pythonu byl nainstalován distribuce Anaconda Python 2.7 i 3.6. Toto rozdělení obsahuje základní Python spolu s přibližně 300 nejoblíbenějších balíčků analytics matematické, inženýrství a data. Můžete použít nástroje Pythonu pro Visual Studio (PTVS), který je nainstalován v rámci Visual Studio 2017 Community edition nebo jeden z prostředí IDE spojeny s Anaconda jako nečinný nebo Spyder. Můžete spustit jeden z nich tak, že na panelu hledání (**Win** + **S** klíč).

> [!NOTE]
> Tak, aby odkazoval nástroje Pythonu pro Visual Studio na Anaconda Python 2.7, je potřeba vytvořit vlastní prostředí pro každou verzi. Chcete-li nastavit tyto cesty prostředí v aplikaci Visual Studio 2017 Community Edition, přejděte na **nástroje** -> **nástroje Python Tools** -> **prostředí Pythonu**a potom klikněte na tlačítko **+ vlastní**. 
> 
> 

Anaconda Python 3.6 je nainstalované v C:\Anaconda a nainstaluje se v rámci c:\Anaconda\envs\python2 Anaconda Python 2.7. Zobrazit [dokumentace k PTVS](/visualstudio/python/installing-python-interpreters.md) podrobné pokyny. 

### <a name="jupyter-notebook"></a>Poznámkový blok Jupyter
Anaconda distribuce také součástí poznámkového bloku Jupyter, prostředí pro sdílení kódu a analýzy. Server poznámkového bloku Jupyter se předem nakonfigurovaným rozhraním Python 2.7 Python 3.x, jádra PySpark, Julia a R. Existuje ikona na ploše s názvem "Poznámkový blok Jupyter" spustit Jupyter server a spustit prohlížeč pro přístup k serveru poznámkového bloku. 

Budeme mít zabalit několik ukázkové poznámkové bloky v Pythonu a R. Poznámkové bloky Jupyter ukazují, jak pracovat s Microsoft ML Server, služby SQL Server ML (analýza v databázi), Python, Microsoft Cognitive ToolKit, Tensorflow nebo jiné technologie Azure po přistupujete Jupyter. Zobrazí se odkaz na ukázky na domovské stránce Poznámkový blok po ověření do poznámkového bloku Jupyter pomocí hesla, které jste vytvořili v dřívějším kroku. 

### <a name="visual-studio-2017-community-edition"></a>Visual Studio 2017 Community edition
Visual Studio Community edition nainstalovala do virtuálního počítače. Je bezplatná verze Oblíbené integrované vývojové prostředí společnosti Microsoft, který používáte pro účely vyhodnocení a pro malé týmy. Si můžete prohlédnout licenční podmínky [tady](https://www.visualstudio.com/support/legal/mt171547).  Otevřít Visual Studio na něj poklikejte desktopových ikonu nebo **Start** nabídky. Můžete také vyhledat programy s **Win** + **S** a zadáním "Visual Studio". Jakmile tam můžete vytvořit projekty v jazycích, jako jsou typu jazyka C#, Python, R, node.js. Moduly plug-in jsou také nainstalované, které usnadňují vhodné pracovat se službami Azure, jako je Azure Data Catalog, Azure HDInsight (Hadoop, Spark) nebo Azure Data Lake. Nyní k dispozici je také volá modul plug-in ```Visual Studio Tools for AI``` , který hladce integruje do Azure Machine Learning a umožňuje rychle sestavovat aplikace AI. 

> [!NOTE]
> Může zobrazit zpráva s informacemi o tom, že vypršela platnost vašeho zkušebního období. Zadejte svoje přihlašovací údaje účtu Microsoft nebo vytvořit nový bezplatný účet, abyste získali přístup k Visual Studio Community Edition. 
> 
> 

### <a name="sql-server-2017-developer-edition"></a>SQL Server 2017 Developer edition
Verzi služby ML ke spouštění analýz v databázi systému SQL Server 2017 developer je k dispozici na virtuálním počítači v R nebo Python. Služby ML poskytují model platforma pro vývoj a nasazování inteligentních aplikací. Vám pomůže bohaté a výkonné těchto jazyků a mnoho balíčků od členů komunity a vytvářet modely a generovat předpovědi pro data systému SQL Server. Analytics blízko data můžete zachovat, protože služby ML (v databázi) integruje jazyka R i Python v rámci SQL serveru. Tím se eliminují náklady a bezpečnostní rizika spojená s přesuny dat.

> [!NOTE]
> Edice systému SQL Server developer jde použít jenom pro vývojové a testovací účely. Budete potřebovat licenci k použití v produkčním prostředí. 
> 
> 

SQL server dostanete spuštěním **SQL Server Management Studio**. Název vašeho virtuálního počítače je vyplněna jako název serveru. Použijte ověřování Windows, pokud je přihlášení jako správce na Windows. Až budete v SQL Server Management Studio vytvořit další uživatele, vytvářet databáze, umožňuje importovat data a spouštět dotazy SQL. 

Chcete-li v databázi analytics s využitím služby ML SQL, spusťte následující příkaz jako jednorázovou akcí v aplikaci SQL Server management studio po přihlášení jako správce serveru. 

        CREATE LOGIN [%COMPUTERNAME%\SQLRUserGroup] FROM WINDOWS 

        (Please replace the %COMPUTERNAME% with your VM name)


### <a name="azure"></a>Azure
Několik nástrojů pro Azure jsou nainstalované na virtuálním počítači:

* Je zástupce na ploše pro přístup k dokumentaci k sadě Azure SDK. 
* **AzCopy**: použití pro přesun dat do a z vašeho účtu úložiště Microsoft Azure. Pokud chcete zobrazit využití, zadejte **Azcopy** příkazového řádku zobrazíte využití. 
* **Microsoft Azure Storage Explorer**: umožňuje procházet objekty, které jste uložili v rámci účtu Azure Storage a přenosy dat do a z úložiště Azure. Můžete zadat **Průzkumníka služby Storage** v Hledat nebo najít v nabídce Windows Start na tento nástroj používat. 
* **Adlcopy**: použití pro přesun dat do Azure Data Lake. Pokud chcete zobrazit využití, zadejte **adlcopy** v příkazovém řádku. 
* **dtui**: používá k přesunu dat do a ze služby Azure Cosmos DB, databázi NoSQL v cloudu. Typ **dtui** na příkazovém řádku. 
* **Azure Data Factory Integration Runtime**: umožňuje přesun dat mezi místním zdrojům dat a cloudem. Používá se v rámci nástroje, jako je Azure Data Factory. 
* **Prostředí Azure Powershell**: nástroj používaný ke správě vašich prostředků Azure v prostředí Powershell na vašem virtuálním počítači je také nainstalována skriptovací jazyk. 

### <a name="power-bi"></a>Power BI
Můžete vytvářet řídicí panely a skvělé vizualizace **Power BI Desktopu** byla nainstalována. Pomocí tohoto nástroje k získání dat z různých zdrojů, vytvářet řídicí panely a sestavy a publikovat je do cloudu. Informace najdete v tématu [Power BI](http://powerbi.microsoft.com) lokality. Power BI desktopu najdete v nabídce Start. 

> [!NOTE]
> Budete potřebovat účet Office 365 pro přístup k Power BI. 
> 
> 

### <a name="azure-machine-learning-workbench"></a>Aplikace Azure Machine Learning Workbench

Azure Machine Learning Workbench je desktopová aplikace a rozhraní příkazového řádku. Aplikace Workbench má integrovanou přípravu dat, která se učí jednotlivé kroky přípravy dat, jak je provádíte. Také poskytuje řízení projektů, historie spouštění a integrace poznámkových bloků a zvyšte tak svou produktivitu. Můžete využít nejlepší open source architektur, včetně TensorFlow, Cognitive Toolkit, Spark ML a scikit-Naučte se vyvíjet modely. Na datové VĚDY poskytujeme ikony na ploše instalace aplikace Azure Machine Learning workbench do adresáře % LOCALAPPDATA % jednotlivé uživatele. Každý uživatel, který je potřeba použít v aplikaci Workbench je potřeba provést nějaké jednorázovou akcí poklepání ```AzureML Workbench Setup``` ikon na ploše nainstalovat instanci aplikace Workbench. Azure Machine Learning také vytvoří a použije prostředí Pythonu na uživatele, které je extrahován do % LOCALAPPDATA%\amlworkbench\python.

## <a name="additional-microsoft-development-tools"></a>Další nástroje pro vývoj Microsoft
[ **Instalačního programu webové platformy Microsoft** ](https://www.microsoft.com/web/downloads/platform.aspx) lze vyhledat a stáhnout jiných vývojových nástrojů společnosti Microsoft. Je také zástupce nástroje k dispozici na ploše virtuálního počítače Microsoft datové vědy.  

## <a name="important-directories-on-the-vm"></a>Důležité adresáře na virtuálním počítači
| Položka | Adresář |
| --- | --- |
| Konfigurace serveru poznámkového bloku Jupyter |C:\ProgramData\jupyter |
| Domovský adresář pro ukázky Poznámkový blok Jupyter |c:\dsvm\notebooks a c:\users\<uživatelské jméno > \notebooks|
| Další ukázky |c:\dsvm\samples |
| Anaconda (výchozí: Python 3.6) |c:\Anaconda |
| Anaconda Python 2.7 prostředí |c:\Anaconda\envs\python2 |
| Microsoft ML Server samostatné Pythonu  | C:\Program Files\Microsoft\ML Server\PYTHON_SERVER |
| Výchozí R instance (samostatný Server ML) |C:\Program Files\Microsoft\ML Server\R_SERVER |
| Adresář instance služby ML SQL v databázi |C:\Program Files\Microsoft SQL Server\MSSQL14.MSSQLSERVER |
| Azure Machine Learning Workbench (na jednoho uživatele) | %localappdata%\amlworkbench | 
| Různé nástroje |c:\dsvm\tools |

> [!NOTE]
> V systému Windows Server 2012 verzi edice DSVM a Windows serveru 2016 před březnem 2018 je výchozí prostředí Anaconda Python 2.7. Sekundární prostředí je umístěn na c:\Anaconda\envs\py35 Python 3.5. 
> 
> 

## <a name="next-steps"></a>Další kroky
Tady je několik dalších kroků, abyste mohli pokračovat, učení a zkoumání. 

* Prozkoumejte různých nástrojů pro datové vědy pro datovou vědu virtuálního počítače tak, že kliknete na nabídku start a rezervace v seznamu v nabídce Nástroje.
* Další informace o službách Azure Machine Learning a Workbench návštěvou produktu [stránku rychlý start a kurzy](../service/index.yml). 
* Přejděte do **C:\Program Files\Microsoft\ML Server\R_SERVER\library\RevoScaleR\demoScripts** ukázek použití RevoScaleR knihovny v jazyce R, který podporuje analýzy dat v podnikovém měřítku.  
* Přečtěte si článek: [10 kroky, které můžete provést na virtuální počítač pro datovou vědu](http://aka.ms/dsvmtenthings)
* Zjistěte, jak vytvářet komplexní analytická řešení systematicky pomocí [vědecké zpracování týmových dat](../team-data-science-process/index.yml).
* Přejděte [galerii Azure AI](http://gallery.cortanaintelligence.com) služeb machine learning a datové analýzy ukázek, které využívají Azure Machine learning a související data v Azure. Jsme také zajistili ikona na **Start** nabídky a na ploše virtuálního počítače pro tuto galerii.

