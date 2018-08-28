---
title: Podrobné informace – pokročilé analýzy – Azure HDInsight
description: Zjistěte, jak pokročilou analýzu ke zpracování velkých objemů dat používá algoritmy.
services: hdinsight
author: ashishthaps
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/14/2017
ms.author: ashishth
ms.openlocfilehash: 40536aae15f2ae9fa5e186cdbea1f75f7d264437
ms.sourcegitcommit: 161d268ae63c7ace3082fc4fad732af61c55c949
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/27/2018
ms.locfileid: "43047136"
---
# <a name="deep-dive---advanced-analytics"></a>Podrobné informace – pokročilé analýzy

## <a name="what-is-advanced-analytics-for-hdinsight"></a>Co je pokročilé analýzy pro HDInsight?

HDInsight poskytuje schopnost získávat cenné informace z velké objemy strukturovaných, nestrukturovaných a rychlé tempo data. Pokročilé analýzy je použití vysoce škálovatelnou architekturu, statistické a modely strojového učení a inteligentní řídicích panelů můžete poskytnout smysluplný Přehled. Strojové učení, nebo *prediktivní analýzy*, používá algoritmy, které identifikují a Učte se z relací ve vašich datech a vytvářením předpovědí a provede vaše rozhodnutí.

## <a name="advanced-analytics-process"></a>Proces pokročilých analýz

![Proces](./media/apache-hadoop-deep-dive-advanced-analytics/process.png)

Poté, co jste identifikovali obchodního problému a spustili shromažďování a zpracování dat, budete potřebovat pro vytvoření modelu, který představuje dotaz chcete předpovědět. Váš model použije jeden nebo více algoritmů strojového učení k vytvoření typu predikcí, který nejlépe vyhovuje vašim obchodním potřebám.  Většina dat by měla sloužit k natrénování modelu, s využitím rest použít k otestování nebo vyhodnocení ho. 

Po vytvoření, načtení, testování a vyhodnocení modelu, dalším krokem je jak model nasadit tak, aby začne zadání odpovědi na své otázky. Posledním krokem je monitorovat výkon vašich modelů a vyladit podle potřeby. 

## <a name="common-types-of-algorithms"></a>Běžné typy algoritmů

Pokročilá analytická řešení poskytují sadu algoritmů strojového učení. Zde je uveden seznam kategorií algoritmy a přidružené běžných obchodních případů použití.

![Ve službě Machine Learning případy použití](./media/apache-hadoop-deep-dive-advanced-analytics/ml-use-cases.png)

Společně s výběrem nejlepší těsně algoritmy, je potřeba zvážit, jestli je potřeba zadat data pro vzdělávání. Algoritmy strojového učení jsou rozdělené následujícím způsobem:

* Pod dohledem – algoritmus je potřeba předtím, než může poskytnout výsledky trénink na sadu dat s popiskem
* Částečně pod dohledem - algoritmus může být rozšířen o prostřednictvím interaktivního dotazu službou trainer, zvláštní cíle, které nebyly k dispozici během počáteční fázi přípravy
* Nastavenou možnost bez dohledu – algoritmus nevyžaduje Cvičná data 
* Posílení - algoritmus používá software agentů k určení ideální chování v určitém kontextu (často používají v robotika)


| Algoritmus kategorie| Použití | Typ studijního | Algoritmy |
| --- | --- | --- | -- |
| Klasifikace | Klasifikace lidé, případně věci do skupin | Pod dohledem | Rozhodovací stromy, logistické regrese, neuronových sítí |
| Clustering | Rozdělení sadu příklady do homogenní skupin | Nastavenou možnost bez dohledu | K-means clustering |
| Vzor detekce | Určení častým přidružení v datech | Nastavenou možnost bez dohledu | Asociačních pravidel |
| Regrese | Předpověď číselných výsledků | Pod dohledem | Lineární regrese, neuronových sítí |
| Posílení | Určit optimální chování pro roboty | Posílení | Simulace typu Monte Carlo, DeepMind |

## <a name="machine-learning-on-hdinsight"></a>Strojové učení v HDInsight

HDInsight obsahuje několik strojového učení možnosti pro pokročilou analýzu pracovní postup:

* [Strojové učení a Sparku](#machine-learning-and-spark)
* [R a služby ML](#r-and-r-server)
* [Azure Machine Learning a Hive](#azure-machine-learning-and-hive)
* [Spark a obsáhlého learningu](#spark-and-deep-learning)

### <a name="machine-learning-and-spark"></a>Strojové učení a Sparku

[HDInsight Spark](../spark/apache-spark-overview.md) je nabídka hostovaných v Azure z [Spark](http://spark.apache.org/), sjednoceném open source, rozhraní framework paralelní zpracování dat, která používá zpracování v paměti pro zvýšení analýzy velkých objemů dat. Modul zpracování Spark je postaven pro rychlost, snadné použití a sofistikované analýzy. Spark v paměti distribuovanou výpočetní možnosti usnadňují dobrou volbou pro iterativní algoritmy používané ve výpočtech machine learning a grafů. 

Existují tři škálovatelné machine learning knihovny, které přinášejí možnosti vylepšením modelování do této distribuované prostředí:

* [**MLlib** ](https://spark.apache.org/docs/latest/ml-guide.html) -MLlib obsahuje původní rozhraní API, které jsou postavené na Sparku Rdd.
* [**Ve SparkML** ](https://spark.apache.org/docs/1.2.2/ml-guide.html) -ve SparkML je novější balíček, který poskytuje vyšší úrovně rozhraní API pro vytváření kanálů ML postavené na Sparku datových rámců.
* [**MMLSpark** ](https://github.com/Azure/mmlspark) – knihovna Microsoft Machine Learning pro Apache Spark (MMLSpark) je navržené tak, aby odborníci přes data zvýšit produktivitu práce ve Sparku, zvýšit frekvenci experimentování a využívá nejmodernější machine learning techniky, včetně obsáhlého learningu na velmi rozsáhlých datových sadách. Knihovna MMLSpark zjednodušuje běžné úkoly modelování pro vytváření modelů v PySpark. 

### <a name="r-and-ml-services"></a>R a služby ML

Jako součást HDInsight, můžete vytvořit cluster služby HDInsight s [služby ML](../r-server/r-server-overview.md) připravená k použití s velkými datovými sadami a modely. Tato nová funkce poskytuje odborníkům přes data a statistikům pomocí známých rozhraní R, kterou chcete škálovat na vyžádání prostřednictvím HDInsight, bez režie nastavení clusteru a údržby.

### <a name="azure-machine-learning-and-hive"></a>Azure Machine Learning a Hive

[Azure Machine Learning Studio](https://studio.azureml.net/) poskytuje nástroje, které model prediktivní analýzy, stejně jako plně spravovanou službu, můžete své prediktivní modely nasazovat jako připravené využívání webových služeb. Azure Machine Learning poskytuje nástroje pro vytváření kompletních řešení prediktivní analýzy v cloudu a rychle vytvářet, testovat, zprovoznit a spravovat prediktivní modely. Vyberte od velké knihovny algoritmů, použít studio založeného na webu pro vytváření modelů a snadno model nasadit jako webovou službu.

### <a name="spark-and-deep-learning"></a>Spark a obsáhlého learningu

[Obsáhlý learning](https://www.microsoft.com/research/group/dltc/) je větev služby machine learning, která používá *hluboké neuronové sítě* (dopředné), která vychází z biologických procesy lidský mozek. Mnoho výzkumní pracovníci naleznete v tématu obsáhlý learning jako slibně přístup pro umělou inteligenci. Některé příklady obsáhlý learning jsou mluvený jazyk překladatele, systémy rozpoznávání obrazu a posuzování počítače. Abyste předem své vlastní práci v obsáhlého learningu, společnost Microsoft vyvinula open-source zdarma, snadno použitelný, [Microsoft Cognitive Toolkit](https://www.microsoft.com/en-us/cognitive-toolkit/). Se sadou nástrojů používá výrazně širokou škálu produktů společnosti Microsoft, společností po celém světě s museli nasazovat hloubkového učení ve velkém měřítku a studenty zájem o nejnovější algoritmy a postupy. 

## <a name="scenario---score-images-to-identify-patterns-in-urban-development"></a>Scénář – skóre Imagí pro identifikaci vzorů v rozvoje měst

Pojďme se podívat na příklad pokročilé analýzy strojového učení pomocí HDInsight kanál.

V tomto scénáři uvidíte, jak dopředné vytvořené v rámci obsáhlý learning Microsoft Cognitive Toolkit (CNTK), můžete se mají zprovoznit pro vyhodnocování kolekce velký obrázek uložených v účtu služby Azure Blob Storage pomocí PySpark na cluster HDInsight Spark. Tento přístup se použije pro běžné DNN případ použití, klasifikace leteckých snímků a slouží k identifikaci poslední vzorů v rozvoje měst.  Použijete model klasifikace předem natrénovaných obrázků. Model je předem vytrénovaných na [datovou sadu CIFAR 10](https://www.cs.toronto.edu/~kriz/cifar.html) a použily na 10 000 odmítnuté imagí.

V tomto scénáři pokročilých analýz existují tři klíčové úkoly:

1. Vytvoření clusteru Azure HDInsight Hadoop Apache Spark 2.1.0 rozdělení. 
2. Spusťte vlastní skript pro instalaci sady Microsoft Cognitive Toolkit na všech uzlech clusteru Azure HDInsight Spark. 
3. Nahrajte předem sestavených poznámkového bloku Jupyter pro váš cluster HDInsight Spark pro použití trénovaného Microsoft Cognitive Toolkit hloubkového učení modelu do souborů v účtu úložiště objektů Blob v Azure pomocí rozhraní Python API služby Spark (PySpark). 

Tento příklad používá sada kompilované a distribuované Alex Krizhevsky, Vinod Nair a Geoffrey Hinton CIFAR 10 obrázků. Datová sada CIFAR 10 obsahuje 60 000 32 × 32 bitové kopie, které patří k 10 vzájemně se vylučuje třídám barev:

![Image](./media/apache-hadoop-deep-dive-advanced-analytics/ml-images.png)

Podrobné informace o datové sady, naleznete v tématu Alex Krizhevsky [Learning funkce více vrstev z velmi malé obrázky](https://www.cs.toronto.edu/~kriz/learning-features-2009-TR.pdf).

Datová sada se rozdělené do trénovací sady 50 000 obrázků a testovací sady 10 000 obrázků. První sada byla použita k trénování konvoluční dvacet. vrstvy hloubkové plyne ze zbytkových sítě (modelem ResNet) model s použitím Microsoft Cognitive Toolkit pomocí následujících [v tomto kurzu](https://github.com/Microsoft/CNTK/tree/master/Examples/Image/Classification/ResNet) z úložiště Cognitive Toolkit GitHub. Zbývající 10 000 bitové kopie byly použity pro účely testování přesnosti modelu. To přichází distribuovaného zpracování dat do hry: Úloha předběžného zpracování a vyhodnocování Image je vysoce paralelizovat. Uložené trénovaného modelu v dolním jsme použili:

* PySpark pro distribuci bitové kopie a trénovaného modelu k pracovním uzlům clusteru.
* Python předběžné zpracování imagí na každém uzlu clusteru HDInsight Spark.
* Sada cognitive Toolkit se načíst model a score předběžného zpracování imagí na každém uzlu.
* Poznámkové bloky Jupyter pro spuštění skriptu PySpark agregovat výsledky a použít [Matplotlib](https://matplotlib.org/) můžete vizualizovat výkon modelů.

Na clusteru se 4 uzly pracovního procesu celý předběžného zpracování nebo vyhodnocení 10 000 obrázků trvá méně než jedna minuta. Tento model předpovídá přesně popisky ~ 9,100 imagí (91 %). Chybová matice ukazuje nejběžnější chyby klasifikace. Matice příkladu mislabeling PSI jako koček a naopak vyvolá se, že více často než pro ostatní kombinace popisek.

![Výsledky](./media/apache-hadoop-deep-dive-advanced-analytics/ml-results.png)

### <a name="try-it-out"></a>Vyzkoušejte si to!

Postupujte podle [v tomto kurzu](../spark/apache-spark-microsoft-cognitive-toolkit.md) k implementaci tohoto řešení začátku do konce: nastavit cluster HDInsight Spark, nainstalujte sadu Cognitive Toolkit a spusťte Poznámkový blok Jupyter, která stanoví skóre 10 000 CIFAR imagí.

## <a name="next-steps"></a>Další postup

Hive nebo Azure Machine Learning

* [Hive a Azure Machine Learning začátku do konce](../../machine-learning/team-data-science-process/hive-walkthrough.md)
* [Pomocí clusteru Azure HDInsight Hadoop na 1 TB datové sady](../../machine-learning/team-data-science-process/hive-criteo-walkthrough.md)

Spark a MLLib

* [Machine learning se Spark v HDInsight](../../machine-learning/team-data-science-process/spark-overview.md)
* [Spark s Machine Learning: Používejte Spark v HDInsight pro analýzu teploty v budově pomocí dat HVAC](../spark/apache-spark-ipython-notebook-machine-learning.md)
* [Spark s Machine Learning: Používejte Spark v HDInsight k předpovědím výsledků kontrol potravin](../spark/apache-spark-machine-learning-mllib-ipython.md)

Obsáhlý Learning, Cognitive Toolkit a dalších

* [Klasifikace obrázků jednoduše paralelně zpracovatelné, pomocí Cognitive Toolkit a TensorFlow v Azure HDInsight Spark](https://blogs.technet.microsoft.com/machinelearning/2017/04/12/embarrassingly-parallel-image-classification-using-cognitive-toolkit-tensorflow-on-azure-hdinsight-spark/)
* [Vědecké zpracování dat virtuálních počítačů Azure](../../machine-learning/data-science-virtual-machine/overview.md)
* [Představujeme H2O.ai v Azure HDInsight](https://azure.microsoft.com/blog/introducing-h2o-ai-with-on-azure-hdinsight-to-bring-the-most-robust-ai-platform-for-enterprises/)
