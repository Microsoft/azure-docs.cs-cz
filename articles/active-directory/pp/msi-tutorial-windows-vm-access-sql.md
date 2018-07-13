---
title: Použití MSI virtuálního počítače Windows pro přístup k Azure SQL
description: Kurz vás provede procesem jeho použití Windows virtuální počítač Identity spravované služby (MSI) pro přístup k Azure SQL.
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: daveba
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/15/2017
ms.author: skwan
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 5c3e2af8c6864204a4c373ac4e1c12090389ac32
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/13/2018
ms.locfileid: "39007422"
---
# <a name="use-a-windows-vm-managed-service-identity-msi-to-access-azure-sql"></a>Použít pro přístup k Azure SQL Windows virtuální počítač Identity spravované služby (MSI)

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

V tomto kurzu se dozvíte, jak používat Identity spravované služby (MSI) pro Windows virtuální počítač (VM) pro přístup k serveru Azure SQL. Identita spravované služby je automaticky prováděna nástrojem Azure a umožňují ověření do služeb, které podporují ověřování Azure AD, aniž by bylo nutné vložit pověření do kódu. Získáte informace o těchto tématech:

> [!div class="checklist"]
> * Povolení MSI ve virtuálním počítači Windows 
> * Udělit přístup virtuálního počítače k serveru Azure SQL
> * Získání přístupového tokenu pomocí identity virtuálního počítače a použít ho k dotazování Azure SQL server

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [msi-core-prereqs](~/includes/active-directory-msi-core-prereqs-ua.md)]

[!INCLUDE [msi-tut-prereqs](~/includes/active-directory-msi-tut-prereqs.md)]

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se k webu Azure Portal na adrese [https://portal.azure.com](https://portal.azure.com).

## <a name="create-a-windows-virtual-machine-in-a-new-resource-group"></a>Vytvoření virtuálního počítače s Windows do nové skupiny prostředků

V tomto kurzu vytvoříme nový virtuální počítač s Windows.  Můžete také povolit MSI na existující virtuální počítač.

1.  Klikněte na tlačítko **vytvořit prostředek** v levém horním rohu webu Azure portal.
2.  Vyberte **Compute** a potom vyberte **Windows Server 2016 Datacenter**. 
3.  Zadejte informace o virtuálním počítači. **Uživatelské jméno** a **heslo** vytvořený, tady je přihlašovací údaje použijete k přihlášení k virtuálnímu počítači.
4.  Zvolte správné **předplatné** pro virtuální počítač v rozevírací nabídce.
5.  Chcete-li vybrat nový **skupiny prostředků** ve kterém chcete vytvořit virtuální počítač, zvolte **vytvořit nový**. Jakmile budete hotovi, klikněte na **OK**.
6.  Vyberte velikost virtuálního počítače. Pokud chcete zobrazit další velikosti, vyberte **Zobrazit všechny** nebo změňte filtr **Podporovaný typ disku**. Na stránce nastavení ponechte výchozí nastavení a klikněte na tlačítko **OK**.

    ![Text ALT obrázku](../managed-service-identity/media/msi-tutorial-windows-vm-access-arm/msi-windows-vm.png)

## <a name="enable-msi-on-your-vm"></a>Povolení MSI ve virtuálním počítači 

MSI virtuálního počítače můžete k získání přístupových tokenů z Azure AD, aniž byste museli vložit pověření do kódu. Povolení MSI Azure zjistí, chcete-li vytvořit spravovanou identitu pro váš virtuální počítač. Pod pokličkou, povolení MSI provede dvě věci: nainstaluje rozšíření MSI virtuálního počítače na virtuálním počítači a umožňuje MSI v Azure Resource Manageru.

1.  Vyberte **virtuální počítač** , že chcete povolit na MSI.  
2.  Na navigačním panelu vlevo klikněte na tlačítko **konfigurace**. 
3.  Zobrazí **identita spravované služby**. Chcete-li zaregistrovat a povolit MSI, vyberte **Ano**, pokud chcete zakázat, vyberte Ne. 
4.  Zajištění kliknutí na **Uložit** uložte konfiguraci.  
    ![Text ALT obrázku](../managed-service-identity/media/msi-tutorial-linux-vm-access-arm/msi-linux-extension.png)

5. Pokud chcete zkontrolovat a ověřit, jaká rozšíření jsou na tomto virtuálním počítači, klikněte na tlačítko **rozšíření**. Pokud je povolené MSI, pak **ManagedIdentityExtensionforWindows** se zobrazí v seznamu.

    ![Text ALT obrázku](../managed-service-identity/media/msi-tutorial-windows-vm-access-arm/msi-windows-extension.png)

## <a name="grant-your-vm-access-to-a-database-in-an-azure-sql-server"></a>Udělit váš virtuální počítač přístup k databázi na serveru Azure SQL

Nyní můžete udělit váš virtuální počítač přístup k databázi na serveru Azure SQL.  Pro tento krok můžete použít existující server SQL nebo vytvořte novou.  Pokud chcete vytvořit nový server a databáze pomocí webu Azure portal, postupujte podle to [Azure SQL quickstart](~/articles/sql-database/sql-database-get-started-portal.md). Existují také šablon rychlý start a používat rozhraní příkazového řádku Azure a Azure Powershellu v [dokumentace ke službě Azure SQL](~/articles/sql-database/index.yml).

Existují tři kroky pro poskytování váš virtuální počítač přístup k databázi:
1.  Vytvoření skupiny ve službě Azure AD a nastavte MSI virtuálního počítače jako člena skupiny.
2.  Povolte ověřování Azure AD pro SQL server.
3.  Vytvoření **uživatele** v databázi, která reprezentuje skupinu Azure AD.

> [!NOTE]
> Obvykle vytvoříte uživatele, který mapuje přímo na MSI Virtuálního počítače.  Azure SQL v současné době neumožňuje instanční objekt služby Azure AD, který představuje MSI virtuálního počítače, který má být mapována na uživatele.  Tento problém obejdete podporované nastavte MSI virtuálního počítače jako člena skupiny Azure AD pak vytvořte uživatele v databázi, která reprezentuje skupinu.


### <a name="create-a-group-in-azure-ad-and-make-the-vm-msi-a-member-of-the-group"></a>Vytvoření skupiny ve službě Azure AD a nastavte MSI virtuálního počítače jako člena skupiny

Můžete použít existující skupinu Azure AD nebo vytvořit novou zásadu pomocí prostředí PowerShell Azure AD.  

Nejdřív nainstalujte [Azure AD PowerShell](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2) modulu. Potom se přihlaste pomocí `Connect-AzureAD`a spusťte následující příkaz k vytvoření skupiny a uložte ho jako proměnnou:

```powershell
$Group = New-AzureADGroup -DisplayName "VM MSI access to SQL" -MailEnabled $false -SecurityEnabled $true -MailNickName "NotSet"
```

Výstup bude vypadat jako následující text, který také zkontroluje hodnotu proměnné:

```powershell
$Group = New-AzureADGroup -DisplayName "VM MSI access to SQL" -MailEnabled $false -SecurityEnabled $true -MailNickName "NotSet"
$Group
ObjectId                             DisplayName          Description
--------                             -----------          -----------
6de75f3c-8b2f-4bf4-b9f8-78cc60a18050 VM MSI access to SQL
```

V dalším kroku přidejte MSI Virtuálního počítače do skupiny.  Je třeba MSI **ObjectId**, který můžete získat pomocí Azure Powershellu.  Nejdřív stáhněte [prostředí Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps). Potom se přihlaste pomocí `Connect-AzureRmAccount`, a spusťte následující příkazy:
- Ujistěte se, že váš kontext relace nastavená na požadované předplatné Azure, pokud máte několik snímků.
- Seznam dostupných prostředků ve vašem předplatném Azure, v Ověřte správnou skupinu a názvy virtuálních počítačů.
- Získání vlastností virtuálního počítače MSI, pomocí příslušné hodnoty pro `<RESOURCE-GROUP>` a `<VM-NAME>`.

```powershell
Set-AzureRMContext -subscription "bdc79274-6bb9-48a8-bfd8-00c140fxxxx"
Get-AzureRmResource
$VM = Get-AzureRmVm -ResourceGroup <RESOURCE-GROUP> -Name <VM-NAME>
```

Výstup bude vypadat jako následující text, který také kontroluje ID instančního objektu objektu MSI Virtuálního počítače:
```powershell
$VM = Get-AzureRmVm -ResourceGroup DevTestGroup -Name DevTestWinVM
$VM.Identity.PrincipalId
b83305de-f496-49ca-9427-e77512f6cc64
```

Nyní přidejte MSI virtuálního počítače do skupiny.  Instanční objekt služby můžete přidat pouze do skupiny pomocí Azure AD PowerShell.  Spusťte tento příkaz:
```powershell
Add-AzureAdGroupMember -ObjectId $Group.ObjectId -RefObjectId $VM.Identity.PrincipalId
```

Pokud byste taky zkontrolovat členství ve skupině později, výstup vypadá takto:

```powershell
Add-AzureAdGroupMember -ObjectId $Group.ObjectId -RefObjectId $VM.Identity.PrincipalId
Get-AzureAdGroupMember -ObjectId $Group.ObjectId

ObjectId                             AppId                                DisplayName
--------                             -----                                -----------
b83305de-f496-49ca-9427-e77512f6cc64 0b67a6d6-6090-4ab4-b423-d6edda8e5d9f DevTestWinVM
```

### <a name="enable-azure-ad-authentication-for-the-sql-server"></a>Povolit ověřování Azure AD pro SQL server

Teď, když jste vytvořili skupinu a přidali MSI virtuálního počítače pro členství, můžete [konfigurovat ověřování Azure AD pro SQL server](~/articles/sql-database/sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-managed-instance) pomocí následujících kroků:

1.  Na webu Azure Portal, vyberte **SQL servery** v levém navigačním panelu.
2.  Klikněte na SQL server, aby byla povolená pro ověřování Azure AD.
3.  V **nastavení** části okna klikněte na tlačítko **správce Active Directory**.
4.  Na panelu příkazů klikněte na tlačítko **nastavit správce**.
5.  Vyberte uživatelský účet služby Azure AD provádí správce serveru, a klikněte na **vyberte.**
6.  Na panelu příkazů klikněte na tlačítko **uložit.**

### <a name="create-a-contained-user-in-the-database-that-represents-the-azure-ad-group"></a>Vytvořte uživatele v databázi, která reprezentuje skupinu Azure AD

Tento další krok, budete potřebovat [Microsoft SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) (SSMS). Než začnete, může být také užitečné si projít informace o integraci služby Azure AD v následujících článcích:

- [Univerzální ověřování pomocí SQL Database a SQL Data Warehouse (podpora SSMS pro vícefaktorové ověřování)](~/articles/sql-database/sql-database-ssms-mfa-authentication.md)
- [Konfigurace a Správa ověřování služby Azure Active Directory s SQL Database nebo SQL Data Warehouse](~/articles/sql-database/sql-database-aad-authentication-configure.md)

1.  Spusťte aplikaci SQL Server Management Studio.
2.  V **připojit k serveru** dialogové okno, zadejte název serveru SQL server v **název serveru** pole.
3.  V **ověřování** pole, vyberte **univerzální podporující vícefaktorové ověřování služby Active Directory –**.
4.  V **uživatelské jméno** pole, zadejte název účtu služby Azure AD, kterou jste nastavili jako správce serveru, například helen@woodgroveonline.com
5.  Klikněte na tlačítko **Možnosti**.
6.  V **připojit k databázi** pole, zadejte název jiné než systémové databáze, kterou chcete konfigurovat.
7.  Klikněte na **Připojit**.  Dokončete proces přihlašování.
8.  V **Průzkumník objektů**, rozbalte **databází** složky.
9.  Klikněte pravým tlačítkem na uživatelskou databázi a klikněte na tlačítko **nový dotaz**.
10.  V okně dotazu zadejte následující řádek a klikněte na tlačítko **Execute** na panelu nástrojů:
    
     ```
     CREATE USER [VM MSI access to SQL] FROM EXTERNAL PROVIDER
     ```
    
     Příkaz je úspěšně dokončena, vytváření omezením uživatele pro skupinu.
11.  Zrušte v okně dotazu zadejte následující řádek a klikněte na tlačítko **Execute** na panelu nástrojů:
     
     ```
     ALTER ROLE db_datareader ADD MEMBER [VM MSI access to SQL]
     ```

     Příkaz je úspěšně dokončena, udělení oprávnění ke čtení celé databáze uživatele.

Kód spuštěný ve virtuálním počítači, teď můžete získat token z MSI a použití tokenu k ověření serveru SQL.

## <a name="get-an-access-token-using-the-vm-identity-and-use-it-to-call-azure-sql"></a>Získání přístupového tokenu pomocí identity virtuálního počítače a použít jej k vyvolání Azure SQL 

Azure SQL nativně podporuje Azure AD ověřování, aby mohl přijímat přímo přístupové tokeny získat pomocí Instalační služby MSI.  Můžete použít **přístupový token** způsob vytváření připojení k SQL.  To je součástí Integrace Azure SQL s Azure AD a se liší od poskytnutí přihlašovacích údajů v připojovacím řetězci.

Tady je příklad kódu .net otevření připojení k SQL pomocí přístupového tokenu.  Tento kód musíte spustit na virtuálním počítači bude mít přístup ke koncovému bodu MSI virtuálního počítače.  **Rozhraní .net framework 4.6** nebo vyšší se vyžaduje token metody přístupu.  Nahraďte hodnoty AZURE-SQL-SERVERNAME a databáze odpovídajícím způsobem.  Poznamenejte si ID prostředku pro Azure SQL je "https://database.windows.net/".

```csharp
using System.Net;
using System.IO;
using System.Data.SqlClient;
using System.Web.Script.Serialization;

//
// Get an access token for SQL.
//
HttpWebRequest request = (HttpWebRequest)WebRequest.Create("http://localhost:50342/oauth2/token?resource=https://database.windows.net/");
request.Headers["Metadata"] = "true";
request.Method = "GET";
string accessToken = null;

try
{
    // Call MSI endpoint.
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

Další možností je rychlý způsob, jak otestovat komplexní nastavení bez nutnosti psát a nasazovat aplikace na virtuálním počítači pomocí Powershellu.

1.  Na portálu přejděte na **virtuálních počítačů** a přejděte ke svému virtuálnímu počítači Windows a v **přehled**, klikněte na tlačítko **připojit**. 
2.  Zadejte vaše **uživatelské jméno** a **heslo** pro které jste přidali po vytvoření virtuálního počítače Windows. 
3.  Teď, když jste vytvořili **připojení ke vzdálené ploše** s virtuálním počítačem, otevřete **Powershellu** ve vzdálené relaci. 
4.  Použití Powershellu `Invoke-WebRequest`, vytvořte žádost na místním koncovým bodem MSI pro získání přístupového tokenu pro Azure SQL.

    ```powershell
       $response = Invoke-WebRequest -Uri http://localhost:50342/oauth2/token -Method GET -Body @{resource="https://database.windows.net/"} -Headers @{Metadata="true"}
    ```
    
    Převeďte odpovědi z objektu JSON na objekt prostředí PowerShell. 
    
    ```powershell
    $content = $response.Content | ConvertFrom-Json
    ```

    Rozbalte přístupový token z odpovědi.
    
    ```powershell
    $AccessToken = $content.access_token
    ```

5.  Otevřete připojení k serveru SQL server. Nezapomeňte nahradit hodnoty pro AZURE-SQL-SERVERNAME a databáze.
    
    ```powershell
    $SqlConnection = New-Object System.Data.SqlClient.SqlConnection
    $SqlConnection.ConnectionString = "Data Source = <AZURE-SQL-SERVERNAME>; Initial Catalog = <DATABASE>"
    $SqlConnection.AccessToken = $AccessToken
    $SqlConnection.Open()
    ```

    V dalším kroku vytvořit a odeslat dotaz na server.  Nezapomeňte nahradit hodnotu pro tabulky.

    ```powershell
    $SqlCmd = New-Object System.Data.SqlClient.SqlCommand
    $SqlCmd.CommandText = "SELECT * from <TABLE>;"
    $SqlCmd.Connection = $SqlConnection
    $SqlAdapter = New-Object System.Data.SqlClient.SqlDataAdapter
    $SqlAdapter.SelectCommand = $SqlCmd
    $DataSet = New-Object System.Data.DataSet
    $SqlAdapter.Fill($DataSet)
    ```

Zkoumat hodnoty `$DataSet.Tables[0]` pro zobrazení výsledků dotazu.  Blahopřejeme, jste dotazovat databázi pomocí MSI virtuálního počítače a bez nutnosti poskytnout přihlašovací údaje.

## <a name="related-content"></a>Související obsah

- Přehled MSI najdete v tématu [identita spravované služby přehled](msi-overview.md).
- Další informace o [podporu Azure SQL pro ověřování Azure AD](~/articles/sql-database/sql-database-aad-authentication.md).
- Další informace o [konfigurace podpory Azure SQL pro ověřování Azure AD](~/articles/sql-database/sql-database-aad-authentication-configure.md).
- Další informace o [ověřování a přístup v systému SQL server](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/getting-started-with-database-engine-permissions).

Pomocí následujícího oddílu pro komentáře na svůj názor a Pomozte nám vylepšit a obrazce náš obsah.
