---
title: Monitorování IoT Hub Azure pomocí metriky | Microsoft Docs
description: Jak používat metriky Azure IoT Hub k vyhodnocení a monitorování celkového stavu vašich Center IoT Hub.
author: jlian
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/24/2019
ms.author: jlian
ms.openlocfilehash: ec8a00460b4a750339f929eb6879ac6eb63cac8e
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2020
ms.locfileid: "78396466"
---
# <a name="understand-iot-hub-metrics"></a>Pochopení IoT Hub metriky

IoT Hub metriky poskytují lepší údaje o stavu prostředků Azure IoT ve vašem předplatném Azure. IoT Hub metriky umožňují posoudit celkový stav služby IoT Hub a zařízení, která jsou k ní připojená. Uživatelsky orientované statistiky jsou důležité, protože vám pomůžou zjistit, co se chystá ve službě IoT Hub, a problémy s hlavními příčinami, aniž byste museli kontaktovat podporu Azure.

Metriky jsou ve výchozím nastavení povolené. IoT Hub metriky můžete zobrazit z Azure Portal.

> [!NOTE]
> IoT Hub metriky můžete použít k zobrazení informací o zařízeních IoT technologie Plug and Play připojených k vašemu IoT Hub. Zařízení IoT technologie Plug and Play jsou součástí [iot technologie Plug and Play Public Preview](../iot-pnp/overview-iot-plug-and-play.md).

## <a name="how-to-view-iot-hub-metrics"></a>Jak zobrazit metriky IoT Hub

1. Vytvořte centrum IoT. Pokyny k vytvoření služby IoT Hub najdete v tématu [odeslání telemetrie ze zařízení do průvodce IoT Hub](quickstart-send-telemetry-dotnet.md) .

2. Otevřete okno centra IoT. Odtud klikněte na **metriky**.
   
    ![Snímek obrazovky s možností metriky na IoT Hub stránce prostředků](./media/iot-hub-metrics/enable-metrics-1.png)

3. V okně metriky můžete zobrazit metriky pro Centrum IoT a vytvářet vlastní zobrazení metrik. 
   
    ![Snímek obrazovky zobrazující stránku metriky pro IoT Hub](./media/iot-hub-metrics/enable-metrics-2.png)
    
4. Data metriky můžete odeslat do Event Hubsho koncového bodu nebo účtu Azure Storage kliknutím na **nastavení diagnostiky**a pak na **Přidat nastavení diagnostiky** .

   ![Snímek obrazovky se zobrazeným tlačítkem pro nastavení diagnostiky](./media/iot-hub-metrics/enable-metrics-3.png)

## <a name="iot-hub-metrics-and-how-to-use-them"></a>IoT Hub metriky a jejich použití

IoT Hub poskytuje několik metrik, které vám poskytnou přehled o stavu vašeho centra a celkový počet připojených zařízení. Můžete zkombinovat informace z několika metrik a vykreslit větší přehled o stavu služby IoT Hub. Následující tabulka popisuje metriky jednotlivých sledování IoT Hub a informace o tom, jak se každá metrika vztahuje k celkovému stavu centra IoT.

|Metrika|Metriky zobrazovaný název|Jednotka|Typ agregace|Popis|Rozměry|
|---|---|---|---|---|---|
|d2c.telemetry.ingress.allProtocol|Počet pokusů o odeslání zprávy telemetrie|Počet|Celkem|Počet zpráv typu zařízení-Cloud telemetrie, které se pokusily o odeslání do služby IoT Hub|Žádné|
|d2c.telemetry.ingress.success|Odeslané zprávy telemetrie|Počet|Celkem|Počet zpráv telemetrie typu zařízení-Cloud, které se úspěšně odeslaly do služby IoT Hub|Žádné|
|c2d.commands.egress.complete.success|Doručení zpráv C2D bylo dokončeno.|Počet|Celkem|Počet úspěšně dokončených doručení zpráv typu cloud-zařízení do zařízení|Žádné|
|c2d.commands.egress.abandon.success|Zrušené zprávy C2D|Počet|Celkem|Počet zpráv typu cloud-zařízení opuštěných zařízením|Žádné|
|c2d.commands.egress.reject.success|Odmítnuté zprávy C2D|Počet|Celkem|Počet zpráv typu cloud-zařízení odmítnutých zařízením|Žádné|
|C2DMessagesExpired|C2D zprávy prošly (Preview)|Počet|Celkem|Počet zpráv typu cloud-zařízení, jejichž platnost vypršela|Žádné|
|devices.totalDevices|Celkem zařízení (zastaralé)|Počet|Celkem|Počet zařízení zaregistrovaných ve službě IoT Hub|Žádné|
|devices.connectedDevices.allProtocol|Připojená zařízení (zastaralé) |Počet|Celkem|Počet zařízení připojených ke službě IoT Hub|Žádné|
|d2c.telemetry.egress.success|Směrování: doručené zprávy telemetrie|Počet|Celkem|Počet pokusů o úspěšné doručení zpráv do všech koncových bodů pomocí směrování IoT Hub. Pokud je zpráva směrována do více koncových bodů, tato hodnota se u každého úspěšného doručení zvyšuje o jednu. Pokud se zpráva doručí do stejného koncového bodu víckrát, tato hodnota se u každého úspěšného doručení zvyšuje o jednu.|Žádné|
|d2c.telemetry.egress.dropped|Směrování: vyřazené zprávy telemetrie |Počet|Celkem|Počet pokusů o vyřazení zpráv IoT Hub směrování z důvodu nedoručených koncových bodů. Tato hodnota nepočítá počet zpráv doručených do záložní trasy, protože se tam nedoručují vyřazené zprávy.|Žádné|
|d2c.telemetry.egress.orphaned|Směrování: osamocené zprávy telemetrie |Počet|Celkem|Počet, kolikrát byly zprávy osamocené IoT Hub směrování, protože neodpovídaly pravidlům směrování (včetně záložního pravidla). |Žádné|
|d2c.telemetry.egress.invalid|Směrování: nekompatibilní zprávy telemetrie|Počet|Celkem|Počet, kolikrát IoT Hub směrování nedokázala doručovat zprávy z důvodu nekompatibility s koncovým bodem. Tato hodnota nezahrnuje opakované pokusy.|Žádné|
|d2c.telemetry.egress.fallback|Směrování: zprávy doručené do záložního režimu|Počet|Celkem|Počet, kolikrát IoT Hub směrování doručených zpráv do koncového bodu přidruženého k záložní trase.|Žádné|
|d2c.endpoints.egress.eventHubs|Směrování: zprávy doručené do centra událostí|Počet|Celkem|Počet, kolikrát IoT Hub směrování úspěšně doručuje zprávy do koncových bodů centra událostí.|Žádné|
|d2c.endpoints.latency.eventHubs|Směrování: latence zprávy pro centrum událostí|Milisekundy|Průměr|Průměrná latence (v milisekundách) mezi příchozími a příchozími zprávami IoT Hub a příchozími zprávami do koncového bodu centra událostí.|Žádné|
|d2c.endpoints.egress.serviceBusQueues|Směrování: zprávy doručené do fronty Service Bus|Počet|Celkem|Počet, kolikrát IoT Hub směrování úspěšně doručuje zprávy do koncových bodů fronty Service Bus|Žádné|
|d2c.endpoints.latency.serviceBusQueues|Směrování: latence zprávy pro Service Bus frontu|Milisekundy|Průměr|Průměrná latence (v milisekundách) mezi vstupem zprávy a IoT Hub a zpráv telemetrie do koncového bodu fronty Service Bus|Žádné|
|d2c.endpoints.egress.serviceBusTopics|Směrování: zprávy doručené do Service Bus tématu|Počet|Celkem|Počet, kolikrát IoT Hub směrování úspěšně doručuje zprávy do koncových bodů tématu Service Bus.|Žádné|
|d2c.endpoints.latency.serviceBusTopics|Směrování: latence zprávy pro Service Bus téma|Milisekundy|Průměr|Průměrná latence (v milisekundách) mezi vstupem zprávy a IoT Hub a zpráv telemetrie do koncového bodu Service Bus tématu.|Žádné|
|d2c.endpoints.egress.builtIn.events|Směrování: zprávy doručené zprávám/událostem|Počet|Celkem|Počet, kolikrát IoT Hub směrování úspěšně doručuje zprávy do integrovaného koncového bodu (zprávy/události).|Žádné|
|d2c.endpoints.latency.builtIn.events|Směrování: latence zpráv pro zprávy/události|Milisekundy|Průměr|Průměrná latence (v milisekundách) mezi vstupem zprávy a IoT Hub a zprávou telemetrie do integrovaného koncového bodu (zprávy/události)|Žádné|
|d2c.endpoints.egress.storage|Směrování: zprávy doručené do úložiště|Počet|Celkem|Počet pokusů, IoT Hub směrování úspěšně doručuje zprávy do koncových bodů úložiště.|Žádné|
|D2C. Endpoints. latence. Storage|Směrování: latence zpráv pro úložiště|Milisekundy|Průměr|Průměrná latence (v milisekundách) mezi příchozími a příchozími zprávami pro IoT Hub a zprávy telemetrie do koncového bodu úložiště|Žádné|
|d2c.endpoints.egress.storage.bytes|Směrování: data Doručená do úložiště|Bajty|Celkem|Množství dat (v bajtech) IoT Hub směrování doručené koncovým bodům úložiště.|Žádné|
|d2c.endpoints.egress.storage.blobs|Směrování: objekty blob doručené do úložiště|Počet|Celkem|Počet pokusů IoT Hub směrování dodaných objektů blob do koncových bodů úložiště.|Žádné|
|EventGridDeliveries|Event Grid doručení (Preview)|Počet|Celkem|Počet událostí IoT Hub publikovaných do Event Grid Použijte výslednou dimenzi pro počet úspěšných a neúspěšných žádostí. Dimenze EventType zobrazuje typ události (https://aka.ms/ioteventgrid).|Prostředku<br/>Vyústit<br/>EventType|
|EventGridLatency|Latence Event Grid (Preview)|Milisekundy|Průměr|Průměrná latence (v milisekundách) od okamžiku, kdy byla událost centra IoT vygenerována při publikování události do Event Grid. Toto číslo je průměrem mezi všemi typy událostí. Použijte dimenzi EventType k zobrazení latence konkrétního typu události.|Prostředku<br/>EventType|
|d2c.twin.read.success|Úspěšné čtení ze zařízení|Počet|Celkem|Počet všech úspěšných zdvojených čtení iniciované zařízením|Žádné|
|d2c.twin.read.failure|Neúspěšná čtení ze zařízení|Počet|Celkem|Počet všech neúspěšných čtení, která byla iniciována zařízením.|Žádné|
|d2c.twin.read.size|Velikost odpovědi u dvojitých čtení ze zařízení|Bajty|Průměr|Průměrná, minimální a maximální hodnota všech úspěšných zdvojených čtení iniciované zařízením|Žádné|
|d2c.twin.update.success|Úspěšné nedokončené změny ze zařízení|Počet|Celkem|Počet všech úspěšných nevyžádaných aktualizací iniciované zařízením|Žádné|
|d2c.twin.update.failure|Neúspěšné aktualizace ze zařízení se zdvojenými chybami|Počet|Celkem|Počet všech neúspěšných aktualizací inicializovaných zařízením.|Žádné|
|d2c.twin.update.size|Velikost dvojitě aktualizovaných aktualizací ze zařízení|Bajty|Průměr|Průměrná, minimální a maximální velikost všech úspěšných nevyžádaných aktualizací iniciované zařízením|Žádné|
|c2d.methods.success|Úspěšná volání přímé metody|Počet|Celkem|Počet všech úspěšných volání přímé metody.|Žádné|
|C2D. Methods. Failure|Neúspěšná volání přímé metody|Počet|Celkem|Počet všech neúspěšných volání metody Direct|Žádné|
|c2d.methods.requestSize|Velikost žádosti o vyvolání přímé metody|Bajty|Průměr|Průměrně, minimální a maximální počet všech úspěšných požadavků na metodu Direct.|Žádné|
|c2d.methods.responseSize|Velikost odezvy volání přímých metod|Bajty|Průměr|Průměrných, minimálních a maximálních odpovědí všech úspěšných přímých metod.|Žádné|
|c2d.twin.read.success|Úspěšné zdvojené čtení z back-endu|Počet|Celkem|Počet všech úspěšných zdvojených čtení spouštěných back-endu.|Žádné|
|c2d.twin.read.failure|Neúspěšné čtení z back-endu ze zadních vláken|Počet|Celkem|Počet všech neúspěšných čtení, která byla inicializována back-endu.|Žádné|
|c2d.twin.read.size|Velikost odpovědi zdvojeného čtení z back-endu|Bajty|Průměr|Průměrně, minimální a maximální počet všech úspěšných zdvojených čtení, které byly spuštěny na konci.|Žádné|
|c2d.twin.update.success|Úspěšné zdvojené aktualizace z back-endu|Počet|Celkem|Počet všech úspěšných dokončených aktualizací, které jsou úspěšně spouštěny po konci.|Žádné|
|c2d.twin.update.failure|Neúspěšné zdvojené aktualizace z back-endu|Počet|Celkem|Počet všech neúspěšných provolaných aktualizací, které selhaly po back-endu.|Žádné|
|c2d.twin.update.size|Velikost dvojitě aktualizovaných aktualizací z back-endu|Bajty|Průměr|Průměrná, minimální a maximální velikost všech úspěšných kompletních aktualizací spuštěných na konci.|Žádné|
|twinQueries.success|Úspěšné zdvojené dotazy|Počet|Celkem|Počet všech úspěšných vlákenných dotazů.|Žádné|
|twinQueries.failure|Neúspěšné zdvojené dotazy|Počet|Celkem|Počet všech neúspěšných dotazů, které selhaly.|Žádné|
|twinQueries.resultSize|Velikost výsledku nevlákenných dotazů|Bajty|Průměr|Průměrnou, minimální a maximální velikost výsledku všech úspěšných vlákenných dotazů.|Žádné|
|jobs.createTwinUpdateJob.success|Úspěšné vytváření zdvojených úloh aktualizace|Počet|Celkem|Počet všech úspěšných vytvoření zdvojených úloh aktualizace.|Žádné|
|jobs.createTwinUpdateJob.failure|Nepovedlo se vytvořit úlohy s dvojitou aktualizací|Počet|Celkem|Počet všech neúspěšných vytvoření zdvojených úloh aktualizace.|Žádné|
|jobs.createDirectMethodJob.success|Úspěšné vytváření úloh vyvolání metod|Počet|Celkem|Počet všech úspěšných vytvoření úloh vyvolání přímé metody.|Žádné|
|jobs.createDirectMethodJob.failure|Nepovedlo se vytvořit úlohy vyvolání metody|Počet|Celkem|Počet všech neúspěšných vytvoření úloh volání přímé metody.|Žádné|
|jobs.listJobs.success|Úspěšná volání na seznam úloh|Počet|Celkem|Počet všech úspěšných volání pro výpis úloh.|Žádné|
|jobs.listJobs.failure|Neúspěšná volání pro výpis úloh|Počet|Celkem|Počet všech neúspěšných volání pro výpis úloh|Žádné|
|jobs.cancelJob.success|Úspěšná zrušení úlohy|Počet|Celkem|Počet všech úspěšných volání pro zrušení úlohy.|Žádné|
|jobs.cancelJob.failure|Neúspěšná zrušení úloh|Počet|Celkem|Počet všech neúspěšných volání pro zrušení úlohy.|Žádné|
|jobs.queryJobs.success|Úspěšné dotazy na úlohy|Počet|Celkem|Počet všech úspěšných volání úloh dotazů.|Žádné|
|jobs.queryJobs.failure|Neúspěšné dotazy na úlohy|Počet|Celkem|Počet všech neúspěšných volání pro úlohy dotazu.|Žádné|
|dokončené úlohy|Dokončené úlohy|Počet|Celkem|Počet všech dokončených úloh.|Žádné|
|úlohy. nezdařilo se|Neúspěšné úlohy|Počet|Celkem|Počet všech neúspěšných úloh.|Žádné|
|d2c.telemetry.ingress.sendThrottle|Počet chyb omezování|Počet|Celkem|Počet chyb omezení z důvodu omezení propustnosti zařízení|Žádné|
|dailyMessageQuotaUsed|Celkový počet použitých zpráv|Počet|Průměr|Počet všech aktuálně využívaných zpráv Jedná se o kumulativní hodnotu, která se každý den resetuje na nulu v 00:00 UTC.|Žádné|
|deviceDataUsage|Celkové využití dat zařízení|Bajty|Celkem|Bajtů přenesených do a ze všech zařízení připojených k IotHub|Žádné|
|deviceDataUsageV2|Celkové využití dat zařízení (Preview)|Bajty|Celkem|Bajtů přenesených do a ze všech zařízení připojených k IotHub|Žádné|
|totalDeviceCount|Celkem zařízení (Preview)|Počet|Průměr|Počet zařízení zaregistrovaných ve službě IoT Hub|Žádné|
|connectedDeviceCount|Připojená zařízení (Preview)|Počet|Průměr|Počet zařízení připojených ke službě IoT Hub|Žádné|
|konfigurace|Metriky konfigurace|Počet|Celkem|Metriky pro operace konfigurace|Žádné|

## <a name="next-steps"></a>Další kroky

Teď, když jste se seznámili s přehledem IoT Hub metriky, získáte další informace o správě Azure IoT Hub pomocí tohoto odkazu:

* [Nastavení diagnostických protokolů](iot-hub-monitor-resource-health.md)

Chcete-li dále prozkoumat možnosti IoT Hub, přečtěte si:

* [IoT Hub příručka pro vývojáře](iot-hub-devguide.md)

* [Nasazení AI do hraničních zařízení pomocí Azure IoT Edge](../iot-edge/tutorial-simulate-device-linux.md)
