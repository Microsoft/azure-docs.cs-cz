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
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "61450209"
---
### <a name="code-walkthrough"></a>Původce kódem

Tato část popisuje některé z klíčových částí tohoto ukázkového kódu a vysvětluje, jak se vztahují k akcelerátoru řešení vzdáleného monitorování.

Následující fragment kódu ukazuje, jak jsou definované ohlášené vlastnosti, které popisují funkce zařízení. Tyto vlastnosti patří:

- Umístění zařízení na akcelerátor řešení přidat zařízení do mapy.
- Aktuální verze firmwaru.
- Seznam metod, které zařízení podporuje.
- Schéma telemetrické zprávy odeslané ze zařízení.



Ukázka zahrnuje **serializeToJson** funkce, která serializuje datovou strukturu pomocí Parson knihovny.

Ukázka zahrnuje několik funkcí zpětného volání, které tisknou informace do konzoly jako klient komunikuje s akcelerátor řešení:

- **connection_status_callback**
- **send_confirm_callback**
- **reported_state_callback**
- **device_method_callback**

Následující fragment kódu ukazuje **device_method_callback** funkce. Tato funkce určuje akci, která má provést při volání metody při obdržení ze akcelerátor řešení. Funkce přijímá odkaz na **chladič** datové struktury v **userContextCallback** parametru. Hodnota **userContextCallback** nastavená, pokud je nakonfigurovaná funkce zpětného volání v **hlavní** funkce:



Když akcelerátor řešení volá metodu aktualizace firmwaru, vzorek deserializuje datovou část JSON a spuštění vlákna na pozadí k dokončení procesu aktualizace. Následující fragment kódu ukazuje **do_firmware_update** , který běží na vlákně:



Následující fragment kódu ukazuje, jak klient odešle zprávu telemetrických dat do akcelerátoru řešení. Vlastnosti zprávy zahrnují schéma zpráva umožňující akcelerátor řešení zobrazit telemetrická data na řídicím panelu:



**Hlavní** funkce v ukázce:

- Inicializuje a ukončí se subsystém SDK.
- Inicializuje **chladič** datové struktury.
- Odesílá ohlášené vlastnosti k akcelerátoru řešení.
- Konfiguruje zařízení metoda zpětného volání funkce.
- Odesílá Simulovaná telemetrická data hodnoty na akcelerátor řešení.


