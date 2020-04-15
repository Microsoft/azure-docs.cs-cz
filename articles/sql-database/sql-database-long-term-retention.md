---
title: Dlouhodobé uchovávání záloh
description: Zjistěte, jak Azure SQL Database podporuje ukládání úplných záloh databáze po dobu až 10 let prostřednictvím zásad dlouhodobého uchovávání informací.
services: sql-database
ms.service: sql-database
ms.subservice: backup-restore
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, carlrab
ms.date: 05/18/2019
ms.openlocfilehash: d015eea21bcfa499d6751e024a882a7316b7f1a5
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/14/2020
ms.locfileid: "81380761"
---
# <a name="azure-sql-database-long-term-retention"></a>Azure SQL Database dlouhodobé uchovávání

Mnoho aplikací má regulační předpisy, dodržování předpisů nebo jiné obchodní účely, které vyžadují uchování záloh databáze po dobu 7 až 35 dnů, které poskytuje [automatické zálohování](sql-database-automated-backups.md)Azure SQL Database . Pomocí funkce dlouhodobé uchovávání (LTR) můžete ukládat zadané úplné zálohy databáze SQL v úložišti objektů Blob Azure s geograficky redundantním úložištěm pro čtení až na 10 let. Potom můžete obnovit libovolnou zálohu jako novou databázi. Další informace o redundanci Azure Storage najdete v [tématu redundance Azure Storage](../storage/common/storage-redundancy.md). 

Dlouhodobé uchovávání informací lze povolit pro jednu a sdruženou databází a je v omezené matné verzi verze verze pro spravované instance Azure SQL Database. 

> [!NOTE]
> Úlohy sql agenta můžete naplánovat [zálohování pouze zkopírované databáze](https://docs.microsoft.com/sql/relational-databases/backup-restore/copy-only-backups-sql-server) jako alternativu k LTR po dobu 35 dnů.


## <a name="how-sql-database-long-term-retention-works"></a>Jak funguje dlouhodobé uchovávání databáze SQL Database

Dlouhodobé uchovávání záloh (LTR) využívá úplné zálohy databáze, které jsou [automaticky vytvořeny](sql-database-automated-backups.md) k povolení obnovení bodu času (PITR). Pokud je nakonfigurovaná zásada LTR, tyto zálohy se zkopírují do různých objektů BLOB pro dlouhodobé úložiště. Kopie je úloha na pozadí, která nemá žádný vliv na výkon na zatížení databáze. Zásady LTR pro každou databázi SQL můžete také určit, jak často jsou vytvářeny zálohy LTR.

Chcete-li povolit LTR, můžete definovat zásady pomocí kombinace čtyř parametrů: týdenní uchovávání záloh (W), měsíční uchovávání záloh (M), roční uchovávání záloh (Y) a týden v roce (WeekOfYear). Pokud zadáte W, jedna záloha každý týden se zkopíruje do dlouhodobého úložiště. Pokud zadáte M, první záloha každého měsíce bude zkopírována do dlouhodobého úložiště. Pokud zadáte Y, jedna záloha během týdne určeného WeekOfYear budou zkopírovány do dlouhodobého úložiště. Pokud zadaný WeekOfYear je v minulosti při konfiguraci zásady, první záloha LTR bude vytvořena v následujícím roce. Každá záloha bude uložena v dlouhodobém úložišti podle parametrů zásad, které jsou nakonfigurovány při vytvoření zálohy LTR.

> [!NOTE]
> Jakákoli změna zásad LTR se vztahuje pouze na budoucí zálohy. Pokud je například upraveno týdenní uchovávání záloh (W), měsíční uchovávání záloh (M) nebo roční uchovávání záloh (Y), nové nastavení uchovávání informací se bude vztahovat pouze na nové zálohy. Uchovávání existujících záloh nebude změněno. Pokud máte v úmyslu odstranit staré zálohy LTR před vypršením doby jejich uchování, budete muset [ručně odstranit zálohy](https://docs.microsoft.com/azure/sql-database/sql-database-long-term-backup-retention-configure#delete-ltr-backups).
> 

Příklady zásad LTR:

-  W=0, M=0, Y=5, Týdenroku=3

   Třetí plná záloha každého roku bude uchovávána po dobu pěti let.
   
- W=0, M=3, Y=0

   První úplná záloha každého měsíce bude uchovávána po dobu tří měsíců.

- W=12, M=0, Y=0

   Každá týdenní plná záloha bude uchovávána po dobu 12 týdnů.

- W=6, M=12, Y=10, Týdenroku=16

   Každá týdenní plná záloha bude uchovávána po dobu šesti týdnů. Kromě první úplné zálohy každého měsíce, která bude uchovávána po dobu 12 měsíců. Kromě úplné zálohy pořízené v 16. týdnu roku, která bude uchovávána po dobu 10 let. 

Následující tabulka znázorňuje kadenci a vypršení platnosti dlouhodobých záloh pro následující zásady:

W = 12 týdnů (84 dní), M = 12 měsíců (365 dní), Y = 10 let (3650 dní), WeekOfYear = 15 (týden po dubnu 15)

   ![ltr příklad](./media/sql-database-long-term-retention/ltr-example.png)



Pokud změníte výše uvedené zásady a nastavíte W = 0 (žádné týdenní zálohy), kadence záložních kopií se změní, jak je znázorněno ve výše uvedené tabulce zvýrazněnými daty. Množství úložiště potřebné k udržení těchto záloh by odpovídajícím způsobem snížit. 

> [!IMPORTANT]
> Časování jednotlivých záloh LTR je řízeno Azure SQL Database. Nelze ručně vytvořit zálohu LTR nebo řídit časování vytvoření zálohy. Po konfiguraci zásad LTR může trvat až 7 dní, než se první záloha LTR zobrazí v seznamu dostupných záloh.  
> 

## <a name="geo-replication-and-long-term-backup-retention"></a>Geografická replikace a dlouhodobé uchovávání záloh

Pokud jako řešení kontinuity podnikání používáte aktivní skupiny geografické replikace nebo převzetí služeb při selhání, měli byste se připravit na případná převzetí služeb při selhání a nakonfigurovat stejnou zásadu LTR v geograficky sekundární databázi. Náklady na úložiště LTR se nezvýší, protože zálohy nejsou generovány ze sekundárních. Pouze v případě, že sekundární stane primární zálohy budou vytvořeny. Zajišťuje nepřerušené generování záloh LTR při aktivaci převzetí služeb při selhání a primární přesune do sekundární oblasti. 

> [!NOTE]
> Když se původní primární databáze obnoví z výpadku, který způsobil převzetí služeb při selhání, stane se novým sekundárním. Proto vytvoření zálohy nebude pokračovat a existující zásady LTR se projeví, dokud se stane primární znovu. 

## <a name="managed-instance-support"></a>Podpora spravované instance

Použití dlouhodobého uchovávání záloh u instancí spravované hodnou databází Azure SQL Database má následující omezení:

- **Omezená veřejná verze preview** – tato předběžná verze je dostupná jenom pro předplatná EA a CSP a závisí na omezené dostupnosti.  
- [**Jenom PowerShell**](sql-database-managed-instance-long-term-backup-retention-configure.md) – momentálně neexistuje žádná podpora portálu Azure. LTR musí být povolena pomocí prostředí PowerShell. 

Chcete-li požádat o registraci, vytvořte [lístek podpory Azure](https://azure.microsoft.com/support/create-ticket/) pod tématem podpory **Zálohování, Obnovení a kontinuita podnikání / Dlouhodobé uchovávání záloh**.


## <a name="configure-long-term-backup-retention"></a>Konfigurace dlouhodobého uchovávání záloh

Informace o konfiguraci dlouhodobého uchovávání pomocí webu Azure Portal nebo PowerShellu najdete v [tématu Správa dlouhodobého uchovávání záloh v Azure SQL Database](sql-database-long-term-backup-retention-configure.md).

## <a name="restore-database-from-ltr-backup"></a>Obnovení databáze ze zálohy LTR

Chcete-li obnovit databázi z úložiště LTR, můžete vybrat konkrétní zálohu na základě jeho časového razítka. Databázi lze obnovit na libovolný existující server pod stejným odběrem jako původní databáze. Informace o tom, jak obnovit databázi ze zálohy LTR, pomocí portálu Azure nebo PowerShellu, najdete [v tématu Správa dlouhodobého uchovávání záloh v Azure SQL Database](sql-database-long-term-backup-retention-configure.md).

## <a name="next-steps"></a>Další kroky

Vzhledem k tomu, že zálohy databáze chrání data před náhodným poškozením nebo odstraněním, jsou nezbytnou součástí každé strategie kontinuity podnikání a zotavení po havárii. Další informace o dalších řešeních kontinuity provozu databáze SQL Database naleznete v [tématu Přehled kontinuity provozu](sql-database-business-continuity.md).
