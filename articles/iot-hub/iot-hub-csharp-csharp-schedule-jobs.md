---
title: Plánování úloh s využitím Azure IoT Hub (.NET/.NET) | Microsoft Docs
description: Jak naplánovat úlohu Azure IoT Hub k vyvolání přímé metody na více zařízeních. Pomocí sady SDK pro zařízení Azure IoT pro .NET implementujete aplikace simulovaného zařízení a aplikaci služby pro spuštění úlohy.
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/20/2019
ms.author: robinsh
ms.custom: mqtt, devx-track-csharp
ms.openlocfilehash: 0391afe5562238f488fde7e50d914c46a5c32944
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "92142458"
---
# <a name="schedule-and-broadcast-jobs-net"></a>Úlohy plánování a vysílání (.NET)

[!INCLUDE [iot-hub-selector-schedule-jobs](../../includes/iot-hub-selector-schedule-jobs.md)]

Pomocí Azure IoT Hub můžete plánovat a sledovat úlohy, které aktualizují miliony zařízení. Úlohy můžete použít k těmto akcím:

* Aktualizace požadovaných vlastností

* Aktualizovat značky

* Vyvolání přímých metod

Úloha zalomí jednu z těchto akcí a sleduje spuštění proti sadě zařízení, které jsou definovány dotazem s dvojitým použitím zařízení. Například aplikace back-end může použít úlohu k vyvolání přímé metody na zařízeních 10 000, která zařízení restartuje. Zadáte sadu zařízení se zdvojeným dotazem zařízení a naplánujete, aby úloha běžela v budoucím čase. Tato úloha sleduje průběh, protože každé zařízení přijímá a provádí metodu restartování přímo.

Další informace o jednotlivých možnostech najdete v těchto tématech:

* Vlákna a vlastnosti zařízení: [Začínáme s dvojitými zprávami](iot-hub-csharp-csharp-twin-getstarted.md) a [kurzem zařízení: jak používat vlastnosti se zdvojeným zařízením](tutorial-device-twins.md)

* Přímé metody: [IoT Hub příručka pro vývojáře – přímé metody](iot-hub-devguide-direct-methods.md) a [kurz: použití přímých metod](quickstart-control-device-dotnet.md)

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

V tomto kurzu získáte informace o následujících postupech:

* Vytvořte aplikaci zařízení, která implementuje přímou metodu nazvanou **LockDoor**, kterou může volat back-end aplikace.

* Vytvořte aplikaci back-end, která vytvoří úlohu pro volání přímé metody **LockDoor** na více zařízeních. Jiná úloha odešle požadované aktualizace vlastností do více zařízení.

Na konci tohoto kurzu máte dvě konzolové aplikace .NET (C#):

* **SimulateDeviceMethods**. Tato aplikace se připojí ke službě IoT Hub a implementuje metodu **LockDoor** Direct.

* **ScheduleJob**. Tato aplikace používá úlohy pro volání přímé metody **LockDoor** a aktualizaci požadovaných vlastností zařízení na více zařízeních.

## <a name="prerequisites"></a>Požadavky

* Visual Studio

* Aktivní účet Azure. Pokud účet nemáte, můžete si během několika minut vytvořit [bezplatný účet](https://azure.microsoft.com/pricing/free-trial/).

* Ujistěte se, že je v bráně firewall otevřený port 8883. Ukázka zařízení v tomto článku používá protokol MQTT, který komunikuje přes port 8883. Tento port může být blokovaný v některých podnikových a vzdělávacích prostředích sítě. Další informace a způsoby, jak tento problém obejít, najdete v tématu [připojení k IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

## <a name="create-an-iot-hub"></a>Vytvoření centra IoT

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Registrace nového zařízení ve službě IoT Hub

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="create-a-simulated-device-app"></a>Vytvoření aplikace simulovaného zařízení

V této části vytvoříte konzolovou aplikaci .NET, která reaguje na přímou metodu volanou back-endu řešení.

1. V aplikaci Visual Studio vyberte možnost **vytvořit nový projekt** a pak zvolte šablonu projektu **Konzolová aplikace (.NET Framework)** . Pokračujte výběrem tlačítka **Další**.

1. V části **Konfigurovat nový projekt** pojmenujte projekt *SimulateDeviceMethods* a pak vyberte **vytvořit**.

    ![Konfigurace projektu SimulateDeviceMethods](./media/iot-hub-csharp-csharp-schedule-jobs/configure-device-app.png)

1. V Průzkumník řešení klikněte pravým tlačítkem na projekt **SimulateDeviceMethods** a pak vyberte **Spravovat balíčky NuGet**.

1. V **nástroji Správce balíčků NuGet** vyberte **Procházet** a vyhledejte a vyberte **Microsoft. Azure. Devices. Client**. Vyberte **Nainstalovat**.

    ![Klientská aplikace okna Správce balíčků NuGet](./media/iot-hub-csharp-csharp-schedule-jobs/device-app-nuget.png)

    Tento krok stáhne a nainstaluje balíček NuGet [sady SDK pro zařízení Azure IoT](https://www.nuget.org/packages/Microsoft.Azure.Devices.Client/) a jeho závislosti a přidá se na něj odkaz.

1. Do horní části souboru **Program.cs** přidejte následující příkazy `using`:

    ```csharp
    using Microsoft.Azure.Devices.Client;
    using Microsoft.Azure.Devices.Shared;
    using Newtonsoft.Json;
    ```

1. Do třídy **Program** přidejte následující pole. Nahraďte hodnotu zástupného symbolu připojovacím řetězcem zařízení, který jste si poznamenali v předchozí části:

    ```csharp
    static string DeviceConnectionString = "<yourDeviceConnectionString>";
    static DeviceClient Client = null;
    ```

1. Přidejte následující kód k implementaci přímé metody v zařízení:

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

1. Přidejte následující metodu pro implementaci naslouchacího procesu vláken zařízení na zařízení:

    ```csharp
    private static async Task OnDesiredPropertyChanged(TwinCollection desiredProperties, 
      object userContext)
    {
        Console.WriteLine("Desired property change:");
        Console.WriteLine(JsonConvert.SerializeObject(desiredProperties));
    }
    ```

1. Nakonec do metody **Main** přidejte následující kód, který otevře připojení ke službě IoT Hub a inicializuje naslouchací proces metody:

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

1. Uložte svoji práci a sestavte své řešení.

> [!NOTE]
> V tomto kurzu neimplementují žádné zásady opakování. V produkčním kódu byste měli implementovat zásady opakování (například opakování připojení), jak je navrženo při [zpracování přechodných chyb](/azure/architecture/best-practices/transient-faults).
>

## <a name="get-the-iot-hub-connection-string"></a>Získání připojovacího řetězce centra IoT Hub

[!INCLUDE [iot-hub-howto-schedule-jobs-shared-access-policy-text](../../includes/iot-hub-howto-schedule-jobs-shared-access-policy-text.md)]

[!INCLUDE [iot-hub-include-find-registryrw-connection-string](../../includes/iot-hub-include-find-registryrw-connection-string.md)]

## <a name="schedule-jobs-for-calling-a-direct-method-and-sending-device-twin-updates"></a>Plánování úloh pro volání přímé metody a odesílání bezplatných aktualizací zařízení

V této části vytvoříte konzolovou aplikaci .NET (pomocí jazyka C#), která používá úlohy pro volání přímé metody **LockDoor** a odeslání požadovaných aktualizací vlastností do více zařízení.

1. V aplikaci Visual Studio vyberte **soubor**  >  **Nový**  >  **projekt**. V možnosti **vytvořit nový projekt** zvolte **Konzolová aplikace (.NET Framework)** a pak vyberte **Další**.

1. V části **Konfigurovat nový projekt** pojmenujte projekt *ScheduleJob*. V případě **řešení** zvolte možnost **Přidat do řešení** a pak vyberte **vytvořit**.

    ![Název a konfigurace projektu ScheduleJob](./media/iot-hub-csharp-csharp-schedule-jobs/config-schedule-job-app.png)

1. V Průzkumník řešení klikněte pravým tlačítkem na projekt **ScheduleJob** a pak vyberte **Spravovat balíčky NuGet**.

1. V okně **Správce balíčků NuGet** vyberte **Procházet**, vyhledejte a zvolte **Microsoft. Azure. Devices** a pak vyberte **nainstalovat**.

   Tento krok stáhne a nainstaluje balíček NuGet [sady SDK služby Azure IoT](https://www.nuget.org/packages/Microsoft.Azure.Devices/) a jeho závislosti a přidá odkaz na něj.

1. Do horní části souboru **Program.cs** přidejte následující příkazy `using`:

    ```csharp
    using Microsoft.Azure.Devices;
    using Microsoft.Azure.Devices.Shared;
    ```

1. Přidejte následující `using` příkaz, pokud ve výchozích příkazech ještě neexistují.

    ```csharp
    using System.Threading;
    using System.Threading.Tasks;
    ```

1. Do třídy **Program** přidejte následující pole. Zástupné symboly nahraďte připojovacím řetězcem IoT Hub, který jste dříve zkopírovali v [části získání připojovacího řetězce služby IoT Hub](#get-the-iot-hub-connection-string) a názvu vašeho zařízení.

    ```csharp
    static JobClient jobClient;
    static string connString = "<yourIotHubConnectionString>";
    static string deviceId = "<yourDeviceId>";
    ```

1. Do třídy **program** přidejte následující metodu:

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

1. Do třídy **program** přidejte následující metodu:

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

1. Přidejte další metodu do třídy **program** :

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
    > Další informace o syntaxi dotazů naleznete v tématu [IoT Hub dotazovací jazyk](./iot-hub-devguide-query-language.md).
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

1. Uložte svoji práci a sestavte své řešení.

## <a name="run-the-apps"></a>Spouštění aplikací

Nyní jste připraveni aplikaci spustit.

1. V sadě Visual Studio Průzkumník řešení klikněte pravým tlačítkem na své řešení a pak vyberte **nastavit projekty po spuštění**.

1. Vyberte **společné vlastnosti**  >  **spouštěný projekt** a potom vyberte **více projektů po spuštění**.

1. Ujistěte se, že `SimulateDeviceMethods` je na začátku seznamu a následuje `ScheduleJob` . Nastavte obě akce na **Start** a vyberte **OK**.

1. Spusťte projekty kliknutím na tlačítko **Start** nebo přejděte do nabídky **ladění** a klikněte na tlačítko **Spustit ladění**.

   Uvidíte výstup jak v zařízení, tak i v back-endové aplikaci.

    ![Spuštění aplikací pro plánování úloh](./media/iot-hub-csharp-csharp-schedule-jobs/schedule-jobs-console-results.png)

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste použili úlohu k naplánování přímé metody na zařízení a aktualizaci vlastností vlákna zařízení.

* Pokud chcete pokračovat v seznámení se IoT Hub a způsoby správy zařízení, jako je například vzdálené prostřednictvím aktualizace firmwaru Air, přečtěte si [kurz: jak provést aktualizaci firmwaru](tutorial-firmware-update.md).

* Další informace o nasazování AI do hraničních zařízení pomocí Azure IoT Edge najdete v tématu [Začínáme s IoT Edge](../iot-edge/quickstart-linux.md).