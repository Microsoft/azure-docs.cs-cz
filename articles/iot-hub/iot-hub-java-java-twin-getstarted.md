---
title: Začínáme s Azure IoT Hub dvojčata zařízení (Java) | Microsoft Docs
description: Jak používat dvojčata zařízení Azure IoT Hub přidat značky a pak použijte dotaz služby IoT Hub. Použití zařízení Azure IoT SDK pro jazyk Java k implementaci aplikace zařízení a sady SDK pro jazyk Java k implementaci aplikační služby, které přidá značky a spustí dotaz IoT Hub služby Azure IoT.
services: iot-hub
documentationcenter: java
author: dominicbetts
manager: timlt
editor: ''
ms.service: iot-hub
ms.devlang: java
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/04/2017
ms.author: dobett
ms.openlocfilehash: 8611b55f6e4f33f8946d8c7c481163166d24049c
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2018
---
# <a name="get-started-with-device-twins-java"></a>Začínáme s dvojčata zařízení (Java)

[!INCLUDE [iot-hub-selector-twin-get-started](../../includes/iot-hub-selector-twin-get-started.md)]

V tomto kurzu vytvoříte dvě aplikace konzoly v jazyce Java:

* **Přidání dotazu značky**, back-end aplikaci Java, která přidá značky a dotazuje dvojčata zařízení.
* **simulated-device**, zařízení aplikaci Java, která které připojuje ke službě IoT hub a jeho připojení sestavy podmínek použití hlášené vlastnost.

> [!NOTE]
> Článek [SDK služby Azure IoT](iot-hub-devguide-sdks.md) poskytuje informace o SDK služby Azure IoT, můžete použít k tvorbě aplikací, zařízení a back-end.

Pro absolvování tohoto kurzu potřebujete:

* Nejnovější [Java SE Development Kit 8](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)
* [Maven 3](https://maven.apache.org/install.html)
* Aktivní účet Azure. (Pokud nemáte účet, můžete vytvořit [bezplatný účet](http://azure.microsoft.com/pricing/free-trial/) si během několika minut.)

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity-portal](../../includes/iot-hub-get-started-create-device-identity-portal.md)]

## <a name="create-the-service-app"></a>Vytvořit aplikaci aplikační služby

V této části vytvoříte aplikaci Java, která přidá umístění metadat jako značku pro dvojče zařízení IoT hub spojené s **myDeviceId**. Aplikace se nejdřív dotazuje služby IoT hub pro zařízení nachází v USA a pak pro zařízení, která sestavy připojení k mobilní síti.

1. Na vývojovém počítači, vytvořte prázdnou složku s názvem `iot-java-twin-getstarted`.

1. V `iot-java-twin-getstarted` složku vytvořit projekt Maven s názvem **přidat dotazu značky** pomocí následujícího příkazu na příkazovém řádku. Všimněte si, že se jedná o jeden dlouhý příkaz:

    `mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=add-tags-query -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false`

1. Na příkazovém řádku přejděte do `add-tags-query` složky.

1. Pomocí textového editoru, otevřete `pom.xml` v soubor `add-tags-query` složky a přidejte následující závislost na **závislosti** uzlu. Tuto závislost umožňuje používat **klienta služby iot** balíček ve vaší aplikaci pro komunikaci se službou IoT hub:

    ```xml
    <dependency>
      <groupId>com.microsoft.azure.sdk.iot</groupId>
      <artifactId>iot-service-client</artifactId>
      <version>1.7.23</version>
      <type>jar</type>
    </dependency>
    ```

    > [!NOTE]
    > Můžete zkontrolovat pro nejnovější verzi **klienta služby iot** pomocí [Maven vyhledávání](http://search.maven.org/#search%7Cga%7C1%7Ca%3A%22iot-service-client%22%20g%3A%22com.microsoft.azure.sdk.iot%22).

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

1. Do třídy **App** přidejte následující proměnné na úrovni třídy. Nahraďte `{youriothubconnectionstring}` IoT hub připojovacím řetězcem jste si poznamenali v *vytvoření služby IoT Hub* části:

    ```java
    public static final String iotHubConnectionString = "{youriothubconnectionstring}";
    public static final String deviceId = "myDeviceId";

    public static final String region = "US";
    public static final String plant = "Redmond43";
    ```

1. Aktualizace **hlavní** podpis metody k patří `throws` klauzule:

    ```java
    public static void main( String[] args ) throws IOException
    ```

1. Přidejte následující kód, který **hlavní** metodu pro vytvoření **DeviceTwin** a **DeviceTwinDevice** objekty. **DeviceTwin** objekt zpracovává komunikaci se službou IoT hub. **DeviceTwinDevice** objekt představuje dvojče zařízení s jeho vlastnosti a značky:

    ```java
    // Get the DeviceTwin and DeviceTwinDevice objects
    DeviceTwin twinClient = DeviceTwin.createFromConnectionString(iotHubConnectionString);
    DeviceTwinDevice device = new DeviceTwinDevice(deviceId);
    ```

1. Přidejte následující `try/catch` zablokujte **hlavní** metoda:

    ```java
    try {
      // Code goes here
    } catch (IotHubException e) {
      System.out.println(e.getMessage());
    } catch (IOException e) {
      System.out.println(e.getMessage());
    }
    ```

1. Aktualizace **oblast** a **závodu** značky twin zařízení ve vaší dvojče zařízení, přidejte následující kód v `try` bloku:

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

1. K dotazování dvojčata zařízení IoT hub, přidejte následující kód, který `try` blok po kódu přidaném v předchozím kroku. Spustí kód nástroje dva dotazy. Každý dotaz vrací maximálně 100 zařízení:

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

1. Sestavení **přidat dotazu značky** aplikace a opravte všechny chyby. Na příkazovém řádku přejděte do `add-tags-query` složky a spusťte následující příkaz:

    `mvn clean package -DskipTests`

## <a name="create-a-device-app"></a>Vytvoření aplikace pro zařízení

V této části vytvoříte konzolovou aplikaci Java, která nastaví hodnotu hlášené vlastnost, která je odeslána do služby IoT Hub.

1. V `iot-java-twin-getstarted` složku vytvořit projekt Maven s názvem **simulated-device** pomocí následujícího příkazu na příkazovém řádku. Všimněte si, že se jedná o jeden dlouhý příkaz:

    `mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=simulated-device -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false`

1. Na příkazovém řádku přejděte do `simulated-device` složky.

1. Pomocí textového editoru, otevřete `pom.xml` v soubor `simulated-device` složky a přidejte následující závislosti na **závislosti** uzlu. Tuto závislost umožňuje používat **klienta zařízení iot** balíček ve vaší aplikaci pro komunikaci se službou IoT hub:

    ```xml
    <dependency>
      <groupId>com.microsoft.azure.sdk.iot</groupId>
      <artifactId>iot-device-client</artifactId>
      <version>1.3.32</version>
    </dependency>
    ```

    > [!NOTE]
    > Můžete zkontrolovat pro nejnovější verzi **klienta zařízení iot** pomocí [Maven vyhledávání](http://search.maven.org/#search%7Cga%7C1%7Ca%3A%22iot-device-client%22%20g%3A%22com.microsoft.azure.sdk.iot%22).

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

1. Do třídy **App** přidejte následující proměnné na úrovni třídy. Nahrazení `{youriothubname}` názvem služby IoT hub, a `{yourdevicekey}` klíčem zařízení hodnotou, kterou jste vygenerovaných *vytvoření identity zařízení* části:

    ```java
    private static String connString = "HostName={youriothubname}.azure-devices.net;DeviceId=myDeviceID;SharedAccessKey={yourdevicekey}";
    private static IotHubClientProtocol protocol = IotHubClientProtocol.MQTT;
    private static String deviceId = "myDeviceId";
    ```

    Tato ukázková aplikace používá při vytváření instance objektu **DeviceClient** proměnnou **protocol**. 

1. Přidejte následující kód, který **hlavní** metody:
    * Vytvoření klienta zařízení pro komunikaci se službou IoT Hub.
    * Vytvoření **zařízení** objekt pro uložení twin vlastnosti zařízení.

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

1. Přidejte následující kód, který **hlavní** metodu pro vytvoření **connectivityType** hlášené vlastnost a odeslat do služby IoT Hub:

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

1. Přidejte následující kód do konce **hlavní** metoda. Čekání **Enter** klíč umožňuje dobu IoT Hub, která nahlásit stav operací twin zařízení:

    ```java
    System.out.println("Press any key to exit...");

    Scanner scanner = new Scanner(System.in);
    scanner.nextLine();

    dataCollector.clean();
    client.close();
    ```

1. Uložte a zavřete `simulated-device\src\main\java\com\mycompany\app\App.java` souboru.

1. Sestavení **simulated-device** aplikace a opravte všechny chyby. Na příkazovém řádku přejděte do `simulated-device` složky a spusťte následující příkaz:

    `mvn clean package -DskipTests`

## <a name="run-the-apps"></a>Spouštění aplikací

Nyní jste připraveni ke spuštění aplikace konzoly.

1. Na příkazovém řádku v `add-tags-query` složky, spusťte následující příkaz ke spuštění **přidat dotazu značky** služby App Service:

    `mvn exec:java -Dexec.mainClass="com.mycompany.app.App"`

    ![Aplikace služby Java IoT Hub k aktualizaci hodnot značek a spouštět dotazy zařízení](media/iot-hub-java-java-twin-getstarted/service-app-1.png)

    Můžete zobrazit **závodu** a **oblast** značky, které jsou přidány do dvojče zařízení. První dotaz vrátí zařízení, ale druhý není.

1. Na příkazovém řádku v `simulated-device` složky, spusťte následující příkaz pro přidání **connectivityType** hlášených vlastnost dvojče zařízení:

    `mvn exec:java -Dexec.mainClass="com.mycompany.app.App"`

    ![Přidá klienta zařízení ** connectivityType ** hlášené vlastnost](media/iot-hub-java-java-twin-getstarted/device-app-1.png)

1. Na příkazovém řádku v `add-tags-query` složky, spusťte následující příkaz ke spuštění **přidat dotazu značky** aplikační služby ještě jednou:

    `mvn exec:java -Dexec.mainClass="com.mycompany.app.App"`

    ![Aplikace služby Java IoT Hub k aktualizaci hodnot značek a spouštět dotazy zařízení](media/iot-hub-java-java-twin-getstarted/service-app-2.png)

    Nyní zařízení odeslal **connectivityType** vlastnost ke službě IoT Hub, druhý dotaz vrátí zařízení.

## <a name="next-steps"></a>Další postup

V tomto kurzu jste nakonfigurovali novou službu IoT Hub na webu Azure Portal a potom jste vytvořili identitu zařízení v registru identit ve službě IoT Hub. Přidat metadata zařízení jako značky z back-end aplikace a aplikace na zařízení zapsal do sestavy informace o připojení k zařízení v dvojče zařízení. Také jste zjistili, jak dotazovat informací o zařízení twin pomocí dotazu jazyka SQL jako IoT Hub.

Použijte v následujících zdrojích informací další postup:

* Odesílat telemetrická data ze zařízení pomocí [Začínáme se službou IoT Hub](iot-hub-java-java-getstarted.md) kurzu.
* S kontroly nad zařízeními interaktivně (například zapnutí ventilátor z aplikace řízené uživatele) [použít přímé metody](iot-hub-java-java-direct-methods.md) kurzu.

<!-- Images. -->
[7]: ./media/iot-hub-java-java-twin-getstarted/invoke-method.png
[8]: ./media/iot-hub-java-java-twin-getstarted/device-listen.png
[9]: ./media/iot-hub-java-java-twin-getstarted/device-respond.png

<!-- Links -->
[lnk-hub-sdks]: iot-hub-devguide-sdks.md
