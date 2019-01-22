---
title: Použití spravované identity přiřazené systémem na virtuálním počítači s Windows pro přístup k Azure SQL
description: Tento kurz vás postupně provede používáním spravované identity přiřazené systémem na virtuálním počítači s Windows pro přístup k Azure SQL.
services: active-directory
documentationcenter: ''
author: daveba
manager: daveba
editor: bryanla
ms.service: active-directory
ms.component: msi
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/07/2018
ms.author: daveba
ms.openlocfilehash: 938bbd62ba6e27359b909eda3d2dbc2b91ae539b
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/22/2019
ms.locfileid: "54436514"
---
# <a name="tutorial-use-a-windows-vm-system-assigned-managed-identity-to-access-azure-sql"></a>Kurz: Použití spravované identity systém přiřadil virtuálního počítače Windows pro přístup k Azure SQL

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

V tomto kurzu se dozvíte, jak pomocí identity přiřazené systémem pro virtuální počítač s Windows získat přístup k serveru SQL Azure. Identity spravovaných služeb, které se spravují automaticky v Azure, slouží k ověření přístupu ke službám podporujícím ověřování Azure AD bez nutnosti vložení přihlašovacích údajů do kódu. Získáte informace o těchto tématech:

> [!div class="checklist"]
> * Udělení přístupu virtuálnímu počítači k serveru SQL Azure
> * Povolení ověřování Azure AD pro server SQL
> * Vytvoření uživatele v databázi reprezentujícího systémem přiřazenou identitu virtuálního počítače
> * Získání přístupového tokenu pomocí identity virtuálního počítače a jeho použití k dotazování serveru SQL Azure

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [msi-tut-prereqs](../../../includes/active-directory-msi-tut-prereqs.md)]

## <a name="grant-your-vm-access-to-a-database-in-an-azure-sql-server"></a>Udělení přístupu virtuálnímu počítači k databázi na serveru SQL Azure

Pokud chcete virtuálnímu počítači udělit přístup k databázi na serveru SQL Azure, můžete použít existující server SQL nebo vytvořit nový.  Pokud chcete vytvořit nový server a databázi pomocí webu Azure Portal, postupujte podle tohoto [rychlého startu k Azure SQL](https://docs.microsoft.com/azure/sql-database/sql-database-get-started-portal). Rychlé starty s využitím Azure CLI a Azure PowerShellu najdete v [dokumentaci k Azure SQL](https://docs.microsoft.com/azure/sql-database/).

Udělení přístupu virtuálnímu počítači k databázi se skládá ze dvou kroků:

1.  Povolení ověřování Azure AD pro server SQL
2.  Vytvoření **uživatele** v databázi reprezentujícího systémem přiřazenou identitu virtuálního počítače

## <a name="enable-azure-ad-authentication-for-the-sql-server"></a>Povolení ověřování Azure AD pro server SQL

[Nakonfigurujte pro server SQL ověřování Azure AD](/azure/sql-database/sql-database-aad-authentication-configure#provision-an-azure-active-directory-administrator-for-your-azure-sql-server) pomocí následujících kroků:

1.  Na levém navigačním panelu na webu Azure Portal vyberte **Servery SQL**.
2.  Kliknutím na server SQL u něj povolte ověřování Azure AD.
3.  V části okna **Nastavení** klikněte na **Správce Active Directory**.
4.  Na panelu příkazů klikněte na **Nastavit správce**.
5.  Vyberte uživatelský účet Azure AD, který se má stát správcem serveru, a klikněte na **Vybrat**.
6.  Na panelu příkazů klikněte na **Uložit**.

## <a name="create-a-contained-user-in-the-database-that-represents-the-vms-system-assigned-identity"></a>Vytvoření uživatele v databázi reprezentujícího systémem přiřazenou identitu virtuálního počítače

Pro tento další krok budete potřebovat aplikaci [Microsoft SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) (SSMS). Než začnete, můžete si také přečíst následující články se základními informacemi o integraci Azure AD:

- [Univerzální ověřování s využitím služeb SQL Database a SQL Data Warehouse (podpora SSMS pro MFA)](/azure/sql-database/sql-database-ssms-mfa-authentication)
- [Konfigurace a správa ověřování služby Azure Active Directory s využitím služby SQL Database nebo SQL Data Warehouse](/azure/sql-database/sql-database-aad-authentication-configure)

SQL DB vyžaduje jedinečné zobrazované názvy AAD. Díky tomu účty AAD, jako jsou uživatelé, skupiny nebo instanční objekty (aplikace) a názvy virtuálních počítačů, které jsou povolené pro spravovanou identitu musí být definovány jedinečně v adresáři AAD. Pokud jde o zobrazovaného jména. SQL DB zkontroluje AAD zobrazovaný název při vytváření jazyka T-SQL těchto uživatelů a pokud není jedinečný, příkaz se nezdaří, pokud chcete zadat jedinečný název zobrazovaný AAD pro daný účet.

1.  Spusťte aplikaci SQL Server Management Studio.
2.  V dialogovém okně **Připojení k serveru** zadejte do pole **Název serveru** název vašeho serveru SQL.
3.  V poli **Ověřování** vyberte **Active Directory – univerzální s podporou vícefaktorového ověřování**.
4.  Do pole **Uživatelské jméno** zadejte název účtu Azure AD, který jste nastavili jako správce serveru, například helen@woodgroveonline.com.
5.  Klikněte na tlačítko **Možnosti**.
6.  Do pole **Připojení k databázi** zadejte název nesystémové databáze, kterou chcete nakonfigurovat.
7.  Klikněte na **Připojit**.  Dokončete proces přihlašování.
8.  V **Průzkumníku objektů** rozbalte složku **Databáze**.
9.  Klikněte pravým tlačítkem na uživatelskou databázi a pak klikněte na **Nový dotaz**.
10. V okně dotazu zadejte následující řádek a na panelu nástrojů klikněte na **Provést**:

    > [!NOTE]
    > `VMName` v následujícím příkazu je název virtuálního počítače, pro který jste v části Požadavky povolili identitu přiřazenou systémem.
    
     ```
     CREATE USER [VMName] FROM EXTERNAL PROVIDER
     ```
    
     Příkaz by se měl úspěšně provést a vytvořit uživatele pro systémem přiřazenou identitu virtuálního počítače.
11.  Vymažte okno dotazu, zadejte následující řádek a na panelu nástrojů klikněte na **Provést**:

    > [!NOTE]
    > `VMName` v následujícím příkazu je název virtuálního počítače, pro který jste v části Požadavky povolili identitu přiřazenou systémem.
     
     ```
     ALTER ROLE db_datareader ADD MEMBER [VMName]
     ```

     Příkaz by se měl úspěšně provést a udělit uživateli možnost čtení celé databáze.

Kód spuštěný na virtuálním počítači teď může ze spravované identity přiřazené systémem získat token a pomocí něj provést ověření na serveru SQL.

## <a name="get-an-access-token-using-the-vms-system-assigned-managed-identity-and-use-it-to-call-azure-sql"></a>Získání přístupového tokenu pomocí spravované identity přiřazené systémem virtuálního počítače a jeho použití k volání Azure SQL 

Azure SQL nativně podporuje ověřování Azure AD, takže může přímo přijímat přístupové tokeny získané pomocí spravovaných identit pro prostředky Azure.  Pomocí metody s využitím **přístupového tokenu** můžete vytvořit připojení k SQL.  Jedná se o součást integrace Azure SQL s Azure AD a liší se od zadávání přihlašovacích údajů v připojovacím řetězci.

Tady je příklad kódu .NET, který otevře připojení k SQL pomocí přístupového tokenu.  Tento kód je potřeba spustit na virtuálním počítači, aby byl možný přístup ke koncovému bodu spravované identity přiřazené systémem virtuálního počítače.  Při použití metody s využitím přístupového tokenu se vyžaduje **.NET Framework 4.6** nebo novější.  Nahraďte hodnoty AZURE-SQL-SERVERNAME a DATABASE odpovídajícím způsobem.  Všimněte si, že ID prostředku pro Azure SQL je https://database.windows.net/.

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
// Open a connection to the SQL server using the access token.
//
if (accessToken != null) {
    string connectionString = "Data Source=<AZURE-SQL-SERVERNAME>; Initial Catalog=<DATABASE>;";
    SqlConnection conn = new SqlConnection(connectionString);
    conn.AccessToken = accessToken;
    conn.Open();
}
```

Dalším způsobem, jak rychle otestovat kompletní nastavení bez nutnosti psát kód a nasazovat aplikaci do virtuálního počítače, je použít PowerShell.

1.  Na portálu přejděte na **Virtuální počítače**, přejděte ke svému virtuálnímu počítači s Windows a v části **Přehled** klikněte na **Připojit**. 
2.  Zadejte své **uživatelské jméno** a **heslo**, které jste přidali při vytváření virtuálního počítače s Windows. 
3.  Teď, když jste vytvořili **připojení ke vzdálené ploše** s virtuálním počítačem, otevřete ve vzdálené relaci **PowerShell**. 
4.  Pomocí příkazu `Invoke-WebRequest` v PowerShellu odešlete do místního koncového bodu spravované identity žádost o přístupový token pro Azure SQL.

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

5.  Otevřete připojení k serveru SQL. Nezapomeňte nahradit hodnoty AZURE-SQL-SERVERNAME a DATABASE.
    
    ```powershell
    $SqlConnection = New-Object System.Data.SqlClient.SqlConnection
    $SqlConnection.ConnectionString = "Data Source = <AZURE-SQL-SERVERNAME>; Initial Catalog = <DATABASE>"
    $SqlConnection.AccessToken = $AccessToken
    $SqlConnection.Open()
    ```

    Dále vytvořte dotaz a odešlete ho na server.  Nezapomeňte nahradit hodnotu TABLE.

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

## <a name="next-steps"></a>Další postup

V tomto kurzu jste zjistili, jak využít spravovanou identitu přiřazenou systémem pro přístup k serveru Azure SQL.  Další informace o Azure SQL Serveru najdete tady:

> [!div class="nextstepaction"]
>[Služba Azure SQL Database](/azure/sql-database/sql-database-technical-overview)
