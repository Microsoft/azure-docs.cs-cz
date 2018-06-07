---
title: Uložte zálohy databáze SQL Azure pro až 10 let | Microsoft Docs
description: Zjistěte, jak Azure SQL Database podporuje ukládání záloh úplné databáze až 10 let.
services: sql-database
author: anosov1960
manager: craigg
ms.service: sql-database
ms.custom: business continuity
ms.topic: conceptual
ms.date: 05/17/2018
ms.author: sashan
ms.reviewer: carlrab
ms.openlocfilehash: b2f3c454ba84c7b892096cc42dcbe2706ab6159f
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34648288"
---
# <a name="store-azure-sql-database-backups-for-up-to-10-years"></a>Uložte zálohy databáze SQL Azure až 10 let.

Mnoho aplikací mít regulačních, dodržování předpisů nebo jiné obchodní účely, které vyžadují, abyste uchování záloh databáze nad rámec 7-35 dní, poskytuje Azure SQL Database [automatické zálohování](sql-database-automated-backups.md). Pomocí funkce dlouhodobé uchovávání dat (zleva doprava) můžete uložit zadaný SQL databáze úplné zálohy v [RA-GRS](../storage/common/storage-redundancy-grs.md#read-access-geo-redundant-storage) úložiště objektů blob až 10 let. Pak můžete obnovit jakékoli zálohy jako novou databázi.

> [!IMPORTANT]
> Dlouhodobé uchovávání je aktuálně ve verzi preview. Existující zálohy uložené v úložišti službu Azure Recovery Services jako součást předchozí verze preview tato funkce se migrují do úložiště SQL Azure.<!-- and available in the following regions: Australia East, Australia Southeast, Brazil South, Central US, East Asia, East US, East US 2, India Central, India South, Japan East, Japan West, North Central US, North Europe, South Central US, Southeast Asia, West Europe, and West US.-->
>

## <a name="how-sql-database-long-term-retention-works"></a>Jak funguje dlouhodobé uchovávání databáze SQL

Dlouhodobé uchovávání záloh využívá [automatického zálohování SQL Database](sql-database-automated-backups.md) vytvořit trvá čas bodu obnovení (Možnosti PITR). Můžete nakonfigurovat zásady dlouhodobé uchovávání informací pro každou databázi SQL a zadejte, jak často je nutné zkopírovat zálohování do dlouhodobého úložiště. Chcete-li povolit tuto flexibilitu můžete definovat zásady pomocí kombinace čtyř parametrů: týdenního uchovávání záloh (W) měsíční uchovávání záloh (M), roční uchovávání záloh (Y) a týden roku (WeekOfYear). Pokud zadáte W, jednu zálohu každý týden bude zkopírován do dlouhodobého úložiště. Pokud zadáte M, jednu zálohu během první týden v měsíci se zkopírují do dlouhodobého úložiště. Pokud zadáte Y, jednu zálohu během týdne určeného WeekOfYear se zkopírují do dlouhodobého úložiště. Každá záloha se zachová do dlouhodobého úložiště po dobu určenou tyto parametry. 

Příklady:

-  W = 0, M = 0, Y = 5, WeekOfYear = 3

   3. úplné zálohování každý rok se zachová 5 let.

- W = 0, M = 3, Y = 0

   První úplné zálohování každý měsíc budou zachovány pro 3 měsíce.

- W = 12, M = 0, Y = 0

   Každý týdenní úplné zálohování budou zachovány pro 12 týdnů.

- W = 6, M = 12, Y = 10, WeekOfYear = 16

   Každý týdenní úplné zálohování budou zachovány pro 6 týdnů. S výjimkou první úplné zálohování každý měsíc, které budou zachovány po dobu 12 měsíců. S výjimkou úplné zálohy s 16 týden roku, které budou zachovány pro 10 let. 

Následující tabulka znázorňuje cadence a vypršení platnosti dlouhodobé zálohy pro tyto zásady:

W = 12 týdnů (84 dny), M = 12 měsíců (365 dní), Y = 10 let (3650 dny), WeekOfYear = 15 (týden po duben 15)

   ![Příklad zleva doprava.](./media/sql-database-long-term-retention/ltr-example.png)


 
Pokud byste chtěli upravit výše zásady a změní sadu zit = 0 (žádné týdenní zálohy), cadence záložní kopie jako zobrazit podle zvýrazněná kalendářních dat v tabulce. Množství úložiště, které jsou nutné k udržování tyto zálohy by snížit odpovídajícím způsobem. 

> [!NOTE]
1. Služba úložiště Azure vytváří kopie zleva doprava, proces kopírování nemá žádný vliv výkon na existující databázi.
2. Zásady platí pro budoucí zálohy. Například Pokud zadaný WeekOfYear je v minulosti. když zásad je nakonfigurovaná, první zálohování zleva doprava se vytvoří následujícího roku. 
3. Chcete-li obnovit databázi z úložiště zleva doprava, můžete vybrat konkrétní zálohování podle jeho časové razítko.   Možné obnovit databázi do jakékoli existující server v rámci stejného předplatného jako původní databázi. 
> 

## <a name="configure-long-term-backup-retention"></a>Konfigurace dlouhodobého uchovávání záloh

Další postup konfigurace dlouhodobé uchovávání pomocí portálu Azure nebo pomocí prostředí PowerShell najdete v tématu [konfigurace dlouhodobé uchovávání záloh](sql-database-long-term-backup-retention-configure.md).

## <a name="next-steps"></a>Další postup

Protože zálohy databáze chránit data před náhodným poškození nebo odstranění, jsou nedílnou součást vámi vyžádaných žádné kontinuity podnikových procesů a strategie zotavení po havárii. Další informace o jiných řešení kontinuity podnikových procesů databáze SQL najdete v tématu [obchodní kontinuity přehled](sql-database-business-continuity.md).
