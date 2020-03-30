---
title: zahrnout soubor
description: zahrnout soubor
services: iot-suite
author: dominicbetts
ms.service: iot-suite
ms.topic: include
ms.date: 09/17/2018
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: c79b6f854dc78670a7eb8a1275c3e2fc46fcdd99
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "67175442"
---
### <a name="code-walkthrough"></a>Průvodce kódem

Tato část popisuje některé klíčové části ukázkového kódu a vysvětluje, jak souvisejí s akcelerátorem řešení vzdáleného monitorování.

Následující úryvek ukazuje, jak jsou definovány hlášené vlastnosti, které popisují možnosti zařízení. Mezi tyto vlastnosti patří:

- Umístění zařízení, které umožňuje akcelerátoru řešení přidat zařízení do mapy.
- Aktuální verze firmwaru.
- Seznam metod, které zařízení podporuje.
- Schéma telemetrických zpráv odeslaných zařízením.

[!code-cpp[Define data structures for Chiller](~/iot-samples-c/samples/solutions/remote_monitoring_client/remote_monitoring.c?name=datadefinition "Define data structures for Chiller")]

Ukázka obsahuje **serializeToJson** funkce, která serializuje tuto datovou strukturu pomocí knihovny Parson.

Ukázka obsahuje několik funkcí zpětného volání, které tisknou informace do konzoly jako klient spolupracuje s akcelerátorem řešení:

- **connection_status_callback**
- **send_confirm_callback**
- **reported_state_callback**
- **device_method_callback**

Následující úryvek zobrazuje **funkci device_method_callback.** Tato funkce určuje akci, která má být přijata při přijetí volání metody z akcelerátoru řešení. Funkce obdrží odkaz na datovou strukturu **chladiče** v parametru **userContextCallback.** Hodnota **userContextCallback** je nastavena, když je funkce zpětného volání nakonfigurována v **hlavní** funkci:

[!code-cpp[Device method callback](~/iot-samples-c/samples/solutions/remote_monitoring_client/remote_monitoring.c?name=devicemethodcallback "Device method callback")]

Když akcelerátor řešení volá metodu aktualizace firmwaru, ukázka deserializuje datovou část JSON a spustí vlákno na pozadí k dokončení procesu aktualizace. Následující úryvek zobrazuje **do_firmware_update,** který běží ve vlákně:

[!code-cpp[Firmware update thread](~/iot-samples-c/samples/solutions/remote_monitoring_client/remote_monitoring.c?name=firmwareupdate "Firmware update thread")]

Následující úryvek ukazuje, jak klient odešle telemetrickou zprávu do akcelerátoru řešení. Vlastnosti zprávy zahrnují schéma zprávy, které pomáhá akcelerátoru řešení zobrazit telemetrii na řídicím panelu:

[!code-cpp[Send telemetry](~/iot-samples-c/samples/solutions/remote_monitoring_client/remote_monitoring.c?name=sendmessage "Send telemetry")]

**Hlavní** funkce ve vzorku:

- Inicializuje a vypne subsystém SDK.
- Inicializuje datovou strukturu **chladiče.**
- Odešle hlášené vlastnosti akcelerátoru řešení.
- Konfiguruje funkci zpětného volání metody zařízení.
- Odešle simulované hodnoty telemetrie do akcelerátoru řešení.

[!code-cpp[Main](~/iot-samples-c/samples/solutions/remote_monitoring_client/remote_monitoring.c?name=main "Main")]
