---
title: Kurz – úloha ACR s více kroky
description: V tomto kurzu se dozvíte, jak nakonfigurovat úlohu registru kontejneru Azure tak, aby automaticky aktivovala vícekrokový pracovní postup pro vytváření, spouštění a nabízení ibi kontejnerů v cloudu, když popotvrzení zdrojového kódu do úložiště Gitu.
ms.topic: tutorial
ms.date: 05/09/2019
ms.custom: seodec18, mvc
ms.openlocfilehash: ff32b3095638af6b2b246b99a5dc9219e0020782
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "78402299"
---
# <a name="tutorial-run-a-multi-step-container-workflow-in-the-cloud-when-you-commit-source-code"></a>Kurz: Spuštění vícekrokového pracovního postupu kontejneru v cloudu při potvrzení zdrojového kódu

Kromě [rychlého úkolu](container-registry-tutorial-quick-task.md)podporuje úlohy ACR vícekrokové pracovní postupy založené na více kontejnerech, které se mohou automaticky aktivovat při potvrzení zdrojového kódu do úložiště Git. 

V tomto kurzu se dozvíte, jak pomocí ukázkových souborů YAML definovat vícekrokové úlohy, které vytvářejí, spouštějí a vytlačují jednu nebo více iložek kontejnerů do registru při potvrzení zdrojového kódu. Chcete-li vytvořit úlohu, která automatizuje pouze jednu bitovou kopii sestavení na potvrzení kódu, [přečtěte si informace o kurzu: Automatizace sestavení image kontejneru v cloudu při potvrzení zdrojového kódu](container-registry-tutorial-build-task.md). Přehled úloh ACR naleznete v [tématu Automatizace operačního systému a opravy architektury s úkoly ACR](container-registry-tasks-overview.md),

V tomto kurzu:

> [!div class="checklist"]
> * Definování vícekrokového úkolu pomocí souboru YAML
> * Vytvoření úkolu
> * Volitelně přidat pověření k úloze povolit přístup k jinému registru
> * Test úlohy
> * Zobrazení stavu úkolů
> * Aktivace úlohy potvrzením kódu

Tento kurz předpokládá, že jste už dokončili kroky z [předchozího kurzu](container-registry-tutorial-quick-task.md). Pokud jste to ještě neudělali, dokončete před pokračováním kroky v části [Požadavky](container-registry-tutorial-quick-task.md#prerequisites) předchozího kurzu.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Pokud chcete používat Azure CLI místně, musíte mít Azure CLI verze **2.0.62** nebo novější nainstalované a přihlášené pomocí [přihlášení az][az-login]. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade rozhraní příkazového řádku (CLI), přečtěte si téma [Instalace Azure CLI][azure-cli].

[!INCLUDE [container-registry-task-tutorial-prereq.md](../../includes/container-registry-task-tutorial-prereq.md)]

## <a name="create-a-multi-step-task"></a>Vytvoření vícekrokového úkolu

Teď, když jste dokončili kroky potřebné k povolení acr úkoly ke čtení stavu potvrzení a vytvoření webhooky v úložišti, vytvořte vícekrokový úkol, který aktivuje vytváření, spuštění a odesílání image kontejneru.

### <a name="yaml-file"></a>Soubor YAML

V [souboru YAML](container-registry-tasks-reference-yaml.md)definujete kroky pro vícekrokovou úlohu . První příklad vícekrokové úlohy pro tento `taskmulti.yaml`kurz je definován v souboru , který je v kořenovém adresáři úložiště GitHub, které jste naklonovali:

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

Tento vícekrokový úkol provádí následující akce:

1. Spustí `build` krok k vytvoření image z Dockerfile v pracovním adresáři. Bitová kopie `Run.Registry`cílí na registr, ve kterém je úloha spuštěna, a je označena jedinečným ID spuštění úloh ACR. 
1. Spustí `cmd` krok ke spuštění bitové kopie v dočasném kontejneru. Tento příklad spustí dlouhotrvající kontejner na pozadí a vrátí ID kontejneru a potom zastaví kontejner. V reálném scénáři můžete zahrnout kroky k testování spuštěného kontejneru, abyste zajistili, že bude správně spuštěn.
1. V `push` kroku odešle bitovou kopii, která byla vytvořena do registru spuštění.

### <a name="task-command"></a>Úkol, příkaz

Nejdřív vyplňte tyto proměnné prostředí hodnotami vhodnými pro vaše prostředí. Tento krok není nezbytně nutný, ale usnadní provádění víceřádkových příkazů Azure CLI v tomto kurzu. Pokud tyto proměnné prostředí nenaplníte, je nutné ručně nahradit každou hodnotu všude tam, kde se zobrazí v ukázkových příkazech.

[![Spuštění vložení](https://shell.azure.com/images/launchcloudshell.png "Spuštění služby Azure Cloud Shell")](https://shell.azure.com)

```console
ACR_NAME=<registry-name>        # The name of your Azure container registry
GIT_USER=<github-username>      # Your GitHub user account name
GIT_PAT=<personal-access-token> # The PAT you generated in the previous section
```

Nyní vytvořte úlohu provedením [následujícího příkazu az acr task create:][az-acr-task-create]

```azurecli-interactive
az acr task create \
    --registry $ACR_NAME \
    --name example1 \
    --context https://github.com/$GIT_USER/acr-build-helloworld-node.git \
    --file taskmulti.yaml \
    --git-access-token $GIT_PAT
```

Tato úloha určuje, že jakýkoli *master* časový kód je potvrzen `--context`a hlavní větev v úložišti určeném aplikacemi ACR Tasks spustí vícekrokovou úlohu z kódu v této větvi. Soubor YAML určený `--file` z kořenového adresáře úložiště definuje kroky. 

Výstup úspěšného příkazu [az acr task create][az-acr-task-create] je podobný následujícímu:

```output
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

## <a name="test-the-multi-step-workflow"></a>Testování vícekrokového pracovního postupu

Chcete-li otestovat úlohu více kroků, spusťte ji ručně spuštěním příkazu [az acr task run:][az-acr-task-run]

```azurecli-interactive
az acr task run --registry $ACR_NAME --name example1
```

Příkaz `az acr task run` ve výchozím nastavení při spuštění příkazu streamuje výstup protokolu do vaší konzoly. Výstup ukazuje průběh spuštění každého z kroků úlohy. Níže uvedený výstup je zhuštěn, aby zobrazoval klíčové kroky.

```output
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

```console
cd acr-build-helloworld-node
```

Potom spuštěním následujících příkazů vytvořte, potvrďte a zapište nový soubor do forku úložiště na GitHubu:

```console
echo "Hello World!" > hello.txt
git add hello.txt
git commit -m "Testing ACR Tasks"
git push origin master
```

Při spuštění příkazu `git push` můžete být vyzváni k zadání přihlašovacích údajů pro GitHub. Zadejte svoje uživatelské jméno pro GitHub a zadejte token PAT, který jste dříve vytvořili pro heslo.

```azurecli-interactive
Username for 'https://github.com': <github-username>
Password for 'https://githubuser@github.com': <personal-access-token>
```

Po zasunutí potvrzení do úložiště, webhook uvytvoření ACR úkoly požáry a spustí úlohu v registru kontejnerů Azure. Zobrazte protokoly pro aktuálně spuštěnou úlohu, abyste mohli ověřit a monitorovat průběh sestavení:

```azurecli-interactive
az acr task logs --registry $ACR_NAME
```

Výstup je podobný následujícímu a zobrazuje aktuálně spuštěnou (nebo naposledy spuštěnou) úlohu:

```output
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

```output
RUN ID    TASK       PLATFORM    STATUS     TRIGGER    STARTED               DURATION
--------  ---------  ----------  ---------  ---------  --------------------  ----------
cf1d      example1   linux       Succeeded  Commit     2019-05-03T04:16:44Z  00:00:37
cf1c      example1   linux       Succeeded  Commit     2019-05-03T04:16:44Z  00:00:39
cf1b      example1   linux       Succeeded  Manual     2019-05-03T03:10:30Z  00:00:31
cf1a      example1   linux       Succeeded  Commit     2019-05-03T03:09:32Z  00:00:31
cf19      example1   linux       Succeeded  Manual     2019-05-03T03:03:30Z  00:00:21
```

## <a name="create-a-multi-registry-multi-step-task"></a>Vytvoření vícekrokového úkolu ve více registru

ACR Úkoly ve výchozím nastavení má oprávnění k nabízení nebo vytahování bitových kopií z registru, kde je úloha spuštěna. Můžete chtít spustit vícekrokovou úlohu, která se zaměřuje na jeden nebo více registrů kromě registru spuštění. Například může být nutné vytvořit bitové kopie v jednom registru a ukládat bitové kopie s různými značkami v druhém registru, ke kterému má produkční systém přístup. Tento příklad ukazuje, jak vytvořit takový úkol a zadat pověření pro jiný registr.

Pokud ještě nemáte druhý registr, vytvořte jeden pro tento příklad. Pokud potřebujete registr, podívejte se na [předchozí kurz](container-registry-tutorial-quick-task.md) nebo článek [Rychlý start: Vytvoření registru kontejnerů pomocí Azure CLI](container-registry-get-started-azure-cli.md).

Chcete-li vytvořit úlohu, potřebujete název přihlašovacího serveru registru, který je formuláře *mycontainerregistrydate.azurecr.io* (všechna malá písmena). V tomto příkladu použijete druhý registr k ukládání bitových kopií označených datem sestavení.

### <a name="yaml-file"></a>Soubor YAML

Druhý příklad vícekrokový úkol pro tento kurz `taskmulti-multiregistry.yaml`je definován v souboru , který je v kořenovém adresáři úložiště GitHub, které jste naklonovali:

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

Tento vícekrokový úkol provádí následující akce:

1. Spustí `build` dva kroky k vytvoření imitek z Dockerfile v pracovním adresáři:
    * První cílí `Run.Registry`na registr, ve kterém je úloha spuštěna, a je označen id spuštění úloh ACR. 
    * Druhý cílí na registr identifikovaný `regDate`hodnotou , kterou nastavíte při vytváření `values.yaml` úkolu `az acr task create`(nebo poskytujete prostřednictvím externího souboru předaného ). Tento obrázek je označen datem spuštění.
1. Spustí `cmd` krok ke spuštění jednoho z vytvořených kontejnerů. Tento příklad spustí dlouhotrvající kontejner na pozadí a vrátí ID kontejneru a potom zastaví kontejner. V reálném scénáři můžete otestovat spuštěný kontejner, abyste zajistili, že bude správně spuštěn.
1. V `push` kroku odešle bitové kopie, které byly vytvořeny, první do registru `regDate`spuštění, druhý do registru identifikované .

### <a name="task-command"></a>Úkol, příkaz

Pomocí dříve definovaných proměnných prostředí prostředí vytvořte úlohu provedením následujícího příkazu [az acr task create.][az-acr-task-create] Nahraďte název registru *pro mycontainerregistrydate*.

```azurecli-interactive
az acr task create \
    --registry $ACR_NAME \
    --name example2 \
    --context https://github.com/$GIT_USER/acr-build-helloworld-node.git \
    --file taskmulti-multiregistry.yaml \
    --git-access-token $GIT_PAT \
    --set regDate=mycontainerregistrydate.azurecr.io
```

### <a name="add-task-credential"></a>Přidání pověření úkolu

Chcete-li vysunout bitové `regDate`kopie do registru identifikovaného hodnotou aplikace , použijte příkaz add přiřazení úlohy [az acr][az-acr-task-credential-add] k přidání přihlašovacích pověření pro daný registr k úkolu.

V tomto příkladu doporučujeme vytvořit [instanční objekt](container-registry-auth-service-principal.md) s přístupem k registru vymezeného na roli *AcrPush.* Pokud chcete vytvořit instanční objekt, přečtěte si tento [skript Azure CLI](https://github.com/Azure-Samples/azure-cli-samples/blob/master/container-registry/service-principal-create/service-principal-create.sh).

Předaji ID a heslo `az acr task credential add` instančního objektu v následujícím příkazu:

```azurecli-interactive
az acr task credential add --name example2 \
    --registry $ACR_NAME \
    --login-server mycontainerregistrydate.azurecr.io \
    --username <service-principal-application-id> \
    --password <service-principal-password>
```

Zaokreslovací systém vrátí název přihlašovacího serveru registru, který jste přidali.

### <a name="test-the-multi-step-workflow"></a>Testování vícekrokového pracovního postupu

Stejně jako v předchozím příkladu, chcete-li otestovat vícekrokovou úlohu, aktivujte ji ručně spuštěním příkazu [az acr task run.][az-acr-task-run] Pokud chcete úkol aktivovat s potvrzením úložiště Git, přečtěte si část [Aktivace sestavení s potvrzením](#trigger-a-build-with-a-commit).

```azurecli-interactive
az acr task run --registry $ACR_NAME --name example2
```

Příkaz `az acr task run` ve výchozím nastavení při spuštění příkazu streamuje výstup protokolu do vaší konzoly. Stejně jako dříve výstup ukazuje průběh spuštění každého z kroků úlohy. Výstup je zhuštěn tak, aby zobrazoval klíčové kroky.

Výstup:

```output
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

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili, jak vytvořit vícekrokové úlohy založené na více kontejnerech, které se automaticky aktivují při potvrzení zdrojového kódu do úložiště Git. Pokročilé funkce vícekrokových úloh, včetně paralelního a závislého provádění kroků, naleznete v [odkazu YAML na úlohy ACR](container-registry-tasks-reference-yaml.md). Přejděte k dalšímu kurzu, ve kterém se naučíte vytvářet úlohy, které aktivují sestavení při aktualizaci základní image kontejneru.

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
