---
title: Průvodce řešením problémů s nasazením
titleSuffix: Azure Machine Learning
description: Zjistěte, jak pomocí Azure Machine Learning vyřešit, vyřešit a řešit běžné chyby nasazení Dockeru pomocí služby Azure Kubernetes Service a Azure Container Instances.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: clauren42
ms.author: clauren
ms.reviewer: jmartens
ms.date: 03/05/2020
ms.custom: seodec18
ms.openlocfilehash: fab46f7d7ae74ad643ce3f122b27b0dc767f5a78
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78399681"
---
# <a name="troubleshooting-azure-machine-learning-azure-kubernetes-service-and-azure-container-instances-deployment"></a>Řešení potíží s nasazením služby Azure Machine Learning Azure Kubernetes Service a Azure Container Instances

Zjistěte, jak pomocí Azure Machine Learning vyřešit nebo řešit běžné chyby nasazení Dockeru pomocí instancí Azure Container Instances (ACI) a Azure Kubernetes Service (AKS).

Při nasazování modelu v Azure Machine Learning, systém provádí řadu úkolů.

Doporučený a nejaktuálnější přístup pro nasazení modelu je prostřednictvím [rozhraní API Model.deploy()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model%28class%29?view=azure-ml-py#deploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-) pomocí objektu [Prostředí](https://docs.microsoft.com/azure/machine-learning/service/how-to-use-environments) jako vstupní parametr. V takovém případě naše služba vytvoří základní image dockeru pro vás během fáze nasazení a připojit požadované modely vše v jednom volání. Základní úlohy nasazení jsou:

1. Zaregistrujte model v registru modelu pracovního prostoru.

2. Definujte konfiguraci odvození:
    1. Vytvořte objekt [prostředí](https://docs.microsoft.com/azure/machine-learning/service/how-to-use-environments) na základě závislostí, které zadáte v souboru yaml prostředí nebo použijte jedno z našich obstarávaných prostředí.
    2. Vytvořte konfiguraci odvození (objekt InferenceConfig) na základě prostředí a bodovacího skriptu.

3. Nasazení modelu do služby Azure Container Instance (ACI) nebo do služby Azure Kubernetes Service (AKS).

Další informace o tomto procesu naleznete v úvodu [správy modelů.](concept-model-management-and-deployment.md)

## <a name="prerequisites"></a>Požadavky

* Předplatné **Azure**. Pokud ho nemáte, vyzkoušejte [bezplatnou nebo placenou verzi Azure Machine Learning](https://aka.ms/AMLFree).
* Azure [Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py).
* [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).
* [Rozšíření cli pro Azure Machine Learning](reference-azure-machine-learning-cli.md).
* Chcete-li ladit místně, musíte mít pracovní instalaci Dockeru v místním systému.

    Chcete-li ověřit instalaci dockeru, použijte příkaz `docker run hello-world` z terminálu nebo příkazového řádku. Informace o instalaci Dockeru nebo řešení potíží s chybami Dockeru najdete v tématu [Dokumentace k Dockeru](https://docs.docker.com/).

## <a name="before-you-begin"></a>Než začnete

Pokud narazíte na jakýkoli problém, první věc, kterou musíte udělat, je rozdělit úlohu nasazení (dříve popsané) do jednotlivých kroků k izolování problému.

Za předpokladu, že používáte novou/doporučenou metodu nasazení prostřednictvím rozhraní [API Model.deploy()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model%28class%29?view=azure-ml-py#deploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-) s objektem [prostředí](https://docs.microsoft.com/azure/machine-learning/service/how-to-use-environments) jako vstupním parametrem, může být váš kód rozdělen do tří hlavních kroků:

1. Zaregistrujte model. Zde je několik ukázkový kód:

    ```python
    from azureml.core.model import Model


    # register a model out of a run record
    model = best_run.register_model(model_name='my_best_model', model_path='outputs/my_model.pkl')

    # or, you can register a file or a folder of files as a model
    model = Model.register(model_path='my_model.pkl', model_name='my_best_model', workspace=ws)
    ```

2. Definujte konfiguraci odvození pro nasazení:

    ```python
    from azureml.core.model import InferenceConfig
    from azureml.core.environment import Environment


    # create inference configuration based on the requirements defined in the YAML
    myenv = Environment.from_conda_specification(name="myenv", file_path="myenv.yml")
    inference_config = InferenceConfig(entry_script="score.py", environment=myenv)
    ```

3. Nasazení modelu pomocí konfigurace odvození vytvořené v předchozím kroku:

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

Jakmile rozdělíte proces nasazení na jednotlivé úkoly, můžeme se podívat na některé z nejběžnějších chyb.

## <a name="debug-locally"></a>Ladění místně

Pokud narazíte na problémy s nasazením modelu do ACI nebo AKS, zkuste jej nasadit jako místní webovou službu. Použití místní webové služby usnadňuje řešení problémů. Image Dockerobsahující model je stažena a spuštěna v místním systému.

> [!WARNING]
> Nasazení místních webových služeb nejsou podporovány pro produkční scénáře.

Chcete-li nasadit místně, `LocalWebservice.deploy_configuration()` upravte kód, který chcete použít k vytvoření konfigurace nasazení. Potom `Model.deploy()` použijte k nasazení služby. Následující příklad nasazuje model (obsažený v proměnné modelu) jako místní webovou službu:

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

Vezměte prosím na vědomí, že pokud definujete vlastní conda specifikace YAML, musíte uvést azureml-defaults s verzí >= 1.0.45 jako pip závislost. Tento balíček obsahuje funkce potřebné k hostování modelu jako webové služby.

V tomto okamžiku můžete pracovat se službou jako obvykle. Například následující kód ukazuje odesílání dat do služby:

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

Další informace o přizpůsobení prostředí Pythonu najdete v [tématu Vytváření a správa prostředí pro školení a nasazení](how-to-use-environments.md). 

### <a name="update-the-service"></a>Aktualizace služby

Během místního testování může být `score.py` nutné aktualizovat soubor, chcete-li přidat protokolování nebo se pokusit vyřešit všechny zjištěné problémy. Chcete-li znovu `score.py` načíst `reload()`změny souboru, použijte . Například následující kód znovu načte skript pro službu a odešle data do něj. Data jsou hodnocena pomocí `score.py` aktualizovaného souboru:

> [!IMPORTANT]
> Metoda `reload` je k dispozici pouze pro místní nasazení. Informace o aktualizaci nasazení na jiný výpočetní cíl najdete v části aktualizace [nasazení modelů](how-to-deploy-and-where.md#update).

```python
service.reload()
print(service.run(input_data=test_sample))
```

> [!NOTE]
> Skript je znovu načten z umístění `InferenceConfig` určeného objektem používaným službou.

Chcete-li změnit model, conda závislosti nebo konfigurace nasazení, použijte [update()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice%28class%29?view=azure-ml-py#update--args-). Následující příklad aktualizuje model používaný službou:

```python
service.update([different_model], inference_config, deployment_config)
```

### <a name="delete-the-service"></a>Odstranit službu

Chcete-li službu odstranit, použijte [delete()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice%28class%29?view=azure-ml-py#delete--).

### <a name="inspect-the-docker-log"></a><a id="dockerlog"></a>Kontrola protokolu Dockeru

Můžete vytisknout podrobné zprávy protokolu modulu Docker z objektu služby. Můžete zobrazit protokol pro nasazení ACI, AKS a Místní. Následující příklad ukazuje, jak vytisknout protokoly.

```python
# if you already have the service object handy
print(service.get_logs())

# if you only know the name of the service (note there might be multiple services with the same name but different version number)
print(ws.webservices['mysvc'].get_logs())
```

## <a name="service-launch-fails"></a>Spuštění služby se nezdaří.

Po úspěšném vytvoření bitové kopie se systém pokusí spustit kontejner pomocí konfigurace nasazení. Jako součást procesu spuštění kontejneru funkce ve skriptu `init()` bodování je vyvolána systémem. Pokud jsou nezachycené výjimky ve `init()` funkci, může se zobrazit **CrashLoopBackOff** chyba v chybové zprávě.

Pomocí informací v části [Kontrola protokolu Dockeru](#dockerlog) zkontrolujte protokoly.

## <a name="function-fails-get_model_path"></a>Funkce se nezdaří: get_model_path()

Funkce `init()` [Model.get_model_path()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#get-model-path-model-name--version-none---workspace-none-) je často volána ve funkci ve skriptu hodnocení, která vyhledává soubor modelu nebo složku souborů modelu v kontejneru. Pokud soubor modelu nebo složku nelze najít, funkce se nezdaří. Nejjednodušší způsob, jak ladit tuto chybu, je spustit níže uvedený kód Pythonu v prostředí Container:

```python
from azureml.core.model import Model
import logging
logging.basicConfig(level=logging.DEBUG)
print(Model.get_model_path(model_name='my-best-model'))
```

Tento příklad vytiskne místní `/var/azureml-app`cestu (vzhledem k ) v kontejneru, kde je bodovací skript očekává, že najít soubor modelu nebo složku. Pak můžete ověřit, zda je soubor nebo složka skutečně tam, kde se očekává.

Nastavení úrovně protokolování na debug může způsobit další informace, které mají být zaznamenány, což může být užitečné při identifikaci selhání.

## <a name="function-fails-runinput_data"></a>Funkce se nezdaří: run(input_data)

Pokud je služba úspěšně nasazena, ale dojde k chybě při zaúčtování dat do koncového `run(input_data)` bodu vyhodnocování, můžete přidat příkaz zachycení chyby ve vaší funkci tak, aby místo toho vrátí podrobnou chybovou zprávu. Například:

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

**Poznámka:** Vrácení chybových `run(input_data)` zpráv z volání by mělo být provedeno pouze pro účely ladění. Z bezpečnostních důvodů byste neměli vracet chybové zprávy tímto způsobem v provozním prostředí.

## <a name="http-status-code-502"></a>Stavový kód HTTP 502

Stavový kód 502 označuje, že služba vyvolala výjimku nebo došlo k `run()` chybě v metodě score.py souboru. Informace v tomto článku slouží k ladění souboru.

## <a name="http-status-code-503"></a>Stavový kód HTTP 503

Nasazení služby Azure Kubernetes podporují automatické škálování, které umožňuje přidat repliky pro podporu dalšího zatížení. Autoscaler je však navržen tak, aby **zvládal postupné** změny zatížení. Pokud obdržíte velké špičky v požadavcích za sekundu, klienti mohou obdržet stavový kód HTTP 503.

Existují dvě věci, které mohou pomoci zabránit 503 stavové kódy:

* Změňte úroveň využití, při které automatické škálování vytváří nové repliky.
    
    Ve výchozím nastavení je využití cíle automatického škálování nastaveno na 70 %, což znamená, že služba dokáže zpracovat špičky v požadavcích za sekundu (RPS) až o 30 %. Cíl využití můžete upravit nastavením `autoscale_target_utilization` na nižší hodnotu.

    > [!IMPORTANT]
    > Tato změna nezpůsobí, že repliky budou vytvořeny *rychleji*. Místo toho jsou vytvořeny s nižší prahovou hodnotou využití. Namísto čekání na službu 70 % využita, změna hodnoty na 30 % způsobí, že repliky budou vytvořeny, když dojde k využití 30 %.
    
    Pokud webová služba již používá aktuální maximální repliky a stále se zobrazují `autoscale_max_replicas` stavové kódy 503, zvyšte hodnotu a zvyšte maximální počet replik.

* Změňte minimální počet replik. Zvýšení minimální repliky poskytuje větší fond pro zpracování příchozí špičky.

    Chcete-li zvýšit minimální počet `autoscale_min_replicas` replik, nastavte na vyšší hodnotu. Požadované repliky můžete vypočítat pomocí následujícího kódu, který nahradí hodnoty hodnotami specifickými pro váš projekt:

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
    > Pokud obdržíte špičky požadavku větší než nové minimální repliky může zpracovat, můžete obdržet 503s znovu. Například jako provoz na službu zvyšuje, může být nutné zvýšit minimální repliky.

Další informace o `autoscale_target_utilization` `autoscale_max_replicas`nastavení `autoscale_min_replicas` , a pro naleznete v odkazu na modul [AksWebservice.](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.akswebservice?view=azure-ml-py)

## <a name="http-status-code-504"></a>Stavový kód HTTP 504

Stavový kód 504 označuje, že časový čas požadavku byl vybit. Výchozí časový limit je 1 minuta.

Můžete zvýšit časový čas nebo se pokusit urychlit službu úpravou score.py odebrat nepotřebná volání. Pokud tyto akce problém nevyřeší, použijte informace v tomto článku k ladění score.py souboru. Kód může být v stavu hung nebo nekonečné smyčky.

## <a name="advanced-debugging"></a>Pokročilé ladění

V některých případech může být nutné interaktivně ladit kód Pythonu obsažený v nasazení modelu. Například pokud vstupní skript selhává a důvod nelze určit dalším protokolováním. Pomocí kódu Sady Visual Studio a nástrojů Pythonu pro Visual Studio (PTVSD) můžete připojit ke kódu spuštěnému uvnitř kontejneru Dockeru.

> [!IMPORTANT]
> Tato metoda ladění nefunguje při použití `Model.deploy()` `LocalWebservice.deploy_configuration` a nasazení modelu místně. Místo toho je nutné vytvořit bitovou kopii pomocí [Metody Model.package().](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#package-workspace--models--inference-config-none--generate-dockerfile-false-)

Nasazení místních webových služeb vyžadují funkční instalaci Dockeru v místním systému. Další informace o používání Dockeru najdete v [dokumentaci Dockeru](https://docs.docker.com/).

### <a name="configure-development-environment"></a>Konfigurace vývojového prostředí

1. Chcete-li nainstalovat nástroje Pythonu pro Visual Studio (PTVSD) do místního vývojového prostředí VS Code, použijte následující příkaz:

    ```
    python -m pip install --upgrade ptvsd
    ```

    Další informace o používání ptvsd s kódem VS naleznete [v tématu Vzdálené ladění](https://code.visualstudio.com/docs/python/debugging#_remote-debugging).

1. Chcete-li nakonfigurovat kód VS pro komunikaci s bitovou stránkou Dockeru, vytvořte novou konfiguraci ladění:

    1. V nabídce VS Code vyberte nabídku __Ladění__ a pak __vyberte Otevřít konfigurace__. Otevře se soubor s názvem __launch.json.__

    1. V souboru __launch.json__ vyhledejte `"configurations": [`řádek, který obsahuje , a za dejte za něj následující text:

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
        > Pokud již existují další položky v části konfigurace, přidejte čárku (,) za kód, který jste vložili.

        Tato část se připojuje ke kontejneru Dockerpomocí portu 5678.

    1. Uložte soubor __launch.json.__

### <a name="create-an-image-that-includes-ptvsd"></a>Vytvoření obrázku, který obsahuje PTVSD

1. Upravte prostředí conda pro vaše nasazení tak, aby zahrnovala PTVSD. Následující příklad ukazuje přidání pomocí `pip_packages` parametru:

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

1. Chcete-li spustit bod PTVSD a čekat na připojení při spuštění `score.py` služby, přidejte do horní části souboru následující:

    ```python
    import ptvsd
    # Allows other computers to attach to ptvsd on this IP address and port.
    ptvsd.enable_attach(address=('0.0.0.0', 5678), redirect_output = True)
    # Wait 30 seconds for a debugger to attach. If none attaches, the script continues as normal.
    ptvsd.wait_for_attach(timeout = 30)
    print("Debugger attached...")
    ```

1. Vytvořte bitovou kopii na základě definice prostředí a nakreslete bitovou kopii do místního registru. Během ladění můžete chtít provést změny souborů v obraze, aniž byste je museli znovu vytvořit. Chcete-li nainstalovat textový editor (vim) v `Environment.docker.base_image` `Environment.docker.base_dockerfile` bitové kopii Dockeru, použijte vlastnosti a:

    > [!NOTE]
    > Tento příklad předpokládá, že `ws` odkazuje na pracovní prostor `model` Azure Machine Learning a to je model, který se nasazuje. Soubor `myenv.yml` obsahuje conda závislosti vytvořené v kroku 1.

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

    Jakmile je obrázek vytvořen a stažen, cesta k obrázku (obsahuje úložiště, název a značku, která je v tomto případě také jeho digest) se zobrazí ve zprávě podobné následující:

    ```text
    Status: Downloaded newer image for myregistry.azurecr.io/package@sha256:<image-digest>
    ```

1. Chcete-li usnadnit práci s obrazem, přidejte značku pomocí následujícího příkazu. Nahraďte `myimagepath` hodnotou umístění z předchozího kroku.

    ```bash
    docker tag myimagepath debug:1
    ```

    Pro zbývající kroky můžete odkazovat na místní `debug:1` obrázek jako namísto úplné hodnoty cesty obrázku.

### <a name="debug-the-service"></a>Ladění služby

> [!TIP]
> Pokud nastavíte časový limit pro připojení PTVSD v souboru, `score.py` je nutné připojit Kód VS k relaci ladění před vypršením časového limitu. Spusťte Kód VS, `score.py`otevřete místní kopii aplikace , nastavte zarážku a připravte ji před použitím kroků v této části.
>
> Další informace o ladění a nastavení zarážek naleznete v [tématu Ladění](https://code.visualstudio.com/Docs/editor/debugging).

1. Chcete-li spustit kontejner Dockeru pomocí bitové kopie, použijte následující příkaz:

    ```bash
    docker run --rm --name debug -p 8000:5001 -p 5678:5678 debug:1
    ```

1. Chcete-li připojit kód VS k identifikátoru PTVSD uvnitř kontejneru, otevřete kód VS a použijte klávesu F5 nebo vyberte __možnost Ladění__. Po zobrazení výzvy vyberte konfiguraci __Azure Machine Learning: Docker Debug.__ Můžete také vybrat ikonu ladění z postranního panelu, __Azure Machine Learning: Docker Ladění__ položky z nabídky ladění a pak použijte zelenou šipku připojit ladicí program.

    ![Ikona ladění, tlačítko spuštění ladění a volič konfigurace](./media/how-to-troubleshoot-deployment/start-debugging.png)

V tomto okamžiku vs kód připojí k PTVSD uvnitř kontejneru Dockeru a zastaví na zarážky, kterou jste nastavili dříve. Nyní můžete krokovat kód při jeho spuštění, zobrazit proměnné atd.

Další informace o použití kódu VS k ladění Pythonu najdete v [tématu Ladění kódu Pythonu](https://docs.microsoft.com/visualstudio/python/debugging-python-in-visual-studio?view=vs-2019).

<a id="editfiles"></a>
### <a name="modify-the-container-files"></a>Úprava souborů kontejnerů

Chcete-li provést změny souborů v bitové kopii, můžete připojit ke spuštěnému kontejneru a spustit prostředí bash. Odtud můžete použít vim k úpravám souborů:

1. Chcete-li se připojit ke spuštěnému kontejneru a spustit prostředí bash v kontejneru, použijte následující příkaz:

    ```bash
    docker exec -it debug /bin/bash
    ```

1. Chcete-li najít soubory používané službou, použijte následující příkaz z prostředí bash v `/var/azureml-app`kontejneru, pokud se výchozí adresář liší od :

    ```bash
    cd /var/azureml-app
    ```

    Zde můžete soubor upravit `score.py` pomocí vimu. Další informace o používání vimu najdete [v tématu Použití editoru Vim](https://www.tldp.org/LDP/intro-linux/html/sect_06_02.html).

1. Změny kontejneru nejsou obvykle trvalé. Chcete-li uložit provedené změny, použijte následující příkaz, než ukončíte prostředí spuštěné v kroku výše (to znamená v jiném prostředí):

    ```bash
    docker commit debug debug:2
    ```

    Tento příkaz vytvoří nový `debug:2` obrázek s názvem, který obsahuje vaše úpravy.

    > [!TIP]
    > Budete muset zastavit aktuální kontejner a začít používat novou verzi, než se změny projeví.

1. Ujistěte se, že změny provedené v souborech v kontejneru synchronizujete s místními soubory, které používá VS Code. V opačném případě nebude prostředí ladicího programu fungovat podle očekávání.

### <a name="stop-the-container"></a>Zastavení kontejneru

Chcete-li kontejner zastavit, použijte následující příkaz:

```bash
docker stop debug
```

## <a name="next-steps"></a>Další kroky

Další informace o nasazení:

* [Jak nasadit a kde](how-to-deploy-and-where.md)
* [Kurz: Školení & nasazovat modely](tutorial-train-models-with-aml.md)
