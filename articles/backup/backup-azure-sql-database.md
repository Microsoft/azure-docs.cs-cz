---
title: Zálohování databází SQL Serveru do Azure
description: Tento článek vysvětluje, jak zálohovat SQL Server do Azure. Článek také vysvětluje obnovení serveru SQL Server.
ms.topic: conceptual
ms.date: 06/18/2019
ms.openlocfilehash: 537257733d7693598fd8007da6ce12c28fbeb02a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79408756"
---
# <a name="about-sql-server-backup-in-azure-vms"></a>Informace o zálohování SQL Serverů ve virtuálních počítačích Azure

[Azure Backup](backup-overview.md) nabízí specializované řešení založené na streamu pro zálohování SQL Serveru spuštěného ve virtuálních počítačích Azure. Toto řešení je v souladu s výhodami služby Azure Backup v oblasti zálohování nulové infrastruktury, dlouhodobého uchovávání a centrální správy. Navíc poskytuje následující výhody speciálně pro SQL Server:

1. Zálohy s vědomím pracovního vytížení, které podporují všechny typy zálohování – úplné, rozdílové a protokolované
2. 15min RPO (cíl bodu obnovení) s častými zálohami protokolů
3. Zotavení v čase až do sekundy
4. Zálohování a obnovení na úrovni jednotlivých databází

Chcete-li zobrazit scénáře zálohování a obnovení, které dnes podporujeme, podívejte se na [matici podpory](sql-support-matrix.md#scenario-support).

## <a name="backup-process"></a>Proces zálohování

Toto řešení využívá nativní rozhraní API SQL k zálohování databází SQL.

* Jakmile zadáte virtuální počítač SQL Server, který chcete chránit a dotazovat se na databáze v něm, služba Azure `AzureBackupWindowsWorkload` Backup nainstaluje rozšíření pro zálohování úloh na virtuální ms podle rozšíření názvu.
* Toto rozšíření se skládá z koordinátora a modulu plug-sql. Zatímco koordinátor je zodpovědný za spouštění pracovních postupů pro různé operace, jako je konfigurace zálohování, zálohování a obnovení, plugin je zodpovědný za skutečný tok dat.
* Aby bylo možné zjistit databáze na tomto virtuálním `NT SERVICE\AzureWLBackupPluginSvc`počítači, Azure Backup vytvoří účet . Tento účet se používá pro zálohování a obnovení a vyžaduje oprávnění sql sysadmin. Účet `NT SERVICE\AzureWLBackupPluginSvc` je [účet virtuální služby](https://docs.microsoft.com/windows/security/identity-protection/access-control/service-accounts#virtual-accounts), a proto nevyžaduje žádnou správu hesel. Azure Backup využívá `NT AUTHORITY\SYSTEM` účet pro zjišťování databáze nebo dotazování, takže tento účet musí být veřejné přihlášení na SQL. Pokud jste nevytvořili virtuální počítač SQL Server z Azure Marketplace, může se zobrazit chyba **UserErrorSQLNoSysadminMembership**. Pokud k tomu [dojde, postupujte podle těchto pokynů](#set-vm-permissions).
* Jakmile spustíte konfiguraci ochrany ve vybraných databázích, služba zálohování nastaví koordinátora s plány zálohování a dalšípodrobnosti o zásadách, které rozšíření ukládá místně na virtuálním počítači.
* V naplánovaném čase koordinátor komunikuje s pluginem a začne streamovat záložní data ze serveru SQL pomocí VDI.  
* Plugin odesílá data přímo do trezoru služeb pro obnovení, čímž eliminuje potřebu pracovního umístění. Data jsou šifrovaná a uložená službou Azure Backup v účtech úložiště.
* Po dokončení přenosu dat koordinátor potvrdí potvrzení se službou zálohování.

  ![Architektura zálohování SQL](./media/backup-azure-sql-database/backup-sql-overview.png)

## <a name="before-you-start"></a>Než začnete

Než začnete, ověřte následující:

1. Ujistěte se, že máte v Azure spuštěnou instanci SQL Serveru. Můžete [rychle vytvořit instanci serveru SQL Server](../virtual-machines/windows/sql/quickstart-sql-vm-create-portal.md) na trhu.
2. Zkontrolujte [zvažování funkce](sql-support-matrix.md#feature-consideration-and-limitations) a [podporu scénářů](sql-support-matrix.md#scenario-support).
3. [Projděte si běžné otázky](faq-backup-sql-server.md) týkající se tohoto scénáře.

## <a name="set-vm-permissions"></a>Nastavení oprávnění virtuálních aplikací

  Při spuštění zjišťování na serveru SQL Server Azure Backup provádí následující akce:

* Přidá rozšíření AzureBackupWindowsWorkload.
* Vytvoří účet NT SERVICE\AzureWLBackupPluginSvc za účelem zjištění databází ve virtuálním počítači. Tento účet se používá pro zálohování a obnovení a vyžaduje oprávnění sql sysadmin.
* Zjistí databáze, které jsou spuštěny na virtuálním počítači, Azure Backup používá nt authority\system účet. Tento účet musí být veřejné přihlášení na SQL.

Pokud jste nevytvořili virtuální počítač SQL Server na Webu Azure Marketplace nebo pokud jste na SQL 2008 a 2008 R2, může se zobrazit chyba **UserErrorSQLNoSysadminMembership.**

Udělení oprávnění v případě **SQL 2008** a **2008 R2** spuštěné v systému Windows 2008 R2 naleznete [zde](#give-sql-sysadmin-permissions-for-sql-2008-and-sql-2008-r2).

U všech ostatních verzí opravte oprávnění pomocí následujících kroků:

  1. K přihlášení k sql serveru Management Studio (SSMS) použijte účet s oprávněními sql serveru sysadmin. Pokud nepotřebujete zvláštní oprávnění, ověřování systému Windows by mělo fungovat.
  2. Na serveru SQL Server otevřete složku **Zabezpečení/přihlášení.**

      ![Otevření složky Zabezpečení/přihlášení zobrazíte účty](./media/backup-azure-sql-database/security-login-list.png)

  3. Klepněte pravým tlačítkem myši na složku **Přihlášení** a vyberte **možnost Nové přihlášení**. V **pole Přihlásit se – Nový**vyberte **Hledat**.

      ![V dialogovém okně Přihlásit se – nový vyberte Hledat](./media/backup-azure-sql-database/new-login-search.png)

  4. Účet virtuální chod systému Windows **NT SERVICE\AzureWLBackupPluginSvc** byl vytvořen během registrace virtuálního počítače a fáze zjišťování SQL. Zadejte název účtu, jak je znázorněno v **zadejte název objektu, který chcete vybrat**. Chcete-li název přeložit, vyberte **Zkontrolovat názvy.** Klikněte na tlačítko **OK**.

      ![Chcete-li přeložit neznámý název služby, vyberte možnost Zkontrolovat názvy.](./media/backup-azure-sql-database/check-name.png)

  5. V **rolích serveru**zkontrolujte, zda je vybrána role **sysadmin.** Klikněte na tlačítko **OK**. Požadovaná oprávnění by nyní měla existovat.

      ![Zkontrolujte, zda je vybrána role serveru sysadmin.](./media/backup-azure-sql-database/sysadmin-server-role.png)

  6. Nyní přidružte databázi k trezoru služby Recovery Services. Na webu Azure Portal klikněte v seznamu **Chráněné servery** pravým tlačítkem myši na server, který je v chybovém stavu > **znovu objevit disky DB .**

      ![Ověření, zda má server příslušná oprávnění](./media/backup-azure-sql-database/check-erroneous-server.png)

  7. Zkontrolujte průběh v oblasti **Oznámení.** Po nalezení vybraných databází se zobrazí zpráva o úspěchu.

      ![Zpráva o úspěchu nasazení](./media/backup-azure-sql-database/notifications-db-discovered.png)

> [!NOTE]
> Pokud je na serveru SQL Server nainstalováno více instancí serveru SQL Server, je nutné do všech instancí SQL přidat oprávnění sysadmin pro **službu NT\AzureWLBackupPluginSvc.**

### <a name="give-sql-sysadmin-permissions-for-sql-2008-and-sql-2008-r2"></a>Udělení oprávnění sql sysadmin pro SQL 2008 a SQL 2008 R2

Přidejte přihlášení **nt autority\systému** a **služby NT\AzureWLBackupPluginSvc** do instance serveru SQL Server:

1. Přejděte na instanci serveru SQL Server v průzkumníku objektů.
2. Přejít na zabezpečení -> přihlášení
3. Klikněte pravým tlačítkem myši na přihlášení a klikněte na *nové přihlášení...*

    ![Nové přihlášení pomocí SSMS](media/backup-azure-sql-database/sql-2k8-new-login-ssms.png)

4. Přejděte na kartu Obecné a zadejte **NT AUTHORITY\SYSTEM** jako přihlašovací jméno.

    ![přihlašovací jméno pro SSMS](media/backup-azure-sql-database/sql-2k8-nt-authority-ssms.png)

5. Přejděte na *Role serveru* a zvolte *veřejné* role a role *sysadmin.*

    ![výběr rolí v SSMS](media/backup-azure-sql-database/sql-2k8-server-roles-ssms.png)

6. Přejděte na *Stav*. *Udělte* oprávnění k připojení k databázovému stroji a přihlášení jako *povoleno*.

    ![Udělení oprávnění v SSMS](media/backup-azure-sql-database/sql-2k8-grant-permission-ssms.png)

7. Klikněte na tlačítko OK.
8. Opakováním stejné posloupnosti kroků (1-7 výše) přidejte službu NT\AzureWLBackupPluginSvc přihlášení do instance serveru SQL Server. Pokud přihlášení již existuje, ujistěte se, že má roli serveru sysadmin a v části Stav má udělit oprávnění k připojení k databázovému stroji a Přihlášení jako povoleno.
9. Po udělení oprávnění **znovu objevit DBs** na **->** portálu: Vault zálohování infrastruktury **->** zatížení ve virtuálním počítači Azure:

    ![Opětovné objevení objektů DB na webu Azure Portal](media/backup-azure-sql-database/sql-rediscover-dbs.png)

Případně můžete automatizovat udělení oprávnění spuštěním následujících příkazů prostředí PowerShell v režimu správce. Název instance je ve výchozím nastavení nastaven na MSSQLSERVER. V případě potřeby změňte argument názvu instance ve skriptu:

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

* [Další informace o](backup-sql-server-database-azure-vms.md) zálohování databází serveru SQL Server.
* [Další informace o](restore-sql-database-azure-vm.md) obnovení zálohovaných databází serveru SQL Server.
* [Další informace o](manage-monitor-sql-database-backup.md) správě zálohovaných databází serveru SQL Server.
