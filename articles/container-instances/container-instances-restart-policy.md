---
title: Zásady restartování pro úlohy běhu
description: Naučte se používat Azure Container Instances k provádění úloh, které se spouštějí do dokončení, například v úlohách sestavení, testování nebo vykreslování imagí.
ms.topic: article
ms.date: 08/11/2020
ms.openlocfilehash: 336a31a03cdc9dfdfebe79ef47b59ef90053f523
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "88798937"
---
# <a name="run-containerized-tasks-with-restart-policies"></a>Spuštění kontejnerizovaných úkolů se zásadami restartování

Nasazení kontejnerů ve službě Azure Container Instances je jednoduché a rychlé. Proto je tato platforma vhodná pro úkoly, které se spouští jen jednou, jako je sestavení, testování nebo vykreslení image v instanci kontejneru.

Konfigurovatelné zásady restartování umožňují nastavit, aby se kontejnery po dokončení procesů zastavily. Instance kontejnerů se účtují po sekundách. Proto se vám účtují jenom výpočetní prostředky používané po dobu, kdy kontejner běží a provádí vaši úlohu.

Příklady uvedené v tomto článku používají rozhraní příkazového řádku Azure CLI. Musíte mít [místně nainstalovanou][azure-cli-install]verzi Azure CLI 2.0.21 nebo novější, nebo můžete použít rozhraní příkazového řádku v [Azure Cloud Shell](../cloud-shell/overview.md).

## <a name="container-restart-policy"></a>Zásady restartování kontejneru

Při vytváření [skupiny kontejnerů](container-instances-container-groups.md) v Azure Container Instances můžete zadat jedno ze tří nastavení zásad restartování.

| Zásada restartování   | Popis |
| ---------------- | :---------- |
| `Always` | Kontejnery ve skupině kontejnerů se restartují vždy. Jde o **výchozí** nastavení, které se použije vždy, když při vytvoření kontejneru nezadáte zásadu restartování. |
| `Never` | Kontejnery ve skupině kontejnerů se nerestartují nikdy. Kontejnery se spouštějí nejvýše jednou. |
| `OnFailure` | Kontejnery ve skupině se restartují jen v případě, že proces spuštěný v kontejneru nebude úspěšný (když skončí nenulovým ukončovacím kódem). Kontejnery se spouštějí aspoň jednou. |

[!INCLUDE [container-instances-restart-ip](../../includes/container-instances-restart-ip.md)]

## <a name="specify-a-restart-policy"></a>Zadat zásady restartování

Způsob, jakým určíte zásady restartování, závisí na tom, jak vytváříte instance kontejnerů, například pomocí rutin Azure CLI, Azure PowerShell rutin nebo v Azure Portal. V Azure CLI zadejte `--restart-policy` parametr při volání [AZ Container Create][az-container-create].

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer \
    --image mycontainerimage \
    --restart-policy OnFailure
```

## <a name="run-to-completion-example"></a>Příklad spuštění do dokončení

Chcete-li zobrazit zásady restartování v akci, vytvořte instanci kontejneru z image Microsoft [ACI-WORDCOUNT][aci-wordcount-image] a určete `OnFailure` zásady restartování. V tomto ukázkovém kontejneru se spustí skript Pythonu, který ve výchozím nastavení analyzuje text [Hamletu](http://shakespeare.mit.edu/hamlet/full.html)Shakespeare, zapisuje 10 nejčastějších slov do stdout a pak se ukončí.

Spusťte vzorový kontejner pomocí následujícího příkazu [AZ Container Create][az-container-create] :

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer \
    --image mcr.microsoft.com/azuredocs/aci-wordcount:latest \
    --restart-policy OnFailure
```

Azure Container Instances spustí kontejner a po skončení aplikace (v tomto případě skriptu) ho zastaví. Když Azure Container Instances zastaví kontejner, jehož zásada restartování je `Never` nebo `OnFailure` , stav kontejneru je nastaven na **ukončeno**. Stav kontejneru můžete zjistit pomocí příkazu [AZ Container show][az-container-show] :

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

Tento příklad ukazuje výstup, který skript odeslal do STDOUT. Vaše kontejnerové úlohy však mohou místo toho zapsat výstup do trvalého úložiště pro pozdější načtení. Například do [sdílené složky Azure](./container-instances-volume-azure-files.md).

## <a name="next-steps"></a>Další kroky

Scénáře založené na úlohách, například dávkové zpracování velké datové sady s několika kontejnery, mohou využívat vlastní [proměnné prostředí](container-instances-environment-variables.md) nebo [příkazové řádky](container-instances-start-command.md) za běhu.

Podrobnosti o tom, jak zachovat výstup kontejnerů, které se spouští k dokončení, najdete v tématu [připojení sdílené složky Azure pomocí Azure Container Instances](./container-instances-volume-azure-files.md).

<!-- LINKS - External -->
[aci-wordcount-image]: https://hub.docker.com/_/microsoft-azuredocs-aci-wordcount

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container?view=azure-cli-latest#az-container-create
[az-container-logs]: /cli/azure/container?view=azure-cli-latest#az-container-logs
[az-container-show]: /cli/azure/container?view=azure-cli-latest#az-container-show
[azure-cli-install]: /cli/azure/install-azure-cli
