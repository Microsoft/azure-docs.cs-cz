---
author: cynthn
ms.service: virtual-machines-linux
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: 5df1f7ff44a1603dd03d1d803ae9960dc124781e
ms.sourcegitcommit: 6e09760197a91be564ad60ffd3d6f48a241e083b
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/29/2018
ms.locfileid: "50227400"
---
## <a name="install-wordpress"></a>Instalace WordPressu

Pokud chcete vyzkoušet svůj stack, nainstalujte ukázkovou aplikaci. Například následujícím postupem se nainstaluje open source platforma pro vytváření webů a blogů [WordPress](https://wordpress.org/). Mezi další sady funkcí, které můžete vyzkoušet, patří [Drupal](http://www.drupal.org) nebo [Moodle](https://moodle.org/). 

Tato instalace WordPressu slouží pouze pro účely testování konceptu. Pokud chcete nainstalovat nejnovější produkční verzi WordPressu s doporučeným nastavením zabezpečení, přečtěte si [dokumentaci WordPressu](https://codex.wordpress.org/Main_Page). 



### <a name="install-the-wordpress-package"></a>Instalace balíčku WordPress

Spusťte následující příkaz:

```bash
sudo apt install wordpress
```

### <a name="configure-wordpress"></a>Konfigurace WordPress

Nakonfigurujte WordPress tak, aby používal MySQL a PHP.

V pracovním adresáři vytvořte textový soubor `wordpress.sql`, který použijete ke konfiguraci databáze MySQL pro WordPress: 

```bash
sudo sensible-editor wordpress.sql
```

Přidejte následující příkazy a hodnotu *yourPassword* nahraďte libovolným heslem databáze (ostatní hodnoty nechte beze změny). Pokud jste dříve nastavili zásady zabezpečení MySQL pro ověřování síly hesla, ujistěte se, že vaše heslo splňuje tyto požadavky na sílu hesla. Uložte soubor.

```sql
CREATE DATABASE wordpress;
GRANT SELECT,INSERT,UPDATE,DELETE,CREATE,DROP,ALTER
ON wordpress.*
TO wordpress@localhost
IDENTIFIED BY 'yourPassword';
FLUSH PRIVILEGES;
```

Spuštěním následujícího příkazu vytvořte databázi:

```bash
cat wordpress.sql | sudo mysql --defaults-extra-file=/etc/mysql/debian.cnf
```

Protože soubor `wordpress.sql` obsahuje přihlašovací údaje k databázi, po použití ho odstraňte:

```bash
sudo rm wordpress.sql
```

Pokud chcete nakonfigurovat PHP, spuštěním následujícího příkazu otevřete libovolný textový editor a vytvořte soubor `/etc/wordpress/config-localhost.php`:

```bash
sudo sensible-editor /etc/wordpress/config-localhost.php
```
Zkopírujte do souboru následující řádky a hodnotu *yourPassword* nahraďte heslem k vaší databázi WordPressu (ostatní hodnoty nechte beze změny). Pak soubor uložte.

```php
<?php
define('DB_NAME', 'wordpress');
define('DB_USER', 'wordpress');
define('DB_PASSWORD', 'yourPassword');
define('DB_HOST', 'localhost');
define('WP_CONTENT_DIR', '/usr/share/wordpress/wp-content');
?>
```


Přesuňte instalaci WordPressu do kořenového adresáře dokumentů webového serveru:

```bash
sudo ln -s /usr/share/wordpress /var/www/html/wordpress

sudo mv /etc/wordpress/config-localhost.php /etc/wordpress/config-default.php
```

Teď můžete dokončit instalaci WordPressu a začít na této platformě publikovat. Otevřete prohlížeč a přejděte na adresu `http://yourPublicIPAddress/wordpress`. Použijte veřejnou IP adresu svého virtuálního počítače. Informační stránka by měla vypadat podobně jako na tomto obrázku.

![Stránka instalace Wordpressu](./media/virtual-machines-linux-tutorial-wordpress/wordpressstartpage.png)