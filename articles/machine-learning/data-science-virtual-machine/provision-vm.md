---
title: Zřízení virtuálního počítače Windows datové vědy v Azure | Microsoft Docs
description: Konfigurace a vytvoření virtuálního počítače vědecké účely dat v Azure pro analýzy a strojového učení.
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
ms.openlocfilehash: 445b18dee9efa9561ba1274ef59a9a426332d745
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/19/2018
ms.locfileid: "31594037"
---
# <a name="provision-the-windows-data-science-virtual-machine-on-azure"></a>Zřízení virtuálního počítače Windows datové vědy v Azure
Virtuální počítač Microsoft Data vědecké účely je předem nainstalovaná a nakonfigurovaná s několik oblíbených nástrojů, které se běžně používají k analýze dat a strojové učení image virtuálního počítače (VM) systému Windows Azure. Nástroje sady jsou:

* [Azure Machine Learning](../service/index.yml) Workbench
* [Microsoft Machine Learning serveru](https://docs.microsoft.com/machine-learning-server/index) Developer Edition
* Anaconda distribuci jazyka Python
* Poznámkový blok Jupyter (s R, Python, jádra PySpark)
* Visual Studio Community Edition
* Power BI Desktop
* SQL Server 2017 Developer Edition
* Spark samostatné instance pro místní vývoj a testování
* [JuliaPro](https://juliacomputing.com/products/juliapro.html)
* Machine learning a analýza dat nástrojů
  * Hloubkové Learning architektury: bohatou sadu rozhraní AI včetně [kognitivní nástrojů Microsoft](https://www.microsoft.com/en-us/cognitive-toolkit/), [TensorFlow](https://www.tensorflow.org/), [zřetězeného souboru](https://chainer.org/), mxNet, Keras jsou zahrnuty do virtuálního počítače.
  * [K dispozici Vowpal](https://github.com/JohnLangford/vowpal_wabbit): rychlé strojového učení systému, který podporuje, jako jsou online a hash, allreduce, snížení, learning2search, aktivní a interaktivní učení.
  * [XGBoost](https://xgboost.readthedocs.org/en/latest/): nástroj poskytuje rychlé a přesné boosted stromu implementace.
  * [Rattle](http://rattle.togaware.com/) (R Analytical nástroj pro další snadno): nástroj, který umožňuje Začínáme s data analýzy a strojového učení v R snadné. Obsahuje zkoumání dat založených na grafickém uživatelském rozhraní a modelování pomocí automatického generování kódu R.
  * [Weka](http://www.cs.waikato.ac.nz/ml/weka/) : dolování visual dat a strojové učení softwaru v jazyce Java.
  * [Rozbalení Apache](https://drill.apache.org/): bez schémat modul dotazů SQL pro Hadoop, NoSQL a úložiště v cloudu.  Podporuje rozhraní ODBC a JDBC umožňující dotazování NoSQL a soubory ze standardních nástrojů BI Power BI, Excel, Tableau.
* Knihovny v R a Python pro použití v Azure Machine Learning a jinými službami Azure
* Git, včetně Git Bash pro práci s úložišť zdrojového kódu, včetně GitHub, Visual Studio Team Services a poskytuje několik oblíbených Linux nástroje příkazového řádku (včetně awk menšit, perl, grep, najít, wget, curl, atd.) dostupný git bash a příkaz výzvu. 

Provádění vědecké zpracování dat zahrnuje iterace v pořadí úloh:

1. Hledání, načítání a předem zpracování dat.
2. Vytváření a testování modely
3. Nasazování modelů pro používání v inteligentní aplikace

Datových vědců použít celou řadu nástrojů pro dokončení těchto úloh. Může trvat poměrně dlouho najít odpovídající verze softwaru a pak stáhnout a nainstalovat je. Virtuální počítač Microsoft Data vědecké účely můžete usnadňují tato zatížení poskytováním připravených k použití bitovou kopii, která se dá zřídit v Azure s všechny několik oblíbených nástrojů předem nainstalovaná a nakonfigurovaná. 

Virtuální počítač Microsoft Data vědecké účely jump-starts projektu analytics. Umožňuje pracovat na úlohy v různých jazycích včetně R, Python, SQL a C#. Visual Studio poskytuje rozhraní IDE pro vývoj a testování vaší kód, který se snadno používá. Azure SDK zahrnuté ve virtuálním počítači umožňuje sestavení vaší aplikace s použitím různých služeb na Cloudová platforma společnosti Microsoft. 

Neexistují žádné poplatky softwaru pro tuto bitovou kopii dat vědecké účely virtuálních počítačů. Platíte jenom pro poplatky za Azure použití které závisí na velikosti virtuálního počítače, které zřízení. Další informace o poplatky za výpočetní naleznete v části Podrobnosti cena na [datové vědy virtuálního počítače](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-ads.windows-data-science-vm?tab=PlansAndPrice) stránky. 

## <a name="other-versions-of-the-data-science-virtual-machine"></a>Jiné verze datové vědy virtuálního počítače
[Ubuntu](dsvm-ubuntu-intro.md) obrázek k dispozici také, se podobně jako celou řadu nástrojů a několik dalších přímým učení architektury. A [CentOS](linux-dsvm-intro.md) obrázek k dispozici. Mají k dispozici [edice systému Windows Server 2012](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-ads.standard-data-science-vm) datové vědy virtuálního počítače, když několik nástrojů jsou k dispozici pouze v edici systému Windows Server 2016.  Jinak tento článek platí také pro edici Windows Server 2012.

## <a name="prerequisites"></a>Požadavky
Než bude možné vytvořit virtuální počítač Microsoft Data vědecké účely, musíte mít následující:

* **Předplatné Azure**: ho získat, najdete v části [získání bezplatné zkušební verze Azure](http://azure.com/free).


## <a name="create-your-microsoft-data-science-virtual-machine"></a>Vytvořit virtuální počítač Microsoft Data vědecké účely
K vytvoření instance systému Microsoft Data vědecké účely virtuální počítač, postupujte takto:

1. Přejděte k virtuálnímu počítači výpis na [portál Azure](https://portal.azure.com/#create/microsoft-ads.windows-data-science-vmwindows2016).
2. Vyberte **vytvořit** tlačítko dole mají být provedeny do průvodce.![ Konfigurace data-vědecké účely vm](./media/provision-vm/configure-data-science-virtual-machine.png)
3. Průvodce slouží k vytvoření virtuálního počítače Microsoft Data vědecké účely vyžaduje **vstupy** pro každou z **čtyři kroky** uvedené na pravé straně tohoto obrázku. Zde jsou vstupy potřebná ke konfiguraci jednotlivých kroků:
   
   1. **Základy**
      
      1. **Název**: název vašeho serveru vědecké účely data vytváříte.
      2. **Typ disku virtuálního počítače**: výběr mezi SSD a HDD. Pro NC_v1 GPU instance (NVidia tesla – měrná K80 na základě), zvolte **HDD** jako typ disku. 
      3. **Uživatelské jméno**: id přihlášení účtu správce.
      4. **Heslo**: heslo účtu správce.
      5. **Předplatné**: Pokud máte více než jedno předplatné, vyberte ten, na kterém se tento počítač je vytvořen a účtují.
      6. **Skupina prostředků**: můžete vytvořit novou nebo použít stávající skupinu.
      7. **Umístění**: Vyberte datové centrum, která je nejvhodnější. Obvykle je datové centrum, které má většina vašich dat, nebo je nejblíže vašemu fyzické umístění pro nejrychlejší přístup k síti.
   2. **Velikost**: Vyberte jeden z typů serveru, které splňuje požadavek na funkční a náklady na omezení. Další možnosti velikostí virtuálních počítačů můžete získat tak, že vyberete "Zobrazit vše".
   3. **Nastavení**:
      
      1. **Používat spravované disky**: Zvolte spravovat, pokud chcete, aby Azure ke správě disky pro virtuální počítač.  V opačném případě je třeba zadat účet nový nebo existující úložiště. 
      2. **Další parametry**: obvykle stačí použít výchozí hodnoty. Pokud chcete, zvažte možnost použití hodnoty jiné než výchozí, přejděte myší na informační odkaz Nápověda v konkrétních polí.
    a. **Souhrn**: Zkontrolujte, zda jsou všechny informace, které jste zadali správné a klikněte na tlačítko **vytvořit**. **Poznámka:**: virtuálního počítače nemá jakýchkoli dalších poplatků nad rámec výpočetní pro velikost serveru, který jste zvolili v **velikost** krok. 

> [!NOTE]
> Zajišťování zabere asi 10-20 minut. Stav zřizování se zobrazí na portálu Azure.
> 
> 

## <a name="how-to-access-the-microsoft-data-science-virtual-machine"></a>Jak získat přístup k virtuální počítač Microsoft Data vědecké účely
Po vytvoření virtuálního počítače můžete do ní pomocí přihlašovacích údajů účtu správce, které jste nakonfigurovali v předchozím vzdálené plochy **Základy** části. 

Jakmile virtuálního počítače se vytvoří a zřizovat, jste připraveni začít používat nástroje, které jsou nainstalované a nakonfigurované na něm. Existují dlaždice úvodní nabídky a ikony na ploše pro řadu nástrojů. 


## <a name="tools-installed-on-the-microsoft-data-science-virtual-machine"></a>Nástroje nainstalované na Microsoft Data vědecké účely virtuálního počítače



### <a name="microsoft-ml-server-developer-edition"></a>Microsoft ML Server Developer Edition
Pokud chcete používat knihovny Microsoft enterprise pro škálovatelnou R nebo Python pro analytické údaje, virtuální počítač má Microsoft ML Server Developer (dříve označované jako Microsoft R Server) nainstalované verze. Microsoft ML Server je platforma pro analýzu široce nasadit podnikové třídy k dispozici pro R a Python a škálovatelnou, vyvineme z hlediska podporované a zabezpečený. Podpora různých statistiky velkých objemů dat, prediktivního modelování a strojového učení možnosti, ML Server podporuje celou řadu analytics – zkoumání, analýzu, vizualizace a modelování. Pomocí a rozšíření s otevřeným zdrojem R a Python, Microsoft ML Server je plně kompatibilní s R nebo Python skripty, funkce a CRAN / nástrojem pip / škálování Conda balíčky k analýze dat v podniku. Přidáním bloku a paralelní zpracování dat řeší také omezení v paměti R otevřete zdroje. To umožňuje spustit analýzy dat mnohem větší, než co nejlépe odpovídá v hlavní paměti.  Visual Studio Community Edition zahrnuté do virtuálního počítače obsahuje R nástrojů pro Visual Studio a Python tools for rozšíření sady Visual Studio, který obsahuje úplnou IDE pro práci s R nebo Python. Poskytujeme také jiné integrovaného vývojového prostředí, stejně jako [Rstudia](http://www.rstudio.com) a [PyCharm Community edition](https://www.jetbrains.com/pycharm/) ve virtuálním počítači. 

### <a name="python"></a>Python
Pro vývoj pomocí Python byl nainstalován distribuce Anaconda Python 2.7 a 3.6. Toto rozdělení obsahuje základní Python společně s přibližně 300 nejoblíbenější balíčků analytics matematické, technici a data. Můžete použít nástroje Python Tools pro Visual Studio (PTVS) nainstalované v rámci edice Visual Studio 2017 Community nebo jeden z integrovaného vývojového prostředí dodávat s Anaconda jako nečinnosti nebo Spyder. Můžete spustit jeden z těchto oblastí vyhledávání v panelu vyhledávání (**Win** + **S** klíč).

> [!NOTE]
> Chcete-li bod nástroje Python Tools pro sadu Visual Studio v Anaconda Python 2.7, vytvořte vlastní prostředí pro každou verzi. Chcete-li nastavit tyto cesty prostředí Visual Studio 2017 Community Edition, přejděte na **nástroje** -> **Python Tools** -> **prostředí Python**a pak klikněte na **+ vlastní**. 
> 
> 

Anaconda Python 3.6 je nainstalován v části C:\Anaconda a Anaconda Python 2.7 je nainstalován v části c:\Anaconda\envs\python2. V tématu [dokumentaci k těmto nástrojům](/visualstudio/python/python-environments.md#selecting-and-installing-python-interpreters) podrobné pokyny. 

### <a name="jupyter-notebook"></a>Poznámkový blok Jupyter
Anaconda distribuční také obsahuje poznámkového bloku Jupyter, prostředí sdílení kódu a analýzy. Server poznámkového bloku Jupyter byl předem nakonfigurovaným rozhraním Python 2.7, Python 3.x, jádra PySpark, Dita a R. Není ikony na ploše s názvem "Poznámkový blok Jupyter" start pro server Jupyter a spusťte prohlížeč pro přístup k serveru poznámkového bloku. 

Budeme mít zabalené několik poznámkových bloků ukázka v Pythonu a v jazyce R. Jupyter notebooks ukazují, jak pracovat s Microsoft ML Server, ML služby SQL Server (v databázi analytics), Python, Microsoft kognitivní ToolKit, Tensorflow a další technologie Azure po přístup Jupyter. Zobrazí se odkaz na ukázky na domovské stránce poznámkového bloku po ověření do poznámkového bloku Jupyter s heslem, kterou jste vytvořili v předchozím kroku. 

### <a name="visual-studio-2017-community-edition"></a>Visual Studio 2017 Community edition
Visual Studio Community edition nainstalovaný na Virtuálním počítači. Je bezplatnou verzi oblíbených IDE od společnosti Microsoft, který můžete použít pro účely hodnocení a pro malé týmy. Můžete zkontrolovat si licenční podmínky [zde](https://www.visualstudio.com/support/legal/mt171547).  Dvojitým kliknutím na ikony ploše otevřete Visual Studio nebo **spustit** nabídky. Můžete také vyhledat programy s **Win** + **S** a zadání "Visual Studio". Jakmile se tam můžete vytvořit projektů v jazyce jazycích C#, Python, R, node.js. Moduly plug-in jsou nainstalovány také zajišťujících pohodlné pro práci se službami Azure, jako je Azure Data Catalog, Azure HDInsight (Hadoop, Spark) a Azure Data Lake. Nyní k dispozici je také o modul plug-in názvem ```Visual Studio Tools for AI``` která bezproblémově integruje do Azure Machine Learning a vám pomůže rychle sestavení AI aplikace. 

> [!NOTE]
> Může se zobrazit zpráva, že se vaše zkušební období vypršelo. Zadejte přihlašovací údaje účtu Microsoft nebo vytvořit nový bezplatný účet získat přístup k Visual Studio Community Edition. 
> 
> 

### <a name="sql-server-2017-developer-edition"></a>SQL Server 2017 Developer edition
Verzi SQL serveru 2017 službou ML ke spuštění v databázi analýzy pro vývojáře je k dispozici na virtuální počítač v R nebo Python. ML služby poskytují platformu pro vývoj a nasazení inteligentní aplikace. Můžete bohatý a výkonné tyto jazyky a velký počet balíčků od komunity k vytváření modelů a generování předpovědi pro data systému SQL Server. Analýza blízko data můžete zachovat, protože ML služby (v databázi) integruje jazyk R i Python v rámci serveru SQL. Tím se eliminuje náklady a bezpečnostních rizicích spojených s přesun dat.

> [!NOTE]
> Vývojáře edici systému SQL Server můžete použít pouze pro testovací účely vývoje a. Potřebujete licenci na spuštění v produkčním prostředí. 
> 
> 

SQL server můžete přistupovat spuštěním **SQL Server Management Studio**. Název virtuálního počítače se importují jako název serveru. Pomocí ověřování systému Windows při přihlášení jako správce v systému Windows. Jakmile jste na SQL Server Management Studio můžete vytvořit další uživatelé, vytváření databází, umožňuje importovat data a spouštět dotazy SQL. 

Pokud chcete povolit analytics v databázi pomocí služby ML SQL, spusťte následující příkaz jako na čas akce v aplikaci SQL Server management studio po přihlášení jako správce serveru. 

        CREATE LOGIN [%COMPUTERNAME%\SQLRUserGroup] FROM WINDOWS 

        (Please replace the %COMPUTERNAME% with your VM name)


### <a name="azure"></a>Azure
Několik nástrojů Azure jsou nainstalovány ve virtuálním počítači:

* Je zástupce na ploše pro přístup k dokumentaci Azure SDK. 
* **AzCopy**: používá k přesunu dat do aplikace a z účtu úložiště Microsoft Azure. Chcete-li zobrazit využití, zadejte **Azcopy** na příkazovém řádku zobrazíte využití. 
* **Microsoft Azure Storage Explorer**: umožňuje procházet objekty, které jsou uloženy v rámci účtu úložiště Azure a přenos dat do a z úložiště Azure. Můžete zadat **Storage Explorer** v Hledat nebo najít v nabídce Start systému Windows pro přístup k tento nástroj. 
* **Adlcopy**: používá k přesunu dat do Azure Data Lake. Chcete-li zobrazit využití, zadejte **adlcopy** v příkazovém řádku. 
* **dtui**: používá k přesunu dat do a z Azure Cosmos databáze, databáze NoSQL v cloudu. Typ **dtui** na příkazovém řádku. 
* **Azure Data Factory integrace Runtime**: umožňuje přesun dat mezi místní zdroje dat a cloudem. Používá se v rámci nástroje, například Azure Data Factory. 
* **Microsoft Azure Powershell**: nástroj používaný ke správě prostředků Azure v prostředí Powershell skriptovací jazyk. je také nainstalován na váš počítač. 

### <a name="power-bi"></a>Power BI
Pomoc při vytváření řídicích panelů a vizualizací skvělé, **Power BI Desktop** byl nainstalován. Tento nástroj použijte k získání dat z různých zdrojů, chcete-li vytvářet řídicí panely a sestavy a publikovat je do cloudu. Informace najdete v tématu [Power BI](http://powerbi.microsoft.com) lokality. Power BI desktop můžete najít v nabídce Start. 

> [!NOTE]
> Je nutné účet Office 365 pro přístup k Power BI. 
> 
> 

### <a name="azure-machine-learning-workbench"></a>Aplikace Azure Machine Learning Workbench

Azure Machine Learning Workbench je desktopová aplikace a rozhraní příkazového řádku. Nástroje Workbench má příprava předdefinované dat, který zjišťuje přípravné kroky jako jejich provedení. Umožňuje taky správu projektu, spusťte historie a integrace poznámkového bloku účinnějšími produktivitu. Můžete využít osvědčené rozhraní open source, včetně TensorFlow, kognitivní Toolkit, Spark ML a scikit – Naučte se vyvíjet modely. Na DSVM poskytujeme ikony na ploše se nainstalovat nástroje workbench Azure Machine Learning do složky % LOCALAPPDATA % jednotlivé uživatele. Každý uživatel, který potřebuje používat nástroje Workbench je potřeba udělat nějaké doby akce dvojitým kliknutím ```AzureML Workbench Setup``` ploše ikona nainstalovat instanci nástroje Workbench. Azure Machine Learning také vytvoří a použije uživatelské prostředí Python, které je extrahován v % LOCALAPPDATA%\amlworkbench\python.

## <a name="additional-microsoft-development-tools"></a>Další vývojové nástroje společnosti Microsoft
[ **Instalačního programu webové platformy Microsoft** ](https://www.microsoft.com/web/downloads/platform.aspx) lze použít ke zjištění a stáhnout jiné vývojové nástroje společnosti Microsoft. Je také zástupce nástroj, který poskytuje na ploše Microsoft Data vědecké účely virtuálního počítače.  

## <a name="important-directories-on-the-vm"></a>Důležité adresáře na virtuálním počítači
| Položka | Adresář |
| --- | --- |
| Konfigurace serveru poznámkového bloku Jupyter |C:\ProgramData\jupyter |
| Domovský adresář ukázky poznámkového bloku Jupyter |c:\dsvm\notebooks a c:\users\<uživatelské jméno > \notebooks|
| Další ukázky |c:\dsvm\samples |
| Anaconda (výchozí: Python 3.6) |c:\Anaconda |
| Prostředí Python 2.7 anaconda |c:\Anaconda\envs\python2 |
| Microsoft ML Server samostatné Python  | C:\Program Files\Microsoft\ML Server\PYTHON_SERVER |
| Výchozí R instance (ML Server samostatně) |C:\Program Files\Microsoft\ML Server\R_SERVER |
| Adresář instance v databázi SQL ML Services |C:\Program Files\Microsoft SQL Server\MSSQL14.MSSQLSERVER |
| Azure Machine Learning Workbench (podle uživatele) | %localappdata%\amlworkbench | 
| Různé nástroje |c:\dsvm\tools |

> [!NOTE]
> V edici Windows Server 2012 DSVM a Windows Server 2016 edice před březnem 2018 je výchozím Anaconda prostředí Python 2.7. Sekundární prostředí je umístěný v c:\Anaconda\envs\py35 3.5 Python. 
> 
> 

## <a name="next-steps"></a>Další kroky
Tady jsou některé další kroky, chcete-li pokračovat, učení a zkoumání. 

* Prozkoumejte různé vědě nástrojů data na vědecké zpracování dat virtuálního počítače tak, že kliknete nabídky start v seznamu v nabídce nástroje se odhlašuje.
* Další informace o služby Azure Machine Learning a Workbench návštěvou produktu [stránku rychlý start a kurzy](../service/index.yml). 
* Přejděte na **C:\Program Files\Microsoft\ML Server\R_SERVER\library\RevoScaleR\demoScripts** ukázek pomocí knihovny RevoScaleR v R, který podporuje analýzy dat škálované enterprise.  
* Přečtěte si článek: [10 způsobů, jak na vědecké zpracování dat virtuálního počítače](http://aka.ms/dsvmtenthings)
* Naučte se vytvářet koncová analytická řešení systematičtěji pomocí [proces vědecké účely dat Team](../team-data-science-process/index.yml).
* Přejděte [Azure AI Galerie](http://gallery.cortanaintelligence.com) pro machine learning a data analýzy vzorků, které pomocí Azure Machine learning a související data services v Azure. Taky uvádíme ikonu na **spustit** nabídky a na ploše virtuálního počítače do této galerie.

