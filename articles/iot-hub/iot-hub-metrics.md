---
title: Použití metrik k monitorování Azure IoT Hub | Dokumentace Microsoftu
description: Jak používat Azure IoT Hub metriky vyhodnocování a sledování celkového stavu vašeho centra IoT hub.
author: nberdy
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/25/2017
ms.author: nberdy
ms.openlocfilehash: b1c9854d794c88c28c82a4b6e40c81a29552223a
ms.sourcegitcommit: 776b450b73db66469cb63130c6cf9696f9152b6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/18/2018
ms.locfileid: "45984056"
---
# <a name="understand-iot-hub-metrics"></a>Vysvětlení metriky služby IoT Hub
Metrika služby IoT Hub poskytuje lepší data o stavu prostředků Azure IoT ve vašem předplatném Azure. Metriky služby IoT Hub umožňují posouzení celkového stavu služby IoT Hub a zařízení k němu připojená. Přístupných statistiky jsou důležité, protože pomáhají můžete zjistit, co se děje s vaší problémy IoT hub a nápovědu původních příčin aniž byste museli kontaktovat podporu Azure.

Ve výchozím nastavení jsou povolené metriky. Můžete zobrazit metriky služby IoT Hub na webu Azure Portal.

## <a name="how-to-view-iot-hub-metrics"></a>Postup zobrazení metriky služby IoT Hub
1. Vytvoření služby IoT hub. Můžete najít pokyny o tom, jak vytvořit IoT hub v [Začínáme] [ lnk-get-started] průvodce.
2. Otevře se okno služby IoT hub. Odtud, klikněte na tlačítko **metriky**.
   
    ![][1]
3. V okně metrik můžete zobrazit metriky pro službu IoT hub a vytváření vlastních zobrazení metriky. Můžete se rozhodnout poslat data metrik do účtu Azure Storage nebo koncový bod služby Event Hubs kliknutím **nastavení diagnostiky**.
   
    ![][2]

## <a name="iot-hub-metrics-and-how-to-use-them"></a>Metriky služby IoT Hub a způsob jejich použití
IoT Hub poskytuje několik metriky, které vám poskytnou přehled o stavu vašeho centra a celkový počet připojených zařízení. Můžete kombinovat informace z několika metrik k vykreslení větší přehled o stavu služby IoT hub. Následující tabulka popisuje metriky, které sleduje každý IoT hub a jak se jednotlivé metriky souvisí s celkový stav služby IoT hub.

|Metrika|Metriky zobrazovaný název|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|d2c.telemetry.ingress.allProtocol|Pokusy o odeslat zpráva telemetrie|Počet|Celkem|Počet zpráv typu zařízení cloud telemetrie, které se pokusil odeslat do služby IoT hub|Žádné dimenze|
|d2c.telemetry.ingress.success|Telemetrické zprávy odesílané|Počet|Celkem|Počet zpráv typu zařízení cloud telemetrie úspěšně odeslán do služby IoT hub|Žádné dimenze|
|c2d.Commands.egress.COMPLETE.Success|Dokončení příkazů|Počet|Celkem|Počet příkazů typu cloud zařízení zařízení byla úspěšně dokončena|Žádné dimenze|
|c2d.commands.egress.abandon.success|Příkazy opuštění|Počet|Celkem|Počet opuštěných zařízení příkazy typu cloud zařízení|Žádné dimenze|
|c2d.Commands.egress.Reject.Success|Příkazy zamítnuto|Počet|Celkem|Počet odmítnutých zařízení příkazy typu cloud zařízení|Žádné dimenze|
|devices.totalDevices|Celkový počet zařízení (zastaralé)|Počet|Celkem|Počet zařízení zaregistrovaný do služby IoT hub|Žádné dimenze|
|devices.connectedDevices.allProtocol|Připojené zařízení (zastaralé) |Počet|Celkem|Počet zařízení připojená ke službě IoT hub|Žádné dimenze|
|d2c.telemetry.egress.success|Směrování: telemetrické zprávy doručí|Počet|Celkem|Počet pokusů, které zprávy se úspěšně doručila na všechny koncové body pomocí služby IoT Hub směrování. Pokud zpráva je směrována do několika koncových bodů, tato hodnota zvýší o jedna pro každé úspěšné dodání. Pokud zpráva se doručí na stejný koncový bod vícekrát, tato hodnota zvýší o jedna pro každé úspěšné dodání.|Žádné dimenze|
|d2c.telemetry.egress.dropped|Směrování: telemetrie počet ztracených zpráv |Počet|Celkem|Počet pokusů, které služba IoT Hub směrování z důvodu dead koncové body byly zahodit zprávy. Tato hodnota nepočítá doručování zpráv do základní trasy dodaným vynechané zprávy nejsou existuje.|Žádné dimenze|
|d2c.telemetry.egress.orphaned|Směrování: telemetrické zprávy osamocené |Počet|Celkem|Počet pokusů, které zprávy byly osamocené přesměrováním služby IoT Hub, protože neodpovídají žádné pravidla směrování (včetně základní pravidlo). |Žádné dimenze|
|d2c.telemetry.egress.invalid|Směrování: telemetrické zprávy nekompatibilní|Počet|Celkem|Počet pokusů směrování služby IoT Hub se nepodařilo doručit z důvodu nekompatibility s koncovým bodem. Tato hodnota nezahrnuje opakovaných pokusů.|Žádné dimenze|
|d2c.telemetry.egress.fallback|Směrování: zprávy doručeny pro použití náhradní lokality|Počet|Celkem|Počet pokusů, které směrování služby IoT Hub doručení zprávy na koncový bod přidružený k použití náhradní lokality trasy.|Žádné dimenze|
|d2c.endpoints.egress.eventHubs|Směrování: zprávy doručeny do centra událostí|Počet|Celkem|Počet pokusů, které se úspěšně směrování služby IoT Hub doručení zpráv do koncových bodů centra událostí.|Žádné dimenze|
|d2c.endpoints.latency.eventHubs|Směrování: zpráva latence pro Centrum událostí|Milisekundy|Průměr|Průměrná latence (milisekundy) mezi zprávy příchozího přenosu dat do služby IoT Hub a příchozího přenosu dat zprávu do koncového bodu centra událostí.|Žádné dimenze|
|d2c.endpoints.egress.serviceBusQueues|Směrování: doručení zpráv do fronty služby Service Bus|Počet|Celkem|Počet pokusů, které se úspěšně směrování služby IoT Hub doručení zpráv do fronty služby Service Bus koncových bodů.|Žádné dimenze|
|d2c.endpoints.latency.serviceBusQueues|Směrování: zpráva latence pro frontu služby Service Bus|Milisekundy|Průměr|Průměrná latence (milisekundy) mezi zprávy příchozího přenosu dat do služby IoT Hub a příchozího přenosu dat telemetrické zprávy do fronty koncového bodu služby Service Bus.|Žádné dimenze|
|d2c.endpoints.egress.serviceBusTopics|Směrování: doručení zprávy do tématu služby Service Bus|Počet|Celkem|Počet pokusů, které se úspěšně směrování služby IoT Hub doručení zprávy do tématu služby Service Bus koncových bodů.|Žádné dimenze|
|d2c.endpoints.latency.serviceBusTopics|Směrování: zpráva latence pro téma služby Service Bus|Milisekundy|Průměr|Průměrná latence (milisekundy) mezi zprávy příchozího přenosu dat do služby IoT Hub a příchozího přenosu dat telemetrické zprávy do tématu koncovým bodem Service Bus.|Žádné dimenze|
|d2c.endpoints.egress.builtIn.events|Směrování: zprávy doručovat zprávy a události|Počet|Celkem|Počet pokusů, které se úspěšně směrování služby IoT Hub doručení zprávy na integrovaný koncový bod (zprávy/events).|Žádné dimenze|
|d2c.endpoints.latency.builtIn.events|Směrování: zpráva latence pro zprávy a události|Milisekundy|Průměr|Průměrná latence (milisekundy) mezi zprávy příchozího přenosu dat do služby IoT Hub a příchozího přenosu dat telemetrické zprávy do integrovaného koncového bodu (zprávy/events).|Žádné dimenze|
|d2c.endpoints.egress.Storage|Směrování: doručení zprávy do služby storage|Počet|Celkem|Počet pokusů, které se úspěšně směrování služby IoT Hub doručení zpráv do koncových bodů úložiště.|Žádné dimenze|
|d2c.endpoints.latency.Storage|Směrování: zpráva latence pro úložiště|Milisekundy|Průměr|Průměrná latence (milisekundy) mezi zprávy příchozího přenosu dat do služby IoT Hub a příchozího přenosu dat telemetrické zprávy na koncový bod úložiště.|Žádné dimenze|
|d2c.endpoints.egress.Storage.bytes|Směrování: data doručí do úložiště|B|Celkem|Objem dat (v bajtech) směrování služby IoT Hub doručí do koncových bodů úložiště.|Žádné dimenze|
|d2c.endpoints.egress.Storage.BLOBS|Směrování: objekty BLOB doručit do úložiště|Počet|Celkem|Počet pokusů, které směrování služby IoT Hub doručí do koncových bodů úložiště objektů BLOB.|Žádné dimenze|
|d2c.twin.read.success|Čtení dvojčat úspěšné ze zařízení|Počet|Celkem|Počet všech úspěšných čtení dvojčat zařízení iniciované.|Žádné dimenze|
|d2c.Twin.Read.failure|Čtení dvojčat ze zařízení se nezdařila|Počet|Celkem|Počet všech se nezdařilo čtení dvojčat zařízení inicioval.|Žádné dimenze|
|d2c.twin.read.size|Velikost odpovědi čtení dvojčat zařízení|B|Průměr|Průměrné, minimální a maximální ze všech úspěšné, zařízení iniciované dvojčete čtení.|Žádné dimenze|
|d2c.twin.update.success|Aktualizace dvojčat úspěšné ze zařízení|Počet|Celkem|Počet všech úspěšných aktualizací dvojčete zařízení iniciované.|Žádné dimenze|
|d2c.Twin.Update.failure|Aktualizace dvojčat ze zařízení s chybami|Počet|Celkem|Počet všech neúspěšných dvojčete zařízení iniciované aktualizací.|Žádné dimenze|
|d2c.twin.update.size|Velikost aktualizace dvojčete zařízení|B|Průměr|Průměrné a minimální a maximální velikost všech úspěšné, zařízení iniciované dvojčete aktualizace.|Žádné dimenze|
|c2d.methods.success|Volání úspěšné přímé metody|Počet|Celkem|Počet všech úspěšných volání přímé metody.|Žádné dimenze|
|c2d.Methods.failure|Přímé metody vyvolání se nezdařilo|Počet|Celkem|Počet všech se nezdařilo volání metody s přímým přístupem.|Žádné dimenze|
|c2d.Methods.requestSize|Žádost o velikosti vyvolání přímé metody|B|Průměr|Průměrné, minimální a maximální počet všech úspěšných požadavků přímé metody.|Žádné dimenze|
|c2d.methods.responseSize|Velikost odpovědi vyvolání přímé metody|B|Průměr|Průměrné, minimální a maximální všechny úspěšné přímé metody odpovědí.|Žádné dimenze|
|c2d.twin.read.success|Čtení dvojčat úspěšné z back-endu|Počet|Celkem|Počet všech úspěšných čtení dvojčat iniciované back-end.|Žádné dimenze|
|c2d.Twin.Read.failure|Čtení dvojčat se nezdařilo z back-endu|Počet|Celkem|Počet všech se nezdařilo čtení dvojčat iniciované back-end.|Žádné dimenze|
|c2d.twin.read.size|Velikost odpovědi čtení dvojčat z back-endu|B|Průměr|Průměr a minimální a maximální velikost všech úspěšné, iniciované back-end dvojčete čtení.|Žádné dimenze|
|c2d.twin.update.success|Aktualizace dvojčat úspěšné z back-endu|Počet|Celkem|Počet všech úspěšné aktualizace dvojčete iniciované back-end.|Žádné dimenze|
|c2d.Twin.Update.failure|Aktualizace dvojčat se nezdařilo z back-endu|Počet|Celkem|Počet všech neúspěšných iniciované back-end dvojčete aktualizací.|Žádné dimenze|
|c2d.twin.update.size|Velikost dvojčete aktualizací z back-endu|B|Průměr|Průměrné, minimální a maximální velikost všech úspěšné, iniciované back-end dvojčete aktualizace.|Žádné dimenze|
|twinQueries.success|Úspěšné dvojčete dotazy|Počet|Celkem|Počet všech úspěšných dvojčete dotazů.|Žádné dimenze|
|twinQueries.failure|Neúspěšné dvojčete dotazy|Počet|Celkem|Počet všech neúspěšných dvojčete dotazů.|Žádné dimenze|
|twinQueries.resultSize|Velikost výsledku dotazy dvojčete|B|Průměr|Průměrné, minimální a maximální velikosti výsledek všech dotazů na dvojčata úspěšné.|Žádné dimenze|
|jobs.createTwinUpdateJob.success|Úspěšné vytvoření úlohy aktualizace dvojčete|Počet|Celkem|Počet všech po úspěšném vytvoření úlohy aktualizace dvojčete.|Žádné dimenze|
|jobs.createTwinUpdateJob.failure|Neúspěšné vytvoření úlohy aktualizace dvojčete|Počet|Celkem|Počet všech neúspěšných vytváření úlohy aktualizace dvojčete.|Žádné dimenze|
|jobs.createDirectMethodJob.success|Úspěšné vytvoření úloh vyvolání – metoda|Počet|Celkem|Počet všech po úspěšném vytvoření úlohy vyvolání přímé metody.|Žádné dimenze|
|jobs.createDirectMethodJob.failure|Neúspěšné vytvoření úloh vyvolání – metoda|Počet|Celkem|Počet všech neúspěšných vytváření úloh vyvolání přímé metody.|Žádné dimenze|
|jobs.listJobs.success|Úspěšné volání do seznamu úloh|Počet|Celkem|Počet všech úspěšných volání do seznamu úloh.|Žádné dimenze|
|jobs.listJobs.failure|Neúspěšná volání do seznamu úloh|Počet|Celkem|Počet všech neúspěšných volání do seznamu úloh.|Žádné dimenze|
|jobs.cancelJob.success|V případě úspěšné úlohy|Počet|Celkem|Počet všech úspěšných volání zrušit úlohu.|Žádné dimenze|
|jobs.cancelJob.failure|Zrušení úlohy, která selhala|Počet|Celkem|Počet všech neúspěšných volání zrušit úlohu.|Žádné dimenze|
|jobs.queryJobs.success|Úspěšné úlohy dotazů|Počet|Celkem|Počet všech úspěšných volání do úlohy dotaz.|Žádné dimenze|
|jobs.queryJobs.failure|Neúspěšné úlohy dotazy|Počet|Celkem|Počet všech neúspěšných volání do úlohy dotaz.|Žádné dimenze|
|jobs.completed|Dokončené úlohy|Počet|Celkem|Počet všech dokončených úloh.|Žádné dimenze|
|jobs.Failed|Neúspěšné úlohy|Počet|Celkem|Počet všech neúspěšných úloh.|Žádné dimenze|
|d2c.telemetry.ingress.sendThrottle|Počet chyb omezení|Počet|Celkem|Omezí počet chyb omezení kvůli propustnost zařízení|Žádné dimenze|
|dailyMessageQuotaUsed|Celkový počet zpráv používá|Počet|Průměr|Počet celkový počet zpráv, které se dnes používají. Jedná se o kumulativní hodnotu, která se resetuje na nulu v 00:00 UTC každý den.|Žádné dimenze|
|deviceDataUsage|Celkový počet devicedata využití (zastaralé)|B|Celkem|Bajtů přenesených do a z jakékoli zařízení připojené k IOT hub|Žádné dimenze|
|deviceDataUsageV2|Využití dat celkový počet zařízení (preview)|B|Celkem|Bajtů přenesených do a z jakékoli zařízení připojené k IOT hub|Žádné dimenze|
|totalDeviceCount|Celkový počet zařízení (preview)|Počet|Průměr|Počet zařízení zaregistrovaný do služby IoT hub|Žádné dimenze|
|connectedDeviceCount|Připojené zařízení (preview)|Počet|Průměr|Počet zařízení připojená ke službě IoT hub|Žádné dimenze|
|Konfigurace|Konfigurace metrik|Počet|Celkem|Pro operace konfigurace metrik|Žádné dimenze|

## <a name="next-steps"></a>Další postup
Teď, když už víte, přehled metrik služby IoT Hub, přejděte na tento odkaz na další informace o správě služby Azure IoT Hub:

* [Monitorování operací][lnk-monitor]

Podrobněji prozkoumat možnosti služby IoT Hub, najdete v tématech:

* [Příručka vývojáře pro IoT Hub][lnk-devguide]
* [Nasazení AI do hraničních zařízení s použitím Azure IoT Edge][lnk-iotedge]

<!-- Links and images -->
[1]: media/iot-hub-metrics/enable-metrics-1.png
[2]: media/iot-hub-metrics/enable-metrics-2.png

[lnk-get-started]: quickstart-send-telemetry-dotnet.md
[lnk-operations-monitoring]: iot-hub-operations-monitoring.md
[lnk-scaling]: iot-hub-scaling.md
[lnk-dr]: iot-hub-ha-dr.md

[lnk-monitor]: iot-hub-operations-monitoring.md

[lnk-devguide]: iot-hub-devguide.md
[lnk-iotedge]: ../iot-edge/tutorial-simulate-device-linux.md
