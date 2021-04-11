---
title: 'Oracle to Azure Database for PostgreSQL: Průvodce migrací'
titleSuffix: Azure Database for PostgreSQL
description: V této příručce se naučíte migrovat schéma Oracle na Azure Database for PostgreSQL.
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.subservice: migration-guide
ms.topic: how-to
ms.date: 03/18/2021
ms.openlocfilehash: 931528ec415cabde8e862db17b9f8f26502f6788
ms.sourcegitcommit: f5448fe5b24c67e24aea769e1ab438a465dfe037
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "105968930"
---
# <a name="migrate-oracle-to-azure-database-for-postgresql"></a>Migrace Oracle na Azure Database for PostgreSQL

V této příručce se naučíte migrovat schéma Oracle na Azure Database for PostgreSQL. 

Podrobné a komplexní pokyny k migraci najdete v [příručce k migraci prostředků](https://github.com/microsoft/OrcasNinjaTeam/blob/master/Oracle%20to%20PostgreSQL%20Migration%20Guide/Oracle%20to%20Azure%20Database%20for%20PostgreSQL%20Migration%20Guide.pdf). 

## <a name="prerequisites"></a>Požadavky

Chcete-li migrovat schéma Oracle na Azure Database for PostgreSQL, je nutné provést následující kroky: 

- Ověřte, že je podporované vaše zdrojové prostředí. 
- Stáhněte si nejnovější verzi [ora2pg](https://ora2pg.darold.net/). 
- Mít nejnovější verzi [modulu DBD](https://www.cpan.org/modules/by-module/DBD/). 


## <a name="overview"></a>Přehled

PostgreSQL je jednou z nejpokročilejších open source databází na světě. Tento článek popisuje, jak pomocí bezplatného nástroje ora2pg migrovat databázi Oracle na PostgreSQL. Ora2pg můžete použít k migraci databáze Oracle nebo databáze MySQL do schématu kompatibilního s PostgreSQL. 

Nástroj ora2pg propojuje vaši databázi Oracle, automaticky ji vyhledává a extrahuje její strukturu nebo data. Pak ora2pg vygeneruje skripty SQL, které se dají načíst do vaší databáze PostgreSQL. Ora2pg můžete použít pro úlohy, jako je například zpětná analýza databáze Oracle, migrace obrovských podnikových databází nebo pouhá replikace některých dat Oracle do databáze PostgreSQL. Nástroj je snadno použitelný a nevyžaduje žádné znalosti databáze Oracle kromě možnosti poskytnout parametry potřebné pro připojení k databázi Oracle.

> [!NOTE]
> Další informace o používání nejnovější verze ora2pg najdete v [dokumentaci k ora2pg](https://ora2pg.darold.net/documentation.html).

### <a name="typical-ora2pg-migration-architecture"></a>Typická architektura migrace ora2pg

![Snímek obrazovky architektury migrace ora2pg](media/howto-migrate-from-oracle/ora2pg-migration-architecture.png)

Po zřízení virtuálního počítače a Azure Database for PostgreSQL budete potřebovat dvě konfigurace, aby bylo možné mezi nimi povolit připojení: **Povolit přístup ke službám Azure** a **vyhovět připojení SSL**: 

- Okno **zabezpečení připojení** > **Povolení přístupu ke službám Azure**  >  **na**

- Okno **zabezpečení připojení** > **Nastavení SSL**  >  **vynutilo**  >  **zakázané** připojení SSL

### <a name="recommendations"></a>Doporučení

- Chcete-li zlepšit výkon operací posouzení nebo exportu na serveru Oracle, shromážděte statistiku:

   ```
   BEGIN
   
     DBMS_STATS.GATHER_SCHEMA_STATS
     DBMS_STATS.GATHER_DATABASE_STATS
     DBMS_STATS.GATHER_DICTIONARY_STATS
     END;
   ```

- Exportujte data pomocí `COPY` příkazu místo `INSERT` .

- Vyhněte se exportu tabulek s jejich cizími klíči (FKs), omezeními a indexy. Tyto prvky zpomalují proces importu dat do PostgreSQL.

- Vytvářejte materializovaná zobrazení pomocí *klauzule No data*. Potom aktualizujte zobrazení později.

- Pokud je to možné, používejte jedinečné indexy v materializovaná zobrazení. Tyto indexy můžou při použití syntaxe zrychlit aktualizaci `REFRESH MATERIALIZED VIEW CONCURRENTLY` .


## <a name="premigration"></a>Předmigrace 

Až ověříte, že je vaše zdrojové prostředí podporované a že jste vyřešili nějaké požadavky, jste připraveni zahájit fázi předmigrace. Začněte následovně: 

1. Inventarizace databází, které je třeba migrovat. 
1. Vyhodnotit tyto databáze pro potenciální problémy s migrací nebo blokování.
1. Vyřešte všechny položky, které jste nekryli. 
 
Pro heterogenní migrace, jako je například Oracle pro Azure Database for PostgreSQL, zahrnuje tato fáze také vytváření schémat zdrojové databáze, která jsou kompatibilní s cílovým prostředím.

### <a name="discover"></a>Zjišťování

Cílem fáze zjišťování je identifikovat existující zdroje dat a podrobnosti o používaných funkcích. Tato fáze vám pomůže lépe pochopit a naplánovat migraci. Tento proces zahrnuje kontrolu sítě a identifikaci všech instancí Oracle vaší organizace spolu s použitou verzí a funkcemi.

Skripty Microsoft předhodnocení pro Oracle se spouštějí proti databázi Oracle. Skripty předhodnocení dotazují metadata Oracle. Skripty poskytují:

- Inventář databáze, včetně počtu objektů podle schématu, typu a stavu.
- Hrubý odhad nezpracovaných dat v jednotlivých schématech na základě statistik.
- Velikost tabulek v každém schématu.
- Počet řádků kódu na balíček, funkce, procedura a tak dále.

Související skripty si stáhněte z [webu ora2pg](https://ora2pg.darold.net/).

### <a name="assess"></a>Posouzení

Po inventarizaci databází Oracle budete mít představu o velikosti databáze a možných problémech. Dalším krokem je spuštění posouzení.

Odhad nákladů na migraci od Oracle do PostgreSQL není snadný. Pro vyhodnocení nákladů na migraci ora2pg zkontroluje všechny databázové objekty, funkce a uložené procedury pro objekty a kód PL/SQL, který nelze automaticky převést.

Nástroj ora2pg má režim analýzy obsahu, který kontroluje databázi Oracle, aby vygenerovala zprávu typu text. Tato sestava popisuje, co obsahuje databáze Oracle a co nelze exportovat.

Chcete-li aktivovat režim *analýzy a sestavy* , použijte exportovaný typ, `SHOW_REPORT` jak je znázorněno v následujícím příkazu:

```
ora2pg -t SHOW_REPORT
```

Nástroj ora2pg může převést kód SQL a PL/SQL ze syntaxe Oracle na PostgreSQL. Takže po analýze databáze může ora2pg odhadnout obtíže s kódem a dobu potřebnou k migraci úplné databáze.

Aby bylo možné odhadnout náklady na migraci za lidské dny, ora2pg vám umožní použít direktivu konfigurace s názvem `ESTIMATE_COST` . Tuto direktivu můžete povolit taky na příkazovém řádku:

```
ora2pg -t SHOW_REPORT --estimate_cost
```

Výchozí jednotka migrace představuje asi pět minut pro odborníka na PostgreSQL. Pokud je tato migrace vaše první, můžete výchozí jednotku migrace zvýšit pomocí direktivy Configuration `COST_UNIT_VALUE` nebo `--cost_unit_value` Možnosti příkazového řádku.

Poslední řádek sestavy zobrazuje celkový odhadovaný kód migrace za den v/v. Odhad se řídí počtem jednotek migrace předpokládaných pro jednotlivé objekty.

Tato jednotka migrace představuje asi pět minut pro odborníka na PostgreSQL. Pokud je tato migrace vaše první, můžete výchozí hodnotu zvýšit pomocí direktivy konfigurace `COST_UNIT_VALUE` nebo možnosti příkazového řádku `--cost_unit_value` . 

V následujícím příkladu kódu vidíte některé variace posouzení: 
* Vyhodnocení tabulek
* Vyhodnocování sloupců
* Vyhodnocení schématu, které používá výchozí nákladovou jednotku po dobu 5 minut
* Vyhodnocení schématu, které používá jednotku nákladů na 10 minut

```
ora2pg -t SHOW_TABLE -c c:\ora2pg\ora2pg_hr.conf > c:\ts303\hr_migration\reports\tables.txt ora2pg -t SHOW_COLUMN -c c:\ora2pg\ora2pg_hr.conf > c:\ts303\hr_migration\reports\columns.txt
ora2pg -t SHOW_REPORT -c c:\ora2pg\ora2pg_hr.conf --dump_as_html --estimate_cost > c:\ts303\hr
_migration\reports\report.html
ora2pg -t SHOW_REPORT -c c:\ora2pg\ora2pg_hr.conf –-cost_unit_value 10 --dump_as_html --estimate_cost > c:\ts303\hr_migration\reports\report2.html
```

Tady je výstup úrovně migrace vyhodnocení schématu B-5:

* Úrovně migrace:

  * Migrace, která se dá spustit automaticky
    
  * B – migrace s přepisováním kódu a náklady na jiné dny až na 5 dní
    
  * C – migrace s přepsáním kódu a náklady na jiné dny za 5 dní
    
* Technické úrovně:

   * 1 = triviální: žádné uložené funkce a žádné triggery

   * 2 = snadné: žádné uložené funkce, ale triggery; bez ručního přepisu

   * 3 = jednoduché: uložené funkce a triggery; bez ručního přepisu

   * 4 = ruční: žádné uložené funkce, ale triggery nebo zobrazení s přepisem kódu

   * 5 = obtížné: uložené funkce nebo triggery s přepisem kódu

Posouzení se skládá z těchto součástí: 
* Písmeno (A nebo B), chcete-li určit, zda migrace potřebuje ruční přepis.

* Číslo od 1 do 5 označující technickou obtížnost. 

Další možnost, `-human_days_limit` , určuje limit pro lidské dny. Tady nastavte úroveň migrace na C, abyste označili, že migrace potřebuje velký objem práce, úplnou správu projektů a podporu migrace. Výchozí hodnota je 10 dnů (lidí). Pomocí direktivy Configuration můžete `HUMAN_DAYS_LIMIT` tuto výchozí hodnotu trvale změnit.

Toto hodnocení schématu bylo vyvinuto, aby uživatelům usnadnilo rozhodování, která databáze má být nejprve migrována a které týmy budou mobilizovány.

### <a name="convert"></a>Převést

Při migraci s minimálními výpadky se změní váš zdroj migrace. Po jednorázové migraci se na základě dat a schématu posunou od cíle. Během fáze *synchronizace dat* zajistěte, aby všechny změny ve zdroji byly zachyceny a aplikovány na cíl téměř v reálném čase. Po ověření, zda jsou všechny změny v cíli aplikovány, můžete *Vyjmout* ze zdroje do cílového prostředí.

V tomto kroku migrace jsou kód Oracle a skripty DDL převedeny nebo přeloženy na PostgreSQL. Nástroj ora2pg Exportuje objekty Oracle ve formátu PostgreSQL automaticky. Některé z generovaných objektů nelze zkompilovat v databázi PostgreSQL bez ručních změn.  

Chcete-li pochopit, které prvky vyžadují ruční zásah, nejprve zkompilujte soubory vygenerované ora2pgy proti databázi PostgreSQL. Zkontrolujte protokol a proveďte potřebné změny, dokud není struktura schématu kompatibilní se syntaxí PostgreSQL.


#### <a name="create-a-migration-template"></a>Vytvoření šablony migrace 

Doporučujeme použít šablonu migrace, kterou ora2pg poskytuje. Při použití možností `--project_base` a `--init_project` ora2pg vytvoří šablonu projektu s pracovní strukturou, konfiguračním souborem a skriptem pro export všech objektů z databáze Oracle. Další informace najdete v [dokumentaci k ora2pg](https://ora2pg.darold.net/documentation.html).

Použijte následující příkaz: 

```
ora2pg --project_base /app/migration/ --init_project test_project

ora2pg --project_base /app/migration/ --init_project test_project
```

Tady je příklad výstupu: 
   
```
Creating project test_project. /app/migration/test_project/ schema/ dblinks/ directories/ functions/ grants/ mviews/ packages/ partitions/ procedures/ sequences/ synonyms/    tables/ tablespaces/ triggers/ types/ views/ sources/ functions/ mviews/ packages/ partitions/ procedures/ triggers/ types/ views/ data/ config/ reports/

Generating generic configuration file

Creating script export_schema.sh to automate all exports.

Creating script import_all.sh to automate all imports.
```

`sources/`Adresář obsahuje kód Oracle. `schema/`Adresář obsahuje kód, který je portem pro PostgreSQL. A `reports/` adresář obsahuje zprávy HTML a vyhodnocení nákladů na migraci.


Po vytvoření struktury projektu se vytvoří obecný konfigurační soubor. V konfiguračním souboru definujte připojení k databázi Oracle a příslušné parametry konfigurace. Další informace o konfiguračním souboru najdete v [dokumentaci k ora2pg](https://ora2pg.darold.net/documentation.html).


#### <a name="export-oracle-objects"></a>Exportovat objekty Oracle

Potom exportujte objekty Oracle jako objekty PostgreSQL spuštěním souboru *export_schema. sh*.

```
cd /app/migration/mig_project
./export_schema.sh
```

Spusťte následující příkaz ručně.

```
SET namespace="/app/migration/mig_project"

ora2pg -t DBLINK -p -o dblink.sql -b %namespace%/schema/dblinks -c
%namespace%/config/ora2pg.conf
ora2pg -t DIRECTORY -p -o directory.sql -b %namespace%/schema/directories -c
%namespace%/config/ora2pg.conf
ora2pg -p -t FUNCTION -o functions2.sql -b %namespace%/schema/functions -c
%namespace%/config/ora2pg.conf ora2pg -t GRANT -o grants.sql -b %namespace%/schema/grants -c %namespace%/config/ora2pg.conf ora2pg -t MVIEW -o mview.sql -b %namespace%/schema/   mviews -c %namespace%/config/ora2pg.conf
ora2pg -p -t PACKAGE -o packages.sql
%namespace%/config/ora2pg.conf -b %namespace%/schema/packages -c
ora2pg -p -t PARTITION -o partitions.sql %namespace%/config/ora2pg.conf -b %namespace%/schema/partitions -c
ora2pg -p -t PROCEDURE -o procs.sql
%namespace%/config/ora2pg.conf -b %namespace%/schema/procedures -c
ora2pg -t SEQUENCE -o sequences.sql
%namespace%/config/ora2pg.conf -b %namespace%/schema/sequences -c
ora2pg -p -t SYNONYM -o synonym.sql -b %namespace%/schema/synonyms -c
%namespace%/config/ora2pg.conf
ora2pg -t TABLE -o table.sql -b %namespace%/schema/tables -c %namespace%/config/ora2pg.conf ora2pg -t TABLESPACE -o tablespaces.sql -b %namespace%/schema/tablespaces -c
%namespace%/config/ora2pg.conf
ora2pg -p -t TRIGGER -o triggers.sql -b %namespace%/schema/triggers -c
%namespace%/config/ora2pg.conf ora2pg -p -t TYPE -o types.sql -b %namespace%/schema/types -c %namespace%/config/ora2pg.conf ora2pg -p -t VIEW -o views.sql -b %namespace%/   schema/views -c %namespace%/config/ora2pg.conf
```

K extrakci dat použijte následující příkaz.

```
ora2pg -t COPY -o data.sql -b %namespace/data -c %namespace/config/ora2pg.conf
```

#### <a name="compile-files"></a>Kompilovat soubory

Nakonec zkompilujte všechny soubory s Azure Database for PostgreSQL serverem. Můžete zvolit, že se mají načíst ručně generované soubory DDL, nebo použít druhý skript *import_all. sh* , aby se tyto soubory importovaly interaktivně.

```
psql -f %namespace%\schema\sequences\sequence.sql -h server1-

server.postgres.database.azure.com -p 5432 -U username@server1-server -d database -l

%namespace%\ schema\sequences\create_sequences.log

psql -f %namespace%\schema\tables\table.sql -h server1-server.postgres.database.azure.com p 5432 -U username@server1-server -d database -l    %namespace%\schema\tables\create_table.log
```

Tady je příkaz pro import dat:

```
psql -f %namespace%\data\table1.sql -h server1-server.postgres.database.azure.com -p 5432 -U username@server1-server -d database -l %namespace%\data\table1.log

psql -f %namespace%\data\table2.sql -h server1-server.postgres.database.azure.com -p 5432 -U username@server1-server -d database -l %namespace%\data\table2.log
```

Během kompilování souborů ověřte protokoly a opravte jakoukoli syntaxi, kterou ora2pg nebylo možné převést sám na sebe.

Další informace najdete v tématu věnovaném [řešení migrace Azure Database for PostgreSQL Oracle](https://github.com/Microsoft/DataMigrationTeam/blob/master/Whitepapers/Oracle%20to%20Azure%20Database%20for%20PostgreSQL%20Migration%20Workarounds.pdf).

## <a name="migrate"></a>Migrate 

Po splnění potřebných požadavků a dokončení kroků migrace můžete spustit migraci schématu a dat.

### <a name="migrate-schema-and-data"></a>Migrace schématu a dat

Po provedení nezbytných oprav je stabilní sestavení databáze připraveno k nasazení. Spusťte `psql` příkazy importu, které odkazují na soubory, které obsahují upravený kód. Tato úloha kompiluje databázové objekty proti databázi PostgreSQL a importuje data.

V tomto kroku můžete implementovat úroveň paralelismu na importování dat.

### <a name="sync-data-and-cut-over"></a>Synchronizovat data a přeříznout

V případě migrace online (s minimálními výpadky) se zdroj migrace stále mění. Po jednorázové migraci se na základě dat a schématu posunou od cíle. 

Během fáze *synchronizace dat* zajistěte, aby všechny změny ve zdroji byly zachyceny a aplikovány na cíl téměř v reálném čase. Po ověření, zda jsou všechny změny aplikovány, můžete vyjmout ze zdroje do cílového prostředí.

Chcete-li provést online migraci, obraťte se AskAzureDBforPostgreSQL@service.microsoft.com na podporu.

V případě *rozdílové nebo přírůstkové* migrace, která pro každou tabulku používá ora2pg, použijte dotaz, který filtruje (*vyjme*) podle data, času nebo jiného parametru. Pak dokončete migraci pomocí druhého dotazu, který migruje zbývající data.

V tabulce zdrojová data nejprve migrujte všechna historická data. Tady je příklad:

```
select * from table1 where filter_data < 01/01/2019
```

Od počáteční migrace můžete zadat dotaz na změny spuštěním příkazu, jako je tento:

```
select * from table1 where filter_data >= 01/01/2019
```

V takovém případě doporučujeme vylepšit ověřování pomocí kontroly parity dat na obou stranách, zdroje i cíle.

## <a name="postmigration"></a>Postmigration 

Po dokončení fáze *migrace* dokončete úlohy postmigration a ujistěte se, že vše funguje co nejrychleji a efektivně.

### <a name="remediate-applications"></a>Opravit aplikace

Po migraci dat do cílového prostředí všechny aplikace, které dříve využily zdroj, musí začít spotřebovávat cíl. Instalační program někdy vyžaduje změny v aplikacích.

### <a name="test"></a>Test

Po migraci dat do cíle spusťte testy pro databáze, abyste ověřili, že aplikace dobře fungují s cílem. Zajistěte, aby byl zdroj a cíl správně migrován spuštěním ručních ověřovacích skriptů pro zdrojové a PostgreSQL cílové databáze Oracle.

V ideálním případě, pokud má zdrojová a cílová databáze cestu k síti, ora2pg by se měly použít k ověření dat. Tuto akci můžete použít `TEST` k zajištění, že se všechny objekty z databáze Oracle vytvořily v PostgreSQL. 

Spusťte tento příkaz:

```
ora2pg -t TEST -c config/ora2pg.conf > migration_diff.txt
```

### <a name="optimize"></a>Optimalizace

Fáze postmigration je rozhodující pro sjednocení problémů s přesností dat a ověření úplnosti. V této fázi také řešíte problémy s výkonem úloh.

## <a name="migration-assets"></a>Prostředky migrace 

Další informace o tomto scénáři migrace najdete v následujících zdrojích informací. Podporují zapojení projektů do reálné migrace.

| Prostředek | Popis    |
| -------------- | ------------------ |
| [Kuchařka migrace z Oracle do Azure PostgreSQL](https://github.com/Microsoft/DataMigrationTeam/blob/master/Whitepapers/Oracle%20to%20Azure%20PostgreSQL%20Migration%20Cookbook.pdf) | Tento dokument pomůže architektům, konzultantům, správcům databází a souvisejícím rolím rychle migrovat úlohy z Oracle do Azure Database for PostgreSQL pomocí ora2pg. |
| [Alternativní řešení migrace z Oracle do Azure PostgreSQL](https://github.com/Microsoft/DataMigrationTeam/blob/master/Whitepapers/Oracle%20to%20Azure%20Database%20for%20PostgreSQL%20Migration%20Workarounds.pdf) | Tento dokument pomůže architektům, konzultantům, správcům databází a souvisejícím rolím rychle opravovat nebo řešit problémy při migraci úloh z Oracle do Azure Database for PostgreSQL. |
| [Postup instalace ora2pg v systému Windows nebo Linux](https://github.com/microsoft/DataMigrationTeam/blob/master/Whitepapers/Steps%20to%20Install%20ora2pg%20on%20Windows%20and%20Linux.pdf)                       | Tento dokument poskytuje Úvodní příručku pro migraci schématu a dat od Oracle do Azure Database for PostgreSQL pomocí ora2pg v systému Windows nebo Linux. Další informace najdete v [dokumentaci k ora2pg](http://ora2pg.darold.net/documentation.html). |

Data tým SQL Engineering vyvinuli tyto prostředky. Základní Chart týmu je odblokování a urychlení komplexní modernizace pro projekty migrace datové platformy na Microsoft Azure datovou platformu.

## <a name="more-support"></a>Další podpora

Pokud chcete získat nápovědu k migraci nad rámec ora2pg nástrojů, kontaktujte [ @Ask Azure DB pro PostgreSQL](mailto:AskAzureDBforPostgreSQL@service.microsoft.com).

## <a name="next-steps"></a>Další kroky

Matrici služeb a nástrojů pro migraci databáze a dat a pro specializované úlohy najdete v tématu [služby a nástroje pro migraci dat](https://docs.microsoft.com/azure/dms/dms-tools-matrix).

Dokumentace: 
- [Dokumentace k Azure Database for PostgreSQL](https://docs.microsoft.com/azure/postgresql/)
- [dokumentace k ora2pg](https://ora2pg.darold.net/documentation.html)
- [Web PostgreSQL](https://www.postgresql.org/)
- [Podpora autonomních transakcí v PostgreSQL](http://blog.dalibo.com/2016/08/19/Autonoumous_transactions_support_in_PostgreSQL.html) 
