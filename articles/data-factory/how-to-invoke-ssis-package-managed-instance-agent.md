---
title: Spouštění balíčků SSIS pomocí agenta spravované instance Azure SQL Database
description: Naučte se spouštět balíčky SSIS pomocí agenta spravované instance Azure SQL Database.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.author: lle
author: lle
ms.date: 04/14/2020
ms.openlocfilehash: fcbfeb5ab3a3a80fdb8f7e355f290451d4afe804
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "82144806"
---
# <a name="run-ssis-packages-by-using-azure-sql-database-managed-instance-agent"></a>Spouštění balíčků SSIS pomocí agenta spravované instance Azure SQL Database
Tento článek popisuje, jak spustit balíček služba SSIS (SQL Server Integration Services) (SSIS) pomocí Azure SQL Database agenta spravované instance. Tato funkce poskytuje chování, které se podobá tomu, když naplánujete balíčky SSIS pomocí agenta SQL Server v místním prostředí.

Pomocí této funkce můžete spouštět balíčky SSIS uložené v SSISDB ve spravované instanci Azure SQL Database nebo systému souborů, jako jsou soubory Azure.

## <a name="prerequisites"></a>Požadavky
Chcete-li použít tuto funkci, [Stáhněte](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-2017) a nainstalujte nejnovější verzi SQL Server Management Studio (SSMS), což je verze 18,5.

Také je potřeba [zřídit prostředí Azure-SSIS Integration runtime](tutorial-create-azure-ssis-runtime-portal.md) v Azure Data Factory. Používá Azure SQL Database spravovanou instanci jako server koncového bodu. 

## <a name="run-an-ssis-package-in-ssisdb"></a>Spuštění balíčku SSIS v SSISDB
V tomto postupu použijete Azure SQL Database agenta spravované instance k vyvolání balíčku SSIS, který je uložený v SSISDB.

1. V nejnovější verzi SSMS se připojte k Azure SQL Database spravované instanci.
1. Vytvoří novou úlohu agenta a nový krok úlohy. V části **Agent SQL Server**klikněte pravým tlačítkem na složku **Jobs (úlohy** ) a pak vyberte **Nová úloha**.

   ![Výběry pro vytvoření nové úlohy agenta](./media/how-to-invoke-ssis-package-managed-instance-agent/new-agent-job.png)

1. Na stránce **Nový krok úlohy** vyberte jako typ možnost **služba SSIS (SQL Server Integration Services) balíček** .

   ![Výběry pro vytvoření nového kroku úlohy SSIS](./media/how-to-invoke-ssis-package-managed-instance-agent/new-ssis-job-step.png)

1. Na kartě **balíček** jako typ zdroje balíčku vyberte **katalog SSIS** .
1. Vzhledem k tomu, že SSISDB je ve spravované instanci Azure SQL Database, nemusíte zadávat ověřování.
1. Zadejte balíček SSIS z SSISDB.

   ![Karta balíček s výběry pro typ zdroje balíčku](./media/how-to-invoke-ssis-package-managed-instance-agent/package-source-ssisdb.png)

1. Na kartě **Konfigurace** můžete:
  
   - Zadejte hodnoty parametrů v části **parametry**.
   - Přepište hodnoty v části **Správci připojení**.
   - Přepište vlastnost a vyberte úroveň protokolování v části **Upřesnit**.

   ![Karta konfigurace s výběry pro typ zdroje balíčku](./media/how-to-invoke-ssis-package-managed-instance-agent/package-source-ssisdb-configuration.png)

1. Výběrem **OK** uložte konfiguraci úlohy agenta.
1. Spusťte úlohu agenta a spusťte balíček SSIS.


## <a name="run-an-ssis-package-in-the-file-system"></a>Spuštění balíčku SSIS v systému souborů
V tomto postupu použijete Azure SQL Database agenta spravované instance ke spuštění balíčku SSIS, který je uložený v systému souborů.

1. V nejnovější verzi SSMS se připojte k Azure SQL Database spravované instanci.
1. Vytvoří novou úlohu agenta a nový krok úlohy. V části **Agent SQL Server**klikněte pravým tlačítkem na složku **Jobs (úlohy** ) a pak vyberte **Nová úloha**.

   ![Výběry pro vytvoření nové úlohy agenta](./media/how-to-invoke-ssis-package-managed-instance-agent/new-agent-job.png)

1. Na stránce **Nový krok úlohy** vyberte jako typ možnost **služba SSIS (SQL Server Integration Services) balíček** .

   ![Výběry pro vytvoření nového kroku úlohy SSIS](./media/how-to-invoke-ssis-package-managed-instance-agent/new-ssis-job-step.png)

1. Na kartě **balíček** :

   1. Jako **zdroj balíčku**vyberte **systém souborů**.
   
   1. Pro **typ zdroje souboru**:   

      - Pokud se balíček nahraje do souborů Azure, vyberte **sdílená složka Azure**.

        ![Možnosti pro typ zdroje souboru](./media/how-to-invoke-ssis-package-managed-instance-agent/package-source-file-system.png)
      
        Cesta k balíčku je ** \\ <storage account name>File.Core.Windows.NET\<název sdílené složky>\<název balíčku>. dtsx**.
      
        V části **přihlašovací údaje pro přístup k souboru balíčku**zadejte název účtu služby Azure a klíč účtu pro přístup k souboru Azure. Doména je nastavená jako **Azure**.

      - Pokud se balíček nahraje do sdílené síťové složky, vyberte **Síťová sdílená položka**.
      
        Cesta k balíčku je cesta UNC k souboru balíčku s příponou. dtsx.
      
        Zadejte odpovídající doménu, uživatelské jméno a heslo pro přístup k souboru balíčku sdílené síťové složky.
   1. Pokud je soubor balíčku zašifrovaný pomocí hesla, vyberte heslo pro **šifrování** a zadejte heslo.
1. Na kartě **Konfigurace** zadejte cestu ke konfiguračnímu souboru, pokud potřebujete konfigurační soubor ke spuštění balíčku SSIS.
1. Na kartě **Možnosti spuštění** můžete zvolit, zda chcete-li spustit balíček SSIS pomocí **ověřování systému Windows** nebo **32 bitového běhu** .
1. Na kartě **protokolování** můžete zvolit cestu protokolování a odpovídající přihlašovací údaje pro přístup k protokolování pro ukládání souborů protokolu. Ve výchozím nastavení je cesta protokolování stejná jako cesta ke složce balíčku a přihlašovací údaje pro přístup k balíčkům jsou stejné jako přihlašovací údaje pro přístup k balíčku.
1. Na kartě **nastavit hodnoty** můžete zadat cestu a hodnotu vlastnosti pro přepsání vlastností balíčku.
 
   Chcete-li například přepsat hodnotu proměnné uživatele, zadejte její cestu v následujícím formátu: **\package.Variables [uživatel:<variable name>:]. Hodnota**.
1. Výběrem **OK** uložte konfiguraci úlohy agenta.
1. Spusťte úlohu agenta a spusťte balíček SSIS.


## <a name="cancel-ssis-package-execution"></a>Zrušit provádění balíčku SSIS
Chcete-li zrušit spuštění balíčku z úlohy agenta spravované instance Azure SQL Database, proveďte následující kroky místo přímého zastavení úlohy agenta:

1. Najděte svůj Agent SQL **jobId** z **msdb. dbo. tabulka sysjobs**.
1. Pomocí tohoto dotazu Najděte odpovídající **EXECUTIONID** SSIS na základě ID úlohy:
   ```sql
   select * from ssisdb.internal.execution_parameter_values_noncatalog where  parameter_value = 'SQL_Agent_Job_{jobId}' order by execution_id desc
   ```
1. Klikněte pravým tlačítkem na katalog SSISDB a pak vyberte **aktivní operace**.

   !["Aktivní operace" v místní nabídce katalogu SSISDB](./media/how-to-invoke-ssis-package-managed-instance-agent/catalog-active-operations.png)

1. Zastavte odpovídající operaci na základě **ExecutionID**.

## <a name="next-steps"></a>Další kroky
Balíčky SSIS můžete také naplánovat pomocí Azure Data Factory. Podrobné pokyny najdete v tématu [Azure Data Factory Trigger události](how-to-create-event-trigger.md). 