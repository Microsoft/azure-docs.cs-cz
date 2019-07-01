---
title: Zabezpečené připojení k databázi SQL s využitím spravované identity – Azure App Service | Dokumentace Microsoftu
description: Zjistěte, jak zvýšit zabezpečení připojení k databázi s využitím spravované identity a jak tento postup aplikovat u ostatních služeb Azure.
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
ms.date: 06/21/2019
ms.author: cephalin
ms.custom: mvc
ms.openlocfilehash: 31535642526c608ad0ae29e5c0e3c93368e184ad
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/01/2019
ms.locfileid: "67481017"
---
# <a name="tutorial-secure-azure-sql-database-connection-from-app-service-using-a-managed-identity"></a>Kurz: Zabezpečené připojení k databázi SQL Azure ze služby App Service pomocí spravované identity

[App Service ](overview.md) je vysoce škálovatelná služba s automatickými opravami pro hostování webů v Azure. Poskytuje také [spravovanou identitu](overview-managed-identity.md) pro vaši aplikaci, což je řešení na klíč pro zabezpečení přístupu ke službě [Azure SQL Database](/azure/sql-database/) a dalším službám Azure. Spravované identity ve službě App Service zvyšují zabezpečení vaší aplikace tím, že z aplikace odstraňují tajné kódy, jako jsou přihlašovací údaje v připojovacích řetězcích. V tomto kurzu přidáte spravovanou identitu do ukázkové webové aplikace ASP.NET vytvořeným v [kurzu: Vytvoření aplikace ASP.NET se službou SQL Database v Azure](app-service-web-tutorial-dotnet-sqldatabase.md). Až budete hotovi, vaše ukázková aplikace se bezpečně připojí ke službě SQL Database bez potřeby uživatelského jména a hesla.

> [!NOTE]
> Tento scénář se aktuálně podporuje rozhraní .NET Framework 4.7.2 a výše. [.NET Core 2.2](https://www.microsoft.com/net/download/dotnet-core/2.2) tento scénář podporuje, ale ještě není součástí výchozích imagí ve službě App Service. 
>

Naučíte se:

> [!div class="checklist"]
> * Povolení spravovaných identit
> * Udělit přístup ke spravované identitě službě SQL Database
> * Konfigurace rozhraní Entity Framework pro SQL Database pomocí ověřování Azure AD
> * Připojení k SQL Database ze sady Visual Studio pomocí ověřování Azure AD

> [!NOTE]
>Ověřování Azure AD je _různých_ z [ověření integrované Windows](/previous-versions/windows/it-pro/windows-server-2003/cc758557(v=ws.10)) v místní službě Active Directory (AD DS). Služba AD DS a Azure AD používat zcela různých ověřovacích protokolů. Další informace najdete v tématu [dokumentace ke službě Azure AD Domain Services](https://docs.microsoft.com/azure/active-directory-domain-services/).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Požadavky

Tento článek pokračuje tam, kde jste přestali v [kurzu: Vytvoření aplikace ASP.NET se službou SQL Database v Azure](app-service-web-tutorial-dotnet-sqldatabase.md). Pokud jste to ještě neudělali, absolvujte nejprve tento kurz. Alternativně si můžete tento postup přizpůsobit pro vaši vlastní aplikaci ASP.NET se službou SQL Database.

Chcete-li ladit aplikaci využívající službu SQL Database jako back-endu, ujistěte se, že jste [povolené připojení klienta z vašeho počítače](app-service-web-tutorial-dotnet-sqldatabase.md#allow-client-connection-from-your-computer).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="grant-azure-ad-user-access-to-database"></a>Grant Azure AD uživateli přístup k databázi

Nejprve povolte ověřování Azure AD do služby SQL Database pomocí přiřazení uživatele služby Azure AD jako správce Active Directory serveru služby SQL Database. Tento uživatel se liší od účtu Microsoft, který jste použili k registraci pro vaše předplatné Azure. Musí být uživatel, který jste vytvořili, importovat, synchronizované nebo pozvaný do služby Azure AD. Další informace o Azure AD uživatelům, naleznete v části [funkcí Azure AD a omezení v databázi SQL](../sql-database/sql-database-aad-authentication.md#azure-ad-features-and-limitations). 

Najít ID objektu uživatele pomocí služby Azure AD [ `az ad user list` ](/cli/azure/ad/user?view=azure-cli-latest#az-ad-user-list) a nahraďte  *\<uživatele principal-name >* . Výsledek je uložen do proměnné.

```azurecli-interactive
azureaduser=$(az ad user list --filter "userPrincipalName eq '<user-principal-name>'" --query [].objectId --output tsv)
```
> [!TIP]
> Chcete-li zobrazit seznam všech hlavních názvů uživatelů ve službě Azure AD, spusťte `az ad user list --query [].userPrincipalName`.
>

Přidat tohoto uživatele Azure AD jako správce služby Active Directory pomocí [ `az sql server ad-admin create` ](/cli/azure/sql/server/ad-admin?view=azure-cli-latest#az-sql-server-ad-admin-create) příkazu ve službě Cloud Shell. V následujícím příkazu nahraďte  *\<název serveru >* .

```azurecli-interactive
az sql server ad-admin create --resource-group myResourceGroup --server-name <server-name> --display-name ADMIN --object-id $azureaduser
```

Další informace o přidání správce Active Directory najdete v tématu [zřízení správce Azure Active Directory pro Azure SQL Database Server](../sql-database/sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server)

## <a name="set-up-visual-studio"></a>Instalace sady Visual Studio

Pokud chcete povolit, vývoj a ladění v sadě Visual Studio, přidejte uživatele Azure AD v sadě Visual Studio tak, že vyberete **souboru** > **nastavení účtu** nabídky a klikněte na **přidat účet**.

Pokud chcete nastavit uživatele Azure AD pro ověřování služby Azure, vyberte **nástroje** > **možnosti** v nabídce pak vyberte **ověřování služby Azure**  >  **Výběr účtu**. Vyberte uživatele Azure AD, který jste přidali a klikněte na tlačítko **OK**.

Teď jste připraven k vývoji a ladění aplikace pomocí databáze SQL jako back-endu, pomocí ověřování Azure AD.

## <a name="modify-aspnet-project"></a>Upravit projekt ASP.NET

V sadě Visual Studio, otevřete konzolu Správce balíčků a přidejte balíček NuGet [Microsoft.Azure.Services.appauthentication přistupovat](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication):

```powershell
Install-Package Microsoft.Azure.Services.AppAuthentication -Version 1.2.0
```

V *Web.config*, práci v horní části souboru a proveďte následující změny:

- V `<configSections>`, přidejte následující deklarace části v ní:

    ```xml
    <section name="SqlAuthenticationProviders" type="System.Data.SqlClient.SqlAuthenticationProviderConfigurationSection, System.Data, Version=4.0.0.0, Culture=neutral, PublicKeyToken=b77a5c561934e089" />
    ```

- pod uzavírací `</configSections>` značky, přidejte následující kód XML pro `<SqlAuthenticationProviders>`.

    ```xml
    <SqlAuthenticationProviders>
      <providers>
        <add name="Active Directory Interactive" type="Microsoft.Azure.Services.AppAuthentication.SqlAppAuthenticationProvider, Microsoft.Azure.Services.AppAuthentication" />
      </providers>
    </SqlAuthenticationProviders>
    ```    

- Najít připojovací řetězec s názvem `MyDbConnection` a nahraďte jeho `connectionString` hodnotu `"server=tcp:<server-name>.database.windows.net;database=<db-name>;UID=AnyString;Authentication=Active Directory Interactive"`. Nahraďte  _\<název serveru >_ a  _\<db-name >_ pomocí názvu serveru a název databáze.

Typ `Ctrl+F5` spusťte aplikaci znovu. Stejnou aplikaci CRUD ve vašem prohlížeči je teď připojení ke službě Azure SQL Database přímo, pomocí ověřování Azure AD. Toto nastavení umožňuje spusťte migrace databází. Později, když vaše změny nasadí do služby App Service, stejné nastavení práce s využitím identity spravované aplikace.

## <a name="use-managed-identity-connectivity"></a>Použití spravované identity připojení

Dále je nutné nakonfigurovat aplikaci služby App Service pro připojení k databázi SQL s systém přiřadil spravovanou identitu.

### <a name="enable-managed-identity-on-app"></a>Povolení identity spravované v aplikaci

K povolení spravované identity u aplikace Azure použijte příkaz [az webapp identity assign](/cli/azure/webapp/identity?view=azure-cli-latest#az-webapp-identity-assign) v prostředí Cloud Shell. V následujícím příkazu nahraďte  *\<název aplikace >* .

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

### <a name="add-managed-identity-to-an-azure-ad-group"></a>Přidat spravovanou identitu do skupiny Azure AD

Pokud chcete udělit přístup identit do služby SQL Database, musíte ho přidat do [skupiny Azure AD](../active-directory/fundamentals/active-directory-manage-groups.md). Ve službě Cloud Shell, přidejte pro novou skupinu s názvem _myAzureSQLDBAccessGroup_, jak je znázorněno v následujícím skriptu:

```azurecli-interactive
groupid=$(az ad group create --display-name myAzureSQLDBAccessGroup --mail-nickname myAzureSQLDBAccessGroup --query objectId --output tsv)
msiobjectid=$(az webapp identity show --resource-group myResourceGroup --name <app-name> --query principalId --output tsv)
az ad group member add --group $groupid --member-id $msiobjectid
az ad group member list -g $groupid
```

Pokud chcete zobrazit úplný výstup JSON pro jednotlivé příkazy, vynechte parametry `--query objectId --output tsv`.

### <a name="grant-permissions-to-azure-ad-group"></a>Udělit oprávnění ke skupině Azure AD

Ve službě Cloud Shell se přihlaste ke službě SQL Database pomocí příkazu SQLCMD. Nahraďte  _\<název serveru >_ názvem vaší databáze SQL serveru,  _\<db-name >_ s databází název vaší aplikace používá, a  _\< aad – uživatelské jméno >_ a  _\<heslo aad >_ s přihlašovacími údaji vašeho uživatele Azure AD.

```azurecli-interactive
sqlcmd -S <server-name>.database.windows.net -d <db-name> -U <aad-user-name> -P "<aad-password>" -G -l 30
```

V příkazovém řádku SQL pro databázi, kterou chcete spustit následující příkazy, které Azure AD přidejte skupiny a udělení oprávnění aplikace potřebuje. Například: 

```sql
CREATE USER [myAzureSQLDBAccessGroup] FROM EXTERNAL PROVIDER;
ALTER ROLE db_datareader ADD MEMBER [myAzureSQLDBAccessGroup];
ALTER ROLE db_datawriter ADD MEMBER [myAzureSQLDBAccessGroup];
ALTER ROLE db_ddladmin ADD MEMBER [myAzureSQLDBAccessGroup];
GO
```

Zadáním `EXIT` se vraťte do příkazového řádku služby Cloud Shell.

### <a name="modify-connection-string"></a>Úprava připojovacího řetězce

Mějte na paměti, že stejné změny, které jste provedli v `Web.config` funguje s identitou, tak jediné, co chcete udělat, je odebrat existující připojovací řetězec ve vaší aplikaci, které Visual Studio vytvoří při prvním nasazení aplikace. Pomocí následujícího příkazu, ale nahraďte  *\<název aplikace >* s názvem vaší aplikace.

```azurecli-interactive
az webapp config connection-string delete --resource-group myResourceGroup --name <app-name> --setting-names MyDbConnection
```

## <a name="publish-your-changes"></a>Publikování provedených změn

Teď už stačí jen publikovat provedené změny do Azure.

V **Průzkumníku řešení** klikněte pravým tlačítkem na projekt **DotNetAppSqlDb** a vyberte **Publikovat**.

![Publikování z Průzkumníka řešení](./media/app-service-web-tutorial-dotnet-sqldatabase/solution-explorer-publish.png)

Na stránce publikování klikněte na **Publikovat**. Pokud se na nové webové stránce zobrazí seznam úkolů, připojuje se vaše aplikace k databázi pomocí spravované identity.

![Aplikace Azure po migraci Code First](./media/app-service-web-tutorial-dotnet-sqldatabase/this-one-is-done.png)

Teď byste měli mít možnost upravovat seznam úkolu stejně jako předtím.

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

## <a name="next-steps"></a>Další postup

Naučili jste se:

> [!div class="checklist"]
> * Povolení spravovaných identit
> * Udělit přístup ke spravované identitě službě SQL Database
> * Konfigurace rozhraní Entity Framework pro SQL Database pomocí ověřování Azure AD
> * Připojení k SQL Database ze sady Visual Studio pomocí ověřování Azure AD

V dalším kurzu se dozvíte, jak namapovat vlastní název DNS na webovou aplikaci.

> [!div class="nextstepaction"]
> [Mapování existujícího vlastního názvu DNS do služby Azure App Service](app-service-web-tutorial-custom-domain.md)
