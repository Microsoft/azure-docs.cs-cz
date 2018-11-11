---
title: Postup použití služby Azure storage pro zálohování serveru SQL Server a obnovení | Dokumentace Microsoftu
description: Zjistěte, jak k zálohování SQL serveru do služby Azure Storage. Vysvětluje výhody zálohování databází SQL do služby Azure Storage.
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
ms.openlocfilehash: 0fdf768008161fbb72e48dae937a4172222dbb63
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2018
ms.locfileid: "51239742"
---
# <a name="use-azure-storage-for-sql-server-backup-and-restore"></a>Použití služby Azure Storage pro zálohování SQL serveru a obnovení
## <a name="overview"></a>Přehled
Od verze SQL Server 2012 SP1 kumulativní aktualizaci 2, teď můžete psát záloh systému SQL Server přímo do služby Azure Blob storage. Tato funkce slouží k zálohování a obnovení služby objektů Blob v Azure se k místní databázi SQL serveru nebo databáze systému SQL Server ve virtuálním počítači Azure. Zálohování do cloudu nabízí výhody dostupnosti a neomezené vzdálené geograficky replikované úložiště a snadno se migrace dat do a z cloudu. S použitím příkazů jazyka Transact-SQL nebo SMO můžete zadávat příkazy BACKUP nebo RESTORE.

SQL Server 2016 zavádí nové možnosti; můžete použít [zálohy snímků souborů](https://msdn.microsoft.com/library/mt169363.aspx) provádět téměř okamžité zálohování a mimořádně rychlé obnovení.

Toto téma vysvětluje, proč zvolit použití služby Azure storage pro zálohování SQL a pak popisuje používané komponenty. Prostředky k dispozici na konci tohoto článku můžete použít pro přístup k návody a další informace o používání této služby s zálohování SQL serveru.

## <a name="benefits-of-using-the-azure-blob-service-for-sql-server-backups"></a>Výhody používání služby Azure Blob Service pro zálohování SQL serveru
Existuje několik problémy, se kterými se setkáváte při zálohování serveru SQL Server. Patří mezi ně správy úložiště, riziko selhání úložiště, přístup k Vzdálené úložiště a na konfiguraci hardwaru. Mnohé z těchto problémů se tak vyřeší, použijte službu úložiště objektů Blob v Azure pro zálohování systému SQL Server. Vezměte v úvahu následující výhody:

* **Díky jednoduchosti použití**: ukládání záloh v objektech BLOB Azure může být pohodlnou, flexibilní a snadný přístup k vzdálené možnosti. Vytvoření vzdálené úložiště pro zálohování SQL serveru může být stejně snadné jako změna použít existující skripty a úlohy **zálohování na adresu URL** syntaxe. Vzdálené úložiště by měly být většinou dostatečně daleko od umístění databáze produkční zabránit jednoho po havárii, které mohou ovlivnit mimo pracoviště a produkční umístění se databáze. Kliknutím na [objekty BLOB Azure geografickou replikaci](../../../storage/common/storage-redundancy.md), budete mít další vrstvu ochrany v případě havárie, které by mohly ovlivnit celou oblast.
* **Zálohování archivu**: služba The Azure Blob Storage nabízí lepší alternativou k možnosti pásky často používaný k archivaci zálohy. Páskové úložiště může vyžadovat fyzické transportation vzdálené zařízení a opatření k ochraně média. Ukládání záloh ve službě Azure Blob Storage nabízí pro rychlé, vysoce dostupné, a možnost archivace trvale.
* **Spravovat hardware**: neexistuje žádné nároky na správu hardwaru se službami Azure. Služby Azure spravovat hardware a geografická replikace zajistit redundanci a ochranu proti selhání hardwaru.
* **Neomezené úložiště**: tím, že umožňuje přímé zálohování na objektech BLOB Azure, máte přístup k prakticky neomezené úložiště. Alternativně zálohování disk pro virtuální počítač Azure má omezení na základě velikosti počítačů. Platí omezení na počet disků, které lze připojit k virtuálnímu počítači Azure pro zálohy. Toto omezení je 16 disků pro velmi velká instance a méně pro menší instance.
* **Zálohování dostupnosti**: zálohy uložené v objektech BLOB Azure jsou k dispozici odkudkoli a kdykoli a snadno přístupný pro obnovení do SQL Server v místním nebo jiný SQL Server běžící ve virtuálním počítači Azure, bez nutnosti databáze připojení a odpojení nebo stahování a připojení virtuálního pevného disku.
* **Náklady na**: platíte jenom za služby, který se používá. Můžou být nákladově efektivní jako archiv mimo pracoviště a záložní možnost. Najdete v článku [cenovou kalkulačku Azure](https://go.microsoft.com/fwlink/?LinkId=277060 "cenovou kalkulačku funkcí")a [ceny za Azure článku](https://go.microsoft.com/fwlink/?LinkId=277059 "ceny článku") Další informace.
* **Snímky úložiště**: když používáte SQL Server 2016 databázové soubory jsou uloženy v objektu blob Azure, můžete použít [zálohy snímků souborů](https://msdn.microsoft.com/library/mt169363.aspx) provádět téměř okamžité zálohování a mimořádně rychlé obnovení.

Další podrobnosti najdete v tématu [SQL zálohování a obnovení serveru pomocí služby Azure Blob Storage](https://go.microsoft.com/fwlink/?LinkId=271617).

Následující dvě části představují služby Azure Blob storage, včetně požadované součásti SQL serveru. Je důležité pochopit, komponenty a jejich interakce úspěšně použít zálohování a obnovení ze služby Azure Blob storage.

## <a name="azure-blob-storage-service-components"></a>Součásti služby Azure Blob Storage
Následující komponenty Azure se používají při zálohování do služby Azure Blob storage.

| Komponenta | Popis |
| --- | --- |
| **Účet úložiště** |Účet úložiště je výchozím bodem pro všechny služby storage. Pro přístup k službě Azure Blob Storage, nejdřív vytvořte účet služby Azure Storage. Další informace o službě Azure Blob storage najdete v tématu [jak pomocí služby Azure Blob Storage](https://azure.microsoft.com/develop/net/how-to-guides/blob-storage/) |
| **Kontejner** |Kontejner zajišťuje seskupení sady objektů BLOB a můžete pojmout neomezený počet objektů BLOB. Pro zápis systému SQL Server zálohování do služby Azure Blob service, musíte mít alespoň Kořenový kontejner vytvoří. |
| **Objekt blob** |Soubor libovolného typu a velikosti. Objekty BLOB jsou adresovatelné v následujícím formátu adresy URL: **https://[storage account].blob.core.windows.net/[container]/[blob]**. Další informace o objektech BLOB stránky najdete v tématu [Principy bloku a objekty BLOB stránky](https://msdn.microsoft.com/library/azure/ee691964.aspx) |

## <a name="sql-server-components"></a>Součásti SQL serveru
Následující součásti systému SQL Server se používají při zálohování do služby Azure Blob storage.

| Komponenta | Popis |
| --- | --- |
| **Adresa URL** |Adresa URL určuje identifikátor URI (Uniform Resource) do záložního souboru jedinečný. Adresa URL slouží k zadání umístění a název souboru zálohy systému SQL Server. Adresa URL musí odkazovat na skutečné blob, ne jenom kontejner. Pokud objekt blob neexistuje, vytvoří se. Pokud existující objekt blob je zadán, zálohování selže, pokud > je zadána možnost klauzuli WITH FORMAT. Následuje příklad adresy URL, zadali byste v příkazu BACKUP: **http[s]://[storageaccount].blob.core.windows.net/[container]/[FILENAME.bak]**. Protokol HTTPS se doporučuje, ale nevyžaduje. |
| **Přihlašovací údaje** |Informace, které je potřeba připojit a provést ověření do služby Azure Blob storage se ukládá jako přihlašovací údaje.  V pořadí pro SQL Server zapisovat zálohy do objektů Blob v Azure nebo obnovení z něj musí se vytvořit přihlašovací údaje SQL serveru. Další informace najdete v tématu [přihlašovací údaje SQL serveru](https://msdn.microsoft.com/library/ms189522.aspx). |

> [!NOTE]
> Pokud budete chtít zkopírovat a uložit záložní soubor do služby Azure Blob storage, musíte použít typu Objekt blob stránky jako vaše možnost úložiště plánujete použít tento soubor pro operace obnovení. OBNOVENÍ z typu Objekt blob bloku se nezdaří s chybou.
> 
> 

## <a name="next-steps"></a>Další postup
1. Pokud ho ještě nemáte, vytvořte účet Azure. Pokud hodnotíte Azure, vezměte v úvahu [bezplatnou zkušební verzi](https://azure.microsoft.com/free/).
2. Potom přejděte prostřednictvím jednoho z následujících kurzů, které vás provede procesem vytvoření účtu úložiště a provedení obnovení.
   
   * **SQL Server 2014**: [kurz: SQL Server 2014 zálohování a obnovení do služby Microsoft Azure Blob Storage Service](https://msdn.microsoft.com/library/jj720558\(v=sql.120\).aspx).
   * **SQL Server 2016**: [kurz: použití služby Microsoft Azure Blob storage s databází SQL serveru 2016](https://msdn.microsoft.com/library/dn466438.aspx)
3. Přečtěte si další dokumentaci od [SQL zálohování a obnovení serveru pomocí služby Microsoft Azure Blob Storage](https://msdn.microsoft.com/library/jj919148.aspx).

Pokud máte jakékoli problémy, přečtěte si téma [zálohování SQL serveru na adresu URL osvědčené postupy a řešení potíží](https://msdn.microsoft.com/library/jj919149.aspx).

Jiný SQL Server zálohování a možnosti obnovení, najdete v článku [zálohování a obnovení pro SQL Server ve službě Azure Virtual Machines](virtual-machines-windows-sql-backup-recovery.md).

