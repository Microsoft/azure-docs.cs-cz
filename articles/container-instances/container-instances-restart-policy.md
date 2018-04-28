---
title: Spustit kontejnerizované úlohy v Azure kontejner instancí
description: Naučte se používat Azure kontejner instancí spuštění úlohy, které spustí dokončen, například v úlohy vykreslování obrázku, testovací nebo sestavení.
services: container-instances
author: mmacy
manager: jeconnoc
ms.service: container-instances
ms.topic: article
ms.date: 11/16/2017
ms.author: marsma
ms.openlocfilehash: 3bbe3e891423b6ad62a1d1093daef304206f3d76
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2018
---
# <a name="run-a-containerized-task-in-azure-container-instances"></a>Spuštění kontejnerizované úlohy v Azure kontejner instancí

Snadné a rychlost nasazení kontejnerů v Azure kontejner instancí poskytuje poutavé platformu pro provádění jedno spuštění úlohy, jako je sestavení, testování a vykreslování obrázků v instanci kontejneru.

Zásadám Konfigurovat restartování můžete určit, že po dokončení všech jejich procesy, zastaví se kontejnerů. Protože kontejner instancí se účtují druhou, se vám účtovat pouze pro výpočetní prostředky používá, když běží kontejneru provádění úkolu.

Příklady uvedené v tomto článku rozhraní příkazového řádku Azure. Musíte mít Azure CLI verze 2.0.21 nebo větší [nainstalovány místně][azure-cli-install], nebo pomocí rozhraní příkazového řádku v [prostředí cloudu Azure](../cloud-shell/overview.md).

## <a name="container-restart-policy"></a>Zásada restartu kontejneru

Když vytvoříte kontejner instancí kontejneru Azure, můžete zadat jeden ze tří nastavení zásad restartování.

| Restartujte zásad   | Popis |
| ---------------- | :---------- |
| `Always` | Kontejnery v kontejneru skupiny jsou vždycky restartovat. Toto je **výchozí** nastavení použitá v případě žádná zásada restartu je zadána při vytváření kontejneru. |
| `Never` | Kontejnery ve skupině kontejneru se nikdy restartovat. Kontejnery spustit maximálně jednou. |
| `OnFailure` | Kontejnery ve skupině kontejneru se restartují jenom v případě, že je proces spuštěný v kontejneru selže (pokud ho ukončí s nenulový ukončovací kód). Kontejnery jsou spustit alespoň jednou. |

## <a name="specify-a-restart-policy"></a>Zadejte zásady restartování

Jak specifikujete zásada restartu závisí na způsobu vytvoření vaše instance kontejneru jako pomocí Azure CLI, rutin prostředí Azure PowerShell, nebo na portálu Azure. V rozhraní příkazového řádku Azure, zadejte `--restart-policy` parametr při volání [vytvořit kontejner az][az-container-create].

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer \
    --image mycontainerimage \
    --restart-policy OnFailure
```

## <a name="run-to-completion-example"></a>Spustit příkladu dokončení

Pokud chcete zobrazit zásady restartování v akci, vytvořte instanci kontejneru z [microsoft/aci-wordcount] [ aci-wordcount-image] bitovou kopii a určete `OnFailure` začít znovu. Tento příklad kontejner spustí skript v jazyce Python, která ve výchozím nastavení, analyzuje text na Shakespeare [obce](http://shakespeare.mit.edu/hamlet/full.html), zapíše 10 nejčastější slova do STDOUT a bude ukončen.

Spusťte kontejner příklad s následující [vytvořit kontejner az] [ az-container-create] příkaz:

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer \
    --image microsoft/aci-wordcount:latest \
    --restart-policy OnFailure
```

Azure kontejner instancí se spustí kontejneru a zastaví se při ukončení aplikace (nebo skriptu, v tomto případě). Když Azure kontejner instancí zastaví kontejner, jejichž zásada restartu se `Never` nebo `OnFailure`, je nastaven stav kontejneru **ukončeno**. Můžete zkontrolovat stav kontejneru se [az kontejneru zobrazit] [ az-container-show] příkaz:

```azurecli-interactive
az container show --resource-group myResourceGroup --name mycontainer --query containers[0].instanceView.currentState.state
```

Příklad výstupu:

```bash
"Terminated"
```

Jakmile se stav kontejneru příklad ukazuje *ukončeno*, se zobrazí jeho výstup úlohy zobrazením protokoly kontejneru. Spustit [az kontejneru protokoly] [ az-container-logs] výstupu příkazu zobrazte skript:

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

Tento příklad ukazuje výstup odeslaný do STDOUT skript. Kontejnerizované úlohy, ale může místo toho zapisovat jejich výstup do trvalého úložiště pro pozdější načtení. Například [sdílenou složku Azure](container-instances-mounting-azure-files-volume.md).

## <a name="configure-containers-at-runtime"></a>Konfigurace kontejnery za běhu

Když vytvoříte kontejner instance, můžete nastavit jeho **proměnné prostředí**, stejně jako zadejte vlastní **příkazového řádku** provést při spuštění kontejneru. Pomocí těchto nastavení v úlohy batch můžete připravit každý kontejner s konkrétní úlohy konfigurace.

## <a name="environment-variables"></a>Proměnné prostředí

Nastavení proměnných prostředí v vašeho kontejneru zajistit dynamické konfigurace aplikace nebo skriptu, spusťte kontejner. Toto je podobná `--env` argument příkazového řádku k `docker run`.

Například můžete změnit chování skript v kontejneru příklad tak, že zadáte následující proměnné prostředí při vytváření instance kontejneru:

*NumWords*: počet odeslaných do STDOUT slov.

*MinLength*: minimální počet znaků v aplikaci word pro něj pro inventarizaci. Vyšší číslo ignoruje běžná slova jako "z" a "na".

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer2 \
    --image microsoft/aci-wordcount:latest \
    --restart-policy OnFailure \
    --environment-variables NumWords=5 MinLength=8
```

Zadáním `NumWords=5` a `MinLength=8` kontejneru proměnných prostředí, by měl zobrazit protokoly kontejneru odlišný výstup. Jakmile se zobrazí stav kontejneru *ukončeno* (použijte `az container show` a zkontrolujte jeho stav), zobrazit protokoly zobrazíte nové výstup:

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

Při vytváření instance kontejneru přepsat zaručená do bitové kopie kontejneru příkazového řádku, zadejte příkazový řádek. Toto je podobná `--entrypoint` argument příkazového řádku k `docker run`.

Například můžete mít kontejneru příklad analyzovat text jiným než *obce* zadáním jiné příkazového řádku. Skript Pythonu provedený kontejneru, *wordcount.py*, přijímá jako argument adresu URL a zpracuje této stránce obsahu místo výchozího.

Chcete-li například zjistit slova pět písmeno 3 hlavních v *Valentýne a Juliet*:

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer3 \
    --image microsoft/aci-wordcount:latest \
    --restart-policy OnFailure \
    --environment-variables NumWords=3 MinLength=5 \
    --command-line "python wordcount.py http://shakespeare.mit.edu/romeo_juliet/full.html"
```

Znovu Jakmile je kontejner *ukončeno*, zobrazte výstup zobrazením protokoly kontejneru:

```azurecli-interactive
az container logs --resource-group myResourceGroup --name mycontainer3
```

Výstup:

```bash
[('ROMEO', 177), ('JULIET', 134), ('CAPULET', 119)]
```

## <a name="next-steps"></a>Další postup

### <a name="persist-task-output"></a>Zachovat výstup úlohy

Podrobnosti o tom, jak zachovat výstup vaše kontejnery, které dokončit najdete v tématu [připojení sdílenou složku Azure s Azure kontejner instancí](container-instances-mounting-azure-files-volume.md).

<!-- LINKS - External -->
[aci-wordcount-image]: https://hub.docker.com/r/microsoft/aci-wordcount/

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container?view=azure-cli-latest#az_container_create
[az-container-logs]: /cli/azure/container?view=azure-cli-latest#az_container_logs
[az-container-show]: /cli/azure/container?view=azure-cli-latest#az_container_show
[azure-cli-install]: /cli/azure/install-azure-cli
