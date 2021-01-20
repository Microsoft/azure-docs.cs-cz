---
title: 'Rychlý Start: připojení pomocí C#-Azure Database for MySQL flexibilního serveru'
description: V tomto rychlém startu najdete vzorový kód jazyka C# (.NET), který můžete použít k připojení a dotazování dat z Azure Database for MySQL flexibilního serveru.
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.custom: mvc, devx-track-csharp
ms.devlang: csharp
ms.topic: quickstart
ms.date: 01/16/2021
ms.openlocfilehash: c13b8392a766dc29d8c83f9986d0b43271caabbb
ms.sourcegitcommit: fc401c220eaa40f6b3c8344db84b801aa9ff7185
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/20/2021
ms.locfileid: "98606102"
---
# <a name="quickstart-use-net-c-to-connect-and-query-data-in-azure-database-for-mysql---flexible-server"></a>Rychlý Start: použití rozhraní .NET (C#) k připojení a dotazování dat v Azure Database for MySQL-flexibilním serveru

Tento rychlý start ukazuje, jak se připojit ke službě Azure Database for MySQL s použitím aplikace v jazyce C#. Ukazuje, jak pomocí příkazů jazyka SQL dotazovat, vkládat, aktualizovat a odstraňovat data v databázi.

## <a name="prerequisites"></a>Požadavky
Pro tento rychlý Start budete potřebovat:

- Účet Azure s aktivním předplatným. [Vytvořte si účet zdarma](https://azure.microsoft.com/free).
- Vytvoření Azure Database for MySQL flexibilního serveru pomocí [Azure Portal](./quickstart-create-server-portal.md) <br/> nebo [Azure CLI](./quickstart-create-server-cli.md) , pokud ho ještě nemáte.
- Na základě toho, jestli používáte veřejný nebo privátní přístup, proveďte **jednu** z následujících akcí, aby se povolilo připojení.
- [Vytvoření databáze a uživatele bez role správce](../howto-create-users.md)

[Máte problémy? Dejte nám prosím jistotu](https://github.com/MicrosoftDocs/azure-docs/issues)

## <a name="create-a-c-project"></a>Vytvoření projektu C#
Na příkazovém řádku spusťte:

```
mkdir AzureMySqlExample
cd AzureMySqlExample
dotnet new console
dotnet add package MySqlConnector
```

## <a name="get-connection-information"></a>Získání informací o připojení
Získejte informace o připojení potřebné pro připojení ke službě Azure Database for MySQL. Potřebujete plně kvalifikovaný název serveru a přihlašovací údaje.

1. Přihlaste se k [Azure Portal](https://portal.azure.com/).
2. V nabídce vlevo v Azure Portal vyberte **všechny prostředky** a potom vyhledejte server, který jste vytvořili (například **mydemoserver**).
3. Vyberte název serveru.
4. Na panelu **Přehled** serveru si poznamenejte **Název serveru** a **Přihlašovací jméno správce serveru**. Pokud zapomenete své heslo, můžete ho na tomto panelu také resetovat.
 :::image type="content" source="./media/connect-csharp/server-overview-name-login.png" alt-text="Název serveru Azure Database for MySQL":::

## <a name="step-1-connect-and-insert-data"></a>Krok 1: připojení a vložení dat
Pomocí následujícího kódu se připojte a načtěte data s využitím příkazů `CREATE TABLE` a `INSERT INTO` jazyka SQL. Kód používá metody `MySqlConnection` třídy:
- [OpenAsync ()](/dotnet/api/system.data.common.dbconnection.openasync#System_Data_Common_DbConnection_OpenAsync) pro navázání připojení k MySQL.
- [CreateCommand ()](/dotnet/api/system.data.common.dbconnection.createcommand)nastaví vlastnost CommandText.
- [ExecuteNonQueryAsync ()](/dotnet/api/system.data.common.dbcommand.executenonqueryasync) pro spuštění databázových příkazů.

Parametry `Server`, `Database`, `UserID` a `Password` nahraďte hodnotami, které jste zadali při vytváření serveru a databáze.

```csharp
using System;
using System.Threading.Tasks;
using MySqlConnector;

namespace AzureMySqlExample
{
    class MySqlCreate
    {
        static async Task Main(string[] args)
        {
            var builder = new MySqlConnectionStringBuilder
            {
                Server = "YOUR-SERVER.mysql.database.azure.com",
                Database = "YOUR-DATABASE",
                UserID = "USER",
                Password = "PASSWORD",
                SslMode = MySqlSslMode.Required,
            };

            using (var conn = new MySqlConnection(builder.ConnectionString))
            {
                Console.WriteLine("Opening connection");
                await conn.OpenAsync();

                using (var command = conn.CreateCommand())
                {
                    command.CommandText = "DROP TABLE IF EXISTS inventory;";
                    await command.ExecuteNonQueryAsync();
                    Console.WriteLine("Finished dropping table (if existed)");

                    command.CommandText = "CREATE TABLE inventory (id serial PRIMARY KEY, name VARCHAR(50), quantity INTEGER);";
                    await command.ExecuteNonQueryAsync();
                    Console.WriteLine("Finished creating table");

                    command.CommandText = @"INSERT INTO inventory (name, quantity) VALUES (@name1, @quantity1),
                        (@name2, @quantity2), (@name3, @quantity3);";
                    command.Parameters.AddWithValue("@name1", "banana");
                    command.Parameters.AddWithValue("@quantity1", 150);
                    command.Parameters.AddWithValue("@name2", "orange");
                    command.Parameters.AddWithValue("@quantity2", 154);
                    command.Parameters.AddWithValue("@name3", "apple");
                    command.Parameters.AddWithValue("@quantity3", 100);

                    int rowCount = await command.ExecuteNonQueryAsync();
                    Console.WriteLine(String.Format("Number of rows inserted={0}", rowCount));
                }

                // connection will be closed by the 'using' block
                Console.WriteLine("Closing connection");
            }

            Console.WriteLine("Press RETURN to exit");
            Console.ReadLine();
        }
    }
}
```

[Máte nějaké problémy? Dejte nám prosím jistotu.](https://github.com/MicrosoftDocs/azure-docs/issues)

## <a name="step-2-read-data"></a>Krok 2: čtení dat

Pomocí následujícího kódu se připojte a načtěte data s využitím příkazu `SELECT` jazyka SQL. Kód používá `MySqlConnection` třídu s metodami:
- [OpenAsync ()](/dotnet/api/system.data.common.dbconnection.openasync#System_Data_Common_DbConnection_OpenAsync) pro navázání připojení k MySQL.
- [CreateCommand ()](/dotnet/api/system.data.common.dbconnection.createcommand) pro nastavení vlastnosti CommandText.
- [ExecuteReaderAsync ()](/dotnet/api/system.data.common.dbcommand.executereaderasync) pro spuštění databázových příkazů.
- [ReadAsync ()](/dotnet/api/system.data.common.dbdatareader.readasync#System_Data_Common_DbDataReader_ReadAsync) k přechodu na záznamy ve výsledcích. Potom tento kód použije GetInt32 a GetString k parsování hodnot v záznamu.


Parametry `Server`, `Database`, `UserID` a `Password` nahraďte hodnotami, které jste zadali při vytváření serveru a databáze.

```csharp
using System;
using System.Threading.Tasks;
using MySqlConnector;

namespace AzureMySqlExample
{
    class MySqlRead
    {
        static async Task Main(string[] args)
        {
            var builder = new MySqlConnectionStringBuilder
            {
                Server = "YOUR-SERVER.mysql.database.azure.com",
                Database = "YOUR-DATABASE",
                UserID = "USER@YOUR-SERVER",
                Password = "PASSWORD",
                SslMode = MySqlSslMode.Required,
            };

            using (var conn = new MySqlConnection(builder.ConnectionString))
            {
                Console.WriteLine("Opening connection");
                await conn.OpenAsync();

                using (var command = conn.CreateCommand())
                {
                    command.CommandText = "SELECT * FROM inventory;";

                    using (var reader = await command.ExecuteReaderAsync())
                    {
                        while (await reader.ReadAsync())
                        {
                            Console.WriteLine(string.Format(
                                "Reading from table=({0}, {1}, {2})",
                                reader.GetInt32(0),
                                reader.GetString(1),
                                reader.GetInt32(2)));
                        }
                    }
                }

                Console.WriteLine("Closing connection");
            }

            Console.WriteLine("Press RETURN to exit");
            Console.ReadLine();
        }
    }
}
```

[Máte problémy? Dejte nám prosím jistotu](https://github.com/MicrosoftDocs/azure-docs/issues)

## <a name="step-3-update-data"></a>Krok 3: aktualizace dat
Pomocí následujícího kódu se připojte a načtěte data s využitím příkazu `UPDATE` jazyka SQL. Kód používá `MySqlConnection` třídu s metodou:
- [OpenAsync ()](/dotnet/api/system.data.common.dbconnection.openasync#System_Data_Common_DbConnection_OpenAsync) pro navázání připojení k MySQL.
- [CreateCommand ()](/dotnet/api/system.data.common.dbconnection.createcommand) pro nastavení vlastnosti CommandText
- [ExecuteNonQueryAsync ()](/dotnet/api/system.data.common.dbcommand.executenonqueryasync) pro spuštění databázových příkazů.


Parametry `Server`, `Database`, `UserID` a `Password` nahraďte hodnotami, které jste zadali při vytváření serveru a databáze.

```csharp
using System;
using System.Threading.Tasks;
using MySqlConnector;

namespace AzureMySqlExample
{
    class MySqlUpdate
    {
        static async Task Main(string[] args)
        {
            var builder = new MySqlConnectionStringBuilder
            {
                Server = "YOUR-SERVER.mysql.database.azure.com",
                Database = "YOUR-DATABASE",
                UserID = "USER",
                Password = "PASSWORD",
                SslMode = MySqlSslMode.Required,
            };

            using (var conn = new MySqlConnection(builder.ConnectionString))
            {
                Console.WriteLine("Opening connection");
                await conn.OpenAsync();

                using (var command = conn.CreateCommand())
                {
                    command.CommandText = "UPDATE inventory SET quantity = @quantity WHERE name = @name;";
                    command.Parameters.AddWithValue("@quantity", 200);
                    command.Parameters.AddWithValue("@name", "banana");

                    int rowCount = await command.ExecuteNonQueryAsync();
                    Console.WriteLine(String.Format("Number of rows updated={0}", rowCount));
                }

                Console.WriteLine("Closing connection");
            }

            Console.WriteLine("Press RETURN to exit");
            Console.ReadLine();
        }
    }
}
```


## <a name="step-4-delete-data"></a>Krok 4: odstranění dat
Pomocí následujícího kódu se připojte a odstraňte data s využitím příkazu `DELETE` jazyka SQL.

Kód používá `MySqlConnection` třídu s metodou
- [OpenAsync ()](/dotnet/api/system.data.common.dbconnection.openasync#System_Data_Common_DbConnection_OpenAsync) pro navázání připojení k MySQL.
- [CreateCommand ()](/dotnet/api/system.data.common.dbconnection.createcommand) pro nastavení vlastnosti CommandText.
- [ExecuteNonQueryAsync ()](/dotnet/api/system.data.common.dbcommand.executenonqueryasync) pro spuštění databázových příkazů.


Parametry `Server`, `Database`, `UserID` a `Password` nahraďte hodnotami, které jste zadali při vytváření serveru a databáze.

```csharp
using System;
using System.Threading.Tasks;
using MySqlConnector;

namespace AzureMySqlExample
{
    class MySqlDelete
    {
        static async Task Main(string[] args)
        {
            var builder = new MySqlConnectionStringBuilder
            {
                Server = "YOUR-SERVER.mysql.database.azure.com",
                Database = "YOUR-DATABASE",
                UserID = "USER",
                Password = "PASSWORD",
                SslMode = MySqlSslMode.Required,
            };

            using (var conn = new MySqlConnection(builder.ConnectionString))
            {
                Console.WriteLine("Opening connection");
                await conn.OpenAsync();

                using (var command = conn.CreateCommand())
                {
                    command.CommandText = "DELETE FROM inventory WHERE name = @name;";
                    command.Parameters.AddWithValue("@name", "orange");

                    int rowCount = await command.ExecuteNonQueryAsync();
                    Console.WriteLine(String.Format("Number of rows deleted={0}", rowCount));
                }

                Console.WriteLine("Closing connection");
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


