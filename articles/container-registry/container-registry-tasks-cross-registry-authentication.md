---
title: Ověřování mezi registry z úlohy ACR
description: Konfigurace úlohy registru kontejneru Azure (úloha ACR) pro přístup k jinému privátnímu registru kontejnerů Azure pomocí spravované identity pro prostředky Azure
ms.topic: article
ms.date: 01/14/2020
ms.openlocfilehash: 47b2a50784cf56b089fea0981e5a06d581b8ba3a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76842485"
---
# <a name="cross-registry-authentication-in-an-acr-task-using-an-azure-managed-identity"></a>Ověřování mezi registry v úloze ACR pomocí identity spravované Azure 

V [úloze ACR](container-registry-tasks-overview.md)můžete [povolit spravovanou identitu pro prostředky Azure](container-registry-tasks-authentication-managed-identity.md). Úloha můžete použít identitu pro přístup k jiným prostředkům Azure, bez nutnosti zadejte nebo spravovat přihlašovací údaje. 

V tomto článku se dozvíte, jak povolit spravovanou identitu v úloze k vytažení bitové kopie z registru, který se liší od obrázku použitého ke spuštění úlohy.

Chcete-li vytvořit prostředky Azure, tento článek vyžaduje spuštění Azure CLI verze 2.0.68 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI][azure-cli].

## <a name="scenario-overview"></a>Přehled scénáře

Ukázková úloha vytáhne základní image z jiného registru kontejneru Azure k sestavení a nabízení bitové kopie aplikace. Chcete-li vytáhnout základní bitovou kopii, nakonfigurujte úlohu se spravovanou identitou a přiřaďte jí příslušná oprávnění. 

Tento příklad ukazuje kroky pomocí uživatelem přiřazené nebo systémem přiřazené spravované identity. Vaše volba identity závisí na potřebách vaší organizace.

V reálném scénáři organizace může udržovat sadu základních bitových kopií používaných všechny vývojové týmy k vytváření jejich aplikací. Tyto základní image jsou uloženy v podnikovém registru, přičemž každý vývojový tým má pouze práva na vyžádat. 

## <a name="prerequisites"></a>Požadavky

Pro tento článek potřebujete dva registry kontejnerů Azure:

* První registr se používá k vytvoření a spuštění úloh ACR. V tomto článku tento registr se nazývá *myregistry*. 
* Druhý registr je hostitelem základní bitové kopie použité pro úlohu k vytvoření bitové kopie. V tomto článku druhý registr s názvem *mybaseregistry*. 

V pozdějších krocích nahraďte vlastní názvy registrů.

Pokud ještě nemáte potřebné registry kontejnerů Azure, přečtěte si [tématu Úvodní příručka: Vytvoření registru privátního kontejneru pomocí azure cli](container-registry-get-started-azure-cli.md). Obrázky ještě nemusíte do registru vysunout.

## <a name="prepare-base-registry"></a>Příprava základního registru

Nejprve vytvořte pracovní adresář a potom vytvořte soubor s názvem Dockerfile s následujícím obsahem. Tento jednoduchý příklad vytvoří základní bitovou kopii Node.js z veřejné image v Docker Hubu.
    
```bash
echo FROM node:9-alpine > Dockerfile
```
V aktuálním adresáři spusťte příkaz [az acr build][az-acr-build] a vytvořte a přesměrujte základní bitovou kopii do základního registru. V praxi může jiný tým nebo proces v organizaci udržovat základní registr.
    
```azurecli
az acr build --image baseimages/node:9-alpine --registry mybaseregistry --file Dockerfile .
```

## <a name="define-task-steps-in-yaml-file"></a>Definování kroků úloh v souboru YAML

Kroky pro tento příklad [vícekrokové úlohy](container-registry-tasks-multi-step.md) jsou definovány v [souboru YAML](container-registry-tasks-reference-yaml.md). Vytvořte soubor `helloworldtask.yaml` pojmenovaný v místním pracovním adresáři a vložte následující obsah. Aktualizujte hodnotu `REGISTRY_NAME` v kroku sestavení názvem serveru základního registru.

```yml
version: v1.1.0
steps:
# Replace mybaseregistry with the name of your registry containing the base image
  - build: -t $Registry/hello-world:$ID  https://github.com/Azure-Samples/acr-build-helloworld-node.git -f Dockerfile-app --build-arg REGISTRY_NAME=mybaseregistry.azurecr.io
  - push: ["$Registry/hello-world:$ID"]
```

Krok sestavení používá `Dockerfile-app` soubor v [Azure-Samples/acr-build-helloworld-node](https://github.com/Azure-Samples/acr-build-helloworld-node.git) repo k vytvoření image. Odkazuje `--build-arg` na základní registru vytáhnout základní bitové kopie. Po úspěšném spuštění je bitová kopie zasunuta do registru použitého ke spuštění úlohy.

## <a name="option-1-create-task-with-user-assigned-identity"></a>Možnost 1: Vytvoření úkolu s identitou přiřazenou uživatelem

Kroky v této části vytvoří úlohu a povolí identitu přiřazenou uživateli. Pokud chcete místo toho povolit identitu přiřazenou systémem, [přečtěte si informace o možnosti 2: Vytvoření úlohy se systémem přiřazenou identitou](#option-2-create-task-with-system-assigned-identity). 

[!INCLUDE [container-registry-tasks-user-assigned-id](../../includes/container-registry-tasks-user-assigned-id.md)]

### <a name="create-task"></a>Vytvořit úkol

Vytvořte úlohu *helloworldtask* provedením následující [az acr úkol vytvořit][az-acr-task-create] příkaz. Úloha je spuštěna bez kontextu zdrojového kódu `helloworldtask.yaml` a příkaz odkazuje na soubor v pracovním adresáři. Parametr `--assign-identity` předá ID prostředku uživatelem přiřazené identity. 

```azurecli
az acr task create \
  --registry myregistry \
  --name helloworldtask \
  --context /dev/null \
  --file helloworldtask.yaml \
  --assign-identity $resourceID
```

[!INCLUDE [container-registry-tasks-user-id-properties](../../includes/container-registry-tasks-user-id-properties.md)]

## <a name="option-2-create-task-with-system-assigned-identity"></a>Možnost 2: Vytvoření úlohy se systémem přiřazenou identitou

Kroky v této části vytvoří úlohu a povolí identitu přiřazenou systémem. Pokud chcete místo toho povolit identitu přiřazenou uživateli, [přečtěte si informace o možnosti 1: Vytvoření úlohy s identitou přiřazenou uživatelem](#option-1-create-task-with-user-assigned-identity). 

### <a name="create-task"></a>Vytvořit úkol

Vytvořte úlohu *helloworldtask* provedením následující [az acr úkol vytvořit][az-acr-task-create] příkaz. Úloha je spuštěna bez kontextu zdrojového kódu `helloworldtask.yaml` a příkaz odkazuje na soubor v pracovním adresáři. Parametr `--assign-identity` bez hodnoty umožňuje systémem přiřazenou identitu na úkolu. 

```azurecli
az acr task create \
  --registry myregistry \
  --name helloworldtask \
  --context /dev/null \
  --file helloworldtask.yaml \
  --assign-identity 
```
[!INCLUDE [container-registry-tasks-system-id-properties](../../includes/container-registry-tasks-system-id-properties.md)]

## <a name="give-identity-pull-permissions-to-the-base-registry"></a>Udělení oprávnění k vyžádat identitu základnímu registru

V této části udělte oprávnění spravované identity k vyžádat ze základního registru *mybaseregistry*.

Pomocí příkazu [az acr show][az-acr-show] získáte ID prostředku základního registru a uložte jej do proměnné:

```azurecli
baseregID=$(az acr show --name mybaseregistry --query id --output tsv)
```

Pomocí příkazu [az role create][az-role-assignment-create] přiřaďte identitu `acrpull` roli základnímu registru. Tato role má oprávnění pouze k vytahování bitových kopií z registru.

```azurecli
az role assignment create \
  --assignee $principalID \
  --scope $baseregID \
  --role acrpull
```

## <a name="add-target-registry-credentials-to-task"></a>Přidání cílových pověření registru k úkolu

Nyní použijte příkaz [add úlohy az acr,][az-acr-task-credential-add] který umožní, aby se úloha ověřuje pomocí základního registru pomocí pověření identity. Spusťte příkaz odpovídající typu spravované identity, kterou jste v úloze povolili. Pokud jste povolili identitu přiřazenou uživateli, předavěte `--use-identity` ji s ID klienta. Pokud jste povolili systémově přiřazenou identitu, předaj . `--use-identity [system]`

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

## <a name="manually-run-the-task"></a>Ruční spuštění úlohy

Chcete-li ověřit, zda úloha, ve které jste povolili spravovanou identitu, úspěšně spuštěna, ručně spusťte úlohu pomocí příkazu [az acr task run.][az-acr-task-run] 

```azurecli
az acr task run \
  --name helloworldtask \
  --registry myregistry
```

Pokud se úloha úspěšně spustí, výstup je podobný:

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
Step 2/6 : FROM ${REGISTRY_NAME}/baseimages/node:9-alpine
9-alpine: Pulling from baseimages/node
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
    tag: 9-alpine
    digest: sha256:e8e92cffd464fce3be9a3eefd1b65dc9cbe2484da31c11e813a4effc6105c00f
  git:
    git-head-revision: 0f988779c97fe0bfc7f2f74b88531617f4421643

Run ID: cf10 was successful after 32s
```

Spusťte příkaz [az acr repository show-tags][az-acr-repository-show-tags] a ověřte, zda byl obraz vytvořen a úspěšně zatlačen do *registru myregistry*:

```azurecli
az acr repository show-tags --name myregistry --repository hello-world --output tsv
```

Příklad výstupu:

```console
cf10
```

## <a name="next-steps"></a>Další kroky

* Další informace o [povolení spravované identity v úloze ACR](container-registry-tasks-authentication-managed-identity.md).
* Zobrazit [odkaz YAML úkolů ACR](container-registry-tasks-reference-yaml.md)

<!-- LINKS - Internal -->
[az-login]: /cli/azure/reference-index#az-login
[az-acr-login]: /cli/azure/acr#az-acr-login
[az-acr-show]: /cli/azure/acr#az-acr-show
[az-acr-build]: /cli/azure/acr#az-acr-build
[az-acr-repository-show-tags]: /cli/azure/acr/repository#az-acr-repository-show-tags
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[az-acr-login]: /cli/azure/acr#az-acr-login
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-task-create]: /cli/azure/acr/task#az-acr-task-create
[az-acr-task-show]: /cli/azure/acr/task#az-acr-task-show
[az-acr-task-run]: /cli/azure/acr/task#az-acr-task-run
[az-acr-task-list-runs]: /cli/azure/acr/task#az-acr-task-list-runs
[az-acr-task-credential-add]: /cli/azure/acr/task/credential#az-acr-task-credential-add
[az-group-create]: /cli/azure/group?#az-group-create
