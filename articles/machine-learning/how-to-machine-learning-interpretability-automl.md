---
title: Vysvětlení v automatizovaných ML (Preview)
titleSuffix: Azure Machine Learning
description: Naučte se, jak získat vysvětlení toho, jak model automatizovaného ML určuje důležitost funkcí a že při použití Azure Machine Learning SDK je předpovědi.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to, automl, responsible-ml
ms.author: mithigpe
author: minthigpen
ms.date: 07/09/2020
ms.openlocfilehash: 709c85bed4a028c6c168c79cd9fffd6b7b40cb68
ms.sourcegitcommit: 49ea056bbb5957b5443f035d28c1d8f84f5a407b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/09/2021
ms.locfileid: "100008039"
---
# <a name="interpretability-model-explanations-in-automated-machine-learning-preview"></a>Interpretovatelnost: Vysvětlení modelů v rámci automatizovaného strojového učení (Preview)



V tomto článku se dozvíte, jak získat vysvětlení automatizovaného strojového učení (AutoML) v Azure Machine Learning. AutoML pomáhá pochopit důležitost funkcí modelů, které jsou vygenerovány. 

Všechny verze sady SDK po 1.0.85 `model_explainability=True` výchozím nastavení. V sadě SDK verze 1.0.85 a starších verzích je potřeba nastavit `model_explainability=True` v objektu, aby `AutoMLConfig` bylo možné použít interpretaci modelu. 

V tomto článku získáte informace o těchto tématech:

- Během školení proveďte výklad pro nejlepší model nebo model.
- Povolit vizualizace, které vám pomůžou zobrazit vzory v datech a vysvětlení.
- Implementujte výklad při odvozování nebo bodování.

## <a name="prerequisites"></a>Požadavky

- Funkce pro vyhodnocení. `pip install azureml-interpret`Pro získání potřebného balíčku spusťte příkaz.
- Znalosti o sestavování AutoML experimentů. Další informace o tom, jak používat sadu Azure Machine Learning SDK, získáte v tomto [kurzu regresní model](tutorial-auto-train-models.md) nebo v tématu Jak [nakonfigurovat experimenty AutoML](how-to-configure-auto-train.md).

## <a name="interpretability-during-training-for-the-best-model"></a>Možnost interpretace během školení pro nejlepší model

Načtěte vysvětlení z `best_run` , který obsahuje vysvětlení pro nezpracované i inženýrské funkce.

> [!NOTE]
> Interpretace, nejlepší vysvětlení modelu, není k dispozici pro experimenty prognózy automatických ML, které doporučují následující algoritmy jako nejlepší model: 
> * TCNForecaster
> * AutoArima
> * ExponentialSmoothing
> * Prophet
> * Průměr 
> * Naive
> * Sezónní průměr 
> * Sezónní Naive

### <a name="download-the-engineered-feature-importances-from-the-best-run"></a>Stažení nejdůležitějších funkcí z nejlepšího běhu

Můžete použít `ExplanationClient` ke stažení vysvětlivek k funkcím z úložiště artefaktů `best_run` . 

```python
from azureml.interpret import ExplanationClient

client = ExplanationClient.from_run(best_run)
engineered_explanations = client.download_model_explanation(raw=False)
print(engineered_explanations.get_feature_importance_dict())
```

### <a name="download-the-raw-feature-importances-from-the-best-run"></a>Stáhněte si důležitost nezpracovaných funkcí z nejlepšího běhu

Můžete použít `ExplanationClient` ke stažení nezpracovaných Vysvětlení funkcí z úložiště artefaktů `best_run` .

```python
from azureml.interpret import ExplanationClient

client = ExplanationClient.from_run(best_run)
raw_explanations = client.download_model_explanation(raw=True)
print(raw_explanations.get_feature_importance_dict())
```

## <a name="interpretability-during-training-for-any-model"></a>Výklad během školení pro libovolný model 

Při výpočtu vysvětlení modelu a jejich vizualizaci nejsou omezeny na existující vysvětlení modelu pro AutoML model. Můžete také získat vysvětlení pro váš model s různými testovacími daty. Postup v této části ukazuje, jak vypočítat a vizualizovat důležitost navržených funkcí na základě vašich testovacích dat.

### <a name="retrieve-any-other-automl-model-from-training"></a>Načíst jakýkoli jiný AutoML model z školení

```python
automl_run, fitted_model = local_run.get_output(metric='accuracy')
```

### <a name="set-up-the-model-explanations"></a>Nastavení vysvětlení modelu

Použijte `automl_setup_model_explanations` k získání vydaných technik a nezpracovaných vysvětlení. `fitted_model`Může generovat následující položky:

- Doporučená data z výukových nebo testovacích ukázek
- Seznamy názvů zpracovaných funkcí
- Najítelné třídy v popisku sloupce ve scénářích klasifikace

`automl_explainer_setup_obj`Obsahuje všechny struktury ze seznamu výše.

```python
from azureml.train.automl.runtime.automl_explain_utilities import automl_setup_model_explanations

automl_explainer_setup_obj = automl_setup_model_explanations(fitted_model, X=X_train, 
                                                             X_test=X_test, y=y_train, 
                                                             task='classification')
```

### <a name="initialize-the-mimic-explainer-for-feature-importance"></a>Inicializovat vysvětlující podobnou funkci pro důležitost funkcí

K vygenerování vysvětlení pro modely AutoML použijte `MimicWrapper` třídu. MimicWrapper můžete inicializovat pomocí těchto parametrů:

- Objekt nastavení pro vysvětlení
- Váš pracovní prostor
- Náhradní model pro vysvětlení `fitted_model` modelu AutoML

MimicWrapper také převezme `automl_run` objekt, do kterého se nahrají inženýrská vysvětlení.

```python
from azureml.interpret import MimicWrapper

# Initialize the Mimic Explainer
explainer = MimicWrapper(ws, automl_explainer_setup_obj.automl_estimator,
                         explainable_model=automl_explainer_setup_obj.surrogate_model, 
                         init_dataset=automl_explainer_setup_obj.X_transform, run=automl_run,
                         features=automl_explainer_setup_obj.engineered_feature_names, 
                         feature_maps=[automl_explainer_setup_obj.feature_map],
                         classes=automl_explainer_setup_obj.classes,
                         explainer_kwargs=automl_explainer_setup_obj.surrogate_model_params)
```

### <a name="use-mimic-explainer-for-computing-and-visualizing-engineered-feature-importance"></a>Vysvětlete nepodobující se výpočetní výkon a vizualizace důležitosti funkcí v inženýrech

Můžete zavolat `explain()` metodu v MimicWrapper pomocí transformovaných ukázek testů, abyste získali důležitost funkce pro vygenerované funkce v vygenerovaných analýzách. Můžete se také přihlásit k [Azure Machine Learning Studio](https://ml.azure.com/) , abyste zobrazili vizualizaci řídicích panelů hodnot pro vygenerované inženýrské funkce podle AutoML featurizers.

```python
engineered_explanations = explainer.explain(['local', 'global'], eval_dataset=automl_explainer_setup_obj.X_test_transform)
print(engineered_explanations.get_feature_importance_dict())
```

### <a name="use-mimic-explainer-for-computing-and-visualizing-raw-feature-importance"></a>Použití napodobování vysvětlení a vizualizace nezpracované důležité funkce

Můžete zavolat `explain()` metodu v MimicWrapper pomocí transformovaných ukázek testů, abyste získali důležitost funkcí pro nezpracované funkce. V [Machine Learning Studio](https://ml.azure.com/)můžete zobrazit vizualizaci řídicích panelů pro hodnoty důležitosti funkcí nezpracovaných funkcí.

```python
raw_explanations = explainer.explain(['local', 'global'], get_raw=True,
                                     raw_feature_names=automl_explainer_setup_obj.raw_feature_names,
                                     eval_dataset=automl_explainer_setup_obj.X_test_transform,
                                     raw_eval_dataset=automl_explainer_setup_obj.X_test_raw)
print(raw_explanations.get_feature_importance_dict())
```

## <a name="interpretability-during-inference"></a>Výklad při odvozování

V této části se dozvíte, jak zprovoznění model AutoML pomocí vysvětlujícího objektu, který se použil k výpočtu vysvětlení v předchozí části.

### <a name="register-the-model-and-the-scoring-explainer"></a>Zaregistrujte model a vysvětlující hodnocení.

Pomocí nástroje `TreeScoringExplainer` můžete vytvořit vysvětlující bodování, který bude počítat hodnoty důležitosti funkcí v čase odvození. Můžete inicializovat vysvětlující bodování s `feature_map` dříve vypočítaným vypočítaným. 

Uložte vysvětlující hodnocení a pak zaregistrujte model a hodnocení bodování pomocí služby Správa modelů. Spusťte následující kód:

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

Dále vytvořte nezbytné závislosti prostředí v kontejneru pro nasazený model. Upozorňujeme, že služba AzureML-Defaults s verzí >= 1.0.45 musí být uvedená jako závislost PIP, protože obsahuje funkce potřebné k hostování modelu jako webové služby.

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

### <a name="create-the-scoring-script"></a>Vytvoření hodnoticího skriptu

Napsat skript, který načte model a vytvoří předpovědi a vysvětlení založené na nové dávce dat.

```python
%%writefile score.py
import joblib
import pandas as pd
from azureml.core.model import Model
from azureml.train.automl.runtime.automl_explain_utilities import automl_setup_model_explanations


def init():
    global automl_model
    global scoring_explainer

    # Retrieve the path to the model file using the model name
    # Assume original model is named automl_model
    automl_model_path = Model.get_model_path('automl_model')
    scoring_explainer_path = Model.get_model_path('scoring_explainer')

    automl_model = joblib.load(automl_model_path)
    scoring_explainer = joblib.load(scoring_explainer_path)


def run(raw_data):
    data = pd.read_json(raw_data, orient='records')
    # Make prediction
    predictions = automl_model.predict(data)
    # Setup for inferencing explanations
    automl_explainer_setup_obj = automl_setup_model_explanations(automl_model,
                                                                 X_test=data, task='classification')
    # Retrieve model explanations for engineered explanations
    engineered_local_importance_values = scoring_explainer.explain(automl_explainer_setup_obj.X_test_transform)
    # Retrieve model explanations for raw explanations
    raw_local_importance_values = scoring_explainer.explain(automl_explainer_setup_obj.X_test_transform, get_raw=True)
    # You can return any data type as long as it is JSON-serializable
    return {'predictions': predictions.tolist(),
            'engineered_local_importance_values': engineered_local_importance_values,
            'raw_local_importance_values': raw_local_importance_values}
```

### <a name="deploy-the-service"></a>Nasazení služby

Nasaďte službu pomocí souboru conda a souboru bodování z předchozích kroků.

```python
from azureml.core.webservice import Webservice
from azureml.core.webservice import AciWebservice
from azureml.core.model import Model, InferenceConfig
from azureml.core.environment import Environment

aciconfig = AciWebservice.deploy_configuration(cpu_cores=1,
                                               memory_gb=1,
                                               tags={"data": "Bank Marketing",  
                                                     "method" : "local_explanation"},
                                               description='Get local explanations for Bank marketing test data')
myenv = Environment.from_conda_specification(name="myenv", file_path="myenv.yml")
inference_config = InferenceConfig(entry_script="score_local_explain.py", environment=myenv)

# Use configs and models generated above
service = Model.deploy(ws,
                       'model-scoring',
                       [scoring_explainer_model, original_model],
                       inference_config,
                       aciconfig)
service.wait_for_deployment(show_output=True)
```

### <a name="inference-with-test-data"></a>Odvození s testovacími daty

Odvození s některými testovacími daty pro zobrazení předpovězené hodnoty z modelu AutoML, která se aktuálně podporuje jenom v sadě Azure Machine Learning SDK. Podívejte se na důležité funkce, které přispívají k předpovězené hodnotě. 

```python
if service.state == 'Healthy':
    # Serialize the first row of the test data into json
    X_test_json = X_test[:1].to_json(orient='records')
    print(X_test_json)
    # Call the service to get the predictions and the engineered explanations
    output = service.run(X_test_json)
    # Print the predicted value
    print(output['predictions'])
    # Print the engineered feature importances for the predicted value
    print(output['engineered_local_importance_values'])
    # Print the raw feature importances for the predicted value
    print('raw_local_importance_values:\n{}\n'.format(output['raw_local_importance_values']))
```

### <a name="visualize-to-discover-patterns-in-data-and-explanations-at-training-time"></a>Vizualizace pro zjišťování vzorů v datech a vysvětlení v době školení

Graf důležitost funkcí můžete vizualizovat v pracovním prostoru v [Machine Learning Studio](https://ml.azure.com). Po dokončení spuštění AutoML vyberte **Zobrazit podrobnosti o modelu** pro zobrazení konkrétního běhu. Vyberte kartu **vysvětlení** a zobrazte řídicí panel vizualizace vysvětlení.

[![Machine Learning architektura pro interpretaci](./media/how-to-machine-learning-interpretability-automl/automl-explanation.png)](./media/how-to-machine-learning-interpretability-automl/automl-explanation.png#lightbox)

Další informace o vizualizacích řídicího panelu a konkrétních plochách najdete v [dokumentu s postupy pro interpretaci](how-to-machine-learning-interpretability-aml.md).

## <a name="next-steps"></a>Další kroky

Další informace o tom, jak můžete povolit vysvětlení modelu a důležitost funkcí v oblastech Azure Machine Learning sady SDK kromě automatizovaného strojového učení, najdete v [článku koncept o výkladu](how-to-machine-learning-interpretability.md).
