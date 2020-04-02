---
title: Import nebo export databáze SQL
description: Importujte nebo exportujte databázi Azure SQL, aniž byste umožnili službám Azure přístup k serveru.
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
ms.openlocfilehash: 61a85b2554bbd69541b3081f72525d2b7deed625
ms.sourcegitcommit: c5661c5cab5f6f13b19ce5203ac2159883b30c0e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/01/2020
ms.locfileid: "80529230"
---
# <a name="import-or-export-an-azure-sql-database-without-allowing-azure-services-to-access-the-server"></a>Import nebo export databáze Azure SQL bez přístupu služeb Azure k serveru

Tento článek ukazuje, jak importovat nebo exportovat databázi Azure SQL, když *je povolení služeb Azure* nastaveno na *VYPNUTO* na serveru Azure SQL. Pracovní postup používá virtuální počítač Azure ke spuštění sqlpackage k provedení operace importu nebo exportu.

## <a name="sign-in-to-the-azure-portal"></a>Přihlášení k webu Azure Portal

Přihlaste se k [portálu Azure](https://portal.azure.com/).

## <a name="create-the-azure-virtual-machine"></a>Vytvoření virtuálního počítače Azure

Vytvořte virtuální počítač Azure výběrem tlačítka **Nasadit do Azure.**

Tato šablona umožňuje nasadit jednoduchý virtuální počítač se systémem Windows pomocí několika různých možností pro verzi systému Windows pomocí nejnovější opravené verze. Tím se nasadí virtuální počítače velikosti A2 v umístění skupiny prostředků a vrátí plně kvalifikovaný název domény virtuálního počítače.
<br><br>

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-vm-simple-windows%2Fazuredeploy.json" target="_blank">
    <img src="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png"/>
</a>

Další informace naleznete [v tématu Velmi jednoduché nasazení virtuálního počítače se systémem Windows](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-windows).


## <a name="connect-to-the-virtual-machine"></a>Připojení k virtuálnímu počítači

Následující kroky ukazují, jak se připojit k virtuálnímu počítači pomocí připojení ke vzdálené ploše.

1. Po dokončení nasazení přejděte k prostředku virtuálního počítače.

    ![Virtuální počítač](./media/import-export-from-vm/vm.png)  

2. Vyberte **Connect** (Připojit).

   Zobrazí se formulář souboru protokolu RDP (.rdp) s veřejnou IP adresou a číslem portu virtuálního počítače.

   ![Formulář RDP](./media/import-export-from-vm/rdp.png)  

3. Vyberte **stáhnout soubor RDP**.

   > [!NOTE]
   > SSH můžete taky použít k připojení k virtuálnímu počítači.

4. Zavřete formulář **Připojit k virtuálnímu počítači.**
5. Chcete-li se připojit k virtuálnímu počítači, otevřete stažený soubor protokolu RDP.
6. Po zobrazení výzvy vyberte **Připojit**. Na počítači Mac budete potřebovat klienta protokolu RDP, jako je například tento [Klient vzdálené plochy](https://apps.apple.com/app/microsoft-remote-desktop-10/id1295203466?mt=12) na Mac App Storu.

7. Zadejte uživatelské jméno a heslo, které jste zadali při vytváření virtuálního počítače, a pak zvolte **OK**.

8. Při přihlášení se může zobrazit upozornění na certifikát. Chcete-li pokračovat v připojení, zvolte **Ano** nebo **Pokračovat.**



## <a name="install-sqlpackage"></a>Instalace balíčku SqlPackage

[Stáhněte a nainstalujte nejnovější verzi programu SqlPackage](https://docs.microsoft.com/sql/tools/sqlpackage-download).




Další informace naleznete v tématu [SqlPackage.exe](https://docs.microsoft.com/sql/tools/sqlpackage).

## <a name="create-a-firewall-rule-to-allow-the-vm-access-to-the-database"></a>Vytvoření pravidla brány firewall, které umožní přístup virtuálního počítače k databázi

Přidejte veřejnou IP adresu virtuálního počítače do brány firewall serveru SQL Database.

Následující kroky vytvoří pravidlo brány firewall IP na úrovni serveru pro veřejnou IP adresu virtuálního počítače a povolí připojení z virtuálního počítače.

1. Vyberte **databáze SQL** z nabídky na levé straně a pak vyberte databázi na stránce **databáze SQL.** Otevře se stránka s přehledem databáze, která zobrazuje plně kvalifikovaný název serveru (například **servername.database.windows.net)** a poskytuje možnosti pro další konfiguraci.

2. Zkopírujte tento plně kvalifikovaný název serveru, který chcete použít při připojování k serveru a jeho databázím.

   ![název serveru](./media/sql-database-get-started-portal/server-name.png)

3. Na panelu nástrojů vyberte **Nastavit serverovou bránu firewall.** Otevře se stránka **Nastavení brány firewall** pro databázový server.

   ![Pravidlo brány firewall IP na úrovni serveru](./media/sql-database-get-started-portal/server-firewall-rule.png)

4. Zvolte **Přidat IP adresu klienta** na panelu nástrojů, chcete-li přidat veřejnou IP adresu virtuálního počítače do nového pravidla brány firewall IP na úrovni serveru. Pravidlo brány firewall IP na úrovni serveru může otevřít port 1433 pro jednu adresu IP nebo rozsah adres IP.

5. Vyberte **Uložit**. Pravidlo brány firewall IP na úrovni serveru je vytvořeno pro otevření portu veřejné IP adresy virtuálního počítače 1433 na databázovém serveru SQL.

6. Zavřete stránku **nastavení brány firewall.**



## <a name="export-a-database-using-sqlpackage"></a>Export databáze pomocí balíčku SqlPackage

Informace o exportu databáze SQL pomocí nástroje příkazového řádku [SqlPackage](https://docs.microsoft.com/sql/tools/sqlpackage) naleznete v [tématu Export parametrů a vlastností](https://docs.microsoft.com/sql/tools/sqlpackage#export-parameters-and-properties). Nástroj SQLPackage je dodáván s nejnovějšími verzemi [sql server management studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) a SQL Server Data [Tools](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt), nebo si můžete stáhnout nejnovější verzi [SQLPackage](https://docs.microsoft.com/sql/tools/sqlpackage-download).

Doporučujeme použití nástroje SqlPackage pro škálování a výkon ve většině produkčních prostředí. Příspěvek na blogu zákaznického poradního týmu SQL Serveru o migraci pomocí souborů BACPAC najdete v tématu popisujícím [migraci z SQL Serveru do služby SQL Database pomocí souborů BACPAC](https://blogs.msdn.microsoft.com/sqlcat/20../../migrating-from-sql-server-to-azure-sql-database-using-bacpac-files/).

Tento příklad ukazuje, jak exportovat databázi pomocí souboru SqlPackage.exe pomocí univerzálního ověřování služby Active Directory. Nahraďte hodnoty, které jsou specifické pro vaše prostředí.

```cmd
SqlPackage.exe /a:Export /tf:testExport.bacpac /scs:"Data Source=<servername>.database.windows.net;Initial Catalog=MyDB;" /ua:True /tid:"apptest.onmicrosoft.com"
```




## <a name="import-a-database-using-sqlpackage"></a>Import databáze pomocí balíčku SqlPackage

Informace o importu databáze serveru SQL Server pomocí nástroje příkazového řádku [SqlPackage](https://docs.microsoft.com/sql/tools/sqlpackage) naleznete [v tématu import parametrů a vlastností](https://docs.microsoft.com/sql/tools/sqlpackage#import-parameters-and-properties). SqlPackage má nejnovější [sql server management studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) a SQL Server datové [nástroje](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt). Můžete si také stáhnout nejnovější verzi [programu SqlPackage](https://docs.microsoft.com/sql/tools/sqlpackage-download).

Pro škálování a výkon doporučujeme použít SqlPackage ve většině produkčních prostředí, nikoli pomocí portálu Azure. Blog týmu zákaznického zpravodaje serveru SQL `BACPAC` Server o migraci pomocí souborů najdete v [tématu migrace z SQL Serveru do databáze Azure SQL pomocí souborů BACPAC](https://blogs.msdn.microsoft.com/sqlcat/2016/10/20/migrating-from-sql-server-to-azure-sql-database-using-bacpac-files/).

Následující příkaz SqlPackage importuje databázi **AdventureWorks2017** z místního úložiště na server Azure SQL Database. Vytvoří novou databázi s názvem **myMigratedDatabase** s úrovní služby **Premium** a cílem služby **P6.** Změňte tyto hodnoty podle potřeby pro vaše prostředí.

```cmd
sqlpackage.exe /a:import /tcs:"Data Source=<serverName>.database.windows.net;Initial Catalog=myMigratedDatabase>;User Id=<userId>;Password=<password>" /sf:AdventureWorks2017.bacpac /p:DatabaseEdition=Premium /p:DatabaseServiceObjective=P6
```

> [!IMPORTANT]
> Chcete-li se připojit k serveru SQL Database, který spravuje jednu databázi zpoza podnikové brány firewall, musí být brána firewall otevřena jako port 1433. Chcete-li se připojit ke spravované instanci, musíte mít [připojení bodu k síti](sql-database-managed-instance-configure-p2s.md) nebo připojení k expresní trase.

Tento příklad ukazuje, jak importovat databázi pomocí balíčku SqlPackage pomocí univerzálního ověřování služby Active Directory.

```cmd
sqlpackage.exe /a:Import /sf:testExport.bacpac /tdn:NewDacFX /tsn:apptestserver.database.windows.net /ua:True /tid:"apptest.onmicrosoft.com"
```

## <a name="performance-considerations"></a>Otázky výkonu

Rychlost exportu se liší v závislosti na mnoha faktorech (například tvaru dat), takže není možné předpovědět, jaká rychlost by měla být očekávána. SqlPackage může trvat značnou dobu, zejména pro velké databáze.

Chcete-li získat nejlepší výkon, můžete vyzkoušet následující strategie:

1. Ujistěte se, že v databázi není spuštěno žádné jiné zatížení. Vytvoření kopie před exportem může být nejlepším řešením, které zajistí, že nejsou spuštěny žádné jiné úlohy.
2. Zvýšit cíl úrovně databázové služby (SLO) pro lepší zpracování úlohy exportu (především čtení vstupně-v). Pokud je databáze aktuálně GP_Gen5_4, možná by s úlohou čtení pomohla kritická obchodní úroveň.
3. Ujistěte se, že existují seskupené indexy zejména pro velké tabulky. 
4. Virtuální počítače (VM) by měly být ve stejné oblasti jako databáze, aby se zabránilo síťovým omezením.
5. Virtuální počítače by měly mít SSD s odpovídající velikostí pro generování dočasných artefaktů před odesláním do úložiště objektů blob.
6. Virtuální počítače by měly mít odpovídající konfiguraci jádra a paměti pro konkrétní databázi.

## <a name="store-the-imported-or-exported-bacpac-file"></a>Uložte importované nebo exportované . SOUBOR BACPAC

Tá. Soubor BACPAC se dá ukládat v [objektech Blob Azure](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-overview)nebo [v Azure Files](https://docs.microsoft.com/azure/storage/files/storage-files-introduction). 

Chcete-li dosáhnout nejlepšího výkonu, použijte soubory Azure. SqlPackage pracuje se souborovým systémem, takže má přímý přístup k souborům Azure.

Chcete-li snížit náklady, použijte objekty BLOB Azure, které stojí méně než prémiová sdílená složka Azure. Bude však vyžadovat zkopírování [. BACPAC mezi](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/data-tier-applications#bacpac) objektem blob a místním systémem souborů před operací importu nebo exportu. V důsledku toho bude proces trvat déle.

Chcete-li nahrát nebo stáhnout . BACPAC, viz [Přenos dat pomocí úložiště Objektů BloB a](../storage/common/storage-use-azcopy-blobs.md)Přenos dat pomocí [AzCopy a ukládání souborů](../storage/common/storage-use-azcopy-files.md).

V závislosti na vašem prostředí může být nutné [konfigurovat brány firewall azure storage a virtuální sítě](../storage/common/storage-network-security.md).

## <a name="next-steps"></a>Další kroky

- Informace o tom, jak se připojit k importované databázi SQL a jak se jí dotazovat, najdete v [tématu Úvodní příručka: Azure SQL Database: Použití sql server management ového studia pro připojení a dotazování na data](sql-database-connect-query-ssms.md).
- Příspěvek na blogu zákaznického poradního týmu SQL Serveru o migraci pomocí souborů BACPAC najdete v tématu popisujícím [migraci z SQL Serveru do služby SQL Database pomocí souborů BACPAC](https://techcommunity.microsoft.com/t5/DataCAT/Migrating-from-SQL-Server-to-Azure-SQL-Database-using-Bacpac/ba-p/305407).
- Diskuse o celém procesu migrace databáze SQL Server, včetně doporučení pro výkon, naleznete [v tématu migrace databáze SQL Server do Azure SQL Database](sql-database-single-database-migrate.md).
- Informace o tom, jak bezpečně spravovat a sdílet klíče úložiště a sdílené přístupové podpisy, najdete v [tématu Průvodce zabezpečením úložiště Azure](https://docs.microsoft.com/azure/storage/common/storage-security-guide).
