---
title: Připojte k aplikaci Azure IoT Centrale malinu PI (C#) | Microsoft Docs
description: Jako vývojář zařízení, jak připojit malinovou pi k aplikaci Azure IoT Central pomocí C#.
author: viv-liu
ms.author: viviali
ms.date: 09/13/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: d27f792b39a1809cde0f27186f343af7d7aef60a
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/25/2019
ms.locfileid: "75454160"
---
# <a name="connect-a-raspberry-pi-to-your-azure-iot-central-application-c"></a>Připojte k aplikaci Azure IoT Centrale malinu PI (C#)

[!INCLUDE [howto-raspberrypi-selector](../../../includes/iot-central-howto-raspberrypi-selector.md)]

[!INCLUDE [iot-central-original-pnp](../../../includes/iot-central-original-pnp-note.md)]

Tento článek popisuje, jak jako vývojář zařízení připojit k aplikaci Microsoft Azure IoT Central aplikace pomocí C# programovacího jazyka.

## <a name="before-you-begin"></a>Než začnete

K dokončení kroků v tomto článku budete potřebovat následující komponenty:

* Aplikace Azure IoT Central vytvořená ze šablony **starší verze aplikace** . Další informace najdete v [rychlém startu k vytvoření aplikace](quick-deploy-iot-central.md).
* Zařízení malinu PI, na kterém běží operační systém Raspbian. Malina. PI musí být schopná připojit se k Internetu. Další informace najdete v tématu [nastavení svého malinu PI](https://projects.raspberrypi.org/en/projects/raspberry-pi-setting-up/3).

## <a name="add-a-device-template"></a>Přidání šablony zařízení

V aplikaci Azure IoT Central přidejte novou šablonu zařízení **malin. PI** s následujícími charakteristikami:

* Telemetrii, která zahrnuje následující měření, která bude zařízení shromažďovat:
  * Vlhkost
  * Teplota
  * Tlak
  * Magnetometer (X, Y, Z)
  * Akcelerometr (X, Y, Z)
  * Vybavený gyroskopem (X, Y, Z)
* Nastavení
  * Napájení
  * Aktuální
  * Rychlost větráku
  * Přepínač IR
* Vlastnosti
  * Vlastnost zařízení číslo pro Die
  * Umístění vlastnosti cloudu

1. Z šablon zařízení ![vyberte **+ nový**](media/howto-connect-raspberry-pi-csharp/adddevicetemplate.png)
   

2. Vyberte **maliny PI** a vytvořte šablonu zařízení malin. PI ![přidat šablonu zařízení](media/howto-connect-raspberry-pi-csharp/newdevicetemplate.png)

Úplné podrobnosti o konfiguraci šablony zařízení najdete v [podrobnostech o šabloně zařízení malin. PI](#raspberry-pi-device-template-details).

## <a name="add-a-real-device"></a>Přidání skutečného zařízení

V aplikaci Azure IoT Central přidejte reálné zařízení ze šablony zařízení malin. **Pi** . Poznamenejte si podrobnosti o připojení zařízení (**ID oboru**, **ID zařízení**a **primární klíč**). Další informace najdete v tématu [Přidání reálného zařízení do aplikace Azure IoT Central](tutorial-add-device.md).

### <a name="create-your-net-application"></a>Vytvoření aplikace .NET

Aplikaci pro zařízení vytvoříte a otestujete na stolním počítači.

K provedení následujících kroků můžete použít Visual Studio Code. Další informace naleznete v tématu [Working C#with ](https://code.visualstudio.com/docs/languages/csharp).

> [!NOTE]
> Chcete-li, můžete provést následující kroky pomocí jiného editoru kódu.

1. Chcete-li inicializovat projekt .NET a přidat požadované balíčky NuGet, spusťte následující příkazy:

   ```cmd/sh
   mkdir pisample
   cd pisample
   dotnet new console
   dotnet add package Microsoft.Azure.Devices.Client
   dotnet add package Microsoft.Azure.Devices.Provisioning.Client
   dotnet add package Microsoft.Azure.Devices.Provisioning.Transport.Mqtt
   dotnet restore
   ```

1. Otevřete složku `pisample` v Visual Studio Code. Pak otevřete soubor projektu **pisample. csproj** . Přidejte značku `<RuntimeIdentifiers>` zobrazenou v následujícím fragmentu kódu:

    ```xml
    <Project Sdk="Microsoft.NET.Sdk">
      <PropertyGroup>
        <OutputType>Exe</OutputType>
        <TargetFramework>netcoreapp2.1</TargetFramework>
        <RootNamespace>pisample</RootNamespace>
        <RuntimeIdentifiers>win-arm;linux-arm</RuntimeIdentifiers>
      </PropertyGroup>
      <ItemGroup>
        <PackageReference Include="Microsoft.Azure.Devices.Client" Version="1.21.0" />
        <PackageReference Include="Microsoft.Azure.Devices.Provisioning.Client" Version="1.4.0" />
        <PackageReference Include="Microsoft.Azure.Devices.Provisioning.Transport.Mqtt" Version="1.1.8" />
      </ItemGroup>
    </Project>
    ```

    > [!NOTE]
    > Čísla verzí balíčků můžou být vyšší než uvedená.

1. Uložte **pisample. csproj**. Pokud Visual Studio Code zobrazí výzvu k provedení příkazu RESTORE, klikněte na tlačítko **obnovit**.

1. Otevřete **program.cs** a nahraďte obsah následujícím kódem. Aktualizujte `{your Scope ID}`, `{your Device ID}`a `{your Device Primary Key}` hodnoty, které jste si poznamenali dříve:

    ```csharp
    using System;
    using System.Text;
    using System.Threading;
    using System.Threading.Tasks;

    using Microsoft.Azure.Devices.Client;
    using Microsoft.Azure.Devices.Shared;
    using Microsoft.Azure.Devices.Provisioning.Client;
    using Microsoft.Azure.Devices.Provisioning.Client.Transport;

    using Newtonsoft.Json;

    namespace pisample
    {
      class Program
      {
        static string ScopeID = "{your Scope ID}";
        static string DeviceID = "{your Device ID}";
        static string PrimaryKey = "{your Device Primary Key}";
        static string GlobalDeviceEndpoint = "global.azure-devices-provisioning.net";
        static DeviceClient Client = null;
        static TwinCollection reportedProperties = new TwinCollection();
        static CancellationTokenSource cts;
        static double baseTemperature = 60;
        static double basePressure = 500;
        static double baseHumidity = 50;

        static async Task Main(string[] args)
        {
          Console.WriteLine("== Raspberry Pi Azure IoT Central example ==");

          try
          {

            using (var security = new SecurityProviderSymmetricKey(DeviceID, PrimaryKey, null))
            {
              DeviceRegistrationResult result = await RegisterDeviceAsync(security);
              if (result.Status != ProvisioningRegistrationStatusType.Assigned) {
                Console.WriteLine("Failed to register device");
                return;
              }
              IAuthenticationMethod auth = new DeviceAuthenticationWithRegistrySymmetricKey(result.DeviceId, (security as SecurityProviderSymmetricKey).GetPrimaryKey());
              Client = DeviceClient.Create(result.AssignedHub, auth, TransportType.Mqtt);
            }

            await SendDevicePropertiesAsync();

            Console.Write("Register settings changed handler...");
            await Client.SetDesiredPropertyUpdateCallbackAsync(HandleSettingChanged, null);
            Console.WriteLine("Done");

            cts = new CancellationTokenSource();
            Task task = SendTelemetryAsync(cts.Token);

            Console.WriteLine("Press any key to exit...");
            Console.ReadKey();
            cts.Cancel();
            await task;
          }
          catch (Exception ex)
          {
            Console.WriteLine();
            Console.WriteLine(ex.Message);
          }
        }

        public static async Task<DeviceRegistrationResult> RegisterDeviceAsync(SecurityProviderSymmetricKey security)
        {
            Console.WriteLine("Register device...");

            using (var transport = new ProvisioningTransportHandlerMqtt(TransportFallbackType.TcpOnly))
            {
              ProvisioningDeviceClient provClient =
                        ProvisioningDeviceClient.Create(GlobalDeviceEndpoint, ScopeID, security, transport);

              Console.WriteLine($"RegistrationID = {security.GetRegistrationID()}");


              Console.Write("ProvisioningClient RegisterAsync...");
              DeviceRegistrationResult result = await provClient.RegisterAsync();

              Console.WriteLine($"{result.Status}");
              Console.WriteLine($"ProvisioningClient AssignedHub: {result.AssignedHub}; DeviceID: {result.DeviceId}");

              return result;
            }
        }

        public static async Task SendDevicePropertiesAsync()
        {
            Console.WriteLine("Send device properties...");
            Random random = new Random();
            TwinCollection telemetryConfig = new TwinCollection();
            reportedProperties["dieNumber"] = random.Next(1, 6);
            Console.WriteLine(JsonConvert.SerializeObject(reportedProperties));

            await Client.UpdateReportedPropertiesAsync(reportedProperties);
        }

        private static async Task SendTelemetryAsync(CancellationToken token)
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


        private static async Task HandleSettingChanged(TwinCollection desiredProperties, object userContext)
        {
          Console.WriteLine("Received settings change...");
          Console.WriteLine(JsonConvert.SerializeObject(desiredProperties));

          string setting = "fanSpeed";
          if (desiredProperties.Contains(setting))
          {
            // Act on setting change, then
            BuildAcknowledgement(desiredProperties, setting);
          }
          setting = "setVoltage";
          if (desiredProperties.Contains(setting))
          {
            // Act on setting change, then
            BuildAcknowledgement(desiredProperties, setting);
          }
          setting = "setCurrent";
          if (desiredProperties.Contains(setting))
          {
            // Act on setting change, then
            BuildAcknowledgement(desiredProperties, setting);
          }
          setting = "activateIR";
          if (desiredProperties.Contains(setting))
          {
            // Act on setting change, then
            BuildAcknowledgement(desiredProperties, setting);
          }
          Console.WriteLine("Send settings changed acknowledgement...");
          await Client.UpdateReportedPropertiesAsync(reportedProperties);
        }

        private static void BuildAcknowledgement(TwinCollection desiredProperties, string setting)
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

## <a name="run-your-net-application"></a>Spuštění aplikace .NET

Sestavení a spuštění ukázkové aplikace:

1. Spusťte následující příkaz v prostředí příkazového řádku:

   ```cmd/sh
   dotnet restore
   dotnet publish -r linux-arm
   ```

1. Zkopírujte složku `pisample\bin\Debug\netcoreapp2.1\linux-arm\publish` do zařízení malin. PI. K kopírování souborů můžete použít příkaz **SCP** , například:

    ```cmd/sh
    scp -r publish pi@192.168.0.40:publish
    ```

    Další informace najdete v tématu [vzdálený přístup k malinu PI](https://www.raspberrypi.org/documentation/remote-access/).

1. Přihlaste se k zařízení malin. PI a spusťte v prostředí následující příkazy:

    ```cmd/sh
    sudo apt-get update
    sudo apt-get install libc6 libcurl3 libgcc1 libgssapi-krb5-2 liblttng-ust0 libstdc++6 libunwind8 libuuid1 zlib1g
    ```

1. V aplikaci malin PI spusťte následující příkazy:

    ```cmd/sh
    cd publish
    chmod 777 pisample
    ./pisample
    ```

    ![Začátek programu](./media/howto-connect-raspberry-pi-csharp/device_begin.png)

1. V aplikaci Azure IoT Central můžete vidět, jak kód spuštěný ve Malině PI komunikuje s aplikací:

   * Na stránce **měření** reálného zařízení můžete zobrazit telemetrii.
   * Na stránce **vlastnosti** se zobrazí hodnota vlastnosti nahlášené **číslo** .
   * Na stránce **Nastavení** můžete změnit různá nastavení pro malinu PI, jako je například napětí a rychlost ventilátoru.

     Na následujícím snímku obrazovky je uvedeno, že se v malinu PI přijímají Změna nastavení:

     ![Malina Pi obdrží změnu nastavení](./media/howto-connect-raspberry-pi-csharp/device_switch.png)

## <a name="raspberry-pi-device-template-details"></a>Podrobnosti šablony zařízení malin. PI

Aplikace vytvořená z ukázkové šablony aplikace **Devkits** zahrnuje šablonu zařízení **Malina v PI** s následujícími charakteristikami:

### <a name="telemetry-measurements"></a>Měření telemetrie

| Název pole     | Jednotky  | Minimální | Maximum | Desetinná místa |
| -------------- | ------ | ------- | ------- | -------------- |
| vlhkost       | %      | 0       | 100     | 0              |
| temp           | °C     | -40     | 120     | 0              |
| tlak       | hPa    | 260     | 1260    | 0              |
| magnetometerX  | mgauss | -1000   | 1 000    | 0              |
| magnetometerY  | mgauss | -1000   | 1 000    | 0              |
| magnetometerZ  | mgauss | -1000   | 1 000    | 0              |
| accelerometerX | mg/Nm3     | -2000   | 2000    | 0              |
| accelerometerY | mg/Nm3     | -2000   | 2000    | 0              |
| accelerometerZ | mg/Nm3     | -2000   | 2000    | 0              |
| gyroscopeX     | mdps   | -2000   | 2000    | 0              |
| gyroscopeY     | mdps   | -2000   | 2000    | 0              |
| gyroscopeZ     | mdps   | -2000   | 2000    | 0              |

### <a name="settings"></a>Nastavení

Číselná nastavení

| Zobrazované jméno | Název pole | Jednotky | Desetinná místa | Minimální | Maximum | Počáteční |
| ------------ | ---------- | ----- | -------------- | ------- | ------- | ------- |
| Napájení      | setVoltage | V voltech | 0              | 0       | 240     | 0       |
| Aktuální      | setCurrent | Amps  | 0              | 0       | 100     | 0       |
| Rychlost větráku    | fanSpeed   | /MIN   | 0              | 0       | 1 000    | 0       |

Přepnout nastavení

| Zobrazované jméno | Název pole | Na text | Vypnuto textu | Počáteční |
| ------------ | ---------- | ------- | -------- | ------- |
| IR           | activateIR | ON      | OFF      | Vypnuto     |

### <a name="properties"></a>Vlastnosti

| Typ            | Zobrazované jméno | Název pole | Data type                              |
| --------------- | ------------ | ---------- | -------------------------------------- |
| Vlastnost zařízení | Číslo Die   | dieNumber  | číslo                                 |
| Umístění        | Umístění     | location   | {lat: float, Long: float, ALT?: float} |

## <a name="next-steps"></a>Další kroky

Teď, když jste se naučili, jak připojit malinu PI ke svojí aplikaci IoT Central Azure, je doporučeným dalším krokem postup pro [nastavení vlastní šablony zařízení](howto-set-up-template.md) pro vlastní zařízení IoT.
