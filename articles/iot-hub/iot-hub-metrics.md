---
title: Monitorování IoT Hub Azure pomocí metriky | Microsoft Docs
description: Jak používat metriky Azure IoT Hub k vyhodnocení a monitorování celkového stavu vašich Center IoT Hub.
author: jlian
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/24/2019
ms.author: jlian
ms.openlocfilehash: f0bcf12a43a4732b371dd2d64c0b174a0087bea9
ms.sourcegitcommit: cd70273f0845cd39b435bd5978ca0df4ac4d7b2c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/18/2019
ms.locfileid: "71098939"
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

|Metrika|Metriky zobrazovaný název|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|d2c<br>. telemetrie<br>příchozího přenosu dat.<br>allProtocol|Počet pokusů o odeslání zprávy telemetrie|Count|Celkem|Počet zpráv typu zařízení-Cloud telemetrie, které se pokusily o odeslání do služby IoT Hub|Žádné dimenze|
|d2c<br>. telemetrie<br>. příchozí<br>. úspěch|Odeslané zprávy telemetrie|Count|Celkem|Počet zpráv telemetrie typu zařízení-Cloud, které se úspěšně odeslaly do služby IoT Hub|Žádné dimenze|
|c2d<br>. příkazy<br>. odchozí<br>. dokončeno<br>. úspěch|Dokončené příkazy|Count|Celkem|Počet příkazů typu cloud-zařízení úspěšně dokončených zařízením|Žádné dimenze|
|c2d<br>. příkazy<br>. odchozí<br>. Abandon<br>. úspěch|Zrušené příkazy|Count|Celkem|Počet příkazů typu cloud-zařízení, které zařízení opustilo|Žádné dimenze|
|c2d<br>. příkazy<br>. odchozí<br>. zamítnout<br>. úspěch|Odmítnuté příkazy|Count|Celkem|Počet příkazů typu cloud-zařízení odmítnutých zařízením|Žádné dimenze|
|zařízení<br>.totalDevices|Celkem zařízení (zastaralé)|Count|Celkem|Počet zařízení zaregistrovaných ve službě IoT Hub|Žádné dimenze|
|zařízení<br>.connectedDevices<br>.allProtocol|Připojená zařízení (zastaralé) |Count|Celkem|Počet zařízení připojených ke službě IoT Hub|Žádné dimenze|
|d2c<br>. telemetrie<br>. odchozí<br>. úspěch|Směrování: doručené zprávy telemetrie|Count|Celkem|Počet pokusů o úspěšné doručení zpráv do všech koncových bodů pomocí směrování IoT Hub. Pokud je zpráva směrována do více koncových bodů, tato hodnota se u každého úspěšného doručení zvyšuje o jednu. Pokud se zpráva doručí do stejného koncového bodu víckrát, tato hodnota se u každého úspěšného doručení zvyšuje o jednu.|Žádné dimenze|
|d2c<br>. telemetrie<br>. odchozí<br>. vyřazeno|Směrování: vyřazené zprávy telemetrie |Count|Celkem|Počet pokusů o vyřazení zpráv IoT Hub směrování z důvodu nedoručených koncových bodů. Tato hodnota nepočítá počet zpráv doručených do záložní trasy, protože se tam nedoručují vyřazené zprávy.|Žádné dimenze|
|d2c<br>. telemetrie<br>. odchozí<br>. osamocený|Směrování: osamocené zprávy telemetrie |Count|Celkem|Počet, kolikrát byly zprávy osamocené IoT Hub směrování, protože neodpovídaly pravidlům směrování (včetně záložního pravidla). |Žádné dimenze|
|d2c<br>. telemetrie<br>. odchozí<br>. neplatné|Směrování: nekompatibilní zprávy telemetrie|Count|Celkem|Počet, kolikrát IoT Hub směrování nedokázala doručovat zprávy z důvodu nekompatibility s koncovým bodem. Tato hodnota nezahrnuje opakované pokusy.|Žádné dimenze|
|d2c<br>. telemetrie<br>. odchozí<br>. Fallback|Směrování: zprávy doručené do záložního režimu|Count|Celkem|Počet, kolikrát IoT Hub směrování doručených zpráv do koncového bodu přidruženého k záložní trase.|Žádné dimenze|
|d2c<br>. koncové body<br>. odchozí<br>.eventHubs|Směrování: zprávy doručené do centra událostí|Count|Celkem|Počet, kolikrát IoT Hub směrování úspěšně doručuje zprávy do koncových bodů centra událostí.|Žádné dimenze|
|d2c<br>. koncové body<br>. latence<br>.eventHubs|Směrování: latence zprávy pro centrum událostí|Milisekundy|Average|Průměrná latence (v milisekundách) mezi příchozími a příchozími zprávami IoT Hub a příchozími zprávami do koncového bodu centra událostí.|Žádné dimenze|
|d2c<br>. koncové body<br>. odchozí<br>.serviceBusQueues|Směrování: zprávy doručené do fronty Service Bus|Count|Celkem|Počet, kolikrát IoT Hub směrování úspěšně doručuje zprávy do koncových bodů fronty Service Bus|Žádné dimenze|
|d2c<br>. koncové body<br>. latence<br>.serviceBusQueues|Směrování: latence zprávy pro Service Bus frontu|Milisekundy|Average|Průměrná latence (v milisekundách) mezi vstupem zprávy a IoT Hub a zpráv telemetrie do koncového bodu fronty Service Bus|Žádné dimenze|
|d2c<br>. koncové body<br>. odchozí<br>.serviceBusTopics|Směrování: zprávy doručené do Service Bus tématu|Count|Celkem|Počet, kolikrát IoT Hub směrování úspěšně doručuje zprávy do koncových bodů tématu Service Bus.|Žádné dimenze|
|d2c<br>. koncové body<br>. latence<br>.serviceBusTopics|Směrování: latence zprávy pro Service Bus téma|Milisekundy|Average|Průměrná latence (v milisekundách) mezi vstupem zprávy a IoT Hub a zpráv telemetrie do koncového bodu Service Bus tématu.|Žádné dimenze|
|d2c<br>. koncové body<br>. odchozí<br>. builtIn<br>události. events|Směrování: zprávy doručené zprávám/událostem|Count|Celkem|Počet, kolikrát IoT Hub směrování úspěšně doručuje zprávy do integrovaného koncového bodu (zprávy/události). Tato metrika začne pracovat pouze v případě, že https://aka.ms/iotrouting) je povoleno směrování (pro službu IoT Hub.|Žádné dimenze|
|d2c<br>. koncové body<br>. latence<br>. builtIn. events|Směrování: latence zpráv pro zprávy/události|Milisekundy|Average|Průměrná latence (v milisekundách) mezi vstupem zprávy a IoT Hub a zprávou telemetrie do integrovaného koncového bodu (zprávy/události) Tato metrika začne pracovat pouze v případě, že https://aka.ms/iotrouting) je povoleno směrování (pro službu IoT Hub.|Žádné dimenze|
|d2c<br>. koncové body<br>. odchozí<br>. Storage|Směrování: zprávy doručené do úložiště|Count|Celkem|Počet pokusů, IoT Hub směrování úspěšně doručuje zprávy do koncových bodů úložiště.|Žádné dimenze|
|d2c<br>. koncové body<br>. latence<br>. Storage|Směrování: latence zpráv pro úložiště|Milisekundy|Average|Průměrná latence (v milisekundách) mezi příchozími a příchozími zprávami pro IoT Hub a zprávy telemetrie do koncového bodu úložiště|Žádné dimenze|
|d2c<br>. koncové body<br>. odchozí<br>. Storage<br>. bajtů|Směrování: data Doručená do úložiště|B|Celkem|Množství dat (v bajtech) IoT Hub směrování doručené koncovým bodům úložiště.|Žádné dimenze|
|d2c<br>. koncové body<br>. odchozí<br>. Storage<br>objekty blob|Směrování: objekty blob doručené do úložiště|Count|Celkem|Počet pokusů IoT Hub směrování dodaných objektů blob do koncových bodů úložiště.|Žádné dimenze|
|EventGridDeliveries|Event Grid doručení (Preview)|Count|Celkem|Počet událostí IoT Hub publikovaných do Event Grid Použijte výslednou dimenzi pro počet úspěšných a neúspěšných žádostí. Dimenze EventType zobrazuje typ události (https://aka.ms/ioteventgrid). Chcete-li zobrazit, odkud žádosti pocházejí, použijte dimenzi EventType.|Výsledek, EventType|
|EventGridLatency|Latence Event Grid (Preview)|Milisekundy|Average|Průměrná latence (v milisekundách) od okamžiku, kdy byla událost centra IoT vygenerována při publikování události do Event Grid. Toto číslo je průměrem mezi všemi typy událostí. Použijte dimenzi EventType k zobrazení latence konkrétního typu události.|Typ události|
|d2c<br>.twin<br>. Read<br>. úspěch|Úspěšné čtení ze zařízení|Count|Celkem|Počet všech úspěšných zdvojených čtení iniciované zařízením|Žádné dimenze|
|d2c<br>.twin<br>. Read<br>. Chyba|Neúspěšná čtení ze zařízení|Count|Celkem|Počet všech neúspěšných čtení, která byla iniciována zařízením.|Žádné dimenze|
|d2c<br>.twin<br>. Read<br>. Size|Velikost odpovědi u dvojitých čtení ze zařízení|B|Average|Průměrná, minimální a maximální hodnota všech úspěšných zdvojených čtení iniciované zařízením|Žádné dimenze|
|d2c<br>.twin<br>. Update<br>. úspěch|Úspěšné nedokončené změny ze zařízení|Count|Celkem|Počet všech úspěšných nevyžádaných aktualizací iniciované zařízením|Žádné dimenze|
|d2c<br>.twin<br>. Update<br>. Chyba|Neúspěšné aktualizace ze zařízení se zdvojenými chybami|Count|Celkem|Počet všech neúspěšných aktualizací inicializovaných zařízením.|Žádné dimenze|
|d2c<br>.twin<br>. Update<br>. Size|Velikost dvojitě aktualizovaných aktualizací ze zařízení|B|Average|Průměrná, minimální a maximální velikost všech úspěšných nevyžádaných aktualizací iniciované zařízením|Žádné dimenze|
|c2d<br>. Methods<br>. úspěch|Úspěšná volání přímé metody|Count|Celkem|Počet všech úspěšných volání přímé metody.|Žádné dimenze|
|c2d<br>. Methods<br>. Chyba|Neúspěšná volání přímé metody|Count|Celkem|Počet všech neúspěšných volání metody Direct|Žádné dimenze|
|c2d<br>. Methods<br>.requestSize|Velikost žádosti o vyvolání přímé metody|B|Average|Průměrně, minimální a maximální počet všech úspěšných požadavků na metodu Direct.|Žádné dimenze|
|c2d<br>. Methods<br>.responseSize|Velikost odezvy volání přímých metod|B|Average|Průměrných, minimálních a maximálních odpovědí všech úspěšných přímých metod.|Žádné dimenze|
|c2d<br>.twin<br>. Read<br>. úspěch|Úspěšné zdvojené čtení z back-endu|Count|Celkem|Počet všech úspěšných zdvojených čtení spouštěných back-endu. Tento počet nezahrnuje zdvojené čtení iniciované z nevyžádaných dotazů.|Žádné dimenze|
|c2d<br>.twin<br>. Read<br>. Chyba|Neúspěšné čtení z back-endu ze zadních vláken|Count|Celkem|Počet všech neúspěšných čtení, která byla inicializována back-endu.|Žádné dimenze|
|c2d<br>.twin<br>. Read<br>. Size|Velikost odpovědi zdvojeného čtení z back-endu|B|Average|Průměrně, minimální a maximální počet všech úspěšných zdvojených čtení, které byly spuštěny na konci.|Žádné dimenze|
|c2d<br>.twin<br>. Update<br>. úspěch|Úspěšné zdvojené aktualizace z back-endu|Count|Celkem|Počet všech úspěšných dokončených aktualizací, které jsou úspěšně spouštěny po konci.|Žádné dimenze|
|c2d<br>.twin<br>. Update<br>. Chyba|Neúspěšné zdvojené aktualizace z back-endu|Count|Celkem|Počet všech neúspěšných provolaných aktualizací, které selhaly po back-endu.|Žádné dimenze|
|c2d<br>.twin<br>. Update<br>. Size|Velikost dvojitě aktualizovaných aktualizací z back-endu|B|Average|Průměrná, minimální a maximální velikost všech úspěšných kompletních aktualizací spuštěných na konci.|Žádné dimenze|
|twinQueries<br>. úspěch|Úspěšné zdvojené dotazy|Count|Celkem|Počet všech úspěšných vlákenných dotazů.|Žádné dimenze|
|twinQueries<br>. Chyba|Neúspěšné zdvojené dotazy|Count|Celkem|Počet všech neúspěšných dotazů, které selhaly.|Žádné dimenze|
|twinQueries<br>.resultSize|Velikost výsledku nevlákenných dotazů|B|Average|Průměrnou, minimální a maximální velikost výsledku všech úspěšných vlákenných dotazů.|Žádné dimenze|
|jobs<br>.createTwinUpdateJob<br>. úspěch|Úspěšné vytváření zdvojených úloh aktualizace|Count|Celkem|Počet všech úspěšných vytvoření zdvojených úloh aktualizace.|Žádné dimenze|
|jobs<br>.createTwinUpdateJob<br>. Chyba|Nepovedlo se vytvořit úlohy s dvojitou aktualizací|Count|Celkem|Počet všech neúspěšných vytvoření zdvojených úloh aktualizace.|Žádné dimenze|
|jobs<br>.createDirectMethodJob<br>. úspěch|Úspěšné vytváření úloh vyvolání metod|Count|Celkem|Počet všech úspěšných vytvoření úloh vyvolání přímé metody.|Žádné dimenze|
|jobs<br>.createDirectMethodJob<br>. Chyba|Nepovedlo se vytvořit úlohy vyvolání metody|Count|Celkem|Počet všech neúspěšných vytvoření úloh volání přímé metody.|Žádné dimenze|
|jobs<br>.listJobs<br>. úspěch|Úspěšná volání na seznam úloh|Count|Celkem|Počet všech úspěšných volání pro výpis úloh.|Žádné dimenze|
|jobs<br>.listJobs<br>. Chyba|Neúspěšná volání pro výpis úloh|Count|Celkem|Počet všech neúspěšných volání pro výpis úloh|Žádné dimenze|
|jobs<br>.cancelJob<br>. úspěch|Úspěšná zrušení úlohy|Count|Celkem|Počet všech úspěšných volání pro zrušení úlohy.|Žádné dimenze|
|jobs<br>.cancelJob<br>. Chyba|Neúspěšná zrušení úloh|Count|Celkem|Počet všech neúspěšných volání pro zrušení úlohy.|Žádné dimenze|
|jobs<br>.queryJobs<br>. úspěch|Úspěšné dotazy na úlohy|Count|Celkem|Počet všech úspěšných volání úloh dotazů.|Žádné dimenze|
|jobs<br>.queryJobs<br>. Chyba|Neúspěšné dotazy na úlohy|Count|Celkem|Počet všech neúspěšných volání pro úlohy dotazu.|Žádné dimenze|
|jobs<br>. dokončeno|Dokončené úlohy|Count|Celkem|Počet všech dokončených úloh.|Žádné dimenze|
|jobs<br>. Chyba|Neúspěšné úlohy|Count|Celkem|Počet všech neúspěšných úloh.|Žádné dimenze|
|d2c<br>. telemetrie<br>. příchozí<br>.sendThrottle|Počet chyb omezování|Count|Celkem|Počet chyb omezení z důvodu omezení propustnosti zařízení|Žádné dimenze|
|dailyMessage<br>QuotaUsed|Celkový počet použitých zpráv|Count|Average|Počet všech aktuálně využívaných zpráv Jedná se o kumulativní hodnotu, která se každý den resetuje na nulu v 00:00 UTC.|Žádné dimenze|
|deviceDataUsage|Celkové využití dat zařízení|B|Celkem|Bajtů přenesených do a ze všech zařízení připojených k IotHub|Žádné dimenze|
|totalDeviceCount|Celkem zařízení (Preview)|Count|Average|Počet zařízení zaregistrovaných ve službě IoT Hub|Žádné dimenze|
|Připojen<br>Počet zařízení|Připojená zařízení (Preview)|Count|Average|Počet zařízení připojených ke službě IoT Hub|Žádné dimenze|
|Konfiguračních|Metriky konfigurace|Count|Celkem|Metriky pro operace konfigurace|Žádné dimenze|

## <a name="next-steps"></a>Další postup

Teď, když jste se seznámili s přehledem IoT Hub metriky, získáte další informace o správě Azure IoT Hub pomocí tohoto odkazu:

* [Monitorování operací](iot-hub-operations-monitoring.md)

Chcete-li dále prozkoumat možnosti IoT Hub, přečtěte si:

* [IoT Hub příručka pro vývojáře](iot-hub-devguide.md)

* [Nasazení AI do hraničních zařízení pomocí Azure IoT Edge](../iot-edge/tutorial-simulate-device-linux.md)
