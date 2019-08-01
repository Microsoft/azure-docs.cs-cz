---
title: Začínáme s identitou modulu a dvojčetem modulu služby Azure IoT Hub (portál a .NET) | Microsoft Docs
description: Zjistěte, jak vytvořit identitu modulu a aktualizovat dvojče modulu pomocí portálu a .NET.
author: robinsh
manager: philmea
ms.author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: conceptual
ms.date: 04/26/2018
ms.openlocfilehash: cb7b60de3bfcd31b523b9be0fc49a3c621c8446f
ms.sourcegitcommit: fecb6bae3f29633c222f0b2680475f8f7d7a8885
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/30/2019
ms.locfileid: "68668125"
---
# <a name="get-started-with-iot-hub-module-identity-and-module-twin-using-the-portal-and-net-device"></a>Začínáme s identitou modulu a dvojčetem modulu služby IoT Hub pomocí portálu a zařízení .NET

[!INCLUDE [iot-hub-selector-module-twin-getstarted](../../includes/iot-hub-selector-module-twin-getstarted.md)]

> [!NOTE]
> [Identity modulů a dvojčata modulů](iot-hub-devguide-module-twins.md) se podobají identitě zařízení a dvojčeti zařízení služby Azure IoT Hub, ale poskytují větší úroveň členitosti. Zatímco identita zařízení a dvojče zařízení služby Azure IoT Hub umožňují back-endové aplikaci konfigurovat zařízení a poskytují vhled do stavu zařízení, identita modulu a dvojče modulu poskytují tyto možnosti pro jednotlivé součásti zařízení. Na způsobilých zařízeních s několika součástmi, jako jsou zařízení s operačním systémem nebo zařízení s firmwarem, to umožňuje izolovanou konfiguraci a vhled do stavu jednotlivých součástí.
>

V tomto kurzu se dozvíte:

1. Jak vytvořit identitu modulu na portálu.

2. Jak používat sadu SDK pro zařízení .NET aktualizujte modul z vašeho zařízení jako nezávisle.

> [!NOTE]
> Informace o sadách Azure IoT SDK, které můžete použít k vytváření aplikací pro spouštění na zařízeních a back-endu vašeho řešení, najdete v tématu sady [SDK Azure IoT](iot-hub-devguide-sdks.md).
>

Pro absolvování tohoto kurzu potřebujete:

* Visual Studio.
* Aktivní účet Azure. (Pokud účet nemáte, můžete si během několika minut vytvořit [bezplatný účet](https://azure.microsoft.com/pricing/free-trial/) .)

## <a name="create-an-iot-hub"></a>Vytvoření centra IoT

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Registrace nového zařízení ve službě IoT Hub

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="create-a-module-identity-in-the-portal"></a>Vytvoření identity modulu na portálu

V rámci jedné identity zařízení můžete vytvořit až 20 identit modulů. Kliknutím na tlačítko **Přidat identitu modulu** v horní části vytvořte první identitu modulu s názvem **myFirstModule**.

  ![Detaily zařízení](./media/iot-hub-portal-csharp-module-twin-getstarted/create-module-id.png)

Uložte právě vytvořenou identitu modulu a klikněte na ni. Zobrazí se podrobnosti o identitě modulu. Uložte připojovací řetězec – primární klíč. Použijete ho v další části, kde nastavíte svůj modul na zařízení.

  ![Detaily zařízení](./media/iot-hub-portal-csharp-module-twin-getstarted/module-details.png)

## <a name="update-the-module-twin-using-net-device-sdk"></a>Aktualizace dvojčete modulu pomocí sady SDK pro zařízení .NET

Úspěšně jste vytvořili identitu modulu ve službě IoT Hub. Teď zkuste navázat komunikaci mezi simulovaným zařízením a cloudem. Po vytvoření identity modulu se ve službě IoT Hub implicitně vytvoří dvojče modulu. V této části vytvoříte na simulovaném zařízení konzolovou aplikaci .NET, která aktualizuje hlášené vlastnosti dvojčete modulu.

## <a name="create-a-visual-studio-project"></a>Vytvoření projektu ve Visual Studiu

V aplikaci Visual Studio přidejte do stávajícího C# řešení klasický desktopový projekt pro Visual Windows pomocí šablony projektu **Konzolová aplikace (.NET Framework)** . Ujistěte se, že máte .NET Framework verze 4.6.1 nebo novější. Pojmenujte projekt **UpdateModuleTwinReportedProperties**.

  ![Vytvoření projektu v sadě Visual Studio](./media/iot-hub-csharp-csharp-module-twin-getstarted/update-twins-csharp1.png)

## <a name="install-the-latest-azure-iot-hub-net-device-sdk"></a>Nainstalovat nejnovější sadu SDK pro zařízení Azure IoT Hub .NET

Identita modulu a modul s dvojitou identitou jsou ve verzi Public Preview. Je k dispozici pouze v sadách IoT Hub předběžných vydání sady SDK pro zařízení. V sadě Visual Studio otevřete Nástroje > Správce balíčků NuGet > Spravovat balíčky NuGet pro řešení. Vyhledejte Microsoft.Azure.Devices.Client. Ujistěte se, že jste zaškrtli políčko zahrnout předběžné verze. Vyberte nejnovější verzi a nainstalujte. Teď máte přístup ke všem funkcím modulu.

  ![Instalace sady SDK služby Azure IoT Hub pro .NET verze V1.16.0-preview-005](./media/iot-hub-csharp-csharp-module-twin-getstarted/install-sdk.png)

## <a name="get-your-module-connection-string"></a>Získání připojovacího řetězce modulu

Přihlaste se k [portálu Azure](https://portal.azure.com/). Přejděte do vaší služby IoT Hub a klikněte na Zařízení IoT. Najděte myFirstDevice, otevřete ho a uvidíte, že myFirstModule se úspěšně vytvořil. Zkopírujte připojovací řetězec modulu. Budete ho potřebovat v dalším kroku.

  ![Podrobnosti o modulu na webu Azure Portal](./media/iot-hub-csharp-csharp-module-twin-getstarted/module-detail.png)

## <a name="create-updatemoduletwinreportedproperties-console-app"></a>Vytvoření aplikace konzoly UpdateModuleTwinReportedProperties

Do horní části souboru **Program.cs** přidejte následující příkazy `using`:

```csharp
using Microsoft.Azure.Devices.Client;
using Microsoft.Azure.Devices.Shared;
```

Do třídy **Program** přidejte následující pole. Nahraďte zástupnou hodnotu připojovacím řetězcem modulu.

```csharp
private const string ModuleConnectionString = "<Your module connection string>";
private static ModuleClient Client = null;
```

Do třídy **Program** přidejte následující metodu **OnDesiredPropertyChanged**:

```csharp
private static async Task OnDesiredPropertyChanged(TwinCollection desiredProperties, object userContext)
    {
        Console.WriteLine("desired property change:");
        Console.WriteLine(JsonConvert.SerializeObject(desiredProperties));
        Console.WriteLine("Sending current time as reported property");
        TwinCollection reportedProperties = new TwinCollection
        {
            ["DateTimeLastDesiredPropertyChangeReceived"] = DateTime.Now
        };

        await Client.UpdateReportedPropertiesAsync(reportedProperties).ConfigureAwait(false);
    }
```

Nakonec do metody **Main** přidejte následující řádky:

```csharp
static void Main(string[] args)
{
    Microsoft.Azure.Devices.Client.TransportType transport = Microsoft.Azure.Devices.Client.TransportType.Amqp;

    try
    {
        Client = ModuleClient.CreateFromConnectionString(ModuleConnectionString, transport);
        Client.SetConnectionStatusChangesHandler(ConnectionStatusChangeHandler);
        Client.SetDesiredPropertyUpdateCallbackAsync(OnDesiredPropertyChanged, null).Wait();

        Console.WriteLine("Retrieving twin");
        var twinTask = Client.GetTwinAsync();
        twinTask.Wait();
        var twin = twinTask.Result;
        Console.WriteLine(JsonConvert.SerializeObject(twin));

        Console.WriteLine("Sending app start time as reported property");
        TwinCollection reportedProperties = new TwinCollection();
        reportedProperties["DateTimeLastAppLaunch"] = DateTime.Now;

        Client.UpdateReportedPropertiesAsync(reportedProperties);
    }
    catch (AggregateException ex)
    {
        Console.WriteLine("Error in sample: {0}", ex);
    }

    Console.WriteLine("Waiting for Events.  Press enter to exit...");
    Console.ReadKey();
    Client.CloseAsync().Wait();
}

private static void ConnectionStatusChangeHandler(ConnectionStatus status, ConnectionStatusChangeReason reason)
{
    Console.WriteLine($"Status {status} changed: {reason}");
}
```

Tento vzorový kód ukazuje, jak načíst dvojče modulu a aktualizovat hlášené vlastnosti pomocí protokolu AMQP. Ve verzi Public Preview podporujeme operace s dvojčetem modulu pouze pomocí protokolu AMQP.

## <a name="run-the-apps"></a>Spouštění aplikací

Nyní jste připraveni aplikaci spustit. V sadě Visual Studio v Průzkumníku řešení klikněte pravým tlačítkem na řešení a potom klikněte na tlačítko **Nastavit projekty po spuštění**. Vyberte **Více projektů po spuštění** a pak jako akci pro konzolovou aplikaci vyberte **Spustit**. A potom stisknutím klávesy F5 spusťte obě aplikace spuštěné.

## <a name="next-steps"></a>Další postup

Chcete-li pokračovat v seznamování se službou IoT Hub a prozkoumat další scénáře IoT, podívejte se na tato témata:

* [Začínáme s identitou modulu IoT Hub a modulem s dvojitým zprovozněním pomocí .NET Backup a zařízení .NET](iot-hub-csharp-csharp-module-twin-getstarted.md)

* [Začínáme s IoT Edge](../iot-edge/tutorial-simulate-device-linux.md)
