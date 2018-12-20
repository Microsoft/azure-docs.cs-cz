---
title: Vytvoření aplikace v Ruby s Postgres v Linuxu – služba Azure App Service | Dokumentace Microsoftu
description: Naučte se v Azure zprovoznit aplikaci Ruby s připojením k databázi PostgreSQL v Azure.
services: app-service\web
documentationcenter: ''
author: cephalin
manager: cfowler
ms.service: app-service-web
ms.workload: web
ms.devlang: ruby
ms.topic: tutorial
ms.date: 06/15/2018
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: 67dfc99490787eef9164759d34ef9f5ee542b76a
ms.sourcegitcommit: c94cf3840db42f099b4dc858cd0c77c4e3e4c436
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/19/2018
ms.locfileid: "53635336"
---
# <a name="build-a-ruby-and-postgres-app-in-azure-app-service-on-linux"></a>Vytvoření Ruby a Postgres aplikace ve službě Azure App Service v Linuxu

[App Service v Linuxu](app-service-linux-intro.md) je vysoce škálovatelná služba s automatickými opravami pro hostování webů s využitím operačního systému Linux. Tento kurz ukazuje postupy při vytvoření aplikace v Ruby a jejím připojení k databázi PostgreSQL. Po dokončení budete mít ve službě App Service v Linuxu spuštěnou aplikaci [Ruby on Rails](https://rubyonrails.org/).

![Aplikace Ruby on Rails spuštěná ve službě Azure App Service](./media/tutorial-ruby-postgres-app/complete-checkbox-published.png)

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvořit databázi PostgreSQL v Azure
> * Připojit k PostgreSQL aplikaci Ruby on Rails
> * Nasazení aplikace do Azure
> * Aktualizovat datový model a znovu nasadit aplikaci
> * Streamovat diagnostické protokoly z Azure
> * Spravovat aplikaci na webu Azure Portal

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Požadavky

K provedení kroků v tomto kurzu je potřeba:

* [Nainstalovat Git](https://git-scm.com/).
* [Nainstalovat jazyk Ruby 2.3](https://www.ruby-lang.org/en/documentation/installation/).
* [Nainstalovat rámec Ruby on Rails 5.1](https://guides.rubyonrails.org/v5.1/getting_started.html).
* [Nainstalovat a spustit PostgreSQL](https://www.postgresql.org/download/)

## <a name="prepare-local-postgres"></a>Příprava místního Postgres

V tomto kroku vytvoříte na místním serveru Postgres databázi, kterou budete v tomto kurzu používat.

### <a name="connect-to-local-postgres-server"></a>Připojení k místnímu serveru Postgres

Otevřete okno terminálu a spuštěním příkazu `psql` se připojte ke svému místnímu serveru Postgres.

```bash
sudo -u postgres psql
```

Pokud se úspěšně připojíte, vaše databáze Postgres je spuštěná. Pokud ne, podle postupu na stránce [Downloads – PostgreSQL Core Distribution](https://www.postgresql.org/download/) (Ke stažení – Distribuce jádra PostgreSQL) se ujistěte, že je vaše místní databáze Postgres spuštěná.

Zadáním příkazu `\q` ukončete klienta Postgres. 

Vytvořte uživatele Postgres, který může vytvářet databáze, spuštěním následujícího příkazu s použitím vašeho přihlašovacího uživatelského jména pro Linux.

```bash
sudo -u postgres createuser -d <signed_in_user>
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

### <a name="run-the-sample-locally"></a>Spuštění ukázky v místním prostředí

Spusťte [migrace serveru Rails](https://guides.rubyonrails.org/active_record_migrations.html#running-migrations), aby se vytvořily tabulky, které aplikace potřebuje. Pokud chcete zjistit, které tabulky migrace vytvářejí, podívejte se do adresáře _db/migrate_ v úložišti Git.

```bash
rake db:create
rake db:migrate
```

Spusťte aplikaci.

```bash
rails server
```

V prohlížeči přejděte na `http://localhost:3000`. Na stránce přidejte několik úkolů.

![Aplikace Ruby on Rails se úspěšně připojí k Postgres](./media/tutorial-ruby-postgres-app/postgres-connect-success.png)

Pokud chcete server Rails zastavit, zadejte do terminálu `Ctrl + C`.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="create-postgres-in-azure"></a>Vytvoření Postgres v Azure

V tomto kroku vytvoříte v [Azure Database for PostgreSQL](/azure/postgresql/) databázi Postgres. Později nakonfigurujete aplikaci Ruby on Rails pro připojení k této databázi.

### <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

[!INCLUDE [Create resource group](../../../includes/app-service-web-create-resource-group-linux-no-h.md)] 

### <a name="create-a-postgres-server"></a>Vytvoření serveru Postgres

Vytvořte server PostgreSQL pomocí příkazu [`az postgres server create`](/cli/azure/postgres/server?view=azure-cli-latest#az-postgres-server-create).

Ve službě Cloud Shell spusťte následující příkaz a zástupný text *\<postgres_server_name>* nahraďte jedinečným názvem serveru. Název serveru musí být jedinečný mezi všemi servery v Azure. 

```azurecli-interactive
az postgres server create --location "West Europe" --resource-group myResourceGroup --name <postgres_server_name> --admin-user adminuser --admin-password My5up3r$tr0ngPa$w0rd! --sku-name GP_Gen4_2
```

Po vytvoření serveru Azure Database for PostgreSQL se v Azure CLI zobrazí podobné informace jako v následujícím příkladu:

```json
{
  "administratorLogin": "adminuser",
  "earliestRestoreDate": "2018-06-15T12:38:25.280000+00:00",
  "fullyQualifiedDomainName": "<postgres_server_name>.postgres.database.azure.com",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.DBforPostgreSQL/servers/<postgres_server_name>",
  "location": "westeurope",
  "name": "<postgres_server_name>",
  "resourceGroup": "myResourceGroup",
  "sku": {
    "capacity": 2,
    "family": "Gen4",
    "name": "GP_Gen4_2",
    "size": null,
    "tier": "GeneralPurpose"
  },
  < Output has been truncated for readability >
}
```

### <a name="configure-server-firewall"></a>Konfigurace brány firewall serveru

Ve službě Cloud Shell pomocí příkazu [`az postgres server firewall-rule create`](/cli/azure/postgres/server/firewall-rule?view=azure-cli-latest#az-postgres-server-firewall-rule-create) vytvořte pro svůj server Postgres pravidlo brány firewall umožňující klientská připojení. Pokud je jako počáteční i koncová adresa IP nastavená hodnota 0.0.0.0, je brána firewall otevřená jen pro ostatní prostředky Azure. Zástupný text *\<postgres_server_name>* nahraďte jedinečným názvem serveru.

```azurecli-interactive
az postgres server firewall-rule create --resource-group myResourceGroup --server <postgres_server_name> --name AllowAllIps --start-ip-address 0.0.0.0 --end-ip-address 255.255.255.255
```

> [!TIP] 
> Pravidlo brány firewall můžete dál omezit [použitím jenom odchozích IP adres, které vaše aplikace používá](../app-service-ip-addresses.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#find-outbound-ips).
>

### <a name="connect-to-production-postgres-server-locally"></a>Místní připojení k produkčnímu serveru Postgres

Ve službě Cloud Shell se připojte k serveru Postgres v Azure. Místo zástupného textu _&lt;postgres_server_name>_ použijte hodnotu, kterou jste zadali předtím.

```bash
psql -U adminuser@<postgres_server_name> -h <postgres_server_name>.postgres.database.azure.com postgres
```

Po zobrazení výzvy k zadání hesla použijte heslo _My5up3r$tr0ngPa$w0rd!_, které jste zadali při vytváření databázového serveru.

### <a name="create-a-production-database"></a>Vytvoření produkční databáze

Na příkazovém řádku `postgres` vytvořte databázi.

```sql
CREATE DATABASE sampledb;
```

### <a name="create-a-user-with-permissions"></a>Vytvoření uživatele s oprávněními

Vytvořte uživatele databáze se jménem _railsappuser_ a přidělte mu všechna oprávnění k databázi `sampledb`.

```sql
CREATE USER railsappuser WITH PASSWORD 'MyPostgresAzure2017'; 
GRANT ALL PRIVILEGES ON DATABASE sampledb TO railsappuser;
```

Ukončete připojení k serveru zadáním příkazu `\q`.

## <a name="connect-app-to-azure-postgres"></a>Připojení aplikace k Azure Postgres

V tomto kroku připojíte aplikaci Ruby on Rails k databázi Postgres, kterou jste vytvořili v Azure Database for PostgreSQL.

<a name="devconfig"></a>

### <a name="configure-the-database-connection"></a>Konfigurace připojení k databázi

V úložišti otevřete soubor _config/database.yml_. Ve spodní části souboru nahraďte produkční proměnné následujícím kódem. 

```txt
production:
  <<: *default
  host: <%= ENV['DB_HOST'] %>
  database: <%= ENV['DB_DATABASE'] %>
  username: <%= ENV['DB_USERNAME'] %>
  password: <%= ENV['DB_PASSWORD'] %>
```

Uložte změny.

### <a name="test-the-application-locally"></a>Test aplikace v místním prostředí

Vraťte se do místního terminálu a nastavte následující proměnné prostředí:

```bash
export DB_HOST=<postgres_server_name>.postgres.database.azure.com
export DB_DATABASE=sampledb 
export DB_USERNAME=railsappuser@<postgres_server_name>
export DB_PASSWORD=MyPostgresAzure2017
```

Spusťte migrace databáze Rails s produkčními hodnotami, které jste právě nakonfigurovali, aby se ve vaší databázi Postgres v Azure Database for PostgreSQL vytvořily tabulky. 

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

Přejděte na adresu `http://localhost:3000`. Pokud se stránka načte bez chyb, aplikace Ruby on Rails se připojuje k databázi Postgres v Azure.

Na stránce přidejte několik úkolů.

![Úspěšné připojení aplikace Ruby on Rails k Azure Database for PostgreSQL](./media/tutorial-ruby-postgres-app/azure-postgres-connect-success.png)

Pokud chcete server Rails zastavit, zadejte do terminálu `Ctrl + C`.

### <a name="commit-your-changes"></a>Potvrzení změn

Potvrďte provedené změny spuštěním následujících příkazů Gitu:

```bash
git add .
git commit -m "database.yml updates"
```

Vaše aplikace je připravená k nasazení.

## <a name="deploy-to-azure"></a>Nasazení do Azure

V tomto kroku nasadíte aplikaci Rails připojenou k Postgres do služby Azure App Service.

### <a name="configure-a-deployment-user"></a>Konfigurace uživatele nasazení

[!INCLUDE [Configure deployment user](../../../includes/configure-deployment-user-no-h.md)]

### <a name="create-an-app-service-plan"></a>Vytvoření plánu služby App Service

[!INCLUDE [Create app service plan no h](../../../includes/app-service-web-create-app-service-plan-linux-no-h.md)]

### <a name="create-a-web-app"></a>Vytvoření webové aplikace

[!INCLUDE [Create web app](../../../includes/app-service-web-create-web-app-ruby-linux-no-h.md)] 

### <a name="configure-database-settings"></a>Konfigurace nastavení databáze

Ve službě App Service můžete nastavit proměnné prostředí jako _nastavení aplikace_ pomocí příkazu [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) v Cloud Shellu.

Následující příkaz Cloud Shellu nakonfiguruje nastavení aplikace `DB_HOST`, `DB_DATABASE`, `DB_USERNAME` a `DB_PASSWORD`. Nahraďte zástupné texty _&lt;appname>_ a _&lt;postgres_server_name>_.

```azurecli-interactive
az webapp config appsettings set --name <app_name> --resource-group myResourceGroup --settings DB_HOST="<postgres_server_name>.postgres.database.azure.com" DB_DATABASE="sampledb" DB_USERNAME="railsappuser@<postgres_server_name>" DB_PASSWORD="MyPostgresAzure2017"
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

### <a name="browse-to-the-azure-app"></a>Přejděte do aplikace Azure

Přejděte na adresu `http://<app_name>.azurewebsites.net` a přidejte do seznamu několik úkolů.

![Aplikace Ruby on Rails spuštěná ve službě Azure App Service](./media/tutorial-ruby-postgres-app/ruby-postgres-in-azure.png)

Blahopřejeme! Teď máte ve službě Azure App Service spuštěnou aplikaci Ruby on Rails založenou na datech.

## <a name="update-model-locally-and-redeploy"></a>Místní aktualizace modelu a opětovné nasazení

V tomto kroku provedete jednoduchou změnu datového modelu `task` a webové aplikace a potom tuto aktualizaci publikujete v Azure.

Pro scénář úkolů upravíte aplikaci tak, abyste mohli úkol označit jako dokončený.

### <a name="add-a-column"></a>Přidání sloupce

V terminálu přejděte do kořenového adresáře úložiště Gitu.

Vytvořte novou migraci, která přidá do tabulky `Tasks` logický sloupec s názvem `Done`:

```bash
rails generate migration AddDoneToTasks Done:boolean
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

![U úkolu přibylo zaškrtávací políčko](./media/tutorial-ruby-postgres-app/complete-checkbox.png)

Pokud chcete server Rails zastavit, zadejte do terminálu `Ctrl + C`.

### <a name="publish-changes-to-azure"></a>Publikování změn v Azure

V terminálu spusťte migrace databáze Rails, aby produkční prostředí provedlo změnu v databázi Azure.

```bash
rake db:migrate RAILS_ENV=production
```

Potvrďte všechny změny v Gitu a potom nasdílejte změny kódu do Azure.

```bash
git add .
git commit -m "added complete checkbox"
git push azure master
```

Jakmile `git push` dokončí, přejděte do aplikace Azure a vyzkoušejte nové funkce.

![Změny modelu a databáze publikované v Azure](media/tutorial-ruby-postgres-app/complete-checkbox-published.png)

Pokud jste přidali nějaké úkoly, zůstanou v databázi. Aktualizace schématu dat nechávají existující data netknutá.

## <a name="manage-the-azure-app"></a>Správa aplikace Azure

Přejděte [webu Azure portal](https://portal.azure.com) ke správě aplikace, které jste vytvořili.

V levé nabídce klikněte na tlačítko **App Services**a pak klikněte na název aplikace Azure.

![Přechod do aplikace Azure na portálu](./media/tutorial-php-mysql-app/access-portal.png)

Se zobrazí stránka s přehledem vaší aplikace. Tady můžete provádět základní úkoly správy, jako je zastavení, spuštění, restartování, procházení a odstranění.

Levá nabídka obsahuje stránky pro konfiguraci vaší aplikace.

![Stránka služby App Service na webu Azure Portal](./media/tutorial-php-mysql-app/web-app-blade.png)

[!INCLUDE [cli-samples-clean-up](../../../includes/cli-samples-clean-up.md)]

<a name="next"></a>

## <a name="next-steps"></a>Další postup

V tomto kurzu jste se naučili:

> [!div class="checklist"]
> * Vytvořit v Azure databázi Postgres
> * Připojit k Postgres aplikaci Ruby on Rails
> * Nasazení aplikace do Azure
> * Aktualizovat datový model a znovu nasadit aplikaci
> * Streamovat diagnostické protokoly z Azure
> * Spravovat aplikaci na webu Azure Portal

Přejděte k dalšímu kurzu, kde se naučíte, jak namapovat vlastní název DNS do vaší aplikace.

> [!div class="nextstepaction"]
> [Mapování existujícího vlastního názvu DNS do služby Azure App Service](../app-service-web-tutorial-custom-domain.md)
