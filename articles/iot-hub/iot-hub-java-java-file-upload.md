---
title: Nahrání souborů ze zařízení do Azure IoT Hub pomocí Java | Microsoft Docs
description: Postup nahrání souborů ze zařízení do cloudu pomocí sady SDK pro zařízení Azure IoT pro jazyk Java. Nahrané soubory se ukládají v kontejneru objektů BLOB služby Azure Storage.
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
ms.openlocfilehash: 3529361cacf0890b7c4752bbd745a9240020b4f3
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2021
ms.locfileid: "102217818"
---
# <a name="upload-files-from-your-device-to-the-cloud-with-iot-hub-java"></a>Nahrání souborů ze zařízení do cloudu pomocí IoT Hub (Java)

[!INCLUDE [iot-hub-file-upload-language-selector](../../includes/iot-hub-file-upload-language-selector.md)]

V tomto kurzu se seznámíte s kódem v tématu [posílání zpráv z cloudu do zařízení pomocí IoT Hub](iot-hub-java-java-c2d.md) kurzu, ve kterém se dozvíte, jak pomocí [možností nahrávání souborů IoT Hub](iot-hub-devguide-file-upload.md) nahrát soubor do [úložiště objektů BLOB v Azure](../storage/index.yml). V tomto kurzu získáte informace o následujících postupech:

* Bezpečně poskytněte zařízení s identifikátorem URI objektu blob Azure pro nahrání souboru.

* Pomocí IoT Hub oznámení o nahrávání souborů můžete aktivovat zpracování souboru v back-endu vaší aplikace.

[Odeslání telemetrie ze zařízení do rychlého startu centra IoT](quickstart-send-telemetry-java.md) a [posílání zpráv z cloudu na zařízení pomocí IoT Hub](iot-hub-java-java-c2d.md) kurzu zobrazuje základní funkce zasílání zpráv typu zařízení-Cloud a Cloud-zařízení IoT Hub. Kurz [Konfigurace směrování zpráv pomocí IoT Hub](tutorial-routing.md) popisuje způsob, jak spolehlivě ukládat zprávy typu zařízení-Cloud do úložiště objektů BLOB v Azure. V některých scénářích ale nemůžete snadno namapovat data, která zařízení odesílají do relativně malých zpráv ze zařízení do cloudu, které IoT Hub přijmout. Například:

* Velké soubory, které obsahují obrázky
* Videa
* Ukázka dat vibrací s vysokou frekvencí
* Některá forma předzpracovaných dat.

Tyto soubory jsou obvykle dávkově zpracovávány v cloudu pomocí nástrojů, jako je [Azure Data Factory](../data-factory/introduction.md) nebo zásobník [Hadoop](../hdinsight/index.yml) . Pokud potřebujete nakládat soubory ze zařízení, můžete i nadále používat zabezpečení a spolehlivost IoT Hub.

Na konci tohoto kurzu spustíte dvě konzolové aplikace Java:

* **simulovaná** verze aplikace vytvořená v kurzu [posílání zpráv z cloudu na zařízení pomocí IoT Hub]. Tato aplikace nahraje soubor do úložiště pomocí identifikátoru URI SAS, který poskytuje vaše služba IoT Hub.

* **čtení-soubor-nahrávání-oznámení**, které přijímá oznámení o nahrání souborů ze služby IoT Hub.

> [!NOTE]
> IoT Hub podporuje spoustu platforem a jazyků zařízení (včetně C, .NET a JavaScriptu) prostřednictvím sad SDK pro zařízení Azure IoT. Podrobné pokyny k připojení zařízení k Azure IoT Hub najdete v [centru pro vývojáře Azure IoT](https://azure.microsoft.com/develop/iot) .

[!INCLUDE [iot-hub-include-x509-ca-signed-file-upload-support-note](../../includes/iot-hub-include-x509-ca-signed-file-upload-support-note.md)]

## <a name="prerequisites"></a>Požadavky

* [Java se Development Kit 8](/java/azure/jdk/). Ujistěte se, že jste v části **Dlouhodobá podpora** vybrali **Java 8** , abyste se dostali ke stažení pro JDK 8.

* [Maven 3](https://maven.apache.org/download.cgi)

* Aktivní účet Azure. (Pokud účet nemáte, můžete si během několika minut vytvořit [bezplatný účet](https://azure.microsoft.com/pricing/free-trial/) .)

* Ujistěte se, že je v bráně firewall otevřený port 8883. Ukázka zařízení v tomto článku používá protokol MQTT, který komunikuje přes port 8883. Tento port může být blokovaný v některých podnikových a vzdělávacích prostředích sítě. Další informace a způsoby, jak tento problém obejít, najdete v tématu [připojení k IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

[!INCLUDE [iot-hub-associate-storage](../../includes/iot-hub-associate-storage.md)]

## <a name="upload-a-file-from-a-device-app"></a>Nahrání souboru z aplikace zařízení

V této části upravíte aplikaci zařízení, kterou jste vytvořili v části [posílání zpráv z cloudu na zařízení pomocí IoT Hub](iot-hub-java-java-c2d.md) pro nahrání souboru do centra IoT.

1. Zkopírujte soubor obrázku do `simulated-device` složky a přejmenujte ho `myimage.png` .

2. V textovém editoru otevřete `simulated-device\src\main\java\com\mycompany\app\App.java` soubor.

3. Přidejte do třídy **App** deklaraci proměnné:

    ```java
    private static String fileName = "myimage.png";
    ```

4. Chcete-li zpracovat zprávy zpětného volání stavu nahrávání souborů, přidejte do třídy **App** následující vnořenou třídu:

    ```java
    // Define a callback method to print status codes from IoT Hub.
    protected static class FileUploadStatusCallBack implements IotHubEventCallback {
      public void execute(IotHubStatusCode status, Object context) {
        System.out.println("IoT Hub responded to file upload for " + fileName
            + " operation with status " + status.name());
      }
    }
    ```

5. Pokud chcete nahrát obrázky do IoT Hub, přidejte do třídy **App** následující metodu pro nahrání imagí do IoT Hub:

    ```java
    // Use IoT Hub to upload a file asynchronously to Azure blob storage.
    private static void uploadFile(String fullFileName) throws FileNotFoundException, IOException
    {
      File file = new File(fullFileName);
      InputStream inputStream = new FileInputStream(file);
      long streamLength = file.length();

      client.uploadToBlobAsync(fileName, inputStream, streamLength, new FileUploadStatusCallBack(), null);
    }
    ```

6. Upravte metodu **Main** pro volání metody **uploadFile** , jak je znázorněno v následujícím fragmentu kódu:

    ```java
    client.open();

    try
    {
      // Get the filename and start the upload.
      String fullFileName = System.getProperty("user.dir") + File.separator + fileName;
      uploadFile(fullFileName);
      System.out.println("File upload started with success");
    }
    catch (Exception e)
    {
      System.out.println("Exception uploading file: " + e.getCause() + " \nERROR: " + e.getMessage());
    }

    MessageSender sender = new MessageSender();
    ```

7. Pomocí následujícího příkazu sestavte aplikaci **simulovaného zařízení** a vyhledejte chyby:

    ```cmd/sh
    mvn clean package -DskipTests
    ```

## <a name="get-the-iot-hub-connection-string"></a>Získání připojovacího řetězce centra IoT Hub

V tomto článku vytvoříte back-end službu pro příjem zpráv s oznámením o nahrávání souborů ze služby IoT Hub, kterou jste vytvořili v tématu [odeslání telemetrie ze zařízení do služby IoT Hub](quickstart-send-telemetry-java.md). Aby bylo možné přijímat zprávy s oznámením o nahrávání souborů, vaše služba potřebuje oprávnění **k připojení služby** . Ve výchozím nastavení se všechny IoT Hub vytvoří pomocí zásad sdíleného přístupu s názvem **Služba** , která toto oprávnění udělí.

[!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

## <a name="receive-a-file-upload-notification"></a>Doručení oznámení o nahrání souboru

V této části vytvoříte konzolovou aplikaci Java, která přijímá zprávy s oznámením o nahrávání souborů z IoT Hub.

1. Pomocí následujícího příkazu na příkazovém řádku vytvořte projekt Maven s názvem **Read-File-upload-Notification** . Všimněte si, že tento příkaz představuje jeden dlouhý příkaz:

    ```cmd/sh
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=read-file-upload-notification -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

2. Na příkazovém řádku přejděte do nové `read-file-upload-notification` složky.

3. Pomocí textového editoru otevřete `pom.xml` soubor ve `read-file-upload-notification` složce a přidejte následující závislost na uzel **závislosti** . Přidání závislosti vám umožní komunikovat se službou IoT Hub pomocí balíčku **iothub-Java-Service-Client** ve vaší aplikaci:

    ```xml
    <dependency>
      <groupId>com.microsoft.azure.sdk.iot</groupId>
      <artifactId>iot-service-client</artifactId>
      <version>1.7.23</version>
    </dependency>
    ```

    > [!NOTE]
    > Můžete vyhledat nejnovější verzi **iot-service-client** pomocí [vyhledávání Maven](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22iot-service-client%22%20g%3A%22com.microsoft.azure.sdk.iot%22).

4. Soubor uložte a zavřete `pom.xml` .

5. V textovém editoru otevřete `read-file-upload-notification\src\main\java\com\mycompany\app\App.java` soubor.

6. Do souboru přidejte následující příkazy pro **import**:

    ```java
    import com.microsoft.azure.sdk.iot.service.*;
    import java.io.IOException;
    import java.net.URISyntaxException;
    import java.util.concurrent.ExecutorService;
    import java.util.concurrent.Executors;
    ```

7. Do třídy **App** přidejte následující proměnné na úrovni třídy. Nahraďte `{Your IoT Hub connection string}` hodnotu zástupného symbolu připojovacím řetězcem IoT Hub, který jste zkopírovali dříve v [části získání připojovacího řetězce centra IoT](#get-the-iot-hub-connection-string):

    ```java
    private static final String connectionString = "{Your IoT Hub connection string}";
    private static final IotHubServiceClientProtocol protocol = IotHubServiceClientProtocol.AMQPS;
    private static FileUploadNotificationReceiver fileUploadNotificationReceiver = null;
    ```

8. Chcete-li vytisknout informace o nahrání souboru do konzoly, přidejte do třídy **aplikace** následující vnořenou třídu:

    ```java
    // Create a thread to receive file upload notifications.
    private static class ShowFileUploadNotifications implements Runnable {
      public void run() {
        try {
          while (true) {
            System.out.println("Receive file upload notifications...");
            FileUploadNotification fileUploadNotification = fileUploadNotificationReceiver.receive();
            if (fileUploadNotification != null) {
              System.out.println("File Upload notification received");
              System.out.println("Device Id : " + fileUploadNotification.getDeviceId());
              System.out.println("Blob Uri: " + fileUploadNotification.getBlobUri());
              System.out.println("Blob Name: " + fileUploadNotification.getBlobName());
              System.out.println("Last Updated : " + fileUploadNotification.getLastUpdatedTimeDate());
              System.out.println("Blob Size (Bytes): " + fileUploadNotification.getBlobSizeInBytes());
              System.out.println("Enqueued Time: " + fileUploadNotification.getEnqueuedTimeUtcDate());
            }
          }
        } catch (Exception ex) {
          System.out.println("Exception reading reported properties: " + ex.getMessage());
        }
      }
    }
    ```

9. Chcete-li spustit vlákno, které naslouchá oznámením o nahrávání souborů, přidejte do metody **Main** následující kód:

    ```java
    public static void main(String[] args) throws IOException, URISyntaxException, Exception {
      ServiceClient serviceClient = ServiceClient.createFromConnectionString(connectionString, protocol);

      if (serviceClient != null) {
        serviceClient.open();

        // Get a file upload notification receiver from the ServiceClient.
        fileUploadNotificationReceiver = serviceClient.getFileUploadNotificationReceiver();
        fileUploadNotificationReceiver.open();

        // Start the thread to receive file upload notifications.
        ShowFileUploadNotifications showFileUploadNotifications = new ShowFileUploadNotifications();
        ExecutorService executor = Executors.newFixedThreadPool(1);
        executor.execute(showFileUploadNotifications);

        System.out.println("Press ENTER to exit.");
        System.in.read();
        executor.shutdownNow();
        System.out.println("Shutting down sample...");
        fileUploadNotificationReceiver.close();
        serviceClient.close();
      }
    }
    ```

10. Soubor uložte a zavřete `read-file-upload-notification\src\main\java\com\mycompany\app\App.java` .

11. Pomocí následujícího příkazu sestavte aplikaci **pro čtení souborů a odesílání oznámení** a vyhledejte chyby:

    ```cmd/sh
    mvn clean package -DskipTests
    ```

## <a name="run-the-applications"></a>Spuštění aplikací

Nyní můžete spustit aplikace.

Na příkazovém řádku ve `read-file-upload-notification` složce spusťte následující příkaz:

```cmd/sh
mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
```

Na příkazovém řádku ve `simulated-device` složce spusťte následující příkaz:

```cmd/sh
mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
```

Následující snímek obrazovky ukazuje výstup z aplikace **simulovaného zařízení** :

![Výstup z aplikace simulovaného zařízení](media/iot-hub-java-java-upload/simulated-device.png)

Na následujícím snímku obrazovky vidíte výstup z aplikace **pro čtení souborů a odesílání oznámení** :

![Výstup z aplikace pro čtení-soubor-nahrávání-oznámení](media/iot-hub-java-java-upload/read-file-upload-notification.png)

K zobrazení nahraného souboru v kontejneru úložiště, který jste nakonfigurovali, můžete použít portál:

![Nahraný soubor](media/iot-hub-java-java-upload/uploaded-file.png)

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste zjistili, jak používat možnosti nahrávání souborů IoT Hub ke zjednodušení nahrávání souborů ze zařízení. Pomocí následujících článků můžete dál prozkoumat funkce a scénáře IoT Hub:

* [Programové vytvoření centra IoT Hub](iot-hub-rm-template-powershell.md)

* [Seznámení se sadou C SDK](iot-hub-device-sdk-c-intro.md)

* [Sady Azure IoT SDK](iot-hub-devguide-sdks.md)

Chcete-li dále prozkoumat možnosti IoT Hub, přečtěte si:

* [Simulace zařízení s IoT Edge](../iot-edge/quickstart-linux.md)