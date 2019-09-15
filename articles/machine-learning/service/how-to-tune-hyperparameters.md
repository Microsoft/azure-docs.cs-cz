---
title: Optimalizovat pro váš model hyperparameters
titleSuffix: Azure Machine Learning
description: Efektivní ladění parametrů pro model hloubkového učení a strojového učení pomocí Azure Machine Learning. Naučíte se, jak definovat místo pro hledání parametrů, určit primární metriku, která se optimalizuje, a brzké ukončení nedostatečně výkonného spuštění.
ms.author: swatig
author: swatig007
ms.reviewer: sgilley
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 07/08/2019
ms.custom: seodec18
ms.openlocfilehash: cb4023be41377846ed209b3d6702188f5d79ba00
ms.sourcegitcommit: e97a0b4ffcb529691942fc75e7de919bc02b06ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/15/2019
ms.locfileid: "70999393"
---
# <a name="tune-hyperparameters-for-your-model-with-azure-machine-learning"></a>Vyladění parametrů pro model pomocí Azure Machine Learning

Efektivní ladění parametrů pro váš model pomocí Azure Machine Learning.  Hyperparametrů zahrnuje následující kroky:

* Definování prostor parametr hledání
* Zadejte primární metriku pro optimalizaci  
* Zadejte kritéria předčasné ukončení pro nedostatečný výkon spuštění
* Přidělit prostředky pro hyperparametrů
* Spusťte experiment s konfiguraci uvedené výš
* Vizualizujte tréninkových spuštění
* Vyberte ty nejvýkonnější konfigurace pro váš model

## <a name="what-are-hyperparameters"></a>Co jsou hyperparameters?

Hyperparameters jsou měnitelné parametry, které zvolíte pro trénování modelu, kterými se řídí samotný proces školení. Například k trénování hluboké neuronové sítě, rozhodnete počet skrytými vrstvami v síti a počet uzlů v každé vrstvě před trénování modelu. Tyto hodnoty obvykle zůstat konstantní v průběhu školení.

Ve scénářích obsáhlý learning / machine learning výkon modelů silně závisí na hodnotách hyperparameter vybrali. Cílem hyperparameter zkoumání je hledat v různých konfiguracích hyperparameter najít konfiguraci, jejímž výsledkem nejlepší výkon. Obvykle je proces průzkumu hyperparameter pečlivá postupná ruční oznámeno, že je obrovského množství místa vyhledávání a hodnocení každou konfiguraci může být nákladné.

Azure Machine Learning umožňuje automatizovat hyperparameter zkoumání efektivním způsobem ukládá je spoustu času a prostředků. Zadejte rozsah hodnot hyperparameter a maximální počet školení běhů. Systém pak automaticky spustí více souběžných spuštění s konfiguracemi různých parametrů a najde odpovídající konfiguraci, která má za následek nejlepší výkon, měřený podle metriku, kterou zvolíte. Vypíšou tréninkových spuštění jsou automaticky předčasné ukončení, snížit plýtvání zbytečně velký počet výpočetních prostředků. Tyto prostředky se místo toho používají prozkoumat další hyperparameter konfigurace.


## <a name="define-search-space"></a>Definování prostor vyhledávání

Automaticky vylaďte hyperparameters prozkoumáním rozsah hodnot definovaných pro každý hyperparameter.

### <a name="types-of-hyperparameters"></a>Typy hyperparametrů

Každý parametr může být buď diskrétní, nebo souvislý a má distribuci hodnot popsaných [výrazem parametru](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.hyperdrive.parameter_expressions?view=azure-ml-py).

#### <a name="discrete-hyperparameters"></a>Diskrétní hyperparameters 

Diskrétní hyperparameters jsou zadané jako `choice` mezi jednotlivých hodnot. `choice` může být:

* jeden nebo více hodnot oddělených čárkami
* A `range` objektu
* všechny libovolného `list` objektu


```Python
    {
        "batch_size": choice(16, 32, 64, 128)
        "number_of_hidden_layers": choice(range(1,5))
    }
```

V takovém případě `batch_size` provede na jednu z hodnot [16, 32, 64, 128] a `number_of_hidden_layers` provede na jednu z hodnot [1, 2, 3, 4].

Pokročilé diskrétní hyperparameters také lze pomocí distribuci. Podporují se následující distribuce:

* `quniform(low, high, q)` -Vrací hodnotu, jako je kruhové (uniform (dolní, horní) / q) * q
* `qloguniform(low, high, q)` -Vrací hodnotu, jako je kruhové (exp (uniform (dolní, horní)) / q) * q
* `qnormal(mu, sigma, q)` -Vrací hodnotu, jako je kruhové (normální (mu, sigma) / q) * q
* `qlognormal(mu, sigma, q)` -Vrací hodnotu, jako je kruhové (exp (normální (mu, sigma)) / q) * q

#### <a name="continuous-hyperparameters"></a>Průběžné hyperparameters 

Průběžné hyperparameters jsou zadané jako distribuční průběžné rozsahu hodnot. Podporované distribuce patří:

* `uniform(low, high)` -Vrací hodnotu, které jsou rovnoměrně rozloženy mezi nízká a vysoká
* `loguniform(low, high)` -Vrací hodnotu vykreslena podle funkce exp (uniform (dolní, horní)) tak, aby rovnoměrně logaritmus návratová hodnota
* `normal(mu, sigma)` -Vrátí skutečnou hodnotu, která je obvykle distribuován spolu s střední sigma jednotku zasílání zpráv a směrodatné odchylky
* `lognormal(mu, sigma)` -Vrací hodnotu tak, aby logaritmus návratová hodnota je obvykle distribuován vykreslena podle funkce exp (normální (mu, sigma))

Příklad definice místo parametru:

```Python
    {    
        "learning_rate": normal(10, 3),
        "keep_probability": uniform(0.05, 0.1)
    }
```

Tento kód definuje prostoru pro hledání se dvěma parametry - `learning_rate` a `keep_probability`. `learning_rate` je normální rozdělení dat s střední hodnotu 10 a směrodatné odchylky 3. `keep_probability` má rovnoměrné rozdělení s minimální hodnotou 0,05 a maximální hodnota 0,1.

### <a name="sampling-the-hyperparameter-space"></a>Vzorkování hyperparameter místa

Můžete také zadat parametr metody vzorkování na používání přes definici hyperparameter místa. Azure Machine Learning podporuje náhodné vzorkování, vzorkování mřížky a vzorkování bayesovského rozhodování.

#### <a name="random-sampling"></a>Náhodný vzorkování

V náhodných vzorkování, jsou hodnoty hyperparameter náhodně vybrané z definovaných hledání prostoru. [Náhodné vzorkování](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.hyperdrive.randomparametersampling?view=azure-ml-py) umožňuje, aby místo hledání zahrnovalo diskrétní i plynulé parametry.

```Python
from azureml.train.hyperdrive import RandomParameterSampling
param_sampling = RandomParameterSampling( {
        "learning_rate": normal(10, 3),
        "keep_probability": uniform(0.05, 0.1),
        "batch_size": choice(16, 32, 64, 128)
    }
)
```

#### <a name="grid-sampling"></a>Vzorkování mřížky

[Vzorkování mřížky](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.hyperdrive.gridparametersampling?view=azure-ml-py) provede jednoduchou mřížku pro vyhledávání všech vhodných hodnot v definovaném prostoru hledání. Lze použít pouze s hyperparameters zadat pomocí `choice`. Celkem šest vzorků, které se má například následujícím prostoru:

```Python
from azureml.train.hyperdrive import GridParameterSampling
param_sampling = GridParameterSampling( {
        "num_hidden_layers": choice(1, 2, 3),
        "batch_size": choice(16, 32)
    }
)
```

#### <a name="bayesian-sampling"></a>Bayesova vzorkování

[Vzorkování bayesovského rozhodování](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.hyperdrive.bayesianparametersampling?view=azure-ml-py) je založené na algoritmu optimalizace bayesovského rozhodování a umožňuje inteligentní výběry hodnot parametrů na další. Použije vzorek podle jak předchozí ukázky provádí, například, že nové ukázkové zlepšuje ohlášené primární metriku.

Při použití Bayesova vzorkování, počtu souběžných spuštění má dopad na efektivitu ladění procesu. Obvykle menší počet souběžných spuštění může vést k lepší vzorkování konvergence, protože zvyšuje počet běhů, které využívají samosprávné předchozí dokončená spuštění menší míru paralelismu.

Vzorkování bayesovského rozhodování podporuje `choice`jenom distribuce `uniform`, a `quniform` v prostoru pro hledání.

```Python
from azureml.train.hyperdrive import BayesianParameterSampling
param_sampling = BayesianParameterSampling( {
        "learning_rate": uniform(0.05, 0.1),
        "batch_size": choice(16, 32, 64, 128)
    }
)
```

> [!NOTE]
> Vzorkování Bayesova nepodporuje žádnou zásadu předčasné ukončení (viz [zadejte zásadu předčasné ukončení](#specify-early-termination-policy)). Při použití parametru vzorkování Bayesova, nastavte `early_termination_policy = None`, nebo nechte `early_termination_policy` parametru.

<a name='specify-primary-metric-to-optimize'/>

## <a name="specify-primary-metric"></a>Zadejte primární metriku

Určete [primární metriku](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.hyperdrive.primarymetricgoal?view=azure-ml-py) , kterou má experiment pro ladění parametrů optimalizovat. Každé spuštění školení se vyhodnocuje pro primární metriku. Nedostatečný výkon spuštění (kde primární metriku nesplňuje kritéria stanovená zásadou předčasné ukončení) bude ukončena. Kromě primární název metriky cíle optimalizace - také určit, jestli se má Maximalizace nebo minimalizace primární metriku.

* `primary_metric_name`: Název primární metriky, která se má optimalizovat. Název primárního metriky musí přesně shodovat s názvem metrika zapsané podle cvičný skript. Zobrazit [protokolu metriky pro hyperparametrů](#log-metrics-for-hyperparameter-tuning).
* `primary_metric_goal`: Může to být buď `PrimaryMetricGoal.MAXIMIZE` nebo `PrimaryMetricGoal.MINIMIZE` , a určuje, zda bude primární metrika maximalizována nebo minimalizována při vyhodnocování spuštění. 

```Python
primary_metric_name="accuracy",
primary_metric_goal=PrimaryMetricGoal.MAXIMIZE
```

Optimalizujte běží pro maximalizaci "přesnost".  Ujistěte se, že tato hodnota přihlásit cvičný skript.

<a name='log-metrics-for-hyperparameter-tuning'/>

### <a name="log-metrics-for-hyperparameter-tuning"></a>Protokolujte metriky pro hyperparametrů

Skript školení pro váš model musíte se přihlásit důležité metriky během cvičení modelu. Při konfiguraci hyperparametrů určit primární metriky pro účely hodnocení výkonu spuštění. (Viz [zadejte primární metriku pro optimalizaci](#specify-primary-metric-to-optimize).)  Ve skriptu školení musíte se přihlásit tuto metriku tak, aby byl k dispozici hyperparameter ladění procesu.

Tato metrika protokolu ve skriptu školení s následující ukázka fragmentu kódu:

```Python
from azureml.core.run import Run
run_logger = Run.get_context()
run_logger.log("accuracy", float(val_accuracy))
```

Vypočítá cvičný skript `val_accuracy` a zaznamená jako "přesnost", který se používá jako primární metriku. Metrika se protokoluje pokaždé, když se přijme hyperparameter ladění služby. Záleží model pro vývojáře a zjistěte, jak často chcete nahlásit tuto metriku.

<a name='specify-early-termination-policy'/>

## <a name="specify-early-termination-policy"></a>Zadejte zásady předčasné ukončení

Ukončete nedostatečný výkon spuštění automaticky k zásadám předčasné ukončení. Ukončení snižuje plýtvání zbytečně velký počet prostředků a místo toho použije tyto prostředky pro zkoumání konfigurace dalších parametrů.

Při použití zásady předčasné ukončení, můžete nakonfigurovat, které řídí, kdy se použije zásada na následující parametry:

* `evaluation_interval`: frekvence k uplatnění zásad. Pokaždé, když cvičný skript protokoluje primární metrika se počítá jako jeden interval. Proto `evaluation_interval` 1 uplatní zásady pokaždé, když se skript školení hlásí primární metriku. `evaluation_interval` 2 se dané zásady používaly jiné pokaždé cvičný skript hlásí primární metriku. Pokud není zadán, `evaluation_interval` je standardně nastavená na hodnotu 1.
* `delay_evaluation`: zpoždění první vyhodnocení zásad pro zadaný počet intervalů o délce. Je volitelný parametr, který umožňuje všechny konfigurace spuštění pro počáteční minimální počet intervalů, jak se vyhnout předčasné ukončení školení, které běží. Je-li zadána, se zásady vztahují každých několik evaluation_interval, který je větší než nebo rovna hodnotě delay_evaluation.

Azure Machine Learning podporuje následující zásady prvotního ukončení.

### <a name="bandit-policy"></a>Bandit zásad

[Bandit](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.hyperdrive.banditpolicy?view=azure-ml-py#definition) je zásada ukončení založená na rozsahu časové rezervy/rezervy a intervalu vyhodnocení. Zásady se již v rané fázi ukončí všechny běhy, kde primární metriky není v rámci zadaný slackový faktor / slacku velikost s ohledem na nejvýkonnější školení spuštění. Trvá následující parametry konfigurace:

* `slack_factor` nebo `slack_amount`: slack povolené s ohledem na nejvýkonnější školení spustit. `slack_factor` Určuje povolené slack jako poměr. `slack_amount` Určuje povolené slack jako absolutní hodnota, namísto poměr.

    Představte si třeba Bandit zásady se použijí v intervalu 10. Předpokládejme, že nejlepší provádění spustit v intervalu 10 hlášené primární metriku 0,8 s cílem pro maximalizaci primární metriky. Pokud zásada nebyla zadána s `slack_factor` 0,2, spustí všechny školení, jehož nejlepší metriky v intervalu 10 je menší než 0.66 (0,8 / (1 +`slack_factor`)) bude ukončena. Pokud místo toho se zadaným zásady `slack_amount` 0,2, spustí všechny školení, jehož nejlepší metriky v intervalu 10 je menší než 0.6 (0,8 - `slack_amount`) bude ukončena.
* `evaluation_interval`: frekvence pro použití zásady (nepovinný parametr).
* `delay_evaluation`: zpoždění první vyhodnocení zásad pro zadaný počet intervalů (nepovinný parametr).


```Python
from azureml.train.hyperdrive import BanditPolicy
early_termination_policy = BanditPolicy(slack_factor = 0.1, evaluation_interval=1, delay_evaluation=5)
```

V tomto příkladu předčasné ukončení zásady se použijí na každý interval při metriky se vykazují, počínaje interval přehodnocení 5. Jakékoli spuštění, jehož nejlepší metrika je menší než (1/(1+0.1) nebo 91 % nejlépe provádí spuštění bude ukončena.

### <a name="median-stopping-policy"></a>Střední zastavení zásad

[Medián při zastavení](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.hyperdrive.medianstoppingpolicy?view=azure-ml-py) je zásada předčasného ukončení založená na běžících průměrech primárních metrik, které běhy oznámily. Tato zásada vypočítá spuštěné průměry přes všechny tréninkových spuštění a ukončí spuštění, jehož výkon je horší než medián spuštěné průměry. Tyto zásady mají následující parametry konfigurace:
* `evaluation_interval`: frekvence pro použití zásady (nepovinný parametr).
* `delay_evaluation`: zpoždění první vyhodnocení zásad pro zadaný počet intervalů (nepovinný parametr).


```Python
from azureml.train.hyperdrive import MedianStoppingPolicy
early_termination_policy = MedianStoppingPolicy(evaluation_interval=1, delay_evaluation=5)
```

V tomto příkladu předčasné ukončení zásad se použije na každý interval počínaje interval přehodnocení 5. Spuštění bude ukončena v intervalu 5, pokud jeho nejlepší primární metriku horší než medián spuštěné průměry v intervalu 1:5 přes všechny tréninkových spuštění.

### <a name="truncation-selection-policy"></a>Výběr zásad zkrácení

[Zkrácené výběr](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.hyperdrive.truncationselectionpolicy?view=azure-ml-py) zruší v každém intervalu vyhodnocení dané procento nejnižší prováděné běhy. Spuštění porovnání na základě jejich výkonu na primární metriku a nejnižší X % budou ukončeny. Trvá následující parametry konfigurace:

* `truncation_percentage`: spustí procento nejnižší provádění ukončení v každém intervalu hodnocení. Zadejte celočíselnou hodnotu mezi 1 a 99.
* `evaluation_interval`: frekvence pro použití zásady (nepovinný parametr).
* `delay_evaluation`: zpoždění první vyhodnocení zásad pro zadaný počet intervalů (nepovinný parametr).


```Python
from azureml.train.hyperdrive import TruncationSelectionPolicy
early_termination_policy = TruncationSelectionPolicy(evaluation_interval=1, truncation_percentage=20, delay_evaluation=5)
```

V tomto příkladu předčasné ukončení zásad se použije na každý interval počínaje interval přehodnocení 5. Spuštění bude ukončeno v intervalu 5, pokud je jeho výkon v intervalu 5 v nejnižší 20% výkonu všech běhů v intervalu 5.

### <a name="no-termination-policy"></a>Žádné zásady ukončení

Pokud chcete, aby všechny tréninkových spuštění dokončen, nastavení zásad na hodnotu None. To nebude mít vliv není použití předčasné ukončení zásad.

```Python
policy=None
```

### <a name="default-policy"></a>Výchozí zásady

Pokud není zadána žádná zásada, bude služba ladění u parametrů nastavena na dokončení všech školicích běhů.

>[!NOTE] 
>Pokud hledáte konzervativní zásady, které nabízí úspory, bez ukončení slibně úlohy, můžete použít zásady zastavení Medián s `evaluation_interval` 1 a `delay_evaluation` 5. Toto jsou konzervativní nastavení, které můžou zajisti přibližně 25 % – 35 % levnější bez ztráty na primární metriku (podle našich data pro vyhodnocení).

## <a name="allocate-resources"></a>Přidělit prostředky

Řízení rozpočtu prostředků pro váš hyperparameter ladění tak, že zadáte maximální celkový počet tréninkových spuštění experimentu.  Volitelně můžete zadejte maximální dobu trvání vaše hyperparameter ladění experimentu.

* `max_total_runs`: Maximální celkový počet běhů cvičení, které budou vytvořeny. Horní mez – může mít méně spuštění, například místo hyperparameter je však konečný a má menší počet vzorků. Musí být číslo v rozsahu od 1 do 1000.
* `max_duration_minutes`: Maximální doba v minutách experimentu ladění parametrů Parametr je nepovinný a pokud jsou k dispozici, se automaticky zruší všechny běhy, které by být spuštěny po této hodnotě duration.

>[!NOTE] 
>Pokud mají oba `max_total_runs` a `max_duration_minutes` zadávají hyperparameter ladění experiment ukončí po dosažení první z těchto dvou prahové hodnoty.

Kromě toho určete, že maximální počet školení spustí spustit souběžně během vaší hyperparameter optimalizace pro hledání.

* `max_concurrent_runs`: Maximální počet spuštění souběžně v daném okamžiku. Je-li žádný zadán, všechny `max_total_runs` spustí paralelně. Je-li zadána, musí být číslo mezi 1 a 100.

>[!NOTE] 
>Počet souběžných spuštění jsou závislé na dostupné v cílové zadaný výpočetní prostředky. Proto je potřeba zajistit, že cílové výpočetní prostředí nemá k dispozici prostředky požadované rozhraní Concurrency.

Přidělte prostředky pro hyperparametrů:

```Python
max_total_runs=20,
max_concurrent_runs=4
```

Tento kód nakonfiguruje experiment vyladěním parametrů, aby používal maximálně 20 spuštění celkem, současně se čtyřmi konfiguracemi.

## <a name="configure-experiment"></a>Konfigurace testu

[Nakonfigurujte](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.hyperdrive.hyperdriverunconfig?view=azure-ml-py) experiment s optimalizací parametrů pomocí definovaného prostoru pro hledání parametrů, zásad prvotního ukončení, primární metriky a přidělení prostředků z výše uvedených částí. Kromě toho poskytují `estimator` vzorky hyperparameters, která bude volána. `estimator` Popisuje cvičný skript spustíte, zdroje na jednu úlohu (jeden nebo více grafickými procesory) a cílové výpočetní prostředí používat. Protože souběžnost pro vaše hyperparameter ladění experiment jsou závislé na prostředcích, které jsou k dispozici, aby se cílového výpočetního prostředí v `estimator` má dostatek prostředků pro požadovanou souběžnosti. (Další informace o odhady, naleznete v tématu [trénování modelů](how-to-train-ml-models.md).)

Konfigurovat vaše hyperparameter ladění testu:

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

## <a name="submit-experiment"></a>Odeslání experimentu

Po definování konfigurace ladění vašich parametrů [odešlete experiment](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment%28class%29?view=azure-ml-py#submit-config--tags-none----kwargs-):

```Python
from azureml.core.experiment import Experiment
experiment = Experiment(workspace, experiment_name)
hyperdrive_run = experiment.submit(hyperdrive_run_config)
```

`experiment_name`je název, který jste přiřadili experimentu ladění vašich parametrů, `workspace` a je pracovní prostor, ve kterém chcete vytvořit experiment (Další informace o experimentech najdete v tématu [jak Azure Machine Learning funguje?](concept-azure-machine-learning-architecture.md))

## <a name="visualize-experiment"></a>Vizualizujte experimentu

Sada Azure Machine Learning SDK poskytuje pomůcku poznámkového [bloku](https://docs.microsoft.com/python/api/azureml-widgets/azureml.widgets.rundetails?view=azure-ml-py) , která vizualizuje průběh vašich školicích běhů. Následující fragment kódu vizualizuje vaše hyperparameter ladění se spustí v poznámkového bloku Jupyter na jednom místě:

```Python
from azureml.widgets import RunDetails
RunDetails(hyperdrive_run).show()
```

Tento kód zobrazí tabulku s podrobnostmi o tréninkových spuštění pro každou hyperparameter konfigurací.

![Tabulka hyperparameter ladění](media/how-to-tune-hyperparameters/HyperparameterTuningTable.png)

Také můžete vizualizovat výkon jednotlivých spuštění v průběhu školení. 

![Diagram hyperparameter ladění](media/how-to-tune-hyperparameters/HyperparameterTuningPlot.png)

Kromě toho můžete vizuálně identifikovat korelace mezi výkonem a hodnoty jednotlivých hyperparameters pomocí paralelní koordinuje vykreslení. 

[![paralelní souřadnice ladění parametrů](media/how-to-tune-hyperparameters/HyperparameterTuningParallelCoordinates.png)](media/how-to-tune-hyperparameters/hyperparameter-tuning-parallel-coordinates-expanded.png)

Můžete vizualizovat váš hyperparameter ladění spuštění portálu Azure web. Další informace o tom, jak zobrazit experimentu na webovém portálu najdete v tématu [jak sledovat experimenty](how-to-track-experiments.md#view-the-experiment-in-the-web-portal).

## <a name="find-the-best-model"></a>Vyhledání nejvhodnějšího modelu

Po dokončení všech spuštění ladění parametrů proveďte [identifikaci nejlepšího provedení konfigurace](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.hyperdrive.hyperdriverun?view=azure-ml-py#get-best-run-by-primary-metric-include-failed-true--include-canceled-true-) a odpovídajících hodnot parametrů:

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

## <a name="sample-notebook"></a>Ukázka poznámkového bloku
Další informace najdete v poznámkách k vlakovým parametrům-* v této složce:
* [How-to-use-azureml/Training-with-Deep-Learning](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning)

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Další postup
* [Sledovat experimentu](how-to-track-experiments.md)
* [Nasazení trénovaného modelu](how-to-deploy-and-where.md)
