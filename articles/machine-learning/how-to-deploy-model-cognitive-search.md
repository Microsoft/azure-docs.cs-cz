---
title: Nasazení modelu pro použití se službou Cognitive Search
titleSuffix: Azure Machine Learning
description: Naučte se používat Azure Machine Learning k nasazení modelu pro použití s Kognitivní hledání. Model se používá jako vlastní dovednost k rozšíření možností vyhledávání.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: cgronlun
author: cjgronlund
ms.reviewer: larryfr
ms.date: 03/11/2021
ms.custom: deploy
ms.openlocfilehash: 22c8880cbcde1f1a55fa66beee0323e0348e1164
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "103149609"
---
# <a name="deploy-a-model-for-use-with-cognitive-search"></a>Nasazení modelu pro použití se službou Cognitive Search


V tomto článku se naučíte, jak pomocí Azure Machine Learning nasadit model pro použití s [Azure kognitivní hledání](../search/search-what-is-azure-search.md).

Kognitivní hledání provádí zpracování obsahu prostřednictvím obsahu heterogenní, aby Queryable lidi a aplikace. Tento proces se dá zvýšit pomocí modelu nasazeného z Azure Machine Learning.

Azure Machine Learning může nasadit školený model jako webovou službu. Webová služba je pak vložena do Kognitivní hledání _dovednosti_, která se stal součástí kanálu zpracování.

> [!IMPORTANT]
> Informace v tomto článku jsou specifické pro nasazení modelu. Poskytuje informace o podporovaných konfiguracích nasazení, které umožňují použití modelu Kognitivní hledání.
>
> Informace o tom, jak nakonfigurovat Kognitivní hledání pro použití nasazeného modelu, najdete v tématu [sestavení a nasazení vlastní dovednosti pomocí Azure Machine Learning](../search/cognitive-search-tutorial-aml-custom-skill.md) kurzu.
>
> Ukázku, na které je kurz založený, najdete v tématu [https://github.com/Azure-Samples/azure-search-python-samples/tree/master/AzureML-Custom-Skill](https://github.com/Azure-Samples/azure-search-python-samples/tree/master/AzureML-Custom-Skill) .

Když nasazujete model pro použití s Azure Kognitivní hledání, nasazení musí splňovat následující požadavky:

* Použijte službu Azure Kubernetes k hostování modelu pro odvození.
* Povolte protokol TLS (Transport Layer Security) pro službu Azure Kubernetes. Protokol TLS slouží k zabezpečení komunikace pomocí protokolu HTTPS mezi Kognitivní hledání a nasazeným modelem.
* Skript vstupu musí použít `inference_schema` balíček k vygenerování schématu openapi (Swagger) pro službu.
* Vstupní skript musí také přijmout data JSON jako vstup a generovat jako výstup JSON.


## <a name="prerequisites"></a>Požadavky

* Pracovní prostor služby Azure Machine Learning. Další informace najdete v tématu [Vytvoření pracovního prostoru Azure Machine Learning](how-to-manage-workspace.md).

* Prostředí pro vývoj v Pythonu s nainstalovanou sadou Azure Machine Learning SDK. Další informace najdete v tématu [Azure Machine Learning SDK](/python/api/overview/azure/ml/install).  

* Registrovaný model. Pokud model nemáte, použijte vzorový Poznámkový blok na adrese [https://github.com/Azure-Samples/azure-search-python-samples/tree/master/AzureML-Custom-Skill](https://github.com/Azure-Samples/azure-search-python-samples/tree/master/AzureML-Custom-Skill) .

* Obecné informace o [tom, jak a kde nasadit modely](how-to-deploy-and-where.md).

## <a name="connect-to-your-workspace"></a>Připojení k pracovnímu prostoru

Pracovní prostor Azure Machine Learning poskytuje centralizované místo pro práci se všemi artefakty, které vytvoříte při použití Azure Machine Learning. Pracovní prostor uchovává historii všech školicích běhů, včetně protokolů, metrik, výstupu a snímků vašich skriptů.

Pokud se chcete připojit k existujícímu pracovnímu prostoru, použijte následující kód:

> [!IMPORTANT]
> Tento fragment kódu očekává uložení konfigurace pracovního prostoru do aktuálního adresáře nebo jeho nadřazeného objektu. Další informace najdete v tématu [Vytvoření a Správa pracovních prostorů Azure Machine Learning](how-to-manage-workspace.md). Další informace o ukládání konfigurace do souboru najdete v tématu [vytvoření konfiguračního souboru pracovního prostoru](how-to-configure-environment.md#workspace).

```python
from azureml.core import Workspace

try:
    # Load the workspace configuration from local cached inffo
    ws = Workspace.from_config()
    print(ws.name, ws.location, ws.resource_group, ws.location, sep='\t')
    print('Library configuration succeeded')
except:
    print('Workspace not found')
```

## <a name="create-a-kubernetes-cluster"></a>Vytvoření clusteru Kubernetes

**Časový odhad**: přibližně 20 minut.

Cluster Kubernetes je sada instancí virtuálních počítačů (nazývaných uzly), které se používají ke spouštění kontejnerových aplikací.

Když nasadíte model z Azure Machine Learning do služby Azure Kubernetes, model a všechny prostředky potřebné k jeho hostování jako webové služby jsou zabaleny do kontejneru Docker. Tento kontejner se pak nasadí do clusteru.

Následující kód ukazuje, jak vytvořit nový cluster Azure Kubernetes Service (AKS) pro váš pracovní prostor:

> [!TIP]
> Existující službu Azure Kubernetes můžete připojit také k pracovnímu prostoru Azure Machine Learning. Další informace najdete v tématu [nasazení modelů do služby Azure Kubernetes](how-to-deploy-azure-kubernetes-service.md).

> [!IMPORTANT]
> Všimněte si, že kód používá `enable_ssl()` metodu k povolení protokolu TLS (Transport Layer Security) pro cluster. To je nutné při plánování použití nasazeného modelu z Cognitive Services.

```python
from azureml.core.compute import AksCompute, ComputeTarget
# Create or attach to an AKS inferencing cluster

# Create the provisioning configuration with defaults
prov_config = AksCompute.provisioning_configuration()

# Enable TLS (sometimes called SSL) communications
# Leaf domain label generates a name using the formula
#  "<leaf-domain-label>######.<azure-region>.cloudapp.azure.com"
#  where "######" is a random series of characters
prov_config.enable_ssl(leaf_domain_label = "contoso")

cluster_name = 'amlskills'
# Try to use an existing compute target by that name.
# If one doesn't exist, create one.
try:
    
    aks_target = ComputeTarget(ws, cluster_name)
    print("Attaching to existing cluster")
except Exception as e:
    print("Creating new cluster")
    aks_target = ComputeTarget.create(workspace = ws, 
                                  name = cluster_name, 
                                  provisioning_configuration = prov_config)
    # Wait for the create process to complete
    aks_target.wait_for_completion(show_output = True)
```

> [!IMPORTANT]
> Azure vám bude účtovat, dokud cluster AKS existuje. Až s tím budete hotovi, nezapomeňte cluster AKS odstranit.

Další informace o použití AKS s Azure Machine Learning najdete v tématu [nasazení do služby Azure Kubernetes](how-to-deploy-azure-kubernetes-service.md).

## <a name="write-the-entry-script"></a>Zápis vstupního skriptu

Skript vstupu přijme data odeslaná webové službě, předává je do modelu a vrátí výsledky bodování. Následující skript načte model při spuštění a pak použije model k vyhodnocení dat. Tento soubor se někdy nazývá `score.py` .

> [!TIP]
> Vstupní skript je specifický pro váš model. Například skript musí znát rozhraní pro použití s modelem, datovými formáty atd.

> [!IMPORTANT]
> Při plánování použití nasazeného modelu z Azure Cognitive Services je nutné pomocí `inference_schema` balíčku povolit generování schématu pro nasazení. Tento balíček poskytuje dekoratéry, které vám umožní definovat vstupní a výstupní datový formát pro webovou službu, která provádí odvození pomocí modelu.

```python
from azureml.core.model import Model
from nlp_architect.models.absa.inference.inference import SentimentInference
from spacy.cli.download import download as spacy_download
import traceback
import json
# Inference schema for schema discovery
from inference_schema.schema_decorators import input_schema, output_schema
from inference_schema.parameter_types.numpy_parameter_type import NumpyParameterType
from inference_schema.parameter_types.standard_py_parameter_type import StandardPythonParameterType

def init():
    """
    Set up the ABSA model for Inference  
    """
    global SentInference
    spacy_download('en')
    aspect_lex = Model.get_model_path('hotel_aspect_lex')
    opinion_lex = Model.get_model_path('hotel_opinion_lex') 
    SentInference = SentimentInference(aspect_lex, opinion_lex)

# Use inference schema decorators and sample input/output to
# build the OpenAPI (Swagger) schema for the deployment
standard_sample_input = {'text': 'a sample input record containing some text' }
standard_sample_output = {"sentiment": {"sentence": "This place makes false booking prices, when you get there, they say they do not have the reservation for that day.", 
                                        "terms": [{"text": "hotels", "type": "AS", "polarity": "POS", "score": 1.0, "start": 300, "len": 6}, 
                                                  {"text": "nice", "type": "OP", "polarity": "POS", "score": 1.0, "start": 295, "len": 4}]}}
@input_schema('raw_data', StandardPythonParameterType(standard_sample_input))
@output_schema(StandardPythonParameterType(standard_sample_output))    
def run(raw_data):
    try:
        # Get the value of the 'text' field from the JSON input and perform inference
        input_txt = raw_data["text"]
        doc = SentInference.run(doc=input_txt)
        if doc is None:
            return None
        sentences = doc._sentences
        result = {"sentence": doc._doc_text}
        terms = []
        for sentence in sentences:
            for event in sentence._events:
                for x in event:
                    term = {"text": x._text, "type":x._type.value, "polarity": x._polarity.value, "score": x._score,"start": x._start,"len": x._len }
                    terms.append(term)
        result["terms"] = terms
        print("Success!")
        # Return the results to the client as a JSON document
        return {"sentiment": result}
    except Exception as e:
        result = str(e)
        # return error message back to the client
        print("Failure!")
        print(traceback.format_exc())
        return json.dumps({"error": result, "tb": traceback.format_exc()})
```

Další informace o vstupních skriptech najdete v tématu [jak a kde nasadit](how-to-deploy-and-where.md).

## <a name="define-the-software-environment"></a>Definování softwarového prostředí

Třída prostředí slouží k definování závislostí Pythonu pro službu. Obsahuje závislosti vyžadované modelem i vstupním skriptem. V tomto příkladu nainstaluje balíčky z obyčejného indexu PyPI a také z úložiště GitHub. 

```python
from azureml.core.conda_dependencies import CondaDependencies 
from azureml.core import Environment

conda = None
pip = ["azureml-defaults", "azureml-monitoring", 
       "git+https://github.com/NervanaSystems/nlp-architect.git@absa", 'nlp-architect', 'inference-schema',
       "spacy==2.0.18"]

conda_deps = CondaDependencies.create(conda_packages=None, pip_packages=pip)

myenv = Environment(name='myenv')
myenv.python.conda_dependencies = conda_deps
```

Další informace o prostředích najdete v tématu [vytváření a Správa prostředí pro školení a nasazení](how-to-use-environments.md).

## <a name="define-the-deployment-configuration"></a>Definování konfigurace nasazení

Konfigurace nasazení definuje prostředí pro hostování služby Azure Kubernetes, které slouží ke spuštění webové služby.

> [!TIP]
> Pokud si nejste jistí, jak paměť, procesor nebo GPU potřebuje vaše nasazení, můžete je zobrazit pomocí profilace. Další informace najdete v tématu [jak a kde nasadit model](how-to-deploy-and-where.md).

```python
from azureml.core.model import Model
from azureml.core.webservice import Webservice
from azureml.core.image import ContainerImage
from azureml.core.webservice import AksWebservice, Webservice

# If deploying to a cluster configured for dev/test, ensure that it was created with enough
# cores and memory to handle this deployment configuration. Note that memory is also used by
# things such as dependencies and AML components.

aks_config = AksWebservice.deploy_configuration(autoscale_enabled=True, 
                                                       autoscale_min_replicas=1, 
                                                       autoscale_max_replicas=3, 
                                                       autoscale_refresh_seconds=10, 
                                                       autoscale_target_utilization=70,
                                                       auth_enabled=True, 
                                                       cpu_cores=1, memory_gb=2, 
                                                       scoring_timeout_ms=5000, 
                                                       replica_max_concurrent_requests=2, 
                                                       max_request_wait_time=5000)
```

Další informace najdete v referenční dokumentaci k [AksService.deploy_configuration](/python/api/azureml-core/azureml.core.webservice.akswebservice#deploy-configuration-autoscale-enabled-none--autoscale-min-replicas-none--autoscale-max-replicas-none--autoscale-refresh-seconds-none--autoscale-target-utilization-none--collect-model-data-none--auth-enabled-none--cpu-cores-none--memory-gb-none--enable-app-insights-none--scoring-timeout-ms-none--replica-max-concurrent-requests-none--max-request-wait-time-none--num-replicas-none--primary-key-none--secondary-key-none--tags-none--properties-none--description-none--gpu-cores-none--period-seconds-none--initial-delay-seconds-none--timeout-seconds-none--success-threshold-none--failure-threshold-none--namespace-none--token-auth-enabled-none--compute-target-name-none-).

## <a name="define-the-inference-configuration"></a>Definovat odvozenou konfiguraci

Odvozená konfigurace odkazuje na skript vstupu a objekt prostředí:

```python
from azureml.core.model import InferenceConfig
inf_config = InferenceConfig(entry_script='score.py', environment=myenv)
```

Další informace najdete v referenční dokumentaci k [InferenceConfig](/python/api/azureml-core/azureml.core.model.inferenceconfig).

## <a name="deploy-the-model"></a>Nasazení modelu

Nasaďte model do clusteru AKS a počkejte, než se vytvoří vaše služba. V tomto příkladu jsou dva registrované modely načteny z registru a nasazeny do AKS. Po nasazení `score.py` soubor v nasazení načte tyto modely a použije je k provedení odvození.

```python
from azureml.core.webservice import AksWebservice, Webservice

c_aspect_lex = Model(ws, 'hotel_aspect_lex')
c_opinion_lex = Model(ws, 'hotel_opinion_lex') 
service_name = "hotel-absa-v2"

aks_service = Model.deploy(workspace=ws,
                           name=service_name,
                           models=[c_aspect_lex, c_opinion_lex],
                           inference_config=inf_config,
                           deployment_config=aks_config,
                           deployment_target=aks_target,
                           overwrite=True)

aks_service.wait_for_deployment(show_output = True)
print(aks_service.state)
```

Další informace najdete v referenční dokumentaci k [modelu](/python/api/azureml-core/azureml.core.model.model).

## <a name="issue-a-sample-query-to-your-service"></a>Vydat vzorový dotaz do vaší služby

Následující příklad používá informace o nasazení uložené v `aks_service` předchozí části kódu v proměnné. Tato proměnná používá k načtení adresy URL bodování a ověřovacího tokenu potřebného ke komunikaci se službou:

```python
import requests
import json

primary, secondary = aks_service.get_keys()

# Test data
input_data = '{"raw_data": {"text": "This is a nice place for a relaxing evening out with friends. The owners seem pretty nice, too. I have been there a few times including last night. Recommend."}}'

# Since authentication was enabled for the deployment, set the authorization header.
headers = {'Content-Type':'application/json',  'Authorization':('Bearer '+ primary)} 

# Send the request and display the results
resp = requests.post(aks_service.scoring_uri, input_data, headers=headers)
print(resp.text)
```

Výsledek vrácený službou je podobný následujícímu formátu JSON:

```json
{"sentiment": {"sentence": "This is a nice place for a relaxing evening out with friends. The owners seem pretty nice, too. I have been there a few times including last night. Recommend.", "terms": [{"text": "place", "type": "AS", "polarity": "POS", "score": 1.0, "start": 15, "len": 5}, {"text": "nice", "type": "OP", "polarity": "POS", "score": 1.0, "start": 10, "len": 4}]}}
```

## <a name="connect-to-cognitive-search"></a>Připojení k Kognitivní hledání

Informace o použití tohoto modelu z Kognitivní hledání najdete v tématu [sestavování a nasazení vlastní dovednosti pomocí Azure Machine Learning](../search/cognitive-search-tutorial-aml-custom-skill.md) kurzu.

## <a name="clean-up-the-resources"></a>Vyčištění prostředků

Pokud jste cluster AKS vytvořili konkrétně pro tento příklad, odstraňte prostředky po dokončení testování pomocí Kognitivní hledání.

> [!IMPORTANT]
> Azure účtuje na základě toho, jak dlouho je cluster AKS nasazený. Nezapomeňte ho vyčistit až po jeho dokončení.

```python
aks_service.delete()
aks_target.delete()
```

## <a name="next-steps"></a>Další kroky

* [Sestavení a nasazení vlastní dovednosti pomocí Azure Machine Learning](../search/cognitive-search-tutorial-aml-custom-skill.md)