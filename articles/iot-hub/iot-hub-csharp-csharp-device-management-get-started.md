---
title: Začínáme se správou zařízení Azure IoT Hub (.NET/.NET) | Dokumenty společnosti Microsoft
description: Jak pomocí správy zařízení Azure IoT Hub zahájit restartování vzdáleného zařízení. Pomocí sady Azure IoT zařízení SDK pro rozhraní .NET implementovat aplikaci simulované zařízení, která obsahuje přímou metodu a Služby Azure IoT SDK pro .NET k implementaci aplikace služby, která vyvolá přímou metodu.
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: conceptual
ms.date: 08/20/2019
ms.author: robinsh
ms.openlocfilehash: 3b37d7e049e7daabbbb4fe1a7b49feb654e8accc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77110259"
---
# <a name="get-started-with-device-management-net"></a>Začínáme se správou zařízení (.NET)

[!INCLUDE [iot-hub-selector-dm-getstarted](../../includes/iot-hub-selector-dm-getstarted.md)]

V tomto kurzu získáte informace o následujících postupech:

* Pomocí portálu Azure vytvořte centrum IoT a vytvořte identitu zařízení ve vašem centru IoT hub.

* Vytvořte simulovanou aplikaci zařízení, která obsahuje přímou metodu, která restartuje toto zařízení. Přímé metody jsou vyvolány z cloudu.

* Vytvořte aplikaci konzoly .NET, která volá metodu přímého restartování v aplikaci simulovaných zařízení prostřednictvím služby IoT hub.

Na konci tohoto kurzu budete mít dvě konzolové aplikace .NET:

* **SimulateManagedDevice**. Tato aplikace se připojí k centru IoT s identitou zařízení vytvořené dříve, obdrží metodu přímé restartování, simuluje fyzické restartování a hlásí čas pro poslední restartování.

* **TriggerReboot**. Tato aplikace volá přímou metodu v aplikaci simulované zařízení, zobrazí odpověď a zobrazí aktualizované hlášené vlastnosti.

## <a name="prerequisites"></a>Požadavky

* Visual Studio.

* Aktivní účet Azure. Pokud účet nemáte, můžete si během několika minut vytvořit [bezplatný účet](https://azure.microsoft.com/pricing/free-trial/).

* Zkontrolujte, zda je v bráně firewall otevřený port 8883. Ukázka zařízení v tomto článku používá protokol MQTT, který komunikuje přes port 8883. Tento port může být blokován v některých prostředích podnikové a vzdělávací sítě. Další informace a způsoby, jak tento problém vyřešit, najdete [v tématu připojení k centru IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

## <a name="create-an-iot-hub"></a>Vytvoření centra IoT

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Registrace nového zařízení v centru IoT hub

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="get-the-iot-hub-connection-string"></a>Získání připojovacího řetězce centra IoT hub

[!INCLUDE [iot-hub-howto-device-management-shared-access-policy-text](../../includes/iot-hub-howto-device-management-shared-access-policy-text.md)]

[!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

## <a name="trigger-a-remote-reboot-on-the-device-using-a-direct-method"></a>Spuštění vzdáleného restartování zařízení pomocí přímé metody

V této části vytvoříte aplikaci konzoly .NET pomocí jazyka C#, která iniciuje vzdálené restartování zařízení pomocí přímé metody. Aplikace používá dotazy na dvojče zařízení ke zjištění času posledního restartování pro toto zařízení.

1. V sadě Visual Studio vyberte **Vytvořit nový projekt**.

1. V **části Vytvořit nový projekt**vyhledejte a vyberte šablonu projektu **Konzola Aplikace (.NET Framework)** a pak vyberte **Další**.

1. V **části Konfigurace nového projektu**pojmenujte projekt *TriggerReboot*a vyberte rozhraní .NET Framework verze 4.5.1 nebo novější. Vyberte **Vytvořit**.

    ![Nový klasický desktopový projekt Visual C# pro systém Windows](./media/iot-hub-csharp-csharp-device-management-get-started/create-trigger-reboot-configure.png)

1. V **Průzkumníku řešení**klepněte pravým tlačítkem myši na projekt **TriggerReboot** a potom vyberte **příkaz Manage NuGet Packages**.

1. Vyberte **Procházet**, vyhledejte a vyberte **Microsoft.Azure.Devices**. Kliknutím **na Instalovat** nainstalujte balíček **Microsoft.Azure.Devices.**

    ![Okno Správce balíčků NuGet](./media/iot-hub-csharp-csharp-device-management-get-started/create-trigger-reboot-nuget-devices.png)

   Tento krok stáhne, nainstaluje a přidá odkaz na balíček [Služby Azure IoT SDK](https://www.nuget.org/packages/Microsoft.Azure.Devices/) NuGet a jeho závislosti.

1. Do horní části souboru **Program.cs** přidejte následující příkazy `using`:

   ```csharp
   using Microsoft.Azure.Devices;
   using Microsoft.Azure.Devices.Shared;
   ```

1. Do třídy **Program** přidejte následující pole. Nahraďte `{iot hub connection string}` zástupnou hodnotu připojovacím řetězcem služby IoT Hub, který jste dříve zkopírovali v [připojovacím řetězci Služby IoT hub](#get-the-iot-hub-connection-string).

   ```csharp
   static RegistryManager registryManager;
   static string connString = "{iot hub connection string}";
   static ServiceClient client;
   static string targetDevice = "myDeviceId";
   ```

1. Do třídy **Program** přidejte následující metodu.  Tento kód získá dvojče zařízení pro restartování zařízení a výstupy hlášené vlastnosti.

   ```csharp
   public static async Task QueryTwinRebootReported()
   {
       Twin twin = await registryManager.GetTwinAsync(targetDevice);
       Console.WriteLine(twin.Properties.Reported.ToJson());
   }
   ```

1. Do třídy **Program** přidejte následující metodu.  Tento kód iniciuje restartování zařízení pomocí přímé metody.

   ```csharp
   public static async Task StartReboot()
   {
       client = ServiceClient.CreateFromConnectionString(connString);
       CloudToDeviceMethod method = new CloudToDeviceMethod("reboot");
       method.ResponseTimeout = TimeSpan.FromSeconds(30);

       CloudToDeviceMethodResult result = await 
         client.InvokeDeviceMethodAsync(targetDevice, method);

       Console.WriteLine("Invoked firmware update on device.");
   }
   ```

1. Nakonec přidejte do metody **Main** následující řádky:

   ```csharp
   registryManager = RegistryManager.CreateFromConnectionString(connString);
   StartReboot().Wait();
   QueryTwinRebootReported().Wait();
   Console.WriteLine("Press ENTER to exit.");
   Console.ReadLine();
   ```

1. Vyberte **sestavení** > **sestavení řešení**.

> [!NOTE]
> Tento kurz provádí pouze jeden dotaz pro ohlášené vlastnosti zařízení. V produkčním kódu doporučujeme dotazování ke zjištění změn v ohlášených vlastnostech.

## <a name="create-a-simulated-device-app"></a>Vytvoření aplikace simulovaného zařízení

V této části:

* Vytvořte aplikaci konzoly .NET, která reaguje na přímou metodu volanou cloudem.

* Spusťte simulované restartování zařízení.

* Pomocí ohlášených vlastností povolte dotazy dvojčete zařízení k identifikaci zařízení a při jejich posledním restartování.

Chcete-li vytvořit aplikaci simulované zařízení, postupujte takto:

1. V sadě Visual Studio vyberte v řešení TriggerReboot, které jste již vytvořili, **soubor** > **nový** > **projekt**. V **části Vytvořit nový projekt**vyhledejte a vyberte šablonu projektu **Konzola Aplikace (.NET Framework)** a pak vyberte **Další**.

1. V **části Konfigurace nového projektu**pojmenujte projekt *SimulateManagedDevice*a pro **řešení**vyberte Přidat **do řešení**. Vyberte **Vytvořit**.

    ![Pojmenujte a přidejte projekt do řešení](./media/iot-hub-csharp-csharp-device-management-get-started/configure-device-app.png)

1. V Průzkumníku řešení klepněte pravým tlačítkem myši na nový projekt **SimulateManagedDevice** a pak vyberte **spravovat balíčky NuGet**.

1. Vyberte **Procházet**, vyhledejte a vyberte **Microsoft.Azure.Devices.Client**. Vyberte **Install** (Nainstalovat).

    ![Klientská aplikace okna Správce balíčků NuGet](./media/iot-hub-csharp-csharp-device-management-get-started/create-device-nuget-devices-client.png)

   Tento krok stáhne, nainstaluje a přidá odkaz na balíček SDK NuGet [zařízení Azure IoT](https://www.nuget.org/packages/Microsoft.Azure.Devices.Client/) a jeho závislosti.

1. Do horní části souboru **Program.cs** přidejte následující příkazy `using`:

    ```csharp
    using Microsoft.Azure.Devices.Client;
    using Microsoft.Azure.Devices.Shared;
    ```

1. Do třídy **Program** přidejte následující pole. Nahraďte zástupnou hodnotu připojovacím řetězcem zařízení, který jste dříve zaznamenali v [části Registrace nového zařízení v centru IoT](#register-a-new-device-in-the-iot-hub)hub . `{device connection string}`

    ```csharp
    static string DeviceConnectionString = "{device connection string}";
    static DeviceClient Client = null;
    ```

1. K implementaci přímé metody na zařízení přidejte následující:

   ```csharp
   static Task<MethodResponse> onReboot(MethodRequest methodRequest, object userContext)
   {
       // In a production device, you would trigger a reboot 
       //   scheduled to start after this method returns.
       // For this sample, we simulate the reboot by writing to the console
       //   and updating the reported properties.
       try
       {
           Console.WriteLine("Rebooting!");

           // Update device twin with reboot time. 
           TwinCollection reportedProperties, reboot, lastReboot;
           lastReboot = new TwinCollection();
           reboot = new TwinCollection();
           reportedProperties = new TwinCollection();
           lastReboot["lastReboot"] = DateTime.Now;
           reboot["reboot"] = lastReboot;
           reportedProperties["iothubDM"] = reboot;
           Client.UpdateReportedPropertiesAsync(reportedProperties).Wait();
       }
       catch (Exception ex)
       {
           Console.WriteLine();
           Console.WriteLine("Error in sample: {0}", ex.Message);
       }

       string result = @"{""result"":""Reboot started.""}";
       return Task.FromResult(new MethodResponse(Encoding.UTF8.GetBytes(result), 200));
   }
   ```

1. Nakonec přidejte následující kód do **metody Main,** abyste otevřeli připojení k centru IoT hub a inicializovali naslouchací proces metody:

   ```csharp
   try
   {
       Console.WriteLine("Connecting to hub");
       Client = DeviceClient.CreateFromConnectionString(DeviceConnectionString, 
         TransportType.Mqtt);

       // setup callback for "reboot" method
       Client.SetMethodHandlerAsync("reboot", onReboot, null).Wait();
       Console.WriteLine("Waiting for reboot method\n Press enter to exit.");
       Console.ReadLine();

       Console.WriteLine("Exiting...");

       // as a good practice, remove the "reboot" handler
       Client.SetMethodHandlerAsync("reboot", null, null).Wait();
       Client.CloseAsync().Wait();
   }
   catch (Exception ex)
   {
       Console.WriteLine();
       Console.WriteLine("Error in sample: {0}", ex.Message);
   }
   ```

1. V Průzkumníku řešení klikněte pravým tlačítkem myši na vaše řešení a potom vyberte **Nastavit počáteční projekty**.

1. Pro projekt**spuštění** **běžných vlastností** > , vyberte **projekt jednoho spuštění**a vyberte projekt **SimulateManagedDevice.** Výběrem **OK** uložte změny.

1. Vyberte **sestavení** > **sestavení řešení**.

> [!NOTE]
> Za účelem zjednodušení tento kurz neimplementuje žádné zásady opakování. V produkčním kódu byste měli implementovat zásady opakování (například exponenciální backoff), jak je navrženo v [přechodové zpracování chyb](/azure/architecture/best-practices/transient-faults).

## <a name="run-the-apps"></a>Spouštění aplikací

Nyní jste připraveni ke spuštění aplikací.

1. Chcete-li spustit aplikaci zařízení .NET **SimulateManagedDevice**, klepněte v Průzkumníku řešení pravým tlačítkem myši na projekt **SimulateManagedDevice,** vyberte **Ladit**a pak vyberte **Spustit novou instanci**. Aplikace by měla začít naslouchat volání metod z vašeho centra IoT hub.

1. Poté je zařízení připojeno a čeká se na vyvolání metody, klepněte pravým tlačítkem myši na projekt **TriggerReboot,** vyberte **ladění**a pak vyberte **Spustit novou instanci**.

   Měli byste vidět "Rebooting!" napsané v konzole **SimulatedManagedDevice** a hlášené vlastnosti zařízení, které zahrnují čas posledního restartování, napsaný v konzole **TriggerReboot.**

    ![Spuštění aplikace pro služby a zařízení](./media/iot-hub-csharp-csharp-device-management-get-started/combinedrun.png)

[!INCLUDE [iot-hub-dm-followup](../../includes/iot-hub-dm-followup.md)]
