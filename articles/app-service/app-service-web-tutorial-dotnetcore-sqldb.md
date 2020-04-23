---
title: 'Kurz: ASP.NET jádro s databází SQL'
description: Naučte se zprovoznit aplikaci .NET Core ve službě Azure App Service s připojením k databázi SQL.
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 08/06/2019
ms.custom: mvc, cli-validate, seodec18
ms.openlocfilehash: c4dacd06cd53ebb71ca9db2722fdf46aade841bc
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2020
ms.locfileid: "82085398"
---
# <a name="tutorial-build-an-aspnet-core-and-sql-database-app-in-azure-app-service"></a>Kurz: Vytvoření aplikace ASP.NET core a sql database ve službě Azure App Service

> [!NOTE]
> Tento článek nasadí aplikaci do služby App Service ve Windows. Informace o nasazení ve službě App Service na _Linuxu_najdete [v tématu Vytvoření aplikace .NET Core a SQL Database ve službě Azure App Service na Linuxu](./containers/tutorial-dotnetcore-sqldb-app.md).
>

[App Service ](overview.md) je vysoce škálovatelná služba s automatickými opravami pro hostování webů v Azure. Tento kurz ukazuje, jak vytvořit aplikaci .NET Core a připojit ji k databázi SQL. Po dokončení budete mít aplikaci .NET Core MVC spuštěnou ve službě App Service.

![aplikace spuštěná ve službě App Service](./media/app-service-web-tutorial-dotnetcore-sqldb/azure-app-in-browser.png)

Naučíte se:

> [!div class="checklist"]
> * Vytvořit databázi SQL v Azure
> * Připojit aplikaci .NET Core k databázi SQL
> * Nasadit aplikaci do Azure
> * Aktualizovat datový model a znovu nasadit aplikaci
> * Streamovat diagnostické protokoly z Azure
> * Spravovat aplikaci na webu Azure Portal

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Požadované součásti

Pro absolvování tohoto kurzu potřebujete:

* [Nainstalovat Git](https://git-scm.com/).
* [Instalace sady .NET Core SDK](https://dotnet.microsoft.com/download)

## <a name="create-local-net-core-app"></a>Vytvoření místní aplikace .NET Core

V tomto kroku nastavíte místní projekt .NET Core.

### <a name="clone-the-sample-application"></a>Klonování ukázkové aplikace

V okně terminálu přejděte pomocí příkazu `cd` do pracovního adresáře.

Spuštěním následujících příkazů naklonujte ukázkové úložiště a přejděte do jeho kořenové složky.

```bash
git clone https://github.com/azure-samples/dotnetcore-sqldb-tutorial
cd dotnetcore-sqldb-tutorial
```

Ukázkový projekt obsahuje základní aplikaci CRUD (vytváření-čtení-aktualizace-odstraňování) pomocí [Entity Framework Core](https://docs.microsoft.com/ef/core/).

### <a name="run-the-application"></a>Spuštění aplikace

Spuštěním následujících příkazů nainstalujte požadované balíčky, spusťte operace migrace databáze a spusťte aplikaci.

```bash
dotnet tool install -g dotnet-ef --version 3.1.1
dotnet-ef database update
dotnet run
```

V prohlížeči přejděte na `http://localhost:5000`. Vyberte odkaz **Vytvořit nový** a vytvořte několik položek _úkolů_.

![úspěšné připojení k databázi SQL](./media/app-service-web-tutorial-dotnetcore-sqldb/local-app-in-browser.png)

.NET Core můžete kdykoli zastavit stisknutím `Ctrl+C` v terminálu.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-production-sql-database"></a>Vytvoření produkční databáze SQL

V tomto kroku vytvoříte databázi SQL v Azure. Po nasazení do Azure bude aplikace používat tuto cloudovou databázi.

Jako databáze SQL se v tomto kurzu používá [Azure SQL Database](/azure/sql-database/).

### <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

[!INCLUDE [Create resource group](../../includes/app-service-web-create-resource-group-no-h.md)]

### <a name="create-a-sql-database-logical-server"></a>Vytvoření logického serveru databáze SQL

V prostředí cloudu vytvořte logický server [`az sql server create`](/cli/azure/sql/server?view=azure-cli-latest#az-sql-server-create) sql database s příkazem.

Nahraďte zástupný symbol * \<server_name>* jedinečným názvem databáze SQL. Tento název se používá jako součást koncového bodu databáze SQL (`<server_name>.database.windows.net`), takže název musí být jedinečný v rámci všech logických serverů v Azure. Název smí obsahovat jen malá písmena, číslice a znak spojovníku (-) a musí mít délku 3 až 50 znaků. Také nahraďte * \<db_username>* a * \<db_password>* uživatelským jménem a heslem podle vašeho výběru. 


```azurecli-interactive
az sql server create --name <server_name> --resource-group myResourceGroup --location "West Europe" --admin-user <db_username> --admin-password <db_password>
```

Po vytvoření logického serveru databáze SQL se v rozhraní příkazového řádku Azure zobrazí podobné informace jako v následujícím příkladu:

<pre>
{
  "administratorLogin": "&lt;db_username&gt;",
  "administratorLoginPassword": null,
  "fullyQualifiedDomainName": "&lt;server_name&gt;.database.windows.net",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Sql/servers/&lt;server_name&gt;",
  "identity": null,
  "kind": "v12.0",
  "location": "westeurope",
  "name": "&lt;server_name&gt;",
  "resourceGroup": "myResourceGroup",
  "state": "Ready",
  "tags": null,
  "type": "Microsoft.Sql/servers",
  "version": "12.0"
}
</pre>

### <a name="configure-a-server-firewall-rule"></a>Konfigurace pravidla brány firewall serveru

Vytvoření [pravidla brány firewall na úrovni serveru služby Azure SQL Database](../sql-database/sql-database-firewall-configure.md) pomocí příkazu [`az sql server firewall create`](/cli/azure/sql/server/firewall-rule?view=azure-cli-latest#az-sql-server-firewall-rule-create). Pokud je jako počáteční i koncová adresa IP nastavená hodnota 0.0.0.0, je brána firewall otevřená jen pro ostatní prostředky Azure. 

```azurecli-interactive
az sql server firewall-rule create --resource-group myResourceGroup --server <server_name> --name AllowAllIps --start-ip-address 0.0.0.0 --end-ip-address 0.0.0.0
```

> [!TIP] 
> Pravidlo brány firewall můžete dál omezit [použitím jenom odchozích IP adres, které vaše aplikace používá](overview-inbound-outbound-ips.md#find-outbound-ips).
>

### <a name="create-a-database"></a>Vytvoření databáze

Vytvořte na serveru databázi s [úrovní výkonu S0](../sql-database/sql-database-service-tiers-dtu.md) pomocí příkazu [`az sql db create`](/cli/azure/sql/db?view=azure-cli-latest#az-sql-db-create).

```azurecli-interactive
az sql db create --resource-group myResourceGroup --server <server_name> --name coreDB --service-objective S0
```

### <a name="create-connection-string"></a>Vytvoření připojovacího řetězce

Nahraďte následující řetězec * \<server_name>*, * \<>db_username *a * \<>db_password,* které jste použili dříve.

```
Server=tcp:<server_name>.database.windows.net,1433;Database=coreDB;User ID=<db_username>;Password=<db_password>;Encrypt=true;Connection Timeout=30;
```

Toto je připojovací řetězec pro aplikaci .NET Core. Zkopírujte ho pro pozdější použití.

## <a name="deploy-app-to-azure"></a>Nasazení aplikace do Azure

V tomto kroku nasadíte aplikaci .NET Core připojenou k databázi SQL do služby App Service.

### <a name="configure-local-git-deployment"></a>Konfigurace nasazení místního gitu

[!INCLUDE [Configure a deployment user](../../includes/configure-deployment-user-no-h.md)]

### <a name="create-an-app-service-plan"></a>Vytvoření plánu služby App Service

[!INCLUDE [Create app service plan](../../includes/app-service-web-create-app-service-plan-no-h.md)]

### <a name="create-a-web-app"></a>Vytvoření webové aplikace

[!INCLUDE [Create web app](../../includes/app-service-web-create-web-app-dotnetcore-win-no-h.md)] 

### <a name="configure-connection-string"></a>Konfigurace připojovacího řetězce

Pokud chcete nastavit připojovací řetězce [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) pro vaši aplikaci Azure, použijte příkaz v Cloud Shellu. V následujícím příkazu * \< *nahraďte název aplikace>a * \<parametr connection_string>* připojovacím řetězcem, který jste vytvořili dříve.

```azurecli-interactive
az webapp config connection-string set --resource-group myResourceGroup --name <app name> --settings MyDbConnection="<connection_string>" --connection-string-type SQLServer
```

V ASP.NET core můžete použít tento`MyDbConnection`pojmenovaný připojovací řetězec ( ) pomocí standardního vzoru, stejně jako jakýkoli připojovací řetězec zadaný v *souboru appsettings.json*. V tomto `MyDbConnection` případě je také definována v *appsettings.json*. Při spuštění ve službě App Service má připojovací řetězec definovaný ve službě App Service přednost před připojovacím řetězcem definovaným ve vašem *appsettings.json*. Kód používá hodnotu *appsettings.json* během místního vývoje a stejný kód používá hodnotu Služby aplikace při nasazení.

Informace o tom, jak se v kódu odkazuje na připojovací řetězec, naleznete v [tématu Připojení k databázi SQL v produkčním prostředí](#connect-to-sql-database-in-production).

### <a name="configure-environment-variable"></a>Konfigurace proměnné prostředí

Dále nastavte proměnnou `ASPNETCORE_ENVIRONMENT` aplikace na hodnotu _Production_. Toto nastavení vám umožní zjistit, jestli běžíte v Azure, protože pro místní vývojové prostředí a databázi SQL pro prostředí Azure používáte SQLite.

Následující příklad konfiguruje `ASPNETCORE_ENVIRONMENT` nastavení aplikace v aplikaci Azure. Vyměňte * \<* zástupný symbol app_name>.

```azurecli-interactive
az webapp config appsettings set --name <app_name> --resource-group myResourceGroup --settings ASPNETCORE_ENVIRONMENT="Production"
```

Informace o tom, jak se v kódu odkazuje na proměnnou prostředí, naleznete v [tématu Připojení k databázi SQL v produkčním prostředí](#connect-to-sql-database-in-production).

### <a name="connect-to-sql-database-in-production"></a>Připojení k databázi SQL v produkčním prostředí

V místním úložišti otevřete Startup.cs a vyhledejte následující kód:

```csharp
services.AddDbContext<MyDatabaseContext>(options =>
        options.UseSqlite("Data Source=localdatabase.db"));
```

Nahraďte ho následujícím kódem, který používá proměnné prostředí, které jste nakonfigurovali dřív.

```csharp
// Use SQL Database if in Azure, otherwise, use SQLite
if(Environment.GetEnvironmentVariable("ASPNETCORE_ENVIRONMENT") == "Production")
    services.AddDbContext<MyDatabaseContext>(options =>
            options.UseSqlServer(Configuration.GetConnectionString("MyDbConnection")));
else
    services.AddDbContext<MyDatabaseContext>(options =>
            options.UseSqlite("Data Source=localdatabase.db"));

// Automatically perform database migration
services.BuildServiceProvider().GetService<MyDatabaseContext>().Database.Migrate();
```

Pokud tento kód zjistí, že běží v produkčním prostředí (což označuje prostředí Azure), použije připojovací řetězec, který jste nakonfigurovali pro připojení k databázi SQL.

Volání `Database.Migrate()` vám pomůže při spuštění v Azure, protože automaticky vytvoří databáze, které vaše aplikace .NET Core potřebuje, na základě konfigurace migrace. 

> [!IMPORTANT]
> U produkčních aplikací, které je třeba škálovat, postupujte podle osvědčených postupů [v části Použití migrace v produkčním prostředí](/aspnet/core/data/ef-rp/migrations#applying-migrations-in-production).
> 

Uložte provedené změny a potom je potvrďte v úložišti Gitu. 

```bash
git add .
git commit -m "connect to SQLDB in Azure"
```

### <a name="push-to-azure-from-git"></a>Přenos z Gitu do Azure

[!INCLUDE [app-service-plan-no-h](../../includes/app-service-web-git-push-to-azure-no-h.md)]

<pre>
Counting objects: 98, done.
Delta compression using up to 8 threads.
Compressing objects: 100% (92/92), done.
Writing objects: 100% (98/98), 524.98 KiB | 5.58 MiB/s, done.
Total 98 (delta 8), reused 0 (delta 0)
remote: Updating branch 'master'.
remote: .
remote: Updating submodules.
remote: Preparing deployment for commit id '0c497633b8'.
remote: Generating deployment script.
remote: Project file path: ./DotNetCoreSqlDb.csproj
remote: Generated deployment script files
remote: Running deployment command...
remote: Handling ASP.NET Core Web Application deployment.
remote: .
remote: .
remote: .
remote: Finished successfully.
remote: Running post deployment command(s)...
remote: Deployment successful.
remote: App container will begin restart within 10 seconds.
To https://&lt;app_name&gt;.scm.azurewebsites.net/&lt;app_name&gt;.git
 * [new branch]      master -> master
</pre>

### <a name="browse-to-the-azure-app"></a>Přejděte do aplikace Azure

Přejděte do nasazené aplikace pomocí webového prohlížeče.

```bash
http://<app_name>.azurewebsites.net
```

Přidejte několik položek úkolů.

![aplikace spuštěná ve službě App Service](./media/app-service-web-tutorial-dotnetcore-sqldb/azure-app-in-browser.png)

**Blahopřejeme!** Používáte aplikaci .NET Core řízenou daty ve službě App Service.

## <a name="update-locally-and-redeploy"></a>Místní aktualizace a opětovné nasazení

V tomto kroku provedete změnu schématu databáze a publikujete ji v Azure.

### <a name="update-your-data-model"></a>Aktualizace datového modelu

Otevřete _Models\Todo.cs_ v editoru kódu. Do třídy `ToDo` přidejte následující vlastnost:

```csharp
public bool Done { get; set; }
```

### <a name="run-code-first-migrations-locally"></a>Místní spuštění migrace Code First

Spuštěním několika příkazů aktualizujte místní databázi.

```bash
dotnet ef migrations add AddProperty
```

Aktualizujte místní databázi:

```bash
dotnet ef database update
```

### <a name="use-the-new-property"></a>Použití nové vlastnosti

Proveďte v kódu několik změn, aby aplikace využívala vlastnost `Done`. Pro zjednodušení budete v tomto kurzu měnit jen zobrazení `Index` a `Create`, aby se vlastnost projevila v praxi.

Otevřete _Controllers\TodosController.cs_.

Najděte metodu `Create([Bind("ID,Description,CreatedDate")] Todo todo)` a přidejte vlastnosti `Done` do seznamu vlastností v atributu `Bind`. Po dokončení bude podpis vaší metody `Create()` vypadat podobně jako následující kód:

```csharp
public async Task<IActionResult> Create([Bind("ID,Description,CreatedDate,Done")] Todo todo)
```

Otevřete _Views\Todos\Create.cshtml_.

V kódu Razor byste měli vidět prvek `<div class="form-group">` pro `Description`a pak další prvek `<div class="form-group">` pro `CreatedDate`. Přímo za tyto dva prvky přidejte další prvek `<div class="form-group">` pro `Done`:

```csharp
<div class="form-group">
    <label asp-for="Done" class="col-md-2 control-label"></label>
    <div class="col-md-10">
        <input asp-for="Done" class="form-control" />
        <span asp-validation-for="Done" class="text-danger"></span>
    </div>
</div>
```

Otevřete _Views\Todos\Index.cshtml_.

Vyhledejte prázdný prvek `<th></th>`. Přímo nad tento prvek přidejte následující kód Razor:

```csharp
<th>
    @Html.DisplayNameFor(model => model.Done)
</th>
```

Najděte prvek `<td>`, který obsahuje pomocné rutiny značky `asp-action`. Přímo nad tento prvek přidejte následující kód Razor:

```csharp
<td>
    @Html.DisplayFor(modelItem => item.Done)
</td>
```

To je všechno, co potřebujete, aby se v zobrazení `Index` a `Create` projevily změny.

### <a name="test-your-changes-locally"></a>Místní test provedených změn

Spusťte aplikaci místně.

```bash
dotnet run
```

V prohlížeči přejděte na adresu `http://localhost:5000/`. Teď můžete přidat položku úkolu a zaškrtnout možnost **Hotovo**. Položka by se měla zobrazit na domovské stránce jako dokončená. Nezapomeňte, že v zobrazení `Edit` se pole `Done` nezobrazí, protože jste zobrazení `Edit` nezměnili.

### <a name="publish-changes-to-azure"></a>Publikování změn v Azure

```bash
git add .
git commit -m "added done field"
git push azure master
```

`git push` Po dokončení přejděte do aplikace App Service a zkuste přidat položku, kterou chcete provést, a zaškrtněte **políčko Hotovo**.

![Aplikace Azure po první migraci kódu](./media/app-service-web-tutorial-dotnetcore-sqldb/this-one-is-done.png)

Všechny vaše existující položky úkolů jsou nadále zobrazené. Při opětovném publikování aplikace .NET Core se neztratí existující data v databázi SQL. Migrace Entity Framework Core také změní jen datové schéma, ale existující data ponechá beze změny.

## <a name="stream-diagnostic-logs"></a>Streamování diagnostických protokolů

Zatímco aplikace ASP.NET Core běží ve službě Azure App Service, můžete získat protokoly konzoly potrubím do cloudového prostředí. Tímto způsobem můžete získat stejné diagnostické zprávy, které vám pomůžou ladit chyby aplikace.

Ukázkový projekt se již řídí pokyny v [ASP.NET základní protokolování v Azure](https://docs.microsoft.com/aspnet/core/fundamentals/logging#azure-app-service-provider) se dvěma změnami konfigurace:

- Obsahuje odkaz `Microsoft.Extensions.Logging.AzureAppServices` na *do dotNetCoreSqlDb.csproj*.
- Volání `loggerFactory.AddAzureWebAppDiagnostics()` v *Program.cs*.

Chcete-li nastavit [úroveň protokolu](https://docs.microsoft.com/aspnet/core/fundamentals/logging#log-level) ASP.NET `Information` jádra `Error`ve službě [`az webapp log config`](/cli/azure/webapp/log?view=azure-cli-latest#az-webapp-log-config) App Service na výchozí úroveň , použijte příkaz v prostředí Cloud Shell.

```azurecli-interactive
az webapp log config --name <app_name> --resource-group myResourceGroup --application-logging true --level information
```

> [!NOTE]
> Úroveň protokolu projektu je již `Information` nastavena na *appsettings.json*.
> 

Chcete-li spustit streamování [`az webapp log tail`](/cli/azure/webapp/log?view=azure-cli-latest#az-webapp-log-tail) protokolů, použijte příkaz v prostředí Cloud Shell.

```azurecli-interactive
az webapp log tail --name <app_name> --resource-group myResourceGroup
```

Po spuštění streamování protokolu aktualizujte aplikaci Azure v prohlížeči, abyste získali nějaký webový provoz. Teď se zobrazí protokoly konzoly směrované do terminálu. Pokud nevidíte protokoly konzoly okamžitě, podívejte se znovu za 30 sekund.

Chcete-li kdykoli zastavit streamování `Ctrl` + `C`protokolů, zadejte příkaz .

Další informace o přizpůsobení protokolů jádra ASP.NET naleznete [v tématu Protokolování ASP.NET jádra](https://docs.microsoft.com/aspnet/core/fundamentals/logging).

## <a name="manage-your-azure-app"></a>Správa aplikace Azure

Pokud chcete zobrazit aplikaci, kterou jste [vytvořili,](https://portal.azure.com)vyhledejte a vyberte **App Services**.

![Výběr služby App Services na webu Azure Portal](./media/app-service-web-tutorial-dotnetcore-sqldb/app-services.png)

Na stránce **App Services** vyberte název aplikace Azure.

![Přechod do aplikace Azure na portálu](./media/app-service-web-tutorial-dotnetcore-sqldb/access-portal.png)

Ve výchozím nastavení se na portálu zobrazuje stránka **Přehled** vaší aplikace. Tato stránka poskytuje přehled, jak si vaše aplikace stojí. Tady můžete také provést základní úlohy správy, jako je procházení, zastavení, spuštění, restartování a odstranění. Na levé straně stránky jsou zobrazeny různé konfigurační stránky, které můžete otevřít.

![Stránka služby App Service na webu Azure Portal](./media/app-service-web-tutorial-dotnetcore-sqldb/web-app-blade.png)

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

<a name="next"></a>
## <a name="next-steps"></a>Další kroky

Naučili jste se:

> [!div class="checklist"]
> * Vytvořit databázi SQL v Azure
> * Připojit aplikaci .NET Core k databázi SQL
> * Nasadit aplikaci do Azure
> * Aktualizovat datový model a znovu nasadit aplikaci
> * Streamovat protokoly z Azure do terminálu
> * Spravovat aplikaci na webu Azure Portal

Přejdete k dalšímu kurzu, kde se dozvíte, jak namapovat vlastní název DNS do aplikace.

> [!div class="nextstepaction"]
> [Mapování existujícího vlastního názvu DNS na službu Azure App Service](app-service-web-tutorial-custom-domain.md)
