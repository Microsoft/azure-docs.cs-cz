---
title: 'Kurz: aplikace pro Linux Ruby s Postgres'
description: Naučte se, jak získat aplikaci pro Linux Ruby v Azure App Service práci s připojením k databázi PostgreSQL v Azure. V tomto kurzu se používají kolejnice.
ms.devlang: ruby
ms.topic: tutorial
ms.date: 06/18/2020
ms.custom: mvc, cli-validate, seodec18
ms.openlocfilehash: c3fd06e2ac399fa18b588b1db5e154eaf013753b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "90979490"
---
# <a name="build-a-ruby-and-postgres-app-in-azure-app-service-on-linux"></a>Vytvoření aplikace Ruby a Postgres v Azure App Service v systému Linux

[Azure App Service ](overview.md) je vysoce škálovatelná služba s automatickými opravami pro hostování webů. V tomto kurzu se dozvíte, jak vytvořit aplikaci v Ruby a připojit ji k databázi PostgreSQL. Po dokončení budete mít ve službě App Service v Linuxu spuštěnou aplikaci [Ruby on Rails](https://rubyonrails.org/).

:::image type="content" source="./media/tutorial-ruby-postgres-app/complete-checkbox-published.png" alt-text="Snímek obrazovky s příkladem aplikace v Ruby na železnici s názvem úkoly":::

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvořit databázi PostgreSQL v Azure
> * Připojit k PostgreSQL aplikaci Ruby on Rails
> * Nasadit aplikaci do Azure
> * Aktualizovat datový model a znovu nasadit aplikaci
> * Streamovat diagnostické protokoly z Azure
> * Spravovat aplikaci na webu Azure Portal

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Předpoklady

Pro absolvování tohoto kurzu potřebujete:

* [Nainstalovat Git](https://git-scm.com/).
* [Instalace Ruby 2,6](https://www.ruby-lang.org/en/documentation/installation/)
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
sudo -u postgres createuser -d <signed-in-user>
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

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-postgres-in-azure"></a>Vytvoření Postgres v Azure

V tomto kroku vytvoříte v [Azure Database for PostgreSQL](/azure/postgresql/) databázi Postgres. Později nakonfigurujete aplikaci Ruby on Rails pro připojení k této databázi.

### <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

[!INCLUDE [Create resource group](../../includes/app-service-web-create-resource-group-linux-no-h.md)] 

## <a name="create-postgres-database-in-azure"></a>Vytvoření databáze Postgres v Azure

<!-- > [!NOTE]
> Before you create an Azure Database for PostgreSQL server, check which [compute generation](/azure/postgresql/concepts-pricing-tiers#compute-generations-and-vcores) is available in your region. If your region doesn't support Gen4 hardware, change *--sku-name* in the following command line to a value that's supported in your region, such as B_Gen4_1.  -->

V této části vytvoříte Azure Database for PostgreSQL Server a databázi. Začněte tím, že nainstalujete `db-up` rozšíření pomocí následujícího příkazu:

```azurecli
az extension add --name db-up
```

Vytvořte databázi Postgres v Azure pomocí [`az postgres up`](/cli/azure/ext/db-up/postgres#ext-db-up-az-postgres-up) příkazu, jak je znázorněno v následujícím příkladu. Nahraďte *\<postgresql-name>* *jedinečným* názvem (koncový bod serveru je *https:// \<postgresql-name> . Postgres.Database.Azure.com*). V případě *\<admin-username>* a *\<admin-password>* Zadejte přihlašovací údaje pro vytvoření uživatele správce pro tento server Postgres.

<!-- Issue: without --location -->
```azurecli
az postgres up --resource-group myResourceGroup --location westeurope --server-name <postgresql-name> --database-name sampledb --admin-user <admin-username> --admin-password <admin-password> --ssl-enforcement Enabled
```

Tento příkaz může chvíli trvat, protože je to následující:

- Vytvoří [skupinu prostředků](../azure-resource-manager/management/overview.md#terminology) s názvem `myResourceGroup` , pokud neexistuje. Každý prostředek Azure musí být v jednom z těchto. Parametr `--resource-group` je volitelný.
- Vytvoří server Postgres s administrativním uživatelem.
- Vytvoří `sampledb` databázi.
- Umožňuje přístup z místní IP adresy.
- Umožňuje přístup ze služeb Azure.
- Vytvořte uživatele databáze s přístupem k `sampledb` databázi.

Všechny kroky můžete provádět samostatně s ostatními `az postgres` příkazy a `psql` , ale všechny jsou `az postgres up` v jednom kroku.

Po dokončení příkazu Najděte výstupní řádky, které se nacházejí v `Ran Database Query:` . Zobrazují uživatele databáze, který vám byl vytvořen, s uživatelským jménem `root` a heslem `Sampledb1` . Později je budete používat k připojení aplikace k databázi.

<!-- not all locations support az postgres up -->
> [!TIP]
> `--location <location-name>`, lze nastavit na některou z [oblastí Azure](https://azure.microsoft.com/global-infrastructure/regions/). Pomocí příkazu můžete získat oblasti dostupné pro vaše předplatné [`az account list-locations`](/cli/azure/account#az-account-list-locations) . V produkčních aplikacích uložte svou databázi a aplikaci do stejného umístění.

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
export DB_HOST=<postgres-server-name>.postgres.database.azure.com
export DB_DATABASE=sampledb 
export DB_USERNAME=root@<postgres-server-name>
export DB_PASSWORD=Sampledb1
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
export RAILS_MASTER_KEY=<output-of-rails-secret>
export SECRET_KEY_BASE=<output-of-rails-secret>
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

[!INCLUDE [Configure deployment user](../../includes/configure-deployment-user-no-h.md)]

### <a name="create-an-app-service-plan"></a>Vytvoření plánu služby App Service

[!INCLUDE [Create app service plan no h](../../includes/app-service-web-create-app-service-plan-linux-no-h.md)]

### <a name="create-a-web-app"></a>Vytvoření webové aplikace

[!INCLUDE [Create web app](../../includes/app-service-web-create-web-app-ruby-linux-no-h.md)] 

### <a name="configure-database-settings"></a>Konfigurace nastavení databáze

Ve službě App Service můžete nastavit proměnné prostředí jako _nastavení aplikace_ pomocí příkazu [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings?view=azure-cli-latest&preserve-view=true#az-webapp-config-appsettings-set) v Cloud Shellu.

Následující příkaz Cloud Shellu nakonfiguruje nastavení aplikace `DB_HOST`, `DB_DATABASE`, `DB_USERNAME` a `DB_PASSWORD`. Nahraďte zástupné symboly _ &lt; AppName>_ a _ &lt; postgres-Server-Name>_.

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group myResourceGroup --settings DB_HOST="<postgres-server-name>.postgres.database.azure.com" DB_DATABASE="sampledb" DB_USERNAME="root@<postgres-server-name>" DB_PASSWORD="Sampledb1"
```

### <a name="configure-rails-environment-variables"></a>Konfigurace proměnných prostředí Rails

V místním terminálu [Vygenerujte nový tajný klíč](configure-language-ruby.md#set-secret_key_base-manually) pro produkční prostředí na železnici v Azure.

```bash
rails secret
```

Nakonfigurujte proměnné vyžadované produkčním prostředím Rails.

V následujícím příkazu Cloud Shell nahraďte dvě zástupné symboly>pro výstup-- _ &lt; --kolejnice_ s novým tajným klíčem, který jste vygenerovali v místním terminálu.

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group myResourceGroup --settings RAILS_MASTER_KEY="<output-of-rails-secret>" SECRET_KEY_BASE="<output-of-rails-secret>" RAILS_SERVE_STATIC_FILES="true" ASSETS_PRECOMPILE="true"
```

Nastavení `ASSETS_PRECOMPILE="true"` sdělí kontejneru Ruby, že se mají při každém nasazení z Gitu předkompilovat prostředky. Další informace naleznete v tématu [předkompilace prostředků](configure-language-ruby.md#precompile-assets) a [obsluha statických prostředků](configure-language-ruby.md#serve-static-assets).

### <a name="push-to-azure-from-git"></a>Přenos z Gitu do Azure

V místním terminálu přidejte do místního úložiště Gitu vzdálené prostředí Azure.

```bash
git remote add azure <paste-copied-url-here>
```

Nasdílením změn do vzdáleného prostředí Azure nasaďte aplikaci Ruby on Rails. Zobrazí se výzva k zadání hesla, které jste zadali dříve v rámci vytváření uživatele nasazení.

```bash
git push azure master
```

Během nasazení bude služba Azure App Service hlásit Gitu průběh nasazení.

<pre>
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
&lt; Output has been truncated for readability &gt;
</pre>

### <a name="browse-to-the-azure-app"></a>Přejít k aplikaci Azure

Přejděte na adresu `http://<app-name>.azurewebsites.net` a přidejte do seznamu několik úkolů.

:::image type="content" source="./media/tutorial-ruby-postgres-app/ruby-postgres-in-azure.png" alt-text="Snímek obrazovky s příkladem aplikace v Ruby na železnici s názvem úkoly":::

Blahopřejeme! Teď máte ve službě Azure App Service spuštěnou aplikaci Ruby on Rails založenou na datech.

## <a name="update-model-locally-and-redeploy"></a>Místní aktualizace modelu a opětovné nasazení

V tomto kroku provedete jednoduchou změnu datového modelu `task` a webové aplikace a potom tuto aktualizaci publikujete v Azure.

Pro scénář úkolů upravíte aplikaci tak, abyste mohli úkol označit jako dokončený.

### <a name="add-a-column"></a>Přidání sloupce

V terminálu přejděte do kořenového adresáře úložiště Git.

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

Potvrďte všechny změny v Gitu a potom odešlete změny kódu do Azure.

```bash
git add .
git commit -m "added complete checkbox"
git push azure master
```

Až `git push` to bude hotové, přejděte do aplikace Azure a vyzkoušejte nové funkce.

![Změny modelu a databáze publikované v Azure](media/tutorial-ruby-postgres-app/complete-checkbox-published.png)

Pokud jste přidali nějaké úkoly, zůstanou v databázi. Aktualizace schématu dat nechávají existující data netknutá.

## <a name="stream-diagnostic-logs"></a>Streamování diagnostických protokolů

[!INCLUDE [Access diagnostic logs](../../includes/app-service-web-logs-access-no-h.md)]

## <a name="manage-the-azure-app"></a>Správa aplikace Azure

Chcete-li spravovat aplikaci, kterou jste vytvořili, otevřete [Azure Portal](https://portal.azure.com) .

V nabídce vlevo klikněte na **App Services**a pak klikněte na název aplikace Azure.

![Přechod do aplikace Azure na portálu](./media/tutorial-php-mysql-app/access-portal.png)

Zobrazí se stránka s přehledem vaší aplikace. Tady můžete provádět základní úkoly správy, jako je zastavení, spuštění, restartování, procházení a odstranění.

Levá nabídka obsahuje stránky pro konfiguraci vaší aplikace.

![Stránka služby App Service na webu Azure Portal](./media/tutorial-php-mysql-app/web-app-blade.png)

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

<a name="next"></a>

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili:

> [!div class="checklist"]
> * Vytvořit v Azure databázi Postgres
> * Připojit k Postgres aplikaci Ruby on Rails
> * Nasadit aplikaci do Azure
> * Aktualizovat datový model a znovu nasadit aplikaci
> * Streamovat diagnostické protokoly z Azure
> * Spravovat aplikaci na webu Azure Portal

Přejděte k dalšímu kurzu, kde se dozvíte, jak namapovat vlastní název DNS na svou aplikaci.

> [!div class="nextstepaction"]
> [Kurz: mapování vlastního názvu DNS na aplikaci](app-service-web-tutorial-custom-domain.md)

Nebo si prohlédněte další zdroje informací:

> [!div class="nextstepaction"]
> [Konfigurace aplikace Ruby](configure-language-ruby.md)
