---
title: Vysvětlení migrace pro výstrahy Azure Monitor
description: Seznamte se s tím, jak funguje migrace výstrah a řešení problémů.
ms.topic: conceptual
ms.date: 07/10/2019
ms.author: yalavi
author: yalavi
ms.subservice: alerts
ms.openlocfilehash: 0c4c36c61b73e5c5625d02ae581d186e7dc2c9de
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/17/2021
ms.locfileid: "100609558"
---
# <a name="understand-migration-options-to-newer-alerts"></a>Vysvětlení možností migrace s novějšími výstrahami

Klasické výstrahy jsou [vyřazené](../platform/monitoring-classic-retirement.md) pro uživatele veřejného cloudu, ale i v omezeném počtu použití pro prostředky, které ještě nepodporují nové výstrahy. Nové datum bude brzy oznámeno pro migraci zbývajících výstrah, [Azure Government Cloud](../../azure-government/documentation-government-welcome.md)a [Azure Čína 21Vianet](https://docs.azure.cn/).

Tento článek vysvětluje, jak funguje ruční migrace a dobrovolný migrační nástroj, který se použije k migraci zbývajících pravidel upozornění. Popisuje také nápravná opatření pro některé běžné problémy.

> [!IMPORTANT]
> Migrace neovlivní výstrahy protokolu aktivit (včetně upozornění na stav služby) a výstrahy protokolu. Migrace se vztahuje pouze na pravidla pro klasické výstrahy, která jsou popsaná [zde](../platform/monitoring-classic-retirement.md#retirement-of-classic-monitoring-and-alerting-platform).

> [!NOTE]
> Pokud jsou pravidla vašich klasických výstrah neplatná, tj. jsou v [zastaralých metrikách](#classic-alert-rules-on-deprecated-metrics) nebo prostředky, které byly odstraněny, nebudou migrovány a nebudou k dispozici po vyřazení služby.

## <a name="manually-migrating-classic-alerts-to-newer-alerts"></a>Ruční migrace klasických výstrah na novější výstrahy

Zákazníci, kteří mají zájem o ruční migraci zbývajících výstrah, už můžou dělat pomocí následujících oddílů. Tyto oddíly také definují metriky, které jsou vyřazeny poskytovatelem prostředků a aktuálně se nedají migrovat přímo.

### <a name="guest-metrics-on-virtual-machines"></a>Metriky hostů na virtuálních počítačích

Než budete moct vytvořit nové výstrahy metriky pro metriky hostů, musí se metriky hosta odeslat do úložiště vlastních metrik Azure Monitor. Postupujte podle těchto pokynů a povolte Azure Monitor jímka v nastavení diagnostiky:

- [Povolení metrik hosta pro virtuální počítače s Windows](../platform/collect-custom-metrics-guestos-resource-manager-vm.md)
- [Povolení metrik hosta pro virtuální počítače se systémem Linux](../platform/collect-custom-metrics-linux-telegraf.md)

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

Klasická pravidla upozornění na metriky v procentech se musí migrovat na základě [mapování mezi starými a novými metrikami úložiště](../../storage/common/storage-metrics-migration.md#metrics-mapping-between-old-metrics-and-new-metrics). Prahové hodnoty bude nutné upravit odpovídajícím způsobem, protože nová dostupná metrika je absolutní.

Klasická pravidla upozornění na AnonymousThrottlingError, SASThrottlingError a ThrottlingError musí být rozdělená na dvě nové výstrahy, protože neexistuje žádná kombinovaná metrika, která by poskytovala stejné funkce. Prahové hodnoty bude nutné upravit odpovídajícím způsobem.

### <a name="cosmos-db-metrics"></a>Metriky služby Cosmos DB

U všech klasických upozornění na Cosmos DB metriky se dá migrovat s výjimkou upozornění na tyto metriky:

- Průměrný počet požadavků za sekundu
- Úroveň konzistence
- 2xx http
- 3xx http
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

Průměrný počet požadavků za sekundu, úroveň konzistence, maximální počet RUPM spotřebovaných za minutu, Max ru za sekundu, zjištěnou latenci čtení, pozorovaná latence zápisu, kapacita úložiště v současnosti není v [novém systému](../platform/metrics-supported.md#microsoftdocumentdbdatabaseaccounts)dostupná.

Výstrahy na metrikách žádostí, jako je HTTP 2xx, http 3xx, HTTP 400, HTTP 401, interní chyba serveru, dostupnost služby, omezené žádosti a celkový počet požadavků, se nemigrují, protože způsob, jakým se počítají žádosti, se liší od klasických metrik a nových metrik. Výstrahy na těchto počítačích bude nutné ručně znovu vytvořit se upravenými mezními hodnotami.

Výstrahy na metriky neúspěšných žádostí Mongo musí být rozdělené do několika výstrah, protože neexistuje žádná kombinovaná metrika, která by poskytovala stejné funkce. Prahové hodnoty bude nutné upravit odpovídajícím způsobem.

### <a name="classic-compute-metrics"></a>Klasické výpočetní metriky

Žádné výstrahy na klasických výpočetních metrikách nebudou migrovány pomocí nástroje pro migraci, protože klasické výpočetní prostředky se ještě nepodporují s novými výstrahami. Podpora nových výstrah na těchto typech prostředků je aktuálně ve verzi Public Preview a zákazníci mohou znovu vytvořit nová ekvivalentní pravidla upozornění na základě pravidel jejich klasických výstrah.

### <a name="classic-alert-rules-on-deprecated-metrics"></a>Pravidla pro klasické výstrahy na zastaralých metrikách

Jedná se o pravidla pro klasických výstrah pro metriky, které byly dříve podporovány, ale byly nakonec zastaralé. Malé procento zákazníků může mít pro takové metriky neplatná pravidla klasických výstrah. Vzhledem k tomu, že tato pravidla upozornění nejsou platná, nebudou migrována.

| Typ prostředku| Zastaralé metriky |
|-------------|----------------- |
| Microsoft. DBforMySQL/servery | compute_consumption_percent compute_limit |
| Microsoft. DBforPostgreSQL/servery | compute_consumption_percent compute_limit |
| Microsoft. Network/publicIPAddresses | defaultddostriggerrate |
| Microsoft. SQL/servery/databáze | service_level_objective, storage_limit, storage_used, omezování, dtu_consumption_percent storage_used |
| Microsoft. Web/hostingEnvironments/multirolepools | averagememoryworkingset |
| Microsoft. Web/hostingEnvironments/workerpools | BytesReceived, httpqueuelength |

## <a name="how-equivalent-new-alert-rules-and-action-groups-are-created"></a>Jak se vytvářejí ekvivalentní nová pravidla upozornění a skupiny akcí

Nástroj pro migraci převede pravidla vašich klasických výstrah na ekvivalentní nová pravidla upozornění a skupiny akcí. Pro většinu klasických pravidel výstrah jsou stejná nová pravidla upozornění na stejné metrikě se stejnými vlastnostmi, jako jsou `windowSize` a `aggregationType` . Existují však některá klasická pravidla pro výstrahy, která mají jinou ekvivalentní metriku v novém systému. Následující zásady platí pro migraci klasických výstrah, pokud nejsou uvedené v následující části:

- **Frekvence**: definuje, jak často pravidlo klasického nebo nového upozornění kontroluje podmínku. Nastavení `frequency` v klasických pravidlech výstrah nebylo uživatelem konfigurovatelné a bylo vždy 5 minut pro všechny typy prostředků s výjimkou Application Insightsch komponent, pro které bylo 1 min. Frekvence ekvivalentních pravidel je také nastavená na 5 min a 1 min.
- **Typ agregace**: definuje, jak je metrika agregována nad oknem zájmu. `aggregationType`Je také totéž mezi klasickými výstrahami a novými výstrahami pro většinu metrik. V některých případech, protože se metrika liší mezi výstrahami Classic a novými výstrahami, `aggregationType` `primary Aggregation Type` je použita ekvivalentní nebo definovaná pro metriku.
- **Units**: vlastnost metriky, na které je vytvořena výstraha. Některé ekvivalentní metriky mají různé jednotky. Prahová hodnota se podle potřeby upraví. Například pokud má původní metrika sekundy jako jednotky, ale ekvivalentní nová metrika má milisekundy jako jednotky, původní prahová hodnota se vynásobí 1000, aby se zajistilo stejné chování.
- **Velikost okna**: definuje okno, přes které se agreguje data metriky pro porovnání s prahovou hodnotou. U standardních `windowSize` hodnot, jako jsou 5mins, 15mins, 30mins, hodinového, 3hours, 6 hodin, 12 hodin, 1 den, se u ekvivalentního nového pravidla výstrahy neudělala žádná změna. Pro jiné hodnoty se vybere nejpodobnější, která se má `windowSize` použít. U většiny zákazníků neexistuje žádný vliv na tuto změnu. Pro malé procento zákazníků může být potřeba upravit prahovou hodnotu a získat tak přesně stejné chování.

V následujících částech jsme podrobně nastavili metriky, které mají jinou ekvivalentní metriku v novém systému. Žádná metrika, která zůstává stejná pro klasický a nová pravidla upozornění, není uvedená. Seznam metrik podporovaných v novém systému najdete [tady](../platform/metrics-supported.md).

### <a name="microsoftstorageaccountsservices"></a>Microsoft. StorageAccounts/Services

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
| AverageE2ELatency | SuccessE2ELatency | |
| AverageServerLatency | SuccessServerLatency | |
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
| Success | Metrika transakcí s dimenzemi "ResponseType" = "úspěch" | |
| TotalBillableRequests| Transakce | |
| TotalEgress | Výchozí přenos dat | |
| TotalIngress | Příchozí přenos dat | |
| TotalRequests | Transakce | |

### <a name="microsoftinsightscomponents"></a>Microsoft. Insights/Components

Pro Application Insights se používají ekvivalentní metriky, jak je znázorněno níže:

| Metrika v klasických výstrahách | Ekvivalentní metrika v nových výstrahách | Komentáře|
|--------------------------|---------------------------------|---------|
| Availability. availabilityMetric. Value | availabilityResults/availabilityPercentage|   |
| Availability. durationMetric. Value | availabilityResults/doba trvání| Vynásobit původní prahovou hodnotu hodnotou 1000 jako jednotky pro klasickou metriku v sekundách a pro nové jsou v milisekundách.  |
| basicExceptionBrowser. Count | výjimky/prohlížeč|  Použijte `aggregationType` ' count ' místo ' Sum '. |
| basicExceptionServer. Count | výjimky/Server| Použijte `aggregationType` ' count ' místo ' Sum '.  |
| clientPerformance. clientProcess. Value | browserTimings/processingDuration| Vynásobit původní prahovou hodnotu hodnotou 1000 jako jednotky pro klasickou metriku v sekundách a pro nové jsou v milisekundách.  |
| clientPerformance. networkConnection. Value | browserTimings/networkDuration|  Vynásobit původní prahovou hodnotu hodnotou 1000 jako jednotky pro klasickou metriku v sekundách a pro nové jsou v milisekundách. |
| clientPerformance. receiveRequest. Value | browserTimings/receiveDuration| Vynásobit původní prahovou hodnotu hodnotou 1000 jako jednotky pro klasickou metriku v sekundách a pro nové jsou v milisekundách.  |
| clientPerformance. sendRequest. Value | browserTimings/sendDuration| Vynásobit původní prahovou hodnotu hodnotou 1000 jako jednotky pro klasickou metriku v sekundách a pro nové jsou v milisekundách.  |
| clientPerformance. Total. Value | browserTimings/totalDuration| Vynásobit původní prahovou hodnotu hodnotou 1000 jako jednotky pro klasickou metriku v sekundách a pro nové jsou v milisekundách.  |
| performanceCounter.available_bytes. Value | Čítače výkonu/memoryAvailableBytes|   |
| performanceCounter.io_data_bytes_per_sec. Value | Čítače výkonu/processIOBytesPerSecond|   |
| performanceCounter.number_of_exceps_thrown_per_sec. Value | Čítače výkonu/exceptionsPerSecond|   |
| performanceCounter.percentage_processor_time_normalized. Value | Čítače výkonu/processCpuPercentage|   |
| performanceCounter.percentage_processor_time. Value | Čítače výkonu/processCpuPercentage| Prahová hodnota bude muset být vhodně upravena, protože původní metrika byla napříč všemi jádry a nová metrika je normalizována na jednu jádro. Nástroj pro migraci nemění prahové hodnoty.  |
| performanceCounter.percentage_processor_total. Value | Čítače výkonu/processorCpuPercentage|   |
| performanceCounter.process_private_bytes. Value | Čítače výkonu/processPrivateBytes|   |
| performanceCounter.request_execution_time. Value | Čítače výkonu/requestExecutionTime|   |
| performanceCounter.requests_in_application_queue. Value | Čítače výkonu/requestsInQueue|   |
| performanceCounter.requests_per_sec. Value | Čítače výkonu/requestsPerSecond|   |
| Request. Duration | žádosti/doba trvání| Vynásobit původní prahovou hodnotu hodnotou 1000 jako jednotky pro klasickou metriku v sekundách a pro nové jsou v milisekundách.  |
| Request. Rate | žádosti/rychlost|   |
| requestFailed. Count | požadavky/selhání| Použijte `aggregationType` ' count ' místo ' Sum '.   |
| Zobrazit. počet | pageViews/Count| Použijte `aggregationType` ' count ' místo ' Sum '.   |

### <a name="microsoftdocumentdbdatabaseaccounts"></a>Microsoft.DocumentDB/databaseAccounts

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
| Služba není k dispozici| ServiceAvailability||
| TotalRequestUnits | TotalRequestUnits||

### <a name="how-equivalent-action-groups-are-created"></a>Způsob vytvoření ekvivalentních skupin akcí

Klasická pravidla výstrah obsahovala akce e-mailu, Webhooku, aplikace logiky a sady Runbook vázané na samotné pravidlo výstrahy. Nová pravidla upozornění používají skupiny akcí, které je možné použít v rámci více pravidel upozornění. Nástroj pro migraci vytvoří jednu skupinu akcí pro stejné akce bez ohledu na to, kolik pravidel upozornění tuto akci používá. Skupiny akcí vytvořené nástrojem pro migraci používají formát pojmenování "Migrated_AG *".

> [!NOTE]
> Klasické výstrahy odesílají lokalizované e-maily na základě národního prostředí klasického správce při použití pro upozornění na role klasického správce. Nové e-maily výstrah jsou odesílány prostřednictvím skupin akcí a jsou pouze v angličtině.

## <a name="rollout-phases"></a>Fáze uvedení

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
- Microsoft. Insights/AlertRules/*
- Microsoft. Insights/metricAlerts/*
- Microsoft. AlertsManagement/smartDetectorAlertRules/*

> [!NOTE]
> Kromě výše uvedených oprávnění by mělo být předplatné navíc zaregistrované u poskytovatele prostředků Microsoft. AlertsManagement. Tato chyba je nutná k úspěšné migraci upozornění na anomálii selhání na Application Insights. 

## <a name="common-problems-and-remedies"></a>Běžné problémy a nápravná opatření

Po [aktivaci migrace](alerts-using-migration-tool.md)obdržíte e-mailovou adresu, kterou jste zadali, a upozorní vás, že migrace je dokončená, nebo jestli je potřeba provést nějakou akci. Tato část popisuje některé běžné problémy a jejich řešení.

### <a name="validation-failed"></a>Neúspěšné ověření

Vzhledem k nějakým nedávným změnám pravidel výstrah v rámci vašeho předplatného není možné migrovat předplatné. Tento problém je dočasný. Po přesunutí stavu **migrace do několika dnů můžete migraci restartovat** znovu.

### <a name="scope-lock-preventing-us-from-migrating-your-rules"></a>Zámek oboru brání migraci vašich pravidel.

V rámci migrace se vytvoří nové výstrahy metriky a nové skupiny akcí a pak se odstraní klasická pravidla výstrah. Zámek oboru ale může zabránit vytváření a odstraňování prostředků. V závislosti na zámku oboru nelze některá nebo všechna pravidla migrovat. Tento problém můžete vyřešit tak, že odeberete zámek oboru pro předplatné, skupinu prostředků nebo prostředek, který je uvedený v nástroji pro [migraci](https://portal.azure.com/#blade/Microsoft_Azure_Monitoring/MigrationBladeViewModel), a znovu spustíte migraci. Zámek oboru nelze zakázat a je třeba jej odebrat po dobu trvání procesu migrace. [Přečtěte si další informace o správě zámků oboru](../../azure-resource-manager/management/lock-resources.md#portal).

### <a name="policy-with-deny-effect-preventing-us-from-migrating-your-rules"></a>Zásady s efektem odepření nám brání v migraci vašich pravidel.

V rámci migrace se vytvoří nové výstrahy metriky a nové skupiny akcí a pak se odstraní klasická pravidla výstrah. Přiřazení [Azure Policy](../../governance/policy/index.yml) ale může zabránit v vytváření prostředků. V závislosti na přiřazení zásady se některá nebo všechna pravidla nedala migrovat. Přiřazení zásad blokující proces jsou uvedena v [nástroji pro migraci](https://portal.azure.com/#blade/Microsoft_Azure_Monitoring/MigrationBladeViewModel). Vyřešte tento problém jedním z těchto problémů:

- Vyloučením předplatných, skupin prostředků nebo jednotlivých prostředků po dobu trvání procesu migrace z přiřazení zásad. [Přečtěte si další informace o správě oborů vyloučení zásad](../../governance/policy/tutorials/create-and-manage.md#remove-a-non-compliant-or-denied-resource-from-the-scope-with-an-exclusion).
- Nastavte režim vynucení na **zakázáno** u přiřazení zásady. [Přečtěte si další informace o vlastnosti enforcementMode přiřazení zásad](../../governance/policy/concepts/assignment-structure.md#enforcement-mode).
- Nastavte výjimku Azure Policy (Preview) na předplatná, skupiny prostředků nebo jednotlivé prostředky k přiřazení zásady. [Přečtěte si další informace o Azure Policy struktuře výjimek](../../governance/policy/concepts/exemption-structure.md).
- Odebrání nebo změna efektu na disabled, audit, Append nebo Modify (což může například řešit problémy týkající se chybějících značek). [Přečtěte si další informace o správě efektů zásad](../../governance/policy/concepts/definition-structure.md#policy-rule).

## <a name="next-steps"></a>Další kroky

- [Způsoby použití nástroje pro migraci](alerts-using-migration-tool.md)
- [Příprava na migraci](alerts-prepare-migration.md)
