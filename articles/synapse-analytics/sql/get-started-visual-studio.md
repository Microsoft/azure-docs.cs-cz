---
title: Připojení a dotazování SQL synapse pomocí sady Visual Studio a SSDT
description: Pomocí sady Visual Studio se můžete dotazovat na vyhrazený fond SQL pomocí Azure synapse Analytics.
services: synapse analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 04/15/2020
ms.author: stefanazaric
ms.reviewer: jrasnick
ms.openlocfilehash: ef8e2a3d1a6b78e8f2b6b9a900ed2485c1a4a5d7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "96451601"
---
# <a name="connect-to-synapse-sql-with-visual-studio-and-ssdt"></a>Připojení k Synapse SQL pomocí sady Visual Studio a SSDT

> [!div class="op_single_selector"]
> * [Azure Data Studio](get-started-azure-data-studio.md)
> * [Power BI](get-started-power-bi-professional.md)
> * [Visual Studio](get-started-visual-studio.md)
> * [Sqlcmd](get-started-connect-sqlcmd.md) 
> * [SSMS](get-started-ssms.md)
> 
> 

Pomocí sady Visual Studio se můžete dotazovat na vyhrazený fond SQL pomocí Azure synapse Analytics. Tato metoda používá rozšíření SQL Server Data Tools (SSDT) v aplikaci Visual Studio 2019. 

> [!NOTE]
> SSDT není podporován fondem SQL bez serveru.

## <a name="prerequisites"></a>Požadavky

Chcete-li použít tento kurz, musíte mít následující komponenty:

* Existující vyhrazený fond SQL. Pokud ho nemáte, přečtěte si téma [vytvoření vyhrazeného fondu SQL](../sql-data-warehouse/create-data-warehouse-portal.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) pro dokončení této součásti.
* SSDT pro Visual Studio. Pokud máte aplikaci Visual Studio, pravděpodobně tuto součást již máte. Pokyny k instalaci a možnosti najdete v tématu věnovaném [instalaci sady Visual Studio a rozšíření SSDT](../sql-data-warehouse/sql-data-warehouse-install-visual-studio.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).
* Plně kvalifikovaný název serveru SQL. Název tohoto serveru najdete v tématu [připojení k vyhrazenému fondu SQL](connect-overview.md).

## <a name="1-connect-to-a-dedicated-sql-pool"></a>1. připojení k vyhrazenému fondu SQL
1. Otevřete Visual Studio 2019.
2. Kliknutím na **Zobrazit**  >  **Průzkumník objektů systému SQL Server** otevřete Průzkumník objektů systému SQL Server.
   
    ![Průzkumník objektů systému SQL Server](./media/get-started-visual-studio/open-ssdt.png)
3. Klikněte na ikonu **Přidat SQL Server**.
   
    ![Přidat SQL Server](./media/get-started-visual-studio/add-server.png)
4. Vyplňte pole v okně pro připojení k serveru.
   
    ![Připojení k serveru](./media/get-started-visual-studio/connection-dialog.png)
   
   * **Název serveru**: zadejte **název serveru** , který jste předtím identifikovali.
   * **Ověřování**: vyberte **ověřování SQL Server** nebo **integrované ověřování služby Active Directory**:
   * **Uživatelské jméno** a **heslo**: pokud jste výše vybrali SQL Server ověřování, zadejte své uživatelské jméno a heslo.
   * Klikněte na **Připojit**.
5. Pokud chcete SQL server Azure prozkoumat, rozbalte ho. Můžete se podívat, které databáze jsou k tomuto serveru přidružené. Rozbalte položku AdventureWorksDW a podívejte se na tabulky v ukázkové databázi.
   
    ![Prozkoumejte AdventureWorksDW.](./media/get-started-visual-studio/explore-sample.png)

## <a name="2-run-a-sample-query"></a>2. spuštění ukázkového dotazu
Teď, když jste navázali připojení k vaší databázi, napíšete dotaz.

1. Pravým tlačítkem myši klikněte na databázi v Průzkumníku objektů systému SQL Server.
2. Vyberte **Nový dotaz**. Otevře se nové okno dotazu.
   
    ![Nový dotaz](./media/get-started-visual-studio/new-query2.png)
3. Zkopírujte následující dotaz T-SQL do okna dotazu:
   
    ```sql
    SELECT COUNT(*) FROM dbo.FactInternetSales;
    ```
4. Spusťte dotaz kliknutím na zelenou šipku nebo použijte následující klávesovou zkratku: `CTRL` + `SHIFT` + `E` .
   
    ![Spuštění dotazu](./media/get-started-visual-studio/run-query.png)
5. Podívejte se na výsledky dotazu. V tomto příkladě má tabulka FactInternetSales 60 398 řádků.
   
    ![Výsledky dotazu](./media/get-started-visual-studio/query-results.png)

## <a name="next-steps"></a>Další kroky
Teď, když se můžete připojit a dotazovat, zkuste [vizualizovat data pomocí Power BI](get-started-power-bi-professional.md).
Pokud chcete nakonfigurovat prostředí pro ověřování Azure Active Directory, přečtěte si téma [ověření ve vyhrazeném fondu SQL](../sql-data-warehouse/sql-data-warehouse-authentication.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).
 