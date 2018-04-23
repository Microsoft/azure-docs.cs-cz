---
title: Vytvoření webové aplikace Ruby využívající databázi MySQL ve službě Azure App Service v Linuxu | Microsoft Docs
description: Naučte se v Azure zprovoznit aplikaci Ruby s připojením k databázi MySQL v Azure.
services: app-service\web
documentationcenter: ''
author: cephalin
manager: cfowler
ms.service: app-service-web
ms.workload: web
ms.devlang: ruby
ms.topic: tutorial
ms.date: 12/21/2017
ms.author: cephalin
ms.custom: mvc
ms.openlocfilehash: a4a422e62940a535a6303339bd5712654881d304
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/18/2018
---
# <a name="build-a-ruby-and-mysql-web-app-in-azure-app-service-on-linux"></a>Vytvoření webové aplikace Ruby využívající databázi MySQL ve službě Azure App Service v Linuxu

[App Service v Linuxu](app-service-linux-intro.md) je vysoce škálovatelná služba s automatickými opravami pro hostování webů s využitím operačního systému Linux. Tento kurz předvádí postup při vytváření webové aplikace Ruby a jejím připojení k databázi MySQL. Po dokončení budete mít ve službě App Service v Linuxu spuštěnou aplikaci [Ruby on Rails](http://rubyonrails.org/).

![Aplikace Ruby on Rails spuštěná ve službě Azure App Service](./media/tutorial-ruby-mysql-app/complete-checkbox-published.png)

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvořit databázi MySQL v Azure
> * Připojit k databázi MySQL aplikaci Ruby on Rails
> * Nasadit aplikaci do Azure
> * Aktualizovat datový model a znovu nasadit aplikaci
> * Streamovat diagnostické protokoly z Azure
> * Spravovat aplikaci na webu Azure Portal

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Požadavky

Pro absolvování tohoto kurzu potřebujete:

* [Nainstalovat Git](https://git-scm.com/).
* [Nainstalovat jazyk Ruby 2.3](https://www.ruby-lang.org/documentation/installation/).
* [Nainstalovat rámec Ruby on Rails 5.1](http://guides.rubyonrails.org/v5.1/getting_started.html).
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

Ukončete připojení k serveru zadáním příkazu `quit`.

```sql
quit
```

<a name="step2"></a>

## <a name="create-a-ruby-on-rails-app-locally"></a>Vytvoření aplikace Ruby on Rails v místním prostředí
V tomto kroku získáte ukázkovou aplikaci Ruby on Rails, nakonfigurujete její připojení k databázi a spustíte ji v místním prostředí. 

### <a name="clone-the-sample"></a>Vytvoření klonu ukázky

V okně terminálu přejděte pomocí příkazu `cd` do pracovního adresáře.

Ukázkové úložiště naklonujete spuštěním následujícího příkazu.

```bash
git clone https://github.com/Azure-Samples/rubyrails-tasks.git
```

Pomocí příkazu `cd` přejděte do naklonovaného adresáře. Nainstalujte požadované balíčky.

```bash
cd rubyrails-tasks
bundle install --path vendor/bundle
```

### <a name="configure-mysql-connection"></a>Konfigurace připojení k MySQL

V úložišti otevřete soubor _config/database.yml_ a zadejte uživatelské jméno a heslo místního kořenového uživatele MySQL (řádek 13). Pokud jste nainstalovali MySQL pomocí nástroje, jako je [Homebrew](https://brew.sh/), jsou už přihlašovací údaje v souboru nastavené na výchozí hodnoty (což je `root` a prázdné heslo).

```txt
default: &default
  adapter: mysql2
  encoding: utf8
  pool: <%= ENV.fetch("RAILS_MAX_THREADS") { 5 } %>
  username: root
  password:
  socket: /tmp/mysql.sock
```

### <a name="run-the-sample-locally"></a>Spuštění ukázky v místním prostředí

Spusťte [migrace serveru Rails](http://guides.rubyonrails.org/active_record_migrations.html#running-migrations), aby se vytvořily tabulky, které aplikace potřebuje. Pokud chcete zjistit, které tabulky migrace vytvářejí, podívejte se do adresáře _db/migrate_ v úložišti Git.

```bash
rake db:create
rake db:migrate
```

Spusťte aplikaci.

```bash
rails server
```

V prohlížeči přejděte na `http://localhost:3000`. Na stránce přidejte několik úkolů.

![Aplikace Ruby on Rails se úspěšně připojí k MySQL](./media/tutorial-ruby-mysql-app/mysql-connect-success.png)

Pokud chcete server Rails zastavit, zadejte do terminálu `Ctrl + C`.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="create-mysql-in-azure"></a>Vytvoření databáze MySQL v Azure

V tomto kroku vytvoříte v [Azure Database for MySQL (Preview)](/azure/mysql) databázi MySQL. Později nakonfigurujete aplikaci Ruby on Rails pro připojení k této databázi.

### <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

[!INCLUDE [Create resource group](../../../includes/app-service-web-create-resource-group-linux-no-h.md)] 

### <a name="create-a-mysql-server"></a>Vytvoření serveru MySQL

Pomocí příkazu [`az mysql server create`](/cli/azure/mysql/server?view=azure-cli-latest#az_mysql_server_create) vytvořte v Azure Database for MySQL (Preview) server.

V následujícím příkazu nahraďte zástupný text _&lt;mysql_server_name>_ názvem vašeho serveru MySQL (platné znaky jsou `a-z`, `0-9` a `-`). Tento název je součástí názvu hostitele serveru MySQL (`<mysql_server_name>.mysql.database.azure.com`) a musí být globálně jedinečný.

```azurecli-interactive
az mysql server create --name <mysql_server_name> --resource-group myResourceGroup --location "North Europe" --admin-user adminuser --admin-password My5up3r$tr0ngPa$w0rd!
```

Po vytvoření serveru MySQL se v Azure CLI zobrazí podobné informace jako v následujícím příkladu:

```json
{
  "administratorLogin": "adminuser",
  "administratorLoginPassword": null,
  "fullyQualifiedDomainName": "<mysql_server_name>.database.mysql.database.azure.com",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.DBforMySQL/servers/<mysql_server_name>",
  "location": "northeurope",
  "name": "<mysql_server_name>",
  "resourceGroup": "myResourceGroup",
  ...
}
```

### <a name="configure-server-firewall"></a>Konfigurace brány firewall serveru

Pomocí příkazu [`az mysql server firewall-rule create`](/cli/azure/mysql/server/firewall-rule?view=azure-cli-latest#az_mysql_server_firewall_rule_create) vytvořte pro svůj server MySQL pravidlo brány firewall umožňující klientská připojení. Pokud je jako počáteční i koncová adresa IP nastavená hodnota 0.0.0.0, je brána firewall otevřená jen pro ostatní prostředky Azure. 

```azurecli-interactive
az mysql server firewall-rule create --name allAzureIPs --server <mysql_server_name> --resource-group myResourceGroup --start-ip-address 0.0.0.0 --end-ip-address 0.0.0.0
```

### <a name="connect-to-production-mysql-server-locally"></a>Místní připojení k produkčnímu serveru MySQL

V okně terminálu se připojte k serveru MySQL v Azure. U položky _&lt;mysql_server_name>_ použijte hodnotu, kterou jste zadali předtím.

```bash
mysql -u adminuser@<mysql_server_name> -h <mysql_server_name>.mysql.database.azure.com -P 3306 -p
```

Po zobrazení výzvy k zadání hesla použijte heslo _My5up3r$tr0ngPa$w0rd!_, které jste zadali při vytváření databázového serveru.

### <a name="create-a-production-database"></a>Vytvoření produkční databáze

Na příkazovém řádku `mysql` vytvořte databázi.

```sql
CREATE DATABASE sampledb;
```

### <a name="create-a-user-with-permissions"></a>Vytvoření uživatele s oprávněními

Vytvořte uživatele databáze se jménem _railsappuser_ a přidělte mu všechna oprávnění k databázi `sampledb`.

```sql
CREATE USER 'railsappuser' IDENTIFIED BY 'MySQLAzure2017'; 
GRANT ALL PRIVILEGES ON sampledb.* TO 'railsappuser';
```

Ukončete připojení k serveru zadáním příkazu `quit`.

```sql
quit
```

## <a name="connect-app-to-azure-mysql"></a>Připojení aplikace k Azure MySQL

V tomto kroku připojíte aplikaci Ruby on Rails k databázi MySQL, kterou jste vytvořili v Azure Database for MySQL (Preview).

<a name="devconfig"></a>

### <a name="configure-the-database-connection"></a>Konfigurace připojení k databázi

V úložišti otevřete soubor _config/database.yml_. Ve spodní části souboru nahraďte produkční proměnné následujícím kódem. Namísto zástupného textu _&lt;mysql_server_name>_ zadejte název serveru MySQL, který jste vytvořili.

```txt
production:
  <<: *default
  host: <%= ENV['DB_HOST'] %>
  port: 3306
  database: <%= ENV['DB_DATABASE'] %>
  username: <%= ENV['DB_USERNAME'] %>
  password: <%= ENV['DB_PASSWORD'] %>
  sslca: ssl/BaltimoreCyberTrustRoot.crt.pem
```

Uložte změny.

> [!NOTE]
> Přidá se položka `sslca` odkazující na existující soubor _.pem_ v úložišti ukázek. Ve výchozím nastavení vynucuje Azure Database for MySQL od klientů připojení SSL. Tento certifikát `.pem` umožňuje vytvořit připojení SSL k Azure Database for MySQL. Další informace najdete v tématu [Konfigurace připojení SSL v aplikaci pro zabezpečené připojení k Azure Database for MySQL](../../mysql/howto-configure-ssl.md).
>

### <a name="test-the-application-locally"></a>Test aplikace v místním prostředí

V místní terminálu nastavte následující proměnné prostředí:

```bash
export DB_HOST=<mysql_server_name>.mysql.database.azure.com
export DB_DATABASE=sampledb 
export DB_USERNAME=railsappuser@<mysql_server_name>
export DB_PASSWORD=MySQLAzure2017
```

Spusťte migrace databáze Rails s produkčními hodnotami, které jste právě nakonfigurovali, aby se ve vaší databázi MySQL v Azure Database for MySQL (Preview) vytvořily tabulky. 

```bash
rake db:migrate RAILS_ENV=production
```

Při spuštění v produkčním prostředí potřebuje aplikace Rails předkompilované prostředky. Požadované prostředky vygenerujte pomocí následujícího příkazu:

```bash
rake assets:precompile
```

Produkční prostředí Rails také používá ke správě zabezpečení tajné kódy. Vygenerujte tajný klíč.

```bash
rails secret
```

Uložte tajný klíč do odpovídajících proměnných používaných produkčním prostředím Rails. Pro jednoduchost použijte u obou proměnných stejný klíč.

```bash
export RAILS_MASTER_KEY=<output_of_rails_secret>
export SECRET_KEY_BASE=<output_of_rails_secret>
```

Povolte v produkčním prostředí Rails zpracování souborů JavaScript a CSS.

```bash
export RAILS_SERVE_STATIC_FILES=true
```

Spusťte v produkčním prostředí ukázkovou aplikaci.

```bash
rails server -e production
```

Přejděte na adresu `http://localhost:3000`. Pokud se stránka načte bez chyb, aplikace Ruby on Rails se v Azure připojuje k databázi MySQL.

Na stránce přidejte několik úkolů.

![Aplikace Ruby on Rails se úspěšně připojí k Azure Database for MySQL (Preview)](./media/tutorial-ruby-mysql-app/azure-mysql-connect-success.png)

Pokud chcete server Rails zastavit, zadejte do terminálu `Ctrl + C`.

### <a name="commit-your-changes"></a>Potvrzení změn

Potvrďte provedené změny spuštěním následujících příkazů Gitu:

```bash
git add .
git commit -m "database.yml updates"
```

Vaše aplikace je připravená k nasazení.

## <a name="deploy-to-azure"></a>Nasazení do Azure

V tomto kroku nasadíte aplikaci Rails připojenou k MySQL do služby Azure App Service.

### <a name="configure-a-deployment-user"></a>Konfigurace uživatele nasazení

[!INCLUDE [Configure deployment user](../../../includes/configure-deployment-user-no-h.md)]

### <a name="create-an-app-service-plan"></a>Vytvoření plánu služby App Service

[!INCLUDE [Create app service plan no h](../../../includes/app-service-web-create-app-service-plan-linux-no-h.md)]

### <a name="create-a-web-app"></a>Vytvoření webové aplikace

[!INCLUDE [Create web app](../../../includes/app-service-web-create-web-app-ruby-linux-no-h.md)] 

### <a name="configure-database-settings"></a>Konfigurace nastavení databáze

Ve službě App Service můžete nastavit proměnné prostředí jako _nastavení aplikace_ pomocí příkazu [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az_webapp_config_appsettings_set) v Cloud Shellu.

Následující příkaz Cloud Shellu nakonfiguruje nastavení aplikace `DB_HOST`, `DB_DATABASE`, `DB_USERNAME` a `DB_PASSWORD`. Nahraďte zástupné texty _&lt;appname>_ a _&lt;mysql_server_name>_.

```azurecli-interactive
az webapp config appsettings set --name <app_name> --resource-group myResourceGroup --settings DB_HOST="<mysql_server_name>.mysql.database.azure.com" DB_DATABASE="sampledb" DB_USERNAME="railsappuser@<mysql_server_name>" DB_PASSWORD="MySQLAzure2017"
```

### <a name="configure-rails-environment-variables"></a>Konfigurace proměnných prostředí Rails

V místním terminálu vygenerujte nový tajný klíč pro produkční prostředí Rails v Azure.

```bash
rails secret
```

Nakonfigurujte proměnné vyžadované produkčním prostředím Rails.

V následujícím příkazu Cloud Shellu nahraďte oba zástupné texty _&lt;output_of_rails_secret>_ novým tajným klíčem, který jste vygenerovali v místním terminálu.

```azurecli-interactive
az webapp config appsettings set --name <app_name> --resource-group myResourceGroup --settings RAILS_MASTER_KEY="<output_of_rails_secret>" SECRET_KEY_BASE="<output_of_rails_secret>" RAILS_SERVE_STATIC_FILES="true" ASSETS_PRECOMPILE="true"
```

Nastavení `ASSETS_PRECOMPILE="true"` sdělí kontejneru Ruby, že se mají při každém nasazení z Gitu předkompilovat prostředky.

### <a name="push-to-azure-from-git"></a>Přenos z Gitu do Azure

V místním terminálu přidejte do místního úložiště Gitu vzdálené prostředí Azure.

```bash
git remote add azure <paste_copied_url_here>
```

Nasdílením změn do vzdáleného prostředí Azure nasaďte aplikaci Ruby on Rails. Zobrazí se výzva k zadání hesla, které jste zadali dříve v rámci vytváření uživatele nasazení.

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

### <a name="browse-to-the-azure-web-app"></a>Přechod do webové aplikace Azure

Přejděte na adresu `http://<app_name>.azurewebsites.net` a přidejte do seznamu několik úkolů.

![Aplikace Ruby on Rails spuštěná ve službě Azure App Service](./media/tutorial-ruby-mysql-app/ruby-mysql-in-azure.png)

Blahopřejeme! Teď máte ve službě Azure App Service spuštěnou aplikaci Ruby on Rails založenou na datech.

## <a name="update-model-locally-and-redeploy"></a>Místní aktualizace modelu a opětovné nasazení

V tomto kroku provedete jednoduchou změnu datového modelu `task` a webové aplikace a potom tuto aktualizaci publikujete v Azure.

Pro scénář úkolů upravíte aplikaci tak, abyste mohli úkol označit jako dokončený.

### <a name="add-a-column"></a>Přidání sloupce

V terminálu přejděte do kořenového adresáře úložiště Gitu.

Vytvořte novou migraci, která přidá do tabulky `Tasks` logický sloupec s názvem `Done`:

```bash
rails generate migration add_Done_to_Tasks Done:boolean
```

Tento příkaz vygeneruje v adresáři _db/migrate_ nový soubor migrace.


V terminálu spusťte migrace databáze Rails, aby se změna provedla v místní databázi.

```bash
rake db:migrate
```

### <a name="update-application-logic"></a>Aktualizace logiky aplikace

Otevřete soubor *app/controllers/tasks_controller.rb*. Na konci souboru najdete následující řádek:

```rb
params.require(:task).permit(:Description)
```

Tento řádek upravte tak, aby obsahoval nový parametr `Done`.

```rb
params.require(:task).permit(:Description, :Done)
```

### <a name="update-the-views"></a>Aktualizace zobrazení

Otevřete soubor *app/views/tasks/_form.html.erb*, což je formulář pro úpravy.

Najděte řádek `<%=f.error_span(:Description) %>` a přímo pod něj vložte následující kód:

```erb
<%= f.label :Done, :class => 'control-label col-lg-2' %>
<div class="col-lg-10">
  <%= f.check_box :Done, :class => 'form-control' %>
</div>
```

Otevřete soubor *app/views/tasks/show.html.erb* což je stránka zobrazení s jedním záznamem. 

Najděte řádek `<dd><%= @task.Description %></dd>` a přímo pod něj vložte následující kód:

```erb
  <dt><strong><%= model_class.human_attribute_name(:Done) %>:</strong></dt>
  <dd><%= check_box "task", "Done", {:checked => @task.Done, :disabled => true}%></dd>
```

Otevřete soubor *app/views/tasks/index.html.erb*, což je indexová stránka pro všechny záznamy.

Najděte řádek `<th><%= model_class.human_attribute_name(:Description) %></th>` a přímo pod něj vložte následující kód:

```erb
<th><%= model_class.human_attribute_name(:Done) %></th>
```

Ve stejném souboru najděte řádek `<td><%= task.Description %></td>` a přímo pod něj vložte následující kód:

```erb
<td><%= check_box "task", "Done", {:checked => task.Done, :disabled => true} %></td>
```

### <a name="test-the-changes-locally"></a>Místní test provedených změn

V místní terminálu spusťte server Rails.

```bash
rails server
```

Pokud chcete vidět změnu stavu úkolu, přejděte na adresu `http://localhost:3000` a přidejte nebo upravte položky.

![U úkolu přibylo zaškrtávací políčko](./media/tutorial-ruby-mysql-app/complete-checkbox.png)

Pokud chcete server Rails zastavit, zadejte do terminálu `Ctrl + C`.

### <a name="publish-changes-to-azure"></a>Publikování změn v Azure

V terminálu spusťte migrace databáze Rails, aby produkční prostředí provedlo změnu v databázi Azure.

```bash
rake db:migrate RAILS_ENV=production
```

Potvrďte všechny změny v Gitu a potom odešlete změny kódu do Azure.

```bash
git add .
git commit -m "added complete checkbox"
git push azure master
```

Po dokončení operace `git push` přejděte do webové aplikace Azure a vyzkoušejte nové funkce.

![Změny modelu a databáze publikované v Azure](media/tutorial-ruby-mysql-app/complete-checkbox-published.png)

Pokud jste přidali nějaké úkoly, zůstanou v databázi. Aktualizace schématu dat nechávají existující data netknutá.

## <a name="manage-the-azure-web-app"></a>Správa webové aplikace Azure

Pokud chcete spravovat webovou aplikaci, kterou jste vytvořili, přejděte na web [Azure Portal](https://portal.azure.com).

V levé nabídce klikněte na **App Services** a pak klikněte na název vaší webové aplikace Azure.

![Navigace portálem k webové aplikaci Azure](./media/tutorial-php-mysql-app/access-portal.png)

Zobrazí se stránka s přehledem vaší webové aplikace. Tady můžete provádět základní úkoly správy, jako je zastavení, spuštění, restartování, procházení a odstranění.

Levá nabídka obsahuje stránky pro konfiguraci vaší aplikace.

![Stránka služby App Service na webu Azure Portal](./media/tutorial-php-mysql-app/web-app-blade.png)

[!INCLUDE [cli-samples-clean-up](../../../includes/cli-samples-clean-up.md)]

<a name="next"></a>

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili:

> [!div class="checklist"]
> * Vytvořit databázi MySQL v Azure
> * Připojit k databázi MySQL aplikaci Ruby on Rails
> * Nasadit aplikaci do Azure
> * Aktualizovat datový model a znovu nasadit aplikaci
> * Streamovat diagnostické protokoly z Azure
> * Spravovat aplikaci na webu Azure Portal

V dalším kurzu se dozvíte, jak namapovat na webovou aplikaci vlastní název DNS.

> [!div class="nextstepaction"]
> [Mapování existujícího vlastního názvu DNS na Azure Web Apps](../app-service-web-tutorial-custom-domain.md)
