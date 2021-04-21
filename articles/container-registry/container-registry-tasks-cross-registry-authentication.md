---
title: Ověřování mezi registry z ACR úlohy
description: Konfigurace úlohy Azure Container Registry (úloha ACR) pro přístup k jinému privátnímu registru služby Azure Container Registry pomocí spravované identity pro prostředky Azure
ms.topic: article
ms.date: 07/06/2020
ms.openlocfilehash: a9b70a44de0cfccb9a61bc24575281e440db6e32
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/20/2021
ms.locfileid: "107781110"
---
# <a name="cross-registry-authentication-in-an-acr-task-using-an-azure-managed-identity"></a>Ověřování mezi registry v úloze ACR pomocí identity spravované službou Azure 

V [úloze ACR](container-registry-tasks-overview.md)můžete [Povolit spravovanou identitu pro prostředky Azure](container-registry-tasks-authentication-managed-identity.md). Tato úloha může používat identitu pro přístup k dalším prostředkům Azure, aniž byste museli zadávat nebo spravovat přihlašovací údaje. 

V tomto článku se dozvíte, jak v úloze povolit spravovanou identitu pro vyžádání image z registru, který se liší od toho, který se používá ke spuštění úlohy.

Tento článek vyžaduje, abyste spustili Azure CLI verze 2.0.68 nebo novější, abyste mohli vytvořit prostředky Azure. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI][azure-cli].

## <a name="scenario-overview"></a>Přehled scénáře

Ukázkový úkol vyžádá základní image z jiného služby Azure Container registry pro sestavení a vložení image aplikace. Pokud chcete načíst základní image, nakonfigurujete úlohu se spravovanou identitou a přiřadíte jí patřičná oprávnění. 

Tento příklad ukazuje kroky buď pomocí uživatelsky přiřazené nebo spravované identity přiřazené systémem. Vaše volba identity závisí na potřebách vaší organizace.

V reálném scénáři může organizace udržovat sadu základních imagí, které používají všechny vývojové týmy pro vytváření aplikací. Tyto základní image jsou uložené v podnikovém registru, přičemž každý vývojový tým má jenom práva k získání. 

## <a name="prerequisites"></a>Požadavky

Pro tento článek potřebujete dva služby Azure Container Registry:

* Pomocí prvního registru můžete vytvářet a spouštět úlohy ACR. V tomto článku má tento registr název *myregistry*. 
* Druhý registr hostuje základní image, která se používá pro úkol k sestavení image. V tomto článku má druhý registr název *mybaseregistry*. 

Nahraďte vlastními názvy registru v pozdějších krocích.

Pokud ještě nemáte potřebné registry kontejnerů Azure, přečtěte si téma [rychlý Start: Vytvoření privátního registru kontejnerů pomocí Azure CLI](container-registry-get-started-azure-cli.md). Image ještě nemusíte vkládat do registru.

## <a name="prepare-base-registry"></a>Příprava základního registru

Pro demonstrační účely, jako jednorázovou operaci, spusťte příkaz [az ACR Import] [az-ACR-Import], abyste importovali veřejnou Node.js image z Docker Hub do svého základního registru. V praxi může jiný tým nebo proces v organizaci uchovávat image v základním registru.

```azurecli
az acr import --name mybaseregistry \
  --source docker.io/library/node:15-alpine \
  --image baseimages/node:15-alpine 
```

## <a name="define-task-steps-in-yaml-file"></a>Definování kroků úkolu v souboru YAML

Kroky pro tento příklad [úlohy s více kroky](container-registry-tasks-multi-step.md) jsou definovány v [souboru YAML](container-registry-tasks-reference-yaml.md). Vytvořte soubor s názvem `helloworldtask.yaml` v místním pracovním adresáři a vložte následující obsah. Aktualizujte hodnotu `REGISTRY_NAME` v kroku sestavení s názvem serveru vašeho základního registru.

```yml
version: v1.1.0
steps:
# Replace mybaseregistry with the name of your registry containing the base image
  - build: -t $Registry/hello-world:$ID  https://github.com/Azure-Samples/acr-build-helloworld-node.git#main -f Dockerfile-app --build-arg REGISTRY_NAME=mybaseregistry.azurecr.io
  - push: ["$Registry/hello-world:$ID"]
```

Krok sestavení používá `Dockerfile-app` soubor v úložišti [Azure-Samples/ACR-Build-HelloWorld-Node](https://github.com/Azure-Samples/acr-build-helloworld-node.git) k sestavení image. `--build-arg`Odkazuje na základní registr pro získání základní image. Po úspěšném sestavení se obrázek vloží do registru, který se používá ke spuštění úlohy.

## <a name="option-1-create-task-with-user-assigned-identity"></a>Možnost 1: vytvoření úlohy s identitou přiřazenou uživatelem

Kroky v této části vytvoří úlohu a umožní uživateli přiřazenou identitu. Pokud místo toho chcete povolit identitu přiřazenou systémem, přečtěte si část [možnost 2: vytvoření úlohy s identitou přiřazenou systémem](#option-2-create-task-with-system-assigned-identity). 

[!INCLUDE [container-registry-tasks-user-assigned-id](../../includes/container-registry-tasks-user-assigned-id.md)]

### <a name="create-task"></a>Vytvořit úlohu

Vytvořte úlohu *helloworldtask* spuštěním následujícího příkazu [AZ ACR Task Create][az-acr-task-create] . Úloha se spouští bez kontextu zdrojového kódu a příkaz odkazuje na soubor `helloworldtask.yaml` v pracovním adresáři. `--assign-identity`Parametr předá ID prostředku identity přiřazené uživatelem. 

```azurecli
az acr task create \
  --registry myregistry \
  --name helloworldtask \
  --context /dev/null \
  --file helloworldtask.yaml \
  --assign-identity $resourceID
```

[!INCLUDE [container-registry-tasks-user-id-properties](../../includes/container-registry-tasks-user-id-properties.md)]

### <a name="give-identity-pull-permissions-to-the-base-registry"></a>Udělení oprávnění pro získání identity základnímu registru

V této části udělte spravované identitě oprávnění k vyžádání ze základního registru *mybaseregistry*.

Pomocí příkazu [AZ ACR show][az-acr-show] Získejte ID prostředku základního registru a uložte ho do proměnné:

```azurecli
baseregID=$(az acr show --name mybaseregistry --query id --output tsv)
```

Pomocí příkazu [AZ role Assignment Create][az-role-assignment-create] přiřaďte identitu `acrpull` roli k základnímu registru. Tato role má oprávnění pouze k vyžádání imagí z registru.

```azurecli
az role assignment create \
  --assignee $principalID \
  --scope $baseregID \
  --role acrpull
```

Pokračujte [přidáním přihlašovacích údajů cílového registru do úlohy](#add-target-registry-credentials-to-task).

## <a name="option-2-create-task-with-system-assigned-identity"></a>Možnost 2: vytvoření úlohy s identitou přiřazenou systémem

Kroky v této části vytvoří úlohu a povolí identitu přiřazenou systémem. Pokud místo toho chcete povolit uživatelsky přiřazenou identitu, přečtěte si část [možnost 1: vytvoření úlohy s identitou přiřazenou uživatelem](#option-1-create-task-with-user-assigned-identity). 

### <a name="create-task"></a>Vytvořit úlohu

Vytvořte úlohu *helloworldtask* spuštěním následujícího příkazu [AZ ACR Task Create][az-acr-task-create] . Úloha se spouští bez kontextu zdrojového kódu a příkaz odkazuje na soubor `helloworldtask.yaml` v pracovním adresáři. `--assign-identity`Parametr bez hodnoty umožňuje systémem přiřazenou identitu daného úkolu. 

```azurecli
az acr task create \
  --registry myregistry \
  --name helloworldtask \
  --context /dev/null \
  --file helloworldtask.yaml \
  --assign-identity 
```
[!INCLUDE [container-registry-tasks-system-id-properties](../../includes/container-registry-tasks-system-id-properties.md)]

### <a name="give-identity-pull-permissions-to-the-base-registry"></a>Udělení oprávnění pro získání identity základnímu registru

V této části udělte spravované identitě oprávnění k vyžádání ze základního registru *mybaseregistry*.

Pomocí příkazu [AZ ACR show][az-acr-show] Získejte ID prostředku základního registru a uložte ho do proměnné:

```azurecli
baseregID=$(az acr show --name mybaseregistry --query id --output tsv)
```

Pomocí příkazu [AZ role Assignment Create][az-role-assignment-create] přiřaďte identitu `acrpull` roli k základnímu registru. Tato role má oprávnění pouze k vyžádání imagí z registru.

```azurecli
az role assignment create \
  --assignee $principalID \
  --scope $baseregID \
  --role acrpull
```

## <a name="add-target-registry-credentials-to-task"></a>Přidat do úlohy přihlašovací údaje pro cílový registr

Nyní pomocí příkazu [AZ ACR Task Credential Add přidejte][az-acr-task-credential-add] úlohu k ověření se základním registrem pomocí přihlašovacích údajů identity. Spusťte příkaz odpovídající typu spravované identity, který jste povolili v úloze. Pokud jste povolili identitu přiřazenou uživatelem, předejte jí `--use-identity` ID klienta identity. Pokud jste povolili identitu přiřazenou systémem, předejte `--use-identity [system]` .

```azurecli
# Add credentials for user-assigned identity to the task
az acr task credential add \
  --name helloworldtask \
  --registry myregistry \
  --login-server mybaseregistry.azurecr.io \
  --use-identity $clientID

# Add credentials for system-assigned identity to the task
az acr task credential add \
  --name helloworldtask \
  --registry myregistry \
  --login-server mybaseregistry.azurecr.io \
  --use-identity [system]
```

## <a name="manually-run-the-task"></a>Ručně spustit úlohu

Chcete-li ověřit, zda je úloha, ve které jste povolili spravovanou identitu, úspěšně spuštěna, spusťte úlohu ručně pomocí příkazu [AZ ACR Task Run][az-acr-task-run] . 

```azurecli
az acr task run \
  --name helloworldtask \
  --registry myregistry
```

Pokud je úloha úspěšně spuštěna, výstup je podobný následujícímu:

```
Queued a run with ID: cf10
Waiting for an agent...
2019/06/14 22:47:32 Using acb_vol_dbfbe232-fd76-4ca3-bd4a-687e84cb4ce2 as the home volume
2019/06/14 22:47:39 Creating Docker network: acb_default_network, driver: 'bridge'
2019/06/14 22:47:40 Successfully set up Docker network: acb_default_network
2019/06/14 22:47:40 Setting up Docker configuration...
2019/06/14 22:47:41 Successfully set up Docker configuration
2019/06/14 22:47:41 Logging in to registry: myregistry.azurecr.io
2019/06/14 22:47:42 Successfully logged into myregistry.azurecr.io
2019/06/14 22:47:42 Logging in to registry: mybaseregistry.azurecr.io
2019/06/14 22:47:43 Successfully logged into mybaseregistry.azurecr.io
2019/06/14 22:47:43 Executing step ID: acb_step_0. Timeout(sec): 600, Working directory: '', Network: 'acb_default_network'
2019/06/14 22:47:43 Scanning for dependencies...
2019/06/14 22:47:45 Successfully scanned dependencies
2019/06/14 22:47:45 Launching container with name: acb_step_0
Sending build context to Docker daemon   25.6kB
Step 1/6 : ARG REGISTRY_NAME
Step 2/6 : FROM ${REGISTRY_NAME}/baseimages/node:15-alpine
15-alpine: Pulling from baseimages/node
[...]
Successfully built 41b49a112663
Successfully tagged myregistry.azurecr.io/hello-world:cf10
2019/06/14 22:47:56 Successfully executed container: acb_step_0
2019/06/14 22:47:56 Executing step ID: acb_step_1. Timeout(sec): 600, Working directory: '', Network: 'acb_default_network'
2019/06/14 22:47:56 Pushing image: myregistry.azurecr.io/hello-world:cf10, attempt 1
The push refers to repository [myregistry.azurecr.io/hello-world]
[...]
2019/06/14 22:48:00 Step ID: acb_step_1 marked as successful (elapsed time in seconds: 2.517011)
2019/06/14 22:48:00 The following dependencies were found:
2019/06/14 22:48:00
- image:
    registry: myregistry.azurecr.io
    repository: hello-world
    tag: cf10
    digest: sha256:611cf6e3ae3cb99b23fadcd89fa144e18aa1b1c9171ad4a0da4b62b31b4e38d1
  runtime-dependency:
    registry: mybaseregistry.azurecr.io
    repository: baseimages/node
    tag: 15-alpine
    digest: sha256:e8e92cffd464fce3be9a3eefd1b65dc9cbe2484da31c11e813a4effc6105c00f
  git:
    git-head-revision: 0f988779c97fe0bfc7f2f74b88531617f4421643

Run ID: cf10 was successful after 32s
```

Spuštěním příkazu [AZ ACR úložiště show-Tags][az-acr-repository-show-tags] ověřte, že se image sestavila a byla úspěšně vložena do *myregistry*:

```azurecli
az acr repository show-tags --name myregistry --repository hello-world --output tsv
```

Příklad výstupu:

```console
cf10
```

## <a name="next-steps"></a>Další kroky

* Přečtěte si další informace o [Povolení spravované identity v ACR úloze](container-registry-tasks-authentication-managed-identity.md).
* Další informace najdete v tématu [ACR Tasks YAML reference](container-registry-tasks-reference-yaml.md)

<!-- LINKS - Internal -->
[az-login]: /cli/azure/reference-index#az_login
[az-acr-login]: /cli/azure/acr#az_acr_login
[az-acr-show]: /cli/azure/acr#az_acr_show
[az-acr-build]: /cli/azure/acr#az_acr_build
[az-acr-repository-show-tags]: /cli/azure/acr/repository#az_acr_repository_show_tags
[az-role-assignment-create]: /cli/azure/role/assignment#az_role_assignment_create
[az-acr-login]: /cli/azure/acr#az_acr_login
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-task-create]: /cli/azure/acr/task#az_acr_task_create
[az-acr-task-show]: /cli/azure/acr/task#az_acr_task_show
[az-acr-task-run]: /cli/azure/acr/task#az_acr_task_run
[az-acr-task-list-runs]: /cli/azure/acr/task#az_acr_task_list_runs
[az-acr-task-credential-add]: /cli/azure/acr/task/credential#az_acr_task_credential_add
[az-group-create]: /cli/azure/group?#az_group_create
