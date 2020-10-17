---
title: Plánování úloh pomocí Azure IoT Hub (Java) | Microsoft Docs
description: Jak naplánovat úlohu Azure IoT Hub k vyvolání přímé metody a nastavit požadovanou vlastnost na více zařízeních. Pomocí sady SDK pro zařízení Azure IoT pro jazyk Java implementujete aplikace simulovaného zařízení a sadu SDK služby Azure IoT pro jazyk Java k implementaci aplikace služby pro spuštění úlohy.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.devlang: java
ms.topic: conceptual
ms.date: 08/16/2019
ms.custom: mqtt, devx-track-java
ms.openlocfilehash: 36092e0bb7d0249837d2eaa7898e9231ecb5f5a9
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/17/2020
ms.locfileid: "92146998"
---
# <a name="schedule-and-broadcast-jobs-java"></a>Úlohy plánování a vysílání (Java)

[!INCLUDE [iot-hub-selector-schedule-jobs](../../includes/iot-hub-selector-schedule-jobs.md)]

Pomocí Azure IoT Hub můžete plánovat a sledovat úlohy, které aktualizují miliony zařízení. Úlohy můžete použít k těmto akcím:

* Aktualizace požadovaných vlastností
* Aktualizovat značky
* Vyvolání přímých metod

Úloha zalomí jednu z těchto akcí a sleduje spuštění na základě sady zařízení. Dotaz na zdvojené zařízení definuje sadu zařízení, na kterých se úloha spouští. Například aplikace back-end může použít úlohu k vyvolání přímé metody na zařízeních 10 000, která zařízení restartuje. Zadáte sadu zařízení se zdvojeným dotazem zařízení a naplánujete, aby úloha běžela v budoucím čase. Tato úloha sleduje průběh, protože každé zařízení přijímá a provádí metodu restartování přímo.

Další informace o jednotlivých možnostech najdete v těchto tématech:

* Vlákna a vlastnosti zařízení: [Začínáme s nevlákenou zařízení](iot-hub-java-java-twin-getstarted.md)

* Přímé metody: [IoT Hub příručka pro vývojáře – přímé metody](iot-hub-devguide-direct-methods.md) a [kurz: použití přímých metod](quickstart-control-device-java.md)

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

V tomto kurzu získáte informace o následujících postupech:

* Vytvořte aplikaci zařízení, která implementuje přímou metodu nazvanou **lockDoor**. Aplikace zařízení také přijímá požadované změny vlastností z back-endové aplikace.

* Vytvořte aplikaci back-end, která vytvoří úlohu pro volání přímé metody **lockDoor** na více zařízeních. Jiná úloha odešle požadované aktualizace vlastností do více zařízení.

Na konci tohoto kurzu máte konzolovou aplikaci Java a aplikaci back-end konzoly Java:

**simulované zařízení** , které se připojuje ke službě IoT Hub, implementuje metodu **lockDoor** Direct a zpracovává požadované změny vlastností.

**plán – úlohy** , které používají úlohy k volání přímé metody **lockDoor** a aktualizují v zařízení požadované vlastnosti na více zařízeních.

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

K přidání zařízení do služby IoT Hub můžete použít také nástroj [rozšíření IoT pro Azure CLI](https://github.com/Azure/azure-iot-cli-extension) .

## <a name="get-the-iot-hub-connection-string"></a>Získání připojovacího řetězce centra IoT Hub

[!INCLUDE [iot-hub-howto-schedule-jobs-shared-access-policy-text](../../includes/iot-hub-howto-schedule-jobs-shared-access-policy-text.md)]

[!INCLUDE [iot-hub-include-find-registryrw-connection-string](../../includes/iot-hub-include-find-registryrw-connection-string.md)]

## <a name="create-the-service-app"></a>Vytvoření aplikace služby

V této části vytvoříte konzolovou aplikaci Java, která používá úlohy pro:

* Zavolejte metodu **lockDoor** Direct na více zařízení.

* Požadované vlastnosti pošlete na více zařízení.

Vytvoření aplikace:

1. Ve vývojovém počítači vytvořte prázdnou složku s názvem **IoT-Java-Schedule-Jobs**.

2. Ve složce **IoT-Java-Schedule-Jobs** vytvořte pomocí následujícího příkazu na příkazovém řádku projekt Maven s názvem **Schedule-Jobs** . Všimněte si, že se jedná o jeden dlouhý příkaz:

   ```cmd/sh
   mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=schedule-jobs -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
   ```

3. Na příkazovém řádku přejděte do složky **plán – úlohy** .

4. Pomocí textového editoru otevřete soubor **pom.xml** ve složce **plán – úlohy** a přidejte následující závislost na uzel **závislosti** . Tato závislost vám umožní komunikovat se službou IoT Hub pomocí balíčku **IoT-Service-Client** ve vaší aplikaci:

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

7. Pomocí textového editoru otevřete soubor **Schedule-jobs\src\main\java\com\mycompany\app\App.Java** .

8. Do souboru přidejte následující příkazy pro **import**:

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

9. Do třídy **App** přidejte následující proměnné na úrovni třídy. Nahraďte `{youriothubconnectionstring}` připojovacím řetězcem služby IoT Hub, který jste zkopírovali dříve v [části získání připojovacího řetězce služby IoT Hub](#get-the-iot-hub-connection-string):

    ```java
    public static final String iotHubConnectionString = "{youriothubconnectionstring}";
    public static final String deviceId = "myDeviceId";

    // How long the job is permitted to run without
    // completing its work on the set of devices
    private static final long maxExecutionTimeInSeconds = 30;
    ```

10. Přidejte následující metodu do třídy **App** k naplánování úlohy, která aktualizuje požadované vlastnosti **sestavování** a **uspořádání** v zařízení s dvojitou kapacitou:

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

11. K naplánování úlohy pro volání metody **lockDoor** přidejte následující metodu do třídy **aplikace** :

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

12. Chcete-li monitorovat úlohu, přidejte do třídy **aplikace** následující metodu:

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

13. Pokud chcete zadat dotaz na podrobnosti o úlohách, které jste spustili, přidejte následující metodu:

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

14. Aktualizujte podpis metody **Main** tak, aby zahrnoval následující `throws` klauzuli:

    ```java
    public static void main( String[] args ) throws Exception
    ```

15. Chcete-li spustit a monitorovat dvě úlohy sekvenčně, nahraďte kód v metodě **Main** následujícím kódem:

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

16. Uložte a zavřete soubor **Schedule-jobs\src\main\java\com\mycompany\app\App.Java**

17. Sestavte aplikaci **Schedule-Jobs** a opravte všechny chyby. Na příkazovém řádku přejděte do složky **Schedule-Jobs** a spusťte následující příkaz:

    ```cmd/sh
    mvn clean package -DskipTests
    ```

## <a name="create-a-device-app"></a>Vytvoření aplikace pro zařízení

V této části vytvoříte konzolovou aplikaci Java, která zpracovává požadované vlastnosti odesílané z IoT Hub a implementuje přímé volání metody.

1. Ve složce **IoT-Java-Schedule-Jobs** vytvořte pomocí následujícího příkazu na příkazovém řádku projekt Maven s názvem **simulované zařízení** . Všimněte si, že se jedná o jeden dlouhý příkaz:

   ```cmd/sh
   mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=simulated-device -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
   ```

2. Na příkazovém řádku přejděte do složky **simulovaného zařízení** .

3. Pomocí textového editoru otevřete soubor **pom.xml** ve složce **simulovaného zařízení** a přidejte následující závislosti do uzlu **závislosti** . Tato závislost vám umožní komunikovat se službou IoT Hub pomocí balíčku **IoT-Device-Client** ve vaší aplikaci:

    ```xml
    <dependency>
      <groupId>com.microsoft.azure.sdk.iot</groupId>
      <artifactId>iot-device-client</artifactId>
      <version>1.17.5</version>
    </dependency>
    ```

    > [!NOTE]
    > Můžete vyhledat nejnovější verzi **iot-device-client** pomocí [vyhledávání Maven](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22iot-device-client%22%20g%3A%22com.microsoft.azure.sdk.iot%22).

4. Přidejte následující závislost na uzel **závislosti** . Tato závislost konfiguruje NOP pro přihlašování k Apache [SLF4J](https://www.slf4j.org/) , které používá klientská sada SDK pro zařízení k implementaci protokolování. Tato konfigurace je volitelná, ale pokud ji vynecháte, může se při spuštění aplikace zobrazit upozornění v konzole. Další informace o protokolování v sadě SDK klienta zařízení najdete v tématu [protokolování](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-samples/readme.md#logging)v ukázkách pro soubor Readme pro *zařízení Azure IoT SDK pro jazyk Java* .

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

9. Do třídy **App** přidejte následující proměnné na úrovni třídy. Nahraďte `{yourdeviceconnectionstring}` připojovacím řetězcem zařízení, který jste zkopírovali dříve v části [zaregistrovat nové zařízení v rámci centra IoT Hub](#register-a-new-device-in-the-iot-hub) :

    ```java
    private static String connString = "{yourdeviceconnectionstring}";
    private static IotHubClientProtocol protocol = IotHubClientProtocol.MQTT;
    private static final int METHOD_SUCCESS = 200;
    private static final int METHOD_NOT_DEFINED = 404;
    ```

    Tato ukázková aplikace používá při vytváření instance objektu **DeviceClient** proměnnou **protocol**.

10. K tisku dvojitých oznámení v konzole nástroje přidejte do třídy **aplikace** následující vnořenou třídu:

    ```java
    // Handler for device twin operation notifications from IoT Hub
    protected static class DeviceTwinStatusCallBack implements IotHubEventCallback {
      public void execute(IotHubStatusCode status, Object context) {
        System.out.println("IoT Hub responded to device twin operation with status " + status.name());
      }
    }
    ```

11. Chcete-li vytisknout oznámení přímých metod do konzoly, přidejte do třídy **aplikace** následující vnořenou třídu:

    ```java
    // Handler for direct method notifications from IoT Hub
    protected static class DirectMethodStatusCallback implements IotHubEventCallback {
      public void execute(IotHubStatusCode status, Object context) {
        System.out.println("IoT Hub responded to direct method operation with status " + status.name());
      }
    }
    ```

12. Chcete-li zpracovat Přímá volání metody z IoT Hub, přidejte do třídy **aplikace** následující vnořenou třídu:

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

13. Aktualizujte podpis metody **Main** tak, aby zahrnoval následující `throws` klauzuli:

    ```java
    public static void main( String[] args ) throws IOException, URISyntaxException
    ```

14. Nahraďte kód v metodě **Main** následujícím kódem:
    * Vytvořte klienta zařízení pro komunikaci s IoT Hub.
    * Vytvořte objekt **zařízení** pro uložení vlastností, které jsou v zařízení.

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

15. Chcete-li spustit služby klienta zařízení, přidejte do metody **Main** následující kód:

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

16. Chcete-li počkat, než uživatel stiskne klávesu **ENTER** před vypnutím, přidejte na konec metody **Main** následující kód:

    ```java
    // Close the app
    System.out.println("Press any key to exit...");
    Scanner scanner = new Scanner(System.in);
    scanner.nextLine();
    dataCollector.clean();
    client.closeNow();
    scanner.close();
    ```

17. Uložte a zavřete soubor **Simulated-device\src\main\java\com\mycompany\app\App.Java** .

18. Sestavte aplikaci **simulovaného zařízení** a opravte všechny chyby. Na příkazovém řádku přejděte do složky **simulovaného zařízení** a spusťte následující příkaz:

    ```cmd/sh
    mvn clean package -DskipTests
    ```

## <a name="run-the-apps"></a>Spouštění aplikací

Nyní jste připraveni spustit konzolové aplikace.

1. Na příkazovém řádku ve složce **simulovaného zařízení** spusťte následující příkaz, který spustí aplikaci zařízení naslouchající pro požadované změny vlastností a přímé volání metody:

   ```cmd/sh
   mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
   ```

   ![Spustí se klient zařízení.](./media/iot-hub-java-java-schedule-jobs/device-app-1.png)

2. Na příkazovém řádku ve složce spusťte `schedule-jobs` následující příkaz, který spustí aplikaci **Schedule-Jobs** Service ke spuštění dvou úloh. První nastaví požadované hodnoty vlastností a druhý zavolá přímo metodu:

   ```cmd\sh
   mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
   ```

   ![Aplikace služby Java IoT Hub vytvoří dvě úlohy.](./media/iot-hub-java-java-schedule-jobs/service-app-1.png)

3. Aplikace zařízení zpracovává požadovanou změnu vlastnosti a přímé volání metody:

   ![Klient zařízení reaguje na změny](./media/iot-hub-java-java-schedule-jobs/device-app-2.png)

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste použili úlohu k naplánování přímé metody na zařízení a aktualizaci vlastností vlákna zařízení.

Pomocí následujících zdrojů se naučíte:

* Pomocí kurzu [Začínáme s IoT Hub](quickstart-send-telemetry-java.md) odešlete telemetrii ze zařízení.

* Interaktivní řízení zařízení (například zapnutí ventilátoru z aplikace řízené uživatelem) pomocí kurzu [použití přímých metod](quickstart-control-device-java.md) . s