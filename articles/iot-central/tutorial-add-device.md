---
title: Přidání skutečného zařízení do aplikace Azure IoT Central | Microsoft Docs
description: Jako operátor přidejte skutečné zařízení do aplikace Azure IoT Central.
author: sandeeppujar
ms.author: sandeepu
ms.date: 04/16/2018
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: peterpr
ms.openlocfilehash: 2e01f61ff915a8fe4327aa78c8867d666dc36fda
ms.sourcegitcommit: 776b450b73db66469cb63130c6cf9696f9152b6a
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/18/2018
ms.locfileid: "45983214"
---
# <a name="tutorial-add-a-real-device-to-your-azure-iot-central-application"></a>Kurz: Přidání skutečného zařízení do aplikace Azure IoT Central

V tomto kurzu se naučíte, jak přidat skutečné zařízení do aplikace Microsoft Azure IoT Central a nakonfigurovat ho.

Tento kurz se skládá ze dvou částí:

1. Nejprve se jako operátor naučíte přidat skutečné zařízení do aplikace Azure IoT Central a nakonfigurovat ho. Na konci této části načtete připojovací řetězec, který použijete v druhé části.
2. Potom se jako vývojář zařízení dozvíte informace o kódu ve skutečném zařízení. Přidáte připojovací řetězec z první části do vzorového kódu.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Přidat nové skutečné zařízení
> * Nakonfigurovat nové zařízení
> * Získat připojovací řetězec pro skutečné zařízení z aplikace
> * Porozumět, jak se klientský kód mapuje k aplikaci
> * Nakonfigurovat klientský kód pro skutečné zařízení

## <a name="prerequisites"></a>Požadavky

Než se pustíte do práce, měl by tvůrce dokončit minimálně první kurz pro tvůrce a vytvořit aplikaci Azure IoT Central:

* [Definování nového typu zařízení](tutorial-define-device-type.md) (povinné)
* [Konfigurace pravidel a akcí pro zařízení](tutorial-configure-rules.md) (nepovinné)
* [Přizpůsobení zobrazení operátora](tutorial-customize-operator.md) (nepovinné)

## <a name="add-a-real-device"></a>Přidání skutečného zařízení

Pokud chcete přidat skutečné zařízení do aplikace, použijete šablonu zařízení **Connected Air Conditioner** (Připojené klimatizační zařízení), kterou jste vytvořili v kurzu [Definování nového typu zařízení](tutorial-define-device-type.md).

1. Pokud chcete nové zařízení přidat jako operátor, zvolte v levé navigační nabídce možnost **Device Explorer**:

   ![Stránka Device Explorer zobrazující připojené klimatizační zařízení](media/tutorial-add-device/explorer.png)

   **Device Explorer** zobrazuje šablonu zařízení **Connected Air Conditioner** a simulované zařízení, které se automaticky vytvořilo při vytváření šablony zařízení tvůrcem.

2. Pokud chcete začít s připojením skutečného klimatizačního zařízení, zvolte **New** (Nové) a potom **Real** (Skutečné):

   ![Zahájení přidávání nového skutečného klimatizačního zařízení](media/tutorial-add-device/newreal.png)

3. Zadejte ID zařízení (**musí být malými písmeny**) nebo použijte navrhované ID zařízení. Můžete také zadat název nového zařízení.  

   ![Přejmenování zařízení](media/tutorial-add-device/rename.png)

## <a name="configure-a-real-device"></a>Konfigurace skutečného zařízení

Skutečné zařízení se vytvoří ze šablony **Connected Air Conditioner**. Jako tvůrce můžete prostřednictvím stránky **Settings** nakonfigurovat zařízení a nastavit hodnoty vlastnosti pro záznam informací o zařízení.

1. Na stránce **Settings** si všimněte, že stav nastavení **Set temperature** (Nastavit teplotu) je **no update** (Bez aktualizace). Tento stav zůstává beze změny až do té doby, než se skutečné zařízení připojí a potvrdí, že na nastavení reaguje:

    ![Nastavení zobrazující synchronizaci](media/tutorial-add-device/settingssyncing.png)

2. Na stránce **Properties** (Vlastnosti) nového klimatizačního zařízení, které je skutečně připojené, nastavte **Serial Number** (Sériové číslo) **10001** a **Firmware version** (Verze firmwaru) na 9.75. Potom zvolte **Save** (Uložit):

    ![Nastavení vlastností skutečného zařízení](media/tutorial-add-device/setproperties.png)

3. Jako tvůrce můžete zobrazit stránky **Measurements** (Měření), **Rules** (Pravidla) a **Dashboard** (Řídicí panel) pro svoje skutečné zařízení.

## <a name="get-connection-details-for-real-device-from-application"></a>Získání podrobností z aplikace kvůli připojení skutečného zařízení

Vývojář zařízení musí vložit *podrobnosti o skutečně připojeném zařízení* do kódu, který běží v zařízení. Připojovací řetězec umožňuje, aby se zařízení bezpečně připojilo k aplikaci Azure IoT Central. Následující postup znázorňuje, jak v aplikaci vyhledat připojovací řetězec pro instanci zařízení:

1. Na obrazovce **Device** (Zařízení) skutečného připojeného klimatizačního zařízení zvolte **Connect this device** (Připojit toto zařízení):

    ![Stránka zařízení s odkazem pro zobrazení informací o připojení](media/tutorial-add-device/connectionlink.png)

2. Na stránce **Connect** (Připojit) zkopírujte hodnoty **Scope ID, Device ID a Primary key** (ID oboru, ID zařízení a Primární klíč) a uložte je.

   ![Podrobnosti připojení](media/tutorial-add-device/device-connect.PNG)

   K získání připojovacího řetězce zařízení použijte následující nástroj příkazového řádku:  

    ```cmd/sh
    npm i -g dps-keygen
    ```
    **Použití**
    
    K vytvoření připojovacího řetězce potřebujete najít binární hodnotu, která je ve složce kontejneru.
    ```cmd/sh
    dps_cstr <scope_id> <device_id> <Primary Key(for device)>
    ```
    Další informace o [nástroji příkazového řádku najdete zde](https://www.npmjs.com/package/dps-keygen).

## <a name="prepare-the-client-code"></a>Příprava klientského kódu

Ukázkový kód v tomto článku je napsán v [Node.js](https://nodejs.org/) a zobrazuje dostatečnou část kódu k tomu, abyste mohli provést tyto akce:

* Připojit zařízení k aplikaci Azure IoT Central
* Odeslat teplotní telemetrii jako připojené klimatizační zařízení
* Odpovědět operátorovi, který používá nastavení **Set Temperature**

Články s postupy, na které najdete odkazy v části [Další kroky](#next-steps), poskytují kompletnější ukázky a předvádí použití ostatních programovacích jazyků. Další informace o tom, jak se zařízení připojují k aplikaci Azure IoT Central, najdete v článku věnovaném [možnostem připojení zařízení](concepts-connectivity.md).

Následující postup popisuje, jak připravit ukázku [Node.js](https://nodejs.org/):

1. Na počítač nainstalujte [Node.js](https://nodejs.org/) verze 4.0.x nebo novější. Node.js je k dispozici pro širokou škálu operačních systémů.

2. Na počítači vytvořte složku s názvem `connectedairconditioner`.

3. V prostředí příkazového řádku přejděte na složku `connectedairconditioner`, kterou jste vytvořili.

4. Pokud chcete inicializovat projekt Node.js, spusťte následující příkaz a přijměte všechny výchozí hodnoty:

   ```cmd/sh
   npm init
   ```

5. Potřebné balíčky nainstalujte spuštěním následujícího příkazu:

   ```cmd/sh
   npm install azure-iot-device azure-iot-device-mqtt --save
   ```

6. V textovém editoru vytvořte soubor s názvem **ConnectedAirConditioner.js** ve složce `connectedairconditioner`.

7. Na začátek souboru **ConnectedAirConditioner.js** přidejte následující příkazy `require`:

   ```javascript
   'use strict';

   var clientFromConnectionString = require('azure-iot-device-mqtt').clientFromConnectionString;
   var Message = require('azure-iot-device').Message;
   var ConnectionString = require('azure-iot-device').ConnectionString;
   ```

8. Do souboru přidejte následující deklarace proměnných:

 

   ```javascript
   var connectionString = '{your device connection string}';
   var targetTemperature = 0;
   var client = clientFromConnectionString(connectionString);
   ```
   

   > [!NOTE]
   > Zástupný symbol `{your device connection string}` aktualizujete později. 

9. Uložte změny, které jste až dosud provedli, ale ponechejte soubor otevřený.

## <a name="understand-how-client-code-maps-to-the-application"></a>Jak se klientský kód mapuje k aplikaci

V předchozí části jste vytvořili kostru projektu Node.js pro aplikaci, která se připojuje k aplikaci Azure IoT Central. V této části přidáte kód, abyste mohli provést tyto akce:

* Připojit se k aplikaci Azure IoT Central
* Odeslat telemetrii do aplikace Azure IoT Central
* Přijmout nastavení z aplikace Azure IoT Central

1. Pokud chcete odeslat telemetrii do aplikace Azure IoT Central, přidejte následující kód do souboru **ConnectedAirConditioner.js**:

   ```javascript
   // Send device telemetry.
   function sendTelemetry() {
     var temperature = targetTemperature + (Math.random() * 15);
     var data = JSON.stringify({ temperature: temperature });
     var message = new Message(data);
     client.sendEvent(message, (err, res) => console.log(`Sent message: ${message.getData()}` +
       (err ? `; error: ${err.toString()}` : '') +
       (res ? `; status: ${res.constructor.name}` : '')));
   }
   ```

   Název pole v JSON, který odesíláte, musí odpovídat názvu pole zadaného pro teplotní telemetrii v šabloně zařízení. V tomto příkladu se pole nazývá **temperature** (Teplota).

2. Pokud chcete definovat nastavení, které vaše zařízení podporuje, například **setTemperature**, přidejte následující definici:

   ```javascript
   // Add any settings your device supports
   // mapped to a function that is called when the setting is changed.
   var settings = {
     'setTemperature': (newValue, callback) => {
       // Simulate the temperature setting taking two steps.
       setTimeout(() => {
         targetTemperature = targetTemperature + (newValue - targetTemperature) / 2;
         callback(targetTemperature, 'pending');
         setTimeout(() => {
           targetTemperature = newValue;
           callback(targetTemperature, 'completed');
         }, 5000);
       }, 5000);
     }
   };
   ```

3. Abyste mohli zpracovat nastavení odeslaná z aplikace Azure IoT Central, přidejte následující funkci, která vyhledá a spustí příslušný kód zařízení:

   ```javascript
   // Handle settings changes that come from Azure IoT Central via the device twin.
   function handleSettings(twin) {
     twin.on('properties.desired', function (desiredChange) {
       for (let setting in desiredChange) {
         if (settings[setting]) {
           console.log(`Received setting: ${setting}: ${desiredChange[setting].value}`);
           settings[setting](desiredChange[setting].value, (newValue, status, message) => {
             var patch = {
               [setting]: {
                 value: newValue,
                 status: status,
                 desiredVersion: desiredChange.$version,
                 message: message
               }
             }
             twin.properties.reported.update(patch, (err) => console.log(`Sent setting update for ${setting}; ` +
               (err ? `error: ${err.toString()}` : `status: success`)));
           });
         }
       }
     });
   }
   ```

    Tato funkce:

    * sleduje odeslání požadované vlastnosti z aplikace Azure IoT Central,
    * vyhledá příslušnou funkci, která se má zavolat, aby se zpracovala změna nastavení,
    * odešle potvrzení zpět do aplikace Azure IoT Central.

4. Přidejte následující kód, abyste dokončili připojení k aplikaci Azure IoT Central a připojili funkce v klientském kódu:

   ```javascript
   // Handle device connection to Azure IoT Central.
   var connectCallback = (err) => {
     if (err) {
       console.log(`Device could not connect to Azure IoT Central: ${err.toString()}`);
     } else {
       console.log('Device successfully connected to Azure IoT Central');
        // Send telemetry measurements to Azure IoT Central every 1 second.
       setInterval(sendTelemetry, 1000);
        // Get device twin from Azure IoT Central.
       client.getTwin((err, twin) => {
         if (err) {
           console.log(`Error getting device twin: ${err.toString()}`);
         } else {
           // Apply device settings and handle changes to device settings.
           handleSettings(twin);
         }
       });
     }
   };

   client.open(connectCallback);
   ```

5. Uložte změny, které jste až dosud provedli, ale ponechejte soubor otevřený.

## <a name="configure-client-code-for-the-real-device"></a>Nakonfigurovat klientský kód pro skutečné zařízení

<!-- Add the connection string to the sample code, build, and run --> Pokud chcete nakonfigurovat klientský kód na připojení k aplikaci Azure IoT Central, musíte přidat připojovací řetězec skutečného zařízení, který jste si poznamenali v předchozí části tohoto kurzu.

1. V souboru **ConnectedAirConditioner.js** vyhledejte následující řádek kódu:

   ```javascript
   var connectionString = '{your device connection string}';
   ```

2. Nahraďte řetězec `{your device connection string}` připojovacím řetězcem skutečného zařízení. Na konci části Získání připojovacího řetězce pro skutečné zařízení z aplikace jste si poznamenali připojovací řetězec.

3. Uložte změny v souboru **ConnectedAirConditioner.js**.

4. Ukázku spustíte tak, že v prostředí příkazového řádku spustíte následující příkaz:

   ```cmd/sh
   node ConnectedAirConditioner.js
   ```

   > [!NOTE]
   > Zkontrolujte, že se nacházíte ve složce `connectedairconditioner`, když spouštíte tento příkaz.

5. Aplikace zobrazí výstup do konzoly:

   ![Výstup klientské aplikace](media/tutorial-add-device/output.png)

6. Přibližně po 30 sekundách uvidíte telemetrii na stránce zařízení **Measurements**:

   ![Skutečná telemetrie](media/tutorial-add-device/realtelemetry.png)

7. Na stránce **Settings** uvidíte, že nastavení je nyní synchronizováno. Při prvním připojení zařízení obdrželo hodnotu nastavení a potvrdilo změnu:

   ![Nastavení synchronizováno](media/tutorial-add-device/settingsynced.png)

8. Na stránce **Settings** nastavte teplotu zařízení na **95** a zvolte **Update device** (Aktualizovat zařízení). Ukázková aplikace obdrží tuto změnu a zpracuje ji:

   ![Příjem a zpracování nastavení](media/tutorial-add-device/receivesetting.png)

   > [!NOTE]
   > Existují dvě zprávy týkající se aktualizace nastavení. Jedna zpráva se používá při odeslání stavu `pending` a druhá při odeslání stavu `completed`.

1. Na stránce **Measurements** si můžete všimnout, že zařízení posílá vyšší teplotní hodnoty:

    ![Teplotní telemetrie je nyní vyšší.](media/tutorial-add-device/highertemperature.png)

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili:

> [!div class="nextstepaction"]
> * Přidat nové skutečné zařízení
> * Nakonfigurovat nové zařízení
> * Získat připojovací řetězec pro skutečné zařízení z aplikace
> * Porozumět, jak se klientský kód mapuje k aplikaci
> * Nakonfigurovat klientský kód pro skutečné zařízení

Nyní, když máte skutečné zařízení připojeno k aplikaci Azure IoT Central, můžete přejít k dalším navrhovaným krokům:

Jako operátor se naučíte tyto postupy:

* [Správa zařízení](howto-manage-devices.md)
* [Použití sad zařízení](howto-use-device-sets.md)
* [Vytvoření vlastní analýzy](howto-create-analytics.md)

Jako vývojář zařízení se naučíte tyto postupy:

* [Příprava a připojení DevKit](howto-connect-devkit.md)
* [Příprava a připojení Raspberry Pi](howto-connect-raspberry-pi-python.md)
* [Připojení obecného klienta Node.js k aplikaci Azure IoT Central](howto-connect-nodejs.md)