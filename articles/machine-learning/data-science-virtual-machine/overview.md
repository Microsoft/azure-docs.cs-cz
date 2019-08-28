---
title: Úvod do Azure Data Science Virtual Machine pro Linux a Windows | Microsoft Docs
description: Důležité analytické scénáře a komponenty pro virtuální počítače pro datové vědy pro Windows a Linux
keywords: nástroje pro datové vědy, virtuální počítač pro datové vědy, datové vědy pro linux
services: machine-learning
documentationcenter: ''
author: vijetajo
manager: cgronlun
ms.assetid: d4f91270-dbd2-4290-ab2b-b7bfad0b2703
ms.service: machine-learning
ms.subservice: data-science-vm
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 02/22/2019
ms.author: vijetaj
ms.openlocfilehash: 22192d67a946f9f466848b65b9cd763b16e6c5f0
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/28/2019
ms.locfileid: "70099372"
---
# <a name="what-is-the-azure-data-science-virtual-machine-for-linux-and-windows"></a>Co je Azure Data Science Virtual Machine pro Linux a Windows?

Data Science Virtual Machine (DSVM) je přizpůsobená image virtuálního počítače na cloudové platformě Azure sestavená speciálně pro účely datové vědy. Obsahuje řadu oblíbených předinstalovaných nástrojů datové vědy a dalších funkcí a je předem nakonfigurovaná, abyste mohli hned začít sestavovat inteligentní aplikace pro pokročilou analýzu. 

Konfigurace nástrojů jsou přísně testovány odborníky přes data a vývojáři v Microsoftu a širší komunitou pro odborníky přes data. Toto testování pomáhá zajistit stabilitu a obecnou životaschopnost.

DSVM je k dispozici na:
+ Windows Server 2016, Windows Server 2012
+ Ubuntu 16,04 LTS a CentOS 7,4

> [!NOTE]
> Všechny nástroje virtuálních počítačů pro obsáhlý Learning byly přeložené do Data Science Virtual Machine. 


## <a name="what-can-i-do-with-the-dsvm"></a>Co můžu s DSVM dělat?
Cílem Data Science Virtual Machine je poskytnout odborníky na data pro všechny úrovně dovedností a v rámci odvětví pomocí bezproblémového, předem nakonfigurovaného a plně integrovaného prostředí pro datové vědy. Místo toho, abyste si přihlásili srovnatelný pracovní prostor sami, můžete zřídit DSVM. Tato volba může ušetřit dny nebo dokonce _týdny_ v procesech instalace, konfigurace a správy balíčků. Po přidělení virtuálního počítače pro datové vědy můžete okamžitě začít pracovat na svém projektu datové vědy.

DSVM je navržený a nakonfigurovaný pro práci s širokou škálou scénářů použití. V průběhu změny požadavků na projekt můžete škálovat prostředí nahoru nebo dolů. Můžete také použít preferovaný jazyk k programování úkolů pro datové vědy a instalovat další nástroje, abyste mohli přizpůsobit systém podle svých potřeb.

### <a name="preconfigured-analytics-desktop-in-the-cloud"></a>Předkonfigurovaný analytický desktop v cloudu
DSVM poskytuje základní konfiguraci pro týmy pro datové vědy, které chtějí nahradit své místní plochy pomocí spravovaného cloudového klienta. Tato základní konfigurace zajišťuje, že všichni odborníci přes data v týmu mají konzistentní nastavení pro ověřování experimentů a spolupráci. Také snižuje náklady snížením zatížení sysadmin. Toto omezení režie šetří dobu potřebnou k vyhodnocení, instalaci a údržbě softwarových balíčků pro pokročilou analýzu.

### <a name="data-science-training-and-education"></a>Školení a vzdělávání v oblasti datové vědy
Firemní učitelé a pedagogy, kteří učí třídy pro datové vědy, obvykle poskytují image virtuálního počítače. Bitová kopie zajistí, že studenti mají konzistentní nastavení a že ukázky fungují podle předpovědi. 

DSVM vytvoří prostředí na vyžádání s konzistentním nastavením, které usnadňuje problémy s podporou a nekompatibilitou. Užitečný je zejména v případech, kdy je potřeba taková prostředí vytvářet často, například pro kratší školení.

### <a name="on-demand-elastic-capacity-for-large-scale-projects"></a>Přizpůsobivá kapacita na vyžádání pro velké projekty
Datové vědy hackathony/soutěže nebo rozsáhlé modelování dat a průzkumy vyžadují kapacitu hardwaru škálované na více instancí, obvykle pro krátkou dobu trvání. DSVM může rychle replikovat prostředí pro datové vědy na vyžádání na serverech se škálováním na více systémů, které umožňují experimenty s vysokými výpočetními prostředky, které se dají spustit.

### <a name="custom-compute-power-for-azure-notebooks"></a>Vlastní výpočetní výkon pro Azure Notebooks
[Azure Notebooks](../../notebooks/azure-notebooks-overview.md) je bezplatná hostované služba pro vývoj, spouštění a sdílení poznámkových bloků Jupyter v cloudu bez instalace. Úroveň služby Free je omezená na 4 GB paměti a 1 GB dat. 

Pokud chcete uvolnit všechna omezení, můžete k DSVM nebo jakémukoli jinému VIRTUÁLNÍmu počítači běžícímu na serveru Jupyter připojit projekt poznámkových bloků. Pokud se k Azure Notebooks přihlašujete pomocí účtu pomocí Azure Active Directory (například podnikového účtu), poznámkové bloky se automaticky zobrazí DSVMs v jakýchkoli předplatných přidružených k tomuto účtu. K rozšíření dostupného výpočetního výkonu můžete k [Azure Notebooks připojit DSVM](../../notebooks/configure-manage-azure-notebooks-projects.md#compute-tier) .

### <a name="short-term-experimentation-and-evaluation"></a>Krátkodobé experimenty a vyhodnocování
DSVM můžete použít k vyhodnocení nebo poučení nástrojů, jako jsou tyto nástroje, s minimálním úsilím k nastavení:

- Server Microsoft Machine Learning
- SQL Server
- Nástroje sady Visual Studio
- Jupyter
- Sady nástrojů pro hloubkové učení a strojové učení
- Nové nástroje oblíbené ve komunitě 

Vzhledem k tomu, že DSVM můžete rychle nastavit, můžete ho použít v jiných scénářích krátkodobého použití. Mezi tyto scénáře patří replikace publikovaných experimentů, provádění ukázek a postupy v online relacích a konferenčních kurzech.

### <a name="deep-learning"></a>Hloubkové učení
Ve DSVM můžou vaše školicí modely používat algoritmy hloubkového učení na hardwaru, který je založený na procesorech GPU (Graphics Processing units). Díky možnostem škálování virtuálního počítače na platformě Azure vám DSVM pomůže používat v cloudu hardware využívající GPU podle vašich potřeb. Při výuce velkých modelů můžete přepnout na virtuální počítač s grafickým procesorem nebo když budete potřebovat vysokorychlostní výpočty a zároveň zachováte stejný disk s operačním systémem.  

Edice DSVM systému Windows Server 2016 je součástí předinstalovaného s ovladači GRAFICKÉho učení, architekturami a verzemi GPU pro hloubkové učení. V edici Linux je obsáhlý Learning na GPU povolený jak na CentOS, tak na Ubuntu DSVMs. 

Edici DSVM, CentOS nebo Windows 2016 můžete nasadit taky na virtuální počítač Azure, který není založený na GPU. V takovém případě se všechny architektury hloubkového učení vrátí do režimu CPU.
 
[Přečtěte si další informace o dostupných architekturách hloubkového učení a AI](dsvm-deep-learning-ai-frameworks.md).

<a name="included"></a>

## <a name="whats-included-on-the-dsvm"></a>Co je součástí DSVM?
Virtuální počítač pro datovou vědu obsahuje mnoho oblíbených nástrojů pro datovou vědu a hloubkové učení, které jsou již nainstalovány a nakonfigurovány. Obsahuje také nástroje, které usnadňují práci s různými produkty Azure pro data a analýzu. Mezi tyto produkty patří Microsoft Machine Learning Server (R, Python) pro vytváření prediktivních modelů a SQL Server 2017 pro vysoce škálovatelný průzkum datových sad. DSVM obsahuje další nástroje od Open source komunity a od Microsoftu spolu s [ukázkovým kódem a poznámkovými bloky](dsvm-samples-and-walkthroughs.md). 

Tady je seznam nástrojů a platforem:
+ [Podporované programovací jazyky](dsvm-languages.md)

+ [Podporované datové platformy](dsvm-data-platforms.md)

+ [Vývojové nástroje a IDEs](dsvm-tools-development.md)

+ [Rozhraní pro hloubkové učení a AI](dsvm-deep-learning-ai-frameworks.md)

+ [Machine Learning a nástroje pro datové vědy](dsvm-ml-data-science-tools.md)

+ [Nástroje pro přijímání dat](dsvm-tools-ingestion.md)

+ [Nástroje pro zkoumání a vizualizaci dat](dsvm-tools-explore-and-visualize.md)

V následující tabulce najdete výčet a srovnání hlavních komponent, které jsou součástí edic virtuálního počítače pro datovou vědu pro Windows a Linux.

| **Nástroj**                                                           | **Edice Windows** | **Linux Edition** |
| :------------------------------------------------------------------ |:-------------------:|:------------------:|
| [Microsoft R Open](https://mran.microsoft.com/open/) s předinstalovanými oblíbenými balíčky   |Ano                      | Ano             |
| [Microsoft Machine Learning Server (R, Python)](https://docs.microsoft.com/machine-learning-server/) Edice Developer Edition zahrnuje: <br />  &nbsp;&nbsp;&nbsp;&nbsp;[RevoScaleR/revoscalepy](https://docs.microsoft.com/machine-learning-server/r/concept-what-is-revoscaler) , paralelní a distribuované vysoce výkonné architektury (R a Python)<br />  &nbsp;&nbsp;&nbsp;&nbsp;[MicrosoftML](https://docs.microsoft.com/machine-learning-server/r/concept-what-is-the-microsoftml-package), nové nejmodernější algoritmy strojového učení od Microsoftu <br />  &nbsp;&nbsp;&nbsp;&nbsp;[Provoz R a Pythonu](https://docs.microsoft.com/machine-learning-server/what-is-operationalization)                                            |Ano                      | Ano |
| [Systém Microsoft Office](https://products.office.com/business/office-365-proplus-business-software) ProPlus se sdílenou aktivací: Excel, Word a PowerPoint   |Ano                      |Ne              |
| [Anaconda Python](https://www.continuum.io/) 2,7 a 3,5 s předinstalovanými oblíbenými balíčky    |Ano                      |Ano              |
| [JuliaPro](https://juliacomputing.com/products/juliapro.html) s předinstalovanými oblíbenými balíčky pro jazyk Julia                         |Ano                      |Ano              |
| Relační databáze                                                            | [SQL Server 2017](https://www.microsoft.com/sql-server/sql-server-2017) <br/> Developer Edition| [PostgreSQL](https://www.postgresql.org/) (CentOS),<br/>[SQL Server 2017](https://www.microsoft.com/sql-server/sql-server-2017) <br/> Developer Edition (Ubuntu) |
| Databázové nástroje                                                       |  SQL Server Management Studio <br/> Služba SSIS (SQL Server Integration Services)<br/> [BCP, Sqlcmd](https://docs.microsoft.com/sql/tools/command-prompt-utility-reference-database-engine)<br />  Ovladače ODBC/JDBC|  [SQUIRREL SQL](http://squirrel-sql.sourceforge.net/) (Nástroj pro dotazování), <br />  BCP, Sqlcmd <br />  Ovladače ODBC/JDBC|
| Škálovatelná analýza v databázi pomocí služby SQL Server Machine Learning Services (R, Python) | Ano     |Ne              |
| [Jupyter notebook Server](https://jupyter.org/) s následujícími jádry:                                  | Ano     | Ano |
|     &nbsp;&nbsp;&nbsp;&nbsp;Í | Ano | Ano |
|     &nbsp;&nbsp;&nbsp;&nbsp;Python | Ano | Ano |
|     &nbsp;&nbsp;&nbsp;&nbsp;Helena | Ano | Ano |
|     &nbsp;&nbsp;&nbsp;&nbsp;PySpark | Ano | Ano |
|     &nbsp;&nbsp;&nbsp;&nbsp;[Sparkmagic](https://github.com/jupyter-incubator/sparkmagic) | Ne | Ano (pouze Ubuntu) |
|     &nbsp;&nbsp;&nbsp;&nbsp;SparkR     | Ne | Ano |
| JupyterHub (Server poznámkového bloku pro více uživatelů)| Ne | Ano |
| JupyterLab (Server poznámkového bloku pro více uživatelů) | Ne | Ano (pouze Ubuntu) |
| Vývojové nástroje, IDEs a editory kódu:| | |
| &nbsp;&nbsp;&nbsp;&nbsp;[Visual Studio 2019 (Community Edition)](https://www.visualstudio.com/community/) s modulem plug-in Git, Azure HDInsight (Hadoop), Azure Data Lake, SQL Server Data Tools, [Node. js](https://github.com/Microsoft/nodejstools), [Python](https://aka.ms/ptvs)a [nástroje R pro Visual Studio (RTVS)](https://microsoft.github.io/RTVS-docs/) | Ano | Ne |
| &nbsp;&nbsp;&nbsp;&nbsp;[Visual Studio Code](https://code.visualstudio.com/) | Ano | Ano |
| &nbsp;&nbsp;&nbsp;&nbsp;[RStudio Desktop](https://www.rstudio.com/products/rstudio/#Desktop) | Ano | Ano |
| &nbsp;&nbsp;&nbsp;&nbsp;[Server RStudio](https://www.rstudio.com/products/rstudio/#Server) | Ne | Ano |
| &nbsp;&nbsp;&nbsp;&nbsp;[Edice PyCharm Community](https://www.jetbrains.com/pycharm/) | Ne | Ano |
| &nbsp;&nbsp;&nbsp;&nbsp;[Atom](https://atom.io/) | Ne | Ano |
| &nbsp;&nbsp;&nbsp;&nbsp;[Juno (Helena IDE)](https://junolab.org/)| Ano | Ano |
| &nbsp;&nbsp;&nbsp;&nbsp;Systémem VIM a (Emacs) | Ano | Ano |
| &nbsp;&nbsp;&nbsp;&nbsp;Git a Git bash | Ano | Ano |
| &nbsp;&nbsp;&nbsp;&nbsp;OpenJDK | Ano | Ano |
| &nbsp;&nbsp;&nbsp;&nbsp;.NET Framework | Ano | Ne |
| Power BI Desktop | Ano | Ne |
| Sady SDK pro přístup ke službám Azure a Cortana Intelligence Suite | Ano | Ano |
| Nástroje pro přesun a správu dat: | | |
| &nbsp;&nbsp;&nbsp;&nbsp;Průzkumník služby Azure Storage | Ano | Ano |
| &nbsp;&nbsp;&nbsp;&nbsp;Rozhraní příkazového [řádku Azure](https://docs.microsoft.com/cli/azure) | Ano | Ano |
| &nbsp;&nbsp;&nbsp;&nbsp;Azure PowerShell | Ano | Ne |
| &nbsp;&nbsp;&nbsp;&nbsp;[AzCopy](https://docs.microsoft.com/azure/storage/storage-use-azcopy) | Ano | Ne |
| &nbsp;&nbsp;&nbsp;&nbsp;[Ovladač zapékací objektu BLOB](https://github.com/Azure/azure-storage-fuse) | Ne | Ano |
| &nbsp;&nbsp;&nbsp;&nbsp;[AdlCopy (Azure Data Lake Storage)](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-copy-data-azure-storage-blob) | Ano | Ne |
| &nbsp;&nbsp;&nbsp;&nbsp;[Nástroj pro migraci dat Azure Cosmos DB](https://docs.microsoft.com/azure/documentdb/documentdb-import-data) | Ano | Ne |
| &nbsp;&nbsp;&nbsp;&nbsp;[Brána Microsoft Správa dat Gateway](https://msdn.microsoft.com/library/dn879362.aspx): přesun dat mezi místním prostředím a cloudem | Ano | Ne |
| &nbsp;&nbsp;&nbsp;&nbsp;Nástroje příkazového řádku pro UNIX/Linux | Ano | Ano |
| Zkoumání podrobností pro prozkoumávání dat pro [Apache](https://drill.apache.org) | Ano | Ano |
| Nástroje pro strojové učení: |||
| &nbsp;&nbsp;&nbsp;&nbsp;Integrace s [Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/) (R, Python) | Ano | Ano |
| &nbsp;&nbsp;&nbsp;&nbsp;[XGBoost](https://github.com/dmlc/xgboost) | Ano | Ano |
| &nbsp;&nbsp;&nbsp;&nbsp;[Pro dostupné](https://github.com/JohnLangford/vowpal_wabbit) | Ano | Ano |
| &nbsp;&nbsp;&nbsp;&nbsp;[Weka](https://www.cs.waikato.ac.nz/ml/weka/) | Ano | Ano |
| &nbsp;&nbsp;&nbsp;&nbsp;[Rattle](https://togaware.com/rattle/) | Ano | Ano |
| &nbsp;&nbsp;&nbsp;&nbsp;[LightGBM](https://github.com/Microsoft/LightGBM) | Ne | Ano (pouze Ubuntu) |
| &nbsp;&nbsp;&nbsp;&nbsp;[CatBoost](https://tech.yandex.com/catboost/) | Ne | Ano (pouze Ubuntu) |
| &nbsp;&nbsp;&nbsp;&nbsp;[](https://www.h2o.ai/h2o/)Jiskrová, [sparková voda](https://www.h2o.ai/sparkling-water/) | Ne | Ano (pouze Ubuntu) |
| Nástroje pro hloubkové učení, které pracují s grafickým procesorem nebo PROCESORem: |  |  |
| &nbsp;&nbsp;&nbsp;&nbsp;[Microsoft Cognitive Toolkit (CNTK)](https://docs.microsoft.com/cognitive-toolkit/) (Windows 2016) | Ano | Ano |
| &nbsp;&nbsp;&nbsp;&nbsp;[TensorFlow](https://www.tensorflow.org/) | Ano (Windows 2016) | Ano |
| &nbsp;&nbsp;&nbsp;&nbsp;[Horovod](https://github.com/uber/horovod) | Ne | Ano (Ubuntu) |
| &nbsp;&nbsp;&nbsp;&nbsp;[MXNet](https://mxnet.io/) | Ano (Windows 2016) | Ano|
| &nbsp;&nbsp;&nbsp;&nbsp;[Caffe a Caffe2](https://github.com/caffe2/caffe2) | Ne | Ano |
| &nbsp;&nbsp;&nbsp;&nbsp;[Řetězení](https://chainer.org/) | Ne | Ano |
| &nbsp;&nbsp;&nbsp;&nbsp;[Torch](http://torch.ch/) | Ne | Ano |
| &nbsp;&nbsp;&nbsp;&nbsp;[Theano](https://github.com/Theano/Theano) | Ne | Ano |
| &nbsp;&nbsp;&nbsp;&nbsp;[Keras](https://keras.io/)| Ne | Ano |
| &nbsp;&nbsp;&nbsp;&nbsp;[PyTorch](https://pytorch.org/)| Ne | Ano |
| &nbsp;&nbsp;&nbsp;&nbsp;[Číslice NVIDIA](https://github.com/NVIDIA/DIGITS) | Ne | Ano |
| &nbsp;&nbsp;&nbsp;&nbsp;[Server MXNet modelů](https://github.com/awslabs/mxnet-model-server) | Ne | Ano |
| &nbsp;&nbsp;&nbsp;&nbsp;[TensorFlow obsluhující](https://www.tensorflow.org/serving/) | Ne | Ano |
| &nbsp;&nbsp;&nbsp;&nbsp;[TensorRT](https://developer.nvidia.com/tensorrt) | Ne | Ano |
| &nbsp;&nbsp;&nbsp;&nbsp;[CUDA, cuDNN, ovladač NVIDIA](https://developer.nvidia.com/cuda-toolkit) | Ano | Ano |

## <a name="next-steps"></a>Další kroky

Další informace najdete v těchto článcích:

+ Windows:
  + [Nastavení DSVM s Windows](provision-vm.md)
  + [10 věcí, které můžete provádět na DSVM Windows](vm-do-ten-things.md)

+ Linux:
  + [Nastavení DSVM pro Linux (Ubuntu)](dsvm-ubuntu-intro.md)
  + [Nastavení DSVM pro Linux (CentOS)](linux-dsvm-intro.md)
  + [Datové vědy na DSVM pro Linux](linux-dsvm-walkthrough.md)
