---
title: Zabezpečené SQL Database připojení ke spravované identitě – Azure App Service | Microsoft Docs
description: Naučte se, jak zajistit bezpečnější připojení k databázi pomocí spravované identity a jak ji použít pro jiné služby Azure.
services: app-service\web
documentationcenter: dotnet
author: cephalin
manager: syntaxc4
editor: ''
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 08/06/2019
ms.author: cephalin
ms.custom: mvc
ms.openlocfilehash: 8a0b974e9b64d477e53c37757b4f2fa952befba2
ms.sourcegitcommit: 388c8f24434cc96c990f3819d2f38f46ee72c4d8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/27/2019
ms.locfileid: "70061859"
---
# <a name="tutorial-secure-azure-sql-database-connection-from-app-service-using-a-managed-identity"></a>Kurz: Zabezpečené Azure SQL Database připojení z App Service pomocí spravované identity

[App Service ](overview.md) je vysoce škálovatelná služba s automatickými opravami pro hostování webů v Azure. Poskytuje také [spravovanou identitu](overview-managed-identity.md) pro vaši aplikaci, což je řešení na klíč pro zabezpečení přístupu ke službě [Azure SQL Database](/azure/sql-database/) a dalším službám Azure. Spravované identity ve službě App Service zvyšují zabezpečení vaší aplikace tím, že z aplikace odstraňují tajné kódy, jako jsou přihlašovací údaje v připojovacích řetězcích. V tomto kurzu přidáte spravovanou identitu do ukázkové webové aplikace, kterou jste vytvořili v jednom z následujících kurzů: 

- [Kurz: Vytvoření aplikace v ASP.NET v Azure pomocí SQL Database](app-service-web-tutorial-dotnet-sqldatabase.md)
- [Kurz: Vytvoření aplikace ASP.NET Core a SQL Database v Azure App Service](app-service-web-tutorial-dotnetcore-sqldb.md)

Až budete hotovi, vaše ukázková aplikace se bezpečně připojí ke službě SQL Database bez potřeby uživatelského jména a hesla.

> [!NOTE]
> Postup popsaný v tomto kurzu podporuje následující verze:
> 
> - .NET Framework 4.7.2 a vyšší.
> - .NET Core 2,2 a vyšší.
>

Co se naučíte:

> [!div class="checklist"]
> * Povolení spravovaných identit
> * Udělit přístup ke spravované identitě službě SQL Database
> * Konfigurace Entity Framework pro použití ověřování Azure AD s SQL Database
> * Připojení k SQL Database ze sady Visual Studio pomocí ověřování Azure AD

> [!NOTE]
>Ověřování Azure AD se _liší_ od [integrovaného ověřování Windows](/previous-versions/windows/it-pro/windows-server-2003/cc758557(v=ws.10)) v místní službě Active Directory (služba AD DS). Služba AD DS a Azure AD používají zcela jiné ověřovací protokoly. Další informace najdete v tématu [dokumentace ke službě Azure AD Domain Services](https://docs.microsoft.com/azure/active-directory-domain-services/).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Požadavky

Tento článek bude pokračovat tam, kde jste [v kurzu přestali: Vytvoření aplikace v ASP.NET v Azure pomocí SQL Database](app-service-web-tutorial-dotnet-sqldatabase.md) nebo [kurzu: Sestavte ASP.NET Core a SQL Database aplikaci v](app-service-web-tutorial-dotnetcore-sqldb.md)Azure App Service. Pokud jste to ještě neudělali, Projděte si jeden ze dvou kurzů jako první. Alternativně můžete upravit postup pro vlastní aplikaci .NET pomocí SQL Database.

Pokud chcete aplikaci ladit pomocí SQL Database jako back-end, ujistěte se, že jste povolili připojení klienta z počítače. V takovém případě přidejte IP adresu klienta podle kroků v části [Správa pravidel brány firewall na úrovni serveru pomocí Azure Portal](../sql-database/sql-database-firewall-configure.md#manage-server-level-ip-firewall-rules-using-the-azure-portal).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="grant-database-access-to-azure-ad-user"></a>Udělení přístupu k databázi uživateli Azure AD

Nejdřív povolte ověřování Azure AD, které se SQL Database přiřazením uživatele Azure AD jako správce služby Active Directory serveru SQL Database. Tento uživatel se liší od účet Microsoft, které jste použili k registraci předplatného Azure. Musí se jednat o uživatele, kterého jste vytvořili, importovali, synchronizoval nebo pozvali do služby Azure AD. Další informace o povolených uživatelích Azure AD najdete v tématu [funkce a omezení služby Azure AD v SQL Database](../sql-database/sql-database-aad-authentication.md#azure-ad-features-and-limitations).

Pokud váš tenant služby Azure AD ještě nemá uživatele, vytvořte ho podle kroků v části [Přidání nebo odstranění uživatelů pomocí Azure Active Directory](../active-directory/fundamentals/add-users-azure-active-directory.md).

Vyhledejte ID objektu uživatele Azure AD pomocí [`az ad user list`](/cli/azure/ad/user?view=azure-cli-latest#az-ad-user-list) služby a nahraďte  *\<User-Principal-Name >* . Výsledek je uložen do proměnné.

```azurecli-interactive
azureaduser=$(az ad user list --filter "userPrincipalName eq '<user-principal-name>'" --query [].objectId --output tsv)
```
> [!TIP]
> Pokud chcete zobrazit seznam všech hlavních názvů uživatelů v Azure AD, spusťte `az ad user list --query [].userPrincipalName`.
>

Přidejte tohoto uživatele Azure AD jako správce služby Active Directory pomocí [`az sql server ad-admin create`](/cli/azure/sql/server/ad-admin?view=azure-cli-latest#az-sql-server-ad-admin-create) příkazu ve Cloud Shell. V následujícím příkazu nahraďte  *\<> název serveru* `.database.windows.net` názvem SQL Database serveru (bez přípony).

```azurecli-interactive
az sql server ad-admin create --resource-group myResourceGroup --server-name <server-name> --display-name ADMIN --object-id $azureaduser
```

Další informace o přidání správce služby Active Directory najdete v tématu [zřízení správce Azure Active Directory pro Server Azure SQL Database](../sql-database/sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server) .

## <a name="set-up-visual-studio"></a>Nastavení sady Visual Studio

Pokud chcete povolit vývoj a ladění v aplikaci Visual Studio, přidejte uživatele služby Azure AD v aplikaci Visual Studio tak, že v nabídce vyberete**nastavení účet** **soubor** > a kliknete na **Přidat účet**.

Pokud chcete nastavit uživatele Azure AD pro ověřování služby Azure, v nabídce vyberte**Možnosti** **nástrojů** > a pak vyberte**Výběr účtu** >  **ověřování služby Azure**. Vyberte uživatele Azure AD, kterého jste přidali, a klikněte na **OK**.

Nyní jste připraveni vyvíjet a ladit svou aplikaci pomocí SQL Database jako back-endu pomocí ověřování Azure AD.

## <a name="modify-your-project"></a>Úprava projektu

Kroky, které provedete pro váš projekt, závisí na tom, zda se jedná o projekt ASP.NET nebo projekt ASP.NET Core.

- [Upravit ASP.NET](#modify-aspnet)
- [Upravit ASP.NET Core](#modify-aspnet-core)

### <a name="modify-aspnet"></a>Upravit ASP.NET

V aplikaci Visual Studio otevřete konzolu Správce balíčků a přidejte balíček NuGet [Microsoft. Azure. Services. AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication):

```powershell
Install-Package Microsoft.Azure.Services.AppAuthentication -Version 1.3.0
```

V souboru *Web. config*pracujete v horní části souboru a proveďte následující změny:

- V `<configSections>`přidejte do něj následující deklaraci oddílu:

    ```xml
    <section name="SqlAuthenticationProviders" type="System.Data.SqlClient.SqlAuthenticationProviderConfigurationSection, System.Data, Version=4.0.0.0, Culture=neutral, PublicKeyToken=b77a5c561934e089" />
    ```

- pod uzavírací `</configSections>` značku přidejte následující kód XML pro `<SqlAuthenticationProviders>`.

    ```xml
    <SqlAuthenticationProviders>
      <providers>
        <add name="Active Directory Interactive" type="Microsoft.Azure.Services.AppAuthentication.SqlAppAuthenticationProvider, Microsoft.Azure.Services.AppAuthentication" />
      </providers>
    </SqlAuthenticationProviders>
    ```    

- Vyhledejte připojovací řetězec s `MyDbConnection` `"server=tcp:<server-name>.database.windows.net;database=<db-name>;UID=AnyString;Authentication=Active Directory Interactive"`názvem a nahraďte `connectionString` jeho hodnotu hodnotou. Nahraďte  _\<název serveru >_ a  _\<DB-Name >_ názvem serveru a názvem databáze.

To je vše, co potřebujete pro připojení k SQL Database. Při ladění v sadě Visual Studio váš kód používá uživatele Azure AD, kterého jste nakonfigurovali v [nastavení sady Visual Studio](#set-up-visual-studio). Server SQL Database později nastavíte, aby se povolilo připojení ze spravované identity vaší aplikace App Service.

Zadejte `Ctrl+F5` pro opětovné spuštění aplikace. Stejná aplikace CRUD v prohlížeči se teď připojuje k Azure SQL Database přímo pomocí ověřování Azure AD. Tato instalace umožňuje spustit migrace databáze ze sady Visual Studio.

### <a name="modify-aspnet-core"></a>Upravit ASP.NET Core

V aplikaci Visual Studio otevřete konzolu Správce balíčků a přidejte balíček NuGet [Microsoft. Azure. Services. AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication):

```powershell
Install-Package Microsoft.Azure.Services.AppAuthentication -Version 1.3.0
```

V `MyDbConnection` [kurzu ASP.NET Core a SQL Database](app-service-web-tutorial-dotnetcore-sqldb.md)se připojovací řetězec vůbec nepoužívá, protože místní vývojové prostředí používá soubor databáze sqlite a produkční prostředí Azure používá připojovací řetězec z App Service. Při ověřování pomocí služby Active Directory chcete, aby obě prostředí používala stejný připojovací řetězec. V souboru *appSettings. JSON*nahraďte hodnotu `MyDbConnection` připojovacího řetězce:

```json
"Server=tcp:<server-name>.database.windows.net,1433;Database=<database-name>;"
```

V *Startup.cs*odeberte oddíl kódu, který jste přidali dříve:

```csharp
// Use SQL Database if in Azure, otherwise, use SQLite
if (Environment.GetEnvironmentVariable("ASPNETCORE_ENVIRONMENT") == "Production")
    services.AddDbContext<MyDatabaseContext>(options =>
            options.UseSqlServer(Configuration.GetConnectionString("MyDbConnection")));
else
    services.AddDbContext<MyDatabaseContext>(options =>
            options.UseSqlite("Data Source=localdatabase.db"));

// Automatically perform database migration
services.BuildServiceProvider().GetService<MyDatabaseContext>().Database.Migrate();
```

A nahraďte ji následujícím kódem:

```csharp
services.AddDbContext<MyDatabaseContext>(options => {
    options.UseSqlServer(Configuration.GetConnectionString("MyDbConnection"));
});
```

Potom zadáte kontext databáze Entity Framework k přístupovému tokenu SQL Database. V *Data\MyDatabaseContext.cs*přidejte následující kód do složených závorek prázdného `MyDatabaseContext (DbContextOptions<MyDatabaseContext> options)` konstruktoru:

```csharp
var conn = (System.Data.SqlClient.SqlConnection)Database.GetDbConnection();
conn.AccessToken = (new Microsoft.Azure.Services.AppAuthentication.AzureServiceTokenProvider()).GetAccessTokenAsync("https://database.windows.net/").Result;
```

> [!TIP]
> Tento ukázkový kód je synchronní pro přehlednost. Další informace naleznete v tématu [asynchronní Průvodce konstruktory](https://github.com/davidfowl/AspNetCoreDiagnosticScenarios/blob/master/AsyncGuidance.md#constructors).

To je vše, co potřebujete pro připojení k SQL Database. Při ladění v sadě Visual Studio váš kód používá uživatele Azure AD, kterého jste nakonfigurovali v [nastavení sady Visual Studio](#set-up-visual-studio). Server SQL Database později nastavíte, aby se povolilo připojení ze spravované identity vaší aplikace App Service. `AzureServiceTokenProvider` Třída ukládá token do mezipaměti a načítá ho z Azure AD těsně před vypršením platnosti. K aktualizaci tokenu nepotřebujete žádný vlastní kód.

Zadejte `Ctrl+F5` pro opětovné spuštění aplikace. Stejná aplikace CRUD v prohlížeči se teď připojuje k Azure SQL Database přímo pomocí ověřování Azure AD. Tato instalace umožňuje spustit migrace databáze ze sady Visual Studio.

## <a name="use-managed-identity-connectivity"></a>Použití spravované identity připojení

Dále nakonfigurujete aplikaci App Service pro připojení k SQL Database se spravovanou identitou přiřazenou systémem.

### <a name="enable-managed-identity-on-app"></a>Povolit spravovanou identitu v aplikaci

K povolení spravované identity u aplikace Azure použijte příkaz [az webapp identity assign](/cli/azure/webapp/identity?view=azure-cli-latest#az-webapp-identity-assign) v prostředí Cloud Shell. V následujícím příkazu nahraďte  *\<> App-Name*.

```azurecli-interactive
az webapp identity assign --resource-group myResourceGroup --name <app-name>
```

Tady je příklad výstupu:

```json
{
  "additionalProperties": {},
  "principalId": "21dfa71c-9e6f-4d17-9e90-1d28801c9735",
  "tenantId": "72f988bf-86f1-41af-91ab-2d7cd011db47",
  "type": "SystemAssigned"
}
```

### <a name="add-managed-identity-to-an-azure-ad-group"></a>Přidání spravované identity do skupiny Azure AD

Pokud chcete této identitě udělit přístup k vašemu SQL Database, musíte ji přidat do [skupiny Azure AD](../active-directory/fundamentals/active-directory-manage-groups.md). V Cloud Shell ho přidejte do nové skupiny s názvem _myAzureSQLDBAccessGroup_, která se zobrazí v následujícím skriptu:

```azurecli-interactive
groupid=$(az ad group create --display-name myAzureSQLDBAccessGroup --mail-nickname myAzureSQLDBAccessGroup --query objectId --output tsv)
msiobjectid=$(az webapp identity show --resource-group myResourceGroup --name <app-name> --query principalId --output tsv)
az ad group member add --group $groupid --member-id $msiobjectid
az ad group member list -g $groupid
```

Pokud chcete zobrazit úplný výstup JSON pro jednotlivé příkazy, vynechte parametry `--query objectId --output tsv`.

### <a name="grant-permissions-to-azure-ad-group"></a>Udělení oprávnění skupině Azure AD

Ve službě Cloud Shell se přihlaste ke službě SQL Database pomocí příkazu SQLCMD. Položku název  _\<serveru >_ nahraďte názvem serveru SQL Database,  _\<DB-Name >_ názvem databáze, kterou vaše aplikace používá, a  _\<službou AAD-User-Name >_ a _\<AAD-Password >_ s přihlašovacími údaji uživatele Azure AD.

```azurecli-interactive
sqlcmd -S <server-name>.database.windows.net -d <db-name> -U <aad-user-name> -P "<aad-password>" -G -l 30
```

V příkazovém řádku SQL pro požadovanou databázi spusťte následující příkazy a přidejte skupinu Azure AD a udělte jim oprávnění, která vaše aplikace potřebuje. Například 

```sql
CREATE USER [myAzureSQLDBAccessGroup] FROM EXTERNAL PROVIDER;
ALTER ROLE db_datareader ADD MEMBER [myAzureSQLDBAccessGroup];
ALTER ROLE db_datawriter ADD MEMBER [myAzureSQLDBAccessGroup];
ALTER ROLE db_ddladmin ADD MEMBER [myAzureSQLDBAccessGroup];
GO
```

Zadáním `EXIT` se vraťte do příkazového řádku služby Cloud Shell.

### <a name="modify-connection-string"></a>Úprava připojovacího řetězce

Pamatujte, že stejné změny, které jste provedli v *souboru Web. config* nebo *appSettings. JSON* , fungují se spravovanou identitou, takže jediným krokem je odebrání stávajícího připojovacího řetězce v App Service, který Visual Studio vytvořilo nasazování vaší aplikace jako první. interval. Použijte následující příkaz, ale nahraďte  *\<název aplikace >* názvem vaší aplikace.

```azurecli-interactive
az webapp config connection-string delete --resource-group myResourceGroup --name <app-name> --setting-names MyDbConnection
```

## <a name="publish-your-changes"></a>Publikování provedených změn

Teď už stačí jen publikovat provedené změny do Azure.

**Pokud jste pocházeli z [kurzu: Sestavte aplikaci ASP.NET v Azure pomocí](app-service-web-tutorial-dotnet-sqldatabase.md)SQL Database**, publikujte změny v aplikaci Visual Studio. V **Průzkumníku řešení** klikněte pravým tlačítkem na projekt **DotNetAppSqlDb** a vyberte **Publikovat**.

![Publikování z Průzkumníka řešení](./media/app-service-web-tutorial-dotnet-sqldatabase/solution-explorer-publish.png)

Na stránce publikování klikněte na **Publikovat**. 

**Pokud jste pocházeli z [kurzu: Sestavte aplikaci ASP.NET Core a SQL Database v](app-service-web-tutorial-dotnetcore-sqldb.md)Azure App Service**, publikujte své změny pomocí Gitu s následujícími příkazy:

```bash
git commit -am "configure managed identity"
git push azure master
```

Pokud se na nové webové stránce zobrazí seznam úkolů, připojuje se vaše aplikace k databázi pomocí spravované identity.

![Aplikace Azure po Code First migraci](./media/app-service-web-tutorial-dotnet-sqldatabase/this-one-is-done.png)

Teď byste měli mít možnost upravovat seznam úkolu stejně jako předtím.

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

## <a name="next-steps"></a>Další kroky

Naučili jste se:

> [!div class="checklist"]
> * Povolení spravovaných identit
> * Udělit přístup ke spravované identitě službě SQL Database
> * Konfigurace Entity Framework pro použití ověřování Azure AD s SQL Database
> * Připojení k SQL Database ze sady Visual Studio pomocí ověřování Azure AD

V dalším kurzu se dozvíte, jak namapovat vlastní název DNS na webovou aplikaci.

> [!div class="nextstepaction"]
> [Mapování existujícího vlastního názvu DNS na Azure App Service](app-service-web-tutorial-custom-domain.md)
