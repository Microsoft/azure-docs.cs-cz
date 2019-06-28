---
title: Jak detekovat odchylek dat (Preview) v nasazení AKS
titleSuffix: Azure Machine Learning service
description: Zjistěte, jak detekovat odchylek data ve službě Azure Kubernetes Service nasadit modely ve službě Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: copeters
author: cody-dkdc
ms.date: 06/20/2019
ms.openlocfilehash: e4deeab28fb643ff32624ba9dd16574e621f508c
ms.sourcegitcommit: 08138eab740c12bf68c787062b101a4333292075
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/22/2019
ms.locfileid: "67332722"
---
# <a name="how-to-detect-data-drift-preview-on-models-deployed-to-azure-kubernetes-service"></a>Jak detekovat odchylek dat (preview) v modelech nasazení do služby Azure Kubernetes Service
V tomto článku se dozvíte, jak monitorovat [data odchylek](concept-data-drift.md) mezi trénovací datové sady a odvozování data nasazeného modelu. 

Posun dat jeden z hlavních důvodů je, kde přesnost modelu zhoršení v čase. To se stane, když data obsluhovat modelů v produkčním prostředí se liší od data použitá k natrénování modelu. Služba Azure Machine Learning můžete sledovat data odchylek pomocí detektoru odchylek Data. Pokud se zjistí odchylek služby můžete posílat upozornění.  

> [!Note]
> Tato služba je ve verzi (Preview) a omezené možnosti konfigurace. Podrobnosti najdete na naší [dokumentace k rozhraní API](https://docs.microsoft.com/python/api/azureml-contrib-datadrift/?view=azure-ml-py) a [zpráva k vydání verze](azure-machine-learning-release-notes.md) podrobnosti a aktualizace. 

Pomocí služby Azure Machine Learning můžete monitorovat vstupy modelu nasazené v AKS a porovnání těchto dat na trénovací datové sady pro model. V pravidelných intervalech, jsou data odvození [vytváření snímků a Profilovat](how-to-explore-prepare-data.md), pak výpočtech datové sady standardních hodnot k vytvoření analýza odchylek dat, který: 

+ Měří velikost dat odchylek, volá koeficient odchylek.
+ Opatření data odchylek příspěvek funkcí informací, které funkce způsobila odchylek data.
+ Opatření vzdálenost metriky. Aktuálně jsou vypočítány Wasserstein a vzdáleností energií.
+ Měří distribuce funkcí. Aktuálně odhad hustotou jádra a histogramy.
+ Posílejte upozornění na data odchylek e-mailem.

Podrobnosti o tom, jak se počítají tyto metriky, najdete v článku [data odchylek koncept](concept-data-drift.md) článku.

## <a name="prerequisites"></a>Požadavky

- Pokud nemáte předplatné Azure, vytvořte si bezplatný účet, před zahájením. Zkuste [bezplatné nebo placené verzi aplikace služby Azure Machine Learning](https://aka.ms/AMLFree) ještě dnes.

- Pracovní prostor služby Azure Machine Learning service a Azure Machine Learning SDK for nainstalovaný Python. Další informace o získání těchto nezbytných podmínkách používání [jak nakonfigurovat prostředí pro vývoj](how-to-configure-environment.md) dokumentu.

- [Nastavení prostředí](how-to-configure-environment.md)a pak nainstalujte odchylek data sady SDK, pomocí následujícího příkazu:

    ```
    pip install azureml-contrib-datadrift
    ```

- Vytvoření [datovou sadu](how-to-create-register-datasets.md) z vašeho modelu trénovací data.

- Zadejte trénovací datové sady při [registrace](concept-model-management-and-deployment.md) modelu. Následující příklad ukazuje použití `datasets` parametr k určení trénovací datové sady:

    ```python
    model = Model.register(model_path=model_file,
                        model_name=model_name,
                        workspace=ws,
                        datasets=datasets)

    print(model_name, image_name, service_name, model)
    ```

- Nastavit [shromažďování dat modelu](how-to-enable-data-collection.md) shromažďování dat z modelu nasazení služby AKS a potvrdit data se shromažďují v `modeldata` kontejner objektů blob.

## <a name="import-dependencies"></a>Importujte závislosti 
Importujte závislosti používané v tomto průvodci:

```python
# Azure ML service packages 
from azureml.core import Experiment, Run, RunDetails
from azureml.contrib.datadrift import DataDriftDetector, AlertConfiguration
``` 

## <a name="configure-data-drift"></a>Konfigurace dat odchylek 

Následující příklad Python ukazuje konfiguraci `DataDriftDetector` objektu:

```python
# if email address is specified, setup AlertConfiguration
alert_config = AlertConfiguration('your_email@contoso.com')

# create a new DatadriftDetector object
datadrift = DataDriftDetector.create(ws, model.name, model.version, services, frequency="Day", alert_config=alert_config)
    
print('Details of Datadrift Object:\n{}'.format(datadrift))
```

Další informace najdete v tématu [DataDrift](https://docs.microsoft.com/python/api/azureml-contrib-datadrift/?view=azure-ml-py) odkaz.

## <a name="submit-a-datadriftdetector-run"></a>Odeslat DataDriftDetector spuštění

S DataDriftDetector nakonfigurovaný, můžete odeslat [odchylek data spusťte](https://docs.microsoft.com/python/api/azureml-contrib-datadrift/azureml.contrib.datadrift.datadriftdetector%28class%29?view=azure-ml-py#run-target-date--services--compute-target-name-none--create-compute-target-false--feature-list-none--drift-threshold-none-) v určitém dni pro model. 

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

## <a name="get-data-drift-analysis-results"></a>Výsledky analýzy dat odchylek

Následující příklad Python ukazuje, jak k vykreslení metrik odchylek relevantní data. Metriky vrácené můžete použít k vytváření vlastních vizualizací:

```python
# start and end are datetime objects 
drift_metrics = datadrift.get_output(start_time=start, end_time=end)

# Show all data drift result figures, one per serivice.
# If setting with_details is False (by default), only the data drift magnitude will be shown; if it's True, all details will be shown.
drift_figures = datadrift.show(with_details=True)
```

![Zobrazit data odchylek](media/how-to-monitor-data-drift/drift_show.png)

Podrobnosti o tom, která se zpracovávají, najdete v článku [data odchylek koncept](concept-data-drift.md) článku.

## <a name="schedule-data-drift-detection"></a>Plán zjišťování odchylek dat 

Povolování plánu odchylek dat provádí DataDriftDetector spouštět v zadaných intervalech. Pokud koeficient odchylek překračuje danou prahovou hodnotu, se odešle e-mail. 

```python
datadrift.enable_schedule()
datadrift.disable_schedule()
```

Konfigurace sady detektor odchylek data lze zobrazit na stránce s podrobnostmi modelu na webu Azure Portal.

![Data odchylek konfigurace webu Azure portal](media/how-to-monitor-data-drift/drift_config.png)

## <a name="view-results-in-azure-ml-workspace-ui"></a>Zobrazení výsledků v uživatelském prostředí služby Azure ML pracovního prostoru

Chcete-li zobrazit výsledky v uživatelském prostředí služby Azure ML pracovní prostor, přejděte na stránku modelu. Na kartě Podrobnosti v modelu je znázorněna konfigurace odchylek data. Na kartě "odchylek dat (Preview)' je teď dostupná vizualizaci metrik odchylek data. 

![Posun dat webu Azure portal](media/how-to-monitor-data-drift/drift_ui.png)

## <a name="setting-up-alerts"></a>Nastavení výstrah 

Nastavením koeficient odchylek prahová hodnota pro upozornění a poskytuje tak e-mailovou adresu [Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/overview) Pokud koeficient odchylek je nad prahovou hodnotou je odesláno e-mailové upozornění. Všechny metriky odchylek data se ukládají v prostředku app insights přidružený pracovnímu prostoru Azure Machine Learning service si můžete nastavit vlastní výstrahy nebo akce. Použijte odkaz v e-mailové upozornění na dotaz app insights.

![E-mailová upozornění na data odchylek](media/how-to-monitor-data-drift/drift_email.png)

## <a name="next-steps"></a>Další postup

* Úplný příklad použití odchylek data, najdete v článku [data Azure ML odchylek Poznámkový blok](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/data-drift/azure-ml-datadrift.ipynb). Tento poznámkový blok Jupyter ukazuje, jak pomocí [otevřete datovou sadu Azure](https://docs.microsoft.com/azure/open-datasets/overview-what-are-open-datasets) trénování modelu k předpovědi počasí, jeho nasazení do AKS a monitorování dat posun. 

* Výrazně Uvítáme vaše dotazy, komentáře nebo návrhy při přesunu dat Posun směrem k obecné dostupnosti. Pomocí tlačítka produktu níže! 
