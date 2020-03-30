---
title: Zásady restartování pro úlohy spouštění
description: Zjistěte, jak používat instance kontejnerů Azure ke spouštění úloh, které se spouštějí do dokončení, jako jsou úlohy sestavení, testování nebo vykreslování obrázků.
ms.topic: article
ms.date: 04/15/2019
ms.openlocfilehash: 8ef4ef228038242f53abc8041470f7f596ab1157
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80131497"
---
# <a name="run-containerized-tasks-with-restart-policies"></a>Spuštění kontejnerizovaných úloh se zásadami restartování

Nasazení kontejnerů ve službě Azure Container Instances je jednoduché a rychlé. Proto je tato platforma vhodná pro úkoly, které se spouští jen jednou, jako je sestavení, testování nebo vykreslení image v instanci kontejneru.

Konfigurovatelné zásady restartování umožňují nastavit, aby se kontejnery po dokončení procesů zastavily. Instance kontejnerů se účtují po sekundách. Proto se vám účtují jenom výpočetní prostředky používané po dobu spuštění kontejneru, když plní vaši úlohu.

Příklady uvedené v tomto článku použít azure cli. Musíte mít Azure CLI verze 2.0.21 nebo vyšší [nainstalované místně][azure-cli-install], nebo použít cli v [Azure Cloud Shell](../cloud-shell/overview.md).

## <a name="container-restart-policy"></a>Zásady restartování kontejneru

Když vytvoříte [skupinu kontejnerů](container-instances-container-groups.md) v Azure Container Instances, můžete zadat jedno ze tří nastavení zásad restartování.

| Zásady restartování   | Popis |
| ---------------- | :---------- |
| `Always` | Kontejnery ve skupině kontejnerů se restartují vždy. Jde o **výchozí** nastavení, které se použije vždy, když při vytvoření kontejneru nezadáte zásadu restartování. |
| `Never` | Kontejnery ve skupině kontejnerů se nerestartují nikdy. Kontejnery se spouštějí nejvýše jednou. |
| `OnFailure` | Kontejnery ve skupině se restartují jen v případě, že proces spuštěný v kontejneru nebude úspěšný (když skončí nenulovým ukončovacím kódem). Kontejnery se spouštějí aspoň jednou. |

## <a name="specify-a-restart-policy"></a>Určení zásad restartování

Způsob, jakým zadáte zásady restartování, závisí na tom, jak vytvoříte instance kontejneru, například pomocí azure cli, rutin Azure PowerShell nebo na webu Azure Portal. V rozhraní příkazového řádku `--restart-policy` Azure zadejte parametr při volání [az kontejner create][az-container-create].

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer \
    --image mycontainerimage \
    --restart-policy OnFailure
```

## <a name="run-to-completion-example"></a>Příklad spuštění dokončení

Chcete-li zobrazit zásady restartování v akci, vytvořte instanci kontejneru `OnFailure` z image [microsoft aci-wordcount][aci-wordcount-image] a zadejte zásadu restartování. Tento příklad kontejneru spustí skript Pythonu, který ve výchozím nastavení analyzuje text Shakespearova [Hamleta](http://shakespeare.mit.edu/hamlet/full.html), zapíše 10 nejběžnějších slov do STDOUT a potom ukončí.

Spusťte ukázkový kontejner s následujícím [příkazem az container create:][az-container-create]

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer \
    --image mcr.microsoft.com/azuredocs/aci-wordcount:latest \
    --restart-policy OnFailure
```

Azure Container Instances spustí kontejner a po skončení aplikace (v tomto případě skriptu) ho zastaví. Když Instance kontejneru Azure zastaví kontejner, jehož zásady restartování je `Never` nebo `OnFailure`, stav kontejneru je nastavena na **Ukončeno**. Stav kontejneru můžete zkontrolovat pomocí příkazu [az container show:][az-container-show]

```azurecli-interactive
az container show \
    --resource-group myResourceGroup \
    --name mycontainer \
    --query containers[0].instanceView.currentState.state
```

Příklad výstupu:

```bash
"Terminated"
```

Jakmile se zobrazí stav kontejneru v příkladu *Ukončeno*, zobrazí se výstup jeho úlohy zobrazením protokolů kontejnerů. Spuštěním příkazu [az container logs][az-container-logs] zobrazte výstup skriptu:

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

Tento příklad ukazuje výstup, který skript odeslaný stdout. Kontejnerizované úkoly však místo toho může zapsat jejich výstup do trvalého úložiště pro pozdější načtení. Například do [sdílené složky Azure](container-instances-mounting-azure-files-volume.md).

## <a name="next-steps"></a>Další kroky

Scénáře založené na úlohách, jako je například dávkové zpracování velké datové sady s několika kontejnery, můžete využít vlastní [proměnné prostředí](container-instances-environment-variables.md) nebo [příkazové řádky](container-instances-start-command.md) za běhu.

Podrobnosti o tom, jak zachovat výstup kontejnerů, které běží na dokončení, najdete [v tématu připojení sdílené složky Azure s instancemi kontejneru Azure](container-instances-mounting-azure-files-volume.md).

<!-- LINKS - External -->
[aci-wordcount-image]: https://hub.docker.com/_/microsoft-azuredocs-aci-wordcount

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container?view=azure-cli-latest#az-container-create
[az-container-logs]: /cli/azure/container?view=azure-cli-latest#az-container-logs
[az-container-show]: /cli/azure/container?view=azure-cli-latest#az-container-show
[azure-cli-install]: /cli/azure/install-azure-cli
