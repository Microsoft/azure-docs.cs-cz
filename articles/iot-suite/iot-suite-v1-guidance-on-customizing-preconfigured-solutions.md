---
title: Přizpůsobení předkonfigurovaných řešení | Dokumentace Microsoftu
description: Poskytuje pokyny o tom, jak přizpůsobit Azure IoT Suite předkonfigurovaného řešení.
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
ms.sourcegitcommit: cc4fdd6f0f12b44c244abc7f6bc4b181a2d05302
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2018
ms.locfileid: "47106782"
---
# <a name="customize-a-preconfigured-solution"></a>Přizpůsobení předkonfigurovaného řešení

Předkonfigurovaná řešení, které jsou součástí sady Azure IoT Suite Ukázka služby v rámci sady spolupracují na poskytování-ucelené řešení. Od této chvíle počáteční jsou různých místech, ve kterých můžete rozšířit a přizpůsobit řešení pro konkrétní scénáře. Následující části popisují tyto společných bodů vlastního nastavení.

## <a name="find-the-source-code"></a>Vyhledání zdrojového kódu

Zdrojový kód předkonfigurovaného řešení je k dispozici na Githubu v úložištích následující:

* Vzdálené monitorování: [https://www.github.com/Azure/azure-iot-remote-monitoring](https://github.com/Azure/azure-iot-remote-monitoring)
* Prediktivní údržba: [https://github.com/Azure/azure-iot-predictive-maintenance](https://github.com/Azure/azure-iot-predictive-maintenance)
* Propojené továrny: [https://github.com/Azure/azure-iot-connected-factory](https://github.com/Azure/azure-iot-connected-factory)

Zdrojový kód předkonfigurovaného řešení je k dispozici k předvedení vzory a postupy používaný k implementaci funkce začátku do konce pro vaše řešení IoT s využitím sady Azure IoT Suite. Můžete najít další informace o tom, jak vytvářet a nasazovat řešení v úložištích GitHub.

## <a name="change-the-preconfigured-rules"></a>Změnit předkonfigurovaných pravidel

Řešení vzdáleného monitorování obsahuje tři [Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/) úlohy pro zpracování informací o zařízení, telemetrie a logiku pravidla v řešení.

Tři úlohy stream analytics a jejich syntaxe jsou popsány v části [vzdálené monitorování návod pro předkonfigurované řešení](iot-suite-v1-remote-monitoring-sample-walkthrough.md). 

Můžete upravit tyto úlohy přímo ke změně logiku nebo přidání logiky specifické pro váš scénář. Úlohy Stream Analytics můžete najít následujícím způsobem:

1. Přejděte na [webu Azure portal](https://portal.azure.com).
2. Přejděte do skupiny prostředků se stejným názvem jako řešení IoT. 
3. Vyberte úlohu Azure Stream Analytics, kterou chcete upravit. 
4. Po zastavení úlohy tak, že vyberete **Zastavit** v sadu příkazů. 
5. Upravte vstupy, výstupy a dotazů.
   
    Jednoduchých úprav, je změnit dotaz na **pravidla** úlohy pro použití **"<"** místo **">"**. Na portálu řešení stále zobrazuje **">"** při upravit pravidlo, ale Všimněte si, jak se chování obráceně z důvodu změny v rámci základní úlohy.
6. Spuštění úlohy

> [!NOTE]
> Řídicím panelu vzdáleného monitorování, závisí na konkrétní data, takže změna úlohy, které může způsobit selhání řídicího panelu.

## <a name="add-your-own-rules"></a>Přidat vlastní pravidla

Validated předkonfigurované úlohy Azure Stream Analytics můžete na webu Azure portal můžete přidat nové úlohy nebo přidat nové dotazy k existující úlohy.

## <a name="customize-devices"></a>Vlastní nastavení zařízení

Jeden z nejběžnějších aktivity rozšíření pracuje se zařízeními, které jsou specifické pro váš scénář. Existuje několik metod pro práci se zařízeními. Tyto metody patří změna simulovaného zařízení tak, aby odpovídaly vašemu scénáři nebo pomocí [sady SDK zařízení IoT] [ IoT Device SDK] k připojení fyzických zařízení k řešení.

Podrobné pokyny k přidání zařízení, najdete v článku [Iot Suite připojování zařízení](iot-suite-v1-connecting-devices.md) článku a [vzdálené monitorování C SDK – ukázka](https://github.com/Azure/azure-iot-sdk-c/tree/master/serializer/samples/remote_monitoring). Tato ukázka je navržen pro práci s předkonfigurovaného řešení vzdáleného monitorování.

### <a name="create-your-own-simulated-device"></a>Vytvoření simulovaného zařízení

Součástí [pro vzdálené monitorování zdrojový kód řešení](https://github.com/Azure/azure-iot-remote-monitoring), je simulátor .NET. Simulátor je zřízená jako součást řešení a můžete ho odeslat rozdílná metadata, telemetrická data, a reagovat na jiné příkazy a metodami změnit.

Předkonfigurované simulátor v předkonfigurovaném řešení vzdáleného monitorování simuluje chladnější zařízení, který vysílá telemetrická data teploty a vlhkosti. Můžete upravit v simulátoru [Simulator.WebJob](https://github.com/Azure/azure-iot-remote-monitoring/tree/master/Simulator/Simulator.WebJob) projektu, když jste Rozvětvená úložiště GitHub.

### <a name="available-locations-for-simulated-devices"></a>Dostupná umístění pro simulované zařízení

Sada výchozí umístění je v Seattlu/Redmond, Washington, USA. Můžete změnit v těchto umístěních [SampleDeviceFactory.cs][lnk-sample-device-factory].

### <a name="add-a-desired-property-update-handler-to-the-simulator"></a>Přidání obslužné rutiny aktualizace požadovaných vlastností do simulátoru

Můžete nastavit hodnotu požadované vlastnosti pro zařízení na portálu řešení. Je zodpovědností zařízení pro zpracování vlastnost žádost o změnu, když zařízení načítá hodnotu požadované vlastnosti. Přidání podpory pro změně hodnoty vlastnosti prostřednictvím požadovanou vlastnost, budete muset přidat obslužnou rutinu do simulátoru.

Simulátor obsahuje rutiny pro **SetPointTemp** a **TelemetryInterval** vlastnosti, které můžete aktualizovat tak, že nastavíte požadované hodnoty na portálu řešení.

Následující příklad ukazuje rutinu **SetPointTemp** požadované vlastnosti v **CoolerDevice** třídy:

```csharp
protected async Task OnSetPointTempUpdate(object value)
{
    var telemetry = _telemetryController as ITelemetryWithSetPointTemperature;
    telemetry.SetPointTemperature = Convert.ToDouble(value);

    await SetReportedPropertyAsync(SetPointTempPropertyName, telemetry.SetPointTemperature);
}
```

Tato metoda aktualizuje bodu teplotní telemetrie a oznamuje ho změny zpátky do služby IoT Hub nastavením ohlášené vlastnosti.

Pomocí následujícího vzoru v předchozím příkladu můžete přidat svoje vlastní obslužné rutiny pro vlastní vlastnosti.

Musíte také svázat požadovanou vlastnost na obslužnou rutinu jak je znázorněno v následujícím příkladu od **CoolerDevice** konstruktor:

```csharp
_desiredPropertyUpdateHandlers.Add(SetPointTempPropertyName, OnSetPointTempUpdate);
```

Všimněte si, že **SetPointTempPropertyName** je konstanta definovaná jako "Config.SetPointTemp".

### <a name="add-support-for-a-new-method-to-the-simulator"></a>Přidání podpory pro novou metodu do simulátoru

Můžete přizpůsobit simulátor přidat podporu pro nové [– metoda (přímé metody)][lnk-direct-methods]. Existují dvě klíčové kroky:

- Simulátor oznámí služby IoT hub v rámci předkonfigurovaného řešení s podrobnostmi metody.
- Simulátor musí obsahovat kód pro zpracování volání metody, které při vyvolání z **podrobnosti o zařízení** panelu v Průzkumníku řešení nebo projektu.

Vzdálené monitorování předkonfigurované řešení používá *ohlášené vlastnosti* odesílat podrobnosti o podporovaných metod do služby IoT hub. Back-end řešení udržuje seznam všech metod podporovaných jednotlivá zařízení společně s historie volání metod. Můžete zobrazit tyto informace o zařízeních a volat metody na portálu řešení.

Upozornění služby IoT hub, že zařízení podporuje metodu, zařízení musí přidat podrobnosti metody, která **SupportedMethods** uzel v ohlášených vlastností:

```json
"SupportedMethods": {
  "<method signature>": "<method description>",
  "<method signature>": "<method description>"
}
```

Podpis metody má následující formát: `<method name>--<parameter #0 name>-<parameter #1 type>-...-<parameter #n name>-<parameter #n type>`. Například, chcete-li určit **InitiateFirmwareUpdate** metoda očekává parametr řetězce s názvem **FwPackageURI**, použijte následující podpis metody:

```json
InitiateFirmwareUpate--FwPackageURI-string: "description of method"
```

Seznam podporované typy parametrů najdete v tématu **CommandTypes** třídu v projektu infrastruktury.

Chcete-li odstranit metodu, nastavte podpis metody `null` v ohlášené vlastnosti.

> [!NOTE]
> Back-end řešení aktualizuje informace o podporovaných metod pouze, když přijme *informace o zařízení* zpráv ze zařízení.

Následující ukázka kódu **SampleDeviceFactory** třídu v projektu běžné ukazuje, jak přidat metodu do seznamu **SupportedMethods** v ohlášených vlastností odeslané ze zařízení:

```csharp
device.Commands.Add(new Command(
    "InitiateFirmwareUpdate",
    DeliveryType.Method,
    "Updates device Firmware. Use parameter 'FwPackageUri' to specifiy the URI of the firmware file, e.g. https://iotrmassets.blob.core.windows.net/firmwares/FW20.bin",
    new[] { new Parameter("FwPackageUri", "string") }
));
```

Tento fragment kódu přidá podrobnosti **InitiateFirmwareUpdate** metoda včetně text, který se zobrazí na portálu řešení a podrobnosti o parametrech požadovanou metodu.

Simulátor odesílá ohlášené vlastnosti, včetně seznam podporovaných metod do služby IoT Hub při spuštění simulátoru.

Přidáte obslužnou rutinu kód simulátoru pro každou metodu, kterou podporuje. Zobrazí se existující obslužné rutiny ve **CoolerDevice** třídu v projektu Simulator.WebJob. Následující příklad ukazuje rutinu **InitiateFirmwareUpdate** metody:

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

Metoda obslužné rutiny názvy musí začínat `On` následovaný názvem metody. **MethodRequest** parametr obsahuje všechny parametry předané pomocí volání metody z back-endu řešení. Vrácená hodnota musí být typu **úloh&lt;MethodResponse&gt;**. **BuildMethodResponse** metody nástrojů vám pomůže vytvořit návratovou hodnotu.

Uvnitř obslužnou rutinu metody můžete:

- Spuštění asynchronní úlohy.
- Načítání požadovaných vlastností ze *dvojče zařízení* ve službě IoT Hub.
- Aktualizace jedné ohlášené vlastnosti pomocí **SetReportedPropertyAsync** metodu **CoolerDevice** třídy.
- Aktualizace více ohlášené vlastnosti tak, že vytvoříte **TwinCollection** instance a volání **Transport.UpdateReportedPropertiesAsync** metody.

V předchozím příkladu aktualizace firmwaru provede následující kroky:

- Ověří, že je možné tak, aby přijímal žádosti o aktualizaci firmwaru zařízení.
- Asynchronně zahájí operaci aktualizace firmwaru a obnoví telemetrická data po dokončení operace.
- Okamžitě odpovídá na zprávu "FirmwareUpdate přijata" označující, že žádost byla přijata v zařízení.

### <a name="build-and-use-your-own-physical-device"></a>Vytvářet a používat vlastní zařízení (fyzické)

[Sad SDK Azure IoT](https://github.com/Azure/azure-iot-sdks) poskytuje knihovny pro připojení (jazyky a operační systémy) využití nejrůznějších typů zařízení do řešení IoT.

## <a name="modify-dashboard-limits"></a>Upravit omezení řídicí panel

### <a name="number-of-devices-displayed-in-dashboard-dropdown"></a>Počet zařízení, které jsou zobrazeny v rozevírací nabídce řídicího panelu

Výchozí hodnota je 200. Můžete změnit toto číslo [DashboardController.cs][lnk-dashboard-controller].

### <a name="number-of-pins-to-display-in-bing-map-control"></a>Počet kódů PIN pro zobrazení v ovládacím prvku mapy Bing

Výchozí hodnota je 200. Můžete změnit toto číslo [TelemetryApiController.cs][lnk-telemetry-api-controller-01].

### <a name="time-period-of-telemetry-graph"></a>Časové období telemetrických dat grafu

Výchozí hodnota je 10 minut. Můžete změnit tuto hodnotu v [TelmetryApiController.cs][lnk-telemetry-api-controller-02].

## <a name="feedback"></a>Váš názor

Máte vlastní nastavení můžete byste si rádi pokryté v tomto dokumentu? Přidat funkci návrhy [User Voice](https://feedback.azure.com/forums/321918-azure-iot), nebo komentář v tomto článku. 

## <a name="next-steps"></a>Další postup

Další informace o možnostech přizpůsobení předkonfigurovaných řešení, naleznete v tématu:

* [Připojení aplikace logiky k řešení Azure IoT Suite vzdálené monitorování předkonfigurované][lnk-logicapp]
* [Použití dynamické telemetrie s předkonfigurovaného řešení vzdáleného monitorování][lnk-dynamic]
* [Informace metadat zařízení v předkonfigurovaném řešení vzdáleného monitorování][lnk-devinfo]
* [Přizpůsobení zobrazení dat ze serverů OPC UA řešení propojené továrny][lnk-cf-customize]

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