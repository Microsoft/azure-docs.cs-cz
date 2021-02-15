---
title: Správa balíčků pomocí Azure-SSIS Integration Runtimeho úložiště balíčků
description: Naučte se spravovat balíčky pomocí Azure-SSIS Integration Runtimeového úložiště balíčků.
ms.service: data-factory
ms.topic: conceptual
author: swinarko
ms.author: sawinark
ms.custom: seo-lt-2019
ms.date: 09/29/2020
ms.openlocfilehash: 66fdd20e66360be7cdaa6d918e18ca09c413da07
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/14/2021
ms.locfileid: "100393832"
---
# <a name="manage-packages-with-azure-ssis-integration-runtime-package-store"></a>Správa balíčků pomocí Azure-SSIS Integration Runtimeho úložiště balíčků

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Pokud chcete napředt & přesun místních úloh služba SSIS (SQL Server Integration Services) (SSIS) do cloudu, můžete v Azure Data Factory (ADF) zřídit Azure-SSIS Integration Runtime (IR). Další informace najdete v tématu [zřízení Azure-SSIS IR](./tutorial-deploy-ssis-packages-azure.md). Azure-SSIS IR podporuje:

- Spouštění balíčků nasazených do katalogu SSIS (SSISDB) Azure SQL Database hostovaných serverem/spravovanou instancí (model nasazení projektu)
- Spouštění balíčků nasazených do systému souborů, souborů Azure nebo databáze služby SQL Server Database (MSDB) hostované službou Azure SQL Managed instance (model nasazení balíčku)

Když použijete model nasazení balíčku, můžete si vybrat, jestli chcete Azure-SSIS IR zřídit pomocí obchodů s balíčky. Poskytují vrstvu správy balíčků nad systémem souborů, soubory Azure nebo MSDB hostovaným pomocí spravované instance Azure SQL. Azure-SSIS IR úložiště balíčků umožňuje importovat/exportovat, odstraňovat a spouštět balíčky a monitorovat nebo zastavovat balíčky přes SQL Server Management Studio (SSMS) podobně jako [starší verze úložiště balíčků SSIS](/sql/integration-services/service/package-management-ssis-service). 

## <a name="connect-to-azure-ssis-ir"></a>Připojení k Azure-SSIS IR

Po zřízení Azure-SSIS IR se k němu můžete připojit a procházet jeho úložiště balíčků na SSMS.

![Připojení k Azure-SSIS IR](media/azure-ssis-integration-runtime-package-store/ssms-package-store-connect.png)

V **Průzkumník objektům** okně SSMS vyberte v rozevírací nabídce **připojit** možnost **Azure-SSIS Integration runtime** . Potom se přihlaste k Azure a vyberte příslušné předplatné, ADF a Azure-SSIS IR, které jste zřídili s obchody s balíčky. Vaše Azure-SSIS IR se zobrazí se **spuštěnými balíčky** a uzly **uložených balíčků** pod. Rozbalte uzel **uložené balíčky** , abyste viděli úložiště balíčků pod. Rozbalením úložišť balíčků Zobrazte složky a balíčky pod. Může se zobrazit výzva k zadání přihlašovacích údajů pro úložiště balíčků, pokud se k nim SSMS nepřipojí automaticky. Pokud například rozbalíte úložiště balíčků v databázi MSDB, může být nejprve požádáno o připojení ke spravované instanci Azure SQL.

![Připojit ke spravované instanci Azure SQL](media/azure-ssis-integration-runtime-package-store/ssms-package-store-connect2.png)

## <a name="manage-folders-and-packages"></a>Správa složek a balíčků

Po připojení k vašemu Azure-SSIS IR v SSMS můžete kliknout pravým tlačítkem na libovolné úložiště balíčků, složky nebo balíčky a automaticky se otevře nabídka a vybrat **Nová složka**, **importovat balíček**, **Exportovat balíček**, **Odstranit** nebo **aktualizovat**.

   ![Správa složek a balíčků](media/azure-ssis-integration-runtime-package-store/ssms-package-store-manage.png)

   *  Výběrem **nové složky** vytvořte novou složku pro importované balíčky.

   *  Vyberte **importovat balíček** pro import balíčků ze **systému souborů**, **SQL Server** (msdb) nebo z staršího **úložiště balíčků SSIS** do úložiště balíčků.

      ![Importovat balíček](media/azure-ssis-integration-runtime-package-store/ssms-package-store-import.png)

      V závislosti na **umístění balíčku** , ze kterého se má importovat, vyberte příslušný  / **typ ověřování** serveru, zadejte přihlašovací údaje v případě potřeby, vyberte **cestu k balíčku** a zadejte nový **název balíčku**. Při importu balíčků nejde změnit jejich úroveň ochrany. Pokud ho chcete změnit, použijte nástroje pro SQL Server Data Tools (SSDT) nebo `dtutil` Nástroj příkazového řádku.

      > [!NOTE]
      > Import balíčků SSIS Azure-SSIS IR do úložiště balíčků se dá provést jenom jednou a jenom tak, že je zkopírujete do základního souboru MSDB/soubor/Azure a při zachování jejich verze SQL Server/SSIS. 
      >
      > Vzhledem k tomu, že Azure-SSIS IR je aktuálně založená na **SQL Server 2017**, při spouštění balíčků s nižší verzí se tyto balíčky v době běhu upgradují na balíčky SSIS 2017. Spouštění balíčků s vyšší verzí není podporováno.
      >
      > Vzhledem k tomu, že starší verze úložiště balíčků SSIS jsou vázány na konkrétní verzi SQL Server a jsou přístupné pouze v SSMS pro danou verzi, je třeba nejprve exportovat balíčky nižší verze ve starších úložištích balíčků SSIS pomocí určené verze SSMS, než je bude možné importovat do Azure-SSIS IRch úložišť pomocí SSMS 2019 nebo novějších verzí.
      >
      > Pokud chcete při přepínání úrovně ochrany importovat víc balíčků SSIS do úložiště balíčků Azure-SSIS IR, můžete použít nástroj příkazového řádku [dtutil](/sql/integration-services/dtutil-utility) . Další informace najdete v tématu [nasazení více balíčků s dtutil](#deploying-multiple-packages-with-dtutil).

   *  Vyberte **Exportovat balíček** pro export balíčků z úložiště balíčků do **systému souborů**, **SQL Server** (msdb) nebo starší verze **úložiště balíčků SSIS**.

      ![Exportovat balíček](media/azure-ssis-integration-runtime-package-store/ssms-package-store-export.png)

      V závislosti na **umístění balíčku** , do kterého se má exportovat, vyberte příslušný / **typ ověřování** serveru, v případě potřeby zadejte přihlašovací údaje a vyberte **cestu k balíčku**. Při exportování balíčků, pokud jsou zašifrované, zadejte hesla pro jejich dešifrování a pak můžete změnit jejich úroveň ochrany, například k tomu, abyste se vyhnuli ukládání citlivých dat nebo jejich šifrování nebo všech dat pomocí klíče nebo hesla uživatele.

      > [!NOTE]
      > Export balíčků SSIS Azure-SSIS IR z úložiště balíčků se dá udělat jenom jednou. stačí, když přepnete bez přepínání jejich úrovně ochrany, ale jednoduše je zkopírujete a zachováte jejich SQL Server/SSIS verzi, jinak se upgraduje na balíčky SSIS 2019 nebo novější verze.
      >
      > Vzhledem k tomu, že Azure-SSIS IR je aktuálně založená na **SQL Server 2017**, při spouštění balíčků s nižší verzí se tyto balíčky v době běhu upgradují na balíčky SSIS 2017. Spouštění balíčků s vyšší verzí není podporováno.
      >
      > Pokud chcete při přepínání úrovně ochrany exportovat víc balíčků SSIS z úložiště balíčků Azure-SSIS IR, můžete použít nástroj příkazového řádku [dtutil](/sql/integration-services/dtutil-utility) . Další informace najdete v tématu [nasazení více balíčků s dtutil](#deploying-multiple-packages-with-dtutil).

   *  Vyberte **Odstranit** , pokud chcete z úložiště balíčků odstranit existující složky nebo balíčky.

   *  Výběrem **aktualizovat** zobrazíte nově přidané složky/balíčky v úložišti balíčků.

## <a name="execute-packages"></a>Spustit balíčky

Po připojení k vašemu Azure-SSIS IR v SSMS můžete kliknout pravým tlačítkem na libovolné uložené balíčky a automaticky se otevře nabídka a vybrat **spustit balíček**.  Tím se otevře dialogové okno **Spustit nástroj balíčku** , kde můžete nakonfigurovat spouštění balíčků na Azure-SSIS IR jako spustit aktivity balíčku SSIS v kanálech ADF.

![Spustit stránky nástroje balíčku 1 & 2](media/azure-ssis-integration-runtime-package-store/ssms-package-store-execute.png)

![Spustit stránky nástroje balíčku 3 & 4](media/azure-ssis-integration-runtime-package-store/ssms-package-store-execute2.png)

Stránka **Obecné**, **Konfigurace**, **Možnosti spuštění** a **protokolování** v dialogovém okně **Spustit nástroj pro spuštění balíčku** odpovídají kartě  **Nastavení** aktivity spustit balíček SSIS. Na těchto stránkách můžete zadat šifrovací heslo pro váš balíček a získat přístup k informacím pro konfigurační soubor balíčku. Můžete také zadat přihlašovací údaje a vlastnosti pro spuštění balíčku a také informace o přístupu pro složku protokolu.  Stránka **nastavit hodnoty** v dialogovém okně **Spustit nástroj balíčku** odpovídá kartě **přepsání vlastností** aktivity spustit balíček SSIS, kde můžete zadat existující vlastnosti balíčku, které chcete přepsat. Další informace najdete v tématu [spouštění balíčků SSIS jako aktivity balíčku SSIS v kanálech ADF](./how-to-invoke-ssis-package-ssis-activity.md).

Když vyberete tlačítko **Spustit** , automaticky se vygeneruje a aktivuje se nový kanál ADF s aktivitou spustit balíček SSIS. Pokud již existuje kanál ADF se stejným nastavením, bude znovu spuštěn a nebude vygenerován nový kanál. Aktivita kanálu ADF a spustit balíček SSIS budou pojmenovány `Pipeline_SSMS_YourPackageName_HashString` a v `Activity_SSMS_YourPackageName` uvedeném pořadí.

![Tlačítko spustit nástroj pro balíček](media/azure-ssis-integration-runtime-package-store/ssms-package-store-execute3.png)

![Aktivita provádění balíčku SSIS](media/azure-ssis-integration-runtime-package-store/ssis-activity-package-store.png)

## <a name="monitor-and-stop-running-packages"></a>Monitorování a zastavení spouštění balíčků

Po připojení k vašemu Azure-SSIS IR v SSMS můžete rozbalit uzel **běžící balíčky** , abyste viděli aktuálně spuštěné balíčky pod.  Kliknutím pravým tlačítkem myši na libovolnou z nich můžete zobrazit nabídku a vybrat **zastavit** nebo **aktualizovat**.

   ![Monitorování a zastavení spouštění balíčků](media/azure-ssis-integration-runtime-package-store/ssms-package-store-monitor.png)

   *  Výběrem možnosti **zastavit** zrušíte aktuálně běžící kanál ADF, který spouští balíček jako aktivitu spustit SSIS balíček.

   *  Výběrem **aktualizovat** zobrazíte nově běžící balíčky z úložišť balíčků.

## <a name="monitor-azure-ssis-ir-and-edit-package-stores"></a>Monitorování Azure-SSIS IR a úpravy úložišť balíčků

Po připojení k Azure-SSIS IR na SSMS můžete kliknout pravým tlačítkem myši na ni a vybrat **Přejít na Azure Data Factory Portal** nebo **aktualizovat**.

   ![Přejít na portál ADF](media/azure-ssis-integration-runtime-package-store/ssms-package-store-monitor2.png)

   *  Výběrem možnosti **Přejít na Azure Data Factory Portal** otevřete stránku **Integration runtime** centra monitorování ADF, kde můžete monitorovat Azure-SSIS IR. Na dlaždici **úložiště balíčků** můžete zobrazit počet úložišť balíčků, které jsou připojené k vašemu Azure-SSIS IR.  Výběrem tohoto čísla se zobrazí okno, kde můžete upravit propojené služby ADF, které ukládají informace o přístupu k úložištím balíčků.

      ![Upravit úložiště balíčků](media/azure-ssis-integration-runtime-package-store/ssms-package-store-monitor3.png)

   *  Výběrem **aktualizovat** zobrazíte nově přidané složky/balíčky v úložištích balíčků a spustíte balíčky z úložišť balíčků.

## <a name="deploying-multiple-packages-with-dtutil"></a>Nasazení více balíčků pomocí dtutil

Pokud chcete převést & přesun místních úloh SSIS do SSIS v ADF při zachování staršího modelu nasazení balíčku, je potřeba nasadit balíčky ze systému souborů, MSDB hostovaného pomocí SQL Server nebo starších úložišť balíčků SSIS do souborů Azure, MSDB hostovaných pomocí spravované instance Azure SQL nebo Azure-SSIS IRch obchodů s balíčky. Pokud jste to ještě neudělali, měli byste také přepnout úroveň ochrany ze šifrování pomocí klíče uživatele na nezašifrované nebo šifrování pomocí hesla.

K nasazení více balíčků v dávkách můžete použít nástroj příkazového řádku [dtutil](/sql/integration-services/dtutil-utility) , který je součástí instalace nástroje SQL Server/SSIS. Je svázána s konkrétní verzí SSIS, takže pokud ji použijete k nasazení balíčků nižší verze bez přepínání jejich úrovně ochrany, jednoduše je zkopírujete a zachováte svou SSIS verzi. Pokud ji použijete k jejich nasazení a zároveň přepnete úroveň ochrany, upgraduje se na její verzi SSIS.

 Vzhledem k tomu, že Azure-SSIS IR je aktuálně založená na **SQL Server 2017**, při spouštění balíčků s nižší verzí se tyto balíčky v době běhu upgradují na balíčky SSIS 2017. Spouštění balíčků s vyšší verzí není podporováno.

Proto pokud chcete zabránit upgradům za běhu, nasazování balíčků ke spuštění na Azure-SSIS IR v modelu nasazení balíčku by měl používat dtutil 2017, který je součástí instalace SQL Server/SSIS 2017. Pro tento účel si můžete stáhnout a nainstalovat bezplatnou [edici SQL Server/SSIS 2017 Developer Edition](https://go.microsoft.com/fwlink/?linkid=853016) . Po instalaci můžete v této složce najít dtutil 2017: `YourLocalDrive:\Program Files\Microsoft SQL Server\140\DTS\Binn` .

### <a name="deploying-multiple-packages-from-file-system-on-premises-into-azure-files-with-dtutil"></a>Nasazení více balíčků z místního systému souborů do souborů Azure pomocí dtutil

 K nasazení více balíčků ze systému souborů do souborů Azure a přepínání úrovně ochrany současně můžete spustit následující příkazy z příkazového řádku. Nahraďte prosím všechny řetězce, které jsou specifické pro váš případ.
  
```dos
REM Persist the access credentials for Azure Files on your local machine
cmdkey /ADD:YourStorageAccountName.file.core.windows.net /USER:azure\YourStorageAccountName /PASS:YourStorageAccountKey

REM Connect Azure Files to a drive on your local machine
net use Z: \\YourStorageAccountName.file.core.windows.net\YourFileShare /PERSISTENT:Yes

REM Go to a local folder where you store your packages
cd YourLocalDrive:\...\YourPackageFolder

REM Run dtutil in a loop to deploy your packages from the local folder into Azure Files while switching their protection level
for %f in (*.dtsx) do dtutil.exe /FILE %f /ENCRYPT FILE;Z:\%f;2;YourEncryptionPassword
```

Chcete-li spustit výše uvedené příkazy v dávkovém souboru, nahraďte parametr `%f` `%%f` .

Pokud chcete nasadit více balíčků ze starších úložišť balíčků SSIS do systému souborů do služby soubory Azure a současně přepnout úroveň ochrany, můžete použít stejné příkazy, ale nahradit ji `YourLocalDrive:\...\YourPackageFolder` místní složkou, kterou používají starší úložiště balíčků SSIS: `YourLocalDrive:\Program Files\Microsoft SQL Server\YourSQLServerDefaultCompatibilityLevel\DTS\Packages\YourPackageFolder` . Například pokud je vaše starší úložiště balíčků SSIS vázáno na SQL Server 2016, pokračujte na `YourLocalDrive:\Program Files\Microsoft SQL Server\130\DTS\Packages\YourPackageFolder` .  Hodnotu můžete najít `YourSQLServerDefaultCompatibilityLevel` ze [seznamu SQL Server výchozí úrovně kompatibility](/sql/t-sql/statements/alter-database-transact-sql-compatibility-level#arguments).

Pokud jste nakonfigurovali Azure-SSIS IR úložiště balíčků na Azure Files, nasadí se v nich vaše nasazené balíčky, když se připojíte k Azure-SSIS IR na SSMS 2019 nebo novějších verzích.

### <a name="deploying-multiple-packages-from-msdb-on-premises-into-msdb-in-azure-with-dtutil"></a>Nasazení více balíčků z úložiště MSDB místně do MSDB v Azure pomocí dtutil

 Chcete-li nasadit více balíčků z databáze MSDB hostované pomocí SQL Server nebo starších úložišť balíčků SSIS na databázi msdb hostované službou Azure SQL Managed instance a zároveň přepnout úroveň ochrany, můžete se připojit k vaší SQL Server na SSMS, kliknout pravým tlačítkem myši na `Databases->System Databases->msdb` uzel na **Průzkumník objektů** SSMS a spustit následující skript T-SQL  . Nahraďte prosím všechny řetězce, které jsou specifické pro váš případ:  
  
```sql
BEGIN
  SELECT 'dtutil /SQL '+f.foldername+'\'+NAME+' /ENCRYPT SQL;'+f.foldername+'\'+NAME+';2;YourEncryptionPassword /DestServer YourSQLManagedInstanceEndpoint /DestUser YourSQLAuthUsername /DestPassword YourSQLAuthPassword'
  FROM msdb.dbo.sysssispackages p
  inner join msdb.dbo.sysssispackagefolders f
  ON p.folderid = f.folderid
END
```

Pokud chcete použít soukromý nebo veřejný koncový bod spravované instance SQL Azure, nahraďte `YourSQLManagedInstanceEndpoint` v `YourSQLMIName.YourDNSPrefix.database.windows.net` / `YourSQLMIName.public.YourDNSPrefix.database.windows.net,3342` uvedeném pořadí.

Skript vygeneruje příkazové řádky dtutil pro všechny balíčky v databázi MSDB, kde můžete vybírat, kopírovat & vkládat a spouštět na příkazovém řádku.

![Generovat příkazové řádky dtutil](media/azure-ssis-integration-runtime-package-store/sql-server-msdb-to-sql-mi-msdb.png)

```dos
dtutil /SQL YourFolder\YourPackage1 /ENCRYPT SQL;YourFolder\YourPackage1;2;YourEncryptionPassword /DestServer YourSQLManagedInstanceEndpoint /DestUser YourUserName /DestPassword YourPassword
dtutil /SQL YourFolder\YourPackage2 /ENCRYPT SQL;YourFolder\YourPackage2;2;YourEncryptionPassword /DestServer YourSQLManagedInstanceEndpoint /DestUser YourUserName /DestPassword YourPassword
dtutil /SQL YourFolder\YourPackage3 /ENCRYPT SQL;YourFolder\YourPackage3;2;YourEncryptionPassword /DestServer YourSQLManagedInstanceEndpoint /DestUser YourUserName /DestPassword YourPassword
```

Pokud jste nakonfigurovali Azure-SSIS IR úložiště balíčků v databázi MSDB, nasadí se v nich nasazené balíčky, když se připojíte k Azure-SSIS IR na SSMS 2019 nebo novějších verzích.

### <a name="deploying-multiple-packages-from-msdb-on-premises-into-azure-files-with-dtutil"></a>Nasazení více balíčků z místního počítače MSDB do souborů Azure pomocí dtutil

 Pokud chcete nasadit víc balíčků z MSDB hostovaného SQL Server nebo starších úložišť balíčků SSIS na databázi MSDB do služby Azure Files a zároveň přepnout úroveň ochrany, můžete se připojit k vaší SQL Server na SSMS, kliknout pravým tlačítkem na `Databases->System Databases->msdb` uzel **Průzkumník objektů** SSMS a otevřít tak **nové okno dotazu** a spustit následující skript T-SQL. Nahraďte prosím všechny řetězce, které jsou specifické pro váš případ:  
  
```sql
BEGIN
  SELECT 'dtutil /SQL '+f.foldername+'\'+NAME+' /ENCRYPT FILE;Z:\'+f.foldername+'\'+NAME+'.dtsx;2;YourEncryptionPassword' 
  FROM msdb.dbo.sysssispackages p
  inner join msdb.dbo.sysssispackagefolders f
  ON p.folderid = f.folderid
END
```

Skript vygeneruje příkazové řádky dtutil pro všechny balíčky v databázi MSDB, kde můžete vybírat, kopírovat & vkládat a spouštět na příkazovém řádku.

```dos
REM Persist the access credentials for Azure Files on your local machine
cmdkey /ADD:YourStorageAccountName.file.core.windows.net /USER:azure\YourStorageAccountName /PASS:YourStorageAccountKey

REM Connect Azure Files to a drive on your local machine
net use Z: \\YourStorageAccountName.file.core.windows.net\YourFileShare /PERSISTENT:Yes

REM Multiselect, copy & paste, and run the T-SQL-generated dtutil command lines to deploy your packages from MSDB on premises into Azure Files while switching their protection level
dtutil /SQL YourFolder\YourPackage1 /ENCRYPT FILE;Z:\YourFolder\YourPackage1.dtsx;2;YourEncryptionPassword
dtutil /SQL YourFolder\YourPackage2 /ENCRYPT FILE;Z:\YourFolder\YourPackage2.dtsx;2;YourEncryptionPassword
dtutil /SQL YourFolder\YourPackage3 /ENCRYPT FILE;Z:\YourFolder\YourPackage3.dtsx;2;YourEncryptionPassword
```

Pokud jste nakonfigurovali Azure-SSIS IR úložiště balíčků na Azure Files, nasadí se v nich vaše nasazené balíčky, když se připojíte k Azure-SSIS IR na SSMS 2019 nebo novějších verzích.

## <a name="next-steps"></a>Další kroky

Automaticky generované kanály ADF můžete znovu spustit pomocí aktivit balíčku Execute SSIS nebo vytvářet nové v portálu ADF. Další informace najdete v tématu [spouštění balíčků SSIS jako aktivity balíčku SSIS v kanálech ADF](./how-to-invoke-ssis-package-ssis-activity.md).