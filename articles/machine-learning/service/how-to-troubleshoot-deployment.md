---
title: Průvodce řešením problémů s nasazením
titleSuffix: Azure Machine Learning
description: Naučte se řešit, řešit a řešit běžné chyby nasazení Docker pomocí služby Azure Kubernetes a Azure Container Instances pomocí Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: chris-lauren
ms.author: clauren
ms.reviewer: jmartens
ms.date: 10/25/2019
ms.custom: seodec18
ms.openlocfilehash: 3a79c95d627bbdec3a91a1d048a48ff061b308ca
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2019
ms.locfileid: "73489360"
---
# <a name="troubleshooting-azure-machine-learning-azure-kubernetes-service-and-azure-container-instances-deployment"></a>Řešení potíží s Azure Machine Learning služby Azure Kubernetes a nasazení Azure Container Instances

Naučte se, jak obejít nebo vyřešit běžné chyby nasazení Docker pomocí Azure Container Instances (ACI) a Azure Kubernetes Service (AKS) pomocí Azure Machine Learning.

Při nasazování modelu v Azure Machine Learning systém provádí řadu úloh. Úlohy nasazení:

1. Zaregistrujte model v registru modelu pracovního prostoru.

2. Sestavte image Docker, včetně:
    1. Stáhněte si registrovaný model z registru. 
    2. Vytvořte souboru Dockerfile s prostředím Python na základě závislostí, které zadáte v souboru prostředí YAML.
    3. Přidejte své soubory modelu a model bodování, který zadáte do souboru Dockerfile.
    4. Sestavte novou image Docker pomocí souboru Dockerfile.
    5. Zaregistrujte bitovou kopii Docker s Azure Container Registry přidruženým k pracovnímu prostoru.

    > [!IMPORTANT]
    > V závislosti na vašem kódu probíhá vytváření obrázků automaticky bez vašeho vstupu.

3. Nasaďte image Docker do služby Azure Container instance (ACI) nebo do služby Azure Kubernetes Service (AKS).

4. Spuštění nového kontejneru (nebo kontejnerů) v ACI nebo AKS. 

Další informace o tomto procesu najdete v úvodu [Správa modelů](concept-model-management-and-deployment.md) .

## <a name="before-you-begin"></a>Než začnete

Pokud narazíte na problém, je první věc, kterou je potřeba udělat, rozdělte úlohu nasazení (popsanou výše) do jednotlivých kroků k izolaci problému.

Přerušení nasazení na úlohy je užitečné, pokud používáte rozhraní API [WebService. deploy ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice%28class%29?view=azure-ml-py#deploy-workspace--name--model-paths--image-config--deployment-config-none--deployment-target-none-) nebo [WebService. deploy_from_model ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice%28class%29?view=azure-ml-py#deploy-from-model-workspace--name--models--image-config--deployment-config-none--deployment-target-none-) , protože obě tyto funkce provádějí výše uvedené kroky jako jednu akci. Tato rozhraní API jsou obvykle užitečná, ale pomáhají při odstraňování potíží, když je nahradíte pomocí níže uvedených volání rozhraní API.

1. Zaregistrujte model. Zde je ukázkový kód:

    ```python
    # register a model out of a run record
    model = best_run.register_model(model_name='my_best_model', model_path='outputs/my_model.pkl')

    # or, you can register a file or a folder of files as a model
    model = Model.register(model_path='my_model.pkl', model_name='my_best_model', workspace=ws)
    ```

2. Sestavte image. Zde je ukázkový kód:

    ```python
    # configure the image
    image_config = ContainerImage.image_configuration(runtime="python",
                                                      entry_script="score.py",
                                                      conda_file="myenv.yml")

    # create the image
    image = Image.create(name='myimg', models=[model], image_config=image_config, workspace=ws)

    # wait for image creation to finish
    image.wait_for_creation(show_output=True)
    ```

3. Nasaďte Image jako službu. Zde je ukázkový kód:

    ```python
    # configure an ACI-based deployment
    aci_config = AciWebservice.deploy_configuration(cpu_cores=1, memory_gb=1)

    aci_service = Webservice.deploy_from_image(deployment_config=aci_config, 
                                               image=image, 
                                               name='mysvc', 
                                               workspace=ws)
    aci_service.wait_for_deployment(show_output=True)    
    ```

Po rozlomení procesu nasazení na jednotlivé úlohy si můžeme prohlédnout některé z nejběžnějších chyb.

## <a name="image-building-fails"></a>Vytváření imagí se nepovede.

Pokud image Docker nelze sestavit, volání [image. wait_for_creation ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.image.image(class)?view=azure-ml-py#wait-for-creation-show-output-false-) nebo [Service. wait_for_deployment ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py#wait-for-deployment-show-output-false-) se nezdařila s některými chybovými zprávami, které mohou nabídnout některá oznámení. Můžete také získat další podrobnosti o chybách z protokolu sestavení imagí. Níže je uvedený ukázkový kód, který ukazuje, jak zjistit identifikátor URI protokolu sestavení imagí.

```python
# if you already have the image object handy
print(image.image_build_log_uri)

# if you only know the name of the image (note there might be multiple images with the same name but different version number)
print(ws.images['myimg'].image_build_log_uri)

# list logs for all images in the workspace
for name, img in ws.images.items():
    print(img.name, img.version, img.image_build_log_uri)
```

Identifikátor URI protokolu imagí je adresa URL SAS ukazující na soubor protokolu uložený ve službě Azure Blob Storage. Jednoduše zkopírujte a vložte identifikátor URI do okna prohlížeče a můžete si stáhnout a zobrazit soubor protokolu.

### <a name="azure-key-vault-access-policy-and-azure-resource-manager-templates"></a>Azure Key Vault zásady přístupu a šablony Azure Resource Manager

Kvůli potížím se zásadami přístupu na Azure Key Vault může sestavení bitové kopie selhat i v důsledku problému. K této situaci může dojít, když použijete šablonu Azure Resource Manager k vytvoření pracovního prostoru a přidružených prostředků (včetně Azure Key Vault) několikrát. Například použití šablony několikrát se stejnými parametry jako součást kanálu průběžné integrace a nasazení.

Většina operací vytváření prostředků prostřednictvím šablon je idempotentní, ale Key Vault neodstraní zásady přístupu pokaždé, když se šablona používá. Vymazání zásad přístupu přeruší přístup k Key Vault pro libovolný existující pracovní prostor, který ho používá. Tato podmínka způsobí chyby při pokusu o vytvoření nových imagí. Následují příklady chyb, které můžete získat:

__Portál__:
```text
Create image "myimage": An internal server error occurred. Please try again. If the problem persists, contact support.
```

__Sada SDK__:
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

Rozhraní příkazového __řádku__:
```text
ERROR: {'Azure-cli-ml Version': None, 'Error': WebserviceException('Received bad response from Model Management Service:\nResponse Code: 500\nHeaders: {\'Date\': \'Tue, 26 Feb 2019 17:34:05
GMT\', \'Content-Type\': \'application/json\', \'Transfer-Encoding\': \'chunked\', \'Connection\': \'keep-alive\', \'api-supported-versions\': \'2018-03-01-preview, 2018-11-19\', \'x-ms-client-request-id\':
\'bc89430916164412abe3d82acb1d1109\', \'x-ms-client-session-id\': \'\', \'Strict-Transport-Security\': \'max-age=15724800; includeSubDomains; preload\'}\nContent:
b\'{"code":"InternalServerError","statusCode":500,"message":"An internal server error occurred. Please try again. If the problem persists, contact support"}\'',)}
```

Chcete-li se tomuto problému vyhnout, doporučujeme jeden z následujících přístupů:

* Nesaďte šablonu více než jednou pro stejné parametry. Nebo odstraňte existující prostředky, abyste je mohli znovu vytvořit pomocí šablony.
* Zkontrolujte zásady přístupu Key Vault a pak pomocí těchto zásad nastavte vlastnost `accessPolicies` šablony.
* Ověřte, zda prostředek Key Vault již existuje. Pokud tomu tak není, nevytvářejte ho znovu prostřednictvím šablony. Například přidejte parametr, který umožňuje zakázat vytvoření prostředku Key Vault, pokud již existuje.

## <a name="debug-locally"></a>Místní ladění

Pokud narazíte na problémy s nasazením modelu do ACI nebo AKS, zkuste ho nasadit jako místní. Použití místního prostředí usnadňuje řešení problémů. Image Docker obsahující model se stáhne a spustí v místním systému.

> [!IMPORTANT]
> Místní nasazení vyžadují v místním systému pracovní instalaci do dokovacího prostředí. Před nasazením místního prostředí musí být spuštěný Docker. Informace o instalaci a použití Docker najdete v tématu [https://www.docker.com/](https://www.docker.com/).

> [!WARNING]
> Místní nasazení se pro produkční scénáře nepodporují.

Chcete-li nasadit místně, upravte kód tak, aby používal `LocalWebservice.deploy_configuration()` k vytvoření konfigurace nasazení. Pak použijte `Model.deploy()` k nasazení služby. Následující příklad nasadí model (obsažený v proměnné `model`) jako místní:

```python
from azureml.core.model import InferenceConfig, Model
from azureml.core.webservice import LocalWebservice

# Create inference configuration. This creates a docker image that contains the model.
inference_config = InferenceConfig(runtime="python",
                                   entry_script="score.py",
                                   conda_file="myenv.yml")

# Create a local deployment, using port 8890 for the  endpoint
deployment_config = LocalWebservice.deploy_configuration(port=8890)
# Deploy the service
service = Model.deploy(
    ws, "mymodel", [model], inference_config, deployment_config)
# Wait for the deployment to complete
service.wait_for_deployment(True)
# Display the port that the  is available on
print(service.port)
```

V tuto chvíli můžete pracovat se službou jako normální. Například následující kód ukazuje odeslání dat do služby:

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

### <a name="update-the-service"></a>Aktualizace služby

Během místního testování možná budete muset aktualizovat soubor `score.py`, aby se přidalo protokolování, nebo se pokusí vyřešit všechny problémy, které jste zjistili. Chcete-li znovu načíst změny v souboru `score.py`, použijte `reload()`. Například následující kód znovu načte skript pro službu a poté do něj pošle data. Data se vyhodnotí pomocí aktualizovaného `score.py` souboru:

> [!IMPORTANT]
> Metoda `reload` je k dispozici pouze pro místní nasazení. Informace o aktualizaci nasazení na jiný cíl služby COMPUTE najdete v části aktualizace v tématu [nasazení modelů](how-to-deploy-and-where.md#update).

```python
service.reload()
print(service.run(input_data=test_sample))
```

> [!NOTE]
> Skript se znovu načte z umístění určeného objektem `InferenceConfig`, který služba používá.

Chcete-li změnit model, závislosti conda nebo konfiguraci nasazení, použijte [Update ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice%28class%29?view=azure-ml-py#update--args-). Následující příklad aktualizuje model používaný službou:

```python
service.update([different_model], inference_config, deployment_config)
```

### <a name="delete-the-service"></a>Odstranit službu

Pokud chcete službu odstranit, použijte [Delete ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice%28class%29?view=azure-ml-py#delete--).

### <a id="dockerlog"></a>Kontrola protokolu Docker

Podrobnou zprávu protokolu modulu Docker můžete vytisknout z objektu služby. Můžete zobrazit protokol pro ACI, AKS a místní nasazení. Následující příklad ukazuje, jak tisknout protokoly.

```python
# if you already have the service object handy
print(service.get_logs())

# if you only know the name of the service (note there might be multiple services with the same name but different version number)
print(ws.webservices['mysvc'].get_logs())
```

## <a name="service-launch-fails"></a>Spuštění služby se nezdařilo.

Po úspěšném vytvoření image se systém pokusí spustit kontejner pomocí konfigurace nasazení. V rámci procesu spuštění kontejneru je funkce `init()` ve vašem skriptu bodování vyvolána systémem. Pokud se ve funkci `init()` nezachycené výjimky, může se v chybové zprávě zobrazit chyba **CrashLoopBackOff** .

Použijte informace v části Kontrola [protokolu Docker](#dockerlog) pro kontrolu protokolů.

## <a name="function-fails-get_model_path"></a>Neúspěšná funkce: get_model_path ()

Často se ve funkci `init()` ve skriptu bodování volá funkce [model. Get _model_path ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#get-model-path-model-name--version-none---workspace-none-) , která vyhledá soubor modelu nebo složku souborů modelů v kontejneru. Pokud soubor modelu nebo složku nelze nalézt, funkce se nezdařila. Nejjednodušší způsob, jak tuto chybu ladit, je spuštění níže uvedeného kódu Pythonu v prostředí kontejneru:

```python
from azureml.core.model import Model
import logging
logging.basicConfig(level=logging.DEBUG)
print(Model.get_model_path(model_name='my-best-model'))
```

Tento příklad vytiskne místní cestu (vzhledem k `/var/azureml-app`) v kontejneru, ve kterém váš skript bodování očekává nalezení souboru modelu nebo složky. Pak můžete ověřit, jestli je soubor nebo složka skutečně tam, kde se očekává.

Nastavení úrovně protokolování na ladění může způsobit, že budou protokolovány Další informace, které mohou být užitečné při identifikaci selhání.

## <a name="function-fails-runinput_data"></a>Neúspěšná funkce: Run (input_data)

Pokud se služba úspěšně nasadila, ale dojde k chybě při odesílání dat do koncového bodu, můžete do funkce `run(input_data)` přidat příkaz pro zachycení chyb, aby se místo toho vrátila podrobná chybová zpráva. Například:

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

**Poznámka**: vrácení chybových zpráv z `run(input_data)` volání by mělo být provedeno pouze pro účely ladění. Z bezpečnostních důvodů byste neměli vracet chybové zprávy tímto způsobem v produkčním prostředí.

## <a name="http-status-code-503"></a>Stavový kód HTTP 503

Nasazení služby Azure Kubernetes podporují automatické škálování, které umožňuje přidat repliky pro podporu dalšího zatížení. Automatické škálování je však navrženo pro zpracování **postupných** změn v zatížení. Pokud v požadavcích za sekundu obdržíte velké špičky, mohou klienti obdržet stavový kód HTTP 503.

K dispozici jsou dvě věci, které vám pomůžou zabránit stavovým kódům 503:

* Změňte úroveň využití, při které automatické škálování vytvoří nové repliky.
    
    Ve výchozím nastavení je cílení na automatické škálování nastaveno na 70%, což znamená, že služba dokáže zpracovávat špičky v požadavcích za sekundu (RPS) až o 30%. Cíl využití můžete upravit nastavením `autoscale_target_utilization` na nižší hodnotu.

    > [!IMPORTANT]
    > Tato změna nezpůsobí *rychlejší*vytváření replik. Místo toho jsou vytvořeny s nižší prahovou hodnotou využití. Místo čekání na vyčerpání služby 70% se změna hodnoty na 30% způsobí, že se repliky vytvoří, když dojde k 30% využití.
    
    Pokud už používá aktuální maximální počet replik a stále se zobrazuje stavové kódy 503, zvyšte hodnotu `autoscale_max_replicas`, abyste zvýšili maximální počet replik.

* Změňte minimální počet replik. Zvýšení minimálního počet replik poskytuje větší fond pro zpracování příchozích špiček.

    Pokud chcete zvýšit minimální počet replik, nastavte `autoscale_min_replicas` na vyšší hodnotu. Požadované repliky můžete vypočítat pomocí následujícího kódu a nahradit hodnoty hodnotami specifickými pro váš projekt:

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
    > Pokud obdržíte špičky žádostí větší, než jsou nové minimální repliky schopné zpracovat, můžete se 503s znovu dostat. Například při zvyšování provozu na službu možná budete muset zvětšit minimální repliky.

Další informace o nastavení `autoscale_target_utilization`, `autoscale_max_replicas`a `autoscale_min_replicas` pro najdete v tématu Reference k modulu [AksWebservice](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.akswebservice?view=azure-ml-py) .


## <a name="advanced-debugging"></a>Pokročilé ladění

V některých případech možná budete muset interaktivně ladit kód Pythonu obsažený v nasazení modelu. Například pokud se skript vstupu nezdařil a důvod nelze určit pomocí dalšího protokolování. Pomocí Visual Studio Code a Python Tools for Visual Studio (PTVSD) se můžete připojit ke kódu běžícímu uvnitř kontejneru Docker.

> [!IMPORTANT]
> Tato metoda ladění nefunguje při použití `Model.deploy()` a `LocalWebservice.deploy_configuration` k nasazení modelu místně. Místo toho je nutné vytvořit Image pomocí třídy [ContainerImage](https://docs.microsoft.com/python/api/azureml-core/azureml.core.image.containerimage?view=azure-ml-py) . 
>
> Místní nasazení vyžadují v místním systému pracovní instalaci do dokovacího prostředí. Před nasazením místního prostředí musí být spuštěný Docker. Informace o instalaci a použití Docker najdete v tématu [https://www.docker.com/](https://www.docker.com/).

### <a name="configure-development-environment"></a>Konfigurace vývojového prostředí

1. Chcete-li nainstalovat Python Tools for Visual Studio (PTVSD) do místního vývojového prostředí VS Code, použijte následující příkaz:

    ```
    python -m pip install --upgrade ptvsd
    ```

    Další informace o použití PTVSD s VS Code najdete v tématu [vzdálené ladění](https://code.visualstudio.com/docs/python/debugging#_remote-debugging).

1. Pokud chcete nakonfigurovat VS Code ke komunikaci s imagí Docker, vytvořte novou konfiguraci ladění:

    1. Z VS Code vyberte nabídku __ladění__ a pak vyberte __otevřít konfigurace__. Otevře se soubor s názvem __Launch. JSON__ .

    1. V souboru __Launch. JSON__ Najděte řádek, který obsahuje `"configurations": [`a vložte následující text za něj:

        ```json
        {
            "name": "Azure Machine Learning: Docker Debug",
            "type": "python",
            "request": "attach",
            "port": 5678,
            "host": "localhost",
            "pathMappings": [
                {
                    "localRoot": "${workspaceFolder}",
                    "remoteRoot": "/var/azureml-app"
                }
            ]
        }
        ```

        > [!IMPORTANT]
        > Pokud již existují další položky v oddílu konfigurace, přidejte čárku (,) za kód, který jste vložili.

        Tato část se připojuje k kontejneru Docker pomocí portu 5678.

    1. Uložte soubor __Launch. JSON__ .

### <a name="create-an-image-that-includes-ptvsd"></a>Vytvoření image, která zahrnuje PTVSD

1. Upravte prostředí conda pro nasazení tak, aby zahrnovalo PTVSD. Následující příklad ukazuje přidání pomocí parametru `pip_packages`:

    ```python
    from azureml.core.conda_dependencies import CondaDependencies 
    
    # Usually a good idea to choose specific version numbers
    # so training is made on same packages as scoring
    myenv = CondaDependencies.create(conda_packages=['numpy==1.15.4',            
                                'scikit-learn==0.19.1', 'pandas==0.23.4'],
                                 pip_packages = ['azureml-defaults==1.0.17', 'ptvsd'])
    
    with open("myenv.yml","w") as f:
        f.write(myenv.serialize_to_string())
    ```

1. Pokud chcete začít PTVSD a po spuštění služby počkat na připojení, přidejte do horní části souboru `score.py` následující:

    ```python
    import ptvsd
    # Allows other computers to attach to ptvsd on this IP address and port.
    ptvsd.enable_attach(address=('0.0.0.0', 5678), redirect_output = True)
    # Wait 30 seconds for a debugger to attach. If none attaches, the script continues as normal.
    ptvsd.wait_for_attach(timeout = 30)
    print("Debugger attached...")
    ```

1. Během ladění možná budete chtít provést změny v souborech v imagi, aniž byste je museli znovu vytvářet. Pokud chcete v imagi Docker nainstalovat textový editor (vim), vytvořte nový textový soubor s názvem `Dockerfile.steps` a jako obsah souboru použijte následující:

    ```text
    RUN apt-get update && apt-get -y install vim
    ```

    Textový editor umožňuje upravovat soubory v imagi Docker a testovat změny bez vytvoření nové image.

1. Pokud chcete vytvořit image, která používá soubor `Dockerfile.steps`, použijte při vytváření image parametr `docker_file`. Následující příklad ukazuje, jak to provést:

    > [!NOTE]
    > V tomto příkladu se předpokládá, že `ws` odkazuje na pracovní prostor Azure Machine Learning a že `model` je model nasazený. `myenv.yml` soubor obsahuje závislosti conda vytvořené v kroku 1.

    ```python
    from azureml.core.image import Image, ContainerImage
    image_config = ContainerImage.image_configuration(runtime= "python",
                                 execution_script="score.py",
                                 conda_file="myenv.yml",
                                 docker_file="Dockerfile.steps")

    image = Image.create(name = "myimage",
                     models = [model],
                     image_config = image_config, 
                     workspace = ws)
    # Print the location of the image in the repository
    print(image.image_location)
    ```

Po vytvoření bitové kopie se zobrazí umístění bitové kopie v registru. Umístění se podobá následujícímu textu:

```text
myregistry.azurecr.io/myimage:1
```

V tomto textovém příkladu je název registru `myregistry` a image má název `myimage`. Verze bitové kopie je `1`.

### <a name="download-the-image"></a>Stáhnout image

1. Otevřete příkazový řádek, terminál nebo jiné prostředí a pomocí následujícího příkazu [Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) proveďte ověření předplatného Azure, které obsahuje váš Azure Machine Learning pracovní prostor:

    ```azurecli
    az login
    ```

1. K ověření Azure Container Registry (ACR), který obsahuje vaši image, použijte následující příkaz. Nahradí `myregistry`, který se vrátil při registraci Image:

    ```azurecli
    az acr login --name myregistry
    ```

1. Pokud chcete stáhnout image do místního Docker, použijte následující příkaz. Nahraďte `myimagepath` umístěním vráceným při registraci Image:

    ```bash
    docker pull myimagepath
    ```

    Cesta k obrázku by měla být podobná `myregistry.azurecr.io/myimage:1`. Kde `myregistry` je váš registr, `myimage` je vaše image a `1` je verze image.

    > [!TIP]
    > Ověřování z předchozího kroku nekončí trvale. Pokud mezi příkazem pro ověřování a příkazem Pull počkáte dostatečně dlouho, obdržíte chybu ověřování. Pokud k tomu dojde, znovu proveďte ověření.

    Doba potřebná k dokončení stahování závisí na rychlosti připojení k Internetu. Během procesu se zobrazí stav stahování. Po dokončení stahování můžete pomocí příkazu `docker images` ověřit, zda bylo staženo.

1. Pro usnadnění práce s imagí použijte následující příkaz a přidejte značku. Nahraďte `myimagepath` hodnotou umístění z kroku 2.

    ```bash
    docker tag myimagepath debug:1
    ```

    V případě zbývajících kroků můžete jako `debug:1` použít místní obrázek místo hodnoty úplná cesta k imagi.

### <a name="debug-the-service"></a>Ladění služby

> [!TIP]
> Pokud nastavíte časový limit pro připojení PTVSD v souboru `score.py`, je nutné připojit VS Code k ladicí relaci před vypršením časového limitu. Spusťte VS Code, otevřete místní kopii `score.py`, nastavte zarážku a připravte ji, abyste mohli použít kroky v této části.
>
> Další informace o ladění a nastavení zarážek naleznete v tématu [ladění](https://code.visualstudio.com/Docs/editor/debugging).

1. Pokud chcete spustit kontejner Docker pomocí Image, použijte následující příkaz:

    ```bash
    docker run --rm --name debug -p 8000:5001 -p 5678:5678 debug:1
    ```

1. Pokud chcete připojit VS Code k PTVSD uvnitř kontejneru, otevřete VS Code a použijte klávesu F5 nebo vyberte __ladit__. Po zobrazení výzvy vyberte __Azure Machine Learning: konfigurace ladění Docker__ . Můžete také vybrat ikonu ladění z bočního panelu, __Azure Machine Learning: položku ladění Docker__ z rozevírací nabídky ladění a potom použít zelenou šipku pro připojení ladicího programu.

    ![Ikona ladění, tlačítko Spustit ladění a selektor konfigurace](media/how-to-troubleshoot-deployment/start-debugging.png)

V tomto okamžiku se VS Code připojí k PTVSD uvnitř kontejneru Docker a zastaví se na zarážce, kterou jste předtím nastavili. Nyní můžete krokovat kód při spuštění, zobrazit proměnné atd.

Další informace o použití VS Code k ladění Pythonu najdete v tématu [ladění kódu Pythonu](https://docs.microsoft.com/visualstudio/python/debugging-python-in-visual-studio?view=vs-2019).

<a id="editfiles"></a>
### <a name="modify-the-container-files"></a>Úprava souborů kontejneru

Chcete-li provést změny v souborech v imagi, můžete se připojit ke spuštěnému kontejneru a spustit prostředí bash. Odtud můžete použít systémem VIM k úpravě souborů:

1. Pokud se chcete připojit ke spuštěnému kontejneru a spustit prostředí bash v kontejneru, použijte následující příkaz:

    ```bash
    docker exec -it debug /bin/bash
    ```

1. Chcete-li najít soubory používané službou, použijte následující příkaz z prostředí bash v kontejneru:

    ```bash
    cd /var/azureml-app
    ```

    Odsud můžete soubor `score.py` upravit pomocí služby Vim. Další informace o používání systému vim najdete v tématu [použití Editoru systému vim](https://www.tldp.org/LDP/intro-linux/html/sect_06_02.html).

1. Změny v kontejneru nejsou obvykle trvalé. Pokud chcete uložit provedené změny, použijte následující příkaz před ukončením prostředí spuštěného v předchozím kroku (tj. v jiném prostředí):

    ```bash
    docker commit debug debug:2
    ```

    Tento příkaz vytvoří nový obrázek s názvem `debug:2`, který obsahuje vaše úpravy.

    > [!TIP]
    > Budete muset zastavit aktuální kontejner a začít používat novou verzi, než se změny projeví.

1. Zajistěte, aby byly změny provedené v souborech v kontejneru synchronizovány s místními soubory, které VS Code používá. V opačném případě nebude prostředí ladicího programu fungovat podle očekávání.

### <a name="stop-the-container"></a>Zastavení kontejneru

Chcete-li zastavit kontejner, použijte následující příkaz:

```bash
docker stop debug
```

## <a name="next-steps"></a>Další kroky

Další informace o nasazení:

* [Jak nasadit a kde](how-to-deploy-and-where.md)
* [Kurz: výuka & nasazení modelů](tutorial-train-models-with-aml.md)
