---
title: Spouštění balíčků SSIS podle agenta spravované instance Azure SQL
description: Zjistěte, jak spouštět balíčky SSIS pomocí agenta spravované instance Azure SQL.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.author: lle
author: lle
ms.date: 04/14/2020
ms.openlocfilehash: b3b7a25149a9d075c81b30307ade2beb71907637
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/15/2020
ms.locfileid: "81394719"
---
# <a name="execute-ssis-packages-by-azure-sql-managed-instance-agent"></a>Spouštění balíčků SSIS podle agenta spravované instance Azure SQL
Tento článek popisuje, jak spustit balíček SQL Server Integration Services (SSIS) pomocí Agenta spravované instance Azure SQL. Tato funkce poskytuje podobné chování, stejně jako při plánování balíčků SSIS agentem SQL Server v prostředí on-prem.

Pomocí této funkce můžete spustit balíčky SSIS, které jsou uložené v SSISDB spravované instance Azure SQL nebo souborového systému, jako jsou soubory Azure.

## <a name="prerequisites"></a>Požadavky
Chcete-li tuto funkci používat, stáhněte a nainstalujte nejnovější verzi ssms, která je verze 18.5 nebo novější. Stáhněte si ji z [této webové stránky](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-2017).

A musíte zřídit runtime integrace Azure-SSIS ve službě Azure Data Factory, která používá Azure SQL Managed Instance jako koncový server. Pokud jste ji ještě nezřídit, zřídit podle následujících pokynů v [kurzu](tutorial-create-azure-ssis-runtime-portal.md). 

## <a name="run-ssis-packages-in-ssisdb-by-azure-sql-managed-instance-agent"></a>Spuštění balíčků SSIS v SSISDB pomocí agenta spravované instance Azure SQL
V tomto kroku použijete agenta spravované instance Azure SQL k vyvolání balíčků SSIS, které jsou uložené v SSISDB ve spravované instanci Azure SQL.
1. V nejnovější verzi SSMS se připojte ke spravované instanci Azure SQL.
2. Vytvořte novou úlohu agenta a nový krok úlohy.

![Nová práce agenta](./media/how-to-invoke-ssis-package-managed-instance-agent/new-agent-job.png)

3. Na stránce **Nový krok úlohy** zvolte typ **balíčku služby SQL Server Integration Services.**

![Nový krok úlohy SSIS](./media/how-to-invoke-ssis-package-managed-instance-agent/new-ssis-job-step.png)

4. Na kartě **Balíček** zvolte jako typ zdroje balíčku **katalog SSIS.**
5. Vzhledem k tomu, že SSISDB je ve stejné azure SQL spravované instance, není nutné zadat ověřování.
6. Zadejte balíček SSIS z databáze SSISDB.

![Typ zdroje balíčku - katalog SSIS](./media/how-to-invoke-ssis-package-managed-instance-agent/package-source-ssisdb.png)

7. Na kartě **Konfigurace** můžete zadat hodnoty **parametrů,** přepsat hodnoty v **položkách Správce připojení**, přepsat **vlastnost** a zvolit **Úroveň protokolování**.

![Typ zdroje balíčku – konfigurace katalogu SSIS](./media/how-to-invoke-ssis-package-managed-instance-agent/package-source-ssisdb-configuration.png)

8. Po dokončení výše uvedené konfigurace klepnutím na tlačítko **OK** uložte konfiguraci úlohy agenta.
9. Spusťte úlohu agenta pro spuštění balíčku SSIS.


## <a name="run-ssis-packages-in-file-system-by-azure-sql-managed-instance-agent"></a>Spouštění balíčků SSIS v systému souborů agentem spravované instance Azure SQL
V tomto kroku použijete agenta spravované instance Azure SQL k vyvolání balíčků SSIS, které jsou uložené v systému souborů ke spuštění.
1. V nejnovější verzi SSMS se připojte ke spravované instanci Azure SQL.
2. Vytvořte novou úlohu agenta a nový krok úlohy.

   ![Nová práce agenta](./media/how-to-invoke-ssis-package-managed-instance-agent/new-agent-job.png)

3. Na stránce **Nový krok úlohy** zvolte typ **balíčku služby SQL Server Integration Services.**

   ![Nový krok úlohy SSIS](./media/how-to-invoke-ssis-package-managed-instance-agent/new-ssis-job-step.png)

4. Na kartě **Balíček** zvolte **Systém souborů** jako typ zdroje balíčku.

   ![Typ zdroje balíčku – systém souborů](./media/how-to-invoke-ssis-package-managed-instance-agent/package-source-file-system.png)

   1. Pokud se váš balíček nahraje do Souboru Azure, zvolte jako typ zdroje **souboru Azure.**
      - Cesta k balíčku je ** \\ <storage account name>\<.file.core.windows.net \<název sdílené složky>název balíčku>.dtsx**
      - Zadejte název účtu souboru Azure a klíč účtu v **přihlašovacích pověřeních přístupu k souboru balíčku** pro přístup k souboru Azure. Doména je nastavena jako **Azure**.
   2. Pokud se balíček nahraje do sdílené síťové složky, zvolte Jako typ zdroje souboru **síťovou sdílenou** složku.
      - Cesta k balíčku je **cesta UNC** souboru balíčku s jeho rozšířením dtsx.
      - Zadejte odpovídající **doménu**, **uživatelské jméno**a **heslo** pro přístup k souboru balíčku sdílené síťové složky.
   3. Pokud je soubor balíčku zašifrován heslem, vyberte **Šifrovací heslo** a zadejte heslo.

 5. Na kartě **Konfigurace** zadejte **cestu konfiguračního souboru,** pokud potřebujete konfigurační soubor ke spuštění balíčku SSIS.
 6. Na kartě **Možnosti spuštění** můžete zvolit, zda chcete k spuštění balíčku SSIS použít **ověřování systému Windows** nebo **32bitový běh.**
 7. Na kartě **Protokolování** můžete zvolit **cestu protokolování** a odpovídající přihlašovací údaje pro přístup protokolování pro uložení souborů protokolu. Ve výchozím nastavení bude cesta protokolování stejná jako cesta ke složce balíčku a přihlašovací pověření pro protokolování bude stejné jako pověření pro přístup k balíčku.
 8. Na kartě **Nastavit hodnoty** můžete zadat **vlastnost cesta** a **hodnota** a přepsat vlastnosti balíčku.
 Chcete-li například přepsat hodnotu uživatelské proměnné, zadejte její cestu v následujícím formátu: **<variable name>\Package.Variables[Uživatel:: ]. Hodnota**.
 9. Po dokončení výše uvedené konfigurace klepnutím na tlačítko **OK** uložte konfiguraci úlohy agenta.
 10. Spusťte úlohu agenta pro spuštění balíčku SSIS.


 ## <a name="cancel-ssis-package-execution"></a>Zrušit spuštění balíčku SSIS
 Chcete-li zrušit spuštění balíčku z úlohy spravovaného agenta Azure SQL, měli byste postupovat podle následujících kroků namísto přímého zastavení úlohy agenta.
 1. Najděte si **jobId agenta** SQL z **msdb.dbo.sysjobs**.
 2. Najít odpovídající SSIS **spuštěníId** na základě ID úlohy podle níže uvedeného dotazu:
    ```sql
    select * from ssisdb.internal.execution_parameter_values_noncatalog where  parameter_value = 'SQL_Agent_Job_{jobId}' order by execution_id desc
    ```
 3. V katalogu SSIS vyberte **Aktivní operace.**

    ![Typ zdroje balíčku – systém souborů](./media/how-to-invoke-ssis-package-managed-instance-agent/catalog-active-operations.png)

 4. Zastavit odpovídající operaci na základě **executionId**.

## <a name="next-steps"></a>Další kroky
 Balíčky SSIS můžete také naplánovat pomocí Azure Data Factory. Podrobné pokyny najdete v tématu [Aktivace událostí Azure Data Factory](how-to-create-event-trigger.md). 