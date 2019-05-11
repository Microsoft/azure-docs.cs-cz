---
title: Kurz – kontejneru vícekrokové úlohy – Azure Container registru úlohy
description: V tomto kurzu se dozvíte, jak nakonfigurovat úlohu Azure Container Registry pro automatickou aktivaci vícekrokový pracovní postup sestavení, spouštět, a oznamte Image kontejneru v cloudu, když jste se zavázali zdrojový kód do úložiště Git.
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: tutorial
ms.date: 05/09/2019
ms.author: danlep
ms.custom: seodec18, mvc
ms.openlocfilehash: 09b8e5d31bc6a4ec24633889920e2768bb7ce538
ms.sourcegitcommit: f6c85922b9e70bb83879e52c2aec6307c99a0cac
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/11/2019
ms.locfileid: "65546570"
---
# <a name="tutorial-run-a-multi-step-container-workflow-in-the-cloud-when-you-commit-source-code"></a>Kurz: Spuštění pracovního postupu vícekrokového kontejneru v cloudu, když jste se zavázali zdrojového kódu

Kromě [rychlých úloh](container-registry-tutorial-quick-task.md), podporuje vícekrokové úlohy ACR, pracovních postupů založených na více container, které můžete aktivovat automaticky, když jste se zavázali zdrojový kód do úložiště Git. 

V tomto kurzu se dozvíte, jak soubory YAML příklad použijte k definování vícekrokových úkolů, které sestavení, spouštět, a nabídne jednu nebo víc imagí kontejneru do registru, jakmile potvrdíte zdrojového kódu. Pokud chcete vytvořit úlohu, která automatizuje jen jedné image sestavení na potvrzení kódu, naleznete v tématu [kurzu: Automatizace sestavování imagí kontejneru v cloudu, když potvrdíte nějaký kód zdrojové](container-registry-tutorial-build-task.md). Přehled služby ACR úkoly, naleznete v tématu [automatizovat operačního systému a rozhraní framework opravy chyb s ACR úkoly](container-registry-tasks-overview.md),

V tomto kurzu:

> [!div class="checklist"]
> * Definování vícekrokový úloh pomocí souboru YAML
> * Vytvořit úkol
> * Volitelně můžete přidáte přihlašovací údaje k povolení přístupu k registru jiného úkolu
> * Test úlohy
> * Zobrazení stavu úkolů
> * Aktivace úlohy potvrzením kódu

Tento kurz předpokládá, že jste už dokončili kroky z [předchozího kurzu](container-registry-tutorial-quick-task.md). Pokud jste to ještě neudělali, dokončete před pokračováním kroky v části [Požadavky](container-registry-tutorial-quick-task.md#prerequisites) předchozího kurzu.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Pokud chcete používat rozhraní příkazového řádku Azure CLI místně, musíte mít verzi Azure CLI **2.0.62** nebo novější nainstalován a s přihlášením [az login][az-login]. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade rozhraní příkazového řádku (CLI), přečtěte si téma [Instalace Azure CLI][azure-cli].

[!INCLUDE [container-registry-task-tutorial-prereq.md](../../includes/container-registry-task-tutorial-prereq.md)]

## <a name="create-a-multi-step-task"></a>Vytváření vícekrokových úkolů

Teď, když jste dokončili kroky potřebné k povolení úloh ACR umožňuje načíst stav potvrzení a vytváření webhooků v úložišti, vytvoření vícekrokového úlohu, která aktivuje sestavování, spouštění a nahráním image kontejneru.

### <a name="yaml-file"></a>Soubor YAML

Můžete definovat postup vícekrokových úkolů v [soubor YAML](container-registry-tasks-reference-yaml.md). První příklad vícekrokového úkol pro účely tohoto kurzu je definována v souboru `taskmulti.yaml`, což je v kořenové složce, která jste naklonovali úložiště GitHub:

```yml
version: v1.0.0
steps:
# Build target image
- build: -t {{.Run.Registry}}/hello-world:{{.Run.ID}} -f Dockerfile .
# Run image 
- cmd: -t {{.Run.Registry}}/hello-world:{{.Run.ID}}
  id: test
  detach: true
  ports: ["8080:80"]
- cmd: docker stop test
# Push image
- push:
  - {{.Run.Registry}}/hello-world:{{.Run.ID}}
```

Tato úloha vícekrokového provede následující akce:

1. Spuštění `build` krok k sestavení image ze souboru Dockerfile v pracovním adresáři. Cílení na image `Run.Registry`, registru úloha běží kde je označené jedinečný ACR úlohy ID spuštění. 
1. Spuštění `cmd` se krok spustil bitovou kopii v dočasné kontejneru. Tento příklad spustí kontejner dlouho běžící na pozadí a vrátí ID kontejneru a pak se zastaví kontejner. Ve skutečném scénáři může obsahovat kroky k otestování spuštěný kontejner zajistit, že běží správně.
1. V `push` krok, nahraje obrázek, který byl vytvořen do běhu registru.

### <a name="task-command"></a>Příkaz úloh

Nejdřív vyplňte tyto proměnné prostředí hodnotami vhodnými pro vaše prostředí. Tento krok není nezbytně nutný, ale usnadní provádění víceřádkových příkazů Azure CLI v tomto kurzu. Pokud není naplněn tyto proměnné prostředí, je nutné ručně nahradit jednotlivé hodnoty bez ohledu na to se zobrazí v příklady příkazů.

```azurecli-interactive
ACR_NAME=<registry-name>        # The name of your Azure container registry
GIT_USER=<github-username>      # Your GitHub user account name
GIT_PAT=<personal-access-token> # The PAT you generated in the previous section
```

Teď vytvořte úlohu spuštěním následujícího [az acr úloha vytvoření] [ az-acr-task-create] příkaz:

```azurecli-interactive
az acr task create \
    --registry $ACR_NAME \
    --name example1 \
    --context https://github.com/$GIT_USER/acr-build-helloworld-node.git \
    --branch master \
    --file taskmulti.yaml \
    --git-access-token $GIT_PAT
```

Tato úloha Určuje, že všechny kódu v době záleží na *hlavní* větve v úložišti určené `--context`, ACR úkoly spustí vícekrokové úlohy z kódu v této větvi. Soubor YAML určené `--file` v úložišti kořenové definuje kroky. 

Výstup úspěšného příkazu [az acr task create][az-acr-task-create] je podobný následujícímu:

```console
{
  "agentConfiguration": {
    "cpu": 2
  },
  "creationDate": "2019-05-03T03:14:31.763887+00:00",
  "credentials": null,
  "id": "/subscriptions/<Subscription ID>/resourceGroups/myregistry/providers/Microsoft.ContainerRegistry/registries/myregistry/tasks/taskmulti",
  "location": "westus",
  "name": "example1",
  "platform": {
    "architecture": "amd64",
    "os": "linux",
    "variant": null
  },
  "provisioningState": "Succeeded",
  "resourceGroup": "myresourcegroup",
  "status": "Enabled",
  "step": {
    "baseImageDependencies": null,
    "contextAccessToken": null,
    "contextPath": "https://github.com/gituser/acr-build-helloworld-node.git",
    "taskFilePath": "taskmulti.yaml",
    "type": "FileTask",
    "values": [],
    "valuesFilePath": null
  },
  "tags": null,
  "timeout": 3600,
  "trigger": {
    "baseImageTrigger": {
      "baseImageTriggerType": "Runtime",
      "name": "defaultBaseimageTriggerName",
      "status": "Enabled"
    },
    "sourceTriggers": [
      {
        "name": "defaultSourceTriggerName",
        "sourceRepository": {
          "branch": "master",
          "repositoryUrl": "https://github.com/gituser/acr-build-helloworld-node.git",
          "sourceControlAuthProperties": null,
          "sourceControlType": "Github"
        },
        "sourceTriggerEvents": [
          "commit"
        ],
        "status": "Enabled"
      }
    ]
  },
  "type": "Microsoft.ContainerRegistry/registries/tasks"
}
```

## <a name="test-the-multi-step-workflow"></a>Testování vícekrokového pracovní postup

Testování vícekrokového úkolu, aktivovat ji ručně spuštěním [az acr úlohy] [ az-acr-task-run] příkaz:

```azurecli-interactive
az acr task run --registry $ACR_NAME --name example1
```

Příkaz `az acr task run` ve výchozím nastavení při spuštění příkazu streamuje výstup protokolu do vaší konzoly. Výstup zobrazuje průběh spuštěním jednotlivých kroků úlohy. Následující výstup je zhušťovat zobrazíte klíčové kroky.

```console
Queued a run with ID: cf19
Waiting for an agent...
2019/05/03 03:03:31 Downloading source code...
2019/05/03 03:03:33 Finished downloading source code
2019/05/03 03:03:33 Using acb_vol_cfe6bd55-3076-4215-8091-6a81aec3d1b1 as the home volume
2019/05/03 03:03:33 Creating Docker network: acb_default_network, driver: 'bridge'
2019/05/03 03:03:34 Successfully set up Docker network: acb_default_network
2019/05/03 03:03:34 Setting up Docker configuration...
2019/05/03 03:03:34 Successfully set up Docker configuration
2019/05/03 03:03:34 Logging in to registry: myregistry.azurecr.io
2019/05/03 03:03:35 Successfully logged into myregistry.azurecr.io
2019/05/03 03:03:35 Executing step ID: acb_step_0. Working directory: '', Network: 'acb_default_network'
2019/05/03 03:03:35 Scanning for dependencies...
2019/05/03 03:03:36 Successfully scanned dependencies
2019/05/03 03:03:36 Launching container with name: acb_step_0
Sending build context to Docker daemon  24.06kB

[...]

Successfully built f669bfd170af
Successfully tagged myregistry.azurecr.io/hello-world:cf19
2019/05/03 03:03:43 Successfully executed container: acb_step_0
2019/05/03 03:03:43 Executing step ID: acb_step_1. Working directory: '', Network: 'acb_default_network'
2019/05/03 03:03:43 Launching container with name: acb_step_1
279b1cb6e092b64c8517c5506fcb45494cd5a0bd10a6beca3ba97f25c5d940cd
2019/05/03 03:03:44 Successfully executed container: acb_step_1
2019/05/03 03:03:44 Executing step ID: acb_step_2. Working directory: '', Network: 'acb_default_network'
2019/05/03 03:03:44 Pushing image: myregistry.azurecr.io/hello-world:cf19, attempt 1

[...]

2019/05/03 03:03:46 Successfully pushed image: myregistry.azurecr.io/hello-world:cf19
2019/05/03 03:03:46 Step ID: acb_step_0 marked as successful (elapsed time in seconds: 7.425169)
2019/05/03 03:03:46 Populating digests for step ID: acb_step_0...
2019/05/03 03:03:47 Successfully populated digests for step ID: acb_step_0
2019/05/03 03:03:47 Step ID: acb_step_1 marked as successful (elapsed time in seconds: 0.827129)
2019/05/03 03:03:47 Step ID: acb_step_2 marked as successful (elapsed time in seconds: 2.112113)
2019/05/03 03:03:47 The following dependencies were found:
2019/05/03 03:03:47
- image:
    registry: myregistry.azurecr.io
    repository: hello-world
    tag: cf19
    digest: sha256:6b981a8ca8596e840228c974c929db05c0727d8630465de536be74104693467a
  runtime-dependency:
    registry: registry.hub.docker.com
    repository: library/node
    tag: 9-alpine
    digest: sha256:8dafc0968fb4d62834d9b826d85a8feecc69bd72cd51723c62c7db67c6dec6fa
  git:
    git-head-revision: 1a3065388a0238e52865db1c8f3e97492a43444c

Run ID: cf19 was successful after 18s
```

## <a name="trigger-a-build-with-a-commit"></a>Spuštění sestavení pomocí vložení

Když jste teď ručním spuštěním otestovali úlohu, aktivujte ji automaticky pomocí změny zdrojového kódu.

Nejdřív se ujistěte, že jste v adresáři obsahujícím místní klon [úložiště][sample-repo]:

```azurecli-interactive
cd acr-build-helloworld-node
```

Potom spuštěním následujících příkazů vytvořte, potvrďte a zapište nový soubor do forku úložiště na GitHubu:

```azurecli-interactive
echo "Hello World!" > hello.txt
git add hello.txt
git commit -m "Testing ACR Tasks"
git push origin master
```

Při spuštění příkazu `git push` můžete být vyzváni k zadání přihlašovacích údajů pro GitHub. Zadejte svoje uživatelské jméno pro GitHub a zadejte token PAT, který jste dříve vytvořili pro heslo.

```console
$ git push origin master
Username for 'https://github.com': <github-username>
Password for 'https://githubuser@github.com': <personal-access-token>
```

Jakmile jste vložil potvrzení změn do úložiště, vytvořené úlohami ACR webhook aktivuje a zahajuje úkolů ve službě Azure Container Registry. Zobrazte protokoly pro aktuálně spuštěnou úlohu, abyste mohli ověřit a monitorovat průběh sestavení:

```azurecli-interactive
az acr task logs --registry $ACR_NAME
```

Výstup je podobný následujícímu a zobrazuje aktuálně spuštěnou (nebo naposledy spuštěnou) úlohu:

```console
$ az acr task logs --registry $ACR_NAME
Showing logs of the last created run.
Run ID: cf1d

[...]

Run ID: cf1d was successful after 37s
```

## <a name="list-builds"></a>Seznam sestavení

Pokud chcete zobrazit seznam spuštění úloh, která služba ACR Tasks dokončila pro váš registr, spusťte příkaz [az acr task list-runs][az-acr-task-list-runs]:

```azurecli-interactive
az acr task list-runs --registry $ACR_NAME --output table
```

Výstup příkazu by měl vypadat podobně jako ten následující. Zobrazí se spuštění provedená službou ACR Tasks a ve sloupci označujícím způsob aktivace se u nejnovější úlohy zobrazí „Git Commit“:

```console
$ az acr task list-runs --registry $ACR_NAME --output table

RUN ID    TASK       PLATFORM    STATUS     TRIGGER    STARTED               DURATION
--------  ---------  ----------  ---------  ---------  --------------------  ----------
cf1d      example1   linux       Succeeded  Commit     2019-05-03T04:16:44Z  00:00:37
cf1c      example1   linux       Succeeded  Commit     2019-05-03T04:16:44Z  00:00:39
cf1b      example1   linux       Succeeded  Manual     2019-05-03T03:10:30Z  00:00:31
cf1a      example1   linux       Succeeded  Commit     2019-05-03T03:09:32Z  00:00:31
cf19      example1   linux       Succeeded  Manual     2019-05-03T03:03:30Z  00:00:21
```

## <a name="create-a-multi-registry-multi-step-task"></a>Vytvořte úlohu vícekrokového více registru

Úlohy služby ACR ve výchozím nastavení má oprávnění k nabízená nebo přetahování imagí z registru, ve kterém je úloha spuštěna. Můžete chtít spustit vícekrokové úlohy, která cílí na jeden nebo více registrů kromě spuštění registru. Například můžete potřebovat k vytvoření imagí v jeden registr a ukládání imagí s využitím různých klíčových slov v druhé registru, ke kterým přistupují produkční systém. Tento příklad ukazuje způsob vytvoření takového úkolu a zadejte přihlašovací údaje pro jiné registru.

Pokud ještě nemáte druhý registru, vytvořte pro účely tohoto příkladu. Pokud potřebujete registr, najdete v článku [předchozí kurz o službě](container-registry-tutorial-quick-task.md), nebo [rychlý start: Vytvoření registru kontejnerů pomocí Azure CLI](container-registry-get-started-azure-cli.md).

Vytvoření úlohy, je třeba název přihlašovacího serveru registru, který je ve formátu *mycontainerregistrydate.azurecr.io* (malými písmeny). V tomto příkladu použijete k ukládání imagí označené datum sestavení druhý registru.

### <a name="yaml-file"></a>Soubor YAML

Druhá úloha vícekrokového příklad pro účely tohoto kurzu je definována v souboru `taskmulti-multiregistry.yaml`, což je v kořenové složce, která jste naklonovali úložiště GitHub:

```yml
version: v1.0.0
steps:
# Build target images
- build: -t {{.Run.Registry}}/hello-world:{{.Run.ID}} -f Dockerfile .
- build: -t {{.Values.regDate}}/hello-world:{{.Run.Date}} -f Dockerfile .
# Run image 
- cmd: -t {{.Run.Registry}}/hello-world:{{.Run.ID}}
  id: test
  detach: true
  ports: ["8080:80"]
- cmd: docker stop test
# Push images
- push:
  - {{.Run.Registry}}/hello-world:{{.Run.ID}}
  - {{.Values.regDate}}/hello-world:{{.Run.Date}}
```

Tato úloha vícekrokového provede následující akce:

1. Spustí dvě `build` postup pro sestavení Image ze souboru Dockerfile v pracovním adresáři:
    * První cílí `Run.Registry`, registru úloha běží kde je označené ACR úlohy ID spuštění. 
    * Druhý, zaměřuje identifikovaný hodnotu registru `regDate`, které jste nastavili při vytváření úkolu (nebo zadejte prostřednictvím externího `values.yaml` souboru předán `az acr task create`). Tento image je označené datum spuštění.
1. Spuštění `cmd` kroku spusťte některý z vytvořených kontejnery. Tento příklad spustí kontejner dlouho běžící na pozadí a vrátí ID kontejneru a pak se zastaví kontejner. Ve skutečném scénáři může otestovat spuštěný kontejner zajistit, že běží správně.
1. V `push` krok, vložení obrázků, které byly vytvořeny, první spuštění registru, druhá k registru identifikovaný `regDate`.

### <a name="task-command"></a>Příkaz úloh

Použití proměnných prostředí shell definovali dříve, vytvořte úkol spuštěním následujícího [az acr úloha vytvoření] [ az-acr-task-create] příkazu. Nahraďte názvem svého registru pro *mycontainerregistrydate*.

```azurecli-interactive
az acr task create \
    --registry $ACR_NAME \
    --name example2 \
    --context https://github.com/$GIT_USER/acr-build-helloworld-node.git \
    --branch master \
    --file taskmulti-image.yaml \
    --git-access-token $GIT_PAT \
    --set regDate=mycontainerregistrydate.azurecr.io
```

### <a name="add-task-credential"></a>Přidat přihlašovací údaj úloh

K imagím nabízených oznámení do registru identifikovaný hodnotu `regDate`, použijte [přidání az acr úloh pověření] [ az-acr-task-credential-add] příkaz pro přidání přihlašovacích údajů pro tento registr úkolu.

V tomto příkladu doporučujeme vytvořit [instanční objekt služby](container-registry-auth-service-principal.md) přístup k registru vymezený *AcrPush* role. Pokud chcete vytvořit instanční objekt služby, najdete v tomto [skript rozhraní příkazového řádku Azure](https://github.com/Azure-Samples/azure-cli-samples/blob/master/container-registry/service-principal-create/service-principal-create.sh).

Předat ID aplikace instančního objektu služby a heslo v následujícím `az acr task credential add` příkaz:

```azurecli-interactive
az acr task credential add --name example2 \
    --registry $ACR_NAME \
    --login-server mycontainerregistrydate.azurecr.io \
    --username <service-principal-application-id> \
    --password <service-principal-password>
```

Rozhraní příkazového řádku vrátí název přihlašovacího serveru registru, které jste přidali.

### <a name="test-the-multi-step-workflow"></a>Testování vícekrokového pracovní postup

Jako v předchozím příkladu, k otestování vícekrokových úkolů, aktivovat ji ručně spuštěním [az acr úlohy] [ az-acr-task-run] příkazu. Pokud chcete aktivovat úlohu s potvrzení změn do úložiště Git, najdete v části [aktivovat sestavení s potvrzení](#trigger-a-build-with-a-commit).

```azurecli-interactive
az acr task run --registry $ACR_NAME --name example2
```

Příkaz `az acr task run` ve výchozím nastavení při spuštění příkazu streamuje výstup protokolu do vaší konzoly. Jako dříve, výstup zobrazuje průběh spuštěním jednotlivých kroků úlohy. Výstup je zhušťovat zobrazíte klíčové kroky

Výstup:

```console
Queued a run with ID: cf1g
Waiting for an agent...
2019/05/03 04:33:39 Downloading source code...
2019/05/03 04:33:41 Finished downloading source code
2019/05/03 04:33:42 Using acb_vol_4569b017-29fe-42bd-83b2-25c45a8ac807 as the home volume
2019/05/03 04:33:42 Creating Docker network: acb_default_network, driver: 'bridge'
2019/05/03 04:33:43 Successfully set up Docker network: acb_default_network
2019/05/03 04:33:43 Setting up Docker configuration...
2019/05/03 04:33:44 Successfully set up Docker configuration
2019/05/03 04:33:44 Logging in to registry: mycontainerregistry.azurecr.io
2019/05/03 04:33:45 Successfully logged into mycontainerregistry.azurecr.io
2019/05/03 04:33:45 Logging in to registry: mycontainerregistrydate.azurecr.io
2019/05/03 04:33:47 Successfully logged into mycontainerregistrydate.azurecr.io
2019/05/03 04:33:47 Executing step ID: acb_step_0. Working directory: '', Network: 'acb_default_network'
2019/05/03 04:33:47 Scanning for dependencies...
2019/05/03 04:33:47 Successfully scanned dependencies
2019/05/03 04:33:47 Launching container with name: acb_step_0
Sending build context to Docker daemon  25.09kB

[...]

Successfully tagged mycontainerregistry.azurecr.io/hello-world:cf1g
2019/05/03 04:33:55 Successfully executed container: acb_step_0
2019/05/03 04:33:55 Executing step ID: acb_step_1. Working directory: '', Network: 'acb_default_network'
2019/05/03 04:33:55 Scanning for dependencies...
2019/05/03 04:33:56 Successfully scanned dependencies
2019/05/03 04:33:56 Launching container with name: acb_step_1
Sending build context to Docker daemon  25.09kB

[...]

Successfully tagged mycontainerregistrydate.azurecr.io/hello-world:20190503-043342z
2019/05/03 04:33:57 Successfully executed container: acb_step_1
2019/05/03 04:33:57 Executing step ID: acb_step_2. Working directory: '', Network: 'acb_default_network'
2019/05/03 04:33:57 Launching container with name: acb_step_2
721437ff674051b6be63cbcd2fa8eb085eacbf38d7d632f1a079320133182101
2019/05/03 04:33:58 Successfully executed container: acb_step_2
2019/05/03 04:33:58 Executing step ID: acb_step_3. Working directory: '', Network: 'acb_default_network'
2019/05/03 04:33:58 Launching container with name: acb_step_3
test
2019/05/03 04:34:09 Successfully executed container: acb_step_3
2019/05/03 04:34:09 Executing step ID: acb_step_4. Working directory: '', Network: 'acb_default_network'
2019/05/03 04:34:09 Pushing image: mycontainerregistry.azurecr.io/hello-world:cf1g, attempt 1
The push refers to repository [mycontainerregistry.azurecr.io/hello-world]

[...]

2019/05/03 04:34:12 Successfully pushed image: mycontainerregistry.azurecr.io/hello-world:cf1g
2019/05/03 04:34:12 Pushing image: mycontainerregistrydate.azurecr.io/hello-world:20190503-043342z, attempt 1
The push refers to repository [mycontainerregistrydate.azurecr.io/hello-world]

[...]

2019/05/03 04:34:19 Successfully pushed image: mycontainerregistrydate.azurecr.io/hello-world:20190503-043342z
2019/05/03 04:34:19 Step ID: acb_step_0 marked as successful (elapsed time in seconds: 8.125744)
2019/05/03 04:34:19 Populating digests for step ID: acb_step_0...
2019/05/03 04:34:21 Successfully populated digests for step ID: acb_step_0
2019/05/03 04:34:21 Step ID: acb_step_1 marked as successful (elapsed time in seconds: 2.009281)
2019/05/03 04:34:21 Populating digests for step ID: acb_step_1...
2019/05/03 04:34:23 Successfully populated digests for step ID: acb_step_1
2019/05/03 04:34:23 Step ID: acb_step_2 marked as successful (elapsed time in seconds: 0.795440)
2019/05/03 04:34:23 Step ID: acb_step_3 marked as successful (elapsed time in seconds: 11.446775)
2019/05/03 04:34:23 Step ID: acb_step_4 marked as successful (elapsed time in seconds: 9.734973)
2019/05/03 04:34:23 The following dependencies were found:
2019/05/03 04:34:23
- image:
    registry: mycontainerregistry.azurecr.io
    repository: hello-world
    tag: cf1g
    digest: sha256:75354e9edb995e8661438bad9913deed87a185fddd0193811f916d684b71a5d2
  runtime-dependency:
    registry: registry.hub.docker.com
    repository: library/node
    tag: 9-alpine
    digest: sha256:8dafc0968fb4d62834d9b826d85a8feecc69bd72cd51723c62c7db67c6dec6fa
  git:
    git-head-revision: 9d9023473c46a5e2c315681b11eb4552ef0faccc
- image:
    registry: mycontainerregistrydate.azurecr.io
    repository: hello-world
    tag: 20190503-043342z
    digest: sha256:75354e9edb995e8661438bad9913deed87a185fddd0193811f916d684b71a5d2
  runtime-dependency:
    registry: registry.hub.docker.com
    repository: library/node
    tag: 9-alpine
    digest: sha256:8dafc0968fb4d62834d9b826d85a8feecc69bd72cd51723c62c7db67c6dec6fa
  git:
    git-head-revision: 9d9023473c46a5e2c315681b11eb4552ef0faccc

Run ID: cf1g was successful after 46s
```

## <a name="next-steps"></a>Další postup

V tomto kurzu jste zjistili, jak k vytvoření vícekrokového, založené na více container úkolů, které automaticky aktivovat, když jste se zavázali zdrojový kód do úložiště Git. Pokročilé funkce vícekrokových úkolů, včetně spuštění paralelní aplikace a závislá kroku najdete v tématu [ACR úlohy YAML odkaz](container-registry-tasks-reference-yaml.md). Přejděte k dalšímu kurzu, ve kterém se naučíte vytvářet úlohy, které aktivují sestavení při aktualizaci základní image kontejneru.

> [!div class="nextstepaction"]
> [Automatizace sestavení při aktualizaci základní image](container-registry-tutorial-base-image-update.md)

<!-- LINKS - External -->
[sample-repo]: https://github.com/Azure-Samples/acr-build-helloworld-node

<!-- LINKS - Internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-task]: /cli/azure/acr/task
[az-acr-task-create]: /cli/azure/acr/task#az-acr-task-create
[az-acr-task-run]: /cli/azure/acr/task#az-acr-task-run
[az-acr-task-list-runs]: /cli/azure/acr/task#az-acr-task-list-runs
[az-acr-task-credential-add]: /cli/azure/acr/task/credential#az-acr-task-credential-add    
[az-login]: /cli/azure/reference-index#az-login

<!-- IMAGES -->
[build-task-01-new-token]: ./media/container-registry-tutorial-build-tasks/build-task-01-new-token.png
[build-task-02-generated-token]: ./media/container-registry-tutorial-build-tasks/build-task-02-generated-token.png
