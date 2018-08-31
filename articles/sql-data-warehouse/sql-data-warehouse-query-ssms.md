---
title: Připojení k Azure SQL Data Warehouse – SSMS | Dokumentace Microsoftu
description: Připojení a dotazování Azure SQL Data Warehouse pomocí SQL Server Management Studio (SSMS).
services: sql-data-warehouse
author: kavithaj
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: consume
ms.date: 04/17/2018
ms.author: kavithaj
ms.reviewer: igorstan
ms.openlocfilehash: 6079c3064699da38fad20468517eb97d6ab107f8
ms.sourcegitcommit: 1fb353cfca800e741678b200f23af6f31bd03e87
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/30/2018
ms.locfileid: "43307196"
---
# <a name="connect-to-sql-data-warehouse-with-sql-server-management-studio-ssms"></a>Připojení k SQL Data Warehouse pomocí SQL Server Management Studio (SSMS)
> [!div class="op_single_selector"]
> * [Power BI](sql-data-warehouse-get-started-visualize-with-power-bi.md)
> * [Azure Machine Learning](sql-data-warehouse-get-started-analyze-with-azure-machine-learning.md)
> * [Visual Studio](sql-data-warehouse-query-visual-studio.md)
> * [sqlcmd](sql-data-warehouse-get-started-connect-sqlcmd.md) 
> * [SSMS](sql-data-warehouse-query-ssms.md)
> 
> 

Připojení a dotazování Azure SQL Data Warehouse pomocí SQL Server Management Studio (SSMS). 

## <a name="prerequisites"></a>Požadavky
Chcete-li použít tento kurz, potřebujete:

* Existující SQL Data Warehouse. Pokud chcete jeden vytvořit, podívejte se na téma [Vytvoření SQL Data Warehouse][Create a SQL Data Warehouse].
* SQL Server Management Studio (SSMS) nainstalovaný. [Instalace aplikace SSMS] [ Install SSMS] zdarma, pokud ještě nemáte ho.
* Plně kvalifikovaný název serveru SQL. Ten zjistíte v části [Připojení k SQL Data Warehouse][Connect to SQL Data Warehouse].

## <a name="1-connect-to-your-sql-data-warehouse"></a>1. Připojení k vaší službě SQL Data Warehouse
1. Otevřete aplikaci SSMS.
2. Otevřete Průzkumník objektů. Chcete-li to provést, vyberte **souboru** > **připojit Průzkumník objektů**.
   
    ![Průzkumník objektů systému SQL Server][1]
3. Vyplňte pole v okně pro připojení k serveru.
   
    ![Připojení k serveru][2]
   
   * **Název serveru**: Zadejte **název serveru**, který jste si zjistili.
   * **Ověřování**: Vyberte **Ověřování serveru SQL Server** nebo **Integrované ověřování Active Directory**.
   * **Uživatelské jméno** a **Heslo**: Pokud jste výše vybrali možnost Ověřování serveru SQL Server, zadejte uživatelské jméno a heslo.
   * Klikněte na **Připojit**.
4. Pokud chcete SQL server Azure prozkoumat, rozbalte ho. Můžete se podívat, které databáze jsou k tomuto serveru přidružené. Rozbalte položku AdventureWorksDW a podívejte se na tabulky v ukázkové databázi.
   
    ![Prozkoumejte AdventureWorksDW.][3]

## <a name="2-run-a-sample-query"></a>2. Spuštění ukázkového dotazu
Teď, když jste si vytvořili připojení k databázi, můžete napsat dotaz.

1. Pravým tlačítkem myši klikněte na databázi v Průzkumníku objektů systému SQL Server.
2. Vyberte **Nový dotaz**. Otevře se nové okno dotazu.
   
    ![Nový dotaz][4]
3. Zkopírujte tento dotaz TSQL do okna dotazu:
   
    ```sql
    SELECT COUNT(*) FROM dbo.FactInternetSales;
    ```
4. Spusťte dotaz. Chcete-li to provést, klikněte na tlačítko `Execute` nebo použijte následující klávesovou zkratku: `F5`.
   
    ![Spuštění dotazu][5]
5. Podívejte se na výsledky dotazu. V tomto příkladě má tabulka FactInternetSales 60 398 řádků.
   
    ![Výsledky dotazu][6]

## <a name="next-steps"></a>Další postup
Teď, když se můžete připojit a můžete zadávat dotazy, můžete si vyzkoušet [vizualizaci dat pomocí PowerBI][visualizing the data with PowerBI].

Informace o tom, jak nakonfigurovat prostředí pro ověřování služby Azure Active Directory, najdete v části [Ověřování pro SQL Data Warehouse][Authenticate to SQL Data Warehouse].

<!--Arcticles-->
[Connect to SQL Data Warehouse]: sql-data-warehouse-connect-overview.md
[Create a SQL Data Warehouse]: sql-data-warehouse-get-started-provision.md
[Authenticate to SQL Data Warehouse]: sql-data-warehouse-authentication.md
[visualizing the data with PowerBI]: sql-data-warehouse-get-started-visualize-with-power-bi.md 

<!--Other-->
[Azure portal]: https://portal.azure.com
[Install SSMS]: https://msdn.microsoft.com/library/hh213248.aspx


<!--Image references-->

[1]: media/sql-data-warehouse-query-ssms/connect-object-explorer.png
[2]: media/sql-data-warehouse-query-ssms/connect-object-explorer1.png
[3]: media/sql-data-warehouse-query-ssms/explore-tables.png
[4]: media/sql-data-warehouse-query-ssms/new-query.png
[5]: media/sql-data-warehouse-query-ssms/execute-query.png
[6]: media/sql-data-warehouse-query-ssms/results.png
