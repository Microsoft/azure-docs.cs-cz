---
title: Vlastnosti dvojčat a modulu agenta a rozbočovače – Azure IoT Edge
description: Zkontrolujte konkrétní vlastnosti a jejich hodnoty pro dvojčata modulů edgeAgent a edgeHub
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 06/17/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 4684daf2a1095a40c478170be37edcae788868ef
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79284834"
---
# <a name="properties-of-the-iot-edge-agent-and-iot-edge-hub-module-twins"></a>Vlastnosti agenta IoT Edge a dvojčata modulů centra IoT Edge

Agent IoT Edge a Centrum IoT Edge jsou dva moduly, které tvoří modul runtime IoT Edge. Další informace o odpovědnosti každého modulu runtime najdete [v tématu Principy modulu Azure IoT Edge a jeho architektury](iot-edge-runtime.md).

Tento článek obsahuje požadované vlastnosti a hlášené vlastnosti dvojčat modulu runtime. Další informace o nasazení modulů na zařízeních IoT Edge najdete v tématu [Naučte se nasazovat moduly a nastavovat trasy v IoT Edge](module-composition.md).

Dvojče modulu zahrnuje:

* **Požadované vlastnosti**. Back-end řešení můžete nastavit požadované vlastnosti a modul je může číst. Modul může také přijímat oznámení o změnách požadovaných vlastností. Požadované vlastnosti se používají spolu s vykazošovacími vlastnostmi k synchronizaci konfigurace modulu nebo podmínek.

* **Hlášené vlastnosti**. Modul můžete nastavit hlášené vlastnosti a back-end řešení můžete číst a dotaz na ně. Hlášené vlastnosti se používají spolu s požadovanými vlastnostmi pro synchronizaci konfigurace modulu nebo podmínek.

## <a name="edgeagent-desired-properties"></a>Požadované vlastnosti EdgeAgent

Dvojče modulu pro agenta IoT Edge se nazývá `$edgeAgent` a koordinuje komunikaci mezi agentem IoT Edge spuštěným na zařízení a ioT hubem. Požadované vlastnosti jsou nastaveny při použití manifestu nasazení na konkrétní zařízení jako součást jednoho zařízení nebo ve velkém měřítku nasazení.

| Vlastnost | Popis | Požaduje se |
| -------- | ----------- | -------- |
| verze schématu | Musí být "1.0" | Ano |
| runtime.type | Musí být "docker" | Ano |
| runtime.settings.minDockerVersion | Nastavte minimální verzi Dockeru vyžadovou tímto manifestem nasazení. | Ano |
| runtime.settings.loggingOptions | Řetězecifikovaný JSON obsahující možnosti protokolování pro kontejner agenta IoT Edge. [Možnosti protokolování dockeru](https://docs.docker.com/engine/admin/logging/overview/) | Ne |
| runtime.settings.registryCredentials<br>. {registryId}.uživatelské jméno | Uživatelské jméno registru kontejneru. Pro Azure Container Registry uživatelské jméno je obvykle název registru.<br><br> Pověření registru jsou nezbytné pro všechny bitové kopie soukromých modulů. | Ne |
| runtime.settings.registryCredentials<br>. {registryId}.heslo | Heslo pro registr kontejneru. | Ne |
| runtime.settings.registryCredentials<br>. {registryId}.address | Adresa registru kontejneru. V registru kontejnerů Azure je adresa obvykle *{název registru}.azurecr.io*. | Ne |  
| systemModules.edgeAgent.type | Musí být "docker" | Ano |
| systemModules.edgeAgent.settings.image | Identifikátor URI obrázku agenta IoT Edge. V současné době agent IoT Edge není schopen aktualizovat sám. | Ano |
| systemModules.edgeAgent.settings<br>.createOptions | Řetězecifikovaný JSON obsahující možnosti pro vytvoření kontejneru agenta IoT Edge. [Docker vytvořit možnosti](https://docs.docker.com/engine/api/v1.32/#operation/ContainerCreate) | Ne |
| systemModules.edgeAgent.configuration.id | ID nasazení, které nasadilo tento modul. | Služba IoT Hub nastaví tuto vlastnost, když je manifest použit pomocí nasazení. Není součástí manifestu nasazení. |
| systemModules.edgeHub.type | Musí být "docker" | Ano |
| systemModules.edgeHub.status | Musí být "běží" | Ano |
| systemModules.edgeHub.restartPolicy | Musí být "vždy" | Ano |
| systemModules.edgeHub.settings.image | Identifikátor URI image centra IoT Edge. | Ano |
| systemModules.edgeHub.settings<br>.createOptions | Řetězecifikovaný JSON obsahující možnosti pro vytvoření kontejneru centra IoT Edge. [Docker vytvořit možnosti](https://docs.docker.com/engine/api/v1.32/#operation/ContainerCreate) | Ne |
| systemModules.edgeHub.configuration.id | ID nasazení, které nasadilo tento modul. | Služba IoT Hub nastaví tuto vlastnost, když je manifest použit pomocí nasazení. Není součástí manifestu nasazení. |
| Moduly. {moduleId}.version {moduleId}.version {moduleId}.version {module | Uživatelem definovaný řetězec představující verzi tohoto modulu. | Ano |
| Moduly. {moduleId}.type | Musí být "docker" | Ano |
| Moduly. {moduleId}.status | {"spuštěno" \| "zastaveno"} | Ano |
| Moduly. {moduleId}.restartPolicy | {"never" \| "on-failure" \| "on-un \| healthy" "always"} | Ano |
| Moduly. {moduleId}.imagePullPolicy | {"on-create" \| "never"} | Ne |
| Moduly. {moduleId}.settings.image | Identifikátor URI pro bitovou kopii modulu. | Ano |
| Moduly. {moduleId}.settings.createOptions | Řetězecifikovaný JSON obsahující možnosti pro vytvoření kontejneru modulu. [Docker vytvořit možnosti](https://docs.docker.com/engine/api/v1.32/#operation/ContainerCreate) | Ne |
| Moduly. {moduleId}.configuration.id | ID nasazení, které nasadilo tento modul. | Služba IoT Hub nastaví tuto vlastnost, když je manifest použit pomocí nasazení. Není součástí manifestu nasazení. |

## <a name="edgeagent-reported-properties"></a>Vlastnosti vykázané edgeagentem

Vlastnosti hlášené agentem IoT Edge zahrnují tři hlavní informace:

1. Stav použití naposledy viděných požadovaných vlastností;
2. Stav modulů aktuálně spuštěných v zařízení, jak je uvedeno agentem IoT Edge; A
3. Kopie požadovaných vlastností aktuálně spuštěných v zařízení.

Kopie aktuální požadované vlastnosti je užitečné zjistit, zda zařízení má použít nejnovější nasazení nebo je stále spuštěna předchozí manifest nasazení.

> [!NOTE]
> Hlášené vlastnosti agenta IoT Edge jsou užitečné, protože mohou být dotazovány pomocí [dotazovacího jazyka Služby IoT Hub,](../iot-hub/iot-hub-devguide-query-language.md) aby se prozkoumal stav nasazení ve velkém měřítku. Další informace o tom, jak používat vlastnosti agenta IoT Edge pro stav, [najdete v tématu Principy nasazení IoT Edge pro jednotlivá zařízení nebo ve velkém měřítku](module-deployment-monitoring.md).

Následující tabulka neobsahuje informace, které jsou zkopírovány z požadovaných vlastností.

| Vlastnost | Popis |
| -------- | ----------- |
| lastDesiredVersion | Toto celé číslo odkazuje na poslední verzi požadovaných vlastností zpracovaných agentem IoT Edge. |
| lastDesiredStatus.code | Tento stavový kód odkazuje na poslední požadované vlastnosti, které vidí agent IoT Edge. Povolené `200` hodnoty: `400` Úspěch, `412` Neplatná konfigurace, Neplatná verze schématu, `417` požadované vlastnosti jsou prázdné, `500` neúspěšné |
| lastDesiredStatus.description | Textový popis stavu |
| deviceHealth | `healthy`pokud je stav za běhu všech `running` `stopped`modulů buď nebo , `unhealthy` jinak |
| konfiguraceZdraví. {deploymentId}.health | `healthy`Pokud je stav modulu runtime všech modulů nastavených `running` nasazením {deploymentId} buď nebo `stopped`, `unhealthy` jinak |
| runtime.platform.OS | Nahlášení operačního operačního systémem spuštěného na zařízení |
| runtime.platform.architecture | Vykazování architektury procesoru v zařízení |
| systemModules.edgeAgent.runtimeStatus | Ohlášený stav agenta IoT Edge: \| {"running" "Není v pořádku"} |
| systemModules.edgeAgent.statusPopis | Textový popis ohlášeného stavu agenta IoT Edge. |
| systemModules.edgeHub.runtimeStatus | Stav centra IoT Edge: { \| "running" "stopped" \| "failed" \| "backoff" \| "unhealthy" } |
| systemModules.edgeHub.statusPopis | Textový popis stavu centra IoT Edge, pokud není v pořádku. |
| systemModules.edgeHub.exitCode | Ukončovací kód nahlášený kontejnerem centra IoT Edge, pokud kontejner ukončí |
| systemModules.edgeHub.startTimeUtc | Čas posledního spuštění centra IoT Edge |
| systemModules.edgeHub.lastExitTimeUtc | Čas, kdy byl centrum IoT Edge naposledy ukončeno |
| systemModules.edgeHub.lastRestartTimeUtc | Čas posledního restartování centra IoT Edge |
| systemModules.edgeHub.restartCount | Počet restartování tohoto modulu jako součást zásad restartování. |
| Moduly. {moduleId}.runtimeStatus | Stav \| modulu: { "running" \| "stopped" \| "failed" \| "backoff" "unhealthy" } |
| Moduly. {moduleId}.statusDescription | Textový popis stavu modulu, pokud není v pořádku. |
| Moduly. {moduleId}.exitCode | Ukončovací kód nahlášený kontejnerem modulu, pokud kontejner ukončí |
| Moduly. {moduleId}.startTimeUtc | Čas posledního spuštění modulu |
| Moduly. {moduleId}.lastExitTimeUtc | Čas, kdy byl modul naposledy ukončen |
| Moduly. {moduleId}.lastRestartTimeUtc | Čas posledního restartování modulu |
| Moduly. {moduleId}.restartCount | Počet restartování tohoto modulu jako součást zásad restartování. |

## <a name="edgehub-desired-properties"></a>Požadované vlastnosti EdgeHub

Dvojče modulu pro rozbočovač `$edgeHub` IoT Edge se nazývá a koordinuje komunikaci mezi centrem IoT Edge spuštěným na zařízení a centrem IoT Hub. Požadované vlastnosti jsou nastaveny při použití manifestu nasazení na konkrétní zařízení jako součást jednoho zařízení nebo ve velkém měřítku nasazení.

| Vlastnost | Popis | Povinné v manifestu nasazení |
| -------- | ----------- | -------- |
| verze schématu | Musí být "1.0" | Ano |
| Trasy. {routeName} | Řetězec představující trasu rozbočovače IoT Edge. Další informace naleznete v tématu [Deklarovat trasy](module-composition.md#declare-routes). | Prvek `routes` může být přítomen, ale prázdný. |
| storeAndForwardConfiguration.timeToLiveSecs | Čas v sekundách, který služba IoT Edge hub uchovává zprávy, pokud jsou odpojeny od koncových bodů směrování, ať už ioT Hub nebo místní modul. Hodnota může být libovolné kladné celé číslo. | Ano |

## <a name="edgehub-reported-properties"></a>Vlastnosti oznamovány společností EdgeHub

| Vlastnost | Popis |
| -------- | ----------- |
| lastDesiredVersion | Toto celé číslo odkazuje na poslední verzi požadovaných vlastností zpracovaných centrem IoT Edge. |
| lastDesiredStatus.code | Stavový kód odkazující na poslední požadované vlastnosti, které zobrazuje centrum IoT Edge. Povolené hodnoty: `200` `400` Úspěch, `500` Neplatná konfigurace, Neúspěšná |
| lastDesiredStatus.description | Textový popis stavu. |
| Klienty. {zařízení nebo modulId}.status | Stav připojení tohoto zařízení nebo modulu. Možné hodnoty {"connected" \| "disconnected"}. Pouze identity modulu mohou být v odpojeném stavu. Zařízení pro připojení k rozbočovači IoT Edge se zobrazují pouze v případě, že jsou připojena. |
| Klienty. {zařízení nebo modulId}.lastConnectTime | Naposledy připojené zařízení nebo modul. |
| Klienty. {device or moduleId}.lastDisconnectTime | Naposledy se zařízení nebo modul odpojil. |

## <a name="next-steps"></a>Další kroky

Informace o použití těchto vlastností k vytváření manifestů nasazení najdete v [tématu Informace o tom, jak lze moduly IoT Edge používat, konfigurovat a znovu používat](module-composition.md).
