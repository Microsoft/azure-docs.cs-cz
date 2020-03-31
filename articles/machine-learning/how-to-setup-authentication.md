---
title: Nastavení ověřování
titleSuffix: Azure Machine Learning
description: Zjistěte, jak nastavit a nakonfigurovat ověřování pro různé prostředky a pracovní postupy v Azure Machine Learning. Existuje několik způsobů konfigurace a použití ověřování v rámci služby, od jednoduchého ověření na základě ui pro účely vývoje nebo testování až po úplné ověřování primárníslužby Azure Active Directory.
services: machine-learning
author: trevorbye
ms.author: trbye
ms.reviewer: trbye
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 12/17/2019
ms.openlocfilehash: fcaa7a0c44851d6b48b40b01af4c8ec992c330b8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79283534"
---
# <a name="set-up-authentication-for-azure-machine-learning-resources-and-workflows"></a>Nastavení ověřování pro prostředky a pracovní postupy Azure Machine Learning
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

V tomto článku se dozvíte, jak nastavit a nakonfigurovat ověřování pro různé prostředky a pracovní postupy v Azure Machine Learning. Existuje několik způsobů, jak ověřit službu, od jednoduchého ověření na základě ui pro účely vývoje nebo testování až po úplné ověřování primárníslužby Azure Active Directory. Tento článek také vysvětluje rozdíly ve způsobu ověřování webových služeb a také jak ověřit rozhraní REST API Azure Machine Learning.

Toto postup ukazuje, jak provést následující úkoly:

* Použití interaktivního ověřování ui pro testování a vývoj
* Nastavení ověřování instančního objektu
* Ověřování pracovního prostoru
* Získání tokenů typu nosiče OAuth2.0 pro rozhraní REST api Azure Machine Learning
* Principy ověřování webové služby

Obecný [přehled](concept-enterprise-security.md) zabezpečení a ověřování v rámci Azure Machine Learning najdete v článku konceptu.

## <a name="prerequisites"></a>Požadavky

* Vytvořte [pracovní prostor Azure Machine Learning](how-to-manage-workspace.md).
* [Nakonfigurujte své vývojové prostředí](how-to-configure-environment.md) tak, aby bylo nainstalováno sada Azure Machine Learning SDK, nebo použijte [virtuální počítač ový počítač Azure Machine Learning s](concept-azure-machine-learning-architecture.md#compute-instance) již nainstalovanou sadou SDK.

## <a name="interactive-authentication"></a>Interaktivní ověřování

Většina příkladů v dokumentaci k této službě používá interaktivní ověřování v poznámkových blocích Jupyter jako jednoduchou metodu testování a demonstrace. Jedná se o lehký způsob, jak otestovat, co stavíte. Existují dvě volání funkcí, která vás automaticky vyzve pomocí toku ověřování založeného na ui.

Volání `from_config()` funkce vydá výzvu.

```python
from azureml.core import Workspace
ws = Workspace.from_config()
```

Funkce `from_config()` vyhledá soubor JSON obsahující informace o připojení pracovního prostoru. Můžete také zadat podrobnosti o `Workspace` připojení explicitně pomocí konstruktoru, který bude také výzvou k interaktivnímu ověřování. Oba hovory jsou ekvivalentní.

```python
ws = Workspace(subscription_id="your-sub-id",
               resource_group="your-resource-group-id",
               workspace_name="your-workspace-name"
              )
```

Pokud máte přístup k více klientům, budete muset importovat třídu a explicitně definovat, na jakého klienta cílíte. Volání konstruktoru `InteractiveLoginAuthentication` pro také vyzve k přihlášení podobné volání výše.

```python
from azureml.core.authentication import InteractiveLoginAuthentication
interactive_auth = InteractiveLoginAuthentication(tenant_id="your-tenant-id")
```

Interaktivní ověřování, které je užitečné pro testování a učení, vám nepomůže při vytváření automatizovaných nebo bezhlavých pracovních postupů. Nastavení ověřování instančního objektu je nejlepší přístup pro automatizované procesy, které používají sadu SDK.

## <a name="set-up-service-principal-authentication"></a>Nastavení ověřování instančního objektu

Tento proces je nezbytný pro povolení ověřování, které je odděleno od konkrétního přihlášení uživatele, což umožňuje ověření azure machine learningu Python SDK v automatizovaných pracovních postupech. Ověřování instančního objektu také umožní [ověření rozhraní REST API](#azure-machine-learning-rest-api-auth).

Chcete-li nastavit ověřování o objektu zabezpečení služby, nejprve vytvořte registraci aplikace ve službě Azure Active Directory a pak udělte přístup k pracovnímu prostoru ML na základě rolí aplikace. Nejjednodušší způsob, jak dokončit toto nastavení je prostřednictvím [Azure Cloud Shell](https://azure.microsoft.com/features/cloud-shell/) na webu Azure Portal. Po přihlášení k portálu `>_` otevřete prostředí kliknutím na ikonu v pravém horním rohu stránky poblíž svého jména.

Pokud jste cloudové prostředí ještě nepoužili ve svém účtu Azure, budete muset vytvořit prostředek účtu úložiště pro ukládání všech souborů, které jsou zapsány. Obecně platí, že tento účet úložiště bude mít zanedbatelné měsíční náklady. Kromě toho nainstalujte rozšíření strojového učení, pokud jste ho dříve nepoužili pomocí následujícího příkazu.

```azurecli-interactive
az extension add -n azure-cli-ml
```

> [!NOTE]
> Chcete-li provést následující kroky, musíte být správcem předplatného.

Dále spusťte následující příkaz k vytvoření instančního objektu. Dejte mu jméno, v tomto případě **ml-auth**.

```azurecli-interactive
az ad sp create-for-rbac --sdk-auth --name ml-auth
```

Výstup bude JSON podobný následujícímu. Poznamenejte `clientId` `clientSecret`si `tenantId` pole , a pole, protože je budete potřebovat pro další kroky v tomto článku.

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

Dále spusťte následující příkaz, abyste získali podrobnosti o `clientId` instančním objektu, který jste právě vytvořili, pomocí hodnoty shora jako vstupu do parametru. `--id`

```azurecli-interactive
az ad sp show --id your-client-id
```

Následuje zjednodušený příklad výstupu JSON z příkazu. Vezměte na `objectId` vědomí pole, protože budete potřebovat jeho hodnotu pro další krok.

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

Dále pomocí následujícího příkazu přiřaďte primární přístup k pracovnímu prostoru strojového učení. Budete potřebovat název pracovního prostoru a jeho `-w` název `-g` skupiny prostředků pro parametry a. Pro `--user` parametr použijte `objectId` hodnotu z předchozího kroku. Parametr `--role` umožňuje nastavit přístupovou roli pro instanční objekt a obecně budete používat **vlastníka** nebo **přispěvatele**. Oba mají přístup pro zápis do existujících prostředků, jako jsou výpočetní clustery a úložiště dat, ale pouze **vlastník** může zřídit tyto prostředky. 

```azurecli-interactive
az ml workspace share -w your-workspace-name -g your-resource-group-name --user your-sp-object-id --role owner
```

Toto volání nevytváří žádný výstup, ale nyní máte pro pracovní prostor nastaveno ověření instančního objektu.

## <a name="authenticate-to-your-workspace"></a>Ověření pracovního prostoru

Nyní, když máte povoleno ověření instančního objektu, můžete ověřit do pracovního prostoru v sadě SDK bez fyzického přihlášení jako uživatel. Použijte `ServicePrincipalAuthentication` konstruktor třídy a jako parametry použijte hodnoty, které jste získali z předchozích kroků. Parametr `tenant_id` mapuje `tenantId` shora, `service_principal_id` `clientId`mapuje `service_principal_password` na `clientSecret`a mapuje na .

```python
from azureml.core.authentication import ServicePrincipalAuthentication

sp = ServicePrincipalAuthentication(tenant_id="your-tenant-id", # tenantID
                                    service_principal_id="your-client-id", # clientId
                                    service_principal_password="your-client-secret") # clientSecret
```

Proměnná `sp` nyní obsahuje ověřovací objekt, který používáte přímo v sadě SDK. Obecně je vhodné uložit id/tajné kódy použité výše v proměnných prostředí, jak je znázorněno v následujícím kódu.

```python
import os 

sp = ServicePrincipalAuthentication(tenant_id=os.environ['AML_TENANT_ID'],
                                    service_principal_id=os.environ['AML_PRINCIPAL_ID'],
                                    service_principal_password=os.environ['AML_PRINCIPAL_PASS'])
```

Pro automatizované pracovní postupy, které běží v Pythonu a používají sdk především, můžete použít tento objekt jako je ve většině případů pro vaše ověřování. Následující kód se ověřuje do pracovního prostoru pomocí objektu ověření, který jste právě vytvořili.

```python
from azureml.core import Workspace

ws = Workspace.get(name="ml-example", 
                   auth=sp,
                   subscription_id="your-sub-id")
ws.get_details()
```

## <a name="azure-machine-learning-rest-api-auth"></a>Azure Machine Learning REST API auth

Instanční objekt vytvořený ve výše uvedených krocích lze také použít k ověření rozhraní [REST api](https://docs.microsoft.com/rest/api/azureml/)Azure Machine Learning . Používáte [tok udělení pověření klienta](https://docs.microsoft.com/azure/active-directory/develop/v1-oauth2-client-creds-grant-flow)Azure Active Directory , který umožňuje volání služby ke službě pro bezobslužné ověřování v automatizovaných pracovních postupech. Příklady jsou implementovány s [knihovnou ADAL](https://docs.microsoft.com/azure/active-directory/develop/active-directory-authentication-libraries) v Pythonu i Node.js, ale můžete také použít libovolnou open source knihovnu, která podporuje OpenID Connect 1.0. 

> [!NOTE]
> MSAL.js je novější knihovna než ADAL, ale nelze provést ověřování mezi službami pomocí pověření klienta s MSAL.js, protože se jedná především o knihovnu na straně klienta určenou pro interaktivní ověřování uživatelského rozhraní vázané na konkrétního uživatele. Doporučujeme používat ADAL, jak je znázorněno níže, k vytvoření automatizovaných pracovních postupů s rozhraním REST API.

### <a name="nodejs"></a>Node.js

Pomocí následujících kroků vygenerujte token ověřování pomocí souboru Node.js. Ve vašem prostředí `npm install adal-node`spusťte . Potom použijte `tenantId`váš `clientId`, `clientSecret` a z instančního objektu, který jste vytvořili v krocích výše, jako hodnoty pro odpovídající proměnné v následujícím skriptu.

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

Proměnná `tokenResponse` je objekt, který obsahuje token a přidružená metadata, jako je například čas vypršení platnosti. Tokeny jsou platné po dobu 1 hodiny a lze je aktualizovat spuštěním stejného volání znovu načíst nový token. Následuje ukázková odpověď.

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

Pomocí `accessToken` vlastnosti načíst token auth. Najdete [v dokumentaci k rozhraní REST API](https://github.com/microsoft/MLOps/tree/master/examples/AzureML-REST-API) příklady o tom, jak pomocí tokenu k volání rozhraní API.

### <a name="python"></a>Python 

Pomocí následujících kroků vygenerujte token ověřování pomocí Pythonu. Ve vašem prostředí `pip install adal`spusťte . Potom použijte `tenantId`váš `clientId`, `clientSecret` a z instančního objektu, který jste vytvořili v krocích výše, jako hodnoty pro příslušné proměnné v následujícím skriptu.

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

Proměnná `token_response` je slovník, který obsahuje token a přidružená metadata, jako je například čas vypršení platnosti. Tokeny jsou platné po dobu 1 hodiny a lze je aktualizovat spuštěním stejného volání znovu načíst nový token. Následuje ukázková odpověď.

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

Slouží `token_response["accessToken"]` k načtení tokenu ověřování. Najdete [v dokumentaci k rozhraní REST API](https://github.com/microsoft/MLOps/tree/master/examples/AzureML-REST-API) příklady o tom, jak pomocí tokenu k volání rozhraní API.

## <a name="web-service-authentication"></a>Ověřování webové služby

Webové služby v Azure Machine Learning používají jiný způsob ověřování, než jak je popsáno výše. Nejjednodušší způsob, jak ověřit nasazené webové služby je použití **ověřování na základě klíče**, který generuje statické klíče ověřování typu nosiče, které není nutné aktualizovat. Pokud potřebujete pouze ověření na nasazenou webovou službu, není nutné nastavit ověřování principu služby, jak je uvedeno výše.

Webové služby nasazené ve službě Azure Kubernetes mají ve výchozím nastavení *povolené* auth založené na klíčích. Azure Container Instance nasazené služby mají *uzeniny* založené na klíči `auth_enabled=True`zakázáno ve výchozím nastavení, ale můžete povolit nastavením při vytváření webové služby ACI. Následuje příklad vytvoření konfigurace nasazení ACI s povoleným authem založeným na klíči.

```python
from azureml.core.webservice import AciWebservice

aci_config = AciWebservice.deploy_configuration(cpu_cores = 1,
                                                memory_gb = 1,
                                                auth_enabled=True)
```

Potom můžete použít vlastní konfiguraci ACI `Model` v nasazení pomocí třídy.

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

Chcete-li načíst auth klíče, použijte `aci_service.get_keys()`. Chcete-li znovu vygenerovat klíč, použijte `regen_key()` funkci a předajte **primární** nebo **sekundární**.

```python
aci_service.regen_key("Primary")
# or
aci_service.regen_key("Secondary")
```

Webové služby také podporují ověřování na základě tokenů, ale jenom pro nasazení služby Azure Kubernetes. Další informace o ověřování najdete v [návodu ke](how-to-consume-web-service.md) využívání webových služeb.

### <a name="token-based-web-service-authentication"></a>Ověřování webové služby založené na tokenech

Když povolíte ověřování tokenů pro webovou službu, uživatelé musí předložit Webový token Azure Machine Learning JSON webové službě, aby k ní měli přístup. Platnost tokenu vyprší po zadaném časovém rámci a je třeba jej aktualizovat, aby bylo možné pokračovat v volání.

* Ověřování tokenů je **ve výchozím nastavení zakázáno** při nasazení do služby Azure Kubernetes.
* Ověřování tokenů **není podporované** při nasazení do instancí kontejneru Azure.

Chcete-li řídit ověřování `token_auth_enabled` tokenů, použijte parametr při vytváření nebo aktualizaci nasazení.

Pokud je povoleno ověřování tokenu, můžete použít metodu `get_token` k načtení webového tokenu JSON (JWT) a doby vypršení platnosti tohoto tokenu:

```python
token, refresh_by = service.get_token()
print(token)
```

> [!IMPORTANT]
> Budete muset požádat o nový token po `refresh_by` uplynutí doby tokenu. Pokud potřebujete aktualizovat tokeny mimo Python SDK, jednou z možností je použití rozhraní REST API `service.get_token()` s ověřováním instančního objektu pravidelně volat, jak je popsáno dříve.
>
> Důrazně doporučujeme vytvořit pracovní prostor Azure Machine Learning ve stejné oblasti jako cluster služby Azure Kubernetes. 
>
> Chcete-li ověřit pomocí tokenu, webová služba provede volání do oblasti, ve které se vytvoří pracovní prostor Azure Machine Learning. Pokud oblast vašeho pracovního prostoru není k dispozici, nebudete moct načíst token pro webovou službu, a to ani v případě, že se váš cluster nachází v jiné oblasti než váš pracovní prostor. Výsledkem je, že ověřování Azure AD není k dispozici, dokud oblast vašeho pracovního prostoru je znovu k dispozici. 
>
> Také, čím větší je vzdálenost mezi oblastí clusteru a oblastí pracovního prostoru, tím déle bude trvat načtení tokenu.

## <a name="next-steps"></a>Další kroky

* [Trénování a nasazování modelu klasifikace obrázků](tutorial-train-models-with-aml.md).
* [Využijte model Azure Machine Learning nasazený jako webová služba](how-to-consume-web-service.md).
