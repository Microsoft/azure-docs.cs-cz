---
title: Připojení k Azure Database for PostgreSQL pomocí Ruby
description: V tomto rychlém startu najdete vzorový kód Ruby, který můžete použít k připojení a dotazování dat ze služby Azure Database for PostgreSQL.
services: postgresql
author: rachel-msft
ms.author: raagyema
manager: kfile
editor: jasonwhowell
ms.service: postgresql
ms.custom: mvc
ms.devlang: ruby
ms.topic: quickstart
ms.date: 02/28/2018
ms.openlocfilehash: 911dcd49273edb202c64d046424418b7db048291
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/28/2018
ms.locfileid: "29692415"
---
# <a name="azure-database-for-postgresql-use-ruby-to-connect-and-query-data"></a>Azure Database for PostgreSQL: Použití Ruby k připojení a dotazování dat
Tento rychlý start ukazuje, jak se připojit ke službě Azure Database for PostgreSQL pomocí aplikace v [Ruby](https://www.ruby-lang.org). Ukazuje, jak pomocí příkazů jazyka SQL dotazovat, vkládat, aktualizovat a odstraňovat data v databázi. Kroky v tomto článku předpokládají, že máte zkušenosti s vývojem pomocí Ruby a teprve začínáte pracovat se službou Azure Database for PostgreSQL.

## <a name="prerequisites"></a>Požadavky
Tento rychlý start využívá jako výchozí bod prostředky vytvořené v některém z těchto průvodců:
- [Vytvoření databáze – portál](quickstart-create-server-database-portal.md)
- [Vytvoření databáze – rozhraní příkazového řádku Azure](quickstart-create-server-database-azure-cli.md)

## <a name="install-ruby"></a>Instalace Ruby
Nainstalujte Ruby na vlastní počítač. 

### <a name="windows"></a>Windows
- Stáhněte a nainstalujte nejnovější verzi [Ruby](http://rubyinstaller.org/downloads/).
- Na poslední obrazovce instalačního programu MSI zaškrtněte políčko s textem „Run 'ridk install' to install MSYS2 and development toolchain“ (Spustit ridk install pro instalaci MSYS2 a vývojářské sady nástrojů). Potom kliknutím na **Finish** (Dokončit) spusťte další instalační program.
- Spustí se instalační program RubyInstaller2 pro Windows. Zadejte 2 pro instalaci aktualizace úložiště MSYS2. Po dokončení a navrácení na příkazový řádek instalace zavřete příkazové okno.
- Z nabídky Start spusťte nový příkazový řádek (cmd).
- Příkazem `ruby -v` otestujte instalaci Ruby a zobrazte nainstalovanou verzi.
- Příkazem `gem -v` otestujte instalaci nástroje Gem a zobrazte nainstalovanou verzi.
- Pomocí nástroje Gem sestavte modul PostgreSQL pro Ruby spuštěním příkazu `gem install pg`.

### <a name="macos"></a>MacOS
- Nainstalujte Ruby pomocí Homebrew spuštěním příkazu `brew install ruby`. Další možnosti instalace najdete v [dokumentaci k instalaci](https://www.ruby-lang.org/en/documentation/installation/#homebrew) Ruby.
- Příkazem `ruby -v` otestujte instalaci Ruby a zobrazte nainstalovanou verzi.
- Příkazem `gem -v` otestujte instalaci nástroje Gem a zobrazte nainstalovanou verzi.
- Pomocí nástroje Gem sestavte modul PostgreSQL pro Ruby spuštěním příkazu `gem install pg`.

### <a name="linux-ubuntu"></a>Linux (Ubuntu)
- Nainstalujte Ruby spuštěním příkazu `sudo apt-get install ruby-full`. Další možnosti instalace najdete v [dokumentaci k instalaci](https://www.ruby-lang.org/en/documentation/installation/) Ruby.
- Příkazem `ruby -v` otestujte instalaci Ruby a zobrazte nainstalovanou verzi.
- Nainstalujte nejnovější aktualizace pro nástroj Gem spuštěním příkazu `sudo gem update --system`.
- Příkazem `gem -v` otestujte instalaci nástroje Gem a zobrazte nainstalovanou verzi.
- Nainstalujte gcc, make a další nástroje sestavení spuštěním příkazu `sudo apt-get install build-essential`.
- Nainstalujte knihovny PostgreSQL spuštěním příkazu `sudo apt-get install libpq-dev`.
- Pomocí nástroje Gem sestavte modul pg pro Ruby spuštěním příkazu `sudo gem install pg`.

## <a name="run-ruby-code"></a>Spuštění kódu Ruby 
- Uložte kód do textového souboru s příponou .rb a soubor uložte do složky projektu, například `C:\rubypostgres\read.rb` nebo `/home/username/rubypostgres/read.rb`.
- Pokud chcete kód spustit, spusťte příkazový řádek nebo prostředí Bash. Pokud chcete aplikaci spustit, změňte adresář na složku projektu příkazem `cd rubypostgres` a pak zadejte příkaz `ruby read.rb`.

## <a name="get-connection-information"></a>Získání informací o připojení
Získejte informace o připojení potřebné pro připojení ke službě Azure Database for PostgreSQL. Potřebujete plně kvalifikovaný název serveru a přihlašovací údaje.

1. Přihlaste se k portálu [Azure Portal](https://portal.azure.com/).
2. V nabídce vlevo na webu Azure Portal klikněte na **Všechny prostředky** a vyhledejte vytvořený server (například **mydemoserver**).
3. Klikněte na název serveru.
4. Na panelu **Přehled** serveru si poznamenejte **Název serveru** a **Přihlašovací jméno správce serveru**. Pokud zapomenete své heslo, můžete ho na tomto panelu také resetovat.
 ![Název serveru Azure Database for PostgreSQL](./media/connect-ruby/1-connection-string.png)

## <a name="connect-and-create-a-table"></a>Připojení a vytvoření tabulky
Pomocí následujícího kódu se připojte a vytvořte tabulku s využitím příkazu **CREATE TABLE** jazyka SQL, po kterém následují příkazy **INSERT INTO** jazyka SQL, které do tabulky přidají řádky.

Kód pro připojení ke službě Azure Database for PostgreSQL používá objekt [PG::Connection](http://www.rubydoc.info/gems/pg/PG/Connection) s konstruktorem [new()](http://www.rubydoc.info/gems/pg/PG%2FConnection:initialize). Potom volá metodu [exec()](http://www.rubydoc.info/gems/pg/PG/Connection#exec-instance_method) pro spuštění příkazů DROP, CREATE TABLE a INSERT INTO. Kód kontroluje chyby pomocí třídy [PG::Error](http://www.rubydoc.info/gems/pg/PG/Error). Před ukončením potom volá metodu [close()](http://www.rubydoc.info/gems/pg/PG/Connection#lo_close-instance_method) pro ukončení připojení.

Nahraďte řetězce `host`, `database`, `user` a `password` vlastními hodnotami. 
```ruby
require 'pg'

begin
    # Initialize connection variables.
    host = String('mydemoserver.postgres.database.azure.com')
    database = String('postgres')
    user = String('mylogin@mydemoserver')
    password = String('<server_admin_password>')

    # Initialize connection object.
    connection = PG::Connection.new(:host => host, :user => user, :dbname => database, :port => '5432', :password => password)
    puts 'Successfully created connection to database'

    # Drop previous table of same name if one exists
    connection.exec('DROP TABLE IF EXISTS inventory;')
    puts 'Finished dropping table (if existed).'

    # Drop previous table of same name if one exists.
    connection.exec('CREATE TABLE inventory (id serial PRIMARY KEY, name VARCHAR(50), quantity INTEGER);')
    puts 'Finished creating table.'

    # Insert some data into table.
    connection.exec("INSERT INTO inventory VALUES(1, 'banana', 150)")
    connection.exec("INSERT INTO inventory VALUES(2, 'orange', 154)")
    connection.exec("INSERT INTO inventory VALUES(3, 'apple', 100)")
    puts 'Inserted 3 rows of data.'

rescue PG::Error => e
    puts e.message 
    
ensure
    connection.close if connection
end
```

## <a name="read-data"></a>Čtení dat
Pomocí následujícího kódu se připojte a načtěte data s využitím příkazu **SELECT** jazyka SQL. 

Kód pro připojení ke službě Azure Database for PostgreSQL používá objekt [PG::Connection](http://www.rubydoc.info/gems/pg/PG/Connection) s konstruktorem [new()](http://www.rubydoc.info/gems/pg/PG%2FConnection:initialize). Potom volá metodu [exec()](http://www.rubydoc.info/gems/pg/PG/Connection#exec-instance_method) pro spuštění příkazu SELECT a výsledky uloží do sady výsledků dotazu. Nad kolekcí sady výsledků dotazu se iteruje pomocí smyčky `resultSet.each do` a hodnoty aktuálního řádku se ukládají do proměnné `row`. Kód kontroluje chyby pomocí třídy [PG::Error](http://www.rubydoc.info/gems/pg/PG/Error). Před ukončením potom volá metodu [close()](http://www.rubydoc.info/gems/pg/PG/Connection#lo_close-instance_method) pro ukončení připojení.

Nahraďte řetězce `host`, `database`, `user` a `password` vlastními hodnotami. 

```ruby
require 'pg'

begin
    # Initialize connection variables.
    host = String('mydemoserver.postgres.database.azure.com')
    database = String('postgres')
    user = String('mylogin@mydemoserver')
    password = String('<server_admin_password>')

    # Initialize connection object.
    connection = PG::Connection.new(:host => host, :user => user, :database => dbname, :port => '5432', :password => password)
    puts 'Successfully created connection to database.'

    resultSet = connection.exec('SELECT * from inventory;')
    resultSet.each do |row|
        puts 'Data row = (%s, %s, %s)' % [row['id'], row['name'], row['quantity']]
    end

rescue PG::Error => e
    puts e.message 
    
ensure
    connection.close if connection
end
```

## <a name="update-data"></a>Aktualizace dat
Pomocí následujícího kódu se připojte a aktualizujte data s využitím příkazu **UPDATE** jazyka SQL.

Kód pro připojení ke službě Azure Database for PostgreSQL používá objekt [PG::Connection](http://www.rubydoc.info/gems/pg/PG/Connection) s konstruktorem [new()](http://www.rubydoc.info/gems/pg/PG%2FConnection:initialize). Potom volá metodu [exec()](http://www.rubydoc.info/gems/pg/PG/Connection#exec-instance_method) pro spuštění příkazu UPDATE. Kód kontroluje chyby pomocí třídy [PG::Error](http://www.rubydoc.info/gems/pg/PG/Error). Před ukončením potom volá metodu [close()](http://www.rubydoc.info/gems/pg/PG/Connection#lo_close-instance_method) pro ukončení připojení.

Nahraďte řetězce `host`, `database`, `user` a `password` vlastními hodnotami. 

```ruby
require 'pg'

begin
    # Initialize connection variables.
    host = String('mydemoserver.postgres.database.azure.com')
    database = String('postgres')
    user = String('mylogin@mydemoserver')
    password = String('<server_admin_password>')

    # Initialize connection object.
    connection = PG::Connection.new(:host => host, :user => user, :dbname => database, :port => '5432', :password => password)
    puts 'Successfully created connection to database.'

    # Modify some data in table.
    connection.exec('UPDATE inventory SET quantity = %d WHERE name = %s;' % [200, '\'banana\''])
    puts 'Updated 1 row of data.'

rescue PG::Error => e
    puts e.message 
    
ensure
    connection.close if connection
end
```


## <a name="delete-data"></a>Odstranění dat
Pomocí následujícího kódu se připojte a načtěte data s využitím příkazu **DELETE** jazyka SQL. 

Kód pro připojení ke službě Azure Database for PostgreSQL používá objekt [PG::Connection](http://www.rubydoc.info/gems/pg/PG/Connection) s konstruktorem [new()](http://www.rubydoc.info/gems/pg/PG%2FConnection:initialize). Potom volá metodu [exec()](http://www.rubydoc.info/gems/pg/PG/Connection#exec-instance_method) pro spuštění příkazu UPDATE. Kód kontroluje chyby pomocí třídy [PG::Error](http://www.rubydoc.info/gems/pg/PG/Error). Před ukončením potom volá metodu [close()](http://www.rubydoc.info/gems/pg/PG/Connection#lo_close-instance_method) pro ukončení připojení.

Nahraďte řetězce `host`, `database`, `user` a `password` vlastními hodnotami. 

```ruby
require 'pg'

begin
    # Initialize connection variables.
    host = String('mydemoserver.postgres.database.azure.com')
    database = String('postgres')
    user = String('mylogin@mydemoserver')
    password = String('<server_admin_password>')

    # Initialize connection object.
    connection = PG::Connection.new(:host => host, :user => user, :dbname => database, :port => '5432', :password => password)
    puts 'Successfully created connection to database.'

    # Modify some data in table.
    connection.exec('DELETE FROM inventory WHERE name = %s;' % ['\'orange\''])
    puts 'Deleted 1 row of data.'

rescue PG::Error => e
    puts e.message 
    
ensure
    connection.close if connection
end
```

## <a name="next-steps"></a>Další kroky
> [!div class="nextstepaction"]
> [Migrace vaší databáze pomocí exportu a importu](./howto-migrate-using-export-and-import.md)
