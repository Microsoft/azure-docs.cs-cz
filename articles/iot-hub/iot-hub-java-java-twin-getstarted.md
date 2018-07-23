---
title: Začínáme s dvojčaty zařízení Azure IoT Hub (Java) | Dokumentace Microsoftu
description: Jak používat dvojče zařízení Azure IoT Hub pro přidání značek a následné použití k dotazu služby IoT Hub. Implementace aplikace pro zařízení a služby Azure IoT SDK pro Javu k implementaci app service, které přidá značky a spustí dotaz služby IoT Hub pomocí zařízení Azure IoT SDK pro Javu.
author: dominicbetts
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.devlang: java
ms.topic: conceptual
ms.date: 07/04/2017
ms.author: dobett
ms.openlocfilehash: b8884cafbf250b9d7a88219b5647addafee9904a
ms.sourcegitcommit: bf522c6af890984e8b7bd7d633208cb88f62a841
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/20/2018
ms.locfileid: "39186896"
---
# <a name="get-started-with-device-twins-java"></a>Začínáme s dvojčaty zařízení (Java)

[!INCLUDE [iot-hub-selector-twin-get-started](../../includes/iot-hub-selector-twin-get-started.md)]

V tomto kurzu vytvoříte dvě konzolové aplikace Java:

* **Přidat dotaz značky**, back endové aplikace v Javě, které přidá značky a dotazuje dvojčata zařízení.
* **simulated-device**, zařízení aplikace v Javě, která se připojuje ke službě IoT hub a sestav stavu připojení pomocí ohlášených vlastností.

> [!NOTE]
> Tento článek [sad SDK Azure IoT](iot-hub-devguide-sdks.md) poskytuje informace o Azure IoT SDK, že vám pomůže vytvářet aplikace pro zařízení i back-end.

Pro absolvování tohoto kurzu potřebujete:

* Nejnovější [Java SE Development Kit 8](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)
* [Maven 3](https://maven.apache.org/install.html)
* Aktivní účet Azure. (Pokud účet nemáte, můžete vytvořit [bezplatný účet](http://azure.microsoft.com/pricing/free-trial/) během několika minut.)

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity-portal](../../includes/iot-hub-get-started-create-device-identity-portal.md)]

## <a name="create-the-service-app"></a>Vytvoření aplikace služby

V této části vytvoříte aplikaci v Javě, která přidá umístění metadat jako značku, kterou chcete dvojčeti zařízení ve službě IoT Hub přidružené **myDeviceId**. Aplikace se nejdřív dotazuje služby IoT hub pro zařízení se nachází ve Spojených státech a pro zařízení, které podléhají připojení k mobilní síti.

1. Na svém vývojovém počítači vytvořit prázdnou složku s názvem `iot-java-twin-getstarted`.

1. V `iot-java-twin-getstarted` složku, vytvořte projekt Maven s názvem **přidat dotaz značky** pomocí následujícího příkazu na příkazovém řádku. Všimněte si, že se jedná o jeden dlouhý příkaz:

    `mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=add-tags-query -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false`

1. Na příkazovém řádku, přejděte `add-tags-query` složky.

1. Pomocí textového editoru otevřete `pom.xml` soubor `add-tags-query` složky a přidejte následující závislost **závislosti** uzlu. Tato závislost vám umožní použít **iot-service-client** balíčku v aplikaci tak, aby komunikovat se službou IoT hub:

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

1. Uložte a zavřete `pom.xml` souboru.

1. Pomocí textového editoru, otevřete `add-tags-query\src\main\java\com\mycompany\app\App.java` souboru.

1. Do souboru přidejte následující příkazy pro **import**:

    ```java
    import com.microsoft.azure.sdk.iot.service.devicetwin.*;
    import com.microsoft.azure.sdk.iot.service.exceptions.IotHubException;

    import java.io.IOException;
    import java.util.HashSet;
    import java.util.Set;
    ```

1. Do třídy **App** přidejte následující proměnné na úrovni třídy. Nahraďte `{youriothubconnectionstring}` jste si poznamenali v IoT hubu připojovacím řetězcem *vytvoření služby IoT Hub* části:

    ```java
    public static final String iotHubConnectionString = "{youriothubconnectionstring}";
    public static final String deviceId = "myDeviceId";

    public static final String region = "US";
    public static final String plant = "Redmond43";
    ```

1. Aktualizace **hlavní** podpis metody pro patří `throws` klauzule:

    ```java
    public static void main( String[] args ) throws IOException
    ```

1. Přidejte následující kód, který **hlavní** metodu pro vytvoření **DeviceTwin** a **DeviceTwinDevice** objekty. **DeviceTwin** objekt zpracovává komunikaci s centrem IoT. **DeviceTwinDevice** objekt představuje dvojče zařízení pomocí její vlastnosti a značky:

    ```java
    // Get the DeviceTwin and DeviceTwinDevice objects
    DeviceTwin twinClient = DeviceTwin.createFromConnectionString(iotHubConnectionString);
    DeviceTwinDevice device = new DeviceTwinDevice(deviceId);
    ```

1. Přidejte následující `try/catch` bloku **hlavní** metody:

    ```java
    try {
      // Code goes here
    } catch (IotHubException e) {
      System.out.println(e.getMessage());
    } catch (IOException e) {
      System.out.println(e.getMessage());
    }
    ```

1. Aktualizovat **oblasti** a **zařízení** značky dvojčat zařízení v dvojče zařízení, přidejte následující kód `try` blok:

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

1. K dotazování dvojčat zařízení ve službě IoT hub, přidejte následující kód, který `try` blok po kódu přidaném v předchozím kroku. Kód spustí dva dotazy. Každý dotaz vrací maximálně 100 zařízení:

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

1. Uložte a zavřete `add-tags-query\src\main\java\com\mycompany\app\App.java` souboru

1. Sestavení **přidat dotaz značky** aplikace a opravíte jakékoli chyby. Na příkazovém řádku, přejděte `add-tags-query` složky a spusťte následující příkaz:

    `mvn clean package -DskipTests`

## <a name="create-a-device-app"></a>Vytvoření aplikace pro zařízení

V této části vytvoříte konzolovou aplikaci Java, která nastaví hodnotu ohlášené vlastnosti, která je odeslána do služby IoT Hub.

1. V `iot-java-twin-getstarted` složku, vytvořte projekt Maven s názvem **simulated-device** pomocí následujícího příkazu na příkazovém řádku. Všimněte si, že se jedná o jeden dlouhý příkaz:

    `mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=simulated-device -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false`

1. Na příkazovém řádku, přejděte `simulated-device` složky.

1. Pomocí textového editoru otevřete `pom.xml` soubor `simulated-device` složky a přidat následující závislosti, které **závislosti** uzlu. Tato závislost vám umožní použít **iot-device-client** balíčku v aplikaci tak, aby komunikovat se službou IoT hub:

    ```xml
    <dependency>
      <groupId>com.microsoft.azure.sdk.iot</groupId>
      <artifactId>iot-device-client</artifactId>
      <version>1.3.32</version>
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

1. Uložte a zavřete `pom.xml` souboru.

1. Pomocí textového editoru, otevřete `simulated-device\src\main\java\com\mycompany\app\App.java` souboru.

1. Do souboru přidejte následující příkazy pro **import**:

    ```java
    import com.microsoft.azure.sdk.iot.device.*;
    import com.microsoft.azure.sdk.iot.device.DeviceTwin.*;

    import java.io.IOException;
    import java.net.URISyntaxException;
    import java.util.Scanner;
    ```

1. Do třídy **App** přidejte následující proměnné na úrovni třídy. Nahrazení `{youriothubname}` názvem služby IoT hub, a `{yourdevicekey}` klíčem zařízení hodnotou, kterou jste vygenerovali v *vytvoření identity zařízení* části:

    ```java
    private static String connString = "HostName={youriothubname}.azure-devices.net;DeviceId=myDeviceID;SharedAccessKey={yourdevicekey}";
    private static IotHubClientProtocol protocol = IotHubClientProtocol.MQTT;
    private static String deviceId = "myDeviceId";
    ```

    Tato ukázková aplikace používá při vytváření instance objektu **DeviceClient** proměnnou **protocol**. 

1. Přidejte následující kód, který **hlavní** metodu:
    * Vytvoření klienta zařízení ke komunikaci s centrem IoT.
    * Vytvoření **zařízení** objekt pro uložení vlastnosti dvojčete zařízení.

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

1. Přidejte následující kód, který **hlavní** metodu pro vytvoření **connectivityType** hlášené vlastnost a jeho odeslání do služby IoT Hub:

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
      client.close();
      System.out.println("Shutting down...");
    }
    ```

1. Přidejte následující kód do konce **hlavní** metody. Čeká se **Enter** klíč umožňuje aplikaci čas pro službu IoT Hub informuje o stavu operace dvojčete zařízení:

    ```java
    System.out.println("Press any key to exit...");

    Scanner scanner = new Scanner(System.in);
    scanner.nextLine();

    dataCollector.clean();
    client.close();
    ```

1. Uložte a zavřete `simulated-device\src\main\java\com\mycompany\app\App.java` souboru.

1. Sestavení **simulated-device** aplikace a opravíte jakékoli chyby. Na příkazovém řádku, přejděte `simulated-device` složky a spusťte následující příkaz:

    `mvn clean package -DskipTests`

## <a name="run-the-apps"></a>Spouštění aplikací

Nyní jste připraveni ke spuštění aplikace konzoly.

1. Na příkazovém řádku v `add-tags-query` složky, spusťte následující příkaz pro spuštění **přidat dotaz značky** služby App Service:

    `mvn exec:java -Dexec.mainClass="com.mycompany.app.App"`

    ![Aplikace služby Java IoT Hub pro aktualizaci hodnoty značek a spouštět dotazy na zařízení](media/iot-hub-java-java-twin-getstarted/service-app-1.png)

    Zobrazí se **zařízení** a **oblasti** značky přidané do dvojčete zařízení. První dotaz vrátí zařízení, ale nikoli druhý.

1. Na příkazovém řádku v `simulated-device` složky, spusťte následující příkaz pro přidání **connectivityType** ohlášených vlastností pro dvojče zařízení:

    `mvn exec:java -Dexec.mainClass="com.mycompany.app.App"`

    ![Přidá klienta zařízení ** connectivityType ** hlášené vlastnost](media/iot-hub-java-java-twin-getstarted/device-app-1.png)

1. Na příkazovém řádku v `add-tags-query` složky, spusťte následující příkaz pro spuštění **přidat dotaz značky** služby app Service ještě jednou:

    `mvn exec:java -Dexec.mainClass="com.mycompany.app.App"`

    ![Aplikace služby Java IoT Hub pro aktualizaci hodnoty značek a spouštět dotazy na zařízení](media/iot-hub-java-java-twin-getstarted/service-app-2.png)

    Teď zařízení odeslala **connectivityType** vlastnost do služby IoT Hub, druhý dotaz vrací zařízení.

## <a name="next-steps"></a>Další postup

V tomto kurzu jste nakonfigurovali novou službu IoT Hub na webu Azure Portal a potom jste vytvořili identitu zařízení v registru identit ve službě IoT Hub. Přidá metadata zařízení jako značky z back endové aplikace a aplikace pro zařízení zapsáno do sestavy informací o připojení k zařízení ve dvojčeti zařízení. Také jste zjistili, jak zadávat dotazy na informace o dvojče zařízení pomocí služby IoT Hub SQL jako dotazovací jazyk.

Pomocí následujících zdrojích se dozvíte jak:

* Odesílání telemetrie ze zařízení s [Začínáme se službou IoT Hub](quickstart-send-telemetry-java.md) kurzu.
* Ovládací prvek zařízení interaktivně (například zapnutí ventilátor z aplikace řízené uživatelem) [použití přímých metod](quickstart-control-device-java.md) kurzu.

<!-- Images. -->
[7]: ./media/iot-hub-java-java-twin-getstarted/invoke-method.png
[8]: ./media/iot-hub-java-java-twin-getstarted/device-listen.png
[9]: ./media/iot-hub-java-java-twin-getstarted/device-respond.png

<!-- Links -->
[lnk-hub-sdks]: iot-hub-devguide-sdks.md
