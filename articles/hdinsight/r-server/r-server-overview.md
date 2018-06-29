---
title: Úvod do služby ML v Azure HDInsight | Microsoft Docs
description: Zjistěte, jak používat k vytváření aplikací pro analýzu velkých objemů dat službu ML v HDInsight.
services: hdinsight
documentationcenter: ''
author: nitinme
manager: cgronlun
editor: cgronlun
ms.assetid: 6dc21bf5-4429-435f-a0fb-eea856e0ea96
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: nitinme
ms.openlocfilehash: 9633a7df1cb72f3e9e5ee79be0c332565e7e8f2a
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/27/2018
ms.locfileid: "37054098"
---
# <a name="introduction-to-ml-services-and-open-source-r-capabilities-on-hdinsight"></a>Úvod do služby ML a možnosti R open source v HDInsight

> [!NOTE]
> V září 2017 Microsoft R Server byl vydán pod novým názvem z **Microsoft Machine Learning Server** nebo ML serveru. V důsledku toho se nyní označuje jako cluster R serverem v HDInsight **služby Machine Learning** nebo **ML služby** clusteru v HDInsight. Další informace o změnu názvu R Server najdete v tématu [Microsoft R Server je nyní Microsoft Machine Learning Server](https://docs.microsoft.com/machine-learning-server/rebranding-microsoft-r-server#get-support-for-r-server).

Microsoft Machine Learning Server je k dispozici jako možnost nasazení, při vytváření clusterů HDInsight v Azure. Typ clusteru, který nabízí tato možnost se nazývá **ML služby**. Tato funkce poskytuje datových vědců statistikami a programátory v jazyce R na vyžádání přístup k škálovatelné, distribuované metody analýz v HDInsight.

[!INCLUDE [hdinsight-price-change](../../../includes/hdinsight-enhancements.md)]

ML služby v HDInsight nabízí nejnovější funkce pro analýzu na základě R na datové sady prakticky jakékoli velikosti, načtených do úložiště objektů Blob v Azure nebo Data Lake. Vzhledem k tomu, že ML služby clusteru je založený na R open source, můžete využít při sestavování aplikací na základě R jakékoli balíčky R open-source 8000 +. Rutiny v ScaleR balíček analýzy velkých objemů dat společnosti Microsoft jsou k dispozici.

Hraničního uzlu clusteru poskytuje vhodné místo pro připojení ke clusteru a spustit skripty R. S hraniční uzel máte možnost spuštění parallelized distribuované funkce ScaleR mezi jader hraničního uzlu serveru. Můžete také spustit je mezi uzly clusteru pomocí ScaleR na Hadoop mapy snížit nebo výpočetní kontexty Spark.

Modely nebo předpovědi, které jsou výsledkem analysis si můžete stáhnout pro místní použití. Se může také být operationalized jinde v Azure, konkrétně prostřednictvím [Azure Machine Learning Studio](http://studio.azureml.net) [webovou službu](../../machine-learning/studio/publish-a-machine-learning-web-service.md).

## <a name="get-started-with-ml-services-on-hdinsight"></a>Začínáme se službou ML v HDInsight

K vytvoření clusteru ML služby v Azure HDInsight, vyberte **ML služby** clusteru typ při vytváření clusteru HDInsight pomocí portálu Azure. Typ clusteru ML Services obsahuje ML serveru na datové uzly clusteru a na hraniční uzel, který slouží jako cílová zóna založeného na službách ML analýzy. V tématu [Začínáme se službou ML v HDInsight](r-server-get-started.md) podrobný postup vytvoření clusteru.

## <a name="why-choose-ml-services-in-hdinsight"></a>Proč zvolit ML služeb v prostředí HDInsight?

ML služeb v prostředí HDInsight poskytuje následující výhody:

### <a name="ai-innovation-from-microsoft-and-open-source"></a>AI inovací od společnosti Microsoft a open source

  ML Services obsahuje sadu vysoce škálovatelnou a distribuované algoritmy, jako [RevoscaleR](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/revoscaler), [revoscalepy](https://docs.microsoft.com/machine-learning-server/python-reference/revoscalepy/revoscalepy-package), a [microsoftML](https://docs.microsoft.com/machine-learning-server/python-reference/microsoftml/microsoftml-package) , může pracovat na větší velikosti dat velikost fyzické paměti a spusťte na širokou škálu platformy distribuovanému. Další informace o kolekci Microsoft je vlastní [balíčky R](https://docs.microsoft.com/machine-learning-server/r-reference/introducing-r-server-r-package-reference) a [balíčků Python](https://docs.microsoft.com/machine-learning-server/python-reference/introducing-python-package-reference) součástí produktu.
  
  ML služby přemosťuje tyto Microsoft inovace a příspěvky pocházejících z komunity open-source (sadách R, Python a AI) všechny nad platformu jeden podnikové úrovni. Libovolný balíček R nebo Python open-source machine learning můžete pracovat node souběžně s všechny vlastní inovací od společnosti Microsoft.

### <a name="simple-secure-and-high-scale-operationalization-and-administration"></a>Jednoduchý, zabezpečenou a špičkové operationalization a Správa

  Podniky spoléhat na prostředí a tradiční vzorů investovat mnohem čas a úsilí na operationalization. To má za následek zvýšeným náklady a zpozdí včetně doba překladu pro modely, iterací, abyste zajistili jejich platný a aktuální, zákonné schválení a správě oprávnění prostřednictvím operationalization.

  Nabízí ML služby enterprise úrovni [operationalization](https://docs.microsoft.com/machine-learning-server/what-is-operationalization)v, že po dokončení model machine learning trvá jen několik kliknutí k vytváření webových služeb rozhraní API. Tyto [webové služby](https://docs.microsoft.com/machine-learning-server/operationalize/concept-what-are-web-services) hostované v mřížce serveru v cloudu a může být integrovaná v-obchodní aplikace. Možnost nasazení na elastické mřížky umožňuje škálování bezproblémově se potřebám vaší firmy, a to jak pro batch a v reálném čase vyhodnocování. Pokyny najdete v tématu [zprovoznit ML služby v HDInsight](r-server-operationalize.md).

<!---
* **Deep ecosystem engagements to deliver customer success with optimal total cost of ownership**

  Individuals embarking on the journey of making their applications intelligent or simply wanting to learn the new world of AI and machine learning, need the right resources to help them get started. In addition to this documentation, Microsoft provides several learning resources and has engaged several training partners to help you ramp up and become productive quickly.
--->

## <a name="key-features-of-ml-services-on-hdinsight"></a>Klíčové funkce služby ML v HDInsight

Následující funkce jsou součástí služby ML v HDInsight.

| Funkce kategorie | Popis |
|------------------|-------------|
| Povolit R | [Balíčky R](https://docs.microsoft.com/machine-learning-server/r-reference/introducing-r-server-r-package-reference) pro řešení, které jsou napsané v R, otevřeným zdrojem rozdělení R a spuštění infrastruktury pro spuštění skriptu. |
| Povolit Python | [Moduly jazyka Python](https://docs.microsoft.com/machine-learning-server/python-reference/introducing-python-package-reference) pro řešení, které jsou napsané v Pythonu, otevřeným zdrojem rozdělení Python a spuštění infrastruktury pro spuštění skriptu.
| [Předem trénované modely](https://docs.microsoft.com/machine-learning-server/install/microsoftml-install-pretrained-models) | Pro visual analýzy a analýzy postojích text připravena ke stanovení skóre dat je zadat. |
| [Nasazení a využívat](r-server-operationalize.md) | Zprovoznění vašeho serveru a nasadit řešení jako webovou službu. |
| [Vzdálené spuštění](r-server-hdinsight-manage.md#connect-remotely-to-microsoft-ml-services) | Spuštění vzdálené relace v clusteru služby ML ve vaší síti ze své klientské pracovní stanice. |


## <a name="data-storage-options-for-ml-services-on-hdinsight"></a>Možnosti úložiště dat pro služby ML v HDInsight

Výchozí úložiště pro systém souborů HDFS clusterů HDInsight, které může být přidruženy k účtu Azure Storage nebo Azure Data Lake Store. Toto přidružení zajistí, že ať data jsou odeslána do clusteru se provádí trvalé úložiště během analýzy a data jsou k dispozici i po odstranění clusteru. Existují různé nástroje pro zpracování přenos dat do úložiště možnost, kterou vyberete, včetně zařízení nahrávání založené na portálu účtu úložiště a [AzCopy](../../storage/common/storage-use-azcopy.md) nástroj.

Máte možnost povolení přístupu k další objekt Blob a ukládá Data lake během procesu bez ohledu na to možnost primárního úložiště používá zřizování clusteru. V tématu [Začínáme se službou ML v HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-r-server-get-started) informace o přidávání přístup do dalších účtů. V tématu [možnosti úložiště Azure pro ML služby v HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-r-server-storage) článek a další informace o používání více účtů úložiště.

Můžete také použít [Azure Files](../../storage/files/storage-how-to-use-files-linux.md) jako řešením úložiště pro použití na uzlu edge. Soubory Azure umožňuje připojit sdílené složce, která byla vytvořena ve službě Azure Storage do souboru systému Linux. Další informace o těchto možnostech úložiště dat pro služby ML v clusteru HDInsight najdete v tématu [možnosti úložiště Azure pro ML služby v HDInsight](r-server-storage.md).

## <a name="access-ml-services-edge-node"></a>Uzel edge ML služby přístupu

Můžete se připojit k serveru ML Microsoft v uzlu edge pomocí prohlížeče. Je nainstalovaná ve výchozím nastavení při vytváření clusteru. Další informace najdete v tématu [získat stared službou ML v HDInsight](r-server-get-started.md). Můžete také připojit k okrajovému uzlu clusteru z příkazového řádku pomocí programu SSH/PuTTY přístup ke konzole R.

## <a name="develop-and-run-r-scripts"></a>Vývoj a spouštět skripty R

Vytvářet a spouštět skripty R můžete použít některou z 8000 + open-source R balíčky kromě paralelizovaná málo a distribuované rutiny k dispozici v knihovně ScaleR. Obecně platí skript, který běží na uzlu edge službou ML běží v rámci překladač R v tomto uzlu. Výjimky jsou tyto kroky, které je třeba volat funkci ScaleR s výpočetní kontext, který je nastavený na Hadoop mapy snížení (RxHadoopMR) nebo Spark (RxSpark). V takovém případě je funkce spuštěná distribuované způsobem napříč dat (úlohy) uzlů clusteru, které jsou spojeny s daty odkazuje. Další informace o možnostech kontextu různými výpočetními najdete v tématu [výpočetní kontextu možnosti pro ML služby v HDInsight](r-server-compute-contexts.md).

## <a name="operationalize-a-model"></a>Zprovoznění modelu

Po dokončení modelování vaše data můžete zprovoznit model, který má provádět předpovědi nových dat z Azure nebo místně. Tento proces se označuje jako vyhodnocování. Vyhodnocování lze provést v prostředí HDInsight, Azure Machine Learning nebo místně.

### <a name="score-in-hdinsight"></a>Skóre v HDInsight

Ke stanovení skóre v HDInsight, zapisovat R funkce, která volá modelu provádět předpovědi nový datový soubor, který jste načtených do účtu úložiště. Potom uložte jsou předpovědi zpět k účtu úložiště. Spuštěním této rutiny na vyžádání v hraničního uzlu clusteru nebo pomocí naplánované úlohy.

### <a name="score-in-azure-machine-learning-aml"></a>Skóre v Azure strojového učení (AML)

Ke stanovení skóre pomocí Azure Machine Learning, použít balíček Azure Machine Learning R open-source známé jako [AzureML](https://cran.r-project.org/web/packages/AzureML/vignettes/getting_started.html) Chcete-li publikovat model jako Azure webové služby. Pro větší pohodlí si tento balíček je nainstalovány na uzlu edge. V dalším kroku použít zařízení v Azure Machine Learning vytvořit uživatelské rozhraní pro webovou službu a poté volání webové služby podle potřeby pro vyhodnocování.

Pokud zvolíte tuto možnost, je nutné převést všechny objekty modelu ScaleR objekty ekvivalentní modelu open source pro použití s webovou službou. Pomocí funkce Převod ScaleR, jako například `as.randomForest()` pro modely na základě komplet, pro tento převod.

### <a name="score-on-premises"></a>Skóre na místě

Ke stanovení skóre místní po vytvoření modelu, můžete serializuje model v R, ho stáhnout, deserializovat ho a použít ho pro vyhodnocování nová data. Vám může skóre pro nová data s využitím metody popsané výše v [vyhodnocování v HDInsight](#scoring-in-hdinsight) nebo pomocí [DeployR](https://deployr.revolutionanalytics.com/).

## <a name="maintain-the-cluster"></a>Udržovat clusteru

### <a name="install-and-maintain-r-packages"></a>Instalaci a údržbě balíčky R

Na uzlu edge od většinu kroků existuje spustit skripty R se vyžadují většinu R balíčky, které používáte. Chcete-li nainstalovat další balíčky R na uzlu edge, můžete použít `install.packages()` metody v jazyce R.

Pokud právě používáte rutiny z knihovny ScaleR napříč clusterem, není nutné obvykle nainstalovat další balíčky R na datové uzly. Však může být zapotřebí další balíčky pro podporu použití **rxExec** nebo **RxDataStep** spuštění na uzlech data.

V takových případech další balíčky můžete nainstalovat akce skriptu po vytvoření clusteru. Další informace najdete v tématu [spravovat ML služeb v clusteru HDInsight](r-server-hdinsight-manage.md).

### <a name="change-hadoop-mapreduce-memory-settings"></a>Změňte nastavení paměti Hadoop MapReduce

Chcete-li změnit velikost paměti, které jsou k dispozici služby ML při spuštění úlohy MapReduce lze upravit clusteru. Chcete-li upravit cluster, použijte rozhraní Apache Ambari, který je k dispozici prostřednictvím Azure okno portálu pro váš cluster. Pokyny o tom, jak získat přístup k rozhraní Ambari pro váš cluster najdete v tématu [Správa clusterů HDInsight pomocí webového uživatelského rozhraní Ambari](../hdinsight-hadoop-manage-ambari.md).

Je také možné změnit velikost paměti, která je k dispozici pro ML služby pomocí přepínače Hadoop ve volání **RxHadoopMR** následujícím způsobem:

    hadoopSwitches = "-libjars /etc/hadoop/conf -Dmapred.job.map.memory.mb=6656"  

### <a name="scale-your-cluster"></a>Škálování clusteru

Existující cluster služby ML v HDInsight můžete škálovat nahoru nebo dolů prostřednictvím portálu. Rozšiřování prostředků, můžete získat další kapacitu, které byste mohli potřebovat pro větší úlohy zpracování, nebo můžete škálovat zpět cluster nečinný. Pokyny o tom, jak škálování clusteru najdete v tématu [Správa clusterů HDInsight](../hdinsight-administer-use-portal-linux.md).

### <a name="maintain-the-system"></a>Udržování systému

Základní virtuální počítače Linux v clusteru služby HDInsight provádění údržby použít oprav operačního systému a další aktualizace špičku. Obvykle údržby se provádí na 3:30 AM (podle místního času pro virtuální počítač) každé pondělí a čtvrtek. Aktualizace se provádějí v tak, aby se nemáte vliv více než čtvrtletí clusteru současně.

Vzhledem k tomu, že jsou redundantní hlavních uzlech a dopad na všechny uzly dat, všechny úlohy, které jsou spuštěné během této doby může zpomalit. Nicméně by měly být spuštěny stále dokončen. Všechny vlastní software nebo místní data, zda máte se zachová napříč tyto události údržby, pokud dojde k závažné chybě, která vyžaduje nové vytvoření clusteru.

## <a name="ide-options-for-ml-services-on-hdinsight"></a>Možnosti IDE pro ML služby v HDInsight

Linux hraničního uzlu clusteru HDInsight je cílová zónu pro R na základě analýzy. Nejnovější verze služby HDInsight výchozí instalaci Rstudia serveru zadejte na uzlu edge jako IDE se založené na prohlížeči. Použití serveru Rstudia jako rozhraní IDE pro vývoj a spouštění skriptů R, může být výrazně zvýšit produktivitu, než jen pomocí konzole R.

Kromě toho můžete nainstalovat plochy IDE a používat pro přístup ke clusteru prostřednictvím použití vzdáleného kontextu výpočetní MapReduce nebo Spark. Mezi možnosti patří společnosti Microsoft [R Tools pro Visual Studio](https://www.visualstudio.com/features/rtvs-vs.aspx) (RTVS), Rstudia a Walware adresy, na základě Eclipse [StatET](http://www.walware.de/goto/statet).

Kromě toho získat přístup ke konzole R na uzlu edge tak, že zadáte **R** na příkazovém řádku Linux po připojení prostřednictvím protokolu SSH nebo PuTTY. Pokud používáte rozhraní konzoly, je vhodné spustit v jiném okně, textový editor pro vývoj skriptu R a kopírování a vkládání části souboru skriptu do konzoly R podle potřeby.

## <a name="pricing"></a>Ceny

Ceny, které jsou přidružené clusteru služby HDInsight ML jsou strukturovaná podobně jako ceny pro jiné typy clusteru HDInsight. Mezi název, data a uzly okraj, a uveďte základní hodinu zdvih jsou založené na velikost základní virtuálních počítačů. Další informace najdete v tématu [HDInsight ceny](https://azure.microsoft.com/pricing/details/hdinsight/).

## <a name="next-steps"></a>Další postup

Další informace o tom, jak používat služby ML v clusterech HDInsight, naleznete v následujících tématech:

* [Začínáme s clusterem ML Serices v HDInsight](r-server-get-started.md)
* [Výpočetní kontextu možnosti pro cluster služby ML v HDInsight](r-server-compute-contexts.md)
* [Možnosti úložiště pro cluster služby ML v HDInsight](r-server-storage.md)