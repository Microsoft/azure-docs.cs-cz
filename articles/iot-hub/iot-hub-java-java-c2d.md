---
title: Zprávy typu cloud zařízení pomocí služby Azure IoT Hub (Java) | Dokumentace Microsoftu
description: Jak odesílat zprávy typu cloud zařízení na zařízení ze služby Azure IoT hub pomocí sad Azure IoT SDK pro Javu. Upravíte aplikaci simulovaného zařízení pro příjem zpráv z cloudu do zařízení a upravovat back endové aplikace odesílat zprávy typu cloud zařízení.
author: dominicbetts
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.devlang: java
ms.topic: conceptual
ms.date: 06/28/2017
ms.author: dobett
ms.openlocfilehash: e4d0df28449a2e50e72b192f0118a8eae3325d15
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/26/2018
ms.locfileid: "47220185"
---
# <a name="send-cloud-to-device-messages-with-iot-hub-java"></a>Odesílání zpráv typu cloud zařízení pomocí služby IoT Hub (Java)
[!INCLUDE [iot-hub-selector-c2d](../../includes/iot-hub-selector-c2d.md)]

Azure IoT Hub je plně spravovaná služba, která pomáhá povolit spolehlivou a zabezpečenou obousměrnou komunikaci mezi miliony zařízení a back-endu řešení. [Začínáme se službou IoT Hub] kurz ukazuje postupy při vytvoření služby IoT hub, zřídit identitu zařízení v něm a kódu aplikace simulovaného zařízení, která odesílá zprávy typu zařízení cloud.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Tento kurz vychází [Začínáme se službou IoT Hub]. To se dozvíte, jak do:

* Z back-end vašeho řešení odesílat zprávy typu cloud zařízení na jediné zařízení prostřednictvím služby IoT Hub.
* Příjem zpráv typu cloud zařízení na zařízení.
* Z back-end vašeho řešení, požádat o doručení potvrzení (*zpětnou vazbu*) pro zprávy odeslané do zařízení ze služby IoT Hub.

Můžete najít další informace o zprávy typu cloud zařízení v [Příručka vývojáře pro IoT Hub][IoT Hub developer guide - C2D].

Na konci tohoto kurzu spusťte dvě konzolové aplikace Java:

* **simulated-device**, upravenou verzi aplikaci vytvořenou v [Začínáme se službou IoT Hub], který se připojí ke službě IoT hub a přijímá zprávy typu cloud zařízení.
* **odeslání c2d-messages**, která odesílá zprávy typu cloud zařízení do aplikace simulovaného zařízení prostřednictvím služby IoT Hub a potom přijímá jeho doručení potvrzení.

> [!NOTE]
> IoT Hub má sady SDK podporují mnoho platforem zařízení a jazyky (včetně C, Javy a JavaScriptu) prostřednictvím sady SDK pro zařízení Azure IoT. Podrobné pokyny o tom, jak připojit zařízení ke kódu v tomto kurzu a obecně pro službu Azure IoT Hub, najdete v článku [centrum pro vývojáře Azure IoT].

Pro absolvování tohoto kurzu potřebujete:

* Kompletní funkční verzi [Začínáme se službou IoT Hub](quickstart-send-telemetry-java.md) nebo [zpráv typu zařízení cloud procesu IoT Hub](tutorial-routing.md) kurzu.
* Nejnovější [Java SE Development Kit 8](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)
* [Maven 3](https://maven.apache.org/install.html)
* Aktivní účet Azure. (Pokud účet nemáte, můžete si během několika minut vytvořit [bezplatný účet][lnk-free-trial].)

## <a name="receive-messages-in-the-simulated-device-app"></a>Příjem zpráv v aplikaci simulovaného zařízení

V této části upravíte aplikaci simulovaného zařízení, kterou jste vytvořili v [Začínáme se službou IoT Hub] pro příjem zpráv typu cloud zařízení ze služby IoT hub.

1. Pomocí textového editoru otevřete soubor simulated-device\src\main\java\com\mycompany\app\App.java.

2. Přidejte následující **MessageCallback** třídu jako vnořené třídy uvnitř **aplikace** třídy. **Provést** metoda je voláno, když zařízení přijímá zprávy ze služby IoT Hub. V tomto příkladu zařízení vždy oznámí služby IoT hub dokončení zprávy:

    ```java
    private static class AppMessageCallback implements MessageCallback {
      public IotHubMessageResult execute(Message msg, Object context) {
        System.out.println("Received message from hub: "
          + new String(msg.getBytes(), Message.DEFAULT_IOTHUB_MESSAGE_CHARSET));
    
        return IotHubMessageResult.COMPLETE;
      }
    }
    ```
3. Upravit **hlavní** metodu pro vytvoření **AppMessageCallback** instance a volání **setMessageCallback** metoda předtím, než se otevře klienta následujícím způsobem:

    ```java
    client = new DeviceClient(connString, protocol);
   
    MessageCallback callback = new AppMessageCallback();
    client.setMessageCallback(callback, null);
    client.open();
    ```

    > [!NOTE]
    > Pokud pomocí protokolu HTTPS místo protokol MQTT nebo AMQP k přenosu **DeviceClient** instance kontroluje pro zprávy ze služby IoT Hub zřídka (méně než každých 25 minut). Další informace o rozdílech mezi podpora MQTT, AMQP a protokolu HTTPS a omezení šířky pásma služby IoT Hub, najdete v článku [Příručka vývojáře pro IoT Hub][IoT Hub developer guide - C2D].

4. Aplikaci **simulated-device** pomocí nástroje Maven sestavíte tak, že v příkazovém řádku ve složce simulated-device spustíte následující příkaz:

    ```cmd/sh
    mvn clean package -DskipTests
    ```

## <a name="send-a-cloud-to-device-message"></a>Odeslání zprávy typu cloud zařízení

V této části vytvoříte konzolovou aplikaci Java, která odesílá zprávy typu cloud zařízení do aplikace simulovaného zařízení. ID zařízení, zařízení, které jste přidali v kroku budete potřebovat [Začínáme se službou IoT Hub] kurzu. Budete potřebovat připojovací řetězec služby IoT Hub pro vaše centrum, které můžete najít v [Azure Portal].

1. Vytvořte projekt Maven s názvem **odesílat c2d-zprávy** pomocí následujícího příkazu na příkazovém řádku. Všimněte si, že tento příkaz je jeden dlouhý příkaz:

    ```cmd/sh
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=send-c2d-messages -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

2. Na příkazovém řádku přejděte do nové složky odesílat c2d-zprávy.

3. Pomocí textového editoru otevřete soubor pom.xml ve složce odesílat c2d-zprávy a přidejte následující závislost **závislosti** uzlu. Přidat závislost vám umožní použít **iothub-java-service-client** balíčku v aplikaci komunikovat s vaší službou IoT hub:

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

7. Přidejte následující proměnné na úrovni třídy pro **aplikace** třídy, nahraďte **{yourhubconnectionstring}** a **{yourdeviceid}** hodnotami, které vaše uvedené výše:

    ```java
    private static final String connectionString = "{yourhubconnectionstring}";
    private static final String deviceId = "{yourdeviceid}";
    private static final IotHubServiceClientProtocol protocol = IotHubServiceClientProtocol.AMQPS;
    ```

8. Nahradit **hlavní** metodu s následujícím kódem. Tento kód se připojí ke službě IoT hub, odešle zprávu do vašeho zařízení a pak čeká na potvrzení, že zařízení přijme a zpracuje zprávy:
   
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
    > Pro saké pro zjednodušení tento kurz neimplementuje žádné zásady opakování. V produkčním kódu by měly implementovat zásady opakování (například exponenciální regresí), jak je navrženo v článku [zpracování přechodných chyb](/azure/architecture/best-practices/transient-faults).


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

2. Na příkazovém řádku ve složce odesílat c2d-zprávy spusťte následující příkaz k odeslání zprávy typu cloud zařízení a počkejte na potvrzení zpětnou vazbu:

    ```cmd/sh
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
    ```

    ![Spustit příkaz, který odesílá zprávy typu cloud zařízení][img-send-command]

## <a name="next-steps"></a>Další postup

V tomto kurzu jste zjistili, jak posílat a přijímat zprávy typu cloud zařízení. 

Příklady kompletní řešení začátku do konce, které používají služby IoT Hub najdete v tématu [Akcelerátor řešení Azure IoT vzdálené monitorování].

Další informace o vývoji řešení s využitím služby IoT Hub, najdete v článku [Příručka vývojáře pro IoT Hub].

<!-- Images -->
[img-simulated-device]: media/iot-hub-java-java-c2d/receivec2d.png
[img-send-command]:  media/iot-hub-java-java-c2d/sendc2d.png
<!-- Links -->

[Začínáme se službou IoT Hub]: quickstart-send-telemetry-java.md
[IoT Hub developer guide - C2D]: iot-hub-devguide-messaging.md
[Příručka vývojáře pro IoT Hub]: iot-hub-devguide.md
[Centrum pro vývojáře Azure IoT]: http://azure.microsoft.com/develop/iot
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdk-java
[Azure Portal]: https://portal.azure.com
[Akcelerátor řešení Azure IoT vzdálené monitorování]: https://azure.microsoft.com/documentation/suites/iot-suite/
[lnk-maven-service-search]: http://search.maven.org/#search%7Cga%7C1%7Ca%3A%22iot-service-client%22%20g%3A%22com.microsoft.azure.sdk.iot%22