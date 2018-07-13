---
title: Zálohování a obnovení pro SQL Server na virtuálních počítačích Azure | Dokumentace Microsoftu
description: Popisuje aspekty zálohování a obnovení databází systému SQL Server provozovaný v Azure Virtual Machines.
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
ms.openlocfilehash: d46c55f809d24529ea5deeb4d84de44dae876a4b
ms.sourcegitcommit: f606248b31182cc559b21e79778c9397127e54df
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/12/2018
ms.locfileid: "38968982"
---
# <a name="backup-and-restore-for-sql-server-in-azure-virtual-machines"></a>Zálohování a obnovení pro SQL Server v Azure Virtual Machines

Tento článek obsahuje pokyny týkající možnosti zálohování a obnovení k dispozici pro SQL Server spuštěná ve službě Windows Azure Virtual Machines. Azure Storage uchovává tři kopie všechny disky virtuálního počítače Azure k zajištění ochrany proti ztrátě dat nebo poškození dat fyzické. Na rozdíl od on-premises nepotřebujete tedy zaměřit na selhání hardwaru. Byste však stále zálohovat databáze SQL serveru k ochraně proti chybám aplikaci nebo uživatele, jako je například zvyšuje ochranu před nechtěnými data vložení nebo odstranění. V takovém případě je důležité mít možnost obnovení k určitému bodu v čase.

První část tohoto článku poskytuje přehled o dostupné zálohy a možnosti obnovení. Následuje oddíly, které poskytují další informace o jednotlivých strategií.

## <a name="backup-and-restore-options"></a>Možnosti zálohování a obnovení

Následující tabulka obsahuje informace o různých možnostech zálohování a obnovení pro SQL Server běžící na virtuálních počítačích Azure:

| Strategie | Verze SQL | Popis |
|---|---|---|---|
| [Automatizované zálohování](#automated) | 2014<br/> 2016<br/> 2017 | Automatizované zálohování můžete naplánovat pravidelné zálohování pro všechny databáze na virtuální počítač s SQL serverem. Zálohy jsou uložené v úložišti Azure po dobu až 30 dnů. Od verze SQL serveru 2016, automatizovaného zálohování v2 nabízí další možnosti, jako je například konfigurace ručního plánování a četnost úplný a záloh protokolu. |
| [Azure Backup pro virtuální počítače SQL](#azbackup) | 2012<br/> 2014<br/> 2016<br/> 2017 | Azure Backup poskytuje možnost zálohování podnikové třídy pro SQL Server spuštěných na virtuálních počítačích Azure. Pomocí této služby můžete centrálně spravovat zálohy pro více serverů a tisíce databází. Můžete obnovit databáze k určitému bodu v čase na portálu. Nabízí zásady uchovávání informací přizpůsobitelné, můžete zachovat zálohy po dobu let. Tato funkce je aktuálně ve verzi public preview. |
| [Ruční zálohy](#manual) | Vše | V závislosti na vaší verzi SQL serveru jsou různých technik vytváření ručně zálohování a obnovení SQL serveru běžícího na Virtuálním počítači Azure. V tomto scénáři jste zodpovědní za jak zálohovat vašich databází a umístění úložiště a správy tyto zálohy. |

Následující části popisují jednotlivé možnosti podrobněji. Koncová část tohoto článku poskytuje souhrn ve formě přehled funkcí.

## <a id="autoamted"></a> Automatizované zálohování

Automatizované zálohování poskytuje automatické zálohování služby pro edice SQL Server Standard a Enterprise spuštěná ve virtuálním počítači Windows Azure. Tato služba je poskytovaná [rozšíření agenta SQL Server IaaS](virtual-machines-windows-sql-server-agent-extension.md), které se automaticky instaluje do Image virtuálních počítačů Windows serveru SQL na webu Azure Portal.

Všechny databáze se zálohují do účtu služby Azure storage, který nakonfigurujete. Zálohování můžete šifrované a uchovávají po dobu až 30 dnů.

SQL Server 2016 a vyšší virtuálních počítačů nabízejí větší možnosti přizpůsobení pomocí automatizovaného zálohování v2. Tato vylepšení patří:

- Zálohování databáze systému
- Ruční plán zálohování a časový interval
- Frekvence zálohování souborů protokolu a úplné

Obnovení databáze, musíte najít požadované soubory záloh v účtu úložiště a proveďte obnovení na virtuálním počítači SQL pomocí SQL Server Management Studio (SSMS) nebo pomocí příkazů jazyka Transact-SQL.

Další informace o tom, jak nakonfigurovat automatizované zálohování pro virtuální počítače s SQL naleznete v následujících článcích:

- **SQL Server 2016 a 2017**: [automatizované zálohování v2 pro Azure Virtual Machines ](virtual-machines-windows-sql-automated-backup-v2.md)
- **SQL Server 2014**: [automatizované zálohování pro virtuální počítače s SQL Server 2014](virtual-machines-windows-sql-automated-backup.md)

## <a id="azbackup"></a> Azure Backup pro virtuální počítače s SQL (Public Preview)

[Azure Backup](/azure/backup/) poskytuje možnost zálohování podnikové třídy pro SQL Server běžící na virtuálních počítačích Azure. Všechny zálohy ukládání a správy v trezoru služby Recovery Services. Existuje několik výhod, které poskytuje toto řešení, hlavně pro velké podniky:

- **Zálohování bez potřeby jakékoli infrastruktury**: není nutné spravovat záložní servery nebo umístění úložiště.
- **Škálování**: ochrana mnoha virtuálních počítačů SQL a tisíce databází.
- **Průběžné platby**: Tato funkce je samostatná služba Azure Backup poskytuje, ale stejně jako u všech služeb Azure, platíte jenom za využité.
- **Centrální správy a monitorování**: centrálně spravovat všechny zálohy, včetně jiné úlohy, které podporuje Azure Backup z jednoho řídicího panelu v Azure.
- **Zásady zálohování a uchovávání řízené**: vytvořit standardní zásady zálohování pravidelného zálohování. Vytvořte zásady uchovávání informací, které chcete zachovat zálohy po dobu let.
- **Podpora pro SQL Always On**: zjišťování a ochranu konfigurace AlwaysOn SQL serveru a bude respektovat zálohování předvolbu zálohování skupiny dostupnosti.
- **Cíl bodu obnovení 15 minut (RPO)**: zálohy transakčního protokolu konfigurace SQL až každých 15 minut.
- **Pokud plánujete nasazení systému SQL Server na Virtuálním počítači Azure, najdete pokyny k zřizování v následujícím průvodci: **přidělení virtuálního počítače s Windows SQL serverem na webu Azure Portal**.
- **Přestože zálohování a obnovení lze použít k migraci dat, jsou potenciálně jednodušší cesty k datům migrace na SQL Server na Virtuálním počítači Azure.
- **Úplnou diskusi o možnostech migrace a doporučení, najdete v části **migrace databáze do služby SQL Server na Virtuálním počítači Azure**.

Rychlý přehled toho, jak to funguje, spolu s ukázkovou verzi z následujícího videa:

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE2dNbw]

Toto řešení Azure Backup pro virtuální počítače s SQL je aktuálně ve verzi public preview. Další informace najdete v tématu [zálohovat databáze SQL serveru do Azure](../../../backup/backup-azure-sql-database.md).

## <a id="manual"></a> Ruční zálohy

Pokud chcete ručně Správa zálohování a obnovení operací na virtuálních počítačích SQL, máte několik možností v závislosti na verzi SQL serveru, který používáte. Přehled zálohování a obnovení najdete v jednom z následujících článků založené na vaší verzi SQL serveru:

- [Zálohování a obnovení pro SQL Server 2016 a novější](https://docs.microsoft.com/sql/relational-databases/backup-restore/back-up-and-restore-of-sql-server-databases)
- [Zálohování a obnovení pro SQL Server 2014](https://msdn.microsoft.com/library/ms187048%28v=sql.120%29.aspx)
- [Zálohování a obnovení pro SQL Server 2012](https://msdn.microsoft.com/library/ms187048%28v=sql.110%29.aspx)
- [Zálohování a obnovení pro SQL Server SQL Server 2008 R2](https://msdn.microsoft.com/library/ms187048%28v=sql.105%29.aspx)
- [Zálohování a obnovení pro SQL Server 2008](https://msdn.microsoft.com/library/ms187048%28v=sql.100%29.aspx)

Následující části popisují několik ručního zálohování a obnovení možností podrobněji.

### <a name="backup-to-attached-disks"></a>Záloha na připojených disků

Pro SQL Server běžící na virtuálních počítačích Azure můžete použít nativní zálohování a obnovení techniky pomocí připojené disky na virtuálním počítači pro cíl záložní soubory. Existuje ale omezení počtu disků můžete připojit k virtuálnímu počítači Azure, na základě [velikost virtuálního počítače](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Je také režie na správu disků, které byste měli zvážit.

Příklad toho, jak ručně vytvořit úplnou zálohu databáze pomocí SQL Server Management Studio (SSMS) nebo příkazů jazyka Transact-SQL, najdete v části [vytvořit úplnou zálohu databáze](https://docs.microsoft.com/sql/relational-databases/backup-restore/create-a-full-database-backup-sql-server).

### <a name="backup-to-url"></a>Zálohování na adresu URL

Od verze SQL Server 2012 SP1 kumulativní aktualizaci 2, můžete zálohovat a obnovit přímo do Microsoft Azure Blob storage, který se označuje také jako zálohování na adresu URL. SQL Server 2016 zavedli tato vylepšení pro tuto funkci:

| Vylepšení 2016 | Podrobnosti |
| --- | --- |
| **Prokládání** |Při zálohování do služby Microsoft Azure blob storage, SQL Server 2016 podporuje zálohování až po několik přes bloby až po povolení zálohování velkých databází, až do maximálního počtu 12,8 TB. |
| **Zálohy snímku** |Pomocí Azure snímků zálohy snímků souborů SQL serveru poskytuje téměř okamžité zálohování a rychlé obnovení pro soubory databáze ukládat pomocí služby Azure Blob storage. Tato funkce umožňuje zjednodušit zálohování a obnovení zásad. Zálohy snímků souborů taky podporuje bodu v čase. Další informace najdete v tématu [zálohy snímků souborů databáze v Azure](https://docs.microsoft.com/sql/relational-databases/backup-restore/file-snapshot-backups-for-database-files-in-azure). |

Další informace najdete v tématu jeden z následujících článků založené na vaší verzi SQL serveru:

- **SQL Server 2016 a 2017**: [zálohování SQL serveru na adresu URL](https://docs.microsoft.com/sql/relational-databases/backup-restore/sql-server-backup-and-restore-with-microsoft-azure-blob-storage-service)
- **SQL Server 2014**: [zálohování SQL serveru 2014 na adresu URL](https://msdn.microsoft.com/library/jj919148%28v=sql.120%29.aspx)
- **SQL Server 2012**: [zálohování SQL serveru 2012 na adresu URL](https://msdn.microsoft.com/library/jj919148%28v=sql.110%29.aspx)

### <a name="managed-backup"></a>Spravovaná záloha

Od verze SQL Server 2014, spravovanou zálohu automatizuje vytváření záloh do služby Azure storage. Na pozadí spravovaného zálohování využívá zálohování na adresu URL funkce popsané v předchozí části tohoto článku. Spravovaná záloha je také základní funkce, která podporuje službu SQL Server VM automatizovaného zálohování.

Od SQL serveru 2016, spravovanou službu backup získali další možnosti pro plánování, systémová databáze, zálohy a úplné a četnost záloh protokolu.

Další informace najdete v tématu jednu z následujících článků založené na vaší verzi SQL serveru:

- [Spravované zálohování Microsoft Azure pro SQL Server 2016 a novější](https://docs.microsoft.com/sql/relational-databases/backup-restore/sql-server-managed-backup-to-microsoft-azure)
- [Spravovaná záloha do Microsoft Azure pro SQL Server 2014](https://msdn.microsoft.com/library/dn449496%28v=sql.120%29.aspx)

## <a name="decision-matrix"></a>Rozhodnutí matice

V následující tabulce najdete souhrn možností jednotlivých možností zálohování a obnovení pro virtuální počítače systému SQL Server v Azure.

|| **Automatizované zálohování** | **Azure Backup pro SQL** | **Ruční zálohy** |
|---|---|---|---|
| Vyžaduje další služby Azure |   | ![Ano](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |   |
| Na webu Azure portal nakonfigurovat zásady zálohování | ![Ano](./media/virtual-machines-windows-sql-backup-recovery/yes.png) | ![Ano](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |   |
| Obnovení databáze na webu Azure portal |   | ![Ano](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |   |
| Správa více serverů na jednom řídicím panelu |   | ![Ano](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |   |
| Obnovení k určitému bodu v čase | ![Ano](./media/virtual-machines-windows-sql-backup-recovery/yes.png) | ![Ano](./media/virtual-machines-windows-sql-backup-recovery/yes.png) | ![Ano](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |
| Cíl bodu obnovení 15 minut (RPO) | ![Ano](./media/virtual-machines-windows-sql-backup-recovery/yes.png) | ![Ano](./media/virtual-machines-windows-sql-backup-recovery/yes.png) | ![Ano](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |
| Krátkodobé zásadu uchovávání záloh (dny) | ![Ano](./media/virtual-machines-windows-sql-backup-recovery/yes.png) | ![Ano](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |   |
| Zásadu dlouhodobého uchovávání záloh (měsíců, let) |   | ![Ano](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |   |
| Integrovaná podpora pro SQL serveru Always On |   | ![Ano](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |   |
| Zálohování do účtů úložiště Azure: {0} | ![Ano](./media/virtual-machines-windows-sql-backup-recovery/yes.png)(automaticky) | ![Ano](./media/virtual-machines-windows-sql-backup-recovery/yes.png)(automaticky) | ![Ano](./media/virtual-machines-windows-sql-backup-recovery/yes.png)(spravovaná zákazníkem) |
| Správa souborů, úložiště a zálohování | | ![Ano](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |  |
| Záloha na připojené disky na virtuálním počítači |   |   | ![Ano](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |
| Centrální přizpůsobitelných sestav zálohování |   | ![Ano](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |   |
| Konsolidované e-mailové výstrahy při selhání |   | ![Ano](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |   |
| Přizpůsobit monitorování podle OMS |   | ![Ano](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |   |
| Monitorování úlohy zálohování pomocí aplikace SSMS nebo příkazů jazyka Transact-SQL skriptů | ![Ano](./media/virtual-machines-windows-sql-backup-recovery/yes.png) | ![Ano](./media/virtual-machines-windows-sql-backup-recovery/yes.png) | ![Ano](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |
| Obnovení databáze pomocí SSMS nebo příkazů jazyka Transact-SQL skriptů | ![Ano](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |   | ![Ano](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |

## <a name="next-steps"></a>Další postup

Pokud plánujete nasazení systému SQL Server na Virtuálním počítači Azure, najdete pokyny k zřizování v následujícím průvodci: [přidělení virtuálního počítače s Windows SQL serverem na webu Azure Portal](virtual-machines-windows-portal-sql-server-provision.md).

Přestože zálohování a obnovení lze použít k migraci dat, jsou potenciálně jednodušší cesty k datům migrace na SQL Server na Virtuálním počítači Azure. Úplnou diskusi o možnostech migrace a doporučení, najdete v části [migrace databáze do služby SQL Server na Virtuálním počítači Azure](virtual-machines-windows-migrate-sql.md).