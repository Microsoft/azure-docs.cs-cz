---
title: Hloubkové produsšny – pokročilá analytika – Azure HDInsight
description: Zjistěte, jak pokročilá analytika používá algoritmy ke zpracování velkých objemů dat v Azure HDInsight.
author: ashishthaps
ms.author: ashishth
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 01/01/2020
ms.openlocfilehash: 6fc6ea9ca0ce3bf82f027b964db94df50f07f2bd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75646519"
---
# <a name="deep-dive---advanced-analytics"></a>Hloubkové potápění – pokročilá analytika

## <a name="what-is-advanced-analytics-for-hdinsight"></a>Co je pokročilá analýza pro HDInsight?

HDInsight poskytuje možnost získat cenné informace z velkého množství strukturovaných, nestrukturovaných a rychle se pohybujících dat. Pokročilá analytika je použití vysoce škálovatelných architektur, statistických modelů a modelů strojového učení a inteligentních řídicích panelů, které vám poskytnou smysluplné přehledy. Strojové učení neboli *prediktivní analýzy*používá algoritmy, které identifikují vztahy ve vašich datech a učí se z nich, aby se předpovědi a řídily vašimi rozhodnutími.

## <a name="advanced-analytics-process"></a>Pokročilý analytický proces

![Tok pokročilého analytického procesu](./media/apache-hadoop-deep-dive-advanced-analytics/hdinsight-analytic-process.png)

Po identifikaci obchodního problému a zahájení shromažďování a zpracování dat je třeba vytvořit model, který představuje otázku, kterou chcete předpovědět. Váš model bude používat jeden nebo více algoritmů strojového učení, aby se typ předpovědi, která nejlépe vyhovuje vašim obchodním potřebám.  Většina vašich dat by měla být použita k trénování modelu, přičemž zbytek by měl být použit k jeho testování nebo vyhodnocení.

Po vytvoření, načtení, testování a vyhodnocení modelu je dalším krokem nasazení modelu tak, aby začal dodávat odpovědi na vaše otázky. Posledním krokem je sledování výkonu modelu a jeho vyladění podle potřeby.

## <a name="common-types-of-algorithms"></a>Běžné typy algoritmů

Pokročilá analytická řešení poskytují sadu algoritmů strojového učení. Zde je přehled kategorií algoritmů a souvisejících běžných případů použití.

![Souhrny kategorií strojového učení](./media/apache-hadoop-deep-dive-advanced-analytics/machine-learning-use-cases.png)

Spolu s výběrem algoritmu(s nejlepší přizpůsobení), je třeba zvážit, zda je nutné poskytnout data pro školení. Algoritmy strojového učení jsou rozděleny do následujících kategorií:

* Pod dohledem - algoritmus musí být vyškolen na sadu označených dat, než může poskytnout výsledky
* Pod polodohledem - algoritmus může být rozšířen o další cíle prostřednictvím interaktivního dotazu trenéra, které nebyly k dispozici v počáteční fázi tréninku
* Bez dozoru - algoritmus nevyžaduje trénovací data
* Posilování - algoritmus používá softwarové agenty k určení ideálního chování v určitém kontextu (často používaném v robotice)

| Kategorie algoritmu| Použití | Typ učení | Algoritmy |
| --- | --- | --- | -- |
| Classification | Klasifikace lidí nebo věcí do skupin | Pod dohledem | Rozhodovací stromy, Logistická regrese, neuronové sítě |
| Clustering | Rozdělení souboru příkladů do homogenních skupin | Bez dozoru | K-znamená shlukování |
| Detekce vzorů | Identifikace častých přidružení v datech | Bez dozoru | Association rules |
| Regrese | Předvídání číselných výsledků | Pod dohledem | Lineární regrese, neuronové sítě |
| Posílení | Určení optimálního chování pro roboty | Posílení | Monte Carlo Simulace, DeepMind |

## <a name="machine-learning-on-hdinsight"></a>Strojové učení na HDInsight

HDInsight má několik možností strojového učení pro pokročilý pracovní postup analýzy:

* Strojové učení a Apache Spark
* Služby r a ml
* Azure Machine Learning a Apache Hive
* Apache Spark a Hluboké učení

### <a name="machine-learning-and-apache-spark"></a>Strojové učení a Apache Spark

[HDInsight Spark](../spark/apache-spark-overview.md) je nabídka hostovaná v Azure [apache spark](https://spark.apache.org/), sjednocené, open source, paralelní zpracování dat, které používá zpracování v paměti k posílení analýzy velkých objemů dat. Modul pro zpracování Spark je navržen pro rychlost, snadné použití a sofistikované analýzy. Možnosti distribuovaného výpočtu sparku v paměti z něj činí dobrou volbu pro iterativní algoritmy používané ve strojovém učení a výpočtech grafů.

Existují tři škálovatelné knihovny strojového učení, které přinášejí možnosti algoritmického modelování do tohoto distribuovaného prostředí:

* [**MLlib**](https://spark.apache.org/docs/latest/ml-guide.html) - MLlib obsahuje původní rozhraní API postavené na spark RDD.
* [**SparkML**](https://spark.apache.org/docs/1.2.2/ml-guide.html) - SparkML je novější balíček, který poskytuje rozhraní API vyšší úrovně postavené na datových rámcích Spark pro vytváření kanálů ML.
* [**MMLSpark**](https://github.com/Azure/mmlspark) - Microsoft Machine Learning knihovna pro Apache Spark (MMLSpark) je navržen tak, aby datové vědce produktivnější na Spark, zvýšit rychlost experimentování, a využít špičkové techniky strojového učení, včetně hlubokého učení, na velmi velké datové sady. Knihovna MMLSpark zjednodušuje běžné úlohy modelování pro vytváření modelů v PySkaparku.

### <a name="r-and-ml-services"></a>Služby r a ml

Jako součást HDInsight, můžete vytvořit HDInsight cluster s [ML Služby](../r-server/r-server-overview.md) připravené k použití s masivní datové sady a modely. Tato nová funkce poskytuje datovým vědcům a statistikům známé rozhraní R, které lze škálovat na vyžádání prostřednictvím hdinsightu, bez režie nastavení a údržby clusteru.

### <a name="azure-machine-learning-and-apache-hive"></a>Azure Machine Learning a Apache Hive

[Azure Machine Learning Studio (classic)](https://studio.azureml.net/) poskytuje nástroje pro modelování prediktivní analýzy a plně spravovanou službu, kterou můžete použít k nasazení prediktivních modelů jako webových služeb připravených ke spotřebování. Azure Machine Learning poskytuje nástroje pro vytváření kompletních prediktivních analytických řešení v cloudu pro rychlé vytváření, testování, zprovoznění a správu prediktivních modelů. Vyberte si z velké knihovny algoritmů, použijte webové studio pro vytváření modelů a snadno nasaďte model jako webovou službu.

### <a name="apache-spark-and-deep-learning"></a>Apache Spark a Hluboké učení

[Hluboké učení](https://www.microsoft.com/research/group/dltc/) je odvětví strojového učení, které využívá *hluboké neuronové sítě* (DNNs), inspirované biologickými procesy lidského mozku. Mnozí badatelé vnímají hluboké učení jako slibný přístup k umělé inteligenci. Některé příklady hlubokého učení jsou překladače mluveného jazyka, systémy rozpoznávání obrazu a strojové uvažování. Společnost Microsoft vyvinula bezplatnou a snadno použitelnou sadu [Microsoft Cognitive Toolkit](https://www.microsoft.com/en-us/cognitive-toolkit/)s otevřeným zdrojovým kódem, aby pomohla pokročit ve své práci v hlubokém učení. Sada nástrojů je široce využívána širokou škálou produktů společnosti Microsoft, společnostmi po celém světě, které potřebují nasadit hloubkové učení ve velkém měřítku, a studenty, kteří se zajímají o nejnovější algoritmy a techniky.

## <a name="scenario---score-images-to-identify-patterns-in-urban-development"></a>Scénář - Skóre obrázky k identifikaci vzory v městském rozvoji

Podívejme se na příklad pokročilého kanálu strojového učení analýzy analýzy pomocí HDInsight.

V tomto scénáři uvidíte, jak dny vyrobené v rámci hlubokéučení, Microsoft Cognitive Toolkit (CNTK), lze zprovoznit pro vyhodnocování velké image kolekce uložené v účtu azure blob storage pomocí PySpark v clusteru HDInsight Spark. Tento přístup se používá pro společný případ použití DNN, klasifikaci leteckého obrazu a lze jej použít k identifikaci nedávných vzorů v rozvoji měst.  Budete používat předem vyškolený model klasifikace obrázků. Model je předem trénovaný na [datové sadě CIFAR-10](https://www.cs.toronto.edu/~kriz/cifar.html) a byl aplikován na 10 000 zatajených obrázků.

V tomto scénáři pokročilé analýzy jsou tři klíčové úkoly:

1. Vytvořte cluster Azure HDInsight Hadoop s distribucí Apache Spark 2.1.0.
2. Spusťte vlastní skript pro instalaci sady Microsoft Cognitive Toolkit do všech uzlů clusteru Azure HDInsight Spark.
3. Nahrajte předem sestavený poznámkový blok Jupyter do clusteru HDInsight Spark a aplikujte trénovaný model hlubokého učení Microsoft Cognitive Toolkit na soubory v účtu úložiště objektů blob Azure pomocí rozhraní Api Spark Pythonu (PySpark).

Tento příklad používá sadu obrázků CIFAR-10 zkompilovanou a distribuovanou Alexem Krizhevským, Vinodem Nairem a Geoffreym Hintonem. Datová sada CIFAR-10 obsahuje 60 000 barevných obrázků 32×32, které patří do 10 vzájemně se vylučujících tříd:

![Ukázkové obrázky strojového učení](./media/apache-hadoop-deep-dive-advanced-analytics/machine-learning-images.png)

Další informace o datové sadě naleznete v tématu Alex Krizhevsky [učení více vrstev funkcí z tiny obrázky](https://www.cs.toronto.edu/~kriz/learning-features-2009-TR.pdf).

Datová sada byla rozdělena do trénovací sady 50 000 obrázků a testovací sady 10 000 obrázků. První sada byla použita k trénování dvacátévrstvé konvoluční reziduální sítě (ResNet) model pomocí Microsoft Cognitive Toolkit podle [tohoto kurzu](https://github.com/Microsoft/CNTK/tree/master/Examples/Image/Classification/ResNet) z úložiště Cognitive Toolkit GitHub. Zbývajících 10 000 snímků bylo použito k testování přesnosti modelu. To je místo, kde distribuované výpočetní vstoupí do hry: úkol pre-zpracování a bodování obrázky je velmi paralelizovatelný. S uloženým trénovaným modelem v ruce jsme použili:

* PySpark distribuovat bitové kopie a trénovaný model do pracovních uzlů clusteru.
* Python předem zpracuje obrázky na každém uzlu clusteru HDInsight Spark.
* Cognitive Toolkit načíst model a skóre předem zpracované obrazy na každém uzlu.
* Jupyter Notebooky spustit skript PySpark, agregovat výsledky a použít [Matplotlib](https://matplotlib.org/) vizualizovat výkon modelu.

Celé předběžné zpracování/vyhodnocování 10 000 bitových kopií trvá méně než jednu minutu v clusteru se 4 pracovními uzly. Model přesně předpovídá popisky ~9,100 (91%) Obrázky. Matice záměny ilustruje nejběžnější chyby klasifikace. Například matice ukazuje, že nesprávné označení psů jako koček a naopak se vyskytuje častěji než u jiných párů štítků.

![Graf výsledků strojového učení](./media/apache-hadoop-deep-dive-advanced-analytics/machine-learning-results.png)

### <a name="try-it-out"></a>Vyzkoušejte to!

Postupujte [podle tohoto kurzu](../spark/apache-spark-microsoft-cognitive-toolkit.md) k implementaci tohoto řešení end-to-end: nastavit HDInsight Spark cluster, nainstalovat Cognitive Toolkit a spustit Jupyter notebook, který skóre 10.000 CIFAR bitové kopie.

## <a name="next-steps"></a>Další kroky

Apache Hive a Azure Machine Learning

* [Apache Hive a Azure Machine Learning od konce](../../machine-learning/team-data-science-process/hive-walkthrough.md)
* [Použití clusteru Azure HDInsight Hadoop v datové sadě o velikosti 1 TB](../../machine-learning/team-data-science-process/hive-criteo-walkthrough.md)

Apache Spark a MLLib

* [Strojové učení s Apache Spark na HDInsight](../../machine-learning/team-data-science-process/spark-overview.md)
* [Apache Spark se strojovým učením: Použijte Apache Spark v HDInsightu pro analýzu teploty budovy pomocí dat HVAC](../spark/apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark s machine learningem: Použijte Apache Spark v HDInsightu k předvídání výsledků kontroly potravin](../spark/apache-spark-machine-learning-mllib-ipython.md)

Hloubkové učení, sada kognitivních nástrojů a další

* [Virtuální počítač Azure pro datové vědy](../../machine-learning/data-science-virtual-machine/overview.md)
* [Představujeme H2O.ai na Azure HDInsight](https://azure.microsoft.com/blog/introducing-h2o-ai-with-on-azure-hdinsight-to-bring-the-most-robust-ai-platform-for-enterprises/)
