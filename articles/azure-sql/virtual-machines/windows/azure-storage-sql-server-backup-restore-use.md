---
title: Jak používat Azure Storage pro SQL Server zálohování a obnovení | Microsoft Docs
description: Naučte se, jak zálohovat SQL Server Azure Storage. Vysvětluje výhody zálohování databází SQL pro Azure Storage.
services: virtual-machines-windows
documentationcenter: ''
author: MikeRayMSFT
tags: azure-service-management
ms.assetid: 0db7667d-ef63-4e2b-bd4d-574802090f8b
ms.service: virtual-machines-sql
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 01/31/2017
ms.author: mathoma
ms.openlocfilehash: c59fc03e36b738f7eaa91fcd13dda1cf0f6497a8
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91272799"
---
# <a name="use-azure-storage-for-sql-server-backup-and-restore"></a>Pro SQL Server zálohování a obnovení použít Azure Storage
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Počínaje SQL Server 2012 SP1 CU2 můžete teď zapisovat SQL Server zálohy přímo do Azure Blob Storage. Tuto funkci můžete použít k zálohování a obnovení ze služby Azure Blob Storage a databáze SQL Server. Zálohování do cloudu nabízí výhody dostupnosti, neomezené geograficky replikované úložiště mimo pracoviště a snadné migrace dat do cloudu a z cloudu. Příkazy BACKUP nebo Restore můžete vystavit pomocí jazyka Transact-SQL nebo SMO.

## <a name="overview"></a>Přehled
SQL Server 2016 přináší nové možnosti; pomocí [zálohování snímků souborů](https://msdn.microsoft.com/library/mt169363.aspx) můžete provádět skoro okamžité zálohování a neuvěřitelně rychlé obnovení.

V tomto tématu se dozvíte, proč se můžete rozhodnout pro SQL Server zálohování použít Azure Storage a pak popisuje tyto komponenty. Pomocí prostředků uvedených na konci článku můžete získat přístup k podrobným postupům a dalším informacím, abyste mohli tuto službu začít používat s SQL Server zálohování.

## <a name="benefits-of-using-azure-blob-storage-for-sql-server-backups"></a>Výhody používání služby Azure Blob Storage pro zálohování SQL Server
Při zálohování SQL Server se můžete setkat s několika problémy. Tyto výzvy zahrnují správu úložiště, riziko selhání úložiště, přístup k úložišti mimo lokalitu a konfiguraci hardwaru. Mnohé z těchto výzev jsou řešeny pomocí služby Azure Blob Storage pro SQL Server zálohování. Vezměte v úvahu následující výhody:

* **Snadné použití**: ukládání záloh do objektů blob Azure může být pohodlný, flexibilní a snadný přístup k možnosti mimo lokalitu. Vytváření úložiště mimo lokalitu pro zálohování SQL Server může být stejně snadné jako úprava stávajících skriptů nebo úloh, aby bylo možné použít syntaxi **zálohování na adresu URL** . Úložiště mimo lokalitu by mělo být obvykle dostatečně daleko od umístění produkční databáze, aby se zabránilo jedné havárii, která by mohla mít vliv na umístění v lokalitě i v provozní databázi. Když zvolíte [geografickou replikaci vašich objektů blob Azure](../../../storage/common/storage-redundancy.md), budete mít dodatečnou vrstvu ochrany v případě havárie, která by mohla ovlivnit celou oblast.
* **Záložní archivace**: úložiště objektů BLOB v Azure nabízí lepší alternativu k často používané možnosti pásky k archivaci záloh. Páskové úložiště může vyžadovat fyzickou přepravu do zařízení mimo pracoviště a opatření k ochraně média. Uložení záloh v Azure Blob Storage poskytuje rychlou, vysoce dostupnou a trvalou možnost archivace.
* **Spravovaný hardware**: nehrozí žádná režie správy hardwaru se službami Azure. Služby Azure spravují hardware a poskytují geografickou replikaci pro zajištění redundance a ochrany před selháním hardwaru.
* **Neomezené úložiště**: povolením přímé zálohy do objektů blob Azure získáte přístup k prakticky neomezenému úložišti. Další možností je, že zálohování na disk virtuálního počítače Azure má omezení na základě velikosti počítače. Počet disků, které můžete k virtuálnímu počítači Azure připojit k zálohování, je omezený. Toto omezení je 16 disků pro větší velkou instanci a méně pro menší instance.
* **Dostupnost zálohování**: zálohy uložené v objektech blob Azure jsou dostupné odkudkoli a kdykoli a dají se snadno získat z důvodu obnovení do SQL Server instance bez nutnosti připojení nebo odpojení databáze nebo stahování a připojení VHD.
* **Náklady**: platíte jenom za službu, která se používá. Může být nákladově efektivní jako možnost mimo pracoviště a záložní archivace. Další informace najdete v [cenové kalkulačkě Azure](https://go.microsoft.com/fwlink/?LinkId=277060 "Cenová kalkulačka")a v [článku ceny Azure](https://go.microsoft.com/fwlink/?LinkId=277059 "Článek o cenách") .
* **Snímky úložiště**: když se soubory databáze ukládají v objektu blob Azure a používáte SQL Server 2016, můžete použít [zálohování snímků souborů](https://msdn.microsoft.com/library/mt169363.aspx) k provádění skoro okamžitých záloh a neuvěřitelně rychlé obnovy.

Další podrobnosti najdete v tématu [SQL Server zálohování a obnovování pomocí služby Azure Blob Storage](https://go.microsoft.com/fwlink/?LinkId=271617).

Následující dvě části představují službu Azure Blob Storage, včetně požadovaných součástí SQL Server. Je důležité pochopit komponenty a jejich interakci k úspěšnému použití zálohování a obnovení z úložiště objektů BLOB v Azure.

## <a name="azure-blob-storage-components"></a>Komponenty služby Azure Blob Storage
Při zálohování do úložiště objektů BLOB v Azure se použijí následující komponenty Azure.

| Komponenta | Popis |
| --- | --- |
| **Účet úložiště** |Účet úložiště je výchozím bodem pro všechny služby úložiště. Pokud chcete získat přístup k úložišti objektů BLOB v Azure, vytvořte nejdřív účet Azure Storage. Další informace o službě Azure Blob Storage najdete v tématu [Jak používat úložiště objektů BLOB v Azure](https://azure.microsoft.com/develop/net/how-to-guides/blob-storage/). |
| **Kontejner** |Kontejner poskytuje seskupení sady objektů BLOB a může ukládat neomezený počet objektů BLOB. Pokud chcete zapsat SQL Server zálohu do úložiště objektů BLOB v Azure, musíte mít vytvořený aspoň kořenový kontejner. |
| **Objekt blob** |Soubor libovolného typu a velikosti. Objekty blob jsou adresovatelné v následujícím formátu adresy URL: **https://[účet úložiště]. blob. Core. Windows. NET/[Container]/[BLOB]**. Další informace o objektech blob stránky najdete v tématu [Principy objektů blob bloku a stránky](https://msdn.microsoft.com/library/azure/ee691964.aspx) . |

## <a name="sql-server-components"></a>SQL Server komponenty
Při zálohování do úložiště objektů BLOB v Azure se použijí následující SQL Server komponenty.

| Komponenta | Popis |
| --- | --- |
| **Adresa URL** |Adresa URL určuje identifikátor URI (Uniform Resource Identifier) pro jedinečný záložní soubor. Adresa URL slouží k poskytnutí umístění a názvu souboru zálohy SQL Server. Adresa URL musí ukazovat na skutečný objekt blob, nikoli jenom na kontejner. Pokud objekt BLOB neexistuje, vytvoří se. Pokud je zadaný existující objekt blob, zálohování se nepovede, pokud se nezadá možnost > WITH FORMAT. Následuje příklad adresy URL, kterou byste zadali v příkazu BACKUP: **http [s]://[storageaccount]. blob. Core. Windows. NET/[kontejner]/[filename. bak]**. Protokol HTTPS se doporučuje, ale není povinný. |
| **Přihlašovací údaj** |Informace, které se vyžadují pro připojení a ověření ve službě Azure Blob Storage, se ukládají jako přihlašovací údaje. Aby bylo možné SQL Server zapisovat zálohy do objektu blob Azure nebo je z něj obnovit, je nutné vytvořit SQL Server přihlašovací údaje. Další informace najdete v tématu [SQL Server přihlašovacích údajů](https://msdn.microsoft.com/library/ms189522.aspx). |

> [!NOTE]
> SQL Server 2016 byl aktualizován tak, aby podporoval objekty blob bloku. Další podrobnosti najdete v [kurzu: používání úložiště objektů Blob Microsoft Azure s databázemi SQL Server 2016](https://msdn.microsoft.com/library/dn466438.aspx) .
> 
> 

## <a name="next-steps"></a>Další kroky
1. Vytvořte účet Azure, pokud ho ještě nemáte. Pokud vyhodnocujete Azure, zvažte [bezplatnou zkušební verzi](https://azure.microsoft.com/free/).
2. Pak Projděte jeden z následujících kurzů, které vás provedou vytvořením účtu úložiště a obnovením.
   
   * **SQL Server 2014**: [kurz: SQL Server 2014 zálohování a obnovení do úložiště objektů BLOB Microsoft Azure](https://msdn.microsoft.com/library/jj720558\(v=sql.120\).aspx).
   * **SQL Server 2016**: [kurz: používání úložiště objektů Blob Microsoft Azure s SQL Servermi databázemi 2016](https://msdn.microsoft.com/library/dn466438.aspx)
3. Přečtěte si další dokumentaci od [SQL Server zálohování a obnovení s Microsoft azurem úložištěm objektů BLOB](https://msdn.microsoft.com/library/jj919148.aspx).

Pokud máte nějaké problémy, přečtěte si téma [SQL Server zálohování na osvědčené postupy a řešení potíží na adrese URL](https://msdn.microsoft.com/library/jj919149.aspx).

Další SQL Server možnosti zálohování a obnovení najdete v tématu [zálohování a obnovení pro SQL Server v Azure Virtual Machines](backup-restore.md).

