---
title: 'Kurz: Přístup k datům se spravovanou identitou'
description: Zjistěte, jak zabezpečit připojení k databázi pomocí spravované identity a také jak ji použít na jiné služby Azure.
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 11/18/2019
ms.custom: mvc, cli-validate
ms.openlocfilehash: b66874cf95ed29d9be0a2d1ea397704131c7b21d
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2020
ms.locfileid: "82085428"
---
# <a name="tutorial-secure-azure-sql-database-connection-from-app-service-using-a-managed-identity"></a>Kurz: Zabezpečení připojení ke službě Azure SQL Database ze služby App Service s využitím spravované identity

[App Service ](overview.md) je vysoce škálovatelná služba s automatickými opravami pro hostování webů v Azure. Poskytuje také [spravovanou identitu](overview-managed-identity.md) pro vaši aplikaci, což je řešení na klíč pro zabezpečení přístupu ke službě [Azure SQL Database](/azure/sql-database/) a dalším službám Azure. Spravované identity ve službě App Service zvyšují zabezpečení vaší aplikace tím, že z aplikace odstraňují tajné kódy, jako jsou přihlašovací údaje v připojovacích řetězcích. V tomto kurzu přidáte spravovanou identitu do ukázkové webové aplikace, kterou jste vytvořili v jednom z následujících kurzů: 

- [Kurz: Vytvoření aplikace ASP.NET se službou SQL Database v Azure](app-service-web-tutorial-dotnet-sqldatabase.md)
- [Kurz: Vytvoření aplikace ASP.NET core a sql database ve službě Azure App Service](app-service-web-tutorial-dotnetcore-sqldb.md)

Až budete hotovi, vaše ukázková aplikace se bezpečně připojí ke službě SQL Database bez potřeby uživatelského jména a hesla.

> [!NOTE]
> Kroky uvedené v tomto kurzu podporují následující verze:
> 
> -  .NET Framework 4.7.2
> - .NET Core 2.2
>

Co se naučíte:

> [!div class="checklist"]
> * Povolit spravované identity
> * Udělit přístup ke spravované identitě službě SQL Database
> * Konfigurace entity Framework pro použití ověřování Azure AD s databází SQL
> * Připojení k databázi SQL z Visual Studia pomocí ověřování Azure AD

> [!NOTE]
>Ověřování Azure AD _se liší_ od [integrovaného ověřování systému Windows](/previous-versions/windows/it-pro/windows-server-2003/cc758557(v=ws.10)) v místním adresáři Active Directory (AD DS). Služby AD DS a Azure AD používají zcela odlišné ověřovací protokoly. Další informace naleznete v [dokumentaci k službě Azure AD Domain Services](https://docs.microsoft.com/azure/active-directory-domain-services/).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Požadované součásti

Tento článek pokračuje tam, kde jste skončili v [kurzu: Vytvoření aplikace ASP.NET v Azure s databází SQL](app-service-web-tutorial-dotnet-sqldatabase.md) nebo [kurz: Vytvoření aplikace ASP.NET Core a SQL Database ve službě Azure App Service](app-service-web-tutorial-dotnetcore-sqldb.md). Pokud jste tak dosud neučinili, postupujte nejprve podle jednoho ze dvou kurzů. Případně můžete přizpůsobit kroky pro vlastní aplikaci .NET s databází SQL.

Chcete-li ladit aplikaci pomocí sql database jako back-end, ujistěte se, že jste povolili připojení klienta z počítače. Pokud ne, přidejte IP adresu klienta podle kroků při [správě pravidel brány IP na úrovni serveru pomocí portálu Azure](../sql-database/sql-database-firewall-configure.md#use-the-azure-portal-to-manage-server-level-ip-firewall-rules).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="grant-database-access-to-azure-ad-user"></a>Udělení přístupu k databázi uživateli Azure AD

Nejprve povolte ověřování Azure AD do databáze SQL přiřazením uživatele Azure AD jako správce služby Active Directory serveru SQL Database. Tento uživatel se liší od účtu Microsoft, který jste použili k registraci předplatného Azure. Musí to být uživatel, který jste vytvořili, importovali, synchronizovali nebo pozvali do Služby Azure AD. Další informace o povolených uživatelích Azure AD najdete [v tématu funkce a omezení Azure AD v databázi SQL](../sql-database/sql-database-aad-authentication.md#azure-ad-features-and-limitations).

Pokud váš klient Azure AD ještě nemá uživatele, vytvořte ho podle pokynů na [přidejte nebo odstraňte uživatele pomocí Služby Azure Active Directory](../active-directory/fundamentals/add-users-azure-active-directory.md).

Najděte ID objektu uživatele Azure [`az ad user list`](/cli/azure/ad/user?view=azure-cli-latest#az-ad-user-list) AD pomocí a nahraďte * \<>hlavního názvu uživatele *. Výsledek se uloží do proměnné.

```azurecli-interactive
azureaduser=$(az ad user list --filter "userPrincipalName eq '<user-principal-name>'" --query [].objectId --output tsv)
```
> [!TIP]
> Chcete-li zobrazit seznam všech hlavních uživatelských `az ad user list --query [].userPrincipalName`jmen ve službě Azure AD, spusťte .
>

Přidejte tohoto uživatele Azure AD [`az sql server ad-admin create`](/cli/azure/sql/server/ad-admin?view=azure-cli-latest#az-sql-server-ad-admin-create) jako správce služby Active Directory pomocí příkazu v prostředí Cloud. V následujícím příkazu * \<* nahraďte název serveru>názvem `.database.windows.net` serveru SQL Database (bez přípony).

```azurecli-interactive
az sql server ad-admin create --resource-group myResourceGroup --server-name <server-name> --display-name ADMIN --object-id $azureaduser
```

Další informace o přidání správce služby Active Directory najdete v [tématu Zřízení správce služby Azure Active Directory pro váš databázový server Azure SQL.](../sql-database/sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server)

## <a name="set-up-visual-studio"></a>Nastavit Visual Studio

### <a name="windows"></a>Windows
Visual Studio pro Windows je integrované s ověřováním Azure AD. Pokud chcete povolit vývoj a ladění ve Visual Studiu, přidejte uživatele Azure AD do Sady Visual Studio tak, že v nabídce vyberete**Nastavení účtu** **souboru** > a kliknete na **Přidat účet**.

Pokud chcete nastavit ověřování uživatele Azure AD pro službu Azure, vyberte**možnosti** **nástrojů** > z nabídky a pak vyberte Výběr **ověřovacího** > **účtu**služby Azure . Vyberte uživatele Azure AD, který jste přidali, a klepněte na tlačítko **OK**.

Teď jste připraveni k vývoji a ladění aplikace s databází SQL jako back-end, pomocí ověřování Azure AD.

### <a name="macos"></a>MacOS

Visual Studio pro Mac není integrované s ověřováním Azure AD. Knihovna [Microsoft.Azure.Services.AppAuthentication,](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) kterou později použijete, však může používat tokeny z rozhraní příkazového příkazu Azure. Chcete-li povolit vývoj a ladění v sadě Visual Studio, musíte [nejprve nainstalovat Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) v místním počítači.

Po nainstalovaný Azure CLI na vašem místním počítači, přihlaste se k Azure CLI pomocí následujícího příkazu pomocí uživatele Azure AD:

```bash
az login --allow-no-subscriptions
```
Teď jste připraveni k vývoji a ladění aplikace s databází SQL jako back-end, pomocí ověřování Azure AD.

## <a name="modify-your-project"></a>Úprava projektu

Kroky, které pro projekt provedete, závisí na tom, zda se jedná o projekt ASP.NET nebo ASP.NET základní projekt.

- [Upravit ASP.NET](#modify-aspnet)
- [Upravit ASP.NET jádro](#modify-aspnet-core)

### <a name="modify-aspnet"></a>Upravit ASP.NET

V sadě Visual Studio otevřete konzolu Správce balíčků a přidejte balíček NuGet [Microsoft.Azure.Services.AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication):

```powershell
Install-Package Microsoft.Azure.Services.AppAuthentication -Version 1.3.1
```

V *souboru Web.config*pracujete z horní části souboru a proveďte následující změny:

- V `<configSections>`písmenu a) přidejte do ní následující prohlášení oddílu:

    ```xml
    <section name="SqlAuthenticationProviders" type="System.Data.SqlClient.SqlAuthenticationProviderConfigurationSection, System.Data, Version=4.0.0.0, Culture=neutral, PublicKeyToken=b77a5c561934e089" />
    ```

- Pod uzavírací `</configSections>` značku přidejte následující `<SqlAuthenticationProviders>`kód XML pro .

    ```xml
    <SqlAuthenticationProviders>
      <providers>
        <add name="Active Directory Interactive" type="Microsoft.Azure.Services.AppAuthentication.SqlAppAuthenticationProvider, Microsoft.Azure.Services.AppAuthentication" />
      </providers>
    </SqlAuthenticationProviders>
    ```    

- Najděte volaným připojovacím `MyDbConnection` řetězcem a nahraďte jeho `connectionString` hodnotu písmenem `"server=tcp:<server-name>.database.windows.net;database=<db-name>;UID=AnyString;Authentication=Active Directory Interactive"`. Nahraďte _ \<>_ názvu serveru a _ \<>názvu db_ názvem názvem serveru a názvem databáze.

> [!NOTE]
> Zprostředkovatel SqlAuthenticationProvider, který jste právě zaregistrovali, je založen na knihovně AppAuthentication, kterou jste nainstalovali dříve. Ve výchozím nastavení používá systémově přiřazenou identitu. Chcete-li využít identitu přiřazenou uživateli, budete muset zadat další konfiguraci. Přečtěte si [podporu připojovacího řetězce](../key-vault/general/service-to-service-authentication.md#connection-string-support) pro knihovnu AppAuthentication.

To je každá věc, kterou potřebujete pro připojení k databázi SQL. Při ladění ve Visual Studiu používá váš kód uživatele Azure AD, který jste nakonfigurovali v [nastavení sady Visual Studio](#set-up-visual-studio). Server SQL Database nastavíte později, aby bylo možné připojení ze spravované identity aplikace App Service.

Zadejte, `Ctrl+F5` chcete-li aplikaci spustit znovu. Stejná aplikace CRUD ve vašem prohlížeči se teď připojuje přímo k Azure SQL Database pomocí ověřování Azure AD. Toto nastavení umožňuje spouštět migrace databází z sady Visual Studio.

### <a name="modify-aspnet-core"></a>Upravit ASP.NET jádro

V sadě Visual Studio otevřete konzolu Správce balíčků a přidejte balíček NuGet [Microsoft.Azure.Services.AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication):

```powershell
Install-Package Microsoft.Azure.Services.AppAuthentication -Version 1.3.1
```

V [kurzu ASP.NET core a](app-service-web-tutorial-dotnetcore-sqldb.md) `MyDbConnection` sql database se připojovací řetězec vůbec nepoužívá, protože místní vývojové prostředí používá databázový soubor Sqlite a produkční prostředí Azure používá připojovací řetězec z app service. Při ověřování služby Active Directory chcete, aby obě prostředí používala stejný připojovací řetězec. V *souboru appsettings.json*nahraďte hodnotu připojovacího `MyDbConnection` řetězce:

```json
"Server=tcp:<server-name>.database.windows.net,1433;Database=<database-name>;"
```

V *Startup.cs*odeberte oddíl kódu, který jste přidali před:

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

A nahraďte jej následujícím kódem:

```csharp
services.AddDbContext<MyDatabaseContext>(options => {
    options.UseSqlServer(Configuration.GetConnectionString("MyDbConnection"));
});
```

Dále zadáte kontext databáze Entity Framework s přístupovým tokenem pro databázi SQL. V *poli Data\MyDatabaseContext.cs*přidejte do složených závorek prázdného `MyDatabaseContext (DbContextOptions<MyDatabaseContext> options)` konstruktoru následující kód:

```csharp
var conn = (System.Data.SqlClient.SqlConnection)Database.GetDbConnection();
conn.AccessToken = (new Microsoft.Azure.Services.AppAuthentication.AzureServiceTokenProvider()).GetAccessTokenAsync("https://database.windows.net/").Result;
```

> [!NOTE]
> Tento ukázkový kód je synchronní pro přehlednost a jednoduchost.

To je každá věc, kterou potřebujete pro připojení k databázi SQL. Při ladění ve Visual Studiu používá váš kód uživatele Azure AD, který jste nakonfigurovali v [nastavení sady Visual Studio](#set-up-visual-studio). Server SQL Database nastavíte později, aby bylo možné připojení ze spravované identity aplikace App Service. Třída `AzureServiceTokenProvider` ukládá token do mezipaměti v paměti a načte ho z Azure AD těsně před vypršením platnosti. K aktualizaci tokenu nepotřebujete žádný vlastní kód.

> [!TIP]
> Pokud uživatel Azure AD, který jste nakonfigurovali, má přístup k více klientům, zavolejte `GetAccessTokenAsync("https://database.windows.net/", tenantid)` s požadovaným ID klienta a načtěte správný přístupový token.

Zadejte, `Ctrl+F5` chcete-li aplikaci spustit znovu. Stejná aplikace CRUD ve vašem prohlížeči se teď připojuje přímo k Azure SQL Database pomocí ověřování Azure AD. Toto nastavení umožňuje spouštět migrace databází z sady Visual Studio.

## <a name="use-managed-identity-connectivity"></a>Použití připojení spravované identity

Dále nakonfigurujete aplikaci App Service pro připojení k databázi SQL pomocí spravované identity přiřazené k systému.

> [!NOTE]
> Zatímco pokyny v této části jsou pro systémově přiřazenou identitu, identitu přiřazenou uživateli lze stejně snadno použít. Postupujte takto. budete potřebovat změnu `az webapp identity assign command` přiřadit požadovanou identitu přiřazenou uživateli. Potom při vytváření uživatele SQL, ujistěte se, že použít název prostředku identity přiřazené uživateli, nikoli název webu.

### <a name="enable-managed-identity-on-app"></a>Povolení spravované identity v aplikaci

K povolení spravované identity u aplikace Azure použijte příkaz [az webapp identity assign](/cli/azure/webapp/identity?view=azure-cli-latest#az-webapp-identity-assign) v prostředí Cloud Shell. V následujícím příkazu * \< *nahraďte název aplikace>.

```azurecli-interactive
az webapp identity assign --resource-group myResourceGroup --name <app-name>
```

Zde je příklad výstupu:

<pre>
{
  "additionalProperties": {},
  "principalId": "21dfa71c-9e6f-4d17-9e90-1d28801c9735",
  "tenantId": "72f988bf-86f1-41af-91ab-2d7cd011db47",
  "type": "SystemAssigned"
}
</pre>

### <a name="grant-permissions-to-managed-identity"></a>Udělení oprávnění spravované identitě

> [!NOTE]
> Pokud chcete, můžete přidat identitu do [skupiny Azure AD](../active-directory/fundamentals/active-directory-manage-groups.md)a pak udělit sql database přístup ke skupině Azure AD namísto identity. Například následující příkazy přidávají spravovanou identitu z předchozího kroku do nové skupiny s názvem _myAzureSQLDBAccessGroup_:
> 
> ```azurecli-interactive
> groupid=$(az ad group create --display-name myAzureSQLDBAccessGroup --mail-nickname myAzureSQLDBAccessGroup --query objectId --output tsv)
> msiobjectid=$(az webapp identity show --resource-group myResourceGroup --name <app-name> --query principalId --output tsv)
> az ad group member add --group $groupid --member-id $msiobjectid
> az ad group member list -g $groupid
> ```
>

Ve službě Cloud Shell se přihlaste ke službě SQL Database pomocí příkazu SQLCMD. Nahraďte _ \<>s názvem_ serveru SQL Database, _ \<název db>_ názvem databáze, který vaše aplikace používá, a _ \<>a_ _ \<aad-user-name>_ s přihlašovacími údaji uživatele Azure AD.

```azurecli-interactive
sqlcmd -S <server-name>.database.windows.net -d <db-name> -U <aad-user-name> -P "<aad-password>" -G -l 30
```

Ve výzvě SQL pro požadovanou databázi spusťte následující příkazy a přidejte skupinu Azure AD a udělte oprávnění, která vaše aplikace potřebuje. Například: 

```sql
CREATE USER [<identity-name>] FROM EXTERNAL PROVIDER;
ALTER ROLE db_datareader ADD MEMBER [<identity-name>];
ALTER ROLE db_datawriter ADD MEMBER [<identity-name>];
ALTER ROLE db_ddladmin ADD MEMBER [<identity-name>];
GO
```

název identity>je název spravované identity ve službě Azure AD. * \<* Pokud je identita přiřazena systému, název je vždy stejný jako název aplikace App Service. Chcete-li udělit oprávnění pro skupinu Azure AD, použijte místo toho zobrazovaný název skupiny (například *myAzureSQLDBAccessGroup).*

Zadáním `EXIT` se vraťte do příkazového řádku služby Cloud Shell.

> [!NOTE]
> Back-endové služby spravovaných identit také [udržuje mezipaměť tokenů,](overview-managed-identity.md#obtain-tokens-for-azure-resources) která aktualizuje token pro cílový prostředek pouze v případě, že vyprší jeho platnost. Pokud uděláte chybu při konfiguraci oprávnění databáze SQL a pokusíte se změnit oprávnění *po* pokusu o získání tokenu s vaší aplikací, nezískáte ve skutečnosti nový token s aktualizovanými oprávněními, dokud nevyprší platnost tokenu uloženého v mezipaměti.

### <a name="modify-connection-string"></a>Úprava připojovacího řetězce

Nezapomeňte, že stejné změny, které jste provedli v *souboru Web.config* nebo *appsettings.json,* fungují se spravovanou identitou, takže jediné, co můžete udělat, je odebrat existující připojovací řetězec ve službě App Service, který Visual Studio vytvořilo při prvním nasazení aplikace. Použijte následující příkaz, * \<* ale nahraďte název aplikace>názvem aplikace.

```azurecli-interactive
az webapp config connection-string delete --resource-group myResourceGroup --name <app-name> --setting-names MyDbConnection
```

## <a name="publish-your-changes"></a>Publikování provedených změn

Teď už stačí jen publikovat provedené změny do Azure.

**Pokud jste přišli z [kurzu: Vytvoření ASP.NET aplikace v Azure s DATABÁZÍ SQL](app-service-web-tutorial-dotnet-sqldatabase.md)**, publikujte změny ve Visual Studiu. V **Průzkumníku řešení** klikněte pravým tlačítkem na projekt **DotNetAppSqlDb** a vyberte **Publikovat**.

![Publikování z Průzkumníka řešení](./media/app-service-web-tutorial-dotnet-sqldatabase/solution-explorer-publish.png)

Na stránce publikování klikněte na **Publikovat**. 

**Pokud jste přišli z [kurzu: Vytvoření aplikace ASP.NET Core a SQL Database ve službě Azure App Service](app-service-web-tutorial-dotnetcore-sqldb.md)**, publikujte změny pomocí Gitu pomocí následujících příkazů:

```bash
git commit -am "configure managed identity"
git push azure master
```

Pokud se na nové webové stránce zobrazí seznam úkolů, připojuje se vaše aplikace k databázi pomocí spravované identity.

![Aplikace Azure po první migraci kódu](./media/app-service-web-tutorial-dotnet-sqldatabase/this-one-is-done.png)

Teď byste měli mít možnost upravovat seznam úkolu stejně jako předtím.

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

## <a name="next-steps"></a>Další kroky

Naučili jste se:

> [!div class="checklist"]
> * Povolit spravované identity
> * Udělit přístup ke spravované identitě službě SQL Database
> * Konfigurace entity Framework pro použití ověřování Azure AD s databází SQL
> * Připojení k databázi SQL z Visual Studia pomocí ověřování Azure AD

V dalším kurzu se dozvíte, jak namapovat vlastní název DNS na webovou aplikaci.

> [!div class="nextstepaction"]
> [Mapování existujícího vlastního názvu DNS na službu Azure App Service](app-service-web-tutorial-custom-domain.md)
