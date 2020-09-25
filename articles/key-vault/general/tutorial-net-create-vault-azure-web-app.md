---
title: Kurz – použití Azure Key Vault s Azure WebApp v .NET | Microsoft Docs
description: V tomto kurzu nakonfigurujete a Azure WebApp v aplikaci ASP.NET Core pro čtení tajného klíče z vašeho trezoru klíčů.
services: key-vault
author: msmbaldwin
manager: rajvijan
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 05/06/2020
ms.author: mbaldwin
ms.custom: devx-track-csharp
ms.openlocfilehash: e537bb74655bce5c8438e22fb9b990b72eab73d7
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91336679"
---
# <a name="tutorial-use-a-managed-identity-to-connect-key-vault-to-an-azure-web-app-with-net"></a>Kurz: použití spravované identity pro připojení Key Vault k webové aplikaci Azure pomocí .NET

Azure Key Vault poskytuje způsob, jak bezpečně ukládat přihlašovací údaje a další tajné kódy, ale váš kód musí ověřit, aby se Key Vaulty načetly. [Přehled spravovaných identit pro prostředky Azure](../../active-directory/managed-identities-azure-resources/overview.md) pomáhá vyřešit tento problém tím, že poskytuje službám Azure automaticky spravovanou identitu ve službě Azure AD. Tuto identitu můžete použít k ověření pro libovolnou službu, která podporuje ověřování Azure AD, včetně Key Vault, bez nutnosti zobrazovat přihlašovací údaje v kódu.

V tomto kurzu se používá spravovaná identita k ověření webové aplikace Azure pomocí Azure Key Vault. I když postup používá [klientskou knihovnu Azure Key Vault v4 pro .NET](/dotnet/api/overview/azure/key-vault?view=azure-dotnet) a [Azure CLI](/cli/azure/get-started-with-azure-cli), platí stejné základní zásady i při použití vývojového jazyka dle vašeho výběru, Azure PowerShell nebo Azure Portal.

## <a name="prerequisites"></a>Požadavky

K provedení kroků v tomto kurzu Rychlý start je potřeba:

* Předplatné Azure – [Vytvořte si ho zdarma](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [.NET Core 3,1 SDK nebo novější](https://dotnet.microsoft.com/download/dotnet-core/3.1).
* Rozhraní příkazového [řádku Azure](/cli/azure/install-azure-cli?view=azure-cli-latest) nebo [Azure PowerShell](/powershell/azure/)

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Skupina prostředků je logický kontejner, ve kterém se nasazují a spravují prostředky Azure. Vytvořte skupinu prostředků pro vytvoření trezoru klíčů i webové aplikace pomocí příkazu [AZ Group Create](/cli/azure/group?view=azure-cli-latest#az-group-create) :

```azurecli-interactive
az group create --name "myResourceGroup" -l "EastUS"
```

## <a name="set-up-your-key-vault"></a>Nastavení trezoru klíčů

Nyní vytvoříte Trezor klíčů a umístíte do něj tajný kód pro pozdější použití v tomto kurzu.

Pokud chcete vytvořit Trezor klíčů, použijte příkaz [AZ Key trezor Create](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-create) :

> [!Important]
> Každý Trezor klíčů musí mít jedinečný název. V následujících příkladech nahraďte <název vašeho trezoru klíčů> názvem vašeho trezoru klíčů.

```azurecli-interactive
az keyvault create --name "<your-keyvault-name>" -g "myResourceGroup"
```

Poznamenejte si vrácenou hodnotu `vaultUri` , která bude ve formátu "https:// &lt; Your-Vault.Azure.NET/trezor-name &gt; .". Bude použit v kroku [aktualizace kódu](#update-the-code) .

[!INCLUDE [Create a secret](../../../includes/key-vault-create-secret.md)]

## <a name="create-a-net-web-app"></a>Vytvoření webové aplikace .NET

### <a name="create-a-local-app"></a>Vytvoření místní aplikace

V okně terminálu na svém počítači vytvořte adresář `akvwebapp` a přejděte do něj.

```bash
mkdir akvwebapp
cd akvwebapp
```

Nyní vytvořte novou aplikaci .NET Core pomocí příkazu [dotnet New Web](/dotnet/core/tools/dotnet-new) :

```bash
dotnet new web
```

Spusťte aplikaci místně, abyste viděli, jak by měla vypadat po nasazení do Azure. 

```bash
dotnet run
```

Otevřete webový prohlížeč a přejděte do aplikace na adrese `http://localhost:5000`.

Ze vzorové aplikace zobrazené na stránce se zobrazí zpráva **Hello World** .

### <a name="initialize-the-git-repository"></a>Inicializovat úložiště Git

V okně terminálu ukončete webový server stisknutím **Ctrl + C**.  Inicializujte úložiště Git pro projekt .NET Core.

```bash
git init
git add .
git commit -m "first commit"
```

### <a name="configure-a-deployment-user"></a>Konfigurace uživatele nasazení

FTP a místní Git se můžou nasadit do webové aplikace Azure pomocí *uživatele nasazení*. Jakmile nakonfigurujete uživatele nasazení, můžete ho použít pro všechna nasazení Azure. Uživatelské jméno a heslo nasazení na úrovni účtu se liší od přihlašovacích údajů předplatného Azure. 

Pokud chcete nakonfigurovat uživatele nasazení, spusťte příkaz [AZ WebApp Deployment User set](/cli/azure/webapp/deployment/user?view=azure-cli-latest#az-webapp-deployment-user-set) . Vyberte uživatelské jméno a heslo, které dodržuje tyto pokyny: 

- Uživatelské jméno musí být v rámci Azure jedinečné a pro místní nabízená oznámení Git nesmí obsahovat symbol @. 
- Heslo musí mít délku alespoň osm znaků a dva z následujících tří prvků: písmena, číslice a symboly. 

```azurecli-interactive
az webapp deployment user set --user-name "<username>" --password "<password>"
```

Výstup JSON zobrazuje heslo jako `null` . Pokud se zobrazí chyba `'Conflict'. Details: 409`, změňte uživatelské jméno. Pokud se zobrazí chyba `'Bad Request'. Details: 400`, použijte silnější heslo. 

Poznamenejte si uživatelské jméno a heslo, které chcete použít k nasazení webových aplikací.

### <a name="create-an-app-service-plan"></a>Vytvoření plánu služby App Service

Pomocí příkazu Azure CLI [AZ AppService Plan Create](/cli/azure/appservice/plan?view=azure-cli-latest) vytvořte plán App Service. Následující příklad vytvoří plán App Service s názvem `myAppServicePlan` v **bezplatné** cenové úrovni:

```azurecli-interactive
az appservice plan create --name myAppServicePlan --resource-group myResourceGroup --sku FREE
```

Po vytvoření plánu služby App Service se v rozhraní příkazového řádku Azure zobrazí podobné informace jako v následujícím příkladu:

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


### <a name="create-a-remote-web-app"></a>Vytvoření vzdálené webové aplikace

Vytvořte [webovou aplikaci Azure](../../app-service/overview.md#app-service-on-linux) v `myAppServicePlan` plánu App Service. 

> [!Important]
> Podobně jako u Key Vault musí mít webová aplikace Azure jedinečný název. \<your-webapp-name\>V následujících příkladech nahraďte názvem vaší webové aplikace.


```azurecli-interactive
az webapp create --resource-group "myResourceGroup" --plan "myAppServicePlan" --name "<your-webapp-name>" --deployment-local-git
```

Po vytvoření webové aplikace Azure CLI zobrazí výstup podobný následujícímu příkladu:

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


Adresa URL vzdáleného úložiště Git se zobrazuje ve vlastnosti `deploymentLocalGitUrl` ve formátu `https://<username>@<your-webapp-name>.scm.azurewebsites.net/<your-webapp-name>.git`. Tuto adresu URL uložte, jak ji budete potřebovat později.

Přejděte k nově vytvořené aplikaci. Název vaší aplikace nahraďte parametrem _ &lt; -WebApp-Name>_ .

```bash
https://<your-webapp-name>.azurewebsites.net
```

Zobrazí se výchozí webová stránka pro nově vytvořenou webovou aplikaci Azure.

### <a name="deploy-your-local-app"></a>Nasazení místní aplikace

Zpátky v místním okně terminálu přidejte vzdálené úložiště Azure do místního úložiště Git a nahraďte *\<deploymentLocalGitUrl-from-create-step>* adresu URL vzdáleného úložiště Git, kterou jste uložili v kroku [vytvoření vzdálené webové aplikace](#create-a-remote-web-app) .

```bash
git remote add azure <deploymentLocalGitUrl-from-create-step>
```

Nasaďte aplikaci do vzdáleného úložiště Azure pomocí následujícího příkazu. Když vám správce přihlašovacích údajů Git vyzve k zadání přihlašovacích údajů, použijte přihlašovací údaje, které jste vytvořili v kroku [Konfigurace nasazení uživatele](#configure-a-deployment-user) .

```bash
git push azure master
```

Spuštění tohoto příkazu může trvat několik minut. Při spuštění příkaz zobrazí podobné informace jako v následujícím příkladu:
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

Pomocí webového prohlížeče vyhledejte (nebo aktualizujte) nasazenou aplikaci.

```bash
http://<your-webapp-name>.azurewebsites.net
```

Zobrazí se zpráva "Hello World!". zpráva, kterou jste dříve viděli při návštěvě `http://localhost:5000` .

## <a name="create-and-assign-a-managed-identity"></a>Vytvoření a přiřazení spravované identity

V Azure CLI pro vytvoření identity pro tuto aplikaci spusťte příkaz [AZ WebApp-identity Assign](/cli/azure/webapp/identity?view=azure-cli-latest#az-webapp-identity-assign) :

```azurecli-interactive
az webapp identity assign --name "<your-webapp-name>" --resource-group "myResourceGroup"
```

Tato operace vrátí tento fragment kódu JSON:

```json
{
  "principalId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "tenantId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "type": "SystemAssigned"
}
```

Pokud chcete vaší webové aplikaci udělit oprávnění k **získání** a **výpisu** operací v trezoru klíčů, předejte principalID do Azure CLI [AZ Key trezor set-Policy](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-set-policy) Command:

```azurecli-interactive
az keyvault set-policy --name "<your-keyvault-name>" --object-id "<principalId>" --secret-permissions get list
```


## <a name="modify-the-app-to-access-your-key-vault"></a>Úprava aplikace pro přístup k trezoru klíčů

### <a name="install-the-packages"></a>Nainstalovat balíčky

V okně terminálu nainstalujte Azure Key Vault klientskou knihovnu pro balíčky .NET:

```console
dotnet add package Azure.Identity
dotnet add package Azure.Security.KeyVault.Secrets
```

### <a name="update-the-code"></a>Aktualizace kódu

Vyhledejte a otevřete soubor Startup.cs v projektu akvwebapp. 

Přidejte tyto dva řádky do záhlaví:

```csharp
using Azure.Identity;
using Azure.Security.KeyVault.Secrets;
using Azure.Core;
```

Přidejte tyto řádky před `app.UseEndpoints` voláním, aktualizujte identifikátor URI tak, aby odrážela `vaultUri` váš Trezor klíčů. Pod kódem používá  [DefaultAzureCredential ()](/dotnet/api/azure.identity.defaultazurecredential?view=azure-dotnet) pro ověřování do trezoru klíčů, který používá token ze spravované identity aplikace k ověření. Používá se také exponenciální omezení rychlosti pro opakované pokusy v případě omezení trezoru klíčů.

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

KeyVaultSecret secret = client.GetSecret("mySecret");

string secretValue = secret.Value;
```

Aktualizujte řádek `await context.Response.WriteAsync("Hello World!");` , který se má přečíst:

```csharp
await context.Response.WriteAsync(secretValue);
```

Před pokračováním k dalšímu kroku si nezapomeňte změny uložit.

### <a name="redeploy-your-web-app"></a>Opětovné nasazení webové aplikace

Aktualizovaný kód můžete znovu nasadit do Azure pomocí následujících příkazů git:

```bash
git add .
git commit -m "Updated web app to access my key vault"
git push azure master
```

## <a name="visit-your-completed-web-app"></a>Navštívit dokončenou webovou aplikaci

```bash
http://<your-webapp-name>.azurewebsites.net
```

Místo, kde jste viděli **Hello World**, by se teď měla zobrazit hodnota zobrazeného tajného kódu: **úspěch!**

## <a name="next-steps"></a>Další kroky

- Další informace o [spravovaných identitách pro prostředky Azure](../../active-directory/managed-identities-azure-resources/overview.md)
- Další informace o [spravovaných identitách pro App Service](../../app-service/overview-managed-identity.md?tabs=dotnet)
- Viz reference k rozhraní [.NET API klientské knihovny Azure Key Vault](/dotnet/api/overview/azure/key-vault?view=azure-dotnet) .
- Viz [Klientská knihovna Azure Key Vault pro zdrojový kód .NET](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/keyvault) .
- Podívejte se na [balíček klientské knihovny NuGet Azure Key Vault v4 pro .NET](https://www.nuget.org/packages/Azure.Security.KeyVault.Secrets/) .


