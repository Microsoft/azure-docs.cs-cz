---
title: Spuštění rychlého úkolu se šablonou
description: Zařazení spuštění úlohy ACR do fronty pro sestavení Image pomocí šablony Azure Resource Manager
ms.topic: article
ms.date: 04/22/2020
ms.openlocfilehash: af7bebc311f81bb489fcc8be419f167ff6f9460a
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/20/2021
ms.locfileid: "107781230"
---
# <a name="run-acr-tasks-using-resource-manager-templates"></a>Spouštění úloh ACR pomocí šablon Správce prostředků

[ACR úkoly](container-registry-tasks-overview.md) jsou sadou funkcí v rámci Azure Container Registry, které vám pomůžou se správou a úpravou imagí kontejnerů napříč životním cyklem kontejneru. 

V tomto článku se dozvíte, Azure Resource Manager příklady šablon pro zařazení rychlého spuštění úlohy do fronty, podobně jako v tématu, který můžete vytvořit ručně pomocí příkazu [AZ ACR Build][az-acr-build] .

Správce prostředků šablona pro zařazení spuštění úlohy do fronty je užitečná ve scénářích automatizace a rozšiřuje funkce nástroje `az acr build` . Například:

* Použití šablony k vytvoření registru kontejneru a okamžitému zařazování spuštění úlohy do fronty pro sestavení a vložení image kontejneru
* Umožňuje vytvořit nebo povolit další prostředky, které můžete použít v rychlém spuštění úlohy, jako je spravovaná identita pro prostředky Azure.

## <a name="limitations"></a>Omezení

* Je nutné zadat vzdálený kontext, jako je například úložiště GitHub, jako [zdrojové umístění](container-registry-tasks-overview.md#context-locations) pro spuštění úlohy. Nemůžete použít místní zdrojový kontext.
* Pro úlohy spouštěné pomocí spravované identity je povolená jenom spravovaná identita *přiřazená uživatelem* .

## <a name="prerequisites"></a>Požadavky

* **Účet GitHub** – vytvořte účet https://github.com , pokud ho ještě nemáte. 
* **Rozvětvení ukázkové úložiště** – pro příklady úloh, které jsou tady uvedené, použijte uživatelské rozhraní GitHubu k rozvětvení následujícího ukázkového úložiště do svého účtu GitHubu: https://github.com/Azure-Samples/acr-build-helloworld-node . Toto úložiště obsahuje vzorový fázemi a zdrojový kód pro vytváření malých imagí kontejneru.

## <a name="example-create-registry-and-queue-task-run"></a>Příklad: vytvoření registru a spuštění úlohy ve frontě

V tomto příkladu se používá [Ukázková šablona](https://github.com/Azure/acr/tree/master/docs/tasks/run-as-deployment/quickdockerbuild) pro vytvoření registru kontejnerů a zařazování spuštění úlohy do fronty, které sestaví a vloží image. 

### <a name="template-parameters"></a>Parametry šablony

V tomto příkladu zadejte hodnoty pro následující parametry šablony:

|Parametr  |Hodnota  |
|---------|---------|
|registr     |Jedinečný název vytvořeného registru         |
|úložiště     |Cílové úložiště pro úlohu sestavení        |
|taskRunName     |Název spuštění úlohy, který určuje značku obrázku |
|sourceLocation     |Vzdálený kontext pro úlohu sestavení, například https://github.com/Azure-Samples/acr-build-helloworld-node . Souboru Dockerfile v kořenovém adresáři úložiště vytvoří image kontejneru pro malou Node.js webovou aplikaci. V případě potřeby použijte jako kontext sestavení své rozvětvení úložiště.         |

### <a name="deploy-the-template"></a>Nasazení šablony

Nasaďte šablonu pomocí příkazu [AZ Deployment Group Create][az-deployment-group-create] . Tento příklad sestaví a vloží do registru s názvem *mycontainerregistry* obrázek *typu HelloWorld-Node: TestRun* .

```azurecli
az deployment group create \
  --resource-group myResourceGroup \
  --template-uri https://raw.githubusercontent.com/Azure/acr/master/docs/tasks/run-as-deployment/quickdockerbuild/azuredeploy.json \
  --parameters \
    registryName=mycontainerregistry \
    repository=helloworld-node \
    taskRunName=testrun \
    sourceLocation=https://github.com/Azure-Samples/acr-build-helloworld-node.git#main
 ```

Předchozí příkaz předává parametry na příkazovém řádku. V případě potřeby je předejte do [souboru parametrů](../azure-resource-manager/templates/parameter-files.md).

### <a name="verify-deployment"></a>Ověření nasazení

Po úspěšném dokončení nasazení ověřte, že se image sestavila spuštěním [AZ ACR úložiště show-Tags][az-acr-repository-show-tags]:

```azurecli
az acr repository show-tags \
  --name mycontainerregistry \
  --repository helloworld-node --output table
```

Výstup:

```console
Result
--------
testrun
```

### <a name="view-run-log"></a>Zobrazit protokol spuštění

Chcete-li zobrazit podrobnosti o spuštění úlohy, zobrazte protokol spuštění.

Nejdřív Získejte ID spuštění pomocí [AZ ACR Task list-][az-acr-task-list-runs] runs.
```azurecli
az acr task list-runs \
  --registry mycontainerregistry --output table
```

Výstup se podobá tomuto:

```console
RUN ID    TASK    PLATFORM    STATUS     TRIGGER    STARTED               DURATION
--------  ------  ----------  ---------  ---------  --------------------  ----------
ca1               linux       Succeeded  Manual     2020-03-23T17:54:28Z  00:00:48
```

Spuštěním [AZ ACR Task logs][az-acr-task-logs] zobrazíte protokoly spuštění úloh pro ID běhu. v tomto případě *CA1*:

```azurecli
az acr task logs \
  --registry mycontainerregistry \
  --run-id ca1
```

Výstup zobrazuje protokol spuštění úlohy.

Můžete také zobrazit protokol spuštění úlohy v Azure Portal. 

1. Přejděte do registru kontejneru.
2. V části **služby** vyberte možnost  >  **spuštěné** úlohy.
3. V tomto případě *CA1* vyberte ID běhu. 

Portál zobrazuje protokol spuštění úlohy.

## <a name="example-task-run-with-managed-identity"></a>Příklad: úloha spuštěna se spravovanou identitou

Použijte [ukázkovou šablonu](https://github.com/Azure/acr/tree/master/docs/tasks/run-as-deployment/quickdockerbuildwithidentity) pro zařazení spuštění úlohy do fronty, která umožňuje spravovanou identitu přiřazenou uživatelem. Během spuštění úlohy se identita ověřuje, aby načetla image z jiného služby Azure Container Registry. 

Tento scénář je podobný [ověřování mezi registry v úloze ACR pomocí identity spravované službou Azure](container-registry-tasks-cross-registry-authentication.md). Organizace může například udržovat centralizovaný registr se základními obrázky, ke kterým přistupovalo více vývojových týmů.

### <a name="prepare-base-registry"></a>Příprava základního registru

Pro demonstrační účely Vytvořte samostatný registr kontejnerů jako základní registr a vložte Node.js základní image získanou z Docker Hub.

1. Vytvořte druhý registr kontejnerů, například *mybaseregistry*, a uložte základní image.
1. Nahrajte `node:9-alpine` image z Docker Hub, označte ji pro svůj základní registr a nahrajte ji do základního registru:

  ```azurecli
  docker pull node:9-alpine
  docker tag node:9-alpine mybaseregistry.azurecr.io/baseimages/node:9-alpine
  az acr login -n mybaseregistry
  docker push mybaseregistry.azurecr.io/baseimages/node:9-alpine
  ```

### <a name="create-new-dockerfile"></a>Vytvořit nové souboru Dockerfile

Vytvořte souboru Dockerfile, který načte základní image ze základního registru. V místním rozvětvení úložiště GitHub proveďte následující kroky, například `https://github.com/myGitHubID/acr-build-helloworld-node.git` .

1. V uživatelském rozhraní GitHubu vyberte **vytvořit nový soubor**.
1. Pojmenujte soubor *souboru Dockerfile-test* a vložte následující obsah. Nahraďte název registru pro *mybaseregistry*.
    ```
    FROM mybaseregistry.azurecr.io/baseimages/node:9-alpine
    COPY . /src
    RUN cd /src && npm install
    EXPOSE 80
    CMD ["node", "/src/server.js"]
    ```
 1. Vyberte **Potvrdit nový soubor**.

[!INCLUDE [container-registry-tasks-user-assigned-id](../../includes/container-registry-tasks-user-assigned-id.md)]

### <a name="give-identity-pull-permissions-to-the-base-registry"></a>Udělení oprávnění pro získání identity základnímu registru

Udělte spravované identitě oprávnění k vyžádání ze základního registru *mybaseregistry*.

Pomocí příkazu [AZ ACR show][az-acr-show] Získejte ID prostředku základního registru a uložte ho do proměnné:

```azurecli
baseregID=$(az acr show \
  --name mybaseregistry \
  --query id --output tsv)
```

Pomocí příkazu [AZ role Assignment Create][az-role-assignment-create] přiřaďte identitu roli Acrpull k základnímu registru. Tato role má oprávnění pouze k vyžádání imagí z registru.

```azurecli
az role assignment create \
  --assignee $principalID \
  --scope $baseregID \
  --role acrpull
```

### <a name="template-parameters"></a>Parametry šablony

V tomto příkladu zadejte hodnoty pro následující parametry šablony:

|Parametr  |Hodnota  |
|---------|---------|
|registr     |Název registru, ve kterém je sestaven obrázek  |
|úložiště     |Cílové úložiště pro úlohu sestavení        |
|taskRunName     |Název spuštění úlohy, který určuje značku obrázku |
|userAssignedIdentity |ID prostředku povoleného uživatelem přiřazené identity v úloze|
|customRegistryIdentity | ID klienta povolené identity přiřazené uživatelem v úloze, která se používá k ověřování pomocí vlastního registru |
|customRegistry |Název přihlašovacího serveru vlastního registru, ke kterému v úloze přistupoval, například *mybaseregistry.azurecr.IO*|
|sourceLocation     |Vzdálený kontext pro úlohu sestavení, například *https://github.com/ \<your-GitHub-ID\> /ACR-Build-HelloWorld-Node.* |
|dockerFilePath | Cesta k souboru Dockerfile ve vzdáleném kontextu, která se používá k sestavení image. |

### <a name="deploy-the-template"></a>Nasazení šablony

Nasaďte šablonu pomocí příkazu [AZ Deployment Group Create][az-deployment-group-create] . Tento příklad sestaví a vloží do registru s názvem *mycontainerregistry* obrázek *typu HelloWorld-Node: TestRun* . Základní Image je načítána z *mybaseregistry.azurecr.IO*.

```azurecli
az deployment group create \
  --resource-group myResourceGroup \
  --template-uri https://raw.githubusercontent.com/Azure/acr/master/docs/tasks/run-as-deployment/quickdockerbuildwithidentity/azuredeploy.json \
  --parameters \
    registryName=mycontainerregistry \
    repository=helloworld-node \
    taskRunName=basetask \
    userAssignedIdentity=$resourceID \
    customRegistryIdentity=$clientID \
    sourceLocation=https://github.com/<your-GitHub-ID>/acr-build-helloworld-node.git#main \
    dockerFilePath=Dockerfile-test \
    customRegistry=mybaseregistry.azurecr.io
```

Předchozí příkaz předává parametry na příkazovém řádku. V případě potřeby je předejte do [souboru parametrů](../azure-resource-manager/templates/parameter-files.md).

### <a name="verify-deployment"></a>Ověření nasazení

Po úspěšném dokončení nasazení ověřte, že se image sestavila spuštěním [AZ ACR úložiště show-Tags][az-acr-repository-show-tags]:

```azurecli
az acr repository show-tags \
  --name mycontainerregistry \
  --repository helloworld-node --output table
```

Výstup:

```console
Result
--------
basetask
```

### <a name="view-run-log"></a>Zobrazit protokol spuštění

Chcete-li zobrazit protokol spuštění, přečtěte si postup uvedený v [předchozí části](#view-run-log).

## <a name="next-steps"></a>Další kroky

 * Podívejte se na další příklady šablon v [úložišti GitHub ACR](https://github.com/Azure/acr/tree/master/docs/tasks/run-as-deployment).
 * Podrobnosti o vlastnostech šablony najdete v referenčních informacích k šabloně pro [spuštění úloh](/azure/templates/microsoft.containerregistry/2019-06-01-preview/registries/taskruns) a [úlohy](/azure/templates/microsoft.containerregistry/2019-06-01-preview/registries/tasks).


<!-- LINKS - Internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-build]: /cli/azure/acr#az_acr_build
[az-acr-show]: /cli/azure/acr#az_acr_show
[az-acr-task-run]: /cli/azure/acr/task#az_acr_task_run
[az-acr-task-logs]: /cli/azure/acr/task#az_acr_task_logs
[az-acr-repository-show-tags]: /cli/azure/acr/repository#az_acr_repository_show_tags
[az-acr-task-list-runs]: /cli/azure/acr/task#az_acr_task_list_runs
[az-deployment-group-create]: /cli/azure/deployment/group#az_deployment_group_create
[az-identity-create]: /cli/azure/identity#az_identity_create
[az-identity-show]: /cli/azure/identity#az_identity_show
[az-role-assignment-create]: /cli/azure/role/assignment#az_role_assignment_create
