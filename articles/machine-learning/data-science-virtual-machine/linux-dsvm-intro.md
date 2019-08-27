---
title: Vytvořit virtuální počítač CentOS Linux datové vědy
titleSuffix: Azure
description: Vytvořte a nakonfigurujte Data Science Virtual Machine pro Linux v Azure pro analýzy a strojové učení.
services: machine-learning
documentationcenter: ''
author: vijetajo
manager: cgronlun
ms.custom: seodec18
ms.assetid: 3bab0ab9-3ea5-41a6-a62a-8c44fdbae43b
ms.service: machine-learning
ms.subservice: data-science-vm
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 03/16/2018
ms.author: vijetaj
ms.openlocfilehash: c0464253c55aa5e51e8e86686405ea6b107c8382
ms.sourcegitcommit: bba811bd615077dc0610c7435e4513b184fbed19
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/27/2019
ms.locfileid: "70047726"
---
# <a name="provision-a-linux-centos-data-science-virtual-machine-in-azure"></a>Zřízení Data Science Virtual Machine CentOS pro Linux v Azure

Linux Data Science Virtual Machine (DSVM) je virtuální počítač Azure založený na CentOS. DSVM pro Linux obsahuje kolekci předinstalovaných nástrojů, které můžete použít pro analýzu dat a strojové učení. 

Klíčové softwarové komponenty, které jsou součástí DSVM pro Linux, jsou:

* CentOS pro distribuci operačního systému Linux.
* Microsoft Machine Learning Server.
* Distribuce Pythonu Anaconda (verze 3,5 a 2,7), včetně oblíbených knihoven analýz dat.
* JuliaPro, uspořádaná distribuce jazyka Helena a oblíbených vědeckých a datových knihoven analýz.
* Samostatná instance Sparku a Hadoop s jedním uzlem (HDFS, PŘÍZe).
* JupyterHub, Jupyter Server s více uživateli, který podporuje jádro R, Python, PySpark a Helena.
* Průzkumník služby Azure Storage.
* Azure CLI, rozhraní příkazového řádku Azure pro správu prostředků Azure.
* Databáze PostgresSQL
* Nástroje pro strojové učení:
  * [Microsoft Cognitive Toolkit](https://github.com/Microsoft/CNTK) (CNTK), software sada nástrojů pro hloubkové učení od Microsoft Research.
  * [Pro dostupné](https://github.com/JohnLangford/vowpal_wabbit), rychlý systém strojového učení, který podporuje techniky jako online, hashing, allreduce, snižování, learning2search, aktivní a interaktivní učení.
  * [XGBoost](https://xgboost.readthedocs.org/en/latest/), nástroj, který poskytuje rychlou a přesnou zesílenou implementaci stromu.
  * [Rattle](https://togaware.com/rattle/)je nástroj, který usnadňuje zahájení práce s analýzou dat a strojovým učením v R. Rattle nabízí zkoumání a modelování dat na základě grafického uživatelského rozhraní pomocí automatického generování kódu R.
* Sada Azure SDK v jazycích Java, Python, Node. js, Ruby a PHP.
* Knihovny v R a Pythonu, které se použijí v Azure Machine Learning a dalších službách Azure.
* Vývojové nástroje a editory (RStudio, PyCharm, IntelliJ, (Emacs), gedit, VI).

Vědecké zpracování dat zahrnuje iterace v pořadí úloh:

1. Vyhledávejte, načítají a předběžně zpracovávají data.
1. Sestavení a otestování modelů.
1. Nasazení modelů pro použití v inteligentních aplikacích.

Odborníci přes data pomocí různých nástrojů k provedení těchto úloh. Vyhledání správných verzí softwaru a stažení, kompilování a instalace softwaru může být časově náročné.

DSVM pro Linux může toto zatížení podstatně zjednodušit. Použijte DSVM pro Linux k přechodu na začátek analytického projektu. Linux DSVM vám pomůže pracovat na úkolech v různých jazycích, včetně jazyků R, Python, SQL, Java a C++. Zatmění nabízí snadno použitelné prostředí IDE pro vývoj a testování kódu. Sada Azure SDK, která je součástí DSVM, vám pomůže sestavovat aplikace pomocí různých služeb systému Linux pro cloudovou platformu Microsoftu. Další jazyky jsou předem nainstalovány, včetně Ruby, Perl, PHP a Node. js.

Pro Image DSVM se neúčtují žádné poplatky za software. Platíte jenom poplatky za využití hardwaru Azure, které jsou vyhodnocené na základě velikosti virtuálního počítače, který zřizujete s imagí DSVM. Další informace o výpočetních poplatcích najdete v tématu [Data Science Virtual Machine pro Linux (CentOS)](https://azure.microsoft.com/marketplace/partners/microsoft-ads/linux-data-science-vm/) v Azure Marketplace.

## <a name="prerequisites"></a>Požadavky

Než budete moct vytvořit Data Science Virtual Machine pro Linux, musíte mít tyto požadavky:

* **Předplatné Azure**: Pokud chcete získat předplatné Azure, přečtěte si téma [Vytvoření bezplatného účtu Azure](https://azure.microsoft.com/free/).
* **Účet úložiště Azure**: Pokud chcete získat účet úložiště Azure, přečtěte si téma [Vytvoření účtu úložiště](../../storage/common/storage-quickstart-create-account.md). Pokud nechcete používat existující účet úložiště Azure, můžete při vytváření DSVM vytvořit účet úložiště.

## <a name="other-versions-of-the-data-science-virtual-machine"></a>Jiné verze z virtuální počítač pro datové vědy

Data Science Virtual Machine je také k dispozici v těchto verzích:

* [Ubuntu](dsvm-ubuntu-intro.md): Image Ubuntu má spoustu stejných nástrojů jako image CentOS, včetně architektur hloubkového učení. 
* [Windows](provision-vm.md)

## <a name="create-a-linux-data-science-virtual-machine"></a>Vytvoření Data Science Virtual Machine pro Linux

Vytvoření instance DSVM pro Linux:

1. Přejít na výpis virtuálního počítače na [Azure Portal](https://portal.azure.com/#create/microsoft-ads.linux-data-science-vmlinuxdsvm).
1. Vyberte **vytvořit** a otevřete tak průvodce.

   ![Průvodce, který konfiguruje Data Science Virtual Machine](./media/linux-dsvm-intro/configure-linux-data-science-virtual-machine.png)
1. Pro každý krok průvodce zadejte nebo vyberte následující informace:
   
   **1** **základní informace**:

      * **Název**: Název serveru datové vědy vytváříte.
      * **Uživatelské jméno**: První ID přihlášení k účtu.
      * **Heslo**: První heslo účtu. (Místo hesla můžete použít veřejný klíč SSH.)
      * **Předplatné:** Pokud máte více než jedno předplatné, vyberte předplatné, ve kterém se bude počítač vytvářet a účtují. Musíte mít oprávnění k vytváření prostředků pro předplatné.
      * **Skupina prostředků**: Můžete vytvořit novou skupinu prostředků nebo použít existující skupinu.
      * **Umístění**: Vyberte datové centrum, které chcete použít pro DSVM. Ve většině případů vyberte datové centrum, které obsahuje většinu vašich dat nebo které je nejblíže vašemu fyzickému umístění (pro nejrychlejší přístup k síti).
   
   **2** **Velikost**: Vyberte typ serveru, který splňuje vaše funkční požadavky a omezení nákladů. Vyberte **zobrazení všech** zobrazíte další možnosti velikostí virtuálních počítačů.
   
   
   **3** **Nastavení**:
      * **Typ disku**: Pokud dáváte přednost jednotce SSD (Solid-State Drive), vyberte **Premium**. V opačném případě vyberte možnost **standardní**.
      * **Účet úložiště**: V rámci svého předplatného můžete vytvořit nový účet Azure Storage nebo použít existující účet Azure ve stejném umístění, které jste vybrali v kroku **základy** průvodce.
      * **Další parametry**: Ve většině případů použijte k nakonfigurování dalších parametrů výchozí hodnoty. Pokud chcete zobrazit jiné než výchozí hodnoty, najeďte na informační odkaz pro parametr.
   
   **4** **Souhrn**: Ověřte, zda jsou zadané informace správné.
   
   **5** **Nákup**: Pokud chcete zahájit zřizování, vyberte **koupit**. K dispozici je odkaz na podmínku transakce. Za DSVM se neúčtují žádné další poplatky za výpočet velikosti serveru, který jste vybrali.

Zřizování trvá 10-20 minut. Stav zřizování se zobrazí v Azure Portal.

## <a name="how-to-access-the-linux-data-science-virtual-machine"></a>Jak získat přístup k virtuální počítač Linux datové vědy

Po vytvoření DSVM se k němu můžete přihlásit pomocí SSH. Použijte přihlašovací údaje účtu, které jste vytvořili v části **základy** v průvodci pro rozhraní textového prostředí. Ve Windows si můžete stáhnout klientský nástroj SSH, jako je [](https://www.putty.org)například výstup. Pokud dáváte přednost grafické ploše (systém Windows X), můžete použít předávání X11 na výstupu nebo nainstalovat klienta X2Go.

> [!NOTE]
> Klient X2Go lepších výsledků než X11 předávání v testování. Doporučujeme používat X2Go klienta klasické pracovní plochy grafické rozhraní.

## <a name="install-and-configure-the-x2go-client"></a>Instalace a konfigurace klienta X2Go

DSVM se systémem Linux je zajištěna serverem X2Go a je připravena přijímat připojení klientů. Pokud se chcete připojit k grafické ploše DSVM Linux, proveďte na svém klientovi následující postup:

1. Stažení a instalace klienta X2Go pro vaše klientská platforma z [X2Go](https://wiki.x2go.org/doku.php/doc:installation:x2goclient).
1. Spusťte klienta X2Go. Vyberte **Nová relace**. Otevře se okno konfigurace s několika kartami. Zadejte následující parametry konfigurace:
   * **Karta relace**:
     * **Hostitel**: Zadejte název hostitele nebo IP adresu svého DSVM pro Linux.
     * **Přihlašovací jméno**: Zadejte uživatelské jméno na DSVM pro Linux.
     * **Port SSH**: Ponechte výchozí hodnotu **22**.
     * **Typ relace**: Změňte hodnotu na **desktop Xfce**. V současné době DSVM Linux podporuje pouze desktopovou plochu desktop Xfce.
   * Karta **média** : Pokud je nepotřebujete používat, můžete vypnout zvukovou podporu a tisk klienta.
   * **Sdílené složky**: Pokud chcete, aby se adresáře z klientských počítačů připojily k DSVM Linux, přidejte adresáře klientských počítačů, které chcete sdílet s DSVM.

Po přihlášení k DSVM pomocí klienta SSH nebo grafické plochy desktop Xfce prostřednictvím klienta X2Go budete připraveni začít používat nástroje, které jsou nainstalované a nakonfigurované v DSVM. V desktop Xfce se můžete podívat na zástupce nabídky aplikace a ikony na ploše pro mnohé z těchto nástrojů.

## <a name="tools-installed-on-the-linux-data-science-virtual-machine"></a>Nástroje nainstalované v systému Linux virtuální počítač pro datové vědy

### <a name="machine-learning-server"></a>Machine Learning Server

R je jedním z nejoblíbenějších jazyků pro analýzy dat a strojové učení. Pokud pro analýzu chcete použít R, DSVM má Machine Learning Server s otevřenou a otevřenou knihovnou jádra Microsoft R. Knihovna Math kernel optimalizuje běžné matematické operace v analytických algoritmech. Open jazyka r je plně kompatibilní s CRAN R. Všechny knihovny R publikované v CRAN se dají nainstalovat do jazyka R Open. 

Pomocí Machine Learning Server můžete škálovat a zprovoznění modely R na webové služby. Můžete upravit sady R v jednom z výchozí editorů, jako je RStudio, editoru vi nebo (emacs). Editor (Emacs) je předinstalován na DSVM. Balíček (Emacs) ESS ((Emacs) mluví STATISTICS) usnadňuje práci se soubory R v editoru (Emacs).

Konzolu R otevřete tak, že v prostředí zadáte **R**. Tento příkaz vás provede interaktivním prostředím. K vývoji programu jazyka R obvykle používáte editor jako (Emacs) nebo VI a potom spustíte skripty v R. RStudio nabízí úplné grafické prostředí IDE pro vývoj programu R.

Skript R, který můžete použít k instalaci [prvních 20 balíčků r](https://www.kdnuggets.com/2015/06/top-20-r-packages.html) , je součástí DSVM. Tento skript můžete spustit, když jste v interaktivním rozhraní R. Jak bylo zmíněno dříve, chcete-li otevřít toto rozhraní, zadejte do prostředí **R**.  

### <a name="python"></a>Python

Anaconda Python se instaluje s prostředími Python 3,5 a 2,7. Prostředí 2,7 se nazývá _root_ a prostředí 3,5 se nazývá _py35_. Toto rozdělení obsahuje základní Python spolu s přibližně 300 nejoblíbenějších balíčků analytics matematické, inženýrství a data.

Výchozím nastavením je py35 prostředí. Pokud chcete aktivovat kořenové prostředí (2,7), použijte tento příkaz:

```bash
source activate root
```

Pokud chcete prostředí py35 aktivovat znovu, použijte tento příkaz:

```bash
source activate py35
```

Pokud chcete vyvolat interaktivní relaci Pythonu, zadejte do prostředí **Python**. 

Nainstalujte další knihovny Pythonu pomocí conda nebo PIP. V případě PIP aktivujte správné prostředí, pokud nechcete výchozí nastavení:

```bash
source activate root
pip install <package>
```

Nebo zadat úplnou cestu k nástroje pip:

```bash
/anaconda/bin/pip install <package>
```

V případě conda byste měli vždy zadat název prostředí (py35 nebo root):

```bash
conda install <package> -n py35
```

Pokud používáte grafické rozhraní nebo máte nastavené předávání X11, můžete zadat **PyCharm** a otevřít PYCHARM Python IDE. Můžete použít výchozí textových editorů. Kromě toho můžete použít Spyder, integrované vývojové prostředí (IDE), které je součástí distribuce Anaconda Pythonu. Spyder potřebuje grafické stolní počítač nebo X11 předávání. Grafická plocha má zástupce pro Spyder.

### <a name="jupyter-notebook"></a>Poznámkový blok Jupyter

Anaconda distribuce také přináší Jupyter Notebook, prostředí pro sdílení kódu a analýzy. Přístup k Jupyter Notebook pomocí JupyterHub. Přihlašujete se pomocí uživatelského jména a hesla místního systému Linux.

Jupyter Notebook Server je předem nakonfigurovaný s jádry Python 2, Python 3 a R. Pomocí ikony **Jupyter notebook** plocha otevřete prohlížeč a přejděte na server Jupyter notebook. Pokud k DSVM přistupujete přes SSH nebo klienta X2Go, můžete k serveru Jupyter notebook přistupovat taky na adrese https:\//localhost: 8000/.

> [!NOTE]
> Pokračujte, pokud chcete získat všechna upozornění certifikátu.

Server poznámkového bloku Jupyter můžete přistupovat z libovolného hostitele. Zadejte **https:\//\>DSVM název DNS nebo IP adresa: 8000/.\<**

> [!NOTE]
> Po zřízení DSVM se ve výchozím nastavení v bráně firewall otevře port 8000. 

Společnost Microsoft obsahuje balíčky ukázkových poznámkových bloků, jeden v Pythonu a jeden v R. Odkaz na ukázky na domovské stránce Jupyter Notebook můžete zobrazit po ověření pro Jupyter Notebook pomocí místního uživatelského jména a hesla systému Linux. Chcete-li vytvořit nový Poznámkový blok, vyberte možnost **Nový**a potom vyberte jádro jazyka, které chcete použít. Pokud tlačítko **Nový** nevidíte, vyberte v levém horním rohu ikonu **Jupyter** a přejděte na domovskou stránku serveru poznámkového bloku.

### <a name="spark-standalone"></a>Spark – samostatný 

Instance samostatného režimu Sparku je předem nainstalovanou na platformě Linux DSVM, která vám pomůžou vyvíjet aplikace Spark lokálně ještě předtím, než je otestujete a nasadíte na velké clustery. 

Jádra aplikace Jupyter můžete si projít programy PySpark. Když otevřete Jupyter, vyberte tlačítko **Nový** a měl by se zobrazit seznam dostupných jader. **Spark-Python** je jádrem PySpark, který umožňuje sestavovat aplikace Spark pomocí jazyka Python. K sestavování programu Spark můžete použít také Python IDE, jako je PyCharm nebo Spyder. 

V této samostatné instanci se sada Spark spouští v volajícím klientském programu. Tato funkce usnadňuje a usnadňuje řešení problémů v porovnání s vývojem v clusteru Spark.

Jupyter poskytuje ukázkový Poznámkový blok PySpark. Můžete ji najít v adresáři SparkML v domovském adresáři Jupyter ($HOME/notebooks/SparkML/pySpark). 

Pokud programujete v R pro Spark, můžete použít Machine Learning Server, Spark nebo sparklyr. 

Před spuštěním v kontextu Sparku v Machine Learning Server je třeba provést jednorázový krok nastavení, který povolí místní instanci Hadoop s jedním uzlem a instancí PŘÍZ. Ve výchozím nastavení služby Hadoop jsou nainstalované ale deaktivovány datové VĚDY. Pokud chcete povolit služby Hadoop, spusťte následující příkazy jako první při prvním:

```bash
echo -e 'y\n' | ssh-keygen -t rsa -P '' -f ~hadoop/.ssh/id_rsa
cat ~hadoop/.ssh/id_rsa.pub >> ~hadoop/.ssh/authorized_keys
chmod 0600 ~hadoop/.ssh/authorized_keys
chown hadoop:hadoop ~hadoop/.ssh/id_rsa
chown hadoop:hadoop ~hadoop/.ssh/id_rsa.pub
chown hadoop:hadoop ~hadoop/.ssh/authorized_keys
systemctl start hadoop-namenode hadoop-datanode hadoop-yarn
```

Služby související se systémem Hadoop můžete zastavit, pokud je nepotřebujete spuštěním `systemctl stop hadoop-namenode hadoop-datanode hadoop-yarn`.

Adresář/dsvm/samples/MRS poskytuje ukázku, která ukazuje, jak vyvíjet a testovat Machine Learning Server ve vzdáleném kontextu Spark (samostatná instance Sparku na DSVM).

### <a name="ides-and-editors"></a>Editory a integrovanými vývojovými prostředími

Můžete si vybrat z několika editorů kódu, včetně VI/VIM, (Emacs), gedit, PyCharm, RStudio, zatmění, LaTeX a IntelliJ. 

* gedit, zatmění, IntelliJ, R Studio a PyCharm jsou grafické editory. Pokud je chcete použít, musíte být přihlášeni k grafické ploše. Otevřete je pomocí klávesových zkratek nabídky Desktop a aplikace.

* Procesory vim a (Emacs) jsou textové editory. Na (Emacs) balíček doplňku ESS pracuje s R v editoru (Emacs) snadněji. Další informace najdete na [webu ESS](https://ess.r-project.org/).

* Zatmění je open source rozšiřitelné integrované vývojové prostředí (IDE), které podporuje více jazyků. Integrované vývojové prostředí pro vývojáře v jazyce Java je verze nainstalovaná v DSVM. Můžete nainstalovat moduly plug-in pro několik oblíbených jazyků a prostředí tak rozšíříte. 

  Modul plug-in Azure Toolkit for Eclipse se instaluje s zatmění na DSVM. Azure Toolkit for Eclipse můžete použít k vytváření, vývoji, testování a nasazování aplikací Azure pomocí prostředí pro vývoj v zatmění, které podporuje jazyky jako Java.

  Sada Azure SDK pro jazyk Java je také nainstalovaná s Azure Toolkit for Eclipse v DSVM. Sada Azure SDK pro jazyk Java vám umožní přístup k různým službám Azure z prostředí Java. 
  
  Další informace najdete v tématu [Azure Toolkit for Eclipse](/java/azure/eclipse/azure-toolkit-for-eclipse).

* LaTeX se instaluje prostřednictvím balíčku texlive společně s balíčkem doplňků (Emacs) s názvem [AUCTeX](https://www.gnu.org/software/auctex/manual/auctex/auctex.html). Tento balíček zjednodušuje vytváření dokumentů LaTeX v (Emacs). 

### <a name="databases"></a>Databáze

DSVM pro Linux umožňuje přístup k několika nástrojům pro databáze a příkazový řádek.

#### <a name="postgressql"></a>PostgresSQL

Open source databáze PostgresSQL je k dispozici na DSVM, se službami spuštěnými a initdb dokončenými. Musíte vytvořit databáze a uživatele. Další informace najdete v [dokumentaci k PostgresSQL](https://www.postgresql.org/docs/).  

#### <a name="squirrel-sql"></a>SQuirreL SQL

SQuirreL SQL je grafický klient SQL, který se může připojit k různým databázím (včetně SQL Server, PostgresSQL a MySQL) a spouštět dotazy SQL. SQuirreL SQL můžete spustit z grafické relace plochy (například prostřednictvím klienta X2Go) pomocí ikony na ploše. Klienta můžete také spustit pomocí následujícího příkazu v prostředí:

```bash
/usr/local/squirrel-sql-3.7/squirrel-sql.sh /usr/local/squirrel-sql-3.7/squirrel-sql.sh
```

Před prvním použitím nastavení ovladačů a aliasy databáze. Ovladače JDBC jsou umístěné na adrese/usr/share/Java/jdbcdrivers.

Další informace najdete v tématu [SQUIRREL SQL](http://squirrel-sql.sourceforge.net/index.php?page=screenshots).

#### <a name="command-line-tools-for-accessing-sql-server"></a>Nástroje příkazového řádku pro přístup k SQL Server

Balíček ovladače ODBC pro SQL Server také obsahuje dva nástroje příkazového řádku:

* **bcp**: Nástroj BCP hromadně kopíruje data mezi instancí SQL Server a datovým souborem do uživatelsky definovaného formátu. Pomocí nástroje BCP můžete importovat velký počet nových řádků do SQL Server tabulek nebo exportovat data mimo tabulky do datových souborů. Chcete-li importovat data do tabulky, je nutné použít formátový soubor vytvořený pro tuto tabulku. Nebo, musíte pochopit strukturu tabulky a typy dat, které jsou pro své sloupce platné.

  Další informace najdete v tématu [připojení pomocí BCP](https://msdn.microsoft.com/library/hh568446.aspx).

* **sqlcmd**: Pomocí nástroje Sqlcmd můžete do příkazového řádku zadat příkazy jazyka Transact-SQL, systémové procedury a soubory skriptu. Nástroj Sqlcmd používá rozhraní ODBC ke spouštění dávek Transact-SQL.

  Další informace najdete v tématu [připojení pomocí nástroje Sqlcmd](https://msdn.microsoft.com/library/hh568447.aspx).

  > [!NOTE]
  > Mezi platformami Linux a Windows jsou rozdíly v tomto nástroji. Viz podrobnosti naleznete v dokumentaci.

#### <a name="database-access-libraries"></a>Přístup ke knihovnám databáze

Knihovny pro přístup k databázi jsou k dispozici v R a Pythonu:

* V R můžete k dotazování nebo spouštění příkazů SQL na databázovém serveru použít balíček RODBC nebo balíček dplyr.
* V Pythonu poskytuje knihovna pyodbc přístup k databázi s rozhraním ODBC jako podkladovou vrstvu.

### <a name="azure-tools"></a>Nástroje Azure

Na DSVM jsou nainstalovány následující nástroje Azure:

* **Azure CLI**: Pomocí rozhraní příkazového řádku v Azure můžete vytvářet a spravovat prostředky Azure prostřednictvím příkazů prostředí. Pokud chcete otevřít nástroje Azure, zadejte **Azure Help**. Další informace najdete v tématu [stránku dokumentace k Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-az-cli2).
* **Průzkumník služby Azure Storage**: Průzkumník služby Azure Storage je grafický nástroj, pomocí kterého můžete procházet objekty, které jste uložili v účtu úložiště Azure, a nahrávat a stahovat data do a z objektů blob Azure. Průzkumník služby Storage se dá dostat z ikonu zástupce na ploše. Můžete ho také otevřít z příkazového řádku prostředí zadáním **StorageExplorer**. Musíte být přihlášeni z klienta X2Go nebo mít nastavené přesměrování X11.
* **Knihovny Azure**: Na DSVM jsou předem nainstalovány následující knihovny:
  
  * **Python**: Knihovny související s Azure v Pythonu jsou *Azure*, *AzureML*, *pydocumentdb*a *pyodbc*. První tři knihovny můžete přístup služby Azure storage, Azure Machine Learning a Azure Cosmos DB (databáze NoSQL v Azure). Čtvrtý knihovny pyodbc (spolu s ovladač Microsoft ODBC pro SQL Server), umožňuje přístup k systému SQL Server, Azure SQL Database a Azure SQL Data Warehouse z Pythonu pomocí rozhraní ODBC. Zadejte **pip seznamu** zobrazíte všechny uvedené knihovny. Ujistěte se, že tento příkaz spustit v případě Pythonu 2.7 i 3.5 prostředí.
  * **R**: Knihovny související s Azure v jazyce R jsou AzureML a RODBC.
  * **Java**: Seznam knihoven Azure Java najdete v adresáři/dsvm/sdk/AzureSDKJava na DSVM. Jsou klíčové knihovny Azure ovladače úložiště a správu rozhraní API služby Azure Cosmos DB a JDBC pro SQL Server.  

K [Azure Portal](https://portal.azure.com) můžete přistupovat z předinstalovaného prohlížeče Firefox. V Azure Portal můžete vytvářet, spravovat a monitorovat prostředky Azure.

### <a name="azure-machine-learning"></a>Azure Machine Learning

Azure Machine Learning je plně spravovaná cloudová služba, kterou můžete použít k sestavení, nasazení a sdílení řešení prediktivní analýzy. Vytváření experimentů a modelů z Azure Machine Learning Studio. Pokud chcete získat přístup k Azure Machine Learning z webového prohlížeče na DSVM, přejděte na [Microsoft Azure Machine Learning](https://studio.azureml.net).

Po přihlášení k Azure Machine Learning Studio můžete k vytvoření logického toku pro algoritmy strojového učení použít plátno experimentování. Máte také přístup k Jupyter Notebook, která je hostována v Azure Machine Learning. Poznámkový blok může bez problémů pracovat s experimenty v Machine Learning Studio. 

Zprovoznění modely strojového učení, které vytvoříte, jejich zabalením do rozhraní webové služby. Modely strojového učení zprovozňování umožňují klientům napsaným v jakémkoli jazyce vyvolat předpovědi z těchto modelů. Další informace najdete v tématu [dokumentace ke službě Machine Learning](https://azure.microsoft.com/documentation/services/machine-learning/).

Modely můžete vytvářet také v R nebo Pythonu na DSVM a pak je nasadit v produkčním prostředí na Azure Machine Learning. Microsoft má nainstalované knihovny v R (**AzureML**) a Pythonu (**AzureML**) pro podporu této funkce.

Informace o tom, jak nasadit modely v R a Pythonu do Azure Machine Learning, najdete v článku [o deseti věcí, které můžete na data Science Virtual Machine dělat](vm-do-ten-things.md).

> [!NOTE]
> Pokyny k [10 akcím, které můžete provádět na data Science Virtual Machine](vm-do-ten-things.md) byly napsány pro verzi DSVM systému Windows. Nicméně informace o nasazení modelů do Azure Machine Learning platí i pro Linux DSVM.

### <a name="machine-learning-tools"></a>Nástroje Machine learning

DSVM obsahuje několik nástrojů a algoritmů strojového učení, které jsou předkompilovány a předinstalované místně. Mezi ně patří:

* **Microsoft Cognitive Toolkit**: Sada nástrojů pro hloubkové učení
* **Pro dostupné**: Rychlý online algoritmus pro učení.
* **XGBoost**: Nástroj, který poskytuje optimalizované a posílené doménové algoritmy.
* **Python**: Anaconda Python se dodává s algoritmy strojového učení s knihovnami, jako je Scikit – učení. Můžete nainstalovat další knihovny pomocí `pip install` příkazu.
* **R**: K dispozici je bohatá knihovna funkcí strojového učení pro R. předinstalované knihovny zahrnují LM, GLM, randomForest a rpart. Můžete nainstalovat další knihovny spuštěním `install.packages(<lib name>)`.

Microsoft Cognitive Toolkit, pro dostupné a XGBoost jsou podrobněji popsány v dalších částech.

#### <a name="microsoft-cognitive-toolkit"></a>Microsoft Cognitive Toolkit

Microsoft Cognitive Toolkit je open source sada nástrojů pro hloubkové učení. Jedná se o nástroj příkazového řádku (CNTK) a je už v cestě.

Pokud chcete spustit základní ukázku, spusťte v prostředí následující příkazy:

```bash
cd /home/[USERNAME]/notebooks/CNTK/HelloWorld-LogisticRegression
cntk configFile=lr_bs.cntk makeMode=false command=Train
```

Další informace najdete v [úložišti GITHUB CNTK](https://github.com/Microsoft/CNTK) a na [wikiwebu CNTK](https://github.com/Microsoft/CNTK/wiki).

#### <a name="vowpal-wabbit"></a>Vowpal Wabbit

Pro dostupné je systém strojového učení, který využívá techniky jako online, hashing, allreduce, snižování, learning2search, aktivní a interaktivní učení.

Chcete-li spustit nástroj na základním příkladu, spusťte následující příkazy:

```bash
cp -r /dsvm/tools/VowpalWabbit/demo vwdemo
cd vwdemo
vw house_dataset
```

Ukázkový adresář pro dostupné obsahuje další, větší ukázky. Další informace o pro dostupné najdete v [úložišti GitHub pro dostupné](https://github.com/JohnLangford/vowpal_wabbit) a na [wikiwebu pro dostupné](https://github.com/JohnLangford/vowpal_wabbit/wiki).

#### <a name="xgboost"></a>XGBoost

Knihovna XGBoost je navržená a optimalizovaná pro zvýšení (stromové) algoritmy. Cílem knihovny XGBoost je nabízet omezení výpočtů počítačů až k extrémním hodnotám, které jsou potřeba k zajištění vysokého zvyšování stromové struktury, které jsou škálovatelné, přenosné a přesné.

XGBoost je k dispozici jako příkazový řádek a jako knihovna R.

Chcete-li použít knihovnu XGBoost v jazyce R, spusťte interaktivní relaci jazyka R (v prostředí, zadejte **R**) a poté knihovnu načtěte.

Tady je jednoduchý příklad, který můžete spustit na příkazovém řádku R:

```R
library(xgboost)

data(agaricus.train, package='xgboost')
data(agaricus.test, package='xgboost')
train <- agaricus.train
test <- agaricus.test
bst <- xgboost(data = train$data, label = train$label, max.depth = 2,
                eta = 1, nthread = 2, nround = 2, objective = "binary:logistic")
pred <- predict(bst, test$data)
```

Pro spuštění příkazového řádku XGBoost spusťte v prostředí tyto příkazy:

```bash
cp -r /dsvm/tools/xgboost/demo/binary_classification/ xgboostdemo
cd xgboostdemo
xgboost mushroom.conf
```

Soubor. model je zapsán do zadaného adresáře. Informace o tomto ukázkovém příkladu na GitHubu najdete v tématu [binární klasifikace](https://github.com/dmlc/xgboost/tree/master/demo/binary_classification).

Další informace o XGBoost najdete v dokumentaci k [XGBoost](https://xgboost.readthedocs.org/en/latest/) a v [úložišti GitHub XGBoost](https://github.com/dmlc/xgboost).

#### <a name="rattle"></a>Rattle

Rattle (*R* *a*nalytická *T*OOL *t*o *L*získat *E*asily) využívá zkoumání a modelování dat založeného na grafickém uživatelském rozhraní. Rattle:
- Prezentuje statistické a vizuální souhrny dat.
- Transformuje data, která lze snadno modelovat.
- Sestaví jak v režimu pod dohledem, tak i pod dohledem z dat.
- Graficky prezentuje výkon modelů.
- Vyrovnává nové sady dat.
- Generuje kód R.
- Replikuje operace v uživatelském rozhraní, které lze spustit přímo v R nebo použít jako výchozí bod pro další analýzu.

Aby bylo možné spustit Rattle, musíte být přihlášeni k grafické relaci plochy. V terminálu zadejte **r** a otevřete prostředí r. Na řádku R zadejte následující příkazy:

```R
library(rattle)
rattle()
```

Grafické rozhraní, které má otevřenou sadu karet. Pomocí následujících kroků rychlého startu v Rattle můžete použít ukázku sady dat počasí a vytvořit model. V některých krocích se zobrazí výzva, abyste automaticky nainstalovali a načetli některé požadované balíčky R, které ještě nejsou v systému.

> [!NOTE]
> Pokud nemáte oprávnění k instalaci balíčku do systémového adresáře (výchozí nastavení), může se v okně konzoly R zobrazit výzva, abyste mohli balíčky nainstalovat do osobní knihovny. Pokud se zobrazí tyto výzvy, zadejte **y**.

1. Vyberte **Provést**.
1. Zobrazí se dialogové okno s výzvou k načtení ukázkové sady dat počasí. Vyberte **Ano** , pokud chcete příklad načíst.
1. Vyberte kartu **model** .
1. Vyberte **provést** a sestavte rozhodovací strom.
1. Vyberte možnost **Kreslení** pro zobrazení rozhodovacího stromu.
1. Vyberte možnost **doménová struktura** a pak výběrem možnosti **Spustit** Vytvořte náhodnou doménovou strukturu.
1. Vyberte kartu vyhodnotit.
1. Vyberte možnost **rizika** a pak výběrem možnosti **Spustit** zobrazíte zobrazení se dvěma **riziky (kumulativní)** výkon.
1. Vyberte kartu **protokol** , chcete-li zobrazit generovaný kód R pro předchozí operace. (Kvůli chybě v aktuální verzi Rattle je nutné před **exportem tohoto protokolu** do textu **#** protokolu vložit znak.)
1. Vyberte tlačítko **exportovat** a uložte soubor skriptu jazyka R s názvem *weather_script. R* do domovské složky.

Můžete ukončit Rattle a R. Nyní můžete upravit generovaný skript R. Nebo použijte skript jako takový a spusťte ho kdykoli, abyste mohli opakovat všechno, co bylo provedeno v uživatelském rozhraní Rattle. Hlavně pro začátečníky v R je to způsob, jak rychle analyzovat a strojové učení v jednoduchém grafickém rozhraní, a to při automatickém generování kódu v jazyce R pro úpravy nebo pro učení.

## <a name="next-steps"></a>Další postup

Zde je, jak můžete pokračovat v učení a zkoumání:

* Návod pro [datovou vědu na data Science Virtual Machine pro Linux](linux-dsvm-walkthrough.md) vám ukáže, jak provést několik běžných úloh pro datové vědy se systémem Linux DSVM zřízeným zde. 
* Vyzkoušením nástrojů popsaných v tomto článku prozkoumejte různé nástroje pro datové vědy na DSVM. Pro základní Úvod a `dsvm-more-info` pro ukazatele na Další informace o nástrojích nainstalovaných v DSVM můžete také spustit v prostředí ve virtuálním počítači.  
* Další informace o vytváření analytická řešení začátku do konce systematicky pomocí [vědecké zpracování týmových dat](https://aka.ms/tdsp).
* Přejděte [galerii Azure AI](https://gallery.azure.ai/) pro machine learning a datové analýzy ukázek, které využívají služby Azure AI.