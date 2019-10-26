---
title: Monitorování připojení zařízení pomocí Azure IoT Central Exploreru
description: Sledujte zprávy zařízení a sledujte změny v zařízeních přes rozhraní příkazového řádku IoT Central Explorer.
author: viv-liu
ms.author: viviali
ms.date: 06/17/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: ec3ccc0a9fa6d1ae5d6193aacd1b068f2d97afe0
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/25/2019
ms.locfileid: "72949609"
---
# <a name="monitor-device-connectivity-using-the-azure-iot-central-explorer"></a>Monitorování připojení zařízení pomocí Azure IoT Central Exploreru

*Toto téma se týká tvůrců a správců.*

Pomocí rozhraní příkazového řádku IoT Central Explorer můžete zobrazit zprávy, které vaše zařízení odesílají do IoT Central, a sledovat změny ve IoT Hub zdvojené. Tento Open source nástroj můžete použít k získání hlubšího přehledu o stavu připojení zařízení a diagnostikování problémů se zprávami zařízení, které nedosáhnou cloudu, nebo zařízení, která nereagují na nereagující změny.

[Navštivte úložiště Průzkumníka IOTC v GitHubu.](https://aka.ms/iotciotcexplorercligithub)

## <a name="prerequisites"></a>Předpoklady

+ Node. js verze 8. x nebo vyšší – https://nodejs.org
+ Správce vaší aplikace musí vygenerovat přístupový token, který můžete použít v IOTC – Průzkumník.

## <a name="install-iotc-explorer"></a>Instalace součásti IOTC – Průzkumník

Spusťte následující příkaz z příkazového řádku pro instalaci:

```cmd/sh
npm install -g iotc-explorer
```

> [!NOTE]
> Obvykle je potřeba spustit příkaz Install s `sudo` v prostředích podobných platformě UNIX.

## <a name="run-iotc-explorer"></a>Spustit IOTC – Průzkumník

Následující části popisují běžné příkazy a možnosti, které můžete použít při spuštění `iotc-explorer`. Chcete-li zobrazit úplnou sadu příkazů a možností, předejte `--help` do `iotc-explorer` nebo kteréhokoli jeho dílčích příkazů.

### <a name="login"></a>Přihlášení

Než začnete, musíte mít správce aplikace IoT Central, abyste mohli získat přístupový token, který můžete použít. Správce provede následující kroky:

1. Přejděte do **správy** a pak **přístupové tokeny**.
1. Vyberte **Generovat token**.
    snímek obrazovky ![ho přístupového tokenu](media/howto-use-iotc-explorer/accesstokenspage.png)

1. Zadejte název tokenu, klikněte na **Další**a pak na **Kopírovat**.
    > [!NOTE]
    > Hodnota tokenu se zobrazuje jenom jednou, takže je potřeba před zavřením dialogu zkopírovat. Po zavření dialogového okna se znovu nezobrazí.

    ![Snímek obrazovky dialogového okna kopírovat přístupový token](media/howto-use-iotc-explorer/copyaccesstoken.png)

Token můžete použít k přihlášení do CLI následujícím způsobem:

```cmd/sh
iotc-explorer login "<Token value>"
```

Pokud si nepřejete, aby byl token uložený v historii prostředí, můžete token opustit a místo toho ho zadat po zobrazení výzvy:

```cmd/sh
iotc-explorer login
```

### <a name="monitor-device-messages"></a>Monitorování zpráv zařízení

Zprávy přicházející z konkrétního zařízení nebo ze všech zařízení v aplikaci můžete sledovat pomocí příkazu `monitor-messages`. Tento příkaz spustí sledovací proces, který průběžně vypíše nové zprávy při jejich doručení:

Chcete-li sledovat všechna zařízení v aplikaci, spusťte následující příkaz:

```cmd/sh
iotc-explorer monitor-messages
```

Výstup:

![Monitor – výstup příkazu zpráv](media/howto-use-iotc-explorer/monitormessages.png)

Pokud chcete sledovat konkrétní zařízení, stačí přidat ID zařízení na konec příkazu:

```cmd/sh
iotc-explorer monitor-messages <your-device-id>
```

Přidáním možnosti `--raw` do příkazu můžete také výstupem více strojově přívětivého formátu:

```cmd/sh
iotc-explorer monitor-messages --raw
```

### <a name="get-device-twin"></a>Získat dvojitou dvojici zařízení

K získání obsahu vlákna pro IoT Central zařízení můžete použít příkaz `get-twin`. Uděláte to tak, že spustíte následující příkaz:

```cmd/sh
iotc-explorer get-twin <your-device-id>
```

Výstup:

![výstup příkazu Get-zdvojený](media/howto-use-iotc-explorer/getdevicetwin.png)

Stejně jako u `monitor-messages`můžete získat více uživatelsky přívětivého výstupu předáním možnosti `--raw`:

```cmd/sh
iotc-explorer get-twin <your-device-id> --raw
```

## <a name="next-steps"></a>Další kroky

Teď, když jste se naučili, jak používat IoT Central Exploreru, je navržený další krok prozkoumat [správu zařízení IoT Central](howto-manage-devices.md).
