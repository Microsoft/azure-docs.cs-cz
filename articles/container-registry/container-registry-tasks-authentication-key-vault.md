---
title: Externí ověřování z úlohy ACR
description: Nakonfigurujte úlohu registru kontejneru Azure (úloha ACR) ke čtení přihlašovacích údajů dockerového centra uložených v trezoru klíčů Azure pomocí spravované identity pro prostředky Azure.
ms.topic: article
ms.date: 01/14/2020
ms.openlocfilehash: 47d3d643ee1287ef4f444095a2c6cfe6dcab294b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76842516"
---
# <a name="external-authentication-in-an-acr-task-using-an-azure-managed-identity"></a>Externí ověřování v úloze ACR pomocí identity spravované Azure 

V [úloze ACR](container-registry-tasks-overview.md)můžete [povolit spravovanou identitu pro prostředky Azure](container-registry-tasks-authentication-managed-identity.md). Úloha můžete použít identitu pro přístup k jiným prostředkům Azure, bez nutnosti zadejte nebo spravovat přihlašovací údaje. 

V tomto článku se dozvíte, jak povolit spravovanou identitu v úloze, která přistupuje k tajným kódům uloženým v trezoru klíčů Azure. 

Chcete-li vytvořit prostředky Azure, tento článek vyžaduje spuštění Azure CLI verze 2.0.68 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI][azure-cli].

## <a name="scenario-overview"></a>Přehled scénáře

Ukázková úloha čte přihlašovací údaje dockerového centra uložené v trezoru klíčů Azure. Pověření jsou pro účet Docker Hub s oprávněními pro zápis (nabízený) do soukromého úložiště Docker Hub. Chcete-li si přečíst pověření, nakonfigurujte úlohu se spravovanou identitou a přiřaďte k ní příslušná oprávnění. Úloha přidružená k identitě vytvoří image a přihlásí se do Docker Hubu, aby ji posunula do soukromého úložiště. 

Tento příklad ukazuje kroky pomocí uživatelem přiřazené nebo systémem přiřazené spravované identity. Vaše volba identity závisí na potřebách vaší organizace.

V reálném scénáři může společnost publikovat image do soukromého úložiště v Docker Hubu jako součást procesu sestavení. 

## <a name="prerequisites"></a>Požadavky

Potřebujete registr kontejnerů Azure, ve kterém spustíte úlohu. V tomto článku tento registr se nazývá *myregistry*. V pozdějších krocích nahraďte vlastním názvem registru.

Pokud ještě nemáte registr kontejnerů Azure, [přečtěte si tématu Úvodní příručka: Vytvoření registru privátního kontejneru pomocí azure cli](container-registry-get-started-azure-cli.md). Obrázky ještě nemusíte do registru vysunout.

Potřebujete také privátní úložiště v Docker Hubu a účet Docker Hub s oprávněními k zápisu do úložiště. V tomto příkladu se toto úložiště nazývá *hubuser/hubrepo*. 

## <a name="create-a-key-vault-and-store-secrets"></a>Vytvoření trezoru klíčů a uložení tajných kódů

Za prvé, pokud je to nutné, vytvořte skupinu prostředků s názvem *myResourceGroup* v umístění *eastus* s následujícím [příkazem vytvořit skupinu az:][az-group-create]

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

K vytvoření trezoru klíčů použijte příkaz [az keyvault][az-keyvault-create] create. Nezapomeňte zadat jedinečný název trezoru klíčů. 

```azurecli-interactive
az keyvault create --name mykeyvault --resource-group myResourceGroup --location eastus
```

Uložte požadovaná pověření Docker Hubu do trezoru klíčů pomocí příkazu [az keyvault secret set.][az-keyvault-secret-set] V těchto příkazech jsou hodnoty předávány v proměnných prostředí:

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

V reálném scénáři by tajemství pravděpodobně nastavit a udržovat v samostatném procesu.

## <a name="define-task-steps-in-yaml-file"></a>Definování kroků úloh v souboru YAML

Kroky pro tento příklad úlohy jsou definovány v [souboru YAML](container-registry-tasks-reference-yaml.md). Vytvořte soubor `dockerhubtask.yaml` pojmenovaný v místním pracovním adresáři a vložte následující obsah. Nezapomeňte nahradit název trezoru klíčů v souboru názvem trezoru klíčů.

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

Kroky úkolu postupují takto:

* Spravujte tajné přihlašovací údaje k ověření pomocí Docker Hubu.
* Ověřte pomocí Docker Hub `docker login` předáním tajných kódů příkazu.
* Vytvořte bitovou kopii pomocí ukázkového souboru Dockerfile v repo úlohách Azure-Samples/acr.Build a image using a sample Dockerfile in the [Azure-Samples/acr-tasks](https://github.com/Azure-Samples/acr-tasks.git) repo.
* Posuňte bitovou kopii do soukromého úložiště Docker Hub.


## <a name="option-1-create-task-with-user-assigned-identity"></a>Možnost 1: Vytvoření úkolu s identitou přiřazenou uživatelem

Kroky v této části vytvoří úlohu a povolí identitu přiřazenou uživateli. Pokud chcete místo toho povolit identitu přiřazenou systémem, [přečtěte si informace o možnosti 2: Vytvoření úlohy se systémem přiřazenou identitou](#option-2-create-task-with-system-assigned-identity). 

[!INCLUDE [container-registry-tasks-user-assigned-id](../../includes/container-registry-tasks-user-assigned-id.md)]

### <a name="create-task"></a>Vytvořit úkol

Vytvořte úlohu *dockerhubtask* provedením následující [az acr úkol vytvořit][az-acr-task-create] příkaz. Úloha je spuštěna bez kontextu zdrojového kódu `dockerhubtask.yaml` a příkaz odkazuje na soubor v pracovním adresáři. Parametr `--assign-identity` předá ID prostředku uživatelem přiřazené identity. 

```azurecli
az acr task create \
  --name dockerhubtask \
  --registry myregistry \
  --context /dev/null \
  --file dockerhubtask.yaml \
  --assign-identity $resourceID
```

[!INCLUDE [container-registry-tasks-user-id-properties](../../includes/container-registry-tasks-user-id-properties.md)]

## <a name="option-2-create-task-with-system-assigned-identity"></a>Možnost 2: Vytvoření úlohy se systémem přiřazenou identitou

Kroky v této části vytvoří úlohu a povolí identitu přiřazenou systémem. Pokud chcete místo toho povolit identitu přiřazenou uživateli, [přečtěte si informace o možnosti 1: Vytvoření úlohy s identitou přiřazenou uživatelem](#option-1-create-task-with-user-assigned-identity). 

### <a name="create-task"></a>Vytvořit úkol

Vytvořte úlohu *dockerhubtask* provedením následující [az acr úkol vytvořit][az-acr-task-create] příkaz. Úloha je spuštěna bez kontextu zdrojového kódu `dockerhubtask.yaml` a příkaz odkazuje na soubor v pracovním adresáři. Parametr `--assign-identity` bez hodnoty umožňuje systémem přiřazenou identitu na úkolu.  

```azurecli
az acr task create \
  --name dockerhubtask \
  --registry myregistry \
  --context /dev/null \
  --file dockerhubtask.yaml \
  --assign-identity 
```

[!INCLUDE [container-registry-tasks-system-id-properties](../../includes/container-registry-tasks-system-id-properties.md)]

## <a name="grant-identity-access-to-key-vault"></a>Udělení přístupu k identitě trezoru klíčů

Spusťte následující příkaz [az keyvault set-policy][az-keyvault-set-policy] a nastavte zásady přístupu v trezoru klíčů. Následující příklad umožňuje identity číst tajné klíče z trezoru klíčů. 

```azurecli
az keyvault set-policy --name mykeyvault \
  --resource-group myResourceGroup \
  --object-id $principalID \
  --secret-permissions get
```

## <a name="manually-run-the-task"></a>Ruční spuštění úlohy

Chcete-li ověřit, zda úloha, ve které jste povolili spravovanou identitu, úspěšně spuštěna, ručně spusťte úlohu pomocí příkazu [az acr task run.][az-acr-task-run] Parametr `--set` slouží k předání soukromého názvu repo úkolu. V tomto příkladu je zástupný název úložiště *hubuser/hubrepo*.

```azurecli
az acr task run --name dockerhubtask --registry myregistry --set PrivateRepo=hubuser/hubrepo
```

Při úspěšném spuštění úlohy se výstup zobrazí úspěšné ověřování v dockerovém centru a bitová kopie se úspěšně nastaví a zasune do privátního úložiště:

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

Chcete-li potvrdit, že je bitka obrázku posunuta, zkontrolujte značku (`cf24` v tomto příkladu) v soukromém úložiště Docker Hub.

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
