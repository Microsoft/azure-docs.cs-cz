---
title: O SQL serveru virtuálních počítačích Azure s nástrojem Azure Backup | Dokumentace Microsoftu
description: Další informace o databázích SQL Server na virtuálním počítači Azure a funkce zkoumání této funkce.
services: backup
author: sachdevaswati
manager: vijayts
ms.service: backup
ms.topic: conceptual
ms.date: 03/14/2019
ms.author: sachdevaswati
ms.openlocfilehash: a57b52b3b0cc493fdde60e9bddfb0125ff2ce3e4
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2019
ms.locfileid: "58175960"
---
# <a name="about-sql-server-backup-in-azure-vms"></a>Informace o zálohování SQL serveru na virtuálních počítačích Azure

Databáze systému SQL Server jsou důležité úlohy, které vyžadují plánovaného bodu s nízkou obnovení (RPO) a dlouhodobé uchovávání. Zálohování databází systému SQL Server běžící na virtuálních počítačích Azure pomocí [Azure Backup](backup-overview.md).

## <a name="backup-process"></a>Proces zálohování

Toto řešení využívá nativní rozhraní API SQL pro zálohování databází SQL.

* Po zadání virtuálního počítače SQL serveru, který chcete chránit a dotazování databází v it, služba Azure Backup nainstaluje rozšíření zálohování úloh do virtuálního počítače podle názvu `AzureBackupWindowsWorkload`  rozšíření.
* Toto rozšíření se skládá z koordinátor a modulu plug-in SQL. Koordinátor je zodpovědná za aktivaci pracovní postupy pro různé operace, jako je například konfigurace zálohování, zálohování a obnovení, modul plug-in je zodpovědný za samotný datový tok.
* Aby bylo možné zjistit databáze na tomto virtuálním počítači, Azure Backup vytvoří účet `NT SERVICE\AzureWLBackupPluginSvc`. Tento účet se používá pro zálohování a obnovení a vyžaduje oprávnění správce systému SQL. Využívá službu Azure Backup `NT AUTHORITY\SYSTEM` účet za databázi zjišťování nebo dotaz, aby tento účet musí být veřejné přihlášení na SQL. Pokud jste nevytvořili virtuální počítač SQL Server na Azure Marketplace, k chybě může dojít **UserErrorSQLNoSysadminMembership**. V tomto případě [postupujte podle těchto pokynů](backup-azure-sql-database.md).
* Jakmile je aktivační událost Konfigurace ochrany pro vybrané databáze, služba backup nastaví koordinátor s plány zálohování a další podrobnosti zásad, které rozšíření ukládá místně na virtuálním počítači 
* V naplánovaném čase koordinátor komunikuje s modulem plug-in a začne streamování zálohovaná data ze služby SQL server pomocí infrastruktury virtuálních klientských počítačů.  
* Modul plug-in odešle data přímo do trezoru služby recovery services, proto není potom potřeba pracovní umístění. Data jsou zašifrované a uložené ve službě Azure Backup v účtech úložiště.
* Po dokončení přenosu dat se potvrdí koordinátor potvrzení u zálohovací služby.

  ![Architektura zálohování SQL](./media/backup-azure-sql-database/backup-sql-overview.png)

## <a name="before-you-start"></a>Než začnete

Než začnete, ověřte následující:

1. Ujistěte se, že máte instanci systému SQL Server běžící v Azure. Je možné [rychle vytvořit instanci systému SQL Server](../virtual-machines/windows/sql/quickstart-sql-vm-create-portal.md) na webu Marketplace.
2. Zkontrolujte [funkce zvážení](#feature-consideration) a [scénář podporu](#scenario_support).
3. [Projděte si nejčastější dotazy](faq-backup-sql-server.md) o tomto scénáři.


## <a name="feature-consideration-and-limitations"></a>Funkce aspektů a omezení

- Virtuální počítač s SQL serverem vyžaduje připojení k Internetu pro přístup k veřejným IP adresám Azure.
- Zálohy [distribuované skupiny dostupnosti](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/distributed-availability-groups?view=sql-server-2017) skupiny nejsou plně funkční.
- Vždy na převzetí služeb při selhání instance clusteru SQL Server (instancích Fci) nejsou podporovány pro zálohování.
- Zálohování serveru SQL Server musí být nakonfigurovaný na portálu nebo pomocí Powershellu.
- Operace zálohování a obnovení pro FCI zrcadlení databáze, snímky databází a databází nejsou podporovány.
- Databáze s velkým počtem souborů nelze chránit. Maximální počet souborů je 1000.
- Můžete zálohovat až ~ 2000 databází systému SQL Server v trezoru. Pokud máte více, vytvořte nový trezor.
- Můžete nakonfigurovat zálohování až 50 databázemi najednou; Toto omezení pomáhá optimalizovat zálohování zátěží.
- Nemůže chránit databáze s více než 1000 souborů.
- Doporučená velikost zajistit lepší výkon je 2TB.
- Chrání až 300 databází na serveru, pokud máte nakonfigurované pro každých 15 minut zálohy protokolu. Počet databází, které můžete zvýšit, pokud je četnost záloh je méně časté. Jsme s sebou nese sdílení podrobné způsob, jak vypočítat to za chvíli.


## <a name="scenario-support"></a>Scénáře podpory

**Podpora** | **Podrobnosti**
--- | ---
**Podporované nasazení** | Virtuální počítače Azure Marketplace SQL a mimo Marketplace (ručně nainstalovat SQL Server) se podporují virtuální počítače.
**Podporované zeměpisných oblastech** | Austrálie – jihovýchod (ASE), Východní Austrálie (AE) <br> Brazílie – jih (BRS)<br> Kanada – střed (CNC), Kanada – východ (CE)<br> Jihovýchodní Asie (SEA), východní Asie (EA) <br> USA – východ (EUS), východní USA 2 (EUS2), střed USA – západ (WCUS), USA – západ (WUS); Západní USA 2 (WUS 2) – sever (NCUS) střed USA střed USA (CUS) střed USA – Jih (SCUS) <br> India Central (INC), India South (INS) <br> Japonsko – východ (JPE), Japonsko – západ (JPW) <br> Korea – střed (KRC), Korea – Jih (KRS) <br> Severní Evropa (NE), západní Evropa <br> Jihoafrická republika – sever (SAN), Jihoafrická republika – západ (SAW.) <br> Spojené arabské emiráty – střed (UAC), Spojené arabské emiráty – sever (UAN) <br> Velká Británie – Jih (UKS), Velká Británie – západ (UKW)
**Podporované operační systémy** | Windows Server 2016, Windows Server 2012 R2, Windows Server 2012<br/><br/> Linux se momentálně nepodporuje.
**Podporované verze systému SQL Server** | SQL Server 2017; SQL Server 2016, SQL Server 2014, SQL Server 2012.<br/><br/> Enterprise, Standard, Web, Developer, Express.
**Podporované verze rozhraní .NET** | Rozhraní .NET framework 4.5.2 a vyšších nainstalovala do virtuálního počítače



## <a name="next-steps"></a>Další postup

- [Další informace o](backup-sql-server-database-azure-vms.md) zálohování databází systému SQL Server.
- [Další informace o](restore-sql-database-azure-vm.md) obnovení zálohovaných databáze systému SQL Server.
- [Další informace o](manage-monitor-sql-database-backup.md) Správa zálohování databází systému SQL Server.
