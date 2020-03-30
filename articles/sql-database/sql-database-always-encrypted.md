---
title: Vždy šifrované – úložiště certifikátů systému Windows
description: Tento článek ukazuje, jak zabezpečit citlivá data v databázi SQL s šifrováním databáze pomocí Vždy šifrované průvodce v SQL Server Management Studio (SSMS). Také ukazuje, jak ukládat šifrovací klíče v úložišti certifikátů Windows.
keywords: šifrovat data, šifrování SQL, šifrování databáze, citlivá data, vždy šifrované
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: VanMSFT
ms.author: vanto
ms.reviwer: ''
ms.date: 03/08/2019
ms.openlocfilehash: 82c3c3274a8a9d66019ce906ee7be47cedac7470
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "73822041"
---
# <a name="always-encrypted-protect-sensitive-data-and-store-encryption-keys-in-the-windows-certificate-store"></a>Vždy šifrované: Ochrana citlivých dat a ukládání šifrovacích klíčů v úložišti certifikátů Windows

Tento článek ukazuje, jak zabezpečit citlivá data v databázi SQL s šifrováním databáze pomocí [Průvodce vždy šifrované](https://msdn.microsoft.com/library/mt459280.aspx) v [SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/hh213248.aspx). Také ukazuje, jak ukládat šifrovací klíče v úložišti certifikátů Windows.

Vždy šifrovaná je nová technologie šifrování dat v Azure SQL Database a SQL Serveru, která pomáhá chránit citlivá data v klidovém stavu na serveru, během pohybu mezi klientem a serverem a při používání dat, což zajišťuje, že citlivá data se nikdy nezobrazí jako prostého textu uvnitř databázového systému. Po šifrování dat mají přístup k datům ve formátu prostého textu pouze klientské aplikace nebo servery aplikací, které mají přístup ke klíčům. Podrobné informace naleznete v tématu [Vždy šifrované (Database Engine)](https://msdn.microsoft.com/library/mt163865.aspx).

Po konfiguraci databáze pro použití vždy šifrované, vytvoříte klientskou aplikaci v jazyce C# s Visual Studio pro práci s šifrovaná data.

Podle pokynů v tomto článku se dozvíte, jak nastavit vždy šifrované pro databázi Azure SQL. V tomto článku se dozvíte, jak provádět následující úkoly:

* K vytvoření [vždy šifrovaných klíčů](https://msdn.microsoft.com/library/mt163865.aspx#Anchor_3)použijte Průvodce vždy šifrovanými v ssms .
  * Vytvořte [hlavní klíč sloupce (CMK)](https://msdn.microsoft.com/library/mt146393.aspx).
  * Vytvořte [šifrovací klíč sloupce (CEK)](https://msdn.microsoft.com/library/mt146372.aspx).
* Vytvořte databázovou tabulku a šifrujte sloupce.
* Vytvořte aplikaci, která vloží, vybere a zobrazí data ze šifrovaných sloupců.

## <a name="prerequisites"></a>Požadavky

Pro účely tohoto kurzu budete potřebovat:

* Účet a předplatné Azure. Pokud ji nemáte, zaregistrujte si [bezplatnou zkušební verzi](https://azure.microsoft.com/pricing/free-trial/).
* [SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) verze 13.0.700.242 nebo novější.
* [Rozhraní .NET Framework 4.6](https://msdn.microsoft.com/library/w0x726c2.aspx) nebo novější (v klientském počítači).
* [Visual Studio](https://www.visualstudio.com/downloads/download-visual-studio-vs.aspx).

## <a name="create-a-blank-sql-database"></a>Vytvoření prázdné databáze SQL

1. Přihlaste se k [portálu Azure](https://portal.azure.com/).
2. Klepněte **na tlačítko Vytvořit databázi** > **SQL**dat**a dat úložiště** > .
3. Vytvořte **prázdnou** databázi s názvem **Klinika** na novém nebo existujícím serveru. Podrobné pokyny k vytvoření databáze na webu Azure Portal najdete v [tématu Vaše první databáze Azure SQL](sql-database-single-database-get-started.md).

    ![Vytvoření prázdné databáze](./media/sql-database-always-encrypted/create-database.png)

Budete potřebovat připojovací řetězec později v kurzu. Po vytvoření databáze přejděte do nové databáze Clinic a zkopírujte připojovací řetězec. Připojovací řetězec můžete získat kdykoli, ale je snadné ho zkopírovat, když jste na webu Azure Portal.

1. Klepněte na **položku SQL databases** > **Clinic** > **Zobrazit připojovací řetězce databáze**.
2. Zkopírujte připojovací řetězec pro **ADO.NET**.

    ![Zkopírování připojovacího řetězce](./media/sql-database-always-encrypted/connection-strings.png)

## <a name="connect-to-the-database-with-ssms"></a>Připojit se k databázi pomocí SSMS

Otevřete SSMS a připojte se k serveru pomocí databáze Clinic.

1. Otevřete aplikaci SSMS. (Klepnutím na tlačítko **Připojit** > **databázový stroj** otevřete okno Připojit k **serveru,** pokud není otevřené).
2. Zadejte název serveru a pověření. Název serveru lze nalézt v okně databáze SQL a v připojovacím řetězci, který jste zkopírovali dříve. Zadejte úplný název serveru včetně *database.windows.net*.

    ![Zkopírování připojovacího řetězce](./media/sql-database-always-encrypted/ssms-connect.png)

Pokud se otevře okno **Pravidlo nové brány firewall,** přihlaste se do Azure a nechte SSMS vytvořit nové pravidlo brány firewall za vás.

## <a name="create-a-table"></a>Vytvoření tabulky

V této části vytvoříte tabulku pro uložení údajů o pacientech. Zpočátku se bude jedná o normální tabulku – šifrování nakonfigurujete v další části.

1. Rozbalte **položku Databáze**.
2. Klepněte pravým tlačítkem myši na databázi **Kliniky** a klepněte na příkaz **Nový dotaz**.
3. Vložte následující Transact-SQL (T-SQL) do nového okna dotazu a **Spusťte** jej.

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

## <a name="encrypt-columns-configure-always-encrypted"></a>Šifrovat sloupce (konfigurace vždy šifrované)

SSMS poskytuje průvodce pro snadnou konfiguraci vždy šifrované nastavením CMK, CEK a šifrované sloupce pro vás.

1. Rozbalte**tabulky****klinik** >  **databází** > .
2. Klikněte pravým tlačítkem myši na tabulku **Pacienti** a výběrem **možnosti Šifrovat sloupce** otevřete Průvodce vždy šifrovanými:

    ![Šifrování sloupců](./media/sql-database-always-encrypted/encrypt-columns.png)

Průvodce vždy šifrovanými obsahuje následující části: **Výběr sloupců**, **Konfigurace hlavního klíče** (CMK), **Ověření**a **Souhrn**.

### <a name="column-selection"></a>Výběr sloupce

Kliknutím na **Další** na stránce **Úvod** otevřete stránku **Výběr sloupců.** Na této stránce vyberete sloupce, které chcete zašifrovat, [typ šifrování a jaký kód šifrovacího klíče (CEK)](https://msdn.microsoft.com/library/mt459280.aspx#Anchor_2) použít.

Šifrujte informace **o SSN** a **Date pro** každého pacienta. Sloupec **SSN** bude používat deterministické šifrování, které podporuje vyhledávání rovnosti, spojení a seskupení podle. Sloupec **Datum narození** bude používat randomizované šifrování, které nepodporuje operace.

Nastavte **typ šifrování** pro sloupec **SSN** na **deterministický** a sloupec **Datum narození** na **Randomizovaný**. Klikněte na **Další**.

![Šifrování sloupců](./media/sql-database-always-encrypted/column-selection.png)

### <a name="master-key-configuration"></a>Konfigurace hlavního klíče

Stránka **Konfigurace hlavního klíče** je místo, kde nastavíte CMK a vyberete zprostředkovatele úložiště klíčů, kde bude cmk uložen. V současné době můžete uložit CMK v úložišti certifikátů Windows, Azure Key Vault nebo modul hardwarového zabezpečení (HSM). Tento kurz ukazuje, jak uložit klíče v úložišti certifikátů Windows.

Ověřte, zda je **vybráno úložiště certifikátů systému Windows,** a klepněte na tlačítko **Další**.

![Konfigurace hlavního klíče](./media/sql-database-always-encrypted/master-key-configuration.png)

### <a name="validation"></a>Ověřování

Sloupce můžete nyní zašifrovat nebo uložit skript prostředí PowerShell, který chcete spustit později. V tomto kurzu vyberte **pokračovat a dokončit nyní** a klepněte na tlačítko **Další**.

### <a name="summary"></a>Souhrn

Ověřte, zda jsou všechna nastavení správná, a klepnutím na **tlačítko Dokončit** dokončete nastavení pro vždy šifrované.

![Souhrn](./media/sql-database-always-encrypted/summary.png)

### <a name="verify-the-wizards-actions"></a>Ověření akcí průvodce

Po dokončení průvodce je databáze nastavena na vždy šifrované. Průvodce provedl následující akce:

* Vytvořil CMK.
* Vytvořil CEK.
* Nakonfiguroval vybrané sloupce pro šifrování. Tabulka **Pacienti** aktuálně neobsahuje žádná data, ale všechna existující data ve vybraných sloupcích jsou nyní zašifrována.

Vytvoření klíčů v SSMS můžete ověřit tak, že přejdete na **kliniku** > **Security** > **Always Encrypted Keys**. Nyní můžete zobrazit nové klíče, které průvodce vygeneroval za vás.

## <a name="create-a-client-application-that-works-with-the-encrypted-data"></a>Vytvoření klientské aplikace, která pracuje se šifrovanými daty

Nyní, když je nastaveno vždy šifrované, můžete vytvořit aplikaci, která provádí *vložení* a *výběr v* šifrovaných sloupcích. Chcete-li ukázkovou aplikaci úspěšně spustit, je nutné ji spustit ve stejném počítači, ve kterém jste spustili Průvodce vždy šifrovanými. Chcete-li spustit aplikaci v jiném počítači, je nutné nasadit vždy šifrované certifikáty do počítače se spuštěnou klientskou aplikací.  

> [!IMPORTANT]
> Aplikace musí používat [sqlparameter](https://msdn.microsoft.com/library/system.data.sqlclient.sqlparameter.aspx) objekty při předávání dat ve formátu prostého textu na server s vždy šifrované sloupce. Předání literálových hodnot bez použití objektů SqlParameter bude mít za následek výjimku.

1. Otevřete Visual Studio a vytvořte novou aplikaci konzoly C#. Ujistěte se, že váš projekt je nastavena na **rozhraní .NET Framework 4.6** nebo novější.
2. Pojmenujte projekt **AlwaysEncryptedConsoleApp** a klepněte na tlačítko **OK**.

![Nová konzolová aplikace](./media/sql-database-always-encrypted/console-app.png)

## <a name="modify-your-connection-string-to-enable-always-encrypted"></a>Úprava připojovacího řetězce tak, aby povoloval vždy šifrované

Tato část vysvětluje, jak povolit vždy šifrované v připojovacím řetězci databáze. Aplikaci konzoly, kterou jste právě vytvořili, upravíte v další části "Vždy šifrovaná ukázková konzolová aplikace".

Chcete-li povolit možnost Vždy šifrované, je třeba přidat klíčové slovo **Nastavení šifrování sloupců** do připojovacího řetězce a nastavit jej na **hodnotu Povoleno**.

Můžete nastavit přímo v připojovacím řetězci, nebo můžete nastavit pomocí [SqlConnectionStringBuilder](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnectionstringbuilder.aspx). Ukázková aplikace v další části ukazuje, jak používat **SqlConnectionStringBuilder**.

> [!NOTE]
> Toto je jediná změna požadovaná v klientské aplikaci specifické pro vždy šifrované. Pokud máte existující aplikaci, která ukládá svůj připojovací řetězec externě (to znamená v konfiguračním souboru), můžete povolit vždy šifrované bez změny kódu.

### <a name="enable-always-encrypted-in-the-connection-string"></a>Povolit vždy šifrované v připojovacím řetězci

Do připojovacího řetězce přidejte následující klíčové slovo:

    Column Encryption Setting=Enabled

### <a name="enable-always-encrypted-with-a-sqlconnectionstringbuilder"></a>Povolit vždy šifrované pomocí sqlconnectionstringbuilderbuilder

Následující kód ukazuje, jak povolit vždy šifrované nastavením [SqlConnectionStringBuilder.ColumnEncryptionSetting](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnectionstringbuilder.columnencryptionsetting.aspx) na [Povoleno](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnectioncolumnencryptionsetting.aspx).

    // Instantiate a SqlConnectionStringBuilder.
    SqlConnectionStringBuilder connStringBuilder =
       new SqlConnectionStringBuilder("replace with your connection string");

    // Enable Always Encrypted.
    connStringBuilder.ColumnEncryptionSetting =
       SqlConnectionColumnEncryptionSetting.Enabled;

## <a name="always-encrypted-sample-console-application"></a>Vždy šifrovaná ukázková konzola

Tato ukázka ukazuje, jak:

* Upravte připojovací řetězec tak, aby byl vždy šifrován.
* Vložte data do šifrovaných sloupců.
* Vyberte záznam filtrováním pro určitou hodnotu v zašifrovaném sloupci.

Nahraďte obsah **Program.cs** následujícím kódem. Nahraďte připojovací řetězec pro globální proměnnou ConnectionString v řádku přímo nad hlavní metodou platným připojovacím řetězcem z portálu Azure. Toto je jediná změna, kterou je třeba provést v tomto kódu.

Spusťte aplikaci a podívejte se na vždy šifrované v akci.

```cs
using System;
using System.Collections.Generic;
using System.Data;
using System.Data.SqlClient;
using System.Globalization;

namespace AlwaysEncryptedConsoleApp
{
    class Program
    {
        // Update this line with your Clinic database connection string from the Azure portal.
        static string connectionString = @"Data Source = SPE-T640-01.sys-sqlsvr.local; Initial Catalog = Clinic; Integrated Security = true";

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

            CultureInfo culture = CultureInfo.CreateSpecificCulture("en-US");
            InsertPatient(new Patient()
            {
                SSN = "999-99-0001",
                FirstName = "Orlando",
                LastName = "Gee",
                BirthDate = DateTime.Parse("01/04/1964", culture)
            });
            InsertPatient(new Patient()
            {
                SSN = "999-99-0002",
                FirstName = "Keith",
                LastName = "Harris",
                BirthDate = DateTime.Parse("06/20/1977", culture)
            });
            InsertPatient(new Patient()
            {
                SSN = "999-99-0003",
                FirstName = "Donna",
                LastName = "Carreras",
                BirthDate = DateTime.Parse("02/09/1973", culture)
            });
            InsertPatient(new Patient()
            {
                SSN = "999-99-0004",
                FirstName = "Janet",
                LastName = "Gates",
                BirthDate = DateTime.Parse("08/31/1985", culture)
            });
            InsertPatient(new Patient()
            {
                SSN = "999-99-0005",
                FirstName = "Lucy",
                LastName = "Harrington",
                BirthDate = DateTime.Parse("05/06/1993", culture)
            });


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
```

## <a name="verify-that-the-data-is-encrypted"></a>Ověření šifrování dat

Můžete rychle zkontrolovat, zda jsou skutečná data na serveru šifrována dotazem na data **pacientů** pomocí SSMS. (Použijte aktuální připojení, kde ještě není povoleno nastavení šifrování sloupců.)

Spusťte následující dotaz v databázi Kliniky.

    SELECT FirstName, LastName, SSN, BirthDate FROM Patients;

Můžete vidět, že šifrované sloupce neobsahují žádná data ve formátu prostého textu.

   ![Nová konzolová aplikace](./media/sql-database-always-encrypted/ssms-encrypted.png)

Chcete-li použít ssms pro přístup k datům ve formátu prostého textu, můžete k připojení přidat parametr **Column Encryption Setting=enabled.**

1. V ssms klepněte pravým tlačítkem myši na server v **Průzkumníkovi objektů**a potom klepněte na příkaz **Odpojit**.
2. Klepnutím na **tlačítko Připojit** > **databázový stroj** otevřete okno Připojit k **serveru** a klepněte na tlačítko **Možnosti**.
3. Klepněte na **položku Další parametry připojení** a zadejte **příkaz Column Encryption Setting=enabled**.

    ![Nová konzolová aplikace](./media/sql-database-always-encrypted/ssms-connection-parameter.png)
4. Spusťte následující dotaz v databázi **Kliniky.**

        SELECT FirstName, LastName, SSN, BirthDate FROM Patients;

     Nyní můžete vidět data ve formátu prostého textu v šifrovaných sloupcích.

    ![Nová konzolová aplikace](./media/sql-database-always-encrypted/ssms-plaintext.png)

> [!NOTE]
> Pokud se připojíte k SSMS (nebo libovolnému klientovi) z jiného počítače, nebude mít přístup k šifrovacím klíčům a nebude moci data dešifrovat.

## <a name="next-steps"></a>Další kroky

Po vytvoření databáze, která používá vždy šifrované, můžete provést následující kroky:

* Spusťte tuto ukázku z jiného počítače. Nebude mít přístup k šifrovacím klíčům, takže nebude mít přístup k datům ve formátu prostého textu a nebude úspěšně spuštěn.
* [Otočte a vyčistěte klíče](https://msdn.microsoft.com/library/mt607048.aspx).
* [Migrujte data, která jsou již zašifrována pomocí funkce Vždy šifrované](https://msdn.microsoft.com/library/mt621539.aspx).
* [Nasazení vždy šifrovaných certifikátů do jiných klientských počítačů](https://msdn.microsoft.com/library/mt723359.aspx#Anchor_1) (viz část Zpřístupnění certifikátů aplikacím a uživatelům).

## <a name="related-information"></a>Související informace

* [Vždy šifrované (vývoj klienta)](https://msdn.microsoft.com/library/mt147923.aspx)
* [Transparentní šifrování dat](https://msdn.microsoft.com/library/bb934049.aspx)
* [Šifrování serveru SQL Server](https://msdn.microsoft.com/library/bb510663.aspx)
* [Vždy šifrovaný průvodce](https://msdn.microsoft.com/library/mt459280.aspx)
* [Vždy šifrovaný blog](https://blogs.msdn.com/b/sqlsecurity/archive/tags/always-encrypted/)