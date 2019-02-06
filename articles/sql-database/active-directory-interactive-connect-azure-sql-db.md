---
title: ActiveDirectoryInteractive připojuje k SQL | Dokumentace Microsoftu
description: Příklad kódu jazyka C#, s vysvětlením, pro připojení ke službě Azure SQL Database s použitím SqlAuthenticationMethod.ActiveDirectoryInteractive režimu.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: active directory
ms.devlang: ''
ms.topic: conceptual
author: GithubMirek
ms.author: MirekS
ms.reviewer: GeneMi
ms.date: 01/25/2019
manager: craigg
ms.openlocfilehash: def50aecbcf9186af9d0b9c781c3141ad2dcee59
ms.sourcegitcommit: 039263ff6271f318b471c4bf3dbc4b72659658ec
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/06/2019
ms.locfileid: "55753669"
---
# <a name="connect-to-azure-sql-database-with-active-directory-mfa"></a>Připojení k Azure SQL Database pomocí služby Active Directory MFA

Tento článek obsahuje C# program, který se připojuje k Microsoft Azure SQL Database. Program používá interaktivní režim ověřování, která podporuje [Azure Active Directory (AD) vícefaktorové ověřování (MFA)](https://docs.microsoft.com/azure/active-directory/authentication/concept-mfa-howitworks).

Další informace o podpoře vícefaktorové ověřování pro nástroje systému SQL najdete v tématu [podpora služby Azure Active Directory v SQL Server Data Tools (SSDT)](https://docs.microsoft.com/sql/ssdt/azure-active-directory).

## <a name="multi-factor-authentication-for-azure-sql-database"></a>Ověřování službou Multi-Factor Authentication pro službu Azure SQL Database

Počínaje rozhraním .NET Framework verze 4.7.2, výčtového typu [ `SqlAuthenticationMethod` ](https://docs.microsoft.com/dotnet/api/system.data.sqlclient.sqlauthenticationmethod) má nová hodnota - `ActiveDirectoryInteractive`. V klientovi C# programu, hodnota výčtu určí, že systém použít interaktivní režim Azure AD podporuje vícefaktorové ověřování pro připojení ke službě Azure SQL Database. Uživatel, který spouští program zobrazí dialogových oknech následující:

1. Dialogové okno, které zobrazí uživatelské jméno Azure AD a vyzve k zadání hesla.

   Pokud doména uživatele je Federovaná pomocí služby Azure AD, pak toto dialogové okno nezobrazí podle potřeby bez hesla.

   Pokud zásady Azure AD MFA ukládá na uživatele, se zobrazí další dvě dialogových oknech.

2. Při prvním uživatel prochází MFA, systém zobrazí dialogové okno s žádostí o mobilní telefonní číslo neposílali textové zprávy na. Každá zpráva obsahuje *ověřovací kód* , které uživatel musí zadat v dialogovém okně Další.

3. Dialogové okno s žádostí o ověřovací kód vícefaktorové ověřování, které systém odeslala na mobilní telefon.

Informace o tom, jak nakonfigurovat tak, aby vyžadovala vícefaktorové ověřování Azure AD najdete v tématu [Začínáme se službou Azure Multi-Factor Authentication v cloudu](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-get-started-cloud).

Snímky obrazovky tyto dialogy, naleznete v tématu [konfigurovat vícefaktorové ověřování pro SQL Server Management Studio a Azure AD](sql-database-ssms-mfa-authentication-configure.md).

> [!TIP]
> Můžete hledat rozhraní API .NET Framework s naší **.NET API Browseru** stránka nástroje:
>
> [https://docs.microsoft.com/dotnet/api/](https://docs.microsoft.com/dotnet/api/)
>
> Můžete také vyhledat přímo s nepovinným **? termín =&lt;vyhledat hodnotu&gt;**  parametr:
>
> [https://docs.microsoft.com/dotnet/api/?term=SqlAuthenticationMethod](https://docs.microsoft.com/dotnet/api/?term=SqlAuthenticationMethod)

## <a name="configure-your-c-application-in-the-azure-portal"></a>Konfigurace vašeho C# aplikace na webu Azure Portal

Než začnete, měli byste [serveru Azure SQL Database](sql-database-get-started-portal.md) vytvořený a k dispozici.

### <a name="register-your-app-and-set-permissions"></a>Registrace aplikace a nastavení oprávnění


Použití ověřování Azure AD vaší C# program se má zaregistrovat jako aplikaci AD. Postup pro registraci aplikace, musíte být správce AD nebo uživatel přiřazenou AD *vývojář aplikace* role. Další informace o přiřazování rolí najdete v tématu [přiřazení rolí správce a bez oprávnění správce uživatelům v Azure Active Directory.](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-users-assign-role-azure-portal)

 Dokončuje se registrace aplikace vygeneruje a zobrazí **ID aplikace**. Program musí obsahovat toto ID se má připojit.


K registraci a nastavit potřebná oprávnění pro vaši aplikaci:

1. Azure portal > **Azure Active Directory** > **registrace aplikací** > **registrace nové aplikace**

    ![Registrace aplikace](media/active-directory-interactive-connect-azure-sql-db/image1.png)

    Po vytvoření registrace aplikace **ID aplikace** vygeneruje a zobrazí hodnotu.

    ![Zobrazí ID aplikace](media/active-directory-interactive-connect-azure-sql-db/image2.png)

2. **Registrovaná aplikace** > **nastavení** > **požadovaná oprávnění** > **přidat**

    ![Nastavení oprávnění pro registrovaná aplikace](media/active-directory-interactive-connect-azure-sql-db/sshot-registered-app-settings-required-permissions-add-api-access-c32.png)

3. **Požadovaná oprávnění** > **přidat** > **vyberte rozhraní API** > **Azure SQL Database**

    ![Přidat přístup k rozhraní API pro službu Azure SQL Database](media/active-directory-interactive-connect-azure-sql-db/sshot-registered-app-settings-required-permissions-add-api-access-Azure-sql-db-d11.png)

4. **Přístup přes rozhraní API** > **vyberte oprávnění** > **delegovaná oprávnění**

    ![Delegování oprávnění k rozhraní API pro službu Azure SQL Database](media/active-directory-interactive-connect-azure-sql-db/sshot-add-api-access-azure-sql-db-delegated-permissions-checkbox-e14.png)

### <a name="set-an-azure-ad-admin-for-your-sql-database-server"></a>Nastavit správce Azure AD pro váš server SQL Database


Pro vaše C# ke spuštění programu, správce serveru Azure SQL je potřeba přiřadit správce Azure AD pro váš server Azure SQL. 

 * **SQL Server** > **správce Active Directory** > **nastavit správce**

Další informace o Správci Azure AD a uživatelů pro službu Azure SQL Database najdete v tématu snímky obrazovky v [konfigurovat a spravovat ověřování Azure Active Directory s využitím SQL Database](sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server), v jeho oddílu **zřízení Azure Správce služby Active Directory pro váš server Azure SQL Database**.

### <a name="add-a-non-admin-user-to-a-specific-database-optional"></a>Přidat uživatele bez oprávnění správce databáze (volitelné)

Správce Azure AD pro server služby SQL Database můžete spustit C# ukázkový program. Uživatele služby Azure AD můžete spustit program, pokud jsou v databázi. Správce Azure AD SQL nebo uživatele Azure AD, který v databázi již existuje a má `ALTER ANY USER` oprávnění v databázi můžete přidat uživatele.

Můžete přidat uživatele do databáze s SQL [ `Create User` ](https://docs.microsoft.com/sql/t-sql/statements/create-user-transact-sql?view=sql-server-2017) příkazu. Například, `CREATE USER [<username>] FROM EXTERNAL PROVIDER`.

Další informace najdete v tématu [používání ověřování Azure Active Directory pro ověřování pomocí SQL Database, mi nebo SQL Data Warehouse](sql-database-aad-authentication.md).

## <a name="new-authentication-enum-value"></a>Nová hodnota výčtu ověřování

C# Příkladu se může spolehnout [ `System.Data.SqlClient` ](https://docs.microsoft.com/dotnet/api/system.data.sqlclient) oboru názvů. Zajímají ověřování službou Multi-Factor Authentication je výčet `SqlAuthenticationMethod`, který má následující hodnoty:

- `SqlAuthenticationMethod.ActiveDirectoryInteractive`

   Používejte tuto hodnotu s názvem uživatele Azure AD k implementaci vícefaktorového ověřování. Tato hodnota je hlavním cílem tohoto článku. Vytvoří interaktivní zobrazením dialogová okna pro heslo uživatele a pro ověřování MFA, pokud vícefaktorové ověřování se vynucují na tohoto uživatele. Tato hodnota je k dispozici od verze rozhraní .NET Framework verze 4.7.2.

- `SqlAuthenticationMethod.ActiveDirectoryIntegrated`

  Použijte tuto hodnotu pro *federované* účtu. Pro federované účet, který se označuje uživatelské jméno k doméně Windows. Tato metoda ověřování nepodporuje vícefaktorové ověřování.

- `SqlAuthenticationMethod.ActiveDirectoryPassword`

  Tato hodnota slouží k ověřování, který vyžaduje Azure AD uživatelské jméno a heslo. Ověřování se postará Azure SQL Database. Tato metoda nepodporuje vícefaktorové ověřování.

## <a name="set-c-parameter-values-from-the-azure-portal"></a>Nastavte C# hodnoty parametrů z webu Azure portal

Pro C# k úspěšnému spuštění programu, je potřeba přiřadit statická pole odpovídajícími hodnotami. Je vidět tady jsou ukázkové hodnoty polí. Umístění Azure portal, kde můžete získat potřebné hodnoty jsou také uvedeny:

| Statické pole název | Příklad hodnoty | Pokud na webu Azure portal |
| :---------------- | :------------ | :-------------------- |
| Az_SQLDB_svrName | "my-sqldb-svr.database.windows.net" | **SQL servery** > **filtrovat podle názvu** |
| AzureAD_UserID | auser@abc.onmicrosoft.com | **Azure Active Directory** > **uživatele** > **nový uživatel typu Host** |
| Initial_DatabaseName | "myDatabase" | **SQL servery** > **databáze SQL** |
| ClientApplicationID | "a94f9c62-97fe-4d19-b06d-111111111111" | **Azure Active Directory** > **registrace aplikací** > **hledat podle názvu** > **ID aplikace** |
| RedirectUri | nový identifikátor Uri ("https://mywebserver.com/") | **Azure Active Directory** > **registrace aplikací** > **hledat podle názvu** > *[Your-App-regis]*  >  **Nastavení** > **RedirectURIs**<br /><br />Pro účely tohoto článku libovolnou platnou hodnotu je v pořádku pro identifikátor URI pro přesměrování, jak se tady nepoužívá. |
| &nbsp; | &nbsp; | &nbsp; |

## <a name="verify-with-sql-server-management-studio-ssms"></a>Ověření pomocí SQL Server Management Studio (SSMS)

Před spuštěním C# program, je vhodné zkontrolovat, že vaše instalace a konfigurace jsou správné v aplikaci SSMS. Žádné C# selhání program může potom zúžit zdrojového kódu.

### <a name="verify-sql-database-firewall-ip-addresses"></a>Zkontrolujte IP adresy brány firewall SQL Database

Spusťte aplikaci SSMS ze stejného počítače ve stejné budově, kde plánujete provozovat C# programu. Pro tento test jakékoli **ověřování** režimu je v pořádku. Pokud jakoukoli indikaci toho, že brány firewall serveru databáze nepřijímá vaši IP adresu, přečtěte si téma [pravidla brány firewall na úrovni serveru a databáze Azure SQL Database](sql-database-firewall-configure.md) nápovědu.

### <a name="verify-azure-active-directory-mfa"></a>Ověření služby Azure Active Directory MFA

Spusťte aplikaci SSMS znovu, tentokrát s **ověřování** nastavena na **univerzální podporující vícefaktorové ověřování služby Active Directory –**. Tato možnost vyžaduje 17,5 nebo novější verze aplikace SSMS.

Další informace najdete v tématu [ověřování službou Multi-Factor Authentication konfigurovat pro aplikace SSMS a Azure AD](sql-database-ssms-mfa-authentication-configure.md).

> [!NOTE]
> Pokud jste uživatel typu Host v databázi, musíte také zadat název domény AD pro databázi - **možnosti** > **ID tenanta nebo název domény AD**. Chcete-li najít název domény na webu Azure Portal, vyberte **Azure Active Directory** > **vlastní názvy domén**. V C# ukázkový program, poskytnutí názvu domény není nutné.

## <a name="c-code-example"></a>Příklad kódu jazyka C#

V příkladu C# program závisí na [ *Microsoft.IdentityModel.Clients.ActiveDirectory* ](https://docs.microsoft.com/dotnet/api/microsoft.identitymodel.clients.activedirectory) sestavení knihovny DLL.

Chcete-li nainstalovat tento balíček v sadě Visual Studio, vyberte **projektu** > **spravovat balíčky NuGet**. Vyhledejte a nainstalujte **Microsoft.IdentityModel.Clients.ActiveDirectory**.

### <a name="c-source-code"></a>Zdrojový kód C#

```csharp

using System;

// Reference to AD authentication assembly
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
    } // EOClass Program .

    /// <summary>
    /// SqlAuthenticationProvider - Is a public class that defines 3 different Azure AD
    /// authentication methods.  The methods are supported in the new .NET 4.7.2 .
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
    } // EOClass ActiveDirectoryAuthProvider .
} // EONamespace.  End of entire program source code.

```

&nbsp;

#### <a name="actual-test-output-from-c"></a>Skutečný test výstup z jazyka C#

```
[C:\Test\VSProj\ADInteractive5\ADInteractive5\bin\Debug\]
>> ADInteractive5.exe
In method 'AcquireTokenAsync', case_0 == '.ActiveDirectoryInteractive'.
******** MY QUERY RAN SUCCESSFULLY!! ********

:Success

[C:\Test\VSProj\ADInteractive5\ADInteractive5\bin\Debug\]
>>
```

## <a name="next-steps"></a>Další postup

- [Získání informací o správce Azure AD pro SQL Server](https://docs.microsoft.com/powershell/module/azurerm.sql/get-azurermsqlserveractivedirectoryadministrator)

