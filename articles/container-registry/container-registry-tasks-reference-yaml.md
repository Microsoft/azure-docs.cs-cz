---
title: YAML reference – úlohy ACR
description: Referenční informace k definování úkolů v YAML pro úlohy ACR, včetně vlastností úloh, typů kroků, vlastností kroku a integrovaných proměnných.
ms.topic: article
ms.date: 10/23/2019
ms.openlocfilehash: 9558f698b4a9dbca46431fc02ced6ae30de29121
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "79246978"
---
# <a name="acr-tasks-reference-yaml"></a>Odkazy na úlohy ACR: YAML

Definice více kroků v úlohách ACR poskytuje primitivní výpočetní prostředí zaměřené na kontejnery zaměřené na vytváření, testování a opravy kontejnerů. Tento článek se zabývá příkazy, parametry, vlastnostmi a syntaxí souborů YAML, které definují úlohy s více kroky.

Tento článek obsahuje referenční informace k vytváření YAML souborů úloh s více kroky pro úlohy ACR. Pokud se chcete seznámit s ACR úkoly, přečtěte si téma [Přehled úloh ACR](container-registry-tasks-overview.md).

## <a name="acr-taskyaml-file-format"></a>ACR-Task. yaml – formát souboru

Úlohy ACR podporují deklaraci úlohy ve více krocích ve standardní syntaxi YAML. Kroky úkolu definujete v souboru YAML. Tuto úlohu pak můžete spustit ručně tak, že soubor předáte do příkazu [AZ ACR Run][az-acr-run] . Nebo pomocí souboru vytvořte úlohu s příkazem [AZ ACR Task Create][az-acr-task-create] , který se automaticky aktivuje na základě potvrzení Git nebo aktualizaci základní image. I když tento článek odkazuje `acr-task.yaml` na soubor, který obsahuje kroky, úlohy ACR podporují libovolný platný název souboru s [podporovanou příponou](#supported-task-filename-extensions).

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

V následujících částech tohoto článku je odkazováno na několik ukázkových souborů úloh. Ukázkové úlohy jsou ve veřejném úložišti GitHubu, [Azure-Samples/ACR-Tasks][acr-tasks]. Můžete je spustit pomocí příkazu Azure CLI [AZ ACR Run][az-acr-run]. Ukázkové příkazy jsou podobné následujícímu:

```azurecli
az acr run -f build-push-hello-world.yaml https://github.com/Azure-Samples/acr-tasks.git
```

Formátování ukázkových příkazů předpokládá, že jste v rozhraní příkazového řádku Azure nakonfigurovali výchozí registr, takže tento `--registry` parametr vynechá. Pokud chcete nakonfigurovat výchozí registr, použijte příkaz [AZ Configure][az-configure] s `--defaults` parametrem, který přijímá `acr=REGISTRY_NAME` hodnotu.

Pokud třeba chcete nakonfigurovat Azure CLI s výchozím registrem s názvem "myregistry":

```azurecli
az configure --defaults acr=myregistry
```

## <a name="task-properties"></a>Vlastnosti úlohy

Vlastnosti úlohy se obvykle zobrazují v horní části `acr-task.yaml` souboru. Jedná se o globální vlastnosti, které se použijí v celém plném provedení kroků úkolu. Některé z těchto globálních vlastností lze přepsat v rámci jednotlivého kroku.

| Vlastnost | Typ | Nepovinné | Popis | Přepsání podporováno | Výchozí hodnota |
| -------- | ---- | -------- | ----------- | ------------------ | ------------- |
| `version` | řetězec | Ano | Verze `acr-task.yaml` souboru, jak je analyzována službou ACR Tasks. I když se ACR úlohy snaží zachovat zpětnou kompatibilitu, tato hodnota umožňuje úlohám ACR zachovat kompatibilitu v rámci definované verze. Pokud tento parametr nezadáte, použije se výchozí verze na nejnovější verzi. | Ne | Žádná |
| `stepTimeout` | int (sekundy) | Ano | Maximální počet sekund, po které může být krok spuštěn. Pokud je vlastnost určena na úkolu, nastaví výchozí `timeout` vlastnost všech kroků. Pokud je `timeout` vlastnost určena v kroku, Přepisuje vlastnost poskytnutou úlohou. | Ano | 600 (10 minut) |
| `workingDirectory` | řetězec | Ano | Pracovní adresář kontejneru během běhu. Pokud je vlastnost určena na úkolu, nastaví výchozí `workingDirectory` vlastnost všech kroků. Pokud je zadáno v kroku, přepíše vlastnost poskytnutou úlohou. | Ano | `/workspace` |
| `env` | [řetězec, řetězec,...] | Ano |  Pole řetězců ve `key=value` formátu, které definují proměnné prostředí pro úlohu. Pokud je vlastnost určena na úkolu, nastaví výchozí `env` vlastnost všech kroků. V případě zadání v kroku přepíše všechny proměnné prostředí zděděné z úlohy. | Žádná |
| `secrets` | [tajný klíč, tajný kód,...] | Ano | Pole [tajných](#secret) objektů. | Žádná |
| `networks` | [síť, síť,...] | Ano | Pole [síťových](#network) objektů. | Žádná |

### <a name="secret"></a>Tajný kód

Objekt tajného kódu má následující vlastnosti.

| Vlastnost | Typ | Nepovinné | Popis | Výchozí hodnota |
| -------- | ---- | -------- | ----------- | ------- |
| `id` | řetězec | Ne | Identifikátor tajného klíče | Žádná |
| `keyvault` | řetězec | Ano | Adresa URL Azure Key Vault tajného klíče | Žádná |
| `clientID` | řetězec | Ano | ID klienta [spravované identity přiřazené uživatelem](container-registry-tasks-authentication-managed-identity.md) pro prostředky Azure. | Žádná |

### <a name="network"></a>network

Objekt sítě má následující vlastnosti.

| Vlastnost | Typ | Nepovinné | Popis | Výchozí hodnota |
| -------- | ---- | -------- | ----------- | ------- | 
| `name` | řetězec | Ne | Název sítě. | Žádná |
| `driver` | řetězec | Ano | Ovladač pro správu sítě. | Žádná |
| `ipv6` | bool | Ano | Zda je povolená síť s protokolem IPv6. | `false` |
| `skipCreation` | bool | Ano | Zda se má přeskočit vytváření sítě. | `false` |
| `isDefault` | bool | Ano | Zda je síť výchozí sítí poskytovanou pomocí Azure Container Registry | `false` |

## <a name="task-step-types"></a>Typy kroků úlohy

Úlohy ACR podporují tři typy kroků. Každý typ kroku podporuje několik vlastností, které jsou popsány v části pro každý typ kroku.

| Typ kroku | Popis |
| --------- | ----------- |
| [`build`](#build) | Vytvoří Image kontejneru pomocí známé `docker build` syntaxe. |
| [`push`](#push) | Spustí v `docker push` registru kontejneru nově sestavené nebo přeoznačené image. Podporují se Azure Container Registry, jiné privátní registry a veřejné centrum Docker. |
| [`cmd`](#cmd) | Spustí kontejner jako příkaz s parametry předanými do kontejneru `[ENTRYPOINT]`. Typ `cmd` kroku podporuje parametry, jako `env`, `detach`a další známé `docker run` možnosti příkazu, povolení jednotky a funkční testování pomocí souběžného spouštění kontejnerů. |

## <a name="build"></a>sestavení

Sestavte image kontejneru. Typ `build` kroku představuje víceklientské a zabezpečené způsoby spuštění `docker build` v cloudu jako primitivum první třídy.

### <a name="syntax-build"></a>Syntaxe: Build

```yml
version: v1.1.0
steps:
  - [build]: -t [imageName]:[tag] -f [Dockerfile] [context]
    [property]: [value]
```

Typ `build` kroku podporuje parametry v následující tabulce. Typ `build` kroku podporuje také všechny možnosti sestavení příkazu [Docker Build](https://docs.docker.com/engine/reference/commandline/build/) , `--build-arg` jako je například nastavení proměnných v době sestavení.

| Parametr | Popis | Nepovinné |
| --------- | ----------- | :-------: |
| `-t`&#124;`--image` | Definuje plně kvalifikovaný `image:tag` vzhled sestavené image.<br /><br />Jako obrázek lze použít k ověření vnitřních úkolů, jako jsou funkční testy, nikoli všechny bitové kopie, které `push` jsou požadovány pro Registry. Chcete-li však vytvořit instanci obrázku v rámci provádění úlohy, obrázek bude potřebovat název, který bude odkazovat.<br /><br />Na rozdíl `az acr build`od, spuštěné úlohy ACR neposkytují výchozí nabízené chování. U úloh ACR předpokládá výchozí scénář možnost sestavit, ověřit a potom vložit obrázek. Postup, jak volitelně vydávat sestavené image, najdete v tématu věnovaném [vložení](#push) . | Ano |
| `-f`&#124;`--file` | Určuje souboru Dockerfile předaný do `docker build`. Pokud není zadán, předpokládá se výchozí souboru Dockerfile v kořenovém adresáři kontextu. Chcete-li zadat souboru Dockerfile, předejte název souboru relativně ke kořenu kontextu. | Ano |
| `context` | Kořenový adresář předaný do `docker build`. Kořenový adresář každého úkolu je nastaven na sdílenou [WorkingDirectory](#task-step-properties)a zahrnuje kořen přidruženého klonovaného adresáře Git. | Ne |

### <a name="properties-build"></a>Vlastnosti: sestavení

Typ `build` kroku podporuje následující vlastnosti. Podrobnosti o těchto vlastnostech najdete v části [Vlastnosti kroku úlohy](#task-step-properties) v tomto článku.

| | | |
| -------- | ---- | -------- |
| `detach` | bool | Nepovinné |
| `disableWorkingDirectoryOverride` | bool | Nepovinné |
| `entryPoint` | řetězec | Nepovinné |
| `env` | [řetězec, řetězec,...] | Nepovinné |
| `expose` | [řetězec, řetězec,...] | Nepovinné |
| `id` | řetězec | Nepovinné |
| `ignoreErrors` | bool | Nepovinné |
| `isolation` | řetězec | Nepovinné |
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
| `workingDirectory` | řetězec | Nepovinné |

### <a name="examples-build"></a>Příklady: sestavení

#### <a name="build-image---context-in-root"></a>Image sestavení – kontext v kořenovém adresáři

```azurecli
az acr run -f build-hello-world.yaml https://github.com/AzureCR/acr-tasks-sample.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/build-hello-world.yaml -->
[!code-yml[task](~/acr-tasks/build-hello-world.yaml)]

#### <a name="build-image---context-in-subdirectory"></a>Sestavit image – kontext v podadresáři

```yml
version: v1.1.0
steps:
  - build: -t $Registry/hello-world -f hello-world.dockerfile ./subDirectory
```

## <a name="push"></a>push

Nahrajte jednu nebo více sestavených nebo přetagovaných imagí do registru kontejneru. Podporuje doručování do privátních registrů, jako je Azure Container Registry, nebo do veřejného centra Docker.

### <a name="syntax-push"></a>Syntaxe: push

Typ `push` kroku podporuje kolekci imagí. Syntaxe kolekce YAML podporuje vložené a vnořené formáty. Vložení jednoho obrázku je obvykle znázorněno pomocí vložené syntaxe:

```yml
version: v1.1.0
steps:
  # Inline YAML collection syntax
  - push: ["$Registry/hello-world:$ID"]
```

Pro lepší čitelnost použijte při nahrávání více imagí vnořenou syntaxi:

```yml
version: v1.1.0
steps:
  # Nested YAML collection syntax
  - push:
    - $Registry/hello-world:$ID
    - $Registry/hello-world:latest
```

### <a name="properties-push"></a>Vlastnosti: push

Typ `push` kroku podporuje následující vlastnosti. Podrobnosti o těchto vlastnostech najdete v části [Vlastnosti kroku úlohy](#task-step-properties) v tomto článku.

| | | |
| -------- | ---- | -------- |
| `env` | [řetězec, řetězec,...] | Nepovinné |
| `id` | řetězec | Nepovinné |
| `ignoreErrors` | bool | Nepovinné |
| `startDelay` | int (sekundy) | Nepovinné |
| `timeout` | int (sekundy) | Nepovinné |
| `when` | [řetězec, řetězec,...] | Nepovinné |

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

## <a name="cmd"></a>přepsat

Typ `cmd` kroku spouští kontejner.

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
| `env` | [řetězec, řetězec,...] | Nepovinné |
| `expose` | [řetězec, řetězec,...] | Nepovinné |
| `id` | řetězec | Nepovinné |
| `ignoreErrors` | bool | Nepovinné |
| `isolation` | řetězec | Nepovinné |
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
| `workingDirectory` | řetězec | Nepovinné |

Podrobnosti o těchto vlastnostech najdete v části [Vlastnosti kroku úlohy](#task-step-properties) v tomto článku.

### <a name="examples-cmd"></a>Příklady: cmd

#### <a name="run-hello-world-image"></a>Spustit obrázek Hello-World

Tento příkaz spustí soubor `hello-world.yaml` úlohy, který odkazuje na obrázek [Hello-World](https://hub.docker.com/_/hello-world/) v Docker Hub.

```azurecli
az acr run -f hello-world.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/hello-world.yaml -->
[!code-yml[task](~/acr-tasks/hello-world.yaml)]

#### <a name="run-bash-image-and-echo-hello-world"></a>Spustit image bash a echo "Hello World"

Tento příkaz spustí soubor `bash-echo.yaml` úlohy, který odkazuje na Image [bash](https://hub.docker.com/_/bash/) v Docker Hub.

```azurecli
az acr run -f bash-echo.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/bash-echo.yaml -->
[!code-yml[task](~/acr-tasks/bash-echo.yaml)]

#### <a name="run-specific-bash-image-tag"></a>Spustit konkrétní značku image bash

Chcete-li spustit konkrétní verzi bitové kopie, zadejte značku v `cmd`.

Tento příkaz spustí soubor `bash-echo-3.yaml` úlohy, který odkazuje na Image [bash: 3.0](https://hub.docker.com/_/bash/) v Docker Hub.

```azurecli
az acr run -f bash-echo-3.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/bash-echo-3.yaml -->
[!code-yml[task](~/acr-tasks/bash-echo-3.yaml)]

#### <a name="run-custom-images"></a>Spuštění vlastních imagí

Typ `cmd` kroku odkazuje na Image pomocí standardního `docker run` formátu. Pro image, které nejsou v registru, se předpokládá, že pocházejí z docker.io. Předchozí příklad může být stejně reprezentován jako:

```yml
version: v1.1.0
steps:
  - cmd: docker.io/bash:3.0 echo hello world
```

Pomocí standardní `docker run` konvence odkazů na Image může `cmd` aplikace spouštět image z libovolného privátního registru nebo z veřejného centra Docker. Pokud odkazujete na Image ve stejném registru, ve kterém je spuštěný úkol ACR, nemusíte zadávat žádné přihlašovací údaje registru.

* Spustí image z Azure Container Registry. Následující příklad předpokládá, že máte registr s názvem `myregistry`a vlastní image `myimage:mytag`.

    ```yml
    version: v1.1.0
    steps:
        - cmd: myregistry.azurecr.io/myimage:mytag
    ```

* Generalize reference registru pomocí proměnné Run nebo aliasu

    Místo hardwarového kódování názvu registru v `acr-task.yaml` souboru můžete zvýšit jeho přenos pomocí [proměnné Run](#run-variables) nebo [aliasu](#aliases). Proměnná `Run.Registry` nebo `$Registry` alias se rozbalí za běhu do názvu registru, ve kterém je úloha spuštěná.

    Chcete-li například zobecnit předchozí úlohu, aby fungovala v jakémkoli registru služby Azure Container Registry, odkazujte na $Registry proměnnou v názvu bitové kopie:

    ```yml
    version: v1.1.0
    steps:
      - cmd: $Registry/myimage:mytag
    ```

## <a name="task-step-properties"></a>Vlastnosti kroku úlohy

Každý typ kroku podporuje několik vlastností vhodných pro svůj typ. Následující tabulka definuje všechny dostupné vlastnosti kroku. Ne všechny typy kroků podporují všechny vlastnosti. Chcete-li zjistit, které z těchto vlastností jsou k dispozici pro každý typ kroku, přečtěte si referenční oddíly typu [cmd](#cmd), [Build](#build)a [push](#push) Step.

| Vlastnost | Typ | Nepovinné | Popis | Výchozí hodnota |
| -------- | ---- | -------- | ----------- | ------- |
| `detach` | bool | Ano | Určuje, zda má být při spuštění odpojen kontejner. | `false` |
| `disableWorkingDirectoryOverride` | bool | Ano | Určuje, zda `workingDirectory` má být zakázána funkce přepsání. Toto použijte v kombinaci s `workingDirectory` nástrojem, aby měla úplnou kontrolu nad pracovním adresářem kontejneru. | `false` |
| `entryPoint` | řetězec | Ano | `[ENTRYPOINT]` Přepíše kontejner kroku. | Žádná |
| `env` | [řetězec, řetězec,...] | Ano | Pole řetězců ve `key=value` formátu, které definují proměnné prostředí pro krok | Žádná |
| `expose` | [řetězec, řetězec,...] | Ano | Pole portů, které jsou zpřístupněny z kontejneru. |  Žádná |
| [`id`](#example-id) | řetězec | Ano | Jednoznačně identifikuje krok v rámci úkolu. Další kroky v úloze můžou odkazovat na krok `id`, například pro kontrolu závislosti s nástrojem. `when`<br /><br />`id` Je také název běžícího kontejneru. Procesy běžící v jiných kontejnerech v úloze můžou `id` jako název hostitele DNS odkazovat jako na jeho název hostitele DNS nebo pro přístup k němu pomocí protokolů Docker [ID], například. | `acb_step_%d`, kde `%d` je index založený na nule v souboru YAML shora dolů |
| `ignoreErrors` | bool | Ano | Určuje, zda má být krok označen jako úspěšný bez ohledu na to, zda při provádění kontejneru došlo k chybě. | `false` |
| `isolation` | řetězec | Ano | Úroveň izolace kontejneru. | `default` |
| `keep` | bool | Ano | Určuje, zda má být kontejner kroku po provedení uchováván. | `false` |
| `network` | objekt | Ano | Identifikuje síť, ve které se kontejner spouští. | Žádná |
| `ports` | [řetězec, řetězec,...] | Ano | Pole portů, které jsou publikovány z kontejneru pro hostitele. |  Žádná |
| `pull` | bool | Ano | Určuje, zda má být před spuštěním kontejneru vynutit stažení, aby nedocházelo k chování ukládání do mezipaměti. | `false` |
| `privileged` | bool | Ano | Určuje, zda má být kontejner spuštěn v privilegovaném režimu. | `false` |
| `repeat` | int | Ano | Počet opakovaných pokusů o opakování provádění kontejneru. | 0 |
| `retries` | int | Ano | Počet opakovaných pokusů o pokus o vykonání kontejneru, pokud kontejner neprojde. Opakování se pokusí pouze v případě, že ukončovací kód kontejneru je nenulový. | 0 |
| `retryDelay` | int (sekundy) | Ano | Zpoždění v sekundách mezi opakovanými pokusy o spuštění kontejneru. | 0 |
| `secret` | objekt | Ano | Identifikuje Azure Key Vault tajný klíč nebo [spravovanou identitu pro prostředky Azure](container-registry-tasks-authentication-managed-identity.md). | Žádná |
| `startDelay` | int (sekundy) | Ano | Počet sekund, po který se má zpozdit spuštění kontejneru | 0 |
| `timeout` | int (sekundy) | Ano | Maximální počet sekund, po které může krok běžet, než se ukončí. | 600 |
| [`when`](#example-when) | [řetězec, řetězec,...] | Ano | Nakonfiguruje závislost kroku na jednom nebo několika dalších krocích v rámci úlohy. | Žádná |
| `user` | řetězec | Ano | Uživatelské jméno nebo UID kontejneru | Žádná |
| `workingDirectory` | řetězec | Ano | Nastaví pracovní adresář pro krok. Ve výchozím nastavení ACR úlohy vytvoří kořenový adresář jako pracovní adresář. Nicméně pokud má sestavení několik kroků, předchozí kroky mohou sdílet artefakty s pozdějšími kroky zadáním stejného pracovního adresáře. | `/workspace` |

### <a name="examples-task-step-properties"></a>Příklady: vlastnosti kroku úlohy

#### <a name="example-id"></a>Příklad: ID

Sestavení dvou imagí, vytváření instancí bitové kopie funkčního testu. Každý krok je identifikován jedinečným `id` , který z dalších kroků v odkazu na úlohu ve `when` své vlastnosti.

```azurecli
az acr run -f when-parallel-dependent.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/when-parallel-dependent.yaml -->
[!code-yml[task](~/acr-tasks/when-parallel-dependent.yaml)]

#### <a name="example-when"></a>Příklad: when

`when` Vlastnost určuje závislost kroku na dalších krocích v rámci úlohy. Podporuje dvě hodnoty parametrů:

* `when: ["-"]`-Neurčuje žádnou závislost na jiných krocích. Zadání `when: ["-"]` kroku spustí okamžité provedení a povolí souběžné provádění kroků.
* `when: ["id1", "id2"]`– Určuje, že krok je závislý na krocích `id` s "id1" `id` a "ID 2". Tento krok se neprovede až po dokončení obou kroků "id1" a "ID 2".

Pokud `when` není v kroku zadán, je tento krok závislý na dokončení předchozího kroku v `acr-task.yaml` souboru.

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
* `Run.SharedVolume`
* `Run.Registry`
* `Run.RegistryName`
* `Run.Date`
* `Run.OS`
* `Run.Architecture`
* `Run.Commit`
* `Run.Branch`
* `Run.TaskName`

Názvy proměnných jsou všeobecně vysvětlivekné. Podrobnosti se řídí pro běžně používané proměnné. Od verze `v1.1.0`YAML můžete místo většiny proměnných spuštění použít zkrácený a předem definovaný [alias úkolu](#aliases) . Například místo `{{.Run.Registry}}`použijte `$Registry` alias.

### <a name="runid"></a>Run.ID

Každé spuštění, prostřednictvím `az acr run`nebo aktivační událost založené na spuštění úkolů vytvořených prostřednictvím `az acr task create`, má jedinečné ID. ID představuje aktuálně prováděné spuštění.

Obvykle se používá pro jedinečnou tagování obrázku:

```yml
version: v1.1.0
steps:
    - build: -t $Registry/hello-world:$ID .
```

### <a name="runregistry"></a>Spustit. Registry

Plně kvalifikovaný název serveru registru. Obvykle se běžně používá pro obecné odkazy na registr, ve kterém se úloha spouští.

```yml
version: v1.1.0
steps:
  - build: -t $Registry/hello-world:$ID .
```

### <a name="runregistryname"></a>Run. Registry

Název registru kontejneru. Obvykle se `cmd` používá v krocích úloh, které nevyžadují plně kvalifikovaný název serveru, například kroky, které spouštějí příkazy rozhraní příkazového řádku Azure CLI v registrech.

```yml
version 1.1.0
steps:
# List repositories in registry
- cmd: az login --identity
- cmd: az acr repository list --name $RegistryName
```

### <a name="rundate"></a>Spustit. datum

Aktuální čas UTC zahájení běhu.

### <a name="runcommit"></a>Spustit. potvrdit

V případě úlohy aktivované potvrzením do úložiště GitHub je identifikátor potvrzení.

### <a name="runbranch"></a>Spustit. větev

V případě úlohy aktivované potvrzením do úložiště GitHub se jedná o název větve.

## <a name="aliases"></a>Aliasy

Od `v1.1.0`nástroje ACR úkoly podporují aliasy, které jsou k dispozici pro kroky úlohy při jejich spuštění. Aliasy jsou podobné v konceptu aliasů (příkazy Command Shortcuts) podporovaných v bash a některých dalších příkazových prostředích. 

S aliasem můžete spustit libovolný příkaz nebo skupinu příkazů (včetně možností a názvů souborů) zadáním jediného slova.

Úlohy ACR podporují několik předdefinovaných aliasů a také vlastní aliasy, které vytvoříte.

### <a name="predefined-aliases"></a>Předdefinované aliasy

K dispozici jsou následující aliasy úloh, které lze použít místo [proměnných spuštění](#run-variables):

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

V části `$` kroky úkolu předchází alias s direktivou, jako v tomto příkladu:

```yml
version: v1.1.0
steps:
  - build: -t $Registry/hello-world:$ID -f hello-world.dockerfile .
```

### <a name="image-aliases"></a>Aliasy obrázků

Každý z následujících aliasů odkazuje na stabilní obrázek v Microsoft Container Registry (MCR). Na každý z nich můžete odkazovat v `cmd` části souboru úlohy bez použití direktivy.

| Alias | Image |
| ----- | ----- |
| `acr` | `mcr.microsoft.com/acr/acr-cli:0.1` |
| `az` | `mcr.microsoft.com/acr/azure-cli:a80af84` |
| `bash` | `mcr.microsoft.com/acr/bash:a80af84` |
| `curl` | `mcr.microsoft.com/acr/curl:a80af84` |

Následující příklad úlohy používá několik aliasů k [vyprázdnění](container-registry-auto-purge.md) značek obrázků starších než 7 dní v úložišti `samples/hello-world` v registru spuštění:

```yml
version: v1.1.0
steps:
  - cmd: acr tag list --registry $RegistryName --repository samples/hello-world
  - cmd: acr purge --registry $RegistryName --filter samples/hello-world:.* --ago 7d
```

### <a name="custom-alias"></a>Vlastní alias

V souboru YAML definujte vlastní alias a použijte ho tak, jak je znázorněno v následujícím příkladu. Alias může obsahovat pouze alfanumerické znaky. Výchozí direktivou pro rozšíření alias je `$` znak.

```yml
version: v1.1.0
alias:
  values:
    repo: myrepo
steps:
  - build: -t $Registry/$repo/hello-world:$ID -f Dockerfile .
```

Pro vlastní definice aliasů můžete propojit se vzdáleným nebo místním souborem YAML. Následující příklad odkazuje na soubor YAML v úložišti objektů BLOB v Azure:

```yml
version: v1.1.0
alias:
  src:  # link to local or remote custom alias files
    - 'https://link/to/blob/remoteAliases.yml?readSasToken'
[...]
```

## <a name="next-steps"></a>Další kroky

Přehled úloh s více kroky najdete [v tématu spuštění více kroků při sestavování, testování a oprav úloh v ACR úlohách](container-registry-tasks-multi-step.md).

Informace o sestaveních s jedním krokem najdete v tématu [Přehled úloh ACR](container-registry-tasks-overview.md).



<!-- IMAGES -->

<!-- LINKS - External -->
[acr-tasks]: https://github.com/Azure-Samples/acr-tasks

<!-- LINKS - Internal -->
[az-acr-run]: /cli/azure/acr#az-acr-run
[az-acr-task-create]: /cli/azure/acr/task#az-acr-task-create
[az-configure]: /cli/azure/reference-index#az-configure
