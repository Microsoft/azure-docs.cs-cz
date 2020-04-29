---
title: Dlouhodobé uchovávání záloh
description: Přečtěte si, jak Azure SQL Database podporuje ukládání úplných záloh databáze po dobu až 10 let prostřednictvím dlouhodobých zásad uchovávání informací.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "81380761"
---
# <a name="azure-sql-database-long-term-retention"></a>Azure SQL Database dlouhodobé uchovávání

Mnoho aplikací má zákonné, dodržování předpisů nebo jiné obchodní účely, které vyžadují, abyste zachovali zálohy databáze po 7-35 dnů, které Azure SQL Database [Automatické zálohy](sql-database-automated-backups.md)poskytují. Pomocí funkce dlouhodobá doba uchovávání (LTR) můžete ukládat zadané úplné zálohy SQL Database do úložiště objektů BLOB v Azure s geograficky redundantním úložištěm s přístupem pro čtení po dobu až 10 let. Pak můžete obnovit jakoukoli zálohu jako novou databázi. Další informace o Azure Storage redundanci najdete v tématu [Azure Storage redundance](../storage/common/storage-redundancy.md). 

Dlouhodobá doba uchovávání se dá povolit pro databáze s jednou a ve fondu a je v omezeném formátu Public Preview pro Azure SQL Database spravované instance. 

> [!NOTE]
> Úlohy agenta SQL můžete použít k naplánování [záloh databáze pouze kopírování](https://docs.microsoft.com/sql/relational-databases/backup-restore/copy-only-backups-sql-server) jako alternativu k více než 35 dnům.


## <a name="how-sql-database-long-term-retention-works"></a>Jak funguje dlouhodobé uchovávání databáze SQL Database

Dlouhodobá doba uchovávání záloh (LTR) využívá úplné zálohy databáze, které se [automaticky vytvoří](sql-database-automated-backups.md) , aby se povolilo obnovení bodu a času (PITR). Pokud je nakonfigurovaná zásada LTR, zkopírují se tyto zálohy do různých objektů BLOB pro dlouhodobé uložení. Kopie je úloha na pozadí, která nemá žádný vliv na výkon úlohy databáze. Zásady LTR pro každou databázi SQL umožňují také určit, jak často se budou vytvářet zálohy LTR.

Pokud chcete povolit LTR, můžete definovat zásady s použitím kombinace čtyř parametrů: týdenní uchovávání záloh (W), uchovávání měsíčního uchovávání záloh (M), ročního uchovávání záloh (Y) a týdenního roku (WeekOfYear). Pokud zadáte W, bude jedna záloha každý týden zkopírována do dlouhodobého úložiště. Zadáte-li M, bude první záloha každého měsíce zkopírována do dlouhodobého úložiště. Pokud zadáte Y, bude do dlouhodobého úložiště zkopírována jedna záloha během týdne určeného parametrem WeekOfYear. Pokud je zadaný WeekOfYear v minulosti, když je zásada nakonfigurovaná, vytvoří se první záloha LTR v následujícím roce. Každá záloha bude uchována v dlouhodobém úložišti podle parametrů zásad, které jsou konfigurovány při vytvoření zálohy LTR.

> [!NOTE]
> Jakékoli změny v zásadě LTR se vztahují pouze na budoucí zálohy. Pokud se například upraví týdenní uchovávání záloh (W), měsíční uchovávání záloh (M) nebo roční uchovávání záloh (Y), bude se nové nastavení uchovávání vztahovat jenom na nové zálohy. Uchování stávajících záloh se neupraví. Pokud máte v úmyslu odstranit staré zálohy LTR ještě před uplynutím doby uchování, budete muset [zálohy ručně odstranit](https://docs.microsoft.com/azure/sql-database/sql-database-long-term-backup-retention-configure#delete-ltr-backups).
> 

Příklady zásad LTR:

-  W = 0, M = 0, Y = 5, WeekOfYear = 3

   Třetí úplná záloha každého roku se uchová po dobu pěti let.
   
- W = 0, M = 3, Y = 0

   První úplná záloha každého měsíce bude uchována po dobu tří měsíců.

- W = 12, M = 0, Y = 0

   Každé týdenní úplné zálohování bude uchováno po dobu 12 týdnů.

- W = 6, M = 12, Y = 10, WeekOfYear = 16

   Každé týdenní úplné zálohování bude uchováno po dobu šesti týdnů. Kromě prvního úplného zálohování každého měsíce, který bude zachován po dobu 12 měsíců. S výjimkou úplného zálohování pořízeného na šestnáctý týden roku, který bude zachován po dobu 10 let. 

Následující tabulka ilustruje tempo a vypršení platnosti dlouhodobých záloh pro následující zásady:

W = 12 týdnů (84 dní), M = 12 měsíců (365 dnů), Y = 10 let (3650 dní), WeekOfYear = 15 (týden po 15. dubnu)

   ![Příklad ltr](./media/sql-database-long-term-retention/ltr-example.png)



Pokud upravíte výše uvedenou zásadu a nastavíte W = 0 (bez týdenního zálohování), tempo se záložní kopie změní, jak je uvedeno ve výše uvedené tabulce zvýrazněnými daty. Velikost úložiště potřebná k tomu, aby se tyto zálohy mohly snížit, se odpovídajícím způsobem sníží. 

> [!IMPORTANT]
> Časování jednotlivých záloh LTR je řízeno Azure SQL Database. Nemůžete ručně vytvořit zálohu LTR nebo řídit časování vytváření zálohy. Po nakonfigurování zásady LTR může trvat až 7 dní, než se v seznamu dostupných záloh zobrazí první záloha LTR.  
> 

## <a name="geo-replication-and-long-term-backup-retention"></a>Geografická replikace a dlouhodobé uchovávání záloh

Pokud jako řešení provozní kontinuity používáte aktivní geografickou replikaci nebo skupiny převzetí služeb při selhání, měli byste se připravit na případné převzetí služeb při selhání a nakonfigurovat stejnou zásadu LTR na geograficky sekundární databázi. Náklady na úložiště LTR se nebudou zvyšovat, protože zálohy se negenerují z nesekundárních. Pouze v případě, že sekundární se změní na primární, vytvoří se zálohy. Zajišťuje nepřerušenou generaci záloh LTR při aktivaci převzetí služeb při selhání a primární se přesune do sekundární oblasti. 

> [!NOTE]
> Když se původní primární databáze obnoví z výpadku, který způsobil převzetí služeb při selhání, stane se nová sekundární. Vytvoření zálohy proto nebude pokračovat a stávající zásady LTR se neprojeví, dokud se znovu nevrátí k primárnímu. 

## <a name="managed-instance-support"></a>Podpora spravované instance

Použití dlouhodobého uchovávání záloh s Azure SQL Database spravovanými instancemi má následující omezení:

- **Omezená verze Public Preview** – Tato verze Preview je dostupná jenom pro předplatná EA a CSP a podléhá omezené dostupnosti.  
- [**Jenom PowerShell**](sql-database-managed-instance-long-term-backup-retention-configure.md) – momentálně není dostupná žádná Azure Portal podpora. LTR musí být povoleno pomocí prostředí PowerShell. 

Pokud chcete požádat o registraci, vytvořte [lístek podpory Azure](https://azure.microsoft.com/support/create-ticket/) pod tématem podpory **zálohování, obnovení a provozní kontinuita/dlouhodobé uchovávání záloh**.


## <a name="configure-long-term-backup-retention"></a>Konfigurace dlouhodobého uchovávání záloh

Informace o tom, jak nakonfigurovat dlouhodobé uchovávání pomocí Azure Portal nebo PowerShellu, najdete v tématu [správa Azure SQL Database dlouhodobé uchovávání záloh](sql-database-long-term-backup-retention-configure.md).

## <a name="restore-database-from-ltr-backup"></a>Obnovit databázi ze zálohy LTR

Chcete-li obnovit databázi z úložiště LTR, můžete vybrat konkrétní zálohování na základě jeho časového razítka. Databázi můžete obnovit na jakýkoli existující server v rámci stejného předplatného, jako je původní databáze. Informace o tom, jak obnovit databázi ze zálohy LTR, pomocí Azure Portal nebo PowerShellu najdete v tématu [správa Azure SQL Database dlouhodobé uchovávání záloh](sql-database-long-term-backup-retention-configure.md).

## <a name="next-steps"></a>Další kroky

Vzhledem k tomu, že zálohy databáze chrání data před náhodným poškozením nebo odstraněním, jsou zásadní součástí jakékoli strategie pro provozní kontinuitu a zotavení po havárii. Další informace o ostatních řešeních pro provozní kontinuitu SQL Database najdete v tématu [Přehled provozní kontinuity](sql-database-business-continuity.md).
