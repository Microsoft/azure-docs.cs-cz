---
title: Store Azure SQL Database zálohy až 10 let | Dokumentace Microsoftu
description: Zjistěte, jak Azure SQL Database podporuje ukládání úplné zálohy databáze po dobu až 10 let.
services: sql-database
ms.service: sql-database
ms.subservice: backup-restore
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, carlrab
manager: craigg
ms.date: 05/18/2019
ms.openlocfilehash: 6549892bfd04065bf83ab50fa5f5b439c35c4238
ms.sourcegitcommit: 156b313eec59ad1b5a820fabb4d0f16b602737fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/18/2019
ms.locfileid: "67190547"
---
# <a name="store-azure-sql-database-backups-for-up-to-10-years"></a>Store Azure SQL Database zálohy až 10 let

Máte spoustu aplikací na dodržování legislativních, dodržování předpisů nebo jiné obchodní účely, které vyžadují, abyste pro uchovávání záloh databáze se po uplynutí 7 – 35 dnů poskytuje Azure SQL Database [automatické zálohování](sql-database-automated-backups.md). Když použijete funkci dlouhodobé uchovávání dat (LTR), můžete uložit zadaný SQL database úplné zálohy v [RA-GRS](../storage/common/storage-redundancy-grs.md#read-access-geo-redundant-storage) úložiště objektů blob až 10 let. Pak můžete libovolnou zálohu obnovit jako novou databázi.

> [!NOTE]
> Pro jednu databázi, databázi ve fondu je možné povolit zleva doprava. To je ještě nejsou k dispozici pro instanci databáze ve spravovaných instancí. Úlohy agenta SQL serveru můžete použít k naplánování [pouze kopie zálohy](https://docs.microsoft.com/sql/relational-databases/backup-restore/copy-only-backups-sql-server) jako alternativu k LTR nad rámec po dobu 35 dní.
> 

## <a name="how-sql-database-long-term-retention-works"></a>Jak funguje dlouhodobé uchovávání databáze SQL

Dlouhodobé uchovávání záloh (LTR) využívá zálohování úplné databáze, které jsou [automaticky vytvořené](sql-database-automated-backups.md) umožňující čas bodu obnovení (PITR). Pokud je nakonfigurované zásady LTR, tyto zálohy se zkopírují do různých objektů blob pro dlouhodobé uložení. Operace kopírování je úloha na pozadí, který nemá žádný vliv na výkon na zatížení databáze. LTR zálohy se uchovávají po dobu nastavenou zásadami zleva doprava. Zásady LTR pro každou databázi SQL můžete také určit, jak často se vytvoří zálohy zleva doprava. Povolit flexibilita můžete definovat zásady pomocí kombinace čtyři parametry: týdenní uchovávání záloh (W) měsíční uchovávání záloh (M), roční uchovávání záloh (Y) a týden v roce (WeekOfYear). Pokud zadáte W, jediná záloha každý týden bude zkopírován do dlouhodobého úložiště. Pokud chcete zadat M, jediná záloha během první týden každého měsíce se zkopírují do dlouhodobého úložiště. Pokud chcete zadat Y, jediná záloha v týdnu určeném WeekOfYear budou zkopírovány do dlouhodobého úložiště. Každá záloha se zachová do dlouhodobého úložiště pro tyto parametry zadané období. Změny zásad LTR platí pro budoucí zálohy. Například pokud zadaný WeekOfYear je v minulosti, kdy jsou nakonfigurované zásady, první zálohování LTR vytvoří se další rok. 

Příklady zásad LTR:

-  W = 0, M = 0, Y = 5, WeekOfYear = 3

   Třetí úplné zálohování každý rok se uchovají po dobu pěti let.
   
- W = 0, M = 3, Y = 0

   Po dobu tří měsíců zůstanou zachovaná, bude první úplná záloha každý měsíc.

- W = 12, M = 0, Y = 0

   Každý týdenní úplné zálohování se uchovají 12 týdnů.

- W = 6, M = 12, Y = 10 WeekOfYear = 16

   Každý týdenní úplné zálohování budou zachovány pro šest týdnů. S výjimkou první úplná záloha každý měsíc, které se uchovají po dobu 12 měsíců. Kromě úplné zálohy pořízené 16 týden v roce, které se uchovají po dobu 10 let. 

Následující tabulka znázorňuje tempo a dlouhodobého zálohování pro následující zásady vypršení platnosti:

W = 12 týdnů (84 dní), M = 12 měsíců (365 dní), Y = 10 let (3650 dnů) WeekOfYear = 15 (týden po 15. dubna)

   ![Příklad zleva doprava.](./media/sql-database-long-term-retention/ltr-example.png)



Pokud upravíte výše uvedenou zásadu a W = 0 (žádné týdenní zálohy), tempo sadu záložní kopie se změní zobrazené v tabulce výše zvýrazněná data. Velikost úložiště potřebných k zajištění tyto zálohy by došlo ke snížení odpovídajícím způsobem. 

> [!IMPORTANT]
> Služba Azure SQL Database je řízen časování jednotlivých záloh zleva doprava. Nelze ručně vytvořit zálohu zleva doprava nebo ovlivnit načasování vytvoření zálohy. Po konfiguraci zásady LTR, může trvat až 7 dní před první zálohování LTR se zobrazí v seznamu dostupných záloh.  
> 

## <a name="geo-replication-and-long-term-backup-retention"></a>Geografická replikace a dlouhodobé uchovávání záloh

Pokud používáte aktivní geografickou replikaci nebo skupin převzetí služeb při selhání jako řešení obchodní kontinuity podnikových procesů, by měla připravit pro konečné převzetí služeb při selhání a konfigurace stejné zásady LTR na geograficky sekundární databázi. Vaše náklady na úložiště LTR nenarůstá jako zálohy se negenerují ze sekundární databáze. Pouze v případě, že sekundární stane primární zálohy se vytvoří. To zajišťuje bez přerušení generování zálohování LTR při převzetí služeb při selhání se aktivuje a přesune primární do sekundární oblasti. 

> [!NOTE]
> Pokud původní primární databáze provádí zotavení z výpadku, který způsobil převzetí služeb při selhání, bude nový sekundární. Proto nebude pokračovat vytvoření zálohy a existující zásady LTR neprojeví, dokud nebude primární znovu. 

## <a name="configure-long-term-backup-retention"></a>Konfigurace dlouhodobého uchovávání záloh

Zjistěte, jak nakonfigurovat dlouhodobé uchovávání pomocí webu Azure portal nebo Powershellu, najdete v článku [dlouhodobého uchovávání záloh Správa služby Azure SQL Database](sql-database-long-term-backup-retention-configure.md).

## <a name="restore-database-from-ltr-backup"></a>Obnovit databázi ze zálohy zleva doprava.

Chcete-li obnovit databázi z úložiště LTR, můžete vybrat konkrétní zálohy založené na její časové razítko. Databázi můžete obnovit do jakékoli existující server ve stejném předplatném jako původní databáze. Další informace o obnovení databáze ze zálohy zleva doprava, pomocí webu Azure portal nebo Powershellu, najdete v článku [dlouhodobého uchovávání záloh Správa služby Azure SQL Database](sql-database-long-term-backup-retention-configure.md).

## <a name="next-steps"></a>Další postup

Protože zálohy databáze chránit data před náhodným poškozením nebo odstranění, jsou důležitou součástí žádné provozní kontinuitu a strategii zotavení po havárii. Další informace o jiných řešení obchodní kontinuity SQL Database najdete v tématu [přehled zajištění provozní kontinuity firmy](sql-database-business-continuity.md).
