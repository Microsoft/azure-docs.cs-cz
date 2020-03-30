---
title: Začínáme s dvojčaty zařízení Azure IoT Hub (Java) | Dokumenty společnosti Microsoft
description: Jak použít dvojčata zařízení Azure IoT Hub k přidání značek a pak použít dotaz služby IoT Hub. Pomocí sady Azure IoT zařízení SDK pro Javu implementovat aplikaci zařízení a Azure IoT služby SDK pro Java k implementaci aplikace služby, která přidá značky a spustí dotaz Služby IoT Hub.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.devlang: java
ms.topic: conceptual
ms.date: 08/26/2019
ms.openlocfilehash: e0114c37b2204a7ad1d7b0cf9c7f336dcd85883a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77110488"
---
# <a name="get-started-with-device-twins-java"></a>Začínáme s dvojčaty zařízení (Java)

[!INCLUDE [iot-hub-selector-twin-get-started](../../includes/iot-hub-selector-twin-get-started.md)]

V tomto kurzu vytvoříte dvě konzolové aplikace Java:

* **add-tags-query**, java back-end aplikace, která přidává značky a dotazy dvojčata zařízení.
* **simulované zařízení**, aplikace pro zařízení Java, která se připojuje k centru IoT hub a hlásí stav připojení pomocí ohlášené vlastnosti.

> [!NOTE]
> Článek [sady Azure IoT SDK obsahuje](iot-hub-devguide-sdks.md) informace o sadách Azure IoT SDK, které můžete použít k vytváření aplikací pro zařízení i back-end.

## <a name="prerequisites"></a>Požadavky

* [Java SE Vývojová sada 8](https://docs.microsoft.com/java/azure/jdk/?view=azure-java-stable). Ujistěte se, že jste v části **Dlouhodobá podpora** vybrali **Javu 8,** abyste se dostali ke stažení pro JDK 8.

* [Maven 3](https://maven.apache.org/download.cgi)

* Aktivní účet Azure. (Pokud nemáte účet, můžete si vytvořit [bezplatný účet](https://azure.microsoft.com/pricing/free-trial/) během několika minut.)

* Zkontrolujte, zda je v bráně firewall otevřený port 8883. Ukázka zařízení v tomto článku používá protokol MQTT, který komunikuje přes port 8883. Tento port může být blokován v některých prostředích podnikové a vzdělávací sítě. Další informace a způsoby, jak tento problém vyřešit, najdete [v tématu připojení k centru IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

## <a name="create-an-iot-hub"></a>Vytvoření centra IoT

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Registrace nového zařízení v centru IoT hub

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="get-the-iot-hub-connection-string"></a>Získání připojovacího řetězce centra IoT hub

[!INCLUDE [iot-hub-howto-twin-shared-access-policy-text](../../includes/iot-hub-howto-twin-shared-access-policy-text.md)]

[!INCLUDE [iot-hub-include-find-custom-connection-string](../../includes/iot-hub-include-find-custom-connection-string.md)]

## <a name="create-the-service-app"></a>Vytvoření aplikace služby

V této části vytvoříte aplikaci Java, která přidá metadata umístění jako značku dvojčete zařízení v centru IoT Hub přidružené **k myDeviceId**. Aplikace nejprve dotazuje službu IoT hub pro zařízení umístěná v USA a potom pro zařízení, která hlásí připojení k mobilní síti.

1. Ve vývojovém počítači vytvořte prázdnou složku s názvem **iot-java-twin-getstarted**.

2. Ve složce **iot-java-twin-getstarted** vytvořte projekt Maven s názvem **add-tags-query** pomocí následujícího příkazu na příkazovém řádku:

    ```cmd/sh
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=add-tags-query -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

3. Na příkazovém řádku přejděte do složky **add-tags-query.**

4. Pomocí textového editoru otevřete soubor **pom.xml** ve složce **add-tags-query** a přidejte do uzlu **závislostí** následující závislost. Tato závislost umožňuje používat balíček **iot-service-client** ve vaší aplikaci ke komunikaci s centrem IoT:

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

5. Za uzel **závislostí** přidejte následující uzel **sestavení.** Tato konfigurace instruuje Maven používat Java 1.8 k vytvoření aplikace.

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

7. Pomocí textového editoru otevřete soubor **add-tags-query\src\main\java\com\mycompany\app\App.java.**

8. Do souboru přidejte následující příkazy pro **import**:

    ```java
    import com.microsoft.azure.sdk.iot.service.devicetwin.*;
    import com.microsoft.azure.sdk.iot.service.exceptions.IotHubException;

    import java.io.IOException;
    import java.util.HashSet;
    import java.util.Set;
    ```

9. Do třídy **App** přidejte následující proměnné na úrovni třídy. Nahraďte `{youriothubconnectionstring}` připojovacím řetězcem centra IoT, který jste zkopírovali v [části Získání připojovacího řetězce centra IoT hub](#get-the-iot-hub-connection-string).

    ```java
    public static final String iotHubConnectionString = "{youriothubconnectionstring}";
    public static final String deviceId = "myDeviceId";

    public static final String region = "US";
    public static final String plant = "Redmond43";
    ```

10. Aktualizujte podpis **hlavní** metody `throws` tak, aby obsahoval následující klauzuli:

    ```java
    public static void main( String[] args ) throws IOException
    ```

11. Nahraďte kód v **hlavní** metodě následujícím kódem pro vytvoření objektů **DeviceTwin** a **DeviceTwinDevice.** Objekt **DeviceTwin** zpracovává komunikaci s centrem IoT. Objekt **DeviceTwinDevice** představuje dvojče zařízení s jeho vlastnostmi a značkami:

    ```java
    // Get the DeviceTwin and DeviceTwinDevice objects
    DeviceTwin twinClient = DeviceTwin.createFromConnectionString(iotHubConnectionString);
    DeviceTwinDevice device = new DeviceTwinDevice(deviceId);
    ```

12. Do `try/catch` **hlavní** metody přidejte následující blok:

    ```java
    try {
      // Code goes here
    } catch (IotHubException e) {
      System.out.println(e.getMessage());
    } catch (IOException e) {
      System.out.println(e.getMessage());
    }
    ```

13. Chcete-li aktualizovat značky dvojčete **zařízení pro oblast** a `try` **zařízení** v dvojčeti zařízení, přidejte do bloku následující kód:

    ```java
    // Get the device twin from IoT Hub
    System.out.println("Device twin before update:");
    twinClient.getTwin(device);
    System.out.println(device);

    // Update device twin tags if they are different
    // from the existing values
    String currentTags = device.tagsToString();
    if ((!currentTags.contains("region=" + region) && !currentTags.contains("plant=" + plant))) {
      // Create the tags and attach them to the DeviceTwinDevice object
      Set<Pair> tags = new HashSet<Pair>();
      tags.add(new Pair("region", region));
      tags.add(new Pair("plant", plant));
      device.setTags(tags);

      // Update the device twin in IoT Hub
      System.out.println("Updating device twin");
      twinClient.updateTwin(device);
    }

    // Retrieve the device twin with the tag values from IoT Hub
    System.out.println("Device twin after update:");
    twinClient.getTwin(device);
    System.out.println(device);
    ```

14. Chcete-li dotaz dvojčata zařízení v centru IoT, přidejte následující kód do `try` bloku za kód, který jste přidali v předchozím kroku. Kód spustí dva dotazy. Každý dotaz vrátí maximálně 100 zařízení.

    ```java
    // Query the device twins in IoT Hub
    System.out.println("Devices in Redmond:");

    // Construct the query
    SqlQuery sqlQuery = SqlQuery.createSqlQuery("*", SqlQuery.FromType.DEVICES, "tags.plant='Redmond43'", null);

    // Run the query, returning a maximum of 100 devices
    Query twinQuery = twinClient.queryTwin(sqlQuery.getQuery(), 100);
    while (twinClient.hasNextDeviceTwin(twinQuery)) {
      DeviceTwinDevice d = twinClient.getNextDeviceTwin(twinQuery);
      System.out.println(d.getDeviceId());
    }

    System.out.println("Devices in Redmond using a cellular network:");

    // Construct the query
    sqlQuery = SqlQuery.createSqlQuery("*", SqlQuery.FromType.DEVICES, "tags.plant='Redmond43' AND properties.reported.connectivityType = 'cellular'", null);

    // Run the query, returning a maximum of 100 devices
    twinQuery = twinClient.queryTwin(sqlQuery.getQuery(), 3);
    while (twinClient.hasNextDeviceTwin(twinQuery)) {
      DeviceTwinDevice d = twinClient.getNextDeviceTwin(twinQuery);
      System.out.println(d.getDeviceId());
    }
    ```

15. Uložení a zavření souboru **add-tags-query\src\main\java\com\mycompany\app\App.java**

16. Vytvořte aplikaci **pro přidání značek dotazů** a opravte všechny chyby. Na příkazovém řádku přejděte do složky **add-tags-query** a spusťte následující příkaz:

    ```cmd/sh
    mvn clean package -DskipTests
    ```

## <a name="create-a-device-app"></a>Vytvoření aplikace pro zařízení

V této části vytvoříte konzolovou aplikaci Java, která nastaví ohlášenou hodnotu vlastnosti, která je odeslána do služby IoT Hub.

1. Ve složce **iot-java-twin-getstarted** vytvořte projekt Maven s názvem **simulované zařízení** pomocí následujícího příkazu na příkazovém řádku:

    ```cmd/sh
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=simulated-device -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

2. Na příkazovém řádku přejděte do složky **simulované zařízení.**

3. Pomocí textového editoru otevřete soubor **pom.xml** ve složce **simulovaných zařízení** a přidejte následující závislosti do uzlu **závislostí.** Tato závislost umožňuje používat balíček **iot-device-client** ve vaší aplikaci ke komunikaci s centrem IoT Hub.

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

7. Pomocí textového editoru otevřete soubor **Simulated-device\src\main\java\com\mycompany\app\App.java.**

8. Do souboru přidejte následující příkazy pro **import**:

    ```java
    import com.microsoft.azure.sdk.iot.device.*;
    import com.microsoft.azure.sdk.iot.device.DeviceTwin.*;

    import java.io.IOException;
    import java.net.URISyntaxException;
    import java.util.Scanner;
    ```

9. Do třídy **App** přidejte následující proměnné na úrovni třídy. Nahraďte `{yourdeviceconnectionstring}` připojovacím řetězcem zařízení, který jste zkopírovali v [části Registrace nového zařízení v centru IoT](#register-a-new-device-in-the-iot-hub)hub .

    ```java
    private static String connString = "{yourdeviceconnectionstring}";
    private static IotHubClientProtocol protocol = IotHubClientProtocol.MQTT;
    private static String deviceId = "myDeviceId";
    ```

    Tato ukázková aplikace používá při vytváření instance objektu **DeviceClient** proměnnou **protocol**.

10. Přidejte do třídy **App** následující metodu, abyste vytiskli informace o aktualizacích dvojčat:

    ```java
    protected static class DeviceTwinStatusCallBack implements IotHubEventCallback {
        @Override
        public void execute(IotHubStatusCode status, Object context) {
          System.out.println("IoT Hub responded to device twin operation with status " + status.name());
        }
      }
    ```

11. Nahraďte kód v **hlavní** metodě následujícím kódem, aby:

    * Vytvořte klienta zařízení pro komunikaci s službou IoT Hub.

    * Vytvořte objekt **Device** pro uložení vlastností dvojčete zařízení.

    ```java
    DeviceClient client = new DeviceClient(connString, protocol);

    // Create a Device object to store the device twin properties
    Device dataCollector = new Device() {
      // Print details when a property value changes
      @Override
      public void PropertyCall(String propertyKey, Object propertyValue, Object context) {
        System.out.println(propertyKey + " changed to " + propertyValue);
      }
    };
    ```

12. Přidejte následující kód do **hlavní** metody k vytvoření **vlastnosti connectivityType** a odešlete ji do služby IoT Hub:

    ```java
    try {
      // Open the DeviceClient and start the device twin services.
      client.open();
      client.startDeviceTwin(new DeviceTwinStatusCallBack(), null, dataCollector, null);

      // Create a reported property and send it to your IoT hub.
      dataCollector.setReportedProp(new Property("connectivityType", "cellular"));
      client.sendReportedProperties(dataCollector.getReportedProp());
    }
    catch (Exception e) {
      System.out.println("On exception, shutting down \n" + " Cause: " + e.getCause() + " \n" + e.getMessage());
      dataCollector.clean();
      client.closeNow();
      System.out.println("Shutting down...");
    }
    ```

13. Přidejte následující kód na konec **hlavní** metody. Čekání **na** enter klíč umožňuje čas pro IoT Hub hlásit stav operací dvojčete zařízení.

    ```java
    System.out.println("Press any key to exit...");

    Scanner scanner = new Scanner(System.in);
    scanner.nextLine();

    dataCollector.clean();
    client.close();
    ```

14. Upravte podpis metody **Main** tak, aby zahrnoval následující výjimky:

     ```java
     public static void main(String[] args) throws URISyntaxException, IOException
     ```

15. Uložte a zavřete soubor **simulované zařízení\src\main\java\com\mycompany\app\App.java.**

16. Vytvořte aplikaci **simulované zařízení** a opravte všechny chyby. Na příkazovém řádku přejděte do složky **simulovaných zařízení** a spusťte následující příkaz:

    ```cmd/sh
    mvn clean package -DskipTests
    ```

## <a name="run-the-apps"></a>Spouštění aplikací

Nyní jste připraveni ke spuštění konzolových aplikací.

1. Na příkazovém řádku ve složce **add-tags-query** spusťte následující příkaz a spusťte aplikaci **služby add-tags-query:**

    ```cmd/sh
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
    ```

    ![Aplikace služby Java IoT Hub pro aktualizaci hodnot značek a spouštění dotazů na zařízení](./media/iot-hub-java-java-twin-getstarted/service-app-1.png)

    Můžete vidět **značky zařízení** a **oblasti** přidané do dvojčete zařízení. První dotaz vrátí zařízení, ale druhý ne.

2. Na příkazovém řádku ve složce **simulované zařízení** spusťte následující příkaz a přidejte vlastnost **connectivityType** hlášenou do dvojčete zařízení:

    ```cmd/sh
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
    ```

    ![Klient zařízení přidá **connectivityType** ohlášenou vlastnost](./media/iot-hub-java-java-twin-getstarted/device-app-1.png)

3. Na příkazovém řádku ve složce **add-tags-query** spusťte podruhé následující příkaz a spusťte aplikaci **služby add-tags-query:**

    ```cmd/sh
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
    ```

    ![Aplikace služby Java IoT Hub pro aktualizaci hodnot značek a spouštění dotazů na zařízení](./media/iot-hub-java-java-twin-getstarted/service-app-2.png)

    Teď, když vaše zařízení odeslala **vlastnost connectivityType** do služby IoT Hub, druhý dotaz vrátí vaše zařízení.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste nakonfigurovali novou službu IoT Hub na webu Azure Portal a potom jste vytvořili identitu zařízení v registru identit ve službě IoT Hub. Metadata zařízení jste přidali jako značky z back-endové aplikace a napsali jste aplikaci zařízení, která nahlásí informace o připojení zařízení do dvojčete zařízení. Také jste se naučili, jak dotaz ovat informace o dvojčeti zařízení pomocí dotazovacího jazyka IoT Hub podobné SQL.

Pomocí následujících zdrojů se dozvíte, jak:

* Odesílejte telemetrii ze zařízení pomocí výukového [programu Začínáme s ioT hubem.](quickstart-send-telemetry-java.md)

* Ovládejte zařízení interaktivně (například zapnutí ventilátoru z uživatelem řízené aplikace) pomocí kurzu [Použít přímé metody.](quickstart-control-device-java.md)
