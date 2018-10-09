---
title: Azure Container Registry úlohy reference – YAML
description: Referenční informace pro definování úloh v YAML pro ACR úlohy, včetně vlastnosti úlohy, krok typy, vlastnosti kroku a předdefinovaných proměnných.
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: article
ms.date: 09/24/2018
ms.author: danlep
ms.openlocfilehash: a26b766c18ea2b14796af796abe7f36aa4fc1c66
ms.sourcegitcommit: 67abaa44871ab98770b22b29d899ff2f396bdae3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/08/2018
ms.locfileid: "48854945"
---
# <a name="acr-tasks-reference-yaml"></a>Referenční dokumentace úlohy ACR: YAML

Definice vícekrokové úlohy v úlohách ACR poskytuje zaměřené na kontejner výpočetní primitivní zaměřené na sestavování, testování a opravy chyb kontejnery. Tento článek popisuje příkazy, parametry, vlastnosti a syntaxe pro soubor YAML, které definují vícekrokových úkolů.

Tento článek obsahuje referenční informace pro vytváření vícekrokových úkolů YAML soubory pro úlohy služby ACR. Pokud byste o ni Úvod do služby ACR úkoly, naleznete v tématu [ACR úlohy přehled](container-registry-tasks-overview.md).

> [!IMPORTANT]
> Funkce vícekrokových úkolů ACR úloh je momentálně ve verzi preview. Verze Preview vám zpřístupňujeme pod podmínkou, že budete souhlasit s [dodatečnými podmínkami použití][terms-of-use]. Některé aspekty této funkce se můžou před zveřejněním změnit.

## <a name="acr-taskyaml-file-format"></a>Formát souboru acr task.yaml

Úlohy služby ACR podporuje deklarace vícekrokové úlohy ve standardní syntaxe YAML. Definování kroků úloh v souboru YAML, který můžete spustit ručně nebo automaticky aktivaci při aktualizaci Git commit nebo základní image. Přestože tento článek odkazuje na `acr-task.yaml` jako souboru, který obsahuje kroky, ACR úlohy podporuje libovolný platný název souboru s [nepodporuje rozšíření](#supported-task-filename-extensions).

Na nejvyšší úrovni `acr-task.yaml` primitivních elementů jsou **vlastnosti úlohy**, **krok typy**, a **krok vlastnosti**:

* [Vlastnosti úlohy](#task-properties) platí pro všechny kroky v průběhu provádění úkolu. Existují tři vlastnosti globální úlohy:
  * version
  * stepTimeout
  * totalTimeout
* [Úloha krok typy](#task-step-types) představují typy akcí, které můžete provést v rámci úlohy. Existují tři typy kroku:
  * Sestavení
  * nabízených oznámení
  * cmd
* [Vlastnosti krok úlohy](#task-step-properties) jsou parametry, které platí pro jednotlivé kroky. Existuje několik vlastností krok, včetně:
  * startDelay
  * timeout
  * kdy
  * .. .a mnoho dalších.

Základní formát `acr-task.yaml` následuje souboru, včetně některých běžných vlastnosti kroku. Při není vyčerpávající reprezentaci všech k dispozici krok vlastnosti nebo použití typu krok poskytuje rychlý přehled o formátu základní soubor.

```yaml
version: # acr-task.yaml format version.
stepTimeout: # Seconds each step may take.
totalTimeout: # Total seconds allowed for all steps to complete.
steps: # A collection of image or container actions.
    build: # Equivalent to "docker build," but in a multi-tenant environment
    push: # Push a newly built or retagged image to a registry.
      when: # Step property that defines either parallel or dependent step execution.
    cmd: # Executes a container, supports specifying an [ENTRYPOINT] and parameters.
      startDelay: # Step property that specifies the number of seconds to wait before starting execution.
```

### <a name="supported-task-filename-extensions"></a>Název souboru rozšíření podporovaných úloh

Úlohy služby ACR zarezervovala několik přípony názvů souborů, včetně `.yaml`, který bude zpracovávat jako soubor úloh. Všechna rozšíření *není* v následujícím seznamu se považuje za úlohami ACR souboru Dockerfile: .yaml .yml, .toml, .json, .sh, .bash, .zsh, .ps1, PS, .cmd, .bat, .ts, JS, PHP, .py, .rb, .lua

YAML je pouze formát souboru aktuálně podporuje úlohy služby ACR. Jiné přípony názvů souborů jsou vyhrazené pro budoucí podpory je to možné.

## <a name="run-the-sample-tasks"></a>Spuštění ukázkové úlohy

Existuje několik ukázkových souborů úkolů odkazováno v dalších částech tohoto článku. Ukázkové úkoly jsou ve veřejném úložišti Githubu [Azure-Samples/acr úlohy][acr-tasks]. Ji můžete spustit pomocí příkazu Azure CLI [az acr spustit][az-acr-run]. Ukázkové příkazy jsou podobné:

```azurecli
az acr run -f build-push-hello-world.yaml https://github.com/Azure-Samples/acr-tasks.git
```

Formátování ukázkové příkazy z předpokládá registru výchozí jste nakonfigurovali v rozhraní příkazového řádku Azure, takže opomenou `--registry` parametru. Ke konfiguraci výchozího registru, použijte [az konfigurace] [ az-configure] příkazů `--defaults` parametr, který přijímá `acr=REGISTRY_NAME` hodnotu.

Třeba ke konfiguraci Azure CLI s výchozí registru s názvem "myregistry":

```azurecli
az configure --defaults acr=myregistry
```

## <a name="task-properties"></a>Vlastnosti úlohy

Vlastnosti úlohy se obvykle zobrazuje v horní části `acr-task.yaml` souborů a jsou globální vlastnosti, které se vztahují během úplné spuštění úlohy. Některé z těchto globálních vlastností mohou přepsat v rámci jednoho kroku.

| Vlastnost | Typ | Nepovinné | Popis | Přepsání podporována | Výchozí hodnota |
| -------- | ---- | -------- | ----------- | ------------------ | ------------- |
| `version` | řetězec | Ne | Verze `acr-task.yaml` sdílené, jak analyzovat služba ACR úkoly. Během úlohy služby ACR se snaží udržovat zpětnou kompatibilitu, tato hodnota umožňuje ACR úkoly pro zachování kompatibility v rámci definovaných verze. | Ne | Žádný |
| `stepTimeout` | int (sekundy) | Ano | Maximální počet sekund, po které můžete spustit krok. Tato vlastnost může být přepsána v kroku nastavení v kroku [vypršení časového limitu](#timeout) vlastnost. | Ano | 600 (10 minut) |
| `totalTimeout` | int (sekundy) | Ano | Maximální počet sekund, po které může spustit úlohu. "Spustit" zahrnuje spuštění a dokončení všech kroků v úloze, ať už úspěšně nebo se nezdařilo. Taky je tiskových úloh výstup jako závislosti zjištěné image a stav provádění úlohy. | Ne | 3600 (1 hodina) |

## <a name="task-step-types"></a>Krok typy úloh

Úlohy služby ACR podporuje tři typy kroku. Každý typ kroku podporuje několik vlastností, najdete v části pro každý typ kroku.

| Typ kroku | Popis |
| --------- | ----------- |
| [`build`](#build) | Sestavení image kontejneru pomocí dobře známých `docker build` syntaxe. |
| [`push`](#push) | Spustí `docker push` nově vytvořené nebo retagged imagí do registru kontejneru. Podporovány jsou služba Azure Container Registry, jiných privátních registrů a veřejného Docker Hubu.
| [`cmd`](#cmd) | Spuštění kontejneru jako příkaz s parametry předané do tohoto kontejneru `[ENTRYPOINT]`. `cmd` Typ kroku podporuje parametry jako env, odpojení a další známé `docker run` možnosti povolení jednotek a funkční testování s kontejneru souběžných spuštění příkazu. |

## <a name="build"></a>Sestavení

Sestavení image kontejneru. `build` Odpovídající typ představuje více tenantů a bezpečnější způsob spuštění `docker build` v cloudu jako první třídy jednoduchého typu.

### <a name="syntax-build"></a>Syntaxe: sestavení

```yaml
version: 1.0-preview-1
steps:
    - [build]: -t [imageName]:[tag] -f [Dockerfile] [context]
      [property]: [value]
```

`build` Typ kroku podporuje následující parametry:

| Parametr | Popis | Nepovinné |
| --------- | ----------- | :-------: |
| `-t` &#124; `--image` | Určuje plně kvalifikovaný `image:tag` sestavené Image.<br /><br />Jako bitové kopie mohou být použity pro vnitřní úloha ověření, jako jsou například funkční testy, ne všechny bitové kopie vyžadují `push` do registru. Však k instanci image v rámci provádění úloh, bitovou kopii potřebuje název, který má odkazovat.<br /><br />Na rozdíl od `az acr build`, spouštění úloh ACR neposkytuje výchozí nabízených oznámení chování. S úlohami služby ACR předpokládá scénář výchozí možnost sestavení, ověření a potom nasdílejte image. Zobrazit [nabízených](#push) jak volitelně nabízených integrované Image. | Ano |
| `-f` &#124; `--file` | Určuje soubor Dockerfile předán `docker build`. Pokud není zadán, výchozí soubor Dockerfile v kořenovém adresáři kontextu považován za. Chcete-li určit alternativní soubor Dockerfile, předejte název souboru kořeni kontextu. | Ano |
| `context` | Kořenový adresář předaný `docker build`. Kořenový adresář každého úkolu je nastavený na sdílené [workingDirectory](#task-step-properties)a obsahuje kořenový přidružené Git naklonovaném adresáři. | Ne |

### <a name="properties-build"></a>Vlastnosti: sestavení

`build` Typ kroku podporuje tyto vlastnosti můžete najít podrobnosti o těchto vlastností [vlastnosti krok úlohy](#task-step-properties) části tohoto článku.

| | | |
| -------- | ---- | -------- |
| `detach` | BOOL | Nepovinné |
| `entryPoint` | řetězec | Nepovinné |
| `env` | [řetězec, řetězec,...] | Nepovinné |
| `id` | řetězec | Nepovinné |
| `ignoreErrors` | BOOL | Nepovinné |
| `keep` | BOOL | Nepovinné |
| `startDelay` | int (sekundy) | Nepovinné |
| `timeout` | int (sekundy) | Nepovinné |
| `when` | [řetězec, řetězec,...] | Nepovinné |
| `workingDirectory` | řetězec | Nepovinné |

### <a name="examples-build"></a>Příklady: sestavení

#### <a name="build-image---context-in-root"></a>Sestavení image - kontextu v kořenovém adresáři

```azurecli
az acr run -f build-hello-world.yaml https://github.com/AzureCR/acr-tasks-sample.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/build-hello-world.yaml --> [!code-yml[task](~/acr-tasks/build-hello-world.yaml)]

#### <a name="build-image---context-in-subdirectory"></a>Sestavení image - kontextu v podadresáři

```yaml
version: 1.0-preview-1
steps:
- build: -t {{.Run.Registry}}/hello-world -f hello-world.dockerfile ./subDirectory
```

## <a name="push"></a>nabízených oznámení

Vložit jeden nebo více vytvořené nebo retagged Image do registru kontejneru. Podporuje doručením (push) do privátních registrů, jako je Azure Container Registry nebo do veřejného Docker Hubu.

### <a name="syntax-push"></a>Syntaxe: nabízených oznámení

`push` Typ kroku podporuje kolekci obrázků. Syntaxe YAML kolekce podporuje vložené a vnořené formátů. Doručením (push) jedné image se obvykle vyjadřuje vložená syntaxe:

```yaml
version: 1.0-preview-1
steps:
  # Inline YAML collection syntax
  - push: ["{{.Run.Registry}}/hello-world:{{.Run.ID}}"]
```

Pro zvýšení přehlednosti syntaxí vnořené při vkládání více bitových kopií:

```yaml
version: 1.0-preview-1
steps:
  # Nested YAML collection syntax
  - push:
    - {{.Run.Registry}}/hello-world:{{.Run.ID}}
    - {{.Run.Registry}}/hello-world:latest
```

### <a name="properties-push"></a>Vlastnosti: nabízených oznámení

`push` Typ kroku podporuje následující vlastnosti. Podrobnosti těchto vlastností můžete najít [vlastnosti krok úlohy](#task-step-properties) části tohoto článku.

| | | |
| -------- | ---- | -------- |
| `env` | [řetězec, řetězec,...] | Nepovinné |
| `id` | řetězec | Nepovinné |
| `ignoreErrors` | BOOL | Nepovinné |
| `startDelay` | int (sekundy) | Nepovinné |
| `timeout` | int (sekundy) | Nepovinné |
| `when` | [řetězec, řetězec,...] | Nepovinné |

### <a name="examples-push"></a>Příklady: nabízených oznámení

#### <a name="push-multiple-images"></a>Vložit více bitových kopií

```azurecli
az acr run -f build-push-hello-world.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/build-push-hello-world.yaml --> [!code-yml[task](~/acr-tasks/build-push-hello-world.yaml)]

#### <a name="build-push-and-run"></a>Vytvoření, push a spuštění

```azurecli
az acr run -f build-run-hello-world.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/build-run-hello-world.yaml --> [!code-yml[task](~/acr-tasks/build-run-hello-world.yaml)]

## <a name="cmd"></a>cmd

`cmd` Typ kroku spuštění kontejneru.

### <a name="syntax-cmd"></a>Syntaxe: cmd

```yaml
version: 1.0-preview-1
steps:
    - [cmd]: [containerImage]:[tag (optional)] [cmdParameters to the image]
```

### <a name="properties-cmd"></a>Vlastnosti: cmd

`cmd` Typ kroku podporuje následující vlastnosti:

| | | |
| -------- | ---- | -------- |
| `detach` | BOOL | Nepovinné |
| `entryPoint` | řetězec | Nepovinné |
| `env` | [řetězec, řetězec,...] | Nepovinné |
| `id` | řetězec | Nepovinné |
| `ignoreErrors` | BOOL | Nepovinné |
| `keep` | BOOL | Nepovinné |
| `startDelay` | int (sekundy) | Nepovinné |
| `timeout` | int (sekundy) | Nepovinné |
| `when` | [řetězec, řetězec,...] | Nepovinné |
| `workingDirectory` | řetězec | Nepovinné |

Podrobnosti těchto vlastností můžete najít [vlastnosti krok úlohy](#task-step-properties) části tohoto článku.

### <a name="examples-cmd"></a>Příklady: cmd

#### <a name="run-hello-world-image"></a>Spuštění image hello world

Tento příkaz spustí `hello-world.yaml` úkolů soubor, který odkazuje [hello-world](https://hub.docker.com/_/hello-world/) image v Docker Hubu.

```azurecli
az acr run -f hello-world.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/hello-world.yaml --> [!code-yml[task](~/acr-tasks/hello-world.yaml)]

#### <a name="run-bash-image-and-echo-hello-world"></a>Spusťte prostředí bash image a odezvu "hello world"

Tento příkaz spustí `bash-echo.yaml` úkolů soubor, který odkazuje [bash](https://hub.docker.com/_/bash/) image v Docker Hubu.

```azurecli
az acr run -f bash-echo.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/bash-echo.yaml --> [!code-yml[task](~/acr-tasks/bash-echo.yaml)]

#### <a name="run-specific-bash-image-tag"></a>Spustit konkrétní bash značky obrázku

Chcete-li spustit verzi konkrétní image, zadejte značky v `cmd`.

Tento příkaz spustí `bash-echo-3.yaml` úkolů soubor, který odkazuje [bash: 3.0](https://hub.docker.com/_/bash/) image v Docker Hubu.

```azurecli
az acr run -f bash-echo-3.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/bash-echo-3.yaml --> [!code-yml[task](~/acr-tasks/bash-echo-3.yaml)]

#### <a name="run-custom-images"></a>Spuštění vlastní Image

`cmd` Odkazuje typ kroku obrázků s využitím standardu `docker run` formátu. Bitové kopie není uvedena registru se předpokládá, že pocházejí z docker.io. Předchozí příklad můžou být reprezentované stejně jako:

```yaml
version: 1.0-preview-1
steps:
    - cmd: docker.io/bash:3.0 echo hello world
```

S použitím standardu `docker run` obrázku referenční konvence `cmd` můžete spustit imagí, které se nacházejí v jakéhokoli privátního registru nebo veřejného Docker Hubu. Pokud už odkazuje na Image ve stejné registru, ve kterém je spuštěn úkol ACR, není nutné zadat všechny přihlašovací údaje registru.

* Spuštění image, které se nacházejí ve službě Azure container registry

    Nahraďte `[myregistry]` s názvem vašeho registru:

    ```yaml
    version: 1.0-preview-1
    steps:
        - cmd: [myregistry].azurecr.io/bash:3.0 echo hello world
    ```

* Zobecněte registru odkaz s proměnnou spuštění

    Místo pevného kódování název vašeho registru ve `acr-task.yaml` soubor, můžete si je větší přenositelnost pomocí [spustit proměnná](#run-variables). `Run.Registry` Proměnné rozbalí za běhu, abyste názvem registru, ve kterém je spuštěn úkol.

    K zobecnění předchozího úkolu, který bude fungovat v jakékoli Azure container registry, odkazovat [Run.Registry](#runregistry) proměnné v názvu image:

    ```yaml
    version: 1.0-preview-1
    steps:
        - cmd: {{.Run.Registry}}/bash:3.0 echo hello world
    ```

## <a name="task-step-properties"></a>Vlastnosti krok úlohy

Každý typ kroku podporuje několik vlastností, které jsou vhodné pro její typ. Následující tabulka definuje všechny vlastnosti k dispozici krok. Ne všechny typy krok podporují všechny vlastnosti. Které z těchto vlastností jsou dostupné pro každý typ kroku najdete v tématu [cmd](#cmd), [sestavení](#build), a [nabízených](#push) krok oddíly typu odkazu.

| Vlastnost | Typ | Nepovinné | Popis |
| -------- | ---- | -------- | ----------- |
| `detach` | BOOL | Ano | Určuje, zda by měl kontejneru odpojit při spuštění. |
| `entryPoint` | řetězec | Ano | Přepsání `[ENTRYPOINT]` o krok kontejneru. |
| `env` | [řetězec, řetězec,...] | Ano | Pole řetězců v: `key=value` formátu definující proměnné prostředí pro krok. |
| [`id`](#example-id) | řetězec | Ano | Jednoznačně identifikuje krok v rámci úlohy. Další kroky v úloze může odkazovat krok `id`, například pro kontrolu se závislosti `when`.<br /><br />`id` Je zároveň názvem spuštěný kontejner. Procesy v jiných kontejnerů v úloze může odkazovat `id` jako název hostitele DNS nebo pro přístup k jeho s protokoly dockeru [id], třeba. |
| `ignoreErrors` | BOOL | Ano | Pokud je nastavena na `true`, krok se označí jako dokončené, bez ohledu na to, zda došlo k chybě při jeho spuštění. Výchozí hodnota: `false`. |
| `keep` | BOOL | Ano | Určuje, zda se uchovávají po spuštění kontejneru na krok. |
| `startDelay` | int (sekundy) | Ano | Počet sekund bude trvat zpoždění spuštění pro krok. |
| `timeout` | int (sekundy) | Ano | Maximální počet sekund před ukončuje se dá provádět krok. |
| [`when`](#example-when) | [řetězec, řetězec,...] | Ano | Nakonfiguruje pro krok závislost na jeden nebo více dalších kroků v úloze. |
| `workingDirectory` | řetězec | Ano | Nastaví pracovní adresář pro krok. Ve výchozím nastavení vytvoří úkoly ACR kořenový adresář jako pracovní adresář. Ale pokud sestavení má několik kroků, dřívějších krocích můžete sdílet artefakty se pozdější kroky zadáním stejného pracovního adresáře. |

### <a name="examples-task-step-properties"></a>Příklady: Vlastnosti krok úlohy

#### <a name="example-id"></a>Příklad: id

Vytvořte dvě bitové kopie, vytváření instancí bitovou kopii funkčního testu. Každý krok je identifikován jedinečný `id` které odkazují na další kroky v úloze v jejich `when` vlastnost.

```azurecli
az acr run -f when-parallel-dependent.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/when-parallel-dependent.yaml --> [!code-yml[task](~/acr-tasks/when-parallel-dependent.yaml)]

#### <a name="example-when"></a>Příklad: Pokud

`when` Vlastnost určuje o krok závislost na další kroky v rámci úkolu. Podporuje dvě hodnoty parametrů:

* `when: ["-"]` -Označuje žádná závislost na další kroky. Krok zadání `when: ["-"]` provádění začne okamžitě a umožňuje spuštění souběžných kroku.
* `when: ["id1", "id2"]` -Znamená kroku je závislá na kroky, `id` "ID 1" a `id` "s ID 2". Tento krok nebude provedeno, dokud dokončit kroky "ID 1" a "s ID 2".

Pokud `when` není zadané v kroku, tento krok je závislá na dokončení předchozího kroku v `acr-task.yaml` souboru.

Krok sekvenční provádění bez `when`:

```azurecli
az acr run -f when-sequential-default.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/when-sequential-default.yaml --> [!code-yml[task](~/acr-tasks/when-sequential-default.yaml)]

Krok sekvenční provádění s `when`:

```azurecli
az acr run -f when-sequential-id.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/when-sequential-id.yaml --> [!code-yml[task](~/acr-tasks/when-sequential-id.yaml)]

Paralelní bitové kopie sestavení:

```azurecli
az acr run -f when-parallel.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/when-parallel.yaml --> [!code-yml[task](~/acr-tasks/when-parallel.yaml)]

Paralelní sestavení image a závislé testování:

```azurecli
az acr run -f when-parallel-dependent.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/when-parallel-dependent.yaml --> [!code-yml[task](~/acr-tasks/when-parallel-dependent.yaml)]

## <a name="run-variables"></a>Spustit proměnné

Úlohy služby ACR obsahuje výchozí sadu proměnných, které jsou k dispozici kroky úkolů při jejich spuštění. Tyto proměnné je přístupný ve formátu `{{.Run.VariableName}}`, kde `VariableName` je jedním z následujících akcí:

* `Run.ID`
* `Run.Registry`
* `Run.Date`

### <a name="run46id"></a>Spustit&#46;ID

Každé spuštění prostřednictvím `az acr run`, nebo aktivační události na základě spuštění úlohy vytvořené prostřednictvím `az acr task create` mít jedinečné ID. Toto ID představuje spuštění se spouští.

Obvykle se používá pro jedinečné tagování obrázku:

```yaml
version: 1.0-preview-1
steps:
    - build: -t {{.Run.Registry}}/hello-world:{{.Run.ID}} .
```

### <a name="runregistry"></a>Run.Registry

Plně kvalifikovaný název registru. Obvykle používá k odkazování obecně registru, ve kterém je spuštěn úkol.

```yaml
version: 1.0-preview-1
steps:
    - build: -t {{.Run.Registry}}/hello-world:{{.Run.ID}} .
```

### <a name="rundate"></a>Run.Date

Aktuální čas UTC začátku spuštění.

## <a name="next-steps"></a>Další postup

Přehled vícekrokových úkolů, najdete v článku [spuštění několika kroky sestavení, testování a opravu úkoly v úlohách ACR](container-registry-tasks-multi-step.md).

Pro sestavení jedním krokem, najdete v článku [ACR úlohy přehled](container-registry-tasks-overview.md).

<!-- IMAGES -->

<!-- LINKS - External -->
[acr-tasks]: https://github.com/Azure-Samples/acr-tasks
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - Internal -->
[az-acr-run]: /cli/azure/acr/run#az-acr-run
[az-configure]: /cli/azure/reference-index#az-configure