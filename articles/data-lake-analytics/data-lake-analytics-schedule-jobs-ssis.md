---
title: Plánování úloh Azure Data Lake Analytics U-SQL pomocí služby SSIS
description: Zjistěte, jak použít k naplánování úloh U-SQL služby SQL Server Integration Services.
services: data-lake-analytics
author: yanancai
ms.author: yanacai
ms.reviewer: jasonwhowell
ms.assetid: 66dd58b1-0b28-46d1-aaae-43ee2739ae0a
ms.service: data-lake-analytics
ms.topic: conceptual
ms.workload: big-data
ms.date: 07/17/2018
ms.openlocfilehash: 1a0be03c278a152ea56fe0dac5277c39a0878057
ms.sourcegitcommit: 82cdc26615829df3c57ee230d99eecfa1c4ba459
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/19/2019
ms.locfileid: "54412485"
---
# <a name="schedule-u-sql-jobs-using-sql-server-integration-services-ssis"></a>Plánování úloh U-SQL pomocí SQL Server Integration Services (SSIS)

V tomto dokumentu se dozvíte, jak orchestrovat a vytvoření úloh U-SQL pomocí SQL Server Integration Service (SSIS). 

## <a name="prerequisites"></a>Požadavky

[Azure Feature Pack for integrační služby](https://docs.microsoft.com/sql/integration-services/azure-feature-pack-for-integration-services-ssis?view=sql-server-2017#scenario-managing-data-in-the-cloud) poskytuje [úloh Azure Data Lake Analytics](https://docs.microsoft.com/sql/integration-services/control-flow/azure-data-lake-analytics-task?view=sql-server-2017) a [Správce připojení Azure Data Lake Analytics](https://docs.microsoft.com/sql/integration-services/connection-manager/azure-data-lake-analytics-connection-manager?view=sql-server-2017) , že umožňuje připojení k Azure Data Lake Služba analýzy. Pokud chcete pomocí této úlohy, ujistěte se, že jste si nainstalovali:

- [Stáhněte a nainstalujte SQL Server Data Tools (SSDT) pro Visual Studio](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt?view=sql-server-2017)
- [Instalace Azure Feature Pack for Integration Services (SSIS)](https://docs.microsoft.com/sql/integration-services/azure-feature-pack-for-integration-services-ssis?view=sql-server-2017)

## <a name="azure-data-lake-analytics-task"></a>Úlohy Azure Data Lake Analytics

Úlohy Azure Data Lake Analytics tak uživatelé mohli odesílat úloh U-SQL k účtu Azure Data Lake Analytics. 

[Další informace o konfiguraci úlohy Azure Data Lake Analytics](https://docs.microsoft.com/sql/integration-services/control-flow/azure-data-lake-analytics-task?view=sql-server-2017).

![Úloha Azure Data Lake Analytics v prostředí SSIS](./media/data-lake-analytics-schedule-jobs-ssis/data-lake-analytics-azure-data-lake-analytics-task-in-ssis.png)

Skript U-SQL můžete získat z různých míst pomocí integrovaných funkcí služby SSIS a úkolů, scénáře ukazují, jak lze nakonfigurovat skriptů U-SQL pro případy, jiný uživatel.

## <a name="scenario-1-use-inline-script-call-tvfs-and-stored-procs"></a>Scénář 1 – použití vloženého skriptu volání tvf a uložené procs

Nakonfigurujte v Azure Data Lake Analytics Editor úloh, **SourceType** jako **DirectInput**a put U-SQL příkazy do **USQLStatemnt**.

Pro snadné údržby a správy kódu pouze vložte krátký skript U-SQL jako vložené skripty například můžete volat existující funkce vracející tabulku a uloženým procedurám databáze U-SQL. 

![Upravit skript pro vložené U-SQL v úloze služby SSIS](./media/data-lake-analytics-schedule-jobs-ssis/edit-inline-usql-script-in-ssis.png)

Související článek: [Jak předat parametr uložené procedury](#scenario-6-pass-parameters-to-u-sql-script)

## <a name="scenario-2-use-u-sql-files-in-azure-data-lake-store"></a>Scénář 2 pomocí U-SQL souborů v Azure Data Lake Store

Můžete také použít souborů U-SQL v Azure Data Lake Store s použitím **úlohy Azure Data Lake Store souborů systému** v Azure Feature Pack. Tento přístup umožňuje používat skripty uložené v cloudu.

Pomocí následujícího postupu k nastavení připojení mezi Azure Data Lake Store souboru systému úloh a úloha Azure Data Lake Analytics.

### <a name="set-task-control-flow"></a>Sada úkolů řízení toku

V zobrazení návrhu balíčku služby SSIS, přidejte **úlohy Azure Data Lake Store souborů systému**, **kontejneru smyčky Foreach** a **úloha Azure Data Lake Analytics** ve smyčce Foreach Kontejner. Úlohy Azure Data Lake Store souborů systému pomáhá ke stahování souborů U-SQL ve vašem účtu ADLS do dočasné složky. Kontejner smyčky Foreach a úloh Azure Data Lake Analytics pomoct odešlete každý soubor U-SQL v dočasné složce do účtu Azure Data Lake Analytics jako úlohu U-SQL.

![Použití souborů U-SQL v Azure Data Lake Store](./media/data-lake-analytics-schedule-jobs-ssis/use-u-sql-files-in-azure-data-lake-store.png)

### <a name="configure-azure-data-lake-store-file-system-task"></a>Konfigurace úlohy systému souborů Azure Data Lake Store

1. Nastavte **operace** k **CopyFromADLS**.
2. Nastavit **AzureDataLakeConnection**, další informace o [Správce připojení Azure Data Lake Store](https://docs.microsoft.com/sql/integration-services/connection-manager/azure-data-lake-store-connection-manager?view=sql-server-2017).
3. Nastavte **AzureDataLakeDirectory**. Přejděte ke složce pro ukládání skriptů U-SQL. Použijte relativní cestu, která je relativní vzhledem ke kořenové složce účtu Azure Data Lake Store.
4. Nastavte **cílové** do složky, která ukládá do mezipaměti staženými skripty U-SQL. Tato cesta ke složce se použije v kontejneru smyčky Foreach pro odeslání úlohy U-SQL. 

![Konfigurace úlohy systému souborů Azure Data Lake Store](./media/data-lake-analytics-schedule-jobs-ssis/configure-azure-data-lake-store-file-system-task.png)

[Další informace o Azure Data Lake Store souboru systému úloh](https://docs.microsoft.com/sql/integration-services/control-flow/azure-data-lake-store-file-system-task?view=sql-server-2017).

### <a name="configure-foreach-loop-container"></a>Konfigurace kontejneru smyčky Foreach

1. V **kolekce** nastavte **enumerátor** k **Foreach souboru enumerátor**.

2. Nastavte **složky** pod **enumerátor konfigurace** do dočasné složky, která zahrnuje staženými skripty U-SQL.

3. Nastavit **soubory** pod **enumerátor konfigurace** k `*.usql` tak, aby kontejneru smyčky zachytí pouze soubory končící `.usql`.

    ![Konfigurace kontejneru smyčky Foreach](./media/data-lake-analytics-schedule-jobs-ssis/configure-foreach-loop-container-collection.png)

4. V **proměnné mapování** stránce, přidání uživatelem definované proměnné pro získání názvu souboru pro každý soubor U-SQL. Nastavte **Index** na hodnotu 0 a získat tak název souboru. V tomto příkladu, definujte proměnnou s názvem `User::FileName`. Tato proměnná se použije pro dynamické získání připojení souboru skriptu U-SQL a nastavte název úlohy U-SQL v Azure Data Lake Analytics úkolu.

    ![Konfigurace kontejneru smyčka Foreach se získat název souboru](./media/data-lake-analytics-schedule-jobs-ssis/configure-foreach-loop-container-variable-mapping.png)

### <a name="configure-azure-data-lake-analytics-task"></a>Konfigurace úlohy Azure Data Lake Analytics 

1. Nastavte **SourceType** k **FileConnection**.

2. Nastavte **FileConnection** do souboru připojení, která odkazuje na objekty souboru vrácená z kontejneru smyčku Foreach.
    
    Chcete-li vytvořit toto připojení k souboru:

    1. Zvolte **<New Connection...>** FileConnection nastavení.
    2. Nastavte **typ použití** k **existující soubor**a nastavte **souboru** cestu všechny existující soubory.

        ![Konfigurace kontejneru smyčky Foreach](./media/data-lake-analytics-schedule-jobs-ssis/configure-file-connection-for-foreach-loop-container.png)

    3. V **připojení správci** zobrazení, klikněte pravým tlačítkem na připojení souboru právě vytvořili a zvolte **vlastnosti**.

    4. V **vlastnosti** okna rozbalte **výrazy**a nastavte **ConnectionString** proměnné definované v kontejneru smyčky Foreach, například `@[User::FileName]`.

        ![Konfigurace kontejneru smyčky Foreach](./media/data-lake-analytics-schedule-jobs-ssis/configure-file-connection-property-for-foreach-loop-container.png)

3. Nastavte **AzureDataLakeAnalyticsConnection** k účtu Azure Data Lake Analytics, které chcete odesílat úlohy do. Další informace o [Správce připojení Azure Data Lake Analytics](https://docs.microsoft.com/sql/integration-services/connection-manager/azure-data-lake-analytics-connection-manager?view=sql-server-2017).

4. Nastavte další úlohy konfigurace. [Další informace](https://docs.microsoft.com/sql/integration-services/control-flow/azure-data-lake-analytics-task?view=sql-server-2017).

5. Použití **výrazy** nastavovat dynamicky název úlohy U-SQL:

    1. V **výrazy** stránce zadat nový pár klíč hodnota výrazu pro **JobName**.
    2. Nastavte hodnotu pro název úlohy na proměnnou definovanou v kontejneru smyčky Foreach, například `@[User::FileName]`.
    
        ![Konfigurace prostředí SSIS výraz pro název úlohy U-SQL](./media/data-lake-analytics-schedule-jobs-ssis/configure-expression-for-u-sql-job-name.png)

## <a name="scenario-3-use-u-sql-files-in-azure-blob-storage"></a>Scénář 3 použití U-SQL souborů ve službě Azure Blob Storage

Soubory U-SQL ve službě Azure Blob Storage můžete používat pomocí **úloha stažení objektů Blob v Azure** v Azure Feature Pack. Díky tomu můžete pomocí skriptů v cloudu.

Kroky jsou podobné s [scénář 2: Použití souborů U-SQL v Azure Data Lake Store](#scenario-2-use-u-sql-files-in-azure-data-lake-store). Změňte úlohu systém souborů Azure Data Lake Store k úloze stažení objektů Blob v Azure. [Další informace o úloze stažení objektů Blob v Azure](https://docs.microsoft.com/sql/integration-services/control-flow/azure-blob-download-task?view=sql-server-2017).

Tok řízení je jako níže.

![Použití souborů U-SQL v Azure Data Lake Store](./media/data-lake-analytics-schedule-jobs-ssis/use-u-sql-files-in-azure-blob-storage.png)

## <a name="scenario-4-use-u-sql-files-on-the-local-machine"></a>Scénář 4 pomocí U-SQL souborů na místním počítači

Kromě použití U-SQL soubory uložené v cloudu, můžete také použít soubory na svém místním počítači nebo souborech nasazených s balíčků služby SSIS.

1. Klikněte pravým tlačítkem na **připojení správci** v SSIS projekt a zvolte **nový správce připojení**.

2. Vyberte **souboru** zadejte a klikněte na tlačítko **přidat...** .

3. Nastavte **typ použití** k **existující soubor**a nastavte **souboru** do souboru na místním počítači.

    ![Přidat soubor připojení k místnímu souboru.](./media/data-lake-analytics-schedule-jobs-ssis/configure-file-connection-for-foreach-loop-container.png)

4. Přidat **Azure Data Lake Analytics** úloh a:
    1. Nastavte **SourceType** k **FileConnection**.
    2. Nastavte **FileConnection** připojení k souboru, který je právě vytvořili.

5. Další konfigurace pro Azure Data Lake Analytics úkol dokončete.

## <a name="scenario-5-use-u-sql-statement-in-ssis-variable"></a>Scénář 5 pomocí U-SQL v proměnné prostředí SSIS

V některých případech budete muset dynamicky generovat příkazy U-SQL. Můžete použít **SSIS proměnné** s **SSIS výraz** a další úlohy služby SSIS, jako je skript úkolu, vám pomůže dynamicky generovat příkazu U-SQL.

1. Otevřít okno nástroje proměnné prostřednictvím **SSIS > proměnné** nabídek nejvyšší úrovně.

2. Přidat proměnné prostředí SSIS a nastavte hodnotu přímo nebo použijte **výraz** a vygeneruje tak hodnotu.

3. Přidat **úloh Azure Data Lake Analytics** a:
    1. Nastavte **SourceType** k **proměnné**.
    2. Nastavte **SourceVariable** k službou SSIS proměnné právě vytvořili.

4. Další konfigurace pro Azure Data Lake Analytics úkol dokončete.

## <a name="scenario-6-pass-parameters-to-u-sql-script"></a>Scénář 6 Pass parametry skriptu U-SQL

V některých případech můžete dynamicky nastavovat hodnotu proměnné U-SQL ve skriptu U-SQL. **Mapování parametrů** funkce v Azure Data Lake Analytics úloh nápovědy v tomto scénáři. Obvykle existují dva možné případy typický uživatel:

- Nastavte vstupní a výstupní proměnné cesty souboru dynamicky podle aktuální datum a čas.
- Nastavte parametr pro uložené procedury.

[Další informace o tom, jak nastavit parametry pro skript U-SQL](https://docs.microsoft.com/sql/integration-services/control-flow/azure-data-lake-analytics-task?view=sql-server-2017#parameter-mapping-page-configuration).

## <a name="next-steps"></a>Další postup

- [Spouštění balíčků služby SSIS v Azure](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity)
- [Azure Feature Pack for Integration Services (SSIS)](https://docs.microsoft.com/sql/integration-services/azure-feature-pack-for-integration-services-ssis?view=sql-server-2017#scenario-managing-data-in-the-cloud)
- [Plánování úloh U-SQL pomocí Azure Data Factory](https://docs.microsoft.com/azure/data-factory/transform-data-using-data-lake-analytics)

