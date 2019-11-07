---
title: Připojení zařízení DevKit k aplikaci Azure IoT Central | Microsoft Docs
description: Jako vývojář zařízení se naučíte připojit zařízení MXChip IoT DevKit k vaší aplikaci Azure IoT Central.
author: dominicbetts
ms.author: dobett
ms.date: 03/22/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 4e04ae7d9594ac064c9f3707c797fb2709a79cb6
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2019
ms.locfileid: "73583009"
---
# <a name="connect-an-mxchip-iot-devkit-device-to-your-azure-iot-central-application"></a>Připojení zařízení IoT DevKit MXChip k aplikaci Azure IoT Central

[!INCLUDE [iot-central-original-pnp](../../../includes/iot-central-original-pnp-note.md)]

Tento článek popisuje, jak jako vývojář zařízení připojit zařízení MXChip IoT DevKit (DevKit) k vaší aplikaci Microsoft Azure IoT Central.

## <a name="before-you-begin"></a>Než začnete

K dokončení kroků v tomto článku budete potřebovat následující zdroje:

1. Aplikace IoT Central v Azure vytvořená z **ukázkové** šablony aplikace Devkits Další informace najdete v [rychlém startu k vytvoření aplikace](quick-deploy-iot-central.md).
1. Zařízení DevKit. Pokud si chcete koupit zařízení DevKit, přejděte na [MXChip IoT DevKit](https://microsoft.github.io/azure-iot-developer-kit/).

## <a name="sample-devkits-application"></a>Ukázková aplikace Devkits

Aplikace vytvořená z ukázkové šablony aplikace **Devkits** zahrnuje šablonu zařízení **MXChip** , která definuje následující vlastnosti zařízení:

- Měření telemetrie pro **vlhkost**, **teplotu**, **tlak**, **magnetometer** (měřeno podél x, y, z osy z), **akcelerometr** (měřeno podél x, y, z osy z) a **vybavený gyroskopem** (měřeno podél x, y, z osy z).
- Měření stavu **zařízení**.
- Byla stisknuta měření události pro **tlačítko B**.
- Nastavení pro **napětí**, **aktuální**, **rychlost ventilátoru**a přepínač **IR**
- Vlastnosti zařízení **číslo** a **umístění zařízení**, což je vlastnost umístění.
- Vlastnost cloudu **vyrobená v**.
- Příkazy **echo** a **odpočítávání**. Když reálné zařízení obdrží příkaz **echo** , zobrazí odeslanou hodnotu v zobrazení zařízení. Když reálné zařízení obdrží příkaz **odpočítávání** , indikátor LED projde vzorem a zařízení pošle hodnoty odpočítávání zpět do IoT Central.

Úplné podrobnosti o konfiguraci najdete v tématu [Podrobnosti o šabloně zařízení MXChip](#mxchip-device-template-details) .

## <a name="add-a-real-device"></a>Přidání skutečného zařízení

### <a name="get-your-device-connection-details"></a>Získat podrobnosti o připojení zařízení

V aplikaci Azure IoT Central přidejte reálné zařízení ze šablony zařízení **MXChip** a poznamenejte si podrobnosti o připojení zařízení: **ID oboru, ID zařízení a primární klíč**:

1. Přidejte **reálné zařízení** z Device Explorer, vyberte **+ Nový > Real** a přidejte reálné zařízení.

    * Zadejte **ID zařízení**s malým písmenem nebo použijte navržené **ID zařízení**.
    * Zadejte **název zařízení**nebo použijte navrhovaný název.

    ![Přidání zařízení](media/howto-connect-devkit/add-device.png)

1. Pokud chcete získat podrobnosti o připojení zařízení, **ID oboru**, **ID zařízení**a **primární klíč**, vyberte **připojit** na stránce zařízení.

    ![Podrobnosti připojení](media/howto-connect-devkit/device-connect.png)

1. Poznamenejte si podrobnosti o připojení. Po přípravě zařízení DevKit v dalším kroku budete dočasně odpojeni od Internetu.

### <a name="prepare-the-devkit-device"></a>Příprava zařízení DevKit

Pokud jste zařízení dřív používali a chcete ho znovu nakonfigurovat tak, aby používalo jinou síť **Wi-Fi** , připojovací řetězec nebo měření telemetrie, stiskněte současně tlačítka a a **B** . Pokud nefunguje, stiskněte tlačítko **resetovat** a zkuste to znovu.

#### <a name="to-prepare-the-devkit-device"></a>Příprava zařízení DevKit

1. Stáhněte si nejnovější předem připravený firmware IoT Central Azure pro MXChip ze stránky [releases](https://aka.ms/iotcentral-docs-MXChip-releases) na GitHubu.
1. Připojte zařízení DevKit k vývojovému počítači pomocí kabelu USB. V systému Windows se otevře okno Průzkumník souborů na jednotce namapované na úložiště na zařízení DevKit. Například jednotka může být volána **AZ3166 (D:)** .
1. Přetáhněte soubor **iotCentral. bin** do okna jednotky. Po dokončení kopírování se zařízení restartuje pomocí nového firmwaru.

1. Po restartování zařízení DevKit se zobrazí následující obrazovka:

    ```
    Connect HotSpot:
    AZ3166_??????
    go-> 192.168.0.1
    PIN CODE xxxxx
    ```

    > [!NOTE]
    > Pokud se na obrazovce zobrazí cokoli jiného, resetujte zařízení a stisknutím tlačítka **a a** **B** na zařízení restartujte zařízení.

1. Zařízení je nyní v režimu přístupového bodu (AP). K tomuto přístupovému bodu Wi-Fi se můžete připojit z počítače nebo mobilního zařízení.

1. V počítači, telefonu nebo tabletu se připojte k názvu sítě Wi-Fi, který je zobrazený na obrazovce zařízení. Když se připojíte k této síti, nemáte přístup k Internetu. Tento stav je očekávaný a během konfigurace zařízení jste se k této síti připojili jenom krátkou dobu.

1. Otevřete webový prohlížeč a přejděte na [http://192.168.0.1/start](http://192.168.0.1/start). Zobrazí se následující webová stránka:

    ![Stránka konfigurace zařízení](media/howto-connect-devkit/configpage.png)

    Na webové stránce zadejte:
    - Název sítě Wi-Fi
    - Vaše heslo k síti Wi-Fi
    - Kód PIN zobrazený na displeji zařízení
    - **ID rozsahu**, **ID zařízení**a **primární klíč** vašeho zařízení (podrobnosti o připojení byste už měli uložit podle postupu)
    - Vybrat všechna dostupná měření telemetrie

1. Po zvolení možnosti **Konfigurovat zařízení**uvidíte tuto stránku:

    ![Nakonfigurované zařízení](media/howto-connect-devkit/deviceconfigured.png)

1. V zařízení stiskněte tlačítko **obnovit** .

## <a name="view-the-telemetry"></a>Zobrazit telemetrii

Po restartování zařízení DevKit se zobrazí obrazovka na zařízení:

* Počet odeslaných zpráv telemetrie.
* Počet selhání.
* Počet přijatých požadovaných vlastností a počet odeslaných hlášených vlastností.

> [!NOTE]
> Pokud se zařízení při pokusu o připojení jeví jako smyčka, ověřte, jestli je zařízení **blokované** v IoT Central, a **odblokujte** zařízení, aby se mohlo připojit k aplikaci.

Zatřeste zařízení k odeslání hlášené vlastnosti. Zařízení pošle náhodné číslo jako vlastnost zařízení **číslo** .

Můžete zobrazit měření telemetrie a hlášené hodnoty vlastností a nakonfigurovat nastavení v Azure IoT Central:

1. Pomocí **zařízení** přejděte na stránku **měření** pro skutečné zařízení MXChip, které jste přidali:

    ![Přejít na reálné zařízení](media/howto-connect-devkit/realdevicenew.png)

1. Na stránce **měření** se zobrazí telemetrie přicházející ze zařízení MXChip:

    ![Zobrazit telemetrii z reálného zařízení](media/howto-connect-devkit/devicetelemetrynew.png)

1. Na stránce **vlastnosti** můžete zobrazit poslední číslo a umístění zařízení hlášené zařízením:

    ![Zobrazit vlastnosti zařízení](media/howto-connect-devkit/devicepropertynew.png)

1. Na stránce **Nastavení** můžete aktualizovat nastavení na zařízení MXChip:

    ![Zobrazit nastavení zařízení](media/howto-connect-devkit/devicesettingsnew.png)

1. Na stránce **příkazy** můžete zavolat příkazy **echo** a **odpočítávání** :

    ![Příkazy volání](media/howto-connect-devkit/devicecommands.png)

1. Na stránce **řídicího panelu** uvidíte mapu umístění.

    ![Zobrazení řídicího panelu zařízení](media/howto-connect-devkit/devicedashboardnew.png)

## <a name="download-the-source-code"></a>Stažení zdrojového kódu

Pokud chcete prozkoumat a upravit kód zařízení, můžete si ho stáhnout z GitHubu. Pokud plánujete upravit kód, postupujte podle těchto pokynů pro [přípravu vývojového prostředí](https://microsoft.github.io/azure-iot-developer-kit/docs/get-started/#step-5-prepare-the-development-environment) pro operační systém pro stolní počítače.

Zdrojový kód stáhnete spuštěním následujícího příkazu na stolním počítači:

```cmd/sh
git clone https://github.com/Azure/iot-central-firmware
```

Předchozí příkaz stáhne zdrojový kód do složky s názvem `iot-central-firmware`.

> [!NOTE]
> Pokud **Git** není nainstalovaný ve vašem vývojovém prostředí, můžete si ho stáhnout z [https://git-scm.com/download](https://git-scm.com/download).

## <a name="review-the-code"></a>Kontrola kódu

Pomocí Visual Studio Code otevřete složku `MXCHIP/mxchip_advanced` ve složce `iot-central-firmware`:

![Visual Studio Code](media/howto-connect-devkit/vscodeview.png)

Pokud chcete zjistit, jak se telemetrie pošle do aplikace Azure IoT Central, otevřete soubor **telemetrie. cpp** ve složce `src`:

- Funkce `TelemetryController::buildTelemetryPayload` vytvoří datovou část telemetrie JSON pomocí dat ze senzorů na zařízení.

- Funkce `TelemetryController::sendTelemetryPayload` volá `sendTelemetry` v **AzureIOTClient. cpp** k odeslání datové části JSON do IoT Hub používá vaše aplikace Azure IoT Central.

Chcete-li zjistit, jak jsou hodnoty vlastností hlášeny do aplikace Azure IoT Central, otevřete soubor **telemetrie. cpp** ve složce `src`:

- Funkce `TelemetryController::loop` odesílá vlastnost **locationed (umístění** hlášené přibližně každých 30 sekund). Používá funkci `sendReportedProperty` ve zdrojovém souboru **AzureIOTClient. cpp** .

- Funkce `TelemetryController::loop` odesílá vlastnost hlášené **dieNumber** , když zařízení akcelerometr detekuje dvojitou klepnutím. Používá funkci `sendReportedProperty` ve zdrojovém souboru **AzureIOTClient. cpp** .

Chcete-li zjistit, jak zařízení reaguje na příkazy volané z aplikace IoT Central, otevřete soubor **registeredMethodHandlers. cpp** ve složce `src`:

- Funkce **dmEcho** je obslužná rutina pro příkaz **echo** . Zobrazuje **displayedValue** v datové části na obrazovce zařízení.

- Funkce **dmCountdown** je obslužná rutina pro příkaz **odpočítávání** . Změní barvu indikátoru LED zařízení a pomocí hlášené vlastnosti odešle hodnotu odpočítávání zpět do aplikace IoT Central. Vykazovaná vlastnost má stejný název jako příkaz. Funkce používá funkci `sendReportedProperty` ve zdrojovém souboru **AzureIOTClient. cpp** .

Kód ve zdrojovém souboru **AzureIOTClient. cpp** používá funkce z Microsoft Azure sady [SDK a knihovny IoT pro jazyk C](https://github.com/Azure/azure-iot-sdk-c) k interakci s IoT Hub.

Informace o tom, jak upravit, sestavit a nahrát vzorový kód do zařízení, najdete v souboru **Readme.MD** ve složce `MXCHIP/mxchip_advanced`.

## <a name="mxchip-device-template-details"></a>Podrobnosti o šabloně zařízení MXChip

Aplikace vytvořená z ukázkové šablony aplikace Devkits zahrnuje šablonu zařízení MXChip s následujícími charakteristikami:

### <a name="measurements"></a>Měření

#### <a name="telemetry"></a>Telemetrická data

| Název pole     | Jednotky  | Minimální | Maximum | Desetinná místa |
| -------------- | ------ | ------- | ------- | -------------- |
| vlhkost       | %      | 0       | 100     | 0              |
| názvem           | 7C     | -40     | 120     | 0              |
| tlak       | hPa    | 260     | 1260    | 0              |
| magnetometerX  | mgauss | -1000   | 1 000    | 0              |
| magnetometerY  | mgauss | -1000   | 1 000    | 0              |
| magnetometerZ  | mgauss | -1000   | 1 000    | 0              |
| accelerometerX | mg/Nm3     | -2000   | 2000    | 0              |
| akcelerometr | mg/Nm3     | -2000   | 2000    | 0              |
| accelerometerZ | mg/Nm3     | -2000   | 2000    | 0              |
| gyroscopeX     | mdps   | -2000   | 2000    | 0              |
| gyroscopeY     | mdps   | -2000   | 2000    | 0              |
| gyroscopeZ     | mdps   | -2000   | 2000    | 0              |

#### <a name="states"></a>Stavy 
| Název          | Zobrazované jméno   | BĚŽNOU | Upozornění | BEZPEČNÁ | 
| ------------- | -------------- | ------ | ------- | ------ | 
| DeviceState   | Stav zařízení   | Zelená  | Orange  | Červená    | 

#### <a name="events"></a>Akce 
| Název             | Zobrazované jméno      | 
| ---------------- | ----------------- | 
| ButtonBPressed   | Stisknutí tlačítka B  | 

### <a name="settings"></a>Nastavení

Číselná nastavení

| Zobrazované jméno | Název pole | Jednotky | Desetinná místa | Minimální | Maximum | Pořizovací |
| ------------ | ---------- | ----- | -------------- | ------- | ------- | ------- |
| Napájení      | setVoltage | V voltech | 0              | 0       | 240     | 0       |
| Aktivní      | setCurrent | Amps  | 0              | 0       | 100     | 0       |
| Rychlost ventilátoru    | fanSpeed   | /MIN   | 0              | 0       | 1 000    | 0       |

Přepnout nastavení

| Zobrazované jméno | Název pole | Na text | Vypnuto textu | Pořizovací |
| ------------ | ---------- | ------- | -------- | ------- |
| IR           | activateIR | ON      | OFF      | Vypnuto     |

### <a name="properties"></a>Vlastnosti

| Typ            | Zobrazované jméno | Název pole | Data type |
| --------------- | ------------ | ---------- | --------- |
| Vlastnost zařízení | Číslo Die   | dieNumber  | číslo    |
| Vlastnost zařízení | Umístění zařízení   | location  | location    |
| Text            | Vyrobeno v     | manufacturedIn   | Není dostupné.       |

### <a name="commands"></a>Příkazy

| Zobrazované jméno | Název pole | Návratový typ | Zobrazovaný název vstupního pole | Název vstupního pole | Typ vstupního pole |
| ------------ | ---------- | ----------- | ------------------------ | ---------------- | ---------------- |
| Zvuk         | echo       | text        | hodnota, která se má zobrazit         | displayedValue   | text             |
| Odpočítávání    | Odpočítávání  | číslo      | Počet z               | countFrom        | číslo           |

## <a name="next-steps"></a>Další kroky

Teď, když jste se naučili připojit MXChip IoT DevKit k vaší aplikaci Azure IoT Central, je navržený další krok, kde se dozvíte, jak [nastavit vlastní šablonu zařízení](howto-set-up-template.md) pro vlastní zařízení IoT.
