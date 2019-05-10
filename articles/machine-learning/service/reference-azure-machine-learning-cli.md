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
ms.date: 05/02/2019
ms.custom: seodec18
ms.openlocfilehash: 35e57dfcc7b1fd6f8de265ab75de29dedd8fdfc2
ms.sourcegitcommit: 1d257ad14ab837dd13145a6908bc0ed7af7f50a2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/09/2019
ms.locfileid: "65501667"
---
# <a name="use-the-cli-extension-for-azure-machine-learning-service"></a>Použití rozšíření rozhraní příkazového řádku pro službu Azure Machine Learning

Rozhraní příkazového řádku Azure Machine Learning je rozšíření [rozhraní příkazového řádku Azure](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest), multiplatformního rozhraní příkazového řádku pro platformu Azure. Toto rozšíření poskytuje příkazy pro práci se službou Azure Machine Learning. Umožňuje vám automatizovat strojového učení aktivity. Následující seznam uvádí některé příklad akce, které vám pomůžou se rozšíření rozhraní příkazového řádku:

+ Spustit vytváření modelů strojového učení

+ Zaregistrujte modelů strojového učení pro využití ze strany zákazníků

+ Balení, nasazování a sledování životního cyklu vašich modelů machine learning

Rozhraní příkazového řádku, není to náhrada pro sadu SDK Azure Machine Learning. Je doplňkové nástroj, který je optimalizovaný pro zpracování vysoce parametrizované úkoly, které sami podle dobře službě automation.

## <a name="prerequisites"></a>Požadavky

* Pokud chcete používat rozhraní příkazového řádku, musíte mít předplatné Azure. Pokud nemáte předplatné Azure, vytvořte si bezplatný účet, před zahájením. Zkuste [bezplatné nebo placené verzi aplikace služby Azure Machine Learning](https://aka.ms/AMLFree) ještě dnes.

* [Rozhraní příkazového řádku Azure](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest).

## <a name="full-reference-docs"></a>Úplný přehled dokumentace

Najít [úplné referenční dokumenty pro azure-cli-ml rozšíření Azure CLI](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/?view=azure-cli-latest).

## <a name="install-the-extension"></a>Nainstalovat rozšíření

Pokud chcete nainstalovat rozšíření Machine Learning CLI, použijte následující příkaz:

```azurecli-interactive
az extension add -n azure-cli-ml
```

> [!TIP]
> Příklad soubory můžete použít pomocí níže uvedených příkazů lze nalézt [tady](https://aka.ms/azml-deploy-cloud).

Po zobrazení výzvy vyberte `y` nainstalovat rozšíření.

Pokud chcete ověřit, jestli je nainstalovaná rozšíření, použijte následující příkaz k zobrazení seznamu podpříkazů specifické pro ML:

```azurecli-interactive
az ml -h
```

## <a name="remove-the-extension"></a>Odebrat rozšíření

Chcete-li odebrat rozšíření rozhraní příkazového řádku, použijte následující příkaz:

```azurecli-interactive
az extension remove -n azure-cli-ml
```

## <a name="resource-management"></a>Správa prostředků

Následující příkazy ukazují, jak používat rozhraní příkazového řádku ke správě prostředků používat Azure Machine Learning.

+ Pokud již nemáte, vytvořte skupinu prostředků:

    ```azurecli-interactive
    az group create -n myresourcegroup -l westus2
    ```

+ Vytvořte pracovní prostor služby Azure Machine Learning:

    ```azurecli-interactive
    az ml workspace create -w myworkspace -g myresourcegroup
    ```

    Další informace najdete v tématu [vytvořit pracovní prostor služby ml az](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/workspace?view=azure-cli-latest#ext-azure-cli-ml-az-ml-workspace-create).

+ Konfigurace pracovního prostoru se připojte k složku, do které umožňují kontextové povědomí o rozhraní příkazového řádku.

    ```azurecli-interactive
    az ml folder attach -w myworkspace -g myresourcegroup
    ```

    Tento příkaz vytvoří `.azureml` podadresář, který obsahuje soubory příklad runconfig a conda prostředí. Obsahuje taky `config.json` soubor, který se používá ke komunikaci s pracovního prostoru Azure Machine Learning.

    Další informace najdete v tématu [az ml složky připojit](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/folder?view=azure-cli-latest#ext-azure-cli-ml-az-ml-folder-attach).

+ Připojení ke kontejneru objektů blob v Azure jako datového úložiště.

    ```azurecli-interactive
    az ml datastore attach-blob  -n datastorename -a accountname -c containername
    ```

    Další informace najdete v tématu [az ml úložiště dat připojit – objekt blob](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/datastore?view=azure-cli-latest#ext-azure-cli-ml-az-ml-datastore-attach-blob).

    
+ Připojte jako cíl výpočetní AKS cluster.

    ```azurecli-interactive
    az ml computetarget attach aks -n myaks -i myaksresourceid -g myresourcegroup -w myworkspace
    ```

    Další informace najdete v tématu [připojit az ml computetarget aks](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/computetarget/attach?view=azure-cli-latest#ext-azure-cli-ml-az-ml-computetarget-attach-aks)

+ Vytvořte nový cíl AMLcompute.

    ```azurecli-interactive
    az ml computetarget create amlcompute -n cpu --min-nodes 1 --max-nodes 1 -s STANDARD_D3_V2
    ```

    Další informace najdete v tématu [az ml computetarget vytvořit amlcompute](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/computetarget/create?view=azure-cli-latest#ext-azure-cli-ml-az-ml-computetarget-create-amlcompute).

## <a id="experiments"></a>Spouštění experimentů

* Spusťte běh experimentu. Při použití tohoto příkazu, zadejte název souboru runconfig (text před \*.runconfig, pokud chcete v systému souborů) pro parametr - c.

    ```azurecli-interactive
    az ml run submit-script -c sklearn -e testexperiment train.py
    ```

    > [!TIP]
    > `az ml folder attach` Příkaz vytvoří `.azureml` podadresář, který obsahuje dva soubory runconfig příklad. 
    >
    > Pokud máte skript v jazyce Python, která vytvoří objekt konfigurace spuštění prostřednictvím kódu programu, můžete použít [RunConfig.save()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfiguration?view=azure-ml-py#save-path-none--name-none--separate-environment-yaml-false-) ji uložit jako soubor runconfig.
    >
    > Další příklad runconfig soubory, naleznete v tématu [ https://github.com/MicrosoftDocs/pipelines-azureml/tree/master/.azureml ](https://github.com/MicrosoftDocs/pipelines-azureml/tree/master/.azureml).

    Další informace najdete v tématu [az ml spuštění odeslat skript](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/run?view=azure-cli-latest#ext-azure-cli-ml-az-ml-run-submit-script).

* Zobrazení seznamu experimentů:

    ```azurecli-interactive
    az ml experiment list
    ```

    Další informace najdete v tématu [az ml experimentovat seznamu](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/experiment?view=azure-cli-latest#ext-azure-cli-ml-az-ml-experiment-list).

## <a name="model-registration-profiling-deployment"></a>Registrace modelu, profilování, nasazení

Následující příkazy ukazují, jak registrace trénovaného modelu a pak ho nasadíme jako služby v produkčním prostředí:

+ Zaregistrujte model Azure Machine Learning:

    ```azurecli-interactive
    az ml model register -n mymodel -p sklearn_regression_model.pkl
    ```

    Další informace najdete v tématu [registrace modelu ml az](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-register).

+ **VOLITELNÉ** profilu modelu k získání hodnot optimální využití procesoru a paměti pro nasazení.
    ```azurecli-interactive
    az ml model profile -n myprofile -m mymodel:1 --ic inferenceconfig.json -d "{\"data\": [[1,2,3,4,5,6,7,8,9,10],[10,9,8,7,6,5,4,3,2,1]]}" -t myprofileresult.json
    ```

    Další informace najdete v tématu [profil modelu ml az](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-profile).

+ Nasazení modelu do AKS

    ```azurecli-interactive
    az ml model deploy -n myservice -m mymodel:1 --ic inferenceconfig.json --dc deploymentconfig.json
    ```

    Tady je příklad `inferenceconfig.json` dokumentu:

    ```json
    {
    "entryScript": "score.py",
    "runtime": "python",
    "condaFile": "myenv.yml",
    "extraDockerfileSteps": null,
    "sourceDirectory": null,
    "enableGpu": false,
    "baseImage": null,
    "baseImageRegistry": null
    }
    ```

    Další informace najdete v tématu [nasazení modelu ml az](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-deploy).


## <a name="next-steps"></a>Další postup

* [Příkaz referenční informace k rozšíření Machine Learning CLI](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml?view=azure-cli-latest).

* [Trénování a nasazovat modely machine learningu pomocí kanálů Azure](/azure/devops/pipelines/targets/azure-machine-learning?view=azure-devops)