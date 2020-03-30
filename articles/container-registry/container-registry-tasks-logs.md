---
title: Zobrazit protokoly spuštění úloh – úkoly
description: Jak zobrazit a spravovat protokoly spuštění generované úkoly ACR.
ms.topic: article
ms.date: 03/09/2020
ms.openlocfilehash: f7098f470a3f8a0cdac019f4bf8eb8fe14330337
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79246965"
---
# <a name="view-and-manage-task-run-logs"></a>Zobrazení a správa protokolů spuštění úloh

Každá úloha spuštěná v [úlohách registru kontejnerů Azure](container-registry-tasks-overview.md) generuje výstup protokolu, který můžete zkontrolovat a zjistit, zda byly kroky úlohy úspěšně spuštěny. 

Tento článek vysvětluje, jak zobrazit a spravovat protokoly spuštění úloh.

## <a name="view-streamed-logs"></a>Zobrazení streamovaných protokolů

Při ručním spuštění úlohy je výstup protokolu přenášen přímo do konzoly. Pokud například spustíte úlohu ručně pomocí příkazu [az acr build](/cli/azure/acr#az-acr-build), [az acr run](/cli/azure/acr#az-acr-run)nebo [az acr task run,](/cli/azure/acr/task#az-acr-task-run) zobrazí se výstup protokolu vysílaný do konzoly. 

Následující ukázkový příkaz [az acr run](/cli/azure/acr#az-acr-run) ručně spustí úlohu, která spouští kontejner vytažený ze stejného registru:

```azurecli
az acr run --registry mycontainerregistry1220 \
  --cmd '$Registry/samples/hello-world:v1' /dev/null
```

Streamovaný protokol:

```console
Queued a run with ID: cf4
Waiting for an agent...
2020/03/09 20:30:10 Alias support enabled for version >= 1.1.0, please see https://aka.ms/acr/tasks/task-aliases for more information.
2020/03/09 20:30:10 Creating Docker network: acb_default_network, driver: 'bridge'
2020/03/09 20:30:10 Successfully set up Docker network: acb_default_network
2020/03/09 20:30:10 Setting up Docker configuration...
2020/03/09 20:30:11 Successfully set up Docker configuration
2020/03/09 20:30:11 Logging in to registry: mycontainerregistry1220azurecr.io
2020/03/09 20:30:12 Successfully logged into mycontainerregistry1220azurecr.io
2020/03/09 20:30:12 Executing step ID: acb_step_0. Timeout(sec): 600, Working directory: '', Network: 'acb_default_network'
2020/03/09 20:30:12 Launching container with name: acb_step_0
Unable to find image 'mycontainerregistry1220azurecr.io/samples/hello-world:v1' locally
v1: Pulling from samples/hello-world
Digest: sha256:92c7f9c92844bbbb5d0a101b22f7c2a7949e40f8ea90c8b3bc396879d95e888a
Status: Downloaded newer image for mycontainerregistry1220azurecr.io/samples/hello-world:v1

Hello from Docker!
This message shows that your installation appears to be working correctly.
[...]

2020/03/09 20:30:13 Successfully executed container: acb_step_0
2020/03/09 20:30:13 Step ID: acb_step_0 marked as successful (elapsed time in seconds: 1.180081)

Run ID: cf4 was successful after 5s
```

## <a name="view-stored-logs"></a>Zobrazit uložené protokoly 

Úložiště registru kontejnerů Azure spouštějí protokoly pro všechny úlohy. Uložené protokoly spuštění můžete zobrazit na webu Azure Portal. Nebo pomocí příkazu [az acr protokoly úloh](/cli/azure/acr/task#az-acr-task-logs) zobrazte vybraný protokol. Ve výchozím nastavení jsou protokoly uchovávány po dobu 30 dnů.

Pokud je úloha automaticky spuštěna, například aktualizací zdrojového kódu, je přístup k uloženým protokolům *jediným* způsobem, jak zobrazit protokoly spuštění. Mezi automatické aktivační události úloh patří potvrzení zdrojového kódu nebo žádosti o přijetí získat do kupy, aktualizace základní bitové kopie a aktivační události časovače.

Zobrazení protokolů spuštění na portálu:

1. Přejděte do registru kontejnerů.
1. Ve **službě Services**vyberte příkaz **Spouští úlohy** > **Runs**.
1. Chcete-li zobrazit stav spuštění a spustit protokoly, vyberte **id spuštění.** Protokol obsahuje stejné informace jako datový proud protokolu, pokud je generován.

![Zobrazit přihlašovací portál spuštění úlohy](./media/container-registry-tasks-logs/portal-task-run-logs.png)

Chcete-li zobrazit protokol pomocí příkazového příkazu k onomu Azure CLI, [spusťte protokoly úloh az acr](/cli/azure/acr/task#az-acr-task-logs) a zadejte ID spuštění, název úlohy nebo konkrétní bitovou kopii vytvořit úlohou sestavení. Pokud je zadán název úlohy, příkaz zobrazí protokol pro poslední vytvořené spuštění.

Následující příklad výstupy protokolu pro spuštění s ID *cf4*:

```azurecli
az acr task logs --registry mycontainerregistry1220 \
  --run-id cf4
```

## <a name="alternative-log-storage"></a>Alternativní úložiště protokolů

Můžete chtít uložit protokoly spuštění úloh v místním systému souborů nebo použít alternativní řešení archivace, jako je Azure Storage.

Můžete například vytvořit místní adresář *tasklogů* a přesměrovat výstup [protokolů úloh az acr](/cli/azure/acr/task#az-acr-task-logs) do místního souboru:

```azurecli
mkdir ~/tasklogs

az acr task logs --registry mycontainerregistry1220 \
  --run-id cf4 > ~/tasklogs/cf4.log
```

Můžete také uložit místní soubory protokolu do služby Azure Storage. Například použijte [Azure CLI](../storage/blobs/storage-quickstart-blobs-cli.md), [portál Azure](../storage/blobs/storage-quickstart-blobs-portal.md)nebo jiné metody k nahrání souborů do účtu úložiště.


## <a name="next-steps"></a>Další kroky

* Další informace o [úlohách registru kontejnerů Azure](container-registry-tasks-overview.md)

<!-- LINKS - External -->
[base-alpine]: https://hub.docker.com/_/alpine/
[base-dotnet]: https://hub.docker.com/r/microsoft/dotnet/
[base-node]: https://hub.docker.com/_/node/
[base-windows]: https://hub.docker.com/r/microsoft/nanoserver/
[sample-archive]: https://github.com/Azure-Samples/acr-build-helloworld-node/archive/master.zip
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - Internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-build]: /cli/azure/acr#az-acr-build
[az-acr-pack-build]: /cli/azure/acr/pack#az-acr-pack-build
[az-acr-task]: /cli/azure/acr/task
[az-acr-task-create]: /cli/azure/acr/task#az-acr-task-create
[az-acr-task-run]: /cli/azure/acr/task#az-acr-task-run
[az-acr-task-update]: /cli/azure/acr/task#az-acr-task-update
[az-login]: /cli/azure/reference-index#az-login
[az-login-service-principal]: /cli/azure/authenticate-azure-cli

<!-- IMAGES -->
[quick-build-01-fork]: ./media/container-registry-tutorial-quick-build/quick-build-01-fork.png
[quick-build-02-browser]: ./media/container-registry-tutorial-quick-build/quick-build-02-browser.png
