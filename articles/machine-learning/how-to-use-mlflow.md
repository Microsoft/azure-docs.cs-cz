---
title: Sledování mlflow pro experimenty ML
titleSuffix: Azure Machine Learning
description: Nastavte MLflow s Azure Machine Learning pro protokolování metrik a artefaktů z modelů ML vytvořených v clusterech Databricks, místním prostředí nebo prostředí virtuálních počítače.
services: machine-learning
author: rastala
ms.author: roastala
ms.service: machine-learning
ms.subservice: core
ms.reviewer: nibaccam
ms.topic: conceptual
ms.date: 02/03/2020
ms.custom: seodec18
ms.openlocfilehash: dce7db9fc508c70d79be62a7e97b3bf52a316b22
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76983694"
---
# <a name="track-models-metrics-with-mlflow-and-azure-machine-learning-preview"></a>Sledování metrik modelů pomocí MLflow a Azure Machine Learning (preview)

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Tento článek ukazuje, jak povolit mlflow sledování URI a protokolování rozhraní API, souhrnně označované jako [Sledování MLflow](https://mlflow.org/docs/latest/quickstart.html#using-the-tracking-api), pro připojení experimentů MLflow a Azure Machine Learning. To vám umožní sledovat a protokolovat metriky experimentů a artefakty v [pracovním prostoru Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/concept-azure-machine-learning-architecture#workspaces). Pokud již používáte sledování mlflow pro experimenty, pracovní prostor poskytuje centralizované, zabezpečené a škálovatelné umístění pro ukládání trénovacích metrik a modelů.

<!--
+ Deploy your MLflow experiments as an Azure Machine Learning web service. By deploying as a web service, you can apply the Azure Machine Learning monitoring and data drift detection functionalities to your production models. 
-->

[MLflow](https://www.mlflow.org) je open source knihovna pro správu životního cyklu experimentů strojového učení. MLFlow Tracking je součástí MLflow, která zaznamenává a sleduje metriky trénovacího běhu a artefakty modelu bez ohledu na prostředí experimentu – místně v počítači, na vzdáleném výpočetním cíli, ve virtuálním počítači nebo v clusteru Azure Databricks. 

Následující diagram znázorňuje, že pomocí sledování MLflow můžete sledovat metriky spuštění experimentu a ukládat artefakty modelu v pracovním prostoru Azure Machine Learning.

![mlflow s diagramem azure machine learning](./media/how-to-use-mlflow/mlflow-diagram-track.png)

> [!TIP]
> Informace v tomto dokumentu jsou určeny především pro datové vědce a vývojáře, kteří chtějí sledovat proces školení modelu. Pokud máte zájem o monitorování využití prostředků a událostí z Azure Machine Learning, jako jsou kvóty, dokončené spuštění školení nebo dokončená nasazení modelu, přečtěte si část [Monitorování Azure Machine Learning](monitor-azure-machine-learning.md).

## <a name="compare-mlflow-and-azure-machine-learning-clients"></a>Porovnání klientů MLflow a Azure Machine Learning

 Níže uvedená tabulka shrnuje různé klienty, kteří můžou používat Azure Machine Learning a jejich příslušné funkce.

 Sledování mlflow nabízí funkce protokolování metrik a úložiště artefaktů, které jsou jinak dostupné jenom prostřednictvím sady [Azure Machine Learning Python SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py).


| | Sledování mlflow&nbsp; <!--& Deployment--> | Azure Machine Learning Python SDK |  Azure Machine Learning CLI | Azure Machine Learning Studio|
|---|---|---|---|---|
| Správa pracovního prostoru |   | ✓ | ✓ | ✓ |
| Použití úložišť dat  |   | ✓ | ✓ | |
| Metriky protokolu      | ✓ | ✓ |   | |
| Nahrávání artefaktů | ✓ | ✓ |   | |
| Zobrazit metriky     | ✓ | ✓ | ✓ | ✓ |
| Správa výpočetních služeb   |   | ✓ | ✓ | ✓ |

<!--| Deploy models    | ✓ | ✓ | ✓ | ✓ |
|Monitor model performance||✓|  |   |
| Detect data drift |   | ✓ |   | ✓ |
-->
## <a name="prerequisites"></a>Požadavky

* [Nainstalujte mlflow.](https://mlflow.org/docs/latest/quickstart.html)
* [Nainstalujte azure machine learning sdk](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py) na místní počítač Sada SDK poskytuje připojení pro MLflow pro přístup k vašemu pracovnímu prostoru.
* [Vytvořte pracovní prostor Azure Machine Learning](how-to-manage-workspace.md).

## <a name="track-local-runs"></a>Sledovat místní spuštění

Sledování MLflow pomocí Azure Machine Learning umožňuje ukládat zaznamenané metriky a artefakty z místních spuštění do pracovního prostoru Azure Machine Learning.

Nainstalujte `azureml-mlflow` balíček, abyste pomocí funkce Sledování MLflow s Azure Machine Learning používali experimenty místně spuštěné v poznámkovém bloku Jupyter nebo editoru kódu.

```shell
pip install azureml-mlflow
```

>[!NOTE]
>Obor názvů azureml.contrib se často mění, protože pracujeme na zlepšení služby. Jako takové by mělo být cokoli v tomto oboru názvů považováno za náhled a nebylo plně podporováno společností Microsoft.

Importujte `mlflow` [`Workspace`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace(class)?view=azure-ml-py) třídy a pro přístup k měřicímu uri technologie MLflow a nakonfigurujte pracovní prostor.

V následujícím kódu `get_mlflow_tracking_uri()` metoda přiřadí pracovnímu prostoru `ws`jedinečnou adresu `set_tracking_uri()` URI sledování a na tuto adresu nařídí identifikátor URI sledování mlflow.

```Python
import mlflow
from azureml.core import Workspace

ws = Workspace.from_config()

mlflow.set_tracking_uri(ws.get_mlflow_tracking_uri())
```

>[!NOTE]
>Identifikátor URI pro sledování je platný až hodinu nebo méně. Pokud restartujete skript po určité době nečinnosti, použijte get_mlflow_tracking_uri rozhraní API získat nový identifikátor URI.

Nastavte název experimentu MLflow s `set_experiment()` a `start_run()`spusťte tréninkový běh s . Pak `log_metric()` použijte k aktivaci rozhraní API protokolování MLflow a začněte protokolovat metriky spuštění školení.

```Python
experiment_name = 'experiment_with_mlflow'
mlflow.set_experiment(experiment_name)

with mlflow.start_run():
    mlflow.log_metric('alpha', 0.03)
```

## <a name="track-remote-runs"></a>Sledování vzdálených spuštění

Sledování MLflow pomocí Azure Machine Learning umožňuje ukládat zaznamenané metriky a artefakty ze vzdálených spuštění do pracovního prostoru Azure Machine Learning.

Vzdálené spuštění umožňují trénovat vaše modely na výkonnějšívýpočetní výkony, jako jsou virtuální počítače s podporou GPU nebo clustery Machine Learning Compute. Informace o různých možnostech výpočetních výkonů najdete v tématu [Nastavení výpočetních cílů pro trénování modelu.](how-to-set-up-training-targets.md)

Nakonfigurujte prostředí výpočetního a trénovacího běhu pomocí třídy. [`Environment`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py) Zahrnout `mlflow` `azureml-mlflow` a pip balíčky [`CondaDependencies`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.conda_dependencies.condadependencies?view=azure-ml-py) v části prostředí. Pak [`ScriptRunConfig`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.script_run_config.scriptrunconfig?view=azure-ml-py) se stavějte pomocí vzdáleného výpočetního výkonu jako výpočetního cíle.

```Python
from azureml.core.environment import Environment
from azureml.core.conda_dependencies import CondaDependencies
from azureml.core import ScriptRunConfig

exp = Experiment(workspace = 'my_workspace',
                 name='my_experiment')

mlflow_env = Environment(name='mlflow-env')

cd = CondaDependencies.create(pip_packages=['mlflow', 'azureml-mlflow'])

mlflow_env.python.conda_dependencies = cd

src = ScriptRunConfig(source_directory='./my_script_location', script='my_training_script.py')

src.run_config.target = 'my-remote-compute-compute'
src.run_config.environment = mlflow_env
```

V trénovacím `mlflow` skriptu importujte, abyste použili api protokolování MLflow, a začněte protokolovat metriky spuštění.

```Python
import mlflow

with mlflow.start_run():
    mlflow.log_metric('example', 1.23)
```

S tímto výpočetním a `Experiment.submit('train.py')` trénovacím spuštěním použijte metodu k odeslání spuštění. Tato metoda automaticky nastaví identifikátor URI sledování mlflow a přesměruje protokolování z MLflow do pracovního prostoru.

```Python
run = exp.submit(src)
```

## <a name="track-azure-databricks-runs"></a>Sledování azure datových cihel běží

Sledování MLflow pomocí Azure Machine Learning umožňuje ukládat zaznamenané metriky a artefakty z vašich Datových cihel Azure, které běží ve vašem pracovním prostoru Azure Machine Learning.

Chcete-li spouštět experimenty MLFLOW s Azure Databricks, musíte nejprve vytvořit [pracovní prostor Azure Databricks a cluster](https://docs.microsoft.com/azure/azure-databricks/quickstart-create-databricks-workspace-portal). Ve vašem clusteru nezapomeňte nainstalovat knihovnu *azureml-mlflow* z PyPi, abyste zajistili, že váš cluster má přístup k potřebným funkcím a třídám.

Tady importujte poznámkový blok experimentu, připojte ho ke clusteru Azure Databricks a spusťte experiment. 

### <a name="install-libraries"></a>Instalace knihoven

Pokud chcete do clusteru nainstalovat knihovny, přejděte na kartu **Knihovny** a klikněte na **Instalovat nové.**

 ![mlflow s diagramem azure machine learning](./media/how-to-use-mlflow/azure-databricks-cluster-libraries.png)

Do pole **Balíček** zadejte azureml-mlflow a klikněte na tlačítko Instalovat. Tento krok podle potřeby opakujte a nainstalujte další další balíčky do clusteru pro experiment.

 ![mlflow s diagramem azure machine learning](./media/how-to-use-mlflow/install-libraries.png)

### <a name="set-up-your-notebook-and-workspace"></a>Nastavení poznámkového bloku a pracovního prostoru

Po nastavení clusteru importujte poznámkový blok experimentu, otevřete ho a připojte k němu cluster.

Následující kód by měl být v poznámkovém bloku experimentu. Tento kód získá podrobnosti o předplatném Azure k vytvoření instance vašeho pracovního prostoru. Tento kód předpokládá, že máte existující skupinu prostředků a pracovní prostor Azure Machine Learning, jinak je můžete [vytvořit](how-to-manage-workspace.md). 

```python
import mlflow
import mlflow.azureml
import azureml.mlflow
import azureml.core

from azureml.core import Workspace
from azureml.mlflow import get_portal_url

subscription_id = 'subscription_id'

# Azure Machine Learning resource group NOT the managed resource group
resource_group = 'resource_group_name' 

#Azure Machine Learning workspace name, NOT Azure Databricks workspace
workspace_name = 'workspace_name'  

# Instantiate Azure Machine Learning workspace
ws = Workspace.get(name=workspace_name,
                   subscription_id=subscription_id,
                   resource_group=resource_group)
```

#### <a name="connect-your-azure-databricks-and-azure-machine-learning-workspaces"></a>Propojení pracovních prostorů Azure Databricks a Azure Machine Learning

Na [portálu Azure](https://ms.portal.azure.com)můžete propojit pracovní prostor Azure Databricks (ADB) s novým nebo existujícím pracovním prostorem Azure Machine Learning. Chcete-li tak učinit, přejděte do pracovního prostoru ADB a vyberte tlačítko **propojit Azure Machine Learning pracovního prostoru** v pravém dolním rohu. Propojení pracovních prostorů umožňuje sledovat data experimentu v pracovním prostoru Azure Machine Learning. 

### <a name="link-mlflow-tracking-to-your-workspace"></a>Propojení sledování mlflow s pracovním prostorem

Po vytvoření instance pracovního prostoru nastavte identifikátor URI sledování MLflow. Tímto způsobem propojíte sledování MLflow s pracovním prostorem Azure Machine Learning. Po propojení všechny vaše experimenty přistanou ve spravované službě sledování Azure Machine Learning.

#### <a name="directly-set-mlflow-tracking-in-your-notebook"></a>Přímé nastavení sledování mlflow v poznámkovém bloku

```python
uri = ws.get_mlflow_tracking_uri()
mlflow.set_tracking_uri(uri)
```

Ve školicím skriptu importujte mlflow, abyste použili api protokolování MLflow a začněte protokolovat metriky spuštění. V následujícím příkladu protokoly epocha ztráta metriky. 

```python
import mlflow 
mlflow.log_metric('epoch_loss', loss.item()) 
```

#### <a name="automate-setting-mlflow-tracking"></a>Automatizace nastavení sledování mlflow

Místo ručního nastavení identifikátoru URI pro sledování v každé následující relaci poznámkového bloku experimentu v clusterech tak učiňte automaticky pomocí tohoto [skriptu Init clusteru sledování strojového učení Azure.](https://github.com/Azure/MachineLearningNotebooks/blob/3ce779063b000e0670bdd1acc6bc3a4ee707ec13/how-to-use-azureml/azure-databricks/linking/README.md)

Při správné konfiguraci můžete zobrazit data sledování MLflow v rozhraní API REST Azure Machine Learning a ve všech klientech a v Azure Databricks prostřednictvím uživatelského rozhraní MLflow nebo pomocí klienta MLflow.

## <a name="view-metrics-and-artifacts-in-your-workspace"></a>Zobrazení metrik a artefaktů v pracovním prostoru

Metriky a artefakty z protokolování MLflow jsou uloženy ve vašem pracovním prostoru. Chcete-li je kdykoli zobrazit, přejděte do pracovního prostoru a vyhledejte experiment podle názvu v pracovním prostoru ve [studiu Azure Machine Learning](https://ml.azure.com).  Nebo spusťte níže uvedený kód. 

```python
run.get_metrics()
ws.get_details()
```

<!-- ## Deploy MLflow models as a web service

Deploying your MLflow experiments as an Azure Machine Learning web service allows you to leverage the Azure Machine Learning model management and data drift detection capabilities and apply them to your production models.

The following diagram demonstrates that with the MLflow deploy API you can deploy your existing MLflow models as an Azure Machine Learning web service, despite their frameworks--PyTorch, Tensorflow, scikit-learn, ONNX, etc., and manage your production models in your workspace.

![mlflow with azure machine learning diagram](./media/how-to-use-mlflow/mlflow-diagram-deploy.png)

### Log your model

Before you can deploy, be sure that your model is saved so you can reference it and its path location for deployment. In your training script, there should be code similar to the following [mlflow.sklearn.log_model()](https://www.mlflow.org/docs/latest/python_api/mlflow.sklearn.html) method, that saves your model to the specified outputs directory. 

```python
# change sklearn to pytorch, tensorflow, etc. based on your experiment's framework 
import mlflow.sklearn

# Save the model to the outputs directory for capture
mlflow.sklearn.log_model(regression_model, model_save_path)
```
>[!NOTE]
> Include the `conda_env` parameter to pass a dictionary representation of the dependencies and environment this model should be run in.

### Retrieve model from previous run

To retrieve the run, you need the run ID and the path in run history of where the model was saved. 

```python
# gets the list of runs for your experiment as an array
experiment_name = 'experiment-with-mlflow'
exp = ws.experiments[experiment_name]
runs = list(exp.get_runs())

# get the run ID and the path in run history
runid = runs[0].id
model_save_path = 'model'
```

### Create Docker image

The `mlflow.azureml.build_image()` function builds a Docker image from the saved model in a framework-aware manner. It automatically creates the framework-specific inferencing wrapper code and specifies package dependencies for you. Specify the model path, your workspace, run ID and other parameters.

The following code builds a docker image using *runs:/<run.id>/model* as the model_uri path for a Scikit-learn experiment.

```python
import mlflow.azureml

azure_image, azure_model = mlflow.azureml.build_image(model_uri='runs:/{}/{}'.format(runid, model_save_path),
                                                      workspace=ws,
                                                      model_name='sklearn-model',
                                                      image_name='sklearn-image',
                                                      synchronous=True)
```
The creation of the Docker image can take several minutes. 

### Deploy the Docker image 

After the image is created, use the Azure Machine Learning SDK to deploy the image as a web service.

First, specify the deployment configuration. Azure Container Instance (ACI) is a suitable choice for a quick dev-test deployment, while Azure Kubernetes Service (AKS) is suitable for scalable production deployments.

#### Deploy to ACI

Set up your deployment configuration with the [deploy_configuration()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aciwebservice?view=azure-ml-py#deploy-configuration-cpu-cores-none--memory-gb-none--tags-none--properties-none--description-none--location-none--auth-enabled-none--ssl-enabled-none--enable-app-insights-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--ssl-cname-none--dns-name-label-none-) method. You can also add tags and descriptions to help keep track of your web service.

```python
from azureml.core.webservice import AciWebservice, Webservice

# Configure 
aci_config = AciWebservice.deploy_configuration(cpu_cores=1, 
                                                memory_gb=1, 
                                                tags={'method' : 'sklearn'}, 
                                                description='Diabetes model',
                                                location='eastus2')
```

Then, deploy the image by using the Azure Machine Learning SDK [deploy_from_image()](/python/api/azureml-core/azureml.core.webservice.webservice(class)?view=azure-ml-py#deploy-from-image-workspace--name--image--deployment-config-none--deployment-target-none--overwrite-false-) method. 

```python
webservice = Webservice.deploy_from_image( image=azure_image, 
                                           workspace=ws, 
                                           name='diabetes-model-1', 
                                           deployment_config=aci_config)

webservice.wait_for_deployment(show_output=True)
```
#### Deploy to AKS

To deploy to AKS, first create an AKS cluster and bring over the Docker image you want to deploy. For this example, bring over the previously created image from the ACI deployment.

To get the image from the previous ACI deployment use the [Image](https://docs.microsoft.com/python/api/azureml-core/azureml.core.image.image.image?view=azure-ml-py) class. 

```python
from azureml.core.image import Image

# Get the image by name, you can change this based on the image you want to deploy
myimage = Image(workspace=ws, name='sklearn-image') 
```

Create an AKS cluster using the [ComputeTarget.create()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.computetarget?view=azure-ml-py#create-workspace--name--provisioning-configuration-) method. It may take 20-25 minutes to create a new cluster.

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
Set up your deployment configuration with the [deploy_configuration()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aciwebservice?view=azure-ml-py#deploy-configuration-cpu-cores-none--memory-gb-none--tags-none--properties-none--description-none--location-none--auth-enabled-none--ssl-enabled-none--enable-app-insights-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--ssl-cname-none--dns-name-label-none-) method. You can also add tags and descriptions to help keep track of your web service.

```python
from azureml.core.webservice import Webservice, AksWebservice
from azureml.core.image import ContainerImage

# Set the web service configuration (using default here with app insights)
aks_config = AksWebservice.deploy_configuration(enable_app_insights=True)

# Unique service name
service_name ='aks-service'
```

Then, deploy the image by using the Azure Machine Learning SDK [deploy_from_image()](/python/api/azureml-core/azureml.core.webservice.webservice(class)?view=azure-ml-py#deploy-from-image-workspace--name--image--deployment-config-none--deployment-target-none--overwrite-false-) method. 

```python
# Webservice creation using single command
aks_service = Webservice.deploy_from_image( workspace=ws, 
                                            name=service_name,
                                            deployment_config = aks_config
                                            image = myimage,
                                            deployment_target = aks_target)

aks_service.wait_for_deployment(show_output=True)
```

The service deployment can take several minutes.

## Clean up resources

If you don't plan to use the logged metrics and artifacts in your workspace, the ability to delete them individually is currently unavailable. Instead, delete the resource group that contains the storage account and workspace, so you don't incur any charges:

1. In the Azure portal, select **Resource groups** on the far left.

   ![Delete in the Azure portal](./media/how-to-use-mlflow/delete-resources.png)

1. From the list, select the resource group you created.

1. Select **Delete resource group**.

1. Enter the resource group name. Then select **Delete**.

 -->

 ## <a name="example-notebooks"></a>Ukázkové poznámkové bloky

[MLflow s poznámkovými bloky Azure ML](https://aka.ms/azureml-mlflow-examples) demonstrovat a rozšířit na koncepty uvedené v tomto článku.

## <a name="next-steps"></a>Další kroky
* [Spravujte své modely](concept-model-management-and-deployment.md).
* Sledujte produkční modely pro [posun dat](how-to-monitor-data-drift.md).
