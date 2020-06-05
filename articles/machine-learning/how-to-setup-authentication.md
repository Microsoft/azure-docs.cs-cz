---
title: Nastavení ověřování
titleSuffix: Azure Machine Learning
description: Naučte se, jak nastavit a nakonfigurovat ověřování pro různé prostředky a pracovní postupy v Azure Machine Learning. Existuje několik způsobů, jak nakonfigurovat a používat ověřování v rámci služby, od jednoduchých ověřování založeného na uživatelském rozhraní pro účely vývoje nebo testování, až po úplné Azure Active Directory ověřování instančního objektu.
services: machine-learning
author: trevorbye
ms.author: trbye
ms.reviewer: trbye
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.date: 12/17/2019
ms.custom: has-adal-ref
ms.openlocfilehash: 57160088c283b1f2c686429168cc858fee58324a
ms.sourcegitcommit: b55d1d1e336c1bcd1c1a71695b2fd0ca62f9d625
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/04/2020
ms.locfileid: "84433122"
---
# <a name="set-up-authentication-for-azure-machine-learning-resources-and-workflows"></a>Nastavení ověřování pro Azure Machine Learning prostředky a pracovní postupy
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

V tomto článku se dozvíte, jak nastavit a nakonfigurovat ověřování pro různé prostředky a pracovní postupy v Azure Machine Learning. Existuje několik způsobů, jak ověřit službu, od jednoduchého ověřování založeného na uživatelském rozhraní pro účely vývoje nebo testování až po úplné Azure Active Directory ověřování instančního objektu. Tento článek také vysvětluje rozdíly v tom, jak funguje ověřování webové služby, a jak ověřit REST API Azure Machine Learning.

V této části se dozvíte, jak provádět následující úlohy:

* Použít interaktivní ověřování uživatelského rozhraní pro testování a vývoj
* Nastavení ověřování objektu služby
* Ověřování ve vašem pracovním prostoru
* Získat tokeny nosného typu OAuth 2.0 pro Azure Machine Learning REST API
* Principy ověřování webové služby

Obecný přehled zabezpečení a ověřování v rámci Azure Machine Learning najdete v [článku o konceptu](concept-enterprise-security.md) .

## <a name="prerequisites"></a>Požadavky

* Vytvořte [pracovní prostor Azure Machine Learning](how-to-manage-workspace.md).
* [Nakonfigurujte vývojové prostředí](how-to-configure-environment.md) pro instalaci Azure Machine Learning sady SDK nebo použijte [virtuální počítač s Azure Machine Learningm poznámkovým blokem](concept-azure-machine-learning-architecture.md#compute-instance) , který už je nainstalovaný s SDK.

## <a name="interactive-authentication"></a>Interaktivní ověřování

Většina příkladů v dokumentaci pro tuto službu používá interaktivní ověřování v poznámkových blocích Jupyter jako jednoduchou metodu pro testování a ukázku. Toto je jednoduchý způsob, jak otestovat, co vytváříte. Existují dvě volání funkcí, která vás automaticky vyzve k vytvoření toku ověřování založeného na uživatelském rozhraní.

Voláním `from_config()` funkce se vydá výzva.

```python
from azureml.core import Workspace
ws = Workspace.from_config()
```

`from_config()`Funkce vyhledá soubor JSON, který obsahuje informace o připojení k pracovnímu prostoru. Podrobnosti o připojení lze také zadat explicitně pomocí `Workspace` konstruktoru, který bude také vyzván k interaktivnímu ověření. Obě volání jsou ekvivalentní.

```python
ws = Workspace(subscription_id="your-sub-id",
               resource_group="your-resource-group-id",
               workspace_name="your-workspace-name"
              )
```

Pokud máte přístup k více klientům, možná budete muset třídu naimportovat a explicitně definovat, který tenant cílíte. Volání konstruktoru pro `InteractiveLoginAuthentication` vás také vyzve k přihlášení podobným voláním uvedeným výše.

```python
from azureml.core.authentication import InteractiveLoginAuthentication
interactive_auth = InteractiveLoginAuthentication(tenant_id="your-tenant-id")
```

I když je to užitečné pro testování a učení, interaktivní ověřování vám pomůže vytvářet automatizované nebo nepracovní postupy. Nastavení ověřování instančního objektu je nejlepším řešením pro automatizované procesy, které používají sadu SDK.

## <a name="set-up-service-principal-authentication"></a>Nastavení ověřování objektu služby

Tento postup je nezbytný pro povolení ověřování, které je odděleno od přihlášení konkrétního uživatele, což vám umožní ověřit Azure Machine Learning Python SDK v automatizovaných pracovních postupech. Ověřování instančního objektu vám taky umožní [ověřit REST API](#azure-machine-learning-rest-api-auth).

Pokud chcete nastavit ověřování instančního objektu, nejdřív vytvořte registraci aplikace v Azure Active Directory a potom udělte vašemu pracovnímu prostoru ML přístup na základě role vaší aplikace. Nejjednodušší způsob, jak toto nastavení dokončit, je [Azure Cloud Shell](https://azure.microsoft.com/features/cloud-shell/) v Azure Portal. Po přihlášení na portál klikněte na `>_` ikonu v pravém horním rohu stránky poblíž jména a otevřete prostředí.

Pokud jste Cloud Shell nepoužívali před účtem Azure, budete muset vytvořit prostředek účtu úložiště pro ukládání souborů, které jsou zapsány. Obecně platí, že tento účet úložiště bude mít zanedbatelné měsíční náklady. Kromě toho nainstalujte rozšíření Machine Learning, pokud jste ho předtím nepoužili pomocí následujícího příkazu.

```azurecli-interactive
az extension add -n azure-cli-ml
```

> [!NOTE]
> K provedení následujících kroků musíte být správcem předplatného.

Dále spusťte následující příkaz k vytvoření instančního objektu. V tomto případě se v tomto případě dá v **ml ověřování**název.

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

Potom spuštěním následujícího příkazu Získejte podrobné informace o instančním objektu, který jste právě vytvořili, a použijte `clientId` hodnotu uvedenou výše jako vstup `--id` parametru.

```azurecli-interactive
az ad sp show --id your-client-id
```

Následuje zjednodušený příklad výstupu JSON z příkazu. Poznamenejte si `objectId` pole, jak budete potřebovat jeho hodnotu pro další krok.

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

Dále pomocí následujícího příkazu přiřaďte k pracovnímu prostoru Machine Learning přístup k hlavnímu objektu služby. Budete potřebovat název vašeho pracovního prostoru a jeho název skupiny prostředků pro `-w` parametry a v `-g` uvedeném pořadí. Pro `--user` parametr použijte `objectId` hodnotu z předchozího kroku. `--role`Parametr vám umožní nastavit roli přístupu pro instanční objekt a obecně budete používat buď **vlastníka** , nebo **přispěvatele**. Oba mají přístup pro zápis ke stávajícím prostředkům, jako jsou výpočetní clustery a úložiště dat, ale můžou tyto prostředky zřídit jenom **vlastník** .

```azurecli-interactive
az ml workspace share -w your-workspace-name -g your-resource-group-name --user your-sp-object-id --role owner
```

Toto volání nevyprodukuje žádný výstup, ale nyní máte nastaveno ověřování instančního objektu pro váš pracovní prostor.

## <a name="authenticate-to-your-workspace"></a>Ověření v pracovním prostoru

Když teď máte povolený instanční objekt, můžete ho ověřit v sadě SDK bez toho, aby se fyzicky přihlásil jako uživatel. Použijte `ServicePrincipalAuthentication` konstruktor třídy a použijte hodnoty, které jste získali z předchozích kroků jako parametry. `tenant_id`Parametr se mapuje `tenantId` z výše, `service_principal_id` mapuje na `clientId` a `service_principal_password` mapuje na `clientSecret` .

```python
from azureml.core.authentication import ServicePrincipalAuthentication

sp = ServicePrincipalAuthentication(tenant_id="your-tenant-id", # tenantID
                                    service_principal_id="your-client-id", # clientId
                                    service_principal_password="your-client-secret") # clientSecret
```

`sp`Proměnná nyní obsahuje objekt ověřování, který používáte přímo v sadě SDK. Obecně je vhodné ukládat ID/tajné klíče použité výše v proměnných prostředí, jak je znázorněno v následujícím kódu.

```python
import os

sp = ServicePrincipalAuthentication(tenant_id=os.environ['AML_TENANT_ID'],
                                    service_principal_id=os.environ['AML_PRINCIPAL_ID'],
                                    service_principal_password=os.environ['AML_PRINCIPAL_PASS'])
```

Pro automatizované pracovní postupy, které běží v Pythonu a používají primárně sadu SDK, můžete použít tento objekt ve většině případů pro vaše ověřování. Následující kód se ověří do vašeho pracovního prostoru pomocí objektu auth, který jste právě vytvořili.

```python
from azureml.core import Workspace

ws = Workspace.get(name="ml-example",
                   auth=sp,
                   subscription_id="your-sub-id")
ws.get_details()
```

## <a name="azure-machine-learning-rest-api-auth"></a>Ověřování REST API Azure Machine Learning

Instanční objekt vytvořený v krocích výše se dá použít taky k ověření [REST API](https://docs.microsoft.com/rest/api/azureml/)Azure Machine Learning. Použijete [tok udělení přihlašovacích údajů klienta](https://docs.microsoft.com/azure/active-directory/develop/v1-oauth2-client-creds-grant-flow)Azure Active Directory, který umožňuje volání mezi službami pro ověřování bez periferních procesů v automatizovaných pracovních postupech. Příklady jsou implementovány pomocí [knihovny ADAL](https://docs.microsoft.com/azure/active-directory/develop/active-directory-authentication-libraries) v Pythonu a Node. js, ale můžete také použít jakoukoli Open Source knihovnu, která podporuje OpenID Connect 1,0.

> [!NOTE]
> MSAL. js je novější knihovna než ADAL, ale ověřování pomocí služby nemůžete použít s přihlašovacími údaji klienta v MSAL. js, protože se primárně jedná o knihovnu na straně klienta určenou pro interaktivní nebo uživatelské ověřování vázané na konkrétního uživatele. Pro vytváření automatizovaných pracovních postupů s REST API doporučujeme použít ADAL, jak vidíte níže.

### <a name="nodejs"></a>Node.js

Pomocí následujících kroků vygenerujte ověřovací token pomocí Node. js. Ve svém prostředí spusťte `npm install adal-node` . Pak použijte své `tenantId` služby, `clientId` a `clientSecret` z instančního objektu, který jste vytvořili v předchozích krocích, jako hodnoty pro vyhovující proměnné v následujícím skriptu.

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

Proměnná `tokenResponse` je objekt, který obsahuje token a přidružená metadata, jako je například čas vypršení platnosti. Tokeny jsou platné 1 hodiny a lze je aktualizovat opětovným spuštěním stejného volání pro načtení nového tokenu. Následuje ukázková odpověď.

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

### <a name="python"></a>Python

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

Proměnná `token_response` je slovník, který obsahuje token a přidružená metadata, jako je například čas vypršení platnosti. Tokeny jsou platné 1 hodiny a lze je aktualizovat opětovným spuštěním stejného volání pro načtení nového tokenu. Následuje ukázková odpověď.

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

Webové služby v Azure Machine Learning používají jiný vzor ověřování, než je popsáno výše. Nejjednodušší způsob, jak provést ověření pro nasazené webové služby, je použití **ověřování založeného na klíčích**, které generuje statické ověřovací klíče typu nosiče, které není nutné aktualizovat. Pokud potřebujete provést ověření pouze pro nasazenou webovou službu, není nutné nastavovat zásady služby, jak je uvedeno výše.

Webové služby nasazené ve službě Azure Kubernetes mají ve výchozím nastavení *povolené* ověřování pomocí klíčů. Azure Container Instances nasazené služby mají ve výchozím nastavení *zakázané* ověřování na základě klíčů, ale můžete je povolit nastavením `auth_enabled=True` při vytváření webové služby ACI. Následuje příklad vytvoření konfigurace nasazení ACI s povoleným ověřováním pomocí klíče.

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

Webové služby také podporují ověřování na základě tokenů, ale jenom pro nasazení služby Azure Kubernetes. Další informace o ověřování najdete v tématu [postup](how-to-consume-web-service.md) při využívání webových služeb.

### <a name="token-based-web-service-authentication"></a>Ověřování webové služby založené na tokenech

Pokud povolíte ověřování pomocí tokenu pro webovou službu, uživatelé musí pro přístup k této webové službě předložit Azure Machine Learning JSON Web Token. Platnost tokenu vyprší po zadaném časovém rámci a je nutné ji aktualizovat, aby bylo možné pokračovat v volání.

* Ověřování tokenu je **ve výchozím nastavení** při nasazení do služby Azure Kubernetes zakázané.
* Ověřování tokenu se při nasazení do Azure Container Instances **nepodporuje** .

K řízení ověřování tokenu použijte `token_auth_enabled` parametr při vytváření nebo aktualizaci nasazení.

Pokud je povoleno ověřování tokenu, můžete použít `get_token` metodu k načtení JSON web token (Jwt) a času vypršení platnosti tokenu:

```python
token, refresh_by = service.get_token()
print(token)
```

> [!IMPORTANT]
> Po čase tokenu budete muset požádat o nový token `refresh_by` . Pokud potřebujete aktualizovat tokeny mimo sadu Python SDK, jednu z možností je použít REST API s ověřováním instančního objektu pro pravidelné `service.get_token()` volání, jak je popsáno výše.
>
> Důrazně doporučujeme vytvořit pracovní prostor Azure Machine Learning ve stejné oblasti jako cluster služby Azure Kubernetes.
>
> K ověřování pomocí tokenu webová služba provede volání do oblasti, ve které je vytvořen Azure Machine Learning pracovní prostor. Pokud oblast pracovního prostoru není k dispozici, nebudete moci načíst token pro webovou službu, i když je váš cluster v jiné oblasti než pracovní prostor. Výsledkem je, že ověřování Azure AD není k dispozici, dokud nebude oblast pracovního prostoru znovu dostupná.
>
> Čím větší vzdálenost mezi oblastí vašeho clusteru a oblastí vašeho pracovního prostoru, tím déle bude trvat Načtení tokenu.

## <a name="next-steps"></a>Další kroky

* Proveďte [výuku a nasaďte model klasifikace imagí](tutorial-train-models-with-aml.md).
* [Využití modelu Azure Machine Learning nasazeného jako webové služby](how-to-consume-web-service.md).
