---
title: Plánování úloh U-SQL Azure Data Lake Analytics pomocí SSIS
description: Naučte se používat služba SSIS (SQL Server Integration Services) k plánování úloh U-SQL pomocí vloženého skriptu nebo souborů dotazů U-SQL.
ms.reviewer: jasonh
ms.service: data-lake-analytics
ms.topic: how-to
ms.date: 07/17/2018
ms.openlocfilehash: b080b433f5af49e970faba02003fb68e21a08365
ms.sourcegitcommit: 8d8deb9a406165de5050522681b782fb2917762d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/20/2020
ms.locfileid: "92221447"
---
# <a name="schedule-u-sql-jobs-using-sql-server-integration-services-ssis"></a>Plánování úloh U-SQL pomocí služba SSIS (SQL Server Integration Services) (SSIS)

V tomto dokumentu se dozvíte, jak orchestrovat a vytvářet úlohy U-SQL pomocí služby SQL Server Integration Service (SSIS). 

## <a name="prerequisites"></a>Předpoklady

[Azure Feature Pack pro integrační služby](/sql/integration-services/azure-feature-pack-for-integration-services-ssis#scenario-managing-data-in-the-cloud) poskytuje [úlohu Azure Data Lake Analytics](/sql/integration-services/control-flow/azure-data-lake-analytics-task) a [Správce připojení Azure Data Lake Analytics](/sql/integration-services/connection-manager/azure-data-lake-analytics-connection-manager) , který pomáhá připojit se ke službě Azure Data Lake Analytics. Chcete-li použít tuto úlohu, nezapomeňte nainstalovat:

- [Stažení a instalace nástrojů SQL Server Data Tools (SSDT) pro Visual Studio](/sql/ssdt/download-sql-server-data-tools-ssdt)
- [Nainstalovat Azure Feature Pack pro integrační služby (SSIS)](/sql/integration-services/azure-feature-pack-for-integration-services-ssis)

## <a name="azure-data-lake-analytics-task"></a>Azure Data Lake Analytics úkol

Úkol Azure Data Lake Analytics umožní uživatelům odesílat úlohy U-SQL do účtu Azure Data Lake Analytics. 

[Přečtěte si, jak nakonfigurovat úlohu Azure Data Lake Analytics](/sql/integration-services/control-flow/azure-data-lake-analytics-task).

![Azure Data Lake Analytics úkol v SSIS](./media/data-lake-analytics-schedule-jobs-ssis/data-lake-analytics-azure-data-lake-analytics-task-in-ssis.png)

Skript U-SQL můžete získat z různých míst pomocí integrovaných funkcí a úloh SSIS. pod scénáře ukazují, jak můžete nakonfigurovat skripty U-SQL pro různé uživatelské případy.

## <a name="scenario-1-use-inline-script-call-tvfs-and-stored-procs"></a>Scénář 1 – použití vloženého skriptového volání TVF a uložených procesů

V editoru úloh Azure Data Lake Analytics nakonfigurujte typ **sourceType** jako **DirectInput**a vložte příkazy U-SQL do **USQLStatement**.

V případě snadné údržby a správy kódu vložte pouze krátký skript U-SQL jako vložené skripty, například můžete volat existující funkce vracející tabulku a uložené procedury v databázích U-SQL. 

![Úprava vloženého skriptu U-SQL v úloze SSIS](./media/data-lake-analytics-schedule-jobs-ssis/edit-inline-usql-script-in-ssis.png)

Související článek: [jak předat parametr uloženým procedurám](#scenario-6-pass-parameters-to-u-sql-script)

## <a name="scenario-2-use-u-sql-files-in-azure-data-lake-store"></a>Scénář 2 – použití souborů U-SQL v Azure Data Lake Store

Soubory U-SQL můžete v Azure Data Lake Store použít také pomocí **úlohy systému souborů Azure Data Lake Store** v sadě Azure Feature Pack. Tento přístup umožňuje používat skripty uložené v cloudu.

Postupujte podle následujících kroků a nastavte připojení mezi úlohou Azure Data Lake Storeho systému souborů a úlohou Azure Data Lake Analytics.

### <a name="set-task-control-flow"></a>Nastavit tok řízení úloh

V zobrazení návrhu balíčku SSIS přidejte **úlohu systému souborů Azure Data Lake Store**, **kontejner smyčky foreach** a **úlohu Azure Data Lake Analytics** do kontejneru smyčky foreach. Úloha Azure Data Lake Store systému souborů pomáhá stahovat soubory U-SQL v účtu ADLS do dočasné složky. Kontejner smyčky foreach a úloha Azure Data Lake Analytics pomůžou odeslat každý soubor U-SQL pod dočasnou složkou do Azure Data Lake Analytics účtu jako úlohu U-SQL.

![Diagram, který ukazuje úkol systému souborů Azure Data Lake Store přidaný do kontejneru smyčky foreach.](./media/data-lake-analytics-schedule-jobs-ssis/use-u-sql-files-in-azure-data-lake-store.png)

### <a name="configure-azure-data-lake-store-file-system-task"></a>Konfigurace úlohy systému souborů Azure Data Lake Store

1. Nastavte **operaci** na **CopyFromADLS**.
2. Nastavte **AzureDataLakeConnection**, přečtěte si další informace o [Azure Data Lake Store Správce připojení](/sql/integration-services/connection-manager/azure-data-lake-store-connection-manager).
3. Nastavte **AzureDataLakeDirectory**. Přejděte do složky, kde jsou uloženy skripty U-SQL. Použijte relativní cestu, která je relativní vzhledem ke kořenové složce účtu Azure Data Lake Store.
4. Nastavte **cíl** na složku, která ukládá do mezipaměti stažené skripty U-SQL. Tato cesta ke složce se použije v kontejneru smyčky foreach pro odeslání úlohy U-SQL. 

![Konfigurace úlohy systému souborů Azure Data Lake Store](./media/data-lake-analytics-schedule-jobs-ssis/configure-azure-data-lake-store-file-system-task.png)

[Přečtěte si další informace o úloze Azure Data Lake Store systému souborů](/sql/integration-services/control-flow/azure-data-lake-store-file-system-task).

### <a name="configure-foreach-loop-container"></a>Konfigurovat kontejner smyčky foreach

1. Na stránce **kolekce** **nastavte čítač výčtu** **souboru foreach**.

2. Nastavte **složku** ve skupině **Konfigurace enumerátoru** na dočasnou složku, která obsahuje stažené skripty U-SQL.

3. Nastavte **soubory** v **konfiguraci enumerátoru** tak `*.usql` , aby kontejner smyčky zachytává pouze soubory končící na `.usql` .

    ![Snímek obrazovky zobrazující Editor smyčky foreach s vybraným kolekcí a zvýrazněné konfigurační oddíly výčtu a čítače výčtu.](./media/data-lake-analytics-schedule-jobs-ssis/configure-foreach-loop-container-collection.png)

4. Na stránce **mapování proměnných** přidejte uživatelsky definovanou proměnnou pro získání názvu souboru u každého souboru U-SQL. Pro získání názvu souboru nastavte **index** na 0. V tomto příkladu definujte proměnnou s názvem `User::FileName` . Tato proměnná se použije k dynamickému získání připojení souboru skriptu U-SQL a nastavení názvu úlohy U-SQL v Azure Data Lake Analytics úlohy.

    ![Konfigurace kontejneru smyčky foreach pro získání názvu souboru](./media/data-lake-analytics-schedule-jobs-ssis/configure-foreach-loop-container-variable-mapping.png)

### <a name="configure-azure-data-lake-analytics-task"></a>Konfigurace úlohy Azure Data Lake Analytics 

1. Nastavte typ **sourceType** na **připojení**.

2. Nastavte **připojení** k souboru připojení, které odkazuje na objekty souborů vrácené z kontejneru smyčky foreach.
    
    Vytvoření tohoto připojení k souboru:

   1. Vyberte možnost **\<New Connection...>** v nastavení připojení \.
   2. Nastavte **typ využití** na **existující soubor**a nastavte **soubor** na cestu k souboru existujícímu souboru.

       ![Snímek obrazovky, který zobrazuje Editor Správce připojení souborů s vybraným "existujícím souborem", pro "typ využití".](./media/data-lake-analytics-schedule-jobs-ssis/configure-file-connection-for-foreach-loop-container.png)

   3. V zobrazení **Správci připojení** klikněte pravým tlačítkem myši na vytvořené připojení k souboru a vyberte možnost **vlastnosti**.

   4. V okně **vlastnosti** rozbalte **výrazy**a nastavte **ConnectionString** na proměnnou definovanou v kontejneru smyčky foreach, například `@[User::FileName]` .

       ![Konfigurovat kontejner smyčky foreach](./media/data-lake-analytics-schedule-jobs-ssis/configure-file-connection-property-for-foreach-loop-container.png)

3. Nastavte **AzureDataLakeAnalyticsConnection** na účet Azure Data Lake Analytics, do kterého chcete odesílat úlohy. Přečtěte si další informace o [Azure Data Lake Analytics Správce připojení](/sql/integration-services/connection-manager/azure-data-lake-analytics-connection-manager).

4. Nastavte další konfigurace úloh. [Další informace](/sql/integration-services/control-flow/azure-data-lake-analytics-task)

5. Použijte **výrazy** k dynamickému nastavení názvu úlohy U-SQL:

    1. Na stránce **Expressions (výrazy** ) přidejte novou dvojici klíč-hodnota výrazu pro **JobName**.
    2. Nastavte hodnotu pro JobName na proměnnou definovanou v kontejneru smyčky foreach, například `@[User::FileName]` .
    
        ![Konfigurovat výraz SSIS pro název úlohy U-SQL](./media/data-lake-analytics-schedule-jobs-ssis/configure-expression-for-u-sql-job-name.png)

## <a name="scenario-3-use-u-sql-files-in-azure-blob-storage"></a>Scénář 3 – použití souborů U-SQL v Azure Blob Storage

V Azure Feature Pack můžete použít soubory U-SQL v Azure Blob Storage pomocí **úlohy stažení objektu BLOB v Azure** . Tento přístup vám umožní používat skripty v cloudu.

Postup je podobný [scénáři 2: použití souborů U-SQL v Azure Data Lake Store](#scenario-2-use-u-sql-files-in-azure-data-lake-store). Změňte úlohu systému souborů Azure Data Lake Store na úlohu stažení objektu BLOB v Azure. [Přečtěte si další informace o úloze stažení objektu BLOB v Azure](/sql/integration-services/control-flow/azure-blob-download-task).

Tok řízení je podobný následujícímu.

![Použití souborů U-SQL v Azure Data Lake Store](./media/data-lake-analytics-schedule-jobs-ssis/use-u-sql-files-in-azure-blob-storage.png)

## <a name="scenario-4-use-u-sql-files-on-the-local-machine"></a>Scénář 4 – použití souborů U-SQL na místním počítači

Kromě použití souborů U-SQL uložených v cloudu můžete také použít soubory na místním počítači nebo soubory nasazené s balíčky SSIS.

1. V projektu SSIS klikněte pravým tlačítkem na **Správce připojení** a vyberte **Nový správce připojení**.

2. Vyberte typ **souboru** a klikněte na **Přidat...**.

3. Nastavte **typ využití** na **existující soubor**a nastavte **soubor** na soubor na místním počítači.

    ![Přidat připojení k souboru do místního souboru](./media/data-lake-analytics-schedule-jobs-ssis/configure-file-connection-for-foreach-loop-container.png)

4. Přidat úlohu **Azure Data Lake Analytics** a:
    1. Nastavte typ **sourceType** na **připojení**.
    2. Nastavte **připojení** k souboru, které se právě vytvořilo.

5. Dokončete další konfigurace pro úlohu Azure Data Lake Analytics.

## <a name="scenario-5-use-u-sql-statement-in-ssis-variable"></a>Scénář 5 – použití příkazu U-SQL v proměnné SSIS

V některých případech může být nutné dynamicky generovat příkazy U-SQL. Můžete použít **proměnnou SSIS** s **výrazem SSIS** a dalšími úkoly SSIS, jako je například úloha skriptu, abyste mohli dynamicky generovat příkaz U-SQL.

1. Okno nástrojů pro otevření proměnných pomocí **SSIS > proměnných** nabídky nejvyšší úrovně.

2. Přidejte proměnnou SSIS a nastavte hodnotu přímo nebo použijte k vygenerování hodnoty **výraz** .

3. Přidat **úlohu Azure Data Lake Analytics** a:
    1. Nastavte typ **sourceType** na **Variable**.
    2. Nastavte **SourceVariable** na aktuálně vytvořenou proměnnou SSIS.

4. Dokončete další konfigurace pro úlohu Azure Data Lake Analytics.

## <a name="scenario-6-pass-parameters-to-u-sql-script"></a>Scénář 6 – předání parametrů skriptu U-SQL

V některých případech možná budete chtít dynamicky nastavit hodnotu proměnné U-SQL ve skriptu U-SQL. Funkce **mapování parametrů** v rámci úlohy Azure Data Lake Analytics v tomto scénáři. Obvykle existují dva typické uživatelské případy:

- Nastavte proměnné cesty vstupního a výstupního souboru dynamicky na základě aktuálního data a času.
- Nastavte parametr pro uložené procedury.

[Přečtěte si další informace o tom, jak nastavit parametry pro skript U-SQL](/sql/integration-services/control-flow/azure-data-lake-analytics-task#parameter-mapping-page-configuration).

## <a name="next-steps"></a>Další kroky

- [Spouštění balíčků služby SSIS v Azure](../data-factory/how-to-invoke-ssis-package-ssis-activity.md)
- [Azure Feature Pack pro integrační služby (SSIS)](/sql/integration-services/azure-feature-pack-for-integration-services-ssis#scenario-managing-data-in-the-cloud)
- [Plánování úloh U-SQL pomocí Azure Data Factory](../data-factory/transform-data-using-data-lake-analytics.md)