---
title: Připojte zařízení za DevKit do aplikace Azure IoT Central | Dokumentace Microsoftu
description: Jako vývojář zařízení zjistěte, jak připojit zařízení MXChip IoT DevKit do aplikace Azure IoT Central.
author: tbhagwat3
ms.author: tanmayb
ms.date: 04/16/2018
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: 237a19b76268e1207c9de438a4f79d3dc8382476
ms.sourcegitcommit: d551ddf8d6c0fd3a884c9852bc4443c1a1485899
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/07/2018
ms.locfileid: "37903805"
---
# <a name="connect-an-mxchip-iot-devkit-device-to-your-azure-iot-central-application"></a>Připojení MXChip IoT DevKit zařízení do aplikace Azure IoT Central

Tento článek popisuje, jak jako vývojář zařízení pro připojení MXChip IoT DevKit (DevKit) zařízení do aplikace Microsoft Azure IoT Central.

## <a name="before-you-begin"></a>Než začnete

K dokončení kroků v tomto článku budete potřebovat následující:

1. Azure IoT Central aplikace vytvořené z **ukázka Devkits** šablony aplikace. Další informace najdete v tématu [vytvoření aplikace Azure IoT Central](howto-create-application.md).
1. DevKit zařízení. Koupit DevKit zařízení, najdete v tématu [MXChip IoT DevKit](http://mxchip.com/az3166).

Aplikace vytvořené z **ukázka Devkits** zahrnuje šablony aplikace **MXChip** šablona zařízení s následujícími charakteristikami:

### <a name="measurements"></a>Měření

#### <a name="telemetry"></a>Telemetrická data 

| Název pole     | Jednotky  | Minimální | Maximum | Desetinná místa |
| -------------- | ------ | ------- | ------- | -------------- |
| vlhkost       | %      | 0       | 100     | 0              |
| temp           | ° C     | -40     | 120     | 0              |
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

#### <a name="states"></a>Stavy 

| Název          | Zobrazované jméno   | NORMÁLNÍ | UPOZORNĚNÍ: | NEBEZPEČÍ | 
| ------------- | -------------- | ------ | ------- | ------ | 
| Devicestate povolená   | Stav zařízení   | Zelená  | Orange  | Červená    | 

#### <a name="events"></a>Události 

| Název             | Zobrazované jméno      | 
| ---------------- | ----------------- | 
| ButtonBPressed   | Stiskne tlačítko B  | 



### <a name="settings"></a>Nastavení

Číselné nastavení

| Zobrazované jméno | Název pole | Jednotky | Desetinná místa | Minimální | Maximum | Počáteční |
| ------------ | ---------- | ----- | -------------- | ------- | ------- | ------- |
| Snímač napětí      | setVoltage | Intenzita | 0              | 0       | 240     | 0       |
| Aktuální      | setCurrent | A  | 0              | 0       | 100     | 0       |
| Ventilátor rychlost    | fanSpeed   | OT. / MIN   | 0              | 0       | 1000    | 0       |

Přepínací tlačítko Nastavení

| Zobrazované jméno | Název pole | Na text | Vypnout text | Počáteční |
| ------------ | ---------- | ------- | -------- | ------- |
| PROSTŘEDÍ IR           | activateIR | ON      | OFF      | Vypnuto     |

### <a name="properties"></a>Vlastnosti

| Typ            | Zobrazované jméno | Název pole | Typ dat |
| --------------- | ------------ | ---------- | --------- |
| Vlastnosti zařízení | Kostka čísla   | dieNumber  | číslo    |
| Text            | Umístění     | location   | neuvedeno       |


### <a name="add-a-real-device"></a>Přidání skutečného zařízení

V aplikaci Azure IoT Central přidat z reálného zařízení **MXChip** šablona zařízení a zkontrolujte poznamenejte si připojovací řetězec zařízení. Další informace najdete v tématu [skutečné zařízení přidat do aplikace Azure IoT Central](tutorial-add-device.md).

## <a name="prepare-the-devkit-device"></a>Připravte zařízení DevKit

> [!NOTE]
> Pokud jste už dřív použili zařízení a máte Wi-Fi přihlašovací údaje uloženy a chcete změnit konfiguraci zařízení používat jinou síť Wi-Fi, připojovací řetězec nebo telemetrická data měření, stiskněte klávesu i **A** a **B** tlačítka na panelu současně. Pokud to nepomůže, stiskněte **resetování** tlačítko a zkuste to znovu.

K přípravě DevKit zařízení:

1. Stáhněte si nejnovější předem sestavených Azure IoT Central firmware pro MXChip z [uvolní](https://github.com/Azure/iot-central-firmware/releases) stránku na Githubu. Název souboru ke stažení na stránce vydání vypadá jako `AZ3166-IoT-Central-X.X.X.bin`.

1. Připojte zařízení DevKit na vývojovém počítači pomocí kabelu USB. Ve Windows otevře se okno Průzkumníka souborů na jednotce namapované na úložiště na zařízení DevKit. Například může být volán na jednotce **AZ3166 (D:)**.

1. Přetáhněte **iotCentral.bin** soubor do okna jednotky. Po dokončení kopírování s novým firmwarem restartování zařízení.

1. Po restartu zařízení DevKit, zobrazí se následující obrazovka:

    ```
    Connect HotSpot:
    AZ3166_??????
    go-> 192.168.0.1 
    PIN CODE xxxxx
    ```

    > [!NOTE]
    > Pokud na obrazovce se zobrazí cokoli jiného, stiskněte **resetování** na zařízení tlačítko. 

1. Zařízení je nyní v režimu přístupu bod (přístupový bod). Můžete se připojit k této přístupový bod Wi-Fi ze svého počítače nebo mobilního zařízení.

1. V počítači telefon nebo tablet připojit k názvu sítě Wi-Fi na obrazovce zařízení. Když se připojíte k této síti, nemáte přístup k Internetu. Tento stav se očekává a jste pouze připojení k této síti krátkou dobu při konfiguraci zařízení.

1. Otevřete webový prohlížeč a přejděte do [ http://192.168.0.1/start ](http://192.168.0.1/start). Následující příkaz zobrazí webová stránka:

    ![Stránka Konfigurace zařízení](media/howto-connect-devkit/configpage.png)

    Na webové stránce: 
    - Přidat název sítě Wi-Fi 
    - vaše heslo sítě Wi-Fi 
    - KÓD PIN, zobrazí na zařízení LCD 
    - připojovací řetězec zařízení. 
      Můžete najít připojovací řetězec @ `https://apps.iotcentral.com`  ->  `Device Explorer`  ->  `Device`  ->  `Select or Create a new Real Device`  ->  `Connect this device` (v pravém horním rohu) 
    - Vyberte všechny dostupné telemetrie měření! 

1. Po zvolení **konfigurovat zařízení**, zobrazí tato stránka:

    ![Zařízení nakonfigurovaná](media/howto-connect-devkit/deviceconfigured.png)

1. Stisknutím klávesy **resetování** tlačítko na vašem zařízení.



## <a name="view-the-telemetry"></a>Zobrazení telemetrických dat

Po restartu zařízení DevKit obrazovku na zařízení zobrazí:

* Počet telemetrické zprávy odesílané.
* Počet chyb.
* Počet přijatých požadované vlastnosti a počet ohlášených vlastností odeslaných.

Zatřeste zařízením přírůstek počet ohlášených vlastností odeslaných. Zařízení odesílá náhodné číslo jako **kostka číslo** vlastnosti zařízení.

Můžete zobrazit telemetrická data měření a hodnoty ohlášených vlastností a nastavení v Azure IoT Central:

1. Použití **Device Explorer** přejděte **měření** stránky pro skutečné zařízení MXChip jste přidali:

    ![Přejděte na skutečných zařízení](media/howto-connect-devkit/realdevice.png)

1. Na **měření** stránky, můžete zobrazit telemetrická data přicházející z MXChip zařízení:

    ![Zobrazení telemetrie z reálného zařízení](media/howto-connect-devkit/realtelemetry.png)

1. Na **vlastnosti** stránky, můžete zobrazit poslední číslo oznámí zařízení:

    ![Zobrazení vlastností zařízení](media/howto-connect-devkit/deviceproperties.png)

1. Na **nastavení** stránce nastavení na zařízení MXChip lze aktualizovat:

    ![Zobrazení nastavení zařízení](media/howto-connect-devkit/settings.png)

## <a name="download-the-source-code"></a>Stáhněte si zdrojový kód

Pokud chcete prozkoumat a upravit kód zařízení, můžete si ho stáhnout z Githubu. Pokud budete chtít upravit kód, postupujte podle těchto pokynů a [Příprava vývojového prostředí](https://microsoft.github.io/azure-iot-developer-kit/docs/get-started/#step-5-prepare-the-development-environment) pro desktopové operační systém.

Pokud chcete stáhnout zdrojový kód, spusťte následující příkaz na desktopovém počítači:

```cmd/sh
git clone https://github.com/Azure/iot-central-firmware
```

Předchozí příkaz načte zdrojový kód do složky s názvem `iot-central-firmware`. 

> [!NOTE]
> Pokud **git** není nainstalovaný ve vašem vývojovém prostředí, můžete ji stáhnout [ https://git-scm.com/download ](https://git-scm.com/download).

## <a name="review-the-code"></a>Kontrola kódu

Použití Visual Studio Code, která byla nainstalována během přípravy vývojového prostředí, otevřete `AZ3166` složky `iot-central-firmware` složky: 

![Visual Studio Code](media/howto-connect-devkit/vscodeview.png)

Chcete-li zjistit, jak je odesílají telemetrická data do aplikace Azure IoT Central, otevřete **main_telemetry.cpp** souboru ve zdrojové složce.

Funkce `buildTelemetryPayload` vytvoří datovou část JSON telemetrická data pomocí dat z snímačům na zařízení.

Funkce `sendTelemetryPayload` volání `sendTelemetry` v **iotHubClient.cpp** Odeslat datovou část JSON pro Centrum IoT Azure IoT Central aplikace používá.

Chcete-li zjistit, jak hodnoty vlastností jsou k aplikaci Azure IoT Central, otevřete **main_telemetry.cpp** souboru ve zdrojové složce.

Funkce `telemetryLoop` odešle **doubleTap** hlášené vlastnosti, když zjistí akcelerometr dvojitým klepnutím. Používá `sendReportedProperty` fungovat v **iotHubClient.cpp** zdrojový soubor.

Kód v **iotHubClient.cpp** zdrojový soubor používá funkce z [ sadami SDK služby Microsoft Azure IoT a knihovny pro jazyk C](https://github.com/Azure/azure-iot-sdk-c) k interakci se službou IoT Hub.

Informace o tom, jak změnit, vytvořit a nahrát ukázkový kód do vašeho zařízení, najdete v článku **readme.md** soubor `AZ3166` složky.

## <a name="next-steps"></a>Další postup

Teď, když jste se naučili, jak připojit zařízení DevKit do aplikace Azure IoT Central, tady jsou další navrhované kroky:

* [Příprava a připojení Raspberry Pi](howto-connect-raspberry-pi-python.md)