---
title: Řešení potíží s nasazením místního modelu
titleSuffix: Azure Machine Learning
description: Zkuste místní nasazení modelu jako první krok při řešení chyb nasazení modelu.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
author: gvashishtha
ms.author: gopalv
ms.reviewer: luquinta
ms.date: 11/25/2020
ms.topic: troubleshooting
ms.custom: devx-track-python, deploy, contperf-fy21q2
ms.openlocfilehash: 69ac47296cb4624de6cdf05ddb3e72973751f631
ms.sourcegitcommit: 956dec4650e551bdede45d96507c95ecd7a01ec9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/09/2021
ms.locfileid: "102519618"
---
# <a name="troubleshooting-with-a-local-model-deployment"></a>Řešení potíží s nasazením místního modelu

Zkuste místní nasazení modelu jako první krok při řešení potíží s nasazením do Azure Container Instances (ACI) nebo Azure Kubernetes Service (AKS).  Použití místní webové služby usnadňuje vystavování a opravy běžných chyb nasazení webové služby Azure Machine Learning Docker.

## <a name="prerequisites"></a>Požadavky

* **Předplatné Azure** Vyzkoušení [bezplatné nebo placené verze Azure Machine Learning](https://aka.ms/AMLFree).
* Option A (**doporučeno**) – místní ladění na Azure Machine Learning výpočetní instanci
   * Pracovní prostor Azure Machine Learning s [výpočetní instancí](how-to-deploy-local-container-notebook-vm.md) spuštěnou
* Možnost B – ladění lokálně na výpočetní úrovni
   * [Sada Azure Machine Learning SDK](/python/api/overview/azure/ml/install).
   * Rozhraní příkazového [řádku Azure](/cli/azure/install-azure-cli)
   * [Rozšíření CLI pro Azure Machine Learning](reference-azure-machine-learning-cli.md).
   * Mít v místním systému fungující instalaci Docker. 
   * K ověření instalace Docker použijte příkaz `docker run hello-world` z terminálu nebo příkazového řádku. Informace o instalaci Docker nebo odstraňování potíží s chybami Docker najdete v [dokumentaci k Docker](https://docs.docker.com/).

## <a name="debug-locally"></a>Místní ladění

Pokud chcete prozkoumat příklad spustitelný, najdete v úložišti [MachineLearningNotebooks](https://github.com/Azure/MachineLearningNotebooks) ukázkový [Poznámkový blok místního nasazení](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/deploy-to-local/register-model-deploy-local.ipynb) .

> [!WARNING]
> Nasazení místních webových služeb se v produkčních scénářích nepodporují.

Chcete-li nasadit místně, upravte kód tak, aby se použil `LocalWebservice.deploy_configuration()` k vytvoření konfigurace nasazení. Pak použijte `Model.deploy()` k nasazení služby. Následující příklad nasadí model (obsažený v proměnné modelu) jako místní webovou službu:

```python
from azureml.core.environment import Environment
from azureml.core.model import InferenceConfig, Model
from azureml.core.webservice import LocalWebservice


# Create inference configuration based on the environment definition and the entry script
myenv = Environment.from_conda_specification(name="env", file_path="myenv.yml")
inference_config = InferenceConfig(entry_script="score.py", environment=myenv)
# Create a local deployment, using port 8890 for the web service endpoint
deployment_config = LocalWebservice.deploy_configuration(port=8890)
# Deploy the service
service = Model.deploy(
    ws, "mymodel", [model], inference_config, deployment_config)
# Wait for the deployment to complete
service.wait_for_deployment(True)
# Display the port that the web service is available on
print(service.port)
```

Pokud definujete vlastní conda specifikace YAML, Seznamte se s výchozím nastavením verze >= 1.0.45 jako závislostí PIP. Tento balíček je nutný k hostování modelu jako webové služby.

V tuto chvíli můžete pracovat se službou jako normální. Následující kód demonstruje odeslání dat do služby:

```python
import json

test_sample = json.dumps({'data': [
    [1, 2, 3, 4, 5, 6, 7, 8, 9, 10],
    [10, 9, 8, 7, 6, 5, 4, 3, 2, 1]
]})

test_sample = bytes(test_sample, encoding='utf8')

prediction = service.run(input_data=test_sample)
print(prediction)
```

Další informace o přizpůsobení prostředí Pythonu najdete v tématu [vytváření a Správa prostředí pro školení a nasazení](how-to-use-environments.md). 

### <a name="update-the-service"></a>Aktualizace služby

Během místního testování možná budete muset soubor aktualizovat, `score.py` aby se přidalo protokolování nebo pokus o vyřešení všech zjištěných problémů. Chcete-li znovu načíst změny v `score.py` souboru, použijte `reload()` . Například následující kód znovu načte skript pro službu a poté do něj pošle data. Data jsou hodnocena pomocí aktualizovaného `score.py` souboru:

> [!IMPORTANT]
> `reload`Metoda je k dispozici pouze pro místní nasazení. Informace o aktualizaci nasazení na jiný cíl výpočetní služby najdete v tématu [Postup aktualizace vaší webové služby](how-to-deploy-update-web-service.md).

```python
service.reload()
print(service.run(input_data=test_sample))
```

> [!NOTE]
> Skript se znovu načte z umístění zadaného `InferenceConfig` objektem, který služba používá.

Chcete-li změnit model, závislosti conda nebo konfiguraci nasazení, použijte [Update ()](/python/api/azureml-core/azureml.core.webservice%28class%29#update--args-). Následující příklad aktualizuje model používaný službou:

```python
service.update([different_model], inference_config, deployment_config)
```

### <a name="delete-the-service"></a>Odstranit službu

Pokud chcete službu odstranit, použijte [Delete ()](/python/api/azureml-core/azureml.core.webservice%28class%29#delete--).

### <a name="inspect-the-docker-log"></a><a id="dockerlog"></a> Kontrola protokolu Docker

Podrobnou zprávu protokolu modulu Docker můžete vytisknout z objektu služby. Můžete zobrazit protokol pro ACI, AKS a místní nasazení. Následující příklad ukazuje, jak tisknout protokoly.

```python
# if you already have the service object handy
print(service.get_logs())

# if you only know the name of the service (note there might be multiple services with the same name but different version number)
print(ws.webservices['mysvc'].get_logs())
```

Pokud se `Booting worker with pid: <pid>` v protokolech zobrazuje řádek vícekrát, znamená to, že ke spuštění pracovního procesu není dost paměti.
Chybu můžete vyřešit tak, že zvýšíte hodnotu `memory_gb` v `deployment_config`

## <a name="next-steps"></a>Další kroky

Další informace o nasazení:

* [Řešení potíží se vzdáleným nasazením](how-to-troubleshoot-deployment.md)
* [Jak nasadit a kde](how-to-deploy-and-where.md)
* [Kurz: výuka & nasazení modelů](tutorial-train-models-with-aml.md)
* [Jak spustit a ladit experimenty místně](./how-to-debug-visual-studio-code.md)