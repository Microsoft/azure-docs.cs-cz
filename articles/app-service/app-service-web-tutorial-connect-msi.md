---
title: Zabezpečení připojení ke službě Azure SQL Database ze služby App Service s využitím spravované identity | Microsoft Docs
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
ms.date: 04/17/2018
ms.author: cephalin
ms.custom: mvc
ms.openlocfilehash: 3125db03dc13f70524fd094736f50b563ef712a4
ms.sourcegitcommit: 5a9be113868c29ec9e81fd3549c54a71db3cec31
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/11/2018
ms.locfileid: "44379923"
---
# <a name="tutorial-secure-azure-sql-database-connection-from-app-service-using-a-managed-identity"></a>Kurz: Zabezpečení připojení ke službě Azure SQL Database ze služby App Service s využitím spravované identity

[App Service ](app-service-web-overview.md) je vysoce škálovatelná služba s automatickými opravami pro hostování webů v Azure. Poskytuje také [spravovanou identitu](app-service-managed-service-identity.md) pro vaši aplikaci, což je řešení na klíč pro zabezpečení přístupu ke službě [Azure SQL Database](/azure/sql-database/) a dalším službám Azure. Spravované identity ve službě App Service zvyšují zabezpečení vaší aplikace tím, že z aplikace odstraňují tajné kódy, jako jsou přihlašovací údaje v připojovacích řetězcích. V tomto kurzu přidáte spravovanou identitu do ukázkové webové aplikace ASP.NET, kterou jste vytvořili v článku [Kurz: Vytvoření aplikace ASP.NET se službou SQL Database v Azure](app-service-web-tutorial-dotnet-sqldatabase.md). Až budete hotovi, vaše ukázková aplikace se bezpečně připojí ke službě SQL Database bez potřeby uživatelského jména a hesla.

Naučíte se:

> [!div class="checklist"]
> * Povolit spravované identity
> * Udělit přístup ke spravované identitě službě SQL Database
> * Nakonfigurovat kód aplikace tak, aby k ověřování ve službě SQL Database používala ověřování Azure Active Directory
> * Udělit minimální oprávnění ke spravované identitě ve službě SQL Database

> [!NOTE]
> Ověřování pomocí Azure Active Directory se _liší_ od [integrovaného ověřování systému Windows](/previous-versions/windows/it-pro/windows-server-2003/cc758557(v=ws.10)) v místní službě Active Directory (služba AD DS). Služba AD DS a Azure Active Directory využívají k ověřování zcela odlišné protokoly. Další informace najdete v tématu popisujícím [rozdíly mezi službou AD DS Windows Serveru a Azure AD](../active-directory/fundamentals/understand-azure-identity-solutions.md#the-difference-between-windows-server-ad-ds-and-azure-ad).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Požadavky

Tento článek pokračuje tam, kde jste přestali v kurzu [Vytvoření aplikace ASP.NET se službou SQL Database v Azure](app-service-web-tutorial-dotnet-sqldatabase.md). Pokud jste to ještě neudělali, absolvujte nejprve tento kurz. Alternativně si můžete tento postup přizpůsobit pro vaši vlastní aplikaci ASP.NET se službou SQL Database.

<!-- ![app running in App Service](./media/app-service-web-tutorial-dotnetcore-sqldb/azure-app-in-browser.png) -->

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="enable-managed-identities"></a>Povolení spravovaných identit

K povolení spravované identity u aplikace Azure použijte příkaz [az webapp identity assign](/cli/azure/webapp/identity?view=azure-cli-latest#az-webapp-identity-assign) v prostředí Cloud Shell. V následujícím příkazu nahraďte *\<app name>* názvem aplikace.

```azurecli-interactive
az webapp identity assign --resource-group myResourceGroup --name <app name>
```

Tady je příklad výstupu po vytvoření identity v Azure Active Directory:

```json
{
  "additionalProperties": {},
  "principalId": "21dfa71c-9e6f-4d17-9e90-1d28801c9735",
  "tenantId": "72f988bf-86f1-41af-91ab-2d7cd011db47",
  "type": "SystemAssigned"
}
```

Hodnotu `principalId` použijete v dalším kroku. Pokud chcete zobrazit podrobnosti o nové identitě v Azure Active Directory, spusťte následující volitelný příkaz s hodnotou `principalId`:

```azurecli-interactive
az ad sp show --id <principalid>
```

## <a name="grant-database-access-to-identity"></a>Udělení přístupu k databázi identitě

Dále spravované identitě vaší aplikace udělíte přístup k databázi pomocí příkazu [`az sql server ad-admin create`](/cli/azure/sql/server/ad-admin?view=azure-cli-latest#az-sql-server-ad-admin_create) v prostředí Cloud Shell. V následujícím příkazu nahraďte *\<server_name>* názvem serveru a <principalid_from_last_step> za ID instančního objektu z předchozího kroku. Místo *\<admin_user>* zadejte jméno správce.

```azurecli-interactive
az sql server ad-admin create --resource-group myResourceGroup --server-name <server_name> --display-name <admin_user> --object-id <principalid_from_last_step>
```

Spravovaná identita má teď přístup k vašemu serveru služby Azure SQL Database.

## <a name="modify-connection-string"></a>Úprava připojovacího řetězce

Upravte připojení, které jste dříve nastavili pro svou aplikaci, pomocí příkazu [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) ve službě Cloud Shell. V následujícím příkazu nahraďte *\<app name>* názvem vaší aplikace a *\<server_name>* a *\<db_name>* názvem serveru a databáze pro vaši službu SQL Database.

```azurecli-interactive
az webapp config connection-string set --resource-group myResourceGroup --name <app name> --settings MyDbConnection='Server=tcp:<server_name>.database.windows.net,1433;Database=<db_name>;' --connection-string-type SQLAzure
```

## <a name="modify-aspnet-code"></a>Úprava kódu ASP.NET

Ve svém projektu **DotNetAppSqlDb** v sadě Visual Studio otevřete soubor _packages.config_ a do seznamu balíčků přidejte následující řádek.

```xml
<package id="Microsoft.Azure.Services.AppAuthentication" version="1.1.0-preview" targetFramework="net461" />
```

Otevřete soubor _Models\MyDatabaseContext.cs_ a přidejte na jeho začátek následující příkazy `using`:

```csharp
using System.Data.SqlClient;
using Microsoft.Azure.Services.AppAuthentication;
using System.Web.Configuration;
```

Do třídy `MyDatabaseContext` přidejte následující konstruktor:

```csharp
public MyDatabaseContext(SqlConnection conn) : base(conn, true)
{
    conn.ConnectionString = WebConfigurationManager.ConnectionStrings["MyDbConnection"].ConnectionString;
    // DataSource != LocalDB means app is running in Azure with the SQLDB connection string you configured
    if(conn.DataSource != "(localdb)\\MSSQLLocalDB")
        conn.AccessToken = (new AzureServiceTokenProvider()).GetAccessTokenAsync("https://database.windows.net/").Result;

    Database.SetInitializer<MyDatabaseContext>(null);
}
```

Tento konstruktor nakonfiguruje vlastní objekt SqlConnection tak, aby používal přístupový token pro službu Azure SQL Database ze služby App Service. S přístupovým tokenem se vaše aplikace App Service ověří ve službě Azure SQL Database pomocí své spravované identity. Další informace najdete v tématu popisujícím [získání tokenů pro prostředky Azure](app-service-managed-service-identity.md#obtaining-tokens-for-azure-resources). Příkaz `if` umožňuje pokračovat v místním testováním aplikace s místní databází.

> [!NOTE]
> `SqlConnection.AccessToken` se aktuálně podporuje pouze v rozhraní .NET Framework 4.6 a novějším, nikoli v [.NET Core](https://www.microsoft.com/net/learn/get-started/windows).
>

Pokud chcete tento nový konstruktor použít, otevřete soubor `Controllers\TodosController.cs` a vyhledejte řádek `private MyDatabaseContext db = new MyDatabaseContext();`. Stávající kód pomocí výchozího kontroleru `MyDatabaseContext` vytvoří databázi s použitím standardního připojovacího řetězce, který před tím, než [jste ho změnili](#modify-connection-string), obsahoval uživatelské jméno a heslo ve formátu prostého textu.

Celý řádek nahraďte následujícím kódem:

```csharp
private MyDatabaseContext db = new MyDatabaseContext(new SqlConnection());
```

### <a name="publish-your-changes"></a>Publikování provedených změn

Teď už stačí jen publikovat provedené změny do Azure.

V **Průzkumníku řešení** klikněte pravým tlačítkem na projekt **DotNetAppSqlDb** a vyberte **Publikovat**.

![Publikování z Průzkumníka řešení](./media/app-service-web-tutorial-dotnet-sqldatabase/solution-explorer-publish.png)

Na stránce publikování klikněte na **Publikovat**. Pokud se na nové webové stránce zobrazí seznam úkolů, připojuje se vaše aplikace k databázi pomocí spravované identity.

![Webová aplikace Azure po migraci Code First](./media/app-service-web-tutorial-dotnet-sqldatabase/this-one-is-done.png)

Teď byste měli mít možnost upravovat seznam úkolu stejně jako předtím.

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

## <a name="grant-minimal-privileges-to-identity"></a>Udělení minimálních oprávnění identitě

V předchozích krocích jste si pravděpodobně všimli, že je vaše spravovaná identita připojená k SQL Serveru jako správce Azure AD. Pokud chcete spravované identitě udělit minimální oprávnění, musíte se přihlásit k serveru služby Azure SQL Database jako správce Azure AD a pak přidat skupinu Azure Active Directory, která obsahuje tuto spravovanou identitu. 

### <a name="add-managed-identity-to-an-azure-active-directory-group"></a>Přidání spravované identity do skupiny Azure Active Directory

V prostředí Cloud Shell přidejte spravovanou identitu vaší aplikace do nové skupiny Azure Active Directory s názvem _myAzureSQLDBAccessGroup_, jak je znázorněno v následujícím skriptu:

```azurecli-interactive
groupid=$(az ad group create --display-name myAzureSQLDBAccessGroup --mail-nickname myAzureSQLDBAccessGroup --query objectId --output tsv)
msiobjectid=$(az webapp identity show --resource-group <group_name> --name <app_name> --query principalId --output tsv)
az ad group member add --group $groupid --member-id $msiobjectid
az ad group member list -g $groupid
```

Pokud chcete zobrazit úplný výstup JSON pro jednotlivé příkazy, vynechte parametry `--query objectId --output tsv`.

### <a name="reconfigure-azure-ad-administrator"></a>Překonfigurování správce Azure AD

Dříve jste přiřadili spravovanou identitu jako správce Azure AD pro vaši službu SQL Database. Tuto identitu nemůžete použít k interaktivnímu přihlášení (pro přidání uživatelů databáze), takže musíte použít skutečného uživatele Azure AD. Pokud chcete přidat uživatele Azure AD, postupujte podle kroků uvedených v tématu popisujícím [zřízení správce Azure Active Directory pro server služby Azure SQL Database](../sql-database/sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server). 

### <a name="grant-permissions-to-azure-active-directory-group"></a>Udělení oprávnění skupině Azure Active Directory

Ve službě Cloud Shell se přihlaste ke službě SQL Database pomocí příkazu SQLCMD. Nahraďte _\<servername>_ názvem vašeho serveru služby SQL a _\<AADusername>_ a _\<AADpassword>_ přihlašovacími údaji vašeho uživatele Azure AD.

```azurecli-interactive
sqlcmd -S <server_name>.database.windows.net -U <AADuser_name> -P "<AADpassword>" -G -l 30
```

Na příkazovém řádku SQL spusťte následující příkazy, které přidají skupinu Azure Active Directory, kterou jste vytvořili dříve, jako uživatele.

```sql
CREATE USER [myAzureSQLDBAccessGroup] FROM EXTERNAL PROVIDER;
GO
```

Zadáním `EXIT` se vraťte do příkazového řádku služby Cloud Shell. Pak znovu spusťte příkaz SQLCMD, ale místo _\<dbname>_ zadejte název databáze.

```azurecli-interactive
sqlcmd -S <server_name>.database.windows.net -d <db_name> -U <AADuser_name> -P "<AADpassword>" -G -l 30
```

Na příkazovém řádku SQL pro požadovanou databázi spusťte následující příkazy, kterými skupině Azure Active Directory udělíte oprávnění ke čtení i zápisu.

```sql
ALTER ROLE db_datareader ADD MEMBER [myAzureSQLDBAccessGroup];
ALTER ROLE db_datawriter ADD MEMBER [myAzureSQLDBAccessGroup];
GO
```

## <a name="next-steps"></a>Další kroky

Naučili jste se:

> [!div class="checklist"]
> * Povolit spravované identity
> * Udělit přístup ke spravované identitě službě SQL Database
> * Nakonfigurovat kód aplikace tak, aby k ověřování ve službě SQL Database používala ověřování Azure Active Directory
> * Udělit minimální oprávnění ke spravované identitě ve službě SQL Database

V dalším kurzu se dozvíte, jak namapovat vlastní název DNS na webovou aplikaci.

> [!div class="nextstepaction"]
> [Mapování existujícího vlastního názvu DNS na Azure Web Apps](app-service-web-tutorial-custom-domain.md)
