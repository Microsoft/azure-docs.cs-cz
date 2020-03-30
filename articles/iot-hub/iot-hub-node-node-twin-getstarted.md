---
title: Začínáme s dvojčaty zařízení Azure IoT Hub (uzel) | Dokumenty společnosti Microsoft
description: Jak použít dvojčata zařízení Azure IoT Hub k přidání značek a pak použít dotaz služby IoT Hub. Sady Azure IoT SDK pro node.js slouží k implementaci aplikace simulovaných zařízení a aplikace služby, která přidá značky a spustí dotaz služby IoT Hub.
author: fsautomata
ms.service: iot-hub
services: iot-hub
ms.devlang: nodejs
ms.topic: conceptual
ms.date: 08/26/2019
ms.author: elioda
ms.openlocfilehash: 55dc7f73a3e5bbff2e6e331ba0bd7d4088a86536
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77110825"
---
# <a name="get-started-with-device-twins-nodejs"></a>Začínáme s dvojčaty zařízení (Node.js)

[!INCLUDE [iot-hub-selector-twin-get-started](../../includes/iot-hub-selector-twin-get-started.md)]

Na konci tohoto kurzu budete mít dvě konzolové aplikace Node.js:

* **AddTagsAndQuery.js**, back-end aplikace Node.js, která přidává značky a dotazy dvojčata zařízení.

* **TwinSimulatedDevice.js**, aplikace Node.js, která simuluje zařízení, které se připojuje k centru IoT s identitou zařízení vytvořenou dříve a hlásí stav připojení.

> [!NOTE]
> Článek [sady Azure IoT SDK obsahuje](iot-hub-devguide-sdks.md) informace o sadách Azure IoT SDK, které můžete použít k vytváření aplikací pro zařízení i back-end.
>

## <a name="prerequisites"></a>Požadavky

Pro absolvování tohoto kurzu potřebujete:

* Node.js verze 10.0.x nebo novější.

* Aktivní účet Azure. (Pokud nemáte účet, můžete si vytvořit [bezplatný účet](https://azure.microsoft.com/pricing/free-trial/) během několika minut.)

* Zkontrolujte, zda je v bráně firewall otevřený port 8883. Ukázka zařízení v tomto článku používá protokol MQTT, který komunikuje přes port 8883. Tento port může být blokován v některých prostředích podnikové a vzdělávací sítě. Další informace a způsoby, jak tento problém vyřešit, najdete [v tématu připojení k centru IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

## <a name="create-an-iot-hub"></a>Vytvoření centra IoT

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Registrace nového zařízení v centru IoT hub

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

## <a name="get-the-iot-hub-connection-string"></a>Získání připojovacího řetězce centra IoT hub

[!INCLUDE [iot-hub-howto-twin-shared-access-policy-text](../../includes/iot-hub-howto-twin-shared-access-policy-text.md)]

[!INCLUDE [iot-hub-include-find-custom-connection-string](../../includes/iot-hub-include-find-custom-connection-string.md)]

## <a name="create-the-service-app"></a>Vytvoření aplikace služby

V této části vytvoříte konzolovou aplikaci Node.js, která přidá metadata umístění do dvojčete zařízení přidruženého k **myDeviceId**. Potom se dotazuje dvojčata zařízení uložená v centru IoT výběru zařízení umístěných v USA a potom ty, které hlásí mobilní připojení.

1. Vytvořte novou prázdnou složku s názvem **addtagsandqueryapp**. Ve složce **addtagsandqueryapp** vytvořte nový soubor package.json pomocí následujícího příkazu na příkazovém řádku. Parametr `--yes` přijímá všechny výchozí hodnoty.

    ```cmd/sh
    npm init --yes
    ```

2. Na příkazovém řádku ve složce **addtagsandqueryapp** spusťte následující příkaz pro instalaci balíčku **azure-iothub:**

    ```cmd/sh
    npm install azure-iothub --save
    ```

3. Pomocí textového editoru vytvořte nový soubor **AddTagsAndQuery.js** ve složce **addtagsandqueryapp.**

4. Přidejte následující kód do souboru **AddTagsAndQuery.js.** Nahraďte `{iot hub connection string}` připojovacím řetězcem ioT hubu, který jste zkopírovali v [části Získání připojovacího řetězce centra IoT Hub](#get-the-iot-hub-connection-string).

   ``` javascript
        'use strict';
        var iothub = require('azure-iothub');
        var connectionString = '{iot hub connection string}';
        var registry = iothub.Registry.fromConnectionString(connectionString);

        registry.getTwin('myDeviceId', function(err, twin){
            if (err) {
                console.error(err.constructor.name + ': ' + err.message);
            } else {
                var patch = {
                    tags: {
                        location: {
                            region: 'US',
                            plant: 'Redmond43'
                      }
                    }
                };

                twin.update(patch, function(err) {
                  if (err) {
                    console.error('Could not update twin: ' + err.constructor.name + ': ' + err.message);
                  } else {
                    console.log(twin.deviceId + ' twin updated successfully');
                    queryTwins();
                  }
                });
            }
        });
   ```

    Objekt **Registru** zveřejňuje všechny metody potřebné k interakci s dvojčaty zařízení ze služby. Předchozí kód nejprve inicializuje objekt **registru,** pak načte dvojče zařízení pro **myDeviceId**a nakonec aktualizuje jeho značky s informacemi o požadovaném umístění.

    Po aktualizaci značek volá **queryTwins** funkce.

5. Přidejte následující kód na konci **AddTagsAndQuery.js** k implementaci **queryTwins** funkce:

   ```javascript
        var queryTwins = function() {
            var query = registry.createQuery("SELECT * FROM devices WHERE tags.location.plant = 'Redmond43'", 100);
            query.nextAsTwin(function(err, results) {
                if (err) {
                    console.error('Failed to fetch the results: ' + err.message);
                } else {
                    console.log("Devices in Redmond43: " + results.map(function(twin) {return twin.deviceId}).join(','));
                }
            });

            query = registry.createQuery("SELECT * FROM devices WHERE tags.location.plant = 'Redmond43' AND properties.reported.connectivity.type = 'cellular'", 100);
            query.nextAsTwin(function(err, results) {
                if (err) {
                    console.error('Failed to fetch the results: ' + err.message);
                } else {
                    console.log("Devices in Redmond43 using cellular network: " + results.map(function(twin) {return twin.deviceId}).join(','));
                }
            });
        };
   ```

    Předchozí kód provede dva dotazy: první vybere pouze dvojčata zařízení umístěných v závodě **Redmond43** a druhý zpřesní dotaz a vybere pouze zařízení, která jsou také připojena prostřednictvím mobilní sítě.

    Když kód vytvoří objekt **dotazu,** určuje maximální počet vrácených dokumentů v druhém parametru. Objekt **dotazu** obsahuje logickou vlastnost **hasMoreResults,** kterou můžete použít k vyvolání metod **nextAsTwin** vícekrát k načtení všech výsledků. Metoda voláná **další** je k dispozici pro výsledky, které nejsou dvojčata zařízení, například výsledky agregační dotazy.

6. Spusťte aplikaci s:

    ```cmd/sh
        node AddTagsAndQuery.js
    ```

   Ve výsledcích dotazu byste měli vidět jedno zařízení s žádostí o všechna zařízení umístěná v **Redmond43** a žádné pro dotaz, který omezuje výsledky na zařízení, která používají mobilní síť.

   ![Zobrazení jednoho zařízení ve výsledcích dotazu](media/iot-hub-node-node-twin-getstarted/service1.png)

V další části vytvoříte aplikaci pro zařízení, která hlásí informace o připojení a změní výsledek dotazu v předchozí části.

## <a name="create-the-device-app"></a>Vytvoření aplikace pro zařízení

V této části vytvoříte konzolovou aplikaci Node.js, která se připojí k vašemu rozbočovači jako **myDeviceId**a potom aktualizuje ohlášené vlastnosti dvojčete zařízení tak, aby obsahovaly informace, které jsou připojeny pomocí mobilní sítě.

1. Vytvořte novou prázdnou složku nazvanou **reportconnectivity**. Ve složce **reportconnectivity** vytvořte nový soubor package.json pomocí následujícího příkazu na příkazovém řádku. Parametr `--yes` přijímá všechny výchozí hodnoty.

    ```cmd/sh
    npm init --yes
    ```

2. Na příkazovém řádku ve složce **reportconnectivity** spusťte následující příkaz pro instalaci balíčků **azure-iot-device**a **azure-iot-device-mqtt:**

    ```cmd/sh
    npm install azure-iot-device azure-iot-device-mqtt --save
    ```

3. Pomocí textového editoru vytvořte nový soubor **ReportConnectivity.js** ve složce **reportconnectivity.**

4. Přidejte následující kód do souboru **ReportConnectivity.js.** Nahraďte `{device connection string}` připojovacím řetězcem zařízení, který jste zkopírovali při vytváření identity zařízení **myDeviceId** v [části Registrace nového zařízení v centru IoT](#register-a-new-device-in-the-iot-hub).

    ```javascript
        'use strict';
        var Client = require('azure-iot-device').Client;
        var Protocol = require('azure-iot-device-mqtt').Mqtt;

        var connectionString = '{device connection string}';
        var client = Client.fromConnectionString(connectionString, Protocol);

        client.open(function(err) {
        if (err) {
            console.error('could not open IotHub client');
        }  else {
            console.log('client opened');

            client.getTwin(function(err, twin) {
            if (err) {
                console.error('could not get twin');
            } else {
                var patch = {
                    connectivity: {
                        type: 'cellular'
                    }
                };

                twin.properties.reported.update(patch, function(err) {
                    if (err) {
                        console.error('could not update twin');
                    } else {
                        console.log('twin state reported');
                        process.exit();
                    }
                });
            }
            });
        }
        });
    ```

    Client **Client** objekt zveřejňuje všechny metody, které potřebujete k interakci s dvojčaty zařízení ze zařízení. Předchozí kód po inicializaci objektu **Klienta** načte dvojče zařízení pro **myDeviceId** a aktualizuje jeho ohlášenou vlastnost s informacemi o připojení.

5. Spuštění aplikace zařízení

    ```cmd/sh
        node ReportConnectivity.js
    ```

    Měli byste vidět `twin state reported`zprávu .

6. Nyní, když zařízení ohlásilo informace o připojení, mělo by se zobrazit v obou dotazech. Vraťte se do složky **addtagsandqueryapp** a znovu spusťte dotazy:

    ```cmd/sh
        node AddTagsAndQuery.js
    ```

    Tentokrát **myDeviceId** by se měl zobrazit v obou výsledcích dotazu.

    ![Zobrazit myDeviceId v obou výsledcích dotazu](media/iot-hub-node-node-twin-getstarted/service2.png)

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste nakonfigurovali novou službu IoT Hub na webu Azure Portal a potom jste vytvořili identitu zařízení v registru identit ve službě IoT Hub. Přidali jste metadata zařízení jako značky z back-endové aplikace a napsali jste simulovanou aplikaci zařízení, která nahlásí informace o připojení zařízení v dvojčeti zařízení. Také jste se naučili, jak dotaz ovat tyto informace pomocí dotazovacího jazyka ioT hub u verze SQL.

Pomocí následujících zdrojů se dozvíte, jak:

* odesílání telemetrie ze zařízení pomocí výukového programu [Začínáme s ioT hubem,](quickstart-send-telemetry-node.md)

* konfigurace zařízení pomocí požadovaných vlastností dvojčete zařízení pomocí výukového kurzu [Použít požadované vlastnosti ke konfiguraci zařízení,](tutorial-device-twins.md)

* ovládací zařízení interaktivně (například zapnutí ventilátoru z uživatelem řízené aplikace), s [kurzem Použít přímé metody.](quickstart-control-device-node.md)
