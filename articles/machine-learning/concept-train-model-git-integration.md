---
title: Integrace Gitu pro Azure Machine Learning
titleSuffix: Azure Machine Learning
description: Zjistěte, jak se Azure Machine Learning integruje s místním úložištěm Git. Při odesílání školení spustit z místního adresáře, který je úložiště Git, informace o úložiště, větev a aktuální potvrzení jsou sledovány jako součást spuštění.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: jordane
author: jpe316
ms.date: 03/05/2020
ms.openlocfilehash: 7cc2e346a35cd1cdf1278b527dc451a903d60f89
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78402824"
---
# <a name="git-integration-for-azure-machine-learning"></a>Integrace Gitu pro Azure Machine Learning

[Git](https://git-scm.com/) je populární systém správy verzí, který umožňuje sdílet a spolupracovat na vašich projektech. 

Azure Machine Learning plně podporuje úložiště Git pro sledování práce – můžete klonovat úložiště přímo do sdíleného systému souborů pracovního prostoru, používat Git na místní pracovní stanici nebo používat Git z kanálu CI/CD.

Při odesílání úlohy do Azure Machine Learning, pokud zdrojové soubory jsou uloženy v místním úložišti git pak informace o úložišti je sledována jako součást procesu školení.

Vzhledem k tomu, že Azure Machine Learning sleduje informace z místního úložiště git, není vázána na žádné konkrétní centrální úložiště. Vaše úložiště lze klonovat z GitHub, GitLab, Bitbucket, Azure DevOps nebo jakékoli jiné služby kompatibilní s gitem.

## <a name="clone-git-repositories-into-your-workspace-file-system"></a>Klonování úložišť Git do systému souborů pracovního prostoru
Azure Machine Learning poskytuje sdílený systém souborů pro všechny uživatele v pracovním prostoru.
Chcete-li naklonovat úložiště Git do této sdílené složky, doporučujeme vytvořit výpočetní instanci & otevřít terminál.
Po otevření terminálu máte přístup k úplnému klientovi Git u rychlého zákazníka a můžete s Ním klonovat a pracovat s ním prostřednictvím prostředí Git CLI.

Doporučujeme naklonovat úložiště do adresáře uživatelů tak, aby ostatní nebudou kolize přímo na pracovní větev.

Můžete klonovat libovolné úložiště Git, na které můžete ověřit (GitHub, Azure Repos, BitBucket atd.)

Průvodce používáním rozhraní G6 naleznete [zde](https://guides.github.com/introduction/git-handbook/).

## <a name="track-code-that-comes-from-git-repositories"></a>Sledování kódu, který pochází z úložišť Git

Když odešlete trénovací běh z pythonu SDK nebo Machine Learning CLI, soubory potřebné k trénování modelu se nahrají do vašeho pracovního prostoru. Pokud `git` je příkaz k dispozici ve vývojovém prostředí, proces nahrávání jej používá ke kontrole, zda jsou soubory uloženy v úložišti git. Pokud ano, pak informace z vašeho git úložiště je také nahrán jako součást tréninkového běhu. Tyto informace jsou uloženy v následujících vlastnostech pro spuštění školení:

| Vlastnost | Příkaz Git použitý k získání hodnoty | Popis |
| ----- | ----- | ----- |
| `azureml.git.repository_uri` | `git ls-remote --get-url` | Identifikátor URI, ze kterého bylo vaše úložiště klonováno. |
| `mlflow.source.git.repoURL` | `git ls-remote --get-url` | Identifikátor URI, ze kterého bylo vaše úložiště klonováno. |
| `azureml.git.branch` | `git symbolic-ref --short HEAD` | Aktivní větev při odeslání spuštění. |
| `mlflow.source.git.branch` | `git symbolic-ref --short HEAD` | Aktivní větev při odeslání spuštění. |
| `azureml.git.commit` | `git rev-parse HEAD` | Hash potvrzení kódu, který byl odeslán pro spuštění. |
| `mlflow.source.git.commit` | `git rev-parse HEAD` | Hash potvrzení kódu, který byl odeslán pro spuštění. |
| `azureml.git.dirty` | `git status --porcelain .` | `True`, pokud je větev/commit znečištěná; v `false`opačném případě . |

Tyto informace jsou odesílány pro spuštění, které používají odhad, kanál strojového učení nebo spuštění skriptu.

Pokud vaše školicí soubory nejsou umístěny v úložišti `git` git ve vývojovém prostředí nebo příkaz není k dispozici, pak jsou sledovány žádné informace týkající se gitu.

> [!TIP]
> Chcete-li zkontrolovat, zda je příkaz git k dispozici ve vývojovém prostředí, otevřete relaci prostředí, příkazový řádek, prostředí PowerShell nebo jiné rozhraní příkazového řádku a zadejte následující příkaz:
>
> ```
> git --version
> ```
>
> Pokud je nainstalován a v cestě, obdržíte odpověď podobnou `git version 2.4.1`. Další informace o instalaci gitu do vývojového prostředí najdete na [webu Git](https://git-scm.com/).

## <a name="view-the-logged-information"></a>Zobrazit protokolované informace

Informace git jsou uloženy ve vlastnostech pro trénovací běh. Tyto informace můžete zobrazit pomocí portálu Azure, Python SDK a CLI. 

### <a name="azure-portal"></a>portál Azure

1. Na [portálu Azure](https://portal.azure.com)vyberte svůj pracovní prostor.
1. Vyberte __Experimenty__a pak vyberte jeden z experimentů.
1. Ve sloupci RUN __NUMBER__ vyberte jeden z běhů.
1. Vyberte __protokoly__a rozbalte __protokoly__ a __položky azureml.__ Vyberte odkaz, který začíná __ ### \_azure__.

    ![Položka ###_azure na portálu](./media/concept-train-model-git-integration/azure-machine-learning-logs.png)

Protokolované informace obsahují text podobný následujícímu JSON:

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

Po odeslání trénovací spuštění je vrácen objekt [Run.](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py) Atribut `properties` tohoto objektu obsahuje protokolované informace git. Například následující kód načte hash potvrzení:

```python
run.properties['azureml.git.commit']
```

### <a name="cli"></a>Rozhraní příkazového řádku

Příkaz `az ml run` příkazu příkazu cli lze použít k načtení vlastností z běhu. Například následující příkaz vrátí vlastnosti pro poslední spuštění `train-on-amlcompute`experimentu s názvem :

```azurecli-interactive
az ml run list -e train-on-amlcompute --last 1 -w myworkspace -g myresourcegroup --query '[].properties'
```

Další informace naleznete v referenční dokumentaci [ke spuštění az ml.](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/run?view=azure-cli-latest)

## <a name="next-steps"></a>Další kroky

* [Nastavení a použití výpočetních cílů pro trénování modelu](how-to-set-up-training-targets.md)
