---
title: Řešení potíží s nasazením vzdáleného modelu
titleSuffix: Azure Machine Learning
description: Naučte se řešit, řešit a řešit některé běžné chyby nasazení Docker pomocí služby Azure Kubernetes a Azure Container Instances.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
author: gvashishtha
ms.author: gopalv
ms.reviewer: jmartens
ms.date: 11/25/2020
ms.topic: troubleshooting
ms.custom: contperf-fy20q4, devx-track-python, deploy, contperf-fy21q2
ms.openlocfilehash: 4224e301d6410fc97da1f98cd0dd9577c6341cd3
ms.sourcegitcommit: 44844a49afe8ed824a6812346f5bad8bc5455030
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/23/2020
ms.locfileid: "97740619"
---
# <a name="troubleshooting-remote-model-deployment"></a>Řešení potíží s nasazením vzdáleného modelu 

Přečtěte si, jak řešit a řešit běžné chyby, ke kterým může dojít při nasazování modelu do Azure Container Instances (ACI) a Azure Kubernetes Service (AKS) pomocí Azure Machine Learning.

## <a name="prerequisites"></a>Požadavky

* **Předplatné Azure** Vyzkoušení [bezplatné nebo placené verze Azure Machine Learning](https://aka.ms/AMLFree).
* [Sada Azure Machine Learning SDK](/python/api/overview/azure/ml/install?preserve-view=true&view=azure-ml-py).
* Rozhraní příkazového [řádku Azure](/cli/azure/install-azure-cli?preserve-view=true&view=azure-cli-latest)
* [Rozšíření CLI pro Azure Machine Learning](reference-azure-machine-learning-cli.md).

## <a name="steps-for-docker-deployment-of-machine-learning-models"></a>Postup pro nasazení Docker modelů strojového učení

Při nasazení modelu do nemístního výpočetního prostředí v Azure Machine Learning dojde k následujícím akcím:

1. Souboru Dockerfile, který jste zadali v objektu prostředí v InferenceConfig, se odesílají do cloudu spolu s obsahem zdrojového adresáře.
1. Pokud v registru kontejneru není k dispozici dříve vytvořená image, vytvoří se nová image Docker v cloudu a uloží se do výchozího registru kontejnerů v pracovním prostoru.
1. Image Docker z vašeho registru kontejneru se stáhne do vašeho cíle výpočetní služby.
1. Výchozí úložiště objektů BLOB v pracovním prostoru je připojené k vašemu cíli výpočtů a poskytuje přístup k registrovaným modelům.
1. Váš webový server se inicializuje spuštěním funkce skriptu vašeho vstupu. `init()`
1. Když nasazený model obdrží požadavek, `run()` funkce zpracuje tento požadavek.

Hlavním rozdílem při použití místního nasazení je, že image kontejneru je postavená na místním počítači, což je důvod, proč je nutné mít k místnímu nasazení nainstalovaný Docker.

Pochopení těchto kroků na vysoké úrovni by vám mělo pomáhat pochopit, kde dochází k chybám.

## <a name="get-deployment-logs"></a>Získat protokoly nasazení

Prvním krokem při ladění chyb je získání protokolů nasazení. Nejdřív se připojte k pracovnímu prostoru podle [pokynů uvedených tady](how-to-deploy-and-where.md#connect-to-your-workspace) .

# <a name="azure-cli"></a>[Azure CLI](#tab/azcli)

Chcete-li získat protokoly z nasazené webové služby, udělejte toto:

```bash
az ml service get-logs --verbose --workspace-name <my workspace name> --name <service name>
```

# <a name="python"></a>[Python](#tab/python)


Za předpokladu, že máte objekt typu s `azureml.core.Workspace` názvem `ws` , můžete provést následující:

```python
print(ws.webservices)

# Choose the webservice you are interested in

from azureml.core import Webservice

service = Webservice(ws, '<insert name of webservice>')
print(service.get_logs())
```

---

## <a name="debug-locally"></a>Místní ladění

Pokud máte problémy při nasazování modelu do ACI nebo AKS, nasaďte ho jako místní webovou službu. Použití místní webové služby usnadňuje řešení potíží. Místní řešení potíží s nasazením najdete v [článku věnovaném řešení potíží s místním prostředím](./how-to-troubleshoot-deployment-local.md).

## <a name="container-cannot-be-scheduled"></a>Kontejner nelze naplánovat.

Při nasazování služby do cílového výpočetního objektu Azure Kubernetes Service se služba Azure Machine Learning pokusí pro službu naplánovat požadované množství prostředků. Pokud v clusteru nejsou k dispozici žádné uzly s odpovídajícím množstvím prostředků po 5 minutách, nasazení se nezdaří. Zpráva o selhání je `Couldn't Schedule because the kubernetes cluster didn't have available resources after trying for 00:05:00` . Tuto chybu můžete vyřešit přidáním více uzlů, změnou SKU vašich uzlů nebo změnou požadavků na prostředky vaší služby. 

Chybová zpráva obvykle indikuje, který prostředek potřebujete více, pokud se zobrazí chybová zpráva oznamující `0/3 nodes are available: 3 Insufficient nvidia.com/gpu` , že služba vyžaduje GPU a v clusteru jsou tři uzly, které nemají k dispozici GPU. To se dá řešit přidáním dalších uzlů, pokud používáte SKU GPU, přechodem na SKU s povoleným GPU, pokud nechcete nebo neměníte prostředí, aby nevyžadovalo GPU.  

## <a name="service-launch-fails"></a>Spuštění služby se nezdařilo.

Po úspěšném vytvoření image se systém pokusí spustit kontejner pomocí konfigurace nasazení. V rámci procesu spuštění kontejneru `init()` je funkce ve vašem skriptu bodování vyvolána systémem. Pokud ve funkci nejsou zachycené výjimky `init()` , může se zobrazit chyba **CrashLoopBackOff** v chybové zprávě.

Použijte informace v článku [Kontrola protokolu Docker](how-to-troubleshoot-deployment-local.md#dockerlog) .

## <a name="function-fails-get_model_path"></a>Funkce se nezdařila: get_model_path ()

Často `init()` je ve funkci skriptu bodování volána funkce [model.get_model_path ()](/python/api/azureml-core/azureml.core.model.model?preserve-view=true&view=azure-ml-py#&preserve-view=trueget-model-path-model-name--version-none---workspace-none-) pro vyhledání souboru modelu nebo složky souborů modelu v kontejneru. Pokud se soubor modelu nebo složka modelů nenajde, funkce selže. Nejjednodušší způsob, jak tuto chybu ladit, je spuštění níže uvedeného kódu Pythonu v prostředí kontejneru:

```python
from azureml.core.model import Model
import logging
logging.basicConfig(level=logging.DEBUG)
print(Model.get_model_path(model_name='my-best-model'))
```

Tento příklad vytiskne místní cestu (vzhledem k `/var/azureml-app` ) v kontejneru, ve kterém váš skript bodování očekává nalezení souboru modelu nebo složky. Pak můžete ověřit, jestli je soubor nebo složka skutečně tam, kde se očekává.

Nastavení úrovně protokolování na ladění může způsobit, že budou protokolovány Další informace, které mohou být užitečné při identifikaci selhání.

## <a name="function-fails-runinput_data"></a>Neúspěšná funkce: Run (input_data)

Pokud se služba úspěšně nasadila, ale dojde k chybě při odesílání dat do koncového bodu, můžete do funkce Přidat příkaz pro zachycení chyb, `run(input_data)` aby se místo toho vrátila podrobná chybová zpráva. Například:

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
    > Tato změna nezpůsobí *rychlejší* vytváření replik. Místo toho jsou vytvořeny s nižší prahovou hodnotou využití. Místo čekání na vyčerpání služby 70% se změna hodnoty na 30% způsobí, že se repliky vytvoří, když dojde k 30% využití.
    
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

Další informace o nastavení `autoscale_target_utilization` , `autoscale_max_replicas` a `autoscale_min_replicas` pro naleznete v tématu Reference k modulu [AksWebservice](/python/api/azureml-core/azureml.core.webservice.akswebservice?preserve-view=true&view=azure-ml-py) .

## <a name="http-status-code-504"></a>Stavový kód HTTP 504

Stavový kód 504 označuje, že vypršel časový limit žádosti. Výchozí časový limit je 1 minuta.

Časový limit můžete zvýšit nebo se pokusit o urychlení služby úpravou score.py pro odebrání nepotřebných volání. Pokud tyto akce problém nevyřeší, použijte informace v tomto článku k ladění souboru score.py. Kód může být v nereagující stavu nebo nekonečné smyčce.

## <a name="other-error-messages"></a>Další chybové zprávy

Proveďte tyto akce při následujících chybách:

|Chybová  | Řešení  |
|---------|---------|
|Chyba při sestavování obrázku při nasazení webové služby     |  Přidat "pynacl = = 1.2.1" jako závislost PIP k souboru conda pro konfiguraci bitové kopie       |
|`['DaskOnBatch:context_managers.DaskOnBatch', 'setup.py']' died with <Signals.SIGKILL: 9>`     |   Změňte SKU pro virtuální počítače používané ve vašem nasazení na jednu, která má více paměti. |
|Selhání FPGA     |  Modely v FPGA nebudete moct nasadit, dokud si nebudete požádáni a neschválili kvótu FPGA. Pokud chcete požádat o přístup, vyplňte formulář žádosti o kvótu: https://aka.ms/aml-real-time-ai       |

## <a name="advanced-debugging"></a>Pokročilé ladění

Možná budete muset interaktivně ladit kód Pythonu ve vašem nasazení modelu. Například pokud se skript vstupu nezdařil a důvod nelze určit pomocí dalšího protokolování. Pomocí Visual Studio Code a debugpy můžete připojit k kódu běžícímu uvnitř kontejneru Docker.

Další informace najdete [v příručce k interaktivnímu ladění v vs Code](how-to-debug-visual-studio-code.md#debug-and-troubleshoot-deployments).

## <a name="model-deployment-user-forum"></a>[Fórum uživatele nasazení modelu](/answers/topics/azure-machine-learning-inference.html)

## <a name="next-steps"></a>Další kroky

Další informace o nasazení:

* [Jak nasadit a kde](how-to-deploy-and-where.md)
* [Kurz: výuka & nasazení modelů](tutorial-train-models-with-aml.md)
* [Jak spustit a ladit experimenty místně](./how-to-debug-visual-studio-code.md)