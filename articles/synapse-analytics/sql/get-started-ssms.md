---
title: 'SSMS: Připojení a dotaz Synapse SQL'
description: Pomocí sql server management studio (SSMS) pro připojení a dotaz Synapse SQL v Azure Synapse Analytics.
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick
ms.openlocfilehash: 704da86fd1d816dbf5d6cd9cf67dfee53fce2622
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81423737"
---
# <a name="connect-to-synapse-sql-with-sql-server-management-studio-ssms"></a>Připojení k Synapse SQL pomocí sql server management studio (SSMS)
> [!div class="op_single_selector"]
> * [Azure Data Studio](get-started-azure-data-studio.md)
> * [Power BI](get-started-power-bi-professional.md)
> * [Visual Studio](../sql-data-warehouse/sql-data-warehouse-query-visual-studio.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
> * [Sqlcmd](../sql/get-started-connect-sqlcmd.md)
> * [SSMS](get-started-ssms.md)
> 
> 

[Sql Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms) můžete použít k připojení a dotaz Synapse SQL v Azure Synapse Analytics prostřednictvím sql na vyžádání (preview) nebo sql fond prostředků. 

### <a name="supported-tools-for-sql-on-demand-preview"></a>Podporované nástroje pro SQL na vyžádání (náhled)

SSMS je částečně podporována počínaje verzí 18.5 s omezenými funkcemi, jako je připojení a dotazování. [Azure Data Studio](/sql/azure-data-studio/download-azure-data-studio) je plně podporované.

## <a name="prerequisites"></a>Požadavky

Než začnete, ujistěte se, že máte následující předpoklady:  

* [SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms). 
* Pro fond SQL potřebujete existující datový sklad. Pokud chcete ho vytvořit, [přečtěte si](../quickstart-create-sql-pool.md)informace o vytvoření fondu SQL . Pro SQL na vyžádání, jeden je již zřízena ve vašem pracovním prostoru v době vytvoření. 
* Plně kvalifikovaný název serveru SQL Server. Chcete-li najít, naleznete [v tématu Připojení k Synapse SQL](connect-overview.md).

## <a name="connect"></a>Připojit

### <a name="sql-pool"></a>Fond SQL

Chcete-li se připojit k synapse SQL pomocí fondu SQL, postupujte takto: 

1. Otevřete SQL Server Management Studio (SSMS). 
1. V dialogovém okně **Připojit k serveru** vyplňte pole a pak vyberte **Připojit**: 
  
    ![Připojení k serveru](../sql-data-warehouse/media/sql-data-warehouse-query-ssms/connect-object-explorer1.png)
   
   * **Název serveru**: Zadejte název **serveru,** který byl dříve identifikován.
   * **Ověřování**: Zvolte typ ověřování, například **ověřování serveru SQL Server** nebo integrované ověřování **služby Active Directory**.
   * **Uživatelské jméno** a **heslo**: Zadejte své uživatelské jméno a heslo, pokud bylo výše vybrané ověřování serveru SQL Server.

1. Rozbalte Svůj Azure SQL Server v **Průzkumníkovi objektů**. Můžete zobrazit databáze přidružené k serveru, například ukázkovou databázi AdventureWorksDW. Můžete rozbalit databázi a zobrazit tabulky:
   
    ![Prozkoumejte AdventureWorksDW.](../sql-data-warehouse/media/sql-data-warehouse-query-ssms/explore-tables.png)


### <a name="sql-on-demand-preview"></a>SQL na vyžádání (náhled)

Chcete-li se připojit k synapse SQL pomocí SQL na vyžádání, postupujte takto: 

1. Otevřete SQL Server Management Studio (SSMS).
1. V dialogovém okně **Připojit k serveru** vyplňte pole a pak vyberte **Připojit**: 
   
    ![Připojení k serveru](./media/get-started-ssms/connect-object-explorer1.png)
   
   * **Název serveru**: Zadejte název **serveru,** který byl dříve identifikován.
   * **Ověřování**: Zvolte typ ověřování, například **ověřování serveru SQL Server** nebo integrované ověřování **služby Active Directory**:
   * **Uživatelské jméno** a **heslo**: Zadejte své uživatelské jméno a heslo, pokud bylo výše vybrané ověřování serveru SQL Server.
   * Klikněte na **Připojit**.

4. Pokud chcete SQL server Azure prozkoumat, rozbalte ho. Můžete se podívat, které databáze jsou k tomuto serveru přidružené. Rozbalte *ukázku* a zobcíte obsah ukázkové databáze.
   
    ![Prozkoumejte AdventureWorksDW.](./media/get-started-ssms/explore-tables.png)


## <a name="run-a-sample-query"></a>Spuštění ukázkového dotazu

### <a name="sql-pool"></a>Fond SQL

Nyní, když bylo navázáno připojení k databázi, můžete dotazovat data.

1. Pravým tlačítkem myši klikněte na databázi v Průzkumníku objektů systému SQL Server.
2. Vyberte **Nový dotaz**. Otevře se nové okno dotazu.
   
    ![Nový dotaz](../sql-data-warehouse/media/sql-data-warehouse-query-ssms/new-query.png)
3. Zkopírujte tento dotaz T-SQL do okna dotazu:
   
    ```sql
    SELECT COUNT(*) FROM dbo.FactInternetSales;
    ```
4. Spusťte dotaz. Chcete-li to `Execute` provést, klepněte `F5`nebo použijte následující zástupce: .
   
    ![Spuštění dotazu](../sql-data-warehouse/media/sql-data-warehouse-query-ssms/execute-query.png)
5. Podívejte se na výsledky dotazu. V tomto příkladě má tabulka FactInternetSales 60 398 řádků.
   
    ![Výsledky dotazu](../sql-data-warehouse/media/sql-data-warehouse-query-ssms/results.png)

### <a name="sql-on-demand"></a>SQL na vyžádání

Nyní, když jste navázali připojení k databázi, můžete dotazovat data.

1. Pravým tlačítkem myši klikněte na databázi v Průzkumníku objektů systému SQL Server.
2. Vyberte **Nový dotaz**. Otevře se nové okno dotazu.
   
    ![Nový dotaz](./media/get-started-ssms/new-query.png)
3. Do okna dotazu zkopírujte následující dotaz T-SQL:
   
    ```sql
    SELECT COUNT(*) FROM demo.dbo.usPopulationView
    ```
4. Spusťte dotaz. Chcete-li to `Execute` provést, klepněte `F5`nebo použijte následující zástupce: .
   
    ![Spuštění dotazu](./media/get-started-ssms/execute-query.png)
5. Podívejte se na výsledky dotazu. V tomto příkladu má zobrazení usPopulationView 3664512 řádků.
   
    ![Výsledky dotazu](./media/get-started-ssms/results.png)

## <a name="next-steps"></a>Další kroky
Teď, když se můžete připojit a dotazovat, zkuste [data vizualizovat pomocí Power BI](get-started-power-bi-professional.md).

Pokud chcete nakonfigurovat prostředí pro ověřování služby Azure Active Directory, přečtěte si informace [o ověření na synapse SQL](../sql-data-warehouse/sql-data-warehouse-authentication.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).

