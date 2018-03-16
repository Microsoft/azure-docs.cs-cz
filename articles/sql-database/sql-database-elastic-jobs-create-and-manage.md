---
title: "Spravovat skupiny databází Azure SQL | Microsoft Docs"
description: "Provede procesem vytváření a správa elastické úlohy."
services: sql-database
manager: craigg
author: stevestein
ms.service: sql-database
ms.custom: scale out apps
ms.topic: article
ms.date: 10/24/2016
ms.author: sstein
ms.openlocfilehash: 77204e167d298ad77b2786b5d0a3dab1b557ccfc
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/16/2018
---
# <a name="create-and-manage-scaled-out-azure-sql-databases-using-elastic-jobs-preview"></a>Vytvoření a správa škálovaný databází Azure SQL pomocí elastické úlohy (preview)


**Elastické databáze úlohy** zjednodušení správy skupin databáze spuštěním operace správy, například změny schématu, Správa přihlašovacích údajů, aktualizace dat odkaz, shromažďování dat výkonu nebo telemetrie klienta (zákazníka) kolekce. Úlohy elastické databáze je aktuálně k dispozici prostřednictvím portálu Azure a rutin prostředí PowerShell. Však Azure portálu povrchy snížit funkce omezené na provádění mezi všechny databáze [elastického fondu (preview)](sql-database-elastic-pool.md). Pro přístup k další funkce a spouštění skriptů napříč skupinou databází, včetně vlastní definovanou kolekci nebo horizontálního oddílu nastavit (vytvořený [klientské knihovny pro elastické databáze](sql-database-elastic-scale-introduction.md)), najdete v části [vytváření a Správa úlohy pomocí prostředí PowerShell](sql-database-elastic-jobs-powershell.md). Další informace o úlohách najdete v tématu [přehled úlohy elastické databáze](sql-database-elastic-jobs-overview.md). 

## <a name="prerequisites"></a>Požadavky
* Předplatné Azure. Bezplatná zkušební verze, najdete v části [bezplatnou zkušební verzi](https://azure.microsoft.com/pricing/free-trial/).
* Elastický fond. V tématu [o elastické fondy](sql-database-elastic-pool.md).
* Instalace součásti služby úlohy elastické databáze. V tématu [nainstalovat službu úlohy elastické databáze](sql-database-elastic-jobs-service-installation.md).

## <a name="creating-jobs"></a>Vytváření úloh
1. Pomocí [portál Azure](https://portal.azure.com), z existující fond elastické databáze úlohy, klikněte na tlačítko **vytvořit úlohu**.
2. Zadejte uživatelské jméno a heslo správce databáze (vytvořeny při instalaci úloh) pro databázi řízení úloh (metadata úložiště pro úlohy).
   
    ![Název úlohy, zadejte nebo vložte kód a klikněte na tlačítko Spustit][1]
3. V **vytvoření úlohy** okno, zadejte název pro úlohu.
4. Zadejte uživatelské jméno a heslo pro připojení k cílovým databázím s dostatečnými oprávněními pro spuštění skriptu proběhla úspěšně.
5. Vložení nebo typu ve skriptu T-SQL.
6. Klikněte na tlačítko **Uložit** a pak klikněte na **spustit**.
   
    ![Vytvoření úlohy a spuštění][5]

## <a name="run-idempotent-jobs"></a>Spuštění úloh idempotent
Při spuštění skriptu pro sadu databází, musí být jisti, že skript idempotent. Skript tedy musí být možné spustit vícekrát, i v případě, že se nezdařil před nekompletnímu stavu. Například pokud skript selže, úloha bude automaticky opakovat, dokud neproběhne úspěšně (omezení, jako opakovat logiku nakonec přestanou opakování). Způsob k tomu je použít klauzuli "Pokud existuje" a odstranit všechny nalezen instanci před vytvořením nového objektu. Zde je uveden příklad:

    IF EXISTS (SELECT name FROM sys.indexes
            WHERE name = N'IX_ProductVendor_VendorID')
    DROP INDEX IX_ProductVendor_VendorID ON Purchasing.ProductVendor;
    GO
    CREATE INDEX IX_ProductVendor_VendorID
    ON Purchasing.ProductVendor (VendorID);

Můžete taky použijte klauzuli "Pokud není existuje" před vytvořením nové instance:

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

Tento skript potom aktualizuje v tabulce, které jste vytvořili dříve.

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
   
    ![Klikněte na tlačítko "Spravovat úlohy"][2]
2. Klikněte na název (a) úlohy. **Stav** může být "Dokončeno" nebo "Se nezdařilo." S jeho datum a čas vytvoření a spuštění zobrazují podrobnosti úlohy (b). V seznamu (c) níže se, že zobrazí průběh skriptu na každou databázi ve fondu, poskytne jeho podrobnosti datum a čas.
   
    ![Kontrola dokončení úlohy][3]

## <a name="checking-failed-jobs"></a>Kontrola neúspěšné úlohy
Pokud úloha selže, můžete najít protokolu jeho spuštění. Klikněte na název zobrazíte její podrobnosti o neúspěšné úloze.

![Podívejte se neúspěšnou úlohu][4]

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-jobs-create-and-manage/screen-1.png
[2]: ./media/sql-database-elastic-jobs-create-and-manage/click-manage-jobs.png
[3]: ./media/sql-database-elastic-jobs-create-and-manage/running-jobs.png
[4]: ./media/sql-database-elastic-jobs-create-and-manage/failed.png
[5]: ./media/sql-database-elastic-jobs-create-and-manage/screen-2.png


