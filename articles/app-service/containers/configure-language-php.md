---
title: Konfigurace aplikace v PHP – Azure App Service | Dokumentace Microsoftu
description: Další informace o konfiguraci aplikace v PHP v Azure App Service
services: app-service
documentationcenter: ''
author: cephalin
manager: jpconnock
editor: ''
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/28/2019
ms.author: cephalin
ms.openlocfilehash: ed6a50ee68d39e6e0d01b405eb02edd6d4c93613
ms.sourcegitcommit: 6f043a4da4454d5cb673377bb6c4ddd0ed30672d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/08/2019
ms.locfileid: "65407587"
---
# <a name="configure-a-linux-php-app-for-azure-app-service"></a>Konfigurace aplikace v jazyce PHP Linuxem pro Azure App Service

Tato příručka ukazuje, jak konfigurovat předdefinované PHP runtime pro webové aplikace, back-endů mobilních a API apps ve službě Azure App Service.

Tato příručka obsahuje klíčové koncepty a pokyny pro vývojáře v PHP, které používají integrované kontejneru Linuxu ve službě App Service. Pokud jste nikdy použili službu Azure App Service, postupujte [rychlý start PHP](quickstart-php.md) a [PHP s MySQL kurzu](tutorial-php-mysql-app.md) první.

## <a name="show-php-version"></a>Zobrazit verze PHP

Chcete-li zobrazit aktuální verzi PHP, spusťte následující příkaz [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp config show --resource-group <resource-group-name> --name <app-name> --query linuxFxVersion
```

Chcete-li zobrazit všechny podporované verze PHP, spusťte následující příkaz [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp list-runtimes --linux | grep PHP
```

## <a name="set-php-version"></a>Nastavení verze PHP

Spuštěním následujícího příkazu [Cloud Shell](https://shell.azure.com) nastavení verze PHP 7.2:

```azurecli-interactive
az webapp config set --name <app-name> --resource-group <resource-group-name> --linux-fx-version "PHP|7.2"
```

## <a name="run-composer"></a>Spustit Composer

Ve výchozím nastavení, Kudu nespustí [Composer](https://getcomposer.org/). Povolení automatizace nástroje Composer během nasazení Kudu, budete muset zadat [vlastní skript nasazení](https://github.com/projectkudu/kudu/wiki/Custom-Deployment-Script).

V místním okně terminálu přejděte do kořenového adresáře úložiště. Postupujte podle [kroky instalace z příkazového řádku](https://getcomposer.org/download/) Stáhnout *composer.phar*.

Spusťte následující příkazy:

```bash
npm install kuduscript -g
kuduscript --php --scriptType bash --suppressPrompt
```

Kořenového adresáře úložiště teď má dva nové soubory kromě *composer.phar*: *.deployment* a *deploy.sh*. Tyto soubory lze použít i pro Windows a Linuxem verzí služby App Service.

Otevřít *deploy.sh* a najít `Deployment` oddílu. Nahradí celý oddíl s následujícím kódem:

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

Potvrďte všechny změny a znovu nasaďte svůj kód. Jako součást automatizace nasazení by teď měla být spuštěná autora.

## <a name="customize-start-up"></a>Přizpůsobení spuštění

Ve výchozím nastavení integrované spuštění serveru Apache kontejneru PHP. Při spouštění, spouští `apache2ctl -D FOREGROUND"`. Pokud chcete, můžete spustit k jinému příkazu při spouštění, spuštěním následujícího příkazu v [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --startup-file "<custom-command>"
```

## <a name="access-environment-variables"></a>Přístup k proměnným prostředí

Ve službě App Service můžete [nastavení aplikace](../web-sites-configure.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#app-settings) mimo kód vaší aplikace. Pak můžete přistupovat pomocí standardu [getenv()](https://secure.php.net/manual/function.getenv.php) vzor. Například pro přístup k nastavení aplikace volá `DB_HOST`, použijte následující kód:

```php
getenv("DB_HOST")
```

## <a name="change-site-root"></a>Změnit kořenového webu

Webová architektura podle vašeho výběru použít podadresář jako kořenový adresář webu. Například [Laravel](https://laravel.com/), používá `public/` podadresář jako kořenový adresář webu.

Výchozí image PHP pro službu App Service používá Apache a neumožňuje přizpůsobení kořenového webu pro vaši aplikaci. Chcete-li toto omezení obejít, přidejte *.htaccess* soubor do kořenového adresáře úložiště s následujícím obsahem:

```
<IfModule mod_rewrite.c>
    RewriteEngine on

    RewriteRule ^.*$ /public/$1 [NC,L,QSA]
</IfModule>
```

Pokud byste raději nepoužívali přepisování v souboru *.htaccess*, můžete místo toho nasadit svou aplikaci Laravel s použitím [vlastní image Dockeru](quickstart-docker-go.md).

## <a name="detect-https-session"></a>Zjistit relace HTTPS

Ve službě App Service [ukončení protokolu SSL](https://wikipedia.org/wiki/TLS_termination_proxy) se odehrává na nástroje pro vyrovnávání zatížení sítě, takže všechny požadavky HTTPS kontaktovat vaši aplikaci jako nešifrované požadavky HTTP. Pokud požadavkům vašich aplikací logiky ke kontrole, pokud jsou zašifrované požadavky uživatelů, nebo Ne, zkontrolujte `X-Forwarded-Proto` záhlaví.

```php
if (isset($_SERVER['X-Forwarded-Proto']) && $_SERVER['X-Forwarded-Proto'] === 'https') {
  // Do something when HTTPS is used
}
```

Oblíbené webové architektury umožněte přístup `X-Forwarded-*` informace o vzoru standardní aplikace. V [CodeIgniter](https://codeigniter.com/), [is_https()](https://github.com/bcit-ci/CodeIgniter/blob/master/system/core/Common.php#L338-L365) zkontroluje hodnotu vlastnosti `X_FORWARDED_PROTO` ve výchozím nastavení.

## <a name="customize-phpini-settings"></a>Nastavení souboru php.ini

Pokud potřebujete provést změny v instalaci PHP, můžete změnit libovolné [php.ini direktivy](https://www.php.net/manual/ini.list.php) pomocí následujících kroků.

> [!NOTE]
> Nejlepší způsob, jak zobrazit verze PHP a aktuální *php.ini* konfigurace je pro volání [phpinfo()](https://php.net/manual/function.phpinfo.php) ve vaší aplikaci.
>

### <a name="customize-non-phpinisystem-directives"></a>Přizpůsobení bez PHP_INI_SYSTEM direktivy

Přizpůsobení PHP_INI_USER PHP_INI_PERDIR a PHP_INI_ALL direktivy (naleznete v tématu [php.ini direktivy](https://www.php.net/manual/ini.list.php)), přidejte *.htaccess* souboru do kořenového adresáře aplikace.

V *.htaccess* přidejte direktivy pomocí `php_value <directive-name> <value>` syntaxe. Příklad:

```
php_value upload_max_filesize 1000M
php_value post_max_size 2000M
php_value memory_limit 3000M
php_value max_execution_time 180
php_value max_input_time 180
php_value display_errors On
php_value upload_max_filesize 10M
```

Opětovné nasazení aplikace se změnami a restartujte ji. Pokud provádíte nasazení pomocí Kudu (například pomocí [Git](../deploy-local-git.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)), se automaticky restartuje po nasazení.

Jako alternativu k použití *.htaccess*, můžete použít [ini_set()](https://www.php.net/manual/function.ini-set.php) ve vaší aplikaci přizpůsobit tyto jiné PHP_INI_SYSTEM direktivy.

### <a name="customize-phpinisystem-directives"></a>Přizpůsobení PHP_INI_SYSTEM direktivy

Přizpůsobení PHP_INI_SYSTEM direktivy (naleznete v tématu [php.ini direktivy](https://www.php.net/manual/ini.list.php)), nelze použít *.htaccess* přístup. Služba App Service poskytuje samostatným mechanismem pomocí `PHP_INI_SCAN_DIR` nastavení aplikace.

Nejprve spusťte následující příkaz [Cloud Shell](https://shell.azure.com) přidání aplikace názvem `PHP_INI_SCAN_DIR`:

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings PHP_INI_SCAN_DIR="/usr/local/etc/php/conf.d:/home/site/ini"
```

`/usr/local/etc/php/conf.d` je výchozí adresář, ve kterém *php.ini* existuje. `/home/site/ini` je vlastní adresář, ve kterém přidáte vlastní *.ini* souboru. Oddělte hodnoty `:`.

Přejděte na web relace SSH pomocí vašeho kontejneru Linuxu (`https://cephalin-container.scm.azurewebsites.net/webssh/host`).

Vytvoření adresáře v `/home/site` volá `ini`, vytvořte *.ini* ve `/home/site/ini` adresáře (například *settings.ini)* pomocí direktivy, které chcete přizpůsobit. Používají stejnou syntaxi, kterou použijete v *php.ini* souboru. 

> [!TIP]
> V předdefinované kontejnery Linuxu ve službě App Service */home* slouží jako trvalý sdílené úložiště. 
>

Například, chcete-li změnit hodnotu [expose_php](https://php.net/manual/ini.core.php#ini.expose-php) spuštěním následujících příkazů:

```bash
cd /home/site
mkdir ini
echo "expose_php = Off" >> ini/setting.ini
```

Aby se změny projevily restartujte aplikaci.

## <a name="enable-php-extensions"></a>Povolit rozšíření PHP

Integrované instalace PHP obsahovat nejčastěji používanými rozšířeními. Můžete povolit další rozšíření stejným způsobem, jakým [přizpůsobení souboru php.ini direktivy](#customize-php_ini_system-directives).

> [!NOTE]
> Nejlepší způsob, jak zobrazit verze PHP a aktuální *php.ini* konfigurace je pro volání [phpinfo()](https://php.net/manual/function.phpinfo.php) ve vaší aplikaci.
>

Chcete-li povolit další rozšíření, pomocí následujících kroků:

Přidat `bin` adresáře do kořenového adresáře aplikace a put `.so` soubory rozšíření (například *mongodb.so*). Ujistěte se, že rozšíření jsou kompatibilní s verzí PHP v Azure, jsou VC9 i bez bezpečným pro vlákno (nts) kompatibilní.

Vaše změny nasadí.

Postupujte podle kroků v [přizpůsobit PHP_INI_SYSTEM direktivy](#customize-php_ini_system-directives), přidejte rozšíření do vlastní *.ini* souboru [rozšíření](https://www.php.net/manual/ini.core.php#ini.extension) nebo [zend_extension ](https://www.php.net/manual/ini.core.php#ini.zend-extension) direktivy.

```ini
extension=/home/site/wwwroot/bin/mongodb.so
zend_extension=/home/site/wwwroot/bin/xdebug.so
```

Aby se změny projevily restartujte aplikaci.

## <a name="access-diagnostic-logs"></a>Přístup k diagnostickým protokolům

[!INCLUDE [Access diagnostic logs](../../../includes/app-service-web-logs-access-no-h.md)]

## <a name="open-ssh-session-in-browser"></a>Otevřít relaci SSH v prohlížeči

[!INCLUDE [Open SSH session in browser](../../../includes/app-service-web-ssh-connect-builtin-no-h.md)]

## <a name="troubleshooting"></a>Řešení potíží

Když pracovní aplikace v PHP ve službě App Service se chová jinak nebo obsahuje chyby, proveďte následující kroky:

- [Přístup k datovému proudu protokolů](#access-diagnostic-logs).
- Místní testování aplikace v provozním režimu. App Service spouští vaše aplikace Node.js v provozním režimu, takže je třeba, abyste měli jistotu, že projekt pracuje podle očekávání v provozním režimu místně. Příklad:
    - V závislosti na vaší *composer.json*, mohou být nainstalovány jiné balíčky k provozním režimu (`require` vs. `require-dev`).
    - Některá webová rozhraní může nasadit statické soubory jinak než v provozním režimu.
    - Některá webová rozhraní může používat vlastní spouštěcí skripty, při spuštění v produkčním režimu.
- Spusťte aplikaci ve službě App Service v režimu ladění. Například v [Laravel](https://meanjs.org/), můžete nakonfigurovat aplikaci tak, aby výstupní zprávy ladění v produkčním prostředí pomocí [nastavení `APP_DEBUG` nastavení aplikace nastavte na `true` ](../web-sites-configure.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json).

### <a name="robots933456"></a>robots933456

Může se zobrazit následující zpráva v protokoly kontejneru:

```
2019-04-08T14:07:56.641002476Z "-" - - [08/Apr/2019:14:07:56 +0000] "GET /robots933456.txt HTTP/1.1" 404 415 "-" "-"
```

Tuto zprávu můžete ignorovat. `/robots933456.txt` je fiktivní cesta adresy URL, která služba App Service používá ke kontrole, jestli je schopná obsluhovat požadavky kontejner. Odpovědi 404 jednoduše označuje, že cesta neexistuje, ale umožňuje vědět, že kontejner je v pořádku a připravená reagovat na žádosti o služby App Service.

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Kurz: Aplikace v PHP s MySQL](tutorial-php-mysql-app.md)

> [!div class="nextstepaction"]
> [App Service Linuxu – nejčastější dotazy](app-service-linux-faq.md)
