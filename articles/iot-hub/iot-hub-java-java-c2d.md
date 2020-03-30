---
title: Zprávy z cloudu na zařízení s Azure IoT Hub (Java) | Dokumenty společnosti Microsoft
description: Jak odesílat zprávy z cloudu na zařízení do zařízení z centra Azure IoT pomocí sad Azure IoT SDK pro Jazyk Java. Můžete upravit simulované zařízení aplikace přijímat zprávy z cloudu na zařízení a upravit back-end aplikace pro odesílání zpráv z cloudu na zařízení.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.devlang: java
ms.topic: conceptual
ms.date: 06/28/2017
ms.openlocfilehash: 518f8057f222a628f8c3cd077cad4a7362e2cac8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77110823"
---
# <a name="send-cloud-to-device-messages-with-iot-hub-java"></a>Odesílání zpráv z cloudu na zařízení pomocí služby IoT Hub (Java)

[!INCLUDE [iot-hub-selector-c2d](../../includes/iot-hub-selector-c2d.md)]

Azure IoT Hub je plně spravovaná služba, která pomáhá umožnit spolehlivou a zabezpečenou obousměrnou komunikaci mezi miliony zařízení a back-endem řešení. [Odeslat telemetrii ze zařízení do služby IoT hub](quickstart-send-telemetry-java.md) rychlý start ukazuje, jak vytvořit službu IoT hub, zřízení identity zařízení v něm a kód simulované zařízení aplikace, která odesílá zprávy zařízení cloud.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Tento kurz staví na [odesílání telemetrie ze zařízení do služby IoT hub](quickstart-send-telemetry-java.md). To vám ukáže, jak to udělat následující:

* Z back-endu vašeho řešení můžete odesílat zprávy z cloudu na zařízení do jednoho zařízení prostřednictvím služby IoT Hub.

* Příjem zpráv z cloudu na zařízení na zařízení.

* Z back-endu vašeho řešení požádejte o potvrzení doručení *(zpětnou vazbu)* pro zprávy odeslané do zařízení ze služby IoT Hub.

Další informace o [zprávách mezi cloudy najdete v průvodci vývojáři služby IoT Hub](iot-hub-devguide-messaging.md).

Na konci tohoto kurzu spustíte dvě konzolové aplikace Java:

* **simulované zařízení**, upravená verze aplikace vytvořená v [aplikaci Send telemetrie ze zařízení do centra IoT](quickstart-send-telemetry-java.md)hub , která se připojuje k centru IoT hub a přijímá zprávy z cloudu na zařízení.

* **send-c2d-messages**, který odešle zprávu z cloudu na zařízení do aplikace simulované zařízení prostřednictvím služby IoT Hub a pak obdrží potvrzení o doručení.

> [!NOTE]
> IoT Hub má podporu sady SDK pro mnoho platforem a jazyků zařízení (včetně C, Java, Pythonu a Javascriptu) prostřednictvím sad SDK zařízení Azure IoT. Podrobné pokyny, jak připojit zařízení ke kódu tohoto kurzu a obecně k azure iot hubu, najdete v [tématu Azure IoT Developer Center](https://azure.microsoft.com/develop/iot).

## <a name="prerequisites"></a>Požadavky

* Kompletní pracovní verze [odeslat telemetrii ze zařízení do](quickstart-send-telemetry-java.md) služby Rychlé spuštění služby IoT hub nebo konfigurovat směrování zpráv pomocí služby [IoT Hub](tutorial-routing.md) kurzu.

* [Java SE Vývojová sada 8](https://docs.microsoft.com/java/azure/jdk/?view=azure-java-stable). Ujistěte se, že jste v části **Dlouhodobá podpora** vybrali **Javu 8,** abyste se dostali ke stažení pro JDK 8.

* [Maven 3](https://maven.apache.org/download.cgi)

* Aktivní účet Azure. Pokud účet nemáte, můžete si během několika minut vytvořit [bezplatný účet](https://azure.microsoft.com/pricing/free-trial/).

* Zkontrolujte, zda je v bráně firewall otevřený port 8883. Ukázka zařízení v tomto článku používá protokol MQTT, který komunikuje přes port 8883. Tento port může být blokován v některých prostředích podnikové a vzdělávací sítě. Další informace a způsoby, jak tento problém vyřešit, najdete [v tématu připojení k centru IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

## <a name="receive-messages-in-the-simulated-device-app"></a>Příjem zpráv v aplikaci pro simulované zařízení

V této části upravíte aplikaci simulovaných zařízení, kterou jste vytvořili v [aplikaci Send telemetrie ze zařízení do centra IoT,](quickstart-send-telemetry-java.md) abyste přijímali zprávy mezi cloudy z centra IoT.

1. Pomocí textového editoru otevřete soubor simulated-device\src\main\java\com\mycompany\app\App.java.

2. Přidejte následující třídu **MessageCallback** jako vnořenou třídu uvnitř třídy **App.** Metoda **spuštění** je vyvolána, když zařízení obdrží zprávu z centra IoT Hub. V tomto příkladu zařízení vždy upozorní službu IoT hub, že zprávu dokončilo:

    ```java
    private static class AppMessageCallback implements MessageCallback {
      public IotHubMessageResult execute(Message msg, Object context) {
        System.out.println("Received message from hub: "
          + new String(msg.getBytes(), Message.DEFAULT_IOTHUB_MESSAGE_CHARSET));

        return IotHubMessageResult.COMPLETE;
      }
    }
    ```

3. Upravte **hlavní** metodu pro vytvoření instance **AppMessageCallback** a zavolejte metodu **setMessageCallback** před tím, než otevře klienta následujícím způsobem:

    ```java
    client = new DeviceClient(connString, protocol);

    MessageCallback callback = new AppMessageCallback();
    client.setMessageCallback(callback, null);
    client.open();
    ```

    > [!NOTE]
    > Pokud používáte https místo MQTT nebo AMQP jako přenos, **deviceclient** instance kontroluje zprávy z ioT hubu zřídka (méně než každých 25 minut). Další informace o rozdílech mezi podporou MQTT, AMQP a HTTPS a omezením služby IoT Hub naleznete v [části zasílání zpráv v průvodci vývojáři služby IoT Hub](iot-hub-devguide-messaging.md).

4. Aplikaci **simulated-device** pomocí nástroje Maven sestavíte tak, že v příkazovém řádku ve složce simulated-device spustíte následující příkaz:

    ```cmd/sh
    mvn clean package -DskipTests
    ```

## <a name="get-the-iot-hub-connection-string"></a>Získání připojovacího řetězce centra IoT hub

V tomto článku vytvoříte back-endovou službu pro odesílání zpráv mezi cloudy prostřednictvím služby IoT hub, kterou jste vytvořili v [aplikaci Send telemetrie ze zařízení do služby IoT hub](quickstart-send-telemetry-java.md). K odesílání zpráv z cloudu na zařízení potřebuje vaše služba oprávnění **k připojení služby.** Ve výchozím nastavení je každé centrum IoT Hub vytvořeno pomocí zásady sdíleného přístupu s názvem **služba,** která uděluje toto oprávnění.

[!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

## <a name="send-a-cloud-to-device-message"></a>Odeslání zprávy z cloudu na zařízení

V této části vytvoříte konzolovou aplikaci Java, která odesílá zprávy z cloudu na zařízení do aplikace simulovaných zařízení. Potřebujete ID zařízení zařízení, které jste přidali v [odesílání telemetrie ze zařízení do rychlého startu služby IoT hub.](quickstart-send-telemetry-java.md) Potřebujete také připojovací řetězec centra IoT, který jste dříve zkopírovali v [připojovacím řetězci Centra IoT](#get-the-iot-hub-connection-string).

1. Vytvořte projekt Maven s názvem **send-c2d-messages** pomocí následujícího příkazu na příkazovém řádku. Všimněte si, že tento příkaz je jeden, dlouhý příkaz:

    ```cmd/sh
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=send-c2d-messages -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

2. Na příkazovém řádku přejděte do nové složky send-c2d-messages.

3. Pomocí textového editoru otevřete soubor pom.xml ve složce send-c2d-messages a přidejte do uzlu **závislostí** následující závislost. Přidání závislosti umožňuje použít balíček **iothub-java-service-client** ve vaší aplikaci ke komunikaci se službou IoT hub:

    ```xml
    <dependency>
      <groupId>com.microsoft.azure.sdk.iot</groupId>
      <artifactId>iot-service-client</artifactId>
      <version>1.7.23</version>
    </dependency>
    ```

    > [!NOTE]
    > Můžete vyhledat nejnovější verzi **iot-service-client** pomocí [vyhledávání Maven](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22iot-service-client%22%20g%3A%22com.microsoft.azure.sdk.iot%22).

4. Soubor pom.xml uložte a zavřete.

5. Pomocí textového editoru otevřete soubor send-c2d-messages\src\main\java\com\mycompany\app\App.java.

6. Do souboru přidejte následující příkazy pro **import**:

    ```java
    import com.microsoft.azure.sdk.iot.service.*;
    import java.io.IOException;
    import java.net.URISyntaxException;
    ```

7. Přidejte do třídy **App** následující proměnné na úrovni třídy, které nahradí **{yourhubconnectionstring}** a **{yourdeviceid}** hodnotami, které jste si poznamenali dříve:

    ```java
    private static final String connectionString = "{yourhubconnectionstring}";
    private static final String deviceId = "{yourdeviceid}";
    private static final IotHubServiceClientProtocol protocol =    
        IotHubServiceClientProtocol.AMQPS;
    ```

8. Nahraďte **hlavní** metodu následujícím kódem. Tento kód se připojí k centru IoT hub, odešle zprávu do vašeho zařízení a pak čeká na potvrzení, že zařízení přijalo a zpracovalo zprávu:

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
    > Pro jednoduchost tento kurz neimplementuje žádné zásady opakování. V produkčním kódu byste měli implementovat zásady opakování (například exponenciální backoff), jak je navrženo v článku [Přechodné zpracování chyb](/azure/architecture/best-practices/transient-faults).

9. Aplikaci **simulated-device** pomocí nástroje Maven sestavíte tak, že v příkazovém řádku ve složce simulated-device spustíte následující příkaz:

    ```cmd/sh
    mvn clean package -DskipTests
    ```

## <a name="run-the-applications"></a>Spuštění aplikací

Nyní můžete spustit aplikace.

1. Na příkazovém řádku ve složce simulovaných zařízení spusťte následující příkaz, abyste začali odesílat telemetrii do centra IoT hub a naslouchali zprávám z cloudu na zařízení odeslaným z vašeho centra:

    ```cmd/sh
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
    ```

    ![Spuštění aplikace simulované zařízení](./media/iot-hub-java-java-c2d/receivec2d.png)

2. Na příkazovém řádku ve složce send-c2d-messages spusťte následující příkaz, který odešle zprávu z cloudu na zařízení a počká na potvrzení zpětné vazby:

    ```cmd/sh
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
    ```

    ![Spuštění příkazu pro odeslání zprávy z cloudu na zařízení](media/iot-hub-java-java-c2d/sendc2d.png)

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili, jak odesílat a přijímat zprávy z cloudu na zařízení.

Příklady kompletních komplexních řešení, která používají službu IoT Hub, najdete v [tématu Akcelerátory řešení Azure IoT](https://azure.microsoft.com/documentation/suites/iot-suite/).

Další informace o vývoji řešení pomocí služby IoT Hub najdete v [průvodci vývojáři služby IoT Hub](iot-hub-devguide.md).
