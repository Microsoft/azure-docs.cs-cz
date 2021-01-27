---
title: Kurz – úloha ACR pro více kroků
description: V tomto kurzu se naučíte konfigurovat úlohu Azure Container Registry, která automaticky aktivuje pracovní postup s více kroky pro vytváření, spouštění a vkládání imagí kontejnerů v cloudu při potvrzení zdrojového kódu do úložiště Git.
ms.topic: tutorial
ms.date: 11/24/2020
ms.custom: seodec18, mvc, devx-track-azurecli
ms.openlocfilehash: be5c8c4012267dc7ce6362502c806a9f238732b7
ms.sourcegitcommit: 436518116963bd7e81e0217e246c80a9808dc88c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/27/2021
ms.locfileid: "98920270"
---
# <a name="tutorial-run-a-multi-step-container-workflow-in-the-cloud-when-you-commit-source-code"></a>Kurz: spuštění víceúrovňového pracovního postupu kontejneru v cloudu při potvrzení zdrojového kódu

Kromě [Rychlé úlohy](container-registry-tutorial-quick-task.md)ACR úlohy podporují více než více kontejnerů, které se můžou automaticky aktivovat při potvrzení zdrojového kódu do úložiště Git. 

V tomto kurzu se naučíte, jak pomocí ukázkových souborů YAML definovat úlohy s více kroky, které sestavují, spouštějí a nadávají do registru jednu nebo více imagí kontejneru při potvrzení zdrojového kódu. Chcete-li vytvořit úkol, který automatizuje pouze jedno sestavení image při potvrzení kódu, přečtěte si [kurz: automatizace sestavení imagí kontejneru v cloudu při potvrzení zdrojového kódu](container-registry-tutorial-build-task.md). Přehled úloh ACR najdete v tématu [Automatizace oprav operačního systému a architektury s úlohami ACR](container-registry-tasks-overview.md),

V tomto kurzu:

> [!div class="checklist"]
> * Definování úlohy s více kroky pomocí souboru YAML
> * Vytvoření úkolu
> * Volitelně přidejte do úlohy přihlašovací údaje, abyste mohli povolit přístup k jinému registru.
> * Test úlohy
> * Zobrazení stavu úkolů
> * Aktivace úlohy potvrzením kódu

Tento kurz předpokládá, že jste už dokončili kroky z [předchozího kurzu](container-registry-tutorial-quick-task.md). Pokud jste to ještě neudělali, dokončete před pokračováním kroky v části [Požadavky](container-registry-tutorial-quick-task.md#prerequisites) předchozího kurzu.

[!INCLUDE [container-registry-task-tutorial-prereq.md](../../includes/container-registry-task-tutorial-prereq.md)]
[!INCLUDE [azure-cli-prepare-your-environment-h3.md](../../includes/azure-cli-prepare-your-environment-h3.md)]

## <a name="create-a-multi-step-task"></a>Vytvoření úlohy s více kroky

Teď, když jste dokončili kroky potřebné k tomu, aby ACR úlohy mohly číst stav potvrzení a vytvořit Webhooky v úložišti, vytvořte úlohu s více kroky, která spustí sestavování, spuštění a vložení image kontejneru.

### <a name="yaml-file"></a>Soubor YAML

Nadefinujete kroky pro úlohu s více kroky v [souboru YAML](container-registry-tasks-reference-yaml.md). První příklad úlohy s více kroky pro tento kurz je definován v souboru `taskmulti.yaml` , který je v kořenovém adresáři úložiště GitHub, který jste naklonováni:

```yml
version: v1.1.0
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

Tato úloha s více kroky provede následující akce:

1. Spustí `build` krok pro sestavení image z souboru Dockerfile v pracovním adresáři. Image cílí na `Run.Registry` , registr, ve kterém je úloha spuštěná, a je označený jedinečným ID spuštění ACR úloh. 
1. Spustí `cmd` krok pro spuštění image v dočasném kontejneru. Tento příklad spustí dlouhodobě běžící kontejner na pozadí a vrátí ID kontejneru a potom zastaví kontejner. V reálných scénářích můžete zahrnout kroky pro otestování spuštěného kontejneru, aby se zajistilo jeho správné spuštění.
1. V `push` kroku se nahraje image, která se vytvořila v registru spuštění.

### <a name="task-command"></a>Příkaz úkolu

Nejdřív vyplňte tyto proměnné prostředí hodnotami vhodnými pro vaše prostředí. Tento krok není nezbytně nutný, ale usnadní provádění víceřádkových příkazů Azure CLI v tomto kurzu. Pokud tyto proměnné prostředí neplníte, je nutné ručně nahradit každou hodnotu, pokud se zobrazí v ukázkových příkazech.

```console
ACR_NAME=<registry-name>        # The name of your Azure container registry
GIT_USER=<github-username>      # Your GitHub user account name
GIT_PAT=<personal-access-token> # The PAT you generated in the previous section
```

Teď úlohu vytvořte spuštěním následujícího příkazu [AZ ACR Task Create][az-acr-task-create] :

```azurecli-interactive
az acr task create \
    --registry $ACR_NAME \
    --name example1 \
    --context https://github.com/$GIT_USER/acr-build-helloworld-node.git#main \
    --file taskmulti.yaml \
    --git-access-token $GIT_PAT
```

Tato úloha určuje, že jakýkoliv kód času je potvrzen do *Hlavní* větve v úložišti určeném nástrojem `--context` , úlohy ACR spustí úlohu s více kroky z kódu v této větvi. `--file`Tento postup definuje soubor YAML určený z kořenového adresáře úložiště. 

Výstup úspěšného příkazu [az acr task create][az-acr-task-create] je podobný následujícímu:

```output
{
  "agentConfiguration": {
    "cpu": 2
  },
  "creationDate": "2020-11-20T03:14:31.763887+00:00",
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
    "contextPath": "https://github.com/gituser/acr-build-helloworld-node.git#main",
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
          "branch": "main",
          "repositoryUrl": "https://github.com/gituser/acr-build-helloworld-node.git#main",
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

## <a name="test-the-multi-step-workflow"></a>Testování pracovního postupu s více kroky

Chcete-li otestovat úlohu s více kroky, aktivujte ji ručně spuštěním příkazu [AZ ACR Task Run][az-acr-task-run] :

```azurecli-interactive
az acr task run --registry $ACR_NAME --name example1
```

Příkaz `az acr task run` ve výchozím nastavení při spuštění příkazu streamuje výstup protokolu do vaší konzoly. Výstup zobrazuje průběh spuštění jednotlivých kroků úkolu. Výstup níže je zúžený, aby se zobrazily klíčové kroky.

```output
Queued a run with ID: cab
Waiting for an agent...
2020/11/20 00:03:31 Downloading source code...
2020/11/20 00:03:33 Finished downloading source code
2020/11/20 00:03:33 Using acb_vol_cfe6bd55-3076-4215-8091-6a81aec3d1b1 as the home volume
2020/11/20 00:03:33 Creating Docker network: acb_default_network, driver: 'bridge'
2020/11/20 00:03:34 Successfully set up Docker network: acb_default_network
2020/11/20 00:03:34 Setting up Docker configuration...
2020/11/20 00:03:34 Successfully set up Docker configuration
2020/11/20 00:03:34 Logging in to registry: myregistry.azurecr.io
2020/11/20 00:03:35 Successfully logged into myregistry.azurecr.io
2020/11/20 00:03:35 Executing step ID: acb_step_0. Working directory: '', Network: 'acb_default_network'
2020/11/20 00:03:35 Scanning for dependencies...
2020/11/20 00:03:36 Successfully scanned dependencies
2020/11/20 00:03:36 Launching container with name: acb_step_0
Sending build context to Docker daemon  24.06kB
[...]
Successfully built f669bfd170af
Successfully tagged myregistry.azurecr.io/hello-world:cf19
2020/11/20 00:03:43 Successfully executed container: acb_step_0
2020/11/20 00:03:43 Executing step ID: acb_step_1. Working directory: '', Network: 'acb_default_network'
2020/11/20 00:03:43 Launching container with name: acb_step_1
279b1cb6e092b64c8517c5506fcb45494cd5a0bd10a6beca3ba97f25c5d940cd
2020/11/20 00:03:44 Successfully executed container: acb_step_1
2020/11/20 00:03:44 Executing step ID: acb_step_2. Working directory: '', Network: 'acb_default_network'
2020/11/20 00:03:44 Pushing image: myregistry.azurecr.io/hello-world:cf19, attempt 1
[...]
2020/11/20 00:03:46 Successfully pushed image: myregistry.azurecr.io/hello-world:cf19
2020/11/20 00:03:46 Step ID: acb_step_0 marked as successful (elapsed time in seconds: 7.425169)
2020/11/20 00:03:46 Populating digests for step ID: acb_step_0...
2020/11/20 00:03:47 Successfully populated digests for step ID: acb_step_0
2020/11/20 00:03:47 Step ID: acb_step_1 marked as successful (elapsed time in seconds: 0.827129)
2020/11/20 00:03:47 Step ID: acb_step_2 marked as successful (elapsed time in seconds: 2.112113)
2020/11/20 00:03:47 The following dependencies were found:
2020/11/20 00:03:47
- image:
    registry: myregistry.azurecr.io
    repository: hello-world
    tag: cf19
    digest: sha256:6b981a8ca8596e840228c974c929db05c0727d8630465de536be74104693467a
  runtime-dependency:
    registry: registry.hub.docker.com
    repository: library/node
    tag: 15-alpine
    digest: sha256:8dafc0968fb4d62834d9b826d85a8feecc69bd72cd51723c62c7db67c6dec6fa
  git:
    git-head-revision: 1a3065388a0238e52865db1c8f3e97492a43444c

Run ID: cab was successful after 18s
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
git push origin main
```

Při spuštění příkazu `git push` můžete být vyzváni k zadání přihlašovacích údajů pro GitHub. Zadejte svoje uživatelské jméno pro GitHub a zadejte token PAT, který jste dříve vytvořili pro heslo.

```azurecli-interactive
Username for 'https://github.com': <github-username>
Password for 'https://githubuser@github.com': <personal-access-token>
```

Po vložení potvrzení do úložiště se Webhook vytvořený pomocí úloh ACR aktivuje a vypíná od něj úlohu v Azure Container Registry. Zobrazte protokoly pro aktuálně spuštěnou úlohu, abyste mohli ověřit a monitorovat průběh sestavení:

```azurecli-interactive
az acr task logs --registry $ACR_NAME
```

Výstup je podobný následujícímu a zobrazuje aktuálně spuštěnou (nebo naposledy spuštěnou) úlohu:

```output
Showing logs of the last created run.
Run ID: cad

[...]

Run ID: cad was successful after 37s
```

## <a name="list-builds"></a>Seznam sestavení

Pokud chcete zobrazit seznam spuštění úloh, která služba ACR Tasks dokončila pro váš registr, spusťte příkaz [az acr task list-runs][az-acr-task-list-runs]:

```azurecli-interactive
az acr task list-runs --registry $ACR_NAME --output table
```

Výstup příkazu by měl vypadat podobně jako ten následující. Zobrazí se spuštění provedená službou ACR Tasks a ve sloupci označujícím způsob aktivace se u nejnovější úlohy zobrazí „Git Commit“:

```output
RUN ID    TASK            PLATFORM    STATUS     TRIGGER    STARTED               DURATION
--------  --------------  ----------  ---------  ---------  --------------------  ----------
cad       example1        linux       Succeeded  Commit     2020-11-20T00:22:15Z  00:00:35
cac       taskhelloworld  linux       Succeeded  Commit     2020-11-20T00:22:15Z  00:00:22
cab       example1        linux       Succeeded  Manual     2020-11-20T00:18:36Z  00:00:47
```

## <a name="create-a-multi-registry-multi-step-task"></a>Vytvoření úlohy s více kroky pro více registrů

ACR úlohy ve výchozím nastavení mají oprávnění k odesílání nebo vyžádání imagí z registru, ve kterém je úloha spuštěná. Je možné, že budete chtít spustit úlohu s více kroky, která cílí na jeden nebo více registrů kromě registru Run. Například může být potřeba sestavit image v jednom registru a ukládat image s různými značkami v druhém registru, ke kterému se přistupovalo z produkčního systému. V tomto příkladu se dozvíte, jak vytvořit takovou úlohu a zadat přihlašovací údaje pro jiný registr.

Pokud ještě nemáte druhý registr, vytvořte ho pro tento příklad. Pokud potřebujete registr, podívejte se na [předchozí kurz](container-registry-tutorial-quick-task.md) nebo článek [Rychlý start: Vytvoření registru kontejnerů pomocí Azure CLI](container-registry-get-started-azure-cli.md).

Chcete-li vytvořit úlohu, budete potřebovat název přihlašovacího serveru registru, který má formu *mycontainerregistrydate.azurecr.IO* (všechna malá písmena). V tomto příkladu použijete druhý registr k ukládání imagí označených datem sestavení.

### <a name="yaml-file"></a>Soubor YAML

Druhý příklad úlohy s více kroky pro tento kurz je definován v souboru `taskmulti-multiregistry.yaml` , který je v kořenovém adresáři úložiště GitHub, který jste naklonoval:

```yml
version: v1.1.0
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

Tato úloha s více kroky provede následující akce:

1. Spustí dva `build` kroky pro sestavení imagí z souboru Dockerfile v pracovním adresáři:
    * První cílí na `Run.Registry` , registr, ve kterém je úloha spuštěná, a je označený ID běhu ACR úloh. 
    * Druhý cílí na registr identifikovaný hodnotou `regDate` , kterou jste nastavili při vytváření úlohy (nebo zadání prostřednictvím `values.yaml` předaného externího souboru `az acr task create` ). Tento obrázek je označený jako datum spuštění.
1. Spustí `cmd` krok pro spuštění jednoho ze sestavených kontejnerů. Tento příklad spustí dlouhodobě běžící kontejner na pozadí a vrátí ID kontejneru a potom zastaví kontejner. Ve scénáři reálného světa můžete otestovat spuštěný kontejner, aby se zajistilo jeho správné spuštění.
1. V kroku jsou vloženy `push` obrázky, které byly vytvořeny, první do registru spuštění, druhý k registru, který identifikuje `regDate` .

### <a name="task-command"></a>Příkaz úkolu

Pomocí dříve definovaných proměnných prostředí prostředí vytvořte úlohu provedením následujícího příkazu [AZ ACR Task Create][az-acr-task-create] . Nahraďte název vašeho registru pro *mycontainerregistrydate*.

```azurecli-interactive
az acr task create \
    --registry $ACR_NAME \
    --name example2 \
    --context https://github.com/$GIT_USER/acr-build-helloworld-node.git#main \
    --file taskmulti-multiregistry.yaml \
    --git-access-token $GIT_PAT \
    --set regDate=mycontainerregistrydate.azurecr.io
```

### <a name="add-task-credential"></a>Přidat pověření úkolu

Pro vložení imagí do registru identifikovaného hodnotou `regDate` pomocí příkazu [AZ ACR Task Credential Add][az-acr-task-credential-add] přidejte přihlašovací údaje pro tento registr do úlohy.

V tomto příkladu doporučujeme vytvořit [instanční objekt](container-registry-auth-service-principal.md) s přístupem k registru vymezenému na roli *AcrPush* , aby měl oprávnění k odesílání obrázků. Pokud chcete vytvořit instanční objekt, přečtěte si tento [skript Azure CLI](https://github.com/Azure-Samples/azure-cli-samples/blob/master/container-registry/service-principal-create/service-principal-create.sh).

V následujícím příkazu předejte ID aplikace a heslo objektu služby `az acr task credential add` . Nezapomeňte aktualizovat název přihlašovacího serveru *mycontainerregistrydate* názvem vašeho druhého registru:

```azurecli-interactive
az acr task credential add --name example2 \
    --registry $ACR_NAME \
    --login-server mycontainerregistrydate.azurecr.io \
    --username <service-principal-application-id> \
    --password <service-principal-password>
```

Rozhraní příkazového řádku vrátí název přidaného přihlašovacího serveru registru.

### <a name="test-the-multi-step-workflow"></a>Testování pracovního postupu s více kroky

Jak je uvedeno v předchozím příkladu, chcete-li otestovat úlohu s více kroky, aktivujte ji ručně spuštěním příkazu [AZ ACR Task Run][az-acr-task-run] . Chcete-li aktivovat úlohu s potvrzením změn do úložiště Git, přečtěte si část [Aktivace sestavení s potvrzením](#trigger-a-build-with-a-commit).

```azurecli-interactive
az acr task run --registry $ACR_NAME --name example2
```

Příkaz `az acr task run` ve výchozím nastavení při spuštění příkazu streamuje výstup protokolu do vaší konzoly. Stejně jako dřív výstup zobrazuje průběh spuštění jednotlivých kroků úkolu. Výstup je zúžený, aby se zobrazily klíčové kroky.

Výstup:

```output
Queued a run with ID: cf1g
Waiting for an agent...
2020/11/20 04:33:39 Downloading source code...
2020/11/20 04:33:41 Finished downloading source code
2020/11/20 04:33:42 Using acb_vol_4569b017-29fe-42bd-83b2-25c45a8ac807 as the home volume
2020/11/20 04:33:42 Creating Docker network: acb_default_network, driver: 'bridge'
2020/11/20 04:33:43 Successfully set up Docker network: acb_default_network
2020/11/20 04:33:43 Setting up Docker configuration...
2020/11/20 04:33:44 Successfully set up Docker configuration
2020/11/20 04:33:44 Logging in to registry: mycontainerregistry.azurecr.io
2020/11/20 04:33:45 Successfully logged into mycontainerregistry.azurecr.io
2020/11/20 04:33:45 Logging in to registry: mycontainerregistrydate.azurecr.io
2020/11/20 04:33:47 Successfully logged into mycontainerregistrydate.azurecr.io
2020/11/20 04:33:47 Executing step ID: acb_step_0. Working directory: '', Network: 'acb_default_network'
2020/11/20 04:33:47 Scanning for dependencies...
2020/11/20 04:33:47 Successfully scanned dependencies
2020/11/20 04:33:47 Launching container with name: acb_step_0
Sending build context to Docker daemon  25.09kB
[...]
Successfully tagged mycontainerregistry.azurecr.io/hello-world:cf1g
2020/11/20 04:33:55 Successfully executed container: acb_step_0
2020/11/20 04:33:55 Executing step ID: acb_step_1. Working directory: '', Network: 'acb_default_network'
2020/11/20 04:33:55 Scanning for dependencies...
2020/11/20 04:33:56 Successfully scanned dependencies
2020/11/20 04:33:56 Launching container with name: acb_step_1
Sending build context to Docker daemon  25.09kB
[...]
Successfully tagged mycontainerregistrydate.azurecr.io/hello-world:20190503-043342z
2020/11/20 04:33:57 Successfully executed container: acb_step_1
2020/11/20 04:33:57 Executing step ID: acb_step_2. Working directory: '', Network: 'acb_default_network'
2020/11/20 04:33:57 Launching container with name: acb_step_2
721437ff674051b6be63cbcd2fa8eb085eacbf38d7d632f1a079320133182101
2020/11/20 04:33:58 Successfully executed container: acb_step_2
2020/11/20 04:33:58 Executing step ID: acb_step_3. Working directory: '', Network: 'acb_default_network'
2020/11/20 04:33:58 Launching container with name: acb_step_3
test
2020/11/20 04:34:09 Successfully executed container: acb_step_3
2020/11/20 04:34:09 Executing step ID: acb_step_4. Working directory: '', Network: 'acb_default_network'
2020/11/20 04:34:09 Pushing image: mycontainerregistry.azurecr.io/hello-world:cf1g, attempt 1
The push refers to repository [mycontainerregistry.azurecr.io/hello-world]
[...]
2020/11/20 04:34:12 Successfully pushed image: mycontainerregistry.azurecr.io/hello-world:cf1g
2020/11/20 04:34:12 Pushing image: mycontainerregistrydate.azurecr.io/hello-world:20190503-043342z, attempt 1
The push refers to repository [mycontainerregistrydate.azurecr.io/hello-world]
[...]
2020/11/20 04:34:19 Successfully pushed image: mycontainerregistrydate.azurecr.io/hello-world:20190503-043342z
2020/11/20 04:34:19 Step ID: acb_step_0 marked as successful (elapsed time in seconds: 8.125744)
2020/11/20 04:34:19 Populating digests for step ID: acb_step_0...
2020/11/20 04:34:21 Successfully populated digests for step ID: acb_step_0
2020/11/20 04:34:21 Step ID: acb_step_1 marked as successful (elapsed time in seconds: 2.009281)
2020/11/20 04:34:21 Populating digests for step ID: acb_step_1...
2020/11/20 04:34:23 Successfully populated digests for step ID: acb_step_1
2020/11/20 04:34:23 Step ID: acb_step_2 marked as successful (elapsed time in seconds: 0.795440)
2020/11/20 04:34:23 Step ID: acb_step_3 marked as successful (elapsed time in seconds: 11.446775)
2020/11/20 04:34:23 Step ID: acb_step_4 marked as successful (elapsed time in seconds: 9.734973)
2020/11/20 04:34:23 The following dependencies were found:
2020/11/20 04:34:23
- image:
    registry: mycontainerregistry.azurecr.io
    repository: hello-world
    tag: cf1g
    digest: sha256:75354e9edb995e8661438bad9913deed87a185fddd0193811f916d684b71a5d2
  runtime-dependency:
    registry: registry.hub.docker.com
    repository: library/node
    tag: 15-alpine
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
    tag: 15-alpine
    digest: sha256:8dafc0968fb4d62834d9b826d85a8feecc69bd72cd51723c62c7db67c6dec6fa
  git:
    git-head-revision: 9d9023473c46a5e2c315681b11eb4552ef0faccc

Run ID: cf1g was successful after 46s
```

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste zjistili, jak vytvořit vícenásobné úlohy založené na více kontejnerech, které se automaticky aktivují při potvrzení zdrojového kódu do úložiště Git. Pokročilé funkce pro úlohy s více kroky, včetně spuštění paralelního a závislého kroku, najdete v tématu [ACR Tasks YAML reference](container-registry-tasks-reference-yaml.md). Přejděte k dalšímu kurzu, ve kterém se naučíte vytvářet úlohy, které aktivují sestavení při aktualizaci základní image kontejneru.

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
