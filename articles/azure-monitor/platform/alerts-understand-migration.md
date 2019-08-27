---
title: Vysvětlení způsobu, jakým nástroj k dobrovolné migraci funguje pro Azure Monitor výstrahy
description: Zjistěte, jak Nástroj pro migraci výstrah funguje a jak řešit problémy.
author: snehithm
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 07/10/2019
ms.author: snmuvva
ms.subservice: alerts
ms.openlocfilehash: 5257724add570be480063ab776248a8fd1d944c7
ms.sourcegitcommit: 94ee81a728f1d55d71827ea356ed9847943f7397
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/26/2019
ms.locfileid: "70034757"
---
# <a name="understand-how-the-migration-tool-works"></a>Vysvětlení fungování nástroje pro migraci

Jak [už](monitoring-classic-retirement.md)jsme oznámili, klasické výstrahy v Azure monitor jsou vyřazené z 31. srpna 2019 (původně od 30. června 2019). Nástroj pro migraci je k dispozici v Azure Portal zákazníkům, kteří používají pravidla pro klasické výstrahy a kteří chtějí aktivovat migraci sami.

Tento článek vysvětluje, jak nástroj k dobrovolné migraci funguje. Popisuje také nápravná opatření pro některé běžné problémy.

> [!NOTE]
> Vzhledem k prodlevám při zavádění migračního nástroje pro migraci se datum vyřazení pro migraci klasických výstrah [rozšířila na 31. srpna 2019](https://azure.microsoft.com/updates/azure-monitor-classic-alerts-retirement-date-extended-to-august-31st-2019/) od původně oznámeného dne 30. června 2019.

## <a name="classic-alert-rules-that-will-not-be-migrated"></a>Klasická pravidla výstrah, která se nebudou migrovat

> [!IMPORTANT]
> Migrace neovlivní výstrahy protokolu aktivit (včetně upozornění na stav služby) a výstrahy protokolu. Migrace se vztahuje pouze na pravidla pro klasické výstrahy, která jsou popsaná [zde](monitoring-classic-retirement.md#retirement-of-classic-monitoring-and-alerting-platform).

I když nástroj může migrovat téměř všechna [klasická pravidla výstrah](monitoring-classic-retirement.md#retirement-of-classic-monitoring-and-alerting-platform), existují výjimky. Následující pravidla upozornění nebudou migrována pomocí nástroje (nebo během automatické migrace od září 2019):

- Klasická pravidla upozornění na metriky hostů virtuálních počítačů (Windows i Linux). Přečtěte si [pokyny k opětovnému vytvoření těchto pravidel upozornění v nových výstrahách metriky](#guest-metrics-on-virtual-machines) dále v tomto článku.
- Klasická pravidla upozornění na klasických metrikách úložiště Podívejte se na [pokyny pro monitorování účtů klasického úložiště](https://azure.microsoft.com/blog/modernize-alerting-using-arm-storage-accounts/).
- Klasická pravidla výstrah pro některé metriky účtu úložiště. Další [](#storage-account-metrics) informace najdete v části dále v tomto článku.
- Pravidla pro klasické výstrahy pro některé metriky Cosmos DB. Další [](#cosmos-db-metrics) informace najdete v části dále v tomto článku.
- Klasická pravidla pro upozornění na všechny klasické virtuální počítače a metriky Cloud Services (Microsoft. ClassicCompute/virtualMachines a Microsoft. ClassicCompute/domainNames/sloty/role). Další [](#classic-compute-metrics) informace najdete v části dále v tomto článku.

Pokud má vaše předplatné nějaká pravidla klasického modelu, je nutné je ručně migrovat. Vzhledem k tomu, že nemůžeme poskytnout automatickou migraci, všechny existující klasické výstrahy metriky těchto typů budou i nadále fungovat do června 2020. Toto rozšíření vám dává čas přejít na nové výstrahy. Žádné nové klasické výstrahy však nelze vytvořit po 2019. srpna.

> [!NOTE]
> Kromě výše uvedených výjimek, pokud jsou pravidla pro vaše Klasická upozornění neplatná, tj. se jedná o [zastaralé metriky](#classic-alert-rules-on-deprecated-metrics) nebo prostředky, které byly odstraněny, nebudou migrovány během dobrovolné migrace. Při automatické migraci dojde k odstranění všech takových neplatných pravidel upozornění na klasický čas.

### <a name="guest-metrics-on-virtual-machines"></a>Metriky hostů na virtuálních počítačích

Než budete moct vytvořit nové výstrahy metriky pro metriky hostů, musí se metriky hosta odeslat do úložiště vlastních metrik Azure Monitor. Postupujte podle těchto pokynů a povolte Azure Monitor jímka v nastavení diagnostiky:

- [Povolení metrik hosta pro virtuální počítače s Windows](collect-custom-metrics-guestos-resource-manager-vm.md)
- [Povolení metrik hosta pro virtuální počítače se systémem Linux](collect-custom-metrics-linux-telegraf.md)

Po dokončení těchto kroků můžete pro metriky hostů vytvořit nové výstrahy metriky. A po vytvoření nových výstrah metriky můžete klasické výstrahy odstranit.

### <a name="storage-account-metrics"></a>Metriky účtu úložiště

Všechny klasické výstrahy na účtech úložiště se dají migrovat s výjimkou upozornění na tyto metriky:

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

Klasická pravidla upozornění na metriky v procentech se musí migrovat na základě [mapování mezi starými a novými metrikami úložiště](https://docs.microsoft.com/azure/storage/common/storage-metrics-migration#metrics-mapping-between-old-metrics-and-new-metrics). Prahové hodnoty bude nutné upravit odpovídajícím způsobem, protože nová dostupná metrika je absolutní.

Klasická pravidla upozornění na AnonymousThrottlingError, SASThrottlingError a ThrottlingError musí být rozdělená na dvě nové výstrahy, protože neexistuje žádná kombinovaná metrika, která by poskytovala stejné funkce. Prahové hodnoty bude nutné upravit odpovídajícím způsobem.

### <a name="cosmos-db-metrics"></a>Metriky služby Cosmos DB

U všech klasických upozornění na Cosmos DB metriky se dá migrovat s výjimkou upozornění na tyto metriky:

- Průměrný počet požadavků za sekundu
- Úrovně konzistence
- Http 2xx
- Http 3xx
- HTTP 400
- HTTP 401
- Vnitřní chyba serveru
- Maximální počet spotřebovaných RUPM za minutu
- Maximální počet ru za sekundu
- Neúspěšné požadavky počtu Mongo
- Mongo odstranit neúspěšné žádosti
- Mongo vložit neúspěšné žádosti
- Mongo jiných neúspěšných žádostí
- Mongo další poplatek za požadavek
- Mongo další rychlost žádosti
- Neúspěšné požadavky dotazu Mongo
- Neúspěšné žádosti o aktualizaci Mongo
- Pozorovaná latence čtení
- Pozorovaná latence zápisu
- Dostupnost služby
- Kapacita úložiště
- Omezené žádosti
- Požadavky celkem

Průměrný počet požadavků za sekundu, úroveň konzistence, maximální počet RUPM spotřebovaných za minutu, Max ru za sekundu, zjištěnou latenci čtení, pozorovaná latence zápisu, kapacita úložiště v současnosti není v [novém systému](metrics-supported.md#microsoftdocumentdbdatabaseaccounts)dostupná.

Výstrahy na metrikách žádostí, jako je HTTP 2xx, http 3xx, HTTP 400, HTTP 401, interní chyba serveru, dostupnost služby, omezené žádosti a celkový počet požadavků, se nemigrují, protože způsob, jakým se počítají žádosti, se liší od klasických metrik a nových metrik. Výstrahy na těchto počítačích bude nutné ručně znovu vytvořit se upravenými mezními hodnotami.

Výstrahy na metriky neúspěšných žádostí Mongo musí být rozdělené do několika výstrah, protože neexistuje žádná kombinovaná metrika, která by poskytovala stejné funkce. Prahové hodnoty bude nutné upravit odpovídajícím způsobem.

### <a name="classic-compute-metrics"></a>Klasické výpočetní metriky

Žádné výstrahy na klasických výpočetních metrikách nebudou migrovány pomocí nástroje pro migraci, protože klasické výpočetní prostředky se ještě nepodporují s novými výstrahami. Podpora nových výstrah u těchto typů prostředků se přidá do budoucna. Jakmile je tato skutečnost k dispozici, zákazníci musí znovu vytvořit nová ekvivalentní pravidla výstrah na základě jejich klasických pravidel upozornění do června 2020.

### <a name="classic-alert-rules-on-deprecated-metrics"></a>Pravidla pro klasické výstrahy na zastaralých metrikách

Jedná se o pravidla pro klasických výstrah pro metriky, které byly dříve podporovány, ale byly nakonec zastaralé. Malé procento zákazníků může mít pro takové metriky neplatná pravidla klasických výstrah. Vzhledem k tomu, že tato pravidla upozornění nejsou platná, nebudou migrována.

| Typ prostředku| Zastaralé metriky |
|-------------|----------------- |
| Microsoft.DBforMySQL/servers | compute_consumption_percent, compute_limit |
| Microsoft.DBforPostgreSQL/servers | compute_consumption_percent, compute_limit |
| Microsoft.Network/publicIPAddresses | defaultddostriggerrate |
| Microsoft.SQL/servers/databases | service_level_objective, storage_limit, storage_used, omezování, dtu_consumption_percent, storage_used |
| Microsoft.Web/hostingEnvironments/multirolepools | averagememoryworkingset |
| Microsoft.Web/hostingEnvironments/workerpools | BytesReceived, httpqueuelength |

## <a name="how-equivalent-new-alert-rules-and-action-groups-are-created"></a>Jak se vytvářejí ekvivalentní nová pravidla upozornění a skupiny akcí

Nástroj pro migraci převede pravidla vašich klasických výstrah na ekvivalentní nová pravidla upozornění a skupiny akcí. Pro většinu klasických pravidel výstrah jsou stejná nová pravidla upozornění na stejné metrikě se stejnými vlastnostmi, jako `windowSize` jsou `aggregationType`a. Existují však některá klasická pravidla pro výstrahy, která mají jinou ekvivalentní metriku v novém systému. Následující zásady platí pro migraci klasických výstrah, pokud nejsou uvedené v následující části:

- **Frekvence**: Definuje, jak často pravidlo klasického nebo nového upozornění kontroluje podmínku. Nastavení `frequency` v klasických pravidlech výstrah nebylo uživatelem konfigurovatelné a bylo vždy 5 minut pro všechny typy prostředků s výjimkou Application Insightsch komponent, pro které bylo 1 min. Frekvence ekvivalentních pravidel je také nastavená na 5 min a 1 min.
- **Typ agregace**: Definuje, jak je metrika agregována nad oknem zájmu. `aggregationType` Je také totéž mezi klasickými výstrahami a novými výstrahami pro většinu metrik. V některých případech, protože se metrika liší mezi výstrahami Classic a novými výstrahami `aggregationType` , je `primary Aggregation Type` použita ekvivalentní nebo definovaná pro metriku.
- **Jednotky**: Vlastnost metriky, na které je vytvořena výstraha. Některé ekvivalentní metriky mají různé jednotky. Prahová hodnota se podle potřeby upraví. Například pokud má původní metrika sekundy jako jednotky, ale ekvivalentní nová metrika má milisekundy jako jednotky, původní prahová hodnota se vynásobí 1000, aby se zajistilo stejné chování.
- **Velikost okna**: Definuje okno, přes které se agreguje data metriky pro porovnání s prahovou hodnotou. U standardních `windowSize` hodnot, jako jsou 5mins, 15mins, 30mins, hodinového, 3hours, 6 hodin, 12 hodin, 1 den, se u ekvivalentního nového pravidla výstrahy neudělala žádná změna. Pro jiné hodnoty se vybere nejpodobnější `windowSize` , která se má použít. U většiny zákazníků neexistuje žádný vliv na tuto změnu. Pro malé procento zákazníků může být potřeba upravit prahovou hodnotu a získat tak přesně stejné chování.

V následujících částech jsme podrobně nastavili metriky, které mají jinou ekvivalentní metriku v novém systému. Žádná metrika, která zůstává stejná pro klasický a nová pravidla upozornění, není uvedená. Seznam metrik podporovaných v novém systému najdete [tady](metrics-supported.md).

### <a name="microsoftstorageaccountsservices"></a>Microsoft.StorageAccounts/services

Pro služby účtu úložiště, jako je například blob, Table, File a Queue, jsou následující metriky namapovány na ekvivalentní metriky, jak je znázorněno níže:

| Metrika v klasických výstrahách | Ekvivalentní metrika v nových výstrahách | Komentáře|
|--------------------------|---------------------------------|---------|
| AnonymousAuthorizationError| Metrika transakcí s dimenzemi "ResponseType" = "AuthorizationError" a "Authentication" = "anonymous"| |
| AnonymousClientOtherError | Metrika transakcí s dimenzemi "ResponseType" = "ClientOtherError" a "Authentication" = "anonymous" | |
| AnonymousClientTimeOutError| Metrika transakcí s dimenzemi "ResponseType" = "ClientTimeOutError" a "Authentication" = "anonymous" | |
| AnonymousNetworkError | Metrika transakcí s dimenzemi "ResponseType" = "NetworkError" a "Authentication" = "anonymous" | |
| AnonymousServerOtherError | Metrika transakcí s dimenzemi "ResponseType" = "ServerOtherError" a "Authentication" = "anonymous" | |
| AnonymousServerTimeOutError | Metrika transakcí s dimenzemi "ResponseType" = "ServerTimeOutError" a "Authentication" = "anonymous" | |
| AnonymousSuccess | Metrika transakcí s dimenzemi "ResponseType" = "úspěch" a "ověřování" = "anonymní" | |
| AuthorizationError | Metrika transakcí s dimenzemi "ResponseType" = "AuthorizationError" | |
| Hodnotu averagee2elatency | SuccessE2ELatency | |
| Hodnotu averageserverlatency | SuccessServerLatency | |
| Kapacita | BlobCapacity | Použijte `aggregationType` ' Average ' místo ' Last '. Metrika se vztahuje pouze na služby BLOB Services. |
| ClientOtherError | Metrika transakcí s dimenzemi "ResponseType" = "ClientOtherError"  | |
| ClientTimeoutError | Metrika transakcí s dimenzemi "ResponseType" = "ClientTimeOutError" | |
| ContainerCount | ContainerCount | Použijte `aggregationType` ' Average ' místo ' Last '. Metrika se vztahuje pouze na služby BLOB Services. |
| NetworkError | Metrika transakcí s dimenzemi "ResponseType" = "NetworkError" | |
| ObjectCount | BlobCount| Použijte `aggregationType` ' Average ' místo ' Last '. Metrika se vztahuje pouze na služby BLOB Services. |
| SASAuthorizationError | Metrika transakcí s rozměry "ResponseType" = "AuthorizationError" a "ověřování" = "SAS" | |
| SASClientOtherError | Metrika transakcí s rozměry "ResponseType" = "ClientOtherError" a "ověřování" = "SAS" | |
| SASClientTimeOutError | Metrika transakcí s rozměry "ResponseType" = "ClientTimeOutError" a "ověřování" = "SAS" | |
| SASNetworkError | Metrika transakcí s rozměry "ResponseType" = "NetworkError" a "ověřování" = "SAS" | |
| SASServerOtherError | Metrika transakcí s rozměry "ResponseType" = "ServerOtherError" a "ověřování" = "SAS" | |
| SASServerTimeOutError | Metrika transakcí s rozměry "ResponseType" = "ServerTimeOutError" a "ověřování" = "SAS" | |
| SASSuccess | Metrika transakcí s dimenzemi "ResponseType" = "úspěch" a "ověřování" = "SAS" | |
| ServerOtherError | Metrika transakcí s dimenzemi "ResponseType" = "ServerOtherError" | |
| ServerTimeOutError | Metrika transakcí s dimenzemi "ResponseType" = "ServerTimeOutError"  | |
| Úspěch | Metrika transakcí s dimenzemi "ResponseType" = "úspěch" | |
| TotalBillableRequests| Transakce | |
| TotalEgress | Výchozí přenos | |
| Totalbillablerequests | Příchozí přenos dat | |
| TotalRequests | Transakce | |

### <a name="microsoftinsightscomponents"></a>Microsoft. Insights/Components

Pro Application Insights se používají ekvivalentní metriky, jak je znázorněno níže:

| Metrika v klasických výstrahách | Ekvivalentní metrika v nových výstrahách | Komentáře|
|--------------------------|---------------------------------|---------|
| Availability. availabilityMetric. Value | availabilityResults/availabilityPercentage|   |
| Availability. durationMetric. Value | availabilityResults/doba trvání| Vynásobit původní prahovou hodnotu hodnotou 1000 jako jednotky pro klasickou metriku v sekundách a pro nové jsou v milisekundách.  |
| basicExceptionBrowser.count | výjimky/prohlížeč|  Použijte `aggregationType` ' count ' místo ' Sum '. |
| basicExceptionServer.count | výjimky/Server| Použijte `aggregationType` ' count ' místo ' Sum '.  |
| clientPerformance.clientProcess.value | browserTimings/processingDuration| Vynásobit původní prahovou hodnotu hodnotou 1000 jako jednotky pro klasickou metriku v sekundách a pro nové jsou v milisekundách.  |
| clientPerformance.networkConnection.value | browserTimings/networkDuration|  Vynásobit původní prahovou hodnotu hodnotou 1000 jako jednotky pro klasickou metriku v sekundách a pro nové jsou v milisekundách. |
| clientPerformance.receiveRequest.value | browserTimings/receiveDuration| Vynásobit původní prahovou hodnotu hodnotou 1000 jako jednotky pro klasickou metriku v sekundách a pro nové jsou v milisekundách.  |
| clientPerformance.sendRequest.value | browserTimings/sendDuration| Vynásobit původní prahovou hodnotu hodnotou 1000 jako jednotky pro klasickou metriku v sekundách a pro nové jsou v milisekundách.  |
| clientPerformance.total.value | browserTimings/totalDuration| Vynásobit původní prahovou hodnotu hodnotou 1000 jako jednotky pro klasickou metriku v sekundách a pro nové jsou v milisekundách.  |
| performanceCounter.available_bytes.value | Čítače výkonu/memoryAvailableBytes|   |
| performanceCounter.io_data_bytes_per_sec.value | performanceCounters/processIOBytesPerSecond|   |
| performanceCounter.number_of_exceps_thrown_per_sec.value | performanceCounters/exceptionsPerSecond|   |
| performanceCounter.percentage_processor_time_normalized.value | Čítače výkonu/processCpuPercentage|   |
| performanceCounter.percentage_processor_time.value | Čítače výkonu/processCpuPercentage| Prahová hodnota bude muset být vhodně upravena, protože původní metrika byla napříč všemi jádry a nová metrika je normalizována na jednu jádro. Nástroj pro migraci nemění prahové hodnoty.  |
| performanceCounter.percentage_processor_total.value | Čítače výkonu/processorCpuPercentage|   |
| performanceCounter.process_private_bytes.value | performanceCounters/processPrivateBytes|   |
| performanceCounter.request_execution_time.value | Čítače výkonu/requestExecutionTime|   |
| performanceCounter.requests_in_application_queue.value | performanceCounters/requestsInQueue|   |
| performanceCounter.requests_per_sec.value | performanceCounters/requestsPerSecond|   |
| Request. Duration | žádosti/doba trvání| Vynásobit původní prahovou hodnotu hodnotou 1000 jako jednotky pro klasickou metriku v sekundách a pro nové jsou v milisekundách.  |
| Request. Rate | žádosti/rychlost|   |
| requestFailed.count | požadavky/selhání| Použijte `aggregationType` ' count ' místo ' Sum '.   |
| Zobrazit. počet | pageViews/Count| Použijte `aggregationType` ' count ' místo ' Sum '.   |

### <a name="microsoftdocumentdbdatabaseaccounts"></a>Microsoft. DocumentDB/databaseAccounts

Pro Cosmos DB se používají ekvivalentní metriky, jak je znázorněno níže:

| Metrika v klasických výstrahách | Ekvivalentní metrika v nových výstrahách | Komentáře|
|--------------------------|---------------------------------|---------|
| AvailableStorage     |AvailableStorage|   |
| Velikost dat | Využití datausage| |
| Počet dokumentů | DocumentCount||
| Velikost indexu | IndexUsage||
| Poplatek za požadavek na počet Mongo| MongoRequestCharge s dimenzí "Command." = "Count"||
| Frekvence požadavků na počet Mongo | MongoRequestsCount s dimenzí "Command." = "Count"||
| Poplatek za žádost o odstranění Mongo | MongoRequestCharge s dimenzí "Command." = "Delete"||
| Frekvence žádosti o odstranění Mongo | MongoRequestsCount s dimenzí "Command." = "Delete"||
| Poplatek za požadavek na vložení Mongo | MongoRequestCharge s dimenzí "Command." = "Insert"||
| Frekvence požadavků na vložení Mongo | MongoRequestsCount s dimenzí "Command." = "Insert"||
| Poplatek za požadavek na dotaz na Mongo | MongoRequestCharge s dimenzí "Command." = "Find"||
| Frekvence požadavků na dotaz Mongo | MongoRequestsCount s dimenzí "Command." = "Find"||
| Poplatek za žádost o aktualizaci Mongo | MongoRequestCharge s dimenzí "Command." = "Update"||
| Služba není dostupná| ServiceAvailability||
| TotalRequestUnits | TotalRequestUnits||

### <a name="how-equivalent-action-groups-are-created"></a>Způsob vytvoření ekvivalentních skupin akcí

Klasická pravidla výstrah obsahovala akce e-mailu, Webhooku, aplikace logiky a sady Runbook vázané na samotné pravidlo výstrahy. Nová pravidla upozornění používají skupiny akcí, které je možné použít v rámci více pravidel upozornění. Nástroj pro migraci vytvoří jednu skupinu akcí pro stejné akce bez ohledu na to, kolik pravidel upozornění tuto akci používá. Skupiny akcí vytvořené nástrojem pro migraci používají formát pojmenování "Migrated_AG *".

> [!NOTE]
> Klasické výstrahy odesílají lokalizované e-maily na základě národního prostředí klasického správce při použití pro upozornění na role klasického správce. Nové e-maily výstrah jsou odesílány prostřednictvím skupin akcí a jsou pouze v angličtině.

## <a name="rollout-phases"></a>Fáze zavedení

Nástroj pro migraci probíhá ve fázích pro zákazníky, kteří používají pravidla pro klasických výstrah. Vlastníci předplatného obdrží e-mail, když je předplatné připravené k migraci pomocí tohoto nástroje.

> [!NOTE]
> Vzhledem k tomu, že se nástroj zavádí v fázích, může se stát, že některá z vašich předplatných ještě nejsou připravené k migraci v počátečních fázích.

Většina předplatných je aktuálně označena jako připravená k migraci. Pouze předplatná, která mají klasické výstrahy na následujících typech prostředků, nejsou stále připravena k migraci.

- Microsoft. classicCompute/domainNames/sloty/role
- Microsoft. Insights/Components

## <a name="who-can-trigger-the-migration"></a>Kdo může migraci aktivovat?

Migraci může aktivovat libovolný uživatel, který má integrovanou roli Přispěvatel monitorování na úrovni předplatného. Migrace může spustit i uživatelé, kteří mají vlastní roli s následujícími oprávněními:

- */read
- Microsoft. Insights/actiongroups/*
- Microsoft.Insights/AlertRules/*
- Microsoft. Insights/metricAlerts/*
- Microsoft. AlertsManagement/smartDetectorAlertRules/*

> [!NOTE]
> Kromě výše uvedených oprávnění by mělo být předplatné navíc zaregistrované u poskytovatele prostředků Microsoft. AlertsManagement. Tato chyba je nutná k úspěšné migraci upozornění na anomálii selhání na Application Insights. 

## <a name="common-problems-and-remedies"></a>Běžné problémy a nápravná opatření

Po [aktivaci migrace](alerts-using-migration-tool.md)obdržíte e-mailovou adresu, kterou jste zadali, a upozorní vás, že migrace je dokončená, nebo jestli je potřeba provést nějakou akci. Tato část popisuje některé běžné problémy a jejich řešení.

### <a name="validation-failed"></a>Ověření se nepovedlo

Vzhledem k nějakým nedávným změnám pravidel výstrah v rámci vašeho předplatného není možné migrovat předplatné. Tento problém je dočasný. Po přesunutí stavu migrace do několika dnů můžete migraci restartovat znovu .

### <a name="scope-lock-preventing-us-from-migrating-your-rules"></a>Zámek oboru brání migraci vašich pravidel.

V rámci migrace se vytvoří nové výstrahy metriky a nové skupiny akcí a pak se odstraní klasická pravidla výstrah. Zámek oboru ale může zabránit vytváření a odstraňování prostředků. V závislosti na zámku oboru nelze některá nebo všechna pravidla migrovat. Tento problém můžete vyřešit tak, že odeberete zámek oboru pro předplatné, skupinu prostředků nebo prostředek, který je uvedený v nástroji pro [migraci](https://portal.azure.com/#blade/Microsoft_Azure_Monitoring/MigrationBladeViewModel), a znovu spustíte migraci. Zámek oboru nelze zakázat a je třeba jej odebrat po dobu trvání procesu migrace. [Přečtěte si další informace o správě zámků oboru](../../azure-resource-manager/resource-group-lock-resources.md#portal).

### <a name="policy-with-deny-effect-preventing-us-from-migrating-your-rules"></a>Zásady s efektem odepření nám brání v migraci vašich pravidel.

V rámci migrace se vytvoří nové výstrahy metriky a nové skupiny akcí a pak se odstraní klasická pravidla výstrah. Zásady ale můžou zabránit v vytváření prostředků. V závislosti na zásadách se některá nebo všechna pravidla nedala migrovat. Zásady blokující proces jsou uvedeny v [nástroji pro migraci](https://portal.azure.com/#blade/Microsoft_Azure_Monitoring/MigrationBladeViewModel). Vyřešte tento problém jedním z těchto problémů:

- Vyloučení předplatných nebo skupin prostředků po dobu trvání procesu migrace z přiřazení zásad. [Přečtěte si další informace o správě rozsahu vyloučení zásad](../../governance/policy/tutorials/create-and-manage.md#exempt-a-non-compliant-or-denied-resource-using-exclusion).
- Odebrání nebo změna efektu na audit nebo připojení (což může například vyřešit problémy týkající se chybějících značek). [Přečtěte si další informace o správě účinku zásad](../../governance/policy/concepts/definition-structure.md#policy-rule).

## <a name="next-steps"></a>Další kroky

- [Způsoby použití nástroje pro migraci](alerts-using-migration-tool.md)
- [Příprava na migraci](alerts-prepare-migration.md)
