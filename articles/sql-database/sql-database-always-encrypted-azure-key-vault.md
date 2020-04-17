---
title: Vždy šifrované – trezor klíčů Azure
description: Tento článek ukazuje, jak zabezpečit citlivá data v databázi SQL s šifrováním dat pomocí Průvodce vždy šifrované v SQL Server Management Studio.
keywords: šifrování dat, šifrovací klíč, šifrování v cloudu
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: VanMSFT
ms.author: vanto
ms.reviewer: ''
ms.date: 03/12/2019
ms.openlocfilehash: f1d08581c5d29fc41fb33541d766af7cece88cdc
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81451665"
---
# <a name="always-encrypted-protect-sensitive-data-and-store-encryption-keys-in-azure-key-vault"></a>Vždy šifrované: Ochrana citlivých dat a ukládání šifrovacích klíčů v trezoru klíčů Azure

Tento článek ukazuje, jak zabezpečit citlivá data v databázi SQL s šifrováním dat pomocí [Průvodce vždy šifrované](https://msdn.microsoft.com/library/mt459280.aspx) v [SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/hh213248.aspx). Obsahuje také pokyny, které vám ukáže, jak uložit každý šifrovací klíč v azure key vault.

Vždy šifrovaná je nová technologie šifrování dat v Azure SQL Database a SQL Server, která pomáhá chránit citlivá data v klidovém stavu na serveru, během pohybu mezi klientem a serverem a při použití dat. Vždy šifrované zajišťuje, že citlivá data se nikdy nezobrazí jako prostý text uvnitř databázového systému. Po konfiguraci šifrování dat mají k datům ve formátu prostého textu přístup pouze klientské aplikace nebo aplikační servery, které mají přístup ke klíčům. Podrobné informace naleznete v tématu [Vždy šifrované (Database Engine)](https://msdn.microsoft.com/library/mt163865.aspx).

Po konfiguraci databáze pro použití vždy šifrované, vytvoříte klientskou aplikaci v jazyce C# s Visual Studio pro práci s šifrovaná data.

Postupujte podle kroků v tomto článku a zjistěte, jak nastavit vždy šifrované pro databázi Azure SQL. V tomto článku se dozvíte, jak provádět následující úkoly:

- Pomocí Průvodce vždy šifrovanými v ssms vytvořte [vždy šifrované klíče](https://msdn.microsoft.com/library/mt163865.aspx#Anchor_3).
  - Vytvořte [hlavní klíč sloupce (CMK)](https://msdn.microsoft.com/library/mt146393.aspx).
  - Vytvořte [sloupcový šifrovací klíč (CEK).](https://msdn.microsoft.com/library/mt146372.aspx)
- Vytvořte databázovou tabulku a šifrujte sloupce.
- Vytvořte aplikaci, která vloží, vybere a zobrazí data ze šifrovaných sloupců.

## <a name="prerequisites"></a>Požadavky

Pro účely tohoto kurzu budete potřebovat:

- Účet a předplatné Azure. Pokud ji nemáte, zaregistrujte si [bezplatnou zkušební verzi](https://azure.microsoft.com/pricing/free-trial/).
- [SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) verze 13.0.700.242 nebo novější.
- [Rozhraní .NET Framework 4.6](https://msdn.microsoft.com/library/w0x726c2.aspx) nebo novější (v klientském počítači).
- [Visual Studio](https://www.visualstudio.com/downloads/download-visual-studio-vs.aspx).
- [Azure PowerShell](/powershell/azure/overview) nebo [Azure CLI](/cli/azure/install-azure-cli)

## <a name="enable-your-client-application-to-access-the-sql-database-service"></a>Povolení přístupu klientské aplikace ke službě SQL Database

Je nutné povolit klientské aplikace pro přístup ke službě SQL Database nastavením aplikace Azure Active Directory (AAD) a zkopírováním *ID aplikace* a *klíče,* který budete potřebovat k ověření aplikace.

Pokud chcete získat *ID a* *klíč*aplikace , postupujte podle pokynů při [vytváření aplikace azure active directory a instancí služby, které mají přístup k prostředkům](../active-directory/develop/howto-create-service-principal-portal.md).

## <a name="create-a-key-vault-to-store-your-keys"></a>Vytvoření trezoru klíčů pro uložení klíčů

Teď, když je vaše klientská aplikace nakonfigurovaná a máte ID aplikace, je čas vytvořit trezor klíčů a nakonfigurovat jeho zásady přístupu, abyste vy i vaše aplikace měli přístup k tajným kódům trezoru (vždy šifrované klíče). Oprávnění *vytvořit*, *získat*, *seznam*, *podepsat*, *ověřit*, *wrapKey*a *rozbalitklíč* jsou vyžadována pro vytvoření nového hlavního klíče sloupce a pro nastavení šifrování pomocí aplikace SQL Server Management Studio.

Trezor klíčů můžete rychle vytvořit spuštěním následujícího skriptu. Podrobné vysvětlení těchto příkazů a další informace o vytváření a konfiguraci trezoru klíčů naleznete v tématu [Co je Azure Key Vault?](../key-vault/general/overview.md).

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

> [!IMPORTANT]
> Modul Správce prostředků Azure (RM) prostředí PowerShell je stále podporovaný službou Azure SQL Database, ale veškerý budoucí vývoj je pro modul Az.Sql. Modul AzureRM bude nadále dostávat opravy chyb nejméně do prosince 2020.  Argumenty pro příkazy v modulu Az a v modulech AzureRm jsou v podstatě identické. Další informace o jejich kompatibilitě [najdete v tématu Představení nového modulu Azure PowerShell Az](/powershell/azure/new-azureps-module-az).

```powershell
$subscriptionName = '<subscriptionName>'
$userPrincipalName = '<username@domain.com>'
$applicationId = '<applicationId from AAD application>'
$resourceGroupName = '<resourceGroupName>' # use the same resource group name when creating your SQL Database below
$location = '<datacenterLocation>'
$vaultName = '<vaultName>'

Connect-AzAccount
$subscriptionId = (Get-AzSubscription -SubscriptionName $subscriptionName).Id
Set-AzContext -SubscriptionId $subscriptionId

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzKeyVault -VaultName $vaultName -ResourceGroupName $resourceGroupName -Location $location

Set-AzKeyVaultAccessPolicy -VaultName $vaultName -ResourceGroupName $resourceGroupName -PermissionsToKeys create,get,wrapKey,unwrapKey,sign,verify,list -UserPrincipalName $userPrincipalName
Set-AzKeyVaultAccessPolicy  -VaultName $vaultName  -ResourceGroupName $resourceGroupName -ServicePrincipalName $applicationId -PermissionsToKeys get,wrapKey,unwrapKey,sign,verify,list
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
$subscriptionName = '<subscriptionName>'
$userPrincipalName = '<username@domain.com>'
$applicationId = '<applicationId from AAD application>'
$resourceGroupName = '<resourceGroupName>' # use the same resource group name when creating your SQL Database below
$location = '<datacenterLocation>'
$vaultName = '<vaultName>'

az login
az account set --subscription $subscriptionName

az group create --location $location --name $resourceGroupName

az keyvault create --name $vaultName --resource-group $resourceGroupName --location $location

az keyvault set-policy --name $vaultName --key-permissions create, get, list, sign, unwrapKey, verify, wrapKey --resource-group $resourceGroupName --upn $userPrincipalName
az keyvault set-policy --name $vaultName --key-permissions get, list, sign, unwrapKey, verify, wrapKey --resource-group $resourceGroupName --spn $applicationId
```

* * *

## <a name="create-a-blank-sql-database"></a>Vytvoření prázdné databáze SQL

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/).
2. Přejděte na vytvoření**databáze databází** >  **prostředků** > **SQL Database**.
3. Vytvořte **prázdnou** databázi s názvem **Klinika** na novém nebo existujícím serveru. Podrobné pokyny k vytvoření databáze na webu Azure Portal najdete v [tématu Vaše první databáze Azure SQL](sql-database-single-database-get-started.md).

    ![Vytvoření prázdné databáze](./media/sql-database-always-encrypted-azure-key-vault/create-database.png)

Budete potřebovat připojovací řetězec později v kurzu, takže po vytvoření databáze, přejděte do nové databáze Clinic a zkopírujte připojovací řetězec. Připojovací řetězec můžete získat kdykoli, ale je snadné jej zkopírovat na webu Azure Portal.

1. Přejít na **SQL databáze** > **Klinika** > **Zobrazit připojovací řetězce databáze**.
2. Zkopírujte připojovací řetězec pro **ADO.NET**.

    ![Zkopírování připojovacího řetězce](./media/sql-database-always-encrypted-azure-key-vault/connection-strings.png)

## <a name="connect-to-the-database-with-ssms"></a>Připojit se k databázi pomocí SSMS

Otevřete SSMS a připojte se k serveru pomocí databáze Clinic.

1. Otevřete aplikaci SSMS. (Přejděte na **připojení** > **databázového stroje** a otevřete okno Připojit k **serveru,** pokud není otevřené.)

2. Zadejte název serveru a pověření. Název serveru lze nalézt v okně databáze SQL a v připojovacím řetězci, který jste zkopírovali dříve. Zadejte úplný název serveru, včetně *database.windows.net*.

    ![Zkopírování připojovacího řetězce](./media/sql-database-always-encrypted-azure-key-vault/ssms-connect.png)

Pokud se otevře okno **Pravidlo nové brány firewall,** přihlaste se do Azure a nechte SSMS vytvořit nové pravidlo brány firewall za vás.

## <a name="create-a-table"></a>Vytvoření tabulky

V této části vytvoříte tabulku pro uložení údajů o pacientech. Není zpočátku šifrována – šifrování nakonfigurujete v další části.

1. Rozbalte **položku Databáze**.
2. Klepněte pravým tlačítkem myši na databázi **Kliniky** a klepněte na příkaz **Nový dotaz**.
3. Vložte následující Transact-SQL (T-SQL) do nového okna dotazu a **Spusťte** jej.

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

## <a name="encrypt-columns-configure-always-encrypted"></a>Šifrovat sloupce (konfigurace vždy šifrované)

Služba SSMS poskytuje průvodce, který vám pomůže snadno nakonfigurovat vždy šifrované nastavením hlavního klíče sloupce, šifrovacího klíče sloupce a šifrovaných sloupců.

1. Rozbalte**tabulky****klinik** >  **databází** > .
2. Klikněte pravým tlačítkem myši na tabulku **Pacienti** a výběrem **možnosti Šifrovat sloupce** otevřete Průvodce vždy šifrovanými:

    ![Šifrování sloupců](./media/sql-database-always-encrypted-azure-key-vault/encrypt-columns.png)

Průvodce vždy šifrovanými obsahuje následující části: **Výběr sloupců**, **Konfigurace hlavního klíče**, **Ověření**a **Souhrn**.

### <a name="column-selection"></a>Výběr sloupce

Kliknutím na **Další** na stránce **Úvod** otevřete stránku **Výběr sloupců.** Na této stránce vyberete sloupce, které chcete zašifrovat, [typ šifrování a jaký kód šifrovacího klíče (CEK)](https://msdn.microsoft.com/library/mt459280.aspx#Anchor_2) použít.

Šifrujte informace **o SSN** a **Date pro** každého pacienta. Sloupec SSN bude používat deterministické šifrování, které podporuje vyhledávání rovnosti, spojení a seskupení podle. Sloupec Datum narození bude používat randomizované šifrování, které nepodporuje operace.

Nastavte **typ šifrování** pro sloupec SSN na **deterministický** a sloupec Datum narození na **Randomizovaný**. Klikněte na **Další**.

![Šifrování sloupců](./media/sql-database-always-encrypted-azure-key-vault/column-selection.png)

### <a name="master-key-configuration"></a>Konfigurace hlavního klíče

Stránka **Konfigurace hlavního klíče** je místo, kde nastavíte CMK a vyberete zprostředkovatele úložiště klíčů, kde bude cmk uložen. V současné době můžete uložit CMK v úložišti certifikátů Windows, Azure Key Vault nebo modul hardwarového zabezpečení (HSM).

Tento kurz ukazuje, jak ukládat klíče v azure key vault.

1. Vyberte **Azure Key Vault**.
2. V rozevíracím seznamu vyberte požadovaný trezor klíčů.
3. Klikněte na **Další**.

![Konfigurace hlavního klíče](./media/sql-database-always-encrypted-azure-key-vault/master-key-configuration.png)

### <a name="validation"></a>Ověřování

Sloupce můžete nyní zašifrovat nebo uložit skript prostředí PowerShell, který chcete spustit později. V tomto kurzu vyberte **pokračovat a dokončit nyní** a klepněte na tlačítko **Další**.

### <a name="summary"></a>Souhrn

Ověřte, zda jsou všechna nastavení správná, a klepnutím na **tlačítko Dokončit** dokončete nastavení pro vždy šifrované.

![Souhrn](./media/sql-database-always-encrypted-azure-key-vault/summary.png)

### <a name="verify-the-wizards-actions"></a>Ověření akcí průvodce

Po dokončení průvodce je databáze nastavena na vždy šifrované. Průvodce provedl následující akce:

- Vytvořil hlavní klíč sloupce a uložil ho do úložiště klíčů Azure.
- Vytvořil šifrovací klíč sloupce a uložil ho do úložiště klíčů Azure.
- Nakonfiguroval vybrané sloupce pro šifrování. Tabulka Pacienti aktuálně neobsahuje žádná data, ale všechna existující data ve vybraných sloupcích jsou nyní zašifrována.

Vytvoření klíčů v SSMS můžete ověřit rozbalením**rozšířením zabezpečení** >  **kliniky** > **vždy šifrované klíče**.

## <a name="create-a-client-application-that-works-with-the-encrypted-data"></a>Vytvoření klientské aplikace, která pracuje se šifrovanými daty

Nyní, když je nastaveno vždy šifrované, můžete vytvořit aplikaci, která provádí *vložení* a *výběr v* šifrovaných sloupcích.  

> [!IMPORTANT]
> Aplikace musí používat [sqlparameter](https://msdn.microsoft.com/library/system.data.sqlclient.sqlparameter.aspx) objekty při předávání dat ve formátu prostého textu na server s vždy šifrované sloupce. Předání literálových hodnot bez použití objektů SqlParameter bude mít za následek výjimku.

1. Otevřete Visual Studio a vytvořte novou **aplikaci konzoly** C# (Visual Studio 2015 a starší) nebo **konzolovou aplikaci (.NET Framework)** (Visual Studio 2017 a novější). Ujistěte se, že váš projekt je nastavena na **rozhraní .NET Framework 4.6** nebo novější.
2. Pojmenujte projekt **AlwaysEncryptedConsoleAKVApp** a klepněte na tlačítko **OK**.
3. Nainstalujte následující balíčky NuGet tak, že přejdete na **konzoli Správce** > **balíčků****nástroje NuGet .** > 

Spusťte tyto dva řádky kódu v konzole Správce balíčků:

   ```powershell
   Install-Package Microsoft.SqlServer.Management.AlwaysEncrypted.AzureKeyVaultProvider
   Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory
   ```

## <a name="modify-your-connection-string-to-enable-always-encrypted"></a>Úprava připojovacího řetězce tak, aby povoloval vždy šifrované

Tato část vysvětluje, jak povolit vždy šifrované v připojovacím řetězci databáze.

Chcete-li povolit možnost Vždy šifrované, je třeba přidat klíčové slovo **Nastavení šifrování sloupců** do připojovacího řetězce a nastavit jej na **hodnotu Povoleno**.

Můžete to nastavit přímo v připojovacím řetězci nebo ji můžete nastavit pomocí [sqlconnectionstringbuilderbuilder](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnectionstringbuilder.aspx). Ukázková aplikace v další části ukazuje, jak používat **SqlConnectionStringBuilder**.

### <a name="enable-always-encrypted-in-the-connection-string"></a>Povolit vždy šifrované v připojovacím řetězci

Přidejte do připojovacího řetězce následující klíčové slovo.

   `Column Encryption Setting=Enabled`

### <a name="enable-always-encrypted-with-sqlconnectionstringbuilder"></a>Povolit vždy šifrované pomocí sqlconnectionstringbuilderbuilder

Následující kód ukazuje, jak povolit vždy šifrované nastavením [SqlConnectionStringBuilder.ColumnEncryptionSetting](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnectionstringbuilder.columnencryptionsetting.aspx) na [Povoleno](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnectioncolumnencryptionsetting.aspx).

```csharp
// Instantiate a SqlConnectionStringBuilder.
SqlConnectionStringBuilder connStringBuilder = new SqlConnectionStringBuilder("replace with your connection string");

// Enable Always Encrypted.
connStringBuilder.ColumnEncryptionSetting = SqlConnectionColumnEncryptionSetting.Enabled;
```

## <a name="register-the-azure-key-vault-provider"></a>Registrace zprostředkovatele azure key vault
Následující kód ukazuje, jak zaregistrovat zprostředkovatele Azure Key Vault s ovladačem ADO.NET.

```csharp
private static ClientCredential _clientCredential;

static void InitializeAzureKeyVaultProvider() {
    _clientCredential = new ClientCredential(applicationId, clientKey);

    SqlColumnEncryptionAzureKeyVaultProvider azureKeyVaultProvider = new SqlColumnEncryptionAzureKeyVaultProvider(GetToken);

    Dictionary<string, SqlColumnEncryptionKeyStoreProvider> providers = new Dictionary<string, SqlColumnEncryptionKeyStoreProvider>();

    providers.Add(SqlColumnEncryptionAzureKeyVaultProvider.ProviderName, azureKeyVaultProvider);
    SqlConnection.RegisterColumnEncryptionKeyStoreProviders(providers);
}
```

## <a name="always-encrypted-sample-console-application"></a>Vždy šifrovaná ukázková konzola

Tato ukázka ukazuje, jak:

- Upravte připojovací řetězec tak, aby byl vždy šifrován.
- Zaregistrujte Azure Key Vault jako zprostředkovatele úložiště klíčů aplikace.  
- Vložte data do šifrovaných sloupců.
- Vyberte záznam filtrováním pro určitou hodnotu v zašifrovaném sloupci.

Nahraďte obsah *Program.cs* následujícím kódem. Nahraďte připojovací řetězec pro globální proměnnou connectionstring v řádku, který přímo předchází Metodě Main platným připojovacím řetězcem z portálu Azure. Toto je jediná změna, kterou je třeba provést v tomto kódu.

Spusťte aplikaci a podívejte se na vždy šifrované v akci.

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;
using System.Data;
using System.Data.SqlClient;
using Microsoft.IdentityModel.Clients.ActiveDirectory;
using Microsoft.SqlServer.Management.AlwaysEncrypted.AzureKeyVaultProvider;

namespace AlwaysEncryptedConsoleAKVApp {
    class Program {
        // Update this line with your Clinic database connection string from the Azure portal.
        static string connectionString = @"<connection string from the portal>";
        static string applicationId = @"<application ID from your AAD application>";
        static string clientKey = "<key from your AAD application>";

        static void Main(string[] args) {
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

            InsertPatient(new Patient() {
                SSN = "999-99-0001",
                FirstName = "Orlando",
                LastName = "Gee",
                BirthDate = DateTime.Parse("01/04/1964")
            });
            InsertPatient(new Patient() {
                SSN = "999-99-0002",
                FirstName = "Keith",
                LastName = "Harris",
                BirthDate = DateTime.Parse("06/20/1977")
            });
            InsertPatient(new Patient() {
                SSN = "999-99-0003",
                FirstName = "Donna",
                LastName = "Carreras",
                BirthDate = DateTime.Parse("02/09/1973")
            });
            InsertPatient(new Patient() {
                SSN = "999-99-0004",
                FirstName = "Janet",
                LastName = "Gates",
                BirthDate = DateTime.Parse("08/31/1985")
            });
            InsertPatient(new Patient() {
                SSN = "999-99-0005",
                FirstName = "Lucy",
                LastName = "Harrington",
                BirthDate = DateTime.Parse("05/06/1993")
            });

            // Fetch and display all patients.
            Console.WriteLine(Environment.NewLine + "All the records currently in the Patients table:");

            foreach (Patient patient in SelectAllPatients()) {
                Console.WriteLine(patient.FirstName + " " + patient.LastName + "\tSSN: " + patient.SSN + "\tBirthdate: " + patient.BirthDate);
            }

            // Get patients by SSN.
            Console.WriteLine(Environment.NewLine + "Now lets locate records by searching the encrypted SSN column.");

            string ssn;

            // This very simple validation only checks that the user entered 11 characters.
            // In production be sure to check all user input and use the best validation for your specific application.
            do {
                Console.WriteLine("Please enter a valid SSN (ex. 999-99-0003):");
                ssn = Console.ReadLine();
            } while (ssn.Length != 11);

            // The example allows duplicate SSN entries so we will return all records
            // that match the provided value and store the results in selectedPatients.
            Patient selectedPatient = SelectPatientBySSN(ssn);

            // Check if any records were returned and display our query results.
            if (selectedPatient != null) {
                Console.WriteLine("Patient found with SSN = " + ssn);
                Console.WriteLine(selectedPatient.FirstName + " " + selectedPatient.LastName + "\tSSN: "
                    + selectedPatient.SSN + "\tBirthdate: " + selectedPatient.BirthDate);
            }
            else {
                Console.WriteLine("No patients found with SSN = " + ssn);
            }

            Console.WriteLine("Press Enter to exit...");
            Console.ReadLine();
        }

        private static ClientCredential _clientCredential;

        static void InitializeAzureKeyVaultProvider() {
            _clientCredential = new ClientCredential(applicationId, clientKey);

            SqlColumnEncryptionAzureKeyVaultProvider azureKeyVaultProvider =
              new SqlColumnEncryptionAzureKeyVaultProvider(GetToken);

            Dictionary<string, SqlColumnEncryptionKeyStoreProvider> providers =
              new Dictionary<string, SqlColumnEncryptionKeyStoreProvider>();

            providers.Add(SqlColumnEncryptionAzureKeyVaultProvider.ProviderName, azureKeyVaultProvider);
            SqlConnection.RegisterColumnEncryptionKeyStoreProviders(providers);
        }

        public async static Task<string> GetToken(string authority, string resource, string scope) {
            var authContext = new AuthenticationContext(authority);
            AuthenticationResult result = await authContext.AcquireTokenAsync(resource, _clientCredential);

            if (result == null)
                throw new InvalidOperationException("Failed to obtain the access token");
            return result.AccessToken;
        }

        static int InsertPatient(Patient newPatient) {
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

            using (sqlCmd.Connection = new SqlConnection(connectionString)) {
                try {
                    sqlCmd.Connection.Open();
                    sqlCmd.ExecuteNonQuery();
                }
                catch (Exception ex) {
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


        static List<Patient> SelectAllPatients() {
            List<Patient> patients = new List<Patient>();

            SqlCommand sqlCmd = new SqlCommand(
              "SELECT [SSN], [FirstName], [LastName], [BirthDate] FROM [dbo].[Patients]",
                new SqlConnection(connectionString));

            using (sqlCmd.Connection = new SqlConnection(connectionString))

            using (sqlCmd.Connection = new SqlConnection(connectionString)) {
                try {
                    sqlCmd.Connection.Open();
                    SqlDataReader reader = sqlCmd.ExecuteReader();

                    if (reader.HasRows) {
                        while (reader.Read()) {
                            patients.Add(new Patient() {
                                SSN = reader[0].ToString(),
                                FirstName = reader[1].ToString(),
                                LastName = reader["LastName"].ToString(),
                                BirthDate = (DateTime)reader["BirthDate"]
                            });
                        }
                    }
                }
                catch (Exception ex) {
                    throw;
                }
            }

            return patients;
        }

        static Patient SelectPatientBySSN(string ssn) {
            Patient patient = new Patient();

            SqlCommand sqlCmd = new SqlCommand(
                "SELECT [SSN], [FirstName], [LastName], [BirthDate] FROM [dbo].[Patients] WHERE [SSN]=@SSN",
                new SqlConnection(connectionString));

            SqlParameter paramSSN = new SqlParameter(@"@SSN", ssn);
            paramSSN.DbType = DbType.AnsiStringFixedLength;
            paramSSN.Direction = ParameterDirection.Input;
            paramSSN.Size = 11;

            sqlCmd.Parameters.Add(paramSSN);

            using (sqlCmd.Connection = new SqlConnection(connectionString)) {
                try {
                    sqlCmd.Connection.Open();
                    SqlDataReader reader = sqlCmd.ExecuteReader();

                    if (reader.HasRows) {
                        while (reader.Read()) {
                            patient = new Patient() {
                                SSN = reader[0].ToString(),
                                FirstName = reader[1].ToString(),
                                LastName = reader["LastName"].ToString(),
                                BirthDate = (DateTime)reader["BirthDate"]
                            };
                        }
                    }
                    else {
                        patient = null;
                    }
                }
                catch (Exception ex) {
                    throw;
                }
            }
            return patient;
        }

        // This method simply deletes all records in the Patients table to reset our demo.
        static int ResetPatientsTable() {
            int returnValue = 0;

            SqlCommand sqlCmd = new SqlCommand("DELETE FROM Patients");
            using (sqlCmd.Connection = new SqlConnection(connectionString)) {
                try {
                    sqlCmd.Connection.Open();
                    sqlCmd.ExecuteNonQuery();

                }
                catch (Exception ex) {
                    returnValue = 1;
                }
            }
            return returnValue;
        }
    }

    class Patient {
        public string SSN { get; set; }
        public string FirstName { get; set; }
        public string LastName { get; set; }
        public DateTime BirthDate { get; set; }
    }
}
```

## <a name="verify-that-the-data-is-encrypted"></a>Ověření šifrování dat

Můžete rychle zkontrolovat, zda jsou skutečná data na serveru šifrována dotazem na data pacientů pomocí SSMS (pomocí aktuálního připojení, kde **nastavení šifrování sloupců** ještě není povoleno).

Spusťte následující dotaz v databázi Kliniky.

```sql
SELECT FirstName, LastName, SSN, BirthDate FROM Patients;
```

Můžete vidět, že šifrované sloupce neobsahují žádná data ve formátu prostého textu.

   ![Nová konzolová aplikace](./media/sql-database-always-encrypted-azure-key-vault/ssms-encrypted.png)

Chcete-li použít SSMS pro přístup k datům ve formátu prostého textu, musíte nejprve zajistit, aby uživatel měl správná oprávnění k trezoru klíčů Azure: *get*, *unwrapKey*a *verify*. Podrobné informace naleznete v [tématu Vytvoření a uložení hlavních klíčů sloupců (vždy šifrované).](https://docs.microsoft.com/sql/relational-databases/security/encryption/create-and-store-column-master-keys-always-encrypted)

Pak přidejte parametr *Column Encryption=enabled* během připojení.

1. V ssms klepněte pravým tlačítkem myši na server v **Průzkumníkovi objektů** a zvolte **Odpojit**.
2. Klepnutím na **tlačítko Připojit** > **databázový stroj** otevřete okno Připojit k **serveru** a klepněte na tlačítko **Možnosti**.
3. Klepněte na **položku Další parametry připojení** a zadejte **příkaz Column Encryption Setting=enabled**.

    ![Nová konzolová aplikace](./media/sql-database-always-encrypted-azure-key-vault/ssms-connection-parameter.png)

4. Spusťte následující dotaz v databázi Kliniky.

   ```sql
   SELECT FirstName, LastName, SSN, BirthDate FROM Patients;
   ```

     Nyní můžete vidět data ve formátu prostého textu v šifrovaných sloupcích.
     ![Nová konzolová aplikace](./media/sql-database-always-encrypted-azure-key-vault/ssms-plaintext.png)

## <a name="next-steps"></a>Další kroky

Po vytvoření databáze, která používá vždy šifrované, můžete provést následující kroky:

- [Otočte a vyčistěte klíče](https://msdn.microsoft.com/library/mt607048.aspx).
- [Migrujte data, která jsou již zašifrována pomocí funkce Vždy šifrované](https://msdn.microsoft.com/library/mt621539.aspx).

## <a name="related-information"></a>Související informace

- [Vždy šifrované (vývoj klienta)](https://msdn.microsoft.com/library/mt147923.aspx)
- [Transparentní šifrování dat](https://msdn.microsoft.com/library/bb934049.aspx)
- [Šifrování serveru SQL Server](https://msdn.microsoft.com/library/bb510663.aspx)
- [Vždy šifrovaný průvodce](https://msdn.microsoft.com/library/mt459280.aspx)
- [Vždy šifrovaný blog](https://blogs.msdn.com/b/sqlsecurity/archive/tags/always-encrypted/)
