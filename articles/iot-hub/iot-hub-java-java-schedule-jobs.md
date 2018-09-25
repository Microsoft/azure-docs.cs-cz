---
title: Plánování úloh s Azure IoT Hub (Java) | Dokumentace Microsoftu
description: Jak naplánovat úlohu služby Azure IoT Hub k vyvolání přímé metody a nastavte požadované vlastnosti na více zařízeních. Použití zařízení Azure IoT SDK pro Javu k implementaci aplikace simulovaného zařízení a služby Azure IoT SDK pro Javu k implementaci app service na spuštění úlohy.
author: dominicbetts
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.devlang: java
ms.topic: conceptual
ms.date: 07/10/2017
ms.author: dobett
ms.openlocfilehash: 7d41732103bd76e281c2a669a649645c8ff5bc73
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "46957978"
---
# <a name="schedule-and-broadcast-jobs-java"></a>Úlohy vysílání a plánování (Java)

[!INCLUDE [iot-hub-selector-schedule-jobs](../../includes/iot-hub-selector-schedule-jobs.md)]

Azure IoT Hub použijte k plánování a sledování úloh, které aktualizují miliony zařízení. Použití úloh na:

* Aktualizace požadovaných vlastností
* Aktualizace značky
* Vyvolání přímých metod

Úloha zabalí jednu z těchto akcí a sleduje spuštění proti sadu zařízení. Dotaz dvojčete zařízení definuje sadu zařízení, která se spustí úlohy pro. Například back endové aplikace úlohu můžete použít k vyvolání přímé metody v 10 000 zařízení, která restartuje zařízení. Určení sady zařízení, s dotazy dvojčete zařízení a naplánovat úlohu pro spuštění někdy v budoucnu. Sleduje průběh úlohy jako každé ze zařízení přijímat a provést restartování přímé metody.

Další informace o každé z těchto možností najdete v tématu:

* Dvojče zařízení a vlastností: [Začínáme s dvojčaty zařízení](iot-hub-java-java-twin-getstarted.md)
* Přímé metody: [Příručka pro vývojáře IoT Hub - přímých metod](iot-hub-devguide-direct-methods.md) a [kurz: použití přímých metod](quickstart-control-device-java.md)

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

V tomto kurzu získáte informace o následujících postupech:

* Vytvoření aplikace pro zařízení, která implementuje přímé metody volá **lockDoor**. Aplikace pro zařízení také přijímat změny požadované vlastnosti z back endové aplikace.
* Vytvoření back endové aplikace, která vytvoří úlohu k volání **lockDoor** přímá metoda na více zařízeních. Jiná úloha odešle aktualizace požadované vlastnosti na více zařízeních.

Na konci tohoto kurzu budete mít konzolovou aplikaci java zařízení a back-end konzolovou aplikaci java:

**simulated-device** , která se připojuje ke službě IoT hub implementuje **lockDoor** přímé metody a obslužné rutiny požadované změny vlastností.

**plánování úloh** používají úlohy k volání **lockDoor** přímá metoda a aktualizovat požadované vlastnosti dvojčete zařízení na různých zařízeních.

> [!NOTE]
> Tento článek [sad SDK Azure IoT](iot-hub-devguide-sdks.md) poskytuje informace o Azure IoT SDK, že vám pomůže vytvářet aplikace pro zařízení i back-end.

## <a name="prerequisites"></a>Požadavky

Pro absolvování tohoto kurzu potřebujete:

* Nejnovější [Java SE Development Kit 8](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)
* [Maven 3](https://maven.apache.org/install.html)
* Aktivní účet Azure. (Pokud účet nemáte, můžete vytvořit [bezplatný účet](http://azure.microsoft.com/pricing/free-trial/) během několika minut.)

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity-portal](../../includes/iot-hub-get-started-create-device-identity-portal.md)]

Můžete také použít [rozšíření IoT pro Azure CLI](https://github.com/Azure/azure-iot-cli-extension) nástroje pro přidání zařízení do služby IoT hub.

## <a name="create-the-service-app"></a>Vytvoření aplikace služby

V této části vytvoříte konzolovou aplikaci Java, která používá úlohy:

* Volání **lockDoor** přímá metoda na více zařízeních.
* Odeslat požadované vlastnosti na více zařízeních.

Pokud chcete vytvořit aplikaci:

1. Na svém vývojovém počítači vytvořit prázdnou složku s názvem `iot-java-schedule-jobs`.

1. V `iot-java-schedule-jobs` složku, vytvořte projekt Maven s názvem **plánování úloh** pomocí následujícího příkazu na příkazovém řádku. Všimněte si, že se jedná o jeden dlouhý příkaz:

    `mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=schedule-jobs -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false`

1. Na příkazovém řádku, přejděte `schedule-jobs` složky.

1. Pomocí textového editoru otevřete `pom.xml` soubor `schedule-jobs` složky a přidejte následující závislost **závislosti** uzlu. Tato závislost vám umožní použít **iot-service-client** balíčku v aplikaci tak, aby komunikovat se službou IoT hub:

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

1. Do třídy **App** přidejte následující proměnné na úrovni třídy. Nahraďte `{youriothubconnectionstring}` jste si poznamenali v IoT hubu připojovacím řetězcem *vytvoření služby IoT Hub* části:

    ```java
    public static final String iotHubConnectionString = "{youriothubconnectionstring}";
    public static final String deviceId = "myDeviceId";

    // How long the job is permitted to run without
    // completing its work on the set of devices
    private static final long maxExecutionTimeInSeconds = 30;
    ```

1. Přidejte následující metodu do **aplikace** třídy naplánování úlohy pro aktualizaci **vytváření** a **Floor** požadované vlastnosti v dvojčeti zařízení:

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

1. Naplánování úlohy k volání **lockDoor** metodu, přidejte následující metodu do **aplikace** třídy:

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

1. Pokud chcete monitorovat úlohy, přidejte následující metodu do **aplikace** třídy:

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

1. K dotazování podrobnosti úlohy, které jste spustili, přidejte následující metodu:

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

1. Aktualizace **hlavní** podpis metody pro patří `throws` klauzule:

    ```java
    public static void main( String[] args ) throws Exception
    ```

1. Ke spuštění a monitorování dvě úlohy postupně, přidejte následující kód, který **hlavní** metody:

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

1. Sestavení **plánování úloh** aplikace a opravíte jakékoli chyby. Na příkazovém řádku, přejděte `schedule-jobs` složky a spusťte následující příkaz:

    `mvn clean package -DskipTests`

## <a name="create-a-device-app"></a>Vytvoření aplikace pro zařízení

V této části vytvoříte konzolovou aplikaci Java, který zpracovává požadovaných vlastností odeslané ze služby IoT Hub a implementuje volání přímé metody.

1. V `iot-java-schedule-jobs` složku, vytvořte projekt Maven s názvem **simulated-device** pomocí následujícího příkazu na příkazovém řádku. Všimněte si, že se jedná o jeden dlouhý příkaz:

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
    private static final int METHOD_SUCCESS = 200;
    private static final int METHOD_NOT_DEFINED = 404;
    ```

    Tato ukázková aplikace používá při vytváření instance objektu **DeviceClient** proměnnou **protocol**.

1. Můžete vytisknout oznámení dvojčete zařízení do konzoly, přidáním následující vnořené třídy **aplikace** třídy:

    ```java
    // Handler for device twin operation notifications from IoT Hub
    protected static class DeviceTwinStatusCallBack implements IotHubEventCallback {
      public void execute(IotHubStatusCode status, Object context) {
        System.out.println("IoT Hub responded to device twin operation with status " + status.name());
      }
    }
    ```

1. Můžete vytisknout oznámení přímé metody do konzoly, přidáním následující vnořené třídy **aplikace** třídy:

    ```java
    // Handler for direct method notifications from IoT Hub
    protected static class DirectMethodStatusCallback implements IotHubEventCallback {
      public void execute(IotHubStatusCode status, Object context) {
        System.out.println("IoT Hub responded to direct method operation with status " + status.name());
      }
    }
    ```

1. Ke zpracování volání přímé metody ze služby IoT Hub, přidáním následující vnořené třídy **aplikace** třídy:

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

1. Aktualizace **hlavní** podpis metody pro patří `throws` klauzule:

    ```java
    public static void main( String[] args ) throws IOException, URISyntaxException
    ```

1. Přidejte následující kód, který **hlavní** metodu:
    * Vytvoření klienta zařízení ke komunikaci s centrem IoT.
    * Vytvoření **zařízení** objekt pro uložení vlastnosti dvojčete zařízení.

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

1. Ke spuštění služeb klienta zařízení, přidejte následující kód, který **hlavní** metody:

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

1. Čekání uživatele ke stisknutí **Enter** klíče před ukončením, přidejte následující kód na konec objektu **hlavní** metody:

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

1. Sestavení **simulated-device** aplikace a opravíte jakékoli chyby. Na příkazovém řádku, přejděte `simulated-device` složky a spusťte následující příkaz:

    `mvn clean package -DskipTests`

## <a name="run-the-apps"></a>Spouštění aplikací

Nyní jste připraveni ke spuštění aplikace konzoly.

1. Na příkazovém řádku v `simulated-device` složky, spusťte následující příkaz pro spuštění aplikace pro zařízení s poslouchání změny požadované vlastnosti a volání přímé metody:

    `mvn exec:java -Dexec.mainClass="com.mycompany.app.App"`

    ![Spuštění klienta zařízení](media/iot-hub-java-java-schedule-jobs/device-app-1.png)

1. Na příkazovém řádku v `schedule-jobs` složky, spusťte následující příkaz pro spuštění **plánování úloh** služby app Service spustit dvě úlohy. První nastaví hodnot požadovaných vlastností, druhé volání přímé metody:

    `mvn exec:java -Dexec.mainClass="com.mycompany.app.App"`

    ![Aplikace služby Java IoT Hub vytvoří dvě úlohy](media/iot-hub-java-java-schedule-jobs/service-app-1.png)

1. Aplikace pro zařízení zpracovává změny požadované vlastnosti a volání přímé metody:

    ![Klient zařízení reaguje na změny](media/iot-hub-java-java-schedule-jobs/device-app-2.png)

## <a name="next-steps"></a>Další postup

V tomto kurzu jste nakonfigurovali novou službu IoT Hub na webu Azure Portal a potom jste vytvořili identitu zařízení v registru identit ve službě IoT Hub. Vytvoříte back endové aplikace můžete spouštět dvě úlohy. První úlohu nastavit hodnot požadovaných vlastností a druhá úloha s názvem přímé metody.

Pomocí následujících zdrojích se dozvíte jak:

* Odesílání telemetrie ze zařízení s [Začínáme se službou IoT Hub](quickstart-send-telemetry-java.md) kurzu.
* Ovládací prvek zařízení interaktivně (například zapnutí ventilátor z aplikace řízené uživatelem) [použití přímých metod](quickstart-control-device-java.md) kurzu.
