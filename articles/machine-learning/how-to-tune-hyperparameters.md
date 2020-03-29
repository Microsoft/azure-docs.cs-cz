---
title: Vyladění hyperparametrů pro váš model
titleSuffix: Azure Machine Learning
description: Efektivně vylaďte hyperparametry pro váš model hlubokého učení / strojového učení pomocí Azure Machine Learning. Dozvíte se, jak definovat prostor pro vyhledávání parametrů, určit primární metriku pro optimalizaci a brzy ukončit špatně výkonné spuštění.
ms.author: swatig
author: swatig007
ms.reviewer: sgilley
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 11/04/2019
ms.custom: seodec18
ms.openlocfilehash: 4ca9cac00b9d00dcdbbd27f2fe769de2983c13ae
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75536639"
---
# <a name="tune-hyperparameters-for-your-model-with-azure-machine-learning"></a>Vylaďte hyperparametry pro váš model pomocí Azure Machine Learning
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Efektivně vylaďte hyperparametry pro váš model pomocí Azure Machine Learning.  Hyperparametrika zahrnuje následující kroky:

* Definování prostoru pro vyhledávání parametrů
* Určení primární metriky pro optimalizaci  
* Určit kritéria předčasného ukončení pro špatně výkonné spuštění
* Přidělení prostředků pro optimalizaci hyperparametrů
* Spuštění experimentu s výše uvedenou konfigurací
* Vizualizace tréninkových běhů
* Výběr nejvýkonnější konfigurace pro váš model

## <a name="what-are-hyperparameters"></a>Co jsou hyperparametry?

Hyperparametry jsou nastavitelné parametry, které se rozhodnete trénovat model, který řídí samotný proces školení. Chcete-li například trénovat hlubokou neuronovou síť, můžete před trénovací modelu rozhodnout o počtu skrytých vrstev v síti a počtu uzlů v každé vrstvě. Tyto hodnoty obvykle zůstávají konstantní během procesu tréninku.

Ve scénářích hlubokého učení / strojového učení závisí výkon modelu do značné míry na vybraných hodnotách hyperparametrů. Cílem průzkumu hyperparametrů je prohledat různé konfigurace hyperparametrů a najít konfiguraci, která má za následek nejlepší výkon. Proces průzkumu hyperparametrů je obvykle pečlivě manuální, vzhledem k tomu, že vyhledávací prostor je obrovský a hodnocení každé konfigurace může být nákladné.

Azure Machine Learning umožňuje efektivně automatizovat zkoumání hyperparametrů, což vám ušetří značný čas a prostředky. Můžete zadat rozsah hodnot hyperparametrů a maximální počet spuštění školení. Systém pak automaticky spustí více souběžných spuštění s různými konfiguracemi parametrů a najde konfiguraci, která má za následek nejlepší výkon, měřeno podle metriky, kterou zvolíte. Špatně výkonné tréninkové běhy jsou automaticky předčasně ukončeny, což snižuje plýtvání výpočetními prostředky. Tyto prostředky se místo toho používají k prozkoumání dalších konfigurací hyperparametrů.


## <a name="define-search-space"></a>Definování vyhledávacího prostoru

Automaticky vyladit hyperparametry zkoumáním rozsahu hodnot definovaných pro každý hyperparametr.

### <a name="types-of-hyperparameters"></a>Typy hyperparametrů

Každý hyperparametr může být diskrétní nebo souvislý a má rozdělení hodnot popsaných [výrazem parametru](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.hyperdrive.parameter_expressions?view=azure-ml-py).

#### <a name="discrete-hyperparameters"></a>Diskrétní hyperparametry 

Diskrétní hyperparametry jsou určeny `choice` jako mezi diskrétními hodnotami. `choice`může být:

* jedna nebo více hodnot oddělených čárkami
* objekt `range`
* libovolný `list` objekt


```Python
    {
        "batch_size": choice(16, 32, 64, 128)
        "number_of_hidden_layers": choice(range(1,5))
    }
```

V tomto `batch_size` případě přebírá jednu z hodnot [16, 32, 64, 128] a `number_of_hidden_layers` přebírá jednu z hodnot [1, 2, 3, 4].

Pokročilé diskrétní hyperparametry lze také zadat pomocí distribuce. Podporovány jsou následující distribuce:

* `quniform(low, high, q)`- Vrátí hodnotu jako zaoblená (jednotná(nízká, vysoká) / q ) * q
* `qloguniform(low, high, q)`- Vrátí hodnotu jako round(exp(uniform(nízká, vysoká)) / q ) * q
* `qnormal(mu, sigma, q)`- Vrátí hodnotu jako round(normal(mu, sigma) / q) * q
* `qlognormal(mu, sigma, q)`- Vrátí hodnotu jako round(exp(normal(mu, sigma)) / q) * q

#### <a name="continuous-hyperparameters"></a>Kontinuální hyperparametry 

Kontinuální hyperparametry jsou určeny jako rozdělení přes souvislý rozsah hodnot. Mezi podporované distribuce patří:

* `uniform(low, high)`- Vrátí hodnotu rovnoměrně rozloženou mezi nízkou a vysokou hodnotou.
* `loguniform(low, high)`- Vrátí hodnotu nakreslenou podle exp(uniform(low(high)) tak, aby logaritmus vrácené hodnoty byl rovnoměrně rozložen.
* `normal(mu, sigma)`- Vrátí reálnou hodnotu, která je normálně distribuována se střední hodnotou mu a směrodatnou odchylkou sigma.
* `lognormal(mu, sigma)`- Vrátí hodnotu nakreslenou podle exp(normal(mu, sigma)) tak, aby logaritmus vrácené hodnoty byl normálně distribuován.

Příklad definice prostoru parametru:

```Python
    {    
        "learning_rate": normal(10, 3),
        "keep_probability": uniform(0.05, 0.1)
    }
```

Tento kód definuje vyhledávací prostor se `learning_rate` dvěma parametry - a `keep_probability`. `learning_rate`má normální rozdělení se střední hodnotou 10 a směrodatnou odchylkou 3. `keep_probability`má rovnoměrné rozdělení s minimální hodnotou 0,05 a maximální hodnotou 0,1.

### <a name="sampling-the-hyperparameter-space"></a>Vzorkování hyperparametrické mezery

Můžete také určit metodu vzorkování parametrů, která se má použít přes definici prostoru hyperparametrů. Azure Machine Learning podporuje náhodné vzorkování, vzorkování mřížky a Bayesian vzorkování.

#### <a name="picking-a-sampling-method"></a>Výběr metody odběru vzorků

* Vzorkování mřížky lze použít, pokud lze hyperparametrický prostor definovat jako volbu mezi diskrétními hodnotami a pokud máte dostatečný rozpočet k vyčerpávajícímu vyhledávání všech hodnot v definovaném vyhledávacím prostoru. Kromě toho lze použít automatické předčasné ukončení špatně výkonných spuštění, což snižuje plýtvání prostředky.
* Náhodné vzorkování umožňuje hyperparametrický prostor zahrnout jak diskrétní, tak kontinuální hyperparametry. V praxi to přináší dobré výsledky většinu času a také umožňuje použití automatického předčasného ukončení špatně fungujících běhů. Někteří uživatelé provádějí počáteční vyhledávání pomocí náhodného vzorkování a poté iterativně zpřesňují prostor hledání, aby se zlepšily výsledky.
* Bayesova vzorkování využívá znalosti předchozích vzorků při výběru hodnot hyperparametrů a účinně se snaží zlepšit hlášenou primární metriku. Bayesova vzorkování se doporučuje, pokud máte dostatečný rozpočet na prozkoumání hyperparametrické mezery - pro dosažení nejlepších výsledků s Bayesovým vzorkováním doporučujeme použít maximální počet běhů větší nebo roven 20násobku počtu naladěných hyperparametrů. Všimněte si, že Bayesian vzorkování nepodporuje v současné době žádné zásady předčasného ukončení.

#### <a name="random-sampling"></a>Náhodný odběr vzorků

Při náhodném vzorkování jsou hodnoty hyperparametrů náhodně vybrány z definovaného vyhledávacího prostoru. [Náhodný odběr vzorků](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.hyperdrive.randomparametersampling?view=azure-ml-py) umožňuje, aby vyhledávací prostor zahrnoval jak diskrétní, tak kontinuální hyperparametry.

```Python
from azureml.train.hyperdrive import RandomParameterSampling
param_sampling = RandomParameterSampling( {
        "learning_rate": normal(10, 3),
        "keep_probability": uniform(0.05, 0.1),
        "batch_size": choice(16, 32, 64, 128)
    }
)
```

#### <a name="grid-sampling"></a>Odběr vzorků mřížky

[Vzorkování mřížky](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.hyperdrive.gridparametersampling?view=azure-ml-py) provádí jednoduché vyhledávání mřížky přes všechny možné hodnoty v definovaném vyhledávacím prostoru. Lze jej použít pouze s hyperparametry zadané pomocí `choice`. Například následující mezera má celkem šest vzorků:

```Python
from azureml.train.hyperdrive import GridParameterSampling
param_sampling = GridParameterSampling( {
        "num_hidden_layers": choice(1, 2, 3),
        "batch_size": choice(16, 32)
    }
)
```

#### <a name="bayesian-sampling"></a>Bayesovské vzorkování

[Bayesovského vzorkování](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.hyperdrive.bayesianparametersampling?view=azure-ml-py) je založeno na Bayesském optimalizačním algoritmu a provádí inteligentní volby na hodnotách hyperparametrů, které mají být přidány další. Vybere vzorek na základě toho, jak se provádí předchozí vzorky tak, aby nový vzorek vylepšuje hlášenou primární metriku.

Při použití Bayesian vzorkování, počet souběžných spuštění má vliv na účinnost procesu ladění. Obvykle menší počet souběžných spuštění může vést k lepší vzorkování konvergence, protože menší stupeň paralelismu zvyšuje počet spuštění, které využívají dříve dokončené spuštění.

Bayesova vzorkování `choice`podporuje `uniform`pouze `quniform` , a distribuce přes vyhledávací prostor.

```Python
from azureml.train.hyperdrive import BayesianParameterSampling
param_sampling = BayesianParameterSampling( {
        "learning_rate": uniform(0.05, 0.1),
        "batch_size": choice(16, 32, 64, 128)
    }
)
```

> [!NOTE]
> Bayesské vzorkování nepodporuje žádné zásady předčasného ukončení (viz [Zadání zásad předčasného ukončení](#specify-early-termination-policy)). Při použití Bayesského parametru `early_termination_policy = None`vzorkování, `early_termination_policy` nastavení nebo ponechání parametru.

<a name='specify-primary-metric-to-optimize'/>

## <a name="specify-primary-metric"></a>Určení primární metriky

Zadejte [primární metriku,](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.hyperdrive.primarymetricgoal?view=azure-ml-py) kterou má experiment optimalizace optimalizace hyperparametrizování parametrů. Každý tréninkový běh je vyhodnocen pro primární metriku. Špatně výkonné spuštění (kde primární metrika nesplňuje kritéria stanovená zásadami předčasného ukončení) budou ukončena. Kromě názvu primární metriky můžete také zadat cíl optimalizace – zda chcete maximalizovat nebo minimalizovat primární metriku.

* `primary_metric_name`: Název primární metriky pro optimalizaci. Název primární metriky musí přesně odpovídat názvu metriky zaznamenané školicím skriptem. Viz [Protokol metriky pro hyperparameter tuning](#log-metrics-for-hyperparameter-tuning).
* `primary_metric_goal`: Může být `PrimaryMetricGoal.MAXIMIZE` `PrimaryMetricGoal.MINIMIZE` buď nebo a určuje, zda primární metrika bude maximalizována nebo minimalizována při vyhodnocování spuštění. 

```Python
primary_metric_name="accuracy",
primary_metric_goal=PrimaryMetricGoal.MAXIMIZE
```

Optimalizujte běhy pro maximalizaci "přesnosti".  Ujistěte se, že tuto hodnotu zaznamenáte do školicího skriptu.

<a name='log-metrics-for-hyperparameter-tuning'/>

### <a name="log-metrics-for-hyperparameter-tuning"></a>Metriky protokolu pro hyperparametrické ladění

Trénovací skript pro váš model musí protokolovat příslušné metriky během trénování modelu. Při konfiguraci hyperparametriku ladění, zadáte primární metriku, která se má použít pro vyhodnocení výkonu spuštění. (Viz [Určení primární metriky pro optimalizaci](#specify-primary-metric-to-optimize).)  V trénovacím skriptu je nutné protokolovat tuto metriku, aby byla k dispozici pro proces optimalizace hyperparametrů.

Tuto metriku zaznamenejte do školicího skriptu pomocí následujícího ukázkového fragmentu:

```Python
from azureml.core.run import Run
run_logger = Run.get_context()
run_logger.log("accuracy", float(val_accuracy))
```

Trénovací skript `val_accuracy` vypočítá a přihlásí jej jako "přesnost", který se používá jako primární metriky. Pokaždé, když je metrika zaznamenána, je přijata službou optimalizace hyperparametrů. Je na vývojáři modelu, aby určil, jak často má být tato metrika nahloroků.

<a name='specify-early-termination-policy'/>

## <a name="specify-early-termination-policy"></a>Zadat zásady předčasného ukončení

Ukončit špatně výkonné spuštění automaticky se zásadami předčasného ukončení. Ukončení snižuje plýtvání prostředky a místo toho používá tyto prostředky pro zkoumání jiných konfigurací parametrů.

Při použití zásad předčasného ukončení můžete nakonfigurovat následující parametry, které řídí použití zásady:

* `evaluation_interval`: četnost uplatňování zásad. Pokaždé, když trénovací skript protokoluje primární metriky počítá jako jeden interval. Proto `evaluation_interval` 1 bude používat zásady pokaždé, když skript školení hlásí primární metriku. A `evaluation_interval` of 2 bude používat zásady pokaždé, když skript školení hlásí primární metriku. Pokud není `evaluation_interval` zadán, je ve výchozím nastavení nastavena na hodnotu 1.
* `delay_evaluation`: zpozdí první vyhodnocení zásad o určitý počet intervalů. Jedná se o volitelný parametr, který umožňuje spuštění všech konfigurací pro počáteční minimální počet intervalů, aby se zabránilo předčasnému ukončení tréninkových běhů. Pokud je zadán, zásada platí každý násobek evaluation_interval, která je větší nebo rovna delay_evaluation.

Azure Machine Learning podporuje následující zásady předčasného ukončení.

### <a name="bandit-policy"></a>Zásady banditů

[Bandit](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.hyperdrive.banditpolicy?view=azure-ml-py#definition) je politika ukončení založená na slack faktoru/časově rezervě a intervalu vyhodnocení. Zásady brzy ukončí všechny spuštění, kde primární metrika není v rámci zadaného časového faktoru / časová rezerva částka s ohledem na nejvýkonnější tréninku spustit. Trvá následující parametry konfigurace:

* `slack_factor`nebo `slack_amount`: povolená rezerva s ohledem na nejlepší tréninkový běh. `slack_factor`určuje přípustnou časovou rezervu jako poměr. `slack_amount`určuje přípustnou časovou rezervu jako absolutní částku namísto poměru.

    Zvažte například zásadu Banditů použitou v intervalu 10. Předpokládejme, že nejvýkonnější spuštění v intervalu 10 vykázalo primární metriku 0,8 s cílem maximalizovat primární metriku. Pokud byla zásada `slack_factor` zadána s 0,2, všechny školení běží, jehož nejlepší metrika v intervalu 10 je menší než 0,66 (0,8 /(1+`slack_factor`)) bude ukončena. Pokud místo toho byla zásada zadána `slack_amount` s 0,2, všechny tréninkové běhy, jejichž nejlepší metrika `slack_amount`v intervalu 10 je menší než 0,6 (0,8 - ) bude ukončena.
* `evaluation_interval`: frekvence použití zásady (volitelný parametr).
* `delay_evaluation`: zpozdí první vyhodnocení zásad o zadaný počet intervalů (volitelný parametr).


```Python
from azureml.train.hyperdrive import BanditPolicy
early_termination_policy = BanditPolicy(slack_factor = 0.1, evaluation_interval=1, delay_evaluation=5)
```

V tomto příkladu je zásada předčasného ukončení použita v každém intervalu při vykazování metrik, počínaje intervalem hodnocení 5. Jakýkoli běh, jehož nejlepší metrika je menší než (1/(1+0.1) nebo 91 % nejvýkonnějšího spuštění, bude ukončen.

### <a name="median-stopping-policy"></a>Medián politiky zastavení

[Medián zastavení](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.hyperdrive.medianstoppingpolicy?view=azure-ml-py) je zásada předčasného ukončení založená na spuštěných průměrech primárních metrik vykázaných spuštěními. Tato zásada vypočítá spuštěné průměry napříč všemi spuštěními školení a ukončí spuštění, jejichž výkon je horší než medián spuštěných průměrů. Tato zásada přebírá následující parametry konfigurace:
* `evaluation_interval`: frekvence použití zásady (volitelný parametr).
* `delay_evaluation`: zpozdí první vyhodnocení zásad o zadaný počet intervalů (volitelný parametr).


```Python
from azureml.train.hyperdrive import MedianStoppingPolicy
early_termination_policy = MedianStoppingPolicy(evaluation_interval=1, delay_evaluation=5)
```

V tomto příkladu je zásada předčasného ukončení použita v každém intervalu začínajícím v intervalu hodnocení 5. Běh bude ukončen v intervalu 5, pokud jeho nejlepší primární metrika je horší než medián průběžných průměrů v intervalech 1:5 ve všech tréninkových běhů.

### <a name="truncation-selection-policy"></a>Zásady výběru zkrácení

[Výběr zkrácení](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.hyperdrive.truncationselectionpolicy?view=azure-ml-py) zruší dané procento nejméně výkonných spuštění v každém intervalu hodnocení. Spuštění jsou porovnána na základě jejich výkonu na primární metriky a nejnižší X% jsou ukončeny. Trvá následující parametry konfigurace:

* `truncation_percentage`: procento nejméně výkonných běhů, které mají být ukončeny v každém intervalu hodnocení. Zadejte hodnotu celéčíslo mezi 1 a 99.
* `evaluation_interval`: frekvence použití zásady (volitelný parametr).
* `delay_evaluation`: zpozdí první vyhodnocení zásad o zadaný počet intervalů (volitelný parametr).


```Python
from azureml.train.hyperdrive import TruncationSelectionPolicy
early_termination_policy = TruncationSelectionPolicy(evaluation_interval=1, truncation_percentage=20, delay_evaluation=5)
```

V tomto příkladu je zásada předčasného ukončení použita v každém intervalu začínajícím v intervalu hodnocení 5. Běh bude ukončen v intervalu 5, pokud je jeho výkon v intervalu 5 v nejnižším 20 % výkonu všech běhů v intervalu 5.

### <a name="no-termination-policy"></a>Žádné zásady ukončení

Pokud chcete, aby všechny spuštění školení spustit až do dokončení, nastavte zásady žádné. To bude mít za následek neuplatňování žádné politiky předčasného ukončení.

```Python
policy=None
```

### <a name="default-policy"></a>Výchozí zásada

Pokud není zadána žádná zásada, služba optimalizace hyperparametrů umožní spuštění všech školení až do dokončení.

### <a name="picking-an-early-termination-policy"></a>Výběr zásad předčasného ukončení

* Pokud hledáte konzervativní politiku, která poskytuje úspory bez ukončení slibných pracovních míst, můžete použít medián zastavení politiky s `evaluation_interval` 1 a `delay_evaluation` 5. Jedná se o konzervativní nastavení, které může poskytnout přibližně 25% -35% úspory bez ztráty na primární metriky (na základě našich hodnotících dat).
* Pokud hledáte agresivnější úspory z předčasného ukončení, můžete buď použít Zásady banditů s přísnější (menší) přípustnou časovou rezervou nebo zkrácením zásad výběru s větším procentem zkrácení.

## <a name="allocate-resources"></a>Přidělení zdrojů

Ovládejte rozpočet prostředků pro experiment optimalizace hyperparametrů zadáním maximálního celkového počtu spuštění školení.  Volitelně zadejte maximální dobu trvání experimentu optimalizace hyperparametrů.

* `max_total_runs`: Maximální celkový počet tréninkových běhů, které budou vytvořeny. Horní mez - může být méně spuštění, například pokud je hyperparametrický prostor konečný a má méně vzorků. Musí být číslo mezi 1 a 1000.
* `max_duration_minutes`: Maximální doba trvání experimentu ladění hyperparametrů v minutách. Parametr je volitelný a pokud je k dispozici, všechny spuštěné spuštění po této době trvání jsou automaticky zrušeny.

>[!NOTE] 
>Pokud `max_total_runs` jsou `max_duration_minutes` zadány obě a jsou zadány, experiment optimalizace hyperparametrů ukončí při dosažení první z těchto dvou prahových hodnot.

Kromě toho zadejte maximální počet spuštění školení spustit souběžně během hledání hyperparametering.

* `max_concurrent_runs`: Maximální počet spuštění, které mají být spuštěny souběžně v daném okamžiku. Pokud není zadán, všechny `max_total_runs` budou spuštěny paralelně. Pokud je zadán, musí být číslo mezi 1 a 100.

>[!NOTE] 
>Počet souběžných spuštění je bránou na prostředky, které jsou k dispozici v zadaném výpočetním cíli. Proto je třeba zajistit, že výpočetní cíl má dostupné prostředky pro požadovanou souběžnost.

Přidělení prostředků pro optimalizaci hyperparametrů:

```Python
max_total_runs=20,
max_concurrent_runs=4
```

Tento kód konfiguruje experiment optimalizace hyperparametrů tak, aby používal maximálně 20 celkových spuštění, spouštějící čtyři konfigurace najednou.

## <a name="configure-experiment"></a>Konfigurace experimentu

[Nakonfigurujte experiment optimalizace hyperparametrů](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.hyperdrive.hyperdriverunconfig?view=azure-ml-py) pomocí definovaného vyhledávacího prostoru hyperparametrů, zásady předčasného ukončení, primární metriky a přidělení prostředků z výše uvedených oddílů. Navíc `estimator` zadejte, který bude volán s vzorkované hyperparametry. Popisuje `estimator` trénovací skript, který spustíte, prostředky na úlohu (jeden nebo více GPU) a výpočetní cíl, který chcete použít. Vzhledem k tomu, že souběžnost pro váš experiment optimalizace hyperparametrů je `estimator` bránou na prostředky, které jsou k dispozici, ujistěte se, že výpočetní cíl zadaný v má dostatečné prostředky pro požadovanou souběžnost. (Další informace o odhadech naleznete v tématu [jak trénovat modely](how-to-train-ml-models.md).)

Nakonfigurujte experiment optimalizace hyperparametrů:

```Python
from azureml.train.hyperdrive import HyperDriveConfig
hyperdrive_run_config = HyperDriveConfig(estimator=estimator,
                          hyperparameter_sampling=param_sampling, 
                          policy=early_termination_policy,
                          primary_metric_name="accuracy", 
                          primary_metric_goal=PrimaryMetricGoal.MAXIMIZE,
                          max_total_runs=100,
                          max_concurrent_runs=4)
```

## <a name="submit-experiment"></a>Odeslat experiment

Po definování konfigurace ladění hyperparametrů [odešlete experiment](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment%28class%29?view=azure-ml-py#submit-config--tags-none----kwargs-):

```Python
from azureml.core.experiment import Experiment
experiment = Experiment(workspace, experiment_name)
hyperdrive_run = experiment.submit(hyperdrive_run_config)
```

`experiment_name`je název, který přiřadíte experimentu `workspace` optimalizace hyperparametrů, a je pracovní prostor, ve kterém chcete experiment vytvořit (Další informace o experimentech najdete v [tématu Jak azure machine learning funguje?](concept-azure-machine-learning-architecture.md))

## <a name="warm-start-your-hyperparameter-tuning-experiment-optional"></a>Experiment s laděním hyperparametrů teplým startem (volitelné)

Často hledání nejlepší hodnoty hyperparameter pro váš model může být iterativní proces, které vyžadují více optimalizace běží, které se učí z předchozích spuštění hyperparametering. Opětovné použití znalostí z těchto předchozích spuštění urychlí proces ladění hyperparametrů, čímž se sníží náklady na ladění modelu a potenciálně zlepší primární metriku výsledného modelu. Při spuštění experimentu s hyperparametry ladění s Bayesian vzorkování, zkoušky z předchozího spuštění budou použity jako předchozí znalosti inteligentně vybrat nové vzorky, ke zlepšení primární metriky. Navíc při použití náhodné nebo grid vzorkování, jakékoli rozhodnutí o předčasnéukončení bude využívat metriky z předchozích spuštění k určení špatně výkonné školení běží. 

Azure Machine Learning umožňuje zahřívat spuštění hyperparameter ladění spustit využitím znalostí až z 5 dříve dokončena / zrušené hyperparameter ladění nadřazené spuštění. Můžete určit seznam nadřazených spuštění, které chcete zahřívat, od použití tohoto úryvku:

```Python
from azureml.train.hyperdrive import HyperDriveRun

warmstart_parent_1 = HyperDriveRun(experiment, "warmstart_parent_run_ID_1")
warmstart_parent_2 = HyperDriveRun(experiment, "warmstart_parent_run_ID_2")
warmstart_parents_to_resume_from = [warmstart_parent_1, warmstart_parent_2]
```

Navíc mohou napovědět situace, kdy jsou jednotlivé tréninkové běhy experimentu optimalizace hyperparametrů zrušeny z důvodu rozpočtových omezení nebo selhání z jiných důvodů. Nyní je možné pokračovat v těchto jednotlivých školení běží z posledního kontrolního bodu (za předpokladu, že váš skript školení zpracovává kontrolní body). Obnovení jednotlivých školení spustit bude používat stejnou konfiguraci hyperparameter a připojit výstupy složky používané pro tento běh. Trénovací skript `resume-from` by měl přijmout argument, který obsahuje kontrolní bod nebo soubory modelu, ze kterých chcete pokračovat v trénovacím běhu. Jednotlivé tréninkové běhy můžete pokračovat pomocí následujícího úryvku:

```Python
from azureml.core.run import Run

resume_child_run_1 = Run(experiment, "resume_child_run_ID_1")
resume_child_run_2 = Run(experiment, "resume_child_run_ID_2")
child_runs_to_resume = [resume_child_run_1, resume_child_run_2]
```

Experiment s laděním hyperparametrů můžete nakonfigurovat tak, aby se teplý start `resume_from` z `resume_child_runs` předchozího experimentu nebo obnovil individuální trénovací běhy pomocí volitelných parametrů a v konfiguraci:

```Python
from azureml.train.hyperdrive import HyperDriveConfig

hyperdrive_run_config = HyperDriveConfig(estimator=estimator,
                          hyperparameter_sampling=param_sampling, 
                          policy=early_termination_policy,
                          resume_from=warmstart_parents_to_resume_from, 
                          resume_child_runs=child_runs_to_resume,
                          primary_metric_name="accuracy", 
                          primary_metric_goal=PrimaryMetricGoal.MAXIMIZE,
                          max_total_runs=100,
                          max_concurrent_runs=4)
```

## <a name="visualize-experiment"></a>Vizualizujte experiment

Sada Azure Machine Learning SDK poskytuje [widget poznámkového bloku,](https://docs.microsoft.com/python/api/azureml-widgets/azureml.widgets.rundetails?view=azure-ml-py) který vizualizuje průběh vašich trénovacích běhů. Následující úryvek vizualizuje všechny hyperparametry ladění běží na jednom místě v jupyter notebooku:

```Python
from azureml.widgets import RunDetails
RunDetails(hyperdrive_run).show()
```

Tento kód zobrazuje tabulku s podrobnostmi o spuštění školení pro každou konfiguraci hyperparametrů.

![tabulka ladění hyperparametrů](./media/how-to-tune-hyperparameters/HyperparameterTuningTable.png)

Můžete také vizualizovat výkon každého spuštění v průběhu školení. 

![hyperparametrladění plot](./media/how-to-tune-hyperparameters/HyperparameterTuningPlot.png)

Kromě toho můžete vizuálně identifikovat korelaci mezi výkonem a hodnotami jednotlivých hyperparametrů pomocí vykreslení paralelních souřadnic. 

[![hyperparametrické ladění paralelních souřadnic](./media/how-to-tune-hyperparameters/HyperparameterTuningParallelCoordinates.png)](media/how-to-tune-hyperparameters/hyperparameter-tuning-parallel-coordinates-expanded.png)

Můžete vizualizovat všechny vaše hyperparameter tuning běží na webovém portálu Azure také. Další informace o zobrazení experimentu na webovém portálu naleznete v tématu [Sledování experimentů](how-to-track-experiments.md#view-the-experiment-in-the-web-portal).

## <a name="find-the-best-model"></a>Najděte nejlepší model

Po dokončení všech spuštění hyperparametrika ladění [určete nejvýkonnější konfiguraci](/python/api/azureml-train-core/azureml.train.hyperdrive.hyperdriverun?view=azure-ml-py#get-best-run-by-primary-metric-include-failed-true--include-canceled-true--include-resume-from-runs-true-----typing-union-azureml-core-run-run--nonetype-) a odpovídající hodnoty hyperparametrů:

```Python
best_run = hyperdrive_run.get_best_run_by_primary_metric()
best_run_metrics = best_run.get_metrics()
parameter_values = best_run.get_details()['runDefinition']['Arguments']

print('Best Run Id: ', best_run.id)
print('\n Accuracy:', best_run_metrics['accuracy'])
print('\n learning rate:',parameter_values[3])
print('\n keep probability:',parameter_values[5])
print('\n batch size:',parameter_values[7])
```

## <a name="sample-notebook"></a>Ukázkový poznámkový blok
Viz poznámkové bloky train-hyperparameter-* v této složce:
* [použití azureml/školení s hlubokým učením](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning)

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Další kroky
* [Sledování experimentu](how-to-track-experiments.md)
* [Nasazení trénovaného modelu](how-to-deploy-and-where.md)
