---
title: Vyladění hyperparameters pro model pomocí Azure Machine Learning
description: Zjistěte, jak ladit hyperparameters pro obsáhlý learning / machine learning modelu pomocí služby Azure Machine Learning. Uvidíte jak definovat parametr prostoru pro hledání, zadejte primární metriku pro optimalizaci a již v rané fázi ukončit nedostatečný výkon konfigurace.
ms.author: swatig
author: swatig007
ms.reviewer: sgilley
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: b6370fd9125c5b14df781b27e028c139175b7589
ms.sourcegitcommit: b7e5bbbabc21df9fe93b4c18cc825920a0ab6fab
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/27/2018
ms.locfileid: "47405797"
---
# <a name="tune-hyperparameters-for-your-model"></a>Optimalizovat pro váš model hyperparameters

V tomto článku zjistíte, jak efektivně vyladit hyperparameters pro váš model. Uvidíte jak definovat parametr prostoru pro hledání, zadejte primární metriku pro optimalizaci a již v rané fázi ukončit nedostatečný výkon konfigurace. Můžete také vizualizovat různé tréninkových spuštění a vyberte ty nejvýkonnější konfigurace pro váš model.

## <a name="what-are-hyperparameters"></a>Co jsou hyperparameters?
Hyperparameters jsou měnitelné parametry zvolili před trénování modelu, kterými se řídí samotný proces školení. Například před školení hluboké neuronové sítě, je potřeba rozhodnout, počet skrytými vrstvami v síti a počet uzlů v každé vrstvě. Tyto hodnoty obvykle zůstat konstantní v průběhu školení.

Ve scénářích obsáhlý learning / machine learning výkon modelů silně závisí na hodnotách hyperparameter vybrali. Cílem hyperparameter zkoumání je hledat v různých konfiguracích hyperparameter najít konfiguraci, jejímž výsledkem požadovaný výkon. Obvykle je proces průzkumu hyperparameter pečlivá postupná ruční oznámeno, že je obrovského množství místa vyhledávání a hodnocení každou konfiguraci může být nákladné.

Azure Machine Learning umožňuje automatizovat tento průzkum hyperparameter efektivním způsobem ukládá je spoustu času a prostředků. Můžete určit rozsah hodnot hyperparameter k prozkoumání a maximální počet školení běží na tento průzkum. Systém pak automaticky spustí více souběžných tréninkových spuštění s konfiguracemi různých parametrů a najde odpovídající konfiguraci, která má za následek nejlepší výkon, měřený podle metriky uživatelem. Vypíšou tréninkových spuštění jsou automaticky předčasné ukončení, snížit plýtvání zbytečně velký počet výpočetních prostředků. Tyto prostředky se místo toho používají prozkoumat další hyperparameter konfigurace.

Pokud chcete ladit hyperparameters pro model pomocí služby Azure Machine Learning, je potřeba provést následovně –
* Definování prostor hyperparameter vyhledávání
* Zadejte primární metriku pro optimalizaci
* Zadejte zásadu předčasné ukončení
* Přidělit prostředky pro hyperparametrů
* Spusťte experiment s konfiguraci uvedené výš

## <a name="define-the-hyperparameter-search-space"></a>Definování prostor hyperparameter vyhledávání
Služba Azure Machine Learning automaticky vyladí hyperparameters prozkoumáním rozsah hodnot definovaných pro každý hyperparameter.

### <a name="types-of-hyperparameters"></a>Typy hyperparametrů
Každý hyperparameter může být buď samostatný nebo průběžné.

#### <a name="discrete-hyperparameters"></a>Diskrétní hyperparameters 
Diskrétní hyperparameters můžete zadat hodnotu `choice` mezi jednotlivých hodnot. `choice` může mít buď jeden nebo více čárkou oddělené hodnoty, `range` objektu nebo některou libovolného `list` objektu. Například  

```Python
    {    
        "batch_size": choice(16, 32, 64, 128)
        "number_of_hidden_layers": choice(range(1,5))
    }
```

V tomto případě batch_size můžete provést jednu z hodnot [16, 32, 64, 128] a number_of_hidden_layers můžete provést jednu z hodnot [1, 2, 3, 4].

Pokročilé diskrétní hyperparameters také lze pomocí distribuci. Jsou podporovány následující distribuce-
* `quniform(low, high, q)` -Vrací hodnotu, jako je kruhové (uniform (dolní, horní) / q) * q
* `qloguniform(low, high, q)` -Vrací hodnotu, jako je kruhové (exp (uniform (dolní, horní)) / q) * q
* `qnormal(mu, sigma, q)` -Vrací hodnotu, jako je kruhové (normální (mu, sigma) / q) * q
* `qlognormal(mu, sigma, q)` -Vrací hodnotu, jako je kruhové (exp (normální (mu, sigma)) / q) * q

#### <a name="continuous-hyperparameters"></a>Průběžné hyperparameters 
Průběžné hyperparameters lze zadat jako distribuční průběžné rozsahu hodnot. Podporované distribuce zahrnují-
* `uniform(low, high)` -Vrací hodnotu, které jsou rovnoměrně rozloženy mezi nízká a vysoká
* `loguniform(low, high)` -Vrací hodnotu vykreslena podle funkce exp (uniform (dolní, horní)) tak, aby rovnoměrně logaritmus návratová hodnota
* `normal(mu, sigma)` -Vrátí skutečnou hodnotu, která je obvykle distribuován spolu s střední sigma jednotku zasílání zpráv a směrodatné odchylky
* `lognormal(mu, sigma)` -Vrací hodnotu tak, aby logaritmus návratová hodnota je obvykle distribuován vykreslena podle funkce exp (normální (mu, sigma))

Tady je příklad definici místo parametru-

```Python
    {    
        "learning_rate": normal(10, 3),
        "keep_probability": uniform(0.05, 0.1)
    }
```

Tento příklad definuje prostoru pro hledání se dvěma parametry - learning_rate a keep_probability. learning_rate bude mít normální rozdělení dat s střední hodnotu 10 a směrodatné odchylky 3. keep_probability bude mít rovnoměrné rozdělení s minimální hodnotou 0,05 a maximální hodnota 0,1.

### <a name="sampling-the-hyperparameter-space"></a>Vzorkování hyperparameter místa
Uživatel také určuje vzorkování parametr metody pro použití v definici zadaného hyperparameter místa. Služba Azure Machine Learning podporuje vzorkování Random, vzorkování mřížky a Bayesova vzorkování.

#### <a name="random-sampling"></a>Náhodný vzorkování
Při vzorkování Random jsou hodnoty hyperparameter náhodně vybrané z definovaných hledání prostoru. Výběrová umožňuje zahrnout diskrétních a souvislých hyperparameters prostoru pro hledání. Například

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
Mřížka vzorkování provádí jednoduché tabulky vyhledávání přes všechny možné hodnoty v prostoru definované vyhledávání. Lze použít pouze s hyperparameters zadat pomocí `choice`. Například následující místo má celkem šest ukázky –

```Python
from azureml.train.hyperdrive import GridParameterSampling
param_sampling = GridParameterSampling( {
        "num_hidden_layers": choice(1, 2, 3),
        "batch_size": choice(16, 32)
    }
)
```

#### <a name="bayesian-sampling"></a>Bayesova vzorkování
Bayesova vzorkování je založená na algoritmus pro optimalizaci Bayesova a díky inteligentní možnosti na hodnoty hyperparameter ukázkový dále. Použije tuto ukázku podle jak předchozí ukázky provádí, například, že nové ukázkové zlepšuje ohlášené primární metriku.

Při použití Bayesova vzorkování, počtu souběžných spuštění má dopad na efektivitu ladění procesu. Obvykle menší počet souběžných spuštění může vést k vyšší konvergence vzorkování. Je to proto, že menší míru paralelismu zvýší počet běhů, které využívají samosprávné předchozí dokončená spuštění.

Vzorkování Bayesova podporuje pouze `choice` a `uniform` distribuce v prostoru pro hledání. Například 

```Python
from azureml.train.hyperdrive import BayesianParameterSampling
param_sampling = BayesianParameterSampling( {
        "learning_rate": uniform(0.05, 0.1),
        "batch_size": choice(16, 32, 64, 128)
    }
)
```

> [!NOTE]
> Vzorkování Bayesův aktuálně nepodporuje žádnou zásadu předčasné ukončení (viz [zadejte zásadu předčasné ukončení](#specify-an-early-termination-policy)). Pokud používáte Bayesova parametr vzorkování, je nutné nastavit zásady na `None`. Bez zadání zásady ukončení s povoleným vzorkováním Bayesova bude mít stejný účinek.
>
> ```Python
> early_termination_policy = None
> ```

## <a name="specify-a-primary-metric-to-optimize"></a>Zadejte primární metriku pro optimalizaci
Při ladění hyperparameters, musíte zadat primární metriku, která chcete hyperparameter experiment optimalizovat pro ladění. Každé spuštění školení je vyhodnocen pro tuto primární metriku a nedostatečný výkon spuštění (kde primární metriku nesplňuje kritéria stanovená zásadou předčasné ukončení) bude ukončena. Kromě zadání primární název metriky, musíte také zadat cíle optimalizace -, jestli se má Maximalizace nebo minimalizace primární metriku.
* `primary_metric_name`: Název primární metriky pro optimalizaci. Název primárního metriky musí přesně shodovat s názvem metrika zapsané podle cvičný skript. Zobrazit [protokolu metriky pro hyperparametrů](#log-metrics-for-hyperparameter-tuning).
* `primary_metric_goal`: To může být buď `PrimaryMetricGoal.MAXIMIZE` nebo `PrimaryMetricGoal.MINIMIZE` a určuje, zda bude primární metriku maximalizované nebo minimalizovaná při vyhodnocování spuštění. 

Například-
```Python
primary_metric_name="accuracy",
primary_metric_goal=PrimaryMetricGoal.MAXIMIZE
```
Optimalizuje běží pro maximalizaci "přesnost".

### <a name="log-metrics-for-hyperparameter-tuning"></a>Protokolujte metriky pro hyperparametrů
Chcete-li používat službu Azure Machine Learning pro hyperparametrů, bude nutné skript školení pro váš model sestavy důležité metriky, zatímco model spustí. Uživatel zadá primární metriku chtějí službu, aby používala za vaše rozhodnutí vyzkoušet spuštění výkonu, a trénovací skript bude nutné se přihlásit tuto metriku. Zobrazit [zadejte primární metriku pro optimalizaci](#specify-a-primary-metric-to-optimize).

Váš skript školení k protokolování tuto metriku můžete aktualizovat pomocí následující ukázka fragmentu kódu -

```Python
from azureml.core.run import Run
run_logger = Run.get_submitted_run()
run_logger.log("accuracy", float(val_accuracy))
```

V tomto příkladu cvičný skript vypočítá `val_accuracy` a protokolují tato "přesnost", který se používá jako primární metriku. Záleží model pro vývojáře a zjistěte, jak často chcete nahlásit tuto metriku.

## <a name="specify-an-early-termination-policy"></a>Zadejte zásadu předčasné ukončení
Při použití služby Azure Machine Learning pro optimalizaci hyperparameters nedostatečný výkon spuštění jsou automaticky předčasné ukončení. To snižuje plýtvání zbytečně velký počet prostředků a místo toho použije tyto prostředky pro zkoumání konfigurace dalších parametrů.

Při použití zásady předčasné ukončení, uživatel může konfigurovat následující parametry, které řídí, kdy se použije zásada na-
* `evaluation_interval`: frekvence k uplatnění zásad. Pokaždé, když cvičný skript protokoluje primární metrika se počítá jako jeden interval. Proto `evaluation_interval` 1 uplatní zásady pokaždé, když se skript školení hlásí primární metriku. `evaluation_interval` 2 se dané zásady používaly jiné pokaždé cvičný skript hlásí primární metriku. Toto je volitelný parametr a pokud není zadán, `evaluation_interval` je standardně nastavená na hodnotu 1.
* `delay_evaluation`: zpoždění první vyhodnocení zásad pro zadaný počet intervalů o délce. Toto je volitelný parametr, který umožňuje všechny konfigurace spuštění pro počáteční minimální počet intervalů, jak se vyhnout předčasné ukončení tréninková spuštění. Je-li zadána, se zásady vztahují každých několik evaluation_interval, který je větší než nebo rovna hodnotě delay_evaluation.

Služba Azure Machine Learning podporuje následující zásady předčasné ukončení –

### <a name="bandit-policy"></a>Bandit zásad
Bandit zásady je zásada ukončení na základě intervalu slacku faktor/slack velikost a vyhodnocení. Tyto zásady se již v rané fázi ukončí všechny běhy, kde primární metriky není v rámci zadaný slackový faktor / slacku velikost s ohledem na nejvýkonnější školení spuštění. Přijímá následující parametry konfigurace-
* `slack_factor` nebo `slack_amount`: slack povolené s ohledem na nejvýkonnější školení spustit. `slack_factor` Určuje povolené slack jako poměr. `slack_amount` Určuje povolené slack jako absolutní hodnota, namísto poměr.

    Představte si třeba Bandit zásady se použijí v intervalu 10. Předpokládejme, že nejlepší provádění spustit v intervalu 10 hlášené primární metriku 0,8 s cílem pro maximalizaci primární metriky. Pokud zásada nebyla zadána s `slack_factor` 0,2, spustí všechny školení, jehož nejlepší metriky v intervalu 10 je menší než 0.66 (0,8 / (1 +`slack_factor`)) bude ukončena. Pokud místo toho se zadaným zásady `slack_amount` 0,2, spustí všechny školení, jehož nejlepší metriky v intervalu 10 je menší než 0.6 (0,8 - `slack_amount`) bude ukončena.
* `evaluation_interval`: frekvence pro použití zásady (nepovinný parametr).
* `delay_evaluation`: zpoždění první vyhodnocení zásad pro zadaný počet intervalů (nepovinný parametr).

Podívejte se například-

```Python
from azureml.train.hyperdrive import BanditPolicy
early_termination_policy = BanditPolicy(slack_factor = 0.1, evaluation_interval=1, delay_evaluation=5)
```

V tomto příkladu předčasné ukončení zásady se použijí na každý interval při metriky se vykazují, počínaje interval přehodnocení 5. Jakékoli spuštění, jehož nejlepší metrika je menší než (1/(1+0.1) nebo 91 % nejlépe provádí spuštění bude ukončena.

### <a name="median-stopping-policy"></a>Medián zastavení zásad
Medián zastavení zásad je zásadu předčasné ukončení podle systémem průměry primární metriky hlášených spuštění. Tato zásada vypočítá spuštěné průměry přes všechny tréninkových spuštění a ukončí spuštění, jehož výkon je horší než medián spuštěné průměry. Tyto zásady mají následující parametry konfigurace-
* `evaluation_interval`: frekvence pro použití zásady (nepovinný parametr).
* `delay_evaluation`: zpoždění první vyhodnocení zásad pro zadaný počet intervalů (nepovinný parametr).

Podívejte se například-

```Python
from azureml.train.hyperdrive import MedianStoppingPolicy
early_termination_policy = MedianStoppingPolicy(evaluation_interval=1, delay_evaluation=5)
```

V tomto příkladu předčasné ukončení zásad se použije na každý interval počínaje interval přehodnocení 5. Spuštění bude ukončena v intervalu 5, pokud jeho nejlepší primární metriku horší než medián spuštěné průměry v intervalu 1:5 přes všechny tréninkových spuštění.

### <a name="truncation-selection-policy"></a>Výběr zásad zkrácení
Zruší výběr zásad zkrácení příslušné procento nejnižší provádění běží v každém intervalu hodnocení. Spuštění porovnání na základě jejich výkonu na primární metriku a nejnižší X % budou ukončeny. Přijímá následující parametry konfigurace-
* `truncation_percentage`: spustí procento nejnižší provádění ukončení v každém intervalu hodnocení. To by měla být celočíselná hodnota mezi 1 a 99.
* `evaluation_interval`: frekvence pro použití zásady (nepovinný parametr).
* `delay_evaluation`: zpoždění první vyhodnocení zásad pro zadaný počet intervalů (nepovinný parametr).

Podívejte se například-

```Python
from azureml.train.hyperdrive import TruncationSelectionPolicy
early_termination_policy = TruncationSelectionPolicy(evaluation_interval=1, truncation_percentage=20, delay_evaluation=5)
```

V tomto příkladu předčasné ukončení zásad se použije na každý interval počínaje interval přehodnocení 5. Spuštění se ukončí v intervalu 5, pokud je její výkon v intervalu 5 v nejnižší 20 % výkonu všechna spuštění v intervalu 5.

### <a name="no-termination-policy"></a>Žádné zásady ukončení
Pokud chcete, aby všechny tréninkových spuštění dokončen, použijte NoTerminationPolicy. To nebude mít vliv není použití předčasné ukončení zásad. Například 

```Python
from azureml.train.hyperdrive import NoTerminationPolicy
early_termination_policy = NoTerminationPolicy()
```

### <a name="default-policy"></a>Výchozí zásady
Pokud není zadána žádná zásada, hyperparameter ladění služby bude používat střední zastavení zásad s `evaluation_interval` 1 a `delay_evaluation` 5 ve výchozím nastavení. Toto jsou konzervativní nastavení, které můžou zajisti přibližně 25 % – 35 % levnější bez ztráty na primární metriku (podle našich data pro vyhodnocení).

## <a name="allocate-resources-for-hyperparameter-tuning"></a>Přidělit prostředky pro hyperparametrů
Řízení rozpočtu prostředků pro váš hyperparameter ladění experiment zadáním maximální celkový počet tréninkových spuštění a volitelně také maximální dobu trvání vaše hyperparameter ladění experiment (v minutách). 
* `max_total_runs`: Maximální počet tréninkových spuštění, které budou vytvořeny. Toto je horní mez – může máme méně spuštění, například pokud hyperparameter prostor je však konečný a má menší počet vzorků. Musí být číslo v rozsahu od 1 do 1000.
* `max_duration_minutes`: Maximální doba trvání hyperparameter ladění testu v minutách. Toto je volitelný parametr, a pokud jsou k dispozici, se automaticky zruší všechny běhy, které můžou běžet po této hodnotě duration.

>[!NOTE] 
>Pokud mají oba `max_total_runs` a `max_duration_minutes` zadávají hyperparameter ladění experimentu je ukončen po dosažení první z těchto dvou prahové hodnoty.

Kromě toho můžete určit, že maximální počet školení spustí spustit souběžně během vaší hyperparameter optimalizace pro hledání.
* `max_concurrent_runs`: Toto je maximální počet spuštění spustit současně v kterémkoli daném okamžiku. Je-li žádný zadán, všechny `max_total_runs` spustí paralelně. Je-li zadána, musí být číslo mezi 1 a 100.

>[!NOTE] 
>Počet souběžných spuštění jsou závislé na dostupné v cílové zadaný výpočetní prostředky. Proto je potřeba zajistit, že v cílové výpočetní prostředí dostupné prostředky pro požadovanou souběžnosti.

Jak je znázorněno v tomto příkladu – můžete přidělit prostředky pro hyperparametrů
```Python
max_total_runs=20,
max_concurrent_runs=4
```
Tím se nakonfiguruje hyperparameter optimalizace pro experiment použít maximálně 20 celkový počet běhů s 4 konfigurace současně.

## <a name="configure-your-hyperparameter-tuning-experiment"></a>Konfigurace vašeho hyperparameter ladění experimentu
Můžete nakonfigurovat váš hyperparameter ladění experimentu s využitím definované hyperpameter prostoru pro hledání, předčasné ukončení zásad, primární metriky a přidělení prostředků z výše uvedených částech. Kromě toho budete muset zadat `estimator` vzorky hyperparameters, která bude volána. `estimator` Popisuje cvičný skript spustíte, zdroje na jednu úlohu (jeden nebo více grafickými procesory) a cílové výpočetní prostředí používat. Od souběžnosti pro vaše hyperparameter ladění experiment jsou závislé na prostředcích, které jsou k dispozici, budete muset zajistit, že vaše cílové výpočetní prostředí podle `estimator` má dostatek prostředků pro požadovanou souběžnosti. (Viz [trénování modelů](how-to-train-ml-models.md) Další informace o odhady).

Tady je příklad konfigurace ladění experimentu hyperparameter-

```Python
from azureml.train.hyperdrive import HyperDriveRunConfig
hyperdrive_run_config = HyperDriveRunConfig(estimator=estimator,
                          hyperparameter_sampling=param_sampling, 
                          policy=early_termination_policy,
                          primary_metric_name="accuracy", 
                          primary_metric_goal=PrimaryMetricGoal.MAXIMIZE,
                          max_total_runs=100,
                          max_concurrent_runs=4)
```

## <a name="submit-your-hyperparameter-tuning-experiment"></a>Odešlete vaši hyperparameter ladění experimentu
Jakmile je definován váš hyperparameter vyladění konfigurace, můžete odeslat experimentu pomocí této konfigurace -

```Python
from azureml.core.experiment import Experiment
experiment = Experiment(workspace, experiment_name)
hyperdrive_run = experiment.submit(hyperdrive_run_config)
```

kde `experiment_name` je název, kterou chcete přiřadit k vaší hyperparameter ladění experiment, a `workspace` je pracovní prostor, ve kterém chcete vytvořit experiment (viz [odkaz](/concept-azure-machine-learning-architecture.md) Další informace o experimenty).

## <a name="visualize-your-hyperparameter-tuning-experiment"></a>Vizualizujte váš hyperparameter ladění experimentu
Azure Machine Learning sada SDK poskytuje widget Poznámkový blok, který je možné znázornit průběh tréninkových spuštění. Následující fragment kódu je možné vizualizovat všechna vaše hyperparameter optimalizace spuštění na jednom místě –

```Python
from azureml.train.widgets import RunDetails
RunDetails(hyperdrive_run).show()
```

Tato akce zobrazí tabulku s podrobnostmi o tréninkových spuštění pro každou hyperparameter konfigurací. Například

![Tabulka hyperparameter ladění](media/how-to-tune-hyperparameters/HyperparameterTuningTable.png)

Také můžete vizualizovat výkon jednotlivých spuštění v průběhu školení. Například

![Diagram hyperparameter ladění](media/how-to-tune-hyperparameters/HyperparameterTuningPlot.png)

Nakonec můžete vizuálně identifikovat korelace mezi výkonem a hodnoty jednotlivých hyperparameters pomocí paralelní koordinuje vykreslení. Například 

![hyperparameter ladění paralelní souřadnice](media/how-to-tune-hyperparameters/HyperparameterTuningParallelCoordinates.png)

Alternativně můžete vizualizovat váš hyperparameter ladění spuštění portálu Azure web. Zobrazit [odkaz](/how-to-track-experiments.md/#view-the-experiment-in-the-web-portal) Další informace o tom, jak zobrazit experimentu na webovém portálu. Například-

![portál hyperparameter ladění](media/how-to-tune-hyperparameters/HyperparameterTuningPortal.png)

## <a name="find-the-configuration-that-resulted-in-the-best-performance"></a>Najít konfiguraci, které vedlo k zajištění nejlepšího výkonu
Jakmile všechny hyperparameter ladění spuštění dokončila, můžete identifikovat nejvýkonnější konfigurace a odpovídající hyperparameter hodnot pomocí následujícího fragmentu kódu -

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
Odkazovat na 
* `training/03.train-hyperparameter-tune-deploy-with-tensorflow/03.train-hyperparameter-tune-deploy-with-tensorflow.ipynb` kurz týkající se ladění hyperparameters Tensorflow modelu. 

Získáte tento poznámkový blok:

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Další postup
* [Sledovat experimentu](how-to-track-experiments.md)
* [Nasazení trénovaného modelu](how-to-deploy-and-where.md)
