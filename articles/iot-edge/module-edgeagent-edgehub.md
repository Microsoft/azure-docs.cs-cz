---
title: Vlastnosti agenta a modulu centra jsou vlákna – Azure IoT Edge
description: Zkontrolujte konkrétní vlastnosti a jejich hodnoty pro vlákna modulu edgeAgent a edgeHub.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 04/16/2021
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 29ec958764f4a464d51f29f4b9c8223d5d7a1760
ms.sourcegitcommit: d3bcd46f71f578ca2fd8ed94c3cdabe1c1e0302d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2021
ms.locfileid: "107576002"
---
# <a name="properties-of-the-iot-edge-agent-and-iot-edge-hub-module-twins"></a>Vlastnosti IoT Edge agenta a modulu IoT Edge centra pro vlákna

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

Agent IoT Edge a centrum IoT Edge jsou dva moduly, které tvoří modul runtime IoT Edge. Další informace o odpovědnostech jednotlivých modulů modulu runtime naleznete v tématu [pochopení Azure IoT Edge runtime a jeho architektury](iot-edge-runtime.md).

Tento článek poskytuje požadované vlastnosti a hlášené vlastnosti vláken modulu runtime. Další informace o tom, jak nasadit moduly na zařízeních IoT Edge, najdete v tématu [Naučte se nasazovat moduly a navázat trasy v IoT Edge](module-composition.md).

Nevlákenný modul obsahuje:

* **Požadované vlastnosti**. Back-end řešení může nastavit požadované vlastnosti a modul je může číst. Modul může také přijímat oznámení o změnách v požadovaných vlastnostech. Požadované vlastnosti se používají spolu s nahlášenými vlastnostmi pro synchronizaci konfigurace nebo podmínek modulu.

* **Hlášené vlastnosti**. Modul může nastavit hlášené vlastnosti a back-end řešení může číst a dotazovat je. Hlášené vlastnosti se používají spolu s požadovanými vlastnostmi pro synchronizaci konfigurace nebo podmínek modulu.

## <a name="edgeagent-desired-properties"></a>EdgeAgent požadované vlastnosti

Pro agenta IoT Edge je volána `$edgeAgent` nefunkční modul a koordinuje komunikaci mezi agentem IoT Edge spuštěným v zařízení a IoT Hub. Požadované vlastnosti jsou nastaveny při použití manifestu nasazení na určitém zařízení jako součást nasazení v jednom zařízení nebo ve velkém měřítku.

| Vlastnost | Popis | Povinné |
| -------- | ----------- | -------- |
| schemaVersion | Buď "1,0" nebo "1,1". Verze 1,1 byla představena s IoT Edge verzí 1.0.10 a doporučuje se. | Yes |
| Runtime. Type | Musí být "Docker" | Yes |
| Runtime. Settings. minDockerVersion | Nastavte na minimální verzi Docker, kterou vyžaduje tento manifest nasazení. | Yes |
| Runtime. Settings. loggingOptions | Dokument JSON obsahující možnosti protokolování pro kontejner agenta IoT Edge. [Možnosti protokolování Docker](https://docs.docker.com/engine/admin/logging/overview/) | No |
| Runtime. Settings. registryCredentials<br>. {registryId}. username | Uživatelské jméno registru kontejneru. Pro Azure Container Registry je uživatelské jméno obvykle název registru.<br><br> Přihlašovací údaje registru jsou nezbytné pro všechny image privátních modulů. | No |
| Runtime. Settings. registryCredentials<br>. {registryId}. heslo | Heslo pro registr kontejneru | No |
| Runtime. Settings. registryCredentials<br>. {registryId}. adresa | Adresa registru kontejneru. Pro Azure Container Registry adresa je obvykle *{název registru}. azurecr. IO*. | No |  
| systemModules. edgeAgent. Type | Musí být "Docker" | Yes |
| systemModules. edgeAgent. Settings. Image | Identifikátor URI obrázku agenta IoT Edge. V současné době se agent IoT Edge nemůže sám aktualizovat. | Yes |
| systemModules. edgeAgent. Settings<br>.createOptions | Dokument JSON obsahující možnosti pro vytvoření kontejneru agenta IoT Edge. [Možnosti vytvoření Docker](https://docs.docker.com/engine/api/v1.32/#operation/ContainerCreate) | No |
| systemModules.edgeAgent.configuration.id | ID nasazení, které tento modul nasadilo. | IoT Hub tuto vlastnost nastavuje při použití manifestu pomocí nasazení. Není součástí manifestu nasazení. |
| systemModules. edgeHub. Type | Musí být "Docker" | Yes |
| systemModules. edgeHub. status | Musí být spuštěno. | Yes |
| systemModules.edgeHub.restartPolicy | Musí být vždycky | Yes |
| systemModules.edgeHub.startupOrder | Celočíselná hodnota, pro kterou má být modul v pořadí spouštění. 0 je první a maximální celé číslo (4294967295) je poslední. Pokud hodnota není zadaná, výchozí hodnota je maximální celé číslo.  | No |
| systemModules. edgeHub. Settings. Image | Identifikátor URI obrázku IoT Edgeového centra | Yes |
| systemModules. edgeHub. Settings<br>.createOptions | Dokument JSON obsahující možnosti pro vytvoření kontejneru centra IoT Edge. [Možnosti vytvoření Docker](https://docs.docker.com/engine/api/v1.32/#operation/ContainerCreate) | No |
| systemModules.edgeHub.configuration.id | ID nasazení, které tento modul nasadilo. | IoT Hub tuto vlastnost nastavuje při použití manifestu pomocí nasazení. Není součástí manifestu nasazení. |
| Aktualizuj. {moduleId}. verze | Uživatelsky definovaný řetězec představující verzi tohoto modulu. | Yes |
| Aktualizuj. {moduleId}. Type | Musí být "Docker" | Yes |
| Aktualizuj. {moduleId}. status | {"Running" \| "zastaveno"} | Yes |
| Aktualizuj. {moduleId}. restartPolicy | {"nikdy" \| "Chyba" při \| nesprávném stavu "" \| Always "} | Yes |
| Aktualizuj. {moduleId}. startupOrder | Celočíselná hodnota, pro kterou má být modul v pořadí spouštění. 0 je první a maximální celé číslo (4294967295) je poslední. Pokud hodnota není zadaná, výchozí hodnota je maximální celé číslo.  | No |
| Aktualizuj. {moduleId}. imagePullPolicy | {"on-Create" \| "nikdy"} | No |
| Aktualizuj. {moduleId}. env | Seznam proměnných prostředí, které se mají předat modulu. Má formát `"<name>": {"value": "<value>"}` | No |
| Aktualizuj. {moduleId}. Settings. Image | Identifikátor URI k imagi modulu | Yes |
| Aktualizuj. {moduleId}. Settings. createOptions | Dokument JSON obsahující možnosti pro vytvoření kontejneru modulu. [Možnosti vytvoření Docker](https://docs.docker.com/engine/api/v1.32/#operation/ContainerCreate) | No |
| Aktualizuj. {moduleId}. Configuration. ID | ID nasazení, které tento modul nasadilo. | IoT Hub tuto vlastnost nastavuje při použití manifestu pomocí nasazení. Není součástí manifestu nasazení. |

## <a name="edgeagent-reported-properties"></a>EdgeAgent hlášené vlastnosti

Agent IoT Edge nahlásil vlastnosti obsahují tři hlavní informace:

1. Stav aplikace naposledy zobrazených požadovaných vlastností;
2. Stav modulů aktuálně spuštěných v zařízení, jak je uvedeno v agentovi IoT Edgee; ani
3. Kopie požadovaných vlastností aktuálně spuštěných v zařízení.

Kopie aktuálních požadovaných vlastností je užitečná pro zjištění, zda zařízení používalo nejnovější nasazení nebo stále používá předchozí manifest nasazení.

> [!NOTE]
> Hlášené vlastnosti agenta IoT Edge jsou užitečné, protože se dají dotazovat pomocí [dotazovacího jazyka IoT Hub](../iot-hub/iot-hub-devguide-query-language.md) a prozkoumat stav nasazení ve velkém měřítku. Další informace o tom, jak používat IoT Edge vlastnosti agenta pro stav, najdete v tématu [pochopení IoT Edge nasazení pro jednotlivá zařízení nebo ve velkém měřítku](module-deployment-monitoring.md).

Následující tabulka neobsahuje informace, které jsou zkopírovány z požadovaných vlastností.

| Vlastnost | Popis |
| -------- | ----------- |
| lastDesiredVersion | Toto celé číslo odkazuje na poslední verzi požadovaných vlastností zpracovaných agentem IoT Edge. |
| lastDesiredStatus. Code | Tento stavový kód odkazuje na poslední požadované vlastnosti, které uvidí agent IoT Edge. Povolené hodnoty: `200` úspěch, `400` neplatná konfigurace, `412` neplatná verze schématu, `417` požadované vlastnosti jsou prázdné, `500` selhaly. |
| lastDesiredStatus. Description | Textový popis stavu |
| configurationHealth. stav: {deploymentId}. | `healthy` Pokud je stav modulu runtime pro všechny moduly nastavené nasazením {deploymentId} buď `running` nebo `stopped` , `unhealthy` jinak |
| Runtime. Platform. OS | Hlášení operačního systému běžícího na zařízení |
| Runtime. Platform. Architecture | Vytváření sestav architektury procesoru v zařízení |
| systemModules.edgeAgent.runtimeStatus | Stav hlášené IoT Edge agenta: {"spouštění" "není v \| pořádku"} |
| systemModules. edgeAgent. statusDescription | Popis stavu hlášeného agenta IoT Edge. |
| systemModules.edgeHub.runtimeStatus | Stav centra IoT Edge: {"běžící" "zastavené" "neúspěšné" omezení rychlosti "", "není v \| \| \| \| pořádku"} |
| systemModules. edgeHub. statusDescription | Textový popis stavu centra IoT Edge, pokud není v pořádku. |
| systemModules. edgeHub. exitCode | Ukončovací kód, který je hlášen kontejnerem centra IoT Edge v případě ukončení kontejneru |
| systemModules.edgeHub.startTimeUtc | Čas posledního spuštění centra IoT Edge |
| systemModules.edgeHub.lastExitTimeUtc | Čas posledního ukončení IoT Edgeho centra |
| systemModules.edgeHub.lastRestartTimeUtc | Čas, kdy byl naposledy restartován IoT Edge hub |
| systemModules.edgeHub.restartCount | Počet restartování tohoto modulu v rámci zásad restartování |
| Aktualizuj. {moduleId}. runtimeStatus | Stav modulu: {"spouštění" "zastaveno" "omezení rychlosti" "není v \| \| \| \| pořádku"} |
| Aktualizuj. {moduleId}. statusDescription | Textový popis stavu modulu, pokud není v pořádku. |
| Aktualizuj. {moduleId}. exitCode | Ukončovací kód hlášených kontejnerem modulu, pokud se kontejner ukončí |
| Aktualizuj. {moduleId}. startTimeUtc | Čas posledního spuštění modulu |
| Aktualizuj. {moduleId}. lastExitTimeUtc | Čas posledního ukončení modulu |
| Aktualizuj. {moduleId}. lastRestartTimeUtc | Čas, kdy byl modul naposledy restartován |
| Aktualizuj. {moduleId}. restartCount | Počet restartování tohoto modulu v rámci zásad restartování |

## <a name="edgehub-desired-properties"></a>EdgeHub požadované vlastnosti

Pro IoT Edge se volá modul, který se zavolá, `$edgeHub` a koordinuje komunikaci mezi IoT Edgem rozbočovačem běžícím na zařízení a IoT Hub. Požadované vlastnosti jsou nastaveny při použití manifestu nasazení na určitém zařízení jako součást nasazení v jednom zařízení nebo ve velkém měřítku.

| Vlastnost | Popis | Vyžadováno v manifestu nasazení |
| -------- | ----------- | -------- |
| schemaVersion | Buď "1,0" nebo "1,1". Verze 1,1 byla představena s IoT Edge verzí 1.0.10 a doporučuje se. | Yes |
| tras. RouteName | Řetězec představující trasu centra IoT Edge. Další informace naleznete v tématu [Declare Routes](module-composition.md#declare-routes). | `routes`Element může být přítomen, ale prázdný. |
| storeAndForwardConfiguration.timeToLiveSecs | Čas v sekundách, po který IoT Edge hub udržuje zprávy, pokud je odpojený od koncových bodů směrování, ať už IoT Hub nebo místní modul. Hodnota může být libovolné kladné celé číslo. | Yes |

## <a name="edgehub-reported-properties"></a>EdgeHub hlášené vlastnosti

| Vlastnost | Popis |
| -------- | ----------- |
| lastDesiredVersion | Toto celé číslo odkazuje na poslední verzi požadovaných vlastností zpracovaných centrem IoT Edge. |
| lastDesiredStatus. Code | Stavový kód odkazuje na poslední požadované vlastnosti, které vidí centrum IoT Edge. Povolené hodnoty: `200` úspěch, `400` neplatná konfigurace, `500` neúspěšná |
| lastDesiredStatus. Description | Textový popis stavu |
| klienti. {Device nebo moduleId}. status | Stav připojení tohoto zařízení nebo modulu. Možné hodnoty {"připojeno" \| "Odpojeno"}. Jenom identity modulu můžou být v odpojeném stavu. Podřízená zařízení připojující se k rozbočovači IoT Edge se zobrazí pouze v případě připojení. |
| klienti. {Device nebo moduleId}. lastConnectTime | Čas posledního připojení zařízení nebo modulu |
| klienti. {Device nebo moduleId}. lastDisconnectTime | Čas posledního odpojení zařízení nebo modulu. |

## <a name="next-steps"></a>Další kroky

Informace o tom, jak tyto vlastnosti použít k sestavení manifestů nasazení, najdete v tématu [Vysvětlení způsobu použití, konfigurace a](module-composition.md)opětovného použití modulů IoT Edge.
