---
title: IoT EdgeAgent a EdgeHub referenční informace k Azure | Dokumentace Microsoftu
description: Zkontrolujte konkrétním vlastnostem a jejich hodnoty pro edgeAgent a edgeHub dvojčaty modulů
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 09/21/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: ecc48adfeef30a777ae4d96c9b996c8bcdfea12d
ms.sourcegitcommit: 1d3353b95e0de04d4aec2d0d6f84ec45deaaf6ae
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2018
ms.locfileid: "50247806"
---
# <a name="properties-of-the-edge-agent-and-edge-hub-module-twins"></a>Vlastnosti agenta Edge a dvojčaty modulů Centrum Edge

Edge agent a Centrum Edge jsou dva moduly, které tvoří modul runtime IoT Edge. Další informace o jaké povinnosti každý modul provádí, najdete v části [pochopení runtime Azure IoT Edge a jeho architektura](iot-edge-runtime.md). 

Tento článek obsahuje požadované vlastnosti a ohlášených vlastností dvojčat modulů runtime. Další informace o tom, jak nasadit moduly na hraniční zařízení IoT, najdete v části [nasazení a monitorování](module-deployment-monitoring.md).

## <a name="edgeagent-desired-properties"></a>EdgeAgent požadované vlastnosti

Dvojče modulu pro agenta Edge se nazývá `$edgeAgent` a koordinuje komunikaci mezi se agent Edge běží na zařízení a služby IoT Hub. Požadované vlastnosti nastavené při použití manifestu nasazení na konkrétní zařízení jako součást nasazení jednoho zařízení nebo ve velkém měřítku. 

| Vlastnost | Popis | Požaduje se |
| -------- | ----------- | -------- |
| schemaVersion | Musí být "1.0" | Ano |
| Runtime.Type | Musí být "docker" | Ano |
| runtime.settings.minDockerVersion | Nastavte na tento manifest nasazení požadavek na minimální verzi Dockeru | Ano |
| runtime.settings.loggingOptions | Převedený na řetězec formátu JSON obsahující možnosti protokolování pro kontejner agenta Edge. [Možnosti protokolování dockeru](https://docs.docker.com/engine/admin/logging/overview/) | Ne |
| runtime.settings.registryCredentials<br>. .username {registryId} | Uživatelské jméno registru kontejneru. Pro službu Azure Container Registry uživatelské jméno je obvykle název registru.<br><br> Přihlašovací údaje registru jsou nezbytné pro všechny bitové kopie modulu, které nejsou veřejné. | Ne |
| runtime.settings.registryCredentials<br>. .password {registryId} | Heslo pro registr kontejneru. | Ne |
| runtime.settings.registryCredentials<br>. xlDown {registryId} | Adresa registru kontejneru. Pro službu Azure Container Registry, je obvykle adresa *.azurecr.IO {registryname}*. | Ne |  
| systemModules.edgeAgent.type | Musí být "docker" | Ano |
| systemModules.edgeAgent.settings.image | Identifikátor URI image agenta Edge. Edge agent v současné době není možné aktualizovalo samo. | Ano |
| systemModules.edgeAgent.settings<br>.createOptions | Převedený na řetězec formátu JSON obsahující požadované možnosti pro vytvoření kontejneru agenta Edge. [Možnosti vytvoření dockeru](https://docs.docker.com/engine/api/v1.32/#operation/ContainerCreate) | Ne |
| systemModules.edgeAgent.configuration.id | ID nasazení, které nasadit tento modul. | Tato vlastnost nastavena ve službě IoT Hub, při použití tohoto manifestu použitím modelu nasazení. Není součástí manifestu nasazení. |
| systemModules.edgeHub.type | Musí být "docker" | Ano |
| systemModules.edgeHub.status | Musí být "spuštěno" | Ano |
| systemModules.edgeHub.restartPolicy | Musí být "always" | Ano |
| systemModules.edgeHub.settings.image | Identifikátor URI image Centrum Edge. | Ano |
| systemModules.edgeHub.settings<br>.createOptions | Převedený na řetězec formátu JSON obsahující požadované možnosti pro vytvoření kontejneru Edge hub. [Možnosti vytvoření dockeru](https://docs.docker.com/engine/api/v1.32/#operation/ContainerCreate) | Ne |
| systemModules.edgeHub.configuration.id | ID nasazení, které nasadit tento modul. | Tato vlastnost nastavena ve službě IoT Hub, při použití tohoto manifestu použitím modelu nasazení. Není součástí manifestu nasazení. |
| moduly. {ID modulu} .version | Uživatelem definovaný řetězec představující verze tohoto modulu. | Ano |
| modules.{moduleId}.type | Musí být "docker" | Ano |
| moduly. {ID modulu} .status | {"spuštěno" \| "zastavena"} | Ano |
| moduly. {ID modulu} .restartPolicy | {"nikdy" \| "na-se nezdařilo" \| "na-není v pořádku" \| "always"} | Ano |
| modules.{moduleId}.settings.image | Identifikátor URI bitové kopie modulu. | Ano |
| modules.{moduleId}.settings.createOptions | Převedený na řetězec formátu JSON obsahující požadované možnosti pro vytvoření kontejneru modulu. [Možnosti vytvoření dockeru](https://docs.docker.com/engine/api/v1.32/#operation/ContainerCreate) | Ne |
| modules.{moduleId}.configuration.id | ID nasazení, které nasadit tento modul. | Tato vlastnost nastavena ve službě IoT Hub, při použití tohoto manifestu použitím modelu nasazení. Není součástí manifestu nasazení. |

## <a name="edgeagent-reported-properties"></a>EdgeAgent ohlášené vlastnosti

Edge agent ohlásil, že vlastnosti zahrnují tři hlavní údaje:

1. Stav aplikace zobrazí poslední požadované vlastnosti;
2. Stav modulů aktuálně spuštěného v příslušném zařízení, jak je hlásí pomocí agenta Edge; a
3. Kopírování požadovaných vlastností aktuálně spuštěného v příslušném zařízení.

Tento poslední část informací je užitečné v případě nejnovější požadované vlastnosti nejsou úspěšně použity modulem runtime a zařízení je stále spuštěn předchozí manifestu nasazení.

> [!NOTE]
> Ohlášené vlastnosti se agent Edge jsou užitečné, protože může být dotázán pomocí [dotazovací jazyk služby IoT Hub](../iot-hub/iot-hub-devguide-query-language.md) prozkoumat stav nasazení ve velkém měřítku. Další informace o tom, jak používat vlastnosti agenta Edge pro stav najdete v tématu [vysvětlení nasazení IoT Edge pro jednotlivá zařízení nebo ve velkém měřítku](module-deployment-monitoring.md).

V následující tabulce nezahrnují informace, který se zkopíruje z požadovaných vlastností.

| Vlastnost | Popis |
| -------- | ----------- |
| lastDesiredVersion | Toto celé číslo odkazuje na poslední verzi požadované vlastnosti zpracovává se agent Edge. |
| lastDesiredStatus.code | Toto je stavový kód odkazuje na poslední požadované vlastnosti detekovala se agent Edge. Povolené hodnoty: `200` úspěch, `400` neplatná konfigurace `412` neplatná verze schématu, `417` požadované vlastnosti jsou prázdné, `500` se nezdařilo |
| lastDesiredStatus.description | Textový popis stavu |
| deviceHealth | `healthy` Pokud je stav modulu runtime všech modulů, buď `running` nebo `stopped`, `unhealthy` jinak |
| configurationHealth. .health {deploymentId} | `healthy` Pokud je stav modulu runtime všech modulů nastavil nasazení {deploymentId} buď `running` nebo `stopped`, `unhealthy` jinak |
| runtime.platform.OS | Vytváření sestav operační systém, na zařízení |
| Runtime.Platform.Architecture | Vyvářet architektura procesoru zařízení |
| systemModules.edgeAgent.runtimeStatus | Reportovaný stav agenta Edge: {"spuštěno" \| "není v pořádku"} |
| systemModules.edgeAgent.statusDescription | Textový popis reportovaný stav agenta Edge. |
| systemModules.edgeHub.runtimeStatus | Aktuální stav Centrum Edge: {"spuštěno" \| "zastavena" \| "se nezdařilo" \| "omezení rychlosti" \| "není v pořádku"} |
| systemModules.edgeHub.statusDescription | Textový popis aktuální stav Centrum Edge, pokud není v pořádku. |
| systemModules.edgeHub.exitCode | Pokud byl ukončen, ukončovací kód hlášených kontejner centra Edge |
| systemModules.edgeHub.startTimeUtc | Čas posledního zahájení Centrum Edge |
| systemModules.edgeHub.lastExitTimeUtc | Čas, kdy naposledy ukončil Centrum Edge |
| systemModules.edgeHub.lastRestartTimeUtc | Čas poslední restartováním Centrum Edge |
| systemModules.edgeHub.restartCount | Počet pokusů, které tento modul se restartoval jako součást zásady restartování. |
| modules.{moduleId}.runtimeStatus | Aktuální stav modulu: {"spuštěno" \| "zastavena" \| "se nezdařilo" \| "omezení rychlosti" \| "není v pořádku"} |
| modules.{moduleId}.statusDescription | Textový popis aktuální stav modulu, pokud není v pořádku. |
| modules.{moduleId}.exitCode | Pokud byl ukončen, ukončovací kód hlášených modulu container |
| modules.{moduleId}.startTimeUtc | Čas posledního zahájení modulu |
| modules.{moduleId}.lastExitTimeUtc | Čas, kdy naposledy ukončil modulu |
| modules.{moduleId}.lastRestartTimeUtc | Čas poslední restartováním modulu |
| moduly. {ID modulu} .restartCount | Počet pokusů, které tento modul se restartoval jako součást zásady restartování. |

## <a name="edgehub-desired-properties"></a>EdgeHub požadované vlastnosti

Dvojče modulu pro Centrum Edge se nazývá `$edgeHub` a koordinuje komunikaci mezi Centrum Edge běží na zařízení a služby IoT Hub. Požadované vlastnosti nastavené při použití manifestu nasazení na konkrétní zařízení jako součást nasazení jednoho zařízení nebo ve velkém měřítku. 

| Vlastnost | Popis | V manifestu nasazení vyžaduje |
| -------- | ----------- | -------- |
| schemaVersion | Musí být "1.0" | Ano |
| trasy. {routeName} | Řetězec představující trasy Edge hub. | `routes` Element může být existuje, ale je prázdný. |
| storeAndForwardConfiguration.timeToLiveSecs | Čas v sekundách, které Centrum Edge udržuje zprávy v případě odpojené směrování koncových bodů, například odpojen od služby IoT Hub nebo místní modul | Ano |

## <a name="edgehub-reported-properties"></a>EdgeHub ohlášené vlastnosti

| Vlastnost | Popis |
| -------- | ----------- |
| lastDesiredVersion | Toto celé číslo odkazuje na poslední verzi požadované vlastnosti zpracovány Centrum Edge. |
| lastDesiredStatus.code | Toto je stavový kód odkazuje na poslední požadovaných vlastností zjištěných Centrum Edge. Povolené hodnoty: `200` úspěch, `400` neplatná konfigurace `500` se nezdařilo |
| lastDesiredStatus.description | Textový popis stavu |
| Klienti. .status {zařízení nebo moduleId} | Stav tohoto zařízení nebo modulu. Možné hodnoty {"připojeno" \| "odpojené"}. Pouze modul identit může být v odpojeném stavu. Příjem dat zařízení připojující se k Centrum Edge zobrazí pouze v případě, že připojení. |
| Klienti. .lastConnectTime {zařízení nebo moduleId} | Čas poslední připojeným na zařízení |
| Klienti. .lastDisconnectTime {zařízení nebo moduleId} | Čas posledního zařízení nebo modul odpojen |

## <a name="next-steps"></a>Další postup

Další informace o použití těchto vlastností k sestavení manifestu nasazení, najdete v článku [pochopit, jak můžete použít moduly IoT Edge a způsob jejich konfiguraci a znovu použít](module-composition.md).
