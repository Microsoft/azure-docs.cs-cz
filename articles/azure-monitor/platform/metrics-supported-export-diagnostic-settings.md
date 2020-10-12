---
title: Chování při exportu metrik s NULOVÝmi a nulovými hodnotami
description: Při exportování metrik a ukazatele na seznam těch, které metriky exportovat nejsou, je diskuze NULL a nulové hodnoty.
services: azure-monitor
ms.topic: reference
ms.date: 07/22/2020
ms.subservice: metrics
ms.openlocfilehash: ca6acb97e52123a0663d988b3f217d305bce2c4b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87131680"
---
# <a name="azure-monitor-platform-metrics-exportable-via-diagnostic-settings"></a>Metriky platformy Azure Monitor, které je možné exportovat prostřednictvím nastavení diagnostiky

Azure Monitor poskytuje ve výchozím nastavení [metriky platforem](data-platform-metrics.md) bez konfigurace. Nabízí několik způsobů, jak pracovat s metrikami platforem, včetně jejich grafů na portálu, přístupu k nim prostřednictvím REST API nebo jejich dotazování pomocí PowerShellu nebo rozhraní příkazového řádku. Úplný seznam metrik platforem, které jsou aktuálně k dispozici v konsolidovaném kanálu metriky Azure Monitor, najdete v tématu [metriky – podporované](metrics-supported.md) . Pro dotazování a přístup k těmto metrikám použijte [verzi api 2018-01-01](/rest/api/monitor/metricdefinitions). Jiné metriky mohou být k dispozici na portálu nebo pomocí starších rozhraní API.

## <a name="metrics-not-exportable-via-diagnostic-settings"></a>Metriky nejde exportovat prostřednictvím nastavení diagnostiky.

Obsah, který se použil v tomto umístění, se přesunul na [seznam podporovaných Azure monitor metrik](metrics-supported.md#exporting-platform-metrics-to-other-locations).

Při exportu metrik prostřednictvím nastavení diagnostiky platí omezení. Všechny metriky je možné exportovat pomocí REST API. 

## <a name="exported-zero-vs-null-values"></a>Vyexportovány nulové hodnoty vs NULL 

Metriky mají různé chování při práci s hodnotami 0 nebo NULL.  Některé metriky nahlásí 0, pokud nejsou získána žádná data, například metriky při selhání http. Jiné metriky ukládají hodnoty NULL, pokud nejsou získána žádná data, protože by to mohlo znamenat, že je prostředek offline. Při sestavování těchto metrik s hodnotami NULL zobrazenými jako [přerušované řádky](metrics-troubleshoot.md#chart-shows-dashed-line)vidíte rozdíl. 

Pokud je možné metriky platforem exportovat prostřednictvím nastavení diagnostiky, odpovídají chování metriky. To znamená, že exportují hodnoty NULL, pokud prostředek neposílá žádná data.  Vyexportují ' 0 ' jenom v případě, že jsou skutečně vygenerované podkladovým prostředkem. 

Pokud odstraníte skupinu prostředků nebo konkrétní prostředek, data metriky ze ovlivněných prostředků už nebudou odesílána do nastavení diagnostiky export cílů. To znamená, že se už nezobrazuje v Event Hubs, Azure Storage účty a Log Analytics pracovní prostory.

### <a name="metrics-that-used-to-export-zero-for-null"></a>Metriky, které se použily k exportu nuly pro NULL

Před 1. června 2020 se následující metriky vygenerovaly 0, pokud nedošlo **k žádným** datům. Tyto nuly pak mohou být exportovány do navazujících systémů, jako jsou Log Analytics a Azure Storage.  Toto chování způsobilo nějakou nejasnost mezi skutečnou ' 0s ' (vysílanou prostředkem) a interpretována ' 0s ' (hodnoty NULL), takže chování bylo změněno tak, aby odpovídalo základní metrikě, jak je uvedeno v předchozí části. 

Změna nastala ve všech veřejných i privátních cloudech.

Změna neovlivnila chování žádné z následujících možností: 
   - Protokoly prostředků platformy exportované prostřednictvím nastavení diagnostiky
   - Vytváření grafů metrik v Průzkumník metrik
   - Upozornění na metriky platforem
 
Následuje seznam metrik, u kterých došlo ke změně chování. 

| ResourceType                    | Metrika               |  MetricDisplayName  | 
|---------------------------------|----------------------|---------------------|
| Microsoft.ApiManagement/service | UnauthorizedRequests |  Neautorizované žádosti o bránu (zastaralé)  | 
| Microsoft.ApiManagement/service | TotalRequests |  Požadavky brány celkem (zastaralé)  | 
| Microsoft.ApiManagement/service | SuccessfulRequests |  Úspěšné požadavky brány (zastaralé)  | 
| Microsoft.ApiManagement/service | Žádosti |  Žádosti  | 
| Microsoft.ApiManagement/service | OtherRequests |  Jiné požadavky na bránu (zastaralé)  | 
| Microsoft.ApiManagement/service | FailedRequests |  Neúspěšné požadavky brány (zastaralé)  | 
| Microsoft.ApiManagement/service | EventHubTotalFailedEvents |  Neúspěšné události EventHub  | 
| Microsoft.ApiManagement/service | EventHubTotalEvents |  Celkový počet událostí EventHub  | 
| Microsoft.ApiManagement/service | EventHubTotalBytesSent |  Velikost událostí EventHub  | 
| Microsoft.ApiManagement/service | EventHubTimedoutEvents |  Vypršel časový limit událostí EventHub.  | 
| Microsoft.ApiManagement/service | EventHubThrottledEvents |  Omezené události EventHub  | 
| Microsoft.ApiManagement/service | EventHubSuccessfulEvents |  Úspěšné události EventHub  | 
| Microsoft.ApiManagement/service | EventHubRejectedEvents |  Odmítnuté události EventHub  | 
| Microsoft.ApiManagement/service | EventHubDroppedEvents |  Vyřazené události EventHub  | 
| Microsoft.ApiManagement/service | Doba trvání |  Celková doba trvání žádostí o bránu  | 
| Microsoft.ApiManagement/service | BackendDuration |  Doba trvání požadavků back-endu  | 
| Microsoft. DBforMariaDB/servery | storage_used |  Využité úložiště  | 
| Microsoft. DBforMariaDB/servery | storage_percent |  Procento úložiště  | 
| Microsoft. DBforMariaDB/servery | storage_limit |  Omezení úložiště  | 
| Microsoft. DBforMariaDB/servery | serverlog_storage_usage |  Využité úložiště protokolu serveru  | 
| Microsoft. DBforMariaDB/servery | serverlog_storage_percent |  Procentuální hodnota úložiště protokolu serveru  | 
| Microsoft. DBforMariaDB/servery | serverlog_storage_limit |  Limit úložiště protokolu serveru  | 
| Microsoft. DBforMariaDB/servery | seconds_behind_master |  Prodleva replikace v sekundách  | 
| Microsoft. DBforMariaDB/servery | network_bytes_ingress |  Síťové vstupy  | 
| Microsoft. DBforMariaDB/servery | network_bytes_egress |  Síťové výstupy  | 
| Microsoft. DBforMariaDB/servery | memory_percent |  Procentuální hodnota paměti  | 
| Microsoft. DBforMariaDB/servery | io_consumption_percent |  V/v procenta  | 
| Microsoft. DBforMariaDB/servery | cpu_percent |  Procento využití procesoru  | 
| Microsoft. DBforMariaDB/servery | connections_failed |  Neúspěšná připojení  | 
| Microsoft. DBforMariaDB/servery | backup_storage_used |  Využité úložiště záloh  | 
| Microsoft. DBforMariaDB/servery | active_connections |  Aktivní připojení  | 
| Microsoft. DBforMySQL/servery | storage_used |  Využité úložiště  | 
| Microsoft. DBforMySQL/servery | storage_percent |  Procento úložiště  | 
| Microsoft. DBforMySQL/servery | storage_limit |  Omezení úložiště  | 
| Microsoft. DBforMySQL/servery | serverlog_storage_usage |  Využité úložiště protokolu serveru  | 
| Microsoft. DBforMySQL/servery | serverlog_storage_percent |  Procentuální hodnota úložiště protokolu serveru  | 
| Microsoft. DBforMySQL/servery | serverlog_storage_limit |  Limit úložiště protokolu serveru  | 
| Microsoft. DBforMySQL/servery | seconds_behind_master |  Prodleva replikace v sekundách  | 
| Microsoft. DBforMySQL/servery | network_bytes_ingress |  Síťové vstupy  | 
| Microsoft. DBforMySQL/servery | network_bytes_egress |  Síťové výstupy  | 
| Microsoft. DBforMySQL/servery | memory_percent |  Procentuální hodnota paměti  | 
| Microsoft. DBforMySQL/servery | io_consumption_percent |  V/v procenta  | 
| Microsoft. DBforMySQL/servery | cpu_percent |  Procento využití procesoru  | 
| Microsoft. DBforMySQL/servery | connections_failed |  Neúspěšná připojení  | 
| Microsoft. DBforMySQL/servery | backup_storage_used |  Využité úložiště záloh  | 
| Microsoft. DBforMySQL/servery | active_connections |  Aktivní připojení  | 
| Microsoft. zařízení/účet | digitaltwins. telemetrie. Nodes |  Zástupný text telemetrie uzlu digitální vlákna  | 
| Microsoft. Devices/IotHubs | twinQueries. Success |  Úspěšné zdvojené dotazy  | 
| Microsoft. Devices/IotHubs | twinQueries.resultSize |  Velikost výsledku nevlákenných dotazů  | 
| Microsoft. Devices/IotHubs | twinQueries. selhání |  Neúspěšné zdvojené dotazy  | 
| Microsoft. Devices/IotHubs | Jobs. queryJobs. Success |  Úspěšné dotazy na úlohy  | 
| Microsoft. Devices/IotHubs | Jobs. queryJobs. selhání |  Neúspěšné dotazy na úlohy  | 
| Microsoft. Devices/IotHubs | Jobs. listJobs. Success |  Úspěšná volání na seznam úloh  | 
| Microsoft. Devices/IotHubs | Jobs. listJobs. selhání |  Neúspěšná volání pro výpis úloh  | 
| Microsoft. Devices/IotHubs | úlohy. nezdařilo se |  Neúspěšné úlohy  | 
| Microsoft. Devices/IotHubs | Jobs. createTwinUpdateJob. Success |  Úspěšné vytváření zdvojených úloh aktualizace  | 
| Microsoft. Devices/IotHubs | Jobs. createTwinUpdateJob. selhání |  Nepovedlo se vytvořit úlohy s dvojitou aktualizací  | 
| Microsoft. Devices/IotHubs | Jobs. createDirectMethodJob. Success |  Úspěšné vytváření úloh vyvolání metod  | 
| Microsoft. Devices/IotHubs | Jobs. createDirectMethodJob. selhání |  Nepovedlo se vytvořit úlohy vyvolání metody  | 
| Microsoft. Devices/IotHubs | dokončené úlohy |  Dokončené úlohy  | 
| Microsoft. Devices/IotHubs | Jobs. cancelJob. Success |  Úspěšná zrušení úlohy  | 
| Microsoft. Devices/IotHubs | Jobs. cancelJob. selhání |  Neúspěšná zrušení úloh  | 
| Microsoft. Devices/IotHubs | EventGridLatency |  Latence Event Grid (Preview)  | 
| Microsoft. Devices/IotHubs | EventGridDeliveries |  Event Grid doručení (Preview)  | 
| Microsoft. Devices/IotHubs | Devices. totalDevices |  Celkem zařízení (zastaralé)  | 
| Microsoft. Devices/IotHubs | Devices. connectedDevices. allProtocol |  Připojená zařízení (zastaralé)   | 
| Microsoft. Devices/IotHubs | deviceDataUsageV2 |  Celkové využití dat zařízení (Preview)  | 
| Microsoft. Devices/IotHubs | deviceDataUsage |  Celkové využití dat zařízení  | 
| Microsoft. Devices/IotHubs | dailyMessageQuotaUsed |  Celkový počet použitých zpráv  | 
| Microsoft. Devices/IotHubs | D2C. nevlákenná. Update. Success |  Úspěšné nedokončené změny ze zařízení  | 
| Microsoft. Devices/IotHubs | D2C. nevlákenná. Update. Size |  Velikost dvojitě aktualizovaných aktualizací ze zařízení  | 
| Microsoft. Devices/IotHubs | D2C. nevlákenná. aktualizace. selhání |  Neúspěšné aktualizace ze zařízení se zdvojenými chybami  | 
| Microsoft. Devices/IotHubs | D2C. vláken. Read. Success |  Úspěšné čtení ze zařízení  | 
| Microsoft. Devices/IotHubs | D2C. nevlákenný. Read. Size |  Velikost odpovědi u dvojitých čtení ze zařízení  | 
| Microsoft. Devices/IotHubs | D2C. nevlákenný. Read. Failure |  Neúspěšná čtení ze zařízení  | 
| Microsoft. Devices/IotHubs | D2C. telemetrie. příchozí přenos dat. úspěch |  Odeslané zprávy telemetrie  | 
| Microsoft. Devices/IotHubs | D2C. telemetrie. příchozí přenos dat sendThrottle |  Počet chyb omezování  | 
| Microsoft. Devices/IotHubs | D2C. telemetrie. příchozí přenos dat allProtocol |  Počet pokusů o odeslání zprávy telemetrie  | 
| Microsoft. Devices/IotHubs | D2C. telemetrie. odchozí. úspěch |  Směrování: doručené zprávy telemetrie  | 
| Microsoft. Devices/IotHubs | D2C. telemetrie. výstup. osamocený |  Směrování: osamocené zprávy telemetrie   | 
| Microsoft. Devices/IotHubs | D2C. telemetrie. invýstup. neplatné |  Směrování: nekompatibilní zprávy telemetrie  | 
| Microsoft. Devices/IotHubs | D2C. telemetrie. odchozí. Fallback |  Směrování: zprávy doručené do záložního režimu  | 
| Microsoft. Devices/IotHubs | D2C. telemetrie. výstup. vyřazeno |  Směrování: vyřazené zprávy telemetrie   | 
| Microsoft. Devices/IotHubs | D2C. Endpoints. latence. Storage |  Směrování: latence zpráv pro úložiště  | 
| Microsoft. Devices/IotHubs | D2C. Endpoints. latence. serviceBusTopics |  Směrování: latence zprávy pro Service Bus téma  | 
| Microsoft. Devices/IotHubs | D2C. Endpoints. latence. serviceBusQueues |  Směrování: latence zprávy pro Service Bus frontu  | 
| Microsoft. Devices/IotHubs | D2C. Endpoints. latence. eventHubs |  Směrování: latence zprávy pro centrum událostí  | 
| Microsoft. Devices/IotHubs | D2C. Endpoints. latence. builtIn. events |  Směrování: latence zpráv pro zprávy/události  | 
| Microsoft. Devices/IotHubs | D2C. Endpoints. invýstups. Storage. bytes |  Směrování: data Doručená do úložiště  | 
| Microsoft. Devices/IotHubs | D2C. Endpoints. výstup. Storage. BLOBs |  Směrování: objekty blob doručené do úložiště  | 
| Microsoft. Devices/IotHubs | D2C. Endpoints. odchozí úložiště. Storage |  Směrování: zprávy doručené do úložiště  | 
| Microsoft. Devices/IotHubs | D2C. Endpoints. odchozí. serviceBusTopics |  Směrování: zprávy doručené do Service Bus tématu  | 
| Microsoft. Devices/IotHubs | D2C. Endpoints. odchozí. serviceBusQueues |  Směrování: zprávy doručené do fronty Service Bus  | 
| Microsoft. Devices/IotHubs | D2C. Endpoints. odchozí. eventHubs |  Směrování: zprávy doručené do centra událostí  | 
| Microsoft. Devices/IotHubs | D2C. Endpoints. invýstups. builtIns. events |  Směrování: zprávy doručené zprávám/událostem  | 
| Microsoft. Devices/IotHubs | konfiguračních |  Metriky konfigurace  | 
| Microsoft. Devices/IotHubs | C2DMessagesExpired |  C2D zprávy prošly (Preview)  | 
| Microsoft. Devices/IotHubs | C2D. nevlákenná. Update. Success |  Úspěšné zdvojené aktualizace z back-endu  | 
| Microsoft. Devices/IotHubs | C2D. nevlákenná. Update. Size |  Velikost dvojitě aktualizovaných aktualizací z back-endu  | 
| Microsoft. Devices/IotHubs | C2D. nevlákenná. aktualizace. selhání |  Neúspěšné zdvojené aktualizace z back-endu  | 
| Microsoft. Devices/IotHubs | C2D. vláken. Read. Success |  Úspěšné zdvojené čtení z back-endu  | 
| Microsoft. Devices/IotHubs | C2D. nevlákenný. Read. Size |  Velikost odpovědi zdvojeného čtení z back-endu  | 
| Microsoft. Devices/IotHubs | C2D. nevlákenný. Read. Failure |  Neúspěšné čtení z back-endu ze zadních vláken  | 
| Microsoft. Devices/IotHubs | C2D. Methods. Success |  Úspěšná volání přímé metody  | 
| Microsoft. Devices/IotHubs | C2D. Methods. responseSize |  Velikost odezvy volání přímých metod  | 
| Microsoft. Devices/IotHubs | C2D. Methods. requestSize |  Velikost žádosti o vyvolání přímé metody  | 
| Microsoft. Devices/IotHubs | C2D. Methods. Failure |  Neúspěšná volání přímé metody  | 
| Microsoft. Devices/IotHubs | C2D. Commands. odchozí. remítat. Success |  Odmítnuté zprávy C2D  | 
| Microsoft. Devices/IotHubs | C2D. Commands.. Complete. Complete. Success |  Doručení zpráv C2D bylo dokončeno.  | 
| Microsoft. Devices/IotHubs | C2D. Commands. odchozí. Abandon. Success |  Zrušené zprávy C2D  | 
| Microsoft. Devices/provisioningServices | RegistrationAttempts |  Pokusy o registraci  | 
| Microsoft. Devices/provisioningServices | DeviceAssignments |  Přiřazená zařízení  | 
| Microsoft. Devices/provisioningServices | AttestationAttempts |  Pokusy o ověření identity  | 
| Microsoft.DocumentDB/databaseAccounts | TotalRequestUnits |  Celkový počet jednotek žádostí  | 
| Microsoft.DocumentDB/databaseAccounts | TotalRequests |  Požadavky celkem  | 
| Microsoft.DocumentDB/databaseAccounts | MongoRequests |  Žádosti Mongo  | 
| Microsoft.DocumentDB/databaseAccounts | MongoRequestCharge |  Poplatek za požadavek Mongo  | 
| Microsoft. EventGrid/domény | PublishSuccessLatencyInMs |  Latence úspěšného publikování  | 
| Microsoft. EventGrid/domény | PublishSuccessCount |  Publikované události  | 
| Microsoft. EventGrid/domény | PublishFailCount |  Publikovat neúspěšné události  | 
| Microsoft. EventGrid/domény | MatchedEventCount |  Spárované události  | 
| Microsoft. EventGrid/domény | DroppedEventCount |  Vyřazené události  | 
| Microsoft. EventGrid/domény | DeliverySuccessCount |  Doručené události  | 
| Microsoft. EventGrid/domény | DeadLetteredCount |  Nedoručené události s písmeny  | 
| Microsoft. EventGrid/eventSubscriptions | MatchedEventCount |  Spárované události  | 
| Microsoft. EventGrid/eventSubscriptions | DroppedEventCount |  Vyřazené události  | 
| Microsoft. EventGrid/eventSubscriptions | DeliverySuccessCount |  Doručené události  | 
| Microsoft. EventGrid/eventSubscriptions | DeadLetteredCount |  Nedoručené události s písmeny  | 
| Microsoft. EventGrid/extensionTopics | UnmatchedEventCount |  Nespárované události  | 
| Microsoft. EventGrid/extensionTopics | PublishSuccessLatencyInMs |  Latence úspěšného publikování  | 
| Microsoft. EventGrid/extensionTopics | PublishSuccessCount |  Publikované události  | 
| Microsoft. EventGrid/extensionTopics | PublishFailCount |  Publikovat neúspěšné události  | 
| Microsoft. EventGrid/témata | UnmatchedEventCount |  Nespárované události  | 
| Microsoft. EventGrid/témata | PublishSuccessLatencyInMs |  Latence úspěšného publikování  | 
| Microsoft. EventGrid/témata | PublishSuccessCount |  Publikované události  | 
| Microsoft. EventGrid/témata | PublishFailCount |  Publikovat neúspěšné události  | 
| Microsoft. EventHub/clustery | OutgoingMessages |  Odchozí zprávy  | 
| Microsoft. EventHub/clustery | OutgoingBytes |  Odchozí bajty  | 
| Microsoft. EventHub/clustery | IncomingRequests |  Příchozí žádosti  | 
| Microsoft. EventHub/clustery | IncomingMessages |  Příchozí zprávy  | 
| Microsoft. EventHub/clustery | IncomingBytes |  Příchozí bajty  | 
| Microsoft. EventHub/obory názvů | SVRBSY |  Chyby zaneprázdněnosti serveru (zastaralé)  | 
| Microsoft. EventHub/obory názvů | SUCCREQ |  Úspěšné požadavky (zastaralé)  | 
| Microsoft. EventHub/obory názvů | OUTMSGS |  Odchozí zprávy (zastaralé) (zastaralé)  | 
| Microsoft. EventHub/obory názvů | OutgoingMessages |  Odchozí zprávy  | 
| Microsoft. EventHub/obory názvů | OutgoingBytes |  Odchozí bajty  | 
| Microsoft. EventHub/obory názvů | MISCERR |  Další chyby (zastaralé)  | 
| Microsoft. EventHub/obory názvů | MEZI sebou |  Interní chyby serveru (zastaralé)  | 
| Microsoft. EventHub/obory názvů | INREQS |  Příchozí požadavky (zastaralé)  | 
| Microsoft. EventHub/obory názvů | INMSGS |  Příchozí zprávy (zastaralé) (zastaralé)  | 
| Microsoft. EventHub/obory názvů | IncomingRequests |  Příchozí žádosti  | 
| Microsoft. EventHub/obory názvů | IncomingMessages |  Příchozí zprávy  | 
| Microsoft. EventHub/obory názvů | IncomingBytes |  Příchozí bajty  | 
| Microsoft. EventHub/obory názvů | FAILREQ |  Neúspěšné žádosti (zastaralé)  | 
| Microsoft. EventHub/obory názvů | EHOUTMSGS |  Odchozí zprávy (zastaralé)  | 
| Microsoft. EventHub/obory názvů | EHOUTMBS |  Odchozí bajty (zastaralé) (zastaralé)  | 
| Microsoft. EventHub/obory názvů | EHOUTBYTES |  Odchozí bajty (zastaralé)  | 
| Microsoft. EventHub/obory názvů | EHINMSGS |  Příchozí zprávy (zastaralé)  | 
| Microsoft. EventHub/obory názvů | EHINMBS |  Příchozí bajty (zastaralé) (zastaralé)  | 
| Microsoft. EventHub/obory názvů | EHINBYTES |  Příchozí bajty (zastaralé)  | 
| Microsoft. EventHub/obory názvů | EHAMSGS |  Archivní zprávy (zastaralé)  | 
| Microsoft. EventHub/obory názvů | EHAMBS |  Propustnost zpráv archivu (zastaralé)  | 
| Microsoft. EventHub/obory názvů | EHABL |  Archivovat nevyřízené zprávy (zastaralé)  | 
| Microsoft. HDInsight/clustery | NumActiveWorkers |  Počet aktivních pracovníků  | 
| Microsoft. HDInsight/clustery | GatewayRequests |  Žádosti o bránu  | 
| Microsoft. HDInsight/clustery | CategorizedGatewayRequests |  Zařadit požadavky na bránu  | 
| Microsoft. Insights/AutoscaleSettings | ScaleActionsInitiated |  Zahájené akce škálování  | 
| Microsoft. Insights/Components | trasování/počet |  Trasování  | 
| Microsoft. Insights/Components | Čítače výkonu/requestsPerSecond |  Rychlost požadavku HTTP  | 
| Microsoft. Insights/Components | Čítače výkonu/requestsInQueue |  Požadavky HTTP ve frontě aplikací  | 
| Microsoft. Insights/Components | Čítače výkonu/exceptionsPerSecond |  Míra výjimek  | 
| Microsoft. Insights/Components | pageViews/Count |  Zobrazení stránek  | 
| Microsoft. Insights/Components | výjimky/počet |  Výjimky  | 
| Microsoft. Kusto/clustery | StreamingIngestResults |  Výsledek příjmu streamování  | 
| Microsoft. Kusto/clustery | StreamingIngestDuration |  Doba trvání příjmu streamování  | 
| Microsoft. Kusto/clustery | StreamingIngestDataRate |  Datová rychlost příjmu streamování  | 
| Microsoft. Kusto/clustery | SteamingIngestRequestRate |  Četnost požadavků příjmu streamování  | 
| Microsoft. Kusto/clustery | QueryDuration |  Doba trvání dotazu  | 
| Microsoft. Kusto/clustery | Udržení |  Zachovat naživu  | 
| Microsoft. Kusto/clustery | IngestionVolumeInMB |  Objem příjmu (v MB)  | 
| Microsoft. Kusto/clustery | IngestionUtilization |  Využití příjmu  | 
| Microsoft. Kusto/clustery | IngestionResult |  Výsledek ingestování  | 
| Microsoft. Kusto/clustery | IngestionLatencyInSeconds |  Latence příjmu (v sekundách)  | 
| Microsoft. Kusto/clustery | ExportUtilization |  Využití exportu  | 
| Microsoft. Kusto/clustery | EventsProcessedForEventHubs |  Zpracované události (pro Event/centra IoT)  | 
| Microsoft. Kusto/clustery | Procesor |  Procesor  | 
| Microsoft. Kusto/clustery | ContinuousExportResult |  Výsledek průběžného exportu  | 
| Microsoft. Kusto/clustery | ContinuousExportPendingCount |  Počet nevyřízených položek průběžného exportu  | 
| Microsoft. Kusto/clustery | ContinuousExportNumOfRecordsExported |  Průběžný export – počet exportovaných záznamů  | 
| Microsoft. Kusto/clustery | ContinuousExportMaxLatenessMinutes |  Maximální počet minut zpoždění pro průběžný export  | 
| Microsoft. Kusto/clustery | CacheUtilization |  Využití mezipaměti  | 
| Microsoft. Logic/integrationServiceEnvironments | TriggerThrottledEvents |  Omezené události triggeru  | 
| Microsoft. Logic/integrationServiceEnvironments | TriggersSucceeded |  Aktivační události byly úspěšné   | 
| Microsoft. Logic/integrationServiceEnvironments | TriggersStarted |  Spuštěné aktivační události   | 
| Microsoft. Logic/integrationServiceEnvironments | TriggersSkipped |  Aktivační události přeskočeny  | 
| Microsoft. Logic/integrationServiceEnvironments | TriggersFired |  Aktivační události aktivovány   | 
| Microsoft. Logic/integrationServiceEnvironments | TriggersFailed |  Neúspěšná triggery   | 
| Microsoft. Logic/integrationServiceEnvironments | TriggersCompleted |  Aktivační události dokončeny   | 
| Microsoft. Logic/integrationServiceEnvironments | RunThrottledEvents |  Události omezeného spuštění  | 
| Microsoft. Logic/integrationServiceEnvironments | RunStartThrottledEvents |  Spustit omezené události  | 
| Microsoft. Logic/integrationServiceEnvironments | RunsSucceeded |  Úspěšná spuštění  | 
| Microsoft. Logic/integrationServiceEnvironments | RunsStarted |  Spuštěné běhy  | 
| Microsoft. Logic/integrationServiceEnvironments | RunsFailed |  Neúspěšná spuštění  | 
| Microsoft. Logic/integrationServiceEnvironments | RunsCompleted |  Dokončené běhy  | 
| Microsoft. Logic/integrationServiceEnvironments | RunsCancelled |  Zrušené běhy  | 
| Microsoft. Logic/integrationServiceEnvironments | RunFailurePercentage |  Procento selhání spuštění  | 
| Microsoft. Logic/integrationServiceEnvironments | ActionThrottledEvents |  Omezené události akcí  | 
| Microsoft. Logic/integrationServiceEnvironments | ActionsSucceeded |  Úspěšné akce   | 
| Microsoft. Logic/integrationServiceEnvironments | ActionsStarted |  Spuštěné akce   | 
| Microsoft. Logic/integrationServiceEnvironments | ActionsSkipped |  Vynechané akce   | 
| Microsoft. Logic/integrationServiceEnvironments | ActionsFailed |  Neúspěšné akce   | 
| Microsoft. Logic/integrationServiceEnvironments | ActionsCompleted |  Dokončené akce   | 
| Microsoft. Logic/Workflows | TriggerThrottledEvents |  Omezené události triggeru  | 
| Microsoft. Logic/Workflows | TriggersSucceeded |  Aktivační události byly úspěšné   | 
| Microsoft. Logic/Workflows | TriggersStarted |  Spuštěné aktivační události   | 
| Microsoft. Logic/Workflows | TriggersSkipped |  Aktivační události přeskočeny  | 
| Microsoft. Logic/Workflows | TriggersFired |  Aktivační události aktivovány   | 
| Microsoft. Logic/Workflows | TriggersFailed |  Neúspěšná triggery   | 
| Microsoft. Logic/Workflows | TriggersCompleted |  Aktivační události dokončeny   | 
| Microsoft. Logic/Workflows | TotalBillableExecutions |  Fakturovatelná spuštění celkem  | 
| Microsoft. Logic/Workflows | RunThrottledEvents |  Události omezeného spuštění  | 
| Microsoft. Logic/Workflows | RunStartThrottledEvents |  Spustit omezené události  | 
| Microsoft. Logic/Workflows | RunsSucceeded |  Úspěšná spuštění  | 
| Microsoft. Logic/Workflows | RunsStarted |  Spuštěné běhy  | 
| Microsoft. Logic/Workflows | RunsFailed |  Neúspěšná spuštění  | 
| Microsoft. Logic/Workflows | RunsCompleted |  Dokončené běhy  | 
| Microsoft. Logic/Workflows | RunsCancelled |  Zrušené běhy  | 
| Microsoft. Logic/Workflows | RunFailurePercentage |  Procento selhání spuštění  | 
| Microsoft. Logic/Workflows | BillingUsageStorageConsumption |  Využití fakturace pro provádění spotřeby úložiště  | 
| Microsoft. Logic/Workflows | BillingUsageStorageConsumption |  Využití fakturace pro provádění spotřeby úložiště  | 
| Microsoft. Logic/Workflows | BillingUsageStandardConnector |  Využití fakturace pro spuštění standardních konektorů  | 
| Microsoft. Logic/Workflows | BillingUsageStandardConnector |  Využití fakturace pro spuštění standardních konektorů  | 
| Microsoft. Logic/Workflows | BillingUsageNativeOperation |  Využití fakturace pro provádění nativních operací  | 
| Microsoft. Logic/Workflows | BillingUsageNativeOperation |  Využití fakturace pro provádění nativních operací  | 
| Microsoft. Logic/Workflows | BillableTriggerExecutions |  Fakturovatelná spuštění triggerů  | 
| Microsoft. Logic/Workflows | BillableActionExecutions |  Fakturovatelné provádění akcí  | 
| Microsoft. Logic/Workflows | ActionThrottledEvents |  Omezené události akcí  | 
| Microsoft. Logic/Workflows | ActionsSucceeded |  Úspěšné akce   | 
| Microsoft. Logic/Workflows | ActionsStarted |  Spuštěné akce   | 
| Microsoft. Logic/Workflows | ActionsSkipped |  Vynechané akce   | 
| Microsoft. Logic/Workflows | ActionsFailed |  Neúspěšné akce   | 
| Microsoft. Logic/Workflows | ActionsCompleted |  Dokončené akce   | 
| Microsoft. Network/frontdoors | WebApplicationFirewallRequestCount |  Počet požadavků firewallu webových aplikací  | 
| Microsoft. Network/frontdoors | TotalLatency |  Celková latence  | 
| Microsoft. Network/frontdoors | ResponseSize |  Velikost odpovědi  | 
| Microsoft. Network/frontdoors | RequestSize |  Velikost požadavku  | 
| Microsoft. Network/frontdoors | RequestCount |  Počet požadavků  | 
| Microsoft. Network/frontdoors | BillableResponseSize |  Fakturovatelná velikost odpovědi  | 
| Microsoft. Network/frontdoors | BackendRequestLatency |  Latence žádosti back-endu  | 
| Microsoft. Network/frontdoors | BackendRequestCount |  Počet požadavků back-endu  | 
| Microsoft. Network/frontdoors | BackendHealthPercentage |  Procento stavu back-endu  | 
| Microsoft. Network/trafficManagerProfiles | QpsByEndpoint |  Dotazy podle vráceného koncového bodu  | 
| Microsoft. NotificationHubs/obory názvů/NotificationHubs | naplánováno. čeká na |  Nedokončená plánovaná oznámení  | 
| Microsoft. NotificationHubs/obory názvů/NotificationHubs | registrace. aktualizace |  Operace aktualizace registrace  | 
| Microsoft. NotificationHubs/obory názvů/NotificationHubs | registrace. Get |  Registrace – operace čtení  | 
| Microsoft. NotificationHubs/obory názvů/NotificationHubs | registrace. odstranění |  Registrace – operace odstranění  | 
| Microsoft. NotificationHubs/obory názvů/NotificationHubs | registrace. Create |  Operace vytvoření registrace  | 
| Microsoft. NotificationHubs/obory názvů/NotificationHubs | registrace. vše |  Operace registrace  | 
| Microsoft. NotificationHubs/obory názvů/NotificationHubs | odchozí. WNS. wrongtoken |  WNS chyby autorizace (špatný token)  | 
| Microsoft. NotificationHubs/obory názvů/NotificationHubs | odchozí. WNS. tokenproviderunreachable |  WNS – chyby autorizace (nedostupné)  | 
| Microsoft. NotificationHubs/obory názvů/NotificationHubs | odchozí. WNS. omezení |  WNS omezená oznámení  | 
| Microsoft. NotificationHubs/obory názvů/NotificationHubs | odchozí. WNS. úspěch |  WNS úspěšná oznámení  | 
| Microsoft. NotificationHubs/obory názvů/NotificationHubs | odchozí. WNS. pnserror |  WNS chyby  | 
| Microsoft. NotificationHubs/obory názvů/NotificationHubs | odchozí. WNS. invalidtoken |  WNS – chyby autorizace (neplatný token)  | 
| Microsoft. NotificationHubs/obory názvů/NotificationHubs | odchozí. WNS. invalidnotificationsize |  WNSá Chyba neplatné velikosti oznámení  | 
| Microsoft. NotificationHubs/obory názvů/NotificationHubs | odchozí. WNS. invalidnotificationformat |  WNS neplatný formát oznámení  | 
| Microsoft. NotificationHubs/obory názvů/NotificationHubs | odchozí. WNS. invalidcredentials |  WNS – chyby autorizace (neplatné přihlašovací údaje)  | 
| Microsoft. NotificationHubs/obory názvů/NotificationHubs | odchozí. WNS. expiredchannel |  Chyba kanálu vypršení platnosti WNS  | 
| Microsoft. NotificationHubs/obory názvů/NotificationHubs | odchozí. WNS. vyřazeno |  WNS Vyřazená oznámení  | 
| Microsoft. NotificationHubs/obory názvů/NotificationHubs | odchozí. WNS. channelthrottled |  WNS kanál – omezení  | 
| Microsoft. NotificationHubs/obory názvů/NotificationHubs | odchozí. WNS. channeldisconnected |  WNS kanál odpojen  | 
| Microsoft. NotificationHubs/obory názvů/NotificationHubs | odchozí. WNS. badchannel |  Chyba WNS špatného kanálu  | 
| Microsoft. NotificationHubs/obory názvů/NotificationHubs | odchozí. WNS. authenticationerror |  WNS chyby ověřování  | 
| Microsoft. NotificationHubs/obory názvů/NotificationHubs | odchozí. MPNS. omezení |  MPNS omezená oznámení  | 
| Microsoft. NotificationHubs/obory názvů/NotificationHubs | odchozí. MPNS. úspěch |  MPNS úspěšná oznámení  | 
| Microsoft. NotificationHubs/obory názvů/NotificationHubs | odchozí. MPNS. pnserror |  MPNS chyby  | 
| Microsoft. NotificationHubs/obory názvů/NotificationHubs | odchozí. MPNS. invalidnotificationformat |  MPNS neplatný formát oznámení  | 
| Microsoft. NotificationHubs/obory názvů/NotificationHubs | odchozí. MPNS. invalidcredentials |  Neplatné přihlašovací údaje MPNS  | 
| Microsoft. NotificationHubs/obory názvů/NotificationHubs | odchozí. MPNS. vyřazeno |  MPNS Vyřazená oznámení  | 
| Microsoft. NotificationHubs/obory názvů/NotificationHubs | odchozí. MPNS. channeldisconnected |  MPNS kanál odpojen  | 
| Microsoft. NotificationHubs/obory názvů/NotificationHubs | odchozí. MPNS. badchannel |  Chyba MPNS špatného kanálu  | 
| Microsoft. NotificationHubs/obory názvů/NotificationHubs | odchozí. MPNS. authenticationerror |  MPNS chyby ověřování  | 
| Microsoft. NotificationHubs/obory názvů/NotificationHubs | odchozí. GCM. wrongchannel |  GCM chybná chyba kanálu  | 
| Microsoft. NotificationHubs/obory názvů/NotificationHubs | odchozí. GCM. omezení |  GCM omezená oznámení  | 
| Microsoft. NotificationHubs/obory názvů/NotificationHubs | odchozí. GCM. úspěch |  GCM úspěšná oznámení  | 
| Microsoft. NotificationHubs/obory názvů/NotificationHubs | odchozí. GCM. pnserror |  GCM chyby  | 
| Microsoft. NotificationHubs/obory názvů/NotificationHubs | odchozí. GCM. invalidnotificationsize |  GCMá Chyba neplatné velikosti oznámení  | 
| Microsoft. NotificationHubs/obory názvů/NotificationHubs | odchozí. GCM. invalidnotificationformat |  GCM neplatný formát oznámení  | 
| Microsoft. NotificationHubs/obory názvů/NotificationHubs | odchozí. GCM. invalidcredentials |  GCM – chyby autorizace (neplatné přihlašovací údaje)  | 
| Microsoft. NotificationHubs/obory názvů/NotificationHubs | odchozí. GCM. expiredchannel |  Chyba kanálu vypršení platnosti GCM  | 
| Microsoft. NotificationHubs/obory názvů/NotificationHubs | odchozí. GCM. badchannel |  Chyba GCM špatného kanálu  | 
| Microsoft. NotificationHubs/obory názvů/NotificationHubs | odchozí. GCM. authenticationerror |  GCM chyby ověřování  | 
| Microsoft. NotificationHubs/obory názvů/NotificationHubs | odchozí. APNs. Success |  Úspěšná oznámení APNS  | 
| Microsoft. NotificationHubs/obory názvů/NotificationHubs | odchozí. APNs. pnserror |  Chyby služby APN  | 
| Microsoft. NotificationHubs/obory názvů/NotificationHubs | odchozí. APNs. invalidnotificationsize |  APN – Chyba neplatné velikosti oznámení  | 
| Microsoft. NotificationHubs/obory názvů/NotificationHubs | odchozí. APNs. invalidcredentials |  Chyby autorizace APNS  | 
| Microsoft. NotificationHubs/obory názvů/NotificationHubs | odchozí. APNs. expiredchannel |  Chyba kanálu vypršení platnosti služby APNS  | 
| Microsoft. NotificationHubs/obory názvů/NotificationHubs | odchozí. APNs. badchannel |  Chyba služby APN Bad Channel  | 
| Microsoft. NotificationHubs/obory názvů/NotificationHubs | odchozí. allpns. úspěch |  Úspěšná oznámení  | 
| Microsoft. NotificationHubs/obory názvů/NotificationHubs | odchozí. allpns. pnserror |  Chyby systému externích oznámení  | 
| Microsoft. NotificationHubs/obory názvů/NotificationHubs | odchozí. allpns. invalidpayload |  Chyby datové části  | 
| Microsoft. NotificationHubs/obory názvů/NotificationHubs | odchozí. allpns. channelerror |  Chyby kanálu  | 
| Microsoft. NotificationHubs/obory názvů/NotificationHubs | notificationhub. push |  Všechna odchozí oznámení  | 
| Microsoft. NotificationHubs/obory názvů/NotificationHubs | instalace. Upsert |  Operace vytvoření nebo aktualizace instalace  | 
| Microsoft. NotificationHubs/obory názvů/NotificationHubs | instalace. patch |  Operace instalace opravy  | 
| Microsoft. NotificationHubs/obory názvů/NotificationHubs | instalace. Get |  Získat operace instalace  | 
| Microsoft. NotificationHubs/obory názvů/NotificationHubs | instalace. odstranit |  Odstranit operace instalace  | 
| Microsoft. NotificationHubs/obory názvů/NotificationHubs | instalace. vše |  Operace správy instalace  | 
| Microsoft. NotificationHubs/obory názvů/NotificationHubs | příchozí. plánováno. Cancel |  Zrušená plánovaná nabízená oznámení  | 
| Microsoft. NotificationHubs/obory názvů/NotificationHubs | příchozí. plánováno |  Odeslaná naplánovaná nabízená oznámení  | 
| Microsoft. NotificationHubs/obory názvů/NotificationHubs | příchozí. All. – žádosti |  Všechny příchozí žádosti  | 
| Microsoft. NotificationHubs/obory názvů/NotificationHubs | příchozí. All. failedrequests |  Všechny příchozí neúspěšné požadavky  | 
| Microsoft. NotificationHubs/obory názvů/NotificationHubs | přijíman |  Příchozí zprávy  | 
| Microsoft. OperationalInsights/pracovní prostory | Tep |  Tep  | 
| Microsoft. Relay/obory názvů | BytesTransferred |  BytesTransferred  | 
| Microsoft. ServiceBus/obory názvů | OutgoingMessages |  Odchozí zprávy  | 
| Microsoft. ServiceBus/obory názvů | IncomingRequests |  Příchozí žádosti  | 
| Microsoft. ServiceBus/obory názvů | IncomingMessages |  Příchozí zprávy  | 
| Microsoft. SignalRService/Signaler | UserErrors |  Chyby uživatele  | 
| Microsoft. SignalRService/Signaler | SystemErrors |  Systémové chyby  | 
| Microsoft. SignalRService/Signaler | OutboundTraffic |  Odchozí provoz  | 
| Microsoft. SignalRService/Signaler | MessageCount |  Počet zpráv  | 
| Microsoft. SignalRService/Signaler | InboundTraffic |  Příchozí provoz  | 
| Microsoft. SignalRService/Signaler | ConnectionCount |  Počet připojení  | 
| Microsoft. SQL/managedInstances | avg_cpu_percent |  Průměrné procento procesoru  | 
| Microsoft. SQL/servery | dtu_used |  Využité DTU  | 
| Microsoft. SQL/servery | dtu_consumption_percent |  Procento DTU  | 
| Microsoft. SQL/servery/databáze | xtp_storage_percent |  In-Memory OLTP úložiště v procentech  | 
| Microsoft. SQL/servery/databáze | workers_percent |  Procento pracovních procesů  | 
| Microsoft. SQL/servery/databáze | sessions_percent |  Procento relací  | 
| Microsoft. SQL/servery/databáze | physical_data_read_percent |  Procento datových V/V  | 
| Microsoft. SQL/servery/databáze | log_write_percent |  Procentní hodnota protokolu v/v  | 
| Microsoft. SQL/servery/databáze | dwu_used |  DWU použito  | 
| Microsoft. SQL/servery/databáze | dwu_consumption_percent |  Procento DWU  | 
| Microsoft. SQL/servery/databáze | dtu_used |  Využité DTU  | 
| Microsoft. SQL/servery/databáze | dtu_consumption_percent |  Procento DTU  | 
| Microsoft. SQL/servery/databáze | ukončení |  Zablokování  | 
| Microsoft. SQL/servery/databáze | cpu_used |  Využitý procesor  | 
| Microsoft. SQL/servery/databáze | cpu_percent |  Procento CPU  | 
| Microsoft. SQL/servery/databáze | connection_successful |  Úspěšná připojení  | 
| Microsoft. SQL/servery/databáze | connection_failed |  Neúspěšná připojení  | 
| Microsoft. SQL/servery/databáze | cache_hit_percent |  Procento přístupů do mezipaměti  | 
| Microsoft. SQL/servery/databáze | blocked_by_firewall |  Blokováno bránou firewall  | 
| Microsoft. SQL/servery/elasticPools | xtp_storage_percent |  In-Memory OLTP úložiště v procentech  | 
| Microsoft. SQL/servery/elasticPools | workers_percent |  Procento pracovních procesů  | 
| Microsoft. SQL/servery/elasticPools | sessions_percent |  Procento relací  | 
| Microsoft. SQL/servery/elasticPools | physical_data_read_percent |  Procento datových V/V  | 
| Microsoft. SQL/servery/elasticPools | log_write_percent |  Procentní hodnota protokolu v/v  | 
| Microsoft. SQL/servery/elasticPools | eDTU_used |  využité eDTU  | 
| Microsoft. SQL/servery/elasticPools | dtu_consumption_percent |  Procento DTU  | 
| Microsoft. SQL/servery/elasticPools | cpu_percent |  Procento CPU  | 
| Microsoft. Web/hostingEnvironments/multiRolePools | TotalFrontEnds |  Celkový počet front-endy  | 
| Microsoft. Web/hostingEnvironments/multiRolePools | SmallAppServicePlanInstances |  Plánování pracovníků malých App Service  | 
| Microsoft. Web/hostingEnvironments/multiRolePools | Žádosti |  Žádosti  | 
| Microsoft. Web/hostingEnvironments/multiRolePools | MemoryPercentage |  Procento paměti  | 
| Microsoft. Web/hostingEnvironments/multiRolePools | MediumAppServicePlanInstances |  Střední App Service plánování pracovních procesů  | 
| Microsoft. Web/hostingEnvironments/multiRolePools | LargeAppServicePlanInstances |  Zaměstnanci s velkými App Servicey plánu  | 
| Microsoft. Web/hostingEnvironments/multiRolePools | HttpQueueLength |  Délka fronty http  | 
| Microsoft. Web/hostingEnvironments/multiRolePools | Http5xx |  Chyby serveru http  | 
| Microsoft. Web/hostingEnvironments/multiRolePools | Http4xx |  4xx http  | 
| Microsoft. Web/hostingEnvironments/multiRolePools | Http406 |  Http 406  | 
| Microsoft. Web/hostingEnvironments/multiRolePools | Http404 |  HTTP 404  | 
| Microsoft. Web/hostingEnvironments/multiRolePools | Http403 |  HTTP 403  | 
| Microsoft. Web/hostingEnvironments/multiRolePools | Http401 |  HTTP 401  | 
| Microsoft. Web/hostingEnvironments/multiRolePools | Http3xx |  3xx http  | 
| Microsoft. Web/hostingEnvironments/multiRolePools | Http2xx |  2xx http  | 
| Microsoft. Web/hostingEnvironments/multiRolePools | Http101 |  Http 101  | 
| Microsoft. Web/hostingEnvironments/multiRolePools | DiskQueueLength |  Délka fronty disku  | 
| Microsoft. Web/hostingEnvironments/multiRolePools | CpuPercentage |  Procento procesoru  | 
| Microsoft. Web/hostingEnvironments/multiRolePools | BytesSent |  Výstupní data  | 
| Microsoft. Web/hostingEnvironments/multiRolePools | BytesReceived |  Data v  | 
| Microsoft. Web/hostingEnvironments/multiRolePools | AverageResponseTime |  Průměrná doba odezvy  | 
| Microsoft. Web/hostingEnvironments/multiRolePools | ActiveRequests |  Aktivní požadavky  | 
| Microsoft. Web/hostingEnvironments/workerPools | WorkersUsed |  Využívané pracovní procesy  | 
| Microsoft. Web/hostingEnvironments/workerPools | WorkersTotal |  Celkový počet pracovníků  | 
| Microsoft. Web/hostingEnvironments/workerPools | WorkersAvailable |  Zaměstnanci, kteří jsou k dispozici  | 
| Microsoft. Web/hostingEnvironments/workerPools | MemoryPercentage |  Procento paměti  | 
| Microsoft. Web/hostingEnvironments/workerPools | CpuPercentage |  Procento procesoru  | 
| Microsoft. Web/serverových farem | TcpTimeWait |  Doba čekání protokolu TCP  | 
| Microsoft. Web/serverových farem | TcpSynSent |  Odesláno TCP syn  | 
| Microsoft. Web/serverových farem | TcpSynReceived |  Přijato TCP syn  | 
| Microsoft. Web/serverových farem | TcpLastAck |  Poslední potvrzení TCP  | 
| Microsoft. Web/serverových farem | TcpFinWait2 |  TCP FIN – čekání 2  | 
| Microsoft. Web/serverových farem | TcpFinWait1 |  TCP FIN – čekání 1  | 
| Microsoft. Web/serverových farem | TcpEstablished |  TCP – vytvořeno  | 
| Microsoft. Web/serverových farem | TcpClosing |  Ukončení protokolu TCP  | 
| Microsoft. Web/serverových farem | TcpCloseWait |  Ukončení čekání protokolu TCP  | 
| Microsoft. Web/serverových farem | MemoryPercentage |  Procento paměti  | 
| Microsoft. Web/serverových farem | HttpQueueLength |  Délka fronty http  | 
| Microsoft. Web/serverových farem | DiskQueueLength |  Délka fronty disku  | 
| Microsoft. Web/serverových farem | CpuPercentage |  Procento procesoru  | 
| Microsoft. Web/serverových farem | BytesSent |  Výstupní data  | 
| Microsoft. Web/serverových farem | BytesReceived |  Data v  | 
| Microsoft. Web/weby | TotalAppDomainsUnloaded |  Celkový počet uvolněných domén aplikace  | 
| Microsoft. Web/weby | TotalAppDomains |  Celkový počet domén aplikace  | 
| Microsoft. Web/weby | Vlákna |  Počet vláken  | 
| Microsoft. Web/weby | RequestsInApplicationQueue |  Žádosti ve frontě aplikací  | 
| Microsoft. Web/weby | Žádosti |  Žádosti  | 
| Microsoft. Web/weby | PrivateBytes |  Soukromé bajty  | 
| Microsoft. Web/weby | MemoryWorkingSet |  Pracovní sada paměti  | 
| Microsoft. Web/weby | IoWriteOperationsPerSecond |  Vstupně-výstupní operace zápisu za sekundu  | 
| Microsoft. Web/weby | IoWriteBytesPerSecond |  Vstupně-výstupní bajty zápisu za sekundu  | 
| Microsoft. Web/weby | IoReadOperationsPerSecond |  Vstupně-výstupní operace čtení za sekundu  | 
| Microsoft. Web/weby | IoReadBytesPerSecond |  Bajty čtení v/v za sekundu  | 
| Microsoft. Web/weby | IoOtherOperationsPerSecond |  V/v – ostatní operace za sekundu  | 
| Microsoft. Web/weby | IoOtherBytesPerSecond |  IO – ostatní bajty za sekundu  | 
| Microsoft. Web/weby | HttpResponseTime |  Doba odezvy  | 
| Microsoft. Web/weby | Http5xx |  Chyby serveru http  | 
| Microsoft. Web/weby | Http4xx |  4xx http  | 
| Microsoft. Web/weby | Http406 |  Http 406  | 
| Microsoft. Web/weby | Http404 |  HTTP 404  | 
| Microsoft. Web/weby | Http403 |  HTTP 403  | 
| Microsoft. Web/weby | Http401 |  HTTP 401  | 
| Microsoft. Web/weby | Http3xx |  3xx http  | 
| Microsoft. Web/weby | Http2xx |  2xx http  | 
| Microsoft. Web/weby | Http101 |  Http 101  | 
| Microsoft. Web/weby | HealthCheckStatus |  Stav kontroly stavu  | 
| Microsoft. Web/weby | Handles |  Počet popisovačů  | 
| Microsoft. Web/weby | Gen2Collections |  Uvolňování paměti 2. generace  | 
| Microsoft. Web/weby | Gen1Collections |  Generace paměti 1. generace  | 
| Microsoft. Web/weby | Gen0Collections |  Generace paměti gen 0  | 
| Microsoft. Web/weby | FunctionExecutionUnits |  Jednotky spuštění funkce  | 
| Microsoft. Web/weby | FunctionExecutionCount |  Počet spuštění funkce  | 
| Microsoft. Web/weby | CurrentAssemblies |  Aktuální sestavení  | 
| Microsoft. Web/weby | CpuTime |  Čas procesoru  | 
| Microsoft. Web/weby | BytesSent |  Výstupní data  | 
| Microsoft. Web/weby | BytesReceived |  Data v  | 
| Microsoft. Web/weby | AverageResponseTime |  Průměrná doba odezvy  | 
| Microsoft. Web/weby | AverageMemoryWorkingSet |  Průměrná pracovní sada paměti  | 
| Microsoft. Web/weby | AppConnections |  Připojení  | 
| Microsoft. Web/weby/sloty | TotalAppDomainsUnloaded |  Celkový počet uvolněných domén aplikace  | 
| Microsoft. Web/weby/sloty | TotalAppDomains |  Celkový počet domén aplikace  | 
| Microsoft. Web/weby/sloty | Vlákna |  Počet vláken  | 
| Microsoft. Web/weby/sloty | RequestsInApplicationQueue |  Žádosti ve frontě aplikací  | 
| Microsoft. Web/weby/sloty | Žádosti |  Žádosti  | 
| Microsoft. Web/weby/sloty | PrivateBytes |  Soukromé bajty  | 
| Microsoft. Web/weby/sloty | MemoryWorkingSet |  Pracovní sada paměti  | 
| Microsoft. Web/weby/sloty | IoWriteOperationsPerSecond |  Vstupně-výstupní operace zápisu za sekundu  | 
| Microsoft. Web/weby/sloty | IoWriteBytesPerSecond |  Vstupně-výstupní bajty zápisu za sekundu  | 
| Microsoft. Web/weby/sloty | IoReadOperationsPerSecond |  Vstupně-výstupní operace čtení za sekundu  | 
| Microsoft. Web/weby/sloty | IoReadBytesPerSecond |  Bajty čtení v/v za sekundu  | 
| Microsoft. Web/weby/sloty | IoOtherOperationsPerSecond |  V/v – ostatní operace za sekundu  | 
| Microsoft. Web/weby/sloty | IoOtherBytesPerSecond |  IO – ostatní bajty za sekundu  | 
| Microsoft. Web/weby/sloty | HttpResponseTime |  Doba odezvy  | 
| Microsoft. Web/weby/sloty | Http5xx |  Chyby serveru http  | 
| Microsoft. Web/weby/sloty | Http4xx |  4xx http  | 
| Microsoft. Web/weby/sloty | Http406 |  Http 406  | 
| Microsoft. Web/weby/sloty | Http404 |  HTTP 404  | 
| Microsoft. Web/weby/sloty | Http403 |  HTTP 403  | 
| Microsoft. Web/weby/sloty | Http401 |  HTTP 401  | 
| Microsoft. Web/weby/sloty | Http3xx |  3xx http  | 
| Microsoft. Web/weby/sloty | Http2xx |  2xx http  | 
| Microsoft. Web/weby/sloty | Http101 |  Http 101  | 
| Microsoft. Web/weby/sloty | HealthCheckStatus |  Stav kontroly stavu  | 
| Microsoft. Web/weby/sloty | Handles |  Počet popisovačů  | 
| Microsoft. Web/weby/sloty | Gen2Collections |  Uvolňování paměti 2. generace  | 
| Microsoft. Web/weby/sloty | Gen1Collections |  Generace paměti 1. generace  | 
| Microsoft. Web/weby/sloty | Gen0Collections |  Generace paměti gen 0  | 
| Microsoft. Web/weby/sloty | FunctionExecutionUnits |  Jednotky spuštění funkce  | 
| Microsoft. Web/weby/sloty | FunctionExecutionCount |  Počet spuštění funkce  | 
| Microsoft. Web/weby/sloty | CurrentAssemblies |  Aktuální sestavení  | 
| Microsoft. Web/weby/sloty | CpuTime |  Čas procesoru  | 
| Microsoft. Web/weby/sloty | BytesSent |  Výstupní data  | 
| Microsoft. Web/weby/sloty | BytesReceived |  Data v  | 
| Microsoft. Web/weby/sloty | AverageResponseTime |  Průměrná doba odezvy  | 
| Microsoft. Web/weby/sloty | AverageMemoryWorkingSet |  Průměrná pracovní sada paměti  | 
| Microsoft. Web/weby/sloty | AppConnections |  Připojení  | 
| Microsoft. SQL/servery/databáze | cpu_percent | Procento CPU | 
| Microsoft. SQL/servery/databáze | physical_data_read_percent | Procento datových V/V | 
| Microsoft. SQL/servery/databáze | log_write_percent | Procentní hodnota protokolu v/v | 
| Microsoft. SQL/servery/databáze | dtu_consumption_percent | Procento DTU | 
| Microsoft. SQL/servery/databáze | connection_successful | Úspěšná připojení | 
| Microsoft. SQL/servery/databáze | connection_failed | Neúspěšná připojení | 
| Microsoft. SQL/servery/databáze | blocked_by_firewall | Blokováno bránou firewall | 
| Microsoft. SQL/servery/databáze | ukončení | Zablokování | 
| Microsoft. SQL/servery/databáze | xtp_storage_percent | In-Memory OLTP úložiště v procentech | 
| Microsoft. SQL/servery/databáze | workers_percent | Procento pracovních procesů | 
| Microsoft. SQL/servery/databáze | sessions_percent | Procento relací | 
| Microsoft. SQL/servery/databáze | dtu_used | Využité DTU | 
| Microsoft. SQL/servery/databáze | cpu_used | Využitý procesor | 
| Microsoft. SQL/servery/databáze | dwu_consumption_percent | Procento DWU | 
| Microsoft. SQL/servery/databáze | dwu_used | DWU použito | 
| Microsoft. SQL/servery/databáze | cache_hit_percent | Procento přístupů do mezipaměti | 
| Microsoft. SQL/servery/databáze | wlg_allocation_relative_to_system_percent | Přidělení skupiny úloh podle systémových procent | 
| Microsoft. SQL/servery/databáze | wlg_allocation_relative_to_wlg_effective_cap_percent | Přidělení skupiny úloh podle maximálního procenta prostředků | 
| Microsoft. SQL/servery/databáze | wlg_active_queries | Aktivní dotazy skupiny úloh | 
| Microsoft. SQL/servery/databáze | wlg_queued_queries | Dotazování skupin úloh ve frontě | 
| Microsoft. SQL/servery/databáze | active_queries | Aktivní dotazy | 
| Microsoft. SQL/servery/databáze | queued_queries | Dotazy ve frontě | 
| Microsoft. SQL/servery/databáze | wlg_active_queries_timeouts | Vypršení časového limitu dotazu skupiny úloh | 
| Microsoft. SQL/servery/databáze | wlg_queued_queries_timeouts | Časový limit dotazu pro skupinu úloh ve frontě | 
| Microsoft. SQL/servery/databáze | wlg_effective_min_resource_percent | Efektivní minimální procento prostředků | 
| Microsoft. SQL/servery/databáze | wlg_effective_cap_resource_percent | Procento efektivního Cap prostředku | 
| Microsoft. SQL/servery/elasticPools | cpu_percent | Procento CPU | 
| Microsoft. SQL/servery/elasticPools | physical_data_read_percent | Procento datových V/V | 
| Microsoft. SQL/servery/elasticPools | log_write_percent | Procentní hodnota protokolu v/v | 
| Microsoft. SQL/servery/elasticPools | dtu_consumption_percent | Procento DTU | 
| Microsoft. SQL/servery/elasticPools | workers_percent | Procento pracovních procesů | 
| Microsoft. SQL/servery/elasticPools | sessions_percent | Procento relací | 
| Microsoft. SQL/servery/elasticPools | eDTU_used | využité eDTU | 
| Microsoft. SQL/servery/elasticPools | xtp_storage_percent | In-Memory OLTP úložiště v procentech | 
| Microsoft. SQL/servery | dtu_consumption_percent | Procento DTU | 
| Microsoft. SQL/servery | dtu_used | Využité DTU | 
| Microsoft. SQL/managedInstances | avg_cpu_percent | Průměrné procento procesoru | 

