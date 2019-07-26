---
title: Začínáme s využitím nevláken zařízení v Azure IoT Hub (Node) | Microsoft Docs
description: Jak používat vlákna v zařízeních Azure IoT Hub k přidávání značek a k následnému použití dotazu IoT Hub. Sady SDK Azure IoT pro Node. js můžete použít k implementaci aplikace simulovaného zařízení a aplikace služby, která přidá značky a spustí dotaz IoT Hub.
author: fsautomata
ms.service: iot-hub
services: iot-hub
ms.devlang: nodejs
ms.topic: conceptual
ms.date: 08/25/2017
ms.author: elioda
ms.openlocfilehash: 8dd5269532e9eb6139d8debb0ee9b503cd2e4354
ms.sourcegitcommit: 9dc7517db9c5817a3acd52d789547f2e3efff848
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/23/2019
ms.locfileid: "68403993"
---
# <a name="get-started-with-device-twins-node"></a>Začínáme s nevlákenou zařízení (Node)

[!INCLUDE [iot-hub-selector-twin-get-started](../../includes/iot-hub-selector-twin-get-started.md)]

Na konci tohoto kurzu budete mít dvě konzolové aplikace Node. js:

* **AddTagsAndQuery. js**– back-endové aplikace v Node. js, které přidávají značky a dotaz na vlákna zařízení.

* **TwinSimulatedDevice. js**aplikace v Node. js, která simuluje zařízení, které se připojuje ke službě IoT Hub s dříve vytvořenou identitou zařízení a oznamuje její podmínku připojení.

> [!NOTE]
> V článku sady [SDK Azure IoT](iot-hub-devguide-sdks.md) najdete informace o sadách SDK Azure IoT, které můžete použít k vytvoření zařízení i back-endové aplikace.
>

K dokončení tohoto kurzu potřebujete následující:

* Node. js verze 10.0. x nebo novější.

* Aktivní účet Azure. (Pokud účet nemáte, můžete si během několika minut vytvořit [bezplatný účet](https://azure.microsoft.com/pricing/free-trial/) .)

## <a name="create-an-iot-hub"></a>Vytvoření centra IoT

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

## <a name="get-the-iot-hub-connection-string"></a>Získání připojovacího řetězce centra IoT Hub

[!INCLUDE [iot-hub-howto-twin-shared-access-policy-text](../../includes/iot-hub-howto-twin-shared-access-policy-text.md)]

[!INCLUDE [iot-hub-include-find-custom-connection-string](../../includes/iot-hub-include-find-custom-connection-string.md)]

## <a name="create-the-service-app"></a>Vytvoření aplikace služby

V této části vytvoříte konzolovou aplikaci Node. js, která přidá metadata umístění do vlákna zařízení přidruženého k **myDeviceId**. Pak se dotazuje na vlákna, která jsou uložená ve službě IoT Hub, vybírá zařízení umístěná v USA a potom ty, které vytvářejí mobilní připojení.

1. Vytvořte novou prázdnou složku s názvem **addtagsandqueryapp**. Ve složce **addtagsandqueryapp** vytvořte nový soubor Package. JSON pomocí následujícího příkazu na příkazovém řádku. Přijměte všechny výchozí hodnoty:

    ```
    npm init
    ```

2. Na příkazovém řádku ve složce **addtagsandqueryapp** spusťte následující příkaz k instalaci balíčku **Azure-iothub** :
   
    ```
    npm install azure-iothub --save
    ```

3. Pomocí textového editoru vytvořte nový soubor **AddTagsAndQuery. js** ve složce **addtagsandqueryapp** .

4. Do souboru **AddTagsAndQuery. js** přidejte následující kód a nahraďte zástupný symbol **{připojovací řetězec pro IoT Hub}** připojovacím řetězcem IoT Hub, který jste předtím zkopírovali v části [získání připojovacího řetězce centra IoT](#get-the-iot-hub-connection-string):

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

    Objekt **registru** zveřejňuje všechny metody, které jsou potřeba pro interakci se zařízeními ze služby. Předchozí kód nejprve inicializuje objekt **registru** , potom načte **myDeviceId**pro zařízení a nakonec aktualizuje své značky o požadované informace o umístění.

    Po aktualizaci značek volá funkci **queryTwins** .

5. Přidejte následující kód na konec **AddTagsAndQuery. js** k implementaci funkce **queryTwins** :

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

    Předchozí kód spustí dva dotazy: první vybere jenom zařízení, která se nacházejí ve složce **Redmond43** , a druhá ho doplní a vybere jenom zařízení, která jsou taky připojená přes mobilní síť.

    Předchozí kód při vytváření objektu **dotazu** určuje maximální počet vrácených dokumentů. Objekt **dotazu** obsahuje vlastnost **hasMoreResults** Boolean, kterou můžete použít k vícenásobnému vyvolání metod **nextAsTwin** pro načtení všech výsledků. K dispozici **je metoda** , která je k dispozici pro výsledky, které nejsou nevlákenné zařízení, například výsledky agregačních dotazů.

6. Spusťte aplikaci pomocí:

    ```
        node AddTagsAndQuery.js
    ```

   Ve výsledcích dotazu pro všechna zařízení umístěná v **Redmond43** byste měli vidět jedno zařízení, které pro dotaz neomezuje výsledky na zařízení, která používají mobilní síť.
   
    ![Podívejte se na jedno zařízení ve výsledcích dotazu.](media/iot-hub-node-node-twin-getstarted/service1.png)

V další části vytvoříte aplikaci pro zařízení, která oznamuje informace o připojení a mění výsledek dotazu v předchozí části.

## <a name="create-the-device-app"></a>Vytvoření aplikace pro zařízení

V této části vytvoříte konzolovou aplikaci Node. js, která se připojí k vašemu rozbočovači jako **myDeviceId**, a pak aktualizuje nahlášené vlastnosti dodaného zařízení tak, aby obsahovaly informace, které jsou připojené pomocí mobilní sítě.

1. Vytvořte novou prázdnou složku s názvem **reportconnectivity**. Ve složce **reportconnectivity** vytvořte nový soubor Package. JSON pomocí následujícího příkazu na příkazovém řádku. Přijměte všechny výchozí hodnoty:
   
    ```
    npm init
    ```

2. Na příkazovém řádku ve složce **reportconnectivity** spusťte následující příkaz k instalaci balíčku **Azure-IoT-Device**a **Azure-IoT-Device-MQTT** :
   
    ```
    npm install azure-iot-device azure-iot-device-mqtt --save
    ```

3. Pomocí textového editoru vytvořte nový soubor **ReportConnectivity. js** ve složce **ReportConnectivity** .

4. Do souboru **ReportConnectivity. js** přidejte následující kód a nahraďte zástupný symbol **{připojovací řetězec zařízení}** připojovacím řetězcem zařízení, který jste zkopírovali při vytváření identity zařízení **myDeviceId** :

    ```
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

    Objekt **klienta** zveřejňuje všechny metody, které vyžadujete pro interakci se zařízeními ze zařízení. Předchozí kód, po inicializaci objektu **klienta** , načte do zařízení **myDeviceId** a aktualizuje jeho hlášenou vlastnost informacemi o připojení.

5. Spuštění aplikace pro zařízení

    ```   
        node ReportConnectivity.js
    ```

    Měla by se zobrazit zpráva `twin state reported`.

6. Teď, když zařízení oznámilo informace o připojení, by se mělo zobrazit v obou dotazech. Vraťte se zpátky do složky **addtagsandqueryapp** a spusťte dotazy znovu:

    ```   
        node AddTagsAndQuery.js
    ```

    Toto časové **myDeviceId** by se mělo objevit ve výsledcích dotazu.

    ![Zobrazit myDeviceId ve výsledcích dotazu](media/iot-hub-node-node-twin-getstarted/service2.png)

## <a name="next-steps"></a>Další postup

V tomto kurzu jste nakonfigurovali novou službu IoT Hub na webu Azure Portal a potom jste vytvořili identitu zařízení v registru identit ve službě IoT Hub. Metadata zařízení jste přidali jako značky z back-endové aplikace a zapsali jste aplikaci simulovaného zařízení, která oznamuje informace o připojení zařízení v zařízení. Zjistili jste také, jak zadat dotaz na tyto informace pomocí dotazovacího jazyka IoT Hub, jako je třeba SQL.

Pomocí následujících zdrojů se naučíte:

* pomocí kurzu [Začínáme se službou IoT Hub](quickstart-send-telemetry-node.md) odeslat telemetrii ze zařízení,

* v kurzu konfigurace zařízení pomocí [požadovaných vlastností pro konfiguraci](tutorial-device-twins.md) zařízení nakonfigurujte zařízení pomocí požadovaných vlastností.

* interaktivní řízení zařízení (například zapnutí ventilátoru z aplikace řízené uživatelem) pomocí kurzu [použití přímých metod](quickstart-control-device-node.md) .