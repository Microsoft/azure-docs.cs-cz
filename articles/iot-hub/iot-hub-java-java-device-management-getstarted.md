---
title: Začínáme se správou zařízení v Azure IoT Hub (Java) | Microsoft Docs
description: Jak používat správu zařízení v Azure IoT Hub k zahájení restartování vzdáleného zařízení. Pomocí sady SDK pro zařízení Azure IoT pro jazyk Java implementujete aplikaci simulovaného zařízení, která zahrnuje přímou metodu a sadu SDK služby Azure IoT pro jazyk Java k implementaci aplikace služby, která vyvolá přímou metodu.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.devlang: java
ms.topic: conceptual
ms.date: 08/20/2019
ms.custom: mqtt, devx-track-java
ms.openlocfilehash: f05e1a458bc83fe4042c4b6cf35d9aa2095868ef
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "102217954"
---
# <a name="get-started-with-device-management-java"></a>Začínáme se správou zařízení (Java)

[!INCLUDE [iot-hub-selector-dm-getstarted](../../includes/iot-hub-selector-dm-getstarted.md)]

V tomto kurzu získáte informace o následujících postupech:

* Pomocí Azure Portal můžete vytvořit IoT Hub a vytvořit identitu zařízení ve službě IoT Hub.

* Vytvořte aplikaci simulovaného zařízení, která implementuje přímou metodu pro restartování zařízení. Přímé metody jsou vyvolány z cloudu.

* Vytvořte aplikaci, která vyvolá přímou metodu restart v aplikaci simulovaného zařízení ve službě IoT Hub. Tato aplikace následně monitoruje hlášené vlastnosti ze zařízení, aby zjistila, že se operace restartování dokončila.

Na konci tohoto kurzu máte dvě konzolové aplikace Java:

**simulované zařízení**. Tato aplikace:

* Připojí se ke službě IoT Hub s identitou zařízení vytvořenou dříve.

* Přijme volání přímé metody restart.

* Simuluje fyzický restart.

* Oznamuje čas posledního restartování prostřednictvím hlášené vlastnosti.

**Trigger-restart**. Tato aplikace:

* Volá přímo metodu v aplikaci simulovaného zařízení.

* Zobrazí odpověď na volání přímé metody odesílané simulovaným zařízením.

* Zobrazí aktualizované hlášené vlastnosti.

> [!NOTE]
> Informace o sadách SDK, které můžete použít k sestavení aplikací pro spouštění na zařízeních a back-endu vašeho řešení, najdete v tématu sady [SDK služby Azure IoT](iot-hub-devguide-sdks.md).

## <a name="prerequisites"></a>Předpoklady

* [Java se Development Kit 8](/java/azure/jdk/). Ujistěte se, že jste v části **Dlouhodobá podpora** vybrali **Java 8** , abyste se dostali ke stažení pro JDK 8.

* [Maven 3](https://maven.apache.org/download.cgi)

* Aktivní účet Azure. (Pokud účet nemáte, můžete si během několika minut vytvořit [bezplatný účet](https://azure.microsoft.com/pricing/free-trial/) .)

* Ujistěte se, že je v bráně firewall otevřený port 8883. Ukázka zařízení v tomto článku používá protokol MQTT, který komunikuje přes port 8883. Tento port může být blokovaný v některých podnikových a vzdělávacích prostředích sítě. Další informace a způsoby, jak tento problém obejít, najdete v tématu [připojení k IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

## <a name="create-an-iot-hub"></a>Vytvoření centra IoT

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Registrace nového zařízení ve službě IoT Hub

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

## <a name="get-the-iot-hub-connection-string"></a>Získání připojovacího řetězce centra IoT Hub

[!INCLUDE [iot-hub-howto-device-management-shared-access-policy-text](../../includes/iot-hub-howto-device-management-shared-access-policy-text.md)]

[!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

## <a name="trigger-a-remote-reboot-on-the-device-using-a-direct-method"></a>Aktivace vzdáleného restartování zařízení pomocí přímé metody

V této části vytvoříte konzolovou aplikaci Java, která:

1. Vyvolá přímou metodu restartu v aplikaci simulovaného zařízení.

2. Zobrazí odpověď.

3. Provede dotazování hlášených vlastností odeslaných ze zařízení za účelem určení, kdy se restart dokončí.

Tato Konzolová aplikace se připojí k vašemu IoT Hub a vyvolá přímo metodu a přečte hlášené vlastnosti.

1. Vytvořte prázdnou složku s názvem **DM-Get-Started**.

2. Ve složce **DM-Get-Started** vytvořte pomocí následujícího příkazu na příkazovém řádku projekt Maven s názvem **Trigger-restart** .

    ```cmd/sh
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=trigger-reboot -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

3. Na příkazovém řádku přejděte do složky **Trigger-restart** .

4. Pomocí textového editoru otevřete soubor **pom.xml** ve složce **Trigger-restart** a přidejte následující závislost na uzel **závislosti** . Tato závislost vám umožní komunikovat se službou IoT Hub pomocí balíčku IoT-Service-Client ve vaší aplikaci:

    ```xml
    <dependency>
      <groupId>com.microsoft.azure.sdk.iot</groupId>
      <artifactId>iot-service-client</artifactId>
      <version>1.17.1</version>
      <type>jar</type>
    </dependency>
    ```

    > [!NOTE]
    > Můžete vyhledat nejnovější verzi **iot-service-client** pomocí [vyhledávání Maven](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22iot-service-client%22%20g%3A%22com.microsoft.azure.sdk.iot%22).

5. Přidejte následující uzel **sestavení** za uzel **závislosti** . Tato konfigurace instruuje Maven, aby k sestavení aplikace použil Java 1,8:

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

6. Uložte a zavřete soubor **pom.xml**.

7. Pomocí textového editoru otevřete zdrojový soubor **Trigger-reboot\src\main\java\com\mycompany\app\App.Java** .

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

9. Do třídy **App** přidejte následující proměnné na úrovni třídy. Nahraďte `{youriothubconnectionstring}` připojovacím řetězcem IoT Hub, který jste dříve zkopírovali v části [získání připojovacího řetězce centra IoT](#get-the-iot-hub-connection-string):

    ```java
    public static final String iotHubConnectionString = "{youriothubconnectionstring}";
    public static final String deviceId = "myDeviceId";

    private static final String methodName = "reboot";
    private static final Long responseTimeout = TimeUnit.SECONDS.toSeconds(30);
    private static final Long connectTimeout = TimeUnit.SECONDS.toSeconds(5);
    ```

10. K implementaci vlákna, které přečte hlášené vlastnosti ze zařízení je v každém 10 sekundách, přidejte do třídy **aplikace** následující vnořenou třídu:

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

11. Upravte podpis metody **Main** tak, aby vyvolal následující výjimku:

    ```java
    public static void main(String[] args) throws IOException
    ```

12. Chcete-li vyvolat přímou metodu restartu na simulovaném zařízení, nahraďte kód v metodě **Main** následujícím kódem:

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

13. Chcete-li spustit vlákno pro dotazování hlášených vlastností z simulovaného zařízení, přidejte do metody **Main** následující kód:

    ```java
    ShowReportedProperties showReportedProperties = new ShowReportedProperties();
    ExecutorService executor = Executors.newFixedThreadPool(1);
    executor.execute(showReportedProperties);
    ```

14. K tomu, abyste mohli aplikaci zastavit, přidejte do metody **Main** následující kód:

    ```java
    System.out.println("Press ENTER to exit.");
    System.in.read();
    executor.shutdownNow();
    System.out.println("Shutting down sample...");
    ```

15. Uložte a zavřete soubor **Trigger-reboot\src\main\java\com\mycompany\app\App.Java** .

16. Vytvořte **Trigger – restartujte** back-end aplikaci a opravte všechny chyby. Na příkazovém řádku přejděte do složky **Trigger-restart** a spusťte následující příkaz:

    ```cmd/sh
    mvn clean package -DskipTests
    ```

## <a name="create-a-simulated-device-app"></a>Vytvoření aplikace simulovaného zařízení

V této části vytvoříte konzolovou aplikaci Java, která simuluje zařízení. Aplikace čeká na restartování přímé volání metody ze služby IoT Hub a okamžitě reaguje na toto volání. Aplikace následně v režimu spánku, aby simulovala proces restartování před tím, než použije hlášenou vlastnost, aby upozornila back-end aplikaci na **aktivační událost** , že se restart dokončil.

1. Ve složce **DM-Get-Started** vytvořte pomocí následujícího příkazu na příkazovém řádku projekt Maven s názvem **simulované zařízení** :

    ```cmd/sh
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=simulated-device -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

2. Na příkazovém řádku přejděte do složky **simulovaného zařízení** .

3. Pomocí textového editoru otevřete soubor **pom.xml** ve složce **simulovaného zařízení** a přidejte následující závislost na uzel **závislosti** . Tato závislost vám umožní komunikovat se službou IoT Hub pomocí balíčku IoT-Service-Client ve vaší aplikaci:

    ```xml
    <dependency>
      <groupId>com.microsoft.azure.sdk.iot</groupId>
      <artifactId>iot-device-client</artifactId>
      <version>1.17.5</version>
    </dependency>
    ```

    > [!NOTE]
    > Můžete vyhledat nejnovější verzi **iot-device-client** pomocí [vyhledávání Maven](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22iot-device-client%22%20g%3A%22com.microsoft.azure.sdk.iot%22).

4. Přidejte následující závislost na uzel **závislosti** . Tato závislost konfiguruje NOP pro přihlašování k Apache [SLF4J](https://www.slf4j.org/) , které používá klientská sada SDK pro zařízení k implementaci protokolování. Tato konfigurace je volitelná, ale pokud ji vynecháte, může se při spuštění aplikace zobrazit upozornění v konzole. Další informace o protokolování v sadě SDK klienta zařízení najdete v tématu [protokolování](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-samples/readme.md#logging) v ukázkách pro soubor Readme pro *zařízení Azure IoT SDK pro jazyk Java* .

    ```xml
    <dependency>
      <groupId>org.slf4j</groupId>
      <artifactId>slf4j-nop</artifactId>
      <version>1.7.28</version>
    </dependency>
    ```

5. Přidejte následující uzel **sestavení** za uzel **závislosti** . Tato konfigurace instruuje Maven, aby k sestavení aplikace použil Java 1,8:

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

6. Uložte a zavřete soubor **pom.xml**.

7. Pomocí textového editoru otevřete zdrojový soubor **Simulated-device\src\main\java\com\mycompany\app\App.Java** .

8. Do souboru přidejte následující příkazy pro **import**:

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

9. Do třídy **App** přidejte následující proměnné na úrovni třídy. Nahraďte `{yourdeviceconnectionstring}` připojovacím řetězcem zařízení, který jste si poznamenali v části [Registrace nového zařízení v centru IoT Hub](#register-a-new-device-in-the-iot-hub) :

    ```java
    private static final int METHOD_SUCCESS = 200;
    private static final int METHOD_NOT_DEFINED = 404;

    private static IotHubClientProtocol protocol = IotHubClientProtocol.MQTT;
    private static String connString = "{yourdeviceconnectionstring}";
    private static DeviceClient client;
    ```

10. Pro implementaci obslužné rutiny zpětného volání pro události stavu přímé metody přidejte do třídy **aplikace** následující vnořenou třídu:

    ```java
    protected static class DirectMethodStatusCallback implements IotHubEventCallback
    {
      public void execute(IotHubStatusCode status, Object context)
      {
        System.out.println("IoT Hub responded to device method operation with status " + status.name());
      }
    }
    ```

11. K implementaci obslužné rutiny zpětného volání pro události stavu zařízení s dvojitým stavem přidejte do třídy **aplikace** následující vnořenou třídu:

    ```java
    protected static class DeviceTwinStatusCallback implements IotHubEventCallback
    {
        public void execute(IotHubStatusCode status, Object context)
        {
            System.out.println("IoT Hub responded to device twin operation with status " + status.name());
        }
    }
    ```

12. Chcete-li implementovat obslužnou rutinu zpětného volání pro události vlastností, přidejte do třídy **aplikace** následující vnořenou třídu:

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

13. K implementaci vlákna pro simulaci restartování zařízení přidejte do třídy **App** následující vnořenou třídu. Vlákno se v režimu spánku po dobu pěti sekund a pak nastaví vlastnost **lastRebooted** :

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

14. K implementaci přímé metody na zařízení přidejte do třídy **App** následující vnořenou třídu. Když simulovaná aplikace přijme volání přímé metody **restartu** , vrátí volajícímu a potom spustí vlákno pro zpracování restartování:

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

15. Upravte podpis metody **Main** tak, aby se vyvolaly následující výjimky:

    ```java
    public static void main(String[] args) throws IOException, URISyntaxException
    ```

16. Chcete-li vytvořit instanci **DeviceClient**, nahraďte kód v metodě **Main** následujícím kódem:

    ```java
    System.out.println("Starting device client sample...");
    client = new DeviceClient(connString, protocol);
    ```

17. Chcete-li začít naslouchat volání přímých metod, přidejte do metody **Main** následující kód:

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

18. Chcete-li vypnout simulátor zařízení, přidejte do metody **Main** následující kód:

    ```java
    System.out.println("Press any key to exit...");
    Scanner scanner = new Scanner(System.in);
    scanner.nextLine();
    scanner.close();
    client.close();
    System.out.println("Shutting down...");
    ```

19. Uložte a zavřete soubor simulated-device\src\main\java\com\mycompany\app\App.java.

20. Sestavte aplikaci **simulovaného zařízení** a opravte všechny chyby. Na příkazovém řádku přejděte do složky **simulovaného zařízení** a spusťte následující příkaz:

    ```cmd/sh
    mvn clean package -DskipTests
    ```

## <a name="run-the-apps"></a>Spouštění aplikací

Nyní jste připraveni spustit aplikace.

1. Na příkazovém řádku ve složce **simulovaného zařízení** spusťte následující příkaz, který zahájí naslouchání volání metod restartování ze služby IoT Hub:

    ```cmd/sh
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
    ```

    ![Aplikace pro simulované zařízení Java IoT Hub k naslouchání volání přímých metod restartování](./media/iot-hub-java-java-device-management-getstarted/launchsimulator.png)

2. Na příkazovém řádku ve složce **Trigger-restart** spusťte následující příkaz, který zavolá metodu restart na simulovaném zařízení ve službě IoT Hub:

    ```cmd/sh
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
    ```

    ![Aplikace služby Java IoT Hub pro volání přímé metody restartování](./media/iot-hub-java-java-device-management-getstarted/triggerreboot.png)

3. Simulované zařízení reaguje na volání přímé metody restartování:

    ![Aplikace s simulovaným zařízením Java IoT Hub reaguje na přímé volání metody](./media/iot-hub-java-java-device-management-getstarted/respondtoreboot.png)

[!INCLUDE [iot-hub-dm-followup](../../includes/iot-hub-dm-followup.md)]