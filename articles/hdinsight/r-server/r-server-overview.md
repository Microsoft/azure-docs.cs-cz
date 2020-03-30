---
title: Úvod ke službám ML na Azure HDInsight
description: Přečtěte si, jak pomocí služeb ML na HDInsight vytvářet aplikace pro analýzu velkých objemů dat.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: overview
ms.date: 02/10/2020
ms.openlocfilehash: a77771880da962298f6e80782e5f3e251f5f4641
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "77122370"
---
# <a name="what-is-ml-services-in-azure-hdinsight"></a>Co je služba ML ve službě Azure HDInsight

Microsoft Machine Learning Server je k dispozici jako možnost nasazení při vytváření clusterů HDInsight v Azure. Typ clusteru, který poskytuje tuto možnost, se nazývá **ML Services**. Tato funkce poskytuje datovým vědcům, statistikům a programátorům R přístup k škálovatelným distribuovaným metodám analýzy na hdinsightu na vyžádání.

Ml Services na HDInsight poskytuje nejnovější funkce pro analýzy založené na R na datové sady prakticky libovolné velikosti, načíst buď Azure Blob nebo úložiště datového jezera. Vzhledem k tomu, že cluster ml services je postaven na open source R, aplikace založené na R, které vytvoříte můžete využít některý z 8000 + open source R balíčky. Rutiny v ScaleR, Microsoft big data analytics balíček jsou také k dispozici.

Hraniční uzel clusteru poskytuje vhodné místo pro připojení ke clusteru a spuštění skriptů R. S hraničním uzlem máte možnost spouštět paralelizované distribuované funkce ScaleR napříč jádry hraničního uzlu serveru. Můžete je také spustit přes uzly clusteru pomocí vertoop mapy snížit nebo Apache Spark výpočetní kontexty.

Modely nebo předpovědi, které jsou výsledkem analýzy lze stáhnout pro místní použití. Zprovozněny je taky jinde v Azure, zejména prostřednictvím [klasické webové služby](../../machine-learning/studio/deploy-a-machine-learning-web-service.md) [Azure Machine Learning Studio (klasické).](https://studio.azureml.net)

## <a name="get-started-with-ml-services-on-hdinsight"></a>Začínáme se službami ML na HDInsightu

Pokud chcete vytvořit cluster služeb ML v Azure HDInsight, vyberte typ clusteru **SLUŽBY ML** při vytváření clusteru HDInsight pomocí portálu Azure. Typ clusteru služby ML Services zahrnuje server ML server na datových uzlech clusteru a na hraničním uzlu, který slouží jako vstupní zóna pro analýzu založenou na službách ML Services. Viz [Vytvoření clusterů Apache Hadoop pomocí portálu Azure](../hdinsight-hadoop-create-linux-clusters-portal.md) pro návod, jak vytvořit cluster.

## <a name="why-choose-ml-services-in-hdinsight"></a>Proč zvolit služby ML ve službě HDInsight?

Služby ML ve službě HDInsight poskytují následující výhody:

### <a name="ai-innovation-from-microsoft-and-open-source"></a>Inovace v oblasti umělá iv od Microsoftu a open source

  Ml Services zahrnuje vysoce škálovatelné, distribuované sady algoritmů, jako je [Například RevoscaleR](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/revoscaler), [revoscalepy](https://docs.microsoft.com/machine-learning-server/python-reference/revoscalepy/revoscalepy-package)a [MicrosoftML,](https://docs.microsoft.com/machine-learning-server/python-reference/microsoftml/microsoftml-package) které mohou pracovat na velikosti dat větší než velikost fyzické paměti a spustit na široké škále platforem distribuovaným způsobem. Další informace o kolekci vlastních [balíčků R](https://docs.microsoft.com/machine-learning-server/r-reference/introducing-r-server-r-package-reference) společnosti Microsoft a [balíčků Pythonu,](https://docs.microsoft.com/machine-learning-server/python-reference/introducing-python-package-reference) které jsou součástí produktu.
  
  Služba ML Services tyto inovace a příspěvky společnosti Microsoft, které pocházejí z open source komunity (sady nástrojů R, Python a AI), převyšují nad jedinou platformu podnikové úrovně. Jakýkoli balíček r nebo pythonopen source strojového učení může pracovat bok po boku s jakoukoli proprietární inovací od společnosti Microsoft.

### <a name="simple-secure-and-high-scale-operationalization-and-administration"></a>Jednoduchá, bezpečná a rozsáhlá operativizace a správa

  Podniky, které se spoléhají na tradiční paradigmata a prostředí, investují mnoho času a úsilí do zprovoznění. To má za následek nafouknuté náklady a zpoždění, včetně doby překladu pro modely, iterací, aby byly platné a aktuální, schválení regulačních orgánů a správa oprávnění prostřednictvím zprovoznění.

  Ml Services nabízí podnikové provozní [nastavení](https://docs.microsoft.com/machine-learning-server/what-is-operationalization)v tom, že po dokončení modelu strojového učení trvá generování webových služeb api. Tyto [webové služby](https://docs.microsoft.com/machine-learning-server/operationalize/concept-what-are-web-services) jsou hostovány na serverové mřížce v cloudu a lze je integrovat s obchodními aplikacemi. Možnost nasazení do elastické mřížky umožňuje bezproblémové škálování podle potřeb vaší firmy, a to jak pro dávkové, tak pro vyhodnocování v reálném čase. Pokyny naleznete v [tématu Operationalize ML Services on HDInsight](r-server-operationalize.md).

<!---
* **Deep ecosystem engagements to deliver customer success with optimal total cost of ownership**

  Individuals embarking on the journey of making their applications intelligent or simply wanting to learn the new world of AI and machine learning, need the right resources to help them get started. In addition to this documentation, Microsoft provides several learning resources and has engaged several training partners to help you ramp up and become productive quickly.
--->

> [!NOTE]  
> Typ clusteru služby ML services na rozhraní HDInsight je podporován pouze v rozhraní HDInsight 3.6. HDInsight 3.6 je naplánováno na odchod do důchodu v prosinci 31, 2020.

## <a name="key-features-of-ml-services-on-hdinsight"></a>Klíčové vlastnosti ml služeb na HDInsight

Následující funkce jsou zahrnuty ve službách ML na HDInsight.

| Kategorie funkcí | Popis |
|------------------|-------------|
| R-povoleno | [R balíčky](https://docs.microsoft.com/machine-learning-server/r-reference/introducing-r-server-r-package-reference) pro řešení napsaná v R, s open source distribuce R a run-time infrastruktury pro spuštění skriptu. |
| S podporou Pythonu | [Moduly Pythonu](https://docs.microsoft.com/machine-learning-server/python-reference/introducing-python-package-reference) pro řešení napsaná v Pythonu s open source distribucí Pythonu a run-time infrastrukturou pro spouštění skriptů.
| [Předem vycvičené modely](https://docs.microsoft.com/machine-learning-server/install/microsoftml-install-pretrained-models) | Pro vizuální analýzu a analýzu mínění textu připraveno k vyhodzení dat, která zadáte. |
| [Nasazení a využití](r-server-operationalize.md) | Zprovoznit server a nasadit řešení jako webovou službu. |
| [Vzdálené spuštění](r-server-hdinsight-manage.md#connect-remotely-to-microsoft-ml-services) | Spusťte vzdálené relace v clusteru služby ML v síti z klientské pracovní stanice. |


## <a name="data-storage-options-for-ml-services-on-hdinsight"></a>Možnosti ukládání dat pro služby ML na HDInsightu

Výchozí úložiště pro souborový systém HDFS clusterů HDInsight lze přidružit buď k účtu Azure Storage, nebo k úložišti Azure Data Lake Storage. Toto přidružení zajišťuje, že jakákoli data odeslaná do úložiště clusteru během analýzy jsou trvalá a data jsou k dispozici i po odstranění clusteru. Existují různé nástroje pro zpracování přenosu dat do možnosti úložiště, které vyberete, včetně portálu naupload zařízení účtu úložiště a nástroj [AzCopy.](../../storage/common/storage-use-azcopy.md)

Máte možnost povolit přístup k dalším úložištím objektů Blob a datových jezer během procesu zřizování clusteru bez ohledu na možnost primárního úložiště, která se používá.  Další informace o používání více účtů úložiště najdete v článku [Možnosti úložiště Azure pro služby ML na webu HDInsight.](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-r-server-storage)

[Soubory Azure](../../storage/files/storage-how-to-use-files-linux.md) můžete také použít jako možnost úložiště pro použití na hraničním uzlu. Azure Files umožňuje připojit sdílenou složku, která byla vytvořena ve službě Azure Storage, do systému souborů Linux. Další informace o těchto možnostech ukládání dat pro služby ML v clusteru HDInsight najdete v [tématu Možnosti úložiště Azure pro služby ML na HDInsight](r-server-storage.md).

## <a name="access-ml-services-edge-node"></a>Hraniční uzel služby Access ML Services

K serveru Microsoft ML Server na hraničním uzlu se můžete připojit pomocí prohlížeče. Je nainstalován ve výchozím nastavení během vytváření clusteru.  K hraničnímu uzlu clusteru se můžete také připojit z příkazového řádku pomocí ssh/putty pro přístup ke konzoli R.

## <a name="develop-and-run-r-scripts"></a>Vývoj a spouštění skriptů R

Skripty R, které vytvoříte a spustíte, mohou kromě paralelizovaných a distribuovaných rutin dostupných v knihovně ScaleR používat libovolný z balíčků R s otevřeným zdrojovým kódem 8000+. Obecně platí, že skript, který je spuštěn se službami ML na hraničním uzlu, běží v rámci překladače R v tomto uzlu. Výjimky jsou ty kroky, které je třeba volat scaler funkce s výpočetní kontext, který je nastaven na Hadoop Map Reduce (RxHadoopMR) nebo Spark (RxSpark). V tomto případě funkce běží distribuovaným způsobem napříč těmito daty (úloha) uzly clusteru, které jsou přidruženy k odkazovaná data. Další informace o různých možnostech kontextu výpočetního prostředí najdete v [tématu Možnosti výpočetního kontextu pro služby ML ve službě HDInsight](r-server-compute-contexts.md).

## <a name="operationalize-a-model"></a>Zprovoznění modelu

Po dokončení modelování dat můžete zprovoznit model a vytvořit předpovědi pro nová data z Azure nebo místně. Tento proces se označuje jako vyhodnocování. Vyhodnocování se dá dělat v HDInsightu, Azure Machine Learningu nebo místně.

### <a name="score-in-hdinsight"></a>Skóre v HDInsight

Chcete-li skóre v HDInsight, napište funkci R, která volá váš model, aby předpovědi pro nový datový soubor, který jste načetli do účtu úložiště. Potom uložte předpovědi zpět do účtu úložiště. Tuto rutinu můžete spustit na vyžádání na hraničním uzlu clusteru nebo pomocí naplánované úlohy.

### <a name="score-in-azure-machine-learning-aml"></a>Skóre v Azure Machine Learning (AML)

Pokud chcete skóre pomocí Azure Machine Learning, použijte open source balíček Azure Machine Learning R známý jako [AzureML](https://cran.r-project.org/src/contrib/Archive/AzureML/) k publikování modelu jako webové služby Azure. Pro větší pohodlí je tento balíček předinstalován na hraničním uzlu. Dále použijte zařízení v Azure Machine Learning k vytvoření uživatelského rozhraní pro webovou službu a pak volání webové služby podle potřeby pro vyhodnocování.

Pokud zvolíte tuto možnost, musíte převést všechny objekty modelu ScaleR na ekvivalentní objekty modelu s otevřeným zdrojovým kódem pro použití s webovou službou. Pro tento převod použijte funkce asektercionu, například `as.randomForest()` pro modely založené na souboru.

### <a name="score-on-premises"></a>Skóre v místním prostředí

Chcete-li skóre v místním po vytvoření modelu, můžete serializovat model v R, stáhnout, de-serializovat a pak použít pro vyhodnocování nových dat. Nová data můžete získat pomocí přístupu popsaného výše v [části Skóre v HDInsight](#score-in-hdinsight) nebo pomocí [webových služeb](https://docs.microsoft.com/machine-learning-server/operationalize/concept-what-are-web-services).

## <a name="maintain-the-cluster"></a>Udržovat cluster

### <a name="install-and-maintain-r-packages"></a>Instalace a údržba balíčků R

Většina balíčků R, které používáte, je vyžadována na hraničním uzlu, protože většina kroků skriptů R je zde spuštěna. Chcete-li nainstalovat další balíčky R na `install.packages()` hraniční uzel, můžete použít metodu v R.

Pokud používáte pouze rutiny z knihovny ScaleR v celém clusteru, není obvykle nutné instalovat další balíčky R na datových uzlů. Však může být nutné další balíčky pro podporu použití **rxExec** nebo **RxDataStep** spuštění na datových uzlů.

V takových případech lze další balíčky nainstalovat pomocí akce skriptu po vytvoření clusteru. Další informace naleznete v [tématu Správa služeb ML v clusteru HDInsight](r-server-hdinsight-manage.md).

### <a name="change-apache-hadoop-mapreduce-memory-settings"></a>Změna apache hadoop mapySnížit nastavení paměti

Cluster lze upravit tak, aby se změnilo množství paměti, která je k dispozici služby ML Services při spuštění úlohy MapReduce. Chcete-li upravit cluster, použijte rozhraní Apache Ambari, které je k dispozici prostřednictvím okna portálu Azure pro váš cluster. Pokyny k přístupu k uživatelskému rozhraní Ambari pro váš cluster najdete v [tématu Správa clusterů HDInsight pomocí webového uživatelského rozhraní Ambari](../hdinsight-hadoop-manage-ambari.md).

Je také možné změnit velikost paměti, která je k dispozici ml služby pomocí Hadoop přepínače ve volání **RxHadoopMR** takto:

    hadoopSwitches = "-libjars /etc/hadoop/conf -Dmapred.job.map.memory.mb=6656"  

### <a name="scale-your-cluster"></a>Škálování clusteru

Existující cluster služeb ML na webu HDInsight lze škálovat nahoru nebo dolů prostřednictvím portálu. Škálování nahoru, můžete získat další kapacitu, kterou můžete potřebovat pro větší úlohy zpracování nebo můžete škálovat zpět clusteru, když je nečinný. Pokyny k škálování clusteru najdete v tématu [Správa clusterů HDInsight](../hdinsight-administer-use-portal-linux.md).

### <a name="maintain-the-system"></a>Udržovat systém

Údržba pro použití oprav operačního systému a dalších aktualizací se provádí na podkladových virtuálních počítačích SIF v clusteru HDInsight mimo pracovní dobu. Údržba se obvykle provádí každé pondělí a čtvrtek ve 3:30 (na základě místního času virtuálního počítače). Aktualizace jsou prováděny takovým způsobem, že nemají vliv na více než čtvrtinu clusteru najednou.

Vzhledem k tomu, že hlavní uzly jsou redundantní a ne všechny datové uzly jsou ovlivněny, všechny úlohy, které jsou spuštěny během této doby může zpomalit. Nicméně, měly by ještě běžet až do dokončení. Všechny vlastní software nebo místní data, která máte, jsou zachovány v rámci těchto událostí údržby, pokud nedojde ke katastrofickému selhání, které vyžaduje opětovné sestavení clusteru.

## <a name="ide-options-for-ml-services-on-hdinsight"></a>Možnosti ide pro služby ML na HDInsight

Hraniční uzel Linuxu clusteru HDInsight je vstupní zónou pro analýzu založenou na R. Nejnovější verze HDInsight poskytují výchozí instalaci RStudio Server na hraničním uzlu jako ide založené na prohlížeči. Použití RStudio Server jako IDE pro vývoj a provádění skriptů R může být podstatně produktivnější než jen pomocí konzoly R.

Kromě toho můžete nainstalovat ide plochy a použít jej pro přístup ke clusteru pomocí vzdáleného mapreduce nebo Spark výpočetní kontext. Mezi možnosti patří [nástroje R společnosti](https://marketplace.visualstudio.com/items?itemName=MikhailArkhipov007.RTVS2019) Microsoft pro visual studio (RTVS), RStudio a Walware's Eclipse-based [StatET](http://www.walware.de/goto/statet).

Kromě toho můžete přistupovat ke konzole R na hraničním uzlu zadáním **R** na příkazovém řádku Linuxu po připojení přes SSH nebo PuTTY. Při použití rozhraní konzoly je vhodné spustit textový editor pro vývoj skriptu R v jiném okně a podle potřeby vyjmout a vložit části skriptu do konzoly R.

## <a name="pricing"></a>Ceny

Ceny, které jsou přidruženy k clusteru HDInsight služby ML Services jsou strukturovány podobně jako ceny pro jiné typy clusteru HDInsight. Jsou založeny na velikosti podkladových virtuálních počítačů přes název, data a hraniční uzly s přidáním zvýšení základní hodiny. Další informace naleznete v tématu [HDInsight pricing](https://azure.microsoft.com/pricing/details/hdinsight/).

## <a name="next-steps"></a>Další kroky

Další informace o používání služeb ML v clusterech HDInsight naleznete v následujících tématech:

* [Spuštění skriptu R v clusteru služby ML v Azure HDInsight pomocí serveru RStudio](machine-learning-services-quickstart-job-rstudio.md)
* [Možnosti výpočetního kontextu pro cluster služby ML Services v HDInsight](r-server-compute-contexts.md)
* [Možnosti úložiště pro cluster ML Services na HDInsightu](r-server-storage.md)
