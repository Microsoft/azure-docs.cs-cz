---
title: Monitorování připojení zařízení pomocí Průzkumníka Azure IoT Central
description: Monitorování zprávy typu zařízení a sledovat změny dvojče zařízení pomocí IoT Central Průzkumník rozhraní příkazového řádku.
author: viv-liu
ms.author: viviali
ms.date: 06/17/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: 4d17f0e5273c7397bd9c6a71d14b7992d8652768
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/17/2019
ms.locfileid: "67165864"
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
    ![Snímek obrazovky stránky tokenu přístupu](media/howto-use-iotc-explorer/accesstokenspage.png)

1. Zadejte název tokenu, vyberte **Další**a potom **kopírování**.
    > [!NOTE]
    > Hodnota tokenu se zobrazí pouze jednou, takže musí být zkopírovány před jeho zavřením dialogu. Po zavření dialogového okna, se nikdy znovu zobrazí.

    ![Zkopírovat snímek dialogové okno tokenu přístupu](media/howto-use-iotc-explorer/copyaccesstoken.png)

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

![výstup příkazu monitorování zprávy](media/howto-use-iotc-explorer/monitormessages.png)

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

![výstup příkazu GET-twin](media/howto-use-iotc-explorer/getdevicetwin.png)

Stejně jako u `monitor-messages`, můžete získat další počítač popisný výstup předáním `--raw` možnost:

```cmd/sh
iotc-explorer get-twin <your-device-id> --raw
```

## <a name="next-steps"></a>Další postup

Teď, když jsme zjistili, jak pomocí Průzkumníka IoT Central, navrhované dalším krokem je prozkoumat [správy zařízení IoT Central](howto-manage-devices.md).
