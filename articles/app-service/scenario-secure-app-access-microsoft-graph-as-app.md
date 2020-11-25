---
title: Kurz – webová aplikace přistupuje k Microsoft Graph jako aplikace | Azure
description: V tomto kurzu se naučíte získat přístup k datům v Microsoft Graph pomocí spravovaných identit.
services: microsoft-graph, app-service-web
author: rwike77
manager: CelesteDG
ms.service: app-service-web
ms.topic: tutorial
ms.workload: identity
ms.date: 11/09/2020
ms.author: ryanwi
ms.reviewer: stsoneff
ms.openlocfilehash: a7b8ca309bf5710ddbd88413935bef5e97a1ed9f
ms.sourcegitcommit: 1bf144dc5d7c496c4abeb95fc2f473cfa0bbed43
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/24/2020
ms.locfileid: "95999667"
---
# <a name="tutorial-access-microsoft-graph-from-a-secured-app-as-the-app"></a>Kurz: přístup k Microsoft Graph z zabezpečené aplikace jako aplikace

Naučte se, jak získat přístup k Microsoft Graph z webové aplikace běžící na Azure App Service.

:::image type="content" alt-text="Diagram, který zobrazuje přístup k Microsoft Graph." source="./media/scenario-secure-app-access-microsoft-graph/web-app-access-graph.svg" border="false":::

Chcete volat Microsoft Graph pro webovou aplikaci. Bezpečný způsob, jak poskytnout přístup k datům vaší webové aplikaci, je použití [spravované identity přiřazené systémem](/azure/active-directory/managed-identities-azure-resources/overview). Spravovaná identita z Azure Active Directory umožňuje App Service přistupovat k prostředkům prostřednictvím řízení přístupu na základě role (RBAC), aniž by museli vyžadovat přihlašovací údaje aplikace. Po přiřazení spravované identity k webové aplikaci se Azure postará o vytvoření a distribuci certifikátu. Nemusíte si dělat starosti se správou tajných klíčů nebo přihlašovacích údajů aplikací.

V tomto kurzu se naučíte:

> [!div class="checklist"]
>
> * Vytvořte spravovanou identitu přiřazenou systémem na webové aplikaci.
> * Přidejte Microsoft Graph oprávnění API ke spravované identitě.
> * Volání Microsoft Graph z webové aplikace pomocí spravovaných identit.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Požadavky

* Webová aplikace spuštěná v Azure App Service s [povoleným App Servicem ověřováním/autorizačním modulem](scenario-secure-app-authentication-app-service.md).

## <a name="enable-managed-identity-on-app"></a>Povolit spravovanou identitu v aplikaci

Pokud webovou aplikaci vytváříte a publikujete prostřednictvím sady Visual Studio, ve vaší aplikaci je povolená spravovaná identita za vás. Ve službě App Service v levém podokně vyberte **Identita** a pak vyberte **přiřazený systém**. Ověřte, zda je **stav** nastaven na hodnotu **zapnuto**. Pokud ne, vyberte **Uložit** a vyberte **Ano** , pokud chcete povolit spravovanou identitu přiřazenou systémem. Je-li povolena spravovaná identita, je stav nastaven na hodnotu **zapnuto** a je k dispozici ID objektu.

Poznamenejte si hodnotu **ID objektu** , kterou budete potřebovat v dalším kroku.

:::image type="content" alt-text="Snímek obrazovky zobrazující identitu přiřazenou systémem" source="./media/scenario-secure-app-access-microsoft-graph/create-system-assigned-identity.png":::

## <a name="grant-access-to-microsoft-graph"></a>Udělit přístup k Microsoft Graph

Při přístupu k Microsoft Graph musí mít spravovaná identita správná oprávnění pro operaci, kterou chce provést. V současné době není k dispozici možnost přiřazení takových oprávnění prostřednictvím Azure Portal. Následující skript přidá požadovaná oprávnění Microsoft Graph API k objektu Managed identity instančního objektu.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```powershell
# Install the module. (You need admin on the machine.)
# Install-Module AzureAD.

# Your tenant ID (in the Azure portal, under Azure Active Directory > Overview).
$TenantID="<tenant-id>"
$resourceGroup = "securewebappresourcegroup"
$webAppName="SecureWebApp-20201102125811"

# Get the ID of the managed identity for the web app.
$spID = (Get-AzWebApp -ResourceGroupName $resourceGroup -Name $webAppName).identity.principalid

# Check the Microsoft Graph documentation for the permission you need for the operation.
$PermissionName = "User.Read.All"

Connect-AzureAD -TenantId $TenantID

# Get the service principal for Microsoft Graph.
$GraphServicePrincipal = Get-AzureADServicePrincipal -SearchString "Microsoft Graph"

# Assign permissions to the managed identity service principal.
$AppRole = $GraphServicePrincipal.AppRoles | `
Where-Object {$_.Value -eq $PermissionName -and $_.AllowedMemberTypes -contains "Application"}

New-AzureAdServiceAppRoleAssignment -ObjectId $spID -PrincipalId $spID `
-ResourceId $GraphServicePrincipal.ObjectId -Id $AppRole.Id
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az login

webAppName="SecureWebApp-20201106120003"

spId=$(az resource list -n $webAppName --query [*].identity.principalId --out tsv)

graphResourceId=$(az ad sp list --display-name "Microsoft Graph" --query [0].objectId --out tsv)

appRoleId=$(az ad sp list --display-name "Microsoft Graph" --query "[0].appRoles[?value=='User.Read.All' && contains(allowedMemberTypes, 'Application')].id" --output tsv)

uri=https://graph.microsoft.com/v1.0/servicePrincipals/$spID/appRoleAssignments

body="{'principalId':'$spId','resourceId':'$graphResourceId','appRoleId':'$appRoleId'}"

az rest --method post --uri $uri --body $body --headers "Content-Type=application/json"
```

---

Po spuštění skriptu můžete ověřit v [Azure Portal](https://portal.azure.com) , že k spravované identitě jsou přiřazena požadovaná oprávnění rozhraní API.

Přejít na **Azure Active Directory** a pak vyberte **podnikové aplikace**. V tomto podokně se zobrazí všechny objekty služby ve vašem tenantovi. V části **všechny aplikace** vyberte instanční objekt spravované identity. 

Pokud s tímto kurzem pracujete, existují dva instanční objekty se stejným zobrazovaným názvem (například SecureWebApp2020094113531). Instanční objekt, který má **adresu URL domovské stránky** , představuje webovou aplikaci ve vašem tenantovi. Instanční objekt bez **adresy URL domovské stránky** představuje spravovanou identitu přiřazenou systémem pro vaši webovou aplikaci. Hodnota **ID objektu** pro spravovanou identitu se shoduje s ID objektu spravované identity, kterou jste předtím vytvořili.

Vyberte instanční objekt pro spravovanou identitu.

:::image type="content" alt-text="Snímek obrazovky zobrazující možnost všechny aplikace" source="./media/scenario-secure-app-access-microsoft-graph/enterprise-apps-all-applications.png":::

V části **Přehled** vyberte **oprávnění** a zobrazí se jim přidaná oprávnění pro Microsoft Graph.

:::image type="content" alt-text="Snímek obrazovky, který zobrazuje podokno oprávnění" source="./media/scenario-secure-app-access-microsoft-graph/enterprise-apps-permissions.png":::

## <a name="call-microsoft-graph-net"></a>Volat Microsoft Graph (.NET)

Třída [DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential) slouží k získání přihlašovacích údajů tokenu pro váš kód k autorizaci požadavků na Microsoft Graph. Vytvořte instanci třídy [DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential) , která pomocí spravované identity načte tokeny a připojí je k klientovi služby. Následující příklad kódu získá pověření ověřeného tokenu a použije ho k vytvoření objektu klienta služby, který získá uživatele ve skupině.

### <a name="install-the-microsoftgraph-client-library-package"></a>Instalace balíčku klientské knihovny Microsoft. Graph

Do projektu nainstalujte [balíček NuGet Microsoft. Graph](https://www.nuget.org/packages/Microsoft.Graph) pomocí rozhraní příkazového řádku .NET Core nebo konzoly Správce balíčků v aplikaci Visual Studio.

# <a name="command-line"></a>[Příkazový řádek](#tab/command-line)

Otevřete příkazový řádek a přejděte do adresáře, který obsahuje soubor projektu.

Spusťte příkazy install.

```dotnetcli
dotnet add package Microsoft.Graph
```

# <a name="package-manager"></a>[Správce balíčků](#tab/package-manager)

Otevřete projekt nebo řešení v aplikaci Visual Studio a otevřete konzolu pomocí **Tools**  >  příkazu **Správce balíčků NuGet správce** balíčků nástroje  >  **Package Manager Console** .

Spusťte příkazy install.
```powershell
Install-Package Microsoft.Graph
```

---

### <a name="example"></a>Příklad

```csharp
using System;
using System.Collections.Generic;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Mvc.RazorPages;
using Azure.Identity;
using Microsoft.Graph.Core;
using System.Net.Http.Headers;

...

public IList<MSGraphUser> Users { get; set; }

public async Task OnGetAsync()
{
    // Create the Microsoft Graph service client with a DefaultAzureCredential class, which gets an access token by using the available Managed Identity.
    var credential = new DefaultAzureCredential();
    var token = credential.GetToken(
        new Azure.Core.TokenRequestContext(
            new[] { "https://graph.microsoft.com/.default" }));

    var accessToken = token.Token;
    var graphServiceClient = new GraphServiceClient(
        new DelegateAuthenticationProvider((requestMessage) =>
        {
            requestMessage
            .Headers
            .Authorization = new AuthenticationHeaderValue("bearer", accessToken);

            return Task.CompletedTask;
        }));

    List<MSGraphUser> msGraphUsers = new List<MSGraphUser>();
    try
    {
        var users =await graphServiceClient.Users.Request().GetAsync();
        foreach(var u in users)
        {
            MSGraphUser user = new MSGraphUser();
            user.userPrincipalName = u.UserPrincipalName;
            user.displayName = u.DisplayName;
            user.mail = u.Mail;
            user.jobTitle = u.JobTitle;

            msGraphUsers.Add(user);
        }
    }
    catch(Exception ex)
    {
        string msg = ex.Message;
    }

    Users = msGraphUsers;
}
```

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud jste s tímto kurzem hotovi a už nepotřebujete webovou aplikaci ani související prostředky, [vyčistěte prostředky, které jste vytvořili](scenario-secure-app-clean-up-resources.md).

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili:

> [!div class="checklist"]
>
> * Vytvořte spravovanou identitu přiřazenou systémem na webové aplikaci.
> * Přidejte Microsoft Graph oprávnění API ke spravované identitě.
> * Volání Microsoft Graph z webové aplikace pomocí spravovaných identit.

Naučte se, jak připojit [aplikaci .NET Core](tutorial-dotnetcore-sqldb-app.md), [aplikaci Python](tutorial-python-postgresql-app.md), [aplikaci Java](tutorial-java-spring-cosmosdb.md)nebo [ aplikaciNode.js](tutorial-nodejs-mongodb-app.md) k databázi.
