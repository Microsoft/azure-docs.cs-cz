---
title: 'Kurz: ASP.NET Core s Azure SQL Database'
description: Naučte se, jak získat aplikaci .NET Core pracující v Azure App Service s připojením k Azure SQL Database.
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 06/20/2020
ms.custom: devx-track-csharp, mvc, cli-validate, seodec18, devx-track-azurecli
zone_pivot_groups: app-service-platform-windows-linux
ms.openlocfilehash: 52a5b127312ef979791d17b27ca67b21a779e310
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/20/2021
ms.locfileid: "107765721"
---
# <a name="tutorial-build-an-aspnet-core-and-azure-sql-database-app-in-azure-app-service"></a>Kurz: Vytvoření aplikace ASP.NET Core a Azure SQL Database v Azure App Service

::: zone pivot="platform-windows"  

[Azure App Service](overview.md) poskytuje vysoce škálovatelnou službu s automatickými opravami pro hostování webů v Azure. V tomto kurzu se dozvíte, jak vytvořit aplikaci .NET Core a jak ji připojit k SQL Database. Až budete hotovi, budete mít aplikaci .NET Core MVC spuštěnou v App Service ve Windows.

::: zone-end

::: zone pivot="platform-linux"

[Azure App Service](overview.md) poskytuje vysoce škálovatelnou službu s automatickými opravami pro hostování webů pomocí operačního systému Linux. V tomto kurzu se dozvíte, jak vytvořit aplikaci .NET Core a jak ji připojit k SQL Database. Po dokončení budete mít aplikaci .NET Core MVC spuštěnou ve službě App Service v Linuxu.

::: zone-end

![aplikace spuštěná ve službě App Service](./media/tutorial-dotnetcore-sqldb-app/azure-app-in-browser.png)

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvořit databázi SQL v Azure
> * Připojit aplikaci .NET Core k databázi SQL
> * Nasadit aplikaci do Azure
> * Aktualizovat datový model a znovu nasadit aplikaci
> * Streamovat diagnostické protokoly z Azure
> * Spravovat aplikaci na webu Azure Portal

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Požadavky

Pro absolvování tohoto kurzu potřebujete:

- <a href="https://git-scm.com/" target="_blank">Nainstalovat Git</a>.
- <a href="https://dotnet.microsoft.com/download/dotnet-core/3.1" target="_blank">Instalace nejnovější sady .NET Core 3,1 SDK</a>

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

## <a name="create-local-net-core-app"></a>Vytvoření místní aplikace .NET Core

V tomto kroku nastavíte místní projekt .NET Core.

### <a name="clone-the-sample-application"></a>Klonování ukázkové aplikace

V okně terminálu přejděte pomocí příkazu `cd` do pracovního adresáře.

Spuštěním následujících příkazů naklonujte ukázkové úložiště a přejděte do jeho kořenové složky.

```bash
git clone https://github.com/azure-samples/dotnetcore-sqldb-tutorial
cd dotnetcore-sqldb-tutorial
```

Ukázkový projekt obsahuje základní aplikaci CRUD (vytváření-čtení-aktualizace-odstraňování) pomocí [Entity Framework Core](/ef/core/).

### <a name="run-the-application"></a>Spuštění aplikace

Spuštěním následujících příkazů nainstalujte požadované balíčky, spusťte operace migrace databáze a spusťte aplikaci.

```bash
dotnet tool install -g dotnet-ef
dotnet ef database update
dotnet run
```

V prohlížeči přejděte na `http://localhost:5000`. Vyberte odkaz **Vytvořit nový** a vytvořte několik položek _úkolů_.

![úspěšné připojení k databázi SQL](./media/tutorial-dotnetcore-sqldb-app/local-app-in-browser.png)

.NET Core můžete kdykoli zastavit stisknutím `Ctrl+C` v terminálu.

## <a name="create-production-sql-database"></a>Vytvoření produkční databáze SQL

V tomto kroku vytvoříte databázi SQL v Azure. Po nasazení do Azure bude aplikace používat tuto cloudovou databázi.

Jako databáze SQL se v tomto kurzu používá [Azure SQL Database](/azure/sql-database/).

### <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

[!INCLUDE [Create resource group](../../includes/app-service-web-create-resource-group-no-h.md)]

### <a name="create-a-sql-database-logical-server"></a>Vytvoření logického serveru databáze SQL

V Cloud Shell vytvořte pomocí příkazu SQL Database logický server [`az sql server create`](/cli/azure/sql/server#az_sql_server_create) .

*\<server-name>* Zástupný text nahraďte *jedinečným* názvem SQL Database. Tento název se používá jako součást globálně jedinečného SQL Databaseho koncového bodu, `<server-name>.database.windows.net` . Platné znaky jsou `a` - `z` , `0` - `9` , `-` . Také nahraďte *\<db-username>* a zadejte *\<db-password>* uživatelské jméno a heslo podle vašeho výběru. 


```azurecli-interactive
az sql server create --name <server-name> --resource-group myResourceGroup --location "West Europe" --admin-user <db-username> --admin-password <db-password>
```

Po vytvoření logického serveru databáze SQL se v rozhraní příkazového řádku Azure zobrazí podobné informace jako v následujícím příkladu:

<pre>
{
  "administratorLogin": "&lt;db-username&gt;",
  "administratorLoginPassword": null,
  "fullyQualifiedDomainName": "&lt;server-name&gt;.database.windows.net",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Sql/servers/&lt;server-name&gt;",
  "identity": null,
  "kind": "v12.0",
  "location": "westeurope",
  "name": "&lt;server-name&gt;",
  "resourceGroup": "myResourceGroup",
  "state": "Ready",
  "tags": null,
  "type": "Microsoft.Sql/servers",
  "version": "12.0"
}
</pre>

### <a name="configure-a-server-firewall-rule"></a>Konfigurace pravidla brány firewall serveru

Vytvoření [pravidla brány firewall na úrovni serveru služby Azure SQL Database](../azure-sql/database/firewall-configure.md) pomocí příkazu [`az sql server firewall create`](/cli/azure/sql/server/firewall-rule#az_sql_server_firewall_rule_create). Pokud je jako počáteční i koncová adresa IP nastavená hodnota 0.0.0.0, je brána firewall otevřená jen pro ostatní prostředky Azure. 

```azurecli-interactive
az sql server firewall-rule create --resource-group myResourceGroup --server <server-name> --name AllowAzureIps --start-ip-address 0.0.0.0 --end-ip-address 0.0.0.0
```

> [!TIP] 
> Pravidlo brány firewall můžete dál omezit [použitím jenom odchozích IP adres, které vaše aplikace používá](overview-inbound-outbound-ips.md#find-outbound-ips).
>

V Cloud Shell znovu spusťte příkaz a umožněte přístup z místního počítače nahrazením *\<your-ip-address>* [místní IP adresy IPv4](https://www.whatsmyip.org/).

```azurecli-interactive
az sql server firewall-rule create --name AllowLocalClient --server <server-name> --resource-group myResourceGroup --start-ip-address=<your-ip-address> --end-ip-address=<your-ip-address>
```

### <a name="create-a-database"></a>Vytvoření databáze

Vytvořte na serveru databázi s [úrovní výkonu S0](../azure-sql/database/service-tiers-dtu.md) pomocí příkazu [`az sql db create`](/cli/azure/sql/db#az_sql_db_create).

```azurecli-interactive
az sql db create --resource-group myResourceGroup --server <server-name> --name coreDB --service-objective S0
```

### <a name="create-connection-string"></a>Vytvoření připojovacího řetězce

Pomocí příkazu Získejte připojovací řetězec [`az sql db show-connection-string`](/cli/azure/sql/db#az_sql_db_show_connection_string) .

```azurecli-interactive
az sql db show-connection-string --client ado.net --server <server-name> --name coreDB
```

Ve výstupu příkazu nahraďte *\<username>* a *\<password>* s přihlašovacími údaji správce databáze, které jste použili dříve.

Toto je připojovací řetězec pro aplikaci .NET Core. Zkopírujte ho pro pozdější použití.

### <a name="configure-app-to-connect-to-production-database"></a>Konfigurace aplikace pro připojení k provozní databázi

V místním úložišti otevřete Startup.cs a vyhledejte následující kód:

```csharp
services.AddDbContext<MyDatabaseContext>(options =>
        options.UseSqlite("Data Source=localdatabase.db"));
```

Nahraďte následujícím kódem.

```csharp
services.AddDbContext<MyDatabaseContext>(options =>
        options.UseSqlServer(Configuration.GetConnectionString("MyDbConnection")));
```

> [!IMPORTANT]
> U produkčních aplikací, které se musí škálovat, postupujte podle osvědčených postupů při [použití migrace v produkčním](/aspnet/core/data/ef-rp/migrations#applying-migrations-in-production)prostředí.
> 

### <a name="run-database-migrations-to-the-production-database"></a>Spuštění migrace databáze do provozní databáze

Vaše aplikace se aktuálně připojuje k místní databázi SQLite. Teď, když jste nakonfigurovali Azure SQL Database, vytvořte novou migraci, abyste ji mohli cílit. 

V kořenovém adresáři úložiště spusťte následující příkazy. Nahraďte *\<connection-string>* připojovacím řetězcem, který jste vytvořili dříve.

```
# Delete old migrations
rm -r Migrations
# Recreate migrations
dotnet ef migrations add InitialCreate

# Set connection string to production database
# PowerShell
$env:ConnectionStrings:MyDbConnection="<connection-string>"
# CMD (no quotes)
set ConnectionStrings:MyDbConnection=<connection-string>
# Bash (no quotes)
export ConnectionStrings__MyDbConnection=<connection-string>

# Run migrations
dotnet ef database update
```

### <a name="run-app-with-new-configuration"></a>Spustit aplikaci s novou konfigurací

Teď, když jsou migrace databáze spuštěné v provozní databázi, otestujte aplikaci spuštěním:

```
dotnet run
```

V prohlížeči přejděte na `http://localhost:5000`. Vyberte odkaz **Vytvořit nový** a vytvořte několik položek _úkolů_. Vaše aplikace teď čte a zapisuje data do provozní databáze.

Potvrďte své místní změny a pak je potvrďte do úložiště Git. 

```bash
git add .
git commit -m "connect to SQLDB in Azure"
```

Teď jste připraveni nasadit svůj kód.

## <a name="deploy-app-to-azure"></a>Nasazení aplikace do Azure

V tomto kroku nasadíte aplikaci .NET Core připojenou k databázi SQL do služby App Service.

### <a name="configure-local-git-deployment"></a>Konfigurace nasazení místního gitu

[!INCLUDE [Configure a deployment user](../../includes/configure-deployment-user-no-h.md)]

### <a name="create-an-app-service-plan"></a>Vytvoření plánu služby App Service

::: zone pivot="platform-windows"  

[!INCLUDE [Create app service plan](../../includes/app-service-web-create-app-service-plan-no-h.md)]

::: zone-end

::: zone pivot="platform-linux"

[!INCLUDE [Create app service plan](../../includes/app-service-web-create-app-service-plan-linux-no-h.md)]

::: zone-end

### <a name="create-a-web-app"></a>Vytvoření webové aplikace

::: zone pivot="platform-windows"  

[!INCLUDE [Create web app](../../includes/app-service-web-create-web-app-dotnetcore-win-no-h.md)]

::: zone-end

::: zone pivot="platform-linux"

[!INCLUDE [Create web app](../../includes/app-service-web-create-web-app-dotnetcore-linux-no-h.md)]

::: zone-end

### <a name="configure-connection-string"></a>Konfigurace připojovacího řetězce

K nastavení připojovacích řetězců pro aplikaci Azure použijte [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings#az_webapp_config_appsettings_set) příkaz v Cloud Shell. V následujícím příkazu nahraďte a *\<app-name>* *\<connection-string>* parametr připojovacím řetězcem, který jste vytvořili dříve.

```azurecli-interactive
az webapp config connection-string set --resource-group myResourceGroup --name <app-name> --settings MyDbConnection="<connection-string>" --connection-string-type SQLAzure
```

V ASP.NET Core můžete použít tento pojmenovaný připojovací řetězec ( `MyDbConnection` ) pomocí standardního vzoru, jako je libovolný připojovací řetězec zadaný v *appsettings.js*. V tomto případě `MyDbConnection` je také definováno v *appsettings.js* v. Při spuštění v App Service má připojovací řetězec definovaný v App Service přednost před připojovacím řetězcem definovaným v *appsettings.js*. Kód používá *appsettings.jsna* hodnotu během místního vývoje a stejný kód používá hodnotu App Service při nasazení.

Pokud chcete zjistit, jak se na připojovací řetězec odkazuje v kódu, přečtěte si téma [Konfigurace aplikace pro připojení k provozní databázi](#configure-app-to-connect-to-production-database).

### <a name="push-to-azure-from-git"></a>Přenos z Gitu do Azure

::: zone pivot="platform-windows"  

[!INCLUDE [push-to-azure-no-h](../../includes/app-service-web-git-push-to-azure-no-h.md)]

<pre>
Enumerating objects: 268, done.
Counting objects: 100% (268/268), done.
Compressing objects: 100% (171/171), done.
Writing objects: 100% (268/268), 1.18 MiB | 1.55 MiB/s, done.
Total 268 (delta 95), reused 251 (delta 87), pack-reused 0
remote: Resolving deltas: 100% (95/95), done.
remote: Updating branch 'main'.
remote: Updating submodules.
remote: Preparing deployment for commit id '64821c3558'.
remote: Generating deployment script.
remote: Project file path: .\DotNetCoreSqlDb.csproj
remote: Generating deployment script for ASP.NET MSBuild16 App
remote: Generated deployment script files
remote: Running deployment command...
remote: Handling ASP.NET Core Web Application deployment with MSBuild16.
remote: .
remote: .
remote: .
remote: Finished successfully.
remote: Running post deployment command(s)...
remote: Triggering recycle (preview mode disabled).
remote: App container will begin restart within 10 seconds.
To https://&lt;app-name&gt;.scm.azurewebsites.net/&lt;app-name&gt;.git
 * [new branch]      main -> main
</pre>

::: zone-end

::: zone pivot="platform-linux"

[!INCLUDE [push-to-azure-no-h](../../includes/app-service-web-git-push-to-azure-no-h.md)]

<pre>
Enumerating objects: 273, done.
Counting objects: 100% (273/273), done.
Delta compression using up to 4 threads
Compressing objects: 100% (175/175), done.
Writing objects: 100% (273/273), 1.19 MiB | 1.85 MiB/s, done.
Total 273 (delta 96), reused 259 (delta 88)
remote: Resolving deltas: 100% (96/96), done.
remote: Deploy Async
remote: Updating branch 'main'.
remote: Updating submodules.
remote: Preparing deployment for commit id 'cccecf86c5'.
remote: Repository path is /home/site/repository
remote: Running oryx build...
remote: Build orchestrated by Microsoft Oryx, https://github.com/Microsoft/Oryx
remote: You can report issues at https://github.com/Microsoft/Oryx/issues
remote: .
remote: .
remote: .
remote: Done.
remote: Running post deployment command(s)...
remote: Triggering recycle (preview mode disabled).
remote: Deployment successful.
remote: Deployment Logs : 'https://&lt;app-name&gt;.scm.azurewebsites.net/newui/jsonviewer?view_url=/api/deployments/cccecf86c56493ffa594e76ea1deb3abb3702d89/log'
To https://&lt;app-name&gt;.scm.azurewebsites.net/&lt;app-name&gt;.git
 * [new branch]      main -> main
</pre>

::: zone-end

### <a name="browse-to-the-azure-app"></a>Přejít k aplikaci Azure

Přejděte do nasazené aplikace pomocí webového prohlížeče.

```bash
http://<app-name>.azurewebsites.net
```

Přidejte několik položek úkolů.

![aplikace spuštěná ve službě App Service](./media/tutorial-dotnetcore-sqldb-app/azure-app-in-browser.png)

**Blahopřejeme!** Používáte aplikaci .NET Core řízenou daty ve službě App Service.

## <a name="update-locally-and-redeploy"></a>Místní aktualizace a opětovné nasazení

V tomto kroku provedete změnu schématu databáze a publikujete ji v Azure.

### <a name="update-your-data-model"></a>Aktualizace datového modelu

Otevřete _modely/todo. cs_ v editoru kódu. Do třídy `ToDo` přidejte následující vlastnost:

```csharp
public bool Done { get; set; }
```

### <a name="rerun-database-migrations"></a>Znovu spustit migrace databáze

Spusťte několik příkazů, abyste provedli aktualizace provozní databáze.

```bash
dotnet ef migrations add AddProperty
dotnet ef database update
```

> [!NOTE]
> Pokud otevřete nové okno terminálu, budete muset připojovací řetězec nastavit na provozní databázi v terminálu, stejně jako při [spuštění migrace databáze do provozní databáze](#run-database-migrations-to-the-production-database).
>

### <a name="use-the-new-property"></a>Použití nové vlastnosti

Proveďte v kódu několik změn, aby aplikace využívala vlastnost `Done`. Pro zjednodušení budete v tomto kurzu měnit jen zobrazení `Index` a `Create`, aby se vlastnost projevila v praxi.

Otevřete _Controllers/TodosController. cs_.

Najděte metodu `Create([Bind("ID,Description,CreatedDate")] Todo todo)` a přidejte vlastnosti `Done` do seznamu vlastností v atributu `Bind`. Po dokončení bude podpis vaší metody `Create()` vypadat podobně jako následující kód:

```csharp
public async Task<IActionResult> Create([Bind("ID,Description,CreatedDate,Done")] Todo todo)
```

Otevřete _zobrazení/TODO/vytvořit. cshtml_.

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

Otevřete _zobrazení/todoe/index. cshtml_.

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

> [!NOTE]
> Pokud otevřete nové okno terminálu, budete muset připojovací řetězec nastavit na provozní databázi v terminálu, stejně jako při [spuštění migrace databáze do provozní databáze](#run-database-migrations-to-the-production-database).
>

V prohlížeči přejděte na adresu `http://localhost:5000/`. Teď můžete přidat položku úkolu a zaškrtnout možnost **Hotovo**. Položka by se měla zobrazit na domovské stránce jako dokončená. Nezapomeňte, že v zobrazení `Edit` se pole `Done` nezobrazí, protože jste zobrazení `Edit` nezměnili.

### <a name="publish-changes-to-azure"></a>Publikování změn v Azure

```bash
git add .
git commit -m "added done field"
git push azure main
```

Až `git push` to bude hotové, přejděte do aplikace App Service a zkuste přidat položku úkoly a vrátit se k ní .

![Aplikace Azure po Code First migraci](./media/tutorial-dotnetcore-sqldb-app/this-one-is-done.png)

Všechny vaše existující položky úkolů jsou nadále zobrazené. Při opětovném publikování ASP.NET Core aplikace nebudou ztracena stávající data v SQL Database. Migrace Entity Framework Core také změní jen datové schéma, ale existující data ponechá beze změny.

## <a name="stream-diagnostic-logs"></a>Streamování diagnostických protokolů

I když je aplikace ASP.NET Core spuštěná v Azure App Service, můžete získat protokoly konzoly, které budou směrované do Cloud Shell. Tímto způsobem můžete získat stejné diagnostické zprávy, které vám pomůžou ladit chyby aplikace.

Vzorový projekt již postupuje podle pokynů [ASP.NET Core protokolování v Azure](/aspnet/core/fundamentals/logging#azure-app-service-provider) se dvěma změnami konfigurace:

- Obsahuje odkaz na `Microsoft.Extensions.Logging.AzureAppServices` v *DotNetCoreSqlDb. csproj*.
- Volání `loggerFactory.AddAzureWebAppDiagnostics()` v *programu program. cs*.

Chcete-li nastavit [úroveň protokolu](/aspnet/core/fundamentals/logging#log-level) ASP.NET Core v App Service na `Information` z výchozí úrovně `Error` , použijte [`az webapp log config`](/cli/azure/webapp/log#az_webapp_log_config) příkaz v Cloud Shell.

```azurecli-interactive
az webapp log config --name <app-name> --resource-group myResourceGroup --application-logging filesystem --level information
```

> [!NOTE]
> Úroveň protokolu projektu je již `Information` v *appsettings.jsnastavena na*.

Chcete-li spustit streamování protokolů, použijte [`az webapp log tail`](/cli/azure/webapp/log#az_webapp_log_tail) příkaz v Cloud Shell.

```azurecli-interactive
az webapp log tail --name <app-name> --resource-group myResourceGroup
```

Po spuštění streamování protokolů aktualizujte aplikaci Azure v prohlížeči, abyste získali nějaký webový provoz. Teď se zobrazí protokoly konzoly směrované do terminálu. Pokud nevidíte protokoly konzoly okamžitě, podívejte se znovu za 30 sekund.

Streamování protokolů můžete kdykoli zastavit zadáním `Ctrl`+`C`.

Další informace o přizpůsobení protokolů ASP.NET Core najdete v tématu věnovaném [přihlášení ASP.NET Core](/aspnet/core/fundamentals/logging).

## <a name="manage-your-azure-app"></a>Správa aplikace Azure

Aplikaci, kterou jste vytvořili, zobrazíte tak, že v [Azure Portal](https://portal.azure.com)vyhledáte a vyberete **App Services**.

![Vyberte App Services v Azure Portal](./media/tutorial-dotnetcore-sqldb-app/app-services.png)

Na stránce **App Services** vyberte název aplikace Azure.

![Přechod do aplikace Azure na portálu](./media/tutorial-dotnetcore-sqldb-app/access-portal.png)

Ve výchozím nastavení se na portálu zobrazí stránka s **přehledem** vaší aplikace. Tato stránka poskytuje přehled, jak si vaše aplikace stojí. Tady můžete také provést základní úlohy správy, jako je procházení, zastavení, spuštění, restartování a odstranění. Karty na levé straně stránky obsahují různé stránky konfigurace, které můžete otevřít.

![Stránka služby App Service na webu Azure Portal](./media/tutorial-dotnetcore-sqldb-app/web-app-blade.png)

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

Přejděte k dalšímu kurzu, kde se dozvíte, jak namapovat vlastní název DNS na svou aplikaci.

> [!div class="nextstepaction"]
> [Kurz: mapování vlastního názvu DNS na aplikaci](app-service-web-tutorial-custom-domain.md)

Nebo si prohlédněte další zdroje informací:

> [!div class="nextstepaction"]
> [Konfigurace aplikace ASP.NET Core](configure-language-dotnetcore.md)
