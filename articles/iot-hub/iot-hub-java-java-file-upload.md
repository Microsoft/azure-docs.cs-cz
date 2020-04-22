---
title: Nahrávání souborů ze zařízení do služby Azure IoT Hub s Javou | Dokumenty společnosti Microsoft
description: Jak nahrát soubory ze zařízení do cloudu pomocí sady Azure IoT device SDK pro Jazyk Java. Nahrané soubory se ukládají v kontejneru objektů blob úložiště Azure.
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
ms.openlocfilehash: f0753827fe5f7f2b866726683d4cb1f205da4599
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/21/2020
ms.locfileid: "81732471"
---
# <a name="upload-files-from-your-device-to-the-cloud-with-iot-hub-java"></a>Nahrávání souborů ze zařízení do cloudu pomocí služby IoT Hub (Java)

[!INCLUDE [iot-hub-file-upload-language-selector](../../includes/iot-hub-file-upload-language-selector.md)]

Tento kurz vychází z kódu v kurzu Odesílat zprávy z cloudu na zařízení pomocí služby [IoT Hub,](iot-hub-java-java-c2d.md) který vám ukáže, jak pomocí [možností nahrávání souborů služby IoT Hub](iot-hub-devguide-file-upload.md) nahrát soubor do úložiště objektů [blob Azure](../storage/index.yml). V tomto kurzu získáte informace o následujících postupech:

* Bezpečně poskytněte zařízení s identifikátorem URI objektu BLOB Azure pro nahrávání souboru.

* Pomocí oznámení o nahrání souboru centra IoT Hub aktivujte zpracování souboru v back-endu aplikace.

[Odeslání telemetrie ze zařízení do rychlého startu služby IoT hub](quickstart-send-telemetry-java.md) a odesílání zpráv z cloudu na zařízení pomocí kurzu [služby IoT Hub](iot-hub-java-java-c2d.md) zobrazují základní funkce zasílání zpráv mezi zařízeními a cloud-to-zařízení služby IoT Hub. Kurz [Konfigurace směrování zpráv pomocí služby IoT Hub](tutorial-routing.md) popisuje způsob spolehlivého ukládání zpráv mezi zařízeními v úložišti objektů blob Azure. V některých případech však nelze snadno mapovat data, která vaše zařízení odesílají do relativně malých zpráv zařízení cloud, které služba IoT Hub přijímá. Příklad:

* Velké soubory, které obsahují obrázky
* Videa
* Údaje o vibracích odebrané při vysoké frekvenci
* Nějaká forma předzpracovaných dat.

Tyto soubory se obvykle zpracovávají v cloudu pomocí nástrojů, jako je [Azure Data Factory](../data-factory/introduction.md) nebo zásobník [Hadoop.](../hdinsight/index.yml) Když potřebujete upland soubory ze zařízení, můžete stále používat zabezpečení a spolehlivost IoT Hub.

Na konci tohoto kurzu spustíte dvě konzolové aplikace Java:

* **simulované zařízení**, upravená verze aplikace vytvořená v kurzu [Odeslat zprávy z cloudu na zařízení pomocí služby IoT Hub]. Tato aplikace nahraje soubor do úložiště pomocí identifikátoru URI SAS poskytovaného službou IoT hub.

* **read-file-upload-notification**, který přijímá oznámení o nahrání souborů z vašeho centra IoT hub.

> [!NOTE]
> IoT Hub podporuje mnoho platforem zařízení a jazyků (včetně C, .NET a Javascript) prostřednictvím sad SDK zařízení Azure IoT. Podrobné pokyny k připojení zařízení k Azure IoT Hub vám najdete v [Centru pro vývojáře Azure IoT.](https://azure.microsoft.com/develop/iot)

## <a name="prerequisites"></a>Požadavky

* [Java SE Vývojová sada 8](https://docs.microsoft.com/java/azure/jdk/?view=azure-java-stable). Ujistěte se, že jste v části **Dlouhodobá podpora** vybrali **Javu 8,** abyste se dostali ke stažení pro JDK 8.

* [Maven 3](https://maven.apache.org/download.cgi)

* Aktivní účet Azure. (Pokud nemáte účet, můžete si vytvořit [bezplatný účet](https://azure.microsoft.com/pricing/free-trial/) během několika minut.)

* Zkontrolujte, zda je v bráně firewall otevřený port 8883. Ukázka zařízení v tomto článku používá protokol MQTT, který komunikuje přes port 8883. Tento port může být blokován v některých prostředích podnikové a vzdělávací sítě. Další informace a způsoby, jak tento problém vyřešit, najdete [v tématu připojení k centru IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

[!INCLUDE [iot-hub-associate-storage](../../includes/iot-hub-associate-storage.md)]

## <a name="upload-a-file-from-a-device-app"></a>Nahrání souboru z aplikace pro zařízení

V této části můžete upravit aplikaci zařízení, kterou jste vytvořili v [odesílání zpráv z cloudu na zařízení pomocí služby IoT Hub,](iot-hub-java-java-c2d.md) a nahrát soubor do služby IoT hub.

1. Zkopírujte soubor obrázku do `simulated-device` složky a přejmenujte jej `myimage.png`.

2. Pomocí textového editoru `simulated-device\src\main\java\com\mycompany\app\App.java` otevřete soubor.

3. Přidejte deklaraci proměnné do třídy **App:**

    ```java
    private static String fileName = "myimage.png";
    ```

4. Chcete-li zpracovat zprávy o zpětném volání o stavu odesílání souborů, přidejte do třídy **App** následující vnořenou třídu:

    ```java
    // Define a callback method to print status codes from IoT Hub.
    protected static class FileUploadStatusCallBack implements IotHubEventCallback {
      public void execute(IotHubStatusCode status, Object context) {
        System.out.println("IoT Hub responded to file upload for " + fileName
            + " operation with status " + status.name());
      }
    }
    ```

5. Chcete-li nahrát obrázky do ioT hubu, přidejte do třídy **App** následující metodu pro nahrávání obrázků do ioT hubu:

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

6. Upravte **hlavní** metodu pro volání metody **uploadFile,** jak je znázorněno v následujícím fragmentu:

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

7. Pomocí následujícího příkazu vytvořte aplikaci **simulované zařízení** a zkontrolujte chyby:

    ```cmd/sh
    mvn clean package -DskipTests
    ```

## <a name="get-the-iot-hub-connection-string"></a>Získání připojovacího řetězce centra IoT hub

V tomto článku vytvoříte back-endovou službu pro příjem oznámení o nahrání souborů z centra IoT, které jste vytvořili v [aplikaci Odeslat telemetrii ze zařízení do služby IoT hub](quickstart-send-telemetry-java.md). Chcete-li přijímat oznámení o nahrání souboru, vaše služba potřebuje oprávnění **k připojení služby.** Ve výchozím nastavení je každé centrum IoT Hub vytvořeno pomocí zásady sdíleného přístupu s názvem **služba,** která uděluje toto oprávnění.

[!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

## <a name="receive-a-file-upload-notification"></a>Přijetí oznámení o nahrání souboru

V této části vytvoříte konzolovou aplikaci Java, která přijímá oznámení o nahrání souborů z ioT hubu.

1. Vytvořte projekt Maven s názvem **read-file-upload-notification** pomocí následujícího příkazu na příkazovém řádku. Všimněte si, že tento příkaz je jeden, dlouhý příkaz:

    ```cmd/sh
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=read-file-upload-notification -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

2. Na příkazovém řádku přejděte do nové `read-file-upload-notification` složky.

3. Pomocí textového editoru `pom.xml` otevřete `read-file-upload-notification` soubor ve složce a přidejte následující závislost do uzlu **závislostí.** Přidání závislosti umožňuje použít balíček **iothub-java-service-client** ve vaší aplikaci ke komunikaci se službou IoT hub:

    ```xml
    <dependency>
      <groupId>com.microsoft.azure.sdk.iot</groupId>
      <artifactId>iot-service-client</artifactId>
      <version>1.7.23</version>
    </dependency>
    ```

    > [!NOTE]
    > Můžete vyhledat nejnovější verzi **iot-service-client** pomocí [vyhledávání Maven](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22iot-service-client%22%20g%3A%22com.microsoft.azure.sdk.iot%22).

4. Uložte a `pom.xml` zavřete soubor.

5. Pomocí textového editoru `read-file-upload-notification\src\main\java\com\mycompany\app\App.java` otevřete soubor.

6. Do souboru přidejte následující příkazy pro **import**:

    ```java
    import com.microsoft.azure.sdk.iot.service.*;
    import java.io.IOException;
    import java.net.URISyntaxException;
    import java.util.concurrent.ExecutorService;
    import java.util.concurrent.Executors;
    ```

7. Do třídy **App** přidejte následující proměnné na úrovni třídy. Nahraďte `{Your IoT Hub connection string}` zástupnou hodnotu připojovacím řetězcem služby IoT hub, který jste dříve zkopírovali v [připojovacím řetězci Služby IoT hub](#get-the-iot-hub-connection-string):

    ```java
    private static final String connectionString = "{Your IoT Hub connection string}";
    private static final IotHubServiceClientProtocol protocol = IotHubServiceClientProtocol.AMQPS;
    private static FileUploadNotificationReceiver fileUploadNotificationReceiver = null;
    ```

8. Chcete-li vytisknout informace o nahrání souboru do konzoly, přidejte do třídy **App** následující vnořenou třídu:

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

9. Chcete-li spustit vlákno, které naslouchá oznámení o nahrávání souborů, přidejte k **hlavní** metodě následující kód:

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

10. Uložte a `read-file-upload-notification\src\main\java\com\mycompany\app\App.java` zavřete soubor.

11. Pomocí následujícího příkazu vytvořte aplikaci **read-file-upload-notification** a zkontrolujte chyby:

    ```cmd/sh
    mvn clean package -DskipTests
    ```

## <a name="run-the-applications"></a>Spuštění aplikací

Nyní můžete spustit aplikace.

Na příkazovém `read-file-upload-notification` řádku ve složce spusťte následující příkaz:

```cmd/sh
mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
```

Na příkazovém `simulated-device` řádku ve složce spusťte následující příkaz:

```cmd/sh
mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
```

Následující snímek obrazovky ukazuje výstup z aplikace **simulované zařízení:**

![Výstup z aplikace simulované zařízení](media/iot-hub-java-java-upload/simulated-device.png)

Následující snímek obrazovky ukazuje výstup z aplikace **read-file-upload-notification:**

![Výstup z aplikace read-file-upload-notification](media/iot-hub-java-java-upload/read-file-upload-notification.png)

Pomocí portálu můžete zobrazit nahraný soubor v kontejneru úložiště, který jste nakonfigurovali:

![Nahraný soubor](media/iot-hub-java-java-upload/uploaded-file.png)

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili používat možnosti nahrávání souborů v centru IoT Hub ke zjednodušení odesílání souborů ze zařízení. Můžete pokračovat v prozkoumání funkcí a scénářů centra IoT v následujících článcích:

* [Programové vytvoření centra IoT hub](iot-hub-rm-template-powershell.md)

* [Úvod do sady C SDK](iot-hub-device-sdk-c-intro.md)

* [Sady Azure IoT SDK](iot-hub-devguide-sdks.md)

Další informace o možnostech IoT Hubu najdete v následujících tématech:

* [Simulace zařízení pomocí IoT Edge](../iot-edge/tutorial-simulate-device-linux.md)
