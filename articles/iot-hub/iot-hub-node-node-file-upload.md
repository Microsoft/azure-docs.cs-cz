---
title: Nahrání souborů ze zařízení do Azure IoT Hub s využitím uzlu | Microsoft Docs
description: Postup nahrání souborů ze zařízení do cloudu pomocí sady SDK pro zařízení Azure IoT pro Node.js. Nahrané soubory se ukládají v kontejneru objektů BLOB služby Azure Storage.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.devlang: nodejs
ms.topic: conceptual
ms.date: 06/28/2017
ms.custom: mqtt, devx-track-js
ms.openlocfilehash: db4f78e14696c421adaedd16b0b3f8d598f12846
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91251894"
---
# <a name="upload-files-from-your-device-to-the-cloud-with-iot-hub-nodejs"></a>Nahrání souborů ze zařízení do cloudu pomocí IoT Hub (Node.js)

[!INCLUDE [iot-hub-file-upload-language-selector](../../includes/iot-hub-file-upload-language-selector.md)]

V tomto kurzu se seznámíte s kódem v tématu [posílání zpráv z cloudu do zařízení pomocí IoT Hub](iot-hub-node-node-c2d.md) kurzu, ve kterém se dozvíte, jak pomocí [možností nahrávání souborů IoT Hub](iot-hub-devguide-file-upload.md) nahrát soubor do [úložiště objektů BLOB v Azure](../storage/index.yml). V tomto kurzu získáte informace o následujících postupech:

* Bezpečně poskytněte zařízení s identifikátorem URI objektu blob Azure pro nahrání souboru.

* Pomocí IoT Hub oznámení o nahrávání souborů můžete aktivovat zpracování souboru v back-endu vaší aplikace.

[Možnost Odeslat telemetrii ze zařízení do centra IoT](quickstart-send-telemetry-node.md) se rychlým startem předvádí základní funkce zasílání zpráv typu zařízení-Cloud IoT Hub. V některých scénářích ale nemůžete snadno namapovat data, která zařízení odesílají do relativně malých zpráv ze zařízení do cloudu, které IoT Hub přijmout. Například:

* Velké soubory, které obsahují obrázky
* Videa
* Ukázka dat vibrací s vysokou frekvencí
* Některá forma předem zpracovaných dat.

Tyto soubory jsou obvykle dávkově zpracovávány v cloudu pomocí nástrojů, jako je [Azure Data Factory](../data-factory/introduction.md) nebo zásobník [Hadoop](../hdinsight/index.yml) . Pokud potřebujete nakládat soubory ze zařízení, můžete i nadále používat zabezpečení a spolehlivost IoT Hub.

Na konci tohoto kurzu spustíte dvě Node.js konzolové aplikace:

* **SimulatedDevice.js**, který nahraje soubor do úložiště pomocí identifikátoru URI SAS, který poskytuje vaše služba IoT Hub.

* **ReadFileUploadNotification.js**, která přijímá oznámení o nahrávání souborů ze služby IoT Hub.

> [!NOTE]
> IoT Hub podporuje spoustu platforem a jazyků zařízení (včetně C, .NET, JavaScriptu, Pythonu a Java) prostřednictvím sad SDK pro zařízení Azure IoT. Podrobné pokyny k připojení zařízení k Azure IoT Hub najdete v tématu [středisko pro vývojáře Azure IoT].

[!INCLUDE [iot-hub-include-x509-ca-signed-file-upload-support-note](../../includes/iot-hub-include-x509-ca-signed-file-upload-support-note.md)]

## <a name="prerequisites"></a>Požadavky

* Node.js verze 10.0. x nebo novější. [Příprava vývojového prostředí](https://github.com/Azure/azure-iot-sdk-node/tree/master/doc/node-devbox-setup.md) popisuje, jak nainstalovat Node.js pro tento kurz v systému Windows nebo Linux.

* Aktivní účet Azure. (Pokud účet nemáte, můžete si během několika minut vytvořit [bezplatný účet](https://azure.microsoft.com/pricing/free-trial/) .)

* Ujistěte se, že je v bráně firewall otevřený port 8883. Ukázka zařízení v tomto článku používá protokol MQTT, který komunikuje přes port 8883. Tento port může být blokovaný v některých podnikových a vzdělávacích prostředích sítě. Další informace a způsoby, jak tento problém obejít, najdete v tématu [připojení k IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

[!INCLUDE [iot-hub-associate-storage](../../includes/iot-hub-associate-storage.md)]

## <a name="upload-a-file-from-a-device-app"></a>Nahrání souboru z aplikace zařízení

V této části vytvoříte aplikaci pro zařízení pro nahrání souboru do centra IoT.

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

5. Přidejte proměnnou `deviceconnectionstring` a použijte ji k vytvoření instance **klienta**.  Nahraďte `{deviceconnectionstring}` názvem zařízení, které jste vytvořili v části *Vytvoření IoT Hub* :

    ```javascript
    var connectionString = '{deviceconnectionstring}';
    var filename = 'myimage.png';
    ```

    > [!NOTE]
    > V zájmu jednoduchosti je připojovací řetězec zahrnutý v kódu: nejedná se o doporučený postup a v závislosti na vašem případu použití a architektuře možná budete chtít zvážit bezpečnější způsoby ukládání tohoto tajného klíče.

6. Přidejte následující kód pro připojení klienta:

    ```javascript
    var client = clientFromConnectionString(connectionString);
    console.log('Client connected');
    ```

7. Vytvořte zpětné volání a použijte funkci **uploadToBlob** pro nahrání souboru.

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

9. Zkopírujte soubor obrázku do `simulateddevice` složky a přejmenujte ho `myimage.png` .

## <a name="get-the-iot-hub-connection-string"></a>Získání připojovacího řetězce centra IoT Hub

V tomto článku vytvoříte back-end službu pro příjem zpráv s oznámením o nahrávání souborů ze služby IoT Hub, kterou jste vytvořili v tématu [odeslání telemetrie ze zařízení do služby IoT Hub](quickstart-send-telemetry-node.md). Aby bylo možné přijímat zprávy s oznámením o nahrávání souborů, vaše služba potřebuje oprávnění **k připojení služby** . Ve výchozím nastavení se všechny IoT Hub vytvoří pomocí zásad sdíleného přístupu s názvem **Služba** , která toto oprávnění udělí.

[!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

## <a name="receive-a-file-upload-notification"></a>Doručení oznámení o nahrání souboru

V této části vytvoříte konzolovou aplikaci Node.js, která přijímá zprávy s oznámením o nahrávání souborů z IoT Hub.

K dokončení této části můžete použít připojovací řetězec **iothubowner** ze svého IoT Hub. Připojovací řetězec najdete v okně [Azure Portal](https://portal.azure.com/) v okně **zásady sdíleného přístupu** .

1. Vytvořte prázdnou složku s názvem ```fileuploadnotification```.  Ve složce ```fileuploadnotification``` vytvořte soubor package.json pomocí následujícího příkazu na příkazovém řádku.  Přijměte všechny výchozí hodnoty:

    ```cmd/sh
    npm init
    ```

2. Na příkazovém řádku ve ```fileuploadnotification``` složce spusťte následující příkaz k instalaci balíčku **Azure-iothub** SDK:

    ```cmd/sh
    npm install azure-iothub --save
    ```

3. Pomocí textového editoru vytvořte soubor **FileUploadNotification.js** ve `fileuploadnotification` složce.

4. `require`Na začátek souboru **FileUploadNotification.js** přidejte následující příkazy:

    ```javascript
    'use strict';

    var Client = require('azure-iothub').Client;
    ```

5. Přidejte proměnnou `iothubconnectionstring` a použijte ji k vytvoření instance **klienta**.  Nahraďte `{iothubconnectionstring}` hodnotu zástupného symbolu připojovacím řetězcem IoT Hub, který jste zkopírovali dříve v [části získání připojovacího řetězce centra IoT](#get-the-iot-hub-connection-string):

    ```javascript
    var connectionString = '{iothubconnectionstring}';
    ```

    > [!NOTE]
    > V zájmu jednoduchosti je připojovací řetězec zahrnutý v kódu: nejedná se o doporučený postup a v závislosti na vašem případu použití a architektuře možná budete chtít zvážit bezpečnější způsoby ukládání tohoto tajného klíče.

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

8. Uložte a zavřete soubor **FileUploadNotification.js** .

## <a name="run-the-applications"></a>Spuštění aplikací

Nyní můžete spustit aplikace.

Na příkazovém řádku ve `fileuploadnotification` složce spusťte následující příkaz:

```cmd/sh
node FileUploadNotification.js
```

Na příkazovém řádku ve `simulateddevice` složce spusťte následující příkaz:

```cmd/sh
node SimulatedDevice.js
```

Následující snímek obrazovky ukazuje výstup z aplikace **SimulatedDevice** :

![Výstup z aplikace simulovaného zařízení](./media/iot-hub-node-node-file-upload/simulated-device.png)

Následující snímek obrazovky ukazuje výstup z aplikace **FileUploadNotification** :

![Výstup z aplikace pro čtení-soubor-nahrávání-oznámení](./media/iot-hub-node-node-file-upload/read-file-upload-notification.png)

K zobrazení nahraného souboru v kontejneru úložiště, který jste nakonfigurovali, můžete použít portál:

![Nahraný soubor](./media/iot-hub-node-node-file-upload/uploaded-file.png)

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste zjistili, jak používat možnosti nahrávání souborů IoT Hub ke zjednodušení nahrávání souborů ze zařízení. Pomocí následujících článků můžete dál prozkoumat funkce a scénáře IoT Hub:

* [Programové vytvoření centra IoT Hub](iot-hub-rm-template-powershell.md)

* [Seznámení se sadou C SDK](iot-hub-device-sdk-c-intro.md)

* [Sady Azure IoT SDK](iot-hub-devguide-sdks.md)
