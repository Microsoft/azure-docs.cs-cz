---
title: Informace metadat zařízení v řešení vzdáleného monitorování | Dokumentace Microsoftu
description: Popis předkonfigurovaného řešení Azure IoT pro vzdálené monitorování a jeho architektura.
services: ''
suite: iot-suite
documentationcenter: ''
author: dominicbetts
manager: timlt
editor: ''
ms.assetid: 1b334769-103b-4eb0-a293-184f3d1ba9a3
ms.service: iot-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/02/2017
ms.author: dobett
ms.openlocfilehash: 4efea316c05f566add3e175bc5bb18842225ede3
ms.sourcegitcommit: e8f443ac09eaa6ef1d56a60cd6ac7d351d9271b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/12/2018
ms.locfileid: "35758157"
---
# <a name="device-information-metadata-in-the-remote-monitoring-preconfigured-solution"></a>Informace metadat zařízení v předkonfigurovaném řešení vzdáleného monitorování

Předkonfigurované řešení vzdáleného monitorování sady Azure IoT Suite ukazuje přístup ke správě metadat zařízení. Tento článek popisuje přístup, který trvá toto řešení vám umožní pochopit:

* Ukládá řešení metadat jaký zařízení.
* Jak řešení slouží ke správě metadat zařízení.

## <a name="context"></a>Kontext

Vzdálené monitorování předkonfigurované řešení používá [Azure IoT Hub] [ lnk-iot-hub] umožní vašim zařízením odesílat data do cloudu. Řešení ukládá informace o zařízeních ve třech různých umístěních:

| Umístění | Informace uložené | Implementace |
| -------- | ------------------ | -------------- |
| Registr identit | Id zařízení, ověřovací klíče, povolený stav | Integrované do služby IoT Hub |
| Dvojčata zařízení | Metadat: ohlášené vlastnosti, požadovaných vlastností, značky | Integrované do služby IoT Hub |
| Databáze Cosmos | Historie příkazu a – metoda | Vlastní řešení |

Služba IoT Hub obsahuje [registr identit zařízení] [ lnk-identity-registry] ke správě přístupu pro službu IoT hub a používá [dvojčata zařízení] [ lnk-device-twin] ke správě metadat zařízení. K dispozici je také vzdáleného monitorování specifická pro řešení *registr zařízení* , který uchovává historii příkazu a metody. Řešení vzdáleného monitorování využívá [Cosmos DB] [ lnk-docdb] databáze pro implementaci vlastního úložiště pro příkaz a metoda historie.

> [!NOTE]
> Předkonfigurované řešení vzdáleného monitorování udržuje registr identit zařízení synchronizované informace v databázi Cosmos DB. Stejné id zařízení používají k jedinečné identifikaci každého zařízení připojeného ke službě IoT hub.

## <a name="device-metadata"></a>Metadata zařízení

Služba IoT Hub udržuje [dvojče zařízení] [ lnk-device-twin] Každá simulovaná a fyzická zařízení připojeného k řešení vzdáleného monitorování. Toto řešení využívá dvojčata zařízení ke správě metadata přidružená k zařízení. Dvojče zařízení je dokument JSON spravuje pomocí služby IoT Hub a toto řešení využívá rozhraní API služby IoT Hub pro interakci s dvojčaty zařízení.

Dvojče zařízení ukládá tři typy metadat:

- *Ohlášené vlastnosti* se odesílají do služby IoT hub na zařízení. V řešení vzdáleného monitorování, Simulovaná zařízení odesílají ohlášené vlastnosti při spuštění a v reakci na **změnit stav zařízení** příkazy a metodami. Ohlášené vlastnosti v můžete zobrazit **seznam zařízení** a **podrobnosti o zařízení** na portálu řešení. Ohlášené vlastnosti jsou jen pro čtení.
- *Požadované vlastnosti* se načítají ze služby IoT hub zařízení. Zodpovídá za zařízení provést jakoukoli nutné konfiguraci změnit v zařízení. Je také odpovědnost zařízení a změny hlásí zpět do hub jako ohlášené vlastnosti. Můžete nastavit hodnotu požadované vlastnosti prostřednictvím portálu pro řešení.
- *Značky* existují pouze ve dvojčeti zařízení a nikdy se synchronizují se zařízením. Můžete nastavit hodnoty značek na portálu řešení a jejich použití filtrování seznamu zařízení. Řešení také využívá značku k identifikaci ikonu zobrazenou zařízení na portálu řešení.

Příklad oznámil, že vlastnosti ze simulovaných zařízení zahrnují výrobce, číslo modelu, zeměpisné šířky a délky. Simulovaná zařízení také vrátit seznam podporovaných metod jako ohlášené vlastnosti.

> [!NOTE]
> Kód simulovaného zařízení k aktualizaci ohlášených vlastností odeslaných zpět do služby IoT Hub využívá pouze požadované vlastnosti **Desired.Config.TemperatureMeanValue** a **Desired.Config.TelemetryInterval**. Všechny ostatní žádosti o změnu požadované vlastnosti jsou ignorovány.

Zařízení informace metadat JSON dokument uložený v databázi Cosmos DB registru zařízení má následující strukturu:

```json
{
  "DeviceProperties": {
    "DeviceID": "deviceid1",
    "HubEnabledState": null,
    "CreatedTime": "2016-04-25T23:54:01.313802Z",
    "DeviceState": "normal",
    "UpdatedTime": null
    },
  "SystemProperties": {
    "ICCID": null
  },
  "Commands": [],
  "CommandHistory": [],
  "IsSimulatedDevice": false,
  "id": "fe81a81c-bcbc-4970-81f4-7f12f2d8bda8"
}
```

> [!NOTE]
> Informace o zařízení může taky obsahovat metadata popisující telemetrická data, která zařízení odesílá do služby IoT Hub. Řešení vzdáleného monitorování používá tato metadata telemetrie přizpůsobit, jak na řídicím panelu zobrazí [dynamické telemetrie][lnk-dynamic-telemetry].

## <a name="lifecycle"></a>Životní cyklus

Při prvním vytvoření zařízení na portálu řešení, řešení vytvoří záznam v databázi Cosmos DB k ukládání historie příkazu a metody. V tomto okamžiku řešení také vytvoří záznam zařízení v registru identit zařízení, která generuje klávesy, které zařízení použije k ověření pomocí služby IoT Hub. Vytvoří také dvojčete zařízení.

Když se zařízení poprvé připojí k řešení, odešle ohlášené vlastnosti a zprávu s informacemi o zařízení. Hodnoty ohlášených vlastností se automaticky uloží ve dvojčeti zařízení. Ohlášené vlastnosti zahrnují výrobce zařízení, číslo modelu, sériového čísla a seznam podporovaných metod. Zprávu s informacemi o zařízení obsahuje seznam příkazů, na které zařízení podporuje, včetně informací o všechny parametry příkazu. Když řešení obdrží tuto zprávu, aktualizuje informace o zařízení v databázi Cosmos DB.

### <a name="view-and-edit-device-information-in-the-solution-portal"></a>Zobrazit a upravit informace o zařízení na portálu řešení

Seznam zařízení na portálu řešení zobrazuje následující vlastnosti zařízení jako sloupce ve výchozím nastavení: **stav**, **DeviceId**, **výrobce**, **modelu Číslo**, **sériové číslo**, **Firmware**, **platformy**, **procesoru**, a  **Nainstalovaná paměť RAM**. Sloupce můžete přizpůsobit kliknutím **editor sloupců**. Vlastnosti zařízení **zeměpisná šířka** a **délky** jednotka umístění v objektu Map Bing na řídicím panelu.

![Editor sloupců v seznamu zařízení][img-device-list]

V **podrobnosti o zařízení** podokně portálu řešení můžete upravit značky a požadované vlastnosti (uvedené vlastnosti jsou jen pro čtení).

![V podokně Podrobnosti o zařízení][img-device-edit]

Na portálu řešení můžete použít k odebrání zařízení z vašeho řešení. Při odebrání zařízení odebere položky zařízení v registru identit řešení a pak odstraní dvojčeti zařízení. Řešení také odstraní informace související s zařízení z databáze Cosmos DB. Před odstraněním zařízení, je nutné ho vypnout.

![Odebrání zařízení][img-device-remove]

## <a name="device-information-message-processing"></a>Zpracování zpráv informace o zařízení

Kterou zařízení odešle zprávy s informacemi o zařízení se liší od telemetrické zprávy. Zprávy s informacemi o zařízení zahrnovat příkazy, které zařízení může reagovat na a jakékoli historie příkazů. IoT Hub, samotný nezná metadat obsažených v zařízení informační zprávy a zprávu zpracuje stejným způsobem zpracovávat všechny zprávy typu zařízení cloud. V řešení vzdáleného monitorování [Azure Stream Analytics] [ lnk-stream-analytics] úloha (ASA) přečte zprávy ze služby IoT Hub. **DeviceInfo** úlohy stream analytics filtry pro zprávy, které obsahují **"ObjectType": "DeviceInfo"** a předává je do **EventProcessorHost** instance hostitele který běží ve webové úloze. Logiku **EventProcessorHost** instance používá ID zařízení aktualizovat záznam a najít záznam služby Cosmos DB pro konkrétní zařízení.

> [!NOTE]
> Informační zprávy zařízení uvádíme standardní zprávu typu zařízení cloud. Řešení rozlišuje mezi zprávy s informacemi o zařízení a telemetrická data zprávy pomocí Azure Stream Analytics dotazů.

## <a name="next-steps"></a>Další postup

Nyní jste dokončili studium, jak můžete přizpůsobit předkonfigurovaná řešení, můžete prozkoumat některé další funkce a možnosti předkonfigurovaná řešení IoT Suite:

* [Přehled řešení předkonfigurované prediktivní údržby][lnk-predictive-overview]
* [Nejčastější dotazy k sadě IoT Suite][lnk-faq]
* [Zabezpečení IoT od počátku][lnk-security-groundup]

<!-- Images and links -->
[img-device-list]: media/iot-suite-v1-remote-monitoring-device-info/image1.png
[img-device-edit]: media/iot-suite-v1-remote-monitoring-device-info/image2.png
[img-device-remove]: media/iot-suite-v1-remote-monitoring-device-info/image3.png

[lnk-iot-hub]: https://azure.microsoft.com/documentation/services/iot-hub/
[lnk-identity-registry]: ../iot-hub/iot-hub-devguide-identity-registry.md
[lnk-device-twin]: ../iot-hub/iot-hub-devguide-device-twins.md
[lnk-docdb]: https://azure.microsoft.com/documentation/services/documentdb/
[lnk-stream-analytics]: https://azure.microsoft.com/documentation/services/stream-analytics/
[lnk-dynamic-telemetry]: iot-suite-v1-dynamic-telemetry.md

[lnk-predictive-overview]:../iot-accelerators/iot-accelerators-predictive-overview.md
[lnk-faq]: iot-suite-v1-faq.md
[lnk-security-groundup]:/azure/iot-fundamentals/iot-security-ground-up
