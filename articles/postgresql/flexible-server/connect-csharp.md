---
title: 'Rychlý Start: připojení pomocí C#-Azure Database for PostgreSQL-flexibilního serveru'
description: V tomto rychlém startu najdete vzorový kód jazyka C# (.NET), který můžete použít k připojení a dotazování dat z Azure Database for PostgreSQL-flexibilního serveru.
author: mksuni
ms.author: sumuth
ms.service: postgresql
ms.custom: mvc, devcenter, devx-track-csharp
ms.devlang: csharp
ms.topic: quickstart
ms.date: 01/16/2021
ms.openlocfilehash: fd6df64482c5eaeec79fe5f2c9a92aaae475dfdc
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "98606095"
---
# <a name="quickstart-use-net-c-to-connect-and-query-data-in-azure-database-for-postgresql---flexible-server"></a>Rychlý Start: použití rozhraní .NET (C#) k připojení a dotazování dat v Azure Database for PostgreSQL-flexibilním serveru

Tento rychlý start ukazuje, jak se připojit ke službě Azure Database for MySQL pomocí aplikace v C#. Ukazuje, jak pomocí příkazů jazyka SQL dotazovat, vkládat, aktualizovat a odstraňovat data v databázi. Kroky v tomto článku předpokládají, že máte zkušenosti s vývojem pomocí C# a teprve začínáte pracovat se službou Azure Database for PostgreSQL.

## <a name="prerequisites"></a>Předpoklady
Pro tento rychlý Start budete potřebovat:

- Účet Azure s aktivním předplatným. [Vytvořte si účet zdarma](https://azure.microsoft.com/free).
- Vytvoření Azure Database for PostgreSQL flexibilního serveru pomocí [Azure Portal](./quickstart-create-server-portal.md) <br/> nebo [Azure CLI](./quickstart-create-server-cli.md) , pokud ho ještě nemáte.
- Použijte prázdnou databázi *Postgres* , která je k dispozici na serveru, nebo vytvořte [novou databázi](./quickstart-create-server-portal.md#connect-to-the-postgresql-database-using-psql).
- Nainstalujte [.NET Framework](https://www.microsoft.com/net/download) pro vaši platformu (Windows, Ubuntu Linux nebo MacOS).
- Nainstalujte [Visual Studio](https://www.visualstudio.com/downloads/) a sestavte projekt.
- Nainstalujte balíček NuGet [Npgsql](https://www.nuget.org/packages/Npgsql/) v aplikaci Visual Studio.

## <a name="get-connection-information"></a>Získání informací o připojení
Získejte informace o připojení potřebné pro připojení ke službě Azure Database for PostgreSQL. Potřebujete plně kvalifikovaný název serveru a přihlašovací údaje.

1. Přihlaste se k [Azure Portal](https://portal.azure.com/).
2. V nabídce vlevo na webu Azure Portal klikněte na **Všechny prostředky** a vyhledejte vytvořený server (například **mydemoserver**).
3. Klikněte na název serveru.
4. Na panelu **Přehled** serveru si poznamenejte **Název serveru** a **Přihlašovací jméno správce serveru**. Pokud zapomenete své heslo, můžete ho na tomto panelu také resetovat.
 :::image type="content" source="./media/connect-csharp/1-connection-string.png" alt-text="Název serveru Azure Database for PostgreSQL":::

## <a name="step-1-connect-and-insert-data"></a>Krok 1: připojení a vložení dat
Použijte následující kód k připojení a načtení dat pomocí příkazů jazyka SQL **CREATE TABLE** a **INSERT INTO**. Kód používá třídu využívá npgsqlcommand s metodou:
- [Otevřete ()](https://www.npgsql.org/doc/api/Npgsql.NpgsqlConnection.html#Npgsql_NpgsqlConnection_Open) , aby se navázalo připojení k databázi PostgreSQL.
- [CreateCommand ()](https://www.npgsql.org/doc/api/Npgsql.NpgsqlConnection.html#Npgsql_NpgsqlConnection_CreateCommand) nastaví vlastnost CommandText.
- Metoda [ExecuteNonQuery ()](https://www.npgsql.org/doc/api/Npgsql.NpgsqlCommand.html#Npgsql_NpgsqlCommand_ExecuteNonQuery) pro spuštění databázových příkazů.

> [!IMPORTANT]
> Nahraďte parametry Host (hostitel), DBName (název databáze), User (uživatel) a Password (heslo) hodnotami, které jste zadali při vytváření vlastního serveru a databáze.

```csharp
using System;
using Npgsql;

namespace Driver
{
    public class AzurePostgresCreate
    {
        // Obtain connection string information from the portal
        //
        private static string Host = "mydemoserver.postgres.database.azure.com";
        private static string User = "mylogin";
        private static string  DBname = "postgres";
        private static string Password = "<server_admin_password>";
        private static string Port = "5432";

        static void Main(string[] args)
        {
            // Build connection string using parameters from portal
            //
            string connString =
                String.Format(
                    "Server={0};Username={1};Database={2};Port={3};Password={4};SSLMode=Prefer",
                    Host,
                    User,
                    DBname,
                    Port,
                    Password);


            using (var conn = new NpgsqlConnection(connString))

            {
                Console.Out.WriteLine("Opening connection");
                conn.Open();

                using (var command = new NpgsqlCommand("DROP TABLE IF EXISTS inventory", conn))
                {
                    command.ExecuteNonQuery();
                    Console.Out.WriteLine("Finished dropping table (if existed)");

                }

                using (var command = new NpgsqlCommand("CREATE TABLE inventory(id serial PRIMARY KEY, name VARCHAR(50), quantity INTEGER)", conn))
                {
                    command.ExecuteNonQuery();
                    Console.Out.WriteLine("Finished creating table");
                }

                using (var command = new NpgsqlCommand("INSERT INTO inventory (name, quantity) VALUES (@n1, @q1), (@n2, @q2), (@n3, @q3)", conn))
                {
                    command.Parameters.AddWithValue("n1", "banana");
                    command.Parameters.AddWithValue("q1", 150);
                    command.Parameters.AddWithValue("n2", "orange");
                    command.Parameters.AddWithValue("q2", 154);
                    command.Parameters.AddWithValue("n3", "apple");
                    command.Parameters.AddWithValue("q3", 100);

                    int nRows = command.ExecuteNonQuery();
                    Console.Out.WriteLine(String.Format("Number of rows inserted={0}", nRows));
                }
            }

            Console.WriteLine("Press RETURN to exit");
            Console.ReadLine();
        }
    }
}
```

[Máte nějaké problémy? Dejte nám prosím jistotu.](https://github.com/MicrosoftDocs/azure-docs/issues)

## <a name="step-2-read-data"></a>Krok 2: čtení dat
Pomocí následujícího kódu se připojte a načtěte data s využitím příkazu **SELECT** jazyka SQL. Kód používá třídu využívá npgsqlcommand s metodou:
- [Otevřete ()](https://www.npgsql.org/doc/api/Npgsql.NpgsqlConnection.html#Npgsql_NpgsqlConnection_Open) , aby se navázalo připojení k PostgreSQL.
- [CreateCommand ()](https://www.npgsql.org/doc/api/Npgsql.NpgsqlConnection.html#Npgsql_NpgsqlConnection_CreateCommand) a [ExecuteReader ()](https://www.npgsql.org/doc/api/Npgsql.NpgsqlCommand.html#Npgsql_NpgsqlCommand_ExecuteReader) pro spuštění databázových příkazů.
- [Čtení ()](https://www.npgsql.org/doc/api/Npgsql.NpgsqlDataReader.html#Npgsql_NpgsqlDataReader_Read) pro přechod k záznamu ve výsledcích.
- [GetInt32 ()](https://www.npgsql.org/doc/api/Npgsql.NpgsqlDataReader.html#Npgsql_NpgsqlDataReader_GetInt32_System_Int32_) a [GetString ()](https://www.npgsql.org/doc/api/Npgsql.NpgsqlDataReader.html#Npgsql_NpgsqlDataReader_GetString_System_Int32_) pro analýzu hodnot v záznamu.

> [!IMPORTANT]
> Nahraďte parametry Host (hostitel), DBName (název databáze), User (uživatel) a Password (heslo) hodnotami, které jste zadali při vytváření vlastního serveru a databáze.

```csharp
using System;
using Npgsql;

namespace Driver
{
    public class AzurePostgresRead
    {
        // Obtain connection string information from the portal
        //
        private static string Host = "mydemoserver.postgres.database.azure.com";
        private static string User = "mylogin";
        private static string  DBname = "postgres";
        private static string Password = "<server_admin_password>";
        private static string Port = "5432";

        static void Main(string[] args)
        {
            // Build connection string using parameters from portal
            //
            string connString =
                String.Format(
                    "Server={0}; User Id={1}; Database={2}; Port={3}; Password={4};SSLMode=Prefer",
                    Host,
                    User,
                    DBname,
                    Port,
                    Password);

            using (var conn = new NpgsqlConnection(connString))
            {

                Console.Out.WriteLine("Opening connection");
                conn.Open();


                using (var command = new NpgsqlCommand("SELECT * FROM inventory", conn))
                {

                    var reader = command.ExecuteReader();
                    while (reader.Read())
                    {
                        Console.WriteLine(
                            string.Format(
                                "Reading from table=({0}, {1}, {2})",
                                reader.GetInt32(0).ToString(),
                                reader.GetString(1),
                                reader.GetInt32(2).ToString()
                                )
                            );
                    }
                    reader.Close();
                }
            }

            Console.WriteLine("Press RETURN to exit");
            Console.ReadLine();
        }
    }
}
```
[Máte nějaké problémy? Dejte nám prosím jistotu.](https://github.com/MicrosoftDocs/azure-docs/issues)


## <a name="step-3-update-data"></a>Krok 3: aktualizace dat
Pomocí následujícího kódu se připojte a aktualizujte data s využitím příkazu **UPDATE** jazyka SQL. Kód používá třídu využívá npgsqlcommand s metodou:
- [Otevřete ()](https://www.npgsql.org/doc/api/Npgsql.NpgsqlConnection.html#Npgsql_NpgsqlConnection_Open) , aby se navázalo připojení k PostgreSQL.
- [CreateCommand ()](https://www.npgsql.org/doc/api/Npgsql.NpgsqlConnection.html#Npgsql_NpgsqlConnection_CreateCommand)nastaví vlastnost CommandText.
- Metoda [ExecuteNonQuery ()](https://www.npgsql.org/doc/api/Npgsql.NpgsqlCommand.html#Npgsql_NpgsqlCommand_ExecuteNonQuery) pro spuštění databázových příkazů.

> [!IMPORTANT]
> Nahraďte parametry Host (hostitel), DBName (název databáze), User (uživatel) a Password (heslo) hodnotami, které jste zadali při vytváření vlastního serveru a databáze.

```csharp
using System;
using Npgsql;

namespace Driver
{
    public class AzurePostgresUpdate
    {
        // Obtain connection string information from the portal
        //
        private static string Host = "mydemoserver.postgres.database.azure.com";
        private static string User = "mylogin";
        private static string  DBname = "postgres";
        private static string Password = "<server_admin_password>";
        private static string Port = "5432";

        static void Main(string[] args)
        {
            // Build connection string using parameters from portal
            //
            string connString =
                String.Format(
                    "Server={0}; User Id={1}; Database={2}; Port={3}; Password={4};SSLMode=Prefer",
                    Host,
                    User,
                    DBname,
                    Port,
                    Password);

            using (var conn = new NpgsqlConnection(connString))
            {

                Console.Out.WriteLine("Opening connection");
                conn.Open();

                using (var command = new NpgsqlCommand("UPDATE inventory SET quantity = @q WHERE name = @n", conn))
                {
                    command.Parameters.AddWithValue("n", "banana");
                    command.Parameters.AddWithValue("q", 200);
                    int nRows = command.ExecuteNonQuery();
                    Console.Out.WriteLine(String.Format("Number of rows updated={0}", nRows));
                }
            }

            Console.WriteLine("Press RETURN to exit");
            Console.ReadLine();
        }
    }
}


```

[Máte nějaké problémy? Dejte nám prosím jistotu.](https://github.com/MicrosoftDocs/azure-docs/issues)

## <a name="step-4-delete-data"></a>Krok 4: odstranění dat
Pomocí následujícího kódu se připojte a odstraňte data s využitím příkazu **DELETE** jazyka SQL.

Tento kód pro navázání připojení k databázi PostgreSQL využívá třídu NpgsqlCommand s metodou [Open()](https://www.npgsql.org/doc/api/Npgsql.NpgsqlConnection.html#Npgsql_NpgsqlConnection_Open). Potom tento kód použije metodu [CreateCommand()](https://www.npgsql.org/doc/api/Npgsql.NpgsqlConnection.html#Npgsql_NpgsqlConnection_CreateCommand), nastaví vlastnost CommandText a volá metodu [ExecuteNonQuery()](https://www.npgsql.org/doc/api/Npgsql.NpgsqlCommand.html#Npgsql_NpgsqlCommand_ExecuteNonQuery) pro spuštění databázových příkazů.

> [!IMPORTANT]
> Nahraďte parametry Host (hostitel), DBName (název databáze), User (uživatel) a Password (heslo) hodnotami, které jste zadali při vytváření vlastního serveru a databáze.

```csharp
using System;
using Npgsql;

namespace Driver
{
    public class AzurePostgresDelete
    {
        // Obtain connection string information from the portal
        //
        private static string Host = "mydemoserver.postgres.database.azure.com";
        private static string User = "mylogin@mydemoserver";
        private static string  DBname = "postgres";
        private static string Password = "<server_admin_password>";
        private static string Port = "5432";

        static void Main(string[] args)
        {
            // Build connection string using parameters from portal
            //
            string connString =
                String.Format(
                    "Server={0}; User Id={1}; Database={2}; Port={3}; Password={4};SSLMode=Prefer",
                    Host,
                    User,
                    DBname,
                    Port,
                    Password);

            using (var conn = new NpgsqlConnection(connString))
            {
                Console.Out.WriteLine("Opening connection");
                conn.Open();

                using (var command = new NpgsqlCommand("DELETE FROM inventory WHERE name = @n", conn))
                {
                    command.Parameters.AddWithValue("n", "orange");

                    int nRows = command.ExecuteNonQuery();
                    Console.Out.WriteLine(String.Format("Number of rows deleted={0}", nRows));
                }
            }

            Console.WriteLine("Press RETURN to exit");
            Console.ReadLine();
        }
    }
}

```

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud chcete vyčistit všechny prostředky používané v rámci tohoto rychlého startu, odstraňte skupinu prostředků pomocí následujícího příkazu:

```azurecli
az group delete \
    --name $AZ_RESOURCE_GROUP \
    --yes
```

## <a name="next-steps"></a>Další kroky
> [!div class="nextstepaction"]
> [Správa serveru Azure Database for MySQL pomocí portálu](./how-to-manage-server-portal.md)<br/>

> [!div class="nextstepaction"]
> [Správa serveru Azure Database for MySQL pomocí rozhraní příkazového řádku](./how-to-manage-server-cli.md)
