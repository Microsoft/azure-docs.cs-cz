---
title: Vysvětlení migrace pro výstrahy Azure Monitor
description: Seznamte se s tím, jak funguje migrace výstrah a řešení problémů.
ms.topic: conceptual
ms.date: 02/14/2021
ms.author: yalavi
author: yalavi
ms.openlocfilehash: 88b75c46898e1a74b33051d8996af96201f639c7
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "102037706"
---
# <a name="understand-migration-options-to-newer-alerts"></a>Vysvětlení možností migrace s novějšími výstrahami

Klasické výstrahy jsou [vyřazené](./monitoring-classic-retirement.md) pro uživatele veřejného cloudu, i když jsou omezeného využití do **31. května 2021**. Klasické výstrahy pro Azure Government Cloud a Azure Čína 21Vianet vybudou od **29. února 2024**.

Tento článek vysvětluje, jak funguje ruční migrace a dobrovolný migrační nástroj, který se použije k migraci zbývajících pravidel upozornění. Popisuje také řešení některých běžných problémů.

> [!IMPORTANT]
> Migrace neovlivní výstrahy protokolu aktivit (včetně upozornění na stav služby) a výstrahy protokolu. Migrace se vztahuje pouze na pravidla pro klasické výstrahy, která jsou popsaná [zde](./monitoring-classic-retirement.md#retirement-of-classic-monitoring-and-alerting-platform).

> [!NOTE]
> Pokud jsou pravidla vašich klasických výstrah neplatná, tj. jsou v [zastaralých metrikách](#classic-alert-rules-on-deprecated-metrics) nebo prostředky, které byly odstraněny, nebudou migrovány a nebudou k dispozici po vyřazení služby.

## <a name="manually-migrating-classic-alerts-to-newer-alerts"></a>Ruční migrace klasických výstrah na novější výstrahy

Zákazníci, kteří mají zájem o ruční migraci zbývajících výstrah, už můžou dělat pomocí následujících oddílů. Obsahuje také metriky, které jsou vyřazeny a nelze je migrovat přímo.

### <a name="guest-metrics-on-virtual-machines"></a>Metriky hostů na virtuálních počítačích

Než budete moct vytvořit nové výstrahy metriky pro metriky hostů, musí se metriky hosta odeslat do úložiště protokolů Azure Monitor. Při vytváření výstrah postupujte podle těchto pokynů:

- [Povoluje se shromažďování metrik hosta do Log Analytics.](../agents/agent-data-sources.md)
- [Vytváření výstrah protokolu v Azure Monitor](./alerts-log.md)

K dispozici je více možností, jak shromažďovat metriky hostů a upozorňovat na ně, další [informace](../agents/agents-overview.md).

### <a name="storage-and-classic-storage-account-metrics"></a>Metriky účtu úložiště a klasického účtu úložiště

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
- Maximální počet spotřebovaných RUPM za minutu
- Maximální počet ru za sekundu
- Mongo další poplatek za požadavek
- Mongo další rychlost žádosti
- Pozorovaná latence čtení
- Pozorovaná latence zápisu
- Dostupnost služby
- Kapacita úložiště

Průměrný počet požadavků za sekundu, úroveň konzistence, maximální RUPM spotřebovaných za minutu, Max ru za sekundu, zjištěnou latenci čtení, pozorovanou latenci zápisu a kapacita úložiště nejsou v současnosti v [novém systému](../essentials/metrics-supported.md#microsoftdocumentdbdatabaseaccounts)k dispozici.

Výstrahy na metrikách žádostí, jako je HTTP 2xx, http 3xx a dostupnost služby, se nemigrují, protože způsob, jakým se počítají žádosti, se liší od klasických metrik a nových metrik. Výstrahy na těchto metrikách bude nutné ručně znovu vytvořit se upravenými mezními hodnotami.

### <a name="classic-alert-rules-on-deprecated-metrics"></a>Pravidla pro klasické výstrahy na zastaralých metrikách

Níže jsou pravidla pro upozornění klasická pro metriky, které byly dříve podporovány, ale byly nakonec zastaralé. Malé procento zákazníků může mít pro takové metriky neplatná pravidla klasických výstrah. Vzhledem k tomu, že tato pravidla upozornění nejsou platná, nebudou migrována.

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

- **Frekvence**: definuje, jak často pravidlo klasického nebo nového upozornění kontroluje podmínku. Nastavení `frequency` v klasických pravidlech výstrahy nebylo uživatelem konfigurovatelné a bylo vždy 5 minut pro všechny typy prostředků. Frekvence ekvivalentních pravidel je také nastavena na 5 minut.
- **Typ agregace**: definuje, jak je metrika agregována nad oknem zájmu. `aggregationType`Je také totéž mezi klasickými výstrahami a novými výstrahami pro většinu metrik. V některých případech, protože se metrika liší mezi výstrahami Classic a novými výstrahami, `aggregationType` `primary Aggregation Type` je použita ekvivalentní nebo definovaná pro metriku.
- **Units**: vlastnost metriky, na které je vytvořena výstraha. Některé ekvivalentní metriky mají různé jednotky. Prahová hodnota se podle potřeby upraví. Například pokud má původní metrika sekundy jako jednotky, ale ekvivalentní nová metrika má milisekundy jako jednotky, původní prahová hodnota se vynásobí 1000, aby se zajistilo stejné chování.
- **Velikost okna**: definuje okno, přes které se agreguje data metriky pro porovnání s prahovou hodnotou. U standardních `windowSize` hodnot, jako je 5 minut, 15 minut, 30 minut, 1 hodina, 3 hodiny, 6 hodin, 12 hodin, 1 den, se u ekvivalentního nového pravidla výstrahy nezměnila žádná změna. Pro jiné hodnoty se používá nejpodobnější `windowSize` . U většiny zákazníků se tato změna nijak neprojeví. Pro malé procento zákazníků může být potřeba upravit prahovou hodnotu a získat tak přesně stejné chování.

V následujících částech jsme podrobně nastavili metriky, které mají jinou ekvivalentní metriku v novém systému. Žádná metrika, která zůstává shodná s pravidly pro klasický a nová upozornění, není uvedená. Seznam metrik podporovaných v novém systému najdete [tady](../essentials/metrics-supported.md).

### <a name="microsoftstoragestorageaccounts-and-microsoftclassicstoragestorageaccounts"></a>Microsoft. Storage/storageAccounts a Microsoft. ClassicStorage/storageAccounts

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

### <a name="microsoftdocumentdbdatabaseaccounts"></a>Microsoft.DocumentDB/databaseAccounts

Pro Cosmos DB se používají ekvivalentní metriky, jak je znázorněno níže:

| Metrika v klasických výstrahách | Ekvivalentní metrika v nových výstrahách | Komentáře|
|--------------------------|---------------------------------|---------|
| AvailableStorage | AvailableStorage||
| Velikost dat | Využití datausage| |
| Počet dokumentů | DocumentCount||
| Velikost indexu | IndexUsage||
| Služba není k dispozici | ServiceAvailability||
| TotalRequestUnits | TotalRequestUnits||
| Omezené žádosti | TotalRequests s dimenzí "StatusCode" = "429"| Typ agregace Average se opravil na Count.|
| Vnitřní chyby serveru | TotalRequests s dimenzí "StatusCode" = "500"}| Typ agregace Average se opravil na Count.|
| HTTP 401 | TotalRequests s dimenzí "StatusCode" = "401"| Typ agregace Average se opravil na Count.|
| HTTP 400 | TotalRequests s dimenzí "StatusCode" = "400"| Typ agregace Average se opravil na Count.|
| Požadavky celkem | TotalRequests| Typ agregace Max se opravil na Count.|
| Poplatek za požadavek na počet Mongo| MongoRequestCharge s dimenzí "Command." = "Count"||
| Frekvence požadavků na počet Mongo | MongoRequestsCount s dimenzí "Command." = "Count"||
| Poplatek za žádost o odstranění Mongo | MongoRequestCharge s dimenzí "Command." = "Delete"||
| Frekvence žádosti o odstranění Mongo | MongoRequestsCount s dimenzí "Command." = "Delete"||
| Poplatek za požadavek na vložení Mongo | MongoRequestCharge s dimenzí "Command." = "Insert"||
| Frekvence požadavků na vložení Mongo | MongoRequestsCount s dimenzí "Command." = "Insert"||
| Poplatek za požadavek na dotaz na Mongo | MongoRequestCharge s dimenzí "Command." = "Find"||
| Frekvence požadavků na dotaz Mongo | MongoRequestsCount s dimenzí "Command." = "Find"||
| Poplatek za žádost o aktualizaci Mongo | MongoRequestCharge s dimenzí "Command." = "Update"||
| Mongo vložit neúspěšné žádosti | MongoRequestCount s rozměry "Commanda" = "Insert" a "status" = "Failed"| Typ agregace Average se opravil na Count.|
| Neúspěšné požadavky dotazu Mongo | MongoRequestCount s rozměry "Command-" = "Query" and "status" = "Failed"| Typ agregace Average se opravil na Count.|
| Neúspěšné požadavky počtu Mongo | MongoRequestCount s rozměry "Command-" = "Count" a "status" = "Failed"| Typ agregace Average se opravil na Count.|
| Neúspěšné žádosti o aktualizaci Mongo | MongoRequestCount s rozměry "Commanda" = "Update" a "status" = "Failed"| Typ agregace Average se opravil na Count.|
| Mongo jiných neúspěšných žádostí | MongoRequestCount s rozměry "Command." = "other" a "status" = "Failed"| Typ agregace Average se opravil na Count.|
| Mongo odstranit neúspěšné žádosti | MongoRequestCount s rozměry "Commanda" = "Delete" a "status" = "Failed"| Typ agregace Average se opravil na Count.|

### <a name="how-equivalent-action-groups-are-created"></a>Způsob vytvoření ekvivalentních skupin akcí

Klasická pravidla výstrah obsahovala akce e-mailu, Webhooku, aplikace logiky a sady Runbook vázané na samotné pravidlo výstrahy. Nová pravidla upozornění používají skupiny akcí, které je možné znovu použít napříč více pravidly upozornění. Nástroj pro migraci vytvoří jednu skupinu akcí pro stejné akce bez ohledu na to, kolik pravidel výstrah používá tuto akci. Skupiny akcí vytvořené nástrojem pro migraci používají formát pojmenování "Migrated_AG *".

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

Kvůli nějakým nedávným změnám v klasických pravidlech výstrah v rámci předplatného není možné migrovat předplatné. Tento problém je dočasný. Po přesunutí stavu **migrace do několika dnů můžete migraci restartovat** znovu.

### <a name="scope-lock-preventing-us-from-migrating-your-rules"></a>Zámek oboru brání migraci vašich pravidel.

V rámci migrace se vytvoří nové výstrahy metriky a nové skupiny akcí a pak se odstraní klasická pravidla výstrah. Zámek oboru ale může zabránit vytváření a odstraňování prostředků. V závislosti na uzamčení oboru se některá nebo všechna pravidla nedala migrovat. Tento problém můžete vyřešit tak, že odeberete zámek oboru pro předplatné, skupinu prostředků nebo prostředek, který je uvedený v nástroji pro [migraci](https://portal.azure.com/#blade/Microsoft_Azure_Monitoring/MigrationBladeViewModel), a znovu spustíte migraci. Zámek oboru nelze zakázat a je třeba jej odebrat během procesu migrace. [Přečtěte si další informace o správě zámků oboru](../../azure-resource-manager/management/lock-resources.md#portal).

### <a name="policy-with-deny-effect-preventing-us-from-migrating-your-rules"></a>Zásady s efektem odepření nám brání v migraci vašich pravidel.

V rámci migrace se vytvoří nové výstrahy metriky a nové skupiny akcí a pak se odstraní klasická pravidla výstrah. Přiřazení [Azure Policy](../../governance/policy/index.yml) ale může zabránit v vytváření prostředků. V závislosti na přiřazení zásady se nepovedlo migrovat některá nebo všechna pravidla. Přiřazení zásad blokující proces jsou uvedena v [nástroji pro migraci](https://portal.azure.com/#blade/Microsoft_Azure_Monitoring/MigrationBladeViewModel). Vyřešte tento problém jedním z těchto problémů:

- Vyloučením předplatných, skupin prostředků nebo jednotlivých prostředků během procesu migrace z přiřazení zásad. [Přečtěte si další informace o správě oborů vyloučení zásad](../../governance/policy/tutorials/create-and-manage.md#remove-a-non-compliant-or-denied-resource-from-the-scope-with-an-exclusion).
- Nastavte režim vynucení na **zakázáno** u přiřazení zásady. [Přečtěte si další informace o vlastnosti enforcementMode přiřazení zásad](../../governance/policy/concepts/assignment-structure.md#enforcement-mode).
- Nastavte výjimku Azure Policy (Preview) u předplatných, skupin prostředků nebo jednotlivých prostředků na přiřazení zásady. [Přečtěte si další informace o Azure Policy struktuře výjimek](../../governance/policy/concepts/exemption-structure.md).
- Odebrání nebo změna efektu na disabled, audit, Append nebo Modify (což může například řešit problémy týkající se chybějících značek). [Přečtěte si další informace o správě efektů zásad](../../governance/policy/concepts/definition-structure.md#policy-rule).

## <a name="next-steps"></a>Další kroky

- [Způsoby použití nástroje pro migraci](alerts-using-migration-tool.md)
- [Příprava na migraci](alerts-prepare-migration.md)