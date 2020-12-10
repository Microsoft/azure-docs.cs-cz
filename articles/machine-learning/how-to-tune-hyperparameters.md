---
title: Vyladění parametrů pro váš model
titleSuffix: Azure Machine Learning
description: Pomocí Azure Machine Learning můžete efektivně ladit parametry pro obsáhlý Learning a modely strojového učení.
ms.author: swatig
author: swatig007
ms.reviewer: sgilley
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.date: 03/30/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python, contperf-fy21q1
ms.openlocfilehash: a1a0b89dee5a6d57e6f317c5f6c8b61ffeda3c33
ms.sourcegitcommit: 3ea45bbda81be0a869274353e7f6a99e4b83afe2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/10/2020
ms.locfileid: "97029069"
---
# <a name="tune-hyperparameters-for-your-model-with-azure-machine-learning"></a>Vyladění parametrů pro model pomocí Azure Machine Learning


Automatizujte efektivní ladění parametrů pomocí Azure Machine Learning [balíčku Hyperdrive](/python/api/azureml-train-core/azureml.train.hyperdrive?preserve-view=true&view=azure-ml-py). Naučte se, jak provést kroky potřebné k optimalizaci parametrů s [Azure Machine Learning SDK](/python/api/overview/azure/ml/?preserve-view=true&view=azure-ml-py):

1. Definice hledaného prostoru parametrů
1. Určení primární metriky k optimalizaci  
1. Zadat zásady prvotního ukončení pro nízkoúrovňové spouštění
1. Přidělit prostředky
1. Spustit experiment s definovanou konfigurací
1. Vizualizace školicích běhů
1. Vyberte nejlepší konfiguraci pro váš model.

## <a name="what-are-hyperparameters"></a>Co jsou to parametry?

V **parametrech** jsou seřiditelné parametry, které umožňují řídit proces školení modelu. Například u neuronové sítí určíte počet skrytých vrstev a počet uzlů v jednotlivých vrstvách. Výkon modelu závisí silně na parametrech.

 **Ladění parametrů** je proces hledání konfigurace parametrů, které mají za následek nejlepší výkon. Tento proces je obvykle výpočetně nákladný a ruční.

Azure Machine Learning umožňuje automatizovat optimalizaci parametrů a paralelně spouštět experimenty, aby bylo možné efektivně optimalizovat parametry.


## <a name="define-the-search-space"></a>Definování prostoru pro hledání

Vyladěním parametrů můžete prozkoumat rozsah hodnot definovaných pro každý parametr.

Parametry mohou být diskrétní nebo souvislé a mají distribuci hodnot popsaných [výrazem parametru](/python/api/azureml-train-core/azureml.train.hyperdrive.parameter_expressions?preserve-view=true&view=azure-ml-py).

### <a name="discrete-hyperparameters"></a>Samostatné parametry 

Diskrétní parametry jsou zadány jako `choice` mezi diskrétní hodnoty. `choice` může být:

* jedna nebo více hodnot oddělených čárkami
* `range`objekt
* libovolný `list` objekt


```Python
    {
        "batch_size": choice(16, 32, 64, 128)
        "number_of_hidden_layers": choice(range(1,5))
    }
```

V tomto případě `batch_size` jedna z hodnot [16, 32, 64, 128] a `number_of_hidden_layers` přebírá jednu z hodnot [1, 2, 3, 4].

Pomocí distribuce lze také zadat následující pokročilé samostatné parametry:

* `quniform(low, high, q)` -Vrátí hodnotu, třeba Round (Uniform (nízká, High)/q) * q.
* `qloguniform(low, high, q)` -Vrátí hodnotu jako Round (EXP (Uniform (nízká, High)/q) * q.
* `qnormal(mu, sigma, q)` -Vrátí hodnotu, jako je Round (normální (mu, Sigma)/q) * q.
* `qlognormal(mu, sigma, q)` -Vrátí hodnotu, jako je Round (EXP (normální (mu, Sigma))/q) * q

### <a name="continuous-hyperparameters"></a>Souvislé parametry 

Průběžné parametry jsou zadány jako distribuce pro souvislý rozsah hodnot:

* `uniform(low, high)` -Vrátí hodnotu rovnoměrně distribuovanou mezi dolní a vysokou hodnotou.
* `loguniform(low, high)` -Vrátí hodnotu vykreslenou podle EXP (Uniform (Low, High)), aby byl logaritmus návratové hodnoty stejnoměrně distribuován.
* `normal(mu, sigma)` – Vrátí reálnou hodnotu, která je normálně distribuována se středníky a směrodatnou odchylkou Sigma.
* `lognormal(mu, sigma)` -Vrátí hodnotu vykreslenou podle EXP (Normal (mu, Sigma)), aby byl standardně distribuován logaritmus návratové hodnoty.

Příklad definice prostoru parametrů:

```Python
    {    
        "learning_rate": normal(10, 3),
        "keep_probability": uniform(0.05, 0.1)
    }
```

Tento kód definuje prostor pro hledání se dvěma parametry – `learning_rate` a `keep_probability` . `learning_rate` má normální distribuci s střední hodnotou 10 a směrodatnou odchylku 3. `keep_probability` má jednotnou distribuci s minimální hodnotou 0,05 a maximální hodnotou 0,1.

### <a name="sampling-the-hyperparameter-space"></a>Vzorkování prostoru s parametrem

Zadejte metodu vzorkování parametrů, která se má použít v prostoru s parametrem. Azure Machine Learning podporuje následující metody:

* Náhodný výběr
* Vzorkování mřížky
* Bayesovské vzorkování

#### <a name="random-sampling"></a>Náhodný výběr

[Náhodné vzorkování](/python/api/azureml-train-core/azureml.train.hyperdrive.randomparametersampling?preserve-view=true&view=azure-ml-py) podporuje diskrétní a souvislé parametry. Podporuje předčasné ukončení běhu s nízkým výkonem. Někteří uživatelé provedou počáteční vyhledávání s náhodným vzorkováním a pak upřesníte prostor pro hledání za účelem zlepšení výsledků.

V případě náhodného vzorkování jsou hodnoty s parametry náhodně vybrány z definovaného prostoru hledání. 

```Python
from azureml.train.hyperdrive import RandomParameterSampling
from azureml.train.hyperdrive import normal, uniform, choice
param_sampling = RandomParameterSampling( {
        "learning_rate": normal(10, 3),
        "keep_probability": uniform(0.05, 0.1),
        "batch_size": choice(16, 32, 64, 128)
    }
)
```

#### <a name="grid-sampling"></a>Vzorkování mřížky

[Vzorkování mřížky](/python/api/azureml-train-core/azureml.train.hyperdrive.gridparametersampling?preserve-view=true&view=azure-ml-py) podporuje samostatné parametry. Vzorkování mřížky použijte v případě, že můžete rozpočtovat do vyčerpání prostoru pro hledání. Podporuje předčasné ukončení běhu s nízkým výkonem.

Provede jednoduchou hledání v mřížce přes všechny možné hodnoty. Vzorkování mřížky lze použít pouze s `choice` parametry. Například následující místo má šest vzorků:

```Python
from azureml.train.hyperdrive import GridParameterSampling
from azureml.train.hyperdrive import choice
param_sampling = GridParameterSampling( {
        "num_hidden_layers": choice(1, 2, 3),
        "batch_size": choice(16, 32)
    }
)
```

#### <a name="bayesian-sampling"></a>Bayesovské vzorkování

[Vzorkování bayesovského rozhodování](/python/api/azureml-train-core/azureml.train.hyperdrive.bayesianparametersampling?preserve-view=true&view=azure-ml-py) je založené na algoritmu optimalizace bayesovského rozhodování. Vybere ukázky založené na tom, jak se prováděly předchozí ukázky, aby nové ukázky vylepšily primární metriku.

Vzorkování bayesovského rozhodování se doporučuje, pokud máte dostatečný rozpočet na prozkoumání prostoru s parametrem. Pro dosažení nejlepších výsledků doporučujeme maximální počet spuštění, který je větší nebo roven 20, počtu vyladěných parametrů. 

Počet souběžných spuštění má vliv na efektivitu procesu optimalizace. Menší počet souběžných spuštění může vést k lepší konvergenci vzorkování, protože menší stupeň paralelismu zvyšuje počet spuštění s výhodou dříve dokončených spuštění.

Vzorkování bayesovského rozhodování podporuje jenom `choice` `uniform` distribuce, a v `quniform` prostoru pro hledání.

```Python
from azureml.train.hyperdrive import BayesianParameterSampling
from azureml.train.hyperdrive import uniform, choice
param_sampling = BayesianParameterSampling( {
        "learning_rate": uniform(0.05, 0.1),
        "batch_size": choice(16, 32, 64, 128)
    }
)
```



## <a name="specify-primary-metric"></a><a name="specify-primary-metric-to-optimize"></a> Zadat primární metriku

Určete [primární metriku](/python/api/azureml-train-core/azureml.train.hyperdrive.primarymetricgoal?preserve-view=true&view=azure-ml-py) , která má optimalizovat laděním parametrů. U každého výukového běhu se vyhodnocuje primární metrika. Zásady prvotního ukončení používají primární metriku k identifikaci spuštění s nízkým výkonem.

Zadejte následující atributy pro primární metriku:

* `primary_metric_name`: Název primární metriky musí přesně odpovídat názvu metriky zaznamenané školicím skriptem.
* `primary_metric_goal`: Může být buď `PrimaryMetricGoal.MAXIMIZE` nebo `PrimaryMetricGoal.MINIMIZE` , a určuje, zda bude primární metrika maximalizována nebo minimalizována při vyhodnocování spuštění. 

```Python
primary_metric_name="accuracy",
primary_metric_goal=PrimaryMetricGoal.MAXIMIZE
```

Tato ukázka maximalizuje přesnost.

### <a name="log-metrics-for-hyperparameter-tuning"></a><a name="log-metrics-for-hyperparameter-tuning"></a>Metriky protokolu pro ladění parametrů

Školicí skript pro váš model **musí** během školení modelu protokolovat primární metriku, aby k nim Hyperdrive měl přístup pro ladění pomocí parametrů.

Zaprotokolujte primární metriku ve školicím skriptu pomocí následujícího ukázkového fragmentu kódu:

```Python
from azureml.core.run import Run
run_logger = Run.get_context()
run_logger.log("accuracy", float(val_accuracy))
```

Školicí skript vypočítá `val_accuracy` a zaznamená jako přesnost primární metriky. Pokaždé, když se metrika zaznamená, obdrží ji služba pro ladění pomocí parametrů. Můžete určit četnost generování sestav.

Další informace o hodnotách protokolování v běhu školicích kurzů najdete v tématu [Povolení protokolování v běhu školicích kurzů Azure ml](how-to-track-experiments.md).

## <a name="specify-early-termination-policy"></a><a name="early-termination"></a> Zadat zásady prvotního ukončení

Automatické ukončení nedostatečně výkonného spuštění se zásadami prvotního ukončení. Předčasné ukončení vylepšuje výpočetní efektivitu.

Můžete nakonfigurovat následující parametry, které řídí, kdy se zásada použije:

* `evaluation_interval`: frekvence použití zásad. Pokaždé, když školicí skript zaznamená primární metriku, počítá se jako jeden interval. V případě `evaluation_interval` 1 bude zásada použita pokaždé, když skript školení ohlásí primární metriku. U `evaluation_interval` 2 se zásada použije kdykoli. Pokud není zadán, `evaluation_interval` je ve výchozím nastavení nastaveno na hodnotu 1.
* `delay_evaluation`: zpoždění prvního vyhodnocení zásad pro zadaný počet intervalů. Toto je volitelný parametr, který brání předčasnému ukončení školicích běhů tím, že umožňuje spuštění všech konfigurací v případě minimálního počtu intervalů. Když se tato zásada zadá, uplatní se všechny násobky evaluation_interval, které jsou větší nebo rovny delay_evaluation.

Azure Machine Learning podporuje následující zásady prvotního ukončení:
* [Zásady Bandit](#bandit-policy)
* [Medián – zastavení zásad](#median-stopping-policy)
* [Zásada výběru zkrácení](#truncation-selection-policy)
* [Žádné zásady ukončení](#no-termination-policy-default)


### <a name="bandit-policy"></a>Zásady Bandit

[Zásada Bandit](/python/api/azureml-train-core/azureml.train.hyperdrive.banditpolicy?preserve-view=true&view=azure-ml-py#&preserve-view=truedefinition) je založená na rozsahu časové rezervy/rezervy a intervalu vyhodnocení. Bandit ukončí běh, kde primární metrika není v rámci zadané hodnoty faktoru rezervy nebo rezervy v porovnání s nejlepším výkonem.

> [!NOTE]
> Vzorkování bayesovského rozhodování nepodporuje předčasné ukončení. Při použití vzorkování bayesovského rozhodování nastavte `early_termination_policy = None` .

Zadejte následující parametry konfigurace:

* `slack_factor` nebo `slack_amount` : časová rezerva povolená s ohledem na nejlepší provádění školicích běhů. `slack_factor` Určuje povolenou časovou rezervu jako poměr. `slack_amount` Určuje povolenou časovou rezervu jako absolutní hodnotu namísto poměru.

    Představte si třeba zásady Bandit použité v intervalu 10. Předpokládejme, že nejlepší provádění v intervalu 10 oznámilo primární metriku je 0,8 s cílem maximalizovat primární metriku. Pokud zásada specifikuje `slack_factor` 0,2, budou všechny školicí běhy, jejichž nejlepší metrika v intervalu 10 je nižší než 0,66 (0,8/(1 + `slack_factor` )), budou ukončeny.
* `evaluation_interval`: (volitelné) frekvence použití zásad
* `delay_evaluation`: (volitelné) odloží první vyhodnocení zásad pro zadaný počet intervalů.


```Python
from azureml.train.hyperdrive import BanditPolicy
early_termination_policy = BanditPolicy(slack_factor = 0.1, evaluation_interval=1, delay_evaluation=5)
```

V tomto příkladu se zásada prvotního ukončení používá v každém intervalu při hlášení metrik počínaje v intervalu vyhodnocení 5. Jakékoli spuštění, jejichž nejlepší metrika je menší než (1/(1 + 0,1) nebo 91% nejlepšího spuštění, se ukončí.

### <a name="median-stopping-policy"></a>Medián – zastavení zásad

[Medián při zastavení](/python/api/azureml-train-core/azureml.train.hyperdrive.medianstoppingpolicy?preserve-view=true&view=azure-ml-py) je zásada předčasného ukončení založená na běžících průměrech primárních metrik, které běhy oznámily. Tato zásada vypočítá průměry ve všech školicích běhůch a ukončí běh s primárními hodnotami metriky, které jsou horší než střední hodnota průměrů.

Tato zásada přijímá následující konfigurační parametry:
* `evaluation_interval`: frekvence použití zásad (volitelného parametru).
* `delay_evaluation`: zpoždění prvního vyhodnocení zásad pro zadaný počet intervalů (volitelný parametr).


```Python
from azureml.train.hyperdrive import MedianStoppingPolicy
early_termination_policy = MedianStoppingPolicy(evaluation_interval=1, delay_evaluation=5)
```

V tomto příkladu se zásady prvotního ukončení aplikují v intervalu vyhodnocení 5 v každém intervalu. Běh se ukončí v intervalu 5, pokud je jeho nejlepší primární metrika horší než střední hodnota průměrných běhů v intervalech 1:5 v rámci všech školicích běhů.

### <a name="truncation-selection-policy"></a>Zásada výběru zkrácení

[Výběr zkrácení](/python/api/azureml-train-core/azureml.train.hyperdrive.truncationselectionpolicy?preserve-view=true&view=azure-ml-py) zruší procento nejnižších běhů běhu v každém intervalu vyhodnocení. Běhy se porovnávají s použitím primární metriky. 

Tato zásada přijímá následující konfigurační parametry:

* `truncation_percentage`: procento nejnižších běhů běhu pro ukončení v každém intervalu vyhodnocení. Celočíselná hodnota v rozmezí od 1 do 99.
* `evaluation_interval`: (volitelné) frekvence použití zásad
* `delay_evaluation`: (volitelné) odloží první vyhodnocení zásad pro zadaný počet intervalů.


```Python
from azureml.train.hyperdrive import TruncationSelectionPolicy
early_termination_policy = TruncationSelectionPolicy(evaluation_interval=1, truncation_percentage=20, delay_evaluation=5)
```

V tomto příkladu se zásady prvotního ukončení aplikují v intervalu vyhodnocení 5 v každém intervalu. Spuštění skončí v intervalu 5, pokud je jeho výkon v intervalu 5 v nejnižší 20% výkonu všech běhů v intervalu 5.

### <a name="no-termination-policy-default"></a>Žádné zásady ukončení (výchozí)

Pokud není zadána žádná zásada, bude služba ladění u parametrů nastavena na dokončení všech školicích běhů.

```Python
policy=None
```

### <a name="picking-an-early-termination-policy"></a>Vybírání zásad prvotního ukončení

* Pro konzervativní zásadu, která poskytuje úspory bez ukončení přislíbení úloh, zvažte střední zastavování zásad s `evaluation_interval` 1 a `delay_evaluation` 5. Jedná se o konzervativní nastavení, které může poskytovat přibližně 25 až 35% úspory bez ztráty primární metriky (na základě našich zkušebních údajů).
* Pro efektivnější úspory použijte zásady Bandit s menší povolenou časovou rezervou nebo zkrácenými zásadami výběru s větším procentem zkrácení.

## <a name="allocate-resources"></a>Přidělit prostředky

Určete svůj rozpočet prostředků zadáním maximálního počtu běhů cvičení.

* `max_total_runs`: Maximální počet běhů cvičení. Musí být celé číslo v rozmezí od 1 do 1000.
* `max_duration_minutes`: (volitelné) maximální doba trvání experimentu ladění parametrů v řádu minut. Spustí se po zrušení této doby.

>[!NOTE] 
>Pokud `max_total_runs` `max_duration_minutes` jsou zadány oba a, experiment ladění s parametry se ukončí při dosažení prvního z těchto dvou mezních hodnot.

Navíc můžete zadat maximální počet běhů cvičení, které mají být spuštěny souběžně během vyhledávání laděním parametrů.

* `max_concurrent_runs`: (volitelné) maximální počet spuštění, který může běžet souběžně. Pokud tento parametr nezadáte, spustí se všechny spuštění paralelně. Je-li tento parametr zadán, musí být celé číslo v rozmezí od 1 do 100.

>[!NOTE] 
>Počet souběžných spuštění je gated u prostředků, které jsou k dispozici v zadaném cíli výpočtu. Ujistěte se, že cíl výpočtů má dostupné prostředky pro požadovanou souběžnost.

```Python
max_total_runs=20,
max_concurrent_runs=4
```

Tento kód nakonfiguruje experiment vyladěním parametrů, aby používal maximálně 20 spuštění celkem, současně se čtyřmi konfiguracemi.

## <a name="configure-experiment"></a>Konfigurovat experiment

Chcete-li nakonfigurovat experiment s [optimalizací parametrů](/python/api/azureml-train-core/azureml.train.hyperdrive.hyperdriverunconfig?preserve-view=true&view=azure-ml-py) , zadejte následující:
* Definovaný prostor pro hledání parametrů
* Zásady předčasného ukončení
* Primární metrika
* Nastavení přidělení prostředků
* ScriptRunConfig `src`

ScriptRunConfig je školicí skript, který se spustí s ukázkovými parametry. Definuje prostředky na úlohu (jeden nebo víc uzlů) a výpočetní cíl, který se má použít.

> [!NOTE]
>Cíl výpočtů zadaný v nástroji `src` musí mít dostatek prostředků pro uspokojení vaší úrovně souběžnosti. Další informace o ScriptRunConfig najdete v tématu [Konfigurace školicích běhů](how-to-set-up-training-targets.md).

Konfigurace experimentu ladění vašich parametrů:

```Python
from azureml.train.hyperdrive import HyperDriveConfig
hd_config = HyperDriveConfig(run_config=src,
                             hyperparameter_sampling=param_sampling,
                             policy=early_termination_policy,
                             primary_metric_name="accuracy",
                             primary_metric_goal=PrimaryMetricGoal.MAXIMIZE,
                             max_total_runs=100,
                             max_concurrent_runs=4)
```

## <a name="submit-experiment"></a>Odeslat experiment

Po definování konfigurace ladění vašich parametrů [odešlete experiment](/python/api/azureml-core/azureml.core.experiment%28class%29?preserve-view=true&view=azure-ml-py#&preserve-view=truesubmit-config--tags-none----kwargs-):

```Python
from azureml.core.experiment import Experiment
experiment = Experiment(workspace, experiment_name)
hyperdrive_run = experiment.submit(hd_config)
```

## <a name="warm-start-your-hyperparameter-tuning-experiment-optional"></a>Zahajte svůj experiment s optimalizací parametrů (volitelné)

Hledání nejlepších hodnot parametrů pro váš model může být iterativní proces. Pokud chcete zrychlit ladění parametrů, můžete znovu použít znalostní bázi z pěti předchozích běhů.


Zahřívání od začátku se zpracovává jinak v závislosti na metodě vzorkování:
- **Vzorkování bayesovského rozhodování**: pro výběr nových ukázek a pro zlepšení primární metriky se používají zkušební verze předchozího běhu jako předchozí znalosti.
- **Náhodný odběr vzorkování** nebo **mřížky**: předčasné ukončení používá k určení nedostatečně výkonného spuštění znalosti z předchozích spuštění. 

Zadejte seznam nadřazených spuštění, ze kterých chcete začít zahřívání.

```Python
from azureml.train.hyperdrive import HyperDriveRun

warmstart_parent_1 = HyperDriveRun(experiment, "warmstart_parent_run_ID_1")
warmstart_parent_2 = HyperDriveRun(experiment, "warmstart_parent_run_ID_2")
warmstart_parents_to_resume_from = [warmstart_parent_1, warmstart_parent_2]
```

Pokud se zruší experimentování s laděním parametrů, můžete pokračovat v školicích běhůch z posledního kontrolního bodu. Školicí skript však musí zpracovat logiku kontrolního bodu.

Školicí běh musí používat stejnou konfiguraci parametrů a připojit složky výstupy. Školicí skript musí souhlasit s `resume-from` argumentem, který obsahuje soubor kontrolního bodu nebo soubory modelu, ze kterých se má pokračovat v běhu školení. Můžete obnovit jednotlivé běhy školení pomocí následujícího fragmentu kódu:

```Python
from azureml.core.run import Run

resume_child_run_1 = Run(experiment, "resume_child_run_ID_1")
resume_child_run_2 = Run(experiment, "resume_child_run_ID_2")
child_runs_to_resume = [resume_child_run_1, resume_child_run_2]
```

Experiment ladění vašich parametrů můžete nakonfigurovat tak, aby začal začínat od předchozího experimentu, nebo obnovit jednotlivé školicí běhy pomocí volitelných parametrů `resume_from` a `resume_child_runs` v konfiguraci:

```Python
from azureml.train.hyperdrive import HyperDriveConfig

hd_config = HyperDriveConfig(run_config=src,
                             hyperparameter_sampling=param_sampling,
                             policy=early_termination_policy,
                             resume_from=warmstart_parents_to_resume_from,
                             resume_child_runs=child_runs_to_resume,
                             primary_metric_name="accuracy",
                             primary_metric_goal=PrimaryMetricGoal.MAXIMIZE,
                             max_total_runs=100,
                             max_concurrent_runs=4)
```

## <a name="visualize-experiment"></a>Vizualizace experimentu

Pomocí [widgetu poznámkového bloku](/python/api/azureml-widgets/azureml.widgets.rundetails?preserve-view=true&view=azure-ml-py) Vizualizujte průběh vašich školicích běhů. Následující fragment kódu vizualizuje všechna vaše ladění vašich parametrů na jednom místě v Jupyter poznámkovém bloku:

```Python
from azureml.widgets import RunDetails
RunDetails(hyperdrive_run).show()
```

Tento kód zobrazí tabulku s podrobnostmi o spuštěních školení pro jednotlivé konfigurace parametrů.

![Tabulka ladění předaných parametrů](./media/how-to-tune-hyperparameters/HyperparameterTuningTable.png)

Můžete také vizualizovat výkon každého spuštění jako školicí průběh. 

![vykreslení pro vyladění parametrů](./media/how-to-tune-hyperparameters/HyperparameterTuningPlot.png)

Můžete vizuálně identifikovat korelaci mezi výkonem a hodnotami jednotlivých parametrů, a to pomocí paralelního vykreslení souřadnic. 

[![paralelní souřadnice ladění parametrů](./media/how-to-tune-hyperparameters/HyperparameterTuningParallelCoordinates.png)](media/how-to-tune-hyperparameters/hyperparameter-tuning-parallel-coordinates-expanded.png)

Na webovém portálu Azure můžete také vizualizovat všechny spuštěné ladění vašich parametrů. Další informace o tom, jak zobrazit experiment na portálu, najdete v tématu [jak sledovat experimenty](how-to-monitor-view-training-logs.md#view-the-experiment-in-the-web-portal).

## <a name="find-the-best-model"></a>Najít nejlepší model

Po dokončení všech spuštění ladění parametrů se identifikují nejlepší provedení konfigurace a hodnot parametrů:

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

## <a name="sample-notebook"></a>Ukázkový Poznámkový blok
Další informace najdete v poznámkách k vlakovým parametrům-* v této složce:
* [Postupy: použití-AzureML/ml – rozhraní](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/ml-frameworks)

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Další kroky
* [Sledování experimentu](how-to-track-experiments.md)
* [Nasazení trained model](how-to-deploy-and-where.md)