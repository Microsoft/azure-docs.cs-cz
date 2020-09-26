---
title: Vyladění parametrů pro váš model
titleSuffix: Azure Machine Learning
description: Efektivní ladění parametrů pro model hloubkového učení a strojového učení pomocí Azure Machine Learning. Naučíte se, jak definovat místo pro hledání parametrů, určit primární metriku, která se optimalizuje, a brzké ukončení nedostatečně výkonného spuštění.
ms.author: swatig
author: swatig007
ms.reviewer: sgilley
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.date: 03/30/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python
ms.openlocfilehash: 44616d5d90f9c5c3a4f3abf8b8cf2128dc4f0585
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91333796"
---
# <a name="tune-hyperparameters-for-your-model-with-azure-machine-learning"></a>Vyladění parametrů pro model pomocí Azure Machine Learning


Efektivní ladění parametrů pro váš model pomocí Azure Machine Learning.  Ladění parametrů obsahuje následující kroky:

* Definice hledaného prostoru parametrů
* Určení primární metriky k optimalizaci  
* Zadat kritéria prvotního ukončení pro nedostatečně výkonné běhy
* Přidělit prostředky pro optimalizaci parametrů
* Spustit experiment s výše uvedenou konfigurací
* Vizualizace školicích běhů
* Vyberte pro svůj model nejlepší provádění konfigurace.

## <a name="what-are-hyperparameters"></a>Co jsou to parametry?

Vlastní parametry jsou seřiditelné parametry, které jste zvolili pro výuku modelu, který řídí samotný školicí proces. Pokud například chcete vytvořit rozsáhlou neuronové síť, určíte počet skrytých vrstev v síti a počet uzlů v každé vrstvě před tím, než provedete model. Tyto hodnoty obvykle zůstávají během školicího procesu konstantní.

Ve scénářích s hloubkovým učením a strojovým učením závisí výkon modelu silně na vybraných hodnotách parametrů. Cílem průzkumu s parametry je hledání v různých konfiguracích parametrů a vyhledání konfigurace, která má za následek nejlepší výkon. Obvykle je proces zkoumání parametrů painstakingly ručně, vzhledem k tom, že hledaný prostor je obrovské a vyhodnocení každé konfigurace může být nákladné.

Azure Machine Learning vám umožní efektivně automatizovat interaktivní zkoumání parametrů, což vám ušetří značný čas a prostředky. Zadejte rozsah hodnot parametrů a maximální počet spuštění školení. Systém pak automaticky spustí více souběžných spuštění s různými konfiguracemi parametrů a najde konfiguraci, která má za následek nejlepší výkon, měřeno metrikou, kterou zvolíte. Nedostatečně výkonné školicí běhy se automaticky ukončí, čímž se sníží plýtvání výpočetních prostředků. Místo toho se používají tyto prostředky k prozkoumávání jiných konfigurací parametrů.


## <a name="define-search-space"></a>Definovat místo pro hledání

Automatické vyladění podparametrů tím, že prozkoumáte rozsah hodnot definovaných pro každý parametr.

### <a name="types-of-hyperparameters"></a>Typy parametrů

Každý parametr může být buď diskrétní, nebo souvislý a má distribuci hodnot popsaných [výrazem parametru](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.hyperdrive.parameter_expressions?view=azure-ml-py&preserve-view=true).

#### <a name="discrete-hyperparameters"></a>Samostatné parametry 

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

V takovém případě `batch_size` převezme jednu z hodnot [16, 32, 64, 128] a `number_of_hidden_layers` převezme jednu z hodnot [1, 2, 3, 4].

Rozšířené samostatné parametry lze také určit pomocí distribuce. Podporují se tyto distribuce:

* `quniform(low, high, q)` -Vrátí hodnotu, třeba Round (Uniform (nízká, High)/q) * q.
* `qloguniform(low, high, q)` -Vrátí hodnotu jako Round (EXP (Uniform (nízká, High)/q) * q.
* `qnormal(mu, sigma, q)` -Vrátí hodnotu, jako je Round (normální (mu, Sigma)/q) * q.
* `qlognormal(mu, sigma, q)` -Vrátí hodnotu, jako je Round (EXP (normální (mu, Sigma))/q) * q

#### <a name="continuous-hyperparameters"></a>Souvislé parametry 

Průběžné parametry jsou zadány jako distribuce přes souvislý rozsah hodnot. Mezi podporované distribuce patří:

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

Můžete také zadat metodu vzorkování parametrů, která se má použít v definici prostoru pro parametry. Azure Machine Learning podporuje náhodné vzorkování, vzorkování mřížky a vzorkování bayesovského rozhodování.

#### <a name="picking-a-sampling-method"></a>Výběr metody vzorkování

* Vzorkování mřížky lze použít v případě, že prostor vlastního parametru může být definován jako volba mezi diskrétními hodnotami a pokud máte dostatečný rozpočet pro vyčerpání všech hodnot v definovaném hledaném prostoru. Kromě toho může použití automatizovaného předčasného ukončení nedostatečně výkonného spuštění, což snižuje počet nevracení prostředků.
* Náhodné vzorkování umožňuje, aby prostor s parametrem zahrnoval jak diskrétní, tak i plynulé parametry. V praxi přináší ve většině případů dobrý výsledek a také umožňuje použití automatizovaného předčasného ukončení nedostatečně výkonného spuštění. Někteří uživatelé provádějí počáteční vyhledávání pomocí náhodného vzorkování a pak iterativním vylepšit místo hledání za účelem zlepšení výsledků.
* Bayesovského rozhodování vzorkování využívá při volbě hodnot parametrů informace o předchozích ukázkách a efektivně se snaží vylepšit hlášené primární metriky. Bayesovského rozhodování vzorkování se doporučuje, když máte dostatečný rozpočet na prozkoumání prostoru s jedním parametrem – pro dosažení nejlepších výsledků pomocí vzorkování bayesovského rozhodování doporučujeme použít maximální počet spuštění, který je větší nebo roven 20, kolikrát se vyladěné parametry. Všimněte si, že vzorkování bayesovského rozhodování aktuálně nepodporuje žádné zásady předčasného ukončení.

#### <a name="random-sampling"></a>Náhodný výběr

V případě náhodného vzorkování jsou hodnoty s parametry náhodně vybrány z definovaného prostoru hledání. [Náhodné vzorkování](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.hyperdrive.randomparametersampling?view=azure-ml-py&preserve-view=true) umožňuje, aby místo hledání zahrnovalo diskrétní i plynulé parametry.

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

[Vzorkování mřížky](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.hyperdrive.gridparametersampling?view=azure-ml-py&preserve-view=true) provede jednoduchou mřížku pro vyhledávání všech vhodných hodnot v definovaném prostoru hledání. Dá se použít jenom s parametry určenými pomocí `choice` . Například následující místo má celkem šest vzorků:

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

[Vzorkování bayesovského rozhodování](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.hyperdrive.bayesianparametersampling?view=azure-ml-py&preserve-view=true) je založené na algoritmu optimalizace bayesovského rozhodování a umožňuje inteligentní výběry hodnot parametrů na další. Vybere ukázku na základě toho, jak byly provedeny předchozí ukázky, takže Nová ukázka vylepšuje hlášené primární metriku.

Při použití vzorkování bayesovského rozhodování má počet souběžných spuštění vliv na efektivitu procesu optimalizace. Obvykle může menší počet souběžných běhů vést k lepší konvergenci vzorkování, protože menší stupeň paralelismu zvyšuje počet spuštění s výhodou dříve dokončených spuštění.

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

> [!NOTE]
> Bayesovského rozhodování vzorkování nepodporuje žádné zásady prvotního ukončení (viz [určení zásady prvotního ukončení](#early-termination)). Při použití vzorkování parametrů bayesovského rozhodování, nastavení `early_termination_policy = None` nebo ponechání `early_termination_policy` parametru.

## <a name="specify-primary-metric"></a><a name="specify-primary-metric-to-optimize"></a> Zadat primární metriku

Určete [primární metriku](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.hyperdrive.primarymetricgoal?view=azure-ml-py&preserve-view=true) , kterou má experiment pro ladění parametrů optimalizovat. U každého výukového běhu se vyhodnocuje primární metrika. Nedostatečně výkonné běhy (kde primární metrika nesplňuje kritéria nastavená zásadami prvotního ukončení) se ukončí. Kromě primárního názvu metriky určíte také cíl optimalizace – zda má být primární metrika maximalizována nebo minimalizována.

* `primary_metric_name`: Název primární metriky, která se má optimalizovat. Název primární metriky musí přesně odpovídat názvu metriky zaznamenané školicím skriptem. Další informace najdete v tématu [metriky protokolu pro ladění](#log-metrics-for-hyperparameter-tuning)předaných parametrů.
* `primary_metric_goal`: Může být buď `PrimaryMetricGoal.MAXIMIZE` nebo `PrimaryMetricGoal.MINIMIZE` , a určuje, zda bude primární metrika maximalizována nebo minimalizována při vyhodnocování spuštění. 

```Python
primary_metric_name="accuracy",
primary_metric_goal=PrimaryMetricGoal.MAXIMIZE
```

Optimalizujte spuštění a maximalizujte "přesnost".  Nezapomeňte tuto hodnotu ve školicím skriptu zaprotokolovat.

### <a name="log-metrics-for-hyperparameter-tuning"></a><a name="log-metrics-for-hyperparameter-tuning"></a>Metriky protokolu pro ladění parametrů

Školicí skript pro váš model musí během školení modelu protokolovat relevantní metriky. Když nakonfigurujete ladění parametrů, zadáte primární metriku, která se má použít pro vyhodnocení výkonu spuštění. (Viz [Určení primární metriky k optimalizaci](#specify-primary-metric-to-optimize).)  Ve školicím skriptu je nutné tuto metriku zaprotokolovat, aby byla dostupná pro proces ladění parametrů.

Zaprotokolujte tuto metriku ve školicím skriptu pomocí následujícího ukázkového fragmentu kódu:

```Python
from azureml.core.run import Run
run_logger = Run.get_context()
run_logger.log("accuracy", float(val_accuracy))
```

Školicí skript vypočítá `val_accuracy` a zaprotokoluje ho jako přesnost, která se používá jako primární metrika. Pokaždé, když se zaprotokoluje metrika, obdrží služba ladění pomocí parametrů. Aby bylo možné určit, jak často se má tato metrika hlásit, je to vývojář modelů.

## <a name="specify-early-termination-policy"></a><a name="early-termination"></a> Zadat zásady prvotního ukončení

Vykonání nedostatečně výkonného spuštění automaticky pomocí zásad předčasného ukončení. Ukončení omezuje plýtvání prostředky a místo toho využívá tyto prostředky k prozkoumávání jiných konfigurací parametrů.

Pokud používáte zásady prvotního ukončení, můžete nakonfigurovat následující parametry, které řídí, kdy se zásada použije:

* `evaluation_interval`: frekvence použití zásad. Pokaždé, když školicí skript zaznamená primární metriku, počítá se jako jeden interval. A proto se `evaluation_interval` zásada bude uplatňovat vždy, když skript pro školení oznámí primární metriku. A `evaluation_interval` 2 bude zásadu používat pokaždé, když skript pro školení nahlásí primární metriku. Pokud není zadán, `evaluation_interval` je ve výchozím nastavení nastaveno na hodnotu 1.
* `delay_evaluation`: zpoždění prvního vyhodnocení zásad pro zadaný počet intervalů. Je to volitelný parametr, který umožňuje spuštění všech konfigurací v počátečním minimálním počtu intervalů, což vyloučí předčasné ukončení školicích běhů. Když se tato zásada zadá, uplatní se všechny násobky evaluation_interval, které jsou větší nebo rovny delay_evaluation.

Azure Machine Learning podporuje následující zásady prvotního ukončení.

### <a name="bandit-policy"></a>Zásady Bandit

[Bandit](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.hyperdrive.banditpolicy?view=azure-ml-py&preserve-view=true#&preserve-view=truedefinition) je zásada ukončení založená na rozsahu časové rezervy/rezervy a intervalu vyhodnocení. Zásada zpočátku ukončí všechna spuštění, kde primární metrika není v rámci zadané hodnoty faktoru rezervy nebo rezervy, s ohledem na to, co nejlépe provedete školicí běh. Má následující konfigurační parametry:

* `slack_factor` nebo `slack_amount` : časová rezerva povolená s ohledem na nejlepší provádění školicích běhů. `slack_factor` Určuje povolenou časovou rezervu jako poměr. `slack_amount` Určuje povolenou časovou rezervu jako absolutní hodnotu namísto poměru.

    Představte si třeba zásadu Bandit, která se použije v intervalu 10. Předpokládejme, že nejlepší provádění v intervalu 10 oznámilo primární metriku 0,8 s cílem maximalizovat primární metriku. Pokud byla zásada zadána s `slack_factor` 0,2, bude ukončeno jakékoli školicí běhy, jejichž nejlepší metrika v intervalu 10 je nižší než 0,66 (0,8/(1 + `slack_factor` )). Pokud místo toho byla zásada zadána s `slack_amount` 0,2, jakékoli školicí běhy, jejichž nejlepší metrika v intervalu 10 je nižší než 0,6 (0,8-), `slack_amount` se ukončí.
* `evaluation_interval`: frekvence použití zásad (volitelného parametru).
* `delay_evaluation`: zpoždění prvního vyhodnocení zásad pro zadaný počet intervalů (volitelný parametr).


```Python
from azureml.train.hyperdrive import BanditPolicy
early_termination_policy = BanditPolicy(slack_factor = 0.1, evaluation_interval=1, delay_evaluation=5)
```

V tomto příkladu se zásada prvotního ukončení používá v každém intervalu při hlášení metrik počínaje v intervalu vyhodnocení 5. Jakékoli spuštění, jejichž nejlepší metrika je menší než (1/(1 + 0,1) nebo 91% nejlepšího spuštění, se ukončí.

### <a name="median-stopping-policy"></a>Medián – zastavení zásad

[Medián při zastavení](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.hyperdrive.medianstoppingpolicy?view=azure-ml-py&preserve-view=true) je zásada předčasného ukončení založená na běžících průměrech primárních metrik, které běhy oznámily. Tato zásada vypočítá průměry ve všech školicích běhůch a ukončí běh, jejichž výkon je horší než střední hodnota běžících průměrů. Tato zásada přijímá následující konfigurační parametry:
* `evaluation_interval`: frekvence použití zásad (volitelného parametru).
* `delay_evaluation`: zpoždění prvního vyhodnocení zásad pro zadaný počet intervalů (volitelný parametr).


```Python
from azureml.train.hyperdrive import MedianStoppingPolicy
early_termination_policy = MedianStoppingPolicy(evaluation_interval=1, delay_evaluation=5)
```

V tomto příkladu se zásady prvotního ukončení aplikují v intervalu vyhodnocení 5 v každém intervalu. Spuštění bude ukončeno v intervalu 5, pokud je nejvyšší primární metrika horší než střední hodnota průměrných běhů v intervalech 1:5 v rámci všech školicích běhů.

### <a name="truncation-selection-policy"></a>Zásada výběru zkrácení

[Zkrácené výběr](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.hyperdrive.truncationselectionpolicy?view=azure-ml-py&preserve-view=true) zruší v každém intervalu vyhodnocení dané procento nejnižší prováděné běhy. Běhy se porovnávají na základě jejich výkonu na primární metrikě a nejnižší X% se ukončí. Má následující konfigurační parametry:

* `truncation_percentage`: procento nejnižších běhů běhu pro ukončení v každém intervalu vyhodnocení. Zadejte celočíselnou hodnotu mezi 1 a 99.
* `evaluation_interval`: frekvence použití zásad (volitelného parametru).
* `delay_evaluation`: zpoždění prvního vyhodnocení zásad pro zadaný počet intervalů (volitelný parametr).


```Python
from azureml.train.hyperdrive import TruncationSelectionPolicy
early_termination_policy = TruncationSelectionPolicy(evaluation_interval=1, truncation_percentage=20, delay_evaluation=5)
```

V tomto příkladu se zásady prvotního ukončení aplikují v intervalu vyhodnocení 5 v každém intervalu. Spuštění bude ukončeno v intervalu 5, pokud je jeho výkon v intervalu 5 v nejnižší 20% výkonu všech běhů v intervalu 5.

### <a name="no-termination-policy"></a>Žádné zásady ukončení

Pokud chcete, aby se všechny školicí běhy spouštěly až do dokončení, nastavte zásady na žádné. To bude mít vliv na neuplatnění zásad předčasného ukončení.

```Python
policy=None
```

### <a name="default-policy"></a>Výchozí zásady

Pokud není zadána žádná zásada, bude služba ladění u parametrů nastavena na dokončení všech školicích běhů.

### <a name="picking-an-early-termination-policy"></a>Vybírání zásad prvotního ukončení

* Pokud hledáte konzervativní zásadu, která poskytuje úspory, aniž byste museli ukončit přislíbené úlohy, můžete použít medián zastavení zásady s `evaluation_interval` 1 a `delay_evaluation` 5. Jedná se o konzervativní nastavení, které může poskytovat přibližně 25 až 35% úspory bez ztráty primární metriky (na základě našich zkušebních údajů).
* Pokud hledáte výkonnější úspory od předčasného ukončení, můžete buď použít zásady Bandit s přísnější (menší) povolenou časovou rezervou, nebo zkrátit výběr, s větším procentem zkrácení.

## <a name="allocate-resources"></a>Přidělit prostředky

Určením maximálního celkového počtu běhů cvičení můžete řídit svůj rozpočet prostředku pro experimentování s optimalizací parametrů.  Volitelně můžete zadat maximální dobu, po kterou experiment vyladění vašich parametrů.

* `max_total_runs`: Maximální celkový počet běhů cvičení, které se vytvoří. Horní mez – v případě, že je prostor pro parametr konečný a má méně vzorků, může být například méně spuštění. Hodnota musí být číslo mezi 1 a 1000.
* `max_duration_minutes`: Maximální doba v minutách experimentu ladění parametrů. Parametr je nepovinný, a pokud je přítomen, všechna spuštění, která by byla spuštěna po této době, se automaticky zruší.

>[!NOTE] 
>Pokud `max_total_runs` `max_duration_minutes` jsou zadány oba a, experiment ladění s parametry se ukončí při dosažení prvního z těchto dvou mezních hodnot.

Navíc můžete zadat maximální počet běhů cvičení, které mají být spuštěny souběžně během vyhledávání laděním parametrů.

* `max_concurrent_runs`: Maximální počet spuštění souběžně v daném okamžiku. Pokud není zadaný, `max_total_runs` spustí se paralelně. Je-li tento parametr zadán, musí být číslo v rozmezí od 1 do 100.

>[!NOTE] 
>Počet souběžných spuštění je gated u prostředků, které jsou k dispozici v zadaném cíli výpočtu. Proto je potřeba zajistit, aby cíl výpočetních prostředků měl dostupné prostředky pro požadovanou souběžnost.

Přidělit prostředky pro ladění předaných parametrů:

```Python
max_total_runs=20,
max_concurrent_runs=4
```

Tento kód nakonfiguruje experiment vyladěním parametrů, aby používal maximálně 20 spuštění celkem, současně se čtyřmi konfiguracemi.

## <a name="configure-experiment"></a>Konfigurovat experiment

[Nakonfigurujte experiment s optimalizací parametrů](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.hyperdrive.hyperdriverunconfig?view=azure-ml-py&preserve-view=true) pomocí definovaného prostoru pro hledání parametrů, zásad prvotního ukončení, primární metriky a přidělení prostředků z výše uvedených částí. Kromě toho zadejte ScriptRunConfig `src` pro běh, který se bude volat pomocí ukázkových parametrů. ScriptRunConfig definuje školicí skript, který se má spustit, prostředky na úlohu (jeden nebo víc uzlů) a výpočetní cíl, který se má použít. Vzhledem k tomu, že je souběžnost pro experimenty s optimalizací parametrů na dostupných prostředcích ověřovaný, ujistěte se, že cíl výpočtů zadaný v nástroji `src` má dostatek prostředků pro požadovanou souběžnost. (Další informace o ScriptRunConfig najdete v tématu [Konfigurace školicích běhů](how-to-set-up-training-targets.md).)

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

Po definování konfigurace ladění vašich parametrů [odešlete experiment](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment%28class%29?view=azure-ml-py&preserve-view=true#&preserve-view=truesubmit-config--tags-none----kwargs-):

```Python
from azureml.core.experiment import Experiment
experiment = Experiment(workspace, experiment_name)
hyperdrive_run = experiment.submit(hd_config)
```

`experiment_name` je název, který jste přiřadili experimentu ladění vašich parametrů, a `workspace` je pracovní prostor, ve kterém chcete vytvořit experiment (Další informace o experimentech najdete v tématu [jak Azure Machine Learning funguje?](concept-azure-machine-learning-architecture.md))

## <a name="warm-start-your-hyperparameter-tuning-experiment-optional"></a>Zahajte svůj experiment s optimalizací parametrů (volitelné)

Často se při hledání nejlepších hodnot parametrů pro váš model může jednat o iterativní proces, který vyžaduje více spuštění ladění, které se naučí z předchozích spuštění ladění pomocí parametrů. Při dalším použití znalostí z předchozích běhů se urychlí proces ladění parametrů, čímž se sníží náklady na optimalizaci modelu a budou potenciálně vylepšit primární metrika výsledného modelu. Když zahájíte teplý experiment s bayesovského rozhodování vzorkováním, budou se zkušební verze z předchozího spuštění používat jako předchozí znalosti k inteligentnímu výběru nových ukázek, aby se zlepšila primární metrika. Kromě toho při použití náhodného vzorkování nebo vzorkování mřížky budou všechna rozhodnutí o předčasném ukončení využívat metriky z předchozích spuštění k určení nedostatečně výkonného běhu školicích kurzů. 

Azure Machine Learning vám umožní začít s laděním vašich parametrů pomocí znalostí od až 5 dříve dokončených nebo zrušených nadřazených spuštění ladění parametrů. Seznam nadřazených spuštění, která chcete zahříváním spustit, můžete určit pomocí tohoto fragmentu:

```Python
from azureml.train.hyperdrive import HyperDriveRun

warmstart_parent_1 = HyperDriveRun(experiment, "warmstart_parent_run_ID_1")
warmstart_parent_2 = HyperDriveRun(experiment, "warmstart_parent_run_ID_2")
warmstart_parents_to_resume_from = [warmstart_parent_1, warmstart_parent_2]
```

Kromě toho mohou nastat situace, kdy se jednotlivé školicí testy experimentu ladění parametrů zruší z důvodu omezení rozpočtu nebo selžou z důvodu jiných důvodů. Nyní je možné obnovit takové konkrétní školicí běhy z posledního kontrolního bodu (za předpokladu, že školicí skript zpracovává kontrolní body). Obnovení jednotlivých běhů cvičení bude používat stejnou konfiguraci parametrů a připojení složky výstupy použité pro daný běh. Školicí skript by měl souhlasit s `resume-from` argumentem, který obsahuje kontrolní body nebo soubory modelů, ze kterých se má pokračovat v běhu školení. Můžete obnovit jednotlivé běhy školení pomocí následujícího fragmentu kódu:

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

Sada Azure Machine Learning SDK poskytuje [pomůcku poznámkového bloku](https://docs.microsoft.com/python/api/azureml-widgets/azureml.widgets.rundetails?view=azure-ml-py&preserve-view=true) , která vizualizuje průběh vašich školicích běhů. Následující fragment kódu vizualizuje všechna vaše ladění vašich parametrů na jednom místě v Jupyter poznámkovém bloku:

```Python
from azureml.widgets import RunDetails
RunDetails(hyperdrive_run).show()
```

Tento kód zobrazí tabulku s podrobnostmi o spuštěních školení pro jednotlivé konfigurace parametrů.

![Tabulka ladění předaných parametrů](./media/how-to-tune-hyperparameters/HyperparameterTuningTable.png)

Můžete také vizualizovat výkon každého spuštění jako školicí průběh. 

![vykreslení pro vyladění parametrů](./media/how-to-tune-hyperparameters/HyperparameterTuningPlot.png)

Kromě toho můžete vizuálně identifikovat korelaci mezi výkonem a hodnotami jednotlivých parametrů pomocí paralelního vykreslení souřadnic. 

[![paralelní souřadnice ladění parametrů](./media/how-to-tune-hyperparameters/HyperparameterTuningParallelCoordinates.png)](media/how-to-tune-hyperparameters/hyperparameter-tuning-parallel-coordinates-expanded.png)

Všechny vaše optimalizační parametry můžete vizualizovat také na webu Azure Portal. Další informace o tom, jak zobrazit experiment na webovém portálu, najdete v tématu [jak sledovat experimenty](how-to-monitor-view-training-logs.md#view-the-experiment-in-the-web-portal).

## <a name="find-the-best-model"></a>Najít nejlepší model

Po dokončení všech spuštění ladění parametrů proveďte [identifikaci nejlepšího provedení konfigurace](/python/api/azureml-train-core/azureml.train.hyperdrive.hyperdriverun?view=azure-ml-py&preserve-view=true#&preserve-view=trueget-best-run-by-primary-metric-include-failed-true--include-canceled-true--include-resume-from-runs-true-----typing-union-azureml-core-run-run--nonetype-) a odpovídajících hodnot parametrů:

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
