---
title: Konfigurace aplikací PHP
description: Přečtěte si, jak nakonfigurovat předem sestavený kontejner PHP pro vaši aplikaci. Tento článek ukazuje nejběžnější konfigurační úlohy.
ms.devlang: php
ms.topic: article
ms.date: 03/28/2019
ms.openlocfilehash: a3de4769193d95a3ef483924c4d65c4fa1cc9f8d
ms.sourcegitcommit: 265f1d6f3f4703daa8d0fc8a85cbd8acf0a17d30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/02/2019
ms.locfileid: "74671840"
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

## <a name="run-composer"></a>Spustit skladatele

Ve výchozím nastavení Kudu nespustí [skladatel](https://getcomposer.org/). Pokud chcete povolit automatizaci skladatele během nasazování Kudu, je potřeba dodat [vlastní skript nasazení](https://github.com/projectkudu/kudu/wiki/Custom-Deployment-Script).

Z místního okna terminálu změňte adresář na svůj kořenový adresář úložiště. Postupujte podle pokynů k instalaci *skladatele. phar*pomocí [příkazového řádku](https://getcomposer.org/download/) .

Spusťte následující příkazy:

```bash
npm install kuduscript -g
kuduscript --php --scriptType bash --suppressPrompt
```

Kořen vašeho úložiště má teď dva nové soubory kromě *skladatele. phar*: *. Deployment* a *Deploy.sh*. Tyto soubory fungují jak pro Windows, tak pro systém Linux App Service.

Otevřete *Deploy.sh* a vyhledejte část `Deployment`. Celý oddíl nahraďte následujícím kódem:

```bash
##################################################################################################################################
# Deployment
# ----------

echo PHP deployment

# 1. KuduSync
if [[ "$IN_PLACE_DEPLOYMENT" -ne "1" ]]; then
  "$KUDU_SYNC_CMD" -v 50 -f "$DEPLOYMENT_SOURCE" -t "$DEPLOYMENT_TARGET" -n "$NEXT_MANIFEST_PATH" -p "$PREVIOUS_MANIFEST_PATH" -i ".git;.hg;.deployment;deploy.sh"
  exitWithMessageOnError "Kudu Sync failed"
fi

# 3. Initialize Composer Config
initializeDeploymentConfig

# 4. Use composer
echo "$DEPLOYMENT_TARGET"
if [ -e "$DEPLOYMENT_TARGET/composer.json" ]; then
  echo "Found composer.json"
  pushd "$DEPLOYMENT_TARGET"
  php composer.phar install $COMPOSER_ARGS
  exitWithMessageOnError "Composer install failed"
  popd
fi
##################################################################################################################################
```

Potvrďte všechny změny a znovu nasaďte svůj kód. Skladatel by teď měl běžet jako součást automatizace nasazení.

## <a name="customize-start-up"></a>Přizpůsobení spuštění

Ve výchozím nastavení zabudovaný kontejner PHP spouští server Apache. Při spuštění se spustí `apache2ctl -D FOREGROUND"`. Pokud chcete, můžete při spuštění spustit jiný příkaz spuštěním následujícího příkazu v [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --startup-file "<custom-command>"
```

## <a name="access-environment-variables"></a>Přístup k proměnným prostředí

V App Service můžete [nastavit nastavení aplikace](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings) mimo kód vaší aplikace. Pak k nim můžete přistupovat pomocí standardního vzoru [getenv ()](https://secure.php.net/manual/function.getenv.php) . Chcete-li například získat přístup k nastavení aplikace s názvem `DB_HOST`, použijte následující kód:

```php
getenv("DB_HOST")
```

## <a name="change-site-root"></a>Změnit kořenovou složku webu

Vybraná webová architektura může jako kořen webu použít podadresář. Například [Laravel](https://laravel.com/)používá podadresář `public/` jako kořenový adresář webu.

Výchozí obrázek PHP pro App Service používá Apache a neumožňuje přizpůsobení kořene webu pro vaši aplikaci. Pokud chcete toto omezení obejít, přidejte do svého kořene úložiště soubor *. htaccess* s následujícím obsahem:

```
<IfModule mod_rewrite.c>
    RewriteEngine on

    RewriteRule ^.*$ /public/$1 [NC,L,QSA]
</IfModule>
```

Pokud byste raději nepoužívali přepisování v souboru *.htaccess*, můžete místo toho nasadit svou aplikaci Laravel s použitím [vlastní image Dockeru](quickstart-docker-go.md).

## <a name="detect-https-session"></a>Zjistit relaci HTTPS

V App Service dojde k [ukončení protokolu SSL](https://wikipedia.org/wiki/TLS_termination_proxy) v nástrojích pro vyrovnávání zatížení sítě, takže všechny požadavky HTTPS dosáhnou vaší aplikace jako nešifrované požadavky HTTP. Pokud vaše logika aplikace potřebuje zkontrolovat, jestli jsou požadavky uživatele zašifrované, nebo ne, zkontrolujte `X-Forwarded-Proto` záhlaví.

```php
if (isset($_SERVER['X-Forwarded-Proto']) && $_SERVER['X-Forwarded-Proto'] === 'https') {
  // Do something when HTTPS is used
}
```

Oblíbená webová rozhraní umožňují přístup k informacím o `X-Forwarded-*` ve standardním vzorcích aplikací. V [CodeIgniter](https://codeigniter.com/) [is_https ()](https://github.com/bcit-ci/CodeIgniter/blob/master/system/core/Common.php#L338-L365) ve výchozím nastavení kontroluje hodnotu `X_FORWARDED_PROTO`.

## <a name="customize-phpini-settings"></a>Přizpůsobení nastavení php. ini

Pokud potřebujete provést změny v instalaci PHP, můžete změnit libovolné [direktivy php. ini](https://www.php.net/manual/ini.list.php) pomocí následujících kroků.

> [!NOTE]
> Nejlepším způsobem, jak zobrazit verzi PHP a aktuální konfiguraci *php. ini* , je volat [phpinfo ()](https://php.net/manual/function.phpinfo.php) ve vaší aplikaci.
>

### <a name="Customize-non-PHP_INI_SYSTEM directives"></a>Přizpůsobení – direktivy bez PHP_INI_SYSTEM

Chcete-li přizpůsobit direktivy PHP_INI_USER, PHP_INI_PERDIR a PHP_INI_ALL (viz [direktivy php. ini](https://www.php.net/manual/ini.list.php)), přidejte soubor *. htaccess* do kořenového adresáře aplikace.

V souboru *. htaccess* přidejte direktivy pomocí syntaxe `php_value <directive-name> <value>`. Například:

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

### <a name="customize-php_ini_system-directives"></a>Přizpůsobení direktiv PHP_INI_SYSTEM

Pokud chcete přizpůsobit direktivy PHP_INI_SYSTEM (viz [direktivy php. ini](https://www.php.net/manual/ini.list.php)), nemůžete použít přístup *. htaccess* . App Service poskytuje samostatný mechanismus pomocí nastavení aplikace `PHP_INI_SCAN_DIR`.

Nejdřív spuštěním následujícího příkazu v [Cloud Shell](https://shell.azure.com) přidejte nastavení aplikace s názvem `PHP_INI_SCAN_DIR`:

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings PHP_INI_SCAN_DIR="/usr/local/etc/php/conf.d:/home/site/ini"
```

`/usr/local/etc/php/conf.d` je výchozí adresář, ve kterém existuje *php. ini* . `/home/site/ini` je vlastní adresář, do kterého přidáte vlastní soubor *. ini* . Hodnoty se oddělují `:`.

Přejděte k webové relaci SSH pomocí kontejneru Linux (`https://<app-name>.scm.azurewebsites.net/webssh/host`).

Vytvořte adresář v `/home/site` s názvem `ini`a pak vytvořte soubor *. ini* v adresáři `/home/site/ini` (například *Settings. ini)* se směrnicemi, které chcete přizpůsobit. Použijte stejnou syntaxi, kterou použijete v souboru *php. ini* . 

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

Přidejte `bin` adresář do kořenového adresáře aplikace a umístěte do něj soubory rozšíření `.so` (například *MongoDB.so*). Ujistěte se, že jsou rozšíření kompatibilní s verzí PHP v Azure a jsou kompatibilní s VC9 a bez NTS (non-Thread-Safe).

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
- Otestujte aplikaci místně v provozním režimu. App Service spouští aplikace v Node. js v produkčním režimu, takže je nutné zajistit, aby váš projekt fungoval v provozním režimu místně. Například:
    - V závislosti na vašem *skladatele. JSON*se můžou nainstalovat různé balíčky pro produkční režim (`require` vs. `require-dev`).
    - Některé webové architektury můžou nasazovat statické soubory odlišně v produkčním režimu.
    - Při spuštění v produkčním režimu mohou některé webové architektury používat vlastní spouštěcí skripty.
- Spusťte aplikaci v App Service v režimu ladění. Například v [Laravel](https://meanjs.org/)můžete nakonfigurovat aplikaci tak, aby vyladěna zprávy ladění v produkčním prostředí, nastavením [nastavení aplikace `APP_DEBUG` na `true`](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings).

### <a name="robots933456"></a>robots933456

V protokolech kontejneru se může zobrazit následující zpráva:

```
2019-04-08T14:07:56.641002476Z "-" - - [08/Apr/2019:14:07:56 +0000] "GET /robots933456.txt HTTP/1.1" 404 415 "-" "-"
```

Tuto zprávu můžete bez obav ignorovat. `/robots933456.txt` je fiktivní cesta URL, kterou App Service používá ke kontrole, zda kontejner podporuje požadavky. Odpověď 404 jednoduše indikuje, že cesta neexistuje, ale umožňuje App Service víte, že je kontejner v pořádku a je připravený na reakci na požadavky.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Kurz: aplikace PHP s MySQL](tutorial-php-mysql-app.md)

> [!div class="nextstepaction"]
> [Nejčastější dotazy k App Service Linux](app-service-linux-faq.md)
