---
title: Konfigurace Always Encrypted pomocí Azure Key Vault
description: V tomto kurzu se dozvíte, jak zabezpečit citlivá data v databázi v Azure SQL Database s šifrováním dat pomocí Průvodce Always Encrypted v SQL Server Management Studio.
keywords: šifrování dat, šifrovací klíč, cloudové šifrování
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: sqldbrb=1, devx-track-azurecli
ms.devlang: ''
ms.topic: how-to
author: VanMSFT
ms.author: vanto
ms.reviewer: ''
ms.date: 11/02/2020
ms.openlocfilehash: 257abf03994c7006b1c3789174f550515dcd309a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "94841457"
---
# <a name="configure-always-encrypted-by-using-azure-key-vault"></a>Konfigurace Always Encrypted pomocí Azure Key Vault 

[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb-sqlmi.md)]

V tomto článku se dozvíte, jak zabezpečit citlivá data v databázi v Azure SQL Database s šifrováním dat pomocí [průvodce Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-wizard) v [SQL Server Management Studio (SSMS)](/sql/ssms/sql-server-management-studio-ssms). Obsahuje také pokyny, které vám ukážou, jak uložit jednotlivé šifrovací klíče do Azure Key Vault.

Always Encrypted je technologie pro šifrování dat, která pomáhá chránit citlivá data umístěná na serveru, během pohybu mezi klientem a serverem a i když se data používají. Always Encrypted zajistí, aby se citlivá data v rámci databázového systému nikdy nezobrazovala jako prostý text. Po nakonfigurování šifrování dat budou mít přístup k datům ve formátu prostého textu jenom klientské aplikace nebo aplikační servery, které mají přístup ke klíčům. Podrobné informace najdete v tématu [Always Encrypted (databázový stroj)](/sql/relational-databases/security/encryption/always-encrypted-database-engine).

Po nakonfigurování databáze pro použití Always Encrypted vytvoříte klientskou aplikaci v jazyce C# se sadou Visual Studio, která bude fungovat s šifrovanými daty.

Postupujte podle kroků v tomto článku a Naučte se, jak nastavit Always Encrypted pro vaši databázi v Azure SQL Database nebo spravované instanci SQL. V tomto článku se dozvíte, jak provádět následující úlohy:

- Pomocí Průvodce Always Encrypted v SSMS můžete vytvořit [Always Encrypted klíče](/sql/relational-databases/security/encryption/always-encrypted-database-engine#Anchor_3).
  - Vytvoří [hlavní klíč sloupce (CMK)](/sql/t-sql/statements/create-column-master-key-transact-sql).
  - Vytvoří [šifrovací klíč sloupce (cek)](/sql/t-sql/statements/create-column-encryption-key-transact-sql).
- Vytvořte databázovou tabulku a Zašifrujte sloupce.
- Vytvořte aplikaci, která vloží, vybere a zobrazí data ze zašifrovaných sloupců.

## <a name="prerequisites"></a>Předpoklady


- Účet a předplatné Azure. Pokud ho ještě nemáte, zaregistrujte si [bezplatnou zkušební verzi](https://azure.microsoft.com/pricing/free-trial/).
- Databáze v [Azure SQL Database](single-database-create-quickstart.md) nebo [spravované instanci Azure SQL](../managed-instance/instance-create-quickstart.md).
- [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) verze 13.0.700.242 nebo novější.
- [.NET Framework 4,6](/dotnet/framework/) nebo novější (na klientském počítači).
- [Visual Studio](https://www.visualstudio.com/downloads/download-visual-studio-vs.aspx).
- [Azure PowerShell](/powershell/azure/) nebo [Azure CLI](/cli/azure/install-azure-cli)

## <a name="enable-client-application-access"></a>Povolit přístup k klientské aplikaci

Musíte povolit klientské aplikaci přístup k databázi v SQL Database nastavením aplikace Azure Active Directory (Azure AD) a zkopírováním ID a *klíče* *aplikace* , které budete potřebovat k ověření vaší aplikace.

Chcete-li získat ID a *klíč* *aplikace* , postupujte podle kroků v části [Vytvoření Azure Active Directory aplikace a instančního objektu, který má přístup k prostředkům](../../active-directory/develop/howto-create-service-principal-portal.md).

## <a name="create-a-key-vault-to-store-your-keys"></a>Vytvoření trezoru klíčů pro ukládání klíčů

Teď, když je nakonfigurovaná klientská aplikace a máte ID aplikace, je čas vytvořit Trezor klíčů a nakonfigurovat zásady přístupu, abyste vy a vaše aplikace mohli získat přístup k tajným tajným klíčům trezoru (Always Encrypted klíče). Pro vytvoření nového hlavního klíče sloupce a pro nastavení šifrování pomocí SQL Server Management Studio jsou nutná oprávnění *vytvořit*, *získat*, *Zobrazit*, *podepsat*, *ověřit*, *wrapKey* a *unwrapKey* .

Trezor klíčů můžete rychle vytvořit spuštěním následujícího skriptu. Podrobné vysvětlení těchto příkazů a další informace o vytváření a konfiguraci trezoru klíčů najdete v tématu [co je Azure Key Vault?](../../key-vault/general/overview.md).

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

> [!IMPORTANT]
> Modul Azure Resource Manager PowerShellu (RM) je stále podporován Azure SQL Database, ale všechny budoucí vývojové prostředí jsou k dispozici pro modul AZ. SQL. V modulu AzureRM bude i nadále docházet k opravám chyb až do prosince 2020.  Argumenty pro příkazy v modulech AZ a v modulech AzureRm jsou v podstatě identické. Další informace o kompatibilitě najdete v tématu [představení nového Azure PowerShell AZ Module](/powershell/azure/new-azureps-module-az).

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
$resourceGroupName = '<resourceGroupName>' # use the same resource group name when creating your database in Azure SQL Database below
$location = '<datacenterLocation>'
$vaultName = '<vaultName>'

az login
az account set --subscription $subscriptionName

az group create --location $location --name $resourceGroupName

az keyvault create --name $vaultName --resource-group $resourceGroupName --location $location

az keyvault set-policy --name $vaultName --key-permissions create get list sign unwrapKey verify wrapKey --resource-group $resourceGroupName --upn $userPrincipalName
az keyvault set-policy --name $vaultName --key-permissions get list sign unwrapKey verify wrapKey --resource-group $resourceGroupName --spn $applicationId
```

---

## <a name="connect-with-ssms"></a>Připojení přes SSMS

Otevřete SQL Server Management Studio (SSMS) a připojte se k serveru nebo ho můžete spravovat pomocí vaší databáze.

1. Otevřete aplikaci SSMS. (Přejít na **připojení**  >  **Databázový stroj** pro otevření okna **připojit k serveru** , pokud není otevřený.)

2. Zadejte název serveru nebo název instance a přihlašovací údaje. 

    ![Zkopírování připojovacího řetězce](./media/always-encrypted-azure-key-vault-configure/ssms-connect.png)

Pokud se otevře okno **nové pravidlo brány firewall** , přihlaste se k Azure a umožněte SSMS vytvořit nové pravidlo brány firewall.

## <a name="create-a-table"></a>Vytvoření tabulky

V této části vytvoříte tabulku, která bude uchovávat data o pacientech. Zpočátku není šifrovaný – v další části budete konfigurovat šifrování.

1. Rozbalte položku **databáze**.
2. Klikněte pravým tlačítkem na databázi a potom klikněte na **Nový dotaz**.
3. Vložte následující příkaz Transact-SQL (T-SQL) do nového okna dotazu a **Spusťte** jej.

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

## <a name="encrypt-columns-configure-always-encrypted"></a>Šifrovat sloupce (nakonfigurovat Always Encrypted)

SSMS poskytuje průvodce, který vám pomůže snadno nakonfigurovat Always Encrypted nastavením hlavního klíče sloupce, šifrovacího klíče sloupce a šifrovaných sloupců za vás.

1. Rozbalte položku **databáze**  >  **Clinic**  >  **Tables**.
2. Klikněte pravým tlačítkem na tabulku **pacientům** a vyberte možnost **Šifrovat sloupce** a otevřete tak Průvodce Always Encrypted:

    ![Snímek obrazovky, který zvýrazní sloupce šifrování... možnost nabídky](./media/always-encrypted-azure-key-vault-configure/encrypt-columns.png)

Průvodce Always Encrypted obsahuje následující části: **Výběr sloupce**, **Konfigurace hlavního klíče**, **ověřování** a **Souhrn**.

### <a name="column-selection"></a>Výběr sloupce

Kliknutím na tlačítko **Další** na **úvodní** stránce otevřete stránku **Výběr sloupce** . Na této stránce můžete vybrat, které sloupce chcete šifrovat, [typ šifrování a který šifrovací klíč sloupce (cek)](/sql/relational-databases/security/encryption/always-encrypted-wizard#Anchor_2) se má použít.

Zašifrujte informace **SSN** a **DatumNarození** pro každého pacienta. Sloupec SSN bude používat deterministické šifrování, které podporuje vyhledávání rovnosti, spojení a seskupování podle. Sloupec DatumNarození bude používat náhodné šifrování, které nepodporuje operace.

Nastavte **typ šifrování** pro sloupec SSN na **deterministické** a sloupec DatumNarození, který se má **náhodným** nastavením. Klikněte na **Next** (Další).

![Šifrovat sloupce](./media/always-encrypted-azure-key-vault-configure/column-selection.png)

### <a name="master-key-configuration"></a>Konfigurace hlavního klíče

Na stránce **Konfigurace hlavního klíče** je místo, kde jste nastavili CMK a vyberete poskytovatele úložiště klíčů, kde se CMK uloží. V současné době můžete CMK ukládat do úložiště certifikátů Windows, Azure Key Vault nebo modulu hardwarového zabezpečení (HSM).

V tomto kurzu se dozvíte, jak ukládat klíče v Azure Key Vault.

1. Vyberte **Azure Key Vault**.
2. V rozevíracím seznamu vyberte požadovaný Trezor klíčů.
3. Klikněte na **Next** (Další).

![Konfigurace hlavního klíče](./media/always-encrypted-azure-key-vault-configure/master-key-configuration.png)

### <a name="validation"></a>Ověřování

Nyní můžete šifrovat sloupce nebo uložit skript prostředí PowerShell pro pozdější spuštění. Pro tento kurz vyberte **pokračovat** a klikněte na **Další**.

### <a name="summary"></a>Souhrn

Ověřte správnost nastavení a kliknutím na **Dokončit** dokončete instalaci pro Always Encrypted.

![Snímek obrazovky zobrazující stránku výsledků s úkoly označenými jako prošlé.](./media/always-encrypted-azure-key-vault-configure/summary.png)

### <a name="verify-the-wizards-actions"></a>Ověření akcí Průvodce

Po dokončení průvodce je vaše databáze nastavená pro Always Encrypted. Průvodce provedl následující akce:

- Byl vytvořen hlavní klíč sloupce a uložený v Azure Key Vault.
- Byl vytvořen šifrovací klíč sloupce a uložený v Azure Key Vault.
- Nakonfigurovali jste vybrané sloupce pro šifrování. Tabulka pacientů aktuálně neobsahuje žádná data, ale všechna existující data ve vybraných sloupcích jsou nyní zašifrovaná.

Pomocí rozšíření **Clinic**  >  **Security**  >  **Always Encrypted Keys** můžete ověřit vytvoření klíčů v SSMS.

## <a name="create-a-client-application-that-works-with-the-encrypted-data"></a>Vytvořit klientskou aplikaci, která funguje s šifrovanými daty

Teď, když je nastavené Always Encrypted, můžete vytvořit aplikaci, která provede *vkládání* a *výběry* na šifrovaných sloupcích.  

> [!IMPORTANT]
> Vaše aplikace musí při předávání nešifrovaných dat na server pomocí Always Encrypted sloupců používat objekty [SqlParameter](/dotnet/api/system.data.sqlclient.sqlparameter) . Předání literálových hodnot bez použití objektů SqlParameter způsobí výjimku.

1. Otevřete Visual Studio a vytvořte novou **konzolovou aplikaci** v jazyce C# (visual Studio 2015 a starší) nebo **konzolovou aplikaci (.NET Framework)** (Visual Studio 2017 a novější). Ujistěte se, že je váš projekt nastaven na **.NET Framework 4,6** nebo novější.
2. Pojmenujte projekt **AlwaysEncryptedConsoleAKVApp** a klikněte na **OK**.
3. Do části **nástroje**  >  **Správce balíčků NuGet**  >  **Konzola správce** balíčků nainstalujte následující balíčky NuGet.

Spusťte tyto dva řádky kódu v konzole správce balíčků:

   ```powershell
   Install-Package Microsoft.SqlServer.Management.AlwaysEncrypted.AzureKeyVaultProvider
   Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory
   ```

## <a name="modify-your-connection-string-to-enable-always-encrypted"></a>Změňte připojovací řetězec tak, aby povoloval Always Encrypted

V této části se dozvíte, jak povolit Always Encrypted v připojovacím řetězci databáze.

Chcete-li povolit Always Encrypted, je nutné přidat klíčové slovo **nastavení šifrování sloupce** do připojovacího řetězce a nastavit jej na **povoleno**.

Tuto možnost můžete nastavit přímo v připojovacím řetězci nebo ji můžete nastavit pomocí [SqlConnectionStringBuilder](/dotnet/api/system.data.sqlclient.sqlconnectionstringbuilder). Ukázková aplikace v další části ukazuje, jak používat **SqlConnectionStringBuilder**.

### <a name="enable-always-encrypted-in-the-connection-string"></a>Povolit Always Encrypted v připojovacím řetězci

Do připojovacího řetězce přidejte následující klíčové slovo.

   `Column Encryption Setting=Enabled`

### <a name="enable-always-encrypted-with-sqlconnectionstringbuilder"></a>Povolení Always Encrypted s SqlConnectionStringBuilder

Následující kód ukazuje, jak povolit Always Encrypted nastavením [SqlConnectionStringBuilder. ColumnEncryptionSetting](/dotnet/api/system.data.sqlclient.sqlconnectionstringbuilder.columnencryptionsetting) na [Enabled](/dotnet/api/system.data.sqlclient.sqlconnectioncolumnencryptionsetting).

```csharp
// Instantiate a SqlConnectionStringBuilder.
SqlConnectionStringBuilder connStringBuilder = new SqlConnectionStringBuilder("replace with your connection string");

// Enable Always Encrypted.
connStringBuilder.ColumnEncryptionSetting = SqlConnectionColumnEncryptionSetting.Enabled;
```

## <a name="register-the-azure-key-vault-provider"></a>Registrace poskytovatele Azure Key Vault
Následující kód ukazuje, jak zaregistrovat poskytovatele Azure Key Vault s ovladačem ADO.NET.

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

## <a name="always-encrypted-sample-console-application"></a>Ukázková Konzolová aplikace Always Encrypted

Tato ukázka předvádí, jak:

- Změňte připojovací řetězec tak, aby povoloval Always Encrypted.
- Zaregistrujte Azure Key Vault jako zprostředkovatele úložiště klíčů aplikace.  
- Vloží data do šifrovaných sloupců.
- Vyberte záznam filtrováním konkrétní hodnoty v zašifrovaném sloupci.

Obsah *programu program. cs* nahraďte následujícím kódem. Nahraďte připojovací řetězec pro globální proměnnou connectionString v řádku, který přímo předchází metodě Main, s platným připojovacím řetězcem z Azure Portal. Toto je jediná změna, kterou je třeba provést v tomto kódu.

Spusťte aplikaci, abyste viděli Always Encrypted v akci.

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

## <a name="verify-that-the-data-is-encrypted"></a>Ověřte, že jsou data zašifrovaná.

Můžete rychle ověřit, jestli jsou skutečná data na serveru šifrovaná pomocí dotazu na data pacientů pomocí SSMS (pomocí aktuálního připojení, ve kterém ještě není povolené **nastavení šifrování sloupce** ).

Spusťte následující dotaz na Clinic Database.

```sql
SELECT FirstName, LastName, SSN, BirthDate FROM Patients;
```

Můžete vidět, že šifrované sloupce neobsahují žádná data ve formátu prostého textu.

   ![Snímek obrazovky, který ukazuje, že šifrované sloupce neobsahují žádná data ve formátu prostého textu.](./media/always-encrypted-azure-key-vault-configure/ssms-encrypted.png)

Pokud chcete pro přístup k datům ve formátu prostého textu používat SSMS, musíte nejdřív zajistit, aby měl uživatel správná oprávnění k Azure Key Vault: *Get*, *unwrapKey* a *verify*. Podrobné informace najdete v tématu [Vytvoření a uložení hlavních klíčů sloupce (Always Encrypted)](/sql/relational-databases/security/encryption/create-and-store-column-master-keys-always-encrypted).

Pak během připojení přidejte parametr *nastavení šifrování sloupce = povoleno* .

1. V SSMS klikněte pravým tlačítkem na server v **Průzkumník objektů** a vyberte **Odpojit**.
2. Kliknutím na **připojit**  >  **databázový stroj** otevřete okno **připojit k serveru** a klikněte na **Možnosti**.
3. Klikněte na **Další parametry připojení** a **nastavení šifrování sloupce typ = povoleno**.

    ![Snímek obrazovky zobrazující kartu parametrů dalších oprav](./media/always-encrypted-azure-key-vault-configure/ssms-connection-parameter.png)

4. Spusťte následující dotaz na Clinic Database.

   ```sql
   SELECT FirstName, LastName, SSN, BirthDate FROM Patients;
   ```

   V šifrovaných sloupcích teď můžete zobrazit data ve formátu prostého textu.
   
   ![Nová Konzolová aplikace](./media/always-encrypted-azure-key-vault-configure/ssms-plaintext.png)

## <a name="next-steps"></a>Další kroky

Po nakonfigurování databáze pro použití Always Encrypted může být vhodné provést následující akce:

- [Otočení a vyčištění klíčů](/sql/relational-databases/security/encryption/configure-always-encrypted-using-sql-server-management-studio)
- [Migrujte data, která jsou už zašifrovaná pomocí Always Encrypted](/sql/relational-databases/security/encryption/migrate-sensitive-data-protected-by-always-encrypted).

## <a name="related-information"></a>Související informace

- [Always Encrypted (vývoj klientů)](/sql/relational-databases/security/encryption/always-encrypted-client-development)
- [Transparentní šifrování dat](/sql/relational-databases/security/encryption/transparent-data-encryption)
- [SQL Server šifrování](/sql/relational-databases/security/encryption/sql-server-encryption)
- [Průvodce Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-wizard)
- [Blog Always Encrypted](/archive/blogs/sqlsecurity/always-encrypted-key-metadata)
