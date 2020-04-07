---
title: Připojení přes SSMS
description: Pomocí sql server management studio (SSMS) pro připojení a dotaz Azure Synapse Analytics.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/17/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 0d65ea9b684439cab384bbaa09fcdef1302b6bcf
ms.sourcegitcommit: bd5fee5c56f2cbe74aa8569a1a5bce12a3b3efa6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "80743863"
---
# <a name="connect-to-azure-synapse-analytics-with-sql-server-management-studio-ssms"></a>Připojení k Azure Synapse Analytics pomocí sql server management studio (SSMS)

> [!div class="op_single_selector"]
>
> * [Power BI](sql-data-warehouse-get-started-visualize-with-power-bi.md)
> * [Azure Machine Learning](sql-data-warehouse-get-started-analyze-with-azure-machine-learning.md)
> * [Visual Studio](sql-data-warehouse-query-visual-studio.md)
> * [Sqlcmd](sql-data-warehouse-get-started-connect-sqlcmd.md)
> * [SSMS](sql-data-warehouse-query-ssms.md)

Pomocí sql server management studio (SSMS) pro připojení a dotaz datového skladu v rámci Azure Synapse.

## <a name="prerequisites"></a>Požadavky

Chcete-li použít tento kurz, potřebujete:

* Existující fond SQL. Pokud chcete ho vytvořit, [přečtěte si](create-data-warehouse-portal.md)informace o vytvoření fondu SQL .
* Sql Server Management Studio (SSMS) nainstalován. [Stáhněte si SSMS](/sql/ssms/download-sql-server-management-studio-ssms?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) zdarma, pokud ji ještě nemáte.
* Plně kvalifikovaný název serveru SQL. Chcete-li tyto informace najít, [přečtěte si](sql-data-warehouse-connect-overview.md)téma Připojení k fondu SQL .

## <a name="1-connect-to-your-sql-pool"></a>1. Připojení k fondu SQL

1. Otevřete aplikaci SSMS.
2. Otevřete Průzkumníka objektů výběrem**Průzkumníka objektů Připojení** **souborů** > .

    ![Průzkumník objektů systému SQL Server](./media/sql-data-warehouse-query-ssms/connect-object-explorer.png)
3. Vyplňte pole v okně pro připojení k serveru.

   ![Připojení k serveru](./media/sql-data-warehouse-query-ssms/connect-object-explorer1.png)

   * **Název serveru**. Zadejte **název serveru**, který jste si zjistili.
   * **Ověřování .** Vyberte **Ověřování serveru SQL Server** nebo **Integrované ověřování Active Directory**.
   * **Uživatelské jméno** a **Heslo**: Pokud jste výše vybrali možnost Ověřování serveru SQL Server, zadejte uživatelské jméno a heslo.
   * Klikněte na **Připojit**.
4. Pokud chcete SQL server Azure prozkoumat, rozbalte ho. Můžete se podívat, které databáze jsou k tomuto serveru přidružené. Rozbalte položku AdventureWorksDW a podívejte se na tabulky v ukázkové databázi.

   ![Prozkoumejte AdventureWorksDW.](./media/sql-data-warehouse-query-ssms/explore-tables.png)

## <a name="2-run-a-sample-query"></a>2. Spuštění ukázkového dotazu

Teď, když jste si vytvořili připojení k databázi, můžete napsat dotaz.

1. Pravým tlačítkem myši klikněte na databázi v Průzkumníku objektů systému SQL Server.
2. Vyberte **Nový dotaz**. Otevře se nové okno dotazu.

   ![Nový dotaz](./media/sql-data-warehouse-query-ssms/new-query.png)
3. Do okna dotazu zkopírujte následující dotaz T-SQL:

   ```sql
   SELECT COUNT(*) FROM dbo.FactInternetSales;
   ```

4. Spusťte dotaz `Execute` klepnutím nebo pomocí `F5`následujícího zástupce: .

   ![Spuštění dotazu](./media/sql-data-warehouse-query-ssms/execute-query.png)
5. Podívejte se na výsledky dotazu. V tomto příkladě má tabulka FactInternetSales 60 398 řádků.

   ![Výsledky dotazu](./media/sql-data-warehouse-query-ssms/results.png)

## <a name="next-steps"></a>Další kroky

Teď, když se můžete připojit a dotazovat, zkuste [data vizualizovat pomocí Power BI](sql-data-warehouse-get-started-visualize-with-power-bi.md). Pokud chcete nakonfigurovat prostředí pro ověřování služby Azure Active Directory, [přečtěte si](sql-data-warehouse-authentication.md)informace o ověření fondu SQL .
