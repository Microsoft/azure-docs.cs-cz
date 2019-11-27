---
title: Zásady restartování pro úlohy běhu
description: Naučte se používat Azure Container Instances k provádění úloh, které se spouštějí do dokončení, například v úlohách sestavení, testování nebo vykreslování imagí.
ms.topic: article
ms.date: 04/15/2019
ms.openlocfilehash: f814b1c99827c07f8dadfb0cfd80c87a93377cdc
ms.sourcegitcommit: 85e7fccf814269c9816b540e4539645ddc153e6e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/26/2019
ms.locfileid: "74533464"
---
# <a name="run-containerized-tasks-with-restart-policies"></a>Spuštění kontejnerových úloh pomocí zásad restartování

Náběh a rychlost nasazování kontejnerů v Azure Container Instances poskytují působivou platformu pro provádění úloh spuštění, jako je sestavení, testování a vykreslování obrázků v instanci kontejneru.

Díky konfigurovatelné zásadě restartování můžete určit, že se kontejnery po dokončení jejich procesů zastavily. Vzhledem k tomu, že se instance kontejnerů účtují za sekundu, účtují se vám jenom výpočetní prostředky, které se používají, když je spuštěný kontejner, který spouští vaši úlohu.

Příklady uvedené v tomto článku používají rozhraní příkazového řádku Azure CLI. Musíte mít [místně nainstalovanou][azure-cli-install]verzi Azure CLI 2.0.21 nebo novější, nebo můžete použít rozhraní příkazového řádku v [Azure Cloud Shell](../cloud-shell/overview.md).

## <a name="container-restart-policy"></a>Zásady restartování kontejneru

Při vytváření [skupiny kontejnerů](container-instances-container-groups.md) v Azure Container Instances můžete zadat jedno ze tří nastavení zásad restartování.

| Zásady restartování   | Popis |
| ---------------- | :---------- |
| `Always` | Kontejnery ve skupině kontejnerů se vždy restartují. Toto je **výchozí** nastavení, které se použije, když se při vytváření kontejneru nezadá žádná zásada restartování. |
| `Never` | Kontejnery ve skupině kontejnerů se nikdy nerestartují. Kontejnery se spouštějí současně. |
| `OnFailure` | Kontejnery ve skupině kontejnerů se restartují pouze v případě, že proces spuštěný v kontejneru dojde k chybě (Pokud se ukončí s nenulovým ukončovacím kódem). Kontejnery se alespoň jednou spouštějí. |

## <a name="specify-a-restart-policy"></a>Zadat zásady restartování

Způsob, jakým určíte zásady restartování, závisí na tom, jak vytváříte instance kontejnerů, například pomocí rutin Azure CLI, Azure PowerShell rutin nebo v Azure Portal. V Azure CLI zadejte parametr `--restart-policy` při volání [AZ Container Create][az-container-create].

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer \
    --image mycontainerimage \
    --restart-policy OnFailure
```

## <a name="run-to-completion-example"></a>Příklad spuštění do dokončení

Chcete-li zobrazit zásady restartování v akci, vytvořte instanci kontejneru z image Microsoft [ACI-WORDCOUNT][aci-wordcount-image] a určete zásadu restartování `OnFailure`. V tomto ukázkovém kontejneru se spustí skript Pythonu, který ve výchozím nastavení analyzuje text [Hamletu](http://shakespeare.mit.edu/hamlet/full.html)Shakespeare, zapisuje 10 nejčastějších slov do stdout a pak se ukončí.

Spusťte vzorový kontejner pomocí následujícího příkazu [AZ Container Create][az-container-create] :

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer \
    --image mcr.microsoft.com/azuredocs/aci-wordcount:latest \
    --restart-policy OnFailure
```

Azure Container Instances spustí kontejner a pak ho zastaví, když se jeho aplikace (nebo skript, v tomto případě) ukončí. Když Azure Container Instances zastaví kontejner, jehož zásada restartování je `Never` nebo `OnFailure`, stav kontejneru se nastaví na **ukončeno**. Stav kontejneru můžete zjistit pomocí příkazu [AZ Container show][az-container-show] :

```azurecli-interactive
az container show --resource-group myResourceGroup --name mycontainer --query containers[0].instanceView.currentState.state
```

Příklad výstupu:

```bash
"Terminated"
```

Jakmile se stav ukázkového kontejneru zobrazí jako *ukončeno*, můžete zobrazit jeho výstup jeho úkolů zobrazením protokolů kontejnerů. Spuštěním příkazu [AZ Container logs][az-container-logs] zobrazíte výstup skriptu:

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

Tento příklad ukazuje výstup, který skript odeslal do STDOUT. Vaše kontejnerové úlohy však mohou místo toho zapsat výstup do trvalého úložiště pro pozdější načtení. Například do [sdílené složky Azure](container-instances-mounting-azure-files-volume.md).

## <a name="next-steps"></a>Další kroky

Scénáře založené na úlohách, například dávkové zpracování velké datové sady s několika kontejnery, mohou využívat vlastní [proměnné prostředí](container-instances-environment-variables.md) nebo [příkazové řádky](container-instances-start-command.md) za běhu.

Podrobnosti o tom, jak zachovat výstup kontejnerů, které se spouští k dokončení, najdete v tématu [připojení sdílené složky Azure pomocí Azure Container Instances](container-instances-mounting-azure-files-volume.md).

<!-- LINKS - External -->
[aci-wordcount-image]: https://hub.docker.com/_/microsoft-azuredocs-aci-wordcount

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container?view=azure-cli-latest#az-container-create
[az-container-logs]: /cli/azure/container?view=azure-cli-latest#az-container-logs
[az-container-show]: /cli/azure/container?view=azure-cli-latest#az-container-show
[azure-cli-install]: /cli/azure/install-azure-cli
