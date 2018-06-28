---
title: Aktualizace firmwaru zařízení s Azure IoT Hub (uzel) | Microsoft Docs
description: Jak používat správu zařízení v Azure IoT Hub zahájíte aktualizaci firmwaru zařízení. Použijete k implementaci aplikace simulovaného zařízení a aplikaci služby, která spustí aktualizaci firmwaru SDK služby Azure IoT pro Node.js.
author: juanjperez
manager: cberlin
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 09/07/2017
ms.author: juanpere
ms.openlocfilehash: 0cd8c019cf9a65e0e72227ba99c1995a45ed4067
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/27/2018
ms.locfileid: "34634960"
---
# <a name="use-device-management-to-initiate-a-device-firmware-update-nodenode"></a>Použití správy zařízení za účelem zahájení aktualizaci firmwaru zařízení (uzel nebo uzel)
[!INCLUDE [iot-hub-selector-firmware-update](../../includes/iot-hub-selector-firmware-update.md)]

V [Začínáme se správou zařízení] [ lnk-dm-getstarted] kurz, jste viděli, jak používat [dvojče zařízení] [ lnk-devtwin] a [přímé metody ] [ lnk-c2dmethod] primitiv vzdáleně restartování zařízení. Tento kurz používá stejné primitiv IoT Hub a poskytuje pokyny a ukazuje, jak provést aktualizaci firmwaru simulované začátku do konce.  Tento vzor slouží k implementaci aktualizace firmwaru pro vzorovou Intel Edison zařízení.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

V tomto kurzu získáte informace o následujících postupech:

* Vytvořte konzolovou aplikaci softwaru Node.js, která volá metodu firmwareUpdate přímé v aplikaci simulovaného zařízení prostřednictvím služby IoT hub.
* Vytvoření aplikace simulovaného zařízení, která implementuje **firmwareUpdate** přímá metoda. Tato metoda inicializuje více fáze procesu, který čeká na stažení bitové kopie firmwaru, stáhne bitovou kopii firmware a nakonec platí bitovou kopii firmwaru. Během aktualizace v každé fázi používá zařízení hlášené vlastnosti hlásit průběh.

Na konci tohoto kurzu máte dvě aplikace konzoly Node.js:

**dmpatterns_fwupdate_service.js**, která volá metodu přímé v aplikaci simulovaného zařízení, zobrazí odpověď a pravidelně (každých 500ms) zobrazí aktualizovaná hlášené vlastnosti.

**dmpatterns_fwupdate_device.js**, který se připojí ke službě IoT hub s identitou zařízení vytvořenou dříve, dostane přímá metoda firmwareUpdate, spustí prostřednictvím více stavu procesu k simulaci, včetně aktualizace firmwaru: čekání bitové kopie stažení, stahování novou bitovou kopii a nakonec použitím bitové kopie.

Pro absolvování tohoto kurzu potřebujete:

* Verze Node.js 4.0.x nebo novější, <br/>  [Příprava vývojového prostředí] [ lnk-dev-setup] popisuje postup instalace Node.js pro tento návod v systému Windows nebo Linux.
* Aktivní účet Azure. (Pokud účet nemáte, můžete si během několika minut vytvořit [bezplatný účet][lnk-free-trial].)

Postupujte podle [Začínáme se správou zařízení](iot-hub-node-node-device-management-get-started.md) článek k vytvoření služby IoT hub a získat připojovací řetězec služby IoT Hub.

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

## <a name="trigger-a-remote-firmware-update-on-the-device-using-a-direct-method"></a>Spustit aktualizaci vzdálené firmwaru v zařízení s přímá metoda
V této části vytvoříte konzolovou aplikaci softwaru Node.js, který iniciuje aktualizace vzdálené firmwaru v zařízení. Přímá metoda používá k zahájení aktualizace a aplikace používá zařízení twin dotazy a pravidelně získat stav aktualizace active firmware.

1. Vytvořit prázdnou složku s názvem **triggerfwupdateondevice**.  V **triggerfwupdateondevice** složky, vytvořte soubor package.json pomocí následujícího příkazu na příkazovém řádku.  Přijměte všechny výchozí hodnoty:
   
    ```
    npm init
    ```
2. Na příkazovém řádku v **triggerfwupdateondevice** složky, spusťte následující příkaz k instalaci **azure-iot-hub** balíčku:
   
    ```
    npm install azure-iothub --save
    ```
3. Pomocí textového editoru, vytvořte **dmpatterns_getstarted_service.js** v soubor **triggerfwupdateondevice** složky.
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
6. Přidejte následující funkci k vyhledání a zobrazení hodnotu firmwareUpdate jsou uvedeny vlastnosti.
   
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
7. Přidejte následující funkci k vyvolání metody firmwareUpdate restartování cílového zařízení:
   
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
8. Nakonec přidejte následující funkci k kód pro spuštění pořadí aktualizací firmwaru a spusťte pravidelně zobrazuje hlášené vlastnosti:
   
    ```
    startFirmwareUpdateDevice();
    setInterval(queryTwinFWUpdateReported, 500);
    ```
9. Uložte a zavřete **dmpatterns_fwupdate_service.js** souboru.

[!INCLUDE [iot-hub-device-firmware-update](../../includes/iot-hub-device-firmware-update.md)]

## <a name="run-the-apps"></a>Spouštění aplikací
Nyní jste připraveni aplikaci spustit.

1. Na příkazovém řádku v **manageddevice** složky, spusťte následující příkaz, aby začal přijímat přímá metoda restartování.
   
    ```
    node dmpatterns_fwupdate_device.js
    ```
2. Na příkazovém řádku v **triggerfwupdateondevice** složky, spusťte následující příkaz k aktivační události restartovat vzdálený počítač a dotaz pro dvojče zařízení najít poslední čas restartování počítače.
   
    ```
    node dmpatterns_fwupdate_service.js
    ```
3. Zobrazí zařízení odpověď na přímá metoda v konzole.

## <a name="next-steps"></a>Další postup
V tomto kurzu přímá metoda používá k aktivaci aktualizace vzdálené firmwaru v zařízení a umožňuje sledovat průběh aktualizace firmwaru hlášené vlastnosti.

Zjistěte, jak rozšířit vaše IoT řešení a plán metoda volá na několika zařízeních, najdete v článku [plán a všesměrového vysílání úlohy] [ lnk-tutorial-jobs] kurzu.

[lnk-devtwin]: iot-hub-devguide-device-twins.md
[lnk-c2dmethod]: iot-hub-devguide-direct-methods.md
[lnk-dm-getstarted]: iot-hub-node-node-device-management-get-started.md
[lnk-tutorial-jobs]: iot-hub-node-node-schedule-jobs.md

[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdk-node/tree/master/doc/node-devbox-setup.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
