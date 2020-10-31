---
title: Monitorování referenčních informací o službě Azure IoT Hub
description: Důležité referenční materiály, které jsou potřeba při monitorování IoT Hub Azure
author: robinsh
ms.author: robinsh
ms.topic: reference
ms.service: iot-hub
ms.date: 10/22/2020
ms.openlocfilehash: 03941c3abe833deb218844cc60e2f04556fccc22
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2020
ms.locfileid: "93078199"
---
# <a name="monitoring-azure-iot-hub-data-reference"></a>Monitorování referenčních informací o službě Azure IoT Hub

Podrobnosti o shromažďování a analýze dat monitorování pro Azure IoT Hub najdete v tématu [monitorování azure IoT Hub](monitor-iot-hub.md) .

## <a name="metrics"></a>Metriky

V této části jsou uvedené všechny automaticky shromážděné metriky platforem pro Azure IoT Hub. Obor názvů poskytovatele prostředků pro IoT Hub metriky jsou **Microsoft. Devices** a obor názvů typu **IoTHubs** .

Následující podčásti rozdělují IoT Hub metriky platforem podle obecné kategorie a uvádějí je podle zobrazovaného názvu, který se zobrazí v Azure Portal. K dispozici jsou také informace týkající se metrik, které se zobrazují v jednotlivých pododdílech.

V dokumentaci k Azure Monitor můžete také najít jednu tabulku se seznamem všech metrik IoT Hub platformou podle názvu metriky v části [Microsoft. Devices/IotHubs](/azure/azure-monitor/platform/metrics-supported#microsoftdevicesiothubs) . Uvědomte si, že tato tabulka neposkytuje některé informace, jako jsou [podporované agregace](#supported-aggregations) pro některé metriky, které jsou k dispozici v tomto článku.

Další informace o metrikách podporovaných jinými službami Azure najdete v tématu [podporované metriky s Azure monitor](/azure/azure-monitor/platform/metrics-supported).

**Témata v této části**

- [Podporované agregace](#supported-aggregations)
- [Metriky příkazů z cloudu do zařízení](#cloud-to-device-command-metrics)
- [Metriky přímých metod cloudu na zařízení](#cloud-to-device-direct-methods-metrics)
- [Metriky s dvojitou operací z cloudu na zařízení](#cloud-to-device-twin-operations-metrics)
- [Konfigurace metrik](#configurations-metrics)
- [Denní metriky kvót](#daily-quota-metrics)
- [Metriky zařízení](#device-metrics)
- [Metriky telemetrie zařízení](#device-telemetry-metrics)
- [Metriky nevlákenných operací zařízení do cloudu](#device-to-cloud-twin-operations-metrics)
- [Metriky Event gridu](#event-grid-metrics)
- [Metriky úloh](#jobs-metrics)
- [Metriky směrování](#routing-metrics)
- [Dvojitá metrika dotazu](#twin-query-metrics)

### <a name="supported-aggregations"></a>Podporované agregace

Sloupec **typ agregace** v každé tabulce odpovídá výchozí agregaci, která se používá při výběru metriky pro graf nebo výstrahu.

   ![Snímek obrazovky znázorňující agregaci pro metriky](./media/monitor-iot-hub-reference/aggregation-type.png)

U většiny metrik jsou všechny typy agregace platné; pro počítání metrik, které mají hodnotu sloupce **Unit** ( **počet** ), jsou však platné pouze některé agregace. Počet metrik může být jeden ze dvou typů:

* V případě metrik počtu s **jednou desetinnou čárkou** IoT Hub registruje jeden datový bod – v podstatě 1 – při každém výskytu měřené operace. Azure Monitor pak sečte tyto datové body v rámci zadané členitosti. Příklady metrik s **jednou desetinnou čárkou** jsou *odeslané zprávy telemetrie* a *dokončené doručení zpráv C2D* . Pro tyto metriky je jediným relevantním typem agregace součet (Sum). Portál umožňuje zvolit minimální, maximální a průměrnou velikost; Tyto hodnoty však budou vždy 1.

* U metrik počtu **snímků** IoT Hub registruje celkový počet při výskytu měřené operace. V současné době jsou vygenerovány tři metriky **snímků** IoT Hub: *Celkový počet použitých zpráv* , *Celkový počet zařízení (Preview)* a *připojená zařízení (Preview)* . Vzhledem k tomu, že tyto metriky představují "celkové" množství pokaždé, když jsou vygenerovány, jejich sčítání přes zadanou členitost nepředstavuje žádný smysl. Azure Monitor omezuje výběr průměru, minima a maxima pro typ agregace pro tyto metriky.

### <a name="cloud-to-device-command-metrics"></a>Metriky příkazů z cloudu do zařízení

|Zobrazovaný název metriky|Metrika|Jednotka|Typ agregace|Description|Dimenze|
|---|---|---|---|---|---|
|C2D zprávy prošly (Preview)|C2DMessagesExpired|Počet|Celkem|Počet zpráv typu cloud-zařízení, jejichž platnost vypršela|Žádné|
|Doručení zpráv C2D bylo dokončeno.|C2D. Commands.. Complete. Complete. Success|Počet|Celkem|Počet úspěšně dokončených doručení zpráv typu cloud-zařízení do zařízení|Žádné|
|Zrušené zprávy C2D|C2D. Commands. odchozí. Abandon. Success|Počet|Celkem|Počet zpráv typu cloud-zařízení opuštěných zařízením|Žádné|
|Odmítnuté zprávy C2D|C2D. Commands. odchozí. remítat. Success|Počet|Celkem|Počet zpráv typu cloud-zařízení odmítnutých zařízením|Žádné|

Pro metriky s **jednotkovou** hodnotou **Count** je platná pouze agregace celkem (Sum). Hodnoty agregace minima, maxima a průměr vždy vrátí hodnotu 1. Další informace najdete v tématu [podporované agregace](#supported-aggregations).

### <a name="cloud-to-device-direct-methods-metrics"></a>Metriky přímých metod cloudu na zařízení

|Zobrazovaný název metriky|Metrika|Jednotka|Typ agregace|Description|Dimenze|
|---|---|---|---|---|---|
|Neúspěšná volání přímé metody|C2D. Methods. Failure|Počet|Celkem|Počet všech neúspěšných volání metody Direct|Žádné|
|Velikost žádosti o vyvolání přímé metody|C2D. Methods. requestSize|Bajty|Průměr|Počet všech úspěšných žádostí přímých metod.|Žádné|
|Velikost odezvy volání přímých metod|C2D. Methods. responseSize|Bajty|Průměr|Počet všech úspěšných odpovědí přímých metod.|Žádné|
|Úspěšná volání přímé metody|C2D. Methods. Success|Počet|Celkem|Počet všech úspěšných volání přímé metody.|Žádné|

Pro metriky s **jednotkovou** hodnotou **Count** (suma) je platná agregace. Hodnoty agregace minima, maxima a průměr vždy vrátí hodnotu 1. Další informace najdete v tématu [podporované agregace](#supported-aggregations).

### <a name="cloud-to-device-twin-operations-metrics"></a>Metriky s dvojitou operací z cloudu na zařízení

|Zobrazovaný název metriky|Metrika|Jednotka|Typ agregace|Description|Dimenze|
|---|---|---|---|---|---|
|Neúspěšné čtení z back-endu ze zadních vláken|C2D. nevlákenný. Read. Failure|Počet|Celkem|Počet všech neúspěšných čtení, která byla inicializována back-endu.|Žádné|
|Neúspěšné zdvojené aktualizace z back-endu|C2D. nevlákenná. aktualizace. selhání|Počet|Celkem|Počet všech neúspěšných provolaných aktualizací, které selhaly po back-endu.|Žádné|
|Velikost odpovědi zdvojeného čtení z back-endu|C2D. nevlákenný. Read. Size|Bajty|Průměr|Počet všech úspěšných zdvojených čtení spouštěných back-endu.|Žádné|
|Velikost dvojitě aktualizovaných aktualizací z back-endu|C2D. nevlákenná. Update. Size|Bajty|Průměr|Celková velikost všech úspěšných kompletních aktualizací, které byly spuštěny v back-endu.|Žádné|
|Úspěšné zdvojené čtení z back-endu|C2D. vláken. Read. Success|Počet|Celkem|Počet všech úspěšných zdvojených čtení spouštěných back-endu.|Žádné|
|Úspěšné zdvojené aktualizace z back-endu|C2D. nevlákenná. Update. Success|Počet|Celkem|Počet všech úspěšných dokončených aktualizací, které jsou úspěšně spouštěny po konci.|Žádné|

Pro metriky s **jednotkovou** hodnotou **Count** je platná pouze agregace celkem (Sum). Hodnoty agregace minima, maxima a průměr vždy vrátí hodnotu 1. Další informace najdete v tématu [podporované agregace](#supported-aggregations).

### <a name="configurations-metrics"></a>Konfigurace metrik

|Zobrazovaný název metriky|Metrika|Jednotka|Typ agregace|Description|Dimenze|
|---|---|---|---|---|---|
|Metriky konfigurace|konfiguračních|Počet|Celkem|Počet celkových operací CRUD provedených pro konfiguraci zařízení a nasazení IoT Edge v sadě cílových zařízení. To zahrnuje také počet operací, které mění v důsledku těchto konfigurací práci v zařízení, která je typu vlákna nebo je v něm.|Žádné|

Pro metriky s **jednotkovou** hodnotou **Count** je platná pouze agregace celkem (Sum). Hodnoty agregace minima, maxima a průměr vždy vrátí hodnotu 1. Další informace najdete v tématu [podporované agregace](#supported-aggregations).

### <a name="daily-quota-metrics"></a>Denní metriky kvót

|Zobrazovaný název metriky|Metrika|Jednotka|Typ agregace|Description|Dimenze|
|---|---|---|---|---|---|
|Celkové využití dat zařízení|deviceDataUsage|Bajty|Celkem|Bajtů přenesených do a ze všech zařízení připojených k IotHub|Žádné|
|Celkové využití dat zařízení (Preview)|deviceDataUsageV2|Bajty|Celkem|Bajtů přenesených do a ze všech zařízení připojených k IotHub|Žádné|
|Celkový počet použitých zpráv|dailyMessageQuotaUsed|Počet|Průměr|Počet všech aktuálně využívaných zpráv Jedná se o kumulativní hodnotu, která se každý den resetuje na nulu v 00:00 UTC.|Žádné|

Pro *Celkový počet použitých zpráv* jsou podporovány pouze agregace minima, maxima a průměr. Další informace najdete v tématu [podporované agregace](#supported-aggregations).

### <a name="device-metrics"></a>Metriky zařízení

|Zobrazovaný název metriky|Metrika|Jednotka|Typ agregace|Description|Dimenze|
|---|---|---|---|---|---|
|Celkem zařízení (zastaralé)|Devices. totalDevices|Počet|Celkem|Počet zařízení zaregistrovaných ve službě IoT Hub|Žádné|
|Připojená zařízení (zastaralé) |Devices. connectedDevices. allProtocol|Počet|Celkem|Počet zařízení připojených ke službě IoT Hub|Žádné|
|Celkem zařízení (Preview)|totalDeviceCount|Počet|Průměr|Počet zařízení zaregistrovaných ve službě IoT Hub|Žádné|
|Připojená zařízení (Preview)|connectedDeviceCount|Počet|Průměr|Počet zařízení připojených ke službě IoT Hub|Žádné|

Pro *celkem zařízení (nepoužívané)* a *připojená zařízení (nepoužívané)* je platná pouze agregace celkem (Sum). Hodnoty agregace minima, maxima a průměr vždy vrátí hodnotu 1. Další informace najdete v tématu [podporované agregace](#supported-aggregations).

Pro *Celkový počet zařízení (Preview)* a *připojených zařízení (ve verzi Preview)* jsou platné pouze agregace minima, maxima a průměr. Další informace najdete v tématu [podporované agregace](#supported-aggregations).

*Připojená zařízení (Preview)* a *Celkový počet zařízení (Preview)* není možné exportovat prostřednictvím nastavení diagnostiky.

### <a name="device-telemetry-metrics"></a>Metriky telemetrie zařízení

|Zobrazovaný název metriky|Metrika|Jednotka|Typ agregace|Description|Dimenze|
|---|---|---|---|---|---|
|Počet chyb omezování|D2C. telemetrie. příchozí přenos dat sendThrottle|Počet|Celkem|Počet chyb omezení z důvodu omezení propustnosti zařízení|Žádné|
|Počet pokusů o odeslání zprávy telemetrie|D2C. telemetrie. příchozí přenos dat allProtocol|Počet|Celkem|Počet zpráv typu zařízení-Cloud telemetrie, které se pokusily o odeslání do služby IoT Hub|Žádné|
|Odeslané zprávy telemetrie|D2C. telemetrie. příchozí přenos dat. úspěch|Počet|Celkem|Počet zpráv telemetrie typu zařízení-Cloud, které se úspěšně odeslaly do služby IoT Hub|Žádné|

Pro metriky s **jednotkovou** hodnotou **Count** je platná pouze agregace celkem (Sum). Hodnoty agregace minima, maxima a průměr vždy vrátí hodnotu 1. Další informace najdete v tématu [podporované agregace](#supported-aggregations).

### <a name="device-to-cloud-twin-operations-metrics"></a>Metriky nevlákenných operací zařízení do cloudu

|Zobrazovaný název metriky|Metrika|Jednotka|Typ agregace|Description|Dimenze|
|---|---|---|---|---|---|
|Neúspěšná čtení ze zařízení|D2C. nevlákenný. Read. Failure|Počet|Celkem|Počet všech neúspěšných čtení, která byla iniciována zařízením.|Žádné|
|Neúspěšné aktualizace ze zařízení se zdvojenými chybami|D2C. nevlákenná. aktualizace. selhání|Počet|Celkem|Počet všech neúspěšných aktualizací inicializovaných zařízením.|Žádné|
|Velikost odpovědi u dvojitých čtení ze zařízení|D2C. nevlákenný. Read. Size|Bajty|Průměr|Počet všech úspěšných zdvojených čtení iniciované zařízením|Žádné|
|Velikost dvojitě aktualizovaných aktualizací ze zařízení|D2C. nevlákenná. Update. Size|Bajty|Průměr|Celková velikost všech úspěšných nevyžádaných aktualizací iniciované zařízením|Žádné|
|Úspěšné čtení ze zařízení|D2C. vláken. Read. Success|Počet|Celkem|Počet všech úspěšných zdvojených čtení iniciované zařízením|Žádné|
|Úspěšné nedokončené změny ze zařízení|D2C. nevlákenná. Update. Success|Počet|Celkem|Počet všech úspěšných nevyžádaných aktualizací iniciované zařízením|Žádné|

Pro metriky s **jednotkovou** hodnotou **Count** je platná pouze agregace celkem (Sum). Hodnoty agregace minima, maxima a průměr vždy vrátí hodnotu 1. Další informace najdete v tématu [podporované agregace](#supported-aggregations).

### <a name="event-grid-metrics"></a>Metriky Event gridu

|Zobrazovaný název metriky|Metrika|Jednotka|Typ agregace|Description|Dimenze|
|---|---|---|---|---|---|
|Event Grid doručení (Preview)|EventGridDeliveries|Počet|Celkem|Počet událostí IoT Hub publikovaných do Event Grid Použijte výslednou dimenzi pro počet úspěšných a neúspěšných žádostí. Dimenze EventType zobrazuje typ události ( https://aka.ms/ioteventgrid) .|Výsledek<br/>Typ události<br>*Další informace najdete v tématu [dimenze metriky](#metric-dimensions)* .|
|Latence Event Grid (Preview)|EventGridLatency|Milisekund|Průměr|Průměrná latence (v milisekundách) od okamžiku, kdy byla událost centra IoT vygenerována při publikování události do Event Grid. Toto číslo je průměrem mezi všemi typy událostí. Použijte dimenzi EventType k zobrazení latence konkrétního typu události.|Typ události<br>*Další informace najdete v tématu [dimenze metriky](#metric-dimensions)* .|

Pro metriky s **jednotkovou** hodnotou **Count** je platná pouze agregace celkem (Sum). Hodnoty agregace minima, maxima a průměr vždy vrátí hodnotu 1. Další informace najdete v tématu [podporované agregace](#supported-aggregations).

### <a name="jobs-metrics"></a>Metriky úloh

|Zobrazovaný název metriky|Metrika|Jednotka|Typ agregace|Description|Dimenze|
|---|---|---|---|---|---|
|Dokončené úlohy|dokončené úlohy|Počet|Celkem|Počet všech dokončených úloh.|Žádné|
|Neúspěšná volání pro výpis úloh|Jobs. listJobs. selhání|Počet|Celkem|Počet všech neúspěšných volání pro výpis úloh|Žádné|
|Nepovedlo se vytvořit úlohy vyvolání metody|Jobs. createDirectMethodJob. selhání|Počet|Celkem|Počet všech neúspěšných vytvoření úloh volání přímé metody.|Žádné|
|Nepovedlo se vytvořit úlohy s dvojitou aktualizací|Jobs. createTwinUpdateJob. selhání|Počet|Celkem|Počet všech neúspěšných vytvoření zdvojených úloh aktualizace.|Žádné|
|Neúspěšná zrušení úloh|Jobs. cancelJob. selhání|Počet|Celkem|Počet všech neúspěšných volání pro zrušení úlohy.|Žádné|
|Neúspěšné dotazy na úlohy|Jobs. queryJobs. selhání|Počet|Celkem|Počet všech neúspěšných volání pro úlohy dotazu.|Žádné|
|Neúspěšné úlohy|úlohy. nezdařilo se|Počet|Celkem|Počet všech neúspěšných úloh.|Žádné|
|Úspěšná volání na seznam úloh|Jobs. listJobs. Success|Počet|Celkem|Počet všech úspěšných volání pro výpis úloh.|Žádné|
|Úspěšné vytváření úloh vyvolání metod|Jobs. createDirectMethodJob. Success|Počet|Celkem|Počet všech úspěšných vytvoření úloh vyvolání přímé metody.|Žádné|
|Úspěšné vytváření zdvojených úloh aktualizace|Jobs. createTwinUpdateJob.<br>úspěch|Počet|Celkem|Počet všech úspěšných vytvoření zdvojených úloh aktualizace.|Žádné|
|Úspěšná zrušení úlohy|Jobs. cancelJob. Success|Počet|Celkem|Počet všech úspěšných volání pro zrušení úlohy.|Žádné|
|Úspěšné dotazy na úlohy|Jobs. queryJobs. Success|Počet|Celkem|Počet všech úspěšných volání úloh dotazů.|Žádné|

Pro metriky s **jednotkovou** hodnotou **Count** je platná pouze agregace celkem (Sum). Hodnoty agregace minima, maxima a průměr vždy vrátí hodnotu 1. Další informace najdete v tématu [podporované agregace](#supported-aggregations).

### <a name="routing-metrics"></a>Metriky směrování

|Zobrazovaný název metriky|Metrika|Jednotka|Typ agregace|Description|Dimenze|
|---|---|---|---|---|---|
| Směrování pokusů o doručení (Preview) |RoutingDeliveries | Počet | Celkem |Toto je metrika doručení směrování. Použijte dimenze k identifikaci stavu doručení pro konkrétní koncový bod nebo pro konkrétní zdroj směrování.| Výsledek<br>RoutingSource,<br>EndpointType,<br>FailureReasonCategory,<br>Koncový bod<br>*Další informace najdete v tématu [dimenze metriky](#metric-dimensions)* . |
| Velikost dat doručení směrování v bajtech (Preview)|RoutingDataSizeInBytesDelivered| Bajty | Celkem |Celkový počet bajtů směrovaných nástrojem IoT Hub do vlastního koncového bodu a předdefinovaného koncového bodu. Použijte dimenze k identifikaci velikosti dat směrovaného na konkrétní koncový bod nebo pro konkrétní zdroj směrování.| RoutingSource,<br>EndpointType<br>Koncový bod<br>*Další informace najdete v tématu [dimenze metriky](#metric-dimensions)* .|
| Latence směrování (Preview) |RoutingDeliveryLatency| Milisekund | Průměr |Toto je metrika latence doručení směrování. Použijte dimenze k identifikaci latence pro konkrétní koncový bod nebo pro konkrétní zdroj směrování.| RoutingSource,<br>EndpointType,<br>Koncový bod<br>*Další informace najdete v tématu [dimenze metriky](#metric-dimensions)* .|
|Směrování: objekty blob doručené do úložiště|D2C. Endpoints. výstup. Storage. BLOBs|Počet|Celkem|Počet pokusů IoT Hub směrování dodaných objektů blob do koncových bodů úložiště.|Žádné|
|Směrování: data Doručená do úložiště|D2C. Endpoints. invýstups. Storage. bytes|Bajty|Celkem|Množství dat (v bajtech) IoT Hub směrování doručené koncovým bodům úložiště.|Žádné|
|Směrování: latence zprávy pro centrum událostí|D2C. Endpoints. latence. eventHubs|Milisekund|Průměr|Průměrná latence (v milisekundách) mezi příchozími a příchozími zprávami, které se IoT Hub a příchozími zprávami do vlastních koncových bodů typu centrum událostí. To nezahrnuje směrování zpráv do předdefinovaných koncových bodů (události).|Žádné|
|Směrování: latence zprávy pro Service Bus frontu|D2C. Endpoints. latence. serviceBusQueues|Milisekund|Průměr|Průměrná latence (v milisekundách) mezi příchozími a příchozími zprávami IoT Hub a příchozími zprávami do koncového bodu fronty Service Bus|Žádné|
|Směrování: latence zprávy pro Service Bus téma|D2C. Endpoints. latence. serviceBusTopics|Milisekund|Průměr|Průměrná latence (v milisekundách) mezi příchozími a příchozími zprávami IoT Hub a příchozími zprávami do koncového bodu Service Bus tématu.|Žádné|
|Směrování: latence zpráv pro zprávy/události|D2C. Endpoints. latence. builtIn. events|Milisekund|Průměr|Průměrná latence (v milisekundách) mezi příchozími a příchozími zprávami pro IoT Hub a příchozí přenos dat do integrovaného koncového bodu (zprávy/události) a záložní trasy.|Žádné|
|Směrování: latence zpráv pro úložiště|D2C. Endpoints. latence. Storage|Milisekund|Průměr|Průměrná latence (v milisekundách) mezi příchozími a příchozími zprávami pro IoT Hub a příchozí přenos dat do koncového bodu úložiště|Žádné|
|Směrování: zprávy doručené do centra událostí|D2C. Endpoints. odchozí. eventHubs|Počet|Celkem|Počet, kolikrát IoT Hub směrování úspěšně doručuje zprávy do vlastních koncových bodů typu centrum událostí. To nezahrnuje směrování zpráv do předdefinovaných koncových bodů (události).|Žádné|
|Směrování: zprávy doručené do fronty Service Bus|D2C. Endpoints. odchozí. serviceBusQueues|Počet|Celkem|Počet, kolikrát IoT Hub směrování úspěšně doručuje zprávy do koncových bodů fronty Service Bus|Žádné|
|Směrování: zprávy doručené do Service Bus tématu|D2C. Endpoints. odchozí. serviceBusTopics|Počet|Celkem|Počet, kolikrát IoT Hub směrování úspěšně doručuje zprávy do koncových bodů tématu Service Bus.|Žádné|
|Směrování: zprávy doručené do záložního režimu|D2C. telemetrie. odchozí. Fallback|Počet|Celkem|Počet, kolikrát IoT Hub směrování doručených zpráv do koncového bodu přidruženého k záložní trase.|Žádné|
|Směrování: zprávy doručené zprávám/událostem|D2C. Endpoints. invýstups. builtIns. events|Počet|Celkem|Počet, kolikrát IoT Hub směrování úspěšně doručuje zprávy do integrovaného koncového bodu (zprávy/události) a záložní trasy.|Žádné|
|Směrování: zprávy doručené do úložiště|D2C. Endpoints. odchozí úložiště. Storage|Počet|Celkem|Počet pokusů, IoT Hub směrování úspěšně doručuje zprávy do koncových bodů úložiště.|Žádné|
|Směrování: doručené zprávy telemetrie|D2C. telemetrie. odchozí. úspěch|Počet|Celkem|Počet pokusů o úspěšné doručení zpráv do všech koncových bodů pomocí směrování IoT Hub. Pokud je zpráva směrována do více koncových bodů, tato hodnota se u každého úspěšného doručení zvyšuje o jednu. Pokud se zpráva doručí do stejného koncového bodu víckrát, tato hodnota se u každého úspěšného doručení zvyšuje o jednu.|Žádné|
|Směrování: vyřazené zprávy telemetrie |D2C. telemetrie. výstup. vyřazeno|Počet|Celkem|Počet pokusů o vyřazení zpráv IoT Hub směrování z důvodu nedoručených koncových bodů. Tato hodnota nepočítá počet zpráv doručených do záložní trasy, protože se tam nedoručují vyřazené zprávy.|Žádné|
|Směrování: nekompatibilní zprávy telemetrie|D2C. telemetrie. invýstup. neplatné|Počet|Celkem|Počet, kolikrát IoT Hub směrování nedokázala doručovat zprávy z důvodu nekompatibility s koncovým bodem. Zpráva není kompatibilní s koncovým bodem, když se služba IoT Hub pokusí doručit zprávu do koncového bodu a dojde k jejímu nepřechodné chybě. Neplatné zprávy se neopakují. Tato hodnota nezahrnuje opakované pokusy.|Žádné|
|Směrování: osamocené zprávy telemetrie |D2C. telemetrie. výstup. osamocený|Počet|Celkem|Počet, kolikrát byly zprávy osamoceny IoT Hub směrování, protože neodpovídaly žádnému dotazu směrování, pokud je vypnutá záložní trasa.|Žádné|

Pro metriky s **jednotkovou** hodnotou **Count** je platná pouze agregace celkem (Sum). Hodnoty agregace minima, maxima a průměr vždy vrátí hodnotu 1. Další informace najdete v tématu [podporované agregace](#supported-aggregations).

### <a name="twin-query-metrics"></a>Dvojitá metrika dotazu

|Zobrazovaný název metriky|Metrika|Jednotka|Typ agregace|Description|Dimenze|
|---|---|---|---|---|---|
|Neúspěšné zdvojené dotazy|twinQueries. selhání|Počet|Celkem|Počet všech neúspěšných dotazů, které selhaly.|Žádné|
|Úspěšné zdvojené dotazy|twinQueries. Success|Počet|Celkem|Počet všech úspěšných vlákenných dotazů.|Žádné|
|Velikost výsledku nevlákenných dotazů|twinQueries.resultSize|Bajty|Průměr|Celková velikost výsledků všech úspěšných vlákenných dotazů.|Žádné|

Pro metriky s **jednotkovou** hodnotou **Count** je platná pouze agregace celkem (Sum). Hodnoty agregace minima, maxima a průměr vždy vrátí hodnotu 1. Další informace najdete v tématu [podporované agregace](#supported-aggregations).

## <a name="metric-dimensions"></a>Dimenze metriky

Azure IoT Hub má následující dimenze, které jsou přidružené k některým z jeho metrik směrování a služby Event Grid.

|Název dimenze | Description|
|---|---|
||
|**Koncový bod**| Název koncového bodu.|
|**EndpointType**|Jedna z následujících: **eventHubs** , **serviceBusQueues** , **cosmosDB** , **serviceBusTopics** . **Builtin** nebo **blobStorage** .|
|**Typ**| Jeden z následujících typů Event Grid události: **Microsoft. Devices. DeviceCreated** . **Microsoft. Devices. DeviceDeleted** , **Microsoft. Devices. DeviceConnected** , **Microsoft. Devices. DeviceDisconnected** nebo **Microsoft. Devices. DeviceTelemetry** . Další informace naleznete v tématu [typy událostí](iot-hub-event-grid.md#event-types).|
|**FailureReasonCategory**| Jedna z následujících hodnot: **neplatná** , **Vyřazená** , **osamocená** nebo **null** .|
|**Výsledek**| Buď **úspěch** , nebo **neúspěch** .|
|**RoutingSource**| Zprávy zařízení<br>Události s dvojitou změnou<br>Události životního cyklu zařízení|

Další informace o dimenzích metrik najdete v tématu multidimenzionální [metriky](/azure/azure-monitor/platform/data-platform-metrics#multi-dimensional-metrics).

## <a name="resource-logs"></a>Protokoly prostředků

V této části jsou uvedené všechny typy kategorií protokolu prostředků a schémata shromážděná pro Azure IoT Hub. Poskytovatel prostředků a typ pro všechny protokoly IoT Hub jsou [Microsoft. Devices/IotHubs](/azure/azure-monitor/platform/resource-logs-categories#microsoftdevicesiothubs).

**Témata v této části**

- [Připojení](#connections)
- [Telemetrie zařízení](#device-telemetry)
- [Příkazy cloud-zařízení](#cloud-to-device-commands)
- [Operace identity zařízení](#device-identity-operations)
- [Operace nahrávání souborů](#file-upload-operations)
- [Trasy](#routes)
- [Zdvojené operace mezi zařízeními a cloudem](#device-to-cloud-twin-operations)
- [Zdvojené operace z cloudu na zařízení](#cloud-to-device-twin-operations)
- [Zdvojené dotazy](#twin-queries)
- [Operace úloh](#jobs-operations)
- [Přímé metody](#direct-methods)
- [Distribuované trasování (Preview)](#distributed-tracing-preview)
  - [Protokoly IoT Hub D2C (zařízení-Cloud)](#iot-hub-d2c-device-to-cloud-logs)
  - [IoT Hub protokoly příchozího přenosu dat](#iot-hub-ingress-logs)
  - [Protokoly odchozího IoT Hub](#iot-hub-egress-logs)
- [Konfigurace](#configurations)
- [Datové proudy zařízení (Preview)](#device-streams-preview)

### <a name="connections"></a>Připojení

Kategorie připojení sleduje události připojení zařízení a odpojení od služby IoT Hub a také chyby. Tato kategorie je užitečná pro identifikaci neautorizovaných pokusů o připojení a upozornění, když ztratíte připojení k zařízením.

> [!NOTE]
> U spolehlivých stavů připojení zařízení se kontrolují [prezenční signály zařízení](iot-hub-devguide-identity-registry.md#device-heartbeat).

```json
{
   "records":
   [
        {
            "time": " UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "deviceConnect",
            "category": "Connections",
            "level": "Information",
            "properties": "{\"deviceId\":\"<deviceId>\",\"sdkVersion\":\"<sdkVersion>\",\"protocol\":\"<protocol>\",\"authType\":\"{\\\"scope\\\":\\\"device\\\",\\\"type\\\":\\\"sas\\\",\\\"issuer\\\":\\\"iothub\\\",\\\"acceptingIpFilterRule\\\":null}\",\"maskedIpAddress\":\"<maskedIpAddress>\"}",
            "location": "Resource location"
        }
    ]
}
```

### <a name="device-telemetry"></a>Telemetrie zařízení

Kategorie telemetrie zařízení sleduje chyby, ke kterým dochází ve službě IoT Hub a souvisí s kanálem telemetrie. Tato kategorie zahrnuje chyby, ke kterým dochází při posílání událostí telemetrie (například omezování) a přijímání událostí telemetrie (například neoprávněné čtecí zařízení). Tato kategorie nemůže zachytit chyby způsobené kódem běžícím na samotném zařízení.

```json
{
    "records":
    [
        {
            "time": "UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "ingress",
            "category": "DeviceTelemetry",
            "level": "Error",
            "resultType": "Event status",
            "resultDescription": "MessageDescription",
            "properties": "{\"deviceId\":\"<deviceId>\",\"batching\":\"0\",\"messageSizeInBytes\":\"<messageSizeInBytes>\",\"EventProcessedUtcTime\":\"<UTC timestamp>\",\"EventEnqueuedUtcTime\":\"<UTC timestamp>\",\"partitionId\":\"1\"}", 
            "location": "Resource location"
        }
    ]
}
```

### <a name="cloud-to-device-commands"></a>Příkazy cloud-zařízení

Kategorie příkazy Cloud-zařízení sleduje chyby, ke kterým dochází ve službě IoT Hub a které souvisejí s kanálem zpráv z cloudu na zařízení. Tato kategorie zahrnuje chyby, ke kterým dochází:

* Posílání zpráv z cloudu na zařízení (jako jsou chyby neautorizovaných odesílatelů),
* Příjem zpráv z cloudu na zařízení (jako je počet předaných doručení překročil chyby) a
* Příjem zpětné vazby ze zpráv typu cloud-zařízení (jako jsou chyby vypršení zpětné vazby)

Tato kategorie nezachycuje chyby, když se zpráva z cloudu na zařízení úspěšně doručí, ale zařízení nesprávně zpracuje.

```json
{
    "records":
    [
        {
            "time": " UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "messageExpired",
            "category": "C2DCommands",
            "level": "Error",
            "resultType": "Event status",
            "resultDescription": "MessageDescription",
            "properties": "{\"deviceId\":\"<deviceId>\",\"messageId\":\"<messageId>\",\"messageSizeInBytes\":\"<messageSize>\",\"protocol\":\"Amqp\",\"deliveryAcknowledgement\":\"<None, NegativeOnly, PositiveOnly, Full>\",\"deliveryCount\":\"0\",\"expiryTime\":\"<timestamp>\",\"timeInSystem\":\"<timeInSystem>\",\"ttl\":<ttl>, \"EventProcessedUtcTime\":\"<UTC timestamp>\",\"EventEnqueuedUtcTime\":\"<UTC timestamp>\", \"maskedIpAddress\": \"<maskedIpAddress>\", \"statusCode\": \"4XX\"}",
            "location": "Resource location"
        }
    ]
}
```

### <a name="device-identity-operations"></a>Operace identity zařízení

Kategorie operace identity zařízení sleduje chyby, ke kterým dochází při pokusu o vytvoření, aktualizaci nebo odstranění záznamu v registru identit ve službě IoT Hub. Sledování této kategorie je užitečné pro scénáře zřizování.

```json
{
    "records":
    [
        {
            "time": "UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "get",
            "category": "DeviceIdentityOperations",
            "level": "Error",
            "resultType": "Event status",
            "resultDescription": "MessageDescription",
            "properties": "{\"maskedIpAddress\":\"<maskedIpAddress>\",\"deviceId\":\"<deviceId>\", \"statusCode\":\"4XX\"}",
            "location": "Resource location"
        }
    ]
}
```

### <a name="file-upload-operations"></a>Operace nahrávání souborů

Kategorie nahrání souborů sleduje chyby, ke kterým dochází ve službě IoT Hub a souvisí s funkcemi pro nahrávání souborů. Tato kategorie zahrnuje:

* Chyby, ke kterým dochází s identifikátorem URI SAS, například když vyprší platnost před tím, než zařízení upozorní centrum dokončeného nahrávání.

* Neúspěšná odeslání uvedená v zařízení

* Chyby, ke kterým dochází, když se během vytváření zprávy IoT Hub oznámení v úložišti nenajde soubor

Tato kategorie nemůže zachytit chyby, ke kterým přímo dojde, když zařízení nahrává do úložiště soubor.

```json
{
    "records":
    [
        {
            "time": "UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "ingress",
            "category": "FileUploadOperations",
            "level": "Error",
            "resultType": "Event status",
            "resultDescription": "MessageDescription",
            "durationMs": "1",
            "properties": "{\"deviceId\":\"<deviceId>\",\"protocol\":\"<protocol>\",\"authType\":\"{\\\"scope\\\":\\\"device\\\",\\\"type\\\":\\\"sas\\\",\\\"issuer\\\":\\\"iothub\\\",\\\"acceptingIpFilterRule\\\":null}\",\"blobUri\":\"http//bloburi.com\"}",
            "location": "Resource location"
        }
    ]
}
```

### <a name="routes"></a>Trasy

Kategorie [směrování zpráv](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-d2c) sleduje chyby, ke kterým došlo během hodnocení směrování zpráv a stavu koncového bodu, jak je uvedeno IoT Hub. Tato kategorie zahrnuje události jako:

* Pravidlo se vyhodnotí jako nedefinované.
* IoT Hub označí koncový bod jako mrtvý nebo
* Jakékoli chyby přijaté z koncového bodu.

Tato kategorie neobsahuje konkrétní chyby týkající se samotných zpráv (například chyby omezování zařízení), které jsou uvedeny v kategorii telemetrie zařízení.

```json
{
    "records":
    [
        {
            "time":"2019-12-12T03:25:14Z",
            "resourceId":"/SUBSCRIPTIONS/91R34780-3DEC-123A-BE2A-213B5500DFF0/RESOURCEGROUPS/ANON-TEST/PROVIDERS/MICROSOFT.DEVICES/IOTHUBS/ANONHUB1",
            "operationName":"endpointUnhealthy",
            "category":"Routes",
            "level":"Error",
            "resultType":"403004",
            "resultDescription":"DeviceMaximumQueueDepthExceeded",
            "properties":"{\"deviceId\":null,\"endpointName\":\"anon-sb-1\",\"messageId\":null,\"details\":\"DeviceMaximumQueueDepthExceeded\",\"routeName\":null,\"statusCode\":\"403\"}",
            "location":"westus"
        }
    ]
}
```

Tady jsou další podrobnosti o protokolech prostředků směrování:

* [Seznam kódů chyb protokolu prostředků směrování](troubleshoot-message-routing.md#diagnostics-error-codes)
* [Seznam protokolů prostředku směrování operationNames](troubleshoot-message-routing.md#diagnostics-operation-names)

### <a name="device-to-cloud-twin-operations"></a>Zdvojené operace mezi zařízeními a cloudem

Kategorie operace s dvojitým cloudem v zařízení sleduje události iniciované zařízením v případě nevláken zařízení. Tyto operace mohou zahrnovat získat zdvojené, aktualizovat hlášené vlastnosti a přihlásit se k odběru požadovaných vlastností.

```json
{
    "records":
    [
        {
            "time": "UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "update",
            "category": "D2CTwinOperations",
            "level": "Information",
            "durationMs": "1",
            "properties": "{\"deviceId\":\"<deviceId>\",\"protocol\":\"<protocol>\",\"authenticationType\":\"{\\\"scope\\\":\\\"device\\\",\\\"type\\\":\\\"sas\\\",\\\"issuer\\\":\\\"iothub\\\",\\\"acceptingIpFilterRule\\\":null}\"}",
            "location": "Resource location"
        }
    ]
}
```

### <a name="cloud-to-device-twin-operations"></a>Zdvojené operace z cloudu na zařízení

Kategorie dvojitých operací z cloudu na zařízení sleduje události iniciované službou v případě vláken zařízení. Tyto operace mohou zahrnovat získání dvojitých značek, aktualizovat nebo nahradit značky a aktualizovat nebo nahrazovat požadované vlastnosti.

```json
{
    "records":
    [
        {
            "time": "UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "read",
            "category": "C2DTwinOperations",
            "level": "Information",
            "durationMs": "1",
            "properties": "{\"deviceId\":\"<deviceId>\",\"sdkVersion\":\"<sdkVersion>\",\"messageSize\":\"<messageSize>\"}",
            "location": "Resource location"
        }
    ]
}
```

### <a name="twin-queries"></a>Zdvojené dotazy

Kategorie dvojitých dotazů se sestavuje na dotazech na požadavky na vlákna zařízení, která jsou inicializovaná v cloudu.

```json
{
    "records":
    [
        {
            "time": "UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "query",
            "category": "TwinQueries",
            "level": "Information",
            "durationMs": "1",
            "properties": "{\"query\":\"<twin query>\",\"sdkVersion\":\"<sdkVersion>\",\"messageSize\":\"<messageSize>\",\"pageSize\":\"<pageSize>\", \"continuation\":\"<true, false>\", \"resultSize\":\"<resultSize>\"}",
            "location": "Resource location"
        }
    ]
}
```

### <a name="jobs-operations"></a>Operace úloh

Kategorie operace úlohy se sestavuje na žádostech o úlohy, aby se aktualizovaly vlákna zařízení nebo vyvolaly přímé metody na více zařízeních. Tyto požadavky jsou iniciovány v cloudu.

```json
{
    "records":
    [
        {
            "time": "UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "jobCompleted",
            "category": "JobsOperations",
            "level": "Information",
            "durationMs": "1",
            "properties": "{\"jobId\":\"<jobId>\", \"sdkVersion\": \"<sdkVersion>\",\"messageSize\": <messageSize>,\"filter\":\"DeviceId IN ['1414ded9-b445-414d-89b9-e48e8c6285d5']\",\"startTimeUtc\":\"Wednesday, September 13, 2017\",\"duration\":\"0\"}",
            "location": "Resource location"
        }
    ]
}
```

### <a name="direct-methods"></a>Přímé metody

Kategorie přímé metody sleduje interakce odpovědí na požadavky odeslané na jednotlivá zařízení. Tyto požadavky jsou iniciovány v cloudu.

```json
{
    "records":
    [
        {
            "time": "UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "send",
            "category": "DirectMethods",
            "level": "Information",
            "durationMs": "1",
            "properties": "{\"deviceId\":<messageSize>, \"RequestSize\": 1, \"ResponseSize\": 1, \"sdkVersion\": \"2017-07-11\"}",
            "location": "Resource location"
        }
    ]
}
```

### <a name="distributed-tracing-preview"></a>Distribuované trasování (Preview)

Kategorie distribuované trasování sleduje ID korelace pro zprávy, které přenášejí hlavičku kontextu trasování. Aby bylo možné tyto protokoly plně povolit, musí být kód na straně klienta aktualizován pomocí následujících příkazů [analyzovat a diagnostikovat aplikace IoT pomocí IoT Hub distribuované trasování (Preview)](iot-hub-distributed-tracing.md).

Všimněte si, že `correlationId` odpovídá návrhu [kontextu trasování W3C](https://github.com/w3c/trace-context) , kde obsahuje, a `trace-id` také `span-id` .

#### <a name="iot-hub-d2c-device-to-cloud-logs"></a>Protokoly IoT Hub D2C (zařízení-Cloud)

IoT Hub zaznamenává tento protokol, když se do IoT Hub dorazí zpráva obsahující platné vlastnosti trasování.

```json
{
    "records":
    [
        {
            "time": "UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "DiagnosticIoTHubD2C",
            "category": "DistributedTracing",
            "correlationId": "00-8cd869a412459a25f5b4f31311223344-0144d2590aacd909-01",
            "level": "Information",
            "resultType": "Success",
            "resultDescription":"Receive message success",
            "durationMs": "",
            "properties": "{\"messageSize\": 1, \"deviceId\":\"<deviceId>\", \"callerLocalTimeUtc\": : \"2017-02-22T03:27:28.633Z\", \"calleeLocalTimeUtc\": \"2017-02-22T03:27:28.687Z\"}",
            "location": "Resource location"
        }
    ]
}
```

V tomto případě `durationMs` se nepočítá, protože hodiny IoT Hub nemusejí být synchronizované s hodinami zařízení, takže výpočet doby trvání může být zavádějící. `properties`Pro zachycení špičky v latenci v rámci zařízení do cloudu doporučujeme napsat logiku pomocí časových razítek v části.

| Vlastnost | Typ | Description |
|--------------------|-----------------------------------------------|------------------------------------------------------------------------------------------------|
| **messageSize** | Integer | Velikost zprávy typu zařízení-Cloud v bajtech |
| **deviceId** | Řetězec alfanumerických alfanumerických znaků ASCII | Identita zařízení |
| **callerLocalTimeUtc** | Časové razítko UTC | Čas vytvoření zprávy, jak je uvedeno v místních hodinách zařízení |
| **calleeLocalTimeUtc** | Časové razítko UTC | Čas doručení zprávy na bránu IoT Hub, jak je uvedeno v IoT Hub hodiny na straně služby |

#### <a name="iot-hub-ingress-logs"></a>IoT Hub protokoly příchozího přenosu dat

IoT Hub zaznamenává tento protokol, pokud zpráva obsahující platné vlastnosti trasování zapisuje do interního nebo integrovaného centra událostí.

```json
{
    "records":
    [
        {
            "time": "UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "DiagnosticIoTHubIngress",
            "category": "DistributedTracing",
            "correlationId": "00-8cd869a412459a25f5b4f31311223344-349810a9bbd28730-01",
            "level": "Information",
            "resultType": "Success",
            "resultDescription":"Ingress message success",
            "durationMs": "10",
            "properties": "{\"isRoutingEnabled\": \"true\", \"parentSpanId\":\"0144d2590aacd909\"}",
            "location": "Resource location"
        }
    ]
}
```

V `properties` části Tento protokol obsahuje další informace o příchozím přenosu zpráv.

| Vlastnost | Typ | Description |
|--------------------|-----------------------------------------------|------------------------------------------------------------------------------------------------|
| **isRoutingEnabled** | Řetězec | Hodnota true nebo false označuje, zda je v IoT Hub povoleno směrování zpráv. |
| **parentSpanId** | Řetězec | [Identifikátor rozsahu](https://w3c.github.io/trace-context/#parent-id) nadřazené zprávy, který by byl trasováním zpráv D2C v tomto případě |

#### <a name="iot-hub-egress-logs"></a>Protokoly odchozího IoT Hub

IoT Hub zaznamenává tento protokol, pokud je povoleno [Směrování](iot-hub-devguide-messages-d2c.md) a zpráva je zapsána do [koncového bodu](iot-hub-devguide-endpoints.md). Pokud směrování není povolené, IoT Hub tento protokol nezaznamenává.

```json
{
    "records":
    [
        {
            "time": "UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "DiagnosticIoTHubEgress",
            "category": "DistributedTracing",
            "correlationId": "00-8cd869a412459a25f5b4f31311223344-98ac3578922acd26-01",
            "level": "Information",
            "resultType": "Success",
            "resultDescription":"Egress message success",
            "durationMs": "10",
            "properties": "{\"endpointType\": \"EventHub\", \"endpointName\": \"myEventHub\", \"parentSpanId\":\"349810a9bbd28730\"}",
            "location": "Resource location"
        }
    ]
}
```

V `properties` části Tento protokol obsahuje další informace o příchozím přenosu zpráv.

| Vlastnost | Typ | Description |
|--------------------|-----------------------------------------------|------------------------------------------------------------------------------------------------|
| **koncový bod** | Řetězec | Název koncového bodu směrování |
| **endpointType** | Řetězec | Typ koncového bodu směrování |
| **parentSpanId** | Řetězec | [Identifikátor ID](https://w3c.github.io/trace-context/#parent-id) nadřazené zprávy, který bude v tomto případě IoT Hub trasování zprávy příchozího přenosu dat |

### <a name="configurations"></a>Konfigurace

Protokoly konfigurace IoT Hub sledují události a chybu pro sadu funkcí automatické správy zařízení.

```json
{
    "records":
    [
         {
             "time": "2019-09-24T17:21:52Z",
             "resourceId": "Resource Id",
             "operationName": "ReadManyConfigurations",
             "category": "Configurations",
             "resultType": "",
             "resultDescription": "",
             "level": "Information",
             "durationMs": "17",
             "properties": "{\"configurationId\":\"\",\"sdkVersion\":\"2018-06-30\",\"messageSize\":\"0\",\"statusCode\":null}",
             "location": "southcentralus"
         }
    ]
}
```

### <a name="device-streams-preview"></a>Datové proudy zařízení (Preview)

Kategorie streamy zařízení sleduje interakce požadavků a odpovědí odeslaných na jednotlivá zařízení.

```json
{
    "records":
    [
         {
             "time": "2019-09-19T11:12:04Z",
             "resourceId": "Resource Id",
             "operationName": "invoke",
             "category": "DeviceStreams",
             "resultType": "",
             "resultDescription": "",    
             "level": "Information",
             "durationMs": "74",
             "properties": "{\"deviceId\":\"myDevice\",\"moduleId\":\"myModule\",\"sdkVersion\":\"2019-05-01-preview\",\"requestSize\":\"3\",\"responseSize\":\"5\",\"statusCode\":null,\"requestName\":\"myRequest\",\"direction\":\"c2d\"}",
             "location": "Central US"
         }
    ]
}
```

## <a name="azure-monitor-logs-tables"></a>Tabulky protokolů Azure Monitor
<!-- REQUIRED. Please keep heading in this order -->

Tato část odkazuje na všechny Azure Monitor protokoluje tabulky Kusto relevantní pro Azure IoT Hub a k dispozici pro dotazy pomocí Log Analytics. Seznam těchto tabulek a odkazy na Další informace o typu IoT Hub prostředku najdete v tématu [IoT Hub](/azure/azure-monitor/reference/tables/tables-resourcetype#iot-hub) v odkazu na tabulku Azure monitor protokoly.

Odkaz na všechny Azure Monitor protokolů/Log Analytics tabulek najdete v tématu [referenční tabulka protokolu Azure monitor](/azure/azure-monitor/reference/tables/tables-resourcetype).

## <a name="see-also"></a>Viz také

* Popis monitorování Azure IoT Hub najdete v tématu [monitorování azure IoT Hub](monitor-iot-hub.md) .
* Podrobnosti o monitorování prostředků Azure najdete v tématu [monitorování prostředků Azure pomocí Azure monitor](/azure/azure-monitor/insights/monitor-azure-resource) .
