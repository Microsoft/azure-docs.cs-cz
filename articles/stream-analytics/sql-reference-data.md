---
title: Použití referenčních dat databáze SQL v úloze Azure Stream Analytics
description: Tento článek popisuje, jak použít databázi SQL jako referenční datový vstup pro úlohu Azure Stream Analytics na webu Azure Portal a ve Visual Studiu.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 01/29/2019
ms.openlocfilehash: aebb590d93b3fb26151f15c176a2941845cdd50c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75426504"
---
# <a name="use-reference-data-from-a-sql-database-for-an-azure-stream-analytics-job"></a>Použití referenčních dat z databáze SQL pro úlohu Azure Stream Analytics

Azure Stream Analytics podporuje Azure SQL Database jako zdroj vstupu pro referenční data. Sql Database můžete použít jako referenční data pro úlohu Stream Analytics na webu Azure Portal a ve Visual Studiu s nástroji Stream Analytics. Tento článek ukazuje, jak provést obě metody.

## <a name="azure-portal"></a>portál Azure

Pomocí následujících kroků přidejte Azure SQL Database jako referenční vstupní zdroj pomocí portálu Azure:

### <a name="portal-prerequisites"></a>Požadavky portálu

1. Vytvoření úlohy Stream Analytics

2. Vytvořte účet úložiště, který bude používat úloha Stream Analytics.

3. Vytvořte si Azure SQL Database s datovou sadou, která se použije jako referenční data úlohy Stream Analytics.

### <a name="define-sql-database-reference-data-input"></a>Definování vstupu referenčních dat databáze SQL

1. V úloze Stream Analytics vyberte **Vstupy v** části **Topologie úlohy**. Klepněte na **tlačítko Přidat odkaz ový vstup** a zvolte **databázi SQL**.

   ![Vstup úlohy Stream Analytics](./media/sql-reference-data/stream-analytics-inputs.png)

2. Vyplňte vstupní konfigurace Analýzy datových proudů. Zvolte název databáze, název serveru, uživatelské jméno a heslo. Pokud chcete, aby se váš vstup referenčních dat pravidelně aktualizoval, zvolte možnost "Zapnuto", chcete-li zadat obnovovací frekvenci v DD:HH:MM. Pokud máte velké sady dat s krátkou obnovovací frekvencí, můžete použít [rozdílový dotaz](sql-reference-data.md#delta-query).

   ![Konfigurace odkazu na databázi SQL](./media/sql-reference-data/sql-input-config.png)

3. Otestujte dotaz na snímek v editoru dotazů SQL. Další informace najdete [v tématu Použití editoru dotazů SQL na webu Azure Portal pro připojení a dotazování na data.](../sql-database/sql-database-connect-query-portal.md)

### <a name="specify-storage-account-in-job-config"></a>Určení účtu úložiště v konfiguraci úlohy

Přejděte na **nastavení účtu úložiště** v části **Konfigurovat** a vyberte Přidat **účet úložiště**.

   ![Nastavení účtu úložiště Stream Analytics](./media/sql-reference-data/storage-account-settings.png)

### <a name="start-the-job"></a>Spuštění úlohy

Jakmile nakonfigurujete další vstupy, výstupy a dotaz, můžete spustit úlohu Stream Analytics.

## <a name="tools-for-visual-studio"></a>Nástroje pro sadu Visual Studio

Pomocí následujících kroků přidejte Azure SQL Database jako referenční vstupní zdroj pomocí Sady Visual Studio:

### <a name="visual-studio-prerequisites"></a>Požadavky sady Visual Studio

1. [Nainstalujte nástroje Stream Analytics pro Visual Studio](stream-analytics-tools-for-visual-studio-install.md). Podporovány jsou následující verze sady Visual Studio:

   * Visual Studio 2015
   * Visual Studio 2019

2. Seznamte se s [nástroji Analýzy streamu pro](stream-analytics-quick-create-vs.md) rychlý start Visual Studia.

3. Vytvoření účtu úložiště

### <a name="create-a-sql-database-table"></a>Vytvoření tabulky databáze SQL

Pomocí aplikace SQL Server Management Studio vytvořte tabulku pro uložení referenčních dat. Podrobnosti najdete v [tématu Návrh první databáze Azure SQL pomocí SSMS.](../sql-database/sql-database-design-first-database.md)

Tabulka příkladů použitá v následujícím příkladu byla vytvořena z následujícího příkazu:

```SQL
create table chemicals(Id Bigint,Name Nvarchar(max),FullName Nvarchar(max));
```

### <a name="choose-your-subscription"></a>Zvolte vaše předplatné.

1. V sadě Visual Studio v nabídce **Zobrazení** vyberte **Průzkumník serveru**.

2. Klikněte pravým tlačítkem na **Azure**, vyberte **Připojit k předplatnému Microsoft Azure**a přihlaste se pomocí svého účtu Azure.

### <a name="create-a-stream-analytics-project"></a>Vytvoření projektu Stream Analytics

1. Vyberte **Soubor > Nový projekt**. 

2. V seznamu šablon na levé straně vyberte **Stream Analytics** a pak vyberte **Aplikace Azure Stream Analytics**. 

3. Zadejte **název**projektu , **umístění**a **název řešení**a vyberte **OK**.

   ![Nový projekt Stream Analytics ve Visual Studiu](./media/sql-reference-data/stream-analytics-vs-new-project.png)

### <a name="define-sql-database-reference-data-input"></a>Definování vstupu referenčních dat databáze SQL

1. Vytvořte nový vstup.

   ![Nový vstup Stream Analytics ve Visual Studiu](./media/sql-reference-data/stream-analytics-vs-input.png)

2. Poklepejte na **soubor Input.json** v **Průzkumníku řešení**.

3. Vyplňte **vstupní konfiguraci analýzy datového proudu**. Zvolte název databáze, název serveru, typ aktualizace a obnovovací frekvenci. Zadejte obnovovací frekvenci `DD:HH:MM`ve formátu .

   ![Konfigurace vstupu Služby Stream Analytics ve Visual Studiu](./media/sql-reference-data/stream-analytics-vs-input-config.png)

   Pokud zvolíte "Spustit pouze jednou" nebo "Spustit pravidelně", jeden soubor SQL CodeBehind s názvem **[Input Alias].snapshot.sql** je generován v projektu pod uzlíkem souboru **Input.json.**

   ![Vstupní kód v sadě Visual Studio](./media/sql-reference-data/once-or-periodically-codebehind.png)

   Pokud zvolíte "Pravidelně aktualizovat pomocí rozdílu", budou vygenerovány dva soubory SQL CodeBehind: **[Input Alias].snapshot.sql** a **[Input Alias].delta.sql**.

   ![Kód v průzkumníku řešení](./media/sql-reference-data/periodically-delta-codebehind.png)

4. Otevřete soubor SQL v editoru a napište dotaz SQL.

5. Pokud používáte Visual Studio 2019 a nainstalovali jste nástroje SQL Server Data, můžete dotaz otestovat klepnutím na tlačítko **Spustit**. Objeví se okno průvodce, které vám pomůže připojit se k databázi SQL, a výsledek dotazu se zobrazí v okně v dolní části.

### <a name="specify-storage-account"></a>Určení účtu úložiště

Otevřete **jobconfig.json** a určete účet úložiště pro ukládání odkazů na SQL snímky.

   ![Konfigurace úloh Stream Analytics ve Visual Studiu](./media/sql-reference-data/stream-analytics-job-config.png)

### <a name="test-locally-and-deploy-to-azure"></a>Testování místně a nasazení do Azure

Před nasazením úlohy do Azure můžete logiku dotazu otestovat místně proti živým vstupním datům. Další informace o této funkci najdete v [tématu Testování živých dat místně pomocí nástrojů Azure Stream Analytics pro Visual Studio (Preview).](stream-analytics-live-data-local-testing.md) Po dokončení testování klikněte na **Odeslat do Azure**. Odkazovat [na Vytvoření Stream Analytics pomocí nástrojů Azure Stream Analytics pro Visual Studio](stream-analytics-quick-create-vs.md) rychlý start se dozvíte, jak spustit úlohu.

## <a name="delta-query"></a>Rozdílový dotaz

Při použití rozdílového dotazu se doporučují [časové tabulky v Azure SQL Database.](../sql-database/sql-database-temporal-tables.md)

1. Vytvořte časovou tabulku v Azure SQL Database.
   
   ```SQL 
      CREATE TABLE DeviceTemporal 
      (  
         [DeviceId] int NOT NULL PRIMARY KEY CLUSTERED 
         , [GroupDeviceId] nvarchar(100) NOT NULL
         , [Description] nvarchar(100) NOT NULL 
         , [ValidFrom] datetime2 (0) GENERATED ALWAYS AS ROW START
         , [ValidTo] datetime2 (0) GENERATED ALWAYS AS ROW END
         , PERIOD FOR SYSTEM_TIME (ValidFrom, ValidTo)
      )  
      WITH (SYSTEM_VERSIONING = ON (HISTORY_TABLE = dbo.DeviceHistory));  -- DeviceHistory table will be used in Delta query
   ```
2. Vytvořte dotaz na snímek. 

   Pomocí parametru ** \@snapshotTime** můžete dát pokyn modulu runtime Stream Analytics k získání sady referenčních dat z časové tabulky databáze SQL platné v systémovédobě. Pokud tento parametr nezadáte, riskujete získání nepřesné sady dat základního odkazu z důvodu zkosení hodin. Příklad úplného dotazu na snímek je uveden níže:
   ```SQL
      SELECT DeviceId, GroupDeviceId, [Description]
      FROM dbo.DeviceTemporal
      FOR SYSTEM_TIME AS OF @snapshotTime
   ```
 
2. Vytvořte rozdílový dotaz. 
   
   Tento dotaz načte všechny řádky v databázi SQL, které byly vloženy nebo odstraněny v rámci počátečního ** \@času, deltaStartTime**a ** \@dioda**koncového času deltaEndTime . Rozdílový dotaz musí vrátit stejné sloupce jako dotaz snímku, stejně jako **_operace_** sloupce . Tento sloupec definuje, zda je řádek vložen nebo odstraněn mezi ** \@deltaStartTime** a ** \@deltaEndTime**. Výsledné řádky jsou označeny jako **1,** pokud byly vloženy záznamy, nebo **2,** pokud byly odstraněny. 

   U záznamů, které byly aktualizovány, provádí časová tabulka účetnictví zachycením operace vložení a odstranění. Za běhu Stream Analytics pak použije výsledky rozdílového dotazu na předchozí snímek, aby byla referenční data aktuální. Příklad rozdílového dotazu je uveden níže:

   ```SQL
      SELECT DeviceId, GroupDeviceId, Description, 1 as _operation_
      FROM dbo.DeviceTemporal
      WHERE ValidFrom BETWEEN @deltaStartTime AND @deltaEndTime   -- records inserted
      UNION
      SELECT DeviceId, GroupDeviceId, Description, 2 as _operation_
      FROM dbo.DeviceHistory   -- table we created in step 1
      WHERE ValidTo BETWEEN @deltaStartTime AND @deltaEndTime     -- record deleted
   ```
 
   Všimněte si, že stream analytics runtime může pravidelně spouštět snímek dotazu kromě rozdílový dotaz pro ukládání kontrolních bodů.

## <a name="test-your-query"></a>Otestování dotazu
   Je důležité ověřit, že váš dotaz vrací očekávanou datovou sadu, kterou bude úloha Stream Analytics používat jako referenční data. Chcete-li otestovat dotaz, přejděte na vstupní část Topologie úlohy na portálu. Potom můžete vybrat ukázková data na vstupu sql database reference. Jakmile bude ukázka k dispozici, můžete soubor stáhnout a zkontrolovat, zda jsou vrácená data podle očekávání. Pokud chcete optimalizovat vývoj a testovat iterace, doporučujeme použít [nástroje Stream Analytics pro Visual Studio](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-tools-for-visual-studio-install). Můžete také libovolný jiný nástroj vašeho předvolby nejprve zajistit, že dotaz vrací správné výsledky z azure SQL database a pak použít v úloze Stream Analytics. 

## <a name="faqs"></a>Nejčastější dotazy

**Budou mi účtovány další náklady pomocí vstupu referenčních dat SQL v Azure Stream Analytics?**

V úloze Stream Analytics nejsou žádné další [náklady na jednotku streamování.](https://azure.microsoft.com/pricing/details/stream-analytics/) Úloha Stream Analytics však musí mít přidružený účet úložiště Azure. Úloha Stream Analytics se dotazuje na SQL DB (během intervalu spuštění úlohy a aktualizace) k načtení sady referenčních dat a uloží tento snímek do účtu úložiště. Ukládání těchto snímků bude mít za následek další poplatky podrobně popsané na [stránce s cenami](https://azure.microsoft.com/pricing/details/storage/) pro účet úložiště Azure.

**Jak poznám, že se na snímek referenčních dat dotazuje z databáze SQL a používá se v úloze Azure Stream Analytics?**

Existují dvě metriky filtrované podle logického názvu (v části Metriky Azure Portal), které můžete použít ke sledování stavu vstupu referenčních dat databáze SQL.

   * InputEvents: Tato metrika měří počet záznamů načtených ze sady referenčních dat databáze SQL.
   * InputEventBytes: Tato metrika měří velikost snímku referenčních dat načtených v paměti úlohy Stream Analytics. 

Kombinace obou těchto metrik lze odvodit, pokud úloha je dotazování databáze SQL načíst sadu referenčních dat a načtení do paměti.

**Budu potřebovat speciální typ Azure SQL Database?**

Azure Stream Analytics bude fungovat s jakýmkoli typem Azure SQL Database. Je však důležité si uvědomit, že obnovovací frekvence nastavená pro vstup referenčních dat může ovlivnit zatížení dotazu. Chcete-li použít možnost rozdílového dotazu, doporučujeme použít časové tabulky v Azure SQL Database.

**Proč Azure Stream Analytics ukládá snímky do účtu Azure Storage?**

Stream Analytics zaručuje právě jedno zpracování událostí a minimálně jedno doručení událostí. V případech, kdy přechodné problémy ovlivňují vaši úlohu, je k obnovení stavu nutné malé množství přehrání. Chcete-li povolit přehrání, je nutné mít tyto snímky uložené v účtu azure úložiště. Další informace o přehrání kontrolního bodu najdete v [tématu Checkpoint a replay koncepty v azure stream analytics úlohy](stream-analytics-concepts-checkpoint-replay.md).

## <a name="next-steps"></a>Další kroky

* [Použití referenčních dat pro vyhledávání v Stream Analytics](stream-analytics-use-reference-data.md)
* [Rychlý start: Vytvoření úlohy Stream Analytics pomocí Azure Stream Analytics Tools for Visual Studio](stream-analytics-quick-create-vs.md)
* [Testování živých dat místně pomocí nástrojů Azure Stream Analytics pro Visual Studio (Preview)](stream-analytics-live-data-local-testing.md)
