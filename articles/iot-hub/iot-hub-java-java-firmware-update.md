---
title: Aktualizace firmwaru zařízení s Azure IoT Hub (Java/Java) | Dokumentace Microsoftu
description: Jak zahájit aktualizaci firmwaru zařízení pomocí správy zařízení ve službě Azure IoT Hub. Zařízení Azure IoT SDK pro Javu použijete k implementaci aplikace simulovaného zařízení a provádět app service, která spustí aktualizaci firmwaru.
author: dominicbetts
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.devlang: java
ms.topic: conceptual
ms.date: 09/11/2017
ms.author: dobett
ms.openlocfilehash: 5991615bca26749e1f138b561260108f8bcf2646
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2018
ms.locfileid: "38611323"
---
# <a name="use-device-management-to-initiate-a-device-firmware-update-javajava"></a>Použití správy zařízení za účelem Zahájit aktualizaci firmwaru zařízení (Java/Java)
[!INCLUDE [iot-hub-selector-firmware-update](../../includes/iot-hub-selector-firmware-update.md)]

V [Začínáme se správou zařízení] [ lnk-dm-getstarted] výukový program, jste viděli, jak používat [dvojče zařízení] [ lnk-devtwin] a [přímé metody ] [ lnk-c2dmethod] primitiv vzdálené restartování zařízení. Tento kurz používá stejný základní služby IoT Hub a popisuje, jak provádět aktualizace firmwaru simulované začátku do konce.  Tento model se používá k provedení aktualizace firmwaru pro [ukázková implementace zařízení Raspberry Pi][lnk-rpi-implementation].

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

V tomto kurzu získáte informace o následujících postupech:

* Vytvořte konzolovou aplikaci Java, která volá **firmwareUpdate** přímé metody v aplikaci simulovaného zařízení prostřednictvím služby IoT hub.
* Vytvořte konzolovou aplikaci Java, která simuluje zařízení a implementuje **firmwareUpdate** přímá metoda. Tato metoda zahájí vícefázový proces, který čeká na stažením image firmwaru, stáhne image firmwaru a nakonec použije image firmwaru. Během každé fáze aktualizace zařízení využívá ohlášené vlastnosti k podávat zprávy o pokroku.

Na konci tohoto kurzu budete mít dvě konzolové aplikace Java:

**aktualizace firmwaru**, volá metodu s přímým přístupem v simulovaném zařízení, zobrazí odpovědi a pravidelně ohlášené vlastnosti aktualizace

**simulated-device**, připojí ke službě IoT hub s identitou zařízení vytvořenou dříve, obdrží volání přímé metody firmwareUpdate a prochází simulace aktualizace firmwaru

Pro absolvování tohoto kurzu potřebujete:

* Nejnovější [Java SE Development Kit 8](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) 
* [Maven 3](https://maven.apache.org/install.html) 
* Aktivní účet Azure. (Pokud účet nemáte, můžete si během několika minut vytvořit [bezplatný účet][lnk-free-trial].)

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity-portal](../../includes/iot-hub-get-started-create-device-identity-portal.md)]

## <a name="trigger-a-remote-firmware-update-on-the-device-using-a-direct-method"></a>Aktivace firmwaru vzdálené aktualizace na zařízení s využitím přímé metody
V této části vytvoříte konzolovou aplikaci Java, který iniciuje vzdálené firmwaru v zařízení. Aplikace používá přímé metody k zahájení aktualizace a používá dotazů na dvojčata zařízení a pravidelně získat stav aktualizace firmwaru aktivní.

1. Vytvořte prázdnou složku s názvem fw-get-started.

1. Ve složce fw-get-started vytvořte projekt Maven s názvem **aktualizace firmwaru** pomocí následujícího příkazu na příkazovém řádku. Všimněte si, že se jedná o jeden dlouhý příkaz:

    `mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=firmware-update -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false`

1. Na příkazovém řádku přejděte do složky aktualizace firmwaru.

1. Pomocí textového editoru otevřete soubor pom.xml ve složce aktualizace firmwaru a přidejte následující závislost **závislosti** uzlu. Tato závislost umožňuje komunikovat se službou IoT hub pomocí balíček iot-service-client ve vaší aplikaci:

    ```xml
    <dependency>
      <groupId>com.microsoft.azure.sdk.iot</groupId>
      <artifactId>iot-service-client</artifactId>
      <version>1.5.22</version>
      <type>jar</type>
    </dependency>
    ```

    > [!NOTE]
    > Můžete vyhledat nejnovější verzi **iot-service-client** pomocí [vyhledávání Maven](http://search.maven.org/#search%7Cga%7C1%7Ca%3A%22iot-service-client%22%20g%3A%22com.microsoft.azure.sdk.iot%22).

1. Přidejte následující **sestavení** uzlu po **závislosti** uzlu. Tuto konfiguraci nastaví Maven k sestavení aplikace pomocí Javy 1.8:

    ```xml
    <build>
      <plugins>
        <plugin>
          <groupId>org.apache.maven.plugins</groupId>
          <artifactId>maven-compiler-plugin</artifactId>
          <version>3.3</version>
          <configuration>
            <source>1.8</source>
            <target>1.8</target>
          </configuration>
        </plugin>
      </plugins>
    </build>
    ```

1. Soubor pom.xml uložte a zavřete.

1. Pomocí textového editoru otevřete zdrojový soubor firmware-update\src\main\java\com\mycompany\app\App.java.

1. Do souboru přidejte následující příkazy pro **import**:

    ```java
    import com.microsoft.azure.sdk.iot.service.devicetwin.DeviceMethod;
    import com.microsoft.azure.sdk.iot.service.devicetwin.MethodResult;
    import com.microsoft.azure.sdk.iot.service.exceptions.IotHubException;
    import com.microsoft.azure.sdk.iot.service.devicetwin.DeviceTwin;
    import com.microsoft.azure.sdk.iot.service.devicetwin.DeviceTwinDevice;

    import java.io.IOException;
    import java.util.concurrent.TimeUnit;
    ```

1. Do třídy **App** přidejte následující proměnné na úrovni třídy. Nahraďte **{youriothubconnectionstring}** jste si poznamenali v IoT hubu připojovacím řetězcem *vytvoření služby IoT Hub* části:

    ```java
    public static final String iotHubConnectionString = "{youriothubconnectionstring}";
    public static final String deviceId = "myDeviceId";

    private static final String methodName = "firmwareUpdate";
    private static final Long responseTimeout = TimeUnit.SECONDS.toSeconds(30);
    private static final Long connectTimeout = TimeUnit.SECONDS.toSeconds(5);
    ```

1. Pokud chcete implementovat metodu, která načteme ohlášené vlastnosti dvojčete zařízení, přidejte následující text do **aplikace** třídy:

    ```java
    public static void ShowReportedProperties() 
    {
        try 
        {
          DeviceTwin deviceTwins = DeviceTwin.createFromConnectionString(iotHubConnectionString);
          DeviceTwinDevice twinDevice = new DeviceTwinDevice(deviceId);
          
          Boolean firmwareUpdated = false;
          Integer timeoutCycle = 0;
    
          while (!firmwareUpdated)
          {
            if (timeoutCycle > 5)
            {
              System.out.println("Operation timed out");
              break;
            }
    
            Thread.sleep(1000);
              
            deviceTwins.getTwin(twinDevice);
    
            String reportedProperties = twinDevice.reportedPropertiesToString();
              
            if(reportedProperties.contains("status=waiting"))
            {
              System.out.println("Waiting on device...");
            }
            else if(reportedProperties.contains("status=downloadComplete"))
            {
              System.out.println("Download complete, applying firmware...");
            }
            else if (reportedProperties.contains("status=applyComplete"))
            {
              System.out.println("Firmware applied");
              System.out.println("Get reported properties from device twin");
              System.out.println(twinDevice.reportedPropertiesToString());
              firmwareUpdated = true;
            }
            else
            {
              timeoutCycle++;
            }
          }
        } catch (Exception ex) {
            System.out.println("Exception reading reported properties: " + ex.getMessage());
        }
    }
    ```

1. Upravte podpis metody **hlavní** metoda k vyvolání následující výjimky:

    ```java
    public static void main( String[] args ) throws IOException
    ```

1. K vyvolání přímé metody firmwareUpdate v simulovaném zařízení, přidejte následující kód, který **hlavní** metody:

    ```java
    DeviceMethod methodClient = DeviceMethod.createFromConnectionString(iotHubConnectionString);

    try
    {
      String payload = "https://someurl";
      
      System.out.println("Invoked firmware update on device.");
      System.out.println("Sent URL: " + payload);
      
      MethodResult result = methodClient.invoke(deviceId, methodName, responseTimeout, connectTimeout, payload);

      if(result == null)
      {
        throw new IOException("Invoke direct method reboot returns null");
      }

      System.out.println("Status for device:   " + result.getStatus());
      System.out.println("Message from device: " + result.getPayload());
    }
    catch (IotHubException e)
    {
      System.out.println(e.getMessage());
    }
    ```

1. Pokud chcete dotazování spustit ohlášené vlastnosti ze simulovaného zařízení, přidejte následující kód, který **hlavní** metody:

    ```java
    ShowReportedProperties();
    ```

1. Umožňuje zastavit aplikaci, přidejte následující kód, který **hlavní** metody:

    ```java
    System.out.println("Press ENTER to exit.");
    System.in.read();
    System.out.println("Shutting down sample...");
    ```

1. Uložte a zavřete soubor firmware-update\src\main\java\com\mycompany\app\App.java.

1. Sestavení **aktualizace firmwaru** back endové aplikace a opravíte jakékoli chyby. Na příkazovém řádku přejděte do složky, aktualizace firmwaru a spusťte následující příkaz:

    `mvn clean package -DskipTests`

## <a name="simulate-a-device-to-handle-direct-method-calls"></a>Simulace zařízení ke zpracování volání přímé metody
V této části vytvoříte konzolovou simulovaného zařízení aplikaci Java, který může přijmout firmwareUpdate přímé metody. Aplikace se pak spustí více stavů procesem simulovat pomocí reportedProperties předkládají stav aktualizace firmwaru.

1. Ve složce fw-get-started vytvořte projekt Maven s názvem **simulated-device** pomocí následujícího příkazu na příkazovém řádku. Všimněte si, že se jedná o jeden dlouhý příkaz:

    `mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=simulated-device -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false`

1. Na příkazovém řádku přejděte do složky simulated-devices.

1. Pomocí textového editoru otevřete soubor pom.xml ve složce aktualizace firmwaru a přidejte následující závislost **závislosti** uzlu. Tato závislost umožňuje komunikovat se službou IoT hub pomocí balíček iot-service-client ve vaší aplikaci:

    ```xml
    <dependency>
      <groupId>com.microsoft.azure.sdk.iot</groupId>
      <artifactId>iot-device-client</artifactId>
      <version>1.3.32</version>
      <type>jar</type>
    </dependency>
    ```

    > [!NOTE]
    > Můžete vyhledat nejnovější verzi **iot-device-client** pomocí [vyhledávání Maven](http://search.maven.org/#search%7Cga%7C1%7Ca%3A%22iot-device-client%22%20g%3A%22com.microsoft.azure.sdk.iot%22).

1. Přidejte následující **sestavení** uzlu po **závislosti** uzlu. Tuto konfiguraci nastaví Maven k sestavení aplikace pomocí Javy 1.8:

    ```xml
    <build>
      <plugins>
        <plugin>
          <groupId>org.apache.maven.plugins</groupId>
          <artifactId>maven-compiler-plugin</artifactId>
          <version>3.3</version>
          <configuration>
            <source>1.8</source>
            <target>1.8</target>
          </configuration>
        </plugin>
      </plugins>
    </build>
    ```

1. Soubor pom.xml uložte a zavřete.

1. Pomocí textového editoru otevřete zdrojový soubor simulated-device\src\main\java\com\mycompany\app\App.java.

1. Do souboru přidejte následující příkazy pro **import**:

    ```java
    import com.microsoft.azure.sdk.iot.device.*;
    import com.microsoft.azure.sdk.iot.device.DeviceTwin.*;

    import java.io.IOException;
    import java.net.URISyntaxException;
    import java.time.LocalDateTime;
    import java.util.Scanner;
    import java.util.Set;
    import java.util.HashSet;
    import java.util.HashMap;
    ```

1. Do třídy **App** přidejte následující proměnné na úrovni třídy. Nahraďte **{yourdeviceconnectionstring}** vaším připojovacím řetězcem zařízení, které jste si poznamenali v *vytvoření Identity zařízení* části:

    ```java
    private static final int METHOD_SUCCESS = 200;
    private static final int METHOD_NOT_DEFINED = 404;

    private static IotHubClientProtocol protocol = IotHubClientProtocol.MQTT;
    private static String connString = "{yourdeviceconnectionstring";
    private static DeviceClient client;

    private static String downloadURL = "unknown";
    ```

1. K implementaci funkcionality přímé metody, poskytují zpětná volání přidáním následující vnořené třídy **aplikace** třídy:

    ```java
    protected static class DirectMethodStatusCallback implements IotHubEventCallback
    {
      public void execute(IotHubStatusCode status, Object context)
      {
        System.out.println("IoT Hub responded to device method operation with status " + status.name());
      }
    }
    protected static class DirectMethodCallback implements com.microsoft.azure.sdk.iot.device.DeviceTwin.DeviceMethodCallback
    {
      @Override
      public DeviceMethodData call(String methodName, Object methodData, Object context)
      {
        DeviceMethodData deviceMethodData;
        switch (methodName)
        {
          case "firmwareUpdate" :
          {
            System.out.println("Response to method '" + methodName + "' sent successfully");
    
            downloadURL = new String((byte[])methodData);
    
            int status = METHOD_SUCCESS;
            System.out.println("Starting firmware update");
            deviceMethodData = new DeviceMethodData(status, "Started firmware update");
            FirmwareUpdateThread firmwareUpdateThread = new FirmwareUpdateThread();
            Thread t = new Thread(firmwareUpdateThread);
            t.start();
            break;
          }
          default:
          {
            int status = METHOD_NOT_DEFINED;
            deviceMethodData = new DeviceMethodData(status, "Not defined direct method " + methodName);
          }
        }
        return deviceMethodData;
      }
    }
    ```

1. K implementaci funkcionality dvojčete zařízení, zadejte zpětná volání přidáním následující vnořené třídy **aplikace** třídy:

    ```java
    protected static class DeviceTwinStatusCallback implements IotHubEventCallback
    {
      public void execute(IotHubStatusCode status, Object context)
      {
        System.out.println("IoT Hub responded to device twin operation with status " + status.name());
      }
    }
    protected static class PropertyCallback implements PropertyCallBack<String, String>
    {
      public void PropertyCall(String propertyKey, String propertyValue, Object context)
      {
        System.out.println("PropertyKey:     " + propertyKey);
        System.out.println("PropertyKvalue:  " + propertyKey);
      }
    }
    ```

1. K provedení aktualizace firmwaru, přidáním následující vnořené třídy **aplikace** třídy:

    ```java
    protected static class FirmwareUpdateThread implements Runnable {
      public void run() {
        try {      
          HashMap initialUpdate = new HashMap();
          Property sentProperty = new Property("firmwareUpdate", initialUpdate);
          Set<Property> sentPackage = new HashSet<Property>();
          
          System.out.println("Downloading from " + downloadURL);
    
          initialUpdate.put("status", "waiting");
          initialUpdate.put("fwPackageUri", downloadURL);
          initialUpdate.put("startedWaitingTime", LocalDateTime.now().toString());   
          sentPackage.add(sentProperty);
    
          client.sendReportedProperties(sentPackage);
    
          Thread.sleep(5000);
    
          System.out.println("Download complete");
    
          HashMap downloadUpdate = new HashMap();
          downloadUpdate.put("status","downloadComplete");
          downloadUpdate.put("downloadCompleteTime", LocalDateTime.now().toString());
          downloadUpdate.put("startedApplyingImage", LocalDateTime.now().toString());
          sentProperty.setValue(downloadUpdate);
    
          client.sendReportedProperties(sentPackage);
    
          Thread.sleep(5000);
    
          System.out.println("Apply complete");
    
          HashMap applyUpdate = new HashMap();
          applyUpdate.put("status","applyComplete");
          applyUpdate.put("lastFirmwareUpdate", LocalDateTime.now().toString());
          sentProperty.setValue(applyUpdate);
    
          client.sendReportedProperties(sentPackage);
    
          Thread.sleep(5000);
    
          HashMap resetUpdate = new HashMap();
          applyUpdate.put("status","reset");
          sentProperty.setValue(resetUpdate);
    
          client.sendReportedProperties(sentPackage);
        }
        catch (Exception ex) {
          System.out.println("Exception in reboot thread: " + ex.getMessage());
        }
      }
    }
    ```

1. Upravte podpis metody **hlavní** metoda k vyvolání následující výjimky:

    ```java
    public static void main(String[] args) throws IOException, URISyntaxException
    ```

1. K zahájení přímých metod a rutina dvojčata zařízení, přidejte následující kód, který **hlavní** metody:

    ```java
    client = new DeviceClient(connString, protocol);

    try
    {
      client.open();
      client.subscribeToDeviceMethod(new DirectMethodCallback(), null, new DirectMethodStatusCallback(), null);
      client.startDeviceTwin(new DeviceTwinStatusCallback(), null, new PropertyCallback(), null);
      System.out.println("Client connected to IoT Hub.  Waiting for firmwareUpdate direct method.");
    }
    catch (Exception e)
    {
      System.out.println("On exception, shutting down \n" + " Cause: " + e.getCause() + " \n" +  e.getMessage());
      client.close();
      System.out.println("Shutting down...");
    }
    ```

1. Umožňuje zastavit aplikaci, přidejte následující kód na konec objektu **hlavní** metody:

    ```java
    System.out.println("Press any key to exit...");
    Scanner scanner = new Scanner(System.in);
    scanner.nextLine();
    scanner.close();
    client.close();
    System.out.println("Shutting down...");
    ```

1. Uložte a zavřete soubor simulated-device\src\main\java\com\mycompany\app\App.java.

1. Sestavení **simulated-device** aplikace a opravíte jakékoli chyby. Na příkazovém řádku přejděte do složky s Simulovaná zařízení a spusťte následující příkaz:

    `mvn clean package -DskipTests`

## <a name="run-the-apps"></a>Spouštění aplikací
Nyní jste připraveni aplikaci spustit.

1. Na příkazovém řádku v **simulated-device** složky, spuštěním následujícího příkazu zahajte naslouchání pro přímé metody aktualizace firmwaru.
   
    `mvn exec:java -Dexec.mainClass="com.mycompany.app.App"`

1. Na příkazovém řádku v **aktualizace firmwaru** složky, spusťte následující příkaz k vyvolání aktualizace firmwaru a dotazování dvojčat zařízení na zařízení s Simulovaná ze služby IoT hub:

    `mvn exec:java -Dexec.mainClass="com.mycompany.app.App"`

3. Zobrazí se simulované zařízení reagovat na přímé metody v konzole.

    ![Firmware úspěšně aktualizovala.][img-fwupdate]

## <a name="next-steps"></a>Další postup
V tomto kurzu se používá přímé metody k aktivaci vzdáleného firmwaru v zařízení a umožňuje sledovat průběh aktualizace firmwaru ohlášené vlastnosti.

Zjistěte, jak rozšířit vaše IoT řešení a plán metoda volá do různých zařízení, najdete v článku [plánování a vysílání úloh] [ lnk-tutorial-jobs] kurzu.

<!-- images -->
[img-fwupdate]: media/iot-hub-java-java-firmware-update/firmwareUpdated.png

[lnk-devtwin]: iot-hub-devguide-device-twins.md
[lnk-c2dmethod]: iot-hub-devguide-direct-methods.md
[lnk-dm-getstarted]: iot-hub-java-java-device-management-getstarted.md
[lnk-tutorial-jobs]: iot-hub-java-java-schedule-jobs.md

[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
[lnk-rpi-implementation]: https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_client/samples/iothub_client_sample_mqtt_dm/pi_device