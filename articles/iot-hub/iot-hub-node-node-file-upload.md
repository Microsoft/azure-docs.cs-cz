---
title: Nahrávání souborů ze zařízení do služby Azure IoT Hub s uzlem | Dokumenty společnosti Microsoft
description: Jak nahrát soubory ze zařízení do cloudu pomocí sady Azure IoT zařízení SDK pro Node.js. Nahrané soubory se ukládají v kontejneru objektů blob úložiště Azure.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.devlang: nodejs
ms.topic: conceptual
ms.date: 06/28/2017
ms.custom: mqtt
ms.openlocfilehash: af9743233a61e8e6d816b362d35e6a38735df35b
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/21/2020
ms.locfileid: "81732251"
---
# <a name="upload-files-from-your-device-to-the-cloud-with-iot-hub-nodejs"></a>Nahrávání souborů ze zařízení do cloudu pomocí služby IoT Hub (Node.js)

[!INCLUDE [iot-hub-file-upload-language-selector](../../includes/iot-hub-file-upload-language-selector.md)]

Tento kurz vychází z kódu v kurzu Odesílat zprávy z cloudu na zařízení pomocí služby [IoT Hub,](iot-hub-node-node-c2d.md) který vám ukáže, jak pomocí [možností nahrávání souborů služby IoT Hub](iot-hub-devguide-file-upload.md) nahrát soubor do úložiště objektů [blob Azure](../storage/index.yml). V tomto kurzu získáte informace o následujících postupech:

* Bezpečně poskytněte zařízení s identifikátorem URI objektu BLOB Azure pro nahrávání souboru.

* Pomocí oznámení o nahrání souboru centra IoT Hub aktivujte zpracování souboru v back-endu aplikace.

[Odeslat telemetrii ze zařízení do rychlého startu služby IoT hub](quickstart-send-telemetry-node.md) demonstruje základní funkce zasílání zpráv mezi zařízeními a cloudy služby IoT Hub. V některých případech však nelze snadno mapovat data, která vaše zařízení odesílají do relativně malých zpráv zařízení cloud, které služba IoT Hub přijímá. Příklad:

* Velké soubory, které obsahují obrázky
* Videa
* Údaje o vibracích odebrané při vysoké frekvenci
* Nějaká forma předem zpracovaných dat.

Tyto soubory se obvykle zpracovávají v cloudu pomocí nástrojů, jako je [Azure Data Factory](../data-factory/introduction.md) nebo zásobník [Hadoop.](../hdinsight/index.yml) Když potřebujete upland soubory ze zařízení, můžete stále používat zabezpečení a spolehlivost IoT Hub.

Na konci tohoto kurzu spustíte dvě konzolové aplikace Node.js:

* **SimulatedDevice.js**, který nahraje soubor do úložiště pomocí identifikátoru URI SAS poskytovaného službou IoT hub.

* **Soubor ReadFileUploadNotification.js**, který přijímá oznámení o nahrání souborů z centra IoT.

> [!NOTE]
> IoT Hub podporuje mnoho platforem a jazyků zařízení (včetně C, .NET, Javascript, Python a Java) prostřednictvím sad SDK zařízení Azure IoT. Podrobné pokyny k připojení zařízení k azure iot hubu najdete v Centru pro vývojáře [Azure IoT].

## <a name="prerequisites"></a>Požadavky

* Node.js verze 10.0.x nebo novější. [Příprava vývojového prostředí](https://github.com/Azure/azure-iot-sdk-node/tree/master/doc/node-devbox-setup.md) popisuje, jak nainstalovat soubor Node.js pro tento kurz v systému Windows nebo Linux.

* Aktivní účet Azure. (Pokud nemáte účet, můžete si vytvořit [bezplatný účet](https://azure.microsoft.com/pricing/free-trial/) během několika minut.)

* Zkontrolujte, zda je v bráně firewall otevřený port 8883. Ukázka zařízení v tomto článku používá protokol MQTT, který komunikuje přes port 8883. Tento port může být blokován v některých prostředích podnikové a vzdělávací sítě. Další informace a způsoby, jak tento problém vyřešit, najdete [v tématu připojení k centru IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

[!INCLUDE [iot-hub-associate-storage](../../includes/iot-hub-associate-storage.md)]

## <a name="upload-a-file-from-a-device-app"></a>Nahrání souboru z aplikace pro zařízení

V této části vytvoříte aplikaci pro zařízení pro nahrání souboru do centra IoT hub.

1. Vytvořte prázdnou složku s názvem ```simulateddevice```.  Ve složce ```simulateddevice``` vytvořte soubor package.json pomocí následujícího příkazu na příkazovém řádku.  Přijměte všechny výchozí hodnoty:

    ```cmd/sh
    npm init
    ```

2. Na příkazovém řádku ve složce ```simulateddevice``` spusťte následující příkaz k instalaci balíčku sady SDK pro zařízení **azure-iot-device** a balíčku **azure-iot-device-mqtt**:

    ```cmd/sh
    npm install azure-iot-device azure-iot-device-mqtt --save
    ```

3. Pomocí textového editoru vytvořte ve složce ```simulateddevice``` soubor **SimulatedDevice.js**.

4. Na začátek souboru **SimulatedDevice.js** přidejte následující příkazy ```require```:

    ```javascript
    'use strict';

    var fs = require('fs');
    var mqtt = require('azure-iot-device-mqtt').Mqtt;
    var clientFromConnectionString = require('azure-iot-device-mqtt').clientFromConnectionString;
    ```

5. Přidejte proměnnou `deviceconnectionstring` a použijte ji k vytvoření instance **klienta**.  Nahraďte `{deviceconnectionstring}` název zařízení, které jste vytvořili v části *Vytvořit centrum IoT Hub:*

    ```javascript
    var connectionString = '{deviceconnectionstring}';
    var filename = 'myimage.png';
    ```

    > [!NOTE]
    > Z důvodu jednoduchosti připojovací řetězec je součástí kódu: to není doporučená praxe a v závislosti na případu použití a architektury můžete zvážit bezpečnější způsoby ukládání tohoto tajného klíče.

6. Přidejte následující kód pro připojení klienta:

    ```javascript
    var client = clientFromConnectionString(connectionString);
    console.log('Client connected');
    ```

7. Vytvořte zpětné volání a pomocí funkce **uploadToBlob** nahrajte soubor.

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

8. Soubor **SimulatedDevice.js** uložte a zavřete.

9. Zkopírujte soubor obrázku do `simulateddevice` složky a přejmenujte jej `myimage.png`.

## <a name="get-the-iot-hub-connection-string"></a>Získání připojovacího řetězce centra IoT hub

V tomto článku vytvoříte back-endovou službu pro příjem oznámení o nahrání souborů z centra IoT, které jste vytvořili v [aplikaci Odeslat telemetrii ze zařízení do služby IoT hub](quickstart-send-telemetry-node.md). Chcete-li přijímat oznámení o nahrání souboru, vaše služba potřebuje oprávnění **k připojení služby.** Ve výchozím nastavení je každé centrum IoT Hub vytvořeno pomocí zásady sdíleného přístupu s názvem **služba,** která uděluje toto oprávnění.

[!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

## <a name="receive-a-file-upload-notification"></a>Přijetí oznámení o nahrání souboru

V této části vytvoříte konzolovou aplikaci Node.js, která přijímá oznámení o nahrání souborů z centra IoT Hub.

K dokončení této části můžete použít připojovací řetězec **iothubowner** z vašeho ioT Hubu. Připojovací řetězec najdete na [portálu Azure](https://portal.azure.com/) na okně **zásad sdíleného přístupu.**

1. Vytvořte prázdnou složku s názvem ```fileuploadnotification```.  Ve složce ```fileuploadnotification``` vytvořte soubor package.json pomocí následujícího příkazu na příkazovém řádku.  Přijměte všechny výchozí hodnoty:

    ```cmd/sh
    npm init
    ```

2. Na příkazovém ```fileuploadnotification``` řádku ve složce spusťte následující příkaz k instalaci balíčku **sady Azure-iothub** SDK:

    ```cmd/sh
    npm install azure-iothub --save
    ```

3. Pomocí textového editoru vytvořte soubor **FileUploadNotification.js** ve `fileuploadnotification` složce.

4. Na začátek souboru `require` **FileUploadNotification.js** přidejte následující příkazy:

    ```javascript
    'use strict';

    var Client = require('azure-iothub').Client;
    ```

5. Přidejte proměnnou `iothubconnectionstring` a použijte ji k vytvoření instance **klienta**.  Nahraďte `{iothubconnectionstring}` zástupnou hodnotu připojovacím řetězcem služby IoT hub, který jste dříve zkopírovali v [připojovacím řetězci Služby IoT hub](#get-the-iot-hub-connection-string):

    ```javascript
    var connectionString = '{iothubconnectionstring}';
    ```

    > [!NOTE]
    > Z důvodu jednoduchosti připojovací řetězec je součástí kódu: to není doporučená praxe a v závislosti na případu použití a architektury můžete zvážit bezpečnější způsoby ukládání tohoto tajného klíče.

6. Přidejte následující kód pro připojení klienta:

    ```javascript
    var serviceClient = Client.fromConnectionString(connectionString);
    ```

7. Otevřete klienta a použijte funkci **getFileNotificationReceiver** pro příjem aktualizací stavu.

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

8. Uložte a zavřete soubor **FileUploadNotification.js.**

## <a name="run-the-applications"></a>Spuštění aplikací

Nyní můžete spustit aplikace.

Na příkazovém `fileuploadnotification` řádku ve složce spusťte následující příkaz:

```cmd/sh
node FileUploadNotification.js
```

Na příkazovém `simulateddevice` řádku ve složce spusťte následující příkaz:

```cmd/sh
node SimulatedDevice.js
```

Následující snímek obrazovky ukazuje výstup z aplikace **SimulatedDevice:**

![Výstup z aplikace simulované zařízení](./media/iot-hub-node-node-file-upload/simulated-device.png)

Následující snímek obrazovky ukazuje výstup z aplikace **FileUploadNotification:**

![Výstup z aplikace read-file-upload-notification](./media/iot-hub-node-node-file-upload/read-file-upload-notification.png)

Pomocí portálu můžete zobrazit nahraný soubor v kontejneru úložiště, který jste nakonfigurovali:

![Nahraný soubor](./media/iot-hub-node-node-file-upload/uploaded-file.png)

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili používat možnosti nahrávání souborů v centru IoT Hub ke zjednodušení odesílání souborů ze zařízení. Můžete pokračovat v prozkoumání funkcí a scénářů centra IoT v následujících článcích:

* [Programové vytvoření centra IoT hub](iot-hub-rm-template-powershell.md)

* [Úvod do sady C SDK](iot-hub-device-sdk-c-intro.md)

* [Sady Azure IoT SDK](iot-hub-devguide-sdks.md)
