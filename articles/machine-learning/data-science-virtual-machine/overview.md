---
title: Úvod do služby Azure Data vědecké účely virtuálního počítače pro systémy Linux a Windows | Microsoft Docs
description: Scénáře klíče analýzu a součásti pro Windows a Linux datové vědy virtuální počítače.
keywords: datové vědy nástroje, datové vědy virtuálního počítače, nástroje pro vědecké zpracování dat, vědecké zpracování dat linux
services: machine-learning
documentationcenter: ''
author: gopitk
manager: cgronlun
editor: cgronlun
ms.assetid: d4f91270-dbd2-4290-ab2b-b7bfad0b2703
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/27/2017
ms.author: gokuma
ms.openlocfilehash: 8ee4af162ddaa64d4dbe83bebbb93e22409f041d
ms.sourcegitcommit: c3d53d8901622f93efcd13a31863161019325216
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2018
---
# <a name="introduction-to-azure-data-science-virtual-machine-for-linux-and-windows"></a>Úvod do služby Azure Data vědecké účely virtuálního počítače pro systémy Linux a Windows

Virtuální počítač vědecké účely dat (DSVM) je přizpůsobená image virtuálního počítače v cloudu společnosti Microsoft Azure vytvořené speciálně pro provádění vědecké zpracování dat. Obsahuje řadu oblíbených předinstalovaných nástrojů datové vědy a dalších funkcí a je předem nakonfigurovaná, abyste mohli hned začít sestavovat inteligentní aplikace pro pokročilou analýzu. Je k dispozici pro Windows Server a Linux. Edici virtuálního počítače pro datové vědy pro Windows nabízíme ve verzích pro Server 2016 a Server 2012. Nabízíme verzích DSVM na Ubuntu 16.04 LTS a CentOS 7.4 systému Linux.

Toto téma popisuje, co můžete dělat s virtuálním Počítačem vědecké účely Data, popisuje některé z klíčových scénářů pro použití virtuálního počítače, rozepisuje klíčové funkce, které jsou k dispozici ve verzích systému Windows a Linux a obsahuje pokyny, jak začít pracovat, jejich používání.


## <a name="what-can-i-do-with-the-data-science-virtual-machine"></a>Jak se Data virtuálního počítače vědecké účely?
Cílem z virtuálního počítače datové vědy (DSVM) je poskytnout Odborníci v oblasti dat na všech úrovních dovedností a ve všech rolích prostředí vědecké účely dat bez tření, předem nakonfigurovaná a plně integrovaná. Místo zavedením porovnatelný z hlediska prostoru sami, můžete zřídit DSVM - ušetřit dny nebo i _týdny_ na instalaci, konfiguraci a procesy správy balíčku. Po přidělení vaší DSVM můžete okamžitě začít pracovat na projektu vědecké účely data.

Virtuální počítač datové vědy a konfigurují pro práci s širokou škálu scénářů použití. Prostředí můžete škálovat nahoru nebo dolů podle projektu změn. Budete moci použít upřednostňovaný jazyk pro úlohy vědecké účely dat programu. Můžete nainstalovat další nástroje a přizpůsobení systému pro vašim konkrétním potřebám.

## <a name="key-scenarios"></a>Klíčové scénáře
Tato část navrhuje některých klíčových scénářů, pro které se dá nasadit virtuální počítač vědecké účely Data.

### <a name="preconfigured-analytics-desktop-in-the-cloud"></a>Plocha předkonfigurované analýzy v cloudu
Virtuální počítač vědecké účely dat poskytuje standardních hodnot konfigurace pro data vědecké účely týmům nahraďte plocha spravovaný cloudu jejich místních počítačů. Tyto standardní hodnoty zajistí, že všechny datových vědců na tým instalace, ke které má ověřte experimenty a zlepšení spolupráce s konzistentní. Také snižuje náklady na snížením zatížení správce systému a ukládání na čas potřebný k vyhodnocení, instalaci a údržbě různé softwarové balíčky, které jsou potřebné k provádět pokročilé analýzy.  

### <a name="data-science-training-and-education"></a>Školení a vzdělávání v oblasti datové vědy
Enterprise školitelů a lektorům který naučit vědecké zpracování dat třídy obvykle nabízejí bitovou kopii virtuálního počítače zajistit, že jejich studenti, kteří mají konzistentní instalační program a ukázky fungovat předvídatelné. Virtuální počítač vědecké účely dat vytvoří prostředí na vyžádání s konzistentní instalačního programu, která usnadňuje podporu a nekompatibilita problémů. Případy, kdy musela být vytvořená často, hlavně pro kratší školení třídy těchto prostředích využívat podstatně.

### <a name="on-demand-elastic-capacity-for-large-scale-projects"></a>Elastické kapacity na vyžádání u rozsáhlých projektů
Data vědecké účely hackathons/soutěže nebo modelování ve velkém rozsahu dat a zkoumání vyžadují škálovat na více systémů kapacitu hardwaru, obvykle pro krátké doby trvání. Virtuální počítač vědecké účely dat může pomoci replikovat prostředí vědecké účely data rychle na vyžádání, na škálovaný serverech, které umožňují experimenty, které vyžadují vysokým výkonem výpočetních prostředků ke spuštění.

### <a name="short-term-experimentation-and-evaluation"></a>Krátkodobé experimentování a vyhodnocení
Virtuální počítač vědecké účely Data lze použít k vyhodnocení nebo další nástroje, jako je Microsoft ML Server, SQL Server, nástroje Visual Studio, Jupyter, hluboké učení / ML sadách a nových nástrojů oblíbených v komunitě s minimálním instalační program úsilí. Vzhledem k tomu, že virtuální počítač vědecké účely dat můžete rychle nastavit tak, můžete použít v jiné krátkodobé scénáře použití například replikace publikované experimenty, provádění ukázky, následující kurzy v online relací nebo kurzy konference.

### <a name="deep-learning"></a>Hloubkové učení
Vědecké zpracování dat virtuálních počítačů lze použít pro model školení pomocí hloubkové learning algoritmů na základě hardwaru grafický procesor (grafiky zpracování jednotky). Použití virtuálních počítačů škálování možnosti cloudu Azure, DSVM vám pomůže používat bázi GPU hardwaru v cloudu podle potřeby. Jeden můžete přepnout virtuálních počítačů na bázi GPU při tréninku velké modely nebo potřebovat vysokorychlostní výpočty při zachování stejné disk operačního systému.  Edice systému Windows Server 2016 DSVM dodává předinstalované ovladače grafického procesoru, architektury a GPU verzích rozhraní hloubkové učení. V systému Linux povolí se ve CentOS i Ubuntu DSVMs přímým učení na grafický procesor. Ubuntu a CentOS, Windows 2016 edice vědecké účely dat virtuálních počítačů můžete nasadit na jiný bázi GPU virtuální počítač Azure v takovém případě bude všechna rozhraní hloubkové learning přechod na režim procesoru. 

## <a name="whats-included-in-the-data-science-vm"></a>Co je součástí virtuálního počítače vědecké účely dat?
Virtuální počítač vědecké účely dat má mnoho oblíbených datové vědy a přímý výukové nástroje již nainstalován a nakonfigurován. Zahrnuje také nástroje, které usnadňují spolupráci s různými Azure data a analýzy produkty. Vám umožní zkoumat a vytvářet prediktivní modely v rozsáhlých datových sad pomocí serveru ML Microsoft (R, Python) nebo pomocí SQL serveru 2017. Hostitele z dalších nástrojů od komunity s otevřeným zdrojem a od společnosti Microsoft jsou také zahrnuté, stejně jako ukázku kódu a poznámkových bloků. V následující tabulce rozepisuje a porovná hlavní komponenty zahrnuté v edicích Windows a Linux z virtuálního počítače vědecké účely Data.


| **Nástroj**                                                           | **Windows Edition** | **Edice systému Linux** |
| :------------------------------------------------------------------ |:-------------------:|:------------------:|
| [Microsoft R otevřete](https://mran.microsoft.com/open/) pomocí Oblíbené balíčků předinstalovaným   |Ano                      | Ano             |
| [Server ML Microsoft (R, Python)](https://docs.microsoft.com/machine-learning-server/) zahrnuje Developer Edition, <br />  &nbsp;&nbsp;&nbsp;&nbsp;* [RevoScaleR/revoscalepy](https://docs.microsoft.com/machine-learning-server/r/concept-what-is-revoscaler) paralelní a distribuované vysoce výkonné framework (R & Python)<br />  &nbsp;&nbsp;&nbsp;&nbsp;* [MicrosoftML](https://docs.microsoft.com/machine-learning-server/r/concept-what-is-the-microsoftml-package) -nových algoritmů ML stavu techniky od společnosti Microsoft <br />  &nbsp;&nbsp;&nbsp;&nbsp;* [R a Python Operationalization](https://docs.microsoft.com/machine-learning-server/what-is-operationalization)                                            |Ano                      | Ano |
| [Aplikace Microsoft Office](https://products.office.com/en-us/business/office-365-proplus-business-software) poměrně Plus s sdílené aktivace – aplikace Excel, Word a PowerPoint   |Ano                      |N              |
| [Anaconda Python](https://www.continuum.io/) 2.7, 3.5 pomocí Oblíbené balíčků předinstalovaným    |Ano                      |Ano              |
| [JuliaPro](https://juliacomputing.com/products/juliapro.html) pomocí Oblíbené balíčků pro jazyk Dita předinstalovaným                         |Ano                      |Ano              |
| Relační databáze                                                            | [SQL Server 2017](https://www.microsoft.com/sql-server/sql-server-2017) <br/> Developer Edition| [PostgreSQL](https://www.postgresql.org/)(pouze CentOS) |
| Databáze nástroje                                                       | * SQL Server Management Studio <br/>* SQL Server Integration Services<br/>* [BCP, sqlcmd](https://docs.microsoft.com/sql/tools/command-prompt-utility-reference-database-engine)<br /> * Rozhraní ODBC/JDBC ovladače| * [SQuirreL SQL](http://squirrel-sql.sourceforge.net/) (dotazování nástroj), <br /> * bcp, sqlcmd <br /> * Rozhraní ODBC/JDBC ovladače|
| Škálovatelné analýzy v databázi s služby SQL Server ML (R, Python) | Ano     |N              |
| **[Server poznámkového bloku Jupyter](http://jupyter.org/) s následující jádra**                                  | Ano     | Ano |
|     &nbsp;&nbsp;&nbsp;&nbsp;* R | Ano | Ano |
|     &nbsp;&nbsp;&nbsp;&nbsp;* Python 2.7 & 3.5 | Ano | Ano |
|     &nbsp;&nbsp;&nbsp;&nbsp;* Dita | Ano | Ano |
|     &nbsp;&nbsp;&nbsp;&nbsp;* PySpark | Ano | Ano |
|     &nbsp;&nbsp;&nbsp;&nbsp;* [Sparkmagic](https://github.com/jupyter-incubator/sparkmagic) | N | Y (pouze Ubuntu) |
|     &nbsp;&nbsp;&nbsp;&nbsp;* SparkR     | N | Ano |
| JupyterHub (server poznámkových bloků více uživatelů)| N | Ano |
| **Nástroje pro vývoj, editory integrovaného vývojového prostředí a kódu**| | |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Visual Studio 2017 (Community Edition)](https://www.visualstudio.com/community/) > pomocí modulu plug-in Git, Azure HDInsight (Hadoop), Data Lake, SQL Server Data tools [Node.js](https://github.com/Microsoft/nodejstools), [Python](http://aka.ms/ptvs), a [R nástrojů pro Visual Studio (RTVS)](http://microsoft.github.io/RTVS-docs/) | Ano | N |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Visual Studio Code](https://code.visualstudio.com/) | Ano | Ano |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Rstudia plochy](https://www.rstudio.com/products/rstudio/#Desktop) | Ano | Ano |
| &nbsp;&nbsp;&nbsp;&nbsp;* [RStudio Server](https://www.rstudio.com/products/rstudio/#Server) | N | Ano |
| &nbsp;&nbsp;&nbsp;&nbsp;* [PyCharm](https://www.jetbrains.com/pycharm/) | N | Ano |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Atom](https://atom.io/) | N | Ano |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Juno (Dita IDE)](http://junolab.org/)| Ano | Ano |
| &nbsp;&nbsp;&nbsp;&nbsp;* Vim a Emacs | Ano | Ano |
| &nbsp;&nbsp;&nbsp;&nbsp;* Git a Git Bash | Ano | Ano |
| &nbsp;&nbsp;&nbsp;&nbsp;* OpenJDK | Ano | Ano |
| &nbsp;&nbsp;&nbsp;&nbsp;* Rozhraní .net framework | Ano | N |
| PowerBI Desktop | Ano | N |
| Sady SDK pro přístup k Azure a Cortana Intelligence Suite služeb | Ano | Ano |
| **Přesun dat a nástroje pro správu** | | |
| &nbsp;&nbsp;&nbsp;&nbsp;* Azure Storage Explorer | Ano | Ano |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Azure CLI](https://docs.microsoft.com/cli/azure) | Ano | Ano |
| &nbsp;&nbsp;&nbsp;&nbsp;* Azure Powershell | Ano | N |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Azcopy](https://docs.microsoft.com/azure/storage/storage-use-azcopy) | Ano | N |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Objekt BLOB POJISTKA ovladačů](https://github.com/Azure/azure-storage-fuse) | N | Ano |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Adlcopy (Azure Data Lake Storage)](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-copy-data-azure-storage-blob) | Ano | N |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Nástroj pro migraci dat DocDB](https://docs.microsoft.com/azure/documentdb/documentdb-import-data) | Ano | N |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Brána pro správu dat](https://msdn.microsoft.com/library/dn879362.aspx): Přesun dat mezi místní a Cloud | Ano | N |
| &nbsp;&nbsp;&nbsp;&nbsp;* Nástroje příkazového řádku systému Unix/Linux | Ano | Ano |
| [Rozbalení Apache](http://drill.apache.org) pro zkoumání dat | Ano | Ano |
| **Nástroje Machine Learning** |||
| &nbsp;&nbsp;&nbsp;&nbsp;* Integrace s [Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/) (R, Python) | Ano | Ano |
| &nbsp;&nbsp;&nbsp;&nbsp;* [xgboost](https://github.com/dmlc/xgboost) | Ano | Ano |
| &nbsp;&nbsp;&nbsp;&nbsp;* [K dispozici Vowpal](https://github.com/JohnLangford/vowpal_wabbit) | Ano | Ano |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Weka](http://www.cs.waikato.ac.nz/ml/weka/) | Ano | Ano |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Rattle](http://rattle.togaware.com/) | Ano | Ano |
| &nbsp;&nbsp;&nbsp;&nbsp;* [LightGBM](https://github.com/Microsoft/LightGBM) | N | Y (pouze Ubuntu) |
| &nbsp;&nbsp;&nbsp;&nbsp;* [H2O](https://www.h2o.ai/h2o/) | N | Y (pouze Ubuntu) |
| **Výukové nástroje přímý bázi GPU** |Windows Server 2016 edition  | Ano |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Microsoft kognitivní Toolkit (dříve označované jako CNTK)](https://www.microsoft.com/en-us/cognitive-toolkit/) | Ano | Ano |
| &nbsp;&nbsp;&nbsp;&nbsp;* [TensorFlow](https://www.tensorflow.org/) | Ano | Ano |
| &nbsp;&nbsp;&nbsp;&nbsp;* [MXNet](http://mxnet.io/) | Ano | Ano|
| &nbsp;&nbsp;&nbsp;&nbsp;* [Caffe & Caffe2](https://github.com/caffe2/caffe2) | N | Ano |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Svítilnou](http://torch.ch/) | N | Ano |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Theano](https://github.com/Theano/Theano) | N | Ano |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Keras](https://keras.io/)| N | Ano |
| &nbsp;&nbsp;&nbsp;&nbsp;* [PyTorch](http://pytorch.org/)| N | Ano |
| &nbsp;&nbsp;&nbsp;&nbsp;* [NVidia číslic](https://github.com/NVIDIA/DIGITS) | N | Ano |
| &nbsp;&nbsp;&nbsp;&nbsp;* [MXNet Model serveru](https://github.com/awslabs/mxnet-model-server) | N | Ano |
| &nbsp;&nbsp;&nbsp;&nbsp;* [TensorFlow slouží](https://www.tensorflow.org/serving/) | N | Ano |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Ovladač Nvidia CUDA, CUDNN,](https://developer.nvidia.com/cuda-toolkit) | Ano | Ano |
| **Platforma velké objemy dat (pouze Devtest)**|||
| &nbsp;&nbsp;&nbsp;&nbsp;* Místní [Spark](http://spark.apache.org/) samostatné | N | Ano |
| &nbsp;&nbsp;&nbsp;&nbsp;* Místní [Hadoop](http://hadoop.apache.org/) (HDFS, YARN) | N | Ano |



## <a name="get-started-with-the-windows-data-science-vm"></a>Začínáme s virtuální počítač Windows dat vědecké účely
* Vytvořit instanci požadovaného edice Windows DSVM přechodem na
  * [Windows Server 2016 based DSVM](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/microsoft-ads.windows-data-science-vm)
  
  nebo 
  * [Windows Server 2012 based DSVM](https://azure.microsoft.com/marketplace/partners/microsoft-ads/standard-data-science-vm/) 
* Klikněte **získat IT teď** tlačítko.
* Přihlásit se k virtuálnímu počítači pomocí vzdálené plochy s použitím přihlašovacích údajů, které jste zadali při vytváření virtuálního počítače.
* Pokud chcete zjistit a spusťte nástroje dostupné, klikněte **spustit** nabídky.

## <a name="get-started-with-the-linux-data-science-vm"></a>Začínáme s virtuálního počítače s Linuxem dat vědecké účely
* Vytvořit instanci požadovaného edice Linux DSVM tak, že přejdete na 
  * [Ubuntu na základě DSVM](http://aka.ms/dsvm/ubuntu)

  nebo

  * [CentOS na základě DSVM](http://aka.ms/dsvm/centos)

  
* Klikněte **ho získat** tlačítko.
* Přihlaste se k virtuálnímu počítači z klienta SSH, jako je například Putty nebo příkaz SSH, pomocí přihlašovacích údajů, které jste zadali při vytváření virtuálního počítače.
* V řádku prostředí zadejte dsvm. Další informace.
* Grafické stolní počítač, stáhněte si klienta X2Go pro vaše klientská platforma [sem](http://wiki.x2go.org/doku.php/doc:installation:x2goclient) a postupujte podle pokynů v dokumentu virtuálního počítače s Linuxem datové vědy [zřízení virtuálního počítače Linux datové vědy](linux-dsvm-intro.md#installing-and-configuring-x2go-client).

## <a name="next-steps"></a>Další postup
### <a name="for-the-windows-data-science-vm"></a>Pro vědecké zpracování dat systému Windows virtuálního počítače
* Další informace o tom, jak spustit určité nástroje, které jsou k dispozici na verzi systému Windows najdete v tématu [zřízení virtuálního počítače Microsoft Data vědecké účely](provision-vm.md) a
* Další informace o tom, jak provádět různé úlohy, které jsou potřebné pro svůj projekt vědecké účely data na virtuálním počítači Windows najdete v tématu [deset způsobů, jak na vědecké zpracování dat virtuálního počítače](vm-do-ten-things.md).

### <a name="for-the-linux-data-science-vm"></a>Pro Linux datové vědy virtuálních počítačů
* Další informace o tom, jak spustit určité nástroje, které jsou k dispozici na verzi systému Linux najdete v tématu [zřízení virtuálního počítače Linux datové vědy](linux-dsvm-intro.md).
* Návod, který ukazuje, jak provést několik běžných úkolů data vědecké účely pomocí virtuálního počítače s Linuxem najdete v tématu [vědecké zpracování dat na datové vědě virtuální počítač Linux](linux-dsvm-walkthrough.md).

