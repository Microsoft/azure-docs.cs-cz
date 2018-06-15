---
title: Úvod do R serverem v Azure HDInsight | Microsoft Docs
description: Naučte se používat R Server pro vytváření aplikací pro analýzy velkých objemů dat v HDInsight.
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
ms.date: 03/23/2018
ms.author: nitinme
ms.openlocfilehash: 19c286db9a8a2aa537badc83d98a1b74b73e9873
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
ms.locfileid: "31414714"
---
# <a name="introduction-to-r-server-and-open-source-r-capabilities-on-hdinsight"></a>Úvod k serveru R a možnosti R open source v HDInsight

R Server společnosti Microsoft (také označované jako Microsoft Machine Learning Server) je k dispozici jako možnost nasazení, při vytváření clusterů HDInsight v Azure. Typ clusteru, který nabízí tato možnost se nazývá **R Server**. Tato funkce poskytuje datových vědců statistikami a programátory v jazyce R na vyžádání přístup k škálovatelné, distribuované metody analýz v HDInsight.

[!INCLUDE [hdinsight-price-change](../../../includes/hdinsight-enhancements.md)]

R serverem v HDInsight obsahuje nejnovější funkce pro analýzu na základě R datové sady prakticky jakékoli velikosti, načtených do úložiště objektů Blob v Azure nebo Data Lake. Vzhledem k tomu, že cluster R Server je založený na R open source, můžete využít při sestavování aplikací na základě R jakékoli balíčky R open-source 8000 +. Rutiny v ScaleR, balíček analýzy velkých objemů dat společnosti Microsoft součástí R Server, jsou k dispozici.

Hraničního uzlu clusteru poskytuje vhodné místo pro připojení ke clusteru a spustit skripty R. S hraniční uzel máte možnost spuštění parallelized distribuované funkce ScaleR mezi jader hraničního uzlu serveru. Můžete také spustit je mezi uzly clusteru pomocí ScaleR na Hadoop mapy snížit nebo výpočetní kontexty Spark.

Modely nebo předpovědi, které jsou výsledkem analysis si můžete stáhnout pro místní použití. Se může také být operationalized jinde v Azure, konkrétně prostřednictvím [Azure Machine Learning Studio](http://studio.azureml.net) [webovou službu](../../machine-learning/studio/publish-a-machine-learning-web-service.md).

## <a name="get-started-with-r-server-on-hdinsight"></a>Začínáme s R Serverem ve službě HDInsight

Vytvořit cluster R serverem v Azure HDInsight, vyberte **R Server** clusteru typ při vytváření clusteru HDInsight pomocí portálu Azure. Typ clusteru R Server zahrnuje R Server na datové uzly clusteru a na hraniční uzel, který slouží jako cílová zóny na základě R Server analýzy. V tématu [Začínáme s R serverem v HDInsight](r-server-get-started.md) podrobný postup vytvoření clusteru.

## <a name="why-choose-r-server-in-hdinsight"></a>Proč zvolit R Server v prostředí HDInsight?

R Server v prostředí HDInsight poskytuje možnost používat R Server v Azure. R Server zahrnuje:

+ **Nejlepší inovací AI od společnosti Microsoft a s otevřeným zdrojem**

  Microsoft se snaží zpřístupněte AI pro každé jednotlivé a organizaci. R Server obsahuje bohatou sadu vysoce škálovatelnou a distribuované sadu algoritmů, například [RevoscaleR](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/revoscaler), [revoscalepy](https://docs.microsoft.com/machine-learning-server/python-reference/revoscalepy/revoscalepy-package), a [microsoftML](https://docs.microsoft.com/machine-learning-server/python-reference/microsoftml/microsoftml-package) , může pracovat na větší velikosti dat než je velikost fyzické paměti a spusťte na širokou škálu platformy distribuovanému. Další informace o kolekci Microsoft je vlastní [balíčky R](https://docs.microsoft.com/machine-learning-server/r-reference/introducing-r-server-r-package-reference) a [balíčků Python](https://docs.microsoft.com/machine-learning-server/python-reference/introducing-python-package-reference) součástí produktu.
  
  R Server přemosťuje inovace tyto společnosti Microsoft a ty pocházejících z komunity s otevřeným zdrojem (sadách R, Python a AI) všechny nad platformu jeden podnikové úrovni. Libovolný balíček R nebo Python machine learning s otevřeným zdrojem můžete pracovat vedle sebe se všechny vlastní inovací od společnosti Microsoft. 

+ **Jednoduchý, zabezpečenou a špičkové operationalization a Správa**

  Podniky spoléhat na prostředí a tradiční operationalization vzorů skončili Investujete mnohem čas a úsilí na této oblasti. Problémové body často výsledkem zvýšeným náklady a zahrnují zpoždění: Doba překladu pro modely, iterací, abyste zajistili jejich platný a aktuální, zákonné schválení, Správa oprávnění prostřednictvím operationalization.

  R Server nabízí nejlepší třídy [operationalization](https://docs.microsoft.com/machine-learning-server/what-is-operationalization) – od času dokončení model machine learning, trvá jen několik kliknutí k vytváření webových služeb rozhraní API. Tyto [webové služby](https://docs.microsoft.com/machine-learning-server/operationalize/concept-what-are-web-services) hostovaných na serveru mřížky místně nebo v cloudu a může být integrovaná v-obchodní aplikace. Možnost nasazení na elastické mřížky umožňuje škálování bezproblémově se potřebám vaší firmy, a to jak pro batch a v reálném čase vyhodnocování. Pokyny najdete v tématu [zprovoznit R serverem v HDInsight](r-server-operationalize.md).

+ **Hloubkové ekosystém oznámeních podporujících zapojení uživatelů k poskytování úspěch zákazníka s optimální celkové náklady na vlastnictví**

  Jednotlivce začátkem na cestu podáním žádosti inteligentního nebo jednoduše se chtějí další nové world AI a strojového učení, třeba správné prostředky, aby jim pomohl začít. Kromě této dokumentace společnost Microsoft poskytuje několik materiály a vykonává několik partnery školení vám pomůžou rychle pochopit práci a produktivitu rychle.


## <a name="key-features-of-r-server-on-hdinsight"></a>Klíčové funkce R serverem v HDInsight

Následující funkce jsou součástí R serverem v HDInsight.

| Funkce kategorie | Popis |
|------------------|-------------|
| Povolit R | [Balíčky R](https://docs.microsoft.com/machine-learning-server/r-reference/introducing-r-server-r-package-reference) pro řešení, které jsou napsané v R, otevřeným zdrojem rozdělení R a spuštění infrastruktury pro spuštění skriptu. |
| Povolit Python | [Moduly jazyka Python](https://docs.microsoft.com/machine-learning-server/python-reference/introducing-python-package-reference) pro řešení, které jsou napsané v Pythonu, otevřeným zdrojem rozdělení Python a spuštění infrastruktury pro spuštění skriptu.  
| [Předem trénované modely](https://docs.microsoft.com/machine-learning-server/install/microsoftml-install-pretrained-models) | Pro visual analýzy a analýzy postojích text připravena ke stanovení skóre dat je zadat. |
| [Nasazení a využívat](r-server-operationalize.md) | Zprovoznění vašeho serveru a nasadit řešení jako webovou službu. |
| [Vzdálené spuštění](r-server-hdinsight-manage.md#connect-remotely-to-microsoft-ml-server-or-client) | Spuštění vzdálené relace na serveru R ve vaší síti ze své klientské pracovní stanice. |


## <a name="data-storage-options-for-r-server-on-hdinsight"></a>Možnosti úložiště dat pro R Server v HDInsight

Výchozí úložiště pro systém souborů HDFS clusterů HDInsight, které může být přidruženy k účtu Azure Storage nebo Azure Data Lake Store. Toto přidružení zajistí, že ať data jsou odeslána do clusteru se provádí trvalé úložiště během analýzy a data jsou k dispozici i po odstranění clusteru. Existují různé nástroje pro zpracování přenos dat do úložiště možnost, kterou vyberete, včetně zařízení nahrávání založené na portálu účtu úložiště a [AzCopy](../../storage/common/storage-use-azcopy.md) nástroj.

Máte možnost přidání přístupu do dalších objektů Blob a ukládá Data lake během procesu bez ohledu na to možnost primárního úložiště používá zřizování clusteru. V tématu [Začínáme s R serverem v HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-r-server-get-started) informace o přidávání přístup do dalších účtů. Najdete v článku doplňující [možnosti úložiště Azure pro R Server v HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-r-server-storage) článek a další informace o používání více účtů úložiště.

Můžete také použít [Azure Files](../../storage/files/storage-how-to-use-files-linux.md) jako řešením úložiště pro použití na uzlu edge. Soubory Azure umožňuje připojit sdílené složce, která byla vytvořena ve službě Azure Storage do souboru systému Linux. Další informace o těchto možnostech úložiště dat pro R Server v clusteru HDInsight najdete v tématu [možnosti úložiště Azure pro R Server v HDInsight](r-server-storage.md).

## <a name="access-machine-learning-server-on-the-cluster"></a>Přístup Machine Learning serveru v clusteru

Můžete se připojit k serveru Microsoft Machine Learning na uzlu edge pomocí prohlížeče. Je nainstalovaná ve výchozím nastavení při vytváření clusteru. Další informace najdete v tématu [získat stared s R serverem v HDInsight](r-server-get-started.md). Můžete také připojíte k serveru ML z příkazového řádku pomocí SSH nebo PuTTY přístup ke konzole R. 

## <a name="develop-and-run-r-scripts"></a>Vývoj a spouštět skripty R

Vytvářet a spouštět skripty R můžete použít některou z 8000 + open-source R balíčky kromě paralelizovaná málo a distribuované rutiny k dispozici v knihovně ScaleR. Obecně platí skript, který běží na uzlu edge s R Server běží v rámci překladač R v tomto uzlu. Výjimky jsou tyto kroky, které je třeba volat funkci ScaleR s výpočetní kontext, který je nastavený na Hadoop mapy snížení (RxHadoopMR) nebo Spark (RxSpark). V takovém případě je funkce spuštěná distribuované způsobem napříč dat (úlohy) uzlů clusteru, které jsou spojeny s daty odkazuje. Další informace o možnostech kontextu různými výpočetními najdete v tématu [výpočetní kontextu možnosti pro R Server v HDInsight](r-server-compute-contexts.md).

## <a name="operationalize-a-model"></a>Zprovoznění modelu

Po dokončení modelování vaše data můžete zprovoznit model, který má provádět předpovědi nových dat, buď z Azure a místní. Tento proces se označuje jako vyhodnocování. Vyhodnocování lze provést v prostředí HDInsight, Azure Machine Learning nebo místně.

### <a name="score-in-hdinsight"></a>Skóre v HDInsight
Ke stanovení skóre v HDInsight, zapisovat R funkce, která volá modelu provádět předpovědi nový datový soubor, který jste načtených do účtu úložiště. Potom uložte jsou předpovědi zpět k účtu úložiště. Spuštěním této rutiny na vyžádání v hraničního uzlu clusteru nebo pomocí naplánované úlohy.

### <a name="score-in-azure-machine-learning-aml"></a>Skóre v Azure strojového učení (AML)
Ke stanovení skóre pomocí Azure Machine Learning, použít balíček Azure Machine Learning R open-source známé jako [AzureML](https://cran.r-project.org/web/packages/AzureML/vignettes/getting_started.html) Chcete-li publikovat model jako Azure webové služby. Pro větší pohodlí si tento balíček je nainstalovány na uzlu edge. V dalším kroku použít zařízení v Azure Machine Learning vytvořit uživatelské rozhraní pro webovou službu a poté volání webové služby podle potřeby pro vyhodnocování.

Pokud zvolíte tuto možnost, je nutné převést všechny objekty modelu ScaleR objekty ekvivalentní modelu open source pro použití s webovou službou. Pomocí funkce Převod ScaleR, jako například `as.randomForest()` pro modely na základě komplet, pro tento převod.

### <a name="score-on-premises"></a>Skóre na místě
Ke stanovení skóre místní po vytvoření modelu, můžete serializuje model v R, ho stáhnout, deserializovat ho a použít ho pro vyhodnocování nová data. Vám může skóre pro nová data s využitím metody popsané výše v [vyhodnocování v HDInsight](#scoring-in-hdinsight) nebo pomocí [DeployR](https://deployr.revolutionanalytics.com/).

## <a name="maintain-the-cluster"></a>Udržovat clusteru

### <a name="install-and-maintain-r-packages"></a>Instalaci a údržbě balíčky R

Na uzlu edge od většinu kroků existuje spustit skripty R se vyžadují většinu R balíčky, které používáte. K instalaci dalších balíčků R na uzlu edge, můžete obvykle `install.packages()` metody v jazyce R.

Pokud právě používáte rutiny z knihovny ScaleR napříč clusterem, není nutné obvykle nainstalovat další balíčky R na datové uzly. Však může být zapotřebí další balíčky pro podporu použití **rxExec** nebo **RxDataStep** spuštění na uzlech data.

V takových případech další balíčky můžete nainstalovat akce skriptu po vytvoření clusteru. Další informace najdete v tématu [spravovat R Server v clusteru HDInsight](r-server-hdinsight-manage.md).

### <a name="change-hadoop-mapreduce-memory-settings"></a>Změňte nastavení paměti Hadoop MapReduce

Chcete-li změnit velikost paměti, která je k dispozici pro R Server při spuštění úlohy MapReduce lze upravit clusteru. Chcete-li upravit cluster, použijte rozhraní Apache Ambari, který je k dispozici prostřednictvím Azure okno portálu pro váš cluster. Pokyny o tom, jak získat přístup k rozhraní Ambari pro váš cluster najdete v tématu [Správa clusterů HDInsight pomocí webového uživatelského rozhraní Ambari](../hdinsight-hadoop-manage-ambari.md).

Je také možné změnit velikost paměti, která je k dispozici k serveru R pomocí přepínače Hadoop ve volání **RxHadoopMR** následujícím způsobem:

    hadoopSwitches = "-libjars /etc/hadoop/conf -Dmapred.job.map.memory.mb=6656"  

### <a name="scale-your-cluster"></a>Škálování clusteru

Existující cluster R serverem v HDInsight můžete škálovat nahoru nebo dolů prostřednictvím portálu. Rozšiřování prostředků, můžete získat další kapacitu, které byste mohli potřebovat pro větší úlohy zpracování, nebo můžete škálovat zpět cluster nečinný. Pokyny o tom, jak škálování clusteru najdete v tématu [Správa clusterů HDInsight](../hdinsight-administer-use-portal-linux.md).

### <a name="maintain-the-system"></a>Udržování systému

Základní virtuální počítače Linux v clusteru služby HDInsight provádění údržby použít oprav operačního systému a další aktualizace špičku. Obvykle údržby se provádí na 3:30 AM (podle místního času pro virtuální počítač) každé pondělí a čtvrtek. Aktualizace se provádějí v tak, aby se nemáte vliv více než čtvrtletí clusteru současně.  

Vzhledem k tomu, že jsou redundantní hlavních uzlech a dopad na všechny uzly dat, všechny úlohy, které jsou spuštěné během této doby může zpomalit. Nicméně by měly být spuštěny stále dokončen. Všechny vlastní software nebo místní data, zda máte se zachová napříč tyto události údržby, pokud dojde k závažné chybě, která vyžaduje nové vytvoření clusteru.

## <a name="ide-options-for-r-server-on-an-hdinsight-cluster"></a>Možnosti IDE pro R Server v clusteru HDInsight

Linux hraničního uzlu clusteru HDInsight je cílová zónu pro R na základě analýzy. Nejnovější verze služby HDInsight výchozí instalaci Rstudia serveru zadejte na uzlu edge jako IDE se založené na prohlížeči. Použití serveru Rstudia jako rozhraní IDE pro vývoj a spouštění skriptů R, může být výrazně zvýšit produktivitu, než jen pomocí konzole R.

Kromě toho můžete nainstalovat plochy IDE a používat pro přístup ke clusteru prostřednictvím použití vzdáleného kontextu výpočetní MapReduce nebo Spark. Mezi možnosti patří společnosti Microsoft [R Tools pro Visual Studio](https://www.visualstudio.com/features/rtvs-vs.aspx) (RTVS), Rstudia a Walware adresy, na základě Eclipse [StatET](http://www.walware.de/goto/statet).

Nakonec získat přístup ke konzole R na uzlu edge tak, že zadáte **R** na příkazovém řádku Linux po připojení prostřednictvím protokolu SSH nebo PuTY. Pokud používáte rozhraní konzoly, je vhodné spustit v jiném okně, textový editor pro vývoj skriptu R a kopírování a vkládání části souboru skriptu do konzoly R podle potřeby.

## <a name="pricing"></a>Ceny

Ceny, které jsou přidružené clusteru HDInsight bez R Server jsou strukturovaná podobně jako ceny pro standardní clusterů HDInsight. Mezi název, data a uzly okraj, a uveďte základní hodinu zdvih jsou založené na velikost základní virtuálních počítačů. Další informace o cenách prostředí HDInsight najdete v tématu [HDInsight ceny](https://azure.microsoft.com/pricing/details/hdinsight/).

## <a name="next-steps"></a>Další postup

Další informace o tom, jak používat R Server v clusterech HDInsight, naleznete v následujících tématech:

* [Začínáme se službou cluster R serverem v HDInsight](r-server-get-started.md)
* [Možnosti výpočetního kontextu pro cluster R Serveru v HDInsight](r-server-compute-contexts.md)
* [Možnosti služby Azure Storage pro cluster R Serveru v HDInsight](r-server-storage.md)
