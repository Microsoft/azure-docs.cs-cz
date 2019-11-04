---
title: Interpretace modelu v automatizovaném ML
titleSuffix: Azure Machine Learning
description: Naučte se vysvětlit, proč vám model automatizovaného ML předpovědi pomocí sady SDK Azure Machine Learning. Dá se použít během školení a odvození, abyste porozuměli tomu, jak model určuje důležitost funkcí a zpřístupňuje předpovědi.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: mesameki
author: mesameki
ms.reviewer: trbye
ms.date: 10/25/2019
ms.openlocfilehash: 2c9df55eb319dd45281eca4684c79d83dc6ef933
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2019
ms.locfileid: "73515327"
---
# <a name="model-interpretability-for-automated-ml-models"></a>Interpretace modelů pro automatizované modely ML

[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

V tomto postupu se naučíte, jak povolit funkci interpretace v automatizovaném strojovém učení pomocí služby Azure Machine Learning. Automatizované ML vám umožní pochopit důležitost nezpracovaných i inženýrů funkcí. Aby bylo možné použít interpretaci modelu, nastavte `model_explainability=True` v objektu `AutoMLConfig`.  

V tomto článku se seznámíte s následujícími úlohami:

* Výklad během školení pro nejlepší model nebo libovolný model
* Povolení vizualizací pro pomoc při zjišťování vzorů v datech a vysvětlení
* Výklad při odvozování nebo bodování

## <a name="prerequisites"></a>Předpoklady

* Spusťte `pip install azureml-interpret azureml-contrib-interpret` a získejte potřebné balíčky pro funkce pro vyhodnocení.
* Tento článek předpokládá znalost sestavování automatizovaných experimentů ML. Informace o použití automatizovaného ML v sadě SDK najdete v [kurzu](tutorial-auto-train-models.md) nebo s [postupem](how-to-configure-auto-train.md) .
 
## <a name="interpretability-during-training-for-the-best-model"></a>Možnost interpretace během školení pro nejlepší model 

Načtěte vysvětlení z `best_run`, které obsahuje vysvětlení pro inženýrské funkce a nezpracované funkce. 

### <a name="download-engineered-feature-importance-from-artifact-store"></a>Stažení důležitosti funkcí z úložiště artefaktů

Pomocí `ExplanationClient` můžete stáhnout vydaná Vysvětlení funkcí z úložiště artefaktů best_run. Pro získání vysvětlení pro sadu funkcí s nezpracovanými funkcemi `raw=True`. 

```python
from azureml.contrib.interpret.explanation.explanation_client import ExplanationClient

client = ExplanationClient.from_run(best_run)
engineered_explanations = client.download_model_explanation(raw=False)
print(engineered_explanations.get_feature_importance_dict())
```

## <a name="interpretability-during-training-for-any-model"></a>Výklad během školení pro libovolný model 

V této části se dozvíte, jak vypočítat vysvětlení modelu a vizualizovat vysvětlení. Kromě načítání existujícího vysvětlení modelu pro automatizovaný model ML můžete také vysvětlit svůj model pomocí různých testovacích dat. Následující kroky vám umožní vypočítat a vizualizovat důležitost navržených funkcí a význam nezpracovaných funkcí na základě vašich testovacích dat.

### <a name="retrieve-any-other-automl-model-from-training"></a>Načíst jakýkoli jiný AutoML model z školení

```python
automl_run, fitted_model = local_run.get_output(metric='r2_score')
```

### <a name="setup-the-model-explanations"></a>Nastavení vysvětlení modelu

Fitted_model může vygenerovat následující položky, které se budou používat k získání podrobných a nezpracovaných Vysvětlení funkcí pomocí automl_setup_model_explanations:

* Natrénuje data z ukázek výukových ukázek/ukázek testů
* Shromažďování seznamů zpracovaných a nezpracovaných názvů funkcí
* Hledání tříd ve sloupci s popisky ve scénářích klasifikace

Automl_explainer_setup_obj obsahuje všechny struktury ze seznamu výše.

```python
from azureml.train.automl.automl_explain_utilities import AutoMLExplainerSetupClass, automl_setup_model_explanations

automl_explainer_setup_obj = automl_setup_model_explanations(fitted_model, X=X_train, 
                                                             X_test=X_test, y=y_train, 
                                                             task='classification')
```
### <a name="initialize-the-mimic-explainer-for-feature-importance"></a>Inicializovat vysvětlující podobnou funkci pro důležitost funkcí

Pro vysvětlení modelů AutoML použijte třídu `MimicWrapper`. MimicWrapper se dá inicializovat pomocí parametrů pro objekt nastavení pro vysvětlení, váš pracovní prostor a model LightGBM, který slouží jako náhradní model a vysvětluje model automatizovaného ML (fitted_model tady). MimicWrapper také převezme objekt automl_run, kde se nahrají nezpracovaná a inženýrská vysvětlení.

```python
from azureml.interpret.mimic.models.lightgbm_model import LGBMExplainableModel
from azureml.interpret.mimic_wrapper import MimicWrapper

explainer = MimicWrapper(ws, automl_explainer_setup_obj.automl_estimator, LGBMExplainableModel, 
                         init_dataset=automl_explainer_setup_obj.X_transform, run=automl_run,
                         features=automl_explainer_setup_obj.engineered_feature_names, 
                         feature_maps=[automl_explainer_setup_obj.feature_map],
                         classes=automl_explainer_setup_obj.classes)
```

### <a name="use-mimicexplainer-for-computing-and-visualizing-engineered-feature-importance"></a>Použití MimicExplainer pro výpočet a vizualizaci důležitosti funkcí navržených pro inženýry

Metodu vysvětlit () v MimicWrapper lze volat pomocí transformovaných ukázek testů, aby bylo možné získat důležitost funkce pro vygenerované funkce vygenerované v rámci. ExplanationDashboard můžete použít také k zobrazení přerušované vizualizace hodnot pro hodnoty důležitosti funkcí vygenerovaných v rámci automatizovaného ML featurizers.

```python
from azureml.contrib.interpret.visualize import ExplanationDashboard
engineered_explanations = explainer.explain(['local', 'global'],              
                                            eval_dataset=automl_explainer_setup_obj.X_test_transform)

print(engineered_explanations.get_feature_importance_dict())
ExplanationDashboard(engineered_explanations, automl_explainer_setup_obj.automl_estimator, automl_explainer_setup_obj.X_test_transform)
```
### <a name="use-mimic-explainer-for-computing-and-visualizing-raw-feature-importance"></a>Použití napodobování vysvětlení a vizualizace nezpracované důležité funkce

Metoda vysvětlit () v MimicWrapper může být znovu volána pomocí transformovaných ukázek testů a nastavením `get_raw` na hodnotu true, aby bylo možné získat důležitost funkcí pro nezpracované funkce. Pomocí ExplanationDashboard můžete také zobrazit vizualizaci řídicích panelů pro hodnoty důležitosti funkcí nezpracovaných funkcí.

```python
from azureml.contrib.interpret.visualize import ExplanationDashboard

raw_explanations = explainer.explain(['local', 'global'], get_raw=True, 
                                     raw_feature_names=automl_explainer_setup_obj.raw_feature_names,
                                     eval_dataset=automl_explainer_setup_obj.X_test_transform)

print(raw_explanations.get_feature_importance_dict())
ExplanationDashboard(raw_explanations, automl_explainer_setup_obj.automl_pipeline, automl_explainer_setup_obj.X_test_raw)
```

### <a name="interpretability-during-inference"></a>Výklad při odvozování

V této části se dozvíte, jak zprovoznění model automatizovaného ML pomocí vysvětlujícího nástroje, který se použil k výpočtu vysvětlení v předchozí části.

### <a name="register-the-model-and-the-scoring-explainer"></a>Zaregistrujte model a vysvětlující hodnocení.

Pomocí `TreeScoringExplainer` můžete vytvořit vysvětlující bodování, který se použije k výpočtu nezpracovaných a navržených hodnot důležitosti funkcí v době odvození. Všimněte si, že vydáte vysvětlující hodnocení s feature_map, který byl vypočítán dříve. Feature_map bude používat vyhodnocování bodování, aby vrátil důležitost nezpracovaných funkcí.

V níže uvedeném kódu uložíte vysvětlující hodnocení a zaregistrujete model a hodnocení bodování pomocí služby Správa modelů.

```python
from azureml.interpret.scoring.scoring_explainer import TreeScoringExplainer, save

# Initialize the ScoringExplainer
scoring_explainer = TreeScoringExplainer(explainer.explainer, feature_maps=[automl_explainer_setup_obj.feature_map])

# Pickle scoring explainer locally
save(scoring_explainer, exist_ok=True)

# Register trained automl model present in the 'outputs' folder in the artifacts
original_model = automl_run.register_model(model_name='automl_model', 
                                           model_path='outputs/model.pkl')

# Register scoring explainer
automl_run.upload_file('scoring_explainer.pkl', 'scoring_explainer.pkl')
scoring_explainer_model = automl_run.register_model(model_name='scoring_explainer', model_path='scoring_explainer.pkl')
```

### <a name="create-the-conda-dependencies-for-setting-up-the-service"></a>Vytvoření závislostí conda pro nastavení služby

Dále vytvoříte závislosti prostředí potřebné v kontejneru pro nasazený model.

```python
from azureml.core.conda_dependencies import CondaDependencies 

azureml_pip_packages = [
    'azureml-interpret', 'azureml-train-automl', 'azureml-defaults'
]

myenv = CondaDependencies.create(conda_packages=['scikit-learn', 'pandas', 'numpy', 'py-xgboost<=0.80'],
                                 pip_packages=azureml_pip_packages,
                                 pin_sdk_version=True)

with open("myenv.yml","w") as f:
    f.write(myenv.serialize_to_string())

with open("myenv.yml","r") as f:
    print(f.read())

```

### <a name="deploy-the-service"></a>Nasazení služby

Nasaďte službu pomocí souboru conda a souboru bodování z předchozích kroků.

```python
from azureml.core.webservice import Webservice
from azureml.core.model import InferenceConfig
from azureml.core.webservice import AciWebservice
from azureml.core.model import Model

aciconfig = AciWebservice.deploy_configuration(cpu_cores=1, 
                                               memory_gb=1, 
                                               tags={"data": "Bank Marketing",  
                                                     "method" : "local_explanation"}, 
                                               description='Get local explanations for Bank marketing test data')

inference_config = InferenceConfig(runtime= "python", 
                                   entry_script="score_local_explain.py",
                                   conda_file="myenv.yml")

# Use configs and models generated above
service = Model.deploy(ws, 'model-scoring', [scoring_explainer_model, original_model], inference_config, aciconfig)
service.wait_for_deployment(show_output=True)
```

### <a name="inference-using-test-data"></a>Odvození pomocí testovacích dat

Odvození pomocí některých testovacích dat zobrazuje předpovězenou hodnotu z automatizovaného modelu ML a zobrazuje důležitost navržených funkcí pro předpokládanou hodnotu a důležitost nezpracovaných funkcí pro předpokládanou hodnotu.

```python
if service.state == 'Healthy':
    # Serialize the first row of the test data into json
    X_test_json = X_test[:1].to_json(orient='records')
    print(X_test_json)
    # Call the service to get the predictions and the engineered and raw explanations
    output = service.run(X_test_json)
    # Print the predicted value
    print(output['predictions'])
    # Print the engineered feature importances for the predicted value
    print(output['engineered_local_importance_values'])
    # Print the raw feature importances for the predicted value
    print(output['raw_local_importance_values'])
```

### <a name="visualizations-to-aid-you-in-the-discovery-of-patterns-in-data-and-explanations-at-training-time"></a>Vizualizace, které pomáhají při zjišťování vzorů v datech a vysvětlení v době školení

Graf důležitost funkcí můžete také vizualizovat v pracovním prostoru v [Azure Machine Learning Studiu](https://ml.azure.com). Po dokončení automatizovaného běhu ML budete muset kliknout na Zobrazit podrobnosti modelu, které vás zajímají na konkrétní spuštění. Odsud kliknete na kartu vysvětlení a zobrazí se řídicí panel vizualizace vysvětlení. 

[Architektura interpretace ![Machine Learning](./media/machine-learning-interpretability-explainability/automl-explainability.png)](./media/machine-learning-interpretability-explainability/automl-explainability.png#lightbox)

## <a name="next-steps"></a>Další kroky

Další informace o tom, jak je možné povolit vysvětlení modelu a důležitost funkcí v jiných oblastech sady SDK mimo automatizované strojové učení, najdete v článku [konceptu](how-to-machine-learning-interpretability.md) o výkladu.