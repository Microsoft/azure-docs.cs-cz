---
title: Reference YAML – úlohy ACR
description: Odkaz pro definování úkolů v yaml pro úkoly ACR, včetně vlastností úkolů, typů kroků, vlastností kroků a předdefinovaných proměnných.
ms.topic: article
ms.date: 10/23/2019
ms.openlocfilehash: 9558f698b4a9dbca46431fc02ced6ae30de29121
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79246978"
---
# <a name="acr-tasks-reference-yaml"></a>Odkaz na úkoly ACR: YAML

Vícekroková definice úloh v úlohách ACR poskytuje výpočetní primitivní zaměřené na kontejnery zaměřené na vytváření, testování a opravy kontejnerů. Tento článek popisuje příkazy, parametry, vlastnosti a syntaxe pro soubory YAML, které definují vaše vícekrokové úlohy.

Tento článek obsahuje odkaz na vytváření vícekrokových úloh YAML souborů pro úlohy ACR. Pokud chcete seznámení s úkoly ACR, přečtěte [si přehled úkolů ACR](container-registry-tasks-overview.md).

## <a name="acr-taskyaml-file-format"></a>formát souboru acr-task.yaml

Úlohy ACR podporují deklaraci vícekrokových úloh ve standardní syntaxi YAML. Kroky úlohy definujete v souboru YAML. Úlohu pak můžete spustit ručně předáním souboru příkazu [az acr run.][az-acr-run] Nebo použijte soubor k vytvoření úlohy s [vytvořením úkolu az acr,][az-acr-task-create] který se automaticky aktivuje při potvrzení Gitu nebo aktualizaci základníbitové bitové kopie. Přestože tento článek `acr-task.yaml` odkazuje jako soubor obsahující kroky, ACR Úkoly podporuje všechny platné název souboru s [podporovanou příponou](#supported-task-filename-extensions).

Základní prvky `acr-task.yaml` nejvyšší úrovně jsou **vlastnosti úkolů**, **typy kroků**a **vlastnosti kroku**:

* [Vlastnosti úlohy](#task-properties) platí pro všechny kroky v průběhu provádění úloh. Existuje několik vlastností globálních úkolů, včetně:
  * `version`
  * `stepTimeout`
  * `workingDirectory`
* [Typy kroků úkolu](#task-step-types) představují typy akcí, které lze provést v úkolu. Existují tři typy kroků:
  * `build`
  * `push`
  * `cmd`
* [Vlastnosti kroku úkolu](#task-step-properties) jsou parametry, které se vztahují k jednotlivému kroku. Existuje několik vlastností kroku, včetně:
  * `startDelay`
  * `timeout`
  * `when`
  * ... a mnoho dalších.

Následuje základní formát `acr-task.yaml` souboru, včetně některých běžných vlastností kroku. I když není vyčerpávající reprezentace všech dostupných vlastností kroku nebo použití typu kroku, poskytuje rychlý přehled o základním formátu souboru.

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

### <a name="supported-task-filename-extensions"></a>Podporované přípony názvů souborů úloh

Úkoly ACR mají vyhrazeno několik `.yaml`přípon názvů souborů, včetně , které bude zpracovávat jako soubor úloh. Jakékoli *rozšíření,* které není v následujícím seznamu, je považováno acr úkoly za Dockerfile: .yaml, .yml, .toml, .json, .sh, .bash, .zsh, .ps1, .ps, .cmd, .bat, .ts, .js, .php, .py, .rb, .lua

YAML je jediný formát souboru, který jsou aktuálně podporovány úkoly ACR. Ostatní přípony názvu souboru jsou vyhrazeny pro případnou budoucí podporu.

## <a name="run-the-sample-tasks"></a>Spuštění ukázkových úloh

V následujících částech tohoto článku je uvedeno několik ukázkových souborů úloh. Ukázkové úlohy jsou ve veřejném úložišti GitHub, [Azure-Samples/acr-tasks][acr-tasks]. Můžete je spustit pomocí příkazu Azure CLI [az az run][az-acr-run]. Ukázkové příkazy jsou podobné:

```azurecli
az acr run -f build-push-hello-world.yaml https://github.com/Azure-Samples/acr-tasks.git
```

Formátování ukázkových příkazů předpokládá, že jste nakonfigurovali výchozí registr v rozhraní příkazového `--registry` řádku Azure, takže parametr vynechou. Chcete-li nakonfigurovat výchozí registr, použijte `--defaults` příkaz [az configure][az-configure] s parametrem, který přijímá hodnotu. `acr=REGISTRY_NAME`

Například konfigurace rozhraní příkazového příkazu Azure s výchozím registrem s názvem "myregistry":

```azurecli
az configure --defaults acr=myregistry
```

## <a name="task-properties"></a>Vlastnosti úkolu

Vlastnosti úloh se obvykle zobrazují `acr-task.yaml` v horní části souboru a jsou globální vlastnosti, které platí v průběhu úplného provedení kroků úlohy. Některé z těchto globálních vlastností lze přepsat v rámci jednotlivého kroku.

| Vlastnost | Typ | Nepovinné | Popis | Podporováno přepsání | Výchozí hodnota |
| -------- | ---- | -------- | ----------- | ------------------ | ------------- |
| `version` | řetězec | Ano | Verze souboru `acr-task.yaml` analyzovaná službou ACR Tasks. Zatímco Úkoly ACR se snaží zachovat zpětnou kompatibilitu, tato hodnota umožňuje acr úkoly udržovat kompatibilitu v rámci definované verze. Pokud není zadán, výchozí nejnovější verze. | Ne | Žádný |
| `stepTimeout` | int (v sekundách) | Ano | Maximální počet sekund, po které může být krok spuštěn. Pokud je vlastnost zadána na úkolu, `timeout` nastaví výchozí vlastnost všech kroků. Pokud `timeout` je vlastnost zadána v kroku, přepíše vlastnost poskytovanou úlohou. | Ano | 600 (10 minut) |
| `workingDirectory` | řetězec | Ano | Pracovní adresář kontejneru za běhu. Pokud je vlastnost zadána na úkolu, `workingDirectory` nastaví výchozí vlastnost všech kroků. Pokud je zadán v kroku, přepíše vlastnost poskytovanou úlohou. | Ano | `/workspace` |
| `env` | [řetězec, řetězec, ...] | Ano |  Pole řetězců ve `key=value` formátu, které definují proměnné prostředí pro úlohu. Pokud je vlastnost zadána na úkolu, `env` nastaví výchozí vlastnost všech kroků. Pokud je zadán v kroku, přepíše všechny proměnné prostředí zděděné z úkolu. | Žádný |
| `secrets` | [tajné, tajné, ...] | Ano | Pole [tajných](#secret) objektů. | Žádný |
| `networks` | [síť, síť, ...] | Ano | Pole [síťových](#network) objektů. | Žádný |

### <a name="secret"></a>Tajný kód

Tajný objekt má následující vlastnosti.

| Vlastnost | Typ | Nepovinné | Popis | Výchozí hodnota |
| -------- | ---- | -------- | ----------- | ------- |
| `id` | řetězec | Ne | Identifikátor tajného klíče. | Žádný |
| `keyvault` | řetězec | Ano | Adresa URL tajného klíče Azure Vault. | Žádný |
| `clientID` | řetězec | Ano | ID klienta [uživatelem přiřazené spravované identity](container-registry-tasks-authentication-managed-identity.md) pro prostředky Azure. | Žádný |

### <a name="network"></a>network

Síťový objekt má následující vlastnosti.

| Vlastnost | Typ | Nepovinné | Popis | Výchozí hodnota |
| -------- | ---- | -------- | ----------- | ------- | 
| `name` | řetězec | Ne | Název sítě. | Žádný |
| `driver` | řetězec | Ano | Ovladač pro správu sítě. | Žádný |
| `ipv6` | bool | Ano | Zda je povolena síť IPv6. | `false` |
| `skipCreation` | bool | Ano | Určuje, zda má být vytvoření sítě přesahono. | `false` |
| `isDefault` | bool | Ano | Jestli je síť výchozí sítí dodaný s registrem kontejnerů Azure | `false` |

## <a name="task-step-types"></a>Typy kroků úkolu

ACR Úkoly podporuje tři typy kroků. Každý typ kroku podporuje několik vlastností, které jsou podrobně popsány v části pro každý typ kroku.

| Typ kroku | Popis |
| --------- | ----------- |
| [`build`](#build) | Vytvoří image kontejneru `docker build` pomocí známé syntaxe. |
| [`push`](#push) | `docker push` Provede nově vytvořené nebo retagged image do registru kontejneru. Azure Container Registry, další privátní registry a veřejné Docker Hub jsou podporované. |
| [`cmd`](#cmd) | Spustí kontejner jako příkaz s parametry předanými `[ENTRYPOINT]`kontejneru . Typ `cmd` kroku podporuje parametry, jako je `env`, `detach`, a další známé `docker run` možnosti příkazu, povolení jednotky a funkční testování s souběžným spuštěním kontejneru. |

## <a name="build"></a>sestavení

Vytvořte image kontejneru. Typ `build` kroku představuje víceklientské, zabezpečené `docker build` prostředky pro spuštění v cloudu jako primitivní prvotřídní.

### <a name="syntax-build"></a>Syntaxe: sestavení

```yml
version: v1.1.0
steps:
  - [build]: -t [imageName]:[tag] -f [Dockerfile] [context]
    [property]: [value]
```

Typ `build` kroku podporuje parametry v následující tabulce. Typ `build` kroku také podporuje všechny možnosti sestavení [příkazu sestavení dockeru,](https://docs.docker.com/engine/reference/commandline/build/) například `--build-arg` k nastavení proměnných v době sestavení.

| Parametr | Popis | Nepovinné |
| --------- | ----------- | :-------: |
| `-t`&#124;`--image` | Definuje plně kvalifikovanou `image:tag` vytvořenou bitovou kopii.<br /><br />Jako bitové kopie mohou být použity pro ověření vnitřní `push` úlohy, jako jsou například funkční testy, ne všechny bitové kopie vyžadují do registru. Však instance bitové kopie v rámci spuštění úlohy, obraz potřebuje název odkazovat.<br /><br />Na `az acr build`rozdíl od , spuštění úlohy ACR neposkytuje výchozí chování push. S Úkoly ACR výchozí scénář předpokládá schopnost vytvářet, ověřovat a potom push image. Viz [push,](#push) jak volitelně tlačit vytvořené obrázky. | Ano |
| `-f`&#124;`--file` | Určuje soubor Dockerfile `docker build`předaná společnosti . Pokud není zadán, předpokládá se výchozí dockerfile v kořenovém adresáři kontextu. Chcete-li zadat soubor Dockerfile, předajte název souboru vzhledem ke kořenu kontextu. | Ano |
| `context` | Kořenový adresář `docker build`byl předán společnosti . Kořenový adresář jednotlivých úloh je nastaven na sdílený [pracovní adresář](#task-step-properties)a obsahuje kořenový adresář přidruženého klonovaného adresáře Gitu. | Ne |

### <a name="properties-build"></a>Vlastnosti: sestavení

Typ `build` kroku podporuje následující vlastnosti. Podrobnosti o těchto vlastnostech naleznete v tomto článku v části [Vlastnosti kroku úlohy.](#task-step-properties)

| | | |
| -------- | ---- | -------- |
| `detach` | bool | Nepovinné |
| `disableWorkingDirectoryOverride` | bool | Nepovinné |
| `entryPoint` | řetězec | Nepovinné |
| `env` | [řetězec, řetězec, ...] | Nepovinné |
| `expose` | [řetězec, řetězec, ...] | Nepovinné |
| `id` | řetězec | Nepovinné |
| `ignoreErrors` | bool | Nepovinné |
| `isolation` | řetězec | Nepovinné |
| `keep` | bool | Nepovinné |
| `network` | objekt | Nepovinné |
| `ports` | [řetězec, řetězec, ...] | Nepovinné |
| `pull` | bool | Nepovinné |
| `repeat` | int | Nepovinné |
| `retries` | int | Nepovinné |
| `retryDelay` | int (v sekundách) | Nepovinné |
| `secret` | objekt | Nepovinné |
| `startDelay` | int (v sekundách) | Nepovinné |
| `timeout` | int (v sekundách) | Nepovinné |
| `when` | [řetězec, řetězec, ...] | Nepovinné |
| `workingDirectory` | řetězec | Nepovinné |

### <a name="examples-build"></a>Příklady: sestavení

#### <a name="build-image---context-in-root"></a>Sestavení image - kontext v kořenovém adresáři

```azurecli
az acr run -f build-hello-world.yaml https://github.com/AzureCR/acr-tasks-sample.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/build-hello-world.yaml -->
[!code-yml[task](~/acr-tasks/build-hello-world.yaml)]

#### <a name="build-image---context-in-subdirectory"></a>Vytvořit image - kontext v podadresáři

```yml
version: v1.1.0
steps:
  - build: -t $Registry/hello-world -f hello-world.dockerfile ./subDirectory
```

## <a name="push"></a>push

Vysunejte jednu nebo více vytvořených nebo retagovaných bitových kopií do registru kontejnerů. Podporuje odesílání do soukromých registrů, jako je Azure Container Registry, nebo do veřejného Docker Hubu.

### <a name="syntax-push"></a>Syntaxe: push

Typ `push` kroku podporuje kolekci obrázků. Syntaxe kolekce YAML podporuje vložkové a vnořené formáty. Stisknutí jednoho obrázku je obvykle reprezentováno pomocí včleněné syntaxe:

```yml
version: v1.1.0
steps:
  # Inline YAML collection syntax
  - push: ["$Registry/hello-world:$ID"]
```

Pro větší čitelnost použijte vnořenou syntaxi při odesílání více obrázků:

```yml
version: v1.1.0
steps:
  # Nested YAML collection syntax
  - push:
    - $Registry/hello-world:$ID
    - $Registry/hello-world:latest
```

### <a name="properties-push"></a>Vlastnosti: push

Typ `push` kroku podporuje následující vlastnosti. Podrobnosti o těchto vlastnostech naleznete v tomto článku v části [Vlastnosti kroku úlohy.](#task-step-properties)

| | | |
| -------- | ---- | -------- |
| `env` | [řetězec, řetězec, ...] | Nepovinné |
| `id` | řetězec | Nepovinné |
| `ignoreErrors` | bool | Nepovinné |
| `startDelay` | int (v sekundách) | Nepovinné |
| `timeout` | int (v sekundách) | Nepovinné |
| `when` | [řetězec, řetězec, ...] | Nepovinné |

### <a name="examples-push"></a>Příklady: push

#### <a name="push-multiple-images"></a>Nabízení více obrázků

```azurecli
az acr run -f build-push-hello-world.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/build-push-hello-world.yaml -->
[!code-yml[task](~/acr-tasks/build-push-hello-world.yaml)]

#### <a name="build-push-and-run"></a>Sestavení, tlačení a spuštění

```azurecli
az acr run -f build-run-hello-world.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/build-run-hello-world.yaml -->
[!code-yml[task](~/acr-tasks/build-run-hello-world.yaml)]

## <a name="cmd"></a>Cmd

Typ `cmd` kroku spustí kontejner.

### <a name="syntax-cmd"></a>Syntaxe: cmd

```yml
version: v1.1.0
steps:
  - [cmd]: [containerImage]:[tag (optional)] [cmdParameters to the image]
```

### <a name="properties-cmd"></a>Vlastnosti: cmd

Typ `cmd` kroku podporuje následující vlastnosti:

| | | |
| -------- | ---- | -------- |
| `detach` | bool | Nepovinné |
| `disableWorkingDirectoryOverride` | bool | Nepovinné |
| `entryPoint` | řetězec | Nepovinné |
| `env` | [řetězec, řetězec, ...] | Nepovinné |
| `expose` | [řetězec, řetězec, ...] | Nepovinné |
| `id` | řetězec | Nepovinné |
| `ignoreErrors` | bool | Nepovinné |
| `isolation` | řetězec | Nepovinné |
| `keep` | bool | Nepovinné |
| `network` | objekt | Nepovinné |
| `ports` | [řetězec, řetězec, ...] | Nepovinné |
| `pull` | bool | Nepovinné |
| `repeat` | int | Nepovinné |
| `retries` | int | Nepovinné |
| `retryDelay` | int (v sekundách) | Nepovinné |
| `secret` | objekt | Nepovinné |
| `startDelay` | int (v sekundách) | Nepovinné |
| `timeout` | int (v sekundách) | Nepovinné |
| `when` | [řetězec, řetězec, ...] | Nepovinné |
| `workingDirectory` | řetězec | Nepovinné |

Podrobnosti o těchto vlastnostech naleznete v tomto článku v části [Vlastnosti kroku úlohy.](#task-step-properties)

### <a name="examples-cmd"></a>Příklady: cmd

#### <a name="run-hello-world-image"></a>Spustit obrázek hello-world

Tento příkaz provede `hello-world.yaml` soubor úlohy, který odkazuje na image [hello-world](https://hub.docker.com/_/hello-world/) v Docker Hubu.

```azurecli
az acr run -f hello-world.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/hello-world.yaml -->
[!code-yml[task](~/acr-tasks/hello-world.yaml)]

#### <a name="run-bash-image-and-echo-hello-world"></a>Spustit bash image a echo "Hello world"

Tento příkaz provede `bash-echo.yaml` soubor úlohy, který odkazuje na image [bash](https://hub.docker.com/_/bash/) v Docker Hubu.

```azurecli
az acr run -f bash-echo.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/bash-echo.yaml -->
[!code-yml[task](~/acr-tasks/bash-echo.yaml)]

#### <a name="run-specific-bash-image-tag"></a>Spustit konkrétní značku obrázku bash

Chcete-li spustit konkrétní verzi bitové `cmd`kopie, zadejte značku v .

Tento příkaz provede `bash-echo-3.yaml` soubor úlohy, který odkazuje na bitovou kopii [bash:3.0](https://hub.docker.com/_/bash/) v Docker Hubu.

```azurecli
az acr run -f bash-echo-3.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/bash-echo-3.yaml -->
[!code-yml[task](~/acr-tasks/bash-echo-3.yaml)]

#### <a name="run-custom-images"></a>Spuštění vlastních bitových kopií

Typ `cmd` kroku odkazuje na obrázky ve standardním `docker run` formátu. Předpokládá se, že bitové kopie, které nejsou předregistry, pocházejí z docker.io. Předchozí příklad by mohl být stejně reprezentován jako:

```yml
version: v1.1.0
steps:
  - cmd: docker.io/bash:3.0 echo hello world
```

Pomocí standardní `docker run` konvence odkazu `cmd` na bitové kopie můžete spouštět bitové kopie z libovolného soukromého registru nebo veřejnédocker hub. Pokud odkazujete na bitové kopie ve stejném registru, ve kterém je úloha ACR spuštěna, není nutné zadávat žádná pověření registru.

* Spusťte bitovou kopii, která je z registru kontejnerů Azure. Následující příklad předpokládá, že máte `myregistry`registr s `myimage:mytag`názvem a vlastní bitovou kopii .

    ```yml
    version: v1.1.0
    steps:
        - cmd: myregistry.azurecr.io/myimage:mytag
    ```

* Generalize odkazu na registr pomocí proměnné nebo aliasu Spustit

    Místo pevného kódování názvu registru `acr-task.yaml` v souboru můžete jej provést přenosnějšípomocí [proměnné run](#run-variables) nebo [aliasu](#aliases). Proměnná `Run.Registry` `$Registry` nebo alias se za běhu rozbalí na název registru, ve kterém je úloha spuštěna.

    Chcete-li například zobecnit předchozí úlohu tak, aby fungovala v libovolném registru kontejnerů Azure, odkazujte na proměnnou $Registry v názvu bitové kopie:

    ```yml
    version: v1.1.0
    steps:
      - cmd: $Registry/myimage:mytag
    ```

## <a name="task-step-properties"></a>Vlastnosti kroku úkolu

Každý typ kroku podporuje několik vlastností vhodných pro jeho typ. Následující tabulka definuje všechny dostupné vlastnosti kroku. Ne všechny typy kroků podporují všechny vlastnosti. Chcete-li zjistit, které z těchto vlastností jsou k dispozici pro každý typ kroku, naleznete v části odkaz na text [cmd](#cmd), [build](#build)a [push](#push) step.

| Vlastnost | Typ | Nepovinné | Popis | Výchozí hodnota |
| -------- | ---- | -------- | ----------- | ------- |
| `detach` | bool | Ano | Určuje, zda má být kontejner odpojen při spuštění. | `false` |
| `disableWorkingDirectoryOverride` | bool | Ano | Určuje, `workingDirectory` zda má být zakázána funkce přepsání. Použijte tuto kombinaci s `workingDirectory` mít úplnou kontrolu nad pracovní ho adresáře kontejneru. | `false` |
| `entryPoint` | řetězec | Ano | Přepíše `[ENTRYPOINT]` kontejner kroku. | Žádný |
| `env` | [řetězec, řetězec, ...] | Ano | Pole řetězců ve `key=value` formátu, které definují proměnné prostředí pro krok. | Žádný |
| `expose` | [řetězec, řetězec, ...] | Ano | Pole portů, které jsou vystaveny z kontejneru. |  Žádný |
| [`id`](#example-id) | řetězec | Ano | Jednoznačně identifikuje krok v rámci úkolu. Další kroky v úloze mohou `id`odkazovat na krok , `when`například pro kontrolu závislostí s .<br /><br />Je `id` také název spuštěné kontejneru. Procesy spuštěné v jiných kontejnerech `id` v úloze mohou odkazovat na jako jeho název hostitele DNS nebo pro přístup k němu pomocí protokolů dockeru [id], například. | `acb_step_%d`, `%d` kde je 0-založený index kroku shora dolů v souboru YAML |
| `ignoreErrors` | bool | Ano | Určuje, zda má být krok označen jako úspěšný bez ohledu na to, zda došlo k chybě během provádění kontejneru. | `false` |
| `isolation` | řetězec | Ano | Úroveň izolace kontejneru. | `default` |
| `keep` | bool | Ano | Zda kontejner kroku by měly být uchovávány po spuštění. | `false` |
| `network` | objekt | Ano | Identifikuje síť, ve které je kontejner spuštěn. | Žádný |
| `ports` | [řetězec, řetězec, ...] | Ano | Pole portů, které jsou publikovány z kontejneru na hostitele. |  Žádný |
| `pull` | bool | Ano | Zda vynutit vyžádat vyžádat kontejneru před jeho spuštěním, aby se zabránilo ukládání do mezipaměti chování. | `false` |
| `privileged` | bool | Ano | Určuje, zda má být kontejner spuštěn v privilegovaném režimu. | `false` |
| `repeat` | int | Ano | Počet opakování opakování provádění kontejneru. | 0 |
| `retries` | int | Ano | Počet opakování pokusu, pokud kontejner selže jeho spuštění. Opakování je pouze pokus, pokud je kód ukončení kontejneru nenulový. | 0 |
| `retryDelay` | int (v sekundách) | Ano | Zpoždění v sekundách mezi opakování spuštění kontejneru. | 0 |
| `secret` | objekt | Ano | Identifikuje tajný klíč Azure Key Vault nebo [spravovanou identitu pro prostředky Azure](container-registry-tasks-authentication-managed-identity.md). | Žádný |
| `startDelay` | int (v sekundách) | Ano | Počet sekund zpoždění spuštění kontejneru. | 0 |
| `timeout` | int (v sekundách) | Ano | Maximální počet sekund, po které může být krok před ukončením spuštěn. | 600 |
| [`when`](#example-when) | [řetězec, řetězec, ...] | Ano | Konfiguruje závislost kroku na jednom nebo více dalších krocích v rámci úkolu. | Žádný |
| `user` | řetězec | Ano | Uživatelské jméno nebo UID kontejneru | Žádný |
| `workingDirectory` | řetězec | Ano | Nastaví pracovní adresář pro krok. Ve výchozím nastavení vytvoří úkoly ACR jako pracovní adresář kořenový adresář. Pokud však sestavení obsahuje několik kroků, dřívější kroky mohou sdílet artefakty s pozdějšími kroky zadáním stejného pracovního adresáře. | `/workspace` |

### <a name="examples-task-step-properties"></a>Příklady: Vlastnosti kroku úkolu

#### <a name="example-id"></a>Příklad: id

Vytvořte dva obrazy, instance funkční testovací bitové kopie. Každý krok je identifikován `id` jedinečným, které další `when` kroky v odkazu na úkol v jejich vlastnosti.

```azurecli
az acr run -f when-parallel-dependent.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/when-parallel-dependent.yaml -->
[!code-yml[task](~/acr-tasks/when-parallel-dependent.yaml)]

#### <a name="example-when"></a>Příklad: kdy

Vlastnost `when` určuje závislost kroku na dalších krocích v rámci úkolu. Podporuje dvě hodnoty parametrů:

* `when: ["-"]`- Označuje žádnou závislost na jiných krocích. Krok určení `when: ["-"]` začne provádění okamžitě a umožňuje souběžné spuštění kroku.
* `when: ["id1", "id2"]`- Označuje, že krok `id` je závislý na `id` krocích s "id1" a "id2". Tento krok nebude proveden, dokud nebudou dokončeny kroky "id1" a "id2".

Pokud `when` není zadán v kroku, tento krok je závislá na `acr-task.yaml` dokončení předchozího kroku v souboru.

Sekvenční `when`provádění kroků bez :

```azurecli
az acr run -f when-sequential-default.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/when-sequential-default.yaml -->
[!code-yml[task](~/acr-tasks/when-sequential-default.yaml)]

Postupné provádění `when`kroků s :

```azurecli
az acr run -f when-sequential-id.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/when-sequential-id.yaml -->
[!code-yml[task](~/acr-tasks/when-sequential-id.yaml)]

Paralelní image sestavení:

```azurecli
az acr run -f when-parallel.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/when-parallel.yaml -->
[!code-yml[task](~/acr-tasks/when-parallel.yaml)]

Paralelní sestavení bitové kopie a závislé testování:

```azurecli
az acr run -f when-parallel-dependent.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/when-parallel-dependent.yaml -->
[!code-yml[task](~/acr-tasks/when-parallel-dependent.yaml)]

## <a name="run-variables"></a>Spustit proměnné

Úlohy ACR obsahují výchozí sadu proměnných, které jsou k dispozici pro kroky úlohy při jejich spuštění. K těmto proměnným lze přistupovat `{{.Run.VariableName}}`pomocí `VariableName` formátu , kde je jedna z následujících možností:

* `Run.ID`
* `Run.SharedVolume`
* `Run.Registry`
* `Run.RegistryName`
* `Run.Date`
* `Run.OS`
* `Run.Architecture`
* `Run.Commit`
* `Run.Branch`
* `Run.TaskName`

Názvy proměnných jsou obecně samovysvětlující. Podrobnosti následují pro běžně používané proměnné. Od verze `v1.1.0`YAML můžete místo většiny proměnných spustit použít zkrácený, předdefinovaný [alias úlohy.](#aliases) Například místo `{{.Run.Registry}}`, použijte `$Registry` alias.

### <a name="runid"></a>Run.ID

Každé spuštění, `az acr run`prostřednictvím , nebo aktivační `az acr task create`události na základě provádění úloh vytvořených prostřednictvím , má jedinečné ID. ID představuje spustit aktuálně spouštěné.

Obvykle se používá pro jedinečné označení obrázku:

```yml
version: v1.1.0
steps:
    - build: -t $Registry/hello-world:$ID .
```

### <a name="runregistry"></a>Spustit.Registr

Plně kvalifikovaný název serveru registru. Obvykle se používá k obecnému odkazu na registr, ve kterém je úloha spuštěna.

```yml
version: v1.1.0
steps:
  - build: -t $Registry/hello-world:$ID .
```

### <a name="runregistryname"></a>Run.Název_registru

Název registru kontejneru. Obvykle se používá v krocích úloh, které nevyžadují plně `cmd` kvalifikovaný název serveru, například kroky, které spouštějí příkazy Azure CLI v registrech.

```yml
version 1.1.0
steps:
# List repositories in registry
- cmd: az login --identity
- cmd: az acr repository list --name $RegistryName
```

### <a name="rundate"></a>Spustit.Datum

Aktuální čas UTC spuštění začalo.

### <a name="runcommit"></a>Spustit soubor Potvrzení

Pro úlohu aktivovanou potvrzením do úložiště GitHub, identifikátor potvrzení.

### <a name="runbranch"></a>Spustit.Větev

Pro úlohu aktivovanou potvrzením do úložiště GitHub název větve.

## <a name="aliases"></a>Aliasy

Od `v1.1.0`, ACR Úkoly podporuje aliasy, které jsou k dispozici pro kroky úlohy při jejich spuštění. Aliasy jsou svým konceptem podobné aliasům (příkazovým zkratkám) podporovaným v basha některých dalších příkazových prostředích. 

Pomocí aliasu můžete zadáním jediného slova spustit libovolný příkaz nebo skupinu příkazů (včetně voleb a názvů souborů).

Úlohy ACR podporují několik předdefinovaných aliasů a také vlastní aliasy, které vytvoříte.

### <a name="predefined-aliases"></a>Předdefinované aliasy

Namísto proměnných spuštění jsou k dispozici následující [aliasy úloh](#run-variables):

| Alias | Spustit proměnnou |
| ----- | ------------ |
| `ID` | `Run.ID` |
| `SharedVolume` | `Run.SharedVolume` |
| `Registry` | `Run.Registry` |
| `RegistryName` | `Run.RegistryName` |
| `Date` | `Run.Date` |
| `OS` | `Run.OS` |
| `Architecture` | `Run.Architecture` |
| `Commit` | `Run.Commit` |
| `Branch` | `Run.Branch` |

V krocích úkolů předchází `$` alias se směrnicí, jako v tomto příkladu:

```yml
version: v1.1.0
steps:
  - build: -t $Registry/hello-world:$ID -f hello-world.dockerfile .
```

### <a name="image-aliases"></a>Aliasy obrázků

Každý z následujících aliasů odkazuje na stabilní bitovou kopii v registru kontejnerů Microsoft (MCR). Můžete odkazovat na každý `cmd` z nich v části souboru úlohy bez použití směrnice.

| Alias | Image |
| ----- | ----- |
| `acr` | `mcr.microsoft.com/acr/acr-cli:0.1` |
| `az` | `mcr.microsoft.com/acr/azure-cli:a80af84` |
| `bash` | `mcr.microsoft.com/acr/bash:a80af84` |
| `curl` | `mcr.microsoft.com/acr/curl:a80af84` |

Následující ukázková úloha používá několik aliasů k [vymazání](container-registry-auto-purge.md) `samples/hello-world` značek obrázků starších než 7 dní v repo v registru spuštění:

```yml
version: v1.1.0
steps:
  - cmd: acr tag list --registry $RegistryName --repository samples/hello-world
  - cmd: acr purge --registry $RegistryName --filter samples/hello-world:.* --ago 7d
```

### <a name="custom-alias"></a>Vlastní alias

Definujte vlastní alias v souboru YAML a použijte ho tak, jak je znázorněno v následujícím příkladu. Alias může obsahovat pouze alfanumerické znaky. Výchozí direktivou pro `$` rozbalení aliasu je znak.

```yml
version: v1.1.0
alias:
  values:
    repo: myrepo
steps:
  - build: -t $Registry/$repo/hello-world:$ID -f Dockerfile .
```

Můžete propojit vzdálený nebo místní soubor YAML pro vlastní definice aliasů. Následující příklad odkazuje na soubor YAML v úložišti objektů blob Azure:

```yml
version: v1.1.0
alias:
  src:  # link to local or remote custom alias files
    - 'https://link/to/blob/remoteAliases.yml?readSasToken'
[...]
```

## <a name="next-steps"></a>Další kroky

Přehled vícekrokových úloh najdete v tématu [Spuštění úloh sestavení, testování a opravy ve více krocích v úlohách ACR](container-registry-tasks-multi-step.md).

Sestavení v jednom kroku najdete v přehledu [úkolů ACR](container-registry-tasks-overview.md).



<!-- IMAGES -->

<!-- LINKS - External -->
[acr-tasks]: https://github.com/Azure-Samples/acr-tasks

<!-- LINKS - Internal -->
[az-acr-run]: /cli/azure/acr#az-acr-run
[az-acr-task-create]: /cli/azure/acr/task#az-acr-task-create
[az-configure]: /cli/azure/reference-index#az-configure
