---
title: Připojení pomocí přesměrování – Azure Database for MySQL
description: Tento článek popisuje, jak můžete nakonfigurovat aplikaci pro připojení k Azure Database for MySQL pomocí přesměrování.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: how-to
ms.date: 6/8/2020
ms.openlocfilehash: bea618b34809544308caba46a0ffbdecee91d69f
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/12/2020
ms.locfileid: "94541806"
---
# <a name="connect-to-azure-database-for-mysql-with-redirection"></a>Připojení ke službě Azure Database for MySQL s přesměrováním

Toto téma vysvětluje, jak připojit aplikaci Azure Database for MySQL server s režimem přesměrování. Přesměrování se zaměřuje na snížení latence sítě mezi klientskými aplikacemi a servery MySQL tím, že umožňuje aplikacím připojení přímo k uzlům back-end serverů.

## <a name="before-you-begin"></a>Než začnete
Přihlaste se na [Azure Portal](https://portal.azure.com). Vytvořte Azure Database for MySQL server s modulem verze 5,6, 5,7 nebo 8,0. 

Podrobnosti najdete v tématu Postup vytvoření Azure Database for MySQL serveru pomocí [Azure Portal](quickstart-create-mysql-server-database-using-azure-portal.md) nebo [Azure CLI](quickstart-create-mysql-server-database-using-azure-cli.md).

## <a name="enable-redirection"></a>Povolit přesměrování

Na serveru Azure Database for MySQL nakonfigurujte `redirect_enabled` parametr tak, aby `ON` povoloval připojení s režimem přesměrování. Pokud chcete tento parametr serveru aktualizovat, použijte [Azure Portal](howto-server-parameters.md) nebo [Azure CLI](howto-configure-server-parameters-using-cli.md).

## <a name="php"></a>PHP

Podpora pro přesměrování v aplikacích PHP je dostupná prostřednictvím rozšíření [mysqlnd_azure](https://github.com/microsoft/mysqlnd_azure) , které vyvinula společnost Microsoft. 

Rozšíření mysqlnd_azure je k dispozici pro přidání do aplikací PHP prostřednictvím PECL a důrazně se doporučuje nainstalovat a nakonfigurovat rozšíření prostřednictvím oficiálně publikovaného [balíčku PECL](https://pecl.php.net/package/mysqlnd_azure).

> [!IMPORTANT]
> Podpora přesměrování [mysqlnd_azure](https://github.com/microsoft/mysqlnd_azure) rozšíření PHP je v současnosti ve verzi Preview.

### <a name="redirection-logic"></a>Logika přesměrování

>[!IMPORTANT]
> Logika/chování přesměrování – počáteční verze 1.1.0 byla aktualizována a **doporučuje se použít verzi 1.1.0 +**.

Chování přesměrování je určeno hodnotou `mysqlnd_azure.enableRedirect` . Následující tabulka popisuje chování přesměrování na základě hodnoty tohoto parametru počínaje **verzí 1.1.0 +**.

Pokud používáte starší verzi rozšíření mysqlnd_azure (verze 1.0.0-1.0.3), chování přesměrování je určeno hodnotou `mysqlnd_azure.enabled` . Platné hodnoty jsou `off` (funguje podobně jako v následující tabulce) a `on` (jako `preferred` v tabulce níže).  

|**hodnota mysqlnd_azure. enableRedirect**| **Chování**|
|----------------------------------------|-------------|
|`off` nebo `0`|Přesměrování nebude použito. |
|`on` nebo `1`|– Pokud připojení na straně ovladače nepoužívá protokol SSL, nebude provedeno žádné připojení. Vrátí se následující chyba: *"mysqlnd_azure. enableRedirect je zapnutá, ale v připojovacím řetězci není nastavená možnost SSL. Přesměrování je možné jenom s protokolem SSL.*<br>– Pokud se na straně ovladače používá protokol SSL, ale přesměrování není na straně serveru podporované, první připojení se přerušilo a vrátí se následující chyba: *"připojení přerušeno, protože přesměrování není na serveru MySQL povolené, nebo síťový balíček nesplňuje protokol přesměrování".*<br>– Pokud server MySQL podporuje přesměrování, ale přesměrované připojení z nějakého důvodu selhalo, přeruší také první připojení k proxy. Vrátí chybu přesměrovaného připojení.|
|`preferred` nebo `2`<br> (výchozí hodnota)|-mysqlnd_azure použije přesměrování, pokud je to možné.<br>– Pokud připojení na straně ovladače nepoužívá protokol SSL, server nepodporuje přesměrování nebo se přesměrované připojení nepovede na nezávažný důvod, zatímco připojení k proxy serveru je stále platným serverem, a to se přepne na první připojení proxy|

V dalších částech dokumentu se dozvíte, jak nainstalovat `mysqlnd_azure` rozšíření pomocí PECL a jak nastavit hodnotu tohoto parametru.

### <a name="ubuntu-linux"></a>Ubuntu Linux

#### <a name="prerequisites"></a>Požadavky 
- Verze PHP 7.2.15 + a 7.3.2 +
- HRUŠKY PHP 
- PHP – MySQL
- Server Azure Database for MySQL

1. Nainstalujte [mysqlnd_azure](https://github.com/microsoft/mysqlnd_azure) s využitím [PECL](https://pecl.php.net/package/mysqlnd_azure). Doporučuje se používat verzi 1.1.0 +.

    ```bash
    sudo pecl install mysqlnd_azure
    ```

2. Vyhledejte adresář rozšíření ( `extension_dir` ) spuštěním následujícího příkladu:

    ```bash
    php -i | grep "extension_dir"
    ```

3. Změňte adresáře na vrácenou složku a ujistěte `mysqlnd_azure.so` se, že je umístěna v této složce. 

4. Vyhledejte složku souborů. ini spuštěním následujícího příkladu: 

    ```bash
    php -i | grep "dir for additional .ini files"
    ```

5. Umožňuje změnit adresáře na tuto vrácenou složku. 

6. Vytvořte nový soubor. ini pro `mysqlnd_azure` . Zajistěte, aby bylo abecední pořadí názvu po mysqnld, protože moduly jsou načteny podle pořadí názvů souborů INI. Například pokud `mysqlnd` je pojmenovaný. ini `10-mysqlnd.ini` název, pojmenujte mysqlnd ini jako `20-mysqlnd-azure.ini` .

7. Do nového souboru. ini přidejte následující řádky, aby bylo možné přesměrování povolit.

    ```bash
    extension=mysqlnd_azure
    mysqlnd_azure.enableRedirect = on/off/preferred
    ```

### <a name="windows"></a>Windows

#### <a name="prerequisites"></a>Požadavky 
- Verze PHP 7.2.15 + a 7.3.2 +
- PHP – MySQL
- Server Azure Database for MySQL

1. Zjistěte, zda používáte verzi PHP x64 nebo x86 spuštěním následujícího příkazu:

    ```cmd
    php -i | findstr "Thread"
    ```

2. Stáhněte si odpovídající verzi x64 nebo x86 knihovny [mysqlnd_azure](https://github.com/microsoft/mysqlnd_azure) dll z [PECL](https://pecl.php.net/package/mysqlnd_azure) , která odpovídá vaší verzi PHP. Doporučuje se používat verzi 1.1.0 +.

3. Extrahujte soubor zip a vyhledejte DLL s názvem `php_mysqlnd_azure.dll` .

4. Vyhledejte adresář rozšíření ( `extension_dir` ) spuštěním následujícího příkazu:

    ```cmd
    php -i | find "extension_dir"
    ```

5. Zkopírujte `php_mysqlnd_azure.dll` soubor do adresáře vráceného v kroku 4. 

6. Vyhledejte složku PHP obsahující `php.ini` soubor pomocí následujícího příkazu:

    ```cmd
    php -i | find "Loaded Configuration File"
    ```

7. Upravte `php.ini` soubor a přidejte následující další řádky, aby bylo možné přesměrování povolit. 

    V části dynamické rozšíření: 
    ```cmd
    extension=mysqlnd_azure
    ```
    
    V části nastavení modulu:     
    ```cmd 
    [mysqlnd_azure]
    mysqlnd_azure.enableRedirect = on/off/preferred
    ```

### <a name="confirm-redirection"></a>Potvrdit přesměrování

Můžete také potvrdit přesměrování je nakonfigurované pomocí níže uvedeného ukázkového kódu PHP. Vytvořte soubor PHP s názvem `mysqlConnect.php` a vložte následující kód. Aktualizujte název serveru, uživatelské jméno a heslo vlastními. 
 
 ```php
<?php
$host = '<yourservername>.mysql.database.azure.com';
$username = '<yourusername>@<yourservername>';
$password = '<yourpassword>';
$db_name = 'testdb';
  echo "mysqlnd_azure.enableRedirect: ", ini_get("mysqlnd_azure.enableRedirect"), "\n";
  $db = mysqli_init();
  //The connection must be configured with SSL for redirection test
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
Další informace o připojovacích řetězcích naleznete v tématu [připojovací řetězce](howto-connection-string.md).
