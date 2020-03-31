---
title: 'Reference: CentOS Data Science Virtuální počítač'
titleSuffix: Azure Data Science Virtual Machine
description: Podrobnosti o nástrojích zahrnutých ve virtuálním počítači CentOS Data Science
author: gvashishtha
ms.service: machine-learning
ms.subservice: data-science-vm
ms.author: gopalv
ms.date: 09/11/2019
ms.topic: reference
ms.openlocfilehash: db49a9f5e0e6675d93cb58d6af9c92fac21e8b74
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77525834"
---
# <a name="reference-centos-linux-data-science-virtual-machine"></a>Reference: Centos (Linux) Data Science Virtuální stroj

Linux Data Science Virtual Machine (DSVM) je virtuální počítač Azure založený na CentOS. Linux DSVM je dodáván s kolekcí předinstalovaných nástrojů, které můžete použít pro analýzu dat a strojové učení. 

Klíčové softwarové komponenty obsažené v LinuxU DSVM jsou:

* Linux CentOS distribuční operační systém.
* Microsoft Machine Learning Server.
* Distribuce Anaconda Python (verze 3.5 a 2.7), včetně populárních knihoven pro analýzu dat.
* JuliaPro, kurátorská distribuce jazyka Julia a populárních vědeckých knihoven a knihoven pro analýzu dat.
* Spark Samostatná instance a jednouzelHadoop (HDFS, YARN).
* JupyterHub, víceuživatelský notebookOvý server Jupyter, který podporuje jádra R, Python, PySpark a Julia.
* Průzkumník úložišť Azure.
* Azure CLI, rozhraní příkazového řádku Azure pro správu prostředků Azure.
* Databáze PostgresSQL.
* Nástroje strojového učení:
  * [Microsoft Cognitive Toolkit](https://github.com/Microsoft/CNTK) (CNTK), sada nástrojů pro software pro hluboké učení od společnosti Microsoft Research.
  * [Vowpal Wabbit](https://github.com/JohnLangford/vowpal_wabbit), rychlý systém strojového učení, který podporuje techniky, jako je on-line, hašování, allreduce, redukce, learning2search, aktivní a interaktivní učení.
  * [XGBoost](https://xgboost.readthedocs.org/en/latest/), nástroj, který poskytuje rychlé a přesné posílené implementace stromu.
  * [Rattle](https://togaware.com/rattle/), nástroj, který umožňuje začít s analýzou dat a strojovým učením v R snadné. Rattle nabízí jak průzkum dat založený na grafickém uživatelském rozhraní, tak modelování pomocí automatického generování kódu R.
* Azure SDK v jazyce Java, Pythonu, Node.js, Ruby a PHP.
* Knihovny v R a Pythonu pro použití v Azure Machine Learning a dalších službách Azure.
* Vývojové nástroje a editory (RStudio, PyCharm, IntelliJ, Emacs, gedit, vi).

Datová věda zahrnuje iterace na posloupnost úkolů:

1. Vyhledání, načtení a předběžné zpracování dat.
1. Vytvářejte a testujte modely.
1. Nasaďte modely pro spotřebu v inteligentních aplikacích.

K dokončení těchto úkolů používají datoví vědci různé nástroje. Může být časově náročné najít správné verze softwaru a poté stáhnout, zkompilovat a nainstalovat software.

Linux DSVM může tuto zátěž podstatně zmírnit. Pomocí linuxového DSVM můžete nastartovat svůj analytický projekt. Linux DSVM vám pomůže pracovat na úkolech v různých jazycích, včetně R, Python, SQL, Java a C++. Eclipse poskytuje snadno použitelné ide pro vývoj a testování kódu. Sada Azure SDK, která je součástí dsvm, vám pomůže vytvářet aplikace pomocí různých služeb v Linuxu pro cloudovou platformu Microsoftu. Další jazyky jsou předinstalovány, včetně Ruby, Perl, PHP a Node.js.

U image DSVM se neúčtují žádné poplatky za software. Platíte jenom poplatky za využití hardwaru Azure, které se vyhodnocují na základě velikosti virtuálního počítače, který zřídíte pomocí bitové kopie DSVM. Další informace o výpočetních poplatcích najdete v [seznamu virtuálního počítače pro virtuální počítače pro linuxovou (CentOS) datové vědy na](https://azure.microsoft.com/marketplace/partners/microsoft-ads/linux-data-science-vm/) Azure Marketplace.


## <a name="machine-learning-server"></a>Server strojového učení

R je jedním z nejoblíbenějších jazyků pro analýzu dat a strojové učení. Pokud chcete použít R pro analýzy, DSVM má Machine Learning Server s Microsoft R Open a Math Kernel Library. Knihovna matematického jádra optimalizuje běžné matematické operace v analytických algoritmech. R Open je plně kompatibilní s CRAN R. Všechny knihovny R publikované v CRAN lze nainstalovat na R Open. 

Machine Learning Server můžete použít k škálování a zprovoznění modelů R do webových služeb. Programy R můžete upravovat v jednom z výchozích editorů, jako je RStudio, vi nebo Emacs. Editor Emacs je předinstalován na DSVM. Balíček Emacs ESS (Emacs Speaks Statistics) zjednodušuje práci se soubory R v editoru Emacs.

Chcete-li otevřít konzolu R, zadejte do skořepiny **hodnotu R**. Tento příkaz vás přenese do interaktivního prostředí. Chcete-li rozvíjet program R, obvykle používáte editor jako Emacs nebo vi a pak spusťte skripty v R. RStudio nabízí úplné grafické IDE pro vývoj r programu.

Skript R, který můžete použít k instalaci [prvních 20 R balíčků](https://www.kdnuggets.com/2015/06/top-20-r-packages.html) je součástí DSVM. Tento skript můžete spustit, když jste v interaktivním rozhraní R. Jak již bylo zmíněno dříve, chcete-li otevřít toto rozhraní, ve skořápce zadejte **R**.  

## <a name="python"></a>Python

Anaconda Python je nainstalován v prostředích Python3.5 a 2.7. Prostředí 2.7 se nazývá _root_ a prostředí 3.5 se nazývá _py35_. Tato distribuce obsahuje základní Python spolu s asi 300 nejoblíbenějších matematických, inženýrských a datových analytických balíčků.

Prostředí py35 je výchozí. Chcete-li aktivovat kořenové prostředí (2.7), použijte tento příkaz:

```bash
source activate root
```

Chcete-li znovu aktivovat prostředí py35, použijte tento příkaz:

```bash
source activate py35
```

Chcete-li vyvolat interaktivní relaci Pythonu, zadejte do prostředí **python**. 

Nainstalujte další knihovny Pythonu pomocí Conda nebo pip. Pro pip nejprve aktivujte správné prostředí, pokud nechcete výchozí:

```bash
source activate root
pip install <package>
```

Nebo zadejte úplnou cestu k pipu:

```bash
/anaconda/bin/pip install <package>
```

Pro Conda byste měli vždy zadat název prostředí (py35 nebo root):

```bash
conda install <package> -n py35
```

Pokud jste na grafickém rozhraní nebo máte x11 přesměrování nastavit, můžete zadat **pycharm** otevřít PyCharm Python IDE. Můžete použít výchozí textové editory. Kromě toho můžete použít Spyder, Python IDE, který je dodáván s distribucemi Anaconda Python. Spyder potřebuje grafický desktop nebo X11 forwarding. Grafická plocha má zástupce Spyder.

## <a name="jupyter-notebook"></a>Poznámkový blok Jupyter

Distribuce Anaconda je také dodávána s Jupyter Notebook, prostředí pro sdílení kódu a analýzy. Přístup k Jupyter notebookpřes JupyterHub. Přihlašujete se pomocí místního uživatelského jména a hesla pro Linux.

Jupyter notebook server je předkonfigurován s Python 2, Python 3 a R jádra. Pomocí ikony **jupyterového notebooku** otevřete prohlížeč a získejte přístup k serveru Jupyter Notebook. Pokud přistupujete k DSVM přes SSH nebo X2Go klienta, můžete\/také přistupovat k serveru Jupyter Notebook na adrese https: /localhost:8000/.

> [!NOTE]
> Pokud se objeví upozornění na certifikát, pokračujte.

K notebookovému serveru Jupyter můžete přistupovat z libovolného hostitele. Zadejte **\//\<https: DSVM DNS\>jméno nebo IP adresu :8000/**.

> [!NOTE]
> Port 8000 je otevřen v bráně firewall ve výchozím nastavení při zřízení DSVM. 

Společnost Microsoft má zabalené ukázkové poznámkové bloky, jeden v Pythonu a jeden v R. Odkaz na ukázky na domovské stránce jupyterského poznámkového bloku můžete vidět po ověření poznámkového bloku Jupyter pomocí místního uživatelského jména a hesla pro Linux. Chcete-li vytvořit nový poznámkový blok, vyberte **Nový**a pak vyberte jazorné jádro, které chcete použít. Pokud tlačítko **Nový** nevidíte, v levém horním rohu vyberte ikonu **Jupyter** a přejděte na domovskou stránku notebooku.

## <a name="spark-standalone"></a>Jiskra samostatná 

Instance samostatného režimu Spark je předinstalována na LinuxU DSVM, která vám pomůže vyvíjet aplikace Spark místně před testováním a nasazením na velkých clusterech. 

Programy PySpark můžete spouštět prostřednictvím jádra Jupyter. Když otevřete Jupyter, vyberte tlačítko **Nový** a měli byste vidět seznam dostupných jader. **Spark - Python** je jádro PySpark, které umožňuje vytvářet aplikace Spark pomocí jazyka Python. Můžete také použít Python IDE jako PyCharm nebo Spyder k vytvoření programu Spark. 

V této samostatné instanci zásobníku Spark běží v volajícím klientském programu. Tato funkce umožňuje rychlejší a snadnější řešení problémů ve srovnání s vývojem v clusteru Spark.

Jupyter poskytuje ukázkový notebook PySpark. Najdete ji v adresáři SparkML pod domovskou adresáří Jupyter ($HOME/notebooks/SparkML/pySpark). 

Pokud programujete v R pro Spark, můžete použít Machine Learning Server, SparkR nebo sparklyr. 

Před spuštěním v kontextu Spark v Machine Learning Server, musíte provést jednorázový krok nastavení povolit místní jednonohý Hadoop HDFS a YARN instance. Ve výchozím nastavení jsou služby Hadoop nainstalovány, ale zakázány v dsvm. Chcete-li povolit služby Hadoop, spusťte poprvé jako kořen následující příkazy:

```bash
echo -e 'y\n' | ssh-keygen -t rsa -P '' -f ~hadoop/.ssh/id_rsa
cat ~hadoop/.ssh/id_rsa.pub >> ~hadoop/.ssh/authorized_keys
chmod 0600 ~hadoop/.ssh/authorized_keys
chown hadoop:hadoop ~hadoop/.ssh/id_rsa
chown hadoop:hadoop ~hadoop/.ssh/id_rsa.pub
chown hadoop:hadoop ~hadoop/.ssh/authorized_keys
systemctl start hadoop-namenode hadoop-datanode hadoop-yarn
```

Služby související s Hadoopem můžete zastavit, když `systemctl stop hadoop-namenode hadoop-datanode hadoop-yarn`je nepotřebujete, spuštěním .

Adresář /dsvm/samples/MRS poskytuje ukázku, která ukazuje, jak vyvíjet a testovat server Strojového učení ve vzdáleném kontextu Spark (samostatná instance Spark na DSVM).

## <a name="ides-and-editors"></a>IDC a redaktoři

Můžete si vybrat z několika editorů kódu, včetně vi/VIM, Emacs, gedit, PyCharm, RStudio, Eclipse, LaTeX a IntelliJ. 

* gedit, Eclipse, IntelliJ, R Studio a PyCharm jsou grafické editory. Chcete-li je používat, musíte být přihlášeni k grafické ploše. Otevřete je pomocí zástupců nabídek plochy a aplikace.

* Vim a Emacs jsou textové editory. Na Emacsu usnadňuje doplňkový balíček ESS práci s R v editoru Emacs. Více informací naleznete na [internetových stránkách ESS](https://ess.r-project.org/).

* Eclipse je open source, rozšiřitelné IDE, které podporuje více jazyků. Eclipse IDE pro Java Developers je verze nainstalovaná na DSVM. Můžete nainstalovat moduly plug-in pro několik oblíbených jazyků pro rozšíření prostředí. 

  Modul plug-in Azure Toolkit for Eclipse je nainstalovaný s eclipse na DSVM. Sadu nástrojů Azure Toolkit for Eclipse můžete použít k vytváření, vývoji, testování a nasazování aplikací Azure pomocí vývojového prostředí Eclipse, které podporuje jazyky, jako je Java.

  Sada Azure SDK pro Jazyk Java je taky nainstalovaná s Sadou nástrojů Azure pro Eclipse na DSVM. Sada Azure SDK pro Jazyk Java umožňuje přístup k různým službám Azure z prostředí Java. 
  
  Další informace najdete v tématu [Azure Toolkit for Eclipse](/java/azure/eclipse/azure-toolkit-for-eclipse).

* LaTeX je nainstalován prostřednictvím texlive balíčku, spolu s emacs add-on balíček s názvem [AUCTeX](https://www.gnu.org/software/auctex/manual/auctex/auctex.html). Tento balíček zjednodušuje vytváření dokumentů LaTeXu v Emacsu. 

## <a name="databases"></a>Databáze

Linux DSVM vám poskytuje přístup k několika databázovým nástrojům a nástrojům příkazového řádku.

### <a name="postgressql"></a>PostgresSQL

Open source databáze PostgresSQL je k dispozici na DSVM, se službami spuštěnými a initdb dokončena. Je nutné vytvořit databáze a uživatele. Další informace naleznete v [dokumentaci PostgresSQL](https://www.postgresql.org/docs/).  

### <a name="squirrel-sql"></a>SQuirreL SQL

SQuirreL SQL je grafický SQL klient, který se může připojit k různým databázím (včetně SQL Serveru, PostgresSQL a MySQL) a spouštět SQL dotazy. SquirreL SQL můžete spustit z grafické relace plochy (například prostřednictvím klienta X2Go) pomocí ikony na ploše. Nebo můžete spustit klienta pomocí následujícího příkazu v prostředí:

```bash
/usr/local/squirrel-sql-3.7/squirrel-sql.sh /usr/local/squirrel-sql-3.7/squirrel-sql.sh
```

Před prvním použitím nastavte ovladače a aliasy databáze. Ovladače JDBC jsou umístěny na adrese /usr/share/java/jdbcdrivers.

Další informace naleznete [v tématu SQuirreL SQL](http://squirrel-sql.sourceforge.net/index.php?page=screenshots).

### <a name="command-line-tools-for-accessing-sql-server"></a>Nástroje příkazového řádku pro přístup k serveru SQL Server

Balíček ovladačů ODBC pro SQL Server je také dodáván se dvěma nástroji příkazového řádku:

* **bcp**: Nástroj bcp hromadně kopíruje data mezi instancí serveru SQL Server a datovým souborem v uživatelem určeném formátu. Pomocí nástroje bcp můžete importovat velký počet nových řádků do tabulek serveru SQL Server nebo exportovat data z tabulek do datových souborů. Chcete-li importovat data do tabulky, musíte použít formátovaný soubor vytvořený pro tuto tabulku. Nebo je nutné pochopit strukturu tabulky a typy dat, které jsou platné pro její sloupce.

  Další informace naleznete v tématu [Připojení pomocí služby bcp](https://msdn.microsoft.com/library/hh568446.aspx).

* **sqlcmd**: Pomocí nástroje sqlcmd můžete na příkazovém řádku zadat příkazy Transact-SQL, systémové postupy a soubory skriptů. Nástroj sqlcmd používá rozhraní ODBC ke spuštění dávek Transact-SQL.

  Další informace naleznete v [tématu Připojení pomocí sqlcmd](https://msdn.microsoft.com/library/hh568447.aspx).

  > [!NOTE]
  > Existují určité rozdíly v tomto nástroji mezi platformami Linux a Windows. Podrobnosti naleznete v dokumentaci.

### <a name="database-access-libraries"></a>Knihovny přístupu k databázi

Knihovny pro přístup k databázi jsou k dispozici v R a Pythonu:

* V r, můžete použít balíček RODBC nebo dplyr balíček dotazovat nebo spouštět příkazy SQL na databázovém serveru.
* V Pythonu poskytuje knihovna pyodbc přístup k databázi s rozhraním ODBC jako základní vrstvou.

## <a name="azure-tools"></a>Nástroje Azure

V dsvm jsou nainstalované následující nástroje Azure:

* **Azure CLI**: Rozhraní příkazového řádku v Azure můžete použít k vytváření a správě prostředků Azure pomocí příkazů prostředí. Chcete-li otevřít nástroje Azure, zadejte **nápovědu azure**. Další informace najdete na [stránce dokumentace k azure cli](https://docs.microsoft.com/cli/azure/get-started-with-az-cli2).
* **Azure Storage Explorer**: Azure Storage Explorer je grafický nástroj, který můžete použít k procházení objektů, které jste uložili v účtu úložiště Azure a k nahrávání a stahování dat do a z objektů blob Azure. Průzkumníka úložiště můžete získat z ikony zástupce na ploše. Můžete ji také otevřít z výzvy prostředí zadáním **aplikace StorageExplorer**. Musíte být přihlášeni z klienta X2Go nebo mít x11 předávání nastaveno.
* **Knihovny Azure**: Následující knihovny jsou předinstalované na DSVM:
  
  * **Python**: Knihovny související s Azure v *Pythonu*jsou azure , *azureml*, *pydocumentdb*a *pyodbc*. S prvními třemi knihovnami máte přístup ke službám úložiště Azure, Azure Machine Learning a Azure Cosmos DB (databáze NoSQL v Azure). Čtvrtá knihovna, pyodbc (spolu s ovladačem Microsoft ODBC pro SQL Server), umožňuje přístup k SQL Serveru, Azure SQL Database a Azure SQL Data Warehouse z Pythonu pomocí rozhraní ODBC. Zadejte **seznam pip,** abyste viděli všechny uvedené knihovny. Nezapomeňte spustit tento příkaz v prostředí pythonu 2.7 i 3.5.
  * **R**: Knihovny související s Azure v R jsou AzureML a RODBC.
  * **Java**: Seznam knihoven Azure Java najdete v adresáři /dsvm/sdk/AzureSDKJava na DSVM. Klíčové knihovny jsou Azure rozhraní API úložiště a správy, Azure Cosmos DB a JDBC ovladače pro SQL Server.  

Na portál [Azure](https://portal.azure.com) se dostanete z předinstalovaného prohlížeče Firefox. Na webu Azure Portal můžete vytvářet, spravovat a monitorovat prostředky Azure.

## <a name="azure-machine-learning"></a>Azure Machine Learning

Azure Machine Learning je plně spravovaná cloudová služba, kterou můžete použít k vytváření, nasazování a sdílení prediktivních analytických řešení. Experimenty a modely vytváříte ze sady Azure Machine Learning Studio (klasické). Pokud chcete získat přístup k Azure Machine Learning z webového prohlížeče na DSVM, přejděte na [Microsoft Azure Machine Learning](https://studio.azureml.net).

Po přihlášení k Azure Machine Learning Studio (klasické), můžete použít plátno experimentování k vytvoření logického toku pro algoritmy strojového učení. Máte také přístup k jupyterovému poznámkovému bloku, který je hostovaný v Azure Machine Learning. Poznámkový blok může bezproblémově pracovat s experimenty v Azure Machine Learning Studio (klasické). 

Zprovoznit modely strojového učení, které vytvoříte, jejich zabalením do rozhraní webové služby. Zprovoznění modelů strojového učení umožňuje klientům napsaným v libovolném jazyce vyvolat předpovědi z těchto modelů. Další informace naleznete v [dokumentaci k machine learningu](https://azure.microsoft.com/documentation/services/machine-learning/).

Můžete také vytvořit modely v R nebo Pythonu na DSVM a pak je nasadit v produkčním prostředí na Azure Machine Learning. Microsoft nainstaloval knihovny v R (**AzureML**) a Python (**azureml**) pro podporu této funkce.

Informace o tom, jak nasadit modely v R a Pythonu do Azure Machine Learning, najdete v tématu [deset věcí, které můžete dělat na virtuálním počítači datové vědy](vm-do-ten-things.md).

> [!NOTE]
> Pokyny v [deset věcí, které můžete udělat na virtuální mši pro datové vědy](vm-do-ten-things.md) byly napsány pro verzi systému Windows DSVM. Informace o nasazení modelů do Azure Machine Learning se však vztahují také na Linux DSVM.

## <a name="machine-learning-tools"></a>Nástroje strojového učení

DSVM je dodáván s několika strojovými nástroji a algoritmy, které jsou předkompilovány a předinstalovány místně. Mezi ně patří:

* **Microsoft Cognitive Toolkit:** Sada nástrojů pro hluboké učení.
* **Vowpal Wabbit:** Rychlý online algoritmus učení.
* **XGBoost**: Nástroj, který poskytuje optimalizované, posílené stromové algoritmy.
* **Python**: Anaconda Python je dodáván s algoritmy strojového učení s knihovnami, jako je Scikit-learn. Pomocí příkazu můžete nainstalovat `pip install` další knihovny.
* **R**: Bohatá knihovna funkcí strojového učení je k dispozici pro R. Předinstalované knihovny zahrnují lm, glm, randomForest a rpart. Další knihovny můžete nainstalovat `install.packages(<lib name>)`spuštěním aplikace .

Microsoft Cognitive Toolkit, Vowpal Wabbit a XGBoost jsou podrobněji popsány v následujících částech.

### <a name="microsoft-cognitive-toolkit"></a>Microsoft Cognitive Toolkit

Microsoft Cognitive Toolkit je open-source sada nástrojů pro hluboké učení. Je to nástroj příkazového řádku (CNTK) a je již v PATH.

Chcete-li spustit základní ukázku, spusťte ve skořápce následující příkazy:

```bash
cd /home/[USERNAME]/notebooks/CNTK/HelloWorld-LogisticRegression
cntk configFile=lr_bs.cntk makeMode=false command=Train
```

Další informace naleznete v [úložišti GitHub CNTK](https://github.com/Microsoft/CNTK) a [wiki CNTK](https://github.com/Microsoft/CNTK/wiki).

### <a name="vowpal-wabbit"></a>Vowpal Wabbit

Vowpal Wabbit je systém strojového učení, který používá techniky, jako je online, hašování, allreduce, redukce, learning2search, aktivní a interaktivní učení.

Chcete-li spustit nástroj v základním příkladu, spusťte následující příkazy:

```bash
cp -r /dsvm/tools/VowpalWabbit/demo vwdemo
cd vwdemo
vw house_dataset
```

Vowpal Wabbit demo adresář obsahuje další, větší dema. Další informace o Vowpal Wabbit najdete v [úložišti GitHub Vowpal Wabbit](https://github.com/JohnLangford/vowpal_wabbit) a [wiki Vowpal Wabbit](https://github.com/JohnLangford/vowpal_wabbit/wiki).

### <a name="xgboost"></a>XGBoost

Knihovna XGBoost je navržena a optimalizována pro posílené (stromové) algoritmy. Cílem knihovny XGBoost je posunout výpočetní limity počítačů do extrémů potřebných k zajištění rozšíření stromu ve velkém měřítku, které je škálovatelné, přenosné a přesné.

XGBoost je k dispozici jako příkazový řádek a jako knihovna R.

Chcete-li použít knihovnu XGBoost v R, spusťte interaktivní relaci R (ve skořepině zadejte **R**) a načtěte knihovnu.

Zde je jednoduchý příklad, který můžete spustit na řádku R:

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

Chcete-li spustit příkazový řádek XGBoost, spusťte tyto příkazy ve skořápce:

```bash
cp -r /dsvm/tools/xgboost/demo/binary_classification/ xgboostdemo
cd xgboostdemo
xgboost mushroom.conf
```

Soubor modelu je zapsán do zadaného adresáře. Informace o tomto ukázkovém příkladu na GitHubu naleznete [v tématu Binární klasifikace](https://github.com/dmlc/xgboost/tree/master/demo/binary_classification).

Další informace o XGBoostu najdete v [dokumentaci KGBoost](https://xgboost.readthedocs.org/en/latest/) a [v úložišti XGBoost GitHub](https://github.com/dmlc/xgboost).

### <a name="rattle"></a>Rattle

Chrastítko (*R* *A*nalytical *T*ool *T*o *L*vydělávat *E*asily) používá GUI-založené zkoumání dat a modelování. Chrastítko:
- Představuje statistické a vizuální souhrny údajů.
- Transformuje data, která lze snadno modelovat.
- Vytvoří modely bez dohledu i modely s dohledem z dat.
- Představuje grafický výkon modelů.
- Skóre nové sady dat.
- Generuje kód R.
- Replikuje operace v uzp.

Chcete-li spustit rattle, musíte být přihlášeni k grafické relaci plochy. V terminálu zadejte **R,** chcete-li otevřít prostředí R. Na řádku R zadejte následující příkazy:

```R
library(rattle)
rattle()
```

Otevře se grafické rozhraní, které má sadu karet. Pomocí následujících kroků rychlého startu v rattle použít ukázkovou sadu dat počasí a vytvořit model. V některých krocích budete vyzváni k automatické instalaci a načtení některých požadovaných balíčků R, které ještě nejsou v systému.

> [!NOTE]
> Pokud nemáte oprávnění k instalaci balíčku do systémového adresáře (výchozí), může se v okně konzoly R zobrazit výzva k instalaci balíčků do osobní knihovny. Pokud se zobrazí tyto výzvy, zadejte **y**.

1. Vyberte **Provést**.
1. Dialogové okno vás vyzve k načtení ukázkové datové sady počasí. Chcete-li příklad načíst, vyberte **možnost Ano.**
1. Vyberte kartu **Model.**
1. Vyberte **Execute** pro vytvoření rozhodovacího stromu.
1. Výběrem **možnosti Kreslení** zobrazíte rozhodovací strom.
1. Vyberte možnost **Doménová struktura** a pak vyberte **Spustit,** chcete-li vytvořit náhodnou doménovou strukturu.
1. Vyberte kartu **Vyhodnotit.**
1. Vyberte možnost **Riziko** a pak vyberte **Spustit,** chcete-li zobrazit dvě obrázky výkonu **rizika (kumulativní).**
1. Výběrem karty **Protokol** zobrazíte generovaný kód R pro předchozí operace. (Z důvodu chyby v aktuální verzi rattle, **#** je nutné vložit znak před **Export tohoto protokolu** v textu protokolu.)
1. Výběrem tlačítka **Export** uložte soubor skriptu R s názvem *weather_script. R* do domovské složky.

Můžete opustit Rattle a R. Nyní můžete upravit vygenerovaný skript R. Nebo použijte skript, jak to je, a spusťte jej kdykoliv opakovat vše, co bylo provedeno v Rattle UI. Zejména pro začátečníky v R, je to způsob, jak rychle provést analýzu a strojové učení v jednoduchém grafickém rozhraní, zatímco automaticky generuje kód v R upravit nebo pro učení.

## <a name="next-steps"></a>Další kroky

Máte ještě nějaké otázky? Zvažte vytvoření [lístku podpory](https://azure.microsoft.com/support/create-ticket/).