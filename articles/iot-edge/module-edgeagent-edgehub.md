---
title: "Referenční dokumentace Azure IoT EdgeAgent a EdgeHub | Microsoft Docs"
description: "Zkontrolujte konkrétní vlastnosti a jejich hodnoty pro modul dvojčata edgeAgent a edgeHub"
services: iot-edge
keywords: 
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 03/14/2018
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: 17c97c6f233c9861ac42abc0a1f11089bb938e7c
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/16/2018
---
# <a name="properties-of-the-edge-agent-and-edge-hub-module-twins"></a>Vlastnosti agenta okraj a okraj rozbočovače modul dvojčata

Agent okraj a okraj centra jsou dva moduly, které tvoří runtime IoT okraj. Další informace o jaké povinností každý modul provádí najdete v tématu [pochopit modulu runtime Azure IoT okraj a jeho architecture]](iot-edge-runtime.md). 

Tento článek obsahuje požadované vlastnosti a vlastnosti hlášené dvojčata modulu runtime. V tématu [nasazení a monitorování] [ lnk-deploy] Další informace o tom, jak nasadit modulů na IoT hraniční zařízení.

## <a name="edgeagent-desired-properties"></a>Vlastnosti EdgeAgent potřeby

Je volána twin modulu pro agenta hraniční `$edgeAgent` a koordinuje komunikaci mezi hraniční agenta spuštěného na zařízení a služby IoT Hub. Požadované vlastnosti se nastavují při použití manifest nasazení na určité zařízení v rámci jednoho zařízení nebo v odpovídajícím měřítku nasazení. 

| Vlastnost | Popis | Požaduje se |
| -------- | ----------- | -------- |
| schemaVersion | Musí být "1.0" | Ano |
| runtime.type | Musí být "docker" | Ano |
| runtime.settings.minDockerVersion | Nastavte na tento – manifest nasazení požadavek na minimální verzi Docker | Ano |
| runtime.settings.loggingOptions | Stringified JSON obsahující možnosti protokolování pro kontejner agenta okraj. [Možnosti protokolování docker][lnk-docker-logging-options] | Ne |
| systemModules.edgeAgent.type | Musí být "docker" | Ano |
| systemModules.edgeAgent.settings.image | Identifikátor URI bitovou kopii agenta okraj. Edge agent v současné době není možné aktualizovat sám sebe. | Ano |
| systemModules.edgeAgent.settings.createOptions | Stringified JSON obsahující možnosti pro vytvoření kontejneru agenta okraj. [Možnosti vytvoření docker][lnk-docker-create-options] | Ne |
| systemModules.edgeAgent.configuration.id | ID nasazení, které tento modul pro nasazení. | Centrum IoT hub je nastavena v případě použije tento manifestu pomocí nasazení. Není součástí manifest nasazení. |
| systemModules.edgeHub.type | Musí být "docker" | Ano |
| systemModules.edgeHub.status | Musí být "spuštění" | Ano |
| systemModules.edgeHub.restartPolicy | Musí být "vždy" | Ano |
| systemModules.edgeHub.settings.image | Identifikátor URI bitovou kopii Edge rozbočovače. | Ano |
| systemModules.edgeHub.settings.createOptions | Stringified JSON obsahující možnosti pro vytvoření kontejneru Edge rozbočovače. [Možnosti vytvoření docker][lnk-docker-create-options] | Ne |
| systemModules.edgeHub.configuration.id | ID nasazení, které tento modul pro nasazení. | Centrum IoT hub je nastavena v případě použije tento manifestu pomocí nasazení. Není součástí manifest nasazení. |
| modules.{moduleId}.version | Uživatelem definované řetězec představující verze tohoto modulu. | Ano |
| modules.{moduleId}.type | Musí být "docker" | Ano |
| modules.{moduleId}.restartPolicy | {"nikdy" \| "na-se nezdařilo" \| "na-není v pořádku" \| "vždy"} | Ano |
| modules.{moduleId}.settings.image | Identifikátor URI pro image modulu. | Ano |
| modules.{moduleId}.settings.createOptions | Stringified JSON obsahující možnosti pro vytvoření kontejneru modulu. [Možnosti vytvoření docker][lnk-docker-create-options] | Ne |
| modules.{moduleId}.configuration.id | ID nasazení, které tento modul pro nasazení. | Centrum IoT hub je nastavena v případě použije tento manifestu pomocí nasazení. Není součástí manifest nasazení. |

## <a name="edgeagent-reported-properties"></a>EdgeAgent hlášené vlastnosti

Edge agent oznámil, že vlastnosti zahrnují tři hlavní údaje:

1. Stav aplikace vidět poslední požadované vlastnosti;
2. Stav modulů, které jsou aktuálně spuštěné na zařízení, vykazované Edge agenta; a
3. Kopie požadované vlastnosti, které jsou aktuálně spuštěné na zařízení.

Tento poslední část informace je užitečné v případě, že nejsou úspěšně použít nejnovější požadované vlastností modulem runtime a zařízení stále běží předchozí manifest nasazení.

> [!NOTE]
> Hlášené vlastnosti agenta hraniční jsou užitečné, protože může být dotazován s [IoT Hub dotazovací jazyk] [ lnk-iothub-query] prozkoumat stav nasazení ve velkém měřítku. Odkazovat na [nasazení] [ lnk-deploy] Další informace o tom, jak tuto funkci používat.

V následující tabulce nezahrnuje informace, které budou zkopírována z požadované vlastnosti.

| Vlastnost | Popis |
| -------- | ----------- |
| lastDesiredVersion | Toto celé číslo odkazuje na poslední verzi požadované vlastnosti zpracovat agentem okraj. |
| lastDesiredStatus.code | Toto je kód stavu odkazující na poslední požadované vlastnosti kontaktu s agentem okraj. Povolené hodnoty: `200` úspěch, `400` neplatná konfigurace `412` neplatné schéma verze `417` požadované vlastnosti jsou prázdná, `500` se nezdařilo |
| lastDesiredStatus.description | Textový popis stavu |
| deviceHealth | `healthy` Pokud je stav modulu runtime všechny moduly, buď `running` nebo `stopped`, `unhealthy` jinak |
| configurationHealth.{deploymentId}.health | `healthy` Pokud stav modulu runtime všechny moduly nastavit nasazení {deploymentId} je buď `running` nebo `stopped`, `unhealthy` jinak |
| runtime.platform.OS | Vytváření sestav operačního systému spuštěné na zařízení |
| runtime.platform.architecture | Vytváření sestav architekturu procesoru na zařízení |
| systemModules.edgeAgent.runtimeStatus | Reportovaný stav agenta Edge: {"spuštění" \| "není v pořádku"} |
| systemModules.edgeAgent.statusDescription | Textový popis reportovaný stav agenta okraj. |
| systemModules.edgeHub.runtimeStatus | Aktuální stav rozbočovače Edge: {"spuštění" \| "stopped" \| "se nezdařilo" \| "omezení rychlosti" \| "není v pořádku"} |
| systemModules.edgeHub.statusDescription | Textový popis aktuální stav Edge rozbočovače, pokud není v pořádku. |
| systemModules.edgeHub.exitCode | Pokud byl ukončen, ukončovací kód hlášené kontejneru Edge rozbočovače |
| systemModules.edgeHub.startTimeUtc | Čas posledního spuštění Edge rozbočovače |
| systemModules.edgeHub.lastExitTimeUtc | Čas, kdy Edge rozbočovače poslední byl ukončen |
| systemModules.edgeHub.lastRestartTimeUtc | Čas poslední restartováním Edge rozbočovače |
| systemModules.edgeHub.restartCount | Počet pokusů, které byl tento modul restartován v rámci zásad restartování. |
| modules.{moduleId}.runtimeStatus | Aktuální stav modulu: {"spuštění" \| "stopped" \| "se nezdařilo" \| "omezení rychlosti" \| "není v pořádku"} |
| modules.{moduleId}.statusDescription | Textový popis aktuální stav modulu, pokud není v pořádku. |
| modules.{moduleId}.exitCode | Pokud byl ukončen, ukončovací kód hlášené kontejner modulu |
| modules.{moduleId}.startTimeUtc | Čas posledního spuštění modulu |
| modules.{moduleId}.lastExitTimeUtc | Čas, kdy modul poslední byl ukončen |
| modules.{moduleId}.lastRestartTimeUtc | Čas, kdy modul posledního restartování |
| modules.{moduleId}.restartCount | Počet pokusů, které byl tento modul restartován v rámci zásad restartování. |

## <a name="edgehub-desired-properties"></a>Vlastnosti EdgeHub potřeby

Je volána twin modulu pro rozbočovač na hraniční `$edgeHub` a koordinuje komunikaci mezi hraniční rozbočovače spuštěné v zařízení a služby IoT Hub. Požadované vlastnosti se nastavují při použití manifest nasazení na určité zařízení v rámci jednoho zařízení nebo v odpovídajícím měřítku nasazení. 

| Vlastnost | Popis | Požadované v manifestu nasazení |
| -------- | ----------- | -------- |
| schemaVersion | Musí být "1.0" | Ano |
| routes.{routeName} | Řetězec představující trasu rozbočovače okraj. | `routes` Prvek může být existuje, ale je prázdný. |
| storeAndForwardConfiguration.timeToLiveSecs | Doba v sekundách, Edge hub uchovává zprávy v případě odpojené směrování koncových bodů, například odpojen od služby IoT Hub, nebo místní modulu | Ano |

## <a name="edgehub-reported-properties"></a>EdgeHub hlášené vlastnosti

| Vlastnost | Popis |
| -------- | ----------- |
| lastDesiredVersion | Toto celé číslo odkazuje na poslední verzi požadované vlastnosti zpracovává Edge rozbočovače. |
| lastDesiredStatus.code | Toto je kód stavu odkazující na poslední požadované vlastnosti pohledu Edge rozbočovače. Povolené hodnoty: `200` úspěch, `400` neplatná konfigurace `500` se nezdařilo |
| lastDesiredStatus.description | Textový popis stavu |
| Klienti. {identity zařízení nebo modul} .status | Stav tohoto zařízení nebo modul. Možné hodnoty {"připojené" \| "odpojení"}. Pouze modul identit může být v odpojeném stavu. Podřízené zařízení připojující se k rozbočovači hraniční se zobrazí, jenom když připojení. |
| Klienti. {identity zařízení nebo modul} .lastConnectTime | Poslední čas modulu připojení na zařízení |
| Klienti. {identity zařízení nebo modul} .lastDisconnectTime | Čas poslední zařízení nebo modul odpojení |

## <a name="next-steps"></a>Další kroky

Naučte se používat tyto vlastnosti k sestavení se manifesty nasazení, najdete v tématu [pochopit, jak IoT Edge moduly můžete použít, nakonfigurovat a znovu použít](module-composition.md).

<!--links -->
[lnk-deploy]: module-deployment-monitoring.md
[lnk-docker-create-options]: https://docs.docker.com/engine/api/v1.32/#operation/ContainerCreate
[lnk-docker-logging-options]: https://docs.docker.com/engine/admin/logging/overview/
[lnk-iothub-query]: ../iot-hub/iot-hub-devguide-query-language.md
