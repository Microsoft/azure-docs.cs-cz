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
ms.openlocfilehash: 2960726cf687908e8e4aed9333fce490dd7ff006
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/26/2021
ms.locfileid: "98788733"
---
# <a name="tutorial-use-a-managed-identity-to-connect-key-vault-to-an-azure-web-app-in-net"></a>Kurz: použití spravované identity pro připojení Key Vault k webové aplikaci Azure v .NET

[Azure Key Vault](./overview.md) poskytuje způsob ukládání přihlašovacích údajů a dalších tajných kódů s vyšším zabezpečením. Váš kód ale musí ověřit, aby se Key Vault načíst. [Spravované identity pro prostředky Azure](../../active-directory/managed-identities-azure-resources/overview.md) usnadňují řešení tohoto problému tím, že službám Azure poskytne automaticky spravovanou identitu ve službě Azure Active Directory (Azure AD). Tuto identitu můžete použít k ověření pro libovolnou službu, která podporuje ověřování Azure AD, včetně Key Vault, bez nutnosti zobrazovat přihlašovací údaje v kódu.

V tomto kurzu vytvoříte a nasadíte webovou aplikaci Azure pro [Azure App Service](../../app-service/overview.md). Pomocí spravované identity můžete svoji webovou aplikaci Azure ověřit pomocí trezoru klíčů Azure pomocí [Azure Key Vault tajné klientské knihovny pro .NET](/dotnet/api/overview/azure/key-vault) a [Azure CLI](/cli/azure/get-started-with-azure-cli). Stejné základní principy platí při použití vývojového jazyka dle vašeho výběru, Azure PowerShell a/nebo Azure Portal.

Další informace o webových aplikacích a nasazení služby Azure App Service prezentovaných v tomto kurzu najdete v tématech:
- [Přehled služby App Service](../../app-service/overview.md)
- [Vytvoření webové aplikace v ASP.NET Core v Azure App Service](../../app-service/quickstart-dotnetcore.md)
- [Místní nasazení Gitu pro Azure App Service](../../app-service/deploy-local-git.md)

## <a name="prerequisites"></a>Požadavky

Pro absolvování tohoto kurzu potřebujete:

* Předplatné Azure. [Vytvořte si ho zdarma.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* [Sada .NET Core 3,1 SDK (nebo novější)](https://dotnet.microsoft.com/download/dotnet-core/3.1).
* Instalace [Gitu](https://www.git-scm.com/downloads) .
* Rozhraní příkazového [řádku Azure](/cli/azure/install-azure-cli) nebo [Azure PowerShell](/powershell/azure/).
* [Azure Key Vault.](./overview.md) Trezor klíčů můžete vytvořit pomocí [Azure Portal](quick-create-portal.md), rozhraní příkazového [řádku Azure](quick-create-cli.md)nebo [Azure PowerShell](quick-create-powershell.md).
* Key Vault [tajný klíč](../secrets/about-secrets.md). Tajný klíč můžete vytvořit pomocí [Azure Portal](../secrets/quick-create-portal.md), [PowerShellu](../secrets/quick-create-powershell.md)nebo rozhraní příkazového [řádku Azure CLI](../secrets/quick-create-cli.md).

Pokud již máte webovou aplikaci nasazenou v Azure App Service, můžete přeskočit ke [konfiguraci přístupu k webové aplikaci k trezoru klíčů](#create-and-assign-a-managed-identity) a [úpravám částí kódu webové aplikace](#modify-the-app-to-access-your-key-vault) .

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

Další informace o vytváření webových aplikací pro Azure najdete v tématu [Vytvoření webové aplikace v ASP.NET Core v Azure App Service](../../app-service/quickstart-dotnetcore.md)

## <a name="deploy-the-app-to-azure"></a>Nasadit aplikaci do Azure

V tomto kroku nasadíte aplikaci .NET Core pro Azure App Service pomocí místního Gitu. Další informace o tom, jak vytvořit a nasadit aplikace, najdete v tématu [Vytvoření webové aplikace v ASP.NET Core v Azure](../../app-service/quickstart-dotnetcore.md).

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

Pomocí příkazu Azure CLI [AZ AppService Plan Create](/cli/azure/appservice/plan) vytvořte [plán App Service](../../app-service/overview-hosting-plans.md) . Následující příklad vytvoří plán App Service s názvem `myAppServicePlan` v `FREE` cenové úrovni:

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

Další informace najdete v článku [Správa plánu služby App Service v Azure](../../app-service/app-service-plan-manage.md).

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
git push azure main
```

Spuštění tohoto příkazu může trvat několik minut. Při spuštění se zobrazí podobné informace jako v následujícím příkladu:
<pre>
Enumerating objects: 5, done.
Counting objects: 100% (5/5), done.
Compressing objects: 100% (3/3), done.
Writing objects: 100% (3/3), 285 bytes | 95.00 KiB/s, done.
Total 3 (delta 2), reused 0 (delta 0), pack-reused 0
remote: Deploy Async
remote: Updating branch 'main'.
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
   d87e6ca..d6b5447  main -> main
</pre>

Pomocí webového prohlížeče (nebo aktualizujte) nasazené aplikace:

```bash
http://<your-webapp-name>.azurewebsites.net
```

Uvidíte zprávu „Hello World!“, zpráva, kterou jste viděli dříve po navštívení `http://localhost:5000` .

Další informace o nasazení webové aplikace pomocí Gitu najdete v tématu [nasazení do místního úložiště git Azure App Service](../../app-service/deploy-local-git.md)
 
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

Zásady přístupu můžete také přiřadit pomocí [Azure Portal](./assign-access-policy-portal.md) nebo [PowerShellu](./assign-access-policy-powershell.md).

### <a name="modify-the-app-to-access-your-key-vault"></a>Úprava aplikace pro přístup k trezoru klíčů

V tomto kurzu použijete pro demonstrační účely [knihovnu klienta Azure Key Vault tajných klíčů](/dotnet/api/overview/azure/security.keyvault.secrets-readme) . Můžete také použít [Azure Key Vault klientské knihovny certifikátů](/dotnet/api/overview/azure/security.keyvault.certificates-readme)nebo [Azure Key Vault klíčovou knihovnu klienta](/dotnet/api/overview/azure/security.keyvault.keys-readme).

#### <a name="install-the-packages"></a>Nainstalovat balíčky

V okně terminálu nainstalujte pro balíčky klientské knihovny .NET a Azure identity klienta Azure Key Vault tajnou klíčovou knihovnu:

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

Přidejte následující řádky před `app.UseEndpoints` voláním, aktualizujte identifikátor URI tak, aby odrážely `vaultUri` váš Trezor klíčů. Tento kód používá  [DefaultAzureCredential ()](/dotnet/api/azure.identity.defaultazurecredential) k ověření pro Key Vault, které používá token ze spravované identity k ověření. Další informace o ověřování pro Key Vault najdete v [příručce pro vývojáře](./developers-guide.md#authenticate-to-key-vault-in-code). Kód také používá exponenciální omezení rychlosti pro opakování v případě omezení Key Vault. Další informace o Key Vault omezeních transakcí najdete v tématu [pokyny k omezování Azure Key Vault](./overview-throttling.md).

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
git push azure main
```

## <a name="go-to-your-completed-web-app"></a>Přejít na dokončenou webovou aplikaci

```bash
http://<your-webapp-name>.azurewebsites.net
```

Místo, kde jste si viděli "Hello World!", by se teď měla zobrazit hodnota zobrazeného tajného kódu.

## <a name="next-steps"></a>Další kroky

- [Použití Azure Key Vault s aplikacemi nasazenými do virtuálního počítače v .NET](./tutorial-net-virtual-machine.md)
- Další informace o [spravovaných identitách pro prostředky Azure](../../active-directory/managed-identities-azure-resources/overview.md)
- Zobrazit [příručku pro vývojáře](./developers-guide.md)
- [Zabezpečení přístupu k trezoru klíčů](./secure-your-key-vault.md)