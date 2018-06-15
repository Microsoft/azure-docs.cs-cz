---
title: Jak používat Azure úložiště pro zálohování serveru SQL Server a obnovit | Microsoft Docs
description: Informace o zálohování systému SQL Server do služby Azure Storage. Vysvětluje výhody zálohování databází SQL Azure Storage.
services: virtual-machines-windows
documentationcenter: ''
author: MikeRayMSFT
manager: craigg
tags: azure-service-management
ms.assetid: 0db7667d-ef63-4e2b-bd4d-574802090f8b
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 01/31/2017
ms.author: mikeray
ms.openlocfilehash: 39d4f452143454a345bd91f550e44c93651ff933
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/21/2018
ms.locfileid: "29399045"
---
# <a name="use-azure-storage-for-sql-server-backup-and-restore"></a>Použít úložiště Azure pro obnovení a zálohování systému SQL Server
## <a name="overview"></a>Přehled
Od verze SQL Server 2012 SP1 CU2, můžete nyní napsat záloh systému SQL Server přímo ke službě Azure Blob storage. Tato funkce slouží k zálohování a obnovení ze služby objektů Blob v Azure místní databázi systému SQL Server nebo databázi serveru SQL Server ve virtuálním počítači Azure. Zálohování do cloudu nabízí výhody dostupnosti, neomezenou geograficky replikované odlehlého úložiště a snadnou migraci dat do a z cloudu. Můžete použít příkazy zálohování nebo obnovení pomocí jazyka Transact-SQL nebo SMO.

SQL Server 2016 zavádí nové funkce; můžete použít [zálohy snímku souboru](http://msdn.microsoft.com/library/mt169363.aspx) k provádění téměř okamžité zálohy a velmi rychlé obnovení.

Toto téma vysvětluje, proč můžete použít zálohování SQL na úložiště Azure a pak popisuje součásti související se situací. Prostředky zadaná na konci tohoto článku můžete použít pro přístup k návody a další informace o používání této služby s záloh systému SQL Server.

## <a name="benefits-of-using-the-azure-blob-service-for-sql-server-backups"></a>Výhody použití služby objektů Blob v Azure pro zálohování serveru SQL
Existuje několik výzvy, které při zálohování serveru SQL Server. Patří mezi ně správy úložiště, riziko selhání úložiště, přístup k odlehlého úložiště a konfiguraci hardwaru. Řadu tyto problémy řešeny pomocí služby Azure Blob úložiště pro zálohování systému SQL Server. Vezměte v úvahu následující výhody:

* **Snadné použití**: ukládání záloh v Azure BLOB může být pohodlnou, flexibilní a snadný přístup k odlehlého možnosti. Vytváření odlehlého úložiště pro zálohování serveru SQL Server může být stejně snadná jako úprava použít existující skripty nebo úlohách **zálohování na adresu URL** syntaxe. Odlehlého úložiště by měl obvykle být dostatečně z umístění provozní databáze aby jednoho po havárii, které mohou ovlivnit odlehlého a provozní umístění se databáze. Výběrem k [objekty BLOB vaší Azure geograficky replikovat](../../../storage/common/storage-redundancy.md), máte další vrstvu ochrany v případě havárie, který může mít vliv na celou oblast.
* **Zálohování archivu**: služby Azure Blob Storage nabízí lepší alternativu k možnosti pásky často používané k archivaci zálohy. Páskové úložiště můžou vyžadovat fyzické Transport odlehlého zařízení a opatření k ochraně média. Ukládání záloh ve službě Azure Blob Storage poskytuje rychlých, vysoce dostupný, a trvale archivovat možnost.
* **Spravované hardwaru**: neexistuje žádné režijní náklady na správu hardwaru se službami Azure. Azure services spravovat hardwaru a poskytovat geografická replikace redundanci a ochranu proti selhání hardwaru.
* **Neomezené úložiště**: tím, že umožňuje přímé zálohování do Azure BLOB, máte přístup k prakticky neomezené úložiště. Můžete taky zálohování až disk pro virtuální počítač Azure je omezení na základě velikosti počítače. Existuje omezení počtu disky, které můžete provést připojení k virtuální počítač pro zálohování Azure. Tento limit je 16 disků pro instanci velmi velké a méně pro menší instance.
* **Zálohování dostupnosti**: záloh uložených v Azure BLOB jsou k dispozici odkudkoli a kdykoli a můžete snadno přístupné pro obnovení systému SQL Server místní nebo jiný Server SQL spuštěn v virtuální počítač Azure, bez nutnosti databáze připojení a odpojení nebo stahování a připojení virtuálního pevného disku.
* **Náklady na**: platit pouze pro službu, která se používá. Může být cenově jako archiv mimo pracoviště a záložní možnost. Najdete v článku [Azure cenové kalkulačky](http://go.microsoft.com/fwlink/?LinkId=277060 "cenové kalkulačky")a [Azure – ceny článku](http://go.microsoft.com/fwlink/?LinkId=277059 "cenová článku") Další informace.
* **Úložiště snímků**: Pokud soubory databáze jsou uložená v objektu blob Azure a používáte SQL Server 2016, můžete použít [zálohy snímku souboru](http://msdn.microsoft.com/library/mt169363.aspx) k provádění téměř okamžité zálohy a velmi rychlé obnovení.

Další podrobnosti najdete v tématu [zálohování systému SQL Server a obnovení služby úložiště objektů Blob Azure](http://go.microsoft.com/fwlink/?LinkId=271617).

Následující dvě části zavést službu úložiště objektů Blob v Azure, včetně požadované součásti systému SQL Server. Je důležité pochopit součásti a jejich interakci úspěšně použít zálohování a obnovení ze služby Azure Blob storage.

## <a name="azure-blob-storage-service-components"></a>Součásti služby úložiště objektů Blob v Azure
Následující součásti Azure se používají při zálohování do služby Azure Blob storage.

| Komponenta | Popis |
| --- | --- |
| **Účet úložiště** |Účet úložiště je výchozím bodem pro všechny služby úložiště. Pro přístup k službě Azure Blob Storage, nejdřív vytvořte účet úložiště Azure. Další informace o službě Azure Blob storage najdete v tématu [jak používat službu Azure Blob Storage](https://azure.microsoft.com/develop/net/how-to-guides/blob-storage/) |
| kontejner |Kontejner zajišťuje seskupení sady objektů BLOB a můžete uložit libovolný počet objektů BLOB. Pro zápis systému SQL Server zálohování do služby Azure Blob, musíte mít alespoň Kořenový kontejner vytvořit. |
| **Objekt BLOB** |Soubor libovolného typu a velikosti. Objekty BLOB jsou adresovatelné v následujícím formátu adresy URL: **https://[storage account].blob.core.windows.net/[container]/[blob]**. Další informace o objekty BLOB stránky najdete v tématu [Principy bloku a objekty BLOB stránky](http://msdn.microsoft.com/library/azure/ee691964.aspx) |

## <a name="sql-server-components"></a>Součásti SQL serveru
Následující součásti systému SQL Server se používají při zálohování do služby Azure Blob storage.

| Komponenta | Popis |
| --- | --- |
| **Adresa URL** |Určuje adresu URL identifikátoru URI (Uniform Resource) jedinečný soubor zálohy. Adresa URL slouží k zadání umístění a název souboru zálohy systému SQL Server. Adresa URL musí odkazovat na skutečný blob, ne jenom do kontejneru. Pokud objekt blob neexistuje, vytvoří se. Pokud existující objekt blob je zadán, zálohování se nezdaří, pokud > je zadána možnost klauzuli WITH FORMAT. Následuje příklad adresy URL, zadali byste v příkazu BACKUP: **http[s]://[storageaccount].blob.core.windows.net/[container]/[FILENAME.bak]**. HTTPS je doporučená, ale není potřeba. |
| **přihlašovací údaje** |Informace, které je potřeba připojit a ověření pro službu úložiště objektů Blob v Azure uloží jako pověření.  V pořadí pro SQL Server k zápisu zálohování do Azure Blob nebo obnovení z něj musí být vytvořený přihlašovací údaje systému SQL Server. Další informace najdete v tématu [přihlašovací údaje SQL serveru](https://msdn.microsoft.com/library/ms189522.aspx). |

> [!NOTE]
> Pokud se rozhodnete kopírovat a nahrajte záložní soubor do služby Azure Blob storage, musíte použít typu Objekt blob stránky jako svoji možnost úložiště Pokud máte v úmyslu použít tento soubor pro operace obnovení. OBNOVENÍ z typu Objekt blob bloku se nezdaří s chybou.
> 
> 

## <a name="next-steps"></a>Další postup
1. Pokud jste ještě nemáte, vytvořte účet Azure. Pokud hodnotíte Azure, zvažte [bezplatnou zkušební verzi](https://azure.microsoft.com/free/).
2. Potom přejděte prostřednictvím jednoho z následujících návodů, které vás provede procesem vytvoření účtu úložiště a provedení obnovení.
   
   * **SQL Server 2014**: [kurz: SQL Server 2014 zálohování a obnovení do služby Microsoft Azure služba úložiště objektů Blob](https://msdn.microsoft.com/library/jj720558\(v=sql.120\).aspx).
   * **SQL Server 2016**: [kurz: služby Microsoft Azure Blob storage pomocí databáze SQL Server 2016](https://msdn.microsoft.com/library/dn466438.aspx)
3. Přečtěte si další dokumentaci od [zálohování systému SQL Server a obnovení službou Microsoft Azure Blob Storage](https://msdn.microsoft.com/library/jj919148.aspx).

Pokud máte potíže, přečtěte si téma [zálohování systému SQL Server na adresu URL osvědčené postupy a řešení potíží](https://msdn.microsoft.com/library/jj919149.aspx).

Pro jiný Server SQL zálohování a možnosti obnovení, najdete v části [zálohování a obnovení pro SQL Server v Azure Virtual Machines](virtual-machines-windows-sql-backup-recovery.md).

