---
title: Spravovat skupiny databází Azure SQL | Dokumentace Microsoftu
description: Provede procesem vytvoření a správa elastických úloh.
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
manager: craigg
ms.date: 07/16/2018
ms.openlocfilehash: 59235a20db5610ff3ace09d94b8726a9dd8cbfb8
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/04/2018
ms.locfileid: "52876770"
---
# <a name="create-and-manage-scaled-out-azure-sql-databases-using-elastic-jobs-preview"></a>Vytvářet a spravovat škálovaná databází Azure SQL Database pomocí elastických úloh (preview)


[!INCLUDE [elastic-database-jobs-deprecation](../../includes/sql-database-elastic-jobs-deprecate.md)]


**Úlohy elastic Database** zjednodušit správu skupiny databází pomocí provádí operace správy, například změny schématu, správu přihlašovacích údajů, aktualizace referenčních dat, shromažďování údajů o výkonu nebo telemetrie tenanta (zákazníka) kolekce. Úlohy elastic Database je aktuálně k dispozici prostřednictvím webu Azure portal a rutin prostředí PowerShell. Ale Azure portal povrchy funkčnost omezeno na spuštění napříč všemi databázemi ve omezená [elastického fondu](sql-database-elastic-pool.md). Pro přístup k dalším funkcím a spouštění skriptů napříč skupinou databází, včetně uživatelem definované kolekci nebo horizontální oddíl nastavit (vytvořené pomocí [Klientská knihovna Elastic Database](sql-database-elastic-scale-introduction.md)), najdete v článku [vytváření a Správa úlohy s použitím prostředí PowerShell](sql-database-elastic-jobs-powershell.md). Další informace o úlohách najdete v tématu [přehled úloh Elastických databází](sql-database-elastic-jobs-overview.md). 

## <a name="prerequisites"></a>Požadavky
* Předplatné Azure. Bezplatná zkušební verze, najdete v části [bezplatnou zkušební verzi](https://azure.microsoft.com/pricing/free-trial/).
* Elastický fond. Zobrazit [o elastických fondech](sql-database-elastic-pool.md).
* Instalace součásti služby úlohy elastické databáze. Zobrazit [instalace úlohy služby elastic database](sql-database-elastic-jobs-service-installation.md).

## <a name="creating-jobs"></a>Vytváření úloh
1. Použití [webu Azure portal](https://portal.azure.com), z existujícího fondu elastické databáze projektu, klikněte na tlačítko **vytvořit úlohu**.
2. Zadejte uživatelské jméno a heslo správce databáze (vytvořené při instalaci úlohy) pro databázi řízení úloh (metadata úložiště pro úlohy).
   
    ![Název úlohy zadejte nebo vložte v kódu a klikněte na tlačítko Spustit][1]
3. V **vytvoření úlohy** okno, zadejte název úlohy.
4. Zadejte uživatelské jméno a heslo pro připojení k cílovým databázím s dostatečnými oprávněními pro spuštění skriptu na úspěšné.
5. Vložte nebo zadejte skript T-SQL.
6. Klikněte na tlačítko **Uložit** a potom klikněte na tlačítko **spustit**.
   
    ![Vytvoření úlohy a spuštění][5]

## <a name="run-idempotent-jobs"></a>Spouštění úloh idempotentní
Při spuštění skriptu pro sadu databází, musí být jisti, že skript je idempotentní. Skript to znamená, musí být možné spouštět více než jednou, i v případě, že selhala před nekompletnímu stavu. Například pokud skript selže, úloha se automaticky zopakuje, dokud nebude úspěšný (v rámci omezení, jako opakování logiky nakonec přestane opakování). Způsob, jak to provést, je použít klauzuli "Pokud existuje" a odstranit všechny nalezené instanci před vytvořením nového objektu. Zde je uveden příklad:

    IF EXISTS (SELECT name FROM sys.indexes
            WHERE name = N'IX_ProductVendor_VendorID')
    DROP INDEX IX_ProductVendor_VendorID ON Purchasing.ProductVendor;
    GO
    CREATE INDEX IX_ProductVendor_VendorID
    ON Purchasing.ProductVendor (VendorID);

Můžete také použijte klauzuli "IF NOT EXISTS" před vytvořením nové instance:

    IF NOT EXISTS (SELECT name FROM sys.tables WHERE name = 'TestTable')
    BEGIN
     CREATE TABLE TestTable(
      TestTableId INT PRIMARY KEY IDENTITY,
      InsertionTime DATETIME2
     );
    END
    GO

    INSERT INTO TestTable(InsertionTime) VALUES (sysutcdatetime());
    GO

Tento skript pak aktualizuje tabulku jste vytvořili dříve.

    IF NOT EXISTS (SELECT columns.name FROM sys.columns INNER JOIN sys.tables on columns.object_id = tables.object_id WHERE tables.name = 'TestTable' AND columns.name = 'AdditionalInformation')
    BEGIN

    ALTER TABLE TestTable

    ADD AdditionalInformation NVARCHAR(400);
    END
    GO

    INSERT INTO TestTable(InsertionTime, AdditionalInformation) VALUES (sysutcdatetime(), 'test');
    GO


## <a name="checking-job-status"></a>Kontrola stavu úloh
Po zahájení úlohy, můžete zkontrolovat v jeho průběhu.

1. Na stránce elastického fondu, klikněte na tlačítko **spravovat úlohy**.
   
    ![Klikněte na tlačítko "Spravovat projekty"][2]
2. Klikněte na název (a) úlohy. **Stav** může být "Dokončeno" nebo "Se nezdařilo." S jeho datum a čas vytvoření a spuštění zobrazí podrobnosti úlohy (b). Seznam (c) pod prvek, který zobrazuje průběh skriptu na každou databázi ve fondu, poskytuje podrobnosti datum a čas.
   
    ![Kontroluje se dokončená úloha][3]

## <a name="checking-failed-jobs"></a>Kontroluje se neúspěšné úlohy
Pokud úloha selže, můžete najít protokol jeho spuštění. Klikněte na název z neúspěšné úlohy zobrazíte její podrobnosti.

![Zkontrolujte neúspěšné úlohy][4]

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-jobs-create-and-manage/screen-1.png
[2]: ./media/sql-database-elastic-jobs-create-and-manage/click-manage-jobs.png
[3]: ./media/sql-database-elastic-jobs-create-and-manage/running-jobs.png
[4]: ./media/sql-database-elastic-jobs-create-and-manage/failed.png
[5]: ./media/sql-database-elastic-jobs-create-and-manage/screen-2.png


