---
title: 'Výuka: Linux PHP aplikace s MySQL'
description: Zjistěte, jak získat aplikaci Linux Node.js, která funguje ve službě Azure App Service, s připojením k databázi MySQL v Azure. Laravel se používá v tomto kurzu.
ms.devlang: php
ms.topic: tutorial
ms.date: 11/25/2019
ms.custom: mvc, cli-validate, seodec18
ms.openlocfilehash: 693f132bed590af65cf79ebf4739ea5dcadbfc52
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "80045537"
---
# <a name="build-a-php-and-mysql-app-in-azure-app-service-on-linux"></a>Vytvoření aplikace PHP a MySQL ve službě Azure App Service na Linuxu

> [!NOTE]
> Tento článek nasadí aplikaci do služby App Service v Linuxu. Informace o nasazení ve službě App Service ve _Windows_najdete [v tématu Vytvoření aplikace PHP a MySQL v Azure](../app-service-web-tutorial-php-mysql.md).
>

[App Service na Linuxu](app-service-linux-intro.md) poskytuje vysoce škálovatelnou webhostingovou službu s vlastní mizákou pomocí operačního systému Linux. Tento výukový program ukazuje, jak vytvořit aplikaci PHP a připojit ji k databázi MySQL. Po dokončení budete mít ve službě App Service v Linuxu spuštěnou aplikaci [Laravel](https://laravel.com/).

![Aplikace PHP spuštěná ve službě Azure App Service](./media/tutorial-php-mysql-app/complete-checkbox-published.png)

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvořit databázi MySQL v Azure
> * Připojit k MySQL aplikaci PHP
> * Nasadit aplikaci do Azure
> * Aktualizovat datový model a znovu nasadit aplikaci
> * Streamovat diagnostické protokoly z Azure
> * Spravovat aplikaci na webu Azure Portal

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Požadavky

Pro absolvování tohoto kurzu potřebujete:

* [Nainstalovat Git](https://git-scm.com/).
* [Nainstalovat PHP 5.6.4 nebo novější](https://php.net/downloads.php)
* [Nainstalovat Composer](https://getcomposer.org/doc/00-intro.md)
* Povolit následující rozšíření PHP vyžadovaná aplikací Laravel: OpenSSL, PDO-MySQL, Mbstring, Tokenizer a XML
* [Nainstalovat a spustit MySQL](https://dev.mysql.com/doc/refman/5.7/en/installing.html). 

## <a name="prepare-local-mysql"></a>Příprava místního MySQL

V tomto kroku vytvoříte na místním serveru MySQL databázi, kterou budete v tomto kurzu používat.

### <a name="connect-to-local-mysql-server"></a>Připojení k místnímu serveru MySQL

V okně terminálu se připojte k místnímu serveru MySQL. Toto okno terminálu můžete používat ke spuštění všech příkazů v tomto kurzu.

```bash
mysql -u root -p
```

Pokud se zobrazí výzva k zadání hesla, zadejte heslo k účtu `root`. Pokud si heslo ke kořenovému účtu nepamatujete, projděte si článek [MySQL: Resetování kořenového hesla](https://dev.mysql.com/doc/refman/5.7/en/resetting-permissions.html).

Pokud se váš příkaz úspěšně provede, znamená to, že je váš server MySQL spuštěný. Pokud ne, provedením [kroků po instalaci MySQL](https://dev.mysql.com/doc/refman/5.7/en/postinstallation.html) zkontrolujte, jestli je místní server MySQL spuštěný.

### <a name="create-a-database-locally"></a>Vytvoření databáze v místním prostředí

Na příkazovém řádku `mysql` vytvořte databázi.

```sql 
CREATE DATABASE sampledb;
```

Ukončete připojení k serveru zadáním příkazu `quit`.

```sql
quit
```

<a name="step2"></a>

## <a name="create-a-php-app-locally"></a>Vytvoření aplikace PHP v místním prostředí
V tomto kroku získáte ukázkovou aplikaci Laravel, nakonfigurujete její připojení k databázi a spustíte ji v místním prostředí. 

### <a name="clone-the-sample"></a>Vytvoření klonu ukázky

V okně terminálu přejděte pomocí příkazu `cd` do pracovního adresáře.

Ukázkové úložiště naklonujete spuštěním následujícího příkazu.

```bash
git clone https://github.com/Azure-Samples/laravel-tasks
```

Pomocí příkazu `cd` přejděte do naklonovaného adresáře.
Nainstalujte požadované balíčky.

```bash
cd laravel-tasks
composer install
```

### <a name="configure-mysql-connection"></a>Konfigurace připojení k MySQL

V kořenovém adresáři úložiště vytvořte soubor *.env*. Zkopírujte do souboru *.env* následující proměnné. Nahraďte zástupný symbol _ &lt;root_password>_ heslem uživatele kořenového účtu MySQL.

```txt
APP_ENV=local
APP_DEBUG=true
APP_KEY=SomeRandomString

DB_CONNECTION=mysql
DB_HOST=127.0.0.1
DB_DATABASE=sampledb
DB_USERNAME=root
DB_PASSWORD=<root_password>
```

Informace o tom, jak Laravel používá soubor _.env_, najdete v článku [Laravel Environment Configuration](https://laravel.com/docs/5.4/configuration#environment-configuration) (Konfigurace prostředí Laravel).

### <a name="run-the-sample-locally"></a>Spuštění ukázky v místním prostředí

Spusťte [migrace databází Laravel](https://laravel.com/docs/5.4/migrations), aby se vytvořily tabulky, které aplikace potřebuje. Pokud chcete zjistit, které tabulky migrace vytvářejí, podívejte se do adresáře _database/migrations_ v úložišti Git.

```bash
php artisan migrate
```

Vygenerujte nový klíč aplikace Laravel.

```bash
php artisan key:generate
```

Spusťte aplikaci.

```bash
php artisan serve
```

V prohlížeči přejděte na `http://localhost:8000`. Na stránce přidejte několik úkolů.

![Úspěšné připojení aplikace PHP k MySQL](./media/tutorial-php-mysql-app/mysql-connect-success.png)

Pokud chcete zastavit PHP, zadejte v terminálu `Ctrl + C`.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="create-mysql-in-azure"></a>Vytvoření databáze MySQL v Azure

V tomto kroku vytvoříte v [Azure Database for MySQL](/azure/mysql) databázi MySQL. Později nakonfigurujete aplikaci PHP pro připojení k této databázi.

### <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

[!INCLUDE [Create resource group](../../../includes/app-service-web-create-resource-group-linux-no-h.md)] 

### <a name="create-a-mysql-server"></a>Vytvoření serveru MySQL

Vytvořte server v Azure Database [`az mysql server create`](/cli/azure/mysql/server?view=azure-cli-latest#az-mysql-server-create) for MySQL pomocí příkazu.

V následujícím příkazu nahraďte jedinečný název serveru zástupným symbolem * \<mySQL-server-name>,* uživatelským jménem * \<>správce a uživatele mandatory *a heslem pro zástupný symbol * \<hesla správce>.* Název serveru se používá jako součást koncového bodu MySQL (`https://<mysql-server-name>.mysql.database.azure.com`), takže musí být jedinečný v rámci všech serverů v Azure. Podrobnosti o výběru sku MySQL DB najdete v [tématu vytvoření databáze Azure pro server MySQL](https://docs.microsoft.com/azure/mysql/quickstart-create-mysql-server-database-using-azure-cli#create-an-azure-database-for-mysql-server).

```azurecli-interactive
az mysql server create --resource-group myResourceGroup --name <mysql-server-name> --location "West Europe" --admin-user <admin-user> --admin-password <admin-password> --sku-name B_Gen5_1
```

Po vytvoření serveru MySQL se v Azure CLI zobrazí podobné informace jako v následujícím příkladu:

```json
{
  "administratorLogin": "<admin-user>",
  "administratorLoginPassword": null,
  "fullyQualifiedDomainName": "<mysql-server-name>.mysql.database.azure.com",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.DBforMySQL/servers/<mysql-server-name>",
  "location": "westeurope",
  "name": "<mysql-server-name>",
  "resourceGroup": "myResourceGroup",
  ...
}
```

### <a name="configure-server-firewall"></a>Konfigurace brány firewall serveru

Vytvořte pravidlo brány firewall pro server MySQL, [`az mysql server firewall-rule create`](/cli/azure/mysql/server/firewall-rule?view=azure-cli-latest#az-mysql-server-firewall-rule-create) které umožní připojení klientů pomocí příkazu. Pokud je jako počáteční i koncová adresa IP nastavená hodnota 0.0.0.0, je brána firewall otevřená jen pro ostatní prostředky Azure. 

```azurecli-interactive
az mysql server firewall-rule create --name allAzureIPs --server <mysql-server-name> --resource-group myResourceGroup --start-ip-address 0.0.0.0 --end-ip-address 0.0.0.0
```

> [!TIP] 
> Pravidlo brány firewall můžete dál omezit [použitím jenom odchozích IP adres, které vaše aplikace používá](../overview-inbound-outbound-ips.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#find-outbound-ips).
>

V prostředí Cloud Shell spusťte příkaz znovu, abyste * \<* povolili přístup z místního počítače nahrazením>ip adresy [místní IP adresou místní IP adresou IPv4](https://www.whatsmyip.org/).

```azurecli-interactive
az mysql server firewall-rule create --name AllowLocalClient --server <mysql-server-name> --resource-group myResourceGroup --start-ip-address=<your-ip-address> --end-ip-address=<your-ip-address>
```

### <a name="connect-to-production-mysql-server-locally"></a>Místní připojení k produkčnímu serveru MySQL

V okně terminálu se připojte k serveru MySQL v Azure. Použijte hodnotu, kterou jste zadali dříve, pro _ &lt;>admin-user>_ a _ &lt;mysql-server-name _. Po zobrazení výzvy k zadání hesla použijte heslo, které jste zadali při vytváření databáze v Azure.

```bash
mysql -u <admin-user>@<mysql-server-name> -h <mysql-server-name>.mysql.database.azure.com -P 3306 -p
```

### <a name="create-a-production-database"></a>Vytvoření produkční databáze

Na příkazovém řádku `mysql` vytvořte databázi.

```sql
CREATE DATABASE sampledb;
```

### <a name="create-a-user-with-permissions"></a>Vytvoření uživatele s oprávněními

Vytvořte uživatele databáze se jménem _phpappuser_ a přidělte mu všechna oprávnění k databázi `sampledb`.

```sql
CREATE USER 'phpappuser' IDENTIFIED BY 'MySQLAzure2017'; 
GRANT ALL PRIVILEGES ON sampledb.* TO 'phpappuser';
```

Ukončete připojení k serveru zadáním příkazu `quit`.

```sql
quit
```

## <a name="connect-app-to-azure-mysql"></a>Připojení aplikace k Azure MySQL

V tomto kroku připojíte aplikaci PHP k databázi MySQL, kterou jste vytvořili v Azure Database for MySQL.

<a name="devconfig"></a>

### <a name="configure-the-database-connection"></a>Konfigurace připojení k databázi

V kořenovém adresáři úložiště vytvořte soubor _.env.production_ a zkopírujte do něj následující proměnné. Nahraďte zástupný _ &lt;symbol>názvu mysql-server _.

```txt
APP_ENV=production
APP_DEBUG=true
APP_KEY=SomeRandomString

DB_CONNECTION=mysql
DB_HOST=<mysql-server-name>.mysql.database.azure.com
DB_DATABASE=sampledb
DB_USERNAME=phpappuser@<mysql-server-name>
DB_PASSWORD=MySQLAzure2017
MYSQL_SSL=true
```

Uložte změny.

> [!TIP]
> Za účelem zabezpečení informací o vašem připojení k MySQL je tento soubor již vyloučený z úložiště Git (viz soubor _.gitignore_ v kořenovém adresáři úložiště). Později se dozvíte, jak ve službě App Service nakonfigurovat proměnné prostředí pro připojení k vaší databázi v Azure Database for MySQL. S použitím proměnných prostředí nepotřebujete soubor *.env* ve službě App Service.
>

### <a name="configure-ssl-certificate"></a>Konfigurace certifikátu SSL

Ve výchozím nastavení vynucuje Azure Database for MySQL od klientů připojení SSL. Pokud se chcete připojit ke své databázi MySQL v Azure, musíte použít certifikát [_.pem_, který poskytuje Azure Database for MySQL](../../mysql/howto-configure-ssl.md).

Otevřete soubor _config/database.php_ a do `connections.mysql` přidejte parametry _sslmode_ a _options_, jak je znázorněno v následujícím kódu.

```php
'mysql' => [
    ...
    'sslmode' => env('DB_SSLMODE', 'prefer'),
    'options' => (env('MYSQL_SSL') && extension_loaded('pdo_mysql')) ? [
        PDO::MYSQL_ATTR_SSL_KEY    => '/ssl/BaltimoreCyberTrustRoot.crt.pem',
    ] : []
],
```

V tomto kurzu je certifikát `BaltimoreCyberTrustRoot.crt.pem` pro usnadnění součástí úložiště.

### <a name="test-the-application-locally"></a>Test aplikace v místním prostředí

Spusťte migrace databází Laravel s _.env.production_ jako souborem prostředí, aby se ve vaší databázi MySQL v Azure Database for MySQL vytvořily tabulky. Nezapomeňte, že soubor _.env.production_ obsahuje informace o připojení k vaší databázi MySQL v Azure.

```bash
php artisan migrate --env=production --force
```

Soubor _.env.production_ ještě nemá platný klíč aplikace. Vygenerujte pro něj nový klíč v terminálu.

```bash
php artisan key:generate --env=production --force
```

Spusťte ukázkovou aplikaci s _.env.production_ jako souborem prostředí.

```bash
php artisan serve --env=production
```

Přejděte na adresu `http://localhost:8000`. Pokud se stránka načte bez chyb, aplikace PHP se v Azure připojuje k databázi MySQL v Azure.

Na stránce přidejte několik úkolů.

![Úspěšné připojení aplikace PHP k Azure Database for MySQL](./media/tutorial-php-mysql-app/mysql-connect-success.png)

Pokud chcete zastavit PHP, zadejte v terminálu `Ctrl + C`.

### <a name="commit-your-changes"></a>Potvrzení změn

Potvrďte provedené změny spuštěním následujících příkazů Gitu:

```bash
git add .
git commit -m "database.php updates"
```

Vaše aplikace je připravená k nasazení.

## <a name="deploy-to-azure"></a>Nasazení do Azure

V tomto kroku nasadíte aplikaci PHP připojenou k MySQL do služby Azure App Service.

Aplikace Laravel se spustí v adresáři _/public_. Výchozí image Dockeru s PHP pro službu App Service používá Apache a neumožňuje přizpůsobení `DocumentRoot` pro Laravel. Pomocí souboru `.htaccess` však můžete přepsat směrování všech požadavků do adresáře _/public_ místo kořenového adresáře. Kořenový adresář úložiště pro tyto účely již soubor `.htaccess` obsahuje. S tímto souborem je vaše aplikace Laravel připravená k nasazení.

Další informace naleznete v [tématu Změna kořenového adresáře webu](configure-language-php.md#change-site-root).

### <a name="configure-a-deployment-user"></a>Konfigurace uživatele nasazení

[!INCLUDE [Configure deployment user](../../../includes/configure-deployment-user-no-h.md)]

### <a name="create-an-app-service-plan"></a>Vytvoření plánu služby App Service

[!INCLUDE [Create app service plan no h](../../../includes/app-service-web-create-app-service-plan-linux-no-h.md)]

### <a name="create-a-web-app"></a>Vytvoření webové aplikace

[!INCLUDE [Create web app](../../../includes/app-service-web-create-web-app-php-linux-no-h.md)] 

### <a name="configure-database-settings"></a>Konfigurace nastavení databáze

Ve službě App Service můžete nastavit proměnné prostředí jako _nastavení aplikace_ pomocí příkazu [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set).

Následující příkaz nakonfiguruje nastavení aplikace `DB_HOST`, `DB_DATABASE`, `DB_USERNAME` a `DB_PASSWORD`. Nahraďte zástupné symboly _ &lt;>appname_ a _ &lt;>název mysql-server _.

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group myResourceGroup --settings DB_HOST="<mysql-server-name>.mysql.database.azure.com" DB_DATABASE="sampledb" DB_USERNAME="phpappuser@<mysql-server-name>" DB_PASSWORD="MySQLAzure2017" MYSQL_SSL="true"
```

Můžete použít metodu PHP [getenv](https://php.net/manual/en/function.getenv.php) pro [přístup k nastavení aplikace](configure-language-php.md#access-environment-variables). Laravel kód používá [env](https://laravel.com/docs/5.4/helpers#method-env) obálku `getenv`přes PHP . Například konfigurace MySQL v souboru _config/database.php_ vypadá podobně jako v následujícím kódu:

```php
'mysql' => [
    'driver'    => 'mysql',
    'host'      => env('DB_HOST', 'localhost'),
    'database'  => env('DB_DATABASE', 'forge'),
    'username'  => env('DB_USERNAME', 'forge'),
    'password'  => env('DB_PASSWORD', ''),
    ...
],
```

### <a name="configure-laravel-environment-variables"></a>Konfigurace proměnných prostředí Laravel

Laravel potřebuje ve službě App Service klíč aplikace. Můžete ho nakonfigurovat pomocí nastavení aplikace.

Pomocí příkazu `php artisan` vygenerujte nový klíč aplikace, aniž byste ho ukládali do souboru _.env_.

```bash
php artisan key:generate --show
```

Nastavte klíč aplikace v aplikaci App [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) Service pomocí příkazu. Nahraďte zástupné symboly _ &lt;appname>_ a _ &lt;outputofphpartisankey:generate>_.

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group myResourceGroup --settings APP_KEY="<output_of_php_artisan_key:generate>" APP_DEBUG="true"
```

`APP_DEBUG="true"`říká Laravel vrátit informace o ladění, když nasazené aplikace narazí na chyby. Při spouštění produkční aplikace ho nastavte na `false`, což je bezpečnější.

### <a name="push-to-azure-from-git"></a>Přenos z Gitu do Azure

Přidejte vzdálené úložiště Azure do místního úložiště Gitu.

```bash
git remote add azure <paste_copied_url_here>
```

Nasdílením změn do vzdáleného prostředí Azure nasaďte aplikaci PHP. Zobrazí se výzva k zadání hesla, které jste zadali dříve v rámci vytváření uživatele nasazení.

```bash
git push azure master
```

Během nasazení bude služba Azure App Service hlásit Gitu průběh nasazení.

```bash
Counting objects: 3, done.
Delta compression using up to 8 threads.
Compressing objects: 100% (3/3), done.
Writing objects: 100% (3/3), 291 bytes | 0 bytes/s, done.
Total 3 (delta 2), reused 0 (delta 0)
remote: Updating branch 'master'.
remote: Updating submodules.
remote: Preparing deployment for commit id 'a5e076db9c'.
remote: Running custom deployment command...
remote: Running deployment command...
...
< Output has been truncated for readability >
```

<!-- > [!NOTE]
> You may notice that the deployment process installs [Composer](https://getcomposer.org/) packages at the end. App Service does not run these automations during default deployment, so this sample repository has three additional files in its root directory to enable it:
>
> - `.deployment` - This file tells App Service to run `bash deploy.sh` as the custom deployment script.
> - `deploy.sh` - The custom deployment script. If you review the file, you will see that it runs `php composer.phar install` after `npm install`.
> - `composer.phar` - The Composer package manager.
>
> You can use this approach to add any step to your Git-based deployment to App Service. For more information, see [Run Composer](configure-language-php.md#run-composer). -->
> 

### <a name="browse-to-the-azure-app"></a>Přejděte do aplikace Azure

Přejděte na adresu `http://<app-name>.azurewebsites.net` a přidejte do seznamu několik úkolů.

![Aplikace PHP spuštěná ve službě Azure App Service](./media/tutorial-php-mysql-app/php-mysql-in-azure.png)

Blahopřejeme! Teď máte ve službě Azure App Service spuštěnou aplikaci PHP řízenou daty.

## <a name="update-model-locally-and-redeploy"></a>Místní aktualizace modelu a opětovné nasazení

V tomto kroku provedete jednoduchou změnu datového modelu `task` a webové aplikace a potom tuto aktualizaci publikujete v Azure.

Pro scénář úkolů upravíte aplikaci tak, abyste mohli úkol označit jako dokončený.

### <a name="add-a-column"></a>Přidání sloupce

V terminálu přejděte do kořenového adresáře úložiště Git.

Vygenerujte novou migraci databáze pro tabulku `tasks`:

```bash
php artisan make:migration add_complete_column --table=tasks
```

Tento příkaz zobrazí název generovaného souboru migrace. Vyhledejte tento soubor v adresáři _database/migrations_ a otevřete ho.

Nahraďte metodu `up` následujícím kódem:

```php
public function up()
{
    Schema::table('tasks', function (Blueprint $table) {
        $table->boolean('complete')->default(False);
    });
}
```

Předchozí kód přidá do tabulky `tasks` logický sloupec `complete`.

Pro akci vrácení zpět nahraďte metodu `down` následujícím kódem:

```php
public function down()
{
    Schema::table('tasks', function (Blueprint $table) {
        $table->dropColumn('complete');
    });
}
```

V terminálu spusťte migrace databází Laravel, aby se změna provedla v místní databázi.

```bash
php artisan migrate
```

Na základě [konvence pojmenování Laravel](https://laravel.com/docs/5.4/eloquent#defining-models) model `Task` (viz _app/Task.php_) ve výchozím nastavení provádí mapování na tabulku `tasks`.

### <a name="update-application-logic"></a>Aktualizace logiky aplikace

Otevřete soubor *routes/web.php*. V něm aplikace definuje své trasy a obchodní logiku.

Na konec souboru přidejte trasu s následujícím kódem:

```php
/**
 * Toggle Task completeness
 */
Route::post('/task/{id}', function ($id) {
    error_log('INFO: post /task/'.$id);
    $task = Task::findOrFail($id);

    $task->complete = !$task->complete;
    $task->save();

    return redirect('/');
});
```

Předchozí kód provede jednoduchou aktualizaci datového modelu tím, že přepne hodnotu položky `complete`.

### <a name="update-the-view"></a>Aktualizace zobrazení

Otevřete soubor *resources/views/tasks.blade.php*. Vyhledejte počáteční značku `<tr>` a nahraďte ji:

```html
<tr class="{{ $task->complete ? 'success' : 'active' }}" >
```

Předchozí kód změní barvu řádku v závislosti na tom, jestli je úkol dokončený.

Na dalším řádku je následující kód:

```html
<td class="table-text"><div>{{ $task->name }}</div></td>
```

Celý řádek nahraďte následujícím kódem:

```html
<td>
    <form action="{{ url('task/'.$task->id) }}" method="POST">
        {{ csrf_field() }}

        <button type="submit" class="btn btn-xs">
            <i class="fa {{$task->complete ? 'fa-check-square-o' : 'fa-square-o'}}"></i>
        </button>
        {{ $task->name }}
    </form>
</td>
```

Předchozí kód přidá tlačítko Odeslat odkazující na trasy, které jste definovali předtím.

### <a name="test-the-changes-locally"></a>Místní test provedených změn

Z kořenového adresáře úložiště Git spusťte vývojový server.

```bash
php artisan serve
```

Pokud chcete vidět změnu stavu úkolu, přejděte na adresu `http://localhost:8000` a zaškrtněte políčko.

![U úkolu přibylo zaškrtávací políčko](./media/tutorial-php-mysql-app/complete-checkbox.png)

Pokud chcete zastavit PHP, zadejte v terminálu `Ctrl + C`.

### <a name="publish-changes-to-azure"></a>Publikování změn v Azure

V terminálu spusťte migrace databází Laravel s produkčním připojovacím řetězcem, aby se provedla změna v databázi Azure.

```bash
php artisan migrate --env=production --force
```

Potvrďte všechny změny v Gitu a potom odešlete změny kódu do Azure.

```bash
git add .
git commit -m "added complete checkbox"
git push azure master
```

Po `git push` dokončení přejděte do aplikace Azure a otestujte nové funkce.

![Změny modelu a databáze publikované v Azure](media/tutorial-php-mysql-app/complete-checkbox-published.png)

Pokud jste přidali nějaké úkoly, zůstanou v databázi. Aktualizace schématu dat nechávají existující data netknutá.

## <a name="stream-diagnostic-logs"></a>Streamování diagnostických protokolů

[!INCLUDE [Access diagnostic logs](../../../includes/app-service-web-logs-access-no-h.md)]

## <a name="manage-the-azure-app"></a>Správa aplikace Azure

Přejděte na [portál Azure](https://portal.azure.com) a spravujte aplikaci, kterou jste vytvořili.

V levé nabídce klikněte na **App Services**a potom klikněte na název aplikace Azure.

![Přechod do aplikace Azure na portálu](./media/tutorial-php-mysql-app/access-portal.png)

Zobrazí se stránka Přehled aplikace. Tady můžete provádět základní úkoly správy, jako je zastavení, spuštění, restartování, procházení a odstranění.

Levá nabídka obsahuje stránky pro konfiguraci vaší aplikace.

![Stránka služby App Service na webu Azure Portal](./media/tutorial-php-mysql-app/web-app-blade.png)

[!INCLUDE [cli-samples-clean-up](../../../includes/cli-samples-clean-up.md)]

<a name="next"></a>

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili:

> [!div class="checklist"]
> * Vytvořit databázi MySQL v Azure
> * Připojit k MySQL aplikaci PHP
> * Nasadit aplikaci do Azure
> * Aktualizovat datový model a znovu nasadit aplikaci
> * Streamovat diagnostické protokoly z Azure
> * Spravovat aplikaci na webu Azure Portal

Přejdete k dalšímu kurzu, kde se dozvíte, jak namapovat vlastní název DNS do aplikace.

> [!div class="nextstepaction"]
> [Kurz: Mapování vlastního názvu DNS do aplikace](../app-service-web-tutorial-custom-domain.md)

Nebo se podívejte na další zdroje:

> [!div class="nextstepaction"]
> [Konfigurace aplikace PHP](configure-language-php.md)