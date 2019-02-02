---
title: Nahrání souborů ze zařízení do služby Azure IoT Hub s uzlem | Dokumentace Microsoftu
description: Postup nahrání souborů ze zařízení do cloudu pomocí zařízení Azure IoT SDK pro Node.js. Nahrané soubory se ukládají v kontejneru objektů blob v Azure storage.
author: dominicbetts
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.devlang: nodejs
ms.topic: conceptual
ms.date: 06/28/2017
ms.author: dobett
ms.openlocfilehash: b3afbeb5a3fa2cda6ec5eaabe368163a370352d1
ms.sourcegitcommit: ba035bfe9fab85dd1e6134a98af1ad7cf6891033
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/01/2019
ms.locfileid: "55568188"
---
# <a name="upload-files-from-your-device-to-the-cloud-with-iot-hub"></a>Nahrání souborů ze zařízení do cloudu pomocí služby IoT Hub

[!INCLUDE [iot-hub-file-upload-language-selector](../../includes/iot-hub-file-upload-language-selector.md)]

V tomto kurzu vychází z kódu v [odesílat zprávy typu Cloud-zařízení pomocí služby IoT Hub](iot-hub-node-node-c2d.md) kurzu se dozvíte, jak používat [soubor nahrát možnosti služby IoT Hub](iot-hub-devguide-file-upload.md) nahrát soubor do [objektů blob v Azure úložiště](../storage/index.yml). V tomto kurzu získáte informace o následujících postupech:

- Zabezpečeně dodávají zařízení s Azure blob identifikátorů URI pro nahrání souboru.
- Oznámení o nahrávání souborů služby IoT Hub použijte k aktivaci zpracování souboru v back-endu aplikace.

[Začínáme se službou IoT Hub](quickstart-send-telemetry-node.md) kurz ukazuje základní funkce zasílání zpráv typu zařízení cloud ze služby IoT Hub. Nicméně v některých scénářích nelze mapovat snadno data, která vaše zařízení odesílají do poměrně málo početnému zpráv typu zařízení cloud, které služby IoT Hub přijímá. Příklad:

* Velké soubory, které obsahují obrázky
* Videa
* Data pronikavost odebírána data v vysoká frekvence
* Určitou formu předzpracovaná data.

Tyto soubory jsou obvykle dávkově zpracovány v cloudu pomocí nástrojů, jako [Azure Data Factory](../data-factory/introduction.md) nebo [Hadoop](../hdinsight/index.yml) zásobníku. Když budete potřebovat hornatých souborů ze zařízení, můžete stále použít zabezpečení a spolehlivost služby IoT Hub.

Na konci tohoto kurzu spustíte dvě konzolové aplikace Node.js:

* **SimulatedDevice.js**, který nahraje soubor do úložiště pomocí SAS URI poskytované služby IoT hub.
* **ReadFileUploadNotification.js**, který obdrží oznámení o nahrávání souborů ze služby IoT hub.

> [!NOTE]
> IoT Hub podporuje mnoho platforem zařízení a jazyků (včetně C, .NET, Javascript, Python a Java) prostřednictvím sady SDK pro zařízení Azure IoT. Odkazovat [centrum pro vývojáře Azure IoT] podrobné pokyny o tom, jak připojit zařízení ke službě Azure IoT Hub.

Pro absolvování tohoto kurzu potřebujete:

* Node.js verze 4.0.x nebo novější.
* Aktivní účet Azure. (Pokud účet nemáte, můžete vytvořit [bezplatný účet](https://azure.microsoft.com/pricing/free-trial/) během několika minut.)

[!INCLUDE [iot-hub-associate-storage](../../includes/iot-hub-associate-storage.md)]

## <a name="upload-a-file-from-a-device-app"></a>Nahrajte soubor z aplikace pro zařízení

V této části vytvoříte aplikaci pro nahrání souboru do služby IoT hub zařízení.

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

    ```javascript
    'use strict';
    
    var fs = require('fs');
    var mqtt = require('azure-iot-device-mqtt').Mqtt;
    var clientFromConnectionString = require('azure-iot-device-mqtt').clientFromConnectionString;
    ```

1. Přidejte proměnnou ```deviceconnectionstring``` a použijte ji k vytvoření instance **klienta**.  Nahraďte ```{deviceconnectionstring}``` s názvem zařízení, kterou jste vytvořili v _vytvoření služby IoT Hub_ části:

    ```javascript
    var connectionString = '{deviceconnectionstring}';
    var filename = 'myimage.png';
    ```

    > [!NOTE]
    > Z důvodu zjednodušení připojovací řetězec je zahrnuta v kódu: Toto není doporučený postup a v závislosti na případu použití a architektury můžete chtít zvážit bezpečnější způsoby ukládání tento tajný kód.

1. Přidejte následující kód k připojení klienta:

    ```javascript
    var client = clientFromConnectionString(connectionString);
    console.log('Client connected');
    ```

1. Vytvořte zpětné volání a použijte **uploadToBlob** funkce pro nahrání souboru.

    ```javascript
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

1. Kopírování souboru obrázku, který `simulateddevice` složku a přejmenujte jej `myimage.png`.

## <a name="receive-a-file-upload-notification"></a>Přijímat oznámení o nahrání souborů

V této části vytvoříte konzolovou aplikaci Node.js, která bude přijímat zprávy oznámení nahrávání souborů ze služby IoT Hub.

Můžete použít **iothubowner** připojovací řetězec ze služby IoT Hub k dokončení této části. Vás bude připojovací řetězec [webu Azure portal](https://portal.azure.com/) na **zásady sdíleného přístupu** okno.

1. Vytvořte prázdnou složku s názvem ```fileuploadnotification```.  Ve složce ```fileuploadnotification``` vytvořte soubor package.json pomocí následujícího příkazu na příkazovém řádku.  Přijměte všechny výchozí hodnoty:

    ```cmd/sh
    npm init
    ```

1. Na příkazovém řádku v ```fileuploadnotification``` složky, spusťte následující příkaz k instalaci **azure-iothub** balíčku sady SDK:

    ```cmd/sh
    npm install azure-iothub --save
    ```

1. Pomocí textového editoru, vytvořte **FileUploadNotification.js** soubor ```fileuploadnotification``` složky.

1. Přidejte následující ```require``` příkazy na začátku **FileUploadNotification.js** souboru:

    ```javascript
    'use strict';
    
    var Client = require('azure-iothub').Client;
    ```

1. Přidejte proměnnou ```iothubconnectionstring``` a použijte ji k vytvoření instance **klienta**.  Nahraďte ```{iothubconnectionstring}``` připojovacím řetězcem ke službě IoT hub, kterou jste vytvořili v _vytvoření služby IoT Hub_ části:

    ```javascript
    var connectionString = '{iothubconnectionstring}';
    ```

    > [!NOTE]
    > Z důvodu zjednodušení připojovací řetězec je zahrnuta v kódu: Toto není doporučený postup a v závislosti na případu použití a architektury můžete chtít zvážit bezpečnější způsoby ukládání tento tajný kód.

1. Přidejte následující kód k připojení klienta:

    ```javascript
    var serviceClient = Client.fromConnectionString(connectionString);
    ```

1. Otevřít klienta a použít **getFileNotificationReceiver** funkci pro příjem aktualizace stavu.

    ```javascript
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

Následující snímek obrazovky ukazuje výstup z **SimulatedDevice** aplikace:

![Výstup z aplikace simulovaného zařízení](./media/iot-hub-node-node-file-upload/simulated-device.png)

Následující snímek obrazovky ukazuje výstup z **FileUploadNotification** aplikace:

![Výstup z aplikace pro čtení souboru odeslání oznámení](./media/iot-hub-node-node-file-upload/read-file-upload-notification.png)

Na portálu můžete použít k zobrazení nahraných souborů v kontejneru úložiště, které jste nakonfigurovali:

![Nahraný soubor](./media/iot-hub-node-node-file-upload/uploaded-file.png)

## <a name="next-steps"></a>Další postup

V tomto kurzu jste zjistili, jak zjednodušit nahrávání souborů ze zařízení pomocí možnosti nahrávání souborů služby IoT Hub. Můžete pokračovat k prozkoumání funkcí služby IoT hub a scénáře najdete v následujících článcích:

* [Vytvoření centra IoT prostřednictvím kódu programu][lnk-create-hub]
* [Seznámení s C SDK][lnk-c-sdk]
* [Sady Azure IoT SDK][lnk-sdks]

<!-- Links -->
[Centrum pro vývojáře Azure IoT]: http://azure.microsoft.com/develop/iot

[lnk-create-hub]: iot-hub-rm-template-powershell.md
[lnk-c-sdk]: iot-hub-device-sdk-c-intro.md
[lnk-sdks]: iot-hub-devguide-sdks.md
