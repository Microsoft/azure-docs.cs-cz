---
title: Vlákna & modulu identity modulu Azure IoT Hub (portál a .NET)
description: Zjistěte, jak vytvořit identitu modulu a aktualizovat dvojče modulu pomocí portálu a .NET.
author: robinsh
manager: philmea
ms.author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: conceptual
ms.date: 08/20/2019
ms.custom: amqp, devx-track-csharp
ms.openlocfilehash: 0d16d7a6dbf903ae790015c12e3d34e4a2a553d3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "92139343"
---
# <a name="get-started-with-iot-hub-module-identity-and-module-twin-using-the-portal-and-net-device"></a>Začínáme s identitou modulu a dvojčetem modulu služby IoT Hub pomocí portálu a zařízení .NET

[!INCLUDE [iot-hub-selector-module-twin-getstarted](../../includes/iot-hub-selector-module-twin-getstarted.md)]

> [!NOTE]
> [Identity modulů a dvojčata modulů](iot-hub-devguide-module-twins.md) se podobají identitě zařízení a dvojčeti zařízení služby Azure IoT Hub, ale poskytují větší úroveň členitosti. I když se v Azure IoT Hub identita zařízení a zařízení dokončí v back-endové aplikaci pro konfiguraci zařízení a zajištění viditelnosti podmínek zařízení, identita modulu a funkční vlákna poskytují tyto možnosti pro jednotlivé součásti zařízení. V zařízeních s více součástmi, jako jsou zařízení s operačním systémem nebo zařízení firmwaru, identity modulů a vlákna modulu, umožňují pro jednotlivé komponenty izolovanou konfiguraci a podmínky.
>

V tomto kurzu se dozvíte:

* Jak vytvořit identitu modulu na portálu.

* Jak použít sadu SDK pro zařízení .NET k aktualizaci nepracovního modulu z vašeho zařízení.

> [!NOTE]
> Informace o sadách SDK Azure IoT, které můžete použít k vytváření aplikací pro spouštění na zařízeních a back-endu vašeho řešení, najdete v tématu sady [SDK služby Azure IoT](iot-hub-devguide-sdks.md).
>

## <a name="prerequisites"></a>Požadavky

* Visual Studio

* Aktivní účet Azure. Pokud účet nemáte, můžete si během několika minut vytvořit [bezplatný účet](https://azure.microsoft.com/pricing/free-trial/).

## <a name="create-a-hub"></a>Vytvoření rozbočovače

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-hub"></a>Registrace nového zařízení v centru

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="create-a-module-identity-in-the-portal"></a>Vytvoření identity modulu na portálu

V rámci jedné identity zařízení můžete vytvořit až 20 identit modulů. K přidání identity použijte následující postup:

1. Pro zařízení, které jste vytvořili v předchozí části, vyberte **Přidat identitu modulu** a vytvořte svou první identitu modulu.

1. Zadejte název *myFirstModule*. Uložte identitu modulu.

    ![Přidat identitu modulu](./media/iot-hub-portal-csharp-module-twin-getstarted/add-module-identity.png)

    Vaše Nová identita modulu se zobrazí v dolní části obrazovky. Pokud si chcete zobrazit podrobnosti identity modulu, vyberte ji.

    ![Viz podrobnosti o identitě modulu.](./media/iot-hub-portal-csharp-module-twin-getstarted/module-identity-details.png)

Uložte **řetězec připojení – primární klíč**. Použijete ho v další části k nastavení modulu na zařízení.

## <a name="update-the-module-twin-using-net-device-sdk"></a>Aktualizace dvojčete modulu pomocí sady SDK pro zařízení .NET

Úspěšně jste vytvořili identitu modulu ve službě IoT Hub. Teď zkuste navázat komunikaci mezi simulovaným zařízením a cloudem. Po vytvoření identity modulu se ve službě IoT Hub implicitně vytvoří dvojče modulu. V této části vytvoříte na simulovaném zařízení konzolovou aplikaci .NET, která aktualizuje hlášené vlastnosti dvojčete modulu.

### <a name="create-a-visual-studio-project"></a>Vytvoření projektu ve Visual Studiu

Chcete-li vytvořit aplikaci, která aktualizuje nedokončené hlášené vlastnosti modulu, postupujte podle následujících kroků:

1. V aplikaci Visual Studio vyberte **vytvořit nový projekt**, pak zvolte **Konzolová aplikace (.NET Framework)** a pak vyberte **Další**.

1. V části **Konfigurovat nový projekt** zadejte *UpdateModuleTwinReportedProperties* jako **název projektu**. Pokračujte výběrem **vytvořit** .

    ![Konfigurace projektu sady Visual Studio](./media/iot-hub-portal-csharp-module-twin-getstarted/configure-twins-project.png)

### <a name="install-the-latest-azure-iot-hub-net-device-sdk"></a>Nainstalovat nejnovější sadu SDK pro zařízení Azure IoT Hub .NET

Identita modulu a modul s dvojitou identitou jsou ve verzi Public Preview. Je k dispozici pouze v sadách IoT Hub předběžných vydání sady SDK pro zařízení. Pokud ho chcete nainstalovat, postupujte následovně:

1. V aplikaci Visual Studio otevřete **nástroje**  >  **Správce balíčků NuGet**  >  **Spravovat balíčky NuGet pro řešení**.

1. Vyberte **Procházet** a pak vyberte **Zahrnout předprodejní verze**. Vyhledejte *Microsoft. Azure. Devices. Client*. Vyberte nejnovější verzi a nainstalujte.

    ![Instalace sady SDK služby Azure IoT Hub .NET Preview](./media/iot-hub-csharp-csharp-module-twin-getstarted/install-sdk.png)

    Teď máte přístup ke všem funkcím modulu.

### <a name="get-your-module-connection-string"></a>Získání připojovacího řetězce modulu

Pro konzolovou aplikaci potřebujete připojovací řetězec modulu. Postupujte takto:

1. Přihlaste se na web [Azure Portal](https://portal.azure.com/).

1. Přejděte do svého centra IoT a vyberte **zařízení IoT**. Otevřete **myFirstDevice** a uvidíte, že **myFirstModule** se úspěšně vytvořil.

1. V části **identity modulu** vyberte **myFirstModule** . V části **Podrobnosti o identitě modulu** zkopírujte **připojovací řetězec (primární klíč)**.

    ![Podrobnosti o modulu na webu Azure Portal](./media/iot-hub-portal-csharp-module-twin-getstarted/module-identity-details.png)

### <a name="create-updatemoduletwinreportedproperties-console-app"></a>Vytvoření aplikace konzoly UpdateModuleTwinReportedProperties

K vytvoření aplikace použijte následující postup:

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

4. Nakonec nahraďte metodu **Main** následujícím kódem:

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
  
  Tuto aplikaci můžete sestavit a spustit pomocí **F5**.

Tento vzorový kód ukazuje, jak načíst dvojče modulu a aktualizovat hlášené vlastnosti pomocí protokolu AMQP. Ve verzi Public Preview podporujeme operace s dvojčetem modulu pouze pomocí protokolu AMQP.

## <a name="next-steps"></a>Další kroky

Chcete-li pokračovat v seznamování se službou IoT Hub a prozkoumat další scénáře IoT, podívejte se na tato témata:

* [Začínáme s identitou modulu a dvojčetem modulu služby IoT Hub pomocí zálohování .NET a zařízení .NET](iot-hub-csharp-csharp-module-twin-getstarted.md)

* [Začínáme se službou IoT Edge](../iot-edge/quickstart-linux.md)