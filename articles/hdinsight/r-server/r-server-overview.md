---
title: Seznámení se službami ML ve službě Azure HDInsight
description: Naučte se používat služby ML v HDInsight k vytváření aplikací pro analýzu velkých objemů dat.
ms.service: hdinsight
ms.topic: overview
ms.custom: hdinsightactive,seoapr2020
ms.date: 04/20/2020
ms.openlocfilehash: 87f4181e820b1c6ecdeb0fda85a88e80db248dd2
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "98943915"
---
# <a name="what-is-ml-services-in-azure-hdinsight"></a>Co je služba ML Services ve službě Azure HDInsight

Microsoft Machine Learning Server je k dispozici jako možnost nasazení při vytváření clusterů HDInsight v Azure. Typ clusteru, který poskytuje tuto možnost, se nazývá **ml Services**. Tato funkce poskytuje přístup na vyžádání pro adaptivní distribuované metody analýzy v HDInsight.

Služba ML Services v HDInsight poskytuje nejnovější funkce pro analýzy založené na jazyce R v datových sadách prakticky libovolné velikosti. Datové sady se dají načíst do objektu blob Azure nebo do úložiště Data Lake. Vaše aplikace založené na jazyce R můžou používat balíčky R 8000 + open-source. K dispozici jsou také rutiny nástroje pro škálování a analýzu velkých objemů dat od Microsoftu.

Hraniční uzel nabízí pohodlný místo pro připojení ke clusteru a spouštění skriptů jazyka R. Hraniční uzel umožňuje spuštění paralelních distribuovaných funkcí škálovat napříč jádry serveru. Můžete je také spouštět v uzlech clusteru pomocí zmenšování mapy Hadoop služby škálovat. Můžete také použít Výpočetní kontexty Apache Spark.

Modely nebo předpovědi, které vyplývají z analýzy, se dají stáhnout pro místní použití. Můžou být taky `operationalized` jinde v Azure. Konkrétně prostřednictvím [Azure Machine Learning Studio (Classic)](https://studio.azureml.net)a [webové služby](../../machine-learning/classic/deploy-a-machine-learning-web-service.md).

## <a name="get-started-with-ml-services-on-hdinsight"></a>Začínáme se službami ML v HDInsight

Chcete-li vytvořit cluster služby ML v HDInsight, vyberte typ clusteru **služby ml** . Typ clusteru služby ML obsahuje ML Server na datových uzlech a na hraničním uzlu. Hraniční uzel slouží jako cílová zóna pro analýzu na základě služby ML. Návod, jak vytvořit cluster, najdete v tématu [vytvoření Apache Hadoop clusterů pomocí Azure Portal](../hdinsight-hadoop-create-linux-clusters-portal.md) .

## <a name="why-choose-ml-services-in-hdinsight"></a>Proč zvolit služby ML v HDInsight?

Služby ML ve službě HDInsight přináší následující výhody:

### <a name="ai-innovation-from-microsoft-and-open-source"></a>Inovace AI z Microsoftu a open source

  Služba ML Services zahrnuje vysoce přizpůsobitelnou, distribuovanou sadu algoritmů, jako jsou [RevoscaleR](/machine-learning-server/r-reference/revoscaler/revoscaler), [revoscalepy](/machine-learning-server/python-reference/revoscalepy/revoscalepy-package)a [microsoftML](/machine-learning-server/python-reference/microsoftml/microsoftml-package). Tyto algoritmy můžou pracovat na velikostech dat větších než velikost fyzické paměti. Jsou také spouštěny na nejrůznějších platformách distribuovaným způsobem. Přečtěte si další informace o shromažďování vlastních [balíčků R](/machine-learning-server/r-reference/introducing-r-server-r-package-reference) a [balíčků Pythonu](/machine-learning-server/python-reference/introducing-python-package-reference) společnosti Microsoft, které jsou součástí produktu.
  
  Služby ML přemostění těchto inovací a příspěvků od společnosti Microsoft pocházejících z open source komunity (sady nástrojů R, Python a AI). Vše nad jedinou platformou na podnikové úrovni. Libovolný otevřený zdrojový balíček R nebo Python může fungovat vedle všech vlastnických inovací od Microsoftu.

### <a name="simple-secure-and-high-scale-operationalization-and-administration"></a>Jednoduché, zabezpečené a vysoce škálovatelné zprovoznění a Správa

  Podniky spoléhají na tradiční paradigma a prostředí, které investují mnohem dlouho a úsilí k provozuschopnosti. Výsledkem této akce jsou neploché náklady a zpoždění, včetně doby překladu pro: modely, iterace, aby byly platné a aktuální, zákonné schválení a Správa oprávnění.

  Služba ML Services nabízí [provozuschopnost](/machine-learning-server/what-is-operationalization)na podnikové úrovni. Až se model strojového učení dokončí, stačí několik kliknutí a vygenerovat rozhraní API webových služeb. Tyto [webové služby](/machine-learning-server/operationalize/concept-what-are-web-services) jsou hostovány v serverové mřížce v cloudu a je možné je integrovat s podnikovými aplikacemi. Možnost nasazení do elastické mřížky vám umožní plynule škálovat požadavky vaší firmy, a to jak pro účely bodování dávky, tak i v reálném čase. Pokyny najdete v tématu [zprovoznění ml Services ve službě HDInsight](r-server-operationalize.md).

<!---
* **Deep ecosystem engagements to deliver customer success with optimal total cost of ownership**

  Individuals embarking on the journey of making their applications intelligent or simply wanting to learn the new world of AI and machine learning, need the right resources to help them get started. In addition to this documentation, Microsoft provides several learning resources and has engaged several training partners to help you ramp up and become productive quickly.
--->

> [!NOTE]  
> Typ clusteru služby ML v HDInsight se podporuje jenom v HDInsight 3,6. Pro HDInsight 3,6 se naplánuje vyřazení do 31. prosince 2020.

## <a name="key-features-of-ml-services-on-hdinsight"></a>Klíčové funkce služby ML v HDInsight

Následující funkce jsou součástí ML Services v HDInsight.

| Kategorie funkce | Description |
|------------------|-------------|
| R – povoleno | [Balíčky r](/machine-learning-server/r-reference/introducing-r-server-r-package-reference) pro řešení napsané v jazyce r s využitím open source distribuce R a běhové infrastruktury pro provádění skriptu. |
| Python – povolený | [Moduly Pythonu](/machine-learning-server/python-reference/introducing-python-package-reference) pro řešení napsané v Pythonu, s využitím open source distribuce Pythonu a běhové infrastruktury pro provádění skriptu.
| [Předem připravené modely](/machine-learning-server/install/microsoftml-install-pretrained-models) | Pro vizuální analýzu a analýzu textu mínění připravené k určení skóre dat, která zadáte. |
| [Nasazení a využití](r-server-operationalize.md) | `Operationalize` Server a nasaďte řešení jako webovou službu. |
| [Vzdálené spuštění](r-server-hdinsight-manage.md#connect-remotely-to-microsoft-ml-services) | Z pracovní stanice klienta spusťte vzdálené relace v clusteru služby ML Services ve vaší síti. |

## <a name="data-storage-options-for-ml-services-on-hdinsight"></a>Možnosti úložiště dat pro služby ML v HDInsight

Výchozím úložištěm pro systém souborů HDFS může být účet Azure Storage nebo Azure Data Lake Storage. Data nahraná do úložiště clusteru se v průběhu analýzy nahrála jako trvalá. Data jsou k dispozici i po odstranění clusteru. Přenos dat do úložiště lze zvládnout různými nástroji. Mezi tyto nástroje patří zařízení pro nahrávání na základě portálu účtu úložiště a nástroj AzCopy.

Během vytváření clusteru můžete povolit přístup k dalším objektům blob a datům Lake Store. Nejste omezeni možností primárního úložiště, které se používá.  Další informace o používání více účtů úložiště najdete v článku [možnosti Azure Storage služby ml Services na HDInsight](./r-server-storage.md) .

Soubory Azure můžete použít také jako možnost úložiště pro použití na hraničním uzlu. Soubory Azure umožňují sdílené složky vytvořené v Azure Storage systému souborů Linux. Další informace najdete v tématu [možnosti Azure Storage pro služby ml v HDInsight](r-server-storage.md).

## <a name="access-ml-services-edge-node"></a>Hraniční uzel služeb Access ML

K Microsoft ML Server na hraničním uzlu se můžete připojit pomocí prohlížeče, nebo pomocí SSH/výstupu. Konzola R se nainstaluje ve výchozím nastavení při vytváření clusteru.  

## <a name="develop-and-run-r-scripts"></a>Vývoj a spouštění skriptů R

Vaše skripty R můžou používat libovolný z balíčků sady R 8000 + open-source. Můžete také použít paralelní a distribuované rutiny z knihovny škálování. Skripty spouštěné na hraničním uzlu běží v překladači R v tomto uzlu. S výjimkou kroků, které volají funkci Scaleer, s výpočetním kontextem RxHadoopMR (map zmenšení) nebo Spark (výpočetního rxspark). Funkce jsou spouštěny distribuovaně napříč datovými uzly, které jsou přidruženy k datům. Další informace o možnostech kontextu najdete v tématu [Možnosti výpočetního kontextu pro služby ml v HDInsight](r-server-compute-contexts.md).

## <a name="operationalize-a-model"></a>`Operationalize` model

Po dokončení modelování dat `operationalize` model předpovědi pro nová data z Azure nebo místního prostředí. Tento proces se označuje jako bodování. Bodování se dá udělat v HDInsight, Azure Machine Learning nebo v místním prostředí.

### <a name="score-in-hdinsight"></a>Skóre v HDInsight

Pokud chcete skóre v HDInsight, napište funkci R. Funkce volá váš model, aby předpovědi nový datový soubor, který jste načetli do svého účtu úložiště. Pak předpovědi uložte zpátky do účtu úložiště. Tuto rutinu můžete spustit na vyžádání na hraničním uzlu clusteru nebo pomocí naplánované úlohy.

### <a name="score-in-azure-machine-learning-aml"></a>Skóre v Azure Machine Learning (AML)

Pokud chcete určit skóre pomocí Azure Machine Learning, použijte Open Source Azure Machine Learning R známý jako [AzureML](https://cran.r-project.org/src/contrib/Archive/AzureML/) k publikování modelu jako webové služby Azure. Pro usnadnění práce je tento balíček předem nainstalován na hraničním uzlu. Dále pomocí zařízení v Azure Machine Learning vytvořte uživatelské rozhraní webové služby a potom zavolejte webovou službu podle potřeby pro vyhodnocování. Pak převeďte objekty modelu nástroje pro horizontální navýšení kapacity na ekvivalentní objekty Open Source modelu pro použití s webovou službou. Pro tento převod použijte funkce pro vynucení škálování, například `as.randomForest()` pro modely založené na kompletech.

### <a name="score-on-premises"></a>Místní skóre

Určení skóre místního prostředí po vytvoření modelu: serializace modelu v jazyce R, jeho stažení, zrušení jeho serializace a jeho použití pro vyhodnocování nových dat. Můžete vyhodnotit nová data pomocí výše popsaného přístupu v tématu skóre v HDInsight nebo pomocí [webových služeb](/machine-learning-server/operationalize/concept-what-are-web-services).

## <a name="maintain-the-cluster"></a>Údržba clusteru

### <a name="install-and-maintain-r-packages"></a>Instalace a údržba balíčků R

Většina balíčků R, které použijete, se na hraničním uzlu vyžaduje, protože tam běží většina kroků v skriptech jazyka R. Chcete-li nainstalovat další balíčky R na hraničním uzlu, můžete použít `install.packages()` metodu v jazyce R.

Pokud jste právě používali rutiny knihovny pro škálování na více systému, obvykle nepotřebujete další balíčky R. V datových uzlech můžete potřebovat další balíčky pro provádění **rxExec** nebo **RxDataStep** .

Další balíčky lze nainstalovat pomocí akce skriptu po vytvoření clusteru. Další informace najdete v tématu [Správa služeb ml v clusteru HDInsight](r-server-hdinsight-manage.md).

### <a name="change-apache-hadoop-mapreduce-memory-settings"></a>Změnit nastavení Apache Hadoop paměti MapReduce

Dostupná paměť pro služby ML se dá upravit při spuštění úlohy MapReduce. Pro úpravu clusteru použijte uživatelské rozhraní Apache Ambari pro váš cluster. Pokyny k uživatelskému rozhraní Ambari najdete v tématu [Správa clusterů HDInsight pomocí webového uživatelského rozhraní Ambari](../hdinsight-hadoop-manage-ambari.md).

Dostupnou paměť pro služby ML lze změnit pomocí přepínačů Hadoop v volání **RxHadoopMR**:

```r
hadoopSwitches = "-libjars /etc/hadoop/conf -Dmapred.job.map.memory.mb=6656"
```

### <a name="scale-your-cluster"></a>Škálování clusteru

Existující cluster služeb ML v HDInsight se dá škálovat nahoru nebo dolů na portálu. Horizontálním škálováním kapacity získáte další kapacitu pro úlohy s větším zpracováním. Pokud je cluster nečinný, můžete ho škálovat zpátky. Pokyny, jak škálovat cluster, najdete v tématu [Správa clusterů HDInsight](../hdinsight-administer-use-portal-linux.md).

### <a name="maintain-the-system"></a>Údržba systému

Údržba operačního systému se provádí na podkladových virtuálních počítačích se systémem Linux v clusteru HDInsight v době mimo špičku. Údržba se obvykle provádí v 3:30. (místní čas virtuálního počítače) každé pondělí a čtvrtek. Aktualizace neovlivňují více než čtvrtou dobu clusteru.

Spuštěné úlohy můžou při údržbě zpomalit. Měli byste je ale pořád spouštět až po dokončení. Jakékoli vlastní software nebo místní data, která jste zachovali napříč těmito událostmi údržby, pokud nedojde k závažné chybě, která vyžaduje opětovné sestavení clusteru.

## <a name="ide-options-for-ml-services-on-hdinsight"></a>Možnosti IDE pro služby ML ve službě HDInsight

Hraniční uzel Linux clusteru HDInsight je cílová zóna pro analýzu založenou na jazyce R. Poslední verze HDInsight poskytují integrované vývojové prostředí (IDE) na RStudio serveru na hraničním uzlu. RStudio Server je produktivnější než konzola R pro vývoj a provádění.

Prostředí IDE pro stolní počítače má přístup ke clusteru prostřednictvím vzdáleného výpočetního kontextu MapReduce nebo Sparku. Mezi možnosti patří: Microsoft 's [nástroje R pro Visual Studio](https://marketplace.visualstudio.com/items?itemName=MikhailArkhipov007.RTVS2019) (RTVS), RStudio a Walware (stav) na základě zatmění.

Na hraničním uzlu zadejte **r** na příkazovém řádku a přejděte do konzoly r. Při použití rozhraní konzoly je vhodné vyvíjet skript R v textovém editoru. Pak podle potřeby vyjměte a vložte oddíly skriptu do konzoly R.

## <a name="pricing"></a>Ceny

Ceny spojené s clusterem služby ML Services HDInsight jsou strukturované podobně jako ostatní typy clusterů HDInsight. Jsou založené na velikosti podkladových virtuálních počítačů napříč názvy, daty a hraničními uzly. Navýšení za běhu i po hodinách. Další informace najdete v tématu [ceny služby HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/).

## <a name="next-steps"></a>Další kroky

Další informace o tom, jak používat služby ML v clusterech HDInsight, najdete v následujících článcích:

* [Spuštění skriptu R v clusteru služby ML ve službě Azure HDInsight pomocí serveru RStudio](machine-learning-services-quickstart-job-rstudio.md)
* [Možnosti výpočetního kontextu pro cluster služby ML Services v HDInsight](r-server-compute-contexts.md)
* [Možnosti úložiště pro cluster služeb ML v HDInsight](r-server-storage.md)