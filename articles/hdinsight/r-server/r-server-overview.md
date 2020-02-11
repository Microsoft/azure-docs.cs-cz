---
title: Seznámení se službami ML ve službě Azure HDInsight
description: Naučte se používat služby ML v HDInsight k vytváření aplikací pro analýzu velkých objemů dat.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: overview
ms.date: 02/10/2020
ms.openlocfilehash: a77771880da962298f6e80782e5f3e251f5f4641
ms.sourcegitcommit: 7c18afdaf67442eeb537ae3574670541e471463d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/11/2020
ms.locfileid: "77122370"
---
# <a name="what-is-ml-services-in-azure-hdinsight"></a>Co je služba ML Services ve službě Azure HDInsight

Microsoft Machine Learning Server je k dispozici jako možnost nasazení při vytváření clusterů HDInsight v Azure. Typ clusteru, který poskytuje tuto možnost, se nazývá **ml Services**. Tato funkce poskytuje odborníkům na data, statistiku a programátory R s přístupem na vyžádání ke škálovatelným distribuovaným metodám analýz v HDInsight.

Služba ML Services v HDInsight poskytuje nejnovější funkce pro analýzy založené na jazyce R v datových sadách prakticky libovolné velikosti, načtené do úložiště objektů blob Azure nebo Data Lake. Vzhledem k tomu, že cluster služby ML Services je postaven na Open Source R, aplikace založené na jazyce R můžou využívat kterýkoli z balíčků 8000 a open-source R. K dispozici jsou také rutiny nástroje pro škálování a analýzu velkých objemů dat od Microsoftu.

Hraniční uzel clusteru poskytuje vhodné místo pro připojení ke clusteru a spouštění skriptů jazyka R. Pomocí hraničního uzlu máte možnost spouštět paralelní distribuované funkce nástroje pro horizontální navýšení kapacity napříč jádry serveru hraničního uzlu. Můžete je také spouštět v uzlech clusteru pomocí mapy Hadoop pro škálování zmenšení nebo Apache Spark výpočetních kontextů.

Modely nebo předpovědi, které vyplývají z analýzy, se dají stáhnout pro místní použití. Můžou být taky v Azure fungující jinde, zejména prostřednictvím [webové služby](../../machine-learning/studio/deploy-a-machine-learning-web-service.md) [Azure Machine Learning Studio (Classic)](https://studio.azureml.net) .

## <a name="get-started-with-ml-services-on-hdinsight"></a>Začínáme se službami ML v HDInsight

Pokud chcete v Azure HDInsight vytvořit cluster služeb ML, vyberte typ clusteru **služby ml Services** při vytváření clusteru HDInsight pomocí Azure Portal. Typ clusteru služby ML obsahuje ML Server v datových uzlech clusteru a na hraničním uzlu, který slouží jako cílová zóna pro analýzu na základě služby ML. Návod, jak vytvořit cluster, najdete v tématu [vytvoření Apache Hadoop clusterů pomocí Azure Portal](../hdinsight-hadoop-create-linux-clusters-portal.md) .

## <a name="why-choose-ml-services-in-hdinsight"></a>Proč zvolit služby ML v HDInsight?

Služby ML ve službě HDInsight přináší následující výhody:

### <a name="ai-innovation-from-microsoft-and-open-source"></a>Inovace AI z Microsoftu a open source

  Služba ML Services zahrnuje vysoce škálovatelnou, distribuovanou sadu algoritmů, jako jsou [RevoscaleR](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/revoscaler), [revoscalepy](https://docs.microsoft.com/machine-learning-server/python-reference/revoscalepy/revoscalepy-package)a [microsoftML](https://docs.microsoft.com/machine-learning-server/python-reference/microsoftml/microsoftml-package) , které mohou pracovat na velikosti dat větší než velikost fyzické paměti, a provozovat širokou škálu platforem distribuovaným způsobem. Přečtěte si další informace o shromažďování vlastních [balíčků R](https://docs.microsoft.com/machine-learning-server/r-reference/introducing-r-server-r-package-reference) a [balíčků Pythonu](https://docs.microsoft.com/machine-learning-server/python-reference/introducing-python-package-reference) společnosti Microsoft, které jsou součástí produktu.
  
  Služby ML přemostění těchto inovací a příspěvků od společnosti Microsoft pocházejících z open source komunity (R, Python a AI) jsou zcela na jedné platformě na podnikové úrovni. Libovolný otevřený zdrojový balíček R nebo Python může fungovat vedle všech vlastnických inovací od Microsoftu.

### <a name="simple-secure-and-high-scale-operationalization-and-administration"></a>Jednoduché, zabezpečené a vysoce škálovatelné zprovoznění a Správa

  Podniky spoléhají na tradiční paradigma a prostředí, které investují mnohem dlouho a úsilí k provozuschopnosti. Výsledkem je neploché náklady a zpoždění, včetně doby překladu pro modely, iterace, aby byly platné a aktuální, zákonné schválení a Správa oprávnění prostřednictvím provozuschopnosti.

  Služba ML Services nabízí [provozuschopnost](https://docs.microsoft.com/machine-learning-server/what-is-operationalization)na podnikové úrovni. v takovém případě je po dokončení modelu strojového učení několik kliknutí, které vygenerují rozhraní API webových služeb. Tyto [webové služby](https://docs.microsoft.com/machine-learning-server/operationalize/concept-what-are-web-services) jsou hostovány v serverové mřížce v cloudu a je možné je integrovat s podnikovými aplikacemi. Možnost nasazení do elastické mřížky vám umožní plynule škálovat požadavky vaší firmy, a to jak pro účely bodování dávky, tak i v reálném čase. Pokyny najdete v tématu [zprovoznění ml Services ve službě HDInsight](r-server-operationalize.md).

<!---
* **Deep ecosystem engagements to deliver customer success with optimal total cost of ownership**

  Individuals embarking on the journey of making their applications intelligent or simply wanting to learn the new world of AI and machine learning, need the right resources to help them get started. In addition to this documentation, Microsoft provides several learning resources and has engaged several training partners to help you ramp up and become productive quickly.
--->

> [!NOTE]  
> Typ clusteru služby ML v HDInsight se podporuje jenom v HDInsight 3,6. Pro HDInsight 3,6 se naplánuje vyřazení do 31. prosince 2020.

## <a name="key-features-of-ml-services-on-hdinsight"></a>Klíčové funkce služby ML v HDInsight

Následující funkce jsou součástí ML Services v HDInsight.

| Kategorie funkce | Popis |
|------------------|-------------|
| R – povoleno | [Balíčky r](https://docs.microsoft.com/machine-learning-server/r-reference/introducing-r-server-r-package-reference) pro řešení napsané v jazyce r s využitím open source distribuce r a běhové infrastruktury pro spuštění skriptu. |
| Python-enabled | [Moduly Pythonu](https://docs.microsoft.com/machine-learning-server/python-reference/introducing-python-package-reference) pro řešení napsané v Pythonu s využitím otevřené distribuce zdrojového kódu Pythonu a běhové infrastruktury pro provádění skriptu.
| [Předem připravené modely](https://docs.microsoft.com/machine-learning-server/install/microsoftml-install-pretrained-models) | Pro vizuální analýzu a analýzu textu mínění připravené k určení skóre dat, která zadáte. |
| [Nasazení a využití](r-server-operationalize.md) | Zprovoznění svůj server a nasaďte řešení jako webovou službu. |
| [Vzdálené spuštění](r-server-hdinsight-manage.md#connect-remotely-to-microsoft-ml-services) | Z pracovní stanice klienta spusťte vzdálené relace v clusteru služby ML Services ve vaší síti. |


## <a name="data-storage-options-for-ml-services-on-hdinsight"></a>Možnosti úložiště dat pro služby ML v HDInsight

Výchozím úložištěm systému souborů HDFS v clusterech HDInsight je možné přidružit účet Azure Storage nebo Azure Data Lake Storage. Toto přidružení zajišťuje trvalé nahrávání všech dat do úložiště clusteru během analýzy a data jsou k dispozici i po odstranění clusteru. Existují různé nástroje pro zpracování přenosu dat do vybrané možnosti úložiště, včetně nahrávání na základě portálu účtu úložiště a nástroje [AzCopy](../../storage/common/storage-use-azcopy.md) .

Máte možnost povolit přístup k dalším objektům blob a datům Lake Store během procesu zřizování clusteru bez ohledu na použitou možnost primárního úložiště.  Další informace o používání více účtů úložiště najdete v článku [možnosti Azure Storage služby ml Services na HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-r-server-storage) .

[Soubory Azure](../../storage/files/storage-how-to-use-files-linux.md) můžete použít také jako možnost úložiště pro použití na hraničním uzlu. Služba soubory Azure umožňuje připojit sdílenou složku, která byla vytvořena v Azure Storage do systému souborů Linux. Další informace o těchto možnostech úložiště dat pro služby ML Services na clusteru HDInsight najdete v tématu [možnosti Azure Storage pro služby ml Services v HDInsight](r-server-storage.md).

## <a name="access-ml-services-edge-node"></a>Hraniční uzel služeb Access ML

Pomocí prohlížeče se můžete připojit k Microsoft ML Server na hraničním uzlu. Ve výchozím nastavení se instaluje při vytváření clusteru.  K přístupu ke konzole R se taky můžete připojit k hraničnímu uzlu clusteru z příkazového řádku pomocí SSH/výstupu.

## <a name="develop-and-run-r-scripts"></a>Vývoj a spouštění skriptů R

Skripty jazyka R, které vytvoříte a spustíte, mohou kromě paralelních a distribuovaných rutin, které jsou k dispozici v knihovně škálování, použít libovolný z balíčků 8000 a open-source. Obecně platí, že skript, který se spouští se službami ML na hraničním uzlu, běží v překladači R na tomto uzlu. Výjimkou jsou kroky, které vyžadují volání funkce škálování s výpočetním kontextem, který je nastaven na RxHadoopMR (Hadoop map COMPUTE) nebo Spark (výpočetního rxspark). V takovém případě je funkce spouštěna v distribuovaném uzlu mezi těmito uzly dat (Task) clusteru, které jsou přidruženy k odkazovanému dat. Další informace o různých možnostech výpočetního kontextu najdete v tématu [Možnosti výpočetního kontextu pro služby ml v HDInsight](r-server-compute-contexts.md).

## <a name="operationalize-a-model"></a>Zprovoznění modelu

Po dokončení modelování dat můžete zprovoznění model a vytvořit předpovědi pro nová data z Azure nebo místního prostředí. Tento proces se označuje jako bodování. Bodování se dá udělat v HDInsight, Azure Machine Learning nebo v místním prostředí.

### <a name="score-in-hdinsight"></a>Skóre v HDInsight

Pokud chcete dosáhnout skóre v HDInsight, napište funkci R, která volá váš model, aby předpovědi nový datový soubor, který jste načetli do svého účtu úložiště. Pak předpovědi uložte zpátky do účtu úložiště. Tuto rutinu můžete spustit na vyžádání na hraničním uzlu clusteru nebo pomocí naplánované úlohy.

### <a name="score-in-azure-machine-learning-aml"></a>Skóre v Azure Machine Learning (AML)

Pokud chcete určit skóre pomocí Azure Machine Learning, použijte Open Source Azure Machine Learning R známý jako [AzureML](https://cran.r-project.org/src/contrib/Archive/AzureML/) k publikování modelu jako webové služby Azure. Pro usnadnění práce je tento balíček předem nainstalován na hraničním uzlu. Dále pomocí zařízení v Azure Machine Learning vytvořte uživatelské rozhraní webové služby a potom zavolejte webovou službu podle potřeby pro vyhodnocování.

Pokud zvolíte tuto možnost, musíte převést objekty modelu nástroje pro horizontální navýšení kapacity na ekvivalentní objekty Open Source modelu pro použití s webovou službou. Pro tento převod použijte funkce pro vynucení horizontálního navýšení, například `as.randomForest()` pro modely založené na kompletech.

### <a name="score-on-premises"></a>Místní skóre

Chcete-li po vytvoření modelu vyhodnotit místní skóre, můžete model serializovat v jazyce R, stáhnout jej, zrušit jeho serializaci a potom ho použít pro vyhodnocování nových dat. Můžete vyhodnotit nová data pomocí výše popsaného přístupu v tématu [skóre v HDInsight](#score-in-hdinsight) nebo pomocí [webových služeb](https://docs.microsoft.com/machine-learning-server/operationalize/concept-what-are-web-services).

## <a name="maintain-the-cluster"></a>Údržba clusteru

### <a name="install-and-maintain-r-packages"></a>Instalace a údržba balíčků R

Většina balíčků R, které použijete, se na hraničním uzlu vyžaduje, protože tam běží většina kroků v skriptech jazyka R. Chcete-li nainstalovat další balíčky R na hraničním uzlu, můžete použít metodu `install.packages()` v jazyce R.

Pokud pouze používáte rutiny z knihovny škálování v clusteru, nemusíte většinou na datových uzlech instalovat další balíčky R. Je však možné, že budete potřebovat další balíčky pro podporu použití **rxExec** nebo **RxDataStep** provádění na datových uzlech.

V takových případech lze další balíčky nainstalovat pomocí akce skriptu po vytvoření clusteru. Další informace najdete v tématu [Správa služeb ml v clusteru HDInsight](r-server-hdinsight-manage.md).

### <a name="change-apache-hadoop-mapreduce-memory-settings"></a>Změnit nastavení Apache Hadoop paměti MapReduce

Cluster se dá změnit tak, aby se změnila velikost paměti, která je dostupná pro služby ML při spuštění úlohy MapReduce. Pokud chcete upravit cluster, použijte uživatelské rozhraní Apache Ambari, které je dostupné v okně Azure Portal pro váš cluster. Pokyny k přístupu k uživatelskému rozhraní Ambari pro váš cluster najdete v tématu [Správa clusterů HDInsight pomocí webového uživatelského rozhraní Ambari](../hdinsight-hadoop-manage-ambari.md).

Velikost paměti, která je k dispozici pro služby ML, je také možné změnit pomocí přepínačů Hadoop ve volání **RxHadoopMR** následujícím způsobem:

    hadoopSwitches = "-libjars /etc/hadoop/conf -Dmapred.job.map.memory.mb=6656"  

### <a name="scale-your-cluster"></a>Škálování clusteru

Existující cluster služeb ML v HDInsight se dá škálovat nahoru nebo dolů na portálu. Horizontálním škálováním můžete získat další kapacitu, kterou můžete potřebovat pro úlohy většího zpracování, nebo můžete škálovat cluster zpátky, pokud je nečinný. Pokyny, jak škálovat cluster, najdete v tématu [Správa clusterů HDInsight](../hdinsight-administer-use-portal-linux.md).

### <a name="maintain-the-system"></a>Údržba systému

Údržba pro použití oprav operačního systému a další aktualizace se provádí na základních virtuálních počítačích se systémem Linux v clusteru HDInsight mimo špičku. Údržba se obvykle provádí v 3:30 ráno (na základě místního času pro virtuální počítač) každé pondělí a čtvrtek. Aktualizace se provádí takovým způsobem, že neovlivňují více než čtvrtou dobu clusteru.

Vzhledem k tomu, že hlavní uzly jsou redundantní a nejsou ovlivněny všechny datové uzly, mohou všechny úlohy, které jsou spuštěny během této doby, fungovat pomalu. Měli byste je ale pořád spouštět až po dokončení. Jakékoli vlastní software nebo místní data, která jste zachovali mezi tyto události údržby, pokud nedojde k závažné chybě, která vyžaduje opětovné sestavení clusteru.

## <a name="ide-options-for-ml-services-on-hdinsight"></a>Možnosti IDE pro služby ML ve službě HDInsight

Hraniční uzel Linux clusteru HDInsight je cílová zóna pro analýzu založenou na jazyce R. Poslední verze HDInsight poskytují výchozí instalaci serveru RStudio na hraničním uzlu jako integrované vývojové prostředí (IDE) založené na prohlížeči. Použití serveru RStudio jako integrovaného vývojového prostředí (IDE) pro vývoj a spouštění skriptů R může výrazně zvýšit produktivitu, než jenom pomocí konzoly R.

Kromě toho můžete nainstalovat integrované vývojové prostředí (IDE) pro Desktop a použít ho pro přístup ke clusteru prostřednictvím použití vzdáleného výpočetního kontextu MapReduce nebo Sparku. Možnosti zahrnují [stav](http://www.walware.de/goto/statet)RTVS ( [nástroje R pro Visual Studio](https://marketplace.visualstudio.com/items?itemName=MikhailArkhipov007.RTVS2019) Microsoft), RStudio a Walware na základě zatmění.

Kromě toho můžete ke konzole R na hraničním uzlu přejít zadáním **R** na příkazovém řádku systému Linux po připojení přes protokol SSH nebo výstup. Při použití rozhraní konzoly je vhodné spustit textový editor pro vývoj skriptů R v jiném okně a podle potřeby vyjímat a vkládat oddíly skriptu do konzoly jazyka R.

## <a name="pricing"></a>Ceny

Ceny, které jsou přidružené k clusteru HDInsight služby ML Services, jsou strukturované podobně jako ceny pro ostatní typy clusterů HDInsight. Jsou založené na velikosti příslušných virtuálních počítačů v názvech, datech a hraničních uzlech, a to s přidáním oprotia na základě základní hodiny. Další informace najdete v tématu [ceny služby HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/).

## <a name="next-steps"></a>Další kroky

Další informace o tom, jak používat služby ML v clusterech HDInsight, najdete v následujících tématech:

* [Spuštění skriptu R v clusteru služby ML ve službě Azure HDInsight pomocí serveru RStudio](machine-learning-services-quickstart-job-rstudio.md)
* [Možnosti výpočetního kontextu pro cluster služby ML Services v HDInsight](r-server-compute-contexts.md)
* [Možnosti úložiště pro cluster služeb ML v HDInsight](r-server-storage.md)
