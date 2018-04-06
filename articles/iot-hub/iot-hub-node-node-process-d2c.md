---
title: Směrování zpráv službou Azure IoT Hub (uzel) | Microsoft Docs
description: Postupy zpracování zpráv typu zařízení cloud Azure IoT Hub pomocí pravidel směrování a vlastní koncové body k odeslání zprávy do dalších služeb back-end.
services: iot-hub
documentationcenter: node
author: dominicbetts
manager: timlt
editor: ''
ms.assetid: bd9af5f9-a740-4780-a2a6-8c0e2752cf48
ms.service: iot-hub
ms.devlang: node
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/17/2017
ms.author: v-masebo;dobett
ms.openlocfilehash: 02edb06d2d871cffac717358e33a6720c444a9b3
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/05/2018
---
# <a name="routing-messages-with-iot-hub-node"></a>Směrování zpráv službou IoT Hub (uzel)

[!INCLUDE [iot-hub-selector-process-d2c](../../includes/iot-hub-selector-process-d2c.md)]

V tomto kurzu vychází [Začínáme se službou IoT Hub] kurzu.  Kurz:

* Ukazuje, jak používat směrování pravidla k odesílání zpráv typu zařízení cloud způsobem snadno, založené na konfiguraci.
* Ukazuje, jak izolovat interaktivní zprávy, které vyžadují okamžitou akci z back-end řešení pro další zpracování.  Zařízení může například odešle zprávu výstrahy, která aktivuje vkládání lístek do systému CRM.  Naproti tomu datového bodu zprávy, jako je například teplotní telemetrie informačního kanálu do modul analytics.

Na konci tohoto kurzu můžete spustit aplikace konzoly tři Node.js:

* **SimulatedDevice.js**, upravenou verzi aplikace vytvořená v [Začínáme se službou IoT Hub] kurzu každý druhý a interaktivní zprávy o zařízení cloud za náhodné odešle zprávy typu zařízení cloud datového bodu interval. Tato aplikace používá protokol MQTT ke komunikaci se službou IoT Hub.
* **ReadDeviceToCloudMessages.js** zobrazuje telemetrické zprávy odesílané aplikace zařízení.
* **ReadCriticalQueue.js** zrušte fronty kritické zprávy z fronty Service Bus připojit ke službě IoT hub.

> [!NOTE]
> IoT Hub je podpora v sadě SDK pro mnoho zařízení platformy a jazyky, včetně C, Java a JavaScript. Pokyny, jak nahradit zařízení v tomto kurzu fyzické zařízení a jak pro připojení zařízení do služby IoT Hub, najdete v článku [Azure střediska pro vývojáře IoT].

Pro absolvování tohoto kurzu potřebujete:

* Úplnou verzi práci [Začínáme se službou IoT Hub] kurzu.
* Node.js verze 4.0.x nebo novější.
* Aktivní účet Azure. (Pokud účet nemáte, můžete si během několika minut vytvořit [bezplatný účet][lnk-free-trial].)

Doporučujeme také výklad o [Azure Storage] a [Azure Service Bus].

## <a name="send-interactive-messages-from-a-device-app"></a>Odesílat interaktivní zprávy z aplikace na zařízení
V této části upravíte zařízení aplikaci, kterou jste vytvořili v [Začínáme se službou IoT Hub] kurzu příležitostně odesílat zprávy, které vyžadují okamžitou zpracování.

1. Pomocí textového editoru otevřete **simulateddevice\SimulatedDevice.js** souboru. Tento soubor obsahuje kód pro **SimulatedDevice** aplikace, které jste vytvořili v [Začínáme se službou IoT Hub] kurzu.

2. Nahraďte **connectCallback** funkce následujícím kódem:

    ```nodejs
    var connectCallback = function (err) {
      if (err) {
        console.log('Could not connect: ' + err);
      } else {
        console.log('Client connected');
    
        // Create a message and send it to the IoT Hub every second
        setInterval(function(){
        var data, message;
        if (Math.random() > 0.7) {
            if (Math.random() > 0.5) {
                data = "This is a critical message.";
                message = new Message(data);
                message.properties.add("level", "critical");
            } else {
                data = "This is a storage message.";
                message = new Message(data);
                message.properties.add("level", "storage");
            }
            console.log("Sending message: " + message.getData());
        }
        else {
                var temperature = 20 + (Math.random() * 15);
                var humidity = 60 + (Math.random() * 20);            
                data = JSON.stringify({ deviceId: 'myFirstNodeDevice', temperature: temperature, humidity: humidity });
                message = new Message(data);
                message.properties.add('temperatureAlert', (temperature > 30) ? 'true' : 'false');
                console.log("Sending message: " + message.getData());
            }
        client.sendEvent(message, printResultFor('send'));
        }, 1000);
      }
    };
    ```
   
    Tato metoda náhodně přidá vlastnost `"level": "critical"` a `"level": "storage"` na zprávy odeslané zařízením, která simuluje zprávu, která vyžaduje okamžitý zásah back-end aplikace nebo ten, který musí být trvale uložená. Aplikace podporuje směrování zpráv podle tělo zprávy.
   
   > [!NOTE]
   > Vlastnosti zprávy pro směrování zpráv můžete použít pro různé scénáře, včetně studený cesty při zpracování, kromě zde ukazuje příklad aktivní trase.

2. Uložte a zavřete **simulateddevice\SimulatedDevice.js** souboru.

    > [!NOTE]
    > Důrazně doporučujeme implementovat zásady opakování například exponenciálního omezení rychlosti dle pokynů v článku na webu MSDN [přechodných chyb].

## <a name="add-service-bus-queue-to-your-iot-hub-and-route-messages-to-it"></a>Přidejte do IoT hub a směrování zpráv do ní fronty Service Bus

V této části Vytvoření fronty Service Bus, připojte ho do služby IoT hub a konfigurace služby IoT hub odesílat zprávy do fronty na základě přítomnosti vlastnosti na zprávu. Další informace o tom, jak zpracování zpráv z fronty služby Service Bus najdete v tématu [začít pracovat s fronty][lnk-sb-queues-node].

1. Vytvoření fronty Service Bus, jak je popsáno v [začít pracovat s fronty][lnk-sb-queues-node]. Poznamenejte si název oboru názvů a fronty.

    > [!NOTE]
    > Fronty sběrnice a témata použít jako koncové body centra IoT nesmí mít **relací** nebo **duplicitní detekce** povolena. Pokud některá z těchto možností jsou povolené, koncový bod se zobrazí jako **Unreachable** na portálu Azure.

2. Na portálu Azure otevřete své služby IoT hub a klikněte na tlačítko **koncové body**.

    ![Koncové body v centru IoT][30]

3. V **koncové body** okně klikněte na tlačítko **přidat** v horní části přidat fronty do služby IoT hub. Název koncového bodu **CriticalQueue** a použijte rozevírací seznamy a vyberte **fronty Service Bus**, oboru názvů Service Bus, ve které je umístěn fronty a názvu fronty. Až budete hotovi, klikněte na tlačítko **OK** dole.  

    ![Přidání koncového bodu][31]

4. Klikněte na tlačítko **trasy** ve službě IoT Hub. Klikněte na tlačítko **přidat** v horní části okna Vytvořit pravidlo směrování pro směrování zpráv do fronty jste právě přidali. Vyberte **zprávy zařízení** jako zdroj dat. Zadejte `level="critical"` jako podmínka a zvolte **CriticalQueue** jako vlastní koncový bod jako koncový bod směrování pravidlo. Klikněte na tlačítko **Uložit** dole.  

    ![Přidání trasu][32]

    Ujistěte se, že záložní cesta je nastavena na **ON**. Toto nastavení je výchozí konfigurace služby IoT hub.

    ![Záložní trasy][33]

## <a name="optional-read-from-the-queue-endpoint"></a>(Volitelné) Čtení z fronty koncového bodu

V této části vytvoříte konzolovou aplikaci softwaru Node.js, který čte kritické zprávy ze sběrnice IoT. Zobrazit další informace v [začít pracovat s fronty][lnk-sb-queues-node]. 

1. Vytvořte prázdnou složku s názvem `readcriticalqueue`. Ve složce `readcriticalqueue` vytvořte soubor package.json pomocí následujícího příkazu na příkazovém řádku. Přijměte všechny výchozí hodnoty:

    ```cmd/sh
    npm init
    ```

2. Na příkazovém řádku v `readcriticalqueue` složky, spusťte následující příkaz k instalaci **azure** balíčku:

    ```cmd/sh
    npm install azure --save
    ```

3. Pomocí textového editoru, vytvořte **ReadCriticalQueue.js** v soubor `readcriticalqueue` složky.

4. Přidejte následující `require` příkazy na začátku **ReadCriticalQueue.js** souboru:

    ```nodejs
    'use strict';

    var azure = require('azure');
    ```

5. Přidejte následující deklarace proměnné a zástupné hodnoty nahraďte názvem IoT Service Bus připojovací řetězec a fronty:

    ```nodejs
    var connectionString = '{iotservicebus connection string}';
    var queueName = '{iotservice bus queue name}';
    ```

6. Přidejte následující dvě funkce, které vytisknout výstup do konzoly:

    ```nodejs
    var serviceBusService = azure.createServiceBusService(connectionString);

    setInterval(function() {serviceBusService.receiveQueueMessage(queueName, function(error, receivedMessage) {
        if(!error){
            // Message received and deleted
            console.log(receivedMessage);
        } else { console.log(error); }
        });
    }, 3000);
    ```

7. Uložte a zavřete **ReadCriticalQueue.js** souboru.

## <a name="run-the-applications"></a>Spuštění aplikací

Nyní jste připraveni ke spuštění tři aplikací.

1. Ke spuštění **ReadDeviceToCloudMessages.js** aplikace, v příkazovém řádku nebo prostředí přejděte do složky readdevicetocloudmessages a spusťte následující příkaz:

   ```cmd/sh
   node ReadDeviceToCloudMessages.js
   ```

   ![Spustit read-d2c-messages][readd2c]

2. Ke spuštění **ReadCriticalQueue.js** aplikace, v příkazovém řádku nebo prostředí přejděte do složky readcriticalqueue a spusťte následující příkaz:

   ```cmd/sh
   node ReadCriticalQueue.js
   ```
   
   ![Spustit pro čtení kritické zprávy][readqueue]

3. Ke spuštění **SimulatedDevice.js** aplikaci, v příkazovém řádku nebo prostředí přejděte do složky simulateddevice a spusťte následující příkaz:

   ```cmd/sh
   node SimulatedDevice.js
   ```
   
   ![Spustit simulated-device][simulateddevice]

## <a name="optional-add-storage-container-to-your-iot-hub-and-route-messages-to-it"></a>(Volitelné) Přidat kontejner úložiště pro IoT hub a směrování zpráv do ní

V této části vytvořit účet úložiště, připojte ho do služby IoT hub a konfigurace služby IoT hub pro odesílání zpráv pro účet, na základě přítomnosti vlastnosti na zprávu. Další informace o tom, jak spravovat úložiště najdete v tématu [Začínáme s Azure Storage][Azure Storage].

 > [!NOTE]
   > Pokud si nejste omezená na jedno **koncový bod**, může instalace **StorageContainer** kromě **CriticalQueue** a spusťte obě simulatneously.

1. Vytvořit účet úložiště, jak je popsáno v [dokumentaci pro Azure Storage][lnk-storage]. Poznamenejte si název účtu.

2. Na portálu Azure otevřete své služby IoT hub a klikněte na tlačítko **koncové body**.

3. V **koncové body** okně, vyberte **CriticalQueue** koncový bod a klikněte na tlačítko **odstranit**. Klikněte na tlačítko **Ano**a potom klikněte na **přidat**. Název koncového bodu **StorageContainer** a použijte rozevírací seznamy a vyberte **kontejneru úložiště Azure**a vytvořte **účet úložiště** a **úložiště kontejner**.  Poznamenejte si názvy.  Až budete hotovi, klikněte na tlačítko **OK** dole. 

 > [!NOTE]
   > Pokud si nejste omezená na jedno **koncový bod**, není potřeba odstranit **CriticalQueue**.

4. Klikněte na tlačítko **trasy** ve službě IoT Hub. Klikněte na tlačítko **přidat** v horní části okna Vytvořit pravidlo směrování pro směrování zpráv do fronty jste právě přidali. Vyberte **zprávy zařízení** jako zdroj dat. Zadejte `level="storage"` jako podmínka a zvolte **StorageContainer** jako vlastní koncový bod jako koncový bod směrování pravidlo. Klikněte na tlačítko **Uložit** dole.  

    Ujistěte se, že záložní cesta je nastavena na **ON**. Toto nastavení je výchozí konfigurace služby IoT hub.

1. Zajistěte, aby předchozí aplikaci **SimulatedDevice.js** je stále spuštěná. 

1. Na portálu Azure přejděte na svůj účet úložiště, v části **služby objektů Blob**, klikněte na tlačítko **procházet objekty BLOB...** .  Vyberte vašeho kontejneru, přejděte na a klikněte na soubor JSON a klikněte na tlačítko **Stáhnout** chcete zobrazit data.

## <a name="next-steps"></a>Další postup

V tomto kurzu jste zjistili, jak spolehlivě odesláním zprávy typu zařízení cloud pomocí funkce směrování zpráv služby IoT Hub.

[Odesílání zpráv typu cloud zařízení s centrem IoT] [ lnk-c2d] ukazuje, jak k odesílání zpráv do vašeho zařízení z back end vašeho řešení.

Příklady dokončení začátku do konce řešení, které pomocí služby IoT Hub, najdete v sekci [Azure IoT Suite][lnk-suite].

Další informace o vývoji řešení službou IoT Hub, najdete v článku [Příručka vývojáře pro službu IoT Hub].

Další informace o směrování zpráv do služby IoT Hub, najdete v části [odesílat a přijímat zprávy službou IoT Hub][lnk-devguide-messaging].

<!-- Images. -->
<!-- TODO: UPDATE PICTURES -->
[simulateddevice]: ./media/iot-hub-node-node-process-d2c/runsimulateddevice.png
[readd2c]: ./media/iot-hub-node-node-process-d2c/runprocessinteractive.png
[readqueue]: ./media/iot-hub-node-node-process-d2c/runprocessd2c.png

[30]: ./media/iot-hub-node-node-process-d2c/click-endpoints.png
[31]: ./media/iot-hub-node-node-process-d2c/endpoint-creation.png
[32]: ./media/iot-hub-node-node-process-d2c/route-creation.png
[33]: ./media/iot-hub-node-node-process-d2c/fallback-route.png

<!-- Links -->

[lnk-sb-queues-node]: ../service-bus-messaging/service-bus-nodejs-how-to-use-queues.md

[Azure Storage]: https://azure.microsoft.com/documentation/services/storage/
[Azure Service Bus]: https://azure.microsoft.com/documentation/services/service-bus/

[Příručka vývojáře pro službu IoT Hub]: iot-hub-devguide.md
[lnk-devguide-messaging]: iot-hub-devguide-messaging.md
[Začínáme se službou IoT Hub]: iot-hub-node-node-getstarted.md
[Azure střediska pro vývojáře IoT]: https://azure.microsoft.com/develop/iot
[přechodných chyb]: https://msdn.microsoft.com/library/hh675232.aspx

<!-- Links -->
[Přechodná chyba zpracování]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx

[lnk-c2d]: iot-hub-node-node-c2d.md
[lnk-suite]: https://azure.microsoft.com/documentation/suites/iot-suite/
[lnk-free-trial]: https://azure.microsoft.com/free/
[lnk-storage]: https://docs.microsoft.com/azure/storage/