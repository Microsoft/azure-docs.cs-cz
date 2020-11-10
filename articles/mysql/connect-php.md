---
title: 'Rychlý Start: připojení pomocí PHP-Azure Database for MySQL'
description: V tomto rychlém startu najdete vzorový kód PHP, který můžete použít k připojení a dotazování dat z databáze Azure Database for MySQL.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.custom: mvc
ms.topic: quickstart
ms.date: 10/28/2020
ms.openlocfilehash: 17d8d2638751d504c2a9a7ba90452faeb8ed36f1
ms.sourcegitcommit: 0dcafc8436a0fe3ba12cb82384d6b69c9a6b9536
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/10/2020
ms.locfileid: "94425962"
---
# <a name="quickstart-use-php-to-connect-and-query-data-in-azure-database-for-mysql"></a>Rychlý Start: použití PHP k připojení a dotazování dat v Azure Database for MySQL
Tento rychlý start ukazuje, jak se připojit ke službě Azure Database for MySQL pomocí aplikace v [PHP](https://secure.php.net/manual/intro-whatis.php). Ukazuje, jak pomocí příkazů jazyka SQL dotazovat, vkládat, aktualizovat a odstraňovat data v databázi.

## <a name="prerequisites"></a>Předpoklady
Pro tento rychlý Start budete potřebovat:

- Účet Azure s aktivním předplatným. [Vytvořte si účet zdarma](https://azure.microsoft.com/free).
- Vytvoření jednoho serveru Azure Database for MySQL pomocí [Azure Portal](./quickstart-create-mysql-server-database-using-azure-portal.md) <br/> nebo [Azure CLI](./quickstart-create-mysql-server-database-using-azure-cli.md) , pokud ho ještě nemáte.
- Na základě toho, jestli používáte veřejný nebo privátní přístup, proveďte **jednu** z následujících akcí, aby se povolilo připojení.

    |Akce| Metoda připojení|Praktičtí průvodci|
    |:--------- |:--------- |:--------- |
    | **Konfigurace pravidel brány firewall** | Veřejná | [Azure Portal](./howto-manage-firewall-using-portal.md) <br/> [Rozhraní příkazového řádku](./howto-manage-firewall-using-cli.md)|
    | **Konfigurace koncového bodu služby** | Veřejná | [Azure Portal](./howto-manage-vnet-using-portal.md) <br/> [Rozhraní příkazového řádku](./howto-manage-vnet-using-cli.md)|
    | **Konfigurace privátního odkazu** | Privátní | [Azure Portal](./howto-configure-privatelink-portal.md) <br/> [Rozhraní příkazového řádku](./howto-configure-privatelink-cli.md) |

- [Vytvoření databáze a uživatele bez role správce](/howto-create-users?tabs=single-server)
- Nainstalovat nejnovější verzi PHP pro váš operační systém
    - [PHP na macOS](https://secure.php.net/manual/install.macosx.php)
    - [PHP v systému Linux](https://secure.php.net/manual/install.unix.php)
    - [PHP ve Windows](https://secure.php.net/manual/install.windows.php)

> [!NOTE]
> Ke správě připojení a dotazování serveru v tomto rychlém startu používáme knihovnu [MySQL](https://www.php.net/manual/en/book.mysqli.php) .

## <a name="get-connection-information"></a>Získání informací o připojení
Informace o připojení k databázovému serveru můžete získat z Azure Portal pomocí následujících kroků:

1. Přihlaste se k [Azure Portal](https://portal.azure.com/).
2. Přejděte na stránku databáze Azure pro MySQL. Můžete vyhledat a vybrat **Azure Database for MySQL**.
:::image type="content" source="./media/quickstart-create-mysql-server-database-using-azure-portal/find-azure-mysql-in-portal.png" alt-text="Najít Azure Database for MySQL":::

2. Vyberte svůj server MySQL (například **mydemoserver** ).
3. Na stránce **Přehled** zkopírujte název plně kvalifikovaného serveru vedle pole **název serveru** a uživatelské jméno správce vedle **přihlašovací jméno správce serveru**. Pokud chcete zkopírovat název serveru nebo název hostitele, najeďte na něj ukazatelem myši a vyberte ikonu **kopírování** .

> [!IMPORTANT]
> - Pokud jste zapomněli heslo, můžete [heslo resetovat](./howto-create-manage-server-portal.md#update-admin-password).
> - Nahraďte parametry **hostitel, uživatelské jméno, heslo** a **db_name** vlastními hodnotami * *

## <a name="step-1-connect-to-the-server"></a>Krok 1: připojení k serveru
Protokol SSL je ve výchozím nastavení povolený. Možná budete muset stáhnout [certifikát SSL DigiCertGlobalRootG2](https://cacerts.digicert.com/DigiCertGlobalRootG2.crt.pem) pro připojení z místního prostředí. Tento kód volá:
- [mysqli_init](https://secure.php.net/manual/mysqli.init.php) k inicializaci MySQL.
- [mysqli_ssl_set](https://www.php.net/manual/en/mysqli.ssl-set.php) odkazovat na cestu k certifikátu SSL. To se vyžaduje pro vaše místní prostředí, ale nevyžaduje se pro App Service webové aplikace nebo virtuální počítače Azure.
- [mysqli_real_connect](https://secure.php.net/manual/mysqli.real-connect.php) se připojit k MySQL.
- připojení se [mysqli_close](https://secure.php.net/manual/mysqli.close.php) zavřít.


```php
$host = 'mydemoserver.mysql.database.azure.com';
$username = 'myadmin@mydemoserver';
$password = 'your_password';
$db_name = 'your_database';

//Initializes MySQLi
$conn = mysqli_init();

// If using  Azure Virtual machines or Azure Web App, 'mysqli-ssl_set()' is not required as the certificate is already installed on the machines.
mysqli_ssl_set($conn,NULL,NULL, "/var/www/html/DigiCertGlobalRootG2.crt.pem", NULL, NULL);

// Establish the connection
mysqli_real_connect($conn, 'mydemoserver.mysql.database.azure.com', 'myadmin@mydemoserver', 'yourpassword', 'quickstartdb', 3306, MYSQLI_CLIENT_SSL);

//If connection failed, show the error
if (mysqli_connect_errno($conn))
{
    die('Failed to connect to MySQL: '.mysqli_connect_error());
}
```
[Máte problémy? Dejte nám prosím jistotu](https://aka.ms/mysql-doc-feedback)

## <a name="step-2-create-a-table"></a>Krok 2: vytvoření tabulky
Pomocí následujícího kódu se připojte. Tento kód volá:
- spuštění dotazu [mysqli_query](https://secure.php.net/manual/mysqli.query.php) .
```php
// Run the create table query
if (mysqli_query($conn, '
CREATE TABLE Products (
`Id` INT NOT NULL AUTO_INCREMENT ,
`ProductName` VARCHAR(200) NOT NULL ,
`Color` VARCHAR(50) NOT NULL ,
`Price` DOUBLE NOT NULL ,
PRIMARY KEY (`Id`)
);
')) {
printf("Table created\n");
}
```

## <a name="step-3-insert-data"></a>Krok 3: vložení dat
Pomocí následujícího kódu vložte data pomocí příkazu **INSERT** jazyka SQL. Tento kód používá metody:
- Vytvoření připraveného příkazu INSERT pomocí [mysqli_prepare](https://secure.php.net/manual/mysqli.prepare.php)
- [mysqli_stmt_bind_param](https://secure.php.net/manual/mysqli-stmt.bind-param.php) vytvořit vazby parametrů pro každou hodnotu vloženého sloupce.
- [mysqli_stmt_execute](https://secure.php.net/manual/mysqli-stmt.execute.php)
- [mysqli_stmt_close](https://secure.php.net/manual/mysqli-stmt.close.php) uzavření příkazu pomocí metody


```php
//Create an Insert prepared statement and run it
$product_name = 'BrandNewProduct';
$product_color = 'Blue';
$product_price = 15.5;
if ($stmt = mysqli_prepare($conn, "INSERT INTO Products (ProductName, Color, Price) VALUES (?, ?, ?)"))
{
    mysqli_stmt_bind_param($stmt, 'ssd', $product_name, $product_color, $product_price);
    mysqli_stmt_execute($stmt);
    printf("Insert: Affected %d rows\n", mysqli_stmt_affected_rows($stmt));
    mysqli_stmt_close($stmt);
}

```

## <a name="step-4-read-data"></a>Krok 4: čtení dat
Pomocí následujícího kódu načtěte data pomocí příkazu **Select** jazyka SQL.  Kód používá metodu:
- [mysqli_query](https://secure.php.net/manual/mysqli.query.php) spustit dotaz **Select**
- [mysqli_fetch_assoc](https://secure.php.net/manual/mysqli-result.fetch-assoc.php) načtení výsledných řádků.

```php
//Run the Select query
printf("Reading data from table: \n");
$res = mysqli_query($conn, 'SELECT * FROM Products');
while ($row = mysqli_fetch_assoc($res))
 {
    var_dump($row);
 }

```


## <a name="step-5-delete-data"></a>Krok 5: odstranění dat
Pomocí následujícího kódu odstraňte řádky pomocí příkazu **Delete** jazyka SQL. Kód používá metody:
- Vytvoření připraveného příkazu Delete pomocí [mysqli_prepare](https://secure.php.net/manual/mysqli.prepare.php)
- [mysqli_stmt_bind_param](https://secure.php.net/manual/mysqli-stmt.bind-param.php) váže parametry
- [mysqli_stmt_execute](https://secure.php.net/manual/mysqli-stmt.execute.php) provede připravený příkaz DELETE.
- [mysqli_stmt_close](https://secure.php.net/manual/mysqli-stmt.close.php) uzavře příkaz.

```php
//Run the Delete statement
$product_name = 'BrandNewProduct';
if ($stmt = mysqli_prepare($conn, "DELETE FROM Products WHERE ProductName = ?")) {
mysqli_stmt_bind_param($stmt, 's', $product_name);
mysqli_stmt_execute($stmt);
printf("Delete: Affected %d rows\n", mysqli_stmt_affected_rows($stmt));
mysqli_stmt_close($stmt);
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
> [Správa serveru Azure Database for MySQL pomocí portálu](./howto-create-manage-server-portal.md)<br/>

> [!div class="nextstepaction"]
> [Správa serveru Azure Database for MySQL pomocí rozhraní příkazového řádku](./how-to-manage-single-server-cli.md)

[Nemůžete najít, co hledáte? Dejte nám prosím jistotu.](https://aka.ms/mysql-doc-feedback)
