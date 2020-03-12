---
title: Vyprázdnit značky a manifesty
description: Pomocí příkazu vyčistit můžete odstranit více značek a manifestů z registru kontejneru Azure na základě stáří a filtru značek a volitelně naplánovat operace vyprázdnění.
ms.topic: article
ms.date: 08/14/2019
ms.openlocfilehash: f9d86b628bdd0ce0db3067b02a47517d8aadcba3
ms.sourcegitcommit: 20429bc76342f9d365b1ad9fb8acc390a671d61e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/11/2020
ms.locfileid: "79087340"
---
# <a name="automatically-purge-images-from-an-azure-container-registry"></a>Automatické mazání imagí z Azure Container Registry

Když použijete službu Azure Container Registry jako součást pracovního postupu vývoje, registr může rychle vyplnit obrázky nebo jiné artefakty, které se po krátké době nevyžadují. Možná budete chtít odstranit všechny značky, které jsou starší než určitá doba trvání nebo odpovídají zadanému filtru názvů. V tomto článku se seznámíte s příkazem `acr purge`, který můžete spustit jako na vyžádání nebo na [plánovaném](container-registry-tasks-scheduled.md) úkolu ACR, který je možné rychle odstranit. 

Příkaz `acr purge` je aktuálně distribuován ve veřejné imagi kontejneru (`mcr.microsoft.com/acr/acr-cli:0.1`) sestavený ze zdrojového kódu v úložišti [ACR-CLI](https://github.com/Azure/acr-cli) v GitHubu.

Příklady úloh ACR v tomto článku můžete spustit pomocí Azure Cloud Shell nebo místní instalace Azure CLI. Pokud ho chcete používat místně, je potřeba verze 2.0.69 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI][azure-cli-install]. 

> [!IMPORTANT]
> Tato funkce je aktuálně ve verzi Preview. Verze Preview vám zpřístupňujeme pod podmínkou, že budete souhlasit s [dodatečnými podmínkami použití][terms-of-use]. Některé aspekty této funkce se můžou před zveřejněním změnit.

> [!WARNING]
> Použití příkazu `acr purge` se zvýšenou opatrností – data odstraněná Image je možné obnovit. Pokud máte systémy, které vyžádají image podle výtahu manifestu (na rozdíl od názvu image), neměli byste vyprázdnit netagované image. Odstraněním netagovaných imagí znemožníte těmto systémům navrácení imagí z registru. Místo toho, aby se vybral manifest, zvažte přijetí *jedinečného schématu označování* , což je [doporučený osvědčený postup](container-registry-image-tag-version.md).

Pokud chcete odstranit jednotlivé značky obrázků nebo manifesty pomocí příkazů rozhraní příkazového řádku Azure, přečtěte si téma [odstranění imagí kontejneru v Azure Container Registry](container-registry-delete.md).

## <a name="use-the-purge-command"></a>Použití příkazu vyčistit

Příkaz `acr purge` Container odstraní image podle značky v úložišti, které odpovídají filtru názvů a které jsou starší než zadaná doba trvání. Ve výchozím nastavení jsou odstraněny pouze odkazy na značky, nikoli základní [manifesty](container-registry-concepts.md#manifest) a data vrstev. Příkaz má možnost také odstranit manifesty. 

> [!NOTE]
> `acr purge` neodstraní značku obrázku nebo úložiště, kde je atribut `write-enabled` nastaven na `false`. Informace najdete v tématu [uzamknutí image kontejneru ve službě Azure Container Registry](container-registry-image-lock.md).

`acr purge` je navržená tak, aby se spouštěla jako příkaz kontejneru v [úloze ACR](container-registry-tasks-overview.md), aby se automaticky ověřovala s registrem, ve kterém je úloha spuštěná, a provádí akce. Příklady úloh v tomto článku používají [alias](container-registry-tasks-reference-yaml.md#aliases) příkazu `acr purge` místo úplného příkazu image kontejneru.

Při spuštění `acr purge`zadejte alespoň následující:

* `--filter` – úložiště a *regulární výraz* pro filtrování značek v úložišti. Příklady: `--filter "hello-world:.*"` odpovídá všem značkám v úložišti `hello-world` a `--filter "hello-world:^1.*"` odpovídá značkám začínajícím `1`. Předejte několik parametrů `--filter` pro vyprázdnění více úložišť.
* `--ago` – [řetězec doby trvání](https://golang.org/pkg/time/) ve stylu na cestách, který určuje dobu, po kterou se obrázky odstraňují. Doba trvání se skládá z jednoho nebo více desetinných čísel, z nichž každá má příponu jednotky. Platné časové jednotky zahrnují "d" pro dny, "h" pro hodiny a "m" pro minuty. `--ago 2d3h6m` například vybere všechny filtrované obrázky naposledy změněné více než 2 dny, 3 hodiny a před 6 minutami a `--ago 1.5h` vybere obrázky naposledy změněné více než 1,5 hodin.

`acr purge` podporuje několik volitelných parametrů. V příkladech v tomto článku se používají následující dvě:

* `--untagged` – určuje, že se odstraní manifesty, které nemají přidružené značky (*netagované manifesty*).
* `--dry-run` – určuje, že nejsou odstraněna žádná data, ale výstup je stejný, jako kdyby byl příkaz spuštěn bez tohoto příznaku. Tento parametr je vhodný pro testování příkazu vyčistit, aby se zajistilo, že nechtěně neodstraní data, která máte v úmyslu zachovat.

Pro další parametry spusťte `acr purge --help`. 

`acr purge` podporuje další funkce příkazů úloh ACR, včetně [proměnných pro spuštění](container-registry-tasks-reference-yaml.md#run-variables) a [protokolů spuštění úloh](container-registry-tasks-logs.md) , které jsou streamované a také uložené pro pozdější načtení.

### <a name="run-in-an-on-demand-task"></a>Spuštění v úloze na vyžádání

V následujícím příkladu se pomocí příkazu [AZ ACR Run][az-acr-run] spustí na vyžádání příkaz `acr purge`. Tento příklad odstraní všechny značky a manifesty obrázků v úložišti `hello-world` v *myregistry* , které byly změněny před 1 dnem. Příkaz kontejneru se předává pomocí proměnné prostředí. Úloha se spustí bez zdrojového kontextu.

```azurecli
# Environment variable for container command line
PURGE_CMD="acr purge --filter 'hello-world:.*' \
  --untagged --ago 1d"

az acr run \
  --cmd "$PURGE_CMD" \
  --registry myregistry \
  /dev/null
```

### <a name="run-in-a-scheduled-task"></a>Spustit v naplánovaném úkolu

Následující příklad používá příkaz [AZ ACR Task Create][az-acr-task-create] k vytvoření denního [naplánovaného ACR úlohy](container-registry-tasks-scheduled.md). Úkol vyprázdní značky v úložišti `hello-world` před více než 7 dny. Příkaz kontejneru se předává pomocí proměnné prostředí. Úloha se spustí bez zdrojového kontextu.

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

Spuštěním příkazu [AZ ACR Task show][az-acr-task-show] zobrazíte, že je nakonfigurovaná aktivační událost časovače.

### <a name="purge-large-numbers-of-tags-and-manifests"></a>Vyprázdnit velký počet značek a manifestů

Vymazání velkého počtu značek a manifestů může trvat několik minut nebo i delší dobu. Pro vyprázdnění tisíců značek a manifestů může být nutné spustit příkaz delší než výchozí časový limit 600 sekund pro úlohu na vyžádání nebo 3600 sekund pro naplánovanou úlohu. Pokud dojde k překročení časového limitu, odstraní se jenom podmnožina značek a manifestů. Chcete-li zajistit, aby bylo vyčištění velkého rozsahu dokončeno, předejte parametr `--timeout`, aby se hodnota zvýšila. 

Například následující úloha na vyžádání nastaví časový limit 3600 sekund (1 hodina):

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

## <a name="example-scheduled-purge-of-multiple-repositories-in-a-registry"></a>Příklad: naplánované vyprázdnění více úložišť v registru

Tento příklad vás provede použitím `acr purge` k pravidelnému vyčištění více úložišť v registru. Můžete mít například vývojový kanál, který vloží obrázky do `samples/devimage1` a `samples/devimage2` úložišť. Pravidelně importujete vývojové image do produkčního úložiště pro vaše nasazení, takže už nebudete potřebovat vývojové image. Každý týden vymažete `samples/devimage1` a `samples/devimage2` úložiště, a to v přípravě na práci na nadcházející týden.

### <a name="preview-the-purge"></a>Náhled vyprázdnění

Před odstraněním dat doporučujeme spustit úlohu mazání na vyžádání pomocí parametru `--dry-run`. Tato možnost umožňuje zobrazit značky a manifesty, které příkaz vyprázdní, aniž byste museli odstranit žádná data. 

V následujícím příkladu filtr v každém úložišti vybere všechny značky. Parametr `--ago 0d` odpovídá obrázkům všech stáří v úložištích, které odpovídají filtrům. V případě potřeby upravte kritéria výběru. Parametr `--untagged` označuje, že se kromě značek mají odstranit i manifesty. Příkaz Container se předává příkazu [AZ ACR Run][az-acr-run] pomocí proměnné prostředí.

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

Zkontrolujte výstup příkazu, abyste viděli značky a manifesty, které odpovídají parametrům výběru. Vzhledem k tomu, že je příkaz spuštěný s `--dry-run`, neodstraňují se žádná data.

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

Po ověření suchého běhu Vytvořte naplánovanou úlohu pro automatizaci mazání. Následující příklad naplánuje týdenní úkol v neděli v 1:00 UTC a spustí předchozí příkaz Vyčištění:

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

Spuštěním příkazu [AZ ACR Task show][az-acr-task-show] zobrazíte, že je nakonfigurovaná aktivační událost časovače.

## <a name="next-steps"></a>Další kroky

Přečtěte si o dalších možnostech, jak [Odstranit data obrázku](container-registry-delete.md) v Azure Container Registry.

Další informace o úložišti imagí najdete v tématu [úložiště imagí kontejneru v Azure Container Registry](container-registry-storage.md).

<!-- LINKS - External -->

[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - Internal -->
[azure-cli-install]: /cli/azure/install-azure-cli
[az-acr-run]: /cli/azure/acr#az-acr-run
[az-acr-task-create]: /cli/azure/acr/task#az-acr-task-create
[az-acr-task-show]: /cli/azure/acr/task#az-acr-task-show

