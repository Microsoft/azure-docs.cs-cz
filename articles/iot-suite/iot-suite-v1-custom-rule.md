---
title: Vytvoření vlastního pravidla v sadě Azure IoT Suite | Dokumentace Microsoftu
description: Postup vytvoření vlastního pravidla v sadě IoT Suite předkonfigurovaného řešení.
services: ''
suite: iot-suite
documentationcenter: ''
author: dominicbetts
manager: timlt
editor: ''
ms.assetid: 562799dc-06ea-4cdd-b822-80d1f70d2f09
ms.service: iot-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/02/2017
ms.author: dobett
ms.openlocfilehash: 9bf2a13035de141766fd935966ce18459dccdaab
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2018
ms.locfileid: "38590517"
---
# <a name="create-a-custom-rule-in-the-remote-monitoring-preconfigured-solution"></a>Vytvoření vlastního pravidla v předkonfigurovaném řešení vzdáleného monitorování

## <a name="introduction"></a>Úvod

V předkonfigurovaných řešeních, můžete nakonfigurovat [hodnota pravidla, která se aktivuje při telemetrie zařízení dosáhne určité prahové hodnoty][lnk-builtin-rule]. [Použití dynamické telemetrie pomocí vzdáleného monitorování předkonfigurované řešení] [ lnk-dynamic-telemetry] popisuje, jak přidat vlastní telemetrická data hodnoty jako *ExternalTemperature* do vašeho řešení. V tomto článku se dozvíte, jak vytvořit vlastní pravidlo pro dynamické telemetrie typy ve vašem řešení.

Tento kurz používá jednoduché simulovaného zařízení Node.js ke generování dynamických telemetrie k odeslání do předkonfigurovaného řešení back-endu. Potom přidáte vlastní pravidla v **RemoteMonitoring** řešení sady Visual Studio a nasazení této vlastní back-endu ke svému předplatnému Azure.

Pro absolvování tohoto kurzu potřebujete:

* Aktivní předplatné Azure. Pokud nemáte účet, můžete si během několika minut vytvořit bezplatný účet zkušební. Podrobnosti najdete v článku [Bezplatná zkušební verze Azure][lnk_free_trial].
* [Node.js] [ lnk-node] verze 0.12.x nebo novější k vytvoření simulovaného zařízení.
* Visual Studio 2015 nebo Visual Studio 2017 pro úpravu předkonfigurovaného řešení končit nová pravidla.

[!INCLUDE [iot-suite-v1-provision-remote-monitoring](../../includes/iot-suite-v1-provision-remote-monitoring.md)]

Poznamenejte si název řešení, které jste zvolili pro vaše nasazení. Později v tomto kurzu potřebujete název tohoto řešení.

[!INCLUDE [iot-suite-v1-send-external-temperature](../../includes/iot-suite-v1-send-external-temperature.md)]

Aplikace konzoly Node.js můžete zastavit, když si ověříte, jestli je posílá **ExternalTemperature** telemetrických dat do předkonfigurovaného řešení. V okně konzoly nechte otevřené, protože tato aplikace konzoly Node.js znovu spustit až do řešení přidat vlastní pravidlo.

## <a name="rule-storage-locations"></a>Pravidlo umístění úložiště

Informace o pravidlech se ukládají na dvou místech:

* **DeviceRulesNormalizedTable** tabulka – Tato tabulka ukládá normalizované odkaz z pravidel definovaných pomocí portálu řešení. Portál řešení zobrazí pravidla zařízení, vyžádá si tuto tabulku pro definice pravidla.
* **DeviceRules** objektů blob – tento objekt blob uloží všechna pravidla, které jsou definovány pro všechny zaregistrované zařízení a je definován jako vstup do úlohy Azure Stream Analytics.
 
Při aktualizaci existující pravidlo nebo definovat nové pravidlo na portálu řešení, tabulky a objektů blob jsou aktualizovány tak, aby odrážely změny. Definice pravidla zobrazí na portálu pochází z tabulky úložiště a definice pravidla odkazuje úlohy Stream Analytics pochází z objektu blob. 

## <a name="update-the-remotemonitoring-visual-studio-solution"></a>Aktualizace řešení sady RemoteMonitoring Visual Studio

Následující kroky ukazují, jak upravit řešení sady RemoteMonitoring Visual Studio zahrnout nové pravidlo, které používá **ExternalTemperature** telemetrická data odesílaná ze simulovaného zařízení:

1. Pokud jste tak již neučinili, naklonujte **azure-iot-remote-monitoring** úložiště do vhodného umístění na místním počítači pomocí následujícího příkazu Git:

    ```
    git clone https://github.com/Azure/azure-iot-remote-monitoring.git
    ```

2. V sadě Visual Studio, otevřete soubor RemoteMonitoring.sln z místní kopie **azure-iot-remote-monitoring** úložiště.

3. Otevřete soubor Infrastructure\Models\DeviceRuleBlobEntity.cs a přidejte **ExternalTemperature** vlastnost následujícím způsobem:

    ```csharp
    public double? Temperature { get; set; }
    public double? Humidity { get; set; }
    public double? ExternalTemperature { get; set; }
    ```

4. Ve stejném souboru přidejte **ExternalTemperatureRuleOutput** vlastnost následujícím způsobem:

    ```csharp
    public string TemperatureRuleOutput { get; set; }
    public string HumidityRuleOutput { get; set; }
    public string ExternalTemperatureRuleOutput { get; set; }
    ```

5. Otevřete soubor Infrastructure\Models\DeviceRuleDataFields.cs a přidejte následující **ExternalTemperature** vlastnost po existující **vlhkosti** vlastnost:

    ```csharp
    public static string ExternalTemperature
    {
        get { return "ExternalTemperature"; }
    }
    ```

6. Ve stejném souboru, aktualizujte **_availableDataFields** tak, aby zahrnoval **ExternalTemperature** následujícím způsobem:

    ```csharp
    private static List<string> _availableDataFields = new List<string>
    {                    
        Temperature, Humidity, ExternalTemperature
    };
    ```

7. Otevřete soubor Infrastructure\Repository\DeviceRulesRepository.cs a upravit **BuildBlobEntityListFromTableRows** metodu následujícím způsobem:

    ```csharp
    else if (rule.DataField == DeviceRuleDataFields.Humidity)
    {
        entity.Humidity = rule.Threshold;
        entity.HumidityRuleOutput = rule.RuleOutput;
    }
    else if (rule.DataField == DeviceRuleDataFields.ExternalTemperature)
    {
      entity.ExternalTemperature = rule.Threshold;
      entity.ExternalTemperatureRuleOutput = rule.RuleOutput;
    }
    ```

## <a name="rebuild-and-redeploy-the-solution"></a>Znovu sestavte a opětovnému nasazení řešení.

Teď můžete nasadit aktualizované řešení do vašeho předplatného Azure.

1. Otevřete příkazový řádek se zvýšenými oprávněními a přejděte do kořenové složce místní kopie úložiště azure-iot-remote-monitoring.

2. Pokud chcete nasadit řešení aktualizované, spusťte následující příkaz nahrazování **{název nasazení}** s názvem, který jste si poznamenali dříve nasazení předkonfigurovaného řešení:

    ```
    build.cmd cloud release {deployment name}
    ```

## <a name="update-the-stream-analytics-job"></a>Aktualizace úlohy Stream Analytics

Po dokončení nasazení můžete aktualizovat pomocí nové pravidlo definice úlohy Stream Analytics.

1. Na webu Azure Portal přejděte na skupinu prostředků obsahující prostředky předkonfigurovaného řešení. Tato skupina prostředků má stejný název, který jste zadali pro řešení během nasazení.

2. Přejděte na název nasazení {} – úlohy Stream Analytics pravidla. 

3. Klikněte na tlačítko **Zastavit** o zastavení úlohy Stream Analytics spouštění. (Je nutné počkat úlohu streamování zastavit před úpravou dotazu).

4. Klikněte na tlačítko **dotazu**. Upravit dotaz pro přidání **vyberte** příkaz pro **ExternalTemperature**. Následující příklad ukazuje kompletní dotaz s novými **vyberte** – příkaz:

    ```
    WITH AlarmsData AS 
    (
    SELECT
         Stream.IoTHub.ConnectionDeviceId AS DeviceId,
         'Temperature' as ReadingType,
         Stream.Temperature as Reading,
         Ref.Temperature as Threshold,
         Ref.TemperatureRuleOutput as RuleOutput,
         Stream.EventEnqueuedUtcTime AS [Time]
    FROM IoTTelemetryStream Stream
    JOIN DeviceRulesBlob Ref ON Stream.IoTHub.ConnectionDeviceId = Ref.DeviceID
    WHERE
         Ref.Temperature IS NOT null AND Stream.Temperature > Ref.Temperature
     
    UNION ALL
     
    SELECT
         Stream.IoTHub.ConnectionDeviceId AS DeviceId,
         'Humidity' as ReadingType,
         Stream.Humidity as Reading,
         Ref.Humidity as Threshold,
         Ref.HumidityRuleOutput as RuleOutput,
         Stream.EventEnqueuedUtcTime AS [Time]
    FROM IoTTelemetryStream Stream
    JOIN DeviceRulesBlob Ref ON Stream.IoTHub.ConnectionDeviceId = Ref.DeviceID
    WHERE
         Ref.Humidity IS NOT null AND Stream.Humidity > Ref.Humidity
     
    UNION ALL
     
    SELECT
         Stream.IoTHub.ConnectionDeviceId AS DeviceId,
         'ExternalTemperature' as ReadingType,
         Stream.ExternalTemperature as Reading,
         Ref.ExternalTemperature as Threshold,
         Ref.ExternalTemperatureRuleOutput as RuleOutput,
         Stream.EventEnqueuedUtcTime AS [Time]
    FROM IoTTelemetryStream Stream
    JOIN DeviceRulesBlob Ref ON Stream.IoTHub.ConnectionDeviceId = Ref.DeviceID
    WHERE
         Ref.ExternalTemperature IS NOT null AND Stream.ExternalTemperature > Ref.ExternalTemperature
    )
     
    SELECT *
    INTO DeviceRulesMonitoring
    FROM AlarmsData
     
    SELECT *
    INTO DeviceRulesHub
    FROM AlarmsData
    ```

5. Klikněte na tlačítko **Uložit** změnit dotaz aktualizovaná pravidla.

6. Klikněte na tlačítko **Start** spustit znovu úlohu Stream Analytics.

## <a name="add-your-new-rule-in-the-dashboard"></a>Přidat nové pravidlo na řídicím panelu

Teď můžete přidávat **ExternalTemperature** pravidlo, které zařízení na řídicím panelu řešení.

1. Přejděte na portálu řešení.

2. Přejděte **zařízení** panelu.

3. Vyhledání vlastních zařízení jste vytvořili, která odesílá **ExternalTemperature** telemetrických dat a na **podrobnosti o zařízení** panelu, klikněte na tlačítko **přidat pravidlo**.

4. Vyberte **ExternalTemperature** v **datové pole**.

5. Nastavte **prahová hodnota** až 56. Pak klikněte na tlačítko **uložit a zobrazit pravidla**.

6. Vraťte se do zobrazení historie alarmů na řídicím panelu.

7. V okně konzoly je otevřená, spusťte konzolovou aplikaci Node.js, aby se začala odesílat **ExternalTemperature** telemetrická data.

8. Všimněte si, **historie alarmů** tabulce jsou uvedeny nové alarmy při aktivaci nové pravidlo.
 
## <a name="additional-information"></a>Další informace

Změna operátor **>** je složitější a jde nad rámec podle kroků uvedených v tomto kurzu. Když změníte úlohy Stream Analytics použít jakýkoli operátor vám vyhovuje, odráží tento operátor na portálu řešení je složitější úlohy. 

## <a name="next-steps"></a>Další postup
Teď, když už víte, jak vytvořit vlastní pravidla, můžete další informace o předkonfigurovaných řešeních:

- [Připojení aplikace logiky k řešení Azure IoT Suite vzdálené monitorování předkonfigurované][lnk-logic-app]
- [Předkonfigurované řešení vzdáleného monitorování informace metadat zařízení][lnk-devinfo].

[lnk-devinfo]: iot-suite-v1-remote-monitoring-device-info.md

[lnk_free_trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-node]: http://nodejs.org
[lnk-builtin-rule]: iot-suite-v1-getstarted-preconfigured-solutions.md#view-alarms
[lnk-dynamic-telemetry]: iot-suite-v1-dynamic-telemetry.md
[lnk-logic-app]: iot-suite-v1-logic-apps-tutorial.md