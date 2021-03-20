---
title: 'Rychlý Start: použití jazyků Java a JDBC se serverem Azure Database for MySQLFlexible'
description: Naučte se používat Java a JDBC s Azure Database for MySQL flexibilní serverovou databází.
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.custom: mvc, devcenter, devx-track-azurecli
ms.topic: quickstart
ms.devlang: java
ms.date: 01/16/2021
ms.openlocfilehash: 93a8a7644c7ca729f0e4fd201b88e995576d4bda
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "98606101"
---
# <a name="quickstart-use-java-and-jdbc-with-azure-database-for-mysql-flexible-server"></a>Rychlý Start: použití jazyků Java a JDBC s Azure Database for MySQLm flexibilním serverem

Toto téma ukazuje, jak vytvořit ukázkovou aplikaci, která používá Java a [JDBC](https://en.wikipedia.org/wiki/Java_Database_Connectivity) k ukládání a načítání informací v [Azure Database for MySQL flexibilním serveru](./index.yml).

## <a name="prerequisites"></a>Předpoklady

- Účet Azure: Pokud ho nemáte, [Získejte bezplatnou zkušební verzi](https://azure.microsoft.com/free/).
- [Azure Cloud Shell](../../cloud-shell/quickstart.md) nebo [Azure CLI](/cli/azure/install-azure-cli). Doporučujeme, abyste Azure Cloud Shell, že budete automaticky přihlášeni a budete mít přístup ke všem nástrojům, které budete potřebovat.
- Podporovaná [sada Java Development Kit](/azure/developer/java/fundamentals/java-jdk-long-term-support), verze 8 (obsažená v Azure Cloud Shell).
- Nástroj pro sestavení [Apache Maven](https://maven.apache.org/)

## <a name="prepare-the-working-environment"></a>Příprava pracovního prostředí

Proměnné prostředí se používají k omezení chyb při psaní a usnadňuje vám přizpůsobení následující konfigurace konkrétním potřebám.

Tyto proměnné prostředí nastavte pomocí následujících příkazů:

```bash
AZ_RESOURCE_GROUP=database-workshop
AZ_DATABASE_NAME= flexibleserverdb
AZ_LOCATION=<YOUR_AZURE_REGION>
AZ_MYSQL_USERNAME=demo
AZ_MYSQL_PASSWORD=<YOUR_MYSQL_PASSWORD>
AZ_LOCAL_IP_ADDRESS=<YOUR_LOCAL_IP_ADDRESS>
```

Zástupné symboly nahraďte následujícími hodnotami, které se používají v tomto článku:

- `<YOUR_DATABASE_NAME>`: Název serveru MySQL. Měl by být jedinečný v rámci Azure.
- `<YOUR_AZURE_REGION>`: Oblast Azure, kterou budete používat. Standardně můžete použít `eastus`, ale doporučujeme nakonfigurovat oblast blíže k místu, kde se nacházíte. Můžete mít úplný seznam oblastí, které jsou k dispozici, zadáním `az account list-locations` .
- `<YOUR_MYSQL_PASSWORD>`: Heslo serveru databáze MySQL. Toto heslo by mělo mít minimálně osm znaků. Znaky by měly pocházet z následujících tří kategorií: Velká písmena anglické abecedy, malá písmena anglické abecedy, číslice (0–9) a jiné než alfanumerické znaky (!, $, #, % atd.).
- `<YOUR_LOCAL_IP_ADDRESS>`: IP adresa místního počítače, ze kterého spouštíte aplikaci Java. Jedním pohodlným způsobem, jak ho najít, je ukázat svůj prohlížeč na [whatismyip.Akamai.com](http://whatismyip.akamai.com/).

Následně vytvořte skupinu prostředků:

```azurecli
az group create \
    --name $AZ_RESOURCE_GROUP \
    --location $AZ_LOCATION \
  	| jq
```

> [!NOTE]
> Používáme nástroj `jq`, který je standardně nainstalován v [Azure Cloud Shellu](https://shell.azure.com/) a umožňuje zobrazit data JSON v čitelnějším formátu.
> Pokud tento nástroj nepoužíváte, můžete ve všech příkazech, které budeme používat, klidně odebrat část `| jq`.

## <a name="create-an-azure-database-for-mysql-instance"></a>Vytvoření instance Azure Database for MySQL

Jako první vytvoříme spravovaný server MySQL.

> [!NOTE]
> Podrobnější informace o vytváření serverů MySQL najdete v článku [Vytvoření serveru Azure Database for MySQL pomocí webu Azure Portal](./quickstart-create-server-portal.md).

V [Azure Cloud Shell](https://shell.azure.com/)spusťte následující skript:

```azurecli
az mysql flexible-server create \
    --resource-group $AZ_RESOURCE_GROUP \
    --name $AZ_DATABASE_NAME \
    --location $AZ_LOCATION \
    --sku-name Standard_B1ms \
    --storage-size 5120 \
    --admin-user $AZ_MYSQL_USERNAME \
    --admin-password $AZ_MYSQL_PASSWORD \
    --public-access $AZ_LOCAL_IP_ADDRESS
  	| jq
```

Abyste měli přístup k serveru z místního počítače, ujistěte se, že jste zadali <> IP adres. Tento příkaz vytvoří elastický server vrstvy MySQL, který je vhodný pro vývoj.

Server MySQL, který jste vytvořili, má prázdnou databázi s názvem **flexibleserverdb**. Tuto databázi použijeme pro tento článek.

[Máte nějaké problémy? Dejte nám prosím jistotu.](https://github.com/MicrosoftDocs/azure-docs/issues)

### <a name="create-a-new-java-project"></a>Vytvořit nový projekt Java

Pomocí svého oblíbeného integrovaného vývojového prostředí (IDE) vytvořte nový projekt Java a přidejte `pom.xml` ho do svého kořenového adresáře:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <groupId>com.example</groupId>
    <artifactId>demo</artifactId>
    <version>0.0.1-SNAPSHOT</version>
    <name>demo</name>

    <properties>
        <java.version>1.8</java.version>
        <maven.compiler.source>1.8</maven.compiler.source>
        <maven.compiler.target>1.8</maven.compiler.target>
    </properties>

    <dependencies>
        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
            <version>8.0.20</version>
        </dependency>
    </dependencies>
</project>
```

Tento soubor je [Apache Maven](https://maven.apache.org/) , který nakonfiguruje náš projekt na použití:

- Java 8
- Poslední ovladač MySQL pro Java

### <a name="prepare-a-configuration-file-to-connect-to-azure-database-for-mysql"></a>Příprava konfiguračního souboru pro připojení k Azure Database for MySQL

Vytvořte soubor *Src/Main/Resources/Application. Properties* a přidejte:

```properties
url=jdbc:mysql://$AZ_DATABASE_NAME.mysql.database.azure.com:3306/demo?serverTimezone=UTC
user=demo
password=$AZ_MYSQL_PASSWORD
```

- Nahraďte dvě `$AZ_DATABASE_NAME` proměnné hodnotou, kterou jste nakonfigurovali na začátku tohoto článku.
- Nahraďte `$AZ_MYSQL_PASSWORD` proměnnou hodnotou, kterou jste nakonfigurovali na začátku tohoto článku.

> [!NOTE]
> Tím, že ke konfigurační vlastnosti `url` přidáme `?serverTimezone=UTC`, ovladači JDBC sdělíme, aby při připojování k databázi používal datum ve formátu UTC (koordinovaný univerzální čas). V opačném případě by server Java nepoužíval stejný datumový formát jako databáze, což by způsobilo chybu.

### <a name="create-an-sql-file-to-generate-the-database-schema"></a>Vytvoření souboru SQL pro generování schématu databáze

Pro vytvoření schématu databáze použijeme *Src/Main/ `schema.sql` Resources/* File. Vytvořte tento soubor s následujícím obsahem:

```sql
DROP TABLE IF EXISTS todo;
CREATE TABLE todo (id SERIAL PRIMARY KEY, description VARCHAR(255), details VARCHAR(4096), done BOOLEAN);
```

## <a name="code-the-application"></a>Vytvoření kódu aplikace

### <a name="connect-to-the-database"></a>Připojte se k databázi.

Dále přidejte kód Java, který bude používat JDBC k ukládání a načítání dat ze serveru MySQL.

Vytvořte soubor *Src/Main/Java/DemoApplication. Java* , který obsahuje:

```java
package com.example.demo;

import com.mysql.cj.jdbc.AbandonedConnectionCleanupThread;

import java.sql.*;
import java.util.*;
import java.util.logging.Logger;

public class DemoApplication {

    private static final Logger log;

    static {
        System.setProperty("java.util.logging.SimpleFormatter.format", "[%4$-7s] %5$s %n");
        log =Logger.getLogger(DemoApplication.class.getName());
    }

    public static void main(String[] args) throws Exception {
        log.info("Loading application properties");
        Properties properties = new Properties();
        properties.load(DemoApplication.class.getClassLoader().getResourceAsStream("application.properties"));

        log.info("Connecting to the database");
        Connection connection = DriverManager.getConnection(properties.getProperty("url"), properties);
        log.info("Database connection test: " + connection.getCatalog());

        log.info("Create database schema");
        Scanner scanner = new Scanner(DemoApplication.class.getClassLoader().getResourceAsStream("schema.sql"));
        Statement statement = connection.createStatement();
        while (scanner.hasNextLine()) {
            statement.execute(scanner.nextLine());
        }

        /*
        Todo todo = new Todo(1L, "configuration", "congratulations, you have set up JDBC correctly!", true);
        insertData(todo, connection);
        todo = readData(connection);
        todo.setDetails("congratulations, you have updated data!");
        updateData(todo, connection);
        deleteData(todo, connection);
        */

        log.info("Closing database connection");
        connection.close();
        AbandonedConnectionCleanupThread.uncheckedShutdown();
    }
}
```

[Máte nějaké problémy? Dejte nám prosím jistotu.](https://github.com/MicrosoftDocs/azure-docs/issues)

Tento kód Java bude používat soubory *Application. Properties* a *Schema. SQL* , které jsme vytvořili dříve, aby se mohl připojit k serveru MySQL a vytvořit schéma, které bude ukládat naše data.

V tomto souboru vidíte, že jsme do těchto metod vložili metody pro vkládání, čtení, aktualizaci a odstraňování dat: tyto metody budeme kódovat ve zbývající části tohoto článku a budete je moct po sobě odkomentovat.

> [!NOTE]
> Pověření databáze se ukládají do vlastností *uživatel* a *heslo* souboru *Application. Properties* . Tyto přihlašovací údaje se používají při spuštění `DriverManager.getConnection(properties.getProperty("url"), properties);` , protože soubor vlastností se předává jako argument.

> [!NOTE]
> `AbandonedConnectionCleanupThread.uncheckedShutdown();`Řádek na konci je příkaz specifický pro ovladač MySQL ke zničení interního vlákna při ukončování aplikace.
> Můžete ho bezpečně ignorovat.

Tuto hlavní třídu teď můžete spustit pomocí vašeho oblíbeného nástroje:

- Pomocí prostředí IDE byste měli být schopni kliknout pravým tlačítkem na třídu *DemoApplication* a provést ji.
- Pomocí Maven můžete spustit aplikaci spuštěním příkazu: `mvn exec:java -Dexec.mainClass="com.example.demo.DemoApplication"` .

Aplikace by se měla připojit k Azure Database for MySQL, vytvořit schéma databáze a pak připojení zavřít, jak by se mělo zobrazit v protokolech konzoly:

```
[INFO   ] Loading application properties
[INFO   ] Connecting to the database
[INFO   ] Database connection test: demo
[INFO   ] Create database schema
[INFO   ] Closing database connection
```

### <a name="create-a-domain-class"></a>Vytvoření doménové třídy

Vytvořte novou `Todo` třídu Java vedle `DemoApplication` třídy a přidejte následující kód:

```java
package com.example.demo;

public class Todo {

    private Long id;
    private String description;
    private String details;
    private boolean done;

    public Todo() {
    }

    public Todo(Long id, String description, String details, boolean done) {
        this.id = id;
        this.description = description;
        this.details = details;
        this.done = done;
    }

    public Long getId() {
        return id;
    }

    public void setId(Long id) {
        this.id = id;
    }

    public String getDescription() {
        return description;
    }

    public void setDescription(String description) {
        this.description = description;
    }

    public String getDetails() {
        return details;
    }

    public void setDetails(String details) {
        this.details = details;
    }

    public boolean isDone() {
        return done;
    }

    public void setDone(boolean done) {
        this.done = done;
    }

    @Override
    public String toString() {
        return "Todo{" +
                "id=" + id +
                ", description='" + description + '\'' +
                ", details='" + details + '\'' +
                ", done=" + done +
                '}';
    }
}
```

Tato třída je model domény mapovaný na `todo` tabulku, kterou jste vytvořili při provádění skriptu *Schema. SQL* .

### <a name="insert-data-into-azure-database-for-mysql"></a>Vložit data do Azure Database for MySQL

V souboru *Src/Main/Java/DemoApplication. Java* za metodou Main přidejte následující metodu, která vloží data do databáze:

```java
private static void insertData(Todo todo, Connection connection) throws SQLException {
    log.info("Insert data");
    PreparedStatement insertStatement = connection
            .prepareStatement("INSERT INTO todo (id, description, details, done) VALUES (?, ?, ?, ?);");

    insertStatement.setLong(1, todo.getId());
    insertStatement.setString(2, todo.getDescription());
    insertStatement.setString(3, todo.getDetails());
    insertStatement.setBoolean(4, todo.isDone());
    insertStatement.executeUpdate();
}
```

Nyní můžete odkomentovat tyto dva řádky v `main` metodě:

```java
Todo todo = new Todo(1L, "configuration", "congratulations, you have set up JDBC correctly!", true);
insertData(todo, connection);
```

Spuštění hlavní třídy by nyní mělo mít následující výstup:

```
[INFO   ] Loading application properties
[INFO   ] Connecting to the database
[INFO   ] Database connection test: demo
[INFO   ] Create database schema
[INFO   ] Insert data
[INFO   ] Closing database connection
```

### <a name="reading-data-from-azure-database-for-mysql"></a>Čtení dat z Azure Database for MySQL

Pojďme číst dřív vložená data a ověřit tak, že náš kód funguje správně.

V souboru *Src/Main/Java/DemoApplication. Java* za `insertData` metodou přidejte následující metodu pro čtení dat z databáze:

```java
private static Todo readData(Connection connection) throws SQLException {
    log.info("Read data");
    PreparedStatement readStatement = connection.prepareStatement("SELECT * FROM todo;");
    ResultSet resultSet = readStatement.executeQuery();
    if (!resultSet.next()) {
        log.info("There is no data in the database!");
        return null;
    }
    Todo todo = new Todo();
    todo.setId(resultSet.getLong("id"));
    todo.setDescription(resultSet.getString("description"));
    todo.setDetails(resultSet.getString("details"));
    todo.setDone(resultSet.getBoolean("done"));
    log.info("Data read from the database: " + todo.toString());
    return todo;
}
```

Nyní můžete v metodě zrušit komentář k následujícímu řádku `main` :

```java
todo = readData(connection);
```

Spuštění hlavní třídy by nyní mělo mít následující výstup:

```
[INFO   ] Loading application properties
[INFO   ] Connecting to the database
[INFO   ] Database connection test: demo
[INFO   ] Create database schema
[INFO   ] Insert data
[INFO   ] Read data
[INFO   ] Data read from the database: Todo{id=1, description='configuration', details='congratulations, you have set up JDBC correctly!', done=true}
[INFO   ] Closing database connection
```

[Máte nějaké problémy? Dejte nám prosím jistotu.](https://github.com/MicrosoftDocs/azure-docs/issues)

### <a name="updating-data-in-azure-database-for-mysql"></a>Aktualizace dat v Azure Database for MySQL

Pojďme aktualizovat data, která jsme předtím vložili.

Ještě v souboru *Src/Main/Java/DemoApplication. Java* , a to za `readData` metodu, přidejte následující metodu pro aktualizaci dat v databázi:

```java
private static void updateData(Todo todo, Connection connection) throws SQLException {
    log.info("Update data");
    PreparedStatement updateStatement = connection
            .prepareStatement("UPDATE todo SET description = ?, details = ?, done = ? WHERE id = ?;");

    updateStatement.setString(1, todo.getDescription());
    updateStatement.setString(2, todo.getDetails());
    updateStatement.setBoolean(3, todo.isDone());
    updateStatement.setLong(4, todo.getId());
    updateStatement.executeUpdate();
    readData(connection);
}
```

Nyní můžete odkomentovat tyto dva řádky v `main` metodě:

```java
todo.setDetails("congratulations, you have updated data!");
updateData(todo, connection);
```

Spuštění hlavní třídy by nyní mělo mít následující výstup:

```
[INFO   ] Loading application properties
[INFO   ] Connecting to the database
[INFO   ] Database connection test: demo
[INFO   ] Create database schema
[INFO   ] Insert data
[INFO   ] Read data
[INFO   ] Data read from the database: Todo{id=1, description='configuration', details='congratulations, you have set up JDBC correctly!', done=true}
[INFO   ] Update data
[INFO   ] Read data
[INFO   ] Data read from the database: Todo{id=1, description='configuration', details='congratulations, you have updated data!', done=true}
[INFO   ] Closing database connection
```

### <a name="deleting-data-in-azure-database-for-mysql"></a>Odstraňování dat v Azure Database for MySQL

Nakonec odstraníme data, která jsme předtím vložili.

Ještě v souboru *Src/Main/Java/DemoApplication. Java* za `updateData` metodou přidejte následující metodu, která odstraní data v databázi:

```java
private static void deleteData(Todo todo, Connection connection) throws SQLException {
    log.info("Delete data");
    PreparedStatement deleteStatement = connection.prepareStatement("DELETE FROM todo WHERE id = ?;");
    deleteStatement.setLong(1, todo.getId());
    deleteStatement.executeUpdate();
    readData(connection);
}
```

Nyní můžete v metodě zrušit komentář k následujícímu řádku `main` :

```java
deleteData(todo, connection);
```

Spuštění hlavní třídy by nyní mělo mít následující výstup:

```
[INFO   ] Loading application properties
[INFO   ] Connecting to the database
[INFO   ] Database connection test: demo
[INFO   ] Create database schema
[INFO   ] Insert data
[INFO   ] Read data
[INFO   ] Data read from the database: Todo{id=1, description='configuration', details='congratulations, you have set up JDBC correctly!', done=true}
[INFO   ] Update data
[INFO   ] Read data
[INFO   ] Data read from the database: Todo{id=1, description='configuration', details='congratulations, you have updated data!', done=true}
[INFO   ] Delete data
[INFO   ] Read data
[INFO   ] There is no data in the database!
[INFO   ] Closing database connection
```

## <a name="clean-up-resources"></a>Vyčištění prostředků

Gratulujeme! Vytvořili jste aplikaci Java, která používá JDBC k ukládání a načítání dat z Azure Database for MySQL.

Pokud chcete vyčistit všechny prostředky používané v rámci tohoto rychlého startu, odstraňte skupinu prostředků pomocí následujícího příkazu:

```azurecli
az group delete \
    --name $AZ_RESOURCE_GROUP \
    --yes
```

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Migrace databáze MySQL do služby Azure Database for MySQL pomocí výpisu a obnovení.](../concepts-migrate-dump-restore.md)
