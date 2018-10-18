---
title: Začínáme se správou zařízení Azure IoT Hub (Java) | Dokumentace Microsoftu
description: Jak používat správu zařízení Azure IoT Hub k zahajte restartování vzdáleném zařízení. Implementace aplikace s Simulovaná zařízení, která obsahuje metodu s přímým přístupem a službu Azure IoT SDK pro Javu k implementaci app service, která vyvolá přímou metodu pomocí zařízení Azure IoT SDK pro Javu.
author: dominicbetts
ms.service: iot-hub
services: iot-hub
ms.devlang: java
ms.topic: conceptual
ms.date: 08/08/2017
ms.author: dobett
ms.openlocfilehash: 75216a6be990e6b994bb62b5b833c03d4b4062c2
ms.sourcegitcommit: 3a7c1688d1f64ff7f1e68ec4bb799ba8a29a04a8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/17/2018
ms.locfileid: "49378047"
---
# <a name="get-started-with-device-management-java"></a>Začínáme se správou zařízení (Java)

[!INCLUDE [iot-hub-selector-dm-getstarted](../../includes/iot-hub-selector-dm-getstarted.md)]

V tomto kurzu získáte informace o následujících postupech:

* Pomocí webu Azure portal k vytvoření služby IoT Hub a vytvoření identity zařízení ve službě IoT hub.

* Vytvoření aplikace simulovaného zařízení, která implementuje metodu s přímým přístupem k restartování zařízení. Přímé metody jsou vyvolány z cloudu.

* Vytvořte aplikaci, která volá metodu restartování s přímým přístupem v aplikaci simulovaného zařízení prostřednictvím služby IoT hub. Tuto aplikaci pak monitoruje ohlášené vlastnosti ze zařízení, které najdete v článku po dokončení operace restartování.

Na konci tohoto kurzu budete mít dvě konzolové aplikace Java:

**simulated-device**. Tato aplikace:

* Připojí ke službě IoT hub s dříve vytvořenou identitou zařízení.

* Přijímá volání přímé metody restartování.

* Simuluje fyzické restartovat počítač.

* Čas poslední restartování prostřednictvím ohlášených vlastností hlásí.

**aktivační událost restartování**. Tato aplikace:

* Volá metodu s přímým přístupem v aplikaci simulovaného zařízení.

* Zobrazí odpověď na volání přímé metody odeslané ze simulovaného zařízení.

* Zobrazí aktualizovaný ohlášené vlastnosti.

> [!NOTE]
> Informace o sadách SDK, které můžete použít k vytváření aplikací pro zařízení i back-endem řešení najdete v tématu [sad SDK Azure IoT](iot-hub-devguide-sdks.md).

Pro absolvování tohoto kurzu potřebujete:

* Java SE 8. <br/> [Příprava vývojového prostředí](https://github.com/Azure/azure-iot-sdk-java/blob/master/doc/java-devbox-setup.md) popisuje postup instalace Javy pro účely tohoto kurzu ve Windows nebo Linuxu.

* Maven 3.  <br/> [Příprava vývojového prostředí](https://github.com/Azure/azure-iot-sdk-java/blob/master/doc/java-devbox-setup.md) najdete popis postupu instalace [Maven](https://maven.apache.org/what-is-maven.html) pro účely tohoto kurzu ve Windows nebo Linuxu.

* [Verze Node.js 0.10.0 nebo vyšší](http://nodejs.org).

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

## <a name="trigger-a-remote-reboot-on-the-device-using-a-direct-method"></a>Aktivační událost vzdálené restartování zařízení pomocí přímé metody

V této části vytvoříte konzolovou aplikaci Java, který:

1. Vyvolá metodu restartování s přímým přístupem v aplikaci simulovaného zařízení.

2. Zobrazí odpovědi.

3. Hlasování ohlášených vlastností odeslaných ze zařízení k určení po dokončení restartování.

Tato Konzolová aplikace se připojí ke službě IoT Hub pro vyvolání přímé metody a čtení ohlášené vlastnosti.

1. Vytvořte prázdnou složku s názvem dm-get-started.

2. Ve složce dm-get-started vytvořte projekt Maven s názvem **aktivační události – restartování** pomocí následujícího příkazu na příkazovém řádku. Následující obrázek znázorňuje jeden dlouhý příkaz:

    `mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=trigger-reboot -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false`

3. Na příkazovém řádku přejděte do složky restartování aktivační události.

4. Pomocí textového editoru otevřete soubor pom.xml ve složce restartování aktivační události a přidejte následující závislost **závislosti** uzlu. Tato závislost umožňuje komunikovat se službou IoT hub pomocí balíček iot-service-client ve vaší aplikaci:

    ```xml
    <dependency>
      <groupId>com.microsoft.azure.sdk.iot</groupId>
      <artifactId>iot-service-client</artifactId>
      <version>1.7.23</version>
      <type>jar</type>
    </dependency>
    ```

    > [!NOTE]
    > Můžete vyhledat nejnovější verzi **iot-service-client** pomocí [vyhledávání Maven](http://search.maven.org/#search%7Cga%7C1%7Ca%3A%22iot-service-client%22%20g%3A%22com.microsoft.azure.sdk.iot%22).

5. Přidejte následující **sestavení** uzlu po **závislosti** uzlu. Tuto konfiguraci nastaví Maven k sestavení aplikace pomocí Javy 1.8:

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

6. Soubor pom.xml uložte a zavřete.

7. Pomocí textového editoru otevřete zdrojový soubor trigger-reboot\src\main\java\com\mycompany\app\App.java.

8. Do souboru přidejte následující příkazy pro **import**:

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

9. Do třídy **App** přidejte následující proměnné na úrovni třídy. Nahraďte `{youriothubconnectionstring}` jste si poznamenali v IoT hubu připojovacím řetězcem *vytvoření služby IoT Hub* části:

    ```java
    public static final String iotHubConnectionString = "{youriothubconnectionstring}";
    public static final String deviceId = "myDeviceId";

    private static final String methodName = "reboot";
    private static final Long responseTimeout = TimeUnit.SECONDS.toSeconds(30);
    private static final Long connectTimeout = TimeUnit.SECONDS.toSeconds(5);
    ```

10. K implementaci vlákno, které načteme ohlášené vlastnosti dvojčete zařízení každých 10 sekund, přidáním následující vnořené třídy **aplikace** třídy:

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

11. Upravte podpis metody **hlavní** metoda k vyvolání následující výjimky:

    ```java
    public static void main(String[] args) throws IOException
    ```

12. K vyvolání přímé metody restartování v simulovaném zařízení, přidejte následující kód, který **hlavní** metody:

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

13. Spustit vlákno k dotazování ohlášené vlastnosti ze simulovaného zařízení, přidejte následující kód, který **hlavní** metody:

    ```java
    ShowReportedProperties showReportedProperties = new ShowReportedProperties();
    ExecutorService executor = Executors.newFixedThreadPool(1);
    executor.execute(showReportedProperties);
    ```

14. Umožňuje zastavit aplikaci, přidejte následující kód, který **hlavní** metody:

    ```java
    System.out.println("Press ENTER to exit.");
    System.in.read();
    executor.shutdownNow();
    System.out.println("Shutting down sample...");
    ```

15. Uložte a zavřete soubor trigger-reboot\src\main\java\com\mycompany\app\App.java.

16. Sestavení **aktivační události – restartování** back endové aplikace a opravíte jakékoli chyby. Na příkazovém řádku přejděte do složky restartování aktivační události a spusťte následující příkaz:

    `mvn clean package -DskipTests`

## <a name="create-a-simulated-device-app"></a>Vytvoření aplikace simulovaného zařízení

V této části vytvoříte konzolovou aplikaci Java, která simuluje zařízení. Aplikace naslouchá restartování přímé volání metody ze služby IoT hub a okamžitě odpovídá na toto volání. Aplikace pak po prodlevě nějakou dobu pro simulaci restartování procesu před využívá ohlášené vlastnosti k upozornění **aktivační události – restartování** back endové aplikace dokončení restartování.

1. Ve složce dm-get-started vytvořte projekt Maven s názvem **simulated-device** pomocí následujícího příkazu na příkazovém řádku. Tady je jeden dlouhý příkaz:

    `mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=simulated-device -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false`

2. Na příkazovém řádku přejděte do složky simulated-devices.

3. Pomocí textového editoru otevřete ve složce simulated-device soubor pom.xml a přidejte následující závislost **závislosti** uzlu. Tato závislost umožňuje komunikovat se službou IoT hub pomocí balíček iot-service-client ve vaší aplikaci:

    ```xml
    <dependency>
      <groupId>com.microsoft.azure.sdk.iot</groupId>
      <artifactId>iot-device-client</artifactId>
      <version>1.3.32</version>
    </dependency>
    ```

    > [!NOTE]
    > Můžete vyhledat nejnovější verzi **iot-device-client** pomocí [vyhledávání Maven](http://search.maven.org/#search%7Cga%7C1%7Ca%3A%22iot-device-client%22%20g%3A%22com.microsoft.azure.sdk.iot%22).

4. Přidejte následující **sestavení** uzlu po **závislosti** uzlu. Tuto konfiguraci nastaví Maven k sestavení aplikace pomocí Javy 1.8:

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

5. Soubor pom.xml uložte a zavřete.

6. Pomocí textového editoru otevřete zdrojový soubor simulated-device\src\main\java\com\mycompany\app\App.java.

7. Do souboru přidejte následující příkazy pro **import**:

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

7. Do třídy **App** přidejte následující proměnné na úrovni třídy. Nahraďte `{yourdeviceconnectionstring}` připojovacím řetězcem zařízení, které jste si poznamenali v *vytvoření identity zařízení* části:

    ```java
    private static final int METHOD_SUCCESS = 200;
    private static final int METHOD_NOT_DEFINED = 404;

    private static IotHubClientProtocol protocol = IotHubClientProtocol.MQTT;
    private static String connString = "{yourdeviceconnectionstring}";
    private static DeviceClient client;
    ```

8. Implementace zpětného volání obslužné rutiny pro události stavu přímé metody, přidáním následující vnořené třídy **aplikace** třídy:

    ```java
    protected static class DirectMethodStatusCallback implements IotHubEventCallback
    {
      public void execute(IotHubStatusCode status, Object context)
      {
        System.out.println("IoT Hub responded to device method operation with status " + status.name());
      }
    }
    ```

9. Implementace zpětného volání obslužné rutiny pro události stav dvojčete zařízení, přidejte následující vnořené třídy **aplikace** třídy:

    ```java
    protected static class DeviceTwinStatusCallback implements IotHubEventCallback
    {
        public void execute(IotHubStatusCode status, Object context)
        {
            System.out.println("IoT Hub responded to device twin operation with status " + status.name());
        }
    }
    ```

10. Implementace zpětného volání obslužné rutiny pro vlastnost události, přidáním následující vnořené třídy **aplikace** třídy:

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

11. K implementaci vlákno pro simulaci restartování zařízení, přidejte následující vnořené třídy **aplikace** třídy. Vlákno uspí pět sekund a pak nastaví **lastReboot** hlášené vlastnost:

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

12. Chcete-li implementovat přímé metody v zařízení, přidejte následující vnořené třídy **aplikace** třídy. Když simulované aplikace obdrží volání **restartování** přímé metody, se vrátí volajícímu potvrzení a pak spustí vlákna ke zpracování restartování:

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

13. Upravte podpis metody **hlavní** metoda k vyvolání následující výjimky:

    ```java
    public static void main(String[] args) throws IOException, URISyntaxException
    ```

14. K vytvoření instance **DeviceClient**, přidejte následující kód, který **hlavní** metody:

    ```java
    System.out.println("Starting device client sample...");
    client = new DeviceClient(connString, protocol);
    ```

15. Zahájit naslouchání pro volání přímé metody, přidejte následující kód, který **hlavní** metody:

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

16. Vypnout simulátor zařízení, přidejte následující kód, který **hlavní** metody:

    ```java
    System.out.println("Press any key to exit...");
    Scanner scanner = new Scanner(System.in);
    scanner.nextLine();
    scanner.close();
    client.close();
    System.out.println("Shutting down...");
    ```

17. Uložte a zavřete soubor simulated-device\src\main\java\com\mycompany\app\App.java.

18. Sestavení **simulated-device** back endové aplikace a opravíte jakékoli chyby. Na příkazovém řádku přejděte do složky s Simulovaná zařízení a spusťte následující příkaz:

    `mvn clean package -DskipTests`

## <a name="run-the-apps"></a>Spouštění aplikací

Nyní jste připraveni aplikaci spustit.

1. Na příkazovém řádku ve složce simulated-device spusťte následující příkaz, který zahájit naslouchání pro volání metody restartování ze služby IoT hub:

    `mvn exec:java -Dexec.mainClass="com.mycompany.app.App"`

    ![Restartování aplikace simulovaného zařízení služby Java IoT Hub pro naslouchání volání přímé metody](./media/iot-hub-java-java-device-management-getstarted/launchsimulator.png)

2. Na příkazovém řádku ve složce restartování aktivační událost spusťte následující příkaz k volání metody restartování na zařízení s Simulovaná ze služby IoT hub:

    `mvn exec:java -Dexec.mainClass="com.mycompany.app.App"`

    ![Aplikace služby Java IoT Hub pro volání přímé metody restartování](./media/iot-hub-java-java-device-management-getstarted/triggerreboot.png)

3. Simulovaná zařízení jsou reaguje na volání přímé metody restartování:

    ![Aplikace simulovaného zařízení služby Java IoT Hub reaguje na volání přímé metody](./media/iot-hub-java-java-device-management-getstarted/respondtoreboot.png)

[!INCLUDE [iot-hub-dm-followup](../../includes/iot-hub-dm-followup.md)]