---
title: Připojení k přesměrování – databáze Azure pro MySQL
description: Tento článek popisuje, jak můžete nakonfigurovat aplikaci pro připojení k Azure Database pro MySQL s přesměrováním.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 03/16/2020
ms.openlocfilehash: f987d5d9640c3bfef61320df379a68eae2f4712b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80246330"
---
# <a name="connect-to-azure-database-for-mysql-with-redirection"></a>Připojení k azure databázi pro MySQL s přesměrováním

Toto téma vysvětluje, jak připojit aplikaci azure databáze pro mysql server s režimem přesměrování. Přesměrování má za cíl snížit latenci sítě mezi klientskými aplikacemi a servery MySQL tím, že umožňuje aplikacím připojit se přímo k uzlům back-endového serveru.

## <a name="before-you-begin"></a>Než začnete
Přihlaste se k [portálu Azure](https://portal.azure.com). Vytvořte azure databázi pro server MySQL s motorem verze 5.6, 5.7 nebo 8.0. Podrobnosti najdete v článku [Jak vytvořit Azure Database pro MySQL server z portálu](quickstart-create-mysql-server-database-using-azure-portal.md) nebo Jak vytvořit Azure Database pro [MySQL server pomocí příkazového příkazu k příkazu k příkazu .](quickstart-create-mysql-server-database-using-azure-cli.md)

Přesměrování je aktuálně podporováno jenom v případě, že je na serveru Azure Database for MySQL **povolena ssl.** Podrobnosti o konfiguraci protokolu SSL najdete [v tématu Použití protokolu SSL s databází Azure pro mySQL](howto-configure-ssl.md#step-3--enforcing-ssl-connections-in-azure).

## <a name="php"></a>PHP

Podpora přesměrování v aplikacích PHP je k dispozici prostřednictvím [rozšíření mysqlnd_azure,](https://github.com/microsoft/mysqlnd_azure) které vyvinula společnost Microsoft. 

Rozšíření mysqlnd_azure je k dispozici pro přidání do aplikací PHP prostřednictvím PECL a důrazně doporučujeme nainstalovat a nakonfigurovat rozšíření prostřednictvím oficiálně publikovaného [balíčku PECL](https://pecl.php.net/package/mysqlnd_azure).

> [!IMPORTANT]
> Podpora přesměrování v rozšíření PHP [mysqlnd_azure](https://github.com/microsoft/mysqlnd_azure) je v současné době ve verzi preview.

### <a name="redirection-logic"></a>Logika přesměrování

>[!IMPORTANT]
> Logika/chování přesměrování začínající verze 1.1.0 byla aktualizována a **doporučuje se používat verzi 1.1.0+**.

Chování přesměrování je určeno hodnotou `mysqlnd_azure.enableRedirect`. Níže uvedená tabulka popisuje chování přesměrování na základě hodnoty tohoto parametru začínající **ve verzi 1.1.0+**.

Pokud používáte starší verzi rozšíření mysqlnd_azure (verze 1.0.0-1.0.3), chování přesměrování je `mysqlnd_azure.enabled`určeno hodnotou . Platné hodnoty `off` jsou (funguje podobně jako chování uvedené v `on` tabulce níže) a (funguje jako `preferred` v následující tabulce).  

|**mysqlnd_azure.enableHodnota Přesměrování**| **Chování**|
|----------------------------------------|-------------|
|`off` nebo `0`|Přesměrování nebude použito. |
|`on` nebo `1`|- Pokud není povoleno SSL na Azure Database for MySQL server, nebude provedeno žádné připojení. Bude vrácena následující chyba: *"mysqlnd_azure.enableRedirect je zapnuto, ale možnost SSL není nastavena v připojovacím řetězci. Přesměrování je možné pouze s SSL."*<br>- Pokud je na serveru MySQL povoleno SSL, ale přesměrování není na serveru podporováno, první připojení je přerušeno a je vrácena následující chyba: *"Připojení bylo přerušeno, protože přesměrování není povoleno na serveru MySQL nebo síťový balíček nesplňuje protokol přesměrování."*<br>- Pokud server MySQL podporuje přesměrování, ale přesměrované připojení se z nějakého důvodu nezdařilo, také přerušte první připojení proxy. Vrátí chybu přesměrovaného připojení.|
|`preferred` nebo `2`<br> (výchozí hodnota)|- mysqlnd_azure použije přesměrování, pokud je to možné.<br>- Pokud připojení nepoužívá SSL, server nepodporuje přesměrování nebo se přesměrované připojení nepřipojí z důvodu, který není závažný, zatímco připojení proxy je stále platné, vrátí se zpět k prvnímu připojení proxy.|

Následující části dokumentu budou nastínit, `mysqlnd_azure` jak nainstalovat rozšíření pomocí PECL a nastavit hodnotu tohoto parametru.

### <a name="ubuntu-linux"></a>Ubuntu Linux

#### <a name="prerequisites"></a>Požadavky 
- PHP verze 7.2.15+ a 7.3.2+
- PHP HRUŠKA 
- php-mysql
- Azure Database for MySQL server s povoleným protokolem SSL

1. Nainstalujte [mysqlnd_azure](https://github.com/microsoft/mysqlnd_azure) s [PECL](https://pecl.php.net/package/mysqlnd_azure). Doporučuje se používat verzi 1.1.0+.

    ```bash
    sudo pecl install mysqlnd_azure
    ```

2. Vyhledejte adresář`extension_dir`rozšíření ( ) spuštěním následujícího:

    ```bash
    php -i | grep "extension_dir"
    ```

3. Změňte adresáře na vrácenou složku a ujistěte se, že `mysqlnd_azure.so` je umístěn v této složce. 

4. Vyhledejte složku pro soubory INI spuštěním následujícího: 

    ```bash
    php -i | grep "dir for additional .ini files"
    ```

5. Změňte adresáře na tuto vrácenou složku. 

6. Vytvořte nový soubor INI pro soubor `mysqlnd_azure`. Ujistěte se, že abecední pořadí názvu je po mysqnld, protože moduly jsou načteny podle pořadí názvů souborů ini. Například pokud `mysqlnd` je pojmenováno `10-mysqlnd.ini`.ini , pojmenujte mysqlnd ini jako `20-mysqlnd-azure.ini`.

7. Do nového souboru INI přidejte následující řádky, které povolí přesměrování.

    ```bash
    extension=mysqlnd_azure
    mysqlnd_azure.enableRedirect = on/off/preferred
    ```

### <a name="windows"></a>Windows

#### <a name="prerequisites"></a>Požadavky 
- PHP verze 7.2.15+ a 7.3.2+
- php-mysql
- Azure Database for MySQL server s povoleným protokolem SSL

1. Zjistěte, zda používáte verzi PHP x64 nebo x86, pomocí následujícího příkazu:

    ```cmd
    php -i | findstr "Thread"
    ```

2. Stáhněte si odpovídající x64 nebo x86 verzi [mysqlnd_azure](https://github.com/microsoft/mysqlnd_azure) DLL z [PECL,](https://pecl.php.net/package/mysqlnd_azure) která odpovídá vaší verzi PHP. Doporučuje se používat verzi 1.1.0+.

3. Extrahujte soubor zip a `php_mysqlnd_azure.dll`vyhledejte dll s názvem .

4. Vyhledejte adresář`extension_dir`rozšíření ( ) spuštěním níže uvedeného příkazu:

    ```cmd
    php -i | find "extension_dir"
    ```

5. Zkopírujte `php_mysqlnd_azure.dll` soubor do adresáře vráceného v kroku 4. 

6. Vyhledejte složku PHP `php.ini` obsahující soubor pomocí následujícího příkazu:

    ```cmd
    php -i | find "Loaded Configuration File"
    ```

7. Upravte `php.ini` soubor a přidejte následující další řádky, abyste povolili přesměrování. 

    V části Dynamic extensions: 
    ```cmd
    extension=mysqlnd_azure
    ```
    
    V části Nastavení modulu:     
    ```cmd 
    [mysqlnd_azure]
    mysqlnd_azure.enableRedirect = on/off/preferred
    ```

### <a name="confirm-redirection"></a>Potvrdit přesměrování

Můžete také potvrdit, že přesměrování je nakonfigurováno s níže udávaným kódem PHP. Vytvořte soubor `mysqlConnect.php` PHP s názvem a vložte níže uvedený kód. Aktualizujte název serveru, uživatelské jméno a heslo pomocí vlastního. 
 
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
Další informace o připojovacích řetězcích naleznete [v tématu Connection Strings](howto-connection-string.md).
