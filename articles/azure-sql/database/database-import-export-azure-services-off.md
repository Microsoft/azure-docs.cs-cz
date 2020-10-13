---
title: Importujte nebo exportujte Azure SQL Database, aniž byste službě Azure povolili přístup k serveru.
description: Importujte nebo exportujte Azure SQL Database, aniž byste službě Azure povolili přístup k serveru.
services: sql-database
ms.service: sql-database
ms.subservice: migration
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 01/08/2020
ms.openlocfilehash: 9b34a2435486a905923e783153ccae97628193a2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91443754"
---
# <a name="import-or-export-an-azure-sql-database-without-allowing-azure-services-to-access-the-server"></a>Import nebo export Azure SQL Database, aniž by bylo možné povolit službám Azure přístup k serveru
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

V tomto článku se dozvíte, jak importovat nebo exportovat *Azure SQL Database, když* je na serveru *povolené služby Azure* . Pracovní postup používá virtuální počítač Azure ke spuštění SqlPackage k provedení operace import nebo export.

## <a name="sign-in-to-the-azure-portal"></a>Přihlášení k webu Azure Portal

Přihlaste se k [portálu Azure Portal](https://portal.azure.com/).

## <a name="create-the-azure-virtual-machine"></a>Vytvoření virtuálního počítače Azure

Vytvořte virtuální počítač Azure tak, že vyberete tlačítko **nasadit do Azure** .

Tato šablona vám umožní nasadit jednoduchý virtuální počítač s Windows pomocí několika různých možností pro verzi Windows, a to pomocí nejnovější opravené verze. Tato akce nasadí virtuální počítač velikosti a2 v umístění skupiny prostředků a vrátí plně kvalifikovaný název domény virtuálního počítače.
<br><br>

[![Obrázek znázorňující tlačítko s názvem "nasadit do Azure".](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-vm-simple-windows%2Fazuredeploy.json)

Další informace najdete v tématu [velmi jednoduché nasazení virtuálního počítače s Windows](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-windows).

## <a name="connect-to-the-virtual-machine"></a>Připojení k virtuálnímu počítači

Následující kroky ukazují, jak se připojit k virtuálnímu počítači pomocí připojení ke vzdálené ploše.

1. Po dokončení nasazení přejděte k prostředku virtuálního počítače.

   ![Snímek obrazovky s tlačítkem připojit zobrazí stránku s přehledem virtuálního počítače.](./media/database-import-export-azure-services-off/vm.png)  

2. Vyberte **Připojit**.

   Zobrazí se formulář soubor protokol RDP (Remote Desktop Protocol) (soubor. RDP) s veřejnou IP adresou a číslem portu pro virtuální počítač.

   ![Formulář RDP](./media/database-import-export-azure-services-off/rdp.png)  

3. Vyberte **Stáhnout soubor RDP**.

   > [!NOTE]
   > K připojení k VIRTUÁLNÍmu počítači můžete také použít SSH.

4. Zavřete formulář **připojit k virtuálnímu počítači** .
5. Chcete-li se připojit k virtuálnímu počítači, otevřete stažený soubor protokolu RDP.
6. Po zobrazení výzvy vyberte **připojit**. Na počítači Mac budete potřebovat klienta protokolu RDP, jako je například tento [Klient vzdálené plochy](https://apps.apple.com/app/microsoft-remote-desktop-10/id1295203466?mt=12) na Mac App Storu.

7. Zadejte uživatelské jméno a heslo, které jste zadali při vytváření virtuálního počítače, a pak zvolte **OK**.

8. Při přihlášení se může zobrazit upozornění na certifikát. Vyberte **Ano** nebo **pokračovat** a pokračujte v připojování.

## <a name="install-sqlpackage"></a>Nainstalovat SqlPackage

[Stáhněte a nainstalujte si nejnovější verzi SqlPackage](https://docs.microsoft.com/sql/tools/sqlpackage-download).

Další informace najdete v tématu [SqlPackage.exe](https://docs.microsoft.com/sql/tools/sqlpackage).

## <a name="create-a-firewall-rule-to-allow-the-vm-access-to-the-database"></a>Vytvořte pravidlo brány firewall, které umožní virtuálnímu počítači přístup k databázi.

Přidejte veřejnou IP adresu virtuálního počítače k bráně firewall serveru.

Následující postup slouží k vytvoření pravidla brány firewall protokolu IP na úrovni serveru pro veřejnou IP adresu vašeho virtuálního počítače a umožňuje připojení z virtuálního počítače.

1. V nabídce na levé straně vyberte **databáze SQL** a pak na stránce **databáze SQL** vyberte svou databázi. Otevře se stránka s přehledem pro vaši databázi, na které se zobrazí plně kvalifikovaný název serveru (například **servername.Database.Windows.NET**), který poskytuje možnosti pro další konfiguraci.

2. Zkopírujte tento plně kvalifikovaný název serveru, který se má použít při připojování k serveru a jeho databázím.

   ![název serveru](./media/database-import-export-azure-services-off/server-name.png)

3. Na panelu nástrojů vyberte **nastavit bránu firewall serveru** . Otevře se stránka **nastavení brány firewall** pro server.

   ![pravidlo brány firewall protokolu IP na úrovni serveru](./media/database-import-export-azure-services-off/server-firewall-rule.png)

4. Výběrem možnosti **Přidat IP adresu klienta** na panelu nástrojů přidejte veřejnou IP adresu virtuálního počítače k novému pravidlu brány firewall IP na úrovni serveru. Pravidlo brány firewall protokolu IP na úrovni serveru může otevřít port 1433 pro jednu IP adresu nebo rozsah IP adres.

5. Vyberte **Uložit**. Pro veřejnou IP adresu vašeho virtuálního počítače se vytvoří pravidlo brány firewall IP adresy na úrovni serveru, které na serveru otevírá port 1433.

6. Zavřete stránku **nastavení brány firewall** .

## <a name="export-a-database-using-sqlpackage"></a>Export databáze pomocí SqlPackage

Chcete-li exportovat Azure SQL Database pomocí nástroje příkazového řádku [SqlPackage](https://docs.microsoft.com/sql/tools/sqlpackage) , přečtěte si téma [Export parametrů a vlastností](https://docs.microsoft.com/sql/tools/sqlpackage#export-parameters-and-properties). Nástroj SqlPackage se dodává s nejnovějšími verzemi nástrojů [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) a [SQL Server Data Tools](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt)nebo si můžete stáhnout nejnovější verzi nástroje [SqlPackage](https://docs.microsoft.com/sql/tools/sqlpackage-download).

Pro většinu produkčních prostředí doporučujeme používat nástroj SqlPackage pro škálování a výkon. Příspěvek na blogu zákaznického poradního týmu SQL Serveru o migraci pomocí souborů BACPAC najdete v tématu popisujícím [migraci z SQL Serveru do služby SQL Database pomocí souborů BACPAC](https://blogs.msdn.microsoft.com/sqlcat/20../../migrating-from-sql-server-to-azure-sql-database-using-bacpac-files/).

Tento příklad ukazuje, jak exportovat databázi pomocí SqlPackage.exe s univerzálním ověřováním služby Active Directory. Nahraďte hodnotami, které jsou specifické pro vaše prostředí.

```cmd
SqlPackage.exe /a:Export /tf:testExport.bacpac /scs:"Data Source=<servername>.database.windows.net;Initial Catalog=MyDB;" /ua:True /tid:"apptest.onmicrosoft.com"
```

## <a name="import-a-database-using-sqlpackage"></a>Import databáze pomocí SqlPackage

Chcete-li importovat databázi SQL Server pomocí nástroje příkazového řádku [SqlPackage](https://docs.microsoft.com/sql/tools/sqlpackage) , přečtěte si téma [Import parametrů a vlastností](https://docs.microsoft.com/sql/tools/sqlpackage#import-parameters-and-properties). SqlPackage má nejnovější [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) a [SQL Server Data Tools](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt). Můžete si také stáhnout nejnovější verzi [SqlPackage](https://docs.microsoft.com/sql/tools/sqlpackage-download).

Pro škálování a výkon doporučujeme místo použití Azure Portal používat SqlPackage ve většině produkčních prostředí. Blog týmu pro poradenské zákazníky SQL Server o migraci pomocí `BACPAC` souborů najdete v tématu [migrace z SQL Server na Azure SQL Database pomocí souborů BacPac](https://blogs.msdn.microsoft.com/sqlcat/2016/10/20/migrating-from-sql-server-to-azure-sql-database-using-bacpac-files/).

Následující příkaz SqlPackage importuje databázi **AdventureWorks2017** z místního úložiště do Azure SQL Database. Vytvoří novou databázi s názvem **myMigratedDatabase** s úrovní služeb **Premium** a cílem služby **P6** . Změňte tyto hodnoty tak, jak jsou vhodné pro vaše prostředí.

```cmd
sqlpackage.exe /a:import /tcs:"Data Source=<serverName>.database.windows.net;Initial Catalog=myMigratedDatabase>;User Id=<userId>;Password=<password>" /sf:AdventureWorks2017.bacpac /p:DatabaseEdition=Premium /p:DatabaseServiceObjective=P6
```

> [!IMPORTANT]
> Pokud se chcete připojit k tAzure SQL Database z za podnikovým firewallem, musí mít brána firewall otevřený port 1433.

Tento příklad ukazuje, jak importovat databázi pomocí SqlPackage s univerzálním ověřováním služby Active Directory.

```cmd
sqlpackage.exe /a:Import /sf:testExport.bacpac /tdn:NewDacFX /tsn:apptestserver.database.windows.net /ua:True /tid:"apptest.onmicrosoft.com"
```

## <a name="performance-considerations"></a>Otázky výkonu

Rychlosti exportu se liší v důsledku mnoha faktorů (například datový tvar), takže není možné předpovědět, jakou rychlost by měla očekávat. SqlPackage může trvat značnou dobu, zejména pro velké databáze.

Nejlepšího výkonu dosáhnete, když můžete vyzkoušet následující strategie:

1. Zajistěte, aby v databázi neběžely žádné jiné úlohy. Vytvoření kopie před exportem může být nejlepším řešením, aby se zajistilo, že nejsou spuštěné žádné další úlohy.
2. Zvyšte cíl na úrovni databázové služby (SLO) pro lepší zpracování úlohy exportu (primárně čtení I/O). Pokud je databáze momentálně GP_Gen5_4, možná Pro důležité obchodní informace vrstva pomůžete s úlohou čtení.
3. Ujistěte se, že existují clusterované indexy, zejména pro velké tabulky.
4. Virtuální počítače by měly být ve stejné oblasti jako databáze, aby se zabránilo omezením sítě.
5. Před nahráním do úložiště objektů BLOB by virtuální počítače měly mít jednotku SSD s adekvátní velikostí pro vytváření dočasných artefaktů.
6. Virtuální počítače by měly mít odpovídající konfiguraci základní a paměti pro konkrétní databázi.

## <a name="store-the-imported-or-exported-bacpac-file"></a>Uložte import nebo export. Soubor BACPAC

Okně. Soubor BACPAC se dá Uložit do [objektů blob Azure](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-overview)nebo [souborů Azure](https://docs.microsoft.com/azure/storage/files/storage-files-introduction).

K dosažení nejlepšího výkonu použijte soubory Azure. SqlPackage pracuje se systémem souborů, aby mohl přistupovat přímo k souborům Azure.

Pokud chcete snížit náklady, používejte objekty blob Azure, které jsou nižší než Premium Azure File sdílená složka. Bude však vyžadovat zkopírování [. BACPAC soubor](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/data-tier-applications#bacpac) mezi objektem BLOB a místním systémem souborů před operací import nebo export. Výsledkem bude to, že proces trvá déle.

Pro nahrání nebo stažení. Soubory BACPAC, přečtěte si téma [přenos dat pomocí úložiště AzCopy a objektů BLOB](../../storage/common/storage-use-azcopy-blobs.md)a [přenos dat pomocí AzCopy a souborového úložiště](../../storage/common/storage-use-azcopy-files.md).

V závislosti na vašem prostředí možná budete muset [nakonfigurovat Azure Storage brány firewall a virtuální sítě](../../storage/common/storage-network-security.md).

## <a name="next-steps"></a>Další kroky

- Informace o tom, jak se připojit k importovaným SQL Database a dotazování na ně, najdete v tématu [rychlý Start: Azure SQL Database: použití SQL Server Management Studio k připojení a dotazování dat](connect-query-ssms.md).
- Příspěvek na blogu zákaznického poradního týmu SQL Serveru o migraci pomocí souborů BACPAC najdete v tématu popisujícím [migraci z SQL Serveru do služby SQL Database pomocí souborů BACPAC](https://techcommunity.microsoft.com/t5/DataCAT/Migrating-from-SQL-Server-to-Azure-SQL-Database-using-Bacpac/ba-p/305407).
- Diskuzi o celém procesu migrace databáze SQL Server, včetně doporučení týkajících se výkonu, najdete v tématu [SQL Server migrace databáze na Azure SQL Database](migrate-to-database-from-sql-server.md).
- Informace o tom, jak bezpečně spravovat a sdílet klíče úložiště a signatury sdíleného přístupu, najdete v tématu [Azure Storage Průvodce zabezpečením](https://docs.microsoft.com/azure/storage/common/storage-security-guide).
