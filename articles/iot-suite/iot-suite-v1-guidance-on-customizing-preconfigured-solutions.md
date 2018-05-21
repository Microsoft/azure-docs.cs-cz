---
title: Přizpůsobení předkonfigurovaných řešení | Microsoft Docs
description: Poskytuje pokyny o tom, jak přizpůsobit Azure IoT Suite předkonfigurované řešení.
services: ''
suite: iot-suite
documentationcenter: .net
author: dominicbetts
manager: timlt
editor: ''
ms.assetid: 4653ae53-4110-4a10-bd6c-7dc034c293a8
ms.service: iot-suite
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/02/2017
ms.author: corywink
ms.openlocfilehash: cb5955111cb3954f71f11602042b5153ccee3473
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/20/2018
---
# <a name="customize-a-preconfigured-solution"></a>Přizpůsobení předkonfigurovaného řešení

Předkonfigurovaná řešení Azure IoT Suite součástí Ukázka služby v rámci sady společně k poskytování-komplexní řešení. Z tohoto bodu počáteční jsou různých místech, ve kterých můžete rozšířit a přizpůsobit řešení pro konkrétní scénáře. Následující části popisují tyto společné body přizpůsobení.

## <a name="find-the-source-code"></a>Vyhledání zdrojového kódu

Zdrojový kód pro předkonfigurované řešení je k dispozici na Githubu v následující úložiště:

* Vzdálené monitorování: [https://www.github.com/Azure/azure-iot-remote-monitoring](https://github.com/Azure/azure-iot-remote-monitoring)
* Prediktivní údržby: [https://github.com/Azure/azure-iot-predictive-maintenance](https://github.com/Azure/azure-iot-predictive-maintenance)
* Objekt pro vytváření připojené: [https://github.com/Azure/azure-iot-connected-factory](https://github.com/Azure/azure-iot-connected-factory)

Zdrojový kód pro předkonfigurované řešení je určen k předvedení vzory a postupy, které slouží k implementaci začátku do konce funkcí řešení IoT pomocí sady Azure IoT Suite. Můžete najít další informace o tom, jak vytvořit a nasadit řešení v úložišť GitHub.

## <a name="change-the-preconfigured-rules"></a>Změnit předkonfigurovaných pravidel

Řešení vzdáleného monitorování obsahuje tři [Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/) úlohy pro zpracování informací o zařízení, telemetrie a logiku pravidla v řešení.

Jsou tři stream analytics úloh a jejich syntaxi jsou popsané v podrobně [vzdálené monitorování návod pro předkonfigurované řešení](iot-suite-v1-remote-monitoring-sample-walkthrough.md). 

Můžete upravit tyto úlohy přímo ke změně logiku, nebo přidejte logiku, které jsou specifické pro váš scénář. Úlohy služby Stream Analytics můžete najít následujícím způsobem:

1. Přejděte na [portál Azure](https://portal.azure.com).
2. Přejděte do skupiny prostředků se stejným názvem jako řešení IoT. 
3. Vyberte úlohu Azure Stream Analytics, kterou chcete upravit. 
4. Zastavit úlohu tak, že vyberete **Zastavit** v sadu příkazů. 
5. Upravte vstupy, dotazů a výstupy.
   
    Jednoduchých úprav je změna dotazu pro **pravidla** úlohu, která má použít **"<"** místo **">"**. Na portálu řešení stále zobrazuje **">"** při upravit pravidlo, ale Všimněte si, jak se chování obráceně z důvodu změny v základní úlohy.
6. Spuštění úlohy

> [!NOTE]
> Řídicím panelu vzdáleného monitorování závisí na konkrétní data, takže změna úloh může způsobit selhání řídicího panelu.

## <a name="add-your-own-rules"></a>Přidání vlastních pravidel

Kromě změna předkonfigurované úlohy Azure Stream Analytics, můžete na portálu Azure můžete přidat nové úlohy nebo přidat nové dotazy k stávající úlohy.

## <a name="customize-devices"></a>Vlastní nastavení zařízení

Mezi nejběžnější rozšíření aktivity ve spolupráci s zařízení, které jsou specifické pro váš scénář. Existuje několik metod pro práci s zařízení. Tyto metody zahrnují změny simulované zařízení tak, aby odpovídaly vašemu scénáři, nebo pomocí [sady SDK zařízení IoT] [ IoT Device SDK] připojit fyzického zařízení k řešení.

Podrobný návod k přidávání zařízení, najdete v článku [Iot Suite připojení zařízení](iot-suite-v1-connecting-devices.md) článku a [vzdálené monitorování C SDK – ukázka](https://github.com/Azure/azure-iot-sdk-c/tree/master/serializer/samples/remote_monitoring). Tato ukázka je navržen pro práci s předkonfigurovaného řešení vzdáleného monitorování.

### <a name="create-your-own-simulated-device"></a>Vytvoření simulovaného zařízení

Součástí [pro vzdálené monitorování řešení zdrojového kódu](https://github.com/Azure/azure-iot-remote-monitoring), je simulátoru .NET. Tato simulátoru je zřízená jako součást řešení a můžete ho odeslat rozdílná metadata, telemetrických dat, a reagovat na jiné příkazy a metody alter.

Simulátoru předem nakonfigurované v předkonfigurovaného řešení vzdáleného monitorování simuluje chladič zařízení, který vysílá telemetrická data teploty a vlhkosti. Můžete upravit v simulátoru [Simulator.WebJob](https://github.com/Azure/azure-iot-remote-monitoring/tree/master/Simulator/Simulator.WebJob) projektu, když jste forked úložiště GitHub.

### <a name="available-locations-for-simulated-devices"></a>K dispozici umístění pro simulované zařízení

Sada výchozí umístění je v Praze/Redmond, Washington, USA. Můžete změnit tato místa v [SampleDeviceFactory.cs][lnk-sample-device-factory].

### <a name="add-a-desired-property-update-handler-to-the-simulator"></a>Přidání obslužné rutiny aktualizace požadované vlastnosti simulátoru

Na portálu řešení můžete nastavit hodnotu pro požadovanou vlastnost pro zařízení. Je zodpovědností zařízení ke zpracování vlastnosti žádost o změnu, když zařízení načte hodnoty požadované vlastnosti. Chcete-li přidat podporu pro změnu hodnoty vlastnosti prostřednictvím požadované vlastnosti, přidání obslužné rutiny simulátoru.

Simulátor obsahuje obslužné rutiny pro **SetPointTemp** a **TelemetryInterval** vlastnosti, které lze aktualizovat nastavením požadované hodnoty na portálu řešení.

Následující příklad ukazuje rutinu **SetPointTemp** potřeby vlastnost **CoolerDevice** – třída:

```csharp
protected async Task OnSetPointTempUpdate(object value)
{
    var telemetry = _telemetryController as ITelemetryWithSetPointTemperature;
    telemetry.SetPointTemperature = Convert.ToDouble(value);

    await SetReportedPropertyAsync(SetPointTempPropertyName, telemetry.SetPointTemperature);
}
```

Tato metoda aktualizace teploty bodu telemetrie a hlásí změnu zpět do služby IoT Hub nastavením hlášené vlastnosti.

Podle vzoru v předchozím příkladu můžete přidat svoje vlastní obslužné rutiny pro vlastní vlastnosti.

Je třeba také svázat požadovanou vlastnost obslužná rutina jak je znázorněno v následujícím příkladu z **CoolerDevice** konstruktor:

```csharp
_desiredPropertyUpdateHandlers.Add(SetPointTempPropertyName, OnSetPointTempUpdate);
```

Všimněte si, že **SetPointTempPropertyName** konstanta definovaný jako "Config.SetPointTemp".

### <a name="add-support-for-a-new-method-to-the-simulator"></a>Přidat podporu pro nové metody pro simulátoru

Můžete přizpůsobit simulátoru přidat podporu pro nové [– metoda (přímá metoda)][lnk-direct-methods]. Existují dva klíče kroky:

- Simulátor musíte upozornit službu IoT hub v předkonfigurovaném řešení s podrobnostmi metody.
- Simulátor musí obsahovat kód pro zpracování volání metody, které při vyvolání z **podrobnosti o zařízení** panely v Průzkumníku řešení nebo prostřednictvím úlohu.

Vzdálené monitorování předkonfigurované řešení používá *hlášené vlastnosti* Odeslat podrobnosti o podporovaných metod do služby IoT hub. Back-end řešení udržuje seznam všech metod nepodporuje každé zařízení společně s historie volání metod. Můžete zobrazit tyto informace o zařízeních a volat metody na portálu řešení.

Oznámení služby IoT hub, zařízení podporuje metodu, zařízení musí přidat podrobnosti metody, která **SupportedMethods** uzlu ve vlastnostech hlášené:

```json
"SupportedMethods": {
  "<method signature>": "<method description>",
  "<method signature>": "<method description>"
}
```

Podpis metody má následující formát: `<method name>--<parameter #0 name>-<parameter #1 type>-...-<parameter #n name>-<parameter #n type>`. Například zadejte **InitiateFirmwareUpdate** metoda očekává parametr řetězec s názvem **FwPackageURI**, použijte následující podpis metody:

```json
InitiateFirmwareUpate--FwPackageURI-string: "description of method"
```

Seznam typů podporovaných parametrů najdete v tématu **CommandTypes** – třída v projektu infrastruktury.

Chcete-li odstranit metodu, nastavte podpis metody `null` ve vlastnostech hlášené.

> [!NOTE]
> Back-end řešení aktualizuje informace o podporovaných metod pouze, když obdrží *informace o zařízení* zprávy ze zařízení.

Následující příklad kódu z **SampleDeviceFactory** – třída v běžné projektu ukazuje, jak přidat metodu do seznamu **SupportedMethods** ve vlastnostech hlášené poslal zařízení:

```csharp
device.Commands.Add(new Command(
    "InitiateFirmwareUpdate",
    DeliveryType.Method,
    "Updates device Firmware. Use parameter 'FwPackageUri' to specifiy the URI of the firmware file, e.g. https://iotrmassets.blob.core.windows.net/firmwares/FW20.bin",
    new[] { new Parameter("FwPackageUri", "string") }
));
```

Tento fragment kódu přidá podrobnosti o **InitiateFirmwareUpdate** metoda včetně text, který se zobrazí na portálu řešení a podrobnosti o parametrech požadovaná metoda.

Simulátor odešle hlášené vlastnosti, včetně seznamu podporovaných metod do služby IoT Hub při spuštění simulátoru.

Přidání obslužné rutiny kód simulátor pro každou metodu, kterou podporuje. Zobrazí se stávající obslužné rutiny v **CoolerDevice** – třída v projektu Simulator.WebJob. Následující příklad ukazuje rutinu **InitiateFirmwareUpdate** metoda:

```csharp
public async Task<MethodResponse> OnInitiateFirmwareUpdate(MethodRequest methodRequest, object userContext)
{
    if (_deviceManagementTask != null && !_deviceManagementTask.IsCompleted)
    {
        return await Task.FromResult(BuildMethodRespose(new
        {
            Message = "Device is busy"
        }, 409));
    }

    try
    {
        var operation = new FirmwareUpdate(methodRequest);
        _deviceManagementTask = operation.Run(Transport).ContinueWith(async task =>
        {
            // after firmware completed, we reset telemetry
            var telemetry = _telemetryController as ITelemetryWithTemperatureMeanValue;
            if (telemetry != null)
            {
                telemetry.TemperatureMeanValue = 34.5;
            }

            await UpdateReportedTemperatureMeanValue();
        });

        return await Task.FromResult(BuildMethodRespose(new
        {
            Message = "FirmwareUpdate accepted",
            Uri = operation.Uri
        }));
    }
    catch (Exception ex)
    {
        return await Task.FromResult(BuildMethodRespose(new
        {
            Message = ex.Message
        }, 400));
    }
}
```

Metoda obslužná rutina názvy musí začínat `On` následuje název metody. **MethodRequest** parametr obsahuje žádné parametry předané pomocí volání metody z back-end řešení. Návratová hodnota musí být typu **úloh&lt;MethodResponse&gt;**. **BuildMethodResponse** nástroj metoda vám pomůže vytvořit návratovou hodnotu.

Uvnitř obslužné rutiny metoda může takto:

- Spustí asynchronní úlohu.
- Načíst požadované vlastnosti z *dvojče zařízení* IoT hub.
- Aktualizovat vlastnosti jediné hlášené pomocí **SetReportedPropertyAsync** metoda v **CoolerDevice** třídy.
- Aktualizovat více hlášené vlastnosti tak, že vytvoříte **TwinCollection** instance a volání **Transport.UpdateReportedPropertiesAsync** metoda.

V předchozím příkladu aktualizace firmwaru provede následující kroky:

- Kontroluje, zda je zařízení nemůže přijmout žádost o aktualizaci firmwaru.
- Asynchronně spustí operace aktualizace firmwaru a obnoví telemetrii po dokončení operace.
- Okamžitě vrátí zprávu "FirmwareUpdate přijata" se indikovat, že žádost byla přijata zařízení.

### <a name="build-and-use-your-own-physical-device"></a>Vytvoření a použití si vlastní zařízení (fyzické)

[SDK služby Azure IoT](https://github.com/Azure/azure-iot-sdks) poskytují knihovny pro připojení (jazyky a operační systémy) celou řadu typů zařízení do řešení IoT.

## <a name="modify-dashboard-limits"></a>Upravit omezení řídicí panel

### <a name="number-of-devices-displayed-in-dashboard-dropdown"></a>Počet zařízení, které jsou zobrazeny v rozevírací nabídce řídicí panel

Výchozí hodnota je 200. Můžete změnit toto číslo v [DashboardController.cs][lnk-dashboard-controller].

### <a name="number-of-pins-to-display-in-bing-map-control"></a>Počet kódů PIN pro zobrazení v ovládacím prvku mapy Bing

Výchozí hodnota je 200. Můžete změnit toto číslo v [TelemetryApiController.cs][lnk-telemetry-api-controller-01].

### <a name="time-period-of-telemetry-graph"></a>Časové období telemetrických dat grafu

Výchozí hodnota je 10 minut. Můžete změnit tuto hodnotu v [TelmetryApiController.cs][lnk-telemetry-api-controller-02].

## <a name="feedback"></a>Váš názor

Máte přizpůsobení chcete najdete v části zahrnuté v tomto dokumentu? Přidání funkce návrhy na [User Voice](https://feedback.azure.com/forums/321918-azure-iot), nebo komentář k tomuto článku. 

## <a name="next-steps"></a>Další postup

Další informace o možnostech přizpůsobení předkonfigurovaných řešení najdete v tématu:

* [Připojení aplikace logiky k řešení Azure IoT Suite vzdálené monitorování předkonfigurované][lnk-logicapp]
* [Použití dynamické telemetrie s předkonfigurovaného řešení vzdáleného monitorování][lnk-dynamic]
* [Informace metadat zařízení v předkonfigurovaného řešení vzdáleného monitorování][lnk-devinfo]
* [Přizpůsobit způsob, jakým připojené vytváření řešení zobrazí data z vašich serverů OPC UA][lnk-cf-customize]

[lnk-logicapp]: iot-suite-v1-logic-apps-tutorial.md
[lnk-dynamic]: iot-suite-v1-dynamic-telemetry.md
[lnk-devinfo]: iot-suite-v1-remote-monitoring-device-info.md

[IoT Device SDK]: https://azure.microsoft.com/documentation/articles/iot-hub-sdks-summary/
[lnk-permissions]: iot-suite-v1-permissions.md
[lnk-dashboard-controller]: https://github.com/Azure/azure-iot-remote-monitoring/blob/3fd43b8a9f7e0f2774d73f3569439063705cebe4/DeviceAdministration/Web/Controllers/DashboardController.cs#L27
[lnk-telemetry-api-controller-01]: https://github.com/Azure/azure-iot-remote-monitoring/blob/3fd43b8a9f7e0f2774d73f3569439063705cebe4/DeviceAdministration/Web/WebApiControllers/TelemetryApiController.cs#L27
[lnk-telemetry-api-controller-02]: https://github.com/Azure/azure-iot-remote-monitoring/blob/e7003339f73e21d3930f71ceba1e74fb5c0d9ea0/DeviceAdministration/Web/WebApiControllers/TelemetryApiController.cs#L25 
[lnk-sample-device-factory]: https://github.com/Azure/azure-iot-remote-monitoring/blob/master/Common/Factory/SampleDeviceFactory.cs#L40
[lnk-direct-methods]: ../iot-hub/iot-hub-devguide-direct-methods.md
[lnk-cf-customize]:../iot-accelerators/iot-accelerators-connected-factory-customize.md