---
title: Rozsáhlá podrobněa – Pokročilá analýza – Azure HDInsight
description: Přečtěte si, jak Pokročilá analýza používá algoritmy ke zpracování velkých objemů dat ve službě Azure HDInsight.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 01/01/2020
ms.openlocfilehash: 4b57eddafbf9a5615dc42e9a3c5a49c5f90781e2
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/28/2021
ms.locfileid: "98946669"
---
# <a name="deep-dive---advanced-analytics"></a>Rozsáhlá podrobněa – Pokročilá analýza

## <a name="what-is-advanced-analytics-for-hdinsight"></a>Co je Pokročilá analýza pro HDInsight?

HDInsight nabízí možnost získat hodnotný přehled z velkých objemů strukturovaných, nestrukturovaných a rychlých přesunů dat. Pokročilá analýza využívá vysoce škálovatelné architektury, statistické modely a modely strojového učení a inteligentní řídicí panely, které poskytují smysluplné přehledy. Strojové učení nebo *prediktivní analýza* využívá algoritmy, které identifikují a zjišťují relace ve vašich datech, aby předpovědi a zajistila vaše rozhodnutí.

## <a name="advanced-analytics-process"></a>Proces pokročilé analýzy

![Tok procesu pokročilé analýzy](./media/apache-hadoop-deep-dive-advanced-analytics/hdinsight-analytic-process.png)

Po zjištění obchodního problému a zahájení shromažďování a zpracování dat je potřeba vytvořit model, který představuje otázku, kterou chcete předpovědět. Model bude používat jeden nebo více algoritmů strojového učení, aby vytvářely typ předpovědi, který nejlépe vyhovuje vašim obchodním potřebám.  Většina vašich dat by měla být použita ke školení vašeho modelu se zbytkem, který se používá k otestování nebo vyhodnocení.

Až vytvoříte, nasadíte, otestujete a vyhodnocujete svůj model, dalším krokem je nasazení modelu tak, aby bylo zahájeno zadávání odpovědí na vaše otázky. Posledním krokem je monitorovat výkon svého modelu a ladit ho podle potřeby.

## <a name="common-types-of-algorithms"></a>Běžné typy algoritmů

Pokročilá Analytická řešení poskytují sadu algoritmů strojového učení. Tady je souhrn kategorií algoritmů a přidružených běžných případů použití firmy.

![Machine Learning souhrny kategorií](./media/apache-hadoop-deep-dive-advanced-analytics/machine-learning-use-cases.png)

Společně s výběrem nejlépe přidaných algoritmů je potřeba zvážit, jestli potřebujete zadat data pro školení. Algoritmy strojového učení jsou rozdělené takto:

* Pod dohledem – algoritmus musí být vyškolený pro sadu dat s popiskem, než může poskytnout výsledky.
* Částečně pod dohledem – algoritmus lze rozšířit o další cíle prostřednictvím interaktivního dotazu Trainer, který nebyl během úvodní fáze školení k dispozici.
* Bez dohledu – algoritmus nevyžaduje školicí data.
* Výztužný algoritmus používá softwarové agenty k určení ideálního chování v rámci konkrétního kontextu (často se používá v robotech).

| Kategorie algoritmu| Použití | Typ učení | Algoritmy |
| --- | --- | --- | -- |
| Klasifikace | Klasifikovat lidi nebo věci do skupin | Pod dohledem | Rozhodovací stromy, logistické regrese, sítě neuronové |
| Clustering | Dělení sady příkladů do homogenních skupin | Bez dohledu | K-znamená clustering |
| Detekce vzoru | Identifikace častých přidružení v datech | Bez dohledu | Association rules |
| Regrese | Předpověď číselných výsledků | Pod dohledem | Lineární regrese, neuronové sítě |
| Zpětnovazební | Určení optimálního chování pro roboty | Zpětnovazební | Simulace Monte Carlo, DeepMind |

## <a name="machine-learning-on-hdinsight"></a>Machine Learning v HDInsight

HDInsight má několik možností strojového učení pro pracovní postup pokročilé analýzy:

* Machine Learning a Apache Spark
* Služby R a ML
* Azure Machine Learning a Apache Hive
* Apache Spark a obsáhlý Learning

### <a name="machine-learning-and-apache-spark"></a>Machine Learning a Apache Spark

[HDInsight Spark](../spark/apache-spark-overview.md) je nabídka Azure hostovaná s využitím [Apache Spark](https://spark.apache.org/)sjednoceného Open Source architektury paralelního zpracování dat, která využívá zpracování v paměti ke zvýšení analýz velkých objemů dat. Modul pro zpracování Spark je založený na rychlosti, snadném použití a propracované analýze. Funkce distribuovaného výpočtu v paměti Sparku nabízí dobrou volbu pro iterativní algoritmy používané v strojovém učení a výpočtech grafů.

Existují tři škálovatelné knihovny strojového učení, které přinášejí možnosti algoritmu modelování do tohoto distribuovaného prostředí:

* [**MLlib**](https://spark.apache.org/docs/latest/ml-guide.html) -MLlib obsahuje původní rozhraní API postavené na Spark RDD.
* [**SparkML**](https://spark.apache.org/docs/1.2.2/ml-guide.html) -SparkML je novější balíček, který poskytuje rozhraní API na vyšší úrovni sestavené nad datovými snímky Spark pro vytváření kanálů ml.
* [**MMLSpark**](https://github.com/Azure/mmlspark)  – knihovna Microsoft Machine Learning library for Apache Spark (MMLSpark) je navržená tak, aby se zvýšila produktivita vědeckých dat ve Sparku, zvýšila se rychlost experimentu a využila se špičkové techniky strojového učení, včetně hloubkového učení, u velmi rozsáhlých datových sad. Knihovna MMLSpark zjednodušuje běžné úlohy modelování pro vytváření modelů v PySpark.

### <a name="r-and-ml-services"></a>Služby R a ML

V rámci služby HDInsight můžete vytvořit cluster HDInsight se [službami ml](../r-server/r-server-overview.md) , které jsou připravené k použití s obrovskými datovými sadami a modely. Tato nová funkce zajišťuje odborníky na data a statistiku se známým rozhraním jazyka R, které dokáže škálovat na vyžádání prostřednictvím služby HDInsight bez režie instalace a údržby clusteru.

### <a name="azure-machine-learning-and-apache-hive"></a>Azure Machine Learning a Apache Hive

[Azure Machine Learning Studio (Classic)](https://studio.azureml.net/) poskytují nástroje pro modelování prediktivních analýz a plně spravovanou službu, kterou můžete použít k nasazení prediktivních modelů jako připravených webových služeb. Azure Machine Learning poskytuje nástroje pro vytváření kompletních řešení prediktivní analýzy v cloudu, aby bylo možné rychle vytvářet, testovat, zprovoznění a spravovat prediktivní modely. Vyberte si z velké knihovny algoritmů, použijte webové Studio pro vytváření modelů a jednoduše nasaďte model jako webovou službu.

### <a name="apache-spark-and-deep-learning"></a>Apache Spark a obsáhlý Learning

[Obsáhlý Learning](https://www.microsoft.com/research/group/dltc/) je větev strojového učení, která využívá *rozsáhlé neuronové sítě* (hluboké), nechte inspirovat biologických procesů lidského mozku. Mnoho výzkumných pracovníků zobrazuje obsáhlý Learning jako slibový přístup pro umělou analýzu. Některé příklady obsáhlého učení jsou mluvené překlady jazyka, systémy pro rozpoznávání obrázků a důvody pro počítače. Společnost Microsoft vyvinula bezplatný a snadno použitelný [Microsoft Cognitive Toolkit](https://www.microsoft.com/en-us/cognitive-toolkit/)open source, aby mohla lépe využít svou vlastní práci v obsáhlém učení. Sada nástrojů se často používá širokou škálou produktů Microsoftu, a to společnostmi po celém světě s potřebou nasazení hloubkového učení ve velkém měřítku a studentům, kteří mají zájem o nejnovější algoritmy a techniky.

## <a name="scenario---score-images-to-identify-patterns-in-urban-development"></a>Scénář – obrázky skóre pro identifikaci vzorů v městském vývoji

Pojďme se podívat na příklad kanálu strojového učení s pokročilým analýzou pomocí HDInsight.

V tomto scénáři se dozvíte, jak hluboké vytvářely v rámci systému hloubkového učení, a Microsoft Cognitive Toolkit (CNTK) je možné provozovat pro vyhodnocování rozsáhlých kolekcí imagí uložených v účtu Azure Blob Storage pomocí PySpark v clusteru HDInsight Spark. Tento přístup se použije na společný DNN případ použití, klasifikaci leteckého obrazu a dá se použít k identifikaci nedávných vzorů při vývoji měst.  Použijete předem vyškolený model klasifikace imagí. Model je předem vyškolený pro [datovou sadu CIFAR-10](https://www.cs.toronto.edu/~kriz/cifar.html) a byl použit na 10 000 odmítnutých imagí.

Tento scénář pokročilých analýz obsahuje tři klíčové úlohy:

1. Vytvořte cluster Azure HDInsight Hadoop s distribucí Apache Spark 2.1.0.
2. Spusťte vlastní skript pro instalaci Microsoft Cognitive Toolkit na všech uzlech Azure HDInsight Spark clusteru.
3. Nahrajte předem sestavený Jupyter Notebook do clusteru HDInsight Spark, abyste mohli použít školicí model Microsoft Cognitive Toolkit s hloubkovým učením na soubory v účtu Azure Blob Storage pomocí rozhraní Spark Python API (PySpark).

V tomto příkladu se používá sada imagí CIFAR-10 zkompilovaná a distribuovaná Alex Krizhevsky, Vinoed Nair a Geoffrey Hinton. Datová sada CIFAR-10 obsahuje 60 000 32 × 32 barev obrázků, které patří 10 vzájemně se vylučující třídy:

![Příklady imagí Machine Learning](./media/apache-hadoop-deep-dive-advanced-analytics/machine-learning-images.png)

Další informace o datové sadě najdete v tématu učení Alex Krizhevsky [s více vrstvami funkcí z drobných imagí](https://www.cs.toronto.edu/~kriz/learning-features-2009-TR.pdf).

Datová sada byla rozdělená na školicí sadu 50 000 imagí a sadu testů 10 000 imagí. První sada se použila k vytvoření modelu ResNet (konvoluční reziduální sítě s přímým odkazem) pomocí Microsoft Cognitive Toolkit pomocí [tohoto kurzu](https://github.com/Microsoft/CNTK/tree/master/Examples/Image/Classification/ResNet) z úložiště GitHub Cognitive Toolkit. Zbývající image 10 000 byly použity pro testování přesnosti modelu. Sem patří distribuované výpočetní prostředí: úloha předběžného zpracování a bodování imagí je vysoce paralelizovat. Díky uloženému škole modelu jsme použili:

* PySpark pro distribuci imagí a trained model do pracovních uzlů clusteru.
* Python pro předběžné zpracování imagí v každém uzlu clusteru HDInsight Spark.
* Cognitive Toolkit načíst model a určit skóre předzpracovaných imagí na jednotlivých uzlech.
* Jupyter poznámkové bloky pro spuštění skriptu PySpark, agregované výsledky a použití [matplotlib](https://matplotlib.org/) k vizualizaci výkonu modelu.

Celý proces předzpracování nebo bodování imagí 10 000 trvá méně než jednu minutu v clusteru se 4 pracovními uzly. Model přesně předpovídá popisky ~ 9 100 (91%) fotografií. Nejasná matice ilustrují nejběžnější chyby klasifikace. Matice například ukazuje, že nepopisení psi jako kočky a naopak probíhá častěji než u jiných párů popisků.

![Graf výsledků Machine Learning](./media/apache-hadoop-deep-dive-advanced-analytics/machine-learning-results.png)

### <a name="try-it-out"></a>Vyzkoušejte si to!

Pokud chcete toto řešení implementovat do konce, postupujte podle [tohoto kurzu](../spark/apache-spark-microsoft-cognitive-toolkit.md) : nastavte cluster HDInsight Spark, nainstalujte Cognitive Toolkit a spusťte Jupyter notebook, který vyhodnotí 10 000 imagí.

## <a name="next-steps"></a>Další kroky

Apache Hive a Azure Machine Learning

* [Apache Hive a Azure Machine Learning od začátku do konce](../../machine-learning/team-data-science-process/hive-walkthrough.md)
* [Použití clusteru Azure HDInsight Hadoop pro datovou sadu o 1 TB](../../machine-learning/team-data-science-process/hive-criteo-walkthrough.md)

Apache Spark a MLLib

* [Machine Learning s Apache Spark ve službě HDInsight](../../machine-learning/team-data-science-process/spark-overview.md)
* [Apache Spark s Machine Learning: použití Apache Spark ve službě HDInsight k analýze teploty budovy pomocí dat TVK](../spark/apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark s Machine Learning: použití Apache Spark ve službě HDInsight k předpovědi výsledků kontroly potravin](../spark/apache-spark-machine-learning-mllib-ipython.md)

Obsáhlý Learning, Cognitive Toolkit a další

* [Virtuální počítač Azure pro datové vědy](../../machine-learning/data-science-virtual-machine/overview.md)
* [Představujeme H2O.ai do Azure HDInsight](https://azure.microsoft.com/blog/introducing-h2o-ai-with-on-azure-hdinsight-to-bring-the-most-robust-ai-platform-for-enterprises/)
