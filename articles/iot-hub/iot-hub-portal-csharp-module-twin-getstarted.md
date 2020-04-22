---
title: Identita identity modulu Azure IoT Hub & dvojče modulu (portál a .NET)
description: Zjistěte, jak vytvořit identitu modulu a aktualizovat dvojče modulu pomocí portálu a .NET.
author: robinsh
manager: philmea
ms.author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: conceptual
ms.date: 08/20/2019
ms.custom: amqp
ms.openlocfilehash: a3258de2ed7269ab50e6feca3c421d55de5a9d91
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/22/2020
ms.locfileid: "81759781"
---
# <a name="get-started-with-iot-hub-module-identity-and-module-twin-using-the-portal-and-net-device"></a>Začínáme s identitou modulu a dvojčetem modulu služby IoT Hub pomocí portálu a zařízení .NET

[!INCLUDE [iot-hub-selector-module-twin-getstarted](../../includes/iot-hub-selector-module-twin-getstarted.md)]

> [!NOTE]
> [Identity modulů a dvojčata modulů](iot-hub-devguide-module-twins.md) se podobají identitě zařízení a dvojčeti zařízení služby Azure IoT Hub, ale poskytují větší úroveň členitosti. Zatímco identita zařízení Azure IoT Hub a dvojče zařízení umožňují back-endové aplikaci konfigurovat zařízení a poskytovat viditelnost podmínek zařízení, identita modulu a dvojče modulu poskytují tyto funkce pro jednotlivé součásti zařízení. Na schopných zařízeních s více komponentami, jako jsou zařízení založená na operačním systému nebo zařízení firmwaru, umožňují identity modulů a dvojčata modulů izolovanou konfiguraci a podmínky pro každou komponentu.
>

V tomto kurzu se dozvíte:

* Jak vytvořit identitu modulu na portálu.

* Jak pomocí sady SDK zařízení .NET aktualizovat dvojče modulu ze zařízení.

> [!NOTE]
> Informace o sadách Azure IoT SDK, které můžete použít k sestavení obou aplikací pro spuštění na zařízeních a back-endu vašeho řešení, najdete v [tématu sady Azure IoT SDK](iot-hub-devguide-sdks.md).
>

## <a name="prerequisites"></a>Požadavky

* Visual Studio.

* Aktivní účet Azure. Pokud účet nemáte, můžete si během několika minut vytvořit [bezplatný účet](https://azure.microsoft.com/pricing/free-trial/).

## <a name="create-a-hub"></a>Vytvoření rozbočovače

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-hub"></a>Registrace nového zařízení v rozbočovači

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="create-a-module-identity-in-the-portal"></a>Vytvoření identity modulu na portálu

V rámci jedné identity zařízení můžete vytvořit až 20 identit modulů. Chcete-li přidat identitu, postupujte takto:

1. Pro zařízení, které jste vytvořili v předchozí části, zvolte **Přidat identitu modulu** a vytvořte svou první identitu modulu.

1. Zadejte název *myFirstModule*. Uložte identitu modulu.

    ![Přidání identity modulu](./media/iot-hub-portal-csharp-module-twin-getstarted/add-module-identity.png)

    V dolní části obrazovky se zobrazí nová identita modulu. Vyberte ji, chcete-li zobrazit podrobnosti o identitě modulu.

    ![Zobrazit podrobnosti o identitě modulu](./media/iot-hub-portal-csharp-module-twin-getstarted/module-identity-details.png)

Uložit **řetězec Připojit - primární klíč**. Použijete jej v další části k nastavení modulu v zařízení.

## <a name="update-the-module-twin-using-net-device-sdk"></a>Aktualizace dvojčete modulu pomocí sady SDK pro zařízení .NET

Úspěšně jste vytvořili identitu modulu ve službě IoT Hub. Teď zkuste navázat komunikaci mezi simulovaným zařízením a cloudem. Po vytvoření identity modulu se ve službě IoT Hub implicitně vytvoří dvojče modulu. V této části vytvoříte na simulovaném zařízení konzolovou aplikaci .NET, která aktualizuje hlášené vlastnosti dvojčete modulu.

### <a name="create-a-visual-studio-project"></a>Vytvoření projektu ve Visual Studiu

Chcete-li vytvořit aplikaci, která aktualizuje ohlášené vlastnosti dvojčete modulu, postupujte takto:

1. V Sadě Visual Studio vyberte **Vytvořit nový projekt**, pak zvolte Console App **(.NET Framework)** a vyberte **Další**.

1. V **části Konfigurace nového projektu**zadejte jako název projektu **funkci** *UpdateModuleTwinReportedProperties* . **Chcete-li pokračovat,** vyberte možnost Vytvořit.

    ![Konfigurace projektu visual studia](./media/iot-hub-portal-csharp-module-twin-getstarted/configure-twins-project.png)

### <a name="install-the-latest-azure-iot-hub-net-device-sdk"></a>Instalace nejnovějšího zařízení SDK služby Azure IoT Hub .NET

Identita modulu a dvojče modulu jsou ve verzi Public Preview. Je k dispozici pouze v předprodejních sadách SDK služby IoT Hub. Chcete-li jej nainstalovat, postupujte takto:

1. V sadě Visual Studio otevřete **nástroje, který** > **spravuje balíčky** > **NuGet pro řešení**.

1. Vyberte **Procházet**a pak vyberte **Zahrnout předběžnou verzi**. Vyhledejte *microsoft.azure.devices.client*. Vyberte nejnovější verzi a nainstalujte.

    ![Instalace azure iot hub .net služby SDK preview](./media/iot-hub-csharp-csharp-module-twin-getstarted/install-sdk.png)

    Teď máte přístup ke všem funkcím modulu.

### <a name="get-your-module-connection-string"></a>Získání připojovacího řetězce modulu

Potřebujete připojovací řetězec modulu pro konzolovou aplikaci. Postupujte následovně:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/).

1. Přejděte do svého centra IoT hub a vyberte **zařízení IoT**. Otevřete **myFirstDevice** a uvidíte, že **myFirstModule** byl úspěšně vytvořen.

1. V části **Identity modulu**vyberte **příkaz myFirstModule** . V **části Podrobnosti identity modulu**zkopírujte **připojovací řetězec (primární klíč).**

    ![Podrobnosti o modulu na webu Azure Portal](./media/iot-hub-portal-csharp-module-twin-getstarted/module-identity-details.png)

### <a name="create-updatemoduletwinreportedproperties-console-app"></a>Vytvořit konzolovou aplikaci UpdateModuleTwinReportedProperties

Pokud chcete aplikaci vytvořit, postupujte takto:

1. Do horní části souboru **Program.cs** přidejte následující příkazy `using`:

  ```csharp
  using Microsoft.Azure.Devices.Client;
  using Microsoft.Azure.Devices.Shared;
  using Newtonsoft.Json;
  ```

2. Do třídy **Program** přidejte následující pole. Nahraďte zástupnou hodnotu připojovacím řetězcem modulu.

  ```csharp
  private const string ModuleConnectionString = "<Your module connection string>";
  private static ModuleClient Client = null;
  ```

3. Do třídy **Program** přidejte následující metodu **OnDesiredPropertyChanged**:

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

4. Nakonec nahraďte **Metodu Main** následujícím kódem:

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
  
  Tuto aplikaci můžete vytvořit a spustit pomocí **f5**.

Tento vzorový kód ukazuje, jak načíst dvojče modulu a aktualizovat hlášené vlastnosti pomocí protokolu AMQP. Ve verzi Public Preview podporujeme operace s dvojčetem modulu pouze pomocí protokolu AMQP.

## <a name="next-steps"></a>Další kroky

Chcete-li pokračovat v seznamování se službou IoT Hub a prozkoumat další scénáře IoT, podívejte se na tato témata:

* [Začínáme s identitou modulu a dvojčetem modulu služby IoT Hub pomocí zálohování .NET a zařízení .NET](iot-hub-csharp-csharp-module-twin-getstarted.md)

* [Začínáme se službou IoT Edge](../iot-edge/tutorial-simulate-device-linux.md)
