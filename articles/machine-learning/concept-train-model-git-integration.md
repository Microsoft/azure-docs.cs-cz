---
title: Integrace Gitu pro Azure Machine Learning
titleSuffix: Azure Machine Learning
description: Přečtěte si, jak Azure Machine Learning integrovat s místním úložištěm Git. Při odesílání školicích běhů z místního adresáře, který je úložištěm Git, se jako součást běhu sledují informace o úložišti, větvi a aktuálním potvrzení.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: jordane
author: jpe316
ms.date: 03/05/2020
ms.openlocfilehash: 6d9d9f2d7363217ef4e9b13b44d3665af33cb8ef
ms.sourcegitcommit: f8d2ae6f91be1ab0bc91ee45c379811905185d07
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/10/2020
ms.locfileid: "89661797"
---
# <a name="git-integration-for-azure-machine-learning"></a>Integrace Gitu pro Azure Machine Learning

[Git](https://git-scm.com/) je oblíbený systém pro správu verzí, který umožňuje sdílení a spolupráci na vašich projektech. 

Azure Machine Learning plně podporuje úložiště Git pro sledování práce – úložiště můžete klonovat přímo do sdíleného systému souborů pracovních prostorů, použít Git na místní pracovní stanici nebo použít git z kanálu CI/CD.

Při odesílání úlohy do Azure Machine Learning, pokud jsou zdrojové soubory uložené v místním úložišti Git, informace o úložišti se sledují jako součást procesu školení.

Vzhledem k tomu, že Azure Machine Learning sleduje informace z místního úložiště Git, není svázán s žádným konkrétním centrálním úložištěm. Vaše úložiště je možné klonovat z GitHubu, GitLab, BitBucket, Azure DevOps nebo jakékoli jiné služby kompatibilní s Git.

## <a name="clone-git-repositories-into-your-workspace-file-system"></a>Klonování úložišť Git do systému souborů vlastního pracovního prostoru
Azure Machine Learning poskytuje sdílený systém souborů pro všechny uživatele v pracovním prostoru.
K naklonování úložiště Git do této sdílené složky doporučujeme vytvořit výpočetní instanci & otevřete terminál.
Po otevření terminálu máte přístup k plnému klientovi Git a budete moct pomocí prostředí Git CLI klonovat a pracovat s ním.

Doporučujeme, abyste naklonoval úložiště do adresáře uživatelů, aby ostatní nedošlo k kolizím přímo ve vaší pracovní větvi.

Můžete klonovat jakékoli úložiště Git, na které můžete ověřovat (GitHub, Azure Repos, BitBucket atd.).

Návod, jak používat rozhraní příkazového řádku Git, [si můžete přečíst zde.](https://guides.github.com/introduction/git-handbook/)

## <a name="track-code-that-comes-from-git-repositories"></a>Sledovat kód, který pochází z úložišť Git

Když odešlete školicí běh ze sady Python SDK nebo Machine Learning CLI, soubory potřebné pro výuku modelu se nahrají do vašeho pracovního prostoru. Pokud `git` je příkaz k dispozici ve vašem vývojovém prostředí, proces nahrávání je používá ke kontrole, zda jsou soubory uloženy v úložišti Git. V takovém případě jsou informace z úložiště Git také nahrány v rámci školicího běhu. Tyto informace jsou uloženy v následujících vlastnostech pro školicí běh:

| Vlastnost | Příkaz git použitý k získání hodnoty | Popis |
| ----- | ----- | ----- |
| `azureml.git.repository_uri` | `git ls-remote --get-url` | Identifikátor URI, ze kterého bylo úložiště naklonováno. |
| `mlflow.source.git.repoURL` | `git ls-remote --get-url` | Identifikátor URI, ze kterého bylo úložiště naklonováno. |
| `azureml.git.branch` | `git symbolic-ref --short HEAD` | Aktivní větev při odeslání běhu. |
| `mlflow.source.git.branch` | `git symbolic-ref --short HEAD` | Aktivní větev při odeslání běhu. |
| `azureml.git.commit` | `git rev-parse HEAD` | Hodnota hash potvrzení kódu, který byl odeslán pro spuštění. |
| `mlflow.source.git.commit` | `git rev-parse HEAD` | Hodnota hash potvrzení kódu, který byl odeslán pro spuštění. |
| `azureml.git.dirty` | `git status --porcelain .` | `True`, pokud je větev/potvrzení nečistá; v opačném případě `false` . |

Tyto informace se odesílají pro běhy, které používají Estimator, kanál strojového učení nebo spuštění skriptu.

Pokud vaše školicí soubory nejsou umístěny v úložišti Git ve vašem vývojovém prostředí nebo `git` příkaz není k dispozici, nebudou sledovány žádné informace týkající se Gitu.

> [!TIP]
> Pokud chcete zjistit, jestli je příkaz git ve vývojovém prostředí dostupný, otevřete relaci prostředí, příkazový řádek, PowerShell nebo jiné rozhraní příkazového řádku a zadejte tento příkaz:
>
> ```
> git --version
> ```
>
> V případě instalace a v cestě obdržíte odpověď podobnou této `git version 2.4.1` . Další informace o instalaci Gitu do vývojového prostředí najdete na [webu Git](https://git-scm.com/).

## <a name="view-the-logged-information"></a>Zobrazit protokolované informace

Informace Git se ukládají do vlastností pro školicí běh. Tyto informace můžete zobrazit pomocí Azure Portal, sady Python SDK a rozhraní příkazového řádku. 

### <a name="azure-portal"></a>portál Azure

1. V [Azure Portal](https://portal.azure.com)vyberte svůj pracovní prostor.
1. Vyberte __experimenty__a pak vyberte jeden z experimentů.
1. Vyberte jedno z běhů ze sloupce __číslo běhu__ .
1. Vyberte __protokoly__a potom rozbalte položky __protokoly__ a __AzureML__ . Vyberte odkaz, který začíná na __ ### \_ Azure__.

    ![Položka # # #_azure na portálu](./media/concept-train-model-git-integration/azure-machine-learning-logs.png)

Protokolované informace obsahují text podobný následujícímu formátu JSON:

```json
"properties": {
    "_azureml.ComputeTargetType": "batchai",
    "ContentSnapshotId": "5ca66406-cbac-4d7d-bc95-f5a51dd3e57e",
    "azureml.git.repository_uri": "git@github.com:azure/machinelearningnotebooks",
    "mlflow.source.git.repoURL": "git@github.com:azure/machinelearningnotebooks",
    "azureml.git.branch": "master",
    "mlflow.source.git.branch": "master",
    "azureml.git.commit": "4d2b93784676893f8e346d5f0b9fb894a9cf0742",
    "mlflow.source.git.commit": "4d2b93784676893f8e346d5f0b9fb894a9cf0742",
    "azureml.git.dirty": "True",
    "AzureML.DerivedImageName": "azureml/azureml_9d3568242c6bfef9631879915768deaf",
    "ProcessInfoFile": "azureml-logs/process_info.json",
    "ProcessStatusFile": "azureml-logs/process_status.json"
}
```

### <a name="python-sdk"></a>Python SDK

Po odeslání školicího běhu se vrátí objekt [Run](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py&preserve-view=true) . `properties`Atribut tohoto objektu obsahuje protokolované informace Gitu. Například následující kód načte hodnotu hash potvrzení:

```python
run.properties['azureml.git.commit']
```

### <a name="cli"></a>Rozhraní příkazového řádku

`az ml run`Příkaz CLI lze použít k načtení vlastností z běhu. Například následující příkaz vrátí vlastnosti posledního spuštění v experimentu s názvem `train-on-amlcompute` :

```azurecli-interactive
az ml run list -e train-on-amlcompute --last 1 -w myworkspace -g myresourcegroup --query '[].properties'
```

Další informace najdete v referenční dokumentaci [AZ ml Run](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/run?view=azure-cli-latest) reference.

## <a name="next-steps"></a>Další kroky

* [Použití výpočetních cílů pro školení modelů](how-to-set-up-training-targets.md)
