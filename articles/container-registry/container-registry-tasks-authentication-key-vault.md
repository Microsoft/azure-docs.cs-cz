---
title: Externí ověřování z ACR úlohy
description: Nakonfigurujte úlohu Azure Container Registry (úkol ACR) pro čtení přihlašovacích údajů služby Docker, které jsou uložené v trezoru klíčů Azure, pomocí spravované identity pro prostředky Azure.
ms.topic: article
ms.date: 07/06/2020
ms.openlocfilehash: 0bc43f958a14016146160a06372af0b36a9fff75
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "86058125"
---
# <a name="external-authentication-in-an-acr-task-using-an-azure-managed-identity"></a>Externí ověřování v úloze ACR s využitím identity spravované službou Azure 

V [úloze ACR](container-registry-tasks-overview.md)můžete [Povolit spravovanou identitu pro prostředky Azure](container-registry-tasks-authentication-managed-identity.md). Tato úloha může používat identitu pro přístup k dalším prostředkům Azure, aniž byste museli zadávat nebo spravovat přihlašovací údaje. 

V tomto článku se dozvíte, jak povolit spravovanou identitu v úloze, která přistupuje k tajným klíčům uloženým v trezoru klíčů Azure. 

Tento článek vyžaduje, abyste spustili Azure CLI verze 2.0.68 nebo novější, abyste mohli vytvořit prostředky Azure. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI][azure-cli].

## <a name="scenario-overview"></a>Přehled scénáře

Ukázkový úkol načte přihlašovací údaje Docker Hub uložené v trezoru klíčů Azure. Přihlašovací údaje jsou pro účet Docker Hub s oprávněním Write (push) do úložiště privátního centra Docker. Pokud chcete přihlašovací údaje přečíst, nakonfigurujete úlohu se spravovanou identitou a přiřadíte jí patřičná oprávnění. Úloha přidružená k identitě sestaví image a přihlásí do Docker Hub, aby nastavila image do soukromého úložiště. 

Tento příklad ukazuje kroky buď pomocí uživatelsky přiřazené nebo spravované identity přiřazené systémem. Vaše volba identity závisí na potřebách vaší organizace.

Ve skutečném scénáři může společnost publikovat image do soukromého úložiště v Docker Hub jako součást procesu sestavení. 

## <a name="prerequisites"></a>Předpoklady

Potřebujete službu Azure Container Registry, ve které úlohu spouštíte. V tomto článku má tento registr název *myregistry*. Nahraďte vlastním názvem registru v pozdějších krocích.

Pokud ještě nemáte službu Azure Container Registry, přečtěte si téma [rychlý Start: Vytvoření privátního registru kontejnerů pomocí Azure CLI](container-registry-get-started-azure-cli.md). Image ještě nemusíte vkládat do registru.

Budete také potřebovat privátní úložiště v Docker Hub a účet Docker s oprávněními k zápisu do úložiště. V tomto příkladu má toto úložiště název *hubuser/hubrepo*. 

## <a name="create-a-key-vault-and-store-secrets"></a>Vytvoření trezoru klíčů a uložení tajných kódů

Nejdřív v případě potřeby vytvořte skupinu prostředků s názvem *myResourceGroup* v umístění *eastus* pomocí následujícího příkazu [AZ Group Create][az-group-create] :

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Pomocí příkazu [AZ datatrezor Create][az-keyvault-create] vytvořte Trezor klíčů. Nezapomeňte zadat jedinečný název trezoru klíčů. 

```azurecli-interactive
az keyvault create --name mykeyvault --resource-group myResourceGroup --location eastus
```

V trezoru klíčů uložte požadované přihlašovací údaje k Docker Hub pomocí příkazu [AZ Key trezor tajné sady][az-keyvault-secret-set] . V těchto příkazech jsou hodnoty předány do proměnných prostředí:

```azurecli
# Store Docker Hub user name
az keyvault secret set \
  --name UserName \
  --value $USERNAME \
  --vault-name mykeyvault

# Store Docker Hub password
az keyvault secret set \
  --name Password \
  --value $PASSWORD \
  --vault-name mykeyvault
```

V reálném scénáři by tajné klíče byly pravděpodobně nastaveny a udržovány v samostatném procesu.

## <a name="define-task-steps-in-yaml-file"></a>Definování kroků úkolu v souboru YAML

Kroky pro tento příklad úlohy jsou definovány v [souboru YAML](container-registry-tasks-reference-yaml.md). Vytvořte soubor s názvem `dockerhubtask.yaml` v místním pracovním adresáři a vložte následující obsah. Nezapomeňte nahradit název trezoru klíčů v souboru názvem vašeho trezoru klíčů.

```yml
version: v1.1.0
# Replace mykeyvault with the name of your key vault
secrets:
  - id: username
    keyvault: https://mykeyvault.vault.azure.net/secrets/UserName
  - id: password
    keyvault: https://mykeyvault.vault.azure.net/secrets/Password
steps:
# Log in to Docker Hub
  - cmd: bash echo '{{.Secrets.password}}' | docker login --username '{{.Secrets.username}}' --password-stdin 
# Build image
  - build: -t {{.Values.PrivateRepo}}:$ID https://github.com/Azure-Samples/acr-tasks.git -f hello-world.dockerfile
# Push image to private repo in Docker Hub
  - push:
    - {{.Values.PrivateRepo}}:$ID
```

Kroky úlohy jsou následující:

* Spravujte přihlašovací údaje tajných kódů pro ověřování pomocí Docker Hub.
* Ověřování pomocí Docker Hub předáním tajných kódů `docker login` příkazu.
* Sestavte Image pomocí ukázkového souboru dockerfileu v úložišti [Azure-Samples/ACR-Tasks](https://github.com/Azure-Samples/acr-tasks.git) .
* Nahrajte image do úložiště privátního Docker Hub.


## <a name="option-1-create-task-with-user-assigned-identity"></a>Možnost 1: vytvoření úlohy s identitou přiřazenou uživatelem

Kroky v této části vytvoří úlohu a umožní uživateli přiřazenou identitu. Pokud místo toho chcete povolit identitu přiřazenou systémem, přečtěte si část [možnost 2: vytvoření úlohy s identitou přiřazenou systémem](#option-2-create-task-with-system-assigned-identity). 

[!INCLUDE [container-registry-tasks-user-assigned-id](../../includes/container-registry-tasks-user-assigned-id.md)]

### <a name="create-task"></a>Vytvořit úlohu

Vytvořte úlohu *dockerhubtask* spuštěním následujícího příkazu [AZ ACR Task Create][az-acr-task-create] . Úloha se spouští bez kontextu zdrojového kódu a příkaz odkazuje na soubor `dockerhubtask.yaml` v pracovním adresáři. `--assign-identity`Parametr předá ID prostředku identity přiřazené uživatelem. 

```azurecli
az acr task create \
  --name dockerhubtask \
  --registry myregistry \
  --context /dev/null \
  --file dockerhubtask.yaml \
  --assign-identity $resourceID
```

[!INCLUDE [container-registry-tasks-user-id-properties](../../includes/container-registry-tasks-user-id-properties.md)]


### <a name="grant-identity-access-to-key-vault"></a>Udělení přístupu identit k trezoru klíčů

Pokud chcete nastavit zásady přístupu pro Trezor klíčů, spusťte následující příkaz [AZ Key trezor set-Policy][az-keyvault-set-policy] . Následující příklad umožňuje identitě číst tajné klíče z trezoru klíčů. 

```azurecli
az keyvault set-policy --name mykeyvault \
  --resource-group myResourceGroup \
  --object-id $principalID \
  --secret-permissions get
```

Pokračujte [v ručním spuštění úlohy](#manually-run-the-task).

## <a name="option-2-create-task-with-system-assigned-identity"></a>Možnost 2: vytvoření úlohy s identitou přiřazenou systémem

Kroky v této části vytvoří úlohu a povolí identitu přiřazenou systémem. Pokud místo toho chcete povolit uživatelsky přiřazenou identitu, přečtěte si část [možnost 1: vytvoření úlohy s identitou přiřazenou uživatelem](#option-1-create-task-with-user-assigned-identity). 

### <a name="create-task"></a>Vytvořit úlohu

Vytvořte úlohu *dockerhubtask* spuštěním následujícího příkazu [AZ ACR Task Create][az-acr-task-create] . Úloha se spouští bez kontextu zdrojového kódu a příkaz odkazuje na soubor `dockerhubtask.yaml` v pracovním adresáři. `--assign-identity`Parametr bez hodnoty umožňuje systémem přiřazenou identitu daného úkolu.  

```azurecli
az acr task create \
  --name dockerhubtask \
  --registry myregistry \
  --context /dev/null \
  --file dockerhubtask.yaml \
  --assign-identity 
```

[!INCLUDE [container-registry-tasks-system-id-properties](../../includes/container-registry-tasks-system-id-properties.md)]

### <a name="grant-identity-access-to-key-vault"></a>Udělení přístupu identit k trezoru klíčů

Pokud chcete nastavit zásady přístupu pro Trezor klíčů, spusťte následující příkaz [AZ Key trezor set-Policy][az-keyvault-set-policy] . Následující příklad umožňuje identitě číst tajné klíče z trezoru klíčů. 

```azurecli
az keyvault set-policy --name mykeyvault \
  --resource-group myResourceGroup \
  --object-id $principalID \
  --secret-permissions get
```

## <a name="manually-run-the-task"></a>Ručně spustit úlohu

Chcete-li ověřit, zda je úloha, ve které jste povolili spravovanou identitu, úspěšně spuštěna, spusťte úlohu ručně pomocí příkazu [AZ ACR Task Run][az-acr-task-run] . `--set`Parametr se používá k předání názvu soukromého úložiště do úlohy. V tomto příkladu je zástupný název úložiště *hubuser/hubrepo*.

```azurecli
az acr task run --name dockerhubtask --registry myregistry --set PrivateRepo=hubuser/hubrepo
```

Po úspěšném spuštění úlohy výstup zobrazí úspěšné ověření v Docker Hub a image se úspěšně sestaví a vloží do privátního úložiště:

```console
Queued a run with ID: cf24
Waiting for an agent...
2019/06/20 18:05:55 Using acb_vol_b1edae11-30de-4f2b-a9c7-7d743e811101 as the home volume
2019/06/20 18:05:58 Creating Docker network: acb_default_network, driver: 'bridge'
2019/06/20 18:05:58 Successfully set up Docker network: acb_default_network
2019/06/20 18:05:58 Setting up Docker configuration...
2019/06/20 18:05:59 Successfully set up Docker configuration
2019/06/20 18:05:59 Logging in to registry: myregistry.azurecr.io
2019/06/20 18:06:00 Successfully logged into myregistry.azurecr.io
2019/06/20 18:06:00 Executing step ID: acb_step_0. Timeout(sec): 600, Working directory: '', Network: 'acb_default_network'
2019/06/20 18:06:00 Launching container with name: acb_step_0
[...]
Login Succeeded
2019/06/20 18:06:02 Successfully executed container: acb_step_0
2019/06/20 18:06:02 Executing step ID: acb_step_1. Timeout(sec): 600, Working directory: '', Network: 'acb_default_network'
2019/06/20 18:06:02 Scanning for dependencies...
2019/06/20 18:06:04 Successfully scanned dependencies
2019/06/20 18:06:04 Launching container with name: acb_step_1
Sending build context to Docker daemon    129kB
[...]
2019/06/20 18:06:07 Successfully pushed image: hubuser/hubrepo:cf24
2019/06/20 18:06:07 Step ID: acb_step_0 marked as successful (elapsed time in seconds: 2.064353)
2019/06/20 18:06:07 Step ID: acb_step_1 marked as successful (elapsed time in seconds: 2.594061)
2019/06/20 18:06:07 Populating digests for step ID: acb_step_1...
2019/06/20 18:06:09 Successfully populated digests for step ID: acb_step_1
2019/06/20 18:06:09 Step ID: acb_step_2 marked as successful (elapsed time in seconds: 2.743923)
2019/06/20 18:06:09 The following dependencies were found:
2019/06/20 18:06:09
- image:
    registry: registry.hub.docker.com
    repository: hubuser/hubrepo
    tag: cf24
    digest: sha256:92c7f9c92844bbbb5d0a101b22f7c2a7949e40f8ea90c8b3bc396879d95e899a
  runtime-dependency:
    registry: registry.hub.docker.com
    repository: library/hello-world
    tag: latest
    digest: sha256:0e11c388b664df8a27a901dce21eb89f11d8292f7fca1b3e3c4321bf7897bffe
  git:
    git-head-revision: b0ffa6043dd893a4c75644c5fed384c82ebb5f9e

Run ID: cf24 was successful after 15s
```

Pokud chcete potvrdit, že je image vložená, zkontrolujte, jestli je značka ( `cf24` v tomto příkladu) v úložišti privátního Docker Hub.

## <a name="next-steps"></a>Další kroky

* Přečtěte si další informace o [Povolení spravované identity v ACR úloze](container-registry-tasks-authentication-managed-identity.md).
* Další informace najdete v tématu [ACR Tasks YAML reference](container-registry-tasks-reference-yaml.md)


<!-- LINKS - Internal -->
[az-login]: /cli/azure/reference-index#az-login
[az-acr-login]: /cli/azure/acr#az-acr-login
[az-acr-show]: /cli/azure/acr#az-acr-show
[az-acr-build]: /cli/azure/acr#az-acr-build
[az-acr-repository-show-tags]: /cli/azure/acr/repository#az-acr-repository-show-tags
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[az-acr-login]: /cli/azure/acr#az-acr-login
[az-identity-create]: /cli/azure/identity#az-identity-create
[az-identity-show]: /cli/azure/identity#az-identity-show
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-task-create]: /cli/azure/acr/task#az-acr-task-create
[az-acr-task-show]: /cli/azure/acr/task#az-acr-task-show
[az-acr-task-run]: /cli/azure/acr/task#az-acr-task-run
[az-acr-task-list-runs]: /cli/azure/acr/task#az-acr-task-list-runs
[az-acr-task-credential-add]: /cli/azure/acr/task/credential#az-acr-task-credential-add
[az-group-create]: /cli/azure/group?#az-group-create
[az-keyvault-create]: /cli/azure/keyvault?#az-keyvault-create
[az-keyvault-secret-set]: /cli/azure/keyvault/secret#az-keyvault-secret-set
[az-keyvault-set-policy]: /cli/azure/keyvault#az-keyvault-set-policy
