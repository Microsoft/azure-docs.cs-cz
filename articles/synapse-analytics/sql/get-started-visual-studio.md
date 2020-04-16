---
title: Připojení k Synapse SQL a dotazování pomocí sady Visual Studio a SSDT
description: Visual Studio slouží k dotazování fondu SQL pomocí Azure Synapse Analytics.
services: synapse analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick
ms.openlocfilehash: 5e725df5c875f23d82da1fda19bcdf28db548fc8
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81428560"
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

Visual Studio slouží k dotazování fondu SQL pomocí Azure Synapse Analytics. Tato metoda používá rozšíření SQL Server Data Tools (SSDT) v Sadě Visual Studio 2019. 

### <a name="supported-tools-for-sql-on-demand-preview"></a>Podporované nástroje pro SQL na vyžádání (náhled)

Visual Studio není aktuálně podporováno pro SQL na vyžádání (náhled). Azure Data Studi (preview)o je však plně podporovaný nástroj. SQL Server Management Studio je částečně podporována z verze 18.4 a má omezené funkce, jako je například připojení a dotazování.

## <a name="prerequisites"></a>Požadavky
Chcete-li použít tento kurz, musíte mít následující součásti:

* Existující fond SQL. Pokud nemáte, naleznete [v tématu vytvoření fondu SQL](../sql-data-warehouse/create-data-warehouse-portal.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) k dokončení tohoto předpokladu.
* SSDT pro Visual Studio. Pokud máte Visual Studio, pravděpodobně již máte tuto součást. Pokyny k instalaci a možnosti najdete v tématu věnovaném [instalaci sady Visual Studio a rozšíření SSDT](../sql-data-warehouse/sql-data-warehouse-install-visual-studio.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).
* Plně kvalifikovaný název serveru SQL. Chcete-li najít, naleznete [v tématu Připojení k fondu SQL](connect-overview.md).

## <a name="1-connect-to-sql-pool"></a>1. Připojení k fondu SQL
1. Otevřete Visual Studio 2019.
2. Otevřete Průzkumník a průzkumník a objektsql Server. Chcete-li to provést, vyberte **zobrazit** > **Průzkumník objektů serveru SQL Server**.
   
    ![Průzkumník objektů systému SQL Server](./media/get-started-visual-studio/open-ssdt.png)
3. Klikněte na ikonu **Přidat SQL Server**.
   
    ![Přidat SQL Server](./media/get-started-visual-studio/add-server.png)
4. Vyplňte pole v okně pro připojení k serveru.
   
    ![Připojení k serveru](./media/get-started-visual-studio/connection-dialog.png)
   
   * **Název serveru**: Zadejte název **serveru,** který byl dříve identifikován.
   * **Ověřování**: Vyberte **ověřování serveru SQL Server** nebo integrované ověřování **služby Active Directory**:
   * **Uživatelské jméno** a **heslo**: Zadejte své uživatelské jméno a heslo, pokud bylo výše vybrané ověřování serveru SQL Server.
   * Klikněte na **Připojit**.
5. Pokud chcete SQL server Azure prozkoumat, rozbalte ho. Můžete se podívat, které databáze jsou k tomuto serveru přidružené. Rozbalte položku AdventureWorksDW a podívejte se na tabulky v ukázkové databázi.
   
    ![Prozkoumejte AdventureWorksDW.](./media/get-started-visual-studio/explore-sample.png)

## <a name="2-run-a-sample-query"></a>2. Spuštění ukázkového dotazu
Nyní, když bylo vytvořeno připojení k databázi, napíšete dotaz.

1. Pravým tlačítkem myši klikněte na databázi v Průzkumníku objektů systému SQL Server.
2. Vyberte **Nový dotaz**. Otevře se nové okno dotazu.
   
    ![Nový dotaz](./media/get-started-visual-studio/new-query2.png)
3. Do okna dotazu zkopírujte následující dotaz T-SQL:
   
    ```sql
    SELECT COUNT(*) FROM dbo.FactInternetSales;
    ```
4. Spusťte dotaz. Pokud to chcete provést, klikněte na zelenou šipku nebo použijte následující klávesovou zkratku: `CTRL`+`SHIFT`+`E`.
   
    ![Spuštění dotazu](./media/get-started-visual-studio/run-query.png)
5. Podívejte se na výsledky dotazu. V tomto příkladě má tabulka FactInternetSales 60 398 řádků.
   
    ![Výsledky dotazu](./media/get-started-visual-studio/query-results.png)

## <a name="next-steps"></a>Další kroky
Teď, když se můžete připojit a dotazovat, zkuste [data vizualizovat pomocí Power BI](get-started-power-bi-professional.md).
Pokud chcete nakonfigurovat prostředí pro ověřování služby Azure Active Directory, [přečtěte si](../sql-data-warehouse/sql-data-warehouse-authentication.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)informace o ověření fondu SQL .
 