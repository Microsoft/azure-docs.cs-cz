---
title: Připojení k synapse SQL pomocí SQL Server Management Studio (SSMS)
description: Pomocí SQL Server Management Studio (SSMS) se můžete připojit a dotazovat synapse SQL ve službě Azure synapse Analytics.
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: stefanazaric
ms.reviewer: jrasnick
ms.openlocfilehash: 4239b5400e919621dfff001ca7e6cb9de1626658
ms.sourcegitcommit: c157b830430f9937a7fa7a3a6666dcb66caa338b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/17/2020
ms.locfileid: "94686204"
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

[SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms) můžete použít k připojení a dotazování synapse SQL ve službě Azure synapse Analytics prostřednictvím neserverového fondu SQL (Preview) nebo vyhrazených prostředků fondu SQL. 

### <a name="supported-tools-for-serverless-sql-pool-preview"></a>Podporované nástroje pro fond SQL bez serveru (Preview)

[Azure Data Studio](/sql/azure-data-studio/download-azure-data-studio) se plně podporuje od verze 1.18.0. SSMS je částečně podporovaná od verze 18,5, můžete ji použít jenom pro připojení a dotazování.

> [!NOTE]
> Pokud má přihlášení AAD otevřené připojení po dobu delší než 1 hodinu, dotaz, který se spoléhá na AAD, selže. To zahrnuje dotazování na úložiště pomocí předávacího a příkazového příkazu AAD, které komunikují s AAD (třeba vytvoření externího poskytovatele). To má vliv na všechny nástroje, které udržují připojení otevřené, jako v editoru dotazů v SSMS a REKLAMách. Nástroje, které otevřou nové připojení k provedení dotazu, nejsou ovlivněné, například synapse Studio.
> Pokud chcete tento problém zmírnit, můžete restartovat SSMS nebo připojení a odpojení v REKLAMách. .
## <a name="prerequisites"></a>Požadavky

Než začnete, ujistěte se, že máte následující požadavky:  

* [SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms). 
* Pro vyhrazený fond SQL potřebujete existující datový sklad. Pokud ho chcete vytvořit, přečtěte si téma [vytvoření vyhrazeného fondu SQL](../quickstart-create-sql-pool-portal.md). Pro fond SQL bez serveru je už ve vašem pracovním prostoru v době vytváření zřízen vestavěný název. 
* Plně kvalifikovaný název SQL Server. Tento název najdete v tématu [připojení k synapse SQL](connect-overview.md).

## <a name="connect"></a>Připojit

### <a name="dedicated-sql-pool"></a>Vyhrazený fond SQL

Pokud se chcete připojit k SQL synapse pomocí vyhrazeného fondu SQL, postupujte podle těchto kroků: 

1. Otevřete SQL Server Management Studio (SSMS). 
1. V dialogovém okně **připojit k serveru** vyplňte pole a pak vyberte **připojit**: 
  
    ![Připojit k serveru 1](../sql-data-warehouse/media/sql-data-warehouse-query-ssms/connect-object-explorer1.png)
   
   * **Název serveru**: zadejte **název serveru** , který jste předtím identifikovali.
   * **Ověřování**: Vyberte typ ověřování, například **SQL Server ověřování** nebo **integrované ověřování služby Active Directory**.
   * **Uživatelské jméno** a **heslo**: pokud jste výše vybrali SQL Server ověřování, zadejte své uživatelské jméno a heslo.

1. Rozšiřte SQL Server Azure v **Průzkumník objektů**. Můžete zobrazit databáze přidružené k serveru, jako je například ukázková databáze AdventureWorksDW. Chcete-li zobrazit tabulky, můžete rozšířit databázi:
   
    ![Prozkoumat AdventureWorksDW 1](../sql-data-warehouse/media/sql-data-warehouse-query-ssms/explore-tables.png)


### <a name="serverless-sql-pool-preview"></a>Neserverový fond SQL (Preview)

Pokud se chcete připojit k synapse SQL pomocí neserveru SQL fondu, postupujte takto: 

1. Otevřete SQL Server Management Studio (SSMS).
1. V dialogovém okně **připojit k serveru** vyplňte pole a pak vyberte **připojit**: 
   
    ![Připojit k serveru 2](./media/get-started-ssms/connect-object-explorer1.png)
   
   * **Název serveru**: zadejte **název serveru** , který jste předtím identifikovali.
   * **Ověřování**: Vyberte typ ověřování, například **SQL Server ověřování** nebo **integrované ověřování služby Active Directory**:
   * **Uživatelské jméno** a **heslo**: pokud jste výše vybrali SQL Server ověřování, zadejte své uživatelské jméno a heslo.
   * Vyberte **Connect** (Připojit).

4. Pokud chcete SQL server Azure prozkoumat, rozbalte ho. Můžete se podívat, které databáze jsou k tomuto serveru přidružené. Rozbalením *ukázky* zobrazíte obsah ve vzorové databázi.
   
    ![Prozkoumat AdventureWorksDW 2](./media/get-started-ssms/explore-tables.png)


## <a name="run-a-sample-query"></a>Spuštění ukázkového dotazu

### <a name="dedicated-sql-pool"></a>Vyhrazený fond SQL

Teď, když je navázáno připojení k databázi, můžete zadávat dotazy na data.

1. Pravým tlačítkem myši klikněte na databázi v Průzkumníku objektů systému SQL Server.
2. Vyberte **Nový dotaz**. Otevře se nové okno dotazu.
   
    ![Nový dotaz 1](../sql-data-warehouse/media/sql-data-warehouse-query-ssms/new-query.png)
3. Zkopírujte následující dotaz T-SQL do okna dotazu:
   
    ```sql
    SELECT COUNT(*) FROM dbo.FactInternetSales;
    ```
4. Spusťte dotaz výběrem `Execute` nebo použitím následujícího zástupce: `F5` .
   
    ![Spustit dotaz 1](../sql-data-warehouse/media/sql-data-warehouse-query-ssms/execute-query.png)
5. Podívejte se na výsledky dotazu. V následujícím příkladu má tabulka FactInternetSales 60398 řádků.
   
    ![Výsledky dotazu 1](../sql-data-warehouse/media/sql-data-warehouse-query-ssms/results.png)

### <a name="serverless-sql-pool"></a>Bezserverový fond SQL

Teď, když jste navázali připojení k databázi, můžete zadávat dotazy na data.

1. Pravým tlačítkem myši klikněte na databázi v Průzkumníku objektů systému SQL Server.
2. Vyberte **Nový dotaz**. Otevře se nové okno dotazu.
   
    ![Nový dotaz 2](./media/get-started-ssms/new-query.png)
3. Zkopírujte následující dotaz T-SQL do okna dotazu:
   
    ```sql
    SELECT COUNT(*) FROM demo.dbo.usPopulationView
    ```
4. Spusťte dotaz výběrem `Execute` nebo použitím následujícího zástupce: `F5` .
   
    ![Spustit dotaz 2](./media/get-started-ssms/execute-query.png)
5. Podívejte se na výsledky dotazu. V tomto příkladu má zobrazení usPopulationView řádky 3664512.
   
    ![Výsledky dotazu 2](./media/get-started-ssms/results.png)

## <a name="next-steps"></a>Další kroky
Teď, když se můžete připojit a dotazovat, zkuste [vizualizovat data pomocí Power BI](get-started-power-bi-professional.md).

Pokud chcete nakonfigurovat prostředí pro ověřování Azure Active Directory, přečtěte si téma [ověření pro synapse SQL](../sql-data-warehouse/sql-data-warehouse-authentication.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).

