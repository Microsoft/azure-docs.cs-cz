---
title: Pochopit, jak funguje dobrovolné migračního nástroje Azure Monitor výstrah
description: Pochopit, jak funguje nástroj pro migraci výstrahy a řešit problémy.
author: snehithm
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 06/19/2019
ms.author: snmuvva
ms.subservice: alerts
ms.openlocfilehash: 015000388c5629dbd8ed8833931a809ebd738bd6
ms.sourcegitcommit: 2d3b1d7653c6c585e9423cf41658de0c68d883fa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/20/2019
ms.locfileid: "67295530"
---
# <a name="understand-how-the-migration-tool-works"></a>Pochopit, jak funguje nástroj pro migraci

Jako [jsme oznámili už dřív](monitoring-classic-retirement.md), klasických upozornění ve službě Azure Monitor se vyřazuje podle dne 31. května 2019 (byla původně 30 června. května 2019). Migrační nástroj je dostupný na webu Azure Portal pro zákazníky používající klasický pravidla upozornění a který chcete spustit migrace sami.

Tento článek vysvětluje, jak funguje nástroj pro migraci dobrovolná. Popisuje také nápravná opatření pro některé běžné problémy.

> [!NOTE]
> Datum vyřazení pro migraci klasických upozornění byla z důvodu zpoždění při uvádění nástroj pro migraci [rozšířená tak, aby 31. srpna 2019](https://azure.microsoft.com/updates/azure-monitor-classic-alerts-retirement-date-extended-to-august-31st-2019/) od původně oznámené data 30. června 2019.

## <a name="classic-alert-rules-that-will-not-be-migrated"></a>Klasické pravidel upozornění, které se nebudou migrovat

> [!IMPORTANT]
> Upozornění protokolu aktivit (včetně výstrah stavu služby) a upozornění protokolů nejsou vliv migrace. Migrace se vztahuje pouze na klasické upozornění pravidel popsaných [tady](monitoring-classic-retirement.md#retirement-of-classic-monitoring-and-alerting-platform).

Přestože nástroj můžete migrovat téměř všechny [classic pravidla upozornění](monitoring-classic-retirement.md#retirement-of-classic-monitoring-and-alerting-platform), existují některé výjimky. Následující pravidla upozornění se nemigruje s použitím nástroje (nebo při automatické migraci od září 2019):

- Klasické pravidla upozornění na metriky hosta virtuálního počítače (Windows nebo Linuxem). Zobrazit [pokyny k opětovnému vytváření těchto pravidel upozornění v nové upozornění na metriky](#guest-metrics-on-virtual-machines) dále v tomto článku.
- Klasické pravidla upozornění na metriky klasického úložiště. Zobrazit [pokyny pro monitorování účtů klasického úložiště](https://azure.microsoft.com/blog/modernize-alerting-using-arm-storage-accounts/).
- Klasické pravidla upozornění na některé metriky účtu úložiště. Zobrazit [podrobnosti](#storage-account-metrics) dále v tomto článku.
- Klasické pravidla upozornění na některé metriky služby Cosmos DB. Podrobnosti o bude přidána v budoucí aktualizaci.

Pokud vaše předplatné má tato pravidla classic, musíte je migrovat ručně. Protože neposkytujeme automatické migrace, všechny existující, klasického upozornění metrik z těchto typů budou nadále fungovat až do roku 2020 dne. Toto rozšíření nabízí vám čas přesunout do nové výstrahy. Však žádné nové výstrahy classic je možné vytvořit po srpnu 2019.

> [!NOTE]
> Kromě výše uvedených výjimky, pokud pravidel upozornění classic jsou neplatné například se nachází na [zastaralé metriky](#classic-alert-rules-on-deprecated-metrics) nebo prostředky, které se odstranily, se nemigruje během migrace dobrovolná. Při automatické migraci se stane, se odstraní všechna taková neplatný klasického pravidla upozornění.

### <a name="guest-metrics-on-virtual-machines"></a>Metrik hosta na virtuálních počítačích

Než budete moct vytvořit nové upozornění na metriky týkající se metrik hosta, metrik hosta se musí odeslat na úložiště vlastních metrik Azure monitoru. Postupujte podle těchto pokynů a povolte jímka Azure monitoru v nastavení diagnostiky:

- [Zapnutí metrik hosta pro virtuální počítače s Windows](collect-custom-metrics-guestos-resource-manager-vm.md)
- [Povolení metrik hosta pro virtuální počítače s Linuxem](collect-custom-metrics-linux-telegraf.md)

Po dokončení těchto kroků můžete vytvořit nové upozornění na metriky týkající se metrik hosta. A po vytvoření nové upozornění na metriky, můžete odstranit upozornění classic.

### <a name="storage-account-metrics"></a>Metriky účtu úložiště

S výjimkou oznámení na tyto metriky se dají migrovat všechny klasických upozornění na účty úložiště:

- PercentAuthorizationError
- PercentClientOtherError
- PercentNetworkError
- PercentServerOtherError
- PercentSuccess
- PercentThrottlingError
- PercentTimeoutError
- AnonymousThrottlingError
- SASThrottlingError
- ThrottlingError

Klasické výstraha pravidla na procenta metriky se musí migrovat na základě [mapování mezi metriky staré a nové úložiště](https://docs.microsoft.com/azure/storage/common/storage-metrics-migration#metrics-mapping-between-old-metrics-and-new-metrics). Bude nutné prahové hodnoty odpovídajícím způsobem upravit, protože nové metriky, které jsou k dispozici je některý z absolutní.

Klasické pravidla upozornění na AnonymousThrottlingError, SASThrottlingError a ThrottlingError musí rozdělit na dvě nové výstrahy, protože neexistuje žádný kombinované metriku, která poskytuje stejné funkce. Prahové hodnoty bude třeba ho upravit odpovídajícím způsobem.

### <a name="classic-alert-rules-on-deprecated-metrics"></a>Klasické pravidla upozornění na zastaralé metriky

Toto jsou classic pravidla upozornění na metriky, které byly dříve podporovány, ale nakonec byla vyřazena jako zastaralá. Malé procento zákazník může mít neplatný klasického pravidla upozornění na tyto metriky. Protože tato pravidla upozornění jsou neplatné, nebude se migrovat.

| Typ prostředku| Počet metrik: zastaralé |
|-------------|----------------- |
| Microsoft.DBforMySQL/servers | compute_consumption_percent, compute_limit |
| Microsoft.DBforPostgreSQL/servers | compute_consumption_percent, compute_limit |
| Microsoft.Network/publicIPAddresses | defaultddostriggerrate |
| Microsoft.SQL/servers/databases | service_level_objective, storage_limit, storage_used, throttling, dtu_consumption_percent, storage_used |
| Microsoft.Web/hostingEnvironments/multirolepools | averagememoryworkingset |
| Microsoft.Web/hostingEnvironments/workerpools | BytesReceived httpqueuelength |

## <a name="how-equivalent-new-alert-rules-and-action-groups-are-created"></a>Jak se vytvoří ekvivalentní nová pravidla upozornění a skupiny akcí

Nástroj pro migraci převede classic pravidel upozornění na ekvivalentní nová pravidla upozornění a skupin akcí. Pro většinu pravidel upozornění classic, ekvivalentní nová pravidla upozornění jsou na stejné metriky se stejnými vlastnostmi například `windowSize` a `aggregationType`. Existují však některé klasické výstrahy, pravidla se na metriky, které mají různé, ekvivalentní metriky v novém systému. Tyto zásady platí i pro migraci klasických upozornění není uvedeno v následující části:

- **Frekvence**: Definuje, jak často se kontroluje classic nebo nové pravidlo výstrahy pro podmínku. `frequency` v klasickém pravidla upozornění nebyl uživatelem konfigurovatelné a byla vždy 5 minut pro všechny typy prostředků kromě součásti Application Insights, u kterých bylo 1 min. Takže frekvence ekvivalentní pravidel je také nastavena na 5 minut a 1 min v uvedeném pořadí.
- **Typ agregace**: Definuje, jak je metrika agregován v okně, které vás zajímají. `aggregationType` Je také stejný mezi nové výstrahy pro většinu metriky a upozornění classic. V některých případech, protože se liší mezi klasických upozornění a nové výstrahy, ekvivalentní metriku `aggregationType` nebo `primary Aggregation Type` definované pro metrika.
- **Jednotky**: Vlastnost metrika, ke kterému se vytvoří výstraha. Některé ekvivalentní metriky mají různé jednotky. Prahová hodnota je odpovídajícím způsobem, podle potřeby upravit. Například pokud původní metrika má sekundy jako jednotky, ale má ekvivalentní novou metriku milisekund jako jednotky, původní prahová hodnota se vynásobí hodnotou 1000 zajistit stejné chování.
- **Velikost okna**: Definuje okno, přes která metrika se data agregují k porovnání s prahovou hodnotou. Pro úroveň standard `windowSize` hodnoty jako 15mins 5 minut, 30 minut, 1 hodina, 3 hodiny, 6 hodin, 12 hodin, 1 den, není žádná změna pro ekvivalentní nové pravidlo upozornění. Pro ostatní hodnoty, která je nejblíže `windowSize` je se rozhodli použít. Pro většinu zákazníků nebude to mít žádný vliv díky této změně. Pro malé procento zákazníky může být potřeba upravit prahové hodnoty, chcete-li získat přesně stejné chování.

V následujících částech se budeme podrobně metriky, které mají různé, ekvivalentní metriky v novém systému. Jakékoliv metriky, které zůstává stejná pro klasický i nový pravidla upozornění není uvedený. Můžete najít seznam metrik, které jsou podporovány v novém systému [tady](metrics-supported.md).

### <a name="microsoftstorageaccountsservices"></a>Microsoft.StorageAccounts/services

Pro účet služby Storage jako objekt blob, tabulky, souboru a front následující metriky mapovaly na ekvivalentní metriky, jak je znázorněno níže:

| Metrika v klasických upozornění | Ekvivalentní metriky v nové výstrahy | Komentáře|
|--------------------------|---------------------------------|---------|
| AnonymousAuthorizationError| Transakce metriky s dimenzemi "Hodnotu ResponseType" = "AuthorizationError" a "Ověřování" = "Anonymní"| |
| AnonymousClientOtherError | Transakce metriky s dimenzemi "Hodnotu ResponseType" = "ClientOtherError" a "Ověřování" = "Anonymní" | |
| AnonymousClientTimeOutError| Transakce metriky s dimenzemi "Hodnotu ResponseType" = "ClientTimeOutError" a "Ověřování" = "Anonymní" | |
| AnonymousNetworkError | Transakce metriky s dimenzemi "Hodnotu ResponseType" = "NetworkError" a "Ověřování" = "Anonymní" | |
| AnonymousServerOtherError | Transakce metriky s dimenzemi "Hodnotu ResponseType" = "ServerOtherError" a "Ověřování" = "Anonymní" | |
| AnonymousServerTimeOutError | Transakce metriky s dimenzemi "Hodnotu ResponseType" = "ServerTimeOutError" a "Ověřování" = "Anonymní" | |
| AnonymousSuccess | Transakce metriky s dimenzemi "Hodnotu ResponseType" = "Success" a "Ověřování" = "Anonymní" | |
| AuthorizationError | Transakce metriky s dimenzemi "Hodnotu ResponseType" = "AuthorizationError" | |
| Hodnotu AverageE2ELatency | SuccessE2ELatency | |
| AverageServerLatency | SuccessServerLatency | |
| Kapacita | BlobCapacity | Použití `aggregationType` průměrné místo "last". Metrika platí jenom pro služby objektů Blob |
| ClientOtherError | Transakce metriky s dimenzemi "Hodnotu ResponseType" = "ClientOtherError"  | |
| ClientTimeoutError | Transakce metriky s dimenzemi "Hodnotu ResponseType" = "ClientTimeOutError" | |
| ContainerCount | ContainerCount | Použití `aggregationType` průměrné místo "last". Metrika platí jenom pro služby objektů Blob |
| NetworkError | Transakce metriky s dimenzemi "Hodnotu ResponseType" = "NetworkError" | |
| ObjectCount | BlobCount| Použití `aggregationType` průměrné místo "last". Metrika platí jenom pro služby objektů Blob |
| SASAuthorizationError | Transakce metriky s dimenzemi "Hodnotu ResponseType" = "AuthorizationError" a "Ověřování" = "SAS" | |
| SASClientOtherError | Transakce metriky s dimenzemi "Hodnotu ResponseType" = "ClientOtherError" a "Ověřování" = "SAS" | |
| SASClientTimeOutError | Transakce metriky s dimenzemi "Hodnotu ResponseType" = "ClientTimeOutError" a "Ověřování" = "SAS" | |
| SASNetworkError | Transakce metriky s dimenzemi "Hodnotu ResponseType" = "NetworkError" a "Ověřování" = "SAS" | |
| SASServerOtherError | Transakce metriky s dimenzemi "Hodnotu ResponseType" = "ServerOtherError" a "Ověřování" = "SAS" | |
| SASServerTimeOutError | Transakce metriky s dimenzemi "Hodnotu ResponseType" = "ServerTimeOutError" a "Ověřování" = "SAS" | |
| SASSuccess | Transakce metriky s dimenzemi "Hodnotu ResponseType" = "Success" a "Ověřování" = "SAS" | |
| ServerOtherError | Transakce metriky s dimenzemi "Hodnotu ResponseType" = "ServerOtherError" | |
| ServerTimeOutError | Transakce metriky s dimenzemi "Hodnotu ResponseType" = "ServerTimeOutError"  | |
| Úspěch | Transakce metriky s dimenzemi "Hodnotu ResponseType" = "Success" | |
| TotalBillableRequests| Transakce | |
| TotalEgress | Výchozí přenos | |
| TotalIngress | Příchozí přenos dat | |
| TotalRequests | Transakce | |

### <a name="microsoftinsightscomponents"></a>Microsoft.insights/components

Pro službu Application Insights jsou ekvivalentní metriky, jak je znázorněno níže:

| Metrika v klasických upozornění | Ekvivalentní metriky v nové výstrahy | Komentáře|
|--------------------------|---------------------------------|---------|
| availability.availabilityMetric.value | availabilityResults/availabilityPercentage|   |
| availability.durationMetric.value | availabilityResults/doba trvání| Vynásobte prahová hodnota původní hodnotou 1000 za jednotky klasické metriky jsou během několika sekund a u nových jeden jsou v milisekundách.  |
| basicExceptionBrowser.count | výjimky a prohlížeče|  Použití `aggregationType` "počet" místo "sum". |
| basicExceptionServer.count | výjimky/server| Použití `aggregationType` "počet" místo "sum".  |
| clientPerformance.clientProcess.value | browserTimings/processingDuration| Vynásobte prahová hodnota původní hodnotou 1000 za jednotky klasické metriky jsou během několika sekund a u nových jeden jsou v milisekundách.  |
| clientPerformance.networkConnection.value | browserTimings/networkDuration|  Vynásobte prahová hodnota původní hodnotou 1000 za jednotky klasické metriky jsou během několika sekund a u nových jeden jsou v milisekundách. |
| clientPerformance.receiveRequest.value | browserTimings/receiveDuration| Vynásobte prahová hodnota původní hodnotou 1000 za jednotky klasické metriky jsou během několika sekund a u nových jeden jsou v milisekundách.  |
| clientPerformance.sendRequest.value | browserTimings/sendDuration| Vynásobte prahová hodnota původní hodnotou 1000 za jednotky klasické metriky jsou během několika sekund a u nových jeden jsou v milisekundách.  |
| clientPerformance.total.value | browserTimings/totalDuration| Vynásobte prahová hodnota původní hodnotou 1000 za jednotky klasické metriky jsou během několika sekund a u nových jeden jsou v milisekundách.  |
| performanceCounter.available_bytes.value | čítače výkonu nebo memoryAvailableBytes|   |
| performanceCounter.io_data_bytes_per_sec.value | performanceCounters/processIOBytesPerSecond|   |
| performanceCounter.number_of_exceps_thrown_per_sec.value | performanceCounters/exceptionsPerSecond|   |
| performanceCounter.percentage_processor_time_normalized.value | čítače výkonu nebo processCpuPercentage|   |
| performanceCounter.percentage_processor_time.value | čítače výkonu nebo processCpuPercentage| Bude nutné prahové hodnoty odpovídajícím způsobem změnit, protože byl původní metriky ze všech jader a nových metrik je normalizovány na jedno jádro. Nástroj pro migraci nedojde ke změně prahových hodnot.  |
| performanceCounter.percentage_processor_total.value | čítače výkonu nebo processorCpuPercentage|   |
| performanceCounter.process_private_bytes.value | performanceCounters/processPrivateBytes|   |
| performanceCounter.request_execution_time.value | performanceCounters/requestExecutionTime|   |
| performanceCounter.requests_in_application_queue.value | performanceCounters/requestsInQueue|   |
| performanceCounter.requests_per_sec.value | performanceCounters/requestsPerSecond|   |
| request.duration | požadavky a doba trvání| Vynásobte prahová hodnota původní hodnotou 1000 za jednotky klasické metriky jsou během několika sekund a u nových jeden jsou v milisekundách.  |
| Request.rate | požadavky a míra|   |
| requestFailed.count | / Neúspěšné požadavky| Použití `aggregationType` "počet" místo "sum".   |
| view.count | zobrazení stránky nebo count| Použití `aggregationType` "počet" místo "sum".   |

### <a name="how-equivalent-action-groups-are-created"></a>Jak se vytvoří skupiny ekvivalentní akci

Klasické upozornění, že pravidla měl e-mailu, webhooku, logiku aplikace a runbook akce vázané na upozornění pravidla samotný. Nová upozornění pravidla použít skupiny akcí, které lze opětovně použít napříč více pravidel upozornění. Nástroj pro migraci vytvoří skupinu jednu akci pro stejné akce bez ohledu na to, kolik pravidel upozornění jsou pomocí akce. Skupiny akcí, které vytvořil nástroj pro migraci použijte formát názvů "Migrated_AG *".

## <a name="rollout-phases"></a>Fáze uvedení

Nástroj pro migraci zavádí ve fázích pro zákazníky, které používají klasický pravidla upozornění. Vlastníci předplatného obdrží e-mail, až bude předplatné připravené k migraci pomocí nástroje.

> [!NOTE]
> Protože nástroj je zavádění ve fázích, může se zobrazit, že některé z vašich předplatných ještě nejsou připravené k migraci v raných fázích.

Většina předplatná jsou nyní označen jako připravený pro migraci. Pouze odběry, které mají klasických upozornění na následující typy prostředků jsou ještě není připravený k migraci.

- Microsoft.classicCompute/domainNames/slots/roles
- Microsoft.documentDB/databases
- Microsoft.insights/components

## <a name="who-can-trigger-the-migration"></a>Kdo může aktivovat migrace?

Každý uživatel, který má předdefinovaná role Přispěvatel monitorování na úrovni předplatného můžete aktivovat migrace. Migraci můžete aktivovat také uživatelé, kteří mají vlastní roli s následujícími oprávněními:

- \* / čtení
- Microsoft.Insights/actiongroups/*
- Microsoft.Insights/AlertRules/*
- Microsoft.Insights/metricAlerts/*

> [!NOTE]
> Kromě výše uvedených oprávnění by měla být vaše předplatné kromě zaregistrované u poskytovatele prostředků Microsoft.AlertsManagement. To je potřebné k úspěšné migraci výstrahy selhání anomálií v Application Insights. 

## <a name="common-problems-and-remedies"></a>Běžné problémy a náhrad

Poté co [aktivovat migrace](alerts-using-migration-tool.md), obdržíte e-mail na adresy, které jste zadali, které vás upozorní, že se migrace dokončí, nebo pokud není nutné provádět žádnou akci od vás. Tato část popisuje některé běžné problémy a řešení problémů s nimi.

### <a name="validation-failed"></a>Ověření se nezdařilo

Z důvodu některých nedávné změny classic pravidla výstrah v rámci vašeho předplatného není možné migrovat předplatné. Tento problém je dočasný. Migraci můžete restartovat po stavu migrace přesune zpět **připravený pro migraci** za několik dnů.

### <a name="policy-or-scope-lock-preventing-us-from-migrating-your-rules"></a>Zásady nebo obor zámku nám brání migrace vašich pravidel

Jako součást migrace se vytvoří nová upozornění metrik a nové skupiny akcí a pak se odstraní classic pravidla upozornění. Je však zásad nebo obor zámku nám brání vytváření prostředků. V závislosti na zámek zásad nebo oboru nelze migrovat některá nebo všechna pravidla. Tento problém lze vyřešit dočasně odebráním zámku oboru nebo zásad a aktivuje migrovat znovu.

## <a name="next-steps"></a>Další postup

- [Jak používat nástroj pro migraci](alerts-using-migration-tool.md)
- [Příprava na migraci](alerts-prepare-migration.md)
