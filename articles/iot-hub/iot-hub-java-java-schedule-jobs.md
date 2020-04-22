---
title: Plánování úloh pomocí služby Azure IoT Hub (Java) | Dokumenty společnosti Microsoft
description: Jak naplánovat úlohu Služby Azure IoT Hub k vyvolání přímé metody a nastavit požadovanou vlastnost na více zařízeních. Pomocí sady Azure IoT device SDK for Java můžete implementovat aplikace simulovaných zařízení a sadku Azure IoT service SDK pro Jazyk Java k implementaci aplikace služby pro spuštění úlohy.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.devlang: java
ms.topic: conceptual
ms.date: 08/16/2019
ms.custom: mqtt
ms.openlocfilehash: 5e3f4f4aedb0bc3fb1f8ea11001b08daa57aafc1
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/21/2020
ms.locfileid: "81732487"
---
# <a name="schedule-and-broadcast-jobs-java"></a>Plánování a vysílání úloh (Java)

[!INCLUDE [iot-hub-selector-schedule-jobs](../../includes/iot-hub-selector-schedule-jobs.md)]

Azure IoT Hub můžete používat k plánování a sledování úloh, které aktualizují miliony zařízení. Pomocí úloh můžete:

* Aktualizace požadovaných vlastností
* Aktualizovat značky
* Vyvolat přímé metody

Úloha zabalí jednu z těchto akcí a sleduje provádění proti sadu zařízení. Dotaz dvojčete zařízení definuje sadu zařízení, proti kterým se úloha provádí. Například back-endová aplikace může použít úlohu k vyvolání přímé metody na 10 000 zařízeních, která restartují zařízení. Zadáte sadu zařízení s dotazem dvojčete zařízení a naplánujete spuštění úlohy v budoucnu. Úloha sleduje průběh, protože každé zařízení přijímá a spouští metodu přímého restartování.

Další informace o jednotlivých funkcích najdete v následujících tématech:

* Dvojče a vlastnosti zařízení: [Začínáme s dvojčaty zařízení](iot-hub-java-java-twin-getstarted.md)

* Přímé metody: [Průvodce vývojářem ioT Hubu – přímé metody](iot-hub-devguide-direct-methods.md) a [výuka: Použití přímých metod](quickstart-control-device-java.md)

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

V tomto kurzu získáte informace o následujících postupech:

* Vytvořte aplikaci pro zařízení, která implementuje přímou metodu nazvanou **lockDoor**. Aplikace zařízení také obdrží požadované změny vlastností z back-endové aplikace.

* Vytvořte back-endovou aplikaci, která vytvoří úlohu pro volání přímé metody **lockDoor** na více zařízeních. Jiná úloha odesílá aktualizace požadovaných vlastností do více zařízení.

Na konci tohoto tutoriálu máte aplikaci java console zařízení a back-end ovou aplikaci java konzole:

**simulované zařízení,** které se připojuje k centru IoT, implementuje metodu **lockDoor** direct a zpracovává požadované změny vlastností.

**naplánovat úlohy,** které používají úlohy volat **lockDoor** přímou metodu a aktualizovat dvojče zařízení požadované vlastnosti na více zařízeních.

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

Rozšíření [IoT pro](https://github.com/Azure/azure-iot-cli-extension) nástroj Azure CLI můžete také použít k přidání zařízení do centra IoT.

## <a name="get-the-iot-hub-connection-string"></a>Získání připojovacího řetězce centra IoT hub

[!INCLUDE [iot-hub-howto-schedule-jobs-shared-access-policy-text](../../includes/iot-hub-howto-schedule-jobs-shared-access-policy-text.md)]

[!INCLUDE [iot-hub-include-find-registryrw-connection-string](../../includes/iot-hub-include-find-registryrw-connection-string.md)]

## <a name="create-the-service-app"></a>Vytvoření aplikace služby

V této části vytvoříte konzolovou aplikaci Java, která používá úlohy k:

* Volání **lockDoor** přímou metodu na více zařízeních.

* Odešlete požadované vlastnosti více zařízením.

Vytvoření aplikace:

1. Ve vývojovém počítači vytvořte prázdnou složku nazvanou **iot-java-schedule-jobs**.

2. Ve složce **iot-java-schedule-jobs** vytvořte projekt Maven nazývaný **naplánované úlohy** pomocí následujícího příkazu na příkazovém řádku. Všimněte si, že se jedná o jeden dlouhý příkaz:

   ```cmd/sh
   mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=schedule-jobs -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
   ```

3. Na příkazovém řádku přejděte do složky **plánovače úloh.**

4. Pomocí textového editoru otevřete soubor **pom.xml** ve složce **úlohy plánu** a přidejte do uzlu **závislostí** následující závislost. Tato závislost umožňuje používat balíček **iot-service-client** ve vaší aplikaci ke komunikaci s centrem IoT:

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

7. Pomocí textového editoru otevřete soubor **schedule-jobs\src\main\java\com\mycompany\app\App.java.**

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

9. Do třídy **App** přidejte následující proměnné na úrovni třídy. Nahraďte `{youriothubconnectionstring}` připojovacím řetězcem centra IoT, který jste dříve zkopírovali v [části Získání připojovacího řetězce centra IoT](#get-the-iot-hub-connection-string)hub :

    ```java
    public static final String iotHubConnectionString = "{youriothubconnectionstring}";
    public static final String deviceId = "myDeviceId";

    // How long the job is permitted to run without
    // completing its work on the set of devices
    private static final long maxExecutionTimeInSeconds = 30;
    ```

10. Přidejte do třídy **App** následující metodu, abyste naplánovali úlohu pro aktualizaci požadovaných vlastností **budovy** a **podlahy** v dvojčeti zařízení:

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

11. Chcete-li naplánovat úlohu pro volání metody **lockDoor,** přidejte do třídy **App** následující metodu:

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

12. Chcete-li sledovat úlohu, přidejte do třídy **App** následující metodu:

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

13. Chcete-li se dotazovat na podrobnosti o úlohách, které jste spustili, přidejte následující metodu:

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

14. Aktualizujte podpis **hlavní** metody `throws` tak, aby obsahoval následující klauzuli:

    ```java
    public static void main( String[] args ) throws Exception
    ```

15. Chcete-li spustit a sledovat dvě úlohy postupně, nahraďte kód v **hlavní** metodě následujícím kódem:

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

16. Uložení a zavření souboru **schedule-jobs\src\main\java\com\mycompany\app\App.java**

17. Vytvořte aplikaci **úlohy plánu** a opravte všechny chyby. Na příkazovém řádku přejděte do složky **úlohy plánu** a spusťte následující příkaz:

    ```cmd/sh
    mvn clean package -DskipTests
    ```

## <a name="create-a-device-app"></a>Vytvoření aplikace pro zařízení

V této části vytvoříte konzolovou aplikaci Java, která zpracovává požadované vlastnosti odeslané ze služby IoT Hub a implementuje volání přímé metody.

1. Ve složce **iot-java-schedule-jobs** vytvořte projekt Maven nazývaný **simulované zařízení** pomocí následujícího příkazu na příkazovém řádku. Všimněte si, že se jedná o jeden dlouhý příkaz:

   ```cmd/sh
   mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=simulated-device -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
   ```

2. Na příkazovém řádku přejděte do složky **simulované zařízení.**

3. Pomocí textového editoru otevřete soubor **pom.xml** ve složce **simulovaných zařízení** a přidejte následující závislosti do uzlu **závislostí.** Tato závislost umožňuje používat balíček **iot-device-client** ve vaší aplikaci ke komunikaci s centrem IoT:

    ```xml
    <dependency>
      <groupId>com.microsoft.azure.sdk.iot</groupId>
      <artifactId>iot-device-client</artifactId>
      <version>1.17.5</version>
    </dependency>
    ```

    > [!NOTE]
    > Můžete vyhledat nejnovější verzi **iot-device-client** pomocí [vyhledávání Maven](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22iot-device-client%22%20g%3A%22com.microsoft.azure.sdk.iot%22).

4. Přidejte následující závislost do uzlu **závislostí.** Tato závislost konfiguruje NOP pro fasádu protokolování Apache [SLF4J,](https://www.slf4j.org/) kterou sada SDK klienta zařízení používá k implementaci protokolování. Tato konfigurace je volitelná, ale pokud ji vynechete, může se při spuštění aplikace zobrazit upozornění v konzole. Další informace o protokolování v sdk klienta zařízení najdete v [tématu protokolování ukázek](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-samples/readme.md#logging)pro soubor Readme *zařízení Azure IoT pro Java.*

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

9. Do třídy **App** přidejte následující proměnné na úrovni třídy. Nahraďte `{yourdeviceconnectionstring}` připojovacím řetězcem zařízení, který jste dříve zkopírovali v části Registrovat nové zařízení v centru [IoT:](#register-a-new-device-in-the-iot-hub)

    ```java
    private static String connString = "{yourdeviceconnectionstring}";
    private static IotHubClientProtocol protocol = IotHubClientProtocol.MQTT;
    private static final int METHOD_SUCCESS = 200;
    private static final int METHOD_NOT_DEFINED = 404;
    ```

    Tato ukázková aplikace používá při vytváření instance objektu **DeviceClient** proměnnou **protocol**.

10. Chcete-li vytisknout oznámení dvojčete zařízení do konzoly, přidejte do třídy **App** následující vnořenou třídu:

    ```java
    // Handler for device twin operation notifications from IoT Hub
    protected static class DeviceTwinStatusCallBack implements IotHubEventCallback {
      public void execute(IotHubStatusCode status, Object context) {
        System.out.println("IoT Hub responded to device twin operation with status " + status.name());
      }
    }
    ```

11. Chcete-li vytisknout oznámení přímé metody do konzoly, přidejte do třídy **App** následující vnořenou třídu:

    ```java
    // Handler for direct method notifications from IoT Hub
    protected static class DirectMethodStatusCallback implements IotHubEventCallback {
      public void execute(IotHubStatusCode status, Object context) {
        System.out.println("IoT Hub responded to direct method operation with status " + status.name());
      }
    }
    ```

12. Chcete-li zpracovat volání přímých metod z centra IoT Hub, přidejte do třídy **App** následující vnořenou třídu:

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

13. Aktualizujte podpis **hlavní** metody `throws` tak, aby obsahoval následující klauzuli:

    ```java
    public static void main( String[] args ) throws IOException, URISyntaxException
    ```

14. Nahraďte kód v **hlavní** metodě následujícím kódem, aby:
    * Vytvořte klienta zařízení pro komunikaci s službou IoT Hub.
    * Vytvořte objekt **Device** pro uložení vlastností dvojčete zařízení.

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

15. Chcete-li spustit klientské služby zařízení, přidejte do **hlavní** metody následující kód:

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

16. Chcete-li před vypnutím počkat, až uživatel stiskne klávesu **Enter,** přidejte na konec **hlavní** metody následující kód:

    ```java
    // Close the app
    System.out.println("Press any key to exit...");
    Scanner scanner = new Scanner(System.in);
    scanner.nextLine();
    dataCollector.clean();
    client.closeNow();
    scanner.close();
    ```

17. Uložte a zavřete soubor **simulované zařízení\src\main\java\com\mycompany\app\App.java.**

18. Vytvořte aplikaci **simulované zařízení** a opravte všechny chyby. Na příkazovém řádku přejděte do složky **simulovaných zařízení** a spusťte následující příkaz:

    ```cmd/sh
    mvn clean package -DskipTests
    ```

## <a name="run-the-apps"></a>Spouštění aplikací

Nyní jste připraveni ke spuštění konzolových aplikací.

1. Na příkazovém řádku ve složce **simulovaného zařízení** spusťte následující příkaz a spusťte aplikaci zařízení, která naslouchá požadovaným změnám vlastností a přímým voláním metod:

   ```cmd/sh
   mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
   ```

   ![Klient zařízení se spustí](./media/iot-hub-java-java-schedule-jobs/device-app-1.png)

2. Na příkazovém `schedule-jobs` řádku ve složce spusťte následující příkaz a spusťte aplikaci **služby schedule-jobs** a spusťte dvě úlohy. První nastaví požadované hodnoty vlastností, druhý volá přímou metodu:

   ```cmd\sh
   mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
   ```

   ![Aplikace služby Java IoT Hub vytvoří dvě úlohy](./media/iot-hub-java-java-schedule-jobs/service-app-1.png)

3. Aplikace zařízení zpracovává požadovanou změnu vlastnosti a volání přímé metody:

   ![Klient zařízení reaguje na změny](./media/iot-hub-java-java-schedule-jobs/device-app-2.png)

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste použili úlohu k naplánování přímé metody do zařízení a aktualizace vlastností dvojčete zařízení.

Pomocí následujících zdrojů se dozvíte, jak:

* Odesílejte telemetrii ze zařízení pomocí výukového [programu Začínáme s ioT hubem.](quickstart-send-telemetry-java.md)

* Interaktivní ovládání zařízení (například zapnutí ventilátoru z uživatelem ovládané aplikace) pomocí výukového programu [Use direct methods](quickstart-control-device-java.md) tutorial.s
