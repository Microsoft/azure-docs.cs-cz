---
title: Zprávy z cloudu na zařízení pomocí Azure IoT Hub (Java) | Microsoft Docs
description: Postup posílání zpráv z cloudu na zařízení ze služby Azure IoT Hub pomocí sad SDK Azure IoT pro jazyk Java. Aplikaci simulovaného zařízení upravíte tak, aby přijímala zprávy z cloudu na zařízení a upravila back-end aplikaci pro posílání zpráv z cloudu na zařízení.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.devlang: java
ms.topic: conceptual
ms.date: 06/28/2017
ms.custom:
- amqp
- mqtt
- devx-track-java
ms.openlocfilehash: 5ae1850add94d83278b0fe1905dfa6e53c71fc8e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "102217886"
---
# <a name="send-cloud-to-device-messages-with-iot-hub-java"></a>Posílání zpráv z cloudu na zařízení pomocí IoT Hub (Java)

[!INCLUDE [iot-hub-selector-c2d](../../includes/iot-hub-selector-c2d.md)]

Azure IoT Hub je plně spravovaná služba, která pomáhá zajistit spolehlivou a zabezpečenou obousměrnou komunikaci mezi miliony zařízení a back-endu řešení. V rychlém startu [pro odeslání ze zařízení do služby IoT Hub](quickstart-send-telemetry-java.md) se dozvíte, jak vytvořit centrum IoT, zřídit v něm identitu zařízení a zakódovat aplikaci simulovaného zařízení, která odesílá zprávy typu zařízení-Cloud.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Tento kurz sestaví na [odeslání telemetrie ze zařízení do služby IoT Hub](quickstart-send-telemetry-java.md). Ukazuje, jak provést následující akce:

* Z back-endu vašeho řešení odesílají zprávy typu cloud-zařízení do jediného zařízení prostřednictvím IoT Hub.

* Příjem zpráv typu cloud-zařízení na zařízení.

* Z back-endu vašeho řešení požadavek na doručení zprávodesílaných do zařízení z IoT Hub.

Další informace o [zprávách z cloudu na zařízení najdete v příručce pro vývojáře IoT Hub](iot-hub-devguide-messaging.md).

Na konci tohoto kurzu spustíte dvě konzolové aplikace Java:

* **simulované zařízení –** upravená verze aplikace vytvořená v [rámci odesílání telemetrie ze zařízení do služby IoT Hub](quickstart-send-telemetry-java.md), která se připojuje ke službě IoT Hub a přijímá zprávy typu cloud-zařízení.

* **Send-C2D-Messages** odesílá zprávu typu cloud-zařízení do aplikace simulovaného zařízení prostřednictvím IoT Hub a potom obdrží potvrzení o doručení.

> [!NOTE]
> IoT Hub podporuje sadu SDK pro mnoho platforem a jazyků zařízení (včetně C, Java, Pythonu a JavaScriptu) prostřednictvím sad SDK pro zařízení Azure IoT. Podrobné pokyny, jak připojit zařízení k kódu tohoto kurzu a obecně k Azure IoT Hub, najdete v [centru pro vývojáře Azure IoT](https://azure.microsoft.com/develop/iot).

## <a name="prerequisites"></a>Předpoklady

* Kompletní pracovní verze z zařízení k rychlému zprovoznění služby [IoT Hub](quickstart-send-telemetry-java.md) nebo ke [konfiguraci směrování zpráv pomocí IoT Hub](tutorial-routing.md) kurzu.

* [Java se Development Kit 8](/java/azure/jdk/). Ujistěte se, že jste v části **Dlouhodobá podpora** vybrali **Java 8** , abyste se dostali ke stažení pro JDK 8.

* [Maven 3](https://maven.apache.org/download.cgi)

* Aktivní účet Azure. Pokud účet nemáte, můžete si během několika minut vytvořit [bezplatný účet](https://azure.microsoft.com/pricing/free-trial/).

* Ujistěte se, že je v bráně firewall otevřený port 8883. Ukázka zařízení v tomto článku používá protokol MQTT, který komunikuje přes port 8883. Tento port může být blokovaný v některých podnikových a vzdělávacích prostředích sítě. Další informace a způsoby, jak tento problém obejít, najdete v tématu [připojení k IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

## <a name="receive-messages-in-the-simulated-device-app"></a>Přijímání zpráv v aplikaci simulovaného zařízení

V této části upravíte aplikaci simulovaného zařízení, kterou jste vytvořili v části [odeslání telemetrie ze zařízení do služby IoT Hub](quickstart-send-telemetry-java.md) pro příjem zpráv z cloudu na zařízení ze služby IoT Hub.

1. Pomocí textového editoru otevřete soubor simulated-device\src\main\java\com\mycompany\app\App.java.

2. Do třídy **App** přidejte následující třídu **MessageCallback** jako vnořenou třídu. Metoda **Execute** je vyvolána, když zařízení obdrží zprávu od IoT Hub. V tomto příkladu zařízení vždy oznámí službě IoT Hub, že dokončila zprávu:

    ```java
    private static class AppMessageCallback implements MessageCallback {
      public IotHubMessageResult execute(Message msg, Object context) {
        System.out.println("Received message from hub: "
          + new String(msg.getBytes(), Message.DEFAULT_IOTHUB_MESSAGE_CHARSET));

        return IotHubMessageResult.COMPLETE;
      }
    }
    ```

3. Upravte metodu **Main** pro vytvoření instance **AppMessageCallback** a zavolejte metodu **setMessageCallback** předtím, než otevře klienta následujícím způsobem:

    ```java
    client = new DeviceClient(connString, protocol);

    MessageCallback callback = new AppMessageCallback();
    client.setMessageCallback(callback, null);
    client.open();
    ```

4. Aplikaci **simulated-device** pomocí nástroje Maven sestavíte tak, že v příkazovém řádku ve složce simulated-device spustíte následující příkaz:

    ```cmd/sh
    mvn clean package -DskipTests
    ```

`execute`Metoda ve `AppMessageCallback` třídě vrátí `IotHubMessageResult.COMPLETE` . Tím se upozorní IoT Hub, že zpráva byla úspěšně zpracována a že zprávu lze bezpečně odebrat z fronty zařízení. Zařízení by mělo tuto hodnotu vrátit, když se její zpracování úspěšně dokončí bez ohledu na to, který protokol používá.

V případě AMQP a HTTPS, ale ne MQTT, může zařízení také:

* Opuštění zprávy, která má za následek IoT Hub uchování zprávy ve frontě zařízení pro budoucí spotřebu.
* Odmítněte zprávu, která trvale odstraní zprávu z fronty zařízení.

Pokud dojde k nějakému problému, který zabrání zařízení v dokončení, zrušení nebo odmítnutí zprávy, IoT Hub po pevném časovém limitu zařadí do fronty zprávu pro doručení znovu. Z tohoto důvodu musí být logika zpracování zpráv v aplikaci zařízení *idempotentní*, aby se stejná zpráva zobrazovala vícekrát, což má stejný výsledek.

Podrobnější informace o tom, jak IoT Hub zpracovává zprávy typu cloud-zařízení, včetně podrobností o životním cyklu zpráv z cloudu na zařízení, najdete v tématu [posílání zpráv z cloudu na zařízení ze služby IoT Hub](iot-hub-devguide-messages-c2d.md).

> [!NOTE]
> Pokud jako přenos použijete HTTPS místo MQTT nebo AMQP, zkontroluje instance **DeviceClient** zprávy z IoT Hub zřídka (minimálně každých 25 minut). Další informace o rozdílech mezi podporou MQTT, AMQP a HTTPS najdete v tématu [pokyny pro komunikaci z cloudu na zařízení](iot-hub-devguide-c2d-guidance.md) a [Vyberte komunikační protokol](iot-hub-devguide-protocols.md).

## <a name="get-the-iot-hub-connection-string"></a>Získání připojovacího řetězce centra IoT Hub

V tomto článku vytvoříte back-end službu pro posílání zpráv z cloudu na zařízení prostřednictvím služby IoT Hub, kterou jste vytvořili v tématu [odeslání telemetrie ze zařízení do služby IoT Hub](quickstart-send-telemetry-java.md). K posílání zpráv z cloudu na zařízení potřebuje služba oprávnění **připojit** se. Ve výchozím nastavení se všechny IoT Hub vytvoří pomocí zásad sdíleného přístupu s názvem **Služba** , která toto oprávnění udělí.

[!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

## <a name="send-a-cloud-to-device-message"></a>Odeslání zprávy typu cloud-zařízení

V této části vytvoříte konzolovou aplikaci Java, která odesílá zprávy typu cloud-zařízení do aplikace simulovaného zařízení. Budete potřebovat ID zařízení, které jste přidali v části [odeslání telemetrie ze zařízení do rychlého startu služby IoT Hub](quickstart-send-telemetry-java.md) . Také potřebujete připojovací řetězec centra IoT, který jste zkopírovali dříve, v [části získání připojovacího řetězce centra IoT Hub](#get-the-iot-hub-connection-string).

1. Pomocí následujícího příkazu na příkazovém řádku vytvořte projekt Maven s názvem **Send-C2D-Messages** . Všimněte si, že tento příkaz představuje jeden dlouhý příkaz:

    ```cmd/sh
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=send-c2d-messages -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

2. Na příkazovém řádku přejděte do nové složky Send-C2D-Messages.

3. Pomocí textového editoru otevřete soubor pom.xml ve složce Send-C2D-Messages a přidejte následující závislost na uzel **závislosti** . Přidání závislosti vám umožní komunikovat se službou IoT Hub pomocí balíčku **iothub-Java-Service-Client** ve vaší aplikaci:

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

7. Do třídy **App** přidejte následující proměnné na úrovni třídy a nahraďte **{yourhubconnectionstring}** a **{yourdeviceid}** hodnotami, které jste si poznamenali dříve:

    ```java
    private static final String connectionString = "{yourhubconnectionstring}";
    private static final String deviceId = "{yourdeviceid}";
    private static final IotHubServiceClientProtocol protocol =    
        IotHubServiceClientProtocol.AMQPS;
    ```

8. Metodu **Main** nahraďte následujícím kódem. Tento kód se připojí ke službě IoT Hub, pošle do zařízení zprávu a pak počká na potvrzení, že zařízení přijalo a zpracovalo zprávu:

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
    > Pro zjednodušení tento kurz neimplementuje žádné zásady opakování. V produkčním kódu byste měli implementovat zásady opakování (například exponenciální omezení rychlosti), jak je navrženo v článku, [zpracování přechodných chyb](/azure/architecture/best-practices/transient-faults).

9. Aplikaci **simulated-device** pomocí nástroje Maven sestavíte tak, že v příkazovém řádku ve složce simulated-device spustíte následující příkaz:

    ```cmd/sh
    mvn clean package -DskipTests
    ```

## <a name="run-the-applications"></a>Spuštění aplikací

Nyní můžete spustit aplikace.

1. Na příkazovém řádku ve složce simulovaného zařízení spusťte následující příkaz, který zahájí odesílání telemetrie do služby IoT Hub a naslouchá zprávám typu cloud-zařízení odeslaných z vašeho centra:

    ```cmd/sh
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
    ```

    ![Spuštění aplikace simulovaného zařízení](./media/iot-hub-java-java-c2d/receivec2d.png)

2. Na příkazovém řádku ve složce Send-C2D-Messages spusťte následující příkaz, který odešle zprávu typu cloud-zařízení a počká na potvrzení zpětné vazby:

    ```cmd/sh
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
    ```

    ![Spuštění příkazu pro odeslání zprávy ze zařízení do cloudu](media/iot-hub-java-java-c2d/sendc2d.png)

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste zjistili, jak odesílat a přijímat zprávy z cloudu do zařízení.

Příklady kompletních řešení, která používají IoT Hub, najdete v tématu [akcelerátory řešení Azure IoT](https://azure.microsoft.com/documentation/suites/iot-suite/).

Další informace o vývoji řešení pomocí IoT Hub najdete v příručce pro [vývojáře IoT Hub](iot-hub-devguide.md).