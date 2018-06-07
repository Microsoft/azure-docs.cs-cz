---
title: Zálohování a obnovení pro SQL Server ve virtuálních počítačích Azure | Microsoft Docs
description: Popisuje aspekty zálohování a obnovení pro databáze systému SQL Server, které jsou spuštěné na virtuálních počítačích Azure.
services: virtual-machines-windows
documentationcenter: na
author: MikeRayMSFT
manager: craigg
editor: ''
tags: azure-resource-management
ms.assetid: 95a89072-0edf-49b5-88ed-584891c0e066
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 06/04/2018
ms.author: mikeray
ms.openlocfilehash: 4b90d1b9b2ee64722d3c92bcbd8fa205c9b59ebd
ms.sourcegitcommit: 6cf20e87414dedd0d4f0ae644696151e728633b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/06/2018
ms.locfileid: "34809603"
---
# <a name="backup-and-restore-for-sql-server-in-azure-virtual-machines"></a>Zálohování a obnovení pro SQL Server v Azure Virtual Machines

Tento článek obsahuje pokyny k zálohování a obnovení možnosti k dispozici pro systém SQL Server spuštěn v systému Windows Azure Virtual Machines. Azure úložiště udržuje tři kopie všechny disky virtuálního počítače Azure, pokud chcete zajistit ochranu proti ztrátě dat nebo poškození dat fyzické. Na rozdíl od místní, nepotřebujete tedy soustředit na selhání hardwaru. By však stále zálohování databáze SQL Server k ochraně proti chybám aplikaci nebo uživatele, jako je například vložení nechtěnému dat nebo odstranění. V takovém případě je potřeba mít možnost obnovit k určitému bodu v čase.

První část Tento článek obsahuje přehled možnosti k dispozici zálohování a obnovení. Následují oddíly, které poskytují další informace o jednotlivých strategie.

## <a name="backup-and-restore-options"></a>Možnosti zálohování a obnovení

Následující tabulka obsahuje informace o různých možností zálohování a obnovení pro SQL Server běžící na virtuálních počítačích Azure:

| Strategie | Verze SQL | Popis |
|---|---|---|---|
| [Automatizované zálohování](#automated) | 2014<br/> 2016<br/> 2017 | Automatizované zálohování umožňuje naplánovat pravidelné zálohování pro všechny databáze na virtuální počítač SQL Server. Zálohy jsou uložené v úložišti Azure po dobu 30 dnů. Od verze SQL Server 2016, automatizované zálohování v2 nabízí další možnosti, jako je například konfigurace ruční plánování a četnosti úplný a záloh protokolu. |
| [Zálohování Azure pro virtuální počítače SQL](#azbackup) | 2012<br/> 2014<br/> 2016<br/> 2017 | Azure Backup poskytuje možnost zálohování podnikové třídy pro SQL Server spuštěna ve virtuálních počítačích Azure. S touto službou můžete centrálně spravovat zálohy pro více serverů a tisíce databází. Databáze se dají obnovovat k určitému bodu v čase na portálu. Nabízí zásady přizpůsobitelné uchovávání informací, která můžete spravovat zálohy pro let. Tato funkce je aktuálně ve verzi public preview. |
| [Ruční zálohy](#manual) | Vše | V závislosti na vaší verzi serveru SQL existují různé postupy pro ruční zálohování a obnovení SQL Server běžící na virtuálním počítači Azure. V tomto scénáři jsou zodpovědní za jak jsou zálohované databáze a umístění úložiště a správu tyto zálohy. |

Následující části popisují jednotlivé možnosti podrobněji. Koncová část Tento článek obsahuje souhrn ve formě funkce matice.

## <a id="autoamted"></a> Automatizované zálohování

Automatizované zálohování poskytuje služby Automatické zálohování pro SQL Server Standard a Enterprise edice spuštění ve virtuálním počítači Windows Azure. Tato služba poskytuje [rozšíření agenta systému SQL Server IaaS](virtual-machines-windows-sql-server-agent-extension.md), které se automaticky instaluje do bitové kopie virtuálních počítačů Windows serveru SQL na portálu Azure.

Všechny databáze jsou zálohovány do účtu úložiště Azure, který nakonfigurujete. Zálohování můžete šifrovat a uchovávají po dobu 30 dnů.

SQL Server 2016 a vyšší virtuálních počítačů nabízí další možnosti přizpůsobení v2 automatizovaného zálohování. Tato vylepšení:

- Zálohování databáze systému
- Ruční plán zálohování a časové okno
- Četnost zálohování souborů protokolu a úplné

Chcete-li obnovit databázi, musíte najít požadované soubory zálohy v účtu úložiště a proveďte obnovení na virtuální počítač SQL pomocí SQL Server Management Studio (SSMS) nebo příkazy jazyka Transact-SQL.

Další informace o tom, jak nakonfigurovat automatizované zálohování pro virtuální počítače SQL najdete v následujících článcích:

- **SQL Server 2016 nebo 2017**: [automatizované zálohování v2 pro virtuální počítače Azure ](virtual-machines-windows-sql-automated-backup-v2.md)
- **SQL Server 2014**: [automatizované zálohování pro virtuální počítače se systémem SQL Server 2014](virtual-machines-windows-sql-automated-backup.md)

## <a id="azbackup"></a> Zálohování Azure pro virtuální počítače SQL (verze Public Preview)

[Zálohování Azure](/azure/backup/) poskytuje možnost zálohování podnikové třídy pro SQL Server běžící ve virtuálních počítačích Azure. Všechny zálohy ukládání a správy v trezoru služeb zotavení. Existuje několik výhod, které poskytuje toto řešení, zejména pro podniky:

- **Zálohování nula infrastruktury**: není nutné spravovat záložní servery nebo umístění úložiště.
- **Škálování**: ochrana hodně virtuálních počítačů SQL a tisíce databází.
- **Průběžné platby**: Tato možnost je samostatná služba poskytuje Azure Backup, ale stejně jako u všech služeb Azure, platíte jenom se používá.
- **Centrální správa a sledování**: centrálně spravovat všechny zálohy, včetně jiné úlohy, které podporuje Azure Backup z jednoho řídicího panelu v Azure.
- **Zásady zálohování a uchovávání řízené**: vytvořte standardní zásady zálohování pro pravidelné zálohování. Vytvořte zásady uchovávání informací k udržování zálohy let.
- **Podpora pro SQL Always On**: zjišťovat a chránit konfigurace SQL serveru Always On a bude respektovat zálohování předvoleb zálohování skupiny dostupnosti.
- **15 minut cíl pro bod obnovení (RPO)**: zálohování transakčního protokolu SQL nakonfigurovat až každých 15 minut.
- **Bod v době obnovení**: použití portálu k obnovení databází k určitému bodu v čase bez nutnosti ruční obnovení více úplné, rozdílové a protokolu zálohování.
- **Konsolidované e-mailové výstrahy pro selhání**: nakonfigurovat konsolidované e-mailová oznámení k jeho selhání.
- **Řízení přístupu na základě role**: určení, kdo může Správa zálohování a obnovení operací prostřednictvím portálu.

Rychlý přehled toho, jak to funguje společně s ukázku v následujícím videu:

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE2dNbw]

Toto řešení Azure Backup pro virtuální počítače SQL je aktuálně ve verzi public preview. Další informace najdete v tématu [zálohovat databázi systému SQL Server do Azure](../../../backup/backup-azure-sql-database.md).

## <a id="manual"></a> Ruční zálohy

Pokud chcete ručně Správa zálohování a obnovení operací na virtuální počítače SQL, máte několik možností v závislosti na verzi SQL serveru, kterou používáte. Přehled zálohování a obnovení najdete v jednom z následujících článků založené na vaší verzi systému SQL Server:

- [Zálohování a obnovení pro SQL Server 2016 a novější](https://docs.microsoft.com/en-us/sql/relational-databases/backup-restore/back-up-and-restore-of-sql-server-databases)
- [Zálohování a obnovení pro SQL Server 2014](https://msdn.microsoft.com/en-us/library/ms187048%28v=sql.120%29.aspx)
- [Zálohování a obnovení pro SQL Server 2012](https://msdn.microsoft.com/library/ms187048%28v=sql.110%29.aspx)
- [Zálohování a obnovení pro SQL Server SQL Server 2008 R2](https://msdn.microsoft.com/library/ms187048%28v=sql.105%29.aspx)
- [Zálohování a obnovení pro SQL Server 2008](https://msdn.microsoft.com/library/ms187048%28v=sql.100%29.aspx)

Následující části popisují několik ručního zálohování a obnovení možnosti podrobněji.

### <a name="backup-to-attached-disks"></a>Zálohování na připojené disky

Pro SQL Server běžící ve virtuálních počítačích Azure můžete pomocí nativního zálohování a obnovení techniky pomocí připojené disky na virtuální počítač pro cíl zálohování souborů. Existuje ale omezení počtu disků můžete připojit k Azure virtuálnímu počítači, na základě [velikost virtuálního počítače](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Je také režii správy disků vzít v úvahu.

Příklad toho, jak ručně vytvořit úplnou zálohu databáze pomocí SQL Server Management Studio (SSMS) nebo jazyka Transact-SQL naleznete v části [vytvoření úplné zálohy databáze](https://docs.microsoft.com/sql/relational-databases/backup-restore/create-a-full-database-backup-sql-server).

### <a name="backup-to-url"></a>Zálohování na adresu URL

Od verze SQL Server 2012 SP1 CU2, můžete zálohovat a obnovit přímo do úložiště objektů Blob Microsoft Azure, která se také označuje jako zálohování na adresu URL. SQL Server 2016 zavedli taky následující vylepšení pro tuto funkci:

| Vylepšení 2016 | Podrobnosti |
| --- | --- |
| **Proložení** |Při zálohování do úložiště objektů blob Microsoft Azure, SQL Server 2016 podporuje zálohování na více objektů BLOB k povolení zálohování velké databáze, až do maximálního počtu 12,8 TB. |
| **Zálohy snímku** |Prostřednictvím Azure snímků zálohy snímku souboru SQL Server poskytuje téměř okamžité zálohy a rychlé obnovení pro soubory databáze uložené pomocí služby Azure Blob storage. Tato funkce umožňuje zjednodušit zálohování a obnovení zásad. Zálohy snímku souboru také podporuje bod v době obnovení. Další informace najdete v tématu [snímek zálohy pro soubory databáze v Azure](https://docs.microsoft.com/en-us/sql/relational-databases/backup-restore/file-snapshot-backups-for-database-files-in-azure). |

Další informace najdete v tématu jeden z následujících článků založené na vaší verzi systému SQL Server:

- **SQL Server 2016 nebo 2017**: [zálohování systému SQL Server na adresu URL](https://docs.microsoft.com/sql/relational-databases/backup-restore/sql-server-backup-and-restore-with-microsoft-azure-blob-storage-service)
- **SQL Server 2014**: [zálohování systému SQL Server 2014 na adresu URL](https://msdn.microsoft.com/library/jj919148%28v=sql.120%29.aspx)
- **SQL Server 2012**: [zálohování systému SQL Server 2012 na adresu URL](https://msdn.microsoft.com/library/jj919148%28v=sql.110%29.aspx)

### <a name="managed-backup"></a>Spravované zálohování

Od verze SQL Server 2014, spravované zálohování zautomatizuje vytváření záloh do úložiště Azure. Na pozadí spravovaného zálohování využívá zálohování na adresu URL funkce popsané v předchozí části tohoto článku. Spravované zálohování je také základní funkce, která podporuje službu automatizované zálohování systému SQL Server virtuálního počítače.

Od systému SQL Server 2016, spravované zálohování získali další možnosti pro plánování, databáze systému a úplné zálohování a četnost záloh protokolu.

Další informace najdete v jednom z následujících článků založené na vaší verzi systému SQL Server:

- [Spravované zálohování do služby Microsoft Azure pro SQL Server 2016 nebo novější](https://docs.microsoft.com/sql/relational-databases/backup-restore/sql-server-managed-backup-to-microsoft-azure)
- [Spravované zálohování Microsoft Azure pro SQL Server 2014](https://msdn.microsoft.com/library/dn449496%28v=sql.120%29.aspx)

## <a name="decision-matrix"></a>Matice rozhodnutí

V následující tabulce najdete souhrn možností jednotlivých možností zálohování a obnovení pro virtuální počítače systému SQL Server v Azure.

|| **Automatizované zálohování** | **Zálohování Azure SQL** | **Ruční zálohy** |
|---|---|---|---|
| Vyžaduje další služby Azure |   | ![Ano](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |   |
| Nakonfigurujte zásady zálohování na portálu Azure | ![Ano](./media/virtual-machines-windows-sql-backup-recovery/yes.png) | ![Ano](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |   |
| Obnovení databází na portálu Azure |   | ![Ano](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |   |
| Správa více serverů v jednom řídicím |   | ![Ano](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |   |
| Obnovení k určitému bodu v čase | ![Ano](./media/virtual-machines-windows-sql-backup-recovery/yes.png) | ![Ano](./media/virtual-machines-windows-sql-backup-recovery/yes.png) | ![Ano](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |
| Cíl bodu obnovení 15 minut (RPO) | ![Ano](./media/virtual-machines-windows-sql-backup-recovery/yes.png) | ![Ano](./media/virtual-machines-windows-sql-backup-recovery/yes.png) | ![Ano](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |
| Krátkodobé zásady uchovávání záloh (dny) | ![Ano](./media/virtual-machines-windows-sql-backup-recovery/yes.png) | ![Ano](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |   |
| Dlouhodobé uchovávání záloh zásad (měsíců, roky) |   | ![Ano](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |   |
| Integrovaná podpora pro SQL serveru Always On |   | ![Ano](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |   |
| Zálohování k účtům Azure Storage | ![Ano](./media/virtual-machines-windows-sql-backup-recovery/yes.png)(automaticky) | ![Ano](./media/virtual-machines-windows-sql-backup-recovery/yes.png)(automaticky) | ![Ano](./media/virtual-machines-windows-sql-backup-recovery/yes.png)(spravovaný zákazníkem) |
| Správa úložiště a zálohování souborů | | ![Ano](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |  |
| Zálohování na připojené disky na virtuálním počítači |   |   | ![Ano](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |
| Centrální upravitelné zálohování sestavy |   | ![Ano](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |   |
| Konsolidované e-mailové výstrahy pro selhání |   | ![Ano](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |   |
| Přizpůsobit sledování založené na OMS |   | ![Ano](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |   |
| Monitorování úloh zálohování pomocí aplikace SSMS nebo Transact-SQL skriptů | ![Ano](./media/virtual-machines-windows-sql-backup-recovery/yes.png) | ![Ano](./media/virtual-machines-windows-sql-backup-recovery/yes.png) | ![Ano](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |
| Obnovení databáze pomocí aplikace SSMS nebo Transact-SQL skriptů | ![Ano](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |   | ![Ano](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |

## <a name="next-steps"></a>Další postup

Pokud plánujete nasazení systému SQL Server ve virtuálním počítači Azure, můžete nějakého najít zřizování pokyny v následujícím průvodci: [postup zřízení virtuálního počítače s Windows SQL Server na webu Azure portal](virtual-machines-windows-portal-sql-server-provision.md).

Zálohování a obnovení lze pro migraci dat, ale jsou potenciálně jednodušší cesty migrace dat do systému SQL Server na virtuálním počítači Azure. Úplnou diskusi o možnostech migrace a doporučení, najdete v části [migrace databáze do systému SQL Server na virtuálním počítači Azure](virtual-machines-windows-migrate-sql.md).