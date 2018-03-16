---
title: "Funkce Always Encrypted: SQL Database – Azure Key Vault | Microsoft Docs"
description: "Tento článek ukazuje, jak zajistit citlivá data v databázi SQL s šifrování dat pomocí Průvodce vždycky šifrovaná v aplikaci SQL Server Management Studio."
keywords: "šifrování dat, šifrovací klíč, šifrování cloudu"
services: sql-database
author: stevestein
manager: craigg
ms.service: sql-database
ms.custom: security
ms.topic: article
ms.date: 03/06/2017
ms.author: sstein
ms.openlocfilehash: 3525355a7f997a5dcc54d14f216d0d647aef8407
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/16/2018
---
# <a name="always-encrypted-protect-sensitive-data-in-sql-database-and-store-your-encryption-keys-in-azure-key-vault"></a>Funkce Always Encrypted: Chrání citlivá data v databázi SQL a ukládat šifrovací klíče v Azure Key Vault

V tomto článku se dozvíte, jak zajistit citlivá data v databázi SQL s použitím šifrování dat [vždy šifrované průvodce](https://msdn.microsoft.com/library/mt459280.aspx) v [SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/hh213248.aspx). Zahrnuje také pokyny, které vám ukáže, jak ukládat každý šifrovací klíč v Azure Key Vault.

Vždy šifrovaný je nová technologie šifrování dat v Azure SQL Database a SQL Server, který pomáhá chránit citlivá data v klidovém stavu na serveru během pohybu mezi klientem a serverem, a když data právě používá. Vždy šifrovaný zajistí, že citlivá data nikdy zobrazí jako prostý text v databázi systému. Po dokončení konfigurace šifrování dat, můžete přístup jenom klientské aplikace nebo aplikační servery, které mají přístup ke klíčům data ve formátu prostého textu. Podrobné informace najdete v tématu [vždycky šifrovaná (databázový stroj)](https://msdn.microsoft.com/library/mt163865.aspx).

Po dokončení konfigurace databáze pro použití funkce Always Encrypted, vytvoříte klientskou aplikaci v jazyce C# pomocí sady Visual Studio pro práci s šifrovaná data.

Postupujte podle kroků v tomto článku a zjistěte, jak nastavit vždy šifrována pro Azure SQL database. V tomto článku se dozvíte, jak provádět následující úlohy:

* Pomocí Průvodce vždycky šifrovaná v aplikaci SSMS vytvořit [vždy šifrována klíče](https://msdn.microsoft.com/library/mt163865.aspx#Anchor_3).
  * Vytvoření [k hlavnímu klíči sloupce (CMK)](https://msdn.microsoft.com/library/mt146393.aspx).
  * Vytvoření [šifrovací klíč sloupce (CEK)](https://msdn.microsoft.com/library/mt146372.aspx).
* Vytvořit tabulku databáze a šifrování sloupců.
* Vytvořte aplikaci, která vloží, vybere a zobrazí data z šifrované sloupce.

## <a name="prerequisites"></a>Požadavky
V tomto kurzu budete potřebovat:

* Účet a předplatné Azure. Pokud nemáte, si zaregistrovat [bezplatnou zkušební verzi](https://azure.microsoft.com/pricing/free-trial/).
* [SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) verze 13.0.700.242 nebo novější.
* [Rozhraní .NET framework 4.6](https://msdn.microsoft.com/library/w0x726c2.aspx) nebo novější (v klientském počítači).
* Sadu [Visual Studio](https://www.visualstudio.com/downloads/download-visual-studio-vs.aspx).
* [Prostředí Azure PowerShell](/powershell/azure/overview), verze 1.0 nebo novější. Typ **(Get-Module azure - ListAvailable). Verze** zobrazíte, jaká verze prostředí PowerShell, kterou používáte.

## <a name="enable-your-client-application-to-access-the-sql-database-service"></a>Povolit klientské aplikaci přístup ke službě SQL Database
Je nutné povolit klientské aplikaci přístup ke službě SQL Database nastavením aplikace Azure Active Directory (AAD) a kopírování *ID aplikace* a *klíč* , budete muset ověření vaší aplikace.

Chcete-li získat *ID aplikace* a *klíč*, postupujte podle kroků v [vytvořit Azure Active Directory objekt aplikací a služeb, které mají přístup k prostředkům](../azure-resource-manager/resource-group-create-service-principal-portal.md).

## <a name="create-a-key-vault-to-store-your-keys"></a>Vytvoření trezoru klíčů k ukládání svých klíčů
Teď, když je nastavené vaší klientské aplikace a máte vaše ID aplikace, je čas na vytvoření trezoru klíčů a nakonfigurujte jeho zásady přístupu tak, aby vám a vaší aplikace mohou přistupovat k trezoru tajné klíče (Always Encrypted klíče). *Vytvořit*, *získat*, *seznamu*, *přihlašovací*, *ověřte*, *wrapKey*, a *unwrapKey* oprávnění jsou nutné pro vytvoření nové k hlavnímu klíči sloupce a nastavení šifrování s SQL Server Management Studio.

Spuštěním následujícího skriptu můžete rychle vytvořit trezoru klíčů. Podrobné vysvětlení těchto rutin a další informace o vytváření a konfiguraci trezoru klíčů najdete v tématu [Začínáme s Azure Key Vault](../key-vault/key-vault-get-started.md).

    $subscriptionName = '<your Azure subscription name>'
    $userPrincipalName = '<username@domain.com>'
    $applicationId = '<application ID from your AAD application>'
    $resourceGroupName = '<resource group name>'
    $location = '<datacenter location>'
    $vaultName = 'AeKeyVault'


    Login-AzureRmAccount
    $subscriptionId = (Get-AzureRmSubscription -SubscriptionName $subscriptionName).Id
    Set-AzureRmContext -SubscriptionId $subscriptionId

    New-AzureRmResourceGroup -Name $resourceGroupName -Location $location
    New-AzureRmKeyVault -VaultName $vaultName -ResourceGroupName $resourceGroupName -Location $location

    Set-AzureRmKeyVaultAccessPolicy -VaultName $vaultName -ResourceGroupName $resourceGroupName -PermissionsToKeys create,get,wrapKey,unwrapKey,sign,verify,list -UserPrincipalName $userPrincipalName
    Set-AzureRmKeyVaultAccessPolicy  -VaultName $vaultName  -ResourceGroupName $resourceGroupName -ServicePrincipalName $applicationId -PermissionsToKeys get,wrapKey,unwrapKey,sign,verify,list




## <a name="create-a-blank-sql-database"></a>Vytvoření prázdné databáze SQL
1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/).
2. Přejděte na **vytvořit prostředek** > **databáze** > **SQL Database**.
3. Vytvoření **prázdné** databáze s názvem **Klinika** na nový nebo existující server. Podrobné pokyny o tom, jak vytvořit databázi na portálu Azure, najdete v části [svoji první databázi Azure SQL](sql-database-get-started-portal.md).
   
    ![Vytvoření prázdné databáze](./media/sql-database-always-encrypted-azure-key-vault/create-database.png)

Budete potřebovat připojení řetězec později v tomto kurzu, takže po vytvoření databáze, přejděte k nové databázi Klinika a zkopírujte připojovací řetězec. Kdykoli můžete získat připojovací řetězec, ale je snadné a zkopírujte ho na portálu Azure.

1. Přejděte na **databází SQL** > **Klinika** > **zobrazit databázové připojovací řetězce**.
2. Zkopírujte připojovací řetězec pro **ADO.NET**.
   
    ![Zkopírujte připojovací řetězec](./media/sql-database-always-encrypted-azure-key-vault/connection-strings.png)

## <a name="connect-to-the-database-with-ssms"></a>Připojit se k databázi pomocí SSMS
Otevřete aplikaci SSMS a připojení k serveru s databází Klinika.

1. Otevřete aplikaci SSMS. (Přejít na **připojit** > **databázový stroj** otevřete **připojit k serveru** okno, pokud není otevřený.)
2. Zadejte název serveru a přihlašovací údaje. Název serveru naleznete v okně databáze SQL a v připojovacím řetězci jste zkopírovali dříve. Zadejte úplný název serveru, včetně *database.windows.net*.
   
    ![Zkopírujte připojovací řetězec](./media/sql-database-always-encrypted-azure-key-vault/ssms-connect.png)

Pokud **nové pravidlo brány Firewall** okno otevře, přihlaste k Azure a umožňují SSMS za vás vytvořit nové pravidlo brány firewall.

## <a name="create-a-table"></a>Vytvoření tabulky
V této části vytvoříte tabulku pro uložení pacienta data. Není původně zašifrována – budete konfigurovat šifrování v další části.

1. Rozbalte položku **databáze**.
2. Klikněte pravým tlačítkem myši **Klinika** databáze a klikněte na tlačítko **nový dotaz**.
3. Vložte následující Transact-SQL (T-SQL) do nového okna dotazu a **Execute** ho.

        CREATE TABLE [dbo].[Patients](
         [PatientId] [int] IDENTITY(1,1),
         [SSN] [char](11) NOT NULL,
         [FirstName] [nvarchar](50) NULL,
         [LastName] [nvarchar](50) NULL,
         [MiddleName] [nvarchar](50) NULL,
         [StreetAddress] [nvarchar](50) NULL,
         [City] [nvarchar](50) NULL,
         [ZipCode] [char](5) NULL,
         [State] [char](2) NULL,
         [BirthDate] [date] NOT NULL
         PRIMARY KEY CLUSTERED ([PatientId] ASC) ON [PRIMARY] );
         GO


## <a name="encrypt-columns-configure-always-encrypted"></a>Šifrování sloupců (nakonfigurujte funkce Always Encrypted)
Aplikace SSMS poskytuje průvodce, který vám pomůže snadno nakonfigurovat tak, že k hlavnímu klíči sloupce, šifrovací klíč sloupce a šifrované sloupce můžete vždy šifrována.

1. Rozbalte položku **databáze** > **Klinika** > **tabulky**.
2. Klikněte pravým tlačítkem myši **pacientů** tabulky a vyberte **šifrování sloupců** otevřete Průvodce vždycky šifrovaná:
   
    ![Šifrování sloupců](./media/sql-database-always-encrypted-azure-key-vault/encrypt-columns.png)

Průvodce vždy šifrována obsahuje následující oddíly: **sloupců výběru**, **konfigurace hlavního klíče**, **ověření**, a **Souhrn**.

### <a name="column-selection"></a>Výběr sloupce
Klikněte na tlačítko **Další** na **ÚVOD** otevřít stránku **sloupců výběru** stránky. Na této stránce se vybrat sloupce, které chcete šifrovat, [typ šifrování a jaké šifrovací klíč sloupce (CEK)](https://msdn.microsoft.com/library/mt459280.aspx#Anchor_2) používat.

Šifrování **SSN** a **datum narození** informace pro každý pacienta. Sloupec SSN použije deterministickou šifrování, která podporuje rovnosti vyhledávání, spojení a seskupit podle. Sloupec Datum narození použije náhodnou šifrování, která nepodporuje operace.

Nastavte **typ šifrování** sloupce SSN **Deterministic** a sloupci Datum narození **Randomized**. Klikněte na **Další**.

![Šifrování sloupců](./media/sql-database-always-encrypted-azure-key-vault/column-selection.png)

### <a name="master-key-configuration"></a>Konfigurace hlavního klíče
**Hlavní klíč konfigurace** stránka je kde nastavení vaší CMK a vybrat zprostředkovatele úložiště klíčů uložení CMK. V současné době můžete uložit CMK v úložišti certifikátů systému Windows, Azure Key Vault nebo modul hardwarového zabezpečení (HSM).

Tento kurz ukazuje, jak uložit vaše klíče do Azure Key Vault.

1. Vyberte **Azure Key Vault**.
2. V rozevíracím seznamu vyberte požadovaný trezoru klíčů.
3. Klikněte na **Další**.

![Konfigurace hlavního klíče](./media/sql-database-always-encrypted-azure-key-vault/master-key-configuration.png)

### <a name="validation"></a>Ověření
Můžete teď šifrování sloupců nebo uložení skriptu prostředí PowerShell spustit později. V tomto kurzu vyberte **přejít k dokončení teď** a klikněte na tlačítko **Další**.

### <a name="summary"></a>Souhrn
Ověřte, zda jsou všechny správné nastavení a klikněte na tlačítko **Dokončit** dokončit nastavení pro vždy šifrována.

![Souhrn](./media/sql-database-always-encrypted-azure-key-vault/summary.png)

### <a name="verify-the-wizards-actions"></a>Ověřte v Průvodci akce
Po dokončení Průvodce vaše databáze je nastavena pro vždy šifrována. Průvodce provést následující akce:

* K hlavnímu klíči sloupce vytvoří a uloží ji do Azure Key Vault.
* Šifrovací klíč sloupce vytvoří a uloží ji do Azure Key Vault.
* Nakonfigurovat vybrané sloupce pro šifrování. Tabulky Pacienti aktuálně neobsahuje žádná data, ale je zašifrovaný jakákoli stávající data ve vybraných sloupcích.

Vytvoření klíče v aplikaci SSMS můžete ověřit rozšířením **Klinika** > **zabezpečení** > **vždy šifrované klíče**.

## <a name="create-a-client-application-that-works-with-the-encrypted-data"></a>Vytvořit klientskou aplikaci, která funguje s šifrovaná data
Teď, když je funkce Always Encrypted nastavili, můžete vytvořit aplikaci, která provádí *vloží* a *vybere* pro šifrované sloupce.  

> [!IMPORTANT]
> Vaše aplikace musí používat [SqlParameter](https://msdn.microsoft.com/library/system.data.sqlclient.sqlparameter.aspx) objekty při předávání dat ve formátu prostého textu na server s vždy šifrované sloupce. Předávání hodnot literál bez použití SqlParameter objekty způsobí výjimku.
> 
> 

1. Otevřete Visual Studio a vytvoření nového jazyka C# **konzolové aplikace** (Visual Studio 2015 a starší) nebo **konzolovou aplikaci (rozhraní .NET Framework)** (Visual Studio 2017 a novější). Ujistěte se, že váš projekt je nastaven na **rozhraní .NET Framework 4.6** nebo novější.
2. Název projektu **AlwaysEncryptedConsoleAKVApp** a klikněte na tlačítko **OK**.
3. Instalace následujících balíčků NuGet přechodem na **nástroje** > **Správce balíčků NuGet** > **Konzola správce balíčků**.

Spusťte tyto dva řádky kódu v konzole Správce balíčků.

    Install-Package Microsoft.SqlServer.Management.AlwaysEncrypted.AzureKeyVaultProvider
    Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory



## <a name="modify-your-connection-string-to-enable-always-encrypted"></a>Upravit připojovací řetězec k povolení funkce Always Encrypted
Tato část vysvětluje, jak povolit funkce Always Encrypted v připojovací řetězec databáze.

Pokud chcete povolit, vždycky šifrovaná, je nutné přidat **nastavení šifrování sloupec** – klíčové slovo k připojení k řetězec a nastavte ji na **povoleno**.

To můžete nastavit přímo v připojovacím řetězci, nebo můžete ho nastavit pomocí [SqlConnectionStringBuilder](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnectionstringbuilder.aspx). Ukázkovou aplikaci v další části ukazuje, jak používat **SqlConnectionStringBuilder**.

### <a name="enable-always-encrypted-in-the-connection-string"></a>Povolení funkce Always Encrypted v připojovacím řetězci
Přidejte následující klíčové slovo do připojovacího řetězce.

    Column Encryption Setting=Enabled


### <a name="enable-always-encrypted-with-sqlconnectionstringbuilder"></a>Povolit vždy šifrován SqlConnectionStringBuilder
Následující kód ukazuje, jak povolit funkce Always Encrypted nastavením [SqlConnectionStringBuilder.ColumnEncryptionSetting](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnectionstringbuilder.columnencryptionsetting.aspx) k [povoleno](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnectioncolumnencryptionsetting.aspx).

    // Instantiate a SqlConnectionStringBuilder.
    SqlConnectionStringBuilder connStringBuilder =
       new SqlConnectionStringBuilder("replace with your connection string");

    // Enable Always Encrypted.
    connStringBuilder.ColumnEncryptionSetting =
       SqlConnectionColumnEncryptionSetting.Enabled;

## <a name="register-the-azure-key-vault-provider"></a>Zaregistrujte zprostředkovatele Azure Key Vault
Následující kód ukazuje, jak zaregistrovat zprostředkovatele Azure Key Vault ovladač technologie ADO.NET.

    private static ClientCredential _clientCredential;

    static void InitializeAzureKeyVaultProvider()
    {
       _clientCredential = new ClientCredential(applicationId, clientKey);

       SqlColumnEncryptionAzureKeyVaultProvider azureKeyVaultProvider =
          new SqlColumnEncryptionAzureKeyVaultProvider(GetToken);

       Dictionary<string, SqlColumnEncryptionKeyStoreProvider> providers =
          new Dictionary<string, SqlColumnEncryptionKeyStoreProvider>();

       providers.Add(SqlColumnEncryptionAzureKeyVaultProvider.ProviderName, azureKeyVaultProvider);
       SqlConnection.RegisterColumnEncryptionKeyStoreProviders(providers);
    }



## <a name="always-encrypted-sample-console-application"></a>Vždy šifrovaný ukázkovou aplikaci konzoly
Tento příklad znázorňuje postup:

* Upravte připojovací řetězec k povolení funkce Always Encrypted.
* Zaregistrujte Azure Key Vault jako zprostředkovatele úložiště klíčů aplikace.  
* Vložení dat do šifrované sloupce.
* Vyberte záznam pomocí filtrování pro konkrétní hodnoty v šifrovaný sloupec.

Nahraďte obsah **Program.cs** následujícím kódem. Nahraďte připojovací řetězec pro globální connectionString proměnné v řádku, který přímo předchází metodu Main platný připojovacím řetězcem z portálu Azure. Toto je pouze změny, které musíte udělat na tento kód.

Spusťte aplikaci v akci zobrazovat vždy šifrována.

    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Text;
    using System.Threading.Tasks;
    using System.Data;
    using System.Data.SqlClient;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    using Microsoft.SqlServer.Management.AlwaysEncrypted.AzureKeyVaultProvider;

    namespace AlwaysEncryptedConsoleAKVApp
    {
    class Program
    {
        // Update this line with your Clinic database connection string from the Azure portal.
        static string connectionString = @"<connection string from the portal>";
        static string applicationId = @"<application ID from your AAD application>";
        static string clientKey = "<key from your AAD application>";


        static void Main(string[] args)
        {
            InitializeAzureKeyVaultProvider();

            Console.WriteLine("Signed in as: " + _clientCredential.ClientId);

            Console.WriteLine("Original connection string copied from the Azure portal:");
            Console.WriteLine(connectionString);

            // Create a SqlConnectionStringBuilder.
            SqlConnectionStringBuilder connStringBuilder =
                new SqlConnectionStringBuilder(connectionString);

            // Enable Always Encrypted for the connection.
            // This is the only change specific to Always Encrypted
            connStringBuilder.ColumnEncryptionSetting =
                SqlConnectionColumnEncryptionSetting.Enabled;

            Console.WriteLine(Environment.NewLine + "Updated connection string with Always Encrypted enabled:");
            Console.WriteLine(connStringBuilder.ConnectionString);

            // Update the connection string with a password supplied at runtime.
            Console.WriteLine(Environment.NewLine + "Enter server password:");
            connStringBuilder.Password = Console.ReadLine();


            // Assign the updated connection string to our global variable.
            connectionString = connStringBuilder.ConnectionString;


            // Delete all records to restart this demo app.
            ResetPatientsTable();

            // Add sample data to the Patients table.
            Console.Write(Environment.NewLine + "Adding sample patient data to the database...");

            InsertPatient(new Patient()
            {
                SSN = "999-99-0001",
                FirstName = "Orlando",
                LastName = "Gee",
                BirthDate = DateTime.Parse("01/04/1964")
            });
            InsertPatient(new Patient()
            {
                SSN = "999-99-0002",
                FirstName = "Keith",
                LastName = "Harris",
                BirthDate = DateTime.Parse("06/20/1977")
            });
            InsertPatient(new Patient()
            {
                SSN = "999-99-0003",
                FirstName = "Donna",
                LastName = "Carreras",
                BirthDate = DateTime.Parse("02/09/1973")
            });
            InsertPatient(new Patient()
            {
                SSN = "999-99-0004",
                FirstName = "Janet",
                LastName = "Gates",
                BirthDate = DateTime.Parse("08/31/1985")
            });
            InsertPatient(new Patient()
            {
                SSN = "999-99-0005",
                FirstName = "Lucy",
                LastName = "Harrington",
                BirthDate = DateTime.Parse("05/06/1993")
            });


            // Fetch and display all patients.
            Console.WriteLine(Environment.NewLine + "All the records currently in the Patients table:");

            foreach (Patient patient in SelectAllPatients())
            {
                Console.WriteLine(patient.FirstName + " " + patient.LastName + "\tSSN: " + patient.SSN + "\tBirthdate: " + patient.BirthDate);
            }

            // Get patients by SSN.
            Console.WriteLine(Environment.NewLine + "Now lets locate records by searching the encrypted SSN column.");

            string ssn;

            // This very simple validation only checks that the user entered 11 characters.
            // In production be sure to check all user input and use the best validation for your specific application.
            do
            {
                Console.WriteLine("Please enter a valid SSN (ex. 999-99-0003):");
                ssn = Console.ReadLine();
            } while (ssn.Length != 11);

            // The example allows duplicate SSN entries so we will return all records
            // that match the provided value and store the results in selectedPatients.
            Patient selectedPatient = SelectPatientBySSN(ssn);

            // Check if any records were returned and display our query results.
            if (selectedPatient != null)
            {
                Console.WriteLine("Patient found with SSN = " + ssn);
                Console.WriteLine(selectedPatient.FirstName + " " + selectedPatient.LastName + "\tSSN: "
                    + selectedPatient.SSN + "\tBirthdate: " + selectedPatient.BirthDate);
            }
            else
            {
                Console.WriteLine("No patients found with SSN = " + ssn);
            }

            Console.WriteLine("Press Enter to exit...");
            Console.ReadLine();
        }


        private static ClientCredential _clientCredential;

        static void InitializeAzureKeyVaultProvider()
        {

            _clientCredential = new ClientCredential(applicationId, clientKey);

            SqlColumnEncryptionAzureKeyVaultProvider azureKeyVaultProvider =
              new SqlColumnEncryptionAzureKeyVaultProvider(GetToken);

            Dictionary<string, SqlColumnEncryptionKeyStoreProvider> providers =
              new Dictionary<string, SqlColumnEncryptionKeyStoreProvider>();

            providers.Add(SqlColumnEncryptionAzureKeyVaultProvider.ProviderName, azureKeyVaultProvider);
            SqlConnection.RegisterColumnEncryptionKeyStoreProviders(providers);
        }

        public async static Task<string> GetToken(string authority, string resource, string scope)
        {
            var authContext = new AuthenticationContext(authority);
            AuthenticationResult result = await authContext.AcquireTokenAsync(resource, _clientCredential);

            if (result == null)
                throw new InvalidOperationException("Failed to obtain the access token");
            return result.AccessToken;
        }

        static int InsertPatient(Patient newPatient)
        {
            int returnValue = 0;

            string sqlCmdText = @"INSERT INTO [dbo].[Patients] ([SSN], [FirstName], [LastName], [BirthDate])
     VALUES (@SSN, @FirstName, @LastName, @BirthDate);";

            SqlCommand sqlCmd = new SqlCommand(sqlCmdText);


            SqlParameter paramSSN = new SqlParameter(@"@SSN", newPatient.SSN);
            paramSSN.DbType = DbType.AnsiStringFixedLength;
            paramSSN.Direction = ParameterDirection.Input;
            paramSSN.Size = 11;

            SqlParameter paramFirstName = new SqlParameter(@"@FirstName", newPatient.FirstName);
            paramFirstName.DbType = DbType.String;
            paramFirstName.Direction = ParameterDirection.Input;

            SqlParameter paramLastName = new SqlParameter(@"@LastName", newPatient.LastName);
            paramLastName.DbType = DbType.String;
            paramLastName.Direction = ParameterDirection.Input;

            SqlParameter paramBirthDate = new SqlParameter(@"@BirthDate", newPatient.BirthDate);
            paramBirthDate.SqlDbType = SqlDbType.Date;
            paramBirthDate.Direction = ParameterDirection.Input;

            sqlCmd.Parameters.Add(paramSSN);
            sqlCmd.Parameters.Add(paramFirstName);
            sqlCmd.Parameters.Add(paramLastName);
            sqlCmd.Parameters.Add(paramBirthDate);

            using (sqlCmd.Connection = new SqlConnection(connectionString))
            {
                try
                {
                    sqlCmd.Connection.Open();
                    sqlCmd.ExecuteNonQuery();
                }
                catch (Exception ex)
                {
                    returnValue = 1;
                    Console.WriteLine("The following error was encountered: ");
                    Console.WriteLine(ex.Message);
                    Console.WriteLine(Environment.NewLine + "Press Enter key to exit");
                    Console.ReadLine();
                    Environment.Exit(0);
                }
            }
            return returnValue;
        }


        static List<Patient> SelectAllPatients()
        {
            List<Patient> patients = new List<Patient>();


            SqlCommand sqlCmd = new SqlCommand(
              "SELECT [SSN], [FirstName], [LastName], [BirthDate] FROM [dbo].[Patients]",
                new SqlConnection(connectionString));


            using (sqlCmd.Connection = new SqlConnection(connectionString))

            using (sqlCmd.Connection = new SqlConnection(connectionString))
            {
                try
                {
                    sqlCmd.Connection.Open();
                    SqlDataReader reader = sqlCmd.ExecuteReader();

                    if (reader.HasRows)
                    {
                        while (reader.Read())
                        {
                            patients.Add(new Patient()
                            {
                                SSN = reader[0].ToString(),
                                FirstName = reader[1].ToString(),
                                LastName = reader["LastName"].ToString(),
                                BirthDate = (DateTime)reader["BirthDate"]
                            });
                        }
                    }
                }
                catch (Exception ex)
                {
                    throw;
                }
            }

            return patients;
        }


        static Patient SelectPatientBySSN(string ssn)
        {
            Patient patient = new Patient();

            SqlCommand sqlCmd = new SqlCommand(
                "SELECT [SSN], [FirstName], [LastName], [BirthDate] FROM [dbo].[Patients] WHERE [SSN]=@SSN",
                new SqlConnection(connectionString));

            SqlParameter paramSSN = new SqlParameter(@"@SSN", ssn);
            paramSSN.DbType = DbType.AnsiStringFixedLength;
            paramSSN.Direction = ParameterDirection.Input;
            paramSSN.Size = 11;

            sqlCmd.Parameters.Add(paramSSN);


            using (sqlCmd.Connection = new SqlConnection(connectionString))
            {
                try
                {
                    sqlCmd.Connection.Open();
                    SqlDataReader reader = sqlCmd.ExecuteReader();

                    if (reader.HasRows)
                    {
                        while (reader.Read())
                        {
                            patient = new Patient()
                            {
                                SSN = reader[0].ToString(),
                                FirstName = reader[1].ToString(),
                                LastName = reader["LastName"].ToString(),
                                BirthDate = (DateTime)reader["BirthDate"]
                            };
                        }
                    }
                    else
                    {
                        patient = null;
                    }
                }
                catch (Exception ex)
                {
                    throw;
                }
            }
            return patient;
        }


        // This method simply deletes all records in the Patients table to reset our demo.
        static int ResetPatientsTable()
        {
            int returnValue = 0;

            SqlCommand sqlCmd = new SqlCommand("DELETE FROM Patients");
            using (sqlCmd.Connection = new SqlConnection(connectionString))
            {
                try
                {
                    sqlCmd.Connection.Open();
                    sqlCmd.ExecuteNonQuery();

                }
                catch (Exception ex)
                {
                    returnValue = 1;
                }
            }
            return returnValue;
        }
    }

    class Patient
    {
        public string SSN { get; set; }
        public string FirstName { get; set; }
        public string LastName { get; set; }
        public DateTime BirthDate { get; set; }
    }
    }



## <a name="verify-that-the-data-is-encrypted"></a>Ověřte, že je šifrovaná data
Můžete rychle zjistit, že skutečná data na serveru je šifrovaná pomocí dotazu na data pacientů pomocí SSMS (pomocí aktuálního připojení kde **nastavení šifrování sloupec** zatím není povolená).

Spuštěním následujícího dotazu v databázi Klinika.

    SELECT FirstName, LastName, SSN, BirthDate FROM Patients;

Uvidíte, že šifrované sloupce neobsahují žádná data ve formátu prostého textu.

   ![Novou konzolovou aplikaci](./media/sql-database-always-encrypted-azure-key-vault/ssms-encrypted.png)

Pomocí aplikace SSMS přístup k datům ve formátu prostého textu, můžete přidat *nastavení šifrování sloupec = povoleno* parametr pro připojení.

1. V aplikaci SSMS, klikněte pravým tlačítkem na váš server v **Průzkumník objektů** a zvolte **odpojení**.
2. Klikněte na tlačítko **připojit** > **databázový stroj** otevřete **připojit k serveru** a klikněte na **možnosti**.
3. Klikněte na tlačítko **další parametry připojení** a typ **nastavení šifrování sloupec = povoleno**.
   
    ![Novou konzolovou aplikaci](./media/sql-database-always-encrypted-azure-key-vault/ssms-connection-parameter.png)
4. Spuštěním následujícího dotazu v databázi Klinika.
   
        SELECT FirstName, LastName, SSN, BirthDate FROM Patients;
   
     Nyní můžete vidět data jako prostý text v šifrované sloupce.

    ![Novou konzolovou aplikaci](./media/sql-database-always-encrypted-azure-key-vault/ssms-plaintext.png)


## <a name="next-steps"></a>Další kroky
Po vytvoření databáze, která používá vždycky šifrovaná, můžete provést následující akce:

* [Otočit a vyčištění klíče](https://msdn.microsoft.com/library/mt607048.aspx).
* [Migraci dat, která už je šifrovaný pomocí funkce Always Encrypted](https://msdn.microsoft.com/library/mt621539.aspx).

## <a name="related-information"></a>Související informace
* [Funkce Always Encrypted (vývoj pro klienta)](https://msdn.microsoft.com/library/mt147923.aspx)
* [Transparentní šifrování dat](https://msdn.microsoft.com/library/bb934049.aspx)
* [Šifrování SQL serveru](https://msdn.microsoft.com/library/bb510663.aspx)
* [Vždy šifrovaný Průvodce](https://msdn.microsoft.com/library/mt459280.aspx)
* [Vždy šifrované blog](http://blogs.msdn.com/b/sqlsecurity/archive/tags/always-encrypted/)

