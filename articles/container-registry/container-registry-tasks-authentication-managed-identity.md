---
title: Použití spravované identity s Azure Container Registry úlohami
description: Přiřaďte ke zdrojům Azure přístup k prostředkům Azure Container Registry, včetně dalších privátních registrů kontejnerů, a to přiřazením spravované identity pro prostředky Azure.
services: container-registry
author: dlepow
manager: gwallace
ms.service: container-registry
ms.topic: article
ms.date: 06/12/2019
ms.author: danlep
ms.openlocfilehash: 46351af375ab4c6e59a3ddfba3c05c1e517fab0d
ms.sourcegitcommit: f5075cffb60128360a9e2e0a538a29652b409af9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/18/2019
ms.locfileid: "68311535"
---
# <a name="use-an-azure-managed-identity-in-acr-tasks"></a>Použití spravované identity Azure v úlohách ACR 

Použijte [spravovanou identitu pro prostředky Azure](../active-directory/managed-identities-azure-resources/overview.md) k ověření z ACR úloh do služby Azure Container registry nebo jiných prostředků Azure, aniž byste museli zadávat nebo spravovat přihlašovací údaje v kódu. Například použijte spravovanou identitu pro vyžádání nebo vložení imagí kontejneru do jiného registru jako krok v rámci úlohy.

V tomto článku se dozvíte víc o spravovaných identitách a o tom, jak:

> [!div class="checklist"]
> * Povolení identity přiřazené systémem nebo uživatelsky přiřazené identity pro úlohu ACR
> * Udělte identitám přístup k prostředkům Azure, jako jsou třeba jiné Registry kontejnerů Azure.
> * Přístup k prostředkům z úlohy pomocí spravované identity 

Tento článek vyžaduje, abyste spustili Azure CLI verze 2.0.66 nebo novější, abyste mohli vytvořit prostředky Azure. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI][azure-cli].

## <a name="why-use-a-managed-identity"></a>Proč používat spravovanou identitu?

Spravovaná identita pro prostředky Azure poskytuje služby Azure s automaticky spravovanou identitou v Azure Active Directory (Azure AD). Pomocí spravované identity můžete nakonfigurovat [určité prostředky Azure](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md), včetně úloh ACR. Pak použijte identitu pro přístup k dalším prostředkům Azure bez předání přihlašovacích údajů v kódu nebo skriptech.

Spravované identity mají dva typy:

* *Uživatelsky přiřazené identity*, které můžete přiřadit k více prostředkům a uchovávat tak dlouho, dokud budete chtít. Uživatelsky přiřazené identity jsou momentálně ve verzi Preview.

* *Identita spravovaná systémem*, která je jedinečná pro konkrétní prostředek, jako je například úloha ACR a která trvá po dobu života daného prostředku.

Po nastavení prostředku Azure pomocí spravované identity Udělte identitě přístup k jinému prostředku stejným způsobem jako jakýkoli objekt zabezpečení. Přiřaďte například roli spravované identity s oprávněním Pull, push a pull nebo jinými oprávněními k soukromému registru kontejneru v Azure. (Úplný seznam rolí registru najdete v tématu [Azure Container Registry role a oprávnění](container-registry-roles.md).) Jednomu nebo více prostředkům můžete přidělit přístup k identitě.

## <a name="create-container-registries"></a>Vytvoření registrů kontejnerů

Pro tento kurz potřebujete tři Registry kontejnerů:

* Pomocí prvního registru můžete vytvářet a spouštět úlohy ACR. V tomto článku se jedná o zdrojový registr s názvem *myregistry*. 
* Druhý a třetí registr jsou cílové registry pro první ukázkový úkol, který bude nabízet obrázek, který sestaví. V tomto článku jsou cílové Registry pojmenované *customregistry1* a *customregistry2*.

Nahraďte vlastními názvy registru v pozdějších krocích.

Pokud ještě nemáte potřebné registry kontejnerů Azure, přečtěte si [téma rychlý Start: Vytvoření soukromého registru kontejnerů pomocí Azure CLI](container-registry-get-started-azure-cli.md). Image ještě nemusíte vkládat do registru.

## <a name="example-task-with-a-system-assigned-identity"></a>Příklad: Úloha s identitou přiřazenou systémem

V tomto příkladu se dozvíte, jak vytvořit [úlohu s více kroky](container-registry-tasks-multi-step.md) s identitou přiřazenou systémem. Úloha sestaví image a pak použije identitu k ověření se dvěma cílovými registry pro vložení image.

Kroky pro tento příklad úlohy jsou definovány v [souboru YAML](container-registry-tasks-reference-yaml.md) s názvem `testtask.yaml`. Soubor se nachází v adresáři multipleRegistries úložiště ukázek [ACR-Tasks](https://github.com/Azure-Samples/acr-tasks) . Soubor se reprodukuje zde:

```yml
version: v1.0.0
steps:
  - build: -t {{.Values.REGISTRY1}}/hello-world:{{.Run.ID}} . -f hello-world.dockerfile
  - push: ["{{.Values.REGISTRY1}}/hello-world:{{.Run.ID}}"]
  - build: -t {{.Values.REGISTRY2}}/hello-world:{{.Run.ID}} . -f hello-world.dockerfile
  - push: ["{{.Values.REGISTRY2}}/hello-world:{{.Run.ID}}"]
```

### <a name="create-task-with-system-assigned-identity"></a>Vytvořit úlohu s identitou přiřazenou systémem

Pomocí následujícího příkazu [AZ ACR Task Create][az-acr-task-create] vytvořte úlohu *Multiple-reg* . Kontext úlohy je složka multipleRegistries úložiště ukázek a příkaz odkazuje na soubor `testtask.yaml` v úložišti. `--assign-identity` Parametr bez další hodnoty vytvoří pro úlohu identitu přiřazenou systémem. Tato úloha je nastavená tak, aby ji bylo možné spustit ručně, ale je možné ji nastavit tak, aby se potvrzení odeslala do úložiště nebo žádost o přijetí změn. 

```azurecli
az acr task create \
  --registry myregistry \
  --name multiple-reg \
  --context https://github.com/Azure-Samples/acr-tasks.git#:multipleRegistries \
  --file testtask.yaml \
  --commit-trigger-enabled false \
  --pull-request-trigger-enabled false \
  --assign-identity
```

Ve výstupu `identity` příkazu ukazuje oddíl identitu typu `SystemAssigned` nastavenou v úloze. `principalId` Je ID objektu služby identity:

```console
[...]
  "identity": {
    "principalId": "xxxxxxxx-2703-42f9-97d0-xxxxxxxxxxxx",
    "tenantId": "xxxxxxxx-86f1-41af-91ab-xxxxxxxxxxxx",
    "type": "SystemAssigned",
    "userAssignedIdentities": null
  },
  "location": "eastus",
[...]
``` 

Pomocí příkazu [AZ ACR Task show][az-acr-task-show] uložte `principalId` proměnnou do proměnné a použijte ji v pozdějších příkazech:

```azurecli
principalID=$(az acr task show --name multiple-reg --registry myregistry --query identity.principalId --output tsv)
```

### <a name="give-identity-push-permissions-to-two-target-container-registries"></a>Udělení oprávnění pro nabízení identity dvěma cílovým registrům kontejnerů

V této části udělte systémem přiřazená oprávnění identity k odeslání do dvou cílových registrů s názvem *customregistry1* a *customregistry2*.

Nejprve pomocí příkazu [AZ ACR show][az-acr-show] Získejte ID prostředku každého registru a uložte ID do proměnných:

```azurecli
reg1_id=$(az acr show --name customregistry1 --query id --output tsv)
reg2_id=$(az acr show --name customregistry2 --query id --output tsv)
```

Pomocí příkazu [AZ role Assignment Create][az-role-assignment-create] přiřaďte identitě `acrpush` roli ke každému registru. Tato role má oprávnění k vyžádání a vložení imagí do registru kontejneru.

```azurecli
az role assignment create --assignee $principalID --scope $reg1_id --role acrpush
az role assignment create --assignee $principalID --scope $reg2_id --role acrpush
```

### <a name="add-target-registry-credentials-to-task"></a>Přidat do úlohy přihlašovací údaje pro cílový registr

Teď pomocí příkazu [AZ ACR Task Credential Add][az-acr-task-credential-add] přidejte přihlašovací údaje identity k úloze, aby se mohla ověřit s oběma cílovými Registry.

```azurecli
az acr task credential add \
  --name multiple-reg \
  --registry myregistry \
  --login-server customregistry1.azurecr.io \
  --use-identity [system]

az acr task credential add \
  --name multiple-reg \
  --registry myregistry \
  --login-server customregistry2.azurecr.io \
  --use-identity [system]
```

### <a name="manually-run-the-task"></a>Ručně spustit úlohu

Úlohu můžete aktivovat ručně pomocí příkazu [AZ ACR Task Run][az-acr-task-run] . Parametr se používá k předání názvů přihlašovacích serverů dvou cílových registrů jako hodnot pro proměnné `REGISTRY1` úlohy a `REGISTRY2`. `--set`

```azurecli
az acr task run \
  --name multiple-reg \
  --registry myregistry \
  --set REGISTRY1=customregistry1.azurecr.io \
  --set REGISTRY2=customregistry2.azurecr.io
```

Výstup se podobá tomuto:

```console
Sending build context to Docker daemon  4.096kB
Step 1/1 : FROM hello-world
 ---> fce289e99eb9
Successfully built fce289e99eb9
Successfully tagged customregistry2.azurecr.io/hello-world:cf31
2019/05/31 22:16:02 Successfully executed container: acb_step_0
2019/05/31 22:16:02 Executing step ID: acb_step_1. Timeout(sec): 600, Working directory: 'multipleRegistries', Network: 'acb_default_network'
2019/05/31 22:16:02 Pushing image: customregistry2.azurecr.io/hello-world:cf31, attempt 1
The push refers to repository [customregistry2.azurecr.io/hello-world]
af0b15c8625b: Preparing
af0b15c8625b: Pushed
cf31: digest: sha256:92c7f9c92844bbbb5d0a101b22f7c2a7949e40f8ea90c8b3bc396879d95e899a size: 524
2019/05/31 22:16:08 Successfully pushed image: customregistry2.azurecr.io/hello-world:cf31
2019/05/31 22:16:08 Executing step ID: acb_step_2. Timeout(sec): 600, Working directory: 'multipleRegistries', Network: 'acb_default_network'
2019/05/31 22:16:08 Scanning for dependencies...
2019/05/31 22:16:08 Successfully scanned dependencies
2019/05/31 22:16:08 Launching container with name: acb_step_2
Sending build context to Docker daemon  4.096kB
Step 1/1 : FROM hello-world
 ---> fce289e99eb9
Successfully built fce289e99eb9
Successfully tagged customregistry1.azurecr.io/hello-world:cf31
2019/05/31 22:16:09 Successfully executed container: acb_step_2
2019/05/31 22:16:09 Executing step ID: acb_step_3. Timeout(sec): 600, Working directory: 'multipleRegistries', Network: 'acb_default_network'
2019/05/31 22:16:09 Pushing image: customregistry1.azurecr.io/hello-world:cf31, attempt 1
The push refers to repository [customregistry1.azurecr.io/hello-world]
af0b15c8625b: Preparing
af0b15c8625b: Pushed
cf31: digest: sha256:92c7f9c92844bbbb5d0a101b22f7c2a7949e40f8ea90c8b3bc396879d95e899a size: 524
2019/05/31 22:16:21 Successfully pushed image: customregistry1.azurecr.io/hello-world:cf31
2019/05/31 22:16:21 Step ID: acb_step_0 marked as successful (elapsed time in seconds: 1.985291)
2019/05/31 22:16:21 Populating digests for step ID: acb_step_0...
2019/05/31 22:16:22 Successfully populated digests for step ID: acb_step_0
2019/05/31 22:16:22 Step ID: acb_step_1 marked as successful (elapsed time in seconds: 5.743225)
2019/05/31 22:16:22 Step ID: acb_step_2 marked as successful (elapsed time in seconds: 1.925959)
2019/05/31 22:16:22 Populating digests for step ID: acb_step_2...
2019/05/31 22:16:24 Successfully populated digests for step ID: acb_step_2
2019/05/31 22:16:24 Step ID: acb_step_3 marked as successful (elapsed time in seconds: 11.061057)
2019/05/31 22:16:24 The following dependencies were found:
2019/05/31 22:16:24
- image:
    registry: customregistry2.azurecr.io
    repository: hello-world
    tag: cf31
    digest: sha256:92c7f9c92844bbbb5d0a101b22f7c2a7949e40f8ea90c8b3bc396879d95e899a
  runtime-dependency:
    registry: registry.hub.docker.com
    repository: library/hello-world
    tag: latest
    digest: sha256:6f744a2005b12a704d2608d8070a494ad1145636eeb74a570c56b94d94ccdbfc
  git:
    git-head-revision: 05275dca2bc61f584085ca913c39d509236f576b
- image:
    registry: customregistry1.azurecr.io
    repository: hello-world
    tag: cf31
    digest: sha256:92c7f9c92844bbbb5d0a101b22f7c2a7949e40f8ea90c8b3bc396879d95e899a
  runtime-dependency:
    registry: registry.hub.docker.com
    repository: library/hello-world
    tag: latest
    digest: sha256:6f744a2005b12a704d2608d8070a494ad1145636eeb74a570c56b94d94ccdbfc
  git:
    git-head-revision: 05275dca2bc61f584085ca913c39d509236f576b

Run ID: cf31 was successful after 35s
```

## <a name="example-task-with-a-user-assigned-identity"></a>Příklad: Úkol s identitou přiřazenou uživatelem

V tomto příkladu vytvoříte identitu přiřazenou uživateli s oprávněním ke čtení tajných klíčů z trezoru klíčů Azure. Tuto identitu přiřadíte k úkolu s více kroky, který přečte tajný klíč, vytvoří image a přihlásí se do Azure CLI a přečte si značku image.

### <a name="create-a-key-vault-and-store-a-secret"></a>Vytvoření trezoru klíčů a uložení tajného klíče

Nejdřív v případě potřeby vytvořte skupinu prostředků s názvem *myResourceGroup* v umístění *eastus* pomocí následujícího příkazu [AZ Group Create][az-group-create] :

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Pomocí příkazu [AZ datatrezor Create][az-keyvault-create] vytvořte Trezor klíčů. Nezapomeňte zadat jedinečný název trezoru klíčů. 

```azurecli-interactive
az keyvault create --name mykeyvault --resource-group myResourceGroup --location eastus
```

Pomocí příkazu [AZ Key trezor tajné sady][az-keyvault-secret-set] uložte ukázkový tajný klíč do trezoru klíčů:

```azurecli
az keyvault secret set \
  --name SampleSecret \
  --value "Hello ACR Tasks!" \
  --description ACRTasksecret  \
  --vault-name mykeyvault
```

Můžete například chtít ukládat přihlašovací údaje pro ověření pomocí privátního registru Docker, abyste si mohli vyžádat privátní image.

### <a name="create-an-identity"></a>Vytvoření identity

Pomocí příkazu [AZ identity Create][az-identity-create] vytvořte v předplatném identitu s názvem *myACRTasksId* . Stejnou skupinu prostředků, kterou jste použili dříve, můžete použít k vytvoření registru kontejneru nebo trezoru klíčů nebo jiné.

```azurecli-interactive
az identity create --resource-group myResourceGroup --name myACRTasksId
```

Pokud chcete nakonfigurovat identitu v následujících krocích, pomocí příkazu [AZ identity show][az-identity-show] uložte ID prostředku identity a ID instančního objektu do proměnných.

```azurecli
# Get resource ID of the user-assigned identity
resourceID=$(az identity show --resource-group myResourceGroup --name myACRTasksId --query id --output tsv)

# Get service principal ID of the user-assigned identity
principalID=$(az identity show --resource-group myResourceGroup --name myACRTasksId --query principalId --output tsv)
```

### <a name="grant-identity-access-to-keyvault-to-read-secret"></a>Udělte identitě přístup k trezoru klíčů pro čtení tajného klíče.

Pokud chcete nastavit zásady přístupu pro Trezor klíčů, spusťte následující příkaz [AZ Key trezor set-Policy][az-keyvault-set-policy] . Následující příklad umožňuje uživatelsky přiřazené identitě získat tajné kódy z trezoru klíčů. Tento přístup je potřeba provést později, aby bylo možné úspěšně spustit úlohu s více kroky.

```azurecli-interactive
 az keyvault set-policy --name mykeyvault --resource-group myResourceGroup --object-id $principalID --secret-permissions get
```

### <a name="grant-identity-reader-access-to-the-resource-group-for-registry"></a>Udělení přístupu ke skupině prostředků v registru ke službě čtenář identity

Spusťte následující příkaz [AZ role Assignment Create][az-role-assignment-create] a přiřaďte mu roli Čtenář a v tomto případě do skupiny prostředků obsahující zdrojový registr. Tato role je potřeba později, aby bylo možné úspěšně spustit úlohu s více kroky.

```azurecli
az role assignment create --role reader --resource-group myResourceGroup --assignee $principalID
```

### <a name="create-task-with-user-assigned-identity"></a>Vytvořit úlohu s identitou přiřazenou uživatelem

Nyní vytvořte [úkol s více kroky](container-registry-tasks-multi-step.md) a přiřaďte mu identitu přiřazenou uživatelem. Pro tento příklad úlohy vytvořte [soubor YAML](container-registry-tasks-reference-yaml.md) s názvem `managed-identities.yaml` v místním pracovním adresáři a vložte následující obsah. Nezapomeňte nahradit název trezoru klíčů v souboru názvem vašeho trezoru klíčů.

```yml
version: v1.0.0
# Replace mykeyvault with the name of your key vault
secrets:
  - id: name
    keyvault: https://mykeyvault.vault.azure.net/secrets/SampleSecret
steps:
  # Verify that the task can access the secret in the key vault
  - cmd: bash -c 'if [ -z "$MY_SECRET" ]; then echo "Secret not resolved"; else echo "Secret resolved"; fi'
    env: 
      - MY_SECRET='{{.Secrets.name}}' 

  # Build/push the website image to source registry, using dockerfile in the Azure-Samples repo
  - cmd: docker build -t {{.Run.Registry}}/my-website:{{.Run.ID}} https://github.com/Azure-Samples/aci-helloworld.git
  - push: 
    - "{{.Run.Registry}}/my-website:{{.Run.ID}}"
  
  # Login to Azure CLI with identity and list the tags to verify that the image is in the registry
  - cmd: microsoft/azure-cli az login --identity
  - cmd: microsoft/azure-cli az acr repository show-tags -n {{.Values.registryName}} --repository my-website
```

Tato úloha provede následující akce:

* Ověří, jestli má přístup ke tajnému kódu ve vašem trezoru klíčů. Tento krok je určen pro demonstrační účely. Ve scénáři reálného světa budete možná potřebovat krok úkolu, který získá přihlašovací údaje pro přístup k úložišti privátního Docker Hub.
* Sestaví a `mywebsite` vloží image do zdrojového registru.
* Přihlásí se do Azure CLI a vypíše `my-website` značky imagí ve zdrojovém registru.

Vytvořte úlohu s názvem *msitask* a předejte jí ID prostředku uživatelem přiřazené identity, kterou jste vytvořili dříve. Tento příklad úlohy se vytvoří ze `managed-identities.yaml` souboru, který jste uložili do místního pracovního adresáře, takže je potřeba ho aktivovat ručně.

```azurecli
az acr task create \
  --name msitask \
  --registry myregistry \
  --context /dev/null \
  --file managed-identities.yaml \
  --pull-request-trigger-enabled false \
  --commit-trigger-enabled false \
  --assign-identity $resourceID
```

Ve výstupu `identity` příkazu ukazuje oddíl identitu typu `UserAssigned` nastavenou v úloze. `principalId` Je ID objektu služby identity:

```console
[...]
"identity": {
    "principalId": null,
    "tenantId": null,
    "type": "UserAssigned",
    "userAssignedIdentities": {
      "/subscriptions/xxxxxxxx-d12e-4760-9ab6-xxxxxxxxxxxx/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myACRTasksId": {
        "clientId": "xxxxxxxx-f17e-4768-bb4e-xxxxxxxxxxxx",
        "principalId": "xxxxxxxx-1335-433d-bb6c-xxxxxxxxxxxx"
      }
[...]
```

### <a name="manually-run-the-task"></a>Ručně spustit úlohu

Úlohu můžete aktivovat ručně pomocí příkazu [AZ ACR Task Run][az-acr-task-run] . `--set` Parametr se používá k předání názvu zdrojového registru do úlohy:

```azurecli
az acr task run --name msitask --registry myregistry --set registryName=myregistry  
```

Výstup ukazuje, že je tajný kód vyřešen, image je úspěšně sestavena a vložena a úloha se přihlásí do Azure CLI s identitou ke čtení značky image ze zdrojového registru:

```console
Queued a run with ID: cf32
Waiting for an agent...
2019/06/10 23:25:37 Downloading source code...
2019/06/10 23:25:38 Finished downloading source code
2019/06/10 23:25:39 Using acb_vol_4e4a0a7c-b6ef-4ec5-b40f-3436fc5eb0f5 as the home volume
2019/06/10 23:25:41 Creating Docker network: acb_default_network, driver: 'bridge'
2019/06/10 23:25:41 Successfully set up Docker network: acb_default_network
2019/06/10 23:25:41 Setting up Docker configuration...
2019/06/10 23:25:42 Successfully set up Docker configuration
2019/06/10 23:25:42 Logging in to registry: myregistry.azurecr.io
2019/06/10 23:25:43 Successfully logged into myregistry.azurecr.io
2019/06/10 23:25:43 Executing step ID: acb_step_0. Timeout(sec): 600, Working directory: '', Network: 'acb_default_network'
2019/06/10 23:25:43 Launching container with name: acb_step_0
Secret resolved
2019/06/10 23:25:44 Successfully executed container: acb_step_0
2019/06/10 23:25:44 Executing step ID: acb_step_1. Timeout(sec): 600, Working directory: '', Network: 'acb_default_network'
2019/06/10 23:25:44 Launching container with name: acb_step_1
Sending build context to Docker daemon  74.75kB

[...]

cf32: digest: sha256:cbb4aa83b33f6959d83e84bfd43ca901084966a9f91c42f111766473dc977f36 size: 1577
2019/06/10 23:26:05 Successfully pushed image: myregistry.azurecr.io/my-website:cf32
2019/06/10 23:26:05 Executing step ID: acb_step_3. Timeout(sec): 600, Working directory: '', Network: 'acb_default_network'
2019/06/10 23:26:05 Launching container with name: acb_step_3
[
  {
    "environmentName": "AzureCloud",
    "id": "xxxxxxxx-d12e-4760-9ab6-xxxxxxxxxxxx",
    "isDefault": true,
    "name": "DanLep Internal Consumption",
    "state": "Enabled",
    "tenantId": "xxxxxxxx-86f1-41af-91ab-xxxxxxxxxxxx",
      "user": {
      "assignedIdentityInfo": "MSI",
      "name": "systemAssignedIdentity",
      "type": "servicePrincipal"
    }
  }
]
2019/06/10 23:26:09 Successfully executed container: acb_step_3
2019/06/10 23:26:09 Executing step ID: acb_step_4. Timeout(sec): 600, Working directory: '', Network: 'acb_default_network'
2019/06/10 23:26:09 Launching container with name: acb_step_4
[
  "cf32"
]
2019/06/10 23:26:14 Successfully executed container: acb_step_4
2019/06/10 23:26:14 Step ID: acb_step_0 marked as successful (elapsed time in seconds: 1.025312)
2019/06/10 23:26:14 Step ID: acb_step_1 marked as successful (elapsed time in seconds: 13.703823)
2019/06/10 23:26:14 Step ID: acb_step_2 marked as successful (elapsed time in seconds: 6.791506)
2019/06/10 23:26:14 Step ID: acb_step_3 marked as successful (elapsed time in seconds: 3.852972)
2019/06/10 23:26:14 Step ID: acb_step_4 marked as successful (elapsed time in seconds: 5.079079)
```


## <a name="next-steps"></a>Další kroky

V tomto článku jste se dozvěděli o používání spravovaných identit s Azure Container Registry úlohami a postupy:

> [!div class="checklist"]
> * Povolení uživatelsky přiřazené identity nebo uživatele přiřazené k úloze ACR
> * Udělte identitám přístup k prostředkům Azure, jako jsou třeba jiné Registry kontejnerů Azure.
> * Přístup k prostředkům z úlohy pomocí spravované identity  

* Přečtěte si další informace o [spravovaných identitách pro prostředky Azure](/azure/active-directory/managed-identities-azure-resources/).

<!-- LINKS - Internal -->
[az-login]: /cli/azure/reference-index#az-login
[az-acr-login]: /cli/azure/acr#az-acr-login
[az-acr-show]: /cli/azure/acr#az-acr-show
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[az-acr-login]: /cli/azure/acr#az-acr-login
[az-identity-create]: /cli/azure/identity?view=azure-cli-latest#az-identity-create
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
