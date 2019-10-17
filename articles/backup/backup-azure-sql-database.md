---
title: Zálohování SQL Server databází do Azure
description: V tomto kurzu se dozvíte, jak zálohovat SQL Server do Azure. Článek také vysvětluje SQL Server obnovení.
author: dcurwin
manager: carmonm
ms.service: backup
ms.topic: tutorial
ms.date: 06/18/2019
ms.author: dacurwin
ms.openlocfilehash: 202d608e5d994cabd3d7e2e9a0887c8aab75af31
ms.sourcegitcommit: 77bfc067c8cdc856f0ee4bfde9f84437c73a6141
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/16/2019
ms.locfileid: "72437834"
---
# <a name="about-sql-server-backup-in-azure-vms"></a>Informace o zálohování SQL Serverů ve virtuálních počítačích Azure

SQL Server databáze jsou kritické úlohy, které vyžadují nízký cíl bodu obnovení (RPO) a dlouhodobé uchovávání. Můžete zálohovat SQL Server databáze běžící na virtuálních počítačích Azure pomocí [Azure Backup](backup-overview.md).

## <a name="backup-process"></a>Proces zálohování

Toto řešení využívá rozhraní API systému SQL Native k převzetí záloh vašich databází SQL.

* Jakmile zadáte SQL Server virtuální počítač, který chcete chránit, a dotaz na databáze v něm, služba Azure Backup Service nainstaluje na virtuálním počítači rozšíření zálohování úloh pomocí názvu `AzureBackupWindowsWorkload`.
* Toto rozšíření se skládá z koordinátora a modulu plug-in SQL. I když je koordinátor zodpovědný za aktivaci pracovních postupů pro různé operace, jako je konfigurace zálohování, zálohování a obnovení, je za skutečný tok dat zodpovědný modul plug-in.
* Aby bylo možné zjišťovat databáze na tomto virtuálním počítači, Azure Backup vytvoří účet `NT SERVICE\AzureWLBackupPluginSvc`. Tento účet se používá pro zálohování a obnovení a vyžaduje oprávnění správce systému SQL. Účet `NT SERVICE\AzureWLBackupPluginSvc` je [účet virtuální služby](https://docs.microsoft.com/windows/security/identity-protection/access-control/service-accounts#virtual-accounts), a proto nevyžaduje žádnou správu hesel. Azure Backup využívá účet `NT AUTHORITY\SYSTEM` pro zjišťování nebo dotaz databáze, takže tento účet musí být veřejným přihlášením na SQL. Pokud jste virtuální počítač SQL Server z Azure Marketplace nevytvořili, může se zobrazit chyba **UserErrorSQLNoSysadminMembership**. Pokud k tomu dojde, [postupujte podle těchto pokynů](#set-vm-permissions).
* Jakmile na vybraných databázích spustíte konfiguraci ochrany, služba zálohování nastaví koordinátora s plány zálohování a dalšími podrobnostmi zásad, které rozšíření ukládá do mezipaměti místně na virtuálním počítači.
* V naplánovaném čase koordinátor komunikuje s modulem plug-in a spustí streamování zálohovaných dat z SQL serveru pomocí infrastruktury virtuálních klientských počítačů (VDI).  
* Modul plug-in odesílá data přímo do trezoru služby Recovery Services. tím eliminuje nutnost pracovní umístění. Data jsou zašifrovaná a uložená službou Azure Backup v účtech úložiště.
* Po dokončení přenosu dat koordinátor potvrdí potvrzení u služby zálohování.

  ![Architektura zálohování SQL](./media/backup-azure-sql-database/backup-sql-overview.png)

## <a name="before-you-start"></a>Než začnete

Než začnete, ověřte následující:

1. Ujistěte se, že máte spuštěnou instanci SQL Server v Azure. Na webu Marketplace můžete [rychle vytvořit instanci SQL Server](../virtual-machines/windows/sql/quickstart-sql-vm-create-portal.md) .
2. Přečtěte si téma [posouzení funkcí](#feature-consideration-and-limitations) a [Podpora scénářů](#scenario-support).
3. [Přečtěte si běžné otázky](faq-backup-sql-server.md) k tomuto scénáři.

## <a name="scenario-support"></a>Podpora scénářů

**Podpora** | **Podrobnosti**
--- | ---
**Podporovaná nasazení** | Virtuální počítače Azure Marketplace a virtuální počítače mimo Marketplace (SQL Server ručně nainstalované) jsou podporované.
**Podporované zeměpisných oblastech** | Austrálie – jihovýchod (pomocného mechanismu), východní Austrálie (AE), Austrálie – střed (AC), Austrálie – střed 2 (AC) <br> Brazílie – jih (BRS)<br> Kanada – střed (CNC), Kanada – východ (CE)<br> Jižní Východní Asie (moře), Východní Asie (EA) <br> Východní USA (EUS), Východní USA 2 (EUS2), Středozápadní USA (WCUS), Západní USA (WUS); Západní USA 2 (WUS 2) Střed USA – sever (NCUS) Střed USA (kapacitní jednotky) Střed USA – jih (SCUS) <br> Indie – střed (INC), Indie – jih (in), Indie – západ <br> Japonsko – východ (JPE), Japonsko – západ (JPW) <br> Korea – střed (KRC), Korea – jih (KRS) <br> Severní Evropa (NE), Západní Evropa <br> Velká Británie – jih (UKS), Velká Británie – západ (UKW) <br> US Gov – Arizona, US Gov – Virginie, US Gov – Texas, US DoD – střed US DoD – východ <br> Německo – sever Německo – středozápad <br> Švýcarsko – sever Švýcarsko – západ
**Podporované operační systémy** | Windows Server 2016, Windows Server 2012 R2, Windows Server 2012<br/><br/> Linux není aktuálně podporován.
**Podporované verze SQL Server** | SQL Server 2017, jak je popsáno [zde](https://support.microsoft.com/lifecycle/search?alpha=SQL%20server%202017), SQL Server 2016 a SPS, jak je uvedeno [zde](https://support.microsoft.com/lifecycle/search?alpha=SQL%20server%202016%20service%20pack), SQL Server 2014, SQL Server 2012.<br/><br/> Enterprise, Standard, web, Developer, Express.
**Podporované verze rozhraní .NET** | .NET Framework 4.5.2 a vyšší nainstalované na virtuálním počítači

### <a name="support-for-sql-server-2008-and-sql-server-2008-r2"></a>Podpora SQL Server 2008 a SQL Server 2008 R2

Azure Backup nedávno oznámila podporu [EOSch SQL severs](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-2008-eos-extend-support) – SQL Server 2008 a SQL Server 2008 R2. Řešení je momentálně ve verzi Preview pro EOS SQL Server a podporuje následující konfiguraci:

1. SQL Server 2008 a SQL Server 2008 R2 spuštěné v systému Windows 2008 R2 SP1
2. Na virtuálním počítači musí být nainstalovaný .NET Framework 4.5.2 a novější.
3. Zálohování pro FCI a zrcadlené databáze se nepodporuje.

Uživatelům se tato funkce nebude účtovat až do doby, kdy je všeobecně dostupná. Všechny ostatní [požadavky a omezení funkcí](#feature-consideration-and-limitations) se vztahují také na tyto verze. Před konfigurací ochrany na SQL serverech 2008 a 2008 R2 si zajděte na [požadavky](backup-sql-server-database-azure-vms.md#prerequisites) .

## <a name="feature-consideration-and-limitations"></a>Aspekty a omezení funkcí

* Zálohování SQL Server můžete nakonfigurovat v Azure Portal nebo **PowerShellu**. Rozhraní příkazového řádku nepodporujeme.
* Řešení je podporované v obou druzích [nasazení](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-model) – Azure Resource Manager virtuálních počítačů a klasických virtuálních počítačů.
* Virtuální počítač se spuštěným SQL Server vyžaduje připojení k Internetu pro přístup k veřejným IP adresám Azure.
* SQL Server **instance clusteru s podporou převzetí služeb při selhání (FCI)** a instance clusteru SQL Server Always On se nepodporují.
* Operace zálohování a obnovení pro databáze zrcadlení a snímky databáze nejsou podporovány.
* Použití více než jednoho řešení zálohování k zálohování samostatné instance SQL Server nebo skupiny dostupnosti Always On SQL může způsobit selhání zálohování; upustí od tohoto postupu.
* Zálohování dvou uzlů skupiny dostupnosti jednotlivě se stejnými nebo různými řešeními může také vést k chybě zálohování.
* Pro databáze **jen pro čtení** podporuje Azure Backup jenom úplné typy úplné zálohy a jenom pro kopírování.
* Databáze s velkým počtem souborů není možné chránit. Maximální podporovaný počet souborů je **~ 1000**.  
* V trezoru můžete zálohovat až **~ 2000** SQL Server databází. Pro případ, že máte větší počet databází, můžete vytvořit více trezorů.
* Zálohu můžete nakonfigurovat až na **50** databází v jednom přechodu; Toto omezení pomáhá optimalizovat zatížení zálohování.
* Podporujeme databáze o velikosti až **2 TB** . v případě větší velikosti se mohou vycházet problémy s výkonem.
* Abychom měli smysl o tom, kolik databází je možné chránit na jeden server, musíme vzít v úvahu faktory, jako je šířka pásma, velikost virtuálních počítačů, četnost zálohování, velikost databáze atd. [Stáhněte](https://download.microsoft.com/download/A/B/5/AB5D86F0-DCB7-4DC3-9872-6155C96DE500/SQL%20Server%20in%20Azure%20VM%20Backup%20Scale%20Calculator.xlsx) si Plánovač prostředků, který poskytuje přibližný počet databází, které můžete mít na Server na základě prostředků virtuálních počítačů a zásad zálohování.
* V případě skupin dostupnosti jsou zálohy odebírány z různých uzlů na základě několika faktorů. Chování zálohování skupiny dostupnosti je shrnuto níže.

### <a name="back-up-behavior-in-case-of-always-on-availability-groups"></a>Chování při zálohování v případě skupin dostupnosti Always On

Doporučuje se, aby záloha byla nakonfigurovaná jenom v jednom uzlu ovládacího prvku AG. Zálohování by se mělo vždycky nakonfigurovat ve stejné oblasti jako primární uzel. Jinými slovy, vždy potřebujete, aby byl primární uzel přítomen v oblasti, ve které konfigurujete zálohování. Pokud jsou všechny uzly AG ve stejné oblasti, ve které je nakonfigurované zálohování, nezáleží na tom.

#### <a name="for-cross-region-ag"></a>Pro křížovou oblast AG

* Bez ohledu na předvolbu zálohování nedojde k zálohování z uzlů, které nejsou ve stejné oblasti, ve které je nakonfigurované zálohování. Důvodem je to, že zálohování mezi oblastmi není podporováno. Pokud máte pouze dva uzly a sekundární uzel je v jiné oblasti; v takovém případě budou zálohy i nadále provedeny z primárního uzlu (Pokud vaše preference zálohování není "sekundární").
* Pokud se převzetí služeb při selhání stane jinou oblastí než ta, ve které je zálohování nakonfigurované, zálohování se nepovede na uzlech v oblasti převzetí služeb při selhání.

V závislosti na předvolbách zálohování a typech zálohování (úplné/rozdílové/protokolované/kopie jsou úplné) se zálohují z konkrétního uzlu (primární/sekundární).

* **Předvolby zálohování: primární**

**Typ zálohování** | **Node**
    --- | ---
    Úplná | Primární
    Diferenciál | Primární
    Protokol |  Primární
    Pouze kopírování je úplné |  Primární

* **Předvolby zálohování: jenom sekundární**

**Typ zálohování** | **Node**
--- | ---
Úplná | Primární
Diferenciál | Primární
Protokol |  Sekundární
Pouze kopírování je úplné |  Sekundární

* **Předvolby zálohování: sekundární**

**Typ zálohování** | **Node**
--- | ---
Úplná | Primární
Diferenciál | Primární
Protokol |  Sekundární
Pouze kopírování je úplné |  Sekundární

* **Žádná předvolba zálohování**

**Typ zálohování** | **Node**
--- | ---
Úplná | Primární
Diferenciál | Primární
Protokol |  Sekundární
Pouze kopírování je úplné |  Sekundární

## <a name="set-vm-permissions"></a>Nastavení oprávnění virtuálních počítačů

  Když spustíte zjišťování na SQL Server, Azure Backup provede následující akce:

* Přidá rozšíření AzureBackupWindowsWorkload.
* Vytvoří účet NT SERVICE\AzureWLBackupPluginSvc pro zjišťování databází na virtuálním počítači. Tento účet se používá pro zálohování a obnovení a vyžaduje oprávnění správce systému SQL.
* Zjišťuje databáze, které běží na virtuálním počítači, Azure Backup používá účet NT AUTHORITY\SYSTEM. Tento účet musí být veřejným přihlašováním v SQL.

Pokud jste virtuální počítač SQL Server v Azure Marketplace nevytvořili nebo jste na SQL 2008 a 2008 R2, může se zobrazit chyba **UserErrorSQLNoSysadminMembership** .

Informace o udělení oprávnění v případě **SQL 2008** a **2008 R2** běžících ve Windows 2008 R2 najdete [tady](#give-sql-sysadmin-permissions-for-sql-2008-and-sql-2008-r2).

U všech ostatních verzí opravte oprávnění pomocí následujících kroků:

  1. K přihlášení do SQL Server Management Studio (SSMS) použijte účet s oprávněními správce systému SQL Server. Pokud nepotřebujete zvláštní oprávnění, ověřování systému Windows by mělo fungovat.
  2. Na SQL Server otevřete složku **zabezpečení/přihlášení** .

      ![Otevřete složku zabezpečení/přihlášení a zobrazte si účty.](./media/backup-azure-sql-database/security-login-list.png)

  3. Klikněte pravým tlačítkem na složku **přihlášení** a vyberte **nové přihlašovací údaje**. V **přihlašování – nové**vyberte **Hledat**.

      ![V dialogovém okně přihlášení – nové vyberte Hledat.](./media/backup-azure-sql-database/new-login-search.png)

  4. Účet virtuální služby Windows **NT SERVICE\AzureWLBackupPluginSvc** se vytvořil během registrace virtuálního počítače a fáze zjišťování SQL. Zadejte název účtu, jak je uvedeno v **poli zadejte název objektu, který chcete vybrat**. Chcete-li název vyřešit, vyberte možnost **kontrolovat názvy** . Klikněte na **OK**.

      ![Pokud chcete přeložit neznámý název služby, vyberte možnost kontrolovat názvy.](./media/backup-azure-sql-database/check-name.png)

  5. V části **role serveru**se ujistěte, že je vybraná role **sysadmin** . Klikněte na **OK**. Nyní by měla existovat požadovaná oprávnění.

      ![Ujistěte se, že je vybraná role serveru sysadmin.](./media/backup-azure-sql-database/sysadmin-server-role.png)

  6. Nyní přidružte databázi k trezoru Recovery Services. V Azure Portal v seznamu **chráněné servery** klikněte pravým tlačítkem myši na server, který je v chybovém stavu > znovu **zjistit databáze**.

      ![Ověřte, že server má příslušná oprávnění.](./media/backup-azure-sql-database/check-erroneous-server.png)

  7. Podívejte se na průběh v **oznamovací** oblasti. Když se najde vybrané databáze, zobrazí se zpráva o úspěchu.

      ![Zpráva o úspěšném nasazení](./media/backup-azure-sql-database/notifications-db-discovered.png)

> [!NOTE]
> Pokud je v SQL Server nainstalovaná více instancí SQL Server, musíte do všech instancí SQL přidat oprávnění sysadmin pro účet **NT Service\AzureWLBackupPluginSvc** .

### <a name="give-sql-sysadmin-permissions-for-sql-2008-and-sql-2008-r2"></a>Udělení oprávnění správce systému SQL pro SQL 2008 a SQL 2008 R2

Přidejte do instance SQL Server přihlašovací údaje pro **NT AUTHORITY\SYSTEM** a **NT Service\AzureWLBackupPluginSvc** :

1. V Průzkumníku objektů přejdete na instanci SQL Server.
2. Přejít na zabezpečení-> přihlášení
3. Klikněte pravým tlačítkem na přihlašovací údaje a klikněte na *nové přihlášení...*

    ![Nové přihlášení pomocí SSMS](media/backup-azure-sql-database/sql-2k8-new-login-ssms.png)

4. Přejděte na kartu Obecné a jako přihlašovací jméno zadejte **NT AUTHORITY\SYSTEM** .

    ![přihlašovací jméno pro SSMS](media/backup-azure-sql-database/sql-2k8-nt-authority-ssms.png)

5. Přejít na *role serveru* a zvolit *veřejné* a *sysadmin* role.

    ![Výběr rolí v SSMS](media/backup-azure-sql-database/sql-2k8-server-roles-ssms.png)

6. Přejít na *stav*. *Udělte* oprávnění připojit se k databázovému stroji a přihlásit se jako *povolené*.

    ![Udělení oprávnění v SSMS](media/backup-azure-sql-database/sql-2k8-grant-permission-ssms.png)

7. Klikněte na tlačítko OK.
8. Zopakováním stejné posloupnosti kroků (1-7 výše) přidejte přihlášení NT Service\AzureWLBackupPluginSvc do instance SQL Server. Pokud přihlášení již existuje, ujistěte se, že má roli serveru sysadmin a v části stav uděluje oprávnění připojit se k databázovému stroji a přihlásit se jako povolené.
9. Po udělení oprávnění znovu **zjišťovat databáze** na portálu **@no__t** : úloha **->** infrastruktury zálohování na virtuálním počítači Azure:

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
