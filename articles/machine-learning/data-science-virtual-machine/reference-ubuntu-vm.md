---
title: 'Reference: Ubuntu Data Science Virtuální stroj'
titleSuffix: Azure Data Science Virtual Machine
description: Podrobnosti o nástrojích zahrnutých ve virtuálním stroji Ubuntu Data Science
author: gvashishtha
ms.service: machine-learning
ms.subservice: data-science-vm
ms.author: gopalv
ms.date: 09/11/2019
ms.topic: reference
ms.openlocfilehash: 830668a78929a5e6a7e131ade5c62b81e6d725c3
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2020
ms.locfileid: "80631369"
---
# <a name="reference-ubuntu-linux-data-science-virtual-machine"></a>Reference: Ubuntu (Linux) Data Science Virtuální stroj

Níže naleznete seznam dostupných nástrojů na vašem virtuálním stroji Ubuntu Data Science. 

## <a name="deep-learning-libraries"></a>Knihovny pro hloubkové učení

### <a name="cntk"></a>CNTK

Microsoft Cognitive Toolkit je open-source hloubkové učení toolkit. Vazby Pythonu jsou k dispozici v prostředí root a py35 Conda. Má také nástroj příkazového řádku (CNTK), který je již v cestě.

Ukázkové poznámkové bloky Pythonu jsou k dispozici v JupyterHubu. Chcete-li spustit základní ukázku na příkazovém řádku, spusťte ve skořápce následující příkazy:

```bash
cd /home/[USERNAME]/notebooks/CNTK/HelloWorld-LogisticRegression
cntk configFile=lr_bs.cntk makeMode=false command=Train
```

Další informace naleznete v části CNTK [na GitHubu](https://github.com/Microsoft/CNTK) a na [wiki CNTK](https://github.com/Microsoft/CNTK/wiki).

### <a name="caffe"></a>Caffe

Caffe je hluboký vzdělávací rámec z Berkeley Vision and Learning Center. Je k dispozici v /opt/caffe. Příklady najdete v /opt/caffe/examples.

### <a name="caffe2"></a>Caffe2

Caffe2 je hluboký vzdělávací rámec z Facebooku, který je postaven na Caffe. Je k dispozici v Pythonu 2.7 v kořenovém prostředí Conda. Chcete-li jej aktivovat, spusťte z prostředí následující příkaz:

```bash
source /anaconda/bin/activate root
```

Některé příklady poznámkových bloků jsou k dispozici v JupyterHub.

### <a name="h2o"></a>H2O

H2O je rychlá platforma pro strojové učení a prediktivní analýzu v paměti. Balíček Pythonu je nainstalován v kořenovém prostředí i v prostředí py35 Anaconda. Je také nainstalován balíček R. 

Chcete-li otevřít h2O z `java -jar /dsvm/tools/h2o/current/h2o.jar`příkazového řádku, spusťte . Existují různé [možnosti příkazového řádku,](http://docs.h2o.ai/h2o/latest-stable/h2o-docs/starting-h2o.html#from-the-command-line) které můžete chtít nakonfigurovat. K webovému uživatelskému uživatelskému http://localhost:54321 uživatelskému uživatelskému uživatelskému uživatelskému uživatelského panelu Flow můžete přistupovat tak, že můžete začít. Ukázkové poznámkové bloky jsou také k dispozici v JupyterHub.

### <a name="keras"></a>Keras

Keras je rozhraní API neuronové sítě na vysoké úrovni v Pythonu. Může běžet na vrcholu TensorFlow, Microsoft Cognitive Toolkit nebo Theano. Je k dispozici v prostředí root a py35 Python.

### <a name="mxnet"></a>MXNet

MXNet je rámec hlubokého učení navržený pro efektivitu i flexibilitu. Má R a Python vazby zahrnuty na DSVM. Ukázkové poznámkové bloky jsou součástí JupyterHub a ukázkový kód je k dispozici v /dsvm/samples/mxnet.

### <a name="nvidia-digits"></a>ČÍSLICE NVIDIA

Nvidia Deep Learning GPU Training System, známý jako DIGITS, je systém pro zjednodušení běžných úkolů hlubokého učení. Mezi tyto úkoly patří správa dat, navrhování a školení neuronových sítí v systémech GPU a sledování výkonu v reálném čase s pokročilou vizualizací.

Digits je k dispozici jako služba s názvem *číslice*. Spusťte službu http://localhost:5000 a přejděte na a začněte.

DIGITS je také nainstalován jako modul Pythonu v kořenovém prostředí Conda.

### <a name="tensorflow"></a>TensorFlow

TensorFlow je knihovna společnosti Google pro hluboké učení. Jedná se o knihovnu softwaru s otevřeným zdrojovým kódem pro numerické výpočty pomocí grafů toku dat. TensorFlow je k dispozici v prostředí py35 Python a některé ukázkové poznámkové bloky jsou součástí JupyterHub.

### <a name="theano"></a>Theano

Theano je knihovna Pythonu pro efektivní numerické výpočty. Je k dispozici v prostředí root a py35 Python. 

### <a name="torch"></a>Torch

Torch je vědecký výpočetní rámec s širokou podporou algoritmů strojového učení. Je k dispozici v /dsvm/tools/torch a interaktivní relace **a** správce balíčků LuaRocks jsou k dispozici na příkazovém řádku. Příklady jsou k dispozici v /dsvm/samples/torch.

PyTorch je také k dispozici v kořenovém prostředí Anaconda. Příklady jsou v /dsvm/samples/pytorch.

## <a name="microsoft-machine-learning-server"></a>Server Microsoft Machine Learning

R je jedním z nejoblíbenějších jazyků pro analýzu dat a strojové učení. Pokud chcete použít R pro analýzy, virtuální počítač má Microsoft Machine Learning Server s Microsoft R Open a Math Kernel Library. Knihovna matematického jádra optimalizuje matematické operace běžné v analytických algoritmech. Microsoft R Open je 100 procent kompatibilní s CRAN R a všechny knihovny R publikované v ROZHRANÍ CRAN lze nainstalovat na Microsoft R Open. 

Machine Learning Server umožňuje škálování a zprovoznění modelů R do webových služeb. Programy R můžete upravovat v jednom z výchozích editorů, jako je RStudio, vi nebo Emacs. Pokud dáváte přednost použití editoru Emacs, byl předinstalován. Balíček Emacs ESS (Emacs Speaks Statistics) zjednodušuje práci se soubory R v editoru Emacs.

Chcete-li otevřít konzolu R, zadejte **r** do skořepiny. Tento příkaz vás přenese do interaktivního prostředí. Chcete-li rozvíjet program R, obvykle používáte editor jako Emacs nebo vi a pak spusťte skripty v rámci R. S RStudio, máte plnou grafické IDE rozvíjet program R.

K dispozici je také Skript R pro instalaci [Top 20 R balíčky,](https://www.kdnuggets.com/2015/06/top-20-r-packages.html) pokud chcete. Tento skript můžete spustit po interaktivním rozhraní Jazyka R. Jak již bylo zmíněno dříve, můžete otevřít toto rozhraní zadáním **R** do prostředí.  

## <a name="python"></a>Python

Anaconda Python je nainstalován v prostředích Pythonu 2.7 a 3.5. Prostředí 2.7 se nazývá _root_a prostředí 3.5 se nazývá _py35_. Tato distribuce obsahuje základní Python spolu s asi 300 nejoblíbenějších matematických, inženýrských a datových analytických balíčků.

Prostředí py35 je výchozí. Chcete-li aktivovat kořenové prostředí (2.7), použijte tento příkaz:

```bash
source activate root
```

Chcete-li znovu aktivovat prostředí py35, použijte tento příkaz:

```bash
source activate py35
```

Chcete-li vyvolat interaktivní relaci Pythonu, zadejte **python** do prostředí. 

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

Distribuce Anaconda je také dodávána s notebookem Jupyter, prostředím pro sdílení kódu a analýzy. K notebooku Jupyter se přistupuje přes JupyterHub. Přihlašujete se pomocí místního uživatelského jména a hesla pro Linux.

Notebookový server Jupyter byl předkonfigurován s jádry Pythonu 2, Pythonu 3 a R. Pomocí ikony **jupyterového notebooku** na ploše otevřete prohlížeč a získejte přístup k notebookovému serveru. Pokud jste na virtuálním počítači přes SSH nebo x2Go klienta, můžete také [https://localhost:8000/](https://localhost:8000/)přistupovat k notebooku Jupyter server na .

> [!NOTE]
> Pokud se objeví upozornění na certifikát, pokračujte.

K notebookovému serveru Jupyter můžete přistupovat z libovolného hostitele. Zadejte **https://\<název DNS\>virtuálního počítačů nebo IP adresu :8000/**.

> [!NOTE]
> Port 8000 se otevře ve firewallu ve výchozím nastavení při zřízení virtuálního počítače. 

Zabalili jsme ukázkové poznámkové bloky - jeden v Pythonu a jeden v R. Odkaz na ukázky na domovské stránce poznámkového bloku můžete vidět po ověření poznámkového bloku Jupyter pomocí místního uživatelského jména a hesla pro Linux. Nový poznámkový blok můžete vytvořit tak, že vyberete **Nový**a pak vyberete příslušné jazorné jádro. Pokud tlačítko **Nový** nevidíte, v levém horním rohu vyberte ikonu **Jupyter** a přejděte na domovskou stránku notebooku.

## <a name="apache-spark-standalone"></a>Apache Spark samostatný

Samostatná instance Apache Spark je předinstalována na LinuxU DSVM, která vám pomůže vyvíjet aplikace Spark místně před testováním a nasazením na velkých clusterech. 

Programy PySpark můžete spouštět prostřednictvím jádra Jupyter. Když otevřete Jupyter, vyberte tlačítko **Nový** a měli byste vidět seznam dostupných jader. **Spark - Python** je jádro PySpark, které umožňuje vytvářet aplikace Spark pomocí jazyka Python. Můžete také použít Python IDE jako PyCharm nebo Spyder k vytvoření programu Spark. 

V této samostatné instanci zásobníku Spark běží v rámci volajícího klientského programu. Tato funkce umožňuje rychlejší a snadnější řešení problémů ve srovnání s vývojem v clusteru Spark.

Jupyter poskytuje ukázkový notebook PySpark. Najdete ji v adresáři SparkML pod domovskou adresáří Jupyter ($HOME/notebooks/SparkML/pySpark). 

Pokud programujete v Jazyce R pro Spark, můžete použít Microsoft Machine Learning Server, SparkR nebo sparklyr. 

Před spuštěním v kontextu Spark v Microsoft Machine Learning Server, musíte udělat jednorázový krok nastavení povolit místní jeden uzel Hadoop HDFS a příze instance. Ve výchozím nastavení jsou služby Hadoop nainstalovány, ale zakázány v dsvm. Chcete-li jej povolit, musíte poprvé spustit následující příkazy jako root:

```bash
echo -e 'y\n' | ssh-keygen -t rsa -P '' -f ~hadoop/.ssh/id_rsa
cat ~hadoop/.ssh/id_rsa.pub >> ~hadoop/.ssh/authorized_keys
chmod 0600 ~hadoop/.ssh/authorized_keys
chown hadoop:hadoop ~hadoop/.ssh/id_rsa
chown hadoop:hadoop ~hadoop/.ssh/id_rsa.pub
chown hadoop:hadoop ~hadoop/.ssh/authorized_keys
systemctl start hadoop-namenode hadoop-datanode hadoop-yarn
```

Služby související s Hadoopem můžete zastavit, když ```systemctl stop hadoop-namenode hadoop-datanode hadoop-yarn```je nepotřebujete, spuštěním .

Adresář /dsvm/samples/MRS poskytuje ukázku, která ukazuje, jak vyvíjet a testovat microsoft machine learning server ve vzdáleném kontextu Spark (samostatná instance Spark na DSVM).

## <a name="ides-and-editors"></a>IDC a redaktoři

Máte na výběr z několika editorů kódu, včetně vi/Vim, Emacs, PyCharm, RStudio a IntelliJ. 

PyCharm, RStudio a IntelliJ jsou grafické editory. Chcete-li je používat, musíte být přihlášeni k grafické ploše. Otevřete je pomocí zástupců nabídek plochy a aplikace.

Vim a Emacs jsou textové editory. Na Emacsu usnadňuje doplňkový balíček ESS práci s R v editoru Emacs. Více informací naleznete na [internetových stránkách ESS](https://ess.r-project.org/).

LaTex je nainstalován prostřednictvím balíčku texlive, spolu s emacs add-on balíček s názvem [AUCTeX](https://www.gnu.org/software/auctex/manual/auctex/auctex.html). Tento balíček zjednodušuje vytváření dokumentů LaTex v aplikaci Emacs.  

## <a name="databases"></a>Databáze

### <a name="graphical-sql-client"></a>Grafický klient SQL

SQuirrel SQL, grafický SQL klient, se může připojit k různým databázím (například Microsoft SQL Server a MySQL) a spouštět dotazy SQL. Squirrel SQL můžete spustit z grafické relace plochy (například prostřednictvím klienta X2Go) pomocí ikony na ploše. Nebo můžete spustit klienta pomocí následujícího příkazu v prostředí:

```bash
/usr/local/squirrel-sql-3.7/squirrel-sql.sh
```

Před prvním použitím nastavte ovladače a aliasy databáze. Ovladače JDBC jsou umístěny na adrese /usr/share/java/jdbcdrivers.

Další informace naleznete v tématu [SQuirrel SQL](http://squirrel-sql.sourceforge.net/index.php?page=screenshots).

### <a name="command-line-tools-for-accessing-microsoft-sql-server"></a>Nástroje příkazového řádku pro přístup k serveru Microsoft SQL Server

Balíček ovladačů ODBC pro SQL Server je také dodáván se dvěma nástroji příkazového řádku:

- **bcp**: Nástroj BCP hromadně kopíruje data mezi instancí serveru Microsoft SQL Server a datovým souborem v uživatelem určeném formátu. Pomocí nástroje bcp můžete importovat velký počet nových řádků do tabulek serveru SQL Server nebo exportovat data z tabulek do datových souborů. Chcete-li importovat data do tabulky, musíte použít formátovaný soubor vytvořený pro tuto tabulku. Nebo je nutné pochopit strukturu tabulky a typy dat, které jsou platné pro její sloupce.

  Další informace naleznete v [tématu Připojení pomocí protokolu bcp](https://msdn.microsoft.com/library/hh568446.aspx).

- **sqlcmd**: Příkazy Transact-SQL můžete zadat pomocí nástroje sqlcmd. Na příkazovém řádku můžete také zadat systémové procedury a soubory skriptů. Tento nástroj používá rozhraní ODBC ke spuštění dávek Transact-SQL.

  Další informace naleznete v [tématu Připojení k sqlcmd](https://msdn.microsoft.com/library/hh568447.aspx).

  > [!NOTE]
  > Existují určité rozdíly v tomto nástroji mezi platformami Linux a Windows. Podrobnosti naleznete v dokumentaci.

### <a name="database-access-libraries"></a>Knihovny přístupu k databázi

Knihovny jsou k dispozici v R a Pythonu pro přístup k databázi:

* V r, můžete použít balíček RODBC nebo dplyr balíček dotazovat nebo spouštět příkazy SQL na databázovém serveru.
* V Pythonu poskytuje knihovna pyodbc přístup k databázi s rozhraním ODBC jako základní vrstvou.  

## <a name="azure-tools"></a>Nástroje Azure

Následující nástroje Azure se nainstalované na virtuálním počítači:

* **Azure CLI**: Rozhraní příkazového řádku v Azure můžete použít k vytváření a správě prostředků Azure pomocí příkazů prostředí. Chcete-li otevřít nástroje Azure, zadejte **nápovědu azure**. Další informace najdete na [stránce dokumentace k azure cli](https://docs.microsoft.com/cli/azure/get-started-with-az-cli2).
* **Azure Storage Explorer**: Azure Storage Explorer je grafický nástroj, který můžete použít k procházení objektů, které jste uložili v účtu úložiště Azure a k nahrávání a stahování dat do a z objektů blob Azure. Průzkumníka úložiště můžete získat z ikony zástupce na ploše. Můžete ji také otevřít z výzvy prostředí zadáním **aplikace StorageExplorer**. Musíte být přihlášeni z klienta X2Go nebo mít x11 předávání nastaveno.
* **Knihovny Azure**: Následují některé z předinstalovaných knihoven.
  
  * **Python**: Knihovny související s Azure v *Pythonu*jsou azure , *azureml*, *pydocumentdb*a *pyodbc*. S prvními třemi knihovnami máte přístup ke službám úložiště Azure, Azure Machine Learning a Azure Cosmos DB (databáze NoSQL v Azure). Čtvrtá knihovna, pyodbc (spolu s ovladačem Microsoft ODBC pro SQL Server), umožňuje přístup k SQL Serveru, Azure SQL Database a Azure SQL Data Warehouse z Pythonu pomocí rozhraní ODBC. Zadejte **seznam pip,** abyste viděli všechny uvedené knihovny. Nezapomeňte spustit tento příkaz v prostředí pythonu 2.7 i 3.5.
  * **R**: Knihovny související s Azure v R jsou AzureML a RODBC.
  * **Java**: Seznam knihoven Azure Java najdete v adresáři /dsvm/sdk/AzureSDKJava na virtuálním počítači. Klíčové knihovny jsou Azure rozhraní API úložiště a správy, Azure Cosmos DB a JDBC ovladače pro SQL Server.  

Na portál [Azure](https://portal.azure.com) se dostanete z předinstalovaného prohlížeče Firefox. Na webu Azure Portal můžete vytvářet, spravovat a monitorovat prostředky Azure.

## <a name="azure-machine-learning"></a>Azure Machine Learning

Azure Machine Learning je plně spravovaná cloudová služba, která umožňuje vytvářet, nasazovat a sdílet prediktivní analytická řešení. Experimenty a modely můžete vytvářet ve studiu Azure Machine Learning (preview). Přístup k němu můžete z webového prohlížeče na virtuálním počítači pro datové vědy na webu [Microsoft Azure Machine Learning](https://ml.azure.com).

Po přihlášení do studia Azure Machine Learning studio, můžete použít plátno experimentování k vytvoření logickétotototok pro algoritmy strojového učení. Máte také přístup k poznámkovému bloku Jupyter, který je hostovaný v Azure Machine Learning a můžete bez problémů pracovat s experimenty ve studiu Azure Machine Learning. 

Zprovoznit modely strojového učení, které jste vytvořili, jejich zabalením do rozhraní webové služby. Zprovoznění modelů strojového učení umožňuje klientům napsaným v libovolném jazyce vyvolat předpovědi z těchto modelů. Další informace naleznete v [dokumentaci k machine learningu](https://azure.microsoft.com/documentation/services/machine-learning/).

Můžete také vytvořit modely v R nebo Pythonu na virtuálním počítači a pak je nasadit v produkčním prostředí na Azure Machine Learning. Pro povolení této funkce jsme nainstalovali knihovny v Jazyce R (**AzureML**) a Pythonu (**azureml).**

Informace o tom, jak nasadit modely v R a Pythonu do Azure Machine Learning, najdete v tématu [deset věcí, které můžete dělat na virtuálním počítači datové vědy](vm-do-ten-things.md).

> [!NOTE]
> Tyto pokyny byly napsány pro verzi systému Windows virtuálního počítače pro datové vědy. Ale informace poskytované tam o nasazení modelů do Azure Machine Learning se vztahuje na virtuální počítač s Linuxem.

## <a name="machine-learning-tools"></a>Nástroje strojového učení

Virtuální počítač je dodáván s nástroji strojového učení a algoritmy, které byly předkompilovány a předinstalovány místně. Mezi ně patří:

* **Vowpal Wabbit:** Rychlý online algoritmus učení.
* **xgboost**: Nástroj, který poskytuje optimalizované, posílené stromové algoritmy.
* **Rattle**: Grafický nástroj založený na R pro snadné zkoumání a modelování dat.
* **Python**: Anaconda Python je dodáván s algoritmy strojového učení s knihovnami, jako je Scikit-learn. Pomocí příkazu můžete nainstalovat `pip install` další knihovny.
* **LightGBM**: Rychlé, distribuované, vysoce výkonné přechod posílení rámce založené na algoritmy rozhodovacího stromu.
* **R**: Bohatá knihovna funkcí strojového učení je k dispozici pro R. Předinstalované knihovny zahrnují lm, glm, randomForest a rpart. Další knihovny můžete nainstalovat spuštěním tohoto příkazu:
  
        install.packages(<lib name>)

Zde je několik dalších informací o prvních třech obráběcích nástrojích v seznamu.

### <a name="vowpal-wabbit"></a>Vowpal Wabbit

Vowpal Wabbit je systém strojového učení, který používá techniky, jako je online, hašování, allreduce, redukce, learning2search, aktivní a interaktivní učení.

Chcete-li nástroj spustit v základním příkladu, použijte následující příkazy:

```bash
cp -r /dsvm/tools/VowpalWabbit/demo vwdemo
cd vwdemo
vw house_dataset
```

V tomto adresáři jsou další větší ukázky. Další informace o Vowpal Wabbit najdete v [této části GitHubu](https://github.com/JohnLangford/vowpal_wabbit) a [wiki Vowpal Wabbit](https://github.com/JohnLangford/vowpal_wabbit/wiki).

### <a name="xgboost"></a>xgboost

Knihovna xgboost je navržena a optimalizována pro posílené (stromové) algoritmy. Cílem této knihovny je posunout výpočetní limity počítačů do extrémů potřebných k zajištění rozšíření stromu ve velkém měřítku, které je škálovatelné, přenosné a přesné.

Je k dispozici jako příkazový řádek a knihovna R. Chcete-li použít tuto knihovnu v R, můžete spustit interaktivní relaci R (zadáním **R** do prostředí) a načíst knihovnu.

Zde je jednoduchý příklad, který můžete spustit v řádku R:

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

Chcete-li spustit příkazový řádek xgboost, zde jsou příkazy pro spuštění v prostředí:

```bash
cp -r /dsvm/tools/xgboost/demo/binary_classification/ xgboostdemo
cd xgboostdemo
xgboost mushroom.conf
```

Soubor modelu je zapsán do zadaného adresáře. Informace o tomto ukázkovém příkladu najdete [na GitHubu](https://github.com/dmlc/xgboost/tree/master/demo/binary_classification).

Další informace o xgboostu najdete na [stránce dokumentace xgboost](https://xgboost.readthedocs.org/en/latest/) a jeho [úložišti GitHub](https://github.com/dmlc/xgboost).

### <a name="rattle"></a>Rattle

Chrastítko **(R** **A**nalytical **T**ool **T**o **L**vydělávat **E**asily) používá GUI-založené zkoumání dat a modelování. Prezentuje statistické a vizuální souhrny dat, transformuje data, která lze snadno modelovat, vytváří modely bez dohledu i pod dohledem z dat, graficky prezentuje výkon modelů a získává nové datové sady. Generuje také kód R, replikovat operace v unovém uzem, které lze spustit přímo v R nebo použít jako výchozí bod pro další analýzu.

Chcete-li spustit rattle, musíte být v grafické relaci přihlášení na ploše. Na terminálu zadejte **R,** chcete-li otevřít prostředí R. Na řádku R zadejte následující příkazy:

```R
library(rattle)
rattle()
```

Nyní se otevře grafické rozhraní se sadou karet. Pomocí následujících kroků rychlého startu v rattle použít ukázkovou sadu dat počasí a vytvořit model. V některých krocích budete vyzváni k automatické instalaci a načtení některých požadovaných balíčků R, které ještě nejsou v systému.

> [!NOTE]
> Pokud nemáte přístup k instalaci balíčku v systémovém adresáři (výchozí), může se v okně konzoly R zobrazit výzva k instalaci balíčků do osobní knihovny. Odpovězte **na y,** pokud se zobrazí tyto výzvy.

1. Vyberte **Provést**.
1. Zobrazí se dialogové okno s dotazem, zda chcete použít ukázkovou sadu dat počasí. Chcete-li příklad načíst, vyberte **možnost Ano.**
1. Vyberte kartu **Model.**
1. Vyberte **Execute** pro vytvoření rozhodovacího stromu.
1. Výběrem **možnosti Kreslení** zobrazíte rozhodovací strom.
1. Vyberte možnost **Doménová struktura** a vyberte **Spustit** pro vytvoření náhodné doménové struktury.
1. Vyberte kartu **Vyhodnotit.**
1. Vyberte možnost **Riziko** a výběrem **možnosti Spustit** zobrazíte dvě obrázky výkonu **rizika (kumulativní).**
1. Výběrem karty **Protokol** zobrazíte generovaný kód R pro předchozí operace.
   (Z důvodu chyby v aktuální verzi rattle, **#** je třeba vložit znak před **Export tohoto protokolu** v textu protokolu.)
1. Výběrem tlačítka **Export** uložte soubor skriptu R s názvem *weather_script. R* do domovské složky.

Můžete opustit Rattle a R. Nyní můžete upravit vygenerovaný skript R. Nebo použijte skript, jak to je, a spusťte jej kdykoliv opakovat vše, co bylo provedeno v rámci rattle ui. Zejména pro začátečníky v R, je to způsob, jak rychle provést analýzu a strojové učení v jednoduchém grafickém rozhraní, zatímco automaticky generuje kód v R upravit nebo se učit.

## <a name="next-steps"></a>Další kroky

Máte ještě nějaké otázky? Zvažte vytvoření [lístku podpory](https://azure.microsoft.com/support/create-ticket/).