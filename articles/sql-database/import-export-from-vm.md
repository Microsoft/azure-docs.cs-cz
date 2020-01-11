---
title: Import nebo export databáze SQL
description: Importujte nebo exportujte databázi SQL Azure bez povolení přístupu služeb Azure k serveru.
services: sql-database
ms.service: sql-database
ms.subservice: migration
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 01/08/2020
ms.openlocfilehash: 9f694f3f0ec740d0a4e8dc4e6bf8845c408802c8
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/11/2020
ms.locfileid: "75897842"
---
# <a name="import-or-export-an-azure-sql-database-without-allowing-azure-services-to-access-the-server"></a>Import nebo export databáze SQL Azure bez povolení přístupu služeb Azure k serveru

V tomto článku se dozvíte, jak importovat nebo exportovat databázi SQL Azure, když *je na Azure* SQL serveru zapnuté *služby Azure* . Pracovní postup používá virtuální počítač Azure ke spuštění SqlPackage k provedení operace import nebo export.

## <a name="sign-in-to-the-azure-portal"></a>Přihlášení k webu Azure Portal

Přihlaste se na web [Azure Portal](https://portal.azure.com/).

## <a name="create-the-azure-virtual-machine"></a>Vytvoření virtuálního počítače Azure

Vytvořte virtuální počítač Azure tak, že vyberete tlačítko **nasadit do Azure** .

Tato šablona vám umožní nasadit jednoduchý virtuální počítač s Windows pomocí několika různých možností pro verzi Windows, a to pomocí nejnovější opravené verze. Tato akce nasadí virtuální počítač velikosti a2 v umístění skupiny prostředků a vrátí plně kvalifikovaný název domény virtuálního počítače.
<br><br>

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-vm-simple-windows%2Fazuredeploy.json" target="_blank">
    <img src="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png"/>
</a>

Další informace najdete v tématu [velmi jednoduché nasazení virtuálního počítače s Windows](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-windows).


## <a name="connect-to-the-virtual-machine"></a>Připojení k virtuálnímu počítači

Následující kroky ukazují, jak se připojit k virtuálnímu počítači pomocí připojení ke vzdálené ploše.

1. Po dokončení nasazení přejděte k prostředku virtuálního počítače.

    ![Virtuální počítač](./media/import-export-from-vm/vm.png)  

2. Vyberte **Connect** (Připojit).

   S veřejnou IP adresu a port pro virtuální počítač se zobrazí formulář Remote Desktop Protocol soubor (RDP).

   ![Formulář protokolu RDP](./media/import-export-from-vm/rdp.png)  

3. Vyberte **stáhnout soubor RDP**.

   > [!NOTE]
   > SSH můžete také použít pro připojení k vašemu virtuálnímu počítači.

4. Zavřít **připojit k virtuálnímu počítači** formuláře.
5. Chcete-li se připojit k virtuálnímu počítači, otevřete stažený soubor protokolu RDP.
6. Po zobrazení výzvy vyberte **připojit**. Na počítači Mac budete potřebovat klienta protokolu RDP, jako je například tento [Klient vzdálené plochy](https://itunes.apple.com/us/app/microsoft-remote-desktop/id715768417?mt=12) na Mac App Storu.

7. Zadejte uživatelské jméno a heslo, které jste zadali při vytváření virtuálního počítače a pak zvolte **OK**.

8. Při přihlášení se může zobrazit upozornění na certifikát. Zvolte **Ano** nebo **pokračovat** pokračujte s připojením.



## <a name="install-sqlpackage"></a>Nainstalovat SqlPackage

[Stáhněte a nainstalujte si nejnovější verzi SqlPackage](https://docs.microsoft.com/sql/tools/sqlpackage-download).




Další informace naleznete v tématu [SqlPackage. exe](https://docs.microsoft.com/sql/tools/sqlpackage).

## <a name="create-a-firewall-rule-to-allow-the-vm-access-to-the-database"></a>Vytvořte pravidlo brány firewall, které umožní virtuálnímu počítači přístup k databázi.

Do brány firewall serveru SQL Database přidejte veřejnou IP adresu virtuálního počítače.

Následující postup slouží k vytvoření pravidla brány firewall protokolu IP na úrovni serveru pro veřejnou IP adresu vašeho virtuálního počítače a umožňuje připojení z virtuálního počítače.

1. V nabídce na levé straně vyberte **databáze SQL** a pak na stránce **databáze SQL** vyberte svou databázi. Otevře se stránka s přehledem pro vaši databázi, na které se zobrazí plně kvalifikovaný název serveru (například **servername.Database.Windows.NET**), který poskytuje možnosti pro další konfiguraci.

2. Zkopírujte tento plně kvalifikovaný název serveru, který se má použít při připojování k serveru a jeho databázím.

   ![název serveru](./media/sql-database-get-started-portal/server-name.png)

3. Na panelu nástrojů vyberte **nastavit bránu firewall serveru** . Otevře se stránka **nastavení brány firewall** pro databázový server.

   ![pravidlo brány firewall protokolu IP na úrovni serveru](./media/sql-database-get-started-portal/server-firewall-rule.png)

4. Výběrem možnosti **Přidat IP adresu klienta** na panelu nástrojů přidejte veřejnou IP adresu virtuálního počítače k novému pravidlu brány firewall IP na úrovni serveru. Pravidlo brány firewall protokolu IP na úrovni serveru může otevřít port 1433 pro jednu IP adresu nebo rozsah IP adres.

5. Vyberte **Uložit**. Pro veřejnou IP adresu virtuálního počítače se vytvoří pravidlo brány firewall IP adresy na úrovni serveru, na kterém se spouští port 1433 na serveru SQL Database.

6. Zavřete stránku **nastavení brány firewall** .



## <a name="export-a-database-using-sqlpackage"></a>Export databáze pomocí SqlPackage

Chcete-li exportovat databázi SQL pomocí nástroje příkazového řádku [SqlPackage](https://docs.microsoft.com/sql/tools/sqlpackage) , přečtěte si téma [Export parametrů a vlastností](https://docs.microsoft.com/sql/tools/sqlpackage#export-parameters-and-properties). Nástroj SqlPackage se dodává s nejnovějšími verzemi nástrojů [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) a [SQL Server Data Tools](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt)nebo si můžete stáhnout nejnovější verzi nástroje [SqlPackage](https://docs.microsoft.com/sql/tools/sqlpackage-download).

Pro většinu produkčních prostředí doporučujeme používat nástroj SqlPackage pro škálování a výkon. Příspěvek na blogu zákaznického poradního týmu SQL Serveru o migraci pomocí souborů BACPAC najdete v tématu popisujícím [migraci z SQL Serveru do služby SQL Database pomocí souborů BACPAC](https://blogs.msdn.microsoft.com/sqlcat/20../../migrating-from-sql-server-to-azure-sql-database-using-bacpac-files/).

Tento příklad ukazuje, jak exportovat databázi pomocí SqlPackage. exe s univerzálním ověřováním služby Active Directory. Nahraďte hodnotami, které jsou specifické pro vaše prostředí.

```cmd
SqlPackage.exe /a:Export /tf:testExport.bacpac /scs:"Data Source=<servername>.database.windows.net;Initial Catalog=MyDB;" /ua:True /tid:"apptest.onmicrosoft.com"
```




## <a name="import-a-database-using-sqlpackage"></a>Import databáze pomocí SqlPackage

Chcete-li importovat databázi SQL Server pomocí nástroje příkazového řádku [SqlPackage](https://docs.microsoft.com/sql/tools/sqlpackage) , přečtěte si téma [Import parametrů a vlastností](https://docs.microsoft.com/sql/tools/sqlpackage#import-parameters-and-properties). SqlPackage má nejnovější [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) a [SQL Server Data Tools](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt). Můžete si také stáhnout nejnovější verzi [SqlPackage](https://docs.microsoft.com/sql/tools/sqlpackage-download).

Pro škálování a výkon doporučujeme místo použití Azure Portal používat SqlPackage ve většině produkčních prostředí. Blog týmu pro poradenské zákazníky SQL Server o migraci pomocí souborů `BACPAC` najdete v tématu [migrace z SQL Server na Azure SQL Database pomocí souborů BacPac](https://blogs.msdn.microsoft.com/sqlcat/2016/10/20/migrating-from-sql-server-to-azure-sql-database-using-bacpac-files/).

Následující příkaz SqlPackage importuje databázi **AdventureWorks2017** z místního úložiště na server Azure SQL Database. Vytvoří novou databázi s názvem **myMigratedDatabase** s **Premium** úroveň služby a **P6** cíle služby. Změňte tyto hodnoty v závislosti na vašem prostředí.

```cmd
sqlpackage.exe /a:import /tcs:"Data Source=<serverName>.database.windows.net;Initial Catalog=myMigratedDatabase>;User Id=<userId>;Password=<password>" /sf:AdventureWorks2017.bacpac /p:DatabaseEdition=Premium /p:DatabaseServiceObjective=P6
```

> [!IMPORTANT]
> Pokud se chcete připojit k SQL Database serveru, který spravuje izolovanou databázi za podnikovou bránou firewall, musí mít brána firewall otevřený port 1433. Chcete-li se připojit ke spravované instanci, je nutné mít [připojení typu Point-to-site](sql-database-managed-instance-configure-p2s.md) nebo připojení Express Route.

Tento příklad ukazuje, jak importovat databázi s použitím nástroje SqlPackage pomocí univerzálního ověřování Active Directory.

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

Pro nahrání nebo stažení. Soubory BACPAC, přečtěte si téma [přenos dat pomocí úložiště AzCopy a objektů BLOB](../storage/common/storage-use-azcopy-blobs.md)a [přenos dat pomocí AzCopy a souborového úložiště](../storage/common/storage-use-azcopy-files.md).

V závislosti na vašem prostředí možná budete muset [nakonfigurovat Azure Storage brány firewall a virtuální sítě](../storage/common/storage-network-security.md).

## <a name="next-steps"></a>Další kroky

- Informace o připojení a dotazování importovanou databázi SQL, najdete v článku [rychlý start: Azure SQL Database: použití SQL Server Management Studio k připojení a dotazování dat](sql-database-connect-query-ssms.md).
- Příspěvek na blogu zákaznického poradního týmu SQL Serveru o migraci pomocí souborů BACPAC najdete v tématu popisujícím [migraci z SQL Serveru do služby SQL Database pomocí souborů BACPAC](https://techcommunity.microsoft.com/t5/DataCAT/Migrating-from-SQL-Server-to-Azure-SQL-Database-using-Bacpac/ba-p/305407).
- Diskuzi o celý proces SQL serveru databáze migrace, včetně doporučení k výkonu, naleznete v tématu [migrace databáze SQL serveru do Azure SQL Database](sql-database-single-database-migrate.md).
- Informace o správě a sdílení klíčů k úložišti a sdílené přístupové podpisy bezpečně, najdete v [Průvodci zabezpečením Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-security-guide).
