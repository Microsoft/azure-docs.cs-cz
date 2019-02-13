---
title: Využití referenčních dat z databáze serveru SQL pro úlohy Azure Stream Analytics (Preview)
description: Tento článek popisuje, jak používat SQL Database jako referenčního datového vstupu pro úlohy Azure Stream Analytics na portálu Azure portal a v sadě Visual Studio.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 01/29/2019
ms.openlocfilehash: 79f0e58ea11d8bdb8c30ca1e50fae2635f719681
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/12/2019
ms.locfileid: "56118016"
---
# <a name="use-reference-data-from-a-sql-database-for-an-azure-stream-analytics-job-preview"></a>Využití referenčních dat z databáze serveru SQL pro úlohy Azure Stream Analytics (Preview)

Azure Stream Analytics podporuje Azure SQL Database jako zdroj vstupu pro referenční data. SQL Database můžete použít jako referenční data pro vaši úlohu Stream Analytics na portálu Azure portal a v sadě Visual Studio pomocí nástroje Stream Analytics. Tento článek ukazuje, jak provést obě metody.

## <a name="azure-portal"></a>portál Azure

Chcete-li přidat Azure SQL Database jako vstupní zdroj odkazu pomocí webu Azure portal postupujte následovně:

### <a name="portal-prerequisites"></a>Požadavky na portálu

1. Vytvoření úlohy Stream Analytics.

2. Vytvoření účtu úložiště pro úlohy Stream Analytics.

3. Vytvoření Azure SQL Database s datovou sadou použije jako referenční data úlohy Stream Analytics.

### <a name="define-sql-database-reference-data-input"></a>Definování databázi SQL referenčního datového vstupu

1. V úloze Stream Analytics, vyberte **vstupy** pod **topologie úlohy**. Klikněte na tlačítko **přidat odkaz na vstup** a zvolte **SQL Database**.

   ![Vstup úlohy Stream Analytics](./media/sql-reference-data/stream-analytics-inputs.png)

2. Vyplňte konfigurace vstupní Stream Analytics. Vyberte název databáze, název serveru, uživatelské jméno a heslo. Pokud chcete data referenční vstup do pravidelně aktualizují, zvolte "On" a zadejte obnovovací frekvence v DD:HH:MM. Pokud máte velké sady dat s krátkou obnovovací frekvence, můžete použít [rozdílového dotazu](sql-reference-data.md#delta-query).

   ![Konfigurace referenčního SQL Database](./media/sql-reference-data/sql-input-config.png)

3. Otestujte dotaz snímku v editoru dotazů SQL. Další informace najdete v tématu [připojení a dotazování dat pomocí editoru dotazů SQL webu Azure portal](../sql-database/sql-database-connect-query-portal.md)

### <a name="specify-storage-account-in-job-config"></a>Zadejte účet úložiště v konfiguraci úlohy

Přejděte do **nastavení účtu úložiště** pod **konfigurovat** a vyberte **přidání účtu úložiště**.

   ![Nastavení účtu úložiště Stream Analytics](./media/sql-reference-data/storage-account-settings.png)

### <a name="start-the-job"></a>Spuštění úlohy

Až nakonfigurujete další vstupy, výstupy a dotaz, můžete spustit úlohu Stream Analytics.

## <a name="tools-for-visual-studio"></a>Nástroje pro sadu Visual Studio

Pomocí následujících kroků můžete přidat Azure SQL Database jako vstupní zdroj odkazu pomocí sady Visual Studio:

### <a name="visual-studio-prerequisites"></a>Požadavky na Visual Studio

1. Pokud používáte Visual Studio 2017, aktualizujte na 15.8.2 nebo vyšší. Všimněte si, že 16.0 a vyšší nejsou podporovány v tomto okamžiku.

2. [Nainstalujte nástroje Stream Analytics pro Visual Studio](stream-analytics-tools-for-visual-studio-install.md). Jsou podporovány následující verze sady Visual Studio:

   * Visual Studio 2015
   * Visual Studio 2017

3. Seznamte se s [nástroje Stream Analytics pro Visual Studio](stream-analytics-quick-create-vs.md) rychlý start.

4. Vytvoření účtu úložiště

### <a name="create-a-sql-database-table"></a>Vytvořit tabulky databáze SQL

Vytvořte tabulku pro ukládání referenčních dat pomocí SQL Server Management Studio. Zobrazit [návrh první databáze Azure SQL pomocí SSMS](../sql-database/sql-database-design-first-database.md) podrobnosti.

Příklad tabulka použitá v následujícím příkladu byl vytvořen z následujícího příkazu:

```SQL
create table chemicals(Id Bigint,Name Nvarchar(max),FullName Nvarchar(max));
```

### <a name="choose-your-subscription"></a>Zvolte vaše předplatné.

1. V sadě Visual Studio v nabídce **Zobrazení** vyberte **Průzkumník serveru**.

2. Klikněte pravým tlačítkem na **Azure**vyberte **připojit k předplatnému Microsoft Azure**a přihlaste se pomocí svého účtu Azure.

### <a name="create-a-stream-analytics-project"></a>Vytvoření projektu Stream Analytics

1. Vyberte **Soubor > Nový projekt**. 

2. V seznamu šablon na levé straně vyberte **Stream Analytics** a pak vyberte **Aplikace Azure Stream Analytics**. 

3. Zadejte projekt **název**, **umístění**, a **název řešení**a vyberte **OK**.

   ![Nový projekt Stream Analytics v sadě Visual Studio](./media/sql-reference-data/stream-analytics-vs-new-project.png)

### <a name="define-sql-database-reference-data-input"></a>Definování databázi SQL referenčního datového vstupu

1. Vytvořte nový vstup.

   ![Nový vstup Stream Analytics v sadě Visual Studio](./media/sql-reference-data/stream-analytics-vs-input.png)

2. Dvakrát klikněte na panel **Input.JSON vypadá** v **Průzkumníka řešení**.

3. Vyplňte **konfigurace vstupní Stream Analytics**. Vyberte název databáze, název serveru, typ obnovení a obnovovací frekvence. Zadejte interval ve formátu `DD:HH:MM`.

   ![Konfigurace vstupu Stream Analytics v sadě Visual Studio](./media/sql-reference-data/stream-analytics-vs-input-config.png)

   Pokud vyberete možnost "Spustit jenom jednou" nebo "Spustit pravidelně", jeden soubor SQL CodeBehind s názvem **.snapshot.sql [vstupní Alias]** generováno v projektu **Input.json** uzel souboru.

   ![Vstupní kódu na pozadí v sadě Visual Studio](./media/sql-reference-data/once-or-periodically-codebehind.png)

   Pokud vyberete možnost "Aktualizovat pravidelně s rozdílovou", budou generovány dva soubory SQL CodeBehind: **[vstupní Alias].snapshot.sql** a **[vstupní Alias].delta.sql**.

   ![Kódu na pozadí v Průzkumníku řešení](./media/sql-reference-data/periodically-delta-codebehind.png)

4. Otevřete soubor SQL v editoru a napsat dotaz SQL.

5. Pokud používáte Visual Studio 2017 a jste nainstalovali SQL Server Data tools, můžete otestovat dotaz kliknutím **Execute**. Průvodce okno objeví se při připojení k SQL database a výsledek dotazu se zobrazí v okně v dolní části.

### <a name="specify-storage-account"></a>Zadejte účet služby storage

Otevřít **JobConfig.json** zadat účet úložiště pro ukládání snímků odkaz SQL.

   ![Konfigurace úlohy Stream Analytics v sadě Visual Studio](./media/sql-reference-data/stream-analytics-job-config.png)

### <a name="test-locally-and-deploy-to-azure"></a>Místně ji otestovat a nasadit do Azure

Před nasazením úlohy do Azure, můžete otestovat logiku dotazu místně pro živá vstupní data. Další informace o této funkci najdete v tématu [testování živých dat místně pomocí nástroje Azure Stream Analytics pro Visual Studio (Preview)](stream-analytics-live-data-local-testing.md). Po dokončení testování, klikněte na tlačítko **odeslat do Azure**. Odkaz [vytvořit Stream Analytics pomocí Azure Stream Analytics tools for Visual Studio](stream-analytics-quick-create-vs.md) rychlém startu se naučíte spustit úlohu.

## <a name="delta-query"></a>Dotaz delta

Při použití rozdílového dotazu [dočasných tabulek ve službě Azure SQL Database](../sql-database/sql-database-temporal-tables.md) doporučují.

1. Vytvoření snímku dotazu. 

   Použití **@snapshotTime** parametr dáte pokyn, aby modul runtime Stream Analytics získat referenční sady dat z SQL database dočasnou tabulku platnou v době systému. Pokud tento parametr nezadáte, riskujete získání nesprávné základní referenční sady dat kvůli časovým nepřesnostem. Níže je uveden příklad dotazu úplné snímku:

   ![Stream Analytics snímku dotazu](./media/sql-reference-data/snapshot-query.png)
 
2. Autor rozdílového dotazu. 
   
   Tento dotaz načte všechny řádky v databázi SQL, které bylo vloženo nebo odstraněno v době spuštění **@deltaStartTime**a koncový čas **@deltaEndTime**. Rozdílový dotaz musí vracet stejné sloupce jako snímek dotazu, stejně jako sloupec  **_operace_**. V tomto sloupci definuje, jestli se řádek je vloženo nebo odstraněno mezi **@deltaStartTime** a **@deltaEndTime**. Výsledné řádky se označí jako **1** Pokud záznamy byly vloženy, nebo **2** Pokud odstraněn. 

   Pro záznamy, které byly aktualizovány dočasnou tabulku se účetnictví zachytáváním operace vložení a odstranění. Modul runtime Stream Analytics potom použije výsledky rozdílového dotazu s předchozím snímkem k udržení referenční data. Příkladem rozdílového dotazu se zobrazuje níže:

   ![Stream Analytics rozdílového dotazu](./media/sql-reference-data/delta-query.png)
 
  Všimněte si, že modul runtime Stream Analytics může pravidelně spuštění dotazu snímku kromě rozdílového dotazu k uložení kontrolní body.

## <a name="faqs"></a>Nejčastější dotazy

**Budou účtovat dodatečné náklady s využitím SQL referenčního datového vstupu v Azure Stream Analytics?**

Neexistují žádné další [cena za jednotky streamování](https://azure.microsoft.com/pricing/details/stream-analytics/) v úloze Stream Analytics. Úlohy Stream Analytics ale musíte mít účet přidruženého úložiště Azure. Úlohy Stream Analytics dotazuje databázi SQL (během úlohy spuštění a interval aktualizace) a získejte referenční sady dat a úložiště, která snímku v účtu úložiště. Ukládání tyto snímky budou účtovat další poplatky, které jsou podrobně popsané v [stránce s cenami](https://azure.microsoft.com/pricing/details/storage/) pro účet úložiště Azure.

**Jak zjistím, referenční data snímku se z databáze SQL dotazovat a používat v úloze Azure Stream Analytics?**

Nejsou nakonfigurovaná metriky filtrovat podle logického názvu (v rámci webu Azure Portal metriky), který můžete použít tak, aby monitorovala referenčních dat databáze SQL vstup.

   * Situací: Tato metrika měří počet načtených v SQL database referenční datové sady záznamů.
   * InputEventBytes: Tato metrika měří velikost snímku referenční data načtena do paměti úlohy Stream Analytics. 

Kombinací obou tyto metriky můžete použít k odvození Pokud úloha dotazování databáze SQL k načtení referenční sady dat a potom načtením do paměti.

**Bude vyžadovat speciální typ služby Azure SQL Database?**

Azure Stream Analytics bude fungovat s jakýmkoli typem služby Azure SQL Database. Je důležité pochopit, že obnovovací frekvence pro vaše referenčního datového vstupu může mít vliv na zatížení dotazu. Možnost dotazu rozdílů, se doporučuje použít dočasných tabulek ve službě Azure SQL Database.

**Vzorkovat vstup z referenčních dat SQL Database vstup**

Tato funkce není dostupná.

**Proč Azure Stream Analytics ukládá snímky v účtu Azure Storage?**

Stream Analytics zaručuje právě jedno zpracování událostí a minimálně jedno doručení událostí. V případech, kde přechodná vydá dopad úlohy je potřeba obnovit stav malé množství opakování. Povolit opakování, je potřeba mít tyto snímky se ukládají v účtu služby Azure Storage. Další informace o přehrání kontrolní bod, najdete v části [kontrolní bod a opětovného přehrání koncepty v úlohách Azure Stream Analytics](stream-analytics-concepts-checkpoint-replay.md).

## <a name="next-steps"></a>Další postup

* [Pomocí referenčních dat pro vyhledávání ve službě Stream Analytics](stream-analytics-use-reference-data.md)
* [Rychlé zprovoznění: Vytvoření úlohy Stream Analytics pomocí Azure Stream Analytics tools for Visual Studio](stream-analytics-quick-create-vs.md)
* [Živá data test místně pomocí nástroje Azure Stream Analytics pro Visual Studio (Preview)](stream-analytics-live-data-local-testing.md)
