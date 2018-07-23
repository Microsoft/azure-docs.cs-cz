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
ms.openlocfilehash: b41458f0201c46b99c09d0bfffd219743a36ad50
ms.sourcegitcommit: bf522c6af890984e8b7bd7d633208cb88f62a841
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/20/2018
ms.locfileid: "39186751"
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

|Metrika|Metriky zobrazovaný název|Jednotka|Typ agregace|Popis|
|---|---|---|---|---|
|d2c.telemetry.ingress.allProtocol|Pokusy o odeslat zpráva telemetrie|Počet|Celkem|Počet zpráv typu zařízení cloud telemetrie, které se pokusil odeslat do služby IoT hub|
|d2c.telemetry.ingress.success|Telemetrické zprávy odesílané|Počet|Celkem|Počet zpráv typu zařízení cloud telemetrie úspěšně odeslán do služby IoT hub|
|c2d.Commands.egress.COMPLETE.Success|Dokončení příkazů|Počet|Celkem|Počet příkazů typu cloud zařízení zařízení byla úspěšně dokončena|
|c2d.commands.egress.abandon.success|Příkazy opuštění|Počet|Celkem|Počet opuštěných zařízení příkazy typu cloud zařízení|
|c2d.Commands.egress.Reject.Success|Příkazy zamítnuto|Počet|Celkem|Počet odmítnutých zařízení příkazy typu cloud zařízení|
|devices.totalDevices|Celkem zařízení|Počet|Celkem|Počet zařízení zaregistrovaný do služby IoT hub|
|devices.connectedDevices.allProtocol|Připojená zařízení|Počet|Celkem|Počet zařízení připojená ke službě IoT hub|
|d2c.telemetry.egress.success|Telemetrické zprávy doručí|Počet|Celkem|Počet pokusů, které zprávy se úspěšně zapsaly do koncových bodů (celkem)|
|d2c.telemetry.egress.dropped|Vyřazené zprávy|Počet|Celkem|Počet zpráv, které zahozena, protože neodpovídá žádné trasy a náhradní trasa byla zakázána.|
|d2c.telemetry.egress.orphaned|Osamocené zprávy|Počet|Celkem|Počet zpráv neshodují všechny trasy, včetně postupu pro použití náhradní lokality|
|d2c.telemetry.egress.invalid|Neplatné zprávy|Počet|Celkem|Počet zpráv nelze doručit z důvodu nekompatibility s koncovým bodem|
|d2c.telemetry.egress.fallback|Zprávy odpovídající podmínku pro použití náhradní lokality|Počet|Celkem|Počet zpráv zapsaných do záložního koncového bodu|
|d2c.endpoints.egress.eventHubs|Doručování zpráv do koncových bodů centra událostí|Počet|Celkem|Počet pokusů, které zprávy se úspěšně zapsaly do koncových bodů centra událostí|
|d2c.endpoints.latency.eventHubs|Zpráva latence pro koncové body centra událostí|Milisekundy|Průměr|Průměrná latence mezi zprávy příchozího přenosu dat do služby IoT hub a příchozího přenosu dat zprávu do koncového bodu centra událostí, v milisekundách|
|d2c.endpoints.egress.serviceBusQueues|Doručování zpráv do fronty služby Service Bus koncových bodů|Počet|Celkem|Počet pokusů, které zprávy se úspěšně zapsaly do koncových bodů frontu služby Service Bus|
|d2c.endpoints.latency.serviceBusQueues|Zpráva latence pro koncové body fronty Service Bus|Milisekundy|Průměr|Průměrná latence mezi zprávy příchozího přenosu dat do služby IoT hub a příchozího přenosu dat zprávy do fronty služby Service Bus koncového bodu v milisekundách|
|d2c.endpoints.egress.serviceBusTopics|Doručování zpráv do koncových bodů služby Service Bus|Počet|Celkem|Počet pokusů, které zprávy se úspěšně zapsaly do koncových bodů služby Service Bus|
|d2c.endpoints.latency.serviceBusTopics|Zpráva latence pro koncové body služby Service Bus|Milisekundy|Průměr|Průměrná latence mezi zprávy příchozího přenosu dat do služby IoT hub a příchozího přenosu dat zprávu do koncového bodu služby Service Bus v milisekundách|
|d2c.endpoints.egress.builtIn.events|Doručování zpráv do integrovaného koncového bodu (zprávy/events)|Počet|Celkem|Počet pokusů, které zprávy se úspěšně zapsaly do integrovaného koncového bodu (zprávy/events)|
|d2c.endpoints.latency.builtIn.events|Zpráva latence pro integrovaný koncový bod (zprávy/events)|Milisekundy|Průměr|Průměrná latence mezi zprávy příchozího přenosu dat do služby IoT hub a příchozího přenosu dat zprávy do integrovaného koncového bodu (zprávy událostí), v milisekundách |
|d2c.twin.read.success|Čtení dvojčat úspěšné ze zařízení|Počet|Celkem|Počet všech úspěšných čtení dvojčat zařízení iniciované.|
|d2c.Twin.Read.failure|Čtení dvojčat ze zařízení se nezdařila|Počet|Celkem|Počet všech se nezdařilo čtení dvojčat zařízení inicioval.|
|d2c.twin.read.size|Velikost odpovědi čtení dvojčat zařízení|B|Průměr|Průměrné, minimální a maximální ze všech úspěšné, zařízení iniciované dvojčete čtení.|
|d2c.twin.update.success|Aktualizace dvojčat úspěšné ze zařízení|Počet|Celkem|Počet všech úspěšných aktualizací dvojčete zařízení iniciované.|
|d2c.Twin.Update.failure|Aktualizace dvojčat ze zařízení s chybami|Počet|Celkem|Počet všech neúspěšných dvojčete zařízení iniciované aktualizací.|
|d2c.twin.update.size|Velikost aktualizace dvojčete zařízení|B|Průměr|Průměrné a minimální a maximální velikost všech úspěšné, zařízení iniciované dvojčete aktualizace.|
|c2d.methods.success|Volání úspěšné přímé metody|Počet|Celkem|Počet všech úspěšných volání přímé metody.|
|c2d.Methods.failure|Přímé metody vyvolání se nezdařilo|Počet|Celkem|Počet všech se nezdařilo volání metody s přímým přístupem.|
|c2d.Methods.requestSize|Žádost o velikosti vyvolání přímé metody|B|Průměr|Průměrné, minimální a maximální počet všech úspěšných požadavků přímé metody.|
|c2d.methods.responseSize|Velikost odpovědi vyvolání přímé metody|B|Průměr|Průměrné, minimální a maximální všechny úspěšné přímé metody odpovědí.|
|c2d.twin.read.success|Čtení dvojčat úspěšné z back-endu|Počet|Celkem|Počet všech úspěšných čtení dvojčat iniciované back-end.|
|c2d.Twin.Read.failure|Čtení dvojčat se nezdařilo z back-endu|Počet|Celkem|Počet všech se nezdařilo čtení dvojčat iniciované back-end.|
|c2d.twin.read.size|Velikost odpovědi čtení dvojčat z back-endu|B|Průměr|Průměr a minimální a maximální velikost všech úspěšné, iniciované back-end dvojčete čtení.|
|c2d.twin.update.success|Aktualizace dvojčat úspěšné z back-endu|Počet|Celkem|Počet všech úspěšné aktualizace dvojčete iniciované back-end.|
|c2d.Twin.Update.failure|Aktualizace dvojčat se nezdařilo z back-endu|Počet|Celkem|Počet všech neúspěšných iniciované back-end dvojčete aktualizací.|
|c2d.twin.update.size|Velikost dvojčete aktualizací z back-endu|B|Průměr|Průměrné, minimální a maximální velikost všech úspěšné, iniciované back-end dvojčete aktualizace.|
|twinQueries.success|Úspěšné dvojčete dotazy|Počet|Celkem|Počet všech úspěšných dvojčete dotazů.|
|twinQueries.failure|Neúspěšné dvojčete dotazy|Počet|Celkem|Počet všech neúspěšných dvojčete dotazů.|
|twinQueries.resultSize|Velikost výsledku dotazy dvojčete|B|Průměr|Průměrné, minimální a maximální velikosti výsledek všech dotazů na dvojčata úspěšné.|
|jobs.createTwinUpdateJob.success|Úspěšné vytvoření úlohy aktualizace dvojčete|Počet|Celkem|Počet všech po úspěšném vytvoření úlohy aktualizace dvojčete.|
|jobs.createTwinUpdateJob.failure|Neúspěšné vytvoření úlohy aktualizace dvojčete|Počet|Celkem|Počet všech neúspěšných vytváření úlohy aktualizace dvojčete.|
|jobs.createDirectMethodJob.success|Úspěšné vytvoření úloh vyvolání – metoda|Počet|Celkem|Počet všech po úspěšném vytvoření úlohy vyvolání přímé metody.|
|jobs.createDirectMethodJob.failure|Neúspěšné vytvoření úloh vyvolání – metoda|Počet|Celkem|Počet všech neúspěšných vytváření úloh vyvolání přímé metody.|
|jobs.listJobs.success|Úspěšné volání do seznamu úloh|Počet|Celkem|Počet všech úspěšných volání do seznamu úloh.|
|jobs.listJobs.failure|Neúspěšná volání do seznamu úloh|Počet|Celkem|Počet všech neúspěšných volání do seznamu úloh.|
|jobs.cancelJob.success|V případě úspěšné úlohy|Počet|Celkem|Počet všech úspěšných volání zrušit úlohu.|
|jobs.cancelJob.failure|Zrušení úlohy, která selhala|Počet|Celkem|Počet všech neúspěšných volání zrušit úlohu.|
|jobs.queryJobs.success|Úspěšné úlohy dotazů|Počet|Celkem|Počet všech úspěšných volání do úlohy dotaz.|
|jobs.queryJobs.failure|Neúspěšné úlohy dotazy|Počet|Celkem|Počet všech neúspěšných volání do úlohy dotaz.|
|jobs.completed|Dokončené úlohy|Počet|Celkem|Počet všech dokončených úloh.|
|jobs.Failed|Neúspěšné úlohy|Počet|Celkem|Počet všech neúspěšných úloh.|

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
