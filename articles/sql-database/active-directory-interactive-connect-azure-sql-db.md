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
ms.date: 04/06/2018
manager: craigg
ms.openlocfilehash: 80944e73f21d75943d4fa71c7ac9500e47bab250
ms.sourcegitcommit: 715813af8cde40407bd3332dd922a918de46a91a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "47055522"
---
# <a name="use-activedirectoryinteractive-mode-to-connect-to-azure-sql-database"></a>Režim ActiveDirectoryInteractive slouží k připojení ke službě Azure SQL Database

Tento článek obsahuje spustitelný C# příklad kódu, který se připojuje k Microsoft Azure SQL Database. Program jazyka C# používá interaktivní režim ověřování, které podporuje Azure AD vícefaktorové ověřování (MFA). Pokus o připojení, může obsahovat kód pro ověření odesílaných na váš mobilní telefon.

Další informace o podpoře vícefaktorové ověřování pro nástroje systému SQL najdete v tématu [podpora služby Azure Active Directory v SQL Server Data Tools (SSDT)](https://docs.microsoft.com/sql/ssdt/azure-active-directory).




## <a name="sqlauthenticationmethod-activedirectoryinteractive-enum-value"></a>SqlAuthenticationMethod. Hodnota výčtu ActiveDirectoryInteractive

Počínaje rozhraním .NET Framework verze 4.7.2, výčtového typu [ **SqlAuthenticationMethod** ](https://docs.microsoft.com/dotnet/api/system.data.sqlclient.sqlauthenticationmethod) obsahuje novou hodnotu **. ActiveDirectoryInteractive**. Při použití klienta programu v C#, tato hodnota výčtu přesměruje systém použít interaktivní režim Azure AD podporuje vícefaktorové ověřování k ověření do služby Azure SQL Database. Uživatel, který spouští program, pak se zobrazí následující dialogová okna:

1. Dialogové okno, který zobrazí uživatelské jméno Azure AD a, který vyzve k zadání hesla uživatele Azure AD.
    - Toto dialogové okno se nezobrazí v případě potřeby bez hesla. Pokud doména uživatele je Federovaná pomocí služby Azure AD, je potřeba žádné heslo.

    Pokud vícefaktorové ověřování je uložena na uživatele pomocí zásad nastavení ve službě Azure AD, se zobrazují vedle následující dialogová okna.

2. Pouze úplně první čas uživatelské prostředí scénář MFA systému zobrazí další dialog. Dialogové okno požádá o číslo mobilního telefonu, na které se budou odesílat textové zprávy. Každá zpráva obsahuje *ověřovací kód* , které uživatel musí zadat v dalším dialogovém okně.

3. Další dialog, který vyzve k zadání kódu ověřování MFA, odesílajících systému na mobilní telefon.

Informace o tom, jak nakonfigurovat tak, aby vyžadovala vícefaktorové ověřování Azure AD najdete v tématu [Začínáme se službou Azure Multi-Factor Authentication v cloudu](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-get-started-cloud).

Snímky obrazovky tyto dialogy, naleznete v tématu [konfigurovat vícefaktorové ověřování pro SQL Server Management Studio a Azure AD](sql-database-ssms-mfa-authentication-configure.md).

> [!TIP]
> Naši stránku Obecné vyhledávání pro všechny druhy rozhraní API .NET Framework je k dispozici na na následující odkaz a naše po ruce **.NET API Browseru** nástroje:
>
> [https://docs.microsoft.com/dotnet/api/](https://docs.microsoft.com/dotnet/api/)
>
> Přidáním názvu typu volitelnou připojí **? termín =** parametr, na stránce vyhledávání může mít naše výsledek připraven i na pro nás:
>
> [https://docs.microsoft.com/dotnet/api/?term=SqlAuthenticationMethod](https://docs.microsoft.com/dotnet/api/?term=SqlAuthenticationMethod)


## <a name="preparations-for-c-by-using-the-azure-portal"></a>Příprava pro jazyk C# s použitím webu Azure portal

Předpokládáme, že máte [vytvoření serveru Azure SQL Database](sql-database-get-started-portal.md) a k dispozici.


### <a name="a-create-an-app-registration"></a>A. Vytvoření registrace aplikace

Pokud chcete používat ověřování Azure AD, váš klientský program C#, musíte zadat identifikátor GUID jako *clientId* když váš program pokusí o připojení. Dokončuje se registrace aplikace vygeneruje a zobrazí identifikátor GUID na webu Azure Portal označené jako **ID aplikace**. Navigace kroky jsou následující:

1. Azure portal &gt; **Azure Active Directory** &gt; **registrace aplikace**

    ![Registrace aplikace](media\active-directory-interactive-connect-azure-sql-db\sshot-create-app-registration-b20.png)

2. **ID aplikace** vygeneruje a zobrazí hodnotu.

    ![Zobrazí ID aplikace](media\active-directory-interactive-connect-azure-sql-db\sshot-application-id-app-regis-mk49.png)

3. **Registrovaná aplikace** &gt; **nastavení** &gt; **požadovaná oprávnění** &gt; **přidat**

    ![Nastavení oprávnění pro registrovaná aplikace](media\active-directory-interactive-connect-azure-sql-db\sshot-registered-app-settings-required-permissions-add-api-access-c32.png)

4. **Požadovaná oprávnění** &gt; **přístup přes rozhraní API přidat** &gt; **vyberte rozhraní API** &gt; **Azure SQL Database**

    ![Přidat přístup k rozhraní API pro službu Azure SQL Database](media\active-directory-interactive-connect-azure-sql-db\sshot-registered-app-settings-required-permissions-add-api-access-Azure-sql-db-d11.png)

5. **Přístup přes rozhraní API** &gt; **vyberte oprávnění** &gt; **delegovaná oprávnění**

    ![Delegování oprávnění k rozhraní API pro službu Azure SQL Database](media\active-directory-interactive-connect-azure-sql-db\sshot-add-api-access-azure-sql-db-delegated-permissions-checkbox-e14.png)


### <a name="b-set-azure-ad-admin-on-your-sql-database-server"></a>B. Nastavit správce Azure AD na serveru služby SQL Database

Každý server Azure SQL Database má svůj vlastní logický server SQL Azure AD. Pro C# scénáři je potřeba nastavit správce Azure AD pro váš server Azure SQL.

1. **SQL Server** &gt; **správce Active Directory** &gt; **nastavit správce**

    - Další informace o Správci Azure AD a uživatelů pro službu Azure SQL Database najdete v tématu snímky obrazovky v [konfigurovat a spravovat ověřování Azure Active Directory s využitím SQL Database](sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server), v jeho oddílu **zřízení Azure Správce služby Active Directory pro váš server Azure SQL Database**.


### <a name="c-prepare-an-azure-ad-user-to-connect-to-a-specific-database"></a>C. Příprava pro připojení k databázi konkrétní uživatele Azure AD

Ve službě Azure AD, který je specifický pro váš server Azure SQL Database můžete přidat uživatele, který musí mít přístup ke konkrétní databázi.

Další informace najdete v tématu [používání ověřování Azure Active Directory pro ověřování pomocí SQL Database, mi nebo SQL Data Warehouse](sql-database-aad-authentication.md).


### <a name="d-add-a-non-admin-user-to-azure-ad"></a>D. Přidat uživatele bez oprávnění správce ke službě Azure AD

Správce služby Azure AD z databáze SQL serveru je možné se připojit k serveru služby SQL Database. Další obecné případ je však přidat uživatele bez oprávnění správce k Azure AD. Když uživatel bez oprávnění správce se používá pro připojení, pořadí MFA je vyvolána, pokud MFA je dáno na tohoto uživatele Azure AD.




## <a name="azure-active-directory-authentication-library-adal"></a>Azure Active Directory Authentication Library (ADAL)

Program jazyka C# spoléhá na obor názvů **Microsoft.IdentityModel.Clients.ActiveDirectory**. Třídy pro tento obor názvů jsou v sestavení se stejným názvem.

- Ke stažení a instalaci ADAL sestavení použijte NuGet.
    - [https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/)

- Přidejte odkaz na sestavení pro podporu kompilace programu C#.




## <a name="sqlauthenticationmethod-enum"></a>SqlAuthenticationMethod výčtu

Je jeden obory názvů, příklad jazyka C# využívající **System.Data.SqlClient**. Výčet je speciální zájmová **SqlAuthenticationMethod**. Tento výčet má následující hodnoty:

- **SqlAuthenticationMethod.ActiveDirectory \*interaktivní**\*:&nbsp; použijte tento fragment se uživatelské jméno Azure AD k dosažení vícefaktorové ověřování služby Multi-Factor authentication.
    - Tato hodnota je hlavním cílem tohoto článku. Vytvoří interaktivní zobrazením dialogová okna pro heslo uživatele a pro ověřování MFA, pokud vícefaktorové ověřování se vynucují na tohoto uživatele.
    - Tato hodnota je k dispozici od verze rozhraní .NET Framework verze 4.7.2.

- **SqlAuthenticationMethod.ActiveDirectory \*integrované**\*:&nbsp; se používá *federované* účtu. Pro federované účet, který se označuje uživatelské jméno k doméně Windows. Tato metoda nepodporuje vícefaktorové ověřování.

- **SqlAuthenticationMethod.ActiveDirectory \*heslo**\*:&nbsp; používá se pro ověřování, které vyžaduje uživatele služby Azure AD a heslo uživatele. Azure SQL Database provádí ověřování. Tato metoda nepodporuje vícefaktorové ověřování.




## <a name="prepare-c-parameter-values-from-the-azure-portal"></a>Příprava C# hodnoty parametrů z webu Azure portal

Pro úspěšného spuštění programu jazyka C# je nutné přiřadit odpovídajícími hodnotami pro následující statické pole. Tato statická pole fungují jako parametry do programu. Pole se tady zobrazí pretend hodnotami. Umístění na webu Azure Portal ze kde lze získat odpovídajícími hodnotami jsou také uvedeny:


| Statické pole název | Předstírají, že hodnota | Pokud na webu Azure portal |
| :---------------- | :------------ | :-------------------- |
| Az_SQLDB_svrName | "my Oblíbené sqldb-svr.database.windows.net" | **SQL servery** &gt; **filtrovat podle názvu** |
| AzureAD_UserID | user9@abc.onmicrosoft.com | **Azure Active Directory** &gt; **uživatele** &gt; **nový uživatel typu Host** |
| Initial_DatabaseName | "hlavní" | **SQL servery** &gt; **databáze SQL** |
| ClientApplicationID | "a94f9c62-97fe-4d19-b06d-111111111111" | **Azure Active Directory** &gt; **registrace aplikací**<br /> &nbsp; &nbsp; &gt; **Hledat podle názvu** &gt; **ID aplikace** |
| Identifikátor URI pro přesměrování | nový identifikátor Uri ("https://bing.com/") | **Azure Active Directory** &gt; **registrace aplikací**<br /> &nbsp; &nbsp; &gt; **Hledat podle názvu** &gt; *[Your-App-regis]* &gt;<br /> &nbsp; &nbsp; **Nastavení** &gt; **RedirectURIs**<br /><br />Libovolná platná hodnota pro účely tohoto článku je v pořádku pro identifikátor URI pro přesměrování. V našem případě není skutečně použita hodnota. |
| &nbsp; | &nbsp; | &nbsp; |


V závislosti na konkrétním scénáři nemusí hodnoty všech parametrů v předchozí tabulce.




## <a name="run-ssms-to-verify"></a>Spusťte aplikaci SSMS k ověření

Je vhodné spustit SQL Server Management Studio (SSMS) před spuštěním programu jazyka C#. SSMS spusťte ověří správnost různé konfigurace. Jakékoli neúspěchy programu jazyka C# může být potom zúží právě zdrojového kódu.


#### <a name="verify-sql-database-firewall-ip-addresses"></a>Zkontrolujte IP adresy brány firewall SQL Database

Spusťte aplikaci SSMS ze stejného počítače ve stejné budově, později spustíte program C#. Můžete použít podle toho, co **ověřování** režimu máte pocit, že je nejjednodušší. Pokud jakoukoli indikaci toho, že brány firewall serveru databáze nepřijímá vaše IP adresa se můžete vyřešit tím, jak je znázorněno v [pravidla brány firewall na úrovni serveru a databáze Azure SQL Database](sql-database-firewall-configure.md).


#### <a name="verify-multi-factor-authentication-mfa-for-azure-ad"></a>Ověření služby Multi-Factor authentication (MFA) pro službu Azure AD

Spusťte aplikaci SSMS znovu, tentokrát s **ověřování** nastavena na **univerzální podporující vícefaktorové ověřování služby Active Directory –**. Pro tuto možnost musíte mít aplikaci SSMS verze 17,5 nebo novější.

Další informace najdete v tématu [ověřování službou Multi-Factor Authentication konfigurovat pro aplikace SSMS a Azure AD](sql-database-ssms-mfa-authentication-configure.md).




## <a name="c-code-example"></a>Příklad kódu jazyka C#

Chcete-li zkompilovat tento příklad jazyka C#, je nutné přidat odkaz na sestavení knihovny DLL s názvem **Microsoft.IdentityModel.Clients.ActiveDirectory**.


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

#### <a name="second-half-of-the-c-program"></a>Druhé polovině programu jazyka C#

Pro lepší zobrazení vizuálu programu jazyka C# je rozdělený do dvou bloků kódu. Ke spuštění programu, vložte bloky kódu dvě společně.

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

&nbsp;


## <a name="next-steps"></a>Další postup

- [Get-AzureRmSqlServerActiveDirectoryAdministrator](https://docs.microsoft.com/powershell/module/azurerm.sql/get-azurermsqlserveractivedirectoryadministrator)

