---
title: Začínáme s neznámkou zařízení s Azure IoT Hub (Java) | Microsoft Docs
description: Jak používat vlákna v zařízeních Azure IoT Hub k přidávání značek a k následnému použití dotazu IoT Hub. Pomocí sady SDK pro zařízení Azure IoT pro jazyk Java implementujete aplikaci pro zařízení a sadu SDK služby Azure IoT pro jazyk Java k implementaci aplikace služby, která přidá značky a spustí dotaz IoT Hub.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.devlang: java
ms.topic: conceptual
ms.date: 08/26/2019
ms.custom: mqtt, devx-track-java
ms.openlocfilehash: 68908b7d62b8211c202262112831d5d2b523f594
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/17/2020
ms.locfileid: "92146968"
---
# <a name="get-started-with-device-twins-java"></a>Začínáme se zdvojením zařízení (Java)

[!INCLUDE [iot-hub-selector-twin-get-started](../../includes/iot-hub-selector-twin-get-started.md)]

V tomto kurzu vytvoříte dvě konzolové aplikace Java:

* příkaz **Add-tags-Query**, aplikace pro back-end Java, která přidává značky a dotaz na vlákna zařízení.
* **simulované zařízení**, aplikace Java, která se připojuje ke službě IoT Hub a hlásí podmínku připojení pomocí hlášené vlastnosti.

> [!NOTE]
> V článku sady [SDK Azure IoT](iot-hub-devguide-sdks.md) najdete informace o sadách SDK Azure IoT, které můžete použít k vytvoření zařízení i back-endové aplikace.

## <a name="prerequisites"></a>Předpoklady

* [Java se Development Kit 8](/java/azure/jdk/?view=azure-java-stable). Ujistěte se, že jste v části **Dlouhodobá podpora** vybrali **Java 8** , abyste se dostali ke stažení pro JDK 8.

* [Maven 3](https://maven.apache.org/download.cgi)

* Aktivní účet Azure. (Pokud účet nemáte, můžete si během několika minut vytvořit [bezplatný účet](https://azure.microsoft.com/pricing/free-trial/) .)

* Ujistěte se, že je v bráně firewall otevřený port 8883. Ukázka zařízení v tomto článku používá protokol MQTT, který komunikuje přes port 8883. Tento port může být blokovaný v některých podnikových a vzdělávacích prostředích sítě. Další informace a způsoby, jak tento problém obejít, najdete v tématu [připojení k IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

## <a name="create-an-iot-hub"></a>Vytvoření centra IoT

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Registrace nového zařízení ve službě IoT Hub

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="get-the-iot-hub-connection-string"></a>Získání připojovacího řetězce centra IoT Hub

[!INCLUDE [iot-hub-howto-twin-shared-access-policy-text](../../includes/iot-hub-howto-twin-shared-access-policy-text.md)]

[!INCLUDE [iot-hub-include-find-custom-connection-string](../../includes/iot-hub-include-find-custom-connection-string.md)]

## <a name="create-the-service-app"></a>Vytvoření aplikace služby

V této části vytvoříte aplikaci Java, která přidá metadata umístění jako značku do vlákna zařízení v IoT Hub přidružená k **myDeviceId**. Aplikace nejdřív dotazuje centrum IoT na zařízení umístěná v USA a pak u zařízení, která nahlásí mobilní připojení k síti.

1. Ve vývojovém počítači vytvořte prázdnou složku s názvem **IoT-Java-zdvojené-getstarted**.

2. Ve složce **IoT-Java-zdvojené-getstarted** vytvořte pomocí následujícího příkazu na příkazovém řádku projekt Maven s názvem **Add-tags-Query** :

    ```cmd/sh
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=add-tags-query -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

3. Na příkazovém řádku přejděte do složky **Add-tags-Query** .

4. Pomocí textového editoru otevřete soubor **pom.xml** ve složce **Add-tags-Query** a přidejte následující závislost na uzel **závislosti** . Tato závislost vám umožní komunikovat se službou IoT Hub pomocí balíčku **IoT-Service-Client** ve vaší aplikaci:

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

5. Přidejte následující uzel **sestavení** za uzel **závislosti** . Tato konfigurace instruuje Maven, aby k sestavení aplikace používala Java 1,8.

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

7. Pomocí textového editoru otevřete soubor **Add-tags-query\src\main\java\com\mycompany\app\App.Java** .

8. Do souboru přidejte následující příkazy pro **import**:

    ```java
    import com.microsoft.azure.sdk.iot.service.devicetwin.*;
    import com.microsoft.azure.sdk.iot.service.exceptions.IotHubException;

    import java.io.IOException;
    import java.util.HashSet;
    import java.util.Set;
    ```

9. Do třídy **App** přidejte následující proměnné na úrovni třídy. Nahraďte `{youriothubconnectionstring}` připojovacím řetězcem centra IoT, který jste zkopírovali v [části získání připojovacího řetězce centra IoT Hub](#get-the-iot-hub-connection-string).

    ```java
    public static final String iotHubConnectionString = "{youriothubconnectionstring}";
    public static final String deviceId = "myDeviceId";

    public static final String region = "US";
    public static final String plant = "Redmond43";
    ```

10. Aktualizujte podpis metody **Main** tak, aby zahrnoval následující `throws` klauzuli:

    ```java
    public static void main( String[] args ) throws IOException
    ```

11. Nahraďte kód v metodě **Main** následujícím kódem pro vytvoření objektů **DeviceTwin** a **DeviceTwinDevice** . Objekt **DeviceTwin** zpracovává komunikaci se službou IoT Hub. Objekt **DeviceTwinDevice** představuje nevláken zařízení s jeho vlastnostmi a značkami:

    ```java
    // Get the DeviceTwin and DeviceTwinDevice objects
    DeviceTwin twinClient = DeviceTwin.createFromConnectionString(iotHubConnectionString);
    DeviceTwinDevice device = new DeviceTwinDevice(deviceId);
    ```

12. `try/catch`Do metody **Main** přidejte následující blok:

    ```java
    try {
      // Code goes here
    } catch (IotHubException e) {
      System.out.println(e.getMessage());
    } catch (IOException e) {
      System.out.println(e.getMessage());
    }
    ```

13. Pokud chcete ve **svém zařízení aktualizovat** dvojitou značku **oblasti** a zařízení, přidejte do bloku následující kód `try` :

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

14. Pokud chcete zadat dotaz na vlákna ve službě IoT Hub, přidejte do bloku následující kód `try` po kódu, který jste přidali v předchozím kroku. Kód spouští dva dotazy. Každý dotaz vrátí maximálně 100 zařízení.

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

15. Uložte a zavřete soubor **Add-tags-query\src\main\java\com\mycompany\app\App.Java**

16. Sestavte aplikaci pro **dotaz Add-tags** a opravte všechny chyby. Na příkazovém řádku přejděte do složky **Add-tags-Query** a spusťte následující příkaz:

    ```cmd/sh
    mvn clean package -DskipTests
    ```

## <a name="create-a-device-app"></a>Vytvoření aplikace pro zařízení

V této části vytvoříte konzolovou aplikaci Java, která nastaví hodnotu hlášené vlastnosti odeslané na IoT Hub.

1. Ve složce **IoT-Java-zdvojené-getstarted** vytvořte pomocí následujícího příkazu na příkazovém řádku projekt Maven s názvem **simulované zařízení** :

    ```cmd/sh
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=simulated-device -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

2. Na příkazovém řádku přejděte do složky **simulovaného zařízení** .

3. Pomocí textového editoru otevřete soubor **pom.xml** ve složce **simulovaného zařízení** a přidejte následující závislosti do uzlu **závislosti** . Tato závislost vám umožní komunikovat se službou IoT Hub pomocí balíčku **IoT-Device-Client** ve vaší aplikaci.

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

7. Pomocí textového editoru otevřete soubor **Simulated-device\src\main\java\com\mycompany\app\App.Java** .

8. Do souboru přidejte následující příkazy pro **import**:

    ```java
    import com.microsoft.azure.sdk.iot.device.*;
    import com.microsoft.azure.sdk.iot.device.DeviceTwin.*;

    import java.io.IOException;
    import java.net.URISyntaxException;
    import java.util.Scanner;
    ```

9. Do třídy **App** přidejte následující proměnné na úrovni třídy. Nahraďte `{yourdeviceconnectionstring}` připojovacím řetězcem zařízení, který jste zkopírovali v [části registrace nového zařízení ve službě IoT Hub](#register-a-new-device-in-the-iot-hub).

    ```java
    private static String connString = "{yourdeviceconnectionstring}";
    private static IotHubClientProtocol protocol = IotHubClientProtocol.MQTT;
    private static String deviceId = "myDeviceId";
    ```

    Tato ukázková aplikace používá při vytváření instance objektu **DeviceClient** proměnnou **protocol**.

10. Přidejte následující metodu do třídy **App** pro tisk informací o zdvojených aktualizacích:

    ```java
    protected static class DeviceTwinStatusCallBack implements IotHubEventCallback {
        @Override
        public void execute(IotHubStatusCode status, Object context) {
          System.out.println("IoT Hub responded to device twin operation with status " + status.name());
        }
      }
    ```

11. Nahraďte kód v metodě **Main** následujícím kódem:

    * Vytvořte klienta zařízení pro komunikaci s IoT Hub.

    * Vytvořte objekt **zařízení** pro uložení vlastností, které jsou v zařízení.

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

12. Do metody **Main** přidejte následující kód pro vytvoření **connectivityType** hlášené vlastnosti a odeslání do IoT Hub:

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

13. Na konec metody **Main** přidejte následující kód. Čekání na **zadání klávesy ENTER** umožní, aby čas IoT Hub nahlásil stav zdvojených operací zařízení.

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

15. Uložte a zavřete soubor **Simulated-device\src\main\java\com\mycompany\app\App.Java** .

16. Sestavte aplikaci **simulovaného zařízení** a opravte všechny chyby. Na příkazovém řádku přejděte do složky **simulovaného zařízení** a spusťte následující příkaz:

    ```cmd/sh
    mvn clean package -DskipTests
    ```

## <a name="run-the-apps"></a>Spouštění aplikací

Nyní jste připraveni spustit konzolové aplikace.

1. Na příkazovém řádku ve složce **Add-tags-Query** spusťte následující příkaz ke spuštění aplikace služby pro dotaz rutiny **Add-tags** :

    ```cmd/sh
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
    ```

    ![Snímek obrazovky, který zobrazuje výstup příkazu pro spuštění aplikace služby dotazů na přidání značek](./media/iot-hub-java-java-twin-getstarted/service-app-1.png)

    Můžete zobrazit značky **zařízení** a **oblasti** přidané do vlákna zařízení. První dotaz vrátí vaše zařízení, ale druhá ne.

2. Na příkazovém řádku ve složce **simulovaného zařízení** spusťte následující příkaz, který přidá vlastnost **connectivityType** hlášené do vlákna zařízení:

    ```cmd/sh
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
    ```

    ![Klient zařízení přidá vlastnost nahlášený typ připojení.](./media/iot-hub-java-java-twin-getstarted/device-app-1.png)

3. Na příkazovém řádku ve složce **Add-tags-Query** spusťte následující příkaz pro spuštění aplikace služby pro dotaz rutiny **Add-tags-Tags** :

    ```cmd/sh
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
    ```

    ![Aplikace služby Java IoT Hub pro aktualizaci hodnot značek a spuštění dotazů na zařízení](./media/iot-hub-java-java-twin-getstarted/service-app-2.png)

    Teď, když zařízení odeslalo vlastnost **connectivityType** na IoT Hub, druhý dotaz vrátí vaše zařízení.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste nakonfigurovali novou službu IoT Hub na webu Azure Portal a potom jste vytvořili identitu zařízení v registru identit ve službě IoT Hub. Metadata zařízení jste přidali jako značky z back-endové aplikace a zapsali jsme aplikaci pro zařízení, která oznamuje informace o připojení zařízení v podobě vlákna v zařízení. Zjistili jste také, jak zadat dotaz na informace o zdvojeném zařízení pomocí dotazovacího jazyka IoT Hub, který se podobá jazyku SQL.

Pomocí následujících zdrojů se naučíte:

* Pomocí kurzu [Začínáme s IoT Hub](quickstart-send-telemetry-java.md) odešlete telemetrii ze zařízení.

* Interaktivní řízení zařízení (například zapnutí ventilátoru z aplikace řízené uživatelem) pomocí kurzu [použití přímých metod](quickstart-control-device-java.md) .