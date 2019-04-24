---
title: Použijte výchozí příkazový řádek ve službě Azure Container Instances
description: Přepsat vstupního bodu, nakonfigurován v imagi kontejneru při nasazení Azure container instance
services: container-instances
author: dlepow
ms.service: container-instances
ms.topic: article
ms.date: 04/15/2019
ms.author: danlep
ms.openlocfilehash: 78136a081e52ef3f12d672d01449ce616534462e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60537645"
---
# <a name="set-the-command-line-in-a-container-instance-to-override-the-default-command-line-operation"></a>Nastavení příkazového řádku v instanci kontejneru přepsat výchozí operace příkazového řádku

Při vytváření instance kontejneru, Volitelně můžete zadejte příkaz přepsat výchozí instrukce příkazového řádku vloženými do image kontejneru. Toto chování je podobné `--entrypoint` argument příkazového řádku k `docker run`.

Nastavení, jako jsou [proměnné prostředí](container-instances-environment-variables.md) pro container instances, určení počáteční příkazového řádku je užitečné pro úlohy batch kde je nutné připravit každý kontejner dynamicky pomocí konfigurace specifické pro úlohy.

## <a name="command-line-guidelines"></a>Pokyny příkazového řádku

* Ve výchozím nastavení, určuje příkazový řádek *jeden proces, který se spustí bez prostředí* v kontejneru. Například může příkazového řádku spustit Pythonovému skriptu nebo spustitelného souboru. 

* Ke spuštění více příkazů, začít tak, že nastavíte příkazové prostředí v operačním systému kontejneru příkazového řádku (příklady: `bin/sh`, `/bin/bash`, `cmd`). Dodržují konvence prostředí kombinovat více příkazů ke spuštění v sekvenci.

* V závislosti na konfiguraci kontejneru můžete potřebovat nastavit úplná cesta k spustitelný soubor příkazového řádku nebo argumenty.

* Nastavte odpovídající [zásady restartování](container-instances-restart-policy.md) pro instance kontejneru, v závislosti na tom, jestli příkazového řádku určuje dlouho běžící úlohy nebo úlohy spouštěné jednou. Například, zásady restartování z `Never` nebo `OnFailure` se doporučuje pro úlohy spouštěné jednou. 

* Pokud potřebujete informace o entrypoint výchozí nastavení v imagi kontejneru, použijte [kontrolovat image dockeru](https://docs.docker.com/engine/reference/commandline/image_inspect/) příkazu.

## <a name="command-line-syntax"></a>Syntaxe příkazového řádku

Syntaxe příkazového řádku se liší v závislosti na rozhraní API služby Azure nebo nástroj použitý k vytvoření instance. Pokud zadáte příkazové prostředí, podívejte se také konvencí syntaxe příkazového prostředí.

* [Vytvoření kontejneru az] [ az-container-create] příkaz: Předat řetězec `--command-line` parametru. Příklad: `--command-line "python myscript.py arg1 arg2"`).

* [Nový-AzureRmContainerGroup] [ new-azurermcontainergroup] rutiny Azure Powershellu: Předat řetězec `-Command` parametru. Příklad: `-Command "echo hello"`.

* Azure portal: V **příkaz přepsání** vlastnost konfigurace kontejneru, zadejte čárkou oddělený seznam řetězců, bez uvozovek. Příklad: `python, myscript.py, arg1, arg2`). 

* Šablony Resource Manageru nebo soubor YAML nebo jednu ze sad Azure SDK: Určete vlastnost příkazového řádku jako pole řetězců. Příklad: pole JSON `["python", "myscript.py", "arg1", "arg2"]` v šabloně Resource Manageru. 

  Pokud jste obeznámeni s [soubor Dockerfile](https://docs.docker.com/engine/reference/builder/) syntaxe, tento formát je podobný *exec* formu instrukci CMD.

### <a name="examples"></a>Příklady

|    |  Azure CLI   | Portál | Šablona | 
| ---- | ---- | --- | --- |
| Jediným příkazem | `--command-line "python myscript.py arg1 arg2"` | **Příkaz přepsání**: `python, myscript.py, arg1, arg2` | `"command": ["python", "myscript.py", "arg1", "arg2"]` |
| Více příkazů | `--command-line "/bin/bash -c 'mkdir test; touch test/myfile; tail -f /dev/null'"` |**Příkaz přepsání**: `/bin/bash, -c, mkdir test; touch test/myfile; tail -f /dev/null` | `"command": ["/bin/bash", "-c", "mkdir test; touch test/myfile; tail -f /dev/null"]` |

## <a name="azure-cli-example"></a>Příklad rozhraní příkazového řádku Azure

Jako příklad změnit chování [microsoft/aci-wordcount] [ aci-wordcount] image kontejneru, která analyzuje text v prvku Shakespeare *obce* najít nejčastěji výskytu slova. Místo analýza *obce*, můžete nastavit příkazový řádek, který odkazuje na zdroj jiným textem.

Pokud chcete zobrazit výstup [microsoft/aci-wordcount] [ aci-wordcount] kontejneru analyzuje výchozí text, spusťte následující [az container vytvořit] [ az-container-create] příkazu. Žádná spuštění příkazového řádku není zadán, takže bude možné spustit příkaz výchozí kontejner. Pro ilustraci se v tomto příkladu nastaví [proměnné prostředí](container-instances-environment-variables.md) najít první 3 slova, která jsou dlouhé aspoň pět písmena:

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer1 \
    --image mcr.microsoft.com/azuredocs/aci-wordcount:latest \
    --environment-variables NumWords=3 MinLength=5 \
    --restart-policy OnFailure
```

Jakmile ukazovat stav kontejneru *ukončeno* (použijte [az container show] [ az-container-show] postup kontroly stavu), zobrazit protokol s [protokoly kontejneru az] [ az-container-logs] chcete zobrazit výstup.

```azurecli-interactive
az container logs --resource-group myResourceGroup --name mycontainer1
```

Výstup:

```console
[('HAMLET', 386), ('HORATIO', 127), ('CLAUDIUS', 120)]
```

Nyní nastavte druhý příklad kontejner k analýze jiným textovým zadáním různých příkazového řádku. Spuštění kontejneru, skript v jazyce Python *wordcount.py*, adresu URL jako argument přijímá a zpracovává tuto stránku obsahu místo výchozího.

Například k určení horní 3 slova, které jsou alespoň pět písmena dlouho v *Valentýne a Juliet*:

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer2 \
    --image mcr.microsoft.com/azuredocs/aci-wordcount:latest \
    --restart-policy OnFailure \
    --environment-variables NumWords=3 MinLength=5 \
    --command-line "python wordcount.py http://shakespeare.mit.edu/romeo_juliet/full.html"
```

Znovu Jakmile bude kontejner *ukončeno*, zobrazte výstup zobrazením protokolů kontejneru:

```azurecli-interactive
az container logs --resource-group myResourceGroup --name mycontainer2
```

Výstup:

```console
[('ROMEO', 177), ('JULIET', 134), ('CAPULET', 119)]
```

## <a name="next-steps"></a>Další postup

Scénáře založené na úlohách, jako jsou dávkové zpracování velkou datovou sadu s několika kontejnery, může přinést vlastní příkazové řádky za běhu. Další informace o spuštěné kontejnery založené na úlohách najdete v tématu [spouštění kontejnerizovaných úloh pomocí zásady restartování](container-instances-restart-policy.md).

<!-- LINKS - External -->
[aci-wordcount]: https://hub.docker.com/_/microsoft-azuredocs-aci-wordcount

<!-- LINKS Internal -->
[az-container-create]: /cli/azure/container#az-container-create
[az-container-logs]: /cli/azure/container#az-container-logs
[az-container-show]: /cli/azure/container#az-container-show
[new-azurermcontainergroup]: /powershell/module/azurerm.containerinstance/new-azurermcontainergroup
[portal]: https://portal.azure.com
