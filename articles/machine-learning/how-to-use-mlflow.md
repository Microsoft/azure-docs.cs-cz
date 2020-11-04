---
title: MLflow sledování experimentů ML
titleSuffix: Azure Machine Learning
description: Nastavte MLflow s Azure Machine Learning pro protokolování metrik a artefaktů z modelů ML a nasaďte vaše ML modely jako webovou službu.
services: machine-learning
author: shivp950
ms.author: shipatel
ms.service: machine-learning
ms.subservice: core
ms.reviewer: nibaccam
ms.date: 09/08/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python
ms.openlocfilehash: cf0817ad1e9fae901bfe2b4a174d95a4f673e4c0
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2020
ms.locfileid: "93319004"
---
# <a name="track-experiment-runs-and-deploy-ml-models-with-mlflow-and-azure-machine-learning-preview"></a>Sledování experimentů a nasazení modelů ML pomocí MLflow a Azure Machine Learning (Preview)

V tomto článku se dozvíte, jak povolit sledování identifikátoru URI a protokolovacího rozhraní API pro MLflow, označované jako [sledování MLflow](https://mlflow.org/docs/latest/quickstart.html#using-the-tracking-api), pro připojení Azure Machine Learning jako back-end experimentů MLflow. 

Mezi podporované možnosti patří: 

+ Sledujte metriky a metriky experimentů a artefakty v [pracovním prostoru Azure Machine Learning](./concept-azure-machine-learning-architecture.md#workspace). Pokud již pro vaše experimenty používáte sledování MLflow, pracovní prostor poskytuje centralizované, zabezpečené a škálovatelné umístění pro ukládání výukových metrik a modelů.

+ Odešlete školicí úlohy s MLflow projekty s podporou back-endu Azure Machine Learning (Preview). Úlohy můžete odesílat místně pomocí Azure Machine Learning sledování nebo migrace vašich běhů do cloudu, jako prostřednictvím [Azure Machine Learning COMPUTE](./how-to-create-attach-compute-cluster.md).

+ Sledujte a spravujte modely v MLflow a v registru modelů Azure Machine Learning.

+ Nasaďte MLflow experimenty jako webovou službu Azure Machine Learning. Nasazením jako webové služby můžete v produkčních modelech použít funkce monitorování Azure Machine Learning a detekce posunu dat. 

[MLflow](https://www.mlflow.org) je open source knihovna pro správu životního cyklu experimentů ve strojovém učení. Sledování MLFlow je součást MLflow, která protokoluje a sleduje metriky běhu a artefakty modelu bez ohledu na to, jestli je vaše experimenty v místním počítači, ve vzdáleném cílovém výpočetním prostředí, virtuálním počítači nebo [Azure Databricksm clusteru](how-to-use-mlflow-azure-databricks.md). 

>[!NOTE]
> Jako open source knihovna se MLflow změny často. Funkce, které jsou dostupné prostřednictvím Azure Machine Learning a integrace MLflow, by se měly považovat za verzi Preview, a není plně podporovaná Microsoftem.

Následující diagram znázorňuje, že se sledováním MLflow sledujete metriky spuštění experimentu a artefakty modelu úložiště v pracovním prostoru Azure Machine Learning.

![mlflow s diagramem Azure Machine Learning](./media/how-to-use-mlflow/mlflow-diagram-track.png)

> [!TIP]
> Informace v tomto dokumentu jsou primárně určené pro odborníky přes data a vývojáře, kteří chtějí monitorovat proces školení modelu. Pokud jste správcem a chcete monitorovat využití prostředků a události z Azure Machine Learning, jako jsou kvóty, dokončené školicí běhy nebo dokončená nasazení modelu, přečtěte si téma [monitorování Azure Machine Learning](monitor-azure-machine-learning.md).

## <a name="compare-mlflow-and-azure-machine-learning-clients"></a>Porovnání klientů MLflow a Azure Machine Learning

 Následující tabulka shrnuje různé klienty, kteří můžou použít Azure Machine Learning a jejich příslušné funkce.

 MLflow Tracking nabízí funkce protokolování metrik a úložiště artefaktů, které jsou k dispozici pouze v případě, že jsou k dispozici pouze v [Azure Machine Learning Python SDK](/python/api/overview/azure/ml/intro?preserve-view=true&view=azure-ml-py)

| Schopnost | MLflow sledování & nasazení | Azure Machine Learning Python SDK |  Rozhraní CLI služby Azure Machine Learning | Azure Machine Learning Studio|
|---|---|---|---|---|
| Spravovat pracovní prostor |   | ✓ | ✓ | ✓ |
| Použití úložišť dat  |   | ✓ | ✓ | |
| Metriky protokolů      | ✓ | ✓ |   | |
| Nahrání artefaktů | ✓ | ✓ |   | |
| Zobrazit metriky     | ✓ | ✓ | ✓ | ✓ |
| Správa výpočetních služeb   |   | ✓ | ✓ | ✓ |
| Modely nasazení    | ✓ | ✓ | ✓ | ✓ |
|Monitorování výkonu modelu||✓|  |   |
| Zjišťování odchylek dat |   | ✓ |   | ✓ |

## <a name="prerequisites"></a>Předpoklady

* Nainstalujte balíček `azureml-mlflow`. 
    * Tento balíček automaticky přinese `azureml-core` [sadu SDK Azure Machine Learning Pythonu](/python/api/overview/azure/ml/install?preserve-view=true&view=azure-ml-py), která poskytuje možnosti připojení pro MLflow k vašemu pracovnímu prostoru.
* [Vytvořte pracovní prostor Azure Machine Learning](how-to-manage-workspace.md).

## <a name="track-local-runs"></a>Sledovat místní běhy

MLflow sledování pomocí Azure Machine Learning umožňuje ukládat protokolované metriky a artefakty z místních běhů do pracovního prostoru Azure Machine Learning.

Importujte `mlflow` třídy a a [`Workspace`](/python/api/azureml-core/azureml.core.workspace%28class%29?preserve-view=true&view=azure-ml-py) Získejte přístup k identifikátoru URI sledování MLflow a nakonfigurujte svůj pracovní prostor.

V následujícím kódu `get_mlflow_tracking_uri()` metoda přiřadí k pracovnímu prostoru jedinečnou adresu identifikátoru URI sledování `ws` a `set_tracking_uri()` odkazuje na adresu URI sledování MLflow na tuto adresu.

```Python
import mlflow
from azureml.core import Workspace

ws = Workspace.from_config()

mlflow.set_tracking_uri(ws.get_mlflow_tracking_uri())
```

>[!NOTE]
>Identifikátor URI sledování je platný až do hodin nebo méně. Pokud po nějaké době nečinnosti svůj skript restartujete, použijte rozhraní get_mlflow_tracking_uri API k získání nového identifikátoru URI.

Nastavte název experimentu MLflow pomocí `set_experiment()` a spusťte školicí běh s `start_run()` . Pak použijte `log_metric()` k aktivaci rozhraní API pro protokolování MLflow a zahajte protokolování metriky pro školicí běh.

```Python
experiment_name = 'experiment_with_mlflow'
mlflow.set_experiment(experiment_name)

with mlflow.start_run():
    mlflow.log_metric('alpha', 0.03)
```

## <a name="track-remote-runs"></a>Sledovat vzdálené běhy

Vzdálené spuštění vám umožní naučit vaše modely s výkonnějšími výpočetními prostředky, jako jsou virtuální počítače s podporou GPU nebo Výpočetní prostředky služby Machine Learning clustery. Další informace o různých možnostech výpočtů najdete v tématu [použití výpočetních cílů pro školení modelů](how-to-set-up-training-targets.md) .

MLflow sledování pomocí Azure Machine Learning umožňuje ukládat protokolované metriky a artefakty ze vzdálených běhů do pracovního prostoru Azure Machine Learning. Všechny spuštěné s kódem sledování MLflow v něm budou automaticky zaznamenány metriky do pracovního prostoru. 

Následující příklad prostředí conda zahrnuje `mlflow` a `azureml-mlflow` jako balíčky PIP. 


```yaml
name: sklearn-example
dependencies:
  - python=3.6.2
  - scikit-learn
  - matplotlib
  - numpy
  - pip:
    - azureml-mlflow
    - numpy
```

Ve svém skriptu nakonfigurujte výpočetní prostředí a prostředí pro výukové spuštění pomocí [`Environment`](/python/api/azureml-core/azureml.core.environment.environment?preserve-view=true&view=azure-ml-py) třídy. Pak se  [`ScriptRunConfig`](/python/api/azureml-core/azureml.core.script_run_config.scriptrunconfig?preserve-view=true&view=azure-ml-py) pomocí vzdálených výpočtů vystavíte jako cíl výpočtů.

```Python
import mlflow

with mlflow.start_run():
    mlflow.log_metric('example', 1.23)
```

Pomocí této výpočetní a školicí konfigurace spuštění použijte `Experiment.submit()` metodu pro odeslání běhu. Tato metoda automaticky nastaví identifikátor URI sledování MLflow a přesměruje protokolování z MLflow do vašeho pracovního prostoru.

```Python
run = exp.submit(src)
```

## <a name="train-with-mlflow-projects"></a>Výuka s projekty MLflow

[Projekty MLflow](https://mlflow.org/docs/latest/projects.html) umožňují organizovat a popsat váš kód, aby mohli ostatní datové odborníky (nebo automatizované nástroje) spustit. Projekty MLflow s Azure Machine Learning umožňují sledovat a spravovat vaše školicí běhy ve vašem pracovním prostoru. 

Tento příklad ukazuje, jak odeslat MLflow projekty místně pomocí sledování Azure Machine Learning.

Nainstalujte `azureml-mlflow` balíček pro použití sledování MLflow s Azure Machine Learning v místním experimentech. Experimenty mohou běžet prostřednictvím poznámkového bloku Jupyter nebo editoru kódu.

```shell
pip install azureml-mlflow
```

Importujte `mlflow` třídy a a [`Workspace`](/python/api/azureml-core/azureml.core.workspace%28class%29?preserve-view=true&view=azure-ml-py) Získejte přístup k identifikátoru URI sledování MLflow a nakonfigurujte svůj pracovní prostor.

```Python
import mlflow
from azureml.core import Workspace

ws = Workspace.from_config()

mlflow.set_tracking_uri(ws.get_mlflow_tracking_uri())
```

Nastavte název experimentu MLflow pomocí `set_experiment()` a spusťte školicí běh s `start_run()` . Pak použijte `log_metric()` k aktivaci rozhraní API pro protokolování MLflow a zahajte protokolování metriky pro školicí běh.

```Python
experiment_name = 'experiment-with-mlflow-projects'
mlflow.set_experiment(experiment_name)
```

Vytvořte objekt konfigurace back-endu, ve kterém budou uloženy potřebné informace pro integraci, jako je například výpočetní cíl a typ spravovaného prostředí, který se má použít.

```python
backend_config = {"USE_CONDA": False}
```
Přidejte `azureml-mlflow` balíček jako závislost PIP do konfiguračního souboru prostředí, aby bylo možné sledovat metriky a klíčové artefakty ve vašem pracovním prostoru. 

``` shell
name: mlflow-example
channels:
  - defaults
  - anaconda
  - conda-forge
dependencies:
  - python=3.6
  - scikit-learn=0.19.1
  - pip
  - pip:
    - mlflow
    - azureml-mlflow
```
Odešlete místní běh a ujistěte se, že jste nastavili parametr `backend = "azureml" ` . Pomocí tohoto nastavení můžete odeslat spustit místně a získat přidanou podporu automatického sledování výstupu, soubory protokolu, snímky a tištěné chyby ve vašem pracovním prostoru. 

Podívejte se na vaše běhy a metriky v [Azure Machine Learning Studiu](overview-what-is-machine-learning-studio.md). 


```python
local_env_run = mlflow.projects.run(uri=".", 
                                    parameters={"alpha":0.3},
                                    backend = "azureml",
                                    use_conda=False,
                                    backend_config = backend_config, 
                                    )

```

## <a name="view-metrics-and-artifacts-in-your-workspace"></a>Zobrazení metrik a artefaktů ve vašem pracovním prostoru

Metriky a artefakty z protokolování MLflow se uchovávají ve vašem pracovním prostoru. Pokud je chcete kdykoli zobrazit, přejděte do pracovního prostoru a v pracovním prostoru v [Azure Machine Learning Studiu](https://ml.azure.com)Najděte experiment podle názvu.  Nebo spusťte následující kód. 

```python
run.get_metrics()
ws.get_details()
```

## <a name="manage-models"></a>Správa modelů 

Zaregistrujte a sledujte své modely pomocí [Azure Machine Learning registru modelu](concept-model-management-and-deployment.md#register-package-and-deploy-models-from-anywhere) , který podporuje Registry modelu MLflow. Modely Azure Machine Learning jsou zarovnané ke schématu modelu MLflow, což usnadňuje export a import těchto modelů v různých pracovních postupech. Metadata týkající se MLflow, jako je například, ID běhu, jsou označena také registrovaným modelem pro sledovatelnost. Uživatelé můžou odesílat školicí běhy, registrovat a nasazovat modely vytvořené z MLflowch spuštění. 

Pokud chcete nasadit a zaregistrovat model připravený pro produkční prostředí v jednom kroku, přečtěte si téma [nasazení a registrace modelů MLflow](#deploy-and-register-mlflow-models).

Chcete-li zaregistrovat a zobrazit model z běhu, použijte následující postup:

1. Po dokončení běhu volejte `register_model()` metodu.

    ```python
    # the model folder produced from the run is registered. This includes the MLmodel file, model.pkl and the conda.yaml.
    run.register_model(model_name = 'my-model', model_path = 'model')
    ```

1. Seznamte se s registrovaným modelem v pracovním prostoru pomocí [Azure Machine Learning studia](overview-what-is-machine-learning-studio.md).

    V následujícím příkladu registrovaného modelu `my-model` má označená metadata sledování MLflow. 

    ![Register-mlflow-model](./media/how-to-use-mlflow/registered-mlflow-model.png)

1. Vyberte kartu **artefakty** a zobrazte všechny soubory modelů, které odpovídají schématu modelu MLflow (conda. yaml, MLmodel, model. pkl).

    ![model – schéma](./media/how-to-use-mlflow/mlflow-model-schema.png)

1. Vyberte MLmodel pro zobrazení souboru MLmodel generovaného spuštěním.

    ![MLmodel – schéma](./media/how-to-use-mlflow/mlmodel-view.png)



## <a name="deploy-and-register-mlflow-models"></a>Nasazení a registrace modelů MLflow 

Nasazení MLflow experimentů jako webové služby Azure Machine Learning vám umožní využít a použít možnosti Azure Machine Learning správy modelů a detekce přenosů dat v produkčních modelech.

K tomu je potřeba

1. Registraci modelu.
1. Určete, kterou konfiguraci nasazení chcete použít pro váš scénář.

    1. [Azure Container instance (ACI)](#deploy-to-aci) je vhodná volba pro nasazení s rychlým vývojem a testováním.
    1. [Služba Azure Kubernetes Service (AKS)](#deploy-to-aks) je vhodná pro škálovatelná produkční nasazení.

Následující diagram znázorňuje, že s rozhraním API pro nasazení MLflow můžete nasadit stávající modely MLflow jako webovou službu Azure Machine Learning bez ohledu na jejich architektury--PyTorch, Tensorflow, scikit-učit, ONNX atd. a spravovat produkční modely v pracovním prostoru.

![ nasazení modelů mlflow pomocí Azure Machine Learningu](./media/how-to-use-mlflow/mlflow-diagram-deploy.png)


### <a name="deploy-to-aci"></a>Nasazení do ACI

Nastavte konfiguraci nasazení pomocí metody [deploy_configuration ()](/python/api/azureml-core/azureml.core.webservice.aciwebservice?preserve-view=true&view=azure-ml-py#&preserve-view=truedeploy-configuration-cpu-cores-none--memory-gb-none--tags-none--properties-none--description-none--location-none--auth-enabled-none--ssl-enabled-none--enable-app-insights-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--ssl-cname-none--dns-name-label-none-) . Můžete také přidat značky a popisy, které vám pomohou sledovat webovou službu.

```python
from azureml.core.webservice import AciWebservice, Webservice

# Set the model path to the model folder created by your run
model_path = "model"

# Configure 
aci_config = AciWebservice.deploy_configuration(cpu_cores=1, 
                                                memory_gb=1, 
                                                tags={'method' : 'sklearn'}, 
                                                description='Diabetes model',
                                                location='eastus2')
```

Pak model zaregistrujte a nasaďte v jednom kroku pomocí metody [nasazení](/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py&preserve-view=true#&preserve-view=truedeploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-) Azure Machine Learning SDK. 

```python
(webservice,model) = mlflow.azureml.deploy( model_uri='runs:/{}/{}'.format(run.id, model_path),
                      workspace=ws,
                      model_name='sklearn-model', 
                      service_name='diabetes-model-1', 
                      deployment_config=aci_config, 
                      tags=None, mlflow_home=None, synchronous=True)

webservice.wait_for_deployment(show_output=True)
```

### <a name="deploy-to-aks"></a>Nasazení do AKS

K nasazení na AKS nejprve vytvořte cluster AKS. Vytvořte cluster AKS pomocí metody [ComputeTarget. Create ()](/python/api/azureml-core/azureml.core.computetarget?preserve-view=true&view=azure-ml-py#&preserve-view=truecreate-workspace--name--provisioning-configuration-) . Vytvoření nového clusteru může trvat 20-25 minut.

```python
from azureml.core.compute import AksCompute, ComputeTarget

# Use the default configuration (can also provide parameters to customize)
prov_config = AksCompute.provisioning_configuration()

aks_name = 'aks-mlflow'

# Create the cluster
aks_target = ComputeTarget.create(workspace=ws, 
                                  name=aks_name, 
                                  provisioning_configuration=prov_config)

aks_target.wait_for_completion(show_output = True)

print(aks_target.provisioning_state)
print(aks_target.provisioning_errors)
```
Nastavte konfiguraci nasazení pomocí metody [deploy_configuration ()](/python/api/azureml-core/azureml.core.webservice.aciwebservice?preserve-view=true&view=azure-ml-py#&preserve-view=truedeploy-configuration-cpu-cores-none--memory-gb-none--tags-none--properties-none--description-none--location-none--auth-enabled-none--ssl-enabled-none--enable-app-insights-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--ssl-cname-none--dns-name-label-none-) . Můžete také přidat značky a popisy, které vám pomohou sledovat webovou službu.

```python
from azureml.core.webservice import Webservice, AksWebservice

# Set the web service configuration (using default here with app insights)
aks_config = AksWebservice.deploy_configuration(enable_app_insights=True, compute_target_name='aks-mlflow')

```

Pak model zaregistrujte a nasaďte v jednom kroku pomocí Azure Machine Learning SDK [Deploy ()] (potom Registrujte a nasaďte model pomocí metody [nasazení](/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py&preserve-view=true#&preserve-view=truedeploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-) Azure Machine Learning SDK. 

```python

# Webservice creation using single command
from azureml.core.webservice import AksWebservice, Webservice

# set the model path 
model_path = "model"

(webservice, model) = mlflow.azureml.deploy( model_uri='runs:/{}/{}'.format(run.id, model_path),
                      workspace=ws,
                      model_name='sklearn-model', 
                      service_name='my-aks', 
                      deployment_config=aks_config, 
                      tags=None, mlflow_home=None, synchronous=True)


webservice.wait_for_deployment()
```

Nasazení služby může trvat několik minut.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud neplánujete použít zaznamenané metriky a artefakty v pracovním prostoru, možnost jejich odstranění je momentálně nedostupná. Místo toho odstraňte skupinu prostředků, která obsahuje účet úložiště a pracovní prostor, takže se vám neúčtují žádné poplatky:

1. Úplně nalevo na webu Azure Portal vyberte **Skupiny prostředků**.

   ![Odstranit v Azure Portal](./media/how-to-use-mlflow/delete-resources.png)

1. V seznamu vyberte skupinu prostředků, kterou jste vytvořili.

1. Vyberte **Odstranit skupinu prostředků**.

1. Zadejte název skupiny prostředků. Vyberte **Odstranit**.

## <a name="example-notebooks"></a>Příklady poznámkových bloků

[MLflow s poznámkovým blokům Azure ml](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/track-and-monitor-experiments/using-mlflow) ukazují a rozšiřují koncepty prezentované v tomto článku.

> [!NOTE]
> Úložiště příkladů založené na komunitě s použitím mlflow najdete na adrese https://github.com/Azure/azureml-examples .

## <a name="next-steps"></a>Další kroky

* [Spravujte své modely](concept-model-management-and-deployment.md).
* Monitorujte v produkčních modelech [přenos dat](./how-to-enable-data-collection.md).
* [Sledování Azure Databricks běží s MLflow](how-to-use-mlflow-azure-databricks.md).