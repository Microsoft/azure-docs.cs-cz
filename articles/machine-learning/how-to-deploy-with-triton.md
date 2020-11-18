---
title: Model s vysokým výkonem obsluhující s Triton (Preview)
titleSuffix: Azure Machine Learning
description: Naučte se nasadit model pomocí serveru NVIDIA Triton odvozování v Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: gopalv
author: gvashishtha
ms.date: 09/23/2020
ms.topic: conceptual
ms.reviewer: larryfr
ms.custom: deploy, devx-track-azurecli
ms.openlocfilehash: cfa40332c45eac23d062b83bb789c25f1906ae24
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/18/2020
ms.locfileid: "94831629"
---
# <a name="high-performance-serving-with-triton-inference-server-preview"></a>Vysoce výkonná obsluha s odvozeným serverem Triton (Preview) 

Naučte se používat [Triton odvozený Server NVIDIA](https://developer.nvidia.com/nvidia-triton-inference-server) ke zlepšení výkonu webové služby, která se používá pro odvození modelu.

Jedním z způsobů, jak nasadit model pro odvození, je jako webová služba. Například nasazení do služby Azure Kubernetes nebo Azure Container Instances. Ve výchozím nastavení používá Azure Machine Learning k nasazení webové služby jedinou vláknovou webovou architekturu pro *obecné účely* .

Triton je rozhraní *optimalizované pro odvození*. Poskytuje lepší využití GPU a nabízí cenově výhodné odvození. Na straně serveru dávkuje příchozí požadavky a odesílá tyto dávky pro odvození. Dávkování lépe využívá prostředky GPU a je klíčovou součástí výkonu Triton.

> [!IMPORTANT]
> Použití Triton pro nasazení z Azure Machine Learning je aktuálně ve __verzi Preview__. Na funkci verze Preview nemusí vztahovat zákaznická podpora. Další informace najdete v tématu s [doplňkovými podmínkami použití pro Microsoft Azure Preview.](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)

> [!TIP]
> Fragmenty kódu v tomto dokumentu jsou pro ilustrativní účely a nemusí zobrazovat kompletní řešení. Pro pracovní příklad kódu si přečtěte [kompletní ukázky Triton v tématu Azure Machine Learning](https://github.com/Azure/azureml-examples/tree/main/tutorials).

## <a name="prerequisites"></a>Předpoklady

* **Předplatné Azure** Pokud ho nemáte, vyzkoušejte [bezplatnou nebo placená verzi Azure Machine Learning](https://aka.ms/AMLFree).
* Znalost [toho, jak a kde nasadit model](how-to-deploy-and-where.md) s Azure Machine Learning.
* [Sada SDK Azure Machine Learning pro Python](/python/api/overview/azure/ml/?view=azure-ml-py) **nebo** rozšíření [Azure CLI](/cli/azure/?view=azure-cli-latest) a [Machine Learning](reference-azure-machine-learning-cli.md).
* Pracovní instalace Docker pro místní testování. Informace o instalaci a ověření Docker najdete v dokumentaci k Docker [v dokumentaci k instalaci.](https://docs.docker.com/get-started/)

## <a name="architectural-overview"></a>Přehled architektury

Než se pokusíte použít Triton pro svůj vlastní model, je důležité pochopit, jak funguje s Azure Machine Learning a jak porovnává s výchozím nasazením.

**Výchozí nasazení bez Triton**

* Více pracovních procesů [Gunicorn](https://gunicorn.org/) se spouští souběžně se zpracováním příchozích požadavků.
* Tito pracovníci zpracovávají předběžné zpracování, volání modelu a následné zpracování. 
* Požadavky na odvození používají __identifikátor URI pro vyhodnocování__. Například, `https://myserevice.azureml.net/score`.

:::image type="content" source="./media/how-to-deploy-with-triton/normal-deploy.png" alt-text="Diagram architektury pro nasazení Normal, non-Triton,":::

### <a name="setting-the-number-of-workers"></a>Nastavení počtu pracovních procesů

Chcete-li nastavit počet pracovních procesů v nasazení, nastavte proměnnou prostředí `WORKER_COUNT` . Vzhledem k tomu, že máte objekt [prostředí](https://docs.microsoft.compython/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py&preserve-view=true) s názvem `env` , můžete provést následující akce:

```{py}
env.environment_variables["WORKER_COUNT"] = "1"
```

Díky tomu bude Azure ML informovat o tom, kolik pracovníků zadáte.


**Odvození nasazení konfigurace pomocí Triton**

* Více pracovních procesů [Gunicorn](https://gunicorn.org/) se spouští souběžně se zpracováním příchozích požadavků.
* Žádosti jsou předávány na **Server Triton**. 
* Triton zpracovává požadavky v dávkách k maximalizaci využití GPU.
* Klient používá k vytvoření požadavků __identifikátor URI pro vyhodnocování__ . Například, `https://myserevice.azureml.net/score`.

:::image type="content" source="./media/how-to-deploy-with-triton/inferenceconfig-deploy.png" alt-text="Nasazení Inferenceconfig s Triton":::

Pracovní postup pro použití Triton pro nasazení modelu:

1. Ověřte, že Triton může sloužit vašemu modelu.
1. Ověřte, že můžete odesílat požadavky do modelu nasazeného v Triton.
1. Zahrňte svůj kód specifický pro Triton do nasazení AML.

## <a name="verify-that-triton-can-serve-your-model"></a>Ověřte, že Triton může sloužit vašemu modelu.

Nejprve postupujte podle následujících kroků a ověřte, zda může Triton odvozený Server sloužit vašemu modelu.

### <a name="optional-define-a-model-config-file"></a>Volitelné Definování konfiguračního souboru modelu

Konfigurační soubor modelu oznamuje, kolik vstupů očekává a jaké dimenze budou mít tyto vstupy. Další informace o vytvoření konfiguračního souboru najdete v dokumentaci ke NVIDIA v tématu [Konfigurace modelu](https://docs.nvidia.com/deeplearning/triton-inference-server/user-guide/docs/model_configuration.html) .

> [!TIP]
> Používáme `--strict-model-config=false` možnost při spouštění Triton odvozeného serveru, což znamená, že nemusíte zadávat `config.pbtxt` soubor pro modely ONNX nebo TensorFlow.
> 
> Další informace o této možnosti naleznete v tématu [vygenerovaná konfigurace modelu](https://docs.nvidia.com/deeplearning/triton-inference-server/user-guide/docs/model_configuration.html#generated-model-configuration) v dokumentaci NVIDIA.

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
> Tato adresářová struktura je úložiště modelů Triton a je vyžadováno pro to, aby vaše modely pracovaly s Triton. Další informace najdete v dokumentaci k rozhraní NVIDIA v tématu [úložiště modelu Triton](https://docs.nvidia.com/deeplearning/triton-inference-server/user-guide/docs/model_repository.html) .

### <a name="test-with-triton-and-docker"></a>Testování pomocí Triton a Docker

K otestování modelu, abyste se ujistili, že běží s Triton, můžete použít Docker. Následující příkazy stáhnou kontejner Triton do místního počítače a pak spustí Triton Server:

1. Pokud chcete načíst image pro server Triton do místního počítače, použijte následující příkaz:

    ```bash
    docker pull nvcr.io/nvidia/tritonserver:20.09-py3
    ```

1. K spuštění serveru Triton použijte následující příkaz. Nahraďte `<path-to-models/triton>` cestou k úložišti Triton modelů, které obsahuje vaše modely:

    ```bash
    docker run --rm -ti -v<path-to-models/triton>:/models nvcr.io/nvidia/tritonserver:20.09-py3 tritonserver --model-repository=/models --strict-model-config=false
    ```

    > [!IMPORTANT]
    > Pokud používáte systém Windows, může se při prvním spuštění příkazu zobrazit výzva k povolení síťového připojení k tomuto procesu. Pokud ano, vyberte, pokud chcete povolit přístup.

    Po spuštění se do příkazového řádku zaprotokolují informace podobné následujícímu textu:

    ```bash
    I0923 19:21:30.582866 1 http_server.cc:2705] Started HTTPService at 0.0.0.0:8000
    I0923 19:21:30.626081 1 http_server.cc:2724] Started Metrics Service at 0.0.0.0:8002
    ```

    První řádek označuje adresu webové služby. V tomto případě `0.0.0.0:8000` , který je stejný jako `localhost:8000` .

1. Pro přístup ke koncovému bodu stavu použijte nástroj, jako je například kudrlinkou.

    ```bash
    curl -L -v -i localhost:8000/v2/health/ready
    ```

    Tento příkaz vrátí informace podobné následujícímu. Poznámka: `200 OK` Tento stav znamená, že je webový server spuštěný.

    ```bash
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

Kromě základní kontroly stavu můžete vytvořit klienta, který bude odesílat data do Triton pro odvození. Další informace o vytváření klienta najdete v [příkladech klienta](https://docs.nvidia.com/deeplearning/triton-inference-server/user-guide/docs/client_example.html) v dokumentaci k NVIDIA. K dispozici jsou také [ukázky Pythonu na GitHubu Triton](https://github.com/triton-inference-server/server/tree/master/src/clients/python/examples).

Další informace o spouštění Triton pomocí Docker najdete v tématu [spuštění Triton v systému s grafickým procesorem](https://docs.nvidia.com/deeplearning/triton-inference-server/user-guide/docs/run.html#running-triton-on-a-system-with-a-gpu) a [spuštění Triton v systému bez GPU](https://docs.nvidia.com/deeplearning/triton-inference-server/user-guide/docs/run.html#running-triton-on-a-system-without-a-gpu).

### <a name="register-your-model"></a>Registrace modelu

Teď, když jste ověřili, že váš model spolupracuje s Triton, zaregistrujte ho pomocí Azure Machine Learning. Registrace modelu ukládá vaše soubory modelů v pracovním prostoru Azure Machine Learning a používá se při nasazení pomocí sady Python SDK a Azure CLI.

Následující příklady ukazují, jak registrovat model (y):

# <a name="python"></a>[Python](#tab/python)

```python
from azureml.core.model import Model

model = Model.register(
    model_path=os.path.join("..", "triton"),
    model_name="bidaf_onnx",
    tags={'area': "Natural language processing", 'type': "Question answering"},
    description="Question answering model from ONNX model zoo",
    workspace=ws
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az ml model register --model-path='triton' \
--name='bidaf_onnx' \
--workspace-name='<my_workspace>'
```
---

<a id="processing"></a>

## <a name="verify-you-can-call-into-your-model"></a>Ověření, že můžete volat do svého modelu

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

## <a name="redeploy-with-an-inference-configuration"></a>Znovu nasadit s použitím konfigurace odvození

Odvozená konfigurace umožňuje používat vstupní skript i proces nasazení Azure Machine Learning pomocí sady Python SDK nebo rozhraní příkazového řádku Azure CLI.

> [!IMPORTANT]
> Je nutné zadat toto `AzureML-Triton` [prostředí](./resource-curated-environments.md).
>
> Příklad kódu Pythonu se klonuje `AzureML-Triton` do jiného prostředí s názvem `My-Triton` . I toto prostředí používá kód Azure CLI. Další informace o klonování prostředí naleznete v tématu [prostředí. Clone ()](/python/api/azureml-core/azureml.core.environment.environment?preserve-view=true&view=azure-ml-py#clone-new-name-) reference.

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

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

> [!TIP]
> Další informace o vytvoření konfigurace odvození naleznete v tématu [schéma konfigurace odvození](./reference-azure-machine-learning-cli.md#inference-configuration-schema).

```azurecli
az ml model deploy -n triton-densenet-onnx \
-m densenet_onnx:1 \
--ic inference-config.json \
-e My-Triton --dc deploymentconfig.json \
--overwrite --compute-target=aks-gpu
```

---

Po dokončení nasazení se zobrazí identifikátor URI pro vyhodnocování. Pro toto místní nasazení bude `http://localhost:6789/score` . Pokud nasadíte do cloudu, můžete pomocí příkazu [AZ ml Service show](/cli/azure/ext/azure-cli-ml/ml/service?view=azure-cli-latest#ext_azure_cli_ml_az_ml_service_show) CLI získat identifikátor URI pro vyhodnocování.

Informace o tom, jak vytvořit klienta, který odesílá požadavky na odvození do identifikátoru URI pro vyhodnocování, najdete v tématu [Spotřeba modelu nasazeného jako webové služby](how-to-consume-web-service.md).

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud plánujete pokračování v používání pracovního prostoru Azure Machine Learning, ale chcete se zbavit nasazené služby, použijte jednu z následujících možností:

# <a name="python"></a>[Python](#tab/python)

```python
local_service.delete()
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az ml service delete -n triton-densenet-onnx
```

---

## <a name="next-steps"></a>Další kroky

* [Viz kompletní ukázky Triton v tématu Azure Machine Learning](https://aka.ms/aml-triton-sample)
* Podívejte se na [Příklady klienta Triton](https://github.com/triton-inference-server/server/tree/master/src/clients/python/examples)
* Přečtěte si [dokumentaci k Triton serveru pro odvození](https://docs.nvidia.com/deeplearning/triton-inference-server/user-guide/docs/index.html) .
* [Řešení potíží s neúspěšným nasazením](how-to-troubleshoot-deployment.md)
* [Nasazení do Azure Kubernetes Service](how-to-deploy-azure-kubernetes-service.md)
* [Aktualizace webové služby](how-to-deploy-update-web-service.md)
* [Shromažďování dat pro modely v produkčním prostředí](how-to-enable-data-collection.md)
