---
title: Kurz – sestavení imagí kontejnerů v cloudu – Azure Container Registry úlohy
description: V tomto kurzu zjistíte, jak sestavit image kontejneru Dockeru v Azure pomocí Azure Container Registry Tasks (ACR Tasks) a pak ji nasadit do služby Azure Container Instances.
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: tutorial
ms.date: 09/24/2018
ms.author: danlep
ms.custom: seodec18, mvc
ms.openlocfilehash: eafd6a75b4297056bcf4c5415f77179cefde6541
ms.sourcegitcommit: 1c1f258c6f32d6280677f899c4bb90b73eac3f2e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/11/2018
ms.locfileid: "53256685"
---
# <a name="tutorial-build-and-deploy-container-images-in-the-cloud-with-azure-container-registry-tasks"></a>Kurz: Sestavování a nasazování imagí kontejnerů v cloudu pomocí služby Azure Container Registry úlohy

**ACR Tasks** je sada funkcí ve službě Azure Container Registry, která poskytuje zjednodušená a efektivní sestavení imagí kontejnerů Dockeru v Azure. V tomto článku se dozvíte, jak používat funkci *rychlé úlohy* služby ACR Tasks.

Vývojový cyklus typu „vnitřní smyčka“ je iterativní proces psaní kódu, sestavení a testování aplikace před potvrzením do správy zdrojového kódu. Rychlá úloha rozšiřuje vnitřní smyčku do cloudu, poskytuje ověření úspěšnosti sestavení a automatické odesílání úspěšně sestavených imagí do registru kontejneru. Vaše image se nativně sestavují v cloudu, blízko registru, a umožňují tak rychlejší nasazení.

Veškeré vaše znalosti ohledně souboru Dockerfile můžete přímo aplikovat ve službě ACR Tasks. Pokud chcete provádět sestavení v cloudu pomocí ACR Tasks, nemusíte soubory Dockerfile měnit, stačí změnit jen spouštěný příkaz.

V tomto kurzu, který je první částí série, se naučíte:

> [!div class="checklist"]
> * Získat zdrojový kód ukázkové aplikace
> * Sestavit image kontejneru v Azure
> * Nasazení kontejneru do služby Azure Container Instances

V následujících kurzech se naučíte použít ACR Tasks k automatizovanému sestavení imagu kontejneru při potvrzení kódu a aktualizaci základní image.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Pokud chcete Azure CLI používat místně, musíte mít nainstalovanou verzi Azure CLI **2.0.46** nebo novější a přihlásit se pomocí příkazu [az login][az-login]. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade rozhraní příkazového řádku (CLI), přečtěte si téma [Instalace Azure CLI][azure-cli].

## <a name="prerequisites"></a>Požadavky

### <a name="github-account"></a>Účet GitHub

Pokud ještě nemáte účet na webu https://github.com, vytvořte si ho. Tato série kurzů používá úložiště GitHub k předvedení automatizovaných sestavení imagí v ACR Tasks.

### <a name="fork-sample-repository"></a>Vytvoření forku ukázkového úložiště

Potom použijte uživatelské rozhraní GitHubu a vytvořte fork ukázkového úložiště v účtu GitHub. V tomto kurzu sestavíte image kontejneru ze zdroje v úložišti a v dalším kurzu odešlete potvrzení do forku úložiště a spustíte automatizovanou úlohu.

Vytvořte fork tohoto úložiště: https://github.com/Azure-Samples/acr-build-helloworld-node

![Snímek obrazovky s tlačítkem forku (zvýrazněným) v GitHubu][quick-build-01-fork]

### <a name="clone-your-fork"></a>Klonování forku

Po vytvoření forku úložiště fork naklonujte a zadejte adresář obsahující místní klon.

Úložiště naklonujte pomocí příkazu `git`, **\<your-github-username\>** nahraďte svým uživatelským jménem pro GitHub:

```azurecli-interactive
git clone https://github.com/<your-github-username>/acr-build-helloworld-node
```

Zadejte adresář obsahující zdrojový kód:

```azurecli-interactive
cd acr-build-helloworld-node
```

### <a name="bash-shell"></a>Prostředí Bash

Příkazy v této sérii kurzů jsou formátované pro prostředí Bash. Pokud byste chtěli raději použít PowerShell, příkazový řádek nebo jiné prostředí, budete možná podle toho muset přizpůsobit pokračování řádku a formát proměnné prostředí.

## <a name="build-in-azure-with-acr-tasks"></a>Sestavení v Azure pomocí ACR Tasks

Když teď máte zdrojový kód ve svém počítači, postupujte podle těchto kroků k vytvoření registru kontejneru a sestavení image kontejneru pomocí ACR Tasks.

Aby bylo spouštění ukázkových souborů jednodušší, používají kurzy v této sérii proměnné prostředí shell. Spuštěním následujícího příkazu nastavte proměnnou `ACR_NAME`. **\<registry-name\>** nahraďte jedinečným názvem nového registru kontejneru. Název registru musí být jedinečný v rámci Azure a musí obsahovat 5 až 50 alfanumerických znaků. Další prostředky, které v tomto kurzu vytvoříte, jsou založené na tomto názvu. Proto byste měli změnit jenom tuto první proměnnou.

```azurecli-interactive
ACR_NAME=<registry-name>
```

S vyplněnou proměnnou prostředí registru kontejneru byste teď měli být schopni zkopírovat a vložit zbytek příkazů v kurzu, aniž byste museli upravovat nějaké hodnoty. Spuštěním následujících příkazů vytvořte skupinu prostředků a registr kontejneru:

```azurecli-interactive
RES_GROUP=$ACR_NAME # Resource Group name

az group create --resource-group $RES_GROUP --location eastus
az acr create --resource-group $RES_GROUP --name $ACR_NAME --sku Standard --location eastus
```

Když teď máte registr, pomocí ACR Tasks sestavte image kontejneru ze vzorového kódu. Spuštěním příkazu [az acr build][az-acr-build] proveďte *rychlou úlohu*:

```azurecli-interactive
az acr build --registry $ACR_NAME --image helloacrtasks:v1 .
```

Výstup příkazu [az acr build][az-acr-build] je podobný následujícímu. Můžete si zobrazit nahrání zdrojového kódu („obsahu“) do Azure a podrobnosti operace `docker build`, kterou úloha ACR spouští v cloudu. Vzhledem k tomu, že úlohy ACR používají k sestavení imagí `docker build`, můžete začít okamžitě používat ACR Tasks, aniž by se vyžadovaly změny souborů Dockerfile.

```console
$ az acr build --registry $ACR_NAME --image helloacrtasks:v1 .
Packing source code into tar file to upload...
Sending build context (4.813 KiB) to ACR...
Queued a build with build ID: da1
Waiting for build agent...
2018/08/22 18:31:42 Using acb_vol_01185991-be5f-42f0-9403-a36bb997ff35 as the home volume
2018/08/22 18:31:42 Setting up Docker configuration...
2018/08/22 18:31:43 Successfully set up Docker configuration
2018/08/22 18:31:43 Logging in to registry: myregistry.azurecr.io
2018/08/22 18:31:55 Successfully logged in
Sending build context to Docker daemon   21.5kB
Step 1/5 : FROM node:9-alpine
9-alpine: Pulling from library/node
Digest: sha256:8dafc0968fb4d62834d9b826d85a8feecc69bd72cd51723c62c7db67c6dec6fa
Status: Image is up to date for node:9-alpine
 ---> a56170f59699
Step 2/5 : COPY . /src
 ---> 88087d7e709a
Step 3/5 : RUN cd /src && npm install
 ---> Running in e80e1263ce9a
npm notice created a lockfile as package-lock.json. You should commit this file.
npm WARN helloworld@1.0.0 No repository field.

up to date in 0.1s
Removing intermediate container e80e1263ce9a
 ---> 26aac291c02e
Step 4/5 : EXPOSE 80
 ---> Running in 318fb4c124ac
Removing intermediate container 318fb4c124ac
 ---> 113e157d0d5a
Step 5/5 : CMD ["node", "/src/server.js"]
 ---> Running in fe7027a11787
Removing intermediate container fe7027a11787
 ---> 20a27b90eb29
Successfully built 20a27b90eb29
Successfully tagged myregistry.azurecr.io/helloacrtasks:v1
2018/08/22 18:32:11 Pushing image: myregistry.azurecr.io/helloacrtasks:v1, attempt 1
The push refers to repository [myregistry.azurecr.io/helloacrtasks]
6428a18b7034: Preparing
c44b9827df52: Preparing
172ed8ca5e43: Preparing
8c9992f4e5dd: Preparing
8dfad2055603: Preparing
c44b9827df52: Pushed
172ed8ca5e43: Pushed
8dfad2055603: Pushed
6428a18b7034: Pushed
8c9992f4e5dd: Pushed
v1: digest: sha256:b038dcaa72b2889f56deaff7fa675f58c7c666041584f706c783a3958c4ac8d1 size: 1366
2018/08/22 18:32:43 Successfully pushed image: myregistry.azurecr.io/helloacrtasks:v1
2018/08/22 18:32:43 Step ID acb_step_0 marked as successful (elapsed time in seconds: 15.648945)
The following dependencies were found:
- image:
    registry: myregistry.azurecr.io
    repository: helloacrtasks
    tag: v1
    digest: sha256:b038dcaa72b2889f56deaff7fa675f58c7c666041584f706c783a3958c4ac8d1
  runtime-dependency:
    registry: registry.hub.docker.com
    repository: library/node
    tag: 9-alpine
    digest: sha256:8dafc0968fb4d62834d9b826d85a8feecc69bd72cd51723c62c7db67c6dec6fa
  git: {}

Run ID: da1 was successful after 1m9.970148252s
```

ACR Tasks zobrazí téměř na konci výstupu závislosti, které u image zjistila. ACR Tasks díky tomu může automatizovat sestavení imagí při aktualizacích základních imagí, například při aktualizaci základní image opravami operačního systému nebo architektury. Informace o podpoře aktualizací základních imagí v ACR Tasks najdete v dalších částech této série kurzů.

## <a name="deploy-to-azure-container-instances"></a>Nasazení do Azure Container Instances

Ve výchozím nastavení úlohy ACR automaticky vloží úspěšně sestavené image do registru a umožní vám je z registru okamžitě nasadit.

V této části vytvoříte Azure Key Vault a instanční objekt a pak pomocí přihlašovacích údajů instančního objektu nasadíte kontejner do Azure Container Instances (ACI).

### <a name="configure-registry-authentication"></a>Konfigurace ověřování registru

Veškeré produkční scénáře by měly pro přístup k registru kontejneru Azure používat [instanční objekty][service-principal-auth]. Instanční objekty vám umožní poskytnout řízení přístupu k vašim imagím kontejnerů na základě role. Můžete například nakonfigurovat instanční objekt s přístupem k registru pouze ke čtení.

#### <a name="create-a-key-vault"></a>Vytvořte trezor klíčů

Pokud ještě nemáte trezor ve službě [Azure Key Vault](/azure/key-vault/), vytvořte si ho v Azure CLI pomocí následujících příkazů.

```azurecli-interactive
AKV_NAME=$ACR_NAME-vault

az keyvault create --resource-group $RES_GROUP --name $AKV_NAME
```

#### <a name="create-a-service-principal-and-store-credentials"></a>Vytvoření instančního objektu a uložení přihlašovacích údajů

Teď je potřeba vytvořit instanční objekt a uložit jeho přihlašovací údaje do trezoru klíčů.

Pomocí příkazu [az ad sp create-for-rbac][az-ad-sp-create-for-rbac] vytvořte instanční objekt a pomocí příkazu [az keyvault secret set][az-keyvault-secret-set] uložte **heslo** instančního objektu do trezoru:

```azurecli-interactive
# Create service principal, store its password in AKV (the registry *password*)
az keyvault secret set \
  --vault-name $AKV_NAME \
  --name $ACR_NAME-pull-pwd \
  --value $(az ad sp create-for-rbac \
                --name $ACR_NAME-pull \
                --scopes $(az acr show --name $ACR_NAME --query id --output tsv) \
                --role reader \
                --query password \
                --output tsv)
```

Argument `--role` v předchozím příkazu nakonfiguruje instanční objekt rolí *čtenáře*, která mu udělí přístup k registru pouze ke čtení. Pokud chcete udělit přístup pro zápis i čtení, změňte argument `--role` na *přispěvatel*.

V dalším kroku uložte *appId* instančního objektu v trezoru. Jedná se o **uživatelské jméno**, které předáte službě Azure Container Registry pro ověření:

```azurecli-interactive
# Store service principal ID in AKV (the registry *username*)
az keyvault secret set \
    --vault-name $AKV_NAME \
    --name $ACR_NAME-pull-usr \
    --value $(az ad sp show --id http://$ACR_NAME-pull --query appId --output tsv)
```

Vytvořili jste Azure Key Vault a uložili jste do něj dva tajné kódy:

* `$ACR_NAME-pull-usr`: ID objektu zabezpečení služby pro použití jako registr kontejneru **uživatelské jméno**.
* `$ACR_NAME-pull-pwd`: Heslo instančního objektu služby pro použití jako registr kontejneru **heslo**.

Teď můžete na tyto tajné kódy odkazovat názvem, když vy nebo vaše aplikace a služby budou načítat image z tohoto registru.

### <a name="deploy-a-container-with-azure-cli"></a>Nasazení kontejneru pomocí Azure CLI

Když teď jsou přihlašovací údaje instančního objektu uložené jako tajné kódy služby Azure Key Vault, mohou je vaše aplikace a služby používat pro přístup k vašemu privátnímu registru.

Spuštěním následujícího příkazu [az container create][az-container-create] nasaďte instanci kontejneru. Příkaz používá k ověření u registru kontejneru přihlašovací údaje instančního objektu uložené ve službě Azure Key Vault.

```azurecli-interactive
az container create \
    --resource-group $RES_GROUP \
    --name acr-tasks \
    --image $ACR_NAME.azurecr.io/helloacrtasks:v1 \
    --registry-login-server $ACR_NAME.azurecr.io \
    --registry-username $(az keyvault secret show --vault-name $AKV_NAME --name $ACR_NAME-pull-usr --query value -o tsv) \
    --registry-password $(az keyvault secret show --vault-name $AKV_NAME --name $ACR_NAME-pull-pwd --query value -o tsv) \
    --dns-name-label acr-tasks-$ACR_NAME \
    --query "{FQDN:ipAddress.fqdn}" \
    --output table
```

Hodnota `--dns-name-label` musí být v Azure jedinečná, proto předchozí příkaz připojí název registru kontejneru k popisku názvu DNS kontejneru. Výstup příkazu zobrazí plně kvalifikovaný název domény kontejneru, například:

```console
$ az container create \
>     --resource-group $RES_GROUP \
>     --name acr-tasks \
>     --image $ACR_NAME.azurecr.io/helloacrtasks:v1 \
>     --registry-login-server $ACR_NAME.azurecr.io \
>     --registry-username $(az keyvault secret show --vault-name $AKV_NAME --name $ACR_NAME-pull-usr --query value -o tsv) \
>     --registry-password $(az keyvault secret show --vault-name $AKV_NAME --name $ACR_NAME-pull-pwd --query value -o tsv) \
>     --dns-name-label acr-tasks-$ACR_NAME \
>     --query "{FQDN:ipAddress.fqdn}" \
>     --output table
FQDN
----------------------------------------------
acr-tasks-myregistry.eastus.azurecontainer.io
```

Poznamenejte si tento plně kvalifikovaný název domény kontejneru, použijete ho v další části.

### <a name="verify-the-deployment"></a>Ověření nasazení

Pokud chcete sledovat proces spuštění kontejneru, použijte příkaz [az container attach][az-container-attach]:

```azurecli-interactive
az container attach --resource-group $RES_GROUP --name acr-tasks
```

Výstup příkazu `az container attach` nejdřív zobrazí stav kontejneru, protože načte image a spustí se, potom sváže STDOUT a STDERR místní konzoly s STDOUT a STDEER kontejneru.

```console
$ az container attach --resource-group $RES_GROUP --name acr-tasks
Container 'acr-tasks' is in state 'Running'...
(count: 1) (last timestamp: 2018-08-22 18:39:10+00:00) pulling image "myregistry.azurecr.io/helloacrtasks:v1"
(count: 1) (last timestamp: 2018-08-22 18:39:15+00:00) Successfully pulled image "myregistry.azurecr.io/helloacrtasks:v1"
(count: 1) (last timestamp: 2018-08-22 18:39:17+00:00) Created container
(count: 1) (last timestamp: 2018-08-22 18:39:17+00:00) Started container

Start streaming logs:
Server running at http://localhost:80
```

Když se zobrazí `Server running at http://localhost:80`, přejděte v prohlížeči na plně kvalifikovaný název domény kontejneru a zobrazte spuštěnou aplikaci. Plně kvalifikovaný název domény by se měl zobrazit ve výstupu příkazu `az container create`, který jste spustili v předchozí části.

![Snímek obrazovky ukázkové aplikace vykreslené v prohlížeči][quick-build-02-browser]

Pokud chcete konzolu od kontejneru odpojit, stiskněte `Control+C`.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Instanci kontejneru zastavte pomocí příkazu [az container delete][az-container-delete]:

```azurecli-interactive
az container delete --resource-group $RES_GROUP --name acr-tasks
```

Pokud chcete odebrat *všechny* prostředky, které jste v tomto kurzu vytvořili, včetně registru kontejneru, trezoru klíčů a instančního objektu, použijte následující příkazy: Tyto prostředky se ale používají v [dalším kurzu](container-registry-tutorial-build-task.md) série. Pokud tedy přejdete přímo k dalšímu kurzu, můžete si je chtít nechat.

```azurecli-interactive
az group delete --resource-group $RES_GROUP
az ad sp delete --id http://$ACR_NAME-pull
```

## <a name="next-steps"></a>Další postup

Teď, když jste pomocí rychlé úlohy otestovali vnitřní smyčku, můžete nakonfigurovat **úlohu sestavení**, která aktivuje sestavení imagí kontejnerů při potvrzení zdrojového kódu do úložiště Git:

> [!div class="nextstepaction"]
> [Aktivace automatických sestavení pomocí úloh](container-registry-tutorial-build-task.md)

<!-- LINKS - External -->
[sample-archive]: https://github.com/Azure-Samples/acr-build-helloworld-node/archive/master.zip

<!-- LINKS - Internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-build]: /cli/azure/acr#az-acr-build
[az-ad-sp-create-for-rbac]: /cli/azure/ad/sp#az-ad-sp-create-for-rbac
[az-container-attach]: /cli/azure/container#az-container-attach
[az-container-create]: /cli/azure/container#az-container-create
[az-container-delete]: /cli/azure/container#az-container-delete
[az-keyvault-create]: /cli/azure/keyvault/secret#az-keyvault-create
[az-keyvault-secret-set]: /cli/azure/keyvault/secret#az-keyvault-secret-set
[az-login]: /cli/azure/reference-index#az-login
[service-principal-auth]: container-registry-auth-service-principal.md

<!-- IMAGES -->
[quick-build-01-fork]: ./media/container-registry-tutorial-quick-build/quick-build-01-fork.png
[quick-build-02-browser]: ./media/container-registry-tutorial-quick-build/quick-build-02-browser.png
