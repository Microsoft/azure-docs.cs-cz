---
title: MLflow sledování experimentů ML
titleSuffix: Azure Machine Learning
description: Nastavte MLflow s Azure Machine Learning pro protokolování metrik a artefaktů z modelů ML a nasaďte vaše ML modely jako webovou službu.
services: machine-learning
author: rastala
ms.author: roastala
ms.service: machine-learning
ms.subservice: core
ms.reviewer: nibaccam
ms.date: 06/04/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python
ms.openlocfilehash: e31fdec18ab4c6135031bf21d2387585141c2735
ms.sourcegitcommit: 43558caf1f3917f0c535ae0bf7ce7fe4723391f9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/11/2020
ms.locfileid: "90018216"
---
# <a name="track-model-metrics-and-deploy-ml-models-with-mlflow-and-azure-machine-learning-preview"></a>Sledujte metriky modelu a nasaďte modely ML pomocí MLflow a Azure Machine Learning (Preview).

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

V tomto článku se dozvíte, jak povolit sledování identifikátorů URI a protokolovacího rozhraní API pro MLflow, označované jako [sledování MLflow](https://mlflow.org/docs/latest/quickstart.html#using-the-tracking-api), pro propojení MLflow experimentů a Azure Machine Learning.  Umožní vám to,

+ Sledujte metriky a metriky experimentů a artefakty v [pracovním prostoru Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/concept-azure-machine-learning-architecture#workspaces). Pokud již pro vaše experimenty používáte sledování MLflow, pracovní prostor poskytuje centralizované, zabezpečené a škálovatelné umístění pro ukládání výukových metrik a modelů.

+ Nasaďte MLflow experimenty jako webovou službu Azure Machine Learning. Nasazením jako webové služby můžete v produkčních modelech použít funkce monitorování Azure Machine Learning a detekce posunu dat. 

[MLflow](https://www.mlflow.org) je open source knihovna pro správu životního cyklu experimentů ve strojovém učení. Sledování MLFlow je součást MLflow, která protokoluje a sleduje metriky běhu a artefakty modelu bez ohledu na to, jestli je vaše experimenty v místním počítači, ve vzdáleném cílovém výpočetním prostředí, virtuálním počítači nebo Azure Databricksm clusteru. 

>[!NOTE]
> Jako open source knihovna se MLflow změny často. Funkce, které jsou dostupné prostřednictvím Azure Machine Learning a integrace MLflow, by se měly považovat za verzi Preview, a není plně podporovaná Microsoftem.

Následující diagram znázorňuje, že se sledováním MLflow sledujete metriky spuštění experimentu a artefakty modelu úložiště v pracovním prostoru Azure Machine Learning.

![mlflow s diagramem Azure Machine Learning](./media/how-to-use-mlflow/mlflow-diagram-track.png)

> [!TIP]
> Informace v tomto dokumentu jsou primárně určené pro odborníky přes data a vývojáře, kteří chtějí monitorovat proces školení modelu. Pokud jste správcem a chcete monitorovat využití prostředků a události z Azure Machine Learning, jako jsou kvóty, dokončené školicí běhy nebo dokončená nasazení modelu, přečtěte si téma [monitorování Azure Machine Learning](monitor-azure-machine-learning.md).

## <a name="compare-mlflow-and-azure-machine-learning-clients"></a>Porovnání klientů MLflow a Azure Machine Learning

 Níže uvedená tabulka shrnuje různé klienty, kteří můžou použít Azure Machine Learning a jejich příslušné funkce.

 MLflow Tracking nabízí funkce protokolování metrik a úložiště artefaktů, které jsou k dispozici pouze v případě, že jsou k dispozici pouze v [Azure Machine Learning Python SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py&preserve-view=true)


| Schopnost | MLflow sledování & nasazení | Azure Machine Learning Python SDK |  Azure Machine Learning CLI | Azure Machine Learning Studio|
|---|---|---|---|---|
| Spravovat pracovní prostor |   | ✓ | ✓ | ✓ |
| Použití úložišť dat  |   | ✓ | ✓ | |
| Metriky protokolů      | ✓ | ✓ |   | |
| Nahrání artefaktů | ✓ | ✓ |   | |
| Zobrazení metrik     | ✓ | ✓ | ✓ | ✓ |
| Správa výpočetních služeb   |   | ✓ | ✓ | ✓ |
| Modely nasazení    | ✓ | ✓ | ✓ | ✓ |
|Monitorování výkonu modelu||✓|  |   |
| Zjišťování odchylek dat |   | ✓ |   | ✓ |

## <a name="prerequisites"></a>Požadavky

* [Nainstalujte MLflow.](https://mlflow.org/docs/latest/quickstart.html)
* [Nainstalujte sadu Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py&preserve-view=true) do místního počítače, kterou sada SDK poskytuje pro přístup k vašemu pracovnímu prostoru pro MLflow.
* [Vytvořte pracovní prostor Azure Machine Learning](how-to-manage-workspace.md).

## <a name="track-local-runs"></a>Sledovat místní běhy

MLflow sledování pomocí Azure Machine Learning umožňuje ukládat protokolované metriky a artefakty z místních běhů do pracovního prostoru Azure Machine Learning.

Nainstalujte `azureml-mlflow` balíček, aby používal sledování MLflow s Azure Machine Learning v pokusůch místně spuštěné v editoru Jupyter notebook nebo kódu.

```shell
pip install azureml-mlflow
```

Importujte `mlflow` třídy a a [`Workspace`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace(class)?view=azure-ml-py&preserve-view=true) Získejte přístup k identifikátoru URI sledování MLflow a nakonfigurujte svůj pracovní prostor.

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

MLflow sledování pomocí Azure Machine Learning umožňuje ukládat protokolované metriky a artefakty ze vzdálených běhů do pracovního prostoru Azure Machine Learning.

Vzdálené spuštění vám umožní naučit vaše modely s výkonnějšími výpočetními prostředky, jako jsou virtuální počítače s podporou GPU nebo Výpočetní prostředky služby Machine Learning clustery. Další informace o různých možnostech výpočtů najdete v tématu [použití výpočetních cílů pro školení modelů](how-to-set-up-training-targets.md) .

Proveďte konfiguraci prostředí COMPUTE a školicího běhu s [`Environment`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py&preserve-view=true) třídou. `mlflow`Balíčky zahrnutí a `azureml-mlflow` PIP v oddílu prostředí [`CondaDependencies`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.conda_dependencies.condadependencies?view=azure-ml-py&preserve-view=true) . Pak  [`ScriptRunConfig`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.script_run_config.scriptrunconfig?view=azure-ml-py&preserve-view=true) se pomocí vzdálených výpočtů vystavíte jako cíl výpočtů.

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

## <a name="deploy-mlflow-models-as-a-web-service"></a>Nasazení modelů MLflow jako webové služby

Nasazení MLflow experimentů jako webové služby Azure Machine Learning umožňuje využívat možnosti Azure Machine Learning správy modelů a funkce detekce posunu dat a aplikovat je na produkční modely.

Následující diagram znázorňuje, že s rozhraním API pro nasazení MLflow můžete nasadit stávající modely MLflow jako webovou službu Azure Machine Learning bez ohledu na jejich architektury--PyTorch, Tensorflow, scikit-učit, ONNX atd. a spravovat produkční modely v pracovním prostoru.

![mlflow s diagramem Azure Machine Learning](./media/how-to-use-mlflow/mlflow-diagram-deploy.png)

### <a name="log-your-model"></a>Protokolovat model

Než budete moct nasadit, ujistěte se, že je váš model uložený, abyste na něj mohli odkazovat a umístění cesty pro nasazení. Ve školicím skriptu by měl být podobný kódu jako následující metoda [mlflow. skriptu sklearn. log_model ()](https://www.mlflow.org/docs/latest/python_api/mlflow.sklearn.html) , která uloží váš model do určeného adresáře výstupů. 

```python
# change sklearn to pytorch, tensorflow, etc. based on your experiment's framework 
import mlflow.sklearn

# Save the model to the outputs directory for capture
mlflow.sklearn.log_model(regression_model, model_save_path)
```
>[!NOTE]
> Zahrňte `conda_env` parametr pro předání slovníku reprezentující závislosti a prostředí, ve kterém by měl být tento model spuštěn.

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

### <a name="deploy-the-model"></a>Nasazení modelu

K nasazení modelu jako webové služby použijte sadu SDK Azure Machine Learning.

Nejdřív zadejte konfiguraci nasazení. Azure Container instance (ACI) je vhodná volba pro nasazení s rychlým vývojem a testováním, zatímco služba Azure Kubernetes Service (AKS) je vhodná pro škálovatelná produkční nasazení.

#### <a name="deploy-to-aci"></a>Nasazení do ACI

Nastavte konfiguraci nasazení pomocí metody [deploy_configuration ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aciwebservice?view=azure-ml-py#&preserve-view=truedeploy-configuration-cpu-cores-none--memory-gb-none--tags-none--properties-none--description-none--location-none--auth-enabled-none--ssl-enabled-none--enable-app-insights-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--ssl-cname-none--dns-name-label-none-) . Můžete také přidat značky a popisy, které vám pomohou sledovat webovou službu.

```python
from azureml.core.webservice import AciWebservice, Webservice

# Configure 
aci_config = AciWebservice.deploy_configuration(cpu_cores=1, 
                                                memory_gb=1, 
                                                tags={'method' : 'sklearn'}, 
                                                description='Diabetes model',
                                                location='eastus2')
```

Pak zaregistrujte a nasaďte model pomocí metody [nasazení](/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#&preserve-view=truedeploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-) Azure Machine Learning SDK. 

```python
(webservice,model) = mlflow.azureml.deploy( model_uri='runs:/{}/{}'.format(run.id, model_path),
                      workspace=ws,
                      model_name='sklearn-model', 
                      service_name='diabetes-model-1', 
                      deployment_config=aci_config, 
                      tags=None, mlflow_home=None, synchronous=True)

webservice.wait_for_deployment(show_output=True)
```
#### <a name="deploy-to-aks"></a>Nasazení do AKS

K nasazení na AKS nejprve vytvořte cluster AKS. Vytvořte cluster AKS pomocí metody [ComputeTarget. Create ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.computetarget?view=azure-ml-py#&preserve-view=truecreate-workspace--name--provisioning-configuration-) . Vytvoření nového clusteru může trvat 20-25 minut.

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
Nastavte konfiguraci nasazení pomocí metody [deploy_configuration ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aciwebservice?view=azure-ml-py#&preserve-view=truedeploy-configuration-cpu-cores-none--memory-gb-none--tags-none--properties-none--description-none--location-none--auth-enabled-none--ssl-enabled-none--enable-app-insights-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--ssl-cname-none--dns-name-label-none-) . Můžete také přidat značky a popisy, které vám pomohou sledovat webovou službu.

```python
from azureml.core.webservice import Webservice, AksWebservice

# Set the web service configuration (using default here with app insights)
aks_config = AksWebservice.deploy_configuration(enable_app_insights=True, compute_target_name='aks-mlflow')

```

Pak nasaďte bitovou kopii pomocí sady Azure Machine Learning SDK [Deploy ()] (pak zaregistrujte a nasaďte model pomocí metody [nasazení](/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#&preserve-view=truedeploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-) Azure Machine Learning SDK. 

```python
# Webservice creation using single command
from azureml.core.webservice import AksWebservice, Webservice
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

[MLflow s poznámkovým blokům Azure ml](https://aka.ms/azureml-mlflow-examples) ukazují a rozšiřují koncepty prezentované v tomto článku.

## <a name="next-steps"></a>Další kroky
* [Spravujte své modely](concept-model-management-and-deployment.md).
* Monitorujte v produkčních modelech [přenos dat](how-to-monitor-data-drift.md).
