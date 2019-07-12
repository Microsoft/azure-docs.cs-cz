---
title: Zjišťování odchylek dat (Preview) v nasazení AKS
titleSuffix: Azure Machine Learning service
description: Zjišťování odchylek data ve službě Azure Kubernetes Service nasadit modely ve službě Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: copeters
author: cody-dkdc
ms.date: 07/08/2019
ms.openlocfilehash: 3b8152bde8b7e44dde1b0b9c82216333778f83da
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2019
ms.locfileid: "67806012"
---
# <a name="detect-data-drift-preview-on-models-deployed-to-azure-kubernetes-service-aks"></a>Zjišťování odchylek dat (preview) v modelech nasazení do Azure Kubernetes Service (AKS)

V tomto článku se dozvíte, jak se budou monitorovat data posun mezi datové sady pro trénování a odvozování data z nasazeného modelu. V rámci služby machine learning natrénované modely strojového učení zaznamenat snížení předpověď výkonu z důvodu odchylek. Ve službě Azure Machine Learning můžete monitorovat posun data a služby můžete odeslat e-mailové upozornění při zjištění odchylek.

## <a name="what-is-data-drift"></a>Co je odchylek dat?

Posun dat se stane, když data obsluhovat modelů v produkčním prostředí se liší od data použitá k natrénování modelu. Je jedním z hlavních důvodů kde přesnost modelu zhoršení v čase, tak data monitorování odchylek pomáhá odhalit potíže s výkonem modelu. 

## <a name="what-can-i-monitor"></a>Co můžete monitorovat?

Pomocí služby Azure Machine Learning můžete monitorovat vstupy modelu nasazené v AKS a porovnání těchto dat na trénovací datové sady pro model. V pravidelných intervalech, jsou data odvození [vytváření snímků a Profilovat](how-to-explore-prepare-data.md), pak výpočtech datové sady standardních hodnot k vytvoření analýza odchylek dat, který: 

+ Měří velikost dat odchylek, volá koeficient odchylek.
+ Opatření data odchylek příspěvek funkcí informací, které funkce způsobila odchylek data.
+ Opatření vzdálenost metriky. Aktuálně jsou vypočítány Wasserstein a vzdáleností energií.
+ Měří distribuce funkcí. Aktuálně odhad hustotou jádra a histogramy.
+ Posílejte upozornění na data odchylek e-mailem.

> [!Note]
> Tato služba je ve verzi (Preview) a omezené možnosti konfigurace. Podrobnosti najdete na naší [dokumentace k rozhraní API](https://docs.microsoft.com/python/api/azureml-contrib-datadrift/?view=azure-ml-py) a [zpráva k vydání verze](azure-machine-learning-release-notes.md) podrobnosti a aktualizace. 

### <a name="how-data-drift-is-monitored-in-azure-machine-learning-service"></a>Jak se monitoruje odchylek data ve službě Azure Machine Learning

Pomocí služby Azure Machine Learning, data odchylek je monitorovat prostřednictvím datové sady nebo nasazení. Ke sledování dat odchylek, datovou sadu standardních hodnot – obvykle trénovací datové sady pro model - určena. Druhý datová sada – obvykle modelu vstupních dat shromážděných z nasazení – je testován vůči datovou sadu standardních hodnot. Oba datové sady jsou [profilované](how-to-explore-prepare-data.md#explore-with-summary-statistics) a zadání dat odchylek monitorování služby. Ke zjišťování rozdílů mezi dvěma datovými sadami se trénuje model strojového učení. Výkon modelu je převedena na odchylek koeficient, která měří velikost odchylek mezi dvěma datovými sadami. Pomocí [model interpretability](machine-learning-interpretability-explainability.md), se zpracovávají funkce, které přispívají k koeficient odchylek. Z profilu datové sady jsou sledovány statistické informace o jednotlivých funkcí. 

## <a name="prerequisites"></a>Požadavky

- Předplatné Azure. Pokud ho nemáte, vytvořte si bezplatný účet, než začnete. Zkuste [bezplatné nebo placené verzi aplikace služby Azure Machine Learning](https://aka.ms/AMLFree) ještě dnes.

- Pracovní prostor služby Azure Machine Learning service a Azure Machine Learning SDK for nainstalovaný Python. Postupujte podle pokynů v [vytvořit pracovní prostor služby Azure Machine Learning](setup-create-workspace.md#sdk) můžete provádět následující:

    - Vytvoření prostředí Miniconda
    - Nainstalujte aplikaci Azure Machine Learning sady SDK pro Python
    - Vytvoření pracovního prostoru
    - Zápis do souboru konfigurace pracovního prostoru (aml_config/config.json).

- Nainstalujte odchylek data sady SDK, pomocí následujícího příkazu:

    ```shell
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

- [Povolit shromažďování dat modelu](how-to-enable-data-collection.md) shromažďování dat z modelu nasazení služby AKS a potvrdit data se shromažďují v `modeldata` kontejner objektů blob.

## <a name="configure-data-drift"></a>Konfigurace dat odchylek
Jak je znázorněno v následujícím příkladu Python konfiguraci odchylek data pro experiment, naimportujte závislosti. 

Tento příklad ukazuje konfiguraci [ `DataDriftDetector` ](https://docs.microsoft.com/python/api/azureml-contrib-datadrift/azureml.contrib.datadrift.datadriftdetector.datadriftdetector?view=azure-ml-py) objektu:

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

## <a name="submit-a-datadriftdetector-run"></a>Odeslat DataDriftDetector spuštění

S `DataDriftDetector` objekt nakonfigurovaný, můžete odeslat [odchylek data spusťte](https://docs.microsoft.com/python/api/azureml-contrib-datadrift/azureml.contrib.datadrift.datadriftdetector%28class%29?view=azure-ml-py#run-target-date--services--compute-target-name-none--create-compute-target-false--feature-list-none--drift-threshold-none-) v určitém dni pro model. Jako součást spuštění povolit tak, že nastavíte výstrahy DataDriftDetector `drift_threshold` parametru. Pokud [datadrift_coefficient](#metrics) je vyšší než dané `drift_threshold`, odešle e-mail.

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

## <a name="visualize-drift-metrics"></a>Vizualizujte metriky odchylek

<a name="metrics"></a>

Po odeslání vašich DataDriftDetector spustit, budete moct zobrazit odchylek metriky, které jsou uložené v každé spuštění iterace pro úlohu data odchylek:


|Metrika|Popis|
--|--|
wasserstein_distance|Statistické vzdálenost definované pro jednorozměrné číselné distribuci.|
energy_distance|Statistické vzdálenost definované pro jednorozměrné číselné distribuci.|
datadrift_coefficient|Podobně jako na Matthew korelačního koeficientu počítá, ale tímto výstupem je reálné číslo od 0 do 1. V rámci odchylek 0 znamená bez odchylek a 1 označuje maximální odchylek.|
datadrift_contribution|Funkce důležitost funkce, které přispívají k odchylek.|

Pokud chcete zobrazit metriky odchylek několika způsoby:

* Pomocí widgetu Jupyter.
* Použití [ `get_metrics()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py#get-metrics-name-none--recursive-false--run-type-none--populate-false-) funkce na všech `datadrift` spustit objekt.
* Zobrazení metrik na webu Azure Portal na modelu

Následující příklad Python ukazuje, jak k vykreslení metrik odchylek relevantní data. Metriky vrácené můžete použít k vytváření vlastních vizualizací:

```python
# start and end are datetime objects 
drift_metrics = datadrift.get_output(start_time=start, end_time=end)

# Show all data drift result figures, one per serivice.
# If setting with_details is False (by default), only the data drift magnitude will be shown; if it's True, all details will be shown.
drift_figures = datadrift.show(with_details=True)
```

![Zobrazit data odchylek detekovaných službou Azure Machine Learning](media/how-to-monitor-data-drift/drift_show.png)


## <a name="schedule-data-drift-scans"></a>Plán kontrol odchylek dat 

Když povolíte detekce odchylek dat, DataDriftDetector běží frekvencí tyto informace zadané, naplánované. Pokud datadrift_coefficient dosáhne daný `drift_threshold`, odešle e-mail s každou plánovaného spuštění. 

```python
datadrift.enable_schedule()
datadrift.disable_schedule()
```

Konfigurace sady detektor odchylek data lze zobrazit na stránce s podrobnostmi modelu na webu Azure Portal.

![Data odchylek konfigurace webu Azure portal](media/how-to-monitor-data-drift/drift_config.png)

## <a name="view-results-in-azure-ml-workspace-ui"></a>Zobrazení výsledků v uživatelském prostředí služby Azure ML pracovního prostoru

Chcete-li zobrazit výsledky v uživatelském prostředí služby Azure ML pracovní prostor, přejděte na stránku modelu. Na kartě Podrobnosti v modelu je znázorněna konfigurace odchylek data. Na kartě "odchylek dat (Preview)' je teď dostupná vizualizaci metrik odchylek data. 

![Posun dat webu Azure portal](media/how-to-monitor-data-drift/drift_ui.png)

## <a name="receiving-drift-alerts"></a>Přijímání oznámení odchylek

Nastavením koeficient odchylek prahová hodnota pro upozornění a poskytuje e-mailovou adresu [Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/overview) e-mailové upozornění se automaticky odesílají vždycky, když koeficient odchylek je nad prahovou hodnotou. 

Aby si můžete nastavit vlastní výstrahy a akce, všechny metriky odchylek dat jsou uloženy v [Application Insights](how-to-enable-app-insights.md) prostředek, který byl vytvořen spolu s pracovního prostoru služby Azure Machine Learning. Použijte odkaz v e-mailové upozornění na dotaz Application Insights.

![E-mailová upozornění na data odchylek](media/how-to-monitor-data-drift/drift_email.png)

## <a name="retrain-your-model-after-drift"></a>Přeučování modelu po odchylek

Když data odchylek negativně ovlivňuje výkon nasazený model, je čas přeučování modelu. Následující [ `diff()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#diff-rhs-dataset--compute-target-none--columns-none-
) metoda poskytuje počáteční přehled o tom, co se změnilo mezi staré a nové trénovací datové sady. 

```python
from azureml.core import Dataset

old_training_dataset.diff(new_training_dataset)
```

Na základě výstupu předchozího kódu, můžete přeučování modelu. Uděláte to tak, postupujte podle následujících kroků.

* Prozkoumejte shromážděná data a připravit data pro učení nový model.
* Ho rozdělte do dat pro trénování a testování.
* Trénování modelu znovu pomocí nová data.
* Vyhodnocení výkonu nově vytvořeného modelu.
* Nový model nasazení, pokud výkon je lepší než produkční modelu.

## <a name="next-steps"></a>Další postup

* Úplný příklad použití odchylek data, najdete v článku [data Azure ML odchylek Poznámkový blok](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/data-drift/azure-ml-datadrift.ipynb). Tento poznámkový blok Jupyter ukazuje, jak pomocí [otevřete datovou sadu Azure](https://docs.microsoft.com/azure/open-datasets/overview-what-are-open-datasets) trénování modelu k předpovědi počasí, jeho nasazení do AKS a monitorování dat posun. 

* Výrazně Uvítáme vaše dotazy, komentáře nebo návrhy při přesunu dat Posun směrem k obecné dostupnosti. Pomocí tlačítka produktu níže! 
