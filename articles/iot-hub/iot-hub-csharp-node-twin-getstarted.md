---
title: Začínáme s Azure IoT Hub dvojčata zařízení (.NET nebo uzel) | Microsoft Docs
description: Jak používat dvojčata zařízení Azure IoT Hub přidat značky a pak použijte dotaz služby IoT Hub. Použití zařízení Azure IoT SDK pro Node.js implementovat aplikaci simulovaného zařízení a sady SDK pro .NET k implementaci aplikační služby, které přidá značky a spustí dotaz IoT Hub služby Azure IoT.
author: fsautomata
manager: ''
ms.service: iot-hub
services: iot-hub
ms.devlang: nodejs
ms.topic: conceptual
ms.date: 09/07/2017
ms.author: elioda
ms.openlocfilehash: e2d78fe3f43c493eb126af86580ee473a6aa18c2
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34634416"
---
# <a name="get-started-with-device-twins-netnode"></a>Začínáme s dvojčata zařízení (.NET nebo uzel)
[!INCLUDE [iot-hub-selector-twin-get-started](../../includes/iot-hub-selector-twin-get-started.md)]

Na konci tohoto kurzu budete mít .NET a konzolovou aplikaci softwaru Node.js:

* **AddTagsAndQuery.sln**, aplikace .NET back-end, které přidá značky a dotazuje dvojčata zařízení.
* **TwinSimulatedDevice.js**, aplikace Node.js, která simuluje zařízení, která se připojuje ke službě IoT hub s dříve vytvořenou identitou zařízení a sestav stavu připojení.

> [!NOTE]
> Článek [SDK služby Azure IoT] [ lnk-hub-sdks] poskytuje informace o SDK služby Azure IoT, můžete použít k tvorbě aplikací, zařízení a back-end.
> 
> 

K dokončení tohoto kurzu budete potřebovat následující:

* Visual Studio 2015 nebo Visual Studio 2017.
* Node.js verze 4.0.x nebo novější.
* Aktivní účet Azure. (Pokud účet nemáte, můžete si během několika minut vytvořit [bezplatný účet][lnk-free-trial].)

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

## <a name="create-the-service-app"></a>Vytvořit aplikaci aplikační služby
V této části vytvoříte konzolové aplikace .NET (pomocí jazyka C#), přidá do dvojče zařízení přidružená metadata umístění **myDeviceId**. Následně se dotazuje dvojčata zařízení, které jsou uložené ve službě IoT hub, výběrem zařízení nachází v USA a ty, které hlášené mobilní připojení.

1. V sadě Visual Studio přidejte k stávajícímu řešení klasický desktopový projekt Visual C# pro systém Windows pomocí šablony projektu **Konzolová aplikace**. Název projektu **AddTagsAndQuery**.
   
    ![Nový klasický desktopový projekt Visual C# pro systém Windows][img-createapp]
1. V Průzkumníku řešení klikněte pravým tlačítkem myši **AddTagsAndQuery** projektu a pak klikněte na tlačítko **spravovat balíčky NuGet...** .
1. V **Správce balíčků NuGet** vyberte **Procházet** a vyhledejte **microsoft.azure.devices**. Vyberte **nainstalovat** k instalaci **Microsoft.Azure.Devices** balíček a přijměte podmínky použití. Tímto postupem se stáhne a nainstaluje [balíček NuGet sady SDK pro službu Azure IoT][lnk-nuget-service-sdk] a jeho závislosti a přidá se na něj odkaz.
   
    ![Okno Správce balíčků NuGet][img-servicenuget]
1. Do horní části souboru **Program.cs** přidejte následující příkazy `using`:
   
        using Microsoft.Azure.Devices;
1. Do třídy **Program** přidejte následující pole. Nahraďte hodnotu zástupného symbolu připojovacím řetězcem pro službu IoT Hub, kterou jste vytvořili v předchozí části.
   
        static RegistryManager registryManager;
        static string connectionString = "{iot hub connection string}";
1. Přidejte následující metodu do třídy **Program**:
   
        public static async Task AddTagsAndQuery()
        {
            var twin = await registryManager.GetTwinAsync("myDeviceId");
            var patch =
                @"{
                    tags: {
                        location: {
                            region: 'US',
                            plant: 'Redmond43'
                        }
                    }
                }";
            await registryManager.UpdateTwinAsync(twin.DeviceId, patch, twin.ETag);
   
            var query = registryManager.CreateQuery("SELECT * FROM devices WHERE tags.location.plant = 'Redmond43'", 100);
            var twinsInRedmond43 = await query.GetNextAsTwinAsync();
            Console.WriteLine("Devices in Redmond43: {0}", string.Join(", ", twinsInRedmond43.Select(t => t.DeviceId)));
   
            query = registryManager.CreateQuery("SELECT * FROM devices WHERE tags.location.plant = 'Redmond43' AND properties.reported.connectivity.type = 'cellular'", 100);
            var twinsInRedmond43UsingCellular = await query.GetNextAsTwinAsync();
            Console.WriteLine("Devices in Redmond43 using cellular network: {0}", string.Join(", ", twinsInRedmond43UsingCellular.Select(t => t.DeviceId)));
        }
   
    **RegistryManager** třída poskytuje všechny metody požadované pro interakci s dvojčata zařízení ze služby. Předchozí kód nejprve inicializuje **registryManager** objekt a potom načte dvojče zařízení pro **myDeviceId**a nakonec jeho značky aktualizuje informace o požadované umístění.
   
    Po aktualizaci, se provede dva dotazy: první vybere pouze dvojčata zařízení nachází v zařízení **Redmond43** závodu a druhý zpřesnění dotazu a vyberte pouze zařízení, která jsou také připojené přes mobilní síť.
   
    Všimněte si, že předchozí kód, když vytváří **dotazu** objektu, určuje maximální počet vrácených dokumentů. **Dotazu** objekt obsahuje **HasMoreResults** vlastnost typu boolean, který můžete použít k vyvolání **GetNextAsTwinAsync** metody několikrát načíst všechny výsledky. Volána metoda **GetNextAsJson** je k dispozici pro výsledky, které není dvojčata zařízení, například výsledky dotazů agregace.
1. Nakonec do metody **Main** přidejte následující řádky:
   
        registryManager = RegistryManager.CreateFromConnectionString(connectionString);
        AddTagsAndQuery().Wait();
        Console.WriteLine("Press Enter to exit.");
        Console.ReadLine();

1. V Průzkumníku řešení otevřete **nastavit projekty po spuštění...**  a zajistěte, aby **akce** pro **AddTagsAndQuery** je projekt **spustit**. Sestavte řešení.
1. Kliknutím pravým tlačítkem na spuštění této aplikace **AddTagsAndQuery** projekt a výběrem **ladění**, za nímž následují **spustit novou instanci**. Měli byste vidět jedno zařízení ve výsledcích pro dotaz s dotazem pro všechna zařízení umístěné v **Redmond43** a jeden pro dotaz, který omezuje výsledky na zařízení, která používají mobilní síti.
   
    ![Výsledky dotazu v okně][img-addtagapp]

V další části můžete vytvořit aplikaci zařízení, která hlásí informace o připojení a změní výsledek dotazu v předchozí části.

## <a name="create-the-device-app"></a>Vytvoření aplikace pro zařízení
V této části vytvoříte konzolovou aplikaci softwaru Node.js, která se připojuje k vaší hub jako **myDeviceId**a pak aktualizuje jeho hlášené vlastnosti tak, aby obsahovala informace, že je připojený pomocí mobilní síti.

1. Vytvořit novou prázdnou složku s názvem **reportconnectivity**. V **reportconnectivity** složky, vytvořte nový soubor package.json pomocí následujícího příkazu na příkazovém řádku. Přijměte všechny výchozí hodnoty.
   
    ```
    npm init
    ```
1. Na příkazovém řádku v **reportconnectivity** složky, spusťte následující příkaz k instalaci **azure-iot-device**, a **azure-iot zařízení mqtt** balíčku:
   
    ```
    npm install azure-iot-device azure-iot-device-mqtt --save
    ```
1. Pomocí textového editoru, vytvořte novou **ReportConnectivity.js** v soubor **reportconnectivity** složky.
1. Přidejte následující kód, který **ReportConnectivity.js** souboru a nahraďte zástupný symbol pro řetězec připojení zařízení s jedním jste zkopírovali, když jste vytvořili **myDeviceId** identitu zařízení:
   
        'use strict';
        var Client = require('azure-iot-device').Client;
        var Protocol = require('azure-iot-device-mqtt').Mqtt;
   
        var connectionString = '{device connection string}';
        var client = Client.fromConnectionString(connectionString, Protocol);
   
        client.open(function(err) {
        if (err) {
            console.error('could not open IotHub client');
        }  else {
            console.log('client opened');
   
            client.getTwin(function(err, twin) {
            if (err) {
                console.error('could not get twin');
            } else {
                var patch = {
                    connectivity: {
                        type: 'cellular'
                    }
                };
   
                twin.properties.reported.update(patch, function(err) {
                    if (err) {
                        console.error('could not update twin');
                    } else {
                        console.log('twin state reported');
                        process.exit();
                    }
                });
            }
            });
        }
        });
   
    **Klienta** objekt poskytuje všechny metody vyžadovat interakci s dvojčata zařízení ze zařízení. Předchozí kód, jakmile ji inicializuje **klienta** objektu, načte dvojče zařízení pro **myDeviceId** a aktualizuje jeho hlášené vlastnost s informacemi o připojení.
1. Spuštění aplikace zařízení
   
        node ReportConnectivity.js
   
    Měli byste vidět zprávu `twin state reported`.
1. Teď, když je zařízení hlášené jeho informace o připojení k se má zobrazit v obou dotazy. Spustit .NET **AddTagsAndQuery** aplikaci znovu spustit dotazy. Tentokrát **myDeviceId** by se měla objevit v obou výsledky dotazu.
   
    ![][img-addtagapp2]

## <a name="next-steps"></a>Další postup
V tomto kurzu jste nakonfigurovali novou službu IoT Hub na webu Azure Portal a potom jste vytvořili identitu zařízení v registru identit ve službě IoT Hub. Přidat zařízení metadat jako značky z back-end aplikace a zapsal aplikace simulovaného zařízení do sestavy informace o připojení k zařízení v dvojče zařízení. Také jste zjistili, jak dotazovat tyto informace pomocí dotazu jazyka SQL jako IoT Hub.

Použijte v následujících zdrojích informací další postup:

* odesílat telemetrická data ze zařízení pomocí [Začínáme se službou IoT Hub] [ lnk-iothub-getstarted] kurzu
* Konfigurace zařízení pomocí dvojče zařízení požadované vlastnosti s [použití požadovaného vlastnosti pro konfiguraci zařízení] [ lnk-twin-how-to-configure] kurzu
* s kontroly nad zařízeními interaktivně (například zapnutí ventilátor z aplikace řízené uživatele) [použít přímé metody] [ lnk-methods-tutorial] kurzu.

<!-- images -->
[img-servicenuget]: media/iot-hub-csharp-node-twin-getstarted/servicesdknuget.png
[img-createapp]: media/iot-hub-csharp-node-twin-getstarted/createnetapp.png
[img-addtagapp]: media/iot-hub-csharp-node-twin-getstarted/addtagapp.png
[img-addtagapp2]: media/iot-hub-csharp-node-twin-getstarted/addtagapp2.png

<!-- links -->
[lnk-hub-sdks]: iot-hub-devguide-sdks.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-nuget-service-sdk]: https://www.nuget.org/packages/Microsoft.Azure.Devices/

[lnk-d2c]: iot-hub-devguide-messaging.md#device-to-cloud-messages
[lnk-methods]: iot-hub-devguide-direct-methods.md
[lnk-twins]: iot-hub-devguide-device-twins.md
[lnk-query]: iot-hub-devguide-query-language.md
[lnk-identity]: iot-hub-devguide-identity-registry.md

[lnk-iothub-getstarted]: iot-hub-node-node-getstarted.md
[lnk-methods-tutorial]: iot-hub-node-node-direct-methods.md
[lnk-twin-how-to-configure]: iot-hub-csharp-node-twin-how-to-configure.md

[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdk-node/blob/master/doc/node-devbox-setup.md

