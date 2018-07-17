---
title: Kurz – Sestavení imagí kontejnerů v cloudu se službou Azure Container Registry Build
description: V tomto kurzu se naučíte sestavit image kontejneru Dockeru v Azure se službou Azure Container Registry Build (ACR Build) a pak ji nasadit do Azure Container Instances.
services: container-registry
author: mmacy
manager: jeconnoc
ms.service: container-registry
ms.topic: tutorial
ms.date: 05/11/2018
ms.author: marsma
ms.custom: mvc
ms.openlocfilehash: d86c47c890fd15515c590e06b395ca82f9747ffe
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2018
ms.locfileid: "38461465"
---
# <a name="tutorial-build-container-images-in-the-cloud-with-azure-container-registry-build"></a>Kurz: Sestavení imagí kontejnerů v cloudu se službou Azure Container Registry Build

**ACR Build** je sada funkcí ve službě Azure Container Registry, která poskytuje zjednodušená a efektivní sestavení imagí kontejnerů Dockeru v Azure. V tomto článku se dozvíte, jak používat funkci *Quick Build* služby ACR Build. Funkce Quick Build rozšiřuje vývoj vnitřní smyčky do cloudu, poskytuje ověření úspěšnosti sestavení a automatické vkládání úspěšně sestavených imagí do registru kontejnerů. Vaše image se nativně sestavují v cloudu, blízko registru, a umožňují tak rychlejší nasazení.

Veškeré vaše znalosti ohledně souboru Dockerfile můžete přímo aplikovat ve službě ACR Build. Pokud chcete provádět sestavení v cloudu pomocí služby ACR Build, nemusíte soubory Dockerfile měnit, stačí změnit jen spouštěný příkaz.

V tomto kurzu, který je první částí série, se naučíte:

> [!div class="checklist"]
> * Získat zdrojový kód ukázkové aplikace
> * Sestavit image kontejneru v Azure
> * Nasazení kontejneru do služby Azure Container Instances

V následujících kurzech se naučíte používat úlohy sestavení služby ACR Build pro automatizovaná sestavení image kontejneru při potvrzení kódu a aktualizaci základní image.

[!INCLUDE [container-registry-build-preview-note](../../includes/container-registry-build-preview-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Pokud chcete Azure CLI používat místně, musíte mít nainstalovanou verzi Azure CLI **2.0.32** nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade rozhraní příkazového řádku, přečtěte si téma [Instalace Azure CLI][azure-cli].

## <a name="prerequisites"></a>Požadavky

### <a name="github-account"></a>Účet GitHub

Pokud ještě nemáte účet na webu https://github.com, vytvořte si ho. Tato série kurzů používá úložiště GitHub k předvedení automatických sestavení imagí ve službě ACR Build.

### <a name="fork-sample-repository"></a>Vytvoření forku ukázkového úložiště

Potom použijte uživatelské rozhraní GitHubu a vytvořte fork ukázkového úložiště v účtu GitHub. V tomto kurzu sestavíte image kontejneru ze zdroje v úložišti a v dalším kurzu vložíte potvrzení do forku úložiště a spustíte automatické sestavení.

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

## <a name="build-in-azure-with-acr-build"></a>Sestavení v Azure pomocí služby ACR Build

Když teď máte zdrojový kód ve svém počítači, postupujte podle těchto kroků k vytvoření registru kontejneru a sestavení image kontejneru se službou ACR Build.

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

Když teď máte registr, použijte službu ACR Build a sestavte image kontejneru ze vzorového kódu. Spuštěním příkazu [az acr build][az-acr-build] použijte funkci *Quick Build*:

```azurecli-interactive
az acr build --registry $ACR_NAME --image helloacrbuild:v1 .
```

Výstup příkazu [az acr build][az-acr-build] je podobný následujícímu. Můžete si zobrazit nahrání zdrojového kódu („obsahu“) do Azure a podrobnosti operace `docker build`, kterou ACR Build spouští v cloudu. Vzhledem k tomu, že služba ACR Build používá k sestavení imagí `docker build`, můžete začít okamžitě používat ACR Build, aniž by se vyžadovaly změny souborů Dockerfile.

```console
$ az acr build --registry $ACR_NAME --image helloacrbuild:v1 .
Sending build context (4.909 KiB) to ACR.
Queued a build with build ID: aa1
Waiting for a build agent...
Sending build context to Docker daemon  22.53kB
Step 1/5 : FROM node:9-alpine
9-alpine: Pulling from library/node
Digest: sha256:5149aec8f508d48998e6230cdc8e6832cba192088b442c8ef7e23df3c6892cd3
Status: Image is up to date for node:9-alpine
 ---> 7af437a39ec2
Step 2/5 : COPY . /src
 ---> 0c4814714938
Step 3/5 : RUN cd /src && npm install
 ---> Running in 4f77ce7b330d
npm notice created a lockfile as package-lock.json. You should commit this file.
npm WARN helloworld@1.0.0 No repository field.

up to date in 0.096s
Removing intermediate container 4f77ce7b330d
 ---> e0eb24339e07
Step 4/5 : EXPOSE 80
 ---> Running in 872bd29edbc7
Removing intermediate container 872bd29edbc7
 ---> 22ba8d8ffb4e
Step 5/5 : CMD ["node", "/src/server.js"]
 ---> Running in 1a40c05c4122
Removing intermediate container 1a40c05c4122
 ---> 0a9a4b74fb53
Successfully built 0a9a4b74fb53
Successfully tagged mycontainerregistry.azurecr.io/helloacrbuild:v1
time="2018-05-10T19:10:20Z" level=info msg="Running command docker push mycontainerregistry.azurecr.io/helloacrbuild:v1"
The push refers to repository [mycontainerregistry.azurecr.io/helloacrbuild]
d2b301f7ef94: Preparing
d0e0f2bb8747: Preparing
26b0c207c4a9: Preparing
917e7cdebc8b: Preparing
9dfa40a0da3b: Preparing
26b0c207c4a9: Pushed
d2b301f7ef94: Pushed
d0e0f2bb8747: Pushed
9dfa40a0da3b: Pushed
917e7cdebc8b: Pushed
v1: digest: sha256:78d7980b4c80a078192bd4749c27eeae56421079606ed7b7d8ae84dbb04193fd size: 1366
time="2018-05-10T19:11:07Z" level=info msg="Running command docker inspect --format \"{{json .RepoDigests}}\" mycontainerregistry.azurecr.io/helloacrbuild:v1"
"["mycontainerregistry.azurecr.io/helloacrbuild@sha256:78d7980b4c80a078192bd4749c27eeae56421079606ed7b7d8ae84dbb04193fd"]"
time="2018-05-10T19:11:07Z" level=info msg="Running command docker inspect --format \"{{json .RepoDigests}}\" node:9-alpine"
"["node@sha256:5149aec8f508d48998e6230cdc8e6832cba192088b442c8ef7e23df3c6892cd3"]"
ACR Builder discovered the following dependencies:
- image:
    registry: mycontainerregistry.azurecr.io
    repository: helloacrbuild
    tag: v1
    digest: sha256:78d7980b4c80a078192bd4749c27eeae56421079606ed7b7d8ae84dbb04193fd
  runtime-dependency:
    registry: registry.hub.docker.com
    repository: library/node
    tag: 9-alpine
    digest: sha256:5149aec8f508d48998e6230cdc8e6832cba192088b442c8ef7e23df3c6892cd3

Build complete
Build ID: aa1 was successful after 52.522222729s
```

Služba ACR Build zobrazí téměř na konci výstupu závislosti, které u image zjistila. ACR Build díky tomu může automatizovat sestavení imagí při aktualizacích základních imagí, například při aktualizaci základní image opravami operačního systému nebo architektury. Informace o podpoře aktualizací základních imagí ve službě ACR Build najdete v dalších částech této série kurzů.

## <a name="deploy-to-azure-container-instances"></a>Nasazení do Azure Container Instances

Ve výchozím nastavení služba ACR Build automaticky vloží úspěšně sestavené image do registru a umožní vám je z registru okamžitě nasadit.

V této části vytvoříte Azure Key Vault a instanční objekt a pak pomocí přihlašovacích údajů instančního objektu nasadíte kontejner do Azure Container Instances (ACI).

### <a name="configure-registry-authentication"></a>Konfigurace ověřování registru

Veškeré produkční scénáře by měly pro přístup k registru kontejneru Azure používat [instanční objekty][service-principal-auth]. Instanční objekty vám umožní poskytnout řízení přístupu k vašim imagím kontejnerů na základě role. Můžete například nakonfigurovat instanční objekt s přístupem k registru pouze ke čtení.

#### <a name="create-key-vault"></a>Vytvoření trezoru klíčů

Pokud ještě nemáte trezor ve službě [Azure Key Vault](/azure/key-vault/), vytvořte si ho v Azure CLI pomocí následujících příkazů.

```azurecli-interactive
AKV_NAME=$ACR_NAME-vault

az keyvault create --resource-group $RES_GROUP --name $AKV_NAME
```

#### <a name="create-service-principal-and-store-credentials"></a>Vytvoření instančního objektu a uložení přihlašovacích údajů

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

* `$ACR_NAME-pull-usr`: ID instančního objektu, které se bude používat jako **uživatelské jméno** registru kontejneru.
* `$ACR_NAME-pull-pwd`: heslo instančního objektu, které se bude používat jako **heslo** registru kontejneru.

Teď můžete na tyto tajné kódy odkazovat názvem, když vy nebo vaše aplikace a služby budou načítat image z tohoto registru.

### <a name="deploy-container-with-azure-cli"></a>Nasazení kontejneru pomocí Azure CLI

Když teď jsou přihlašovací údaje instančního objektu uložené jako tajné kódy služby Azure Key Vault, mohou je vaše aplikace a služby používat pro přístup k vašemu privátnímu registru.

Spuštěním následujícího příkazu [az container create][az-container-create] nasaďte instanci kontejneru. Příkaz používá k ověření u registru kontejneru přihlašovací údaje instančního objektu uložené ve službě Azure Key Vault.

```azurecli-interactive
az container create \
    --resource-group $RES_GROUP \
    --name acr-build \
    --image $ACR_NAME.azurecr.io/helloacrbuild:v1 \
    --registry-login-server $ACR_NAME.azurecr.io \
    --registry-username $(az keyvault secret show --vault-name $AKV_NAME --name $ACR_NAME-pull-usr --query value -o tsv) \
    --registry-password $(az keyvault secret show --vault-name $AKV_NAME --name $ACR_NAME-pull-pwd --query value -o tsv) \
    --dns-name-label acr-build-$ACR_NAME \
    --query "{FQDN:ipAddress.fqdn}" \
    --output table
```

Hodnota `--dns-name-label` musí být v Azure jedinečná, proto předchozí příkaz připojí název registru kontejneru k popisku názvu DNS kontejneru. Výstup příkazu zobrazí plně kvalifikovaný název domény kontejneru, například:

```console
$ az container create \
>     --resource-group $RES_GROUP \
>     --name acr-build \
>     --image $ACR_NAME.azurecr.io/helloacrbuild:v1 \
>     --registry-login-server $ACR_NAME.azurecr.io \
>     --registry-username $(az keyvault secret show --vault-name $AKV_NAME --name $ACR_NAME-pull-usr --query value -o tsv) \
>     --registry-password $(az keyvault secret show --vault-name $AKV_NAME --name $ACR_NAME-pull-pwd --query value -o tsv) \
>     --dns-name-label acr-build-$ACR_NAME \
>     --query "{FQDN:ipAddress.fqdn}" \
>     --output table
FQDN
-------------------------------------------
acr-build-1175.eastus.azurecontainer.io
```

Poznamenejte si tento plně kvalifikovaný název domény kontejneru, použijete ho v další části.

### <a name="verify-deployment"></a>Ověření nasazení

Pokud chcete sledovat proces spuštění kontejneru, použijte příkaz [az container attach][az-container-attach]:

```azurecli-interactive
az container attach --resource-group $RES_GROUP --name acr-build
```

Výstup příkazu `az container attach` nejdřív zobrazí stav kontejneru, protože načte image a spustí se, potom sváže STDOUT a STDERR místní konzoly s STDOUT a STDEER kontejneru.

```console
$ az container attach --resource-group $RES_GROUP --name acr-build
Container 'acr-build' is in state 'Waiting'...
Container 'acr-build' is in state 'Running'...
(count: 1) (last timestamp: 2018-04-03 19:45:37+00:00) pulling image "mycontainerregistry.azurecr.io/helloacrbuild:v1"
(count: 1) (last timestamp: 2018-04-03 19:45:44+00:00) Successfully pulled image "mycontainerregistry.azurecr.io/helloacrbuild:v1"
(count: 1) (last timestamp: 2018-04-03 19:45:44+00:00) Created container with id 094ab4da40138b36ca15fc2ad5cac351c358a7540a32e22b52f78e96a4cb3413
(count: 1) (last timestamp: 2018-04-03 19:45:44+00:00) Started container with id 094ab4da40138b36ca15fc2ad5cac351c358a7540a32e22b52f78e96a4cb3413

Start streaming logs:
Server running at http://localhost:80
```

Když se zobrazí `Server running at http://localhost:80`, přejděte v prohlížeči na plně kvalifikovaný název domény kontejneru a zobrazte běžící aplikaci:

![Snímek obrazovky ukázkové aplikace vykreslené v prohlížeči][quick-build-02-browser]

Pokud chcete konzolu od kontejneru odpojit, stiskněte `Control+C`.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Instanci kontejneru zastavte pomocí příkazu [az container delete][az-container-delete]:

```azurecli-interactive
az container delete --resource-group $RES_GROUP --name acr-build
```

Pokud chcete odebrat *všechny* prostředky, které jste v tomto kurzu vytvořili, včetně registru kontejneru, trezoru klíčů a instančního objektu, použijte následující příkazy: Tyto prostředky se ale používají v [dalším kurzu](container-registry-tutorial-build-task.md) série. Pokud tedy přejdete přímo k dalšímu kurzu, můžete si je chtít nechat.

```azurecli-interactive
az group delete --resource-group $RES_GROUP
az ad sp delete --id http://$ACR_NAME-pull
```

## <a name="next-steps"></a>Další kroky

Teď když jste otestovali vnitřní smyčku s funkcí Quick Build, můžete nakonfigurovat **úlohu sestavení**, která bude spouštět sestavení imagí kontejnerů při potvrzení zdrojového kódu do úložiště Git:

> [!div class="nextstepaction"]
> [Spouštění automatických sestavení pomocí úloh sestavení](container-registry-tutorial-build-task.md)

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
[service-principal-auth]: container-registry-auth-service-principal.md

<!-- IMAGES -->
[quick-build-01-fork]: ./media/container-registry-tutorial-quick-build/quick-build-01-fork.png
[quick-build-02-browser]: ./media/container-registry-tutorial-quick-build/quick-build-02-browser.png
