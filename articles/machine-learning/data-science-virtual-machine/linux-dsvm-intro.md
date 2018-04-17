---
title: Zřídit CentOS datové vědy virtuální počítač s Linuxem v Azure | Microsoft Docs
description: Nakonfigurujte a vytvořte virtuální počítač s Linuxem datové vědy na Azure a provádět analýzy a strojového učení.
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
ms.openlocfilehash: 1a201974749acbbb9607e42e67d1935f437f9ca1
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
---
# <a name="provision-a-linux-centos-data-science-virtual-machine-on-azure"></a>Zřídit CentOS datové vědy virtuální počítač s Linuxem v Azure

Virtuální počítač Linux Data vědecké účely je na základě CentOS Azure virtuální počítač, který se dodává s kolekce předem nainstalovaná nástrojů. Tyto nástroje jsou běžně používá pro provádění analýzy dat a strojové učení. Klíčové softwarové komponenty zahrnuté jsou:

* Operační systém: Distribuce Linux CentOS.
* Microsoft R Server Developer Edition
* Anaconda distribuci jazyka Python (verze 2.7 a 3.5), včetně dat Oblíbené knihovny analýz
* JuliaPro - kurátorované distribuční jazyka Dita pomocí Oblíbené knihovny analytics scientific a data
* Spark samostatné instance a jednoho uzlu Hadoop (HDFS, Yarn)
* JupyterHub - víceuživatelská server poznámkového bloku Jupyter s podporou R, Python, PySpark, Dita jádra
* Azure Storage Explorer
* Rozhraní příkazového řádku Azure (CLI) ke správě prostředků Azure
* PostgresSQL databáze
* Nástroje Machine learning
  * [Kognitivní Toolkit](https://github.com/Microsoft/CNTK): hluboká učení softwaru nástrojů Microsoft Research.
  * [K dispozici Vowpal](https://github.com/JohnLangford/vowpal_wabbit): rychlé strojového učení systému, který podporuje, jako jsou online a hash, allreduce, snížení, learning2search, aktivní a interaktivní učení.
  * [XGBoost](https://xgboost.readthedocs.org/en/latest/): nástroj poskytuje rychlé a přesné boosted stromu implementace.
  * [Rattle](http://rattle.togaware.com/) (R Analytical nástroj pro další snadno): nástroj, který umožňuje Začínáme s data analytics a počítač učení v R snadno s zkoumání dat založených na grafickém uživatelském rozhraní a modelování pomocí automatického generování kódu R.
* Azure SDK v jazyce Java, Python, node.js, Ruby, PHP
* Knihovny v R a Python pro použití v Azure Machine Learning a jinými službami Azure
* Nástroje pro vývoj a editory (Rstudia, PyCharm, IntelliJ, Emacs, gedit, vi)


Provádění vědecké zpracování dat zahrnuje iterace v pořadí úloh:

1. Hledání, načítání a předem zpracování dat.
2. Vytváření a testování modely
3. Nasazování modelů pro používání v inteligentní aplikace

Datových vědců pomocí různých nástrojů k dokončení těchto úloh. Můžete být poměrně časově náročné najít odpovídající verze softwaru, a potom si chcete stáhnout, kompilace a nainstalovat tyto verze.

Virtuální počítač Linux datové vědy může podstatně usnadňují tato zatížení. Umožňuje vám rychle začít s prací projektu analytics. Umožňuje pracovat na úlohy v různých jazycích, včetně R, Python, SQL, Java a C++. Eclipse poskytuje rozhraní IDE pro vývoj a testování vaší kód, který se snadno používá. Azure SDK zahrnuté ve virtuálním počítači umožňuje sestavení aplikace pomocí různých služeb v systému Linux pro Cloudová platforma společnosti Microsoft. Kromě toho máte přístup do jiných jazyků jako Ruby, Perl, PHP nebo node.js, které jsou také předem nainstalované.

Neexistují žádné poplatky softwaru pro tuto bitovou kopii dat vědecké účely virtuálních počítačů. Platíte jenom využití poplatků Azure hardwaru, které jsou hodnotí na základě velikosti virtuálního počítače, který poskytnete image virtuálního počítače. Další informace o poplatky za výpočetní naleznete na [stránky Seznam virtuálních počítačů v Azure Marketplace](https://azure.microsoft.com/marketplace/partners/microsoft-ads/linux-data-science-vm/).

## <a name="other-versions-of-the-data-science-virtual-machine"></a>Jiné verze datové vědy virtuálního počítače
[Ubuntu](dsvm-ubuntu-intro.md) bitové kopie je také k dispozici s mnoha stejné nástroje jako obrázek CentOS plus přímým učení architektury. A [Windows](provision-vm.md) bitové kopie je také k dispozici.

## <a name="prerequisites"></a>Požadavky
Než bude možné vytvořit virtuální počítač s Linuxem dat vědecké účely, musíte mít následující:

* **Předplatné Azure**: ho získat, najdete v části [získání bezplatné zkušební verze Azure](https://azure.microsoft.com/free/).
* **Účet úložiště Azure**: Chcete-li vytvořit, přečtěte si téma [vytvořit účet úložiště Azure](../../storage/common/storage-create-storage-account.md#create-a-storage-account). Nebo pokud nechcete použít existující účet, lze vytvořit účet úložiště v rámci procesu vytvoření virtuálního počítače.

## <a name="create-your-linux-data-science-virtual-machine"></a>Vytvořit virtuální počítač Linux Data vědecké účely
Tady jsou kroky k vytvoření instance z datové vědy virtuální počítač Linux:

1. Přejděte k virtuálnímu počítači výpis na [portál Azure](https://portal.azure.com/#create/microsoft-ads.linux-data-science-vmlinuxdsvm).
2. Klikněte na tlačítko **vytvořit** (dole) se zprovoznit průvodce.![ Konfigurace data-vědecké účely vm](./media/linux-dsvm-intro/configure-linux-data-science-virtual-machine.png)
3. Následující části obsahují vstupy pro všechny kroky v průvodci (uvedené na pravé straně na předchozím obrázku) použít k vytvoření virtuálního počítače Microsoft Data vědecké účely. Zde jsou vstupy potřebná ke konfiguraci jednotlivých kroků:
   
   a. **Základy**:
   
   * **Název**: název vašeho serveru vědecké účely data vytváříte.
   * **Uživatelské jméno**: první účet přihlásit ID.
   * **Heslo**: první heslo účtu (veřejný klíč SSH můžete použít místo hesla).
   * **Předplatné**: Pokud máte více než jedno předplatné, vyberte ten, na kterém se tento počítač je vytvořen a účtují. Musíte mít oprávnění vytvoření prostředku pro toto předplatné.
   * **Skupina prostředků**: můžete vytvořit novou nebo použít stávající skupinu.
   * **Umístění**: Vyberte datové centrum, která je nejvhodnější. Obvykle je datové centrum, které má většina vašich dat nebo je nejblíže vašemu fyzické umístění pro nejrychlejší přístup k síti.
   
   b. **Velikost**:
   
   * Vyberte jeden z typů serveru, které splňuje požadavek na funkční a náklady na omezení. Vyberte **Zobrazit vše** zobrazte další možnosti, velikostí virtuálních počítačů.
   
   c. **Nastavení**:
   
   * **Typ disku**: Zvolte **Premium** Pokud dáváte přednost jednotku SSD (SSD). Jinak, vyberte **standardní**.
   * **Účet úložiště**: můžete vytvořit nový účet úložiště Azure v rámci vašeho předplatného, nebo použít stávající ve stejném umístění, která jste vybrali na **Základy** krok průvodce.
   * **Další parametry**: ve většině případů stačí použít výchozí hodnoty. Vzít v úvahu jiné než výchozí hodnoty, najeďte na informační odkaz Nápověda v konkrétních polí.
   
   d. **Souhrn**:
   
   * Ověřte, že všechny informace, které jste zadali správné.
   
   e. **Kupte si**:
   
   * Chcete-li začít, zřizování, klikněte na tlačítko **koupit**. Je k dispozici odkaz na podmínky transakce. Virtuální počítač nemá žádné další poplatky za výpočetní pro velikost serveru, který jste zvolili v **velikost** krok.

Zajišťování zabere asi 10-20 minut. Stav zřizování se zobrazí na portálu Azure.

## <a name="how-to-access-the-linux-data-science-virtual-machine"></a>Jak získat přístup k virtuální počítač Linux Data vědecké účely
Po vytvoření virtuálního počítače se můžete přihlásit se pomocí protokolu SSH. Použít pověření účtu, které jste vytvořili v **Základy** části kroku 3 pro rozhraní prostředí text. V systému Windows, si můžete stáhnout nástroj pro klienta na SSH jako [Putty](http://www.putty.org). Pokud dáváte přednost grafické desktop (systém Windows X), můžete použít X11 předávání na Putty nebo instalace klienta na X2Go.

> [!NOTE]
> Klient X2Go provést výrazně lepší, než X11 předávání v testování. Doporučujeme používat klienta X2Go pro grafické rozhraní plochy.
> 
> 

## <a name="installing-and-configuring-x2go-client"></a>Instalace a konfigurace klienta X2Go
Virtuální počítač s Linuxem již zřízená X2Go server a přijímá připojení klienta. Pro připojení k grafické ploše virtuálního počítače s Linuxem, udělejte na vašeho klienta:

1. Stáhněte a nainstalujte X2Go klienta pro vaši platformu klienta z [X2Go](http://wiki.x2go.org/doku.php/doc:installation:x2goclient).    
2. Spuštění klienta X2Go a vyberte **novou relaci**. Otevře okno Konfigurace s několik karet. Zadejte následující konfigurační parametry:
   * **Karta relace**:
     * **Hostitele**: název hostitele nebo IP adresy virtuálním počítačům s Linuxem dat vědecké účely.
     * **Přihlášení**: uživatelské jméno v virtuálního počítače s Linuxem.
     * **SSH Port**: necháte ve 22, výchozí hodnota.
     * **Typ relace**: Změňte hodnotu na XFCE. Virtuální počítač s Linuxem aktuálně podporuje jenom XFCE plochy.
   * **Karta média**: Pokud nemusíte používat zvukové podporu a klienta tisku, můžete je vypnout.
   * **Sdílené složky**: Pokud chcete adresáře z vaší klientské počítače na virtuální počítač s Linuxem připojeny, přidejte adresáře klientské počítače, které chcete sdílet s virtuálním Počítačem na této kartě.

Po přihlášení k virtuálnímu počítači pomocí SSH klienta nebo XFCE grafické plochy prostřednictvím klienta X2Go, jste připraveni začít používat nástroje, které jsou nainstalované a nakonfigurované ve virtuálním počítači. Na XFCE můžete zobrazit zástupce aplikace nabídky a ikony na ploše pro řadu nástrojů.

## <a name="tools-installed-on-the-linux-data-science-virtual-machine"></a>Nástroje nainstalované na datové vědě virtuální počítač Linux
### <a name="microsoft-r-server"></a>Microsoft R Server
R je jedním z Nejoblíbenější jazyky pro analýzu dat a strojové učení. Pokud chcete použít pro analytické údaje R, virtuální počítač má Microsoft R Server (Paní) s Microsoft R otevřete (MRO) a matematické jádra knihovny (MKL). MKL optimalizuje matematické operace v analytical algoritmy běžné. MRO je kompatibilní s CRAN r. 100 procent a některé z knihovny R publikované v CRAN lze nainstalovat MRO. PANÍ vám dává škálování a operationalization R modelů do webové služby. Můžete upravit programy R v jednom z výchozí editory, jako je Rstudia, vi, Emacs nebo gedit. Pokud používáte editoru Emacs, Všimněte si, že balíček Emacs ESS (mluví statistiky Emacs), který zjednodušuje práce se soubory R v editoru Emacs je už předem nainstalovaná.

Pro spuštění R konzole, stačí zadat **R** v prostředí. Tím přejdete na interaktivní prostředí. K vývoji vašeho programu R, obvykle pomocí editoru jako Emacs nebo vi nebo gedit a potom spuštěním skriptů v R. S Rstudia máte úplné grafické prostředí IDE vyvíjet váš R program.

Je také skript jazyka R pro instalaci [balíčky R 20 horní](http://www.kdnuggets.com/2015/06/top-20-r-packages.html) Chcete-li. Tento skript můžete spustit, jakmile se v rozhraní interaktivní R, kterou lze zadat (jak je uvedeno) tak, že zadáte **R** v prostředí.  

### <a name="python"></a>Python
Pro vývoj pomocí Python byl nainstalován distribuce Anaconda Python 2.7 a 3.5. Toto rozdělení obsahuje základní Python společně s přibližně 300 nejoblíbenější balíčků analytics matematické, technici a data. Můžete použít výchozí textové editory. Kromě toho můžete Spyder IDE Python, který je instalován s distribucí Anaconda Python. Spyder musí grafické plocha nebo X11 předávání. Zástupce Spyder je součástí grafické plochy.

Vzhledem k tomu, že máme Python 2.7 a 3.5, budete muset konkrétně aktivovat požadované verzi Pythonu (conda prostředí) chcete pracovat v aktuální relaci. Proces aktivace nastavuje proměnnou Cesta k požadované verzi Pythonu.

Pokud chcete aktivovat conda prostředí Python 2.7, spusťte následující příkaz z prostředí:

    source /anaconda/bin/activate root

Python 2.7 je nainstalována v */anaconda/bin*.

Pokud chcete aktivovat conda prostředí Python 3.5, spusťte následující příkazy z prostředí:

    source /anaconda/bin/activate py35


Python 3.5 je nainstalována v */anaconda/envs/py35/bin*.

K vyvolání interaktivní relace Python, stačí zadat **python** v prostředí. Pokud jsou na grafické rozhraní nebo mají X11 předávání sadu nahoru, můžete zadat **pycharm** spustit prostředí IDE PyCharm Python.

Pokud chcete nainstalovat další knihovny Python, budete muset spustit ```conda``` nebo ````pip```` příkazu v části sudo a zadejte úplnou cestu Správce balíčků Python (conda nebo pip), chcete-li nainstalovat správnou prostředí Python. Příklad:

    sudo /anaconda/bin/pip install <package> #pip for Python 2.7
    sudo /anaconda/envs/py35/bin/pip install <package> #pip for Python 3.5
    sudo /anaconda/bin/conda install [-n py27] <package> #conda for Python 2.7, default behavior
    sudo /anaconda/bin/conda install -n py35 <package> #conda for Python 3.5


### <a name="jupyter-notebook"></a>Poznámkový blok Jupyter
Distribuce Anaconda také obsahuje poznámkového bloku Jupyter, prostředí sdílení kódu a analýzy. Poznámkového bloku Jupyter přistupuje prostřednictvím JupyterHub. Přihlášení pomocí místní Linux uživatelské jméno a heslo.

Server poznámkového bloku Jupyter předem nakonfigurovaný s Python 2, Python 3 a R jádra. Není ikony na ploše s názvem "Poznámkový blok Jupyter" spustit prohlížeč pro přístup k serveru poznámkového bloku. Pokud jste na virtuální počítač prostřednictvím protokolu SSH nebo X2Go klienta, můžete také navštívit [ https://localhost:8000/ ](https://localhost:8000/) pro přístup k serveru poznámkového bloku Jupyter.

> [!NOTE]
> Pokračujte, pokud chcete získat všechna upozornění certifikátu.
> 
> 

Server poznámkového bloku Jupyter můžete přistupovat z libovolného hostitele. Stačí zadat *https://\<název DNS virtuálního počítače nebo IP adresu\>: 8000 /*

> [!NOTE]
> Port 8000 je otevřen v bráně firewall ve výchozím nastavení při zřízení virtuálního počítače.
> 
> 

Budeme mít zabalené poznámkových bloků ukázka – jednu v Pythonu a jeden v jazyce R. Zobrazí se odkaz na ukázky na domovské stránce Poznámkový blok po ověření do poznámkového bloku Jupyter s použitím místní Linux uživatelské jméno a heslo. Můžete vytvořit nový poznámkový blok výběrem **nový**a pak jádra příslušný jazyk. Pokud nevidíte **nový** tlačítko, klikněte na tlačítko **Jupyter** ikona nahoře vlevo přejít na domovskou stránku serveru poznámkového bloku.

### <a name="apache-spark-standalone"></a>Apache Spark samostatné 
Samostatnou instanci systému Apache Spark je předinstalován v DSVM Linux při vývoji aplikací Spark místně nejprve před testování a nasazení na velkých clusterech. PySpark programy můžete spustit pomocí Jupyter jádra. Když otevřete Jupyter a klikněte na tlačítko **nový** tlačítko by měl zobrazit seznam dostupných jádra. "– Python Spark" je jádra PySpark, který umožňuje vytvářet Spark aplikací pomocí jazyka Python. Můžete taky Python IDE jako PyCharm nebo Spyder k sestavení budete programu Spark. Od to je samostatné instance, Spark zásobníku běží v rámci programu volajícího klienta. Díky tomu je rychlejší a snazší řešení problémů ve srovnání s vývojem na clusteru Spark. 

Ukázkový PySpark Poznámkový blok je k dispozici na Jupyter, které můžete najít v adresáři "SparkML" v části domovský adresář Jupyter ($ DOMOVSKÉ/poznámkových bloků/SparkML/pySpark). 

Pokud programujete v R pro Spark, můžete použít Microsoft R Server SparkR nebo sparklyr. 

Před spuštěním v kontextu Spark Microsoft R Server, budete muset jednou instalační program tak, aby Povolit místní Hadoop HDFS a Yarn instance jednoho uzlu. Ve výchozím nastavení jsou služby Hadoop nainstalován, ale na DSVM zakázáno. Chcete-li ji povolit, budete muset spustit následující příkazy jako kořenová při prvním:

    echo -e 'y\n' | ssh-keygen -t rsa -P '' -f ~hadoop/.ssh/id_rsa
    cat ~hadoop/.ssh/id_rsa.pub >> ~hadoop/.ssh/authorized_keys
    chmod 0600 ~hadoop/.ssh/authorized_keys
    chown hadoop:hadoop ~hadoop/.ssh/id_rsa
    chown hadoop:hadoop ~hadoop/.ssh/id_rsa.pub
    chown hadoop:hadoop ~hadoop/.ssh/authorized_keys
    systemctl start hadoop-namenode hadoop-datanode hadoop-yarn

Můžete zastavit Hadoop související služby, když je nepotřebujete spuštěním ````systemctl stop hadoop-namenode hadoop-datanode hadoop-yarn```` vzorku, který ukazuje, jak pro vývoj a testování paní v vzdálené kontextu Spark (což je samostatnou instanci Spark na DSVM) je zadaná a k dispozici v `/dsvm/samples/MRS` adresáře. 

### <a name="ides-and-editors"></a>Editory a integrovaného vývojového prostředí
Musíte vybrat několik editory kódu. To zahrnuje vi/VIM, Emacs, gEdit, PyCharm, Rstudia, Eclipse a IntelliJ. gEdit Eclipse, IntelliJ, Rstudia a PyCharm jsou grafické editory a třeba, abyste být přihlášeni na grafické plochy jejich použití. Tyto editory obsahují desktopových a aplikačních nabídky zástupců spusťte je.

**VIM** a **Emacs** jsou textové editory. Do Emacs jsme nainstalovali balíček rozšíření názvem Emacs mluví statistiky (ESS), který usnadňuje práci s R v editoru Emacs. Další informace najdete na [ESS](http://ess.r-project.org/).

**Eclipse** je otevřenou zdroje, extensible IDE, která podporuje několik jazyků. Edice vývojáře Java je instance nainstalovaná ve virtuálním počítači. Nejsou k dispozici pro několik oblíbených jazyků, které je možné nainstalovat rozšíření prostředí modulů plug-in. Máme také o modul plug-in nainstalované v prostředí Eclipse názvem **nástrojů Azure pro Eclipse**. Umožňuje vytvořit, vývoj, testování a nasazení aplikace na platformě Azure pomocí vývojové prostředí Eclipse, který podporuje jazyky jako Java. K dispozici je také **Azure SDK pro jazyk Java** umožňuje přístup k jiné služeb Azure z prostředí Java. Další informace o Azure toolkit pro Eclipse naleznete na [nástrojů Azure pro Eclipse](../../azure-toolkit-for-eclipse.md).

**Latexu** je nainstalován pomocí balíčku texlive spolu s doplňkem Emacs [auctex](https://www.gnu.org/software/auctex/manual/auctex/auctex.html) balíček, který zjednodušuje vytváření latexu dokumentů v rámci Emacs.  

### <a name="databases"></a>Databáze
#### <a name="postgres"></a>Postgres
Databáze s otevřeným zdrojem **Postgres** je k dispozici na virtuální počítač s běžící a initdb již byla dokončena. Stále potřebujete k vytváření databází a uživatelů. Další informace najdete v tématu [Postgres dokumentaci](https://www.postgresql.org/docs/).  

#### <a name="graphical-sql-client"></a>Grafický klient SQL
**SQuirrel SQL**, grafický klient SQL, bylo zadáno pro připojení do různých databází (například Microsoft SQL Server, Postgres a MySQL) a ke spouštění dotazů SQL. Když spustíte z grafického relace plochy (například pomocí X2Go klienta). K vyvolání SQuirrel SQL, můžete spustit pomocí ikony na ploše nebo spuštěním následujícího příkazu v prostředí.

    /usr/local/squirrel-sql-3.7/squirrel-sql.sh

Před prvním použitím nastavení ovladače a aliasy databáze. Ovladače JDBC jsou umístěné na adrese:

*/usr/share/java/jdbcdrivers*

Další informace najdete v tématu [SQuirrel SQL](http://squirrel-sql.sourceforge.net/index.php?page=screenshots).

#### <a name="command-line-tools-for-accessing-microsoft-sql-server"></a>Nástroje příkazového řádku pro přístup k systému Microsoft SQL Server
Balíček ovladače ODBC pro SQL Server také obsahuje dva nástroje příkazového řádku:

**BCP**: hromadné nástroj bcp zkopíruje data mezi instance systému Microsoft SQL Server a datový soubor ve formátu zadaného uživatelem. Nástroj bcp slouží k importu velký počet nových řádků do tabulky serveru SQL Server nebo k exportu dat z tabulky do datových souborů. K importu dat do tabulky, musíte použít formát soubor vytvořený pro tuto tabulku nebo pochopit strukturu tabulky a typy dat, které jsou platné pro její sloupce.

Další informace najdete v tématu [připojení pomocí bcp](https://msdn.microsoft.com/library/hh568446.aspx).

**SqlCmd**: můžete zadat příkazy jazyka Transact-SQL s Nástroj sqlcmd také s postupy systému a skript soubory na příkazovém řádku. Tento nástroj používá rozhraní ODBC pro spuštění dávky Transact-SQL.

Další informace najdete v tématu [připojení pomocí sqlcmd](https://msdn.microsoft.com/library/hh568447.aspx).

> [!NOTE]
> Existují určité rozdíly v tento nástroj mezi platformy Linux a Windows. Najdete v dokumentaci podrobnosti.
> 
> 

#### <a name="database-access-libraries"></a>Databáze access knihovny
Nejsou k dispozici v R a Python pro přístup k databázím knihovny.

* V R **RODBC** balíček nebo **dplyr** balíček umožňuje dotazovat nebo spouštění příkazů jazyka SQL na serveru databáze.
* V Python **pyodbc** knihovna poskytuje přístup k databázi s rozhraním ODBC jako základní vrstvě.  

Pro přístup k **Postgres**:

* Z R: Pomocí balíčku **RPostgreSQL**.
* Z Pythonu: Použití **psycopg2** knihovny.

### <a name="azure-tools"></a>Nástroje Azure
Následující nástroje Azure jsou nainstalovány ve virtuálním počítači:

* **Rozhraní příkazového řádku Azure**: rozhraní příkazového řádku Azure umožňuje vytvářet a spravovat prostředky Azure pomocí příkazů prostředí. K vyvolání nástroje Azure, stačí zadat **azure nápovědy**. Další informace najdete v tématu [stránce dokumentace Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-az-cli2).
* **Microsoft Azure Storage Explorer**: Microsoft Azure Storage Explorer je grafický nástroj, který se používá k procházení objekty, které jsou uloženy ve vašem účtu úložiště Azure a k nahrávání a stahování dat do a z Azure BLOB. Storage Explorer můžete přistupovat z ikonu zástupce na ploše. Můžete ji použít z řádku prostředí zadáním **StorageExplorer**. Musíte být přihlášeni z X2Go klienta, nebo můžete nechat X11 předávání sadu nahoru.
* **Knihovny Azure**: Toto jsou některé předem nainstalovaná knihoven.
  
  * **Python**: související s Azure knihovny v Pythonu, zda jsou nainstalovány jsou **azure**, **azureml**, **pydocumentdb v tuto**, a **pyodbc**. První tři knihovny můžete získat přístup k služby Azure storage, Azure Machine Learning a Azure DB Cosmos (databáze NoSQL v Azure). Čtvrtý knihovny pyodbc (spolu s ovladač Microsoft ODBC pro SQL Server), umožňuje přístup k systému SQL Server, databáze SQL Azure a Azure SQL Data Warehouse z Pythonu pomocí rozhraní ODBC. Zadejte **pip seznamu** zobrazíte všechny uvedené knihovny. Ujistěte se, že tento příkaz spustit v prostředí 3.5 i Python 2.7.
  * **R**: knihovny související s Azure, v R, které jsou nainstalovány jsou **AzureML** a **RODBC**.
  * **Java**: seznam knihoven Azure Java lze nalézt v adresáři **/dsvm/sdk/AzureSDKJava** ve virtuálním počítači. Klíče knihovny jsou Azure úložiště a správu rozhraní API, databáze Cosmos Azure a JDBC ovladače systému SQL Server.  

Dostanete [portál Azure](https://portal.azure.com) z předem nainstalovaná prohlížeče Firefox. Na portálu Azure můžete vytvářet, spravovat a monitorovat prostředků Azure.

### <a name="azure-machine-learning"></a>Azure Machine Learning
Azure Machine Learning je plně spravovaná Cloudová služba, která umožňuje vytvářet, nasazovat a sdílet řešení prediktivní analýzy. Sestavit experimentů a modely z Azure Machine Learning Studio. Byla přístupná z webového prohlížeče na virtuálním počítači datové vědy navštivte stránky [Microsoft Azure Machine Learning](https://studio.azureml.net).

Po přihlášení k Azure Machine Learning Studio, máte přístup experimentování plátno kde můžete vytvořit logický tok pro algoritmy strojového učení. Také mají přístup do poznámkového bloku Jupyter hostované v Azure Machine Learning a může bezproblémově pracovat experimentů v nástroji Machine Learning Studio. Zprovoznit strojového učení modely, které jste vytvořili pomocí zabalení je v rozhraní webových služeb. To umožňuje klientům napsané v libovolném jazyce vyvolání predikcím ze strojového učení modelů. Další informace najdete v tématu [Machine Learning dokumentaci](https://azure.microsoft.com/documentation/services/machine-learning/).

Můžete také vytvořit modely v R nebo Python ve virtuálním počítači a poté ji nasadit v provozním prostředí v Azure Machine Learning. Jsme nainstalovali knihovny v R (**AzureML**) a Python (**azureml**) Chcete-li povolit tuto funkci.

Informace o tom, jak nasadit do Azure Machine Learning modely v R a Pythonu najdete v tématu [deset způsobů, jak na vědecké zpracování dat virtuálního počítače](vm-do-ten-things.md) (zejména v části "vytvoření modelů pomocí R nebo Python a zprovoznit je pomocí Azure Machine Learning").

> [!NOTE]
> Tyto pokyny byly napsány pro verzi systému Windows virtuálního počítače vědecké účely Data. Ale k dispozici informace o nasazení modely na Azure Machine Learning je použitelná pro virtuální počítač s Linuxem.
> 
> 

### <a name="machine-learning-tools"></a>Nástroje Machine learning
Virtuální počítač obsahuje několik strojového učení nástroje a algoritmy, které byly předem zkompilovat a předem nainstalovány místně. Mezi ně patří:

* **Sada nástrojů pro kognitivní** : hluboká učení toolkit.
* **K dispozici Vowpal**: algoritmus rychlého online učení.
* **xgboost**: nástroj, který poskytuje optimalizovanou, boosted stromu algoritmy.
* **Python**: Anaconda Python se dodává s algoritmy strojového učení s knihovnami jako další Scikit připojené. Další knihovny můžete nainstalovat pomocí `pip install` příkaz.
* **R**: knihovnu bohaté funkce machine learning je k dispozici pro R. Některé z knihoven, které jsou předem nainstalovaná jsou lm, glm, randomForest, rpart. Další knihovny můžete nainstalovat spuštěním:
  
        install.packages(<lib name>)

Zde jsou některé další informace o nástroje první tři strojové učení v seznamu.

#### <a name="microsoft-cognitive-toolkit"></a>Microsoft Cognitive Toolkit
Toto je typu open source hloubkové učení toolkit. Je nástroj příkazového řádku (cntk) a je již v CESTĚ.

Chcete-li základní ukázku spustit, spusťte následující příkazy v prostředí:

    cd /home/[USERNAME]/notebooks/CNTK/HelloWorld-LogisticRegression
    cntk configFile=lr_bs.cntk makeMode=false command=Train

Další informace najdete v části CNTK [Githubu](https://github.com/Microsoft/CNTK)a [CNTK wiki](https://github.com/Microsoft/CNTK/wiki).

#### <a name="vowpal-wabbit"></a>K dispozici Vowpal
K dispozici Vowpal je machine learning systému, který používá techniky, jako je online a hash, allreduce, snížení, learning2search, aktivní a interaktivní učení.

Chcete-li spustit nástroj na základních příkladů, postupujte takto:

    cp -r /dsvm/tools/VowpalWabbit/demo vwdemo
    cd vwdemo
    vw house_dataset

Existují další, větší ukázky v tomto adresáři. Další informace o zobrazit najdete v tématu [v této části GitHub](https://github.com/JohnLangford/vowpal_wabbit)a [Vowpal k dispozici wiki](https://github.com/JohnLangford/vowpal_wabbit/wiki).

#### <a name="xgboost"></a>xgboost
Toto je do knihovny, která je navržena a optimalizována pro algoritmy boosted (stromu). Cílem této knihovny je k výpočtu omezení počítače krajních potřebných k poskytování rozsáhlé stromové struktury zvyšovat skóre, který je škálovatelný, přenosných a přesné.

Je poskytována jako příkazového řádku a také R knihovny.

Použití této knihovny v R, můžete spustit relaci interaktivní R (tak, že zadáte **R** v prostředí) a načíst knihovnu.

Zde je jednoduchý příklad, který můžete spustit v R řádku:

    library(xgboost)

    data(agaricus.train, package='xgboost')
    data(agaricus.test, package='xgboost')
    train <- agaricus.train
    test <- agaricus.test
    bst <- xgboost(data = train$data, label = train$label, max.depth = 2,
                    eta = 1, nthread = 2, nround = 2, objective = "binary:logistic")
    pred <- predict(bst, test$data)

Spusťte příkazový řádek xgboost, zde jsou příkazů pro spuštění v prostředí:

    cp -r /dsvm/tools/xgboost/demo/binary_classification/ xgboostdemo
    cd xgboostdemo
    xgboost mushroom.conf


.Model – soubor se zapíše na zadaný adresář. Informace o tomto příkladu ukázku najdete [na Githubu](https://github.com/dmlc/xgboost/tree/master/demo/binary_classification).

Další informace o xgboost najdete v tématu [stránky dokumentace, která xgboost](https://xgboost.readthedocs.org/en/latest/)a jeho [úložiště GitHub](https://github.com/dmlc/xgboost).

#### <a name="rattle"></a>Rattle
Rattle ( **R** **A**nalytical **T**ukopis **T**o **L**vám **E**asily) používá zkoumání dat založených na grafickém uživatelském rozhraní a modelování. Představuje statistické a vizuální souhrnů dat, transformace dat, která lze snadno modelovat, sestavení dozorovaných i u nedozorovaných modely z dat, uvede výkon modely graficky, a nastaví skóre nová data. Rovněž vygeneruje kód R, replikace operace v uživatelském rozhraní, které můžete spustit přímo v R nebo používají jako výchozí bod pro další analýzu.

Pokud chcete spustit Rattle, musíte být v grafické přihlášení relaci plochy. V terminálu, zadejte ```R``` k zadání prostředí R. Do příkazového řádku R zadejte následující příkazy:

    library(rattle)
    rattle()

Nyní grafické rozhraní otevře sadu karet. Tady jsou kroky pro rychlé spuštění v Rattle potřebné k použití ukázkové počasí datové sady a vytvoření modelu. V některé z následujících kroků zobrazí se výzva k automatické instalaci a spouštění některé požadované balíčky R, které ještě nejsou v systému.

> [!NOTE]
> Pokud nemáte přístup k instalaci balíčku v adresáři systému (výchozí), může zobrazit výzva na vaše R okna konzoly nainstalovat balíčky do své osobní knihovny. Odpověď *y* Pokud se zobrazí tyto výzvy.
> 
> 

1. Klikněte na tlačítko **Spustit**.
2. Zobrazí se dialogové okno se zobrazí, s dotazem, pokud chcete použít příklad počasí datové sady. Klikněte na tlačítko **Ano** načíst v příkladu.
3. Klikněte **modelu** kartě.
4. Klikněte na tlačítko **Execute** k sestavení rozhodovací strom.
5. Klikněte na tlačítko **kreslení** zobrazíte rozhodovací strom.
6. Klikněte na tlačítko **doménové struktury** přepínač a klikněte na tlačítko **Execute** vytvářet náhodné doménové struktury.
7. Klikněte **Evaluate** kartě.
8. Klikněte na tlačítko **riziko** přepínač a klikněte na tlačítko **Execute** zobrazíte dva pozemků výkonu riziko (kumulativní).
9. Klikněte **protokolu** zobrazte kód generování R pro předchozí operace.
   (Z důvodu chyby v aktuální verzi Rattle, je třeba vložit *#* znak před *exportovat tento protokol...*  v textu protokolu.)
10. Klikněte **exportovat** tlačítko pro uložení souboru skriptu R s názvem *weather_script. R* do domovské složky.

Můžete ukončit Rattle a R. Nyní můžete upravit generovaného skriptu R, nebo ho použít, protože to je vše, co bylo provedeno v rámci rozhraní Rattle opakování ji kdykoli spustit. Zejména pro začátečníky v R je to snadný způsob, jak rychle provést analýzy a strojového učení v jednoduchého grafického rozhraní, při automatické generování kódu v R upravit nebo Další informace.

## <a name="next-steps"></a>Další postup
Zde je, jak můžete dál učení a zkoumání:

* [Vědecké zpracování dat na datové vědě virtuální počítač Linux](linux-dsvm-walkthrough.md) návodu se dozvíte, jak provést několik běžné úkoly vědecké účely dat datové vědy virtuálního počítače s Linuxem zřízený sem. 
* Prozkoumejte různé vědě nástrojů data na vědecké zpracování dat virtuálního počítače tak, že zkusíte se nástrojů popsaných v tomto článku. Můžete také spouštět *dsvm. Další informace* na prostředí v rámci virtuálního počítače pro základní informace a odkazy na další informace o nástrojích nainstalovaný na Virtuálním počítači.  
* Naučte se vytvářet analytická řešení začátku do konce systematičtěji pomocí [proces vědecké účely dat Team](https://azure.microsoft.com/documentation/learning-paths/cortana-analytics-process/).
* Přejděte [Cortana Analytics Gallery](http://gallery.cortanaanalytics.com) pro počítač učení a analýzy vzorků, které používají sadu Analytics Cortana.

