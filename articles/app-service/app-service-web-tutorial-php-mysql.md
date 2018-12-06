---
title: Sestavení webové aplikace PHP s připojením k databázi MySQL v Azure | Microsoft Docs
description: Naučte se v Azure zprovoznit aplikaci PHP s připojením k databázi MySQL v Azure.
services: app-service\web
documentationcenter: php
author: cephalin
manager: erikre
editor: ''
ms.assetid: 14feb4f3-5095-496e-9a40-690e1414bd73
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: php
ms.topic: tutorial
ms.date: 11/15/2018
ms.author: cephalin
ms.custom: mvc
ms.openlocfilehash: e5574eee1ac553151f073fe4e398a84c596c55f2
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/06/2018
ms.locfileid: "52968216"
---
# <a name="tutorial-build-a-php-and-mysql-web-app-in-azure"></a>Kurz: Sestavení webové aplikace PHP s připojením k databázi MySQL v Azure

> [!NOTE]
> Tento článek nasadí aplikaci do služby App Service ve Windows. Nasazení do služby App Service v _Linuxu_ je popsané v tématu [Vytvoření webové aplikace PHP s připojením k databázi MySQL ve službě Azure App Service v Linuxu](./containers/tutorial-php-mysql-app.md).
>

[Azure Web Apps](app-service-web-overview.md) je vysoce škálovatelná služba s automatickými opravami pro hostování webů. Tento kurz předvádí postup při vytváření webové aplikace PHP v Azure a jejím připojení k databázi MySQL. Po dokončení budete mít ve službě Azure App Service Web Apps spuštěnou aplikaci [Laravel](https://laravel.com/).

![Aplikace PHP spuštěná ve službě Azure App Service](./media/app-service-web-tutorial-php-mysql/complete-checkbox-published.png)

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvořit databázi MySQL v Azure
> * Připojit k MySQL aplikaci PHP
> * Nasadit aplikaci do Azure
> * Aktualizovat datový model a znovu nasadit aplikaci
> * Streamovat diagnostické protokoly z Azure
> * Spravovat aplikaci na webu Azure Portal

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Požadavky

Pro absolvování tohoto kurzu potřebujete:

* [Nainstalovat Git](https://git-scm.com/).
* [Nainstalovat PHP 5.6.4 nebo novější](https://php.net/downloads.php)
* [Nainstalovat Composer](https://getcomposer.org/doc/00-intro.md)
* Povolit následující rozšíření PHP vyžadovaná aplikací Laravel: OpenSSL, PDO-MySQL, Mbstring, Tokenizer a XML
* [Nainstalovat a spustit MySQL](https://dev.mysql.com/doc/refman/5.7/en/installing.html) 

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

V kořenovém adresáři úložiště vytvořte textový soubor s názvem *.env*. Zkopírujte do souboru *.env* následující proměnné. Nahraďte zástupný text _&lt;root_password>_ heslem uživatele root databáze MySQL.

```
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

![Úspěšné připojení aplikace PHP k MySQL](./media/app-service-web-tutorial-php-mysql/mysql-connect-success.png)

Pokud chcete server PHP zastavit, zadejte do terminálu `Ctrl + C`.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-mysql-in-azure"></a>Vytvoření databáze MySQL v Azure

V tomto kroku vytvoříte v [Azure Database for MySQL](/azure/mysql) databázi MySQL. Později nakonfigurujete aplikaci PHP pro připojení k této databázi.

### <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

[!INCLUDE [Create resource group](../../includes/app-service-web-create-resource-group-no-h.md)] 

### <a name="create-a-mysql-server"></a>Vytvoření serveru MySQL

Ve službě Cloud Shell pomocí příkazu [`az mysql server create`](/cli/azure/mysql/server?view=azure-cli-latest#az-mysql-server-create) vytvořte server v Azure Database for MySQL.

V následujícím příkazu nahraďte zástupnou hodnotu *\<mysql_server_name>* jedinečným názvem serveru, zástupnou hodnotu *\<admin_user>* uživatelským jménem a zástupnou hodnotu *\<admin_password>* heslem. Název serveru se používá jako součást koncového bodu MySQL (`https://<mysql_server_name>.mysql.database.azure.com`), takže musí být jedinečný v rámci všech serverů v Azure.

```azurecli-interactive
az mysql server create --resource-group myResourceGroup --name <mysql_server_name> --location "West Europe" --admin-user <admin_user> --admin-password <admin_password> --sku-name B_Gen5_1
```

> [!NOTE]
> Vzhledem k tomu, že se v tomto kurzu používá několik sad přihlašovacích údajů, jsou položky `--admin-user` a `--admin-password` pro jednoduchost nastavené na fiktivní hodnoty. V produkčním prostředí se při vybírání vhodného uživatelského jména a hesla pr server MySQL v Azure řiďte osvědčenými postupy v oblasti zabezpečení.
>
>

Po vytvoření serveru MySQL se v Azure CLI zobrazí podobné informace jako v následujícím příkladu:

```json
{
  "location": "westeurope",
  "name": "<mysql_server_name>",
  "resourceGroup": "myResourceGroup",
  "sku": {
    "additionalProperties": {},
    "capacity": 1,
    "family": "Gen5",
    "name": "B_Gen5_1",
    "size": null,
    "tier": "GeneralPurpose"
  },
  "sslEnforcement": "Enabled",
  ...   +  
  -  < Output has been truncated for readability >
}
```

### <a name="configure-server-firewall"></a>Konfigurace brány firewall serveru

Ve službě Cloud Shell pomocí příkazu [`az mysql server firewall-rule create`](/cli/azure/mysql/server/firewall-rule?view=azure-cli-latest#az-mysql-server-firewall-rule-create) vytvořte pro svůj server MySQL pravidlo brány firewall umožňující klientská připojení. Pokud je jako počáteční i koncová adresa IP nastavená hodnota 0.0.0.0, je brána firewall otevřená jen pro ostatní prostředky Azure. 

```azurecli-interactive
az mysql server firewall-rule create --name allAzureIPs --server <mysql_server_name> --resource-group myResourceGroup --start-ip-address 0.0.0.0 --end-ip-address 0.0.0.0
```

> [!TIP] 
> Pravidlo brány firewall můžete dál omezit [použitím jenom odchozích IP adres, které vaše aplikace používá](app-service-ip-addresses.md#find-outbound-ips).
>

Spusťte v Cloud Shellu tento příkaz znovu, ale nahraďte *\<your_ip_address>* [vaší místní IP adresou IPv4](http://www.whatsmyip.org/), abyste měli přístup k databázi.

```azurecli-interactive
az mysql server firewall-rule create --name AllowLocalClient --server <mysql_server_name> --resource-group myResourceGroup --start-ip-address=<your_ip_address> --end-ip-address=<your_ip_address>
```

### <a name="connect-to-production-mysql-server-locally"></a>Místní připojení k produkčnímu serveru MySQL

V místním okně terminálu se připojte k serveru MySQL v Azure. U položky _&lt;mysql_server_name>_ použijte hodnotu, kterou jste zadali předtím. Po zobrazení výzvy k zadání hesla použijte heslo, které jste zadali při vytváření databáze v Azure.

```bash
mysql -u <admin_user>@<mysql_server_name> -h <mysql_server_name>.mysql.database.azure.com -P 3306 -p
```

### <a name="create-a-production-database"></a>Vytvoření produkční databáze

Na příkazovém řádku `mysql` vytvořte databázi.

```sql
CREATE DATABASE sampledb;
```

### <a name="create-a-user-with-permissions"></a>Vytvoření uživatele s oprávněními

Vytvořte uživatele databáze se jménem _phpappuser_ a přidělte mu všechna oprávnění k databázi `sampledb`. Pro jednoduchost v rámci kurzu znovu použijte heslo _MySQLAzure2017_.

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

V kořenovém adresáři úložiště vytvořte soubor _.env.production_ a zkopírujte do něj následující proměnné. Nahraďte zástupný text _&lt;mysql_server_name >_ u položek *DB_HOST* a *DB_USERNAME*.

```
APP_ENV=production
APP_DEBUG=true
APP_KEY=

DB_CONNECTION=mysql
DB_HOST=<mysql_server_name>.mysql.database.azure.com
DB_DATABASE=sampledb
DB_USERNAME=phpappuser@<mysql_server_name>
DB_PASSWORD=MySQLAzure2017
MYSQL_SSL=true
```

Uložte změny.

> [!TIP]
> Za účelem zabezpečení informací o vašem připojení k MySQL je už tento soubor vyloučený z úložiště Git (viz soubor _.gitignore_ v kořenovém adresáři úložiště). Později se dozvíte, jak ve službě App Service nakonfigurovat proměnné prostředí pro připojení k vaší databázi v Azure Database for MySQL. S použitím proměnných prostředí nepotřebujete soubor *.env* ve službě App Service.
>

### <a name="configure-ssl-certificate"></a>Konfigurace certifikátu SSL

Ve výchozím nastavení vynucuje Azure Database for MySQL od klientů připojení SSL. Pokud se chcete připojit ke své databázi MySQL v Azure, je potřeba použít certifikát [_.pem_, který poskytuje Azure Database for MySQL](../mysql/howto-configure-ssl.md).

Otevřete soubor _config/database.php_ a do `connections.mysql` přidejte parametry `sslmode` a `options`, jak je znázorněno v následujícím kódu.

```php
'mysql' => [
    ...
    'sslmode' => env('DB_SSLMODE', 'prefer'),
    'options' => (env('MYSQL_SSL')) ? [
        PDO::MYSQL_ATTR_SSL_KEY    => '/ssl/BaltimoreCyberTrustRoot.crt.pem', 
    ] : []
],
```

V tomto kurzu je certifikát `BaltimoreCyberTrustRoot.crt.pem` pro jednoduchost součástí úložiště. 

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

![Úspěšné připojení aplikace PHP k Azure Database for MySQL](./media/app-service-web-tutorial-php-mysql/mysql-connect-success.png)

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

[!INCLUDE [Configure deployment user](../../includes/configure-deployment-user-no-h.md)]

### <a name="create-an-app-service-plan"></a>Vytvoření plánu služby App Service

[!INCLUDE [Create app service plan no h](../../includes/app-service-web-create-app-service-plan-no-h.md)]

<a name="create"></a>
### <a name="create-a-web-app"></a>Vytvoření webové aplikace

[!INCLUDE [Create web app no h](../../includes/app-service-web-create-web-app-php-no-h.md)] 

### <a name="configure-database-settings"></a>Konfigurace nastavení databáze

Jak jsme už uvedli, k databázi MySQL Azure se můžete připojit pomocí proměnných prostředí ve službě App Service.

Ve službě Cloud Shell můžete nastavit proměnné prostředí jako _nastavení aplikace_ pomocí příkazu [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set).

Následující příkaz nakonfiguruje nastavení aplikace `DB_HOST`, `DB_DATABASE`, `DB_USERNAME` a `DB_PASSWORD`. Nahraďte zástupné texty _&lt;appname>_ a _&lt;mysql_server_name>_.

```azurecli-interactive
az webapp config appsettings set --name <app_name> --resource-group myResourceGroup --settings DB_HOST="<mysql_server_name>.mysql.database.azure.com" DB_DATABASE="sampledb" DB_USERNAME="phpappuser@<mysql_server_name>" DB_PASSWORD="MySQLAzure2017" MYSQL_SSL="true"
```

Pro přístup k nastavení můžete použít metodu PHP [getenv](http://www.php.net/manual/function.getenv.php). Kód Laravel používá pro metodu PHP `getenv` obálku [env](https://laravel.com/docs/5.4/helpers#method-env). Například konfigurace MySQL v souboru _config/database.php_ vypadá podobně jako v následujícím kódu:

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

Ve službě Cloud Shell pomocí příkazu [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) nastavte klíč aplikace ve webové aplikaci App Service. Nahraďte zástupné texty _&lt;appname>_ and _&lt;outputofphpartisankey:generate>_.

```azurecli-interactive
az webapp config appsettings set --name <app_name> --resource-group myResourceGroup --settings APP_KEY="<output_of_php_artisan_key:generate>" APP_DEBUG="true"
```

Atribut `APP_DEBUG="true"` dává Laravelu pokyn k vrácení informací o ladění v případě, že v nasazené webové aplikaci dojde k chybám. Při spouštění produkční aplikace ho nastavte na `false`, což je bezpečnější.

### <a name="set-the-virtual-application-path"></a>Nastavení cesty virtuální aplikace

Nastavte cestu virtuální aplikace k webové aplikaci. Tento krok je nezbytný, protože [životní cyklus aplikace Laravel](https://laravel.com/docs/5.4/lifecycle) začíná ve _veřejném_ adresáři, ne v kořenovém adresáři aplikace. Ostatní platformy PHP, jejichž životní cyklus začíná v kořenovém adresáři, můžou fungovat bez ruční konfigurace cesty virtuální aplikace.

Ve službě Cloud Shell nastavte cestu virtuální aplikace pomocí příkazu [`az resource update`](/cli/azure/resource#az-resource-update). Nahraďte zástupný symbol _&lt;appname>_.

```azurecli-interactive
az resource update --name web --resource-group myResourceGroup --namespace Microsoft.Web --resource-type config --parent sites/<app_name> --set properties.virtualApplications[0].physicalPath="site\wwwroot\public" --api-version 2015-06-01
```

Ve výchozím nastavení Azure App Service odkazuje cestu virtuální aplikace (_/_) do kořenového adresáře souborů nasazené aplikace (_sites\wwwroot_).

### <a name="push-to-azure-from-git"></a>Přenos z Gitu do Azure

[!INCLUDE [app-service-plan-no-h](../../includes/app-service-web-git-push-to-azure-no-h.md)]

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

> [!NOTE]
> Můžete si všimnout, že proces nasazení na konci nainstaluje balíčky [Composer](https://getcomposer.org/). Služba App Service tyto automatizace nespouští při výchozím nasazení, takže toto ukázkové úložiště obsahuje v kořenovém adresáři tři další soubory, které je povolují:
>
> - `.deployment` – Tento soubor informuje službu App Service, že má jako vlastní skript nasazení spustit `bash deploy.sh`.
> - `deploy.sh` – vlastní skript nasazení. Když se do souboru podíváte, zjistíte, že po příkazu `npm install` spouští příkaz `php composer.phar install`.
> - `composer.phar` – správce balíčků Composer.
>
> Pomocí tohoto postupu můžete přidat do služby App Service libovolný krok nasazení z Gitu. Další informace najdete v tématu [Vlastní skript nasazení](https://github.com/projectkudu/kudu/wiki/Custom-Deployment-Script).
>

### <a name="browse-to-the-azure-web-app"></a>Přechod do webové aplikace Azure

Přejděte na adresu `http://<app_name>.azurewebsites.net` a přidejte do seznamu několik úkolů.

![Aplikace PHP spuštěná ve službě Azure App Service](./media/app-service-web-tutorial-php-mysql/php-mysql-in-azure.png)

Blahopřejeme! Teď máte ve službě Azure App Service spuštěnou aplikaci PHP řízenou daty.

## <a name="update-model-locally-and-redeploy"></a>Místní aktualizace modelu a opětovné nasazení

V tomto kroku provedete jednoduchou změnu datového modelu `task` a webové aplikace a potom tuto aktualizaci publikujete v Azure.

Pro scénář úkolů upravíte aplikaci tak, abyste mohli úkol označit jako dokončený.

### <a name="add-a-column"></a>Přidání sloupce

V okně místního terminálu přejděte do kořenového adresáře úložiště Gitu.

Vygenerujte pro tabulku `tasks` novou migraci databáze:

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

![U úkolu přibylo zaškrtávací políčko](./media/app-service-web-tutorial-php-mysql/complete-checkbox.png)

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
git push azure master
```

Po dokončení operace `git push` přejděte do webové aplikace Azure a vyzkoušejte nové funkce.

![Změny modelu a databáze publikované v Azure](media/app-service-web-tutorial-php-mysql/complete-checkbox-published.png)

Pokud jste přidali nějaké úkoly, zůstanou v databázi. Aktualizace schématu dat nechávají existující data netknutá.

## <a name="stream-diagnostic-logs"></a>Streamování diagnostických protokolů

Zatímco je vaše aplikace PHP spuštěná v Azure App Service, můžete směrovat protokoly konzoly do svého terminálu. Tímto způsobem můžete získat stejné diagnostické zprávy, které vám pomůžou ladit chyby aplikace.

Ke spuštění streamování protokolů použijte příkaz [`az webapp log tail`](/cli/azure/webapp/log?view=azure-cli-latest#az-webapp-log-tail) v Cloud Shellu.

```azurecli-interactive
az webapp log tail --name <app_name> --resource-group myResourceGroup
```

Po zahájení streamování protokolu aktualizací webové aplikace Azure v prohlížeči získáte webový provoz. Teď se zobrazí protokoly konzoly směrované do terminálu. Pokud nevidíte protokoly konzoly okamžitě, podívejte se znovu za 30 sekund.

Streamování protokolů můžete kdykoli zastavit zadáním `Ctrl`+`C`.

> [!TIP]
> Aplikace PHP může k výstupu do konzoly použít standardní funkci [error_log()](https://php.net/manual/function.error-log.php). Ukázková aplikace používá tuto metodu v souboru _app/Http/routes.php_.
>
> Jako webová platforma [používá Laravel zprostředkovatele protokolu Monolog](https://laravel.com/docs/5.4/errors). Informace o tom, jak pomocí protokolu Monolog zajistit výstup zpráv do konzoly, najdete v článku [PHP: Přihlášení do konzoly pomocí protokolu Monolog (php://out)](https://stackoverflow.com/questions/25787258/php-how-to-use-monolog-to-log-to-console-php-out).
>
>

## <a name="manage-the-azure-web-app"></a>Správa webové aplikace Azure

Pokud chcete spravovat webovou aplikaci, kterou jste vytvořili, přejděte na web [Azure Portal](https://portal.azure.com).

V levé nabídce klikněte na **App Services** a pak klikněte na název vaší webové aplikace Azure.

![Navigace portálem k webové aplikaci Azure](./media/app-service-web-tutorial-php-mysql/access-portal.png)

Zobrazí se stránka s přehledem vaší webové aplikace. Tady můžete provádět základní úkoly správy, jako je zastavení, spuštění, restartování, procházení a odstranění.

Levá nabídka obsahuje stránky pro konfiguraci vaší aplikace.

![Stránka služby App Service na webu Azure Portal](./media/app-service-web-tutorial-php-mysql/web-app-blade.png)

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

<a name="next"></a>

## <a name="next-steps"></a>Další postup

V tomto kurzu jste se naučili:

> [!div class="checklist"]
> * Vytvořit databázi MySQL v Azure
> * Připojit k MySQL aplikaci PHP
> * Nasadit aplikaci do Azure
> * Aktualizovat datový model a znovu nasadit aplikaci
> * Streamovat diagnostické protokoly z Azure
> * Spravovat aplikaci na webu Azure Portal

V dalším kurzu se dozvíte, jak namapovat na webovou aplikaci vlastní název DNS.

> [!div class="nextstepaction"]
> [Mapování existujícího vlastního názvu DNS na Azure Web Apps](app-service-web-tutorial-custom-domain.md)
