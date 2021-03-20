---
title: Parametry serveru – Citus (Velká měřítko) – Azure Database for PostgreSQL
description: Parametry v rozhraní Citus API pro škálování na více systému ()
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: reference
ms.date: 08/10/2020
ms.openlocfilehash: 07f966c7b0be542f848f1a0a4eaf2b5549735b4b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "91336237"
---
# <a name="server-parameters"></a>Parametry serveru

Existují různé parametry serveru, které mají vliv na chování Citus (PostgreSQL), od úrovně Standard a specifické pro (Citus).
Tyto parametry se dají nastavit v Azure Portal pro skupinu serverů Citus (škálovatelný). V kategorii **Nastavení** vyberte parametry **uzlu pracovního procesu** nebo **parametry uzlu koordinátora**. Tyto stránky umožňují nastavit parametry pro všechny pracovní uzly nebo pouze pro uzel koordinátora.

## <a name="hyperscale-citus-parameters"></a>Parametry Citus (s měřítkem)

> [!NOTE]
>
> Skupiny serverů Citus (), na kterých běží starší verze modulu Citus, nemusí nabízet všechny níže uvedené parametry.

### <a name="general-configuration"></a>Obecná konfigurace

#### <a name="citususe_secondary_nodes-enum"></a>citus. use \_ sekundárních \_ uzlů (Enum)

Nastaví zásadu, která se má použít při výběru uzlů pro VÝBĚRové dotazy. Pokud je nastavená na Always, odešle Plánovač dotaz pouze uzly, které jsou označené jako ' Secondary ' noderole v [pg_dist_node](reference-hyperscale-metadata.md#worker-node-table).

Podporované hodnoty pro tento výčet jsou:

-   **nikdy:** (výchozí) všechna čtení se vyskytují na primárních uzlech.
-   **vždy:** Čtení se spouští na sekundárních uzlech a příkazy INSERT/Update jsou zakázané.

#### <a name="cituscluster_name-text"></a>citus. cluster \_ název (text)

Informuje o plánovači uzlu koordinátora, který cluster IT koordinuje. Jakmile \_ je název clusteru nastavený, Plánovač se bude dotazovat uzly pracovních procesů v samotném clusteru.

#### <a name="citusenable_version_checks-boolean"></a>citus. Enable \_ – \_ kontroly verzí (Boolean)

Upgrade verze Citus (rescale) vyžaduje restart serveru (pro výběr nové sdílené knihovny) následovaný příkazem ALTER EXTENSION UPDATE.  V průběhu provádění obou kroků by mohlo dojít k chybám nebo selháním.
Citus (škálování na více instancí) ověří verzi kódu a příponu shody a chyby, pokud ne \' .

Tato hodnota je ve výchozím nastavení true a v koordinátoru platí. Ve výjimečných případech můžou složité procesy upgradu vyžadovat nastavení tohoto parametru na false, což zakáže kontrolu.

#### <a name="cituslog_distributed_deadlock_detection-boolean"></a>\_detekce distribuovaného \_ zablokování citus. log \_ (Boolean)

Určuje, zda se má v protokolu serveru protokolovat zpracování související s detekcí distribuovaného zablokování. Výchozí hodnota je false.

#### <a name="citusdistributed_deadlock_detection_factor-floating-point"></a>citus. distribuovaný \_ faktor zablokování \_ \_ (plovoucí desetinná čárka)

Nastaví dobu čekání před kontrolou distribuovaných zablokování. Vynásobený časovým \' [ \_ limitem zablokování](https://www.postgresql.org/docs/current/static/runtime-config-locks.html) PostgreSQL s se vyhodnotí zejména doba čekání na vyřízení. Výchozí hodnota je `2`. Hodnota `-1` zakáže zjišťování distribuovaného zablokování.

#### <a name="citusnode_connection_timeout-integer"></a>citus. \_ časový limit připojení k uzlu \_ (celé číslo)

`citus.node_connection_timeout`GUC nastaví maximální dobu trvání (v milisekundách) na čekání na vytvoření připojení. Citus () vyvolá chybu, pokud časový limit uplyne před navázáním alespoň jednoho připojení pracovního procesu. Tento GUC má vliv na připojení od koordinátora na pracovníky a na sebe navzájem.

-   Výchozí: pět sekund
-   Minimálně: 10 milisekund
-   Maximum: jedna hodina

```postgresql
-- set to 30 seconds
ALTER DATABASE foo
SET citus.node_connection_timeout = 30000;
```

### <a name="query-statistics"></a>Statistiky dotazů

#### <a name="citusstat_statements_purge_interval-integer"></a>citus. stat \_ – \_ interval mazání příkazů \_ (celé číslo)

Nastaví četnost, s jakou proces démona údržby odebere záznamy z [citus_stat_statements](reference-hyperscale-metadata.md#query-statistics-table) , které se neshodují v `pg_stat_statements` . Tato hodnota konfigurace nastavuje časový interval mezi vyprázdněním v sekundách a výchozí hodnotou 10. Hodnota 0 zakáže vyprázdnění.

```psql
SET citus.stat_statements_purge_interval TO 5;
```

Tento parametr platí pro koordinátora a lze ho změnit za běhu.

### <a name="data-loading"></a>Načítání dat

#### <a name="citusmulti_shard_commit_protocol-enum"></a>citus. Multi \_ horizontálních oddílů \_ Commit \_ Protocol (Enum)

Nastaví protokol potvrzení, který se má použít při provádění kopírování na distribuované tabulce hash. V každém jednotlivém umístění horizontálních oddílů se kopie provádí v bloku transakce, aby se zajistilo, že se žádná data ingestují v případě, že během kopírování dojde k chybě. Existuje však konkrétní případ selhání, ve kterém je kopie úspěšná na všech místech, ale dojde k selhání (hardwaru) před potvrzením všech transakcí. Tento parametr lze použít k tomu, aby se zabránilo ztrátě dat v takovém případě volbou mezi následujícími protokoly potvrzení:

-   **2PC:** (výchozí) transakce, ve kterých se provádí kopírování na horizontálních oddílů místech, se nejprve připravují pomocí \' [dvoufázové potvrzovací](http://www.postgresql.org/docs/current/static/sql-prepare-transaction.html) operace PostgreSQL s a pak potvrzeny. Neúspěšná potvrzení se dají ručně obnovit nebo zrušit pomocí připraveného potvrzení změn nebo PŘIPRAVENého vrácení zpět (v uvedeném pořadí).
    Při použití 2PC by se mělo zvýšit [maximální počet \_ připravených \_ transakcí](http://www.postgresql.org/docs/current/static/runtime-config-resource.html) na všechny pracovní procesy, obvykle na stejnou hodnotu jako maximální počet \_ připojení.
-   **1pc:** Transakce, ve kterých se provádí kopírování na horizontálních oddílů místech, se potvrdí v jednom kole. Může dojít ke ztrátě dat v případě, že potvrzení selže po úspěšném dokončení kopírování na všech místech (vzácná).

#### <a name="citusshard_replication_factor-integer"></a>citus. horizontálních oddílů \_ – \_ faktor replikace (celé číslo)

Nastaví faktor replikace pro horizontálních oddílů, tj. počet uzlů, na které se horizontálních oddílů umístí, a výchozí hodnota je 1. Tento parametr lze nastavit v době běhu a je platný pro koordinátora. Ideální hodnota pro tento parametr závisí na velikosti clusteru a rychlosti selhání uzlu.  Tento faktor replikace můžete například chtít zvýšit, pokud spouštíte velké clustery a v častých intervalech Sledujte selhání uzlu.

#### <a name="citusshard_count-integer"></a>citus. horizontálních oddílů – \_ počet (celé číslo)

Nastaví počet horizontálních oddílů tabulek dělených algoritmem hash a výchozí hodnota je 32.  Tuto hodnotu používá [create_distributed_table](reference-hyperscale-functions.md#create_distributed_table) UDF při vytváření tabulek dělených algoritmem hash. Tento parametr lze nastavit v době běhu a je platný pro koordinátora.

#### <a name="citusshard_max_size-integer"></a>maximální velikost citus. horizontálních oddílů \_ \_ (celé číslo)

Nastaví maximální velikost, na kterou bude horizontálních oddílů růst, než se rozdělí a výchozí hodnota je 1 GB. Když velikost zdrojového souboru \' (která se používá pro přípravu) pro jeden horizontálních oddílů překročí tuto hodnotu konfigurace, databáze zajistí, že se vytvoří nový horizontálních oddílů. Tento parametr lze nastavit v době běhu a je platný pro koordinátora.

### <a name="planner-configuration"></a>Konfigurace plánovače

#### <a name="cituslimit_clause_row_fetch_count-integer"></a>\_ \_ počet načtených řádků klauzule citus. limit \_ \_ (Integer)

Nastaví počet řádků, které mají být načteny na úkol pro optimalizaci klauzule limit.
V některých případech je možné, že výběr dotazů s klauzulemi limit může vyžadovat načtení všech řádků z každého úkolu za účelem generování výsledků. V těchto případech a v případě, že by aproximace přinesla smysluplné výsledky, tato hodnota konfigurace nastaví počet řádků, které mají být načteny z jednotlivých horizontálních oddílů. Aproximace limitu jsou ve výchozím nastavení zakázané a tento parametr je nastavený na hodnotu-1. Tuto hodnotu lze nastavit v době běhu a platí pro koordinátora.

#### <a name="cituscount_distinct_error_rate-floating-point"></a>\_frekvence chyb jedinečného citus. Count \_ \_ (plovoucí desetinná čárka)

Citus) může vypočítat počet (jedinečných) přibližných hodnot pomocí rozšíření PostgreSQL-HLL. Tato položka konfigurace nastavuje požadovanou četnost chyb při výpočtu počtu (jedinečné položky). 0,0, což je výchozí nastavení, zakáže přibližné počty (jedinečné); a 1,0 neposkytuje žádné záruky týkající se přesnosti výsledků. Pro dosažení nejlepších výsledků doporučujeme nastavit tento parametr na 0,005. Tuto hodnotu lze nastavit v době běhu a platí pro koordinátora.

#### <a name="citustask_assignment_policy-enum"></a>citus. Task \_ Assignment \_ Policy (Enum)

> [!NOTE]
> Tento GUC je použitelný pouze v případě, že je [shard_replication_factor](reference-hyperscale-parameters.md#citusshard_replication_factor-integer) větší než jeden nebo pro dotazy na [reference_tables](concepts-hyperscale-distributed-data.md#type-2-reference-tables).

Nastaví zásadu, která se má použít při přiřazování úloh pracovníkům. Koordinátor přiřadí úlohy pracovníkům na základě umístění horizontálních oddílů. Tato hodnota konfigurace určuje zásadu, která se použije při provádění těchto přiřazení.
V současné době existují tři možné zásady přiřazování úloh, které je možné použít.

-   **hladec:** Zásada hladce je ve výchozím nastavení a zaměřuje se na rovnoměrné rozdělení úkolů mezi pracovními procesy.
-   **kruhové dotazování:** Zásady kruhového dotazování přiřazují úlohy zaměstnancům v kruhovém dotazování mezi různými replikami. Tato zásada umožňuje lepší využití clusteru, pokud je počet horizontálních oddílů v tabulce v porovnání s počtem pracovních procesů malý.
-   **první replika:** Zásady prvního repliky přiřadí úlohy na základě pořadí vkládání míst (repliky) pro horizontálních oddílů. Jinými slovy, dotaz na fragment pro horizontálních oddílů je přiřazen pracovnímu procesu, který má první repliku tohoto horizontálních oddílů.
    Tato metoda vám umožní mít silné záruky týkající se toho, které horizontálních oddílů se budou používat na kterých uzlech (to znamená silnější záruky rezidentů paměti).

Tento parametr lze nastavit v době běhu a je platný pro koordinátora.

### <a name="intermediate-data-transfer"></a>Zprostředkující Přenos dat

#### <a name="citusbinary_worker_copy_format-boolean"></a>Formát kopie citus. Binary \_ Worker \_ copy \_ (Boolean)

Pro přenos mezilehlých dat mezi pracovními procesy použijte formát binárního kopírování.
Při spojování velkých tabulek může být Citus dynamicky měnit oddíly a náhodně přerozdělit data mezi různé pracovní procesy. Ve výchozím nastavení se tato data přenáší v textovém formátu. Povolení tohoto parametru dá databázi k přenosu těchto dat pomocí binárního formátu serializace PostgreSQL. Tento parametr je u pracovních procesů platný a musí se změnit v souboru PostgreSQL. conf. Po úpravě konfiguračního souboru můžou uživatelé odeslat signál SIGHUP nebo restartovat server, aby se tato změna projevila.

#### <a name="citusbinary_master_copy_format-boolean"></a>citus. Binary \_ – \_ Formát hlavní kopie \_ (Boolean)

Pro přenos dat mezi koordinátorem a pracovními procesy použijte formát binárního kopírování. Při spouštění distribuovaných dotazů pracovníci převádějí své mezilehlé výsledky do koordinátora pro konečnou agregaci. Ve výchozím nastavení se tato data přenáší v textovém formátu. Povolení tohoto parametru dá databázi k přenosu těchto dat pomocí binárního formátu serializace PostgreSQL.
Tento parametr lze nastavit za běhu a je platný pro koordinátora.

#### <a name="citusmax_intermediate_result_size-integer"></a>citus. Max \_ – \_ Velikost mezilehlého výsledku \_ (celé číslo)

Maximální velikost v KILOBAJTech mezilehlých výsledků pro CTEs, které nelze vložit do pracovních uzlů pro provedení, a pro složité poddotazy. Výchozí hodnota je 1 GB a hodnota-1 znamená omezení.
Dotazy překračující limit se zruší a vytvoří se chybová zpráva.

### <a name="ddl"></a>SKRIPT

#### <a name="citusenable_ddl_propagation-boolean"></a>citus. Enable \_ – \_ rozšíření DDL (logická hodnota)

Určuje, jestli se mají automaticky šířit změny skriptu DDL od koordinátora na všechny pracovní procesy. Výchozí hodnotou je hodnota true. Vzhledem k tomu, že některé změny schématu vyžadují přístup pro výhradní zámek k tabulkám a vzhledem k tomu, že se automatická propagace vztahuje na všechny pracovní procesy sekvenčně, může cluster Citus () dočasně reagovat rychleji. Toto nastavení můžete zakázat a ručně šířit změny.

### <a name="executor-configuration"></a>Konfigurace prováděcího modulu

#### <a name="general"></a>Obecné

##### <a name="citusall_modifications_commutative"></a>citus. všechny \_ změny \_ komutativní

Citus () vynutila pravidla komutativity a získá vhodné zámky pro operace úpravy, aby bylo zaručeno správné chování. Například předpokládá, že příkaz INSERT dokončí jiný příkaz INSERT, ale nikoli pomocí příkazu UPDATE nebo DELETE. Podobně předpokládá, že příkaz UPDATE nebo DELETE nepracuje s jiným příkazem UPDATE nebo DELETE. Tato preventivní opatření znamená, že aktualizace a odstranění vyžadují Citus k získání silnějších zámků.

Pokud máte příkazy aktualizace, které jsou komutativní s vašimi vloženími nebo jinými aktualizacemi, můžete tyto komutativity předpoklady zmírnit nastavením tohoto parametru na hodnotu true. Pokud je tento parametr nastavený na hodnotu true, všechny příkazy se považují za komutativní a vyžádají sdílený zámek, což může zlepšit celkovou propustnost. Tento parametr lze nastavit za běhu a je platný pro koordinátora.

##### <a name="citusremote_task_check_interval-integer"></a>citus. Remote \_ – \_ interval kontroly úloh \_ (celé číslo)

Nastaví četnost, s jakou Citus kontroluje stav úloh spravovaných prováděcím modulem pro sledování úloh. Výchozí hodnota je 10 ms. Koordinátor přiřadí úlohy pracovníkům a pak je pravidelně kontroluje o všech \' krocích probíhajících úloh. Tato hodnota konfigurace nastavuje časový interval mezi dvěma následnými kontrolami. Tento parametr platí pro koordinátora a dá se nastavit za běhu.

##### <a name="citustask_executor_type-enum"></a>citus. Task – \_ typ prováděcího modulu \_ (Enum)

Citus má tři typy prováděcích modulů pro spouštění distribuovaných dotazů SELECT.  Požadovaný prováděcí modul můžete vybrat nastavením tohoto parametru konfigurace. Přijaté hodnoty pro tento parametr jsou:

-   **adaptivní:** Výchozí hodnota. Je ideální pro rychlé odpovědi na dotazy, které zahrnují agregace a společně umístěné spojení napříč několika horizontálních oddílů.
-   **sledování úloh:** Prováděcí modul pro úlohy je vhodný pro dlouhé běžící a komplexní dotazy, které vyžadují překonání dat napříč pracovními uzly a efektivní správu prostředků.
-   **v reálném čase:** (zastaralé) slouží jako adaptivní vykonavatel, což je podobný účel, ale je méně flexibilní a může způsobit větší tlak na připojení na pracovních uzlech.

Tento parametr lze nastavit v době běhu a je platný pro koordinátora.

##### <a name="citusmulti_task_query_log_level-enum-multi_task_logging"></a>citus. Multi- \_ Task – \_ \_ úroveň protokolu dotazu \_ (Enum) {#multi_task_logging}

Nastaví úroveň protokolu pro jakýkoli dotaz, který generuje více než jeden úkol (to znamená, že se bude jednat o více než jeden horizontálních oddílů). Protokolování je užitečné při migraci více tenantů, protože se můžete rozhodnout pro tyto dotazy nebo je zobrazit, abyste je našli a přidali do \_ nich filtr ID tenanta. Tento parametr lze nastavit za běhu a je platný pro koordinátora. Výchozí hodnota pro tento parametr je \' vypnuta \' .

Podporované hodnoty pro tento výčet jsou:

-   **vypnuto:** Vypnutí protokolování všech dotazů, které generují více úloh (to znamená více horizontálních oddílů)
-   **ladění:** Příkaz logs na úrovni závažnosti ladění.
-   **protokol:** Příkaz logs na úrovni závažnosti protokolu. Řádek protokolu obsahuje dotaz SQL, který se spustil.
-   **Upozornění:** Příkaz logs na úrovni závažnosti oznámení.
-   **Upozornění:** Příkaz logs na úrovni závažnosti upozornění.
-   **Chyba:** Příkaz logs na úrovni závažnosti chyby.

Může být užitečné použít `error` při testování vývoje a nižší úroveň protokolování jako `log` při skutečném nasazení v produkčním prostředí.
Volba způsobí, že se `log` dotazy na více úlohám zobrazí v protokolech databáze s dotazem, který se zobrazí po \" příkazu.\"

```
LOG:  multi-task query about to be executed
HINT:  Queries are split to multiple tasks if they have to be split into several queries on the workers.
STATEMENT:  select * from foo;
```

##### <a name="citusenable_repartition_joins-boolean"></a>citus. povolit \_ přerozdělení do oddílů \_ (logická hodnota)

Obvykle se při pokusu o provedení spojení s adaptivním vykonavatelem nezdaří s chybovou zprávou.  Nastavením `citus.enable_repartition_joins` na hodnotu true (pravda) však umožníte, aby se v Citus mohli dočasně přepnout do prováděcího modulu pro sledování úloh, aby bylo možné spojení provést.  Výchozí hodnota je False.

#### <a name="task-tracker-executor-configuration"></a>Konfigurace prováděcího modulu pro sledování úloh

##### <a name="citustask_tracker_delay-integer"></a>citus. \_ Delay pro sledování úloh \_ (celé číslo)

Tento parametr nastaví dobu spánku v režimu spánku mezi správou úloh a s výchozím nastavením 200 ms. Proces sledování úloh se probudí pravidelně, projde všechny úkoly, které jsou mu přiřazeny, a naplánuje a spustí tyto úlohy.  Pak se sledování úkolů po dobu, kdy se tyto úlohy znovu přenese, do režimu spánku.
Tato hodnota konfigurace určuje délku období v režimu spánku. Tento parametr je u pracovních procesů platný a musí se změnit v souboru PostgreSQL. conf. Po úpravě konfiguračního souboru můžou uživatelé odeslat signál SIGHUP nebo restartovat server, aby se změna projevila.

Tento parametr může být zmenšen, aby se zkrátila prodleva způsobená následkem prováděcího modulu pro sledování úloh tím, že se zkrátí časová mezera mezi úrovněmi správy.
Snížení zpoždění je užitečné v případech, kdy jsou dotazy horizontálních oddílů krátké, takže jejich stav pravidelně aktualizujeme.

##### <a name="citusmax_assign_task_batch_size-integer"></a>velikost dávky citus. max pro \_ přiřazení \_ úlohy \_ \_ (celé číslo)

Prováděcí modul pro sledování úloh v koordinátoru synchronně přiřadí úkoly v dávkách k procesu démona na pracovních procesech. Tento parametr nastaví maximální počet úkolů, které se mají přiřadit v rámci jedné dávky. Výběr větší velikosti dávky umožňuje rychlejší přiřazování úkolů. Pokud je ale velký počet pracovních procesů, může trvat déle, než budou moct všichni pracovníci získat úkoly.  Tento parametr lze nastavit za běhu a je platný pro koordinátora.

##### <a name="citusmax_running_tasks_per_node-integer"></a>počet \_ spuštěných \_ úloh na uzel citus. Max \_ \_ (Integer)

Úkol sledování úkolů provede plány a podle potřeby provede úkoly, které jsou mu přiřazeny. Tato hodnota konfigurace nastavuje maximální počet úloh, které mají být spuštěny souběžně na jednom uzlu, přičemž výchozí hodnota je 8.

Limit zajišťuje, že nebudete \' mít k dispozici mnoho úloh současně, a pomůže vám se vyhnout se vstupně-výstupnímu obsahu disku. Pokud jsou dotazy obsluhovány z paměti nebo SSD, můžete zvýšit maximální počet \_ spuštěných \_ úloh \_ na \_ uzel, aniž by to bylo mnohem důležité.

##### <a name="cituspartition_buffer_size-integer"></a>\_Velikost vyrovnávací paměti citus. partition \_ (celé číslo)

Nastaví velikost vyrovnávací paměti, která se má použít pro operace oddílu a výchozí hodnotu 8 MB.
Citus () umožňuje změnit rozdělení dat tabulky do více souborů, když se spojí dvě velké tabulky. Po vyplňování této vyrovnávací paměti oddílu se přerozdělená data zaprázdní do souborů na disku.  Tato položka konfigurace se dá nastavit v době běhu a platí pro pracovní procesy.

#### <a name="explain-output"></a>Vysvětlit výstup

##### <a name="citusexplain_all_tasks-boolean"></a>citus. Vysvětlete \_ všechny \_ úkoly (logická hodnota)

Ve výchozím nastavení zobrazuje Citus () výstup jedné, libovolné úlohy při použití příkazu [vysvětlit](http://www.postgresql.org/docs/current/static/sql-explain.html) u distribuovaného dotazu. Ve většině případů bude vysvětlený výstup vypadat v různých úlohách. Občas se některé úlohy plánují jinak nebo mají mnohem vyšší dobu spuštění. V těchto případech může být užitečné povolit tento parametr, po kterém bude vysvětlený výstup obsahovat všechny úlohy. Vysvětlení všech úkolů může vysvětlit delší dobu.

## <a name="postgresql-parameters"></a>Parametry PostgreSQL

* [DateStyle](https://www.postgresql.org/docs/current/datatype-datetime.html#DATATYPE-DATETIME-OUTPUT) – nastaví formát zobrazení pro hodnoty data a času.
* [IntervalStyle](https://www.postgresql.org/docs/current/datatype-datetime.html#DATATYPE-INTERVAL-OUTPUT) – nastaví formát zobrazení pro hodnoty intervalu.
* [Timezone](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-TIMEZONE) – nastaví časové pásmo pro zobrazení a interpretaci časových razítek.
* [application_name](https://www.postgresql.org/docs/current/runtime-config-logging.html#GUC-APPLICATION-NAME) – nastaví název aplikace, který se má ohlásit v statistikách a protokolech.
* [array_nulls](https://www.postgresql.org/docs/current/runtime-config-compatible.html#GUC-ARRAY-NULLS) – POVOLÍ vstup prázdných prvků v polích.
* [autovaku](https://www.postgresql.org/docs/current/runtime-config-autovacuum.html#GUC-AUTOVACUUM) – spustí dílčí proces autovaku.
* [autovacuum_analyze_scale_factor](https://www.postgresql.org/docs/current/runtime-config-autovacuum.html#GUC-AUTOVACUUM-ANALYZE-SCALE-FACTOR) – počet vložení, aktualizace nebo odstranění řazené kolekce členů před analýzou jako zlomek reltuples
* [autovacuum_analyze_threshold](https://www.postgresql.org/docs/current/runtime-config-autovacuum.html#GUC-AUTOVACUUM-ANALYZE-THRESHOLD) – minimální počet vložení, aktualizace nebo odstranění řazené kolekce členů před analýzou
* [autovacuum_naptime](https://www.postgresql.org/docs/current/runtime-config-autovacuum.html#GUC-AUTOVACUUM-NAPTIME) – přejít do režimu spánku mezi autovakuová spuštění
* [autovacuum_vacuum_cost_delay](https://www.postgresql.org/docs/current/runtime-config-autovacuum.html#GUC-AUTOVACUUM-VACUUM-COST-DELAY) zpoždění nákladů na vaku v milisekundách pro autovaku
* [autovacuum_vacuum_cost_limit](https://www.postgresql.org/docs/current/runtime-config-autovacuum.html#GUC-AUTOVACUUM-VACUUM-COST-LIMIT) – množství nákladů na vaku k dispozici před Napping pro autovaku
* [autovacuum_vacuum_scale_factor](https://www.postgresql.org/docs/current/runtime-config-autovacuum.html#GUC-AUTOVACUUM-VACUUM-SCALE-FACTOR) – počet aktualizací řazené kolekce členů nebo jejich odstranění před vakuou jako zlomek reltuples
* [autovacuum_vacuum_threshold](https://www.postgresql.org/docs/current/runtime-config-autovacuum.html#GUC-AUTOVACUUM-VACUUM-THRESHOLD) – minimální počet aktualizací nebo odstranění řazené kolekce členů před vaku
* [autovacuum_work_mem](https://www.postgresql.org/docs/current/runtime-config-resource.html#GUC-AUTOVACUUM-WORK-MEM) – nastaví maximální velikost paměti, která se má použít pro každý pracovní proces autovaku.
* [backend_flush_after](https://www.postgresql.org/docs/current/runtime-config-resource.html#GUC-BACKEND-FLUSH-AFTER) – počet stránek, po jejichž uplynutí jsou vypsány dříve provedené zápisy na disk
* [backslash_quote](https://www.postgresql.org/docs/current/runtime-config-compatible.html#GUC-BACKSLASH-QUOTE) – nastaví, zda \' je v řetězcových literálech povoleno "".
* [bgwriter_delay](https://www.postgresql.org/docs/current/runtime-config-resource.html#GUC-BGWRITER-DELAY) -doba spánku zapisovače na pozadí mezi zaokrouhlením
* [bgwriter_flush_after](https://www.postgresql.org/docs/current/runtime-config-resource.html#GUC-BGWRITER-FLUSH-AFTER) – počet stránek, po jejichž uplynutí jsou vypsány dříve provedené zápisy na disk
* maximální počet LRU stránek, které se mají vyprázdnit na kolo, zapisovač na pozadí [bgwriter_lru_maxpages](https://www.postgresql.org/docs/current/runtime-config-resource.html#GUC-BGWRITER-LRU-MAXPAGES)
* [bgwriter_lru_multiplier](https://www.postgresql.org/docs/current/runtime-config-resource.html#GUC-BGWRITER-LRU-MULTIPLIER) – násobek průměrného využití vyrovnávací paměti, které se zadarmo zaokrouhlí na jednu stranu
* [bytea_output](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-BYTEA-OUTPUT) – nastaví výstupní formát pro Byte.
* [check_function_bodies](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-CHECK-FUNCTION-BODIES) – kontroluje texty funkcí během vytváření funkce.
* [checkpoint_completion_target](https://www.postgresql.org/docs/current/runtime-config-wal.html#GUC-CHECKPOINT-COMPLETION-TARGET) čas strávený vyprazdňováním nevyřízených vyrovnávacích pamětí během kontrolního bodu, jako zlomek intervalu kontrolního bodu
* [checkpoint_timeout](https://www.postgresql.org/docs/current/runtime-config-wal.html#GUC-CHECKPOINT-TIMEOUT) – nastaví maximální dobu mezi automatickými kontrolními body Wal.
* [checkpoint_warning](https://www.postgresql.org/docs/current/runtime-config-wal.html#GUC-CHECKPOINT-WARNING) – povolí upozornění, pokud jsou segmenty kontrolního bodu vyplněny častěji než
* [client_encoding](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-CLIENT-ENCODING) – nastaví kódování znakové sady klienta.
* [client_min_messages](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-CLIENT-MIN-MESSAGES) – nastaví úrovně zpráv, které se odesílají klientovi.
* [commit_delay](https://www.postgresql.org/docs/current/runtime-config-wal.html#GUC-COMMIT-DELAY) – nastaví zpoždění v mikrosekundách mezi potvrzením transakce a vyprázdněním Wal na disk.
* [commit_siblings](https://www.postgresql.org/docs/12/runtime-config-wal.html#GUC-COMMIT-SIBLINGS) – nastaví minimální souběžné otevřené transakce před provedením commit_delay
* [constraint_exclusion](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-CONSTRAINT-EXCLUSION) – umožňuje plánovači používat omezení pro optimalizaci dotazů.
* [cpu_index_tuple_cost](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-CPU-INDEX-TUPLE-COST) – nastaví odhad nákladů na zpracování jednotlivých položek indexu během prohledávání indexu.
* [cpu_operator_cost](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-CPU-OPERATOR-COST) – nastaví odhad nákladů na zpracování každého operátoru nebo volání funkce.
* [cpu_tuple_cost](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-CPU-TUPLE-COST) – nastaví odhad nákladů na zpracování každé řazené kolekce členů (řádek).
* [cursor_tuple_fraction](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-CURSOR-TUPLE-FRACTION) – nastaví odhad počtu zlomků řádků kurzoru, které budou načteny.
* [deadlock_timeout](https://www.postgresql.org/docs/current/runtime-config-locks.html#GUC-DEADLOCK-TIMEOUT) – nastaví množství času (v milisekundách), po který se má čekat na zámek před kontrolou zablokování.
* zobrazení stromové struktury pro analýzu a naplánování [debug_pretty_print](https://www.postgresql.org/docs/current/runtime-config-logging.html#id-1.6.6.11.5.2.3.1.3) -odsazení
* [debug_print_parse](https://www.postgresql.org/docs/current/runtime-config-logging.html#id-1.6.6.11.5.2.2.1.3) – protokoluje strom analýzy každého dotazu.
* [debug_print_plan](https://www.postgresql.org/docs/current/runtime-config-logging.html#id-1.6.6.11.5.2.2.1.3) – protokoluje plán provádění jednotlivých dotazů.
* [debug_print_rewritten](https://www.postgresql.org/docs/current/runtime-config-logging.html#id-1.6.6.11.5.2.2.1.3) – protokoluje přepsané stromové struktury každého dotazu.
* [default_statistics_target](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-DEFAULT-STATISTICS-TARGET) – nastaví výchozí cíl statistiky.
* [default_tablespace](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-DEFAULT-TABLESPACE) – nastaví výchozí tabulkový prostor pro vytváření tabulek a indexů v
* [default_text_search_config](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-DEFAULT-TEXT-SEARCH-CONFIG) – nastaví výchozí konfiguraci vyhledávání textu.
* [default_transaction_deferrable](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-DEFAULT-TRANSACTION-DEFERRABLE) – nastaví výchozí neodkladný stav nových transakcí.
* [default_transaction_isolation](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-DEFAULT-TRANSACTION-ISOLATION) – nastaví úroveň izolace transakce pro každou novou transakci.
* [default_transaction_read_only](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-DEFAULT-TRANSACTION-READ-ONLY) – nastaví výchozí stav pro nové transakce jen pro čtení.
* default_with_oids – ve výchozím nastavení vytvoří nové tabulky s identifikátory OID.
* [effective_cache_size](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-EFFECTIVE-CACHE-SIZE) – nastaví předpoklad plánovače o velikosti diskové mezipaměti.
* [enable_bitmapscan](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-ENABLE-BITMAPSCAN) – povolí použití plánů rastrového vyhledávání v plánovači.
* [enable_gathermerge](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-ENABLE-GATHERMERGE) – povolí použití plánů shromáždění sloučení pro Plánovač.
* [enable_hashagg](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-ENABLE-HASHAGG) – povolí použití agregačních plánů s algoritmem hash v plánovači.
* [enable_hashjoin](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-ENABLE-HASHJOIN) – povolí použití plánů připojení algoritmu hash v plánovači.
* [enable_indexonlyscan](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-ENABLE-INDEXONLYSCAN) – povolí použití plánů prohledávání pouze indexu v plánovači.
* [enable_indexscan](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-ENABLE-INDEXSCAN) – povolí použití plánů index-prověřování v plánovači.
* [enable_material](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-ENABLE-MATERIAL) – povolí použití materializace plánovače.
* [enable_mergejoin](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-ENABLE-MERGEJOIN) – povolí použití plánů spojení sloučení v plánovači.
* [enable_nestloop](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-ENABLE-NESTLOOP) – povolí použití plánů připojení vnořených smyček do plánovače.
* [enable_seqscan](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-ENABLE-SEQSCAN) – povolí použití plánů sekvenčního prohledávání plánovače.
* [enable_sort](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-ENABLE-SORT) – povolí použití explicitních kroků řazení plánovače.
* [enable_tidscan](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-ENABLE-TIDSCAN) – povolí použití plánů skenování TID v plánovači.
* [escape_string_warning](https://www.postgresql.org/docs/current/runtime-config-compatible.html#GUC-ESCAPE-STRING-WARNING) – upozorňuje na řídicí znaky zpětného lomítka v běžných řetězcových literálech
* [exit_on_error](https://www.postgresql.org/docs/current/runtime-config-error-handling.html#GUC-EXIT-ON-ERROR) – ukončí relaci při jakékoli chybě
* [extra_float_digits](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-EXTRA-FLOAT-DIGITS) – nastaví počet číslic zobrazených pro hodnoty s plovoucí desetinnou čárkou.
* [force_parallel_mode](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-FORCE-PARALLEL-MODE) – vynutí použití paralelních dotazových funkcí.
* [from_collapse_limit](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-FROM-COLLAPSE-LIMIT) – nastaví velikost ze seznamu, po jejímž uplynutí se poddotazy nebalí.
* [geqo](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-GEQO) – povoluje optimalizaci genetického dotazu.
* [geqo_effort](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-GEQO-EFFORT) -geqo: úsilí se používá k nastavení výchozí hodnoty pro jiné parametry geqo.
* [geqo_generations](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-GEQO-GENERATIONS) -geqo: počet iterací algoritmu
* [geqo_pool_size](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-GEQO-POOL-SIZE) -geqo: počet jednotlivců v populaci
* [geqo_seed](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-GEQO-SEED) -geqo: počáteční pro výběr náhodných cest
* [geqo_selection_bias](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-GEQO-SELECTION-BIAS) -geqo: selektivní tlak v rámci populace
* [geqo_threshold](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-GEQO-THRESHOLD) – nastaví prahovou hodnotu z položek, po kterých se používá geqo.
* [gin_fuzzy_search_limit](https://www.postgresql.org/docs/current/runtime-config-client.html#id-1.6.6.14.5.2.2.1.3) – nastaví maximální povolený výsledek pro přesné hledání podle gin
* [gin_pending_list_limit](https://www.postgresql.org/docs/current/runtime-config-client.html#id-1.6.6.14.2.2.23.1.3) – nastaví maximální velikost seznamu nevyřízených souborů pro gin index.
* [idle_in_transaction_session_timeout](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-IDLE-IN-TRANSACTION-SESSION-TIMEOUT) – nastaví maximální povolenou dobu trvání jakékoli transakce volnoběhu.
* [join_collapse_limit](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-JOIN-COLLAPSE-LIMIT) – nastaví velikost ze seznamu, nad kterou se nesloučí konstrukce spojení.
* [LC_MONETARY](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-LC-MONETARY) – nastaví národní prostředí pro formátování peněžních částek.
* [LC_NUMERIC](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-LC-NUMERIC) – nastaví národní prostředí pro formátování čísel.
* [lo_compat_privileges](https://www.postgresql.org/docs/current/runtime-config-compatible.html#GUC-LO-COMPAT-PRIVILEGES) – Povolí režim zpětné kompatibility pro kontroly oprávnění u velkých objektů.
* [LOCK_TIMEOUT](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-LOCK-TIMEOUT) – nastaví maximální povolenou dobu (v milisekundách) všech čekání na zámek. 0 vypne tuto hodnotu
* [log_autovacuum_min_duration](https://www.postgresql.org/docs/current/runtime-config-autovacuum.html#) – nastaví minimální dobu provádění, po jejímž překročení budou protokolovány akce autovaku.
* [log_checkpoints](https://www.postgresql.org/docs/current/runtime-config-logging.html#GUC-LOG-CHECKPOINTS) – protokoluje každý kontrolní bod.
* [log_connections](https://www.postgresql.org/docs/current/runtime-config-logging.html#GUC-LOG-CONNECTIONS) – protokoluje každé úspěšné připojení.
* [log_destination](https://www.postgresql.org/docs/current/runtime-config-logging.html#GUC-LOG-DESTINATION) – Nastaví cíl pro výstup protokolu serveru.
* [log_disconnections](https://www.postgresql.org/docs/current/runtime-config-logging.html#GUC-LOG-DISCONNECTIONS) – zaznamená konec relace, včetně doby trvání
* [log_duration](https://www.postgresql.org/docs/current/runtime-config-logging.html#GUC-LOG-DURATION) – zaznamená dobu trvání každého dokončeného příkazu SQL.
* [log_error_verbosity](https://www.postgresql.org/docs/current/runtime-config-logging.html#GUC-LOG-ERROR-VERBOSITY) – nastaví podrobnost protokolovaných zpráv.
* [log_lock_waits](https://www.postgresql.org/docs/current/runtime-config-logging.html#GUC-LOG-LOCK-WAITS) – protokoluje dlouhé zámky.
* [log_min_duration_statement](https://www.postgresql.org/docs/current/runtime-config-logging.html#GUC-LOG-MIN-DURATION-STATEMENT) – nastaví minimální dobu spuštění (v milisekundách), po které budou příkazy protokolovány. -1 zakáže dobu trvání příkazů protokolování.
* [log_min_error_statement](https://www.postgresql.org/docs/current/runtime-config-logging.html#GUC-LOG-MIN-ERROR-STATEMENT) – způsobí, že všechny příkazy generují chybu na nebo nad tuto úroveň, aby se zaznamenaly.
* [log_min_messages](https://www.postgresql.org/docs/current/runtime-config-logging.html#GUC-LOG-MIN-MESSAGES) – nastaví úrovně zpráv, které se protokolují.
* [log_replication_commands](https://www.postgresql.org/docs/current/runtime-config-logging.html#GUC-LOG-REPLICATION-COMMANDS) – protokoluje všechny příkazy replikace.
* [log_statement](https://www.postgresql.org/docs/current/runtime-config-logging.html#GUC-LOG-STATEMENT) – nastaví typ protokolovaných příkazů.
* [log_statement_stats](https://www.postgresql.org/docs/current/runtime-config-statistics.html#id-1.6.6.12.3.2.1.1.3) – pro každý dotaz zapíše kumulativní statistiku výkonu do protokolu serveru.
* [log_temp_files](https://www.postgresql.org/docs/current/runtime-config-logging.html#GUC-LOG-TEMP-FILES) – zaznamená používání dočasných souborů větších než tento počet kilobajtů.
* [maintenance_work_mem](https://www.postgresql.org/docs/current/runtime-config-resource.html#GUC-MAINTENANCE-WORK-MEM) – nastaví maximální velikost paměti, která se má použít pro operace údržby.
* [max_parallel_workers](https://www.postgresql.org/docs/current/runtime-config-resource.html#GUC-MAX-PARALLEL-WORKERS) – nastaví maximální počet paralelních pracovních procesů, než může být aktivní v jednom okamžiku.
* [max_parallel_workers_per_gather](https://www.postgresql.org/docs/current/runtime-config-resource.html#GUC-MAX-PARALLEL-WORKERS-PER-GATHER) – nastaví maximální počet paralelních procesů na uzel prováděcího modulu.
* [max_pred_locks_per_page](https://www.postgresql.org/docs/current/runtime-config-locks.html#GUC-MAX-PRED-LOCKS-PER-PAGE) – nastaví maximální počet objektů řazené kolekce členů s predikátem na stránku.
* [max_pred_locks_per_relation](https://www.postgresql.org/docs/current/runtime-config-locks.html#GUC-MAX-PRED-LOCKS-PER-RELATION) – nastaví maximální počet stránek uzamčených predikátů a řazené kolekce členů na vztah.
* [max_standby_archive_delay](https://www.postgresql.org/docs/current/runtime-config-replication.html#GUC-MAX-STANDBY-ARCHIVE-DELAY) – nastaví maximální zpoždění před zrušením dotazů, když aktivní pohotovostní pohotovostní server zpracovává Archivovaná data Wal.
* [max_standby_streaming_delay](https://www.postgresql.org/docs/current/runtime-config-replication.html#GUC-MAX-STANDBY-STREAMING-DELAY) – nastaví maximální zpoždění před zrušením dotazů, když aktivní pohotovostní pohotovostní server zpracovává streamovaná data Wal.
* [max_sync_workers_per_subscription](https://www.postgresql.org/docs/current/runtime-config-replication.html#GUC-MAX-SYNC-WORKERS-PER-SUBSCRIPTION) – maximální počet pracovních procesů synchronizace tabulek na předplatné
* [max_wal_size](https://www.postgresql.org/docs/current/runtime-config-wal.html#GUC-MAX-WAL-SIZE) – nastaví velikost Wal, která spouští kontrolní bod.
* [min_parallel_index_scan_size](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-MIN-PARALLEL-INDEX-SCAN-SIZE) – nastaví minimální množství dat indexu pro paralelní prohledávání.
* [min_wal_size](https://www.postgresql.org/docs/current/runtime-config-wal.html#GUC-MIN-WAL-SIZE) – nastaví minimální velikost pro zmenšení Wal na
* [operator_precedence_warning](https://www.postgresql.org/docs/current/runtime-config-compatible.html#GUC-OPERATOR-PRECEDENCE-WARNING) – vygeneruje upozornění pro konstrukce, které změnily význam od PostgreSQL 9,4
* [parallel_setup_cost](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-PARALLEL-SETUP-COST) – nastaví odhad nákladů na spuštění pracovních procesů pro paralelní dotaz na plánování.
* [parallel_tuple_cost](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-PARALLEL-TUPLE-COST) – nastaví odhad nákladů na plánování jednotlivých řazených kolekcí členů (řádků) z pracovního procesu do hlavního back-endu.
* [pg_stat_statements. Save](https://www.postgresql.org/docs/current/pgstatstatements.html#id-1.11.7.38.8) -uloží pg_stat_statements statistiku mezi vypínání serveru.
* [pg_stat_statements. Track](https://www.postgresql.org/docs/current/pgstatstatements.html#id-1.11.7.38.8) – výběr příkazů, které jsou sledovány pomocí pg_stat_statements
* [pg_stat_statements. track_utility](https://www.postgresql.org/docs/current/pgstatstatements.html#id-1.11.7.38.8) – vybere, zda jsou příkazy nástrojů sledovány pomocí pg_stat_statements
* [quote_all_identifiers](https://www.postgresql.org/docs/current/runtime-config-compatible.html#GUC-QUOTE-ALL-IDENTIFIERS) – při generování fragmentů SQL se v uvozovkách všechny identifikátory
* [random_page_cost](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-RANDOM-PAGE-COST) – nastaví odhad nákladů na stránku nesekvenčního načtení disku.
* [row_security](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-ROW-SECURITY) – povolí zabezpečení řádků.
* [search_path](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-SEARCH-PATH) – nastaví pořadí hledání schématu pro názvy, které nejsou kvalifikované pro schéma.
* [seq_page_cost](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-SEQ-PAGE-COST) – nastaví odhad nákladů na stránku s postupně načtenými disky.
* [session_replication_role](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-SESSION-REPLICATION-ROLE) – nastaví chování relace pro aktivační události a pravidla přepisu.
* [standard_conforming_strings](https://www.postgresql.org/docs/current/runtime-config-compatible.html#id-1.6.6.16.2.2.7.1.3) – příčiny... řetězce pro považovat zpětná lomítka
* [statement_timeout](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-STATEMENT-TIMEOUT) – nastaví maximální povolenou dobu (v milisekundách) libovolného příkazu. 0 vypne tuto hodnotu
* [synchronize_seqscans](https://www.postgresql.org/docs/current/runtime-config-compatible.html#id-1.6.6.16.2.2.8.1.3) – povolí synchronizované sekvenční kontroly.
* [synchronous_commit](https://www.postgresql.org/docs/current/runtime-config-wal.html#GUC-SYNCHRONOUS-COMMIT) – nastaví úroveň synchronizace aktuální transakce.
* [tcp_keepalives_count](https://www.postgresql.org/docs/current/runtime-config-connection.html#GUC-TCP-KEEPALIVES-COUNT) – maximální počet opakovaných přenosů v protokolu TCP
* [tcp_keepalives_idle](https://www.postgresql.org/docs/current/runtime-config-connection.html#GUC-TCP-KEEPALIVES-IDLE) – doba mezi vystavováním neudržením protokolu TCP
* doba [tcp_keepalives_interval](https://www.postgresql.org/docs/current/runtime-config-connection.html#GUC-TCP-KEEPALIVES-INTERVAL) mezi opakovanými ODESLÁNÍMI protokolu TCP
* [temp_buffers](https://www.postgresql.org/docs/current/runtime-config-resource.html#GUC-TEMP-BUFFERS) – nastaví maximální počet dočasných vyrovnávacích pamětí používaných jednotlivými relacemi databáze.
* [temp_tablespaces](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-TEMP-TABLESPACES) – nastaví prostor (y) pro použití pro dočasné tabulky a soubory řazení.
* [track_activities](https://www.postgresql.org/docs/current/runtime-config-statistics.html#GUC-TRACK-ACTIVITIES) – shromažďuje informace o spuštěných příkazech.
* [track_counts](https://www.postgresql.org/docs/current/runtime-config-statistics.html#GUC-TRACK-COUNTS) – shromáždí statistiku o aktivitě databáze.
* [track_functions](https://www.postgresql.org/docs/current/runtime-config-statistics.html#GUC-TRACK-FUNCTIONS) – shromažďuje statistiku na úrovni funkcí u aktivity databáze.
* [track_io_timing](https://www.postgresql.org/docs/current/runtime-config-statistics.html#GUC-TRACK-IO-TIMING) – shromažďuje statistiky časování pro aktivitu v/v databáze.
* [transform_null_equals](https://www.postgresql.org/docs/current/runtime-config-compatible.html#GUC-TRANSFORM-NULL-EQUALS) -zpracovává výraz "EXPR = null", protože "výraz má hodnotu null"
* [vacuum_cost_delay](https://www.postgresql.org/docs/current/runtime-config-resource.html#GUC-VACUUM-COST-DELAY) – zpoždění nákladů na vaku v milisekundách
* [vacuum_cost_limit](https://www.postgresql.org/docs/current/runtime-config-resource.html#GUC-VACUUM-COST-LIMIT) – množství nákladů na vaku k dispozici před Napping
* [vacuum_cost_page_dirty](https://www.postgresql.org/docs/current/runtime-config-resource.html#GUC-VACUUM-COST-PAGE-DIRTY) – náklady na změněných stránky po vaku
* náklady na [vacuum_cost_page_hit](https://www.postgresql.org/docs/current/runtime-config-resource.html#GUC-VACUUM-COST-PAGE-HIT) vaku pro stránku nalezené ve vyrovnávací paměti
* [vacuum_cost_page_miss](https://www.postgresql.org/docs/current/runtime-config-resource.html#GUC-VACUUM-COST-PAGE-MISS) – náklady na vaku pro stránku, která se nenašla v mezipaměti vyrovnávací paměti
* [vacuum_defer_cleanup_age](https://www.postgresql.org/docs/current/runtime-config-replication.html#GUC-VACUUM-DEFER-CLEANUP-AGE) -počet transakcí, kterými se má ODložit vakuová a horká vyčištění, pokud
* [vacuum_freeze_min_age](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-VACUUM-FREEZE-MIN-AGE) – minimální stáří, ve kterém by mělo být zmrazený řádek tabulky
* [vacuum_freeze_table_age](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-VACUUM-FREEZE-TABLE-AGE) – stáří, ve kterém by měl podtlak kontrolovat celou tabulku a zablokovat řazené kolekce členů
* [vacuum_multixact_freeze_min_age](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-VACUUM-MULTIXACT-FREEZE-MIN-AGE) – minimální stáří, ve kterém by měl být podtlak MultiXactId v řádku tabulky
* [vacuum_multixact_freeze_table_age](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-VACUUM-MULTIXACT-FREEZE-TABLE-AGE) – multixact stáří, ve kterém by měl podtlak kontrolovat celou tabulku a zablokovat řazené kolekce členů
* [wal_receiver_status_interval](https://www.postgresql.org/docs/current/runtime-config-replication.html#GUC-WAL-RECEIVER-STATUS-INTERVAL) – nastaví maximální interval mezi zprávami o stavu přijímače Wal na primární.
* [wal_writer_delay](https://www.postgresql.org/docs/current/runtime-config-wal.html#GUC-WAL-WRITER-DELAY) – doba mezi vyprázdněním Wal provedenými v ZAPISOVAČi Wal
* [wal_writer_flush_after](https://www.postgresql.org/docs/current/runtime-config-wal.html#GUC-WAL-WRITER-FLUSH-AFTER) – množství Wal zapsaného zapisovačem Wal, které spouští vyprázdnění
* [work_mem](https://www.postgresql.org/docs/current/runtime-config-resource.html#GUC-WORK-MEM) – nastaví velikost paměti, která se má použít při interní operaci řazení a zatřiďovací tabulky před zapsáním do dočasných souborů disku.
* [xmlbinary](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-XMLBINARY) – nastaví způsob kódování binárních hodnot v XML.
* [xmloption](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-XMLOPTION) – nastaví, jestli se data XML v implicitní analýze a operace serializace považují za dokumenty nebo fragmenty obsahu.

## <a name="next-steps"></a>Další kroky

* Jinou formou konfigurace, kromě parametrů serveru, jsou [Možnosti konfigurace](concepts-hyperscale-configuration-options.md) prostředků ve skupině serverů Citus ().
* Základní data PostgreSQL Base mají také [parametry konfigurace](http://www.postgresql.org/docs/current/static/runtime-config.html).
