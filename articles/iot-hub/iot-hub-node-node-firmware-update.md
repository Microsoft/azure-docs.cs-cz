---
title: Aktualizace firmwaru zařízení s Azure IoT Hub (Node) | Dokumentace Microsoftu
description: Jak zahájit aktualizaci firmwaru zařízení pomocí správy zařízení ve službě Azure IoT Hub. Implementace aplikace s Simulovaná zařízení a app service, která spustí aktualizaci firmwaru pomocí sady Azure IoT SDK pro Node.js.
author: juanjperez
manager: cberlin
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 09/07/2017
ms.author: juanpere
ms.openlocfilehash: 0cd8c019cf9a65e0e72227ba99c1995a45ed4067
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2018
ms.locfileid: "38452426"
---
# <a name="use-device-management-to-initiate-a-device-firmware-update-nodenode"></a>Použití správy zařízení za účelem Zahájit aktualizaci firmwaru zařízení (Node/Node)
[!INCLUDE [iot-hub-selector-firmware-update](../../includes/iot-hub-selector-firmware-update.md)]

V [Začínáme se správou zařízení] [ lnk-dm-getstarted] výukový program, jste viděli, jak používat [dvojče zařízení] [ lnk-devtwin] a [přímé metody ] [ lnk-c2dmethod] primitiv vzdálené restartování zařízení. Tento kurz používá stejný základní služby IoT Hub a obsahuje pokyny a ukazuje, jak provádět aktualizace firmwaru simulované začátku do konce.  Tento model se používá k provedení aktualizace firmwaru Intel Edison ukázkou zařízení.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

V tomto kurzu získáte informace o následujících postupech:

* Vytvořte konzolovou aplikaci Node.js, která volá metodu firmwareUpdate s přímým přístupem v aplikaci simulovaného zařízení prostřednictvím služby IoT hub.
* Vytvoření aplikace simulovaného zařízení, která implementuje **firmwareUpdate** přímá metoda. Tato metoda zahájí vícefázový proces, který čeká na stažením image firmwaru, stáhne image firmwaru a nakonec použije image firmwaru. Během každé fáze aktualizace zařízení využívá ohlášené vlastnosti k podávat zprávy o pokroku.

Na konci tohoto kurzu budete mít dvě konzolové aplikace Node.js:

**dmpatterns_fwupdate_service.js**, která volá metodu s přímým přístupem v aplikaci simulovaného zařízení, zobrazí odpovědi a pravidelně (každých 500ms) zobrazí aktualizovaný ohlášené vlastnosti.

**dmpatterns_fwupdate_device.js**, propojuje službu IoT hub s identitou zařízení vytvořenou dříve, obdrží firmwareUpdate přímé metody, prochází přes více stavu procesu pro simulaci, včetně aktualizace firmwaru: čeká se na obrázku stáhnete, stahuje nové image a nakonec použitím bitové kopie.

Pro absolvování tohoto kurzu potřebujete:

* Node.js verze 4.0.x nebo novější, <br/>  [Příprava vývojového prostředí] [ lnk-dev-setup] popisuje postup instalace Node.js pro účely tohoto kurzu ve Windows nebo Linuxu.
* Aktivní účet Azure. (Pokud účet nemáte, můžete si během několika minut vytvořit [bezplatný účet][lnk-free-trial].)

Postupujte podle [Začínáme se správou zařízení](iot-hub-node-node-device-management-get-started.md) článek k vytvoření služby IoT hub a získejte připojovací řetězec služby IoT Hub.

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

## <a name="trigger-a-remote-firmware-update-on-the-device-using-a-direct-method"></a>Aktivace firmwaru vzdálené aktualizace na zařízení s využitím přímé metody
V této části vytvoříte konzolovou aplikaci Node.js, který iniciuje vzdálené firmwaru v zařízení. Aplikace používá přímé metody k zahájení aktualizace a používá dotazů na dvojčata zařízení a pravidelně získat stav aktualizace firmwaru aktivní.

1. Vytvořte prázdnou složku s názvem **triggerfwupdateondevice**.  V **triggerfwupdateondevice** složce vytvořte soubor package.json pomocí následujícího příkazu na příkazovém řádku.  Přijměte všechny výchozí hodnoty:
   
    ```
    npm init
    ```
2. Na příkazovém řádku v **triggerfwupdateondevice** složky, spusťte následující příkaz k instalaci **azure-iot-hub** balíčku:
   
    ```
    npm install azure-iothub --save
    ```
3. Pomocí textového editoru, vytvořte **dmpatterns_getstarted_service.js** soubor **triggerfwupdateondevice** složky.
4. Přidejte následující příkazy na začátku "vyžadovat" **dmpatterns_getstarted_service.js** souboru:
   
    ```
    'use strict';
   
    var Registry = require('azure-iothub').Registry;
    var Client = require('azure-iothub').Client;
    ```
5. Přidejte následující deklarace proměnných a nahraďte zástupné hodnoty:
   
    ```
    var connectionString = '{device_connectionstring}';
    var registry = Registry.fromConnectionString(connectionString);
    var client = Client.fromConnectionString(connectionString);
    var deviceToUpdate = 'myDeviceId';
    ```
6. Přidejte následující funkci k vyhledání a zobrazení hodnoty firmwareUpdate hlášené vlastnost.
   
    ```
    var queryTwinFWUpdateReported = function() {
        registry.getTwin(deviceToUpdate, function(err, twin){
            if (err) {
              console.error('Could not query twins: ' + err.constructor.name + ': ' + err.message);
            } else {
              console.log((JSON.stringify(twin.properties.reported.iothubDM.firmwareUpdate)) + "\n");
            }
        });
    };
    ```
7. Přidejte následující funkci k vyvolání metody firmwareUpdate restartovat cílové zařízení:
   
    ```
    var startFirmwareUpdateDevice = function() {
      var params = {
          fwPackageUri: 'https://secureurl'
      };
   
      var methodName = "firmwareUpdate";
      var payloadData =  JSON.stringify(params);
   
      var methodParams = {
        methodName: methodName,
        payload: payloadData,
        timeoutInSeconds: 30
      };
   
      client.invokeDeviceMethod(deviceToUpdate, methodParams, function(err, result) {
        if (err) {
          console.error('Could not start the firmware update on the device: ' + err.message)
        } 
      });
    };
    ```
8. Nakonec přidejte následující funkci k kód pro spuštění sekvence aktualizace firmwaru a začne pravidelně zobrazovat ohlášených vlastností:
   
    ```
    startFirmwareUpdateDevice();
    setInterval(queryTwinFWUpdateReported, 500);
    ```
9. Uložte a zavřete **dmpatterns_fwupdate_service.js** souboru.

[!INCLUDE [iot-hub-device-firmware-update](../../includes/iot-hub-device-firmware-update.md)]

## <a name="run-the-apps"></a>Spouštění aplikací
Nyní jste připraveni aplikaci spustit.

1. Na příkazovém řádku v **manageddevice** složky, spuštěním následujícího příkazu zahajte naslouchání pro přímé metody restartování.
   
    ```
    node dmpatterns_fwupdate_device.js
    ```
2. Na příkazovém řádku v **triggerfwupdateondevice** složky, spusťte následující příkaz spustí vzdálené restartování a dotazů pro dvojče zařízení najít poslední čas restartování.
   
    ```
    node dmpatterns_fwupdate_service.js
    ```
3. Zobrazí se zařízení odpověď na přímé metody v konzole.

## <a name="next-steps"></a>Další postup
V tomto kurzu se používá přímé metody k aktivaci vzdáleného firmwaru v zařízení a umožňuje sledovat průběh aktualizace firmwaru ohlášené vlastnosti.

Zjistěte, jak rozšířit vaše IoT řešení a plán metoda volá do různých zařízení, najdete v článku [plánování a vysílání úloh] [ lnk-tutorial-jobs] kurzu.

[lnk-devtwin]: iot-hub-devguide-device-twins.md
[lnk-c2dmethod]: iot-hub-devguide-direct-methods.md
[lnk-dm-getstarted]: iot-hub-node-node-device-management-get-started.md
[lnk-tutorial-jobs]: iot-hub-node-node-schedule-jobs.md

[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdk-node/tree/master/doc/node-devbox-setup.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
