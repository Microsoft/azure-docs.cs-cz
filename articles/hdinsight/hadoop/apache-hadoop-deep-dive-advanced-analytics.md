---
title: Podrobné informace - pokročilou analýzu - Azure HDInsight | Microsoft Docs
description: Zjistěte, jak pokročilou analýzu ke zpracování velkých objemů dat používá algoritmy.
services: hdinsight
documentationcenter: ''
author: ashishthaps
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.date: 11/14/2017
ms.author: ashishth
ms.openlocfilehash: f121a1f95eed04bf8086c03a5d4406c092dc9445
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
---
# <a name="deep-dive---advanced-analytics"></a>Podrobné informace - pokročilou analýzu

## <a name="what-is-advanced-analytics-for-hdinsight"></a>Co je pokročilou analýzu pro HDInsight?

HDInsight poskytuje schopnost získat cenné přehledy z velké objemy strukturovaných a nestrukturovaných a přesunutí fast data. Pokročilou analýzu je použití vysoce škálovatelné architektury, statistické a modely machine learning a inteligentní řídicí panely, kde přinášejí smysluplné přehledy. Strojové učení, nebo *prediktivní analýzy*, používá algoritmy, které identifikují a dozvědět se od relace ve vašich datech a provádět předpovědi Průvodce vaše rozhodnutí.

## <a name="advanced-analytics-process"></a>Proces pokročilou analýzu

![Proces](./media/apache-hadoop-deep-dive-advanced-analytics/process.png)

Poté, co jste zformulovali obchodního problému a spustili shromažďování a zpracování dat, musíte vytvořit model, který představuje otázka chcete předpovědět. Váš model používat jeden nebo více algoritmy strojového učení k provádění typ předpovědi, které bude nejlépe vyhovovat vašim obchodním potřebám.  Většina vašich dat se má použít k natrénování modelu, se zbytkem používá k testování nebo hodnocení. 

Po vytvoření, načíst, testování a vyhodnocení modelu, dalším krokem je model nasadit tak, aby začne poskytuje odpovědi na otázky. Posledním krokem je váš model výkonu monitorovat a vyladit podle potřeby. 

## <a name="common-types-of-algorithms"></a>Běžné typy algoritmů

Řešení pro pokročilou analýzu poskytuje sadu algoritmy strojového učení. Zde je souhrn kategorie algoritmy a přidružené běžné případy obchodního použití.

![Případy použití Machine Learning](./media/apache-hadoop-deep-dive-advanced-analytics/ml-use-cases.png)

Společně s výběrem nejlepší těsně algoritmy, budete muset zvážit, jestli je potřeba poskytující data pro školení. Algoritmy strojového učení jsou rozdělené takto:

* Pod dohledem - algoritmus musí zaškolení na sadu dat s popiskem předtím, než může poskytnout výsledky
* Polovičním pod dohledem - algoritmus může být rozšířen o další cíle prostřednictvím interaktivní dotazu trainer, které nebyly dostupné během počáteční fáze školení
* Bez dozoru – algoritmus nevyžaduje cvičení dat 
* Posílení - algoritmus používá agentů softwaru k určení chování ideální v rámci konkrétní kontextu (často používaný v robotics)


| Algoritmus kategorie| Použití | Learning typu | Algoritmy |
| --- | --- | --- | -- |
| Klasifikace | Klasifikovat osoby nebo věcí do skupin | Pod dohledem. | Rozhodovací stromy, Logistic regression, neuronové sítě |
| Clustering | Dělení sadu příklady do homogenního skupin | Bez dohledu | K-means clustering |
| Vzor detekce | Identifikovat časté přidružení v datech | Bez dohledu | Přidružení pravidla |
| Regrese | Předpověď číselné výstupy | Pod dohledem. | Lineární regrese, neuronové sítě |
| Posílení | Určení optimální chování pro roboty | Posílení | Simulace typu Monte Carlo, DeepMind |

## <a name="machine-learning-on-hdinsight"></a>Strojového učení v HDInsight

HDInsight má několik strojového učení možnosti pracovním postupu pokročilou analýzu:

* [Machine Learning a Spark](#machine-learning-and-spark)
* [R a R Server](#r-and-r-server)
* [Azure Machine Learning a Hive](#azure-machine-learning-and-hive)
* [Spark a hloubkové učení](#spark-and-deep-learning)

### <a name="machine-learning-and-spark"></a>Machine Learning a Spark

[HDInsight Spark](../spark/apache-spark-overview.md) je nabídky Azure hostovaná z [Spark](http://spark.apache.org/), a jednotnou open source, rozhraní paralelní zpracování dat, které používá zpracování v paměti pro zvýšení analýzy velkých objemů dat. Modul zpracování Spark je vytvořené pro rychlost, snadné použití a sofistikované analytics. Možnosti v paměti distribuované výpočtů Spark díky správnou volbu pro iterativní algoritmy použité v machine learning a grafů výpočty. 

Existují tři knihovny learning škálovatelné počítače, které uvede algoritmické modelování možnosti Tento distribuovaném prostředí:

* [**MLlib** ](https://spark.apache.org/docs/latest/ml-guide.html) -MLlib obsahuje původní API postavená na Spark RDDs.
* [**SparkML** ](https://spark.apache.org/docs/1.2.2/ml-guide.html) -SparkML je novější balíček, který poskytuje vyšší úrovně rozhraní API pro vytváření kanálů ML postavená na Spark DataFrames.
* [**MMLSpark** ](https://github.com/Azure/mmlspark) – Microsoft Machine Learning knihovny pro Apache Spark (MMLSpark) je navržený tak, aby datových vědců zvýšit produktivitu na Spark, pokud chcete zvýšit počet experimentování a využívá nejmodernější machine learning techniky, včetně hloubkové learning na velmi rozsáhlých datových sad. Knihovna MMLSpark zjednodušuje běžné úlohy modelování pro vytváření modelů v PySpark. 

### <a name="r-and-r-server"></a>R a R Server

Jako součást HDInsight, můžete vytvořit cluster služby HDInsight s [R Server](../r-server/r-server-overview.md) připravené pro použití s velkých datových sad a modely. Tato nová funkce nabízí datových vědců a statistikami s známé rozhraní R, který můžete škálovat na vyžádání prostřednictvím HDInsight, bez nutnosti instalace a údržby.

### <a name="azure-machine-learning-and-hive"></a>Azure Machine Learning a Hive

[Azure Machine Learning Studio](https://studio.azureml.net/) poskytuje nástroje ke model prediktivní analýzy, jakož i plně spravovaná služba, můžete použít k nasazení své prediktivní modely jako připravené využívají webové služby. Azure Machine Learning poskytuje nástroje pro vytváření kompletních řešení Prediktivní analýza v cloudu k rychlému vytváření, testování, zprovoznění a správa prediktivní modelů. Vyberte z velké knihovny algoritmů, použijte přes webové studio pro vytváření modelů a snadno nasadit model jako webovou službu.

### <a name="spark-and-deep-learning"></a>Spark a hloubkové učení

[Hloubkové learning](https://www.microsoft.com/research/group/dltc/) je větev nástroje machine learning, který používá *hluboké neuronové sítě* (DNNs), INSPIROVANÉ biologické procesy lidského mozku. Mnoho výzkumných pracovníků najdete hloubkové learning jako Slíbení přístup pro umělé inteligence. Některé příklady hloubkové learning jsou překladatelé mluvené jazyk, systémy rozpoznávání bitové kopie a reasoning počítače. Chcete-li zálohy vlastní pracovní hloubkové dozvědět, společnost Microsoft vyvinula open-source zdarma, který se snadno použitelný, [kognitivní nástrojů Microsoft](https://www.microsoft.com/cognitive-toolkit/). Sada nástrojů se hojně používá pro celou řadu produktů společnosti Microsoft, společnostmi po celém světě s potřeba nasadit přímý learning ve velkém měřítku a studenty zájem o nejnovější algoritmy a postupy. 

## <a name="scenario---score-images-to-identify-patterns-in-urban-development"></a>Scénář – skóre bitové kopie k identifikaci vzorů v urbanistické vývoj

Pojďme si příklad pokročilou analýzu strojového učení kanálu pomocí HDInsight.

V tomto scénáři uvidíte, jak DNNs vytvořil v hloubkové learning rozhraní Microsoft kognitivní Toolkit (CNTK), může být operationalized pro vyhodnocování kolekce velký obrázek, které jsou uložené v účtu Azure Blob Storage pomocí PySpark na clusteru HDInsight Spark. Tento přístup se použije pro běžné DNN případu použití, klasifikace leteckou bitové kopie a slouží k identifikaci poslední vzorů v urbanistické vývoj.  Budete používat model klasifikace předem vyškolení bitové kopie. Model je předem vyškolení na [datovou sadu CIFAR 10](https://www.cs.toronto.edu/~kriz/cifar.html) a byla použita v 10 000 odmítnuté bitové kopie.

V tomto scénáři pokročilou analýzu existují tři klíčové úlohy:

1. Vytvoření clusteru Azure HDInsight Hadoop Apache Spark 2.1.0 rozdělení. 
2. Spuštění vlastního skriptu k instalaci kognitivní nástrojů Microsoft na všech uzlech clusteru Azure HDInsight Spark. 
3. Nahrajte předdefinovaných poznámkového bloku Jupyter ke svému clusteru HDInsight Spark k použití vyškolení Microsoft kognitivní Toolkit hloubkové učení modelu do souborů v účtu úložiště objektů Blob Azure pomocí rozhraní API Python Spark (PySpark). 

Tento příklad používá sada kompilované a distribuované Alex Krizhevsky, Vinod Nair a Geoffrey Hinton obrázků CIFAR-10. Datová sada CIFAR 10 obsahuje 60 000 32 × 32 barvu bitové kopie, které patří do 10 vzájemně se vylučuje třídy:

![Image](./media/apache-hadoop-deep-dive-advanced-analytics/ml-images.png)

Další informace o datovou sadu, najdete v části Alex Krizhevsky [Learning funkce více vrstev z malá velikost bitové kopie](https://www.cs.toronto.edu/~kriz/learning-features-2009-TR.pdf).

Datová sada byl rozdělen na oddíly do trénovací sady 50 000 bitových kopií a testovací sadu imagí, 10 000. První sady byl použit k natrénování modelu dvacet. vrstvy přímým convolutional zbytkové sítě (ResNet) pomocí sady nástrojů pro Microsoft kognitivní podle [v tomto kurzu](https://github.com/Microsoft/CNTK/tree/master/Examples/Image/Classification/ResNet) z úložiště Githubu kognitivní Toolkit. Zbývající 10 000 Image se používají pro testování přesnosti modelu. To přichází distribuovanému zpracování dat do play: Úloha předběžné zpracování a vyhodnocování bitové kopie je vysoce může běžet paralelně. S uložené trained model v dolním jsme použili:

* PySpark k distribuci bitové kopie a trained model k pracovním uzlům clusteru.
* Python předběžné zpracování obrázků na každém uzlu clusteru HDInsight Spark.
* Kognitivní Toolkit načíst model a score předem zpracovaných bitové kopie na každém uzlu.
* Poznámkové bloky Jupyter ke spuštění skriptu PySpark agregovat výsledky a použít [Matplotlib](https://matplotlib.org/) můžete vizualizovat výkon modelu.

Na clusteru se 4 uzly pracovního procesu celý předzpracování/vyhodnocování obrázků na 10 000 trvá méně než jedna minuta. Model přesně předpovídá popisky ~ 9,100 obrázků (91 %). Matice nedorozuměním znázorňuje nejběžnějších chyb, klasifikace. Například matice ukazuje, že mislabeling PSI jako kočky a naopak k více často než pro ostatní dvojice popisek.

![Výsledky](./media/apache-hadoop-deep-dive-advanced-analytics/ml-results.png)

### <a name="try-it-out"></a>Vyzkoušejte si to!

Postupujte podle [v tomto kurzu](../spark/apache-spark-microsoft-cognitive-toolkit.md) k implementaci tohoto řešení pro kompletní: nastavení clusteru HDInsight Spark, nainstalujte kognitivní Toolkit a spusťte poznámkového bloku Jupyter, která skóre 10 000 CIFAR bitové kopie.

## <a name="next-steps"></a>Další postup

Hive a Azure Machine Learning

* [Hive a Azure Machine Learning klient server](../../machine-learning/team-data-science-process/hive-walkthrough.md)
* [Použití clusteru Azure HDInsight Hadoop na 1 TB datové sady](../../machine-learning/team-data-science-process/hive-criteo-walkthrough.md)

Spark a MLLib

* [Strojového učení s Spark v HDInsight](../../machine-learning/team-data-science-process/spark-overview.md)
* [Spark s Machine Learning: Používejte Spark v HDInsight pro analýzu teploty v budově pomocí dat HVAC](../spark/apache-spark-ipython-notebook-machine-learning.md)
* [Spark s Machine Learning: Používejte Spark v HDInsight k předpovědím výsledků kontrol potravin](../spark/apache-spark-machine-learning-mllib-ipython.md)

Hloubkové Learning, kognitivní nástrojů a dalších

* [Jednoduše paralelně zpracovatelné image klasifikaci, použití kognitivní Toolkit a TensorFlow v Azure HDInsight Spark](https://blogs.technet.microsoft.com/machinelearning/2017/04/12/embarrassingly-parallel-image-classification-using-cognitive-toolkit-tensorflow-on-azure-hdinsight-spark/)
* [Vědecké zpracování dat virtuálního počítače Azure](../../machine-learning/data-science-virtual-machine/overview.md)
* [Představení H2O.ai v Azure HDInsight](https://azure.microsoft.com/blog/introducing-h2o-ai-with-on-azure-hdinsight-to-bring-the-most-robust-ai-platform-for-enterprises/)
