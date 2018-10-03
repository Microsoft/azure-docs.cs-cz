---
title: O rozšíření rozhraní příkazového řádku pro Azure Machine Learning
description: Další informace o machine learningu rozšíření rozhraní příkazového řádku pro Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: reference
ms.reviewer: jmartens
ms.author: jordane
author: jpe316
ms.date: 09/24/2018
ms.openlocfilehash: ea7782f09902fe96c858cb30fc671d6503b56ba1
ms.sourcegitcommit: 1981c65544e642958917a5ffa2b09d6b7345475d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/03/2018
ms.locfileid: "48236425"
---
# <a name="what-is-the-azure-machine-learning-cli"></a>Co je rozhraní příkazového řádku Azure Machine Learning?

Rozšíření Azure Machine Learning rozhraní příkazového řádku (CLI) je pro odborníky přes data a vývojáři práci se službou Azure Machine Learning. Umožňuje vám rychle automatizovat pracovní postupy machine learning a umístit je do produkčního prostředí, jako například:
+ Spustit vytváření modelů strojového učení

+ Zaregistrujte modelů strojového učení pro využití ze strany zákazníků

+ Balení, nasazování a sledování životního cyklu vašich modelů machine learning

Tato strojového učení rozhraní příkazového řádku je rozšířením [rozhraní příkazového řádku Azure](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) a byla vytvořena na základě Python <a href="http://aka.ms/aml-sdk" target="_blank">SDK</a> pro službu Azure Machine Learning.

> [!NOTE]
> Rozhraní příkazového řádku je aktuálně v předběžné verzi preview a budou aktualizovány.

## <a name="installing-and-uninstalling"></a>Instalace a odinstalace

Můžete nainstalovat rozhraní příkazového řádku pomocí tohoto příkazu z našich náhledu PyPi indexu:
```AzureCLI
az extension add -s https://azuremlsdktestpypi.blob.core.windows.net/wheels/sdk-release/Preview/E7501C02541B433786111FE8E140CAA1/azure_cli_ml-0.1.50-py2.py3-none-any.whl --pip-extra-index-urls  https://azuremlsdktestpypi.azureedge.net/sdk-release/Preview/E7501C02541B433786111FE8E140CAA1
```

Odebrat rozhraní příkazového řádku pomocí tohoto příkazu:
```AzureCLI
az extension remove -n azure-cli-ml
```

Můžete aktualizovat pomocí rozhraní příkazového řádku **odebrat** a **přidat** výše uvedených kroků.

## <a name="using-the-cli-vs-the-sdk"></a>Pomocí rozhraní příkazového řádku a sady SDK
Rozhraní příkazového řádku je vhodnější automatizace dev ops osoby, nebo jako součást kanálu průběžné integrace a doručování. Je optimalizovaný pro zpracování úlohy s řídkým a vysoce parametry úlohy. 

Příklady obsahují:
- COMPUTE zřizování
- odeslání experimentu parametrizované
- registrace modelu, vytvoření bitové kopie
- Nasazení služby

Odborníci přes data jsou vhodné pro použití sady SDK Azure ML.

## <a name="common-machine-learning-cli-commands"></a>Běžné strojového učení příkazy rozhraní příkazového řádku
> [!NOTE]
> Ukázkové soubory, které vám umožní byl úspěšně spuštěn následujících příkazů můžete najít [tady.](https://github.com/Azure/MachineLearningNotebooks/tree/cli/cli)

Využijte bohatou sadu `az ml` příkazy pracovat služby v jakémkoli příkazového řádku prostředí, včetně portálu Azure cloud shellu.

Tady je ukázka běžné příkazy:

### <a name="workspace-creation--compute-setup"></a>Nastavení pracovního prostoru vytváření a výpočetní prostředky

+ Vytvoření Azure Machine Learning pracovního prostoru služby, prostředku nejvyšší úrovně pro machine learning.
   ```AzureCLI
   az ml workspace create -n myworkspace -g myresourcegroup
   ```

+ Nastavení rozhraní příkazového řádku a ve výchozím nastavení používají tento pracovní prostor.
   ```AzureCLI
   az configure --defaults aml_workspace=myworkspace group=myresourcegroup
   ```

+ Vytvoření DSVM (datové vědy virtuálního počítače). Můžete také vytvořit BatchAI clustery pro distribuované trénování nebo nasazení clusterů AKS.
  ```AzureCLI
  az ml computetarget setup dsvm -n mydsvm
  ```

### <a name="experiment-submission"></a>Odeslání experimentu
+ Připojení k projektu (Konfigurace spuštění) pro odeslání experimentu. To se používá ke sledování spuštění experimentů.
  ```AzureCLI
  az ml project attach --experiment-name myhistory
  ```

+ Odeslání experimentu na službu Azure Machine Learning na cílové výpočetní prostředí podle vašeho výběru. V tomto příkladu se spustí před místním výpočetním prostředí. Ujistěte se, že váš soubor prostředí conda zachycuje závislosti pythonu.

  ```AzureCLI
  az ml run submit -c local train.py
  ```

+ Zobrazte seznam odeslaných experimentů.
```AzureCLI
az ml history list
```

### <a name="model-registration-image-ceation--deployment"></a>Model registrace, image ceation a nasazení

+ Zaregistrujte model Azure Machine Learning.
  ```AzureCLI
  az ml model register -n mymodel -m sklearn_regression_model.pkl
  ```

+ Vytvoření image tak, aby obsahovala vašeho modelu strojového učení a závislosti. 
  ```AzureCLI
  az ml image create container -n myimage -r python -m mymodel:1 -f score.py -c myenv.yml
  ```

+ Nasazení modelu zabalené do cíle, včetně ACI a AKS.
  ```AzureCLI
  az ml service create aci -n myaciservice --image-id myimage:1
  ```
    
## <a name="full-command-list"></a>Celý příkaz seznamu
Úplný seznam příkazů pro rozšíření rozhraní příkazového řádku (a jejich podporovaných parametrů) můžete najít spuštěním ```az ml COMMANDNAME -h```. 
