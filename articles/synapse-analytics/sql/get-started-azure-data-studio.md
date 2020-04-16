---
title: 'Azure Data Studio (preview): Připojení a dotaz synapse SQL'
description: Pomocí Azure Data Studio (preview) se můžete připojit k Synapse SQL a dotazovat se na něj v Azure Synapse Analytics.
services: synapse analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick
ms.openlocfilehash: edf5a6a6a0f17c21abb818a0d41d0d0b1c39949c
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81423800"
---
# <a name="connect-to-synapse-sql-with-azure-data-studio-preview"></a>Připojení k Synapse SQL pomocí Azure Data Studio (preview)

> [!div class="op_single_selector"]
>
> * [Azure Data Studio](get-started-azure-data-studio.md)
> * [Power BI](get-started-power-bi-professional.md)
> * [Visual Studio](../sql-data-warehouse/sql-data-warehouse-query-visual-studio.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
> * [Sqlcmd](get-started-connect-sqlcmd.md)
> * [SSMS](get-started-ssms.md)

K připojení a dotazování Synapse SQL v Azure Synapse Analytics se můžete připojit k Řešení [dat(preview) a](/sql/azure-data-studio/download-azure-data-studio?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) zadat dotaz na Synapse SQL. 

## <a name="connect"></a>Připojit

Chcete-li se připojit k Synapse SQL, otevřete Azure Data Studio a vyberte **Nové připojení**.

![Otevření Datového studia Azure](./media/get-started-azure-data-studio/1-start.png)

Jako **typ připojení**zvolte Microsoft **SQL Server** .

Připojení vyžaduje následující parametry:

* **Server:** Server ve `<Azure Synapse workspace name>`formě -ondemand.sql.azuresynapse.net
* **Databáze:** Název databáze

> [!NOTE]
> Pokud chcete použít **SQL na vyžádání (náhled),** adresa URL by měla vypadat takto:
>
> - `<Azure Synapse workspace name>`-ondemand.sql.azuresynapse.net.
>
> Pokud chcete použít **fond SQL,** měla by adresa URL vypadat takto:
>
> - `<Azure Synapse workspace name>`.sql.azuresynapse.net

Jako **typ ověřování**zvolte **Ověřování systému Windows**, **Službu Azure Active Directory**nebo Přihlášení **SQL** .

Chcete-li jako typ ověřování použít **službu SQL Login,** přidejte parametry uživatelského jména a hesla:

* **Uživatel:** Uživatel serveru ve formuláři`<User>`
* **Heslo:** Heslo přidružené k uživateli

Chcete-li používat službu Azure Active Directory, musíte zvolit potřebný typ ověřování.

![Ověřování AAD](./media/get-started-azure-data-studio/3-aad-auth.png)

Tento snímek obrazovky zobrazuje **podrobnosti o připojení** pro **ověřování systému Windows**:

![Ověřování systému Windows](./media/get-started-azure-data-studio/3-windows-auth.png)

Tento snímek obrazovky zobrazuje **podrobnosti o připojení** pomocí **přihlášení SQL**:

![Přihlášení k SQL serveru](./media/get-started-azure-data-studio/2-database-details.png)

Po úspěšném přihlášení byste měli vidět ![řídicí panel, jako je tento: Řídicí panel](./media/get-started-azure-data-studio/4-dashboard.png)

## <a name="query"></a>Dotaz

Po připojení můžete dotaz Synapse SQL pomocí podporovaných příkazů [Transact-SQL (T-SQL)](/sql/t-sql/language-reference?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) proti instanci. Chcete-li začít, vyberte **Nový dotaz** ze zobrazení řídicího panelu.

![Nový dotaz](./media/get-started-azure-data-studio/5-new-query.png)

Můžete například použít následující příkaz Transact-SQL k [dotazování parketových souborů](query-parquet-files.md) pomocí SQL na vyžádání:

```sql
SELECT COUNT(*)
FROM  
OPENROWSET(
    BULK 'https://azureopendatastorage.blob.core.windows.net/censusdatacontainer/release/us_population_county/year=20*/*.parquet',
    FORMAT='PARQUET'
)
```
## <a name="next-steps"></a>Další kroky 
Prozkoumejte další způsoby připojení k Synapse SQL: 

- [SSMS](get-started-ssms.md)
- [Power BI](get-started-power-bi-professional.md)
- [Visual Studio](../sql-data-warehouse/sql-data-warehouse-query-visual-studio.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [Sqlcmd](get-started-connect-sqlcmd.md)
 
