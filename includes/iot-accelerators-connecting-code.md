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
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "67175442"
---
### <a name="code-walkthrough"></a>Průvodce kódem

Tato část popisuje některé klíčové části ukázkového kódu a vysvětluje, jak se vztahují k akcelerátoru řešení vzdáleného monitorování.

Následující fragment kódu ukazuje, jak jsou definovány hlášené vlastnosti, které popisují schopnosti zařízení. Mezi tyto vlastnosti patří:

- Umístění zařízení, aby akcelerátor řešení mohl přidat zařízení do mapy.
- Aktuální verze firmwaru.
- Seznam metod, které zařízení podporuje.
- Schéma zpráv telemetrie odesílaných zařízením

[!code-cpp[Define data structures for Chiller](~/iot-samples-c/samples/solutions/remote_monitoring_client/remote_monitoring.c?name=datadefinition "Define data structures for Chiller")]

Ukázka zahrnuje funkci **serializeToJson** , která serializaci této struktury dat pomocí knihovny Parson.

Ukázka obsahuje několik funkcí zpětného volání, které tisknou informace do konzoly, když klient komunikuje s akcelerátorem řešení:

- **connection_status_callback**
- **send_confirm_callback**
- **reported_state_callback**
- **device_method_callback**

Následující fragment kódu ukazuje funkci **device_method_callback** . Tato funkce určuje akci, která se má provést při přijetí volání metody z akcelerátoru řešení. Funkce přijme odkaz na strukturu dat **chladicího** nástroje v parametru **userContextCallback** . Hodnota **userContextCallback** je nastavena, když je funkce zpětného volání nakonfigurována v **hlavní** funkci:

[!code-cpp[Device method callback](~/iot-samples-c/samples/solutions/remote_monitoring_client/remote_monitoring.c?name=devicemethodcallback "Device method callback")]

Když akcelerátor řešení volá metodu aktualizace firmwaru, ukázka deserializace datové části JSON a spustí vlákno na pozadí pro dokončení procesu aktualizace. Následující fragment kódu ukazuje **do_firmware_update** , která běží ve vlákně:

[!code-cpp[Firmware update thread](~/iot-samples-c/samples/solutions/remote_monitoring_client/remote_monitoring.c?name=firmwareupdate "Firmware update thread")]

Následující fragment kódu ukazuje, jak klient odesílá zprávu telemetrie do akcelerátoru řešení. Vlastnosti zprávy obsahují schéma zprávy, které pomůžou akcelerátoru řešení zobrazit telemetrii na řídicím panelu:

[!code-cpp[Send telemetry](~/iot-samples-c/samples/solutions/remote_monitoring_client/remote_monitoring.c?name=sendmessage "Send telemetry")]

**Hlavní** funkce v ukázce:

- Inicializuje a ukončí podsystém sady SDK.
- Inicializuje strukturu dat **chladicího** nástroje.
- Odešle hlášené vlastnosti do akcelerátoru řešení.
- Nakonfiguruje funkci zpětného volání pro metodu zařízení.
- Odesílá simulované hodnoty telemetrie do akcelerátoru řešení.

[!code-cpp[Main](~/iot-samples-c/samples/solutions/remote_monitoring_client/remote_monitoring.c?name=main "Main")]
