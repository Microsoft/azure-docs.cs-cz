---
title: Spravované identity ve službě App Service a Azure Functions | Dokumentace Microsoftu
description: Koncepční odkaz a Instalační příručka pro spravované identity v Azure App Service a Azure Functions
services: app-service
author: mattchenderson
manager: cfowler
editor: ''
ms.service: app-service
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 06/25/2018
ms.author: mahender
ms.openlocfilehash: 5d058059f523d3567817cad8ac11e837fb4a0a49
ms.sourcegitcommit: c29d7ef9065f960c3079660b139dd6a8348576ce
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/12/2018
ms.locfileid: "44714240"
---
# <a name="how-to-use-managed-identities-for-app-service-and-azure-functions"></a>Použití spravované identity pro App Service a Azure Functions

> [!NOTE] 
> App Service v Linuxu a Web App for Containers spravovaných identit momentálně nepodporují.

> [!Important] 
> Spravované identity pro App Service a Azure Functions nebude chovat dle očekávání, pokud je vaše aplikace migrovat předplatných a tenantů. Aplikace bude muset získat novou identitu, která se dá dělat pomocí zakázat a znovu povolit funkci. Zobrazit [odebrání identitu](#remove) níže. Podřízené prostředky se také musí být aktualizované, aby používaly novou identitu zásady přístupu.

V tomto tématu se dozvíte, jak vytvořit spravovanou identitu pro aplikace služby App Service a Azure Functions a jak ji používat pro přístup k dalším prostředkům. Spravovanou identitu ze služby Azure Active Directory umožňuje aplikaci snadno přistupovat k jiné prostředky s ochranou AAD jako je Azure Key Vault. Identita je spravovaná Platforma Azure a není nutné zřizovat nebo otočit jakýchkoli tajných kódů. Další informace o spravovaných identit ve službě AAD najdete v tématu [spravovaných identit pro prostředky Azure](../active-directory/managed-identities-azure-resources/overview.md).

## <a name="creating-an-app-with-an-identity"></a>Vytvoření aplikace s identitou

Vytvoření aplikace s identitou vyžaduje další vlastnosti nastavit na aplikaci.

### <a name="using-the-azure-portal"></a>Použití webu Azure Portal

Nastavení spravovaných identit na portálu, nejprve vytvoříte aplikaci jako za normálních okolností a pak povolte funkci.

1. Vytvoření aplikace na portálu jako obvykle. Přejděte na ni na portálu.

2. Pokud používáte aplikaci function app, přejděte na **funkce platformy**. Pro další typy aplikací, přejděte dolů k položce **nastavení** skupinu v levém navigačním panelu.

3. Vyberte **se identita spravované**.

4. Přepínač **registraci do Azure Active Directory** k **na**. Klikněte na **Uložit**.

![Spravovaná identita ve službě App Service](media/app-service-managed-service-identity/msi-blade.png)

### <a name="using-the-azure-cli"></a>Použití Azure CLI

Pokud chcete nastavit spravovanou identitu pomocí Azure CLI, budete muset použít `az webapp identity assign` příkaz s existující aplikaci. Existují tři možnosti pro spuštění příkladů v této části:

- Použití [Azure Cloud Shell](../cloud-shell/overview.md) z portálu Azure portal.
- Použijte vložené Azure Cloud Shell pomocí "Vyzkoušet" tlačítka, nachází v pravém horním rohu každý blok kódu níže.
- [Nainstalujte nejnovější verzi 2.0 rozhraní příkazového řádku](https://docs.microsoft.com/cli/azure/install-azure-cli) (2.0.31 nebo novější) Pokud byste radši chtěli použít místní konzoly příkazového řádku. 

Následující postup vás provede procesem vytvoření webové aplikace a její přiřazení identitu pomocí rozhraní příkazového řádku:

1. Pokud používáte Azure CLI v místní konzole, nejprve se přihlaste k Azure pomocí příkazu [az login](/cli/azure/reference-index#az-login). Použijte účet, který je přidružený k předplatnému Azure, pod kterým chcete aplikaci nasadit:

    ```azurecli-interactive
    az login
    ```
2. Vytvoření webové aplikace s využitím rozhraní příkazového řádku. Další příklady použití rozhraní příkazového řádku pomocí služby App Service najdete v tématu [ukázky rozhraní příkazového řádku Service aplikaci](../app-service/app-service-cli-samples.md):

    ```azurecli-interactive
    az group create --name myResourceGroup --location westus
    az appservice plan create --name myPlan --resource-group myResourceGroup --sku S1
    az webapp create --name myApp --resource-group myResourceGroup --plan myPlan
    ```

3. Spustit `identity assign` příkaz pro vytvoření identity pro tuto aplikaci:

    ```azurecli-interactive
    az webapp identity assign --name myApp --resource-group myResourceGroup
    ```

### <a name="using-azure-powershell"></a>Použití Azure Powershell

Následující postup vás provede procesem vytvoření webové aplikace a její přiřazení identitu pomocí Azure Powershellu:

1. V případě potřeby nainstalujte Azure PowerShell podle pokynů uvedených v [příručce k Azure PowerShellu](/powershell/azure/overview) a pak spuštěním rutiny `Login-AzureRmAccount` vytvořte připojení k Azure.

2. Vytvoření webové aplikace s využitím Azure Powershellu. Další příklady toho, jak pomocí služby App Service pomocí Azure Powershellu najdete v tématu [ukázky Powershellu pro App Service](../app-service/app-service-powershell-samples.md):

    ```azurepowershell-interactive
    # Create a resource group.
    New-AzureRmResourceGroup -Name myResourceGroup -Location $location
    
    # Create an App Service plan in Free tier.
    New-AzureRmAppServicePlan -Name $webappname -Location $location -ResourceGroupName myResourceGroup -Tier Free
    
    # Create a web app.
    New-AzureRmWebApp -Name $webappname -Location $location -AppServicePlan $webappname -ResourceGroupName myResourceGroup
    ```

3. Spustit `identity assign` příkaz pro vytvoření identity pro tuto aplikaci:

    ```azurepowershell-interactive
    Set-AzureRmWebApp -AssignIdentity $true -Name $webappname -ResourceGroupName myResourceGroup 
    ```

### <a name="using-an-azure-resource-manager-template"></a>Pomocí šablony Azure Resource Manageru

Šablony Azure Resource Manageru můžete použít k automatizaci nasazení vašich prostředků Azure. Další informace o nasazení do App Service a Functions najdete v tématu [automatizace nasazování prostředků ve službě App Service](../app-service/app-service-deploy-complex-application-predictably.md) a [automatizace nasazování prostředků ve službě Azure Functions](../azure-functions/functions-infrastructure-as-code.md).

Prostředek typu `Microsoft.Web/sites` mohou vytvořit s identitou, včetně následující vlastnost v definici prostředků:
```json
"identity": {
    "type": "SystemAssigned"
}    
```

To říká Azure můžete vytvářet a spravovat identitu pro vaši aplikaci.

Například webové aplikace, může vypadat takto:
```json
{
    "apiVersion": "2016-08-01",
    "type": "Microsoft.Web/sites",
    "name": "[variables('appName')]",
    "location": "[resourceGroup().location]",
    "identity": {
        "type": "SystemAssigned"
    },
    "properties": {
        "name": "[variables('appName')]",
        "serverFarmId": "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]",
        "hostingEnvironment": "",
        "clientAffinityEnabled": false,
        "alwaysOn": true
    },
    "dependsOn": [
        "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]"
    ]
}
```

Při vytvoření webu má tyto další vlastnosti:
```json
"identity": {
    "tenantId": "<TENANTID>",
    "principalId": "<PRINCIPALID>"
}
```

Kde `<TENANTID>` a `<PRINCIPALID>` jsou nahrazeny identifikátory GUID. Identifikuje vlastnost tenantId jaké identity patří do tenanta služby AAD. PrincipalId je jedinečný identifikátor pro novou identitu aplikace. V rámci AAD služby, které má stejný název, který jste přiřadili k vaší instanci služby App Service nebo Azure Functions.

## <a name="obtaining-tokens-for-azure-resources"></a>Získání tokenů pro prostředky Azure

Aplikaci můžete získat tokeny pro jiné prostředky chráněné službou AAD, jako je Azure Key Vault svoji identitu. Tyto tokeny reprezentujícího aplikaci přístup k prostředku a ne všechny konkrétní uživatelské aplikace. 

> [!IMPORTANT]
> Budete muset nakonfigurovat cílový prostředek, pokud chcete povolit přístup z vaší aplikace. Například pokud jste požádali o token do služby Key Vault, budete muset Ujistěte se, že jste přidali zásady přístupu, která obsahuje identitu vaší aplikace. Jinak vaše volání do služby Key Vault odmítne, i v případě, že zahrnují token. Další informace o tom, které prostředky podporují tokeny služby Azure Active Directory najdete v tématu [služby Azure, že podpora Azure AD ověřování](../active-directory/managed-identities-azure-resources/services-support-msi.md#azure-services-that-support-azure-ad-authentication).

Neexistuje jednoduchý protokolu REST pro získání tokenu v App Service a Azure Functions. Pro aplikace .NET knihovnu Microsoft.Azure.Services.appauthentication přistupovat přes tento protokol poskytuje abstrakci a podporuje místní vývojové prostředí.

### <a name="asal"></a>Pomocí knihovnu Microsoft.Azure.Services.appauthentication přistupovat pro .NET

Pro aplikace .NET a funkce je nejjednodušší způsob, jak pracovat s spravovanou identitu prostřednictvím balíčku Microsoft.Azure.Services.appauthentication přistupovat. Tato knihovna také umožňuje testovat kód místně na svém vývojovém počítači, pomocí uživatelského účtu z aplikace Visual Studio [příkazového řádku Azure CLI 2.0](https://docs.microsoft.com/cli/azure?view=azure-cli-latest), nebo integrované ověřování Active Directory. Další informace o možnostech místní vývoj pomocí této knihovny, najdete v článku [Odkaz na Microsoft.Azure.Services.appauthentication přistupovat]. V této části se dozvíte, jak začít pracovat s knihovnou ve vašem kódu.

1. Přidat odkazy [Microsoft.Azure.Services.appauthentication přistupovat](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) a [Microsoft.Azure.KeyVault](https://www.nuget.org/packages/Microsoft.Azure.KeyVault) balíčky NuGet pro vaši aplikaci.

2.  Přidejte následující kód do vaší aplikace:

```csharp
using Microsoft.Azure.Services.AppAuthentication;
using Microsoft.Azure.KeyVault;
// ...
var azureServiceTokenProvider = new AzureServiceTokenProvider();
string accessToken = await azureServiceTokenProvider.GetAccessTokenAsync("https://vault.azure.net");
// OR
var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(azureServiceTokenProvider.KeyVaultTokenCallback));
```

Další informace o Microsoft.Azure.Services.appauthentication přistupovat a zpřístupňuje operací, najdete v článku [Odkaz na Microsoft.Azure.Services.appauthentication přistupovat] a [služby App Service a trezor klíčů s využitím MSI .NET Ukázka](https://github.com/Azure-Samples/app-service-msi-keyvault-dotnet).

### <a name="using-the-rest-protocol"></a>Pomocí protokolu REST

Aplikace s využitím spravované identity má dvě proměnné prostředí definované:
- MSI_ENDPOINT
- MSI_SECRET

**MSI_ENDPOINT** místní adresu URL, ze kterého můžete aplikaci požádat o tokeny. Získá token pro určitý prostředek, ujistěte se, požadavek HTTP GET na tento koncový bod, včetně následujících parametrů:

> [!div class="mx-tdBreakAll"]
> |Název parametru|V|Popis|
> |-----|-----|-----|
> |prostředek|Dotaz|AAD identifikátor URI prostředku, pro která by měla být získána token.|
> |verze API-version|Dotaz|Verze rozhraní API tokenů, který se má použít. "2017-09-01" je momentálně podporována pouze verze.|
> |Tajný kód|Záhlaví|Hodnota proměnné prostředí MSI_SECRET.|


Úspěšná odpověď 200 OK obsahuje text JSON s následujícími vlastnostmi:

> [!div class="mx-tdBreakAll"]
> |Název vlastnosti|Popis|
> |-------------|----------|
> |access_token|Požadovaný přístupový token. Volání webové služby můžete použít tento token k ověření přijímající webové služby.|
> |expires_on|Čas, kdy vyprší platnost přístupového tokenu. Datum je vyjádřena jako počet sekund od 1970-01-01T0:0:0Z UTC až do okamžiku vypršení platnosti. Tato hodnota se používá k určení doby života tokenů v mezipaměti.|
> |prostředek|Identifikátor URI ID aplikace přijímající webové služby.|
> |token_type|Určuje hodnotu pro typ tokenu. Jediný typ, který podporuje Azure AD je nosiče. Další informace o nosných tokenů najdete v tématu [rozhraní Framework autorizace OAuth 2.0: použití nosného tokenu (RFC 6750)](http://www.rfc-editor.org/rfc/rfc6750.txt).|


Tato odpověď je stejné jako [odpověď pro požadavek tokenu přístupu do služby AAD](../active-directory/develop/v1-oauth2-client-creds-grant-flow.md#service-to-service-access-token-response).

> [!NOTE] 
> Proměnné prostředí jsou nastavené při prvním spuštění procesu, tak po povolení spravovanou identitu pro vaši aplikaci, budete muset restartovat aplikaci nebo znovu její kód před `MSI_ENDPOINT` a `MSI_SECRET` jsou k dispozici pro váš kód.

### <a name="rest-protocol-examples"></a>Příklady protokolu REST
Příklad žádosti může vypadat nějak takto:
```
GET /MSI/token?resource=https://vault.azure.net&api-version=2017-09-01 HTTP/1.1
Host: localhost:4141
Secret: 853b9a84-5bfa-4b22-a3f3-0b9a43d9ad8a
```
A ukázkové odpovědi může vypadat nějak takto:
```
HTTP/1.1 200 OK
Content-Type: application/json
 
{
    "access_token": "eyJ0eXAi…",
    "expires_on": "09/14/2017 00:00:00 PM +00:00",
    "resource": "https://vault.azure.net",
    "token_type": "Bearer"
}
```

### <a name="code-examples"></a>Příklady kódu
<a name="token-csharp"></a>K této žádosti v jazyce C#:
```csharp
public static async Task<HttpResponseMessage> GetToken(string resource, string apiversion)  {
    HttpClient client = new HttpClient();
    client.DefaultRequestHeaders.Add("Secret", Environment.GetEnvironmentVariable("MSI_SECRET"));
    return await client.GetAsync(String.Format("{0}/?resource={1}&api-version={2}", Environment.GetEnvironmentVariable("MSI_ENDPOINT"), resource, apiversion));
}
```
> [!TIP]
> Pro jazyky .NET, můžete také použít [Microsoft.Azure.Services.appauthentication přistupovat](#asal) místo vytváření to požádat o sobě.

<a name="token-js"></a>V Node.JS:
```javascript
const rp = require('request-promise');
const getToken = function(resource, apiver, cb) {
    var options = {
        uri: `${process.env["MSI_ENDPOINT"]}/?resource=${resource}&api-version=${apiver}`,
        headers: {
            'Secret': process.env["MSI_SECRET"]
        }
    };
    rp(options)
        .then(cb);
}
```

<a name="token-powershell"></a>V prostředí PowerShell:
```powershell
$apiVersion = "2017-09-01"
$resourceURI = "https://<AAD-resource-URI-for-resource-to-obtain-token>"
$tokenAuthURI = $env:MSI_ENDPOINT + "?resource=$resourceURI&api-version=$apiVersion"
$tokenResponse = Invoke-RestMethod -Method Get -Headers @{"Secret"="$env:MSI_SECRET"} -Uri $tokenAuthURI
$accessToken = $tokenResponse.access_token
```

## <a name="remove"></a>Odebírá se identita

Identita je možné odebrat tím, že zakážete funkci stejným způsobem, pokud byl vytvořený pomocí portálu, Powershellu nebo rozhraní příkazového řádku. V šabloně protokolu REST/ARM se provádí tak, že nastavíte typ, který má "None":

```json
"identity": {
    "type": "None"
}    
```

Odebrání identitu tímto způsobem se odstraní také objekt zabezpečení ze služby AAD. Systém uživatelsky přiřazené identity jsou automaticky odebrány ze služby AAD při odstraňování prostředků aplikace.

> [!NOTE] 
> Je také nastavení aplikace, kterou můžete nastavit, WEBSITE_DISABLE_MSI, tím se zakáže pouze místní služby tokenů. Ale ponechá identity na místě a nástrojů se stále zobrazí spravovanou identitu jako "on" nebo "povoleno". Použití tohoto nastavení v důsledku toho není doporučená.

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Přístup k SQL Database pomocí spravované identity](app-service-web-tutorial-connect-msi.md)

[Odkaz na Microsoft.Azure.Services.appauthentication přistupovat]: https://go.microsoft.com/fwlink/p/?linkid=862452
