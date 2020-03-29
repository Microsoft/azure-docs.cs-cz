---
title: Plánování úloh Azure Data Lake Analytics U-SQL pomocí SSIS
description: Zjistěte, jak pomocí služby SQL Server Integration Services naplánovat úlohy U-SQL s vložkovým skriptem nebo ze souborů dotazů U-SQL.
services: data-lake-analytics
author: yanancai
ms.author: yanacai
ms.reviewer: jasonwhowell
ms.assetid: 66dd58b1-0b28-46d1-aaae-43ee2739ae0a
ms.service: data-lake-analytics
ms.topic: conceptual
ms.workload: big-data
ms.date: 07/17/2018
ms.openlocfilehash: 0650fcc5023ac57b193fa23b0dedf65113fd64e6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "71672895"
---
# <a name="schedule-u-sql-jobs-using-sql-server-integration-services-ssis"></a>Naplánovat úlohy U-SQL pomocí služby SQL Server Integration Services (SSIS)

V tomto dokumentu se dozvíte, jak organizovat a vytvářet úlohy U-SQL pomocí služby SQL Server Integration Service (SSIS). 

## <a name="prerequisites"></a>Požadavky

[Sada Azure Feature Pack pro integrační služby](https://docs.microsoft.com/sql/integration-services/azure-feature-pack-for-integration-services-ssis?view=sql-server-2017#scenario-managing-data-in-the-cloud) poskytuje [úlohu Azure Data Lake Analytics](https://docs.microsoft.com/sql/integration-services/control-flow/azure-data-lake-analytics-task?view=sql-server-2017) a [Azure Data Lake Analytics Connection Manager,](https://docs.microsoft.com/sql/integration-services/connection-manager/azure-data-lake-analytics-connection-manager?view=sql-server-2017) který pomáhá připojit se ke službě Azure Data Lake Analytics. Chcete-li použít tento úkol, ujistěte se, že nainstalujete:

- [Stažení a instalace datových nástrojů serveru SQL Server (SSDT) pro Visual Studio](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt?view=sql-server-2017)
- [Instalace sady Azure Feature Pack pro integrační služby (SSIS)](https://docs.microsoft.com/sql/integration-services/azure-feature-pack-for-integration-services-ssis?view=sql-server-2017)

## <a name="azure-data-lake-analytics-task"></a>Úloha Azure Data Lake Analytics

Úloha Azure Data Lake Analytics umožňuje uživatelům odesílat úlohy U-SQL do účtu Azure Data Lake Analytics. 

[Přečtěte si, jak nakonfigurovat úlohu Azure Data Lake Analytics](https://docs.microsoft.com/sql/integration-services/control-flow/azure-data-lake-analytics-task?view=sql-server-2017).

![Úloha Azure Data Lake Analytics v SSIS](./media/data-lake-analytics-schedule-jobs-ssis/data-lake-analytics-azure-data-lake-analytics-task-in-ssis.png)

Skript U-SQL můžete získat z různých míst pomocí integrovaných funkcí a úloh SSIS, níže uvedené scénáře ukazují, jak můžete nakonfigurovat skripty U-SQL pro různé uživatelské případy.

## <a name="scenario-1-use-inline-script-call-tvfs-and-stored-procs"></a>Scénář 1-Použití vložených skript volání tvfs a uložené procs

V Editoru úloh Azure Data Lake Analytics nakonfigurujte **SourceType** jako **DirectInput**a vložte příkazy U-SQL do **USQLStatement**.

Pro snadnou údržbu a správu kódu vložte pouze krátký skript U-SQL jako vložené skripty, například můžete volat existující funkce s hodnotou tabulky a uložené procedury v databázích U-SQL. 

![Upravit včleněný skript U-SQL v úloze SSIS](./media/data-lake-analytics-schedule-jobs-ssis/edit-inline-usql-script-in-ssis.png)

Související článek: [Jak předat parametr do uložených procedur](#scenario-6-pass-parameters-to-u-sql-script)

## <a name="scenario-2-use-u-sql-files-in-azure-data-lake-store"></a>Scénář 2-Použití U-SQL soubory v Azure Data Lake Store

Soubory U-SQL můžete použít také v Azure Data Lake Store pomocí **úlohy systému souborů Azure Data Lake Store** v azure feature packu. Tento přístup umožňuje používat skripty uložené v cloudu.

Podle následujících kroků nastavte připojení mezi úlohou systému souborů Úložiště dat Azure Data Lake a úlohou Azure Data Lake Analytics.

### <a name="set-task-control-flow"></a>Nastavení toku řízení úloh

V návrhovém zobrazení balíčku SSIS přidejte **úlohu systému souborů úložiště datových jezer Azure**, **kontejner foreach smyčky** a **úlohu Azure Data Lake Analytics** v kontejneru Foreach Loop. Úloha systému souborů úložiště dat datového jezera Azure pomáhá stáhnout soubory U-SQL ve vašem účtu ADLS do dočasné složky. Kontejner Foreach smyčky a úloha Azure Data Lake Analytics pomáhají odeslat každý soubor U-SQL v rámci dočasné složky do účtu Azure Data Lake Analytics jako úlohu U-SQL.

![Použití souborů U-SQL v Azure Data Lake Store](./media/data-lake-analytics-schedule-jobs-ssis/use-u-sql-files-in-azure-data-lake-store.png)

### <a name="configure-azure-data-lake-store-file-system-task"></a>Konfigurace úlohy systému souborů úložiště Datových jezer Azure

1. Nastavte **operaci** na **CopyFromADLS**.
2. Nastavte **AzureDataLakeConnection**, přečtěte si další informace o [Azure Data Lake Store Connection Manager](https://docs.microsoft.com/sql/integration-services/connection-manager/azure-data-lake-store-connection-manager?view=sql-server-2017).
3. Nastavte **AzureDataLakeDirectory**. Přejděte na složku ukládající skripty U-SQL. Použijte relativní cestu, která je relativní k kořenové složce účtu Azure Data Lake Store.
4. Nastavte **cíl** na složku, která ukládá stažené skripty U-SQL do mezipaměti. Tato cesta ke složce bude použita v kontejneru smyčky Foreach pro odeslání úlohy U-SQL. 

![Konfigurace úlohy systému souborů úložiště Datových jezer Azure](./media/data-lake-analytics-schedule-jobs-ssis/configure-azure-data-lake-store-file-system-task.png)

[Další informace o úlohách systému souborů úložiště datových jezer Azure](https://docs.microsoft.com/sql/integration-services/control-flow/azure-data-lake-store-file-system-task?view=sql-server-2017).

### <a name="configure-foreach-loop-container"></a>Konfigurace kontejneru smyčky Foreach

1. Na stránce **Kolekce** nastavte **čítač výčtu** na **Foreach File Enumerator**.

2. Nastavte **složku** v části **Skupina konfigurace výčtu** na dočasnou složku, která obsahuje stažené skripty U-SQL.

3. Nastavte **soubory** v **konfiguraci výčtu** tak, aby `*.usql` kontejner `.usql`smyčky zachycová pouze soubory končící písmenem .

    ![Konfigurace kontejneru smyčky Foreach](./media/data-lake-analytics-schedule-jobs-ssis/configure-foreach-loop-container-collection.png)

4. Na stránce **Mapování proměnných** přidejte uživatelem definovanou proměnnou, abyste získali název souboru pro každý soubor U-SQL. Nastavte **index** na 0, chcete-li získat název souboru. V tomto příkladu definujte proměnnou nazvanou `User::FileName`. Tato proměnná se použije k dynamickému získání připojení k souboru skriptu U-SQL a nastavení názvu úlohy U-SQL v úloze Azure Data Lake Analytics.

    ![Konfigurace kontejneru smyčky Foreach pro získání názvu souboru](./media/data-lake-analytics-schedule-jobs-ssis/configure-foreach-loop-container-variable-mapping.png)

### <a name="configure-azure-data-lake-analytics-task"></a>Konfigurace úlohy Azure Data Lake Analytics 

1. Nastavit **SourceType** na **FileConnection**.

2. Nastavte **připojení souboru** k připojení souboru, které odkazuje na objekty souboru vrácené z kontejneru smyčky Foreach.
    
    Chcete-li vytvořit připojení k tomuto souboru:

   1. V nastavení FileConnection zvolte ** \<Nové připojení...>.**
   2. Nastavte **typ použití** na existující **soubor**a nastavte **soubor** na cestu k souboru libovolného existujícího souboru.

       ![Konfigurace kontejneru smyčky Foreach](./media/data-lake-analytics-schedule-jobs-ssis/configure-file-connection-for-foreach-loop-container.png)

   3. V zobrazení **Správce připojení** klepněte pravým tlačítkem myši na právě vytvořené připojení souboru a zvolte **vlastnosti**.

   4. V okně **Vlastnosti** rozbalte **expressions**a nastavte **například connectionstring** na proměnnou definovanou v kontejneru smyčky Foreach `@[User::FileName]`.

       ![Konfigurace kontejneru smyčky Foreach](./media/data-lake-analytics-schedule-jobs-ssis/configure-file-connection-property-for-foreach-loop-container.png)

3. Nastavte **AzureDataLakeAnalyticsConnection** na účet Azure Data Lake Analytics, kterému chcete odeslat úlohy. Další informace o [Azure Data Lake Analytics Connection Manager](https://docs.microsoft.com/sql/integration-services/connection-manager/azure-data-lake-analytics-connection-manager?view=sql-server-2017).

4. Nastavte další konfigurace úloh. [Další informace](https://docs.microsoft.com/sql/integration-services/control-flow/azure-data-lake-analytics-task?view=sql-server-2017).

5. Pomocí výrazů můžete dynamicky nastavit název **úlohy** U-SQL:

    1. Na stránce **Výrazy** přidejte nový pár klíč-hodnota výrazu pro **JobName**.
    2. Nastavte hodnotu jobname na proměnnou definovanou v kontejneru smyčky Foreach, `@[User::FileName]`například .
    
        ![Konfigurace výrazu SSIS pro název úlohy U-SQL](./media/data-lake-analytics-schedule-jobs-ssis/configure-expression-for-u-sql-job-name.png)

## <a name="scenario-3-use-u-sql-files-in-azure-blob-storage"></a>Scénář 3-Použití U-SQL soubory v úložišti objektů blob Azure

Soubory U-SQL můžete použít v Azure Blob Storage pomocí **úlohy stahování objektů blob Azure** v Azure Feature Pack. Tento přístup umožňuje používat skripty v cloudu.

Kroky jsou podobné se [scénářem 2: Použití souborů U-SQL v Azure Data Lake Store](#scenario-2-use-u-sql-files-in-azure-data-lake-store). Změňte úlohu systému ukládání souborů úložiště dat Azure na úlohu stahování objektů blob Azure. [Další informace o úloze stahování objektů blob Azure](https://docs.microsoft.com/sql/integration-services/control-flow/azure-blob-download-task?view=sql-server-2017).

Tok řízení je jako níže.

![Použití souborů U-SQL v Azure Data Lake Store](./media/data-lake-analytics-schedule-jobs-ssis/use-u-sql-files-in-azure-blob-storage.png)

## <a name="scenario-4-use-u-sql-files-on-the-local-machine"></a>Scénář 4-Use U-SQL soubory v místním počítači

Kromě použití U-SQL souborů uložených v cloudu můžete také použít soubory na místním počítači nebo soubory nasazené s balíčky SSIS.

1. Klepněte pravým tlačítkem myši na **položku Správci připojení** v projektu SSIS a zvolte **Nový správce připojení**.

2. Vyberte **Typ souboru** a klepněte na **tlačítko Přidat...**.

3. Nastavte **typ použití** na existující **soubor**a nastavte **soubor** na soubor v místním počítači.

    ![Přidání připojení k místnímu souboru](./media/data-lake-analytics-schedule-jobs-ssis/configure-file-connection-for-foreach-loop-container.png)

4. Přidejte **úlohu Azure Data Lake Analytics** a:
    1. Nastavit **SourceType** na **FileConnection**.
    2. Nastavte **připojení souboru** k právě vytvořenému připojení k souborům.

5. Dokončete další konfigurace úlohy Azure Data Lake Analytics.

## <a name="scenario-5-use-u-sql-statement-in-ssis-variable"></a>Scénář 5-Use U-SQL příkaz v proměnné SSIS

V některých případech může být nutné dynamicky generovat příkazy U-SQL. **Proměnná SSIS** s **výrazem SSIS** a dalšími úlohami SSIS, jako je úloha skriptu, můžete použít k dynamickému generování příkazu U-SQL.

1. Otevřete okno nástroje Proměnné v nabídce **SSIS > Variables** nejvyšší úrovně.

2. Přidejte proměnnou SSIS a nastavte hodnotu přímo nebo použijte **expression** pro generování hodnoty.

3. Přidejte **úlohu Azure Data Lake Analytics** a:
    1. Nastavte **typ zdroje** na **proměnnou**.
    2. Nastavte **proměnnou SourceVariable** na proměnnou SSIS vytvořenou právě teď.

4. Dokončete další konfigurace úlohy Azure Data Lake Analytics.

## <a name="scenario-6-pass-parameters-to-u-sql-script"></a>Parametry scénáře 6-Pass do skriptu U-SQL

V některých případech můžete chtít dynamicky nastavit hodnotu proměnné U-SQL ve skriptu U-SQL. **Funkce Mapování parametrů** v úloze Azure Data Lake Analytics vám pomůže s tímto scénářem. Obvykle existují dva typické případy uživatele:

- Dynamicky nastavte proměnné cesty vstupu a výstupního souboru na základě aktuálního data a času.
- Nastavte parametr pro uložené procedury.

[Další informace o nastavení parametrů skriptu U-SQL](https://docs.microsoft.com/sql/integration-services/control-flow/azure-data-lake-analytics-task?view=sql-server-2017#parameter-mapping-page-configuration).

## <a name="next-steps"></a>Další kroky

- [Spouštění balíčků služby SSIS v Azure](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity)
- [Sada Azure Feature Pack pro integrační služby (SSIS)](https://docs.microsoft.com/sql/integration-services/azure-feature-pack-for-integration-services-ssis?view=sql-server-2017#scenario-managing-data-in-the-cloud)
- [Plánování úloh U-SQL pomocí Azure Data Factory](https://docs.microsoft.com/azure/data-factory/transform-data-using-data-lake-analytics)
