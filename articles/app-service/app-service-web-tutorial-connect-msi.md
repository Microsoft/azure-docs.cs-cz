---
title: 'Kurz: přístup k datům pomocí spravované identity'
description: Naučte se, jak zajistit bezpečnější připojení k databázi pomocí spravované identity a jak ji použít pro jiné služby Azure.
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 04/27/2020
ms.custom: devx-track-csharp, mvc, cli-validate, devx-track-azurecli
ms.openlocfilehash: f043f7ed63353dcb9cf9fd26690da97b902f32a6
ms.sourcegitcommit: 48e5379c373f8bd98bc6de439482248cd07ae883
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/12/2021
ms.locfileid: "98108615"
---
# <a name="tutorial-secure-azure-sql-database-connection-from-app-service-using-a-managed-identity"></a>Kurz: Zabezpečení připojení ke službě Azure SQL Database ze služby App Service s využitím spravované identity

[App Service ](overview.md) je vysoce škálovatelná služba s automatickými opravami pro hostování webů v Azure. Poskytuje také [spravovanou identitu](overview-managed-identity.md) pro vaši aplikaci, což je řešení na klíč pro zabezpečení přístupu ke službě [Azure SQL Database](/azure/sql-database/) a dalším službám Azure. Spravované identity ve službě App Service zvyšují zabezpečení vaší aplikace tím, že z aplikace odstraňují tajné kódy, jako jsou přihlašovací údaje v připojovacích řetězcích. V tomto kurzu přidáte spravovanou identitu do ukázkové webové aplikace, kterou jste vytvořili v jednom z následujících kurzů: 

- [Kurz: Vytvoření aplikace v ASP.NET v Azure pomocí Azure SQL Database](app-service-web-tutorial-dotnet-sqldatabase.md)
- [Kurz: Vytvoření aplikace ASP.NET Core a Azure SQL Database v Azure App Service](tutorial-dotnetcore-sqldb-app.md)

Až budete hotovi, vaše ukázková aplikace se bezpečně připojí ke službě SQL Database bez potřeby uživatelského jména a hesla.

> [!NOTE]
> Postup popsaný v tomto kurzu podporuje následující verze:
> 
> - .NET Framework 4.7.2 a vyšší
> - .NET Core 2,2 a vyšší
>

Co se naučíte:

> [!div class="checklist"]
> * Povolit spravované identity
> * Udělit přístup ke spravované identitě službě SQL Database
> * Konfigurace Entity Framework pro použití ověřování Azure AD s SQL Database
> * Připojení k SQL Database ze sady Visual Studio pomocí ověřování Azure AD

> [!NOTE]
>Ověřování Azure AD se _liší_ od [integrovaného ověřování Windows](/previous-versions/windows/it-pro/windows-server-2003/cc758557(v=ws.10)) v místní službě Active Directory (služba AD DS). Služba AD DS a Azure AD používají zcela jiné ověřovací protokoly. Další informace najdete v [dokumentaci Azure AD Domain Services](../active-directory-domain-services/index.yml).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Požadavky

Tento článek pokračuje tam, kde jste skončili v [kurzu: sestavení aplikace v ASP.NET v Azure pomocí SQL Database](app-service-web-tutorial-dotnet-sqldatabase.md) nebo [kurzu: sestavení ASP.NET Core a SQL Database aplikace v Azure App Service](tutorial-dotnetcore-sqldb-app.md). Pokud jste to ještě neudělali, Projděte si jeden ze dvou kurzů jako první. Alternativně můžete upravit postup pro vlastní aplikaci .NET pomocí SQL Database.

Pokud chcete aplikaci ladit pomocí SQL Database jako back-end, ujistěte se, že jste povolili připojení klienta z počítače. V takovém případě přidejte IP adresu klienta podle kroků v části [Správa pravidel brány firewall na úrovni serveru pomocí Azure Portal](../azure-sql/database/firewall-configure.md#use-the-azure-portal-to-manage-server-level-ip-firewall-rules).

Připravte prostředí pro rozhraní příkazového řádku Azure CLI.

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

## <a name="grant-database-access-to-azure-ad-user"></a>Udělení přístupu k databázi uživateli Azure AD

Nejdřív povolte ověřování Azure AD, které se SQL Database přiřazením uživatele Azure AD jako správce služby Active Directory serveru. Tento uživatel se liší od účet Microsoft, které jste použili k registraci předplatného Azure. Musí se jednat o uživatele, kterého jste vytvořili, importovali, synchronizoval nebo pozvali do služby Azure AD. Další informace o povolených uživatelích Azure AD najdete v tématu [funkce a omezení služby Azure AD v SQL Database](../azure-sql/database/authentication-aad-overview.md#azure-ad-features-and-limitations).

Pokud váš tenant služby Azure AD ještě nemá uživatele, vytvořte ho podle kroků v části [Přidání nebo odstranění uživatelů pomocí Azure Active Directory](../active-directory/fundamentals/add-users-azure-active-directory.md).

Vyhledejte ID objektu uživatele Azure AD pomocí [`az ad user list`](/cli/azure/ad/user#az-ad-user-list) a nahraďte *\<user-principal-name>* . Výsledek je uložen do proměnné.

```azurecli-interactive
azureaduser=$(az ad user list --filter "userPrincipalName eq '<user-principal-name>'" --query [].objectId --output tsv)
```
> [!TIP]
> Pokud chcete zobrazit seznam všech hlavních názvů uživatelů v Azure AD, spusťte `az ad user list --query [].userPrincipalName` .
>

Přidejte tohoto uživatele Azure AD jako správce služby Active Directory pomocí [`az sql server ad-admin create`](/cli/azure/sql/server/ad-admin#az-sql-server-ad-admin-create) příkazu ve Cloud Shell. V následujícím příkazu nahraďte *\<server-name>* názvem serveru (bez `.database.windows.net` přípony).

```azurecli-interactive
az sql server ad-admin create --resource-group myResourceGroup --server-name <server-name> --display-name ADMIN --object-id $azureaduser
```

Další informace o přidání správce služby Active Directory najdete v tématu [zřízení správce Azure Active Directory pro váš server](../azure-sql/database/authentication-aad-configure.md#provision-azure-ad-admin-sql-managed-instance) .

## <a name="set-up-visual-studio"></a>Nastavit Visual Studio

### <a name="windows-client"></a>Klient Windows
Visual Studio pro Windows je integrované s ověřováním Azure AD. Pokud chcete povolit vývoj a ladění v aplikaci Visual Studio, přidejte uživatele služby Azure AD v aplikaci Visual Studio tak, že v nabídce vyberete  >  **nastavení účet** soubor a kliknete na **Přidat účet**.

Pokud chcete nastavit uživatele Azure AD pro ověřování služby Azure, v   >  nabídce vyberte **Možnosti** nástrojů a pak vyberte výběr **účtu ověřování služby Azure**  >  . Vyberte uživatele Azure AD, kterého jste přidali, a klikněte na **OK**.

Nyní jste připraveni vyvíjet a ladit svou aplikaci pomocí SQL Database jako back-endu pomocí ověřování Azure AD.

### <a name="macos-client"></a>Klient pro macOS

Visual Studio pro Mac není integrovaný s ověřováním Azure AD. Knihovna [Microsoft. Azure. Services. AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) , kterou použijete později, ale může používat tokeny z Azure CLI. Aby bylo možné povolit vývoj a ladění v aplikaci Visual Studio, nejprve je třeba nainstalovat rozhraní příkazového [řádku Azure CLI](/cli/azure/install-azure-cli) do místního počítače.

Po instalaci rozhraní příkazového řádku Azure na místní počítač se přihlaste ke službě Azure CLI pomocí následujícího příkazu pomocí uživatele Azure AD:

```azurecli
az login --allow-no-subscriptions
```
Nyní jste připraveni vyvíjet a ladit svou aplikaci pomocí SQL Database jako back-endu pomocí ověřování Azure AD.

## <a name="modify-your-project"></a>Úprava projektu

Kroky, které provedete pro váš projekt, závisí na tom, zda se jedná o projekt ASP.NET nebo projekt ASP.NET Core.

- [Upravit ASP.NET](#modify-aspnet)
- [Upravit ASP.NET Core](#modify-aspnet-core)

### <a name="modify-aspnet"></a>Upravit ASP.NET

V aplikaci Visual Studio otevřete konzolu Správce balíčků a přidejte balíček NuGet [Microsoft. Azure. Services. AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication):

```powershell
Install-Package Microsoft.Azure.Services.AppAuthentication -Version 1.4.0
```

V *Web.config* pracujete z horní části souboru a proveďte následující změny:

- V `<configSections>` přidejte do něj následující deklaraci oddílu:

    ```xml
    <section name="SqlAuthenticationProviders" type="System.Data.SqlClient.SqlAuthenticationProviderConfigurationSection, System.Data, Version=4.0.0.0, Culture=neutral, PublicKeyToken=b77a5c561934e089" />
    ```

- pod uzavírací `</configSections>` značku přidejte následující kód XML pro `<SqlAuthenticationProviders>` .

    ```xml
    <SqlAuthenticationProviders>
      <providers>
        <add name="Active Directory Interactive" type="Microsoft.Azure.Services.AppAuthentication.SqlAppAuthenticationProvider, Microsoft.Azure.Services.AppAuthentication" />
      </providers>
    </SqlAuthenticationProviders>
    ```    

- Vyhledejte připojovací řetězec s názvem `MyDbConnection` a nahraďte jeho `connectionString` hodnotu hodnotou `"server=tcp:<server-name>.database.windows.net;database=<db-name>;UID=AnyString;Authentication=Active Directory Interactive"` . Nahraďte _\<server-name>_ a názvem _\<db-name>_ serveru a názvem databáze.

> [!NOTE]
> SqlAuthenticationProvider, který jste právě zaregistrovali, je založená na knihovně AppAuthentication, kterou jste nainstalovali dříve. Ve výchozím nastavení používá identitu přiřazenou systémem. K využití identity přiřazené uživatelem budete muset zadat další konfiguraci. Podívejte se prosím na [podporu připojovacích řetězců](../key-vault/general/service-to-service-authentication.md#connection-string-support) pro knihovnu AppAuthentication.

To je vše, co potřebujete pro připojení k SQL Database. Při ladění v sadě Visual Studio váš kód používá uživatele Azure AD, kterého jste nakonfigurovali v [nastavení sady Visual Studio](#set-up-visual-studio). SQL Database později nastavíte, aby se povolilo připojení ze spravované identity vaší aplikace App Service.

Zadejte `Ctrl+F5` pro opětovné spuštění aplikace. Stejná aplikace CRUD v prohlížeči se teď připojuje k Azure SQL Database přímo pomocí ověřování Azure AD. Tato instalace umožňuje spustit migrace databáze ze sady Visual Studio.

### <a name="modify-aspnet-core"></a>Upravit ASP.NET Core

V aplikaci Visual Studio otevřete konzolu Správce balíčků a přidejte balíček NuGet [Microsoft. Azure. Services. AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication):

```powershell
Install-Package Microsoft.Azure.Services.AppAuthentication -Version 1.4.0
```

V [kurzu ASP.NET Core a SQL Database](tutorial-dotnetcore-sqldb-app.md)se `MyDbConnection` připojovací řetězec vůbec nepoužívá, protože místní vývojové prostředí používá soubor databáze sqlite a produkční prostředí Azure používá připojovací řetězec z App Service. Při ověřování pomocí služby Active Directory chcete, aby obě prostředí používala stejný připojovací řetězec. V *appsettings.jsna* nahraďte hodnotu `MyDbConnection` připojovacího řetězce hodnotou:

```json
"Server=tcp:<server-name>.database.windows.net,1433;Database=<database-name>;"
```

Potom zadáte kontext databáze Entity Framework k přístupovému tokenu SQL Database. V *Data\MyDatabaseContext.cs* přidejte následující kód do složených závorek prázdného `MyDatabaseContext (DbContextOptions<MyDatabaseContext> options)` konstruktoru:

```csharp
var connection = (SqlConnection)Database.GetDbConnection();
connection.AccessToken = (new Microsoft.Azure.Services.AppAuthentication.AzureServiceTokenProvider()).GetAccessTokenAsync("https://database.windows.net/").Result;
```

> [!NOTE]
> Tento ukázkový kód je synchronní pro přehlednost a jednoduchost.

To je vše, co potřebujete pro připojení k SQL Database. Při ladění v sadě Visual Studio váš kód používá uživatele Azure AD, kterého jste nakonfigurovali v [nastavení sady Visual Studio](#set-up-visual-studio). SQL Database později nastavíte, aby se povolilo připojení ze spravované identity vaší aplikace App Service. `AzureServiceTokenProvider`Třída ukládá token do mezipaměti a načítá ho z Azure AD těsně před vypršením platnosti. K aktualizaci tokenu nepotřebujete žádný vlastní kód.

> [!TIP]
> Pokud má uživatel Azure AD, kterého jste nakonfigurovali, přístup k více klientům, zavolejte `GetAccessTokenAsync("https://database.windows.net/", tenantid)` s požadovaným ID tenanta, aby se získal správný přístupový token.

Zadejte `Ctrl+F5` pro opětovné spuštění aplikace. Stejná aplikace CRUD v prohlížeči se teď připojuje k Azure SQL Database přímo pomocí ověřování Azure AD. Tato instalace umožňuje spustit migrace databáze ze sady Visual Studio.

## <a name="use-managed-identity-connectivity"></a>Použití spravované identity připojení

Dále nakonfigurujete aplikaci App Service pro připojení k SQL Database se spravovanou identitou přiřazenou systémem.

> [!NOTE]
> I když pokyny v této části jsou určené pro identitu přiřazenou systémem, je možné jednoduše použít uživatelsky přiřazenou identitu. Postupujte takto. tuto změnu budete potřebovat `az webapp identity assign command` k přiřazení požadované uživatelem přiřazené identity. Pak při vytváření uživatele SQL nezapomeňte použít název prostředku identity přiřazený uživatelem, nikoli název lokality.

### <a name="enable-managed-identity-on-app"></a>Povolit spravovanou identitu v aplikaci

K povolení spravované identity u aplikace Azure použijte příkaz [az webapp identity assign](/cli/azure/webapp/identity#az-webapp-identity-assign) v prostředí Cloud Shell. V následujícím příkazu nahraďte *\<app-name>* .

```azurecli-interactive
az webapp identity assign --resource-group myResourceGroup --name <app-name>
```

Tady je příklad výstupu:

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
> Pokud chcete, můžete přidat identitu do [skupiny Azure AD](../active-directory/fundamentals/active-directory-manage-groups.md)a potom udělit SQL Database přístup ke skupině Azure AD namísto identity. Například následující příkazy přidají spravovanou identitu z předchozího kroku do nové skupiny s názvem _myAzureSQLDBAccessGroup_:
> 
> ```azurecli-interactive
> groupid=$(az ad group create --display-name myAzureSQLDBAccessGroup --mail-nickname myAzureSQLDBAccessGroup --query objectId --output tsv)
> msiobjectid=$(az webapp identity show --resource-group myResourceGroup --name <app-name> --query principalId --output tsv)
> az ad group member add --group $groupid --member-id $msiobjectid
> az ad group member list -g $groupid
> ```
>

Ve službě Cloud Shell se přihlaste ke službě SQL Database pomocí příkazu SQLCMD. Nahraďte _\<server-name>_ názvem vašeho serveru a _\<db-name>_ názvem databáze, který vaše aplikace používá, a _\<aad-user-name>_ _\<aad-password>_ s přihlašovacími údaji uživatele Azure AD.

```bash
sqlcmd -S <server-name>.database.windows.net -d <db-name> -U <aad-user-name> -P "<aad-password>" -G -l 30
```

V příkazovém řádku SQL pro požadovanou databázi spusťte následující příkazy, abyste udělili oprávnění, která vaše aplikace potřebuje. Příklad: 

```sql
CREATE USER [<identity-name>] FROM EXTERNAL PROVIDER;
ALTER ROLE db_datareader ADD MEMBER [<identity-name>];
ALTER ROLE db_datawriter ADD MEMBER [<identity-name>];
ALTER ROLE db_ddladmin ADD MEMBER [<identity-name>];
GO
```

*\<identity-name>* je název spravované identity ve službě Azure AD. Pokud je identita přiřazena systémem, název je vždy stejný jako název vaší aplikace App Service. Pokud chcete udělit oprávnění pro skupinu Azure AD, použijte místo toho zobrazované jméno skupiny (například *myAzureSQLDBAccessGroup*).

Zadáním `EXIT` se vraťte do příkazového řádku služby Cloud Shell.

> [!NOTE]
> Back-endové služby spravovaných identit také [uchovávají mezipaměť tokenů](overview-managed-identity.md#obtain-tokens-for-azure-resources) , která aktualizuje token pro cílový prostředek pouze v případě, že jeho platnost vyprší. Pokud uděláte omylem konfiguraci oprávnění SQL Database a pokusíte se změnit oprávnění *po* pokusu o získání tokenu s vaší aplikací, nezískáte ve skutečnosti nový token s aktualizovanými oprávněními, dokud nevyprší platnost tokenu v mezipaměti.

> [!NOTE]
> Pro SQL Server v Prem se nepodporuje AAD, a to zahrnuje MSIs. 

### <a name="modify-connection-string"></a>Úprava připojovacího řetězce

Mějte na paměti, že stejné změny, které jste provedli v *Web.config* nebo *appsettings.jsv* sadě Works se spravovanými identitami, tak stačí k odebrání stávajícího připojovacího řetězce v App Service, který Visual Studio vytvořila poprvé nasazení vaší aplikace. Použijte následující příkaz, ale nahraďte *\<app-name>* názvem vaší aplikace.

```azurecli-interactive
az webapp config connection-string delete --resource-group myResourceGroup --name <app-name> --setting-names MyDbConnection
```

## <a name="publish-your-changes"></a>Publikování provedených změn

Teď už stačí jen publikovat provedené změny do Azure.

**Pokud jste pocházeli z [kurzu: sestavení aplikace v ASP.NET v Azure pomocí SQL Database](app-service-web-tutorial-dotnet-sqldatabase.md)**, publikování změn v aplikaci Visual Studio. V **Průzkumníku řešení** klikněte pravým tlačítkem na projekt **DotNetAppSqlDb** a vyberte **Publikovat**.

![Publikování z Průzkumníka řešení](./media/app-service-web-tutorial-dotnet-sqldatabase/solution-explorer-publish.png)

Na stránce publikování klikněte na **Publikovat**. 

**Pokud jste pocházeli z [kurzu: vytvoření aplikace ASP.NET Core a SQL Database v Azure App Service](tutorial-dotnetcore-sqldb-app.md)**, publikování změn pomocí Gitu s následujícími příkazy:

```bash
git commit -am "configure managed identity"
git push azure main
```

Pokud se na nové webové stránce zobrazí seznam úkolů, připojuje se vaše aplikace k databázi pomocí spravované identity.

![Aplikace Azure po Code First migraci](./media/app-service-web-tutorial-dotnet-sqldatabase/this-one-is-done.png)

Teď byste měli mít možnost upravovat seznam úkolu stejně jako předtím.

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

## <a name="next-steps"></a>Další kroky

Naučili jste se:

> [!div class="checklist"]
> * Povolit spravované identity
> * Udělit přístup ke spravované identitě službě SQL Database
> * Konfigurace Entity Framework pro použití ověřování Azure AD s SQL Database
> * Připojení k SQL Database ze sady Visual Studio pomocí ověřování Azure AD

V dalším kurzu se dozvíte, jak namapovat vlastní název DNS na webovou aplikaci.

> [!div class="nextstepaction"]
> [Mapování existujícího vlastního názvu DNS na Azure App Service](app-service-web-tutorial-custom-domain.md)
