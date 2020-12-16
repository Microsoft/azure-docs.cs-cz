---
title: 'Kurz: sestavení aplikace PHP (Laravel) pomocí Azure Database for MySQL flexibilního serveru'
description: V tomto kurzu se dozvíte, jak vytvořit aplikaci PHP s flexibilním serverem.
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: tutorial
ms.devlang: php
ms.date: 9/21/2020
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 93e605cb20d593750100ec8e340a7ad74c4dd385
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/16/2020
ms.locfileid: "97587889"
---
# <a name="tutorial-build-a-php-laravel-and-mysql-flexible-server-preview-app-in-azure-app-service"></a>Kurz: Vytvoření aplikace PHP (Laravel) a MySQL flexibilního serveru (Preview) v Azure App Service


:::image type="content" source="media/tutorial-php-database-app/complete-checkbox-published.png" alt-text="Webová aplikace PHP v Azure s flexibilním serverem":::

[Azure App Service](../../app-service/overview.md) poskytuje vysoce škálovatelnou službu s automatickými opravami pro hostování webů pomocí operačního systému Linux. V tomto kurzu se dozvíte, jak v Azure vytvořit aplikaci PHP a připojit ji k databázi MySQL. Až budete hotovi, budete mít aplikaci [Laravel](https://laravel.com/) běžící na Azure App Service v systému Linux.

V tomto kurzu se naučíte:
> [!div class="checklist"]
> * Nastavení aplikace PHP (Laravel) s místním MySQL
> * Vytvoření flexibilního serveru MySQL (Preview)
> * Připojení aplikace PHP k MySQL flexibilnímu serveru (Preview)
> * Nasazení aplikace do Azure App Service
> * Aktualizovat datový model a znovu nasadit aplikaci
> * Spravovat aplikaci na webu Azure Portal

Pokud ještě nemáte [předplatné Azure](../../guides/developer/azure-developer-guide.md#understanding-accounts-subscriptions-and-billing), vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) před tím, než začnete.

## <a name="prerequisites"></a>Předpoklady

Pro absolvování tohoto kurzu potřebujete:

1. [Nainstalovat Git](https://git-scm.com/).
2. [Nainstalovat PHP 5.6.4 nebo novější](https://php.net/downloads.php)
3. [Nainstalovat Composer](https://getcomposer.org/doc/00-intro.md)
4. Povolit následující rozšíření PHP vyžadovaná aplikací Laravel: OpenSSL, PDO-MySQL, Mbstring, Tokenizer a XML
5. [Nainstalovat a spustit MySQL](https://dev.mysql.com/doc/refman/5.7/en/installing.html).

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

V okně terminálu přejděte do prázdného adresáře, kde můžete klonovat ukázkovou aplikaci.  Ukázkové úložiště naklonujete spuštěním následujícího příkazu.

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

V kořenovém adresáři úložiště vytvořte soubor *.env*. Zkopírujte do souboru *.env* následující proměnné. Zástupný text _&lt;>root_password_ nahraďte heslem kořenového uživatele MySQL.

```txt
APP_ENV=local
APP_DEBUG=true
APP_KEY=

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

:::image type="content" source="media/tutorial-php-database-app/mysql-connect-success.png" alt-text="Úspěšné připojení aplikace PHP k MySQL":::

Pokud chcete zastavit PHP, zadejte v terminálu `Ctrl + C`.

## <a name="create-a-mysql-flexible-server-preview"></a>Vytvoření flexibilního serveru MySQL (Preview)
V tomto kroku vytvoříte databázi MySQL v [Azure Database for MySQL flexibilním serveru](../index.yml) , který je ve verzi Public Preview. Později nakonfigurujete aplikaci PHP pro připojení k této databázi. V [Azure Cloud Shell](../../cloud-shell/overview.md)vytvořte pomocí [`az flexible-server create`](/cli/azure/mysql/server#az-mysql-flexible-server-create) příkazu Server.

```azurecli-interactive
az mysql flexible-server create  --resource-group myResourceGroup --public-access <IP-Address>
```

> [!IMPORTANT]
> - Poznamenejte si **servername** a **připojovací řetězec** , abyste ho mohli použít v dalším kroku k připojení a spuštění migrace dat Laravel.
> - U argumentu **IP-Address**  zadejte IP adresu klientského počítače. Server je při vytvoření uzamčen a je nutné povolit přístup ke klientskému počítači pro místní správu serveru.

### <a name="configure-server-firewall-to-allow-web-app-to-connect-to-the-server"></a>Konfigurace brány firewall serveru tak, aby umožňovala webové aplikaci připojit se k serveru

V Cloud Shell vytvořte pravidlo brány firewall pro server MySQL, abyste mohli pomocí příkazu AZ MySQL server firewall-Rule Create vytvořit klientská připojení. Pokud jsou počáteční IP adresa i koncová IP adresa nastavené na ```0.0.0.0``` , je brána firewall otevřená jenom pro jiné služby Azure, které nemají statickou IP adresu pro připojení k serveru.

```azurecli
az mysql flexible-server firewall-rule create --name allanyAzureIPs --server <mysql-server-name> --resource-group myResourceGroup --start-ip-address 0.0.0.0 --end-ip-address 0.0.0.0
```

### <a name="connect-to-production-mysql-server-locally"></a>Místní připojení k produkčnímu serveru MySQL

V místním okně terminálu se připojte k serveru MySQL v Azure. Použijte hodnotu, kterou jste zadali dříve pro ```<admin-user>``` a ```<mysql-server-name>``` . Po zobrazení výzvy k zadání hesla použijte heslo, které jste zadali při vytváření databáze v Azure.

```bash
mysql -u <admin-user> -h <mysql-server-name>.mysql.database.azure.com -P 3306 -p
```

### <a name="create-a-production-database"></a>Vytvoření produkční databáze

Na příkazovém řádku `mysql` vytvořte databázi.

```sql
CREATE DATABASE sampledb;
```

### <a name="create-a-user-with-permissions"></a>Vytvoření uživatele s oprávněními

Vytvořte uživatele databáze se jménem _phpappuser_ a přidělte mu všechna oprávnění k databázi `sampledb`. Pro zjednodušení tohoto kurzu použijte jako heslo _MySQLAzure2020_ .

```sql
CREATE USER 'phpappuser' IDENTIFIED BY 'MySQLAzure2020';
GRANT ALL PRIVILEGES ON sampledb.* TO 'phpappuser';
```

Ukončete připojení k serveru zadáním příkazu `quit`.

```sql
quit
```

## <a name="connect-app-to-mysql-flexible-server"></a>Připojení aplikace k serveru MySQL Flexible Server

V tomto kroku připojíte aplikaci PHP k databázi MySQL, kterou jste vytvořili v Azure Database for MySQL.

<a name="devconfig"></a>

### <a name="configure-the-database-connection"></a>Konfigurace připojení k databázi

V kořenovém adresáři úložiště vytvořte soubor _.env.production_ a zkopírujte do něj následující proměnné. Nahraďte zástupný symbol _&lt; MySQL-Server-Name>_ v *DB_HOST* i *DB_USERNAME*.

```
APP_ENV=production
APP_DEBUG=true
APP_KEY=

DB_CONNECTION=mysql
DB_HOST=<mysql-server-name>.mysql.database.azure.com
DB_DATABASE=sampledb
DB_USERNAME=phpappuser
DB_PASSWORD=MySQLAzure2017
MYSQL_SSL=true
```

Uložte změny.

> [!TIP]
> Za účelem zabezpečení informací o vašem připojení k MySQL je tento soubor již vyloučený z úložiště Git (viz soubor _.gitignore_ v kořenovém adresáři úložiště). Později se dozvíte, jak ve službě App Service nakonfigurovat proměnné prostředí pro připojení k vaší databázi v Azure Database for MySQL. S použitím proměnných prostředí nepotřebujete soubor *.env* ve službě App Service.
>

### <a name="configure-tlsssl-certificate"></a>Konfigurace certifikátu TLS/SSL

Ve výchozím nastavení vynutila služba MySQL flexibilní Server připojení TLS od klientů. Pokud se chcete připojit k databázi MySQL v Azure, musíte použít certifikát [ _. pem_ dodaný Azure Database for MySQLm flexibilním serverem](https://dl.cacerts.digicert.com/DigiCertGlobalRootCA.crt.pem). Stáhněte si [Tento certifikát](https://dl.cacerts.digicert.com/DigiCertGlobalRootCA.crt.pem)a umístěte ho do složky **SSL** v místní kopii ukázkového úložiště aplikace.

Otevřete soubor _config/database.php_ a do `connections.mysql` přidejte parametry `sslmode` a `options`, jak je znázorněno v následujícím kódu.

```php
'mysql' => [
    ...
    'sslmode' => env('DB_SSLMODE', 'prefer'),
    'options' => (env('MYSQL_SSL') && extension_loaded('pdo_mysql')) ? [
        PDO::MYSQL_ATTR_SSL_KEY    => '/ssl/DigiCertGlobalRootCA.crt.pem',
    ] : []
],
```

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

:::image type="content" source="media/tutorial-php-database-app/mysql-connect-success.png" alt-text="Úspěšné připojení aplikace PHP k Azure Database for MySQL":::

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

### <a name="configure-a-deployment-user"></a>Konfigurace uživatele nasazení

FTP a místní Git se můžou nasadit do webové aplikace Azure pomocí uživatele nasazení. Jakmile nakonfigurujete uživatele nasazení, můžete ho použít pro všechna nasazení Azure. Uživatelské jméno a heslo nasazení na úrovni účtu se liší od přihlašovacích údajů předplatného Azure.

Pokud chcete nakonfigurovat uživatele nasazení, spusťte v Azure Cloud Shell příkaz [AZ WebApp Deployment User set](/cli/azure/webapp/deployment/user#az-webapp-deployment-user-set) . Místo uživatelského jména a hesla pro nasazení nahraďte _&lt; uživatelské jméno>_ a _&lt; heslo>_ .

Uživatelské jméno musí být v rámci Azure jedinečné a pro místní nabízená oznámení Git nesmí obsahovat symbol @.
Heslo musí mít délku alespoň osm znaků a dva z následujících tří prvků: písmena, číslice a symboly.

```bash
az appservice plan create --name myAppServicePlan --resource-group myResourceGroup --sku F1 --is-linux
```

Výstup JSON zobrazuje heslo jako null. Pokud se zobrazí zpráva "konflikt". Podrobnosti: 409 chyba, změňte uživatelské jméno. Pokud se zobrazí zpráva o špatném požadavku. Podrobnosti: 400 chyba, použití silnějšího hesla. **Poznamenejte si uživatelské jméno a heslo, které chcete použít k nasazení webových aplikací.**

### <a name="create-an-app-service-plan"></a>Vytvoření plánu služby App Service

V Cloud Shell ve skupině prostředků vytvořte App Service plán pomocí příkazu [AZ AppService Plan Create](/cli/azure/appservice/plan#az-appservice-plan-create) . Následující příklad vytvoří plán App Service s názvem myAppServicePlan v bezplatné cenové úrovni (--SKU F1) a v kontejneru Linux (--is-Linux).

AZ AppService Plan Create--Name myAppServicePlan--Resource-Group myResourceGroup--SKU F1--is-Linux

<a name="create"></a>

### <a name="create-a-web-app"></a>Vytvoření webové aplikace

Vytvořte [webovou aplikaci](../../app-service/overview.md#app-service-on-linux) v plánu myAppServicePlan App Service.

V Cloud Shell můžete použít příkaz [AZ WebApp Create](/cli/azure/webapp#az-webapp-create) . V následujícím příkladu nahraďte _&lt; název aplikace>_ globálně jedinečným názvem aplikace (platné znaky jsou `a-z` , `0-9` a `-` ). Modul runtime je nastavený na `PHP|7.0`. Pokud chcete zobrazit všechny podporované moduly runtime, spusťte příkaz [AZ WebApp list-runtimes--Linux](/cli/azure/webapp#az-webapp-list-runtimes).

```bash
az webapp create --resource-group myResourceGroup --plan myAppServicePlan --name <app-name> --runtime "PHP|7.3" --deployment-local-git
```

Po vytvoření webové aplikace Azure CLI zobrazí výstup podobný následujícímu příkladu:

```
Local git is configured with url of 'https://<username>@<app-name>.scm.azurewebsites.net/<app-name>.git'
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "cloningInfo": null,
  "containerSize": 0,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "<app-name>.azurewebsites.net",
  "deploymentLocalGitUrl": "https://<username>@<app-name>.scm.azurewebsites.net/<app-name>.git",
  "enabled": true,
  < JSON data removed for brevity. >
}
```

Vytvořili jste novou prázdnou webovou aplikaci s povoleným nasazením Gitu.

> [!NOTE]
> Adresa URL vzdáleného úložiště Git se zobrazuje ve vlastnosti deploymentLocalGitUrl ve formátu https:// <username> @ <App-name>. scm.azurewebsites.net/<App-name>. Git. Tuto adresu URL si uložte, protože ji budete potřebovat později.

### <a name="configure-database-settings"></a>Konfigurace nastavení databáze

Ve službě App Service můžete nastavit proměnné prostředí jako _nastavení aplikace_ pomocí příkazu [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings#az-webapp-config-appsettings-set).

Následující příkaz nakonfiguruje nastavení aplikace `DB_HOST`, `DB_DATABASE`, `DB_USERNAME` a `DB_PASSWORD`. Nahraďte zástupné symboly _&lt; název aplikace>_ a _&lt; mysql-Server-Name>_.

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group myResourceGroup --settings DB_HOST="<mysql-server-name>.mysql.database.azure.com" DB_DATABASE="sampledb" DB_USERNAME="phpappuser" DB_PASSWORD="MySQLAzure2017" MYSQL_SSL="true"
```

Pro přístup k nastavení můžete použít metodu PHP [getenv](https://www.php.net/manual/en/function.getenv.php). Kód Laravel používá pro metodu PHP `getenv` obálku [env](https://laravel.com/docs/5.4/helpers#method-env). Například konfigurace MySQL v souboru _config/database.php_ vypadá podobně jako v následujícím kódu:

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

V okně místního terminálu pomocí příkazu `php artisan` vygenerujte nový klíč aplikace, aniž byste ho ukládali do souboru _.env_.

```bash
php artisan key:generate --show
```

V Cloud Shell nastavte klíč aplikace v aplikaci App Service pomocí [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings#az-webapp-config-appsettings-set) příkazu. Nahraďte zástupné symboly _&lt; název aplikace>_ a _&lt; outputofphpartisankey: Generate>_.

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group myResourceGroup --settings APP_KEY="<output_of_php_artisan_key:generate>" APP_DEBUG="true"
```

`APP_DEBUG="true"` oznamuje Laravel, aby vracel ladicí informace, když nasazená aplikace zaznamená chyby. Při spouštění produkční aplikace ho nastavte na `false`, což je bezpečnější.

### <a name="set-the-virtual-application-path"></a>Nastavení cesty virtuální aplikace

[Životní cyklus aplikace Laravel](https://laravel.com/docs/5.4/lifecycle) začíná ve _veřejném_ adresáři místo v kořenovém adresáři aplikace. Výchozí image Dockeru s PHP pro službu App Service používá Apache a neumožňuje přizpůsobení `DocumentRoot` pro Laravel. Pomocí souboru `.htaccess` však můžete přepsat směrování všech požadavků do adresáře _/public_ místo kořenového adresáře. Kořenový adresář úložiště pro tyto účely již soubor `.htaccess` obsahuje. S tímto souborem je vaše aplikace Laravel připravená k nasazení.

Další informace najdete v tématu [Změna kořene lokality](../../app-service/configure-language-php.md?pivots=platform-linux#change-site-root).

### <a name="push-to-azure-from-git"></a>Přenos z Gitu do Azure

Zpět v okně místního terminálu přidejte vzdálené úložiště Azure do místního úložiště Git. Nahraďte _&lt; deploymentLocalGitUrl-from-Create-Step>_ adresou URL vzdáleného úložiště Git, kterou jste uložili v části [Vytvoření webové aplikace](#create-a-web-app).

```bash
git remote add azure <deploymentLocalGitUrl-from-create-step>
```

Nasaďte aplikaci do vzdáleného úložiště Azure pomocí následujícího příkazu. Když vám správce přihlašovacích údajů Git vyzve k zadání přihlašovacích údajů, ujistěte se, že jste zadali přihlašovací údaje, které jste vytvořili v části **Konfigurace uživatele nasazení**, a ne přihlašovací údaje, které používáte k přihlášení k Azure Portal.

```bash
git push azure main
```

Spuštění tohoto příkazu může trvat několik minut. Při spuštění příkaz zobrazí podobné informace jako v následujícím příkladu:

<pre>
Counting objects: 3, done.
Delta compression using up to 8 threads.
Compressing objects: 100% (3/3), done.
Writing objects: 100% (3/3), 291 bytes | 0 bytes/s, done.
Total 3 (delta 2), reused 0 (delta 0)
remote: Updating branch 'main'.
remote: Updating submodules.
remote: Preparing deployment for commit id 'a5e076db9c'.
remote: Running custom deployment command...
remote: Running deployment command...
...
&lt; Output has been truncated for readability &gt;
</pre>

### <a name="browse-to-the-azure-app"></a>Přejít k aplikaci Azure

Přejděte na adresu `http://<app-name>.azurewebsites.net` a přidejte do seznamu několik úkolů.

:::image type="content" source="media/tutorial-php-database-app/php-mysql-in-azure.png" alt-text="Webová aplikace PHP v Azure":::

Blahopřejeme! Teď máte ve službě Azure App Service spuštěnou aplikaci PHP řízenou daty.

## <a name="update-model-locally-and-redeploy"></a>Místní aktualizace modelu a opětovné nasazení

V tomto kroku provedete jednoduchou změnu datového modelu `task` a webové aplikace a potom tuto aktualizaci publikujete v Azure.

Pro scénář úkolů upravíte aplikaci tak, abyste mohli úkol označit jako dokončený.

### <a name="add-a-column"></a>Přidání sloupce

V okně místního terminálu přejděte do kořenového adresáře úložiště Gitu.

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

V okně místního terminálu spusťte migrace databáze Laravel, aby se změna provedla v místní databázi.

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

V okně místního terminálu spusťte vývojový server z kořenového adresáře úložiště Git.

```bash
php artisan serve
```

Pokud chcete vidět změnu stavu úkolu, přejděte na adresu `http://localhost:8000` a zaškrtněte políčko.

:::image type="content" source="media/tutorial-php-database-app/complete-checkbox.png" alt-text="U úkolu přibylo zaškrtávací políčko":::

Pokud chcete zastavit PHP, zadejte v terminálu `Ctrl + C`.

### <a name="publish-changes-to-azure"></a>Publikování změn v Azure

V okně místního terminálu spusťte migrace databází Laravel s produkčním připojovacím řetězcem, aby se provedla změna v databázi Azure.

```bash
php artisan migrate --env=production --force
```

Potvrďte všechny změny v Gitu a potom odešlete změny kódu do Azure.

```bash
git add .
git commit -m "added complete checkbox"
git push azure main
```

Až `git push` to bude hotové, přejděte do aplikace Azure a vyzkoušejte nové funkce.

:::image type="content" source="media/tutorial-php-database-app/complete-checkbox-published.png" alt-text="Změny modelu a databáze publikované v Azure":::

Pokud jste přidali nějaké úkoly, zůstanou v databázi. Aktualizace schématu dat nechávají existující data netknutá.

## <a name="clean-up-resources"></a>Vyčištění prostředků
V předchozích krocích jste vytvořili prostředky Azure ve skupině prostředků. Pokud předpokládáte, že už tyto prostředky nebudete potřebovat, odstraňte skupinu prostředků spuštěním následujícího příkazu ve službě Cloud Shell:

```bash
az group delete --name myResourceGroup
```

<a name="next"></a>

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Správa prostředků v Azure Portal](../../azure-resource-manager/management/manage-resources-portal.md) <br/>
> [!div class="nextstepaction"]
> [Správa serveru](how-to-manage-server-cli.md)
