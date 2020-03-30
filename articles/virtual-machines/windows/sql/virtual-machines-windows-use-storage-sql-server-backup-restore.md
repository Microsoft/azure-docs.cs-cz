---
title: Jak používat úložiště Azure pro zálohování a obnovení serveru SQL Server | Dokumenty společnosti Microsoft
description: Přečtěte si, jak zálohovat SQL Server do Azure Storage. Vysvětluje výhody zálohování databází SQL do Azure Storage.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "70101897"
---
# <a name="use-azure-storage-for-sql-server-backup-and-restore"></a>Použití služby Azure Storage pro zálohování a obnovování SQL Serveru
## <a name="overview"></a>Přehled
Počínaje SQL Server 2012 SP1 CU2, teď můžete zapisovat zálohy SQL Serveru přímo do služby úložiště objektů Blob Azure. Pomocí této funkce můžete zálohovat a obnovovat ze služby Azure Blob s místní databází SQL Serveru nebo databází SQL Serveru ve virtuálním počítači Azure. Zálohování do cloudu nabízí výhody dostupnosti, neomezené geograficky replikované úložiště mimo pracoviště a snadnou migraci dat do cloudu a z něj. Příkazy BACKUP nebo RESTORE můžete vydat pomocí příkazu Transact-SQL nebo SMO.

SQL Server 2016 zavádí nové funkce; Můžete použít [zálohování snímků souborů](https://msdn.microsoft.com/library/mt169363.aspx) k provádění téměř okamžitých záloh a neuvěřitelně rychlých obnovení.

Toto téma vysvětluje, proč můžete použít úložiště Azure pro zálohování SQL a pak popisuje součásti, které se týkají. Prostředky uvedené na konci článku můžete použít pro přístup k návody a další informace začít používat tuto službu s sql server zálohy.

## <a name="benefits-of-using-the-azure-blob-service-for-sql-server-backups"></a>Výhody použití služby Azure Blob Service pro zálohování serveru SQL Server
Existuje několik problémů, kterým čelíte při zálohování serveru SQL Server. Mezi tyto problémy patří správa úložiště, riziko selhání úložiště, přístup k úložišti mimo pracoviště a konfigurace hardwaru. Mnohé z těchto problémů jsou vyřešeny pomocí služby Azure Blob Store pro zálohy SQL Serveru. Zvažte následující výhody:

* **Snadné použití:** Ukládání záloh do objektů BLOB Azure může být pohodlná, flexibilní a snadno přístupná mimo pracoviště. Vytvoření úložiště mimo pracoviště pro zálohy serveru SQL Server může být stejně snadné jako úprava existujících skriptů nebo úloh tak, aby používaly syntaxi **zálohování adresy URL.** Úložiště mimo pracoviště by obvykle mělo být dostatečně daleko od umístění produkční databáze, aby se zabránilo jediné havárii, která by mohla mít dopad na umístění mimo pracoviště i produkční databázi. Pokud se rozhodnete [geograficky replikovat objekty BLOB Azure](../../../storage/common/storage-redundancy.md), máte další vrstvu ochrany v případě havárie, která by mohla ovlivnit celou oblast.
* **Archiv záloh**: Služba Azure Blob Storage nabízí lepší alternativu k často používané možnosti pásky k archivaci záloh. Páskové úložiště může vyžadovat fyzickou přepravu do zařízení mimo pracoviště a opatření na ochranu médií. Ukládání záloh ve službě Azure Blob Storage poskytuje okamžitou, vysoce dostupnou a trvalou možnost archivace.
* **Spravovaný hardware**: Neexistuje žádná režie správy hardwaru se službami Azure. Služby Azure spravují hardware a poskytují geografickou replikaci pro redundanci a ochranu proti selhání hardwaru.
* **Neomezené úložiště:** Povolením přímého zálohování na objekty BLOB Azure máte přístup k prakticky neomezenému úložišti. Alternativně zálohování na disk virtuálního počítače Azure má omezení na základě velikosti počítače. Počet disků, které můžete připojit k virtuálnímu počítači Azure pro zálohování, je omezen. Toto omezení je 16 disků pro extra velké instance a méně pro menší instance.
* **Dostupnost záloh**: Zálohy uložené v objektech BLOB Azure jsou k dispozici odkudkoli a kdykoli a lze k nim snadno přistupovat pro obnovení na místní SQL Server nebo jiný SQL Server spuštěný ve virtuálním počítači Azure, bez nutnosti připojení/odpojení databáze nebo stahování a připojování virtuálního pevného disku.
* **Náklady**: Plaťte pouze za službu, která je použita. Může být nákladově efektivní jako možnost archivu mimo pracoviště a zálohování. Další informace najdete v [článku O cenových kalkulačkech Azure](https://go.microsoft.com/fwlink/?LinkId=277060 "Cenová kalkulačka")a v článku Ceny [Azure.](https://go.microsoft.com/fwlink/?LinkId=277059 "Cenový článek")
* **Snímky úložiště**: Když jsou databázové soubory uložené v objektu blob Azure a používáte SQL Server 2016, můžete použít [zálohování snímků souborů](https://msdn.microsoft.com/library/mt169363.aspx) k provádění téměř okamžitých záloh a neuvěřitelně rychlých obnovení.

Další podrobnosti najdete v tématu [ZÁLOHOVÁNÍ a obnovení serveru SQL Server pomocí služby Azure Blob Storage Service](https://go.microsoft.com/fwlink/?LinkId=271617).

V následujících dvou částech se zavádí služba úložiště objektů blob Azure, včetně požadovaných součástí SERVERU SQL Server. Je důležité porozumět součástem a jejich interakci, abyste úspěšně využili zálohování a obnovení ze služby úložiště objektů blob Azure.

## <a name="azure-blob-storage-service-components"></a>Součásti služby Úložiště objektů blob Azure
Následující součásti Azure se používají při zálohování služby úložiště objektů blob Azure.

| Komponenta | Popis |
| --- | --- |
| **Účet úložiště** |Účet úložiště je výchozím bodem pro všechny služby úložiště. Chcete-li získat přístup ke službě Azure Blob Storage, nejprve vytvořte účet úložiště Azure. Další informace o službě úložiště objektů blob Azure najdete v [tématu Jak používat službu Azure Blob Storage Service](https://azure.microsoft.com/develop/net/how-to-guides/blob-storage/) |
| **Kontejner** |Kontejner poskytuje seskupení sady objektů BLOB a můžete uložit neomezený počet objektů BLOB. Chcete-li zapsat zálohu serveru SQL Server do služby Azure Blob, musíte mít vytvořen alespoň kořenový kontejner. |
| **Blob** |Soubor libovolného typu a velikosti. Objekty BLOB lze adresovat pomocí následujícího formátu adresy URL: **https://[účet úložiště].blob.core.windows.net/[container]/[blob]**. Další informace o objektech Blob stránky naleznete [v tématu Principy objektů BLOB a Objektů blob stránky](https://msdn.microsoft.com/library/azure/ee691964.aspx) |

## <a name="sql-server-components"></a>Součásti serveru SQL Server
Následující součásti SQL Serveru se používají při zálohování služby úložiště objektů blob Azure.

| Komponenta | Popis |
| --- | --- |
| **Adresa URL** |Adresa URL určuje identifikátor URI (Uniform Resource Identifier) pro jedinečný záložní soubor. Adresa URL slouží k zadání umístění a názvu záložního souboru serveru SQL Server. Adresa URL musí ukazovat na skutečný objekt blob, nikoli pouze na kontejner. Pokud objekt blob neexistuje, je vytvořen. Pokud je zadán existující objekt blob, zálohování se nezdaří, pokud není zadána možnost > s formátem. Následuje příklad adresy URL, kterou byste zadali v příkazu BACKUP: **http[s]://[storageaccount].blob.core.windows.net/[container]/[FILENAME.bak]**. Https je doporučeno, ale není vyžadováno. |
| **Přihlašovací údaj** |Informace, které jsou potřeba k připojení a ověření služby úložiště objektů blob Azure, se ukládají jako přihlašovací údaje.  Aby sql server zapisovat zálohy do objektu blob Azure nebo obnovení z něj, musí být vytvořen přihlašovací údaje SQL Server. Další informace naleznete v tématu [SQL Server Credential](https://msdn.microsoft.com/library/ms189522.aspx). |

> [!NOTE]
> Sql Server 2016 byl aktualizován na podporu objektů BLOB bloku. Další podrobnosti najdete v tématu: Další podrobnosti [najdete v článku Použití služby úložiště objektů blob microsoft azure s databázemi SQL Serveru 2016.](https://msdn.microsoft.com/library/dn466438.aspx)
> 
> 

## <a name="next-steps"></a>Další kroky
1. Pokud ho ještě nemáte, vytvořte si účet Azure. Pokud vyhodnocujete Azure, zvažte [bezplatnou zkušební verzi](https://azure.microsoft.com/free/).
2. Pak projděte jeden z následujících kurzů, které vás provedou vytvořením účtu úložiště a provedením obnovení.
   
   * **SQL Server 2014**: [Výuka: Zálohování a obnovení služby Microsoft Azure Blob Storage Service serveru SQL Server 2014](https://msdn.microsoft.com/library/jj720558\(v=sql.120\).aspx).
   * **SQL Server 2016**: [Kurz: Použití služby úložiště objektů blob Microsoft Azure s databázemi SQL Serveru 2016](https://msdn.microsoft.com/library/dn466438.aspx)
3. Projděte si další dokumentaci počínaje [sql server zálohování a obnovení se službou Microsoft Azure Blob Storage Service](https://msdn.microsoft.com/library/jj919148.aspx).

Pokud máte nějaké problémy, přečtěte si téma [SQL Server Backup to URL Best Practices and Troubleshooting](https://msdn.microsoft.com/library/jj919149.aspx).

Další možnosti zálohování a obnovení serveru SQL Server najdete v tématu [Zálohování a obnovení pro SQL Server ve virtuálních počítačích Azure](virtual-machines-windows-sql-backup-recovery.md).

