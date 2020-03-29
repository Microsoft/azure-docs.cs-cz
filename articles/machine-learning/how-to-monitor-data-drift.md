---
title: Detekce posunu dat v nasazeních AKS
titleSuffix: Azure Machine Learning
description: Zjistěte unášení dat (preview) na Azure Kubernetes Service nasazených modelů v Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: copeters
author: cody-dkdc
ms.date: 11/04/2019
ms.openlocfilehash: d1da7309b296b57db0c28d5b52fe91efa86709c8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75537003"
---
# <a name="detect-data-drift-preview-on-models-deployed-to-azure-kubernetes-service-aks"></a>Detekce posunu dat (preview) u modelů nasazených ve službě Azure Kubernetes Service (AKS)
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku.md)]

V tomto článku se dozvíte, jak sledovat posun dat mezi trénovací datovou sadou a odvozená data nasazeného modelu. V kontextu strojového učení může dojít k trénovaným modelům strojového učení ke snížení výkonu předpovědi z důvodu posunu. Pomocí Azure Machine Learning můžete sledovat posun dat a služba vám může poslat e-mailové upozornění, když je zjištěn posun.

## <a name="what-is-data-drift"></a>Co je posun dat?

V kontextu strojového učení je posun dat změnou vstupních dat modelu, která vede ke snížení výkonu modelu. Je to jeden z hlavních důvodů, proč přesnost modelu snižuje v průběhu času, tedy sledování dat drift pomáhá detekovat problémy s výkonem modelu. 

## <a name="what-can-i-monitor"></a>Co mohu sledovat?

S Azure Machine Learning můžete sledovat vstupy do modelu nasazeného na AKS a porovnat tato data s trénovací datovou sadou pro model. V pravidelných intervalech jsou data odvození [snímek a profilována](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py), poté vypočtena podle základní datové sady, aby se vytvořila analýza posunu dat, která: 

+ Měří velikost posunu dat, nazvaný drift koeficient.
+ Měří příspěvek posunu dat podle funkce, což označuje, které funkce způsobily posun dat.
+ Měří metriky vzdálenosti. V současné době wasserstein a energetická vzdálenost jsou počítány.
+ Měří rozdělení funkcí. V současné době odhad hustoty jádra a histogramů.
+ Odesílat upozornění na posun dat e-mailem.

> [!Note]
> Tato služba je v (náhled) a omezené v možnostech konfigurace. Podrobnosti a aktualizace najdete v [dokumentaci k rozhraní API](https://docs.microsoft.com/python/api/azureml-datadrift/) a [poznámkách k verzi.](azure-machine-learning-release-notes.md) 

### <a name="how-data-drift-is-monitored-in-azure-machine-learning"></a>Jak se v Azure Machine Learningu monitoruje unášení dat

Pomocí Azure Machine Learning, data drift se monitoruje prostřednictvím datových sad nebo nasazení. Chcete-li sledovat posun dat, je zadána základní datová sada - obvykle trénovací datová sada pro model. Druhá datová sada – obvykle modelová vstupní data shromážděná z nasazení – je testována na základě základní datové sady. Obě datové sady jsou profilovány a vstupdo služby sledování posunu dat. Model strojového učení je trénovaný ke zjištění rozdílů mezi dvěma datovými sadami. Výkon modelu je převeden na drift koeficient, který měří velikost drift mezi dvěma datovými sadami. Pomocí [interpretability modelu](how-to-machine-learning-interpretability.md)se počítají funkce, které přispívají k koeficientu posunu. Z profilu datové sady jsou sledovány statistické informace o jednotlivých funkcích. 

## <a name="prerequisites"></a>Požadavky

- Předplatné Azure. Pokud ho nemáte, vytvořte si účet zdarma, než začnete. Vyzkoušejte [bezplatnou nebo placenou verzi Azure Machine Learning](https://aka.ms/AMLFree) ještě dnes.

- Nainstalovaná sada Azure Machine Learning SDK pro Python. Pomocí pokynů na [Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py) postupujte takto:

    - Vytvoření prostředí Miniconda
    - Instalace sady Azure Machine Learning SDK pro Python

- Pracovní [prostor Azure Machine Learning](how-to-manage-workspace.md).

- [Konfigurační soubor](how-to-configure-environment.md#workspace)pracovního prostoru .

- Nainstalujte sada SDK posunu dat pomocí následujícího příkazu:

    ```shell
    pip install azureml-datadrift
    ```

- Vytvořte [datovou sadu](how-to-create-register-datasets.md) z trénovacích dat modelu.

- Při [registraci](concept-model-management-and-deployment.md) modelu zadejte trénovací datovou sadu. Následující příklad ukazuje použití `datasets` parametru k určení trénovací datové sady:

    ```python
    model = Model.register(model_path=model_file,
                        model_name=model_name,
                        workspace=ws,
                        datasets=datasets)

    print(model_name, image_name, service_name, model)
    ```

- [Povolit shromažďování dat modelu](how-to-enable-data-collection.md) shromažďovat data z nasazení AKS modelu a `modeldata` potvrďte, že data se shromažďují v kontejneru objektů blob.

## <a name="configure-data-drift"></a>Konfigurace posunu dat
Chcete-li nakonfigurovat posun dat pro váš experiment, importujte závislosti, jak je vidět v následujícím příkladu Pythonu. 

Tento příklad ukazuje konfiguraci objektu: [`DataDriftDetector`](/python/api/azureml-datadrift/azureml.datadrift.datadriftdetector.datadriftdetector)

```python
# Import Azure ML packages
from azureml.core import Experiment, Run, RunDetails
from azureml.datadrift import DataDriftDetector, AlertConfiguration

# if email address is specified, setup AlertConfiguration
alert_config = AlertConfiguration('your_email@contoso.com')

# create a new DataDriftDetector object
datadrift = DataDriftDetector.create(ws, model.name, model.version, services, frequency="Day", alert_config=alert_config)
    
print('Details of Datadrift Object:\n{}'.format(datadrift))
```

## <a name="submit-a-datadriftdetector-run"></a>Odeslat spuštění datadriftdetectoru

S `DataDriftDetector` nakonfigurovaným objektem můžete odeslat [posun dat k](https://docs.microsoft.com/python/api/azureml-datadrift/azureml.datadrift.datadriftdetector.datadriftdetector#run-target-date--services-none--compute-target-none--create-compute-target-false--feature-list-none--drift-threshold-none-) danému datu pro model. Jako součást spuštění povolte upozornění DataDriftDetector `drift_threshold` nastavením parametru. Pokud je [datadrift_coefficient](#visualize-drift-metrics) nad `drift_threshold`danou , je odeslán e-mail.

```python
# adhoc run today
target_date = datetime.today()

# create a new compute - creates datadrift-server
run = datadrift.run(target_date, services, feature_list=feature_list, create_compute_target=True)

# or specify existing compute cluster
run = datadrift.run(target_date, services, feature_list=feature_list, compute_target='cpu-cluster')

# show details of the data drift run
exp = Experiment(ws, datadrift._id)
dd_run = Run(experiment=exp, run_id=run.id)
RunDetails(dd_run).show()
```

## <a name="visualize-drift-metrics"></a>Vizualizujte metriky driftu

<a name="metrics"></a>

Po odeslání datadriftDetector spustit, jste schopni vidět drift metriky, které jsou uloženy v každé iteraci spuštění pro úlohu drift dat:


|Metrika|Popis|
--|--|
wasserstein_distance|Statistická vzdálenost definovaná pro jednorozměrné numerické rozdělení.|
energy_distance|Statistická vzdálenost definovaná pro jednorozměrné numerické rozdělení.|
datadrift_coefficient|Vypočítáno podobně jako Matoušův korelační koeficient, ale tento výstup je reálné číslo v rozsahu od 0 do 1. V kontextu driftu 0 označuje žádný posun a 1 označuje maximální posun.|
datadrift_contribution|Funkce význam funkcí, které přispívají k drift.|

Metriky driftu lze zobrazit několika způsoby:

* `RunDetails`Použijte [widget Jupyter](https://docs.microsoft.com/python/api/azureml-widgets/azureml.widgets?view=azure-ml-py).
* Použijte [`get_metrics()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py#get-metrics-name-none--recursive-false--run-type-none--populate-false-) funkci na `datadrift` libovolném spuštěném objektu.
* Zobrazte metriky v části **Modely** vašeho pracovního prostoru ve [studiu Azure Machine Learning](https://ml.azure.com).

Následující příklad Pythonu ukazuje, jak vykreslit relevantní metriky posunu dat. Vrácené metriky můžete použít k vytvoření vlastních vizualizací:

```python
# start and end are datetime objects 
drift_metrics = datadrift.get_output(start_time=start, end_time=end)

# Show all data drift result figures, one per service.
# If setting with_details is False (by default), only the data drift magnitude will be shown; if it's True, all details will be shown.
drift_figures = datadrift.show(with_details=True)
```

![Zobrazení posunu dat zjištěným Azure Machine Learningem](./media/how-to-monitor-data-drift/drift_show.png)


## <a name="schedule-data-drift-scans"></a>Plánování posunových prohledávek dat 

Když povolíte detekci posunu dat, DataDriftDetector je spuštěn na zadanou, naplánované frekvenci. Pokud datadrift_coefficient dosáhne daného `drift_threshold`, je při každém naplánovaném spuštění odeslán e-mail. 

```python
datadrift.enable_schedule()
datadrift.disable_schedule()
```

Konfigurace detektoru posunu dat je vidět v části **Modely** na kartě **Podrobnosti** ve vašem pracovním prostoru ve [studiu Azure Machine Learning](https://ml.azure.com).

[![Posun dat ve Studiu strojového učení Azure](./media/how-to-monitor-data-drift/drift-config.png)](media/how-to-monitor-data-drift/drift-config-expanded.png)

## <a name="view-results-in-your-azure-machine-learning-studio"></a>Zobrazení výsledků ve vašem Azure Machine Learning studio

Chcete-li zobrazit výsledky v pracovním prostoru ve [studiu Azure Machine Learning](https://ml.azure.com), přejděte na stránku modelu. Na kartě podrobnosti modelu je zobrazena konfigurace posunu dat. Karta **Posun dat** je teď k dispozici vizualizaci metriky posundat dat. 

[![Posun dat ve Studiu strojového učení Azure](./media/how-to-monitor-data-drift/drift-ui.png)](media/how-to-monitor-data-drift/drift-ui-expanded.png)

## <a name="receiving-drift-alerts"></a>Příjem upozornění drift

Nastavením prahové hodnoty upozornění koeficientu driftu a poskytnutím e-mailové adresy se e-mailové upozornění [Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/overview) automaticky odešle, kdykoli je koeficient driftu nad prahovou hodnotou. 

Abyste mohli nastavit vlastní výstrahy a akce, všechny metriky posunu dat jsou uloženy v prostředku [Application Insights,](how-to-enable-app-insights.md) který byl vytvořen spolu s pracovním prostorem Azure Machine Learning. Můžete sledovat odkaz v e-mailové výstraze na dotaz Application Insights.

![Upozornění na e-mailový posun dat](./media/how-to-monitor-data-drift/drift_email.png)

## <a name="retrain-your-model-after-drift"></a>Přeškolit model po driftu

Když posun dat negativně ovlivňuje výkon nasazeného modelu, je čas přeškolit model. Chcete-li tak učinit, pokračujte následujícími kroky.

* Prozkoumejte shromážděná data a připravte data pro trénování nového modelu.
* Rozdělte jej na data vlaku/testu.
* Trénování modelu znovu pomocí nových dat.
* Vyhodnoťte výkon nově generovaného modelu.
* Nasaďte nový model, pokud je výkon lepší než produkční model.

## <a name="next-steps"></a>Další kroky

* Úplný příklad použití posunu dat najdete v tématu [Poznámkový blok datového posunu Azure ML](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/monitor-models/data-drift/drift-on-aks.ipynb). Tento poznámkový blok Jupyter ukazuje pomocí [Azure Open Dataset](https://docs.microsoft.com/azure/open-datasets/overview-what-are-open-datasets) trénovat model předpovědět počasí, nasadit do AKS a sledovat posun dat. 

* Detekujte posun dat pomocí [monitorů datových sad](how-to-monitor-datasets.md).

* Velmi bychom ocenili vaše dotazy, komentáře nebo návrhy, protože posun dat směřuje k obecné dostupnosti. Použijte tlačítko zpětné vazby produktu níže! 
