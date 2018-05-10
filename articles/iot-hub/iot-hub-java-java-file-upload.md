---
title: Odeslání souborů ze zařízení do Azure IoT Hub pro Javu | Microsoft Docs
description: Postup nahrání souborů ze zařízení do cloudu pomocí zařízení Azure IoT SDK pro jazyk Java. Odeslané soubory jsou uloženy v kontejneru objektů blob úložiště Azure.
services: iot-hub
documentationcenter: java
author: dominicbetts
manager: timlt
editor: ''
ms.assetid: 4759d229-f856-4526-abda-414f8b00a56d
ms.service: iot-hub
ms.devlang: java
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/28/2017
ms.author: dobett
ms.openlocfilehash: 794ebd3b2d25f6b7d5dcb86b0834380fce9b9a27
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2018
---
# <a name="upload-files-from-your-device-to-the-cloud-with-iot-hub"></a>Odeslání souborů ze zařízení do cloudu s centrem IoT

[!INCLUDE [iot-hub-file-upload-language-selector](../../includes/iot-hub-file-upload-language-selector.md)]

V tomto kurzu vychází kód [odesílání zpráv typu Cloud-zařízení s centrem IoT](iot-hub-java-java-c2d.md) kurzu ukazují, jak používat [souboru nahrávání služby IoT Hub](iot-hub-devguide-file-upload.md) nahrát soubor do [objektů blob v Azure úložiště](../storage/index.yml). Tento kurz ukazuje, jak na:

- Bezpečně zadejte zařízení s Azure blob identifikátor URI pro nahrání souboru.
- Oznámení o odeslání souboru IoT Hub použijte k aktivaci zpracování souboru ve vaší aplikaci back-end.

[Začínáme se službou IoT Hub](iot-hub-java-java-getstarted.md) a [odesílání zpráv typu Cloud-zařízení s centrem IoT](iot-hub-java-java-c2d.md) kurzy zobrazit základních funkcí zařízení cloud a z cloudu do zařízení zasílání zpráv služby IoT Hub. [Zprávy procesu zařízení-Cloud](iot-hub-java-java-process-d2c.md) kurz popisuje způsob, jak spolehlivě ukládat zprávy typu zařízení cloud do úložiště objektů blob v Azure. Ale v některých scénářích nelze mapovat snadno data, která vaše zařízení odesílají do poměrně malý zprávy typu zařízení cloud, které IoT Hub přijímá. Příklad:

* Velkých souborů, které obsahují Image
* Videa
* Data vibrace odebírána data v vysoká frekvence
* Určitou formu předběžně zpracované data.

Tyto soubory jsou obvykle dávkové zpracování v cloudu pomocí nástrojů, jako [Azure Data Factory](../data-factory/introduction.md) nebo [Hadoop](../hdinsight/index.yml) zásobníku. Když potřebujete hornatých soubory ze zařízení, když můžete nadále používat zabezpečení a spolehlivost služby IoT Hub.

Na konci tohoto kurzu můžete spustit dvě aplikace konzoly v jazyce Java:

* **simulated-device**, upravenou verzi aplikace vytvořená v kurzu [odesílání Cloud-zařízení zprávy službou IoT Hub]. Tato aplikace se uloží soubor do úložiště pomocí identifikátoru URI SAS poskytované služby IoT hub.
* **čtení souboru odesílání oznámení**, který obdrží oznámení o odeslání souboru ze služby IoT hub.

> [!NOTE]
> IoT Hub podporuje mnoho zařízení platformy a jazyky (včetně C, rozhraní .NET a Javascript) prostřednictvím sady SDK pro zařízení Azure IoT. Odkazovat [Azure střediska pro vývojáře IoT] podrobné pokyny o tom, jak připojit zařízení ke službě Azure IoT Hub.

Pro absolvování tohoto kurzu potřebujete:

* Nejnovější [Java SE Development Kit 8](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)
* [Maven 3](https://maven.apache.org/install.html)
* Aktivní účet Azure. (Pokud nemáte účet, můžete vytvořit [bezplatný účet](http://azure.microsoft.com/pricing/free-trial/) si během několika minut.)

[!INCLUDE [iot-hub-associate-storage](../../includes/iot-hub-associate-storage.md)]

## <a name="upload-a-file-from-a-device-app"></a>Nahrát soubor z aplikace na zařízení

V této části upravíte zařízení aplikaci, kterou jste vytvořili v [odesílání zpráv typu Cloud-zařízení s centrem IoT](iot-hub-java-java-c2d.md) pro nahrání souboru do služby IoT hub.

1. Zkopírujte soubor obrázku na `simulated-device` složku a přejmenujte ji `myimage.png`.

1. Pomocí textového editoru, otevřete `simulated-device\src\main\java\com\mycompany\app\App.java` souboru.

1. Přidání deklarace proměnné na **aplikace** třídy:

    ```java
    private static String fileName = "myimage.png";
    ```

1. Při zpracování zprávy zpětného volání stav nahrávání souborů, přidejte následující vnořenou třídu k **aplikace** třídy:

    ```java
    // Define a callback method to print status codes from IoT Hub.
    protected static class FileUploadStatusCallBack implements IotHubEventCallback {
      public void execute(IotHubStatusCode status, Object context) {
        System.out.println("IoT Hub responded to file upload for " + fileName
            + " operation with status " + status.name());
      }
    }
    ```

1. Odesílání obrázků na IoT Hub, přidejte následující metodu do **aplikace** třída odesílání obrázků na IoT Hub:

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

1. Změnit **hlavní** metoda k volání **uploadFile** metoda, jak je znázorněno v následujícím fragmentu kódu:

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

1. Použijte následující příkaz k vytvoření **simulated-device** aplikaci a zkontrolujte chyby:

    ```cmd/sh
    mvn clean package -DskipTests
    ```

## <a name="receive-a-file-upload-notification"></a>Nechte si zaslat oznámení nahrávání souborů

V této části vytvoříte konzolovou aplikaci Java, která přijímá zprávy oznámení nahrávání souborů ze služby IoT Hub.

Je nutné **iothubowner** připojovací řetězec služby IoT hub pro dokončení této části. Můžete najít připojovací řetězec [portál Azure](https://portal.azure.com/) na **zásady sdíleného přístupu** okno.

1. Vytvořte projekt Maven s názvem **čtení souboru odesílání oznámení** pomocí následujícího příkazu na příkazovém řádku. Poznámka: Tento příkaz je jeden dlouhý příkaz:

    ```cmd/sh
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=read-file-upload-notification -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

1. Na příkazovém řádku přejděte do nové `read-file-upload-notification` složky.

1. Pomocí textového editoru, otevřete `pom.xml` v soubor `read-file-upload-notification` složky a přidejte následující závislost na **závislosti** uzlu. Přidání závislost umožňuje používat **iothub-java-service-client** balíček ve vaší aplikaci ke komunikaci s vaší služby IoT hub:

    ```xml
    <dependency>
      <groupId>com.microsoft.azure.sdk.iot</groupId>
      <artifactId>iot-service-client</artifactId>
      <version>1.7.23</version>
    </dependency>
    ```

    > [!NOTE]
    > Můžete zkontrolovat pro nejnovější verzi **klienta služby iot** pomocí [Maven vyhledávání](http://search.maven.org/#search%7Cga%7C1%7Ca%3A%22iot-service-client%22%20g%3A%22com.microsoft.azure.sdk.iot%22).

1. Uložte a zavřete `pom.xml` souboru.

1. Pomocí textového editoru, otevřete `read-file-upload-notification\src\main\java\com\mycompany\app\App.java` souboru.

1. Do souboru přidejte následující příkazy pro **import**:

    ```java
    import com.microsoft.azure.sdk.iot.service.*;
    import java.io.IOException;
    import java.net.URISyntaxException;
    import java.util.concurrent.ExecutorService;
    import java.util.concurrent.Executors;
    ```

1. Přidejte následující proměnné na úrovni třídy k **aplikace** třídy:

    ```java
    private static final String connectionString = "{Your IoT Hub connection string}";
    private static final IotHubServiceClientProtocol protocol = IotHubServiceClientProtocol.AMQPS;
    private static FileUploadNotificationReceiver fileUploadNotificationReceiver = null;
    ```

1. Tisknout informace o nahrávání souborů do konzoly, přidejte následující vnořenou třídu k **aplikace** třídy:

    ```java
    // Create a thread to receive file upload notifications.
    private static class ShowFileUploadNotifications implements Runnable {
      public void run() {
        try {
          while (true) {
            System.out.println("Recieve file upload notifications...");
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

1. Chcete-li spustit vlákno, která čeká na oznámení o odeslání souboru, přidejte následující kód do **hlavní** metoda:

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

1. Uložte a zavřete `read-file-upload-notification\src\main\java\com\mycompany\app\App.java` souboru.

1. Použijte následující příkaz k vytvoření **čtení souboru odesílání oznámení** aplikaci a zkontrolujte chyby:

    ```cmd/sh
    mvn clean package -DskipTests
    ```

## <a name="run-the-applications"></a>Spuštění aplikací

Nyní můžete spustit aplikace.

Na příkazovém řádku v `read-file-upload-notification` složky, spusťte následující příkaz:

```cmd/sh
mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
```

Na příkazovém řádku v `simulated-device` složky, spusťte následující příkaz:

```cmd/sh
mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
```

Následující snímek obrazovky ukazuje výstup **simulated-device** aplikace:

![Výstup z aplikace simulated-device](media/iot-hub-java-java-upload/simulated-device.png)

Následující snímek obrazovky ukazuje výstup **čtení souboru odesílání oznámení** aplikace:

![Výstup z aplikace pro čtení souboru odesílání oznámení](media/iot-hub-java-java-upload/read-file-upload-notification.png)

Na portálu můžete použít k zobrazení nahrávaný soubor v kontejneru úložiště, které jste nakonfigurovali:

![Nahrávaný soubor](media/iot-hub-java-java-upload/uploaded-file.png)

## <a name="next-steps"></a>Další postup

V tomto kurzu jste zjistili, jak používat funkce nahrávání souboru služby IoT Hub pro zjednodušení nahrávání souborů ze zařízení. Můžete dál prozkoumat funkcí služby IoT hub a scénáře v následujících článcích:

* [Vytvoření služby IoT hub prostřednictvím kódu programu][lnk-create-hub]
* [Úvod do jazyka C SDK][lnk-c-sdk]
* [Sady SDK služby Azure IoT][lnk-sdks]

Pokud chcete prozkoumat další možnosti IoT Hub, najdete v části:

* [Simulaci zařízení s hranou IoT][lnk-iotedge]

<!-- Images. -->

[50]: ./media/iot-hub-csharp-csharp-file-upload/run-apps1.png
[1]: ./media/iot-hub-csharp-csharp-file-upload/image-properties.png
[2]: ./media/iot-hub-csharp-csharp-file-upload/file-upload-project-csharp1.png
[3]: ./media/iot-hub-csharp-csharp-file-upload/enable-file-notifications.png

<!-- Links -->



[Azure střediska pro vývojáře IoT]: http://azure.microsoft.com/develop/iot

[Transient Fault Handling]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
[Azure Storage]:../storage/common/storage-create-storage-account.md#create-a-storage-account
[lnk-configure-upload]: iot-hub-configure-file-upload.md
[Azure IoT service SDK NuGet package]: https://www.nuget.org/packages/Microsoft.Azure.Devices/
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[lnk-create-hub]: iot-hub-rm-template-powershell.md
[lnk-c-sdk]: iot-hub-device-sdk-c-intro.md
[lnk-sdks]: iot-hub-devguide-sdks.md

[lnk-iotedge]: ../iot-edge/tutorial-simulate-device-linux.md


