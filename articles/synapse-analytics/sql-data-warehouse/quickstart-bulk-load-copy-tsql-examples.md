---
title: Mechanismy ověřování s příkazem COPY
description: Popisuje mechanismy ověřování pro hromadné načtení dat.
services: synapse-analytics
author: kevinvngo
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: sql-dw
ms.date: 07/10/2020
ms.author: kevin
ms.reviewer: jrasnick
ms.openlocfilehash: de446209104c113b10346645f79b461239c3efab
ms.sourcegitcommit: 80c1056113a9d65b6db69c06ca79fa531b9e3a00
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/09/2020
ms.locfileid: "96901266"
---
# <a name="securely-load-data-using-synapse-sql"></a>Bezpečné načtení dat pomocí synapse SQL

Tento článek popisuje a poskytuje příklady mechanismů zabezpečeného ověřování pro [příkaz Copy](https://docs.microsoft.com/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest). Příkaz COPY je nejpružnější a bezpečný způsob hromadného načítání dat v synapse SQL.
## <a name="supported-authentication-mechanisms"></a>Podporované mechanismy ověřování

Následující matice popisuje podporované metody ověřování pro každý typ souboru a účet úložiště. To platí pro zdrojové umístění úložiště a umístění chybového souboru.

|                          |                CSV                |                      Parquet                       |                        ORC                         |
| :----------------------: | :-------------------------------: | :------------------------------------------------: | :------------------------------------------------: |
|  **Azure Blob Storage**  | SAS/MSI/INSTANČNÍ OBJEKT/KLÍČ/AAD |                      SAS/KLÍČ                       |                      SAS/KLÍČ                       |
| **Azure Data Lake Gen2** | SAS/MSI/INSTANČNÍ OBJEKT/KLÍČ/AAD | SAS (BLOB<sup>1</sup>)/MSI (DFS<sup>2</sup>)/Service Principal/Key/AAD | SAS (BLOB<sup>1</sup>)/MSI (DFS<sup>2</sup>)/Service Principal/Key/AAD |

1: pro tuto metodu ověřování je vyžadován koncový bod objektu BLOB (**. blob**. Core.Windows.NET) v cestě k externímu umístění.

2: pro tuto metodu ověřování je vyžadován koncový bod. DFS (**. DFS**. Core.Windows.NET) v cestě k externímu umístění.

## <a name="a-storage-account-key-with-lf-as-the-row-terminator-unix-style-new-line"></a>A. Klíč účtu úložiště s LF jako ukončovací znak řádku (nový řádek systému UNIX)


```sql
--Note when specifying the column list, input field numbers start from 1
COPY INTO target_table (Col_one default 'myStringDefault' 1, Col_two default 1 3)
FROM 'https://adlsgen2account.dfs.core.windows.net/myblobcontainer/folder1/'
WITH (
    FILE_TYPE = 'CSV'
    ,CREDENTIAL=(IDENTITY= 'Storage Account Key', SECRET='<Your_Account_Key>')
    --CREDENTIAL should look something like this:
    --CREDENTIAL=(IDENTITY= 'Storage Account Key', SECRET='x6RWv4It5F2msnjelv3H4DA80n0QW0daPdw43jM0nyetx4c6CpDkdj3986DX5AHFMIf/YN4y6kkCnU8lb+Wx0Pj+6MDw=='),
    ,ROWTERMINATOR='0x0A' --0x0A specifies to use the Line Feed character (Unix based systems)
)
```
> [!IMPORTANT]
>
> - K určení znaku LF/nového řádku použijte hexadecimální hodnotu (0x0A). Všimněte si, že příkaz COPY bude interpretovat řetězec "\n" jako "\r\n" (nový řádek pro návrat na začátek řádku).

## <a name="b-shared-access-signatures-sas-with-crlf-as-the-row-terminator-windows-style-new-line"></a>B. Sdílené přístupové podpisy (SAS) s klávesou CRLF jako ukončovací znak řádku (nový řádek Windows)
```sql
COPY INTO target_table
FROM 'https://adlsgen2account.dfs.core.windows.net/myblobcontainer/folder1/'
WITH (
    FILE_TYPE = 'CSV'
    ,CREDENTIAL=(IDENTITY= 'Shared Access Signature', SECRET='<Your_SAS_Token>')
    --CREDENTIAL should look something like this:
    --CREDENTIAL=(IDENTITY= 'Shared Access Signature', SECRET='?sv=2018-03-28&ss=bfqt&srt=sco&sp=rl&st=2016-10-17T20%3A14%3A55Z&se=2021-10-18T20%3A19%3A00Z&sig=IEoOdmeYnE9%2FKiJDSFSYsz4AkNa%2F%2BTx61FuQ%2FfKHefqoBE%3D'),
    ,ROWTERMINATOR='\n'-- COPY command automatically prefixes the \r character when \n (newline) is specified. This results in carriage return newline (\r\n) for Windows based systems.
)
```

> [!IMPORTANT]
>
> - Nezadávejte ROWTERMINATOR jako "\r\n", který bude interpretován jako "\r\r\n" a může mít za následek problémy s analýzou.

## <a name="c-managed-identity"></a>C. Spravovaná identita

Pokud je váš účet úložiště připojený k virtuální síti, vyžaduje se spravované ověřování identity. 

### <a name="prerequisites"></a>Předpoklady

1. Pomocí tohoto [průvodce](/powershell/azure/install-az-ps?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) nainstalujte Azure PowerShell.
2. Pokud máte účet úložiště pro obecné účely verze 1 nebo účet úložiště objektů blob, musíte nejprve pomocí tohoto [průvodce](../../storage/common/storage-account-upgrade.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) upgradovat na účet úložiště pro obecné účely verze 2.
3. Abyste měli přístup k tomuto účtu úložiště zapnutý, musíte mít **povolené důvěryhodné služby Microsoftu** v nabídce Azure Storage **brány firewall účtů a nastavení virtuálních sítí** . Další informace najdete v tomto [průvodci](../../storage/common/storage-network-security.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json#exceptions).

#### <a name="steps"></a>Postup

1. Pokud máte samostatný vyhrazený fond SQL, zaregistrujte SQL Server pomocí Azure Active Directory (AAD) pomocí prostředí PowerShell: 

   ```powershell
   Connect-AzAccount
   Select-AzSubscription -SubscriptionId <subscriptionId>
   Set-AzSqlServer -ResourceGroupName your-database-server-resourceGroup -ServerName your-SQL-servername -AssignIdentity
   ```

   Tento krok není nutný pro vyhrazené fondy SQL v pracovním prostoru synapse.

1. Pokud máte pracovní prostor synapse, zaregistrujte identitu spravovanou systémem v pracovním prostoru:

   1. V Azure Portal můžete přejít do svého pracovního prostoru synapse
   2. Přejít na okno spravované identity 
   3. Ujistěte se, že je povolená možnost Povolit kanály.
   
   ![Registrovat MSI systému v pracovním prostoru](./media/quickstart-bulk-load-copy-tsql-examples/msi-register-example.png)

1. Pomocí této [příručky](../../storage/common/storage-account-create.md)vytvořte **účet úložiště pro obecné účely v2** .

   > [!NOTE]
   >
   > - Pokud máte účet úložiště pro obecné účely v1 nebo blob, musíte **nejdřív upgradovat na verzi v2** pomocí této [příručky](../../storage/common/storage-account-upgrade.md).
   > - Známé problémy s Azure Data Lake Storage Gen2 najdete v tomto [Průvodci](../../storage/blobs/data-lake-storage-known-issues.md).

1. V části účet úložiště přejděte na **Access Control (IAM)** a vyberte **Přidat přiřazení role**. Přiřaďte roli Azure **Přispěvatel dat objektů BLOB úložiště** k serveru nebo pracovnímu prostoru hostujícím vyhrazený fond SQL, který jste zaregistrovali v Azure Active Directory (AAD).

   > [!NOTE]
   > Tento krok mohou provádět pouze členové s oprávněním vlastníka. Informace o různých předdefinovaných rolích Azure najdete v tomto [Průvodci](../../role-based-access-control/built-in-roles.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json).
   
    > [!IMPORTANT]
    > Zadejte roli vlastníka **dat objektu BLOB** **úložiště** , přispěvatele nebo čtenáře Azure. Tyto role se liší od předdefinovaných rolí vlastník, přispěvatel a čtenář v Azure. 

    ![Udělení oprávnění Azure RBAC k načtení](./media/quickstart-bulk-load-copy-tsql-examples/rbac-load-permissions.png)

4. Nyní můžete spustit příkaz COPY, který určuje spravovanou identitu:

    ```sql
    COPY INTO dbo.target_table
    FROM 'https://myaccount.blob.core.windows.net/myblobcontainer/folder1/*.txt'
    WITH (
        FILE_TYPE = 'CSV',
        CREDENTIAL = (IDENTITY = 'Managed Identity'),
    )
    ```

## <a name="d-azure-active-directory-authentication"></a>D. Ověřování Azure Active Directory
#### <a name="steps"></a>Postup

1. V části účet úložiště přejděte na **Access Control (IAM)** a vyberte **Přidat přiřazení role**. Přiřazení role **objektu BLOB úložiště, přispěvatele nebo čtenáře** pro uživatele Azure AD. 

    > [!IMPORTANT]
    > Zadejte roli vlastníka **dat objektu BLOB** **úložiště** , přispěvatele nebo čtenáře Azure. Tyto role se liší od předdefinovaných rolí vlastník, přispěvatel a čtenář v Azure.

    ![Udělení oprávnění Azure RBAC k načtení](./media/quickstart-bulk-load-copy-tsql-examples/rbac-load-permissions.png)

2. Projděte si následující [dokumentaci](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication-configure?tabs=azure-powershell#create-an-azure-ad-administrator-for-azure-sql-server)a nakonfigurujte ověřování Azure AD. 

3. Připojte se ke svému fondu SQL pomocí služby Active Directory, kde teď můžete spustit příkaz COPY bez zadání přihlašovacích údajů:

    ```sql
    COPY INTO dbo.target_table
    FROM 'https://myaccount.blob.core.windows.net/myblobcontainer/folder1/*.txt'
    WITH (
        FILE_TYPE = 'CSV'
    )
    ```


## <a name="e-service-principal-authentication"></a>E. Ověřování instančních objektů
#### <a name="steps"></a>Postup

1. [Vytvoření aplikace Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#create-an-azure-active-directory-application)
2. [Získat ID aplikace](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in)
3. [Získání ověřovacího klíče](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#create-a-new-application-secret)
4. [Získat koncový bod tokenu v1 OAuth 2,0](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-service-to-service-authenticate-using-active-directory?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json#step-4-get-the-oauth-20-token-endpoint-only-for-java-based-applications)
5. [Přiřazení oprávnění ke čtení, zápisu a provádění vaší aplikace služby Azure AD](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-service-to-service-authenticate-using-active-directory?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json#step-3-assign-the-azure-ad-application-to-the-azure-data-lake-storage-gen1-account-file-or-folder) v účtu úložiště
6. Nyní můžete spustit příkaz COPY:

    ```sql
    COPY INTO dbo.target_table
    FROM 'https://myaccount.blob.core.windows.net/myblobcontainer/folder0/*.txt'
    WITH (
        FILE_TYPE = 'CSV'
        ,CREDENTIAL=(IDENTITY= '<application_ID>@<OAuth_2.0_Token_EndPoint>' , SECRET= '<authentication_key>')
        --CREDENTIAL should look something like this:
        --,CREDENTIAL=(IDENTITY= '92761aac-12a9-4ec3-89b8-7149aef4c35b@https://login.microsoftonline.com/72f714bf-86f1-41af-91ab-2d7cd011db47/oauth2/token', SECRET='juXi12sZ6gse]woKQNgqwSywYv]7A.M')
    )
    ```

> [!IMPORTANT]
>
> - Použijte verzi **v1** koncového bodu tokenu OAuth 2,0.

## <a name="next-steps"></a>Další kroky

- Podrobnou syntaxi najdete v článku o [příkazu copy](https://docs.microsoft.com/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest#syntax) .
- V článku [Přehled načítání dat](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/design-elt-data-loading#what-is-elt) najdete doporučené postupy při načítání.
