---
title: Úvod k virtuálnímu počítači pro datové vědy pro Linux a Windows | Microsoft Docs
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
ms.openlocfilehash: 2814ad51d2f0f868833cf9c6964b7ea4a8424435
ms.sourcegitcommit: 5ded08785546f4a687c2f76b2b871bbe802e7dae
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/19/2019
ms.locfileid: "69574922"
---
# <a name="what-is-azure-data-science-virtual-machine-for-linux-and-windows"></a>Co je Azure Data Science Virtual Machine pro Linux a Windows?

Virtuální počítač pro datové vědy je přizpůsobená image virtuálního počítače v cloudu Azure Microsoftu vytvořená speciálně pro účely datových věd. Obsahuje řadu oblíbených předinstalovaných nástrojů datové vědy a dalších funkcí a je předem nakonfigurovaná, abyste mohli hned začít sestavovat inteligentní aplikace pro pokročilou analýzu. 

Nástroj konfigurace jsou pečlivě otestovat odborníci přes data a vývojáři ve společnosti Microsoft a širší komunity odborníků přes data zajistit stabilitu a obecné životnost.

DSVM je k dispozici na:
+ Windows Server 2016, Windows Server 2012
+ Ubuntu 16,04 LTS a CentOS 7,4

**Všechny nástroje pro virtuální počítače** s hloubkovým učením byly přeloženy do data Science VM. 


## <a name="what-can-i-do-with-dsvm"></a>Co můžu s DSVM dělat?
Virtuální počítač pro datové vědy je určený odborníkům pracujícím s daty všech úrovní a v nejrůznějších oborech, kterým poskytuje bezproblémové předkonfigurované a plně integrované prostředí pro vědecké zpracování dat. Místo zavádění srovnatelného pracovního prostoru vlastními silami můžete zřídit virtuální počítač pro datové vědy a tím si ušetřit dny nebo dokonce _týdny_ práce s instalací, konfigurací a správou balíčků. Po přidělení virtuálního počítače pro datové vědy můžete okamžitě začít pracovat na svém projektu datové vědy.

Virtuální počítač pro datové vědy je nakonfigurován a navržen pro širokou řadu scénářů použití. V průběhu změny požadavků na projekt můžete škálovat prostředí nahoru nebo dolů. Můžete také použít preferovaný jazyk pro programové úkoly pro datové vědy a nainstalovat další nástroje, abyste systém mohli přizpůsobit přesně vašim potřebám.

### <a name="preconfigured-analytics-desktop-in-the-cloud"></a>Předkonfigurovaný analytický desktop v cloudu
Virtuální počítač pro datové vědy poskytuje základní konfiguraci týmům pro datovou vědu, které chtějí nahradit svoje desktopy spravovaným cloudovým desktopem. Tato základní konfigurace zajišťuje, že všichni odborníci přes data v týmu mají konzistentní nastavení pro ověřování experimentů a spolupráci. Také snižuje náklady snížením zatížení sysadmin. Toto omezení režie šetří čas potřebný k vyhodnocení, instalaci a údržbě různých softwarových balíčků potřebných k provádění pokročilých analýz.

### <a name="data-science-training-and-education"></a>Školení a vzdělávání v oblasti datové vědy
Firemní učitelé a pedagogské, kteří se učí třídy pro datové vědy, obvykle poskytují image virtuálního počítače. Poskytují bitovou kopii, aby měli jistotu, že jejich studenti mají konzistentní nastavení a že ukázky fungují předpovídat. Virtuální počítač pro datové vědy vytvoří prostředí na vyžádání s konzistentním nastavením, které usnadňuje podporu a omezuje problémy s nekompatibilitou. Užitečný je zejména v případech, kdy je potřeba taková prostředí vytvářet často, například pro kratší školení.

### <a name="on-demand-elastic-capacity-for-large-scale-projects"></a>Přizpůsobivá kapacita na vyžádání pro velké projekty
Hackathony nebo soutěže v oblasti datových věd nebo rozsáhlé modelování či průzkum dat vyžadují – obvykle na krátkou dobu – horizontální navýšení kapacity hardwaru. Data Science VM může přispět k rychlé replikaci prostředí pro datové vědy na vyžádání na škálované servery, které umožňují experimenty s vysokými výpočetními prostředky, které se mají spustit.

### <a name="custom-compute-power-for-azure-notebooks"></a>Vlastní výpočetní výkon pro Azure Notebooks
[Azure Notebooks](../../notebooks/azure-notebooks-overview.md) je bezplatná hostované služba pro vývoj, spouštění a sdílení poznámkových bloků Jupyter v cloudu bez instalace. Úroveň bezplatné služby se ale omezuje na 4 GB paměti a 1 GB dat. Chcete-li uvolnit všechna omezení, můžete připojit projekt poznámkových bloků k Data Science VM nebo jinému virtuálnímu počítači, na kterém běží server Jupyter. Pokud se k Azure Notebooks přihlašujete pomocí účtu s použitím Azure Active Directory (například podnikového účtu), poznámkové bloky automaticky zobrazí virtuální počítače pro datové vědy v jakýchkoli předplatných přidružených k tomuto účtu. K rozšíření dostupného výpočetního výkonu můžete [připojit data Science VM k Azure Notebooks](../../notebooks/configure-manage-azure-notebooks-projects.md#compute-tier) .

### <a name="short-term-experimentation-and-evaluation"></a>Krátkodobé experimenty a vyhodnocování
S minimálním úsilím věnovaným nastavení je možné virtuální počítač pro datovou vědu využít k vyhodnocení nebo výuce nástrojů, jako jsou Microsoft ML Server, SQL Server, nástroje sady Visual Studio, Jupyter, sady nástrojů pro hloubkové nebo strojové učení a nové nástroje oblíbené v komunitě. Vzhledem k tomu, že Data Science VM lze rychle nastavit, je možné ji použít v jiných scénářích krátkodobého použití. Mezi tyto scénáře patří replikace publikovaných experimentů, provádění ukázek, postupy v online relacích a konferenčních kurzech.

### <a name="deep-learning"></a>Hloubkové učení
Virtuální počítač pro datovou vědu můžete využít v tréninkových modelech s použitím algoritmů hloubkového učení na hardwaru založeného na grafickém procesoru. S využitím možností škálování virtuálního počítače v cloudu Azure vám virtuální počítač pro datovou vědu umožňuje používat hardware založený na grafickém procesoru v cloudu podle potřeby. Při tréninku velkých modelů nebo potřebě vysokorychlostních výpočtů je možné přepnout na virtuální počítač založený na grafickém procesoru a stále zůstat na stejném disku s operačním systémem.  Verze Windows Serveru 2016 virtuálního počítače pro datovou vědu je dodávána s předinstalovanými ovladači grafického procesoru, architekturami a verzemi grafického procesoru architektur hloubkového učení. V edici pro Linux je hloubkové učení na grafickém procesoru povolené jak na virtuálním počítači pro datovou vědu CentOS, tak i Ubuntu. Data Science VM edici Ubuntu, CentOS nebo Windows 2016 můžete nasadit do virtuálního počítače Azure, který není založený na GPU. V takovém případě se všechny architektury hloubkového učení vrátí do režimu CPU. Přečtěte si další informace o [dostupných architekturách hloubkového učení a AI](dsvm-deep-learning-ai-frameworks.md).
 
Přečtěte si další informace o [dostupných architekturách hloubkového učení a AI](dsvm-deep-learning-ai-frameworks.md).

<a name="included"></a>

## <a name="whats-included-on-dsvm"></a>Co je součástí DSVM?
Virtuální počítač pro datovou vědu obsahuje mnoho oblíbených nástrojů pro datovou vědu a hloubkové učení, které jsou již nainstalovány a nakonfigurovány. Obsahuje taky nástroje, které usnadňují práci s různými datovými a analytickými produkty Azure, jako je Microsoft ML Server (R, Python) pro vytváření prediktivních modelů nebo SQL Server 2017 pro průzkum rozsáhlých datových sad. Data Science VM obsahuje hostitele dalších nástrojů od Open source komunity a od společnosti Microsoft, jakož i [ukázkový kód a poznámkové bloky](dsvm-samples-and-walkthroughs.md). 

Nástroje a platformy:
+ [Podporované programovací jazyky](dsvm-languages.md)

+ [Podporované datové platformy](dsvm-data-platforms.md)

+ [Vývojové nástroje a IDEs](dsvm-tools-development.md)

+ [Rozhraní pro hloubkové učení a AI](dsvm-deep-learning-ai-frameworks.md).

+ [Machine Learning a nástroje pro datové vědy](dsvm-ml-data-science-tools.md)

+ [Nástroje pro přijímání dat](dsvm-tools-ingestion.md)

+ [Nástroje pro zkoumání a vizualizaci dat](dsvm-tools-explore-and-visualize.md)

V následující tabulce najdete výčet a srovnání hlavních komponent, které jsou součástí edic virtuálního počítače pro datovou vědu pro Windows a Linux.

| **Nástroj**                                                           | **Edice pro Windows** | **Edice pro Linux** |
| :------------------------------------------------------------------ |:-------------------:|:------------------:|
| [Microsoft R Open](https://mran.microsoft.com/open/) s předinstalovanými oblíbenými balíčky   |Ano                      | Ano             |
| [Microsoft ML Server (R, Python)](https://docs.microsoft.com/machine-learning-server/) Developer Edition zahrnuje: <br />  &nbsp;&nbsp;&nbsp;&nbsp;* [RevoScaleR/revoscalepy](https://docs.microsoft.com/machine-learning-server/r/concept-what-is-revoscaler) – paralelní a distribuovaná architektura s vysokým výkonem (R & Python)<br />  &nbsp;&nbsp;&nbsp;&nbsp;* [MicrosoftML](https://docs.microsoft.com/machine-learning-server/r/concept-what-is-the-microsoftml-package) – nové moderní algoritmy strojového učení od Microsoftu <br />  &nbsp;&nbsp;&nbsp;&nbsp;* [Operacionalizace R a Python](https://docs.microsoft.com/machine-learning-server/what-is-operationalization)                                            |Ano                      | Ano |
| [Systém Microsoft Office](https://products.office.com/business/office-365-proplus-business-software) Pro-Plus se sdílenou aktivací – Excel, Word a PowerPoint   |Ano                      |Ne              |
| [Anaconda Python](https://www.continuum.io/) 2.7, 3.5 s předinstalovanými oblíbenými balíčky    |Ano                      |Ano              |
| [JuliaPro](https://juliacomputing.com/products/juliapro.html) s předinstalovanými oblíbenými balíčky pro jazyk Julia                         |Ano                      |Ano              |
| Relační databáze                                                            | [SQL Server 2017](https://www.microsoft.com/sql-server/sql-server-2017) <br/> Developer Edition| [PostgreSQL](https://www.postgresql.org/) (CentOS),<br/>[SQL Server 2017](https://www.microsoft.com/sql-server/sql-server-2017) <br/> Developer Edition (Ubuntu) |
| Databázové nástroje                                                       | * SQL Server Management Studio <br/>* SQL Server Integration Services<br/>* [bcp, sqlcmd](https://docs.microsoft.com/sql/tools/command-prompt-utility-reference-database-engine)<br /> * Ovladače ODBC/JDBC| * [SQuirreL SQL](http://squirrel-sql.sourceforge.net/) (nástroj pro dotazování), <br /> * bcp, sqlcmd <br /> * Ovladače ODBC/JDBC|
| Škálovatelná analýza v databázi pomocí služeb SQL Server ML (R, Python) | Ano     |Ne              |
| **[Jupyter Notebook Server](https://jupyter.org/) s následujícími jádry:**                                  | Ano     | Ano |
|     &nbsp;&nbsp;&nbsp;&nbsp;* R | Ano | Ano |
|     &nbsp;&nbsp;&nbsp;&nbsp;* Python | Ano | Ano |
|     &nbsp;&nbsp;&nbsp;&nbsp;* Julia | Ano | Ano |
|     &nbsp;&nbsp;&nbsp;&nbsp;* PySpark | Ano | Ano |
|     &nbsp;&nbsp;&nbsp;&nbsp;* [Sparkmagic](https://github.com/jupyter-incubator/sparkmagic) | Ne | Ano (pouze Ubuntu) |
|     &nbsp;&nbsp;&nbsp;&nbsp;* SparkR     | Ne | Ano |
| JupyterHub (server poznámkového bloku pro více uživatelů)| Ne | Ano |
| JupyterLab (server poznámkového bloku pro více uživatelů) | Ne | Ano (pouze Ubuntu) |
| **Vývojové nástroje, IDEs a editory kódu**| | |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Visual Studio 2019 (Community Edition)](https://www.visualstudio.com/community/) s modulem plug-in Git, Azure HDInsight (Hadoop), Data Lake, SQL Server Data Tools, [Node. js](https://github.com/Microsoft/nodejstools), [Python](https://aka.ms/ptvs)a [nástroje R pro Visual Studio (RTVS)](https://microsoft.github.io/RTVS-docs/) | Ano | Ne |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Visual Studio Code](https://code.visualstudio.com/) | Ano | Ano |
| &nbsp;&nbsp;&nbsp;&nbsp;* [RStudio Desktop](https://www.rstudio.com/products/rstudio/#Desktop) | Ano | Ano |
| &nbsp;&nbsp;&nbsp;&nbsp;* [RStudio Server](https://www.rstudio.com/products/rstudio/#Server) | Ne | Ano |
| &nbsp;&nbsp;&nbsp;&nbsp;* [PyCharm Community Edition](https://www.jetbrains.com/pycharm/) | Ne | Ano |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Atom](https://atom.io/) | Ne | Ano |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Juno (Julia IDE)](https://junolab.org/)| Ano | Ano |
| &nbsp;&nbsp;&nbsp;&nbsp;* Vim a Emacs | Ano | Ano |
| &nbsp;&nbsp;&nbsp;&nbsp;* Git a GitBash | Ano | Ano |
| &nbsp;&nbsp;&nbsp;&nbsp;* OpenJDK | Ano | Ano |
| &nbsp;&nbsp;&nbsp;&nbsp;* .NET Framework | Ano | Ne |
| Power BI Desktop | Ano | Ne |
| Sady SDK pro přístup ke službám Azure a Cortana Intelligence Suite | Ano | Ano |
| **Nástroje pro přesun a správu dat** | | |
| &nbsp;&nbsp;&nbsp;&nbsp;* Průzkumník služby Azure Storage | Ano | Ano |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Azure CLI](https://docs.microsoft.com/cli/azure) | Ano | Ano |
| &nbsp;&nbsp;&nbsp;&nbsp;* Azure Powershell | Ano | Ne |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Azcopy](https://docs.microsoft.com/azure/storage/storage-use-azcopy) | Ano | Ne |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Ovladač Blob FUSE](https://github.com/Azure/azure-storage-fuse) | Ne | Ano |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Adlcopy (Azure Data Lake Storage)](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-copy-data-azure-storage-blob) | Ano | Ne |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Nástroj pro migraci dat DocDB](https://docs.microsoft.com/azure/documentdb/documentdb-import-data) | Ano | Ne |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Brána brány Microsoft Správa dat](https://msdn.microsoft.com/library/dn879362.aspx): Přesun dat mezi OnPrem a cloudem | Ano | Ne |
| &nbsp;&nbsp;&nbsp;&nbsp;* Nástroje příkazového řádku Unixu/Linuxu | Ano | Ano |
| [Apache Drill](https://drill.apache.org) pro průzkum dat | Ano | Ano |
| **Nástroje pro strojové učení** |||
| &nbsp;&nbsp;&nbsp;&nbsp;* Integrace s [Azure Machine Learningem](https://azure.microsoft.com/services/machine-learning/) (R, Python) | Ano | Ano |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Xgboost](https://github.com/dmlc/xgboost) | Ano | Ano |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Vowpal Wabbit](https://github.com/JohnLangford/vowpal_wabbit) | Ano | Ano |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Weka](https://www.cs.waikato.ac.nz/ml/weka/) | Ano | Ano |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Rattle](https://togaware.com/rattle/) | Ano | Ano |
| &nbsp;&nbsp;&nbsp;&nbsp;* [LightGBM](https://github.com/Microsoft/LightGBM) | Ne | Ano (pouze Ubuntu) |
| &nbsp;&nbsp;&nbsp;&nbsp;* [CatBoost](https://tech.yandex.com/catboost/) | Ne | Ano (pouze Ubuntu) |
| &nbsp;&nbsp;&nbsp;&nbsp;* [](https://www.h2o.ai/h2o/)Jiskrová, [sparková voda](https://www.h2o.ai/sparkling-water/) | Ne | Ano (pouze Ubuntu) |
| **Nástroje pro hloubkové učení** <br>Všechny nástroje fungují na grafickém procesoru nebo procesoru. |  |  |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Microsoft Cognitive Toolkit (CNTK)](https://docs.microsoft.com/cognitive-toolkit/) (Windows 2016) | Ano | Ano |
| &nbsp;&nbsp;&nbsp;&nbsp;* [TensorFlow](https://www.tensorflow.org/) | Ano (Windows 2016) | Ano |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Horovod](https://github.com/uber/horovod) | Ne | Ano (Ubuntu) |
| &nbsp;&nbsp;&nbsp;&nbsp;* [MXNet](https://mxnet.io/) | Ano (Windows 2016) | Ano|
| &nbsp;&nbsp;&nbsp;&nbsp;* [Caffe & Caffe2](https://github.com/caffe2/caffe2) | Ne | Ano |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Chainer](https://chainer.org/) | Ne | Ano |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Torch](http://torch.ch/) | Ne | Ano |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Theano](https://github.com/Theano/Theano) | Ne | Ano |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Keras](https://keras.io/)| Ne | Ano |
| &nbsp;&nbsp;&nbsp;&nbsp;* [PyTorch](https://pytorch.org/)| Ne | Ano |
| &nbsp;&nbsp;&nbsp;&nbsp;* [NVidia Digits](https://github.com/NVIDIA/DIGITS) | Ne | Ano |
| &nbsp;&nbsp;&nbsp;&nbsp;* [MXNet Model Server](https://github.com/awslabs/mxnet-model-server) | Ne | Ano |
| &nbsp;&nbsp;&nbsp;&nbsp;* [TensorFlow Serving](https://www.tensorflow.org/serving/) | Ne | Ano |
| &nbsp;&nbsp;&nbsp;&nbsp;* [TensorRT](https://developer.nvidia.com/tensorrt) | Ne | Ano |
| &nbsp;&nbsp;&nbsp;&nbsp;* [CUDA, cuDNN, ovladač NVIDIA](https://developer.nvidia.com/cuda-toolkit) | Ano | Ano |

## <a name="next-steps"></a>Další postup

Další informace najdete v těchto článcích:

+ Windows:
  + [Nastavení DSVM pro Windows](provision-vm.md)
  + [10 věcí, které můžete provádět na DSVM Windows](vm-do-ten-things.md)

+ Linux:
  + [Nastavení DSVM pro Linux (Ubuntu)](dsvm-ubuntu-intro.md)
  + [Nastavení DSVM pro Linux (CentOS)](linux-dsvm-intro.md)
  + [Datové vědy na DSVM pro Linux](linux-dsvm-walkthrough.md)
