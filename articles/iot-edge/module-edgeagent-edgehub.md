---
title: Vlastnosti agenta a modulu centra jsou vlákna – Azure IoT Edge
description: Zkontrolujte konkrétním vlastnostem a jejich hodnoty pro edgeAgent a edgeHub dvojčaty modulů
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 06/17/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 4684daf2a1095a40c478170be37edcae788868ef
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/13/2020
ms.locfileid: "79284834"
---
# <a name="properties-of-the-iot-edge-agent-and-iot-edge-hub-module-twins"></a>Vlastnosti IoT Edge agenta a modulu IoT Edge centra pro vlákna

Agent IoT Edge a centrum IoT Edge jsou dva moduly, které tvoří modul runtime IoT Edge. Další informace o odpovědnostech jednotlivých modulů modulu runtime naleznete v tématu [pochopení Azure IoT Edge runtime a jeho architektury](iot-edge-runtime.md).

Tento článek obsahuje požadované vlastnosti a ohlášených vlastností dvojčat modulů runtime. Další informace o tom, jak nasadit moduly na zařízeních IoT Edge, najdete v tématu [Naučte se nasazovat moduly a navázat trasy v IoT Edge](module-composition.md).

Nevlákenný modul obsahuje:

* **Požadované vlastnosti**. Back-end řešení může nastavit požadované vlastnosti a modul je může číst. Modul může také přijímat oznámení o změnách v požadovaných vlastnostech. Požadované vlastnosti se používají spolu s nahlášenými vlastnostmi pro synchronizaci konfigurace nebo podmínek modulu.

* **Hlášené vlastnosti**. Modul může nastavit hlášené vlastnosti a back-end řešení může číst a dotazovat je. Hlášené vlastnosti se používají spolu s požadovanými vlastnostmi pro synchronizaci konfigurace nebo podmínek modulu.

## <a name="edgeagent-desired-properties"></a>EdgeAgent požadované vlastnosti

Modul je pro agenta IoT Edge se nazývá `$edgeAgent` a koordinuje komunikaci mezi agentem IoT Edge spuštěným na zařízení a IoT Hub. Požadované vlastnosti nastavené při použití manifestu nasazení na konkrétní zařízení jako součást nasazení jednoho zařízení nebo ve velkém měřítku.

| Vlastnost | Popis | Požadováno |
| -------- | ----------- | -------- |
| schemaVersion | Musí být "1.0" | Ano |
| Runtime.Type | Musí být "docker" | Ano |
| runtime.settings.minDockerVersion | Nastavte na tento manifest nasazení požadavek na minimální verzi Dockeru | Ano |
| runtime.settings.loggingOptions | Dokument JSON obsahující možnosti protokolování pro kontejner agenta IoT Edge. [Možnosti protokolování Docker](https://docs.docker.com/engine/admin/logging/overview/) | Ne |
| runtime.settings.registryCredentials<br>. .username {registryId} | Uživatelské jméno registru kontejneru. Pro službu Azure Container Registry uživatelské jméno je obvykle název registru.<br><br> Přihlašovací údaje registru jsou nezbytné pro všechny image privátních modulů. | Ne |
| runtime.settings.registryCredentials<br>. .password {registryId} | Heslo pro registr kontejneru. | Ne |
| runtime.settings.registryCredentials<br>. xlDown {registryId} | Adresa registru kontejneru. Pro Azure Container Registry adresa je obvykle *{název registru}. azurecr. IO*. | Ne |  
| systemModules.edgeAgent.type | Musí být "docker" | Ano |
| systemModules.edgeAgent.settings.image | Identifikátor URI obrázku agenta IoT Edge. V současné době se agent IoT Edge nemůže sám aktualizovat. | Ano |
| systemModules.edgeAgent.settings<br>.createOptions | Dokument JSON obsahující možnosti pro vytvoření kontejneru agenta IoT Edge. [Možnosti vytvoření Docker](https://docs.docker.com/engine/api/v1.32/#operation/ContainerCreate) | Ne |
| systemModules.edgeAgent.configuration.id | ID nasazení, které nasadit tento modul. | IoT Hub tuto vlastnost nastavuje při použití manifestu pomocí nasazení. Není součástí manifestu nasazení. |
| systemModules.edgeHub.type | Musí být "docker" | Ano |
| systemModules.edgeHub.status | Musí být "spuštěno" | Ano |
| systemModules.edgeHub.restartPolicy | Musí být "always" | Ano |
| systemModules.edgeHub.settings.image | Identifikátor URI obrázku IoT Edgeového centra | Ano |
| systemModules.edgeHub.settings<br>.createOptions | Dokument JSON obsahující možnosti pro vytvoření kontejneru centra IoT Edge. [Možnosti vytvoření Docker](https://docs.docker.com/engine/api/v1.32/#operation/ContainerCreate) | Ne |
| systemModules.edgeHub.configuration.id | ID nasazení, které nasadit tento modul. | IoT Hub tuto vlastnost nastavuje při použití manifestu pomocí nasazení. Není součástí manifestu nasazení. |
| moduly. {ID modulu} .version | Uživatelem definovaný řetězec představující verze tohoto modulu. | Ano |
| modules.{moduleId}.type | Musí být "docker" | Ano |
| moduly. {ID modulu} .status | {"spuštěný" \| "zastaveno"} | Ano |
| moduly. {ID modulu} .restartPolicy | {"nikdy" \| "při selhání" \| "on-špatný" \| "Always"} | Ano |
| Aktualizuj. {moduleId}. imagePullPolicy | {"on-Create" \| nikdy "} | Ne |
| modules.{moduleId}.settings.image | Identifikátor URI bitové kopie modulu. | Ano |
| modules.{moduleId}.settings.createOptions | Převedený na řetězec formátu JSON obsahující požadované možnosti pro vytvoření kontejneru modulu. [Možnosti vytvoření Docker](https://docs.docker.com/engine/api/v1.32/#operation/ContainerCreate) | Ne |
| modules.{moduleId}.configuration.id | ID nasazení, které nasadit tento modul. | IoT Hub tuto vlastnost nastavuje při použití manifestu pomocí nasazení. Není součástí manifestu nasazení. |

## <a name="edgeagent-reported-properties"></a>EdgeAgent ohlášené vlastnosti

Agent IoT Edge nahlásil vlastnosti obsahují tři hlavní informace:

1. Stav aplikace zobrazí poslední požadované vlastnosti;
2. Stav modulů aktuálně spuštěných v zařízení, jak je uvedeno v agentovi IoT Edgee; ani
3. Kopírování požadovaných vlastností aktuálně spuštěného v příslušném zařízení.

Kopie aktuálních požadovaných vlastností je užitečná pro zjištění, zda zařízení používalo nejnovější nasazení nebo stále používá předchozí manifest nasazení.

> [!NOTE]
> Hlášené vlastnosti agenta IoT Edge jsou užitečné, protože se dají dotazovat pomocí [dotazovacího jazyka IoT Hub](../iot-hub/iot-hub-devguide-query-language.md) a prozkoumat stav nasazení ve velkém měřítku. Další informace o tom, jak používat IoT Edge vlastnosti agenta pro stav, najdete v tématu [pochopení IoT Edge nasazení pro jednotlivá zařízení nebo ve velkém měřítku](module-deployment-monitoring.md).

V následující tabulce nezahrnují informace, který se zkopíruje z požadovaných vlastností.

| Vlastnost | Popis |
| -------- | ----------- |
| lastDesiredVersion | Toto celé číslo odkazuje na poslední verzi požadovaných vlastností zpracovaných agentem IoT Edge. |
| lastDesiredStatus.code | Tento stavový kód odkazuje na poslední požadované vlastnosti, které uvidí agent IoT Edge. Povolené hodnoty: `200` úspěch, `400` neplatná konfigurace, `412` neplatná verze schématu `417` požadované vlastnosti jsou prázdné, `500` se nezdařilo. |
| lastDesiredStatus.description | Textový popis stavu |
| deviceHealth | `healthy`, zda je běhový stav všech modulů buď `running` nebo `stopped`, `unhealthy` jinak |
| configurationHealth. .health {deploymentId} | `healthy`, jestli stav modulu runtime pro všechny moduly nastavené nasazením {deploymentId} je buď `running` nebo `stopped`, `unhealthy` jinak. |
| runtime.platform.OS | Vytváření sestav operační systém, na zařízení |
| Runtime.Platform.Architecture | Vyvářet architektura procesoru zařízení |
| systemModules.edgeAgent.runtimeStatus | Stav hlášené IoT Edge agenta: {"spuštěná" \| "není v pořádku"} |
| systemModules.edgeAgent.statusDescription | Popis stavu hlášeného agenta IoT Edge. |
| systemModules.edgeHub.runtimeStatus | Stav IoT Edgeového centra: {"runninged" \| "Stopped" \| "neúspěšné" \| "omezení rychlosti" \| "není v pořádku"} |
| systemModules.edgeHub.statusDescription | Textový popis stavu centra IoT Edge, pokud není v pořádku. |
| systemModules.edgeHub.exitCode | Ukončovací kód, který je hlášen kontejnerem centra IoT Edge v případě ukončení kontejneru |
| systemModules.edgeHub.startTimeUtc | Čas posledního spuštění centra IoT Edge |
| systemModules.edgeHub.lastExitTimeUtc | Čas posledního ukončení IoT Edgeho centra |
| systemModules.edgeHub.lastRestartTimeUtc | Čas, kdy byl naposledy restartován IoT Edge hub |
| systemModules.edgeHub.restartCount | Počet pokusů, které tento modul se restartoval jako součást zásady restartování. |
| modules.{moduleId}.runtimeStatus | Stav modulu: {"Running" \| "zastaveno" \| "neúspěšné" \| "omezení rychlosti" \| "není v pořádku"} |
| modules.{moduleId}.statusDescription | Textový popis stavu modulu, pokud není v pořádku. |
| modules.{moduleId}.exitCode | Ukončovací kód hlášených kontejnerem modulu, pokud se kontejner ukončí |
| modules.{moduleId}.startTimeUtc | Čas posledního zahájení modulu |
| modules.{moduleId}.lastExitTimeUtc | Čas, kdy naposledy ukončil modulu |
| modules.{moduleId}.lastRestartTimeUtc | Čas poslední restartováním modulu |
| moduly. {ID modulu} .restartCount | Počet pokusů, které tento modul se restartoval jako součást zásady restartování. |

## <a name="edgehub-desired-properties"></a>EdgeHub požadované vlastnosti

Nefunkční modul pro Centrum IoT Edge se nazývá `$edgeHub` a koordinuje komunikaci mezi IoT Edgem rozbočovačem běžícím na zařízení a IoT Hub. Požadované vlastnosti nastavené při použití manifestu nasazení na konkrétní zařízení jako součást nasazení jednoho zařízení nebo ve velkém měřítku.

| Vlastnost | Popis | V manifestu nasazení vyžaduje |
| -------- | ----------- | -------- |
| schemaVersion | Musí být "1.0" | Ano |
| trasy. {routeName} | Řetězec představující trasu centra IoT Edge. Další informace naleznete v tématu [Declare Routes](module-composition.md#declare-routes). | Element `routes` může být přítomen, ale prázdný. |
| storeAndForwardConfiguration.timeToLiveSecs | Čas v sekundách, po který IoT Edge hub udržuje zprávy, pokud je odpojený od koncových bodů směrování, ať už IoT Hub nebo místní modul. Hodnota může být libovolné kladné celé číslo. | Ano |

## <a name="edgehub-reported-properties"></a>EdgeHub ohlášené vlastnosti

| Vlastnost | Popis |
| -------- | ----------- |
| lastDesiredVersion | Toto celé číslo odkazuje na poslední verzi požadovaných vlastností zpracovaných centrem IoT Edge. |
| lastDesiredStatus.code | Stavový kód odkazuje na poslední požadované vlastnosti, které vidí centrum IoT Edge. Povolené hodnoty: `200` úspěch, `400` neplatná konfigurace, `500` chyba |
| lastDesiredStatus.description | Textový popis stavu |
| Klienti. .status {zařízení nebo moduleId} | Stav tohoto zařízení nebo modulu. Možné hodnoty: {"připojeno" \| "Odpojeno"}. Pouze modul identit může být v odpojeném stavu. Podřízená zařízení připojující se k rozbočovači IoT Edge se zobrazí pouze v případě připojení. |
| Klienti. .lastConnectTime {zařízení nebo moduleId} | Čas posledního připojení zařízení nebo modulu |
| Klienti. .lastDisconnectTime {zařízení nebo moduleId} | Čas posledního odpojení zařízení nebo modulu. |

## <a name="next-steps"></a>Další kroky

Informace o tom, jak tyto vlastnosti použít k sestavení manifestů nasazení, najdete v tématu [Vysvětlení způsobu použití, konfigurace a](module-composition.md)opětovného použití modulů IoT Edge.
