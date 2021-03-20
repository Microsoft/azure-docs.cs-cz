---
title: Zobrazit protokoly spuštění úlohy – úlohy
description: Jak zobrazit a spravovat protokoly spuštění generované ACR úkoly
ms.topic: article
ms.date: 03/09/2020
ms.openlocfilehash: b2a10d4a3a2746acf38445673af994c6317c77de
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "93027172"
---
# <a name="view-and-manage-task-run-logs"></a>Zobrazení a správa protokolů spuštění úloh

Každý běh úlohy v [Azure Container Registry úlohy](container-registry-tasks-overview.md) generuje výstup protokolu, který můžete zkontrolovat, abyste zjistili, jestli se kroky úlohy úspěšně spustily. 

Tento článek vysvětluje, jak zobrazit a spravovat protokoly spuštění úloh.

## <a name="view-streamed-logs"></a>Zobrazit protokoly streamování

Při ručním spuštění úlohy se výstup protokolu vysílá přímo do konzoly. Pokud například aktivujete úlohu ručně pomocí příkazu [AZ ACR Build](/cli/azure/acr#az-acr-build), [AZ ACR Run](/cli/azure/acr#az-acr-run)nebo [AZ ACR Task Run](/cli/azure/acr/task#az-acr-task-run) , zobrazí se výstup protokolu do konzoly. 

Následující ukázka [AZ ACR Run](/cli/azure/acr#az-acr-run) Command ručně aktivuje úlohu, která spouští kontejner získaný ze stejného registru:

```azurecli
az acr run --registry mycontainerregistry1220 \
  --cmd '$Registry/samples/hello-world:v1' /dev/null
```

Protokol streamování:

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

Azure Container Registry ukládá protokoly spuštění pro všechny úlohy. Uložené protokoly spuštění můžete zobrazit v Azure Portal. Případně můžete pomocí příkazu [AZ ACR Task logs](/cli/azure/acr/task#az-acr-task-logs) zobrazit vybraný protokol. Ve výchozím nastavení se protokoly uchovávají po dobu 30 dnů.

Pokud se úloha spustí automaticky, například při aktualizaci zdrojového kódu, přístup k uloženým protokolům je *jediným* způsobem, jak zobrazit protokoly spuštění. Automatické triggery úkolů zahrnují potvrzení zdrojového kódu nebo žádosti o přijetí změn, základní aktualizace obrázků a triggery časovače.

Zobrazení protokolů spuštění na portálu:

1. Přejděte do registru kontejneru.
1. V **služby** vyberte možnost   >  **spuštěné** úlohy.
1. Vyberte **ID spuštění** pro zobrazení stavu spuštění a protokolů spuštění. Pokud je vygenerováno, protokol obsahuje stejné informace jako protokol streamování.

![Zobrazit portál pro přihlášení ke spuštění úlohy](./media/container-registry-tasks-logs/portal-task-run-logs.png)

Pokud chcete zobrazit protokol pomocí Azure CLI, spusťte [protokol AZ ACR Task log](/cli/azure/acr/task#az-acr-task-logs) a zadejte ID spuštění, název úlohy nebo konkrétní obrázek, který vytvoří úloha sestavení. Je-li zadán název úlohy, příkaz zobrazí protokol posledního vytvořeného spuštění.

Následující příklad vypíše výstup protokolu pro běh s ID *CF4*:

```azurecli
az acr task logs --registry mycontainerregistry1220 \
  --run-id cf4
```

## <a name="alternative-log-storage"></a>Alternativní úložiště protokolů

Můžete chtít uložit protokoly spuštění úkolů v místním systému souborů nebo použít alternativní řešení archivace, například Azure Storage.

Vytvořte například místní adresář *tasklogs* a přesměrujte výstup [protokolu AZ ACR Task log](/cli/azure/acr/task#az-acr-task-logs) do místního souboru:

```azurecli
mkdir ~/tasklogs

az acr task logs --registry mycontainerregistry1220 \
  --run-id cf4 > ~/tasklogs/cf4.log
```

Do Azure Storage můžete ukládat i místní soubory protokolů. Například můžete použít rozhraní příkazového [řádku Azure CLI](../storage/blobs/storage-quickstart-blobs-cli.md), [Azure Portal](../storage/blobs/storage-quickstart-blobs-portal.md)nebo jiné metody pro nahrání souborů do účtu úložiště.

## <a name="next-steps"></a>Další kroky

* Další informace o [úlohách Azure Container Registry](container-registry-tasks-overview.md)


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
