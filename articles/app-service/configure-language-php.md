---
title: Konfigurace aplikací PHP
description: Naučte se konfigurovat aplikaci PHP v nativních instancích systému Windows nebo v předem sestaveném kontejneru PHP v Azure App Service. Tento článek ukazuje nejběžnější konfigurační úlohy.
ms.devlang: php
ms.topic: article
ms.date: 06/02/2020
zone_pivot_groups: app-service-platform-windows-linux
ms.openlocfilehash: 94cbe0fa6669546cee8e989a6db2fcbb428cb9d0
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/21/2021
ms.locfileid: "107829435"
---
# <a name="configure-a-php-app-for-azure-app-service"></a>Konfigurace aplikace PHP pro Azure App Service

V této příručce se dozvíte, jak nakonfigurovat webové aplikace PHP, mobilní back-endy a aplikace API v Azure App Service.

Tato příručka poskytuje klíčové koncepty a pokyny pro vývojáře v PHP, kteří nasazují aplikace na App Service. Pokud jste nikdy Azure App Service nepoužili, postupujte nejprve podle kurzu [rychlý Start](quickstart-php.md) a [php s](tutorial-php-mysql-app.md) využitím php.

## <a name="show-php-version"></a>Zobrazit verzi PHP

::: zone pivot="platform-windows"  

Pokud chcete zobrazit aktuální verzi PHP, spusťte v [Cloud Shell](https://shell.azure.com)následující příkaz:

```azurecli-interactive
az webapp config show --resource-group <resource-group-name> --name <app-name> --query phpVersion
```

> [!NOTE]
> Pokud chcete adresovat slot pro vývoj, uveďte parametr `--slot` následovaný názvem slotu.

Pokud chcete zobrazit všechny podporované verze PHP, spusťte v [Cloud Shell](https://shell.azure.com)následující příkaz:

```azurecli-interactive
az webapp list-runtimes | grep php
```

::: zone-end

::: zone pivot="platform-linux"

Pokud chcete zobrazit aktuální verzi PHP, spusťte v [Cloud Shell](https://shell.azure.com)následující příkaz:

```azurecli-interactive
az webapp config show --resource-group <resource-group-name> --name <app-name> --query linuxFxVersion
```

> [!NOTE]
> Pokud chcete adresovat slot pro vývoj, uveďte parametr `--slot` následovaný názvem slotu.

Pokud chcete zobrazit všechny podporované verze PHP, spusťte v [Cloud Shell](https://shell.azure.com)následující příkaz:

```azurecli-interactive
az webapp list-runtimes --linux | grep PHP
```

::: zone-end

## <a name="set-php-version"></a>Nastavit verzi PHP

::: zone pivot="platform-windows"  

Spuštěním následujícího příkazu v [Cloud Shell](https://shell.azure.com) nastavte verzi PHP na 7,4:

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --php-version 7.4
```

::: zone-end

::: zone pivot="platform-linux"

Spuštěním následujícího příkazu v [Cloud Shell](https://shell.azure.com) nastavte verzi PHP na 7,2:

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --linux-fx-version "PHP|7.2"
```

::: zone-end

::: zone pivot="platform-windows"  

## <a name="run-composer"></a>Spustit skladatele

Pokud chcete, aby App Service spustil [skladatel](https://getcomposer.org/) v době nasazení, nejjednodušší způsob je zahrnout do úložiště skladatele.

Z místního okna terminálu změňte adresář na svůj kořenový adresář úložiště a podle pokynů v části [stažení skladatele](https://getcomposer.org/download/) stáhněte *skladatele. phar* do kořenového adresáře adresáře.

Spusťte následující příkazy (potřebujete [npm](https://www.npmjs.com/get-npm) nainstalované):

```bash
npm install kuduscript -g
kuduscript --node --scriptType bash --suppressPrompt
```

Kořenový adresář úložiště má teď dva další soubory: *. Deployment* a *Deploy.sh*.

Otevřete *Deploy.sh* a vyhledejte `Deployment` část, která vypadá takto:

```bash
##################################################################################################################################
# Deployment
# ----------
```

Přidejte část Code, kterou potřebujete ke spuštění požadovaného nástroje *na konci* `Deployment` oddílu:

```bash
# 4. Use composer
echo "$DEPLOYMENT_TARGET"
if [ -e "$DEPLOYMENT_TARGET/composer.json" ]; then
  echo "Found composer.json"
  pushd "$DEPLOYMENT_TARGET"
  php composer.phar install $COMPOSER_ARGS
  exitWithMessageOnError "Composer install failed"
  popd
fi
```

Potvrďte všechny změny a nasaďte svůj kód pomocí Gitu nebo nasaďte zip s povoleným automatizací sestavení. Skladatel by teď měl běžet jako součást automatizace nasazení.

## <a name="run-gruntbowergulp"></a>Spustit grunt/Bower/Gulp

Pokud chcete, aby App Service spouštěla oblíbené nástroje pro automatizaci v době nasazení, jako je například grunt, Bower nebo Gulp, je nutné dodat [vlastní skript nasazení](https://github.com/projectkudu/kudu/wiki/Custom-Deployment-Script). App Service spustí tento skript při nasazení v Gitu nebo s povoleným [nasazením zip](deploy-zip.md) s povolenou automatizací sestavení. 

Pokud chcete vašemu úložišti povolit spouštění těchto nástrojů, musíte je přidat k závislostem v *package.jsna.* Například:

```json
"dependencies": {
  "bower": "^1.7.9",
  "grunt": "^1.0.1",
  "gulp": "^3.9.1",
  ...
}
```

Z místního okna terminálu změňte adresář na svůj kořenový adresář úložiště a spusťte následující příkazy (potřebujete nainstalovanou [npm](https://www.npmjs.com/get-npm) ):

```bash
npm install kuduscript -g
kuduscript --node --scriptType bash --suppressPrompt
```

Kořenový adresář úložiště má teď dva další soubory: *. Deployment* a *Deploy.sh*.

Otevřete *Deploy.sh* a vyhledejte `Deployment` část, která vypadá takto:

```bash
##################################################################################################################################
# Deployment
# ----------
```

Tato část končí na běhu `npm install --production` . Přidejte část Code, kterou potřebujete ke spuštění požadovaného nástroje *na konci* `Deployment` oddílu:

- [Bower](#bower)
- [Gulp](#gulp)
- [Grunt](#grunt)

Podívejte se na [příklad v ukázce MEAN.js](https://github.com/Azure-Samples/meanjs/blob/master/deploy.sh#L112-L135), kde skript nasazení také spustí vlastní `npm install` příkaz.

### <a name="bower"></a>Bower

Tento fragment kódu je spuštěn `bower install` .

```bash
if [ -e "$DEPLOYMENT_TARGET/bower.json" ]; then
  cd "$DEPLOYMENT_TARGET"
  eval ./node_modules/.bin/bower install
  exitWithMessageOnError "bower failed"
  cd - > /dev/null
fi
```

### <a name="gulp"></a>Gulp

Tento fragment kódu je spuštěn `gulp imagemin` .

```bash
if [ -e "$DEPLOYMENT_TARGET/gulpfile.js" ]; then
  cd "$DEPLOYMENT_TARGET"
  eval ./node_modules/.bin/gulp imagemin
  exitWithMessageOnError "gulp failed"
  cd - > /dev/null
fi
```

### <a name="grunt"></a>Grunt

Tento fragment kódu je spuštěn `grunt` .

```bash
if [ -e "$DEPLOYMENT_TARGET/Gruntfile.js" ]; then
  cd "$DEPLOYMENT_TARGET"
  eval ./node_modules/.bin/grunt
  exitWithMessageOnError "Grunt failed"
  cd - > /dev/null
fi
```

::: zone-end

::: zone pivot="platform-linux"

## <a name="customize-build-automation"></a>Přizpůsobení automatizace sestavení

Pokud nasadíte aplikaci s použitím balíčků Git nebo zip se zapnutou možností automatizace sestavení, App Service sestavování kroků automatizace pomocí následujícího postupu:

1. Spusťte vlastní skript, pokud je určen `PRE_BUILD_SCRIPT_PATH` .
1. Spusťte `php composer.phar install`.
1. Spusťte vlastní skript, pokud je určen `POST_BUILD_SCRIPT_PATH` .

`PRE_BUILD_COMMAND` a `POST_BUILD_COMMAND` jsou proměnné prostředí, které jsou ve výchozím nastavení prázdné. Chcete-li spustit příkazy před sestavením, definujte `PRE_BUILD_COMMAND` . Chcete-li spustit příkazy po sestavení, definujte `POST_BUILD_COMMAND` .

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

::: zone-end

## <a name="access-environment-variables"></a>Přístup k proměnným prostředí

V App Service můžete [nastavit nastavení aplikace](configure-common.md#configure-app-settings) mimo kód vaší aplikace. Pak k nim můžete přistupovat pomocí standardního vzoru [getenv ()](https://secure.php.net/manual/function.getenv.php) . Například pro přístup k aplikačnímu nastavení s názvem `DB_HOST` použijete následující kód:

```php
getenv("DB_HOST")
```

## <a name="change-site-root"></a>Změnit kořenovou složku webu

::: zone pivot="platform-windows"  

Vybraná webová architektura může jako kořen webu použít podadresář. Například [Laravel](https://laravel.com/)používá *veřejný/* podadresář jako kořenový adresář webu.

Pokud chcete přizpůsobit kořen lokality, nastavte cestu virtuální aplikace pro aplikaci pomocí [`az resource update`](/cli/azure/resource#az_resource_update) příkazu. Následující příklad nastaví kořen lokality na *veřejný/* podadresář v úložišti. 

```azurecli-interactive
az resource update --name web --resource-group <group-name> --namespace Microsoft.Web --resource-type config --parent sites/<app-name> --set properties.virtualApplications[0].physicalPath="site\wwwroot\public" --api-version 2015-06-01
```

Ve výchozím nastavení Azure App Service odkazuje na kořenovou cestu virtuální aplikace ( _/_ ) do kořenového adresáře nasazených souborů aplikace (_sites\wwwroot_).

::: zone-end

::: zone pivot="platform-linux"

Vybraná webová architektura může jako kořen webu použít podadresář. Například [Laravel](https://laravel.com/)používá `public/` podadresář jako kořenový adresář webu.

Výchozí obrázek PHP pro App Service používá Apache a neumožňuje přizpůsobení kořene webu pro vaši aplikaci. Pokud chcete toto omezení obejít, přidejte do svého kořene úložiště soubor *. htaccess* s následujícím obsahem:

```
<IfModule mod_rewrite.c>
    RewriteEngine on
    RewriteCond %{REQUEST_URI} ^(.*)
    RewriteRule ^(.*)$ /public/$1 [NC,L,QSA]
</IfModule>
```

Pokud byste raději nepoužívali přepisování v souboru *.htaccess*, můžete místo toho nasadit svou aplikaci Laravel s použitím [vlastní image Dockeru](quickstart-custom-container.md).

::: zone-end

## <a name="detect-https-session"></a>Zjistit relaci HTTPS

V App Service dojde k [ukončení protokolu SSL](https://wikipedia.org/wiki/TLS_termination_proxy) v nástrojích pro vyrovnávání zatížení sítě, takže všechny požadavky HTTPS dosáhnou vaší aplikace jako nešifrované požadavky HTTP. Pokud vaše logika aplikace potřebuje, aby zkontrolovala, jestli jsou požadavky uživatele zašifrované, zkontrolujte `X-Forwarded-Proto` záhlaví.

```php
if (isset($_SERVER['HTTP_X_FORWARDED_PROTO']) && $_SERVER['HTTP_X_FORWARDED_PROTO'] === 'https') {
// Do something when HTTPS is used
}
```

Oblíbená webová rozhraní umožňují přístup k `X-Forwarded-*` informacím ve standardním vzoru aplikace. V [CodeIgniter](https://codeigniter.com/) [is_https ()](https://github.com/bcit-ci/CodeIgniter/blob/master/system/core/Common.php#L338-L365) ve `X_FORWARDED_PROTO` výchozím nastavení kontroluje hodnotu.

## <a name="customize-phpini-settings"></a>Přizpůsobení nastavení php.ini

Pokud potřebujete provést změny v instalaci PHP, můžete změnit libovolné [ direktivyphp.ini](https://www.php.net/manual/ini.list.php) pomocí následujících kroků.

> [!NOTE]
> Nejlepším způsobem, jak zobrazit verzi PHP a aktuální *php.ini* konfigurace, je volat [phpinfo ()](https://php.net/manual/function.phpinfo.php) ve vaší aplikaci.
>

### <a name="customize-non-php_ini_system-directives"></a><a name="Customize-non-PHP_INI_SYSTEM directives"></a>Přizpůsobení – direktivy bez PHP_INI_SYSTEM

::: zone pivot="platform-windows"  

Chcete-li přizpůsobit direktivy PHP_INI_USER, PHP_INI_PERDIR a PHP_INI_ALL (viz [ direktivyphp.ini](https://www.php.net/manual/ini.list.php)), přidejte `.user.ini` soubor do kořenového adresáře aplikace.

Přidejte konfigurační nastavení do `.user.ini` souboru pomocí stejné syntaxe, jakou byste použili v `php.ini` souboru. Pokud jste například chtěli zapnout nastavení `display_errors` a nastavit `upload_max_filesize` nastavení na 10 milionů, `.user.ini` soubor by obsahoval tento text:

```
 ; Example Settings
 display_errors=On
 upload_max_filesize=10M

 ; Write errors to d:\home\LogFiles\php_errors.log
 ; log_errors=On
```

Znovu nasaďte aplikaci se změnami a restartujte ji.

Jako alternativu k používání `.user.ini` souboru můžete v aplikaci použít [ini_set ()](https://www.php.net/manual/function.ini-set.php) k přizpůsobení těchto nePHP_INI_SYSTEMch direktiv.

::: zone-end

::: zone pivot="platform-linux"

Chcete-li přizpůsobit direktivy PHP_INI_USER, PHP_INI_PERDIR a PHP_INI_ALL (viz [ direktivyphp.ini](https://www.php.net/manual/ini.list.php)), přidejte soubor *. htaccess* do kořenového adresáře aplikace.

V souboru *. htaccess* přidejte direktivy pomocí `php_value <directive-name> <value>` syntaxe. Například:

```
php_value upload_max_filesize 1000M
php_value post_max_size 2000M
php_value memory_limit 3000M
php_value max_execution_time 180
php_value max_input_time 180
php_value display_errors On
php_value upload_max_filesize 10M
```

Znovu nasaďte aplikaci se změnami a restartujte ji. Pokud ho nasadíte pomocí Kudu (například pomocí [Gitu](deploy-local-git.md)), po nasazení se automaticky restartuje.

Jako alternativu k používání *. htaccess* můžete v aplikaci použít [ini_set ()](https://www.php.net/manual/function.ini-set.php) k přizpůsobení těchto nePHP_INI_SYSTEMch direktiv.

::: zone-end

### <a name="customize-php_ini_system-directives"></a><a name="customize-php_ini_system-directives"></a>Přizpůsobení direktiv PHP_INI_SYSTEM

::: zone pivot="platform-windows"  

Pokud chcete přizpůsobit direktivy PHP_INI_SYSTEM (viz [ direktivyphp.ini](https://www.php.net/manual/ini.list.php)), nemůžete použít přístup *. htaccess* . App Service poskytuje samostatný mechanismus pomocí `PHP_INI_SCAN_DIR` nastavení aplikace.

Nejdřív spuštěním následujícího příkazu v [Cloud Shell](https://shell.azure.com) přidejte nastavení aplikace s názvem `PHP_INI_SCAN_DIR` :

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings PHP_INI_SCAN_DIR="d:\home\site\ini"
```

Přejděte do konzoly Kudu ( `https://<app-name>.scm.azurewebsites.net/DebugConsole` ) a přejděte na adresu `d:\home\site` .

Vytvořte adresář s `d:\home\site` názvem `ini` a potom v adresáři vytvořte soubor *. ini* `d:\home\site\ini` (například *settings.ini)* se směrnicemi, které chcete přizpůsobit. Použijte stejnou syntaxi, kterou použijete v souboru *php.ini* . 

Chcete-li například změnit hodnotu [expose_php](https://php.net/manual/ini.core.php#ini.expose-php) spusťte následující příkazy:

```bash
cd /home/site
mkdir ini
echo "expose_php = Off" >> ini/setting.ini
```

Aby se změny projevily, restartujte aplikaci.

::: zone-end

::: zone pivot="platform-linux"

Pokud chcete přizpůsobit direktivy PHP_INI_SYSTEM (viz [ direktivyphp.ini](https://www.php.net/manual/ini.list.php)), nemůžete použít přístup *. htaccess* . App Service poskytuje samostatný mechanismus pomocí `PHP_INI_SCAN_DIR` nastavení aplikace.

Nejdřív spuštěním následujícího příkazu v [Cloud Shell](https://shell.azure.com) přidejte nastavení aplikace s názvem `PHP_INI_SCAN_DIR` :

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings PHP_INI_SCAN_DIR="/usr/local/etc/php/conf.d:/home/site/ini"
```

`/usr/local/etc/php/conf.d` je výchozí adresář, ve kterém *php.ini* existuje. `/home/site/ini` je vlastní adresář, do kterého přidáte vlastní soubor *. ini* . Hodnoty oddělíte hodnotou `:` .

Přejděte k webové relaci SSH pomocí kontejneru Linux ( `https://<app-name>.scm.azurewebsites.net/webssh/host` ).

Vytvořte adresář s `/home/site` názvem `ini` a potom v adresáři vytvořte soubor *. ini* `/home/site/ini` (například *settings.ini)* se směrnicemi, které chcete přizpůsobit. Použijte stejnou syntaxi, kterou použijete v souboru *php.ini* . 

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

::: zone-end

## <a name="enable-php-extensions"></a>Povolit rozšíření PHP

::: zone pivot="platform-windows"  

Vestavěné instalace PHP obsahují nejběžněji používaná rozšíření. Můžete povolit další rozšíření stejným způsobem, jakým [přizpůsobíte direktivy php.ini](#customize-php_ini_system-directives).

> [!NOTE]
> Nejlepším způsobem, jak zobrazit verzi PHP a aktuální *php.ini* konfigurace, je volat [phpinfo ()](https://php.net/manual/function.phpinfo.php) ve vaší aplikaci.
>

Pokud chcete povolit další rozšíření, postupujte podle těchto kroků:

Přidejte `bin` adresář do kořenového adresáře aplikace a umístěte do `.dll` něj soubory rozšíření (například *mongodb.dll*). Ujistěte se, že jsou rozšíření kompatibilní s verzí PHP v Azure a jsou kompatibilní s VC9 a bez NTS (non-Thread-Safe).

Nasaďte změny.

Postupujte podle kroků v části [přizpůsobení direktiv PHP_INI_SYSTEM](#customize-php_ini_system-directives)a přidejte rozšíření do vlastního souboru *. ini* s direktivami [Extension](https://www.php.net/manual/ini.core.php#ini.extension) nebo [zend_extension](https://www.php.net/manual/ini.core.php#ini.zend-extension) .

```
extension=d:\home\site\wwwroot\bin\mongodb.dll
zend_extension=d:\home\site\wwwroot\bin\xdebug.dll
```

Aby se změny projevily, restartujte aplikaci.

::: zone-end

::: zone pivot="platform-linux"

Vestavěné instalace PHP obsahují nejběžněji používaná rozšíření. Můžete povolit další rozšíření stejným způsobem, jakým [přizpůsobíte direktivy php.ini](#customize-php_ini_system-directives).

> [!NOTE]
> Nejlepším způsobem, jak zobrazit verzi PHP a aktuální *php.ini* konfigurace, je volat [phpinfo ()](https://php.net/manual/function.phpinfo.php) ve vaší aplikaci.
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

::: zone-end

## <a name="access-diagnostic-logs"></a>Přístup k diagnostickým protokolům

::: zone pivot="platform-windows"  

Pomocí nástroje standardní [error_log ()](https://php.net/manual/function.error-log.php) nastavte, aby se diagnostické protokoly zobrazovaly v Azure App Service.

[!INCLUDE [Access diagnostic logs](../../includes/app-service-web-logs-access-no-h.md)]

::: zone-end

::: zone pivot="platform-linux"

[!INCLUDE [Access diagnostic logs](../../includes/app-service-web-logs-access-linux-no-h.md)]

::: zone-end

## <a name="troubleshooting"></a>Řešení potíží

Pokud se funkční aplikace v PHP chová odlišně v App Service nebo obsahuje chyby, zkuste následující:

- [Přístup ke streamu protokolů](#access-diagnostic-logs).
- Otestujte aplikaci místně v provozním režimu. App Service spustí vaši aplikaci v produkčním režimu, takže je potřeba zajistit, aby váš projekt fungoval v provozním režimu místně. Například:
    - V závislosti na vaší *composer.js* se můžou v produkčním režimu ( `require` vs.) nainstalovat různé balíčky `require-dev` .
    - Některé webové architektury můžou nasazovat statické soubory odlišně v produkčním režimu.
    - Při spuštění v produkčním režimu mohou některé webové architektury používat vlastní spouštěcí skripty.
- Spusťte aplikaci v App Service v režimu ladění. Například v [Laravel](https://meanjs.org/)můžete nakonfigurovat aplikaci tak, aby výstupní zprávy ladění v produkčním prostředí nakonfigurovali nastavením [ `APP_DEBUG` aplikace na `true` ](configure-common.md#configure-app-settings).

::: zone pivot="platform-linux"

[!INCLUDE [robots933456](../../includes/app-service-web-configure-robots933456.md)]

::: zone-end

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Kurz: aplikace PHP s MySQL](tutorial-php-mysql-app.md)

::: zone pivot="platform-linux"

> [!div class="nextstepaction"]
> [Nejčastější dotazy k App Service v Linuxu](faq-app-service-linux.md)

::: zone-end
