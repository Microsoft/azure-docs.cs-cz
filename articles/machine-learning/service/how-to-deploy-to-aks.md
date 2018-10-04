---
title: Ze služby Azure Machine Learning nasazovat modely do Kubernetes | Dokumentace Microsoftu
description: Informace o nasazení modelu ze služby Azure Machine Learning do služby Azure Kubernetes Service. Nasazení modelu jako webové služby. Azure Kubernetes Service je vhodný pro úlohy v produkčním prostředí vysoce škálovatelné.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.author: raymondl
author: raymondlaghaeian
manager: cgronlun
ms.reviewer: larryfr
ms.date: 09/24/2018
ms.openlocfilehash: e467445b631eca3084f8ec01a854681311979c93
ms.sourcegitcommit: f58fc4748053a50c34a56314cf99ec56f33fd616
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/04/2018
ms.locfileid: "48268406"
---
# <a name="how-to-deploy-models-from-azure-machine-learning-service-to-azure-kubernetes-service"></a>Jak nasadit modely do služby Azure Kubernetes Service ze služby Azure Machine Learning

Pro zajištění vysoce škálovatelné produkční scénáře, můžete svůj model dokážete nasadit do Azure Kubernetes Service (AKS). Azure Machine Learning můžete použít existující cluster AKS nebo novém clusteru vytvoří během nasazení. K zadání nasazení modelu jako webové služby.

Nasazení do AKS nabízí automatické škálování, protokolování, shromažďování dat modelu a Rychlá doba odezvy pro webovou službu.

## <a name="prerequisites"></a>Požadavky

- Předplatné Azure. Pokud ho nemáte, než začnete, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

- Azure service pracovního prostoru Machine Learning, místní adresář obsahující skripty a sady SDK Azure Machine Learning pro Python nainstalován. Další informace o získání těchto nezbytných podmínkách používání [jak nakonfigurovat prostředí pro vývoj](how-to-configure-environment.md) dokumentu.

- Model trénovaného strojového učení. Pokud ho nemáte, přečtěte si článek [trénování modelů klasifikace image](tutorial-train-models-with-aml.md) kurzu.

## <a name="initialize-the-workspace"></a>Inicializovat pracovní prostor

Inicializace pracovního prostoru, načíst `config.json` soubor, který obsahuje informace o pracovním prostoru.

```python
from azureml.coreazureml import Workspace

# Load existing workspace from the config file info.
ws = Workspace.from_config()
```

## <a name="register-the-model"></a>Zaregistrujte model

Zaregistrovat existující model, určete cestu modelu, popisu a značkách.

```python
from azureml.core.model import Model

model = Model.register(model_path = "model.pkl", # this points to a local file
                        model_name = "best_model", # this is the name the model is registered as
                        tags = {"data": "diabetes", "type": "regression"},
                        description = "Ridge regression model to predict diabetes",
                        workspace = ws)
```

## <a name="create-a-docker-image"></a>Vytvoření image Dockeru

Azure Kubernetes Service používá Image Dockeru. Chcete-li vytvořit bitovou kopii, použijte následující kroky:

1. Konfigurace image, musíte vytvořit hodnoticího skriptu a souboru prostředí. Příklad vytvoření souboru skriptu a prostředí najdete v následujících částech příklad klasifikace obrázků:

    * [Vytváření hodnoticí skript (score.py)](tutorial-deploy-models-with-aml.md#create-scoring-script)

    * [Vytvoření souboru prostředí (myenv.yml)](tutorial-deploy-models-with-aml.md#create-environment-file) 

   Tyto soubory konfigurace image v následujícím příkladu:

    ```python
    from azureml.core.image import ContainerImage

    # Image configuration
    image_config = ContainerImage.image_configuration(execution_script = "score.py",
                                                        runtime = "python",
                                                        conda_file = "myenv.yml",
                                                        description = "Image with ridge regression model",
                                                        tags = {"data": "diabetes", "type": "regression"}
                                                        )
    ```

1. Chcete-li vytvořit bitovou kopii, použijte konfiguraci modelu a obrazu. Tato operace může trvat přibližně 5 minut na dokončení:

    ```python
    image = ContainerImage.create(name = "myimage1",
                                    # this is the model object
                                    models = [model],
                                    image_config = image_config,
                                    workspace = ws)

    # Wait for the create process to complete
    image.wait_for_creation(show_output = True)
    ```

## <a name="create-the-aks-cluster"></a>Vytvoření clusteru AKS

Následující fragment kódu ukazuje, jak se vytvoření clusteru AKS. Tento proces trvá přibližně 20 minut na dokončení:

> [!IMPORTANT]
> Vytvoření clusteru AKS je čas procesu pro váš pracovní prostor. Po vytvoření můžete znovu použít tento cluster pro více nasazení. Při odstranění clusteru nebo skupinu prostředků, který jej obsahuje, pak musíte vytvořit nový cluster, které se budete muset nasadit.


```python
from azureml.core.compute import AksCompute, ComputeTarget

# Use the default configuration (you can also provide parameters to customize this)
prov_config = AksCompute.provisioning_configuration()

aks_name = 'aml-aks-1' 
# Create the cluster
aks_target = ComputeTarget.create(workspace = ws, 
                                    name = aks_name, 
                                    provisioning_configuration = prov_config)

# Wait for the create process to complete
aks_target.wait_for_completion(show_output = True)
print(aks_target.provisioning_state)
print(aks_target.provisioning_errors)
```

### <a name="attach-existing-aks-cluster-optional"></a>Připojit existující cluster AKS (volitelné)

Pokud máte existující cluster AKS ve vašem předplatném Azure, můžete ji Pokud chcete nasadit svou image. Následující fragment kódu ukazuje, jak připojit cluster do pracovního prostoru. 

> [!IMPORTANT]
> Je podporována pouze verze 1.11.2 AKS.

```python
# Get the resource id from https://porta..azure.com -> Find your resource group -> click on the Kubernetes service -> Properties
resource_id = '/subscriptions/<your subscription id>/resourcegroups/<your resource group>/providers/Microsoft.ContainerService/managedClusters/<your aks service name>'

# Set to the name of the cluster
cluster_name='my-existing-aks' 

# Attatch the cluster to your workgroup
aks_target = AksCompute.attach(workspace=ws, name=cluster_name, resource_id=resource_id)

# Wait for the operation to complete
aks_target.wait_for_provisioning(True)
```

## <a name="deploy-your-web-service"></a>Nasazení webové služby

Následující fragment kódu ukazuje, jak nasadit bitovou kopii do clusteru AKS:

```python
from azureml.core.webservice import Webservice, AksWebservice

# Set configuration and service name
aks_config = AksWebservice.deploy_configuration()
aks_service_name ='aks-service-1'
# Deploy from image
aks_service = Webservice.deploy_from_image(workspace = ws, 
                                            name = aks_service_name,
                                            image = image,
                                            deployment_config = aks_config,
                                            deployment_target = aks_target)
# Wait for the deployment to complete
aks_service.wait_for_deployment(show_output = True)
print(aks_service.state)
```

> [!TIP]
> Pokud nejsou chyby během nasazení, použijte `aks_service.get_logs()` k zobrazení protokolů služby AKS. Zaznamenané informace může ukazovat na příčinu chyby.

## <a name="test-the-web-service"></a>Test webové služby

Použití `aks_service.run()` k otestování webové služby. Následující fragment kódu ukazuje, jak předat data do služby a zobrazí do predikce.:

```python
import json

test_sample = json.dumps({'data': [
    [1,2,3,4,5,6,7,8,9,10], 
    [10,9,8,7,6,5,4,3,2,1]
]})
test_sample = bytes(test_sample,encoding = 'utf8')

prediction = aks_service.run(input_data = test_sample)
print(prediction)
```

## <a name="cleanup"></a>Vyčištění

Pokud chcete odstranit službu, image a model, použijte následující fragment kódu:

```python
aks_service.delete()
image.delete()
model.delete()
```
