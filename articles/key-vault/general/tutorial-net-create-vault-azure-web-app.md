---
title: Kurz – použití Azure Key Vault s webovou aplikací Azure v .NET
description: V tomto kurzu nakonfigurujete webovou aplikaci Azure v aplikaci ASP.NET Core pro čtení tajného klíče z vašeho trezoru klíčů.
services: key-vault
author: msmbaldwin
manager: rajvijan
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 05/06/2020
ms.author: mbaldwin
ms.custom: devx-track-csharp, devx-track-azurecli
ms.openlocfilehash: f0121a264c3fcf4cba1de72a1b9b81c1af66e82a
ms.sourcegitcommit: 192f9233ba42e3cdda2794f4307e6620adba3ff2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/26/2020
ms.locfileid: "96297096"
---
# <a name="tutorial-use-a-managed-identity-to-connect-key-vault-to-an-azure-web-app-in-net"></a>Kurz: použití spravované identity pro připojení Key Vault k webové aplikaci Azure v .NET

[Azure Key Vault](https://docs.microsoft.com/azure/key-vault/general/overview) poskytuje způsob ukládání přihlašovacích údajů a dalších tajných kódů s vyšším zabezpečením. Váš kód ale musí ověřit, aby se Key Vault načíst. [Spravované identity pro prostředky Azure](../../active-directory/managed-identities-azure-resources/overview.md) usnadňují řešení tohoto problému tím, že službám Azure poskytne automaticky spravovanou identitu ve službě Azure Active Directory (Azure AD). Tuto identitu můžete použít k ověření pro libovolnou službu, která podporuje ověřování Azure AD, včetně Key Vault, bez nutnosti zobrazovat přihlašovací údaje v kódu.

V tomto kurzu použijete spravovanou identitu k ověření webové aplikace Azure pomocí trezoru klíčů Azure. Použijete [klientskou knihovnu Azure Key Vault verze 4 pro .NET](/dotnet/api/overview/azure/key-vault) a [Azure CLI](/cli/azure/get-started-with-azure-cli). Stejné základní principy platí při použití vývojového jazyka dle vašeho výběru, Azure PowerShell a/nebo Azure Portal.

## <a name="prerequisites"></a>Požadované součásti

Co budete potřebovat k dokončení tohoto rychlého startu:

* Předplatné Azure. [Vytvořte si ho zdarma.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* [Sada .NET Core 3,1 SDK (nebo novější)](https://dotnet.microsoft.com/download/dotnet-core/3.1).
* Instalace [Gitu](https://www.git-scm.com/downloads) .
* Rozhraní příkazového [řádku Azure](/cli/azure/install-azure-cli) nebo [Azure PowerShell](/powershell/azure/).
* [Azure Key Vault.](https://docs.microsoft.com/azure/key-vault/general/overview) Trezor klíčů můžete vytvořit pomocí [Azure Portal](quick-create-portal.md), rozhraní příkazového [řádku Azure](quick-create-cli.md)nebo [Azure PowerShell](quick-create-powershell.md).
* Key Vault [tajný klíč](https://docs.microsoft.com/azure/key-vault/secrets/about-secrets). Tajný klíč můžete vytvořit pomocí [Azure Portal](https://docs.microsoft.com/azure/key-vault/secrets/quick-create-portal), [PowerShellu](https://docs.microsoft.com/azure/key-vault/secrets/quick-create-powershell)nebo rozhraní příkazového [řádku Azure CLI](https://docs.microsoft.com/azure/key-vault/secrets/quick-create-cli).

## <a name="create-a-net-core-app"></a>Vytvoření aplikace .NET Core
V tomto kroku nastavíte místní projekt .NET Core.

V okně terminálu na svém počítači vytvořte adresář s názvem `akvwebapp` a nastavte ho jako aktuální adresář:

```bash
mkdir akvwebapp
cd akvwebapp
```

Vytvořte aplikaci .NET Core pomocí příkazu [dotnet New Web](/dotnet/core/tools/dotnet-new) :

```bash
dotnet new web
```

Spusťte aplikaci místně, abyste věděli, jak by měla vypadat při nasazení do Azure:

```bash
dotnet run
```

Ve webovém prohlížeči přejdete do aplikace na adrese `http://localhost:5000` .

Uvidíte zprávu „Hello World!“, Zpráva z ukázkové aplikace zobrazené na stránce

## <a name="deploy-the-app-to-azure"></a>Nasadit aplikaci do Azure

V tomto kroku nasadíte aplikaci .NET Core pro Azure App Service pomocí místního Gitu. Další informace o tom, jak vytvořit a nasadit aplikace, najdete v tématu [Vytvoření webové aplikace v ASP.NET Core v Azure](https://docs.microsoft.com/azure/app-service/quickstart-dotnetcore).

### <a name="configure-the-local-git-deployment"></a>Konfigurace místního nasazení Git

V okně terminálu vyberte **CTRL + C** a zavřete tak webový server.  Inicializujte úložiště Git pro projekt .NET Core:

```bash
git init
git add .
git commit -m "first commit"
```

Pomocí FTP a místního Gitu můžete nasadit webovou aplikaci Azure pomocí *uživatele nasazení*. Po nakonfigurování uživatele nasazení ho můžete použít pro všechna nasazení Azure. Uživatelské jméno a heslo nasazení na úrovni účtu se liší od přihlašovacích údajů předplatného Azure. 

Pokud chcete nakonfigurovat uživatele nasazení, spusťte příkaz [AZ WebApp Deployment User set](/cli/azure/webapp/deployment/user?#az-webapp-deployment-user-set) . Vyberte uživatelské jméno a heslo, které dodržuje tyto pokyny: 

- Uživatelské jméno musí být jedinečné v rámci Azure. Pro místní nabízená oznámení Git nesmí obsahovat symbol symbol @ (@). 
- Heslo musí obsahovat alespoň osm znaků a obsahovat dva z následujících tří prvků: písmena, číslice a symboly. 

```azurecli-interactive
az webapp deployment user set --user-name "<username>" --password "<password>"
```

Výstup JSON zobrazuje heslo jako `null` . Pokud se zobrazí `'Conflict'. Details: 409` Chyba, změňte uživatelské jméno. Pokud se zobrazí chyba `'Bad Request'. Details: 400`, použijte silnější heslo. 

Poznamenejte si své uživatelské jméno a heslo, abyste ho mohli použít k nasazení svých webových aplikací.

### <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Skupina prostředků je logický kontejner, do kterého nasazujete prostředky Azure a spravujete je. Vytvořte skupinu prostředků, která bude obsahovat váš Trezor klíčů a webovou aplikaci, pomocí příkazu [AZ Group Create](/cli/azure/group?#az-group-create) :

```azurecli-interactive
az group create --name "myResourceGroup" -l "EastUS"
```

### <a name="create-an-app-service-plan"></a>Vytvoření plánu služby App Service

Pomocí příkazu Azure CLI [AZ AppService Plan Create](/cli/azure/appservice/plan) vytvořte [plán App Service](https://docs.microsoft.com/azure/app-service/overview-hosting-plans) . Následující příklad vytvoří plán App Service s názvem `myAppServicePlan` v `FREE` cenové úrovni:

```azurecli-interactive
az appservice plan create --name myAppServicePlan --resource-group myResourceGroup --sku FREE
```

Po vytvoření plánu App Service se v Azure CLI zobrazí podobné informace jako v následujícím příkladu:

<pre>
{ 
  "adminSiteName": null,
  "appServicePlanName": "myAppServicePlan",
  "geoRegion": "West Europe",
  "hostingEnvironmentProfile": null,
  "id": "/subscriptions/0000-0000/resourceGroups/myResourceGroup/providers/Microsoft.Web/serverfarms/myAppServicePlan",
  "kind": "app",
  "location": "West Europe",
  "maximumNumberOfWorkers": 1,
  "name": "myAppServicePlan",
  &lt; JSON data removed for brevity. &gt;
  "targetWorkerSizeId": 0,
  "type": "Microsoft.Web/serverfarms",
  "workerTierName": null
} 
</pre>

Další informace najdete v článku [Správa plánu služby App Service v Azure](https://docs.microsoft.com/azure/app-service/app-service-plan-manage).

### <a name="create-a-web-app"></a>Vytvoření webové aplikace

Vytvořte [webovou aplikaci Azure](../../app-service/overview.md) v `myAppServicePlan` plánu App Service. 

> [!Important]
> Podobně jako u trezoru klíčů musí mít webová aplikace Azure jedinečný název. Nahraďte `<your-webapp-name>` názvem vaší webové aplikace v následujících příkladech.


```azurecli-interactive
az webapp create --resource-group "myResourceGroup" --plan "myAppServicePlan" --name "<your-webapp-name>" --deployment-local-git
```

Po vytvoření webové aplikace se v Azure CLI zobrazí výstup podobný tomu, co vidíte tady:

<pre>
Local git is configured with url of 'https://&lt;username&gt;@&lt;your-webapp-name&gt;.scm.azurewebsites.net/&lt;ayour-webapp-name&gt;.git'
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "clientCertExclusionPaths": null,
  "cloningInfo": null,
  "containerSize": 0,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "&lt;your-webapp-name&gt;.azurewebsites.net",
  "deploymentLocalGitUrl": "https://&lt;username&gt;@&lt;your-webapp-name&gt;.scm.azurewebsites.net/&lt;your-webapp-name&gt;.git",
  "enabled": true,
  &lt; JSON data removed for brevity. &gt;
}
</pre>


Adresa URL vzdáleného úložiště Git se zobrazí ve `deploymentLocalGitUrl` vlastnosti ve formátu `https://<username>@<your-webapp-name>.scm.azurewebsites.net/<your-webapp-name>.git` . Uložte tuto adresu URL. Budete ho potřebovat později.

Pomocí následujícího příkazu přejdete k nové aplikaci. Nahraďte `<your-webapp-name>` názvem vaší aplikace.

```bash
https://<your-webapp-name>.azurewebsites.net
```

Zobrazí se výchozí webová stránka pro novou webovou aplikaci Azure.

### <a name="deploy-your-local-app"></a>Nasazení místní aplikace

Zpět v okně místního terminálu přidejte vzdálené úložiště Azure do místního úložiště Git. V následujícím příkazu nahraďte `<deploymentLocalGitUrl-from-create-step>` adresu URL vzdáleného úložiště Git, který jste uložili v části [Vytvoření webové aplikace](#create-a-web-app) .

```bash
git remote add azure <deploymentLocalGitUrl-from-create-step>
```

Pomocí následujícího příkazu nahrajte do vzdáleného úložiště Azure a nasaďte svoji aplikaci. Když vám správce přihlašovacích údajů Git vyzve k zadání přihlašovacích údajů, použijte přihlašovací údaje, které jste vytvořili v části [Konfigurace místního nasazení Git](#configure-the-local-git-deployment) .

```bash
git push azure master
```

Spuštění tohoto příkazu může trvat několik minut. Při spuštění se zobrazí podobné informace jako v následujícím příkladu:
<pre>
Enumerating objects: 5, done.
Counting objects: 100% (5/5), done.
Compressing objects: 100% (3/3), done.
Writing objects: 100% (3/3), 285 bytes | 95.00 KiB/s, done.
Total 3 (delta 2), reused 0 (delta 0), pack-reused 0
remote: Deploy Async
remote: Updating branch 'master'.
remote: Updating submodules.
remote: Preparing deployment for commit id 'd6b54472f7'.
remote: Repository path is /home/site/repository
remote: Running oryx build...
remote: Build orchestrated by Microsoft Oryx, https://github.com/Microsoft/Oryx
remote: You can report issues at https://github.com/Microsoft/Oryx/issues
remote:
remote: Oryx Version      : 0.2.20200114.13, Commit: 204922f30f8e8d41f5241b8c218425ef89106d1d, ReleaseTagName: 20200114.13
remote: Build Operation ID: |imoMY2y77/s=.40ca2a87_
remote: Repository Commit : d6b54472f7e8e9fd885ffafaa64522e74cf370e1
.
.
.
remote: Deployment successful.
remote: Deployment Logs : 'https://&lt;your-webapp-name&gt;.scm.azurewebsites.net/newui/jsonviewer?view_url=/api/deployments/d6b54472f7e8e9fd885ffafaa64522e74cf370e1/log'
To https://&lt;your-webapp-name&gt;.scm.azurewebsites.net:443/&lt;your-webapp-name&gt;.git
   d87e6ca..d6b5447  master -> master
</pre>

Pomocí webového prohlížeče (nebo aktualizujte) nasazené aplikace:

```bash
http://<your-webapp-name>.azurewebsites.net
```

Uvidíte zprávu „Hello World!“, zpráva, kterou jste viděli dříve po navštívení `http://localhost:5000` .
 
## <a name="configure-the-web-app-to-connect-to-key-vault"></a>Konfigurace webové aplikace pro připojení k Key Vault

V této části nakonfigurujete webový přístup tak, aby Key Vault a aktualizovali kód aplikace pro načtení tajného klíče z Key Vault.

### <a name="create-and-assign-a-managed-identity"></a>Vytvoření a přiřazení spravované identity

V tomto kurzu použijeme [spravovanou identitu](../../active-directory/managed-identities-azure-resources/overview.md) k ověření Key Vault. Spravovaná identita automaticky spravuje přihlašovací údaje aplikací.

V Azure CLI pro vytvoření identity pro aplikaci spusťte příkaz [AZ WebApp-identity Assign](/cli/azure/webapp/identity?#az-webapp-identity-assign) :

```azurecli-interactive
az webapp identity assign --name "<your-webapp-name>" --resource-group "myResourceGroup"
```

Příkaz vrátí tento fragment kódu JSON:

```json
{
  "principalId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "tenantId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "type": "SystemAssigned"
}
```

Pokud chcete vaší webové aplikaci udělit oprávnění k **získání** a **výpisu** operací v trezoru klíčů, předejte `principalId` do Azure CLI příkaz [AZ Key trezor set-Policy](/cli/azure/keyvault?#az-keyvault-set-policy) :

```azurecli-interactive
az keyvault set-policy --name "<your-keyvault-name>" --object-id "<principalId>" --secret-permissions get list
```

Zásady přístupu můžete také přiřadit pomocí [Azure Portal](https://docs.microsoft.com/azure/key-vault/general/assign-access-policy-portal) nebo [PowerShellu](https://docs.microsoft.com/azure/key-vault/general/assign-access-policy-powershell).

### <a name="modify-the-app-to-access-your-key-vault"></a>Úprava aplikace pro přístup k trezoru klíčů

#### <a name="install-the-packages"></a>Nainstalovat balíčky

V okně terminálu nainstalujte Azure Key Vault klientskou knihovnu pro balíčky .NET:

```console
dotnet add package Azure.Identity
dotnet add package Azure.Security.KeyVault.Secrets
```

#### <a name="update-the-code"></a>Aktualizace kódu

Vyhledejte a otevřete soubor Startup.cs v projektu akvwebapp. 

Přidejte tyto řádky do záhlaví:

```csharp
using Azure.Identity;
using Azure.Security.KeyVault.Secrets;
using Azure.Core;
```

Přidejte následující řádky před `app.UseEndpoints` voláním, aktualizujte identifikátor URI tak, aby odrážely `vaultUri` váš Trezor klíčů. Tento kód používá  [DefaultAzureCredential ()](/dotnet/api/azure.identity.defaultazurecredential) k ověření pro Key Vault, které používá token ze spravované identity k ověření. Další informace o ověřování pro Key Vault najdete v [příručce pro vývojáře](https://docs.microsoft.com/azure/key-vault/general/developers-guide#authenticate-to-key-vault-in-code). Kód také používá exponenciální omezení rychlosti pro opakování v případě omezení Key Vault. Další informace o Key Vault omezeních transakcí najdete v tématu [pokyny k omezování Azure Key Vault](https://docs.microsoft.com/azure/key-vault/general/overview-throttling).

```csharp
SecretClientOptions options = new SecretClientOptions()
    {
        Retry =
        {
            Delay= TimeSpan.FromSeconds(2),
            MaxDelay = TimeSpan.FromSeconds(16),
            MaxRetries = 5,
            Mode = RetryMode.Exponential
         }
    };
var client = new SecretClient(new Uri("https://<your-unique-key-vault-name>.vault.azure.net/"), new DefaultAzureCredential(),options);

KeyVaultSecret secret = client.GetSecret("<mySecret>");

string secretValue = secret.Value;
```

Aktualizujte řádek `await context.Response.WriteAsync("Hello World!");` , aby vypadal jako tento řádek:

```csharp
await context.Response.WriteAsync(secretValue);
```

Než budete pokračovat k dalšímu kroku, nezapomeňte změny uložit.

#### <a name="redeploy-your-web-app"></a>Opětovné nasazení webové aplikace

Teď, když jste aktualizovali kód, můžete ho znovu nasadit do Azure pomocí těchto příkazů git:

```bash
git add .
git commit -m "Updated web app to access my key vault"
git push azure master
```

## <a name="go-to-your-completed-web-app"></a>Přejít na dokončenou webovou aplikaci

```bash
http://<your-webapp-name>.azurewebsites.net
```

Místo, kde jste si viděli "Hello World!", by se teď měla zobrazit hodnota zobrazeného tajného kódu: "úspěch!"

## <a name="next-steps"></a>Další kroky

- [Použití Azure Key Vault s aplikacemi nasazenými do virtuálního počítače v .NET](https://docs.microsoft.com/azure/key-vault/general/tutorial-net-virtual-machine)
- Další informace o [spravovaných identitách pro prostředky Azure](../../active-directory/managed-identities-azure-resources/overview.md)
- Další informace o [spravovaných identitách pro App Service](../../app-service/overview-managed-identity.md?tabs=dotnet)
- Zobrazit [příručku pro vývojáře](https://docs.microsoft.com/azure/key-vault/general/developers-guide)
- [Zabezpečený přístup k trezoru klíčů](https://docs.microsoft.com/azure/key-vault/general/secure-your-key-vault)


