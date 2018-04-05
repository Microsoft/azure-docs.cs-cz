---
title: Plánování úloh službou Azure IoT Hub (.NET nebo uzel) | Microsoft Docs
description: Popisuje, jak naplánovat úlohu služby Azure IoT Hub pro vyvolání přímé metody na několika zařízeních. Zařízení Azure IoT SDK pro Node.js použijete k implementaci aplikace simulovaného zařízení a sady SDK pro .NET k implementaci aplikace service chcete spustit úlohu služby Azure IoT.
services: iot-hub
documentationcenter: .net
author: juanjperez
manager: timlt
editor: ''
ms.assetid: 2233356e-b005-4765-ae41-3a4872bda943
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/10/2017
ms.author: juanpere
ms.openlocfilehash: 360daf918051ce901a81f96d1873dc90af238e19
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2018
---
# <a name="schedule-and-broadcast-jobs-netnodejs"></a>Úlohy plán a všesměrového vysílání (.NET/Node.js)

[!INCLUDE [iot-hub-selector-schedule-jobs](../../includes/iot-hub-selector-schedule-jobs.md)]

K plánování a sledování úloh, které aktualizují miliony zařízení pomocí služby Azure IoT Hub. Pomocí úloh:

* Aktualizace požadovaných vlastností
* Aktualizace značky
* Vyvolání metody přímé

Úloha zabalí jednu z těchto akcí a sleduje provádění na sadu zařízení, která je definována dotazem twin zařízení. Například můžete použít back-end aplikačním úlohu k vyvolání přímé metodu na 10 000 zařízení, která restartování zařízení. Zadejte sadu zařízení s dotazem twin zařízení a úlohu naplánovat na spuštění v budoucnosti. Průběh úlohy sleduje jako každé zařízení obdrží a provést přímý metodu restartování.

Další informace o každém z těchto funkcí najdete v tématu:

* Dvojče zařízení a vlastností: [začít pracovat s dvojčata zařízení] [ lnk-get-started-twin] a [kurz: použití dvojici vlastností zařízení][lnk-twin-props]
* Přímé metody: [Příručka vývojáře pro službu IoT Hub - přímé metody] [ lnk-dev-methods] a [kurz: použijte přímý metody][lnk-c2d-methods]

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

V tomto kurzu získáte informace o následujících postupech:

* Vytvoření aplikace zařízení, která implementuje přímé metodu s názvem **lockDoor** , lze volat back-end aplikace. Aplikace zařízení také přijme požadovanou vlastnost změny z back-end aplikace.
* Vytvořit aplikaci back-end, která vytvoří úlohu k volání **lockDoor** přímá metoda na několika zařízeních. Jiná úloha odešle aktualizace požadované vlastností do více zařízení.

Na konci tohoto kurzu máte zařízení konzolovou aplikaci Node.js a back-end konzolové aplikace .NET (C#):

**simDevice.js** která se připojuje ke službě IoT hub, implementuje **lockDoor** přímá metoda a zpracovává potřeby změny vlastností.

**ScheduleJob** používající úlohy k volání **lockDoor** přímá metoda a aktualizovat vlastnosti twin požadovaného zařízení na několika zařízeních.

Pro absolvování tohoto kurzu potřebujete:

* Visual Studio 2015 nebo Visual Studio 2017.
* Node.js verze 4.0.x nebo novější. Článek [Příprava vývojového prostředí] [ lnk-dev-setup] popisuje postup instalace Node.js pro tento návod v systému Windows nebo Linux.
* Aktivní účet Azure. Pokud účet nemáte, můžete si během několika minut vytvořit [bezplatný účet][lnk-free-trial].

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

## <a name="schedule-jobs-for-calling-a-direct-method-and-sending-device-twin-updates"></a>Plán úlohy pro volání přímá metoda a odesílání twin aktualizace zařízení

V této části vytvoříte konzolové aplikace .NET (pomocí jazyka C#) používající úlohy k volání **lockDoor** přímá metoda a odesílání aktualizací požadovanou vlastnost na několika zařízeních.

1. V sadě Visual Studio přidejte k stávajícímu řešení klasický desktopový projekt Visual C# pro systém Windows pomocí šablony projektu **Konzolová aplikace**. Název projektu **ScheduleJob**.

    ![Nový klasický desktopový projekt Visual C# pro systém Windows][img-createapp]

1. V Průzkumníku řešení klikněte pravým tlačítkem myši **ScheduleJob** projektu a pak klikněte na tlačítko **spravovat balíčky NuGet...** .
1. V okně **Správce balíčků NuGet** vyberte **Procházet**, vyhledejte **microsoft.azure.devices**, vyberte možnost **Instalovat**, nainstalujte balíček  **Microsoft.Azure.Devices** a přijměte podmínky používání. Tento krok stáhne, nainstaluje a přidá odkaz na [sady SDK služby Azure IoT] [ lnk-nuget-service-sdk] NuGet balíček a jeho závislé součásti.

    ![Okno Správce balíčků NuGet][img-servicenuget]
1. Do horní části souboru **Program.cs** přidejte následující příkazy `using`:
    
    ```csharp
    using Microsoft.Azure.Devices;
    using Microsoft.Azure.Devices.Shared;
    ```

1. Přidejte následující `using` příkaz, pokud ještě není přítomný v příkazech výchozí.

    ```csharp
    using System.Threading;
    using System.Threading.Tasks;
    ```

1. Do třídy **Program** přidejte následující pole. Nahraďte zástupný symbol s připojovací řetězec služby IoT Hub pro rozbočovače, který jste vytvořili v předchozí části.

    ```csharp
    static string connString = "{iot hub connection string}";
    static ServiceClient client;
    static JobClient jobClient;
    ```

1. Přidejte následující metodu do třídy **Program**:

    ```csharp
    public static async Task MonitorJob(string jobId)
    {
        JobResponse result;
        do
        {
            result = await jobClient.GetJobAsync(jobId);
            Console.WriteLine("Job Status : " + result.Status.ToString());
            Thread.Sleep(2000);
        } while ((result.Status != JobStatus.Completed) && (result.Status != JobStatus.Failed));
    }
    ```

1. Přidejte následující metodu do třídy **Program**:

    ```csharp
    public static async Task StartMethodJob(string jobId)
    {
        CloudToDeviceMethod directMethod = new CloudToDeviceMethod("lockDoor", TimeSpan.FromSeconds(5), TimeSpan.FromSeconds(5));

        JobResponse result = await jobClient.ScheduleDeviceMethodAsync(jobId,
            "deviceId='myDeviceId'",
            directMethod,
            DateTime.Now,
            (long)TimeSpan.FromMinutes(2).TotalSeconds);

        Console.WriteLine("Started Method Job");
    }
    ```

1. Přidejte následující metodu do třídy **Program**:

    ```csharp
    public static async Task StartTwinUpdateJob(string jobId)
    {
        var twin = new Twin();
        twin.Properties.Desired["Building"] = "43";
        twin.Properties.Desired["Floor"] = "3";
        twin.ETag = "*";

        JobResponse result = await jobClient.ScheduleTwinUpdateAsync(jobId,
            "deviceId='myDeviceId'",
            twin,
            DateTime.Now,
            (long)TimeSpan.FromMinutes(2).TotalSeconds);

        Console.WriteLine("Started Twin Update Job");
    }
    ```

1. Nakonec do metody **Main** přidejte následující řádky:

    ```csharp
    jobClient = JobClient.CreateFromConnectionString(connString);

    string methodJobId = Guid.NewGuid().ToString();

    StartMethodJob(methodJobId);
    MonitorJob(methodJobId).Wait();
    Console.WriteLine("Press ENTER to run the next job.");
    Console.ReadLine();

    string twinUpdateJobId = Guid.NewGuid().ToString();

    StartTwinUpdateJob(twinUpdateJobId);
    MonitorJob(twinUpdateJobId).Wait();
    Console.WriteLine("Press ENTER to exit.");
    Console.ReadLine();
    ```

1. V Průzkumníku řešení otevřete **nastavit projekty po spuštění...**  a zajistěte, aby **akce** pro **ScheduleJob** je projekt **spustit**. Sestavte řešení.

## <a name="create-a-simulated-device-app"></a>Vytvoření aplikace simulovaného zařízení

V této části vytvoříte konzolovou aplikaci softwaru Node.js, která reaguje na přímé metodu s názvem cloudem, který aktivuje restartu simulované zařízení a používá hlášené vlastnosti povolit zařízení twin dotazy k identifikaci zařízení, a při jejich poslední restartován.

1. Vytvořit novou prázdnou složku s názvem **simDevice**.  V **simDevice** složky, vytvořte soubor package.json pomocí následujícího příkazu na příkazovém řádku.  Přijměte všechny výchozí hodnoty:

    ```cmd/sh
    npm init
    ```

1. Na příkazovém řádku v **simDevice** složky, spusťte následující příkaz k instalaci **azure-iot-device** a **azure-iot zařízení mqtt** balíčky:

    ```cmd/sh
    npm install azure-iot-device azure-iot-device-mqtt --save
    ```

1. Pomocí textového editoru, vytvořte novou **simDevice.js** v soubor **simDevice** složky.

1. Přidejte následující příkazy na začátku "vyžadovat" **simDevice.js** souboru:

    ```nodejs
    'use strict';
   
    var Client = require('azure-iot-device').Client;
    var Protocol = require('azure-iot-device-mqtt').Mqtt;
    ```

1. Přidejte proměnnou **connectionString** a použijte ji k vytvoření instance **klienta**. Ujistěte se, zda jste nahradili zástupné symboly hodnotami, které jsou vhodné pro vaši instalaci.

    ```nodejs
    var connectionString = 'HostName={youriothostname};DeviceId={yourdeviceid};SharedAccessKey={yourdevicekey}';
    var client = Client.fromConnectionString(connectionString, Protocol);
    ```

1. Přidejte následující funkci pro zpracování **lockDoor** metoda.

    ```nodejs
    var onLockDoor = function(request, response) {
   
        // Respond the cloud app for the direct method
        response.send(200, function(err) {
            if (!err) {
                console.error('An error occured when sending a method response:\n' + err.toString());
            } else {
                console.log('Response to method \'' + request.methodName + '\' sent successfully.');
            }
        });
   
        console.log('Locking Door!');
    };
    ```

1. Přidejte následující kód pro obslužnou rutinu pro registraci **lockDoor** metoda.

    ```nodejs
    client.open(function(err) {
        if (err) {
            console.error('Could not connect to IotHub client.');
        }  else {
            console.log('Client connected to IoT Hub.  Waiting for lockDoor direct method.');
            client.onDeviceMethod('lockDoor', onLockDoor);
        }
    });
    ```

1. Uložte a zavřete **simDevice.js** souboru.

> [!NOTE]
> Za účelem zjednodušení tento kurz neimplementuje žádné zásady opakování. V produkčním kódu byte měli implementovat zásady opakování (například exponenciální opakování), jak je navrženo v článku [Řešení přechodných chyb][lnk-transient-faults] na webu MSDN.

## <a name="run-the-apps"></a>Spouštění aplikací

Nyní jste připraveni aplikaci spustit.

1. Na příkazovém řádku v **simDevice** složky, spusťte následující příkaz, aby začal přijímat přímá metoda restartování.

    ```cmd/sh
    node simDevice.js
    ```

1. Spusťte konzolovou aplikaci C# **ScheduleJob** kliknutím pravým tlačítkem na **ScheduleJob** projekt, pak výběrem **ladění** a **spustit novou instanci**.

1. Zobrazí se výstup ze zařízení i back-end aplikace.

    ![Spuštění aplikace při plánování úloh][img-schedulejobs]

## <a name="next-steps"></a>Další postup

V tomto kurzu jste použili úlohu při plánování přímá metoda zařízení a aktualizaci vlastností dvojče zařízení.

Chcete-li pokračovat, Začínáme se službou IoT Hub a vzory správy zařízení, jako je vzdálené přes aktualizaci firmwaru letecké, přečtěte si [kurz: jak to provést aktualizaci firmwaru][lnk-fwupdate].

Další informace o nasazení AI do hraniční zařízení s Azure IoT hranou najdete v tématu [Začínáme se službou IoT Edge][lnk-iot-edge].

<!-- images -->
[img-servicenuget]: media/iot-hub-csharp-node-schedule-jobs/servicesdknuget.png
[img-createapp]: media/iot-hub-csharp-node-schedule-jobs/createnetapp.png
[img-schedulejobs]: media/iot-hub-csharp-node-schedule-jobs/schedulejobs.png

[lnk-get-started-twin]: iot-hub-node-node-twin-getstarted.md
[lnk-twin-props]: iot-hub-node-node-twin-how-to-configure.md
[lnk-c2d-methods]: iot-hub-node-node-direct-methods.md
[lnk-dev-methods]: iot-hub-devguide-direct-methods.md
[lnk-fwupdate]: iot-hub-node-node-firmware-update.md
[lnk-iot-edge]: ../iot-edge/tutorial-simulate-device-linux.md
[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdk-node/blob/master/doc/node-devbox-setup.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
[lnk-nuget-service-sdk]: https://www.nuget.org/packages/Microsoft.Azure.Devices/
