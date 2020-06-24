---
title: Řešení potíží s nasazením Docker
titleSuffix: Azure Machine Learning
description: Naučte se řešit, řešit a řešit běžné chyby nasazení Docker pomocí služby Azure Kubernetes a Azure Container Instances pomocí Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: troubleshooting
author: clauren42
ms.author: clauren
ms.reviewer: jmartens
ms.date: 03/05/2020
ms.custom: contperfq4, tracking-python
ms.openlocfilehash: 13ce9204ad09d2ecb4b149cf50696aa73d927314
ms.sourcegitcommit: 6fd28c1e5cf6872fb28691c7dd307a5e4bc71228
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2020
ms.locfileid: "85214362"
---
# <a name="troubleshoot-docker-deployment-of-models-with-azure-kubernetes-service-and-azure-container-instances"></a>Řešení potíží s nasazením v Docker modelů pomocí služby Azure Kubernetes a Azure Container Instances 

Přečtěte si, jak řešit a řešit běžné chyby nasazení Docker pomocí Azure Container Instances (ACI) a Azure Kubernetes Service (AKS) pomocí Azure Machine Learning.

## <a name="prerequisites"></a>Požadavky

* **Předplatné Azure**. Pokud ho nemáte, vyzkoušejte [bezplatnou nebo placená verzi Azure Machine Learning](https://aka.ms/AMLFree).
* [Sada Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py).
* Rozhraní příkazového [řádku Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)
* [Rozšíření CLI pro Azure Machine Learning](reference-azure-machine-learning-cli.md).
* Chcete-li ladit místně, je nutné mít v místním systému funkční instalaci Docker.

    K ověření instalace Docker použijte příkaz `docker run hello-world` z terminálu nebo příkazového řádku. Informace o instalaci Docker nebo odstraňování potíží s chybami Docker najdete v [dokumentaci k Docker](https://docs.docker.com/).

## <a name="steps-for-docker-deployment-of-machine-learning-models"></a>Postup pro nasazení Docker modelů strojového učení

Při nasazování modelu v Azure Machine Learning systém provádí řadu úloh.

Doporučený přístup k nasazení modelu je prostřednictvím rozhraní API [modelu. deploy ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model%28class%29?view=azure-ml-py#deploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-) pomocí objektu [prostředí](how-to-use-environments.md) jako vstupní parametr. V tomto případě služba vytvoří během fáze nasazení základní image Docker a všechny požadované modely připojí v jednom volání. Základní úlohy nasazení:

1. Zaregistrujte model v registru modelu pracovního prostoru.

2. Definovat odvozenou konfiguraci:
    1. Vytvořte objekt [prostředí](how-to-use-environments.md) na základě závislostí, které zadáte v souboru YAML prostředí, nebo použijte jedno z našich pořízených prostředí.
    2. Vytvořte odvozenou konfiguraci (objekt InferenceConfig) na základě prostředí a hodnoticího skriptu.

3. Nasaďte model do služby Azure Container instance (ACI) nebo do služby Azure Kubernetes Service (AKS).

Další informace o tomto procesu najdete v úvodu [Správa modelů](concept-model-management-and-deployment.md) .

## <a name="before-you-begin"></a>Než začnete

Pokud narazíte na problém, je první věc, kterou je potřeba udělat, rozdělte úlohu nasazení (popsanou výše) do jednotlivých kroků k izolaci problému.

Za předpokladu, že používáte novou/doporučenou metodu nasazení prostřednictvím rozhraní API [model. deploy ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model%28class%29?view=azure-ml-py#deploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-) s objektem [prostředí](how-to-use-environments.md) jako vstupní parametr, váš kód může být rozdělen na tři hlavní kroky:

1. Zaregistrujte model. Zde je ukázkový kód:

    ```python
    from azureml.core.model import Model


    # register a model out of a run record
    model = best_run.register_model(model_name='my_best_model', model_path='outputs/my_model.pkl')

    # or, you can register a file or a folder of files as a model
    model = Model.register(model_path='my_model.pkl', model_name='my_best_model', workspace=ws)
    ```

2. Definovat odvozenou konfiguraci pro nasazení:

    ```python
    from azureml.core.model import InferenceConfig
    from azureml.core.environment import Environment


    # create inference configuration based on the requirements defined in the YAML
    myenv = Environment.from_conda_specification(name="myenv", file_path="myenv.yml")
    inference_config = InferenceConfig(entry_script="score.py", environment=myenv)
    ```

3. Nasaďte model pomocí konfigurace odvození vytvořeného v předchozím kroku:

    ```python
    from azureml.core.webservice import AciWebservice


    # deploy the model
    aci_config = AciWebservice.deploy_configuration(cpu_cores=1, memory_gb=1)
    aci_service = Model.deploy(workspace=ws,
                           name='my-service',
                           models=[model],
                           inference_config=inference_config,
                           deployment_config=aci_config)
    aci_service.wait_for_deployment(show_output=True)
    ```

Po rozlomení procesu nasazení na jednotlivé úlohy si můžeme prohlédnout některé z nejběžnějších chyb.

## <a name="debug-locally"></a>Místní ladění

Pokud narazíte na problémy s nasazením modelu do ACI nebo AKS, zkuste ho nasadit jako místní webovou službu. Použití místní webové služby usnadňuje řešení problémů. Image Docker obsahující model se stáhne a spustí v místním systému.

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

Pokud definujete vlastní conda Specification YAML, je nutné jako závislost PIP vytvořit seznam AzureML-Defaults s Version >= 1.0.45. Tento balíček obsahuje funkce potřebné pro hostování modelu jako webové služby.

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

Další informace o přizpůsobení prostředí Pythonu najdete v tématu [vytváření a Správa prostředí pro školení a nasazení](how-to-use-environments.md). 

### <a name="update-the-service"></a>Aktualizace služby

Během místního testování možná budete muset soubor aktualizovat, `score.py` aby se přidalo protokolování nebo pokus o vyřešení všech zjištěných problémů. Chcete-li znovu načíst změny v `score.py` souboru, použijte `reload()` . Například následující kód znovu načte skript pro službu a poté do něj pošle data. Data jsou hodnocena pomocí aktualizovaného `score.py` souboru:

> [!IMPORTANT]
> `reload`Metoda je k dispozici pouze pro místní nasazení. Informace o aktualizaci nasazení na jiný cíl služby COMPUTE najdete v části aktualizace v tématu [nasazení modelů](how-to-deploy-and-where.md#update).

```python
service.reload()
print(service.run(input_data=test_sample))
```

> [!NOTE]
> Skript se znovu načte z umístění zadaného `InferenceConfig` objektem, který služba používá.

Chcete-li změnit model, závislosti conda nebo konfiguraci nasazení, použijte [Update ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice%28class%29?view=azure-ml-py#update--args-). Následující příklad aktualizuje model používaný službou:

```python
service.update([different_model], inference_config, deployment_config)
```

### <a name="delete-the-service"></a>Odstranit službu

Pokud chcete službu odstranit, použijte [Delete ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice%28class%29?view=azure-ml-py#delete--).

### <a name="inspect-the-docker-log"></a><a id="dockerlog"></a>Kontrola protokolu Docker

Podrobnou zprávu protokolu modulu Docker můžete vytisknout z objektu služby. Můžete zobrazit protokol pro ACI, AKS a místní nasazení. Následující příklad ukazuje, jak tisknout protokoly.

```python
# if you already have the service object handy
print(service.get_logs())

# if you only know the name of the service (note there might be multiple services with the same name but different version number)
print(ws.webservices['mysvc'].get_logs())
```
## <a name="container-cannot-be-scheduled"></a>Kontejner nelze naplánovat.

Když nasadíte službu do cíle výpočetního cíle služby Azure Kubernetes, Azure Machine Learning se pokusí naplánovat službu s požadovaným množstvím prostředků. Pokud po 5 minutách nejsou v clusteru k dispozici žádné uzly s příslušným množstvím dostupných prostředků, nasazení se nezdaří a zobrazí se zpráva `Couldn't Schedule because the kubernetes cluster didn't have available resources after trying for 00:05:00` . Tuto chybu můžete vyřešit přidáním dalších uzlů, změnou SKU vašich uzlů nebo změnou požadavků na prostředky vaší služby. 

Chybová zpráva obvykle indikuje, který prostředek potřebujete více, pokud se zobrazí chybová zpráva oznamující `0/3 nodes are available: 3 Insufficient nvidia.com/gpu` , že služba vyžaduje GPU a v clusteru jsou tři uzly, které nemají k dispozici GPU. To se dá řešit přidáním dalších uzlů, pokud používáte SKU GPU, přechodem na SKU s povoleným GPU, pokud nechcete nebo neměníte prostředí, aby nevyžadovalo GPU.  

## <a name="service-launch-fails"></a>Spuštění služby se nezdařilo.

Po úspěšném vytvoření image se systém pokusí spustit kontejner pomocí konfigurace nasazení. V rámci procesu spuštění kontejneru `init()` je funkce ve vašem skriptu bodování vyvolána systémem. Pokud ve funkci nejsou zachycené výjimky `init()` , může se zobrazit chyba **CrashLoopBackOff** v chybové zprávě.

Použijte informace v části Kontrola [protokolu Docker](#dockerlog) pro kontrolu protokolů.

## <a name="function-fails-get_model_path"></a>Funkce se nezdařila: get_model_path ()

Často se ve `init()` funkci skriptu bodování volá funkce [Model. get_model_path ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#get-model-path-model-name--version-none---workspace-none-) pro vyhledání souboru modelu nebo složky souborů modelu v kontejneru. Pokud soubor modelu nebo složku nelze nalézt, funkce se nezdařila. Nejjednodušší způsob, jak tuto chybu ladit, je spuštění níže uvedeného kódu Pythonu v prostředí kontejneru:

```python
from azureml.core.model import Model
import logging
logging.basicConfig(level=logging.DEBUG)
print(Model.get_model_path(model_name='my-best-model'))
```

Tento příklad vytiskne místní cestu (vzhledem k `/var/azureml-app` ) v kontejneru, ve kterém váš skript bodování očekává nalezení souboru modelu nebo složky. Pak můžete ověřit, jestli je soubor nebo složka skutečně tam, kde se očekává.

Nastavení úrovně protokolování na ladění může způsobit, že budou protokolovány Další informace, které mohou být užitečné při identifikaci selhání.

## <a name="function-fails-runinput_data"></a>Neúspěšná funkce: Run (input_data)

Pokud se služba úspěšně nasadila, ale dojde k chybě při odesílání dat do koncového bodu, můžete do funkce Přidat příkaz pro zachycení chyb, `run(input_data)` aby se místo toho vrátila podrobná chybová zpráva. Příklad:

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

## <a name="http-status-code-502"></a>Stavový kód HTTP 502

Stavový kód 502 označuje, že služba vyvolala výjimku nebo došlo k chybě v `run()` metodě souboru Score.py. K ladění souboru použijte informace v tomto článku.

## <a name="http-status-code-503"></a>Stavový kód HTTP 503

Nasazení služby Azure Kubernetes podporují automatické škálování, které umožňuje přidat repliky pro podporu dalšího zatížení. Automatické škálování je však navrženo pro zpracování **postupných** změn v zatížení. Pokud v požadavcích za sekundu obdržíte velké špičky, mohou klienti obdržet stavový kód HTTP 503.

K dispozici jsou dvě věci, které vám pomůžou zabránit stavovým kódům 503:

* Změňte úroveň využití, při které automatické škálování vytvoří nové repliky.
    
    Ve výchozím nastavení je cílení na automatické škálování nastaveno na 70%, což znamená, že služba dokáže zpracovávat špičky v požadavcích za sekundu (RPS) až o 30%. Cíl využití můžete upravit nastavením `autoscale_target_utilization` na nižší hodnotu.

    > [!IMPORTANT]
    > Tato změna nezpůsobí *rychlejší*vytváření replik. Místo toho jsou vytvořeny s nižší prahovou hodnotou využití. Místo čekání na vyčerpání služby 70% se změna hodnoty na 30% způsobí, že se repliky vytvoří, když dojde k 30% využití.
    
    Pokud už webová služba používá aktuální maximální počet replik a stále se zobrazuje stavové kódy 503, zvyšte `autoscale_max_replicas` hodnotu, aby se zvýšil maximální počet replik.

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

Další informace o nastavení `autoscale_target_utilization` , `autoscale_max_replicas` a `autoscale_min_replicas` pro naleznete v tématu Reference k modulu [AksWebservice](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.akswebservice?view=azure-ml-py) .

## <a name="http-status-code-504"></a>Stavový kód HTTP 504

Stavový kód 504 označuje, že vypršel časový limit žádosti. Výchozí časový limit je 1 minuta.

Časový limit můžete zvýšit nebo se pokusit o urychlení služby úpravou score.py pro odebrání nepotřebných volání. Pokud tyto akce problém nevyřeší, použijte informace v tomto článku k ladění souboru score.py. Kód může být v nereagující stavu nebo nekonečné smyčce.

## <a name="advanced-debugging"></a>Pokročilé ladění

V některých případech možná budete muset interaktivně ladit kód Pythonu obsažený v nasazení modelu. Například pokud se skript vstupu nezdařil a důvod nelze určit pomocí dalšího protokolování. Pomocí Visual Studio Code a Python Tools for Visual Studio (PTVSD) se můžete připojit ke kódu běžícímu uvnitř kontejneru Docker.

> [!IMPORTANT]
> Tato metoda ladění nefunguje při použití `Model.deploy()` a `LocalWebservice.deploy_configuration` k nasazení modelu místně. Místo toho je nutné vytvořit bitovou kopii pomocí metody [model. Package ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#package-workspace--models--inference-config-none--generate-dockerfile-false-) .

Nasazení místních webových služeb vyžaduje pracovní instalaci do dokovacího prostředí v místním systému. Další informace o používání Docker najdete v [dokumentaci k Docker](https://docs.docker.com/).

### <a name="configure-development-environment"></a>Konfigurace vývojového prostředí

1. Chcete-li nainstalovat Python Tools for Visual Studio (PTVSD) do místního vývojového prostředí VS Code, použijte následující příkaz:

    ```
    python -m pip install --upgrade ptvsd
    ```

    Další informace o použití PTVSD s VS Code najdete v tématu [vzdálené ladění](https://code.visualstudio.com/docs/python/debugging#_remote-debugging).

1. Pokud chcete nakonfigurovat VS Code ke komunikaci s imagí Docker, vytvořte novou konfiguraci ladění:

    1. Z VS Code vyberte nabídku __ladění__ a pak vyberte __otevřít konfigurace__. Soubor s názvem __launch.jspři__ otevření.

    1. V __launch.jsv__ souboru vyhledejte řádek, který obsahuje `"configurations": [` , a vložte za něj následující text:

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

    1. Uložte __launch.jsdo__ souboru.

### <a name="create-an-image-that-includes-ptvsd"></a>Vytvoření image, která zahrnuje PTVSD

1. Upravte prostředí conda pro nasazení tak, aby zahrnovalo PTVSD. Následující příklad ukazuje přidání pomocí `pip_packages` parametru:

    ```python
    from azureml.core.conda_dependencies import CondaDependencies 


    # Usually a good idea to choose specific version numbers
    # so training is made on same packages as scoring
    myenv = CondaDependencies.create(conda_packages=['numpy==1.15.4',            
                                'scikit-learn==0.19.1', 'pandas==0.23.4'],
                                 pip_packages = ['azureml-defaults==1.0.45', 'ptvsd'])

    with open("myenv.yml","w") as f:
        f.write(myenv.serialize_to_string())
    ```

1. Pokud chcete začít PTVSD a po spuštění služby počkat na připojení, přidejte na začátek `score.py` souboru následující:

    ```python
    import ptvsd
    # Allows other computers to attach to ptvsd on this IP address and port.
    ptvsd.enable_attach(address=('0.0.0.0', 5678), redirect_output = True)
    # Wait 30 seconds for a debugger to attach. If none attaches, the script continues as normal.
    ptvsd.wait_for_attach(timeout = 30)
    print("Debugger attached...")
    ```

1. Vytvořte bitovou kopii založenou na definici prostředí a přetáhnout image do místního registru. Během ladění možná budete chtít provést změny v souborech v imagi, aniž byste je museli znovu vytvářet. Chcete-li nainstalovat textový editor (vim) v imagi Docker, použijte `Environment.docker.base_image` vlastnosti a `Environment.docker.base_dockerfile` :

    > [!NOTE]
    > V tomto příkladu se předpokládá, že `ws` odkazuje na váš pracovní prostor Azure Machine Learning a který `model` je modelem, který je nasazený. `myenv.yml`Soubor obsahuje závislosti conda vytvořené v kroku 1.

    ```python
    from azureml.core.conda_dependencies import CondaDependencies
    from azureml.core.model import InferenceConfig
    from azureml.core.environment import Environment


    myenv = Environment.from_conda_specification(name="env", file_path="myenv.yml")
    myenv.docker.base_image = None
    myenv.docker.base_dockerfile = "FROM mcr.microsoft.com/azureml/base:intelmpi2018.3-ubuntu16.04\nRUN apt-get update && apt-get install vim -y"
    inference_config = InferenceConfig(entry_script="score.py", environment=myenv)
    package = Model.package(ws, [model], inference_config)
    package.wait_for_creation(show_output=True)  # Or show_output=False to hide the Docker build logs.
    package.pull()
    ```

    Po vytvoření a stažení Image se zobrazí cesta k imagi (včetně úložiště, názvu a značky, která v tomto případě je také její výtah), a to podobně jako v následující zprávě:

    ```text
    Status: Downloaded newer image for myregistry.azurecr.io/package@sha256:<image-digest>
    ```

1. Pro usnadnění práce s imagí použijte následující příkaz a přidejte značku. Nahraďte `myimagepath` hodnotou umístění z předchozího kroku.

    ```bash
    docker tag myimagepath debug:1
    ```

    V případě zbývajících kroků můžete `debug:1` místo hodnoty úplná cesta k imagi použít místní obrázek.

### <a name="debug-the-service"></a>Ladění služby

> [!TIP]
> Pokud nastavíte časový limit pro připojení PTVSD v `score.py` souboru, je nutné před vypršením časového limitu připojit vs Code k ladicí relaci. Spusťte VS Code, otevřete místní kopii `score.py` , nastavte zarážku a před použitím kroků v této části Připravte ji na přechod.
>
> Další informace o ladění a nastavení zarážek naleznete v tématu [ladění](https://code.visualstudio.com/Docs/editor/debugging).

1. Pokud chcete spustit kontejner Docker pomocí Image, použijte následující příkaz:

    ```bash
    docker run --rm --name debug -p 8000:5001 -p 5678:5678 debug:1
    ```

1. Pokud chcete připojit VS Code k PTVSD uvnitř kontejneru, otevřete VS Code a použijte klávesu F5 nebo vyberte __ladit__. Po zobrazení výzvy vyberte __Azure Machine Learning: konfigurace ladění Docker__ . Můžete také vybrat ikonu ladění z bočního panelu, __Azure Machine Learning: položku ladění Docker__ z rozevírací nabídky ladění a potom použít zelenou šipku pro připojení ladicího programu.

    ![Ikona ladění, tlačítko Spustit ladění a selektor konfigurace](./media/how-to-troubleshoot-deployment/start-debugging.png)

V tomto okamžiku se VS Code připojí k PTVSD uvnitř kontejneru Docker a zastaví se na zarážce, kterou jste předtím nastavili. Nyní můžete krokovat kód při spuštění, zobrazit proměnné atd.

Další informace o použití VS Code k ladění Pythonu najdete v tématu [ladění kódu Pythonu](https://docs.microsoft.com/visualstudio/python/debugging-python-in-visual-studio?view=vs-2019).

<a id="editfiles"></a>
### <a name="modify-the-container-files"></a>Úprava souborů kontejneru

Chcete-li provést změny v souborech v imagi, můžete se připojit ke spuštěnému kontejneru a spustit prostředí bash. Odtud můžete použít systémem VIM k úpravě souborů:

1. Pokud se chcete připojit ke spuštěnému kontejneru a spustit prostředí bash v kontejneru, použijte následující příkaz:

    ```bash
    docker exec -it debug /bin/bash
    ```

1. Chcete-li najít soubory používané službou, použijte následující příkaz z prostředí bash v kontejneru, pokud je výchozí adresář jiný než `/var/azureml-app` :

    ```bash
    cd /var/azureml-app
    ```

    Odsud můžete soubor upravit pomocí služby vim `score.py` . Další informace o používání systému vim najdete v tématu [použití Editoru systému vim](https://www.tldp.org/LDP/intro-linux/html/sect_06_02.html).

1. Změny v kontejneru nejsou obvykle trvalé. Pokud chcete uložit provedené změny, použijte následující příkaz před ukončením prostředí spuštěného v předchozím kroku (tj. v jiném prostředí):

    ```bash
    docker commit debug debug:2
    ```

    Tento příkaz vytvoří novou image s názvem `debug:2` , která obsahuje vaše úpravy.

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
