---
title: 'Funkce Always Encrypted: Azure SQL Database – úložiště certifikátů Windows | Microsoft Docs'
description: Tento článek ukazuje, jak zajistit citlivá data v databázi SQL s šifrování databáze pomocí Průvodce vždycky šifrovaná v serveru SQL Server Management Studio (SSMS). Je také ukazuje, jak chcete ukládat šifrovací klíče v úložišti certifikátů systému Windows.
keywords: šifrování dat, šifrování sql, šifrování databáze, citlivých dat, vždycky šifrovaná.
services: sql-database
author: stevestein
manager: jhubbard
ms.service: sql-database
ms.custom: security
ms.topic: article
ms.date: 04/01/2018
ms.author: sstein
ms.openlocfilehash: 38c4a724cc963370e60ae796729476c3bbc0342a
ms.sourcegitcommit: 3a4ebcb58192f5bf7969482393090cb356294399
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2018
---
# <a name="always-encrypted-protect-sensitive-data-in-sql-database-and-store-your-encryption-keys-in-the-windows-certificate-store"></a>Funkce Always Encrypted: Chrání citlivá data v databázi SQL a ukládat šifrovací klíče v úložišti certifikátů systému Windows

V tomto článku se dozvíte, jak zajistit citlivá data v databázi SQL s šifrování databáze pomocí [vždy šifrované průvodce](https://msdn.microsoft.com/library/mt459280.aspx) v [SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/hh213248.aspx). Je také ukazuje, jak chcete ukládat šifrovací klíče v úložišti certifikátů systému Windows.

Vždy šifrovaný je nová technologie šifrování dat v Azure SQL Database a SQL Server, který pomáhá chránit citlivá data v klidovém stavu na serveru během pohybu mezi klientem a serverem a dobu používání dat, zajistit, že citlivá data nikdy se zobrazí jako prostý text v databázi systému. Po šifrování dat, můžete přistupovat pouze klientské aplikace nebo aplikační servery, které mají přístup ke klíčům data ve formátu prostého textu. Podrobné informace najdete v tématu [vždycky šifrovaná (databázový stroj)](https://msdn.microsoft.com/library/mt163865.aspx).

Po dokončení konfigurace databáze pro použití funkce Always Encrypted, vytvoříte klientskou aplikaci v jazyce C# pomocí sady Visual Studio pro práci s šifrovaná data.

Postupujte podle kroků v tomto článku se dozvíte, jak nastavit vždy šifrována pro Azure SQL database. V tomto článku se dozvíte, jak provádět následující úlohy:

* Pomocí Průvodce vždycky šifrovaná v aplikaci SSMS vytvořit [vždy šifrované klíče](https://msdn.microsoft.com/library/mt163865.aspx#Anchor_3).
  * Vytvoření [sloupce hlavního klíče (CMK)](https://msdn.microsoft.com/library/mt146393.aspx).
  * Vytvoření [šifrovací klíč sloupce (CEK)](https://msdn.microsoft.com/library/mt146372.aspx).
* Vytvořit tabulku databáze a šifrování sloupců.
* Vytvořte aplikaci, která vloží, vybere a zobrazí data z šifrované sloupce.

## <a name="prerequisites"></a>Požadavky
V tomto kurzu budete potřebovat:

* Účet a předplatné Azure. Pokud nemáte, si zaregistrovat [bezplatnou zkušební verzi](https://azure.microsoft.com/pricing/free-trial/).
* [SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) verze 13.0.700.242 nebo novější.
* [Rozhraní .NET framework 4.6](https://msdn.microsoft.com/library/w0x726c2.aspx) nebo novější (v klientském počítači).
* Sadu [Visual Studio](https://www.visualstudio.com/downloads/download-visual-studio-vs.aspx).

## <a name="create-a-blank-sql-database"></a>Vytvoření prázdné databáze SQL
1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/).
2. Klikněte na tlačítko **vytvořit prostředek** > **Data + úložiště** > **SQL Database**.
3. Vytvoření **prázdné** databáze s názvem **Klinika** na nový nebo existující server. Podrobné pokyny k vytvoření databáze na portálu Azure, najdete v části [svoji první databázi Azure SQL](sql-database-get-started-portal.md).
   
    ![Vytvoření prázdné databáze](./media/sql-database-always-encrypted/create-database.png)

Později v tomto kurzu budete potřebovat připojovací řetězec. Po vytvoření databáze, přejděte k nové databázi Klinika a zkopírujte připojovací řetězec. Kdykoli můžete získat připojovací řetězec, ale je snadné a zkopírujte ho, když jste na portálu Azure.

1. Klikněte na tlačítko **databází SQL** > **Klinika** > **zobrazit databázové připojovací řetězce**.
2. Zkopírujte připojovací řetězec pro **ADO.NET**.
   
    ![Zkopírujte připojovací řetězec](./media/sql-database-always-encrypted/connection-strings.png)

## <a name="connect-to-the-database-with-ssms"></a>Připojit se k databázi pomocí SSMS
Otevřete aplikaci SSMS a připojení k serveru s databází Klinika.

1. Otevřete aplikaci SSMS. (Klikněte na tlačítko **připojit** > **databázový stroj** otevřete **připojit k serveru** okno, pokud není otevřený).
2. Zadejte název serveru a přihlašovací údaje. Název serveru naleznete v okně databáze SQL a v připojovacím řetězci jste zkopírovali dříve. Zadejte, včetně názvu kompletní server *database.windows.net*.
   
    ![Zkopírujte připojovací řetězec](./media/sql-database-always-encrypted/ssms-connect.png)

Pokud **nové pravidlo brány Firewall** okno otevře, přihlaste k Azure a umožňují SSMS za vás vytvořit nové pravidlo brány firewall.

## <a name="create-a-table"></a>Vytvoření tabulky
V této části vytvoříte tabulku pro uložení pacienta data. To bude normální tabulku původně – budete konfigurovat šifrování v další části.

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
Aplikace SSMS poskytuje průvodce nastavením CMK, CEK a šifrované sloupce můžete snadno konfigurovat vždy šifrována.

1. Rozbalte položku **databáze** > **Klinika** > **tabulky**.
2. Klikněte pravým tlačítkem myši **pacientů** tabulky a vyberte **šifrování sloupců** otevřete Průvodce vždycky šifrovaná:
   
    ![Šifrování sloupců](./media/sql-database-always-encrypted/encrypt-columns.png)

Průvodce vždy šifrována obsahuje následující oddíly: **sloupců výběru**, **konfigurace hlavního klíče** (CMK), **ověření**, a **Souhrn**.

### <a name="column-selection"></a>Výběr sloupce
Klikněte na tlačítko **Další** na **ÚVOD** otevřít stránku **sloupců výběru** stránky. Na této stránce se vybrat sloupce, které chcete šifrovat, [typ šifrování a jaké šifrovací klíč sloupce (CEK)](https://msdn.microsoft.com/library/mt459280.aspx#Anchor_2) používat.

Šifrování **SSN** a **datum narození** informace pro každý pacienta. **SSN** používat deterministickou šifrování, která podporuje rovnosti vyhledávání, spojení a seskupit podle sloupce. **Datum narození** sloupec použije náhodnou šifrování, která nepodporuje operace.

Nastavte **typ šifrování** pro **SSN** sloupec, který se **Deterministic** a **datum narození** sloupec, který se **Randomized**. Klikněte na **Další**.

![Šifrování sloupců](./media/sql-database-always-encrypted/column-selection.png)

### <a name="master-key-configuration"></a>Konfigurace hlavního klíče
**Hlavní klíč konfigurace** stránka je kde nastavení vaší CMK a vybrat zprostředkovatele úložiště klíčů uložení CMK. V současné době můžete uložit CMK v úložišti certifikátů systému Windows, Azure Key Vault nebo modul hardwarového zabezpečení (HSM). Tento kurz ukazuje, jak k ukládání svých klíčů v úložišti certifikátů systému Windows.

Ověřte, že **úložiště certifikátů Windows** je vybrána a klikněte na tlačítko **Další**.

![Konfigurace hlavního klíče](./media/sql-database-always-encrypted/master-key-configuration.png)

### <a name="validation"></a>Ověření
Můžete teď šifrování sloupců nebo uložení skriptu prostředí PowerShell spustit později. V tomto kurzu vyberte **přejít k dokončení teď** a klikněte na tlačítko **Další**.

### <a name="summary"></a>Souhrn
Ověřte, zda jsou všechny správné nastavení a klikněte na tlačítko **Dokončit** dokončit nastavení pro vždy šifrována.

![Souhrn](./media/sql-database-always-encrypted/summary.png)

### <a name="verify-the-wizards-actions"></a>Ověřte v Průvodci akce
Po dokončení Průvodce vaše databáze je nastavena pro vždy šifrována. Průvodce provést následující akce:

* Vytvořit CMK.
* Vytvořit CEK.
* Nakonfigurovat vybrané sloupce pro šifrování. Vaše **pacientů** tabulka nyní neobsahuje žádná data, ale je zašifrovaný jakákoli stávající data ve vybraných sloupcích.

Vytvoření klíče v aplikaci SSMS můžete ověřit tak, že přejdete do **Klinika** > **zabezpečení** > **vždy šifrované klíče**. Nyní uvidíte novou klíčů, které pro vás vygeneroval průvodce.

## <a name="create-a-client-application-that-works-with-the-encrypted-data"></a>Vytvořit klientskou aplikaci, která funguje s šifrovaná data
Teď, když je funkce Always Encrypted nastavili, můžete vytvořit aplikaci, která provádí *vloží* a *vybere* pro šifrované sloupce. K úspěšnému spuštění ukázkové aplikace, je nutné jej spustit na stejném počítači kde jste spustili Průvodce vždycky šifrovaná. Ke spuštění aplikace v jiném počítači, musíte do počítače s klientskou aplikaci nasadit certifikáty vždy šifrována.  

> [!IMPORTANT]
> Vaše aplikace musí používat [SqlParameter](https://msdn.microsoft.com/library/system.data.sqlclient.sqlparameter.aspx) objekty při předávání dat ve formátu prostého textu na server s vždy šifrované sloupce. Předávání hodnot literál bez použití SqlParameter objekty způsobí výjimku.
> 
> 

1. Otevřete Visual Studio a vytvořte novou aplikaci konzoly C#. Ujistěte se, že váš projekt je nastaven na **rozhraní .NET Framework 4.6** nebo novější.
2. Název projektu **AlwaysEncryptedConsoleApp** a klikněte na tlačítko **OK**.

![Novou konzolovou aplikaci](./media/sql-database-always-encrypted/console-app.png)

## <a name="modify-your-connection-string-to-enable-always-encrypted"></a>Upravit připojovací řetězec k povolení funkce Always Encrypted
Tato část vysvětluje, jak povolit funkce Always Encrypted v připojovací řetězec databáze. Upraví konzolovou aplikaci, kterou jste právě vytvořili, v další části, "Always Encrypted ukázkovou aplikaci konzoly."

Pokud chcete povolit, vždycky šifrovaná, je nutné přidat **nastavení šifrování sloupec** – klíčové slovo k připojení k řetězec a nastavte ji na **povoleno**.

To můžete nastavit přímo v připojovacím řetězci, nebo můžete ho nastavit pomocí [SqlConnectionStringBuilder](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnectionstringbuilder.aspx). Ukázkovou aplikaci v další části ukazuje, jak používat **SqlConnectionStringBuilder**.

> [!NOTE]
> Toto je vyžadována v aplikaci klienta konkrétní vždy šifrované pouze změna. Pokud máte existující aplikace, která ukládá externě svém připojovacím řetězci (který je v konfiguračním souboru), je možné povolit funkce Always Encrypted aniž byste měnili kód.
> 
> 

### <a name="enable-always-encrypted-in-the-connection-string"></a>Povolení funkce Always Encrypted v připojovacím řetězci
Přidejte následující klíčové slovo připojovacího řetězce:

    Column Encryption Setting=Enabled


### <a name="enable-always-encrypted-with-a-sqlconnectionstringbuilder"></a>Povolit vždy šifrován SqlConnectionStringBuilder
Následující kód ukazuje, jak povolit funkce Always Encrypted nastavením [SqlConnectionStringBuilder.ColumnEncryptionSetting](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnectionstringbuilder.columnencryptionsetting.aspx) k [povoleno](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnectioncolumnencryptionsetting.aspx).

    // Instantiate a SqlConnectionStringBuilder.
    SqlConnectionStringBuilder connStringBuilder =
       new SqlConnectionStringBuilder("replace with your connection string");

    // Enable Always Encrypted.
    connStringBuilder.ColumnEncryptionSetting =
       SqlConnectionColumnEncryptionSetting.Enabled;



## <a name="always-encrypted-sample-console-application"></a>Vždy šifrovaný ukázkovou aplikaci konzoly
Tento příklad znázorňuje postup:

* Upravte připojovací řetězec k povolení funkce Always Encrypted.
* Vložení dat do šifrované sloupce.
* Vyberte záznam pomocí filtrování pro konkrétní hodnoty v šifrovaný sloupec.

Nahraďte obsah **Program.cs** následujícím kódem. Připojovací řetězec pro globální connectionString proměnná na řádku přímo nad metodu Main nahraďte platný připojovací řetězec z portálu Azure. Toto je pouze změny, které musíte udělat na tento kód.

Spusťte aplikaci v akci zobrazovat vždy šifrována.

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


## <a name="verify-that-the-data-is-encrypted"></a>Ověřte, že je šifrovaná data
Můžete rychle zjistit, že skutečná data na serveru je zašifrované pomocí dotazu **pacientů** dat pomocí aplikace SSMS. (Použít aktuální připojení kde nastavení šifrování sloupec zatím není povolená.)

Spuštěním následujícího dotazu v databázi Klinika.

    SELECT FirstName, LastName, SSN, BirthDate FROM Patients;

Uvidíte, že šifrované sloupce neobsahují žádná data ve formátu prostého textu.

   ![Novou konzolovou aplikaci](./media/sql-database-always-encrypted/ssms-encrypted.png)

Pomocí aplikace SSMS přístup k datům ve formátu prostého textu, můžete přidat **nastavení šifrování sloupec = povoleno** parametr pro připojení.

1. V aplikaci SSMS, klikněte pravým tlačítkem na váš server v **Průzkumník objektů**a potom klikněte na **odpojení**.
2. Klikněte na tlačítko **připojit** > **databázový stroj** otevřete **připojit k serveru** okna a pak klikněte na tlačítko **možnosti**.
3. Klikněte na tlačítko **další parametry připojení** a typ **nastavení šifrování sloupec = povoleno**.
   
    ![Novou konzolovou aplikaci](./media/sql-database-always-encrypted/ssms-connection-parameter.png)
4. Spusťte následující dotaz **Klinika** databáze.
   
        SELECT FirstName, LastName, SSN, BirthDate FROM Patients;
   
     Nyní můžete vidět data jako prostý text v šifrované sloupce.

    ![Novou konzolovou aplikaci](./media/sql-database-always-encrypted/ssms-plaintext.png)



> [!NOTE]
> Pokud se připojujete pomocí aplikace SSMS (nebo libovolného klienta) z jiného počítače, nebudete mít přístup k šifrovacím klíčům a nebude moci dešifrovat data.
> 
> 

## <a name="next-steps"></a>Další postup
Po vytvoření databáze, která používá vždycky šifrovaná, můžete provést následující akce:

* Tuto ukázku spusťte z jiného počítače. Nebude mít přístup k šifrovacím klíčům, tak, aby ho nebudete mít přístup k datům ve formátu prostého textu a nespustí úspěšně.
* [Otočit a vyčištění klíče](https://msdn.microsoft.com/library/mt607048.aspx).
* [Migraci dat, která už je šifrovaný pomocí funkce Always Encrypted](https://msdn.microsoft.com/library/mt621539.aspx).
* [Funkce Always Encrypted certifikáty nasadit do jiných počítačů klienta](https://msdn.microsoft.com/library/mt723359.aspx#Anchor_1) (naleznete v části "Vytváření certifikáty k dispozici pro aplikace a uživatelé").

## <a name="related-information"></a>Související informace
* [Funkce Always Encrypted (vývoj pro klienta)](https://msdn.microsoft.com/library/mt147923.aspx)
* [Transparentní šifrování dat](https://msdn.microsoft.com/library/bb934049.aspx)
* [Šifrování SQL serveru](https://msdn.microsoft.com/library/bb510663.aspx)
* [Vždy šifrované Průvodce](https://msdn.microsoft.com/library/mt459280.aspx)
* [Vždy šifrované Blog](http://blogs.msdn.com/b/sqlsecurity/archive/tags/always-encrypted/)

