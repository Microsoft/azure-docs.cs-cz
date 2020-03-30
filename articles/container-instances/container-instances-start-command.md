---
title: Přepsat vstupní bod v instanci kontejneru
description: Nastavení příkazového řádku pro přepsání vstupního bodu v bitové kopii kontejneru při nasazení instance kontejneru Azure
ms.topic: article
ms.date: 04/15/2019
ms.openlocfilehash: d9554603f78a07fa44af51d8f39a91e1b3c39f70
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79247121"
---
# <a name="set-the-command-line-in-a-container-instance-to-override-the-default-command-line-operation"></a>Nastavení příkazového řádku v instanci kontejneru k přepsání výchozí operace příkazového řádku

Když vytvoříte instanci kontejneru, volitelně určete příkaz, který přepíše výchozí instrukce příkazového řádku vypálené do bitové kopie kontejneru. Toto chování je `--entrypoint` podobné argumentu `docker run`příkazového řádku .

Stejně jako nastavení [proměnných prostředí](container-instances-environment-variables.md) pro instance kontejneru je určení počátečního příkazového řádku užitečné pro dávkové úlohy, kde je třeba dynamicky připravit každý kontejner s konfigurací specifickou pro úlohu.

## <a name="command-line-guidelines"></a>Pokyny příkazového řádku

* Ve výchozím nastavení určuje příkazový řádek *jeden proces, který začíná bez prostředí* v kontejneru. Na příkazovém řádku může být například spuštěn skript Pythonu nebo spustitelný soubor. Proces může určit další parametry nebo argumenty.

* Chcete-li spustit více příkazů, začněte příkazový řádek nastavením prostředí, které je podporováno v operačním systému kontejneru. Příklady:

  |Operační systém  |Výchozí prostředí  |
  |---------|---------|
  |Ubuntu     |   `/bin/bash`      |
  |Alpské     |   `/bin/sh`      |
  |Windows     |    `cmd`     |

  Postupujte podle konvencí prostředí kombinovat více příkazů pro spuštění v pořadí.

* V závislosti na konfiguraci kontejneru může být nutné nastavit úplnou cestu ke spustitelnému souboru příkazového řádku nebo argumentům.

* Nastavte vhodné [zásady restartování](container-instances-restart-policy.md) pro instanci kontejneru v závislosti na tom, zda příkazový řádek určuje dlouho běžící úlohu nebo úlohu run-once. Například zásady `Never` restartování `OnFailure` nebo se doporučuje pro úlohu run-once. 

* Pokud potřebujete informace o výchozím vstupním bodu nastaveném v bitové kopii kontejneru, použijte příkaz [kontrola image dockeru.](https://docs.docker.com/engine/reference/commandline/image_inspect/)

## <a name="command-line-syntax"></a>Syntaxe příkazového řádku

Syntaxe příkazového řádku se liší v závislosti na rozhraní Azure API nebo nástroj použitý k vytvoření instancí. Pokud zadáte prostředí prostředí, dodržujte také konvence syntaxe příkazu prostředí.

* [az kontejner vytvořit][az-container-create] příkaz: Předat `--command-line` řetězec s parametrem. Příklad: `--command-line "python myscript.py arg1 arg2"`).

* [Nová skupina AzureRmContainerGroup][new-azurermcontainergroup] Rutina Prostředí Azure PowerShell: Předají řetězec s parametrem. `-Command` Příklad: `-Command "echo hello"`.

* Portál Azure: V **příkazu přepsat** vlastnost konfigurace kontejneru, zadejte seznam řetězců oddělených čárkami bez uvozovek. Příklad: `python, myscript.py, arg1, arg2`). 

* Šablona Správce prostředků nebo soubor YAML nebo jeden z sad Azure SDK: Zadejte vlastnost příkazového řádku jako pole řetězců. Příklad: pole `["python", "myscript.py", "arg1", "arg2"]` JSON v šabloně Správce prostředků. 

  Pokud jste obeznámeni s [Dockerfile](https://docs.docker.com/engine/reference/builder/) syntaxe, tento formát je podobný *exec* formulář cmd instrukce.

### <a name="examples"></a>Příklady

|    |  Azure CLI   | Portál | Šablona | 
| ---- | ---- | --- | --- |
| Jeden příkaz | `--command-line "python myscript.py arg1 arg2"` | **Přepsání příkazu**:`python, myscript.py, arg1, arg2` | `"command": ["python", "myscript.py", "arg1", "arg2"]` |
| Více příkazů | `--command-line "/bin/bash -c 'mkdir test; touch test/myfile; tail -f /dev/null'"` |**Přepsání příkazu**:`/bin/bash, -c, mkdir test; touch test/myfile; tail -f /dev/null` | `"command": ["/bin/bash", "-c", "mkdir test; touch test/myfile; tail -f /dev/null"]` |

## <a name="azure-cli-example"></a>Příklad příkazového příkazového příkazu k Azure

Jako příklad upravte chování obrázku kontejneru [microsoft/aci-wordcount,][aci-wordcount] který analyzuje text v Shakespearově *Hamletovi* a nalezne nejčastěji se vyskytující slova. Namísto analýzy *Hamleta*můžete nastavit příkazový řádek, který odkazuje na jiný zdroj textu.

Chcete-li zobrazit výstup [kontejneru Microsoft/aci-wordcount][aci-wordcount] při analýze výchozího textu, spusťte jej pomocí následujícího [příkazu az container create.][az-container-create] Není zadán žádný příkazový řádek start, takže je spuštěn výchozí příkaz kontejneru. Pro účely ilustrace tento příklad nastaví [proměnné prostředí](container-instances-environment-variables.md) najít top 3 slova, která jsou alespoň pět písmen dlouhé:

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer1 \
    --image mcr.microsoft.com/azuredocs/aci-wordcount:latest \
    --environment-variables NumWords=3 MinLength=5 \
    --restart-policy OnFailure
```

Jakmile se stav kontejneru zobrazí jako *Ukončeno* (použijte [az kontejner show][az-container-show] ke kontrole stavu), zobrazte protokol s [protokoly kontejneru az,][az-container-logs] abyste viděli výstup.

```azurecli-interactive
az container logs --resource-group myResourceGroup --name mycontainer1
```

Výstup:

```console
[('HAMLET', 386), ('HORATIO', 127), ('CLAUDIUS', 120)]
```

Nyní nastavte druhý příklad kontejneru pro analýzu jiného textu zadáním jiného příkazového řádku. Skript Pythonu spuštěný *kontejnerem, wordcount.py*, přijme adresu URL jako argument a zpracuje obsah této stránky namísto výchozího.

Chcete-li například určit 3 nejlepší slova, která mají v *Romeu a Julii*tloušit alespoň pět písmen :

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer2 \
    --image mcr.microsoft.com/azuredocs/aci-wordcount:latest \
    --restart-policy OnFailure \
    --environment-variables NumWords=3 MinLength=5 \
    --command-line "python wordcount.py http://shakespeare.mit.edu/romeo_juliet/full.html"
```

Opět platí, že jakmile je kontejner *ukončen*, zobrazit výstup zobrazením protokoly kontejneru:

```azurecli-interactive
az container logs --resource-group myResourceGroup --name mycontainer2
```

Výstup:

```console
[('ROMEO', 177), ('JULIET', 134), ('CAPULET', 119)]
```

## <a name="next-steps"></a>Další kroky

Scénáře založené na úlohách, jako je například dávkové zpracování velké datové sady s několika kontejnery, mohou využívat vlastní příkazové řádky za běhu. Další informace o spouštění kontejnerů založených na úlohách naleznete v tématu [Spuštění kontejnerizovaných úloh se zásadami restartování](container-instances-restart-policy.md).

<!-- LINKS - External -->
[aci-wordcount]: https://hub.docker.com/_/microsoft-azuredocs-aci-wordcount

<!-- LINKS Internal -->
[az-container-create]: /cli/azure/container#az-container-create
[az-container-logs]: /cli/azure/container#az-container-logs
[az-container-show]: /cli/azure/container#az-container-show
[new-azurermcontainergroup]: /powershell/module/azurerm.containerinstance/new-azurermcontainergroup
[portal]: https://portal.azure.com
