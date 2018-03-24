---
title: Úvod do R serverem v Azure HDInsight | Microsoft Docs
description: Naučte se používat R Server pro vytváření aplikací pro analýzy velkých objemů dat v HDInsight.
services: hdinsight
documentationcenter: ''
author: bradsev
manager: jhubbard
editor: cgronlun
ms.assetid: 6dc21bf5-4429-435f-a0fb-eea856e0ea96
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 06/19/2017
ms.author: bradsev
ms.openlocfilehash: 2106e5f0c9b6a27da3747bc67aad0937f2356873
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2018
---
# <a name="introduction-to-r-server-and-open-source-r-capabilities-on-hdinsight"></a>Úvod k serveru R a možnosti R open source v HDInsight

Microsoft R Server je k dispozici jako možnost nasazení, při vytváření clusterů HDInsight v Azure. Tato nová funkce poskytuje datových vědců statistikami a programátory v jazyce R na vyžádání přístup k škálovatelné, distribuované metody analýz v HDInsight.

[!INCLUDE [hdinsight-price-change](../../../includes/hdinsight-enhancements.md)]

Clustery můžete odpovídající velikost projekty a úkoly v ručně a pak bylo odstraněno. Pokud jste už nepotřebují. Vzhledem k tomu, že jsou součástí Azure HDInsight, tyto clustery se dodávají s podporu 24 hodin denně 7 podnikové úrovni, SLA 99,9 % doby provozu a možnost integrace s dalšími součástmi v ekosystému Azure.

R serverem v HDInsight obsahuje nejnovější funkce pro analýzu na základě R datové sady prakticky jakékoli velikosti, načtených do úložiště objektů Blob v Azure nebo Data Lake. Vzhledem k tomu, že R Server je založený na R s otevřeným zdrojem, můžete využít při sestavování aplikací na základě R jakékoli balíčky R s otevřeným zdrojem 8000 +. Rutiny v ScaleR, balíček analýzy velkých objemů dat společnosti Microsoft součástí R Server, jsou k dispozici.

Hraničního uzlu clusteru poskytuje vhodné místo pro připojení ke clusteru a spustit skripty R. S hraniční uzel máte možnost spuštění parallelized distribuované funkce ScaleR mezi jader hraničního uzlu serveru. Můžete také spustit je mezi uzly clusteru pomocí ScaleR na Hadoop mapy snížit nebo výpočetní kontexty Spark.

Modely nebo předpovědi, které výsledek z analýzy se dá stáhnout pro pomocí místní. Se může také být operationalized jinde v Azure, konkrétně prostřednictvím [Azure Machine Learning Studio](http://studio.azureml.net) [webovou službu](../../machine-learning/studio/publish-a-machine-learning-web-service.md).

## <a name="get-started-with-r-on-hdinsight"></a>Začínáme s R v HDInsight
Chcete-li zahrnout R Server v clusteru služby HDInsight, je nutné vybrat typ clusteru R Server při vytváření clusteru HDInsight pomocí portálu Azure. Typ clusteru R Server zahrnuje R Server na datové uzly clusteru a na hraniční uzel, který slouží jako cílová zóny na základě R Server analýzy. V tématu [Začínáme s R serverem v HDInsight](r-server-get-started.md) podrobný postup vytvoření clusteru.

## <a name="learn-about-data-storage-options"></a>Další informace o možnosti ukládání dat
Výchozí úložiště pro systém souborů HDFS clusterů HDInsight, které může být přidruženy k účtu Azure Storage nebo úložiště služby Azure Data Lake. Toto přidružení zajistí, že ať data jsou odeslána do clusteru se provádí trvalé úložiště během analýzy. Existují různé nástroje pro zpracování přenos dat do úložiště možnost, kterou vyberete, včetně zařízení nahrávání založené na portálu účtu úložiště a [AzCopy](../../storage/common/storage-use-azcopy.md) nástroj.

Máte možnost přidání přístupu do dalších objektů Blob a ukládá Data lake během procesu bez ohledu na to možnost primárního úložiště používá zřizování clusteru. V tématu [Začínáme s R serverem v HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-r-server-get-started) informace o přidávání přístup do dalších účtů. Najdete v článku doplňující [možnosti úložiště Azure pro R Server v HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-r-server-storage) článek a další informace o používání více účtů úložiště.

Můžete také použít [Azure Files](../../storage/files/storage-how-to-use-files-linux.md) jako řešením úložiště pro použití na uzlu edge. Soubory Azure umožňuje připojit sdílené složce, která byla vytvořena ve službě Azure Storage do souboru systému Linux. Další informace o těchto možnostech úložiště dat pro R Server v clusteru HDInsight najdete v tématu [Azure Storage možnosti pro R serverem v HDInsight clustery](r-server-storage.md).

## <a name="access-r-server-on-the-cluster"></a>Přístup k serveru R na clusteru
Můžete se připojit k serveru R na uzlu edge pomocí prohlížeče. Je nainstalovaná ve výchozím nastavení při vytváření clusteru. Další informace najdete v tématu [začít pracovat s R serverem v HDInsight](r-server-get-started.md).

Můžete také připojíte k serveru R z příkazovému řádku pomocí SSH nebo PuTTY přístup ke konzole R. 

## <a name="develop-and-run-r-scripts"></a>Vývoj a spouštět skripty R
Vytvářet a spouštět skripty R můžete použít některou z balíčky R s otevřeným zdrojem 8000 + kromě paralelizovaná málo a distribuované rutiny, dostupné v knihovně ScaleR. Obecně platí skript, který běží na uzlu edge s R Server běží v rámci překladač R v tomto uzlu. Výjimky jsou tyto kroky, které je třeba volat funkci ScaleR s výpočetní kontext, který je nastavený na Hadoop mapy snížení (RxHadoopMR) nebo Spark (RxSpark). V takovém případě je funkce spuštěná distribuované způsobem napříč dat (úlohy) uzlů clusteru, které jsou spojeny s daty odkazuje. Další informace o možnostech kontextu různými výpočetními najdete v tématu [výpočetní kontextu možnosti pro R Server v HDInsight](r-server-compute-contexts.md).

## <a name="operationalize-a-model"></a>Zprovoznění modelu
Po dokončení modelování vaše data můžete zprovoznit model, který má provádět předpovědi nových dat, buď z Azure a místní. Tento proces se označuje jako vyhodnocování. Vyhodnocování lze provést v prostředí HDInsight, Azure Machine Learning nebo místně.

### <a name="score-in-hdinsight"></a>Skóre v HDInsight
Ke stanovení skóre v HDInsight, zapisovat R funkce, která volá modelu provádět předpovědi nový datový soubor, který jste načtených do účtu úložiště. Potom uložte jsou předpovědi zpět k účtu úložiště. Běžné na vyžádání můžete spustit na hraničního uzlu clusteru nebo pomocí naplánované úlohy.  

### <a name="score-in-azure-machine-learning-aml"></a>Skóre v Azure strojového učení (AML)
Ke stanovení skóre pomocí webové služby AML, použít balíček Azure Machine Learning R s otevřeným zdrojem, označuje jako [AzureML](https://cran.r-project.org/web/packages/AzureML/vignettes/getting_started.html) Chcete-li publikovat model jako Azure webové služby. Pro větší pohodlí si tento balíček je nainstalovány na uzlu edge. V dalším kroku použít zařízení v Machine Learning vytvořit uživatelské rozhraní pro webovou službu a poté volání webové služby podle potřeby pro vyhodnocování.

Pokud zvolíte tuto možnost, musíte převést všechny objekty modelu ScaleR na objekty modelu ekvivalentní open source pro použití s webovou službou. Pomocí funkce Převod ScaleR, jako například `as.randomForest()` pro modely na základě komplet, pro tento převod.

### <a name="score-on-premises"></a>Skóre na místě
Ke stanovení skóre místní po vytvoření modelu, můžete serializuje model v R, ho stáhnout, deserializovat ho a použít ho pro vyhodnocování nová data. Vám může skóre pro nová data s využitím metody popsané výše v [vyhodnocování v HDInsight](#scoring-in-hdinsight) nebo pomocí [DeployR](https://deployr.revolutionanalytics.com/).

## <a name="maintain-the-cluster"></a>Udržovat clusteru
### <a name="install-and-maintain-r-packages"></a>Instalaci a údržbě balíčky R
Na uzlu edge od většinu kroků existuje spustit skripty R se vyžadují většinu R balíčky, které používáte. K instalaci dalších balíčků R na uzlu edge, můžete obvykle `install.packages()` metody v jazyce R.

Pokud právě používáte rutiny z knihovny ScaleR napříč clusterem, není nutné obvykle nainstalovat další balíčky R na datové uzly. Však může být zapotřebí další balíčky pro podporu použití **rxExec** nebo **RxDataStep** spuštění na uzlech data.

Po vytvoření clusteru, lze v těchto případech nainstalovat další balíčky pomocí akce skriptu. Další informace najdete v tématu [vytváření clusteru služby HDInsight s R Server](r-server-get-started.md).   

### <a name="change-hadoop-map-reduce-memory-settings"></a>Změňte nastavení paměti snížit Hadoop mapy
Cluster můžete upravit tak, aby změnit velikost paměti, která je k dispozici pro R Server při spuštění úlohy snížit mapy. Chcete-li upravit cluster, použijte rozhraní Apache Ambari, který je k dispozici prostřednictvím Azure okno portálu pro váš cluster. Pokyny o tom, jak získat přístup k rozhraní Ambari pro váš cluster najdete v tématu [Správa clusterů HDInsight pomocí webového uživatelského rozhraní Ambari](../hdinsight-hadoop-manage-ambari.md).

Je také možné změnit velikost paměti, která je k dispozici k serveru R pomocí přepínače Hadoop ve volání **RxHadoopMR** následujícím způsobem:

    hadoopSwitches = "-libjars /etc/hadoop/conf -Dmapred.job.map.memory.mb=6656"  

### <a name="scale-your-cluster"></a>Škálování clusteru
Existující cluster můžete škálovat nahoru nebo dolů prostřednictvím portálu. Škálování, získáte další kapacitu, které byste mohli potřebovat pro větší úlohy zpracování, nebo je možné škálovat zpět cluster nečinný. Pokyny o tom, jak škálování clusteru najdete v tématu [Správa clusterů HDInsight](../hdinsight-administer-use-portal-linux.md).

### <a name="maintain-the-system"></a>Udržování systému
Základní virtuální počítače Linux v clusteru služby HDInsight provádění údržby použít oprav operačního systému a další aktualizace špičku. Obvykle údržby se provádí na 3:30 AM (podle místního času pro virtuální počítač) každé pondělí a čtvrtek. Aktualizace se provádějí v tak, aby se nemáte vliv více než čtvrtletí clusteru současně.  

Vzhledem k tomu, že jsou redundantní hlavních uzlech a dopad na všechny uzly dat, všechny úlohy, které jsou spuštěné během této doby může zpomalit. By měly být stále spuštěny dokončen, ale. Všechny vlastní software nebo místní data, zda máte se zachová napříč tyto události údržby, pokud dojde k závažné chybě, která vyžaduje nové vytvoření clusteru.

## <a name="learn-about-ide-options-for-r-server-on-an-hdinsight-cluster"></a>Další informace o možnosti IDE pro R Server v clusteru HDInsight
Linux hraničního uzlu clusteru HDInsight je cílová zónu pro R na základě analýzy. Nejnovější verze služby HDInsight výchozí instalaci Rstudia serveru zadejte na uzlu edge jako IDE se založené na prohlížeči. Použití serveru Rstudia jako rozhraní IDE pro vývoj a spouštění skriptů R, může být výrazně zvýšit produktivitu, než jen pomocí konzole R.

Další úplné IDE možností je instalace klientů IDE a použít ho pro přístup ke clusteru prostřednictvím použití vzdáleného kontextu výpočetní mapy snížit nebo Spark. Mezi možnosti patří společnosti Microsoft [R Tools pro Visual Studio](https://www.visualstudio.com/features/rtvs-vs.aspx) (RTVS), Rstudia a Walware adresy, na základě Eclipse [StatET](http://www.walware.de/goto/statet).

Nakonec získat přístup ke konzole serveru R na uzlu edge tak, že zadáte **R** na příkazovém řádku Linux po připojení prostřednictvím protokolu SSH nebo PuTY. Pokud používáte rozhraní konzoly, je vhodné spustit v jiném okně, textový editor pro vývoj skriptu R a kopírování a vkládání části souboru skriptu do konzoly R podle potřeby.

## <a name="learn-about-pricing"></a>Další informace o cenách
Poplatky, které jsou přidruženy clusteru HDInsight bez R Server jsou strukturovaná podobně jako poplatky za standardní clusterů HDInsight. Mezi název, data a uzly okraj, a uveďte základní hodinu zdvih jsou založené na velikost základní virtuálních počítačů. Další informace o cenách prostředí HDInsight naleznete v části [HDInsight ceny](https://azure.microsoft.com/pricing/details/hdinsight/).

## <a name="next-steps"></a>Další postup
Další informace o tom, jak používat R Server s clustery HDInsight, naleznete v následujících tématech:

* [Začínáme s R serverem v HDInsight](r-server-get-started.md)
* [Možnosti výpočetního kontextu pro R Server ve službě HDInsight](r-server-compute-contexts.md)
* [Možnosti služby Azure Storage pro R Server ve službě HDInsight](r-server-storage.md)
