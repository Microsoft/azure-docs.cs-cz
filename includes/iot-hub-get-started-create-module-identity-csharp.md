---
title: zahrnout soubor
description: zahrnout soubor
services: iot-hub
author: chrissie926
ms.service: iot-hub
ms.topic: include
ms.date: 08/07/2019
ms.author: menchi
ms.custom: include file
ms.openlocfilehash: a5c1ddd085ae65b9920d73f50f993f4646785a69
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "68883724"
---
## <a name="create-a-module-identity"></a>Vytvoření identity modulu

V této části vytvoříte aplikaci konzoly .NET, která vytvoří identitu zařízení a identitu modulu v registru identit v centru. Zařízení nebo modul se nemůže připojit k rozbočovači, pokud nemá položku v registru identit. Další informace najdete v části [Registr identit v Příručce pro vývojáře pro službu IoT Hub](../articles/iot-hub/iot-hub-devguide-identity-registry.md).

Když spustíte tuto konzolovou aplikaci, vygeneruje jedinečné ID a klíč zařízení i modulu. Vaše zařízení a modul používají tyto hodnoty k identifikaci při odesílá zprávy zařízení cloud do služby IoT Hub. V ID se rozlišují malá a velká písmena.

1. Otevřete Visual Studio a vyberte **Vytvořit nový projekt**.

1. V **okně Vytvořit nový projekt**vyberte **konzolovou aplikaci (.NET Framework).**

1. Chcete-li otevřít **možnost Konfigurovat nový projekt,** vyberte **možnost Další** . Projekt pojmenujte *CreateIdentities* a řešení pojmenujte *IoTHubGetStarted*. Ujistěte se, že máte .NET Framework verze 4.6.1 nebo novější.

    ![Zadání názvu a architektury pro řešení sady Visual Studio](./media/iot-hub-get-started-create-module-identity-csharp/configure-createidentities-project.png)

1. V sadě Visual Studio otevřete **nástroje, který** > **spravuje balíčky** > **NuGet pro řešení**. Vyberte kartu **Procházet**.

1. Vyhledejte **microsoft.azure.devices**. Vyberte ji a pak vyberte **Instalovat**.

    ![Instalace aktuální verze sady Azure IoT Hub .NET service SDK](./media/iot-hub-get-started-create-module-identity-csharp/install-service-sdk.png)

1. Do horní části souboru **Program.cs** přidejte následující příkazy `using`:

   ```csharp
   using Microsoft.Azure.Devices;
   using Microsoft.Azure.Devices.Common.Exceptions;
   ```

1. Do třídy **Program** přidejte následující pole. Nahraďte hodnotu zástupného symbolu připojovacím řetězcem pro službu IoT Hub, kterou jste vytvořili v předchozí části.

   ```csharp
   const string connectionString = "<replace_with_iothub_connection_string>";
   const string deviceID = "myFirstDevice";
   const string moduleID = "myFirstModule";
   ```

1. Přidejte následující kód do **hlavní** třídy.

   ```csharp
   static void Main(string[] args)
   {
       AddDeviceAsync().Wait();
       AddModuleAsync().Wait();
   }
   ```

1. Do třídy **Program** přidejte následující metody:

    ```csharp
    private static async Task AddDeviceAsync()
    {
       RegistryManager registryManager = 
         RegistryManager.CreateFromConnectionString(connectionString);
       Device device;

       try
       {
           device = await registryManager.AddDeviceAsync(new Device(deviceID));
       }
       catch (DeviceAlreadyExistsException)
        {
            device = await registryManager.GetDeviceAsync(deviceID);
        }

        Console.WriteLine("Generated device key: {0}", 
          device.Authentication.SymmetricKey.PrimaryKey);
    }

    private static async Task AddModuleAsync()
    {
        RegistryManager registryManager = 
          RegistryManager.CreateFromConnectionString(connectionString);
        Module module;

        try
        {
            module = 
              await registryManager.AddModuleAsync(new Module(deviceID, moduleID));
        }
        catch (ModuleAlreadyExistsException)
        {
            module = await registryManager.GetModuleAsync(deviceID, moduleID);
        }

        Console.WriteLine("Generated module key: {0}", module.Authentication.SymmetricKey.PrimaryKey);
    }
    ```

    Metoda `AddDeviceAsync` vytvoří identitu zařízení s ID **myFirstDevice**. Pokud toto ID zařízení již v registru identit existuje, kód jednoduše načte existující informace o zařízení. Aplikace pak zobrazí primární klíč pro danou identitu. Tento klíč se používá v aplikaci simulované zařízení pro připojení k rozbočovači.

    Metoda `AddModuleAsync` vytvoří identitu modulu s ID **myFirstModule** pod zařízením **myFirstDevice**. Pokud toto ID modulu již v registru identit existuje, kód jednoduše načte existující informace o modulu. Aplikace pak zobrazí primární klíč pro danou identitu. Tento klíč použijete v aplikaci simulovaný modul pro připojení k rozbočovači.

   [!INCLUDE [iot-hub-pii-note-naming-device](iot-hub-pii-note-naming-device.md)]

1. Spusťte tuto aplikaci a poznamenejte si klíč zařízení a klíč modulu.

> [!NOTE]
> Registr identit služby IoT Hub ukládá pouze identity zařízení a modulů, které umožňují zabezpečený přístup k rozbočovači. Registr identit ukládá ID zařízení a klíče pro použití jako bezpečnostních pověření. Registr identit také ukládá povolené a zakázané příznaky pro jednotlivá zařízení, pomocí kterých můžete zakázat přístup pro dané zařízení. Pokud vaše aplikace potřebuje ukládat další metadata specifická pro zařízení, měla by používat úložiště specifické pro aplikaci. Pro identity modulů neexistuje žádný příznak povoleno/zakázáno. Další informace najdete v [Příručce pro vývojáře pro službu IoT Hub](../articles/iot-hub/iot-hub-devguide-identity-registry.md).
