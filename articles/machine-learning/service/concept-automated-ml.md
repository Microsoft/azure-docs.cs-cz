---
title: Co je Automated ML/automl
titleSuffix: Azure Machine Learning service
description: Přečtěte si, jak Azure Machine Learning služba může pro vás automaticky vybrat algoritmus a vytvořit z něj model, který vám ušetří čas použitím parametrů a kritérií, které zadáte pro výběr nejlepšího algoritmu pro váš model.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
author: nacharya1
ms.author: nilesha
ms.date: 06/20/2019
ms.custom: seodec18
ms.openlocfilehash: c563278a9d23810a5e6f0adc8082c8cfc5a0510c
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/19/2019
ms.locfileid: "68358847"
---
# <a name="what-is-automated-machine-learning"></a>Co je automatické machine learning?

Automatizované strojové učení, označované také jako autoML, je proces automatizace časově náročného a iterativních úkolů vývoje modelů strojového učení. Umožňuje datovým vědcům, analytikům a vývojářům vytvářet modely ML s vysokým škálováním, efektivitou a produktivitou, a přitom udržuje kvalitu modelu.

Tradiční vývoj modelů ve strojovém učení je náročný na prostředky, což vyžaduje významné znalosti v doméně a dobu potřebnou k vytváření a porovnávání desítk modelů. Automatickou ML použijte, když chcete, Azure Machine Learning vytvořit výuku a vyladit model pro vás pomocí cílové metriky, kterou zadáte. Služba potom prochází algoritmy ML spárované s výběry funkcí, kde každá iterace vytváří model se studijním skóre. Čím vyšší je skóre, tím lépe se model považuje za "přizpůsobit" vašim datům.

Díky automatizovanému strojového učení urychlíte dobu potřebnou k získání modelů ML připravené k produkčnímu prostředí s využitím Skvělé a efektivity.

## <a name="when-to-use-automated-ml"></a>Kdy použít automatizovanou ML

Automatizované ML demokratizuje proces vývoje modelů strojového učení a svým uživatelům umožníte bez ohledu na jejich znalosti v oblasti datových věd, aby identifikovali ucelený kanál strojového učení pro případné potíže.

Odborníci na data, analytici a vývojáři v rámci odvětví můžou používat automatizované ML na:

+ Implementace řešení pro strojové učení bez rozsáhlých programovacích znalostí
+ Úspora času a prostředků
+ Využijte osvědčené postupy pro datové vědy
+ Zajištění agilních problémů – řešení potíží

## <a name="how-automated-ml-works"></a>Jak automatizované ML funguje

Pomocí **Azure Machine Learning služby**můžete navrhovat a spouštět experimenty automatizovaného školení na základě těchto kroků:

1. **Určení problému ml** , který se má vyřešit: klasifikace, předpovědi nebo regrese

1. **Zadejte zdroj a formát podaných školicích dat**: Numpy pole nebo PANDAS dataframe

1. **Nakonfigurujte výpočetní cíl pro školení modelů**, jako je například váš [místní počítač, Azure Machine Learning výpočetní prostředí, vzdálené virtuální počítače nebo Azure Databricks](how-to-set-up-training-targets.md).  Přečtěte si o automatizovaném školení [na vzdáleném prostředku](how-to-auto-train-remote.md).

1. **Nakonfigurujte parametry automatizovaného strojového učení** , které určují, kolik iterací pro různé modely, nastavení parametrů, pokročilý předzpracování/featurization a jaké metriky se mají při určování nejlepšího modelu prohlédnout.  Můžete nakonfigurovat nastavení automatického školení experimentu [v Azure Portal](how-to-create-portal-experiments.md) nebo [pomocí sady SDK](how-to-configure-auto-train.md).

1. **Odešlete školicí běh.**

  ![Automatizované Machine Learning](./media/how-to-automated-ml/automl-concept-diagram2.png)

Během školení vytvoří služba Azure Machine Learning řadu paralelních kanálů, které vyzkoušení různých algoritmů a parametrů. Dojde k zastavení, jakmile bude narazí na kritéria ukončení definovaná v experimentu.

Můžete také zkontrolovat protokolované informace o spuštění, které [obsahují metriky](how-to-understand-automated-ml.md) shromážděné během spuštění. Školicí běh vytvoří serializovaný objekt (`.pkl` soubor) Pythonu, který obsahuje model a předzpracování dat.

I když je vytváření modelů automatizované, můžete také zjistit, jak jsou pro vygenerované modely [důležité nebo relevantní funkce](how-to-configure-auto-train.md#explain) .

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE2Xc9t]

<a name="preprocess"></a>

## <a name="preprocessing"></a>Předzpracování

V každém automatizovaném experimentu Machine Learning se vaše data předzpracovaná pomocí výchozích metod a volitelně prostřednictvím pokročilého předběžného zpracování.

### <a name="automatic-preprocessing-standard"></a>Automatické předzpracování (Standard)

V každém automatizovaném experimentu Machine Learning se vaše data automaticky škálují nebo normalizují, aby se algoritmy lépe prováděly.  Během školení modelů se u každého modelu použije jedna z následujících technik škálování nebo normalizace.

|Normalizace škálování&nbsp;&&nbsp;| Popis |
| ------------- | ------------- |
| [StandardScaleWrapper](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.StandardScaler.html)  | Standardizace funkcí odebráním střední odchylky a měřítka jednotky  |
| [MinMaxScalar](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.MinMaxScaler.html)  | Transformuje funkce změnou velikosti jednotlivých funkcí na minimum a maximum tohoto sloupce.  |
| [MaxAbsScaler](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.MaxAbsScaler.html#sklearn.preprocessing.MaxAbsScaler) |Škálovat jednotlivé funkce podle maximální absolutní hodnoty |
| [RobustScalar](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.RobustScaler.html) |Tato funkce škálování podle jejich Quantile rozsahu |
| [PCA](https://scikit-learn.org/stable/modules/generated/sklearn.decomposition.PCA.html) |Redukce v lineárním měřítku za použití hodnoty data k tomu, aby se vygenerovala do menšího prostorového prostoru |
| [TruncatedSVDWrapper](https://scikit-learn.org/stable/modules/generated/sklearn.decomposition.TruncatedSVD.html) |Tento transformátor provádí snížení hodnoty lineárně pomocí zkrácené dekompozice hodnot v jednotném čísle (SVD). V rozporu s DPS, tato Estimator data necentruje před výpočetem dekompozice hodnoty v jednotném čísle. To znamená, že může efektivně pracovat s scipy. zhuštěnými matricemi |
| [SparseNormalizer](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.Normalizer.html) | Každý vzorek (to znamená, že každý řádek datové matice) s nejméně jednou nenulovou komponentou je znovu škálovat nezávisle na jiných vzorcích, takže jeho norma (L1 nebo L2) se rovná jedné |

### <a name="advanced-preprocessing-optional-featurization"></a>Pokročilý předzpracování: volitelné featurization

K dispozici jsou také další rozšířené předzpracování a featurization, například chybějící hodnoty imputac, Encoding a transformes. [Přečtěte si další informace o tom, co je zahrnuté featurization](how-to-create-portal-experiments.md#preprocess). Povolit toto nastavení pomocí:

+ Azure Portal: Zaškrtnutím  políčka předzpracovat v části **Rozšířená nastavení** [proveďte tyto kroky](how-to-create-portal-experiments.md).

+ Sada Python SDK: Určení `"preprocess": True` [pro třídu`AutoMLConfig` ](https://docs.microsoft.com/python/api/azureml-train-automl/azureml.train.automl.automlconfig?view=azure-ml-py).


## <a name="time-series-forecasting"></a>Předvídání časových řad
Vytváření prognóz je nedílnou součástí jakékoli firmy, ať už jde o výnosy, inventář, prodej nebo poptávku zákazníků. Pomocí automatizovaného ML můžete kombinovat techniky a přístupy a získat doporučenou a vysoce kvalitní předpověď časových řad. 

Automatický experiment s časovou řadou se považuje za problém lineární regrese. Hodnoty za časovou řadou jsou "pivoted" a stanou se dalšími dimenzemi pro regresor společně s jinými koproměnnými. Tento přístup, na rozdíl od metod klasických časových řad, má výhodu přirozeně zahrnující více kontextových proměnných a jejich vzájemný vztah během školení. Automatizovaná ML seznámí s jedním, ale často interně rozvětveným modelem pro všechny položky v datové sadě a horizontech předpovědi. K dispozici jsou proto další data k odhadování parametrů modelu a generalizace na nedostupné řady. 

Přečtěte si další informace a podívejte se na příklad [automatizovaného strojového učení pro předpověď časových řad](how-to-auto-train-forecast.md).

## <a name="ensemble-models"></a>Modely kompletování

Pomocí automatizovaného strojového učení s [algoritmem výběru Caruana kompletování s](http://www.niculescu-mizil.org/papers/shotgun.icml04.revised.rev2.pdf)setříděnou inicializací kompletu můžete přeškolovat modely kompletování. Seznámení se službou kompletování vylepšuje výsledky strojového učení a prediktivní výkon tím, že pronikne mnoho modelů na rozdíl od použití jednoduchých modelů. Iterace kompletu se zobrazí jako poslední iterace běhu.

## <a name="use-with-onnx-in-c-apps"></a>Použití s ONNX v C# aplikacích

Pomocí Azure Machine Learning můžete pomocí automatizovaného ML vytvořit model Pythonu a nechat ho převést na formát ONNX. Modul runtime ONNX podporuje C#, takže můžete automaticky použít model sestavený ve vašich C# aplikacích bez nutnosti opětovného kódování nebo jakékoli latence sítě, které zavádí koncové body REST. Vyzkoušejte příklad tohoto toku [v tomto poznámkovém bloku Jupyter](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-with-onnx/auto-ml-classification-with-onnx.ipynb).

## <a name="automated-ml-across-microsoft"></a>Automatizované ML v Microsoftu

Automatizovaná ML je také k dispozici v jiných řešeních Microsoftu, jako jsou:

|Integrace|Popis|
|------------|-----------|
|[ML.NET](https://docs.microsoft.com/dotnet/machine-learning/automl-overview)|Automatický výběr modelů a školení v aplikacích .NET pomocí sady Visual Studio a Visual Studio Code s využitím ML.NET automatizovaného ML (Preview).|
|[HDInsight](../../hdinsight/spark/apache-spark-run-machine-learning-automl.md)|Škálujte automatizované školicí úlohy na Sparku v clusterech HDInsight paralelně.|
|[Power BI](https://docs.microsoft.com/power-bi/service-machine-learning-automated)|Vyvolejte modely strojového učení přímo v Power BI (Preview).|
|[SQL Server](https://cloudblogs.microsoft.com/sqlserver/2019/01/09/how-to-automate-machine-learning-on-sql-server-2019-big-data-clusters/)|Vytvářejte nové modely strojového učení pro vaše data v clusterech s SQL Server 2019 s velkými objemy dat.|

## <a name="next-steps"></a>Další postup

Podívejte se na příklady a Naučte se vytvářet modely pomocí automatizovaného strojového učení:

+ Postupujte podle [tohoto kurzu: Automatické učení regresního modelu pomocí automatizované Machine Learning Azure](tutorial-auto-train-models.md)

+ Nakonfigurujte nastavení pro experiment automatických školení:
  + V Azure Portal rozhraní [použijte tento postup](how-to-create-portal-experiments.md).
  + Pomocí sady Python SDK [použijte následující postup](how-to-configure-auto-train.md).

+ Naučte se, jak automaticky naučit data časových řad pomocí [těchto kroků](how-to-auto-train-forecast.md).

+ Vyzkoušení [Jupyter notebook ukázek pro automatizované strojové učení](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/)
