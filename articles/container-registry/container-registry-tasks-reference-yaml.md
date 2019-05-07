---
title: Azure Container Registry úlohy reference – YAML
description: Referenční informace pro definování úloh v YAML pro ACR úlohy, včetně vlastnosti úlohy, krok typy, vlastnosti kroku a předdefinovaných proměnných.
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: article
ms.date: 03/28/2019
ms.author: danlep
ms.openlocfilehash: d50d5bc91fbb86e5c0c3d2acc3b55c7d02c71723
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/06/2019
ms.locfileid: "65192267"
---
# <a name="acr-tasks-reference-yaml"></a>Referenční dokumentace úlohy ACR: YAML

Definice vícekrokové úlohy v úlohách ACR poskytuje zaměřené na kontejner výpočetní primitivní zaměřené na sestavování, testování a opravy chyb kontejnery. Tento článek popisuje příkazy, parametry, vlastnosti a syntaxe pro soubor YAML, které definují vícekrokových úkolů.

Tento článek obsahuje referenční informace pro vytváření vícekrokových úkolů YAML soubory pro úlohy služby ACR. Pokud byste o ni Úvod do služby ACR úkoly, naleznete v tématu [ACR úlohy přehled](container-registry-tasks-overview.md).

## <a name="acr-taskyaml-file-format"></a>Formát souboru acr task.yaml

Úlohy služby ACR podporuje deklarace vícekrokové úlohy ve standardní syntaxe YAML. V souboru YAML definovat kroků úloh. Úlohu můžete spustit ručně předáním souboru, který má [az acr spustit] [ az-acr-run] příkazu. Nebo použijte ho k vytvoření úkolu s [az acr úloha vytvoření] [ az-acr-task-create] se automaticky aktivuje na aktualizaci Git commit nebo základní image. Přestože tento článek odkazuje na `acr-task.yaml` jako souboru, který obsahuje kroky, ACR úlohy podporuje libovolný platný název souboru s [nepodporuje rozšíření](#supported-task-filename-extensions).

Na nejvyšší úrovni `acr-task.yaml` primitivních elementů jsou **vlastnosti úlohy**, **krok typy**, a **krok vlastnosti**:

* [Vlastnosti úlohy](#task-properties) platí pro všechny kroky v průběhu provádění úkolu. Existuje několik vlastností globální úloh, včetně:
  * `version`
  * `stepTimeout`
  * `workingDirectory`
* [Úloha krok typy](#task-step-types) představují typy akcí, které můžete provést v rámci úlohy. Existují tři typy kroku:
  * `build`
  * `push`
  * `cmd`
* [Vlastnosti krok úlohy](#task-step-properties) jsou parametry, které platí pro jednotlivé kroky. Existuje několik vlastností krok, včetně:
  * `startDelay`
  * `timeout`
  * `when`
  * .. .a mnoho dalších.

Základní formát `acr-task.yaml` následuje souboru, včetně některých běžných vlastnosti kroku. Při není vyčerpávající reprezentaci všech k dispozici krok vlastnosti nebo použití typu krok poskytuje rychlý přehled o formátu základní soubor.

```yml
version: # acr-task.yaml format version.
stepTimeout: # Seconds each step may take.
steps: # A collection of image or container actions.
  - build: # Equivalent to "docker build," but in a multi-tenant environment
  - push: # Push a newly built or retagged image to a registry.
    when: # Step property that defines either parallel or dependent step execution.
  - cmd: # Executes a container, supports specifying an [ENTRYPOINT] and parameters.
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

Vlastnosti úlohy se obvykle zobrazuje v horní části `acr-task.yaml` souborů a jsou globální vlastnosti, které se vztahují během úplné spuštění kroky úloh. Některé z těchto globálních vlastností mohou přepsat v rámci jednoho kroku.

| Vlastnost | Type | Nepovinné | Popis | Přepsání podporována | Výchozí hodnota |
| -------- | ---- | -------- | ----------- | ------------------ | ------------- |
| `version` | string | Ano | Verze `acr-task.yaml` sdílené, jak analyzovat služba ACR úkoly. Během úlohy služby ACR se snaží udržovat zpětnou kompatibilitu, tato hodnota umožňuje ACR úkoly pro zachování kompatibility v rámci definovaných verze. Pokud tento parametr zadán, výchozí hodnota je na nejnovější verzi. | Ne | Žádný |
| `stepTimeout` | int (sekundy) | Ano | Maximální počet sekund, po které můžete spustit krok. Pokud je zadaná vlastnost na úkol, nastaví výchozí `timeout` vlastnost všechny kroky. Pokud `timeout` je zadána vlastnost v kroku, přepíše vlastnost poskytnuté úlohou. | Ano | 600 (10 minut) |
| `workingDirectory` | string | Ano | Pracovní adresář kontejner za běhu. Pokud je zadaná vlastnost na úkol, nastaví výchozí `workingDirectory` vlastnost všechny kroky. Pokud je zadané v kroku, přepíše vlastnost poskytnuté úlohou. | Ano | `$HOME` |
| `env` | [řetězec, řetězec,...] | Ano |  Pole řetězců v: `key=value` formátu definující proměnné prostředí úkolu. Pokud je zadaná vlastnost na úkol, nastaví výchozí `env` vlastnost všechny kroky. Pokud je zadané v kroku, přepíše všechny proměnné prostředí, zděděno z úlohy. | Žádný |
| `secrets` | [tajných kódů, tajný klíč,...] | Ano | Pole [tajný kód](#secret) objekty. | Žádný |
| `networks` | [, sítě,...] | Ano | Pole [sítě](#network) objekty. | Žádný |

### <a name="secret"></a>Tajný kód

Tajného kódu objektu má následující vlastnosti.

| Vlastnost | Type | Nepovinné | Popis | Výchozí hodnota |
| -------- | ---- | -------- | ----------- | ------- |
| `id` | string | Ne | Identifikátor tajného klíče. | Žádný |
| `akv` | string | Ano | Azure Key Vaultu (AKV) Adresa URL tajného kódu. | Žádný |
| `clientID` | string | Ano | ID klienta o přiřazení uživatele spravované identity pro prostředky Azure. | Žádný |

### <a name="network"></a>síť

Objekt sítě má následující vlastnosti.

| Vlastnost | Type | Nepovinné | Popis | Výchozí hodnota |
| -------- | ---- | -------- | ----------- | ------- | 
| `name` | string | Ne | Název sítě. | Žádný |
| `driver` | string | Ano | Ovladač při správě sítě. | Žádný |
| `ipv6` | bool | Ano | Určuje, zda je povoleno sítě IPv6. | `false` |
| `skipCreation` | bool | Ano | Určuje, zda se mají přeskočit vytvoření sítě. | `false` |
| `isDefault` | bool | Ano | Určuje, zda je síť výchozí síť s Azure Container Registry | `false` |

## <a name="task-step-types"></a>Krok typy úloh

Úlohy služby ACR podporuje tři typy kroku. Každý typ kroku podporuje několik vlastností, najdete v části pro každý typ kroku.

| Typ kroku | Popis |
| --------- | ----------- |
| [`build`](#build) | Sestavení image kontejneru pomocí dobře známých `docker build` syntaxe. |
| [`push`](#push) | Spustí `docker push` nově vytvořené nebo retagged imagí do registru kontejneru. Podporovány jsou služba Azure Container Registry, jiných privátních registrů a veřejného Docker Hubu. |
| [`cmd`](#cmd) | Spuštění kontejneru jako příkaz s parametry předané do tohoto kontejneru `[ENTRYPOINT]`. `cmd` Typ kroku podporuje parametry jako `env`, `detach`a další známé `docker run` možnosti povolení jednotek a funkční testování s kontejneru souběžných spuštění příkazu. |

## <a name="build"></a>Sestavení

Sestavení image kontejneru. `build` Odpovídající typ představuje více tenantů a bezpečnější způsob spuštění `docker build` v cloudu jako první třídy jednoduchého typu.

### <a name="syntax-build"></a>Syntaxe: sestavení

```yml
version: v1.0.0
steps:
  - [build]: -t [imageName]:[tag] -f [Dockerfile] [context]
    [property]: [value]
```

`build` Typ kroku podporuje parametry v následující tabulce. `build` Typ kroku také podporuje všechny možnosti sestavení [sestavení dockeru](https://docs.docker.com/engine/reference/commandline/build/) příkazu, jako například `--build-arg` k nastavení proměnných čas sestavení.

| Parametr | Popis | Nepovinné |
| --------- | ----------- | :-------: |
| `-t` &#124; `--image` | Určuje plně kvalifikovaný `image:tag` sestavené Image.<br /><br />Jako bitové kopie mohou být použity pro vnitřní úloha ověření, jako jsou například funkční testy, ne všechny bitové kopie vyžadují `push` do registru. Však k instanci image v rámci provádění úloh, bitovou kopii potřebuje název, který má odkazovat.<br /><br />Na rozdíl od `az acr build`, spouštění úloh ACR neposkytuje výchozí nabízených oznámení chování. S úlohami služby ACR předpokládá scénář výchozí možnost sestavení, ověření a potom nasdílejte image. Zobrazit [nabízených](#push) jak volitelně nabízených integrované Image. | Ano |
| `-f` &#124; `--file` | Určuje soubor Dockerfile předán `docker build`. Pokud není zadán, výchozí soubor Dockerfile v kořenovém adresáři kontextu považován za. K určení souboru Dockerfile, předejte název souboru kořeni kontextu. | Ano |
| `context` | Kořenový adresář předaný `docker build`. Kořenový adresář každého úkolu je nastavený na sdílené [workingDirectory](#task-step-properties)a obsahuje kořenový přidružené Git naklonovaném adresáři. | Ne |

### <a name="properties-build"></a>Vlastnosti: sestavení

`build` Typ kroku podporuje následující vlastnosti. Podrobnější informace o těchto vlastností v [vlastnosti krok úlohy](#task-step-properties) části tohoto článku.

| | | |
| -------- | ---- | -------- |
| `detach` | bool | Nepovinné |
| `disableWorkingDirectoryOverride` | bool | Nepovinné |
| `entryPoint` | string | Nepovinné |
| `env` | [řetězec, řetězec,...] | Nepovinné |
| `expose` | [řetězec, řetězec,...] | Nepovinné |
| `id` | string | Nepovinné |
| `ignoreErrors` | bool | Nepovinné |
| `isolation` | string | Nepovinné |
| `keep` | bool | Nepovinné |
| `network` | objekt | Nepovinné |
| `ports` | [řetězec, řetězec,...] | Nepovinné |
| `pull` | bool | Nepovinné |
| `repeat` | int | Nepovinné |
| `retries` | int | Nepovinné |
| `retryDelay` | int (sekundy) | Nepovinné |
| `secret` | objekt | Nepovinné |
| `startDelay` | int (sekundy) | Nepovinné |
| `timeout` | int (sekundy) | Nepovinné |
| `when` | [řetězec, řetězec,...] | Nepovinné |
| `workingDirectory` | string | Nepovinné |

### <a name="examples-build"></a>Příklady: sestavení

#### <a name="build-image---context-in-root"></a>Sestavení image - kontextu v kořenovém adresáři

```azurecli
az acr run -f build-hello-world.yaml https://github.com/AzureCR/acr-tasks-sample.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/build-hello-world.yaml -->
[!code-yml[task](~/acr-tasks/build-hello-world.yaml)]

#### <a name="build-image---context-in-subdirectory"></a>Sestavení image - kontextu v podadresáři

```yml
version: v1.0.0
steps:
  - build: -t {{.Run.Registry}}/hello-world -f hello-world.dockerfile ./subDirectory
```

## <a name="push"></a>nabízených oznámení

Vložit jeden nebo více vytvořené nebo retagged Image do registru kontejneru. Podporuje doručením (push) do privátních registrů, jako je Azure Container Registry nebo do veřejného Docker Hubu.

### <a name="syntax-push"></a>Syntaxe: nabízených oznámení

`push` Typ kroku podporuje kolekci obrázků. Syntaxe YAML kolekce podporuje vložené a vnořené formátů. Doručením (push) jedné image se obvykle vyjadřuje vložená syntaxe:

```yml
version: v1.0.0
steps:
  # Inline YAML collection syntax
  - push: ["{{.Run.Registry}}/hello-world:{{.Run.ID}}"]
```

Pro zvýšení přehlednosti syntaxí vnořené při vkládání více bitových kopií:

```yml
version: v1.0.0
steps:
  # Nested YAML collection syntax
  - push:
    - {{.Run.Registry}}/hello-world:{{.Run.ID}}
    - {{.Run.Registry}}/hello-world:latest
```

### <a name="properties-push"></a>Vlastnosti: nabízených oznámení

`push` Typ kroku podporuje následující vlastnosti. Podrobnější informace o těchto vlastností v [vlastnosti krok úlohy](#task-step-properties) části tohoto článku.

| | | |
| -------- | ---- | -------- |
| `env` | [řetězec, řetězec,...] | Nepovinné |
| `id` | string | Nepovinné |
| `ignoreErrors` | bool | Nepovinné |
| `startDelay` | int (sekundy) | Nepovinné |
| `timeout` | int (sekundy) | Nepovinné |
| `when` | [řetězec, řetězec,...] | Nepovinné |

### <a name="examples-push"></a>Příklady: nabízených oznámení

#### <a name="push-multiple-images"></a>Vložit více bitových kopií

```azurecli
az acr run -f build-push-hello-world.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/build-push-hello-world.yaml -->
[!code-yml[task](~/acr-tasks/build-push-hello-world.yaml)]

#### <a name="build-push-and-run"></a>Vytvoření, push a spuštění

```azurecli
az acr run -f build-run-hello-world.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/build-run-hello-world.yaml -->
[!code-yml[task](~/acr-tasks/build-run-hello-world.yaml)]

## <a name="cmd"></a>cmd

`cmd` Typ kroku spuštění kontejneru.

### <a name="syntax-cmd"></a>Syntaxe: cmd

```yml
version: v1.0.0
steps:
  - [cmd]: [containerImage]:[tag (optional)] [cmdParameters to the image]
```

### <a name="properties-cmd"></a>Vlastnosti: cmd

`cmd` Typ kroku podporuje následující vlastnosti:

| | | |
| -------- | ---- | -------- |
| `detach` | bool | Nepovinné |
| `disableWorkingDirectoryOverride` | bool | Nepovinné |
| `entryPoint` | string | Nepovinné |
| `env` | [řetězec, řetězec,...] | Nepovinné |
| `expose` | [řetězec, řetězec,...] | Nepovinné |
| `id` | string | Nepovinné |
| `ignoreErrors` | bool | Nepovinné |
| `isolation` | string | Nepovinné |
| `keep` | bool | Nepovinné |
| `network` | objekt | Nepovinné |
| `ports` | [řetězec, řetězec,...] | Nepovinné |
| `pull` | bool | Nepovinné |
| `repeat` | int | Nepovinné |
| `retries` | int | Nepovinné |
| `retryDelay` | int (sekundy) | Nepovinné |
| `secret` | objekt | Nepovinné |
| `startDelay` | int (sekundy) | Nepovinné |
| `timeout` | int (sekundy) | Nepovinné |
| `when` | [řetězec, řetězec,...] | Nepovinné |
| `workingDirectory` | string | Nepovinné |

Podrobnosti těchto vlastností můžete najít [vlastnosti krok úlohy](#task-step-properties) části tohoto článku.

### <a name="examples-cmd"></a>Příklady: cmd

#### <a name="run-hello-world-image"></a>Spuštění image hello world

Tento příkaz spustí `hello-world.yaml` úkolů soubor, který odkazuje [hello-world](https://hub.docker.com/_/hello-world/) image v Docker Hubu.

```azurecli
az acr run -f hello-world.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/hello-world.yaml -->
[!code-yml[task](~/acr-tasks/hello-world.yaml)]

#### <a name="run-bash-image-and-echo-hello-world"></a>Spusťte prostředí bash image a odezvu "hello world"

Tento příkaz spustí `bash-echo.yaml` úkolů soubor, který odkazuje [bash](https://hub.docker.com/_/bash/) image v Docker Hubu.

```azurecli
az acr run -f bash-echo.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/bash-echo.yaml -->
[!code-yml[task](~/acr-tasks/bash-echo.yaml)]

#### <a name="run-specific-bash-image-tag"></a>Spustit konkrétní bash značky obrázku

Chcete-li spustit verzi konkrétní image, zadejte značky v `cmd`.

Tento příkaz spustí `bash-echo-3.yaml` úkolů soubor, který odkazuje [bash: 3.0](https://hub.docker.com/_/bash/) image v Docker Hubu.

```azurecli
az acr run -f bash-echo-3.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/bash-echo-3.yaml -->
[!code-yml[task](~/acr-tasks/bash-echo-3.yaml)]

#### <a name="run-custom-images"></a>Spuštění vlastní Image

`cmd` Odkazuje typ kroku obrázků s využitím standardu `docker run` formátu. Bitové kopie není uvedena registru se předpokládá, že pocházejí z docker.io. Předchozí příklad můžou být reprezentované stejně jako:

```yml
version: v1.0.0
steps:
  - cmd: docker.io/bash:3.0 echo hello world
```

S použitím standardu `docker run` obrázku referenční konvence `cmd` imagí můžete spustit z jakéhokoli privátního registru nebo veřejného Docker Hubu. Pokud už odkazuje na Image ve stejné registru, ve kterém je spuštěn úkol ACR, není nutné zadat všechny přihlašovací údaje registru.

* Spuštění image, která je ze služby Azure container registry

    Nahraďte `[myregistry]` s názvem vašeho registru:

    ```yml
    version: v1.0.0
    steps:
        - cmd: [myregistry].azurecr.io/bash:3.0 echo hello world
    ```

* Zobecněte registru odkaz s proměnnou spuštění

    Místo pevného kódování název vašeho registru ve `acr-task.yaml` soubor, můžete si je větší přenositelnost pomocí [spustit proměnná](#run-variables). `Run.Registry` Proměnné rozbalí za běhu, abyste názvem registru, ve kterém je spuštěn úkol.

    K zobecnění předchozího úkolu, který bude fungovat v jakékoli Azure container registry, odkazovat [Run.Registry](#runregistry) proměnné v názvu image:

    ```yml
    version: v1.0.0
    steps:
      - cmd: {{.Run.Registry}}/bash:3.0 echo hello world
    ```

## <a name="task-step-properties"></a>Vlastnosti krok úlohy

Každý typ kroku podporuje několik vlastností, které jsou vhodné pro její typ. Následující tabulka definuje všechny vlastnosti k dispozici krok. Ne všechny typy krok podporují všechny vlastnosti. Které z těchto vlastností jsou dostupné pro každý typ kroku najdete v tématu [cmd](#cmd), [sestavení](#build), a [nabízených](#push) krok oddíly typu odkazu.

| Vlastnost | Type | Nepovinné | Popis | Výchozí hodnota |
| -------- | ---- | -------- | ----------- | ------- |
| `detach` | bool | Ano | Určuje, zda by měl kontejneru odpojit při spuštění. | `false` |
| `disableWorkingDirectoryOverride` | bool | Ano | Zda chcete zakázat `workingDirectory` přepsat funkci. Toto použijte v kombinaci s `workingDirectory` mít plnou kontrolu nad kontejneru pracovní adresář. | `false` |
| `entryPoint` | string | Ano | Přepsání `[ENTRYPOINT]` o krok kontejneru. | Žádný |
| `env` | [řetězec, řetězec,...] | Ano | Pole řetězců v: `key=value` formátu definující proměnné prostředí pro krok. | Žádný |
| `expose` | [řetězec, řetězec,...] | Ano | Pole porty, které jsou přístupné z kontejneru. |  Žádný |
| [`id`](#example-id) | string | Ano | Jednoznačně identifikuje krok v rámci úlohy. Další kroky v úloze může odkazovat krok `id`, například pro kontrolu se závislosti `when`.<br /><br />`id` Je zároveň názvem spuštěný kontejner. Procesy v jiných kontejnerů v úloze může odkazovat `id` jako název hostitele DNS nebo pro přístup k jeho s protokoly dockeru [id], třeba. | `acb_step_%d`, kde `%d` je index 0 od kroku shora dolů v souboru YAML |
| `ignoreErrors` | bool | Ano | Zda se má označit krok jako úspěšný bez ohledu na to, zda došlo k chybě během spuštění kontejneru. | `false` |
| `isolation` | string | Ano | Úroveň izolace kontejneru. | `default` |
| `keep` | bool | Ano | Určuje, zda se uchovávají po spuštění kontejneru na krok. | `false` |
| `network` | objekt | Ano | Identifikuje síť, ve kterém se kontejner spustí. | Žádný |
| `ports` | [řetězec, řetězec,...] | Ano | Pole porty, které se publikují v kontejneru do hostitele. |  Žádný |
| `pull` | bool | Ano | Určuje, zda vynutit kontejneru o přijetí změn před provedením zabránili jakékoli chování ukládání do mezipaměti. | `false` |
| `privileged` | bool | Ano | Určuje, zda spuštění kontejneru v privilegovaném režimu. | `false` |
| `repeat` | int | Ano | Počet opakovaných pokusů opakovat spuštění kontejneru. | 0 |
| `retries` | int | Ano | Počet opakování pokusu o selhání spuštění kontejneru. Má pouze pokus opakovat Pokud zbývá nenulový ukončovací kód kontejneru. | 0 |
| `retryDelay` | int (sekundy) | Ano | Zpoždění v sekundách mezi opakování pokusů o spouštění kontejneru. | 0 |
| `secret` | objekt | Ano | Určuje tajný kód trezoru klíčů Azure nebo spravovanou identitu pro prostředky Azure. | Žádný |
| `startDelay` | int (sekundy) | Ano | Počet sekund bude trvat zpoždění spuštění kontejneru. | 0 |
| `timeout` | int (sekundy) | Ano | Maximální počet sekund před ukončuje se dá provádět krok. | 600 |
| [`when`](#example-when) | [řetězec, řetězec,...] | Ano | Nakonfiguruje pro krok závislost na jeden nebo více dalších kroků v úloze. | Žádný |
| `user` | string | Ano | Uživatelské jméno nebo UID kontejneru | Žádný |
| `workingDirectory` | string | Ano | Nastaví pracovní adresář pro krok. Ve výchozím nastavení vytvoří úkoly ACR kořenový adresář jako pracovní adresář. Ale pokud sestavení má několik kroků, dřívějších krocích můžete sdílet artefakty se pozdější kroky zadáním stejného pracovního adresáře. | `$HOME` |

### <a name="examples-task-step-properties"></a>Příklady: Vlastnosti krok úlohy

#### <a name="example-id"></a>Příklad: id

Vytvořte dvě bitové kopie, vytváření instancí bitovou kopii funkčního testu. Každý krok je identifikován jedinečný `id` které odkazují na další kroky v úloze v jejich `when` vlastnost.

```azurecli
az acr run -f when-parallel-dependent.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/when-parallel-dependent.yaml -->
[!code-yml[task](~/acr-tasks/when-parallel-dependent.yaml)]

#### <a name="example-when"></a>Příklad: Pokud

`when` Vlastnost určuje o krok závislost na další kroky v rámci úkolu. Podporuje dvě hodnoty parametrů:

* `when: ["-"]` -Označuje žádná závislost na další kroky. Krok zadání `when: ["-"]` provádění začne okamžitě a umožňuje spuštění souběžných kroku.
* `when: ["id1", "id2"]` -Znamená kroku je závislá na kroky, `id` "ID 1" a `id` "s ID 2". Tento krok nebude provedeno, dokud dokončit kroky "ID 1" a "s ID 2".

Pokud `when` není zadané v kroku, tento krok je závislá na dokončení předchozího kroku v `acr-task.yaml` souboru.

Krok sekvenční provádění bez `when`:

```azurecli
az acr run -f when-sequential-default.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/when-sequential-default.yaml -->
[!code-yml[task](~/acr-tasks/when-sequential-default.yaml)]

Krok sekvenční provádění s `when`:

```azurecli
az acr run -f when-sequential-id.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/when-sequential-id.yaml -->
[!code-yml[task](~/acr-tasks/when-sequential-id.yaml)]

Paralelní bitové kopie sestavení:

```azurecli
az acr run -f when-parallel.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/when-parallel.yaml -->
[!code-yml[task](~/acr-tasks/when-parallel.yaml)]

Paralelní sestavení image a závislé testování:

```azurecli
az acr run -f when-parallel-dependent.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/when-parallel-dependent.yaml -->
[!code-yml[task](~/acr-tasks/when-parallel-dependent.yaml)]

## <a name="run-variables"></a>Spustit proměnné

Úlohy služby ACR obsahuje výchozí sadu proměnných, které jsou k dispozici kroky úkolů při jejich spuštění. Tyto proměnné je přístupný ve formátu `{{.Run.VariableName}}`, kde `VariableName` je jedním z následujících akcí:

* `Run.ID`
* `Run.Registry`
* `Run.Date`
* `Run.Commit`
* `Run.Branch`

### <a name="runid"></a>Run.ID

Každé spuštění prostřednictvím `az acr run`, nebo aktivační události na základě spuštění úlohy vytvořené prostřednictvím `az acr task create` mít jedinečné ID. Toto ID představuje spuštění se spouští.

Obvykle se používá pro jedinečné tagování obrázku:

```yml
version: v1.0.0
steps:
    - build: -t {{.Run.Registry}}/hello-world:{{.Run.ID}} .
```

### <a name="runregistry"></a>Run.Registry

Plně kvalifikovaný název registru. Obvykle používá k odkazování obecně registru, ve kterém je spuštěn úkol.

```yml
version: v1.0.0
steps:
  - build: -t {{.Run.Registry}}/hello-world:{{.Run.ID}} .
```

### <a name="rundate"></a>Run.Date

Aktuální čas UTC začátku spuštění.

### <a name="runcommit"></a>Run.Commit

Úlohy spouštěné potvrzení pro úložiště GitHub, identifikátor potvrzení.

### <a name="runbranch"></a>Run.Branch

Úlohy spouštěné potvrzení pro úložiště GitHub, název větve.

## <a name="next-steps"></a>Další postup

Přehled vícekrokových úkolů, najdete v článku [spuštění několika kroky sestavení, testování a opravu úkoly v úlohách ACR](container-registry-tasks-multi-step.md).

Pro sestavení jedním krokem, najdete v článku [ACR úlohy přehled](container-registry-tasks-overview.md).

<!-- IMAGES -->

<!-- LINKS - External -->
[acr-tasks]: https://github.com/Azure-Samples/acr-tasks

<!-- LINKS - Internal -->
[az-acr-run]: /cli/azure/acr#az-acr-run
[az-acr-task-create]: /cli/azure/acr/task#az-acr-task-create
[az-configure]: /cli/azure/reference-index#az-configure
