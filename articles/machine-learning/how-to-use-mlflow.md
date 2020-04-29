---
title: MLflow sledování experimentů ML
titleSuffix: Azure Machine Learning
description: Nastavte MLflow s Azure Machine Learning pro protokolování metrik a artefaktů z modelů ML vytvořených v clusterech datacihly, v místním prostředí nebo prostředí virtuálních počítačů.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "76983694"
---
# <a name="track-models-metrics-with-mlflow-and-azure-machine-learning-preview"></a>Sledování metrik modelů pomocí MLflow a Azure Machine Learning (Preview)

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

V tomto článku se dozvíte, jak povolit sledování identifikátorů URI a protokolovacího rozhraní API pro MLflow, označované jako [sledování MLflow](https://mlflow.org/docs/latest/quickstart.html#using-the-tracking-api), pro propojení MLflow experimentů a Azure Machine Learning. To umožňuje sledovat a protokolovat metriky a artefakty v [pracovním prostoru Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/concept-azure-machine-learning-architecture#workspaces). Pokud již pro vaše experimenty používáte sledování MLflow, pracovní prostor poskytuje centralizované, zabezpečené a škálovatelné umístění pro ukládání výukových metrik a modelů.

<!--
+ Deploy your MLflow experiments as an Azure Machine Learning web service. By deploying as a web service, you can apply the Azure Machine Learning monitoring and data drift detection functionalities to your production models. 
-->

[MLflow](https://www.mlflow.org) je open source knihovna pro správu životního cyklu experimentů ve strojovém učení. Sledování MLFlow je součást MLflow, která protokoluje a sleduje metriky běhu a artefakty modelu bez ohledu na to, jestli je vaše experimenty v místním počítači, ve vzdáleném cílovém výpočetním prostředí, virtuálním počítači nebo Azure Databricksm clusteru. 

Následující diagram znázorňuje, že se sledováním MLflow sledujete metriky spuštění experimentu a artefakty modelu úložiště v pracovním prostoru Azure Machine Learning.

![mlflow s diagramem Azure Machine Learning](./media/how-to-use-mlflow/mlflow-diagram-track.png)

> [!TIP]
> Informace v tomto dokumentu jsou primárně určené pro odborníky přes data a vývojáře, kteří chtějí monitorovat proces školení modelu. Pokud jste správcem a chcete monitorovat využití prostředků a události z Azure Machine Learning, jako jsou kvóty, dokončené školicí běhy nebo dokončená nasazení modelu, přečtěte si téma [monitorování Azure Machine Learning](monitor-azure-machine-learning.md).

## <a name="compare-mlflow-and-azure-machine-learning-clients"></a>Porovnání klientů MLflow a Azure Machine Learning

 Níže uvedená tabulka shrnuje různé klienty, kteří můžou použít Azure Machine Learning a jejich příslušné funkce.

 MLflow Tracking nabízí funkce protokolování metrik a úložiště artefaktů, které jsou k dispozici pouze v případě, že jsou k dispozici pouze v [Azure Machine Learning Python SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)


| | Sledování&nbsp;MLflow <!--& Deployment--> | Azure Machine Learning Python SDK |  Azure Machine Learning CLI | Azure Machine Learning Studio|
|---|---|---|---|---|
| Spravovat pracovní prostor |   | ✓ | ✓ | ✓ |
| Použití úložišť dat  |   | ✓ | ✓ | |
| Metriky protokolu      | ✓ | ✓ |   | |
| Nahrání artefaktů | ✓ | ✓ |   | |
| Zobrazit metriky     | ✓ | ✓ | ✓ | ✓ |
| Správa výpočetních služeb   |   | ✓ | ✓ | ✓ |

<!--| Deploy models    | ✓ | ✓ | ✓ | ✓ |
|Monitor model performance||✓|  |   |
| Detect data drift |   | ✓ |   | ✓ |
-->
## <a name="prerequisites"></a>Požadavky

* [Nainstalujte MLflow.](https://mlflow.org/docs/latest/quickstart.html)
* [Nainstalujte sadu Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py) do místního počítače, kterou sada SDK poskytuje pro přístup k vašemu pracovnímu prostoru pro MLflow.
* [Vytvořte pracovní prostor Azure Machine Learning](how-to-manage-workspace.md).

## <a name="track-local-runs"></a>Sledovat místní běhy

MLflow sledování pomocí Azure Machine Learning umožňuje ukládat protokolované metriky a artefakty z místních běhů do pracovního prostoru Azure Machine Learning.

Nainstalujte `azureml-mlflow` balíček, aby používal sledování MLflow s Azure Machine Learning v pokusůch místně spuštěné v editoru Jupyter notebook nebo kódu.

```shell
pip install azureml-mlflow
```

>[!NOTE]
>Obor názvů AzureML. contrib se často mění, protože budeme spolupracovat na vylepšení služby. V takovém případě by se cokoli v tomto oboru názvů měly považovat za verzi Preview a společnost Microsoft je plně Nepodporovaná.

Importujte třídy `mlflow` a [`Workspace`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace(class)?view=azure-ml-py) a získejte přístup k identifikátoru URI sledování MLflow a nakonfigurujte svůj pracovní prostor.

V následujícím kódu `get_mlflow_tracking_uri()` metoda přiřadí k pracovnímu prostoru `ws`jedinečnou adresu identifikátoru URI sledování a `set_tracking_uri()` odkazuje na adresu URI sledování MLflow na tuto adresu.

```Python
import mlflow
from azureml.core import Workspace

ws = Workspace.from_config()

mlflow.set_tracking_uri(ws.get_mlflow_tracking_uri())
```

>[!NOTE]
>Identifikátor URI sledování je platný až do hodin nebo méně. Pokud po nějaké době nečinnosti svůj skript restartujete, použijte rozhraní get_mlflow_tracking_uri API k získání nového identifikátoru URI.

Nastavte název experimentu MLflow pomocí `set_experiment()` a spusťte školicí běh s `start_run()`. Pak použijte `log_metric()` k aktivaci rozhraní API pro protokolování MLflow a zahajte protokolování metriky pro školicí běh.

```Python
experiment_name = 'experiment_with_mlflow'
mlflow.set_experiment(experiment_name)

with mlflow.start_run():
    mlflow.log_metric('alpha', 0.03)
```

## <a name="track-remote-runs"></a>Sledovat vzdálené běhy

MLflow sledování pomocí Azure Machine Learning umožňuje ukládat protokolované metriky a artefakty ze vzdálených běhů do pracovního prostoru Azure Machine Learning.

Vzdálené spuštění vám umožní naučit vaše modely s výkonnějšími výpočetními prostředky, jako jsou virtuální počítače s podporou GPU nebo Výpočetní prostředky služby Machine Learning clustery. Další informace o různých možnostech výpočtů najdete v tématu [nastavení cílových výpočetních prostředků pro školení modelů](how-to-set-up-training-targets.md) .

Proveďte konfiguraci prostředí COMPUTE a školicího běhu s [`Environment`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py) třídou. Balíčky `mlflow` zahrnutí `azureml-mlflow` a PIP v [`CondaDependencies`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.conda_dependencies.condadependencies?view=azure-ml-py) oddílu prostředí. Pak se [`ScriptRunConfig`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.script_run_config.scriptrunconfig?view=azure-ml-py) pomocí vzdálených výpočtů vystavíte jako cíl výpočtů.

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

V školicím skriptu proveďte import `mlflow` tak, aby používal rozhraní API pro protokolování MLflow, a spusťte protokolování metriky spuštění.

```Python
import mlflow

with mlflow.start_run():
    mlflow.log_metric('example', 1.23)
```

Pomocí této výpočetní a školicí konfigurace spuštění použijte `Experiment.submit('train.py')` metodu pro odeslání běhu. Tato metoda automaticky nastaví identifikátor URI sledování MLflow a přesměruje protokolování z MLflow do vašeho pracovního prostoru.

```Python
run = exp.submit(src)
```

## <a name="track-azure-databricks-runs"></a>Sledování spuštění Azure Databricks

MLflow sledování pomocí Azure Machine Learning umožňuje ukládat protokolované metriky a artefakty z Azure Databricks se spouští v pracovním prostoru Azure Machine Learning.

Chcete-li spustit Mlflow experimenty s Azure Databricks, je třeba nejprve vytvořit [pracovní prostor Azure Databricks a cluster](https://docs.microsoft.com/azure/azure-databricks/quickstart-create-databricks-workspace-portal). V clusteru nezapomeňte nainstalovat knihovnu *AzureML-mlflow* z PyPi, abyste zajistili, že má cluster přístup k potřebným funkcím a třídám.

Odtud importujte Poznámkový blok experimentů, připojte ho ke clusteru Azure Databricks a spusťte experiment. 

### <a name="install-libraries"></a>Instalovat knihovny

Pokud chcete do clusteru nainstalovat knihovny, přejděte na kartu **knihovny** a klikněte na **instalovat nové** .

 ![mlflow s diagramem Azure Machine Learning](./media/how-to-use-mlflow/azure-databricks-cluster-libraries.png)

Do pole **balíček** zadejte AzureML-mlflow a pak klikněte na nainstalovat. Opakujte tento krok, jak je potřeba k instalaci dalších dalších balíčků do clusteru pro váš experiment.

 ![mlflow s diagramem Azure Machine Learning](./media/how-to-use-mlflow/install-libraries.png)

### <a name="set-up-your-notebook-and-workspace"></a>Nastavení poznámkového bloku a pracovního prostoru

Po nastavení clusteru importujte Poznámkový blok experimentu, otevřete ho a připojte k němu svůj cluster.

Následující kód by měl být v poznámkovém bloku experimentu. Tento kód získá podrobné informace o vašem předplatném Azure, aby se vytvořila instance pracovního prostoru. Tento kód předpokládá, že máte existující skupinu prostředků a Azure Machine Learning pracovní prostor, jinak je můžete [vytvořit](how-to-manage-workspace.md). 

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

#### <a name="connect-your-azure-databricks-and-azure-machine-learning-workspaces"></a>Připojení pracovních prostorů Azure Databricks a Azure Machine Learning

V [Azure Portal](https://ms.portal.azure.com)můžete pracovní prostor Azure DATABRICKS (ADB) propojit s novým nebo existujícím Azure Machine Learning pracovním prostorem. Provedete to tak, že přejdete do svého pracovního prostoru ADB a v pravém dolním rohu vyberete tlačítko **propojit Azure Machine Learning pracovní prostor** . Propojení pracovních prostorů umožňuje sledovat data experimentů v pracovním prostoru Azure Machine Learning. 

### <a name="link-mlflow-tracking-to-your-workspace"></a>Propojení MLflow sledování s vaším pracovním prostorem

Po vytvoření instance pracovního prostoru nastavte identifikátor URI sledování MLflow. Provedete to tak, že propojíte sledování MLflow k pracovnímu prostoru Azure Machine Learning. Po propojování budou všechny experimenty na spravované službě sledování Azure Machine Learning.

#### <a name="directly-set-mlflow-tracking-in-your-notebook"></a>Přímo nastavit sledování MLflow v poznámkovém bloku

```python
uri = ws.get_mlflow_tracking_uri()
mlflow.set_tracking_uri(uri)
```

Ve školicím skriptu importujte mlflow a použijte rozhraní API pro protokolování MLflow a spusťte protokolování metriky spuštění. Následující příklad zaznamená metriku epocha ztrát. 

```python
import mlflow 
mlflow.log_metric('epoch_loss', loss.item()) 
```

#### <a name="automate-setting-mlflow-tracking"></a>Automatizace nastavení sledování MLflow

Místo ručního nastavení sledovacího identifikátoru URI v každé následné relaci poznámkového bloku v clusterech to udělejte automaticky pomocí tohoto [Azure Machine Learning sledování skriptu init clusteru](https://github.com/Azure/MachineLearningNotebooks/blob/3ce779063b000e0670bdd1acc6bc3a4ee707ec13/how-to-use-azureml/azure-databricks/linking/README.md).

Pokud je nakonfigurováno správně, můžete zobrazit data sledování MLflow v REST API Azure Machine Learning a všech klientech a v Azure Databricks prostřednictvím uživatelského rozhraní MLflow nebo pomocí klienta MLflow.

## <a name="view-metrics-and-artifacts-in-your-workspace"></a>Zobrazení metrik a artefaktů ve vašem pracovním prostoru

Metriky a artefakty z protokolování MLflow se uchovávají ve vašem pracovním prostoru. Pokud je chcete kdykoli zobrazit, přejděte do pracovního prostoru a v pracovním prostoru v [Azure Machine Learning Studiu](https://ml.azure.com)Najděte experiment podle názvu.  Nebo spusťte následující kód. 

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

[MLflow s poznámkovým blokům Azure ml](https://aka.ms/azureml-mlflow-examples) ukazují a rozšiřují koncepty prezentované v tomto článku.

## <a name="next-steps"></a>Další kroky
* [Spravujte své modely](concept-model-management-and-deployment.md).
* Monitorujte v produkčních modelech [přenos dat](how-to-monitor-data-drift.md).
