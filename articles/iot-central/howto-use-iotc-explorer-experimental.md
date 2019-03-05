---
title: Monitorování připojení zařízení pomocí Průzkumníka Azure IoT Central
description: Monitorování zprávy typu zařízení a sledovat změny dvojče zařízení pomocí IoT Central Průzkumník rozhraní příkazového řádku.
author: viv-liu
ms.author: viviali
ms.date: 02/20/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: 167a7b008af521c5f4e403a3786b004138d69da9
ms.sourcegitcommit: 3f4ffc7477cff56a078c9640043836768f212a06
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/04/2019
ms.locfileid: "57311147"
---
# <a name="monitor-device-connectivity-using-the-azure-iot-central-explorer"></a>Monitorování připojení zařízení pomocí Průzkumníka Azure IoT Central

*Toto téma platí pro tvůrce a správce.*

Pomocí IoT Central Průzkumník rozhraní příkazového řádku můžete zobrazit zprávy zařízení odesílají do IoT Central a sledovat změny v dvojčeti služby IoT Hub. Získávejte hlubší vhled do stavu připojení zařízení a diagnostikovat problémy zprávy typu zařízení nedosáhne cloudu nebo přestane reagovat na změny dvojčete zařízení můžete použít tento nástroj open source.

[Navštivte iotc Průzkumníka úložiště Githubu.](https://aka.ms/iotciotcexplorercligithub)

## <a name="prerequisites"></a>Požadavky

+ Verze Node.js 8.x nebo vyšší - https://nodejs.org
+ Správce aplikace musí generování přístupového tokenu pro použití v iotc-explorer

## <a name="install-iotc-explorer"></a>Nainstalujte iotc-explorer

Spusťte následující příkaz z příkazového řádku pro instalaci:

```cmd/sh
npm install -g iotc-explorer
```

> [!NOTE]
> Je obvykle potřeba spustit příkaz install s `sudo` v prostředí na bázi systému Unix.

## <a name="run-iotc-explorer"></a>Spusťte iotc-explorer

Následující části popisují běžné příkazy a možnosti, které můžete použít při spuštění `iotc-explorer`. Chcete-li zobrazit úplnou sadu příkazů a možností, předejte `--help` k `iotc-explorer` nebo některý z jeho dílčích příkazů.

### <a name="login"></a>Přihlásit

Předtím, než můžete začít, musíte mít správce vaší aplikace IoT Central pro získání přístupového tokenu pro použití. Správce má následující kroky:

1. Přejděte do **správu** pak **přístupové tokeny**.
1. Vyberte **vygenerovat Token**.
    ![Snímek obrazovky stránky tokenu přístupu](media/howto-use-iotc-explorer-experimental/accesstokenspage.png)

1. Zadejte název tokenu, vyberte **Další**a potom **kopírování**.
    > [!NOTE]
    > Hodnota tokenu se zobrazí pouze jednou, takže musí být zkopírovány před jeho zavřením dialogu. Po zavření dialogového okna, se nikdy znovu zobrazí.

    ![Zkopírovat snímek dialogové okno tokenu přístupu](media/howto-use-iotc-explorer-experimental/copyaccesstoken.png)

Použití tokenu k přihlášení k rozhraní příkazového řádku takto:

```cmd/sh
iotc-explorer login "<Token value>"
```

Pokud dáváte přednost není token uchovávaných v historii vašeho prostředí, můžete nechat token out a místo toho ho po zobrazení výzvy zadat:

```cmd/sh
iotc-explorer login
```

### <a name="monitor-device-messages"></a>Monitorování zprávy typu zařízení

Můžete se podívat zprávy přicházející z určitého zařízení nebo všechna zařízení ve vaší aplikaci pomocí `monitor-messages` příkazu. Tento příkaz spustí sledování, které průběžně výstupy nové zprávy při jejich doručení:

Sledovat všechna zařízení ve vaší aplikaci, spusťte následující příkaz:

```cmd/sh
iotc-explorer monitor-messages
```

Výstup:

![výstup příkazu monitorování zprávy](media/howto-use-iotc-explorer-experimental/monitormessages.png)

Ke sledování konkrétních zařízení, stačí přidat na konec příkazu id zařízení:

```cmd/sh
iotc-explorer monitor-messages <your-device-id>
```

Můžete také výstupní formát počítačů vhodných tak, že přidáte `--raw` možnost příkazu:

```
iotc-explorer monitor-messages --raw
```

### <a name="get-device-twin"></a>Získáte, dvojče zařízení

Můžete použít `get-twin` příkaz má být získán obsah dvojčete zařízení IoT Central. Chcete-li tak učinit, spusťte následující příkaz:

```cmd/sh
iotc-explorer get-twin <your-device-id>
```

Výstup:

![výstup příkazu GET-twin](media/howto-use-iotc-explorer-experimental/getdevicetwin.png)

Stejně jako u `monitor-messages`, můžete získat další počítač popisný výstup předáním `--raw` možnost:

```cmd/sh
iotc-explorer get-twin <your-device-id> --raw
```

## <a name="next-steps"></a>Další postup

Teď, když jsme zjistili, jak pomocí Průzkumníka IoT Central, navrhované dalším krokem je prozkoumat [správy zařízení IoT Central](howto-manage-devices-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json).
