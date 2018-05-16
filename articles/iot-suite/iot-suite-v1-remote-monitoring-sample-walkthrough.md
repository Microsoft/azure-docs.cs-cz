---
title: Návod pro předkonfigurované řešení vzdáleného monitorování | Dokumentace Microsoftu
description: Popis předkonfigurovaného řešení Azure IoT pro vzdálené monitorování a jeho architektura.
services: ''
suite: iot-suite
documentationcenter: ''
author: dominicbetts
manager: timlt
editor: ''
ms.assetid: 31fe13af-0482-47be-b4c8-e98e36625855
ms.service: iot-suite
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/02/2017
ms.author: dobett
ms.openlocfilehash: 3aa9bb9c785bb69c80d9bb33e595393a5a1d220a
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2018
---
# <a name="remote-monitoring-preconfigured-solution-walkthrough"></a>Návod pro předkonfigurované řešení vzdáleného monitorování

[Předkonfigurované řešení][lnk-preconfigured-solutions] vzdáleného monitorování sady IoT Suite je implementace uceleného monitorovacího řešení pro více počítačů spuštěných ve vzdálených umístěních. Řešení kombinuje klíčové služby Azure a poskytuje obecnou implementaci obchodního scénáře. Můžete ho použít jako výchozí bod pro vlastní implementaci a [přizpůsobit][lnk-customize] si ho tak, aby splňovalo vaše konkrétní obchodní požadavky.

Tento článek vás provede některými z klíčových prvků řešení vzdáleného monitorování, aby vám pomohl pochopit, jak toto řešení funguje. Díky tomu budete moct:

* Odstraňovat potíže v řešení.
* Naplánujte, jak řešení přizpůsobit podle konkrétních požadavků. 
* Navrhněte vlastní řešení IoT, které používá služby Azure.

## <a name="logical-architecture"></a>Logická architektura

Následující diagram popisuje logické součásti tohoto předkonfigurovaného řešení:

![Logická architektura](media/iot-suite-remote-monitoring-sample-walkthrough/remote-monitoring-architecture-updated.png)

## <a name="microservices--docker-containers"></a>Mikroslužby a kontejnery Dockeru
Vzdálené monitorování je prvním z našich předkonfigurovaných řešení, které využívá architekturu mikroslužeb. Řešení je dostupné v [.NET](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet) i v [Javě](https://github.com/Azure/azure-iot-pcs-remote-monitoring-java).
Z mikroslužeb se stal převládající způsob dosažení škálovatelnosti a flexibility (díky možnosti škálování jednotlivých kontejnerů) bez negativního vlivu na rychlost vývoje.
Mikroslužby rozčleňují kód a poskytují dobře definovaná rozhraní, díky kterým jsou řešení jednodušší a méně monolitická. Kromě toho také rozšiřují možnosti pro partnery, kteří chtějí rozšířit naše stávající akcelerátory řešení a sestavit hotová řešení, která je možné finančně zhodnotit.

**Další informace o kontejnerech Dockeru**
* [Instalace Dockeru](https://docs.docker.com/engine/installation/)
* [Běžné příkazy Dockeru pro vzdálené monitorování](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Reference-Guide#common-docker-commands)
* [Začínáme s Dockerem](https://docs.docker.com/get-started/)

## <a name="simulated-devices"></a>Simulovaná zařízení

V předkonfigurovaných řešeních představuje simulované zařízení chladící zařízení (například klimatizační jednotku v budově nebo vzduchovod). Při nasazení předkonfigurovaných řešení dojde také automaticky ke zřízení čtyř simulovaných zařízení, která běží ve [webových úlohách Azure][lnk-webjobs]. Simulovaná zařízení umožňují snadno zkoumat chování řešení, aniž by bylo nutné nasazovat fyzická zařízení. Pokud chcete nasadit skutečné fyzické zařízení, přečtěte si kurz [Připojení zařízení k předkonfigurovanému řešení vzdáleného monitorování][lnk-connect-rm].

### <a name="device-to-cloud-messages"></a>Zprávy typu zařízení-cloud

Každé simulované zařízení může odesílat do služby IoT Hub následující typy zpráv:

| Zpráva | Popis |
| --- | --- |
| Spuštění |Při spuštění zařízení odešle zprávu **device-info** s informace o sobě na back-end. Tato data obsahují ID zařízení a seznam příkazů a metod, které zařízení podporuje. |
| Přítomnost |Zařízení pravidelně odesílá zprávu o **presence**, která oznamuje, jestli zařízení rozpoznává přítomnost senzoru. |
| Telemetrická data |Zařízení pravidelně odesílá zprávu **telemetry**, která hlásí simulované hodnoty teploty a vlhkosti získané ze simulovaných senzorů zařízení. |

> [!NOTE]
> Řešení ukládá seznam příkazů, které zařízení podporuje, v databázi Cosmos DB a nikoli ve dvojčeti zařízení.

### <a name="properties-and-device-twins"></a>Vlastnosti a dvojčata zařízení

Simulovaná zařízení odesílají následující vlastnosti zařízení do [dvojčete][lnk-device-twins] ve službě IoT Hub jako *ohlášené vlastnosti*. Zařízení odesílá ohlášené vlastnosti při spuštění a jako reakci na metodu nebo příkaz **Change Device State** (Změnit stav zařízení).

| Vlastnost | Účel |
| --- | --- |
| Config.TelemetryInterval | Frekvence (v sekundách) odesílání telemetrie ze zařízení |
| Config.TemperatureMeanValue | Určuje střední hodnotu telemetrie simulované teploty |
| Device.DeviceID |ID, které je zadáno nebo přiřazeno při vytvoření zařízení v řešení |
| Device.DeviceState | Stav hlášený zařízením |
| Device.CreatedTime |Čas vytvoření zařízení v řešení |
| Device.StartupTime |Čas spuštění zařízení |
| Device.LastDesiredPropertyChange |Číslo verze poslední změny požadované vlastnosti |
| Device.Location.Latitude |Zeměpisná šířka umístění zařízení |
| Device.Location.Longitude |Zeměpisná délka umístění zařízení |
| System.Manufacturer |Výrobce zařízení |
| System.ModelNumber |Číslo modelu zařízení |
| System.SerialNumber |Sériové číslo zařízení |
| System.FirmwareVersion |Aktuální verzi firmwaru v zařízení |
| System.Platform |Architektura platformy zařízení |
| System.Processor |Procesor použitý v zařízení |
| System.InstalledRAM |Velikost paměti RAM nainstalované v zařízení |

Simulátor doplňuje pro tyto vlastnosti v simulovaném zařízení vzorové hodnoty. Pokaždé, když simulátor inicializuje simulované zařízení, ohlásí toto zařízení do služby IoT Hub předdefinovaná metadata jako ohlášené vlastnosti. Ohlášené vlastnosti může aktualizovat pouze zařízení. Pokud chcete změnit ohlášenou vlastnost, musíte na portálu řešení nastavit požadovanou vlastnost. Zařízení je zodpovědné za:

1. Pravidelné načítání požadovaných vlastností ze služby IoT Hub.
2. Aktualizaci vlastní konfigurace s použitím hodnot požadovaných vlastností.
3. Odesílání nových hodnot zpět do služby IoT Hub jako ohlášených vlastností.

Na řídicím panelu řešení můžete použít *požadované vlastnosti* k nastavení vlastností v zařízení pomocí [dvojčete zařízení][lnk-device-twins]. Zařízení obvykle načítá hodnotu požadované vlastnosti ze služby IoT Hub a aktualizuje svůj interní stav, a změny hlásí zpět jako ohlášené vlastnosti.

> [!NOTE]
> Kód simulovaného zařízení k aktualizaci ohlášených vlastností odeslaných zpět do služby IoT Hub využívá pouze požadované vlastnosti **Desired.Config.TemperatureMeanValue** a **Desired.Config.TelemetryInterval**. Všechny ostatní žádosti o změnu požadované vlastnosti se v simulovaném zařízení ignorují.

### <a name="methods"></a>Metody

Simulovaná zařízení můžou zpracovávat následující metody ([přímé metody][lnk-direct-methods]) vyvolané z portálu řešení prostřednictvím služby IoT Hub:

| Metoda | Popis |
| --- | --- |
| InitiateFirmwareUpdate |Dá zařízení pokyn k provedení aktualizace firmwaru |
| Restartování |Dá zařízení pokyn k restartování |
| FactoryReset |Dá zařízení pokyn k provedení obnovení do výrobního nastavení |

Některé metody používají ohlášené vlastnosti k podávání zpráv o průběhu. Například metoda **InitiateFirmwareUpdate** simuluje asynchronní spuštění aktualizace v zařízení. Metoda se v zařízení vykoná okamžitě, zatímco asynchronní úloha pomocí ohlášených vlastností stále odesílá aktualizace stavu zpět do řídicího panelu řešení.

### <a name="commands"></a>Příkazy

Simulovaná zařízení můžou zpracovávat následující příkazy (zprávy typu cloud-zařízení) odeslané z portálu řešení prostřednictvím služby IoT Hub:

| Příkaz | Popis |
| --- | --- |
| PingDevice |Odešle do zařízení *ping* pro účely kontroly, jestli je dané zařízení aktivní. |
| StartTelemetry |Spustí odesílání telemetrických dat ze zařízení |
| StopTelemetry |Zastaví odesílání telemetrických dat zařízením |
| ChangeSetPointTemp |Změní hodnotu bodu, na jehož základě se vytvářejí náhodná data |
| DiagnosticTelemetry |Aktivuje v simulátoru zařízení odesílání další telemetrické hodnoty (externalTemp) |
| ChangeDeviceState |Změní rozšířené stavové vlastnosti zařízení a odešle z něj zprávu s informacemi o zařízení |

> [!NOTE]
> Porovnání těchto příkazů (zpráv typu cloud-zařízení) a metod (přímých metod) najdete v [doprovodných materiálech ke komunikaci typu cloud-zařízení][lnk-c2d-guidance].

## <a name="iot-hub"></a>IoT Hub

Služba [IoT Hub][lnk-iothub] přijímá data odesílaná ze zařízení do cloudu a zpřístupňuje je úlohám Azure Stream Analytics (ASA). Každá úloha datového proudu ASA používá ke čtení streamu zpráv ze zařízení samostatnou skupinu příjemců IoT Hub.

Služba IoT Hub v řešení také:

- Udržuje registr identit, ve kterém jsou uloženy identifikátory a ověřovací klíče všech zařízení s povolením připojit se k portálu. Prostřednictvím registru identit můžete zařízení povolit nebo zakázat.
- Jménem portálu řešení odesílá příkazy do zařízení.
- Jménem portálu řešení vyvolává metody v zařízeních.
- Udržuje dvojčata zařízení pro všechna registrovaná zařízení. Dvojče zařízení ukládá hodnoty vlastností ohlášené zařízením. Dvojče zařízení také ukládá požadované vlastnosti nastavené na portálu řešení, aby si je zařízení při dalším připojení mohlo načíst.
- Plánuje úlohy, které nastaví vlastnosti pro více zařízení, nebo vyvolává metody ve více zařízeních.

## <a name="azure-stream-analytics"></a>Azure Stream Analytics

V řešení vzdáleného monitorování [Azure Stream Analytics][lnk-asa] (ASA) odesílá zprávy zařízení přijaté službou IoT Hub do dalších back-endových komponent ke zpracování nebo uložení. Různé úlohy ASA provádějí konkrétní funkce na základě obsahu zpráv.

**Úloha 1: Informace o zařízení** filtruje z příchozího datového proudu zprávy s informacemi o zařízení a odesílá je do koncového bodu Centra událostí. Zařízení odesílá zprávy s informacemi o zařízení při spuštění a jako odezvu na příkaz **SendDeviceInfo**. Tato úloha používá následující definici dotazu k identifikaci zpráv **device-info**:

```
SELECT * FROM DeviceDataStream Partition By PartitionId WHERE  ObjectType = 'DeviceInfo'
```

Tato úloha odesílá svůj výstup do služby Event Hub k dalšímu zpracování.

**Úloha 2: Pravidla** vyhodnotí příchozí telemetrická data o teplotě a vlhkosti v porovnání s mezními hodnotami na daném zařízení. Mezní hodnoty se nastavují v editoru pravidel, který je dostupný na portálu řešení. Každý pár zařízení/hodnota se ukládá pomocí časového razítka v objektu blob, který služba Stream Analytics načítá jako **referenční data**. Úloha porovná všechny neprázdné hodnoty s nastavenými mezními hodnotami v zařízení. Při překročení podmínky ' >' bude výstupem úlohy událost **alarmu**, která signalizuje, že je překročena mezní hodnota, a její součástí budou informace o zařízení, zjištěné hodnotě a časovém razítku. Tato úloha používá následující definice dotazu k identifikaci telemetrických zpráv, které mohou aktivovat alarm:

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
)

SELECT *
INTO DeviceRulesMonitoring
FROM AlarmsData

SELECT *
INTO DeviceRulesHub
FROM AlarmsData
```

Úloha odesílá svůj výstup do centra událostí k dalšímu zpracování a ukládá podrobnosti jednotlivých upozornění do úložiště objektů blob, odkud může informace o upozorněních přečíst portál řešení.

**Úloha 3: Telemetrická** má v příchozím datovém proudu telemetrických dat dvě funkce. Za prvé odeslání všech telemetrických zpráv ze zařízení do trvalého úložiště objektů blob k dlouhodobému uložení. Za druhé výpočet průměrné, minimální a maximální hodnoty vlhkosti v pětiminutovém posuvném okně a odeslání těchto dat do úložiště objektu blob. Portál řešení načítá telemetrická data z úložiště objektů blob a vytváří z nich grafy. Tato úloha používá následující definici dotazu:

```
WITH 
    [StreamData]
AS (
    SELECT
        *
    FROM [IoTHubStream]
    WHERE
        [ObjectType] IS NULL -- Filter out device info and command responses
) 

SELECT
    IoTHub.ConnectionDeviceId AS DeviceId,
    Temperature,
    Humidity,
    ExternalTemperature,
    EventProcessedUtcTime,
    PartitionId,
    EventEnqueuedUtcTime,
    * 
INTO
    [Telemetry]
FROM
    [StreamData]

SELECT
    IoTHub.ConnectionDeviceId AS DeviceId,
    AVG (Humidity) AS [AverageHumidity],
    MIN(Humidity) AS [MinimumHumidity],
    MAX(Humidity) AS [MaxHumidity],
    5.0 AS TimeframeMinutes 
INTO
    [TelemetrySummary]
FROM [StreamData]
WHERE
    [Humidity] IS NOT NULL
GROUP BY
    IoTHub.ConnectionDeviceId,
    SlidingWindow (mi, 5)
```

## <a name="event-hubs"></a>Event Hubs

Úlohy služby ASA **informace o zařízení** a **pravidla** odesílají výstupní data do služby Event Hubs, aby bylo zajištěno spolehlivé předání do **Procesoru událostí** spuštěném ve webové úloze.

## <a name="azure-storage"></a>Úložiště Azure

Toto řešení využívá službu Azure Blob Storage k trvalému uchování všech nezpracovaných a souhrnných telemetrických dat ze zařízení v řešení. Portál načítá telemetrická data z úložiště objektů blob a vytváří z nich grafy. Za účelem zobrazení upozornění portál načítá z úložiště objektů blob data, která zaznamenává, když hodnoty telemetrie překračují nakonfigurované prahové hodnoty. Řešení také využívá úložiště objektů blob pro záznam prahových hodnot, které nastavíte na portálu řešení.

## <a name="webjobs"></a>Webové úlohy

Webové úlohy v řešení kromě hostování simulátorů zařízení také hostují **Procesor událostí** spuštěný ve webové úloze Azure, který zpracovává odezvy na příkazy. Zprávy s odezvami na příkazy používá k aktualizaci historie příkazů zařízení (uložené v databázi Cosmos DB).

## <a name="cosmos-db"></a>Cosmos DB

Řešení používá k ukládání informací o zařízeních připojených k řešení databázi Cosmos DB. Tyto informace zahrnují historii příkazů odeslaných do zařízení z portálu řešení a metod vyvolaných z portálu řešení.

## <a name="solution-portal"></a>Portál řešení

Portál řešení je webová aplikace nasazená jako součást předkonfigurovaného řešení. Klíčové stránky na portálu řešení jsou Řídicí panel a Seznam zařízení.

### <a name="dashboard"></a>Řídicí panel

Tato stránka ve webové aplikaci používá ovládací prvky PowerBI v jazyce JavaScript (viz [Úložiště vizuálních prvků PowerBI](https://www.github.com/Microsoft/PowerBI-visuals)) k vizualizaci telemetrických dat ze zařízení. Řešení využívá telemetrickou úlohu ASA k zápisu telemetrických dat do úložiště objektů blob.

### <a name="device-list"></a>Seznam zařízení

Na této stránce portálu řešení můžete provádět následující akce:

* Zřízení nového zařízení. Tato akce nastaví jedinečné ID zařízení a vygeneruje klíč pro ověřování. Zapisuje informace o zařízení do registru identity služby IoT Hub a databáze Cosmos DB specifické pro řešení.
* Správa vlastností zařízení. Tato akce zahrnuje zobrazení existujících vlastností a aktualizaci novými vlastnostmi.
* Odesílat příkazy do zařízení.
* Zobrazit historii příkazů pro zařízení.
* Povolení a zákaz zařízení.

## <a name="next-steps"></a>Další kroky

Následující příspěvky na blogu TechNet poskytují další podrobnosti o předkonfigurovaném řešení pro vzdálené monitorování:

* [Sada IoT Suite - Pod pokličkou - Vzdálené monitorování](http://social.technet.microsoft.com/wiki/contents/articles/32941.iot-suite-v1-under-the-hood-remote-monitoring.aspx)
* [Sada IoT Suite - Vzdálené monitorování - Přidávání skutečných a simulovaných zařízení](http://social.technet.microsoft.com/wiki/contents/articles/32975.iot-suite-v1-remote-monitoring-adding-live-and-simulated-devices.aspx)

Další informace o sadě IoT Suite najdete v následujících článcích:

* [Připojení zařízení k předkonfigurovanému řešení vzdáleného monitorování][lnk-connect-rm]
* [Oprávnění na webu azureiotsuite.com][lnk-permissions]

[lnk-preconfigured-solutions]: iot-suite-v1-what-are-preconfigured-solutions.md
[lnk-customize]: iot-suite-v1-guidance-on-customizing-preconfigured-solutions.md
[lnk-iothub]: https://azure.microsoft.com/documentation/services/iot-hub/
[lnk-asa]: https://azure.microsoft.com/documentation/services/stream-analytics/
[lnk-webjobs]: https://azure.microsoft.com/documentation/articles/websites-webjobs-resources/
[lnk-connect-rm]: iot-suite-v1-connecting-devices.md
[lnk-permissions]: iot-suite-v1-permissions.md
[lnk-c2d-guidance]: ../iot-hub/iot-hub-devguide-c2d-guidance.md
[lnk-device-twins]:  ../iot-hub/iot-hub-devguide-device-twins.md
[lnk-direct-methods]: ../iot-hub/iot-hub-devguide-direct-methods.md
