---
title: MLflow sledování experimentů ML
titleSuffix: Azure Machine Learning
description: Nastavte MLflow s Azure Machine Learning k protokolování metrik & artefakty a nasaďte modely z datacihly, místního prostředí nebo prostředí virtuálních počítačů.
services: machine-learning
author: rastala
ms.author: roastala
ms.service: machine-learning
ms.subservice: core
ms.reviewer: nibaccam
ms.topic: conceptual
ms.date: 09/23/2019
ms.custom: seodec18
ms.openlocfilehash: 9b6efdc75c15e9686728236f82fea8794f3782bf
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/21/2019
ms.locfileid: "74276644"
---
# <a name="track-metrics-and-deploy-models-with-mlflow-and-azure-machine-learning-preview"></a>Sledování metrik a nasazení modelů pomocí MLflow a Azure Machine Learning (Preview)
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

V tomto článku se dozvíte, jak povolit sledování identifikátoru URI a protokolovacího rozhraní API pro MLflow, označované jako [sledování MLflow](https://mlflow.org/docs/latest/quickstart.html#using-the-tracking-api), s Azure Machine Learning. V takovém případě vám umožní:

+ Sledujte metriky a metriky experimentů a artefakty v [pracovním prostoru Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/service/concept-azure-machine-learning-architecture#workspaces). Pokud již pro vaše experimenty používáte sledování MLflow, pracovní prostor poskytuje centralizované, zabezpečené a škálovatelné umístění pro ukládání výukových metrik a modelů.

+ Nasaďte MLflow experimenty jako webovou službu Azure Machine Learning. Nasazením jako webové služby můžete v produkčních modelech použít funkce monitorování Azure Machine Learning a detekce posunu dat. 

[MLflow](https://www.mlflow.org) je open source knihovna pro správu životního cyklu experimentů ve strojovém učení. Sledování MLFlow je součást MLflow, která protokoluje a sleduje metriky běhu a artefakty modelu bez ohledu na to, ve kterém prostředí experimentů nezáleží na vzdáleném cíli výpočtů, na virtuálním počítači, místně na vašem počítači nebo na Azure Databricksovém clusteru.

Následující diagram znázorňuje, že se sledováním MLflow sledujete metriky spuštění experimentu a artefakty modelu úložiště v pracovním prostoru Azure Machine Learning.

![mlflow s diagramem Azure Machine Learning](media/how-to-use-mlflow/mlflow-diagram-track.png)

> [!TIP]
> Informace v tomto dokumentu jsou primárně určené pro odborníky přes data a vývojáře, kteří chtějí monitorovat proces školení modelu. Pokud jste správcem a chcete monitorovat využití prostředků a události z Azure Machine Learningu, jako jsou kvóty, dokončené školicí běhy nebo dokončená nasazení modelu, přečtěte si téma [monitorování Azure Machine Learning](monitor-azure-machine-learning.md).

## <a name="compare-mlflow-and-azure-machine-learning-clients"></a>Porovnání klientů MLflow a Azure Machine Learning

 Níže uvedená tabulka shrnuje různé klienty, kteří můžou použít Azure Machine Learning a jejich příslušné funkce.

 MLflow Tracking nabízí funkce protokolování metrik a úložiště artefaktů, které jsou k dispozici pouze v případě, že jsou k dispozici pouze v [Azure Machine Learning Python SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)


| | MLflow sledování & nasazení | Azure Machine Learning Python SDK |  Azure Machine Learning CLI | Azure Machine Learning Studio|
|---|---|---|---|---|
| Spravovat pracovní prostor |   | ✓ | ✓ | ✓ |
| Použití úložišť dat  |   | ✓ | ✓ | |
| Metriky protokolu      | ✓ | ✓ |   | |
| Nahrání artefaktů | ✓ | ✓ |   | |
| Zobrazit metriky     | ✓ | ✓ | ✓ | ✓ |
| Správa výpočetních služeb   |   | ✓ | ✓ | ✓ |
| Modely nasazení    | ✓ | ✓ | ✓ | ✓ |
|Monitorování výkonu modelu||✓|  |   |
| Zjišťování odchylek dat |   | ✓ |   | ✓ |

## <a name="prerequisites"></a>Požadavky

* [Nainstalujte MLflow.](https://mlflow.org/docs/latest/quickstart.html)
* [Nainstalujte sadu Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py) do místního počítače, kterou sada SDK poskytuje pro přístup k vašemu pracovnímu prostoru pro MLflow.
* [Vytvořte pracovní prostor Azure Machine Learning](how-to-manage-workspace.md).

## <a name="track-local-runs"></a>Sledovat místní běhy

MLflow sledování pomocí Azure Machine Learning umožňuje ukládat protokolované metriky a artefakty z místních běhů do pracovního prostoru Azure Machine Learning.

Nainstalujte balíček `azureml-contrib-run` pro použití sledování MLflow s Azure Machine Learning na experimentech místně spouštěných v Jupyter Notebook nebo editoru kódu.

```shell
pip install azureml-contrib-run
```

>[!NOTE]
>Obor názvů AzureML. contrib se často mění, protože budeme spolupracovat na vylepšení služby. V takovém případě by se cokoli v tomto oboru názvů měly považovat za verzi Preview a společnost Microsoft je plně Nepodporovaná.

Importujte třídy `mlflow` a [`Workspace`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace(class)?view=azure-ml-py) pro přístup k identifikátoru URI sledování MLflow a nakonfigurujte pracovní prostor.

V následujícím kódu metoda `get_mlflow_tracking_uri()` přiřadí k pracovnímu prostoru jedinečnou adresu identifikátoru URI sledování `ws`a `set_tracking_uri()` odkazuje na adresu URI sledování MLflow.

```Python
import mlflow
from azureml.core import Workspace

ws = Workspace.from_config()

mlflow.set_tracking_uri(ws.get_mlflow_tracking_uri())
```

>[!NOTE]
>Identifikátor URI sledování je platný až do hodin nebo méně. Pokud po nějaké době nečinnosti svůj skript restartujete, použijte rozhraní get_mlflow_tracking_uri API k získání nového identifikátoru URI.

Nastavte název experimentu MLflow pomocí `set_experiment()` a spusťte školicí běh s `start_run()`. Pak pomocí `log_metric()` aktivujte rozhraní API pro protokolování MLflow a zahajte protokolování metriky pro školicí běh.

```Python
experiment_name = 'experiment_with_mlflow'
mlflow.set_experiment(experiment_name)

with mlflow.start_run():
    mlflow.log_metric('alpha', 0.03)
```

## <a name="track-remote-runs"></a>Sledovat vzdálené běhy

MLflow sledování pomocí Azure Machine Learning umožňuje ukládat protokolované metriky a artefakty ze vzdálených běhů do pracovního prostoru Azure Machine Learning.

Vzdálené spuštění vám umožní naučit vaše modely s výkonnějšími výpočetními prostředky, jako jsou virtuální počítače s podporou GPU nebo Výpočetní prostředky služby Machine Learning clustery. Další informace o různých možnostech výpočtů najdete v tématu [nastavení cílových výpočetních prostředků pro školení modelů](how-to-set-up-training-targets.md) .

Nakonfigurujte prostředí COMPUTE a školicích programů pomocí třídy [`Environment`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py) . Do oddílu [`CondaDependencies`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.conda_dependencies.condadependencies?view=azure-ml-py) prostředí zahrnout balíčky `mlflow` a `azure-contrib-run` PIP. Pak vytvořte [`ScriptRunConfig`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.script_run_config.scriptrunconfig?view=azure-ml-py) s využitím vzdáleného COMPUTE jako cíle výpočtů.

```Python
from azureml.core import Environment
from azureml.core.conda_dependencies import CondaDependencies
from azureml.core import ScriptRunConfig

exp = Experiment(workspace = 'my_workspace',
                 name='my_experiment')

mlflow_env = Environment(name='mlflow-env')

cd = CondaDependencies.create(pip_packages=['mlflow', 'azureml-contrib-run'])

mlflow_env.python.conda_dependencies = cd

src = ScriptRunConfig(source_directory='./my_script_location', script='my_training_script.py')

src.run_config.target = 'my-remote-compute-compute'
src.run_config.environment = mlflow_env
```

V školicím skriptu importujte `mlflow` pro použití rozhraní API pro protokolování MLflow a spusťte protokolování metriky spuštění.

```Python
import mlflow

with mlflow.start_run():
    mlflow.log_metric('example', 1.23)
```

Pomocí této výpočetní a školicí konfigurace spuštění odešlete běh pomocí metody `Experiment.submit('train.py')`. Tato metoda automaticky nastaví identifikátor URI sledování MLflow a přesměruje protokolování z MLflow do vašeho pracovního prostoru.

```Python
run = exp.submit(src)
```

## <a name="track-azure-databricks-runs"></a>Sledování spuštění Azure Databricks

MLflow sledování pomocí Azure Machine Learning umožňuje ukládat zaznamenané metriky a artefakty z vašich datacihlů, které běží ve vašem pracovním prostoru Azure Machine Learning.

Pokud chcete Mlflow experimenty spustit pomocí Azure Databricks, musíte nejdřív vytvořit [pracovní prostor Azure Databricks a cluster](https://docs.microsoft.com/azure/azure-databricks/quickstart-create-databricks-workspace-portal) .

V clusteru nezapomeňte nainstalovat knihovnu *AzureML-mlflow* z PyPi, abyste zajistili, že má cluster přístup k potřebným funkcím a třídám.
Odsud naimportujte Poznámkový blok experimentu, připojte k němu svůj cluster a spusťte experiment. 

### <a name="install-libraries"></a>Instalovat knihovny

Pokud chcete do clusteru nainstalovat knihovny, přejděte na kartu **knihovny** a klikněte na **instalovat nové** .

 ![mlflow s diagramem Azure Machine Learning](media/how-to-use-mlflow/azure-databricks-cluster-libraries.png)

Do pole **balíček** zadejte AzureML-mlflow a pak klikněte na nainstalovat. Opakujte tento krok, jak je potřeba k instalaci dalších dalších balíčků do clusteru pro váš experiment.

 ![mlflow s diagramem Azure Machine Learning](media/how-to-use-mlflow/install-libraries.png)

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

Pokud jste správně nakonfigurovali, můžete zobrazit data sledování MLflow v REST API Azure Machine Learning a všech klientech a v Azure Databricks prostřednictvím uživatelského rozhraní MLflow nebo pomocí klienta MLflow.

## <a name="view-metrics-and-artifacts-in-your-workspace"></a>Zobrazení metrik a artefaktů ve vašem pracovním prostoru

Metriky a artefakty z protokolování MLflow se uchovávají ve vašem pracovním prostoru. Pokud je chcete kdykoli zobrazit, přejděte do pracovního prostoru a v pracovním prostoru v [Azure Machine Learning Studiu](https://ml.azure.com)Najděte experiment podle názvu.  Nebo spusťte následující kód. 

```python
run.get_metrics()
ws.get_details()
```

## <a name="deploy-mlflow-models-as-a-web-service"></a>Nasazení modelů MLflow jako webové služby

Nasazení MLflow experimentů jako webové služby Azure Machine Learning umožňuje využívat možnosti Azure Machine Learning správy modelů a funkce detekce posunu dat a aplikovat je na produkční modely.

Následující diagram znázorňuje, že s rozhraním API pro nasazení MLflow můžete nasadit stávající modely MLflow jako webovou službu Azure Machine Learning bez ohledu na jejich architektury – PyTorch, Tensorflow, scikit-učení, ONNX atd. a spravovat produkční modely v nástroji. Váš pracovní prostor.

![mlflow s diagramem Azure Machine Learning](media/how-to-use-mlflow/mlflow-diagram-deploy.png)

### <a name="log-your-model"></a>Protokolovat model

Než budete moct nasadit, ujistěte se, že je váš model uložený, abyste na něj mohli odkazovat a umístění cesty pro nasazení. Ve školicím skriptu by měl být podobný kódu jako následující metoda [mlflow. skriptu sklearn. log_model ()](https://www.mlflow.org/docs/latest/python_api/mlflow.sklearn.html) , která uloží váš model do určeného adresáře výstupů. 

```python
# change sklearn to pytorch, tensorflow, etc. based on your experiment's framework 
import mlflow.sklearn

# Save the model to the outputs directory for capture
mlflow.sklearn.log_model(regression_model, model_save_path)
```
>[!NOTE]
> Zahrňte parametr `conda_env` pro předání slovníku reprezentující závislosti a prostředí, ve kterém by měl být tento model spuštěn.

### <a name="retrieve-model-from-previous-run"></a>Načíst model z předchozího běhu

Pro načtení běhu budete potřebovat ID běhu a cestu v historii spuštění, kde byl model uložen. 

```python
# gets the list of runs for your experiment as an array
experiment_name = 'experiment-with-mlflow'
exp = ws.experiments[experiment_name]
runs = list(exp.get_runs())

# get the run ID and the path in run history
runid = runs[0].id
model_save_path = 'model'
```

### <a name="create-docker-image"></a>Vytvořit obrázek Docker

Funkce `mlflow.azureml.build_image()` sestaví image Docker z uloženého modelu v rámci způsobu, který se používá. Automaticky vytvoří kód obálky Inferencing specifický pro rozhraní a určí závislosti balíčku. Zadejte cestu k modelu, váš pracovní prostor, ID běhu a další parametry.

Následující kód vytvoří image Docker pomocí *Run:/< spustit. id >/model* jako cestu model_uri pro experiment Scikit-učení.

```python
import mlflow.azureml

azure_image, azure_model = mlflow.azureml.build_image(model_uri='runs:/{}/{}'.format(runid, model_save_path),
                                                      workspace=ws,
                                                      model_name='sklearn-model',
                                                      image_name='sklearn-image',
                                                      synchronous=True)
```
Vytvoření image Docker může trvat několik minut. 

### <a name="deploy-the-docker-image"></a>Nasazení image Docker 

Po vytvoření image použijte sadu SDK Azure Machine Learning k nasazení image jako webové služby.

Nejdřív zadejte konfiguraci nasazení. Azure Container instance (ACI) je vhodná volba pro nasazení s rychlým vývojem a testováním, zatímco služba Azure Kubernetes Service (AKS) je vhodná pro škálovatelná produkční nasazení.

#### <a name="deploy-to-aci"></a>Nasazení do ACI

Nastavte konfiguraci nasazení pomocí metody [deploy_configuration ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aciwebservice?view=azure-ml-py#deploy-configuration-cpu-cores-none--memory-gb-none--tags-none--properties-none--description-none--location-none--auth-enabled-none--ssl-enabled-none--enable-app-insights-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--ssl-cname-none--dns-name-label-none-) . Můžete také přidat značky a popisy, které vám pomohou sledovat webovou službu.

```python
from azureml.core.webservice import AciWebservice, Webservice

# Configure 
aci_config = AciWebservice.deploy_configuration(cpu_cores=1, 
                                                memory_gb=1, 
                                                tags={'method' : 'sklearn'}, 
                                                description='Diabetes model',
                                                location='eastus2')
```

Pak nasaďte bitovou kopii pomocí metody [deploy_from_image ()](/python/api/azureml-core/azureml.core.webservice.webservice(class)?view=azure-ml-py#deploy-from-image-workspace--name--image--deployment-config-none--deployment-target-none--overwrite-false-) sady Azure Machine Learning SDK. 

```python
webservice = Webservice.deploy_from_image( image=azure_image, 
                                           workspace=ws, 
                                           name='diabetes-model-1', 
                                           deployment_config=aci_config)

webservice.wait_for_deployment(show_output=True)
```
#### <a name="deploy-to-aks"></a>Nasazení do AKS

Pokud chcete nasadit na AKS, nejdřív vytvořte cluster AKS a převeďte image Docker, kterou chcete nasadit. V tomto příkladu převeďte dříve vytvořenou bitovou kopii z nasazení ACI.

K získání image z předchozího nasazení ACI použijte třídu [Image](https://docs.microsoft.com/python/api/azureml-core/azureml.core.image.image.image?view=azure-ml-py) . 

```python
from azureml.core.image import Image

# Get the image by name, you can change this based on the image you want to deploy
myimage = Image(workspace=ws, name='sklearn-image') 
```

Vytvořte cluster AKS pomocí metody [ComputeTarget. Create ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.computetarget?view=azure-ml-py#create-workspace--name--provisioning-configuration-) . Vytvoření nového clusteru může trvat 20-25 minut.

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
Nastavte konfiguraci nasazení pomocí metody [deploy_configuration ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aciwebservice?view=azure-ml-py#deploy-configuration-cpu-cores-none--memory-gb-none--tags-none--properties-none--description-none--location-none--auth-enabled-none--ssl-enabled-none--enable-app-insights-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--ssl-cname-none--dns-name-label-none-) . Můžete také přidat značky a popisy, které vám pomohou sledovat webovou službu.

```python
from azureml.core.webservice import Webservice, AksWebservice
from azureml.core.image import ContainerImage

# Set the web service configuration (using default here with app insights)
aks_config = AksWebservice.deploy_configuration(enable_app_insights=True)

# Unique service name
service_name ='aks-service'
```

Pak nasaďte bitovou kopii pomocí metody [deploy_from_image ()](/python/api/azureml-core/azureml.core.webservice.webservice(class)?view=azure-ml-py#deploy-from-image-workspace--name--image--deployment-config-none--deployment-target-none--overwrite-false-) sady Azure Machine Learning SDK. 

```python
# Webservice creation using single command
aks_service = Webservice.deploy_from_image( workspace=ws, 
                                            name=service_name,
                                            deployment_config = aks_config
                                            image = myimage,
                                            deployment_target = aks_target)

aks_service.wait_for_deployment(show_output=True)
```

Nasazení služby může trvat několik minut.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud neplánujete použít zaznamenané metriky a artefakty v pracovním prostoru, možnost jejich odstranění je momentálně nedostupná. Místo toho odstraňte skupinu prostředků, která obsahuje účet úložiště a pracovní prostor, takže se vám neúčtují žádné poplatky:

1. Úplně nalevo na webu Azure Portal vyberte **Skupiny prostředků**.

   ![Odstranit v Azure Portal](media/how-to-use-mlflow/delete-resources.png)

1. V seznamu vyberte skupinu prostředků, kterou jste vytvořili.

1. Vyberte **Odstranit skupinu prostředků**.

1. Zadejte název skupiny prostředků. Vyberte **Odstranit**.


## <a name="example-notebooks"></a>Příklad poznámkové bloky

[MLflow s poznámkovým blokům Azure ml](https://aka.ms/azureml-mlflow-examples) ukazují a rozšiřují koncepty prezentované v tomto článku.

## <a name="next-steps"></a>Další kroky
* [Spravujte své modely](concept-model-management-and-deployment.md).
* Monitorujte v produkčních modelech [přenos dat](how-to-monitor-data-drift.md).
