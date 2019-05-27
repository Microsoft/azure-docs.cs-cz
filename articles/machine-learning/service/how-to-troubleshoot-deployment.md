---
title: Průvodce řešením problémů s nasazením
titleSuffix: Azure Machine Learning service
description: Zjistěte, jak obejít, řešení a řešení potíží s běžnými chybami nasazení Dockeru s AKS a ACI pomocí služby Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: chris-lauren
ms.author: clauren
ms.reviewer: jmartens
ms.date: 05/02/2018
ms.custom: seodec18
ms.openlocfilehash: 3730e4a0bfa05e6606e50b9bbd9d9152e2488954
ms.sourcegitcommit: 16cb78a0766f9b3efbaf12426519ddab2774b815
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/17/2019
ms.locfileid: "65851726"
---
# <a name="troubleshooting-azure-machine-learning-service-aks-and-aci-deployments"></a>Řešení potíží s nasazením služby AKS a ACI Azure Machine Learning

Zjistěte, jak obejít nebo řešit běžné chyby nasazení Dockeru s Azure Container Instances (ACI) a Azure Kubernetes Service (AKS) pomocí služby Azure Machine Learning.

Při nasazení modelu ve službě Azure Machine Learning, systém provádí řadu úloh. Úkoly nasazení jsou:

1. Zaregistrujte model v registru pracovního prostoru modelu.

2. Sestavíte image Dockeru, včetně:
    1. Stáhněte si registrovanému modelu z registru. 
    2. Vytvoření souboru dockerfile, pomocí prostředí Pythonu na základě závislostí, které zadáte v souboru yaml prostředí.
    3. Přidáte soubory modelu a hodnoticí skript, který zadáte v souboru dockerfile.
    4. Vytvářejte nová image Dockeru pomocí souboru dockerfile.
    5. Zaregistrujte image Dockeru s Azure Container Registry přidružený k pracovnímu prostoru.

    > [!IMPORTANT]
    > V závislosti na vašem kódu vytvoření bitové kopie dojít automaticky bez zásahu.

3. Nasaďte image Dockeru do služby Azure Container Instance (ACI) nebo do Azure Kubernetes Service (AKS).

4. Spuštění nového kontejneru (nebo kontejnery) v ACI a AKS. 

Další informace o tomto procesu v [Správa modelů ve službě](concept-model-management-and-deployment.md) úvod.

## <a name="before-you-begin"></a>Před zahájením

Pokud narazíte na jakékoli potíže, je prvním krokem je rozdělit úlohu nasazení (viz předchozí) do jednotlivých kroků a izolovat daný problém.

Rozdělení nasazení do úlohy je užitečný v případě, že používáte [Webservice.deploy()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice%28class%29?view=azure-ml-py#deploy-workspace--name--model-paths--image-config--deployment-config-none--deployment-target-none-) rozhraní API, nebo [Webservice.deploy_from_model()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice%28class%29?view=azure-ml-py#deploy-from-model-workspace--name--models--image-config--deployment-config-none--deployment-target-none-) rozhraní API, jako obě tyto funkce provést výše uvedené kroky jako jednu akci. Obvykle jsou vhodné těchto rozhraních API, ale je k rozdělení kroky při řešení potíží s nahrazením pomocí následující volání rozhraní API.

1. Zaregistrujte model. Tady je ukázkový kód:

    ```python
    # register a model out of a run record
    model = best_run.register_model(model_name='my_best_model', model_path='outputs/my_model.pkl')

    # or, you can register a file or a folder of files as a model
    model = Model.register(model_path='my_model.pkl', model_name='my_best_model', workspace=ws)
    ```

2. Sestavení image. Tady je ukázkový kód:

    ```python
    # configure the image
    image_config = ContainerImage.image_configuration(runtime="python",
                                                      execution_script="score.py",
                                                      conda_file="myenv.yml")

    # create the image
    image = Image.create(name='myimg', models=[model], image_config=image_config, workspace=ws)

    # wait for image creation to finish
    image.wait_for_creation(show_output=True)
    ```

3. Nasazení bitové kopie jako služba. Tady je ukázkový kód:

    ```python
    # configure an ACI-based deployment
    aci_config = AciWebservice.deploy_configuration(cpu_cores=1, memory_gb=1)

    aci_service = Webservice.deploy_from_image(deployment_config=aci_config, 
                                               image=image, 
                                               name='mysvc', 
                                               workspace=ws)
    aci_service.wait_for_deployment(show_output=True)    
    ```

Jakmile máte rozdělené procesu nasazení do jednotlivých kroků, abychom se mohli podívat na některé z nejběžnějších chyb.

## <a name="image-building-fails"></a>Bitové kopie sestavení se nezdaří

Pokud nejde sestavit image Dockeru, [image.wait_for_creation()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.image.image(class)?view=azure-ml-py#wait-for-creation-show-output-false-) nebo [service.wait_for_deployment()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py#wait-for-deployment-show-output-false-) volání selže s některé chybové zprávy, které může nabídnout některé příčiny. Můžete také zjistit další podrobnosti o chybách v protokolu sestavení image. Níže je vzorový kód ukazuje, jak zjistit identifikátor uri protokolu bitové kopie sestavení.

```python
# if you already have the image object handy
print(image.image_build_log_uri)

# if you only know the name of the image (note there might be multiple images with the same name but different version number)
print(ws.images['myimg'].image_build_log_uri)

# list logs for all images in the workspace
for name, img in ws.images.items():
    print (img.name, img.version, img.image_build_log_uri)
```

Identifikátor uri protokolu bitové kopie je adresa URL SAS odkazující na soubor protokolu se ukládají ve službě Azure blob storage. Jednoduše zkopírujte a vložte identifikátor uri do okna prohlížeče a můžete stáhnout a zobrazit soubor protokolu.

### <a name="azure-key-vault-access-policy-and-azure-resource-manager-templates"></a>Azure zásad přístupu trezoru klíčů a šablon Azure Resource Manageru

Sestavení image může také selhat z důvodu problému s zásady přístupu pro Azure Key Vault. Tato situace může nastat, když použijete šablony Azure Resource Manageru k vytvoření pracovního prostoru a přidružené prostředky (včetně služby Azure Key Vault), více než jednou. Například pomocí šablony více než jednou se stejnými parametry jako součást průběžné integrace a nasazení kanálu.

Většina operací vytváření prostředků prostřednictvím šablony jsou idempotentní, ale služby Key Vault vymaže zásady přístupu při každém použití této šablony. Vymazává se zásady konce přístupu ke službě Key Vault pro existující pracovní prostor, který jej používá. Tato podmínka způsobí chyby při pokusu o vytvoření nových imagí. Následují příklady chyb, které můžou přijímat:

__Portál__:
```text
Create image "myimage": An internal server error occurred. Please try again. If the problem persists, contact support.
```

__SDK__:
```python
image = ContainerImage.create(name = "myimage", models = [model], image_config = image_config, workspace = ws)
Creating image
Traceback (most recent call last):
  File "C:\Python37\lib\site-packages\azureml\core\image\image.py", line 341, in create
    resp.raise_for_status()
  File "C:\Python37\lib\site-packages\requests\models.py", line 940, in raise_for_status
    raise HTTPError(http_error_msg, response=self)
requests.exceptions.HTTPError: 500 Server Error: Internal Server Error for url: https://eastus.modelmanagement.azureml.net/api/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.MachineLearningServices/workspaces/<workspace-name>/images?api-version=2018-11-19

Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
  File "C:\Python37\lib\site-packages\azureml\core\image\image.py", line 346, in create
    'Content: {}'.format(resp.status_code, resp.headers, resp.content))
azureml.exceptions._azureml_exception.WebserviceException: Received bad response from Model Management Service:
Response Code: 500
Headers: {'Date': 'Tue, 26 Feb 2019 17:47:53 GMT', 'Content-Type': 'application/json', 'Transfer-Encoding': 'chunked', 'Connection': 'keep-alive', 'api-supported-versions': '2018-03-01-preview, 2018-11-19', 'x-ms-client-request-id': '3cdcf791f1214b9cbac93076ebfb5167', 'x-ms-client-session-id': '', 'Strict-Transport-Security': 'max-age=15724800; includeSubDomains; preload'}
Content: b'{"code":"InternalServerError","statusCode":500,"message":"An internal server error occurred. Please try again. If the problem persists, contact support"}'
```

__CLI__:
```text
ERROR: {'Azure-cli-ml Version': None, 'Error': WebserviceException('Received bad response from Model Management Service:\nResponse Code: 500\nHeaders: {\'Date\': \'Tue, 26 Feb 2019 17:34:05
GMT\', \'Content-Type\': \'application/json\', \'Transfer-Encoding\': \'chunked\', \'Connection\': \'keep-alive\', \'api-supported-versions\': \'2018-03-01-preview, 2018-11-19\', \'x-ms-client-request-id\':
\'bc89430916164412abe3d82acb1d1109\', \'x-ms-client-session-id\': \'\', \'Strict-Transport-Security\': \'max-age=15724800; includeSubDomains; preload\'}\nContent:
b\'{"code":"InternalServerError","statusCode":500,"message":"An internal server error occurred. Please try again. If the problem persists, contact support"}\'',)}
```

K tomuto problému vyhnout, doporučujeme jednu z následujících postupů:

* Nenasazujte šablony více než jednou pro stejné parametry. Nebo odstraňte existující prostředky před je znovu vytvořit pomocí šablony.
* Zkontrolujte zásady přístupu trezoru klíčů a pak pomocí těchto zásad nastavte `accessPolicies` vlastnosti šablony.
* Zkontrolujte, jestli prostředek Key Vault už existuje. Pokud ano, nelze jej znovu vytvořit pomocí šablony. Například přidejte parametr, který umožňuje zakázat vytvoření prostředku služby Key Vault, pokud již existuje.

## <a name="debug-locally"></a>Ladit v místním prostředí

Pokud dojde k problémům při nasazování modelu ACI a AKS, zkuste nasazení jako místní webové služby. Pomocí místní webové služby usnadňuje řešení problémů. Image Dockeru obsahující model je stažena a spuštěna v místním systému.

> [!IMPORTANT]
> Nasazení místní webové služby vyžadují funkční instalace Docker v místním systému. Docker musí běžet, než nasazení místní webové služby. Informace o instalaci a použití Dockeru najdete v tématu [ https://www.docker.com/ ](https://www.docker.com/).

> [!WARNING]
> Nasazení místní webové služby nejsou podporovány pro produkční scénáře.

Pokud chcete nasadit místně, upravte kód Refaktorovat pro použití `LocalWebservice.deploy_configuration()` pro vytvoření konfigurace nasazení. Pak pomocí `Model.deploy()` službu nasadíte. Tento příklad nasadí modelu (součástí `model` proměnná) jako místní webové služby:

```python
from azureml.core.model import InferenceConfig
from azureml.core.webservice import LocalWebservice

# Create inference configuration. This creates a docker image that contains the model.
inference_config = InferenceConfig(runtime= "python", 
                                   execution_script="score.py",
                                   conda_file="myenv.yml")

# Create a local deployment, using port 8890 for the web service endpoint
deployment_config = LocalWebservice.deploy_configuration(port=8890)
# Deploy the service
service = Model.deploy(ws, "mymodel", [model], inference_config, deployment_config)
# Wait for the deployment to complete
service.wait_for_deployment(True)
# Display the port that the web service is available on
print(service.port)
```

V tomto okamžiku můžete pracovat se službou jako za normálních okolností. Například následující kód ukazuje odesílání dat do služby:

```python
import json

test_sample = json.dumps({'data': [
    [1,2,3,4,5,6,7,8,9,10], 
    [10,9,8,7,6,5,4,3,2,1]
]})

test_sample = bytes(test_sample,encoding = 'utf8')

prediction = service.run(input_data=test_sample)
print(prediction)
```

### <a name="update-the-service"></a>Aktualizace služby

Při místním testování, budete muset aktualizovat `score.py` souboru přidat protokolování nebo pokus o vyřešení potíží, které jste se seznámili. Chcete-li znovu načíst změny `score.py` soubor `reload()`. Například následující kód znovu načte skript pro službu a pak odešle data do ní. Data vypočítává se skóre pomocí aktualizovaný `score.py` souboru:

```python
service.reload()
print(service.run(input_data=test_sample))
```

> [!NOTE]
> Skript je znovu načten v místě určeném `InferenceConfig` objekt použitý objektem služby.

Chcete-li změnit model, závislosti systému Conda nebo konfigurace nasazení, použijte [update()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice%28class%29?view=azure-ml-py#update--args-). Následující příklad aktualizuje model používaný službou:

```python
service.update([different_model], inference_config, deployment_config)
```

### <a name="delete-the-service"></a>Odstranit službu

Pokud chcete službu odstranit, použijte [delete()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice%28class%29?view=azure-ml-py#delete--).

### <a id="dockerlog"></a> Zkontrolujte protokol Dockeru.

Můžete vytisknout podrobné zprávy protokolu modulu Dockeru z objektu služby. Zobrazit protokol pro místní nasazení, ACI a AKS. Následující příklad ukazuje, jak vytisknout protokoly.

```python
# if you already have the service object handy
print(service.get_logs())

# if you only know the name of the service (note there might be multiple services with the same name but different version number)
print(ws.webservices['mysvc'].get_logs())
```

## <a name="service-launch-fails"></a>Selhání spuštění služby

Jakmile na obrázku je úspěšně vytvořen, se systém pokusí o spuštění kontejneru pomocí konfigurace vašeho nasazení. Jako součást procesu spouštění kontejneru `init()` vyvolání funkce v hodnoticí skript v systému. Pokud existují nezachycených výjimek `init()` fungovat, může se zobrazit **CrashLoopBackOff** chyby v chybové zprávě.

Použijte informace v [zkontrolujte protokol Dockeru](#dockerlog) můžete zkontrolovat v protokolech.

## <a name="function-fails-getmodelpath"></a>Selže funkce: get_model_path()

Často v `init()` funkce v hodnoticí skript [Model.get_model_path()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#get-model-path-model-name--version-none---workspace-none-) je funkce volaná k vyhledání modelu soubor nebo složku modelu souborů v kontejneru. Pokud model souboru nebo složky nebyl nalezen, funkce selže. Nejjednodušší způsob, jak ladění této chyby je spustit níže uvedeného kódu Pythonu v prostředí kontejneru:

```python
import logging
logging.basicConfig(level=logging.DEBUG)
from azureml.core.model import Model
print(Model.get_model_path(model_name='my-best-model'))
```

Tento příklad vytiskne místní cestu (vzhledem k `/var/azureml-app`) v kontejneru, kde se očekává hodnoticí skript k vyhledání souboru modelu nebo složky. Potom můžete ověřit, pokud soubor nebo složka jsou skutečně kde se očekává se.

Nastavení úrovně protokolování ladění může způsobit další informace o přihlášení, které mohou být užitečné při identifikaci selhání.

## <a name="function-fails-runinputdata"></a>Selže funkce: run(input_data)

Pokud úspěšně nasazení služby, ale jeho dojde k chybě při odesílání dat na bodovací koncový bod, můžete přidat chyby zachytávání příkaz v vaše `run(input_data)` fungovat tak, že místo toho vrátí podrobnou chybovou zprávu. Příklad:

```python
def run(input_data):
    try:
        data = json.loads(input_data)['data']
        data = np.array(data)
        result = model.predict(data)
        return json.dumps({"result": result.tolist()})
    except Exception as e:
        result = str(e)
        # return error message back to the client
        return json.dumps({"error": result})
```

**Poznámka:** Vrací chybové zprávy z `run(input_data)` volání by mělo být provedeno pro ladění pouze pro účely. Z bezpečnostních důvodů, které by neměly vracet chybové zprávy tímto způsobem v produkčním prostředí.

## <a name="http-status-code-503"></a>Stavový kód HTTP 503

Nasazení služby Azure Kubernetes Service podporují automatické škálování, která umožňuje repliky přidaná kvůli podpoře dalších zatížení. Nicméně, automatického škálování je určen ke zpracování **postupné** změn v zatížení. Pokud se zobrazí extrémní požadavků za sekundu, klienti mohou obdržet stavový kód HTTP 503.

Existují dvě věci, které pomáhají zabránit 503 kódy stavu:

* Změnit úroveň využití, které automatické škálování vytvoří nové repliky.
    
    Ve výchozím nastavení, je nastavit automatické škálování cílové využití na 70 %, což znamená, že služba může zpracovat poraďte se špičkami požadavků za sekundu (předávajících stran) až 30 %. Cílové využití můžete upravit tak, že nastavíte `autoscale_target_utilization` na nižší hodnotu.

    > [!IMPORTANT]
    > Tato změna nezpůsobí repliky, který se má vytvořit *rychleji*. Místo toho se vytvoří při nižší prahové hodnotě využití. Namísto čekání, dokud služba nebude 70 % využití, změna hodnoty na 30 % způsobí, že repliky vytvořit, když dojde k využití 30 %.
    
    Pokud webová služba používá aktuální maximální počet replik a stále se zobrazuje 503 stavové kódy, zvýšit `autoscale_max_replicas` hodnotu zvýšit maximální počet replik.

* Změňte minimální počet replik. Zvýšení minimální repliky poskytuje větší fond pro zpracování příchozích provozní špičky.

    Chcete-li zvýšit minimální počet replik, nastavte `autoscale_min_replicas` na vyšší hodnotu. Požadované repliky můžete vypočítat pomocí následujícího kódu, nahraďte hodnoty hodnotami specifickými do projektu:

    ```python
    from math import ceil
    # target requests per second
    targetRps = 20
    # time to process the request (in seconds)
    reqTime = 10
    # Maximum requests per container
    maxReqPerContainer = 1
    # target_utilization. 70% in this example
    targetUtilization = .7

    concurrentRequests = targetRps * reqTime / targetUtilization

    # Number of container replicas
    replicas = ceil(concurrentRequests / maxReqPerContainer)
    ```

    > [!NOTE]
    > Pokud se zobrazí špičky požadavek větší, než dokáže zpracovat nové minimální repliky, může se zobrazit 503s znovu. Například jako provoz do vaší služby zvyšuje, budete muset zvýšit minimální repliky.

Další informace o nastavení `autoscale_target_utilization`, `autoscale_max_replicas`, a `autoscale_min_replicas` , najdete v článku [AksWebservice](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.akswebservice?view=azure-ml-py) odkazu na modul.


## <a name="next-steps"></a>Další postup

Další informace o nasazení:

* [Jak nasadit a kde](how-to-deploy-and-where.md)
* [Kurz: Trénování a nasazení modelů](tutorial-train-models-with-aml.md)
