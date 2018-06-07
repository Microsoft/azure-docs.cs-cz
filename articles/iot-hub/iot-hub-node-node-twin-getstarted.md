---
title: Začínáme s Azure IoT Hub dvojčata zařízení (uzel) | Microsoft Docs
description: Jak používat dvojčata zařízení Azure IoT Hub přidat značky a pak použijte dotaz služby IoT Hub. SDK služby Azure IoT pro Node.js použijete k implementaci aplikaci simulovaného zařízení a aplikace služby, které přidá značky a spustí dotaz IoT Hub.
author: fsautomata
manager: ''
ms.service: iot-hub
services: iot-hub
ms.devlang: nodejs
ms.topic: conceptual
ms.date: 08/25/2017
ms.author: elioda
ms.openlocfilehash: dbb28d7df104887a4056489e9f693ce28ec406df
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34635096"
---
# <a name="get-started-with-device-twins-node"></a>Začínáme s dvojčata zařízení (uzel)
[!INCLUDE [iot-hub-selector-twin-get-started](../../includes/iot-hub-selector-twin-get-started.md)]

Na konci tohoto kurzu budete mít dvě aplikace konzoly Node.js:

* **AddTagsAndQuery.js**, aplikace back-end Node.js, které přidá značky a dotazuje dvojčata zařízení.
* **TwinSimulatedDevice.js**, aplikace Node.js, která simuluje zařízení, která se připojuje ke službě IoT hub s dříve vytvořenou identitou zařízení a sestav stavu připojení.

> [!NOTE]
> Článek [SDK služby Azure IoT] [ lnk-hub-sdks] poskytuje informace o SDK služby Azure IoT, můžete použít k tvorbě aplikací, zařízení a back-end.
> 
> 

K dokončení tohoto kurzu budete potřebovat následující:

* Node.js verze 4.0.x nebo novější.
* Aktivní účet Azure. (Pokud účet nemáte, můžete si během několika minut vytvořit [bezplatný účet][lnk-free-trial].)

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

## <a name="create-the-service-app"></a>Vytvořit aplikaci aplikační služby
V této části vytvoříte konzolovou aplikaci softwaru Node.js, která přidává do dvojče zařízení přidružená metadata umístění **myDeviceId**. Následně se dotazuje dvojčata zařízení, které jsou uložené ve službě IoT hub, výběrem zařízení nachází v USA a ty, které podávají zprávy mobilní připojení.

1. Vytvořit novou prázdnou složku s názvem **addtagsandqueryapp**. V **addtagsandqueryapp** složky, vytvořte nový soubor package.json pomocí následujícího příkazu na příkazovém řádku. Přijměte všechny výchozí hodnoty:
   
    ```
    npm init
    ```
2. Na příkazovém řádku v **addtagsandqueryapp** složky, spusťte následující příkaz k instalaci **azure-iothub** balíčku:
   
    ```
    npm install azure-iothub --save
    ```
3. Pomocí textového editoru, vytvořte novou **AddTagsAndQuery.js** v soubor **addtagsandqueryapp** složky.
4. Přidejte následující kód, který **AddTagsAndQuery.js** souboru a nahraďte **{iot hub, připojovací řetězec}** zástupný symbol jste zkopírovali, když vytvoříte Centrum IoT Hub připojovací řetězec:
   
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
   
    **Registru** objekt poskytuje všechny metody požadované pro interakci s dvojčata zařízení ze služby. Předchozí kód nejprve inicializuje **registru** objekt a potom načte dvojče zařízení pro **myDeviceId**a nakonec jeho značky aktualizuje informace o požadované umístění.
   
    Po aktualizaci značek zavolá **queryTwins** funkce.
5. Přidejte následující kód na konci **AddTagsAndQuery.js** implementovat **queryTwins** funkce:
   
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
   
    Předchozí kód provede dva dotazy: první vybere pouze dvojčata zařízení nachází v zařízení **Redmond43** závodu a druhý zpřesnění dotazu a vyberte pouze zařízení, která jsou také připojené přes mobilní síť.
   
    Předchozí kód, když vytváří **dotazu** objektu, určuje maximální počet vrácených dokumentů. **Dotazu** objekt obsahuje **hasMoreResults** vlastnost typu boolean, který můžete použít k vyvolání **nextAsTwin** metody několikrát načíst všechny výsledky. Volána metoda **Další** je k dispozici pro výsledky, které není dvojčata zařízení, například výsledky dotazů agregace.
6. Spusťte aplikaci klávesou:
   
        node AddTagsAndQuery.js
   
    Měli byste vidět jedno zařízení ve výsledcích pro dotaz s dotazem pro všechna zařízení umístěné v **Redmond43** a jeden pro dotaz, který omezuje výsledky na zařízení, která používají mobilní síti.
   
    ![][1]

V další části můžete vytvořit aplikaci zařízení, která hlásí informace o připojení a změní výsledek dotazu v předchozí části.

## <a name="create-the-device-app"></a>Vytvoření aplikace pro zařízení
V této části vytvoříte konzolovou aplikaci softwaru Node.js, která se připojuje k vaší hub jako **myDeviceId**a poté aktualizace jeho dvojče zařízení je hlášené vlastností obsahují informace, že je připojený pomocí mobilní síti.


1. Vytvořit novou prázdnou složku s názvem **reportconnectivity**. V **reportconnectivity** složky, vytvořte nový soubor package.json pomocí následujícího příkazu na příkazovém řádku. Přijměte všechny výchozí hodnoty:
   
    ```
    npm init
    ```
2. Na příkazovém řádku v **reportconnectivity** složky, spusťte následující příkaz k instalaci **azure-iot-device**, a **azure-iot zařízení mqtt** balíčku:
   
    ```
    npm install azure-iot-device azure-iot-device-mqtt --save
    ```
3. Pomocí textového editoru, vytvořte novou **ReportConnectivity.js** v soubor **reportconnectivity** složky.
4. Přidejte následující kód, který **ReportConnectivity.js** souboru a nahraďte **{zařízení připojovací řetězec}** zástupného symbolu připojovacím řetězcem zařízení jste zkopírovali, když jste vytvořili **myDeviceId** identitu zařízení:
   
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
   
    **Klienta** objekt poskytuje všechny metody vyžadovat interakci s dvojčata zařízení ze zařízení. Předchozí kód, jakmile ji inicializuje **klienta** objektu, načte dvojče zařízení pro **myDeviceId** a aktualizuje jeho hlášené vlastnost s informacemi o připojení.
5. Spuštění aplikace zařízení
   
        node ReportConnectivity.js
   
    Měli byste vidět zprávu `twin state reported`.
6. Teď, když je zařízení hlášené jeho informace o připojení k se má zobrazit v obou dotazy. Přejděte zpět **addtagsandqueryapp** složky a znovu spusťte dotazy:
   
        node AddTagsAndQuery.js
   
    Tentokrát **myDeviceId** by se měla objevit v obou výsledky dotazu.
   
    ![][3]

## <a name="next-steps"></a>Další postup
V tomto kurzu jste nakonfigurovali novou službu IoT Hub na webu Azure Portal a potom jste vytvořili identitu zařízení v registru identit ve službě IoT Hub. Přidat zařízení metadat jako značky z back-end aplikace a zapsal aplikace simulovaného zařízení do sestavy informace o připojení k zařízení v dvojče zařízení. Také jste zjistili, jak dotazovat tyto informace pomocí dotazu jazyka SQL jako IoT Hub.

Použijte v následujících zdrojích informací další postup:

* odesílat telemetrická data ze zařízení pomocí [Začínáme se službou IoT Hub] [ lnk-iothub-getstarted] kurzu
* Konfigurace zařízení pomocí dvojče zařízení požadované vlastnosti s [použití požadovaného vlastnosti pro konfiguraci zařízení] [ lnk-twin-how-to-configure] kurzu
* kontroly nad zařízeními interaktivně (například zapnutí ventilátor z aplikace řízené uživatele), s [použít přímé metody] [ lnk-methods-tutorial] kurzu.

<!-- images -->
[1]: media/iot-hub-node-node-twin-getstarted/service1.png
[3]: media/iot-hub-node-node-twin-getstarted/service2.png

<!-- links -->
[lnk-hub-sdks]: iot-hub-devguide-sdks.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[lnk-d2c]: iot-hub-devguide-messaging.md#device-to-cloud-messages
[lnk-methods]: iot-hub-devguide-direct-methods.md
[lnk-twins]: iot-hub-devguide-device-twins.md
[lnk-query]: iot-hub-devguide-query-language.md
[lnk-identity]: iot-hub-devguide-identity-registry.md

[lnk-iothub-getstarted]: iot-hub-node-node-getstarted.md
[lnk-device-management]: iot-hub-node-node-device-management-get-started.md
[lnk-iot-edge]: iot-hub-linux-iot-edge-get-started.md
[lnk-connect-device]: https://azure.microsoft.com/develop/iot/

[lnk-twin-how-to-configure]: iot-hub-node-node-twin-how-to-configure.md
[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdk-node/tree/master/doc/node-devbox-setup.md

[lnk-methods-tutorial]: iot-hub-node-node-direct-methods.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md
