---
title: Spravované identity
description: Zjistěte, jak spravované identity fungují ve službě Azure App Service a Azure Functions, jak nakonfigurovat spravovanou identitu a vygenerovat token pro back-endový prostředek.
author: mattchenderson
ms.topic: article
ms.date: 03/04/2020
ms.author: mahender
ms.reviewer: yevbronsh
ms.openlocfilehash: 6e3169f2bfcba0a02af1490f875cbab8a14d02f6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79280024"
---
# <a name="how-to-use-managed-identities-for-app-service-and-azure-functions"></a>Jak používat spravované identity pro službu App Service a Funkce Azure

> [!Important] 
> Spravované identity pro služby App Service a Funkce Azure se nebudou chovat podle očekávání, pokud se vaše aplikace migruje mezi předplatnými/tenanty. Aplikace bude muset získat novou identitu, kterou lze provést zakázáním a opětovným povolením funkce. Viz [Odstranění identity](#remove) níže. Následné prostředky budou také muset mít aktualizované zásady přístupu, aby bylo nutné používat novou identitu.

Toto téma ukazuje, jak vytvořit spravovanou identitu pro aplikace App Service a Azure Functions a jak ji použít k přístupu k dalším prostředkům. Spravovaná identita z Azure Active Directory (AAD) umožňuje vaší aplikaci snadný přístup k dalším prostředkům chráněným AAD, jako je Azure Key Vault. Identita je spravována platformou Azure a nevyžaduje, abyste zrozovávat nebo otáčet žádné tajné klíče. Další informace o spravovaných identitách ve službě AAD najdete v [tématu Spravované identity pro prostředky Azure](../active-directory/managed-identities-azure-resources/overview.md).

Vaší žádosti lze udělit dva typy identit: 
- Systémem **přiřazená identita** je s vaší aplikací svázaná a pokud je vaše aplikace odstraněná, smaže se. Aplikace může mít pouze jednu identitu přiřazenou systému.
- **Identita přiřazená uživatelem** je samostatný prostředek Azure, který se dá přiřadit k vaší aplikaci. Aplikace může mít více identit přiřazených uživatelem.

## <a name="add-a-system-assigned-identity"></a>Přidání systémově přiřazené identity

Vytvoření aplikace se systémem přiřazenou identitou vyžaduje, aby byla v aplikaci nastavena další vlastnost.

### <a name="using-the-azure-portal"></a>Použití webu Azure Portal

Chcete-li nastavit spravovanou identitu na portálu, nejprve vytvoříte aplikaci jako normální a poté tuto funkci povolíte.

1. Vytvořte na portálu aplikaci obvyklým způsobem. Přejděte na něj na portálu.

2. Pokud používáte aplikaci funkcí, přejděte na **funkce platformy**. U ostatních typů aplikací přejděte v levém navigačním panelu dolů do skupiny **Nastavení.**

3. Vyberte **identitu**.

4. Na kartě **Systém přiřazený** přepněte **stav** **na Zapnuto**. Klikněte na **Uložit**.

    ![Spravovaná identita ve službě App Service](media/app-service-managed-service-identity/system-assigned-managed-identity-in-azure-portal.png)

### <a name="using-the-azure-cli"></a>Použití Azure CLI

Chcete-li nastavit spravovanou identitu pomocí příkazového příkazu Azure, budete muset použít `az webapp identity assign` příkaz proti existující aplikaci. Máte tři možnosti pro spuštění příkladů v této části:

- Azure [Cloud Shell](../cloud-shell/overview.md) můžete používat z portálu Azure.
- Pomocí integrovaného prostředí Azure Cloud Shell použijte pomocí tlačítka "Try It", které se nachází v pravém horním rohu každého bloku kódu níže.
- [Nainstalujte nejnovější verzi Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) (2.0.31 nebo novější), pokud dáváte přednost použití místní konzoly PŘÍKAZCLI. 

Následující kroky vás provedou vytvořením webové aplikace a přiřazením identity pomocí příkazového příkazu k registraci:

1. Pokud používáte Azure CLI v místní konzole, nejprve se přihlaste k Azure pomocí příkazu [az login](/cli/azure/reference-index#az-login). Použijte účet, který je přidružený k předplatnému Azure, pod kterým chcete aplikaci nasadit:

    ```azurecli-interactive
    az login
    ```
2. Vytvořte webovou aplikaci pomocí cli. Další příklady použití cli se službou aplikace najdete v [tématu ukázky funkce CLI služby App Service](../app-service/samples-cli.md):

    ```azurecli-interactive
    az group create --name myResourceGroup --location westus
    az appservice plan create --name myPlan --resource-group myResourceGroup --sku S1
    az webapp create --name myApp --resource-group myResourceGroup --plan myPlan
    ```

3. Spuštěním `identity assign` příkazu vytvořte identitu pro tuto aplikaci:

    ```azurecli-interactive
    az webapp identity assign --name myApp --resource-group myResourceGroup
    ```

### <a name="using-azure-powershell"></a>Použití Azure Powershell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Následující kroky vás provedou vytvořením webové aplikace a přiřazením identity pomocí Azure PowerShellu:

1. V případě potřeby nainstalujte Azure PowerShell podle pokynů v [průvodci Azure PowerShell](/powershell/azure/overview)a pak spusťte `Login-AzAccount` a vytvořte připojení s Azure.

2. Vytvořte webovou aplikaci pomocí Azure PowerShellu. Další příklady použití Azure PowerShellu se službou App Service najdete v [tématu Ukázky prostředí App Service PowerShell](../app-service/samples-powershell.md):

    ```azurepowershell-interactive
    # Create a resource group.
    New-AzResourceGroup -Name myResourceGroup -Location $location
    
    # Create an App Service plan in Free tier.
    New-AzAppServicePlan -Name $webappname -Location $location -ResourceGroupName myResourceGroup -Tier Free
    
    # Create a web app.
    New-AzWebApp -Name $webappname -Location $location -AppServicePlan $webappname -ResourceGroupName myResourceGroup
    ```

3. Spuštěním `Set-AzWebApp -AssignIdentity` příkazu vytvořte identitu pro tuto aplikaci:

    ```azurepowershell-interactive
    Set-AzWebApp -AssignIdentity $true -Name $webappname -ResourceGroupName myResourceGroup 
    ```

### <a name="using-an-azure-resource-manager-template"></a>Použití šablony Azure Resource Manageru

Šablonu Azure Resource Manager umíte automatizovat nasazení prostředků Azure. Další informace o nasazení do služby App Service a funkce najdete [v tématu Automatizace nasazení prostředků ve službě App Service](../app-service/deploy-complex-application-predictably.md) a [Automatizace nasazení prostředků ve funkcích Azure](../azure-functions/functions-infrastructure-as-code.md).

Libovolný prostředek `Microsoft.Web/sites` typu lze vytvořit s identitou zahrnutím následující vlastnosti do definice prostředku:
```json
"identity": {
    "type": "SystemAssigned"
}    
```

> [!NOTE] 
> Aplikace může mít současně přiřazené i uživatelem přiřazené identity. V takovém případě `type` by byl majetek`SystemAssigned,UserAssigned`

Přidání systémově přiřazeného typu říká Azure k vytvoření a správě identity pro vaši aplikaci.

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

Po vytvoření má web následující další vlastnosti:
```json
"identity": {
    "type": "SystemAssigned",
    "tenantId": "<TENANTID>",
    "principalId": "<PRINCIPALID>"
}
```

Vlastnost tenantId identifikuje, k jakému tenantovi AAD identita patří. PrincipalId je jedinečný identifikátor pro novou identitu aplikace. V rámci Služby AAD má instanční objekt stejného názvu, který jste přiřadili instanci služby App Service nebo Azure Functions.


## <a name="add-a-user-assigned-identity"></a>Přidání identity přiřazené uživateli

Vytvoření aplikace s identitou přiřazenou uživatelem vyžaduje vytvoření identity a přidání jejího identifikátoru prostředku do konfigurace aplikace.

### <a name="using-the-azure-portal"></a>Použití webu Azure Portal

Nejprve budete muset vytvořit prostředek identity přiřazený uživateli.

1. Vytvořte prostředek spravované identity přiřazený uživateli podle [těchto pokynů](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md#create-a-user-assigned-managed-identity).

2. Vytvořte na portálu aplikaci obvyklým způsobem. Přejděte na něj na portálu.

3. Pokud používáte aplikaci funkcí, přejděte na **funkce platformy**. U ostatních typů aplikací přejděte v levém navigačním panelu dolů do skupiny **Nastavení.**

4. Vyberte **identitu**.

5. Na kartě **Uživatel přiřazený** klikněte na **Přidat**.

6. Vyhledejte identitu, kterou jste vytvořili dříve, a vyberte ji. Klikněte na **Přidat**.

    ![Spravovaná identita ve službě App Service](media/app-service-managed-service-identity/user-assigned-managed-identity-in-azure-portal.png)

### <a name="using-an-azure-resource-manager-template"></a>Použití šablony Azure Resource Manageru

Šablonu Azure Resource Manager umíte automatizovat nasazení prostředků Azure. Další informace o nasazení do služby App Service a funkce najdete [v tématu Automatizace nasazení prostředků ve službě App Service](../app-service/deploy-complex-application-predictably.md) a [Automatizace nasazení prostředků ve funkcích Azure](../azure-functions/functions-infrastructure-as-code.md).

Libovolný prostředek `Microsoft.Web/sites` typu lze vytvořit s identitou zahrnutím následujícího bloku `<RESOURCEID>` do definice prostředku a nahrazením ID prostředku požadované identity:
```json
"identity": {
    "type": "UserAssigned",
    "userAssignedIdentities": {
        "<RESOURCEID>": {}
    }
}    
```

> [!NOTE] 
> Aplikace může mít současně přiřazené i uživatelem přiřazené identity. V takovém případě `type` by byl majetek`SystemAssigned,UserAssigned`

Přidání typu přiřazeného uživateli říká Azure, aby používal uživatelem přiřazenou identitu zadanou pro vaši aplikaci.

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

Po vytvoření má web následující další vlastnosti:
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

PrincipalId je jedinečný identifikátor identity, která se používá pro správu AAD. ClientId je jedinečný identifikátor pro novou identitu aplikace, která se používá pro určení identity, která se má použít během volání za běhu.


## <a name="obtain-tokens-for-azure-resources"></a>Získání tokenů pro prostředky Azure

Aplikace může používat svou spravovanou identitu k získání tokenů pro přístup k dalším prostředkům chráněným službou AAD, jako je například Azure Key Vault. Tyto tokeny představují aplikaci, která přistupuje k prostředku, a nikoli žádného konkrétního uživatele aplikace. 

Možná budete muset nakonfigurovat cílový prostředek, aby byl povolen přístup z vaší aplikace. Pokud například požadujete token pro přístup k trezoru klíčů, musíte se ujistit, že jste přidali zásady přístupu, které zahrnují identitu vaší aplikace. V opačném případě budou vaše volání do trezoru klíčů odmítnuta, i když obsahují token. Další informace o tom, které prostředky podporují tokeny Azure Active Directory, najdete v [tématu služby Azure, které podporují ověřování Azure AD](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication).

> [!IMPORTANT]
> Back-endové služby pro spravované identity udržují mezipaměť na identifikátor URI prostředku po dobu přibližně 8 hodin. Pokud aktualizujete zásady přístupu konkrétního cílového prostředku a okamžitě načtete token pro tento prostředek, můžete pokračovat v získávání tokenu uloženého v mezipaměti se zastaralými oprávněními, dokud tento token nevyprší. V současné době neexistuje žádný způsob, jak vynutit obnovení tokenu.

Existuje jednoduchý protokol REST pro získání tokenu ve službě App Service a Azure Functions. To lze použít pro všechny aplikace a jazyky. Pro .NET a Java Azure SDK poskytuje abstrakci přes tento protokol a usnadňuje prostředí pro místní vývoj.

### <a name="using-the-rest-protocol"></a>Použití protokolu REST

Aplikace se spravovanou identitou má definovány dvě proměnné prostředí:

- MSI_ENDPOINT - adresa URL místní služby tokenů.
- MSI_SECRET - záhlaví, které pomáhá zmírnit útoky na požadavky na straně serveru (SSRF). Hodnota je otočena platformou.

**MSI_ENDPOINT** je místní adresa URL, ze které může vaše aplikace požadovat tokeny. Chcete-li získat token pro prostředek, vytvořte požadavek HTTP GET do tohoto koncového bodu, včetně následujících parametrů:

> |Název parametru|V|Popis|
> |-----|-----|-----|
> |prostředek|Dotaz|Identifikátor URI prostředku prostředku AAD prostředku, pro který by měl být získán token. Může se jedná o jednu ze [služeb Azure, které podporují ověřování Azure AD](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication) nebo jakýkoli jiný identifikátor URI prostředků.|
> |verze-api|Dotaz|Verze rozhraní API tokenu, které má být použito. "2017-09-01" je v současné době jedinou podporovanou verzí.|
> |Tajný kód|Hlavička|Hodnota proměnné prostředí MSI_SECRET. Tato hlavička se používá ke zmírnění útoků na serverové požadavky (SSRF).|
> |Clientid|Dotaz|(Nepovinné, pokud není přiřazeno k uživateli) ID uživatelem přiřazené identity, která má být použita. Pokud je vynechána, používá se systémově přiřazená identita.|

> [!IMPORTANT]
> Pokud se pokoušíte získat tokeny pro uživatelem přiřazené `clientid` identity, musíte zahrnout vlastnost. V opačném případě se služba tokenu pokusí získat token pro systémově přiřazenou identitu, která může nebo nemusí existovat.

Úspěšná odpověď 200 OK zahrnuje tělo JSON s následujícími vlastnostmi:

> |Název vlastnosti|Popis|
> |-------------|----------|
> |access_token|Požadovaný přístupový token. Volající webová služba může tento token použít k ověření přijímající webové služby.|
> |expires_on|Čas, kdy vyprší platnost přístupového tokenu. Datum je reprezentováno jako počet sekund od 1970-01-01T0:0:0Z UTC až do doby vypršení platnosti. Tato hodnota se používá k určení životnosti tokenů uložených v mezipaměti.|
> |prostředek|Identifikátor URI ID aplikace přijímající webové služby.|
> |token_type|Označuje hodnotu typu tokenu. Jediný typ, který podporuje Azure AD je Nosič. Další informace o žetonech na doručitele naleznete v [rámci autorizace OAuth 2.0: Použití žetonu nosiče (RFC 6750).](https://www.rfc-editor.org/rfc/rfc6750.txt)|

Tato odpověď je stejná jako [odpověď pro požadavek přístupového tokenu služby AAD služby ke službě](../active-directory/develop/v2-oauth2-client-creds-grant-flow.md#get-a-token).

> [!NOTE]
> Proměnné prostředí jsou nastaveny při prvním spuštění procesu, takže po povolení spravované identity pro vaši aplikaci může `MSI_ENDPOINT` `MSI_SECRET` být nutné restartovat aplikaci nebo znovu nasadit její kód před a jsou k dispozici pro váš kód.

### <a name="rest-protocol-examples"></a>Příklady protokolu REST

Příklad požadavku může vypadat takto:

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

# <a name="net"></a>[.NET](#tab/dotnet)

> [!TIP]
> Pro jazyky .NET můžete také použít [Microsoft.Azure.Services.AppAuthentication](#asal) namísto vytváření tohoto požadavku sami.

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

# <a name="javascript"></a>[Javascript](#tab/javascript)

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

# <a name="python"></a>[Python](#tab/python)

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

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
$resourceURI = "https://<AAD-resource-URI-for-resource-to-obtain-token>"
$tokenAuthURI = $env:MSI_ENDPOINT + "?resource=$resourceURI&api-version=2017-09-01"
$tokenResponse = Invoke-RestMethod -Method Get -Headers @{"Secret"="$env:MSI_SECRET"} -Uri $tokenAuthURI
$accessToken = $tokenResponse.access_token
```

---

### <a name="using-the-microsoftazureservicesappauthentication-library-for-net"></a><a name="asal"></a>Použití knihovny Microsoft.Azure.Services.AppAuthentication pro rozhraní .NET

Pro aplikace a funkce rozhraní .NET je nejjednodušší způsob práce se spravovanou identitou prostřednictvím balíčku Microsoft.Azure.Services.AppAuthentication. Tato knihovna vám také umožní otestovat kód místně ve vývojovém počítači pomocí uživatelského účtu z Visual Studia, [rozhraní příkazového příkazu Azure](/cli/azure)nebo integrovaného ověřování služby Active Directory. Další informace o možnostech místního vývoje v této knihovně najdete v [odkazu Microsoft.Azure.Services.AppAuthentication]. V této části se zobrazí, jak začít s knihovnou v kódu.

1. Přidejte do aplikace odkazy na [microsoft.azure.services.appauthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) a další nezbytné balíčky NuGet. Níže uvedený příklad také používá [Microsoft.Azure.KeyVault](https://www.nuget.org/packages/Microsoft.Azure.KeyVault).

2. Přidejte do aplikace následující kód a upravte tak, aby se zaměřil na správný prostředek. Tento příklad ukazuje dva způsoby práce s Azure Key Vault:

    ```csharp
    using Microsoft.Azure.Services.AppAuthentication;
    using Microsoft.Azure.KeyVault;
    // ...
    var azureServiceTokenProvider = new AzureServiceTokenProvider();
    string accessToken = await azureServiceTokenProvider.GetAccessTokenAsync("https://vault.azure.net");
    // OR
    var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(azureServiceTokenProvider.KeyVaultTokenCallback));
    ```

Další informace o microsoft.azure.services.appauthentication a operacích, které zveřejňuje, najdete v [odkazu Microsoft.Azure.Services.AppAuthentication] a [appvault u Kácení služby MSI .NET](https://github.com/Azure-Samples/app-service-msi-keyvault-dotnet).

### <a name="using-the-azure-sdk-for-java"></a>Použití sady Azure SDK pro Jazyk Java

Pro java aplikace a funkce je nejjednodušší způsob práce se spravovanou identitou prostřednictvím [sady Azure SDK pro Jazyk Java](https://github.com/Azure/azure-sdk-for-java). V této části se zobrazí, jak začít s knihovnou v kódu.

1. Přidejte odkaz na [knihovnu Sady Azure SDK](https://mvnrepository.com/artifact/com.microsoft.azure/azure). U projektů Maven můžete tento úryvek `dependencies` přidat do části souboru POM projektu:

    ```xml
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure</artifactId>
        <version>1.23.0</version>
    </dependency>
    ```

2. Použijte `AppServiceMSICredentials` objekt pro ověřování. Tento příklad ukazuje, jak tento mechanismus může být použit pro práci s Azure Key Vault:

    ```java
    import com.microsoft.azure.AzureEnvironment;
    import com.microsoft.azure.management.Azure;
    import com.microsoft.azure.management.keyvault.Vault
    //...
    Azure azure = Azure.authenticate(new AppServiceMSICredentials(AzureEnvironment.AZURE))
            .withSubscription(subscriptionId);
    Vault myKeyVault = azure.vaults().getByResourceGroup(resourceGroup, keyvaultName);

    ```


## <a name="remove-an-identity"></a><a name="remove"></a>Odebrání identity

Systémem přiřazenou identitu lze odebrat zakázáním funkce pomocí portálu, prostředí PowerShell nebo cli stejným způsobem, jakým byla vytvořena. Uživatelem přiřazené identity lze odebrat jednotlivě. Chcete-li odebrat všechny identity, nastavte typ "None" v [šabloně ARM](#using-an-azure-resource-manager-template):

```json
"identity": {
    "type": "None"
}
```

Odebráním systémově přiřazené identity tímto způsobem ji také odstraníte z aad. Systémem přiřazené identity jsou také automaticky odebrány z AAD při odstranění prostředku aplikace.

> [!NOTE]
> K dispozici je také nastavení aplikace, které lze nastavit, WEBSITE_DISABLE_MSI, který pouze zakáže službu místnítoken. Však ponechá identitu na místě a nástroje budou stále zobrazovat spravovanou identitu jako "zapnuto" nebo "povoleno". V důsledku toho se použití tohoto nastavení nedoporučuje.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Bezpečný přístup k databázi SQL pomocí spravované identity](app-service-web-tutorial-connect-msi.md)

[Odkaz Microsoft.Azure.Services.AppAuthentication]: https://go.microsoft.com/fwlink/p/?linkid=862452
