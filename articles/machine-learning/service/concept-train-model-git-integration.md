---
title: Integrace Gitu pro Azure Machine Learning
titleSuffix: Azure Machine Learning
description: Přečtěte si, jak Azure Machine Learning integrovat s místním úložištěm Git.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: jordane
author: jpe316
ms.date: 10/11/2019
ms.openlocfilehash: db96663ef3d901546e1b32362a9eb9c9ae09dd21
ms.sourcegitcommit: 0576bcb894031eb9e7ddb919e241e2e3c42f291d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/15/2019
ms.locfileid: "72377509"
---
# <a name="git-integration-for-azure-machine-learning"></a>Integrace Gitu pro Azure Machine Learning

[Git](https://git-scm.com/) je oblíbený systém pro správu verzí, který umožňuje sdílení a spolupráci s projekty. Při odeslání školicí úlohy do Azure Machine Learning, pokud jsou školicí soubory uložené v místním úložišti Git, informace o úložišti se sledují jako součást procesu školení.

Vzhledem k tomu, že Azure Machine Learning sleduje informace z místního úložiště Git, není svázán s žádným konkrétním centrálním úložištěm. Vaše úložiště je možné klonovat z GitHubu, GitLab, BitBucket, Azure DevOps nebo jakékoli jiné služby kompatibilní s Git.

## <a name="how-does-git-integration-work"></a>Jak funguje integrace Gitu?

Když odešlete školicí běh ze sady Python SDK nebo Machine Learning CLI, soubory potřebné pro výuku modelu se nahrají do vašeho pracovního prostoru. Pokud je ve vašem vývojovém prostředí k dispozici příkaz `git`, proces nahrávání ho použije ke kontrole, zda jsou soubory uloženy v úložišti Git. V takovém případě jsou informace z úložiště Git také nahrány v rámci školicího běhu. Tyto informace jsou uloženy v následujících vlastnostech pro školicí běh:

| Vlastnost | Popis |
| ----- | ----- |
| `azureml.git.repository_uri` | Identifikátor URI, ze kterého bylo úložiště naklonováno. |
| `mlflow.source.git.repoURL` | Identifikátor URI, ze kterého bylo úložiště naklonováno. |
| `azureml.git.branch` | Aktivní větev při odeslání běhu. |
| `mlflow.source.git.branch` | Aktivní větev při odeslání běhu. |
| `azureml.git.commit` | Hodnota hash potvrzení kódu, který byl odeslán pro spuštění. |
| `mlflow.source.git.commit` | Hodnota hash potvrzení kódu, který byl odeslán pro spuštění. |
| `azureml.git.dirty` | `True`, pokud je potvrzení nečisté; v opačném případě `false`. |

Tyto informace se odesílají pro běhy, které používají Estimator, kanál strojového učení nebo spuštění skriptu.

Pokud vaše školicí soubory nejsou umístěny v úložišti Git ve vašem vývojovém prostředí, nebo příkaz `git` není k dispozici, nebudou sledovány žádné informace týkající se Gitu.

## <a name="view-the-logged-information"></a>Zobrazit protokolované informace

Informace Git se ukládají do vlastností pro školicí běh. Tyto informace můžete zobrazit pomocí Azure Portal, sady Python SDK a rozhraní příkazového řádku. 

### <a name="azure-portal"></a>Portál Azure

1. V [Azure Portal](https://portal.azure.com)vyberte svůj pracovní prostor.
1. Vyberte __experimenty__a pak vyberte jeden z experimentů.
1. Vyberte jedno z běhů ze sloupce __číslo běhu__ .
1. Vyberte __protokoly__a potom rozbalte položky __protokoly__ a __AzureML__ . Vyberte odkaz, který začíná na __### @ no__t-2azure__.

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

Po odeslání školicího běhu se vrátí objekt [Run](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py) . Atribut `properties` tohoto objektu obsahuje protokolované informace Gitu. Například následující kód načte hodnotu hash potvrzení:

```python
run.properties['azureml.git.commit']
```

### <a name="cli"></a>Rozhraní příkazového řádku

Příkaz `az ml run` CLI lze použít k načtení vlastností ze spuštění. Například následující příkaz vrátí vlastnosti posledního spuštění v experimentu s názvem `train-on-amlcompute`:

```azurecli-interactive
az ml run list -e train-on-amlcompute --last 1 -w myworkspace -g myresourcegroup --query '[].properties'
```

Další informace najdete v referenční dokumentaci [AZ ml Run](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/run?view=azure-cli-latest) reference.

## <a name="next-steps"></a>Další kroky

* Návod, jak naučit se Azure Machine Learning v Visual Studio Code, najdete v tématu [kurz: výuka modelů pomocí Azure Machine Learning](tutorial-train-models-with-aml.md).
* Návod, jak upravovat, spouštět a ladit kód místně, najdete v [kurzu Python Hello World](https://code.visualstudio.com/docs/Python/Python-tutorial).
