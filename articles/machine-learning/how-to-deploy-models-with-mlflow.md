---
title: Nasazení experimentů ML pomocí MLflow
titleSuffix: Azure Machine Learning
description: Nastavte MLflow s Azure Machine Learning pro nasazení modelů ML jako webové služby.
services: machine-learning
author: shivp950
ms.author: shipatel
ms.service: machine-learning
ms.subservice: core
ms.reviewer: nibaccam
ms.date: 12/23/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python
ms.openlocfilehash: b905b050752e2a6b7acd11e82420c0b0203dfcd1
ms.sourcegitcommit: 6d6030de2d776f3d5fb89f68aaead148c05837e2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/05/2021
ms.locfileid: "97882190"
---
# <a name="deploy-mlflow-models-with-azure-machine-learning-preview"></a>Nasazení modelů MLflow pomocí Azure Machine Learning (Preview)

V tomto článku se dozvíte, jak nasadit model [MLflow](https://www.mlflow.org) jako webovou službu Azure Machine Learning, abyste mohli využívat a používat Azure Machine Learning správy modelů a možností detekce přenosů dat v produkčních modelech.

Azure Machine Learning nabízí konfigurace nasazení pro:
* Azure Container instance (ACI), což je vhodná volba pro nasazení s rychlým vývojem a testováním.
* Služba Azure Kubernetes (AKS), která se doporučuje pro škálovatelná produkční nasazení.

MLflow je open source knihovna pro správu životního cyklu experimentů ve strojovém učení. Jeho integrace s Azure Machine Learning umožňuje tuto správu nad rámec fáze přípravy modelu nasadit do fáze nasazení v produkčním modelu.

>[!NOTE]
> Jako open source knihovna se MLflow změny často. Funkce, které jsou dostupné prostřednictvím Azure Machine Learning a integrace MLflow, by se měly považovat za verzi Preview, a není plně podporovaná Microsoftem.

Následující diagram znázorňuje, že s rozhraním API a Azure Machine Learning nasazení MLflow můžete nasadit modely vytvořené pomocí oblíbených rozhraní, jako je PyTorch, Tensorflow, scikit-učení atd., jako Azure Machine Learning webové služby a spravovat je ve svém pracovním prostoru. 

![ nasazení modelů mlflow pomocí Azure Machine Learningu](./media/how-to-use-mlflow/mlflow-diagram-deploy.png)

> [!TIP]
> Informace v tomto dokumentu jsou primárně určené pro odborníky přes data a vývojáře, kteří chtějí nasadit svůj MLflow model na koncový bod webové služby Azure Machine Learning. Pokud jste správcem a chcete monitorovat využití prostředků a události z Azure Machine Learning, jako jsou kvóty, dokončené školicí běhy nebo dokončená nasazení modelu, přečtěte si téma [monitorování Azure Machine Learning](monitor-azure-machine-learning.md).

## <a name="prerequisites"></a>Požadavky

* Model strojového učení. Pokud nemáte školený model, Najděte si příklad poznámkového bloku, který nejlépe vyhovuje vašemu výpočetnímu scénáři v [tomto úložišti](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/ml-frameworks/using-mlflow) , a postupujte podle pokynů. 
* [Pro připojení Azure Machine Learning nastavte identifikátor URI pro sledování MLflow](how-to-use-mlflow.md#track-local-runs).
* Nainstalujte balíček `azureml-mlflow`. 
    * Tento balíček automaticky přinese `azureml-core` [sadu SDK Azure Machine Learning Pythonu](/python/api/overview/azure/ml/install?preserve-view=true&view=azure-ml-py), která poskytuje možnosti připojení pro MLflow k vašemu pracovnímu prostoru.
* Podívejte se, která [přístupová oprávnění potřebujete k provádění operací MLflow s vaším pracovním prostorem](how-to-assign-roles.md#mlflow-operations). 

## <a name="deploy-to-aci"></a>Nasazení do ACI

Pro nasazení modelu MLflow do webové služby Azure Machine Learning musí být váš model nastaven s [identifikátorem URI sledování MLflow pro připojení k Azure Machine Learning](how-to-use-mlflow.md). 

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

Pak model zaregistrujte a nasaďte v jednom kroku s metodou [nasazení](https://www.mlflow.org/docs/latest/python_api/mlflow.azureml.html#mlflow.azureml.deploy) MLflow pro Azure Machine Learning. 

```python
(webservice,model) = mlflow.azureml.deploy( model_uri='runs:/{}/{}'.format(run.id, model_path),
                      workspace=ws,
                      model_name='sklearn-model', 
                      service_name='diabetes-model-1', 
                      deployment_config=aci_config, 
                      tags=None, mlflow_home=None, synchronous=True)

webservice.wait_for_deployment(show_output=True)
```

## <a name="deploy-to-aks"></a>Nasazení do AKS

Pro nasazení modelu MLflow do webové služby Azure Machine Learning musí být váš model nastaven s [identifikátorem URI sledování MLflow pro připojení k Azure Machine Learning](how-to-use-mlflow.md). 

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

Pak model zaregistrujte a nasaďte v jednom kroku s metodou [nasazení](https://www.mlflow.org/docs/latest/python_api/mlflow.azureml.html#mlflow.azureml.deploy) MLflow pro Azure Machine Learning. 

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

Pokud neplánujete použít nasazenou webovou službu, použijte `service.delete()` ji k odstranění z poznámkového bloku.  Další informace najdete v dokumentaci k [WebService. Delete ()](/python/api/azureml-core/azureml.core.webservice%28class%29?preserve-view=true&view=azure-ml-py#&preserve-view=truedelete--).

## <a name="example-notebooks"></a>Příklady poznámkových bloků

[MLflow s Azure Machine Learning poznámkovým blokům](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/ml-frameworks/using-mlflow) předvádí a rozbalí v konceptech uvedených v tomto článku.

> [!NOTE]
> Úložiště příkladů založené na komunitě s použitím mlflow najdete na adrese https://github.com/Azure/azureml-examples .

## <a name="next-steps"></a>Další kroky

* [Spravujte své modely](concept-model-management-and-deployment.md).
* Monitorujte v produkčních modelech [přenos dat](./how-to-enable-data-collection.md).
* [Sledování Azure Databricks běží s MLflow](how-to-use-mlflow-azure-databricks.md).

