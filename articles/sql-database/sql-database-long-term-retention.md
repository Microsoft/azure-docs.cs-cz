---
title: Store Azure SQL Database zálohy až 10 let | Dokumentace Microsoftu
description: Zjistěte, jak Azure SQL Database podporuje ukládání úplné zálohy databáze po dobu až 10 let.
services: sql-database
ms.service: sql-database
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: carlrab
manager: craigg
ms.date: 07/16/2018
ms.openlocfilehash: b2d4166c25014416efbb7392acda3f3f028b4fa9
ms.sourcegitcommit: 51a1476c85ca518a6d8b4cc35aed7a76b33e130f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2018
ms.locfileid: "47162034"
---
# <a name="store-azure-sql-database-backups-for-up-to-10-years"></a>Store Azure SQL Database zálohy až 10 let

Máte spoustu aplikací na dodržování legislativních, dodržování předpisů nebo jiné obchodní účely, které vyžadují, abyste pro uchovávání záloh databáze se po uplynutí 7 – 35 dnů poskytuje Azure SQL Database [automatické zálohování](sql-database-automated-backups.md). Když použijete funkci dlouhodobé uchovávání dat (LTR), můžete uložit zadaný SQL database úplné zálohy v [RA-GRS](../storage/common/storage-redundancy-grs.md#read-access-geo-redundant-storage) úložiště objektů blob až 10 let. Pak můžete obnovit jakékoli zálohy jako novou databázi.

## <a name="how-sql-database-long-term-retention-works"></a>Jak funguje dlouhodobé uchovávání databáze SQL

Dlouhodobé uchovávání záloh využívá [automatického zálohování SQL Database](sql-database-automated-backups.md) vytvořili zabere čas bodu obnovení (PITR). Můžete nakonfigurovat zásadu dlouhodobého uchovávání pro každou databázi SQL a určit, jak často je potřeba zkopírovat do dlouhodobého úložiště záloh. Povolit flexibilita můžete definovat zásady pomocí kombinace čtyři parametry: týdenní uchovávání záloh (W) měsíční uchovávání záloh (M), roční uchovávání záloh (Y) a týden v roce (WeekOfYear). Pokud zadáte W, jediná záloha každý týden bude zkopírován do dlouhodobého úložiště. Pokud chcete zadat M, jediná záloha během první týden každého měsíce se zkopírují do dlouhodobého úložiště. Pokud chcete zadat Y, jediná záloha v týdnu určeném WeekOfYear budou zkopírovány do dlouhodobého úložiště. Každá záloha se zachová do dlouhodobého úložiště pro tyto parametry zadané období. 

Příklady:

-  W = 0, M = 0, Y = 5, WeekOfYear = 3

   3. úplné zálohování každý rok se uchovávají po dobu 5 let.
- W = 0, M = 3, Y = 0

   Po dobu 3 měsíců zůstanou zachovaná, bude první úplná záloha každý měsíc.

- W = 12, M = 0, Y = 0

   Každý týdenní úplné zálohování se uchovají 12 týdnů.

- W = 6, M = 12, Y = 10 WeekOfYear = 16

   Každý týdenní úplné zálohování se uchovají 6 týdnů. S výjimkou první úplná záloha každý měsíc, které se uchovají po dobu 12 měsíců. Kromě úplné zálohy pořízené 16 týden v roce, které se uchovají po dobu 10 let. 

Následující tabulka znázorňuje tempo a dlouhodobého zálohování pro následující zásady vypršení platnosti:

W = 12 týdnů (84 dní), M = 12 měsíců (365 dní), Y = 10 let (3650 dnů) WeekOfYear = 15 (týden po 15. dubna)

   ![Příklad zleva doprava.](./media/sql-database-long-term-retention/ltr-example.png)


 
Pokud byste chtěli upravit výše uvedenou zásadu a změní W = 0 (žádné týdenní zálohy), tempo sadu záložní kopie jako zobrazené v tabulce výše zvýrazněná data. Velikost úložiště potřebných k zajištění tyto zálohy by došlo ke snížení odpovídajícím způsobem. 

> [!NOTE]
1. Kopie zleva doprava jsou vytvořeny pomocí služby Azure storage tak proces kopírování nemá žádný vliv na výkon na existující databázi.
2. Zásady platí pro budoucí zálohy. Například Pokud zadaný WeekOfYear je v minulosti, kdy jsou nakonfigurované zásady, první zálohování LTR bude vytvořen příští rok. 
3. Chcete-li obnovit databázi z úložiště LTR, můžete vybrat konkrétní zálohy založené na její časové razítko.   Databázi můžete obnovit do jakékoli existující server ve stejném předplatném jako původní databáze. 
> 

## <a name="geo-replication-and-long-term-backup-retention"></a>Geografická replikace a dlouhodobé uchovávání záloh

Pokud používáte aktivní geografickou replikaci, převzetí služeb při selhání nebo skupiny jako řešení obchodní kontinuity podnikových procesů pro by měla připravit konečné převzetí služeb při selhání a nakonfigurovat stejné zásady LTR na geograficky sekundární databázi. To nezvýší vaše náklady na úložiště LTR jako zálohy se negenerují ze sekundární databáze. Pouze v případě, že sekundární stane primární zálohy se vytvoří. Tímto způsobem, bez přerušení generování zálohování LTR bude zaručit, při aktivaci převzetí služeb při selhání a primární přesune do sekundární oblasti. 

> [!NOTE]
Pokud původní primární databáze provádí zotavení z výpadku, který způsobí, že převzetí služeb při selhání, bude nový sekundární. Proto nebude pokračovat vytvoření zálohy a existující zásady LTR neprojeví, dokud nebude primární znovu. 
> 

## <a name="configure-long-term-backup-retention"></a>Konfigurace dlouhodobého uchovávání záloh

Zjistěte, jak nakonfigurovat dlouhodobé uchovávání pomocí webu Azure portal nebo pomocí Powershellu, najdete v článku [konfigurace dlouhodobého uchovávání záloh](sql-database-long-term-backup-retention-configure.md).

## <a name="next-steps"></a>Další postup

Protože zálohy databáze chránit data před náhodným poškozením nebo odstranění, jsou důležitou součástí žádné provozní kontinuitu a strategii zotavení po havárii. Další informace o jiných řešení obchodní kontinuity SQL Database najdete v tématu [přehled zajištění provozní kontinuity firmy](sql-database-business-continuity.md).
