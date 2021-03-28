---
title: ActiveDirectoryInteractive se připojuje k SQL
description: Příklad kódu C# s vysvětlením pro připojení k Azure SQL Database pomocí režimu SqlAuthenticationMethod. ActiveDirectoryInteractive.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: active directory, has-adal-ref, sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: GithubMirek
ms.author: MirekS
ms.reviewer: vanto
ms.date: 04/23/2020
ms.openlocfilehash: e2fa09ac8609310d4579590214bc25e5d7ee309f
ms.sourcegitcommit: c8b50a8aa8d9596ee3d4f3905bde94c984fc8aa2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2021
ms.locfileid: "105641563"
---
# <a name="connect-to-azure-sql-database-with-azure-ad-multi-factor-authentication"></a>Připojení k Azure SQL Database s využitím Azure AD Multi-Factor Authentication
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Tento článek poskytuje program v jazyce C#, který se připojuje k Azure SQL Database. Program používá ověřování pomocí interaktivního režimu, které podporuje [Multi-Factor Authentication Azure AD](../../active-directory/authentication/concept-mfa-howitworks.md).

Další informace o podpoře Multi-Factor Authentication nástrojů SQL najdete [v tématu podpora Azure Active Directory v nástrojích pro SQL Server Data Tools (SSDT)](/sql/ssdt/azure-active-directory).

## <a name="multi-factor-authentication-for-azure-sql-database"></a>Multi-Factor Authentication pro Azure SQL Database

Počínaje .NET Framework verze 4.7.2 [`SqlAuthenticationMethod`](/dotnet/api/system.data.sqlclient.sqlauthenticationmethod) má výčet novou hodnotu: `ActiveDirectoryInteractive` . V klientském programu v jazyce C# hodnota výčtu přesměruje systém na použití interaktivního režimu Azure Active Directory (Azure AD), který podporuje Multi-Factor Authentication pro připojení Azure SQL Database. Uživatel, který spouští program, uvidí následující dialogová okna:

* Dialogové okno, ve kterém se zobrazuje uživatelské jméno Azure AD a požádá o zadání hesla uživatele.

   Pokud je doména uživatele federované pomocí Azure AD, toto dialogové okno se nezobrazí, protože není potřeba žádné heslo.

   Pokud zásada služby Azure AD ukládá Multi-Factor Authentication na uživatele, zobrazí se další dvě dialogová okna.

* Když uživatel poprvé projde Multi-Factor Authentication, zobrazí se dialogové okno s výzvou k zadání mobilního telefonního čísla, na které se mají odesílat textové zprávy. Každá zpráva poskytuje *ověřovací kód* , který musí uživatel zadat v dalším dialogovém okně.

* Dialogové okno s výzvou k zadání ověřovacího kódu Multi-Factor Authentication, který systém odeslal na mobilní telefon.

Informace o tom, jak nakonfigurovat službu Azure AD tak, aby vyžadovala Multi-Factor Authentication, najdete [v tématu Začínáme se službou Azure ad Multi-Factor Authentication v cloudu](../../active-directory/authentication/howto-mfa-getstarted.md).

Snímky obrazovky těchto dialogových oken najdete v tématu [Konfigurace služby Multi-Factor Authentication pro SQL Server Management Studio a Azure AD](authentication-mfa-ssms-configure.md).

> [!TIP]
> .NET Framework rozhraní API můžete vyhledat pomocí [stránky nástroje prohlížeče rozhraní .NET API](/dotnet/api/).
>
> Můžete také hledat přímo s [ &lt; &gt; parametrem volitelného termínu = hodnota vyhledávání](/dotnet/api/?term=SqlAuthenticationMethod).

## <a name="configure-your-c-application-in-the-azure-portal"></a>Konfigurace aplikace v jazyce C# v Azure Portal

Než začnete, měli byste mít vytvořený a dostupný [logický SQL Server](logical-servers.md) .

### <a name="register-your-app-and-set-permissions"></a>Registrace aplikace a nastavení oprávnění

Pokud chcete používat ověřování Azure AD, musí se program v C# zaregistrovat jako aplikace Azure AD. K registraci aplikace musíte být buď správce Azure AD, nebo uživatel, kterému byla přiřazena role *vývojář aplikací* Azure AD. Další informace o přiřazování rolí najdete v tématu [přiřazení rolí správců a uživatelů bez oprávnění správce k Azure Active Directory](../../active-directory/fundamentals/active-directory-users-assign-role-azure-portal.md).

Dokončení registrace aplikace vygeneruje a zobrazí **ID aplikace**. Váš program musí zahrnovat toto ID pro připojení.

Registrace a nastavení potřebných oprávnění pro vaši aplikaci:

1. V Azure Portal vyberte **Azure Active Directory**  >  **Registrace aplikací**  >  **nové registrace**.

    ![Registrace aplikace](./media/active-directory-interactive-connect-azure-sql-db/image1.png)

    Po vytvoření registrace aplikace se vygeneruje a zobrazí hodnota **ID aplikace** .

    ![ID aplikace zobrazené](./media/active-directory-interactive-connect-azure-sql-db/image2.png)

2. Vyberte **oprávnění rozhraní API**  >  **Přidat oprávnění**.

    ![Nastavení oprávnění pro registrovanou aplikaci](./media/active-directory-interactive-connect-azure-sql-db/sshot-registered-app-settings-required-permissions-add-api-access-c32.png)

3. Vyberte **rozhraní API moje organizace používá** > typ **Azure SQL Database** do vyhledávacího > a vyberte **Azure SQL Database**.

    ![Přidání přístupu k rozhraní API pro Azure SQL Database](./media/active-directory-interactive-connect-azure-sql-db/sshot-registered-app-settings-required-permissions-add-api-access-Azure-sql-db-d11.png)

4. Vyberte **delegovaná oprávnění**  >  **user_impersonation**  >  **Přidat oprávnění**.

    ![Delegovat oprávnění k rozhraní API pro Azure SQL Database](./media/active-directory-interactive-connect-azure-sql-db/sshot-add-api-access-azure-sql-db-delegated-permissions-checkbox-e14.png)

### <a name="set-an-azure-ad-admin-for-your-server"></a>Nastavení správce Azure AD pro váš server

Pro spuštění programu v jazyce C# musí správce [logických serverů SQL Server](logical-servers.md) přiřadit správce Azure AD pro váš server.

Na stránce **SQL Server** vyberte **Správce služby Active Directory**  >  **nastavit správce**.

Další informace o správcích a uživatelích Azure AD pro Azure SQL Database najdete v tématu snímky obrazovky v tématu [Konfigurace a Správa ověřování Azure Active Directory pomocí SQL Database](authentication-aad-configure.md#provision-azure-ad-admin-sql-database).

### <a name="add-a-non-admin-user-to-a-specific-database-optional"></a>Přidat uživatele bez role správce do konkrétní databáze (volitelné)

Správce Azure AD pro [logický SQL Server](logical-servers.md) může spustit ukázkový program v jazyce C#. Uživatel Azure AD může program spustit, pokud se nachází v databázi. Správce Azure AD SQL nebo uživatel Azure AD, který již v databázi existuje a má `ALTER ANY USER` oprávnění k databázi může přidat uživatele.

Do databáze nástroje můžete přidat uživatele pomocí [`Create User`](/sql/t-sql/statements/create-user-transact-sql) příkazu SQL. Příklad: `CREATE USER [<username>] FROM EXTERNAL PROVIDER`.

Další informace najdete v tématu [použití Azure Active Directory ověřování pro ověřování pomocí SQL Database, spravované instance nebo Azure synapse Analytics](authentication-aad-overview.md).

## <a name="new-authentication-enum-value"></a>Nová hodnota výčtu ověřování

Příklad jazyka C# spoléhá na [`System.Data.SqlClient`](/dotnet/api/system.data.sqlclient) obor názvů. Zvláštní zájem pro Multi-Factor Authentication je výčet `SqlAuthenticationMethod` , který má následující hodnoty:

* `SqlAuthenticationMethod.ActiveDirectoryInteractive`

   K implementaci Multi-Factor Authentication použijte tuto hodnotu s uživatelským jménem Azure AD. Tato hodnota je zaměřená na současný článek. Vytváří interaktivní prostředí tím, že zobrazuje dialogová okna pro heslo uživatele a potom pro Multi-Factor Authentication ověřování, pokud je pro tohoto uživatele Multi-Factor Authentication uloženo. Tato hodnota je k dispozici počínaje verzí .NET Framework 4.7.2.

* `SqlAuthenticationMethod.ActiveDirectoryIntegrated`

  Tuto hodnotu použijte pro *federovaný* účet. Pro federovaný účet je uživatelské jméno známo pro doménu systému Windows. Tato metoda ověřování nepodporuje Multi-Factor Authentication.

* `SqlAuthenticationMethod.ActiveDirectoryPassword`

  Tuto hodnotu použijte pro ověřování, které vyžaduje uživatelské jméno a heslo pro Azure AD. Ověřování provádí Azure SQL Database. Tato metoda nepodporuje Multi-Factor Authentication.

> [!NOTE]
> Pokud používáte .NET Core, budete chtít použít obor názvů [Microsoft. data. SqlClient](/dotnet/api/microsoft.data.sqlclient) . Další informace najdete na následujícím [blogu](https://devblogs.microsoft.com/dotnet/introducing-the-new-microsoftdatasqlclient/).

## <a name="set-c-parameter-values-from-the-azure-portal"></a>Nastavení hodnot parametrů C# z Azure Portal

Aby bylo možné program v jazyce C# úspěšně spustit, je třeba přiřadit ke statickým polím správné hodnoty. Zde jsou uvedená pole s příklady hodnot. Zobrazí se také Azure Portal umístění, kde můžete získat potřebné hodnoty.

| Statický název pole | Příklad hodnoty | Místo v Azure Portal |
| :---------------- | :------------ | :-------------------- |
| Az_SQLDB_svrName | "my-sqldb-svr.database.windows.net" | **SQL servery**  >  **Filtrovat podle názvu** |
| AzureAD_UserID | "auser \@ ABC.onmicrosoft.com" | **Azure Active Directory**  >  **Uživatel**  >  **Nový uživatel typu Host** |
| Initial_DatabaseName | "myDatabase" | **SQL servery**  >  **Databáze SQL** |
| ClientApplicationID | "a94f9c62-97fe-4d19-b06d-111111111111" | **Azure Active Directory**  >  **Registrace aplikací**  >  **Hledat podle názvu**  >  **ID aplikace** |
| RedirectUri | nový identifikátor URI (" https://mywebserver.com/ ") | **Azure Active Directory**  >  **Registrace aplikací**  >  **Hledat podle názvu**  >  *[Registrace vaší aplikace]*  >  **Nastavení**  >  **RedirectURIs**<br /><br />Pro tento článek je libovolná platná hodnota pro RedirectUri, protože se tady nepoužívá. |
| &nbsp; | &nbsp; | &nbsp; |

## <a name="verify-with-sql-server-management-studio"></a>Ověřit pomocí SQL Server Management Studio

Před spuštěním programu v jazyce C# je vhodné ověřit, zda je nastavení a konfigurace správné v SQL Server Management Studio (SSMS). Jakékoli selhání programu v jazyce C# lze poté zúžit na zdrojový kód.

### <a name="verify-server-level-firewall-ip-addresses"></a>Ověření IP adres brány firewall na úrovni serveru

Spusťte SSMS ze stejného počítače, ve stejném budova, kde plánujete spustit program v jazyce C#. Pro tento test je v pořádku libovolný režim **ověřování** . Pokud se zobrazí informace, že server nepřijímá vaši IP adresu, požádejte o nápovědu [pravidla brány firewall na úrovni serveru a databáze](firewall-configure.md) .

### <a name="verify-azure-active-directory-multi-factor-authentication"></a>Ověřit Azure Active Directory Multi-Factor Authentication

Spusťte znovu SSMS, tentokrát s **ověřováním** nastaveným na **Azure Active Directory – Universal s MFA**. Tato možnost vyžaduje SSMS verze 17,5 nebo novější.

Další informace najdete v tématu [konfigurace Multi-Factor Authentication pro SSMS a Azure AD](authentication-mfa-ssms-configure.md).

> [!NOTE]
> Pokud jste uživatelem typu Host v databázi, musíte zadat také název domény služby Azure AD pro databázi: vyberte **Možnosti**  >  **název domény služby AD nebo ID tenanta**. Chcete-li najít název domény v Azure Portal, vyberte možnost **Azure Active Directory**  >  **názvy vlastních domén**. V ukázkovém programu v jazyce C# není nutné zadat název domény.

## <a name="c-code-example"></a>Příklad kódu C#

> [!NOTE]
> Pokud používáte .NET Core, budete chtít použít obor názvů [Microsoft. data. SqlClient](/dotnet/api/microsoft.data.sqlclient) . Další informace najdete na následujícím [blogu](https://devblogs.microsoft.com/dotnet/introducing-the-new-microsoftdatasqlclient/).

Vzorový program v jazyce C# spoléhá na sestavení knihovny DLL [*Microsoft. IdentityModel. clients. Active Directory*](/dotnet/api/microsoft.identitymodel.clients.activedirectory) .

Chcete-li nainstalovat tento balíček, v aplikaci Visual Studio vyberte možnost **projekt**  >  **Spravovat balíčky NuGet**. Vyhledejte a nainstalujte **Microsoft. IdentityModel. clients. Active**.

Toto je příklad zdrojového kódu jazyka C#.

```csharp

using System;

// Reference to Azure AD authentication assembly
using Microsoft.IdentityModel.Clients.ActiveDirectory;

using DA = System.Data;
using SC = System.Data.SqlClient;
using AD = Microsoft.IdentityModel.Clients.ActiveDirectory;
using TX = System.Text;
using TT = System.Threading.Tasks;

namespace ADInteractive5
{
    class Program
    {
        // ASSIGN YOUR VALUES TO THESE STATIC FIELDS !!
        static public string Az_SQLDB_svrName = "<Your server>";
        static public string AzureAD_UserID = "<Your User ID>";
        static public string Initial_DatabaseName = "<Your Database>";
        // Some scenarios do not need values for the following two fields:
        static public readonly string ClientApplicationID = "<Your App ID>";
        static public readonly Uri RedirectUri = new Uri("<Your URI>");

        public static void Main(string[] args)
        {
            var provider = new ActiveDirectoryAuthProvider();

            SC.SqlAuthenticationProvider.SetProvider(
                SC.SqlAuthenticationMethod.ActiveDirectoryInteractive,
                //SC.SqlAuthenticationMethod.ActiveDirectoryIntegrated,  // Alternatives.
                //SC.SqlAuthenticationMethod.ActiveDirectoryPassword,
                provider);

            Program.Connection();
        }

        public static void Connection()
        {
            SC.SqlConnectionStringBuilder builder = new SC.SqlConnectionStringBuilder();

            // Program._  static values that you set earlier.
            builder["Data Source"] = Program.Az_SQLDB_svrName;
            builder.UserID = Program.AzureAD_UserID;
            builder["Initial Catalog"] = Program.Initial_DatabaseName;

            // This "Password" is not used with .ActiveDirectoryInteractive.
            //builder["Password"] = "<YOUR PASSWORD HERE>";

            builder["Connect Timeout"] = 15;
            builder["TrustServerCertificate"] = true;
            builder.Pooling = false;

            // Assigned enum value must match the enum given to .SetProvider().
            builder.Authentication = SC.SqlAuthenticationMethod.ActiveDirectoryInteractive;
            SC.SqlConnection sqlConnection = new SC.SqlConnection(builder.ConnectionString);

            SC.SqlCommand cmd = new SC.SqlCommand(
                "SELECT '******** MY QUERY RAN SUCCESSFULLY!! ********';",
                sqlConnection);

            try
            {
                sqlConnection.Open();
                if (sqlConnection.State == DA.ConnectionState.Open)
                {
                    var rdr = cmd.ExecuteReader();
                    var msg = new TX.StringBuilder();
                    while (rdr.Read())
                    {
                        msg.AppendLine(rdr.GetString(0));
                    }
                    Console.WriteLine(msg.ToString());
                    Console.WriteLine(":Success");
                }
                else
                {
                    Console.WriteLine(":Failed");
                }
                sqlConnection.Close();
            }
            catch (Exception ex)
            {
                Console.ForegroundColor = ConsoleColor.Red;
                Console.WriteLine("Connection failed with the following exception...");
                Console.WriteLine(ex.ToString());
                Console.ResetColor();
            }
        }
    } // EOClass Program.

    /// <summary>
    /// SqlAuthenticationProvider - Is a public class that defines 3 different Azure AD
    /// authentication methods.  The methods are supported in the new .NET 4.7.2.
    ///  .
    /// 1. Interactive,  2. Integrated,  3. Password
    ///  .
    /// All 3 authentication methods are based on the Azure
    /// Active Directory Authentication Library (ADAL) managed library.
    /// </summary>
    public class ActiveDirectoryAuthProvider : SC.SqlAuthenticationProvider
    {
        // Program._ more static values that you set!
        private readonly string _clientId = Program.ClientApplicationID;
        private readonly Uri _redirectUri = Program.RedirectUri;

        public override async TT.Task<SC.SqlAuthenticationToken>
            AcquireTokenAsync(SC.SqlAuthenticationParameters parameters)
        {
            AD.AuthenticationContext authContext =
                new AD.AuthenticationContext(parameters.Authority);
            authContext.CorrelationId = parameters.ConnectionId;
            AD.AuthenticationResult result;

            switch (parameters.AuthenticationMethod)
            {
                case SC.SqlAuthenticationMethod.ActiveDirectoryInteractive:
                    Console.WriteLine("In method 'AcquireTokenAsync', case_0 == '.ActiveDirectoryInteractive'.");

                    result = await authContext.AcquireTokenAsync(
                        parameters.Resource,  // "https://database.windows.net/"
                        _clientId,
                        _redirectUri,
                        new AD.PlatformParameters(AD.PromptBehavior.Auto),
                        new AD.UserIdentifier(
                            parameters.UserId,
                            AD.UserIdentifierType.RequiredDisplayableId));
                    break;

                case SC.SqlAuthenticationMethod.ActiveDirectoryIntegrated:
                    Console.WriteLine("In method 'AcquireTokenAsync', case_1 == '.ActiveDirectoryIntegrated'.");

                    result = await authContext.AcquireTokenAsync(
                        parameters.Resource,
                        _clientId,
                        new AD.UserCredential());
                    break;

                case SC.SqlAuthenticationMethod.ActiveDirectoryPassword:
                    Console.WriteLine("In method 'AcquireTokenAsync', case_2 == '.ActiveDirectoryPassword'.");

                    result = await authContext.AcquireTokenAsync(
                        parameters.Resource,
                        _clientId,
                        new AD.UserPasswordCredential(
                            parameters.UserId,
                            parameters.Password));
                    break;

                default: throw new InvalidOperationException();
            }
            return new SC.SqlAuthenticationToken(result.AccessToken, result.ExpiresOn);
        }

        public override bool IsSupported(SC.SqlAuthenticationMethod authenticationMethod)
        {
            return authenticationMethod == SC.SqlAuthenticationMethod.ActiveDirectoryIntegrated
                || authenticationMethod == SC.SqlAuthenticationMethod.ActiveDirectoryInteractive
                || authenticationMethod == SC.SqlAuthenticationMethod.ActiveDirectoryPassword;
        }
    } // EOClass ActiveDirectoryAuthProvider.
} // EONamespace.  End of entire program source code.

```

&nbsp;

Toto je příklad výstupu testu jazyka C#.

```C#
[C:\Test\VSProj\ADInteractive5\ADInteractive5\bin\Debug\]
>> ADInteractive5.exe
In method 'AcquireTokenAsync', case_0 == '.ActiveDirectoryInteractive'.
******** MY QUERY RAN SUCCESSFULLY!! ********

:Success

[C:\Test\VSProj\ADInteractive5\ADInteractive5\bin\Debug\]
>>
```

## <a name="next-steps"></a>Další kroky

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Modul PowerShell Azure Resource Manager je stále podporován Azure SQL Database, ale všechny budoucí vývojové prostředí jsou pro modul AZ. SQL. Tyto rutiny naleznete v tématu [AzureRM. SQL](/powershell/module/AzureRM.Sql/). Argumenty pro příkazy v modulech AZ a v modulech AzureRm jsou v podstatě identické.

& [Get-AzSqlServerActiveDirectoryAdministrator](/powershell/module/az.sql/get-azsqlserveractivedirectoryadministrator)