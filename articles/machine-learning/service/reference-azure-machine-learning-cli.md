---
title: Strojové učení rozšíření rozhraní příkazového řádku
titleSuffix: Azure Machine Learning service
description: Další informace o rozšíření Azure Machine Learning rozhraní příkazového řádku Azure CLI. Azure CLI je nástroj příkazového řádku napříč platformami, která umožňuje pracovat s prostředky v cloudu Azure. Rozšíření Machine Learning umožňuje pracovat s služby Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: jordane
author: jpe316
ms.date: 12/04/2018
ms.custom: seodec18
ms.openlocfilehash: d7baa4faf718852e5bddd89f99e4ffc1248a403c
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/30/2019
ms.locfileid: "55249727"
---
# <a name="use-the-cli-extension-for-azure-machine-learning-service"></a>Použití rozšíření rozhraní příkazového řádku pro službu Azure Machine Learning

Rozhraní příkazového řádku Azure Machine Learning je rozšíření [rozhraní příkazového řádku Azure](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest), multiplatformního rozhraní příkazového řádku pro platformu Azure. Toto rozšíření poskytuje příkazy pro práci se službou Azure Machine Learning z příkazového řádku. Umožňuje vytvořit skripty pro automatizaci strojového učení pracovních postupů. Například můžete vytvořit skripty, které provést následující akce:

+ Spustit vytváření modelů strojového učení

+ Zaregistrujte modelů strojového učení pro využití ze strany zákazníků

+ Balení, nasazování a sledování životního cyklu vašich modelů machine learning

Rozhraní příkazového řádku, není to náhrada pro sadu SDK Azure Machine Learning. Doplňkové nástroj, který je optimalizovaný pro zpracování vysoce parametrizované úkoly, jako je:

* Vytváření výpočetních prostředků

* odeslání experimentu parametrizované

* Registrace modelu

* Vytvoření Image

* Nasazení služby

## <a name="prerequisites"></a>Požadavky


* Pokud chcete používat rozhraní příkazového řádku, musíte mít předplatné Azure. Pokud nemáte předplatné Azure, vytvořte si bezplatný účet, před zahájením. Zkuste [bezplatné nebo placené verzi aplikace služby Azure Machine Learning](http://aka.ms/AMLFree) ještě dnes.

* [Rozhraní příkazového řádku Azure](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest).

## <a name="install-the-extension"></a>Instalace rozšíření

Pokud chcete nainstalovat rozšíření Machine Learning CLI, použijte následující příkaz:

```azurecli-interactive
az extension add -s https://azuremlsdktestpypi.blob.core.windows.net/wheels/sdk-release/Preview/E7501C02541B433786111FE8E140CAA1/azure_cli_ml-1.0.6-py2.py3-none-any.whl --pip-extra-index-urls  https://azuremlsdktestpypi.azureedge.net/sdk-release/Preview/E7501C02541B433786111FE8E140CAA1
```

Po zobrazení výzvy vyberte `y` nainstalovat rozšíření.

Pokud chcete ověřit, jestli je nainstalovaná rozšíření, použijte následující příkaz k zobrazení seznamu podpříkazů specifické pro ML:

```azurecli-interactive
az ml -h
```

> [!TIP]
> Rozšíření je nutné aktualizovat __odebrat__ a potom __nainstalovat__ ho. Tím se nainstaluje nejnovější verzi.

## <a name="remove-the-extension"></a>Odebrat rozšíření

Chcete-li odebrat rozšíření rozhraní příkazového řádku, použijte následující příkaz:

```azurecli-interactive
az extension remove -n azure-cli-ml
```

## <a name="resource-management"></a>Správa prostředků

Následující příkazy ukazují, jak používat rozhraní příkazového řádku ke správě prostředků používat Azure Machine Learning.


+ Vytvořte pracovní prostor služby Azure Machine Learning:

    ```azurecli-interactive
    az ml workspace create -n myworkspace -g myresourcegroup
    ```

+ Nastavení výchozího pracovního prostoru:

    ```azurecli-interactive
    az configure --defaults aml_workspace=myworkspace group=myresourcegroup
    ```

+ Vytvořte spravovanou výpočetní cíl pro distribuované trénování:

    ```azurecli-interactive
    az ml computetarget create amlcompute -n mycompute --max_nodes 4 --size Standard_NC6
    ```

* Aktualizace cílové spravované výpočetní prostředí:

    ```azurecli-interactive
    az ml computetarget update --name mycompute --workspace –-group --max_nodes 4 --min_nodes 2 --idle_time 300
    ```

* Připojení cílové nespravované výpočetní prostředí pro trénování nebo nasazení:

    ```azurecli-interactive
    az ml computetarget attach aks -n myaks -i myaksresourceid -g myrg -w myworkspace
    ```

## <a name="experiments"></a>Experimenty

Následující příkazy ukazují, jak pracovat s experimenty pomocí rozhraní příkazového řádku:

* Před odesláním experiment připojte projekt (Konfigurace spuštění):

    ```azurecli-interactive
    az ml project attach --experiment-name myhistory
    ```

* Spusťte běh experimentu. Při použití tohoto příkazu, zadejte název `.runconfig` soubor, který obsahuje konfigurace spuštění. Cílové výpočetní prostředí používá k vytvoření prostředí pro školení pro model konfigurace spuštění. V tomto příkladu je konfigurace spuštění načtena z `./aml_config/myrunconfig.runconfig` souboru.

    ```azurecli-interactive
    az ml run submit -c myrunconfig train.py
    ```

    Výchozí `.runconfig` soubory s názvem `docker.runconfig` a `local.runconfig` jsou vytvořeny po připojení projektu pomocí `az ml project attach` příkazu. Budete muset upravit před jejich použitím pro trénování modelu. 

    Můžete také vytvořit konfigurace spuštění prostřednictvím kódu programu pomocí [RunConfiguration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfig.runconfiguration?view=azure-ml-py) třídy. Po vytvoření, pak můžete použít `save()` metodu pro vytvoření `.runconfig` souboru.

* Zobrazte seznam odeslaných experimentů:

    ```azurecli-interactive
    az ml history list
    ```

## <a name="model-registration-image-creation--deployment"></a>Model registrace, vytvoření image a nasazení

Následující příkazy ukazují, jak registrace trénovaného modelu a pak ho nasadíme jako služby v produkčním prostředí:

+ Zaregistrujte model Azure Machine Learning:

  ```azurecli-interactive
  az ml model register -n mymodel -m sklearn_regression_model.pkl
  ```

+ Vytvořte bitovou kopii, která obsahuje vaše modelu strojového učení a závislosti: 

  ```azurecli-interactive
  az ml image create container -n myimage -r python -m mymodel:1 -f score.py -c myenv.yml
  ```

+ Nasazení bitové kopie do cílového výpočetního prostředí:

  ```azurecli-interactive
  az ml service create aci -n myaciservice --image-id myimage:1
  ```
