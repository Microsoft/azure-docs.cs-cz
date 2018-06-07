---
title: Začínáme se správou zařízení Azure IoT Hub (.NET nebo uzel) | Microsoft Docs
description: Jak používat k zahájení restartu zařízení vzdálenou správou zařízení Azure IoT Hub. Implementace aplikaci ze simulovaného zařízení, která zahrnuje přímá metoda a sady SDK pro .NET k implementaci aplikační služby, která volá metodu přímé služby Azure IoT pomocí zařízení Azure IoT SDK pro Node.js.
author: juanjperez
manager: cberlin
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 10/05/2017
ms.author: juanpere
ms.openlocfilehash: df41d8b88cf630183afc314dafdc1e898f4e02d9
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34632233"
---
# <a name="get-started-with-device-management-netnode"></a>Začínáme se správou zařízení (.NET nebo uzel)

[!INCLUDE [iot-hub-selector-dm-getstarted](../../includes/iot-hub-selector-dm-getstarted.md)]

V tomto kurzu získáte informace o následujících postupech:

* Použití portálu Azure k vytvoření služby IoT Hub a vytvoření identity zařízení ve službě IoT hub.
* Vytvoření aplikace simulovaného zařízení, která obsahuje přímý metodu, která restartování zařízení. Přímé metody jsou vyvolány z cloudu.
* Vytvoření konzolové aplikace .NET, která volá metodu restartování přímé v aplikaci simulovaného zařízení prostřednictvím služby IoT hub.

Na konci tohoto kurzu máte zařízení konzolovou aplikaci Node.js a back-end konzolové aplikace .NET (C#):

**dmpatterns_getstarted_device.js**, který připojí ke službě IoT hub s identitou zařízení vytvořenou dříve, obdrží přímá metoda restartování, simuluje restartu fyzické a sestavy čas posledního restartování.

**TriggerReboot**, která volá metodu přímé v aplikaci simulovaného zařízení, zobrazí odpověď a zobrazí aktualizovaná hlášené vlastnosti.

Pro absolvování tohoto kurzu potřebujete:

* Visual Studio 2015 nebo Visual Studio 2017.
* Verze Node.js 4.0.x nebo novější, <br/>  [Příprava vývojového prostředí] [ lnk-dev-setup] popisuje postup instalace Node.js pro tento návod v systému Windows nebo Linux.
* Aktivní účet Azure. (Pokud účet nemáte, můžete si během několika minut vytvořit [bezplatný účet][lnk-free-trial].)

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

## <a name="trigger-a-remote-reboot-on-the-device-using-a-direct-method"></a>Aktivační události restartu vzdálené v zařízení s přímá metoda
V této části vytvoříte konzolové aplikace .NET (pomocí jazyka C#) iniciované vzdálené restartování na zařízení pomocí přímého metody. Aplikace používá dotazy twin zařízení Pokud chcete zjistit, čas posledního restartování pro toto zařízení.

1. V sadě Visual Studio přidejte k novému řešení klasický desktopový projekt Visual C# pro systém Windows pomocí šablony projektu **Konzolová aplikace (.NET Framework)**. Zkontrolujte, zda máte verzi rozhraní .NET Framework 4.5.1 nebo novější. Název projektu **TriggerReboot**.

    ![Nový klasický desktopový projekt Visual C# pro systém Windows][img-createapp]

2. V Průzkumníku řešení klikněte pravým tlačítkem myši **TriggerReboot** projektu a pak klikněte na **spravovat balíčky NuGet**.
3. V okně **Správce balíčků NuGet** vyberte **Procházet**, vyhledejte **microsoft.azure.devices**, vyberte možnost **Instalovat**, nainstalujte balíček  **Microsoft.Azure.Devices** a přijměte podmínky používání. Tímto postupem se stáhne a nainstaluje [balíček NuGet sady SDK pro službu Azure IoT][lnk-nuget-service-sdk] a jeho závislosti a přidá se na něj odkaz.

    ![Okno Správce balíčků NuGet][img-servicenuget]
4. Do horní části souboru **Program.cs** přidejte následující příkazy `using`:
   
        using Microsoft.Azure.Devices;
        using Microsoft.Azure.Devices.Shared;
        
5. Do třídy **Program** přidejte následující pole. Nahraďte hodnotu zástupného symbolu připojovacím řetězcem IoT Hub pro rozbočovače, který jste vytvořili v části "Vytvoření služby IoT hub." 
   
        static RegistryManager registryManager;
        static string connString = "{iot hub connection string}";
        static ServiceClient client;
        static JobClient jobClient;
        static string targetDevice = "myDeviceId";
        
6. Do třídy **Program** přidejte následující metodu.  Tento kód získá dvojče zařízení pro restartování zařízení a výstupy hlášené vlastnosti.
   
        public static async Task QueryTwinRebootReported()
        {
            Twin twin = await registryManager.GetTwinAsync(targetDevice);
            Console.WriteLine(twin.Properties.Reported.ToJson());
        }
        
7. Do třídy **Program** přidejte následující metodu.  Tento kód zahájí restartování v zařízení s přímá metoda.

        public static async Task StartReboot()
        {
            client = ServiceClient.CreateFromConnectionString(connString);
            CloudToDeviceMethod method = new CloudToDeviceMethod("reboot");
            method.ResponseTimeout = TimeSpan.FromSeconds(30);

            CloudToDeviceMethodResult result = await client.InvokeDeviceMethodAsync(targetDevice, method);

            Console.WriteLine("Invoked firmware update on device.");
        }

7. Nakonec do metody **Main** přidejte následující řádky:
   
        registryManager = RegistryManager.CreateFromConnectionString(connString);
        StartReboot().Wait();
        QueryTwinRebootReported().Wait();
        Console.WriteLine("Press ENTER to exit.");
        Console.ReadLine();
        
8. Sestavte řešení.

## <a name="create-a-simulated-device-app"></a>Vytvoření aplikace simulovaného zařízení
V této části

* Vytvoříte konzolovou aplikaci Node.js, která bude reagovat na přímou metodu volanou cloudem.
* Aktivační události restartu simulovaného zařízení
* Pomocí hlášen vlastnostech povolit dotazy na twin zařízení k identifikaci zařízení, a když trvají restartovat

1. Vytvořit novou prázdnou složku s názvem **manageddevice**.  Ve složce **manageddevice** vytvořte soubor package.json pomocí následujícího příkazu na příkazovém řádku.  Přijměte všechny výchozí hodnoty:
   
    ```
    npm init
    ```
2. Na příkazovém řádku v **manageddevice** složky, spusťte následující příkaz k instalaci **azure-iot-device** balíčku sady SDK zařízení a **azure-iot zařízení mqtt** balíčku:
   
    ```
    npm install azure-iot-device azure-iot-device-mqtt --save
    ```
3. Pomocí textového editoru, vytvořte novou **dmpatterns_getstarted_device.js** v soubor **manageddevice** složky.
4. Přidejte následující příkazy na začátku "vyžadovat" **dmpatterns_getstarted_device.js** souboru:
   
    ```
    'use strict';
   
    var Client = require('azure-iot-device').Client;
    var Protocol = require('azure-iot-device-mqtt').Mqtt;
    ```
5. Přidejte proměnnou **connectionString** a použijte ji k vytvoření instance **klienta**.  Nahraďte připojovacího řetězce připojovacím řetězcem zařízení.  
   
    ```
    var connectionString = 'HostName={youriothostname};DeviceId=myDeviceId;SharedAccessKey={yourdevicekey}';
    var client = Client.fromConnectionString(connectionString, Protocol);
    ```
6. Přidejte následující funkci implementovat metodu přímé na zařízení
   
    ```
    var onReboot = function(request, response) {
   
        // Respond the cloud app for the direct method
        response.send(200, 'Reboot started', function(err) {
            if (!err) {
                console.error('An error occured when sending a method response:\n' + err.toString());
            } else {
                console.log('Response to method \'' + request.methodName + '\' sent successfully.');
            }
        });
   
        // Report the reboot before the physical restart
        var date = new Date();
        var patch = {
            iothubDM : {
                reboot : {
                    lastReboot : date.toISOString(),
                }
            }
        };
   
        // Get device Twin
        client.getTwin(function(err, twin) {
            if (err) {
                console.error('could not get twin');
            } else {
                console.log('twin acquired');
                twin.properties.reported.update(patch, function(err) {
                    if (err) throw err;
                    console.log('Device reboot twin state reported')
                });  
            }
        });
   
        // Add your device's reboot API for physical restart.
        console.log('Rebooting!');
    };
    ```
7. Přidejte následující kód k otevření připojení do služby IoT hub a spustit přímá metoda naslouchací proces:
   
    ```
    client.open(function(err) {
        if (err) {
            console.error('Could not open IotHub client');
        }  else {
            console.log('Client opened.  Waiting for reboot method.');
            client.onDeviceMethod('reboot', onReboot);
        }
    });
    ```
8. Uložte a zavřete **dmpatterns_getstarted_device.js** souboru.
   
> [!NOTE]
> Za účelem zjednodušení tento kurz neimplementuje žádné zásady opakování. V produkčním kódu byte měli implementovat zásady opakování (například exponenciální opakování), jak je navrženo v článku [Řešení přechodných chyb][lnk-transient-faults] na webu MSDN.


## <a name="run-the-apps"></a>Spouštění aplikací
Nyní jste připraveni aplikaci spustit.

1. Na příkazovém řádku v **manageddevice** složky, spusťte následující příkaz, aby začal přijímat přímá metoda restartování.
   
    ```
    node dmpatterns_getstarted_device.js
    ```
2. Spusťte konzolovou aplikaci C# **TriggerReboot**. Klikněte pravým tlačítkem myši **TriggerReboot** projekt, vyberte **ladění**a potom vyberte **spustit novou instanci**.

3. Zobrazí zařízení odpověď na přímá metoda v konzole.

[!INCLUDE [iot-hub-dm-followup](../../includes/iot-hub-dm-followup.md)]

<!-- images and links -->
[img-output]: media/iot-hub-get-started-with-dm/image6.png
[img-dm-ui]: media/iot-hub-get-started-with-dm/dmui.png
[img-servicenuget]: media/iot-hub-csharp-node-device-management-get-started/servicesdknuget.png
[img-createapp]: media/iot-hub-csharp-node-device-management-get-started/createnetapp.png

[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdk-node/blob/master/doc/node-devbox-setup.md

[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[Azure portal]: https://portal.azure.com/
[Using resource groups to manage your Azure resources]: ../azure-portal/resource-group-portal.md
[lnk-dm-github]: https://github.com/Azure/azure-iot-device-management

[lnk-devtwin]: iot-hub-devguide-device-twins.md
[lnk-c2dmethod]: iot-hub-devguide-direct-methods.md
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
[lnk-nuget-service-sdk]: https://www.nuget.org/packages/Microsoft.Azure.Devices/
