---
title: 'Referenční informace: Ubuntu Data Science Virtual Machine'
titleSuffix: Azure Data Science Virtual Machine
description: Podrobnosti o nástrojích, které jsou součástí Data Science Virtual Machine Ubuntu
author: gvashishtha
services: machine-learning
ms.service: data-science-vm
ms.custom: devx-track-python
ms.author: gopalv
ms.date: 09/11/2019
ms.topic: reference
ms.openlocfilehash: e9a55f72718d6ed5991f3d0f16323409bb0f699f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "101661065"
---
# <a name="reference-ubuntu-linux-data-science-virtual-machine"></a>Referenční informace: Ubuntu (Linux) Data Science Virtual Machine

Níže najdete seznam dostupných nástrojů Ubuntu Data Science Virtual Machine. 

## <a name="deep-learning-libraries"></a>Knihovny pro hloubkové učení

### <a name="cntk"></a>CNTK

Microsoft Cognitive Toolkit je open source sada nástrojů pro hloubkové učení. Vazby Pythonu jsou k dispozici v prostředích root a py35 conda. Má také nástroj příkazového řádku (CNTK), který je již v cestě.

Ukázkové poznámkové bloky Pythonu jsou k dispozici v JupyterHub. Pokud chcete spustit základní ukázku z příkazového řádku, spusťte v prostředí následující příkazy:

```bash
cd /home/[USERNAME]/notebooks/CNTK/HelloWorld-LogisticRegression
cntk configFile=lr_bs.cntk makeMode=false command=Train
```

Další informace najdete v části CNTK na [GitHubu](https://github.com/Microsoft/CNTK) a na [wikiwebu CNTK](https://github.com/Microsoft/CNTK/wiki).

### <a name="caffe"></a>Caffe

Caffe je špičková architektura z Berkeley Learning a studijního centra. Je k dispozici v/opt/Caffe. Příklady najdete v/opt/Caffe/Examples..

### <a name="caffe2"></a>Caffe2

Caffe2 je systém hloubkového učení z Facebooku, který je založený na Caffe. Je k dispozici v Pythonu 2,7 v kořenovém prostředí conda. Pokud ho chcete aktivovat, spusťte následující příkaz z prostředí:

```bash
source /anaconda/bin/activate root
```

V JupyterHub jsou k dispozici několik ukázkových poznámkových bloků.

### <a name="h2o"></a>H2O

Nejedná se o rychlou, distribuovanou a integrovanou platformu pro strojové učení a prediktivní analýzu. Balíček Pythonu se instaluje jak v kořenu, tak i v prostředí py35 Anaconda. Nainstaluje se taky balíček R. 

Chcete-li otevřít z příkazového řádku, spusťte příkaz `java -jar /dsvm/tools/h2o/current/h2o.jar` . K dispozici jsou různé [Možnosti příkazového řádku](http://docs.h2o.ai/h2o/latest-stable/h2o-docs/starting-h2o.html#from-the-command-line) , které byste mohli chtít nakonfigurovat. Chcete-li začít, můžete získat přístup k webovému uživatelskému rozhraní toku `http://localhost:54321` . Ukázkové poznámkové bloky jsou také k dispozici v JupyterHub.

### <a name="keras"></a>Keras

Keras je neuronové síťové rozhraní API v Pythonu vysoké úrovně. Může běžet nad TensorFlow, Microsoft Cognitive Toolkit nebo Theano. Je k dispozici v prostředích root a py35 Pythonu.

### <a name="mxnet"></a>MXNet

MXNet je rozhraní hloubkového učení navržené pro zajištění efektivity i flexibility. Obsahuje vazby R a Pythonu, které jsou součástí DSVM. Ukázkové notebooky jsou součástí JupyterHub a v/dsvm/Samples/mxnet. je k dispozici vzorový kód.

### <a name="nvidia-digits"></a>ČÍSLICE NVIDIA

Školicí systém NVIDIA hloubkového učení GPU, označovaný jako číslice, představuje systém, který zjednodušuje běžné úlohy s hloubkovým učením. Mezi tyto úlohy patří Správa dat, navrhování a školení neuronové sítí na systémech GPU a monitorování výkonu v reálném čase s pokročilou vizualizací.

ČÍSLICE jsou k dispozici jako služba s názvem *číslice*. Spusťte službu a přejděte na adresu a začněte `http://localhost:5000` .

V kořenovém prostředí conda se také nainstalují číslice jako modul Pythonu.

### <a name="tensorflow"></a>TensorFlow

TensorFlow je knihovna pro hloubkové učení Google. Je to open source softwarová knihovna pro numerické výpočty pomocí grafů toku dat. TensorFlow je k dispozici v prostředí py35 Python a některé ukázkové poznámkové bloky jsou zahrnuté v JupyterHub.

### <a name="theano"></a>Theano

Theano je knihovna Pythonu pro efektivní číselné výpočty. Je k dispozici v prostředích root a py35 Pythonu. 

### <a name="torch"></a>Torch

Torch je vědecky výpočetní prostředí s podporou nejrůznějších algoritmů strojového učení. Je k dispozici v/dsvm/Tools/Torch **a v příkazovém** řádku jsou k dispozici interaktivní relace a správce balíčků LuaRocks. Příklady jsou k dispozici v/dsvm/Samples/Torch.

PyTorch je také k dispozici v kořenovém prostředí Anaconda. Příklady jsou v/dsvm/Samples/pytorch..

## <a name="microsoft-machine-learning-server"></a>Microsoft Machine Learning Server

R je jedním z nejoblíbenějších jazyků pro analýzu dat a strojové učení. Pokud pro analýzu chcete použít R, virtuální počítač se Microsoft Machine Learning Server s otevřenou a pomocí knihovny Microsoft R Open a matematického jádra. Knihovna Math kernel optimalizuje matematické operace běžné v analytických algoritmech. Microsoft R Open je 100 procent kompatibilního s CRAN R a kterákoli z knihoven R publikovaných v CRAN se dá nainstalovat do Microsoft R Open. 

Machine Learning Server poskytuje škálování a provoz modelů R na webové služby. Programy R můžete upravit v jednom z výchozích editorů, jako je RStudio, VI nebo (Emacs). Pokud dáváte přednost použití editoru (Emacs), je předinstalovaný. Balíček (Emacs) ESS ((Emacs) mluví STATISTICS) usnadňuje práci se soubory R v editoru (Emacs).

Pokud chcete otevřít konzolu R, zadáte **R** do prostředí. Tento příkaz vás provede interaktivním prostředím. K vývoji programu R obvykle používáte editor jako (Emacs) nebo VI a pak spouštíte skripty v jazyce R. Pomocí RStudio máte k dispozici kompletní grafické rozhraní IDE pro vývoj programu R.

K dispozici je také skript R, který vám umožní nainstalovat [horní 20 balíčků r](https://www.kdnuggets.com/2015/06/top-20-r-packages.html) , pokud chcete. Tento skript můžete spustit potom, co jste v interaktivním rozhraní R. Jak bylo zmíněno dříve, můžete toto rozhraní otevřít zadáním **R** do prostředí.  

## <a name="python"></a>Python

Anaconda Python se instaluje s prostředími Python 2,7 a 3,5. Prostředí 2,7 se nazývá _root_ a prostředí 3,5 se nazývá _py35_. Tato distribuce obsahuje základní Python spolu s přibližně 300 z nejoblíbenějších matematických, inženýrských a analytických balíčků.

Výchozím nastavením je prostředí py35. Pokud chcete aktivovat kořenové prostředí (2,7), použijte tento příkaz:

```bash
source activate root
```

Pokud chcete prostředí py35 aktivovat znovu, použijte tento příkaz:

```bash
source activate py35
```

Pokud chcete vyvolat interaktivní relaci Pythonu, zadejte v prostředí **Python** . 

Nainstalujte další knihovny Pythonu pomocí conda nebo PIP. V případě PIP aktivujte správné prostředí, pokud nechcete výchozí nastavení:

```bash
source activate root
pip install <package>
```

Nebo zadejte úplnou cestu k PIP:

```bash
/anaconda/bin/pip install <package>
```

V případě conda byste měli vždy zadat název prostředí (py35 nebo root):

```bash
conda install <package> -n py35
```

Pokud používáte grafické rozhraní nebo máte nastavené předávání X11, můžete zadat **PyCharm** a otevřít PYCHARM Python IDE. Můžete použít výchozí textové editory. Kromě toho můžete použít Spyder, integrované vývojové prostředí (IDE), které je součástí distribuce Anaconda Pythonu. Spyder potřebuje grafické nebo X11 přesměrování. Grafická plocha má zástupce pro Spyder.

## <a name="jupyter-notebook"></a>Poznámkový blok Jupyter

Anaconda distribuce také přichází s poznámkovým blokem Jupyter, prostředím pro sdílení kódu a analýzy. K poznámkovému bloku Jupyter se dostanete prostřednictvím JupyterHub. Přihlašujete se pomocí uživatelského jména a hesla místního systému Linux.

Server Jupyter Poznámkový blok byl předem nakonfigurován s jádry Python 2, Python 3 a R. Pomocí ikony **Jupyter notebook** plocha otevřete prohlížeč a přejděte na server poznámkového bloku. Pokud se nacházíte na virtuálním počítači přes SSH nebo klienta X2Go, můžete k serveru Jupyter notebook získat přístup také na adrese `https://localhost:8000/` .

> [!NOTE]
> Pokračujte, pokud se zobrazí upozornění týkající se certifikátu.

K serveru Jupyter Poznámkový blok můžete přistupovat z libovolného hostitele. Zadejte **https:// \<VM DNS name or IP address\> : 8000/**.

> [!NOTE]
> Po zřízení virtuálního počítače se ve výchozím nastavení v bráně firewall otevře port 8000. 

Máme balíčky ukázkových poznámkových bloků – jeden v Pythonu a druhý v R. Odkaz na ukázky na domovské stránce poznámkového bloku se zobrazí po ověření do poznámkového bloku Jupyter pomocí místního uživatelského jména a hesla systému Linux. Nový Poznámkový blok můžete vytvořit tak, že vyberete **nové** a pak vyberete příslušné jádro jazyka. Pokud tlačítko **Nový** nevidíte, vyberte v levém horním rohu ikonu **Jupyter** a přejděte na domovskou stránku serveru poznámkového bloku.

## <a name="apache-spark-standalone"></a>Apache Spark samostatný

Samostatná instance Apache Spark je předem nainstalovanou na platformě Linux DSVM, která vám umožní místní vývoj aplikací Spark ještě předtím, než je otestujete a nasadíte do velkých clusterů. 

Můžete spouštět PySpark programy prostřednictvím jádra Jupyter. Když otevřete Jupyter, vyberte tlačítko **Nový** a měl by se zobrazit seznam dostupných jader. **Spark-Python** je jádrem PySpark, který umožňuje sestavovat aplikace Spark pomocí jazyka Python. K sestavování programu Spark můžete použít také Python IDE, jako je PyCharm nebo Spyder. 

V této samostatné instanci se sada Spark spouští v rámci volajícího klientského programu. Tato funkce usnadňuje a usnadňuje řešení problémů v porovnání s vývojem v clusteru Spark.

Jupyter poskytuje ukázkový Poznámkový blok PySpark. Můžete ji najít v adresáři SparkML v domovském adresáři Jupyter ($HOME/notebooks/SparkML/pySpark). 

Pokud programujete v R pro Spark, můžete použít Microsoft Machine Learning Server, Spark nebo sparklyr. 

Před spuštěním v kontextu Sparku v Microsoft Machine Learning Server je třeba provést jednorázový krok nastavení, který povolí místní instanci Hadoop s jedním uzlem a instanci příz. Ve výchozím nastavení jsou na DSVM nainstalovány služby Hadoop, ale jsou zakázané. Pokud ho chcete povolit, musíte poprvé spustit následující příkazy jako root:

```bash
echo -e 'y\n' | ssh-keygen -t rsa -P '' -f ~hadoop/.ssh/id_rsa
cat ~hadoop/.ssh/id_rsa.pub >> ~hadoop/.ssh/authorized_keys
chmod 0600 ~hadoop/.ssh/authorized_keys
chown hadoop:hadoop ~hadoop/.ssh/id_rsa
chown hadoop:hadoop ~hadoop/.ssh/id_rsa.pub
chown hadoop:hadoop ~hadoop/.ssh/authorized_keys
systemctl start hadoop-namenode hadoop-datanode hadoop-yarn
```

Služby související se systémem Hadoop můžete zastavit, pokud je nepotřebujete spuštěním ```systemctl stop hadoop-namenode hadoop-datanode hadoop-yarn``` .

Adresář/dsvm/samples/MRS poskytuje ukázku, která ukazuje, jak vyvíjet a testovat Microsoft Machine Learning Server ve vzdáleném kontextu Spark (samostatná instance Sparku na DSVM).

## <a name="ides-and-editors"></a>IDEs a editory

Máte možnost zvolit si několik editorů kódu, včetně VI/vim, (Emacs), PyCharm, RStudio a IntelliJ. 

PyCharm, RStudio a IntelliJ jsou grafické editory. Pokud je chcete použít, musíte být přihlášeni k grafické ploše. Otevřete je pomocí klávesových zkratek nabídky Desktop a aplikace.

Procesory vim a (Emacs) jsou textové editory. V (Emacs) bude balíček doplňku ESS v editoru (Emacs) pracovat s jazykem R snadněji. Další informace najdete na [webu ESS](https://ess.r-project.org/).

LaTex se instaluje prostřednictvím balíčku texlive společně s balíčkem doplňků (Emacs) s názvem [AUCTeX](https://www.gnu.org/software/auctex/manual/auctex/auctex.html). Tento balíček zjednodušuje vytváření dokumentů LaTex v rámci (Emacs).  

## <a name="databases"></a>Databáze

### <a name="graphical-sql-client"></a>Grafický klient SQL

SQuirrel SQL, grafický klient SQL, se může připojit k různým databázím (jako je například Microsoft SQL Server a MySQL) a spouštět dotazy SQL. SQuirrel SQL můžete spustit z grafické relace plochy (například prostřednictvím klienta X2Go) pomocí ikony na ploše. Klienta můžete také spustit pomocí následujícího příkazu v prostředí:

```bash
/usr/local/squirrel-sql-3.7/squirrel-sql.sh
```

Před prvním použitím nastavte své ovladače a aliasy databáze. Ovladače JDBC jsou umístěné na adrese/usr/share/Java/jdbcdrivers.

Další informace najdete v tématu [SQUIRREL SQL](http://squirrel-sql.sourceforge.net/index.php?page=screenshots).

### <a name="command-line-tools-for-accessing-microsoft-sql-server"></a>Nástroje příkazového řádku pro přístup k Microsoft SQL Server

Balíček ovladačů rozhraní ODBC pro SQL Server také obsahuje dva nástroje příkazového řádku:

- **BCP**: nástroj BCP hromadně kopíruje data mezi instancí Microsoft SQL Server a datovým souborem v uživatelsky definovaném formátu. Pomocí nástroje BCP můžete importovat velký počet nových řádků do SQL Server tabulky nebo exportovat data z tabulek do datových souborů. Chcete-li importovat data do tabulky, je nutné použít formátový soubor vytvořený pro tuto tabulku. Nebo, musíte pochopit strukturu tabulky a typy dat, které jsou pro své sloupce platné.

  Další informace najdete v tématu [připojení pomocí BCP](/sql/connect/odbc/linux-mac/connecting-with-bcp).

- **Sqlcmd**: pomocí nástroje Sqlcmd můžete zadat příkazy jazyka Transact-SQL. Do příkazového řádku můžete také zadat systémové procedury a soubory skriptu. Tento nástroj používá rozhraní ODBC ke spouštění dávek Transact-SQL.

  Další informace najdete v tématu [připojení pomocí nástroje Sqlcmd](/sql/connect/odbc/linux-mac/connecting-with-sqlcmd).

  > [!NOTE]
  > Mezi platformami Linux a Windows jsou rozdíly v tomto nástroji. Podrobnosti najdete v dokumentaci.

### <a name="database-access-libraries"></a>Knihovny pro přístup k databázi

Knihovny jsou k dispozici v R a Pythonu pro přístup k databázi:

* V R můžete k dotazování nebo spouštění příkazů SQL na databázovém serveru použít balíček RODBC nebo balíček dplyr.
* V Pythonu poskytuje knihovna pyodbc přístup k databázi s rozhraním ODBC jako podkladovou vrstvu.  

## <a name="azure-tools"></a>Nástroje Azure

Na virtuálním počítači jsou nainstalované následující nástroje Azure:

* **Azure CLI**: rozhraní příkazového řádku v Azure můžete použít k vytváření a správě prostředků Azure pomocí příkazů prostředí. Pokud chcete otevřít nástroje Azure, zadejte **Azure Help**. Další informace najdete na [stránce dokumentace k Azure CLI](/cli/azure/get-started-with-az-cli2).
* **Průzkumník služby Azure Storage**: Průzkumník služby Azure Storage je grafický nástroj, pomocí kterého můžete procházet objekty, které jste uložili v účtu úložiště Azure, a nahrávat a stahovat data do a z objektů blob Azure. K Průzkumník služby Storage můžete přistupovat pomocí ikony zástupce na ploše. Můžete ho také otevřít z příkazového řádku prostředí zadáním **StorageExplorer**. Musíte být přihlášeni z klienta X2Go nebo mít nastavené přesměrování X11.
* **Knihovny Azure**: Níže jsou uvedené některé z předem nainstalovaných knihoven.
  
  * **Python**: knihovny související s Azure v Pythonu jsou *Azure*, *AzureML*, *pydocumentdb* a *pyodbc*. Pomocí prvních tří knihoven můžete přistupovat ke službám Azure Storage, Azure Machine Learning a Azure Cosmos DB (databáze NoSQL v Azure). Čtvrtá knihovna pyodbc (spolu s ovladačem Microsoft ODBC Driver for SQL Server) umožňuje přístup k SQL Server, Azure SQL Database a Azure synapse Analytics z Pythonu pomocí rozhraní ODBC. Zadáním **seznamu PIP** zobrazíte všechny uvedené knihovny. Nezapomeňte spustit tento příkaz v prostředí Python 2,7 a 3,5.
  * **R**: knihovny související s Azure v jazyce R jsou AZUREML a RODBC.
  * **Java**: seznam knihoven Azure Java najdete v adresáři/DSVM/SDK/AZURESDKJAVA na virtuálním počítači. Knihovny klíčů jsou služby Azure Storage a API pro správu, Azure Cosmos DB a ovladače JDBC pro SQL Server.  

K [Azure Portal](https://portal.azure.com) můžete přistupovat z předinstalovaného prohlížeče Firefox. Na Azure Portal můžete vytvářet, spravovat a monitorovat prostředky Azure.

## <a name="azure-machine-learning"></a>Azure Machine Learning

Azure Machine Learning je plně spravovaná cloudová služba, která umožňuje sestavovat, nasazovat a sdílet řešení prediktivní analýzy. Můžete vytvořit experimenty a modely v aplikaci Azure Machine Learning Studio (Preview). K němu můžete přistupovat z webového prohlížeče na Data Science Virtual Machine tím, že navštívíte [Microsoft Azure Machine Learning](https://ml.azure.com).

Až se přihlásíte k Azure Machine Learning studiu, můžete k vytvoření logického toku pro algoritmy strojového učení použít plátno experimentování. Máte také přístup k poznámkovým blokům Jupyter hostovanému na Azure Machine Learning a můžete bez problémů pracovat s experimenty v Azure Machine Learning Studiu. 

Zprovoznění modely strojového učení, které jste vytvořili, jejich zabalením do rozhraní webové služby. Modely strojového učení zprovozňování umožňují klientům napsaným v jakémkoli jazyce vyvolat předpovědi z těchto modelů. Další informace najdete v dokumentaci k [Machine Learning](https://azure.microsoft.com/documentation/services/machine-learning/).

Můžete také sestavit modely v R nebo Pythonu na virtuálním počítači a pak je nasadit v produkčním prostředí na Azure Machine Learning. Pro povolení této funkce jsme nainstalovali knihovny v R (**AzureML**) a Pythonu (**AzureML**).

> [!NOTE]
> Tyto pokyny byly napsány pro verzi Data Science Virtual Machine Windows. Informace, které jsou k dispozici na nasazení modelů pro Azure Machine Learning, se vztahují i na virtuální počítač se systémem Linux.

## <a name="machine-learning-tools"></a>Nástroje pro strojové učení

Tento virtuální počítač přináší nástroje Machine Learning a algoritmy, které byly předem kompilovány a předinstalované místně. Tady jsou některé z nich:

* **Pro dostupné**: rychlý online algoritmus pro učení.
* **xgboost**: nástroj, který poskytuje optimalizované a zesílené stromové algoritmy.
* **Rattle**: grafický nástroj založený na jazyce R pro snadné zkoumání a modelování dat.
* **Python**: Anaconda Python přichází do sady s algoritmy strojového učení s knihovnami, jako je Scikit – učení. Další knihovny můžete nainstalovat pomocí `pip install` příkazu.
* **LightGBM**: rychlé, distribuované a vysoce výkonné přechodové prostředí s vysokým výkonem na základě algoritmů rozhodovacího stromu.
* **R**: rozsáhlá knihovna funkcí strojového učení je k dispozici pro R. předem nainstalované knihovny zahrnují LM, GLM, randomForest a rpart. Další knihovny můžete nainstalovat spuštěním tohoto příkazu:

    ```r
    install.packages(<lib name>)
    ```

Tady je několik dalších informací o prvních třech nástrojích strojového učení v seznamu.

### <a name="vowpal-wabbit"></a>Vowpal Wabbit

Pro dostupné je systém strojového učení, který využívá techniky, jako je online, hashing, allreduce, snižování, learning2search, aktivní a interaktivní učení.

Chcete-li spustit nástroj na základním příkladu, použijte následující příkazy:

```bash
cp -r /dsvm/tools/VowpalWabbit/demo vwdemo
cd vwdemo
vw house_dataset
```

V tomto adresáři jsou jiné, větší ukázky. Další informace o pro dostupné najdete v  [této části GitHubu](https://github.com/JohnLangford/vowpal_wabbit) a na [wikiwebu pro dostupné](https://github.com/JohnLangford/vowpal_wabbit/wiki).

### <a name="xgboost"></a>xgboost

Knihovna xgboost je navržená a optimalizovaná pro zvýšení (stromové) algoritmy. Cílem této knihovny je nabízet omezení výpočtů počítačů až k extrémním hodnotám, které jsou potřeba k zajištění vysokého zvyšování stromové struktury, které jsou škálovatelné, přenosné a přesné.

Je k dispozici jako příkazový řádek a knihovna R. Chcete-li použít tuto knihovnu v jazyce R, můžete spustit interaktivní relaci jazyka R (zadáním **R** v prostředí) a načíst knihovnu.

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

Pokud chcete spustit příkazový řádek xgboost, najdete tady příkazy, které se mají spustit v prostředí:

```bash
cp -r /dsvm/tools/xgboost/demo/binary_classification/ xgboostdemo
cd xgboostdemo
xgboost mushroom.conf
```

Soubor. model je zapsán do zadaného adresáře. Informace o tomto ukázkovém příkladu najdete [na GitHubu](https://github.com/dmlc/xgboost/tree/master/demo/CLI/binary_classification).

Další informace o xgboost najdete na [stránce dokumentace xgboost](https://xgboost.readthedocs.org/en/latest/) a v jejím [úložišti GitHub](https://github.com/dmlc/xgboost).

### <a name="rattle"></a>Rattle

Rattle ( **R** **a** nalytická **T** OOL **t** o **L** získat **E** asily) používá zkoumání a modelování dat na základě grafického uživatelského rozhraní. Představuje statistické a vizuální souhrny dat, transformuje data, která je možné snadno modelovat a sestavovat v nich jak modely pod dohledem, tak i pod dohledem, prezentují výkon modelů a vychází z nových datových sad. Také generuje kód R, který replikuje operace v uživatelském rozhraní, které lze spustit přímo v R nebo použít jako výchozí bod pro další analýzu.

Pokud chcete spustit Rattle, musíte být v přihlašovací relaci grafického klienta. V terminálu zadejte **r** a otevřete prostředí r. Na příkazovém řádku R zadejte následující příkazy:

```R
library(rattle)
rattle()
```

Nyní se otevře grafické rozhraní se sadou karet. Pomocí následujících kroků rychlého startu v Rattle můžete použít ukázku sady dat počasí a vytvořit model. V některých krocích se zobrazí výzva, abyste automaticky nainstalovali a načetli některé požadované balíčky R, které ještě nejsou v systému.

> [!NOTE]
> Pokud nemáte přístup k instalaci balíčku do systémového adresáře (výchozí nastavení), může se zobrazit výzva v okně konzoly R, kde můžete balíčky nainstalovat do vaší osobní knihovny. Pokud se zobrazí tyto výzvy, odpovězte na **y** .

1. Vyberte **Execute** (Provést).
1. Zobrazí se dialogové okno s dotazem, zda chcete použít ukázkovou sadu dat počasí. Vyberte **Ano** , pokud chcete příklad načíst.
1. Vyberte kartu **model** .
1. Vyberte **provést** a sestavte rozhodovací strom.
1. Vyberte možnost **Kreslení** pro zobrazení rozhodovacího stromu.
1. Vyberte možnost **doménová struktura** a vyberte **Spustit** pro vytvoření náhodné doménové struktury.
1. Vyberte kartu **vyhodnotit** .
1. Vyberte možnost **rizika** a výběrem možnosti **Spustit** zobrazíte zobrazení se dvěma **riziky (kumulativní)** výkon.
1. Vyberte kartu **protokol** , chcete-li zobrazit generovaný kód R pro předchozí operace.
   (Kvůli chybě v aktuální verzi Rattle je nutné **#** před **exportem tohoto protokolu** do textu protokolu vložit znak.)
1. Vyberte tlačítko **exportovat** a uložte soubor skriptu R s názvem *weather_script. R* do domovské složky.

Můžete ukončit Rattle a R. Nyní můžete upravit generovaný skript R. Nebo použijte skript jako takový a spusťte ho kdykoli, abyste mohli opakovat všechno, co bylo provedeno v uživatelském rozhraní Rattle. Hlavně pro začátečníky v R je to způsob, jak rychle analyzovat a strojové učení v jednoduchém grafickém rozhraní, a to při automatickém generování kódu v jazyce R, abyste mohli upravovat nebo učit.

## <a name="next-steps"></a>Další kroky

Máte ještě nějaké otázky? Zvažte vytvoření [lístku podpory](https://azure.microsoft.com/support/create-ticket/).