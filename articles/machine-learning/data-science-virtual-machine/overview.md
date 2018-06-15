---
title: Úvod k virtuálnímu počítači pro datové vědy pro Linux a Windows | Microsoft Docs
description: Důležité analytické scénáře a komponenty pro virtuální počítače pro datové vědy pro Windows a Linux
keywords: nástroje pro datové vědy, virtuální počítač pro datové vědy, datové vědy pro linux
services: machine-learning
documentationcenter: ''
author: gopitk
manager: cgronlun
ms.assetid: d4f91270-dbd2-4290-ab2b-b7bfad0b2703
ms.service: machine-learning
ms.component: data-science-vm
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 10/27/2017
ms.author: gokuma
ms.openlocfilehash: 9ef6b216889416ea00786dcd3043d6e0f246b305
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/19/2018
ms.locfileid: "31595469"
---
# <a name="introduction-to-azure-data-science-virtual-machine-for-linux-and-windows"></a>Úvod k virtuálnímu počítači pro datové vědy pro Linux a Windows

Virtuální počítač pro datové vědy je přizpůsobená image virtuálního počítače v cloudu Azure Microsoftu vytvořená speciálně pro účely datových věd. Obsahuje řadu oblíbených předinstalovaných nástrojů datové vědy a dalších funkcí a je předem nakonfigurovaná, abyste mohli hned začít sestavovat inteligentní aplikace pro pokročilou analýzu. Je k dispozici pro Windows Server a Linux. Edici virtuálního počítače pro datové vědy pro Windows nabízíme ve verzích pro Server 2016 a Server 2012. Edice virtuálního počítače pro datové vědy pro Linux nabízíme ve verzích pro Ubuntu 16.04 LTS a CentOS 7.4.

Toto téma popisuje, co můžete s virtuálním počítačem pro datové vědy dělat, nastiňuje několik důležitých scénářů použití virtuálního počítače, uvádí výčet klíčových funkcí, které jsou ve verzích pro Windows a Linux k dispozici, a poskytuje pokyny, jak je začít používat.


## <a name="what-can-i-do-with-the-data-science-virtual-machine"></a>K čemu mohu využít virtuální počítač pro datové vědy?
Virtuální počítač pro datové vědy je určen odborníkům pracujícím s daty všech úrovní a rolí, kterým poskytuje bezproblémové předkonfigurované a plně integrované prostředí pro vědecké zpracování dat. Místo zavádění srovnatelného pracovního prostoru vlastními silami můžete zřídit virtuální počítač pro datové vědy a tím si ušetřit dny nebo dokonce _týdny_ práce s instalací, konfigurací a správou balíčků. Po přidělení virtuálního počítače pro datové vědy můžete okamžitě začít pracovat na svém projektu datové vědy.

Virtuální počítač pro datové vědy je nakonfigurován a navržen pro širokou řadu scénářů použití. Podle měnících se potřeb projektu můžete kapacitu prostředí vertikálně navýšit nebo snížit. K programování úloh datové vědy můžete využít upřednostňovaný jazyk. Můžete si nainstalovat i další nástroje a přizpůsobit systém přesně tak, jak potřebujete.

## <a name="key-scenarios"></a>Hlavní scénáře
V této části najdete několik hlavních scénářů, pro které můžete virtuální počítač pro datové vědy nasadit.

### <a name="preconfigured-analytics-desktop-in-the-cloud"></a>Předkonfigurovaný analytický desktop v cloudu
Virtuální počítač pro datové vědy poskytuje základní konfiguraci týmům pro datovou vědu, které chtějí nahradit svoje desktopy spravovaným cloudovým desktopem. Tato základní konfigurace zajišťuje, že všichni odborníci přes data v týmu mají konzistentní nastavení pro ověřování experimentů a spolupráci. Šetří také náklady, protože snižuje režii správců systémů a zkracuje čas nutný k vyhodnocení, instalaci a údržbě různých softwarových balíčků, které jsou pro pokročilé analýzy nezbytné.  

### <a name="data-science-training-and-education"></a>Školení a vzdělávání v oblasti datové vědy
Podnikoví školitelé a lektoři, kteří přednášejí vědecké zpracování dat, obvykle poskytují image virtuálního počítače, aby měli jistotu, že jejich studenti mají konzistentní nastavení a že ukázky fungují tak, jak mají. Virtuální počítač pro datové vědy vytvoří prostředí na vyžádání s konzistentním nastavením, které usnadňuje podporu a omezuje problémy s nekompatibilitou. Užitečný je zejména v případech, kdy je potřeba taková prostředí vytvářet často, například pro kratší školení.

### <a name="on-demand-elastic-capacity-for-large-scale-projects"></a>Přizpůsobivá kapacita na vyžádání pro velké projekty
Hackathony nebo soutěže v oblasti datových věd nebo rozsáhlé modelování či průzkum dat vyžadují – obvykle na krátkou dobu – horizontální navýšení kapacity hardwaru. Virtuální počítač pro datové vědy umožňuje rychle replikovat prostředí pro vědecké zpracování dat na vyžádání na serverech s horizontálně navýšenou kapacitou, které umožňují spouštět experimenty vyžadující výpočetní prostředky s vysokým výkonem.

### <a name="short-term-experimentation-and-evaluation"></a>Krátkodobé experimenty a vyhodnocování
S minimálním úsilím věnovaným nastavení je možné virtuální počítač pro datovou vědu využít k vyhodnocení nebo výuce nástrojů, jako jsou Microsoft ML Server, SQL Server, nástroje sady Visual Studio, Jupyter, sady nástrojů pro hloubkové nebo strojové učení a nové nástroje oblíbené v komunitě. Díky rychlému nastavení můžete virtuální počítač pro datovou vědu využít i v dalších scénářích krátkodobého použití, jako jsou replikace publikovaných experimentů, prezentace ukázek, procházejí postupů v online relacích nebo konferenčních kurzech.

### <a name="deep-learning"></a>Hloubkové učení
Virtuální počítač pro datovou vědu můžete využít pro tréninkový model s použitím algoritmů hloubkového učení na hardwaru založeného na grafickém procesoru. S využitím možností škálování virtuálního počítače v cloudu Azure vám virtuální počítač pro datovou vědu umožňuje používat hardware založený na grafickém procesoru v cloudu podle potřeby. Při tréninku velkých modelů nebo potřebě vysokorychlostních výpočtů je možné přepnout na virtuální počítač založený na grafickém procesoru a stále zůstat na stejném disku s operačním systémem.  Verze Windows Serveru 2016 virtuálního počítače pro datovou vědu je dodávána s předinstalovanými ovladači grafického procesoru, architekturami a verzemi grafického procesoru architektur hloubkového učení. Na Linuxu je hloubkové učení na grafickém procesoru povoleno jak na virtuálním počítači pro datovou vědu CentOS, tak i Ubuntu. Edici 2016 virtuálního počítače pro datové vědy pro Ubuntu, CentOS nebo Windows můžete nasadit na virtuální počítač Azure, který není založený na grafickém procesoru. V takovém případě všechny architektury hloubkového učení přejdou do režimu procesoru. 

## <a name="whats-included-in-the-data-science-vm"></a>Co je součástí virtuálního počítače pro datovou vědu?
Virtuální počítač pro datovou vědu obsahuje mnoho oblíbených nástrojů pro datovou vědu a hloubkové učení, které jsou již nainstalovány a nakonfigurovány. Obsahuje také nástroje, které usnadňují práci s různými produkty Azure pro data a analýzu. Pomocí Microsoft ML Serveru (R, Python) nebo SQL Serveru 2017 můžete prozkoumávat a vytvářet prediktivní modely u rozsáhlých sad dat. Součástí je také hostitel dalších nástrojů od komunity open source a od Microsoftu, ale i vzorový kód a poznámkové bloky. V následující tabulce najdete výčet a srovnání hlavních komponent, které jsou součástí edic virtuálního počítače pro datovou vědu pro Windows a Linux.


| **Nástroj**                                                           | **Edice pro Windows** | **Edice pro Linux** |
| :------------------------------------------------------------------ |:-------------------:|:------------------:|
| [Microsoft R Open](https://mran.microsoft.com/open/) s předinstalovanými oblíbenými balíčky   |Ano                      | Ano             |
| [Microsoft ML Server (R, Python)](https://docs.microsoft.com/machine-learning-server/) Developer Edition zahrnuje: <br />  &nbsp;&nbsp;&nbsp;&nbsp;* [RevoScaleR/revoscalepy](https://docs.microsoft.com/machine-learning-server/r/concept-what-is-revoscaler) – paralelní a distribuovaná architektura s vysokým výkonem (R & Python)<br />  &nbsp;&nbsp;&nbsp;&nbsp;* [MicrosoftML](https://docs.microsoft.com/machine-learning-server/r/concept-what-is-the-microsoftml-package) – nové moderní algoritmy strojového učení od Microsoftu <br />  &nbsp;&nbsp;&nbsp;&nbsp;* [Operacionalizace R a Python](https://docs.microsoft.com/machine-learning-server/what-is-operationalization)                                            |Ano                      | Ano |
| [Microsoft Office](https://products.office.com/en-us/business/office-365-proplus-business-software) Pro-Plus se sdílenou aktivací – Excel, Word a PowerPoint   |Ano                      |Ne              |
| [Anaconda Python](https://www.continuum.io/) 2.7, 3.5 s předinstalovanými oblíbenými balíčky    |Ano                      |Ano              |
| [JuliaPro](https://juliacomputing.com/products/juliapro.html) s předinstalovanými oblíbenými balíčky pro jazyk Julia                         |Ano                      |Ano              |
| Relační databáze                                                            | [SQL Server 2017](https://www.microsoft.com/sql-server/sql-server-2017) <br/> Developer Edition| [PostgreSQL](https://www.postgresql.org/) (CentOS),<br/>[SQL Server 2017](https://www.microsoft.com/sql-server/sql-server-2017) <br/> Developer Edition (Ubuntu) |
| Databázové nástroje                                                       | * SQL Server Management Studio <br/>* SQL Server Integration Services<br/>* [bcp, sqlcmd](https://docs.microsoft.com/sql/tools/command-prompt-utility-reference-database-engine)<br /> * Ovladače ODBC/JDBC| * [SQuirreL SQL](http://squirrel-sql.sourceforge.net/) (nástroj pro dotazování), <br /> * bcp, sqlcmd <br /> * Ovladače ODBC/JDBC|
| Škálovatelná analýza v databázi pomocí služeb SQL Server ML (R, Python) | Ano     |Ne              |
| **[Jupyter Notebook Server](http://jupyter.org/) s následujícími jádry:**                                  | Ano     | Ano |
|     &nbsp;&nbsp;&nbsp;&nbsp;* R | Ano | Ano |
|     &nbsp;&nbsp;&nbsp;&nbsp;* Python | Ano | Ano |
|     &nbsp;&nbsp;&nbsp;&nbsp;* Julia | Ano | Ano |
|     &nbsp;&nbsp;&nbsp;&nbsp;* PySpark | Ano | Ano |
|     &nbsp;&nbsp;&nbsp;&nbsp;* [Sparkmagic](https://github.com/jupyter-incubator/sparkmagic) | Ne | Ano (pouze Ubuntu) |
|     &nbsp;&nbsp;&nbsp;&nbsp;* SparkR     | Ne | Ano |
| JupyterHub (server poznámkového bloku pro více uživatelů)| Ne | Ano |
| JupyterLab (server poznámkového bloku pro více uživatelů) | Ne | Ano (pouze Ubuntu) |
| **Vývojové nástroje, integrovaná vývojová prostředí a editory kódu**| | |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Visual Studio 2017 (Community Edition)](https://www.visualstudio.com/community/) s modulem plug-in Git, Azure HDInsight (Hadoop), Data Lake, nástroji SQL Server Data Tools, [Node.js](https://github.com/Microsoft/nodejstools), [Pythonem](http://aka.ms/ptvs) a [nástroji R pro Visual Studio (RTVS)](http://microsoft.github.io/RTVS-docs/) | Ano | Ne |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Visual Studio Code](https://code.visualstudio.com/) | Ano | Ano |
| &nbsp;&nbsp;&nbsp;&nbsp;* [RStudio Desktop](https://www.rstudio.com/products/rstudio/#Desktop) | Ano | Ano |
| &nbsp;&nbsp;&nbsp;&nbsp;* [RStudio Server](https://www.rstudio.com/products/rstudio/#Server) | Ne | Ano |
| &nbsp;&nbsp;&nbsp;&nbsp;* [PyCharm Community Edition](https://www.jetbrains.com/pycharm/) | Ne | Ano |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Atom](https://atom.io/) | Ne | Ano |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Juno (Julia IDE)](http://junolab.org/)| Ano | Ano |
| &nbsp;&nbsp;&nbsp;&nbsp;* Vim a Emacs | Ano | Ano |
| &nbsp;&nbsp;&nbsp;&nbsp;* Git a GitBash | Ano | Ano |
| &nbsp;&nbsp;&nbsp;&nbsp;* OpenJDK | Ano | Ano |
| &nbsp;&nbsp;&nbsp;&nbsp;* .Net Framework | Ano | Ne |
| PowerBI Desktop | Ano | Ne |
| Sady SDK pro přístup ke službám Azure a Cortana Intelligence Suite | Ano | Ano |
| **Nástroje pro přesun a správu dat** | | |
| &nbsp;&nbsp;&nbsp;&nbsp;* Průzkumník služby Azure Storage | Ano | Ano |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Azure CLI](https://docs.microsoft.com/cli/azure) | Ano | Ano |
| &nbsp;&nbsp;&nbsp;&nbsp;* Azure Powershell | Ano | Ne |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Azcopy](https://docs.microsoft.com/azure/storage/storage-use-azcopy) | Ano | Ne |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Ovladač Blob FUSE](https://github.com/Azure/azure-storage-fuse) | Ne | Ano |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Adlcopy (Azure Data Lake Storage)](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-copy-data-azure-storage-blob) | Ano | Ne |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Nástroj pro migraci dat DocDB](https://docs.microsoft.com/azure/documentdb/documentdb-import-data) | Ano | Ne |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Brána pro správu dat společnosti Microsoft](https://msdn.microsoft.com/library/dn879362.aspx): Přesun dat mezi místním úložištěm a cloudem | Ano | Ne |
| &nbsp;&nbsp;&nbsp;&nbsp;* Nástroje příkazového řádku Unixu/Linuxu | Ano | Ano |
| [Apache Drill](http://drill.apache.org) pro průzkum dat | Ano | Ano |
| **Nástroje pro strojové učení** |||
| &nbsp;&nbsp;&nbsp;&nbsp;* Integrace s [Azure Machine Learningem](https://azure.microsoft.com/services/machine-learning/) (R, Python) | Ano | Ano |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Xgboost](https://github.com/dmlc/xgboost) | Ano | Ano |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Vowpal Wabbit](https://github.com/JohnLangford/vowpal_wabbit) | Ano | Ano |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Weka](http://www.cs.waikato.ac.nz/ml/weka/) | Ano | Ano |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Rattle](http://rattle.togaware.com/) | Ano | Ano |
| &nbsp;&nbsp;&nbsp;&nbsp;* [LightGBM](https://github.com/Microsoft/LightGBM) | Ne | Ano (pouze Ubuntu) |
| &nbsp;&nbsp;&nbsp;&nbsp;* [H2O](https://www.h2o.ai/h2o/), [Sparkling Water](https://www.h2o.ai/sparkling-water/), [Deep Water](https://www.h2o.ai/deep-water/) | Ne | Ano (pouze Ubuntu) |
| **Nástroje pro hloubkové učení** <br>Všechny nástroje fungují na grafickém procesoru nebo procesoru. |  |  |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Microsoft Cognitive Toolkit (CNTK)](https://www.microsoft.com/en-us/cognitive-toolkit/) (Windows 2016) | Ano | Ano |
| &nbsp;&nbsp;&nbsp;&nbsp;* [TensorFlow](https://www.tensorflow.org/) | Ano (Windows 2016) | Ano |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Horovod](https://github.com/uber/horovod) | Ne | Ano (Ubuntu) |
| &nbsp;&nbsp;&nbsp;&nbsp;* [MXNet](http://mxnet.io/) | Ano (Windows 2016) | Ano|
| &nbsp;&nbsp;&nbsp;&nbsp;* [Caffe & Caffe2](https://github.com/caffe2/caffe2) | Ne | Ano |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Chainer](https://chainer.org/) | Ne | Ano |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Torch](http://torch.ch/) | Ne | Ano |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Theano](https://github.com/Theano/Theano) | Ne | Ano |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Keras](https://keras.io/)| Ne | Ano |
| &nbsp;&nbsp;&nbsp;&nbsp;* [PyTorch](http://pytorch.org/)| Ne | Ano |
| &nbsp;&nbsp;&nbsp;&nbsp;* [NVidia Digits](https://github.com/NVIDIA/DIGITS) | Ne | Ano |
| &nbsp;&nbsp;&nbsp;&nbsp;* [MXNet Model Server](https://github.com/awslabs/mxnet-model-server) | Ne | Ano |
| &nbsp;&nbsp;&nbsp;&nbsp;* [TensorFlow Serving](https://www.tensorflow.org/serving/) | Ne | Ano |
| &nbsp;&nbsp;&nbsp;&nbsp;* [TensorRT](https://developer.nvidia.com/tensorrt) | Ne | Ano |
| &nbsp;&nbsp;&nbsp;&nbsp;* [CUDA, cuDNN, ovladač NVIDIA](https://developer.nvidia.com/cuda-toolkit) | Ano | Ano |
| **Big Data Platform (pouze Devtest)**|||
| &nbsp;&nbsp;&nbsp;&nbsp;* Místní samostatný [Spark](http://spark.apache.org/) | Ano | Ano |
| &nbsp;&nbsp;&nbsp;&nbsp;* Místní [Hadoop](http://hadoop.apache.org/) (HDFS, YARN) | Ne | Ano |

## <a name="get-started"></a>Začínáme

### <a name="windows-data-science-vm"></a>Virtuální počítač pro datovou vědu pro Windows
* Další informace o vytvoření virtuálního počítače pro datovou vědu pro Windows a jeho použití najdete v článku [Zřízení virtuálního počítače pro datovou vědu pro Windows](provision-vm.md). Další informace o provádění různých úloh potřebných pro projekt vědeckého zpracování dat na virtuálním počítači pro datovou vědu pro Windows najdete v článku [Deset věcí, které můžete provádět na virtuálních počítačích pro datovou vědu](vm-do-ten-things.md).

### <a name="linux-data-science-vm"></a>Virtuální počítač pro datovou vědu pro Linux
* Další informace o vytvoření virtuálního počítače pro datovou vědu pro Ubuntu a jeho použití najdete v článku [Zřízení virtuálního počítače pro datovou vědu pro Linux (Ubuntu)](dsvm-ubuntu-intro.md). Další informace o vytvoření virtuálního počítače pro datovou vědu pro CentOS a jeho použití najdete v článku [Zřízení virtuálního počítače pro datovou vědu pro Linux CentOS](linux-dsvm-intro.md).
* Postup provedení několika běžných úloh vědeckého zpracování dat na virtuálním počítači Linux (v systémech CentOS a Ubuntu) najdete v článku [Datová věda na virtuálním počítači pro datovou vědu pro Linux](linux-dsvm-walkthrough.md).

