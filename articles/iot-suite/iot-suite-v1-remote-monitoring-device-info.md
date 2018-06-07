---
title: Informace metadat zařízení v řešení vzdáleného monitorování | Microsoft Docs
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
ms.openlocfilehash: 80f03a4cef1d79e819c59ca68a786776a5c4edb7
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34636092"
---
# <a name="device-information-metadata-in-the-remote-monitoring-preconfigured-solution"></a>Informace metadat zařízení v předkonfigurovaného řešení vzdáleného monitorování

Azure IoT Suite předkonfigurovanému řešení vzdáleného monitorování ukazuje přístup pro správu metadat zařízení. Tento článek popisuje přístupů, které trvá toto řešení vám umožní pochopit:

* Jaká zařízení metadata ukládá řešení.
* Jak řešení spravuje metadat zařízení.

## <a name="context"></a>Kontext

Vzdálené monitorování předkonfigurované řešení používá [Azure IoT Hub] [ lnk-iot-hub] povolit zařízení k odesílání dat do cloudu. Řešení ukládá informace o zařízeních ve třech různých umístěních:

| Umístění | Informace uložené | Implementace |
| -------- | ------------------ | -------------- |
| Registr identit | Id zařízení, ověřovací klíče, povolené stavu | Integrovaný do služby IoT Hub |
| Dvojčata zařízení | Metadata: hlášené vlastnosti, požadované vlastnosti, značky | Integrovaný do služby IoT Hub |
| Databáze Cosmos | Příkaz a metoda historie | Vlastní řešení |

Služba IoT Hub obsahuje [registr identit zařízení] [ lnk-identity-registry] můžete spravovat přístup k služby IoT hub a používá [dvojčata zařízení] [ lnk-device-twin] ke správě zařízení metadat. Je také vzdálené monitorování řešení – konkrétní *registru zařízení* , ukládá příkaz a metoda historie. Používá řešení vzdáleného monitorování [Cosmos DB] [ lnk-docdb] databáze implementovat vlastní úložiště pro příkaz a metoda historie.

> [!NOTE]
> Předkonfigurované řešení vzdáleného monitorování udržuje registr identit zařízení synchronizované s informacemi v databázi Cosmos DB. Oba stejné id zařízení použít k jednoznačné identifikaci jednotlivých zařízení připojené ke službě IoT hub.

## <a name="device-metadata"></a>Metadat zařízení

IoT Hub uchovává [dvojče zařízení] [ lnk-device-twin] každé simulované a fyzické zařízení připojeného k řešení vzdáleného monitorování. Toto řešení využívá dvojčata zařízení ke správě budou metadata spojená s zařízení. Dvojče zařízení je dokument JSON spravuje pomocí služby IoT Hub a řešení používá rozhraní API služby IoT Hub pro interakci s dvojčata zařízení.

Tři typy metadata se ukládají dvojče zařízení:

- *Hlášené vlastnosti* zařízení odesílá do služby IoT hub. V řešení vzdáleného monitorování, Simulovaná zařízení odesílají hlášené vlastnosti při spuštění a v reakci na **změnit stav zařízení** příkazy a metody. Můžete zobrazit vlastnosti hlášené v **seznam zařízení** a **podrobnosti o zařízení** na portálu řešení. Hlášené vlastnosti jsou jen pro čtení.
- *Požadovaného vlastnosti* se načítají ze služby IoT hub zařízení. Je zodpovědností zařízení, aby všechny nezbytné konfigurace změnit na zařízení. Je také odpovědnost zařízení nahlásit změnu zpět k centru jako hlášené vlastnost. Můžete nastavit hodnotu požadované vlastnosti prostřednictvím portálu řešení.
- *Značky* existují jenom v dvojče zařízení a nikdy se synchronizují s zařízení. Můžete nastavit hodnoty značky na portálu řešení a použít je při filtrování seznamu zařízení. Značku řešení také používá k identifikaci ikonu zobrazíte pro zařízení na portálu řešení.

Příklad ohlásil, že vlastnosti z Simulovaná zařízení zahrnují výrobce, číslo modelu, zeměpisnou šířku a zeměpisnou délku. Simulovaná zařízení také vrátí seznam podporovaných metod jako hlášené vlastnost.

> [!NOTE]
> Kód simulovaného zařízení k aktualizaci ohlášených vlastností odeslaných zpět do služby IoT Hub využívá pouze požadované vlastnosti **Desired.Config.TemperatureMeanValue** a **Desired.Config.TelemetryInterval**. Všechny ostatní žádosti o změnu požadovanou vlastnost se ignorují.

Dokument JSON metadata informace zařízení uložena v databázi Cosmos DB registru zařízení má následující strukturu:

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
> Informace o zařízení může také obsahovat metadata k popisu telemetrie, které zařízení odesílá do služby IoT Hub. Řešení vzdáleného monitorování používá tato metadata telemetrie přizpůsobit jak řídicí panel zobrazuje [dynamické telemetrie][lnk-dynamic-telemetry].

## <a name="lifecycle"></a>Životní cyklus

Při prvním vytváření zařízení na portálu řešení, řešení vytvoří položku v databázi Cosmos DB pro ukládání historie příkaz a metoda. V tomto okamžiku řešení také vytvoří záznam zařízení v registru identit zařízení, která generuje klíče, který zařízení používá k ověření službou IoT Hub. Vytvoří také dvojče zařízení.

Když se zařízení poprvé připojí k řešení, odešle hlášené vlastnosti a informační zprávu zařízení. Hodnoty hlášené vlastností automaticky uloží do dvojče zařízení. Hlášené vlastnosti zahrnují výrobce zařízení, číslo modelu, sériové číslo a seznam podporovaných metod. Informační zpráva zařízení obsahuje seznam příkazů, které podporuje zařízení, včetně informací o všech parametrů příkazu. Při řešení obdrží tuto zprávu, aktualizuje informace o zařízení v databázi Cosmos DB.

### <a name="view-and-edit-device-information-in-the-solution-portal"></a>Zobrazit a upravit informace o zařízení na portálu řešení

V seznamu zařízení na portálu řešení zobrazuje následující vlastnosti zařízení jako sloupce ve výchozím nastavení: **stav**, **DeviceId**, **výrobce**, **modelu Číslo**, **sériové číslo**, **Firmware**, **platformy**, **procesoru**, a  **Nainstalovaná paměť RAM**. Sloupce můžete přizpůsobit kliknutím **editor sloupců**. Vlastnosti zařízení **zeměpisnou šířku** a **zeměpisné délky** jednotka umístění mapy Bing na řídicím panelu.

![Editor sloupců v seznamu zařízení][img-device-list]

V **podrobnosti o zařízení** podokně na portálu řešení můžete upravit požadované vlastnosti a značky (hlášené vlastnosti jsou jen pro čtení).

![V podokně podrobností zařízení][img-device-edit]

Na portálu řešení můžete odebrat zařízení z vašeho řešení. Když odeberete zařízení, řešení odebere položku zařízení z registru identit a poté se odstraní dvojče zařízení. Řešení také odebere informace související s zařízení z databáze Cosmos DB. Před odebráním zařízení, je nutné ho vypnout.

![Odebrání zařízení][img-device-remove]

## <a name="device-information-message-processing"></a>Zpracování zpráv informace o zařízení

Zprávy s informacemi o zařízení odeslaný zařízením se liší od telemetrické zprávy. Zprávy s informacemi o zařízení zahrnovat příkazy, které může reagovat na zařízení a všechny historii příkazů. IoT Hub, sám nemá žádné informace o metadat obsažených v informační zprávu zařízení a zprávu zpracuje stejným způsobem zpracovává všechny zprávy typu zařízení cloud. V řešení vzdáleného monitorování [Azure Stream Analytics] [ lnk-stream-analytics] úlohy (ASA) čte zprávy ze služby IoT Hub. **DeviceInfo** úlohy stream analytics se filtry pro zprávy, které obsahují **"ObjectType": "DeviceInfo"** a předává je na **EventProcessorHost** instance hostitele která se spouští v rámci webové úlohy. Logiky **EventProcessorHost** instance používá ID zařízení a najít záznam Cosmos DB pro určité zařízení aktualizovat záznam.

> [!NOTE]
> Informační zprávu zařízení je standardní zpráva zařízení cloud. Řešení rozlišuje mezi zprávy s informacemi o zařízení a telemetrické zprávy pomocí ASA dotazů.

## <a name="next-steps"></a>Další postup

Nyní jste dokončili učení, jak můžete přizpůsobit předkonfigurovaná řešení, můžete prozkoumat některé z dalších funkcí a možností předkonfigurovaná řešení IoT Suite:

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
[lnk-security-groundup]:../iot-accelerators/securing-iot-ground-up.md
