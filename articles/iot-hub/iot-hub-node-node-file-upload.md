---
title: Odeslání souborů ze zařízení do služby Azure IoT Hub s uzlem | Microsoft Docs
description: Postup nahrání souborů ze zařízení do cloudu pomocí zařízení Azure IoT SDK pro Node.js. Odeslané soubory jsou uloženy v kontejneru objektů blob úložiště Azure.
services: iot-hub
documentationcenter: nodejs
author: dominicbetts
manager: timlt
editor: ''
ms.assetid: 4759d229-f856-4526-abda-414f8b00a56d
ms.service: iot-hub
ms.devlang: node
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/28/2017
ms.author: v-masebo;dobett
ms.openlocfilehash: 7733aa11dd76eac564c115b10c57a84da5666d38
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/05/2018
---
# <a name="upload-files-from-your-device-to-the-cloud-with-iot-hub"></a>Odeslání souborů ze zařízení do cloudu s centrem IoT

[!INCLUDE [iot-hub-file-upload-language-selector](../../includes/iot-hub-file-upload-language-selector.md)]

V tomto kurzu vychází kód [odesílání zpráv typu Cloud-zařízení s centrem IoT](iot-hub-node-node-c2d.md) kurzu ukazují, jak používat [souboru nahrávání služby IoT Hub](iot-hub-devguide-file-upload.md) nahrát soubor do [objektů blob v Azure úložiště](../storage/index.yml). Tento kurz ukazuje, jak na:

- Bezpečně zadejte zařízení s Azure blob identifikátor URI pro nahrání souboru.
- Oznámení o odeslání souboru IoT Hub použijte k aktivaci zpracování souboru ve vaší aplikaci back-end.

[Začínáme se službou IoT Hub](iot-hub-node-node-getstarted.md) kurz představuje základní funkce zasílání zpráv typu zařízení cloud IoT Hub. Ale v některých scénářích nelze mapovat snadno data, která vaše zařízení odesílají do poměrně malý zprávy typu zařízení cloud, které IoT Hub přijímá. Příklad:

* Velkých souborů, které obsahují Image
* Videa
* Data vibrace odebírána data v vysoká frekvence
* Určitou formu předběžně zpracované data.

Tyto soubory jsou obvykle dávkové zpracování v cloudu pomocí nástrojů, jako [Azure Data Factory](../data-factory/introduction.md) nebo [Hadoop](../hdinsight/index.md) zásobníku. Když potřebujete hornatých soubory ze zařízení, když můžete nadále používat zabezpečení a spolehlivost služby IoT Hub.

Na konci tohoto kurzu můžete spustit dvě aplikace konzoly Node.js:

* **SimulatedDevice.js**, které se uloží soubor do úložiště pomocí identifikátoru URI SAS poskytované služby IoT hub.
* **ReadFileUploadNotification.js**, který obdrží oznámení o odeslání souboru ze služby IoT hub.

> [!NOTE]
> IoT Hub podporuje mnoho zařízení platformy a jazyky (včetně C, .NET, Javascript, Python a Java) prostřednictvím SDK pro zařízení Azure IoT. Odkazovat [Azure střediska pro vývojáře IoT] podrobné pokyny o tom, jak připojit zařízení ke službě Azure IoT Hub.

Pro absolvování tohoto kurzu potřebujete:

* Node.js verze 4.0.x nebo novější.
* Aktivní účet Azure. (Pokud nemáte účet, můžete vytvořit [bezplatný účet](http://azure.microsoft.com/pricing/free-trial/) si během několika minut.)

[!INCLUDE [iot-hub-associate-storage](../../includes/iot-hub-associate-storage.md)]

## <a name="upload-a-file-from-a-device-app"></a>Nahrát soubor z aplikace na zařízení

V této části můžete vytvořit aplikaci zařízení pro nahrání souboru do služby IoT hub.

1. Vytvořte prázdnou složku s názvem ```simulateddevice```.  Ve složce ```simulateddevice``` vytvořte soubor package.json pomocí následujícího příkazu na příkazovém řádku.  Přijměte všechny výchozí hodnoty:

    ```cmd/sh
    npm init
    ```

1. Na příkazovém řádku ve složce ```simulateddevice``` spusťte následující příkaz k instalaci balíčku sady SDK pro zařízení **azure-iot-device** a balíčku **azure-iot-device-mqtt**:

    ```cmd/sh
    npm install azure-iot-device azure-iot-device-mqtt --save
    ```

1. Pomocí textového editoru vytvořte ve složce ```simulateddevice``` soubor **SimulatedDevice.js**.

1. Na začátek souboru **SimulatedDevice.js** přidejte následující příkazy ```require```:

    ```nodejs
    'use strict';
    
    var fs = require('fs');
    var mqtt = require('azure-iot-device-mqtt').Mqtt;
    var clientFromConnectionString = require('azure-iot-device-mqtt').clientFromConnectionString;
    ```

1. Přidejte proměnnou ```deviceconnectionstring``` a použijte ji k vytvoření instance **klienta**.  Nahraďte ```{deviceconnectionstring}``` s názvem zařízení, kterou jste vytvořili v _vytvoření služby IoT Hub_ části:

    ```nodejs
    var connectionString = '{deviceconnectionstring}';
    var filename = 'myimage.png';
    ```

    > [!NOTE]
    > Z důvodu zjednodušení připojovací řetězec je součástí kód: Toto není doporučený postup a v závislosti na případ použití a architektura možná budete chtít zvážit bezpečnější způsoby ukládání tento tajný klíč.

1. Přidejte následující kód k připojení klienta:

    ```nodejs
    var client = clientFromConnectionString(connectionString);
    console.log('Client connected');
    ```

1. Vytvořte zpětné volání a použít **uploadToBlob** funkce pro nahrání souboru.

    ```nodejs
    fs.stat(filename, function (err, stats) {
        const rr = fs.createReadStream(filename);
    
        client.uploadToBlob(filename, rr, stats.size, function (err) {
            if (err) {
                console.error('Error uploading file: ' + err.toString());
            } else {
                console.log('File uploaded');
            }
        });
    });
    ```

1. Soubor **SimulatedDevice.js** uložte a zavřete.

1. Zkopírujte soubor obrázku na `simulateddevice` složku a přejmenujte ji `myimage.png`.

## <a name="receive-a-file-upload-notification"></a>Nechte si zaslat oznámení nahrávání souborů

V této části vytvoříte konzolovou aplikaci softwaru Node.js, která přijímá zprávy oznámení nahrávání souborů ze služby IoT Hub.

Můžete použít **iothubowner** připojovací řetězec ze služby IoT Hub pro dokončení této části. Zjistíte, připojovací řetězec [portál Azure](https://portal.azure.com/) na **zásady sdíleného přístupu** okno.

1. Vytvořte prázdnou složku s názvem ```fileuploadnotification```.  Ve složce ```fileuploadnotification``` vytvořte soubor package.json pomocí následujícího příkazu na příkazovém řádku.  Přijměte všechny výchozí hodnoty:

    ```cmd/sh
    npm init
    ```

1. Na příkazovém řádku v ```fileuploadnotification``` složky, spusťte následující příkaz k instalaci **azure-iothub** balíčku SDK:

    ```cmd/sh
    npm install azure-iothub --save
    ```

1. Pomocí textového editoru, vytvořte **FileUploadNotification.js** v soubor ```fileuploadnotification``` složky.

1. Přidejte následující ```require``` příkazy na začátku **FileUploadNotification.js** souboru:

    ```nodejs
    'use strict';
    
    var Client = require('azure-iothub').Client;
    ```

1. Přidejte proměnnou ```iothubconnectionstring``` a použijte ji k vytvoření instance **klienta**.  Nahraďte ```{iothubconnectionstring}``` s řetězcem připojení ke službě IoT hub, kterou jste vytvořili v _vytvoření služby IoT Hub_ části:

    ```nodejs
    var connectionString = '{iothubconnectionstring}';
    ```

    > [!NOTE]
    > Z důvodu zjednodušení připojovací řetězec je součástí kód: Toto není doporučený postup a v závislosti na případ použití a architektura možná budete chtít zvážit bezpečnější způsoby ukládání tento tajný klíč.

1. Přidejte následující kód k připojení klienta:

    ```nodejs
    var serviceClient = Client.fromConnectionString(connectionString);
    ```

1. Otevřít klienta a použít **getFileNotificationReceiver** funkce pro příjem aktualizací stavu.

    ```nodejs
    serviceClient.open(function (err) {
      if (err) {
        console.error('Could not connect: ' + err.message);
      } else {
        console.log('Service client connected');
        serviceClient.getFileNotificationReceiver(function receiveFileUploadNotification(err, receiver){
          if (err) {
            console.error('error getting the file notification receiver: ' + err.toString());
          } else {
            receiver.on('message', function (msg) {
              console.log('File upload from device:')
              console.log(msg.getData().toString('utf-8'));
            });
          }
        });
      }
    });
    ```

1. Uložte a zavřete **FileUploadNotification.js** souboru.

## <a name="run-the-applications"></a>Spuštění aplikací

Nyní můžete spustit aplikace.

Na příkazovém řádku v `fileuploadnotification` složky, spusťte následující příkaz:

```cmd/sh
node FileUploadNotification.js
```

Na příkazovém řádku v `simulateddevice` složky, spusťte následující příkaz:

```cmd/sh
node SimulatedDevice.js
```

Následující snímek obrazovky ukazuje výstup **SimulatedDevice** aplikace:

![Výstup z aplikace simulated-device](./media/iot-hub-node-node-file-upload/simulated-device.png)

Následující snímek obrazovky ukazuje výstup **FileUploadNotification** aplikace:

![Výstup z aplikace pro čtení souboru odesílání oznámení](./media/iot-hub-node-node-file-upload/read-file-upload-notification.png)

Na portálu můžete použít k zobrazení nahrávaný soubor v kontejneru úložiště, které jste nakonfigurovali:

![Nahrávaný soubor](./media/iot-hub-node-node-file-upload/uploaded-file.png)

## <a name="next-steps"></a>Další postup

V tomto kurzu jste zjistili, jak používat funkce nahrávání souboru služby IoT Hub pro zjednodušení nahrávání souborů ze zařízení. Můžete dál prozkoumat funkcí služby IoT hub a scénáře v následujících článcích:

* [Vytvoření služby IoT hub prostřednictvím kódu programu][lnk-create-hub]
* [Úvod do jazyka C SDK][lnk-c-sdk]
* [Sady SDK služby Azure IoT][lnk-sdks]

<!-- Links -->
[Azure střediska pro vývojáře IoT]: http://azure.microsoft.com/develop/iot

[lnk-create-hub]: iot-hub-rm-template-powershell.md
[lnk-c-sdk]: iot-hub-device-sdk-c-intro.md
[lnk-sdks]: iot-hub-devguide-sdks.md
