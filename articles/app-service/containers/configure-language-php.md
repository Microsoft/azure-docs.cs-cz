---
title: Konfigurace aplikací PHP
description: Přečtěte si, jak nakonfigurovat předem sestavený kontejner PHP pro vaši aplikaci. Tento článek ukazuje nejběžnější konfigurační úlohy.
ms.devlang: php
ms.topic: article
ms.date: 03/28/2019
ms.openlocfilehash: 97ccc309e6fd4efd48a609ab558e9842f376ccf5
ms.sourcegitcommit: f0b206a6c6d51af096a4dc6887553d3de908abf3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/28/2020
ms.locfileid: "84142108"
---
# <a name="configure-a-linux-php-app-for-azure-app-service"></a>Konfigurace aplikace pro Linux PHP pro Azure App Service

V této příručce se dozvíte, jak nakonfigurovat integrovaný modul runtime PHP pro webové aplikace, mobilní back-endy a aplikace API v Azure App Service.

Tato příručka poskytuje klíčové koncepty a pokyny pro vývojáře v PHP, kteří používají integrovaný kontejner Linux v nástroji App Service. Pokud jste nikdy Azure App Service nepoužili, postupujte nejprve podle kurzu [rychlý Start](quickstart-php.md) a [php s](tutorial-php-mysql-app.md) využitím php.

## <a name="show-php-version"></a>Zobrazit verzi PHP

Pokud chcete zobrazit aktuální verzi PHP, spusťte v [Cloud Shell](https://shell.azure.com)následující příkaz:

```azurecli-interactive
az webapp config show --resource-group <resource-group-name> --name <app-name> --query linuxFxVersion
```

Pokud chcete zobrazit všechny podporované verze PHP, spusťte v [Cloud Shell](https://shell.azure.com)následující příkaz:

```azurecli-interactive
az webapp list-runtimes --linux | grep PHP
```

## <a name="set-php-version"></a>Nastavit verzi PHP

Spuštěním následujícího příkazu v [Cloud Shell](https://shell.azure.com) nastavte verzi PHP na 7,2:

```azurecli-interactive
az webapp config set --name <app-name> --resource-group <resource-group-name> --linux-fx-version "PHP|7.2"
```

## <a name="customize-build-automation"></a>Přizpůsobení automatizace sestavení

Pokud nasadíte aplikaci s použitím balíčků Git nebo zip se zapnutou možností automatizace sestavení, App Service sestavování kroků automatizace pomocí následujícího postupu:

1. Spusťte vlastní skript, pokud je určen `PRE_BUILD_SCRIPT_PATH` .
1. Spusťte `php composer.phar install`.
1. Spusťte vlastní skript, pokud je určen `POST_BUILD_SCRIPT_PATH` .

`PRE_BUILD_COMMAND`a `POST_BUILD_COMMAND` jsou proměnné prostředí, které jsou ve výchozím nastavení prázdné. Chcete-li spustit příkazy před sestavením, definujte `PRE_BUILD_COMMAND` . Chcete-li spustit příkazy po sestavení, definujte `POST_BUILD_COMMAND` .

Následující příklad určuje dvě proměnné pro řadu příkazů, které jsou odděleny čárkami.

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings PRE_BUILD_COMMAND="echo foo, scripts/prebuild.sh"
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings POST_BUILD_COMMAND="echo foo, scripts/postbuild.sh"
```

Další proměnné prostředí pro přizpůsobení automatizace sestavení naleznete v tématu [Oryx Configuration](https://github.com/microsoft/Oryx/blob/master/doc/configuration.md).

Další informace o tom, jak App Service spouští a sestavuje aplikace PHP v systému Linux, najdete v [dokumentaci k Oryx: jak se zjišťují a vytváří aplikace v php](https://github.com/microsoft/Oryx/blob/master/doc/runtimes/php.md).

## <a name="customize-start-up"></a>Přizpůsobení spuštění

Ve výchozím nastavení používá integrovaný kontejner PHP server Apache. Při spuštění se spustí `apache2ctl -D FOREGROUND"` . Pokud chcete, můžete při spuštění spustit jiný příkaz spuštěním následujícího příkazu v [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --startup-file "<custom-command>"
```

## <a name="access-environment-variables"></a>Přístup k proměnným prostředí

V App Service můžete [nastavit nastavení aplikace](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings) mimo kód vaší aplikace. Pak k nim můžete přistupovat pomocí standardního vzoru [getenv ()](https://secure.php.net/manual/function.getenv.php) . Chcete-li například získat přístup k nastavení aplikace s názvem `DB_HOST` , použijte následující kód:

```php
getenv("DB_HOST")
```

## <a name="change-site-root"></a>Změnit kořenovou složku webu

Vybraná webová architektura může jako kořen webu použít podadresář. Například [Laravel](https://laravel.com/)používá `public/` podadresář jako kořenový adresář webu.

Výchozí obrázek PHP pro App Service používá Apache a neumožňuje přizpůsobení kořene webu pro vaši aplikaci. Pokud chcete toto omezení obejít, přidejte do svého kořene úložiště soubor *. htaccess* s následujícím obsahem:

```
<IfModule mod_rewrite.c>
    RewriteEngine on
    RewriteCond %{REQUEST_URI} ^/$
    RewriteRule ^(.*)$ /public/$1 [NC,L,QSA]
</IfModule>
```

Pokud byste raději nepoužívali přepisování v souboru *.htaccess*, můžete místo toho nasadit svou aplikaci Laravel s použitím [vlastní image Dockeru](quickstart-docker-go.md).

## <a name="detect-https-session"></a>Zjistit relaci HTTPS

V App Service dojde k [ukončení protokolu SSL](https://wikipedia.org/wiki/TLS_termination_proxy) v nástrojích pro vyrovnávání zatížení sítě, takže všechny požadavky HTTPS dosáhnou vaší aplikace jako nešifrované požadavky HTTP. Pokud vaše logika aplikace potřebuje, aby zkontrolovala, jestli jsou požadavky uživatele zašifrované, zkontrolujte `X-Forwarded-Proto` záhlaví.

```php
if (isset($_SERVER['X-Forwarded-Proto']) && $_SERVER['X-Forwarded-Proto'] === 'https') {
  // Do something when HTTPS is used
}
```

Oblíbená webová rozhraní umožňují přístup k `X-Forwarded-*` informacím ve standardním vzoru aplikace. V [CodeIgniter](https://codeigniter.com/) [is_https ()](https://github.com/bcit-ci/CodeIgniter/blob/master/system/core/Common.php#L338-L365) ve `X_FORWARDED_PROTO` výchozím nastavení kontroluje hodnotu.

## <a name="customize-phpini-settings"></a>Přizpůsobení nastavení php. ini

Pokud potřebujete provést změny v instalaci PHP, můžete změnit libovolné [direktivy php. ini](https://www.php.net/manual/ini.list.php) pomocí následujících kroků.

> [!NOTE]
> Nejlepším způsobem, jak zobrazit verzi PHP a aktuální konfiguraci *php. ini* , je volat [phpinfo ()](https://php.net/manual/function.phpinfo.php) ve vaší aplikaci.
>

### <a name="customize-non-php_ini_system-directives"></a><a name="Customize-non-PHP_INI_SYSTEM directives"></a>Přizpůsobení – direktivy bez PHP_INI_SYSTEM

Chcete-li přizpůsobit direktivy PHP_INI_USER, PHP_INI_PERDIR a PHP_INI_ALL (viz [direktivy php. ini](https://www.php.net/manual/ini.list.php)), přidejte soubor *. htaccess* do kořenového adresáře aplikace.

V souboru *. htaccess* přidejte direktivy pomocí `php_value <directive-name> <value>` syntaxe. Příklad:

```
php_value upload_max_filesize 1000M
php_value post_max_size 2000M
php_value memory_limit 3000M
php_value max_execution_time 180
php_value max_input_time 180
php_value display_errors On
php_value upload_max_filesize 10M
```

Znovu nasaďte aplikaci se změnami a restartujte ji. Pokud ho nasadíte pomocí Kudu (například pomocí [Gitu](../deploy-local-git.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)), po nasazení se automaticky restartuje.

Jako alternativu k používání *. htaccess*můžete v aplikaci použít [ini_set ()](https://www.php.net/manual/function.ini-set.php) k přizpůsobení těchto nePHP_INI_SYSTEMch direktiv.

### <a name="customize-php_ini_system-directives"></a><a name="customize-php_ini_system-directives"></a>Přizpůsobení direktiv PHP_INI_SYSTEM

Pokud chcete přizpůsobit direktivy PHP_INI_SYSTEM (viz [direktivy php. ini](https://www.php.net/manual/ini.list.php)), nemůžete použít přístup *. htaccess* . App Service poskytuje samostatný mechanismus pomocí `PHP_INI_SCAN_DIR` nastavení aplikace.

Nejdřív spuštěním následujícího příkazu v [Cloud Shell](https://shell.azure.com) přidejte nastavení aplikace s názvem `PHP_INI_SCAN_DIR` :

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings PHP_INI_SCAN_DIR="/usr/local/etc/php/conf.d:/home/site/ini"
```

`/usr/local/etc/php/conf.d`je výchozí adresář, ve kterém existuje *php. ini* . `/home/site/ini`je vlastní adresář, do kterého přidáte vlastní soubor *. ini* . Hodnoty oddělíte hodnotou `:` .

Přejděte k webové relaci SSH pomocí kontejneru Linux ( `https://<app-name>.scm.azurewebsites.net/webssh/host` ).

Vytvořte adresář s `/home/site` názvem `ini` a pak vytvořte soubor *. ini* v `/home/site/ini` adresáři (například *Settings. ini)* se směrnicemi, které chcete přizpůsobit. Použijte stejnou syntaxi, kterou použijete v souboru *php. ini* . 

> [!TIP]
> V integrovaných kontejnerech pro Linux v App Service se jako trvalé sdílené úložiště používá */Home* . 
>

Chcete-li například změnit hodnotu [expose_php](https://php.net/manual/ini.core.php#ini.expose-php) spusťte následující příkazy:

```bash
cd /home/site
mkdir ini
echo "expose_php = Off" >> ini/setting.ini
```

Aby se změny projevily, restartujte aplikaci.

## <a name="enable-php-extensions"></a>Povolit rozšíření PHP

Vestavěné instalace PHP obsahují nejběžněji používaná rozšíření. Můžete povolit další rozšíření stejným způsobem, jakým [přizpůsobíte direktivy php. ini](#customize-php_ini_system-directives).

> [!NOTE]
> Nejlepším způsobem, jak zobrazit verzi PHP a aktuální konfiguraci *php. ini* , je volat [phpinfo ()](https://php.net/manual/function.phpinfo.php) ve vaší aplikaci.
>

Pokud chcete povolit další rozšíření, postupujte podle těchto kroků:

Přidejte `bin` adresář do kořenového adresáře aplikace a umístěte do `.so` něj soubory rozšíření (například *MongoDB.so*). Ujistěte se, že jsou rozšíření kompatibilní s verzí PHP v Azure a jsou kompatibilní s VC9 a bez NTS (non-Thread-Safe).

Nasaďte změny.

Postupujte podle kroků v části [přizpůsobení direktiv PHP_INI_SYSTEM](#customize-php_ini_system-directives)a přidejte rozšíření do vlastního souboru *. ini* s direktivami [Extension](https://www.php.net/manual/ini.core.php#ini.extension) nebo [zend_extension](https://www.php.net/manual/ini.core.php#ini.zend-extension) .

```ini
extension=/home/site/wwwroot/bin/mongodb.so
zend_extension=/home/site/wwwroot/bin/xdebug.so
```

Aby se změny projevily, restartujte aplikaci.

## <a name="access-diagnostic-logs"></a>Přístup k diagnostickým protokolům

[!INCLUDE [Access diagnostic logs](../../../includes/app-service-web-logs-access-no-h.md)]

## <a name="open-ssh-session-in-browser"></a>Otevřít relaci SSH v prohlížeči

[!INCLUDE [Open SSH session in browser](../../../includes/app-service-web-ssh-connect-builtin-no-h.md)]

## <a name="troubleshooting"></a>Řešení potíží

Pokud se funkční aplikace v PHP chová odlišně v App Service nebo obsahuje chyby, zkuste následující:

- [Přístup ke streamu protokolů](#access-diagnostic-logs).
- Otestujte aplikaci místně v provozním režimu. App Service spouští aplikace v Node. js v produkčním režimu, takže je nutné zajistit, aby váš projekt fungoval v provozním režimu místně. Příklad:
    - V závislosti na vašem *skladatele. JSON*se můžou nainstalovat různé balíčky pro produkční režim ( `require` vs. `require-dev` ).
    - Některé webové architektury můžou nasazovat statické soubory odlišně v produkčním režimu.
    - Při spuštění v produkčním režimu mohou některé webové architektury používat vlastní spouštěcí skripty.
- Spusťte aplikaci v App Service v režimu ladění. Například v [Laravel](https://meanjs.org/)můžete nakonfigurovat aplikaci tak, aby výstupní zprávy ladění v produkčním prostředí nakonfigurovali nastavením [ `APP_DEBUG` aplikace na `true` ](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings).

[!INCLUDE [robots933456](../../../includes/app-service-web-configure-robots933456.md)]

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Kurz: aplikace PHP s MySQL](tutorial-php-mysql-app.md)

> [!div class="nextstepaction"]
> [Nejčastější dotazy k App Service Linux](app-service-linux-faq.md)
