---
title: Principy nástroje pro migraci pro výstrahy Azure Monitoru
description: Zjistěte, jak funguje nástroj pro migraci výstrah, a vyřešte problémy.
ms.topic: conceptual
ms.date: 07/10/2019
ms.author: yalavi
author: yalavi
ms.subservice: alerts
ms.openlocfilehash: d31c856e17348c23ad61130869af6ae440d3050d
ms.sourcegitcommit: fb23286d4769442631079c7ed5da1ed14afdd5fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/10/2020
ms.locfileid: "81114312"
---
# <a name="understand-how-the-migration-tool-works"></a>Princip fungování nástroje pro migraci

Jak [již bylo oznámeno](monitoring-classic-retirement.md), klasické výstrahy ve službě Azure Monitor jsou vyřazeny do srpna 31, 2019 (původně 30 červen 2019). Nástroj pro migraci je k dispozici na webu Azure Portal zákazníkům, kteří používají klasická pravidla výstrah a chtějí migraci spustit sami.

Tento článek vysvětluje, jak funguje nástroj pro dobrovolnou migraci. Popisuje také opravné prostředky pro některé běžné problémy.

> [!NOTE]
> Kvůli zpoždění při zavádění migračního nástroje bylo datum odchodu do důchodu pro klasickou [výstrahu prodlouženo na 31.](https://azure.microsoft.com/updates/azure-monitor-classic-alerts-retirement-date-extended-to-august-31st-2019/)

## <a name="classic-alert-rules-that-will-not-be-migrated"></a>Klasická pravidla upozornění, která se nebudou migrovat

> [!IMPORTANT]
> Výstrahy protokolu aktivit (včetně výstrah stavu služby) a výstrahprotokolu nejsou migrací ovlivněny. Migrace se vztahuje pouze na klasická pravidla výstrah popsaná [zde](monitoring-classic-retirement.md#retirement-of-classic-monitoring-and-alerting-platform).

Přestože nástroj může migrovat téměř všechna [klasická pravidla výstrah](monitoring-classic-retirement.md#retirement-of-classic-monitoring-and-alerting-platform), existují některé výjimky. Následující pravidla výstrah nebudou migrována pomocí nástroje (nebo během automatické migrace od září 2019):

- Klasická pravidla upozornění na metriky hostů virtuálních strojů (Windows i Linux). Podívejte se na [pokyny pro opětovné vytvoření těchto pravidel výstrah v nových upozornění metriky](#guest-metrics-on-virtual-machines) dále v tomto článku.
- Klasická pravidla upozornění na klasické metriky úložiště. Podívejte se na [pokyny pro sledování klasických účtů úložiště](https://azure.microsoft.com/blog/modernize-alerting-using-arm-storage-accounts/).
- Klasická pravidla upozornění na některé metriky účtu úložiště. Podrobnosti [naleznete](#storage-account-metrics) dále v tomto článku.
- Klasická pravidla výstrah pro některé metriky Cosmos DB. Podrobnosti [naleznete](#cosmos-db-metrics) dále v tomto článku.
- Klasická pravidla výstrah pro všechny klasické virtuální počítače a metriky cloudových služeb (Microsoft.ClassicCompute/virtualMachines a Microsoft.ClassicCompute/domainNames/slots/roles). Podrobnosti [naleznete](#classic-compute-metrics) dále v tomto článku.

Pokud vaše předplatné má nějaká taková klasická pravidla, je nutné je migrovat ručně. Vzhledem k tomu, že nemůžeme zajistit automatickou migraci, budou všechny existující klasické metriky výstrahy těchto typů nadále fungovat až do června 2020. Toto rozšíření poskytuje čas přejít na nové výstrahy. Můžete také pokračovat ve vytváření nových klasických výstrah o výše uvedených výjimkách až do června 2020. Po srpnu 2019 však nelze vytvořit žádná nová klasická upozornění.

> [!NOTE]
> Kromě výše uvedených výjimek, pokud jsou klasická pravidla výstrah [deprecated metrics](#classic-alert-rules-on-deprecated-metrics) neplatná, tj.

### <a name="guest-metrics-on-virtual-machines"></a>Metriky pro hosty na virtuálních počítačích

Než budete moci vytvořit nové metriky upozornění na metriky hosta, metriky hosta musí být odeslány do úložiště vlastní metriky Azure Monitor. Podle následujících pokynů povolte jímku Azure Monitoru v diagnostických nastaveních:

- [Povolení metrik hosta pro virtuální servery Windows](collect-custom-metrics-guestos-resource-manager-vm.md)
- [Povolení metrik pro hosty pro virtuální počítače s Linuxem](collect-custom-metrics-linux-telegraf.md)

Po dokončení těchto kroků můžete vytvořit nová upozornění metriky na metriky hosta. A po vytvoření nových upozornění metriky můžete odstranit klasické výstrahy.

### <a name="storage-account-metrics"></a>Metriky účtu úložiště

Všechny klasické výstrahy na účtech úložiště lze migrovat s výjimkou upozornění na tyto metriky:

- Chyba procentaauthorization
- Chyba _A) _1000 000 00
- Chyba _a) je v procentech sítě
- Chyba Procento_serveru OtherError
- ProcentoÚspěch
- Chyba PercentThrottlingError
- Chyba PercentTimeoutError
- Chyba AnonymousThrottling
- Chyba SASThrottlingError
- ThrottlingError

Klasická pravidla výstrah pro metriky Procenta musí být migrována na základě [mapování mezi starými a novými metrikami úložiště](https://docs.microsoft.com/azure/storage/common/storage-metrics-migration#metrics-mapping-between-old-metrics-and-new-metrics). Prahové hodnoty bude nutné odpovídajícím způsobem upravit, protože nová dostupná metrika je absolutní.

Klasická pravidla výstrah na AnonymousThrottlingError, SASThrottlingError a Omezení Chyba musí být rozdělena do dvou nových výstrah, protože neexistuje žádná kombinovaná metrika, která poskytuje stejné funkce. Prahové hodnoty budou muset být odpovídajícím způsobem upraveny.

### <a name="cosmos-db-metrics"></a>Metriky služby Cosmos DB

Všechny klasické výstrahy na cosmos DB metriky lze migrovat s výjimkou výstrah na tyto metriky:

- Průměrný počet požadavků za sekundu
- Úrovně konzistence
- Http 2xx
- Http 3xx
- Http 400
- Http 401
- Vnitřní chyba serveru
- Maximální spotřebováno RUPM za minutu
- Maximální počet sekund
- Neúspěšné požadavky počtu mongo
- Mongo odstranit neúspěšné požadavky
- Mongo Vložit neúspěšné požadavky
- Mongo Další neúspěšné požadavky
- Mongo Další poplatek za vyžádání
- Mongo Další míra požadavků
- Neúspěšné požadavky dotazu Mongo
- Neúspěšné požadavky aktualizace Mongo
- Pozorovaná latence čtení
- Pozorovaná latence zápisu
- Dostupnost služby
- Kapacita úložiště
- Omezené požadavky
- Požadavky celkem

Průměrné požadavky za sekundu, úroveň konzistence, maximální rupm spotřebované za minutu, maximální ru za sekundu, pozorovaná latence čtení, pozorovaná latence zápisu, kapacita úložiště nejsou v současné době k dispozici v [novém systému](metrics-supported.md#microsoftdocumentdbdatabaseaccounts).

Výstrahy na metriky požadavků, jako jsou Http 2xx, Http 3xx, Http 400, Http 401, Vnitřní chyba serveru, Dostupnost služby, Omezené požadavky a Celkový počet požadavků, se nemigrují, protože způsob, jakým se počítají požadavky, se liší mezi klasickými metrikami a novými metrikami. Výstrahy na tyto budou muset být ručně znovu vytvořeny s upravenými prahovými hodnotami.

Výstrahy na mongo neúspěšné požadavky metriky musí být rozděleny do více výstrah, protože neexistuje žádná kombinovaná metrika, která poskytuje stejné funkce. Prahové hodnoty budou muset být odpovídajícím způsobem upraveny.

### <a name="classic-compute-metrics"></a>Klasické výpočetní metriky

Žádné výstrahy na klasické výpočetní metriky nebudou migrovány pomocí nástroje pro migraci, protože klasické výpočetní prostředky ještě nejsou podporovány s novými výstrahami. V budoucnu bude přidána podpora nových výstrah pro tyto typy prostředků. Jakmile bude k dispozici, zákazníci musí znovu vytvořit nová ekvivalentní pravidla výstrah na základě klasických pravidel výstrah před červnem 2020.

### <a name="classic-alert-rules-on-deprecated-metrics"></a>Klasická pravidla upozornění na zastaralé metriky

Jedná se o klasická pravidla výstrah pro metriky, které byly dříve podporovány, ale nakonec byly zastaralé. Malé procento zákazníka může mít neplatná pravidla klasických výstrah pro tyto metriky. Vzhledem k tomu, že tato pravidla výstrah jsou neplatná, nebudou migrována.

| Typ prostředku| Zastaralé metriky |
|-------------|----------------- |
| Microsoft.DBforMySQL/servery | compute_consumption_percent, compute_limit |
| Microsoft.DBforPostgreSQL/servery | compute_consumption_percent, compute_limit |
| Microsoft.Network/publicIPAdresy | defaultddostriggerrate |
| Microsoft.SQL/servery/databáze | service_level_objective, storage_limit, storage_used, škrcení, dtu_consumption_percent, storage_used |
| Microsoft.Web/hostingProstředí/víceúčelové fondy | průměrnápaměťworkingová sada |
| Microsoft.Web/hostingProstředí/workerpools | přijaté bajty, httpqueuelength |

## <a name="how-equivalent-new-alert-rules-and-action-groups-are-created"></a>Jak jsou vytvořena ekvivalentní nová pravidla výstrah a skupiny akcí

Nástroj pro migraci převede klasická pravidla výstrah na ekvivalentní nová pravidla výstrah a skupiny akcí. U většiny klasických pravidel výstrah jsou ekvivalentní nová pravidla výstrah `windowSize` na `aggregationType`stejné metrice se stejnými vlastnostmi, jako jsou například a . Existují však některá klasická pravidla výstrah y na metriky, které mají různé, ekvivalentní metriky v novém systému. Pro migraci klasických výstrah platí následující zásady, pokud není uvedeno v následující části:

- **Frekvence**: Definuje, jak často klasické nebo nové pravidlo výstrahy kontroluje podmínku. Pravidla `frequency` klasické výstrahy nebyla uživatelem konfigurovatelná a vždy trvala 5 minut pro všechny typy prostředků s výjimkou komponent Application Insights, pro které to bylo 1 min. Frekvence ekvivalentních pravidel je tedy také nastavena na 5 minut a 1 min.
- **Typ agregace**: Definuje, jak je metrika agregována v okně zájmu. Je `aggregationType` také stejný mezi klasickými výstrahami a novými výstrahami pro většinu metrik. V některých případech vzhledem k tomu, že `aggregationType` metrika `primary Aggregation Type` se liší mezi klasické výstrahy a nové výstrahy, ekvivalentní nebo definované pro metriku se používá.
- **Jednotky**: Vlastnost metriky, na které je výstraha vytvořena. Některé ekvivalentní metriky mají různé jednotky. Prahová hodnota se podle potřeby odpovídajícím způsobem upraví. Například pokud původní metrika má sekund jako jednotky, ale ekvivalentní nová metrika má milliSeconds jako jednotky, původní prahová hodnota je vynásobena 1000 zajistit stejné chování.
- **Velikost okna**: Definuje okno, ve kterém jsou agregována metrická data, aby se porovnala s prahovou hodnotou. Pro `windowSize` standardní hodnoty, jako je 5mins, 15mins, 30mins, 1hour, 3hours, 6 hodin, 12 hodin, 1 den, není provedena žádná změna pro ekvivalentní nové pravidlo výstrahy. Pro ostatní hodnoty `windowSize` je zvolen nejbližší, který má být použit. Pro většinu zákazníků neexistuje žádný vliv s touto změnou. Pro malé procento zákazníků může být potřeba vyladit prahovou hodnotu, aby bylo možné získat přesně stejné chování.

V následujících částech podrobně popisujeme metriky, které mají v novém systému jinou ekvivalentní metriku. Všechny metriky, které zůstávají stejné pro klasické a nová pravidla výstrah není uveden. Seznam metrik podporovaných v novém systému naleznete [zde](metrics-supported.md).

### <a name="microsoftstorageaccountsservices"></a>Microsoft.StorageAccounts/services

Pro služby účtu úložiště, jako je objekt blob, tabulka, soubor a fronty, jsou následující metriky mapovány na ekvivalentní metriky, jak je znázorněno níže:

| Metrika v klasických výstrahách | Ekvivalentní metrika v nových výstrahách | Komentáře|
|--------------------------|---------------------------------|---------|
| Chyba anonymníautorizace| Metrika transakce s dimenzemi "ResponseType"="AuthorizationError" a "Authentication" = "Anonymní"| |
| AnonymníChyba _Klienta OtherError | Metrika transakce s dimenzemi "ResponseType"="ClientOtherError" a "Authentication" = "Anonymní" | |
| Chyba anonymního časového času klienta| Metrika transakce s dimenzemi "ResponseType"="ClientTimeOutError" a "Authentication" = "Anonymní" | |
| Chyba anonymní sítě | Metrika transakce s dimenzemi "ResponseType"="NetworkError" a "Authentication" = "Anonymní" | |
| AnonymníServerOtherError | Metrika transakce s dimenzemi "ResponseType"="ServerOtherError" a "Authentication" = "Anonymní" | |
| Chyba anonymního časového času serveru | Metrika transakce s dimenzemi "ResponseType"="ServerTimeOutError" a "Authentication" = "Anonymní" | |
| AnonymníÚspěch | Metrika transakce s dimenzemi "ResponseType"="Success" a "Authentication" = "Anonymní" | |
| Chyba autorizace | Metrika transakce s dimenzemi "ResponseType"="AuthorizationError" | |
| AverageE2ELatency | SuccessE2ELatency | |
| AverageServerLatency | SuccessServerLatency | |
| Kapacita | Možnost objektů blob | Místo `aggregationType` "poslední" použijte "průměr". Metrika se vztahuje jenom pro služby objektů Blob |
| Chyba ClientOtherError | Metrika transakce s dimenzemi "ResponseType"="ClientOtherError"  | |
| Chyba ClientTimeoutError | Metrika transakce s dimenzemi "ResponseType"="ClientTimeOutError" | |
| ContainerCount | ContainerCount | Místo `aggregationType` "poslední" použijte "průměr". Metrika se vztahuje jenom pro služby objektů Blob |
| NetworkError | Metrika transakce s dimenzemi "ResponseType"="NetworkError" | |
| Počet objektů | Počet objektů BlobCount| Místo `aggregationType` "poslední" použijte "průměr". Metrika se vztahuje jenom pro služby objektů Blob |
| Chyba autorizace SAS | Metrika transakce s dimenzemi "ResponseType"="AuthorizationError" a "Authentication" = "SAS" | |
| SASClientOtherError | Metrika transakce s dimenzemi "ResponseType"="ClientOtherError" a "Authentication" = "SAS" | |
| Chyba časového_časového času sasklienta | Metrika transakce s dimenzemi "ResponseType"="ClientTimeOutError" a "Authentication" = "SAS" | |
| Chyba spřipisuje síť SAS | Metrika transakce s dimenzemi "ResponseType"="NetworkError" a "Authentication" = "SAS" | |
| SASServerOtherError | Metrika transakce s dimenzemi "ResponseType"="ServerOtherError" a "Authentication" = "SAS" | |
| Chyba časového času sasserverout | Metrika transakce s dimenzemi "ResponseType"="ServerTimeOutError" a "Authentication" = "SAS" | |
| Úspěch SAS | Metrika transakce s dimenzemi "ResponseType"="Success" a "Authentication" = "SAS" | |
| Chyba_other_serveru | Metrika transakce s dimenzemi "ResponseType"="ServerOtherError" | |
| Chyba_časového_outu serveru | Metrika transakce s dimenzemi "ResponseType"="ServerTimeOutError"  | |
| Úspěch | Metrika transakce s dimenzemi "ResponseType"="Úspěch" | |
| TotalBillablePožadavky| Transakce | |
| TotalEgress | Výchozí přenos dat | |
| TotalIngress | Příchozí přenos dat | |
| TotalRequests | Transakce | |

### <a name="microsoftinsightscomponents"></a>Microsoft.insights/komponenty

Pro Application Insights ekvivalentní metriky jsou, jak je znázorněno níže:

| Metrika v klasických výstrahách | Ekvivalentní metrika v nových výstrahách | Komentáře|
|--------------------------|---------------------------------|---------|
| availability.availabilityMetric.value | availabilityResults/availabilityPercentage|   |
| availability.durationMetric.value | availabilityResults/duration| Vynásobte původní prahovou hodnotu 1000 jako jednotky pro klasické metriky jsou v sekundách a pro nové jsou v miliSeconds.  |
| basicExceptionBrowser.count | výjimky/prohlížeč|  Místo `aggregationType` součet použijte "počítat". |
| basicExceptionServer.count | výjimky/server| Místo `aggregationType` součet použijte "počítat".  |
| clientPerformance.clientProcess.value | browserTimings/processingDuration| Vynásobte původní prahovou hodnotu 1000 jako jednotky pro klasické metriky jsou v sekundách a pro nové jsou v miliSeconds.  |
| clientPerformance.networkConnection.value | browserTimings/networkDuration|  Vynásobte původní prahovou hodnotu 1000 jako jednotky pro klasické metriky jsou v sekundách a pro nové jsou v miliSeconds. |
| clientPerformance.receiveRequest.value | browserTimings/receiveDuration| Vynásobte původní prahovou hodnotu 1000 jako jednotky pro klasické metriky jsou v sekundách a pro nové jsou v miliSeconds.  |
| clientPerformance.sendRequest.value | browserTimings/sendDuration| Vynásobte původní prahovou hodnotu 1000 jako jednotky pro klasické metriky jsou v sekundách a pro nové jsou v miliSeconds.  |
| clientPerformance.total.value | browserTimings/totalDuration| Vynásobte původní prahovou hodnotu 1000 jako jednotky pro klasické metriky jsou v sekundách a pro nové jsou v miliSeconds.  |
| performanceCounter.available_bytes.value | performanceCounters/memoryAvailableBytes|   |
| performanceCounter.io_data_bytes_per_sec.value | performanceCounters/processIOBytesPerSecond|   |
| performanceCounter.number_of_exceps_thrown_per_sec.value | performanceCounters/exceptionsPerSecond|   |
| performanceCounter.percentage_processor_time_normalized.value | performanceCounters/processCpuPercentage|   |
| performanceCounter.percentage_processor_time.value | performanceCounters/processCpuPercentage| Prahová hodnota bude muset být odpovídajícím způsobem upravena, protože původní metrika byla napříč všemi jádry a nová metrika je normalizována na jedno jádro. Nástroj pro migraci nemění prahové hodnoty.  |
| performanceCounter.percentage_processor_total.value | performanceCounters/processorCpuPercentage|   |
| performanceCounter.process_private_bytes.value | performanceCounters/processPrivateBytes|   |
| performanceCounter.request_execution_time.value | performanceCounters/requestExecutionTime|   |
| performanceCounter.requests_in_application_queue.value | performanceCounters/requestsInQueue|   |
| performanceCounter.requests_per_sec.value | performanceCounters/requestsPerSecond|   |
| request.duration | žádosti/doba trvání| Vynásobte původní prahovou hodnotu 1000 jako jednotky pro klasické metriky jsou v sekundách a pro nové jsou v miliSeconds.  |
| request.rate | žádosti/sazba|   |
| requestFailed.count | požadavky/neúspěšné| Místo `aggregationType` součet použijte "počítat".   |
| view.count | zobrazení/počet stránek| Místo `aggregationType` součet použijte "počítat".   |

### <a name="microsoftdocumentdbdatabaseaccounts"></a>Microsoft.DocumentDB/databaseAccounts

Pro Cosmos DB jsou ekvivalentní metriky, jak je znázorněno níže:

| Metrika v klasických výstrahách | Ekvivalentní metrika v nových výstrahách | Komentáře|
|--------------------------|---------------------------------|---------|
| Dostupné úložiště     |Dostupné úložiště|   |
| Velikost dat | Využití dat| |
| Počet dokumentů | DocumentCount||
| Velikost indexu | Využití indexu||
| Mongo Počet žádostí poplatek| MongoRequestCharge s dimenzí "CommandName" = "počet"||
| Mongo Počet Poptávka Sazba | MongoRequestsCount s dimenzí "CommandName" = "počet"||
| Poplatek za odstranění požadavku mongo | MongoRequestCharge s dimenzí "CommandName" = "odstranit"||
| Rychlost odstranění požadavku Mongo | MongoRequestsCount s dimenzí "CommandName" = "odstranit"||
| Poplatek za vložení požadavku Mongo | MongoRequestCharge s dimenzí "CommandName" = "insert"||
| Rychlost požadavků vložení mongo | MongoRequestsCount s dimenzí "CommandName" = "insert"||
| Poplatek za požadavek na dotaz Mongo | MongoRequestCharge s dimenzí "CommandName" = "najít"||
| Rychlost požadavků na dotazy Mongo | MongoRequestsCount s dimenzí "CommandName" = "najít"||
| Poplatek za požadavek na aktualizaci mongo | MongoRequestCharge s dimenzí "CommandName" = "aktualizovat"||
| Služba není k dispozici.| Dostupnost služby||
| Jednotky TotalRequest | Jednotky TotalRequest||

### <a name="how-equivalent-action-groups-are-created"></a>Jak jsou vytvořeny ekvivalentní skupiny akcí

Klasická pravidla výstrah měla e-mail, webhook, aplikaci logiky a akce runbooku vázané na samotné pravidlo výstrahy. Nová pravidla výstrah používají skupiny akcí, které lze znovu použít ve více pravidlech výstrah. Nástroj pro migraci vytvoří jednu skupinu akcí pro stejné akce bez ohledu na to, kolik pravidel výstrah akci používá. Skupiny akcí vytvořené nástrojem pro migraci používají formát pojmenování "Migrated_AG*".

> [!NOTE]
> Klasické výstrahy odeslané lokalizované e-maily na základě národního prostředí klasického správce při použití k upozornění klasických rolí správce. Nové výstražné e-maily jsou odesílány prostřednictvím akčních skupin a jsou pouze v angličtině.

## <a name="rollout-phases"></a>Fáze uvedení

Nástroj pro migraci se zavádí ve fázích pro zákazníky, kteří používají klasická pravidla výstrah. Vlastníci předplatného obdrží e-mail, když je předplatné připraveno k migraci pomocí nástroje.

> [!NOTE]
> Vzhledem k tomu, že nástroj se zavádí ve fázích, můžete vidět, že některé z vašich předplatných ještě nejsou připraveny k migraci během počátečních fází.

Většina předplatných je aktuálně označena jako připravená k migraci. Pouze předplatná, která mají klasické výstrahy na následující typy prostředků, stále nejsou připravena k migraci.

- Microsoft.classicCompute/domainNames/slots/roles
- Microsoft.insights/komponenty

## <a name="who-can-trigger-the-migration"></a>Kdo může spustit migraci?

Migrace může aktivovat každý uživatel, který má integrovanou roli přispěvatele monitorování na úrovni předplatného. Migraci mohou aktivovat také uživatelé, kteří mají vlastní roli s následujícími oprávněními:

- */čtení
- Microsoft.Insights/skupiny akcí/*
- Microsoft.Insights/AlertRules/*
- Microsoft.Insights/metricAlerts/*
- Microsoft.AlertsManagement/smartDetectorAlertRules/*

> [!NOTE]
> Kromě výše uvedených oprávnění by vaše předplatné mělo být navíc zaregistrováno u zprostředkovatele prostředků Microsoft.AlertsManagement. To je nutné úspěšně migrovat výstrahy anomálií selhání na Application Insights. 

## <a name="common-problems-and-remedies"></a>Běžné problémy a nápravná opatření

Po [aktivaci migrace](alerts-using-migration-tool.md)obdržíte e-mail na zadaný chránič adres, který vás upozorní, že migrace je dokončena nebo že od vás bude potřeba nějaká akce. Tato část popisuje některé běžné problémy a jak se s nimi vypořádat.

### <a name="validation-failed"></a>Ověření se nezdařilo.

Vzhledem k některým nedávným změnám klasických pravidel výstrah v předplatném nelze předplatné migrovat. Tento problém je dočasný. Migraci můžete restartovat po přesunu stavu migrace zpět **Připraveno k migraci** za několik dní.

### <a name="scope-lock-preventing-us-from-migrating-your-rules"></a>Zámek oboru, který nám brání v migraci pravidel

V rámci migrace budou vytvořeny nové výstrahy metrik a nové skupiny akcí a pak budou odstraněna klasická pravidla výstrah. Zámek oboru nám však může zabránit ve vytváření nebo odstranění prostředků. V závislosti na zámku oboru nelze migrovat některá nebo všechna pravidla. Tento problém můžete vyřešit odebráním zámku oboru pro odběr, skupinu prostředků nebo prostředek, který je uveden v [nástroji pro migraci](https://portal.azure.com/#blade/Microsoft_Azure_Monitoring/MigrationBladeViewModel), a znovu aktivujete migraci. Zámek oboru nelze zakázat a musí být odebrán po dobu trvání procesu migrace. [Další informace o správě zámků oboru](../../azure-resource-manager/management/lock-resources.md#portal).

### <a name="policy-with-deny-effect-preventing-us-from-migrating-your-rules"></a>Zásady s efektem "Odepřít", které nám brání v migraci vašich pravidel

V rámci migrace budou vytvořeny nové výstrahy metrik a nové skupiny akcí a pak budou odstraněna klasická pravidla výstrah. Zásada nám však může bránit ve vytváření zdrojů. V závislosti na zásadách nelze migrovat některá nebo všechna pravidla. Zásady, které blokují proces, jsou uvedeny v [nástroji pro migraci](https://portal.azure.com/#blade/Microsoft_Azure_Monitoring/MigrationBladeViewModel). Tento problém vyřešte buď:

- Vyloučení předplatných nebo skupin prostředků po dobu trvání procesu migrace z přiřazení zásad. [Další informace o správě oboru vyloučení zásad](../../governance/policy/tutorials/create-and-manage.md#exempt-a-non-compliant-or-denied-resource-using-exclusion).
- Odebrání nebo změna efektu na "audit" nebo "append" (což může například vyřešit problémy týkající se chybějících značek). [Přečtěte si další informace o efektu správy zásad](../../governance/policy/concepts/definition-structure.md#policy-rule).

## <a name="next-steps"></a>Další kroky

- [Způsoby použití nástroje pro migraci](alerts-using-migration-tool.md)
- [Příprava pro migraci](alerts-prepare-migration.md)
