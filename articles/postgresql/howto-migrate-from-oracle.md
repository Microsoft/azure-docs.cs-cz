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
ms.openlocfilehash: 1a20ffd7150ac75721b2affc2f4375301c4754c8
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "105643577"
---
# <a name="migrate-oracle-to-azure-database-for-postgresql"></a>Migrace Oracle na Azure Database for PostgreSQL

V této příručce se naučíte migrovat schéma Oracle na Azure Database for PostgreSQL. 

Podrobné a komplexní pokyny k migraci najdete v [příručce k migraci prostředků](https://github.com/microsoft/OrcasNinjaTeam/blob/master/Oracle%20to%20PostgreSQL%20Migration%20Guide/Oracle%20to%20Azure%20Database%20for%20PostgreSQL%20Migration%20Guide.pdf). 

## <a name="prerequisites"></a>Požadavky

Chcete-li migrovat schéma Oracle na Azure Database for PostgreSQL, je nutné provést následující kroky: 

- Ověřte, že je podporované vaše zdrojové prostředí. 
- Stáhněte si nejnovější verzi [ora2pg](https://ora2pg.darold.net/). 
- Nejnovější verze [modulu DBD](https://www.cpan.org/modules/by-module/DBD/) 


## <a name="overview"></a>Přehled

PostgreSQL je jednou z nejpokročilejších open source databází na světě. Tento článek popisuje, jak pomocí nástroje Free ora2pg migrovat databázi Oracle na PostgreSQL. K migraci databáze Oracle nebo MySQL do schématu kompatibilního s PostgreSQL můžete použít ora2pg a bezplatný nástroj. Nástroj propojuje vaši databázi Oracle, automaticky ji vyhledává a extrahuje její strukturu nebo data. Následně ora2pg vygeneruje skripty SQL, které můžete načíst do databáze PostgreSQL. ora2pg se dá použít pro úlohy od zpětné analýzy databáze Oracle, provádění rozsáhlých podnikových migrací databáze nebo pouhá replikace některých dat Oracle do databáze PostgreSQL. Je snadné použít a nevyžaduje žádné znalosti databáze Oracle jiné než možnost zadat parametry potřebné pro připojení k databázi Oracle.

> [!NOTE]
> Další informace o používání nejnovější verze ora2pg najdete v [dokumentaci k ora2pg](https://ora2pg.darold.net/documentation.html).

### <a name="typical-ora2pg-migration-architecture"></a>Typická architektura migrace ora2pg

![Snímek obrazovky architektury migrace ora2pg](media/howto-migrate-from-oracle/ora2pg-migration-architecture.png)

Po zřízení virtuálního počítače a Azure Database for PostgreSQL jsou potřebné dvě konfigurace, aby bylo možné mezi nimi vzájemně navázat spojení: **Povolit služby Azure** a **vykonat připojení SSL**, znázorněné následujícím způsobem:

- Okno **zabezpečení připojení** – > **Povolení přístupu ke službám Azure** – > na

- Okno **zabezpečení připojení** – > **Nastavení SSL**  ->  **vynutilo připojení SSL** – > zakázáno

### <a name="recommendations"></a>Doporučení

- Chcete-li zlepšit výkon operací posouzení nebo exportu na serveru Oracle, shromážděte statistiku následujícím způsobem:

   ```
   BEGIN
   
     DBMS_STATS.GATHER_SCHEMA_STATS
     DBMS_STATS.GATHER_DATABASE_STATS
     DBMS_STATS.GATHER_DICTIONARY_STATS
     END;
   ```

- Exportujte data pomocí příkazu COPY místo příkazu INSERT.

- Vyhněte se exportu tabulek s jejich FKs, omezeními a indexy. díky tomu bude pomalejší data importovat do PostgreSQL.

- Vytvářejte materializovaná zobrazení pomocí **klauzule No data** a aktualizujte ji později.

- Pokud je to možné, implementujte jedinečné indexy v materializovaná zobrazení, díky které se bude aktualizovat rychleji pomocí syntaxe `REFRESH MATERIALIZED VIEW CONCURRENTLY` .



## <a name="pre-migration"></a>Před migrací 

Po ověření, že je vaše zdrojové prostředí podporované a že jste si zajistili, že jste provedli všechny požadované součásti, jste připraveni zahájit fázi před migrací. Tato část procesu zahrnuje provádění inventarizace databází, které potřebujete migrovat, jejich vyhodnocování pro potenciální problémy s migrací nebo blokování a pak řešení všech položek, které jste pravděpodobně vystavili. Pro heterogenní migrace, jako je například Oracle pro Azure Database for PostgreSQL, zahrnuje tato fáze také převod schématu ve zdrojových databázích tak, aby byly kompatibilní s cílovým prostředím.

### <a name="discover"></a>Zjišťování

Cílem fáze zjišťování je identifikovat stávající zdroje dat a podrobnosti o funkcích, které jsou používány k lepšímu porozumění a plánování migrace. Tento proces zahrnuje kontrolu sítě a identifikaci všech instancí Oracle vaší organizace spolu s použitou verzí a funkcemi.

Skripty Microsoft Oracle pre-Assessment se spouštějí proti databázi Oracle. Skripty předběžného posouzení představují sadu dotazů, které jsou v metadatech Oracle, a poskytuje následující:

- Inventář databáze, včetně počtu objektů podle schématu, typu a stavu.

- Hrubý odhad nezpracovaných dat v jednotlivých schématech (na základě statistik).

- Změna velikosti tabulek v každém schématu.

- Počet řádků kódu na balíček, funkce, procedura atd.

Související skripty si stáhněte z [webu ora2pg](http://ora2pg.darold.net/).

### <a name="assess"></a>Posouzení

Po dokončení inventarizace databází Oracle, abyste získali představu o velikosti databáze a o tom, jaké jsou problémy, je dalším krokem spuštění posouzení.

Odhad nákladů na proces migrace od Oracle do PostgreSQL není snadný. Aby bylo možné získat dobrý odhad těchto nákladů na migraci, ora2pg zkontroluje všechny objekty databáze, všechny funkce a uložené procedury k detekci, zda stále existují některé objekty a kód PL/SQL, který nelze automaticky převést pomocí ora2pg.

ora2pg má režim analýzy obsahu, který kontroluje databázi Oracle, aby vygenerovala textovou zprávu o tom, co databáze Oracle obsahuje a co se nedá exportovat.

Chcete-li aktivovat režim **analýzy a sestavy** , použijte exportovaný typ, `SHOW_REPORT` jak je znázorněno v následujícím příkazu:

```
ora2pg -t SHOW_REPORT
```

Po analýze databáze ora2pg s možností převodu kódu SQL a PL/SQL ze syntaxe Oracle na PostgreSQL může další postup zvýšit odhadem potíží s kódem a dobu potřebnou k provedení úplné migrace databáze.

Pokud chcete odhadnout náklady na migraci za muže, ora2pg vám umožní použít direktivu konfigurace s názvem ESTIMATE_COST, kterou je možné povolit taky na příkazovém řádku:

```
ora2pg -t SHOW_REPORT --estimate_cost
```

Výchozí jednotka migrace představuje asi pět minut pro odborníka na PostgreSQL. Pokud je to vaše první migrace, můžete ji získat výše pomocí direktivy konfigurace COST_UNIT_VALUE nebo možnosti příkazového řádku--cost_unit_value.

Poslední řádek sestavy zobrazuje celkový odhadovaný kód migrace ve dnech po dobu od počtu jednotek migrace předpokládaných pro jednotlivé objekty.

Tato jednotka migrace představuje asi pět minut pro odborníka na PostgreSQL. Pokud je to vaše první migrace, můžete výchozí hodnotu zvýšit pomocí direktivy Configuration COST_UNIT_VALUE nebo možnosti příkazového řádku--cost_unit_value. Níže najdete některé variace posouzení a) vyhodnocení tabulek; b) vyhodnocení schématu c) vyhodnocení schématu pomocí výchozího cost_unit (5 min) d) posouzení schématu pomocí 10 minut jako nákladové jednotky.

```
ora2pg -t SHOW_TABLE -c c:\ora2pg\ora2pg_hr.conf > c:\ts303\hr_migration\reports\tables.txt ora2pg -t SHOW_COLUMN -c c:\ora2pg\ora2pg_hr.conf > c:\ts303\hr_migration\reports\columns.txt
ora2pg -t SHOW_REPORT -c c:\ora2pg\ora2pg_hr.conf --dump_as_html --estimate_cost > c:\ts303\hr
_migration\reports\report.html
ora2pg -t SHOW_REPORT -c c:\ora2pg\ora2pg_hr.conf –-cost_unit_value 10 --dump_as_html --estimate_cost > c:\ts303\hr_migration\reports\report2.html
```

Výstup vyhodnocení schématu je znázorněný na následujícím obrázku:

**Úroveň migrace: B-5**

Úrovně migrace:

Migrace, která se může spustit automaticky

B – migrace s přepisováním kódu a náklady na jiné dny až na 5 dní

C – migrace s přepisováním kódu a náklady na jiné dny nad 5 dny

Technické úrovně:

1 = triviální: žádné uložené funkce a žádné triggery

2 = snadné: žádné uložené funkce, ale s triggery, bez ručního přepisu

3 = jednoduché: uložené funkce a triggery bez ručního přepisu

4 = ruční: žádné uložené funkce, ale s triggery nebo zobrazeními s přepisem kódu

5 = obtížné: uložené funkce nebo triggery s přepisem kódu

Posouzení se skládá z písmene (A nebo B), které určuje, jestli migrace potřebuje ruční přepis, nebo ne, a číslo od 1 do 5 k indikaci úrovně technického obtížnosti. Máte další možnost human_days_limit pro určení počtu povolených lidí, kde by měla být úroveň migrace nastavena na C, aby označovala, že potřebuje velký objem práce a úplnou správu projektů s podporou migrace. Výchozí hodnota je 10 dnů (lidí). Pomocí direktivy Configuration HUMAN_DAYS_LIMIT můžete tuto výchozí hodnotu trvale změnit.

Tato funkce byla vyvinuta za účelem určení, která databáze by mohla být migrována jako první a jaký je tým, který je potřeba mobilizovat.

### <a name="convert"></a>Převést

Při migraci s minimálními výpadky se změní zdroj, který migrujete, a když dojde k jednorázové migraci, přesunete se od cíle z hlediska dat a schématu. Během fáze **synchronizace dat** je potřeba zajistit, aby všechny změny ve zdroji byly zachyceny a aplikovány na cíl téměř v reálném čase. Po ověření, že všechny změny ve zdroji byly aplikovány na cíl, můžete přímou migraci ze zdroje do cílového prostředí.

V tomto kroku migrace dojde k převodu nebo překladu kódu Oracle + DDLS na PostgreSQL. Nástroj ora2pg Exportuje objekty Oracle ve formátu PostgreSQL automaticky. V případě generovaných objektů se některé nebudou kompilovat v databázi PostgreSQL bez ručních změn.  
Proces porozumění, které prvky potřebují ruční zásah, se skládá při kompilování souborů generovaných nástrojem ora2pg proti databázi PostgreSQL, kontrole protokolu a provádění nezbytných změn, dokud není všechna struktura schématu kompatibilní se syntaxí PostgreSQL.


#### <a name="create-migration-template"></a>Vytvořit šablonu migrace 

Nejprve se doporučuje vytvořit šablonu migrace, která je k dispozici v poli ora2pg. Dvě možnosti--project_base a--init_project při použití naznačují, že je potřeba vytvořit šablonu projektu s pracovní stromovou strukturou, konfiguračním souborem a skriptem pro export všech objektů z databáze Oracle. Další informace najdete v [dokumentaci k ora2pg](https://ora2pg.darold.net/documentation.html).

   Použijte následující příkaz: 

   ```
   ora2pg --project_base /app/migration/ --init_project test_project
   
   ora2pg --project_base /app/migration/ --init_project test_project
   ```

Příklad výstupu: 
   
   ```
   Creating project test_project. /app/migration/test_project/ schema/ dblinks/ directories/ functions/ grants/ mviews/ packages/ partitions/ procedures/ sequences/ synonyms/    tables/ tablespaces/ triggers/ types/ views/ sources/ functions/ mviews/ packages/ partitions/ procedures/ triggers/ types/ views/ data/ config/ reports/
   
   Generating generic configuration file
   
   Creating script export_schema.sh to automate all exports.
   
   Creating script import_all.sh to automate all imports.
   ```

Zdroje/adresáře obsahují kód Oracle, schéma/adresář obsahuje kód, který je portem a PostgreSQL. Sestavy/adresář obsahují sestavy HTML s hodnocením nákladů na migraci.


Po vytvoření struktury projektu se vytvoří obecný konfigurační soubor. Definujte připojení k databázi Oracle a také příslušné konfigurační parametry v konfiguraci.  V dokumentaci k ora2pg najdete informace o tom, co je možné nakonfigurovat v konfiguračním souboru a jak.


#### <a name="export-oracle-objects"></a>Exportovat objekty Oracle

Potom exportujte objekty Oracle jako objekty PostgreSQL spuštěním souboru export_schema. sh.

   ```
   cd /app/migration/mig_project
   ./export_schema.sh
   
   Run the following command manually:
   
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

   K extrakci dat použijte následující příkaz:

   ```
   ora2pg -t COPY -o data.sql -b %namespace/data -c %namespace/config/ora2pg.conf
   ```

#### <a name="compile-files"></a>Kompilovat soubory

Nakonec zkompilujte všechny soubory proti serveru Azure Database for PostgreSQL. Nyní je možné zvolit, aby se soubory DDL vygenerovaly ručně, nebo pomocí druhého skriptu import_all. sh importovat tyto soubory interaktivně.

   ```
   psql -f %namespace%\schema\sequences\sequence.sql -h server1-
   
   server.postgres.database.azure.com -p 5432 -U username@server1-server -d database -l
   
   %namespace%\ schema\sequences\create_sequences.log
   
   psql -f %namespace%\schema\tables\table.sql -h server1-server.postgres.database.azure.com p 5432 -U username@server1-server -d database -l    %namespace%\schema\tables\create_table.log
   ```

   Příkaz pro import dat:

   ```
   psql -f %namespace%\data\table1.sql -h server1-server.postgres.database.azure.com -p 5432 -U username@server1-server -d database -l %namespace%\data\table1.log
   
   psql -f %namespace%\data\table2.sql -h server1-server.postgres.database.azure.com -p 5432 -U username@server1-server -d database -l %namespace%\data\table2.log
   ```

Během kompilování souborů ověřte protokoly a opravte nezbytné syntaxe, které ora2pg nebylo možné převést.

Pořiďte si dokumentaci k [systému Oracle White Paper, která Azure Database for PostgreSQL řešení migrace](https://github.com/Microsoft/DataMigrationTeam/blob/master/Whitepapers/Oracle%20to%20Azure%20Database%20for%20PostgreSQL%20Migration%20Workarounds.pdf) pro podporu při řešení problémů.

## <a name="migrate"></a>Migrate 

Po splnění potřebných požadavků a dokončení úkolů přidružených ke fázi **před migrací** jste připraveni provést migraci schématu a dat.

### <a name="migrate-schema-and-data"></a>Migrace schématu a dat

Po dokončení oprav je stabilní sestavení databáze připravené k nasazení.

V tomto okamžiku je potřeba provést příkazy importu *psql* , které odkazují na soubory obsahující upravený kód, aby se daly kompilovat databázové objekty proti databázi PostgreSQL a importovat data.

V tomto kroku je možné implementovat určitou úroveň paralelismu pro import dat.

### <a name="data-sync-and-cutover"></a>Synchronizace dat a přímou migraci

V případě migrace online (s minimálními výpadky) se změní zdroj, který migrujete, a když dojde k jednorázové migraci, přesunete se od cíle z hlediska dat a schématu. Během fáze **synchronizace dat** je potřeba zajistit, aby všechny změny ve zdroji byly zachyceny a aplikovány na cíl téměř v reálném čase. Po ověření, že všechny změny ve zdroji byly aplikovány na cíl, můžete přímou migraci ze zdroje do cílového prostředí.

Od března 2019 můžete v případě, že chcete provést online migraci, zvážit použití Attunity repliky pro migrace do Microsoftu nebo Striim.

Pro *rozdílovou a přírůstkovou* migraci pomocí ora2pg se technika skládá z použití pro každou tabulku a dotaz, který používá filtr (vyjmout) podle data a času, atd. a po dokončení migrace použije druhý dotaz, který migruje zbývající data (zbylé).

V tabulce zdrojová data nejprve migrujte všechna historická data. Příklad:

```
select * from table1 where filter_data < 01/01/2019
```

Změny provedené od počáteční migrace můžete zadat spuštěním příkazu, který bude vypadat přibližně takto:

```
select * from table1 where filter_data >= 01/01/2019
```

V takovém případě se doporučuje ověřit, jestli je ověřování rozšířené, a to kontrolou parity dat na obou stranách, zdroji i cíli.

## <a name="post-migration"></a>Po migraci 

Po úspěšném dokončení fáze **migrace** je potřeba projít řadu úkolů po migraci, abyste měli jistotu, že všechno funguje co nejrychleji a efektivně.

### <a name="remediate-applications"></a>Opravit aplikace

Po migraci dat do cílového prostředí všechny aplikace, které dříve využily zdroj, musí začít spotřebovávat cíl. Výsledkem bude, že v některých případech bude nutné provést změny aplikací.

### <a name="perform-tests"></a>Provést testy

Po migraci dat do cíle proveďte testy proti databázím, abyste ověřili, že se aplikace po migraci řádně doplní s cílem.

Aby bylo zaručeno, že zdroj a cíl jsou správně migrovány, spusťte skripty ručního ověření dat proti zdrojové databázi Oracle a PostgreSQL cílové databáze.

V ideálním případě, pokud má zdrojová a cílová databáze cestu k síti, ora2pg by se měly použít k ověření dat. Pomocí akce TEST můžete zkontrolovat, že všechny objekty z databáze Oracle byly vytvořeny v PostgreSQL. Spusťte příkaz, jak je znázorněno na následujícím obrázku:

```
ora2pg -t TEST -c config/ora2pg.conf > migration_diff.txt
```

### <a name="optimize"></a>Optimalizace

Fáze po migraci je zásadní pro sjednocení problémů s přesností dat a ověření úplnosti a také řešení potíží s výkonem s úlohou.

## <a name="migration-assets"></a>Prostředky migrace 

Další pomoc s dokončením tohoto scénáře migrace najdete v následujících materiálech, které byly vyvinuty v rámci podpory zapojení projektu z reálného světa.

| **Odkaz na název** | **Popis**    |
| -------------- | ------------------ |
| [Kuchařka migrace z Oracle do Azure PostgreSQL](https://github.com/Microsoft/DataMigrationTeam/blob/master/Whitepapers/Oracle%20to%20Azure%20PostgreSQL%20Migration%20Cookbook.pdf) | Účelem tohoto dokumentu je poskytnout architektům, konzultantům, specializující a souvisejícím rolím průvodce pro rychlou migraci úloh z Oracle do Azure Database for PostgreSQL pomocí nástroje ora2pg. |
| [Alternativní řešení migrace z Oracle do Azure PostgreSQL](https://github.com/Microsoft/DataMigrationTeam/blob/master/Whitepapers/Oracle%20to%20Azure%20Database%20for%20PostgreSQL%20Migration%20Workarounds.pdf) | Účelem tohoto dokumentu je poskytnout architektům, konzultantům, specializující a souvisejícím rolím průvodce pro rychlé opravy nebo řešení potíží při migraci úloh od Oracle do Azure Database for PostgreSQL. |
| [Postup instalace ora2pg v systému Windows nebo Linux](https://github.com/microsoft/DataMigrationTeam/blob/master/Whitepapers/Steps%20to%20Install%20ora2pg%20on%20Windows%20and%20Linux.pdf)                       | Tento dokument je určen k použití jako úvodní příručka pro povolení migrace schématu & dat od Oracle do Azure Database for PostgreSQL pomocí nástroje ora2pg v systému Windows nebo Linux. Podrobné informace o nástroji najdete na adrese http://ora2pg.darold.net/documentation.html . |

Data tým SQL Engineering vyvinuli tyto prostředky. Základní Chart týmu je odblokování a urychlení komplexní modernizace pro projekty migrace datové platformy na datovou platformu Azure od Microsoftu.


### <a name="contact-support"></a>Kontaktování podpory

Pokud potřebujete pomoc s migrací nad rámec nástrojů ora2pg, obraťte se na alias služby [ @Ask Azure DB for PostgreSQL](mailto:AskAzureDBforPostgreSQL@service.microsoft.com) , kde najdete informace o dalších možnostech migrace.

## <a name="next-steps"></a>Další kroky

- Matrici dostupných služeb nebo nástrojů společnosti Microsoft a třetích stran, které vám pomůžou s různými scénáři migrace databáze a dat (a specializovanými úkoly), najdete v článku [služba a nástroje pro migraci dat](https://docs.microsoft.com/azure/dms/dms-tools-matrix).

Další informace najdete v následujících tématech: 
- [Dokumentace k Azure Database for PostgreSQL](https://docs.microsoft.com/azure/postgresql/)
- [dokumentace k ora2pg](https://ora2pg.darold.net/documentation.html)
- [Web PostgreSQL](https://www.postgresql.org/)
- [Podpora autonomních transakcí v PostgreSQL](http://blog.dalibo.com/2016/08/19/Autonoumous_transactions_support_in_PostgreSQL.html) 

Pro video obsah: 
- [Přehled cesty migrace a nástroje/služby doporučené pro provádění posouzení a migrace](https://azure.microsoft.com/resources/videos/overview-of-migration-and-recommended-tools-services/).