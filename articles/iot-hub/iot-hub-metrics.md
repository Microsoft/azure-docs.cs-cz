---
title: Monitorování IoT Hub Azure pomocí metriky | Microsoft Docs
description: Jak používat metriky Azure IoT Hub k vyhodnocení a monitorování celkového stavu vašich Center IoT Hub.
author: jlian
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/24/2019
ms.author: jlian
ms.openlocfilehash: 808320f89c4dbeca835fc5a710ea1566199f6884
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "84791839"
---
<!--for build: for each metric, if you understand what it is, it's ok. otw add more info.  -->
# <a name="understand-iot-hub-metrics"></a>Pochopení IoT Hub metriky

IoT Hub metriky vám poskytnou informace o stavu prostředků Azure IoT ve vašem předplatném Azure. IoT Hub metriky vám pomůžou vyhodnotit celkový stav služby IoT Hub a zařízení, která jsou k ní připojená. Tyto uživatelsky orientované statistiky vám pomůžou zjistit, co se chystá ve službě IoT Hub, a pomáhat s analýzou příčin problémů, aniž byste museli kontaktovat podporu Azure.

Metriky jsou ve výchozím nastavení povolené. IoT Hub metriky můžete zobrazit z Azure Portal.

> [!NOTE]
> IoT Hub metriky můžete použít k zobrazení informací o zařízeních IoT technologie Plug and Play připojených k vašemu IoT Hub. Zařízení IoT technologie Plug and Play jsou součástí [iot technologie Plug and Play Public Preview](../iot-pnp/overview-iot-plug-and-play.md).

## <a name="how-to-view-iot-hub-metrics"></a>Jak zobrazit metriky IoT Hub

1. Vytvořte centrum IoT. Pokyny k vytvoření služby IoT Hub najdete v tématu [odeslání telemetrie ze zařízení do průvodce IoT Hub](quickstart-send-telemetry-dotnet.md) .

2. Otevřete okno centra IoT. Odtud klikněte na **metriky**.
   
    ![Snímek obrazovky s možností metriky na IoT Hub stránce prostředků](./media/iot-hub-metrics/enable-metrics-1.png)

3. V okně metriky můžete zobrazit metriky pro Centrum IoT a vytvářet vlastní zobrazení metrik. 
   
    ![Snímek obrazovky zobrazující stránku metriky pro IoT Hub](./media/iot-hub-metrics/enable-metrics-2.png)
    
4. Data metriky můžete odeslat do Event Hubsho koncového bodu nebo účtu Azure Storage kliknutím na **nastavení diagnostiky**a pak na **Přidat nastavení diagnostiky**.

   ![Snímek obrazovky se zobrazeným tlačítkem pro nastavení diagnostiky](./media/iot-hub-metrics/enable-metrics-3.png)

## <a name="iot-hub-metrics-and-how-to-use-them"></a>IoT Hub metriky a jejich použití

IoT Hub poskytuje několik metrik, které vám poskytnou přehled o stavu vašeho centra a celkový počet připojených zařízení. Můžete zkombinovat informace z několika metrik a vykreslit větší přehled o stavu služby IoT Hub. Následující tabulka popisuje metriky jednotlivých sledování IoT Hub a informace o tom, jak se každá metrika vztahuje k celkovému stavu centra IoT.

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Description|Dimenze|
|---|---|---|---|---|---|
|RoutingDeliveries | Směrování pokusů o doručení (Preview) | Počet | Celkem |Toto je metrika doručení směrování. Použijte dimenze k identifikaci stavu doručení pro konkrétní koncový bod nebo pro konkrétní zdroj směrování.| Prostředku<br>Vyústit<br>RoutingSource,<br>EndpointType,<br>FailureReasonCategory,<br>Koncový bod<br>*Další podrobnosti o dimenzích [**najdete tady**](#dimensions)*. |
|RoutingDeliveryLatency| Latence směrování (Preview) | Milisekund | Průměr |Toto je metrika latence doručení směrování. Použijte dimenze k identifikaci latence pro konkrétní koncový bod nebo pro konkrétní zdroj směrování.| Prostředku<br>RoutingSource,<br>EndpointType,<br>Koncový bod<br>*Další podrobnosti o dimenzích [**najdete tady**](#dimensions)*.|
|RoutingDataSizeInBytesDelivered| Velikost dat doručení směrování v bajtech (Preview)| Bajty | Celkem |Celkový počet bajtů směrovaných nástrojem IoT Hub do vlastního koncového bodu a předdefinovaného koncového bodu. Použijte dimenze k identifikaci velikosti dat směrovaného na konkrétní koncový bod nebo pro konkrétní zdroj směrování.| Prostředku<br>RoutingSource,<br>EndpointType<br>Koncový bod<br>*Další podrobnosti o dimenzích [**najdete tady**](#dimensions)*.|
|D2C. telemetrie. příchozí přenos dat<br>allProtocol|Počet pokusů o odeslání zprávy telemetrie|Počet|Celkem|Počet zpráv typu zařízení-Cloud telemetrie, které se pokusily o odeslání do služby IoT Hub|Žádná|
|D2C. telemetrie. příchozí přenos dat<br>úspěch|Odeslané zprávy telemetrie|Počet|Celkem|Počet zpráv telemetrie typu zařízení-Cloud, které se úspěšně odeslaly do služby IoT Hub|Žádná|
|C2D. Commands. odchozí.<br>dokončeno. úspěch|Doručení zpráv C2D bylo dokončeno.|Počet|Celkem|Počet úspěšně dokončených doručení zpráv typu cloud-zařízení do zařízení|Žádná|
|C2D. Commands. odchozí.<br>opustit. úspěch|Zrušené zprávy C2D|Počet|Celkem|Počet zpráv typu cloud-zařízení opuštěných zařízením|Žádná|
|C2D. Commands. odchozí.<br>odmítnout. úspěch|Odmítnuté zprávy C2D|Počet|Celkem|Počet zpráv typu cloud-zařízení odmítnutých zařízením|Žádná|
|C2DMessagesExpired|C2D zprávy prošly (Preview)|Počet|Celkem|Počet zpráv typu cloud-zařízení, jejichž platnost vypršela|Žádná|
|Devices. totalDevices|Celkem zařízení (zastaralé)|Počet|Celkem|Počet zařízení zaregistrovaných ve službě IoT Hub|Žádná|
|Devices. connectedDevices.<br>allProtocol|Připojená zařízení (zastaralé) |Počet|Celkem|Počet zařízení připojených ke službě IoT Hub|Žádná|
|D2C. telemetrie. odchozí.<br>úspěch|Směrování: doručené zprávy telemetrie|Počet|Celkem|Počet pokusů o úspěšné doručení zpráv do všech koncových bodů pomocí směrování IoT Hub. Pokud je zpráva směrována do více koncových bodů, tato hodnota se u každého úspěšného doručení zvyšuje o jednu. Pokud se zpráva doručí do stejného koncového bodu víckrát, tato hodnota se u každého úspěšného doručení zvyšuje o jednu.|Žádná|
|D2C. telemetrie. odchozí.<br>odpojení|Směrování: vyřazené zprávy telemetrie |Počet|Celkem|Počet pokusů o vyřazení zpráv IoT Hub směrování z důvodu nedoručených koncových bodů. Tato hodnota nepočítá počet zpráv doručených do záložní trasy, protože se tam nedoručují vyřazené zprávy.|Žádná|
|D2C. telemetrie. odchozí.<br>osamocené|Směrování: osamocené zprávy telemetrie |Počet|Celkem|Počet, kolikrát byly zprávy osamoceny IoT Hub směrování, protože neodpovídaly žádnému dotazu směrování, pokud je vypnutá záložní trasa.|Žádná|
|D2C. telemetrie. odchozí.<br>neplatné|Směrování: nekompatibilní zprávy telemetrie|Počet|Celkem|Počet, kolikrát IoT Hub směrování nedokázala doručovat zprávy z důvodu nekompatibility s koncovým bodem. Zpráva není kompatibilní s koncovým bodem, když se služba IoT Hub pokusí doručit zprávu do koncového bodu, a dojde k jejímu nepřechodné chybě. Neplatné zprávy se neopakují. Tato hodnota nezahrnuje opakované pokusy.|Žádná|
|D2C. telemetrie. odchozí.<br>nalezení|Směrování: zprávy doručené do záložního režimu|Počet|Celkem|Počet, kolikrát IoT Hub směrování doručených zpráv do koncového bodu přidruženého k záložní trase.|Žádná|
|D2C. Endpoints. výstup.<br>eventHubs|Směrování: zprávy doručené do centra událostí|Počet|Celkem|Počet, kolikrát IoT Hub směrování úspěšně doručuje zprávy do vlastních koncových bodů typu centrum událostí. To nezahrnuje směrování zpráv do předdefinovaných koncových bodů (události).|Žádná|
|D2C. Endpoints. latence.<br>eventHubs|Směrování: latence zprávy pro centrum událostí|Milisekund|Průměr|Průměrná latence (v milisekundách) mezi příchozími a příchozími zprávami, které se IoT Hub a příchozími zprávami do vlastních koncových bodů typu centrum událostí. To nezahrnuje směrování zpráv do předdefinovaných koncových bodů (události).|Žádná|
|D2C. Endpoints. výstup.<br>serviceBusQueues|Směrování: zprávy doručené do fronty Service Bus|Počet|Celkem|Počet, kolikrát IoT Hub směrování úspěšně doručuje zprávy do koncových bodů fronty Service Bus|Žádná|
|D2C. Endpoints. latence.<br>serviceBusQueues|Směrování: latence zprávy pro Service Bus frontu|Milisekund|Průměr|Průměrná latence (v milisekundách) mezi příchozími a příchozími zprávami IoT Hub a příchozími zprávami do koncového bodu fronty Service Bus|Žádná|
|D2C. Endpoints. výstup.<br>serviceBusTopics|Směrování: zprávy doručené do Service Bus tématu|Počet|Celkem|Počet, kolikrát IoT Hub směrování úspěšně doručuje zprávy do koncových bodů tématu Service Bus.|Žádná|
|D2C. Endpoints. latence.<br>serviceBusTopics|Směrování: latence zprávy pro Service Bus téma|Milisekund|Průměr|Průměrná latence (v milisekundách) mezi příchozími a příchozími zprávami IoT Hub a příchozími zprávami do koncového bodu Service Bus tématu.|Žádná|
|D2C. Endpoints. výstup.<br>Předdefinované události|Směrování: zprávy doručené zprávám/událostem|Počet|Celkem|Počet, kolikrát IoT Hub směrování úspěšně doručuje zprávy do integrovaného koncového bodu (zprávy/události) a záložní trasy.|Žádná|
|D2C. Endpoints. latence.<br>Předdefinované události|Směrování: latence zpráv pro zprávy/události|Milisekund|Průměr|Průměrná latence (v milisekundách) mezi příchozími a příchozími zprávami pro IoT Hub a příchozí přenos dat do integrovaného koncového bodu (zprávy/události) a záložní trasy.|Žádná|
|D2C. Endpoints. výstup.<br>úložiště|Směrování: zprávy doručené do úložiště|Počet|Celkem|Počet pokusů, IoT Hub směrování úspěšně doručuje zprávy do koncových bodů úložiště.|Žádná|
|D2C. Endpoints. latence.<br>úložiště|Směrování: latence zpráv pro úložiště|Milisekund|Průměr|Průměrná latence (v milisekundách) mezi příchozími a příchozími zprávami pro IoT Hub a příchozí přenos dat do koncového bodu úložiště|Žádná|
|D2C. Endpoints. výstup.<br>Storage. bytes|Směrování: data Doručená do úložiště|Bajty|Celkem|Množství dat (v bajtech) IoT Hub směrování doručené koncovým bodům úložiště.|Žádná|
|D2C. Endpoints. výstup.<br>Storage. BLOBs|Směrování: objekty blob doručené do úložiště|Počet|Celkem|Počet pokusů IoT Hub směrování dodaných objektů blob do koncových bodů úložiště.|Žádná|
|EventGridDeliveries|Event Grid doručení (Preview)|Počet|Celkem|Počet událostí IoT Hub publikovaných do Event Grid Použijte výslednou dimenzi pro počet úspěšných a neúspěšných žádostí. Dimenze EventType zobrazuje typ události ( https://aka.ms/ioteventgrid) .|Prostředku<br/>Vyústit<br/>Typ události|
|EventGridLatency|Latence Event Grid (Preview)|Milisekund|Průměr|Průměrná latence (v milisekundách) od okamžiku, kdy byla událost centra IoT vygenerována při publikování události do Event Grid. Toto číslo je průměrem mezi všemi typy událostí. Použijte dimenzi EventType k zobrazení latence konkrétního typu události.|Prostředku<br/>Typ události|
|D2C. vláken. Read. Success|Úspěšné čtení ze zařízení|Počet|Celkem|Počet všech úspěšných zdvojených čtení iniciované zařízením|Žádná|
|D2C. nevlákenný. Read. Failure|Neúspěšná čtení ze zařízení|Počet|Celkem|Počet všech neúspěšných čtení, která byla iniciována zařízením.|Žádná|
|D2C. nevlákenný. Read. Size|Velikost odpovědi u dvojitých čtení ze zařízení|Bajty|Průměr|Počet všech úspěšných zdvojených čtení iniciované zařízením|Žádná|
|D2C. nevlákenná. Update. Success|Úspěšné nedokončené změny ze zařízení|Počet|Celkem|Počet všech úspěšných nevyžádaných aktualizací iniciované zařízením|Žádná|
|D2C. nevlákenná. aktualizace. selhání|Neúspěšné aktualizace ze zařízení se zdvojenými chybami|Počet|Celkem|Počet všech neúspěšných aktualizací inicializovaných zařízením.|Žádná|
|D2C. nevlákenná. Update. Size|Velikost dvojitě aktualizovaných aktualizací ze zařízení|Bajty|Průměr|Celková velikost všech úspěšných nevyžádaných aktualizací iniciované zařízením|Žádná|
|C2D. Methods. Success|Úspěšná volání přímé metody|Počet|Celkem|Počet všech úspěšných volání přímé metody.|Žádná|
|C2D. Methods. Failure|Neúspěšná volání přímé metody|Počet|Celkem|Počet všech neúspěšných volání metody Direct|Žádná|
|C2D. Methods. requestSize|Velikost žádosti o vyvolání přímé metody|Bajty|Průměr|Počet všech úspěšných žádostí přímých metod.|Žádná|
|C2D. Methods. responseSize|Velikost odezvy volání přímých metod|Bajty|Průměr|Počet všech úspěšných odpovědí přímých metod.|Žádná|
|C2D. vláken. Read. Success|Úspěšné zdvojené čtení z back-endu|Počet|Celkem|Počet všech úspěšných zdvojených čtení spouštěných back-endu.|Žádná|
|C2D. nevlákenný. Read. Failure|Neúspěšné čtení z back-endu ze zadních vláken|Počet|Celkem|Počet všech neúspěšných čtení, která byla inicializována back-endu.|Žádná|
|C2D. nevlákenný. Read. Size|Velikost odpovědi zdvojeného čtení z back-endu|Bajty|Průměr|Počet všech úspěšných zdvojených čtení spouštěných back-endu.|Žádná|
|C2D. nevlákenná. Update. Success|Úspěšné zdvojené aktualizace z back-endu|Počet|Celkem|Počet všech úspěšných dokončených aktualizací, které jsou úspěšně spouštěny po konci.|Žádná|
|C2D. nevlákenná. aktualizace. selhání|Neúspěšné zdvojené aktualizace z back-endu|Počet|Celkem|Počet všech neúspěšných provolaných aktualizací, které selhaly po back-endu.|Žádná|
|C2D. nevlákenná. Update. Size|Velikost dvojitě aktualizovaných aktualizací z back-endu|Bajty|Průměr|Celková velikost všech úspěšných kompletních aktualizací, které byly spuštěny v back-endu.|Žádná|
|twinQueries. Success|Úspěšné zdvojené dotazy|Počet|Celkem|Počet všech úspěšných vlákenných dotazů.|Žádná|
|twinQueries. selhání|Neúspěšné zdvojené dotazy|Počet|Celkem|Počet všech neúspěšných dotazů, které selhaly.|Žádná|
|twinQueries.resultSize|Velikost výsledku nevlákenných dotazů|Bajty|Průměr|Celková velikost výsledků všech úspěšných vlákenných dotazů.|Žádná|
|Jobs. createTwinUpdateJob.<br>úspěch|Úspěšné vytváření zdvojených úloh aktualizace|Počet|Celkem|Počet všech úspěšných vytvoření zdvojených úloh aktualizace.|Žádná|
|Jobs. createTwinUpdateJob.<br>poruše|Nepovedlo se vytvořit úlohy s dvojitou aktualizací|Počet|Celkem|Počet všech neúspěšných vytvoření zdvojených úloh aktualizace.|Žádná|
|Jobs. createDirectMethodJob.<br>úspěch|Úspěšné vytváření úloh vyvolání metod|Počet|Celkem|Počet všech úspěšných vytvoření úloh vyvolání přímé metody.|Žádná|
|Jobs. createDirectMethodJob.<br>poruše|Nepovedlo se vytvořit úlohy vyvolání metody|Počet|Celkem|Počet všech neúspěšných vytvoření úloh volání přímé metody.|Žádná|
|Jobs. listJobs. Success|Úspěšná volání na seznam úloh|Počet|Celkem|Počet všech úspěšných volání pro výpis úloh.|Žádná|
|Jobs. listJobs. selhání|Neúspěšná volání pro výpis úloh|Počet|Celkem|Počet všech neúspěšných volání pro výpis úloh|Žádná|
|Jobs. cancelJob. Success|Úspěšná zrušení úlohy|Počet|Celkem|Počet všech úspěšných volání pro zrušení úlohy.|Žádná|
|Jobs. cancelJob. selhání|Neúspěšná zrušení úloh|Počet|Celkem|Počet všech neúspěšných volání pro zrušení úlohy.|Žádná|
|Jobs. queryJobs. Success|Úspěšné dotazy na úlohy|Počet|Celkem|Počet všech úspěšných volání úloh dotazů.|Žádná|
|Jobs. queryJobs. selhání|Neúspěšné dotazy na úlohy|Počet|Celkem|Počet všech neúspěšných volání pro úlohy dotazu.|Žádná|
|dokončené úlohy|Dokončené úlohy|Počet|Celkem|Počet všech dokončených úloh.|Žádná|
|úlohy. nezdařilo se|Neúspěšné úlohy|Počet|Celkem|Počet všech neúspěšných úloh.|Žádná|
|D2C. telemetrie. příchozí přenos dat<br>sendThrottle|Počet chyb omezování|Počet|Celkem|Počet chyb omezení z důvodu omezení propustnosti zařízení|Žádná|
|dailyMessageQuotaUsed|Celkový počet použitých zpráv|Počet|Průměr|Počet všech aktuálně využívaných zpráv Jedná se o kumulativní hodnotu, která se každý den resetuje na nulu v 00:00 UTC.|Žádná|
|deviceDataUsage|Celkové využití dat zařízení|Bajty|Celkem|Bajtů přenesených do a ze všech zařízení připojených k IotHub|Žádná|
|deviceDataUsageV2|Celkové využití dat zařízení (Preview)|Bajty|Celkem|Bajtů přenesených do a ze všech zařízení připojených k IotHub|Žádná|
|totalDeviceCount|Celkem zařízení (Preview)|Počet|Průměr|Počet zařízení zaregistrovaných ve službě IoT Hub|Žádná|
|connectedDeviceCount|Připojená zařízení (Preview)|Počet|Průměr|Počet zařízení připojených ke službě IoT Hub|Žádná|
|konfiguračních|Metriky konfigurace|Počet|Celkem|Počet celkových operací CRUD provedených pro konfiguraci zařízení a nasazení IoT Edge v sadě cílových zařízení. To zahrnuje také počet operací, které mění v důsledku těchto konfigurací práci v zařízení, která je typu vlákna nebo je v něm.|Žádná|

### <a name="dimensions"></a>Dimenze
Dimenze vám pomůžou identifikovat další podrobnosti o metrikách. Některé metriky směrování poskytují informace pro každý koncový bod. V tabulce níže jsou uvedeny možné hodnoty pro tyto dimenze.

|Dimenze|Hodnoty|
|---|---|
|ResourceID|váš prostředek IoT Hub|
|Výsledek|úspěch<br>poruše|
|RoutingSource|Zprávy zařízení<br>Události s dvojitou změnou<br>Události životního cyklu zařízení|
|EndpointType|eventHubs<br>serviceBusQueues<br>cosmosDB<br>serviceBusTopics<br>integrované<br>blobStorage|
|FailureReasonCategory|neplatné<br>odpojení<br>osamocené<br>null|
|Koncový bod|název koncového bodu|

## <a name="next-steps"></a>Další kroky

Teď, když jste se seznámili s přehledem IoT Hub metriky, získáte další informace o správě Azure IoT Hub pomocí tohoto odkazu:

* [Nastavení diagnostických protokolů](iot-hub-monitor-resource-health.md)

* [Postup řešení potíží se směrováním zpráv](troubleshoot-message-routing.md)

Chcete-li dále prozkoumat možnosti IoT Hub, přečtěte si:

* [IoT Hub příručka pro vývojáře](iot-hub-devguide.md)

* [Nasazení AI do hraničních zařízení s použitím Azure IoT Edge](../iot-edge/tutorial-simulate-device-linux.md)
