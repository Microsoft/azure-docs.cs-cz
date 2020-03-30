---
title: Zálohování a obnovení pro SQL Server ve virtuálních počítačích Azure | Dokumenty společnosti Microsoft
description: Popisuje požadavky na zálohování a obnovení pro databáze SQL Serveru spuštěné na virtuálních počítačích Azure.
services: virtual-machines-windows
documentationcenter: na
author: MikeRayMSFT
manager: craigg
editor: ''
tags: azure-resource-management
ms.assetid: 95a89072-0edf-49b5-88ed-584891c0e066
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 06/04/2018
ms.author: mikeray
ms.openlocfilehash: e4c126bbac73accb984f1040a7fea1740d919233
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79249773"
---
# <a name="backup-and-restore-for-sql-server-in-azure-virtual-machines"></a>Zálohování a obnovení pro SQL Server v Azure Virtual Machines

Tento článek obsahuje pokyny k možnostem zálohování a obnovení, které jsou k dispozici pro SQL Server spuštěný ve virtuálním počítači s Windows v Azure. Azure Storage udržuje tři kopie každého disku virtuálního počítače Azure, aby byla zaručena ochrana proti ztrátě dat nebo poškození fyzických dat. Na rozdíl od místních se tedy nemusíte zaměřovat na selhání hardwaru. Stále byste však měli zálohovat databáze serveru SQL Server, abyste je ochránili před chybami aplikace nebo uživatele, jako je například neúmyslné vložení nebo odstranění dat. V této situaci je důležité, aby bylo možné obnovit do určitého bodu v čase.

První část tohoto článku obsahuje přehled dostupných možností zálohování a obnovení. Následuje oddíly, které poskytují další informace o každé strategii.

## <a name="backup-and-restore-options"></a>Možnosti zálohování a obnovení

Následující tabulka obsahuje informace o různých možnostech zálohování a obnovení pro SQL Server spuštěný na virtuálních počítačích Azure:

| Strategie | Verze SQL | Popis |
|---|---|---|
| [Automatické zálohování](#automated) | 2014<br/> 2016<br/> 2017 | Automatické zálohování umožňuje naplánovat pravidelné zálohování pro všechny databáze na virtuálním počítači serveru SQL Server. Zálohy se ukládají v úložišti Azure po dobu až 30 dnů. Počínaje SQL Server 2016, automatické zálohování v2 nabízí další možnosti, jako je konfigurace ručníplánování a četnost úplné a protokol zálohy. |
| [Azure Backup pro virtuální počítače SQL](#azbackup) | 2008<br/> 2012<br/> 2014<br/> 2016<br/> 2017 | Azure Backup poskytuje možnost zálohování třídy Enterprise pro SQL Server spuštěný ve virtuálních počítačích Azure. Pomocí této služby můžete centrálně spravovat zálohy pro více serverů a tisíce databází. Databáze lze obnovit do určitého bodu v čase na portálu. Nabízí přizpůsobitelné zásady uchovávání informací, které mohou udržovat zálohy po celá léta. |
| [Ruční zálohování](#manual) | Všechny | V závislosti na verzi SQL Serveru existují různé techniky ručního zálohování a obnovení SQL Serveru spuštěného na virtuálním počítači Azure. V tomto scénáři jste zodpovědní za zálohování databází a umístění úložiště a správu těchto záloh. |

Následující části popisují jednotlivé možnosti podrobněji. Poslední část tohoto článku obsahuje souhrn ve formě matice funkcí.

## <a name="automated-backup"></a><a id="automated"></a>Automatické zálohování

Automatické zálohování poskytuje službu automatického zálohování pro edice SQL Server Standard a Enterprise spuštěnou ve virtuálním počítači se systémem Windows v Azure. Tuto službu poskytuje [rozšíření agenta SQL Server IaaS](virtual-machines-windows-sql-server-agent-extension.md), které se automaticky instaluje na ibi virtuálních strojů SQL Server Windows na webu Azure Portal.

Všechny databáze jsou zálohovány na účet úložiště Azure, který nakonfigurujete. Zálohy lze zašifrovat a uchovávat po dobu až 30 dnů.

SQL Server 2016 a vyšší virtuální počítače nabízejí další možnosti přizpůsobení s automatickou zálohování v2. Mezi tato vylepšení patří:

- Zálohy systémové databáze
- Plán ručního zálohování a časové okno
- Frekvence zálohování souborů úplného a protokolu

Chcete-li obnovit databázi, musíte najít požadované záložní soubory v účtu úložiště a provést obnovení na vašem virtuálním počítači SQL pomocí SQL Server Management Studio (SSMS) nebo Transact-SQL příkazy.

Další informace o konfiguraci automatického zálohování pro virtuální počítače SQL naleznete v jednom z následujících článků:

- **SQL Server 2016/2017:** [Automatické zálohování v2 pro virtuální počítače Azure](virtual-machines-windows-sql-automated-backup-v2.md)
- **SQL Server 2014**: [Automatické zálohování pro virtuální počítače SQL Server 2014](virtual-machines-windows-sql-automated-backup.md)

## <a name="azure-backup-for-sql-vms"></a><a id="azbackup"></a>Zálohování Azure pro virtuální počítače SQL

[Azure Backup](/azure/backup/) poskytuje možnost zálohování třídy Enterprise pro SQL Server spuštěný ve virtuálních počítačích Azure. Všechny zálohy jsou uloženy a spravovány v trezoru služby Recovery Services. Toto řešení poskytuje několik výhod, zejména pro podniky:

- **Zálohování nulové infrastruktury**: Není třeba spravovat záložní servery nebo umístění úložišť.
- **Škálování**: Chraňte mnoho virtuálních zařízení SQL a tisíce databází.
- **Průběžně splácet**: Tato funkce je samostatná služba poskytovaná službou Azure Backup, ale stejně jako u všech služeb Azure platíte jenom za to, co používáte.
- **Centrální správa a monitorování**: Centrálně spravujte všechny zálohy, včetně dalších úloh, které Azure Backup podporuje, z jediného řídicího panelu v Azure.
- **Zálohování a uchovávání založené na zásadách**: Vytvořte standardní zásady zálohování pro pravidelné zálohování. Vytvořte zásady uchovávání informací pro údržbu záloh po celá léta.
- **Podpora pro SQL Always On**: Zjišťování a ochrana SQL Server vždy na konfiguraci a ctít záložní skupiny zálohování zálohování zálohování zálohování.
- **15minutový cíl bodu obnovení (RPO):** Konfigurace záloh protokolu transakcí SQL až do 15 minut.
- **Bod v čase obnovení**: Pomocí portálu obnovit databáze do určitého bodu v čase bez nutnosti ručně obnovit více úplné, rozdílové a protokolzálohy.
- **Konsolidovaná e-mailová upozornění na selhání**: Konfigurace konsolidovaných e-mailových oznámení pro všechny chyby.
- **Řízení přístupu založené na rolích**: Určete, kdo může spravovat operace zálohování a obnovení prostřednictvím portálu.

Rychlý přehled o tom, jak to funguje spolu s demo, podívejte se na následující video:

> [!VIDEO https://www.youtube.com/embed/wmbANpHos_E]

Toto řešení Azure Backup pro virtuální počítače SQL je obecně dostupné. Další informace najdete [v tématu Zálohování databáze serveru SQL Server do Azure](../../../backup/backup-azure-sql-database.md).

## <a name="manual-backup"></a><a id="manual"></a>Ruční zálohování

Pokud chcete ručně spravovat operace zálohování a obnovení na virtuálních počítačích SQL, existuje několik možností v závislosti na verzi SQL Serveru, který používáte. Přehled zálohování a obnovení najdete v jednom z následujících článků založených na vaší verzi serveru SQL Server:

- [Zálohování a obnovení pro SQL Server 2016 a novější](https://docs.microsoft.com/sql/relational-databases/backup-restore/back-up-and-restore-of-sql-server-databases)
- [Zálohování a obnovení pro SQL Server 2014](https://msdn.microsoft.com/library/ms187048%28v=sql.120%29.aspx)
- [Zálohování a obnovení pro SQL Server 2012](https://msdn.microsoft.com/library/ms187048%28v=sql.110%29.aspx)
- [Zálohování a obnovení pro SQL Server SQL Server 2008 R2](https://msdn.microsoft.com/library/ms187048%28v=sql.105%29.aspx)
- [Zálohování a obnovení pro sql server 2008](https://msdn.microsoft.com/library/ms187048%28v=sql.100%29.aspx)

Následující části popisují několik možností ručního zálohování a obnovení podrobněji.

### <a name="backup-to-attached-disks"></a>Zálohování na připojené disky

Pro SQL Server spuštěný ve virtuálních počítačích Azure můžete použít nativní techniky zálohování a obnovení pomocí připojených disků na virtuálním počítači pro cíl záložních souborů. Počet disků, které můžete připojit k virtuálnímu počítači Azure, je však omezen na základě [velikosti virtuálního počítače](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). K dispozici je také režie správy disků, aby zvážila.

Příklad ručního vytvoření úplné zálohy databáze pomocí aplikace SQL Server Management Studio (SSMS) nebo Transact-SQL naleznete v [tématu Vytvoření úplné zálohy databáze](https://docs.microsoft.com/sql/relational-databases/backup-restore/create-a-full-database-backup-sql-server).

### <a name="backup-to-url"></a>Zálohování na adresu URL

Počínaje SQL Server 2012 SP1 CU2, můžete zálohovat a obnovit přímo do úložiště objektů Blob Microsoft Azure, které se také označuje jako zálohování na adresu URL. SQL Server 2016 také představil následující vylepšení pro tuto funkci:

| Vylepšení 2016 | Podrobnosti |
| --- | --- |
| **Prokládání** |Při zálohování na úložiště objektů blob Microsoft Azure podporuje SQL Server 2016 zálohování na více objektů BLOB, které umožňují zálohování velkých databází s maximální kapacitou 12,8 TB. |
| **Záloha snímků** |Díky použití snímků Azure sql server file-snapshot backup poskytuje téměř okamžité zálohování a rychlé obnovení pro databázové soubory uložené pomocí služby úložiště objektů Blob Azure. Tato funkce umožňuje zjednodušit zásady zálohování a obnovení. Zálohování snímků souboru také podporuje obnovení bodu v čase. Další informace najdete [v tématu Snapshot Backups for Database Files v Azure](https://docs.microsoft.com/sql/relational-databases/backup-restore/file-snapshot-backups-for-database-files-in-azure). |

Další informace naleznete v jednom z následujících článků založených na verzi serveru SQL Server:

- **SQL Server 2016/2017**: [Zálohování serveru SQL Server na adresu URL](https://docs.microsoft.com/sql/relational-databases/backup-restore/sql-server-backup-and-restore-with-microsoft-azure-blob-storage-service)
- **SQL Server 2014**: [Zálohování SQL Serveru 2014 na adresu URL](https://msdn.microsoft.com/library/jj919148%28v=sql.120%29.aspx)
- **SQL Server 2012**: [Zálohování SQL Serveru 2012 na adresu URL](https://msdn.microsoft.com/library/jj919148%28v=sql.110%29.aspx)

### <a name="managed-backup"></a>Spravované zálohování

Počínaje SQL Serverem 2014, spravované zálohování automatizuje vytváření záloh do úložiště Azure. Na pozadí spravované zálohování využívá funkci Zálohování na adresu URL popsanou v předchozí části tohoto článku. Spravované zálohování je také základní funkce, která podporuje službu automatického zálohování virtuálních vsystému SQL Server.

Počínaje SQL Server 2016, Spravované zálohování dostal další možnosti pro plánování, zálohování systémové databáze a úplné a protokolovací zálohování frekvence.

Další informace naleznete v jednom z následujících článků založených na vaší verzi serveru SQL Server:

- [Spravované zálohování do Microsoft Azure pro SQL Server 2016 a novější](https://docs.microsoft.com/sql/relational-databases/backup-restore/sql-server-managed-backup-to-microsoft-azure)
- [Spravované zálohování do Microsoft Azure pro SQL Server 2014](https://msdn.microsoft.com/library/dn449496%28v=sql.120%29.aspx)

## <a name="decision-matrix"></a>Rozhodovací matice

Následující tabulka shrnuje možnosti každé možnosti zálohování a obnovení pro virtuální počítače SQL Server v Azure.

|| **Automatické zálohování** | **Zálohování Azure pro SQL** | **Ruční zálohování** |
|---|---|---|---|
| Vyžaduje další službu Azure |   | ![Ano](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |   |
| Konfigurace zásad zálohování na webu Azure Portal | ![Ano](./media/virtual-machines-windows-sql-backup-recovery/yes.png) | ![Ano](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |   |
| Obnovení databází na webu Azure Portal |   | ![Ano](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |   |
| Správa více serverů v jednom řídicím panelu |   | ![Ano](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |   |
| Obnovení k určitému bodu v čase | ![Ano](./media/virtual-machines-windows-sql-backup-recovery/yes.png) | ![Ano](./media/virtual-machines-windows-sql-backup-recovery/yes.png) | ![Ano](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |
| 15minutový cíl bodu obnovení (RPO) | ![Ano](./media/virtual-machines-windows-sql-backup-recovery/yes.png) | ![Ano](./media/virtual-machines-windows-sql-backup-recovery/yes.png) | ![Ano](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |
| Krátkodobé zásady uchovávání záloh (dny) | ![Ano](./media/virtual-machines-windows-sql-backup-recovery/yes.png) | ![Ano](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |   |
| Dlouhodobé zásady uchovávání záloh (měsíce, roky) |   | ![Ano](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |   |
| Integrovaná podpora pro SQL Server stále zapnutý |   | ![Ano](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |   |
| Zálohování do účtů úložiště Azure | ![Ano](./media/virtual-machines-windows-sql-backup-recovery/yes.png)(automatické) | ![Ano](./media/virtual-machines-windows-sql-backup-recovery/yes.png)(automatické) | ![Ano](./media/virtual-machines-windows-sql-backup-recovery/yes.png)(spravovaný zákazník) |
| Správa úložných a záložních souborů | | ![Ano](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |  |
| Zálohování na připojené disky na virtuálním počítači |   |   | ![Ano](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |
| Centrální přizpůsobitelné sestavy zálohování |   | ![Ano](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |   |
| Konsolidovaná e-mailová upozornění na selhání |   | ![Ano](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |   |
| Přizpůsobení monitorování na základě protokolů Azure Monitoru |   | ![Ano](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |   |
| Sledování úloh zálohování pomocí skriptů SSMS nebo Transact-SQL | ![Ano](./media/virtual-machines-windows-sql-backup-recovery/yes.png) | ![Ano](./media/virtual-machines-windows-sql-backup-recovery/yes.png) | ![Ano](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |
| Obnovení databází pomocí skriptů SSMS nebo Transact-SQL | ![Ano](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |   | ![Ano](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |

## <a name="next-steps"></a>Další kroky

Pokud plánujete nasazení SQL Serveru ve virtuálním počítači Azure, najdete pokyny pro zřizování v následujícím průvodci: [Jak zřídit virtuální počítač Windows SQL Server na webu Azure Portal](virtual-machines-windows-portal-sql-server-provision.md).

Přestože zálohování a obnovení lze použít k migraci dat, existují potenciálně jednodušší cesty migrace dat na SQL Server na virtuálním počítači Azure. Úplnou diskusi o možnostech a doporučeních migrace najdete [v tématu migrace databáze na SQL Server na virtuálním počítači Azure](virtual-machines-windows-migrate-sql.md).
