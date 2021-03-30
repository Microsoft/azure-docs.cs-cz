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
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "68883724"
---
## <a name="create-a-module-identity"></a>Vytvoření identity modulu

V této části vytvoříte konzolovou aplikaci .NET, která vytvoří identitu zařízení a identitu modulu v registru identit ve vašem centru. Zařízení nebo modul se nemůže připojit k rozbočovači, pokud neobsahuje záznam v registru identit. Další informace najdete v části [Registr identit v Příručce pro vývojáře pro službu IoT Hub](../articles/iot-hub/iot-hub-devguide-identity-registry.md).

Když spustíte tuto konzolovou aplikaci, vygeneruje jedinečné ID a klíč zařízení i modulu. Zařízení a modul používají tyto hodnoty k tomu, aby je identifikovali při odesílání zpráv ze zařízení do cloudu do IoT Hub. V ID se rozlišují malá a velká písmena.

1. Otevřete Visual Studio a vyberte **vytvořit nový projekt**.

1. V v **vytvořit nový projekt** vyberte **Konzolová aplikace (.NET Framework)**.

1. Výběrem **Další** otevřete položku **Konfigurovat nový projekt**. Projekt pojmenujte *CreateIdentities* a řešení pojmenujte *IoTHubGetStarted*. Ujistěte se, že máte .NET Framework verze 4.6.1 nebo novější.

    ![Zadejte název a rozhraní pro řešení sady Visual Studio.](./media/iot-hub-get-started-create-module-identity-csharp/configure-createidentities-project.png)

1. V aplikaci Visual Studio otevřete **nástroje**  >  **Správce balíčků NuGet**  >  **Spravovat balíčky NuGet pro řešení**. Vyberte kartu **Procházet**.

1. Vyhledejte **Microsoft. Azure. Devices**. Vyberte ho a pak vyberte **nainstalovat**.

    ![Instalace aktuální verze sady Azure IoT Hub .NET Service SDK](./media/iot-hub-get-started-create-module-identity-csharp/install-service-sdk.png)

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

1. Do **Hlavní** třídy přidejte následující kód.

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

    `AddDeviceAsync`Metoda vytvoří identitu zařízení s ID **myFirstDevice**. Pokud toto ID zařízení již v registru identit existuje, kód jednoduše načte informace o stávajícím zařízení. Aplikace pak zobrazí primární klíč pro danou identitu. Tento klíč použijete v aplikaci simulovaného zařízení pro připojení k vašemu centru.

    `AddModuleAsync`Metoda vytvoří identitu modulu s ID **MyFirstModule** v **myFirstDevice** zařízení. Pokud toto ID modulu již v registru identit existuje, kód jednoduše načte informace o stávajícím modulu. Aplikace pak zobrazí primární klíč pro danou identitu. Tento klíč použijete v aplikaci simulovaného modulu pro připojení k vašemu centru.

   [!INCLUDE [iot-hub-pii-note-naming-device](iot-hub-pii-note-naming-device.md)]

1. Spusťte tuto aplikaci a poznamenejte si klíč zařízení a klíč modulu.

> [!NOTE]
> Registr identit IoT Hub ukládá pouze identity zařízení a modulů, aby bylo možné povolit zabezpečený přístup k rozbočovači. Registr identit ukládá ID zařízení a klíče pro použití jako bezpečnostních pověření. Registr identit také ukládá povolené a zakázané příznaky pro jednotlivá zařízení, pomocí kterých můžete zakázat přístup pro dané zařízení. Pokud vaše aplikace potřebuje Uložit jiná metadata specifická pro zařízení, měla by používat úložiště pro konkrétní aplikaci. Pro identity modulů neexistuje žádný příznak povoleno/zakázáno. Další informace najdete v [Příručce pro vývojáře pro službu IoT Hub](../articles/iot-hub/iot-hub-devguide-identity-registry.md).
