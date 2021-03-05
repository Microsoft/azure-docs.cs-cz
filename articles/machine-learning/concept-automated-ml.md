---
title: Co je to automatizovaná ML? AutoML
titleSuffix: Azure Machine Learning
description: Přečtěte si, jak Azure Machine Learning může automaticky generovat model pomocí parametrů a kritérií, která zadáte.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: cartacioS
ms.author: sacartac
ms.date: 10/27/2020
ms.custom: automl
ms.openlocfilehash: 6ed9b316fceeb30a775e2e0d90e7bbb0a07278cd
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2021
ms.locfileid: "102180493"
---
# <a name="what-is-automated-machine-learning-automl"></a>Co je Automated Machine Learning (AutoML)?

Automatizované Machine Learning, označovaný také jako automatizovaná ML nebo AutoML, je proces automatizace časově náročného zpracování iterativních úloh vývoje modelů strojového učení. Umožňuje datovým vědcům, analytikům a vývojářům vytvářet modely ML s vysokým škálováním, efektivitou a produktivitou, a přitom udržuje kvalitu modelu. Automatizované ML v Azure Machine Learning jsou založené na převratcích z našeho [oddělení Microsoft Research](https://www.microsoft.com/research/project/automl/).

Tradiční vývoj modelů ve strojovém učení je náročný na prostředky, což vyžaduje významné znalosti v doméně a dobu potřebnou k vytváření a porovnávání desítk modelů. Díky automatizovanému strojového učení urychlíte dobu potřebnou k získání modelů ML připravené k produkčnímu prostředí s využitím Skvělé a efektivity.

## <a name="when-to-use-automl-classify-regression--forecast"></a>Kdy použít AutoML: klasifikovat, regrese, & prognóza

Automatickou ML použijte, když chcete, Azure Machine Learning vytvořit výuku a vyladit model pro vás pomocí cílové metriky, kterou zadáte. Automatizované ML demokratizuje proces vývoje modelů strojového učení a svým uživatelům umožníte bez ohledu na jejich znalosti v oblasti datových věd, aby identifikovali ucelený kanál strojového učení pro případné potíže.

Odborníci na data, analytiké a vývojáři v různých oborech můžou používat automatizovanou ML:
+ Implementace řešení ML bez rozsáhlých programovacích znalostí
+ Úspora času a prostředků
+ Využijte osvědčené postupy pro datové vědy
+ Zajištění agilních problémů – řešení potíží

### <a name="classification"></a>Klasifikace

Klasifikace je běžný úkol strojového učení. Klasifikace je typ vzdělávání pod dohledem, ve kterém se modely učí pomocí školicích dat, a aplikujte tyto učení na nová data. Azure Machine Learning nabízí featurizations specificky pro tyto úlohy, jako je například hluboký neuronové Network text featurizers for Classification. Další informace o [možnostech featurization](how-to-configure-auto-features.md#featurization) 

Hlavním cílem modelů klasifikace je předpovědět, ke kterým kategoriím budou nová data zacházet v závislosti na studiu ze svých školicích dat. Mezi běžné klasifikace patří zjišťování podvodů, rozpoznávání rukopisu a detekce objektů. Přečtěte si další informace a podívejte se na příklad [Vytvoření klasifikačního modelu pomocí automatizovaného ml](tutorial-first-experiment-automated-ml.md).

Podívejte se na příklady klasifikace a automatizované strojové učení v těchto poznámkových blocích Pythonu: [zjišťování podvodů](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-credit-card-fraud/auto-ml-classification-credit-card-fraud.ipynb), [předpověď marketingu](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-bank-marketing-all-features/auto-ml-classification-bank-marketing-all-features.ipynb)a [klasifikace dat diskusních skupin](https://towardsdatascience.com/automated-text-classification-using-machine-learning-3df4f4f9570b) .

### <a name="regression"></a>Regrese

Podobně jako u klasifikace jsou regresní úlohy také běžnou úlohou v dohledovém učení. Azure Machine Learning nabízí [featurizations specificky pro tyto úlohy](how-to-configure-auto-features.md#featurization).

Liší se od klasifikace, kde jsou hodnoty předpokládaných výstupů kategorií, regresní modely odhadne číselné výstupní hodnoty na základě nezávislých prediktivních. V regresi je cílem přispět k navázání vztahu mezi těmito nezávislými proměnnými proměnných odhadem toho, jak jedna proměnná má vliv na ostatní. Například cena automobilu na základě funkcí, jako je plynová km, bezpečnostní hodnocení atd. Přečtěte si další informace a podívejte [se na příklad regrese pomocí automatizovaného strojového učení](tutorial-auto-train-models.md).

Podívejte se na příklady regrese a automatizovaného strojového učení pro předpovědi v těchto poznámkových blocích Pythonu: [předpověď výkonu procesoru](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/regression-explanation-featurization/auto-ml-regression-explanation-featurization.ipynb), 

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

## <a name="how-automated-ml-works"></a>Jak automatizované ML funguje

Během školení Azure Machine Learning vytvoří řadu kanálů paralelně, které pro vás vyzkouší různé algoritmy a parametry. Služba prochází algoritmy ML spárované s výběry funkcí, kde každá iterace vytváří model se studijním skóre. Čím vyšší je skóre, tím lépe se model považuje za "přizpůsobit" vašim datům.  Dojde k zastavení, jakmile bude narazí na kritéria ukončení definovaná v experimentu. 

Pomocí **Azure Machine Learning** můžete navrhovat a spouštět experimenty automatizovaného školení ml pomocí těchto kroků:

1. **Určení problému ml** , který se má vyřešit: klasifikace, předpovědi nebo regrese

1. **Vyberte, jestli chcete použít sadu Python SDK nebo webové prostředí studia**: Přečtěte si informace o paritě mezi [webovým prostředím Python SDK a Studio](#parity).

   * V případě omezeného nebo žádného prostředí pro práci s kódem zkuste vyzkoušet web Azure Machine Learning Studio na adrese. [https://ml.azure.com](https://ml.azure.com/)  
   * Pro vývojáře v Pythonu se podívejte na [sadu SDK Azure Machine Learning Pythonu](how-to-configure-auto-train.md) . 
    
1. **Zadejte zdroj a formát podaných školicích dat**: numpy Arrays nebo PANDAS dataframe

1. **Nakonfigurujte výpočetní cíl pro školení modelů**, jako je například váš [místní počítač, Azure Machine Learning výpočetní prostředí, vzdálené virtuální počítače nebo Azure Databricks](how-to-set-up-training-targets.md).  Přečtěte si o automatizovaném školení [na vzdáleném prostředku](how-to-auto-train-remote.md).

1. **Nakonfigurujte parametry automatizovaného strojového učení** , které určují, kolik iterací pro různé modely, nastavení parametrů, pokročilý předzpracování/featurization a jaké metriky se mají při určování nejlepšího modelu prohlédnout.  
1. **Odešlete školicí běh.**

1. **Kontrola výsledků** 

Tento proces je znázorněn na následujícím obrázku. 
![Automatizované Machine Learning](./media/concept-automated-ml/automl-concept-diagram2.png)


Můžete také zkontrolovat protokolované informace o spuštění, které [obsahují metriky](how-to-understand-automated-ml.md) shromážděné během spuštění. Školicí běh vytvoří serializovaný objekt ( `.pkl` soubor) Pythonu, který obsahuje model a předzpracování dat.

I když je vytváření modelů automatizované, můžete také zjistit, jak jsou pro vygenerované modely [důležité nebo relevantní funkce](how-to-configure-auto-train.md#explain) .

Naučte se používat [vzdálený výpočetní cíl](how-to-auto-train-remote.md).



> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE2Xc9t]


## <a name="feature-engineering"></a>Příprava atributů

Inženýrské funkce je proces využití dat v doméně k vytváření funkcí, které pomůžou lépe zjistit algoritmy ML. V Azure Machine Learning se pro usnadnění metodologie funkcí používají způsoby škálování a normalizace. Souhrnně jsou tyto techniky a inženýry funkcí označovány jako featurization.

Pro automatizované experimenty strojového učení se featurization aplikuje automaticky, ale dá se přizpůsobit i na základě vašich dat. [Přečtěte si další informace o tom, co je zahrnuté featurization](how-to-configure-auto-features.md#featurization).  

> [!NOTE]
> Automatické kroky featurization strojového učení (normalizace funkcí, zpracování chybějících dat, převod textu na číselnou atd.) se stanou součástí základního modelu. Při použití modelu pro předpovědi se na vstupní data automaticky aplikují stejné kroky featurization, jaké jste použili během školení.

### <a name="automatic-featurization-standard"></a>Automatický featurization (Standard)

V každém automatizovaném experimentu Machine Learning se vaše data automaticky škálují nebo normalizují, aby se algoritmy lépe prováděly. Během školení modelů se u každého modelu použije jedna z následujících technik škálování nebo normalizace. Přečtěte si, jak AutoML pomáhá [zabránit přebudování a nevyváženým datům](concept-manage-ml-pitfalls.md) ve vašich modelech.

|&nbsp; & &nbsp; Zpracování škálování| Popis |
| ------------- | ------------- |
| [StandardScaleWrapper](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.StandardScaler.html)  | Standardizace funkcí odebráním střední odchylky a měřítka jednotky  |
| [MinMaxScalar](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.MinMaxScaler.html)  | Transformuje funkce změnou velikosti jednotlivých funkcí na minimum a maximum tohoto sloupce.  |
| [MaxAbsScaler](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.MaxAbsScaler.html#sklearn.preprocessing.MaxAbsScaler) |Škálovat jednotlivé funkce podle maximální absolutní hodnoty |
| [RobustScalar](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.RobustScaler.html) |Tato funkce škálování podle jejich Quantile rozsahu |
| [PCA](https://scikit-learn.org/stable/modules/generated/sklearn.decomposition.PCA.html) |Redukce v lineárním měřítku za použití hodnoty data k tomu, aby se vygenerovala do menšího prostorového prostoru |
| [TruncatedSVDWrapper](https://scikit-learn.org/stable/modules/generated/sklearn.decomposition.TruncatedSVD.html) |Tento transformátor provádí snížení hodnoty lineárně pomocí zkrácené dekompozice hodnot v jednotném čísle (SVD). V rozporu s DPS, tato Estimator data necentruje před výpočetem dekompozice hodnot v jednotném prostředí, což znamená, že může pracovat s efektivně scipy. zhuštěnými matricemi. |
| [SparseNormalizer](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.Normalizer.html) | Každý vzorek (to znamená, že každý řádek datové matice) s nejméně jednou nenulovou komponentou je znovu škálovat nezávisle na jiných vzorcích, takže jeho norma (L1 nebo L2) se rovná jedné |

### <a name="customize-featurization"></a>Přizpůsobení featurization

K dispozici jsou také další techniky techniků, jako je například kódování a transformace. 

Povolit toto nastavení pomocí:

+ Azure Machine Learning Studio: v části **Zobrazit další konfiguraci** povolte **Automatické featurization** [pomocí těchto kroků](how-to-use-automated-ml-for-ml-models.md#customize-featurization).

+ Python SDK: zadejte `"feauturization": 'auto' / 'off' / 'FeaturizationConfig'` v objektu [AutoMLConfig](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig) . Přečtěte si další informace o [Povolení featurization](how-to-configure-auto-features.md). 

## <a name="ensemble-models"></a><a name="ensemble"></a> Modely kompletování

Automatizované Machine Learning podporuje modely kompletování, které jsou ve výchozím nastavení povolené. Seznámení se službou kompletování vylepšuje výsledky strojového učení a prediktivní výkon tím, že kombinuje více modelů na rozdíl od použití jednoduchých modelů. Iterace kompletu se zobrazí jako poslední iterace vašeho spuštění. Automatizované strojové učení používá pro kombinování modelů jak hlasovací, tak i stohování metod kompletování:

* **Hlasovací** vše: Předpověď na základě váženého průměru předpokládaných pravděpodobností třídy (pro úlohy klasifikace) nebo předpokládaných regresních cílů (pro regresní úkoly).
* **Skládání**: skládání kombinuje modely heterogenní a nakládá Meta model založený na výstupu z jednotlivých modelů. Aktuální výchozí meta modely jsou LogisticRegression pro úlohy klasifikace a ElasticNet pro úlohy regrese/předpovědi.

K rozhodnutí, které modely použít v kompletu, se používá [algoritmus výběru kompletu Caruana](http://www.niculescu-mizil.org/papers/shotgun.icml04.revised.rev2.pdf) se seřazenou inicializací kompletu. Na vysoké úrovni tento algoritmus inicializuje komplet až o pět modelů s nejlepšími jednotlivými výsledky a ověří, že tyto modely jsou v rámci 5% prahové hodnoty nejlepšího skóre, aby nedocházelo k nedostatečnému počátečnímu kompletování. Pak pro každou iteraci komplet se do existující kompletu přidá nový model a vypočte se výsledné skóre. Pokud nový model vylepšuje existující skóre kompletu, je komplet aktualizován tak, aby obsahoval nový model.

Přečtěte si téma [postup](how-to-configure-auto-train.md#ensemble) pro změnu výchozího nastavení kompletu v automatizovaném strojovém učení.

## <a name="guidance-on-local-vs-remote-managed-ml-compute-targets"></a><a name="local-remote"></a>Doprovodné materiály k místním a vzdáleným výpočetním cílům ML

Webové rozhraní pro automatizované ML vždy používá vzdálený [cíl výpočtů](concept-compute-target.md).  Při použití sady Python SDK ale zvolíte buď místní výpočetní prostředí, nebo vzdálený výpočetní cíl pro automatické školení ML.

* **Místní výpočty**: školení probíhá na vašem místním přenosném počítači nebo na VÝPOČETNÍm virtuálním počítači. 
* **Vzdálené výpočty**: školení probíhá na Machine Learning výpočetních clusterů.  

### <a name="choose-compute-target"></a>Zvolit cíl pro výpočetní prostředky
Při volbě cíle výpočetní služby Vezměte v úvahu tyto faktory:

 * **Zvolte místní výpočetní** prostředky: Pokud se váš scénář týká počátečních průzkumů nebo ukázek pomocí malých objemů dat a krátkých vlaků (tj. sekund nebo pár minut na jeden podřízený běh), může být lepší volbou školení na místním počítači.  Nejedná se o čas nastavení, prostředky infrastruktury (Váš počítač nebo virtuální počítač) jsou přímo dostupné.
 * **Výběr vzdáleného výpočetního clusteru ml**: Pokud provádíte školení s většími datovými sadami, jako je v produkčním školení, vytváření modelů, které potřebují delší vlaky, vzdálené výpočty budou poskytovat mnohem lepší výkon, protože `AutoML` se paralelizovat vlaky v uzlech clusteru. Ve vzdáleném výpočetním prostředí se čas spuštění interní infrastruktury přidá přibližně 1,5 minut na jedno podřízený běh a další minuty pro infrastrukturu clusteru, pokud virtuální počítače ještě nejsou spuštěné.

### <a name="pros-and-cons"></a>Specialisté a nevýhody
Při volbě místní a vzdálené považovat tyto specialisty i nevýhody.

|  | Specialisté (výhody)  |Nevýhody (nevýhody)  |
|---------|---------|---------|---------|
|**Místní cíl výpočtů** |  <li> Čas spuštění prostředí není k dispozici.   | <li>  Podmnožina funkcí<li>  Nejde paralelizovat spustit. <li> Horší pro velké objemy dat. <li>Žádné datové streamy během školení <li>  Žádná featurization založená na DNN <li> Jenom Python SDK |
|**Vzdálené výpočetní clustery ML**|  <li> Úplná sada funkcí <li> Paralelizovat podřízená spuštění <li>   Podpora velkých objemů dat<li>  Featurization založené na DNN <li>  Dynamická škálovatelnost výpočetního clusteru na vyžádání <li> K dispozici je také prostředí bez kódu (webové uživatelské rozhraní).  |  <li> Čas spuštění pro uzly clusteru <li> Čas zahájení u každého podřízeného spuštění    |

### <a name="feature-availability"></a>Dostupnost funkcí 

 K dispozici jsou další funkce, pokud používáte vzdálené výpočty, jak je znázorněno v následující tabulce. 

| Funkce                                                    | Vzdálené | Místní | 
|------------------------------------------------------------|--------|-------|
| Streamování dat (podpora velkých objemů dat, až 100 GB)          | ✓      |       | 
| Featurization a školení textu založeného na DNN-BERT             | ✓      |       |
| Dopředná podpora GPU (školení a odvození)        | ✓      |       |
| Klasifikace obrázků a podpora popisků                  | ✓      |       |
| Modely auto-ARIMA, Prophet a ForecastTCN pro prognózování | ✓      |       | 
| Paralelní vícenásobné běhy a iterace                       | ✓      |       |
| Vytváření modelů s možností interpretace v uživatelském rozhraní AutoML Studio Web Experience      | ✓      |       |
| Přizpůsobení technologie funkcí v uživatelském rozhraní sady Studio Web Experience| ✓      |       |
| Ladění předparametrů Azure ML                             | ✓      |       |
| Podpora pracovního postupu kanálu Azure ML                         | ✓      |       |
| Pokračovat v běhu                                             | ✓      |       |
| Prognózování                                                | ✓      | ✓     |
| Vytváření a spouštění experimentů v poznámkových blocích                    | ✓      | ✓     |
| Zaregistrujte a vizualizujte informace a metriky experimentů v uživatelském rozhraní. | ✓      | ✓     |
| Guardrails dat                                            | ✓      | ✓     |

## <a name="many-models"></a>Mnoho modelů 

[Mnoho modelů řešení model](https://aka.ms/many-models) (Preview) sestavuje na Azure Machine Learning a umožňuje používat automatizované ml k výuce, provozování a správě stovek nebo dokonce tisíců modelů strojového učení.

Například vytvoření modelu __pro každou instanci nebo jednotlivce__ v následujících scénářích může vést k lepším výsledkům:

* Předpověď prodeje pro každé individuální úložiště
* Prediktivní údržba stovek ropných zásobníků
* Přizpůsobení prostředí pro jednotlivé uživatele.

## <a name="automl-in-azure-machine-learning"></a>AutoML v Azure Machine Learning

Azure Machine Learning nabízí dvě prostředí pro práci s automatizovanými ML:

* Pro zákazníky se zkušenostmi s kódem [Azure Machine Learning Python SDK](/python/api/overview/azure/ml/intro?preserve-view=true&view=azure-ml-py) 

* Pro zákazníky s omezením a nezkušeným kódem se Azure Machine Learning Studio na [https://ml.azure.com](https://ml.azure.com/)  

<a name="parity"></a>

### <a name="experiment-settings"></a>Nastavení experimentu 

Následující nastavení vám umožní nakonfigurovat experiment automatizovaného ML. 

| |Sada Python SDK|Prostředí pro web Studio|
----|:----:|:----:
|**Rozdělit data na sady vlaků a ověření**| ✓|✓
|**Podporuje úlohy ML: klasifikace, regrese a prognózy.**| ✓| ✓
|**Optimalizuje se na základě primární metriky.**| ✓| ✓
|**Podporuje Azure ML COMPUTE jako cíl výpočtů.** | ✓|✓
|**Konfigurace horizontu předpovědi, cílové prodlevy & posuvné okno**|✓|✓
|**Nastavení kritérií ukončení** |✓|✓ 
|**Nastavit souběžné iterace**| ✓|✓
|**Vyřazení sloupců**| ✓|✓
|**Algoritmy blokování**|✓|✓
|**Vzájemné ověřování** |✓|✓
|**Podporuje školení u clusterů Azure Databricks.**| ✓|
|**Zobrazit názvy navržených funkcí**|✓|
|**Featurization souhrn**| ✓|
|**Featurization pro svátky**|✓|
|**Úrovně podrobností souboru protokolu**| ✓|

### <a name="model-settings"></a>Nastavení modelu

Tato nastavení je možné použít pro nejlepší model v důsledku automatizovaného experimentu ML.

| |Sada Python SDK|Prostředí pro web Studio|
|----|:----:|:----:|
|**Nejlepší registrace modelu, nasazení, vyjasnění**| ✓|✓|
|**Povolit &ové kompletování modelů sady zásobníku**| ✓|✓|
|**Zobrazit nejlepší model založený na neprimární metrikě**|✓||
|**Povolit/zakázat kompatibilitu s modelem ONNX**|✓||
|**Testování modelu** | ✓| |

### <a name="run-control-settings"></a>Spustit nastavení ovládacího prvku

Tato nastavení umožňují kontrolovat a řídit spouštění experimentů a jejich podřízených spuštění. 

| |Sada Python SDK|Prostředí pro web Studio|
|----|:----:|:----:|
|**Spustit souhrnnou tabulku**| ✓|✓|
|**Zrušit spuštění & podřízených běhů**| ✓|✓|
|**Získat guardrails**| ✓|✓|
|**Pozastavit & pokračování v běhu**| ✓| |

<a name="use-with-onnx"></a>

## <a name="automl--onnx"></a>AutoML & ONNX

Pomocí Azure Machine Learning můžete pomocí automatizovaného ML vytvořit model Pythonu a nechat ho převést na formát ONNX. Jakmile jsou modely ve formátu ONNX, dají se spouštět na různých platformách a zařízeních. Přečtěte si další informace o [urychlení modelů ml pomocí ONNX](concept-onnx.md).

Podívejte se, jak převést na formát ONNX [v tomto příkladu poznámkového bloku Jupyter](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-bank-marketing-all-features/auto-ml-classification-bank-marketing-all-features.ipynb). Zjistěte, které [algoritmy jsou v ONNX podporované](how-to-configure-auto-train.md#select-your-experiment-type).

Modul runtime ONNX podporuje také jazyk C#, takže můžete použít model sestavený automaticky v aplikacích C# bez nutnosti opětovného kódování nebo jakékoli latence sítě, které zavádí koncové body REST. Přečtěte si další informace o [použití modelu AUTOML ONNX v aplikaci .NET s ml.NET](./how-to-use-automl-onnx-model-dotnet.md) a [Inferencing ONNX modely s rozhraním API ONNX runtime C#](https://github.com/Microsoft/onnxruntime/blob/master/docs/CSharp_API.md). 

## <a name="next-steps"></a>Další kroky

K dispozici je několik prostředků, které vám pomohou při práci s AutoML. 

### <a name="tutorials-how-tos"></a>Kurzy a postupy
Kurzy představují ucelené Úvodní příklady scénářů AutoML.
+ **Při prvním použití kódu** postupujte podle [kurzu: Automatické učení regresního modelu s Azure Machine Learning Python SDK](tutorial-auto-train-models.md).

 + V **případě nedostatku nebo prostředí bez kódu** si přečtěte [kurz: vytváření automatizovaných modelů klasifikace ml pomocí Azure Machine Learning studia](tutorial-first-experiment-automated-ml.md).

Články s postupy poskytují další podrobnosti o tom, jaké funkce AutoML nabízí. Třeba 

+ Konfigurovat nastavení pro automatické experimenty školení
    + V Azure Machine Learning Studiu [použijte tento postup](how-to-use-automated-ml-for-ml-models.md). 
    + Pomocí sady Python SDK [použijte následující postup](how-to-configure-auto-train.md).

+  Pomocí [těchto kroků](how-to-auto-train-forecast.md)se naučíte, jak automaticky naučit pomocí dat časových řad.

### <a name="jupyter-notebook-samples"></a>Ukázky poznámkových bloků Jupyter 

Přečtěte si podrobné příklady kódu a případy použití v [úložišti notebook GitHub pro automatizované ukázky strojového učení](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/).

### <a name="python-sdk-reference"></a>Referenční dokumentace sady Python SDK

Prohloubte své znalosti vzorů návrhu sady SDK a specifikace tříd pomocí [Referenční dokumentace třídy AutoML](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig?preserve-view=true&view=azure-ml-py). 

> [!Note]
> Automatizované funkce strojového učení jsou dostupné i v dalších řešeních Microsoftu, jako jsou [ml.NET](/dotnet/machine-learning/automl-overview), [HDInsight](../hdinsight/spark/apache-spark-run-machine-learning-automl.md), [Power BI](/power-bi/service-machine-learning-automated) a [SQL Server](https://cloudblogs.microsoft.com/sqlserver/2019/01/09/how-to-automate-machine-learning-on-sql-server-2019-big-data-clusters/)
