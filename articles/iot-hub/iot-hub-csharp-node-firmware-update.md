---
title: Aktualizace firmwaru zařízení s Azure IoT Hub (.NET nebo uzel) | Microsoft Docs
description: Jak používat správu zařízení v Azure IoT Hub zahájíte aktualizaci firmwaru zařízení. Použití zařízení Azure IoT SDK pro Node.js pro implementaci aplikace simulovaného zařízení a sady SDK pro .NET k implementaci služby aplikaci, která spustí aktualizaci firmwaru služby Azure IoT.
services: iot-hub
documentationcenter: .net
author: juanjperez
manager: timlt
editor: ''
ms.assetid: 70b84258-bc9f-43b1-b7cf-de1bb715f2cf
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/30/2017
ms.author: juanpere
ms.openlocfilehash: e54ba3e3015c7175814c7f4e3330ad2de3819136
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2018
---
# <a name="use-device-management-to-initiate-a-device-firmware-update-netnode"></a>Použití správy zařízení za účelem zahájení aktualizaci firmwaru zařízení (.NET nebo uzel)
[!INCLUDE [iot-hub-selector-firmware-update](../../includes/iot-hub-selector-firmware-update.md)]

V [Začínáme se správou zařízení] [ lnk-dm-getstarted] kurz, jste viděli, jak používat [dvojče zařízení] [ lnk-devtwin] a [přímé metody ] [ lnk-c2dmethod] primitiv vzdáleně restartování zařízení. Tento kurz používá stejné primitiv IoT Hub a ukazuje, jak provést aktualizaci firmwaru simulované začátku do konce.  Tento vzor slouží k implementaci firmwaru aktualizace pro [malin platformy zařízení implementace ukázka][lnk-rpi-implementation].

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

V tomto kurzu získáte informace o následujících postupech:

* Vytvoření konzolové aplikace .NET, která volá metodu firmwareUpdate přímé v aplikaci simulovaného zařízení prostřednictvím služby IoT hub.
* Vytvoření aplikace simulovaného zařízení, která implementuje **firmwareUpdate** přímá metoda. Tato metoda inicializuje více fáze procesu, který čeká na stažení bitové kopie firmwaru, stáhne bitovou kopii firmware a nakonec platí bitovou kopii firmwaru. Během aktualizace v každé fázi používá zařízení hlášené vlastnosti hlásit průběh.

Na konci tohoto kurzu máte zařízení konzolovou aplikaci Node.js a back-end konzolové aplikace .NET (C#):

**dmpatterns_fwupdate_service.js**, která volá metodu přímé v aplikaci simulovaného zařízení, zobrazí odpověď a pravidelně (každých 500ms) zobrazí aktualizovaná hlášené vlastnosti.

**TriggerFWUpdate**, který se připojí ke službě IoT hub s identitou zařízení vytvořenou dříve, dostane přímá metoda firmwareUpdate, spustí prostřednictvím více stavu procesu k simulaci, včetně aktualizace firmwaru: Čekání na stažení bitové kopie stahování novou bitovou kopii a nakonec použitím bitové kopie.

Pro absolvování tohoto kurzu potřebujete:

* Visual Studio 2015 nebo Visual Studio 2017.
* Verze Node.js 4.0.x nebo novější, <br/>  [Příprava vývojového prostředí] [ lnk-dev-setup] popisuje postup instalace Node.js pro tento návod v systému Windows nebo Linux.
* Aktivní účet Azure. (Pokud účet nemáte, můžete si během několika minut vytvořit [bezplatný účet][lnk-free-trial].)

Postupujte podle [Začínáme se správou zařízení](iot-hub-csharp-node-device-management-get-started.md) článek k vytvoření služby IoT hub a získat připojovací řetězec služby IoT Hub.

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

## <a name="trigger-a-remote-firmware-update-on-the-device-using-a-direct-method"></a>Spustit aktualizaci vzdálené firmwaru v zařízení s přímá metoda
V této části vytvoříte konzolové aplikace .NET (pomocí jazyka C#), zahájí aktualizaci vzdálené firmwaru v zařízení. Přímá metoda používá k zahájení aktualizace a aplikace používá zařízení twin dotazy a pravidelně získat stav aktualizace active firmware.

1. V sadě Visual Studio přidejte k stávajícímu řešení klasický desktopový projekt Visual C# pro systém Windows pomocí šablony projektu **Konzolová aplikace**. Název projektu **TriggerFWUpdate**.

    ![Nový klasický desktopový projekt Visual C# pro systém Windows][img-createapp]

1. V Průzkumníku řešení klikněte pravým tlačítkem myši **TriggerFWUpdate** projektu a pak klikněte na tlačítko **spravovat balíčky NuGet...** .
1. V okně **Správce balíčků NuGet** vyberte **Procházet**, vyhledejte **microsoft.azure.devices**, vyberte možnost **Instalovat**, nainstalujte balíček  **Microsoft.Azure.Devices** a přijměte podmínky používání. Tímto postupem se stáhne a nainstaluje [balíček NuGet sady SDK pro službu Azure IoT][lnk-nuget-service-sdk] a jeho závislosti a přidá se na něj odkaz.

    ![Okno Správce balíčků NuGet][img-servicenuget]
1. Do horní části souboru **Program.cs** přidejte následující příkazy `using`:
   
        using Microsoft.Azure.Devices;
        using Microsoft.Azure.Devices.Shared;
        
1. Do třídy **Program** přidejte následující pole. Více zástupné hodnoty nahraďte připojovací řetězec služby IoT Hub pro rozbočovače, který jste vytvořili v předchozí části a Id vašeho zařízení.
   
        static RegistryManager registryManager;
        static string connString = "{iot hub connection string}";
        static ServiceClient client;
        static JobClient jobClient;
        static string targetDevice = "{deviceIdForTargetDevice}";
        
1. Přidejte následující metodu do třídy **Program**:
   
        public static async Task QueryTwinFWUpdateReported()
        {
            Twin twin = await registryManager.GetTwinAsync(targetDevice);
            Console.WriteLine(twin.Properties.Reported.ToJson());
        }
        
1. Přidejte následující metodu do třídy **Program**:

        public static async Task StartFirmwareUpdate()
        {
            client = ServiceClient.CreateFromConnectionString(connString);
            CloudToDeviceMethod method = new CloudToDeviceMethod("firmwareUpdate");
            method.ResponseTimeout = TimeSpan.FromSeconds(30);
            method.SetPayloadJson(
                @"{
                    fwPackageUri : 'https://someurl'
                }");

            CloudToDeviceMethodResult result = await client.InvokeDeviceMethodAsync(targetDevice, method);

            Console.WriteLine("Invoked firmware update on device.");
        }

1. Nakonec do metody **Main** přidejte následující řádky:
   
        registryManager = RegistryManager.CreateFromConnectionString(connString);
        StartFirmwareUpdate().Wait();
        QueryTwinFWUpdateReported().Wait();
        Console.WriteLine("Press ENTER to exit.");
        Console.ReadLine();
        
1. V Průzkumníku řešení otevřete **nastavit projekty po spuštění...**  a zajistěte, aby **akce** pro **TriggerFWUpdate** je projekt **spustit**.

1. Sestavte řešení.

[!INCLUDE [iot-hub-device-firmware-update](../../includes/iot-hub-device-firmware-update.md)]

## <a name="run-the-apps"></a>Spouštění aplikací
Nyní jste připraveni aplikaci spustit.

1. Na příkazovém řádku v **manageddevice** složky, spusťte následující příkaz, aby začal přijímat přímá metoda restartování.
   
    ```
    node dmpatterns_fwupdate_device.js
    ```
2. V sadě Visual Studio, klikněte pravým tlačítkem na **TriggerFWUpdate** projekt, vyberte **ladění** a **spustit novou instanci**.

3. Zobrazí zařízení odpověď na přímá metoda v konzole.

    ![Firmware aktualizace byla úspěšná.][img-fwupdate]

## <a name="next-steps"></a>Další postup
V tomto kurzu přímá metoda používá k aktivaci aktualizace vzdálené firmwaru v zařízení a umožňuje sledovat průběh aktualizace firmwaru hlášené vlastnosti.

Zjistěte, jak rozšířit vaše IoT řešení a plán metoda volá na několika zařízeních, najdete v článku [plán a všesměrového vysílání úlohy] [ lnk-tutorial-jobs] kurzu.

<!-- images -->
[img-servicenuget]: media/iot-hub-csharp-node-firmware-update/servicesdknuget.png
[img-createapp]: media/iot-hub-csharp-node-firmware-update/createnetapp.png
[img-fwupdate]: media/iot-hub-csharp-node-firmware-update/fwupdated.png

[lnk-devtwin]: iot-hub-devguide-device-twins.md
[lnk-c2dmethod]: iot-hub-devguide-direct-methods.md
[lnk-dm-getstarted]: iot-hub-node-node-device-management-get-started.md
[lnk-tutorial-jobs]: iot-hub-node-node-schedule-jobs.md

[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdk-node/blob/master/doc/node-devbox-setup.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
[lnk-rpi-implementation]: https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_client/samples/iothub_client_sample_mqtt_dm/pi_device
[lnk-nuget-service-sdk]: https://www.nuget.org/packages/Microsoft.Azure.Devices/