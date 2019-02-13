---
title: 'Funkce Always Encrypted: SQL Database – trezor klíčů Azure | Dokumentace Microsoftu'
description: Tento článek popisuje, jak k zabezpečení citlivých dat ve službě SQL database s šifrováním dat pomocí Průvodce vždycky šifrovaná v SQL Server Management Studio.
keywords: šifrování dat, šifrovací klíč cloudu šifrování
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: VanMSFT
ms.author: vanto
ms.reviewer: ''
manager: craigg
ms.date: 01/03/2019
ms.openlocfilehash: 670bdd43a4a581f349ca84c17ead67975fa0232e
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/12/2019
ms.locfileid: "56110162"
---
# <a name="always-encrypted-protect-sensitive-data-and-store-encryption-keys-in-azure-key-vault"></a>Funkce Always Encrypted: Ochrana citlivých dat a ukládání šifrovacích klíčů ve službě Azure Key Vault

Tento článek popisuje, jak k zabezpečení citlivých dat ve službě SQL database s použitím šifrování dat [vždy šifrována průvodce](https://msdn.microsoft.com/library/mt459280.aspx) v [SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/hh213248.aspx). Obsahuje také pokyny, které se seznámíte se způsobem ukládání každý šifrovací klíč ve službě Azure Key Vault.

Funkce Always Encrypted je novou technologií šifrování dat v Azure SQL Database a SQL Server, který pomáhá chránit citlivá data v klidovém stavu na serveru, při přesouvání mezi klientem a serverem, a když jsou data používá. Vždy šifrovaný zajistí, že citlivá data nezobrazí jako prostý text v databázi systému. Po dokončení konfigurace šifrování dat pouze klientských aplikací nebo serverů aplikace, které mají přístup ke klíčům můžou k datům ve formátu prostého textu. Podrobné informace najdete v tématu [(databázový stroj) s funkcí Always Encrypted](https://msdn.microsoft.com/library/mt163865.aspx).

Po nakonfigurování databáze, kterou chcete používat s funkcí Always Encrypted, vytvoříte klientskou aplikaci v jazyce C# pomocí sady Visual Studio pro práci s šifrovaná data.

Postupujte podle kroků v tomto článku a zjistěte, jak nastavit pro službu Azure SQL database s funkcí Always Encrypted. V tomto článku se dozvíte, jak provádět následující úlohy:

* Pomocí Průvodce funkcí Always Encrypted v aplikaci SSMS vytvořit [klíčů s funkcí Always Encrypted](https://msdn.microsoft.com/library/mt163865.aspx#Anchor_3).
  * Vytvoření [hlavního klíče sloupce (CMK)](https://msdn.microsoft.com/library/mt146393.aspx).
  * Vytvoření [šifrovacího klíče sloupce (CEK)](https://msdn.microsoft.com/library/mt146372.aspx).
* Vytvořit tabulku databáze a šifrování sloupce.
* Vytvořte aplikaci, která vloží, vybere a zobrazí data z šifrované sloupce.

## <a name="prerequisites"></a>Požadavky
Pro účely tohoto kurzu budete potřebovat:

* Účet a předplatné Azure. Pokud ho nemáte, zaregistrovat [bezplatnou zkušební verzi](https://azure.microsoft.com/pricing/free-trial/).
* [SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) verze 13.0.700.242 nebo novější.
* [Rozhraní .NET framework 4.6](https://msdn.microsoft.com/library/w0x726c2.aspx) nebo novějším (v klientském počítači).
* Sadu [Visual Studio](https://www.visualstudio.com/downloads/download-visual-studio-vs.aspx).
* [Prostředí Azure PowerShell](/powershell/azure/overview), verze 1.0 nebo novější. Typ **(Get-modul azure - ListAvailable). Verze** zobrazíte, jakou verzi Powershellu používáte.

## <a name="enable-your-client-application-to-access-the-sql-database-service"></a>Povolit klientské aplikaci přístup ke službě SQL Database
Je nutné povolit klientské aplikace pro přístup ke službě SQL Database nastavením aplikace Azure Active Directory (AAD) a kopírování *ID aplikace* a *klíč* , budete muset ověřování vaší aplikace.

Chcete-li získat *ID aplikace* a *klíč*, postupujte podle kroků v [vytvoření službu Azure Active Directory a instančního objektu, který má přístup k prostředkům](../active-directory/develop/howto-create-service-principal-portal.md).

## <a name="create-a-key-vault-to-store-your-keys"></a>Vytvoření trezoru klíčů pro ukládání klíčů
Teď, když vaše klientské aplikace je nakonfigurovaná a máte ID vaší aplikace, je čas vytvořit trezor klíčů a nakonfigurujte jeho zásad přístupu tak, aby vám nebo vaší aplikace můžete přístup k tajným klíčům v trezoru (s funkcí Always Encrypted klíče). *Vytvořit*, *získat*, *seznamu*, *přihlašování*, *ověřte*, *wrapKey*, a *unwrapKey* oprávnění se vyžadují pro vytvoření nové hlavního klíče sloupce a pro nastavení šifrování v systému SQL Server Management Studio.

Spuštěním následujícího skriptu můžete rychle vytvořit trezor klíčů. Podrobné vysvětlení těchto rutin a další informace o vytváření a konfiguraci služby key vault najdete v tématu [co je Azure Key Vault?](../key-vault/key-vault-overview.md).

```powershell
    $subscriptionName = '<your Azure subscription name>'
    $userPrincipalName = '<username@domain.com>'
    $applicationId = '<application ID from your AAD application>'
    $resourceGroupName = '<resource group name>'
    # Use the same resource group name when creating your SQL Database below
    $location = '<datacenter location>'
    $vaultName = 'AeKeyVault'


    Connect-AzureRmAccount
    $subscriptionId = (Get-AzureRmSubscription -SubscriptionName $subscriptionName).Id
    Set-AzureRmContext -SubscriptionId $subscriptionId

    New-AzureRmResourceGroup -Name $resourceGroupName -Location $location
    New-AzureRmKeyVault -VaultName $vaultName -ResourceGroupName $resourceGroupName -Location $location

    Set-AzureRmKeyVaultAccessPolicy -VaultName $vaultName -ResourceGroupName $resourceGroupName -PermissionsToKeys create,get,wrapKey,unwrapKey,sign,verify,list -UserPrincipalName $userPrincipalName
    Set-AzureRmKeyVaultAccessPolicy  -VaultName $vaultName  -ResourceGroupName $resourceGroupName -ServicePrincipalName $applicationId -PermissionsToKeys get,wrapKey,unwrapKey,sign,verify,list
```



## <a name="create-a-blank-sql-database"></a>Vytvoření prázdné databáze SQL
1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/).
2. Přejděte na **vytvořit prostředek** > **databází** > **SQL Database**.
3. Vytvoření **prázdné** databázi s názvem **Clinic** na nový nebo existující server. Podrobné pokyny o tom, jak vytvořit databázi na webu Azure Portal najdete v části [první databáze Azure SQL database](sql-database-single-database-get-started.md).
   
    ![Vytvoření prázdné databáze](./media/sql-database-always-encrypted-azure-key-vault/create-database.png)

Budete potřebovat připojení řetězec později v tomto kurzu, takže po vytvoření databáze, přejděte do nové databáze Clinic a zkopírujte připojovací řetězec. Kdykoli můžete získat připojovací řetězec, ale snadno ho zkopírovat na webu Azure Portal.

1. Přejděte na **databází SQL** > **Clinic** > **zobrazit databázové připojovací řetězce**.
2. Zkopírujte připojovací řetězec pro **ADO.NET**.
   
    ![Zkopírování připojovacího řetězce](./media/sql-database-always-encrypted-azure-key-vault/connection-strings.png)

## <a name="connect-to-the-database-with-ssms"></a>Připojit se k databázi pomocí SSMS
Otevřete SSMS a připojte se k serveru databáze Clinic.

1. Otevřete aplikaci SSMS. (Přejít na **připojit** > **databázový stroj** otevřít **připojit k serveru** okno, pokud není otevřený.)
2. Zadejte název serveru a přihlašovací údaje. Název serveru najdete v okně databáze SQL a v připojovacím řetězci jste si zkopírovali dříve. Zadejte úplný název serveru, včetně *database.windows.net*.
   
    ![Zkopírování připojovacího řetězce](./media/sql-database-always-encrypted-azure-key-vault/ssms-connect.png)

Pokud **nové pravidlo brány Firewall** otevře se okno přihlášení do Azure a umožňují SSMS vytvořit za vás nové pravidlo brány firewall.

## <a name="create-a-table"></a>Vytvoření tabulky
V této části vytvoříte tabulku pro uložení dat o pacientech. Není původně zašifrována – můžete nakonfigurovat šifrování v další části.

1. Rozbalte **databází**.
2. Klikněte pravým tlačítkem myši **Clinic** databáze a klikněte na tlačítko **nový dotaz**.
3. Vložte následující příkazů jazyka Transact-SQL (T-SQL) do nové okno dotazu a **Execute** ho.

```sql
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
```

## <a name="encrypt-columns-configure-always-encrypted"></a>Šifrování sloupců (Konfigurace funkce Always Encrypted)
SSMS poskytuje průvodce, který vám pomůže snadno nakonfigurovat funkce Always Encrypted s nastavením hlavního klíče sloupce, šifrovací klíč sloupce a šifrované sloupce za vás.

1. Rozbalte **databází** > **Clinic** > **tabulky**.
2. Klikněte pravým tlačítkem myši **pacientů** tabulce a vybrat **šifrování sloupce** otevřete Průvodce funkcí Always Encrypted:
   
    ![Šifrování sloupců](./media/sql-database-always-encrypted-azure-key-vault/encrypt-columns.png)

Průvodce funkcí Always Encrypted obsahuje následující oddíly: **Výběr sloupce**, **konfigurace hlavního klíče**, **ověření**, a **Souhrn**.

### <a name="column-selection"></a>Výběr sloupce
Klikněte na tlačítko **Další** na **ÚVOD** otevřít stránku **výběr sloupce** stránky. Na této stránce se vybrat sloupce, které chcete šifrovat, [typ šifrování a jaké šifrovací klíč sloupce (CEK)](https://msdn.microsoft.com/library/mt459280.aspx#Anchor_2) používat.

Šifrování **SSN** a **datum narození** informace pro každého pacienta. Sloupec SSN použije deterministického šifrování, které podporuje vyhledávání rovnosti, spojení a seskupení podle. Sloupec Datum narození použije náhodné šifrování, která nepodporuje operace.

Nastavte **typ šifrování** tento sloupec SSN **Deterministic** a datum narození sloupec, který se **Randomized**. Klikněte na **Další**.

![Šifrování sloupců](./media/sql-database-always-encrypted-azure-key-vault/column-selection.png)

### <a name="master-key-configuration"></a>Konfigurace hlavního klíče
**Konfigurace hlavního klíče** stránky je, když nastavení vašeho CMK a vybrat poskytovatele úložiště klíčů ukládat CMK. V současné době můžete uložit CMK v úložišti certifikátů Windows, Azure Key Vault nebo modulu hardwarového zabezpečení (HSM).

Tento kurz ukazuje, jak ukládat klíče ve službě Azure Key Vault.

1. Vyberte **službou Azure Key Vault**.
2. Z rozevíracího seznamu vyberte požadované služby key vault.
3. Klikněte na **Další**.

![Konfigurace hlavního klíče](./media/sql-database-always-encrypted-azure-key-vault/master-key-configuration.png)

### <a name="validation"></a>Ověření
Můžete teď šifrování sloupce nebo uložit skript prostředí PowerShell spustit později. Pro účely tohoto kurzu vyberte **pokračovat, dokončete nyní** a klikněte na tlačítko **Další**.

### <a name="summary"></a>Souhrn
Ověřte, zda jsou správně nastavení a klikněte na tlačítko **Dokončit** k dokončení instalace pro Always Encrypted.

![Souhrn](./media/sql-database-always-encrypted-azure-key-vault/summary.png)

### <a name="verify-the-wizards-actions"></a>Ověření akce průvodce
Po dokončení průvodce si vaše databáze je nastavený pro Always Encrypted. Průvodce provést následující akce:

* Vytvoření hlavního klíče sloupce a uložené ve službě Azure Key Vault.
* Vytvoří šifrovací klíč sloupce a uloží ve službě Azure Key Vault.
* Nakonfigurovat vybrané sloupce pro šifrování. V tabulce pacientů aktuálně nemá žádná data, ale je zašifrovaný všechna existující data ve vybraných sloupcích.

Vytvoření klíče v aplikaci SSMS můžete ověřit tak, že rozbalíte **Clinic** > **zabezpečení** > **vždy šifrované klíče**.

## <a name="create-a-client-application-that-works-with-the-encrypted-data"></a>Vytvořit klientskou aplikaci, která funguje s šifrovaná data
Teď, když je nastavený s funkcí Always Encrypted, můžete vytvořit aplikaci, která provádí *vloží* a *vybere* pro šifrované sloupce.  

> [!IMPORTANT]
> Vaše aplikace musí používat [SqlParameter](https://msdn.microsoft.com/library/system.data.sqlclient.sqlparameter.aspx) objekty při předávání dat ve formátu prostého textu na server s funkcí Always Encrypted sloupce. Předání hodnoty literálu bez použití SqlParameter objekty povede k výjimce.
> 
> 

1. Otevřete Visual Studio a vytvořte nový C# **konzolovou aplikaci** (Visual Studio 2015 a starší) nebo **Konzolová aplikace (.NET Framework)** (Visual Studio 2017 a novější). Ujistěte se, že váš projekt je nastavena na **rozhraní .NET Framework 4.6** nebo novější.
2. Pojmenujte projekt **AlwaysEncryptedConsoleAKVApp** a klikněte na tlačítko **OK**.
3. Nainstalujte následující balíčky NuGet tak, že přejdete do **nástroje** > **Správce balíčků NuGet** > **Konzola správce balíčků**.

Spusťte následující dva řádky kódu v konzole Správce balíčků.

```powershell
    Install-Package Microsoft.SqlServer.Management.AlwaysEncrypted.AzureKeyVaultProvider
    Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory
```


## <a name="modify-your-connection-string-to-enable-always-encrypted"></a>Upravit připojovací řetězec k povolení funkcí Always Encrypted
Tato část vysvětluje, jak povolit Always Encrypted připojovací řetězec databáze.

Pokud chcete povolit funkce Always Encrypted, budete muset přidat **nastavení šifrování sloupce** – klíčové slovo připojení řetězce a nastavte ho na **povoleno**.

Tento parametr můžete nastavit přímo v připojovacím řetězci, nebo ji můžete nastavit pomocí [SqlConnectionStringBuilder](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnectionstringbuilder.aspx). Ukázková aplikace v další části ukazuje, jak používat **SqlConnectionStringBuilder**.

### <a name="enable-always-encrypted-in-the-connection-string"></a>Povolení funkce Always Encrypted v připojovacím řetězci
Přidejte následující klíčové slovo do připojovacího řetězce.

    Column Encryption Setting=Enabled


### <a name="enable-always-encrypted-with-sqlconnectionstringbuilder"></a>Povolit Always Encrypted s SqlConnectionStringBuilder
Následující kód ukazuje, jak povolit funkce Always Encrypted s nastavením [SqlConnectionStringBuilder.ColumnEncryptionSetting](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnectionstringbuilder.columnencryptionsetting.aspx) k [povoleno](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnectioncolumnencryptionsetting.aspx).

```CS
    // Instantiate a SqlConnectionStringBuilder.
    SqlConnectionStringBuilder connStringBuilder =
       new SqlConnectionStringBuilder("replace with your connection string");

    // Enable Always Encrypted.
    connStringBuilder.ColumnEncryptionSetting =
       SqlConnectionColumnEncryptionSetting.Enabled;
```

## <a name="register-the-azure-key-vault-provider"></a>Zaregistrujte zprostředkovatele služby Azure Key Vault
Následující kód ukazuje, jak se zaregistrovat poskytovatele služby Azure Key Vault pomocí ovladače ADO.NET.

```C#
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
```

## <a name="always-encrypted-sample-console-application"></a>Always Encrypted ukázková Konzolová aplikace
Tato ukázka předvádí, jak:

* Upravte připojovací řetězec k povolení funkcí Always Encrypted.
* Zaregistrujte se jako poskytovatele úložiště klíčů aplikace Azure Key Vault.  
* Vložení dat do šifrované sloupce.
* Vyberte záznam pomocí filtrování pro určitou hodnotu v šifrované sloupce.

Nahraďte obsah **Program.cs** následujícím kódem. Nahraďte připojovací řetězec connectionString globální proměnné na řádku, který přímo předchází metoda Main s platným připojovacím řetězcem z webu Azure portal. Toto je jediná změna, kterou je třeba provést na tento kód.

Spusťte aplikaci v akci najdete v článku s funkcí Always Encrypted.
```CS
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
```


## <a name="verify-that-the-data-is-encrypted"></a>Ověřte, že se data zašifrují.
Můžete rychle zkontrolovat, že skutečná data na serveru se šifrují dotazování na data pacientů pomocí aplikace SSMS (pomocí aktuálního připojení ve kterém **nastavení šifrování sloupce** zatím není povolená).

Spusťte následující dotaz na databázi Clinic.

```sql
    SELECT FirstName, LastName, SSN, BirthDate FROM Patients;
```

Uvidíte, že šifrované sloupce nebudou obsahovat žádná data ve formátu prostého textu.

   ![Novou konzolovou aplikaci](./media/sql-database-always-encrypted-azure-key-vault/ssms-encrypted.png)

Použití SSMS k přístupu k datům ve formátu prostého textu, je nejprve potřeba zajistit, že uživatel má příslušná oprávnění ke službě Azure Key Vault: *získat*, *unwrapKey*, a *ověřte*. Podrobné informace najdete v tématu [vytvořit a hlavních klíčů sloupce pro Store (Always Encrypted)](https://docs.microsoft.com/sql/relational-databases/security/encryption/create-and-store-column-master-keys-always-encrypted?view=sql-server-2017).

Pak přidejte *nastavení šifrování sloupce = povoleno* parametr během připojení.

1. V aplikaci SSMS klikněte pravým tlačítkem na váš server v **Průzkumník objektů** a zvolte **odpojit**.
2. Klikněte na tlačítko **připojit** > **databázový stroj** otevřít **připojit k serveru** okno a klikněte na tlačítko **možnosti**.
3. Klikněte na tlačítko **další parametry připojení** a typ **nastavení šifrování sloupce = povoleno**.
   
    ![Novou konzolovou aplikaci](./media/sql-database-always-encrypted-azure-key-vault/ssms-connection-parameter.png)
4. Spusťte následující dotaz na databázi Clinic.

   ```sql
      SELECT FirstName, LastName, SSN, BirthDate FROM Patients;
   ```

     Nyní je vidět data ve formátu prostého textu v šifrované sloupce.
     ![Novou konzolovou aplikaci](./media/sql-database-always-encrypted-azure-key-vault/ssms-plaintext.png)


## <a name="next-steps"></a>Další postup
Po vytvoření databáze s použitím funkce Always Encrypted, může být vhodné provést následující kroky:

* [Otočit a vyčištění klíče](https://msdn.microsoft.com/library/mt607048.aspx).
* [Migrovat data, která je už zašifrovali pomocí funkce Always Encrypted](https://msdn.microsoft.com/library/mt621539.aspx).

## <a name="related-information"></a>Související informace
* [Funkce Always Encrypted (vývoj pro klientské)](https://msdn.microsoft.com/library/mt147923.aspx)
* [Transparentní šifrování dat](https://msdn.microsoft.com/library/bb934049.aspx)
* [Šifrování SQL serveru](https://msdn.microsoft.com/library/bb510663.aspx)
* [Always Encrypted Průvodce](https://msdn.microsoft.com/library/mt459280.aspx)
* [Blog Always Encrypted](https://blogs.msdn.com/b/sqlsecurity/archive/tags/always-encrypted/)

