---
title: Připojení přes SSMS
description: Pomocí SQL Server Management Studio (SSMS) se můžete připojit k Azure synapse Analytics a dotazovat se na ně.
services: sql-data-warehouse
author: XiaoyuMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: development
ms.date: 04/17/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 2109402a874ff8c722bd05e1e5cb62b461cb2292
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/29/2020
ms.locfileid: "78198614"
---
# <a name="connect-to-azure-synapse-analytics-with-sql-server-management-studio-ssms"></a>Připojení ke službě Azure synapse Analytics pomocí SQL Server Management Studio (SSMS)
> [!div class="op_single_selector"]
> * [Power BI](sql-data-warehouse-get-started-visualize-with-power-bi.md)
> * [Azure Machine Learning](sql-data-warehouse-get-started-analyze-with-azure-machine-learning.md)
> * [Visual Studio](sql-data-warehouse-query-visual-studio.md)
> * [sqlcmd](sql-data-warehouse-get-started-connect-sqlcmd.md) 
> * [SSMS](sql-data-warehouse-query-ssms.md)
> 
> 

Pomocí SQL Server Management Studio (SSMS) se můžete připojit k datovému skladu a dotazovat se na něj v rámci Azure synapse. 

## <a name="prerequisites"></a>Předpoklady
Chcete-li použít tento kurz, potřebujete:

* Existující fond SQL. Pokud ho chcete vytvořit, přečtěte si téma [Vytvoření fondu SQL](sql-data-warehouse-get-started-provision.md).
* SQL Server Management Studio (SSMS) nainstalováno. [Nainstalujte si SSMS](https://msdn.microsoft.com/library/hh213248.aspx) zdarma, pokud ho ještě nemáte.
* Plně kvalifikovaný název serveru SQL. Tyto informace najdete v tématu [připojení k fondu SQL](sql-data-warehouse-connect-overview.md).

## <a name="1-connect-to-your-sql-pool"></a>1. připojení k vašemu fondu SQL
1. Otevřete aplikaci SSMS.
2. Otevřete Průzkumník objektů tak, že vyberete **soubor** > **Připojit Průzkumník objektů**.
   
    ![Průzkumník objektů systému SQL Server](media/sql-data-warehouse-query-ssms/connect-object-explorer.png)
3. Vyplňte pole v okně pro připojení k serveru.
   
    ![Připojení k serveru](media/sql-data-warehouse-query-ssms/connect-object-explorer1.png)
   
   * **Název serveru**: Zadejte **název serveru**, který jste si zjistili.
   * **Ověřování**: Vyberte **Ověřování serveru SQL Server** nebo **Integrované ověřování Active Directory**.
   * **Uživatelské jméno** a **Heslo**: Pokud jste výše vybrali možnost Ověřování serveru SQL Server, zadejte uživatelské jméno a heslo.
   * Klikněte na **Připojit**.
4. Pokud chcete SQL server Azure prozkoumat, rozbalte ho. Můžete se podívat, které databáze jsou k tomuto serveru přidružené. Rozbalte položku AdventureWorksDW a podívejte se na tabulky v ukázkové databázi.
   
    ![Prozkoumejte AdventureWorksDW.](media/sql-data-warehouse-query-ssms/explore-tables.png)

## <a name="2-run-a-sample-query"></a>2. spuštění ukázkového dotazu
Teď, když jste si vytvořili připojení k databázi, můžete napsat dotaz.

1. Pravým tlačítkem myši klikněte na databázi v Průzkumníku objektů systému SQL Server.
2. Vyberte **Nový dotaz**. Otevře se nové okno dotazu.
   
    ![Nový dotaz]( media/sql-data-warehouse-query-ssms/new-query.png)
3. Zkopírujte následující dotaz T-SQL do okna dotazu:
   
    ```sql
    SELECT COUNT(*) FROM dbo.FactInternetSales;
    ```
4. Spusťte dotaz kliknutím na `Execute` nebo použijte následující zkratku: `F5`.
   
    ![Spuštění dotazu](media/sql-data-warehouse-query-ssms/execute-query.png)
5. Podívejte se na výsledky dotazu. V tomto příkladě má tabulka FactInternetSales 60 398 řádků.
   
    ![Výsledky dotazu](media/sql-data-warehouse-query-ssms/results.png)

## <a name="next-steps"></a>Další kroky
Teď, když se můžete připojit a dotazovat, zkuste [vizualizovat data pomocí Power BI](sql-data-warehouse-get-started-visualize-with-power-bi.md ).
Pokud chcete nakonfigurovat prostředí pro ověřování Azure Active Directory, přečtěte si téma [ověření ve fondu SQL](sql-data-warehouse-authentication.md).
