---
title: Vyčistit značky a manifesty
description: Pomocí příkazu pro purge odstraňte více značek a manifestů z registru kontejnerů Azure na základě stáří a filtru značek a volitelně naplánujte operace vymazání.
ms.topic: article
ms.date: 08/14/2019
ms.openlocfilehash: f9d86b628bdd0ce0db3067b02a47517d8aadcba3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79087340"
---
# <a name="automatically-purge-images-from-an-azure-container-registry"></a>Automatické vymazání ibi z registru kontejnerů Azure

Když použijete registr kontejnerů Azure jako součást pracovního postupu vývoje, může registr rychle vyplnit image nebo jiné artefakty, které nejsou potřeba po krátké době. Můžete chtít odstranit všechny značky, které jsou starší než určitá doba trvání nebo odpovídají zadanému filtru názvů. Chcete-li rychle odstranit více artefaktů, tento článek zavádí `acr purge` příkaz, který můžete spustit jako úlohu ACR na vyžádání nebo [naplánovanou.](container-registry-tasks-scheduled.md) 

Příkaz `acr purge` je aktuálně distribuován ve veřejné`mcr.microsoft.com/acr/acr-cli:0.1`image kontejneru ( ), sestavené ze zdrojového kódu v [úložišti acr-cli](https://github.com/Azure/acr-cli) v GitHubu.

Azure Cloud Shell nebo místní instalace Azure CLI spustit příklady úloh ACR v tomto článku. Pokud jej chcete používat místně, je vyžadována verze 2.0.69 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI][azure-cli-install]. 

> [!IMPORTANT]
> Tato funkce je aktuálně ve verzi Preview. Verze Preview vám zpřístupňujeme pod podmínkou, že budete souhlasit s [dodatečnými podmínkami použití][terms-of-use]. Některé aspekty této funkce se můžou před zveřejněním změnit.

> [!WARNING]
> Používejte `acr purge` příkaz opatrně – odstraněná obrazová data jsou nezdolná. Pokud máte systémy, které vytahují obrazy podle manifestu digest (na rozdíl od názvu obrázku), neměli byste očistit netagované obrázky. Odstranění neoznačených obrázků zabrání těmto systémům v vytažení bitových kopií z registru. Místo toho, aby tahání podle manifestu, zvažte přijetí jedinečné schéma *označování,* [doporučený osvědčený postup](container-registry-image-tag-version.md).

Pokud chcete odstranit jednu bitovou kopii značky nebo manifesty pomocí příkazů Azure CLI, najdete [v tématu odstranění ibi kontejnerů v registru kontejnerů Azure](container-registry-delete.md).

## <a name="use-the-purge-command"></a>Použití příkazu vyčistit

Příkaz `acr purge` kontejneru odstraní obrázky podle značek v úložišti, které odpovídají filtru názvů a které jsou starší než zadaná doba trvání. Ve výchozím nastavení se odstraňují pouze odkazy na značky, nikoli [podkladové manifesty](container-registry-concepts.md#manifest) a data vrstev. Příkaz má možnost také odstranit manifesty. 

> [!NOTE]
> `acr purge`neodstraní značku obrázku nebo úložiště, kde je `write-enabled` atribut nastaven na `false`. Další informace najdete [v tématu Uzamčení image kontejneru v registru kontejneru Azure](container-registry-image-lock.md).

`acr purge`je navržen tak, aby byl spuštěn jako příkaz kontejneru v [úloze ACR](container-registry-tasks-overview.md), takže se automaticky ověřuje v registru, ve kterém je úloha spuštěna, a provádí tam akce. Příklady úloh v tomto `acr purge` článku používají [alias](container-registry-tasks-reference-yaml.md#aliases) příkazu místo plně kvalifikovaného příkazu image kontejneru.

Při spuštění `acr purge`zadejte minimálně následující :

* `--filter`- Repozitář a *regulární výraz* pro filtrování tagů v úložišti. Příklady: `--filter "hello-world:.*"` porovnává všechny `hello-world` značky v `--filter "hello-world:^1.*"` úložišti `1`a porovnává značky začínající písmenem . Předajte více `--filter` parametrů, abyste vyčistili více úložišť.
* `--ago`- Řetězec trvání ve stylu Go označuje [dobu,](https://golang.org/pkg/time/) po jejímž uplynutí jsou obrázky odstraněny. Doba trvání se skládá z posloupnosti jednoho nebo více desetinných čísel, z nichž každé má příponu jednotky. Platné časové jednotky zahrnují "d" pro dny, "h" pro hodiny a "m" pro minuty. Vybere například všechny filtrované obrázky naposledy `--ago 2d3h6m` upravené před více než 2 `--ago 1.5h` dny, 3 hodinami a 6 minutami a vybere naposledy změněné obrázky před více než 1,5 hodinami.

`acr purge`podporuje několik volitelných parametrů. Následující dva jsou použity v příkladech v tomto článku:

* `--untagged`- Určuje, že manifesty, které nemají přidružené značky *(netagované manifesty),* jsou odstraněny.
* `--dry-run`- Určuje, že žádná data nejsou odstraněna, ale výstup je stejný, jako kdyby byl příkaz spuštěn bez tohoto příznaku. Tento parametr je užitečný pro testování příkazu vymazání, abyste se ujistili, že neúmyslně neodstraní data, která chcete zachovat.

Další parametry naleznete `acr purge --help`v běhu . 

`acr purge`podporuje další funkce příkazů ACR Tasks, včetně [spouštění proměnných](container-registry-tasks-reference-yaml.md#run-variables) a [protokolů spuštění úloh,](container-registry-tasks-logs.md) které jsou přenášeny datovým proudem a také uloženy pro pozdější načítání.

### <a name="run-in-an-on-demand-task"></a>Spuštění v úkolu na vyžádání

Následující příklad používá příkaz [az acr][az-acr-run] `acr purge` run ke spuštění příkazu na vyžádání. Tento příklad odstraní všechny image značky `hello-world` a manifesty v úložišti v *myregistry,* které byly změněny před více než 1 den. Příkaz kontejneru je předán pomocí proměnné prostředí. Úloha je spuštěna bez zdrojového kontextu.

```azurecli
# Environment variable for container command line
PURGE_CMD="acr purge --filter 'hello-world:.*' \
  --untagged --ago 1d"

az acr run \
  --cmd "$PURGE_CMD" \
  --registry myregistry \
  /dev/null
```

### <a name="run-in-a-scheduled-task"></a>Spustit v naplánované úloze

Následující příklad používá příkaz [az acr task create][az-acr-task-create] k vytvoření denní [naplánované úlohy ACR](container-registry-tasks-scheduled.md). Úkol čistky značky upravené více `hello-world` než 7 dny v úložišti. Příkaz kontejneru je předán pomocí proměnné prostředí. Úloha je spuštěna bez zdrojového kontextu.

```azurecli
# Environment variable for container command line
PURGE_CMD="acr purge --filter 'hello-world:.*' \
  --ago 7d"

az acr task create --name purgeTask \
  --cmd "$PURGE_CMD" \
  --schedule "0 0 * * *" \
  --registry myregistry \
  --context /dev/null
```

Spusťte příkaz [az acr task show,][az-acr-task-show] abyste zjistili, že je nakonfigurována aktivační událost časovače.

### <a name="purge-large-numbers-of-tags-and-manifests"></a>Vymazání velkého počtu značek a manifestů

Vymazání velkého počtu značek a manifestů může trvat několik minut nebo déle. Chcete-li vymazat tisíce značek a manifestů, příkaz může být nutné spustit déle než výchozí časový limit 600 sekund pro úlohu na vyžádání nebo 3600 sekund pro naplánovanou úlohu. Pokud je překročen časový limit, odstraní se pouze podmnožina značek a manifestů. Chcete-li zajistit, že rozsáhlé vyčištění `--timeout` je dokončena, předat parametr pro zvýšení hodnoty. 

Například následující úkol na vyžádání nastaví časový čas 3600 sekund (1 hodina):

```azurecli
# Environment variable for container command line
PURGE_CMD="acr purge --filter 'hello-world:.*' \
  --ago 1d --untagged"

az acr run \
  --cmd "$PURGE_CMD" \
  --registry myregistry \
  --timeout 3600 \
  /dev/null
```

## <a name="example-scheduled-purge-of-multiple-repositories-in-a-registry"></a>Příklad: Naplánované vymazání více úložišť v registru

Tento příklad prochází `acr purge` pomocí pravidelně vyčistit více úložišť v registru. Můžete mít například vývojový kanál, který `samples/devimage1` `samples/devimage2` odesílá obrázky do úložišť a. Pravidelně importujete vývojové bitové kopie do produkčního úložiště pro vaše nasazení, takže již vývojové bitové kopie nepotřebujete. Každý týden očistíte `samples/devimage1` a `samples/devimage2` repozitáře, v rámci přípravy na práci na příští týden.

### <a name="preview-the-purge"></a>Náhled vymazání

Před odstraněním dat doporučujeme spustit úlohu vymazání `--dry-run` na vyžádání pomocí parametru. Tato možnost umožňuje zobrazit značky a manifesty, které příkaz vyčistí, aniž by byla odstraněna všechna data. 

V následujícím příkladu filtr v každém úložišti vybere všechny značky. Parametr `--ago 0d` odpovídá obrázkům všech věkových kategorií v úložištích, které odpovídají filtrům. Upravte kritéria výběru podle potřeby pro váš scénář. Parametr `--untagged` označuje odstranění manifestů kromě značek. Příkaz kontejneru je předán příkazu [az acr run][az-acr-run] pomocí proměnné prostředí.

```azurecli
# Environment variable for container command line
PURGE_CMD="acr purge \
  --filter 'samples/devimage1:.*' --filter 'samples/devimage2:.*' \
  --ago 0d --untagged --dry-run"

az acr run \
  --cmd "$PURGE_CMD" \
  --registry myregistry \
  /dev/null
```

Zkontrolujte výstup příkazu, abyste viděli značky a manifesty, které odpovídají parametrům výběru. Vzhledem k tomu, že příkaz je spuštěn s `--dry-run`, žádná data jsou odstraněna.

Ukázkový výstup:

```console
[...]
Deleting tags for repository: samples/devimage1
myregistry.azurecr.io/samples/devimage1:232889b
myregistry.azurecr.io/samples/devimage1:a21776a
Deleting manifests for repository: samples/devimage1
myregistry.azurecr.io/samples/devimage1@sha256:81b6f9c92844bbbb5d0a101b22f7c2a7949e40f8ea90c8b3bc396879d95e788b
myregistry.azurecr.io/samples/devimage1@sha256:3ded859790e68bd02791a972ab0bae727231dc8746f233a7949e40f8ea90c8b3
Deleting tags for repository: samples/devimage2
myregistry.azurecr.io/samples/devimage2:5e788ba
myregistry.azurecr.io/samples/devimage2:f336b7c
Deleting manifests for repository: samples/devimage2
myregistry.azurecr.io/samples/devimage2@sha256:8d2527cde610e1715ad095cb12bc7ed169b60c495e5428eefdf336b7cb7c0371
myregistry.azurecr.io/samples/devimage2@sha256:ca86b078f89607bc03ded859790e68bd02791a972ab0bae727231dc8746f233a

Number of deleted tags: 4
Number of deleted manifests: 4
[...]
```

### <a name="schedule-the-purge"></a>Naplánování vyprázdnění

Po ověření průběhu nasucho vytvořte naplánovanou úlohu pro automatizaci vyprázdnění. Následující příklad naplánuje týdenní úlohu v neděli v 1:00 UTC ke spuštění předchozího příkazu purge:

```azurecli
# Environment variable for container command line
PURGE_CMD="acr purge \
  --filter 'samples/devimage1:.*' --filter 'samples/devimage2:.*' \
  --ago 0d --untagged"

az acr task create --name weeklyPurgeTask \
  --cmd "$PURGE_CMD" \
  --schedule "0 1 * * Sun" \
  --registry myregistry \
  --context /dev/null
```

Spusťte příkaz [az acr task show,][az-acr-task-show] abyste zjistili, že je nakonfigurována aktivační událost časovače.

## <a name="next-steps"></a>Další kroky

Přečtěte si o dalších možnostech [odstranění obrazových dat](container-registry-delete.md) v registru kontejnerů Azure.

Další informace o úložišti bitových bitových obrazů najdete [v tématu Úložiště bitových bitových údajů kontejneru v registru kontejnerů Azure](container-registry-storage.md).

<!-- LINKS - External -->

[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - Internal -->
[azure-cli-install]: /cli/azure/install-azure-cli
[az-acr-run]: /cli/azure/acr#az-acr-run
[az-acr-task-create]: /cli/azure/acr/task#az-acr-task-create
[az-acr-task-show]: /cli/azure/acr/task#az-acr-task-show

