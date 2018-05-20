---
title: Spojení jednotlivých malin pí do aplikace Azure IoT centrální (C#) | Microsoft Docs
description: Jako vývojář zařízení jak připojit malin platformy Azure IoT centrální aplikace pomocí jazyka C#.
services: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 01/22/2018
ms.topic: article
ms.prod: microsoft-iot-central
manager: timlt
ms.openlocfilehash: d09d3de04f8c846eadc7367ca4d4559eb55f995b
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/16/2018
---
# <a name="connect-a-raspberry-pi-to-your-azure-iot-central-application-c"></a>Pi malin připojit k aplikaci Azure IoT centrální (C#)

[!INCLUDE [howto-raspberrypi-selector](../../includes/iot-central-howto-raspberrypi-selector.md)]

Tento článek popisuje, jak jako vývojář zařízení pro připojení k Microsoft Azure IoT centrální aplikace pomocí programovacího jazyka C# malin pí.

## <a name="before-you-begin"></a>Než začnete

K dokončení kroků v tomto článku budete potřebovat následující:

* [.NET core 2](https://www.microsoft.com/net) nainstalovaný na počítači pro vývoj. Měli byste také mít editor vhodný kódu, jako [Visual Studio Code](https://code.visualstudio.com/).
* Azure IoT centrální aplikace vytvořené pomocí **Devkits ukázka** šablony aplikace. Další informace najdete v tématu [vytvořit aplikaci Azure IoT centrální](howto-create-application.md).
* Malin platformy zařízení se systémem Raspbian operačního systému.

Aplikace vytvořené z **ukázka Devkits** obsahuje šablony aplikace **malin pí** šablona zařízení s následujícími charakteristikami:

### <a name="telemetry-measurements"></a>Měření telemetrie

| Název pole     | Jednotky  | Minimální | Maximum | Desetinných míst |
| -------------- | ------ | ------- | ------- | -------------- |
| vlhkosti       | %      | 0       | 100     | 0              |
| dočasné           | ° C     | -40     | 120     | 0              |
| pressure       | hPa    | 260     | 1260    | 0              |
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

| Zobrazované jméno | Název pole | Jednotky | Desetinných míst | Minimální | Maximum | Počáteční |
| ------------ | ---------- | ----- | -------------- | ------- | ------- | ------- |
| Napětí      | setVoltage | Intenzita | 0              | 0       | 240     | 0       |
| Aktuální      | setCurrent | A  | 0              | 0       | 100     | 0       |
| Ventilátor rychlosti    | fanSpeed   | OT. / MIN   | 0              | 0       | 1000    | 0       |

Přepnutí nastavení

| Zobrazované jméno | Název pole | Na text | Vypnout text | Počáteční |
| ------------ | ---------- | ------- | -------- | ------- |
| REAKCÍ NA INCIDENTY           | activateIR | ON      | OFF      | Vypnuto     |

### <a name="properties"></a>Vlastnosti

| Typ            | Zobrazované jméno | Název pole | Typ dat |
| --------------- | ------------ | ---------- | --------- |
| Vlastnosti zařízení | Kostka číslo   | dieNumber  | číslo    |
| Text            | Umístění     | location   | neuvedeno       |

### <a name="add-a-real-device"></a>Přidání skutečné zařízení

V aplikaci Azure IoT centrální přidat skutečné zařízení z **malin pí** šablona zařízení a je zaznamenána připojovací řetězec zařízení. Další informace najdete v tématu [přidat skutečné zařízení Azure IoT centrální aplikace](tutorial-add-device.md).

## <a name="create-your-net-application"></a>Vytvoření aplikace .NET

Vytvoření a testování aplikace zařízení na stolního počítače.

Pokud chcete provést následující kroky, můžete použít Visual Studio Code. Další informace najdete v tématu [práce s C#](https://code.visualstudio.com/docs/languages/csharp).

> [!NOTE]
> Pokud dáváte přednost, můžete provést následující kroky pomocí editoru jiný kód.

1. K inicializaci projektu rozhraní .NET a přidat požadované balíčky NuGet, spusťte následující příkazy:

  ```cmd/sh
  mkdir pisample
  cd pisample
  dotnet new console
  dotnet add package Microsoft.Azure.Devices.Client
  dotnet restore
  ```

1. Otevřete `pisample` složky ve Visual Studio Code. Otevřete **pisample.csproj** souboru projektu. Přidat `<RuntimeIdentifiers>` značky uvedené v následující fragment kódu:

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
    > Vaše **Microsoft.Azure.Devices.Client** číslo verze balíčku může být vyšší než ten, který zobrazí.

1. Uložit **pisample.csproj**. Pokud Visual Studio Code zobrazí výzvu k provedení příkazu restore, zvolte **obnovení**.

1. Otevřete **Program.cs** a nahraďte jeho obsah následujícím kódem:

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
    > Aktualizujte zástupný symbol `{your device connection string}` v dalším kroku.

## <a name="run-your-net-application"></a>Spuštění aplikace rozhraní .NET

Přidáte kód pro zařízení k ověřování pro Azure IoT centrální konkrétní zařízení připojovací řetězec. Při přidání zařízení skutečné do aplikace Azure IoT centrální, učinit poznámku o tento připojovací řetězec.

1. Nahraďte `{your device connection string}` v **Program.cs** souboru připojovacím řetězcem, který jste si poznamenali dříve.

1. V prostředí příkazového řádku spusťte následující příkaz:

  ```cmd/sh
  dotnet restore
  dotnet publish -r linux-arm
  ```

1. Kopírování `pisample\bin\Debug\netcoreapp2.0\linux-arm\publish` složky malin platformy zařízení. Můžete použít **spojovací bod služby** příkaz pro kopírování souborů, například:

    ```cmd/sh
    scp -r publish pi@192.168.0.40:publish
    ```

    Další informace najdete v tématu [vzdáleného přístupu platformy malin](https://www.raspberrypi.org/documentation/remote-access/).

1. Přihlaste se k zařízení malin platformy a spusťte následující příkazy v prostředí:

    ```cmd/sh
    sudo apt-get update
    sudo apt-get install libc6 libcurl3 libgcc1 libgssapi-krb5-2 liblttng-ust0 libstdc++6 libunwind8 libuuid1 zlib1g
    ```

1. Na vaše malin Pi spusťte následující příkazy:

    ```cmd/sh
    cd publish
    chmod 777 pisample
    ./pisample
    ```

    ![Zahájí programu](./media/howto-connect-raspberry-pi-csharp/device_begin.png)

1. V aplikaci Azure IoT centrální můžete zjistit, jak kód spuštěný na platformy malin komunikuje s aplikací:

    * Na **měření** stránky pro skutečné zařízení, můžete zobrazit telemetrii.
    * Na **vlastnosti** stránky, se zobrazí hodnota hlášení **kostka číslo** vlastnost.
    * Na **nastavení** stránky, můžete změnit různá nastavení na platformy malin například napětí a ventilátor rychlosti.

    Následující snímek obrazovky ukazuje pí malin přijetí Změna nastavení:

    ![Změna nastavení obdrží Malinová platformy](./media/howto-connect-raspberry-pi-csharp/device_switch.png)

## <a name="next-steps"></a>Další postup

Teď, když jste se naučili jak se připojit k aplikaci Azure IoT centrální malin Pi, tady jsou navrhované další kroky:

* [Připojit obecné klienta aplikace Node.js ve službě Azure IoT centrální](howto-connect-nodejs.md)