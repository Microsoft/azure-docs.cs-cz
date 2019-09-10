---
title: Detekce posunu dat (Preview) v nasazeních AKS
titleSuffix: Azure Machine Learning service
description: Detekci posunu dat na nasazených modelech služby Azure Kubernetes ve službě Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: copeters
author: cody-dkdc
ms.date: 07/08/2019
ms.openlocfilehash: 7d47b74d4fef3676101f3f624dcacb832dcedc3a
ms.sourcegitcommit: 65131f6188a02efe1704d92f0fd473b21c760d08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/10/2019
ms.locfileid: "70858705"
---
# <a name="detect-data-drift-preview-on-models-deployed-to-azure-kubernetes-service-aks"></a>Detekce posunu dat (Preview) na modelech nasazených do služby Azure Kubernetes Service (AKS)

V tomto článku se dozvíte, jak monitorovat posun dat mezi školicí datovou sadou a odvozenými daty nasazeného modelu. V kontextu strojového učení můžou školicí modely strojového učení mít vliv na snížení výkonu předpovědi z důvodu snížení úrovně. Pomocí služby Azure Machine Learning můžete monitorovat posun dat a služba vám může při zjištění posunu odeslat e-mailovou výstrahu.

## <a name="what-is-data-drift"></a>Co je posun dat?

K posunu dat dochází, když se data obsluhovaná modelem v produkčním prostředí liší od dat použitých ke výukě modelu. Je to jeden z hlavních důvodů, kdy přesnost modelu v čase snižuje výkon, takže sledování posunu dat pomáhá detekovat problémy s výkonem modelu. 

## <a name="what-can-i-monitor"></a>Co můžu monitorovat?

Pomocí služby Azure Machine Learning můžete sledovat vstupy modelu nasazeného na AKS a porovnávat tato data s datovou sadou dat pro školicí model. Odvozená data jsou v pravidelných intervalech [snímková a](how-to-explore-prepare-data.md)profilovaná a pak se vypočítávají proti základní datové sadě za účelem vytvoření analýzy posunu dat, která: 

+ Měří velikost posunu dat, která se označuje jako unášený koeficient.
+ Měří příspěvek na posun dat podle funkcí a informuje o tom, které funkce způsobily posun dat.
+ Měří metriky na dálku. V současné době se vypočítávají Wasserstein a energetická vzdálenost.
+ Měří distribuce funkcí. V současné době se odhad hustoty a histogramy.
+ Posílat výstrahy na posun dat e-mailem

> [!Note]
> Tato služba je v (Preview) a omezená z možností konfigurace. Podrobnosti a aktualizace najdete v naší [dokumentaci k rozhraní API](https://docs.microsoft.com/python/api/azureml-contrib-datadrift/?view=azure-ml-py) a v [poznámkách k verzi](azure-machine-learning-release-notes.md) . 

### <a name="how-data-drift-is-monitored-in-azure-machine-learning-service"></a>Jak se monitoruje posun dat ve službě Azure Machine Learning

Pomocí Azure Machine Learning služby je mezi datovými sadami a nasazeními monitorovaná data. Chcete-li monitorovat pro posun dat, je určena datová sada, která je obvykle datovou sadou pro model. Druhá datová sada – obvykle modelují vstupní data shromážděná z nasazení – jsou testována proti základní datové sadě. Obě datové sady jsou profilované a vstupní služba pro sledování datového posunu dat. Model strojového učení je vyškolen k detekci rozdílů mezi dvěma datovými sadami. Výkon modelu se převede na koeficient posunu, který měří velikost posunu mezi dvěma datovými sadami. Pomocí [Interpretace modelu](machine-learning-interpretability-explainability.md)jsou vypočítány funkce, které přispívají k součiniteli posunu. Z profilu datové sady jsou sledovány statistické informace o jednotlivých funkcích. 

## <a name="prerequisites"></a>Požadavky

- Předplatné Azure. Pokud ho ještě nemáte, vytvořte si bezplatný účet před tím, než začnete. Vyzkoušení [bezplatné nebo placené verze služby Azure Machine Learning](https://aka.ms/AMLFree) dnes

- Sada Azure Machine Learning SDK pro Python je nainstalována. Pokyny v [Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py) použijte k následujícím akcím:

    - Vytvoření prostředí Miniconda
    - Instalace sady Azure Machine Learning SDK pro Python

- [Pracovní prostor služby Azure Machine Learning](how-to-manage-workspace.md).

- [Konfigurační soubor](how-to-configure-environment.md#workspace)pracovního prostoru.

- Nainstalujte sadu SDK pro přenos dat pomocí následujícího příkazu:

    ```shell
    pip install azureml-contrib-datadrift
    ```

- Vytvořte [datovou sadu](how-to-create-register-datasets.md) z školicích dat vašeho modelu.

- Určete datovou sadu školení při [registraci](concept-model-management-and-deployment.md) modelu. Následující příklad ukazuje použití `datasets` parametru pro určení datové sady školení:

    ```python
    model = Model.register(model_path=model_file,
                        model_name=model_name,
                        workspace=ws,
                        datasets=datasets)

    print(model_name, image_name, service_name, model)
    ```

- [Povolte shromažďování dat modelu](how-to-enable-data-collection.md) pro shromažďování dat z nasazení modelu AKS a ověřte, že se data shromažďují v `modeldata` kontejneru objektů BLOB.

## <a name="configure-data-drift"></a>Konfigurace posunu dat
Pokud chcete pro svůj experiment nakonfigurovat posun dat, importujte závislosti, jak je vidět v následujícím příkladu Pythonu. 

Tento příklad ukazuje konfiguraci [`DataDriftDetector`](https://docs.microsoft.com/python/api/azureml-contrib-datadrift/azureml.contrib.datadrift.datadriftdetector.datadriftdetector?view=azure-ml-py) objektu:

```python
# Import Azure ML packages
from azureml.core import Experiment, Run, RunDetails
from azureml.contrib.datadrift import DataDriftDetector, AlertConfiguration

# if email address is specified, setup AlertConfiguration
alert_config = AlertConfiguration('your_email@contoso.com')

# create a new DatadriftDetector object
datadrift = DataDriftDetector.create(ws, model.name, model.version, services, frequency="Day", alert_config=alert_config)
    
print('Details of Datadrift Object:\n{}'.format(datadrift))
```

## <a name="submit-a-datadriftdetector-run"></a>Odeslání DataDriftDetector spuštění

S nakonfigurovaným objektem můžete odeslat [Posun dat spuštěný](https://docs.microsoft.com/python/api/azureml-contrib-datadrift/azureml.contrib.datadrift.datadriftdetector%28class%29?view=azure-ml-py#run-target-date--services--compute-target-name-none--create-compute-target-false--feature-list-none--drift-threshold-none-) v daném datu pro model. `DataDriftDetector` V rámci spuštění povolte výstrahy DataDriftDetector nastavením `drift_threshold` parametru. Pokud je [datadrift_coefficient](#metrics) nad daným `drift_threshold`, pošle se e-mail.

```python
# adhoc run today
target_date = datetime.today()

# create a new compute - creates datadrift-server
run = datadrift.run(target_date, services, feature_list=feature_list, create_compute_target=True)

# or specify existing compute cluster
run = datadrift.run(target_date, services, feature_list=feature_list, compute_target='cpu-cluster')

# show details of the data drift run
exp = Experiment(ws, datadrift._id)
dd_run = Run(experiment=exp, run_id=run)
RunDetails(dd_run).show()
```

## <a name="visualize-drift-metrics"></a>Vizualizovat metriky s posunem

<a name="metrics"></a>

Po odeslání DataDriftDetector můžete zobrazit metriky posunu, které jsou uloženy v každé iteraci spuštění pro úlohu posunu dat:


|Metrika|Popis|
--|--|
wasserstein_distance|Statistická vzdálenost definovaná pro jednorozměrné číselné rozdělení.|
energy_distance|Statistická vzdálenost definovaná pro jednorozměrné číselné rozdělení.|
datadrift_coefficient|Vypočítáno podobně jako korelační koeficient Matthew, ale tento výstup je reálné číslo od 0 do 1. V případě posunu 0 neindikuje žádný posun a 1 označuje maximální posun.|
datadrift_contribution|Důležitost funkcí, které přispívají k posunu.|

Existují různé způsoby, jak zobrazit metriky pro posun:

* Použijte widget [Jupyter.](https://docs.microsoft.com/python/api/azureml-widgets/azureml.widgets?view=azure-ml-py) `RunDetails`
* Funkci použijte u libovolného `datadrift` objektu run. [`get_metrics()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py#get-metrics-name-none--recursive-false--run-type-none--populate-false-)
* Zobrazte metriky v Azure Portal modelu.
* Podívejte se na metriky v části **modely** na [cílové stránce pracovního prostoru (Preview)](https://ml.azure.com).

Následující příklad Pythonu ukazuje, jak vykreslovat relevantní metriky pro posun dat. Pomocí vrácené metriky můžete vytvářet vlastní vizualizace:

```python
# start and end are datetime objects 
drift_metrics = datadrift.get_output(start_time=start, end_time=end)

# Show all data drift result figures, one per serivice.
# If setting with_details is False (by default), only the data drift magnitude will be shown; if it's True, all details will be shown.
drift_figures = datadrift.show(with_details=True)
```

![Viz posun dat zjištěný Azure Machine Learning](media/how-to-monitor-data-drift/drift_show.png)


## <a name="schedule-data-drift-scans"></a>Plánování posunu dat 

Když povolíte detekci posunu dat, DataDriftDetector se spustí podle zadané plánované frekvence. Pokud datadrift_coefficient dosáhne daného `drift_threshold`přenosu, pošle se při každém plánovaném spuštění e-mail. 

```python
datadrift.enable_schedule()
datadrift.disable_schedule()
```

Konfiguraci rozpoznávání přenosů dat můžete zobrazit na stránce s podrobnostmi modelu na stránce Azure Portal nebo na cílové stránce pracovního prostoru (Preview).

![Azure Portal konfigurace pro posun dat](media/how-to-monitor-data-drift/drift_config.png)

## <a name="view-results-in-azure-portal"></a>Zobrazit výsledky v Azure Portal

Pokud chcete zobrazit výsledky v pracovním prostoru v [Azure Portal](https://portal.azure.com), přejděte na stránku modelu. Na kartě Podrobnosti modelu se zobrazí konfigurace posunování dat. Karta posun dat (Preview) je teď dostupná pro vizualizaci metriky posunu dat. 

![Azure Portal posun dat](media/how-to-monitor-data-drift/drift_ui.png)

Výsledky jsou také k dispozici v podrobnostech modelu na [cílové stránce pracovního prostoru (Preview)](https://ml.azure.com).

## <a name="receiving-drift-alerts"></a>Přijímání upozornění na posun

Když nastavíte prahovou hodnotu pro upozornění na posunu dolů a zadáte e-mailovou adresu, pošle se [Azure monitor](https://docs.microsoft.com/azure/azure-monitor/overview) e-mailová výstraha automaticky, kdykoli je výše uvedená prahová hodnota. 

Aby bylo možné nastavit vlastní výstrahy a akce, jsou všechny metriky posunu dat uloženy v prostředku [Application Insights](how-to-enable-app-insights.md) , který byl vytvořen společně s pracovním prostorem služby Azure Machine Learning. Můžete sledovat odkaz v e-mailovém upozornění na dotaz Application Insights.

![Upozornění na posun dat e-mailem](media/how-to-monitor-data-drift/drift_email.png)

## <a name="retrain-your-model-after-drift"></a>Přeškolování modelu po posunu

Pokud se data odsadí negativně vlivem na výkon nasazeného modelu, je čas na přeškolování modelu. Provedete to tak, že budete pokračovat v následujících krocích.

* Prozkoumat shromážděná data a připravit data pro výuku nového modelu.
* Rozdělte je na data pro analýzu a testování.
* Pomocí nových dat znovu nahlaste model.
* Vyhodnoťte výkon nově vygenerovaného modelu.
* Nasaďte nový model, pokud je výkon lepší než produkční model.

## <a name="next-steps"></a>Další postup

* Úplný příklad použití posunu dat najdete v [poznámkovém bloku Azure ml – datový posun](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/monitor-models/data-drift/azure-ml-datadrift.ipynb). Tento Jupyter Notebook ukazuje použití [datové sady Azure Open](https://docs.microsoft.com/azure/open-datasets/overview-what-are-open-datasets) pro výuku modelu, který předpovídá počasí, nasadí ho do AKS a monitoruje posun dat. 

* Rádi bychom si výrazně vyhodnotili vaše dotazy, komentáře a návrhy, protože se pohyb dat blíží k všeobecné dostupnosti. Použijte následující tlačítko pro odeslání názoru na produkt. 
