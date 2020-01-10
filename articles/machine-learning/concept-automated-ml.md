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
ms.date: 11/04/2019
ms.openlocfilehash: c4177e4310be3e03b64c260d88239de71f70c17a
ms.sourcegitcommit: 02160a2c64a5b8cb2fb661a087db5c2b4815ec04
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/07/2020
ms.locfileid: "75720136"
---
# <a name="what-is-automated-machine-learning"></a>Co je automatické machine learning?

Automatizované strojové učení, označované také jako automatizovaná ML, je proces automatizace časově náročného a iterativních úkolů vývoje modelů strojového učení. Umožňuje datovým vědcům, analytikům a vývojářům vytvářet modely ML s vysokým škálováním, efektivitou a produktivitou, a přitom udržuje kvalitu modelu. Automatizované ML je založené na převratcích z našeho [oddělení Microsoft Research](https://arxiv.org/abs/1705.05355).

Tradiční vývoj modelů ve strojovém učení je náročný na prostředky, což vyžaduje významné znalosti v doméně a dobu potřebnou k vytváření a porovnávání desítk modelů. Automatickou ML použijte, když chcete, Azure Machine Learning vytvořit výuku a vyladit model pro vás pomocí cílové metriky, kterou zadáte. Služba potom prochází algoritmy ML spárované s výběry funkcí, kde každá iterace vytváří model se studijním skóre. Čím vyšší je skóre, tím lépe se model považuje za "přizpůsobit" vašim datům.

Díky automatizovanému strojového učení urychlíte dobu potřebnou k získání modelů ML připravené k produkčnímu prostředí s využitím Skvělé a efektivity.

## <a name="when-to-use-automated-ml"></a>Kdy použít automatizovanou ML

Automatizované ML demokratizuje proces vývoje modelů strojového učení a svým uživatelům umožníte bez ohledu na jejich znalosti v oblasti datových věd, aby identifikovali ucelený kanál strojového učení pro případné potíže.

Odborníci na data, analytiké a vývojáři v různých oborech můžou používat automatizovanou ML:

+ Implementace řešení pro strojové učení bez rozsáhlých programovacích znalostí
+ Úspora času a prostředků
+ Využijte osvědčené postupy pro datové vědy
+ Zajištění agilních problémů – řešení potíží

V následující tabulce jsou uvedeny běžné případy použití automatizovaného ML. 

Classification| Prognózování časových řad | Regrese
---|---|---
[Zjišťování podvodů](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-credit-card-fraud/auto-ml-classification-credit-card-fraud.ipynb)|[Prognózování prodeje](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-orange-juice-sales/auto-ml-forecasting-orange-juice-sales.ipynb)|[Předpověď výkonu procesoru](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/regression-hardware-performance-explanation-and-featurization/auto-ml-regression-hardware-performance-explanation-and-featurization.ipynb)
|[Předpověď marketingu](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-bank-marketing-all-features/auto-ml-classification-bank-marketing-all-features.ipynb)|[Prognózování poptávky](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand/auto-ml-forecasting-energy-demand.ipynb)|
|[Klasifikace dat diskusních skupin](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-text-dnn/auto-ml-classification-text-dnn.ipynb)|[Výrobní prognózy pro nápoje](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-beer-remote/auto-ml-forecasting-beer-remote.ipynb)|

## <a name="how-automated-ml-works"></a>Jak automatizované ML funguje

Pomocí **Azure Machine Learning**můžete navrhovat a spouštět experimenty automatizovaného školení ml pomocí těchto kroků:

1. **Určení problému ml** , který se má vyřešit: klasifikace, předpovědi nebo regrese

1. **Zadejte zdroj a formát podaných školicích dat**: numpy Arrays nebo PANDAS dataframe

1. **Nakonfigurujte výpočetní cíl pro školení modelů**, jako je například váš [místní počítač, Azure Machine Learning výpočetní prostředí, vzdálené virtuální počítače nebo Azure Databricks](how-to-set-up-training-targets.md).  Přečtěte si o automatizovaném školení [na vzdáleném prostředku](how-to-auto-train-remote.md).

1. **Nakonfigurujte parametry automatizovaného strojového učení** , které určují, kolik iterací pro různé modely, nastavení parametrů, pokročilý předzpracování/featurization a jaké metriky se mají při určování nejlepšího modelu prohlédnout.  Můžete nakonfigurovat nastavení pro automatické školení experimentu v [Azure Machine Learning Studiu](https://ml.azure.com)nebo v sadě [SDK](how-to-configure-auto-train.md). 

    [!INCLUDE [aml-applies-to-enterprise-sku](../../includes/aml-applies-to-enterprise-sku-inline.md)]

1. **Odešlete školicí běh.**

  ![Automatizované Machine Learning](./media/concept-automated-ml/automl-concept-diagram2.png)

Během školení Azure Machine Learning vytvoří řadu paralelních kanálů, které vyzkoušení různých algoritmů a parametrů. Dojde k zastavení, jakmile bude narazí na kritéria ukončení definovaná v experimentu.

Můžete také zkontrolovat protokolované informace o spuštění, které [obsahují metriky](how-to-understand-automated-ml.md) shromážděné během spuštění. Školicí běh vytvoří serializovaný objekt Pythonu (`.pkl` soubor), který obsahuje model a předzpracování dat.

I když je vytváření modelů automatizované, můžete také zjistit, jak jsou pro vygenerované modely [důležité nebo relevantní funkce](how-to-configure-auto-train.md#explain) .

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE2Xc9t]

<a name="preprocess"></a>

## <a name="preprocessing"></a>Předzpracování

V každém automatizovaném experimentu Machine Learning se vaše data předzpracovaná pomocí výchozích metod a volitelně prostřednictvím pokročilého předběžného zpracování.

> [!NOTE]
> Automatické kroky před zpracováním strojového učení (normalizace funkcí, zpracování chybějících dat, převod textu na číselnou atd.) se stanou součástí základního modelu. Při použití modelu pro předpovědi se na vstupní data automaticky aplikují stejné kroky před zpracováním během školení.

### <a name="automatic-preprocessing-standard"></a>Automatické předzpracování (Standard)

V každém automatizovaném experimentu Machine Learning se vaše data automaticky škálují nebo normalizují, aby se algoritmy lépe prováděly.  Během školení modelů se u každého modelu použije jedna z následujících technik škálování nebo normalizace.

|Škálování&nbsp;&&nbsp;normalizace| Popis |
| ------------- | ------------- |
| [StandardScaleWrapper](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.StandardScaler.html)  | Standardizace funkcí odebráním střední odchylky a měřítka jednotky  |
| [MinMaxScalar](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.MinMaxScaler.html)  | Transformuje funkce změnou velikosti jednotlivých funkcí na minimum a maximum tohoto sloupce.  |
| [MaxAbsScaler](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.MaxAbsScaler.html#sklearn.preprocessing.MaxAbsScaler) |Škálovat jednotlivé funkce podle maximální absolutní hodnoty |
| [RobustScalar](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.RobustScaler.html) |Tato funkce škálování podle jejich Quantile rozsahu |
| [PCA](https://scikit-learn.org/stable/modules/generated/sklearn.decomposition.PCA.html) |Redukce v lineárním měřítku za použití hodnoty data k tomu, aby se vygenerovala do menšího prostorového prostoru |
| [TruncatedSVDWrapper](https://scikit-learn.org/stable/modules/generated/sklearn.decomposition.TruncatedSVD.html) |Tento transformátor provádí snížení hodnoty lineárně pomocí zkrácené dekompozice hodnot v jednotném čísle (SVD). V rozporu s DPS, tato Estimator data necentruje před výpočetem dekompozice hodnot v jednotném prostředí, což znamená, že může pracovat s efektivně scipy. zhuštěnými matricemi. |
| [SparseNormalizer](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.Normalizer.html) | Každý vzorek (to znamená, že každý řádek datové matice) s nejméně jednou nenulovou komponentou je znovu škálovat nezávisle na jiných vzorcích, takže jeho norma (L1 nebo L2) se rovná jedné |

### <a name="advanced-preprocessing-optional-featurization"></a>Pokročilý předzpracování: volitelné featurization

K dispozici jsou také další rozšířené předzpracování a featurization, například chybějící hodnoty imputac, Encoding a transformes. [Přečtěte si další informace o tom, co je zahrnuté featurization](how-to-create-portal-experiments.md#preprocess). Povolit toto nastavení pomocí:

+ Azure Machine Learning Studio: [pomocí těchto kroků](how-to-create-portal-experiments.md)v části **spuštění konfigurace** vyberte **Nastavení zobrazení featurization** .

+ Python SDK: určení `"feauturization": auto' / 'off' / FeaturizationConfig` pro [třídu`AutoMLConfig`](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig).

## <a name="prevent-over-fitting"></a>Zabránit přebudování

K naplnění ve strojovém učení dochází, když model přesně odpovídá školicím datům, a v důsledku toho nemůže přesně odhadnout data nefungujících testů. Jinými slovy, model jednoduše zapamatovaných konkrétní vzorky a šum ve školicích datech, ale není dostatečně flexibilní, aby předpovědi na skutečná data. Ve většině případů egregious bude převedený model předpokládat, že kombinace hodnot funkcí zobrazených během školení vždy způsobí přesný stejný výstup pro cíl. 

Nejlepším způsobem, jak zabránit přebudování, je sledovat osvědčené postupy ML, včetně těchto:

* Používání dalších školicích dat a odstraňování statistického posunu
* Zabránění úniku cíle
* Použití méně funkcí
* **Pravidelná optimalizace a optimalizace parametrů**
* **Omezení složitosti modelu**
* **Křížové ověření**

V kontextu automatizovaného ML jsou první tři výše uvedené položky **osvědčené postupy, které implementujete**. Poslední tři tučné položky jsou **osvědčenými postupy automatizovaného** STANDARDu ml, které se ve výchozím nastavení implementují k ochraně před umísťováním. V jiných nastaveních než automatizované ML se za každých šest osvědčených postupů doporučuje, abyste se vyhnuli modelům přebudování.

### <a name="best-practices-you-implement"></a>Osvědčené postupy, které implementujete

Použití **většího množství dat** je nejjednodušší a nejlepší možný způsob, jak zabránit převzetí služeb při selhání, protože přidaný bonus obvykle zvyšuje přesnost. Když použijete více dat, bude model mnohem obtížnější, aby nepamatují přesné vzory a bylo nuceno dosáhnout řešení, která jsou pružnější, aby se vešly další podmínky. Je také důležité rozpoznat **statistickou bias**, abyste zajistili, že vaše školicí data neobsahují izolované vzory, které neexistují v datech živé předpovědi. Tento scénář může být obtížné vyřešit, protože nemůžete překládat mezi vlakem a sadami testů, ale ve srovnání s živými testovacími daty může být přítomno více.

Netěsnost cíle je podobný problém, ve kterém se možná nezobrazuje přemístění mezi sadami vlaků a testování, ale místo toho se zobrazí v době předpovědi. K úniku cíle dojde, když během školení model "podvádět" použijete přístup k datům, která by neměla být normálně v době předpovědi. Například pokud váš problém má předpovídat pondělí, jakou má cena za komoditu v pátek, ale jedna z vašich funkcí omylem zahrnovala data z čtvrtek, která by byla data modelu, nebude mít v čase předpovědi, protože nemůže být v budoucnu. Prosakování cíle představuje snadnou chybu, ale často je charakterizována neobvyklou vysokou přesností pro váš problém. Pokud se pokoušíte odhadnout cenu akcií a vyškolený model s 95% přesností, je pravděpodobná netěsné úniky do vašich funkcí.

Odebrání funkcí může také pomáhat s převzetím, protože brání modelu v použití příliš velkého množství polí k nepamatujíí specifických vzorů, což způsobí, že bude pružnější. Může být obtížné změřit kvantitativní měření, ale pokud můžete odebrat funkce a zachovat stejnou přesnost, pravděpodobně jste model lépe flexibilní a snížili rizika při přebudování.

### <a name="best-practices-automated-ml-implements"></a>Osvědčené postupy automatizovaného ML – implementace

Pravidelná operace je proces minimalizace nákladové funkce k postihu složitých a namontovaných modelů. Existují různé typy probíhající funkce, ale obecně všechny postihují velikost modelu, rozptyl a složitost. Automatizované ML používá L1 (laso), L2 (Ridge) a ElasticNet (L1 a L2 současně) v různých kombinacích s různými nastaveními parametrů modelu, které ovládají přizpůsobení. V jednoduchých výrazech se automatizovaná ML bude lišit v tom, kolik modelu je regulováno, a zvolit nejlepší výsledek.

Automatizované ML také implementuje explicitní omezení složitosti modelu, aby se zabránilo přebudování. Ve většině případů tato implementace slouží konkrétně pro rozhodovací strom nebo algoritmy doménové struktury, přičemž maximální hloubka jednotlivého stromu je omezená a celkový počet stromů používaných v doménové struktuře nebo v technikech kompletování jsou omezené.

Křížové ověření (CV) je proces, který zabírá mnoho podmnožin vašich úplných školicích dat a školení modelu v každé podmnožině. Nápad je, že model by mohl získat "štěstí" a mít skvělou přesnost s jednou podmnožinou, ale s využitím mnoha dílčích sad model nedosáhne pokaždé, když se vyhodnotí. Při provádění CV zadáte datovou sadu pro blokování ověřování, určíte vaše přeložená omezení (počet podmnožin) a automatizovaná ML nasadí váš model a optimalizuje parametry pro minimalizaci chyby v sadě ověřování. Jedno přeložení CV může být příliš velké, ale s využitím mnoha z nich snižuje pravděpodobnost, že je finální model vyhodnocený. Kompromisy jsou v tom, že CV má za následek delší dobu školení, takže větší náklady, protože místo toho, abyste model provedli jednou, je pro každou *n* -podmnožinu CV jednou vyškolí.

> [!NOTE]
> Křížové ověření není ve výchozím nastavení povolené. musí být nakonfigurovaný v nastavení automatizovaného ML. Po dokončení konfigurace CV a zadání ověřovací sady dat je proces pro vás automatizovaný.

### <a name="identifying-over-fitting"></a>Identifikace přebudování

Vezměte v úvahu následující vyškolené modely a jejich odpovídající přesností a testování.

| Model | Přesnost vlaku | Přesnost testu |
|-------|----------------|---------------|
| A | 99,9 % | 95% |
| B | 87% | 87% |
| C | 99,9 % | 45 % |

Zvažujeme model **a**, pokud je přesnost testu u nezobrazených dat nižší než přesnost školení, model je přemontovaný. Přesnost testu by však měla být vždy menší než přesnost školení a rozlišení pro převzetí služeb při selhání, které je vhodné, bude mít za *cíl méně přesné* . 

Při porovnávání modelů **a** a **B**model **a** je lepší model, protože má vyšší přesnost testování, i když je přesnost testu mírně nižší v 95%, nejedná se o významný rozdíl, který navrhuje přemístění, je k dispozici. Model **B** byste nezvolili jednoduše proto, že vlak a test přesností jsou bližší dohromady.

Model **C** představuje nejasný případ přeložení; přesnost školení je velmi vysoká, ale přesnost testu není nikde blízko nejvyšší úrovně. Tento rozdíl je subjektivní, ale pochází ze znalostí o vašem problému a datech a o tom, jaké množství chyb je přijatelné. 

## <a name="time-series-forecasting"></a>Předvídání časových řad

Vytváření prognóz je nedílnou součástí jakékoli firmy, ať už jde o výnosy, inventář, prodej nebo poptávku zákazníků. Pomocí automatizovaného ML můžete kombinovat techniky a přístupy a získat doporučenou a vysoce kvalitní předpověď časových řad.

Automatický experiment s časovou řadou se považuje za problém lineární regrese. Hodnoty za časovou řadou jsou "pivoted" a stanou se dalšími dimenzemi pro regresor společně s jinými koproměnnými. Tento přístup, na rozdíl od metod klasických časových řad, má výhodu přirozeně zahrnující více kontextových proměnných a jejich vzájemný vztah během školení. Automatizovaná ML seznámí s jedním, ale často interně rozvětveným modelem pro všechny položky v datové sadě a horizontech předpovědi. K dispozici jsou proto další data k odhadování parametrů modelu a generalizace na nedostupné řady.

Přečtěte si další informace a podívejte se na příklad [automatizovaného strojového učení pro předpověď časových řad](how-to-auto-train-forecast.md). Případně můžete zobrazit podrobné příklady kódu pro pokročilou konfiguraci prognózování v [poznámkovém bloku Energy Demand](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand/auto-ml-forecasting-energy-demand.ipynb) , včetně:

* detekce svátků a featurization
* DNN (auto-ARIMA, Prophet, ForecastTCN)
* řada podpory modelů prostřednictvím seskupení
* křížové ověření pro návratové zdroje
* konfigurovatelné prodlevy
* souhrnné funkce kumulovaných oken

## <a name="ensemble"></a>Modely kompletování

Automatizované Machine Learning podporuje modely kompletování, které jsou ve výchozím nastavení povolené. Seznámení se službou kompletování vylepšuje výsledky strojového učení a prediktivní výkon tím, že kombinuje více modelů na rozdíl od použití jednoduchých modelů. Iterace kompletu se zobrazí jako poslední iterace vašeho spuštění. Automatizované strojové učení používá pro kombinování modelů jak hlasovací, tak i stohování metod kompletování:

* **Hlasovací**vše: Předpověď na základě váženého průměru předpokládaných pravděpodobností třídy (pro úlohy klasifikace) nebo předpokládaných regresních cílů (pro regresní úkoly).
* **Skládání**: skládání kombinuje modely heterogenní a nakládá Meta model založený na výstupu z jednotlivých modelů. Aktuální výchozí meta modely jsou LogisticRegression pro úlohy klasifikace a ElasticNet pro úlohy regrese/předpovědi.

K rozhodnutí, které modely použít v kompletu, se používá [algoritmus výběru kompletu Caruana](http://www.niculescu-mizil.org/papers/shotgun.icml04.revised.rev2.pdf) se seřazenou inicializací kompletu. Na vysoké úrovni tento algoritmus inicializuje komplet až o pět modelů s nejlepšími jednotlivými výsledky a ověří, že tyto modely jsou v rámci 5% prahové hodnoty nejlepšího skóre, aby nedocházelo k nedostatečnému počátečnímu kompletování. Pak pro každou iteraci komplet se do existující kompletu přidá nový model a vypočte se výsledné skóre. Pokud nový model vylepšuje existující skóre kompletu, je komplet aktualizován tak, aby obsahoval nový model.

Přečtěte si téma [postup](how-to-configure-auto-train.md#ensemble) pro změnu výchozího nastavení kompletu v automatizovaném strojovém učení.

## <a name="imbalance"></a>Nevyvážená data

Nevyvážená data se běžně nacházejí v datech pro scénáře klasifikace Machine Learning a odkazují na data, která obsahují neúměrný poměr pozorování v každé třídě. Tato nerovnováha může vést k falešně vnímanému kladnému účinku přesnosti modelu, protože vstupní data mají posun směrem k jedné třídě, což vede k vyškolený model k napodobení tohoto posunu. 

V rámci svého cíle zjednodušit pracovní postup strojového učení mají automatizované funkce ML integrované možnosti, které vám pomůžou se zabývat nevyváženými daty, jako je například, 

- **Sloupec váhy**: automatizovaná ml podporuje jako vstup vážený sloupec, což způsobuje, že řádky v datech mají být vyšší nebo nižší, což může způsobit větší nebo menší "důležité" třídy.

- Algoritmy používané automatizovanou ML dokáže správně zvládnout nerovnováhu až 20:1, což znamená, že nejběžnější třída může mít 20 krát více řádků v datech než nejméně společná třída.

### <a name="identify-models-with-imbalanced-data"></a>Identifikace modelů s nevyváženými daty

Vzhledem k tomu, že algoritmy klasifikace jsou obvykle vyhodnocovány přesností, je kontrola přesnosti modelu dobrým způsobem, jak zjistit, zda byla ovlivněna nevyváženými daty. Má velmi vysokou přesnost nebo velmi nízkou přesnost pro určité třídy?

Automatizované spuštění ML navíc generuje následující grafy automaticky, což vám pomůže pochopit správnost klasifikací modelu a identifikovat modely, které mohou ovlivnit data, která jsou potenciálně ovlivněná nevyváženými daty.

Graf| Popis
---|---
[Nejasná matice](how-to-understand-automated-ml.md#confusion-matrix)| Vyhodnotí správně klasifikované popisky proti skutečným popiskům dat. 
[Přesnost – odvolání](how-to-understand-automated-ml.md#precision-recall-chart)| Vyhodnotí poměr správných popisků oproti poměru nalezených instancí popisků dat. 
[ROC – křivky](how-to-understand-automated-ml.md#roc)| Vyhodnotí poměr správných jmenovek proti poměru falešně pozitivních popisků.

### <a name="handle-imbalanced-data"></a>Zpracovat nevyvážená data 

Následující techniky jsou další možnosti pro zpracování nevyvážených dat mimo automatizované ML. 

- Převzorkování na dokonce i nevyrovnanost třídy, a to buď pomocí vzorkování menších tříd, nebo dolů vyvzorkováním větších tříd. Tyto metody vyžadují, aby byly zkušenosti se zpracováním a analýzou.

- Využijte metriku výkonu, která se bude lépe dopracovat s nevyváženými daty. Například skóre F1 je vážený průměr přesnosti a odvolání. Míry přesnosti – přesnost přesného třídění – nízká přesnost označuje vysoký počet falešně pozitivních hodnot--, zatímco při odvolání měření úplnosti klasifikátoru udává vysoký počet falešně negativních hodnot. 

## <a name="use-with-onnx-in-c-apps"></a>Použití s ONNX v C# aplikacích

Pomocí Azure Machine Learning můžete pomocí automatizovaného ML vytvořit model Pythonu a nechat ho převést na formát ONNX. Modul runtime ONNX podporuje C#, takže můžete automaticky použít model sestavený ve vašich C# aplikacích bez nutnosti opětovného kódování nebo jakékoli latence sítě, které zavádí koncové body REST. Vyzkoušejte příklad tohoto toku [v tomto poznámkovém bloku Jupyter](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-bank-marketing-all-features/auto-ml-classification-bank-marketing-all-features.ipynb).

## <a name="automated-ml-across-microsoft"></a>Automatizované ML v Microsoftu

Automatizovaná ML je také k dispozici v jiných řešeních Microsoftu, jako jsou:

|Integrace|Popis|
|------------|-----------|
|[ML.NET](https://docs.microsoft.com/dotnet/machine-learning/automl-overview)|Automatický výběr modelů a školení v aplikacích .NET pomocí sady Visual Studio a Visual Studio Code s využitím ML.NET automatizovaného ML (Preview).|
|[HDInsight](../hdinsight/spark/apache-spark-run-machine-learning-automl.md)|Škálujte automatizované školicí úlohy na Sparku v clusterech HDInsight paralelně.|
|[Power BI](https://docs.microsoft.com/power-bi/service-machine-learning-automated)|Vyvolejte modely strojového učení přímo v Power BI (Preview).|
|[SQL Server](https://cloudblogs.microsoft.com/sqlserver/2019/01/09/how-to-automate-machine-learning-on-sql-server-2019-big-data-clusters/)|Vytvářejte nové modely strojového učení pro vaše data v clusterech s SQL Server 2019 s velkými objemy dat.|

## <a name="next-steps"></a>Další kroky

Podívejte se na příklady a Naučte se vytvářet modely pomocí automatizovaného strojového učení:

+ Postupujte podle [kurzu: Automatické učení regresního modelu pomocí automatizovaného Machine Learning Azure](tutorial-auto-train-models.md)

+ Nakonfigurujte nastavení pro experiment automatických školení:
  + V Azure Machine Learning Studiu [použijte tento postup](how-to-create-portal-experiments.md).
  + Pomocí sady Python SDK [použijte následující postup](how-to-configure-auto-train.md).

+ Naučte se, jak automaticky naučit data časových řad pomocí [těchto kroků](how-to-auto-train-forecast.md).

+ Vyzkoušení [Jupyter notebook ukázek pro automatizované strojové učení](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/)
