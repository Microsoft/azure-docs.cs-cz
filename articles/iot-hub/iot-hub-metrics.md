---
title: Použít metriky k monitorování Azure IoT Hub | Microsoft Docs
description: Jak používat Azure IoT Hub metriky při vyhodnocování a sledování celkového stavu vašeho centra IoT.
author: nberdy
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/25/2017
ms.author: nberdy
ms.openlocfilehash: 754901bb9c851f66708771346cbb7fe2e42688cd
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34634059"
---
# <a name="understand-iot-hub-metrics"></a>Pochopení metriky služby IoT Hub
IoT Hub metriky získáte lepší data týkající se stavu služby Azure IoT prostředky ve vašem předplatném Azure. IoT Hub metriky umožňují posouzení celkového stavu služby IoT Hub a zařízení k němu připojená. Uživatelsky orientovaný statistiky jsou důležité, protože umožňují můžete zjistit, co se děje s vaší problémy IoT hub a nápovědy příčin bez nutnosti požádejte podporu Azure.

Ve výchozím nastavení jsou povolené metriky. Můžete zobrazit metriky služby IoT Hub z portálu Azure.

## <a name="how-to-view-iot-hub-metrics"></a>Postup zobrazení metriky služby IoT Hub
1. Vytvoření služby IoT hub. Pokyny k vytvoření služby IoT hub v naleznete [Začínáme] [ lnk-get-started] průvodce.
2. Otevřete okno služby IoT hub. Zde, klikněte na tlačítko **metriky**.
   
    ![][1]
3. V okně metriky můžete zobrazit metriky pro službu IoT hub a vytváření vlastních pohledů vaše metriky. Můžete odeslat data metriky koncový bod centra událostí nebo účet služby Azure Storage kliknutím **nastavení diagnostiky**.
   
    ![][2]

## <a name="iot-hub-metrics-and-how-to-use-them"></a>Metriky služby IoT Hub a jejich použití
Služba IoT Hub zajišťuje několik metriky získáte přehled o stavu rozbočovače a celkový počet připojených zařízení. Informace z více metriky pro malování větší přehled o stavu služby IoT hub můžete kombinovat. Následující tabulka popisuje metriky, které sleduje každé služby IoT hub a jak jednotlivé metriky souvisí s celkového stavu služby IoT hub.

|Metrika|Metriky zobrazovaný název|Jednotka|Typ agregace|Popis|
|---|---|---|---|---|
|d2c.telemetry.ingress.allProtocol|Pokusy o odeslání zprávy telemetrie|Počet|Celkem|Počet zařízení cloud telemetrické zprávy se pokusil odeslat do služby IoT hub|
|d2c.telemetry.ingress.success|Telemetrické zprávy odeslané|Počet|Celkem|Počet zařízení cloud telemetrické zprávy úspěšně odeslán do služby IoT hub|
|c2d.Commands.egress.COMPLETE.Success|Příkazy byla dokončena|Počet|Celkem|Počet příkazů typu cloud zařízení úspěšně dokončila, a zařízení|
|c2d.commands.egress.abandon.success|Příkazy opuštění|Počet|Celkem|Počet příkazů typu cloud zařízení opuštěny v rámci zařízení|
|c2d.Commands.egress.Reject.Success|Příkazy odmítnut|Počet|Celkem|Počet zařízení byl odmítnut příkazů typu cloud zařízení|
|devices.totalDevices|Celkem zařízení|Počet|Celkem|Počet zařízení zaregistrovat do služby IoT hub|
|devices.connectedDevices.allProtocol|Připojená zařízení|Počet|Celkem|Počet zařízení připojených ke službě IoT hub|
|d2c.telemetry.egress.success|Telemetrické zprávy doručit|Počet|Celkem|Počet zpráv byla úspěšně zapsána do koncových bodů (celkem)|
|d2c.telemetry.egress.dropped|Vyřazené zprávy|Počet|Celkem|Počet zpráv, vyřadit, protože neodpovídá žádné trasy a záložní trasy, která byla zakázána.|
|d2c.telemetry.egress.orphaned|Osamocené zprávy|Počet|Celkem|Počet zpráv, na které se neshodují žádné cesty, včetně záložní trasy|
|d2c.telemetry.egress.invalid|Neplatné zprávy|Počet|Celkem|Počet zpráv, které nejsou doručeny z důvodu nekompatibility s nástrojem koncový bod|
|d2c.telemetry.egress.fallback|Zprávy odpovídající záložního stavu|Počet|Celkem|Počet zpráv zapsaných do záložního koncového bodu|
|d2c.endpoints.egress.eventHubs|Zpráv doručených do koncové body centra událostí|Počet|Celkem|Počet zpráv byla úspěšně zapsána do koncové body centra událostí|
|d2c.endpoints.latency.eventHubs|Latence zprávy pro koncové body centra událostí|Milisekundy|Průměr|Průměrná latence mezi příchozí zprávy do služby IoT hub a příchozí zprávy do koncového bodu centra událostí, v milisekundách|
|d2c.endpoints.egress.serviceBusQueues|Doručování zpráv do fronty Service Bus koncové body|Počet|Celkem|Počet zpráv byla úspěšně zapsána do koncových bodů frontou Service Bus|
|d2c.endpoints.latency.serviceBusQueues|Latence zprávy pro koncové body frontou Service Bus|Milisekundy|Průměr|Průměrná latence mezi příchozí zprávy do služby IoT hub a příjem příchozích dat zpráv do fronty Service Bus koncový bod, v milisekundách|
|d2c.endpoints.egress.serviceBusTopics|Doručování zpráv do tématu Service Bus koncové body|Počet|Celkem|Počet zpráv byly úspěšně zapsána do tématu Service Bus koncové body|
|d2c.endpoints.latency.serviceBusTopics|Latence zprávy pro koncové body témata sběrnice|Milisekundy|Průměr|Průměrná latence mezi příchozí zprávy do služby IoT hub a příchozí zprávy do tématu Service Bus koncový bod, v milisekundách|
|d2c.endpoints.egress.builtIn.events|Zpráv doručených do vestavěným koncovým bodem (zprávy nebo události)|Počet|Celkem|Počet zpráv byla úspěšně zapsána do vestavěným koncovým bodem (zprávy nebo události)|
|d2c.endpoints.latency.builtIn.events|Zpráva latence pro předdefinovaný koncový bod (zprávy nebo události)|Milisekundy|Průměr|Průměrná latence mezi příchozí zprávy do služby IoT hub a příjem příchozích dat zpráva do vestavěným koncovým bodem (zprávy událostí), v milisekundách |
|d2c.twin.read.success|Úspěšné twin čte ze zařízení|Počet|Celkem|Počet čtení všechny úspěšné twin spouštěná zařízení.|
|d2c.Twin.Read.failure|Čtení twin ze zařízení se nezdařila|Počet|Celkem|Počet všech nepodařilo spouštěná zařízení twin čtení.|
|d2c.twin.read.size|Velikost odpovědi twin čtení ze zařízení|B|Průměr|Průměr, min a max všechny úspěšné, spouštěná zařízení twin čtení.|
|d2c.twin.update.success|Aktualizace úspěšná twin ze zařízení|Počet|Celkem|Počet aktualizací všechny úspěšné twin spouštěná zařízení.|
|d2c.Twin.Update.failure|Aktualizace twin ze zařízení se nezdařila|Počet|Celkem|Počet všech se nezdařila aktualizace twin spouštěná zařízení.|
|d2c.twin.update.size|Velikost twin aktualizace ze zařízení|B|Průměr|Průměr, minimální a maximální velikost všech úspěšné, spouštěná zařízení twin aktualizace.|
|c2d.methods.success|Volání úspěšné přímá metoda|Počet|Celkem|Počet všech úspěšných volání přímá metoda.|
|c2d.Methods.failure|Přímá metoda volání se nezdařilo|Počet|Celkem|Počet všech se nezdařila, volání metod direct.|
|c2d.Methods.requestSize|Žádost o velikosti volání přímá metoda|B|Průměr|Průměr, min a max všechny úspěšné požadavky přímá metoda.|
|c2d.methods.responseSize|Velikost odpovědi volání přímá metoda|B|Průměr|Průměr, min a max všechny úspěšné odpovědi přímá metoda.|
|c2d.twin.read.success|Úspěšné twin čte z back-end|Počet|Celkem|Počet všech úspěšně spustil back-end twin čtení.|
|c2d.Twin.Read.failure|Neúspěšné twin čte z back-end|Počet|Celkem|Počet všech se nezdařilo čtení twin iniciované back-end.|
|c2d.twin.read.size|Velikost odpovědi twin čtení z back-end|B|Průměr|Průměr, min a max všechny úspěšné, iniciované back-end twin čtení.|
|c2d.twin.update.success|Aktualizace úspěšná twin z back-end|Počet|Celkem|Počet aktualizací všechny úspěšné twin iniciované back-end.|
|c2d.Twin.Update.failure|Aktualizace se nezdařila twin z back-end|Počet|Celkem|Počet všech se nezdařila aktualizace twin iniciované back-end.|
|c2d.twin.update.size|Velikost twin aktualizace z back-end|B|Průměr|Průměr, minimální a maximální velikost všech úspěšné, iniciované back-end twin aktualizace.|
|twinQueries.success|Úspěšné twin dotazy|Počet|Celkem|Počet všech úspěšné twin dotazů.|
|twinQueries.failure|Neúspěšné twin dotazy|Počet|Celkem|Počet všechny neúspěšné twin dotazy.|
|twinQueries.resultSize|Objem výsledků dotazů Twin|B|Průměr|Průměr, minimální a maximální velikosti výsledek všechny úspěšné twin dotazy.|
|jobs.createTwinUpdateJob.success|Úspěšné vytvoření úloh aktualizace twin|Počet|Celkem|Počet všech úspěšném vytvoření twin aktualizace úloh.|
|jobs.createTwinUpdateJob.failure|Neúspěšné vytvoření úloh aktualizace twin|Počet|Celkem|Počet všech se nezdařilo vytvoření twin aktualizace úlohy.|
|jobs.createDirectMethodJob.success|Úspěšné vytvoření úloh volání – metoda|Počet|Celkem|Počet všech úspěšném vytvoření přímá metoda volání úloh.|
|jobs.createDirectMethodJob.failure|Neúspěšné vytvoření úloh volání – metoda|Počet|Celkem|Počet všech se nezdařilo vytvoření přímá metoda vyvolání úlohy.|
|jobs.listJobs.success|Úspěšné volání do seznamu úloh|Počet|Celkem|Počet všech úspěšných volání do seznamu úloh.|
|jobs.listJobs.failure|Volání do seznamu úloh se nezdařilo|Počet|Celkem|Počet všech selhání volání do seznamu úloh.|
|jobs.cancelJob.success|Zrušení úloh úspěšné|Počet|Celkem|Počet všech úspěšných volání zrušení úlohy.|
|jobs.cancelJob.failure|Informace o neúspěšné úloze zrušení|Počet|Celkem|Počet všech volání se nezdařilo zrušit úlohu.|
|jobs.queryJobs.success|Až se úloha úspěšně dotazy|Počet|Celkem|Počet všech úspěšných volání do úlohy dotaz.|
|jobs.queryJobs.failure|Úloha se dotazuje se nezdařilo|Počet|Celkem|Počet všech selhání volání do úlohy dotaz.|
|jobs.completed|Dokončené úlohy|Počet|Celkem|Počet všech dokončené úlohy.|
|jobs.Failed|Neúspěšné úlohy|Počet|Celkem|Počet všechny neúspěšné úlohy.|

## <a name="next-steps"></a>Další postup
Teď, když jste se seznámili přehled metriky služby IoT Hub, použijte tento odkaz na další informace o správě Azure IoT Hub:

* [Monitorování operací][lnk-monitor]

Pokud chcete prozkoumat další možnosti IoT Hub, najdete v části:

* [Příručka vývojáře pro službu IoT Hub][lnk-devguide]
* [Nasazení AI do hraničních zařízení s použitím Azure IoT Edge][lnk-iotedge]

<!-- Links and images -->
[1]: media/iot-hub-metrics/enable-metrics-1.png
[2]: media/iot-hub-metrics/enable-metrics-2.png

[lnk-get-started]: iot-hub-csharp-csharp-getstarted.md
[lnk-operations-monitoring]: iot-hub-operations-monitoring.md
[lnk-scaling]: iot-hub-scaling.md
[lnk-dr]: iot-hub-ha-dr.md

[lnk-monitor]: iot-hub-operations-monitoring.md

[lnk-devguide]: iot-hub-devguide.md
[lnk-iotedge]: ../iot-edge/tutorial-simulate-device-linux.md
