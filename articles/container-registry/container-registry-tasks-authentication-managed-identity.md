---
title: Použití spravované identity s Azure Container Registry úkoly
description: Poskytují Azure Container Registry úloh přístup k prostředkům Azure, včetně dalších registrů kontejneru soukromého přiřazením spravovanou identitu pro prostředky Azure.
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: article
ms.date: 06/12/2019
ms.author: danlep
ms.openlocfilehash: 5b60727472a06aaac8ccd3dce8609461e8972311
ms.sourcegitcommit: 72f1d1210980d2f75e490f879521bc73d76a17e1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/14/2019
ms.locfileid: "67148029"
---
# <a name="use-an-azure-managed-identity-in-acr-tasks"></a>Použití Azure spravovat identity v úlohách služby ACR 

Použití [spravované identity pro prostředky Azure](../active-directory/managed-identities-azure-resources/overview.md) o ověření z úlohy služby ACR do služby Azure container registry nebo jiných prostředků služby Azure bez nutnosti poskytnout nebo spravovat přihlašovací údaje v kódu. Například pomocí spravované identity o přijetí změn nebo nahrávání imagí kontejneru do registru jiný jako krok v rámci úlohy.

V tomto článku najdete další informace o spravovaných identit a jak:

> [!div class="checklist"]
> * Povolení identity se systém přiřadil nebo uživatelsky přiřazené identity v rámci úlohy služby ACR
> * Udělit přístup identit k prostředkům Azure, jako jsou jiné registry kontejnerů Azure
> * Použití spravované identity pro přístup k prostředkům z úlohy 

Vytváření prostředků Azure, tento článek vyžaduje, abyste spustili Azure CLI verze 2.0.66 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI][azure-cli].

## <a name="why-use-a-managed-identity"></a>Proč používat spravovanou identitu?

Spravovaná identita pro prostředky Azure poskytuje služby Azure se automaticky spravovanou identitu ve službě Azure Active Directory (Azure AD). Můžete nakonfigurovat [určité prostředky Azure](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md), včetně úloh ACR pomocí spravované identity. Potom použijte identitu a přístup jiných prostředků služby Azure bez předávání přihlašovacích údajů v kódu nebo skriptech.

Spravované identity jsou dvou typů:

* *Uživatelsky přiřazené identity*, který lze přiřadit k více prostředkům a až vaše chcete zachovat. Uživatelsky přiřazené identity jsou aktuálně ve verzi preview.

* A *identity spravované systému*, které jsou jedinečné pro konkrétní prostředek, jako je například úloha služby ACR a má platnost po dobu životnosti prostředku.

Po nastavení prostředku Azure s využitím spravované identity udělte přístup identity na jiný prostředek, stejně jako libovolný objekt zabezpečení. Například spravovanou identitu roli s o přijetí změn, vložení a o přijetí změn nebo jiná oprávnění můžete přiřadíte soukromého registru kontejnerů v Azure. (Úplný seznam rolí registru najdete v tématu [Azure Container Registry role a oprávnění](container-registry-roles.md).) Můžete poskytnout přístup identity na jeden nebo více prostředků.

## <a name="create-container-registries"></a>Vytvoření registry kontejnerů

Pro účely tohoto kurzu potřebujete tři registry kontejnerů:

* První registru použijete k vytvoření a spuštění úlohy služby ACR. V tomto článku se tento zdroj registr jmenuje *myregistry*. 
* Druhý a třetí registry jsou cílové registrů pro první příklad úlohu chcete nasdílet image, které se sestaví. V tomto článku jsou pojmenovány registrů cílového *customregistry1* a *customregistry2*.

Nahraďte vlastními názvy registru v dalších krocích.

Pokud ještě nemáte potřebné Azure container registry, najdete v článku [rychlý start: Vytvořit privátní registr pomocí rozhraní příkazového řádku Azure](container-registry-get-started-azure-cli.md). Není potřeba ještě nahrávání imagí do registru.

## <a name="example-task-with-a-system-assigned-identity"></a>Příklad: Úloha s identitou systém přiřadil

Tento příklad ukazuje, jak vytvořit [vícekrokových úkolů](container-registry-tasks-multi-step.md) s identitou systému přiřazené. Úloha sestavení image a pak použije identitu ověřit pomocí dvou registrů cílového nasdílejte image.

Kroky pro tuto úlohu příkladu jsou definovány v [soubor YAML](container-registry-tasks-reference-yaml.md) s názvem `testtask.yaml`. Soubor se nachází v adresáři multipleRegistries [acr úlohy](https://github.com/Azure-Samples/acr-tasks) ukázkové úložiště. Soubor je reprodukován zde:

```yml
version: v1.0.0
steps:
  - build: -t {{.Values.REGISTRY1}}/hello-world:{{.Run.ID}} . -f hello-world.dockerfile
  - push: ["{{.Values.REGISTRY1}}/hello-world:{{.Run.ID}}"]
  - build: -t {{.Values.REGISTRY2}}/hello-world:{{.Run.ID}} . -f hello-world.dockerfile
  - push: ["{{.Values.REGISTRY2}}/hello-world:{{.Run.ID}}"]
```

### <a name="create-task-with-system-assigned-identity"></a>Vytvoří úkol s identitou systém přiřadil

Vytvořit úlohu *více reg* spuštěním následujícího [az acr úloha vytvoření] [ az-acr-task-create] příkazu. Kontext úlohy je složka multipleRegistries z úložiště ukázek a příkazu odkazuje na soubor `testtask.yaml` v úložišti. `--assign-identity` Parametr bez dalších hodnot vytvoří identitu systém přiřadil pro úlohu. Tato úloha je nastavený tak, že budete muset aktivovat ručně, ale můžete nastavit ji spustit, když se provedou žádost o přijetí změn nebo potvrzení změn do úložiště. 

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

Ve výstupu tohoto příkazu `identity` část ukazuje identity typu `SystemAssigned` je nastavena v úloze. `principalId` Je ID instančního objektu služby identity:

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

Použít [az acr úkolu zobrazit] [ az-acr-task-show] příkazu pro uložení `principalId` do proměnné pro použití v pozdější příkazy:

```azurecli
principalID=$(az acr task show --name multiple-reg --registry myregistry --query identity.principalId --output tsv)
```

### <a name="give-identity-push-permissions-to-two-target-container-registries"></a>Udělit oprávnění identity nabízených oznámení do dvou cílové registrů kontejnerů

V této části poskytují oprávnění systém přiřadil identity tak, aby nabízel dvě cílové registrů, s názvem *customregistry1* a *customregistry2*.

Nejprve pomocí [az acr show] [ az-acr-show] příkazu Získejte ID prostředku každý registr a ukládá ID proměnné:

```azurecli
reg1_id=$(az acr show --name customregistry1 --query id --output tsv)
reg2_id=$(az acr show --name customregistry2 --query id --output tsv)
```

Použití [vytvořit přiřazení role az] [ az-role-assignment-create] příkazu přiřaďte identitu `acrpush` roli pro každý registr. Tato role má oprávnění k pull a push Image do registru kontejneru.

```azurecli
az role assignment create --assignee $principalID --scope $reg1_id --role acrpush
az role assignment create --assignee $principalID --scope $reg2_id --role acrpush
```

### <a name="add-target-registry-credentials-to-task"></a>Přidat přihlašovací údaje registru cíle úlohy

Teď použijte [přidání az acr úloh pověření] [ az-acr-task-credential-add] příkaz pro přidání identity přihlašovací údaje k úloze tak, aby ho mohli ověřit oba registrů cílového.

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

Použití [az acr úlohy] [ az-acr-task-run] příkaz k ruční aktivaci úlohy. `--set` Parametr se používá a zajistěte tak předání přihlašovací jména serveru z registrů dvě cílové hodnoty pro proměnné úkolů `REGISTRY1` a `REGISTRY2`.

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

## <a name="example-task-with-a-user-assigned-identity"></a>Příklad: Úlohu uživatelsky přiřazené identity

V tomto příkladu vytvoření uživatelsky přiřazené identity s oprávněním ke čtení tajných kódů z trezoru klíčů Azure. Tato identita přiřadíte vícekrokových úkolů, která čte tajného kódu, sestavení image a přihlásí do Azure CLI se přečíst značku image.

### <a name="create-a-key-vault-and-store-a-secret"></a>Vytvoření trezoru klíčů a uložíte tajný klíč

Nejprve, pokud je potřeba, vytvořte skupinu prostředků s názvem *myResourceGroup* v *eastus* umístění následujícím [vytvořit skupiny az] [ az-group-create]příkaz:

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Použití [az keyvault vytvořit] [ az-keyvault-create] příkaz pro vytvoření trezoru klíčů. Nezapomeňte zadat název jedinečný trezoru klíčů. 

```azurecli-interactive
az keyvault create --name mykeyvault --resource-group myResourceGroup --location eastus
```

Store ukázkový tajný klíč v trezoru klíčů pomocí [az keyvault secret sady] [ az-keyvault-secret-set] příkaz:

```azurecli
az keyvault secret set \
  --name SampleSecret \
  --value "Hello ACR Tasks!" \
  --description ACRTasksecret  \
  --vault-name mykeyvault
```

Můžete například chtít ukládat přihlašovací údaje k ověření pomocí privátního registru Dockeru, aby si můžete vyžádat privátní image.

### <a name="create-an-identity"></a>Vytvoření identity

Vytvoření identity s názvem *myACRTasksId* v předplatném pomocí [vytvoření az identity] [ az-identity-create] příkazu. Můžete použít stejnou skupinu prostředků, které jste použili k vytvoření registru kontejnerů nebo trezoru klíčů nebo na jiném.

```azurecli-interactive
az identity create --resource-group myResourceGroup --name myACRTasksId
```

Ke konfiguraci identity v následujících krocích, použijte [az identity zobrazit] [ az-identity-show] příkazu pro uložení identity prostředku, ID a ID instančního objektu v proměnné.

```azurecli
# Get resource ID of the user-assigned identity
resourceID=$(az identity show --resource-group myResourceGroup --name myACRTasksId --query id --output tsv)

# Get service principal ID of the user-assigned identity
principalID=$(az identity show --resource-group myResourceGroup --name myACRTasksId --query principalId --output tsv)
```

### <a name="grant-identity-access-to-keyvault-to-read-secret"></a>Identita udělení přístupu k trezoru klíčů pro čtení tajného kódu

Spusťte následující příkaz [az keyvault set-policy] [ az-keyvault-set-policy] příkaz pro nastavení zásad přístupu pro trezor klíčů. Následující příklad umožňuje uživatelsky přiřazené identity k získání tajné kódy z trezoru klíčů. Tento přístup je potřeba později k vícekrokového úkol spuštěn úspěšně.

```azurecli-interactive
 az keyvault set-policy --name mykeyvault --resource-group myResourceGroup --object-id $principalID --secret-permissions get
```

### <a name="grant-identity-reader-access-to-the-resource-group-for-registry"></a>Udělit identity čtečky přístup do skupiny prostředků pro registr

Spusťte následující příkaz [vytvořit přiřazení role az] [ az-role-assignment-create] příkazu přiřaďte identitu role Čtenář, v tomto případě do skupiny prostředků obsahující zdroj registru. Tato role je potřeba později k vícekrokového úkol spuštěn úspěšně.

```azurecli
az role assignment create --role reader --resource-group myResourceGroup --assignee $principalID
```

### <a name="create-task-with-user-assigned-identity"></a>Vytvoření úlohy s využitím uživatelsky přiřazené identity

Nyní vytvořte [vícekrokových úkolů](container-registry-tasks-multi-step.md) a přiřaďte ho uživatelsky přiřazené identity. Pro tuto úlohu příklad vytvoření [soubor YAML](container-registry-tasks-reference-yaml.md) s názvem `managed-identities.yaml` do místního pracovního adresáře a vložte následující obsah. Nezapomeňte nahradit název trezoru klíčů v souboru s názvem trezoru klíčů

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

Tento úkol provede následující akce:

* Ověří, že můžete získat přístup ke tajný klíč v trezoru klíčů. Tento krok je pro demonstrační účely. Ve skutečném scénáři musíte úloh krokem k získání přihlašovacích údajů pro přístup k privátním úložišti Docker Hubu.
* Vytvoří a předá `mywebsite` image do registru zdroje.
* Protokoly do příkazového řádku Azure do seznamu `my-website` obrázku značky v registru zdroje.

Vytvořte úlohu volá *msitask* a předejte mu ID prostředku uživatelsky přiřazené identity, kterou jste předtím vytvořili. Tento příklad úkolu je vytvořený z `managed-identities.yaml` soubor uložený v místní pracovní adresář, takže budete muset aktivovat ručně.

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

Ve výstupu tohoto příkazu `identity` část ukazuje identity typu `UserAssigned` je nastavena v úloze. `principalId` Je ID instančního objektu služby identity:

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

Použití [az acr úlohy] [ az-acr-task-run] příkaz k ruční aktivaci úlohy. `--set` Parametr se používá a zajistěte tak předání názvu registru zdroje do úlohy:

```azurecli
az acr task run --name msitask --registry myregistry --set registryName=myregistry  
```

Výstup ukazuje tajný kód je vyřešen, image úspěšně sestavíte a vloženo a protokoly úlohy do příkazového řádku Azure s použitím identity ke čtení z registru zdrojové značka obrázku:

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


## <a name="next-steps"></a>Další postup

V tomto článku jste se dozvěděli o použití spravovaných identit s Azure Container Registry úlohy a jak:

> [!div class="checklist"]
> * Povolit identitu se systém přiřadil nebo přiřazené uživatele v rámci úlohy služby ACR
> * Udělit přístup identit k prostředkům Azure, jako jsou jiné registry kontejnerů Azure
> * Použití spravované identity pro přístup k prostředkům z úlohy  

* Další informace o [spravovaných identit pro prostředky Azure](/azure/active-directory/managed-identities-azure-resources/).

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
