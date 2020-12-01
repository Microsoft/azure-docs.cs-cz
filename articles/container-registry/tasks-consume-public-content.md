---
title: Pracovní postup úkolu pro správu obsahu veřejného registru
description: Vytvořte pracovní postup automatizovaného Azure Container Registry úloh pro sledování, správu a využívání obsahu veřejné image v privátním registru kontejneru Azure.
author: SteveLasker
ms.topic: article
ms.author: stevelas
ms.date: 10/29/2020
ms.custom: ''
ms.openlocfilehash: 4fba6290b4973e797c13943fc9be4fadb19f3274
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/30/2020
ms.locfileid: "96349278"
---
# <a name="how-to-consume-and-maintain-public-content-with-azure-container-registry-tasks"></a>Jak využívat a spravovat veřejný obsah pomocí Azure Container Registrych úloh

Tento článek popisuje ukázkový pracovní postup v Azure Container Registry, který vám může pomáhat při správě využívání a udržování veřejného obsahu:

1. Importujte místní kopie závislých veřejných imagí.
1. Ověřuje veřejné image prostřednictvím kontroly zabezpečení a funkčního testování.
1. Povýšit obrázky na privátní registry pro interní použití.
1. Aktivovat základní aktualizace obrázků pro aplikace, které jsou závislé na veřejném obsahu.
1. K automatizaci tohoto pracovního postupu použijte [Azure Container Registry úlohy](container-registry-tasks-overview.md) .

Tento pracovní postup je shrnutý na následujícím obrázku:

![Spotřebovává se pracovní postup pro veřejné obsahy.](./media/tasks-consume-public-content/consuming-public-content-workflow.png)

Pracovní postup ověřovaného importu pomáhá spravovat závislosti vaší organizace na externě spravovaných artefaktech, například obrázky zdroje z veřejných registrů, včetně [Docker Hub][docker-hub], [GCR][gcr], [Quay][quay], [GitHub Container Registry][ghcr], [Microsoft Container Registry][mcr]nebo dokonce i jiných [registrů Azure Container Registry][acr]. 

Základní informace o rizicích zavedených závislostmi na veřejném obsahu a o tom, jak je používat Azure Container Registry k jejich zmírnění, najdete v [příspěvku blogu s veřejným obsahem][oci-consuming-public-content] pro rozhraní OCI a [Správa veřejného obsahu pomocí Azure Container Registry](buffer-gate-public-content.md).

K dokončení tohoto postupu můžete použít Azure Cloud Shell nebo místní instalaci rozhraní příkazového řádku Azure CLI. Doporučuje se Azure CLI verze 2,10 nebo novější. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI][install-cli].

## <a name="scenario-overview"></a>Přehled scénáře

![importovat součásti pracovního postupu](./media/tasks-consume-public-content/consuming-public-content-objects.png)

Tento návod nastaví:

1. Tři **Registry kontejnerů**, které představují:
   * Simulované [centrum Docker][docker-hub] ( `publicregistry` ) pro podporu změny základní image
   * Týmový registr ( `contoso` ) pro sdílení privátních obrázků
   * Společnost/sdílený registr ( `baseartifacts` ) pro importovaný veřejný obsah
1. **Úkol ACR** v každém registru. Úkoly:  
   1. Sestavení simulované veřejné `node` Image
   1. Import a ověření `node` image do sdíleného registru společnost/tým
   1. Sestavení a nasazení `hello-world` Image
1. **Definice úloh ACR**, včetně konfigurací pro:
1. Kolekce **přihlašovacích údajů registru**, které odkazují na Trezor klíčů
1. Kolekce **tajných** kódů, které jsou k dispozici v rámci `acr-task.yaml` , což jsou ukazatele na Trezor klíčů
1. Kolekce **nakonfigurovaných hodnot** používaných v rámci `acr-task.yaml`
1. **Trezor klíčů Azure** pro zabezpečení všech tajných kódů
1. **Instance kontejneru Azure**, která je hostitelem `hello-world` sestavovací aplikace

## <a name="prerequisites"></a>Požadavky

Následující kroky nakonfigurují hodnoty pro prostředky vytvořené a používané v tomto návodu.

### <a name="set-environment-variables"></a>Nastavení proměnných prostředí

Nakonfigurujte proměnné jedinečné pro vaše prostředí. Dodržujeme osvědčené postupy pro umístění prostředků s trvalým obsahem do vlastní skupiny prostředků, aby se minimalizovalo náhodné odstranění. V případě potřeby je ale můžete umístit do jedné skupiny prostředků.

Příklady v tomto článku jsou formátované pro prostředí bash shell.

```bash
# Set the three registry names, must be globally unique:
REGISTRY_PUBLIC=publicregistry
REGISTRY_BASE_ARTIFACTS=contosobaseartifacts
REGISTRY=contoso

# set the location all resources will be created in:
RESOURCE_GROUP_LOCATION=eastus

# default resource groups
REGISTRY_PUBLIC_RG=${REGISTRY_PUBLIC}-rg
REGISTRY_BASE_ARTIFACTS_RG=${REGISTRY_BASE_ARTIFACTS}-rg
REGISTRY_RG=${REGISTRY}-rg

# fully qualified registry urls
REGISTRY_DOCKERHUB_URL=docker.io
REGISTRY_PUBLIC_URL=${REGISTRY_PUBLIC}.azurecr.io
REGISTRY_BASE_ARTIFACTS_URL=${REGISTRY_BASE_ARTIFACTS}.azurecr.io
REGISTRY_URL=${REGISTRY}.azurecr.io

# Azure key vault for storing secrets, name must be globally unique
AKV=acr-task-credentials
AKV_RG=${AKV}-rg

# ACI for hosting the deployed application
ACI=hello-world-aci
ACI_RG=${ACI}-rg
```

### <a name="git-repositories-and-tokens"></a>Úložiště Git a tokeny

Chcete-li simulovat prostředí, rozdělíte každé z následujících úložišť Git do úložišť, která můžete spravovat. 

* https://github.com/importing-public-content/base-image-node.git
* https://github.com/importing-public-content/import-baseimage-node.git
* https://github.com/importing-public-content/hello-world.git

Pak aktualizujte následující proměnné pro rozvětvené úložiště.

`:main`Připojení ke konci adres URL Gitu představuje výchozí větev úložiště.

```bash
GIT_BASE_IMAGE_NODE=https://github.com/<your-fork>/base-image-node.git#main
GIT_NODE_IMPORT=https://github.com/<your-fork>/import-baseimage-node.git#main
GIT_HELLO_WORLD=https://github.com/<your-fork>/hello-world.git#main
```

K naklonování a vytváření webhooků Git potřebujete [přístupový token GitHubu (Pat)][git-token] pro ACR úlohy. Postup vytvoření tokenu s požadovanými oprávněními pro soukromé úložiště najdete v tématu [vytvoření přístupového tokenu GitHubu](container-registry-tutorial-build-task.md#create-a-github-personal-access-token). 

```bash
GIT_TOKEN=<set-git-token-here>
```

### <a name="docker-hub-credentials"></a>Přihlašovací údaje k dokovacímu centru  
Pokud nechcete, aby se požadavky na omezování a identitu vyhnuly při přijímání imagí z dokovacího centra, vytvořte [token Docker Hub][docker-hub-tokens]. Pak nastavte následující proměnné prostředí:

```bash
REGISTRY_DOCKERHUB_USER=<yourusername>
REGISTRY_DOCKERHUB_PASSWORD=<yourtoken>
```

### <a name="create-registries"></a>Vytvořit Registry

Pomocí příkazů rozhraní příkazového řádku Azure vytvořte tři Registry kontejneru úrovně Premium, z nichž každá má vlastní skupinu prostředků:

```azurecli-interactive
az group create --name $REGISTRY_PUBLIC_RG --location $RESOURCE_GROUP_LOCATION
az acr create --resource-group $REGISTRY_PUBLIC_RG --name $REGISTRY_PUBLIC --sku Premium

az group create --name $REGISTRY_BASE_ARTIFACTS_RG --location $RESOURCE_GROUP_LOCATION
az acr create --resource-group $REGISTRY_BASE_ARTIFACTS_RG --name $REGISTRY_BASE_ARTIFACTS --sku Premium

az group create --name $REGISTRY_RG --location $RESOURCE_GROUP_LOCATION
az acr create --resource-group $REGISTRY_RG --name $REGISTRY --sku Premium
```

### <a name="create-key-vault-and-set-secrets"></a>Vytvoření trezoru klíčů a nastavení tajných kódů

Vytvoření trezoru klíčů:

```azurecli-interactive
az group create --name $AKV_RG --location $RESOURCE_GROUP_LOCATION
az keyvault create --resource-group $AKV_RG --name $AKV
```

V trezoru klíčů nastavte uživatelské jméno a token Docker centra:

```azurecli-interactive
az keyvault secret set \
--vault-name $AKV \
--name registry-dockerhub-user \
--value $REGISTRY_DOCKERHUB_USER

az keyvault secret set \
--vault-name $AKV \
--name registry-dockerhub-password \
--value $REGISTRY_DOCKERHUB_PASSWORD
```

Nastavte a ověřte PAT Gitu v trezoru klíčů:

```azurecli-interactive
az keyvault secret set --vault-name $AKV --name github-token --value $GIT_TOKEN

az keyvault secret show --vault-name $AKV --name github-token --query value -o tsv
```

### <a name="create-resource-group-for-an-azure-container-instance"></a>Vytvoření skupiny prostředků pro instanci kontejneru Azure

Tato skupina prostředků se používá při nasazování bitové kopie v pozdější úloze `hello-world` .

```azurecli-interactive
az group create --name $ACI_RG --location $RESOURCE_GROUP_LOCATION
```

## <a name="create-public-node-base-image"></a>Vytvořit veřejný `node` základní obrázek

Pokud chcete simulovat `node` image v Docker Hub, vytvořte [úkol ACR][acr-task] , který sestaví a zachová veřejnou image. Tato instalace umožňuje simulovat změny v rámci `node` udržování imagí.

```azurecli-interactive
az acr task create \
  --name node-public \
  -r $REGISTRY_PUBLIC \
  -f acr-task.yaml \
  --context $GIT_BASE_IMAGE_NODE \
  --git-access-token $(az keyvault secret show \
                        --vault-name $AKV \
                        --name github-token \
                        --query value -o tsv) \
  --set REGISTRY_FROM_URL=${REGISTRY_DOCKERHUB_URL}/ \
  --assign-identity
```

Abyste zabránili omezování Docker, přidejte do úlohy [přihlašovací údaje k dokovacímu centru][docker-hub-tokens] . Příkaz [pověření úlohy ACR][acr-task-credentials] se dá použít k předání přihlašovacích údajů Docker do libovolného registru, včetně Docker Hub.

```azurecli-interactive
az acr task credential add \
  -n node-public \
  -r $REGISTRY_PUBLIC \
  --login-server $REGISTRY_DOCKERHUB_URL \
  -u https://${AKV}.vault.azure.net/secrets/registry-dockerhub-user \
  -p https://${AKV}.vault.azure.net/secrets/registry-dockerhub-password \
  --use-identity [system]
```

Udělte úloze oprávnění ke čtení hodnot z trezoru klíčů:

```azurecli-interactive
az keyvault set-policy \
  --name $AKV \
  --resource-group $AKV_RG \
  --object-id $(az acr task show \
                  --name node-public \
                  --registry $REGISTRY_PUBLIC \
                  --query identity.principalId --output tsv) \
  --secret-permissions get
```

[Úlohy se můžou aktivovat][acr-task-triggers] pomocí potvrzení Git, aktualizací základní image, časovačů nebo ručních spuštění. 

Spusťte úlohu ručně, aby se vygenerovala `node` bitová kopie:

```azurecli-interactive
az acr task run -r $REGISTRY_PUBLIC -n node-public
```

Vypíše obrázek v simulovaném veřejném registru:

```azurecli-interactive
az acr repository show-tags -n $REGISTRY_PUBLIC --repository node
```

## <a name="create-the-hello-world-image"></a>Vytvoření `hello-world` Image

V závislosti na simulované veřejné `node` imagi Sestavte `hello-world` image.

### <a name="create-token-for-pull-access-to-simulated-public-registry"></a>Vytvořit token pro přístup k vyžádanému přístupu ke simulovanému veřejnému registru

Vytvoření [přístupového tokenu][acr-tokens] k simulovanému veřejnému registru vymezenému na `pull` . Pak ho nastavte v trezoru klíčů:

```azurecli-interactive
az keyvault secret set \
  --vault-name $AKV \
  --name "registry-${REGISTRY_PUBLIC}-user" \
  --value "registry-${REGISTRY_PUBLIC}-user"

az keyvault secret set \
  --vault-name $AKV \
  --name "registry-${REGISTRY_PUBLIC}-password" \
  --value $(az acr token create \
              --name "registry-${REGISTRY_PUBLIC}-user" \
              --registry $REGISTRY_PUBLIC \
              --scope-map _repositories_pull \
              -o tsv \
              --query credentials.passwords[0].value)
```

### <a name="create-token-for-pull-access-by-azure-container-instances"></a>Vytvořit token pro přístup k vyžádanému přístupu pomocí Azure Container Instances

Vytvoření [přístupového tokenu][acr-tokens] do registru hostujícího `hello-world` bitovou kopii s rozsahem, který se má načíst. Pak ho nastavte v trezoru klíčů:

```azurecli-interactive
az keyvault secret set \
  --vault-name $AKV \
  --name "registry-${REGISTRY}-user" \
  --value "registry-${REGISTRY}-user"

az keyvault secret set \
  --vault-name $AKV \
  --name "registry-${REGISTRY}-password" \
  --value $(az acr token create \
              --name "registry-${REGISTRY}-user" \
              --registry $REGISTRY \
              --repository hello-world content/read \
              -o tsv \
              --query credentials.passwords[0].value)
```

### <a name="create-task-to-build-and-maintain-hello-world-image"></a>Vytvořit úlohu pro sestavení a údržbu `hello-world` obrázku

Následující příkaz vytvoří úlohu z definice v `acr-tasks.yaml` `hello-world` úložišti. Kroky úkolu sestaví `hello-world` image a pak ji nasadí do Azure Container Instances. Skupina prostředků pro Azure Container Instances se vytvořila v předchozí části. Voláním `az container create` v úloze s pouze rozdílem v `image:tag` , je úloha v rámci tohoto návodu nasazena do stejné instance.

```azurecli-interactive
az acr task create \
  -n hello-world \
  -r $REGISTRY \
  -f acr-task.yaml \
  --context $GIT_HELLO_WORLD \
  --git-access-token $(az keyvault secret show \
                        --vault-name $AKV \
                        --name github-token \
                        --query value -o tsv) \
  --set REGISTRY_FROM_URL=${REGISTRY_PUBLIC_URL}/ \
  --set KEYVAULT=$AKV \
  --set ACI=$ACI \
  --set ACI_RG=$ACI_RG \
  --assign-identity
```

Přidat pověření do úlohy pro simulovaný veřejný registr:

```azurecli-interactive
az acr task credential add \
  -n hello-world \
  -r $REGISTRY \
  --login-server $REGISTRY_PUBLIC_URL \
  -u https://${AKV}.vault.azure.net/secrets/registry-${REGISTRY_PUBLIC}-user \
  -p https://${AKV}.vault.azure.net/secrets/registry-${REGISTRY_PUBLIC}-password \
  --use-identity [system]
```

Udělte úloze oprávnění ke čtení hodnot z trezoru klíčů:

```azurecli-interactive
az keyvault set-policy \
  --name $AKV \
  --resource-group $AKV_RG \
  --object-id $(az acr task show \
                  --name hello-world \
                  --registry $REGISTRY \
                  --query identity.principalId --output tsv) \
  --secret-permissions get
```

Udělte úloze přístup k vytváření a správě Azure Container Instances tím, že udělí přístup ke skupině prostředků:

```azurecli-interactive
az role assignment create \
  --assignee $(az acr task show \
  --name hello-world \
  --registry $REGISTRY \
  --query identity.principalId --output tsv) \
  --scope $(az group show -n $ACI_RG --query id -o tsv) \
  --role owner
```

S vytvořeným a nakonfigurovaným úkolem spusťte úlohu pro sestavení a nasazení `hello-world` Image:

```azurecli-interactive
az acr task run -r $REGISTRY -n hello-world
```

Po vytvoření Získejte IP adresu kontejneru, který je hostitelem `hello-world` image.

```azurecli-interactive
az container show \
  --resource-group $ACI_RG \
  --name ${ACI} \
  --query ipAddress.ip \
  --out tsv
```

V prohlížeči přejděte na IP adresu, abyste viděli běžící aplikaci.

## <a name="update-the-base-image-with-a-questionable-change"></a>Aktualizace základní image se změnou "s otázkou"

Tato část simuluje změnu základní image, která by mohla způsobit problémy v prostředí.

1. Otevřete `Dockerfile` v rozvětvené `base-image-node` úložiště.
1. Změnou na `BACKGROUND_COLOR` nastavte `Orange` simulaci změny.

```Dockerfile
ARG REGISTRY_NAME=
FROM ${REGISTRY_NAME}node:15-alpine
ENV NODE_VERSION 15-alpine
ENV BACKGROUND_COLOR Orange
```

Potvrďte změny a sledujte ACR úlohy, které se mají automaticky začít sestavovat.

Sledujte úlohu, která se má začít spouštět:

```azurecli-interactive
watch -n1 az acr task list-runs -r $REGISTRY_PUBLIC -o table
```

Nakonec byste měli vidět stav `Succeeded` na základě triggeru `Commit` :

```azurecli-interactive
RUN ID    TASK      PLATFORM    STATUS     TRIGGER    STARTED               DURATION
--------  --------  ----------  ---------  ---------  --------------------  ----------
ca4       hub-node  linux       Succeeded  Commit     2020-10-24T05:02:29Z  00:00:22
```

Stisknutím **kombinace kláves CTRL + C** ukončete příkaz Watch a potom zobrazte protokoly pro poslední spuštění:

```azurecli-interactive
az acr task logs -r $REGISTRY_PUBLIC
```

Po `node` dokončení image `watch` ACR úlohy, které automaticky začnou sestavovat `hello-world` Image:

```azurecli-interactive
watch -n1 az acr task list-runs -r $REGISTRY -o table
```

Nakonec byste měli vidět stav `Succeeded` na základě triggeru `Image Update` :

```azurecli-interactive
RUN ID    TASK         PLATFORM    STATUS     TRIGGER       STARTED               DURATION
--------  -----------  ----------  ---------  ------------  --------------------  ----------
dau       hello-world  linux       Succeeded  Image Update  2020-10-24T05:08:45Z  00:00:31
```

Stisknutím **kombinace kláves CTRL + C** ukončete příkaz Watch a potom zobrazte protokoly pro poslední spuštění:

```azurecli-interactive
az acr task logs -r $REGISTRY
```

Po dokončení Získejte IP adresu webu, který je hostitelem aktualizované `hello-world` Image:

```azurecli-interactive
az container show \
  --resource-group $ACI_RG \
  --name ${ACI} \
  --query ipAddress.ip \
  --out tsv
```

V prohlížeči přejdete na web, který by měl mít oranžové (problematické) pozadí.

### <a name="checking-in"></a>Vrácení se změnami

V tomto okamžiku jste vytvořili `hello-world` image, která je automaticky založená na potvrzeních Git a změnách základní `node` image. V tomto příkladu je úloha vytvořena na základě základní image v Azure Container Registry, ale může se použít libovolný podporovaný registr.

Základní aktualizace image automaticky znovu aktivuje spuštění úlohy, když `node` se aktualizuje obrázek. Jak vidíte, ne všechny aktualizace byly žádoucí.

## <a name="gated-imports-of-public-content"></a>Ověřované importy veřejného obsahu

Aby nedocházelo k tomu, aby se zabránilo odesílání změn z nepostradatelných úloh, je možné přidat kontrolu zabezpečení a funkční testy.

V této části vytvoříte úlohu ACR k těmto akcím:

* Sestavení testovací image
* Spuštění funkčního testovacího skriptu `./test.sh` proti imagi testu
* Pokud se image úspěšně testuje, naimportujte veřejnou image do registru **baseimages** .

### <a name="add-automation-testing"></a>Přidat testování automatizace

Pro bránu jakéhokoli nadřazeného obsahu je implementováno automatizované testování. V tomto příkladu je k `test.sh` dispozici příkaz, který kontroluje `$BACKGROUND_COLOR` . Pokud se test nezdaří, `EXIT_CODE` vrátí se část z, `1` která způsobí selhání kroku ACR úlohy a ukončí úlohu. Testy lze rozbalit v jakékoli formě nástrojů, včetně výsledků protokolování. Tato brána je spravovaná odpovědí předat/neúspěšné ve skriptu, reprodukována zde:

```bash
if [ ""$(echo $BACKGROUND_COLOR | tr '[:lower:]' '[:upper:]') = 'RED' ]; then
    echo -e "\e[31mERROR: Invalid Color:\e[0m" ${BACKGROUND_COLOR}
    EXIT_CODE=1
else
  echo -e "\e[32mValidation Complete - No Known Errors\e[0m"
fi
exit ${EXIT_CODE}
```
### <a name="task-yaml"></a>YAML úlohy 

Přečtěte si `acr-task.yaml` v `import-baseimage-node` úložišti, které provádí následující kroky:

1. Sestavte testovací základní Image pomocí následujících souboru Dockerfile:
    ```dockerfile
    ARG REGISTRY_FROM_URL=
    FROM ${REGISTRY_FROM_URL}node:15-alpine
    WORKDIR /test
    COPY ./test.sh .
    CMD ./test.sh
    ```
1. Po dokončení ověřte bitovou kopii spuštěním kontejneru, který se spustí `./test.sh`
1. Pouze v případě úspěšného dokončení spusťte kroky importu, které jsou ověřované pomocí nástroje. `when: ['validate-base-image']`

```yaml
version: v1.1.0
steps:
  - id: build-test-base-image
    # Build off the base image we'll track
    # Add a test script to do unit test validations
    # Note: the test validation image isn't saved to the registry
    # but the task logs captures log validation results
    build: >
      --build-arg REGISTRY_FROM_URL={{.Values.REGISTRY_FROM_URL}}
      -f ./Dockerfile
      -t {{.Run.Registry}}/node-import:test
      .
  - id: validate-base-image
    # only continues if node-import:test returns a non-zero code
    when: ['build-test-base-image']
    cmd: "{{.Run.Registry}}/node-import:test"
  - id: pull-base-image
    # import the public image to base-artifacts
    # Override the stable tag,
    # and create a unique tag to enable rollback
    # to a previously working image
    when: ['validate-base-image']
    cmd: >
        docker pull {{.Values.REGISTRY_FROM_URL}}node:15-alpine
  - id: retag-base-image
    when: ['pull-base-image']
    cmd: docker tag {{.Values.REGISTRY_FROM_URL}}node:15-alpine {{.Run.Registry}}/node:15-alpine
  - id: retag-base-image-unique-tag
    when: ['pull-base-image']
    cmd: docker tag {{.Values.REGISTRY_FROM_URL}}node:15-alpine {{.Run.Registry}}/node:15-alpine-{{.Run.ID}}
  - id: push-base-image
    when: ['retag-base-image', 'retag-base-image-unique-tag']
    push:
    - "{{.Run.Registry}}/node:15-alpine"
    - "{{.Run.Registry}}/node:15-alpine-{{.Run.ID}}"
```

### <a name="create-task-to-import-and-test-base-image"></a>Vytvoří úkol pro import a testování základní image.

```azurecli-interactive
  az acr task create \
  --name base-import-node \
  -f acr-task.yaml \
  -r $REGISTRY_BASE_ARTIFACTS \
  --context $GIT_NODE_IMPORT \
  --git-access-token $(az keyvault secret show \
                        --vault-name $AKV \
                        --name github-token \
                        --query value -o tsv) \
  --set REGISTRY_FROM_URL=${REGISTRY_PUBLIC_URL}/ \
  --assign-identity
```

Přidat pověření do úlohy pro simulovaný veřejný registr:

```azurecli-interactive
az acr task credential add \
  -n base-import-node \
  -r $REGISTRY_BASE_ARTIFACTS \
  --login-server $REGISTRY_PUBLIC_URL \
  -u https://${AKV}.vault.azure.net/secrets/registry-${REGISTRY_PUBLIC}-user \
  -p https://${AKV}.vault.azure.net/secrets/registry-${REGISTRY_PUBLIC}-password \
  --use-identity [system]
```

Udělte úloze oprávnění ke čtení hodnot z trezoru klíčů:

```azurecli-interactive
az keyvault set-policy \
  --name $AKV \
  --resource-group $AKV_RG \
  --object-id $(az acr task show \
                  --name base-import-node \
                  --registry $REGISTRY_BASE_ARTIFACTS \
                  --query identity.principalId --output tsv) \
  --secret-permissions get
```

Spusťte úlohu importu:

```azurecli-interactive
az acr task run -n base-import-node -r $REGISTRY_BASE_ARTIFACTS
```

> [!NOTE]
> Pokud se úloha z důvodu nezdařila `./test.sh: Permission denied` , zajistěte, aby měl skript oprávnění ke spuštění, a potvrďte návrat do úložiště Git:
>```bash
>chmod +x ./test.sh
>```

## <a name="update-hello-world-image-to-build-from-gated-node-image"></a>Aktualizovat `hello-world` obrázek pro sestavení z ověřované `node` Image

Vytvořte [přístupový token][acr-tokens] pro přístup k registru základních artefaktů vymezenému na `read` z `node` úložiště. Pak nastavte v trezoru klíčů:

```azurecli-interactive
az keyvault secret set \
  --vault-name $AKV \
  --name "registry-${REGISTRY_BASE_ARTIFACTS}-user" \
  --value "registry-${REGISTRY_BASE_ARTIFACTS}-user"

az keyvault secret set \
  --vault-name $AKV \
  --name "registry-${REGISTRY_BASE_ARTIFACTS}-password" \
  --value $(az acr token create \
              --name "registry-${REGISTRY_BASE_ARTIFACTS}-user" \
              --registry $REGISTRY_BASE_ARTIFACTS \
              --repository node content/read \
              -o tsv \
              --query credentials.passwords[0].value)
```

Přidejte přihlašovací údaje do úlohy **Hello-World** pro Registry základních artefaktů:

```azurecli-interactive
az acr task credential add \
  -n hello-world \
  -r $REGISTRY \
  --login-server $REGISTRY_BASE_ARTIFACTS_URL \
  -u https://${AKV}.vault.azure.net/secrets/registry-${REGISTRY_BASE_ARTIFACTS}-user \
  -p https://${AKV}.vault.azure.net/secrets/registry-${REGISTRY_BASE_ARTIFACTS}-password \
  --use-identity [system]
```

Aktualizujte úlohu tak, aby se změnila na `REGISTRY_FROM_URL` použití `BASE_ARTIFACTS` registru.

```azurecli-interactive
az acr task update \
  -n hello-world \
  -r $REGISTRY \
  --set KEYVAULT=$AKV \
  --set REGISTRY_FROM_URL=${REGISTRY_BASE_ARTIFACTS_URL}/ \
  --set ACI=$ACI \
  --set ACI_RG=$ACI_RG
```

Spusťte úlohu **Hello-World** a změňte její základní závislost Image:

```azurecli-interactive
az acr task run -r $REGISTRY -n hello-world
```

## <a name="update-the-base-image-with-a-valid-change"></a>Aktualizace základní Image pomocí platné změny

1. Otevřete `Dockerfile` v `base-image-node` úložišti.
1. Změňte na `BACKGROUND_COLOR` , `Green` Chcete-li simulovat platnou změnu.

```Dockerfile
ARG REGISTRY_NAME=
FROM ${REGISTRY_NAME}node:15-alpine
ENV NODE_VERSION 15-alpine
ENV BACKGROUND_COLOR Green
```

Potvrďte změnu a sledujte posloupnost aktualizací:

```azurecli-interactive
watch -n1 az acr task list-runs -r $REGISTRY_PUBLIC -o table
```

Po spuštění zadejte **CTRL + C** a sledujte protokoly:

```azurecli-interactive
az acr task logs -r $REGISTRY_PUBLIC
```

Po dokončení Sledujte úlohu **základní-image-import** :

```azurecli-interactive
watch -n1 az acr task list-runs -r $REGISTRY_BASE_ARTIFACTS -o table
```

Po spuštění zadejte **CTRL + C** a sledujte protokoly:

```azurecli-interactive
az acr task logs -r $REGISTRY_BASE_ARTIFACTS
```

Po dokončení Sledujte úlohu **Hello-World** :

```azurecli-interactive
watch -n1 az acr task list-runs -r $REGISTRY -o table
```

Po spuštění zadejte **CTRL + C** a sledujte protokoly:

```azurecli-interactive
az acr task logs -r $REGISTRY
```

Po dokončení Získejte IP adresu webu, který je hostitelem aktualizované `hello-world` Image:

```azurecli-interactive
az container show \
  --resource-group $ACI_RG \
  --name ${ACI} \
  --query ipAddress.ip \
  --out tsv
```

V prohlížeči přejdete na web, který by měl mít zelené (platné) pozadí.

### <a name="view-the-gated-workflow"></a>Zobrazit ověřovaný pracovní postup

Proveďte kroky v předchozí části znovu s barvou pozadí červenou.

1. Otevřete `Dockerfile` v úložišti. `base-image-node`
1. Změňte na `BACKGROUND_COLOR` , `Red` Chcete-li simulovat neplatnou změnu.

```Dockerfile
ARG REGISTRY_NAME=
FROM ${REGISTRY_NAME}node:15-alpine
ENV NODE_VERSION 15-alpine
ENV BACKGROUND_COLOR Red
```

Potvrďte změnu a sledujte posloupnost aktualizací:

```azurecli-interactive
watch -n1 az acr task list-runs -r $REGISTRY_PUBLIC -o table
```

Po spuštění zadejte **CTRL + C** a sledujte protokoly:

```azurecli-interactive
az acr task logs -r $REGISTRY_PUBLIC
```

Po dokončení Sledujte úlohu **základní-image-import** :

```azurecli-interactive
watch -n1 az acr task list-runs -r $REGISTRY_BASE_ARTIFACTS -o table
```

Po spuštění zadejte **CTRL + C** a sledujte protokoly:

```azurecli-interactive
az acr task logs -r $REGISTRY_BASE_ARTIFACTS
```

V tuto chvíli byste měli vidět, že se úloha **Base-import-Node** nedaří ověřit, a zastavit sekvenci k publikování `hello-world` aktualizace. Výstup se podobá tomuto:

```console
[...]
2020/10/30 03:57:39 Launching container with name: validate-base-image
Validating Image
NODE_VERSION: 15-alpine
BACKGROUND_COLOR: Red
ERROR: Invalid Color: Red
2020/10/30 03:57:40 Container failed during run: validate-base-image. No retries remaining.
failed to run step ID: validate-base-image: exit status 1
```

### <a name="publish-an-update-to-hello-world"></a>Publikování aktualizace `hello-world`

Změny v `hello-world` imagi budou dál používat poslední ověřenou `node` bitovou kopii.

Jakékoli další změny základní `node` image, která projde ověřovaným ověřením, budou aktivovat základní aktualizace obrázků v `hello-world` imagi.

## <a name="cleaning-up"></a>Vyčištění

Pokud už je nepotřebujete, odstraňte prostředky použité v tomto článku.

```azurecli-interactive
az group delete -n $REGISTRY_RG --no-wait -y
az group delete -n $REGISTRY_PUBLIC_RG --no-wait -y
az group delete -n $REGISTRY_BASE_ARTIFACTS_RG --no-wait -y
az group delete -n $AKV_RG --no-wait -y
az group delete -n $ACI_RG --no-wait -y
```

## <a name="next-steps"></a>Další kroky

V tomto článku. pomocí úloh ACR jste vytvořili pracovní postup automatizovaného uzavírání, který zavádí aktualizované základní image do vašeho prostředí. V Azure Container Registry najdete v části související informace o správě imagí.


* [Doporučení pro označování a naznačení verzí imagí kontejneru](container-registry-image-tag-version.md)
* [Uzamknutí image kontejneru ve službě Azure Container Registry](container-registry-image-lock.md)

[install-cli]:                  /cli/azure/install-azure-cli
[acr]:                          https://aka.ms/acr
[acr-repo-permissions]:         ./container-registry-repository-scoped-permissions.md
[acr-task]:                     ./container-registry-tasks-overview.md
[acr-task-triggers]:            container-registry-tasks-overview.md#task-scenarios
[acr-task-credentials]:       container-registry-tasks-authentication-managed-identity.md#4-optional-add-credentials-to-the-task
[acr-tokens]:                   ./container-registry-repository-scoped-permissions.md
[aci]:                          https://aka.ms/aci
[alpine-public-image]:          https://hub.docker.com/_/alpine
[docker-hub]:                   https://hub.docker.com
[docker-hub-tokens]:            https://hub.docker.com/settings/security
[git-token]:                    https://github.com/settings/tokens
[gcr]:                          https://cloud.google.com/container-registry
[ghcr]:                         https://docs.github.com/en/free-pro-team@latest/packages/getting-started-with-github-container-registry/about-github-container-registry
[helm-charts]:                  https://helm.sh
[mcr]:                          https://aka.ms/mcr
[nginx-public-image]:           https://hub.docker.com/_/nginx
[oci-artifacts]:                ./container-registry-oci-artifacts.md
[oci-consuming-public-content]: https://opencontainers.org/posts/blog/2020-10-30-consuming-public-content/
[opa]:                          https://www.openpolicyagent.org/
[quay]:                         https://quay.io