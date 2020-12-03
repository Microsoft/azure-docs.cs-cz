---
title: Nastavení ověřování
titleSuffix: Azure Machine Learning
description: Naučte se, jak nastavit a nakonfigurovat ověřování pro různé prostředky a pracovní postupy v Azure Machine Learning.
services: machine-learning
author: cjgronlund
ms.author: cgronlun
ms.reviewer: larryfr
ms.service: machine-learning
ms.subservice: core
ms.date: 11/05/2020
ms.topic: conceptual
ms.custom: how-to, has-adal-ref, devx-track-js, devx-track-azurecli, contperfq2
ms.openlocfilehash: a9bf03fd59e6088ce1c1b09a41b2bf55d1f45455
ms.sourcegitcommit: 5b93010b69895f146b5afd637a42f17d780c165b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/02/2020
ms.locfileid: "96532954"
---
# <a name="set-up-authentication-for-azure-machine-learning-resources-and-workflows"></a>Nastavení ověřování pro prostředky a pracovní postupy služby Azure Machine Learning


Přečtěte si, jak nastavit ověřování pro váš pracovní prostor Azure Machine Learning. Ověřování do vašeho pracovního prostoru Azure Machine Learning je založené na __Azure Active Directory__ (Azure AD) pro většinu věcí. Obecně platí tři pracovní postupy pro ověřování, které můžete použít při připojování k pracovnímu prostoru:

* __Interaktivní__: účet můžete použít v Azure Active Directory k přímému ověření nebo k získání tokenu, který se používá k ověřování. Interaktivní ověřování se používá během _experimentů a iterativního vývoje_. Interaktivní ověřování umožňuje řídit přístup k prostředkům (například webové službě) na základě jednotlivých uživatelů.

* __Instanční objekt__: v Azure Active Directory vytvoříte hlavní účet služby a použijete ho k ověření nebo získání tokenu. Instanční objekt se používá v případě, že potřebujete _automatizovaný proces ověřování_ ke službě bez nutnosti zásahu uživatele. Například skript průběžné integrace a nasazení, který na vlaky a testuje model pokaždé, když se kód školení změní.

* __Spravovaná identita__: při použití sady Azure Machine Learning SDK _na virtuálním počítači Azure_ můžete použít spravovanou identitu pro Azure. Tento pracovní postup umožňuje virtuálnímu počítači připojit se k pracovnímu prostoru pomocí spravované identity, aniž byste museli ukládat přihlašovací údaje v kódu Pythonu nebo vyzvat uživatele k ověření. Azure Machine Learning výpočetní clustery je taky možné nakonfigurovat tak, aby pro přístup k pracovnímu prostoru používaly spravovanou identitu při _výuce modelů_.

> [!IMPORTANT]
> Bez ohledu na použitou pracovní postup ověřování se řízení přístupu na základě role Azure (Azure RBAC) používá k vymezení úrovně přístupu (autorizace) povoleného pro prostředky. Například správce nebo proces automatizace může mít přístup k vytvoření výpočetní instance, ale ne k jeho použití, zatímco ho pracovník IT může použít, ale nemůže ho odstranit ani vytvořit. Další informace najdete v tématu [Správa přístupu k pracovnímu prostoru Azure Machine Learning](how-to-assign-roles.md).

## <a name="prerequisites"></a>Předpoklady

* Vytvořte [pracovní prostor Azure Machine Learning](how-to-manage-workspace.md).
* [Nakonfigurujte vývojové prostředí](how-to-configure-environment.md) pro instalaci Azure Machine Learning sady SDK nebo použijte [výpočetní instanci Azure Machine Learning](concept-azure-machine-learning-architecture.md#compute-instance) s již nainstalovanou sadou SDK.

## <a name="azure-active-directory"></a>Azure Active Directory

Všechny ověřovací pracovní postupy pro váš pracovní prostor spoléhají na Azure Active Directory. Pokud chcete, aby se uživatelé mohli ověřovat pomocí jednotlivých účtů, musí mít ve službě Azure AD účty. Pokud chcete použít instanční objekty, musí existovat ve službě Azure AD. Spravované identity jsou také funkcí služby Azure AD. 

Další informace o službě Azure AD najdete v tématu [co je Azure Active Directory ověřování](..//active-directory/authentication/overview-authentication.md).

Až vytvoříte účty Azure AD, přečtěte si téma [Správa přístupu k Azure Machine Learningmu pracovnímu prostoru](how-to-assign-roles.md) , kde najdete informace o tom, jak jim udělit přístup k pracovnímu prostoru a dalším operacím v Azure Machine Learning.

## <a name="configure-a-service-principal"></a>Konfigurace instančního objektu

Pokud chcete použít instanční objekt (SP), musíte nejdřív vytvořit SP a udělit mu přístup k vašemu pracovnímu prostoru. Jak bylo zmíněno dříve, pro řízení přístupu se používá řízení přístupu na základě role Azure (Azure RBAC), takže musíte také rozhodnout, jaký přístup k této aktualizaci chcete udělit.

> [!IMPORTANT]
> Při použití instančního objektu udělte mu __minimální přístup vyžadovaný pro úkol__ , ke kterému se používá. Například neudělíte vlastníkovi instančního objektu nebo přístup k přispěvateli, pokud se k němu používá přístup, který čte přístupový token pro nasazení webu.
>
> Důvodem pro udělení minimálního přístupu je, že instanční objekt používá k ověření heslo a heslo může být uloženo jako součást skriptu automatizace. Pokud je heslo nedostupné, má minimální přístup vyžadovaný pro konkrétní úkoly minimalizovat škodlivé použití SP.

Nejjednodušší způsob, jak vytvořit SP a udělit přístup k vašemu pracovnímu prostoru, je použití [Azure CLI](/cli/azure/install-azure-cli?preserve-view=true&view=azure-cli-latest). K vytvoření instančního objektu a udělení přístupu k vašemu pracovnímu prostoru použijte následující postup:

> [!NOTE]
> K provedení všech těchto kroků musíte být správcem předplatného.

1. Ověření u vašeho předplatného Azure:

    ```azurecli-interactive
    az login
    ```

    Pokud rozhraní příkazového řádku může spustit výchozí prohlížeč, udělá to a načte přihlašovací stránku. V opačném případě je nutné otevřít prohlížeč a postupovat podle pokynů v příkazovém řádku. Pokyny zahrnují procházení [https://aka.ms/devicelogin](https://aka.ms/devicelogin) a zadávání autorizačního kódu.

    Pokud máte více předplatných Azure, můžete `az account set -s <subscription name or ID>` k nastavení předplatného použít příkaz. Další informace najdete v tématu [použití více předplatných Azure](/cli/azure/manage-azure-subscriptions-azure-cli?view=azure-cli-latest).

    Další metody ověřování najdete v tématu [přihlášení pomocí Azure CLI](/cli/azure/authenticate-azure-cli?preserve-view=true&view=azure-cli-latest).

1. Nainstalujte Azure Machine Learning rozšíření:

    ```azurecli-interactive
    az extension add -n azure-cli-ml
    ```

1. Vytvoření instančního objektu. V následujícím příkladu se vytvoří SP s názvem **ml-auth** :

    ```azurecli-interactive
    az ad sp create-for-rbac --sdk-auth --name ml-auth
    ```

    Výstup bude vypadat jako JSON podobný následujícímu. Poznamenejte si `clientId` pole, `clientSecret` a `tenantId` , jak je budete potřebovat pro další kroky v tomto článku.

    ```json
    {
        "clientId": "your-client-id",
        "clientSecret": "your-client-secret",
        "subscriptionId": "your-sub-id",
        "tenantId": "your-tenant-id",
        "activeDirectoryEndpointUrl": "https://login.microsoftonline.com",
        "resourceManagerEndpointUrl": "https://management.azure.com",
        "activeDirectoryGraphResourceId": "https://graph.windows.net",
        "sqlManagementEndpointUrl": "https://management.core.windows.net:5555",
        "galleryEndpointUrl": "https://gallery.azure.com/",
        "managementEndpointUrl": "https://management.core.windows.net"
    }
    ```

1. Načtěte podrobnosti o instančním objektu pomocí `clientId` hodnoty vrácené v předchozím kroku:

    ```azurecli-interactive
    az ad sp show --id your-client-id
    ```

    Následující kód JSON je zjednodušený příklad výstupu příkazu. Poznamenejte si `objectId` pole, jak budete potřebovat jeho hodnotu pro další krok.

    ```json
    {
        "accountEnabled": "True",
        "addIns": [],
        "appDisplayName": "ml-auth",
        ...
        ...
        ...
        "objectId": "your-sp-object-id",
        "objectType": "ServicePrincipal"
    }
    ```

1. Umožněte službě SP přístup k vašemu pracovnímu prostoru Azure Machine Learning. Budete potřebovat název vašeho pracovního prostoru a jeho název skupiny prostředků pro `-w` parametry a v `-g` uvedeném pořadí. Pro `--user` parametr použijte `objectId` hodnotu z předchozího kroku. `--role`Parametr umožňuje nastavit roli přístupu pro instanční objekt. V následujícím příkladu je tato aktualizace přiřazena k roli **vlastníka** . 

    > [!IMPORTANT]
    > Přístup vlastníka umožňuje instančnímu objektu v pracovním prostoru prakticky jakoukoli operaci. V tomto dokumentu se používá k demonstrování, jak udělit přístup; v produkčním prostředí doporučuje Microsoft udělit instančnímu objektu minimální přístup, který je potřeba k provedení role, pro kterou máte oprávnění. Informace o vytvoření vlastní role s přístupem potřebným pro váš scénář najdete v tématu [Správa přístupu k Azure Machine Learningmu pracovnímu prostoru](how-to-assign-roles.md).

    ```azurecli-interactive
    az ml workspace share -w your-workspace-name -g your-resource-group-name --user your-sp-object-id --role owner
    ```

    Toto volání nevyprodukuje žádný výstup při úspěchu.

## <a name="configure-a-managed-identity"></a>Konfigurace spravované identity

> [!IMPORTANT]
> Spravovaná identita se podporuje jenom v případě, že používáte sadu Azure Machine Learning SDK z virtuálního počítače Azure nebo pomocí Azure Machine Learning výpočetního clusteru. Použití spravované identity s výpočetním clusterem je aktuálně ve verzi Preview.

### <a name="managed-identity-with-a-vm"></a>Spravovaná identita s virtuálním počítačem

1. Povolte [na virtuálním počítači spravovanou identitu přiřazenou systémem pro prostředky Azure](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md#system-assigned-managed-identity).

1. V [Azure Portal](https://portal.azure.com)vyberte svůj pracovní prostor a pak vyberte __Access Control (IAM)__, __Přidat přiřazení role__ a vyberte __virtuální počítač__ v rozevíracím seznamu __přiřadit přístup k__ . Nakonec vyberte identitu svého virtuálního počítače.

1. Vyberte roli, kterou chcete přiřadit k této identitě. Například přispěvatel nebo vlastní role. Další informace najdete v tématu [řízení přístupu k prostředkům](how-to-assign-roles.md).

### <a name="managed-identity-with-compute-cluster"></a>Spravovaná identita s výpočetním clusterem

Další informace najdete v tématu [nastavení spravované identity pro výpočetní cluster](how-to-create-attach-compute-cluster.md#managed-identity).

<a id="interactive-authentication"></a>

## <a name="use-interactive-authentication"></a>Použít interaktivní ověřování

> [!IMPORTANT]
> Interaktivní ověřování používá prohlížeč a vyžaduje soubory cookie (včetně souborů cookie třetích stran). Pokud jste soubory cookie vypnuli, může se zobrazit chyba, například "Nemůžeme vás přihlásit". K této chybě může dojít také v případě, že jste povolili [Multi-Factor Authentication služby Azure AD](../active-directory/authentication/concept-mfa-howitworks.md).

Většina příkladů v dokumentaci a vzorcích používá interaktivní ověřování. Například při použití sady SDK jsou k dispozici dvě volání funkcí, která vás automaticky vyzve k vytvoření toku ověřování založeného na uživatelském rozhraní:

* Voláním `from_config()` funkce se vydá výzva.

    ```python
    from azureml.core import Workspace
    ws = Workspace.from_config()
    ```

    Funkce `from_config()` hledá soubor JSON obsahující informace o připojení k vašemu pracovnímu prostoru.

* Použití `Workspace` konstruktoru k poskytnutí informací o předplatném, skupině prostředků a pracovním prostoru se také zobrazí výzva k zadání interaktivního ověřování.

    ```python
    ws = Workspace(subscription_id="your-sub-id",
                  resource_group="your-resource-group-id",
                  workspace_name="your-workspace-name"
                  )
    ```

> [!TIP]
> Pokud máte přístup k více klientům, možná budete muset třídu naimportovat a explicitně definovat, který tenant cílíte. Volání konstruktoru pro `InteractiveLoginAuthentication` vás také vyzve k přihlášení podobným voláním uvedeným výše.
>
> ```python
> from azureml.core.authentication import InteractiveLoginAuthentication
> interactive_auth = InteractiveLoginAuthentication(tenant_id="your-tenant-id")
> ```

Při použití rozhraní příkazového řádku Azure CLI se `az login` k ověření relace CLI používá příkaz. Další informace najdete v tématu [Začínáme s Azure CLI](/cli/azure/get-started-with-azure-cli).

> [!TIP]
> Pokud sadu SDK používáte z prostředí, ve kterém jste předtím provedli interaktivní ověřování pomocí Azure CLI, můžete použít `AzureCliAuthentication` třídu k ověření v pracovním prostoru pomocí pověření uložených v mezipaměti rozhraní příkazového řádku:
>
> ```python
> from azureml.core.authentication import AzureCliAuthentication
> cli_auth = AzureCliAuthentication()
> ws = Workspace(subscription_id="your-sub-id",
>                resource_group="your-resource-group-id",
>                workspace_name="your-workspace-name",
>                auth=cli_auth
>                )
> ```

<a id="service-principal-authentication"></a>

## <a name="use-service-principal-authentication"></a>Použít ověřování instančního objektu

K ověření v pracovním prostoru ze sady SDK pomocí instančního objektu použijte `ServicePrincipalAuthentication` konstruktor třídy. Použijte hodnoty, které jste získali při vytváření poskytovatele služeb jako parametry. `tenant_id`Parametr se mapuje `tenantId` z výše, `service_principal_id` mapuje na `clientId` a `service_principal_password` mapuje na `clientSecret` .

```python
from azureml.core.authentication import ServicePrincipalAuthentication

sp = ServicePrincipalAuthentication(tenant_id="your-tenant-id", # tenantID
                                    service_principal_id="your-client-id", # clientId
                                    service_principal_password="your-client-secret") # clientSecret
```

`sp`Proměnná nyní obsahuje objekt ověřování, který používáte přímo v sadě SDK. Obecně je vhodné ukládat ID/tajné klíče použité výše v proměnných prostředí, jak je znázorněno v následujícím kódu. Ukládání do proměnných prostředí brání nechtěnému zkontrolování informací do úložiště GitHub.

```python
import os

sp = ServicePrincipalAuthentication(tenant_id=os.environ['AML_TENANT_ID'],
                                    service_principal_id=os.environ['AML_PRINCIPAL_ID'],
                                    service_principal_password=os.environ['AML_PRINCIPAL_PASS'])
```

Pro automatizované pracovní postupy, které běží v Pythonu a používají primárně sadu SDK, můžete použít tento objekt ve většině případů pro vaše ověřování. Následující kód se ověřuje ve vašem pracovním prostoru pomocí objektu auth, který jste vytvořili.

```python
from azureml.core import Workspace

ws = Workspace.get(name="ml-example",
                   auth=sp,
                   subscription_id="your-sub-id")
ws.get_details()
```

### <a name="use-a-service-principal-from-the-azure-cli"></a>Použití instančního objektu ze služby Azure CLI

Pro příkazy rozhraní příkazového řádku Azure můžete použít instanční objekt. Další informace najdete v tématu [přihlášení pomocí instančního objektu](/cli/azure/create-an-azure-service-principal-azure-cli?preserve-view=true&view=azure-cli-latest#sign-in-using-a-service-principal).

### <a name="use-a-service-principal-with-the-rest-api-preview"></a>Použití instančního objektu s REST API (Preview)

Instanční objekt se dá použít taky k ověření Azure Machine Learning [REST API](/rest/api/azureml/) (Preview). Použijete [tok udělení přihlašovacích údajů klienta](../active-directory/azuread-dev/v1-oauth2-client-creds-grant-flow.md)Azure Active Directory, který umožňuje volání mezi službami pro ověřování bez periferních procesů v automatizovaných pracovních postupech. Příklady jsou implementovány pomocí [knihovny ADAL](../active-directory/azuread-dev/active-directory-authentication-libraries.md) v Pythonu i Node.js, ale můžete také použít jakoukoli Open Source knihovnu, která podporuje OpenID Connect 1,0.

> [!NOTE]
> MSAL.js je novější knihovna než ADAL, ale ověřování pomocí služby nemůžete použít s přihlašovacími údaji klienta s MSAL.js, protože se primárně jedná o knihovnu na straně klienta určenou pro interaktivní nebo uživatelské ověřování vázané na konkrétního uživatele. Pro vytváření automatizovaných pracovních postupů s REST API doporučujeme použít ADAL, jak vidíte níže.

#### <a name="nodejs"></a>Node.js

Pomocí následujících kroků vygenerujte ověřovací token pomocí Node.js. Ve svém prostředí spusťte `npm install adal-node` . Pak použijte své `tenantId` služby, `clientId` a `clientSecret` z instančního objektu, který jste vytvořili v předchozích krocích, jako hodnoty pro vyhovující proměnné v následujícím skriptu.

```javascript
const adal = require('adal-node').AuthenticationContext;

const authorityHostUrl = 'https://login.microsoftonline.com/';
const tenantId = 'your-tenant-id';
const authorityUrl = authorityHostUrl + tenantId;
const clientId = 'your-client-id';
const clientSecret = 'your-client-secret';
const resource = 'https://management.azure.com/';

const context = new adal(authorityUrl);

context.acquireTokenWithClientCredentials(
  resource,
  clientId,
  clientSecret,
  (err, tokenResponse) => {
    if (err) {
      console.log(`Token generation failed due to ${err}`);
    } else {
      console.dir(tokenResponse, { depth: null, colors: true });
    }
  }
);
```

Proměnná `tokenResponse` je objekt, který obsahuje token a přidružená metadata, jako je například čas vypršení platnosti. Tokeny jsou platné 1 hodiny a lze je aktualizovat opětovným spuštěním stejného volání pro načtení nového tokenu. Následující fragment kódu je ukázková odpověď.

```javascript
{
    tokenType: 'Bearer',
    expiresIn: 3599,
    expiresOn: 2019-12-17T19:15:56.326Z,
    resource: 'https://management.azure.com/',
    accessToken: "random-oauth-token",
    isMRRT: true,
    _clientId: 'your-client-id',
    _authority: 'https://login.microsoftonline.com/your-tenant-id'
}
```

Pomocí `accessToken` vlastnosti načtěte ověřovací token. Příklady použití tokenu k volání rozhraní API najdete v [dokumentaci k REST API](https://github.com/microsoft/MLOps/tree/master/examples/AzureML-REST-API) .

#### <a name="python"></a>Python

Pomocí následujících kroků vygenerujte ověřovací token pomocí Pythonu. Ve svém prostředí spusťte `pip install adal` . Pak použijte své `tenantId` služby, `clientId` a `clientSecret` z instančního objektu, který jste vytvořili v předchozích krocích, jako hodnoty pro příslušné proměnné v následujícím skriptu.

```python
from adal import AuthenticationContext

client_id = "your-client-id"
client_secret = "your-client-secret"
resource_url = "https://login.microsoftonline.com"
tenant_id = "your-tenant-id"
authority = "{}/{}".format(resource_url, tenant_id)

auth_context = AuthenticationContext(authority)
token_response = auth_context.acquire_token_with_client_credentials("https://management.azure.com/", client_id, client_secret)
print(token_response)
```

Proměnná `token_response` je slovník, který obsahuje token a přidružená metadata, jako je například čas vypršení platnosti. Tokeny jsou platné 1 hodiny a lze je aktualizovat opětovným spuštěním stejného volání pro načtení nového tokenu. Následující fragment kódu je ukázková odpověď.

```python
{
    'tokenType': 'Bearer',
    'expiresIn': 3599,
    'expiresOn': '2019-12-17 19:47:15.150205',
    'resource': 'https://management.azure.com/',
    'accessToken': 'random-oauth-token',
    'isMRRT': True,
    '_clientId': 'your-client-id',
    '_authority': 'https://login.microsoftonline.com/your-tenant-id'
}
```

Použijte `token_response["accessToken"]` k načtení ověřovacího tokenu. Příklady použití tokenu k volání rozhraní API najdete v [dokumentaci k REST API](https://github.com/microsoft/MLOps/tree/master/examples/AzureML-REST-API) .

#### <a name="java"></a>Java

V jazyce Java načtěte nosný token pomocí standardního volání REST:

```java
String tenantId = "your-tenant-id";
String clientId = "your-client-id";
String clientSecret = "your-client-secret";
String resourceManagerUrl = "https://management.azure.com";

HttpRequest tokenAuthenticationRequest = tokenAuthenticationRequest(tenantId, clientId, clientSecret, resourceManagerUrl);

HttpClient client = HttpClient.newBuilder().build();
Gson gson = new Gson();
HttpResponse<String> response = client.send(request, HttpResponse.BodyHandlers.ofString());
if (response.statusCode == 200)
{
     body = gson.fromJson(body, AuthenticationBody.class);

    // ... etc ... 
}
// ... etc ...

static HttpRequest tokenAuthenticationRequest(String tenantId, String clientId, String clientSecret, String resourceManagerUrl){
    String authUrl = String.format("https://login.microsoftonline.com/%s/oauth2/token", tenantId);
    String clientIdParam = String.format("client_id=%s", clientId);
    String resourceParam = String.format("resource=%s", resourceManagerUrl);
    String clientSecretParam = String.format("client_secret=%s", clientSecret);

    String bodyString = String.format("grant_type=client_credentials&%s&%s&%s", clientIdParam, resourceParam, clientSecretParam);

    HttpRequest request = HttpRequest.newBuilder()
            .uri(URI.create(authUrl))
            .POST(HttpRequest.BodyPublishers.ofString(bodyString))
            .build();
    return request;
}

class AuthenticationBody {
    String access_token;
    String token_type;
    int expires_in;
    String scope;
    String refresh_token;
    String id_token;
    
    AuthenticationBody() {}
}
```

Předchozí kód by musel zpracovat výjimky a stavové kódy jiné než `200 OK` , ale zobrazuje vzor: 

- Pomocí ID klienta a tajného klíče ověřte, že by měl mít program přístup.
- Pomocí ID tenanta určete, kde `login.microsoftonline.com` se má hledat.
- Použití Azure Resource Manager jako zdroje autorizačního tokenu

## <a name="use-managed-identity-authentication"></a>Použít spravované ověřování identity

K ověření v pracovním prostoru z virtuálního počítače nebo výpočetního clusteru, který je nakonfigurovaný se spravovanou identitou, použijte `MsiAuthentication` třídu. Následující příklad ukazuje, jak použít tuto třídu k ověření v pracovním prostoru:

```python
from azureml.core.authentication import MsiAuthentication

msi_auth = MsiAuthentication()

ws = Workspace(subscription_id="your-sub-id",
                resource_group="your-resource-group-id",
                workspace_name="your-workspace-name",
                auth=msi_auth
                )
```

## <a name="next-steps"></a>Další kroky

* [Způsob používání tajných kódů při školení](how-to-use-secrets-in-runs.md).
* [Jak nakonfigurovat ověřování pro modely nasazené jako webovou službu](how-to-authenticate-web-service.md).
* [Využívání modelu služby Azure Machine Learning nasazeného jako webová služba](how-to-consume-web-service.md)
