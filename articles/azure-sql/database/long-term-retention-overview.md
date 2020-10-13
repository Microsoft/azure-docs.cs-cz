---
title: Dlouhodobé uchovávání záloh
titleSuffix: Azure SQL Database & Azure SQL Managed Instance
description: Přečtěte si, jak Azure SQL Database & podpora spravovaných instancí Azure SQL ukládání úplných záloh databáze po dobu až 10 let prostřednictvím dlouhodobých zásad uchovávání informací.
services: sql-database
ms.service: sql-database
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, sstein
ms.date: 05/18/2019
ms.openlocfilehash: 83d3bb78ef27af377b0a8c5edf75f658a0ca93e7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91450232"
---
# <a name="long-term-retention---azure-sql-database-and-azure-sql-managed-instance"></a>Dlouhodobé uchovávání – Azure SQL Database a Azure SQL Managed instance

Mnoho aplikací má zákonné, právní nebo jiné obchodní účely, které vyžadují, abyste zachovali zálohy databáze po 7-35 dnů poskytovaných Azure SQL Database a [automatickými zálohami](automated-backups-overview.md)spravované instance Azure SQL. Pomocí funkce dlouhodobá doba uchovávání (LTR) můžete ukládat zadané SQL Database a úplné zálohy spravované instance SQL do úložiště objektů BLOB v Azure s [nakonfigurovanou redundancí](automated-backups-overview.md#backup-storage-redundancy) až po dobu 10 let. Pak můžete obnovit jakoukoli zálohu jako novou databázi.

Je možné povolit dlouhou dobu uchovávání Azure SQL Database a je ve verzi Public Preview pro Azure SQL Managed instance. Tento článek obsahuje koncepční přehled dlouhodobého uchovávání. Pokud chcete nakonfigurovat dlouhodobé uchovávání, přečtěte si téma [konfigurace Azure SQL Database ltr](long-term-backup-retention-configure.md) a [Konfigurace spravované instance Azure SQL ltr](../managed-instance/long-term-backup-retention-configure.md). 

> [!NOTE]
> Úlohy agenta SQL můžete použít k naplánování [záloh databáze pouze kopírování](https://docs.microsoft.com/sql/relational-databases/backup-restore/copy-only-backups-sql-server) jako alternativu k více než 35 dnům.


## <a name="how-long-term-retention-works"></a>Jak dlouhodobé uchovávání funguje
     
Dlouhodobá doba uchovávání záloh (LTR) využívá úplné zálohy databáze, které se [automaticky vytvoří](automated-backups-overview.md) , aby se povolilo obnovení bodu a času (PITR). Pokud je nakonfigurovaná zásada LTR, zkopírují se tyto zálohy do různých objektů BLOB pro dlouhodobé uložení. Kopie je úloha na pozadí, která nemá žádný vliv na výkon úlohy databáze. Zásady LTR pro každou databázi v SQL Database můžou také určovat, jak často se vytvářejí zálohy LTR.

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

   ![Příklad ltr](./media/long-term-retention-overview/ltr-example.png)


Pokud upravíte výše uvedenou zásadu a nastavíte W = 0 (bez týdenního zálohování), tempo se záložní kopie změní, jak je uvedeno ve výše uvedené tabulce zvýrazněnými daty. Velikost úložiště potřebná k tomu, aby se tyto zálohy mohly snížit, se odpovídajícím způsobem sníží. 

> [!IMPORTANT]
> Načasování individuálních záloh LTR řídí Azure. Nemůžete ručně vytvořit zálohu LTR nebo řídit časování vytváření zálohy. Po nakonfigurování zásady LTR může trvat až 7 dní, než se v seznamu dostupných záloh zobrazí první záloha LTR.  


## <a name="geo-replication-and-long-term-backup-retention"></a>Geografická replikace a dlouhodobé uchovávání záloh

Pokud jako řešení provozní kontinuity používáte aktivní geografickou replikaci nebo skupiny převzetí služeb při selhání, měli byste připravit na případné převzetí služeb při selhání a nakonfigurovat stejnou zásadu LTR v sekundární databázi nebo instanci. Náklady na úložiště LTR se nebudou zvyšovat, protože zálohy se negenerují z nesekundárních. Zálohy se vytvoří jenom v případě, že sekundární primární databáze vytvoří zálohy. Zajišťuje nepřerušenou generaci záloh LTR při aktivaci převzetí služeb při selhání a primární se přesune do sekundární oblasti. 

> [!NOTE]
> Když se původní primární databáze obnoví z výpadku, který způsobil převzetí služeb při selhání, stane se nová sekundární. Vytvoření zálohy proto nebude pokračovat a stávající zásady LTR se neprojeví, dokud se znovu nevrátí k primárnímu. 

## <a name="sql-managed-instance-support"></a>Podpora SQL Managed Instance

Použití dlouhodobého uchovávání záloh se spravovanou instancí Azure SQL má následující omezení:

- **Omezená verze Public Preview** – Tato verze Preview je dostupná jenom pro předplatná EA a CSP a podléhá omezené dostupnosti.  
- [**Jenom PowerShell**](../managed-instance/long-term-backup-retention-configure.md) – momentálně není dostupná žádná Azure Portal podpora. LTR musí být povoleno pomocí prostředí PowerShell. 

Pokud chcete požádat o registraci, vytvořte [lístek podpory Azure](https://azure.microsoft.com/support/create-ticket/). Pro typ problému vyberte technický problém, u možnosti služba zvolte spravovanou instanci SQL a jako typ problému vyberte **zálohování, obnovení a kontinuitu podnikových/dlouhodobého uchovávání záloh**. Ve vaší žádosti prosím uveďte stav, který chcete zaregistrovat ve verzi Public Preview pro spravovanou instanci SQL s omezením.

## <a name="configure-long-term-backup-retention"></a>Konfigurace dlouhodobého uchovávání záloh

Dlouhodobou dobu uchovávání záloh můžete nakonfigurovat pomocí Azure Portal a PowerShellu pro Azure SQL Database a PowerShellu pro Azure SQL Managed instance. Chcete-li obnovit databázi z úložiště LTR, můžete vybrat konkrétní zálohování na základě jeho časového razítka. Databázi můžete obnovit do libovolného existujícího serveru nebo spravované instance v rámci stejného předplatného, jako má původní databáze.

Informace o tom, jak nakonfigurovat dlouhodobé uchovávání nebo obnovení databáze ze zálohy pro SQL Database pomocí Azure Portal nebo PowerShellu najdete v tématu [správa Azure SQL Database dlouhodobého uchovávání záloh](long-term-backup-retention-configure.md) .

Informace o tom, jak nakonfigurovat dlouhodobé uchovávání nebo obnovení databáze ze zálohy pro spravovanou instanci SQL pomocí PowerShellu, najdete v tématu [Správa dlouhodobého uchování záloh spravované instance Azure SQL](../managed-instance/long-term-backup-retention-configure.md).

Chcete-li obnovit databázi z úložiště LTR, můžete vybrat konkrétní zálohování na základě jeho časového razítka. Databázi můžete obnovit na jakýkoli existující server v rámci stejného předplatného, jako je původní databáze. Informace o tom, jak obnovit databázi ze zálohy LTR, pomocí Azure Portal nebo PowerShellu najdete v tématu [správa Azure SQL Database dlouhodobé uchovávání záloh](long-term-backup-retention-configure.md). Ve vaší žádosti prosím uveďte stav, který chcete zaregistrovat pro spravovanou instanci SQL s omezením verze Public Preview.

## <a name="next-steps"></a>Další kroky

Vzhledem k tomu, že zálohy databáze chrání data před náhodným poškozením nebo odstraněním, jsou zásadní součástí jakékoli strategie pro provozní kontinuitu a zotavení po havárii. Další informace o ostatních řešeních pro provozní kontinuitu SQL Database najdete v tématu [Přehled provozní kontinuity](business-continuity-high-availability-disaster-recover-hadr-overview.md).
 
