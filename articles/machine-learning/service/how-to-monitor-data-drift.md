---
title: Detekce posunu dat (Preview) v nasazeních AKS
titleSuffix: Azure Machine Learning
description: Detekci posunu dat u nasazených modelů služby Azure Kubernetes v Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: copeters
author: cody-dkdc
ms.date: 11/04/2019
ms.openlocfilehash: eddfddc994482bcb6e60604703e7f306fee9c32b
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2019
ms.locfileid: "73574330"
---
# <a name="detect-data-drift-preview-on-models-deployed-to-azure-kubernetes-service-aks"></a>Detekce posunu dat (Preview) na modelech nasazených do služby Azure Kubernetes Service (AKS)
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-enterprise-sku.md)]

V tomto článku se dozvíte, jak monitorovat posun dat mezi školicí datovou sadou a odvozenými daty nasazeného modelu. V kontextu strojového učení můžou školicí modely strojového učení mít vliv na snížení výkonu předpovědi z důvodu snížení úrovně. Pomocí Azure Machine Learning můžete monitorovat posun dat a služba vám může při zjištění posunu odeslat e-mailovou výstrahu.

## <a name="what-is-data-drift"></a>Co je posun dat?

V kontextu strojového učení je posun dat změnou v modelu vstupní data, která vede k modelování snížení výkonu. Je to jeden z hlavních důvodů, kdy přesnost modelu v čase snižuje výkon, takže sledování posunu dat pomáhá detekovat problémy s výkonem modelu. 

## <a name="what-can-i-monitor"></a>Co můžu monitorovat?

Pomocí Azure Machine Learning můžete sledovat vstupy modelu nasazeného na AKS a porovnávat tato data s datovou sadou školení pro model. Odvozená data jsou v pravidelných intervalech [snímková a](how-to-explore-prepare-data.md)profilovaná a pak se vypočítávají proti základní datové sadě za účelem vytvoření analýzy posunu dat, která: 

+ Měří velikost posunu dat, která se označuje jako unášený koeficient.
+ Měří příspěvek na posun dat podle funkcí a informuje o tom, které funkce způsobily posun dat.
+ Měří metriky na dálku. V současné době se vypočítávají Wasserstein a energetická vzdálenost.
+ Měří distribuce funkcí. V současné době se odhad hustoty a histogramy.
+ Posílat výstrahy na posun dat e-mailem

> [!Note]
> Tato služba je v (Preview) a omezená z možností konfigurace. Podrobnosti a aktualizace najdete v naší [dokumentaci k rozhraní API](https://docs.microsoft.com/python/api/azureml-contrib-datadrift/?view=azure-ml-py) a v [poznámkách k verzi](azure-machine-learning-release-notes.md) . 

### <a name="how-data-drift-is-monitored-in-azure-machine-learning"></a>Jak se monitoruje posun dat v Azure Machine Learning

Pomocí Azure Machine Learning jsou data unášená pomocí datových sad nebo nasazení. Chcete-li monitorovat pro posun dat, je určena datová sada, která je obvykle datovou sadou pro model. Druhá datová sada – obvykle modelují vstupní data shromážděná z nasazení – jsou testována proti základní datové sadě. Obě datové sady jsou profilované a vstupní služba pro sledování datového posunu dat. Model strojového učení je vyškolen k detekci rozdílů mezi dvěma datovými sadami. Výkon modelu se převede na koeficient posunu, který měří velikost posunu mezi dvěma datovými sadami. Pomocí [Interpretace modelu](how-to-machine-learning-interpretability.md)jsou vypočítány funkce, které přispívají k součiniteli posunu. Z profilu datové sady jsou sledovány statistické informace o jednotlivých funkcích. 

## <a name="prerequisites"></a>Požadavky

- Předplatné Azure. Pokud ho ještě nemáte, vytvořte si bezplatný účet před tím, než začnete. Vyzkoušení [bezplatné nebo placené verze Azure Machine Learning](https://aka.ms/AMLFree) dnes

- Sada Azure Machine Learning SDK pro Python je nainstalována. Pokyny v [Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py) použijte k následujícím akcím:

    - Vytvoření prostředí Miniconda
    - Instalace sady Azure Machine Learning SDK pro Python

- [Pracovní prostor Azure Machine Learning](how-to-manage-workspace.md).

- [Konfigurační soubor](how-to-configure-environment.md#workspace)pracovního prostoru.

- Nainstalujte sadu SDK pro přenos dat pomocí následujícího příkazu:

    ```shell
    pip install azureml-datadrift
    ```

- Vytvořte [datovou sadu](how-to-create-register-datasets.md) z školicích dat vašeho modelu.

- Určete datovou sadu školení při [registraci](concept-model-management-and-deployment.md) modelu. Následující příklad ukazuje použití parametru `datasets` k určení datové sady školení:

    ```python
    model = Model.register(model_path=model_file,
                        model_name=model_name,
                        workspace=ws,
                        datasets=datasets)

    print(model_name, image_name, service_name, model)
    ```

- [Povolte shromažďování dat modelu](how-to-enable-data-collection.md) pro shromažďování dat z nasazení modelu AKS a potvrďte shromažďování dat v `modeldata` kontejneru objektů BLOB.

## <a name="configure-data-drift"></a>Konfigurace posunu dat
Pokud chcete pro svůj experiment nakonfigurovat posun dat, importujte závislosti, jak je vidět v následujícím příkladu Pythonu. 

Tento příklad ukazuje, jak nakonfigurovat objekt [`DataDriftDetector`](https://docs.microsoft.com/python/api/azureml-contrib-datadrift/azureml.contrib.datadrift.datadriftdetector.datadriftdetector?view=azure-ml-py) :

```python
# Import Azure ML packages
from azureml.core import Experiment, Run, RunDetails
from azureml.datadrift import DataDriftDetector, AlertConfiguration

# if email address is specified, setup AlertConfiguration
alert_config = AlertConfiguration('your_email@contoso.com')

# create a new DatadriftDetector object
datadrift = DataDriftDetector.create(ws, model.name, model.version, services, frequency="Day", alert_config=alert_config)
    
print('Details of Datadrift Object:\n{}'.format(datadrift))
```

## <a name="submit-a-datadriftdetector-run"></a>Odeslání DataDriftDetector spuštění

S nakonfigurovaným objektem `DataDriftDetector` můžete odeslat [Posun dat spuštěný](https://docs.microsoft.com/python/api/azureml-contrib-datadrift/azureml.contrib.datadrift.datadriftdetector%28class%29?view=azure-ml-py#run-target-date--services--compute-target-name-none--create-compute-target-false--feature-list-none--drift-threshold-none-) v daném datu pro daný model. V rámci spuštění povolte výstrahy DataDriftDetector nastavením parametru `drift_threshold`. Pokud je [datadrift_coefficient](#metrics) nad daným `drift_threshold`, pošle se e-mail.

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

* Použijte [pomůcku `RunDetails`Jupyter](https://docs.microsoft.com/python/api/azureml-widgets/azureml.widgets?view=azure-ml-py).
* Použijte funkci [`get_metrics()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py#get-metrics-name-none--recursive-false--run-type-none--populate-false-) u libovolného objektu `datadrift` spuštění.
* Metriky najdete v části **modely** v pracovním prostoru v [Azure Machine Learning Studiu](https://ml.azure.com).

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

Když povolíte detekci posunu dat, DataDriftDetector se spustí podle zadané plánované frekvence. Pokud datadrift_coefficient dosáhne daného `drift_threshold`, pošle se při každém plánovaném spuštění e-mail. 

```python
datadrift.enable_schedule()
datadrift.disable_schedule()
```

Konfigurace detektoru pro přenos dat se dá zobrazit v části **modely** na kartě **Podrobnosti** ve vašem pracovním prostoru na [Azure Machine Learning Studiu](https://ml.azure.com).

[Posun dat ![Azure Machine Learning Studio](media/how-to-monitor-data-drift/drift-config.png)](media/how-to-monitor-data-drift/drift-config-expanded.png)

## <a name="view-results-in-your-azure-machine-learning-studio"></a>Zobrazení výsledků v Azure Machine Learning Studiu

Pokud chcete zobrazit výsledky v pracovním prostoru v [Azure Machine Learning Studiu](https://ml.azure.com), přejděte na stránku modelu. Na kartě Podrobnosti modelu se zobrazí konfigurace posunování dat. Teď je dostupná karta pro **Posun dat** , která vizualizuje metriky posunu dat. 

[Posun dat ![Azure Machine Learning Studio](media/how-to-monitor-data-drift/drift-ui.png)](media/how-to-monitor-data-drift/drift-ui-expanded.png)

## <a name="receiving-drift-alerts"></a>Přijímání upozornění na posun

Když nastavíte prahovou hodnotu pro upozornění na posunu dolů a zadáte e-mailovou adresu, pošle se [Azure monitor](https://docs.microsoft.com/azure/azure-monitor/overview) e-mailová výstraha automaticky, kdykoli je výše uvedená prahová hodnota. 

Aby bylo možné nastavit vlastní výstrahy a akce, jsou všechny metriky posunu dat uloženy v prostředku [Application Insights](how-to-enable-app-insights.md) , který byl vytvořen společně s pracovním prostorem Azure Machine Learning. Můžete sledovat odkaz v e-mailovém upozornění na dotaz Application Insights.

![Upozornění na posun dat e-mailem](media/how-to-monitor-data-drift/drift_email.png)

## <a name="retrain-your-model-after-drift"></a>Přeškolování modelu po posunu

Pokud se data odsadí negativně vlivem na výkon nasazeného modelu, je čas na přeškolování modelu. Provedete to tak, že budete pokračovat v následujících krocích.

* Prozkoumat shromážděná data a připravit data pro výuku nového modelu.
* Rozdělte je na data pro analýzu a testování.
* Pomocí nových dat znovu nahlaste model.
* Vyhodnoťte výkon nově vygenerovaného modelu.
* Nasaďte nový model, pokud je výkon lepší než produkční model.

## <a name="next-steps"></a>Další kroky

* Úplný příklad použití posunu dat najdete v [poznámkovém bloku Azure ml – datový posun](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/monitor-models/data-drift/azure-ml-datadrift.ipynb). Tento Jupyter Notebook ukazuje použití [datové sady Azure Open](https://docs.microsoft.com/azure/open-datasets/overview-what-are-open-datasets) pro výuku modelu, který předpovídá počasí, nasadí ho do AKS a monitoruje posun dat. 

* Rádi bychom si výrazně vyhodnotili vaše dotazy, komentáře a návrhy, protože se pohyb dat blíží k všeobecné dostupnosti. Použijte následující tlačítko pro odeslání názoru na produkt. 
