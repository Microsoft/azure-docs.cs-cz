---
title: Ukládat zálohy Azure SQL Database po dobu až 10 let
description: Přečtěte si, jak Azure SQL Database podporuje ukládání úplných záloh databáze po dobu až 10 let.
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
ms.openlocfilehash: 5d6f0797802a622ada1916752bc35c1bae2cde9f
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/06/2019
ms.locfileid: "73689511"
---
# <a name="store-azure-sql-database-backups-for-up-to-10-years"></a>Ukládat zálohy Azure SQL Database po dobu až 10 let

Mnoho aplikací má zákonné, dodržování předpisů nebo jiné obchodní účely, které vyžadují, abyste zachovali zálohy databáze po 7-35 dnů, které Azure SQL Database [Automatické zálohy](sql-database-automated-backups.md)poskytují. Pomocí funkce dlouhodobá doba uchovávání (LTR) můžete do úložiště objektů BLOB v [RA-GRS](../storage/common/storage-redundancy-grs.md#read-access-geo-redundant-storage) ukládat zadané úplné zálohy SQL Database po dobu až 10 let. Pak můžete libovolnou zálohu obnovit jako novou databázi.

> [!NOTE]
> U databází s jednou a ve fondu se dá povolit LTR. Ještě není k dispozici pro databáze instancí ve spravovaných instancích. Úlohy agenta SQL můžete použít k naplánování [záloh databáze pouze kopírování](https://docs.microsoft.com/sql/relational-databases/backup-restore/copy-only-backups-sql-server) jako alternativu k více než 35 dnům.
> 

## <a name="how-sql-database-long-term-retention-works"></a>Jak SQL Database dlouhodobá doba uchovávání

Dlouhodobá doba uchovávání záloh (LTR) využívá úplné zálohy databáze, které se [automaticky vytvoří](sql-database-automated-backups.md) , aby se povolilo obnovení bodu a času (PITR). Pokud je nakonfigurovaná zásada LTR, zkopírují se tyto zálohy do různých objektů BLOB pro dlouhodobé uložení. Operace kopírování je úloha na pozadí, která nemá žádný vliv na výkon úlohy databáze. Zálohy LTR se uchovávají po dobu nastavenou zásadou LTR. Zásady LTR pro každou databázi SQL umožňují také určit, jak často se budou vytvářet zálohy LTR. Chcete-li tuto flexibilitu povolit, můžete definovat zásady pomocí kombinace čtyř parametrů: týdenní uchovávání záloh (W), měsíční uchovávání záloh (M), roční uchování záloh (Y) a týden roku (WeekOfYear). Pokud zadáte W, bude jedna záloha každý týden zkopírována do dlouhodobého úložiště. Pokud zadáte M, bude jedna záloha během prvního týdne každého měsíce zkopírována do dlouhodobého úložiště. Pokud zadáte Y, bude do dlouhodobého úložiště zkopírována jedna záloha během týdne určeného parametrem WeekOfYear. Každá záloha bude zachována v dlouhodobém úložišti po dobu určenou pomocí těchto parametrů. Jakékoli změny zásad LTR se vztahují na budoucí zálohy. Pokud je například zadaný WeekOfYear v minulosti při konfiguraci zásady, vytvoří se první záloha LTR v příštím roce. 

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

## <a name="configure-long-term-backup-retention"></a>Konfigurace dlouhodobého uchovávání záloh

Informace o tom, jak nakonfigurovat dlouhodobé uchovávání pomocí Azure Portal nebo PowerShellu, najdete v tématu [správa Azure SQL Database dlouhodobé uchovávání záloh](sql-database-long-term-backup-retention-configure.md).

## <a name="restore-database-from-ltr-backup"></a>Obnovit databázi ze zálohy LTR

Chcete-li obnovit databázi z úložiště LTR, můžete vybrat konkrétní zálohování na základě jeho časového razítka. Databázi můžete obnovit na jakýkoli existující server v rámci stejného předplatného, jako je původní databáze. Informace o tom, jak obnovit databázi ze zálohy LTR pomocí Azure Portal nebo PowerShellu, najdete v tématu [správa Azure SQL Database dlouhodobé uchovávání záloh](sql-database-long-term-backup-retention-configure.md).

## <a name="next-steps"></a>Další kroky

Vzhledem k tomu, že zálohy databáze chrání data před náhodným poškozením nebo odstraněním, jsou zásadní součástí jakékoli strategie pro provozní kontinuitu a zotavení po havárii. Další informace o ostatních řešeních pro provozní kontinuitu SQL Database najdete v tématu [Přehled provozní kontinuity](sql-database-business-continuity.md).
