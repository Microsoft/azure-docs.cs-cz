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
ms.openlocfilehash: 1bfa52d07ddc4eaf86bce17c0ad7c63493a8b5fd
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2018
ms.locfileid: "51255723"
---
# <a name="connect-an-mxchip-iot-devkit-device-to-your-azure-iot-central-application"></a>Připojení MXChip IoT DevKit zařízení do aplikace Azure IoT Central

Tento článek popisuje, jak jako vývojář zařízení pro připojení MXChip IoT DevKit (DevKit) zařízení do aplikace Microsoft Azure IoT Central.

## <a name="before-you-begin"></a>Než začnete

K dokončení kroků v tomto článku budete potřebovat následující:

1. Azure IoT Central aplikace vytvořené z **ukázka Devkits** šablony aplikace. Další informace najdete v tématu [vytvořit aplikaci rychlý Start](quick-deploy-iot-central.md).
1. DevKit zařízení. Koupit DevKit zařízení, najdete v tématu [MXChip IoT DevKit](http://mxchip.com/az3166).


## <a name="sample-devkits-application"></a>**Ukázkový Devkits** aplikace

Aplikace vytvořené z **ukázka Devkits** zahrnuje šablony aplikace **MXChip** šablona zařízení s následujícími charakteristikami: 

- Telemetrická data, která obsahuje měření pro zařízení **vlhkosti**, **teploty**, **tlak**, **Magnometer** (měřeno podél X Y, Z osy), **Accelorometer** (měří podél X, Y, Z osy) a **volný setrvačník** (měří podél X, Y, osy Z).
- Stav, který obsahuje příklad měření **stav zařízení**.
- Měření událostí s **stiskne tlačítko B** událostí. 
- Nastavení zobrazení **napětí**, **aktuální**, **ventilátor rychlost**a **reakcí na Incidenty** přepínací tlačítko.
- Vlastnosti obsahující vlastnosti zařízení **kostka číslo** a **umístění zařízení** což je vlastnost umístění stejně jako v **vyroben v** cloudové vlastnosti. 


Všechny podrobnosti o konfiguraci najdete [Podrobnosti šablony MXChip zařízení](howto-connect-devkit.md#mxchip-device-template-details)


## <a name="add-a-real-device"></a>Přidání skutečného zařízení

V aplikaci Azure IoT Central přidat z reálného zařízení **MXChip** šablona zařízení a zaznamenání podrobností o připojení zařízení (**ID oboru, ID zařízení a primární klíč**).

1. Přidat **skutečné zařízení** z Device Explorer, klikněte na **+ nový > skutečné** skutečné zařízení přidat.
    * Zadejte Id zařízení **<span style="color:Red">(by měl být malými písmeny)</span>** nebo použijte navrhovaný ID zařízení.
    * Zadejte název zařízení nebo použijte navrhovaný název
    
    ![Přidání zařízení](media\concepts-connectivity\add-device.png)


1. Získat podrobnosti o připojení, jako **ID oboru, ID zařízení a primární klíč** pro přidání zařízení kliknutím **připojit** na stránce zařízení.
 
    ![Podrobnosti připojení](media\concepts-connectivity\device-connect.PNG)

3. Ujistěte se, že chcete uložit tyto podrobnosti, jako je bude temporaritly získat odpojení od Internetu během přípravy zařízení DevKit. 


### <a name="prepare-the-devkit-device"></a>Připravte zařízení DevKit

> [!NOTE]
> Pokud jste už dřív použili zařízení a máte Wi-Fi přihlašovací údaje uloženy a chcete změnit konfiguraci zařízení používat jinou síť Wi-Fi, připojovací řetězec nebo telemetrická data měření, stiskněte klávesu i **A** a **B** tlačítka na panelu současně. Pokud to nepomůže, stiskněte **resetování** tlačítko a zkuste to znovu.



#### <a name="to-prepare-the-devkit-device"></a>K přípravě DevKit zařízení:


1. Stáhněte si nejnovější předem sestavených Azure IoT Central firmware pro MXChip z [uvolní](https://aka.ms/iotcentral-docs-MXChip-releases) stránku na Githubu.
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
    > Pokud se na obrazovce se zobrazí cokoli jiného, obnovit zařízení a stiskněte klávesu **A** a **B** tlačítka na zařízení ve stejnou dobu až po restartování zařízení. 

1. Zařízení je nyní v režimu přístupu bod (přístupový bod). Můžete se připojit k této přístupový bod Wi-Fi ze svého počítače nebo mobilního zařízení.

1. V počítači telefon nebo tablet připojit k názvu sítě Wi-Fi na obrazovce zařízení. Když se připojíte k této síti, nemáte přístup k Internetu. Tento stav se očekává a jste pouze připojení k této síti krátkou dobu při konfiguraci zařízení.

1. Otevřete webový prohlížeč a přejděte do [ http://192.168.0.1/start ](http://192.168.0.1/start). Následující příkaz zobrazí webová stránka:

    ![Stránka Konfigurace zařízení](media/howto-connect-devkit/configpage.png)

    Na webové stránce: 
    - Přidat název sítě Wi-Fi 
    - vaše heslo sítě Wi-Fi
    - KÓD PIN, zobrazí na zařízení LCD 
    - Podrobnosti o připojení **Id oboru, Id zařízení a primární klíč** vašeho zařízení (by již uložení tohoto postupu)      
    - Vyberte všechny dostupné telemetrie měření! 

1. Po zvolení **konfigurovat zařízení**, zobrazí tato stránka:

    ![Zařízení nakonfigurovaná](media/howto-connect-devkit/deviceconfigured.png)

1. Stisknutím klávesy **resetování** tlačítko na vašem zařízení.


## <a name="view-the-telemetry"></a>Zobrazení telemetrických dat

Po restartu zařízení DevKit obrazovku na zařízení zobrazí:

* Počet telemetrické zprávy odesílané.
* Počet chyb.
* Počet přijatých požadované vlastnosti a počet ohlášených vlastností odeslaných.

> [!NOTE]
> Pokud se zařízení zobrazí na opakování při kontrole připojení, pokud je zařízení *blokováno* v IoT Central a *Odblokovat* zařízení, aby se mohl připojit k aplikaci.

Zatřeste zařízením přírůstek počet ohlášených vlastností odeslaných. Zařízení odesílá náhodné číslo jako **kostka číslo** vlastnosti zařízení.

Můžete zobrazit telemetrická data měření a hodnoty ohlášených vlastností a nastavení v Azure IoT Central:

1. Použití **Device Explorer** přejděte **měření** stránky pro skutečné zařízení MXChip jste přidali:

    ![Přejděte na skutečných zařízení](media/howto-connect-devkit/realdevicenew.png)

1. Na **měření** stránky, můžete zobrazit telemetrická data přicházející z MXChip zařízení:

    ![Zobrazení telemetrie z reálného zařízení](media/howto-connect-devkit/devicetelemetrynew.png)

1. Na **vlastnosti** stránky, můžete zobrazit poslední číslo a umístění zařízení zařízení:

    ![Zobrazení vlastností zařízení](media/howto-connect-devkit/devicepropertynew.png)

1. Na **nastavení** stránce nastavení na zařízení MXChip lze aktualizovat:

    ![Zobrazení nastavení zařízení](media/howto-connect-devkit/devicesettingsnew.png)

1. Na **řídicí panel** stránky, zobrazí se umístění mapy

    ![Zobrazení řídicího panelu zařízení](media/howto-connect-devkit/devicedashboardnew.png)


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

## <a name="mxchip-device-template-details"></a>Podrobnosti o zařízení MXChip šablony 

Aplikace vytvořené z této šablony Devkits ukázkové aplikace zahrnovat šablonu MXChip zařízení s následujícími charakteristikami:

### <a name="measurements"></a>Měření

#### <a name="telemetry"></a>Telemetrická data 

| Název pole     | Jednotky  | Minimální | Maximum | Desetinná místa |
| -------------- | ------ | ------- | ------- | -------------- |
| vlhkost       | %      | 0       | 100     | 0              |
| temp           | ° C     | -40     | 120     | 0              |
| tlak       | hPa    | 260     | 1260    | 0              |
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
| IR           | activateIR | ON      | OFF      | Vypnuto     |

### <a name="properties"></a>Vlastnosti

| Typ            | Zobrazované jméno | Název pole | Typ dat |
| --------------- | ------------ | ---------- | --------- |
| Vlastnosti zařízení | Kostka čísla   | dieNumber  | číslo    |
| Vlastnosti zařízení | Umístění zařízení   | location  | location    |
| Text            | Vyroben v     | manufacturedIn   | neuvedeno       |



## <a name="next-steps"></a>Další postup

Teď, když jste se naučili, jak připojit zařízení DevKit do aplikace Azure IoT Central, tady jsou další navrhované kroky:

* [Příprava a připojení Raspberry Pi](howto-connect-raspberry-pi-python.md)
