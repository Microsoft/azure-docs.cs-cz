---
title: 'Další informace: DSVM s Ubuntu'
description: Podrobnosti o nástrojích, které jsou součástí Data Science Virtual Machine Ubuntu
author: gvashishtha
ms.service: machine-learning
ms.subservice: data-science-vm
ms.author: gopalv
ms.date: 09/11/2019
ms.topic: reference
ms.openlocfilehash: 0c48b8ba28f67ac7f38d6f636728abe7c5a6bd4e
ms.sourcegitcommit: 8a717170b04df64bd1ddd521e899ac7749627350
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/23/2019
ms.locfileid: "71204032"
---
# <a name="reference-ubuntu-linux-data-science-virtual-machine"></a>Další informace: Ubuntu (Linux) Data Science Virtual Machine

Níže najdete seznam dostupných nástrojů Ubuntu Data Science Virtual Machine. 

## <a name="deep-learning-libraries"></a>Knihovny pro hloubkové učení

### <a name="cntk"></a>CNTK

Microsoft Cognitive Toolkit je open source sada nástrojů pro hloubkové učení. Python vazeb jsou k dispozici v kořenové certifikáty a py35 prostředí Conda. Má také nástroj příkazového řádku (CNTK), který je již v cestě.

Ukázkové poznámkové bloky Python jsou k dispozici v JupyterHub. Pokud chcete spustit základní ukázku z příkazového řádku, spusťte v prostředí následující příkazy:

```bash
cd /home/[USERNAME]/notebooks/CNTK/HelloWorld-LogisticRegression
cntk configFile=lr_bs.cntk makeMode=false command=Train
```

Další informace najdete v části CNTK na [GitHubu](https://github.com/Microsoft/CNTK) a na [wikiwebu CNTK](https://github.com/Microsoft/CNTK/wiki).

### <a name="caffe"></a>Caffe

Caffe je rozhraní hloubkového učení z Berkeley vize a výukové centrum. Je k dispozici v/opt/Caffe. Příklady najdete v/opt/Caffe/Examples.

### <a name="caffe2"></a>Caffe2

Caffe2 je rozhraní hloubkového učení ze sítě Facebook, která je založená na Caffe. Je k dispozici v Pythonu 2,7 v kořenovém prostředí conda. Pokud ho chcete aktivovat, spusťte následující příkaz z prostředí:

```bash
source /anaconda/bin/activate root
```

Jsou k dispozici v JupyterHub některé příklad poznámkových bloků.

### <a name="h2o"></a>H2O

H2O je rychlé, v paměti a distribuované machine learning a prediktivní analytická platforma. Balíček Python je nainstalována v kořenových a py35 Anaconda prostředí. Balíček R je také nainstalována. 

Chcete-li otevřít z příkazového řádku, `java -jar /dsvm/tools/h2o/current/h2o.jar`spusťte příkaz. K dispozici jsou různé [Možnosti příkazového řádku](http://docs.h2o.ai/h2o/latest-stable/h2o-docs/starting-h2o.html#from-the-command-line) , které byste mohli chtít nakonfigurovat. Chcete http://localhost:54321 -li začít, můžete získat přístup k webovému uživatelskému rozhraní toku. Ukázkové poznámkové bloky jsou dostupné v JupyterHub.

### <a name="keras"></a>Keras

Keras je neuronové síťové rozhraní API v Pythonu vysoké úrovně. Může běžet nad TensorFlow, Microsoft Cognitive Toolkit nebo Theano. Je k dispozici v prostředích root a py35 Pythonu.

### <a name="mxnet"></a>MXNet

MXNet je navržené pro efektivity a pružnosti rozhraní hloubkového učení. Má vazby R a Python na datové VĚDY. Ukázkové poznámkové bloky jsou součástí JupyterHub a ukázkový kód je k dispozici v /dsvm/samples/mxnet.

### <a name="nvidia-digits"></a>NVIDIA ČÍSLIC

Školicí systém NVIDIA hloubkového učení GPU, označovaný jako číslice, představuje systém, který zjednodušuje běžné úlohy s hloubkovým učením. Mezi tyto úlohy patří Správa dat, navrhování a školení neuronové sítí na systémech GPU a monitorování výkonu v reálném čase s pokročilou vizualizací.

ČÍSLICE jsou k dispozici jako služba s názvem *číslice*. Spusťte službu a přejděte do http://localhost:5000 začít.

ČÍSLICE se nainstaluje také jako modul Pythonu v prostředí Conda kořenové.

### <a name="tensorflow"></a>TensorFlow

TensorFlow je knihovna obsáhlého learningu od Googlu. Je to open source softwarová knihovna pro numerické výpočty pomocí grafů toku dat. Je k dispozici v prostředí Python py35 TensorFlow, a některé ukázkové poznámkové bloky jsou součástí JupyterHub.

### <a name="theano"></a>Theano

Theano je knihovna Python pro efektivní numerické výpočty. Je k dispozici v prostředích root a py35 Pythonu. 

### <a name="torch"></a>Torch

Svítilnou je vědecké výpočetní architektura díky široké podpoře algoritmů strojového učení. Je k dispozici v/dsvm/Tools/Torch **a v příkazovém** řádku jsou k dispozici interaktivní relace a správce balíčků LuaRocks. Příklady jsou k dispozici v /dsvm/samples/torch.

PyTorch dočíst i v prostředí Anaconda root. Příklady v /dsvm/samples/pytorch.

## <a name="microsoft-machine-learning-server"></a>Server Microsoft Machine Learning

R je jedním z nejoblíbenějších jazyků pro analýzy dat a strojové učení. Pokud pro analýzu chcete použít R, virtuální počítač se Microsoft Machine Learning Server s otevřenou a pomocí knihovny Microsoft R Open a matematického jádra. Knihovna Math kernel optimalizuje matematické operace běžné v analytických algoritmech. Microsoft R Open je 100 procent kompatibilního s CRAN R a kterákoli z knihoven R publikovaných v CRAN se dá nainstalovat do Microsoft R Open. 

Machine Learning Server poskytuje škálování a provoz modelů R na webové služby. Můžete upravit sady R v jednom z výchozí editorů, jako je RStudio, editoru vi nebo (emacs). Pokud dáváte přednost použití editoru Emacs, byl předem nainstalované. Balíček (Emacs) ESS ((Emacs) mluví STATISTICS) usnadňuje práci se soubory R v editoru (Emacs).

Pokud chcete otevřít konzolu R, zadáte **R** do prostředí. Tento příkaz vás provede interaktivním prostředím. K vývoji programu R obvykle používáte editor jako (Emacs) nebo VI a pak spouštíte skripty v jazyce R. Pomocí RStudio máte k dispozici kompletní grafické rozhraní IDE pro vývoj programu R.

K dispozici je také skript R, který vám umožní nainstalovat [horní 20 balíčků r](https://www.kdnuggets.com/2015/06/top-20-r-packages.html) , pokud chcete. Tento skript můžete spustit potom, co jste v interaktivním rozhraní R. Jak bylo zmíněno dříve, můžete toto rozhraní otevřít zadáním **R** do prostředí.  

## <a name="python"></a>Python

Anaconda Python se instaluje s 3,5 prostředí a použije se Python 2.7. 2\.7 prostředí se nazývá _kořenové_, a 3.5 prostředí se nazývá _py35_. Toto rozdělení obsahuje základní Python spolu s přibližně 300 nejoblíbenějších balíčků analytics matematické, inženýrství a data.

Výchozím nastavením je py35 prostředí. Pokud chcete aktivovat kořenové prostředí (2,7), použijte tento příkaz:

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

Nebo zadat úplnou cestu k nástroje pip:

```bash
/anaconda/bin/pip install <package>
```

V případě conda byste měli vždy zadat název prostředí (py35 nebo root):

```bash
conda install <package> -n py35
```

Pokud používáte grafické rozhraní nebo máte nastavené předávání X11, můžete zadat **PyCharm** a otevřít PYCHARM Python IDE. Můžete použít výchozí textových editorů. Kromě toho můžete použít Spyder, integrované vývojové prostředí (IDE), které je součástí distribuce Anaconda Pythonu. Spyder potřebuje grafické stolní počítač nebo X11 předávání. Grafická plocha má zástupce pro Spyder.

## <a name="jupyter-notebook"></a>Jupyter Notebook

Distribuce Anacondy také součástí poznámkového bloku Jupyter, prostředí pro sdílení kódu a analýzy. Poznámkový blok Jupyter je přístupný prostřednictvím JupyterHub. Přihlašujete se pomocí uživatelského jména a hesla místního systému Linux.

Server poznámkového bloku Jupyter předem nakonfigurovaný s Python 2, Python 3 a R jádra. Pomocí ikony **Jupyter notebook** plocha otevřete prohlížeč a přejděte na server poznámkového bloku. Pokud se nacházíte na virtuálním počítači přes SSH nebo klienta X2Go, můžete k serveru Jupyter notebook získat přístup také [https://localhost:8000/](https://localhost:8000/)na adrese.

> [!NOTE]
> Pokračujte, pokud chcete získat všechna upozornění certifikátu.

Server poznámkového bloku Jupyter můžete přistupovat z libovolného hostitele. Zadejte **název\<DNS nebo IP adresu\>https://virtuálního počítače: 8000/** .

> [!NOTE]
> Port 8000 je otevřen v bráně firewall ve výchozím nastavení při zřízení virtuálního počítače. 

Máme balíčky ukázkových poznámkových bloků – jeden v Pythonu a druhý v R. Odkaz na ukázky na domovské stránce poznámkového bloku se zobrazí po ověření do poznámkového bloku Jupyter pomocí místního uživatelského jména a hesla systému Linux. Nový Poznámkový blok můžete vytvořit tak, že vyberete **nové**a pak vyberete příslušné jádro jazyka. Pokud tlačítko **Nový** nevidíte, vyberte v levém horním rohu ikonu **Jupyter** a přejděte na domovskou stránku serveru poznámkového bloku.

## <a name="apache-spark-standalone"></a>Apache Spark samostatný

Samostatná instance Apache Spark je předem nainstalovanou na platformě Linux DSVM, která vám umožní místní vývoj aplikací Spark ještě předtím, než je otestujete a nasadíte do velkých clusterů. 

Jádra aplikace Jupyter můžete si projít programy PySpark. Když otevřete Jupyter, vyberte tlačítko **Nový** a měl by se zobrazit seznam dostupných jader. **Spark-Python** je jádrem PySpark, který umožňuje sestavovat aplikace Spark pomocí jazyka Python. K sestavování programu Spark můžete použít také Python IDE, jako je PyCharm nebo Spyder. 

V této samostatné instanci se sada Spark spouští v rámci volajícího klientského programu. Tato funkce usnadňuje a usnadňuje řešení problémů v porovnání s vývojem v clusteru Spark.

Jupyter poskytuje ukázkový Poznámkový blok PySpark. Můžete ji najít v adresáři SparkML v domovském adresáři Jupyter ($HOME/notebooks/SparkML/pySpark). 

Pokud programujete v R pro Spark, můžete použít Microsoft Machine Learning Server, Spark nebo sparklyr. 

Před spuštěním v kontextu Sparku v Microsoft Machine Learning Server je třeba provést jednorázový krok nastavení, který povolí místní instanci Hadoop s jedním uzlem a instanci příz. Ve výchozím nastavení služby Hadoop jsou nainstalované ale deaktivovány datové VĚDY. Pokud ho chcete povolit, musíte poprvé spustit následující příkazy jako root:

```bash
echo -e 'y\n' | ssh-keygen -t rsa -P '' -f ~hadoop/.ssh/id_rsa
cat ~hadoop/.ssh/id_rsa.pub >> ~hadoop/.ssh/authorized_keys
chmod 0600 ~hadoop/.ssh/authorized_keys
chown hadoop:hadoop ~hadoop/.ssh/id_rsa
chown hadoop:hadoop ~hadoop/.ssh/id_rsa.pub
chown hadoop:hadoop ~hadoop/.ssh/authorized_keys
systemctl start hadoop-namenode hadoop-datanode hadoop-yarn
```

Služby související se systémem Hadoop můžete zastavit, pokud je nepotřebujete spuštěním ```systemctl stop hadoop-namenode hadoop-datanode hadoop-yarn```.

Adresář/dsvm/samples/MRS poskytuje ukázku, která ukazuje, jak vyvíjet a testovat Microsoft Machine Learning Server ve vzdáleném kontextu Spark (samostatná instance Sparku na DSVM).

## <a name="ides-and-editors"></a>Editory a integrovanými vývojovými prostředími

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

Před prvním použitím nastavení ovladačů a aliasy databáze. Ovladače JDBC jsou umístěné na adrese/usr/share/Java/jdbcdrivers.

Další informace najdete v tématu [SQuirrel SQL](http://squirrel-sql.sourceforge.net/index.php?page=screenshots).

### <a name="command-line-tools-for-accessing-microsoft-sql-server"></a>Nástroje příkazového řádku pro přístup k systému Microsoft SQL Server

Balíček ovladače ODBC pro SQL Server také obsahuje dva nástroje příkazového řádku:

- **bcp**: Nástroj BCP hromadně kopíruje data mezi instancí Microsoft SQL Server a datovým souborem v uživatelsky definovaném formátu. Pomocí nástroje BCP můžete importovat velký počet nových řádků do SQL Server tabulky nebo exportovat data z tabulek do datových souborů. Chcete-li importovat data do tabulky, je nutné použít formátový soubor vytvořený pro tuto tabulku. Nebo, musíte pochopit strukturu tabulky a typy dat, které jsou pro své sloupce platné.

  Další informace najdete v tématu [připojení pomocí bcp](https://msdn.microsoft.com/library/hh568446.aspx).

- **sqlcmd**: Příkazy jazyka Transact-SQL můžete zadat pomocí nástroje Sqlcmd. Do příkazového řádku můžete také zadat systémové procedury a soubory skriptu. Tento nástroj používá rozhraní ODBC ke spouštění dávek Transact-SQL.

  Další informace najdete v tématu [připojení pomocí sqlcmd](https://msdn.microsoft.com/library/hh568447.aspx).

  > [!NOTE]
  > Mezi platformami Linux a Windows jsou rozdíly v tomto nástroji. Viz podrobnosti naleznete v dokumentaci.

### <a name="database-access-libraries"></a>Přístup ke knihovnám databáze

Knihovny jsou k dispozici v R a Pythonu pro přístup k databázi:

* V R můžete k dotazování nebo spouštění příkazů SQL na databázovém serveru použít balíček RODBC nebo balíček dplyr.
* V Pythonu poskytuje knihovna pyodbc přístup k databázi s rozhraním ODBC jako podkladovou vrstvu.  

## <a name="azure-tools"></a>Nástroje Azure

Na virtuálním počítači jsou nainstalované nástroje Azure následující:

* **Azure CLI**: Pomocí rozhraní příkazového řádku v Azure můžete vytvářet a spravovat prostředky Azure prostřednictvím příkazů prostředí. Pokud chcete otevřít nástroje Azure, zadejte **Azure Help**. Další informace najdete v tématu [stránku dokumentace k Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-az-cli2).
* **Průzkumník služby Azure Storage**: Průzkumník služby Azure Storage je grafický nástroj, pomocí kterého můžete procházet objekty, které jste uložili v účtu úložiště Azure, a nahrávat a stahovat data do a z objektů blob Azure. Průzkumník služby Storage se dá dostat z ikonu zástupce na ploše. Můžete ho také otevřít z příkazového řádku prostředí zadáním **StorageExplorer**. Musíte být přihlášeni z klienta X2Go nebo mít nastavené přesměrování X11.
* **Knihovny Azure**: Níže jsou uvedené některé z předem nainstalovaných knihoven.
  
  * **Python**: Knihovny související s Azure v Pythonu jsou *Azure*, *AzureML*, *pydocumentdb*a *pyodbc*. První tři knihovny můžete přístup služby Azure storage, Azure Machine Learning a Azure Cosmos DB (databáze NoSQL v Azure). Čtvrtý knihovny pyodbc (spolu s ovladač Microsoft ODBC pro SQL Server), umožňuje přístup k systému SQL Server, Azure SQL Database a Azure SQL Data Warehouse z Pythonu pomocí rozhraní ODBC. Zadejte **pip seznamu** zobrazíte všechny uvedené knihovny. Ujistěte se, že tento příkaz spustit v případě Pythonu 2.7 i 3.5 prostředí.
  * **R**: Knihovny související s Azure v jazyce R jsou AzureML a RODBC.
  * **Java**: Seznam knihoven Azure Java najdete v adresáři/dsvm/sdk/AzureSDKJava na virtuálním počítači. Jsou klíčové knihovny Azure ovladače úložiště a správu rozhraní API služby Azure Cosmos DB a JDBC pro SQL Server.  

Můžete přistupovat [webu Azure portal](https://portal.azure.com) předinstalované prohlížeče Firefox. Na portálu Azure portal můžete vytvářet, spravovat a monitorovat prostředky Azure.

## <a name="azure-machine-learning"></a>Azure Machine Learning

Azure Machine Learning je plně spravovaná Cloudová služba, která umožňuje vytvořit, nasadit a sdílení řešení prediktivní analýzy. Vytváření experimentů a modelů z Azure Machine Learning Studio. K němu můžete přistupovat z webového prohlížeče na Data Science Virtual Machine tím, že navštívíte [Microsoft Azure Machine Learning](https://studio.azureml.net).

Po přihlášení k Azure Machine Learning Studio můžete k vytvoření logického toku pro algoritmy strojového učení použít plátno experimentování. Máte také přístup k poznámkovým blokům Jupyter hostovanému na Azure Machine Learning a můžete bez problémů pracovat s experimenty v Machine Learning Studio. 

Zprovoznění modelů strojového učení, které jste vytvořili podle jejich zabalení v rozhraní webových služeb. Modely strojového učení zprovozňování umožňují klientům napsaným v jakémkoli jazyce vyvolat předpovědi z těchto modelů. Další informace najdete v tématu [dokumentace ke službě Machine Learning](https://azure.microsoft.com/documentation/services/machine-learning/).

Můžete také sestavit modely v R nebo Pythonu na virtuálním počítači a pak je nasadit v produkčním prostředí na Azure Machine Learning. Jsme nainstalovali knihovny v jazyce R (**AzureML**) a Python (**azureml**) Chcete-li povolit tuto funkci.

Informace o tom, jak nasadit modely v R a Pythonu do Azure Machine Learning, najdete v článku [o deseti akcích, které můžete na data Science Virtual Machine dělat](vm-do-ten-things.md).

> [!NOTE]
> Tyto pokyny byly napsány pro verzi Data Science Virtual Machine Windows. Ale uvedené informace o nasazení modelů Azure Machine Learning je pro virtuální počítač s Linuxem.

## <a name="machine-learning-tools"></a>Nástroje Machine learning

Tento virtuální počítač přináší nástroje Machine Learning a algoritmy, které byly předem kompilovány a předinstalované místně. Mezi ně patří:

* **Pro dostupné**: Rychlý online algoritmus pro učení.
* **xgboost**: Nástroj, který poskytuje optimalizované a posílené doménové algoritmy.
* **Rattle**: Grafický nástroj založený na jazyce R pro snadné zkoumání a modelování dat.
* **Python**: Anaconda Python se dodává s algoritmy strojového učení s knihovnami, jako je Scikit – učení. Můžete nainstalovat další knihovny pomocí `pip install` příkazu.
* **LightGBM**: Rychlé a distribuované barevné přechodové rozhraní s vysokým výkonem na základě algoritmů rozhodovacího stromu.
* **R**: K dispozici je bohatá knihovna funkcí strojového učení pro R. předem nainstalované knihovny zahrnují LM, GLM, randomForest a rpart. Další knihovny můžete nainstalovat spuštěním tohoto příkazu:
  
        install.packages(<lib name>)

Zde jsou některé další informace o nástrojích pro první tři strojového učení v seznamu.

### <a name="vowpal-wabbit"></a>Vowpal Wabbit

Vowpal Wabbit je strojové učení systém, který používá techniky, jako je online, hash, allreduce, snížení, learning2search, aktivní a interaktivního vzdělávacího.

Chcete-li spustit nástroj na základním příkladu, použijte následující příkazy:

```bash
cp -r /dsvm/tools/VowpalWabbit/demo vwdemo
cd vwdemo
vw house_dataset
```

Existují jiné, větší ukázky v tomto adresáři. Další informace o pro dostupné najdete v [této části GitHubu](https://github.com/JohnLangford/vowpal_wabbit) a na [wikiwebu pro dostupné](https://github.com/JohnLangford/vowpal_wabbit/wiki).

### <a name="xgboost"></a>xgboost

Knihovna xgboost je navržená a optimalizovaná pro zvýšení (stromové) algoritmy. Cílem této knihovny je tak, aby nabízel výpočet omezení počítačů extrémy potřebných k poskytování rozsáhlé stromové struktury zvýšení skóre, která je škálovatelná, přenosných a přesné.

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

Soubor. model je zapsán do zadaného adresáře. Informace o tomto ukázkovém příkladu najdete [na GitHubu](https://github.com/dmlc/xgboost/tree/master/demo/binary_classification).

Další informace o xgboost najdete na [stránce dokumentace xgboost](https://xgboost.readthedocs.org/en/latest/) a v jejím [úložišti GitHub](https://github.com/dmlc/xgboost).

### <a name="rattle"></a>Rattle

Rattle ( **R** **A**nalytická **T**rukopis **T**o **L**získat **E** asily) používá zkoumání a modelování dat na základě grafického uživatelského rozhraní. Zobrazí statistické a vizuální souhrny dat, transformace dat, která lze snadno modelovat, sestavení bez dohledu a pod dohledem modely z dat, uvede výkon modelů graficky, a nastaví skóre, které se nová data. Také vygeneruje kód R, replikaci operace v uživatelském rozhraní, které můžete spustit přímo v R nebo použít jako výchozí bod pro další analýzu.

Pokud chcete spustit Rattle, musíte být v grafickém přihlašovací relaci plochy. V terminálu zadejte **r** a otevřete prostředí r. Na řádku R zadejte následující příkazy:

```R
library(rattle)
rattle()
```

Nyní se otevře grafické rozhraní se sadou karet. Pomocí následujících kroků rychlého startu v Rattle můžete použít ukázku sady dat počasí a vytvořit model. V některých krocích se zobrazí výzva, abyste automaticky nainstalovali a načetli některé požadované balíčky R, které ještě nejsou v systému.

> [!NOTE]
> Pokud nemáte přístup k instalaci balíčku do systémového adresáře (výchozí nastavení), může se zobrazit výzva v okně konzoly R, kde můžete balíčky nainstalovat do vaší osobní knihovny. Odpověď **y** -li zobrazit tyto výzvy.

1. Vyberte **Provést**.
1. Zobrazí se dialogové okno s dotazem, zda chcete použít ukázkovou sadu dat počasí. Vyberte **Ano** , pokud chcete příklad načíst.
1. Vyberte kartu **model** .
1. Vyberte **provést** a sestavte rozhodovací strom.
1. Vyberte možnost **Kreslení** pro zobrazení rozhodovacího stromu.
1. Vyberte možnost **doménová struktura** a vyberte **Spustit** pro vytvoření náhodné doménové struktury.
1. Vyberte kartu **vyhodnotit** .
1. Vyberte možnost **rizika** a výběrem možnosti **Spustit** zobrazíte zobrazení se dvěma **riziky (kumulativní)** výkon.
1. Vyberte kartu **protokol** , chcete-li zobrazit generovaný kód R pro předchozí operace.
   (Kvůli chybě v aktuální verzi Rattle je nutné před **exportem tohoto protokolu** do textu protokolu **#** vložit znak.)
1. Vyberte tlačítko **exportovat** a uložte soubor skriptu jazyka R s názvem *weather_script. R* do domovské složky.

Můžete ukončit Rattle a R. Nyní můžete upravit generovaný skript R. Nebo použijte skript jako takový a spusťte ho kdykoli, abyste mohli opakovat všechno, co bylo provedeno v uživatelském rozhraní Rattle. Hlavně pro začátečníky v R je to způsob, jak rychle analyzovat a strojové učení v jednoduchém grafickém rozhraní, a to při automatickém generování kódu v jazyce R, abyste mohli upravovat nebo učit.

## <a name="next-steps"></a>Další kroky

Máte další otázky? Zvažte vytvoření [lístku podpory](https://azure.microsoft.com/support/create-ticket/).