---
title: Začínáme se správou zařízení Azure IoT Hub (Java) | Microsoft Docs
description: Jak používat k zahájení restartu zařízení vzdálenou správou zařízení Azure IoT Hub. Zařízení Azure IoT SDK pro jazyk Java, kterou použijete k implementaci aplikaci ze simulovaného zařízení, která zahrnuje přímá metoda a sady SDK pro jazyk Java k implementaci aplikační služby, která volá metodu přímé služby Azure IoT.
author: dominicbetts
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.devlang: java
ms.topic: conceptual
ms.date: 08/08/2017
ms.author: dobett
ms.openlocfilehash: 6a4ba8c2b88520dff028610cf64aa9b3a6e3fefd
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34636184"
---
# <a name="get-started-with-device-management-java"></a>Začínáme se správou zařízení (Java)

[!INCLUDE [iot-hub-selector-dm-getstarted](../../includes/iot-hub-selector-dm-getstarted.md)]

V tomto kurzu získáte informace o následujících postupech:

* Použití portálu Azure k vytvoření služby IoT Hub a vytvoření identity zařízení ve službě IoT hub.
* Vytvořte aplikaci ze simulovaného zařízení, která používá přímý způsob restartování zařízení. Přímé metody jsou vyvolány z cloudu.
* Vytvořte aplikaci, která volá metodu přímé restartování v aplikaci simulovaného zařízení prostřednictvím služby IoT hub. Tuto aplikaci poté sleduje hlášen vlastnostech ze zařízení, které najdete v části po dokončení operace restartování.

Na konci tohoto kurzu máte dvě aplikace konzoly v jazyce Java:

**simulated-device**. Tuto aplikaci:

* Připojí se ke službě IoT hub s dříve vytvořenou identitou zařízení.
* Přijme hovor přímá metoda restartování.
* Simuluje fyzické restartování.
* Čas poslední restartování prostřednictvím hlášené vlastnosti sestavy.

**aktivační událost restartování**. Tuto aplikaci:

* Přímá metoda volá v aplikaci simulovaného zařízení.
* Zobrazí odpověď na volání metody přímé poslal simulované zařízení
* Zobrazí aktualizovaná hlášené vlastnosti.

> [!NOTE]
> Informace o sadách SDK, které můžete použít k vytváření aplikací pro spuštění na zařízení a back end vašeho řešení najdete v tématu [SDK služby Azure IoT][lnk-hub-sdks].

Pro absolvování tohoto kurzu potřebujete:

* Java SE 8. <br/> Kapitola [Příprava vývojového prostředí][lnk-dev-setup] popisuje postup instalace Javy pro tento návod v systému Windows nebo Linux.
* Maven 3.  <br/> Kapitola [Příprava vývojového prostředí][lnk-dev-setup] popisuje postup instalace nástroje [Maven][lnk-maven] pro tento návod v systému Windows nebo Linux.
* [Verze Node.js 0.10.0 nebo novější](http://nodejs.org).

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

## <a name="trigger-a-remote-reboot-on-the-device-using-a-direct-method"></a>Aktivační události restartu vzdálené v zařízení s přímá metoda

V této části vytvoříte konzolovou aplikaci Java:

1. Vyvolá metodu přímé restartování v aplikaci simulovaného zařízení.
1. Zobrazí odpověď.
1. Hlasování hlášen vlastnostech odesílaných ze zařízení k určení po dokončení restartování.

Tato Konzolová aplikace připojí do služby IoT Hub pro vyvolání metody přímé a čtení hlášené vlastnosti.

1. Vytvořte prázdnou složku s názvem dm-get-started.

1. Ve složce dm-get-started vytvořte projekt Maven s názvem **restartování aktivační událost** pomocí následujícího příkazu na příkazovém řádku. Následující obrázek znázorňuje jeden dlouhý příkaz:

    `mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=trigger-reboot -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false`

1. Na příkazovém řádku přejděte do složky restartování aktivační události.

1. Pomocí textového editoru, otevřete soubor pom.xml ve složce restartování aktivační události a přidejte následující závislost na **závislosti** uzlu. Tuto závislost umožňuje komunikovat se službou IoT hub pomocí balíčku klienta služby iot ve vaší aplikaci:

    ```xml
    <dependency>
      <groupId>com.microsoft.azure.sdk.iot</groupId>
      <artifactId>iot-service-client</artifactId>
      <version>1.7.23</version>
      <type>jar</type>
    </dependency>
    ```

    > [!NOTE]
    > Můžete vyhledat nejnovější verzi **iot-service-client** pomocí [vyhledávání Maven][lnk-maven-service-search].

1. Přidejte následující **sestavení** uzlu po **závislosti** uzlu. Tato konfigurace se dá pokyn Maven k sestavení aplikace pomocí Java 1.8:

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

1. Pomocí textového editoru otevřete trigger-reboot\src\main\java\com\mycompany\app\App.java zdrojový soubor.

1. Do souboru přidejte následující příkazy pro **import**:

    ```java
    import com.microsoft.azure.sdk.iot.service.devicetwin.DeviceMethod;
    import com.microsoft.azure.sdk.iot.service.devicetwin.MethodResult;
    import com.microsoft.azure.sdk.iot.service.exceptions.IotHubException;
    import com.microsoft.azure.sdk.iot.service.devicetwin.DeviceTwin;
    import com.microsoft.azure.sdk.iot.service.devicetwin.DeviceTwinDevice;

    import java.io.IOException;
    import java.util.concurrent.TimeUnit;
    import java.util.concurrent.Executors;
    import java.util.concurrent.ExecutorService;
    ```

1. Do třídy **App** přidejte následující proměnné na úrovni třídy. Nahraďte `{youriothubconnectionstring}` IoT hub připojovacím řetězcem jste si poznamenali v *vytvoření služby IoT Hub* části:

    ```java
    public static final String iotHubConnectionString = "{youriothubconnectionstring}";
    public static final String deviceId = "myDeviceId";

    private static final String methodName = "reboot";
    private static final Long responseTimeout = TimeUnit.SECONDS.toSeconds(30);
    private static final Long connectTimeout = TimeUnit.SECONDS.toSeconds(5);
    ```

1. Chcete-li implementovat podproces, který čte hlášené vlastnosti z dvojče zařízení každých 10 sekund, přidejte následující vnořenou třídu k **aplikace** třídy:

    ```java
    private static class ShowReportedProperties implements Runnable {
      public void run() {
        try {
          DeviceTwin deviceTwins = DeviceTwin.createFromConnectionString(iotHubConnectionString);
          DeviceTwinDevice twinDevice = new DeviceTwinDevice(deviceId);
          while (true) {
            System.out.println("Get reported properties from device twin");
            deviceTwins.getTwin(twinDevice);
            System.out.println(twinDevice.reportedPropertiesToString());
            Thread.sleep(10000);
          }
        } catch (Exception ex) {
          System.out.println("Exception reading reported properties: " + ex.getMessage());
        }
      }
    }
    ```

1. Upravte podpis metody **hlavní** metoda k vyvolání následující výjimky:

    ```java
    public static void main(String[] args) throws IOException
    ```

1. K vyvolání metody přímé restartování na simulované zařízení, přidejte následující kód, který **hlavní** metoda:

    ```java
    System.out.println("Starting sample...");
    DeviceMethod methodClient = DeviceMethod.createFromConnectionString(iotHubConnectionString);

    try
    {
      System.out.println("Invoke reboot direct method");
      MethodResult result = methodClient.invoke(deviceId, methodName, responseTimeout, connectTimeout, null);

      if(result == null)
      {
        throw new IOException("Invoke direct method reboot returns null");
      }
      System.out.println("Invoked reboot on device");
      System.out.println("Status for device:   " + result.getStatus());
      System.out.println("Message from device: " + result.getPayload());
    }
    catch (IotHubException e)
    {
        System.out.println(e.getMessage());
    }
    ```

1. Chcete-li spustit vlákno pro cyklické dotazování hlášen vlastnostech ze simulovaného zařízení, přidejte následující kód, který **hlavní** metoda:

    ```java
    ShowReportedProperties showReportedProperties = new ShowReportedProperties();
    ExecutorService executor = Executors.newFixedThreadPool(1);
    executor.execute(showReportedProperties);
    ```

1. Chcete-li umožňují zastavte aplikaci, přidejte následující kód do **hlavní** metoda:

    ```java
    System.out.println("Press ENTER to exit.");
    System.in.read();
    executor.shutdownNow();
    System.out.println("Shutting down sample...");
    ```

1. Uložte a zavřete soubor trigger-reboot\src\main\java\com\mycompany\app\App.java.

1. Sestavení **restartování aktivační událost** back-end aplikace a opravte všechny chyby. Na příkazovém řádku přejděte do složky, restartování aktivační události a spusťte následující příkaz:

    `mvn clean package -DskipTests`

## <a name="create-a-simulated-device-app"></a>Vytvoření aplikace simulovaného zařízení

V této části vytvoříte konzolovou aplikaci Java, která simuluje zařízení. Aplikace naslouchá restartování přímé volání metody ze služby IoT hub a okamžitě odpoví na toto volání. Aplikace pak chvíli k simulaci procesu restartování předtím, než použije hlášené vlastnost oznámení v režimu spánku **restartování aktivační událost** back-end aplikačním dokončení restartování.

1. Ve složce dm-get-started vytvořte projekt Maven s názvem **simulated-device** pomocí následujícího příkazu na příkazovém řádku. Toto je jeden dlouhý příkaz:

    `mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=simulated-device -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false`

1. Na příkazovém řádku přejděte do složky simulated-devices.

1. Pomocí textového editoru otevřete ve složce simulated-device soubor pom.xml a přidejte následující závislost na **závislosti** uzlu. Tuto závislost umožňuje komunikovat se službou IoT hub pomocí balíčku klienta služby iot ve vaší aplikaci:

    ```xml
    <dependency>
      <groupId>com.microsoft.azure.sdk.iot</groupId>
      <artifactId>iot-device-client</artifactId>
      <version>1.3.32</version>
    </dependency>
    ```

    > [!NOTE]
    > Můžete vyhledat nejnovější verzi **iot-device-client** pomocí [vyhledávání Maven][lnk-maven-device-search].

1. Přidejte následující **sestavení** uzlu po **závislosti** uzlu. Tato konfigurace se dá pokyn Maven k sestavení aplikace pomocí Java 1.8:

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

1. Pomocí textového editoru, otevřete soubor simulated-device\src\main\java\com\mycompany\app\App.java zdroje.

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
    ```

1. Do třídy **App** přidejte následující proměnné na úrovni třídy. Nahraďte `{yourdeviceconnectionstring}` jste si poznamenali v připojovacím řetězcem zařízení *vytvoření identity zařízení* části:

    ```java
    private static final int METHOD_SUCCESS = 200;
    private static final int METHOD_NOT_DEFINED = 404;

    private static IotHubClientProtocol protocol = IotHubClientProtocol.MQTT;
    private static String connString = "{yourdeviceconnectionstring}";
    private static DeviceClient client;
    ```

1. K implementaci obslužná rutina zpětného volání pro přímé metoda události stavu, přidejte následující vnořenou třídu k **aplikace** třídy:

    ```java
    protected static class DirectMethodStatusCallback implements IotHubEventCallback
    {
      public void execute(IotHubStatusCode status, Object context)
      {
        System.out.println("IoT Hub responded to device method operation with status " + status.name());
      }
    }
    ```

1. K implementaci obslužná rutina zpětného volání pro zařízení twin stav události, přidejte následující vnořenou třídu k **aplikace** třídy:

    ```java
    protected static class DeviceTwinStatusCallback implements IotHubEventCallback
    {
        public void execute(IotHubStatusCode status, Object context)
        {
            System.out.println("IoT Hub responded to device twin operation with status " + status.name());
        }
    }
    ```

1. Chcete-li implementovat obslužná rutina zpětného volání pro vlastnosti události, přidejte následující vnořenou třídu k **aplikace** třídy:

    ```java
    protected static class PropertyCallback implements PropertyCallBack<String, String>
    {
      public void PropertyCall(String propertyKey, String propertyValue, Object context)
      {
        System.out.println("PropertyKey:     " + propertyKey);
        System.out.println("PropertyKvalue:  " + propertyKey);
      }
    }
    ```

1. Chcete-li implementovat vlákno k simulaci restartování zařízení, přidejte následující vnořenou třídu k **aplikace** třídy. Vlákno pět sekund v režimu spánku a poté nastaví **lastReboot** hlášené vlastnost:

    ```java
    protected static class RebootDeviceThread implements Runnable {
      public void run() {
        try {
          System.out.println("Rebooting...");
          Thread.sleep(5000);
          Property property = new Property("lastReboot", LocalDateTime.now());
          Set<Property> properties = new HashSet<Property>();
          properties.add(property);
          client.sendReportedProperties(properties);
          System.out.println("Rebooted");
        }
        catch (Exception ex) {
          System.out.println("Exception in reboot thread: " + ex.getMessage());
        }
      }
    }
    ```

1. Chcete-li implementovat metodu přímé na zařízení, přidejte následující vnořenou třídu k **aplikace** třídy. Když simulované aplikace obdrží volání **restartovat** přímá metoda ji vrátí potvrzení volající a pak spustí vlákna ke zpracování restartování:

    ```java
    protected static class DirectMethodCallback implements com.microsoft.azure.sdk.iot.device.DeviceTwin.DeviceMethodCallback
    {
      @Override
      public DeviceMethodData call(String methodName, Object methodData, Object context)
      {
        DeviceMethodData deviceMethodData;
        switch (methodName)
        {
          case "reboot" :
          {
            int status = METHOD_SUCCESS;
            System.out.println("Received reboot request");
            deviceMethodData = new DeviceMethodData(status, "Started reboot");
            RebootDeviceThread rebootThread = new RebootDeviceThread();
            Thread t = new Thread(rebootThread);
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

1. Upravte podpis metody **hlavní** metoda k vyvolání následující výjimky:

    ```java
    public static void main(String[] args) throws IOException, URISyntaxException
    ```

1. K vytváření instancí **DeviceClient**, přidejte následující kód, který **hlavní** metoda:

    ```java
    System.out.println("Starting device client sample...");
    client = new DeviceClient(connString, protocol);
    ```

1. Pokud chcete spustit přijímá volání přímá metoda, přidejte následující kód do **hlavní** metoda:

    ```java
    try
    {
      client.open();
      client.subscribeToDeviceMethod(new DirectMethodCallback(), null, new DirectMethodStatusCallback(), null);
      client.startDeviceTwin(new DeviceTwinStatusCallback(), null, new PropertyCallback(), null);
      System.out.println("Subscribed to direct methods and polling for reported properties. Waiting...");
    }
    catch (Exception e)
    {
      System.out.println("On exception, shutting down \n" + " Cause: " + e.getCause() + " \n" +  e.getMessage());
      client.close();
      System.out.println("Shutting down...");
    }
    ```

1. Chcete-li vypnout v simulátoru zařízení, přidejte následující kód do **hlavní** metoda:

    ```java
    System.out.println("Press any key to exit...");
    Scanner scanner = new Scanner(System.in);
    scanner.nextLine();
    scanner.close();
    client.close();
    System.out.println("Shutting down...");
    ```

1. Uložte a zavřete soubor simulated-device\src\main\java\com\mycompany\app\App.java.

1. Sestavení **simulated-device** back-end aplikace a opravte všechny chyby. Na příkazovém řádku přejděte do složky simulated-device a spusťte následující příkaz:

    `mvn clean package -DskipTests`

## <a name="run-the-apps"></a>Spouštění aplikací

Nyní jste připraveni aplikaci spustit.

1. V příkazovém řádku ve složce simulated-device spusťte následující příkaz, aby začal přijímat volání metody restartování ze služby IoT hub:

    `mvn exec:java -Dexec.mainClass="com.mycompany.app.App"`

    ![Přímá metoda volání restartovat aplikaci simulovaného zařízení Java IoT Hub pro naslouchání][1]

1. Na příkazovém řádku ve složce Aktivační událost restartování spusťte následující příkaz k volání metody restartování na simulovaného zařízení ze služby IoT hub:

    `mvn exec:java -Dexec.mainClass="com.mycompany.app.App"`

    ![Aplikace služby Java IoT Hub k volání metody přímé restartování][2]

1. Simulované zařízení reaguje na volání metody přímé restartování:

    ![Aplikaci simulovaného zařízení Java IoT Hub odpoví na volání přímá metoda][3]

[!INCLUDE [iot-hub-dm-followup](../../includes/iot-hub-dm-followup.md)]

<!-- images and links -->
[1]: ./media/iot-hub-java-java-device-management-getstarted/launchsimulator.png
[2]: ./media/iot-hub-java-java-device-management-getstarted/triggerreboot.png
[3]: ./media/iot-hub-java-java-device-management-getstarted/respondtoreboot.png
<!-- Links -->

[lnk-maven]: https://maven.apache.org/what-is-maven.html

[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdk-java/blob/master/doc/java-devbox-setup.md

[lnk-hub-sdks]: iot-hub-devguide-sdks.md

[lnk-maven-service-search]: http://search.maven.org/#search%7Cga%7C1%7Ca%3A%22iot-service-client%22%20g%3A%22com.microsoft.azure.sdk.iot%22
[lnk-maven-device-search]: http://search.maven.org/#search%7Cga%7C1%7Ca%3A%22iot-device-client%22%20g%3A%22com.microsoft.azure.sdk.iot%22