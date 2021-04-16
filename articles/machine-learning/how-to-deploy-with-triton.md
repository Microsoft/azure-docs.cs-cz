---
title: Model s vysokým výkonem obsluhující s Triton (Preview)
titleSuffix: Azure Machine Learning
description: Naučte se nasadit model pomocí serveru NVIDIA Triton odvozování v Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: gopalv
author: gvashishtha
ms.date: 02/16/2020
ms.topic: conceptual
ms.reviewer: larryfr
ms.custom: deploy, devx-track-azurecli
ms.openlocfilehash: 8775696a35bfccc363aa2c6ec06c6c44115916b9
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/14/2021
ms.locfileid: "107479266"
---
# <a name="high-performance-serving-with-triton-inference-server-preview"></a>Vysoce výkonná obsluha s odvozeným serverem Triton (Preview) 

Naučte se používat [Triton odvozený Server NVIDIA](https://aka.ms/nvidia-triton-docs) ke zlepšení výkonu webové služby, která se používá pro odvození modelu.

Jedním z způsobů, jak nasadit model pro odvození, je jako webová služba. Například nasazení do služby Azure Kubernetes nebo Azure Container Instances. Ve výchozím nastavení používá Azure Machine Learning k nasazení webové služby jedinou vláknovou webovou architekturu pro *obecné účely* .

Triton je rozhraní *optimalizované pro odvození*. Poskytuje lepší využití GPU a nabízí cenově výhodné odvození. Na straně serveru dávkuje příchozí požadavky a odesílá tyto dávky pro odvození. Dávkování lépe využívá prostředky GPU a je klíčovou součástí výkonu Triton.

> [!IMPORTANT]
> Použití Triton pro nasazení z Azure Machine Learning je aktuálně ve __verzi Preview__. Na funkci verze Preview nemusí vztahovat zákaznická podpora. Další informace najdete v tématu s [doplňkovými podmínkami použití pro Microsoft Azure Preview.](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)

> [!TIP]
> Fragmenty kódu v tomto dokumentu jsou pro ilustrativní účely a nemusí zobrazovat kompletní řešení. Pro pracovní příklad kódu si přečtěte [kompletní ukázky Triton v tématu Azure Machine Learning](https://aka.ms/triton-aml-sample).

> [!NOTE]
> [Triton odvozeného serveru NVIDIA](https://aka.ms/nvidia-triton-docs) je open source software třetí strany, který je integrovaný v Azure Machine Learning.

## <a name="prerequisites"></a>Požadavky

* **Předplatné Azure** Pokud ho nemáte, vyzkoušejte [bezplatnou nebo placená verzi Azure Machine Learning](https://aka.ms/AMLFree).
* Znalost [toho, jak a kde nasadit model](how-to-deploy-and-where.md) s Azure Machine Learning.
* [Sada SDK Azure Machine Learning pro Python](/python/api/overview/azure/ml/) **nebo** rozšíření [Azure CLI](/cli/azure/) a [Machine Learning](reference-azure-machine-learning-cli.md).
* Pracovní instalace Docker pro místní testování. Informace o instalaci a ověření Docker najdete v dokumentaci k Docker [v dokumentaci k instalaci.](https://docs.docker.com/get-started/)

## <a name="architectural-overview"></a>Přehled architektury

Než se pokusíte použít Triton pro svůj vlastní model, je důležité pochopit, jak funguje s Azure Machine Learning a jak porovnává s výchozím nasazením.

**Výchozí nasazení bez Triton**

* Více pracovních procesů [Gunicorn](https://gunicorn.org/) se spouští souběžně se zpracováním příchozích požadavků.
* Tito pracovníci zpracovávají předběžné zpracování, volání modelu a následné zpracování. 
* Klienti používají __identifikátor URI pro vyhodnocování Azure ml__. Například, `https://myservice.azureml.net/score`.

:::image type="content" source="./media/how-to-deploy-with-triton/normal-deploy.png" alt-text="Diagram architektury pro nasazení Normal, non-Triton,":::

**Přímé nasazení pomocí Triton**

* Požadavky směřují přímo na server Triton.
* Triton zpracovává požadavky v dávkách k maximalizaci využití GPU.
* Klient používá k vytvoření požadavků __identifikátor URI Triton__ . Například, `https://myservice.azureml.net/v2/models/${MODEL_NAME}/versions/${MODEL_VERSION}/infer`.

:::image type="content" source="./media/how-to-deploy-with-triton/triton-deploy.png" alt-text="Nasazení Inferenceconfig jenom s Triton a bez middlewaru Pythonu":::

**Odvození nasazení konfigurace pomocí Triton**

* Více pracovních procesů [Gunicorn](https://gunicorn.org/) se spouští souběžně se zpracováním příchozích požadavků.
* Žádosti jsou předávány na **Server Triton**. 
* Triton zpracovává požadavky v dávkách k maximalizaci využití GPU.
* Klient pro vytváření požadavků používá __identifikátor URI pro vyhodnocování Azure ml__ . Například, `https://myservice.azureml.net/score`.

:::image type="content" source="./media/how-to-deploy-with-triton/inference-config-deploy.png" alt-text="Nasazení pomocí middlewaru Triton a Pythonu":::

Pracovní postup pro použití Triton pro nasazení modelu:

1. Slouží k přímému využití modelu pomocí Triton.
1. Ověřte, že můžete odesílat požadavky do modelu nasazeného v Triton.
1. Volitelné Vytvoření vrstvy middlewaru pro Python na straně serveru před a po zpracování

## <a name="deploying-triton-without-python-pre--and-post-processing"></a>Nasazení Triton bez předběžného a následného zpracování Pythonu

Nejprve postupujte podle následujících kroků a ověřte, zda může Triton odvozený Server sloužit vašemu modelu.

### <a name="optional-define-a-model-config-file"></a>Volitelné Definování konfiguračního souboru modelu

Konfigurační soubor modelu oznamuje, kolik vstupů očekává a jaké dimenze budou mít tyto vstupy. Další informace o vytvoření konfiguračního souboru najdete v dokumentaci ke NVIDIA v tématu [Konfigurace modelu](https://aka.ms/nvidia-triton-docs) .

> [!TIP]
> Používáme `--strict-model-config=false` možnost při spouštění Triton odvozeného serveru, což znamená, že nemusíte zadávat `config.pbtxt` soubor pro modely ONNX nebo TensorFlow.
> 
> Další informace o této možnosti naleznete v tématu [vygenerovaná konfigurace modelu](https://aka.ms/nvidia-triton-docs) v dokumentaci NVIDIA.

### <a name="use-the-correct-directory-structure"></a>Použití správné adresářové struktury

Při registraci modelu pomocí Azure Machine Learning můžete zaregistrovat jednotlivé soubory nebo adresářové struktury. Aby bylo možné používat Triton, musí být registrace modelu pro adresářovou strukturu, která obsahuje adresář s názvem `triton` . Obecnou strukturou tohoto adresáře je:

```bash
models
    - triton
        - model_1
            - model_version
                - model_file
            - config_file
        - model_2
            ...
```

> [!IMPORTANT]
> Tato adresářová struktura je úložiště modelů Triton a je vyžadováno pro to, aby vaše modely pracovaly s Triton. Další informace najdete v dokumentaci k rozhraní NVIDIA v tématu [úložiště modelu Triton](https://aka.ms/nvidia-triton-docs) .

### <a name="register-your-triton-model"></a>Registrace modelu Triton

# <a name="azure-cli"></a>[Azure CLI](#tab/azcli)

```azurecli-interactive
az ml model register -n my_triton_model -p models --model-framework=Multi
```

Další informace o nástroji `az ml model register` najdete v [referenční dokumentaci](/cli/azure/ext/azure-cli-ml/ml/model).

Při registraci modelu v Azure Machine Learning hodnota `--model-path  -p` parametru musí být název nadřazené složky Triton.  
V předchozím příkladu je to  `--model-path` ' modely '.

Hodnota `--name  -n` parametru, â € ̃ my_triton_modelâ €™ v příkladu, bude názvem modelu, který je známý jako pracovní prostor Azure Machine Learning. 

# <a name="python"></a>[Python](#tab/python)


```python

from azureml.core.model import Model

model_path = "models"

model = Model.register(
    model_path=model_path,
    model_name="bidaf-9-tutorial",
    tags={"area": "Natural language processing", "type": "Question-answering"},
    description="Question answering from ONNX model zoo",
    workspace=ws,
    model_framework=Model.Framework.MULTI,  # This line tells us you are registering a Triton model
)

```
Další informace naleznete v dokumentaci k [třídě modelu](/python/api/azureml-core/azureml.core.model.model).

---

### <a name="deploy-your-model"></a>Nasazení modelu

# <a name="azure-cli"></a>[Azure CLI](#tab/azcli)

Pokud máte pomocí Azure Machine Learning vytvořený cluster služby Azure Kubernetes s podporou GPU s názvem AKS-GPU, můžete model nasadit pomocí následujícího příkazu.

```azurecli
az ml model deploy -n triton-webservice -m triton_model:1 --dc deploymentconfig.json --compute-target aks-gpu
```

# <a name="python"></a>[Python](#tab/python)

```python
from azureml.core.webservice import AksWebservice
from azureml.core.model import InferenceConfig
from random import randint

service_name = "triton-webservice"

config = AksWebservice.deploy_configuration(
    compute_target_name="aks-gpu",
    gpu_cores=1,
    cpu_cores=1,
    memory_gb=4,
    auth_enabled=True,
)

service = Model.deploy(
    workspace=ws,
    name=service_name,
    models=[model],
    deployment_config=config,
    overwrite=True,
)
```
---

[Další informace o nasazování modelů najdete v této dokumentaci](how-to-deploy-and-where.md).

### <a name="call-into-your-deployed-model"></a>Volání do nasazeného modelu

Nejdřív získejte identifikátor URI a nosiče s hodnocením.

# <a name="azure-cli"></a>[Azure CLI](#tab/azcli)


```azurecli
az ml service show --name=triton-webservice
```
# <a name="python"></a>[Python](#tab/python)

```python
import requests

print(service.scoring_uri)
print(service.get_keys())

```

---

Pak zajistěte, aby služba běžela tímto způsobem: 

```{bash}
!curl -v $scoring_uri/v2/health/ready -H 'Authorization: Bearer '"$service_key"''
```

Tento příkaz vrátí informace podobné následujícímu. Poznámka: `200 OK` Tento stav znamená, že je webový server spuštěný.

```{bash}
*   Trying 127.0.0.1:8000...
* Connected to localhost (127.0.0.1) port 8000 (#0)
> GET /v2/health/ready HTTP/1.1
> Host: localhost:8000
> User-Agent: curl/7.71.1
> Accept: */*
>
* Mark bundle as not supporting multiuse
< HTTP/1.1 200 OK
HTTP/1.1 200 OK
```

Jakmile provedete kontrolu stavu, můžete vytvořit klienta, který bude odesílat data do Triton pro odvození. Další informace o vytváření klienta najdete v [příkladech klienta](https://aka.ms/nvidia-client-examples) v dokumentaci k NVIDIA. K dispozici jsou také [ukázky Pythonu na GitHubu Triton](https://aka.ms/nvidia-triton-docs).

Pokud nechcete do nasazené služby webwebservice přidat předem a následné zpracování Pythonu, můžete to udělat. Pokud chcete přidat tuto logiku předem a po zpracování, přečtěte si.

## <a name="optional-re-deploy-with-a-python-entry-script-for-pre--and-post-processing"></a>Volitelné Opětovné nasazení pomocí vstupního skriptu Pythonu pro předběžné a následné zpracování

Po ověření, že je Triton schopen zajišťovat váš model, můžete přidat kód před a po zpracování definováním _skriptu pro vložení_. Tento soubor má název `score.py` . Další informace o vstupních skriptech najdete v tématu [Definování skriptu pro zadání](how-to-deploy-and-where.md#define-an-entry-script).

Dva hlavní kroky jsou k inicializaci klienta Triton HTTP v `init()` metodě a volání do tohoto klienta ve vaší `run()` funkci.

### <a name="initialize-the-triton-client"></a>Inicializace klienta Triton

Do souboru přidejte kód podobný následujícímu příkladu `score.py` . Triton v Azure Machine Learning očekává, že se bude řešit na localhost, na portu 8000. V tomto případě je localhost v imagi Docker pro toto nasazení, nikoli port na vašem místním počítači:

> [!TIP]
> `tritonhttpclient`Balíček PIP je zahrnutý ve spravovaném `AzureML-Triton` prostředí, takže ho není potřeba zadávat jako závislost PIP.

```python
import tritonhttpclient

def init():
    global triton_client
    triton_client = tritonhttpclient.InferenceServerClient(url="localhost:8000")
```

### <a name="modify-your-scoring-script-to-call-into-triton"></a>Změna skriptu bodování pro volání do Triton

Následující příklad ukazuje, jak dynamicky žádat metadata pro model:

> [!TIP]
> Můžete dynamicky požadovat metadata modelů, které byly načteny pomocí Triton pomocí `.get_model_metadata` metody klienta Triton. Příklad jeho použití najdete v [ukázkovém poznámkovém bloku](https://aka.ms/triton-aml-sample) .

```python
input = tritonhttpclient.InferInput(input_name, data.shape, datatype)
input.set_data_from_numpy(data, binary_data=binary_data)

output = tritonhttpclient.InferRequestedOutput(
         output_name, binary_data=binary_data, class_count=class_count)

# Run inference
res = triton_client.infer(model_name,
                          [input]
                          request_id='0',
                          outputs=[output])

```

<a id="redeploy"></a>

### <a name="redeploy-with-an-inference-configuration"></a>Znovu nasadit s použitím konfigurace odvození

Odvozená konfigurace umožňuje používat vstupní skript i proces nasazení Azure Machine Learning pomocí sady Python SDK nebo rozhraní příkazového řádku Azure CLI.

> [!IMPORTANT]
> Je nutné zadat toto `AzureML-Triton` [prostředí](./resource-curated-environments.md).
>
> Příklad kódu Pythonu se klonuje `AzureML-Triton` do jiného prostředí s názvem `My-Triton` . I toto prostředí používá kód Azure CLI. Další informace o klonování prostředí naleznete v tématu [prostředí. Clone ()](/python/api/azureml-core/azureml.core.environment.environment#clone-new-name-) reference.

# <a name="azure-cli"></a>[Azure CLI](#tab/azcli)

> [!TIP]
> Další informace o vytvoření konfigurace odvození naleznete v tématu [schéma konfigurace odvození](./reference-azure-machine-learning-cli.md#inference-configuration-schema).

```azurecli
az ml model deploy -n triton-densenet-onnx \
-m densenet_onnx:1 \
--ic inference-config.json \
-e My-Triton --dc deploymentconfig.json \
--overwrite --compute-target=aks-gpu
```

# <a name="python"></a>[Python](#tab/python)

```python
from azureml.core.webservice import LocalWebservice
from azureml.core import Environment
from azureml.core.model import InferenceConfig


local_service_name = "triton-bidaf-onnx"
env = Environment.get(ws, "AzureML-Triton").clone("My-Triton")

for pip_package in ["nltk"]:
    env.python.conda_dependencies.add_pip_package(pip_package)

inference_config = InferenceConfig(
    entry_script="score_bidaf.py",  # This entry script is where we dispatch a call to the Triton server
    source_directory=os.path.join("..", "scripts"),
    environment=env
)

local_config = LocalWebservice.deploy_configuration(
    port=6789
)

local_service = Model.deploy(
    workspace=ws,
    name=local_service_name,
    models=[model],
    inference_config=inference_config,
    deployment_config=local_config,
    overwrite=True)

local_service.wait_for_deployment(show_output = True)
print(local_service.state)
# Print the URI you can use to call the local deployment
print(local_service.scoring_uri)
```

---

Po dokončení nasazení se zobrazí identifikátor URI pro vyhodnocování. Pro toto místní nasazení bude `http://localhost:6789/score` . Pokud nasadíte do cloudu, můžete pomocí příkazu [AZ ml Service show](/cli/azure/ext/azure-cli-ml/ml/service#ext_azure_cli_ml_az_ml_service_show) CLI získat identifikátor URI pro vyhodnocování.

Informace o tom, jak vytvořit klienta, který odesílá požadavky na odvození do identifikátoru URI pro vyhodnocování, najdete v tématu [Spotřeba modelu nasazeného jako webové služby](how-to-consume-web-service.md).

### <a name="setting-the-number-of-workers"></a>Nastavení počtu pracovních procesů

Chcete-li nastavit počet pracovních procesů v nasazení, nastavte proměnnou prostředí `WORKER_COUNT` . Vzhledem k tomu, že máte objekt [prostředí](/python/api/azureml-core/azureml.core.environment.environment) s názvem `env` , můžete provést následující akce:

```{py}
env.environment_variables["WORKER_COUNT"] = "1"
```

Díky tomu bude Azure ML informovat o tom, kolik pracovníků zadáte.


## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud plánujete pokračování v používání pracovního prostoru Azure Machine Learning, ale chcete se zbavit nasazené služby, použijte jednu z následujících možností:


# <a name="azure-cli"></a>[Azure CLI](#tab/azcli)

```azurecli
az ml service delete -n triton-densenet-onnx
```
# <a name="python"></a>[Python](#tab/python)

```python
local_service.delete()
```


---
## <a name="troubleshoot"></a>Řešení potíží

* [Řešení potíží s neúspěšným nasazením](how-to-troubleshoot-deployment.md), zjistěte, jak řešit a řešit běžné chyby, ke kterým může dojít při nasazování modelu.

* Pokud se v protokolech nasazení ukáže, že **se TritonServer nepovedlo spustit**, přečtěte si [dokumentaci k open source v nVidiaâ™ s.](https://github.com/triton-inference-server/server)

## <a name="next-steps"></a>Další kroky

* [Viz kompletní ukázky Triton v tématu Azure Machine Learning](https://aka.ms/aml-triton-sample)
* Podívejte se na [Příklady klienta Triton](https://aka.ms/nvidia-client-examples)
* Přečtěte si [dokumentaci k Triton serveru pro odvození](https://aka.ms/nvidia-triton-docs) .
* [Nasazení do Azure Kubernetes Service](how-to-deploy-azure-kubernetes-service.md)
* [Aktualizace webové služby](how-to-deploy-update-web-service.md)
* [Shromažďování dat pro modely v produkčním prostředí](how-to-enable-data-collection.md)
