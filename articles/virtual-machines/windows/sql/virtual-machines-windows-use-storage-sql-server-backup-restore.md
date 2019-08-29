---
title: Jak používat službu Azure Storage pro SQL Server zálohování a obnovení | Microsoft Docs
description: Naučte se, jak zálohovat SQL Server Azure Storage. Vysvětluje výhody zálohování databází SQL pro Azure Storage.
services: virtual-machines-windows
documentationcenter: ''
author: MikeRayMSFT
manager: craigg
tags: azure-service-management
ms.assetid: 0db7667d-ef63-4e2b-bd4d-574802090f8b
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 01/31/2017
ms.author: mikeray
ms.openlocfilehash: cb19dc7262721e672bd3f54b32db9188dad7fee0
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/28/2019
ms.locfileid: "70101897"
---
# <a name="use-azure-storage-for-sql-server-backup-and-restore"></a>Pro SQL Server zálohování a obnovení použít Azure Storage
## <a name="overview"></a>Přehled
Počínaje SQL Server 2012 SP1 CU2 můžete teď SQL Server zálohy zapisovat přímo do služby Azure Blob Storage. Tuto funkci můžete použít k zálohování a obnovení z Blob service Azure pomocí místní databáze SQL Server nebo databáze SQL Server na virtuálním počítači Azure. Zálohování do cloudu nabízí výhody dostupnosti, neomezené geograficky replikované úložiště mimo pracoviště a snadné migrace dat do a z cloudu. Příkazy BACKUP nebo Restore můžete vystavit pomocí jazyka Transact-SQL nebo SMO.

SQL Server 2016 přináší nové možnosti; pomocí [zálohování snímků souborů](https://msdn.microsoft.com/library/mt169363.aspx) můžete provádět skoro okamžité zálohování a neuvěřitelně rychlé obnovení.

V tomto tématu se dozvíte, proč se můžete rozhodnout použít službu Azure Storage pro zálohování SQL a pak tyto součásti popisuje. Pomocí prostředků uvedených na konci článku můžete získat přístup k názorům a dalším informacím, abyste mohli tuto službu začít používat s vaším zálohováním SQL Server.

## <a name="benefits-of-using-the-azure-blob-service-for-sql-server-backups"></a>Výhody používání služby Azure Blob Service pro zálohování SQL Server
Při zálohování SQL Server se můžete setkat s několika problémy. Tyto výzvy zahrnují správu úložiště, riziko selhání úložiště, přístup k úložišti mimo lokalitu a konfiguraci hardwaru. Mnohé z těchto výzev jsou řešeny pomocí služby Azure Blob Store pro SQL Server zálohy. Vezměte v úvahu následující výhody:

* **Snadné použití**: Ukládání záloh do objektů BLOB v Azure může být pohodlný, flexibilní a snadný přístup k možnosti mimo lokalitu. Vytváření úložiště mimo lokalitu pro zálohování SQL Server může být stejně snadné jako úprava stávajících skriptů nebo úloh, aby bylo možné použít syntaxi **zálohování na adresu URL** . Úložiště mimo lokalitu by mělo být obvykle dostatečně daleko od umístění produkční databáze, aby se zabránilo jedné havárii, která by mohla mít vliv na umístění v lokalitě i v provozní databázi. Když zvolíte [geografickou replikaci vašich objektů blob Azure](../../../storage/common/storage-redundancy.md), budete mít dodatečnou vrstvu ochrany v případě havárie, která by mohla ovlivnit celou oblast.
* **Archiv zálohování**: Služba Azure Blob Storage nabízí lepší alternativu k často používané možnosti pásky k archivaci záloh. Páskové úložiště může vyžadovat fyzickou přepravu do zařízení mimo pracoviště a opatření k ochraně média. Uložení záloh v Azure Blob Storage poskytuje rychlou, vysoce dostupnou a trvalou možnost archivace.
* **Spravovaný hardware**: Není k dispozici žádná režie správy hardwaru se službami Azure. Služby Azure spravují hardware a poskytují geografickou replikaci pro zajištění redundance a ochrany před selháním hardwaru.
* **Neomezené úložiště**: Povolením přímé zálohy do objektů blob Azure získáte přístup k prakticky neomezenému úložišti. Další možností je, že zálohování na disk virtuálního počítače Azure má omezení na základě velikosti počítače. Počet disků, které můžete k virtuálnímu počítači Azure připojit k zálohování, je omezený. Toto omezení je 16 disků pro větší velkou instanci a méně pro menší instance.
* **Dostupnost zálohování**: Zálohy uložené v Azure Blobs jsou dostupné odkudkoli a kdykoli a dají se snadno získat z provozu do místní SQL Server nebo jiného SQL Server spuštěného na virtuálním počítači Azure, aniž by bylo nutné připojení nebo odpojení databáze. a připojení virtuálního pevného disku.
* **Náklady:** Platíte jenom za službu, která se používá. Může být nákladově efektivní jako možnost mimo pracoviště a záložní archivace. Další informace najdete v cenové Kalkulačkě cenové kalkulačky [Azure](https://go.microsoft.com/fwlink/?LinkId=277060 "")a v(https://go.microsoft.com/fwlink/?LinkId=277059 "článku o cenách") za [Azure Price].
* **Snímky úložiště**: Když se soubory databáze ukládají v objektu blob Azure a používáte SQL Server 2016, můžete k provádění skoro okamžitého zálohování a neuvěřitelně rychlé obnovy použít [zálohování snímků souborů](https://msdn.microsoft.com/library/mt169363.aspx) .

Další podrobnosti najdete v tématu [SQL Server zálohování a obnovování pomocí služby Azure Blob Storage Service](https://go.microsoft.com/fwlink/?LinkId=271617).

Následující dvě části představují službu úložiště objektů BLOB v Azure, včetně požadovaných součástí SQL Server. Je důležité pochopit komponenty a jejich interakci, aby bylo možné úspěšně použít zálohování a obnovení ze služby Azure Blob Storage.

## <a name="azure-blob-storage-service-components"></a>Součásti služby Azure Blob Storage
Při zálohování do služby Azure Blob Storage se použijí následující komponenty Azure.

| Komponenta | Popis |
| --- | --- |
| **Účet úložiště** |Účet úložiště je výchozím bodem pro všechny služby úložiště. Pokud chcete získat přístup ke službě Azure Blob Storage, vytvořte nejdřív Azure Storage účet. Další informace o službě Azure Blob Storage najdete v tématu [How to use the azure BLOB Storage Service](https://azure.microsoft.com/develop/net/how-to-guides/blob-storage/) . |
| **Kontejner** |Kontejner poskytuje seskupení sady objektů BLOB a může ukládat neomezený počet objektů BLOB. Pokud chcete zapsat SQL Server zálohu do Blob service Azure, musíte mít vytvořený aspoň kořenový kontejner. |
| **Objekt blob** |Soubor libovolného typu a velikosti. Objekty blob jsou adresovatelné v následujícím formátu adresy URL: **https://[účet úložiště]. blob. Core. Windows. NET/[Container]/[BLOB]** . Další informace o objektech blob stránky najdete v tématu [Principy objektů blob bloku a stránky](https://msdn.microsoft.com/library/azure/ee691964.aspx) . |

## <a name="sql-server-components"></a>SQL Server komponenty
Při zálohování do služby Azure Blob Storage se použijí následující SQL Server komponenty.

| Komponenta | Popis |
| --- | --- |
| **Adresa URL** |Adresa URL určuje identifikátor URI (Uniform Resource Identifier) pro jedinečný záložní soubor. Adresa URL slouží k poskytnutí umístění a názvu souboru zálohy SQL Server. Adresa URL musí ukazovat na skutečný objekt blob, nikoli jenom na kontejner. Pokud objekt BLOB neexistuje, vytvoří se. Pokud je zadaný existující objekt blob, zálohování se nepovede, pokud se nezadá možnost > WITH FORMAT. Následuje příklad adresy URL, kterou byste zadali v příkazu BACKUP: **http [s]://[storageaccount]. blob. Core. Windows. NET/[kontejner]/[filename. bak]** . Protokol HTTPS se doporučuje, ale není povinný. |
| **Přihlašovací údaje** |Informace, které se vyžadují pro připojení a ověření ve službě Azure Blob Storage, jsou uložené jako přihlašovací údaje.  Aby bylo možné SQL Server zapisovat zálohy do objektu blob Azure nebo je z něj obnovit, je nutné vytvořit SQL Server přihlašovací údaje. Další informace najdete v tématu [SQL Server přihlašovacích údajů](https://msdn.microsoft.com/library/ms189522.aspx). |

> [!NOTE]
> Systém SQL Server 2016 byl aktualizován tak, aby podporoval objekty blob bloku. Přečtěte [si prosím kurz: Další podrobnosti najdete ve službě Microsoft Azure Blob Storage s databázemi](https://msdn.microsoft.com/library/dn466438.aspx) SQL Server 2016.
> 
> 

## <a name="next-steps"></a>Další kroky
1. Vytvořte účet Azure, pokud ho ještě nemáte. Pokud vyhodnocujete Azure, zvažte [bezplatnou zkušební verzi](https://azure.microsoft.com/free/).
2. Pak Projděte jeden z následujících kurzů, které vás provedou vytvořením účtu úložiště a obnovením.
   
   * **SQL Server 2014**: [Návodu SQL Server 2014 zálohování a obnovení do služby](https://msdn.microsoft.com/library/jj720558\(v=sql.120\).aspx)Microsoft Azure Blob Storage.
   * **SQL Server 2016**: [Návodu Používání služby Microsoft Azure Blob Storage s databázemi SQL Server 2016](https://msdn.microsoft.com/library/dn466438.aspx)
3. Přečtěte si další dokumentaci od [SQL Server zálohování a obnovení pomocí služby Microsoft Azure Blob Storage](https://msdn.microsoft.com/library/jj919148.aspx).

Pokud máte nějaké problémy, přečtěte si téma [SQL Server zálohování na osvědčené postupy a řešení potíží na adrese URL](https://msdn.microsoft.com/library/jj919149.aspx).

Další SQL Server možnosti zálohování a obnovení najdete v tématu [zálohování a obnovení pro SQL Server ve službě Azure Virtual Machines](virtual-machines-windows-sql-backup-recovery.md).

