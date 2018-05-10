---
title: Směrování zpráv službou Azure IoT Hub (Java) | Microsoft Docs
description: Postupy zpracování zpráv typu zařízení cloud Azure IoT Hub pomocí pravidel směrování a vlastní koncové body k odeslání zprávy do dalších služeb back-end.
services: iot-hub
documentationcenter: java
author: dominicbetts
manager: timlt
editor: ''
ms.assetid: bd9af5f9-a740-4780-a2a6-8c0e2752cf48
ms.service: iot-hub
ms.devlang: java
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/29/2017
ms.author: dobett
ms.openlocfilehash: ff45e9d717b93f89eb8f751294788f08a2fd4592
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2018
---
# <a name="routing-messages-with-iot-hub-java"></a>Směrování zpráv službou IoT Hub (Java)

[!INCLUDE [iot-hub-selector-process-d2c](../../includes/iot-hub-selector-process-d2c.md)]

Azure IoT Hub je plně spravovaná služba, která umožňuje spolehlivou a zabezpečené obousměrnou komunikaci mezi miliony zařízení a řešením back-end. Ostatní kurzy ([Začínáme se službou IoT Hub] a [odesílání zpráv typu cloud zařízení s centrem IoT][lnk-c2d]) ukazují, jak využívat základní funkce zařízení cloud a z cloudu do zařízení zasílání zpráv služby IoT Hub.

V tomto kurzu vychází uvedeném v kódu [Začínáme se službou IoT Hub] kurzu a ukazuje, jak pomocí směrování zpráv ke zpracování zpráv typu zařízení cloud škálovatelné způsobem. Tento kurz ukazuje, jak ke zpracování zpráv, které vyžadují okamžitou akci z back-end řešení. Zařízení může například odešle zprávu výstrahy, která aktivuje vkládání lístek do systému CRM. Naopak datového bodu zprávy jednoduše informačního kanálu do modul analytics. Například teplotní telemetrie ze zařízení, které je nutné uložit pro pozdější analýzu je zpráva datového bodu.

Na konci tohoto kurzu můžete spustit tři aplikace konzoly v jazyce Java:

* **simulated-device**, upravenou verzi aplikace vytvořená v [Začínáme se službou IoT Hub] kurzu posílání zpráv typu zařízení cloud datového bodu za sekundu a interaktivní zařízení cloud zprávy každých 10 sekund. . Tato aplikace používá protokol AMQP ke komunikaci se službou IoT Hub.
* **Read-d2c-messages** zobrazuje telemetrické zprávy odesílané aplikace zařízení.
* **čtení kritické fronty** zrušte fronty kritické zprávy z fronty Service Bus připojit ke službě IoT hub.

> [!NOTE]
> IoT Hub je podpora v sadě SDK pro mnoho zařízení platformy a jazyky, včetně C, Java a JavaScript. Pokyny, jak nahradit zařízení v tomto kurzu fyzické zařízení a jak pro připojení zařízení do služby IoT Hub, najdete v článku [Azure střediska pro vývojáře IoT].

Pro absolvování tohoto kurzu potřebujete:

* Úplnou verzi práci [Začínáme se službou IoT Hub] kurzu.
* Nejnovější [Java SE Development Kit 8](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)
* [Maven 3](https://maven.apache.org/install.html)
* Aktivní účet Azure. (Pokud účet nemáte, můžete si během několika minut vytvořit [bezplatný účet][lnk-free-trial].)

Doporučujeme také výklad o [Azure Storage] a [Azure Service Bus].

## <a name="send-interactive-messages-from-a-device-app"></a>Odesílat interaktivní zprávy z aplikace na zařízení
V této části upravíte zařízení aplikaci, kterou jste vytvořili v [Začínáme se službou IoT Hub] kurzu příležitostně odesílat zprávy, které vyžadují okamžitou zpracování.

1. Pomocí textového editoru otevřete soubor simulated-device\src\main\java\com\mycompany\app\App.java. Tento soubor obsahuje kód pro **simulated-device** aplikace, které jste vytvořili v [Začínáme se službou IoT Hub] kurzu.

2. Nahraďte **MessageSender** třídy následujícím kódem:

    ```java
    private static class MessageSender implements Runnable {

        public void run()  {
            try {
                double minTemperature = 20;
                double minHumidity = 60;
                Random rand = new Random();

                while (true) {
                    String msgStr;
                    Message msg;
                    if (new Random().nextDouble() > 0.7) {
                        if (new Random().nextDouble() > 0.5) {
                            msgStr = "This is a critical message.";
                            msg = new Message(msgStr);
                            msg.setProperty("level", "critical");
                        } else {
                            msgStr = "This is a storage message.";
                            msg = new Message(msgStr);
                            msg.setProperty("level", "storage");
                        }
                    } else {
                        double currentTemperature = minTemperature + rand.nextDouble() * 15;
                        double currentHumidity = minHumidity + rand.nextDouble() * 20; 
                        TelemetryDataPoint telemetryDataPoint = new TelemetryDataPoint();
                        telemetryDataPoint.deviceId = deviceId;
                        telemetryDataPoint.temperature = currentTemperature;
                        telemetryDataPoint.humidity = currentHumidity;

                        msgStr = telemetryDataPoint.serialize();
                        msg = new Message(msgStr);
                    }
                    
                    System.out.println("Sending: " + msgStr);

                    Object lockobj = new Object();
                    EventCallback callback = new EventCallback();
                    client.sendEventAsync(msg, callback, lockobj);

                    synchronized (lockobj) {
                        lockobj.wait();
                    }
                    Thread.sleep(1000);
                }
            } catch (InterruptedException e) {
                System.out.println("Finished.");
            }
        }
    }
    ```
   
    Tato metoda náhodně přidá vlastnost `"level": "critical"` a `"level": "storage"` na zprávy odeslané zařízením, která simuluje zprávu, která vyžaduje okamžitý zásah back-end aplikace nebo ten, který musí být trvale uložená. Aplikace podporuje směrování zpráv podle tělo zprávy.
   
   > [!NOTE]
   > Vlastnosti zprávy pro směrování zpráv můžete použít pro různé scénáře, včetně studený cesty při zpracování, kromě zde ukazuje příklad aktivní trase.

2. Uložte a zavřete soubor simulated-device\src\main\java\com\mycompany\app\App.java.

    > [!NOTE]
    > Důrazně doporučujeme implementovat zásady opakování například exponenciálního omezení rychlosti dle pokynů v článku na webu MSDN [přechodných chyb].

3. Aplikaci **simulated-device** pomocí nástroje Maven sestavíte tak, že v příkazovém řádku ve složce simulated-device spustíte následující příkaz:

    ```cmd/sh
    mvn clean package -DskipTests
    ```

## <a name="add-a-queue-to-your-iot-hub-and-route-messages-to-it"></a>Přidat do fronty pro IoT hub a směrování zpráv do ní

V této části Vytvoření fronty Service Bus, připojte ho do služby IoT hub a konfigurace služby IoT hub odesílat zprávy do fronty na základě přítomnosti vlastnosti na zprávu. Další informace o tom, jak zpracování zpráv z fronty služby Service Bus najdete v tématu [začít pracovat s fronty][lnk-sb-queues-java].

1. Vytvoření fronty Service Bus, jak je popsáno v [začít pracovat s fronty][lnk-sb-queues-java]. Poznamenejte si název oboru názvů a fronty.

    > [!NOTE]
    > Fronty sběrnice a témata použít jako koncové body centra IoT nesmí mít **relací** nebo **duplicitní detekce** povolena. Pokud některá z těchto možností jsou povolené, koncový bod se zobrazí jako **Unreachable** na portálu Azure.

2. Na portálu Azure otevřete své služby IoT hub a klikněte na tlačítko **koncové body**.

    ![Koncové body v centru IoT][30]

3. V **koncové body** okně klikněte na tlačítko **přidat** v horní části přidat fronty do služby IoT hub. Název koncového bodu **CriticalQueue** a použijte rozevírací seznamy a vyberte **fronty Service Bus**, oboru názvů Service Bus, ve které je umístěn fronty a názvu fronty. Až budete hotovi, klikněte na tlačítko **Uložit** dole.

    ![Přidání koncového bodu][31]

4. Klikněte na tlačítko **trasy** ve službě IoT Hub. Klikněte na tlačítko **přidat** v horní části okna Vytvořit pravidlo směrování pro směrování zpráv do fronty jste právě přidali. Vyberte **DeviceTelemetry** jako zdroj dat. Zadejte `level="critical"` jako podmínka a vyberte fronty, který jste právě přidali jako vlastní koncový bod jako koncový bod směrování pravidlo. Až budete hotovi, klikněte na tlačítko **Uložit** dole.

    ![Přidání trasu][32]

    Ujistěte se, že záložní cesta je nastavena na **ON**. Toto nastavení je výchozí konfigurace služby IoT hub.

    ![Záložní trasy][33]

## <a name="optional-read-from-the-queue-endpoint"></a>(Volitelné) Čtení z fronty koncového bodu

Volitelně můžete čtení zpráv z fronty koncového bodu podle pokynů v [začít pracovat s fronty][lnk-sb-queues-java]. Název aplikace **čtení kritické fronty**.

## <a name="run-the-applications"></a>Spuštění aplikací

Nyní jste připraveni ke spuštění tři aplikací.

1. Ke spuštění **read-d2c-messages** aplikace, v příkazovém řádku nebo prostředí přejděte do složky read-d2c a spusťte následující příkaz:

   ```cmd/sh
   mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
   ```

   ![Spustit read-d2c-messages][readd2c]

2. Ke spuštění **čtení kritické fronty** aplikace, v příkazovém řádku nebo prostředí přejděte do složky pro čtení kritické fronty a spusťte následující příkaz:

   ```cmd/sh
   mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
   ```
   
   ![Spustit pro čtení kritické zprávy][readqueue]

3. Ke spuštění **simulated-device** aplikaci, v příkazovém řádku nebo prostředí přejděte do složky simulated-device a spusťte následující příkaz:

   ```cmd/sh
   mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
   ```
   
   ![Spustit simulated-device][simulateddevice]

## <a name="optional-add-storage-container-to-your-iot-hub-and-route-messages-to-it"></a>(Volitelné) Přidat kontejner úložiště pro IoT hub a směrování zpráv do ní

V této části vytvořit účet úložiště, připojte ho do služby IoT hub a konfigurace služby IoT hub pro odesílání zpráv pro účet, na základě přítomnosti vlastnosti na zprávu. Další informace o tom, jak spravovat úložiště najdete v tématu [Začínáme s Azure Storage][Azure Storage].

 > [!NOTE]
   > Pokud si nejste omezená na jedno **koncový bod**, může instalace **StorageContainer** kromě **CriticalQueue** a spusťte obě simulatneously.

1. Vytvořte účet úložiště, jak je popsáno v [dokumentaci úložiště Azure] [lnk úložiště]. Poznamenejte si název účtu.

2. Na portálu Azure otevřete své služby IoT hub a klikněte na tlačítko **koncové body**.

3. V **koncové body** okně, vyberte **CriticalQueue** koncový bod a klikněte na tlačítko **odstranit**. Klikněte na tlačítko **Ano**a potom klikněte na **přidat**. Název koncového bodu **StorageContainer** a použijte rozevírací seznamy a vyberte **kontejneru úložiště Azure**a vytvořte **účet úložiště** a **úložiště kontejner**.  Poznamenejte si názvy.  Až budete hotovi, klikněte na tlačítko **OK** dole. 

 > [!NOTE]
   > Pokud si nejste omezená na jedno **koncový bod**, není potřeba odstranit **CriticalQueue**.

4. Klikněte na tlačítko **trasy** ve službě IoT Hub. Klikněte na tlačítko **přidat** v horní části okna Vytvořit pravidlo směrování pro směrování zpráv do fronty jste právě přidali. Vyberte **zprávy zařízení** jako zdroj dat. Zadejte `level="storage"` jako podmínka a zvolte **StorageContainer** jako vlastní koncový bod jako koncový bod směrování pravidlo. Klikněte na tlačítko **Uložit** dole.  

    Ujistěte se, že záložní cesta je nastavena na **ON**. Toto nastavení je výchozí konfigurace služby IoT hub.

1. Ujistěte se, že předchozí aplikace stále běží. 

1. Na portálu Azure přejděte na svůj účet úložiště, v části **služby objektů Blob**, klikněte na tlačítko **procházet objekty BLOB...** .  Vyberte vašeho kontejneru, přejděte na a klikněte na soubor JSON a klikněte na tlačítko **Stáhnout** chcete zobrazit data.

## <a name="next-steps"></a>Další postup

V tomto kurzu jste zjistili, jak spolehlivě odesláním zprávy typu zařízení cloud pomocí funkce směrování zpráv služby IoT Hub.

[Odesílání zpráv typu cloud zařízení s centrem IoT] [ lnk-c2d] ukazuje, jak k odesílání zpráv do vašeho zařízení z back end vašeho řešení.

Příklady dokončení začátku do konce řešení, které pomocí služby IoT Hub, najdete v sekci [akcelerátoru řešení Azure IoT vzdálené monitorování][lnk-suite].

Další informace o vývoji řešení službou IoT Hub, najdete v článku [Příručka vývojáře pro službu IoT Hub].

Další informace o směrování zpráv do služby IoT Hub, najdete v části [odesílat a přijímat zprávy službou IoT Hub][lnk-devguide-messaging].

<!-- Images. -->
<!-- TODO: UPDATE PICTURES -->
[simulateddevice]: ./media/iot-hub-java-java-process-d2c/runsimulateddevice.png
[readd2c]: ./media/iot-hub-java-java-process-d2c/runprocessinteractive.png
[readqueue]: ./media/iot-hub-java-java-process-d2c/runprocessd2c.png

[30]: ./media/iot-hub-java-java-process-d2c/click-endpoints.png
[31]: ./media/iot-hub-java-java-process-d2c/endpoint-creation.png
[32]: ./media/iot-hub-java-java-process-d2c/route-creation.png
[33]: ./media/iot-hub-java-java-process-d2c/fallback-route.png

<!-- Links -->

[lnk-sb-queues-java]: ../service-bus-messaging/service-bus-java-how-to-use-queues.md

[Azure Storage]: https://azure.microsoft.com/documentation/services/storage/
[Azure Service Bus]: https://azure.microsoft.com/documentation/services/service-bus/

[Příručka vývojáře pro službu IoT Hub]: iot-hub-devguide.md
[lnk-devguide-messaging]: iot-hub-devguide-messaging.md
[Začínáme se službou IoT Hub]: iot-hub-java-java-getstarted.md
[Azure střediska pro vývojáře IoT]: https://azure.microsoft.com/develop/iot
[přechodných chyb]: https://msdn.microsoft.com/library/hh675232.aspx

<!-- Links -->
[Přechodná chyba zpracování]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx

[lnk-c2d]: iot-hub-java-java-c2d.md
[lnk-suite]: https://azure.microsoft.com/documentation/suites/iot-suite/
[lnk-free-trial]: https://azure.microsoft.com/free/
