---
title: Parametry serveru – škálovatelné (Citus) – Azure Database for PostgreSQL
description: Parametry v rozhraní Citus API pro škálování na více systému ()
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: reference
ms.date: 08/10/2020
ms.openlocfilehash: 57258540f3cd7b4c47b662b0885453cedd188e5b
ms.sourcegitcommit: 1aef4235aec3fd326ded18df7fdb750883809ae8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/12/2020
ms.locfileid: "88136768"
---
# <a name="server-parameters"></a>Parametry serveru

Existují různé parametry serveru, které mají vliv na chování Citus (), obou parametrů ze standardních PostgreSQL a parametrů specifických pro (Citus). Další informace o parametrech konfigurace PostgreSQL najdete v části věnované [konfiguraci](http://www.postgresql.org/docs/current/static/runtime-config.html) běhu v dokumentaci PostgreSQL.

Zbytek tohoto odkazu se zaměřuje na diskuzi o specifických parametrech konfigurace (Citus). Tyto parametry se dají nastavit v Azure Portal v části **parametry pracovního uzlu** v části **Nastavení** pro skupinu serverů Citus.

> [!NOTE]
>
> Skupiny serverů s technologií Citus se staršími verzemi modulu nemusí nabízet všechny níže uvedené parametry.

## <a name="general-configuration"></a>Obecná konfigurace

### <a name="citususe_secondary_nodes-enum"></a>citus. use \_ sekundárních \_ uzlů (Enum)

Nastaví zásadu, která se má použít při výběru uzlů pro VÝBĚRové dotazy. Pokud je nastavená na Always, odešle Plánovač dotaz pouze uzly, které jsou označené jako ' Secondary ' noderole v [pg_dist_node](reference-hyperscale-metadata.md#worker-node-table).

Podporované hodnoty pro tento výčet jsou:

-   **nikdy:** (výchozí) všechna čtení se vyskytují na primárních uzlech.
-   **vždy:** Čtení se spouští na sekundárních uzlech a příkazy INSERT/Update jsou zakázané.

### <a name="cituscluster_name-text"></a>citus. cluster \_ název (text)

Informuje o plánovači uzlu koordinátora, který cluster IT koordinuje. Jakmile \_ je název clusteru nastavený, Plánovač se bude dotazovat uzly pracovních procesů v samotném clusteru.

### <a name="citusenable_version_checks-boolean"></a>citus. Enable \_ – \_ kontroly verzí (Boolean)

Upgrade verze Citus (rescale) vyžaduje restart serveru (pro výběr nové sdílené knihovny) následovaný příkazem ALTER EXTENSION UPDATE.  V průběhu provádění obou kroků by mohlo dojít k chybám nebo selháním.
Citus (škálování na více instancí) ověří verzi kódu a příponu shody a chyby, pokud ne \' .

Tato hodnota je ve výchozím nastavení true a v koordinátoru platí. Ve výjimečných případech můžou složité procesy upgradu vyžadovat nastavení tohoto parametru na false, což zakáže kontrolu.

### <a name="cituslog_distributed_deadlock_detection-boolean"></a>\_detekce distribuovaného \_ zablokování citus. log \_ (Boolean)

Určuje, zda se má v protokolu serveru protokolovat zpracování související s detekcí distribuovaného zablokování. Výchozí hodnota je false.

### <a name="citusdistributed_deadlock_detection_factor-floating-point"></a>citus. distribuovaný \_ faktor zablokování \_ \_ (plovoucí desetinná čárka)

Nastaví dobu čekání před kontrolou distribuovaných zablokování. Vynásobený časovým \' [ \_ limitem zablokování](https://www.postgresql.org/docs/current/static/runtime-config-locks.html) PostgreSQL s se vyhodnotí zejména doba čekání na vyřízení. Výchozí hodnota je `2`. Hodnota `-1` zakáže zjišťování distribuovaného zablokování.

### <a name="citusnode_connection_timeout-integer"></a>citus. \_ časový limit připojení k uzlu \_ (celé číslo)

`citus.node_connection_timeout`GUC nastaví maximální dobu trvání (v milisekundách) na čekání na vytvoření připojení. Citus () vyvolá chybu, pokud časový limit uplyne před navázáním alespoň jednoho připojení pracovního procesu. Tento GUC má vliv na připojení od koordinátora na pracovníky a na sebe navzájem.

-   Výchozí: pět sekund
-   Minimálně: 10 milisekund
-   Maximum: jedna hodina

```postgresql
-- set to 30 seconds
ALTER DATABASE foo
SET citus.node_connection_timeout = 30000;
```

## <a name="query-statistics"></a>Statistiky dotazů

### <a name="citusstat_statements_purge_interval-integer"></a>citus. stat \_ – \_ interval mazání příkazů \_ (celé číslo)

Nastaví četnost, s jakou proces démona údržby odebere záznamy z [citus_stat_statements](reference-hyperscale-metadata.md#query-statistics-table) , které se neshodují v `pg_stat_statements` . Tato hodnota konfigurace nastavuje časový interval mezi vyprázdněním v sekundách a výchozí hodnotou 10. Hodnota 0 zakáže vyprázdnění.

```psql
SET citus.stat_statements_purge_interval TO 5;
```

Tento parametr platí pro koordinátora a lze ho změnit za běhu.

## <a name="data-loading"></a>Načítání dat

### <a name="citusmulti_shard_commit_protocol-enum"></a>citus. Multi \_ horizontálních oddílů \_ Commit \_ Protocol (Enum)

Nastaví protokol potvrzení, který se má použít při provádění kopírování na distribuované tabulce hash. V každém jednotlivém umístění horizontálních oddílů se kopie provádí v bloku transakce, aby se zajistilo, že se žádná data ingestují v případě, že během kopírování dojde k chybě. Existuje však konkrétní případ selhání, ve kterém je kopie úspěšná na všech místech, ale dojde k selhání (hardwaru) před potvrzením všech transakcí. Tento parametr lze použít k tomu, aby se zabránilo ztrátě dat v takovém případě volbou mezi následujícími protokoly potvrzení:

-   **2PC:** (výchozí) transakce, ve kterých se provádí kopírování na horizontálních oddílů místech, se nejprve připravují pomocí \' [dvoufázové potvrzovací](http://www.postgresql.org/docs/current/static/sql-prepare-transaction.html) operace PostgreSQL s a pak potvrzeny. Neúspěšná potvrzení se dají ručně obnovit nebo zrušit pomocí připraveného potvrzení změn nebo PŘIPRAVENého vrácení zpět (v uvedeném pořadí).
    Při použití 2PC by se mělo zvýšit [maximální počet \_ připravených \_ transakcí](http://www.postgresql.org/docs/current/static/runtime-config-resource.html) na všechny pracovní procesy, obvykle na stejnou hodnotu jako maximální počet \_ připojení.
-   **1pc:** Transakce, ve kterých se provádí kopírování na horizontálních oddílů místech, se potvrdí v jednom kole. Může dojít ke ztrátě dat v případě, že potvrzení selže po úspěšném dokončení kopírování na všech místech (vzácná).

### <a name="citusshard_replication_factor-integer"></a>citus. horizontálních oddílů \_ – \_ faktor replikace (celé číslo)

Nastaví faktor replikace pro horizontálních oddílů, tj. počet uzlů, na které se horizontálních oddílů umístí, a výchozí hodnota je 1. Tento parametr lze nastavit v době běhu a je platný pro koordinátora. Ideální hodnota pro tento parametr závisí na velikosti clusteru a rychlosti selhání uzlu.  Tento faktor replikace můžete například chtít zvýšit, pokud spouštíte velké clustery a v častých intervalech Sledujte selhání uzlu.

### <a name="citusshard_count-integer"></a>citus. horizontálních oddílů – \_ počet (celé číslo)

Nastaví počet horizontálních oddílů tabulek dělených algoritmem hash a výchozí hodnota je 32.  Tuto hodnotu používá [create_distributed_table](reference-hyperscale-functions.md#create_distributed_table) UDF při vytváření tabulek dělených algoritmem hash. Tento parametr lze nastavit v době běhu a je platný pro koordinátora.

### <a name="citusshard_max_size-integer"></a>maximální velikost citus. horizontálních oddílů \_ \_ (celé číslo)

Nastaví maximální velikost, na kterou bude horizontálních oddílů růst, než se rozdělí a výchozí hodnota je 1 GB. Když velikost zdrojového souboru \' (která se používá pro přípravu) pro jeden horizontálních oddílů překročí tuto hodnotu konfigurace, databáze zajistí, že se vytvoří nový horizontálních oddílů. Tento parametr lze nastavit v době běhu a je platný pro koordinátora.

## <a name="planner-configuration"></a>Konfigurace plánovače

### <a name="cituslimit_clause_row_fetch_count-integer"></a>\_ \_ počet načtených řádků klauzule citus. limit \_ \_ (Integer)

Nastaví počet řádků, které mají být načteny na úkol pro optimalizaci klauzule limit.
V některých případech je možné, že výběr dotazů s klauzulemi limit může vyžadovat načtení všech řádků z každého úkolu za účelem generování výsledků. V těchto případech a v případě, že by aproximace přinesla smysluplné výsledky, tato hodnota konfigurace nastaví počet řádků, které mají být načteny z jednotlivých horizontálních oddílů. Aproximace limitu jsou ve výchozím nastavení zakázané a tento parametr je nastavený na hodnotu-1. Tuto hodnotu lze nastavit v době běhu a platí pro koordinátora.

### <a name="cituscount_distinct_error_rate-floating-point"></a>\_frekvence chyb jedinečného citus. Count \_ \_ (plovoucí desetinná čárka)

Citus) může vypočítat počet (jedinečných) přibližných hodnot pomocí rozšíření PostgreSQL-HLL. Tato položka konfigurace nastavuje požadovanou četnost chyb při výpočtu počtu (jedinečné položky). 0,0, což je výchozí nastavení, zakáže přibližné počty (jedinečné); a 1,0 neposkytuje žádné záruky týkající se přesnosti výsledků. Pro dosažení nejlepších výsledků doporučujeme nastavit tento parametr na 0,005. Tuto hodnotu lze nastavit v době běhu a platí pro koordinátora.

### <a name="citustask_assignment_policy-enum"></a>citus. Task \_ Assignment \_ Policy (Enum)

> [!NOTE]
> Tento GUC je použitelný pouze v případě, že je [shard_replication_factor](reference-hyperscale-parameters.md#citusshard_replication_factor-integer) větší než jeden nebo pro dotazy na [reference_tables](concepts-hyperscale-distributed-data.md#type-2-reference-tables).

Nastaví zásadu, která se má použít při přiřazování úloh pracovníkům. Koordinátor přiřadí úlohy pracovníkům na základě umístění horizontálních oddílů. Tato hodnota konfigurace určuje zásadu, která se použije při provádění těchto přiřazení.
V současné době existují tři možné zásady přiřazování úloh, které je možné použít.

-   **hladec:** Zásada hladce je ve výchozím nastavení a zaměřuje se na rovnoměrné rozdělení úkolů mezi pracovními procesy.
-   **kruhové dotazování:** Zásady kruhového dotazování přiřazují úlohy zaměstnancům v kruhovém dotazování mezi různými replikami. Tato zásada umožňuje lepší využití clusteru, pokud je počet horizontálních oddílů v tabulce v porovnání s počtem pracovních procesů malý.
-   **první replika:** Zásady prvního repliky přiřadí úlohy na základě pořadí vkládání míst (repliky) pro horizontálních oddílů. Jinými slovy, dotaz na fragment pro horizontálních oddílů je přiřazen pracovnímu procesu, který má první repliku tohoto horizontálních oddílů.
    Tato metoda vám umožní mít silné záruky týkající se toho, které horizontálních oddílů se budou používat na kterých uzlech (to znamená silnější záruky rezidentů paměti).

Tento parametr lze nastavit v době běhu a je platný pro koordinátora.

## <a name="intermediate-data-transfer"></a>Zprostředkující Přenos dat

### <a name="citusbinary_worker_copy_format-boolean"></a>Formát kopie citus. Binary \_ Worker \_ copy \_ (Boolean)

Pro přenos mezilehlých dat mezi pracovními procesy použijte formát binárního kopírování.
Při spojování velkých tabulek může být Citus dynamicky měnit oddíly a náhodně přerozdělit data mezi různé pracovní procesy. Ve výchozím nastavení se tato data přenáší v textovém formátu. Povolení tohoto parametru dá databázi k přenosu těchto dat pomocí binárního formátu serializace PostgreSQL. Tento parametr je u pracovních procesů platný a musí se změnit v souboru PostgreSQL. conf. Po úpravě konfiguračního souboru můžou uživatelé odeslat signál SIGHUP nebo restartovat server, aby se tato změna projevila.

### <a name="citusbinary_master_copy_format-boolean"></a>citus. Binary \_ – \_ Formát hlavní kopie \_ (Boolean)

Pro přenos dat mezi koordinátorem a pracovními procesy použijte formát binárního kopírování. Při spouštění distribuovaných dotazů pracovníci převádějí své mezilehlé výsledky do koordinátora pro konečnou agregaci. Ve výchozím nastavení se tato data přenáší v textovém formátu. Povolení tohoto parametru dá databázi k přenosu těchto dat pomocí binárního formátu serializace PostgreSQL.
Tento parametr lze nastavit za běhu a je platný pro koordinátora.

### <a name="citusmax_intermediate_result_size-integer"></a>citus. Max \_ – \_ Velikost mezilehlého výsledku \_ (celé číslo)

Maximální velikost v KILOBAJTech mezilehlých výsledků pro CTEs, které nelze vložit do pracovních uzlů pro provedení, a pro složité poddotazy. Výchozí hodnota je 1 GB a hodnota-1 znamená omezení.
Dotazy překračující limit se zruší a vytvoří se chybová zpráva.

## <a name="ddl"></a>SKRIPT

### <a name="citusenable_ddl_propagation-boolean"></a>citus. Enable \_ – \_ rozšíření DDL (logická hodnota)

Určuje, jestli se mají automaticky šířit změny skriptu DDL od koordinátora na všechny pracovní procesy. Výchozí hodnotou je hodnota true. Vzhledem k tomu, že některé změny schématu vyžadují přístup pro výhradní zámek k tabulkám a vzhledem k tomu, že se automatická propagace vztahuje na všechny pracovní procesy sekvenčně, může cluster Citus () dočasně reagovat rychleji. Toto nastavení můžete zakázat a ručně šířit změny.

## <a name="executor-configuration"></a>Konfigurace prováděcího modulu

### <a name="general"></a>Obecné

#### <a name="citusall_modifications_commutative"></a>citus. všechny \_ změny \_ komutativní

Citus () vynutila pravidla komutativity a získá vhodné zámky pro operace úpravy, aby bylo zaručeno správné chování. Například předpokládá, že příkaz INSERT dokončí jiný příkaz INSERT, ale nikoli pomocí příkazu UPDATE nebo DELETE. Podobně předpokládá, že příkaz UPDATE nebo DELETE nepracuje s jiným příkazem UPDATE nebo DELETE. Tato preventivní opatření znamená, že aktualizace a odstranění vyžadují Citus k získání silnějších zámků.

Pokud máte příkazy aktualizace, které jsou komutativní s vašimi vloženími nebo jinými aktualizacemi, můžete tyto komutativity předpoklady zmírnit nastavením tohoto parametru na hodnotu true. Pokud je tento parametr nastavený na hodnotu true, všechny příkazy se považují za komutativní a vyžádají sdílený zámek, což může zlepšit celkovou propustnost. Tento parametr lze nastavit za běhu a je platný pro koordinátora.

#### <a name="citusremote_task_check_interval-integer"></a>citus. Remote \_ – \_ interval kontroly úloh \_ (celé číslo)

Nastaví četnost, s jakou Citus kontroluje stav úloh spravovaných prováděcím modulem pro sledování úloh. Výchozí hodnota je 10 ms. Koordinátor přiřadí úlohy pracovníkům a pak je pravidelně kontroluje o všech \' krocích probíhajících úloh. Tato hodnota konfigurace nastavuje časový interval mezi dvěma následnými kontrolami. Tento parametr platí pro koordinátora a dá se nastavit za běhu.

#### <a name="citustask_executor_type-enum"></a>citus. Task – \_ typ prováděcího modulu \_ (Enum)

Citus má tři typy prováděcích modulů pro spouštění distribuovaných dotazů SELECT.  Požadovaný prováděcí modul můžete vybrat nastavením tohoto parametru konfigurace. Přijaté hodnoty pro tento parametr jsou:

-   **adaptivní:** Výchozí hodnota. Je ideální pro rychlé odpovědi na dotazy, které zahrnují agregace a společně umístěné spojení napříč několika horizontálních oddílů.
-   **sledování úloh:** Prováděcí modul pro úlohy je vhodný pro dlouhé běžící a komplexní dotazy, které vyžadují překonání dat napříč pracovními uzly a efektivní správu prostředků.
-   **v reálném čase:** (zastaralé) slouží jako adaptivní vykonavatel, což je podobný účel, ale je méně flexibilní a může způsobit větší tlak na připojení na pracovních uzlech.

Tento parametr lze nastavit v době běhu a je platný pro koordinátora.

#### <a name="citusmulti_task_query_log_level-enum-multi_task_logging"></a>citus. Multi- \_ Task – \_ \_ úroveň protokolu dotazu \_ (Enum) {#multi_task_logging}

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

#### <a name="citusenable_repartition_joins-boolean"></a>citus. povolit \_ přerozdělení do oddílů \_ (logická hodnota)

Obvykle se při pokusu o provedení spojení s adaptivním vykonavatelem nezdaří s chybovou zprávou.  Nastavením `citus.enable_repartition_joins` na hodnotu true (pravda) však umožníte, aby se v Citus mohli dočasně přepnout do prováděcího modulu pro sledování úloh, aby bylo možné spojení provést.  Výchozí hodnota je False.

### <a name="task-tracker-executor-configuration"></a>Konfigurace prováděcího modulu pro sledování úloh

#### <a name="citustask_tracker_delay-integer"></a>citus. \_ Delay pro sledování úloh \_ (celé číslo)

Tento parametr nastaví dobu spánku v režimu spánku mezi správou úloh a s výchozím nastavením 200 ms. Proces sledování úloh se probudí pravidelně, projde všechny úkoly, které jsou mu přiřazeny, a naplánuje a spustí tyto úlohy.  Pak se sledování úkolů po dobu, kdy se tyto úlohy znovu přenese, do režimu spánku.
Tato hodnota konfigurace určuje délku období v režimu spánku. Tento parametr je u pracovních procesů platný a musí se změnit v souboru PostgreSQL. conf. Po úpravě konfiguračního souboru můžou uživatelé odeslat signál SIGHUP nebo restartovat server, aby se změna projevila.

Tento parametr může být zmenšen, aby se zkrátila prodleva způsobená následkem prováděcího modulu pro sledování úloh tím, že se zkrátí časová mezera mezi úrovněmi správy.
Snížení zpoždění je užitečné v případech, kdy jsou dotazy horizontálních oddílů krátké, takže jejich stav pravidelně aktualizujeme.

#### <a name="citusmax_assign_task_batch_size-integer"></a>velikost dávky citus. max pro \_ přiřazení \_ úlohy \_ \_ (celé číslo)

Prováděcí modul pro sledování úloh v koordinátoru synchronně přiřadí úkoly v dávkách k procesu démona na pracovních procesech. Tento parametr nastaví maximální počet úkolů, které se mají přiřadit v rámci jedné dávky. Výběr větší velikosti dávky umožňuje rychlejší přiřazování úkolů. Pokud je ale velký počet pracovních procesů, může trvat déle, než budou moct všichni pracovníci získat úkoly.  Tento parametr lze nastavit za běhu a je platný pro koordinátora.

#### <a name="citusmax_running_tasks_per_node-integer"></a>počet \_ spuštěných \_ úloh na uzel citus. Max \_ \_ (Integer)

Úkol sledování úkolů provede plány a podle potřeby provede úkoly, které jsou mu přiřazeny. Tato hodnota konfigurace nastavuje maximální počet úloh, které mají být spuštěny souběžně na jednom uzlu, přičemž výchozí hodnota je 8.

Limit zajišťuje, že nebudete \' mít k dispozici mnoho úloh současně, a pomůže vám se vyhnout se vstupně-výstupnímu obsahu disku. Pokud jsou dotazy obsluhovány z paměti nebo SSD, můžete zvýšit maximální počet \_ spuštěných \_ úloh \_ na \_ uzel, aniž by to bylo mnohem důležité.

#### <a name="cituspartition_buffer_size-integer"></a>\_Velikost vyrovnávací paměti citus. partition \_ (celé číslo)

Nastaví velikost vyrovnávací paměti, která se má použít pro operace oddílu a výchozí hodnotu 8 MB.
Citus () umožňuje změnit rozdělení dat tabulky do více souborů, když se spojí dvě velké tabulky. Po vyplňování této vyrovnávací paměti oddílu se přerozdělená data zaprázdní do souborů na disku.  Tato položka konfigurace se dá nastavit v době běhu a platí pro pracovní procesy.

### <a name="explain-output"></a>Vysvětlit výstup

#### <a name="citusexplain_all_tasks-boolean"></a>citus. Vysvětlete \_ všechny \_ úkoly (logická hodnota)

Ve výchozím nastavení zobrazuje Citus () výstup jedné, libovolné úlohy při použití příkazu [vysvětlit](http://www.postgresql.org/docs/current/static/sql-explain.html) u distribuovaného dotazu. Ve většině případů bude vysvětlený výstup vypadat v různých úlohách. Občas se některé úlohy plánují jinak nebo mají mnohem vyšší dobu spuštění. V těchto případech může být užitečné povolit tento parametr, po kterém bude vysvětlený výstup obsahovat všechny úlohy. Vysvětlení všech úkolů může vysvětlit delší dobu.

## <a name="next-steps"></a>Další kroky

* Jinou formou konfigurace, kromě parametrů serveru, jsou [Možnosti konfigurace](concepts-hyperscale-configuration-options.md) prostředků ve skupině serverů Citus ().
* Základní data PostgreSQL Base mají také [parametry konfigurace](http://www.postgresql.org/docs/current/static/runtime-config.html).
