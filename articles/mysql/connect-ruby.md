---
title: 'Rychlý Start: připojení pomocí Ruby-Azure Database for MySQL'
description: V tomto rychlém startu najdete několik vzorových kódů Ruby, které můžete použít k připojení a dotazování dat ze služby Azure Database for MySQL.
author: savjani
ms.author: pariks
ms.service: mysql
ms.custom: mvc
ms.devlang: ruby
ms.topic: quickstart
ms.date: 5/26/2020
ms.openlocfilehash: 4eba3fabee50e0011d5a63297c726a9647dd84c0
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "97831529"
---
# <a name="quickstart-use-ruby-to-connect-and-query-data-in-azure-database-for-mysql"></a>Rychlý Start: použití Ruby k připojení a dotazování dat v Azure Database for MySQL

Tento rychlý start ukazuje, jak se připojit ke službě Azure Database for MySQL pomocí aplikace v [Ruby](https://www.ruby-lang.org) a gemu [mysql2](https://rubygems.org/gems/mysql2) z platforem Windows, Ubuntu Linux a Mac. Ukazuje, jak pomocí příkazů jazyka SQL dotazovat, vkládat, aktualizovat a odstraňovat data v databázi. Toto téma předpokládá, že máte zkušenosti s vývojem pomocí Ruby a teprve začínáte pracovat se službou Azure Database for MySQL.

## <a name="prerequisites"></a>Předpoklady

Tento rychlý start jako výchozí bod využívá prostředky vytvořené v některém z těchto průvodců:
- [Vytvoření serveru Azure Database for MySQL pomocí webu Azure Portal](./quickstart-create-mysql-server-database-using-azure-portal.md)
- [Vytvoření serveru Azure Database for MySQL pomocí Azure CLI](./quickstart-create-mysql-server-database-using-azure-cli.md)

> [!IMPORTANT]
> Ujistěte se, že IP adresa, ze které se připojujete, přidala pravidla brány firewall serveru pomocí [Azure Portal](./howto-manage-firewall-using-portal.md) nebo rozhraní příkazového [řádku Azure CLI](./howto-manage-firewall-using-cli.md) .

## <a name="install-ruby"></a>Instalace Ruby
Nainstalujte na svém počítači Ruby, nástroj Gem a knihovnu MySQL2.

### <a name="windows"></a>Windows
1. Stáhněte a nainstalujte [Ruby](https://rubyinstaller.org/downloads/) verze 2.3.
2. Z nabídky Start spusťte nový příkazový řádek (cmd).
3. Změňte adresář na adresář Ruby verze 2.3. `cd c:\Ruby23-x64\bin`
4. Otestujte instalaci Ruby spuštěním příkazu `ruby -v`, který zobrazí nainstalovanou verzi.
5. Otestujte instalaci nástroje Gem spuštěním příkazu `gem -v`, který zobrazí nainstalovanou verzi.
6. Pomocí nástroje Gem sestavte modul Mysql2 pro Ruby spuštěním příkazu `gem install mysql2`.

### <a name="macos"></a>macOS
1. Nainstalujte Ruby pomocí Homebrew spuštěním příkazu `brew install ruby`. Další možnosti instalace najdete v [dokumentaci k instalaci](https://www.ruby-lang.org/en/documentation/installation/#homebrew)Ruby.
2. Otestujte instalaci Ruby spuštěním příkazu `ruby -v`, který zobrazí nainstalovanou verzi.
3. Otestujte instalaci nástroje Gem spuštěním příkazu `gem -v`, který zobrazí nainstalovanou verzi.
4. Pomocí nástroje Gem sestavte modul Mysql2 pro Ruby spuštěním příkazu `gem install mysql2`.

### <a name="linux-ubuntu"></a>Linux (Ubuntu)
1. Nainstalujte Ruby spuštěním příkazu `sudo apt-get install ruby-full`. Další možnosti instalace najdete v [dokumentaci k instalaci](https://www.ruby-lang.org/en/documentation/installation/)Ruby.
2. Otestujte instalaci Ruby spuštěním příkazu `ruby -v`, který zobrazí nainstalovanou verzi.
3. Nainstalujte nejnovější aktualizace pro nástroj Gem spuštěním příkazu `sudo gem update --system`.
4. Otestujte instalaci nástroje Gem spuštěním příkazu `gem -v`, který zobrazí nainstalovanou verzi.
5. Nainstalujte gcc, make a další nástroje sestavení spuštěním příkazu `sudo apt-get install build-essential`.
6. Nainstalujte klientské knihovny MySQL pro vývojáře spuštěním příkazu `sudo apt-get install libmysqlclient-dev`.
7. Pomocí nástroje Gem sestavte modul mysql2 pro Ruby spuštěním příkazu `sudo gem install mysql2`.

## <a name="get-connection-information"></a>Získání informací o připojení
Získejte informace o připojení potřebné pro připojení ke službě Azure Database for MySQL. Potřebujete plně kvalifikovaný název serveru a přihlašovací údaje.

1. Přihlaste se k [Azure Portal](https://portal.azure.com/).
2. V nabídce vlevo na webu Azure Portal klikněte na **Všechny prostředky** a vyhledejte vytvořený server (například **mydemoserver**).
3. Klikněte na název serveru.
4. Na panelu **Přehled** serveru si poznamenejte **Název serveru** a **Přihlašovací jméno správce serveru**. Pokud zapomenete své heslo, můžete ho na tomto panelu také resetovat.
 :::image type="content" source="./media/connect-ruby/1_server-overview-name-login.png" alt-text="Název serveru Azure Database for MySQL":::

## <a name="run-ruby-code"></a>Spuštění kódu Ruby
1. Kód Ruby z dalších částí vložte do textových souborů a tyto soubory uložte s příponou .rb do složky projektu (například `C:\rubymysql\createtable.rb` nebo `/home/username/rubymysql/createtable.rb`).
2. Pokud chcete kód spustit, spusťte příkazový řádek nebo prostředí Bash. Změňte adresář na složku vašeho projektu pomocí příkazu `cd rubymysql`.
3. Potom zadejte příkaz Ruby následovaný názvem souboru, například `ruby createtable.rb`, a spusťte aplikaci.
4. Pokud v operačním systému Windows není aplikace v Ruby ve vaší proměnné prostředí PATH, možná bude nutné ke spuštění aplikace v Ruby použít úplnou cestu, například `"c:\Ruby23-x64\bin\ruby.exe" createtable.rb`.

## <a name="connect-and-create-a-table"></a>Připojení a vytvoření tabulky
Pomocí následujícího kódu se připojte a vytvořte tabulku s využitím příkazu **CREATE TABLE** jazyka SQL, po kterém následují příkazy **INSERT INTO** jazyka SQL, které do tabulky přidají řádky.

Kód používá třídu mysql2:: Client pro připojení k serveru MySQL. Poté volá metodu ```query()``` pro spuštění příkazů drop, CREATE TABLE a INSERT INTO. Nakonec zavolejte ```close()``` k ukončení připojení před ukončením.

Nahraďte řetězce `host`, `database`, `username` a `password` vlastními hodnotami.
```ruby
require 'mysql2'

begin
    # Initialize connection variables.
    host = String('mydemoserver.mysql.database.azure.com')
    database = String('quickstartdb')
    username = String('myadmin@mydemoserver')
    password = String('yourpassword')

    # Initialize connection object.
    client = Mysql2::Client.new(:host => host, :username => username, :database => database, :password => password)
    puts 'Successfully created connection to database.'

    # Drop previous table of same name if one exists
    client.query('DROP TABLE IF EXISTS inventory;')
    puts 'Finished dropping table (if existed).'

    # Drop previous table of same name if one exists.
    client.query('CREATE TABLE inventory (id serial PRIMARY KEY, name VARCHAR(50), quantity INTEGER);')
    puts 'Finished creating table.'

    # Insert some data into table.
    client.query("INSERT INTO inventory VALUES(1, 'banana', 150)")
    client.query("INSERT INTO inventory VALUES(2, 'orange', 154)")
    client.query("INSERT INTO inventory VALUES(3, 'apple', 100)")
    puts 'Inserted 3 rows of data.'

# Error handling
rescue Exception => e
    puts e.message

# Cleanup
ensure
    client.close if client
    puts 'Done.'
end
```

## <a name="read-data"></a>Čtení dat
Pomocí následujícího kódu se připojte a načtěte data s využitím příkazu **SELECT** jazyka SQL.

Kód používá třídu mysql2:: Client pro připojení k Azure Database for MySQL s ```new()``` metodou. Pak volá metodu ```query()``` pro spuštění příkazů SELECT. Poté volá metodu ```close()``` pro uzavření připojení před ukončením.

Nahraďte řetězce `host`, `database`, `username` a `password` vlastními hodnotami.

```ruby
require 'mysql2'

begin
    # Initialize connection variables.
    host = String('mydemoserver.mysql.database.azure.com')
    database = String('quickstartdb')
    username = String('myadmin@mydemoserver')
    password = String('yourpassword')

    # Initialize connection object.
    client = Mysql2::Client.new(:host => host, :username => username, :database => database, :password => password)
    puts 'Successfully created connection to database.'

    # Read data
    resultSet = client.query('SELECT * from inventory;')
    resultSet.each do |row|
        puts 'Data row = (%s, %s, %s)' % [row['id'], row['name'], row['quantity']]
    end
    puts 'Read ' + resultSet.count.to_s + ' row(s).'

# Error handling
rescue Exception => e
    puts e.message

# Cleanup
ensure
    client.close if client
    puts 'Done.'
end
```

## <a name="update-data"></a>Aktualizace dat
Pomocí následujícího kódu se připojte a aktualizujte data s využitím příkazu **UPDATE** jazyka SQL.

Kód pro připojení ke službě Azure Database for MySQL používá metodu .new() třídy [mysql2::client](https://rubygems.org/gems/mysql2-client-general_log). Pak volá metodu ```query()```  pro spuštění příkazů Update. Poté volá metodu ```close()``` pro uzavření připojení před ukončením.

Nahraďte řetězce `host`, `database`, `username` a `password` vlastními hodnotami.

```ruby
require 'mysql2'

begin
    # Initialize connection variables.
    host = String('mydemoserver.mysql.database.azure.com')
    database = String('quickstartdb')
    username = String('myadmin@mydemoserver')
    password = String('yourpassword')

    # Initialize connection object.
    client = Mysql2::Client.new(:host => host, :username => username, :database => database, :password => password)
    puts 'Successfully created connection to database.'

    # Update data
   client.query('UPDATE inventory SET quantity = %d WHERE name = %s;' % [200, '\'banana\''])
   puts 'Updated 1 row of data.'

# Error handling
rescue Exception => e
    puts e.message

# Cleanup
ensure
    client.close if client
    puts 'Done.'
end
```


## <a name="delete-data"></a>Odstranění dat
Pomocí následujícího kódu se připojte a načtěte data s využitím příkazu **DELETE** jazyka SQL.

Kód používá třídu [mysql2:: Client](https://rubygems.org/gems/mysql2/) pro připojení k serveru MySQL, spusťte příkaz DELETE a potom připojení k serveru ukončete.

Nahraďte řetězce `host`, `database`, `username` a `password` vlastními hodnotami.

```ruby
require 'mysql2'

begin
    # Initialize connection variables.
    host = String('mydemoserver.mysql.database.azure.com')
    database = String('quickstartdb')
    username = String('myadmin@mydemoserver')
    password = String('yourpassword')

    # Initialize connection object.
    client = Mysql2::Client.new(:host => host, :username => username, :database => database, :password => password)
    puts 'Successfully created connection to database.'

    # Delete data
    resultSet = client.query('DELETE FROM inventory WHERE name = %s;' % ['\'orange\''])
    puts 'Deleted 1 row.'

# Error handling
rescue Exception => e
    puts e.message

# Cleanup
ensure
    client.close if client
    puts 'Done.'
end
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
> [Migrace vaší databáze pomocí exportu a importu](./concepts-migrate-import-export.md) <br/>

> [!div class="nextstepaction"]
> [Další informace o klientovi MySQL2](https://rubygems.org/gems/mysql2-client-general_log) <br/>

