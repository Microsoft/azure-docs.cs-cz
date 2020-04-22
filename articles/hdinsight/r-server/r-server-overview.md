---
title: Úvod ke službám ML na Azure HDInsight
description: Přečtěte si, jak pomocí služeb ML na HDInsight vytvářet aplikace pro analýzu velkých objemů dat.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: overview
ms.custom: hdinsightactive
ms.date: 04/20/2020
ms.openlocfilehash: 76fcdb52df88be2c4033140f4bc71b28424d7f38
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/21/2020
ms.locfileid: "81687799"
---
# <a name="what-is-ml-services-in-azure-hdinsight"></a>Co je služba ML ve službě Azure HDInsight

Microsoft Machine Learning Server je k dispozici jako možnost nasazení při vytváření clusterů HDInsight v Azure. Typ clusteru, který poskytuje tuto možnost, se nazývá **ML Services**. Tato funkce poskytuje přístup na vyžádání k adaptabilní, distribuované metody analýzy na HDInsight.

Ml Services na HDInsight poskytuje nejnovější funkce pro analýzu založenou na R na datové sady prakticky libovolné velikosti. Datové sady lze načíst do azure blob nebo úložiště datového jezera. Vaše aplikace založené na R můžete použít 8000 + open source R balíčky. Rutiny v ScaleR, Microsoft big data analytics balíček jsou také k dispozici.

Hraniční uzel poskytuje vhodné místo pro připojení ke clusteru a spuštění skriptů R. Hraniční uzel umožňuje spouštět paralelizované distribuované funkce ScaleR napříč jádry serveru. Můžete je také spustit přes uzly clusteru pomocí ScaleR hadoop mapy snížit. Můžete také použít apache spark výpočetní kontexty.

Modely nebo předpovědi, které jsou výsledkem analýzy lze stáhnout pro místní použití. Můžou být `operationalized` taky jinde v Azure. Zejména prostřednictvím [Azure Machine Learning Studio (klasické)](https://studio.azureml.net)a webové [služby](../../machine-learning/studio/deploy-a-machine-learning-web-service.md).

## <a name="get-started-with-ml-services-on-hdinsight"></a>Začínáme se službami ML na HDInsightu

Chcete-li vytvořit cluster služby ML services v hdinsightu, vyberte typ clusteru **služby ML** Services. Typ clusteru služby ML Services zahrnuje server ML server na datových uzlech a hraniční uzel. Hraniční uzel slouží jako přistávací zóna pro analýzy založené na službách ML Services. Viz [Vytvoření clusterů Apache Hadoop pomocí portálu Azure](../hdinsight-hadoop-create-linux-clusters-portal.md) pro návod, jak vytvořit cluster.

## <a name="why-choose-ml-services-in-hdinsight"></a>Proč zvolit služby ML ve službě HDInsight?

Služby ML ve službě HDInsight poskytují následující výhody:

### <a name="ai-innovation-from-microsoft-and-open-source"></a>Inovace v oblasti umělá iv od Microsoftu a open source

  Ml Services zahrnuje vysoce adaptabilní, distribuovaná sada algoritmů, jako je [RevoscaleR](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/revoscaler), [revoscalepy](https://docs.microsoft.com/machine-learning-server/python-reference/revoscalepy/revoscalepy-package)a [MicrosoftML](https://docs.microsoft.com/machine-learning-server/python-reference/microsoftml/microsoftml-package). Tyto algoritmy mohou pracovat na velikosti dat větší než velikost fyzické paměti. Oni také běží na široké škále platforem v distribuované způsobem. Další informace o kolekci vlastních [balíčků R](https://docs.microsoft.com/machine-learning-server/r-reference/introducing-r-server-r-package-reference) společnosti Microsoft a [balíčků Pythonu,](https://docs.microsoft.com/machine-learning-server/python-reference/introducing-python-package-reference) které jsou součástí produktu.
  
  Služba ML Services tyto inovace a příspěvky společnosti Microsoft převádí z open source komunity (sady nástrojů R, Python a AI). To vše na jedné podnikové platformě. Jakýkoli balíček r nebo pythonopen source strojového učení může pracovat bok po boku s jakoukoli proprietární inovací od společnosti Microsoft.

### <a name="simple-secure-and-high-scale-operationalization-and-administration"></a>Jednoduchá, bezpečná a rozsáhlá operativizace a správa

  Podniky, které se spoléhají na tradiční paradigmata a prostředí, investují mnoho času a úsilí do zprovoznění. Tato akce má za následek nafouknuté náklady a zpoždění, včetně času překladu pro: modely, iterace, aby byly platné a aktuální, schválení regulačními orgány a oprávnění pro správu.

  Ml Services nabízí podnikové provozní [nastavení](https://docs.microsoft.com/machine-learning-server/what-is-operationalization). Po dokončení modelu strojového učení trvá generování api webových služeb jen několik kliknutí. Tyto [webové služby](https://docs.microsoft.com/machine-learning-server/operationalize/concept-what-are-web-services) jsou hostovány na serverové mřížce v cloudu a lze je integrovat s obchodními aplikacemi. Možnost nasazení do elastické mřížky umožňuje bezproblémové škálování podle potřeb vaší firmy, a to jak pro dávkové, tak pro vyhodnocování v reálném čase. Pokyny naleznete v [tématu Operationalize ML Services on HDInsight](r-server-operationalize.md).

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
| [Nasazení a využití](r-server-operationalize.md) | `Operationalize`serveru a nasazovat řešení jako webovou službu. |
| [Vzdálené spuštění](r-server-hdinsight-manage.md#connect-remotely-to-microsoft-ml-services) | Spusťte vzdálené relace v clusteru služby ML v síti z klientské pracovní stanice. |

## <a name="data-storage-options-for-ml-services-on-hdinsight"></a>Možnosti ukládání dat pro služby ML na HDInsightu

Výchozí úložiště pro systém souborů HDFS může být účet Azure Storage nebo Azure Data Lake Storage. Odeslaná data do úložiště clusteru během analýzy jsou trvalá. Data jsou k dispozici i po odstranění clusteru. Různé nástroje mohou zpracovat přenos dat do úložiště. Nástroje zahrnují nástroj pro nahrávání na portálu pro účet úložiště a nástroj AzCopy.

Můžete povolit přístup k dalším úložištím objektů blob a datových jezer během vytváření clusteru. Nejste omezeni možností primárního úložiště, která se používá.  Další informace o používání více účtů úložiště najdete v článku [Možnosti úložiště Azure pro služby ML na webu HDInsight.](./r-server-storage.md)

Soubory Azure můžete také použít jako možnost úložiště pro použití na hraničním uzlu. Azure Files umožňuje sdílené složky vytvořené v Azure Storage do systému souborů Linuxu. Další informace najdete v [tématu Možnosti úložiště Azure pro služby ML na HDInsight](r-server-storage.md).

## <a name="access-ml-services-edge-node"></a>Hraniční uzel služby Access ML Services

K serveru Microsoft ML server na hraničním uzlu se můžete připojit pomocí prohlížeče nebo ssh/putty. Konzola R je ve výchozím nastavení nainstalována během vytváření clusteru.  

## <a name="develop-and-run-r-scripts"></a>Vývoj a spouštění skriptů R

Vaše R skripty můžete použít některý z 8000 + open source R balíčky. Můžete také použít paralelizované a distribuované rutiny z knihovny ScaleR. Skripty spuštěné na hraničním uzlu jsou spuštěny v rámci překladače R v tomto uzlu. S výjimkou kroků, které volají funkce ScaleR s mapovým redukcem (RxHadoopMR) nebo sítosem (RxSpark) výpočetním kontextem. Funkce spustit distribuovaným způsobem napříč datovými uzly, které jsou přidruženy k datům. Další informace o možnostech kontextu naleznete v [tématu Možnosti kontextu výpočetního kontextu pro služby ML ve službě HDInsight](r-server-compute-contexts.md).

## <a name="operationalize-a-model"></a>`Operationalize`model

Po dokončení modelování dat `operationalize` model pro předpověď pro nová data z Azure nebo místně. Tento proces se označuje jako vyhodnocování. Vyhodnocování se dá dělat v HDInsightu, Azure Machine Learningu nebo místně.

### <a name="score-in-hdinsight"></a>Skóre v HDInsight

Chcete-li skórovat v HDInsight, napište funkci R. Funkce volá váš model, aby předpovědi pro nový datový soubor, který jste načetli do účtu úložiště. Potom uložte předpovědi zpět do účtu úložiště. Tuto rutinu můžete spustit na vyžádání na hraničním uzlu clusteru nebo pomocí naplánované úlohy.

### <a name="score-in-azure-machine-learning-aml"></a>Skóre v Azure Machine Learning (AML)

Pokud chcete skóre pomocí Azure Machine Learning, použijte open source balíček Azure Machine Learning R známý jako [AzureML](https://cran.r-project.org/src/contrib/Archive/AzureML/) k publikování modelu jako webové služby Azure. Pro větší pohodlí je tento balíček předinstalován na hraničním uzlu. Dále použijte zařízení v Azure Machine Learning k vytvoření uživatelského rozhraní pro webovou službu a pak volání webové služby podle potřeby pro vyhodnocování. Potom převeďte objekty modelu ScaleR na ekvivalentní objekty modelu s otevřeným zdrojovým kódem pro použití s webovou službou. Pro tento převod použijte funkce asektercionu, například `as.randomForest()` pro modely založené na souboru.

### <a name="score-on-premises"></a>Skóre v místním prostředí

Chcete-li skóre v místním po vytvoření modelu: serializovat model v R, stáhnout, de-serializovat, pak jej použít pro vyhodnocování nových dat. Nová data můžete získat pomocí přístupu popsaného výše v části Skóre v HDInsight nebo pomocí [webových služeb](https://docs.microsoft.com/machine-learning-server/operationalize/concept-what-are-web-services).

## <a name="maintain-the-cluster"></a>Udržovat cluster

### <a name="install-and-maintain-r-packages"></a>Instalace a údržba balíčků R

Většina balíčků R, které používáte, je vyžadována na hraničním uzlu, protože většina kroků skriptů R je zde spuštěna. Chcete-li nainstalovat další balíčky R na `install.packages()` hraniční uzel, můžete použít metodu v R.

Pokud právě používáte rutiny knihovny ScaleR, obvykle nepotřebujete další balíčky R. Možná budete potřebovat další balíčky pro spuštění **rxExec** nebo **RxDataStep** na datových uzlech.

Další balíčky lze nainstalovat pomocí akce skriptu po vytvoření clusteru. Další informace naleznete v [tématu Správa služeb ML v clusteru HDInsight](r-server-hdinsight-manage.md).

### <a name="change-apache-hadoop-mapreduce-memory-settings"></a>Změna apache hadoop mapySnížit nastavení paměti

Dostupnou paměť služby ML lze upravit při spuštění úlohy MapReduce. Chcete-li upravit cluster, použijte pro svůj cluster u imbarského uzla. Pokyny k uživatelskému rozhraní Ambari najdete [v tématu Správa clusterů HDInsight pomocí webového uživatelského rozhraní Ambari](../hdinsight-hadoop-manage-ambari.md).

Dostupnou paměť pro služby ML lze změnit pomocí přepínačů Hadoop ve volání **RxHadoopMR**:

    hadoopSwitches = "-libjars /etc/hadoop/conf -Dmapred.job.map.memory.mb=6656"  

### <a name="scale-your-cluster"></a>Škálování clusteru

Existující cluster služeb ML na webu HDInsight lze škálovat nahoru nebo dolů prostřednictvím portálu. Škálování nahoru, získáte další kapacitu pro větší úlohy zpracování. Cluster uvázne zpět, když je nečinný. Pokyny k škálování clusteru najdete v tématu [Správa clusterů HDInsight](../hdinsight-administer-use-portal-linux.md).

### <a name="maintain-the-system"></a>Udržovat systém

Údržba operačního systému se provádí na podkladových virtuálních počítačích s Linuxem v clusteru HDInsight mimo pracovní dobu. Údržba se obvykle provádí každé pondělí a čtvrtek ve 3:30 ráno (místní ho času virtuálního zařízení). Aktualizace nemají vliv na více než čtvrtinu clusteru najednou.

Spuštění úloh může zpomalit během údržby. Nicméně, měly by ještě běžet až do dokončení. Všechny vlastní software nebo místní data, která jste zachovali v rámci těchto událostí údržby, pokud dojde ke katastrofickému selhání, které vyžaduje opětovné sestavení clusteru.

## <a name="ide-options-for-ml-services-on-hdinsight"></a>Možnosti ide pro služby ML na HDInsight

Hraniční uzel Linuxu clusteru HDInsight je vstupní zónou pro analýzu založenou na R. Nejnovější verze HDInsight poskytují ide RStudio Server na základě prohlížeče na hraničním uzlu. RStudio Server je produktivnější než konzola R pro vývoj a provádění.

IDE plochy může přistupovat ke clusteru prostřednictvím vzdáleného výpočetního kontextu MapReduce nebo Spark. Mezi možnosti patří: [Microsoft R Tools for Visual Studio](https://marketplace.visualstudio.com/items?itemName=MikhailArkhipov007.RTVS2019) (RTVS), RStudio a Walware Eclipse-založené StatET.

Přístup ke konzole R na hraničním uzlu zadáním **R** na příkazovém řádku. Při použití rozhraní konzoly je vhodné vyvinout skript R v textovém editoru. Potom vyjměte a vložte části skriptu do konzoly R podle potřeby.

## <a name="pricing"></a>Ceny

Ceny spojené s clusterem HDInsight služby ML jsou strukturovány podobně jako jiné typy clusterů HDInsight. Jsou založeny na velikosti podkladových virtuálních počítačů přes název, data a hraniční uzly. Core-hodina povznesení stejně. Další informace naleznete v tématu [HDInsight pricing](https://azure.microsoft.com/pricing/details/hdinsight/).

## <a name="next-steps"></a>Další kroky

Další informace o používání služeb ML v clusterech HDInsight naleznete v následujících článcích:

* [Spuštění skriptu R v clusteru služby ML v Azure HDInsight pomocí serveru RStudio](machine-learning-services-quickstart-job-rstudio.md)
* [Možnosti výpočetního kontextu pro cluster služby ML Services v HDInsight](r-server-compute-contexts.md)
* [Možnosti úložiště pro cluster ML Services na HDInsightu](r-server-storage.md)
