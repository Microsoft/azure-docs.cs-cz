---
title: Spravované identity
description: Přečtěte si, jak spravované identity fungují v Azure App Service a Azure Functions, jak nakonfigurovat spravovanou identitu a vygenerovat token pro prostředek back-end.
author: mattchenderson
ms.topic: article
ms.date: 10/30/2019
ms.author: mahender
ms.reviewer: yevbronsh
ms.openlocfilehash: f341f5bbf7221664301ca53eea1edd6af7544950
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/25/2019
ms.locfileid: "75422015"
---
# <a name="how-to-use-managed-identities-for-app-service-and-azure-functions"></a>Použití spravovaných identit pro App Service a Azure Functions

> [!Important] 
> Spravované identity pro App Service a Azure Functions se nebudou chovat podle očekávání, pokud se vaše aplikace migruje v rámci předplatných nebo tenantů. Aplikace bude muset získat novou identitu, kterou je možné provést zakázáním a opakovaným povolením této funkce. Viz [Odebrání identity](#remove) níže. U podřízených prostředků bude také potřeba mít aktualizované zásady přístupu, aby používaly novou identitu.

V tomto tématu se dozvíte, jak vytvořit spravovanou identitu pro App Service a Azure Functions aplikace a jak ji použít pro přístup k dalším prostředkům. Spravovaná identita z Azure Active Directory umožňuje vaší aplikaci snadný přístup k ostatním prostředkům chráněným pomocí AAD, jako je Azure Key Vault. Identita je spravovaná platformou Azure a nevyžaduje zřízení ani otočení jakýchkoli tajných klíčů. Další informace o spravovaných identitách v AAD najdete v tématu [spravované identity pro prostředky Azure](../active-directory/managed-identities-azure-resources/overview.md).

Aplikaci lze udělit dva typy identit: 
- **Identita přiřazená systémem** je svázána s vaší aplikací a je odstraněna, pokud je vaše aplikace odstraněna. Aplikace může mít jenom jednu identitu přiřazenou systémem.
- **Identita přiřazená uživatelem** je samostatný prostředek Azure, který se dá přiřadit k vaší aplikaci. Aplikace může mít více uživatelsky přiřazených identit.

## <a name="adding-a-system-assigned-identity"></a>Přidání identity přiřazené systémem

Vytvoření aplikace s identitou přiřazenou systémem vyžaduje pro aplikaci nastavenou další vlastnost.

### <a name="using-the-azure-portal"></a>Použití webu Azure Portal

Pokud chcete na portálu nastavit spravovanou identitu, nejdřív vytvořte aplikaci jako normální a pak tuto funkci povolte.

1. Vytvořte aplikaci na portálu obvyklým způsobem. Přejděte na portál na portálu.

2. Pokud používáte aplikaci Function App, přejděte k **funkcím platformy**. U ostatních typů aplikací se posuňte dolů ke skupině **Nastavení** v levém navigačním panelu.

3. Vyberte **Identita**.

4. V rámci karty **přiřazené systémem** přepněte **stav** na **zapnuto**. Klikněte na možnost **Uložit**.

    ![Spravovaná identita v App Service](media/app-service-managed-service-identity/system-assigned-managed-identity-in-azure-portal.png)

### <a name="using-the-azure-cli"></a>Použití Azure CLI

K nastavení spravované identity pomocí Azure CLI budete muset použít příkaz `az webapp identity assign` pro existující aplikaci. Máte tři možnosti, jak spustit příklady v této části:

- Použijte [Azure Cloud Shell](../cloud-shell/overview.md) z Azure Portal.
- Použijte vložený Azure Cloud Shell pomocí tlačítka "vyzkoušet", které je umístěné v pravém horním rohu každého bloku kódu níže.
- Pokud upřednostňujete použití místní konzoly CLI, [nainstalujte nejnovější verzi Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) (2.0.31 nebo novější). 

Následující kroky vás provedou vytvořením webové aplikace a přiřazením identity pomocí rozhraní příkazového řádku:

1. Pokud používáte Azure CLI v místní konzole, nejprve se přihlaste k Azure pomocí příkazu [az login](/cli/azure/reference-index#az-login). Použijte účet, který je přidružený k předplatnému Azure, pod kterým chcete nasadit aplikaci:

    ```azurecli-interactive
    az login
    ```
2. Vytvořte webovou aplikaci pomocí rozhraní příkazového řádku. Další příklady použití rozhraní příkazového řádku s App Service najdete v tématu [ukázky App Service CLI](../app-service/samples-cli.md):

    ```azurecli-interactive
    az group create --name myResourceGroup --location westus
    az appservice plan create --name myPlan --resource-group myResourceGroup --sku S1
    az webapp create --name myApp --resource-group myResourceGroup --plan myPlan
    ```

3. Pro vytvoření identity pro tuto aplikaci spusťte příkaz `identity assign`:

    ```azurecli-interactive
    az webapp identity assign --name myApp --resource-group myResourceGroup
    ```

### <a name="using-azure-powershell"></a>Použití Azure Powershell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Následující kroky vás provedou vytvořením webové aplikace a přiřazením identity pomocí Azure PowerShell:

1. V případě potřeby nainstalujte Azure PowerShell podle pokynů uvedených v [příručce k Azure PowerShellu](/powershell/azure/overview) a pak spuštěním rutiny `Login-AzAccount` vytvořte připojení k Azure.

2. Vytvořte webovou aplikaci pomocí Azure PowerShell. Další příklady použití Azure PowerShell s App Service najdete v tématu [App Service ukázek PowerShellu](../app-service/samples-powershell.md):

    ```azurepowershell-interactive
    # Create a resource group.
    New-AzResourceGroup -Name myResourceGroup -Location $location
    
    # Create an App Service plan in Free tier.
    New-AzAppServicePlan -Name $webappname -Location $location -ResourceGroupName myResourceGroup -Tier Free
    
    # Create a web app.
    New-AzWebApp -Name $webappname -Location $location -AppServicePlan $webappname -ResourceGroupName myResourceGroup
    ```

3. Pro vytvoření identity pro tuto aplikaci spusťte příkaz `Set-AzWebApp -AssignIdentity`:

    ```azurepowershell-interactive
    Set-AzWebApp -AssignIdentity $true -Name $webappname -ResourceGroupName myResourceGroup 
    ```

### <a name="using-an-azure-resource-manager-template"></a>Použití šablony Azure Resource Manager

K automatizaci nasazení prostředků Azure můžete použít šablonu Azure Resource Manager. Další informace o nasazení do App Service a funkcí naleznete v tématu [Automatizace nasazení prostředků v App Service](../app-service/deploy-complex-application-predictably.md) a [Automatizace nasazení prostředků v Azure Functions](../azure-functions/functions-infrastructure-as-code.md).

Libovolný prostředek typu `Microsoft.Web/sites` lze vytvořit s identitou, a to zahrnutím následující vlastnosti do definice prostředků:
```json
"identity": {
    "type": "SystemAssigned"
}    
```

> [!NOTE] 
> Aplikace může mít současně přiřazené i uživatelsky přiřazené identity. V tomto případě by byla vlastnost `type` `SystemAssigned,UserAssigned`

Když přidáte typ přiřazený systémem, dáte službě Azure pokyn k vytvoření a správě identity vaší aplikace.

Webová aplikace může například vypadat takto:
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

Když je web vytvořen, má následující další vlastnosti:
```json
"identity": {
    "type": "SystemAssigned",
    "tenantId": "<TENANTID>",
    "principalId": "<PRINCIPALID>"
}
```

Kde `<TENANTID>` a `<PRINCIPALID>` nahrazují identifikátory GUID. Vlastnost tenantId určuje, ke kterému tenantovi AAD patří identita. PrincipalId je jedinečný identifikátor pro novou identitu aplikace. V rámci služby AAD má instanční objekt stejný název, jaký jste zadali App Service nebo Azure Functions instanci.


## <a name="adding-a-user-assigned-identity"></a>Přidání uživatelsky přiřazené identity

Vytvoření aplikace s uživatelem přiřazenou identitou vyžaduje, abyste vytvořili identitu a pak přidali svůj identifikátor prostředku do vaší konfigurace aplikace.

### <a name="using-the-azure-portal"></a>Použití webu Azure Portal

Nejdřív budete muset vytvořit prostředek identity přiřazené uživatelem.

1. Podle [těchto pokynů](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md#create-a-user-assigned-managed-identity)vytvořte prostředek spravované identity přiřazené uživatelem.

2. Vytvořte aplikaci na portálu obvyklým způsobem. Přejděte na portál na portálu.

3. Pokud používáte aplikaci Function App, přejděte k **funkcím platformy**. U ostatních typů aplikací se posuňte dolů ke skupině **Nastavení** v levém navigačním panelu.

4. Vyberte **Identita**.

5. Na kartě **přiřazené uživatelem** klikněte na tlačítko **Přidat**.

6. Vyhledejte identitu, kterou jste vytvořili dříve, a vyberte ji. Klikněte na tlačítko **Add** (Přidat).

    ![Spravovaná identita v App Service](media/app-service-managed-service-identity/user-assigned-managed-identity-in-azure-portal.png)

### <a name="using-an-azure-resource-manager-template"></a>Použití šablony Azure Resource Manager

K automatizaci nasazení prostředků Azure můžete použít šablonu Azure Resource Manager. Další informace o nasazení do App Service a funkcí naleznete v tématu [Automatizace nasazení prostředků v App Service](../app-service/deploy-complex-application-predictably.md) a [Automatizace nasazení prostředků v Azure Functions](../azure-functions/functions-infrastructure-as-code.md).

Libovolný prostředek typu `Microsoft.Web/sites` lze vytvořit s identitou, a to zahrnutím následujícího bloku do definice prostředků a nahrazením `<RESOURCEID>` ID prostředku požadované identity:
```json
"identity": {
    "type": "UserAssigned",
    "userAssignedIdentities": {
        "<RESOURCEID>": {}
    }
}    
```

> [!NOTE] 
> Aplikace může mít současně přiřazené i uživatelsky přiřazené identity. V tomto případě by byla vlastnost `type` `SystemAssigned,UserAssigned`

Když se přidá uživatelem přiřazený typ, sdělí Azure, aby používal identitu přiřazenou uživateli zadanou pro vaši aplikaci.

Webová aplikace může například vypadat takto:
```json
{
    "apiVersion": "2016-08-01",
    "type": "Microsoft.Web/sites",
    "name": "[variables('appName')]",
    "location": "[resourceGroup().location]",
    "identity": {
        "type": "UserAssigned",
        "userAssignedIdentities": {
            "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities', variables('identityName'))]": {}
        }
    },
    "properties": {
        "name": "[variables('appName')]",
        "serverFarmId": "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]",
        "hostingEnvironment": "",
        "clientAffinityEnabled": false,
        "alwaysOn": true
    },
    "dependsOn": [
        "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]",
        "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities', variables('identityName'))]"
    ]
}
```

Když je web vytvořen, má následující další vlastnosti:
```json
"identity": {
    "type": "UserAssigned",
    "userAssignedIdentities": {
        "<RESOURCEID>": {
            "principalId": "<PRINCIPALID>",
            "clientId": "<CLIENTID>"
        }
    }
}
```

Kde `<PRINCIPALID>` a `<CLIENTID>` nahrazují identifikátory GUID. PrincipalId je jedinečný identifikátor pro identitu, která se používá pro správu AAD. ClientId je jedinečný identifikátor nové identity aplikace, který se používá k určení identity, která se má použít při volání za běhu.


## <a name="obtaining-tokens-for-azure-resources"></a>Získání tokenů pro prostředky Azure

Aplikace může pomocí své identity získat tokeny jiným prostředkům chráněným službou AAD, například Azure Key Vault. Tyto tokeny reprezentují aplikaci, která přistupuje k prostředku, a ne žádného konkrétního uživatele aplikace. 

> [!IMPORTANT]
> Možná budete muset nakonfigurovat cílový prostředek, aby povoloval přístup z vaší aplikace. Pokud například požadujete, aby byl token Key Vault, musíte se ujistit, že jste přidali zásadu přístupu, která zahrnuje identitu vaší aplikace. V opačném případě budou volání Key Vault odmítnuta, a to i v případě, že obsahují token. Další informace o tom, které prostředky podporují Azure Active Directory tokeny, najdete v tématu [služby Azure, které podporují ověřování Azure AD](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication).

K získání tokenu v App Service a Azure Functions existuje jednoduchý protokol REST. Tato možnost se dá použít pro všechny aplikace a jazyky. V případě některých rozhraní .NET a Java poskytuje sada Azure SDK abstrakci prostřednictvím tohoto protokolu a usnadňuje místní vývojové prostředí.

### <a name="using-the-rest-protocol"></a>Použití protokolu REST

Aplikace se spravovanou identitou má definované dvě proměnné prostředí:

- MSI_ENDPOINT – adresa URL místní služby tokenu.
- MSI_SECRET – hlavička sloužící k omezení útoků na straně serveru (SSRF). Hodnota je otočena platformou.

**MSI_ENDPOINT** je místní adresa URL, ze které vaše aplikace může žádat o tokeny. Pokud chcete získat token pro prostředek, udělejte požadavek HTTP GET na tento koncový bod, včetně následujících parametrů:

> |Název parametru|V|Popis|
> |-----|-----|-----|
> |resource|Dotaz|Identifikátor URI prostředku AAD prostředku, pro který by měl být získán token. Může to být jedna ze [služeb Azure, které podporují ověřování Azure AD](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication) nebo jakýkoli jiný identifikátor URI prostředku.|
> |api-version|Dotaz|Verze rozhraní API tokenu, která se má použít. "2017-09-01" je aktuálně podporovaná verze.|
> |Tajný kód|Hlavička|Hodnota proměnné prostředí MSI_SECRET. Tato hlavička se používá ke zmírnění útoků na straně serveru (SSRF).|
> |ClientID|Dotaz|(Volitelné, pokud není přiřazeno uživatelem) ID uživatelem přiřazené identity, která se má použít. Je-li tento parametr vynechán, je použita identita přiřazená systémem.|

> [!IMPORTANT]
> Pokud se pokoušíte získat tokeny pro uživatelsky přiřazené identity, musíte zahrnout vlastnost `clientid`. V opačném případě se služba tokenů pokusí získat token pro identitu přiřazenou systémem, která může nebo nemusí existovat.

Úspěšná odpověď 200 OK zahrnuje tělo JSON s následujícími vlastnostmi:

> |Název vlastnosti|Popis|
> |-------------|----------|
> |access_token|Požadovaný přístupový token Volající webová služba může tento token použít k ověření pro přijímající webovou službu.|
> |expires_on|Čas vypršení platnosti přístupového tokenu. Datum se reprezentuje jako počet sekund od roku 1970-01-01T0:0: 0Z UTC až do doby vypršení platnosti. Tato hodnota se používá k určení doby života tokenů uložených v mezipaměti.|
> |resource|Identifikátor URI ID aplikace přijímající webové služby.|
> |token_type|Určuje hodnotu typu tokenu. Jediným typem, který podporuje Azure AD, je nosič. Další informace o nosných tokenech najdete v části [autorizační rozhraní OAuth 2,0: použití nosných tokenů (RFC 6750)](https://www.rfc-editor.org/rfc/rfc6750.txt).|

Tato odpověď je stejná jako [odpověď pro požadavek Service-to-Service Access tokenu služby AAD](../active-directory/develop/v1-oauth2-client-creds-grant-flow.md#service-to-service-access-token-response).

> [!NOTE]
> Proměnné prostředí jsou nastaveny při prvním spuštění procesu, takže po povolení spravované identity pro aplikaci bude pravděpodobně nutné restartovat aplikaci nebo znovu nasadit kód, než `MSI_ENDPOINT` a `MSI_SECRET` jsou k dispozici pro váš kód.

### <a name="rest-protocol-examples"></a>Příklady protokolu REST

Příkladem požadavku může být následující:

```
GET /MSI/token?resource=https://vault.azure.net&api-version=2017-09-01 HTTP/1.1
Host: localhost:4141
Secret: 853b9a84-5bfa-4b22-a3f3-0b9a43d9ad8a
```

A ukázková odpověď může vypadat takto:

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

# <a name="nettabdotnet"></a>[.NET](#tab/dotnet)

> [!TIP]
> Pro jazyky .NET můžete použít také [Microsoft. Azure. Services. AppAuthentication](#asal) místo toho, aby se tento požadavek využíval sami.

```csharp
private readonly HttpClient _client;
// ...
public async Task<HttpResponseMessage> GetToken(string resource)  {
    var request = new HttpRequestMessage(HttpMethod.Get, 
        String.Format("{0}/?resource={1}&api-version=2017-09-01", Environment.GetEnvironmentVariable("MSI_ENDPOINT"), resource));
    request.Headers.Add("Secret", Environment.GetEnvironmentVariable("MSI_SECRET"));
    return await _client.SendAsync(request);
}
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

```javascript
const rp = require('request-promise');
const getToken = function(resource, cb) {
    let options = {
        uri: `${process.env["MSI_ENDPOINT"]}/?resource=${resource}&api-version=2017-09-01`,
        headers: {
            'Secret': process.env["MSI_SECRET"]
        }
    };
    rp(options)
        .then(cb);
}
```

# <a name="pythontabpython"></a>[Python](#tab/python)

```python
import os
import requests

msi_endpoint = os.environ["MSI_ENDPOINT"]
msi_secret = os.environ["MSI_SECRET"]

def get_bearer_token(resource_uri):
    token_auth_uri = f"{msi_endpoint}?resource={resource_uri}&api-version=2017-09-01"
    head_msi = {'Secret':msi_secret}

    resp = requests.get(token_auth_uri, headers=head_msi)
    access_token = resp.json()['access_token']

    return access_token
```

# <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)

```powershell
$resourceURI = "https://<AAD-resource-URI-for-resource-to-obtain-token>"
$tokenAuthURI = $env:MSI_ENDPOINT + "?resource=$resourceURI&api-version=2017-09-01"
$tokenResponse = Invoke-RestMethod -Method Get -Headers @{"Secret"="$env:MSI_SECRET"} -Uri $tokenAuthURI
$accessToken = $tokenResponse.access_token
```

---

### <a name="asal"></a>Použití knihovny Microsoft. Azure. Services. AppAuthentication pro .NET

Pro aplikace a funkce .NET je nejjednodušší způsob práce se spravovanou identitou prostřednictvím balíčku Microsoft. Azure. Services. AppAuthentication. Tato knihovna vám také umožní testovat kód místně na vývojovém počítači pomocí uživatelského účtu ze sady Visual Studio, [Azure CLI](/cli/azure)nebo integrovaného ověřování služby Active Directory. Další informace o možnostech místního vývoje pomocí této knihovny najdete v [Referenční informace k Microsoft. Azure. Services. AppAuthentication]. V této části se dozvíte, jak začít s knihovnou ve vašem kódu.

1. Do aplikace přidejte odkazy na [Microsoft. Azure. Services. AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) a všechny další nezbytné balíčky NuGet. Následující příklad také používá [Microsoft. Azure. trezor](https://www.nuget.org/packages/Microsoft.Azure.KeyVault).

2. Do aplikace přidejte následující kód, který upraví na cíl správného prostředku. Tento příklad ukazuje dva způsoby, jak pracovat s Azure Key Vault:

    ```csharp
    using Microsoft.Azure.Services.AppAuthentication;
    using Microsoft.Azure.KeyVault;
    // ...
    var azureServiceTokenProvider = new AzureServiceTokenProvider();
    string accessToken = await azureServiceTokenProvider.GetAccessTokenAsync("https://vault.azure.net");
    // OR
    var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(azureServiceTokenProvider.KeyVaultTokenCallback));
    ```

Další informace o Microsoft. Azure. Services. AppAuthentication a o operacích, které zpřístupňuje, najdete v tématu Referenční dokumentace k [Referenční informace k Microsoft. Azure. Services. AppAuthentication] a [App Service a trezor klíčů s balíčkem MSI .NET](https://github.com/Azure-Samples/app-service-msi-keyvault-dotnet).

### <a name="using-the-azure-sdk-for-java"></a>Používání sady Azure SDK pro jazyk Java

V případě aplikací a funkcí Java nejjednodušší způsob, jak pracovat se spravovanou identitou, je [sada Azure SDK pro jazyk Java](https://github.com/Azure/azure-sdk-for-java). V této části se dozvíte, jak začít s knihovnou ve vašem kódu.

1. Přidejte odkaz na [knihovnu Azure SDK](https://mvnrepository.com/artifact/com.microsoft.azure/azure). V případě projektů Maven můžete tento fragment kódu přidat do oddílu `dependencies` souboru POM projektu:

    ```xml
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure</artifactId>
        <version>1.23.0</version>
    </dependency>
    ```

2. Pro ověřování použijte objekt `AppServiceMSICredentials`. Tento příklad ukazuje, jak tento mechanismus lze použít pro práci s Azure Key Vault:

    ```java
    import com.microsoft.azure.AzureEnvironment;
    import com.microsoft.azure.management.Azure;
    import com.microsoft.azure.management.keyvault.Vault
    //...
    Azure azure = Azure.authenticate(new AppServiceMSICredentials(AzureEnvironment.AZURE))
            .withSubscription(subscriptionId);
    Vault myKeyVault = azure.vaults().getByResourceGroup(resourceGroup, keyvaultName);

    ```


## <a name="remove"></a>Odebrání identity

Identitu přiřazenou systémem je možné odebrat tak, že ji zakážete pomocí portálu, PowerShellu nebo rozhraní příkazového řádku stejným způsobem, jakým jste ji vytvořili. Uživatelsky přiřazené identity je možné odebrat jednotlivě. Pokud chcete odebrat všechny identity, můžete v protokolu šablony REST/ARM udělat tento postup nastavením typu na "none":

```json
"identity": {
    "type": "None"
}
```

Odebrání identity přiřazené systémem tímto způsobem ji odstraní také z AAD. Identity přiřazené systémem se při odstranění prostředku aplikace taky automaticky odeberou z AAD.

> [!NOTE]
> Existuje také nastavení aplikace, které lze nastavit, WEBSITE_DISABLE_MSI, což zakazuje pouze místní službu tokenů. Ale ponechá identitu na místě a nástroj bude stále zobrazovat spravovanou identitu jako zapnuto nebo povoleno. V důsledku toho se použití tohoto nastavení nedoporučuje.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Zabezpečený přístup SQL Database pomocí spravované identity](app-service-web-tutorial-connect-msi.md)

[Referenční informace k Microsoft. Azure. Services. AppAuthentication]: https://go.microsoft.com/fwlink/p/?linkid=862452
