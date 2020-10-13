---
title: Nastavení ověřování
titleSuffix: Azure Machine Learning
description: Naučte se, jak nastavit a nakonfigurovat ověřování pro různé prostředky a pracovní postupy v Azure Machine Learning. Existuje několik způsobů, jak nakonfigurovat a používat ověřování v rámci služby, od jednoduchých ověřování založeného na uživatelském rozhraní pro účely vývoje nebo testování, až po úplné Azure Active Directory ověřování instančního objektu.
services: machine-learning
author: cjgronlund
ms.author: cgronlun
ms.reviewer: larryfr
ms.service: machine-learning
ms.subservice: core
ms.date: 06/17/2020
ms.topic: conceptual
ms.custom: how-to, has-adal-ref, devx-track-js
ms.openlocfilehash: 486f026f0d9b325f8e17a040c69f9d3e1da9b359
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91729028"
---
# <a name="set-up-authentication-for-azure-machine-learning-resources-and-workflows"></a>Nastavení ověřování pro Azure Machine Learning prostředky a pracovní postupy


Naučte se ověřovat v pracovním prostoru Azure Machine Learning a modelech nasazených jako webové služby.

Obecně existují dva typy ověřování, které můžete používat s Azure Machine Learning:

* __Interaktivní__: účet můžete použít v Azure Active Directory k přímému ověření nebo k získání tokenu, který se používá k ověřování. Interaktivní ověřování se používá během experimentů a iterativního vývoje. Nebo kde chcete řídit přístup k prostředkům (například webové službě) na základě jednotlivých uživatelů.
* __Instanční objekt__: v Azure Active Directory vytvoříte hlavní účet služby a použijete ho k ověření nebo získání tokenu. Instanční objekt se používá v případě, že potřebujete automatizovaný proces ověřování ke službě bez nutnosti zásahu uživatele. Například skript průběžné integrace a nasazení, který na vlaky a testuje model pokaždé, když se kód školení změní. Můžete také použít instanční objekt k získání tokenu pro ověření webové služby, pokud nechcete, aby koncový uživatel služby ověřil. Nebo tam, kde se ověřování koncového uživatele neprovede přímo pomocí Azure Active Directory.

Bez ohledu na použitý typ ověřování se používá řízení přístupu na základě role (RBAC) k určení rozsahu úrovně přístupu k prostředkům. Například účet, který se používá k získání přístupového tokenu pro nasazený model, potřebuje jenom přístup pro čtení k pracovnímu prostoru. Další informace o RBAC najdete v tématu [Správa přístupu k pracovnímu prostoru Azure Machine Learning](how-to-assign-roles.md).

## <a name="prerequisites"></a>Požadavky

* Vytvořte [pracovní prostor Azure Machine Learning](how-to-manage-workspace.md).
* [Nakonfigurujte vývojové prostředí](how-to-configure-environment.md) pro instalaci Azure Machine Learning sady SDK nebo použijte [virtuální počítač s Azure Machine Learningm poznámkovým blokem](concept-azure-machine-learning-architecture.md#compute-instance) , který už je nainstalovaný s SDK.

## <a name="interactive-authentication"></a>Interaktivní ověřování

> [!IMPORTANT]
> Interaktivní ověřování používá prohlížeč a vyžaduje soubory cookie (včetně souborů cookie třetích stran). Pokud jste soubory cookie vypnuli, může se zobrazit chyba, například "Nemůžeme vás přihlásit". K této chybě může dojít také v případě, že jste povolili [Azure Multi-Factor Authentication](/azure/active-directory/authentication/concept-mfa-howitworks).

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

## <a name="service-principal-authentication"></a>Ověřování instančních objektů

Chcete-li použít ověřování instančního objektu (SP), musíte nejprve vytvořit aktualizaci SP a udělit jí přístup k vašemu pracovnímu prostoru. Jak bylo zmíněno dříve, pro řízení přístupu se používá řízení přístupu na základě role Azure (Azure RBAC), takže musíte také rozhodnout, jaký přístup k této aktualizaci chcete udělit.

> [!IMPORTANT]
> Při použití instančního objektu udělte mu __minimální přístup vyžadovaný pro úkol__ , ke kterému se používá. Například neudělíte vlastníkovi instančního objektu nebo přístup k přispěvateli, pokud se k němu používá přístup, který čte přístupový token pro nasazení webu.
>
> Důvodem pro udělení minimálního přístupu je, že instanční objekt používá k ověření heslo a heslo může být uloženo jako součást skriptu automatizace. Pokud je heslo nedostupné, má minimální přístup vyžadovaný pro konkrétní úkoly minimalizovat škodlivé použití SP.

Nejjednodušší způsob, jak vytvořit SP a udělit přístup k vašemu pracovnímu prostoru, je použití [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest&preserve-view=true). K vytvoření instančního objektu a udělení přístupu k vašemu pracovnímu prostoru použijte následující postup:

> [!NOTE]
> K provedení všech těchto kroků musíte být správcem předplatného.

1. Ověření u vašeho předplatného Azure:

    ```azurecli-interactive
    az login
    ```

    Pokud rozhraní příkazového řádku může spustit výchozí prohlížeč, udělá to a načte přihlašovací stránku. V opačném případě je nutné otevřít prohlížeč a postupovat podle pokynů v příkazovém řádku. Pokyny zahrnují procházení [https://aka.ms/devicelogin](https://aka.ms/devicelogin) a zadávání autorizačního kódu.

    [!INCLUDE [select-subscription](../../includes/machine-learning-cli-subscription.md)] 

    Další metody ověřování najdete v tématu [přihlášení pomocí Azure CLI](https://docs.microsoft.com/cli/azure/authenticate-azure-cli?view=azure-cli-latest&preserve-view=true).

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
    > Přístup vlastníka umožňuje instančnímu objektu v pracovním prostoru prakticky jakoukoli operaci. V tomto dokumentu se používá k demonstrování, jak udělit přístup; v produkčním prostředí doporučuje Microsoft udělit instančnímu objektu minimální přístup, který je potřeba k provedení role, pro kterou máte oprávnění. Další informace najdete v tématu [Správa přístupu k pracovnímu prostoru Azure Machine Learning](how-to-assign-roles.md).

    ```azurecli-interactive
    az ml workspace share -w your-workspace-name -g your-resource-group-name --user your-sp-object-id --role owner
    ```

    Toto volání nevyprodukuje žádný výstup při úspěchu.

### <a name="use-a-service-principal-from-the-sdk"></a>Použití instančního objektu ze sady SDK

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

Pro příkazy rozhraní příkazového řádku Azure můžete použít instanční objekt. Další informace najdete v tématu [přihlášení pomocí instančního objektu](https://docs.microsoft.com/cli/azure/create-an-azure-service-principal-azure-cli?view=azure-cli-latest&preserve-view=true#sign-in-using-a-service-principal).

### <a name="use-a-service-principal-with-the-rest-api-preview"></a>Použití instančního objektu s REST API (Preview)

Instanční objekt se dá použít taky k ověření Azure Machine Learning [REST API](https://docs.microsoft.com/rest/api/azureml/) (Preview). Použijete [tok udělení přihlašovacích údajů klienta](https://docs.microsoft.com/azure/active-directory/develop/v1-oauth2-client-creds-grant-flow)Azure Active Directory, který umožňuje volání mezi službami pro ověřování bez periferních procesů v automatizovaných pracovních postupech. Příklady jsou implementovány pomocí [knihovny ADAL](https://docs.microsoft.com/azure/active-directory/develop/active-directory-authentication-libraries) v Pythonu i Node.js, ale můžete také použít jakoukoli Open Source knihovnu, která podporuje OpenID Connect 1,0.

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

## <a name="web-service-authentication"></a>Ověřování webové služby

Nasazení modelu vytvořená nástrojem Azure Machine Learning poskytují dvě metody ověřování:

* **založené na klíčích**: ke ověřování webové služby se používá statický klíč.
* **založené na tokenech**: z pracovního prostoru se musí získat dočasný token, který se používá k ověření webové služby. Platnost tohoto tokenu vyprší po uplynutí určité doby a je nutné ji aktualizovat, aby bylo možné pokračovat v práci s webovou službou.

    > [!NOTE]
    > Ověřování založené na tokenech je dostupné jenom při nasazení do služby Azure Kubernetes.

### <a name="key-based-web-service-authentication"></a>Ověřování webové služby na základě klíčů

Webové služby nasazené ve službě Azure Kubernetes Service (AKS) mají ve výchozím nastavení *povolené* ověřování na základě klíčů. Ve výchozím nastavení mají nasazené služby Azure Container Instances (ACI) ověřování na základě klíčů *zakázané* , ale můžete je povolit nastavením `auth_enabled=True` při vytváření webové služby ACI. Následující kód je příkladem vytvoření konfigurace nasazení ACI s povoleným ověřováním pomocí klíče.

```python
from azureml.core.webservice import AciWebservice

aci_config = AciWebservice.deploy_configuration(cpu_cores = 1,
                                                memory_gb = 1,
                                                auth_enabled=True)
```

Pak můžete použít vlastní konfiguraci ACI v nasazení pomocí `Model` třídy.

```python
from azureml.core.model import Model, InferenceConfig


inference_config = InferenceConfig(entry_script="score.py",
                                   environment=myenv)
aci_service = Model.deploy(workspace=ws,
                       name="aci_service_sample",
                       models=[model],
                       inference_config=inference_config,
                       deployment_config=aci_config)
aci_service.wait_for_deployment(True)
```

Chcete-li načíst klíče ověřování, použijte `aci_service.get_keys()` . Chcete-li znovu vygenerovat klíč, použijte `regen_key()` funkci a předejte ji buď **primární** , nebo **sekundární**.

```python
aci_service.regen_key("Primary")
# or
aci_service.regen_key("Secondary")
```

Další informace o ověřování pro nasazený model najdete v tématu [Vytvoření klienta pro model nasazený jako webovou službu](how-to-consume-web-service.md).

### <a name="token-based-web-service-authentication"></a>Ověřování webové služby založené na tokenech

Pokud povolíte ověřování pomocí tokenu pro webovou službu, uživatelé musí pro přístup k této webové službě předložit Azure Machine Learning JSON Web Token. Platnost tokenu po zadané době vyprší a je potřeba ji obnovit, pokud chcete provádět další volání.

* Ověřování tokenu je **ve výchozím nastavení** při nasazení do služby Azure Kubernetes zakázané.
* Ověřování tokenu se při nasazení do Azure Container Instances **nepodporuje** .
* Ověřování tokenu **nelze použít současně s ověřováním na základě klíčů**.

K řízení ověřování tokenu použijte `token_auth_enabled` parametr při vytváření nebo aktualizaci nasazení:

```python
from azureml.core.webservice import AksWebservice
from azureml.core.model import Model, InferenceConfig

# Create the config
aks_config = AksWebservice.deploy_configuration()

#  Enable token auth and disable (key) auth on the webservice
aks_config = AksWebservice.deploy_configuration(token_auth_enabled=True, auth_enabled=False)

aks_service_name ='aks-service-1'

# deploy the model
aks_service = Model.deploy(workspace=ws,
                           name=aks_service_name,
                           models=[model],
                           inference_config=inference_config,
                           deployment_config=aks_config,
                           deployment_target=aks_target)

aks_service.wait_for_deployment(show_output = True)
```

Pokud je povoleno ověřování tokenu, můžete použít `get_token` metodu k načtení JSON web token (Jwt) a času vypršení platnosti tokenu:

> [!TIP]
> Pokud k získání tokenu použijete instanční objekt a chcete, aby měl minimální požadovaný přístup k načtení tokenu, přiřaďte ho k roli **čtenáře** pro daný pracovní prostor.

```python
token, refresh_by = aks_service.get_token()
print(token)
```

> [!IMPORTANT]
> Po uplynutí doby `refresh_by` tokenu je potřeba vyžádat si nový token. Pokud potřebujete aktualizovat tokeny mimo sadu Python SDK, jednu z možností je použít REST API s ověřováním instančního objektu pro pravidelné `service.get_token()` volání, jak je popsáno výše.
>
> Důrazně doporučujeme vytvořit pracovní prostor Azure Machine Learning ve stejné oblasti jako cluster služby Azure Kubernetes.
>
> K ověřování pomocí tokenu webová služba provede volání do oblasti, ve které je vytvořen Azure Machine Learning pracovní prostor. Pokud oblast pracovního prostoru není k dispozici, nebudete moci načíst token pro webovou službu, i když je váš cluster v jiné oblasti než pracovní prostor. Výsledkem je, že ověřování Azure AD není k dispozici, dokud nebude oblast pracovního prostoru znovu dostupná.
>
> Čím větší vzdálenost mezi oblastí vašeho clusteru a oblastí vašeho pracovního prostoru, tím déle bude trvat Načtení tokenu.

## <a name="next-steps"></a>Další kroky

* [Způsob používání tajných kódů při školení](how-to-use-secrets-in-runs.md).
* Proveďte [výuku a nasaďte model klasifikace imagí](tutorial-train-models-with-aml.md).
* [Využití modelu Azure Machine Learning nasazeného jako webové služby](how-to-consume-web-service.md).
