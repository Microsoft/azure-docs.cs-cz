---
title: Tutorial - Multi-step ACR task
description: In this tutorial, you learn how to configure an Azure Container Registry Task to automatically trigger a multi-step workflow to build, run, and push container images in the cloud when you commit source code to a Git repository.
ms.topic: tutorial
ms.date: 05/09/2019
ms.custom: seodec18, mvc
ms.openlocfilehash: f5342e51af870b1e5f8651ea2d28894233ed8e62
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/24/2019
ms.locfileid: "74456068"
---
# <a name="tutorial-run-a-multi-step-container-workflow-in-the-cloud-when-you-commit-source-code"></a>Tutorial: Run a multi-step container workflow in the cloud when you commit source code

In addition to a [quick task](container-registry-tutorial-quick-task.md), ACR Tasks supports multi-step, multi-container-based workflows that can automatically trigger when you commit source code to a Git repository. 

In this tutorial, you learn how to use example YAML files to define multi-step tasks that build, run, and push one or more container images to a registry when you commit source code. To create a task that only automates a single image build on code commit, see [Tutorial: Automate container image builds in the cloud when you commit source code](container-registry-tutorial-build-task.md). For an overview of ACR Tasks, see [Automate OS and framework patching with ACR Tasks](container-registry-tasks-overview.md),

In this tutorial:

> [!div class="checklist"]
> * Define a multi-step task using a YAML file
> * Vytvoření úkolu
> * Optionally add credentials to the task to enable access to another registry
> * Test úlohy
> * Zobrazení stavu úkolů
> * Aktivace úlohy potvrzením kódu

Tento kurz předpokládá, že jste už dokončili kroky z [předchozího kurzu](container-registry-tutorial-quick-task.md). Pokud jste to ještě neudělali, dokončete před pokračováním kroky v části [Požadavky](container-registry-tutorial-quick-task.md#prerequisites) předchozího kurzu.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

If you'd like to use the Azure CLI locally, you must have Azure CLI version **2.0.62** or later installed and logged in with [az login][az-login]. Verzi zjistíte spuštěním příkazu `az --version`. If you need to install or upgrade the CLI, see [Install Azure CLI][azure-cli].

[!INCLUDE [container-registry-task-tutorial-prereq.md](../../includes/container-registry-task-tutorial-prereq.md)]

## <a name="create-a-multi-step-task"></a>Create a multi-step task

Now that you've completed the steps required to enable ACR Tasks to read commit status and create webhooks in a repository, create a multi-step task that triggers building, running, and pushing a container image.

### <a name="yaml-file"></a>Soubor YAML

You define the steps for a multi-step task in a [YAML file](container-registry-tasks-reference-yaml.md). The first example multi-step task for this tutorial is defined in the file `taskmulti.yaml`, which is in the root of the GitHub repo that you cloned:

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

This multi-step task does the following:

1. Runs a `build` step to build an image from the Dockerfile in the working directory. The image targets the `Run.Registry`, the registry where the task is run, and is tagged with a unique ACR Tasks run ID. 
1. Runs a `cmd` step to run the image in a temporary container. This example starts a long-running container in the background and returns the container ID, then stops the container. In a real-world scenario, you might include steps to test the running container to ensure it runs correctly.
1. In a `push` step, pushes the image that was built to the run registry.

### <a name="task-command"></a>Task command

Nejdřív vyplňte tyto proměnné prostředí hodnotami vhodnými pro vaše prostředí. Tento krok není nezbytně nutný, ale usnadní provádění víceřádkových příkazů Azure CLI v tomto kurzu. If you don't populate these environment variables, you must manually replace each value wherever it appears in the example commands.

```azurecli-interactive
ACR_NAME=<registry-name>        # The name of your Azure container registry
GIT_USER=<github-username>      # Your GitHub user account name
GIT_PAT=<personal-access-token> # The PAT you generated in the previous section
```

Now, create the task by executing the following [az acr task create][az-acr-task-create] command:

```azurecli-interactive
az acr task create \
    --registry $ACR_NAME \
    --name example1 \
    --context https://github.com/$GIT_USER/acr-build-helloworld-node.git \
    --file taskmulti.yaml \
    --git-access-token $GIT_PAT
```

This task specifies that any time code is committed to the *master* branch in the repository specified by `--context`, ACR Tasks will run the multi-step task from the code in that branch. The YAML file specified by `--file` from the repository root defines the steps. 

Output from a successful [az acr task create][az-acr-task-create] command is similar to the following:

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

## <a name="test-the-multi-step-workflow"></a>Test the multi-step workflow

To test the multi-step task, trigger it manually by executing the [az acr task run][az-acr-task-run] command:

```azurecli-interactive
az acr task run --registry $ACR_NAME --name example1
```

Příkaz `az acr task run` ve výchozím nastavení při spuštění příkazu streamuje výstup protokolu do vaší konzoly. The output shows the progress of running each of the task steps. The output below is condensed to show key steps.

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

First, ensure you're in the directory containing your local clone of the [repository][sample-repo]:

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

Once you've pushed a commit to your repository, the webhook created by ACR Tasks fires and kicks off the task in Azure Container Registry. Zobrazte protokoly pro aktuálně spuštěnou úlohu, abyste mohli ověřit a monitorovat průběh sestavení:

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

To see a list of the task runs that ACR Tasks has completed for your registry, run the [az acr task list-runs][az-acr-task-list-runs] command:

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

## <a name="create-a-multi-registry-multi-step-task"></a>Create a multi-registry multi-step task

ACR Tasks by default has permissions to push or pull images from the registry where the task runs. You might want to run a multi-step task that targets one or more registries in addition to the run registry. For example, you might need to build images in one registry, and store images with different tags in a second registry that is accessed by a production system. This example shows you how to create such a task and provide credentials for another registry.

If you don't already have a second registry, create one for this example. Pokud potřebujete registr, podívejte se na [předchozí kurz](container-registry-tutorial-quick-task.md) nebo článek [Rychlý start: Vytvoření registru kontejnerů pomocí Azure CLI](container-registry-get-started-azure-cli.md).

To create the task, you need the name of the registry login server, which is of the form *mycontainerregistrydate.azurecr.io* (all lowercase). In this example, you use the second registry to store images tagged by build date.

### <a name="yaml-file"></a>Soubor YAML

The second example multi-step task for this tutorial is defined in the file `taskmulti-multiregistry.yaml`, which is in the root of the GitHub repo that you cloned:

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

This multi-step task does the following:

1. Runs two `build` steps to build images from the Dockerfile in the working directory:
    * The first targets the `Run.Registry`, the registry where the task is run, and is tagged with the ACR Tasks run ID. 
    * The second targets the registry identified by the value of `regDate`, which you set when you create the task (or provide through an external `values.yaml` file passed to `az acr task create`). This image is tagged with the run date.
1. Runs a `cmd` step to run one of the built containers. This example starts a long-running container in the background and returns the container ID, then stops the container. In a real-world scenario, you might test a running container to ensure it runs correctly.
1. In a `push` step, pushes the images that were built, the first to the run registry, the second to the registry identified by `regDate`.

### <a name="task-command"></a>Task command

Using the shell environment variables defined previously, create the task by executing the following [az acr task create][az-acr-task-create] command. Substitute the name of your registry for *mycontainerregistrydate*.

```azurecli-interactive
az acr task create \
    --registry $ACR_NAME \
    --name example2 \
    --context https://github.com/$GIT_USER/acr-build-helloworld-node.git \
    --file taskmulti-multiregistry.yaml \
    --git-access-token $GIT_PAT \
    --set regDate=mycontainerregistrydate.azurecr.io
```

### <a name="add-task-credential"></a>Add task credential

To push images to the registry identified by the value of `regDate`, use the [az acr task credential add][az-acr-task-credential-add] command to add login credentials for that registry to the task.

For this example, we recommend that you create a [service principal](container-registry-auth-service-principal.md) with access to the registry scoped to the *AcrPush* role. To create the service principal, see this [Azure CLI script](https://github.com/Azure-Samples/azure-cli-samples/blob/master/container-registry/service-principal-create/service-principal-create.sh).

Pass the service principal application ID and password in the following `az acr task credential add` command:

```azurecli-interactive
az acr task credential add --name example2 \
    --registry $ACR_NAME \
    --login-server mycontainerregistrydate.azurecr.io \
    --username <service-principal-application-id> \
    --password <service-principal-password>
```

The CLI returns the name of the registry login server you added.

### <a name="test-the-multi-step-workflow"></a>Test the multi-step workflow

As in the preceding example, to test the multi-step task, trigger it manually by executing the [az acr task run][az-acr-task-run] command. To trigger the task with a commit to the Git repository, see the section [Trigger a build with a commit](#trigger-a-build-with-a-commit).

```azurecli-interactive
az acr task run --registry $ACR_NAME --name example2
```

Příkaz `az acr task run` ve výchozím nastavení při spuštění příkazu streamuje výstup protokolu do vaší konzoly. As before, the output shows the progress of running each of the task steps. The output is condensed to show key steps.

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

## <a name="next-steps"></a>Další kroky

In this tutorial, you learned how to create multi-step, multi-container-based tasks that automatically trigger when you commit source code to a Git repository. For advanced features of multi-step tasks, including parallel and dependent step execution, see the [ACR Tasks YAML reference](container-registry-tasks-reference-yaml.md). Přejděte k dalšímu kurzu, ve kterém se naučíte vytvářet úlohy, které aktivují sestavení při aktualizaci základní image kontejneru.

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
