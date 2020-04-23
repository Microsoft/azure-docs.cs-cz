---
title: Vysvětlitelnost v automatizovaném ML
titleSuffix: Azure Machine Learning
description: Zjistěte, jak získat vysvětlení, jak váš automatizovaný model ML určuje důležitost funkce a dělá předpovědi při použití sady Azure Machine Learning SDK.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: mesameki
author: mesameki
ms.date: 03/11/2020
ms.openlocfilehash: e9155104905ae3e686a01b90cbcad2610b6f4c91
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2020
ms.locfileid: "82086415"
---
# <a name="interpretability-model-explanations-in-automated-machine-learning"></a>Interpretovatelnost: modelová vysvětlení v automatizovaném strojovém učení

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

V tomto článku se dozvíte, jak získat vysvětlení pro automatizované strojové učení (ML) v Azure Machine Learning. Automatizovaná funkce ML vám pomůže porozumět důležitosti navržených funkcí. 

Všechny verze sady SDK po 1.0.85 nastavené `model_explainability=True` ve výchozím nastavení. V sadě SDK verze 1.0.85 a `model_explainability=True` starších `AutoMLConfig` verzích musí uživatelé nastavit objekt, aby mohli používat interpretabilitu modelu. 

V tomto článku získáte informace o těchto tématech:

- Proveďte interpretabilitu během trénování pro nejlepší model nebo jakýkoli model.
- Povolte vizualizace, které vám pomohou zobrazit vzory v datech a vysvětleních.
- Implementujte interpretabilitu během odvození nebo vyhodnocování.

## <a name="prerequisites"></a>Požadované součásti

- Funkce interpretace. Spuštěním `pip install azureml-interpret azureml-contrib-interpret` získáte potřebné balíčky.
- Znalost vytváření automatizovaných experimentů ML. Další informace o tom, jak používat Azure Machine Learning SDK, dokončete tento [regresní model kurzu](tutorial-auto-train-models.md) nebo se podívejte, jak [nakonfigurovat automatizované experimenty ML](how-to-configure-auto-train.md).

## <a name="interpretability-during-training-for-the-best-model"></a>Interpretovatelnost během tréninku pro nejlepší model

Načíst vysvětlení `best_run`z , který obsahuje vysvětlení pro inženýrské funkce.

### <a name="download-engineered-feature-importance-from-artifact-store"></a>Stažení důležitosti navržených funkcí z úložiště artefaktů

Můžete použít `ExplanationClient` ke stažení inženýrství funkce vysvětlení z úložiště `best_run`artefaktů . 

```python
from azureml.explain.model._internal.explanation_client import ExplanationClient

client = ExplanationClient.from_run(best_run)
engineered_explanations = client.download_model_explanation(raw=False)
print(engineered_explanations.get_feature_importance_dict())
```

## <a name="interpretability-during-training-for-any-model"></a>Interpretovatelnost během tréninku pro jakýkoli model 

Když vypočítáte vysvětlení modelu a vizualizujete je, nejste omezeni na existující vysvětlení modelu pro automatizovaný model ML. Můžete také získat vysvětlení pro váš model s různými testovacími daty. Kroky v této části ukazují, jak vypočítat a vizualizovat důležitost navržených funkcí na základě testovacích dat.

### <a name="retrieve-any-other-automl-model-from-training"></a>Načtení jakéhokoli jiného modelu Automatického praní z trénování

```python
automl_run, fitted_model = local_run.get_output(metric='accuracy')
```

### <a name="set-up-the-model-explanations"></a>Nastavení vysvětlení modelu

Slouží `automl_setup_model_explanations` k získání navržených vysvětlení. Můžete `fitted_model` generovat následující položky:

- Doporučené údaje z vyškolených nebo zkušebních vzorků
- Seznamy názvů inženýrské funkce
- Vyhledatelné třídy ve sloupci označeném popiskem ve scénářích klasifikace

Obsahuje `automl_explainer_setup_obj` všechny struktury z výše uvedeného seznamu.

```python
from azureml.train.automl.runtime.automl_explain_utilities import automl_setup_model_explanations

automl_explainer_setup_obj = automl_setup_model_explanations(fitted_model, X=X_train, 
                                                             X_test=X_test, y=y_train, 
                                                             task='classification')
```

### <a name="initialize-the-mimic-explainer-for-feature-importance"></a>Inicializovat mimincovní vysvětlující funkci pro důležitost funkce

Chcete-li generovat vysvětlení pro modely `MimicWrapper` AutoML, použijte třídu. Mimimicwrapper můžete inicializovat pomocí těchto parametrů:

- Vysvětlující objekt nastavení
- Váš pracovní prostor
- Model LightGBM, který slouží jako `fitted_model` náhrada automatizovaného modelu ML

MimicWrapper také trvá `automl_run` objekt, kde bude nahrán anavržený vysvětlení.

```python
from azureml.explain.model.mimic.models.lightgbm_model import LGBMExplainableModel
from azureml.explain.model.mimic_wrapper import MimicWrapper

# Initialize the Mimic Explainer
explainer = MimicWrapper(ws, automl_explainer_setup_obj.automl_estimator, LGBMExplainableModel, 
                         init_dataset=automl_explainer_setup_obj.X_transform, run=automl_run,
                         features=automl_explainer_setup_obj.engineered_feature_names, 
                         feature_maps=[automl_explainer_setup_obj.feature_map],
                         classes=automl_explainer_setup_obj.classes)
```

### <a name="use-mimicexplainer-for-computing-and-visualizing-engineered-feature-importance"></a>Použití MimicExplainer pro výpočet a vizualizaci inženýrské funkce význam

Můžete volat `explain()` metodu v MimicWrapper s transformované zkušební vzorky získat důležitost funkce pro generované konstrukční funkce. Můžete také `ExplanationDashboard` zobrazit vizualizaci řídicího panelu hodnoty důležitosti funkce generovaných inženýrských funkcí pomocí automatizovaných ml featurizers.

```python
engineered_explanations = explainer.explain(['local', 'global'], eval_dataset=automl_explainer_setup_obj.X_test_transform)
print(engineered_explanations.get_feature_importance_dict())
```

### <a name="interpretability-during-inference"></a>Interpretovatelnost během odvození

V této části se dozvíte, jak zprovoznit automatizovaný model ML s vysvětlující, který byl použit k výpočtu vysvětlení v předchozí části.

### <a name="register-the-model-and-the-scoring-explainer"></a>Registrace modelu a vysvětlivače bodování

Pomocí `TreeScoringExplainer` můžete vytvořit vysvětlující vysvětlivku hodnocení, který vypočítá hodnoty důležitosti navržených funkcí v době odvození. Inicializovat vysvětlivku bodování `feature_map` s, která byla vypočítána dříve. 

Uložte vysvětlivku bodování a pak zaregistrujte model a vysvětlivku hodnocení pomocí služby správy modelu. Spusťte následující kód:

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

Dále vytvořte potřebné závislosti prostředí v kontejneru pro nasazený model. Vezměte prosím na vědomí, že azureml výchozí s verzí >= 1.0.45 musí být uvedeny jako pip závislost, protože obsahuje funkce potřebné k hostování modelu jako webové služby.

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

Nasadit službu pomocí conda souboru a bodovací soubor z předchozích kroků.

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

Odvození s některými testovacími daty zobrazíte předpovídanou hodnotu z automatizovaného modelu ML. Zobrazení důležitosti navržené funkce pro předpokládanou hodnotu.

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
```

### <a name="visualize-to-discover-patterns-in-data-and-explanations-at-training-time"></a>Vizualizujte pro zjišťování vzorů v datech a vysvětlení v době školení

Graf důležitosti funkcí můžete vizualizovat ve vašem pracovním prostoru ve [studiu Azure Machine Learning](https://ml.azure.com). Po dokončení automatického spuštění ml vyberte **Zobrazit podrobnosti modelu,** chcete-li zobrazit konkrétní spuštění. Výběrem karty **Vysvětlení** zobrazíte řídicí panel vizualizace vysvětlení.

[![Architektura interpretace strojového učení](./media/how-to-machine-learning-interpretability-automl/automl-explainability.png)](./media/how-to-machine-learning-interpretability-automl/automl-explainability.png#lightbox)

## <a name="next-steps"></a>Další kroky

Další informace o tom, jak můžete povolit vysvětlení modelu a důležitost funkcí v oblastech sady Azure Machine Learning SDK než automatizované strojové učení, najdete v [článku konceptu o interpretaci](how-to-machine-learning-interpretability.md).
