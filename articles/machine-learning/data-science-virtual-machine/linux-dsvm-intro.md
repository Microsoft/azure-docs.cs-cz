---
title: Zřízení CentOS Data Science virtuálního počítače s Linuxem v Azure | Dokumentace Microsoftu
description: Nakonfigurujte a vytvořte virtuální počítač s Linuxem Data Science v Azure a provádět analýzy a strojové učení.
services: machine-learning
documentationcenter: ''
author: gopitk
manager: cgronlun
ms.assetid: 3bab0ab9-3ea5-41a6-a62a-8c44fdbae43b
ms.service: machine-learning
ms.component: data-science-vm
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/16/2018
ms.author: gokuma
ms.openlocfilehash: 3883fc71c099718face38c16897040788fe0c6ab
ms.sourcegitcommit: 8ebcecb837bbfb989728e4667d74e42f7a3a9352
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/21/2018
ms.locfileid: "42054465"
---
# <a name="provision-a-linux-centos-data-science-virtual-machine-on-azure"></a>Zřízení CentOS Data Science virtuálního počítače s Linuxem v Azure

Virtuální počítač Linux datové vědy se systémem CentOS Azure virtuální počítač, který je součástí kolekce nástrojů pro předem nainstalované. Tyto nástroje se obvykle používají pro provádění analýz dat a strojové učení. Zahrnuté klíčové softwarové komponenty jsou:

* Operačního systému: Distribuce Linux CentOS.
* Microsoft R Server Developer Edition
* Anaconda Python distribuce (verze 2.7 a 3.5), včetně oblíbených dat knihovny analýz
* JuliaPro - kurátorované distribuci jazyk Julia s Oblíbené knihovny analýz vědecké a data
* Instanci prostředí Spark samostatné a jednoho uzlu Hadoop (HDFS, Yarn)
* JupyterHub - víceuživatelské server poznámkového bloku Jupyter s podporou jazyka R, Python, PySpark, Julia jádrech
* Azure Storage Explorer
* Rozhraní příkazového řádku Azure (CLI) pro správu prostředků Azure
* PostgresSQL databáze
* Nástroje Machine learning
  * [Sada cognitive Toolkit](https://github.com/Microsoft/CNTK): pro hloubkové učení softwarových nástrojů Microsoft Research.
  * [Vowpal Wabbit](https://github.com/JohnLangford/vowpal_wabbit): rychlé strojového učení systému, který podporuje techniky, jako je online, hash, allreduce, snížení, learning2search, aktivní a interaktivního vzdělávacího.
  * [XGBoost](https://xgboost.readthedocs.org/en/latest/): nástroj poskytuje rychlý a přesné Posílený stromu implementace.
  * [Rattle](https://togaware.com/rattle/) (R analytické nástroje pro další snadno): nástroj, který umožňuje zahájení práce s analýzy dat a strojové učení v jazyce R snadno s zkoumání dat na základě grafického uživatelského rozhraní a modelování se službou Automatické generování kódu R.
* Azure SDK v jazyce Java, Python, node.js, Ruby, PHP
* Knihovny v R a Python pro použití v Azure Machine Learning a dalšími službami Azure
* Nástroje pro vývoj a editory (RStudio, PyCharm, IntelliJ, (emacs), gedit, vi)


Datových věd zahrnuje iterace v pořadí úloh:

1. Vyhledání, načítání a předzpracování dat
1. Vytváření a testování modely
1. Nasazení modelů pro použití v inteligentních aplikacích

Odborníci přes data pomocí různých nástrojů k provedení těchto úloh. Může být velmi časově najít odpovídající verze softwaru, a pak stáhnout, kompilace a instalaci těchto verzí.

Virtuální počítač Linux datové vědy můžete podstatně usnadňují tato zatížení. Použijte ho k nastartování analytického projektu. Umožňuje vám pro práci na úkolech v různých jazycích, včetně R, Python, SQL, Java a C++. Eclipse poskytuje integrované vývojové prostředí pro vývoj a testování kódu, který se snadno používá. Zahrnuté ve virtuálním počítači Azure SDK můžete vytvářet své aplikace s použitím různých služeb v Linuxu pro cloudovou platformu Microsoft. Kromě toho máte přístup do jiných jazyků, jako jsou Ruby, Perl, PHP a node.js, které jsou také předem nainstalované.

Neúčtují žádné poplatky softwaru pro image virtuálního počítače Toto datové vědy. Platíte jenom Azure hardware poplatky za využití, které jsou vyhodnocené na základě velikosti virtuálního počítače, kterou zřídíte v imagi virtuálního počítače. Další podrobnosti o poplatky za výpočetní prostředky můžete najít na [stránce virtuálního počítače na webu Azure Marketplace](https://azure.microsoft.com/marketplace/partners/microsoft-ads/linux-data-science-vm/).

## <a name="other-versions-of-the-data-science-virtual-machine"></a>Jiné verze virtuální počítač pro datové vědy
[Ubuntu](dsvm-ubuntu-intro.md) image je taky dostupná s řadu stejných nástrojů jako CentOS image a hloubkové učení architektury. A [Windows](provision-vm.md) image je k dispozici také.

## <a name="prerequisites"></a>Požadavky
Než vytvoříte virtuální počítač s Linuxem datové vědy, musíte mít následující:

* **Předplatné Azure**: ho získat, najdete v článku [získání bezplatné zkušební verze Azure](https://azure.microsoft.com/free/).
* **Účet úložiště Azure**: Pokud chcete jeden vytvořit, přečtěte si téma [vytvoření účtu služby Azure storage](../../storage/common/storage-quickstart-create-account.md). Případně pokud nechcete použít existující účet, účet úložiště vytvořit jako součást procesu vytváření virtuálního počítače.

## <a name="create-your-linux-data-science-virtual-machine"></a>Vytvořit virtuální počítač Linux datové vědy
Tady jsou kroky pro vytvoření instance z Linuxu virtuální počítač pro datové vědy:

1. Přejděte na virtuální počítač [webu Azure portal](https://portal.azure.com/#create/microsoft-ads.linux-data-science-vmlinuxdsvm).
1. Klikněte na tlačítko **vytvořit** (dole) a zobrazte si průvodce.![ Konfigurace data vědy vm](./media/linux-dsvm-intro/configure-linux-data-science-virtual-machine.png)
1. Vstupy pro každý z kroků v průvodci (uvedené na pravé straně na předchozím obrázku) použitý k vytvoření virtuálního počítače Microsoft datové vědy v následujících částech. Tady jsou vstupy potřebné ke konfiguraci každý z těchto kroků:
   
   a. **Základy**:
   
   * **Název**: název serveru datové vědy vytváříte.
   * **Uživatelské jméno**: první účet přihlásit ID.
   * **Heslo**: první heslo účtu (veřejný klíč SSH můžete použít namísto hesla).
   * **Předplatné**: Pokud máte více předplatných, vyberte ten, ve které je vytvořené a fakturuje počítač. Musíte mít oprávnění vytváření prostředků pro toto předplatné.
   * **Skupina prostředků**: vytvořit nové nebo použijte existující skupinu.
   * **Umístění**: Vyberte datové centrum, které je nejvhodnější. Obvykle je většina vašich dat, nebo je nejblíže vašemu fyzickému umístění pro nejrychlejší přístup k síti datového centra.
   
   b. **Velikost**:
   
   * Vyberte jeden z typů serveru, které splňuje požadavek na funkční a náklady na omezení. Vyberte **zobrazení všech** zobrazíte další možnosti velikostí virtuálních počítačů.
   
   c. **Nastavení**:
   
   * **Typ disku**: Zvolte **Premium** Pokud dáváte přednost jednotky SSD (Solid-State Drive). Jinak klikněte na tlačítko **standardní**.
   * **Účet úložiště**: můžete vytvořit nový účet úložiště Azure v rámci vašeho předplatného nebo použijte stávající ve stejném umístění, který byl vybrán na **Základy** kroku v průvodci.
   * **Další parametry**: ve většině případů stačí použít výchozí hodnoty. Vzít v úvahu jiné než výchozí hodnoty, najeďte myší na informační odkaz nápovědy pro konkrétní pole.
   
   d. **Souhrn**:
   
   * Ověřte správnost všech informací, které jste zadali.
   
   e. **Koupit**:
   
   * Zřizování spusťte kliknutím na **koupit**. Pomocí odkazu uvedeného na podmínky transakce. Virtuální počítač nemá žádné další poplatky za výpočetní prostředky pro velikost serveru, kterou jste zvolili v **velikost** kroku.

Zřizování by měla trvat asi 10-20 minut. Stav zřizování se zobrazí na portálu Azure portal.

## <a name="how-to-access-the-linux-data-science-virtual-machine"></a>Jak získat přístup k virtuální počítač Linux datové vědy
Po vytvoření virtuálního počítače se můžete přihlásit se pomocí protokolu SSH. Pomocí přihlašovacích údajů účtu, které jste vytvořili v **Základy** část krok 3 pro rozhraní text prostředí. Na Windows, si můžete stáhnout nástroj klienta SSH jako [Putty](http://www.putty.org). Pokud dáváte přednost grafické desktop (X systému Windows), můžete použít X11 předávání v Putty nebo nainstalovat klienta X2Go.

> [!NOTE]
> Klient X2Go proveden výrazně lepší než X11 předávání v testování. Doporučujeme používat X2Go klienta klasické pracovní plochy grafické rozhraní.
> 
> 

## <a name="installing-and-configuring-x2go-client"></a>Instalace a konfigurace X2Go klienta
Virtuální počítač s Linuxem už zřízeny X2Go serveru a připravené tak, aby přijímal připojení klienta. Pro připojení k desktopu grafické virtuálního počítače s Linuxem, udělejte na klienta:

1. Stažení a instalace klienta X2Go pro vaše klientská platforma z [X2Go](http://wiki.x2go.org/doku.php/doc:installation:x2goclient).    
1. Spusťte klienta X2Go a vyberte **novou relaci**. Otevře se okno Konfigurace s více karet. Zadejte následující parametry konfigurace:
   * **Karta relace**:
     * **Hostitel**: název hostitele nebo IP adresu virtuálního počítače s Linuxem datové vědy.
     * **Přihlášení**: uživatelské jméno na Linuxovém virtuálním počítači.
     * **SSH Port**: ponechte výchozí hodnota 22.
     * **Typ relace**: Změňte hodnotu na XFCE. Virtuální počítač s Linuxem v současné době podporuje pouze Desktop xfce.
   * **Karta média**: Pokud není nutné použít zvukové podpory a klient tisku, můžete je vypnout.
   * **Sdílené složky**: Pokud chcete adresářů z klientských počítačů připojené na Linuxovém virtuálním počítači, přidejte adresáře klientské počítače, které chcete sdílet s virtuálním Počítačem na této kartě.

Po přihlášení k virtuálnímu počítači pomocí klienta SSH nebo grafické Desktop xfce prostřednictvím klienta X2Go, jste připraveni začít používat nástroje, které jsou nainstalované a nakonfigurované na virtuálním počítači. Na XFCE uvidíte aplikacích zástupci v nabídce a ikony na ploše pro celou řadu nástrojů.

## <a name="tools-installed-on-the-linux-data-science-virtual-machine"></a>Nástroje nainstalované v systému Linux virtuální počítač pro datové vědy
### <a name="microsoft-r-server"></a>Microsoft R Server
R je jedním z nejoblíbenějších jazyků pro analýzy dat a strojové učení. Pokud chcete používat R pro analýzy, virtuální počítač má Microsoft R Server (Paní) s Microsoft R Open (MRO) a matematické jádra knihovny (MKL). MKL optimalizuje matematických operací, které jsou běžné v analytických algoritmů. MRO je 100 % kompatibilní s CRAN r. a některé z knihoven jazyka R, které jsou publikovány v síti CRAN se dá nainstalovat na MRO. PANÍ umožňuje škálování a operacionalizace R modely do webové služby. Můžete upravit sady R v jednom z výchozí editorů, jako je RStudio, vi, (emacs) nebo gedit. Pokud používáte editor (emacs), Všimněte si, že Emacs balíček UPU (mluví statistiky (emacs)), což zjednodušuje práce se soubory R v editoru (emacs) je už předem nainstalované.

Spuštění R konzoly, stačí zadat **R** v prostředí. Tím přejdete na interaktivní prostředí. K vývoji aplikace R, obvykle použít editor, například (emacs) nebo editoru vi nebo gedit a pak spusťte skripty v rámci jazyka R. S prostředím RStudio máte úplné grafické prostředí IDE k vývoji aplikace R.

K dispozici je také skriptu jazyka R můžete nainstalovat [balíčky Top 20 R](http://www.kdnuggets.com/2015/06/top-20-r-packages.html) potřebujete. Tento skript můžete spustit, jakmile se v rozhraní interaktivní R, které může uživatel zadat (jak je uvedeno) tak, že zadáte **R** v prostředí.  

### <a name="python"></a>Python
Pro vývoj pomocí Pythonu byl nainstalován distribuce Anaconda Python 2.7 a 3.5. Toto rozdělení obsahuje základní Python spolu s přibližně 300 nejoblíbenějších balíčků analytics matematické, inženýrství a data. Můžete použít výchozí textových editorů. Kromě toho můžete použít Spyder, integrované vývojové prostředí Pythonu, který je součástí distribuce Anaconda Python. Spyder potřebuje grafické stolní počítač nebo X11 předávání. Zástupce Spyder je součástí grafické desktop.

Protože máme Python 2.7 a 3.5, musíte výslovně aktivujte požadované verzi Pythonu (prostředí conda) chcete pracovat v aktuální relaci. Proces aktivace nastavuje proměnnou cesty na požadovanou verzi Pythonu.

Pokud chcete aktivovat prostředí conda Python 2.7, spusťte následující příkaz z prostředí:

    source /anaconda/bin/activate root

Je nainstalovaný Python 2.7 na */anaconda/bin*.

Pokud chcete aktivovat prostředí conda Python 3.5, spuštěním následujícího příkazu v prostředí:

    source /anaconda/bin/activate py35


Je nainstalován Python 3.5 na */anaconda/envs/py35/bin*.

Abyste mohli vyvolat interaktivní relace Pythonu, stačí zadat **python** v prostředí. Pokud jste na grafického rozhraní nebo máte X11 předávání set up, můžete zadat **pycharm** ke spuštění Pythonu PyCharm integrovaného vývojového prostředí.

Pokud chcete nainstalovat další knihovny jazyka Python, budete muset spustit ```conda``` nebo ````pip```` příkazů v rámci sudo a zadejte úplnou cestu Správce balíčků Pythonu (conda nebo pip) nainstalovat do správného prostředí Python. Příklad:

    sudo /anaconda/bin/pip install <package> #pip for Python 2.7
    sudo /anaconda/envs/py35/bin/pip install <package> #pip for Python 3.5
    sudo /anaconda/bin/conda install [-n py27] <package> #conda for Python 2.7, default behavior
    sudo /anaconda/bin/conda install -n py35 <package> #conda for Python 3.5


### <a name="jupyter-notebook"></a>Poznámkový blok Jupyter
Distribuce Anacondy také součástí poznámkového bloku Jupyter, prostředí pro sdílení kódu a analýzy. Poznámkový blok Jupyter je přístupný prostřednictvím JupyterHub. Přihlášení pomocí místního systému Linux uživatelské jméno a heslo.

Server poznámkového bloku Jupyter předem nakonfigurovaný s Python 2, Python 3 a R jádra. Existuje ikona na ploše s názvem "Poznámkový blok Jupyter" spustit prohlížeč pro přístup k serveru poznámkového bloku. Pokud jste na virtuálním počítači přes SSH nebo X2Go klienta, můžete také navštívit [ https://localhost:8000/ ](https://localhost:8000/) pro přístup k serveru Poznámkový blok Jupyter.

> [!NOTE]
> Pokračujte, pokud chcete získat všechna upozornění certifikátu.
> 
> 

Server poznámkového bloku Jupyter můžete přistupovat z libovolného hostitele. Stačí zadat *https://\<název DNS virtuálního počítače nebo IP adresu\>: 8000 /*

> [!NOTE]
> Port 8000 je otevřen v bráně firewall ve výchozím nastavení při zřízení virtuálního počítače.
> 
> 

Sbalené ukázkové poznámkové bloky – jednu v Pythonu a jeden v jazyce R. Zobrazí se odkaz na ukázky na domovské stránce Poznámkový blok po ověření do poznámkového bloku Jupyter s použitím místního systému Linux uživatelské jméno a heslo. Můžete vytvořit nový poznámkový blok tak, že vyberete **nový**a potom příslušný jazyk jádra. Pokud se nezobrazí **nový** tlačítko, klikněte na tlačítko **Jupyter** ikony v levém horním rohu přejít na domovskou stránku server poznámkového bloku.

### <a name="apache-spark-standalone"></a>Apache Spark samostatné 
Samostatná Apache Spark je předinstalován v systému Linux datové VĚDY můžete vyvíjet aplikace Spark místně nejprve před testování a nasazení na velkých clusterech. Jádra aplikace Jupyter můžete si projít programy PySpark. Když otevřete Jupyter a klikněte na tlačítko **nový** tlačítko, zobrazí se seznam dostupných jader. "– Python Spark" je jádra PySpark, který vám umožňuje vytvářet aplikace pomocí jazyka Python Spark. Integrované vývojové prostředí Pythonu, jako je PyCharm nebo Spyder můžete také použít k sestavení, můžete program Sparku. Od to je samostatná, Spark zásobníku běží v rámci programu volajícího klienta. To umožňuje rychlejší a snazší řešení problémů ve srovnání s vývojem v clusteru Spark. 

Poznámkový blok PySpark ukázka je k dispozici na Jupyter, které můžete najít v adresáři "Ve SparkML" v domovském adresáři Jupyter ($HOME/poznámkových bloků/ve SparkML/pySpark). 

Pokud programujete v jazyce R pro Spark, můžete použít Microsoft R Server SparkR nebo sparklyr. 

Před spuštěním v kontextu Sparku Microsoft R serveru, je potřeba jeden čas instalace krok umožňující místní Hadoop HDFS a Yarn instance jednoho uzlu. Ve výchozím nastavení služby Hadoop jsou nainstalované ale deaktivovány datové VĚDY. Chcete-li ji povolit, je třeba spustit následující příkazy jako uživatel root při prvním:

    echo -e 'y\n' | ssh-keygen -t rsa -P '' -f ~hadoop/.ssh/id_rsa
    cat ~hadoop/.ssh/id_rsa.pub >> ~hadoop/.ssh/authorized_keys
    chmod 0600 ~hadoop/.ssh/authorized_keys
    chown hadoop:hadoop ~hadoop/.ssh/id_rsa
    chown hadoop:hadoop ~hadoop/.ssh/id_rsa.pub
    chown hadoop:hadoop ~hadoop/.ssh/authorized_keys
    systemctl start hadoop-namenode hadoop-datanode hadoop-yarn

Hadoop můžete zastavit služby související s, když je nepotřebujete spuštěním ````systemctl stop hadoop-namenode hadoop-datanode hadoop-yarn```` ukázka ukazuje, jak vyvíjet a testovat paní ve vzdálené kontextu Spark (což je samostatné instanci Spark na datové VĚDY), je poskytovaná a k dispozici v `/dsvm/samples/MRS` adresář. 

### <a name="ides-and-editors"></a>Editory a integrovanými vývojovými prostředími
Máte možnost výběru z několika editory kódu. To zahrnuje vi/VIM (emacs), gEdit, PyCharm, RStudio, Eclipse a IntelliJ. gEdit, Eclipse, IntelliJ, RStudio nebo PyCharm jsou grafické editory a musíte být přihlášeni grafické desktopu k jejich použití. Tyto editory obsahují desktopových a aplikačních zástupci v nabídce je spustit.

**VIM** a **Emacs** jsou textové editory. Do (emacs) jsme nainstalovali balíček doplňku volat (emacs) mluví statistiky (UPU), která usnadňuje práci s jazykem R v editoru (emacs). Další informace najdete v [UPU](http://ess.r-project.org/).

**Eclipse** je typu open source, rozšiřitelné integrované vývojové prostředí, která podporuje více jazyků. Edice vývojáře Java je instance nainstalovaný na virtuálním počítači. Nejsou k dispozici pro několik oblíbených jazyků, které je možné nainstalovat rozšíření prostředí moduly plug-in. Modul plug-in nainstalované v Eclipse, volá se, máme také **sady Azure Toolkit pro Eclipse**. Umožňuje vytvořit, vývoj, testování a nasazování aplikací Azure pomocí vývojového prostředí Eclipse, který podporuje jazyky jako Java. K dispozici je také **sady Azure SDK pro Javu** , který umožňuje přístup do různých služeb Azure z prostředí Java. Další informace o sadě Azure toolkit pro Eclipse najdete v [sady Azure Toolkit pro Eclipse](../../azure-toolkit-for-eclipse.md).

**LaTex** se instaluje prostřednictvím balíčku texlive spolu s doplňkem (emacs) [auctex](https://www.gnu.org/software/auctex/manual/auctex/auctex.html) balíček, který zjednodušuje vytváření dokumentů LaTex v rámci (emacs).  

### <a name="databases"></a>Databáze
#### <a name="postgres"></a>Postgres
Open source databáze **Postgres** je k dispozici na virtuálním počítači se služby spuštěné a initdb již byla dokončena. Stále potřebujete k vytváření databází a uživatelů. Další informace najdete v tématu [Postgres dokumentaci](https://www.postgresql.org/docs/).  

#### <a name="graphical-sql-client"></a>Grafický klient SQL
**SQuirrel SQL**, grafický klient SQL byl poskytnut se připojit do různých databází (jako je například Microsoft SQL Server, Postgres a MySQL) a ke spouštění dotazů SQL. Lze ji spustit z grafického desktopové relace (například pomocí klienta X2Go). K vyvolání SQuirrel SQL, můžete ji spustit z ikony na ploše nebo spuštěním následujícího příkazu v prostředí.

    /usr/local/squirrel-sql-3.7/squirrel-sql.sh

Před prvním použitím nastavení ovladačů a aliasy databáze. Ovladače JDBC jsou umístěné na:

*/usr/share/java/jdbcdrivers*

Další informace najdete v tématu [SQuirrel SQL](http://squirrel-sql.sourceforge.net/index.php?page=screenshots).

#### <a name="command-line-tools-for-accessing-microsoft-sql-server"></a>Nástroje příkazového řádku pro přístup k systému Microsoft SQL Server
Balíček ovladače ODBC pro SQL Server také obsahuje dva nástroje příkazového řádku:

**BCP**: hromadné nástroj bcp kopíruje data mezi instance systému Microsoft SQL Server a datový soubor ve formátu zadané uživatelem. Nástroje bcp lze použít k importu velkého počtu nových řádků do tabulky serveru SQL Server nebo exportovat data z tabulek do datových souborů. Import dat do tabulky, musíte použít soubor formátu vytvořený pro tabulku nebo pochopit strukturu tabulky a typy dat, které jsou platné pro její sloupce.

Další informace najdete v tématu [připojení pomocí bcp](https://msdn.microsoft.com/library/hh568446.aspx).

**SqlCmd**: můžete zadat příkazů jazyka Transact-SQL s Nástroj sqlcmd, jakož i postupy systému a soubory na příkazovém řádku skriptů. Tento nástroj používá rozhraní ODBC pro spuštění dávky Transact-SQL.

Další informace najdete v tématu [připojení pomocí sqlcmd](https://msdn.microsoft.com/library/hh568447.aspx).

> [!NOTE]
> Existují určité rozdíly v tento nástroj mezi platformami operačních systémů Linux a Windows. Viz podrobnosti naleznete v dokumentaci.
> 
> 

#### <a name="database-access-libraries"></a>Přístup ke knihovnám databáze
Nejsou k dispozici v R a Python pro přístup k databázím knihovny.

* V jazyce R **RODBC** balíčku nebo **dplyr** balíček umožňuje zadat dotaz nebo spouštění příkazů jazyka SQL na serveru databáze.
* V jazyce Python **pyodbc** knihovna poskytuje přístup k databázi s rozhraním ODBC jako základní vrstvě.  

Pro přístup k **Postgres**:

* Z R: Použít balíček **RPostgreSQL**.
* Z Pythonu: Použijte **psycopg2** knihovny.

### <a name="azure-tools"></a>Nástroje Azure
Na virtuálním počítači jsou nainstalované nástroje Azure následující:

* **Rozhraní příkazového řádku Azure**: příkazového řádku Azure umožňuje vytvářet a spravovat prostředky Azure prostřednictvím příkazů prostředí. Abyste mohli vyvolat nástrojů Azure, stačí zadat **azure pomáhají**. Další informace najdete v tématu [stránku dokumentace k Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-az-cli2).
* **Microsoft Azure Storage Explorer**: Microsoft Azure Storage Explorer je grafické nástroj, který slouží k procházení objektů, které jsou uložené v účtu služby Azure storage a k nahrávání a stahování dat do a z objektů BLOB Azure. Průzkumník služby Storage se dá dostat z ikonu zástupce na ploše. Můžete ho vyvolat na příkazovém řádku prostředí tak, že zadáte **StorageExplorer**. Musíte být přihlášeni z klienta X2Go nebo mít X11 předávání set up.
* **Knihovny Azure**: Toto jsou některé z předem nainstalovaných knihoven.
  
  * **Python**: jsou knihovny související s Azure v Pythonu, které jsou nainstalovány **azure**, **azureml**, **pydocumentdb**, a **pyodbc**. První tři knihovny můžete přístup služby Azure storage, Azure Machine Learning a Azure Cosmos DB (databáze NoSQL v Azure). Čtvrtý knihovny pyodbc (spolu s ovladač Microsoft ODBC pro SQL Server), umožňuje přístup k systému SQL Server, Azure SQL Database a Azure SQL Data Warehouse z Pythonu pomocí rozhraní ODBC. Zadejte **pip seznamu** zobrazíte všechny uvedené knihovny. Ujistěte se, že tento příkaz spustit v případě Pythonu 2.7 i 3.5 prostředí.
  * **R**: jsou související s Azure knihovny v jazyce R, které jsou nainstalovány **AzureML** a **RODBC**.
  * **Java**: Seznam knihovny Java v Azure najdete v adresáři **/dsvm/sdk/AzureSDKJava** na virtuálním počítači. Jsou klíčové knihovny Azure ovladače úložiště a správu rozhraní API služby Azure Cosmos DB a JDBC pro SQL Server.  

Můžete přistupovat [webu Azure portal](https://portal.azure.com) předinstalované prohlížeče Firefox. Na portálu Azure portal můžete vytvářet, spravovat a monitorovat prostředky Azure.

### <a name="azure-machine-learning"></a>Azure Machine Learning
Azure Machine Learning je plně spravovaná Cloudová služba, která umožňuje vytvořit, nasadit a sdílení řešení prediktivní analýzy. Vytváření experimentů a modelů z Azure Machine Learning Studio. Byla přístupná z webového prohlížeče na virtuální počítač pro datové vědy návštěvou [Microsoft Azure Machine Learning](https://studio.azureml.net).

Po přihlášení k Azure Machine Learning Studio máte přístup k plátno pro experimentování ve kterém můžete vytvořit logický tok pro algoritmech strojového učení. Také mají přístup do poznámkového bloku Jupyter, které jsou hostované v Azure Machine Learning a můžete bez problémů pracovat s experimenty v nástroji Machine Learning Studio. Zprovoznění modelů strojového učení, které jste vytvořili podle jejich zabalení v rozhraní webových služeb. To umožňuje klientům napsané v libovolném jazyce vyvolat predikcím ze strojového učení modely. Další informace najdete v tématu [dokumentace ke službě Machine Learning](https://azure.microsoft.com/documentation/services/machine-learning/).

Můžete také sestavovat modely v R nebo Pythonu ve virtuálním počítači a potom ji nasadíte v produkčním prostředí v Azure Machine Learning. Jsme nainstalovali knihovny v jazyce R (**AzureML**) a Python (**azureml**) Chcete-li povolit tuto funkci.

Informace o tom, jak nasadit modely R a Pythonu do Azure Machine Learning najdete v tématu [deset věcí, které můžete provést na virtuální počítač pro datovou vědu](vm-do-ten-things.md) (zejména v části "sestavovat modely s využitím R nebo Python a Zprovozňovat jejich používání Azure Machine Learning").

> [!NOTE]
> Tyto pokyny byly vytvořeny pro verze Windows virtuální počítač pro datové vědy. Ale uvedené informace o nasazení modelů Azure Machine Learning je pro virtuální počítač s Linuxem.
> 
> 

### <a name="machine-learning-tools"></a>Nástroje Machine learning
Virtuální počítač obsahuje několik strojového učení, nástroje a algoritmy, které byly předem zkompilovat a předem nainstalovaný místně. Mezi ně patří:

* **Microsoft Cognitive Toolkit** : pro hloubkové učení toolkit.
* **Vowpal Wabbit**: algoritmus rychlého online výukové kurzy.
* **xgboost**: nástroj, který poskytuje optimalizované, zvýšená stromu algoritmy.
* **Python**: Anaconda Python se dodává jako součást balíčku s algoritmy strojového učení s knihovnami, jako je Scikit poučení. Můžete nainstalovat další knihovny pomocí `pip install` příkazu.
* **R**: bohatá knihovna funkcí machine learning je dostupná pro jazyk R. Některé z knihoven, které jsou předem nainstalované jsou lm, glm, randomForest, rpart. Další knihovny se dá nainstalovat spuštěním:
  
        install.packages(<lib name>)

Zde jsou některé další informace o nástrojích pro první tři strojového učení v seznamu.

#### <a name="microsoft-cognitive-toolkit"></a>Microsoft Cognitive Toolkit
Toto je open source, obsáhlý learning toolkit. Je nástroj příkazového řádku (cntk) a je již v CESTĚ.

Základní ukázku spustit, spusťte následující příkazy v prostředí:

    cd /home/[USERNAME]/notebooks/CNTK/HelloWorld-LogisticRegression
    cntk configFile=lr_bs.cntk makeMode=false command=Train

Další informace najdete v tématu část CNTK [Githubu](https://github.com/Microsoft/CNTK)a [CNTK wiki](https://github.com/Microsoft/CNTK/wiki).

#### <a name="vowpal-wabbit"></a>Vowpal Wabbit
Vowpal Wabbit je strojové učení systém, který používá techniky, jako je online, hash, allreduce, snížení, learning2search, aktivní a interaktivního vzdělávacího.

Chcete-li spustit nástroj na základní příklad, postupujte takto:

    cp -r /dsvm/tools/VowpalWabbit/demo vwdemo
    cd vwdemo
    vw house_dataset

Existují jiné, větší ukázky v tomto adresáři. Další informace o zobrazit najdete v tématu [v této části GitHub](https://github.com/JohnLangford/vowpal_wabbit)a [Vowpal Wabbit wiki](https://github.com/JohnLangford/vowpal_wabbit/wiki).

#### <a name="xgboost"></a>xgboost
Toto je knihovnu, která je navržený a optimalizovaný pro algoritmy Posílený (strom). Cílem této knihovny je tak, aby nabízel výpočet omezení počítačů extrémy potřebných k poskytování rozsáhlé stromové struktury zvýšení skóre, která je škálovatelná, přenosných a přesné.

Je zadaný jako příkazového řádku, jakož i knihovny R.

Použití této knihovny v jazyce R, můžete spustit interaktivní relace jazyka R (pouze zadáním **R** v prostředí) a načíst knihovnu.

Tady je jednoduchý příklad, který můžete spustit v R řádku:

    library(xgboost)

    data(agaricus.train, package='xgboost')
    data(agaricus.test, package='xgboost')
    train <- agaricus.train
    test <- agaricus.test
    bst <- xgboost(data = train$data, label = train$label, max.depth = 2,
                    eta = 1, nthread = 2, nround = 2, objective = "binary:logistic")
    pred <- predict(bst, test$data)

Spusťte příkazový řádek xgboost, tady jsou příkazy ke spuštění v prostředí:

    cp -r /dsvm/tools/xgboost/demo/binary_classification/ xgboostdemo
    cd xgboostdemo
    xgboost mushroom.conf


.Model soubor zapsán do zadaný adresář. Informace o tomto příkladu ukázku najdete [na Githubu](https://github.com/dmlc/xgboost/tree/master/demo/binary_classification).

Další informace o xgboost, najdete v článku [stránky dokumentace xgboost](https://xgboost.readthedocs.org/en/latest/)a jeho [úložiště GitHub](https://github.com/dmlc/xgboost).

#### <a name="rattle"></a>Rattle
Rattle ( **R** **A**nalytická **T**rukopis **T**o **L**získat **E** asily) používá zkoumání a modelování dat na základě grafického uživatelského rozhraní. Zobrazí statistické a vizuální souhrny dat, transformace dat, která lze snadno modelovat, sestavení bez dohledu a pod dohledem modely z dat, uvede výkon modelů graficky, a nastaví skóre, které se nová data. Také vygeneruje kód R, replikaci operace v uživatelském rozhraní, které můžete spustit přímo v R nebo použít jako výchozí bod pro další analýzu.

Pokud chcete spustit Rattle, musíte být v grafickém přihlašovací relaci plochy. V terminálu zadejte ```R``` zadat prostředí R. Na řádku R zadejte následující příkazy:

    library(rattle)
    rattle()

Nyní grafické rozhraní otevře sadu karet. Tady jsou kroky pro rychlé spuštění v Rattle potřeba použít ukázkovou datovou sadu počasí a sestavení modelu. V některé z následujících kroků zobrazí se výzva k automaticky nainstaluje a načte některé požadované balíčky jazyka R, které ještě nejsou v systému.

> [!NOTE]
> Pokud nemáte přístup k instalaci balíčku v adresáři systému (výchozí), může se zobrazit výzva v okně konzoly R k instalaci balíčků na své osobní knihovny. Odpověď *y* -li zobrazit tyto výzvy.
> 
> 

1. Klikněte na tlačítko **Spustit**.
1. Otevře se dialogové okno, s výzvou, pokud chcete použít příklad sady dat o počasí. Klikněte na tlačítko **Ano** načíst v příkladu.
1. Klikněte na tlačítko **modelu** kartu.
1. Klikněte na tlačítko **Execute** sestavit rozhodovací strom.
1. Klikněte na tlačítko **nakreslit** zobrazíte rozhodovací strom.
1. Klikněte na tlačítko **doménové struktury** přepínač a klikněte na tlačítko **Execute** vytvářet náhodné doménové struktury.
1. Klikněte na tlačítko **vyhodnotit** kartu.
1. Klikněte na tlačítko **riziko** přepínač a klikněte na tlačítko **Execute** zobrazíte dva grafy výkonu rizika (součet).
1. Klikněte na tlačítko **protokolu** zobrazte vytvořit R kód pro předchozí operace.
   (Kvůli chybě v aktuální verzi Rattle, je třeba jej vložit *#* znak před *exportovat tento protokol...*  v textu v protokolu.)
1. Klikněte na tlačítko **exportovat** tlačítko pro uložení souboru skriptu jazyka R s názvem *weather_script. R* do domovské složky.

Můžete ukončit Rattle a R. Nyní můžete upravit vygenerovaný skript jazyka R nebo ho použít, protože ho můžete kdykoli spustit vše, co se provádí v Uživatelském rozhraní Rattle opakovat. Zejména pro začátečníky v jazyce R Toto je snadný způsob, jak rychle provést analýzu a strojové učení v jednoduché grafického rozhraní, při automatické generování kódu v jazyce R můžete upravit nebo Další informace.

## <a name="next-steps"></a>Další postup
Zde je, jak můžete pokračovat v učení a zkoumání:

* [Vědecké zpracování dat v systému Linux virtuální počítač pro datové vědy](linux-dsvm-walkthrough.md) návodu se dozvíte, jak provést několik běžných úloh datové vědy Data Science virtuálního počítače s Linuxem zřízené tady. 
* Prozkoumejte různé nástrojů pro datové vědy pro datovou vědu virtuální počítač a vyzkoušejte si nástroje popsané v tomto článku. Můžete také spustit *dsvm. více informací* na prostředí v rámci virtuálního počítače pro základní informace a odkazy na další informace o těchto nástrojích, nainstalovaný na virtuálním počítači.  
* Další informace o vytváření analytická řešení začátku do konce systematicky pomocí [vědecké zpracování týmových dat](https://azure.microsoft.com/documentation/learning-paths/cortana-analytics-process/).
* Přejděte [galerii Cortana Analytics](http://gallery.cortanaanalytics.com) pro machine learning a datové analýzy ukázky, které používají Cortana Analytics Suite.

