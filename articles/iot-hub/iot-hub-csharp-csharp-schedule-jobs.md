---
title: Plánování úloh pomocí služby Azure IoT Hub (.NET/.NET) | Dokumenty společnosti Microsoft
description: Jak naplánovat úlohu služby Azure IoT Hub k vyvolání přímé metody na více zařízeních. Pomocí sady Azure IoT zařízení SDK pro rozhraní .NET implementovat aplikace simulovaných zařízení a aplikace služby ke spuštění úlohy.
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/20/2019
ms.author: robinsh
ms.custom: mqtt
ms.openlocfilehash: 4c71a108d1967027465d127db50737119af3e2c1
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/21/2020
ms.locfileid: "81733375"
---
# <a name="schedule-and-broadcast-jobs-net"></a>Úlohy plánování a vysílání (.NET)

[!INCLUDE [iot-hub-selector-schedule-jobs](../../includes/iot-hub-selector-schedule-jobs.md)]

Azure IoT Hub můžete používat k plánování a sledování úloh, které aktualizují miliony zařízení. Pomocí úloh můžete:

* Aktualizace požadovaných vlastností

* Aktualizovat značky

* Vyvolat přímé metody

Úloha zabalí jednu z těchto akcí a sleduje spuštění proti sadě zařízení, která je definována dotazem dvojčete zařízení. Například back-endová aplikace může použít úlohu k vyvolání přímé metody na 10 000 zařízeních, která restartují zařízení. Zadáte sadu zařízení s dotazem dvojčete zařízení a naplánujete spuštění úlohy v budoucnu. Úloha sleduje průběh, protože každé zařízení přijímá a spouští metodu přímého restartování.

Další informace o jednotlivých funkcích najdete v následujících tématech:

* Dvojče zařízení a vlastnosti: [Začínáme s dvojčaty zařízení](iot-hub-csharp-csharp-twin-getstarted.md) a [kurz: Jak používat vlastnosti dvojčete zařízení](tutorial-device-twins.md)

* Přímé metody: [Průvodce vývojářem ioT Hubu – přímé metody](iot-hub-devguide-direct-methods.md) a [výuka: Použití přímých metod](quickstart-control-device-dotnet.md)

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

V tomto kurzu získáte informace o následujících postupech:

* Vytvořte aplikaci pro zařízení, která implementuje přímou metodu nazvanou **LockDoor**, kterou může volat back-endová aplikace.

* Vytvořte back-endovou aplikaci, která vytvoří úlohu pro volání přímé metody **LockDoor** na více zařízeních. Jiná úloha odesílá aktualizace požadovaných vlastností do více zařízení.

Na konci tohoto kurzu máte dvě konzolové aplikace .NET (C#):

* **SimulateDeviceMethods**. Tato aplikace se připojí k centru IoT a implementuje metodu **LockDoor** direct.

* **Naplánovat úlohu**. Tato aplikace používá úlohy k volání **lockdoor** přímé metody a aktualizovat dvojče zařízení požadované vlastnosti na více zařízeních.

## <a name="prerequisites"></a>Požadavky

* Visual Studio.

* Aktivní účet Azure. Pokud účet nemáte, můžete si během několika minut vytvořit [bezplatný účet](https://azure.microsoft.com/pricing/free-trial/).

* Zkontrolujte, zda je v bráně firewall otevřený port 8883. Ukázka zařízení v tomto článku používá protokol MQTT, který komunikuje přes port 8883. Tento port může být blokován v některých prostředích podnikové a vzdělávací sítě. Další informace a způsoby, jak tento problém vyřešit, najdete [v tématu připojení k centru IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

## <a name="create-an-iot-hub"></a>Vytvoření centra IoT

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Registrace nového zařízení v centru IoT hub

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="create-a-simulated-device-app"></a>Vytvoření aplikace simulovaného zařízení

V této části vytvoříte aplikaci konzoly .NET, která reaguje na přímou metodu volanou back-endem řešení.

1. V sadě Visual Studio vyberte **Vytvořit nový projekt**a pak zvolte šablonu projektu **Konzola aplikace (.NET Framework).** Pokračujte výběrem tlačítka **Next** (Další).

1. V **části Konfigurace nového projektu**pojmenujte projekt *SimulateDeviceMethods*a pak vyberte **vytvořit**.

    ![Konfigurace projektu SimulateDeviceMethods](./media/iot-hub-csharp-csharp-schedule-jobs/configure-device-app.png)

1. V Průzkumníku řešení klepněte pravým tlačítkem myši na projekt **SimulateDeviceMethods** a pak vyberte **příkaz Manage NuGet Packages**.

1. Ve **Správci balíčků NuGet**vyberte **Procházet** a vyhledejte a zvolte **Microsoft.Azure.Devices.Client**. Vyberte **Install** (Nainstalovat).

    ![Klientská aplikace okna Správce balíčků NuGet](./media/iot-hub-csharp-csharp-schedule-jobs/device-app-nuget.png)

    Tento krok stáhne, nainstaluje a přidá odkaz na balíček SDK NuGet [zařízení Azure IoT](https://www.nuget.org/packages/Microsoft.Azure.Devices.Client/) a jeho závislosti.

1. Do horní části souboru **Program.cs** přidejte následující příkazy `using`:

    ```csharp
    using Microsoft.Azure.Devices.Client;
    using Microsoft.Azure.Devices.Shared;
    using Newtonsoft.Json;
    ```

1. Do třídy **Program** přidejte následující pole. Nahraďte zástupnou hodnotu připojovacím řetězcem zařízení, který jste zaznamenali v předchozí části:

    ```csharp
    static string DeviceConnectionString = "<yourDeviceConnectionString>";
    static DeviceClient Client = null;
    ```

1. Přidejte následující kód pro implementaci přímé metody na zařízení:

    ```csharp
    static Task<MethodResponse> LockDoor(MethodRequest methodRequest, object userContext)
    {
        Console.WriteLine();
        Console.WriteLine("Locking Door!");
        Console.WriteLine("\nReturning response for method {0}", methodRequest.Name);

        string result = "'Door was locked.'";
        return Task.FromResult(new MethodResponse(Encoding.UTF8.GetBytes(result), 200));
    }
    ```

1. Přidejte následující metodu k implementaci naslouchací proces dvojčat zařízení na zařízení:

    ```csharp
    private static async Task OnDesiredPropertyChanged(TwinCollection desiredProperties, 
      object userContext)
    {
        Console.WriteLine("Desired property change:");
        Console.WriteLine(JsonConvert.SerializeObject(desiredProperties));
    }
    ```

1. Nakonec přidejte následující kód do **metody Main,** abyste otevřeli připojení k centru IoT hub a inicializovali naslouchací proces metody:

    ```csharp
    try
    {
        Console.WriteLine("Connecting to hub");
        Client = DeviceClient.CreateFromConnectionString(DeviceConnectionString, 
          TransportType.Mqtt);

        Client.SetMethodHandlerAsync("LockDoor", LockDoor, null);
        Client.SetDesiredPropertyUpdateCallbackAsync(OnDesiredPropertyChanged, null);

        Console.WriteLine("Waiting for direct method call and device twin update\n Press enter to exit.");
        Console.ReadLine();

        Console.WriteLine("Exiting...");

        Client.SetMethodHandlerAsync("LockDoor", null, null);
        Client.CloseAsync().Wait();
    }
    catch (Exception ex)
    {
        Console.WriteLine();
        Console.WriteLine("Error in sample: {0}", ex.Message);
    }
    ```

1. Uložte svou práci a sestavte si řešení.

> [!NOTE]
> Chcete-li zachovat jednoduché věci, tento kurz neimplementuje žádné zásady opakování. V produkčním kódu byste měli implementovat zásady opakování (například opakování připojení), jak je navrženo v [převodním zpracování chyb](/azure/architecture/best-practices/transient-faults).
>

## <a name="get-the-iot-hub-connection-string"></a>Získání připojovacího řetězce centra IoT hub

[!INCLUDE [iot-hub-howto-schedule-jobs-shared-access-policy-text](../../includes/iot-hub-howto-schedule-jobs-shared-access-policy-text.md)]

[!INCLUDE [iot-hub-include-find-registryrw-connection-string](../../includes/iot-hub-include-find-registryrw-connection-string.md)]

## <a name="schedule-jobs-for-calling-a-direct-method-and-sending-device-twin-updates"></a>Naplánovat úlohy pro volání přímé metody a odesílání aktualizací dvojčete zařízení

V této části vytvoříte aplikaci konzoly .NET (pomocí jazyka C#), která používá úlohy k volání přímé metody **LockDoor** a odesílání požadovaných aktualizací vlastností do více zařízení.

1. V sadě Visual Studio vyberte **Soubor** > **nový** > **projekt**. V **okně Vytvořit nový projekt**zvolte Console App **(.NET Framework)** a pak vyberte **Další**.

1. V **části Konfigurace nového projektu**pojmenujte projekt *ScheduleJob*. V **řešení**zvolte **Přidat do řešení**a pak vyberte **Vytvořit**.

    ![Pojmenování a konfigurace projektu ScheduleJob](./media/iot-hub-csharp-csharp-schedule-jobs/config-schedule-job-app.png)

1. V Průzkumníku řešení klikněte pravým tlačítkem myši na projekt **ScheduleJob** a potom vyberte **Spravovat balíčky NuGet**.

1. Ve **Správci balíčků NuGet**vyberte **Procházet**, vyhledejte a zvolte **Microsoft.Azure.Devices**a pak vyberte **Instalovat**.

   Tento krok stáhne, nainstaluje a přidá odkaz na balíček [Služby Azure IoT SDK](https://www.nuget.org/packages/Microsoft.Azure.Devices/) NuGet a jeho závislosti.

1. Do horní části souboru **Program.cs** přidejte následující příkazy `using`:

    ```csharp
    using Microsoft.Azure.Devices;
    using Microsoft.Azure.Devices.Shared;
    ```

1. Přidejte `using` následující příkaz, pokud již není k dispozici ve výchozím příkazu.

    ```csharp
    using System.Threading;
    using System.Threading.Tasks;
    ```

1. Do třídy **Program** přidejte následující pole. Nahraďte zástupné symboly připojovacím řetězcem ioT hubu, který jste dříve zkopírovali v [připojovacím řetězci Centra IoT](#get-the-iot-hub-connection-string) a názvu vašeho zařízení.

    ```csharp
    static JobClient jobClient;
    static string connString = "<yourIotHubConnectionString>";
    static string deviceId = "<yourDeviceId>";
    ```

1. Do třídy **Program** přidejte následující metodu:

    ```csharp
    public static async Task MonitorJob(string jobId)
    {
        JobResponse result;
        do
        {
            result = await jobClient.GetJobAsync(jobId);
            Console.WriteLine("Job Status : " + result.Status.ToString());
            Thread.Sleep(2000);
        } while ((result.Status != JobStatus.Completed) && 
          (result.Status != JobStatus.Failed));
    }
    ```

1. Do třídy **Program** přidejte následující metodu:

    ```csharp
    public static async Task StartMethodJob(string jobId)
    {
        CloudToDeviceMethod directMethod = 
          new CloudToDeviceMethod("LockDoor", TimeSpan.FromSeconds(5), 
          TimeSpan.FromSeconds(5));

        JobResponse result = await jobClient.ScheduleDeviceMethodAsync(jobId,
            $"DeviceId IN ['{deviceId}']",
            directMethod,
            DateTime.UtcNow,
            (long)TimeSpan.FromMinutes(2).TotalSeconds);

        Console.WriteLine("Started Method Job");
    }
    ```

1. Do třídy **Program** přidejte další metodu:

    ```csharp
    public static async Task StartTwinUpdateJob(string jobId)
    {
        Twin twin = new Twin(deviceId);
        twin.Tags = new TwinCollection();
        twin.Tags["Building"] = "43";
        twin.Tags["Floor"] = "3";
        twin.ETag = "*";

        twin.Properties.Desired["LocationUpdate"] = DateTime.UtcNow;

        JobResponse createJobResponse = jobClient.ScheduleTwinUpdateAsync(
            jobId,
            $"DeviceId IN ['{deviceId}']", 
            twin, 
            DateTime.UtcNow, 
            (long)TimeSpan.FromMinutes(2).TotalSeconds).Result;

        Console.WriteLine("Started Twin Update Job");
    }
    ```

    > [!NOTE]
    > Další informace o syntaxi dotazu naleznete v [tématu Dotazovací jazyk centra IoT](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-query-language).
    >

1. Nakonec přidejte do metody **Main** následující řádky:

    ```csharp
    Console.WriteLine("Press ENTER to start running jobs.");
    Console.ReadLine();

    jobClient = JobClient.CreateFromConnectionString(connString);

    string methodJobId = Guid.NewGuid().ToString();

    StartMethodJob(methodJobId);
    MonitorJob(methodJobId).Wait();
    Console.WriteLine("Press ENTER to run the next job.");
    Console.ReadLine();

    string twinUpdateJobId = Guid.NewGuid().ToString();

    StartTwinUpdateJob(twinUpdateJobId);
    MonitorJob(twinUpdateJobId).Wait();
    Console.WriteLine("Press ENTER to exit.");
    Console.ReadLine();
    ```

1. Uložte svou práci a sestavte si řešení.

## <a name="run-the-apps"></a>Spouštění aplikací

Nyní jste připraveni aplikaci spustit.

1. V Průzkumníku řešení Sady Visual Studio klikněte pravým tlačítkem myši na vaše řešení a potom vyberte **Nastavit počáteční projekty**.

1. Vyberte **možnost Společné vlastnosti** > **projektu spuštění**a potom vyberte více projektů po **spuštění**.

1. Ujistěte `SimulateDeviceMethods` se, že je v `ScheduleJob`horní části seznamu následuje . Nastavte obě jejich akce na **Start** a vyberte **OK**.

1. Spuštění projektů klepnutím na tlačítko **Start** nebo přejděte do nabídky **Ladění** a klepněte na tlačítko **Spustit ladění**.

   Zobrazí se výstup z aplikací zařízení i back-endu.

    ![Spuštění aplikací pro plánování úloh](./media/iot-hub-csharp-csharp-schedule-jobs/schedule-jobs-console-results.png)

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste použili úlohu k naplánování přímé metody do zařízení a aktualizace vlastností dvojčete zařízení.

* Chcete-li pokračovat v začínáme s IoT Hub a vzory správy zařízení, jako je například dálkové ovládání přes aktualizaci firmwaru vzduchu, [přečtěte si návod: Jak to udělat aktualizaci firmwaru](tutorial-firmware-update.md).

* Další informace o nasazení ai na hraniční zařízení s Azure IoT Edge najdete [v tématu Začínáme s IoT Edge](../iot-edge/tutorial-simulate-device-linux.md).
