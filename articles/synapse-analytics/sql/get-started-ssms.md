---
title: 'SSMS: Connect a Query synapse SQL'
description: Pomocí SQL Server Management Studio (SSMS) se můžete připojit a dotazovat synapse SQL ve službě Azure synapse Analytics.
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick
ms.openlocfilehash: 704da86fd1d816dbf5d6cd9cf67dfee53fce2622
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/29/2020
ms.locfileid: "81423737"
---
# <a name="connect-to-synapse-sql-with-sql-server-management-studio-ssms"></a>Připojení k synapse SQL pomocí SQL Server Management Studio (SSMS)
> [!div class="op_single_selector"]
> * [Azure Data Studio](get-started-azure-data-studio.md)
> * [Power BI](get-started-power-bi-professional.md)
> * [Visual Studio](../sql-data-warehouse/sql-data-warehouse-query-visual-studio.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
> * [Sqlcmd](../sql/get-started-connect-sqlcmd.md)
> * [SSMS](get-started-ssms.md)
> 
> 

[SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms) můžete použít k připojení a dotazování synapse SQL ve službě Azure synapse Analytics prostřednictvím SQL na vyžádání (Preview) nebo prostředků fondu SQL. 

### <a name="supported-tools-for-sql-on-demand-preview"></a>Podporované nástroje pro SQL na vyžádání (Preview)

SSMS se částečně podporuje od verze 18,5 s omezenými funkcemi, jako je připojení a dotazování. [Azure Data Studio](/sql/azure-data-studio/download-azure-data-studio) je plně podporovaný.

## <a name="prerequisites"></a>Požadavky

Než začnete, ujistěte se, že máte následující požadavky:  

* [SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms). 
* Pro fond SQL potřebujete existující datový sklad. Pokud ho chcete vytvořit, přečtěte si téma [Vytvoření fondu SQL](../quickstart-create-sql-pool.md). V případě SQL na vyžádání je ve vašem pracovním prostoru v době vytváření již jedna zřízená. 
* Plně kvalifikovaný název SQL Server. Najdete je v tématu [připojení k synapse SQL](connect-overview.md).

## <a name="connect"></a>Připojit

### <a name="sql-pool"></a>Fond SQL

Pokud se chcete připojit k synapse SQL pomocí fondu SQL, postupujte podle těchto kroků: 

1. Otevřete SQL Server Management Studio (SSMS). 
1. V dialogovém okně **připojit k serveru** vyplňte pole a pak vyberte **připojit**: 
  
    ![Připojení k serveru](../sql-data-warehouse/media/sql-data-warehouse-query-ssms/connect-object-explorer1.png)
   
   * **Název serveru**: zadejte **název serveru** , který jste předtím identifikovali.
   * **Ověřování**: Vyberte typ ověřování, například **SQL Server ověřování** nebo **integrované ověřování služby Active Directory**.
   * **Uživatelské jméno** a **heslo**: pokud jste výše vybrali SQL Server ověřování, zadejte své uživatelské jméno a heslo.

1. Rozšiřte SQL Server Azure v **Průzkumník objektů**. Můžete zobrazit databáze přidružené k serveru, jako je například ukázková databáze AdventureWorksDW. Chcete-li zobrazit tabulky, můžete rozšířit databázi:
   
    ![Prozkoumejte AdventureWorksDW.](../sql-data-warehouse/media/sql-data-warehouse-query-ssms/explore-tables.png)


### <a name="sql-on-demand-preview"></a>SQL na vyžádání (Preview)

Pokud se chcete připojit k synapse SQL pomocí SQL na vyžádání, postupujte následovně: 

1. Otevřete SQL Server Management Studio (SSMS).
1. V dialogovém okně **připojit k serveru** vyplňte pole a pak vyberte **připojit**: 
   
    ![Připojení k serveru](./media/get-started-ssms/connect-object-explorer1.png)
   
   * **Název serveru**: zadejte **název serveru** , který jste předtím identifikovali.
   * **Ověřování**: Vyberte typ ověřování, například **SQL Server ověřování** nebo **integrované ověřování služby Active Directory**:
   * **Uživatelské jméno** a **heslo**: pokud jste výše vybrali SQL Server ověřování, zadejte své uživatelské jméno a heslo.
   * Klikněte na **Připojit**.

4. Pokud chcete SQL server Azure prozkoumat, rozbalte ho. Můžete se podívat, které databáze jsou k tomuto serveru přidružené. Rozbalením *ukázky* zobrazíte obsah ve vzorové databázi.
   
    ![Prozkoumejte AdventureWorksDW.](./media/get-started-ssms/explore-tables.png)


## <a name="run-a-sample-query"></a>Spuštění ukázkového dotazu

### <a name="sql-pool"></a>Fond SQL

Teď, když je navázáno připojení k databázi, můžete zadávat dotazy na data.

1. Pravým tlačítkem myši klikněte na databázi v Průzkumníku objektů systému SQL Server.
2. Vyberte **Nový dotaz**. Otevře se nové okno dotazu.
   
    ![Nový dotaz](../sql-data-warehouse/media/sql-data-warehouse-query-ssms/new-query.png)
3. Zkopírujte tento dotaz T-SQL do okna dotazu:
   
    ```sql
    SELECT COUNT(*) FROM dbo.FactInternetSales;
    ```
4. Spusťte dotaz. Pokud to chcete provést, `Execute` klikněte nebo použijte následující klávesovou `F5`zkratku:.
   
    ![Spuštění dotazu](../sql-data-warehouse/media/sql-data-warehouse-query-ssms/execute-query.png)
5. Podívejte se na výsledky dotazu. V tomto příkladě má tabulka FactInternetSales 60 398 řádků.
   
    ![Výsledky dotazu](../sql-data-warehouse/media/sql-data-warehouse-query-ssms/results.png)

### <a name="sql-on-demand"></a>SQL na vyžádání

Teď, když jste navázali připojení k databázi, můžete zadávat dotazy na data.

1. Pravým tlačítkem myši klikněte na databázi v Průzkumníku objektů systému SQL Server.
2. Vyberte **Nový dotaz**. Otevře se nové okno dotazu.
   
    ![Nový dotaz](./media/get-started-ssms/new-query.png)
3. Zkopírujte následující dotaz T-SQL do okna dotazu:
   
    ```sql
    SELECT COUNT(*) FROM demo.dbo.usPopulationView
    ```
4. Spusťte dotaz. Pokud to chcete provést, `Execute` klikněte nebo použijte následující klávesovou `F5`zkratku:.
   
    ![Spuštění dotazu](./media/get-started-ssms/execute-query.png)
5. Podívejte se na výsledky dotazu. V tomto příkladu má zobrazení usPopulationView řádky 3664512.
   
    ![Výsledky dotazu](./media/get-started-ssms/results.png)

## <a name="next-steps"></a>Další kroky
Teď, když se můžete připojit a dotazovat, zkuste [vizualizovat data pomocí Power BI](get-started-power-bi-professional.md).

Pokud chcete nakonfigurovat prostředí pro ověřování Azure Active Directory, přečtěte si téma [ověření pro synapse SQL](../sql-data-warehouse/sql-data-warehouse-authentication.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).

