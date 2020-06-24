---
title: Konfigurace aplikací pro Windows PHP
description: Naučte se konfigurovat aplikaci PHP v nativních instancích systému Windows App Service. Tento článek ukazuje nejběžnější konfigurační úlohy.
ms.devlang: php
ms.topic: article
ms.date: 06/02/2020
ms.openlocfilehash: 1eb4e9d349fdd0097cbde4e4cef3d5c61a167193
ms.sourcegitcommit: 34eb5e4d303800d3b31b00b361523ccd9eeff0ab
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/17/2020
ms.locfileid: "84908075"
---
# <a name="configure-a-windows-php-app-for-azure-app-service"></a>Konfigurace aplikace pro Windows PHP pro Azure App Service

V této příručce se dozvíte, jak nakonfigurovat webové aplikace PHP, mobilní back-endy a aplikace API v Azure App Service. Tato příručka se vztahuje na aplikace hostované na platformě Windows. Informace o aplikacích pro Linux najdete v tématu [Konfigurace aplikace typu php pro Linux pro Azure App Service](containers/configure-language-php.md).

Tato příručka poskytuje klíčové koncepty a pokyny pro vývojáře v PHP, kteří nasazují aplikace na App Service. Pokud jste nikdy Azure App Service nepoužili, postupujte nejprve podle kurzu [rychlý Start](app-service-web-get-started-php.md) a [php s](app-service-web-tutorial-php-mysql.md) využitím php.

## <a name="show-php-version"></a>Zobrazit verzi PHP

Pokud chcete zobrazit aktuální verzi PHP, spusťte v [Cloud Shell](https://shell.azure.com)následující příkaz:

```azurecli-interactive
az webapp config show --resource-group <resource-group-name> --name <app-name> --query phpVersion
```

Pokud chcete zobrazit všechny podporované verze PHP, spusťte v [Cloud Shell](https://shell.azure.com)následující příkaz:

```azurecli-interactive
az webapp list-runtimes | grep php
```

## <a name="set-php-version"></a>Nastavit verzi PHP

Spuštěním následujícího příkazu v [Cloud Shell](https://shell.azure.com) nastavte verzi PHP na 7,4:

```azurecli-interactive
az webapp config set --name <app-name> --resource-group <resource-group-name> --php-version 7.4
```

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

Pokud chcete vašemu úložišti povolit spouštění těchto nástrojů, musíte je přidat k závislostem v *package.jsna.* Příklad:

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

## <a name="access-environment-variables"></a>Přístup k proměnným prostředí

V App Service můžete [nastavit nastavení aplikace](configure-common.md#configure-app-settings) mimo kód vaší aplikace. Pak k nim můžete přistupovat pomocí standardního vzoru [getenv ()](https://secure.php.net/manual/function.getenv.php) . Chcete-li například získat přístup k nastavení aplikace s názvem `DB_HOST` , použijte následující kód:

```php
getenv("DB_HOST")
```

## <a name="change-site-root"></a>Změnit kořenovou složku webu

Vybraná webová architektura může jako kořen webu použít podadresář. Například [Laravel](https://laravel.com/)používá *veřejný/* podadresář jako kořenový adresář webu.

Pokud chcete přizpůsobit kořen lokality, nastavte cestu virtuální aplikace pro aplikaci pomocí [`az resource update`](/cli/azure/resource#az-resource-update) příkazu. Následující příklad nastaví kořen lokality na *veřejný/* podadresář v úložišti. 

```azurecli-interactive
az resource update --name web --resource-group <group-name> --namespace Microsoft.Web --resource-type config --parent sites/<app-name> --set properties.virtualApplications[0].physicalPath="site\wwwroot\public" --api-version 2015-06-01
```

Ve výchozím nastavení Azure App Service odkazuje na kořenovou cestu virtuální aplikace ( _/_ ) do kořenového adresáře nasazených souborů aplikace (_sites\wwwroot_).

## <a name="detect-https-session"></a>Zjistit relaci HTTPS

V App Service dojde k [ukončení protokolu SSL](https://wikipedia.org/wiki/TLS_termination_proxy) v nástrojích pro vyrovnávání zatížení sítě, takže všechny požadavky HTTPS dosáhnou vaší aplikace jako nešifrované požadavky HTTP. Pokud vaše logika aplikace potřebuje, aby zkontrolovala, jestli jsou požadavky uživatele zašifrované, zkontrolujte `X-Forwarded-Proto` záhlaví.

```php
if (isset($_SERVER['X-Forwarded-Proto']) && $_SERVER['X-Forwarded-Proto'] === 'https') {
  // Do something when HTTPS is used
}
```

Oblíbená webová rozhraní umožňují přístup k `X-Forwarded-*` informacím ve standardním vzoru aplikace. V [CodeIgniter](https://codeigniter.com/) [is_https ()](https://github.com/bcit-ci/CodeIgniter/blob/master/system/core/Common.php#L338-L365) ve `X_FORWARDED_PROTO` výchozím nastavení kontroluje hodnotu.

## <a name="customize-phpini-settings"></a>Přizpůsobení nastavení php.ini

Pokud potřebujete provést změny v instalaci PHP, můžete změnit libovolné [direktivyphp.ini](https://www.php.net/manual/ini.list.php) pomocí následujících kroků.

> [!NOTE]
> Nejlepším způsobem, jak zobrazit verzi PHP a aktuální *php.ini* konfigurace, je volat [phpinfo ()](https://php.net/manual/function.phpinfo.php) ve vaší aplikaci.
>

### <a name="customize-non-php_ini_system-directives"></a><a name="Customize-non-PHP_INI_SYSTEM directives"></a>Přizpůsobení – direktivy bez PHP_INI_SYSTEM

Chcete-li přizpůsobit direktivy PHP_INI_USER, PHP_INI_PERDIR a PHP_INI_ALL (viz [direktivyphp.ini](https://www.php.net/manual/ini.list.php)), přidejte `.user.ini` soubor do kořenového adresáře aplikace.

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

### <a name="customize-php_ini_system-directives"></a><a name="customize-php_ini_system-directives"></a>Přizpůsobení direktiv PHP_INI_SYSTEM

Pokud chcete přizpůsobit direktivy PHP_INI_SYSTEM (viz [direktivyphp.ini](https://www.php.net/manual/ini.list.php)), nemůžete použít přístup *. htaccess* . App Service poskytuje samostatný mechanismus pomocí `PHP_INI_SCAN_DIR` nastavení aplikace.

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

## <a name="enable-php-extensions"></a>Povolit rozšíření PHP

Vestavěné instalace PHP obsahují nejběžněji používaná rozšíření. Můžete povolit další rozšíření stejným způsobem, jakým [přizpůsobíte direktivy php.ini](#customize-php_ini_system-directives).

> [!NOTE]
> Nejlepším způsobem, jak zobrazit verzi PHP a aktuální *php.ini* konfigurace, je volat [phpinfo ()](https://php.net/manual/function.phpinfo.php) ve vaší aplikaci.
>

Pokud chcete povolit další rozšíření, postupujte podle těchto kroků:

Přidejte `bin` adresář do kořenového adresáře aplikace a umístěte do `.so` něj soubory rozšíření (například *MongoDB.so*). Ujistěte se, že jsou rozšíření kompatibilní s verzí PHP v Azure a jsou kompatibilní s VC9 a bez NTS (non-Thread-Safe).

Nasaďte změny.

Postupujte podle kroků v části [přizpůsobení direktiv PHP_INI_SYSTEM](#customize-php_ini_system-directives)a přidejte rozšíření do vlastního souboru *. ini* s direktivami [Extension](https://www.php.net/manual/ini.core.php#ini.extension) nebo [zend_extension](https://www.php.net/manual/ini.core.php#ini.zend-extension) .

```
extension=d:\home\site\wwwroot\bin\mongodb.so
zend_extension=d:\home\site\wwwroot\bin\xdebug.so
```

Aby se změny projevily, restartujte aplikaci.

## <a name="access-diagnostic-logs"></a>Přístup k diagnostickým protokolům

Pomocí nástroje standardní [error_log ()](https://php.net/manual/function.error-log.php) nastavte, aby se diagnostické protokoly zobrazovaly v Azure App Service.

[!INCLUDE [Access diagnostic logs](../../includes/app-service-web-logs-access-no-h.md)]

## <a name="troubleshooting"></a>Poradce při potížích

Pokud se funkční aplikace v PHP chová odlišně v App Service nebo obsahuje chyby, zkuste následující:

- [Přístup ke streamu protokolů](#access-diagnostic-logs).
- Otestujte aplikaci místně v provozním režimu. App Service spustí vaši aplikaci v produkčním režimu, takže je potřeba zajistit, aby váš projekt fungoval v provozním režimu místně. Příklad:
    - Některé webové architektury můžou nasazovat statické soubory odlišně v produkčním režimu.
    - Při spuštění v produkčním režimu mohou některé webové architektury používat vlastní spouštěcí skripty.
- Spusťte aplikaci v App Service v režimu ladění. Například v [Laravel](https://meanjs.org/)můžete nakonfigurovat aplikaci tak, aby výstupní zprávy ladění v produkčním prostředí nakonfigurovali nastavením [ `APP_DEBUG` aplikace na `true` ](configure-common.md#configure-app-settings).

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Kurz: aplikace PHP s MySQL](app-service-web-tutorial-php-mysql.md)
