---
title: Co je Automated ML/AutoML
titleSuffix: Azure Machine Learning
description: Přečtěte si, jak Azure Machine Learning může automaticky vybrat algoritmus a vytvořit z něj model, který vám umožní ušetřit čas pomocí parametrů a kritérií, které zadáte pro výběr nejlepšího algoritmu pro váš model.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
author: cartacioS
ms.author: sacartac
ms.date: 04/22/2020
ms.openlocfilehash: f592a7f5a4af38988bcf433f0adc89d9be7579cb
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "82082005"
---
# <a name="what-is-automated-machine-learning-automl"></a>Co je Automated Machine Learning (AutoML)?

Automatizované Machine Learning, označovaný také jako automatizovaná ML nebo AutoML, je proces automatizace časově náročného zpracování iterativních úloh vývoje modelů strojového učení. Umožňuje datovým vědcům, analytikům a vývojářům vytvářet modely ML s vysokým škálováním, efektivitou a produktivitou, a přitom udržuje kvalitu modelu. Automatizované ML je založené na převratcích z našeho [oddělení Microsoft Research](https://arxiv.org/abs/1705.05355).

Tradiční vývoj modelů ve strojovém učení je náročný na prostředky, což vyžaduje významné znalosti v doméně a dobu potřebnou k vytváření a porovnávání desítk modelů. Díky automatizovanému strojového učení urychlíte dobu potřebnou k získání modelů ML připravené k produkčnímu prostředí s využitím Skvělé a efektivity.

## <a name="when-to-use-automl-classify-regression--forecast"></a>Kdy použít AutoML: klasifikovat, regrese, & prognóza

Automatickou ML použijte, když chcete, Azure Machine Learning vytvořit výuku a vyladit model pro vás pomocí cílové metriky, kterou zadáte. Automatizované ML demokratizuje proces vývoje modelů strojového učení a svým uživatelům umožníte bez ohledu na jejich znalosti v oblasti datových věd, aby identifikovali ucelený kanál strojového učení pro případné potíže.

Odborníci na data, analytiké a vývojáři v různých oborech můžou používat automatizovanou ML:
+ Implementace řešení ML bez rozsáhlých programovacích znalostí
+ Úspora času a prostředků
+ Využijte osvědčené postupy pro datové vědy
+ Zajištění agilních problémů – řešení potíží

### <a name="classification"></a>Classification

Klasifikace je běžný úkol strojového učení. Klasifikace je typ vzdělávání pod dohledem, ve kterém se modely učí pomocí školicích dat, a aplikujte tyto učení na nová data. Azure Machine Learning nabízí featurizations specificky pro tyto úlohy, jako je například hluboký neuronové Network text featurizers for Classification. Další informace o [možnostech featurization](how-to-use-automated-ml-for-ml-models.md#featurization) 

Hlavním cílem modelů klasifikace je předpovědět, ke kterým kategoriím budou nová data zacházet v závislosti na studiu ze svých školicích dat. Mezi běžné klasifikace patří zjišťování podvodů, rozpoznávání rukopisu a detekce objektů.  Přečtěte si další informace a podívejte [se na příklad klasifikace pomocí automatizovaného strojového učení](tutorial-train-models-with-aml.md).

Podívejte se na příklady klasifikace a automatizované strojové učení v těchto poznámkových blocích Pythonu: [zjišťování podvodů](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-credit-card-fraud/auto-ml-classification-credit-card-fraud.ipynb), [předpověď marketingu](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-bank-marketing-all-features/auto-ml-classification-bank-marketing-all-features.ipynb)a [klasifikace dat diskusních skupin](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-text-dnn/auto-ml-classification-text-dnn.ipynb) .

### <a name="regression"></a>Regrese
Podobně jako u klasifikace jsou regresní úlohy také běžnou úlohou v dohledovém učení. Azure Machine Learning nabízí [featurizations specificky pro tyto úlohy](how-to-use-automated-ml-for-ml-models.md#featurization).

Liší se od klasifikace, kde jsou hodnoty předpokládaných výstupů kategorií, regresní modely odhadne číselné výstupní hodnoty na základě nezávislých prediktivních. V regresi je cílem přispět k navázání vztahu mezi těmito nezávislými proměnnými proměnných odhadem toho, jak jedna proměnná má vliv na ostatní. Například cena automobilu na základě funkcí, jako je plynová km, bezpečnostní hodnocení atd. Přečtěte si další informace a podívejte [se na příklad regrese pomocí automatizovaného strojového učení](tutorial-auto-train-models.md).

Podívejte se na příklady regrese a automatizovaného strojového učení pro předpovědi v těchto poznámkových blocích Pythonu: [předpověď výkonu procesoru](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/regression-hardware-performance-explanation-and-featurization/auto-ml-regression-hardware-performance-explanation-and-featurization.ipynb), 

### <a name="time-series-forecasting"></a>Prognózování časových řad

Vytváření prognóz je nedílnou součástí jakékoli firmy, ať už jde o výnosy, inventář, prodej nebo poptávku zákazníků. Pomocí automatizovaného ML můžete kombinovat techniky a přístupy a získat doporučenou a vysoce kvalitní předpověď časových řad. Další informace s tímto postupem: [automatizované Machine Learning pro prognózování časových řad](how-to-auto-train-forecast.md). 

Automatický experiment s časovou řadou se považuje za problém lineární regrese. Hodnoty za časovou řadou jsou "pivoted" a stanou se dalšími dimenzemi pro regresor společně s jinými koproměnnými. Tento přístup, na rozdíl od metod klasických časových řad, má výhodu přirozeně zahrnující více kontextových proměnných a jejich vzájemný vztah během školení. Automatizovaná ML seznámí s jedním, ale často interně rozvětveným modelem pro všechny položky v datové sadě a horizontech předpovědi. K dispozici jsou proto další data k odhadování parametrů modelu a generalizace na nedostupné řady.

Pokročilá konfigurace prognózy zahrnuje:
* detekce svátků a featurization
* DNN (auto-ARIMA, Prophet, ForecastTCN)
* mnoho modelů podporuje seskupení
* křížové ověření pro návratové zdroje
* konfigurovatelné prodlevy
* souhrnné funkce kumulovaných oken


Podívejte se na příklady regrese a automatizovaného strojového učení pro předpovědi v těchto poznámkových blocích Pythonu: [Prognózování prodeje](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-orange-juice-sales/auto-ml-forecasting-orange-juice-sales.ipynb), [Prognózování poptávky](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand/auto-ml-forecasting-energy-demand.ipynb)a [výrobní prognózy pro nápoje](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-beer-remote/auto-ml-forecasting-beer-remote.ipynb).

## <a name="how-automl-works"></a>Jak funguje AutoML

Během školení Azure Machine Learning vytvoří řadu kanálů paralelně, které pro vás vyzkouší různé algoritmy a parametry. Služba prochází algoritmy ML spárované s výběry funkcí, kde každá iterace vytváří model se studijním skóre. Čím vyšší je skóre, tím lépe se model považuje za "přizpůsobit" vašim datům.  Dojde k zastavení, jakmile bude narazí na kritéria ukončení definovaná v experimentu. 

Pomocí **Azure Machine Learning**můžete navrhovat a spouštět experimenty automatizovaného školení ml pomocí těchto kroků:

1. **Určení problému ml** , který se má vyřešit: klasifikace, předpovědi nebo regrese

1. **Vyberte, jestli chcete použít sadu Python SDK nebo webové prostředí studia**: Přečtěte si informace o paritě mezi [webovým prostředím Python SDK a Studio](#parity).

   * V případě omezeného nebo žádného prostředí pro práci s kódem zkuste vyzkoušet web Azure Machine Learning Studio na adrese.[https://ml.azure.com](https://ml.azure.com/)  
   * Pro vývojáře v Pythonu se podívejte na [sadu SDK Azure Machine Learning Pythonu](how-to-configure-auto-train.md) . 

    [!INCLUDE [aml-applies-to-enterprise-sku](../../includes/aml-applies-to-enterprise-sku-inline.md)]  
    
1. **Zadejte zdroj a formát podaných školicích dat**: numpy Arrays nebo PANDAS dataframe

1. **Nakonfigurujte výpočetní cíl pro školení modelů**, jako je například váš [místní počítač, Azure Machine Learning výpočetní prostředí, vzdálené virtuální počítače nebo Azure Databricks](how-to-set-up-training-targets.md).  Přečtěte si o automatizovaném školení [na vzdáleném prostředku](how-to-auto-train-remote.md).

1. **Nakonfigurujte parametry automatizovaného strojového učení** , které určují, kolik iterací pro různé modely, nastavení parametrů, pokročilý předzpracování/featurization a jaké metriky se mají při určování nejlepšího modelu prohlédnout.  
1. **Odešlete školicí běh.**

1. **Kontrola výsledků** 

Tento proces je znázorněn na následujícím obrázku. 
![Automatizované Machine Learning](./media/concept-automated-ml/automl-concept-diagram2.png)


Můžete také zkontrolovat protokolované informace o spuštění, které [obsahují metriky](how-to-understand-automated-ml.md) shromážděné během spuštění. Školicí běh vytvoří serializovaný objekt (`.pkl` soubor) Pythonu, který obsahuje model a předzpracování dat.

I když je vytváření modelů automatizované, můžete také zjistit, jak jsou pro vygenerované modely [důležité nebo relevantní funkce](how-to-configure-auto-train.md#explain) .

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE2Xc9t]

<a name="preprocess"></a>

## <a name="preprocessing"></a>Předzpracování

V každém automatizovaném experimentu Machine Learning se vaše data předzpracovaná pomocí výchozích metod a volitelně prostřednictvím pokročilého předběžného zpracování.

> [!NOTE]
> Automatické kroky před zpracováním strojového učení (normalizace funkcí, zpracování chybějících dat, převod textu na číselnou atd.) se stanou součástí základního modelu. Při použití modelu pro předpovědi se na vstupní data automaticky aplikují stejné kroky před zpracováním během školení.

### <a name="automatic-preprocessing-standard"></a>Automatické předzpracování (Standard)

V každém automatizovaném experimentu Machine Learning se vaše data automaticky škálují nebo normalizují, aby se algoritmy lépe prováděly.  Během školení modelů se u každého modelu použije jedna z následujících technik škálování nebo normalizace.

|Normalizace škálování&nbsp;&&nbsp;| Popis |
| ------------- | ------------- |
| [StandardScaleWrapper](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.StandardScaler.html)  | Standardizace funkcí odebráním střední odchylky a měřítka jednotky  |
| [MinMaxScalar](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.MinMaxScaler.html)  | Transformuje funkce změnou velikosti jednotlivých funkcí na minimum a maximum tohoto sloupce.  |
| [MaxAbsScaler](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.MaxAbsScaler.html#sklearn.preprocessing.MaxAbsScaler) |Škálovat jednotlivé funkce podle maximální absolutní hodnoty |
| [RobustScalar](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.RobustScaler.html) |Tato funkce škálování podle jejich Quantile rozsahu |
| [PCA](https://scikit-learn.org/stable/modules/generated/sklearn.decomposition.PCA.html) |Redukce v lineárním měřítku za použití hodnoty data k tomu, aby se vygenerovala do menšího prostorového prostoru |
| [TruncatedSVDWrapper](https://scikit-learn.org/stable/modules/generated/sklearn.decomposition.TruncatedSVD.html) |Tento transformátor provádí snížení hodnoty lineárně pomocí zkrácené dekompozice hodnot v jednotném čísle (SVD). V rozporu s DPS, tato Estimator data necentruje před výpočetem dekompozice hodnot v jednotném prostředí, což znamená, že může pracovat s efektivně scipy. zhuštěnými matricemi. |
| [SparseNormalizer](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.Normalizer.html) | Každý vzorek (to znamená, že každý řádek datové matice) s nejméně jednou nenulovou komponentou je znovu škálovat nezávisle na jiných vzorcích, takže jeho norma (L1 nebo L2) se rovná jedné |

### <a name="advanced-preprocessing--featurization"></a>Rozšířené předzpracování & featurization

K dispozici jsou také další rozšířené předzpracování a featurization, například data guardrails, Encoding a transformes. [Přečtěte si další informace o tom, co je zahrnuté featurization](how-to-use-automated-ml-for-ml-models.md#featurization). Povolit toto nastavení pomocí:

+ Azure Machine Learning Studio: v části **Zobrazit další konfiguraci** povolte **Automatické featurization** [pomocí těchto kroků](how-to-use-automated-ml-for-ml-models.md#create-and-run-experiment).

+ Python SDK: určení `"feauturization": 'auto' / 'off' / 'FeaturizationConfig'` pro [ `AutoMLConfig` třídu](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig). 

<a name="parity"></a>

## <a name="the-studio-vs-sdk"></a>Sada Studio vs SDK

Přečtěte si o paritě a rozdílech mezi možnostmi automatických funkcí ML, které jsou k dispozici prostřednictvím sady Python SDK a studia v Azure Machine Learning. 

### <a name="experiment-settings"></a>Nastavení experimentu 

Následující nastavení vám umožní nakonfigurovat experiment automatizovaného ML. 

| |Sada Python SDK|Prostředí pro web Studio|
----|:----:|:----:
Rozdělit data na sady vlaků a ověření| ✓|✓
Podporuje úlohy ML: klasifikace, regrese a prognózy.| ✓| ✓
Optimalizuje se na základě primární metriky.| ✓| ✓
Podporuje COMPUTE AML jako cíl výpočtů. | ✓|✓
Konfigurace horizontu předpovědi, cílové prodlevy & posuvné okno|✓|✓
Nastavení kritérií ukončení |✓|✓ 
Nastavit souběžné iterace| ✓|✓
Odkládací sloupce| ✓|✓
Algoritmy blokování|✓|✓
Vzájemné ověřování |✓|✓
Podporuje školení u clusterů Azure Databricks.| ✓|
Zobrazit názvy navržených funkcí|✓|
Featurization souhrn| ✓|
Featurization pro svátky|✓|
Úrovně podrobností souboru protokolu| ✓|

### <a name="model-settings"></a>Nastavení modelu

Tato nastavení je možné použít pro nejlepší model v důsledku automatizovaného experimentu ML.

| |Sada Python SDK|Prostředí pro web Studio|
|----|:----:|:----:|
|Nejlepší registrace modelu, nasazení, vyjasnění| ✓|✓|
|Povolit &ové kompletování modelů sady zásobníku| ✓|✓|
|Zobrazit nejlepší model založený na neprimární metrikě|✓||
|Povolit/zakázat kompatibilitu s modelem ONNX|✓||
|Testování modelu | ✓| |

### <a name="run-control-settings"></a>Spustit nastavení ovládacího prvku

Tato nastavení umožňují kontrolovat a řídit spouštění experimentů a jejich podřízených spuštění. 

| |Sada Python SDK|Prostředí pro web Studio|
|----|:----:|:----:|
|Spustit souhrnnou tabulku| ✓|✓|
|Zrušit spuštění & podřízených běhů| ✓|✓|
|Získat guardrails| ✓|✓|
|Pozastavit & pokračování v běhu| ✓| |

## <a name="ensemble-models"></a><a name="ensemble"></a>Modely kompletování

Automatizované Machine Learning podporuje modely kompletování, které jsou ve výchozím nastavení povolené. Seznámení se službou kompletování vylepšuje výsledky strojového učení a prediktivní výkon tím, že kombinuje více modelů na rozdíl od použití jednoduchých modelů. Iterace kompletu se zobrazí jako poslední iterace vašeho spuštění. Automatizované strojové učení používá pro kombinování modelů jak hlasovací, tak i stohování metod kompletování:

* **Hlasovací**vše: Předpověď na základě váženého průměru předpokládaných pravděpodobností třídy (pro úlohy klasifikace) nebo předpokládaných regresních cílů (pro regresní úkoly).
* **Skládání**: skládání kombinuje modely heterogenní a nakládá Meta model založený na výstupu z jednotlivých modelů. Aktuální výchozí meta modely jsou LogisticRegression pro úlohy klasifikace a ElasticNet pro úlohy regrese/předpovědi.

K rozhodnutí, které modely použít v kompletu, se používá [algoritmus výběru kompletu Caruana](http://www.niculescu-mizil.org/papers/shotgun.icml04.revised.rev2.pdf) se seřazenou inicializací kompletu. Na vysoké úrovni tento algoritmus inicializuje komplet až o pět modelů s nejlepšími jednotlivými výsledky a ověří, že tyto modely jsou v rámci 5% prahové hodnoty nejlepšího skóre, aby nedocházelo k nedostatečnému počátečnímu kompletování. Pak pro každou iteraci komplet se do existující kompletu přidá nový model a vypočte se výsledné skóre. Pokud nový model vylepšuje existující skóre kompletu, je komplet aktualizován tak, aby obsahoval nový model.

Přečtěte si téma [postup](how-to-configure-auto-train.md#ensemble) pro změnu výchozího nastavení kompletu v automatizovaném strojovém učení.

<a name="use-with-onnx"></a>

## <a name="automl--onnx"></a>AutoML & ONNX

Pomocí Azure Machine Learning můžete pomocí automatizovaného ML vytvořit model Pythonu a nechat ho převést na formát ONNX. Jakmile jsou modely ve formátu ONNX, dají se spouštět na různých platformách a zařízeních. Přečtěte si další informace o [urychlení modelů ml pomocí ONNX](concept-onnx.md).

Podívejte se, jak převést na formát ONNX [v tomto příkladu poznámkového bloku Jupyter](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-bank-marketing-all-features/auto-ml-classification-bank-marketing-all-features.ipynb). Zjistěte, které [algoritmy jsou v ONNX podporované](how-to-configure-auto-train.md#select-your-experiment-type).

Modul runtime ONNX podporuje také jazyk C#, takže můžete použít model sestavený automaticky v aplikacích C# bez nutnosti opětovného kódování nebo jakékoli latence sítě, které zavádí koncové body REST. Přečtěte si další informace o [modelech INFERENCING ONNX pomocí rozhraní API jazyka C# Runtime ONNX](https://github.com/Microsoft/onnxruntime/blob/master/docs/CSharp_API.md). 



## <a name="next-steps"></a>Další kroky

Podívejte se na příklady a Naučte se vytvářet modely pomocí automatizovaného strojového učení:

+ Postupujte podle [kurzu: Automatické učení regresního modelu s Azure Machine Learning](tutorial-auto-train-models.md)

+ Nakonfigurujte nastavení pro experiment automatických školení:
  + V Azure Machine Learning Studiu [použijte tento postup](how-to-use-automated-ml-for-ml-models.md).
  + Pomocí sady Python SDK [použijte následující postup](how-to-configure-auto-train.md).

+ Naučte se, jak automaticky naučit data časových řad pomocí [těchto kroků](how-to-auto-train-forecast.md).

+ Vyzkoušení [Jupyter notebook ukázek pro automatizované strojové učení](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/)

* Automatizovaná ML je dostupná taky v dalších řešeních Microsoftu, jako je [ml.NET](https://docs.microsoft.com/dotnet/machine-learning/automl-overview), [HDInsight](../hdinsight/spark/apache-spark-run-machine-learning-automl.md), [Power BI](https://docs.microsoft.com/power-bi/service-machine-learning-automated) a [SQL Server](https://cloudblogs.microsoft.com/sqlserver/2019/01/09/how-to-automate-machine-learning-on-sql-server-2019-big-data-clusters/)
