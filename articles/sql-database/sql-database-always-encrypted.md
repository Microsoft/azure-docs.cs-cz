---
title: 'Funkce Always Encrypted: Azure SQL Database – úložiště certifikátů Windows | Dokumentace Microsoftu'
description: Tento článek popisuje, jak zajistit citlivá data v SQL database pomocí šifrování databáze s použitím Průvodce vždycky šifrovaná v SQL Server Management Studio (SSMS). Je také ukazuje, jak uložit šifrovací klíče do úložiště certifikátů Windows.
keywords: šifrování dat, šifrování sql, databáze šifrování, citlivých dat, s funkcí Always Encrypted
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: VanMSFT
ms.author: vanto
ms.reviwer: ''
manager: craigg
ms.date: 04/01/2018
ms.openlocfilehash: 4c9b2099308bc6a239a72fe2ebe559cdfa2fd3a1
ms.sourcegitcommit: cc4fdd6f0f12b44c244abc7f6bc4b181a2d05302
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2018
ms.locfileid: "47064216"
---
# <a name="always-encrypted-protect-sensitive-data-in-sql-database-and-store-your-encryption-keys-in-the-windows-certificate-store"></a>Funkce Always Encrypted: Ochrana citlivých dat ve službě SQL Database a uložit šifrovací klíče do úložiště certifikátů Windows

Tento článek popisuje, jak zajistit citlivá data v SQL database pomocí šifrování databáze s použitím [vždy šifrována průvodce](https://msdn.microsoft.com/library/mt459280.aspx) v [SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/hh213248.aspx). Je také ukazuje, jak uložit šifrovací klíče do úložiště certifikátů Windows.

Funkce Always Encrypted je novou technologií šifrování dat ve službě Azure SQL Database a SQL Server, který pomáhá chránit citlivá data v klidovém stavu na serveru, při přesouvání mezi klientem a serverem, zatímco data se používá, nikdy zajistit citlivá data se zobrazí jako ve formátu prostého textu v systému databáze. Po zašifrování dat pouze klientských aplikací nebo serverů aplikace, které mají přístup ke klíčům můžou k datům ve formátu prostého textu. Podrobné informace najdete v tématu [(databázový stroj) s funkcí Always Encrypted](https://msdn.microsoft.com/library/mt163865.aspx).

Po dokončení konfigurace databáze pro použití s funkcí Always Encrypted, vytvoříte klientskou aplikaci v jazyce C# pomocí sady Visual Studio pro práci s šifrovaná data.

Postupujte podle kroků v tomto článku se dozvíte, jak nastavit pro službu Azure SQL database s funkcí Always Encrypted. V tomto článku se dozvíte, jak provádět následující úlohy:

* Pomocí Průvodce funkcí Always Encrypted v aplikaci SSMS vytvořit [vždy šifrované klíče](https://msdn.microsoft.com/library/mt163865.aspx#Anchor_3).
  * Vytvoření [sloupec Master Key (CMK)](https://msdn.microsoft.com/library/mt146393.aspx).
  * Vytvoření [šifrovacího klíče sloupce (CEK)](https://msdn.microsoft.com/library/mt146372.aspx).
* Vytvořit tabulku databáze a šifrování sloupce.
* Vytvořte aplikaci, která vloží, vybere a zobrazí data z šifrované sloupce.

## <a name="prerequisites"></a>Požadavky
Pro účely tohoto kurzu budete potřebovat:

* Účet a předplatné Azure. Pokud ho nemáte, zaregistrovat [bezplatnou zkušební verzi](https://azure.microsoft.com/pricing/free-trial/).
* [SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) verze 13.0.700.242 nebo novější.
* [Rozhraní .NET framework 4.6](https://msdn.microsoft.com/library/w0x726c2.aspx) nebo novějším (v klientském počítači).
* Sadu [Visual Studio](https://www.visualstudio.com/downloads/download-visual-studio-vs.aspx).

## <a name="create-a-blank-sql-database"></a>Vytvoření prázdné databáze SQL
1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/).
2. Klikněte na tlačítko **vytvořit prostředek** > **Data + úložiště** > **SQL Database**.
3. Vytvoření **prázdné** databázi s názvem **Clinic** na nový nebo existující server. Podrobné pokyny pro vytvoření databáze na webu Azure Portal najdete v tématu [první databáze Azure SQL database](sql-database-get-started-portal.md).
   
    ![Vytvoření prázdné databáze](./media/sql-database-always-encrypted/create-database.png)

Připojovací řetězec budete potřebovat v pozdější části kurzu. Po vytvoření databáze, přejděte do nové databáze Klinika a zkopírujte připojovací řetězec. Kdykoli můžete získat připojovací řetězec, ale snadno ho zkopírovat, když jste na webu Azure Portal.

1. Klikněte na tlačítko **databází SQL** > **Clinic** > **zobrazit databázové připojovací řetězce**.
2. Zkopírujte připojovací řetězec pro **ADO.NET**.
   
    ![Zkopírujte připojovací řetězec](./media/sql-database-always-encrypted/connection-strings.png)

## <a name="connect-to-the-database-with-ssms"></a>Připojit se k databázi pomocí SSMS
Otevřete SSMS a připojte se k serveru databáze Clinic.

1. Otevřete aplikaci SSMS. (Klikněte na tlačítko **připojit** > **databázový stroj** otevřít **připojit k serveru** okno, pokud není otevřený).
2. Zadejte název serveru a přihlašovací údaje. Název serveru najdete v okně databáze SQL a v připojovacím řetězci jste si zkopírovali dříve. Zadejte název včetně kompletní server *database.windows.net*.
   
    ![Zkopírujte připojovací řetězec](./media/sql-database-always-encrypted/ssms-connect.png)

Pokud **nové pravidlo brány Firewall** otevře se okno přihlášení do Azure a umožňují SSMS vytvořit za vás nové pravidlo brány firewall.

## <a name="create-a-table"></a>Vytvoření tabulky
V této části vytvoříte tabulku pro uložení dat o pacientech. Bude jím normální tabulky zpočátku – můžete nakonfigurovat šifrování v další části.

1. Rozbalte **databází**.
2. Klikněte pravým tlačítkem myši **Clinic** databáze a klikněte na tlačítko **nový dotaz**.
3. Vložte následující příkazů jazyka Transact-SQL (T-SQL) do nové okno dotazu a **Execute** ho.

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


## <a name="encrypt-columns-configure-always-encrypted"></a>Šifrování sloupců (Konfigurace funkce Always Encrypted)
Aplikace SSMS obsahuje Průvodce nastavením CMK, CEK a šifrované sloupce můžete snadno nakonfigurovat s funkcí Always Encrypted.

1. Rozbalte **databází** > **Clinic** > **tabulky**.
2. Klikněte pravým tlačítkem myši **pacientů** tabulce a vybrat **šifrování sloupce** otevřete Průvodce funkcí Always Encrypted:
   
    ![Šifrování sloupců](./media/sql-database-always-encrypted/encrypt-columns.png)

Průvodce funkcí Always Encrypted obsahuje následující oddíly: **výběr sloupce**, **konfigurace hlavního klíče** (CMK) **ověření**, a **souhrn** .

### <a name="column-selection"></a>Výběr sloupce
Klikněte na tlačítko **Další** na **ÚVOD** otevřít stránku **výběr sloupce** stránky. Na této stránce se vybrat sloupce, které chcete šifrovat, [typ šifrování a jaké šifrovací klíč sloupce (CEK)](https://msdn.microsoft.com/library/mt459280.aspx#Anchor_2) používat.

Šifrování **SSN** a **datum narození** informace pro každého pacienta. **SSN** používat deterministického šifrování, které podporuje vyhledávání rovnosti, spojení a seskupení podle sloupce. **Datum narození** sloupci použije náhodné šifrování, která nepodporuje operace.

Nastavte **typ šifrování** pro **SSN** sloupec **Deterministic** a **datum narození** sloupec, který se **Randomized** . Klikněte na **Další**.

![Šifrování sloupců](./media/sql-database-always-encrypted/column-selection.png)

### <a name="master-key-configuration"></a>Konfigurace hlavního klíče
**Konfigurace hlavního klíče** stránky je, když nastavení vašeho CMK a vybrat poskytovatele úložiště klíčů ukládat CMK. V současné době můžete uložit CMK v úložišti certifikátů Windows, Azure Key Vault nebo modulu hardwarového zabezpečení (HSM). Tento kurz ukazuje, jak ukládat klíče v úložišti certifikátů Windows.

Ověřte, že **úložiště certifikátů Windows** zaškrtnuto a klikněte na tlačítko **Další**.

![Konfigurace hlavního klíče](./media/sql-database-always-encrypted/master-key-configuration.png)

### <a name="validation"></a>Ověření
Můžete teď šifrování sloupce nebo uložit skript prostředí PowerShell spustit později. Pro účely tohoto kurzu vyberte **pokračovat, dokončete nyní** a klikněte na tlačítko **Další**.

### <a name="summary"></a>Souhrn
Ověřte, zda jsou správně nastavení a klikněte na tlačítko **Dokončit** k dokončení instalace pro Always Encrypted.

![Souhrn](./media/sql-database-always-encrypted/summary.png)

### <a name="verify-the-wizards-actions"></a>Ověření akce průvodce
Po dokončení průvodce si vaše databáze je nastavený pro Always Encrypted. Průvodce provést následující akce:

* Vytvoření CMK.
* Vytvoření CEK.
* Nakonfigurovat vybrané sloupce pro šifrování. Vaše **pacientů** tabulka nyní neobsahuje žádná data, ale je zašifrovaný všechna existující data ve vybraných sloupcích.

Vytvoření klíče v aplikaci SSMS můžete ověřit tak, že přejdete do **Clinic** > **zabezpečení** > **vždy šifrované klíče**. Nyní je vidět nové klíče, které průvodce vygenerovány pro vás.

## <a name="create-a-client-application-that-works-with-the-encrypted-data"></a>Vytvořit klientskou aplikaci, která funguje s šifrovaná data
Teď, když je nastavený s funkcí Always Encrypted, můžete vytvořit aplikaci, která provádí *vloží* a *vybere* pro šifrované sloupce. K úspěšnému spuštění ukázkové aplikace, musíte spustit ji na stejném počítači ve kterém jste spustili Průvodce funkcí Always Encrypted. Ke spuštění aplikace v jiném počítači, je nutné nasadit certifikáty s funkcí Always Encrypted k počítači se systémem klientské aplikace.  

> [!IMPORTANT]
> Vaše aplikace musí používat [SqlParameter](https://msdn.microsoft.com/library/system.data.sqlclient.sqlparameter.aspx) objekty při předávání dat ve formátu prostého textu na server s funkcí Always Encrypted sloupce. Předání hodnoty literálu bez použití SqlParameter objekty povede k výjimce.
> 
> 

1. Otevřít Visual Studio a vytvořte novou aplikaci konzoly C#. Ujistěte se, že váš projekt je nastavena na **rozhraní .NET Framework 4.6** nebo novější.
2. Pojmenujte projekt **AlwaysEncryptedConsoleApp** a klikněte na tlačítko **OK**.

![Novou konzolovou aplikaci](./media/sql-database-always-encrypted/console-app.png)

## <a name="modify-your-connection-string-to-enable-always-encrypted"></a>Upravit připojovací řetězec k povolení funkcí Always Encrypted
Tato část vysvětluje, jak povolit Always Encrypted připojovací řetězec databáze. Upraví konzolovou aplikaci, kterou jste právě vytvořili v oddílu "Always Encrypted ukázková Konzolová aplikace."

Pokud chcete povolit funkce Always Encrypted, budete muset přidat **nastavení šifrování sloupce** – klíčové slovo připojení řetězce a nastavte ho na **povoleno**.

Tento parametr můžete nastavit přímo v připojovacím řetězci, nebo ji můžete nastavit pomocí [SqlConnectionStringBuilder](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnectionstringbuilder.aspx). Ukázková aplikace v další části ukazuje, jak používat **SqlConnectionStringBuilder**.

> [!NOTE]
> Toto je jediná změna v klientské aplikaci konkrétní funkce Always Encrypted. Pokud máte existující aplikaci, která ukládá externě svůj připojovací řetězec (to znamená, že v konfiguračním souboru), je možné povolit Always Encrypted beze změny kódu.
> 
> 

### <a name="enable-always-encrypted-in-the-connection-string"></a>Povolení funkce Always Encrypted v připojovacím řetězci
Přidejte následující klíčové slovo připojovacího řetězce:

    Column Encryption Setting=Enabled


### <a name="enable-always-encrypted-with-a-sqlconnectionstringbuilder"></a>Povolit Always Encrypted s SqlConnectionStringBuilder
Následující kód ukazuje, jak povolit funkce Always Encrypted s nastavením [SqlConnectionStringBuilder.ColumnEncryptionSetting](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnectionstringbuilder.columnencryptionsetting.aspx) k [povoleno](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnectioncolumnencryptionsetting.aspx).

    // Instantiate a SqlConnectionStringBuilder.
    SqlConnectionStringBuilder connStringBuilder =
       new SqlConnectionStringBuilder("replace with your connection string");

    // Enable Always Encrypted.
    connStringBuilder.ColumnEncryptionSetting =
       SqlConnectionColumnEncryptionSetting.Enabled;



## <a name="always-encrypted-sample-console-application"></a>Always Encrypted ukázková Konzolová aplikace
Tato ukázka předvádí, jak:

* Upravte připojovací řetězec k povolení funkcí Always Encrypted.
* Vložení dat do šifrované sloupce.
* Vyberte záznam pomocí filtrování pro určitou hodnotu v šifrované sloupce.

Nahraďte obsah **Program.cs** následujícím kódem. Nahraďte připojovací řetězec connectionString globální proměnné v řádku přímo nad metodu Main platný připojovací řetězec z portálu Azure portal. Toto je jediná změna, kterou je třeba provést na tento kód.

Spusťte aplikaci v akci najdete v článku s funkcí Always Encrypted.

    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Text;
    using System.Threading.Tasks;
    using System.Data;
    using System.Data.SqlClient;

    namespace AlwaysEncryptedConsoleApp
    {
    class Program
    {
        // Update this line with your Clinic database connection string from the Azure portal.
        static string connectionString = @"Replace with your connection string";

        static void Main(string[] args)
        {
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
                SSN = "999-99-0001", FirstName = "Orlando", LastName = "Gee", BirthDate = DateTime.Parse("01/04/1964") });
            InsertPatient(new Patient() {
                SSN = "999-99-0002", FirstName = "Keith", LastName = "Harris", BirthDate = DateTime.Parse("06/20/1977") });
            InsertPatient(new Patient() {
                SSN = "999-99-0003", FirstName = "Donna", LastName = "Carreras", BirthDate = DateTime.Parse("02/09/1973") });
            InsertPatient(new Patient() {
                SSN = "999-99-0004", FirstName = "Janet", LastName = "Gates", BirthDate = DateTime.Parse("08/31/1985") });
            InsertPatient(new Patient() {
                SSN = "999-99-0005", FirstName = "Lucy", LastName = "Harrington", BirthDate = DateTime.Parse("05/06/1993") });


            // Fetch and display all patients.
            Console.WriteLine(Environment.NewLine + "All the records currently in the Patients table:");

            foreach (Patient patient in SelectAllPatients())
            {
                Console.WriteLine(patient.FirstName + " " + patient.LastName + "\tSSN: " + patient.SSN + "\tBirthdate: " + patient.BirthDate);
            }

            // Get patients by SSN.
            Console.WriteLine(Environment.NewLine + "Now let's locate records by searching the encrypted SSN column.");

            string ssn;

            // This very simple validation only checks that the user entered 11 characters.
            // In production be sure to check all user input and use the best validation for your specific application.
            do
            {
                Console.WriteLine("Please enter a valid SSN (ex. 123-45-6789):");
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


## <a name="verify-that-the-data-is-encrypted"></a>Ověřte, že se data zašifrují.
Můžete rychle zkontrolovat, že skutečná data na serveru je zašifrované pomocí dotazu **pacientů** dat pomocí aplikace SSMS. (Použít aktuální připojení kde nastavení šifrování sloupce zatím není povolená.)

Spusťte následující dotaz na databázi Clinic.

    SELECT FirstName, LastName, SSN, BirthDate FROM Patients;

Uvidíte, že šifrované sloupce nebudou obsahovat žádná data ve formátu prostého textu.

   ![Novou konzolovou aplikaci](./media/sql-database-always-encrypted/ssms-encrypted.png)

Použití SSMS k přístupu k datům ve formátu prostého textu, můžete přidat **nastavení šifrování sloupce = povoleno** parametr připojení.

1. V aplikaci SSMS klikněte pravým tlačítkem na váš server v **Průzkumník objektů**a potom klikněte na tlačítko **odpojit**.
2. Klikněte na tlačítko **připojit** > **databázový stroj** otevřít **připojit k serveru** okna a pak klikněte na tlačítko **možnosti**.
3. Klikněte na tlačítko **další parametry připojení** a typ **nastavení šifrování sloupce = povoleno**.
   
    ![Novou konzolovou aplikaci](./media/sql-database-always-encrypted/ssms-connection-parameter.png)
4. Spusťte následující dotaz **Clinic** databáze.
   
        SELECT FirstName, LastName, SSN, BirthDate FROM Patients;
   
     Nyní je vidět data ve formátu prostého textu v šifrované sloupce.

    ![Novou konzolovou aplikaci](./media/sql-database-always-encrypted/ssms-plaintext.png)



> [!NOTE]
> Pokud se připojujete pomocí aplikace SSMS (nebo jakéhokoli klienta) z jiného počítače, nebudete mít přístup k šifrovacím klíčům a nebude možné dešifrovat data.
> 
> 

## <a name="next-steps"></a>Další postup
Po vytvoření databáze s použitím funkce Always Encrypted, může být vhodné provést následující kroky:

* Tuto ukázku spusťte z jiného počítače. Nebude mít přístup k šifrovacím klíčům, nebudete mít přístup k datům ve formátu prostého textu a nebude úspěšně spuštěna.
* [Otočit a vyčištění klíče](https://msdn.microsoft.com/library/mt607048.aspx).
* [Migrovat data, která je už zašifrovali pomocí funkce Always Encrypted](https://msdn.microsoft.com/library/mt621539.aspx).
* [Nasazení certifikátů s funkcí Always Encrypted k jiným počítačům klienta](https://msdn.microsoft.com/library/mt723359.aspx#Anchor_1) (viz oddíl "Provádění certifikáty k dispozici pro aplikace a uživatelé").

## <a name="related-information"></a>Související informace
* [Funkce Always Encrypted (vývoj pro klientské)](https://msdn.microsoft.com/library/mt147923.aspx)
* [Transparentní šifrování dat](https://msdn.microsoft.com/library/bb934049.aspx)
* [Šifrování SQL serveru](https://msdn.microsoft.com/library/bb510663.aspx)
* [Průvodce Always Encrypted](https://msdn.microsoft.com/library/mt459280.aspx)
* [Blog Always Encrypted](http://blogs.msdn.com/b/sqlsecurity/archive/tags/always-encrypted/)

