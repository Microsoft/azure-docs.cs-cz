---
title: Spravované identity
description: Přečtěte si, jak spravované identity fungují v Azure App Service a Azure Functions, jak nakonfigurovat spravovanou identitu a generovat token pro prostředek back-endu.
author: mattchenderson
ms.topic: article
ms.date: 05/27/2020
ms.author: mahender
ms.reviewer: yevbronsh
ms.custom: devx-track-csharp, devx-track-python, devx-track-azurepowershell, devx-track-azurecli
ms.openlocfilehash: c734c0ceb9c4d5418edc51a2c3ad3c052637ad31
ms.sourcegitcommit: e2dc549424fb2c10fcbb92b499b960677d67a8dd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/17/2020
ms.locfileid: "94696978"
---
# <a name="how-to-use-managed-identities-for-app-service-and-azure-functions"></a>Použití spravovaných identit pro App Service a Azure Functions

V tomto tématu se dozvíte, jak vytvořit spravovanou identitu pro App Service a Azure Functions aplikace a jak ji použít pro přístup k dalším prostředkům. 

> [!Important] 
> Spravované identity pro App Service a Azure Functions se nebudou chovat podle očekávání, pokud se vaše aplikace migruje v rámci předplatných nebo tenantů. Aplikace potřebuje získat novou identitu, která je prováděna zakázáním a opakovaným povolením této funkce. Viz [Odebrání identity](#remove) níže. U podřízených prostředků je také potřeba aktualizovat zásady přístupu, aby používaly novou identitu.

[!INCLUDE [app-service-managed-identities](../../includes/app-service-managed-identities.md)]

## <a name="add-a-system-assigned-identity"></a>Přidat identitu přiřazenou systémem

Vytvoření aplikace s identitou přiřazenou systémem vyžaduje pro aplikaci nastavenou další vlastnost.

### <a name="using-the-azure-portal"></a>Použití webu Azure Portal

Pokud chcete na portálu nastavit spravovanou identitu, nejdřív vytvořte aplikaci jako normální a pak tuto funkci povolte.

1. Vytvořte aplikaci na portálu obvyklým způsobem. Přejděte na portál na portálu.

2. Pokud používáte aplikaci Function App, přejděte k **funkcím platformy**. U ostatních typů aplikací se posuňte dolů ke skupině **Nastavení** v levém navigačním panelu.

3. Vyberte **Identita**.

4. V rámci karty **přiřazené systémem** přepněte **stav** na **zapnuto**. Klikněte na **Uložit**.

    ![Snímek obrazovky, který ukazuje, kde přepnout stav na zapnuto a pak vyberte Uložit.](media/app-service-managed-service-identity/system-assigned-managed-identity-in-azure-portal.png)


> [!NOTE] 
> Pokud chcete najít spravovanou identitu pro vaši webovou aplikaci nebo aplikaci slotu v Azure Portal v části **podnikové aplikace**, podívejte se do části **uživatelská nastavení** . Název slotu je obvykle podobný `<app name>/slots/<slot name>` .


### <a name="using-the-azure-cli"></a>Použití Azure CLI

K nastavení spravované identity pomocí Azure CLI budete muset použít `az webapp identity assign` příkaz pro existující aplikaci. Máte tři možnosti, jak spustit příklady v této části:

- Použijte [Azure Cloud Shell](../cloud-shell/overview.md) z Azure Portal.
- Použijte vložený Azure Cloud Shell pomocí tlačítka "vyzkoušet", které je umístěné v pravém horním rohu každého bloku kódu níže.
- Pokud upřednostňujete použití místní konzoly CLI, [nainstalujte nejnovější verzi Azure CLI](/cli/azure/install-azure-cli) (2.0.31 nebo novější). 

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

3. Spusťte `identity assign` příkaz pro vytvoření identity pro tuto aplikaci:

    ```azurecli-interactive
    az webapp identity assign --name myApp --resource-group myResourceGroup
    ```

### <a name="using-azure-powershell"></a>Použití Azure Powershell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Následující kroky vás provedou vytvořením aplikace a přiřazením identity pomocí Azure PowerShell. Pokyny pro vytvoření webové aplikace a aplikace Function App se liší.

#### <a name="using-azure-powershell-for-a-web-app"></a>Použití Azure PowerShell pro webovou aplikaci

1. V případě potřeby nainstalujte Azure PowerShell pomocí pokynů uvedených v [příručce Azure PowerShell](/powershell/azure/)a pak spuštěním rutiny `Login-AzAccount` vytvořte připojení k Azure.

2. Vytvořte webovou aplikaci pomocí Azure PowerShell. Další příklady použití Azure PowerShell s App Service najdete v tématu [App Service ukázek PowerShellu](../app-service/samples-powershell.md):

    ```azurepowershell-interactive
    # Create a resource group.
    New-AzResourceGroup -Name $resourceGroupName -Location $location

    # Create an App Service plan in Free tier.
    New-AzAppServicePlan -Name $webappname -Location $location -ResourceGroupName $resourceGroupName -Tier Free

    # Create a web app.
    New-AzWebApp -Name $webappname -Location $location -AppServicePlan $webappname -ResourceGroupName $resourceGroupName
    ```

3. Spusťte `Set-AzWebApp -AssignIdentity` příkaz pro vytvoření identity pro tuto aplikaci:

    ```azurepowershell-interactive
    Set-AzWebApp -AssignIdentity $true -Name $webappname -ResourceGroupName $resourceGroupName 
    ```

#### <a name="using-azure-powershell-for-a-function-app"></a>Použití Azure PowerShell pro aplikaci Function App

1. V případě potřeby nainstalujte Azure PowerShell pomocí pokynů uvedených v [příručce Azure PowerShell](/powershell/azure/)a pak spuštěním rutiny `Login-AzAccount` vytvořte připojení k Azure.

2. Vytvořte aplikaci funkcí pomocí Azure PowerShell. Další příklady použití Azure PowerShell s Azure Functions najdete v tématu [AZ. Functions](/powershell/module/az.functions/?view=azps-4.1.0#functions):

    ```azurepowershell-interactive
    # Create a resource group.
    New-AzResourceGroup -Name $resourceGroupName -Location $location

    # Create a storage account.
    New-AzStorageAccount -Name $storageAccountName -ResourceGroupName $resourceGroupName -SkuName $sku

    # Create a function app with a system-assigned identity.
    New-AzFunctionApp -Name $functionAppName -ResourceGroupName $resourceGroupName -Location $location -StorageAccountName $storageAccountName -Runtime $runtime -IdentityType SystemAssigned
    ```

Místo toho můžete také aktualizovat existující aplikaci Function App `Update-AzFunctionApp` .

### <a name="using-an-azure-resource-manager-template"></a>Použití šablony Azure Resource Manager

K automatizaci nasazení prostředků Azure můžete použít šablonu Azure Resource Manager. Další informace o nasazení do App Service a funkcí naleznete v tématu [Automatizace nasazení prostředků v App Service](../app-service/deploy-complex-application-predictably.md) a [Automatizace nasazení prostředků v Azure Functions](../azure-functions/functions-infrastructure-as-code.md).

Libovolný prostředek typu `Microsoft.Web/sites` se dá vytvořit s identitou, a to zahrnutím následující vlastnosti do definice prostředků:

```json
"identity": {
    "type": "SystemAssigned"
}
```

> [!NOTE]
> Aplikace může mít současně přiřazené i uživatelsky přiřazené identity. V tomto případě `type` by vlastnost byla `SystemAssigned,UserAssigned`

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

Vlastnost tenantId určuje, do kterého tenanta Azure AD patří identita. PrincipalId je jedinečný identifikátor pro novou identitu aplikace. V rámci služby Azure AD má instanční objekt stejný název, jaký jste zadali App Service nebo Azure Functions instanci.

Pokud potřebujete na tyto vlastnosti odkazovat v pozdější fázi šablony, můžete tak učinit prostřednictvím [ `reference()` funkce šablony](../azure-resource-manager/templates/template-functions-resource.md#reference) s `'Full'` příznakem, jako v tomto příkladu:

```json
{
    "tenantId": "[reference(resourceId('Microsoft.Web/sites', variables('appName')), '2018-02-01', 'Full').identity.tenantId]",
    "objectId": "[reference(resourceId('Microsoft.Web/sites', variables('appName')), '2018-02-01', 'Full').identity.principalId]",
}
```

## <a name="add-a-user-assigned-identity"></a>Přidání uživatelsky přiřazené identity

Vytvoření aplikace s uživatelem přiřazenou identitou vyžaduje, abyste vytvořili identitu a pak přidali svůj identifikátor prostředku do vaší konfigurace aplikace.

### <a name="using-the-azure-portal"></a>Použití webu Azure Portal

Nejdřív budete muset vytvořit prostředek identity přiřazené uživatelem.

1. Podle [těchto pokynů](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md#create-a-user-assigned-managed-identity)vytvořte prostředek spravované identity přiřazené uživatelem.

2. Vytvořte aplikaci na portálu obvyklým způsobem. Přejděte na portál na portálu.

3. Pokud používáte aplikaci Function App, přejděte k **funkcím platformy**. U ostatních typů aplikací se posuňte dolů ke skupině **Nastavení** v levém navigačním panelu.

4. Vyberte **Identita**.

5. Na kartě **přiřazené uživatelem** klikněte na tlačítko **Přidat**.

6. Vyhledejte identitu, kterou jste vytvořili dříve, a vyberte ji. Klikněte na **Přidat**.

    ![Spravovaná identita v App Service](media/app-service-managed-service-identity/user-assigned-managed-identity-in-azure-portal.png)

### <a name="using-azure-powershell"></a>Použití Azure Powershell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Následující kroky vás provedou vytvořením aplikace a přiřazením identity pomocí Azure PowerShell.

> [!NOTE]
> Aktuální verze Azure PowerShell rutin pro Azure App Service nepodporuje identity přiřazené uživatelem. Následující pokyny jsou k disAzure Functions.

1. V případě potřeby nainstalujte Azure PowerShell pomocí pokynů uvedených v [příručce Azure PowerShell](/powershell/azure/)a pak spuštěním rutiny `Login-AzAccount` vytvořte připojení k Azure.

2. Vytvořte aplikaci funkcí pomocí Azure PowerShell. Další příklady použití Azure PowerShell s Azure Functions najdete v tématu [AZ. Functions](/powershell/module/az.functions/?view=azps-4.1.0#functions). Níže uvedený skript také využívá k `New-AzUserAssignedIdentity` tomu, že se musí nainstalovat samostatně podle potřeby pro [Vytvoření, výpis nebo odstranění spravované identity přiřazené uživatelem pomocí Azure PowerShell](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-powershell.md).

    ```azurepowershell-interactive
    # Create a resource group.
    New-AzResourceGroup -Name $resourceGroupName -Location $location

    # Create a storage account.
    New-AzStorageAccount -Name $storageAccountName -ResourceGroupName $resourceGroupName -SkuName $sku

    # Create a user-assigned identity. This requires installation of the "Az.ManagedServiceIdentity" module.
    $userAssignedIdentity = New-AzUserAssignedIdentity -Name $userAssignedIdentityName -ResourceGroupName $resourceGroupName

    # Create a function app with a user-assigned identity.
    New-AzFunctionApp -Name $functionAppName -ResourceGroupName $resourceGroupName -Location $location -StorageAccountName $storageAccountName -Runtime $runtime -IdentityType UserAssigned -IdentityId $userAssignedIdentity.Id
    ```

Místo toho můžete také aktualizovat existující aplikaci Function App `Update-AzFunctionApp` .

### <a name="using-an-azure-resource-manager-template"></a>Použití šablony Azure Resource Manager

K automatizaci nasazení prostředků Azure můžete použít šablonu Azure Resource Manager. Další informace o nasazení do App Service a funkcí naleznete v tématu [Automatizace nasazení prostředků v App Service](../app-service/deploy-complex-application-predictably.md) a [Automatizace nasazení prostředků v Azure Functions](../azure-functions/functions-infrastructure-as-code.md).

Libovolný prostředek typu `Microsoft.Web/sites` se dá vytvořit s identitou, a to tak, že do definice prostředků zahrneme následující blok a nahradíte `<RESOURCEID>` ID prostředku požadované identity:

```json
"identity": {
    "type": "UserAssigned",
    "userAssignedIdentities": {
        "<RESOURCEID>": {}
    }
}
```

> [!NOTE]
> Aplikace může mít současně přiřazené i uživatelsky přiřazené identity. V tomto případě `type` by vlastnost byla `SystemAssigned,UserAssigned`

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

PrincipalId je jedinečný identifikátor pro identitu, která se používá pro správu Azure AD. ClientId je jedinečný identifikátor pro novou identitu aplikace, která se používá k určení identity, která se má použít během volání za běhu.

## <a name="obtain-tokens-for-azure-resources"></a>Získání tokenů pro prostředky Azure

Aplikace může pomocí spravované identity získat tokeny pro přístup k jiným prostředkům chráněným službou Azure AD, jako je například Azure Key Vault. Tyto tokeny reprezentují aplikaci, která přistupuje k prostředku, a ne žádného konkrétního uživatele aplikace. 

Možná budete muset nakonfigurovat cílový prostředek, aby povoloval přístup z vaší aplikace. Například pokud požadujete token pro přístup k Key Vault, musíte se ujistit, že jste přidali zásadu přístupu, která zahrnuje identitu vaší aplikace. V opačném případě budou volání Key Vault odmítnuta, a to i v případě, že obsahují token. Další informace o tom, které prostředky podporují Azure Active Directory tokeny, najdete v tématu [služby Azure, které podporují ověřování Azure AD](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication).

> [!IMPORTANT]
> Back-endové služby pro spravované identity udržují po dobu přibližně 8 hodin identifikátor URI na jeden prostředek. Pokud aktualizujete zásady přístupu určitého cílového prostředku a hned načtete token pro tento prostředek, můžete i nadále získat token uložený v mezipaměti se zastaralými oprávněními, dokud tento token nevyprší. V tuto chvíli neexistuje způsob, jak vynutit aktualizaci tokenu.

K získání tokenu v App Service a Azure Functions existuje jednoduchý protokol REST. Tato možnost se dá použít pro všechny aplikace a jazyky. Pro .NET a Java poskytuje sada Azure SDK abstrakci prostřednictvím tohoto protokolu a usnadňuje místní vývojové prostředí.

### <a name="using-the-rest-protocol"></a>Použití protokolu REST

> [!NOTE]
> Starší verze tohoto protokolu, používající verzi rozhraní API "2017-09-01", používali `secret` hlavičku místo `X-IDENTITY-HEADER` a přijali vlastnost pouze pro uživatele, který je `clientid` přiřazen. Vrátil také `expires_on` ve formátu časového razítka. MSI_ENDPOINT lze použít jako alias pro IDENTITY_ENDPOINT a MSI_SECRET lze použít jako alias pro IDENTITY_HEADER. Tato verze protokolu se v současnosti vyžaduje pro plány hostování spotřeby Linux.

Aplikace se spravovanou identitou má definované dvě proměnné prostředí:

- IDENTITY_ENDPOINT – adresa URL místní služby tokenu.
- IDENTITY_HEADER – hlavička sloužící k omezení útoků na straně serveru (SSRF). Hodnota je otočena platformou.

**IDENTITY_ENDPOINT** je místní adresa URL, ze které vaše aplikace může žádat o tokeny. Pokud chcete získat token pro prostředek, udělejte požadavek HTTP GET na tento koncový bod, včetně následujících parametrů:

> | Název parametru    | V     | Popis                                                                                                                                                                                                                                                                                                                                |
> |-------------------|--------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
> | prostředek          | Dotazy  | Identifikátor URI prostředku Azure AD prostředku, pro který by měl být získán token. Může to být jedna ze [služeb Azure, které podporují ověřování Azure AD](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication) nebo jakýkoli jiný identifikátor URI prostředku.    |
> | verze-api       | Dotazy  | Verze rozhraní API tokenu, která se má použít. Použijte prosím "2019-08-01" nebo novější (Pokud nepoužíváte spotřebu Linux, který aktuálně jenom nabízí "2017-09-01" – viz poznámku výše).                                                                                                                                                                                                                                                                 |
> | X-IDENTITY – HLAVIČKA | Hlavička | Hodnota proměnné prostředí IDENTITY_HEADER. Tato hlavička se používá ke zmírnění útoků na straně serveru (SSRF).                                                                                                                                                                                                    |
> | client_id         | Dotazy  | Volitelné ID klienta, které má uživatel přiřazenou identitu použít. Nelze použít na žádost, která obsahuje `principal_id` , `mi_res_id` nebo `object_id` . Pokud jsou vynechány všechny parametry ID ( `client_id` , `principal_id` , `object_id` a `mi_res_id` ), je použita identita přiřazená systémem.                                             |
> | principal_id      | Dotazy  | Volitelné ID objektu zabezpečení přiřazené identity uživatele, která se má použít `object_id` je alias, který může být použit místo toho. Nelze použít pro požadavek, který obsahuje client_id, mi_res_id nebo object_id. Pokud jsou vynechány všechny parametry ID ( `client_id` , `principal_id` , `object_id` a `mi_res_id` ), je použita identita přiřazená systémem. |
> | mi_res_id         | Dotazy  | Volitelné ID prostředku Azure pro uživatelem přiřazenou identitu, která se má použít. Nelze použít na žádost, která obsahuje `principal_id` , `client_id` nebo `object_id` . Pokud jsou vynechány všechny parametry ID ( `client_id` , `principal_id` , `object_id` a `mi_res_id` ), je použita identita přiřazená systémem.                                      |

> [!IMPORTANT]
> Pokud se pokoušíte získat tokeny pro uživatelsky přiřazené identity, musíte zahrnout jednu z volitelných vlastností. V opačném případě se služba tokenů pokusí získat token pro identitu přiřazenou systémem, která může nebo nemusí existovat.

Úspěšná odpověď 200 OK zahrnuje tělo JSON s následujícími vlastnostmi:

> | Název vlastnosti | Popis                                                                                                                                                                                                                                        |
> |---------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
> | access_token  | Požadovaný přístupový token Volající webová služba může tento token použít k ověření pro přijímající webovou službu.                                                                                                                               |
> | client_id     | ID klienta použité identity.                                                                                                                                                                                                       |
> | expires_on    | Časový interval pro přístup k vypršení platnosti přístupového tokenu Datum se reprezentuje jako počet sekund od "1970-01-01T0:0: 0Z UTC" (odpovídá `exp` deklaraci identity tokenu).                                                                                |
> | not_before    | Časové rozpětí, kdy se přístupový token projeví a lze jej přijmout. Datum se reprezentuje jako počet sekund od "1970-01-01T0:0: 0Z UTC" (odpovídá `nbf` deklaraci identity tokenu).                                                      |
> | prostředek      | Prostředek, pro který byl požadován přístupový token, který odpovídá `resource` parametru řetězce dotazu žádosti.                                                                                                                               |
> | token_type    | Určuje hodnotu typu tokenu. Jediným typem, který podporuje Azure AD, je nosič. Další informace o nosných tokenech najdete v části [autorizační rozhraní OAuth 2,0: použití nosných tokenů (RFC 6750)](https://www.rfc-editor.org/rfc/rfc6750.txt). |

Tato odpověď je stejná jako [odpověď pro žádost o přístup k tokenu služby Azure AD na službu](../active-directory/azuread-dev/v1-oauth2-client-creds-grant-flow.md#service-to-service-access-token-response).

### <a name="rest-protocol-examples"></a>Příklady protokolu REST

Příkladem požadavku může být následující:

```http
GET /MSI/token?resource=https://vault.azure.net&api-version=2019-08-01 HTTP/1.1
Host: localhost:4141
X-IDENTITY-HEADER: 853b9a84-5bfa-4b22-a3f3-0b9a43d9ad8a
```

A ukázková odpověď může vypadat takto:

```http
HTTP/1.1 200 OK
Content-Type: application/json

{
    "access_token": "eyJ0eXAi…",
    "expires_on": "1586984735",
    "resource": "https://vault.azure.net",
    "token_type": "Bearer",
    "client_id": "5E29463D-71DA-4FE0-8E69-999B57DB23B0"
}
```

### <a name="code-examples"></a>Příklady kódu

# <a name="net"></a>[.NET](#tab/dotnet)

> [!TIP]
> Pro jazyky .NET můžete použít také [Microsoft. Azure. Services. AppAuthentication](#asal) místo toho, aby se tento požadavek využíval sami.

```csharp
private readonly HttpClient _client;
// ...
public async Task<HttpResponseMessage> GetToken(string resource)  {
    var request = new HttpRequestMessage(HttpMethod.Get, 
        String.Format("{0}/?resource={1}&api-version=2019-08-01", Environment.GetEnvironmentVariable("IDENTITY_ENDPOINT"), resource));
    request.Headers.Add("X-IDENTITY-HEADER", Environment.GetEnvironmentVariable("IDENTITY_HEADER"));
    return await _client.SendAsync(request);
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const rp = require('request-promise');
const getToken = function(resource, cb) {
    let options = {
        uri: `${process.env["IDENTITY_ENDPOINT"]}/?resource=${resource}&api-version=2019-08-01`,
        headers: {
            'X-IDENTITY-HEADER': process.env["IDENTITY_HEADER"]
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

identity_endpoint = os.environ["IDENTITY_ENDPOINT"]
identity_header = os.environ["IDENTITY_HEADER"]

def get_bearer_token(resource_uri):
    token_auth_uri = f"{identity_endpoint}?resource={resource_uri}&api-version=2019-08-01"
    head_msi = {'X-IDENTITY-HEADER':identity_header}

    resp = requests.get(token_auth_uri, headers=head_msi)
    access_token = resp.json()['access_token']

    return access_token
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
$resourceURI = "https://<AAD-resource-URI-for-resource-to-obtain-token>"
$tokenAuthURI = $env:IDENTITY_ENDPOINT + "?resource=$resourceURI&api-version=2019-08-01"
$tokenResponse = Invoke-RestMethod -Method Get -Headers @{"X-IDENTITY-HEADER"="$env:IDENTITY_HEADER"} -Uri $tokenAuthURI
$accessToken = $tokenResponse.access_token
```

---

### <a name="using-the-microsoftazureservicesappauthentication-library-for-net"></a><a name="asal"></a>Použití knihovny Microsoft. Azure. Services. AppAuthentication pro .NET

Pro aplikace a funkce .NET je nejjednodušší způsob práce se spravovanou identitou prostřednictvím balíčku Microsoft. Azure. Services. AppAuthentication. Tato knihovna vám také umožní testovat kód místně na vývojovém počítači pomocí uživatelského účtu ze sady Visual Studio, [Azure CLI](/cli/azure)nebo integrovaného ověřování služby Active Directory. V případě hostování v cloudu bude ve výchozím nastavení použita identita přiřazená systémem, ale toto chování můžete přizpůsobit pomocí proměnné prostředí připojovacího řetězce, která odkazuje na ID klienta identity přiřazené uživatelem. Další informace o možnostech vývoje pomocí této knihovny najdete v [referenčních informacích k Microsoft. Azure. Services. AppAuthentication]. V této části se dozvíte, jak začít s knihovnou ve vašem kódu.

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

Pokud chcete použít spravovanou identitu přiřazenou uživatelem, můžete nastavit `AzureServicesAuthConnectionString` nastavení aplikace na `RunAs=App;AppId=<clientId-guid>` . Nahraďte `<clientId-guid>` ID klienta identity, kterou chcete použít. Můžete definovat několik takových připojovacích řetězců pomocí vlastních nastavení aplikace a předání jejich hodnot do konstruktoru AzureServiceTokenProvider.

```csharp
    var identityConnectionString1 = Environment.GetEnvironmentVariable("UA1_ConnectionString");
    var azureServiceTokenProvider1 = new AzureServiceTokenProvider(identityConnectionString1);
    
    var identityConnectionString2 = Environment.GetEnvironmentVariable("UA2_ConnectionString");
    var azureServiceTokenProvider2 = new AzureServiceTokenProvider(identityConnectionString2);
```

Další informace o konfiguraci AzureServiceTokenProvider a o operacích, které zpřístupňuje, najdete v tématu Referenční dokumentace k [Microsoft. Azure. Services. AppAuthentication] a [App Service a trezor klíčů s balíčkem MSI .NET](https://github.com/Azure-Samples/app-service-msi-keyvault-dotnet).

### <a name="using-the-azure-sdk-for-java"></a>Používání sady Azure SDK pro jazyk Java

V případě aplikací a funkcí Java nejjednodušší způsob, jak pracovat se spravovanou identitou, je [sada Azure SDK pro jazyk Java](https://github.com/Azure/azure-sdk-for-java). V této části se dozvíte, jak začít s knihovnou ve vašem kódu.

1. Přidejte odkaz na [knihovnu Azure SDK](https://mvnrepository.com/artifact/com.microsoft.azure/azure). V případě projektů Maven můžete tento fragment kódu přidat do `dependencies` oddílu souboru pom projektu:

    ```xml
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure</artifactId>
        <version>1.23.0</version>
    </dependency>
    ```

2. Použijte `AppServiceMSICredentials` objekt pro ověřování. Tento příklad ukazuje, jak tento mechanismus lze použít pro práci s Azure Key Vault:

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

Identitu přiřazenou systémem je možné odebrat tak, že ji zakážete pomocí portálu, PowerShellu nebo rozhraní příkazového řádku stejným způsobem, jakým jste ji vytvořili. Uživatelsky přiřazené identity je možné odebrat jednotlivě. Pokud chcete odebrat všechny identity, nastavte typ identity na None (žádné).

Odebrání identity přiřazené systémem tímto způsobem ji odstraní také z Azure AD. Identity přiřazené systémem se při odstranění prostředku aplikace automaticky odeberou z Azure AD.

Odebrání všech identit v [šabloně ARM](#using-an-azure-resource-manager-template):

```json
"identity": {
    "type": "None"
}
```

Chcete-li odebrat všechny identity v Azure PowerShell (pouze Azure Functions):

```azurepowershell-interactive
# Update an existing function app to have IdentityType "None".
Update-AzFunctionApp -Name $functionAppName -ResourceGroupName $resourceGroupName -IdentityType None
```

> [!NOTE]
> Existuje také nastavení aplikace, které lze nastavit, WEBSITE_DISABLE_MSI, což zakazuje pouze místní službu tokenů. Ale ponechá identitu na místě a nástroj bude stále zobrazovat spravovanou identitu jako zapnuto nebo povoleno. V důsledku toho se použití tohoto nastavení nedoporučuje.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Zabezpečený přístup SQL Database pomocí spravované identity](app-service-web-tutorial-connect-msi.md)

[Referenční informace k Microsoft. Azure. Services. AppAuthentication]: ../key-vault/general/service-to-service-authentication.md
