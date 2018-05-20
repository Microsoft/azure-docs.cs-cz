---
title: Připojení zařízení DevKit do aplikace Azure IoT centrální | Microsoft Docs
description: Jako vývojář zařízení zjistěte, jak se připojit k aplikaci Azure IoT centrální zařízení s MXChip IoT DevKit.
services: iot-central
author: tanmaybhagwat
ms.author: tanmayb
ms.date: 04/16/2018
ms.topic: article
ms.prod: microsoft-iot-central
manager: timlt
ms.openlocfilehash: 4c7074e5e7d3858919f3fc17005fea4f8dce1560
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/16/2018
---
# <a name="connect-an-mxchip-iot-devkit-device-to-your-azure-iot-central-application"></a>Připojte zařízení MXChip IoT DevKit do aplikace Azure IoT centrální

Tento článek popisuje, jak jako vývojář zařízení pro připojení k aplikaci Microsoft Azure IoT centrální zařízení IoT DevKit MXChip (DevKit).

## <a name="before-you-begin"></a>Než začnete

K dokončení kroků v tomto článku budete potřebovat následující:

1. Azure IoT centrální aplikace vytvořené pomocí **Devkits ukázka** šablony aplikace. Další informace najdete v tématu [vytvořit aplikaci Azure IoT centrální](howto-create-application.md).
1. DevKit zařízení. Chcete-li zakoupit DevKit zařízení, najdete [MXChip IoT DevKit](http://mxchip.com/az3166).

Aplikace vytvořené z **ukázka Devkits** obsahuje šablony aplikace **MXChip** šablona zařízení s následujícími charakteristikami:

### <a name="telemetry-measurements"></a>Měření telemetrie

| Název pole     | Jednotky  | Minimální | Maximum | Desetinných míst |
| -------------- | ------ | ------- | ------- | -------------- |
| vlhkosti       | %      | 0       | 100     | 0              |
| dočasné           | ° C     | -40     | 120     | 0              |
| pressure       | hPa    | 260     | 1260    | 0              |
| magnetometerX  | mgauss | -1000   | 1000    | 0              |
| magnetometerY  | mgauss | -1000   | 1000    | 0              |
| magnetometerZ  | mgauss | -1000   | 1000    | 0              |
| accelerometerX | mg     | -2000   | 2000    | 0              |
| accelerometerY | mg     | -2000   | 2000    | 0              |
| accelerometerZ | mg     | -2000   | 2000    | 0              |
| gyroscopeX     | mdps   | -2000   | 2000    | 0              |
| gyroscopeY     | mdps   | -2000   | 2000    | 0              |
| gyroscopeZ     | mdps   | -2000   | 2000    | 0              |

### <a name="settings"></a>Nastavení

Číselné nastavení

| Zobrazované jméno | Název pole | Jednotky | Desetinných míst | Minimální | Maximum | Počáteční |
| ------------ | ---------- | ----- | -------------- | ------- | ------- | ------- |
| Napětí      | setVoltage | Intenzita | 0              | 0       | 240     | 0       |
| Aktuální      | setCurrent | A  | 0              | 0       | 100     | 0       |
| Ventilátor rychlosti    | fanSpeed   | OT. / MIN   | 0              | 0       | 1000    | 0       |

Přepnutí nastavení

| Zobrazované jméno | Název pole | Na text | Vypnout text | Počáteční |
| ------------ | ---------- | ------- | -------- | ------- |
| REAKCÍ NA INCIDENTY           | activateIR | ON      | OFF      | Vypnuto     |

### <a name="properties"></a>Vlastnosti

| Typ            | Zobrazované jméno | Název pole | Typ dat |
| --------------- | ------------ | ---------- | --------- |
| Vlastnosti zařízení | Kostka číslo   | dieNumber  | číslo    |
| Text            | Umístění     | location   | neuvedeno       |

### <a name="states"></a>stavy 

| Název          | Zobrazované jméno   | NORMÁLNÍ | UPOZORNĚNÍ: | NEBEZPEČÍ | 
| ------------- | -------------- | ------ | ------- | ------ | 
| DeviceState   | Stav zařízení   | Zelená  | Orange  | Červená    | 

### <a name="events"></a>Události 

| Název             | Zobrazované jméno      | 
| ---------------- | ----------------- | 
| ButtonBPressed   | Bylo stisknuto tlačítko B  | 

### <a name="add-a-real-device"></a>Přidání skutečné zařízení

V aplikaci Azure IoT centrální přidat skutečné zařízení z **MXChip** šablona zařízení a je zaznamenána připojovací řetězec zařízení. Další informace najdete v tématu [přidat skutečné zařízení Azure IoT centrální aplikace](tutorial-add-device.md).

## <a name="prepare-the-devkit-device"></a>Připravte zařízení DevKit

> [!TIP]
> Zařízení DevKit pokyny při řešení potíží, najdete v části [IoT DevKit Začínáme](https://microsoft.github.io/azure-iot-developer-kit/docs/get-started/).

Příprava DevKit zařízení:

1. Stáhněte si nejnovější předdefinovaných Azure IoT centrální firmware pro MXChip z [uvolní](https://github.com/Azure/iot-central-firmware/releases) stránky na Githubu. Název souboru ke stažení na stránce vydání vypadá `AZ3166-IoT-Central-X.X.X.bin`.

1. Připojte zařízení DevKit na vývojovém počítači pomocí kabelu USB. V systému Windows otevře se okno Průzkumníka souborů na jednotce namapované na úložiště DevKit zařízení. Například může být například jednotky **AZ3166 (D:)**.

1. Přetáhněte **iotCentral.bin** soubor do okna jednotky. Po dokončení kopírování zařízení restartuje se nový firmwaru.

1. Po restartu zařízení DevKit, zobrazí se následující obrazovka:

    ```
    Connect HotSpot:
    AZ3166_??????
    go-> 192.168.0.1 
    PIN CODE xxxxx
    ```

    > [!NOTE]
    > Pokud na obrazovce zobrazí cokoliv jiného, stiskněte **resetovat** na zařízení tlačítko. 

1. Zařízení je nyní v režimu bodů (AP) přístup. Z počítače nebo mobilního zařízení můžete připojit k tomuto bodu přístup přes Wi-Fi.

1. V počítači telefon nebo tablet připojit k názvu sítě Wi-Fi uvedené na obrazovce zařízení. Jakmile se připojíte k této síti, nemáte přístup k Internetu. Tento stav se očekává a pouze připojení k této síti po krátkou dobu při konfiguraci zařízení.

1. Otevřete webový prohlížeč a přejděte do [ http://192.168.0.1/start ](http://192.168.0.1/start). Následující webové stránky zobrazí:

    ![Stránka Konfigurace zařízení](media/howto-connect-devkit/configpage.png)

    Na webové stránce: 
    - Přidejte název sítě Wi-Fi 
    - heslo sítě Wi-Fi 
    - PIN kód zobrazený na zařízení LCD 
    - připojovací řetězec vašeho zařízení. 
      Můžete najít připojovací řetězec @ `https://apps.iotcentral.com`  ->  `Device Explorer`  ->  `Device`  ->  `Select or Create a new Real Device`  ->  `Connect this device` (na vpravo nahoře) 
    - Vyberte všechny dostupné telemetrie měření! 

1. Když vyberete **nakonfigurovat zařízení**, zobrazí tato stránka:

    ![Zařízení nakonfigurovaná](media/howto-connect-devkit/deviceconfigured.png)

1. Stiskněte **resetovat** tlačítko na vašem zařízení.

> [!NOTE]
> Chcete-li překonfigurovat zařízení používat jinou síť Wi-Fi, připojovací řetězec nebo měření telemetrie, stiskněte i **A** a **B** tlačítka na panel současně. Pokud nefunguje, stiskněte klávesu **resetovat** tlačítko a zkuste to znovu. 

## <a name="view-the-telemetry"></a>Zobrazení telemetrie

Po restartu zařízení DevKit ukazuje obrazovku na zařízení:

* Počet odeslaných zpráv telemetrie.
* Počet chyb.
* Počet přijatých požadované vlastnosti a počet odeslaných hlášen vlastností.

Zatřesením přírůstek zařízení počet odeslaných hlášen vlastností. Zařízení odesílá náhodné číslo jako **kostka číslo** vlastnosti zařízení.

Můžete zobrazit telemetrii měření a hodnoty hlášené vlastností a konfigurace nastavení v Azure IoT centrální:

1. Použití **Explorer zařízení** přejděte na **měření** stránky pro skutečné zařízení MXChip jste přidali:

    ![Přejděte na skutečné zařízení](media/howto-connect-devkit/realdevice.png)

1. Na **měření** stránky, můžete zobrazit telemetrii z MXChip zařízení:

    ![Zobrazení telemetrie z reálného zařízení](media/howto-connect-devkit/realtelemetry.png)

1. Na **vlastnosti** stránky, můžete zobrazit poslední číslo hlášených zařízení:

    ![Zobrazení vlastností zařízení](media/howto-connect-devkit/deviceproperties.png)

1. Na **nastavení** stránky, můžete aktualizovat nastavení v zařízení MXChip:

    ![Zobrazení nastavení zařízení](media/howto-connect-devkit/settings.png)

## <a name="download-the-source-code"></a>Stáhnout zdrojový kód

Pokud chcete prozkoumat a upravit kód zařízení, můžete ji stáhnout z webu GitHub. Pokud máte v úmyslu změnit kód, postupujte podle těchto pokynů můžete [Příprava vývojového prostředí](https://microsoft.github.io/azure-iot-developer-kit/docs/get-started/#step-5-prepare-the-development-environment) plochy operačního systému.

Chcete-li stáhnout zdrojový kód, spusťte následující příkaz na počítači klientů:

```cmd/sh
git clone https://github.com/Azure/iot-central-firmware
```

Předchozí příkaz načte zdrojový kód pro složku s názvem `iot-central-firmware`. 

> [!NOTE]
> Pokud **git** není nainstalován ve vašem vývojovém prostředí si můžete stáhnout z [ https://git-scm.com/download ](https://git-scm.com/download).

## <a name="review-the-code"></a>Kontrola kódu

Použít Visual Studio Code, která byla nainstalována během přípravy vývojového prostředí, chcete-li otevřít `AZ3166` složku `iot-central-firmware` složky: 

![Visual Studio Code](media/howto-connect-devkit/vscodeview.png)

Chcete-li zjistit, jak je odesílat telemetrii do Azure IoT centrální aplikace, otevřete **main_telemetry.cpp** soubor ve zdrojové složce.

Funkce `buildTelemetryPayload` vytvoří datové části JSON telemetrie pomocí dat z snímače na zařízení.

Funkce `sendTelemetryPayload` volání `sendTelemetry` v **iotHubClient.cpp** odeslat datové části JSON do služby IoT Hub vaše Azure IoT centrální používá aplikace.

Pokud chcete zobrazit, jak jsou hodnoty vlastností oznamovány Azure IoT centrální aplikaci, otevřete **main_telemetry.cpp** soubor ve zdrojové složce.

Funkce `telemetryLoop` odešle **doubleTap** hlášené vlastnosti, když zrychlení zjistí dvojité klepnutím. Použije `sendReportedProperty` v fungovat **iotHubClient.cpp** zdrojový soubor.

Kód **iotHubClient.cpp** zdrojový soubor používá funkce z [ SDK služby Microsoft Azure IoT a knihovny pro C](https://github.com/Azure/azure-iot-sdk-c) k interakci s centrem IoT.

Informace o tom, jak změnit, vytvoření a odeslání ukázkového kódu do vašeho zařízení najdete v tématu **readme.md** v soubor `AZ3166` složky.

## <a name="next-steps"></a>Další postup

Teď, když jste se naučili jak se připojit k aplikaci Azure IoT centrální DevKit zařízení, tady jsou navrhované další kroky:

* [Příprava a připojte malin platformy](howto-connect-raspberry-pi-python.md)