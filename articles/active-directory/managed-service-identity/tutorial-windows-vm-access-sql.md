---
title: Použití virtuálního počítače s Windows k přístupu k Azure SQL
description: Tento kurz vás provede procesem použití identity spravované služby virtuálního počítače s Windows k přístupu k Azure SQL.
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: bryanla
ms.service: active-directory
ms.component: msi
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/20/2017
ms.author: daveba
ms.openlocfilehash: b6b2985bf72d9ecb2041d51852b5a4230e11d8be
ms.sourcegitcommit: f1e6e61807634bce56a64c00447bf819438db1b8
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/24/2018
ms.locfileid: "42886049"
---
# <a name="tutorial-use-a-windows-vm-managed-service-identity-to-access-azure-sql"></a>Kurz: Použití identity spravované služby virtuálního počítače s Windows k přístupu k Azure SQL

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

V tomto kurzu se dozvíte, jak pomocí identity přiřazené systémem pro virtuální počítač s Windows získat přístup k serveru SQL Azure. Identity spravovaných služeb, které se spravují automaticky v Azure, slouží k ověření přístupu ke službám podporujícím ověřování Azure AD bez nutnosti vložení přihlašovacích údajů do kódu. Získáte informace o těchto tématech:

> [!div class="checklist"]
> * Udělení přístupu virtuálnímu počítači k serveru SQL Azure
> * Vytvoření skupiny v Azure AD a nastavení identity spravované služby virtuálního počítače jako člena této skupiny
> * Povolení ověřování Azure AD pro server SQL
> * Vytvoření uživatele v databázi reprezentujícího skupinu Azure AD
> * Získání přístupového tokenu pomocí identity virtuálního počítače a jeho použití k dotazování serveru SQL Azure

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

[!INCLUDE [msi-tut-prereqs](../../../includes/active-directory-msi-tut-prereqs.md)]

- [Přihlášení k webu Azure Portal](https://portal.azure.com)

- [Vytvoření virtuálního počítače s Windows](/azure/virtual-machines/windows/quick-create-portal)

- [Povolení identity přiřazené systémem pro váš virtuální počítač](/azure/active-directory/managed-service-identity/qs-configure-portal-windows-vm#enable-system-assigned-identity-on-an-existing-vm)

## <a name="grant-your-vm-access-to-a-database-in-an-azure-sql-server"></a>Udělení přístupu virtuálnímu počítači k databázi na serveru SQL Azure

Teď můžete virtuálnímu počítači udělit přístup k databázi na serveru SQL Azure.  Pro tento krok můžete použít stávající server SQL nebo vytvořit nový.  Pokud chcete vytvořit nový server a databázi pomocí webu Azure Portal, postupujte podle tohoto [rychlého startu k Azure SQL](https://docs.microsoft.com/azure/sql-database/sql-database-get-started-portal). Rychlé starty s využitím Azure CLI a Azure PowerShellu najdete v [dokumentaci k Azure SQL](https://docs.microsoft.com/azure/sql-database/).

Udělení přístupu virtuálnímu počítači k databázi se skládá ze tří kroků:
1.  Vytvoření skupiny v Azure AD a nastavení identity spravované služby virtuálního počítače jako člena této skupiny
2.  Povolení ověřování Azure AD pro server SQL
3.  Vytvoření **uživatele** v databázi reprezentujícího skupinu Azure AD

> [!NOTE]
> Za normálních okolností byste vytvořili uživatele, který se mapuje přímo na identitu spravované služby virtuálního počítače.  Azure SQL v současné době neumožňuje mapování instančního objektu Azure AD, který představuje identitu spravované služby virtuálního počítače, na uživatele.  Podporovaným alternativním řešením je nastavit identitu spravované služby virtuálního počítače jako člena skupiny Azure AD a potom v databázi vytvořit uživatele, který tuto skupinu představuje.


## <a name="create-a-group-in-azure-ad-and-make-the-vm-managed-service-identity-a-member-of-the-group"></a>Vytvoření skupiny v Azure AD a nastavení identity spravované služby virtuálního počítače jako člena této skupiny

Můžete vytvořit stávající skupinu Azure AD nebo pomocí Azure AD PowerShellu vytvořit novou.  

Nejprve nainstalujte modul [Azure AD PowerShell](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2). Pak se přihlaste pomocí příkazu `Connect-AzureAD` a spuštěním následujícího příkazu vytvořte skupinu a uložte ji do proměnné:

```powershell
$Group = New-AzureADGroup -DisplayName "VM Managed Service Identity access to SQL" -MailEnabled $false -SecurityEnabled $true -MailNickName "NotSet"
```

Výstup bude vypadat jako v následujícím příkladu, který také ukazuje hodnotu proměnné:

```powershell
$Group = New-AzureADGroup -DisplayName "VM Managed Service Identity access to SQL" -MailEnabled $false -SecurityEnabled $true -MailNickName "NotSet"
$Group
ObjectId                             DisplayName          Description
--------                             -----------          -----------
6de75f3c-8b2f-4bf4-b9f8-78cc60a18050 VM Managed Service Identity access to SQL
```

V dalším kroku přidáte identitu spravované služby virtuálního počítače do této skupiny.  Potřebujete **ID objektu** identity spravované služby, které můžete získat pomocí Azure PowerShellu.  Nejprve stáhněte [Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps). Pak se přihlaste pomocí příkazu `Connect-AzureRmAccount` a spusťte následující příkazy, které:
- Zajistí nastavení kontextu relace na požadované předplatné Azure, pokud jich máte několik.
- Vypíše seznam dostupných prostředků ve vašem předplatném Azure a ověří správnost názvů skupiny prostředků a virtuálního počítače.
- Získají vlastnosti identity spravované služby virtuálního počítače s použitím odpovídajících hodnot pro `<RESOURCE-GROUP>` a `<VM-NAME>`.

```powershell
Set-AzureRMContext -subscription "bdc79274-6bb9-48a8-bfd8-00c140fxxxx"
Get-AzureRmResource
$VM = Get-AzureRmVm -ResourceGroup <RESOURCE-GROUP> -Name <VM-NAME>
```

Výstup bude vypadat jako v následujícím příkladu, který také ukazuje ID instančního objektu identity spravované služby virtuálního počítače:
```powershell
$VM = Get-AzureRmVm -ResourceGroup DevTestGroup -Name DevTestWinVM
$VM.Identity.PrincipalId
b83305de-f496-49ca-9427-e77512f6cc64
```

Teď přidejte identitu spravované služby virtuálního počítače do skupiny.  Instanční objekt můžete do skupiny přidat pouze pomocí Azure AD PowerShellu.  Spusťte tento příkaz:
```powershell
Add-AzureAdGroupMember -ObjectId $Group.ObjectId -RefObjectId $VM.Identity.PrincipalId
```

Pokud pak prozkoumáte také členství ve skupině, bude výstup vypadat následovně:

```powershell
Add-AzureAdGroupMember -ObjectId $Group.ObjectId -RefObjectId $VM.Identity.PrincipalId
Get-AzureAdGroupMember -ObjectId $Group.ObjectId

ObjectId                             AppId                                DisplayName
--------                             -----                                -----------
b83305de-f496-49ca-9427-e77512f6cc64 0b67a6d6-6090-4ab4-b423-d6edda8e5d9f DevTestWinVM
```

## <a name="enable-azure-ad-authentication-for-the-sql-server"></a>Povolení ověřování Azure AD pro server SQL

Vytvořili jste skupinu a přidali do ní identitu spravované služby virtuálního počítače jako člena. Teď můžete pomocí následujících kroků [nakonfigurovat ověřování Azure AD pro server SQL](/azure/sql-database/sql-database-aad-authentication-configure#provision-an-azure-active-directory-administrator-for-your-azure-sql-server):

1.  Na levém navigačním panelu na webu Azure Portal vyberte **Servery SQL**.
2.  Kliknutím na server SQL u něj povolte ověřování Azure AD.
3.  V části okna **Nastavení** klikněte na **Správce Active Directory**.
4.  Na panelu příkazů klikněte na **Nastavit správce**.
5.  Vyberte uživatelský účet Azure AD, který se má stát správcem serveru, a klikněte na **Vybrat**.
6.  Na panelu příkazů klikněte na **Uložit**.

## <a name="create-a-contained-user-in-the-database-that-represents-the-azure-ad-group"></a>Vytvoření uživatele v databázi reprezentujícího skupinu Azure AD

Pro tento další krok budete potřebovat aplikaci [Microsoft SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) (SSMS). Než začnete, můžete si také přečíst následující články se základními informacemi o integraci Azure AD:

- [Univerzální ověřování s využitím služeb SQL Database a SQL Data Warehouse (podpora SSMS pro MFA)](/azure/sql-database/sql-database-ssms-mfa-authentication)
- [Konfigurace a správa ověřování služby Azure Active Directory s využitím služby SQL Database nebo SQL Data Warehouse](/azure/sql-database/sql-database-aad-authentication-configure)

1.  Spusťte aplikaci SQL Server Management Studio.
2.  V dialogovém okně **Připojení k serveru** zadejte do pole **Název serveru** název vašeho serveru SQL.
3.  V poli **Ověřování** vyberte **Active Directory – univerzální s podporou vícefaktorového ověřování**.
4.  Do pole **Uživatelské jméno** zadejte název účtu Azure AD, který jste nastavili jako správce serveru, například helen@woodgroveonline.com.
5.  Klikněte na tlačítko **Možnosti**.
6.  Do pole **Připojení k databázi** zadejte název nesystémové databáze, kterou chcete nakonfigurovat.
7.  Klikněte na **Připojit**.  Dokončete proces přihlašování.
8.  V **Průzkumníku objektů** rozbalte složku **Databáze**.
9.  Klikněte pravým tlačítkem na uživatelskou databázi a pak klikněte na **Nový dotaz**.
10.  V okně dotazu zadejte následující řádek a na panelu nástrojů klikněte na **Provést**:
    
     ```
     CREATE USER [VM Managed Service Identity access to SQL] FROM EXTERNAL PROVIDER
     ```
    
     Příkaz by se měl úspěšně provést a vytvořit uživatele pro skupinu.
11.  Vymažte okno dotazu, zadejte následující řádek a na panelu nástrojů klikněte na **Provést**:
     
     ```
     ALTER ROLE db_datareader ADD MEMBER [VM Managed Service Identity access to SQL]
     ```

     Příkaz by se měl úspěšně provést a udělit uživateli možnost čtení celé databáze.

Kód spuštěný na virtuálním počítači teď může z identity spravované služby získat token a pomocí něj provést ověření na serveru SQL.

## <a name="get-an-access-token-using-the-vm-identity-and-use-it-to-call-azure-sql"></a>Získání přístupového tokenu pomocí identity virtuálního počítače a jeho použití k volání Azure SQL 

Azure SQL nativně podporuje ověřování Azure AD, takže může přímo přijímat přístupové tokeny získané pomocí identity spravované služby.  Pomocí metody s využitím **přístupového tokenu** můžete vytvořit připojení k SQL.  Jedná se o součást integrace Azure SQL s Azure AD a liší se od zadávání přihlašovacích údajů v připojovacím řetězci.

Tady je příklad kódu .NET, který otevře připojení k SQL pomocí přístupového tokenu.  Tento kód je potřeba spustit na virtuálním počítači, aby byl možný přístup ke koncovému bodu identity spravované služby virtuálního počítače.  Při použití metody s využitím přístupového tokenu se vyžaduje **.NET Framework 4.6** nebo novější.  Nahraďte hodnoty AZURE-SQL-SERVERNAME a DATABASE odpovídajícím způsobem.  Všimněte si, že ID prostředku pro Azure SQL je https://database.windows.net/.

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
    // Call Managed Service Identity endpoint.
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
4.  Pomocí příkazu `Invoke-WebRequest` v PowerShellu odešlete do místního koncového bodu identity spravované služby žádost o přístupový token pro Azure SQL.

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

Výsledky dotazu zobrazíte prozkoumáním hodnoty `$DataSet.Tables[0]`.  Blahopřejeme, pomocí identity spravované služby virtuálního počítače jste odeslali dotaz na databázi, aniž byste museli zadávat přihlašovací údaje.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se dozvěděli, jak vytvořit Identitu spravované služby pro přístup k serveru SQL Azure.  Další informace o Azure SQL Serveru najdete tady:

> [!div class="nextstepaction"]
>[Služba Azure SQL Database](/azure/sql-database/sql-database-technical-overview)
