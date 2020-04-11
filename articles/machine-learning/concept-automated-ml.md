---
title: Co je automatizovaná ML / AutoML
titleSuffix: Azure Machine Learning
description: Zjistěte, jak azure machine learning může automaticky vybrat algoritmus pro vás a generovat model z něj ušetřit čas pomocí parametrů a kritérií, které zadáte vybrat nejlepší algoritmus pro váš model.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
author: cartacioS
ms.author: sacartac
ms.date: 02/28/2020
ms.openlocfilehash: 095561f02fdeff6688b78d69cc1becc4ee0f8901
ms.sourcegitcommit: fb23286d4769442631079c7ed5da1ed14afdd5fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/10/2020
ms.locfileid: "81115215"
---
# <a name="what-is-automated-machine-learning"></a>Co je automatizované strojové učení?

Automatizované strojové učení, označované také jako automatizované ML, je proces automatizace časově náročných a iterativních úkolů vývoje modelu strojového učení. Umožňuje datovým vědcům, analytikům a vývojářům vytvářet modely ML s vysokým rozsahem, efektivitou a produktivitou při zachování kvality modelu. Automatizovaná technologie ML je založena na průlomu v naší [divizi Microsoft Research](https://arxiv.org/abs/1705.05355).

Tradiční vývoj modelu strojového učení je náročný na zdroje, což vyžaduje značné znalosti domény a čas na výrobu a porovnání desítek modelů. Díky automatizovanému strojovému učení urychlíte čas potřebný k tomu, abyste získali modely ML připravené pro výrobu s velkou lehkostí a efektivitou.

## <a name="when-to-use-automated-ml"></a>Kdy použít automatizovanou ML

Automatické ml použijte, pokud chcete, aby Azure Machine Learning trénoval a vyladil model pomocí zadané cílové metriky. Automatizovaná funkce ML demokratizuje proces vývoje modelu strojového učení a umožňuje svým uživatelům bez ohledu na jejich odborné znalosti v oblasti datových věd identifikovat kanál strojového učení mezi koncovými body pro jakýkoli problém.

Datoví vědci, analytici a vývojáři napříč odvětvími mohou pomocí automatizovaného ml:

+ Implementace řešení strojového učení bez rozsáhlých znalostí programování
+ Ušetřete čas a zdroje
+ Využití osvědčených postupů pro datové vědy
+ Poskytněte agilní řešení problémů

V následující tabulce jsou uvedeny běžné případy použití automatického ml. 

Classification| Prognóza časových řad | Regrese
---|---|---
[Odhalování podvodů](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-credit-card-fraud/auto-ml-classification-credit-card-fraud.ipynb)|[Prognóza prodeje](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-orange-juice-sales/auto-ml-forecasting-orange-juice-sales.ipynb)|[Predikce výkonu procesoru](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/regression-hardware-performance-explanation-and-featurization/auto-ml-regression-hardware-performance-explanation-and-featurization.ipynb)
|[Predikce marketingu](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-bank-marketing-all-features/auto-ml-classification-bank-marketing-all-features.ipynb)|[Prognózování poptávky](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand/auto-ml-forecasting-energy-demand.ipynb)|
|[Klasifikace dat diskusní skupiny](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-text-dnn/auto-ml-classification-text-dnn.ipynb)|[Prognóza výroby nápojů](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-beer-remote/auto-ml-forecasting-beer-remote.ipynb)|

## <a name="design-automated-ml-experiments"></a>Návrh automatizovaných experimentů ML

Pomocí **Azure Machine Learning**můžete navrhnout a spustit automatizované experimenty školení ML pomocí těchto kroků:

1. **Identifikovat problém ML,** který má být vyřešen: klasifikace, prognózy nebo regrese

1. **Určete zdroj a formát popiskovaných trénovacích dat**: Pole Numpy nebo Datový rámec Pandy

1. **Nakonfigurujte výpočetní cíl pro trénink modelu**, jako je místní [počítač, Azure Machine Learning Computes, vzdálené virtuální počítače nebo Azure Databricks](how-to-set-up-training-targets.md).  Informace o automatizovaném školení [na vzdáleném prostředku](how-to-auto-train-remote.md).

1. **Nakonfigurujte automatizované parametry strojového učení,** které určují, kolik iterací v různých modelech, nastavení hyperparametrů, pokročilé předběžné zpracování/featurization a jaké metriky se mají podívat při určování nejlepšího modelu.  Nastavení automatického trénovacího experimentu můžete nakonfigurovat ve [studiu Azure Machine Learning](https://ml.azure.com)nebo pomocí sady [SDK](how-to-configure-auto-train.md). 

    [!INCLUDE [aml-applies-to-enterprise-sku](../../includes/aml-applies-to-enterprise-sku-inline.md)]

1. **Pošlete tréninkový běh.**

## <a name="how-automated-ml-works"></a>Jak funguje automatizovaná ml

Během školení Azure Machine Learning vytvoří řadu v paralelních kanálech, které vyzkoušejí různé algoritmy a parametry. Služba iterace prostřednictvím algoritmů ML spárované s výběry funkcí, kde každá iterace vytváří model s trénovací skóre. Čím vyšší je skóre, tím lepší je model považován za "vhodný" vaše data.  Zastaví se, jakmile narazí na kritéria ukončení definovaná v experimentu. Následující diagram znázorňuje tento proces. 

  ![Automatizované strojové učení](./media/concept-automated-ml/automl-concept-diagram2.png)


Můžete také zkontrolovat protokolované informace o spuštění, které [obsahují metriky](how-to-understand-automated-ml.md) shromážděné během spuštění. Trénovací běh vytvoří serializovaný`.pkl` objekt (soubor) pythonu, který obsahuje model a předběžné zpracování dat.

Při vytváření modelů je automatizovaná, můžete se také [dozvědět, jak důležité nebo relevantní funkce jsou](how-to-configure-auto-train.md#explain) pro generované modely.

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE2Xc9t]

<a name="preprocess"></a>

## <a name="preprocessing"></a>Předzpracování

V každém automatizovaném experimentu strojového učení jsou vaše data předem zpracována pomocí výchozích metod a volitelně prostřednictvím pokročilého předběžného zpracování.

> [!NOTE]
> Automatizované kroky předběžného zpracování strojového učení (normalizace funkcí, zpracování chybějících dat, převod textu na číselné atd.) se stanou součástí základního modelu. Při použití modelu pro předpovědi, stejné kroky předběžného zpracování použít během trénování se použijí na vstupní data automaticky.

### <a name="automatic-preprocessing-standard"></a>Automatické předběžné zpracování (standardní)

V každém automatizovaném experimentu strojového učení se vaše data automaticky škálují nebo normalizují, aby algoritmy fungovaly dobře.  Během trénování modelu bude pro každý model použita jedna z následujících technik škálování nebo normalizace.

|Normalizace&nbsp;&&nbsp;měřítka| Popis |
| ------------- | ------------- |
| [Obálka StandardScale](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.StandardScaler.html)  | Standardizace funkcí odebráním střední a změny měřítka na odchylku jednotky  |
| [MinMaxSkalar](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.MinMaxScaler.html)  | Transformuje funkce změnou měřítka jednotlivých funkcí podle minimálního a maximálního počtu prvků tohoto sloupce.  |
| [MaxAbsScaler](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.MaxAbsScaler.html#sklearn.preprocessing.MaxAbsScaler) |Měřítko každého prvku podle maximální absolutní hodnoty |
| [RobustSkalar](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.RobustScaler.html) |Tento Scaler se vyznačuje svým kvantitou |
| [Dps](https://scikit-learn.org/stable/modules/generated/sklearn.decomposition.PCA.html) |Lineární dimenzionalita redukce pomocí singular value rozkladu dat promítat do nižšího rozměrového prostoru |
| [ZkrácenýSVDWrapper](https://scikit-learn.org/stable/modules/generated/sklearn.decomposition.TruncatedSVD.html) |Tento transformátor provádí lineární dimenzionalitu pomocí zkráceného rozkladu singulární hodnoty (SVD). Na rozdíl od PCA tento odhad nevystředí data před výpočtem jednotnéhodnoty rozkladu, což znamená, že může efektivně pracovat s maticemi scipy.sparse |
| [SparseNormalizer](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.Normalizer.html) | Každý vzorek (tj. každý řádek datové matice) s alespoň jednou nenulovou složkou je změnit měřítko nezávisle na jiných vzorcích tak, aby jeho norma (l1 nebo l2) se rovnala jedné |

### <a name="advanced-preprocessing-optional-featurization"></a>Pokročilé předběžné zpracování: volitelná featurizace

K dispozici jsou také další pokročilé předběžné zpracování a featurization, jako jsou například svodidla dat, kódování a transformace. [Další informace o tom, co featurization je zahrnuta](how-to-use-automated-ml-for-ml-models.md#featurization). Toto nastavení povolte pomocí:

+ Azure Machine Learning Studio: Povolit **automatické featurization** v části **Zobrazit další konfigurace** s [těmito kroky](how-to-use-automated-ml-for-ml-models.md#create-and-run-experiment).

+ Python SDK: `"feauturization": 'auto' / 'off' / 'FeaturizationConfig'` Zadání pro [ `AutoMLConfig` třídu](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig). 

## <a name="classification--regression"></a>Klasifikace & regrese

Klasifikace a regrese jsou nejběžnější typy úloh strojového učení. Oba jsou typy učení pod dohledem, ve kterém modely učí pomocí trénovacích dat a použít tyto poznatky na nová data. Azure Machine Learning nabízí featurizations speciálně pro tyto úkoly, jako je například hluboké neuronové sítě text featurizers pro klasifikaci. Další informace o [možnostech featurization](how-to-use-automated-ml-for-ml-models.md#featurization). 

Hlavním cílem klasifikačních modelů je předpovědět, do kterých kategorií budou nová data spadat na základě poznatků z tréninkových dat. Mezi běžné příklady klasifikace patří detekce podvodů, rozpoznávání rukopisu a detekce objektů.  Přečtěte si další informace a podívejte se na příklad [klasifikace pomocí automatizovaného strojového učení](tutorial-train-models-with-aml.md).

Liší se od klasifikace, kde jsou předpokládané výstupní hodnoty kategorické, regresní modely předpovídají číselné výstupní hodnoty založené na nezávislých prediktorech. V regresi je cílem pomoci vytvořit vztah mezi těmito nezávislými proměnnými prediktoru odhadem toho, jak jedna proměnná ovlivňuje ostatní. Například cena automobilu na základě funkcí, jako je plynu najetých kilometrů, bezpečnostní hodnocení, atd. Přečtěte si další informace a podívejte se na příklad [regrese pomocí automatizovaného strojového učení](tutorial-auto-train-models.md).

## <a name="time-series-forecasting"></a>Prognóza časových řad

Vytváření prognóz je nedílnou součástí každého podnikání, ať už jde o výnosy, zásoby, prodej nebo poptávku zákazníků. Pomocí automatického ml můžete kombinovat techniky a přístupy a získat doporučenou, vysoce kvalitní prognózu časových řad.

Automatizovaný experiment časových řad je považován za problém regrese s více odchylkami. Minulé hodnoty časových řad jsou "otočné", aby se staly dalšími dimenzemi regresoru spolu s dalšími prediktory. Tento přístup, na rozdíl od klasických metod časových řad, má výhodu přirozeně zahrnující více kontextových proměnných a jejich vztah k sobě během tréninku. Automatizované ML učí jeden, ale často interně rozvětvený model pro všechny položky v datové sadě a horizonty předpovědi. Je tedy k dispozici více dat pro odhad parametrů modelu a zobecnění na neviditelné řady bude možné.

Přečtěte si další informace a podívejte se na příklad [automatizovaného strojového učení pro prognózování časových řad](how-to-auto-train-forecast.md). Nebo, viz [poznámkový blok poptávky po energii](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand/auto-ml-forecasting-energy-demand.ipynb) pro podrobné příklady kódu pokročilé prognózy konfigurace, včetně:

* detekce a featurizace dovolené
* časovky a dnn studenti (Auto-ARIMA, Prorok, ForecastTCN)
* mnoho modelů podporuje prostřednictvím seskupování
* křížové ověření valivého původu
* konfigurovatelné zpoždění
* agregační funkce převádících oken

## <a name="ensemble-models"></a><a name="ensemble"></a>Modely souborů

Automatizované strojové učení podporuje modely souborů, které jsou ve výchozím nastavení povoleny. Ensemble learning zlepšuje výsledky strojového učení a prediktivní výkon tím, že kombinuje více modelů, na rozdíl od použití jednotlivých modelů. Iterace souboru se zobrazí jako konečné iterace vašeho běhu. Automatizované strojové učení používá pro kombinování modelů metody hlasování i stohování:

* **Hlasování**: předpovídá na základě váženého průměru pravděpodobností předpovídané třídy (pro úkoly klasifikace) nebo předpokládaných regresních cílů (pro regresní úkoly).
* **Stohování**: stohování kombinuje heterogenní modely a trénuje metamodel založený na výstupu z jednotlivých modelů. Aktuální výchozí meta-modely jsou LogisticRegression pro úkoly klasifikace a ElasticNet pro regresní/prognostické úkoly.

Algoritmus [výběru souboru Caruana](http://www.niculescu-mizil.org/papers/shotgun.icml04.revised.rev2.pdf) s inicializací seřazeného souboru se používá k rozhodování o tom, které modely v rámci souboru použít. Na vysoké úrovni tento algoritmus inicializuje soubor až pěti modely s nejlepšími individuálními skóre a ověří, zda jsou tyto modely v rámci 5% prahu nejlepšího skóre, aby se zabránilo špatnému počátečnímu souboru. Potom pro každou iteraci souboru je přidán nový model do existujícího souboru a vypočítá se výsledné skóre. Pokud nový model vylepšil stávající skóre souboru, soubor se aktualizuje tak, aby zahrnoval nový model.

Podívejte [se na návody](how-to-configure-auto-train.md#ensemble) pro změnu výchozího nastavení souboru v automatizovaném strojovém učení.

## <a name="use-with-onnx"></a>Použití s ONNX

S Azure Machine Learning můžete použít automatické ML k vytvoření modelu Pythonu a jeho převedení do formátu ONNX. Jakmile jsou modely ve formátu ONNX, mohou být spuštěny na různých platformách a zařízeních. Přečtěte si další informace o [urychlení modelů ML s ONNX](concept-onnx.md).

Podívejte se, jak převést do formátu ONNX [v tomto příkladu poznámkového bloku Jupyter](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-bank-marketing-all-features/auto-ml-classification-bank-marketing-all-features.ipynb). Zjistěte, které [algoritmy jsou podporovány v ONNX](how-to-configure-auto-train.md#select-your-experiment-type).

Runtime ONNX také podporuje C#, takže můžete použít model vytvořený automaticky v aplikacích C# bez nutnosti překódování nebo některé z latencí sítě, které zavádějí koncové body REST. Další informace o [odvození modelů ONNX pomocí rozhraní API ONNX runtime C#](https://github.com/Microsoft/onnxruntime/blob/master/docs/CSharp_API.md). 

## <a name="automated-ml-in-azure-machine-learning"></a>Automatizovaná ml v Azure Machine Learning

Azure Machine Learning nabízí dvě možnosti pro práci s automatizovanou ML

* Pro zákazníky s kódem, [Azure Machine Learning Python SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) 

* Pro zákazníky s omezeným/bez kódu můžete studio Azure Machine Learning studio[https://ml.azure.com](https://ml.azure.com/)  

Následující shrnuje funkce ml vysoké úrovně podporované v každém prostředí.

<a name="parity"></a>

### <a name="experiment-settings"></a>Nastavení experimentu 

Následující nastavení umožňují konfigurovat automatizovaný experiment ML. 

| | Python SDK| Studio
----|:----:|:----:
Rozdělení dat do vlakových/ověřovacích sad| ✓|✓
Podporuje ml úkoly: klasifikace, regrese a prognózování| ✓| ✓
Optimalizuje na základě primární metriky| ✓| ✓
Podporuje výpočetní prostředky AML jako výpočetní cíl | ✓|✓
Konfigurace horizontu prognózy, cílové zpoždění & odvalovacíokno|✓|✓
Nastavení kritérií ukončení |✓|✓ 
Nastavení souběžných iterací| ✓|✓
Přetažení sloupců| ✓|✓
Blokové algoritmy|✓|✓
Křížové ověření |✓|✓
Podporuje školení v clusterech Azure Databricks| ✓|
Zobrazit názvy navržených funkcí|✓|
Shrnutí featurizace| ✓|
Dovolená featurization|✓|
Úroveň podrobností pro soubory protokolu| ✓|

### <a name="model-settings"></a>Nastavení modelu

Tato nastavení lze použít na nejlepší model v důsledku automatizovaného experimentu ML.

||Python SDK|Studio
----|:----:|:----:
Nejlepší registrace modelu| ✓|✓
Nejlepší nasazení modelu| ✓| ✓
Nejlepší vysvětlitelnost modelu| ✓|✓
Povolení hlasování souboru & stohování modelů souborů| ✓|✓
Zobrazit nejlepší model na základě neprimární metriky|✓|
Povolení/zakázání kompatibility modelu ONNX|✓|
Testování modelu | ✓| |

### <a name="run-control-settings"></a>Spustit nastavení ovládacího prvku

Tato nastavení umožňují zkontrolovat a řídit spuštění experimentu a jeho podřízené spuštění. 

||Python SDK| Studio
----|:----:|:----:
Spustit souhrnnou tabulku| ✓|✓
Zrušit spuštění| ✓|✓
Zrušit podřízený běh| ✓| ✓
Získejte svodidla| ✓|✓
Pozastavit spuštění| ✓| 
Pokračovat v běhu| ✓| 

## <a name="next-steps"></a>Další kroky

Podívejte se na příklady a zjistěte, jak vytvářet modely pomocí automatizovaného strojového učení:

+ Postupujte [podle kurzu: Automatické trénování regresního modelu pomocí Azure Machine Learning](tutorial-auto-train-models.md)

+ Konfigurace nastavení pro automatický tréninkový experiment:
  + V Azure Machine Learning studio, [použijte tyto kroky](how-to-use-automated-ml-for-ml-models.md).
  + Pomocí sady Python SDK [postupujte takto](how-to-configure-auto-train.md).

+ Naučte se, jak auto trénovat pomocí dat časových řad, [postupujte takto](how-to-auto-train-forecast.md).

+ Vyzkoušejte [ukázky notebooků Jupyter pro automatizované strojové učení](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/)

* Automatizovaná technologie ML je dostupná také v jiných řešeních microsoftu, jako jsou [ML.NET](https://docs.microsoft.com/dotnet/machine-learning/automl-overview), [HDInsight](../hdinsight/spark/apache-spark-run-machine-learning-automl.md), [Power BI](https://docs.microsoft.com/power-bi/service-machine-learning-automated) a [SQL Server.](https://cloudblogs.microsoft.com/sqlserver/2019/01/09/how-to-automate-machine-learning-on-sql-server-2019-big-data-clusters/)
