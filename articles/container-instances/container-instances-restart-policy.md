---
title: Použití zásad kontejnerizované úlohy restartovat ve službě Azure Container Instances
description: Zjistěte, jak spustit úlohy, které běží na dokončení, například v sestavení, testovací nebo úlohy vykreslování obrázků pomocí Azure Container Instances.
services: container-instances
author: dlepow
ms.service: container-instances
ms.topic: article
ms.date: 04/15/2019
ms.author: danlep
ms.openlocfilehash: 06872eefd0d500a22214109ad5055dd236b5a6ac
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "60608115"
---
# <a name="run-containerized-tasks-with-restart-policies"></a>Spouštění kontejnerizovaných úloh pomocí zásady restartování

Snadné a rychlost nasazování kontejnerů ve službě Azure Container Instances poskytuje poutavé platformu pro provedení úlohy spouštěné jednou, jako je sestavování, testování a vykreslování obrázků v instanci kontejneru.

U zásady konfigurovat restartování můžete zadat, že kontejnery se zastaví po dokončení procesu. Protože služba container instances se účtuje po sekundách, platíte jenom za výpočetní prostředky použité během provádění úlohy kontejneru.

Příklady uvedené v tomto článku využívají Azure CLI. Musíte mít Azure CLI verze 2.0.21 nebo novější [nainstalovaný místně][azure-cli-install], nebo pomocí rozhraní příkazového řádku v [Azure Cloud Shell](../cloud-shell/overview.md).

## <a name="container-restart-policy"></a>Zásady restartování pro kontejner

Když vytvoříte [skupinu kontejnerů](container-instances-container-groups.md) ve službě Azure Container Instances, můžete určit jednu z tři nastavení zásad restartovat.

| Zásady restartování   | Popis |
| ---------------- | :---------- |
| `Always` | Kontejnery ve skupině kontejnerů se vždy restartuje. Toto je **výchozí** nastavení použijí v případě, že žádné zásady restartování je určený při vytvoření kontejneru. |
| `Never` | Nikdy se restartují kontejnerů ve skupině kontejnerů. Kontejnery spustit maximálně jednou. |
| `OnFailure` | Kontejnery ve skupině kontejnerů se restartují jenom v případě, že proces spuštěný v kontejneru selže (při svém ukončení s nenulový ukončovací kód). Kontejnery jsou aspoň jednou spustili. |

## <a name="specify-a-restart-policy"></a>Zadejte zásady restartování

Jak určit zásady restartování závisí na způsobu vytvoření instancí kontejneru, například pomocí Azure CLI, rutin prostředí Azure PowerShell, nebo na webu Azure Portal. V rozhraní příkazového řádku Azure, zadejte `--restart-policy` parametru při volání [az container vytvořit][az-container-create].

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer \
    --image mycontainerimage \
    --restart-policy OnFailure
```

## <a name="run-to-completion-example"></a>Spuštění příkladu dokončení

Zásady restartování v akci najdete vytvořit instanci kontejneru z Microsoft [aci wordcount] [ aci-wordcount-image] obrázku a zadejte `OnFailure` zásady restartování. Tento příklad kontejner spustí skript v jazyce Python, který ve výchozím nastavení, analyzuje text prvku Shakespeare [obce](http://shakespeare.mit.edu/hamlet/full.html), zapíše 10 nejčastější slova do STDOUT a následně skončí.

Spuštění kontejneru příklad následujícím [az container vytvořit] [ az-container-create] příkaz:

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer \
    --image mcr.microsoft.com/azuredocs/aci-wordcount:latest \
    --restart-policy OnFailure
```

Služba Azure Container Instances spustí kontejner a pak se zastaví při ukončení aplikace (nebo skriptu, v tomto případě). Při zastavení kontejner, jehož zásady restartování je Azure Container Instances `Never` nebo `OnFailure`, stav kontejneru je nastaven na **ukončeno**. Stav kontejneru s můžete zjistit [az container show] [ az-container-show] příkaz:

```azurecli-interactive
az container show --resource-group myResourceGroup --name mycontainer --query containers[0].instanceView.currentState.state
```

Příklad výstupu:

```bash
"Terminated"
```

Jakmile se zobrazí stav kontejneru příklad *ukončeno*, zobrazí se jeho výstup úlohy zobrazením protokolů kontejneru. Spustit [protokoly kontejneru az] [ az-container-logs] výstup příkazu zobrazíte skriptu:

```azurecli-interactive
az container logs --resource-group myResourceGroup --name mycontainer
```

Výstup:

```bash
[('the', 990),
 ('and', 702),
 ('of', 628),
 ('to', 610),
 ('I', 544),
 ('you', 495),
 ('a', 453),
 ('my', 441),
 ('in', 399),
 ('HAMLET', 386)]
```

Tento příklad ukazuje výstup, který skript odeslán do STDOUT. Kontejnerizované úlohy, ale může být místo toho zapisují svůj výstup do trvalého úložiště pro pozdější načtení. Například [sdílené složky Azure](container-instances-mounting-azure-files-volume.md).

## <a name="next-steps"></a>Další postup

Scénáře založené na úlohách, jako jsou dávkové zpracování velkou datovou sadu s několika kontejnery, můžou využít výhod vlastního [proměnné prostředí](container-instances-environment-variables.md) nebo [příkazové řádky](container-instances-start-command.md) za běhu.

Podrobnosti o tom, jak zachovat výstup kontejnery, na kterých běží až do ukončení najdete v tématu [připojení sdílené složky Azure pomocí služby Azure Container Instances](container-instances-mounting-azure-files-volume.md).

<!-- LINKS - External -->
[aci-wordcount-image]: https://hub.docker.com/_/microsoft-azuredocs-aci-wordcount

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container?view=azure-cli-latest#az-container-create
[az-container-logs]: /cli/azure/container?view=azure-cli-latest#az-container-logs
[az-container-show]: /cli/azure/container?view=azure-cli-latest#az-container-show
[azure-cli-install]: /cli/azure/install-azure-cli
