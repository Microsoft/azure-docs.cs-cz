---
title: Connnect Raspberry Pi až po aplikace Azure IoT Central (C#) | Dokumentace Microsoftu
description: Jako vývojář zařízení jak se připojit k Azure IoT Central aplikace pomocí jazyka C# Raspberry Pi.
author: viv-liu
ms.author: viviali
ms.date: 10/31/2018
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: 489a644bd2d17e2be3232ec522b9ed7e37d246ad
ms.sourcegitcommit: ada7419db9d03de550fbadf2f2bb2670c95cdb21
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/02/2018
ms.locfileid: "50956719"
---
# <a name="connect-a-raspberry-pi-to-your-azure-iot-central-application-c"></a>Připojte Raspberry Pi do aplikace Azure IoT Central (C#)

[!INCLUDE [howto-raspberrypi-selector](../../includes/iot-central-howto-raspberrypi-selector.md)]

Tento článek popisuje, jak jako vývojář zařízení připojit Raspberry Pi do aplikace Microsoft Azure IoT Central, pomocí programovacího jazyka C#.

## <a name="before-you-begin"></a>Než začnete

K dokončení kroků v tomto článku, budete potřebovat následující komponenty:

* [.NET core 2](https://www.microsoft.com/net) nainstalována na vývojovém počítači. Také byste měli mít vhodný kód editoru, jako [Visual Studio Code](https://code.visualstudio.com/).
* Azure IoT Central aplikace vytvořené z **ukázka Devkits** šablony aplikace. Další informace najdete v tématu [vytvořit aplikaci rychlý Start](quick-deploy-iot-central.md).
* Raspberry Pi zařízení se systémem Raspbian operačního systému.


## <a name="sample-devkits-application"></a>**Ukázkový Devkits** aplikace

Aplikace vytvořené z **ukázka Devkits** zahrnuje šablony aplikace **Raspberry Pi** šablona zařízení s následujícími charakteristikami: 

- Telemetrická data, která zahrnuje následující měření, které zařízení bude shromažďovat:
    - Vlhkost
    - Teplota
    - Tlak
    - Magnetometer (X, Y, Z)
    - Akcelerometr (X, Y, Z)
    - Volný setrvačník (X, Y, Z)
- Nastavení
    - Snímač napětí
    - Aktuální
    - Ventilátor rychlost
    - Přepnout reakcí na Incidenty.
- Vlastnosti
    - Kostka vlastnost počtu zařízení
    - Vlastnost Location cloudu

Najdete všechny podrobnosti o konfiguraci zařízení šablony [Podrobnosti šablony Raspberry PI zařízení](howto-connect-raspberry-pi-csharp.md#raspberry-pi-device-template-details)


## <a name="add-a-real-device"></a>Přidání skutečného zařízení

V aplikaci Azure IoT Central přidat z reálného zařízení **Raspberry Pi** šablona zařízení a zkontrolujte poznamenejte si připojovací řetězec zařízení. Další informace najdete v tématu [skutečné zařízení přidat do aplikace Azure IoT Central](tutorial-add-device.md).

### <a name="create-your-net-application"></a>Vytvoření aplikace .NET

Vytvoření a testování aplikace zařízení na desktopovém počítači.

Dokončete následující postup můžete použít Visual Studio Code. Další informace najdete v tématu [práce s jazykem C#](https://code.visualstudio.com/docs/languages/csharp).

> [!NOTE]
> Pokud dáváte přednost, můžete použít následující kroky pomocí editoru odlišný kód.

1. Inicializace projektu .NET a přidat požadované balíčky NuGet, spusťte následující příkazy:

  ```cmd/sh
  mkdir pisample
  cd pisample
  dotnet new console
  dotnet add package Microsoft.Azure.Devices.Client
  dotnet restore
  ```

1. Otevřít `pisample` složky ve Visual Studio Code. Otevřete **pisample.csproj** souboru projektu. Přidat `<RuntimeIdentifiers>` značka je znázorněno v následujícím fragmentu kódu:

    ```xml
    <Project Sdk="Microsoft.NET.Sdk">
      <PropertyGroup>
        <OutputType>Exe</OutputType>
        <TargetFramework>netcoreapp2.0</TargetFramework>
        <RuntimeIdentifiers>win-arm;linux-arm</RuntimeIdentifiers>
      </PropertyGroup>
      <ItemGroup>
        <PackageReference Include="Microsoft.Azure.Devices.Client" Version="1.5.2" />
      </ItemGroup>
    </Project>
    ```

    > [!NOTE]
    > Vaše **Microsoft.Azure.Devices.Client** může být vyšší než ten, který zobrazí číslo verze balíčku.

1. Uložit **pisample.csproj**. Pokud Visual Studio Code zobrazí výzvu ke spuštění příkazu restore, zvolte **obnovení**.

1. Otevřít **Program.cs** a nahraďte jeho obsah následujícím kódem:

    ```csharp
    using System;
    using System.Text;
    using System.Threading;
    using System.Threading.Tasks;

    using Microsoft.Azure.Devices.Client;
    using Microsoft.Azure.Devices.Shared;
    using Newtonsoft.Json;

    namespace pisample
    {
      class Program
      {
        static string DeviceConnectionString = "{your device connection string}";
        static DeviceClient Client = null;
        static TwinCollection reportedProperties = new TwinCollection();
        static CancellationTokenSource cts;
        static double baseTemperature = 60;
        static double basePressure = 500;
        static double baseHumidity = 50;
        static void Main(string[] args)
        {
          Console.WriteLine("Raspberry Pi Azure IoT Central example");

          try
          {
            InitClient();
            SendDeviceProperties();

            cts = new CancellationTokenSource();
            SendTelemetryAsync(cts.Token);

            Console.WriteLine("Wait for settings update...");
            Client.SetDesiredPropertyUpdateCallbackAsync(HandleSettingChanged, null).Wait();
            Console.ReadKey();
            cts.Cancel();
          }
          catch (Exception ex)
          {
            Console.WriteLine();
            Console.WriteLine("Error in sample: {0}", ex.Message);
          }
        }

        public static void InitClient()
        {
          try
          {
            Console.WriteLine("Connecting to hub");
            Client = DeviceClient.CreateFromConnectionString(DeviceConnectionString, TransportType.Mqtt);
          }
          catch (Exception ex)
          {
            Console.WriteLine();
            Console.WriteLine("Error in sample: {0}", ex.Message);
          }
        }

        public static async void SendDeviceProperties()
        {
          try
          {
            Console.WriteLine("Sending device properties:");
            Random random = new Random();
            TwinCollection telemetryConfig = new TwinCollection();
            reportedProperties["dieNumber"] = random.Next(1, 6);
            Console.WriteLine(JsonConvert.SerializeObject(reportedProperties));

            await Client.UpdateReportedPropertiesAsync(reportedProperties);
          }
          catch (Exception ex)
          {
            Console.WriteLine();
            Console.WriteLine("Error in sample: {0}", ex.Message);
          }
        }

        private static async void SendTelemetryAsync(CancellationToken token)
        {
          try
          {
            Random rand = new Random();

            while (true)
            {
              double currentTemperature = baseTemperature + rand.NextDouble() * 20;
              double currentPressure = basePressure + rand.NextDouble() * 100;
              double currentHumidity = baseHumidity + rand.NextDouble() * 20;

              var telemetryDataPoint = new
              {
                humidity = currentHumidity,
                pressure = currentPressure,
                temp = currentTemperature
              };
              var messageString = JsonConvert.SerializeObject(telemetryDataPoint);
              var message = new Message(Encoding.ASCII.GetBytes(messageString));

              token.ThrowIfCancellationRequested();
              await Client.SendEventAsync(message);

              Console.WriteLine("{0} > Sending telemetry: {1}", DateTime.Now, messageString);

              await Task.Delay(1000);
            }
          }
          catch (Exception ex)
          {
            Console.WriteLine();
            Console.WriteLine("Intentional shutdown: {0}", ex.Message);
          }
        }

        private static async Task HandleSettingChanged(TwinCollection desiredProperties, object userContext)
        {
          try
          {
            Console.WriteLine("Received settings change...");
            Console.WriteLine(JsonConvert.SerializeObject(desiredProperties));

            string setting = "fanSpeed";
            if (desiredProperties.Contains(setting))
            {
              // Act on setting change, then
              AcknowledgeSettingChange(desiredProperties, setting);
            }
            setting = "setVoltage";
            if (desiredProperties.Contains(setting))
            {
              // Act on setting change, then
              AcknowledgeSettingChange(desiredProperties, setting);
            }
            setting = "setCurrent";
            if (desiredProperties.Contains(setting))
            {
              // Act on setting change, then
              AcknowledgeSettingChange(desiredProperties, setting);
            }
            setting = "activateIR";
            if (desiredProperties.Contains(setting))
            {
              // Act on setting change, then
              AcknowledgeSettingChange(desiredProperties, setting);
            }
            await Client.UpdateReportedPropertiesAsync(reportedProperties);
          }

          catch (Exception ex)
          {
            Console.WriteLine();
            Console.WriteLine("Error in sample: {0}", ex.Message);
          }
        }

        private static void AcknowledgeSettingChange(TwinCollection desiredProperties, string setting)
        {
          reportedProperties[setting] = new
          {
            value = desiredProperties[setting]["value"],
            status = "completed",
            desiredVersion = desiredProperties["$version"],
            message = "Processed"
          };
        }
      }
    }
    ```

    > [!NOTE]
    > Aktualizujte zástupný text `{your device connection string}` v dalším kroku.

## <a name="run-your-net-application"></a>Spustit aplikaci v .NET

Přidání konkrétní zařízení připojovací řetězec do kódu pro zařízení pro ověřování pomocí Azure IoT Central. Jste si poznamenali připojovacího řetězce při přidání skutečné zařízení do aplikace Azure IoT Central.

  > [!NOTE]
   > Azure IoT Central převedl na používání služby Azure IoT Hub Device Provisioning (DPS) pro všechna připojení zařízení, postupujte podle těchto instrustions k [získat připojovací řetězec zařízení](concepts-connectivity.md#getting-device-connection-string) a pokračujte se zbývající část tohoto kurzu.

1. Nahraďte `{your device connection string}` v **Program.cs** soubor s připojovacím řetězcem, který jste si poznamenali dříve.

1. V prostředí příkazového řádku spusťte následující příkaz:

  ```cmd/sh
  dotnet restore
  dotnet publish -r linux-arm
  ```

1. Kopírovat `pisample\bin\Debug\netcoreapp2.0\linux-arm\publish` složku pro Raspberry Pi zařízení. Můžete použít **spojovací bod služby** příkaz pro kopírování souborů, například:

    ```cmd/sh
    scp -r publish pi@192.168.0.40:publish
    ```

    Další informace najdete v tématu [vzdáleného přístupu Raspberry Pi](https://www.raspberrypi.org/documentation/remote-access/).

1. Přihlaste se k zařízení Raspberry Pi a spusťte následující příkazy v prostředí:

    ```cmd/sh
    sudo apt-get update
    sudo apt-get install libc6 libcurl3 libgcc1 libgssapi-krb5-2 liblttng-ust0 libstdc++6 libunwind8 libuuid1 zlib1g
    ```

1. Raspberry Pi spusťte následující příkazy:

    ```cmd/sh
    cd publish
    chmod 777 pisample
    ./pisample
    ```

    ![Spuštění programu](./media/howto-connect-raspberry-pi-csharp/device_begin.png)

1. V aplikaci Azure IoT Central uvidíte, jak kód spuštěný na Raspberry Pi komunikuje s aplikací:

    * Na **měření** stránky pro skutečné zařízení, zobrazí se telemetrie.
    * Na **vlastnosti** stránky, zobrazí se hodnota hlášení **kostka číslo** vlastnost.
    * Na **nastavení** stránky, můžete změnit různá nastavení na Raspberry Pi, jako je například napětí a podporuje a rychlost.

    Následující snímek obrazovky ukazuje Raspberry Pi přijetí změny nastavení:

    ![Změna nastavení obdrží Raspberry Pi](./media/howto-connect-raspberry-pi-csharp/device_switch.png)


## <a name="raspberry-pi-device-template-details"></a>Podrobnosti šablony Raspberry PI zařízení

Aplikace vytvořené z **ukázka Devkits** zahrnuje šablony aplikace **Raspberry Pi** šablona zařízení s následujícími charakteristikami:

### <a name="telemetry-measurements"></a>Měření telemetrie

| Název pole     | Jednotky  | Minimální | Maximum | Desetinná místa |
| -------------- | ------ | ------- | ------- | -------------- |
| vlhkost       | %      | 0       | 100     | 0              |
| temp           | ° C     | -40     | 120     | 0              |
| tlak       | hPa    | 260     | 1260    | 0              |
| magnetometerX  | mgauss | -1000   | 1000    | 0              |
| magnetometerY  | mgauss | -1000   | 1000    | 0              |
| magnetometerZ  | mgauss | -1000   | 1000    | 0              |
| accelerometerX | mg     | -2000   | 2000    | 0              |
| accelerometerY | mg     | -2000   | 2000    | 0              |
| accelerometerZ | mg     | -2000   | 2000    | 0              |
| gyroscopeX     | mdps   | -2000   | 2000    | 0              |
| gyroscopeY     | mdps   | -2000   | 2000    | 0              |
| gyroscopeZ     | mdps   | -2000   | 2000    | 0              |

### <a name="settings"></a>Nastavení

Číselné nastavení

| Zobrazované jméno | Název pole | Jednotky | Desetinná místa | Minimální | Maximum | Počáteční |
| ------------ | ---------- | ----- | -------------- | ------- | ------- | ------- |
| Snímač napětí      | setVoltage | Intenzita | 0              | 0       | 240     | 0       |
| Aktuální      | setCurrent | A  | 0              | 0       | 100     | 0       |
| Ventilátor rychlost    | fanSpeed   | OT. / MIN   | 0              | 0       | 1000    | 0       |

Přepínací tlačítko Nastavení

| Zobrazované jméno | Název pole | Na text | Vypnout text | Počáteční |
| ------------ | ---------- | ------- | -------- | ------- |
| IR           | activateIR | ON      | OFF      | Vypnuto     |

### <a name="properties"></a>Vlastnosti

| Typ            | Zobrazované jméno | Název pole | Typ dat |
| --------------- | ------------ | ---------- | --------- |
| Vlastnosti zařízení | Kostka čísla   | dieNumber  | číslo    |
| Text            | Umístění     | location   | neuvedeno       |

## <a name="next-steps"></a>Další postup

Teď, když jste se naučili, jak připojit Raspberry Pi do aplikace Azure IoT Central, tady jsou další navrhované kroky:

* [Připojit obecný klientská aplikace Node.js do Azure IoT Central](howto-connect-nodejs.md)
