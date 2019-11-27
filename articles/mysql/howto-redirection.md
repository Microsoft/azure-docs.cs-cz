---
title: Připojení k Azure Database for MySQL pomocí přesměrování
description: Tento článek popisuje, jak můžete nakonfigurovat aplikaci pro připojení k Azure Database for MySQL pomocí přesměrování.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 11/15/2019
ms.openlocfilehash: eed52c444bbcad60b4e629cf476c285f53c9f515
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/20/2019
ms.locfileid: "74233737"
---
# <a name="connect-to-azure-database-for-mysql-with-redirection"></a>Připojení k Azure Database for MySQL pomocí přesměrování

Toto téma vysvětluje, jak připojit aplikaci Azure Database for MySQL server s režimem přesměrování. Přesměrování se zaměřuje na snížení latence sítě mezi klientskými aplikacemi a servery MySQL tím, že umožňuje aplikacím připojení přímo k uzlům back-end serverů.

> [!IMPORTANT]
> Podpora pro přesměrování v [MYSQLND_AZURE](https://github.com/microsoft/mysqlnd_azure) php je momentálně ve verzi Preview.

## <a name="before-you-begin"></a>Než začnete
Přihlaste se na web [Azure Portal ](https://portal.azure.com). Vytvořte Azure Database for MySQL server s modulem verze 5,6, 5,7 nebo 8,0. Podrobnosti najdete v tématu [Postup vytvoření serveru Azure Database for MySQL z portálu](quickstart-create-mysql-server-database-using-azure-portal.md) nebo [Vytvoření Azure Database for MySQL serveru pomocí](quickstart-create-mysql-server-database-using-azure-cli.md)rozhraní příkazového řádku (CLI).

Přesměrování se momentálně podporuje jenom v případě, že je povolený protokol SSL. Podrobnosti o tom, jak nakonfigurovat SSL, najdete v tématu [použití protokolu SSL s Azure Database for MySQL](https://docs.microsoft.com/azure/mysql/howto-configure-ssl#step-3-enforcing-ssl-connections-in-azure). 

## <a name="php"></a>PHP

### <a name="ubuntu-linux"></a>Ubuntu Linux

#### <a name="prerequisites"></a>Požadavky 
- Verze PHP 7.2.15 + a 7.3.2 +
- HRUŠKY PHP 
- PHP – MySQL
- Server Azure Database for MySQL se zapnutým protokolem SSL

1. Nainstalujte [mysqlnd_azure](https://github.com/microsoft/mysqlnd_azure) s využitím [PECL](https://pecl.php.net/package/mysqlnd_azure).

    ```bash
    sudo pecl install mysqlnd_azure
    ```

2. Vyhledejte adresář rozšíření (`extension_dir`) spuštěním následujícího příkladu:

    ```bash
    php -i | grep "extension_dir"
    ```

3. Změňte adresáře na vrácenou složku a ujistěte se, že `mysqlnd_azure.so` umístěná v této složce. 

4. Vyhledejte složku souborů. ini spuštěním následujícího příkladu: 

    ```bash
    php -i | grep "dir for additional .ini files"
    ```

5. Umožňuje změnit adresáře na tuto vrácenou složku. 

6. Vytvořte nový soubor. ini pro `mysqlnd_azure`. Zajistěte, aby bylo abecední pořadí názvu po mysqnld, protože moduly jsou načteny podle pořadí názvů souborů INI. Pokud má například `mysqlnd`. ini název `10-mysqlnd.ini`, pojmenujte mysqlnd ini jako `20-mysqlnd-azure.ini`.

7. Do nového souboru. ini přidejte následující řádky, aby bylo možné přesměrování povolit.

    ```bash
    extension=mysqlnd_azure
    mysqlnd_azure.enabled=on
    ```

### <a name="windows"></a>Windows

#### <a name="prerequisites"></a>Požadavky 
- Verze PHP 7.2.15 + a 7.3.2 +
- PHP – MySQL
- Server Azure Database for MySQL se zapnutým protokolem SSL

1. Zjistěte, zda používáte verzi PHP x64 nebo x86 spuštěním následujícího příkazu:

    ```cmd
    php -i | findstr "Thread"
    ```

2. Stáhněte si odpovídající verzi x64 nebo x86 knihovny [mysqlnd_azure](https://github.com/microsoft/mysqlnd_azure) dll z [PECL](https://pecl.php.net/package/mysqlnd_azure) , která odpovídá vaší verzi PHP. 

3. Extrahujte soubor zip a vyhledejte knihovnu DLL s názvem `php_mysqlnd_azure.dll`.

4. Vyhledejte adresář rozšíření (`extension_dir`) spuštěním následujícího příkazu:

    ```cmd
    php -i | find "extension_dir"s
    ```

5. Zkopírujte soubor `php_mysqlnd_azure.dll` do adresáře vráceného v kroku 4. 

6. Vyhledejte složku PHP obsahující `php.ini` soubor pomocí následujícího příkazu:

    ```cmd
    php -i | find "Loaded Configuration File"
    ```

7. Upravte soubor `php.ini` a přidejte následující další řádky, aby bylo možné přesměrování povolit. 

    V části dynamické rozšíření: 
    ```cmd
    extension=mysqlnd_azure
    ```
    
    V části nastavení modulu:     
    ```cmd 
    [mysqlnd_azure]
    mysqlnd_azure.enabled=on
    ```

### <a name="confirm-redirection"></a>Potvrdit přesměrování

Můžete také potvrdit přesměrování je nakonfigurované pomocí níže uvedeného ukázkového kódu PHP. Vytvořte soubor PHP s názvem `mysqlConnect.php` a vložte následující kód. Aktualizujte název serveru, uživatelské jméno a heslo vlastními. 
 
 ```php
<?php
$host = '<yourservername>.mysql.database.azure.com';
$username = '<yourusername>@<yourservername>';
$password = '<yourpassword>';
$db_name = 'testdb';
  echo "mysqlnd_azure.enabled: ", ini_get("mysqlnd_azure.enabled") == true?"On":"Off", "\n";
  $db = mysqli_init();
  $link = mysqli_real_connect ($db, $host, $username, $password, $db_name, 3306, NULL, MYSQLI_CLIENT_SSL);
  if (!$link) {
     die ('Connect error (' . mysqli_connect_errno() . '): ' . mysqli_connect_error() . "\n");
  }
  else {
    echo $db->host_info, "\n"; //if redirection succeeds, the host_info will differ from the hostname you used used to connect
    $res = $db->query('SHOW TABLES;'); //test query with the connection
    print_r ($res);
$db->close();
  }
 ?>
 ```

## <a name="next-steps"></a>Další kroky
Další informace o připojovacích řetězcích najdete v tématu [připojovací řetězce](howto-connection-string.md).

