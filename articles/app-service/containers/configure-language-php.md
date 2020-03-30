---
title: Konfigurace aplikací PHP
description: Přečtěte si, jak pro vaši aplikaci nakonfigurovat předem sestavený kontejner PHP. Tento článek ukazuje nejběžnější úlohy konfigurace.
ms.devlang: php
ms.topic: article
ms.date: 03/28/2019
ms.openlocfilehash: ad121d605e521704597471b446fa79cb43dfccc7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78255834"
---
# <a name="configure-a-linux-php-app-for-azure-app-service"></a>Konfigurace aplikace Linux PHP pro Azure App Service

Tato příručka vám ukáže, jak nakonfigurovat vestavěný běh ový čas PHP pro webové aplikace, mobilní back-endy a aplikace rozhraní API ve službě Azure App Service.

Tato příručka obsahuje klíčové koncepty a pokyny pro vývojáře PHP, kteří používají vestavěný kontejner Linuxu ve službě App Service. Pokud jste službu Azure App Service nikdy nepoužívali, postupujte nejprve podle [rychlého startu PHP](quickstart-php.md) a [PHP s kurzem MySQL.](tutorial-php-mysql-app.md)

## <a name="show-php-version"></a>Zobrazit verzi PHP

Chcete-li zobrazit aktuální verzi PHP, spusťte v [prostředí Cloud Shell](https://shell.azure.com)následující příkaz :

```azurecli-interactive
az webapp config show --resource-group <resource-group-name> --name <app-name> --query linuxFxVersion
```

Chcete-li zobrazit všechny podporované verze PHP, spusťte v [prostředí Cloud Shell](https://shell.azure.com)následující příkaz :

```azurecli-interactive
az webapp list-runtimes --linux | grep PHP
```

## <a name="set-php-version"></a>Nastavit verzi PHP

Spusťte následující příkaz v [prostředí Cloud Shell](https://shell.azure.com) a nastavte verzi PHP na verzi 7.2:

```azurecli-interactive
az webapp config set --name <app-name> --resource-group <resource-group-name> --linux-fx-version "PHP|7.2"
```

## <a name="customize-build-automation"></a>Přizpůsobení automatizace sestavení

Pokud aplikaci nasadíte pomocí balíčků Git nebo zip se zapnutou automatizací sestavení, služba App Service provede automatizaci automatizace následujícím pořadím:

1. Spusťte vlastní `PRE_BUILD_SCRIPT_PATH`skript, pokud je určen programem .
1. Spusťte `php composer.phar install`.
1. Spusťte vlastní `POST_BUILD_SCRIPT_PATH`skript, pokud je určen programem .

`PRE_BUILD_COMMAND`a `POST_BUILD_COMMAND` jsou proměnné prostředí, které jsou ve výchozím nastavení prázdné. Chcete-li spustit příkazy `PRE_BUILD_COMMAND`předběžného sestavení, definujte . Chcete-li spustit příkazy `POST_BUILD_COMMAND`po sestavení, definujte .

Následující příklad určuje dvě proměnné řady příkazů oddělených čárkami.

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings PRE_BUILD_COMMAND="echo foo, scripts/prebuild.sh"
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings POST_BUILD_COMMAND="echo foo, scripts/postbuild.sh"
```

Další proměnné prostředí pro přizpůsobení automatizace sestavení naleznete v [tématu Konfigurace Oryx](https://github.com/microsoft/Oryx/blob/master/doc/configuration.md).

Další informace o tom, jak služba App Service běží a vytváří aplikace PHP v Linuxu, najdete v [dokumentaci oryxu: Jak jsou aplikace PHP detekovány a sestaveny](https://github.com/microsoft/Oryx/blob/master/doc/runtimes/php.md).

## <a name="customize-start-up"></a>Přizpůsobení spuštění

Ve výchozím nastavení vestavěný kontejner PHP spouští server Apache. Při startu běží `apache2ctl -D FOREGROUND"`. Pokud chcete, můžete při spuštění spustit jiný příkaz spuštěním následujícího příkazu v [prostředí Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --startup-file "<custom-command>"
```

## <a name="access-environment-variables"></a>Přístup k proměnným prostředí

Ve službě App Service můžete [nastavit nastavení aplikace](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings) mimo kód aplikace. Pak k nim můžete přistupovat pomocí standardního [getenv()](https://secure.php.net/manual/function.getenv.php) vzoru. Chcete-li například získat `DB_HOST`přístup k nastavení aplikace s názvem , použijte následující kód:

```php
getenv("DB_HOST")
```

## <a name="change-site-root"></a>Změna kořenového adresáře webu

Webový rámec podle vašeho výběru může jako kořenový adresář webu používat podadresář. Například [Laravel](https://laravel.com/)používá `public/` podadresář jako kořenovou síť.

Výchozí obrázek PHP pro App Service používá Apache a neumožňuje vám přizpůsobit kořen webu pro vaši aplikaci. Chcete-li toto omezení obejít, přidejte soubor *HTAccess* do kořenového adresáře úložiště s následujícím obsahem:

```
<IfModule mod_rewrite.c>
    RewriteEngine on

    RewriteRule ^.*$ /public/$1 [NC,L,QSA]
</IfModule>
```

Pokud byste raději nepoužívali přepisování v souboru *.htaccess*, můžete místo toho nasadit svou aplikaci Laravel s použitím [vlastní image Dockeru](quickstart-docker-go.md).

## <a name="detect-https-session"></a>Rozpoznat relaci HTTPS

Ve službě App Service dojde k [ukončení SSL](https://wikipedia.org/wiki/TLS_termination_proxy) v síťových nástrojích pro vyrovnávání zatížení, takže všechny požadavky HTTPS se dostanou do vaší aplikace jako nešifrované požadavky HTTP. Pokud vaše logika aplikace potřebuje zkontrolovat, jestli jsou požadavky `X-Forwarded-Proto` uživatelů zašifrované nebo ne, zkontrolujte záhlaví.

```php
if (isset($_SERVER['X-Forwarded-Proto']) && $_SERVER['X-Forwarded-Proto'] === 'https') {
  // Do something when HTTPS is used
}
```

Oblíbené webové architektury umožňují `X-Forwarded-*` přístup k informacím ve standardním vzoru aplikace. V [CodeIgniter](https://codeigniter.com/), [is_https()](https://github.com/bcit-ci/CodeIgniter/blob/master/system/core/Common.php#L338-L365) kontroluje `X_FORWARDED_PROTO` hodnotu ve výchozím nastavení.

## <a name="customize-phpini-settings"></a>Přizpůsobení nastavení php.ini

Pokud potřebujete provést změny v instalaci PHP, můžete změnit některou ze [směrnic php.ini](https://www.php.net/manual/ini.list.php) pomocí následujících kroků.

> [!NOTE]
> Nejlepší způsob, jak vidět verzi PHP a aktuální *konfiguraci php.ini,* je zavolat [phpinfo()](https://php.net/manual/function.phpinfo.php) ve vaší aplikaci.
>

### <a name="customize-non-php_ini_system-directives"></a><a name="Customize-non-PHP_INI_SYSTEM directives"></a>Direktivy vlastní PHP_INI_SYSTEM

Chcete-li přizpůsobit direktivy PHP_INI_USER, PHP_INI_PERDIR a PHP_INI_ALL (viz [direktivy php.ini](https://www.php.net/manual/ini.list.php)), přidejte soubor *HTAccess* do kořenového adresáře aplikace.

Do souboru *.htaccess* přidejte direktivy pomocí `php_value <directive-name> <value>` syntaxe. Například:

```
php_value upload_max_filesize 1000M
php_value post_max_size 2000M
php_value memory_limit 3000M
php_value max_execution_time 180
php_value max_input_time 180
php_value display_errors On
php_value upload_max_filesize 10M
```

Znovu nasaďte aplikaci se změnami a restartujte ji. Pokud ji nasadíte s Kudu (například pomocí [Gitu),](../deploy-local-git.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)automaticky se po nasazení restartuje.

Jako alternativu k použití *.htaccess*můžete použít [ini_set()](https://www.php.net/manual/function.ini-set.php) ve vaší aplikaci k přizpůsobení těchto direktiv, které nejsou PHP_INI_SYSTEM.

### <a name="customize-php_ini_system-directives"></a><a name="customize-php_ini_system-directives"></a>Přizpůsobení direktiv PHP_INI_SYSTEM

Chcete-li přizpůsobit PHP_INI_SYSTEM direktivy (viz [direktivy php.ini](https://www.php.net/manual/ini.list.php)), nelze použít přístup *.htaccess.* Služba App Service poskytuje `PHP_INI_SCAN_DIR` samostatný mechanismus pomocí nastavení aplikace.

Nejprve spusťte následující příkaz v prostředí Cloud `PHP_INI_SCAN_DIR` [Shell](https://shell.azure.com) a přidejte nastavení aplikace s názvem :

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings PHP_INI_SCAN_DIR="/usr/local/etc/php/conf.d:/home/site/ini"
```

`/usr/local/etc/php/conf.d`je výchozí adresář, kde *php.ini* existuje. `/home/site/ini`je vlastní adresář, do kterého přidáte vlastní soubor *INI.* Hodnoty oddělíte `:`pomocí .

Přejděte na webovou relaci SSH`https://<app-name>.scm.azurewebsites.net/webssh/host`s linuxovým kontejnerem ( ).

Vytvořte adresář `/home/site` `ini`s názvem , pak vytvořte `/home/site/ini` soubor INI v *adresáři* (například *settings.ini)* se směrnicemi, které chcete přizpůsobit. Použijte stejnou syntaxi, kterou byste použili v souboru *php.ini.* 

> [!TIP]
> Ve vestavěných kontejnerech Linuxu ve službě App Service */home* se používá jako trvalé sdílené úložiště. 
>

Chcete-li například změnit hodnotu [expose_php](https://php.net/manual/ini.core.php#ini.expose-php) spusťte následující příkazy:

```bash
cd /home/site
mkdir ini
echo "expose_php = Off" >> ini/setting.ini
```

Změny se projeví tak, že aplikaci uvátáte.

## <a name="enable-php-extensions"></a>Povolení rozšíření PHP

Vestavěné instalace PHP obsahují nejčastěji používaná rozšíření. Můžete povolit další rozšíření stejným způsobem, jakým [můžete přizpůsobit php.ini směrnic](#customize-php_ini_system-directives).

> [!NOTE]
> Nejlepší způsob, jak vidět verzi PHP a aktuální *konfiguraci php.ini,* je zavolat [phpinfo()](https://php.net/manual/function.phpinfo.php) ve vaší aplikaci.
>

Chcete-li povolit další rozšíření, postupujte takto:

Přidejte `bin` adresář do kořenového adresáře `.so` aplikace a vložte do něj soubory rozšíření (například *mongodb.so*). Ujistěte se, že rozšíření jsou kompatibilní s verzí PHP v Azure a jsou kompatibilní s VC9 a nepro přístup pro přístup z více vláken (nts).

Nasaďte změny.

Postupujte podle pokynů v [části Přizpůsobit PHP_INI_SYSTEM direktivy](#customize-php_ini_system-directives), přidejte rozšíření do vlastního souboru *INI* pomocí direktiv [rozšíření](https://www.php.net/manual/ini.core.php#ini.extension) nebo [zend_extension.](https://www.php.net/manual/ini.core.php#ini.zend-extension)

```ini
extension=/home/site/wwwroot/bin/mongodb.so
zend_extension=/home/site/wwwroot/bin/xdebug.so
```

Změny se projeví tak, že aplikaci uvátáte.

## <a name="access-diagnostic-logs"></a>Přístup k diagnostickým protokolům

[!INCLUDE [Access diagnostic logs](../../../includes/app-service-web-logs-access-no-h.md)]

## <a name="open-ssh-session-in-browser"></a>Otevření relace SSH v prohlížeči

[!INCLUDE [Open SSH session in browser](../../../includes/app-service-web-ssh-connect-builtin-no-h.md)]

## <a name="troubleshooting"></a>Řešení potíží

Když se pracovní aplikace PHP ve službě App Service chová jinak nebo má chyby, zkuste toto:

- [Přístup k datovému proudu protokolu](#access-diagnostic-logs).
- Otestujte aplikaci místně v produkčním režimu. Služba App Service spouští aplikace Node.js v produkčním režimu, takže se musíte ujistit, že váš projekt funguje podle očekávání v produkčním režimu místně. Například:
    - V závislosti na souboru *composer.json*mohou být`require` pro `require-dev`produkční režim (vs.) nainstalovány různé balíčky.
    - Některé webové architektury mohou nasadit statické soubory odlišně v produkčním režimu.
    - Některé webové architektury mohou při spuštění v produkčním režimu používat vlastní spouštěcí skripty.
- Spusťte aplikaci ve službě App Service v režimu ladění. Například v [Laravel](https://meanjs.org/), můžete nakonfigurovat aplikaci pro výstup ladicí zprávy v produkčním prostředí [nastavením `APP_DEBUG` nastavení aplikace na `true` ](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings).

[!INCLUDE [robots933456](../../../includes/app-service-web-configure-robots933456.md)]

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Výuka: PHP aplikace s MySQL](tutorial-php-mysql-app.md)

> [!div class="nextstepaction"]
> [Nejčastější dotazy k aplikační službě Linux](app-service-linux-faq.md)
