---
title: Začínáme se správou zařízení Azure IoT Hub (Java) | Dokumenty společnosti Microsoft
description: Jak pomocí správy zařízení Azure IoT Hub zahájit restartování vzdáleného zařízení. Pomocí sady Azure IoT zařízení SDK pro Java implementovat aplikaci simulované zařízení, která obsahuje přímou metodu a Azure IoT služby SDK pro Java k implementaci aplikace služby, která vyvolá přímou metodu.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.devlang: java
ms.topic: conceptual
ms.date: 08/20/2019
ms.openlocfilehash: f68e25a618f5c6499ccc9d76c510eab8f1650330
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77110879"
---
# <a name="get-started-with-device-management-java"></a>Začínáme se správou zařízení (Java)

[!INCLUDE [iot-hub-selector-dm-getstarted](../../includes/iot-hub-selector-dm-getstarted.md)]

V tomto kurzu získáte informace o následujících postupech:

* Pomocí portálu Azure vytvořte službu IoT Hub a vytvořte identitu zařízení ve svém centru IoT hub.

* Vytvořte simulovanou aplikaci zařízení, která implementuje přímou metodu restartování zařízení. Přímé metody jsou vyvolány z cloudu.

* Vytvořte aplikaci, která vyvolá metodu přímého restartování v aplikaci simulovaných zařízení prostřednictvím služby IoT hub. Tato aplikace pak monitoruje hlášené vlastnosti ze zařízení, aby zjistila, kdy je operace restartování dokončena.

Na konci tohoto kurzu máte dvě konzolové aplikace Java:

**simulované zařízení**. Tato aplikace:

* Připojí se k centru IoT s identitou zařízení vytvořenou dříve.

* Přijímá volání přímé metody restartování.

* Simuluje fyzické restartování.

* Hlásí čas posledního restartování prostřednictvím ohlášené vlastnosti.

**spuštění-restart**. Tato aplikace:

* Volá přímou metodu v aplikaci simulované zařízení.

* Zobrazí odpověď na přímé volání metody odeslané simulovaným zařízením.

* Zobrazí aktualizované ohlášené vlastnosti.

> [!NOTE]
> Informace o sadách SDK, které můžete použít k vytváření aplikací pro spuštění na zařízeních a back-endu vašeho řešení, naleznete v [tématu sady Azure IoT SDK](iot-hub-devguide-sdks.md).

## <a name="prerequisites"></a>Požadavky

* [Java SE Vývojová sada 8](https://docs.microsoft.com/java/azure/jdk/?view=azure-java-stable). Ujistěte se, že jste v části **Dlouhodobá podpora** vybrali **Javu 8,** abyste se dostali ke stažení pro JDK 8.

* [Maven 3](https://maven.apache.org/download.cgi)

* Aktivní účet Azure. (Pokud nemáte účet, můžete si vytvořit [bezplatný účet](https://azure.microsoft.com/pricing/free-trial/) během několika minut.)

* Zkontrolujte, zda je v bráně firewall otevřený port 8883. Ukázka zařízení v tomto článku používá protokol MQTT, který komunikuje přes port 8883. Tento port může být blokován v některých prostředích podnikové a vzdělávací sítě. Další informace a způsoby, jak tento problém vyřešit, najdete [v tématu připojení k centru IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

## <a name="create-an-iot-hub"></a>Vytvoření centra IoT

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Registrace nového zařízení v centru IoT hub

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

## <a name="get-the-iot-hub-connection-string"></a>Získání připojovacího řetězce centra IoT hub

[!INCLUDE [iot-hub-howto-device-management-shared-access-policy-text](../../includes/iot-hub-howto-device-management-shared-access-policy-text.md)]

[!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

## <a name="trigger-a-remote-reboot-on-the-device-using-a-direct-method"></a>Spuštění vzdáleného restartování zařízení pomocí přímé metody

V této části vytvoříte konzolovou aplikaci Java, která:

1. Vyvolá metodu přímého restartování v aplikaci simulované zařízení.

2. Zobrazí odpověď.

3. Dotazuje hlášené vlastnosti odeslané ze zařízení k určení, kdy je restartování dokončeno.

Tato konzolová aplikace se připojí k centru IoT Hub, aby vyvolala přímou metodu a přečetla ohlášené vlastnosti.

1. Vytvořte prázdnou složku nazvanou **dm-get-started**.

2. Ve složce **dm-get-started** vytvořte projekt Maven s názvem **trigger-reboot** pomocí následujícího příkazu na příkazovém řádku:

    ```cmd/sh
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=trigger-reboot -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

3. Na příkazovém řádku přejděte do složky **trigger-reboot.**

4. Pomocí textového editoru otevřete soubor **pom.xml** ve složce **trigger-reboot** a přidejte do uzlu **závislostí** následující závislost. Tato závislost umožňuje používat balíček iot-service-client ve vaší aplikaci ke komunikaci s centrem IoT:

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

5. Za uzel **závislostí** přidejte následující uzel **sestavení.** Tato konfigurace instruuje Maven používat Java 1.8 k vytvoření aplikace:

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

7. Pomocí textového editoru otevřete zdrojový soubor **trigger-reboot\src\main\java\com\mycompany\app\App.java.**

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

9. Do třídy **App** přidejte následující proměnné na úrovni třídy. Nahraďte `{youriothubconnectionstring}` připojovacím řetězcem ioT hubu, který jste dříve zkopírovali v [části Získání připojovacího řetězce centra IoT Hub](#get-the-iot-hub-connection-string):

    ```java
    public static final String iotHubConnectionString = "{youriothubconnectionstring}";
    public static final String deviceId = "myDeviceId";

    private static final String methodName = "reboot";
    private static final Long responseTimeout = TimeUnit.SECONDS.toSeconds(30);
    private static final Long connectTimeout = TimeUnit.SECONDS.toSeconds(5);
    ```

10. Chcete-li implementovat vlákno, které čte hlášené vlastnosti z dvojčete zařízení každých 10 sekund, přidejte do třídy **App** následující vnořenou třídu:

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

11. Upravte podpis **hlavní** metody tak, aby vyvolávat následující výjimku:

    ```java
    public static void main(String[] args) throws IOException
    ```

12. Chcete-li vyvolat metodu přímého restartování na simulovaném zařízení, nahraďte kód v **hlavní** metodě následujícím kódem:

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

13. Chcete-li spustit vlákno k dotazování na hlášené vlastnosti ze simulovaného zařízení, přidejte do **hlavní** metody následující kód:

    ```java
    ShowReportedProperties showReportedProperties = new ShowReportedProperties();
    ExecutorService executor = Executors.newFixedThreadPool(1);
    executor.execute(showReportedProperties);
    ```

14. Chcete-li aplikaci zastavit, přidejte k **hlavní** metodě následující kód:

    ```java
    System.out.println("Press ENTER to exit.");
    System.in.read();
    executor.shutdownNow();
    System.out.println("Shutting down sample...");
    ```

15. Uložte a zavřete soubor **trigger-reboot\src\main\java\com\mycompany\app\App.java.**

16. Vytvořte **aplikaci back-end spouště-restartu** a opravte všechny chyby. Na příkazovém řádku přejděte do složky **trigger-reboot** a spusťte následující příkaz:

    ```cmd/sh
    mvn clean package -DskipTests
    ```

## <a name="create-a-simulated-device-app"></a>Vytvoření aplikace simulovaného zařízení

V této části vytvoříte konzolovou aplikaci Java, která simuluje zařízení. Aplikace naslouchá volání přímé metody restartování z vašeho centra IoT hub a okamžitě reaguje na toto volání. Aplikace pak přejde na chvíli simulovat proces restartování před používá ohlášenou vlastnost upozornit **aktivační událost restartovat** back-end aplikace, že restartování je dokončena.

1. Ve složce **dm-get-started** vytvořte projekt Maven s názvem **simulované zařízení** pomocí následujícího příkazu na příkazovém řádku:

    ```cmd/sh
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=simulated-device -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

2. Na příkazovém řádku přejděte do složky **simulované zařízení.**

3. Pomocí textového editoru otevřete soubor **pom.xml** ve složce **simulované zařízení** a přidejte následující závislost do uzlu **závislostí.** Tato závislost umožňuje používat balíček iot-service-client ve vaší aplikaci ke komunikaci s centrem IoT:

    ```xml
    <dependency>
      <groupId>com.microsoft.azure.sdk.iot</groupId>
      <artifactId>iot-device-client</artifactId>
      <version>1.17.5</version>
    </dependency>
    ```

    > [!NOTE]
    > Můžete vyhledat nejnovější verzi **iot-device-client** pomocí [vyhledávání Maven](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22iot-device-client%22%20g%3A%22com.microsoft.azure.sdk.iot%22).

4. Přidejte následující závislost do uzlu **závislostí.** Tato závislost konfiguruje NOP pro fasádu protokolování Apache [SLF4J,](https://www.slf4j.org/) kterou sada SDK klienta zařízení používá k implementaci protokolování. Tato konfigurace je volitelná, ale pokud ji vynecháme, může se při spuštění aplikace zobrazit upozornění v konzole. Další informace o protokolování v sdk klienta zařízení najdete v [tématu protokolování ukázek](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-samples/readme.md#logging) pro soubor Readme *zařízení Azure IoT pro Java.*

    ```xml
    <dependency>
      <groupId>org.slf4j</groupId>
      <artifactId>slf4j-nop</artifactId>
      <version>1.7.28</version>
    </dependency>
    ```

5. Za uzel **závislostí** přidejte následující uzel **sestavení.** Tato konfigurace instruuje Maven používat Java 1.8 k vytvoření aplikace:

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

7. Pomocí textového editoru otevřete zdrojový soubor **simulované zařízení\src\main\java\com\mycompany\app\App.java.**

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

9. Do třídy **App** přidejte následující proměnné na úrovni třídy. Nahraďte `{yourdeviceconnectionstring}` připojovacím řetězcem zařízení, který jste si zapsali v části [Registrovat nové zařízení v centru IoT:](#register-a-new-device-in-the-iot-hub)

    ```java
    private static final int METHOD_SUCCESS = 200;
    private static final int METHOD_NOT_DEFINED = 404;

    private static IotHubClientProtocol protocol = IotHubClientProtocol.MQTT;
    private static String connString = "{yourdeviceconnectionstring}";
    private static DeviceClient client;
    ```

10. Chcete-li implementovat obslužnou rutinu zpětného volání pro události stavu přímé metody, přidejte do třídy **App** následující vnořenou třídu:

    ```java
    protected static class DirectMethodStatusCallback implements IotHubEventCallback
    {
      public void execute(IotHubStatusCode status, Object context)
      {
        System.out.println("IoT Hub responded to device method operation with status " + status.name());
      }
    }
    ```

11. Chcete-li implementovat obslužnou rutinu zpětného volání pro události stavu dvojčete zařízení, přidejte do třídy **App** následující vnořenou třídu:

    ```java
    protected static class DeviceTwinStatusCallback implements IotHubEventCallback
    {
        public void execute(IotHubStatusCode status, Object context)
        {
            System.out.println("IoT Hub responded to device twin operation with status " + status.name());
        }
    }
    ```

12. Chcete-li implementovat obslužnou rutinu zpětného volání pro události vlastností, přidejte do třídy **App** následující vnořenou třídu:

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

13. Chcete-li implementovat vlákno pro simulaci restartování zařízení, přidejte do třídy **App** následující vnořenou třídu. Vlákno přepne po dobu pěti sekund a potom nastaví **lastReboot** ohlášenou vlastnost:

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

14. Chcete-li implementovat přímou metodu na zařízení, přidejte následující vnořenou třídu do třídy **App.** Když simulovaná aplikace přijme volání metody **přímé restartování,** vrátí potvrzení volajícímu a pak spustí vlákno pro zpracování restartování:

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

15. Upravte podpis **hlavní** metody tak, aby vyvolávat následující výjimky:

    ```java
    public static void main(String[] args) throws IOException, URISyntaxException
    ```

16. Chcete-li vytvořit instanci **DeviceClient**, nahraďte kód v **hlavní** metodě následujícím kódem:

    ```java
    System.out.println("Starting device client sample...");
    client = new DeviceClient(connString, protocol);
    ```

17. Chcete-li začít naslouchat volání přímé metody, přidejte následující kód k **hlavní** metodě:

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

18. Chcete-li vypnout simulátor zařízení, přidejte do **hlavní** metody následující kód:

    ```java
    System.out.println("Press any key to exit...");
    Scanner scanner = new Scanner(System.in);
    scanner.nextLine();
    scanner.close();
    client.close();
    System.out.println("Shutting down...");
    ```

19. Uložte a zavřete soubor simulované zařízení\src\main\java\com\mycompany\app\App.java.

20. Vytvořte aplikaci **simulované zařízení** a opravte všechny chyby. Na příkazovém řádku přejděte do složky **simulovaných zařízení** a spusťte následující příkaz:

    ```cmd/sh
    mvn clean package -DskipTests
    ```

## <a name="run-the-apps"></a>Spouštění aplikací

Nyní jste připraveni ke spuštění aplikací.

1. Na příkazovém řádku ve složce **simulovaných zařízení** spusťte následující příkaz a začněte naslouchat voláním metod restartování z centra IoT Hub:

    ```cmd/sh
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
    ```

    ![Java IoT Hub simulované zařízení aplikace pro poslech volání přímé metody restartu](./media/iot-hub-java-java-device-management-getstarted/launchsimulator.png)

2. Na příkazovém řádku ve složce **trigger-reboot** spusťte následující příkaz a zavolejte metodu restartování na simulovaném zařízení z centra IoT:

    ```cmd/sh
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
    ```

    ![Java IoT Hub servisní aplikace pro volání metody přímého restartování](./media/iot-hub-java-java-device-management-getstarted/triggerreboot.png)

3. Simulované zařízení reaguje na volání přímé metody restartování:

    ![Aplikace simulovaná zařízení java ioT hubreaguje na volání přímé metody](./media/iot-hub-java-java-device-management-getstarted/respondtoreboot.png)

[!INCLUDE [iot-hub-dm-followup](../../includes/iot-hub-dm-followup.md)]
