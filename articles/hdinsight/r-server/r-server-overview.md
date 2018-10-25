---
title: Úvod do služby ML v Azure HDInsight
description: Zjistěte, jak vytvářet aplikace pro analýzy velkých objemů dat pomocí služby ML na HDInsight.
services: hdinsight
ms.service: hdinsight
author: jasonwhowell
ms.author: jasonh
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 06/27/2018
ms.openlocfilehash: af7a0d01fcf9df42da79a6529c8d4b0e31d73aa1
ms.sourcegitcommit: c2c279cb2cbc0bc268b38fbd900f1bac2fd0e88f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/24/2018
ms.locfileid: "49985484"
---
# <a name="introduction-to-ml-services-and-open-source-r-capabilities-on-hdinsight"></a>Úvod do služby ML a možnosti open-source jazyka R v HDInsight

> [!NOTE]
> V září 2017 byla vydána Microsoft R serveru v rámci nový název **Microsoft Machine Learning Server** nebo ML Server. V důsledku toho se teď nazývá clusteru R serveru v HDInsight **Machine Learning Services** nebo **služby ML** clusteru v HDInsight. Další informace o změně názvu R serveru, naleznete v tématu [Microsoft R Server je teď Microsoft Machine Learning Server](https://docs.microsoft.com/machine-learning-server/rebranding-microsoft-r-server#get-support-for-r-server).

Microsoft Machine Learning Server je dostupná jako možnost nasazení, při vytváření clusterů HDInsight v Azure. Typ clusteru, který poskytuje tato možnost se nazývá **služby ML**. Tato funkce poskytuje datovým vědcům, statistikům a programátorům v R přístup na vyžádání ke škálovatelným, distribuovaným analytickým na HDInsight metodám služby.

[!INCLUDE [hdinsight-price-change](../../../includes/hdinsight-enhancements.md)]

Služby ML v HDInsight poskytuje nejnovější funkce pro analýzy založené na jazyce R na datových sadách prakticky jakékoli velikosti, načteno do úložiště objektů Blob v Azure nebo Data Lake. Vzhledem k tomu, že cluster se službou ML Services je založená na open-source systému R, můžete využít aplikace založené na jazyce R, které vytváříte, některé balíčky R open source 8000 +. Rutiny v ScaleR balíček analýzy velkých objemů dat společnosti Microsoft jsou k dispozici.

Hraničním uzlu clusteru poskytuje praktické místo k připojení ke clusteru a ke spuštění skriptů jazyka R. Díky hraniční uzel máte možnost spuštění paralelizované distribuované funkcí ScaleR mezi jádry hraničního uzlu serveru. Můžete také spustit je na uzlech clusteru s použitím jeho ScaleR Hadoop Map Reduce nebo kontexty služby compute Spark.

Modely nebo předpovědi, které jsou výsledkem analýzy si můžete stáhnout pro místní použití. Jejich lze také se mají zprovoznit jinde v Azure, zejména prostřednictvím [Azure Machine Learning Studio](http://studio.azureml.net) [webová služba](../../machine-learning/studio/publish-a-machine-learning-web-service.md).

## <a name="get-started-with-ml-services-on-hdinsight"></a>Začínáme se službou ML v HDInsight

K vytvoření clusteru služby ML ve službě Azure HDInsight, vyberte **služby ML** typ clusteru při vytváření clusteru HDInsight pomocí webu Azure portal. Typ clusteru služby ML obsahuje ML Server na data uzlů v clusteru a na hraničním uzlu, která slouží jako cílová zóna pro analýzy založené na služby ML. Zobrazit [Začínáme se službou ML na HDInsight](r-server-get-started.md) návod, jak vytvořit cluster.

## <a name="why-choose-ml-services-in-hdinsight"></a>Proč zvolit služby ML v HDInsight?

Služba ML v HDInsight poskytuje následující výhody:

### <a name="ai-innovation-from-microsoft-and-open-source"></a>Inovace AI od Microsoftu i open source

  Služby ML zahrnuje sadu vysoce škálovatelné a distribuované algoritmy, například [RevoscaleR](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/revoscaler), [revoscalepy](https://docs.microsoft.com/machine-learning-server/python-reference/revoscalepy/revoscalepy-package), a [microsoftML](https://docs.microsoft.com/machine-learning-server/python-reference/microsoftml/microsoftml-package) , který může pracovat na velikosti dat větší než velikost fyzické paměti a spouštění na nejrůznějších platformách distribuovanému. Přečtěte si další informace o kolekci Microsoft's vlastní [balíčky R](https://docs.microsoft.com/machine-learning-server/r-reference/introducing-r-server-r-package-reference) a [balíčky Pythonu](https://docs.microsoft.com/machine-learning-server/python-reference/introducing-python-package-reference) součástí produktu.
  
  Služby ML přemosťuje tyto Microsoft inovace a příspěvky z open sourcové komunity (sady nástrojů R, Python a AI) všechny nad rámec jednoho podnikové platformy. Všechny balíčky R nebo Python s otevřeným zdrojem strojového učení můžou fungovat společně s žádné speciální inovace od Microsoftu.

### <a name="simple-secure-and-high-scale-operationalization-and-administration"></a>Operacionalizace jednoduchý, zabezpečený a vysoce škálovatelné a Správa

  Podniky spoléhání se na prostředí a tradiční paradigmat investovat mnohem více času a směrem k operacionalizaci úsilí. Výsledkem zvýšeným náklady a zpoždění, včetně doby překladu pro modely, iterace Novoroční platný a aktuální, dodržování legislativních schválení a správu oprávnění prostřednictvím operacionalizace.

  Nabízí podnikové služby ML [operacionalizace](https://docs.microsoft.com/machine-learning-server/what-is-operationalization)v tomto po dokončení modelu strojového učení trvá jen několik kliknutí k vytváření webových služeb rozhraní API. Tyto [webových služeb](https://docs.microsoft.com/machine-learning-server/operationalize/concept-what-are-web-services) jsou hostované na serveru mřížky v cloudu a je možné integrovat se službou – obchodní aplikace. Možnost nasazení do elastického mřížky umožňuje, můžete bez problémů škálovat s potřebám vašeho podnikání, a to jak pro batch a hodnocení v reálném čase. Pokyny najdete v tématu [zprovoznění služby ML na HDInsight](r-server-operationalize.md).

<!---
* **Deep ecosystem engagements to deliver customer success with optimal total cost of ownership**

  Individuals embarking on the journey of making their applications intelligent or simply wanting to learn the new world of AI and machine learning, need the right resources to help them get started. In addition to this documentation, Microsoft provides several learning resources and has engaged several training partners to help you ramp up and become productive quickly.
--->

## <a name="key-features-of-ml-services-on-hdinsight"></a>Klíčové funkce služby ML v HDInsight

Následující funkce jsou součástí služby ML v HDInsight.

| Kategorie funkce | Popis |
|------------------|-------------|
| Povolené R | [Balíčky R](https://docs.microsoft.com/machine-learning-server/r-reference/introducing-r-server-r-package-reference) pro řešeními napsanými v jazyce R, rozdělení opensourcového jazyka R a za běhu infrastruktury pro spuštění skriptu. |
| Python – povoleno | [Moduly Pythonu](https://docs.microsoft.com/machine-learning-server/python-reference/introducing-python-package-reference) pro řešení, které jsou napsané v Pythonu, rozdělení opensourcových Pythonu a infrastrukturu run-time pro spuštění skriptu.
| [Předem natrénovaných modelů](https://docs.microsoft.com/machine-learning-server/install/microsoftml-install-pretrained-models) | Pro vizuální analýzu a analýza mínění v textu připravené ke stanovení skóre dat zadáte. |
| [Nasazení a využití](r-server-operationalize.md) | Zprovoznění serveru a nasadit řešení jako webovou službu. |
| [Vzdálené spuštění](r-server-hdinsight-manage.md#connect-remotely-to-microsoft-ml-services) | Spuštění vzdálené relace v clusteru služby ML ve vaší síti z pracovní stanice klienta. |


## <a name="data-storage-options-for-ml-services-on-hdinsight"></a>Možnosti úložiště dat pro služby ML v HDInsight

Výchozí úložiště pro systém souborů HDFS clusterů HDInsight může být přidružené k účtu služby Azure Storage nebo Azure Data Lake Store. Toto přidružení se zajistí, že požadovaná data se nahraje do clusteru se provádí trvalého úložiště během analýzy a data jsou k dispozici i po odstranění clusteru. Existují různé nástroje pro přenosy dat, který vyberete, včetně zařízení nahrávání založené na portálu účtu úložiště, možnosti úložiště a [AzCopy](../../storage/common/storage-use-azcopy.md) nástroj.

Máte možnost povolit přístup k další objektů Blob a ukládá Data lake během procesu bez ohledu na to možnost primární úložiště používá zřizování clusteru. Zobrazit [Začínáme se službou ML na HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-r-server-get-started) informace o přidání přístupu na další účty. Zobrazit [možnosti služby Azure Storage pro služby ML v HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-r-server-storage) článku se dozvíte další informace o použití více účtů úložiště.

Můžete také použít [Azure Files](../../storage/files/storage-how-to-use-files-linux.md) jako řešením úložiště pro použití na hraničním uzlu. Služba soubory Azure umožňuje připojit sdílenou složku, která byla vytvořena ve službě Azure Storage k systému souborů v Linuxu. Další informace o těchto možnostech úložiště dat pro služby ML v clusteru HDInsight najdete v tématu [možnosti služby Azure Storage pro služby ML v HDInsight](r-server-storage.md).

## <a name="access-ml-services-edge-node"></a>Přístup služby ML hraniční uzel

Můžete se připojit k Microsoft ML Server na hraničním uzlu, pomocí prohlížeče. Ve výchozím nastavení se nainstaluje při vytváření clusteru. Další informace najdete v tématu [získat stared služby ML na HDInsight](r-server-get-started.md). Můžete také připojit k hraničnímu uzlu clusteru z příkazového řádku pomocí SSH nebo PuTTY pro přístup ke konzole R.

## <a name="develop-and-run-r-scripts"></a>Vývoj a spouštění skriptů jazyka R

Vytváření a spouštění skriptů R můžete použít některou z 8000 + open sourcové balíčky R kromě rutiny paralelizované a distribuované, které jsou k dispozici v knihovně ScaleR. Obecně platí skript, který se spustí s služby ML na hraničním uzlu běží v rámci interpret R na tomto uzlu. Výjimky jsou tyto kroky, které je třeba zavolat funkci ScaleR s výpočetním kontextu, který je nastaven na Hadoop, Map Reduce (RxHadoopMR) nebo Spark (RxSpark). V takovém případě funkce spouští způsobem distribuovaných dat (úlohy) uzlů clusteru, které jsou spojeny s daty odkazuje. Další informace o možnosti různých výpočetního kontextu najdete v tématu [možnosti výpočetního kontextu pro služby ML na HDInsight](r-server-compute-contexts.md).

## <a name="operationalize-a-model"></a>Zprovoznění modelu

Po dokončení modelování vašich dat je můžete model operacionalizovat za účelem provádění předpovědí pro nová data z Azure nebo v místním. Tento proces se označuje jako vyhodnocování. Hodnocení můžete udělat v HDInsight, Azure Machine Learning nebo místně.

### <a name="score-in-hdinsight"></a>Skóre v HDInsight

Ke stanovení skóre v HDInsight, zápis R funkce, která volá váš model k predikci pro nový soubor dat, která jste načetli do vašeho účtu úložiště. Uložte předpovědí zpátky do účtu úložiště. Spuštěním této rutiny na vyžádání na hraničním uzlu clusteru nebo pomocí naplánované úlohy.

### <a name="score-in-azure-machine-learning-aml"></a>Skóre v Azure Machine Learning (AML)

Ke stanovení skóre pro používání Azure Machine Learning, používat balíček Azure Machine Learning R open source říká [AzureML](https://cran.r-project.org/web/packages/AzureML/vignettes/getting_started.html) publikování modelu jako webová služba Azure. Pro usnadnění práce tento balíček je předem nainstalované na hraničním uzlu. Dále používat zařízení ve službě Azure Machine Learning k vytvoření uživatelského rozhraní pro webovou službu a pak volání webové služby podle potřeby pro vyhodnocení.

Pokud zvolíte tuto možnost, je nutné převést všechny objekty modelu ScaleR na objekty modelu ekvivalentní open source pro použití s webovou službou. Kromě funkcí ScaleR vynucení, použít například `as.randomForest()` pro modely na základě skupiny stromů, pro tento převod.

### <a name="score-on-premises"></a>Skóre v místním

Ke stanovení skóre v místním po vytvoření modelu, serializuje model v jazyce R, stáhněte ho, deserializovat ho a následně použít pro vyhodnocování nová data. Můžete stanovení skóre nových dat pomocí postupu popsaného v [skóre v HDInsight](#score-in-hdinsight) nebo s použitím [webových služeb](https://docs.microsoft.com/machine-learning-server/operationalize/concept-what-are-web-services).

## <a name="maintain-the-cluster"></a>Udržovat clusteru

### <a name="install-and-maintain-r-packages"></a>Instalovat a udržovat balíčky jazyka R

Většina balíčky R, které používáte povinnost na hraničním uzlu, od většinu kroků svoje skripty jazyka R spouštět existuje. Instalace dalších balíčků R na hraničním uzlu, můžete použít `install.packages()` metody v jazyce R.

Pokud právě používáte rutiny z knihovny ScaleR napříč clusterem, nepotřebujete obvykle k instalaci dalších balíčků R na datových uzlů. Však může být zapotřebí další balíčky pro podporu použití **rxExec** nebo **RxDataStep** spuštění na uzlech data.

Po vytvoření clusteru, můžete v takových případech nainstalovat další balíčky pomocí akce skriptu. Další informace najdete v tématu [spravovat služby ML v clusteru HDInsight](r-server-hdinsight-manage.md).

### <a name="change-hadoop-mapreduce-memory-settings"></a>Změňte nastavení paměti Hadoop MapReduce

Chcete-li změnit velikost paměti, která je k dispozici služby ML při spuštění úlohy MapReduce lze upravit clusteru. Pokud chcete upravit clusteru, použijte rozhraní Apache Ambari, která je k dispozici prostřednictvím okno Azure portal pro váš cluster. Pokyny ohledně toho, jak získat přístup k rozhraní Ambari, Uživatelskému pro váš cluster, naleznete v tématu [HDInsight Správa clusterů pomocí webového uživatelského rozhraní Ambari](../hdinsight-hadoop-manage-ambari.md).

Je také možné změnit velikost paměti, která je k dispozici služby ML pomocí přepínače Hadoop ve volání **RxHadoopMR** následujícím způsobem:

    hadoopSwitches = "-libjars /etc/hadoop/conf -Dmapred.job.map.memory.mb=6656"  

### <a name="scale-your-cluster"></a>Škálování clusteru

Existující cluster služby ML v HDInsight můžete vertikálně navýšit nebo snížit pomocí portálu. Vertikální navýšení kapacity, můžete získat další kapacitu, které byste mohli potřebovat pro větší úlohy související se zpracováním, nebo můžete vertikálně snížit kapacitu clusteru při nečinnosti. Pokyny o tom, jak škálovat cluster najdete v tématu [clusterů HDInsight spravovat](../hdinsight-administer-use-portal-linux.md).

### <a name="maintain-the-system"></a>Udržovat systému

Základní virtuálních počítačů s Linuxem v clusteru služby HDInsight provádění údržby použít opravy operačního systému a dalších aktualizací špičku. Obvykle údržba se provádí na 3:30:00 (na základě místního času pro virtuální počítač) každé pondělí a čtvrtek. Aktualizace se provádějí v tak, že se nebudou mít vliv na více než čtvrtletí clusteru současně.

Protože jsou redundantní hlavní uzly a všechny datové uzly se to týká, všechny úlohy, které jsou spuštěny během této doby může zpomalit. Ale měli stále spouštějí dokončení. Všechny vlastní software nebo místní data, která máte se zachovají v rámci těchto událostí údržby, pokud dojde k závažné chybě, která vyžaduje opětovné sestavení clusteru.

## <a name="ide-options-for-ml-services-on-hdinsight"></a>Možnosti integrovaného vývojového prostředí pro služby ML v HDInsight

Linux hraničnímu uzlu clusteru služby HDInsight je cílová zóna pro analýzy založené na jazyce R. Nejnovější verze HDInsight poskytují výchozí instalaci RStudio serveru na hraničním uzlu jako integrované vývojové prostředí založené na prohlížeči. Použití nástroje RStudio serveru jako integrované vývojové prostředí pro vývoj a spouštění skriptů R, může být výrazně zvýšit produktivitu práce než jen používání konzoly R.

Kromě toho můžete nainstalovat desktopové prostředí IDE a používat ho pro přístup ke clusteru pomocí vzdálený výpočetní kontext MapReduce nebo Spark. Mezi možnosti patří společnosti Microsoft [nástroje R pro Visual Studio](https://www.visualstudio.com/features/rtvs-vs.aspx) (RTVS), RStudio a Walware uživatele založeného na zatmění [StatET](http://www.walware.de/goto/statet).

Kromě toho můžete přístup ke konzole R na hraničním uzlu tak, že zadáte **R** příkazového řádku systému Linux po připojení přes SSH nebo PuTTY. Při použití rozhraní konzoly, je vhodné spustit textového editoru pro vývoj skriptů R v jiném okně a operace vyjmutí a vložení části skriptu do konzoly R podle potřeby.

## <a name="pricing"></a>Ceny

Ceny, které jsou spojeny s clusterem HDInsight služby ML jsou strukturovaná podobně jako na ceny pro další typy clusterů HDInsight. Název, data a hraničních uzlů, a uveďte zdvih hodinu jádra jsou založené na velikosti základních virtuálních počítačů. Další informace najdete v tématu [ceny HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/).

## <a name="next-steps"></a>Další postup

Další informace o tom, jak používat služby ML na clusterech HDInsight, naleznete v následujících tématech:

* [Začínáme s clusterem služby ML v HDInsight](r-server-get-started.md)
* [Možnosti výpočetního kontextu pro cluster služby ML Services v HDInsight](r-server-compute-contexts.md)
* [Možnosti úložiště pro cluster služby ML v HDInsight](r-server-storage.md)
