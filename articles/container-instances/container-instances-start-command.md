---
title: Přepsat parametr entryPoint v instanci kontejneru
description: Nastavení příkazového řádku pro přepsání vstupního bodu v imagi kontejneru při nasazení instance kontejneru Azure
ms.topic: article
ms.date: 04/15/2019
ms.openlocfilehash: 23221de3dc91c37c2e6fb96489539d3954efcd87
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "86169625"
---
# <a name="set-the-command-line-in-a-container-instance-to-override-the-default-command-line-operation"></a>Nastavení příkazového řádku v instanci kontejneru pro přepsání výchozí operace příkazového řádku

Když vytváříte instanci kontejneru, můžete volitelně zadat příkaz pro přepsání výchozích instrukcí příkazového řádku vloženými do image kontejneru. Toto chování je podobné jako `--entrypoint` argument příkazového řádku pro `docker run` .

Podobně jako při nastavení [proměnných prostředí](container-instances-environment-variables.md) pro instance kontejnerů je zadání počátečního příkazového řádku užitečné pro dávkové úlohy, které je potřeba k dynamické přípravě každého kontejneru s konfigurací specifickou pro úlohy.

## <a name="command-line-guidelines"></a>Pokyny příkazového řádku

* Ve výchozím nastavení příkazový řádek určuje *jeden proces, který začíná bez prostředí* v kontejneru. Například příkazový řádek může spustit skript v jazyce Python nebo spustitelný soubor. Proces může určit další parametry nebo argumenty.

* Pokud chcete spustit více příkazů, spusťte příkazový řádek nastavením prostředí prostředí, které je podporované v operačním systému kontejneru. Příklady:

  |Operační systém  |Výchozí prostředí  |
  |---------|---------|
  |Ubuntu     |   `/bin/bash`      |
  |Alpine     |   `/bin/sh`      |
  |Windows     |    `cmd`     |

  Pokud chcete zkombinovat několik příkazů, které se mají spustit v posloupnosti, postupujte podle konvencí prostředí.

* V závislosti na konfiguraci kontejneru možná budete muset nastavit úplnou cestu ke spustitelnému souboru nebo argumentům příkazového řádku.

* Nastavte vhodné [zásady restartování](container-instances-restart-policy.md) pro instanci kontejneru v závislosti na tom, zda příkazový řádek určuje dlouhodobě běžící úlohu nebo úlohu spuštění jednou. Například zásada restartování systému `Never` nebo `OnFailure` se doporučuje pro úlohu spuštění jednou. 

* Pokud potřebujete informace o výchozí sadě EntryPoint v imagi kontejneru, použijte příkaz [Docker image prověřit](https://docs.docker.com/engine/reference/commandline/image_inspect/) .

## <a name="command-line-syntax"></a>Syntaxe příkazového řádku

Syntaxe příkazového řádku se liší v závislosti na rozhraní API nebo nástroji Azure, které jste použili k vytvoření instancí. Pokud zadáte prostředí prostředí, sledujte také konvence syntaxe příkazu Shell.

* [AZ Container Create][az-container-create] Command: předejte řetězec s `--command-line` parametrem. Příklad: `--command-line "python myscript.py arg1 arg2"` ).

* [New-AzureRmContainerGroup][new-azurermcontainergroup] Azure PowerShell rutina: předejte řetězec s `-Command` parametrem. Příklad: `-Command "echo hello"`.

* Azure Portal: v vlastnosti **přepsání příkazu** konfigurace kontejneru Zadejte čárkami oddělený seznam řetězců bez uvozovek. Příklad: `python, myscript.py, arg1, arg2` ). 

* Správce prostředků šablonu nebo soubor YAML nebo jednu ze sad Azure SDK: zadejte vlastnost příkazového řádku jako pole řetězců. Příklad: pole JSON `["python", "myscript.py", "arg1", "arg2"]` v šabloně správce prostředků. 

  Pokud jste obeznámeni se syntaxí [souboru Dockerfile](https://docs.docker.com/engine/reference/builder/) , je tento formát podobný jako ve formě *exec* instrukce cmd.

### <a name="examples"></a>Příklady

|    |  Azure CLI   | Portál | Šablona | 
| ---- | ---- | --- | --- |
| **Jeden příkaz** | `--command-line "python myscript.py arg1 arg2"` | **Přepsání příkazu**: `python, myscript.py, arg1, arg2` | `"command": ["python", "myscript.py", "arg1", "arg2"]` |
| **Více příkazů** | `--command-line "/bin/bash -c 'mkdir test; touch test/myfile; tail -f /dev/null'"` |**Přepsání příkazu**: `/bin/bash, -c, mkdir test; touch test/myfile; tail -f /dev/null` | `"command": ["/bin/bash", "-c", "mkdir test; touch test/myfile; tail -f /dev/null"]` |

## <a name="azure-cli-example"></a>Příklad rozhraní příkazového řádku Azure

Jako příklad můžete změnit chování image kontejneru [Microsoft/ACI-WORDCOUNT][aci-wordcount] , která analyzuje text v Shakespeare *Hamlet* , aby našli nejčastěji se vyskytující slova. Místo analýzy *Hamlet*můžete nastavit příkazový řádek, který odkazuje na jiný zdroj textu.

Chcete-li zobrazit výstup kontejneru [Microsoft/ACI-WORDCOUNT][aci-wordcount] při analýze výchozího textu, spusťte jej pomocí následujícího příkazu [AZ Container Create][az-container-create] . Není zadán žádný příkazový řádek pro spuštění, takže se spustí výchozí příkaz kontejneru. Pro ilustraci tento příklad nastaví [proměnné prostředí](container-instances-environment-variables.md) tak, aby našli první 3 slova, která jsou aspoň pět písmen dlouhá:

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer1 \
    --image mcr.microsoft.com/azuredocs/aci-wordcount:latest \
    --environment-variables NumWords=3 MinLength=5 \
    --restart-policy OnFailure
```

Jakmile se stav kontejneru zobrazí jako *ukončeno* (ke kontrole stavu použijte příkaz [AZ Container show][az-container-show] ), zobrazí se výstup protokolu příkazem [AZ Container logs][az-container-logs] .

```azurecli-interactive
az container logs --resource-group myResourceGroup --name mycontainer1
```

Výstup:

```console
[('HAMLET', 386), ('HORATIO', 127), ('CLAUDIUS', 120)]
```

Nyní vytvořte druhý vzorový kontejner pro analýzu jiného textu zadáním jiného příkazového řádku. Skript Pythonu spouštěný kontejnerem, *WORDCOUNT.py*, přijímá jako argument adresu URL a zpracovává obsah stránky místo výchozího.

Například k určení prvních 3 slov, která jsou nejméně pět znaků dlouhá v *Romeo a Juliet*:

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer2 \
    --image mcr.microsoft.com/azuredocs/aci-wordcount:latest \
    --restart-policy OnFailure \
    --environment-variables NumWords=3 MinLength=5 \
    --command-line "python wordcount.py http://shakespeare.mit.edu/romeo_juliet/full.html"
```

Jakmile se kontejner *ukončí*, zobrazte výstup zobrazením protokolů kontejneru:

```azurecli-interactive
az container logs --resource-group myResourceGroup --name mycontainer2
```

Výstup:

```console
[('ROMEO', 177), ('JULIET', 134), ('CAPULET', 119)]
```

## <a name="next-steps"></a>Další kroky

Scénáře založené na úlohách, například dávkové zpracování velké datové sady s několika kontejnery, můžou využívat vlastní příkazové řádky za běhu. Další informace o spuštění kontejnerů založených na úlohách najdete v tématu [spuštění kontejnerových úloh pomocí zásad restartování](container-instances-restart-policy.md).

<!-- LINKS - External -->
[aci-wordcount]: https://hub.docker.com/_/microsoft-azuredocs-aci-wordcount

<!-- LINKS Internal -->
[az-container-create]: /cli/azure/container#az-container-create
[az-container-logs]: /cli/azure/container#az-container-logs
[az-container-show]: /cli/azure/container#az-container-show
[new-azurermcontainergroup]: /powershell/module/azurerm.containerinstance/new-azurermcontainergroup
[portal]: https://portal.azure.com
