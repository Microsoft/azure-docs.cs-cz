---
title: Plánování úloh službou Azure IoT Hub (Java) | Microsoft Docs
description: Popisuje, jak naplánovat úlohu služby Azure IoT Hub vyvolat přímé metodu a nastavit požadovanou vlastnost na několika zařízeních. Použití zařízení Azure IoT SDK pro jazyk Java k implementaci aplikace simulovaného zařízení a sady SDK pro jazyk Java k implementaci aplikační služby, který chcete spustit úlohu služby Azure IoT.
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
ms.date: 07/10/2017
ms.author: dobett
ms.openlocfilehash: af853a378a36c97edd74ea99fc5dcce92a073fea
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2018
---
# <a name="schedule-and-broadcast-jobs-java"></a>Úlohy plán a všesměrového vysílání (Java)

[!INCLUDE [iot-hub-selector-schedule-jobs](../../includes/iot-hub-selector-schedule-jobs.md)]

K plánování a sledování úloh, které aktualizují miliony zařízení pomocí služby Azure IoT Hub. Pomocí úloh:

* Aktualizace požadovaných vlastností
* Aktualizace značky
* Vyvolání metody přímé

Úloha zabalí jednu z těchto akcí a sleduje provádění na sadu zařízení. Dotaz zařízení twin definuje sadu zařízení, která provede úlohu proti. Například můžete použít back-end aplikačním úlohu k vyvolání přímé metodu na 10 000 zařízení, která restartování zařízení. Zadejte sadu zařízení s dotazem twin zařízení a úlohu naplánovat na spuštění v budoucnosti. Průběh úlohy sleduje jako každé zařízení obdrží a provést přímý metodu restartování.

Další informace o každém z těchto funkcí najdete v tématu:

* Dvojče zařízení a vlastností: [začít pracovat s dvojčata zařízení](iot-hub-java-java-twin-getstarted.md)
* Přímé metody: [Příručka vývojáře pro službu IoT Hub - přímé metody](iot-hub-devguide-direct-methods.md) a [kurz: použijte přímý metody](iot-hub-java-java-direct-methods.md)

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

V tomto kurzu získáte informace o následujících postupech:

* Vytvoření aplikace zařízení, která implementuje přímé metodu s názvem **lockDoor**. Aplikace zařízení také přijme požadovanou vlastnost změny z back-end aplikace.
* Vytvořit aplikaci back-end, která vytvoří úlohu k volání **lockDoor** přímá metoda na několika zařízeních. Jiná úloha odešle aktualizace požadované vlastností do více zařízení.

Na konci tohoto kurzu máte zařízení konzolovou aplikaci java a back-end konzolovou aplikaci java:

**simulated-device** která se připojuje ke službě IoT hub, implementuje **lockDoor** přímá metoda a zpracovává potřeby změny vlastností.

**plán úlohy** , úlohy využít k volání **lockDoor** přímá metoda a aktualizovat vlastnosti twin požadovaného zařízení na několika zařízeních.

> [!NOTE]
> Článek [SDK služby Azure IoT](iot-hub-devguide-sdks.md) poskytuje informace o SDK služby Azure IoT, můžete použít k tvorbě aplikací, zařízení a back-end.

## <a name="prerequisites"></a>Požadavky

Pro absolvování tohoto kurzu potřebujete:

* Nejnovější [Java SE Development Kit 8](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)
* [Maven 3](https://maven.apache.org/install.html)
* Aktivní účet Azure. (Pokud nemáte účet, můžete vytvořit [bezplatný účet](http://azure.microsoft.com/pricing/free-trial/) si během několika minut.)

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity-portal](../../includes/iot-hub-get-started-create-device-identity-portal.md)]

Pokud dáváte přednost pro vytvoření identity zařízení prostřednictvím kódu programu, najdete v příslušné části [připojení zařízení do služby IoT hub pomocí jazyka Java](iot-hub-java-java-getstarted.md#create-a-device-identity) článku. Můžete také [IoT rozšíření pro Azure CLI 2.0](https://github.com/Azure/azure-iot-cli-extension) nástroje pro přidání zařízení do služby IoT hub.

## <a name="create-the-service-app"></a>Vytvořit aplikaci aplikační služby

V této části vytvoříte konzolovou aplikaci Java, která používá úloh:

* Volání **lockDoor** přímá metoda na několika zařízeních.
* Požadované vlastnosti poslat více zařízení.

Vytvoření aplikace:

1. Na vývojovém počítači, vytvořte prázdnou složku s názvem `iot-java-schedule-jobs`.

1. V `iot-java-schedule-jobs` složku vytvořit projekt Maven s názvem **plán úloh** pomocí následujícího příkazu na příkazovém řádku. Všimněte si, že se jedná o jeden dlouhý příkaz:

    `mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=schedule-jobs -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false`

1. Na příkazovém řádku přejděte do `schedule-jobs` složky.

1. Pomocí textového editoru, otevřete `pom.xml` v soubor `schedule-jobs` složky a přidejte následující závislost na **závislosti** uzlu. Tuto závislost umožňuje používat **klienta služby iot** balíček ve vaší aplikaci pro komunikaci se službou IoT hub:

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

1. Pomocí textového editoru, otevřete `schedule-jobs\src\main\java\com\mycompany\app\App.java` souboru.

1. Do souboru přidejte následující příkazy pro **import**:

    ```java
    import com.microsoft.azure.sdk.iot.service.devicetwin.DeviceTwinDevice;
    import com.microsoft.azure.sdk.iot.service.devicetwin.Pair;
    import com.microsoft.azure.sdk.iot.service.devicetwin.Query;
    import com.microsoft.azure.sdk.iot.service.devicetwin.SqlQuery;
    import com.microsoft.azure.sdk.iot.service.jobs.JobClient;
    import com.microsoft.azure.sdk.iot.service.jobs.JobResult;
    import com.microsoft.azure.sdk.iot.service.jobs.JobStatus;

    import java.util.Date;
    import java.time.Instant;
    import java.util.HashSet;
    import java.util.Set;
    import java.util.UUID;
    ```

1. Do třídy **App** přidejte následující proměnné na úrovni třídy. Nahraďte `{youriothubconnectionstring}` IoT hub připojovacím řetězcem jste si poznamenali v *vytvoření služby IoT Hub* části:

    ```java
    public static final String iotHubConnectionString = "{youriothubconnectionstring}";
    public static final String deviceId = "myDeviceId";

    // How long the job is permitted to run without
    // completing its work on the set of devices
    private static final long maxExecutionTimeInSeconds = 30;
    ```

1. Přidejte následující metodu do **aplikace** třída naplánovat úlohu, která aktualizuje **vytváření** a **podlaží** požadovaných vlastností v dvojče zařízení:

    ```java
    private static JobResult scheduleJobSetDesiredProperties(JobClient jobClient, String jobId) {
      DeviceTwinDevice twin = new DeviceTwinDevice(deviceId);
      Set<Pair> desiredProperties = new HashSet<Pair>();
      desiredProperties.add(new Pair("Building", 43));
      desiredProperties.add(new Pair("Floor", 3));
      twin.setDesiredProperties(desiredProperties);
      // Optimistic concurrency control
      twin.setETag("*");

      // Schedule the update twin job to run now
      // against a single device
      System.out.println("Schedule job " + jobId + " for device " + deviceId);
      try {
        JobResult jobResult = jobClient.scheduleUpdateTwin(jobId, 
          "deviceId='" + deviceId + "'",
          twin,
          new Date(),
          maxExecutionTimeInSeconds);
        return jobResult;
      } catch (Exception e) {
        System.out.println("Exception scheduling desired properties job: " + jobId);
        System.out.println(e.getMessage());
        return null;
      }
    }
    ```

1. Naplánování úlohy k volání **lockDoor** metoda, přidejte následující metodu do **aplikace** třídy:

    ```java
    private static JobResult scheduleJobCallDirectMethod(JobClient jobClient, String jobId) {
      // Schedule a job now to call the lockDoor direct method
      // against a single device. Response and connection
      // timeouts are set to 5 seconds.
      System.out.println("Schedule job " + jobId + " for device " + deviceId);
      try {
        JobResult jobResult = jobClient.scheduleDeviceMethod(jobId,
          "deviceId='" + deviceId + "'",
          "lockDoor",
          5L, 5L, null,
          new Date(),
          maxExecutionTimeInSeconds);
        return jobResult;
      } catch (Exception e) {
        System.out.println("Exception scheduling direct method job: " + jobId);
        System.out.println(e.getMessage());
        return null;
      }
    };
    ```

1. Monitorování úlohy, přidejte následující metodu do **aplikace** třídy:

    ```java
    private static void monitorJob(JobClient jobClient, String jobId) {
      try {
        JobResult jobResult = jobClient.getJob(jobId);
        if(jobResult == null)
        {
          System.out.println("No JobResult for: " + jobId);
          return;
        }
        // Check the job result until it's completed
        while(jobResult.getJobStatus() != JobStatus.completed)
        {
          Thread.sleep(100);
          jobResult = jobClient.getJob(jobId);
          System.out.println("Status " + jobResult.getJobStatus() + " for job " + jobId);
        }
        System.out.println("Final status " + jobResult.getJobStatus() + " for job " + jobId);
      } catch (Exception e) {
        System.out.println("Exception monitoring job: " + jobId);
        System.out.println(e.getMessage());
        return;
      }
    }
    ```

1. Se dotázat na podrobnosti o úlohách, které jste spustili, přidejte následující metodu:

    ```java
    private static void queryDeviceJobs(JobClient jobClient, String start) throws Exception {
      System.out.println("\nQuery device jobs since " + start);

      // Create a jobs query using the time the jobs started
      Query deviceJobQuery = jobClient
          .queryDeviceJob(SqlQuery.createSqlQuery("*", SqlQuery.FromType.JOBS, "devices.jobs.startTimeUtc > '" + start + "'", null).getQuery());

      // Iterate over the list of jobs and print the details
      while (jobClient.hasNextJob(deviceJobQuery)) {
        System.out.println(jobClient.getNextJob(deviceJobQuery));
      }
    }
    ```

1. Aktualizace **hlavní** podpis metody k patří `throws` klauzule:

    ```java
    public static void main( String[] args ) throws Exception
    ```

1. Ke spuštění a monitorování dvě úlohy postupně, přidejte následující kód, který **hlavní** metoda:

    ```java
    // Record the start time
    String start = Instant.now().toString();

    // Create JobClient
    JobClient jobClient = JobClient.createFromConnectionString(iotHubConnectionString);
    System.out.println("JobClient created with success");

    // Schedule twin job desired properties
    // Maximum concurrent jobs is 1 for Free and S1 tiers
    String desiredPropertiesJobId = "DPCMD" + UUID.randomUUID();
    scheduleJobSetDesiredProperties(jobClient, desiredPropertiesJobId);
    monitorJob(jobClient, desiredPropertiesJobId);

    // Schedule twin job direct method
    String directMethodJobId = "DMCMD" + UUID.randomUUID();
    scheduleJobCallDirectMethod(jobClient, directMethodJobId);
    monitorJob(jobClient, directMethodJobId);

    // Run a query to show the job detail
    queryDeviceJobs(jobClient, start);

    System.out.println("Shutting down schedule-jobs app");
    ```

1. Uložte a zavřete `schedule-jobs\src\main\java\com\mycompany\app\App.java` souboru

1. Sestavení **plán úloh** aplikace a opravte všechny chyby. Na příkazovém řádku přejděte do `schedule-jobs` složky a spusťte následující příkaz:

    `mvn clean package -DskipTests`

## <a name="create-a-device-app"></a>Vytvoření aplikace pro zařízení

V této části vytvoříte konzolovou aplikaci Java, která zpracovává požadované vlastnosti odeslané ze služby IoT Hub a implementuje volání přímé metody.

1. V `iot-java-schedule-jobs` složku vytvořit projekt Maven s názvem **simulated-device** pomocí následujícího příkazu na příkazovém řádku. Všimněte si, že se jedná o jeden dlouhý příkaz:

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
    private static final int METHOD_SUCCESS = 200;
    private static final int METHOD_NOT_DEFINED = 404;
    ```

    Tato ukázková aplikace používá při vytváření instance objektu **DeviceClient** proměnnou **protocol**.

1. Tisknout oznámení twin zařízení ke konzole, přidejte následující vnořenou třídu k **aplikace** třídy:

    ```java
    // Handler for device twin operation notifications from IoT Hub
    protected static class DeviceTwinStatusCallBack implements IotHubEventCallback {
      public void execute(IotHubStatusCode status, Object context) {
        System.out.println("IoT Hub responded to device twin operation with status " + status.name());
      }
    }
    ```

1. Tisknout přímá metoda oznámení ke konzole, přidejte následující vnořenou třídu k **aplikace** třídy:

    ```java
    // Handler for direct method notifications from IoT Hub
    protected static class DirectMethodStatusCallback implements IotHubEventCallback {
      public void execute(IotHubStatusCode status, Object context) {
        System.out.println("IoT Hub responded to direct method operation with status " + status.name());
      }
    }
    ```

1. Pro zpracování přímá metoda volání ze služby IoT Hub, přidejte následující vnořenou třídu k **aplikace** třídy:

    ```java
    // Handler for direct method calls from IoT Hub
    protected static class DirectMethodCallback
        implements DeviceMethodCallback {
      @Override
      public DeviceMethodData call(String methodName, Object methodData, Object context) {
        DeviceMethodData deviceMethodData;
        switch (methodName) {
          case "lockDoor": {
            System.out.println("Executing direct method: " + methodName);
            deviceMethodData = new DeviceMethodData(METHOD_SUCCESS, "Executed direct method " + methodName);
            break;
          }
          default: {
            deviceMethodData = new DeviceMethodData(METHOD_NOT_DEFINED, "Not defined direct method " + methodName);
          }
        }
        // Notify IoT Hub of result
        return deviceMethodData;
      }
    }
    ```

1. Aktualizace **hlavní** podpis metody k patří `throws` klauzule:

    ```java
    public static void main( String[] args ) throws IOException, URISyntaxException
    ```

1. Přidejte následující kód, který **hlavní** metody:
    * Vytvoření klienta zařízení pro komunikaci se službou IoT Hub.
    * Vytvoření **zařízení** objekt pro uložení twin vlastnosti zařízení.

    ```java
    // Create a device client
    DeviceClient client = new DeviceClient(connString, protocol);

    // An object to manage device twin desired and reported properties
    Device dataCollector = new Device() {
      @Override
      public void PropertyCall(String propertyKey, Object propertyValue, Object context)
      {
        System.out.println("Received desired property change: " + propertyKey + " " + propertyValue);
      }
    };
    ```

1. Spuštění služby klienta zařízení, přidejte následující kód, který **hlavní** metoda:

    ```java
    try {
      // Open the DeviceClient
      // Start the device twin services
      // Subscribe to direct method calls
      client.open();
      client.startDeviceTwin(new DeviceTwinStatusCallBack(), null, dataCollector, null);
      client.subscribeToDeviceMethod(new DirectMethodCallback(), null, new DirectMethodStatusCallback(), null);
    } catch (Exception e) {
      System.out.println("Exception, shutting down \n" + " Cause: " + e.getCause() + " \n" + e.getMessage());
      dataCollector.clean();
      client.closeNow();
      System.out.println("Shutting down...");
    }
    ```

1. Čekání na uživatele ke stisknutí **Enter** klíče před ukončením, přidejte následující kód do konce **hlavní** metoda:

    ```java
    // Close the app
    System.out.println("Press any key to exit...");
    Scanner scanner = new Scanner(System.in);
    scanner.nextLine();
    dataCollector.clean();
    client.closeNow();
    scanner.close();
    ```

1. Uložte a zavřete `simulated-device\src\main\java\com\mycompany\app\App.java` souboru.

1. Sestavení **simulated-device** aplikace a opravte všechny chyby. Na příkazovém řádku přejděte do `simulated-device` složky a spusťte následující příkaz:

    `mvn clean package -DskipTests`

## <a name="run-the-apps"></a>Spouštění aplikací

Nyní jste připraveni ke spuštění aplikace konzoly.

1. Na příkazovém řádku v `simulated-device` složky, spusťte následující příkaz a spusťte aplikaci zařízení naslouchání pro požadovanou vlastnost změny a volání přímé metod:

    `mvn exec:java -Dexec.mainClass="com.mycompany.app.App"`

    ![Spuštění klienta zařízení](media/iot-hub-java-java-schedule-jobs/device-app-1.png)

1. Na příkazovém řádku v `schedule-jobs` složky, spusťte následující příkaz ke spuštění **plán úloh** aplikační služby ke spuštění dvě úlohy. První nastaví hodnoty požadovanou vlastnost, druhý volá metodu přímé:

    `mvn exec:java -Dexec.mainClass="com.mycompany.app.App"`

    ![Aplikace služby Java IoT Hub vytvoří dvě úlohy](media/iot-hub-java-java-schedule-jobs/service-app-1.png)

1. Aplikace zařízení zpracovává změnu požadované vlastnosti a volání přímé metody:

    ![Klient v zařízení reaguje na změny](media/iot-hub-java-java-schedule-jobs/device-app-2.png)

## <a name="next-steps"></a>Další postup

V tomto kurzu jste nakonfigurovali novou službu IoT Hub na webu Azure Portal a potom jste vytvořili identitu zařízení v registru identit ve službě IoT Hub. Vytvořili jste back-end aplikačním spustit dvě úlohy. První úlohy nastavte hodnoty požadované vlastnosti a druhý úloha s názvem přímá metoda.

Použijte v následujících zdrojích informací další postup:

* Odesílat telemetrická data ze zařízení pomocí [Začínáme se službou IoT Hub](iot-hub-java-java-getstarted.md) kurzu.
* S kontroly nad zařízeními interaktivně (například zapnutí ventilátor z aplikace řízené uživatele) [použít přímé metody](iot-hub-java-java-direct-methods.md) kurzu.
