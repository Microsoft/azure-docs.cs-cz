---
title: Referenční informace o úlohách Azure Container Registry YAML
description: Referenční informace k definování úkolů v YAML pro úlohy ACR, včetně vlastností úloh, typů kroků, vlastností kroku a integrovaných proměnných.
services: container-registry
author: dlepow
manager: gwallace
ms.service: container-registry
ms.topic: article
ms.date: 03/28/2019
ms.author: danlep
ms.openlocfilehash: 588c4c267c16c72a7673c09a4c5726058302fccb
ms.sourcegitcommit: f5075cffb60128360a9e2e0a538a29652b409af9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/18/2019
ms.locfileid: "68310497"
---
# <a name="acr-tasks-reference-yaml"></a>Odkazy na úlohy ACR: YAML

Definice více kroků v úlohách ACR poskytuje primitivní výpočetní prostředí zaměřené na kontejnery zaměřené na vytváření, testování a opravy kontejnerů. Tento článek se zabývá příkazy, parametry, vlastnostmi a syntaxí souborů YAML, které definují úlohy s více kroky.

Tento článek obsahuje referenční informace k vytváření YAML souborů úloh s více kroky pro úlohy ACR. Pokud se chcete seznámit s ACR úkoly, přečtěte si téma [Přehled úloh ACR](container-registry-tasks-overview.md).

## <a name="acr-taskyaml-file-format"></a>ACR-Task. yaml – formát souboru

Úlohy ACR podporují deklaraci úlohy ve více krocích ve standardní syntaxi YAML. Kroky úkolu definujete v souboru YAML. Tuto úlohu pak můžete spustit ručně tak, že soubor předáte do rutiny [AZ ACR Run][az-acr-run] command. Or, use the file to create a task with [az acr task create][az-acr-task-create] , která se aktivuje automaticky v potvrzení změn Git nebo v základní aktualizaci image. I když tento článek odkazuje `acr-task.yaml` na soubor, který obsahuje kroky, úlohy ACR podporují libovolný platný název souboru s [podporovanou příponou](#supported-task-filename-extensions).

Primitivní prvky nejvyšší úrovně `acr-task.yaml` jsou **Vlastnosti úlohy**, **typy kroků**a **Vlastnosti kroku**:

* [Vlastnosti úlohy](#task-properties) se vztahují na všechny kroky v průběhu provádění úlohy. Existuje několik globálních vlastností úlohy, včetně:
  * `version`
  * `stepTimeout`
  * `workingDirectory`
* [Typy kroků úlohy](#task-step-types) reprezentují typy akcí, které lze provést v rámci úlohy. Existují tři typy kroků:
  * `build`
  * `push`
  * `cmd`
* [Vlastnosti kroku úlohy](#task-step-properties) jsou parametry, které platí pro jednotlivé kroky. K dispozici je několik vlastností kroku, včetně:
  * `startDelay`
  * `timeout`
  * `when`
  * ... a spousta dalších.

Základní formát `acr-task.yaml` souboru, včetně některých běžných vlastností kroku, je následující. I když není vyčerpávající reprezentace všech dostupných vlastností kroku nebo použití typu kroku, poskytuje rychlý přehled základního formátu souborů.

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

### <a name="supported-task-filename-extensions"></a>Podporovaná rozšíření názvu souboru úlohy

ACR úlohy rezervovaly několik přípon názvů souborů, `.yaml`včetně, které budou zpracovány jako soubor úlohy. Jakékoli rozšíření, které *není* v následujícím seznamu, je považováno za ACR úlohy souboru Dockerfile:. yaml,. yml,. toml,. JSON,. sh,. bash,. zsh,. ps1,. PS,. cmd,. bat,. TS,. js,. php,. py,.

YAML je jediný formát souboru, který je aktuálně podporován úlohami ACR. Ostatní přípony názvů souborů jsou vyhrazené pro možnou budoucí podporu.

## <a name="run-the-sample-tasks"></a>Spuštění ukázkových úloh

V následujících částech tohoto článku je odkazováno na několik ukázkových souborů úloh. Ukázkové úlohy jsou ve veřejném úložišti GitHubu, [Azure-Samples/ACR-Tasks][acr-tasks]. You can run them with the Azure CLI command [az acr run][az-acr-run]. Ukázkové příkazy jsou podobné následujícímu:

```azurecli
az acr run -f build-push-hello-world.yaml https://github.com/Azure-Samples/acr-tasks.git
```

Formátování ukázkových příkazů předpokládá, že jste v rozhraní příkazového řádku Azure nakonfigurovali výchozí registr, takže tento `--registry` parametr vynechá. Pokud chcete nakonfigurovat výchozí registr, použijte příkaz [AZ Configure][az-configure] s `--defaults` parametrem `acr=REGISTRY_NAME` , který přijímá hodnotu.

Pokud třeba chcete nakonfigurovat Azure CLI s výchozím registrem s názvem "myregistry":

```azurecli
az configure --defaults acr=myregistry
```

## <a name="task-properties"></a>Vlastnosti úlohy

Vlastnosti úlohy se obvykle zobrazují v horní části `acr-task.yaml` souboru. Jedná se o globální vlastnosti, které se použijí v celém plném provedení kroků úkolu. Některé z těchto globálních vlastností lze přepsat v rámci jednotlivého kroku.

| Vlastnost | type | volitelná, | Popis | Přepsání podporováno | Výchozí hodnota |
| -------- | ---- | -------- | ----------- | ------------------ | ------------- |
| `version` | řetězec | Ano | Verze `acr-task.yaml` souboru, jak je analyzována službou ACR Tasks. I když se ACR úlohy snaží zachovat zpětnou kompatibilitu, tato hodnota umožňuje úlohám ACR zachovat kompatibilitu v rámci definované verze. Pokud tento parametr nezadáte, použije se výchozí verze na nejnovější verzi. | Ne | Žádné |
| `stepTimeout` | int (sekundy) | Ano | Maximální počet sekund, po které může být krok spuštěn. Pokud je vlastnost určena na úkolu, nastaví výchozí `timeout` vlastnost všech kroků. Pokud je `timeout` vlastnost určena v kroku, Přepisuje vlastnost poskytnutou úlohou. | Ano | 600 (10 minut) |
| `workingDirectory` | řetězec | Ano | Pracovní adresář kontejneru během běhu. Pokud je vlastnost určena na úkolu, nastaví výchozí `workingDirectory` vlastnost všech kroků. Pokud je zadáno v kroku, přepíše vlastnost poskytnutou úlohou. | Ano | `$HOME` |
| `env` | [řetězec, řetězec,...] | Ano |  Pole řetězců ve `key=value` formátu, které definují proměnné prostředí pro úlohu. Pokud je vlastnost určena na úkolu, nastaví výchozí `env` vlastnost všech kroků. V případě zadání v kroku přepíše všechny proměnné prostředí zděděné z úlohy. | Žádné |
| `secrets` | [tajný klíč, tajný kód,...] | Ano | Pole [tajných](#secret) objektů. | Žádné |
| `networks` | [síť, síť,...] | Ano | Pole [síťových](#network) objektů. | Žádné |

### <a name="secret"></a>secret

Objekt tajného kódu má následující vlastnosti.

| Vlastnost | type | volitelná, | Popis | Výchozí hodnota |
| -------- | ---- | -------- | ----------- | ------- |
| `id` | řetězec | Ne | Identifikátor tajného klíče | Žádný |
| `keyvault` | řetězec | Ano | Adresa URL Azure Key Vault tajného klíče | Žádné |
| `clientID` | řetězec | Ano | ID klienta spravované identity přiřazené uživatelem pro prostředky Azure. | Žádné |

### <a name="network"></a>Sítě

Objekt sítě má následující vlastnosti.

| Vlastnost | type | volitelná, | Popis | Výchozí hodnota |
| -------- | ---- | -------- | ----------- | ------- | 
| `name` | řetězec | Ne | Název sítě. | Žádné |
| `driver` | řetězec | Ano | Ovladač pro správu sítě. | Žádný |
| `ipv6` | bool | Ano | Zda je povolená síť s protokolem IPv6. | `false` |
| `skipCreation` | bool | Ano | Zda se má přeskočit vytváření sítě. | `false` |
| `isDefault` | bool | Ano | Zda je síť výchozí sítí poskytovanou pomocí Azure Container Registry | `false` |

## <a name="task-step-types"></a>Typy kroků úlohy

Úlohy ACR podporují tři typy kroků. Každý typ kroku podporuje několik vlastností, které jsou popsány v části pro každý typ kroku.

| Typ kroku | Popis |
| --------- | ----------- |
| [`build`](#build) | Vytvoří Image kontejneru pomocí známé `docker build` syntaxe. |
| [`push`](#push) | Spustí v `docker push` registru kontejneru nově sestavené nebo přeoznačené image. Podporují se Azure Container Registry, jiné privátní registry a veřejné centrum Docker. |
| [`cmd`](#cmd) | Spustí kontejner jako příkaz s parametry předanými do kontejneru `[ENTRYPOINT]`. Typ kroku podporuje parametry, jako `env`, `detach`a další známé `docker run` možnosti příkazu, povolení jednotky a funkční testování pomocí souběžného spouštění kontejnerů. `cmd` |

## <a name="build"></a>budování

Sestavte image kontejneru. Typ kroku představuje víceklientské a zabezpečené způsoby spuštění `docker build` v cloudu jako primitivum první třídy. `build`

### <a name="syntax-build"></a>Syntaxe: Build

```yml
version: v1.0.0
steps:
  - [build]: -t [imageName]:[tag] -f [Dockerfile] [context]
    [property]: [value]
```

Typ `build` kroku podporuje parametry v následující tabulce. Typ kroku podporuje také všechny možnosti sestavení `--build-arg` příkazu Docker [Build](https://docs.docker.com/engine/reference/commandline/build/) , jako je například nastavení proměnných v době sestavení. `build`

| Parametr | Popis | volitelná, |
| --------- | ----------- | :-------: |
| `-t` &#124; `--image` | Definuje plně kvalifikovaný `image:tag` vzhled sestavené image.<br /><br />Jako obrázek lze použít k ověření vnitřních úkolů, jako jsou funkční testy, nikoli všechny bitové kopie, které `push` jsou požadovány pro Registry. Chcete-li však vytvořit instanci obrázku v rámci provádění úlohy, obrázek bude potřebovat název, který bude odkazovat.<br /><br />Na rozdíl `az acr build`od, spuštěné úlohy ACR neposkytují výchozí nabízené chování. U úloh ACR předpokládá výchozí scénář možnost sestavit, ověřit a potom vložit obrázek. Postup, jak volitelně vydávat sestavené image, najdete v tématu věnovaném [vložení](#push) . | Ano |
| `-f` &#124; `--file` | Určuje souboru Dockerfile předaný do `docker build`. Pokud není zadán, předpokládá se výchozí souboru Dockerfile v kořenovém adresáři kontextu. Chcete-li zadat souboru Dockerfile, předejte název souboru relativně ke kořenu kontextu. | Ano |
| `context` | Kořenový adresář předaný do `docker build`. Kořenový adresář každého úkolu je nastaven na sdílenou [WorkingDirectory](#task-step-properties)a zahrnuje kořen přidruženého klonovaného adresáře Git. | Ne |

### <a name="properties-build"></a>Vlastnosti: sestavení

Typ `build` kroku podporuje následující vlastnosti. Podrobnosti o těchto vlastnostech najdete v části [Vlastnosti kroku úlohy](#task-step-properties) v tomto článku.

| | | |
| -------- | ---- | -------- |
| `detach` | bool | volitelná, |
| `disableWorkingDirectoryOverride` | bool | volitelná, |
| `entryPoint` | řetězec | volitelná, |
| `env` | [řetězec, řetězec,...] | volitelná, |
| `expose` | [řetězec, řetězec,...] | volitelná, |
| `id` | řetězec | volitelná, |
| `ignoreErrors` | bool | volitelná, |
| `isolation` | řetězec | volitelná, |
| `keep` | bool | volitelná, |
| `network` | objekt | volitelná, |
| `ports` | [řetězec, řetězec,...] | volitelná, |
| `pull` | bool | volitelná, |
| `repeat` | int | volitelná, |
| `retries` | int | volitelná, |
| `retryDelay` | int (sekundy) | volitelná, |
| `secret` | objekt | volitelná, |
| `startDelay` | int (sekundy) | volitelná, |
| `timeout` | int (sekundy) | volitelná, |
| `when` | [řetězec, řetězec,...] | volitelná, |
| `workingDirectory` | řetězec | volitelná, |

### <a name="examples-build"></a>Příklady: sestavení

#### <a name="build-image---context-in-root"></a>Image sestavení – kontext v kořenovém adresáři

```azurecli
az acr run -f build-hello-world.yaml https://github.com/AzureCR/acr-tasks-sample.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/build-hello-world.yaml -->
[!code-yml[task](~/acr-tasks/build-hello-world.yaml)]

#### <a name="build-image---context-in-subdirectory"></a>Sestavit image – kontext v podadresáři

```yml
version: v1.0.0
steps:
  - build: -t {{.Run.Registry}}/hello-world -f hello-world.dockerfile ./subDirectory
```

## <a name="push"></a>replik

Nahrajte jednu nebo více sestavených nebo přetagovaných imagí do registru kontejneru. Podporuje doručování do privátních registrů, jako je Azure Container Registry, nebo do veřejného centra Docker.

### <a name="syntax-push"></a>Syntaxe: push

Typ `push` kroku podporuje kolekci imagí. Syntaxe kolekce YAML podporuje vložené a vnořené formáty. Vložení jednoho obrázku je obvykle znázorněno pomocí vložené syntaxe:

```yml
version: v1.0.0
steps:
  # Inline YAML collection syntax
  - push: ["{{.Run.Registry}}/hello-world:{{.Run.ID}}"]
```

Pro lepší čitelnost použijte při nahrávání více imagí vnořenou syntaxi:

```yml
version: v1.0.0
steps:
  # Nested YAML collection syntax
  - push:
    - {{.Run.Registry}}/hello-world:{{.Run.ID}}
    - {{.Run.Registry}}/hello-world:latest
```

### <a name="properties-push"></a>Vlastnosti: push

Typ `push` kroku podporuje následující vlastnosti. Podrobnosti o těchto vlastnostech najdete v části [Vlastnosti kroku úlohy](#task-step-properties) v tomto článku.

| | | |
| -------- | ---- | -------- |
| `env` | [řetězec, řetězec,...] | volitelná, |
| `id` | řetězec | volitelná, |
| `ignoreErrors` | bool | volitelná, |
| `startDelay` | int (sekundy) | volitelná, |
| `timeout` | int (sekundy) | volitelná, |
| `when` | [řetězec, řetězec,...] | volitelná, |

### <a name="examples-push"></a>Příklady: push

#### <a name="push-multiple-images"></a>Odeslat více obrázků

```azurecli
az acr run -f build-push-hello-world.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/build-push-hello-world.yaml -->
[!code-yml[task](~/acr-tasks/build-push-hello-world.yaml)]

#### <a name="build-push-and-run"></a>Sestavení, vložení a spuštění

```azurecli
az acr run -f build-run-hello-world.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/build-run-hello-world.yaml -->
[!code-yml[task](~/acr-tasks/build-run-hello-world.yaml)]

## <a name="cmd"></a>cmd

Typ `cmd` kroku spouští kontejner.

### <a name="syntax-cmd"></a>Syntaxe: cmd

```yml
version: v1.0.0
steps:
  - [cmd]: [containerImage]:[tag (optional)] [cmdParameters to the image]
```

### <a name="properties-cmd"></a>Vlastnosti: cmd

Typ `cmd` kroku podporuje následující vlastnosti:

| | | |
| -------- | ---- | -------- |
| `detach` | bool | volitelná, |
| `disableWorkingDirectoryOverride` | bool | volitelná, |
| `entryPoint` | řetězec | volitelná, |
| `env` | [řetězec, řetězec,...] | volitelná, |
| `expose` | [řetězec, řetězec,...] | volitelná, |
| `id` | řetězec | volitelná, |
| `ignoreErrors` | bool | volitelná, |
| `isolation` | řetězec | volitelná, |
| `keep` | bool | volitelná, |
| `network` | objekt | volitelná, |
| `ports` | [řetězec, řetězec,...] | volitelná, |
| `pull` | bool | volitelná, |
| `repeat` | int | volitelná, |
| `retries` | int | volitelná, |
| `retryDelay` | int (sekundy) | volitelná, |
| `secret` | objekt | volitelná, |
| `startDelay` | int (sekundy) | volitelná, |
| `timeout` | int (sekundy) | volitelná, |
| `when` | [řetězec, řetězec,...] | volitelná, |
| `workingDirectory` | řetězec | volitelná, |

Podrobnosti o těchto vlastnostech najdete v části [Vlastnosti kroku úlohy](#task-step-properties) v tomto článku.

### <a name="examples-cmd"></a>Příklady: cmd

#### <a name="run-hello-world-image"></a>Spustit obrázek Hello-World

Tento příkaz spustí `hello-world.yaml` soubor úlohy, který odkazuje na obrázek [Hello-World](https://hub.docker.com/_/hello-world/) v Docker Hub.

```azurecli
az acr run -f hello-world.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/hello-world.yaml -->
[!code-yml[task](~/acr-tasks/hello-world.yaml)]

#### <a name="run-bash-image-and-echo-hello-world"></a>Spustit image bash a echo "Hello World"

Tento příkaz spustí `bash-echo.yaml` soubor úlohy, který odkazuje na Image [bash](https://hub.docker.com/_/bash/) v Docker Hub.

```azurecli
az acr run -f bash-echo.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/bash-echo.yaml -->
[!code-yml[task](~/acr-tasks/bash-echo.yaml)]

#### <a name="run-specific-bash-image-tag"></a>Spustit konkrétní značku image bash

Chcete-li spustit konkrétní verzi bitové kopie, zadejte značku v `cmd`.

Tento příkaz spustí `bash-echo-3.yaml` soubor úlohy, který odkazuje na Image [bash: 3.0](https://hub.docker.com/_/bash/) v Docker Hub.

```azurecli
az acr run -f bash-echo-3.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/bash-echo-3.yaml -->
[!code-yml[task](~/acr-tasks/bash-echo-3.yaml)]

#### <a name="run-custom-images"></a>Spuštění vlastních imagí

Typ kroku odkazuje na Image pomocí standardního `docker run` formátu. `cmd` Pro image, které nejsou v registru, se předpokládá, že pocházejí z docker.io. Předchozí příklad může být stejně reprezentován jako:

```yml
version: v1.0.0
steps:
  - cmd: docker.io/bash:3.0 echo hello world
```

Pomocí standardní `docker run` konvence odkazů na Image může `cmd` aplikace spouštět image z libovolného privátního registru nebo z veřejného centra Docker. Pokud odkazujete na Image ve stejném registru, ve kterém je spuštěný úkol ACR, nemusíte zadávat žádné přihlašovací údaje registru.

* Spuštění image z Azure Container Registry

    Nahraďte `[myregistry]` názvem vašeho registru:

    ```yml
    version: v1.0.0
    steps:
        - cmd: [myregistry].azurecr.io/bash:3.0 echo hello world
    ```

* Generalize reference registru pomocí proměnné run

    Místo hardwarového kódování názvu registru v `acr-task.yaml` souboru můžete zvýšit jeho přenos pomocí [proměnné Run](#run-variables). Proměnná `Run.Registry` se rozbalí za běhu do názvu registru, ve kterém je úloha spuštěná.

    Pokud chcete provést generalizaci předchozí úlohy tak, aby fungovala v jakémkoli registru kontejneru Azure, odkazujte na proměnnou [Run. Registry](#runregistry) v názvu Image:

    ```yml
    version: v1.0.0
    steps:
      - cmd: {{.Run.Registry}}/bash:3.0 echo hello world
    ```

## <a name="task-step-properties"></a>Vlastnosti kroku úlohy

Každý typ kroku podporuje několik vlastností vhodných pro svůj typ. Následující tabulka definuje všechny dostupné vlastnosti kroku. Ne všechny typy kroků podporují všechny vlastnosti. Chcete-li zjistit, které z těchto vlastností jsou k dispozici pro každý typ kroku, přečtěte si referenční oddíly typu [cmd](#cmd), [Build](#build)a [push](#push) Step.

| Vlastnost | type | volitelná, | Popis | Výchozí hodnota |
| -------- | ---- | -------- | ----------- | ------- |
| `detach` | bool | Ano | Určuje, zda má být při spuštění odpojen kontejner. | `false` |
| `disableWorkingDirectoryOverride` | bool | Ano | Určuje, zda `workingDirectory` má být zakázána funkce přepsání. Toto použijte v kombinaci s `workingDirectory` nástrojem, aby měla úplnou kontrolu nad pracovním adresářem kontejneru. | `false` |
| `entryPoint` | řetězec | Ano | `[ENTRYPOINT]` Přepíše kontejner kroku. | Žádné |
| `env` | [řetězec, řetězec,...] | Ano | Pole řetězců ve `key=value` formátu, které definují proměnné prostředí pro krok | Žádný |
| `expose` | [řetězec, řetězec,...] | Ano | Pole portů, které jsou zpřístupněny z kontejneru. |  Žádný |
| [`id`](#example-id) | řetězec | Ano | Jednoznačně identifikuje krok v rámci úkolu. Další kroky v úloze můžou odkazovat na krok `id`, například pro kontrolu závislosti s nástrojem. `when`<br /><br />`id` Je také název běžícího kontejneru. Procesy běžící v jiných kontejnerech v úloze můžou `id` jako název hostitele DNS odkazovat jako na jeho název hostitele DNS nebo pro přístup k němu pomocí protokolů Docker [ID], například. | `acb_step_%d`, kde `%d` je index založený na nule v souboru YAML shora dolů |
| `ignoreErrors` | bool | Ano | Určuje, zda má být krok označen jako úspěšný bez ohledu na to, zda při provádění kontejneru došlo k chybě. | `false` |
| `isolation` | řetězec | Ano | Úroveň izolace kontejneru. | `default` |
| `keep` | bool | Ano | Určuje, zda má být kontejner kroku po provedení uchováván. | `false` |
| `network` | objekt | Ano | Identifikuje síť, ve které se kontejner spouští. | Žádné |
| `ports` | [řetězec, řetězec,...] | Ano | Pole portů, které jsou publikovány z kontejneru pro hostitele. |  Žádné |
| `pull` | bool | Ano | Určuje, zda má být před spuštěním kontejneru vynutit stažení, aby nedocházelo k chování ukládání do mezipaměti. | `false` |
| `privileged` | bool | Ano | Určuje, zda má být kontejner spuštěn v privilegovaném režimu. | `false` |
| `repeat` | int | Ano | Počet opakovaných pokusů o opakování provádění kontejneru. | 0 |
| `retries` | int | Ano | Počet opakovaných pokusů o pokus o vykonání kontejneru, pokud kontejner neprojde. Opakování se pokusí pouze v případě, že ukončovací kód kontejneru je nenulový. | 0 |
| `retryDelay` | int (sekundy) | Ano | Zpoždění v sekundách mezi opakovanými pokusy o spuštění kontejneru. | 0 |
| `secret` | objekt | Ano | Identifikuje Azure Key Vault tajný klíč nebo spravovanou identitu pro prostředky Azure. | Žádný |
| `startDelay` | int (sekundy) | Ano | Počet sekund, po který se má zpozdit spuštění kontejneru | 0 |
| `timeout` | int (sekundy) | Ano | Maximální počet sekund, po které může krok běžet, než se ukončí. | 600 |
| [`when`](#example-when) | [řetězec, řetězec,...] | Ano | Nakonfiguruje závislost kroku na jednom nebo několika dalších krocích v rámci úlohy. | Žádný |
| `user` | řetězec | Ano | Uživatelské jméno nebo UID kontejneru | Žádné |
| `workingDirectory` | řetězec | Ano | Nastaví pracovní adresář pro krok. Ve výchozím nastavení ACR úlohy vytvoří kořenový adresář jako pracovní adresář. Nicméně pokud má sestavení několik kroků, předchozí kroky mohou sdílet artefakty s pozdějšími kroky zadáním stejného pracovního adresáře. | `$HOME` |

### <a name="examples-task-step-properties"></a>Příklady: Vlastnosti kroku úlohy

#### <a name="example-id"></a>Příklad: ID

Sestavení dvou imagí, vytváření instancí bitové kopie funkčního testu. Každý krok je identifikován jedinečným `id` , který z dalších kroků v odkazu na úlohu ve své `when` vlastnosti.

```azurecli
az acr run -f when-parallel-dependent.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/when-parallel-dependent.yaml -->
[!code-yml[task](~/acr-tasks/when-parallel-dependent.yaml)]

#### <a name="example-when"></a>Příklad: when

`when` Vlastnost určuje závislost kroku na dalších krocích v rámci úlohy. Podporuje dvě hodnoty parametrů:

* `when: ["-"]`-Neurčuje žádnou závislost na jiných krocích. Zadání `when: ["-"]` kroku spustí okamžité provedení a povolí souběžné provádění kroků.
* `when: ["id1", "id2"]`– Určuje, že krok je závislý na krocích `id` s "id1" `id` a "ID 2". Tento krok se neprovede až po dokončení obou kroků "id1" a "ID 2".

Pokud `when` není v kroku zadán, je tento krok závislý na dokončení předchozího kroku `acr-task.yaml` v souboru.

Sekvenční provádění kroků bez `when`:

```azurecli
az acr run -f when-sequential-default.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/when-sequential-default.yaml -->
[!code-yml[task](~/acr-tasks/when-sequential-default.yaml)]

Sekvenční provádění kroku s `when`:

```azurecli
az acr run -f when-sequential-id.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/when-sequential-id.yaml -->
[!code-yml[task](~/acr-tasks/when-sequential-id.yaml)]

Sestavení paralelních imagí:

```azurecli
az acr run -f when-parallel.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/when-parallel.yaml -->
[!code-yml[task](~/acr-tasks/when-parallel.yaml)]

Sestavení paralelní image a závislé testování:

```azurecli
az acr run -f when-parallel-dependent.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/when-parallel-dependent.yaml -->
[!code-yml[task](~/acr-tasks/when-parallel-dependent.yaml)]

## <a name="run-variables"></a>Spustit proměnné

ACR úlohy obsahují výchozí sadu proměnných, které jsou k dispozici pro kroky úlohy při jejich spuštění. K těmto proměnným lze přistupovat pomocí `{{.Run.VariableName}}`formátu, `VariableName` kde je jedna z následujících:

* `Run.ID`
* `Run.Registry`
* `Run.Date`
* `Run.Commit`
* `Run.Branch`

### <a name="runid"></a>Run.ID

Každé spuštění, prostřednictvím `az acr run`nebo aktivační událost založené na triggerech, které `az acr task create` jsou vytvořeny pomocí, mají jedinečné ID. ID představuje aktuálně prováděné spuštění.

Obvykle se používá pro jedinečnou tagování obrázku:

```yml
version: v1.0.0
steps:
    - build: -t {{.Run.Registry}}/hello-world:{{.Run.ID}} .
```

### <a name="runregistry"></a>Spustit. Registry

Plně kvalifikovaný název serveru registru. Obvykle se běžně používá pro obecné odkazy na registr, ve kterém se úloha spouští.

```yml
version: v1.0.0
steps:
  - build: -t {{.Run.Registry}}/hello-world:{{.Run.ID}} .
```

### <a name="rundate"></a>Spustit. datum

Aktuální čas UTC zahájení běhu.

### <a name="runcommit"></a>Spustit. potvrdit

V případě úlohy aktivované potvrzením do úložiště GitHub je identifikátor potvrzení.

### <a name="runbranch"></a>Spustit. větev

V případě úlohy aktivované potvrzením do úložiště GitHub se jedná o název větve.

## <a name="next-steps"></a>Další postup

Přehled úloh s více kroky najdete [v tématu spuštění více kroků při sestavování, testování a oprav úloh v ACR úlohách](container-registry-tasks-multi-step.md).

Informace o sestaveních s jedním krokem najdete v tématu [Přehled úloh ACR](container-registry-tasks-overview.md).

<!-- IMAGES -->

<!-- LINKS - External -->
[acr-tasks]: https://github.com/Azure-Samples/acr-tasks

<!-- LINKS - Internal -->
[az-acr-run]: /cli/azure/acr#az-acr-run
[az-acr-task-create]: /cli/azure/acr/task#az-acr-task-create
[az-configure]: /cli/azure/reference-index#az-configure
