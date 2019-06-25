---
title: Zálohování databází SQL serveru do Azure | Dokumentace Microsoftu
description: Tento kurz vysvětluje, jak k zálohování SQL serveru do Azure. Tento článek také popisuje obnovení SQL serveru.
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: tutorial
ms.date: 06/18/2019
ms.author: raynew
ms.openlocfilehash: cb8b188f8d5313852ce57481031faafc28e247b3
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/18/2019
ms.locfileid: "67204302"
---
# <a name="about-sql-server-backup-in-azure-vms"></a>Informace o zálohování SQL Serverů ve virtuálních počítačích Azure

Databáze systému SQL Server jsou důležité úlohy, které vyžadují plánovaného bodu s nízkou obnovení (RPO) a dlouhodobé uchovávání. Můžete zálohovat databáze systému SQL Server běžící na virtuálních počítačích Azure pomocí [Azure Backup](backup-overview.md).

## <a name="backup-process"></a>Proces zálohování

Toto řešení využívá nativní rozhraní API SQL pro zálohování databází SQL.

* Po zadání virtuálního počítače SQL serveru, který chcete chránit a dotazy pro databáze v něm služba Azure Backup nainstaluje rozšíření zálohování úloh do virtuálního počítače podle názvu `AzureBackupWindowsWorkload`  rozšíření.
* Toto rozšíření se skládá z koordinátor a modulu plug-in SQL. Koordinátor je zodpovědná za aktivaci pracovní postupy pro různé operace, jako je například konfigurace zálohování, zálohování a obnovení, modul plug-in je zodpovědný za samotný datový tok.
* Aby bylo možné zjistit databáze na tomto virtuálním počítači, Azure Backup vytvoří účet `NT SERVICE\AzureWLBackupPluginSvc`. Tento účet se používá pro zálohování a obnovení a vyžaduje oprávnění správce systému SQL. Využívá službu Azure Backup `NT AUTHORITY\SYSTEM` účet za databázi zjišťování nebo dotaz, aby tento účet musí být veřejné přihlášení na SQL. Pokud jste nevytvořili virtuální počítač SQL Server na Azure Marketplace, k chybě může dojít **UserErrorSQLNoSysadminMembership**. V tomto případě [postupujte podle těchto pokynů](backup-azure-sql-database.md).
* Jakmile je aktivační událost Konfigurace ochrany pro vybrané databáze, služba backup nastaví koordinátor s plány zálohování a další podrobnosti zásad, které rozšíření ukládá místně na virtuálním počítači 
* V naplánovaném čase koordinátor komunikuje s modulem plug-in a začne streamování zálohovaná data ze služby SQL server pomocí infrastruktury virtuálních klientských počítačů.  
* Modul plug-in odešle data přímo do trezoru služby recovery services, proto není potom potřeba pracovní umístění. Data jsou zašifrované a uložené ve službě Azure Backup v účtech úložiště.
* Po dokončení přenosu dat se potvrdí koordinátor potvrzení u zálohovací služby.

  ![Architektura zálohování SQL](./media/backup-azure-sql-database/backup-sql-overview.png)

## <a name="before-you-start"></a>Než začnete

Než začnete, ověřte, níže:

1. Ujistěte se, že máte instanci systému SQL Server běžící v Azure. Je možné [rychle vytvořit instanci systému SQL Server](../virtual-machines/windows/sql/quickstart-sql-vm-create-portal.md) na webu Marketplace.
2. Zkontrolujte [funkce zvážení](#feature-consideration-and-limitations) a [scénář podporu](#scenario-support).
3. [Projděte si nejčastější dotazy](faq-backup-sql-server.md) o tomto scénáři.

## <a name="scenario-support"></a>Scénáře podpory

**Podpora** | **Podrobnosti**
--- | ---
**Podporované nasazení** | Virtuální počítače Azure Marketplace SQL a mimo Marketplace (ručně nainstalovat SQL Server) se podporují virtuální počítače.
**Podporované zeměpisných oblastech** | Austrálie – jihovýchod (ASE), Východní Austrálie (AE) <br> Brazílie – jih (BRS)<br> Kanada – střed (CNC), Kanada – východ (CE)<br> Jihovýchodní Asie (SEA), východní Asie (EA) <br> USA – východ (EUS), východní USA 2 (EUS2), střed USA – západ (WCUS), USA – západ (WUS); Západní USA 2 (WUS 2) – sever (NCUS) střed USA střed USA (CUS) střed USA – Jih (SCUS) <br> India Central (INC), India South (INS) <br> Japonsko – východ (JPE), Japonsko – západ (JPW) <br> Korea – střed (KRC), Korea – Jih (KRS) <br> Severní Evropa (NE), západní Evropa <br> Velká Británie – Jih (UKS), Velká Británie – západ (UKW)
**Podporované operační systémy** | Windows Server 2016, Windows Server 2012 R2, Windows Server 2012<br/><br/> Linux se momentálně nepodporuje.
**Podporované verze systému SQL Server** | SQL Server 2017; SQL Server 2016, SQL Server 2014, SQL Server 2012.<br/><br/> Enterprise, Standard, Web, Developer, Express.
**Podporované verze rozhraní .NET** | Rozhraní .NET framework 4.5.2 a vyšších nainstalovala do virtuálního počítače

### <a name="support-for-sql-server-2008-and-sql-server-2008-r2"></a>Podpora pro SQL Server 2008 a SQL Server 2008 R2

Azure Backup nedávno oznámilo podporu [servery SQL Server SESTAVENÁ](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-2008-eos-extend-support) – SQL Server 2008 a SQL Server 2008 R2. Toto řešení je momentálně ve verzi preview pro SQL Server SESTAVENÁ a podporuje následující konfigurace:

1. SQL Server 2008 a SQL Server 2008 R2 a systémem Windows 2008 R2 SP1
2. Rozhraní .NET framework 4.5.2 a vyšším musí být nainstalovaný na virtuálním počítači
3. Nepodporuje zálohování pro FCI a zrcadlených databází

Všechny ostatní [funkce požadavky a omezení](#feature-consideration-and-limitations) platí pro tyto verze také. Tuto funkci do okamžiku je obecně dostupná vám nebude nic účtovat zákazníka.


## <a name="feature-consideration-and-limitations"></a>Funkce aspektů a omezení

- Zálohování serveru SQL Server lze nastavit na webu Azure Portal nebo **Powershellu**. Nepodporujeme rozhraní příkazového řádku.
- Řešení se podporuje na oba typy z [nasazení](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-model) – virtuální počítače Azure Resource Manageru a klasický virtuální počítač.
- Virtuální počítač s SQL serverem vyžaduje připojení k Internetu pro přístup k veřejným IP adresám Azure.
- SQL Server **Instance clusteru převzetí služeb při selhání (FCI)** a SQL serveru Always on Instance clusteru převzetí služeb při selhání se nepodporují.
- Zálohování a obnovení operací pro zrcadlení databáze a snímky databází nejsou podporovány.
- Použití více než jeden řešení zálohování k zálohování vašich samostatný systém SQL Server nebo instance SQL Always on skupina dostupnosti může vést k selhání zálohování; Nepoužívejte přiměřených.
- Dva uzly ve skupině dostupnosti samostatně pomocí stejného nebo jiného řešení zálohování, může také vést k selhání zálohování.
- Azure Backup podporuje pouze úplné a typy Copy-only úplná záloha pro **jen pro čtení** databází
- Databáze s velkým počtem souborů nelze chránit. Maximální počet souborů, která je podporována je **~ 1000**.  
- Můžete zálohovat až **~ 2000** databáze systému SQL Server v trezoru. V případě, že máte větší počet databází, které můžete vytvořit více trezorů.
- Zálohování můžete nakonfigurovat až **50** databáze v jednom patří; toto omezení pomáhá optimalizovat zálohování zátěží.
- Podporujeme až databází **2TB** velikost; pro větší než, který může objevovat další problémy s výkonem.
- Pokud chcete, aby představu o tom, kolik databází je možné chránit jeden server, musíme vezměte v úvahu faktory, jako je například šířky pásma, velikost virtuálního počítače, frekvenci zálohování, velikost databáze, atd. Pracujeme na Plánovač, který by vám vypočítat že vlastní tato čísla na vás. Jsme budete publikovat to za chvíli.
- V případě skupiny dostupnosti zálohy jsou prováděny z různých uzlech založené na několika faktorech. Zálohování chování pro skupinu dostupnosti je uveden níže.

### <a name="back-up-behavior-in-case-of-always-on-availability-groups"></a>Zálohování chování v případě vždy o skupinách dostupnosti

Doporučuje se, že zálohování je nakonfigurovaný na pouze jeden uzel skupinu dostupnosti. Zálohy musí být nakonfigurovaný vždy ve stejné oblasti jako na primárním uzlu. Jinými slovy budete vždy potřebovat primárního uzlu nacházet v oblasti, ve kterém jsou konfigurace zálohování. Pokud jsou všechny uzly skupinu dostupnosti ve stejné oblasti, ve kterém zálohování je nakonfigurované, není k dispozici žádné obavy.

**Pro skupinu AG mezi oblastmi**
- Bez ohledu na předvolby zálohování zálohování se neprovede z uzlů, které nejsou ve stejné oblasti, kde je nakonfigurované zálohování. Je to proto, že mezi různými oblastmi zálohování se nepodporují. Pokud máte pouze se 2 uzly a sekundárního uzlu je v jiné oblasti; v takovém případě zálohy budou i nadále provádět z primárního uzlu (Pokud je vaše předvolby zálohování sekundární pouze).
- V případě převzetí služeb při selhání do oblasti jiný než ten, ve kterém zálohování je nakonfigurované zálohování selže na uzlech v oblasti převzetím služeb při selhání.

Zálohy jsou prováděny z určitého uzlu (primární nebo sekundární), v závislosti na předvolby zálohování a zálohování typy (plně/rozdílové/log/kopírování – pouze úplná záloha).

- **Předvolby zálohování: Primární**

**Typ zálohování** | **Node**
    --- | ---
    Úplná | Primární
    Rozdílové | Primární
    Protokol |  Primární
    Úplné copy-Only |  Primární

- **Předvolby zálohování: Jenom sekundární**

**Typ zálohování** | **Node**
--- | ---
Úplná | Primární
Rozdílové | Primární
Protokol |  Sekundární
Úplné copy-Only |  Sekundární

- **Předvolby zálohování: Sekundární**

**Typ zálohování** | **Node**
--- | ---
Úplná | Primární
Rozdílové | Primární
Protokol |  Sekundární
Úplné copy-Only |  Sekundární

- **Žádná předvolba zálohování**

**Typ zálohování** | **Node**
--- | ---
Úplná | Primární
Rozdílové | Primární
Protokol |  Sekundární
Úplné copy-Only |  Sekundární

## <a name="set-vm-permissions"></a>Nastavit oprávnění pro virtuální počítač

  Když spustíte zjišťování na SQL serveru, Azure Backup provede následující akce:

* Přidá AzureBackupWindowsWorkload rozšíření.
* Vytvoří účet NT SERVICE\AzureWLBackupPluginSvc se zjistit databáze na virtuálním počítači. Tento účet se používá pro účely zálohování a obnovení a vyžaduje oprávnění správce systému SQL.
* Zjistí databáze, které jsou spuštěny na virtuálním počítači s Azure Backup používá účet NT AUTHORITY\SYSTEM. Tento účet musí být u veřejných přihlášení na SQL.

Pokud jste nevytvořili virtuální počítač SQL serverem na webu Azure Marketplace, nebo pokud používáte SQL 2008 a 2008 R2, může se zobrazit **UserErrorSQLNoSysadminMembership** chyby.

K udělení oprávnění u **SQL 2008** a **2008 R2** a systémem Windows 2008 R2, přečtěte si [tady](#give-sql-sysadmin-permissions-for-sql-2008-and-sql-2008-r2).

U všech ostatních verzí opravte oprávnění pomocí následujících kroků:

  1. Použijte účet s oprávněními správce systému SQL Server k přihlášení k serveru SQL Server Management Studio (SSMS). Pokud potřebujete speciální oprávnění, by měla fungovat ověřování Windows.
  2. Na serveru SQL Server, otevřete **zabezpečení/přihlášení** složky.

      ![Otevřít složku zabezpečení/přihlášení a zobrazit účty](./media/backup-azure-sql-database/security-login-list.png)

  3. Klikněte pravým tlačítkem myši **přihlášení** a pak zvolte položku **nového přihlašovacího jména**. V **přihlášení – nové**vyberte **hledání**.

      ![V části přihlášení – nové dialogové okno, vyberte hledání](./media/backup-azure-sql-database/new-login-search.png)

  4. Účet služby Windows virtuální **NT SERVICE\AzureWLBackupPluginSvc** jste vytvořili během registrace virtuálního počítače a fázi zjišťování SQL. Zadejte název účtu, jak je znázorněno v **zadejte název objektu k výběru**. Vyberte **Kontrola názvů** přeložit název. Klikněte na **OK**.

      ![Vyberte Zkontrolovat jména k přeložení názvu služby neznámý](./media/backup-azure-sql-database/check-name.png)

  5. V **role serveru**, ujistěte se, že **sysadmin** je vybrána role. Klikněte na **OK**. Teď by měla existovat požadovaná oprávnění.

      ![Ujistěte se, že je vybrána role serveru sysadmin](./media/backup-azure-sql-database/sysadmin-server-role.png)

  6. Nyní přidružte databázi k trezoru služby Recovery Services. Na webu Azure Portal v **chráněné servery** seznamu, klikněte pravým tlačítkem na server, který je v chybovém stavu > **opětovné zjištění databází**.

      ![Ověřte, že server má příslušná oprávnění](./media/backup-azure-sql-database/check-erroneous-server.png)

  7. Kontrola průběhu **oznámení** oblasti. Když se nacházejí ve vybraných databázích, zobrazí se zpráva o úspěchu.

      ![Zpráva o úspěšném nasazení](./media/backup-azure-sql-database/notifications-db-discovered.png)

> [!NOTE]
> Pokud SQL Server má několik instancí systému SQL Server nainstalován, pak je nutné přidat oprávnění sysadmin pro **NT Service\AzureWLBackupPluginSvc** účet pro všechny instance SQL.

### <a name="give-sql-sysadmin-permissions-for-sql-2008-and-sql-2008-r2"></a>Udělit oprávnění správce systému SQL pro SQL 2008 a SQL 2008 R2

Přidat **NT AUTHORITY\SYSTEM** a **NT Service\AzureWLBackupPluginSvc** přihlášení k instanci serveru SQL Server:

1. Přejděte Instance systému SQL Server v Průzkumníku objektů.
2. Přejděte do zabezpečení -> přihlášení
3. Klikněte pravým tlačítkem na přihlášení a klikněte na tlačítko *nového přihlašovacího jména...*

    ![Nové přihlášení pomocí aplikace SSMS](media/backup-azure-sql-database/sql-2k8-new-login-ssms.png)

4. Přejděte na kartu Obecné a zadejte **NT AUTHORITY\SYSTEM** jako přihlašovací jméno.

    ![přihlašovací jméno pro aplikace SSMS](media/backup-azure-sql-database/sql-2k8-nt-authority-ssms.png)

5. Přejděte na *role serveru* a zvolte *veřejné* a *sysadmin* role.

    ![Výběr rolí v aplikaci SSMS](media/backup-azure-sql-database/sql-2k8-server-roles-ssms.png)

6. Přejděte na *stav*. *Udělení* oprávnění pro připojení k databázovému stroji a přihlaste se jako *povoleno*.

    ![Udělení oprávnění v aplikaci SSMS](media/backup-azure-sql-database/sql-2k8-grant-permission-ssms.png)

7. Klikněte na tlačítko OK.
8. Opakujte stejný postupně jednotlivé kroky (1-7) přidání NT Service\AzureWLBackupPluginSvc přihlášení k instanci systému SQL Server. Pokud přihlašovací jméno už existuje, ujistěte se, že má role serveru sysadmin a v oblasti stav má udělení oprávnění pro připojení k databázovému stroji a přihlaste se na povoleno.
9. Po udělení oprávnění, **znovu zjistit databáze** na portálu: Trezor **->** zálohování infrastruktury **->** úlohy na virtuálním počítači Azure:

    ![Znovu zjistit databáze na webu Azure Portal](media/backup-azure-sql-database/sql-rediscover-dbs.png)

Alternativně můžete automatizovat udělení oprávnění spuštěním následujících příkazů Powershellu v režimu správce. Název instance je ve výchozím nastavení k MSSQLSERVER. Změnu instance název argumentu ve skriptu musí být:

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


## <a name="next-steps"></a>Další postup

* [Další informace o](backup-sql-server-database-azure-vms.md) zálohování databází systému SQL Server.
* [Další informace o](restore-sql-database-azure-vm.md) obnovení zálohovaných databáze systému SQL Server.
* [Další informace o](manage-monitor-sql-database-backup.md) Správa zálohování databází systému SQL Server.
