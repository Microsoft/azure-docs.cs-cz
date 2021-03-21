---
title: Připojení k synapse SQL pomocí Azure Data Studio
description: Pomocí Azure Data Studio se můžete připojit a dotazovat SQL synapse v Azure synapse Analytics.
services: synapse analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: stefanazaric
ms.reviewer: jrasnick
ms.openlocfilehash: 6b039d934993d2acee630205c5b5e5d8e0f6145e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "101667611"
---
# <a name="connect-to-synapse-sql-with-azure-data-studio"></a>Připojení k synapse SQL pomocí Azure Data Studio

> [!div class="op_single_selector"]
>
> * [Azure Data Studio](get-started-azure-data-studio.md)
> * [Power BI](get-started-power-bi-professional.md)
> * [Visual Studio](../sql-data-warehouse/sql-data-warehouse-query-visual-studio.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
> * [Sqlcmd](get-started-connect-sqlcmd.md)
> * [SSMS](get-started-ssms.md)

[Azure Data Studio](/sql/azure-data-studio/download-azure-data-studio?view=azure-sqldw-latest&preserve-view=true) můžete použít pro připojení k SQL a dotazování synapse v Azure synapse Analytics. 

## <a name="connect"></a>Připojit

Pokud se chcete připojit k synapse SQL, otevřete Azure Data Studio a vyberte **nové připojení**.

![Otevřít Azure Data Studio](./media/get-started-azure-data-studio/1-start.png)

Jako **Typ připojení** vyberte **Microsoft SQL Server** .

Připojení vyžaduje následující parametry:

* **Server:** Server ve formě `<Azure Synapse workspace name>` – OnDemand.SQL.azuresynapse.NET
* **Databáze:** Název databáze

> [!NOTE]
> Pokud byste chtěli použít **fond SQL bez serveru** , adresa URL by měla vypadat takto:
>
> - `<Azure Synapse workspace name>`-ondemand.sql.azuresynapse.net.
>
> Pokud byste chtěli použít **vyhrazený fond SQL** , adresa URL by měla vypadat takto:
>
> - `<Azure Synapse workspace name>`. sql.azuresynapse.net

Jako **typ ověřování** vyberte možnost **ověřování systému Windows**, **Azure Active Directory** nebo **přihlášení SQL** .

Pokud chcete jako typ ověřování použít **přihlášení SQL** , přidejte parametry uživatelského jména a hesla:

* **Uživatel:** Uživatel serveru ve formuláři `<User>`
* **Heslo:** Heslo přidružené k uživateli

Chcete-li použít Azure Active Directory, je nutné vybrat požadovaný typ ověřování.

![Ověřování AAD](./media/get-started-azure-data-studio/3-aad-auth.png)

Následující snímek obrazovky ukazuje **Podrobnosti o připojení** pro **ověřování systému Windows**:

![Ověřování systému Windows](./media/get-started-azure-data-studio/3-windows-auth.png)

Následující snímek obrazovky ukazuje **Podrobnosti připojení** pomocí **přihlášení SQL**:

![Přihlášení k SQL](./media/get-started-azure-data-studio/2-database-details.png)

Po úspěšném přihlášení byste měli vidět řídicí panel podobný tomuto: ![ řídicí panel](./media/get-started-azure-data-studio/4-dashboard.png)

## <a name="query"></a>Dotaz

Po připojení můžete dotazovat synapse SQL pomocí podporovaných příkazů [jazyka Transact-SQL (T-SQL)](/sql/t-sql/language-reference?view=azure-sqldw-latest&preserve-view=true) na instanci. Začněte tím, že vyberete **Nový dotaz** v zobrazení řídicího panelu.

![Nový dotaz](./media/get-started-azure-data-studio/5-new-query.png)

Například můžete použít následující příkaz Transact-SQL k [dotazování souborů Parquet](query-parquet-files.md) pomocí fondu SQL bez serveru:

```sql
SELECT COUNT(*)
FROM  
OPENROWSET(
    BULK 'https://azureopendatastorage.blob.core.windows.net/censusdatacontainer/release/us_population_county/year=20*/*.parquet',
    FORMAT='PARQUET'
)
```
## <a name="next-steps"></a>Další kroky 
Prozkoumejte další způsoby, jak se připojit k synapse SQL: 

- [SSMS](get-started-ssms.md)
- [Power BI](get-started-power-bi-professional.md)
- [Visual Studio](../sql-data-warehouse/sql-data-warehouse-query-visual-studio.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [Sqlcmd](get-started-connect-sqlcmd.md)

Další informace najdete na webu [použití Azure Data Studio k připojení a dotazování dat pomocí vyhrazeného fondu SQL ve službě Azure synapse Analytics](/sql/azure-data-studio/quickstart-sql-dw).
