---
title: Služba ActiveDirectoryInteractive se připojuje k sql
description: Příklad kódu Jazyka C# s vysvětlením pro připojení k azure sql database pomocí režimu SqlAuthenticationMethod.ActiveDirectoryInteractive.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: active directory
ms.devlang: ''
ms.topic: conceptual
author: GithubMirek
ms.author: MirekS
ms.reviewer: GeneMi, vanto
ms.date: 10/11/2019
ms.openlocfilehash: 5e7d58e5e0fc79e407e77ae9d73314a1d5d22666
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "73692300"
---
# <a name="connect-to-azure-sql-database-with-azure-multi-factor-authentication"></a>Připojení k Azure SQL Database pomocí azure vícefaktorového ověřování

Tento článek obsahuje program Jazyka C#, který se připojuje k Azure SQL Database. Program používá interaktivní režim ověřování, který podporuje [Azure Multi-Factor Authentication](https://docs.microsoft.com/azure/active-directory/authentication/concept-mfa-howitworks).

Další informace o podpoře vícefaktorového ověřování pro nástroje SQL naleznete [v tématu Podpora Azure Active Directory v nástrojích SQL Server Data Tools (SSDT).](https://docs.microsoft.com/sql/ssdt/azure-active-directory)

## <a name="multi-factor-authentication-for-azure-sql-database"></a>Vícefaktorové ověřování pro Azure SQL Database

Počínaje rozhraním .NET Framework verze 4.7.2 má [`SqlAuthenticationMethod`](https://docs.microsoft.com/dotnet/api/system.data.sqlclient.sqlauthenticationmethod) `ActiveDirectoryInteractive`výčtnová hodnota novou hodnotu: . V programu klientského Jazyka C# hodnota výčtu nasměruje systém k použití interaktivního režimu Azure Active Directory (Azure AD), který podporuje vícefaktorové ověřování pro připojení k databázi Azure SQL. Uživateli, který program spouští, se zobrazí následující dialogová okna:

* Dialogové okno, které zobrazuje uživatelské jméno Azure AD a požádá o heslo uživatele.

   Pokud je doména uživatele federována pomocí služby Azure AD, toto dialogové okno se nezobrazí, protože není potřeba žádné heslo.

   Pokud zásady Azure AD ukládá vícefaktorové ověřování pro uživatele, zobrazí se další dvě dialogová okna.

* Při prvním procházení vícefaktorového ověřování systém zobrazí dialogové okno s žádostí o číslo mobilního telefonu pro odesílání textových zpráv. Každá zpráva obsahuje *ověřovací kód,* který musí uživatel zadat v dalším dialogovém okně.

* Dialogové okno, které požaduje ověřovací kód vícefaktorového ověřování, který systém odeslal do mobilního telefonu.

Informace o tom, jak nakonfigurovat Azure AD tak, aby vyžadovalo vícefaktorové ověřování, najdete [v tématu Začínáme s Azure Multi-Factor Authentication v cloudu](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-get-started-cloud).

Snímky obrazovky těchto dialogových oken najdete [v tématu Konfigurace vícefaktorového ověřování pro SQL Server Management Studio a Azure AD](sql-database-ssms-mfa-authentication-configure.md).

> [!TIP]
> Rozhraní API rozhraní .NET Framework můžete vyhledávat pomocí [stránky nástroje prohlížeče rozhraní .NET API](https://docs.microsoft.com/dotnet/api/).
>
> Můžete také vyhledávat přímo s [volitelným parametrem&lt;?term= search value&gt; ](https://docs.microsoft.com/dotnet/api/?term=SqlAuthenticationMethod).

## <a name="configure-your-c-application-in-the-azure-portal"></a>Konfigurace aplikace C# na webu Azure Portal

Než začnete, měli byste mít vytvořený a dostupný [server Azure SQL Database.](sql-database-get-started-portal.md)

### <a name="register-your-app-and-set-permissions"></a>Registrace aplikace a nastavení oprávnění

Chcete-li použít ověřování Azure AD, váš program C# se musí zaregistrovat jako aplikace Azure AD. Chcete-li zaregistrovat aplikaci, musíte být buď správce Azure AD nebo uživatel přiřazen roli *Vývojář aplikací* Azure AD. Další informace o přiřazování rolí najdete v [tématu Přiřazení rolí správce a role bez oprávnění správce uživatelům služby Azure Active Directory](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-users-assign-role-azure-portal).

Dokončení registrace aplikace generuje a zobrazuje **ID aplikace**. Program musí obsahovat toto ID pro připojení.

Registrace a nastavení potřebných oprávnění pro vaši aplikaci:

1. Na webu Azure Portal vyberte**Registrace** > aplikací **Azure Active Directory** > **Nová registrace**.

    ![Registrace aplikací](media/active-directory-interactive-connect-azure-sql-db/image1.png)

    Po vytvoření registrace aplikace se vygeneruje a zobrazí hodnota **ID aplikace.**

    ![Zobrazí se ID aplikace](media/active-directory-interactive-connect-azure-sql-db/image2.png)

2. Vyberte **oprávnění** > rozhraní**API: Přidejte oprávnění**.

    ![Nastavení oprávnění pro registrovanou aplikaci](media/active-directory-interactive-connect-azure-sql-db/sshot-registered-app-settings-required-permissions-add-api-access-c32.png)

3. Vyberte **api, která moje organizace používá,** > do vyhledávacího > zadejte Azure **SQL Database** a vyberte **Azure SQL Database**.

    ![Přidání přístupu k rozhraní API pro Azure SQL Database](media/active-directory-interactive-connect-azure-sql-db/sshot-registered-app-settings-required-permissions-add-api-access-Azure-sql-db-d11.png)

4. Vyberte **delegovaná oprávnění** > **user_impersonation** > **Přidat oprávnění**.

    ![Delegování oprávnění k rozhraní API pro Azure SQL Database](media/active-directory-interactive-connect-azure-sql-db/sshot-add-api-access-azure-sql-db-delegated-permissions-checkbox-e14.png)

### <a name="set-an-azure-ad-admin-for-your-sql-database-server"></a>Nastavení správce Azure AD pro databázový server SQL

Aby se váš program C# mohl spustit, musí správce serveru Azure SQL přiřadit správce Azure AD pro váš databázový server SQL. 

Na stránce **SQL Server** vyberte možnost Správce **správce** > služby Active Directory Nastavit**správce**.

Další informace o správcích Azure AD a uživatelích pro Azure SQL Database najdete na snímcích obrazovky v [tématu Konfigurace a správa ověřování Azure Active Directory pomocí databáze SQL Database](sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server).

### <a name="add-a-non-admin-user-to-a-specific-database-optional"></a>Přidání uživatele, který není správcem, do určité databáze (volitelné)

Správce Azure AD pro server sql databáze můžete spustit c# ukázkový program. Uživatel Služby Azure AD můžete spustit program, pokud jsou v databázi. Správce Azure AD SQL nebo uživatel Azure AD, který už `ALTER ANY USER` v databázi existuje a má oprávnění k databázi, můžou přidat uživatele.

Uživatele můžete přidat do databáze pomocí [`Create User`](https://docs.microsoft.com/sql/t-sql/statements/create-user-transact-sql) příkazu SQL. Příklad: `CREATE USER [<username>] FROM EXTERNAL PROVIDER`.

Další informace najdete [v tématu Použití ověřování Azure Active Directory pro ověřování pomocí databáze SQL, spravované instance nebo datového skladu SQL](sql-database-aad-authentication.md).

## <a name="new-authentication-enum-value"></a>Nová hodnota výčtu ověřování

Příklad Jazyka C# závisí [`System.Data.SqlClient`](https://docs.microsoft.com/dotnet/api/system.data.sqlclient) na oboru názvů. Zvláštní zájem pro vícefaktorové ověřování `SqlAuthenticationMethod`je výčet , který má následující hodnoty:

- `SqlAuthenticationMethod.ActiveDirectoryInteractive`

   Tuto hodnotu použijte s uživatelským jménem Azure AD k implementaci vícefaktorového ověřování. Tato hodnota je zaměření tohoto článku. Vytváří interaktivní prostředí zobrazením dialogových oken pro uživatelské heslo a potom pro ověřování více faktorem, pokud je tomuto uživateli uloženo vícefaktorové ověřování. Tato hodnota je k dispozici počínaje rozhraním .NET Framework verze 4.7.2.

- `SqlAuthenticationMethod.ActiveDirectoryIntegrated`

  Tuto hodnotu použijte pro *federovaný* účet. U federovaného účtu je uživatelské jméno známo doméně systému Windows. Tato metoda ověřování nepodporuje vícefaktorové ověřování.

- `SqlAuthenticationMethod.ActiveDirectoryPassword`

  Tuto hodnotu použijte pro ověřování, které vyžaduje uživatelské jméno a heslo Azure AD. Azure SQL Database provádí ověřování. Tato metoda nepodporuje vícefaktorové ověřování.

## <a name="set-c-parameter-values-from-the-azure-portal"></a>Nastavení hodnot parametrů Jazyka C# z portálu Azure

Aby byl program Jazyka C# úspěšně spuštěn, je třeba přiřadit statické pole správné hodnoty. Zde jsou uvedena pole s ukázkovými hodnotami. Jsou také zobrazena umístění portálu Azure, kde můžete získat potřebné hodnoty.

| Název statického pole | Příklad hodnoty | Kde v Azure portálu |
| :---------------- | :------------ | :-------------------- |
| Az_SQLDB_svrName | "my-sqldb-svr.database.windows.net" | **Servery** > SQL**filtrují podle názvu** |
| AzureAD_UserID | "auser\@abc.onmicrosoft.com" | **Uživatel služby** > **User** > Azure Active Directory Nový uživatel typu**Host** |
| Initial_DatabaseName | "myDatabase" | **Databáze** > SQL serverů**SQL** |
| ID klientské aplikace | "a94f9c62-97fe-4d19-b06d-111111111111111" | **Azure Active Directory** > **App registrations**Registrace > aplikací Azure Active Directory**Vyhledávání podle názvu** > **ID aplikace** |
| PřesměrováníUri | nový Uri("https://mywebserver.com/") | **Registrace** > **aplikací** > Azure Active Directory Vyhledávání podle**názvu** > *[Vaše registrace aplikací]* > **RedirectURI** **nastavení** > <br /><br />Pro tento článek všechny platné hodnoty je v pořádku pro RedirectUri, protože se zde nepoužívá. |
| &nbsp; | &nbsp; | &nbsp; |

## <a name="verify-with-sql-server-management-studio"></a>Ověření pomocí aplikace SQL Server Management Studio

Před spuštěním programu C# je vhodné zkontrolovat, zda jsou nastavení a konfigurace správné v aplikaci SQL Server Management Studio (SSMS). Jakékoli selhání programu jazyka C# pak lze zúžit na zdrojový kód.

### <a name="verify-sql-database-firewall-ip-addresses"></a>Ověření ip adres brány FIREWALL databáze SQL Database

Spusťte SSMS ze stejného počítače ve stejné budově, kde máte v plánu spustit program Jazyka C#. Pro tento test je jakýkoli režim **ověřování** v pořádku. Pokud existuje nějaký náznak, že brána firewall databázového serveru nepřijímá vaši IP adresu, přečtěte si nápovědu v tématu [Pravidla brány firewall na úrovni serveru Azure SQL Database a na úrovni databáze.](sql-database-firewall-configure.md)

### <a name="verify-azure-active-directory-multi-factor-authentication"></a>Ověření vícefaktorového ověřování služby Azure Active Directory

Spusťte znovu SSMS, tentokrát s **ověřováním** nastaveným na **službu Active Directory - Universal s podporou vícefaktorové ověřování**. Tato možnost vyžaduje SSMS verze 17.5 nebo novější.

Další informace naleznete [v tématu Konfigurace vícefaktorového ověřování pro SSMS a Azure AD](sql-database-ssms-mfa-authentication-configure.md).

> [!NOTE]
> Pokud jste uživatelem typu Host v databázi, musíte také zadat název domény Azure AD pro databázi: Vyberte **možnosti** > **Název domény nebo ID klienta .** Pokud chcete najít název domény na webu Azure Portal, vyberte**Vlastní názvy domén Služby** **Azure Active Directory** > . V příkladu programu C# není zadání názvu domény nutné.

## <a name="c-code-example"></a>Příklad kódu jazyka C#

Ukázkový program Jazyka C# závisí na sestavení DLL [*microsoft.IdentityModel.Clients.ActiveDirectory.*](https://docs.microsoft.com/dotnet/api/microsoft.identitymodel.clients.activedirectory)

Chcete-li nainstalovat tento balíček, vyberte v aplikaci Visual Studio **možnost Spravovat balíčky** > **NuGet**projektu . Vyhledejte a nainstalujte **soubor Microsoft.IdentityModel.Clients.ActiveDirectory**.

Toto je příklad zdrojového kódu Jazyka C#.

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
        static public string Az_SQLDB_svrName = "<Your SQL DB server>";
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

Toto je příklad výstupu testu Jazyka C#.

```
[C:\Test\VSProj\ADInteractive5\ADInteractive5\bin\Debug\]
>> ADInteractive5.exe
In method 'AcquireTokenAsync', case_0 == '.ActiveDirectoryInteractive'.
******** MY QUERY RAN SUCCESSFULLY!! ********

:Success

[C:\Test\VSProj\ADInteractive5\ADInteractive5\bin\Debug\]
>>
```

## <a name="next-steps"></a>Další kroky

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Modul PowerShell Azure Resource Manager je stále podporovaný službou Azure SQL Database, ale veškerý budoucí vývoj je pro modul Az.Sql. Tyto rutiny naleznete v tématu [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Argumenty pro příkazy v modulu Az a v modulech AzureRm jsou v podstatě identické.

- [Správce služby Get-AzSqlServerActiveDirectoryAdministrator](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlserveractivedirectoryadministrator)
