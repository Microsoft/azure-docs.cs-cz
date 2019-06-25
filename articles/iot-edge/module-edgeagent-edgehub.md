---
title: EdgeAgent a EdgeHub požadovaného referenční dokumentace k vlastnostem – Azure IoT Edge | Dokumentace Microsoftu
description: Zkontrolujte konkrétním vlastnostem a jejich hodnoty pro edgeAgent a edgeHub dvojčaty modulů
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 06/17/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: e8a8170023c8f529894522e27a4c6231325089af
ms.sourcegitcommit: 156b313eec59ad1b5a820fabb4d0f16b602737fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/18/2019
ms.locfileid: "67190982"
---
# <a name="properties-of-the-iot-edge-agent-and-iot-edge-hub-module-twins"></a>Vlastnosti agenta IoT Edge a dvojčaty modulů IoT Edge hub

Agenta IoT Edge a Centrum IoT Edge jsou dva moduly, které tvoří modul runtime IoT Edge. Další informace o jaké povinnosti každý modul provádí, najdete v části [pochopení runtime Azure IoT Edge a jeho architektura](iot-edge-runtime.md). 

Tento článek obsahuje požadované vlastnosti a ohlášených vlastností dvojčat modulů runtime. Další informace o tom, jak nasadit moduly na hraniční zařízení IoT, najdete v části [zjistěte, jak nasadit moduly a vytvářet ve službě IoT Edge](module-composition.md).

Dvojče modulu zahrnuje: 

* **Požadované vlastnosti**. Back-end řešení může nastavte požadované vlastnosti a v modulu najdete. V modulu mohou také přijímat oznámení o změnách v požadované vlastnosti. Požadované vlastnosti se používají společně s ohlášené vlastnosti k synchronizaci konfigurace modulu nebo podmínky.

* **Ohlášené vlastnosti**. Modul můžete nastavit ohlášené vlastnosti, a back-end řešení může číst a jejich dotazování. Ohlášené vlastnosti se používají společně s požadované vlastnosti pro synchronizaci konfigurace modulu nebo podmínky. 

## <a name="edgeagent-desired-properties"></a>EdgeAgent požadované vlastnosti

Dvojče modulu pro agenta IoT Edge se nazývá `$edgeAgent` a koordinuje komunikaci mezi IoT Edge agenta spuštěného na mobilních a IoT Hub. Požadované vlastnosti nastavené při použití manifestu nasazení na konkrétní zařízení jako součást nasazení jednoho zařízení nebo ve velkém měřítku. 

| Vlastnost | Popis | Požaduje se |
| -------- | ----------- | -------- |
| schemaVersion | Musí být "1.0" | Ano |
| Runtime.Type | Musí být "docker" | Ano |
| runtime.settings.minDockerVersion | Nastavte na tento manifest nasazení požadavek na minimální verzi Dockeru | Ano |
| runtime.settings.loggingOptions | Převedený na řetězec formátu JSON obsahující možnosti protokolování pro kontejner agenta IoT Edge. [Možnosti protokolování dockeru](https://docs.docker.com/engine/admin/logging/overview/) | Ne |
| runtime.settings.registryCredentials<br>. .username {registryId} | Uživatelské jméno registru kontejneru. Pro službu Azure Container Registry uživatelské jméno je obvykle název registru.<br><br> Přihlašovací údaje registru jsou nezbytné pro všechny bitové kopie modulu, které nejsou veřejné. | Ne |
| runtime.settings.registryCredentials<br>. .password {registryId} | Heslo pro registr kontejneru. | Ne |
| runtime.settings.registryCredentials<br>. xlDown {registryId} | Adresa registru kontejneru. Pro službu Azure Container Registry, je obvykle adresa *.azurecr.IO {název registru}* . | Ne |  
| systemModules.edgeAgent.type | Musí být "docker" | Ano |
| systemModules.edgeAgent.settings.image | Identifikátor URI image agenta IoT Edge. V současné době není schopen aktualizovat agenta IoT Edge. | Ano |
| systemModules.edgeAgent.settings<br>.createOptions | Převedený na řetězec formátu JSON obsahující požadované možnosti pro vytvoření agentů kontejneru IoT Edge. [Možnosti vytvoření dockeru](https://docs.docker.com/engine/api/v1.32/#operation/ContainerCreate) | Ne |
| systemModules.edgeAgent.configuration.id | ID nasazení, které nasadit tento modul. | IoT Hub nastaví tuto vlastnost při použití manifest použitím modelu nasazení. Není součástí manifestu nasazení. |
| systemModules.edgeHub.type | Musí být "docker" | Ano |
| systemModules.edgeHub.status | Musí být "spuštěno" | Ano |
| systemModules.edgeHub.restartPolicy | Musí být "always" | Ano |
| systemModules.edgeHub.settings.image | Identifikátor URI image Centrum IoT Edge. | Ano |
| systemModules.edgeHub.settings<br>.createOptions | Převedený na řetězec formátu JSON obsahující požadované možnosti pro vytvoření kontejneru centra IoT Edge. [Možnosti vytvoření dockeru](https://docs.docker.com/engine/api/v1.32/#operation/ContainerCreate) | Ne |
| systemModules.edgeHub.configuration.id | ID nasazení, které nasadit tento modul. | IoT Hub nastaví tuto vlastnost při použití manifest použitím modelu nasazení. Není součástí manifestu nasazení. |
| moduly. {ID modulu} .version | Uživatelem definovaný řetězec představující verze tohoto modulu. | Ano |
| modules.{moduleId}.type | Musí být "docker" | Ano |
| moduly. {ID modulu} .status | {"spuštěno" \| "zastavena"} | Ano |
| moduly. {ID modulu} .restartPolicy | {"nikdy" \| "na chybu" \| "na-není v pořádku" \| "always"} | Ano |
| modules.{moduleId}.settings.image | Identifikátor URI bitové kopie modulu. | Ano |
| modules.{moduleId}.settings.createOptions | Převedený na řetězec formátu JSON obsahující požadované možnosti pro vytvoření kontejneru modulu. [Možnosti vytvoření dockeru](https://docs.docker.com/engine/api/v1.32/#operation/ContainerCreate) | Ne |
| modules.{moduleId}.configuration.id | ID nasazení, které nasadit tento modul. | IoT Hub nastaví tuto vlastnost při použití manifest použitím modelu nasazení. Není součástí manifestu nasazení. |

## <a name="edgeagent-reported-properties"></a>EdgeAgent ohlášené vlastnosti

Agenta IoT Edge ohlásil, že vlastnosti zahrnují tři hlavní údaje:

1. Stav aplikace zobrazí poslední požadované vlastnosti;
2. Stav modulů aktuálně spuštěného v příslušném zařízení, jak je hlásí pomocí agenta IoT Edge; a
3. Kopírování požadovaných vlastností aktuálně spuštěného v příslušném zařízení.

Tento poslední údaj, kopii aktuální požadované vlastnosti, je užitečné zjistit, jestli zařízení má použít nejnovější požadované vlastnosti nebo jestli stále běží předchozí manifestu nasazení.

> [!NOTE]
> Ohlášené vlastnosti agenta IoT Edge jsou užitečné, protože může být dotázán pomocí [dotazovací jazyk služby IoT Hub](../iot-hub/iot-hub-devguide-query-language.md) prozkoumat stav nasazení ve velkém měřítku. Další informace o tom, jak používat vlastnosti agenta IoT Edge pro stav najdete v tématu [vysvětlení nasazení IoT Edge pro jednotlivá zařízení nebo ve velkém měřítku](module-deployment-monitoring.md).

V následující tabulce nezahrnují informace, který se zkopíruje z požadovaných vlastností.

| Vlastnost | Popis |
| -------- | ----------- |
| lastDesiredVersion | Toto celé číslo odkazuje na poslední verzi požadované vlastnosti zpracování agentem IoT Edge. |
| lastDesiredStatus.code | Tímto stavovým kódem odkazuje na poslední požadovaných vlastností zjištěných agenta IoT Edge. Povolené hodnoty: `200` Úspěch, `400` neplatná konfigurace `412` neplatná verze schématu, `417` požadované vlastnosti jsou prázdné, `500` se nezdařilo |
| lastDesiredStatus.description | Textový popis stavu |
| deviceHealth | `healthy` Pokud je stav modulu runtime všech modulů, buď `running` nebo `stopped`, `unhealthy` jinak |
| configurationHealth. .health {deploymentId} | `healthy` Pokud je stav modulu runtime všech modulů nastavil nasazení {deploymentId} buď `running` nebo `stopped`, `unhealthy` jinak |
| runtime.platform.OS | Vytváření sestav operační systém, na zařízení |
| Runtime.Platform.Architecture | Vyvářet architektura procesoru zařízení |
| systemModules.edgeAgent.runtimeStatus | Reportovaný stav agenta IoT Edge: {"spuštěno" \| "není v pořádku"} |
| systemModules.edgeAgent.statusDescription | Textový popis reportovaný stav agenta IoT Edge. |
| systemModules.edgeHub.runtimeStatus | Stav centra IoT Edge: {"spuštěno" \| "zastavena" \| "se nezdařilo" \| "omezení rychlosti" \| "není v pořádku"} |
| systemModules.edgeHub.statusDescription | Textový popis stavu centra IoT Edge, pokud není v pořádku. |
| systemModules.edgeHub.exitCode | Ukončovací kód ohlášených kontejner centra IoT Edge, pokud ukončení kontejneru |
| systemModules.edgeHub.startTimeUtc | Čas posledního zahájení Centrum IoT Edge |
| systemModules.edgeHub.lastExitTimeUtc | Čas, kdy naposledy ukončil Centrum IoT Edge |
| systemModules.edgeHub.lastRestartTimeUtc | Čas poslední restartováním Centrum IoT Edge |
| systemModules.edgeHub.restartCount | Počet pokusů, které tento modul se restartoval jako součást zásady restartování. |
| modules.{moduleId}.runtimeStatus | Stav modulu: {"spuštěno" \| "zastavena" \| "se nezdařilo" \| "omezení rychlosti" \| "není v pořádku"} |
| modules.{moduleId}.statusDescription | Textový popis stavu modulu, pokud není v pořádku. |
| modules.{moduleId}.exitCode | Ukončovací kód ohlášených kontejner modulu, pokud ukončení kontejneru |
| modules.{moduleId}.startTimeUtc | Čas posledního zahájení modulu |
| modules.{moduleId}.lastExitTimeUtc | Čas, kdy naposledy ukončil modulu |
| modules.{moduleId}.lastRestartTimeUtc | Čas poslední restartováním modulu |
| moduly. {ID modulu} .restartCount | Počet pokusů, které tento modul se restartoval jako součást zásady restartování. |

## <a name="edgehub-desired-properties"></a>EdgeHub požadované vlastnosti

Dvojče modulu pro Centrum IoT Edge se nazývá `$edgeHub` a koordinuje komunikaci mezi centra IoT Edge běží na zařízení a služby IoT Hub. Požadované vlastnosti nastavené při použití manifestu nasazení na konkrétní zařízení jako součást nasazení jednoho zařízení nebo ve velkém měřítku. 

| Vlastnost | Popis | V manifestu nasazení vyžaduje |
| -------- | ----------- | -------- |
| schemaVersion | Musí být "1.0" | Ano |
| trasy. {routeName} | Řetězec představující trasy centra IoT Edge. Další informace najdete v tématu [trasy deklarovat](module-composition.md#declare-routes). | `routes` Element může být existuje, ale je prázdný. |
| storeAndForwardConfiguration.timeToLiveSecs | Čas v sekundách tohoto centra IoT Edge zajišťuje zprávy, pokud odpojená od směrování koncových bodů, zda služby IoT Hub nebo místní modul. Hodnota může být libovolné kladné celé číslo. | Ano |

## <a name="edgehub-reported-properties"></a>EdgeHub ohlášené vlastnosti

| Vlastnost | Popis |
| -------- | ----------- |
| lastDesiredVersion | Toto celé číslo odkazuje na poslední verzi požadované vlastnosti zpracování ve službě IoT Edge hub. |
| lastDesiredStatus.code | Stavový kód odkazuje na poslední požadovaných vlastností zjištěných Centrum IoT Edge. Povolené hodnoty: `200` Úspěch, `400` neplatná konfigurace `500` se nezdařilo |
| lastDesiredStatus.description | Textový popis stavu. |
| Klienti. .status {zařízení nebo moduleId} | Stav tohoto zařízení nebo modulu. Možné hodnoty {"připojeno" \| "odpojené"}. Pouze modul identit může být v odpojeném stavu. Příjem dat zařízení připojující se k centru IoT Edge zobrazí pouze v případě, že připojení. |
| Klienti. .lastConnectTime {zařízení nebo moduleId} | Čas poslední připojeným na zařízení. |
| Klienti. .lastDisconnectTime {zařízení nebo moduleId} | Čas posledního modulu na zařízení odpojí. |

## <a name="next-steps"></a>Další postup

Další informace o použití těchto vlastností k sestavení manifestu nasazení, najdete v článku [pochopit, jak můžete použít moduly IoT Edge a způsob jejich konfiguraci a znovu použít](module-composition.md).
