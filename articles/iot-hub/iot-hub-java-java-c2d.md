---
title: Zprávy typu cloud zařízení s Azure IoT Hub (Java) | Microsoft Docs
description: Postupy pro odesílání zpráv typu cloud zařízení pro zařízení ze služby Azure IoT hub pro jazyk Java pomocí sady SDK služby Azure IoT. Můžete upravit aplikaci simulovaného zařízení příjem zpráv typu cloud zařízení a úpravě back-end aplikace k odesílání zpráv typu cloud zařízení.
services: iot-hub
documentationcenter: java
author: dominicbetts
manager: timlt
editor: ''
ms.assetid: 7f785ea8-e7c2-40c5-87ef-96525e9b9e1e
ms.service: iot-hub
ms.devlang: java
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/28/2017
ms.author: dobett
ms.openlocfilehash: 4c748f01a83c7f569953f7c7f2fc5631ca0efd17
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2018
---
# <a name="send-cloud-to-device-messages-with-iot-hub-java"></a>Odesílání zpráv typu cloud zařízení službou IoT Hub (Java)
[!INCLUDE [iot-hub-selector-c2d](../../includes/iot-hub-selector-c2d.md)]

Azure IoT Hub je plně spravovaná služba, která pomáhá povolit spolehlivou a zabezpečenou obousměrnou komunikaci mezi miliony zařízení a back-end řešení. [Začínáme se službou IoT Hub] kurz ukazuje, jak k vytvoření služby IoT hub, zřídit identitu zařízení v ní a kódu aplikaci ze simulovaného zařízení, která odesílá zprávy typu zařízení cloud.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

V tomto kurzu vychází [Začínáme se službou IoT Hub]. Jak ukazuje na:

* Z back-end vašeho řešení odesílání zpráv typu cloud zařízení na jedno zařízení prostřednictvím služby IoT Hub.
* Příjem zpráv typu cloud zařízení na zařízení.
* Z back-end vašeho řešení, žádosti o potvrzení o doručení (*zpětné vazby*) pro zprávy odeslané do zařízení ze služby IoT Hub.

Můžete najít další informace o zprávy typu cloud zařízení v [Příručka vývojáře pro službu IoT Hub][IoT Hub developer guide - C2D].

Na konci tohoto kurzu můžete spustit dvě aplikace konzoly v jazyce Java:

* **simulated-device**, upravenou verzi aplikace vytvořená v [Začínáme se službou IoT Hub], který se připojuje ke službě IoT hub a přijímá zprávy typu cloud zařízení.
* **Send-c2d-zprávy**, který odešle zprávu cloud zařízení na aplikaci simulovaného zařízení prostřednictvím služby IoT Hub a pak obdrží jeho potvrzení o doručení.

> [!NOTE]
> IoT Hub je podpora v sadě SDK pro mnoho zařízení platformy a jazyky (například C, Javy a JavaScriptu) prostřednictvím SDK pro zařízení Azure IoT. Podrobné pokyny o tom, jak připojit zařízení ke kódu v tomto kurzu a obecně do služby Azure IoT Hub, najdete v článku [Azure střediska pro vývojáře IoT].

Pro absolvování tohoto kurzu potřebujete:

* Úplnou verzi práci [Začínáme se službou IoT Hub](iot-hub-java-java-getstarted.md) nebo [zprávy typu zařízení cloud proces IoT Hub](iot-hub-java-java-process-d2c.md) kurzu.
* Nejnovější [Java SE Development Kit 8](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)
* [Maven 3](https://maven.apache.org/install.html)
* Aktivní účet Azure. (Pokud účet nemáte, můžete si během několika minut vytvořit [bezplatný účet][lnk-free-trial].)

## <a name="receive-messages-in-the-simulated-device-app"></a>Příjem zpráv v aplikaci simulovaného zařízení

V této části upravíte aplikaci simulovaného zařízení, kterou jste vytvořili v [Začínáme se službou IoT Hub] pro příjem zpráv typu cloud zařízení ze služby IoT hub.

1. Pomocí textového editoru otevřete soubor simulated-device\src\main\java\com\mycompany\app\App.java.

2. Přidejte následující **MessageCallback** jako vnořené třída uvnitř **aplikace** třídy. **Provést** metoda je volána, když zařízení obdrží zprávu ze služby IoT Hub. V tomto příkladu zařízení vždy upozorní služby IoT hub splnění zpráva:

    ```java
    private static class AppMessageCallback implements MessageCallback {
      public IotHubMessageResult execute(Message msg, Object context) {
        System.out.println("Received message from hub: "
          + new String(msg.getBytes(), Message.DEFAULT_IOTHUB_MESSAGE_CHARSET));
    
        return IotHubMessageResult.COMPLETE;
      }
    }
    ```
3. Změnit **hlavní** metodu pro vytvoření **AppMessageCallback** instance a volání **setMessageCallback** metoda před jeho otevřením klienta následujícím způsobem:

    ```java
    client = new DeviceClient(connString, protocol);
   
    MessageCallback callback = new AppMessageCallback();
    client.setMessageCallback(callback, null);
    client.open();
    ```

    > [!NOTE]
    > Pokud použijete protokol HTTPS místo MQTT nebo AMQP jako přenos, **DeviceClient** instance vyhledává zprávy ze služby IoT Hub zřídka (méně než každých 25 minut). Další informace o rozdílech mezi podpora MQTT, AMQP a HTTPS a omezení služby IoT Hub, najdete v článku [Příručka vývojáře pro službu IoT Hub][IoT Hub developer guide - C2D].

4. Aplikaci **simulated-device** pomocí nástroje Maven sestavíte tak, že v příkazovém řádku ve složce simulated-device spustíte následující příkaz:

    ```cmd/sh
    mvn clean package -DskipTests
    ```

## <a name="send-a-cloud-to-device-message"></a>Odeslání zprávy typu cloud zařízení

V této části vytvoříte konzolovou aplikaci Java, která posílání zpráv typu cloud zařízení do aplikaci simulovaného zařízení. Je třeba ID zařízení v zařízení, které jste přidali v [Začínáme se službou IoT Hub] kurzu. Připojovací řetězec služby IoT Hub pro vaše centrum, které můžete najít v musíte taky [portál Azure].

1. Vytvořte projekt Maven s názvem **send-c2d-zprávy** pomocí následujícího příkazu na příkazovém řádku. Poznámka: Tento příkaz je jeden dlouhý příkaz:

    ```cmd/sh
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=send-c2d-messages -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

2. Na příkazovém řádku přejděte do nové složky send-c2d-zprávy.

3. Pomocí textového editoru otevřete soubor pom.xml ve složce send-c2d-zprávy a přidejte následující závislost na **závislosti** uzlu. Přidání závislost umožňuje používat **iothub-java-service-client** balíček ve vaší aplikaci ke komunikaci s vaší služby IoT hub:

    ```xml
    <dependency>
      <groupId>com.microsoft.azure.sdk.iot</groupId>
      <artifactId>iot-service-client</artifactId>
      <version>1.7.23</version>
    </dependency>
    ```

    > [!NOTE]
    > Můžete vyhledat nejnovější verzi **iot-service-client** pomocí [vyhledávání Maven][lnk-maven-service-search].

4. Soubor pom.xml uložte a zavřete.

5. Pomocí textového editoru otevřete soubor send-c2d-messages\src\main\java\com\mycompany\app\App.java.

6. Do souboru přidejte následující příkazy pro **import**:

    ```java
    import com.microsoft.azure.sdk.iot.service.*;
    import java.io.IOException;
    import java.net.URISyntaxException;
    ```

7. Přidejte následující proměnné na úrovni třídy k **aplikace** třídy a nahraďte **{yourhubconnectionstring}** a **{yourdeviceid}** s hodnotami jste si dříve poznamenali:

    ```java
    private static final String connectionString = "{yourhubconnectionstring}";
    private static final String deviceId = "{yourdeviceid}";
    private static final IotHubServiceClientProtocol protocol = IotHubServiceClientProtocol.AMQPS;
    ```

8. Nahraďte **hlavní** metoda následujícím kódem. Tento kód se připojí ke službě IoT hub, odešle zprávu do zařízení a pak čeká na potvrzení, že zařízení přijme a zpracuje zprávy:
   
    ```java
    public static void main(String[] args) throws IOException,
        URISyntaxException, Exception {
      ServiceClient serviceClient = ServiceClient.createFromConnectionString(
        connectionString, protocol);
   
      if (serviceClient != null) {
        serviceClient.open();
        FeedbackReceiver feedbackReceiver = serviceClient
          .getFeedbackReceiver();
        if (feedbackReceiver != null) feedbackReceiver.open();
   
        Message messageToSend = new Message("Cloud to device message.");
        messageToSend.setDeliveryAcknowledgement(DeliveryAcknowledgement.Full);
   
        serviceClient.send(deviceId, messageToSend);
        System.out.println("Message sent to device");
   
        FeedbackBatch feedbackBatch = feedbackReceiver.receive(10000);
        if (feedbackBatch != null) {
          System.out.println("Message feedback received, feedback time: "
            + feedbackBatch.getEnqueuedTimeUtc().toString());
        }
   
        if (feedbackReceiver != null) feedbackReceiver.close();
        serviceClient.close();
      }
    }
    ```

    > [!NOTE]
    > Pro saké na jednoduchost tento kurz neimplementuje žádné zásady opakování. V produkčním kódu, měli byste implementovat zásady opakování (například exponenciálního omezení rychlosti), dle pokynů v článku na webu MSDN [přechodných chyb].


9. Aplikaci **simulated-device** pomocí nástroje Maven sestavíte tak, že v příkazovém řádku ve složce simulated-device spustíte následující příkaz:

    ```cmd/sh
    mvn clean package -DskipTests
    ```

## <a name="run-the-applications"></a>Spuštění aplikací

Nyní můžete spustit aplikace.

1. Na příkazovém řádku ve složce simulated-device spusťte následující příkaz, aby se začala odesílat telemetrická data do služby IoT hub a přijímat zprávy typu cloud zařízení odeslané z rozbočovače:

    ```cmd/sh
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App" 
    ```

    ![Spusťte aplikaci simulovaného zařízení][img-simulated-device]

2. Na příkazovém řádku ve složce send-c2d-zprávy spusťte následující příkaz k odeslání zprávy typu cloud zařízení a počkat na potvrzení o názor:

    ```cmd/sh
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
    ```

    ![Spusťte příkaz k odeslání zprávy typu cloud zařízení][img-send-command]

## <a name="next-steps"></a>Další postup

V tomto kurzu jste zjistili, jak odesílat a přijímat zprávy typu cloud zařízení. 

Příklady dokončení začátku do konce řešení, které pomocí služby IoT Hub, najdete v sekci [akcelerátoru řešení Azure IoT vzdálené monitorování].

Další informace o vývoji řešení službou IoT Hub, najdete v článku [Příručka vývojáře pro službu IoT Hub].

<!-- Images -->
[img-simulated-device]: media/iot-hub-java-java-c2d/receivec2d.png
[img-send-command]:  media/iot-hub-java-java-c2d/sendc2d.png
<!-- Links -->

[Začínáme se službou IoT Hub]: iot-hub-java-java-getstarted.md
[IoT Hub developer guide - C2D]: iot-hub-devguide-messaging.md
[Příručka vývojáře pro službu IoT Hub]: iot-hub-devguide.md
[Azure střediska pro vývojáře IoT]: http://azure.microsoft.com/develop/iot
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdk-java
[přechodných chyb]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
[portál Azure]: https://portal.azure.com
[akcelerátoru řešení Azure IoT vzdálené monitorování]: https://azure.microsoft.com/documentation/suites/iot-suite/
[lnk-maven-service-search]: http://search.maven.org/#search%7Cga%7C1%7Ca%3A%22iot-service-client%22%20g%3A%22com.microsoft.azure.sdk.iot%22