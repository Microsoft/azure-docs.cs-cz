---
title: Řešení potíží s nasazením Docker
titleSuffix: Azure Machine Learning
description: Naučte se řešit, řešit a řešit běžné chyby nasazení Docker pomocí služby Azure Kubernetes a Azure Container Instances pomocí Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
author: clauren42
ms.author: clauren
ms.reviewer: jmartens
ms.date: 08/06/2020
ms.topic: conceptual
ms.custom: troubleshooting, contperfq4, devx-track-python, deploy
ms.openlocfilehash: 7ed025bc5b7e59a28fcb2f03eeea56628e2cde0d
ms.sourcegitcommit: 090ea6e8811663941827d1104b4593e29774fa19
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/13/2020
ms.locfileid: "91998622"
---
# <a name="troubleshoot-docker-deployment-of-models-with-azure-kubernetes-service-and-azure-container-instances"></a>Řešení potíží s nasazením v Docker modelů pomocí služby Azure Kubernetes a Azure Container Instances 

Přečtěte si, jak řešit a řešit běžné chyby nasazení Docker pomocí Azure Container Instances (ACI) a Azure Kubernetes Service (AKS) pomocí Azure Machine Learning.

## <a name="prerequisites"></a>Požadavky

* **Předplatné Azure** Vyzkoušení [bezplatné nebo placené verze Azure Machine Learning](https://aka.ms/AMLFree).
* [Sada Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py&preserve-view=true).
* Rozhraní příkazového [řádku Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest&preserve-view=true)
* [Rozšíření CLI pro Azure Machine Learning](reference-azure-machine-learning-cli.md).
* Chcete-li ladit místně, je nutné mít v místním systému funkční instalaci Docker.

    K ověření instalace Docker použijte příkaz `docker run hello-world` z terminálu nebo příkazového řádku. Informace o instalaci Docker nebo odstraňování potíží s chybami Docker najdete v [dokumentaci k Docker](https://docs.docker.com/).

## <a name="steps-for-docker-deployment-of-machine-learning-models"></a>Postup pro nasazení Docker modelů strojového učení

Při nasazování modelu v Azure Machine Learning použijte rozhraní API [model. deploy ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model%28class%29?view=azure-ml-py&preserve-view=true#&preserve-view=truedeploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-) a objekt [prostředí](how-to-use-environments.md) . Služba vytvoří základní image Docker během fáze nasazení a připojí požadované modely v jednom volání. Základní úlohy nasazení:

1. Zaregistrujte model v registru modelu pracovního prostoru.

2. Definovat odvozenou konfiguraci:
    1. Vytvořte objekt [prostředí](how-to-use-environments.md) . Tento objekt může používat závislosti v souboru YAML prostředí, což je jedno z našich prostředí.
    2. Vytvořte odvozenou konfiguraci (objekt InferenceConfig) na základě prostředí a hodnoticího skriptu.

3. Nasaďte model do služby Azure Container instance (ACI) nebo do služby Azure Kubernetes Service (AKS).

Další informace o tomto procesu najdete v úvodu [Správa modelů](concept-model-management-and-deployment.md) .

## <a name="before-you-begin"></a>Než začnete

Pokud narazíte na problém, je první věc, kterou je potřeba udělat, rozdělte úlohu nasazení (popsanou výše) do jednotlivých kroků k izolaci problému.

Při použití [modelu model. deploy ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model%28class%29?view=azure-ml-py&preserve-view=true#&preserve-view=truedeploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-) s objektem [prostředí](how-to-use-environments.md) jako vstupní parametr může být váš kód rozdělen na tři hlavní kroky:

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

Narušení procesu nasazení Thee do jednotlivých úloh usnadňuje identifikaci některých častých chyb.

## <a name="debug-locally"></a>Místní ladění

Pokud máte problémy při nasazování modelu do ACI nebo AKS, nasaďte ho jako místní webovou službu. Použití místní webové služby usnadňuje řešení potíží.

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

Chcete-li změnit model, závislosti conda nebo konfiguraci nasazení, použijte [Update ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice%28class%29?view=azure-ml-py&preserve-view=true#&preserve-view=trueupdate--args-). Následující příklad aktualizuje model používaný službou:

```python
service.update([different_model], inference_config, deployment_config)
```

### <a name="delete-the-service"></a>Odstranit službu

Pokud chcete službu odstranit, použijte [Delete ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice%28class%29?view=azure-ml-py&preserve-view=true#&preserve-view=truedelete--).

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
 
## <a name="container-cannot-be-scheduled"></a>Kontejner nelze naplánovat.

Když nasadíte službu do cíle výpočetního cíle služby Azure Kubernetes, Azure Machine Learning se pokusí naplánovat službu s požadovaným množstvím prostředků. Pokud v clusteru nejsou k dispozici žádné uzly s odpovídajícím množstvím prostředků po 5 minutách, nasazení se nezdaří. Zpráva o selhání je `Couldn't Schedule because the kubernetes cluster didn't have available resources after trying for 00:05:00` . Tuto chybu můžete vyřešit přidáním více uzlů, změnou SKU vašich uzlů nebo změnou požadavků na prostředky vaší služby. 

Chybová zpráva obvykle indikuje, který prostředek potřebujete více, pokud se zobrazí chybová zpráva oznamující `0/3 nodes are available: 3 Insufficient nvidia.com/gpu` , že služba vyžaduje GPU a v clusteru jsou tři uzly, které nemají k dispozici GPU. To se dá řešit přidáním dalších uzlů, pokud používáte SKU GPU, přechodem na SKU s povoleným GPU, pokud nechcete nebo neměníte prostředí, aby nevyžadovalo GPU.  

## <a name="service-launch-fails"></a>Spuštění služby se nezdařilo.

Po úspěšném vytvoření image se systém pokusí spustit kontejner pomocí konfigurace nasazení. V rámci procesu spuštění kontejneru `init()` je funkce ve vašem skriptu bodování vyvolána systémem. Pokud ve funkci nejsou zachycené výjimky `init()` , může se zobrazit chyba **CrashLoopBackOff** v chybové zprávě.

Použijte informace v části Kontrola [protokolu Docker](#dockerlog) pro kontrolu protokolů.

## <a name="function-fails-get_model_path"></a>Funkce se nezdařila: get_model_path ()

Často `init()` je ve funkci skriptu bodování volána funkce [model.get_model_path ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py&preserve-view=true#&preserve-view=trueget-model-path-model-name--version-none---workspace-none-) pro vyhledání souboru modelu nebo složky souborů modelu v kontejneru. Pokud soubor modelu nebo složku nelze nalézt, funkce se nezdařila. Nejjednodušší způsob, jak tuto chybu ladit, je spuštění níže uvedeného kódu Pythonu v prostředí kontejneru:

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

Nasazení služby Azure Kubernetes podporují automatické škálování, které umožňuje přidat repliky pro podporu dalšího zatížení. Automatické škálování je navrženo pro zpracování **postupných** změn v zatížení. Pokud v požadavcích za sekundu obdržíte velké špičky, mohou klienti obdržet stavový kód HTTP 503. I když automatické škálování rychle reaguje, trvá AKS značnou dobu k vytvoření dalších kontejnerů.

Rozhodnutí o horizontálním navýšení/snížení kapacity vycházejí z využití aktuálních replik kontejnerů. Počet replik, které jsou zaneprázdněné (zpracování požadavku) dělený celkovým počtem aktuálních replik, je aktuální využití. Pokud tento počet překročí `autoscale_target_utilization` , vytvoří se další repliky. Pokud je nižší, jsou repliky sníženy. Rozhodnutí o přidání replik jsou Eager a rychlá (kolem 1 sekundy). Rozhodnutí o odebrání replik jsou konzervativní (přibližně 1 minutu). Ve výchozím nastavení je cílení na automatické škálování nastaveno na **70%**, což znamená, že služba dokáže zpracovávat špičky v požadavcích za sekundu (RPS) **až o 30%**.

K dispozici jsou dvě věci, které vám pomůžou zabránit stavovým kódům 503:

> [!TIP]
> Tyto dva přístupy lze použít jednotlivě nebo v kombinaci.

* Změňte úroveň využití, při které automatické škálování vytvoří nové repliky. Cíl využití můžete upravit nastavením `autoscale_target_utilization` na nižší hodnotu.

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

Další informace o nastavení `autoscale_target_utilization` , `autoscale_max_replicas` a `autoscale_min_replicas` pro naleznete v tématu Reference k modulu [AksWebservice](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.akswebservice?view=azure-ml-py&preserve-view=true) .

## <a name="http-status-code-504"></a>Stavový kód HTTP 504

Stavový kód 504 označuje, že vypršel časový limit žádosti. Výchozí časový limit je 1 minuta.

Časový limit můžete zvýšit nebo se pokusit o urychlení služby úpravou score.py pro odebrání nepotřebných volání. Pokud tyto akce problém nevyřeší, použijte informace v tomto článku k ladění souboru score.py. Kód může být v nereagující stavu nebo nekonečné smyčce.

## <a name="advanced-debugging"></a>Pokročilé ladění

Možná budete muset interaktivně ladit kód Pythonu obsažený v nasazení modelu. Například pokud se skript vstupu nezdařil a důvod nelze určit pomocí dalšího protokolování. Pomocí Visual Studio Code a debugpy můžete připojit k kódu běžícímu uvnitř kontejneru Docker.

Další informace najdete [v příručce k interaktivnímu ladění v vs Code](how-to-debug-visual-studio-code.md#debug-and-troubleshoot-deployments).

## <a name="model-deployment-user-forum"></a>[Fórum uživatele nasazení modelu](https://docs.microsoft.com/answers/topics/azure-machine-learning-inference.html)

## <a name="next-steps"></a>Další kroky

Další informace o nasazení:

* [Jak nasadit a kde](how-to-deploy-and-where.md)
* [Kurz: výuka & nasazení modelů](tutorial-train-models-with-aml.md)
