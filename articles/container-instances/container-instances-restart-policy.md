---
title: Použití zásad kontejnerizované úlohy restartovat ve službě Azure Container Instances
description: Zjistěte, jak spustit úlohy, které běží na dokončení, například v sestavení, testovací nebo úlohy vykreslování obrázků pomocí Azure Container Instances.
services: container-instances
author: dlepow
ms.service: container-instances
ms.topic: article
ms.date: 12/10/2018
ms.author: danlep
ms.openlocfilehash: b254adb050aa9826170c0849c3811380db6d9b38
ms.sourcegitcommit: e37fa6e4eb6dbf8d60178c877d135a63ac449076
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/13/2018
ms.locfileid: "53321029"
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

Pokud chcete zobrazit zásady restartování v akci, vytvořit instanci kontejneru z [microsoft/aci-wordcount] [ aci-wordcount-image] obrázku a zadejte `OnFailure` zásady restartování. Tento příklad kontejner spustí skript v jazyce Python, který ve výchozím nastavení, analyzuje text prvku Shakespeare [obce](http://shakespeare.mit.edu/hamlet/full.html), zapíše 10 nejčastější slova do STDOUT a následně skončí.

Spuštění kontejneru příklad následujícím [az container vytvořit] [ az-container-create] příkaz:

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer \
    --image microsoft/aci-wordcount:latest \
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

## <a name="manually-stop-and-start-a-container-group"></a>Ručně zastavit a spustit skupinu kontejnerů

Bez ohledu na nakonfigurované pro zásady restartování [skupinu kontejnerů](container-instances-container-groups.md), můžete chtít ručně zastavit nebo spustit skupinu kontejnerů.

* **Zastavit** - spuštěnou skupinu kontejnerů můžete zastavit ručně kdykoli – například s použitím [az container stop] [ az-container-stop] příkazu. Pro některé úlohy kontejneru, můžete chtít zastavit skupiny kontejnerů za definované období a Šetřete na náklady. 

  Zastavuje se skupinu kontejnerů se ukončí a recykluje kontejnerů ve skupině; stav kontejneru nezachová. 

* **Spustit** – když skupinu kontejnerů se bylo zastaveno – buď protože kontejnery ukončena sami nebo k ručnímu zastavení skupině – můžete použít [API spustit kontejner](/rest/api/container-instances/containergroups/start) nebo webu Azure portal ohledně ručního spuštění kontejnerů Skupina. Pokud image kontejneru pro každý kontejner je aktualizován, je vyžádá novou bitovou kopii. 

  Počáteční skupiny kontejnerů začne nové nasazení se stejnou konfigurací kontejneru. Tuto akci můžete rychle znovu použít konfiguraci skupiny známé kontejneru, která funguje podle očekávání. Není nutné vytvořit novou skupinu kontejnerů ke spuštění stejná úloha.

* **Restartujte** – je možné restartovat skupiny kontejnerů je spuštěný – například pomocí [az container restartování] [ az-container-restart] příkazu. Tato akce restartuje všechny kontejnery ve skupině kontejnerů. Pokud image kontejneru pro každý kontejner je aktualizován, je vyžádá novou bitovou kopii. 

  Restartování skupiny kontejnerů je užitečné, pokud chcete k vyřešení problému nasazení. Například pokud omezení dočasný prostředek, který brání kontejnery v úspěšném spuštění, restartování skupiny můžou tyto potíže vyřešit.

Po ruční spuštění nebo restartování skupiny kontejnerů, spuštění kontejneru skupiny podle nakonfigurované zásady restartování.

## <a name="configure-containers-at-runtime"></a>Konfigurace kontejnery za běhu

Při vytváření instance kontejneru, můžete nastavit jeho **proměnné prostředí**, stejně jako zadejte vlastní **příkazového řádku** provést při spuštění kontejneru. Tato nastavení můžete použít ve svých úlohách služby batch k přípravě každý kontejner se konfigurace pro konkrétní úlohy.

## <a name="environment-variables"></a>Proměnné prostředí

Nastavení proměnných prostředí ve vašem kontejneru poskytnout dynamickou konfiguraci aplikace nebo skript spustit v kontejneru. Podobá se to `--env` argument příkazového řádku k `docker run`.

Například můžete změnit chování skriptu v kontejneru příkladu tak, že zadáte následující proměnné prostředí při vytváření instance kontejneru:

*NumWords*: Počet odeslaných do STDOUT slov.

*MinLength*: Minimální počet znaků v aplikaci word, které se mají spočítat. Větší číslo ignoruje běžná slova, jako je "z" a "the".

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer2 \
    --image microsoft/aci-wordcount:latest \
    --restart-policy OnFailure \
    --environment-variables NumWords=5 MinLength=8
```

Zadáním `NumWords=5` a `MinLength=8` proměnných prostředí kontejneru, by měl zobrazit protokoly kontejneru různé výstup. Jakmile se zobrazí stav kontejneru *ukončeno* (použijte `az container show` zkontrolovat její stav), zobrazovat protokoly zobrazíte nový výstup:

```azurecli-interactive
az container logs --resource-group myResourceGroup --name mycontainer2
```

Výstup:

```bash
[('CLAUDIUS', 120),
 ('POLONIUS', 113),
 ('GERTRUDE', 82),
 ('ROSENCRANTZ', 69),
 ('GUILDENSTERN', 54)]
```



## <a name="command-line-override"></a>Přepsání příkazového řádku

Určuje příkazový řádek, když vytváříte instanci kontejneru přepsat příkazového řádku vloženými do image kontejneru. Podobá se to `--entrypoint` argument příkazového řádku k `docker run`.

Například můžete mít kontejneru příklad analyzovat text než *obce* zadáním různých příkazového řádku. Spuštění kontejneru, skript v jazyce Python *wordcount.py*, přijímá jako argument adresy URL a budou zpracovávat obsah na této stránce místo výchozího.

Například chcete-li určit nejčastějších 3 pět písmeno slov v *Valentýne a Juliet*:

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer3 \
    --image microsoft/aci-wordcount:latest \
    --restart-policy OnFailure \
    --environment-variables NumWords=3 MinLength=5 \
    --command-line "python wordcount.py http://shakespeare.mit.edu/romeo_juliet/full.html"
```

Znovu Jakmile bude kontejner *ukončeno*, zobrazte výstup zobrazením protokolů kontejneru:

```azurecli-interactive
az container logs --resource-group myResourceGroup --name mycontainer3
```

Výstup:

```bash
[('ROMEO', 177), ('JULIET', 134), ('CAPULET', 119)]
```

## <a name="next-steps"></a>Další postup

### <a name="persist-task-output"></a>Trvalý výstup úloh

Podrobnosti o tom, jak zachovat výstup kontejnery, na kterých běží až do ukončení najdete v tématu [připojení sdílené složky Azure pomocí služby Azure Container Instances](container-instances-mounting-azure-files-volume.md).

<!-- LINKS - External -->
[aci-wordcount-image]: https://hub.docker.com/r/microsoft/aci-wordcount/

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container?view=azure-cli-latest#az-container-create
[az-container-logs]: /cli/azure/container?view=azure-cli-latest#az-container-logs
[az-container-restart]: /cli/azure/container?view=azure-cli-latest#az-container-restart
[az-container-show]: /cli/azure/container?view=azure-cli-latest#az-container-show
[az-container-stop]: /cli/azure/container?view=azure-cli-latest#az-container-stop
[azure-cli-install]: /cli/azure/install-azure-cli
