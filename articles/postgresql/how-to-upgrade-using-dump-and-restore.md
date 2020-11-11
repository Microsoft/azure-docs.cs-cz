---
title: Upgrade pomocí výpisu a obnovení Azure Database for PostgreSQL – jeden server
description: Popisuje metody upgradu v režimu offline pomocí výpisu a obnovení databází pro migraci na vyšší verzi Azure Database for PostgreSQL – jeden server.
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.topic: how-to
ms.date: 11/10/2020
ms.openlocfilehash: e756e033c8e5b2508dca9bde76ad16be26a940fa
ms.sourcegitcommit: 4bee52a3601b226cfc4e6eac71c1cb3b4b0eafe2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/11/2020
ms.locfileid: "94505780"
---
# <a name="upgrade-your-postgresql-database-using-dump-and-restore"></a>Upgrade databáze PostgreSQL pomocí výpisu a obnovení

Server PostgreSQL nasazený v Azure Database for PostgreSQL-Single server můžete upgradovat tak, že databáze migrujete na server vyšší hlavní verze pomocí následujících metod.
* Metoda **offline** s použitím PostgreSQL [pg_dump](https://www.postgresql.org/docs/current/static/app-pgdump.html) a [pg_restore](https://www.postgresql.org/docs/current/static/app-pgrestore.html) , které při migraci dat způsobí výpadky. Tento dokument řeší tuto metodu upgradu/migrace.
* **Online** metoda využívající [Database Migration Service](https://docs.microsoft.com/azure/dms/tutorial-azure-postgresql-to-azure-postgresql-online-portal) (DMS). Tato metoda poskytuje zkrácenou migraci za výpadky a udržuje cílovou databázi v synchronizaci se zdrojem a vy můžete vybrat, kdy se má vydělenit. Existuje však několik požadavků a omezení, které je potřeba vyřešit pomocí DMS. Podrobnosti najdete v dokumentaci k [DMS](https://docs.microsoft.com/azure/dms/tutorial-azure-postgresql-to-azure-postgresql-online-portal). 

 V následující tabulce jsou některá doporučení založená na velikostech a scénářích databáze.

| **Databáze/scénář** | **Výpis stavu nebo obnovení (offline)** | **DMS (online)** |
| ------ | :------: | :-----: |
| Máte malou databázi a můžete si dovolit nevýpadky při upgradu  | X | |
| Malé databáze (< 10 GB)  | X | X | 
| Malé a střední databáze (10 GB až 100 GB) | X | X |
| Velké databáze (> 100 GB) |  | X |
| Může umožnit výpadkům upgradovat (bez ohledu na velikost databáze). | X |  |
| Je možné adresovat [požadavky](https://docs.microsoft.com/azure/dms/tutorial-azure-postgresql-to-azure-postgresql-online-portal#prerequisites)DMS, včetně restartování? |  | X |
| Může během procesu upgradu zabránit v DDLs a neprotokolovaných tabulkách? | |  X |

Tato příručka obsahuje několik metod offline migrace a příklady, které ukazují, jak můžete migrovat ze zdrojového serveru na cílový server, na kterém běží vyšší verze PostgreSQL.

> [!NOTE]
> Výpis PostgreSQL a obnovení je možné provést mnoha způsoby. Můžete se rozhodnout migrovat pomocí jedné z metod poskytovaných v této příručce nebo zvolit jakékoli jiné způsoby, jak budete vyhovovat vašim potřebám. Další informace o syntaxi výpisu a obnovení s dalšími parametry najdete v článcích [pg_dump](https://www.postgresql.org/docs/current/static/app-pgdump.html) a [pg_restore](https://www.postgresql.org/docs/current/static/app-pgrestore.html). 


## <a name="prerequisites-for-using-dump-and-restore-with-azure-database-for-postgresql"></a>Předpoklady pro používání výpisu a obnovení pomocí Azure Database for PostgreSQL
 
Pro krokování tohoto průvodce potřebujete:

- **Zdrojová** databáze PostgreSQL se systémem 9,5, 9,6 nebo 10, kterou chcete upgradovat
- **Cílový** Server databáze PostgreSQL s požadovanou hlavní verzí serveru [Azure Database for PostgreSQL](quickstart-create-server-database-portal.md). 
- PostgreSQL klientský systém pro spuštění příkazů výpisu a obnovení.
  - Může to být klient systému Linux nebo Windows s nainstalovaným PostgreSQL a má nainstalované nástroje příkazového řádku [pg_dump](https://www.postgresql.org/docs/current/static/app-pgdump.html) a [pg_restore](https://www.postgresql.org/docs/current/static/app-pgrestore.html) . 
  - Alternativně můžete použít [Azure Cloud Shell](https://shell.azure.com) nebo kliknutím na Azure Cloud Shell na řádku nabídek v pravém horním rohu [Azure Portal](https://portal.azure.com). `az login`Před spuštěním příkazů výpisu paměti a obnovení se budete muset přihlásit ke svému účtu.
- Váš klient PostgreSQL se nejlépe spouští ve stejné oblasti jako zdrojový a cílový server. 


## <a name="additional-details-and-considerations"></a>Další podrobnosti a požadavky
- Připojovací řetězec pro zdrojovou a cílovou databázi můžete najít kliknutím na "připojovací řetězce" na portálu. 
- Na serveru možná běží více než jedna databáze. Seznam databází můžete najít tak, že se připojíte ke zdrojovému serveru a spustíte `\l` .
- Vytvořte odpovídající databáze na cílovém databázovém serveru.
- Můžete přeskočit upgrade `azure_maintenance` nebo šablony databáze.
- Pokud chcete určit, že databáze je vhodná pro tento režim migrace, přečtěte si výše uvedené tabulky.
- Pokud chcete použít Azure Cloud Shell, počítejte s tím, že časový limit relace vypršel po 20 minutách. Pokud je velikost databáze < 10 GB, může být možné dokončit upgrade bez vypršení časového limitu relace. V opačném případě bude pravděpodobně nutné ponechat relaci otevřený jiným způsobem, například stisknutím klávesy <Enter> jednou za 10-15 minut. 


## <a name="example-database-used-in-this-guide"></a>Ukázková databáze použitá v tomto průvodci

V této příručce se k ilustraci s příklady používají následující zdrojové a cílové servery a názvy databází.

 | **Popis** | **Hodnota** |
 | ------- | ------- |
 | Zdrojový server (v 9,5) | pg-95.postgres.database.azure.com |
 | Zdrojová databáze | bench5gb |
 | Velikost zdrojové databáze | 5 GB |
 | Zdrojové uživatelské jméno | pg@pg-95 |
 | Cílový server (V11) | pg-11.postgres.database.azure.com |
 | Cílová databáze | bench5gb |
 | Cílové uživatelské jméno | pg@pg-11 |

## <a name="upgrade-your-databases-using-offline-migration-methods"></a>Upgrade databází pomocí metod offline migrace
Můžete se rozhodnout použít jednu z metod popsaných v této části pro vaše upgrady. Při provádění úloh můžete použít následující tipy.

- Pokud používáte stejné heslo pro zdrojovou a cílovou databázi, můžete nastavit `PGPASSWORD=yourPassword` proměnnou prostředí.  Pak není nutné zadávat heslo pokaždé, když spouštíte příkazy, jako je psql, pg_dump a pg_restore.  Podobně můžete nastavit další proměnné, jako `PGUSER` např `PGSSLMODE` ., viz [proměnné prostředí PostgreSQL](https://www.postgresql.org/docs/11/libpq-envars.html).
  
- Pokud váš server PostgreSQL vyžaduje připojení TLS/SSL (ve výchozím nastavení na serverech Azure Database for PostgreSQL), nastavte proměnnou prostředí `PGSSLMODE=require` tak, aby se nástroj pg_restore připojil k TLS. Bez TLS se chyba může přečíst.  `FATAL:  SSL connection is required. Please specify SSL options and retry.`

- Na příkazovém řádku Windows spusťte příkaz `SET PGSSLMODE=require` před spuštěním příkazu pg_restore. V systému Linux nebo bash spusťte příkaz `export PGSSLMODE=require` před spuštěním příkazu pg_restore.

### <a name="method-1-migrate-using-dump-file"></a>Metoda 1: migrace pomocí souboru s výpisem paměti

Tato metoda se skládá ze dvou kroků. Nejdřív je potřeba vytvořit výpis paměti ze zdrojového serveru. Druhým krokem je obnovení souboru s výpisem paměti na cílovém serveru. Další podrobnosti najdete v dokumentaci k [migraci pomocí výpisu paměti a obnovení](howto-migrate-using-dump-and-restore.md) . Tato metoda se doporučuje, pokud máte velké databáze a váš klientský systém má dostatek úložiště pro uložení souboru s výpisem paměti.

### <a name="method-2-migrate-using-streaming-the-dump-data-to-the-target-database"></a>Metoda 2: migrace pomocí streamování dat výpisu do cílové databáze

Pokud nemáte klienta PostgreSQL nebo chcete použít Azure Cloud Shell, můžete použít tuto metodu. Výpis databáze je vysílán přímo do cílového databázového serveru a neukládá výpis do tohoto klienta. Proto je možné ji použít s klientem s omezeným úložištěm a dokonce můžete spustit z Azure Cloud Shell. 

1. Ujistěte se, že databáze existuje na cílovém serveru pomocí `\l` příkazu. Pokud databáze neexistuje, vytvořte databázi.
   ```azurecli-interactive
    psql "host=myTargetServer port=5432 dbname=postgres user=myUser password=###### sslmode=mySSLmode"
    ```
    ```SQL
    postgres> \l   
    postgres> create database myTargetDB;
   ```

2. Spuštění výpisu paměti a obnovení jako jednoho příkazového řádku pomocí kanálu. 
    ```azurecli-interactive
    pg_dump -Fc -v --mySourceServer --port=5432 --username=myUser --dbname=mySourceDB | pg_restore -v --no-owner --host=myTargetServer --port=5432 --username=myUser --dbname=myTargetDB
    ```

    Třeba

    ```azurecli-interactive
    pg_dump -Fc -v --host=pg-95.postgres.database.azure.com --port=5432 --username=pg@pg-95 --dbname=bench5gb | pg_restore -v --no-owner --host=pg-11.postgres.database.azure.com --port=5432 --username=pg@pg-11 --dbname=bench5gb
    ```  
3. Po dokončení procesu upgradu (migrace) můžete aplikaci otestovat s cílovým serverem. 
4. Tento postup opakujte pro všechny databáze v rámci serveru.

 V následující tabulce vidíte například čas potřebný k migraci pomocí metody výpisu datového proudu. Ukázková data se naplní pomocí [pgbench](https://www.postgresql.org/docs/10/pgbench.html). Vzhledem k tomu, že vaše databáze může mít různý počet objektů s různou velikostí než pgbench generované tabulky a indexy, důrazně doporučujeme, abyste si vyzkoušeli výpis a obnovení vaší databáze, abyste pochopili skutečný čas potřebný k upgradu databáze. 

| **Velikost databáze** | **Přibližně doba trvání** | 
| ----- | ------ |
| 1 GB  | 1-2 minut |
| 5 GB | 8-10 minut |
| 10 GB | 15-20 minut |
| 50 GB | 1 – 1,5 hodiny |
| 100 GB | 2,5 – 3 hodiny|
   
### <a name="method-3-migrate-using-parallel-dump-and-restore"></a>Metoda 3: migrace pomocí paralelního výpisu a obnovení 

Tuto metodu můžete vzít v úvahu, pokud máte v databázi více větších tabulek a chcete paralelizovat proces výpisu a obnovení pro tuto databázi. V klientském systému budete také potřebovat dostatek úložného prostoru, aby bylo možné pojmout výpisy záloh. Tento paralelní výpis a proces obnovení zkracuje spotřebu času pro dokončení celé migrace. Například databáze 50 GB pgbench, která trvala 1 – 1,5 hodiny na migraci, byla dokončena pomocí metody 1 a 2 trvalo méně než 30 minut pomocí této metody.

1. Pro každou databázi na zdrojovém serveru vytvořte odpovídající databázi na cílovém serveru.

   ```bash
    psql "host=myTargetServer port=5432 dbname=postgres user=myuser password=###### sslmode=mySSLmode"
    postgresl> create database myDB;
   ```
   Třeba
    ```bash
    psql "host=pg-11.postgres.database.azure.com port=5432 dbname=postgres user=pg@pg-11 password=###### sslmode=require"

    postgres> create database bench5gb;
    postgres> \q
    ```

2. Spusťte příkaz pg_dump ve formátu adresáře s počtem úloh = 4 (počet tabulek v databázi). S větší výpočetní úrovní a s více tabulkami můžete zvýšit množství na vyšší číslo. Pg_dump vytvoří adresář pro ukládání komprimovaných souborů pro každou úlohu.

    ```bash
    pg_dump -Fd -v --host=sourceServer --port=5432 --username=myUser --dbname=mySourceDB -j 4 -f myDumpDirectory
    ```
    Třeba
    ```bash
    pg_dump -Fd -v --host=pg-95.postgres.database.azure.com --port=5432 --username=pg@pg-95 --dbname=bench5gb -j 4 -f dump.dir
    ```

3. Pak obnovte zálohu na cílovém serveru.
    ```bash
    $ pg_restore -v --no-owner --host=myTargetServer --port=5432 --username=myUser --dbname=myTargetDB -j 4 myDumpDir
    ```
    Třeba
    ```bash
    $ pg_restore -v --no-owner --host=pg-11.postgres.database.azure.com --port=5432 --username=pg@pg-11 --dbname=bench5gb -j 4 dump.dir
    ```

> [!TIP]
> Postup uvedený v tomto dokumentu je také možné použít k upgradu Azure Database for PostgreSQLho flexibilního serveru, který je ve verzi Preview. Hlavním rozdílem je připojovací řetězec pro cíl flexibilního serveru bez `@dbName` .  Pokud je uživatelské jméno například `pg` , uživatelské jméno jednoho serveru v řetězci připojení bude `pg@pg-95` , zatímco s flexibilním serverem, můžete jednoduše použít `pg` .

## <a name="next-steps"></a>Další kroky

- Až budete s funkcí cílové databáze spokojeni, můžete zrušit původní databázový server. 
- Pokud chcete použít stejný koncový bod databáze jako zdrojový server, pak po odstranění starého zdrojového databázového serveru můžete vytvořit repliku pro čtení se starým názvem databázového serveru. Jakmile je ustálený stav navázán, můžete zastavit repliku, která server repliky povýší na nezávislou Server. Další podrobnosti najdete v tématu [replikace](./concepts-read-replicas.md) .
- Nezapomeňte otestovat a ověřit tyto příkazy v testovacím prostředí předtím, než je použijete v produkčním prostředí.
