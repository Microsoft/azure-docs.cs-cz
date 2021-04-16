---
title: Připojení k vyhrazenému fondu SQL (dříve SQL DW) pomocí VSTS
description: Dotazování vyhrazeného fondu SQL (dřív SQL DW) ve službě Azure synapse Analytics se sadou Visual Studio
services: synapse-analytics
author: julieMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 08/15/2019
ms.author: jrasnick
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 926e95887f8d6aa164908a4107656074142a969e
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2021
ms.locfileid: "107566456"
---
# <a name="connect-to-dedicated-sql-pool-formerly-sql-dw-in-azure-synapse-analytics-with-visual-studio-and-ssdt"></a>Připojení k vyhrazenému fondu SQL (dřív SQL DW) ve službě Azure synapse Analytics pomocí sady Visual Studio a SSDT

> [!div class="op_single_selector"]
> * [Azure Data Studio](../sql/get-started-azure-data-studio.md)
> * [Power BI](/power-bi/connect-data/service-azure-sql-data-warehouse-with-direct-connect)
> * [Visual Studio](sql-data-warehouse-query-visual-studio.md)
> * [Sqlcmd](../sql/get-started-connect-sqlcmd.md) 
> * [SSMS](sql-data-warehouse-query-ssms.md)
> 
> 

Pomocí sady Visual Studio se můžete dotazovat na vyhrazený fond SQL (dřív SQL DW) v rámci Azure synapse během několika minut. Tato metoda používá rozšíření SQL Server Data Tools (SSDT) v aplikaci Visual Studio 2019. 

## <a name="prerequisites"></a>Požadavky
Chcete-li použít tento kurz, potřebujete:

* Existující vyhrazený fond SQL (dřív SQL DW). Pokud ho chcete vytvořit, přečtěte si téma [vytvoření vyhrazeného fondu SQL (dřív SQL DW)](create-data-warehouse-portal.md).
* SSDT pro Visual Studio. Pokud máte aplikaci Visual Studio, pravděpodobně již máte SSDT pro Visual Studio. Pokyny k instalaci a možnosti najdete v tématu věnovaném [instalaci sady Visual Studio a rozšíření SSDT](sql-data-warehouse-install-visual-studio.md).
* Plně kvalifikovaný název serveru SQL. Tyto informace najdete v tématu [připojení k vyhrazenému fondu SQL (dřív SQL DW)](sql-data-warehouse-connect-overview.md).

## <a name="1-connect-to-your-dedicated-sql-pool-formerly-sql-dw"></a>1. Připojte se k vyhrazenému fondu SQL (dřív SQL DW).
1. Otevřete Visual Studio 2019.
2. Otevřete Průzkumník objektů systému SQL Server výběrem možnosti **Zobrazit**  >  **Průzkumník objektů systému SQL Server**.
   
    ![Průzkumník objektů systému SQL Server](./media/sql-data-warehouse-query-visual-studio/open-ssdt.png)
3. Klikněte na ikonu **Přidat SQL Server**.
   
    ![Přidat SQL Server](./media/sql-data-warehouse-query-visual-studio/add-server.png)
4. Vyplňte pole v okně pro připojení k serveru.
   
    ![Připojení k serveru](./media/sql-data-warehouse-query-visual-studio/connection-dialog.png)
   
   * **Název serveru** Zadejte **název serveru**, který jste si zjistili.
   * **Ověřování**. Vyberte **Ověřování serveru SQL Server** nebo **Integrované ověřování Active Directory**.
   * **Uživatelské jméno** a **Heslo**: Pokud jste výše vybrali možnost Ověřování serveru SQL Server, zadejte uživatelské jméno a heslo.
   * Klikněte na **Připojit**.
5. Pokud chcete SQL server Azure prozkoumat, rozbalte ho. Můžete se podívat, které databáze jsou k tomuto serveru přidružené. Rozbalte položku AdventureWorksDW a podívejte se na tabulky v ukázkové databázi.
   
    ![Prozkoumejte AdventureWorksDW.](./media/sql-data-warehouse-query-visual-studio/explore-sample.png)

## <a name="2-run-a-sample-query"></a>2. spuštění ukázkového dotazu
Teď, když jste si vytvořili připojení k databázi, můžete napsat dotaz.

1. Pravým tlačítkem myši klikněte na databázi v Průzkumníku objektů systému SQL Server.
2. Vyberte **Nový dotaz**. Otevře se nové okno dotazu.
   
    ![Nový dotaz](./media/sql-data-warehouse-query-visual-studio/new-query2.png)
3. Zkopírujte následující dotaz T-SQL do okna dotazu:
   
    ```sql
    SELECT COUNT(*) FROM dbo.FactInternetSales;
    ```
4. Spusťte dotaz kliknutím na zelenou šipku nebo použijte následující klávesovou zkratku: `CTRL` + `SHIFT` + `E` .
   
    ![Spuštění dotazu](./media/sql-data-warehouse-query-visual-studio/run-query.png)
5. Podívejte se na výsledky dotazu. V tomto příkladě má tabulka FactInternetSales 60 398 řádků.
   
    ![Výsledky dotazu](./media/sql-data-warehouse-query-visual-studio/query-results.png)

## <a name="next-steps"></a>Další kroky
Teď, když se můžete připojit a dotazovat, zkuste [vizualizovat data pomocí Power BI](/power-bi/connect-data/service-azure-sql-data-warehouse-with-direct-connect).

Pokud chcete nakonfigurovat prostředí pro ověřování Azure Active Directory, přečtěte si téma [ověření ve vyhrazeném fondu SQL (dřív SQL DW)](sql-data-warehouse-authentication.md).