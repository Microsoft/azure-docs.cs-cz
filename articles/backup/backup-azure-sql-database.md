---
title: Zálohování SQL Server databází do Azure
description: Tento článek vysvětluje, jak zálohovat SQL Server do Azure. Článek také vysvětluje SQL Server obnovení.
ms.topic: conceptual
ms.date: 06/18/2019
ms.openlocfilehash: 510d9637031928e31abaa5f82a5bf58c6ef44719
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91316833"
---
# <a name="about-sql-server-backup-in-azure-vms"></a>Informace o zálohování SQL Serverů ve virtuálních počítačích Azure

[Azure Backup](backup-overview.md) nabízí specializované řešení založené na datových proudech k zálohování SQL Server spuštěných ve virtuálních počítačích Azure. Toto řešení se zarovnává s výhodami Azure Backup pro zálohování s nulovou infrastrukturou, dlouhodobou uchovávání a centrální správu. Kromě toho nabízí následující výhody, které jsou specifické pro SQL Server:

1. Zálohy s podporou úloh, které podporují všechny typy zálohování – úplný, rozdíl a protokol
2. 15 minut RPO (cíl bodu obnovení) s častými zálohami protokolů
3. Obnovení k určitému bodu v čase až do druhé
4. Zálohování a obnovení úrovně individuální databáze

Pokud chcete zobrazit scénáře zálohování a obnovení, které podporujeme dnes, přečtěte si v tabulce [podpory](sql-support-matrix.md#scenario-support).

## <a name="backup-process"></a>Proces zálohování

Toto řešení využívá rozhraní API systému SQL Native k převzetí záloh vašich databází SQL.

* Jakmile zadáte SQL Server virtuální počítač, který chcete chránit, a dotaz na databáze v něm, služba Azure Backup Service nainstaluje na virtuálním počítači rozšíření zálohování úlohy pomocí `AzureBackupWindowsWorkload` přípony názvu.
* Toto rozšíření se skládá z koordinátora a modulu plug-in SQL. I když je koordinátor zodpovědný za aktivaci pracovních postupů pro různé operace, jako je konfigurace zálohování, zálohování a obnovení, je za skutečný tok dat zodpovědný modul plug-in.
* Aby bylo možné zjišťovat databáze na tomto virtuálním počítači, Azure Backup účet vytvoří `NT SERVICE\AzureWLBackupPluginSvc` . Tento účet se používá pro zálohování a obnovení a vyžaduje oprávnění správce systému SQL. `NT SERVICE\AzureWLBackupPluginSvc`Účet je [účet virtuální služby](/windows/security/identity-protection/access-control/service-accounts#virtual-accounts), a proto nevyžaduje správu hesel. Azure Backup používá `NT AUTHORITY\SYSTEM` účet pro zjišťování nebo dotazování databáze, takže tento účet musí být veřejným přihlášením na SQL. Pokud jste virtuální počítač s SQL Serverem nevytvořili z Azure Marketplace, může se zobrazit chyba **UserErrorSQLNoSysadminMembership**. Pokud k tomu dojde, [postupujte podle těchto pokynů](#set-vm-permissions).
* Jakmile na vybraných databázích spustíte konfiguraci ochrany, služba zálohování nastaví koordinátora s plány zálohování a dalšími podrobnostmi zásad, které rozšíření ukládá do mezipaměti místně na virtuálním počítači.
* V naplánovaném čase koordinátor komunikuje s modulem plug-in a spustí streamování zálohovaných dat z SQL serveru pomocí infrastruktury virtuálních klientských počítačů (VDI).  
* Modul plug-in odesílá data přímo do trezoru Recovery Services, čímž eliminuje nutnost pracovní polohy. Data jsou zašifrovaná a uložená službou Azure Backup v účtech úložiště.
* Po dokončení přenosu dat koordinátor potvrdí potvrzení u služby zálohování.

  ![Architektura zálohování SQL](./media/backup-azure-sql-database/azure-backup-sql-overview.png)

## <a name="before-you-start"></a>Než začnete

Než začnete, ověřte následující požadavky:

1. Ujistěte se, že máte spuštěnou instanci SQL Server v Azure. Na webu Marketplace můžete [rychle vytvořit instanci SQL Server](../azure-sql/virtual-machines/windows/sql-vm-create-portal-quickstart.md) .
2. Seznamte se s [požadavky na funkce](sql-support-matrix.md#feature-considerations-and-limitations) a s [podporou scénářů](sql-support-matrix.md#scenario-support).
3. [Přečtěte si běžné otázky](faq-backup-sql-server.md) k tomuto scénáři.

## <a name="set-vm-permissions"></a>Nastavení oprávnění virtuálního počítače

  Když spustíte zjišťování na SQL Server, Azure Backup provede následující akce:

* Přidá rozšíření AzureBackupWindowsWorkload.
* Vytvoří účet NT SERVICE\AzureWLBackupPluginSvc pro zjišťování databází na virtuálním počítači. Tento účet se používá pro zálohování a obnovení a vyžaduje oprávnění správce systému SQL.
* Zjišťuje databáze, které běží na virtuálním počítači, Azure Backup používá účet NT AUTHORITY\SYSTEM. Tento účet musí být veřejným přihlašováním v SQL.

Pokud jste virtuální počítač SQL Server v Azure Marketplace nevytvořili nebo pokud jste na SQL 2008 nebo 2008 R2, může se zobrazit chyba **UserErrorSQLNoSysadminMembership** .

Informace o udělení oprávnění v případě **SQL 2008** a **2008 R2** běžících ve Windows 2008 R2 najdete [tady](#give-sql-sysadmin-permissions-for-sql-2008-and-sql-2008-r2).

U všech ostatních verzí opravte oprávnění pomocí následujících kroků:

  1. K přihlášení do SQL Server Management Studio (SSMS) použijte účet s oprávněními správce systému SQL Server. Pokud nepotřebujete zvláštní oprávnění, ověřování systému Windows by mělo fungovat.
  2. Na SQL Server otevřete složku **zabezpečení/přihlášení** .

      ![Otevřete složku zabezpečení/přihlášení a zobrazte si účty.](./media/backup-azure-sql-database/security-login-list.png)

  3. Klikněte pravým tlačítkem na složku **přihlášení** a vyberte **nové přihlašovací údaje**. V **přihlašování – nové**vyberte **Hledat**.

      ![V dialogovém okně přihlášení – nové vyberte Hledat.](./media/backup-azure-sql-database/new-login-search.png)

  4. Účet virtuální služby Windows **NT SERVICE\AzureWLBackupPluginSvc** se vytvořil během registrace virtuálního počítače a fáze zjišťování SQL. Zadejte název účtu, jak je uvedeno v **poli zadejte název objektu, který chcete vybrat**. Chcete-li název vyřešit, vyberte možnost **kontrolovat názvy** . Vyberte **OK**.

      ![Pokud chcete přeložit neznámý název služby, vyberte možnost kontrolovat názvy.](./media/backup-azure-sql-database/check-name.png)

  5. V části **role serveru**se ujistěte, že je vybraná role **sysadmin** . Vyberte **OK**. Nyní by měla existovat požadovaná oprávnění.

      ![Ujistěte se, že je vybraná role serveru sysadmin.](./media/backup-azure-sql-database/sysadmin-server-role.png)

  6. Nyní přidružte databázi k trezoru Recovery Services. V Azure Portal v seznamu **chráněné servery** klikněte pravým tlačítkem myši na server, který je v chybovém stavu > znovu **zjistit databáze**.

      ![Ověřte, že server má příslušná oprávnění.](./media/backup-azure-sql-database/check-erroneous-server.png)

  7. Podívejte se na průběh v **oznamovací** oblasti. Když se najde vybrané databáze, zobrazí se zpráva o úspěchu.

      ![Zpráva o úspěchu nasazení](./media/backup-azure-sql-database/notifications-db-discovered.png)

> [!NOTE]
> Pokud je v SQL Server nainstalovaná více instancí SQL Server, musíte do všech instancí SQL přidat oprávnění sysadmin pro účet **NT Service\AzureWLBackupPluginSvc** .

### <a name="give-sql-sysadmin-permissions-for-sql-2008-and-sql-2008-r2"></a>Udělení oprávnění správce systému SQL pro SQL 2008 a SQL 2008 R2

Přidejte do instance SQL Server přihlašovací údaje pro **NT AUTHORITY\SYSTEM** a **NT Service\AzureWLBackupPluginSvc** :

1. V Průzkumníku objektů přejdete na instanci SQL Server.
2. Přejít na zabezpečení-> přihlášení
3. Klikněte pravým tlačítkem na přihlašovací údaje a vyberte *nové přihlášení...*

    ![Nové přihlášení pomocí SSMS](media/backup-azure-sql-database/sql-2k8-new-login-ssms.png)

4. Přejděte na kartu Obecné a jako přihlašovací jméno zadejte **NT AUTHORITY\SYSTEM** .

    ![Přihlašovací jméno pro SSMS](media/backup-azure-sql-database/sql-2k8-nt-authority-ssms.png)

5. Přejít na *role serveru* a zvolit *veřejné* a *sysadmin* role.

    ![Výběr rolí v SSMS](media/backup-azure-sql-database/sql-2k8-server-roles-ssms.png)

6. Přejít na *stav*. *Udělte* oprávnění připojit se k databázovému stroji a přihlásit se jako *povolené*.

    ![Udělení oprávnění v SSMS](media/backup-azure-sql-database/sql-2k8-grant-permission-ssms.png)

7. Vyberte OK.
8. Zopakováním stejné posloupnosti kroků (1-7 výše) přidejte přihlášení NT Service\AzureWLBackupPluginSvc do instance SQL Server. Pokud přihlášení již existuje, ujistěte se, že má roli serveru sysadmin a v části stav uděluje oprávnění připojit se k databázovému stroji a přihlásit se jako povolené.
9. Po udělení oprávnění znovu **zjistit databáze** na portálu: úloha **->** infrastruktury zálohování trezoru **->** na virtuálním počítači Azure:

    ![Znovu zjišťovat databáze v Azure Portal](media/backup-azure-sql-database/sql-rediscover-dbs.png)

Případně můžete automatizovat oprávnění tím, že spustíte následující příkazy PowerShellu v režimu správce. Název instance je ve výchozím nastavení nastaven na hodnotu MSSQLSERVER. V případě potřeby změňte argument název instance ve skriptu:

```powershell
param(
    [Parameter(Mandatory=$false)]
    [string] $InstanceName = "MSSQLSERVER"
)
if ($InstanceName -eq "MSSQLSERVER")
{
    $fullInstance = $env:COMPUTERNAME   # In case it is the default SQL Server Instance
}
else
{
    $fullInstance = $env:COMPUTERNAME + "\" + $InstanceName   # In case of named instance
}
try
{
    sqlcmd.exe -S $fullInstance -Q "sp_addsrvrolemember 'NT Service\AzureWLBackupPluginSvc', 'sysadmin'" # Adds login with sysadmin permission if already not available
}
catch
{
    Write-Host "An error occurred:"
    Write-Host $_.Exception|format-list -force
}
try
{
    sqlcmd.exe -S $fullInstance -Q "sp_addsrvrolemember 'NT AUTHORITY\SYSTEM', 'sysadmin'" # Adds login with sysadmin permission if already not available
}
catch
{
    Write-Host "An error occurred:"
    Write-Host $_.Exception|format-list -force
}
```

## <a name="next-steps"></a>Další kroky

* [Přečtěte si o](backup-sql-server-database-azure-vms.md) zálohování SQL Server databází.
* [Přečtěte si informace o](restore-sql-database-azure-vm.md) obnovení zálohovaných SQL Server databází.
* [Přečtěte si o](manage-monitor-sql-database-backup.md) správě zálohovaných SQL Server databází.
