---
title: Použití SQL Database referenčních dat v úloze Azure Stream Analytics
description: Tento článek popisuje, jak použít SQL Database jako referenční datový vstup pro Azure Stream Analytics úlohu v Azure Portal a v aplikaci Visual Studio.
author: enkrumah
ms.author: ebnkruma
ms.service: stream-analytics
ms.topic: how-to
ms.date: 01/29/2019
ms.openlocfilehash: e7d16de8a7a5c6f5353d64e25580b19845ce96c1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "98016401"
---
# <a name="use-reference-data-from-a-sql-database-for-an-azure-stream-analytics-job"></a>Použití referenčních dat z SQL Database pro úlohu Azure Stream Analytics

Azure Stream Analytics podporuje Azure SQL Database jako zdroj vstupu pro referenční data. Můžete použít SQL Database jako referenční data pro úlohu Stream Analytics v Azure Portal a v aplikaci Visual Studio s nástroji pro Stream Analytics. Tento článek ukazuje, jak provést obě metody.

## <a name="azure-portal"></a>portál Azure

Pomocí následujících kroků přidejte Azure SQL Database jako vstupní zdroj odkazů pomocí Azure Portal:

### <a name="portal-prerequisites"></a>Předpoklady pro portál

1. Vytvoření úlohy Stream Analytics

2. Vytvořte účet úložiště, který bude Stream Analytics úloha používat.

3. Vytvořte svůj Azure SQL Database s datovou sadou, která má být použita jako referenční data, úlohou Stream Analytics.

### <a name="define-sql-database-reference-data-input"></a>Definování datového vstupu SQL Database referenčních dat

1. V Stream Analytics úlohy vyberte **vstupy** v části **topologie úloh**. Klikněte na **Přidat vstup odkazu** a vyberte **SQL Database**.

   ![Vstupy jsou vybrány v levém navigačním podokně. Na vstupech je vybráno + přidat vstup odkazů, což odhalí rozevírací seznam, který zobrazuje hodnoty BLOB Storage a SQL Database.](./media/sql-reference-data/stream-analytics-inputs.png)

2. Doplňte Stream Analytics vstupních konfigurací. Vyberte název databáze, název serveru, uživatelské jméno a heslo. Pokud chcete, aby se váš referenční datový vstup pravidelně aktualizoval, zvolte možnost Zapnuto a zadejte obnovovací frekvenci v DD: HH: MM. Pokud máte velké sady dat se zkrácenou frekvencí obnovení, můžete použít [rozdílový dotaz](sql-reference-data.md#delta-query).

   ![Pokud je vybrána možnost SQL Database, zobrazí se SQL Database nová vstupní stránka. V levém podokně je formulář konfigurace a dotaz na snímek v pravém podokně.](./media/sql-reference-data/sql-input-config.png)

3. Otestujte dotaz na snímek v editoru dotazů SQL. Další informace najdete v tématu [použití Editoru dotazů SQL Azure Portal k připojení a dotazování dat](../azure-sql/database/connect-query-portal.md) .

### <a name="specify-storage-account-in-job-config"></a>Zadat účet úložiště v konfiguraci úlohy

V části **Konfigurace** a vyberte **Přidat účet úložiště** přejděte do **Nastavení účtu úložiště** .

   ![V levém podokně se vybere nastavení účtu úložiště. V pravém podokně se nachází tlačítko Přidat účet úložiště.](./media/sql-reference-data/storage-account-settings.png)

### <a name="start-the-job"></a>Spuštění úlohy

Jakmile nakonfigurujete jiné vstupy, výstupy a dotazy, můžete spustit úlohu Stream Analytics.

## <a name="tools-for-visual-studio"></a>Nástroje pro sadu Visual Studio

Pomocí následujících kroků přidejte Azure SQL Database jako vstupní zdroj odkazů pomocí sady Visual Studio:

### <a name="visual-studio-prerequisites"></a>Požadavky sady Visual Studio

1. [Nainstalujte nástroje Stream Analytics Tools for Visual Studio](stream-analytics-tools-for-visual-studio-install.md). Podporují se tyto verze sady Visual Studio:

   * Visual Studio 2015
   * Visual Studio 2019

2. Seznamte se s [nástroji pro Stream Analytics pro](stream-analytics-quick-create-vs.md) rychlé zprovoznění sady Visual Studio.

3. Vytvoření účtu úložiště

### <a name="create-a-sql-database-table"></a>Vytvoření tabulky SQL Database

Pomocí SQL Server Management Studio můžete vytvořit tabulku, do které se budou ukládat referenční data. Podrobnosti najdete v tématu [Návrh první Azure SQL Database s využitím SSMS](../azure-sql/database/design-first-database-tutorial.md) .

Ukázková tabulka použitá v následujícím příkladu se vytvořila z následujícího příkazu:

```SQL
create table chemicals(Id Bigint,Name Nvarchar(max),FullName Nvarchar(max));
```

### <a name="choose-your-subscription"></a>Zvolte vaše předplatné.

1. V sadě Visual Studio v nabídce **Zobrazení** vyberte **Průzkumník serveru**.

2. Klikněte pravým tlačítkem na **Azure**, vyberte **připojit k předplatnému Microsoft Azure** a přihlaste se pomocí svého účtu Azure.

### <a name="create-a-stream-analytics-project"></a>Vytvoření projektu Stream Analytics

1. Vyberte **Soubor > Nový projekt**. 

2. V seznamu šablon na levé straně vyberte **Stream Analytics** a pak vyberte **Aplikace Azure Stream Analytics**. 

3. Zadejte **název** projektu, **umístění** a **název řešení** a vyberte **OK**.

   ![Je vybrána šablona Stream Analytics, je vybrána možnost Azure Stream Analytics aplikace a jsou zvýrazněna pole název, umístění a názvy řešení.](./media/sql-reference-data/stream-analytics-vs-new-project.png)

### <a name="define-sql-database-reference-data-input"></a>Definování datového vstupu SQL Database referenčních dat

1. Vytvoří nový vstup.

   ![V nabídce Přidat novou položku je vybrán vstup.](./media/sql-reference-data/stream-analytics-vs-input.png)

2. Dvakrát klikněte na **Input.js** v **Průzkumník řešení**.

3. Vyplňte **konfiguraci Stream Analyticsho vstupu**. Vyberte název databáze, název serveru, typ aktualizace a obnovovací frekvenci. Zadejte obnovovací frekvenci ve formátu `DD:HH:MM` .

   ![V Stream Analytics konfiguraci vstupu jsou hodnoty zadány nebo vybrány z rozevíracích seznamů.](./media/sql-reference-data/stream-analytics-vs-input-config.png)

   Pokud zvolíte možnost spustit pouze jednou nebo spustit pravidelně, jeden soubor CodeBehind SQL s názvem **[vstupní alias]. Snapshot. SQL** se vygeneruje v projektu pod **Input.jsv** uzlu File (soubor).

   ![Zvýrazní se soubor SQL CodeBehind rechemikálie. Snapshot. SQL.](./media/sql-reference-data/once-or-periodically-codebehind.png)

   Pokud zvolíte možnost pravidelně aktualizovat s rozdílem, budou se generovat dva soubory CodeBehind SQL: **[Input alias]. Snapshot. SQL** a **[Input alias]. Delta. SQL**.

   ![Jsou zvýrazněny soubory CodeBehind. Delta. SQL a chemikálie. Snapshot. SQL.](./media/sql-reference-data/periodically-delta-codebehind.png)

4. Otevřete soubor SQL v editoru a napište dotaz SQL.

5. Pokud používáte Visual Studio 2019 a máte nainstalované nástroje SQL Server Data Tools, můžete otestovat dotaz kliknutím na **Spustit**. Automaticky se otevře okno průvodce, které vám pomůže se připojit k SQL Database a výsledek dotazu se zobrazí v dolní části okna.

### <a name="specify-storage-account"></a>Zadat účet úložiště

Otevřete **JobConfig.jsv** a určete účet úložiště pro ukládání snímků odkazů SQL.

   ![Konfigurace úlohy Stream Analytics se zobrazuje s výchozími hodnotami. Nastavení globálního úložiště se zvýrazní.](./media/sql-reference-data/stream-analytics-job-config.png)

### <a name="test-locally-and-deploy-to-azure"></a>Místní test a nasazení do Azure

Před nasazením úlohy do Azure můžete testovat logiku dotazu místně proti živým vstupním datům. Další informace o této funkci naleznete v tématu [test Live data místně pomocí Azure Stream Analyticsch nástrojů pro Visual Studio (Preview)](stream-analytics-live-data-local-testing.md). Až budete s testováním hotovi, klikněte na **Odeslat do Azure**. Pokud se chcete dozvědět, jak spustit úlohu, přečtěte si článek [vytvoření Stream Analytics pomocí Azure Stream Analytics nástrojů pro Visual Studio](stream-analytics-quick-create-vs.md) Starter.

## <a name="delta-query"></a>Rozdílový dotaz

Při použití rozdílového dotazu jsou doporučeny [dočasné tabulky v Azure SQL Database](../azure-sql/temporal-tables.md) .

1. Vytvořte dočasnou tabulku v Azure SQL Database.
   
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
2. Vytvořte snímek dotazu. 

   Pomocí parametru **\@ snapshotTime** instruujte modul runtime Stream Analytics, aby získal referenční datovou sadu z SQL Database dočasná tabulka platná v systémovém čase. Pokud tento parametr nezadáte, riskujete získat nepřesnou datovou sadu základních referenčních dat z důvodu zešikmení hodin. Příklad úplného dotazu na snímek je uveden níže:
   ```SQL
      SELECT DeviceId, GroupDeviceId, [Description]
      FROM dbo.DeviceTemporal
      FOR SYSTEM_TIME AS OF @snapshotTime
   ```
 
2. Vytvořte rozdílový dotaz. 
   
   Tento dotaz načte všechny řádky ve SQL Database, které byly vloženy nebo odstraněny v čase zahájení, v **\@ deltaStartTime** a čase ukončení **\@ deltaEndTime**. Rozdílový dotaz musí vracet stejné sloupce jako dotaz snímku a také **_operaci_** sloupce. Tento sloupec definuje, jestli se řádek vloží nebo odstraní mezi **\@ deltaStartTime** a **\@ deltaEndTime**. Výsledné řádky jsou označeny jako **1** , pokud byly záznamy vloženy, nebo **2** , pokud byly odstraněny. Dotaz musí také přidat **vodoznak** ze strany SQL Server, aby bylo zajištěno, že všechny aktualizace v období rozdílů jsou správně zachyceny. Použití rozdílového dotazu bez **meze** může mít za následek nesprávnou datovou sadu reference.  

   U záznamů, které byly aktualizovány, dočasná tabulka provádí účetnictví zachycením operace vložení a odstranění. Modul runtime Stream Analytics pak použije výsledky rozdílového dotazu na předchozí snímek, aby byla referenční data v aktuálním stavu. Příkladem rozdílového dotazu je znázorněno níže:

   ```SQL
      SELECT DeviceId, GroupDeviceId, Description, ValidFrom as _watermark_, 1 as _operation_
      FROM dbo.DeviceTemporal
      WHERE ValidFrom BETWEEN @deltaStartTime AND @deltaEndTime   -- records inserted
      UNION
      SELECT DeviceId, GroupDeviceId, Description, ValidTo as _watermark_, 2 as _operation_
      FROM dbo.DeviceHistory   -- table we created in step 1
      WHERE ValidTo BETWEEN @deltaStartTime AND @deltaEndTime     -- record deleted
   ```
 
   Všimněte si, že modul runtime Stream Analytics může pravidelně spouštět dotaz na snímky společně s rozdílovým dotazem na ukládání kontrolních bodů.

## <a name="test-your-query"></a>Otestování dotazu
   Je důležité ověřit, že váš dotaz vrací očekávanou datovou sadu, kterou bude Stream Analytics úloha používat jako referenční data. Dotaz otestujete tak, že v části topologie úloh na portálu přejdete na vstup. Pak můžete vybrat ukázková data na SQL Database odkazem na vstup. Jakmile bude ukázka k dispozici, můžete stáhnout soubor a ověřit, zda jsou vracená data podle očekávání. Pokud chcete optimalizovat vaše iterace vývoje a testování, doporučuje se používat [nástroje Stream Analytics pro Visual Studio](./stream-analytics-tools-for-visual-studio-install.md). Můžete také použít jakýkoli jiný nástroj vaší předvolby, abyste nejdřív zajistili, že dotaz vrací správné výsledky od vás Azure SQL Database a pak ho použijete v úloze Stream Analytics. 

### <a name="test-your-query-with-visual-studio-code"></a>Otestujte dotaz pomocí Visual Studio Code

   Nainstalujte [Azure Stream Analytics nástroje](https://marketplace.visualstudio.com/items?itemName=ms-bigdatatools.vscode-asa) a [SQL Server (mssql)](https://marketplace.visualstudio.com/items?itemName=ms-mssql.mssql) na Visual Studio Code a nastavte svůj projekt ASA. Další informace najdete v tématu [rychlý Start: vytvoření úlohy Azure Stream Analytics v Visual Studio Code](./quick-create-visual-studio-code.md) a v [kurzu rozšíření SQL Server (MSSQL)](/sql/tools/visual-studio-code/sql-server-develop-use-vscode).

1. Nakonfigurujte zadání referenčních dat SQL.
   
   ![Editor Visual Studio Code (karta) zobrazuje ReferenceSQLDatabase.js.](./media/sql-reference-data/configure-sql-reference-data-input.png)

2. Vyberte ikonu SQL Server a klikněte na **Přidat připojení**.
   
   ![V levém podokně se zobrazí + přidat připojení a zvýrazní se.](./media/sql-reference-data/add-sql-connection.png)

3. Vyplňte informace o připojení.
   
   ![Jsou zvýrazněna dvě pole pro informace o databázi a serveru.](./media/sql-reference-data/fill-connection-information.png)

4. Klikněte pravým tlačítkem na odkaz SQL a vyberte **Spustit dotaz**.
   
   ![Příkaz Spustit dotaz je v místní nabídce zvýrazněný.](./media/sql-reference-data/execute-query.png)

5. Vyberte připojení.
   
   ![V dialogovém okně se zobrazí zpráva "vytvořit profil připojení ze seznamu níže" a seznam obsahuje jednu položku, která je zvýrazněnou.](./media/sql-reference-data/choose-connection.png)

6. Zkontrolujte a ověřte výsledek dotazu.
   
   ![Výsledky hledání dotazů jsou na kartě Editor VS Code.](./media/sql-reference-data/verify-result.png)


## <a name="faqs"></a>Nejčastější dotazy

**Účtují se vám dodatečné náklady pomocí vstupu SQL referenčních dat v Azure Stream Analytics?**

V úloze Stream Analytics se neúčtují žádné další [náklady na jednotku streamování](https://azure.microsoft.com/pricing/details/stream-analytics/) . Úloha Stream Analytics ale musí mít přidružený účet úložiště Azure. Úloha Stream Analytics se dotazuje databáze SQL (při zahájení úlohy a intervalu aktualizace), aby získala referenční datovou sadu a ukládá tento snímek do účtu úložiště. Uložení těchto snímků bude mít za následek detailní podrobnější poplatky na [stránce s cenami](https://azure.microsoft.com/pricing/details/storage/) pro účet služby Azure Storage.

**Návody informace o snímku referenčních dat se dotazuje z databáze SQL a používá se v úloze Azure Stream Analytics?**

Existují dvě metriky filtrované podle logického názvu (v rámci metrik Azure Portal), které můžete použít k monitorování stavu SQL Database referenčních dat.

   * InputEvents: Tato metrika měří počet záznamů načtených z SQL Database referenční datovou sadou.
   * InputEventBytes: Tato metrika měří velikost snímku referenčních dat načteného v paměti Stream Analytics úlohy. 

Kombinaci obou těchto metrik lze použít k odvození, pokud se úloha dotazuje SQL Database načíst referenční datovou sadu a následně ji načíst do paměti.

**Vyžaduje se zvláštní typ Azure SQL Database?**

Azure Stream Analytics bude fungovat s jakýmkoli typem Azure SQL Database. Je ale důležité si uvědomit, že nastavená obnovovací frekvence pro váš vstupní datový vstup by mohla mít vliv na vaše zatížení dotazu. Chcete-li použít možnost rozdílového dotazu, doporučuje se použít dočasné tabulky v Azure SQL Database.

**Proč Azure Stream Analytics ukládají snímky v Azure Storagem účtu?**

Stream Analytics zaručuje právě jedno zpracování událostí a minimálně jedno doručení událostí. V případech, kdy problémy s přechodnou úlohou ovlivňují, je pro stav obnovení zapotřebí malé množství opakování. Chcete-li povolit opakované přehrání, je nutné, aby tyto snímky byly uloženy v účtu Azure Storage. Další informace o opětovném přehrání kontrolního bodu najdete [v tématu Koncepty a přehrání konceptů v Azure Stream Analytics úlohách](stream-analytics-concepts-checkpoint-replay.md).

## <a name="next-steps"></a>Další kroky

* [Použití referenčních dat pro vyhledávání v Stream Analytics](stream-analytics-use-reference-data.md)
* [Rychlý start: Vytvoření úlohy Stream Analytics pomocí Azure Stream Analytics Tools for Visual Studio](stream-analytics-quick-create-vs.md)
* [Místní testování živých dat pomocí Azure Stream Analyticsch nástrojů pro Visual Studio (Preview)](stream-analytics-live-data-local-testing.md)