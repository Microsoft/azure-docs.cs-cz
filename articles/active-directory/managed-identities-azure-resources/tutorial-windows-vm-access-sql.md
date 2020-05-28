---
title: Kurz `:` použití spravované identity pro přístup k Azure SQL Database-Windows-Azure AD
description: Kurz, který vás provede procesem použití spravované identity přiřazené systémem Windows VM pro přístup k Azure SQL Database.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 01/14/2020
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: dd3d3aeecb66ba332d9c32c944d527ac3a07f2fe
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/27/2020
ms.locfileid: "84014311"
---
# <a name="tutorial-use-a-windows-vm-system-assigned-managed-identity-to-access-azure-sql"></a>Kurz: Použití spravované identity přiřazené systémem na virtuálním počítači s Windows pro přístup k Azure SQL

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

V tomto kurzu se dozvíte, jak používat identitu přiřazenou systémem pro virtuální počítač s Windows (VM) pro přístup k Azure SQL Database. Identity spravovaných služeb, které se spravují automaticky v Azure, slouží k ověření přístupu ke službám podporujícím ověřování Azure AD bez nutnosti vložení přihlašovacích údajů do kódu. Získáte informace o těchto tématech:

> [!div class="checklist"]
>
> * Udělení přístupu k VIRTUÁLNÍmu počítači pro Azure SQL Database
> * Povolit ověřování Azure AD
> * Vytvoření uživatele v databázi reprezentujícího systémem přiřazenou identitu virtuálního počítače
> * Získání přístupového tokenu pomocí identity virtuálního počítače a jeho použití k dotazování Azure SQL Database

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [msi-tut-prereqs](../../../includes/active-directory-msi-tut-prereqs.md)]

## <a name="enable"></a>Povolit

[!INCLUDE [msi-tut-enable](../../../includes/active-directory-msi-tut-enable.md)]

## <a name="grant-access"></a>Udělení přístupu

Pokud chcete vašemu VIRTUÁLNÍmu počítači udělit přístup k databázi v Azure SQL Database, můžete použít existující [logický SQL Server](../../azure-sql/database/logical-servers.md) nebo vytvořit nový. Pokud chcete vytvořit nový server a databázi pomocí webu Azure Portal, postupujte podle tohoto [rychlého startu k Azure SQL](https://docs.microsoft.com/azure/sql-database/sql-database-get-started-portal). Rychlé starty s využitím Azure CLI a Azure PowerShellu najdete v [dokumentaci k Azure SQL](https://docs.microsoft.com/azure/sql-database/).

Udělení přístupu virtuálnímu počítači k databázi se skládá ze dvou kroků:

1. Povolte ověřování Azure AD pro server.
2. Vytvoření **uživatele** v databázi reprezentujícího systémem přiřazenou identitu virtuálního počítače

### <a name="enable-azure-ad-authentication"></a>Povolit ověřování Azure AD

**[Konfigurace ověřování Azure AD](/azure/sql-database/sql-database-aad-authentication-configure):**

1. Na levém navigačním panelu na webu Azure Portal vyberte **Servery SQL**.
2. Kliknutím na server SQL u něj povolte ověřování Azure AD.
3. V části okna **Nastavení** klikněte na **Správce Active Directory**.
4. Na panelu příkazů klikněte na **Nastavit správce**.
5. Vyberte uživatelský účet Azure AD, který se má stát správcem serveru, a klikněte na **Vybrat**.
6. Na panelu příkazů klikněte na **Uložit**.

### <a name="create-contained-user"></a>Vytvořit obsaženého uživatele

V této části se dozvíte, jak v databázi vytvořit obsaženého uživatele, který reprezentuje identitu přiřazenou systémem virtuálního počítače. Pro tento krok potřebujete [Microsoft SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) (SSMS). Než začnete, můžete si také přečíst následující články se základními informacemi o integraci Azure AD:

* [Univerzální ověřování s využitím služeb SQL Database a SQL Data Warehouse (podpora SSMS pro MFA)](/azure/sql-database/sql-database-ssms-mfa-authentication)
* [Konfigurace a správa ověřování služby Azure Active Directory s využitím služby SQL Database nebo SQL Data Warehouse](/azure/sql-database/sql-database-aad-authentication-configure)

SQL Database vyžaduje jedinečné zobrazované názvy AAD. V tomto případě musí být účty AAD, jako jsou uživatelé, skupiny a instanční objekty (aplikace) a názvy virtuálních počítačů povolené pro spravovanou identitu, jedinečné v AAD, které se týkají jejich zobrazovaných názvů. SQL Database kontroluje zobrazované jméno AAD během vytváření takových uživatelů T-SQL, a pokud není jedinečné, příkaz se nepovede, aby pro daný účet zadal jedinečný zobrazovaný název AAD.

**Vytvoření uživatele s omezením:**

1. Spusťte aplikaci SQL Server Management Studio.
2. V dialogovém okně **připojit k serveru** zadejte do pole **název serveru** název svého serveru.
3. V poli **Ověřování** vyberte **Active Directory – univerzální s podporou vícefaktorového ověřování**.
4. Do pole **Uživatelské jméno** zadejte název účtu Azure AD, který jste nastavili jako správce serveru, například helen@woodgroveonline.com.
5. Klikněte na tlačítko **Možnosti**.
6. Do pole **Připojení k databázi** zadejte název nesystémové databáze, kterou chcete nakonfigurovat.
7. Klikněte na **Připojit**. Dokončete proces přihlašování.
8. V **Průzkumníku objektů** rozbalte složku **Databáze**.
9. Klikněte pravým tlačítkem na uživatelskou databázi a pak klikněte na **Nový dotaz**.
10. V okně dotazu zadejte následující řádek a na panelu nástrojů klikněte na **Provést**:

    > [!NOTE]
    > `VMName` v následujícím příkazu je název virtuálního počítače, pro který jste v části Požadavky povolili identitu přiřazenou systémem.

    ```sql
    CREATE USER [VMName] FROM EXTERNAL PROVIDER
    ```

    Příkaz by se měl úspěšně provést a vytvořit uživatele pro systémem přiřazenou identitu virtuálního počítače.
11. Vymažte okno dotazu, zadejte následující řádek a na panelu nástrojů klikněte na **Provést**:

    > [!NOTE]
    > `VMName` v následujícím příkazu je název virtuálního počítače, pro který jste v části Požadavky povolili identitu přiřazenou systémem.

    ```sql
    ALTER ROLE db_datareader ADD MEMBER [VMName]
    ```

    Příkaz by se měl úspěšně provést a udělit uživateli možnost čtení celé databáze.

Kód spuštěný ve virtuálním počítači teď může získat token pomocí spravované identity přiřazené systémem a použít token k ověření na serveru.

## <a name="access-data"></a>Přístup k datům

V této části se dozvíte, jak získat přístupový token pomocí spravované identity přiřazené systémem virtuálního počítače a použít ho k volání Azure SQL. Azure SQL nativně podporuje ověřování Azure AD, takže může přímo přijímat přístupové tokeny získané pomocí spravovaných identit pro prostředky Azure. Pomocí metody s využitím **přístupového tokenu** můžete vytvořit připojení k SQL. Jedná se o součást integrace Azure SQL s Azure AD a liší se od zadávání přihlašovacích údajů v připojovacím řetězci.

Tady je příklad kódu .NET pro otevření připojení k SQL pomocí přístupového tokenu. Tento kód je potřeba spustit na virtuálním počítači, aby byl možný přístup ke koncovému bodu spravované identity přiřazené systémem virtuálního počítače. K použití metody přístupového tokenu se vyžaduje **.NET Framework 4,6** nebo vyšší nebo **.NET Core 2,2** nebo vyšší. Nahraďte hodnoty AZURE-SQL-SERVERNAME a DATABASE odpovídajícím způsobem. Všimněte si, že ID prostředku pro Azure SQL je `https://database.windows.net/` .

```csharp
using System.Net;
using System.IO;
using System.Data.SqlClient;
using System.Web.Script.Serialization;

//
// Get an access token for SQL.
//
HttpWebRequest request = (HttpWebRequest)WebRequest.Create("http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https://database.windows.net/");
request.Headers["Metadata"] = "true";
request.Method = "GET";
string accessToken = null;

try
{
    // Call managed identities for Azure resources endpoint.
    HttpWebResponse response = (HttpWebResponse)request.GetResponse();

    // Pipe response Stream to a StreamReader and extract access token.
    StreamReader streamResponse = new StreamReader(response.GetResponseStream());
    string stringResponse = streamResponse.ReadToEnd();
    JavaScriptSerializer j = new JavaScriptSerializer();
    Dictionary<string, string> list = (Dictionary<string, string>) j.Deserialize(stringResponse, typeof(Dictionary<string, string>));
    accessToken = list["access_token"];
}
catch (Exception e)
{
    string errorText = String.Format("{0} \n\n{1}", e.Message, e.InnerException != null ? e.InnerException.Message : "Acquire token failed");
}

//
// Open a connection to the server using the access token.
//
if (accessToken != null) {
    string connectionString = "Data Source=<AZURE-SQL-SERVERNAME>; Initial Catalog=<DATABASE>;";
    SqlConnection conn = new SqlConnection(connectionString);
    conn.AccessToken = accessToken;
    conn.Open();
}
```

Dalším způsobem, jak rychle otestovat kompletní nastavení bez nutnosti psát kód a nasazovat aplikaci do virtuálního počítače, je použít PowerShell.

1. Na portálu přejděte na **Virtuální počítače**, přejděte ke svému virtuálnímu počítači s Windows a v části **Přehled** klikněte na **Připojit**.
2. Zadejte své **Uživatelské jméno** a **Heslo**, které jste přidali při vytváření virtuálního počítače s Windows.
3. Teď, když jste vytvořili **připojení ke vzdálené ploše** s virtuálním počítačem, otevřete **PowerShell** ve vzdálené relaci.
4. Pomocí příkazu `Invoke-WebRequest` v PowerShellu odešlete do místního koncového bodu spravované identity žádost o přístupový token pro Azure SQL.

    ```powershell
        $response = Invoke-WebRequest -Uri 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fdatabase.windows.net%2F' -Method GET -Headers @{Metadata="true"}
    ```

    Převeďte odpověď z objektu JSON na objekt PowerShellu.

    ```powershell
    $content = $response.Content | ConvertFrom-Json
    ```

    Extrahujte z odpovědi přístupový token.

    ```powershell
    $AccessToken = $content.access_token
    ```

5. Otevřete připojení k serveru. Nezapomeňte nahradit hodnoty AZURE-SQL-SERVERNAME a DATABASE.

    ```powershell
    $SqlConnection = New-Object System.Data.SqlClient.SqlConnection
    $SqlConnection.ConnectionString = "Data Source = <AZURE-SQL-SERVERNAME>; Initial Catalog = <DATABASE>"
    $SqlConnection.AccessToken = $AccessToken
    $SqlConnection.Open()
    ```

    Dále vytvořte dotaz a odešlete ho na server. Nezapomeňte nahradit hodnotu TABLE.

    ```powershell
    $SqlCmd = New-Object System.Data.SqlClient.SqlCommand
    $SqlCmd.CommandText = "SELECT * from <TABLE>;"
    $SqlCmd.Connection = $SqlConnection
    $SqlAdapter = New-Object System.Data.SqlClient.SqlDataAdapter
    $SqlAdapter.SelectCommand = $SqlCmd
    $DataSet = New-Object System.Data.DataSet
    $SqlAdapter.Fill($DataSet)
    ```

Výsledky dotazu zobrazíte prozkoumáním hodnoty `$DataSet.Tables[0]`.

## <a name="disable"></a>Zakázat

[!INCLUDE [msi-tut-disable](../../../includes/active-directory-msi-tut-disable.md)]

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste zjistili, jak používat spravovanou identitu přiřazenou systémem pro přístup k Azure SQL Database. Další informace o Azure SQL Database najdete v těchto tématech:

> [!div class="nextstepaction"]
> [Azure SQL Database](/azure/sql-database/sql-database-technical-overview)
