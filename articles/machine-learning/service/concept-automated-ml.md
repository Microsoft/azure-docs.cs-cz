---
title: Co je automatické ML / automl
titleSuffix: Azure Machine Learning service
description: Zjistěte, jak služba Azure Machine Learning můžete automaticky vyberte algoritmus pro vás a generovat model z něj šetřit čas pomocí parametrů a kritéria, které poskytnete vybrat nejlepší algoritmus pro model.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
author: nacharya1
ms.author: nilesha
ms.date: 06/20/2019
ms.custom: seodec18
ms.openlocfilehash: b9fe8ff710cbfe7fbb4a4d8bd351028bb50efcb0
ms.sourcegitcommit: 08138eab740c12bf68c787062b101a4333292075
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/22/2019
ms.locfileid: "67331737"
---
# <a name="what-is-automated-machine-learning"></a>Co je automatické machine learning?

Automatizované strojového učení, také označuje jako autoML, je proces automatizovat časově náročné, iterativní úkoly strojového učení vývoje modelů. Umožňuje datovým vědcům, analytiky a vývojáři k sestavení modelů ML s vysokou škálovatelností, efektivity a produktivity vše při udržování kvality modelu.

Tradiční strojového učení vývoje modelů je náročné vyžadující významné znalosti a čas vytvoření a porovnat desítky modely. Automatizované ML použijte, pokud chcete Azure Machine Learning k trénování a vyladění modelu pomocí cílové metriku, kterou zadáte. Služba poté iteruje skrz algoritmů ML párována s výběry funkcí, kde každé iteraci vytváří model se skóre školení. Čím vyšší skóre, tím lepší považuje model za "podle" vaše data.

Automatizované machine Learning, budete zrychlení čas potřebný k získání skvělé snadnou a efektivní modelů ML připravené pro produkční prostředí.

## <a name="when-to-use-automated-ml"></a>Kdy použít automatizované ML

Automatizované ML demokratizuje strojového učení proces vývoje modelu a umožňuje jejich uživatele, bez ohledu na to své znalosti vědy data k identifikaci kanálu začátku do konce strojového učení pro jakýkoli problém s.

Odborníci přes data, analytiky a vývojáři napříč odvětvími můžete použít automatické ML do:

+ Implementovat řešení strojového učení bez rozsáhlé znalosti programování
+ Šetřete čas a prostředky
+ Využít osvědčené postupy pro datové vědy
+ Poskytují agilní řešení problémů

## <a name="how-automated-ml-works"></a>Funguje jak automatizované ML

Pomocí **služby Azure Machine Learning**, můžete navrhnout a spustit vaše automatizované školení experimenty ML tímto postupem:

1. **Zjistěte, co problém ML** mají být vyřešeny: klasifikace, Prognózování nebo regrese

1. **Zadat zdroj a formát s popiskem trénovacích dat**: Pole Numpy nebo Pandas dataframe

1. **Konfigurace cílové výpočetní prostředí k tréninku modelu**, jako například vaše [místního počítače, Azure Machine Learning vypočítá, vzdálených virtuálních počítačích nebo Azure Databricks](how-to-set-up-training-targets.md).  Další informace o automatické školení [vzdáleného prostředku](how-to-auto-train-remote.md).

1. **Konfigurace automatizovaného strojového učení parametry** , které definují, kolik iterací prostřednictvím různých modelů, nastavení hyperparameter pokročilé předběžného zpracování a snadné a které metriky se podívat na při určování nejlepší model.  Můžete nakonfigurovat nastavení pro automatické výukového experimentu [na webu Azure portal](how-to-create-portal-experiments.md) nebo [pomocí sady SDK](how-to-configure-auto-train.md).

1. **Odešlete školení spustit.**

  ![Automatizované Machine learning](./media/how-to-automated-ml/automl-concept-diagram2.png)

Během cvičení, vytvoří službu Azure Machine Learning celou řadou v paralelní kanály, které se pokoušejí různé algoritmy a parametry. Zastaví se po volání ukončit kritéria definovaná v experimentu.

Můžete také zkontrolovat informace protokolu spuštění, který [obsahuje metriky](how-to-understand-accuracy-metrics.md) shromážděné za běhu. Školení vznikly Python serializovat objekt (`.pkl` souboru), který obsahuje model a data předběžného zpracování.

Při vytváření modelů je automatické, můžete také [zjistěte, jak důležité nebo užitečné funkce jsou](how-to-configure-auto-train.md#explain) pro generované modely.

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE2Xc9t]

<a name="preprocess"></a>

## <a name="preprocessing"></a>Předzpracování

V každé automatizované experimentu strojového učení je Předzpracovaný vašich dat pomocí výchozí metody a volitelně prostřednictvím pokročilého předběžného zpracování.

### <a name="automatic-preprocessing-standard"></a>Automatické předběžného zpracování (standard)

V každé automatizované experimentu strojového učení vaše data automaticky škálovat nebo normalizovány na Nápověda algoritmy dobře fungují.  Při tréninku modelu, jeden z následujících postupů škálování nebo normalizace se použijí pro každý model.

|Škálování&nbsp;&&nbsp;normalizace| Popis |
| ------------- | ------------- |
| [StandardScaleWrapper](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.StandardScaler.html)  | Standardizovat funkce odebráním střední a škáluje se na odchylky jednotky  |
| [MinMaxScalar](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.MinMaxScaler.html)  | Funkce transformuje Škálováním každé funkce tak, že minimum a maximum tento sloupec  |
| [MaxAbsScaler](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.MaxAbsScaler.html#sklearn.preprocessing.MaxAbsScaler) |Můžete škálovat jednotlivé funkce maximální absolutní hodnota |
| [RobustScalar](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.RobustScaler.html) |Tato funkce Scaler podle jejich rozsah quantile |
| [PCA](https://scikit-learn.org/stable/modules/generated/sklearn.decomposition.PCA.html) |Lineární snížení projekt k nižší rozměrného prostoru pomocí singulární rozložené hodnota dat. |
| [TruncatedSVDWrapper](https://scikit-learn.org/stable/modules/generated/sklearn.decomposition.TruncatedSVD.html) |Tato transformace provádí lineární snížení prostřednictvím zkrácená hodnota singulární rozložené (SVD). Rozporu PCA tento odhad není datacentra data před computingu rozložené singulární hodnotu. To znamená, že ho můžete efektivně spolupracovat s scipy.sparse matice |
| [SparseNormalizer](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.Normalizer.html) | Každá ukázka (to znamená, že každý řádek v matici dat) se alespoň jedna součást nenulové je znovu škálovaný nezávisle na ostatních vzorků tak, aby jeho norm (l1 nebo l2) má jednu |

### <a name="advanced-preprocessing-optional-featurization"></a>Pokročilé předběžného zpracování: volitelné snadné

K dispozici, například chybějící hodnoty imputace, kódování a transformace jsou taky další pokročilé předběžného zpracování a snadné. [Další informace o tom, jaké snadné je součástí](how-to-create-portal-experiments.md#preprocess). Povolením tohoto nastavení se:

+ Azure portal: Výběr **předzpracování** zaškrtávací políčko ve **upřesňující nastavení** [tímto postupem](how-to-create-portal-experiments.md).

+ Python SDK: Určení `"preprocess": True` pro [ `AutoMLConfig` třídy](https://docs.microsoft.com/python/api/azureml-train-automl/azureml.train.automl.automlconfig?view=azure-ml-py).


## <a name="time-series-forecasting"></a>Předvídání časových řad
Vytváření prognóz je nedílnou součástí jakoukoli firmu, ať už jde výnosy, inventáře, sales nebo zákazníků. Automatizované ML můžete kombinovat technik a postupů a získejte doporučené, vysoce kvalitní časovou řadu prognózy. 

Automatizované experiment časovou řadu je považován za problém regrese s množstvím proměnných. Poslední časové řady hodnoty jsou "neseskupené" se další dimenze pro regresor společně s další prediktory. Tento přístup, na rozdíl od klasického čas řady metod, má výhodu přirozeně začlenění více kontextových proměnných a jejich vzájemných vztazích během cvičení. Automatizované ML zjistí model jednu, ale často interně větvenou pro všechny položky v datové sady a predikcí možností. Další data jsou tedy k dispozici k odhadu parametry modelu a je možné generalizace nezobrazený řady. 

Další informace a příklad [automatizované strojového učení pro prognózy časových řad](how-to-auto-train-forecast.md).

## <a name="ensemble-models"></a>Modely skupiny stromů

Trénovat modely komplet pomocí automatizovaných machine learning s [algoritmus výběru skupiny stromů Caruana s inicializací seřazený komplet](http://www.niculescu-mizil.org/papers/shotgun.icml04.revised.rev2.pdf). Komplet learning zlepšuje výsledky machine learning a prediktivní výkon kombinace mnoha modelů na rozdíl od použití jedné modelů. Komplet iterace se zobrazí jako poslední iteraci spuštění.

## <a name="use-with-onnx-in-c-apps"></a>Použití s ONNX v C# aplikace

Pomocí služby Azure Machine Learning slouží k sestavení modelu Python a jeho převodu do formátu ONNX automatizované ML. Podporuje modul runtime ONNX C#, abyste mohli použít model sestavený automaticky ve vaší C# aplikace bez nutnosti pro nahrávání nebo některý z síťovou latenci, které zavádějí koncové body REST. Zkuste příkladem tento tok [v tento poznámkový blok Jupyter](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-with-onnx/auto-ml-classification-with-onnx.ipynb).

## <a name="automated-ml-across-microsoft"></a>Automatizované ML rámci společnosti Microsoft

Automatizované ML je také dostupná v dalších řešení Microsoftu, jako:

|Integrace|Popis|
|------------|-----------|
|[ML.NET](https://docs.microsoft.com/dotnet/machine-learning/automl-overview)|Výběr automatického modelu a školení v aplikacích .NET pomocí sady Visual Studio a Visual Studio Code s ML.NET automatizované ML (Preview).|
|[HDInsight](../../hdinsight/spark/apache-spark-run-machine-learning-automl.md)|Horizontální navýšení kapacity vašeho automatizované úlohy trénování ML ve Sparku v HDInsight clustery paralelně.|
|[PowerBI](https://docs.microsoft.com/power-bi/service-machine-learning-automated)|Vyvolání modelů strojového učení přímo v Power BI (Preview).|
|[SQL Server](https://cloudblogs.microsoft.com/sqlserver/2019/01/09/how-to-automate-machine-learning-on-sql-server-2019-big-data-clusters/)|Vytvořte nové modely strojového učení nad vašimi daty v clusterech velké objemy dat SQL serveru 2019.|

## <a name="next-steps"></a>Další postup

Podívejte se na příklady a další informace o vytváření modelů pomocí automatizovaných strojového učení:

+ Postupujte podle [kurzu: Automaticky vyškolíme model klasifikace pomocí automatizované Azure Machine Learning](tutorial-auto-train-models.md)

+ Nakonfigurujte nastavení pro automatické výukového experimentu:
  + V rozhraní Azure portal [postupujte podle tohoto návodu](how-to-create-portal-experiments.md).
  + Pomocí sady Python SDK [postupujte podle tohoto návodu](how-to-configure-auto-train.md).

+ Zjistěte, jak automaticky pomocí dat časových řad, trénování [postupujte podle tohoto návodu](how-to-auto-train-forecast.md).

+ Vyzkoušejte si [Poznámkový blok Jupyter ukázky pro automatizované machine learning](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/)
