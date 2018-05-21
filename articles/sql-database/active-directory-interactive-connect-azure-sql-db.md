---
title: ActiveDirectoryInteractive připojuje k SQL | Microsoft Docs
description: Příklad kódu C#, s vysvětlením, pro připojení k Azure SQL Database pomocí SqlAuthenticationMethod.ActiveDirectoryInteractive režimu.
services: sql-database
author: GithubMirek
manager: craigg
ms.service: sql-database
ms.custom: active directory
ms.topic: article
ms.date: 04/06/2018
ms.author: MirekS
ms.reviewer: GeneMi
ms.openlocfilehash: 6489fb5630e1990c942b461859650e2e469cda73
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/20/2018
---
# <a name="use-activedirectoryinteractive-mode-to-connect-to-azure-sql-database"></a>Režim ActiveDirectoryInteractive slouží k připojení k databázi SQL Azure

Tento článek obsahuje spustitelného C# příklad kódu, který se připojuje k vaší databázi SQL Azure Microsoft. Programu C# používá interaktivní režim ověřování, který podporuje Azure AD vícefaktorové ověřování (MFA). Pokus o připojení může zahrnovat ověřovací kód odesílány na váš mobilní telefon.

Další informace o podpoře vícefaktorového ověřování pro nástroje systému SQL najdete v tématu [podporu služby Azure Active Directory v serveru SQL Server Data Tools (SSDT)](https://docs.microsoft.com/sql/ssdt/azure-active-directory).




## <a name="sqlauthenticationmethod-activedirectoryinteractive-enum-value"></a>SqlAuthenticationMethod. Hodnota výčtu ActiveDirectoryInteractive

Od verze rozhraní .NET Framework verze 4.7.2, je výčet [ **SqlAuthenticationMethod** ](https://docs.microsoft.com/dotnet/api/system.data.sqlclient.sqlauthenticationmethod) má novou hodnotu **. ActiveDirectoryInteractive**. Při použití s klienta programu v C#, tato hodnota výčtu určí, že systém interaktivním režimu Azure AD podporuje vícefaktorové ověřování používat k ověřování do Azure SQL Database. Uživatel, který spouští program, pak se zobrazí následující dialogová okna:

1. Dialog, který zobrazí uživatelské jméno Azure AD, a který požádá o heslo uživatele Azure AD.
    - Toto dialogové okno se nezobrazí, pokud je potřeba žádné heslo. Pokud je doména uživatele sdružených se službou Azure AD, je potřeba žádné heslo.

    Pokud vícefaktorové ověřování, je na uživatele, způsobené zásady nastavené v Azure AD, zobrazí se další následující dialogová okna.

2. Pouze při prvním uživatele vyskytne scénáři MFA systému zobrazí další dialogové okno. Dialogové okno požádá o mobilní telefonní číslo, na které se budou odesílat textové zprávy. Každá zpráva poskytuje *ověřovací kód* , musí uživatel zadat do dialogu Další.

3. Další dialog s žádostí o MFA ověřovací kód, který systému odeslal na mobilní telefon.

Informace o tom, jak konfigurovat Azure AD, aby se vyžadovalo najdete v tématu [Začínáme s Azure Multi-Factor Authentication v cloudu](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-get-started-cloud).

Snímky obrazovky tyto dialogy, najdete v části [konfigurace vícefaktorového ověřování pro SQL Server Management Studio a Azure AD](sql-database-ssms-mfa-authentication-configure.md).

> [!TIP]
> Naše stránka Obecné vyhledávání pro všechny typy rozhraní API technologie .NET Framework je k dispozici na následující odkaz na našem užitečný **prohlížeče rozhraní API .NET** nástroje:
>
> [https://docs.microsoft.com/dotnet/api/](https://docs.microsoft.com/dotnet/api/)
>
> Přidáním název typu volitelnou připojí **? termín =** parametr stránky hledání může mít naše výsledek čekání a připravené pro nás:
>
> [https://docs.microsoft.com/dotnet/api/?term=SqlAuthenticationMethod](https://docs.microsoft.com/dotnet/api/?term=SqlAuthenticationMethod)


## <a name="preparations-for-c-by-using-the-azure-portal"></a>Přípravné kroky pro jazyk C# pomocí portálu Azure

Předpokládáme, že už máte [serveru Azure SQL Database vytvořit](sql-database-get-started-portal.md) a k dispozici.


### <a name="a-create-an-app-registration"></a>A. Vytvořit registraci aplikace

Chcete-li použít ověřování Azure AD, musí váš klientský program C# zadat identifikátor GUID jako *clientId* když váš program pokusí o připojení. Dokončení registraci aplikace vygeneruje a zobrazí GUID v portálu Azure označený jako **ID aplikace**. Navigace kroky jsou následující:

1. Portál Azure &gt; **Azure Active Directory** &gt; **registrace aplikací**

    ![Registrace aplikace](media\active-directory-interactive-connect-azure-sql-db\sshot-create-app-registration-b20.png)

2. **ID aplikace** se vygeneruje a zobrazí hodnotu.

    ![Zobrazí ID aplikace](media\active-directory-interactive-connect-azure-sql-db\sshot-application-id-app-regis-mk49.png)

3. **Registrované aplikaci** &gt; **nastavení** &gt; **požadovaná oprávnění** &gt; **přidat**

    ![Nastavení oprávnění pro registrované aplikaci](media\active-directory-interactive-connect-azure-sql-db\sshot-registered-app-settings-required-permissions-add-api-access-c32.png)

4. **Požadovaná oprávnění** &gt; **přístup přidat rozhraní API** &gt; **vybrat rozhraní API** &gt; **Azure SQL Database**

    ![Přidat přístup k rozhraní API pro databázi SQL Azure](media\active-directory-interactive-connect-azure-sql-db\sshot-registered-app-settings-required-permissions-add-api-access-Azure-sql-db-d11.png)

5. **Přístup pomocí rozhraní API** &gt; **vyberte oprávnění** &gt; **delegovaná oprávnění**

    ![Delegování oprávnění k rozhraní API pro databázi SQL Azure](media\active-directory-interactive-connect-azure-sql-db\sshot-add-api-access-azure-sql-db-delegated-permissions-checkbox-e14.png)


### <a name="b-set-azure-ad-admin-on-your-sql-database-server"></a>B. Nastavit správce Azure AD na server služby SQL Database

Každý server Azure SQL Database má svou vlastní logickému serveru SQL Azure AD. Pro naše C# scénáři je potřeba nastavit správce Azure AD pro server Azure SQL.

1. **SQL Server** &gt; **správce Active Directory** &gt; **nastavit správce**

    - Další informace o Azure AD správci a uživatelé pro databázi SQL Azure, najdete v článku na snímcích obrazovky v [konfigurovat a spravovat ověřování Azure Active Directory s databází SQL](sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server), v jeho části **zřízení Azure Správce Active Directory pro server Azure SQL Database**.


### <a name="c-prepare-an-azure-ad-user-to-connect-to-a-specific-database"></a>C. Příprava pro připojení k databázi konkrétní uživatele Azure AD

Ve službě Azure AD, která je specifická pro server služby Azure SQL Database můžete přidat uživatele, který má přístup ke konkrétní databázi.

Další informace najdete v tématu [použití Azure ověřování služby Active Directory k ověřování připojení k databázi SQL, spravované Instance nebo SQL Data Warehouse](sql-database-aad-authentication.md).


### <a name="d-add-a-non-admin-user-to-azure-ad"></a>D. Přidat uživatele bez oprávnění správce k Azure AD

Azure AD správce databáze SQL serveru slouží k připojení k databázi SQL serveru. Další obecné případě je však přidat uživatele bez oprávnění správce k Azure AD. Pokud uživatele bez oprávnění správce se používá k připojení, pořadí MFA je volána, pokud MFA je vynucená pro tohoto uživatele Azure AD.




## <a name="azure-active-directory-authentication-library-adal"></a>Azure Active Directory Authentication Library (ADAL)

Programu C# spoléhá na obor názvů **Microsoft.IdentityModel.Clients.ActiveDirectory**. Třídy pro tento obor názvů jsou v sestavení se stejným názvem.

- Ke stažení a instalaci ADAL sestavení použijte NuGet.
    - [https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/)

- Přidáte odkaz na sestavení pro podporu kompilace programu C#.




## <a name="sqlauthenticationmethod-enum"></a>SqlAuthenticationMethod výčtu

Jeden obory názvů, které využívá příklad jazyka C# je **System.Data.SqlClient**. Zajímají je je výčet **SqlAuthenticationMethod**. Tento výčet má následující hodnoty:

- **SqlAuthenticationMethod.ActiveDirectory*interaktivní ***:&nbsp; to použít s Azure AD uživatelské jméno, k dosažení vícefaktorového ověřování MFA.
    - Tato hodnota je fokus tohoto článku. Vyvolá interaktivní možnosti zobrazením dialogová okna pro heslo uživatele a poté pro ověření vícefaktorového ověřování, pokud je vynucená vícefaktorového ověřování pro tohoto uživatele.
    - Tato hodnota je k dispozici od verze v rozhraní .NET Framework verze 4.7.2.

- **SqlAuthenticationMethod.ActiveDirectory*integrovaný ***:&nbsp; se používá *federovaný* účtu. Pro účet federované se označuje uživatelské jméno k doméně systému Windows. Tato metoda nepodporuje vícefaktorového ověřování.

- **SqlAuthenticationMethod.ActiveDirectory*heslo ***:&nbsp; používejte pro ověřování, které vyžaduje uživatele Azure AD a heslo uživatele. Databáze SQL Azure provádí ověřování. Tato metoda nepodporuje vícefaktorového ověřování.




## <a name="prepare-c-parameter-values-from-the-azure-portal"></a>Příprava C# hodnoty parametrů z portálu Azure

Pro úspěšný běh programu C# je nutné přiřadit správné hodnoty pro následující statické pole. Tato statická pole fungovat stejně jako parametry do programu. Zde se zobrazují pole pretend hodnotami. Také ukazuje jsou umístění na portálu Azure, ze kterých můžete získat správné hodnoty:


| Statické pole název | Předstírají, že hodnota | Kde na portálu Azure |
| :---------------- | :------------ | :-------------------- |
| Az_SQLDB_svrName | "my-Oblíbené položky.-sqldb-svr.database.windows.net" | **Servery SQL** &gt; **filtrovat podle názvu** |
| AzureAD_UserID | user9@abc.onmicrosoft.com | **Azure Active Directory** &gt; **uživatele** &gt; **nového uživatele guest** |
| Initial_DatabaseName | "hlavní" | **Servery SQL** &gt; **databáze SQL** |
| ClientApplicationID | "a94f9c62-97fe-4d19-b06d-111111111111" | **Azure Active Directory** &gt; **registrace aplikace**<br /> &nbsp; &nbsp; &gt; **Hledat podle názvu** &gt; **ID aplikace** |
| redirectUri | nový identifikátor Uri ("https://bing.com/") | **Azure Active Directory** &gt; **registrace aplikace**<br /> &nbsp; &nbsp; &gt; **Hledat podle názvu** &gt; *[vaše-App-regis]* &gt;<br /> &nbsp; &nbsp; **Nastavení** &gt; **RedirectURIs**<br /><br />V tomto článku je vhodná pro RedirectUri žádné platnou hodnotu. Hodnota není v našem případě skutečně použít. |
| &nbsp; | &nbsp; | &nbsp; |


V závislosti na konkrétní scénář nemusí potřebovat hodnoty všech parametrů v předchozí tabulce.




## <a name="run-ssms-to-verify"></a>Spuštění aplikace SSMS ověření

Je vhodné spustit SQL Server Management Studio (SSMS) před spuštěním programu C#. Aplikace SSMS spustit ověří správnost různých konfiguracích. Pak kdykoli programu C# můžete být narrows právě zdrojovém kódu.


#### <a name="verify-sql-database-firewall-ip-addresses"></a>Ověřovat IP adresy brány firewall SQL Database

Spuštění aplikace SSMS ze stejného počítače ve stejné budově, že později spustíte programu C#. Můžete toho **ověřování** režimu si myslíte, že je nejjednodušší. Pokud je jakoukoli indikaci toho, že brána firewall serveru databáze nepřijímá IP adresu, můžete je vyřešit, jak je znázorněno v [pravidla brány firewall úrovni serveru a na úrovni databáze Azure SQL Database](sql-database-firewall-configure.md).


#### <a name="verify-multi-factor-authentication-mfa-for-azure-ad"></a>Ověření vícefaktorového ověřování (MFA) pro Azure AD

Spusťte aplikaci SSMS znovu, tentokrát s **ověřování** nastavena na **Universal s podpora vícefaktorového ověřování služby Active Directory -**. Pro tuto možnost musíte mít SSMS verze 17,5 nebo novější.

Další informace najdete v tématu [konfigurace vícefaktorového ověřování pro aplikaci SSMS a Azure AD](sql-database-ssms-mfa-authentication-configure.md).




## <a name="c-code-example"></a>Příklad kódu C#

Pro zkompilování tohoto příkladu C#, musíte přidat odkaz na knihovnu DLL sestavení s názvem **Microsoft.IdentityModel.Clients.ActiveDirectory**.


#### <a name="reference-documentation"></a>Referenční dokumentace

- **System.Data.SqlClient** obor názvů:
    - Hledání:&nbsp; [https://docs.microsoft.com/dotnet/api/?term=System.Data.SqlClient](https://docs.microsoft.com/dotnet/api/?term=System.Data.SqlClient)
    - Přímé:&nbsp; [System.Data.Client](https://docs.microsoft.com/dotnet/api/system.data.sqlclient)

- **Microsoft.IdentityModel.Clients.ActiveDirectory** obor názvů:
    - Hledání:&nbsp; [https://docs.microsoft.com/dotnet/api/?term=Microsoft.IdentityModel.Clients.ActiveDirectory](https://docs.microsoft.com/dotnet/api/?term=Microsoft.IdentityModel.Clients.ActiveDirectory)
    - Přímé:&nbsp; [Microsoft.IdentityModel.Clients.ActiveDirectory](https://docs.microsoft.com/dotnet/api/microsoft.identitymodel.clients.activedirectory)


#### <a name="c-source-code-in-two-parts"></a>Zdrojový kód C#, ve dvou částech

&nbsp;

```csharp

using System;    // C# ,  part 1 of 2.

// Add a reference to assembly:  Microsoft.IdentityModel.Clients.ActiveDirectory.DLL
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
        static public string Az_SQLDB_svrName = "<YOUR VALUE HERE>";
        static public string AzureAD_UserID = "<YOUR VALUE HERE>";
        static public string Initial_DatabaseName = "master";
        // Some scenarios do not need values for the following two fields:
        static public readonly string ClientApplicationID = "<YOUR VALUE HERE>";
        static public readonly Uri RedirectUri = new Uri("<YOUR VALUE HERE>");

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

```

&nbsp;

#### <a name="second-half-of-the-c-program"></a>Druhé polovině tématu programu C#

Pro lepší visual zobrazení je programu C# rozdělit na dvě bloky kódu. Ke spuštění programu, vložte bloky dvě kódu společně.

&nbsp;

```csharp

    // C# ,  part 2 of 2 ,  to concatenate below part 1.

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

#### <a name="actual-test-output-from-c"></a>Výstup skutečné testu z jazyka C#

```
[C:\Test\VSProj\ADInteractive5\ADInteractive5\bin\Debug\]
>> ADInteractive5.exe
In method 'AcquireTokenAsync', case_0 == '.ActiveDirectoryInteractive'.
******** MY QUERY RAN SUCCESSFULLY!! ********

:Success

[C:\Test\VSProj\ADInteractive5\ADInteractive5\bin\Debug\]
>>
```

&nbsp;


## <a name="next-steps"></a>Další postup

- [Get-AzureRmSqlServerActiveDirectoryAdministrator](https://docs.microsoft.com/powershell/module/azurerm.sql/get-azurermsqlserveractivedirectoryadministrator)

