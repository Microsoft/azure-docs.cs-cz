---
title: Spojte se s VSTS
description: Dotaz Azure Synapse Analytics s Visual Studio.
services: synapse-analytics
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 08/15/2019
ms.author: kevin
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 174ee07e389e598fed6ed8487e60303fbce81f77
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81416036"
---
# <a name="connect-to-azure-synapse-analytics-with-visual-studio-and-ssdt"></a>Připojení k Azure Synapse Analytics pomocí Visual Studia a SSDT
> [!div class="op_single_selector"]
> * [Power BI](sql-data-warehouse-get-started-visualize-with-power-bi.md)
> * [Azure Machine Learning](sql-data-warehouse-get-started-analyze-with-azure-machine-learning.md)
> * [Visual Studio](sql-data-warehouse-query-visual-studio.md)
> * [Sqlcmd](../sql/get-started-connect-sqlcmd.md) 
> * [SSMS](sql-data-warehouse-query-ssms.md)
> 
> 

Visual Studio použijte k dotazování fondu SQL v rámci Azure Synapse během několika minut. Tato metoda používá rozšíření SQL Server Data Tools (SSDT) v Sadě Visual Studio 2019. 

## <a name="prerequisites"></a>Požadavky
Chcete-li použít tento kurz, potřebujete:

* Existující fond SQL. Pokud chcete ho vytvořit, [přečtěte si](create-data-warehouse-portal.md)informace o vytvoření fondu SQL .
* SSDT pro Visual Studio. Pokud máte Visual Studio, pravděpodobně již máte SSDT pro Visual Studio. Pokyny k instalaci a možnosti najdete v tématu věnovaném [instalaci sady Visual Studio a rozšíření SSDT](sql-data-warehouse-install-visual-studio.md).
* Plně kvalifikovaný název serveru SQL. Chcete-li tyto informace najít, [přečtěte si](../sql/connect-overview.md)téma Připojení k fondu SQL .

## <a name="1-connect-to-your-sql-pool"></a>1. Připojení k fondu SQL
1. Otevřete Visual Studio 2019.
2. Sem otevřete Průzkumníka objektů serveru SQL Server výběrem **možnosti Zobrazit** > **Průzkumník objektů serveru SQL Server**.
   
    ![Průzkumník objektů systému SQL Server](./media/sql-data-warehouse-query-visual-studio/open-ssdt.png)
3. Klikněte na ikonu **Přidat SQL Server**.
   
    ![Přidat SQL Server](./media/sql-data-warehouse-query-visual-studio/add-server.png)
4. Vyplňte pole v okně pro připojení k serveru.
   
    ![Připojení k serveru](./media/sql-data-warehouse-query-visual-studio/connection-dialog.png)
   
   * **Název serveru**. Zadejte **název serveru**, který jste si zjistili.
   * **Ověřování .** Vyberte **Ověřování serveru SQL Server** nebo **Integrované ověřování Active Directory**.
   * **Uživatelské jméno** a **Heslo**: Pokud jste výše vybrali možnost Ověřování serveru SQL Server, zadejte uživatelské jméno a heslo.
   * Klikněte na **Připojit**.
5. Pokud chcete SQL server Azure prozkoumat, rozbalte ho. Můžete se podívat, které databáze jsou k tomuto serveru přidružené. Rozbalte položku AdventureWorksDW a podívejte se na tabulky v ukázkové databázi.
   
    ![Prozkoumejte AdventureWorksDW.](./media/sql-data-warehouse-query-visual-studio/explore-sample.png)

## <a name="2-run-a-sample-query"></a>2. Spuštění ukázkového dotazu
Teď, když jste si vytvořili připojení k databázi, můžete napsat dotaz.

1. Pravým tlačítkem myši klikněte na databázi v Průzkumníku objektů systému SQL Server.
2. Vyberte **Nový dotaz**. Otevře se nové okno dotazu.
   
    ![Nový dotaz](./media/sql-data-warehouse-query-visual-studio/new-query2.png)
3. Do okna dotazu zkopírujte následující dotaz T-SQL:
   
    ```sql
    SELECT COUNT(*) FROM dbo.FactInternetSales;
    ```
4. Spusťte dotaz kliknutím na zelenou šipku nebo použijte následující zkratku: `CTRL` + `SHIFT` + `E`.
   
    ![Spuštění dotazu](./media/sql-data-warehouse-query-visual-studio/run-query.png)
5. Podívejte se na výsledky dotazu. V tomto příkladě má tabulka FactInternetSales 60 398 řádků.
   
    ![Výsledky dotazu](./media/sql-data-warehouse-query-visual-studio/query-results.png)

## <a name="next-steps"></a>Další kroky
Teď, když se můžete připojit a dotazovat, zkuste [data vizualizovat pomocí Power BI](sql-data-warehouse-get-started-visualize-with-power-bi.md).

Pokud chcete nakonfigurovat prostředí pro ověřování služby Azure Active Directory, [přečtěte si](sql-data-warehouse-authentication.md)informace o ověření fondu SQL .
