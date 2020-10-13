---
title: Zálohování a obnovení pro SQL Server na virtuálních počítačích Azure | Microsoft Docs
description: Popisuje požadavky na zálohování a obnovení pro SQL Server databáze běžící na Azure Virtual Machines.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
editor: ''
tags: azure-resource-management
ms.assetid: 95a89072-0edf-49b5-88ed-584891c0e066
ms.service: virtual-machines-sql
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 06/04/2018
ms.author: mikeray
ms.openlocfilehash: a4965c587cb2f42c8411d48af2417dd3fe3d5edb
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91360782"
---
# <a name="backup-and-restore-for-sql-server-on-azure-vms"></a>Zálohování a obnovení pro SQL Server na virtuálních počítačích Azure
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Tento článek poskytuje pokyny k možnostem zálohování a obnovení, které jsou k dispozici pro SQL Server spuštěné na virtuálním počítači s Windows (VM) v Azure. Azure Storage udržuje tři kopie každého disku virtuálního počítače Azure, aby se zajistila ochrana před ztrátou dat nebo poškozením fyzického data. Proto se na rozdíl od SQL Server v místním prostředí nemusíte soustředit na selhání hardwaru. Měli byste ale pořád zálohovat SQL Server databáze, abyste chránili před chybami aplikací nebo uživatelů, jako je například nechtěné vkládání nebo odstraňování dat. V takové situaci je důležité, abyste se mohli vrátit k určitému bodu v čase.

První část tohoto článku poskytuje přehled dostupných možností zálohování a obnovení. Následují oddíly, které obsahují další informace o každé strategii.

## <a name="backup-and-restore-options"></a>Možnosti zálohování a obnovení

Následující tabulka poskytuje informace o různých možnostech zálohování a obnovení pro SQL Server na virtuálních počítačích Azure:

| Strategie | Verze SQL | Description |
|---|---|---|
| [Automatizované zálohování](#automated) | 2014<br/> 2016<br/> 2017 | Automatizované zálohování umožňuje naplánovat pravidelné zálohování pro všechny databáze na SQL Serverm virtuálním počítači. Zálohy se ukládají v Azure Storage po dobu až 30 dnů. Počínaje SQL Server 2016 nabízí automatizované zálohování v2 další možnosti, jako je třeba konfigurace ručního plánování a četnost úplných záloh a zálohování protokolů. |
| [Azure Backup pro virtuální počítače SQL](#azbackup) | 2008<br/> 2012<br/> 2014<br/> 2016<br/> 2017 | Azure Backup poskytuje možnost zálohování podnikové třídy pro SQL Server na virtuálních počítačích Azure. Pomocí této služby můžete centrálně spravovat zálohy pro víc serverů a tisíců databází. Databáze je možné obnovit do určitého bodu v čase na portálu. Nabízí přizpůsobitelné zásady uchovávání informací, které můžou uchovávat zálohy po rocích. |
| [Ruční zálohování](#manual) | Vše | V závislosti na vaší verzi SQL Server existují různé postupy pro ruční zálohování a obnovení SQL Server na virtuálním počítači Azure. V tomto scénáři zodpovídáte za zálohování databází a umístění úložiště a správu těchto záloh. |

V následujících částech jsou jednotlivé možnosti podrobněji popsány. Poslední část tohoto článku poskytuje souhrn ve formě matice funkcí.

## <a name="automated-backup"></a><a id="automated"></a> Automatizované zálohování

Automatizované zálohování poskytuje službu Automatické zálohování pro edice SQL Server Standard a Enterprise běžící na virtuálním počítači s Windows v Azure. Tuto službu poskytuje [rozšíření agenta SQL Server IaaS](sql-server-iaas-agent-extension-automate-management.md), které se automaticky instaluje do SQL Server imagí virtuálních počítačů s Windows v Azure Portal.

Všechny databáze se zálohují do účtu služby Azure Storage, který nakonfigurujete. Zálohy je možné šifrovat a uchovávat po dobu až 30 dnů.

SQL Server 2016 a novějších virtuálních počítačů nabízí další možnosti přizpůsobení pomocí automatizovaného zálohování v2. Mezi tato vylepšení patří:

- Zálohy systémových databází
- Plán ručního zálohování a časový interval
- Úplná četnost záloh souborů a souborů protokolu

Chcete-li obnovit databázi, je nutné najít požadované záložní soubory v účtu úložiště a provést obnovení na VIRTUÁLNÍm počítači SQL pomocí příkazů SQL Server Management Studio (SSMS) nebo Transact-SQL.

Další informace o tom, jak nakonfigurovat automatizované zálohování pro virtuální počítače SQL, najdete v jednom z následujících článků:

- **SQL Server 2016/2017**: [automatizované zálohování v2 pro Azure Virtual Machines](automated-backup.md)
- **SQL Server 2014**: [automatizované zálohování pro SQL Server 2014 Virtual Machines](automated-backup-sql-2014.md)

## <a name="azure-backup-for-sql-vms"></a><a id="azbackup"></a> Azure Backup pro virtuální počítače SQL

[Azure Backup](/azure/backup/) poskytuje možnost zálohování podnikové třídy pro SQL Server na virtuálních počítačích Azure. Všechny zálohy se ukládají a spravují v Recovery Services trezoru. Toto řešení poskytuje několik výhod, zejména pro podniky:

- **Zálohování s nulovou infrastrukturou**: nemusíte spravovat záložní servery nebo umístění úložiště.
- **Škálování**: Chraňte mnoho virtuálních počítačů SQL a tisíc databází.
- Průběžné **platby**: Tato funkce je samostatná služba poskytovaná Azure Backup, ale stejně jako u všech služeb Azure platíte jenom za to, co využijete.
- **Centrální správa a monitorování**: centrálně Spravujte všechna vaše zálohování, včetně dalších zatížení, které Azure Backup podporuje, z jednoho řídicího panelu v Azure.
- **Zálohování a uchovávání řízených zásadou**: vytvořte standardní zásady zálohování pro pravidelné zálohování. Vytvořte zásady uchovávání informací pro zachování záloh po roky.
- **Podpora pro SQL Always On: umožňuje**detekovat a chránit SQL Server konfigurace služby Always On a respektovat předvolbu zálohování skupiny dostupnosti zálohování.
- **15 minut cíl bodu obnovení (RPO)**: Nakonfigurujte zálohy protokolu transakcí SQL až na každých 15 minut.
- Obnovení k určitému **bodu v čase**: použijte portál k obnovení databází do konkrétního bodu v čase, aniž byste museli ručně obnovit několik úplných, rozdílových a mezicelých záloh protokolů.
- **Konsolidovaná e-mailová upozornění pro chyby**: Nakonfigurujte konsolidovaná e-mailová oznámení pro případné chyby.
- **Řízení přístupu na základě role**: Určete, kdo může spravovat operace zálohování a obnovení prostřednictvím portálu.

Rychlý přehled toho, jak funguje společně s ukázkou, najdete v tomto videu:

> [!VIDEO https://www.youtube.com/embed/wmbANpHos_E]

Toto řešení Azure Backup pro virtuální počítače s SQL je všeobecně dostupné. Další informace najdete v tématu [zálohování databáze SQL Server do Azure](../../../backup/backup-azure-sql-database.md).

## <a name="manual-backup"></a><a id="manual"></a> Ruční zálohování

Pokud chcete na virtuálních počítačích SQL ručně spravovat operace zálohování a obnovení, je v závislosti na používané verzi SQL Server k dispozici několik možností. Přehled zálohování a obnovení najdete v jednom z následujících článků na základě vaší verze SQL Server:

- [Zálohování a obnovení pro SQL Server 2016 a novější](https://docs.microsoft.com/sql/relational-databases/backup-restore/back-up-and-restore-of-sql-server-databases)
- [Zálohování a obnovení pro SQL Server 2014](https://msdn.microsoft.com/library/ms187048%28v=sql.120%29.aspx)
- [Zálohování a obnovení pro SQL Server 2012](https://msdn.microsoft.com/library/ms187048%28v=sql.110%29.aspx)
- [Zálohování a obnovení pro SQL Server SQL Server 2008 R2](https://msdn.microsoft.com/library/ms187048%28v=sql.105%29.aspx)
- [Zálohování a obnovení pro SQL Server 2008](https://msdn.microsoft.com/library/ms187048%28v=sql.100%29.aspx)

Následující části popisují několik možností ručního zálohování a obnovení ve více podrobnostech.

### <a name="backup-to-attached-disks"></a>Zálohování na připojené disky

V případě SQL Server na virtuálních počítačích Azure můžete použít nativní techniky zálohování a obnovení pomocí připojených disků na virtuálním počítači pro cílové záložní soubory. Existuje ale omezení počtu disků, které můžete připojit k virtuálnímu počítači Azure, a to na základě [velikosti virtuálního počítače](../../../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Je také potřeba vzít v úvahu režii správy disků.

Příklad, jak ručně vytvořit úplnou zálohu databáze pomocí SQL Server Management Studio (SSMS) nebo Transact-SQL, najdete v tématu [vytvoření úplné zálohy databáze](https://docs.microsoft.com/sql/relational-databases/backup-restore/create-a-full-database-backup-sql-server).

### <a name="backup-to-url"></a>Záloha na adresu URL

Počínaje SQL Server 2012 SP1 CU2 můžete zálohovat a obnovovat přímo do Microsoft Azure úložiště objektů blob, které se také označuje jako záloha na adresu URL. SQL Server 2016 také pro tuto funkci přináší následující vylepšení:

| 2016 vylepšení | Podrobnosti |
| --- | --- |
| **Prokládáním** |Při zálohování na úložiště objektů BLOB Microsoft Azure SQL Server 2016 podporuje zálohování do více objektů blob, aby bylo možné zálohovat velké databáze, maximálně na 12,8 TB. |
| **Záloha snímku** |Pomocí snímků Azure SQL Server zálohování File-Snapshot nabízí skoro okamžité zálohování a rychlé obnovení souborů databáze uložených pomocí služby Azure Blob Storage. Tato funkce vám umožní zjednodušit zásady zálohování a obnovení. Zálohování souborů a snímků také podporuje obnovení k časovému okamžiku. Další informace najdete v tématu [zálohování snímků pro soubory databáze v Azure](https://docs.microsoft.com/sql/relational-databases/backup-restore/file-snapshot-backups-for-database-files-in-azure). |

Další informace najdete v jednom z následujících článků na základě vaší verze SQL Server:

- **SQL Server 2016/2017**: [SQL Server zálohování na adresu URL](https://docs.microsoft.com/sql/relational-databases/backup-restore/sql-server-backup-and-restore-with-microsoft-azure-blob-storage-service)
- **SQL Server 2014**: [SQL Server 2014 zálohování na adresu URL](https://msdn.microsoft.com/library/jj919148%28v=sql.120%29.aspx)
- **SQL Server 2012**: [SQL Server 2012 zálohování na adresu URL](https://msdn.microsoft.com/library/jj919148%28v=sql.110%29.aspx)

### <a name="managed-backup"></a>Spravované zálohování

Počínaje SQL Server 2014 Spravovaná záloha automatizuje vytváření záloh do služby Azure Storage. Spravované zálohování na pozadí využívá funkci zálohování na adresu URL popsanou v předchozí části tohoto článku. Spravovaná záloha je také základní funkcí, která podporuje službu automatizovaného zálohování SQL Server VM.

Počínaje SQL Server 2016 se ve spravovaném zálohování objevily další možnosti pro plánování, zálohování systémové databáze a četnost úplného zálohování a zálohování protokolů.

Další informace najdete v jednom z následujících článků na základě vaší verze SQL Server:

- [Spravované zálohování na Microsoft Azure pro SQL Server 2016 a novější](https://docs.microsoft.com/sql/relational-databases/backup-restore/sql-server-managed-backup-to-microsoft-azure)
- [Spravované zálohování na Microsoft Azure pro SQL Server 2014](https://msdn.microsoft.com/library/dn449496%28v=sql.120%29.aspx)

## <a name="decision-matrix"></a>Tabulka rozhodnutí

Následující tabulka shrnuje možnosti jednotlivých možností zálohování a obnovení pro SQL Server virtuálních počítačů v Azure.

| Možnost | Automatizované zálohování | Azure Backup pro SQL | Ruční zálohování |
|---|---|---|---|
| Vyžaduje další službu Azure. |   | ![Zelená značka zaškrtnutí.](./media/backup-restore/yes.png) |   |
| Konfigurace zásad zálohování v Azure Portal | ![Zelená značka zaškrtnutí.](./media/backup-restore/yes.png) | ![Zelená značka zaškrtnutí.](./media/backup-restore/yes.png) |   |
| Obnovení databází v Azure Portal |   | ![Zelená značka zaškrtnutí.](./media/backup-restore/yes.png) |   |
| Správa více serverů v jednom řídicím panelu |   | ![Zelená značka zaškrtnutí.](./media/backup-restore/yes.png) |   |
| Obnovení k určitému bodu v čase | ![Zelená značka zaškrtnutí.](./media/backup-restore/yes.png) | ![Zelená značka zaškrtnutí.](./media/backup-restore/yes.png) | ![Zelená značka zaškrtnutí.](./media/backup-restore/yes.png) |
| 15 minut cíl bodu obnovení (RPO) | ![Zelená značka zaškrtnutí.](./media/backup-restore/yes.png) | ![Zelená značka zaškrtnutí.](./media/backup-restore/yes.png) | ![Zelená značka zaškrtnutí.](./media/backup-restore/yes.png) |
| Zásady uchovávání krátkodobých záloh (dny) | ![Zelená značka zaškrtnutí.](./media/backup-restore/yes.png) | ![Zelená značka zaškrtnutí.](./media/backup-restore/yes.png) |   |
| Zásady uchovávání dlouhodobých záloh (měsíce, roky) |   | ![Zelená značka zaškrtnutí.](./media/backup-restore/yes.png) |   |
| Integrovaná podpora pro SQL Server Always On |   | ![Zelená značka zaškrtnutí.](./media/backup-restore/yes.png) |   |
| Zálohování do Azure Storage účtů | ![Zelená značka zaškrtnutí.](./media/backup-restore/yes.png)Automatické | ![Zelená značka zaškrtnutí.](./media/backup-restore/yes.png)Automatické | ![Zelená značka zaškrtnutí.](./media/backup-restore/yes.png)(spravované zákazníkem) |
| Správa úložiště a záložních souborů | | ![Zelená značka zaškrtnutí.](./media/backup-restore/yes.png) |  |
| Zálohování na připojené disky ve virtuálním počítači |   |   | ![Zelená značka zaškrtnutí.](./media/backup-restore/yes.png) |
| Sestavy centrálního zálohování s možností přizpůsobení |   | ![Zelená značka zaškrtnutí.](./media/backup-restore/yes.png) |   |
| Konsolidovaná e-mailová upozornění pro chyby |   | ![Zelená značka zaškrtnutí.](./media/backup-restore/yes.png) |   |
| Přizpůsobení monitorování na základě protokolů Azure Monitor |   | ![Zelená značka zaškrtnutí.](./media/backup-restore/yes.png) |   |
| Monitorování úloh zálohování pomocí skriptů SSMS nebo Transact-SQL | ![Zelená značka zaškrtnutí.](./media/backup-restore/yes.png) | ![Zelená značka zaškrtnutí.](./media/backup-restore/yes.png) | ![Zelená značka zaškrtnutí.](./media/backup-restore/yes.png) |
| Obnovení databází pomocí skriptů SSMS nebo Transact-SQL | ![Zelená značka zaškrtnutí.](./media/backup-restore/yes.png) |   | ![Zelená značka zaškrtnutí.](./media/backup-restore/yes.png) |

## <a name="next-steps"></a>Další kroky

Pokud plánujete nasazení SQL Server na VIRTUÁLNÍm počítači Azure, najdete pokyny k zřizování v následující příručce: [jak zřídit virtuální počítač s Windows SQL Server v Azure Portal](create-sql-vm-portal.md).

I když se zálohování a obnovení dá použít k migraci dat, existují potenciálně jednodušší cesty migrace dat SQL Server na VIRTUÁLNÍm počítači. Úplnou diskusi o možnostech migrace a doporučeních najdete v tématu [migrace databáze do SQL Server na virtuálním počítači Azure](migrate-to-vm-from-sql-server.md).
