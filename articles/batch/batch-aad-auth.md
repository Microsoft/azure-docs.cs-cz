---
title: Použití Azure Active Directory k ověřování řešení služby Azure Batch | Dokumentace Microsoftu
description: Služba batch podporuje Azure AD pro ověřování ze služby Batch.
services: batch
documentationcenter: .net
author: dlepow
manager: jeconnoc
editor: ''
tags: ''
ms.assetid: ''
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 04/18/2018
ms.author: danlep
ms.openlocfilehash: 1347d3aac01c62040c164eee22c7a905b07e8236
ms.sourcegitcommit: 6f59cdc679924e7bfa53c25f820d33be242cea28
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2018
ms.locfileid: "48816954"
---
# <a name="authenticate-batch-service-solutions-with-active-directory"></a>Ověřování řešení služby Batch pomocí Active Directory

Azure Batch podporuje ověřování s [Azure Active Directory][aad_about] (Azure AD). Azure AD je Microsoftu pro více tenantů cloudový adresář a služba pro správu identit. Azure samotné služby Azure AD použije k ověření své zákazníky, správci služeb a organizační uživatele.

Při používání ověřování Azure AD pomocí služby Azure Batch, můžete se ověřit jedním ze dvou způsobů:

- S použitím **integrované ověřování** k ověření uživatele, který pracuje s aplikací. Aplikace prostřednictvím integrovaného ověřování přihlašovacích údajů uživatele shromažďuje a používá tyto přihlašovací údaje k ověření přístupu k prostředkům Batch.
- Pomocí **instanční objekt služby** k ověření bezobslužných aplikací. Instanční objekt služby definuje zásady a oprávnění pro aplikaci k reprezentaci při přístupu k prostředkům v době běhu aplikace.

Další informace o službě Azure AD, najdete v článku [Azure Active Directory, dokumentace ke službě](https://docs.microsoft.com/azure/active-directory/).

## <a name="endpoints-for-authentication"></a>Koncové body pro ověřování

K ověření aplikace Batch s využitím Azure AD, budete muset zahrnout některé známé koncových bodů ve vašem kódu.

### <a name="azure-ad-endpoint"></a>Koncový bod Azure AD

Základní třídy je koncového bodu autority Azure AD:

`https://login.microsoftonline.com/`

K ověření ve službě Azure AD, můžete použít tento koncový bod spolu s ID tenanta (ID adresáře). ID tenanta identifikuje tenanta Azure AD pro účely ověření. Pokud chcete načíst ID tenanta, postupujte podle kroků uvedených v [získání ID tenanta pro Azure Active Directory](#get-the-tenant-id-for-your-active-directory):

`https://login.microsoftonline.com/<tenant-id>`

> [!NOTE] 
> Při ověřování pomocí instančního objektu, je vyžadován koncový bod specifickým pro tenanta. 
> 
> Koncový bod specifickým pro tenanta je volitelný při ověřování pomocí integrovaného ověřování, ale doporučujeme. Však můžete také použít běžné koncového bodu Azure AD. Společný koncový bod poskytuje obecný pověření shromažďování rozhraní, když není k dispozici konkrétního tenanta. Společný koncový bod je `https://login.microsoftonline.com/common`.
>
>

Další informace o koncových bodech služby Azure AD najdete v tématu [scénáře ověřování pro službu Azure AD][aad_auth_scenarios].

### <a name="batch-resource-endpoint"></a>Koncový bod prostředků služby batch

Použití **koncový bod prostředku Azure Batch** k získání tokenu pro ověřování požadavků na službu Batch:

`https://batch.core.windows.net/`

## <a name="register-your-application-with-a-tenant"></a>Registrace aplikace pomocí tenanta

Prvním krokem při používání služby Azure AD k ověřování je registrace aplikace v tenantovi Azure AD. Registrace aplikace umožňuje volat Azure [Active Directory Authentication Library][aad_adal] (ADAL) z vašeho kódu. ADAL poskytuje rozhraní API pro ověřování pomocí Azure AD z vaší aplikace. Registrace aplikace vyžaduje se, jestli plánujete používat integrované ověřování nebo instanční objekt služby.

Při registraci vaší aplikace zadejte informace o aplikaci do služby Azure AD. Azure AD pak poskytuje ID aplikace (také nazývané *ID klienta*), který používáte k aplikaci přidružit k Azure AD za běhu. Další informace o ID aplikace, najdete v článku [aplikace a instanční objekty v Azure Active Directory](../active-directory/develop/app-objects-and-service-principals.md).

Pokud chcete zaregistrovat aplikaci služby Batch, postupujte podle kroků v [přidáním aplikace](../active-directory/develop/quickstart-v1-add-azure-ad-app.md) tématu [integrace aplikací s Azure Active Directory][aad_integrate]. Když si zaregistrujete aplikaci jako nativní aplikaci, můžete zadat libovolný platný identifikátor URI pro **identifikátor URI pro přesměrování**. Nemusí být skutečný koncový bod.

Po zaregistrování vaší aplikace, zobrazí se vám ID aplikace:

![Registrace aplikace Batch v Azure AD](./media/batch-aad-auth/app-registration-data-plane.png)

Další informace o registraci aplikace v Azure AD najdete v tématu [scénáře ověřování pro službu Azure AD](../active-directory/develop/authentication-scenarios.md).

## <a name="get-the-tenant-id-for-your-active-directory"></a>Získání ID tenanta pro službu Active Directory

ID tenanta identifikuje tenanta Azure AD, která poskytuje služby ověřování pro vaši aplikaci. K získání ID tenanta, postupujte podle těchto kroků:

1. Na webu Azure Portal vyberte služby Active Directory.
2. Klikněte na **Vlastnosti**.
3. Zkopírujte hodnotu identifikátoru GUID stanovené **ID adresáře**. Tato hodnota se označuje také jako ID tenanta.

![Zkopírujte ID adresáře](./media/batch-aad-auth/aad-directory-id.png)


## <a name="use-integrated-authentication"></a>Použít integrované ověřování

K ověření integrované ověřování, musíte udělit oprávnění k vaší aplikace pro připojení k rozhraní API služby Batch. Tento krok umožňuje vaší aplikaci k ověření volání rozhraní API služby Batch pomocí Azure AD.

Jakmile [vaši aplikaci zaregistrovali](#register-your-application-with-an-azure-ad-tenant), postupujte podle těchto kroků na portálu Azure, aby aplikaci udělil přístup ke službě Batch:

1. V levém navigačním podokně na webu Azure portal zvolte **všechny služby**. Klikněte na tlačítko **registrace aplikací**.
2. Hledat název vaší aplikace v seznamu registrací aplikace:

    ![Vyhledejte název vaší aplikace](./media/batch-aad-auth/search-app-registration.png)

3. Klikněte na aplikaci a klikněte na tlačítko **nastavení**. V **přístup přes rozhraní API** vyberte **požadovaná oprávnění**.
4. V **požadovaná oprávnění** okna, klikněte na tlačítko **přidat** tlačítko.
5. V **vyberte rozhraní API**, vyhledejte rozhraní API služby Batch. Hledejte každý z těchto řetězců, dokud nenajdete rozhraní API:
    1. **MicrosoftAzureBatch**.
    2. **Microsoft Azure Batch**. Novější tenanti služby Azure AD mohou používat tento název.
    3. **ddbf3205-c6bd-46ae-8127-60eb93363864** je ID rozhraní API služby Batch. 
6. Jakmile najdete rozhraní API služby Batch, vyberte ho a klikněte na tlačítko **vyberte**.
7. V **vyberte oprávnění**, zaškrtněte políčko vedle položky **přístup služby Azure Batch** a klikněte na tlačítko **vyberte**.
8. Klikněte na **Done** (Hotovo).

**Požadovaná oprávnění** windows teď rozhraní API služby ukazuje, že vaše aplikace Azure AD má přístup k ADAL a služby Batch. Oprávnění jsou udělena adal automaticky při první registraci vaší aplikace s Azure AD.

![Rozhraní API udělení oprávnění](./media/batch-aad-auth/required-permissions-data-plane.png)

## <a name="use-a-service-principal"></a>Použití instančního objektu 

Aplikace, která běží bezobslužné ověření, použijte instanční objekt služby. Po zaregistrování vaší aplikace, postupujte podle těchto kroků na webu Azure Portal ke konfiguraci hlavního názvu služby:

1. Požádat o tajný klíč pro vaši aplikaci.
2. Přiřaďte roli RBAC pro vaši aplikaci.

### <a name="request-a-secret-key-for-your-application"></a>Požádat o tajný klíč pro vaši aplikaci

Když vaše aplikace se ověřuje pomocí instančního objektu, odešle do služby Azure AD ID aplikace a tajný klíč. Budete muset vytvořit a zkopírovat tajný klíč pro použití v kódu.

Postupujte podle těchto kroků na webu Azure Portal:

1. V levém navigačním podokně na webu Azure portal zvolte **všechny služby**. Klikněte na tlačítko **registrace aplikací**.
2. Vyhledejte název vaší aplikace v seznamu registrace aplikací.
3. Klikněte na aplikaci a klikněte na tlačítko **nastavení**. V **přístup přes rozhraní API** vyberte **klíče**.
4. Vytvoření klíče, zadejte popis klíče. Vyberte dobu trvání pro klíč jeden nebo dva roky. 
5. Klikněte na tlačítko **Uložit** tlačítko a vytvořte a zobrazte klíč. Hodnotu klíče zkopírujte na bezpečné místo, nebudete mít znovu přístup po opuštění okna. 

    ![Vytvoření tajného klíče](./media/batch-aad-auth/secret-key.png)

### <a name="assign-an-rbac-role-to-your-application"></a>Přiřaďte roli RBAC pro vaši aplikaci

K ověření pomocí instančního objektu, musíte přiřadit roli RBAC pro vaši aplikaci. Postupujte následovně:

1. Na webu Azure Portal přejděte na účet Batch používá vaše aplikace.
2. V **nastavení** okno účtu Batch vyberte **řízení přístupu (IAM)**.
3. Klikněte na tlačítko **Přidat**. 
4. Z **Role** rozevíracího seznamu, zvolte buď _Přispěvatel_ nebo _čtečky_ role pro vaši aplikaci. Další informace o těchto rolích najdete v tématu [Začínáme s řízením přístupu na základě rolí na portálu Azure portal](../role-based-access-control/overview.md).  
5. V **vyberte** zadejte název vaší aplikace. Vyberte svou aplikaci ze seznamu a klikněte na tlačítko **Uložit**.

Aplikace by teď zobrazují v nastavení řízení přístupu se přiřazenou roli RBAC. 

![Přiřaďte roli RBAC pro vaši aplikaci](./media/batch-aad-auth/app-rbac-role.png)

### <a name="get-the-tenant-id-for-your-azure-active-directory"></a>Získání ID tenanta pro Azure Active Directory

ID tenanta identifikuje tenanta Azure AD, která poskytuje služby ověřování pro vaši aplikaci. K získání ID tenanta, postupujte podle těchto kroků:

1. Na webu Azure Portal vyberte služby Active Directory.
2. Klikněte na **Vlastnosti**.
3. Zkopírujte hodnotu identifikátoru GUID stanovené **ID adresáře**. Tato hodnota se označuje také jako ID tenanta.

![Zkopírujte ID adresáře](./media/batch-aad-auth/aad-directory-id.png)


## <a name="code-examples"></a>Příklady kódu

Příklady kódu v této části ukazují, jak ověřování pomocí Azure AD pomocí integrovaného ověřování a pomocí instančního objektu. Většina těchto příkladech kódu pomocí .NET, ale popsané koncepty se podobně jako u ostatních jazyků.

> [!NOTE]
> Po jedné hodině vyprší platnost ověřovacího tokenu Azure AD. Při používání dlouhodobé **BatchClient** objektu, doporučujeme načíst token z ADAL u každého požadavku k zajištění, že vždycky máte platný token. 
>
>
> K dosažení tohoto cíle v prostředí .NET, napíše metoda, která načte token ze služby Azure AD a předat tuto metodu **BatchTokenCredentials** objektu jako delegát. Metoda delegáta je volána na každý požadavek do služby Batch k zajištění, že není k dispozici platný token. Ve výchozím nastavení knihovny ADAL ukládá do mezipaměti tokenů, aby nový token je načten z Azure AD pouze v případě potřeby. Další informace o tokeny ve službě Azure AD najdete v tématu [scénáře ověřování pro službu Azure AD][aad_auth_scenarios].
>
>

### <a name="code-example-using-azure-ad-integrated-authentication-with-batch-net"></a>Příklad: používání služby Azure AD integrované ověřování pomocí rozhraní Batch .NET

K ověření integrované ověřování v Batch .NET, odkazovat [Azure Batch .NET](https://www.nuget.org/packages/Microsoft.Azure.Batch/) balíčku a [ADAL](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/) balíčku.

Patří `using` příkazů v kódu:

```csharp
using Microsoft.Azure.Batch;
using Microsoft.Azure.Batch.Auth;
using Microsoft.IdentityModel.Clients.ActiveDirectory;
```

Odkazovat na koncový bod Azure AD ve vašem kódu, včetně ID tenanta. Pokud chcete načíst ID tenanta, postupujte podle kroků uvedených v [získání ID tenanta pro Azure Active Directory](#get-the-tenant-id-for-your-active-directory):

```csharp
private const string AuthorityUri = "https://login.microsoftonline.com/<tenant-id>";
```

Koncový bod prostředků služby Batch – reference:

```csharp
private const string BatchResourceUri = "https://batch.core.windows.net/";
```

Odkazovat na účtu Batch:

```csharp
private const string BatchAccountUrl = "https://myaccount.mylocation.batch.azure.com";
```

Zadejte ID aplikace (ID klienta) pro vaši aplikaci. ID aplikace je k dispozici z registrace vaší aplikace na webu Azure Portal:

```csharp
private const string ClientId = "<application-id>";
```

Zkopírujte přesměrování identifikátor URI, který jste zadali, pokud jste vaši aplikaci zaregistrovali jako nativní aplikaci. Zadaný identifikátor URI ve vašem kódu pro přesměrování musí odpovídat přesměrování identifikátor URI, který jste zadali při registraci aplikace:

```csharp
private const string RedirectUri = "http://mybatchdatasample";
```

Zápis metody zpětného volání pro získání tokenu ověřování ze služby Azure AD. **GetAuthenticationTokenAsync** metoda zpětného volání je vidět tady volání ADAL k ověřování uživatele, který pracuje s aplikací. **AcquireTokenAsync** metody poskytované ADAL výzvu k zadání přihlašovacích údajů a aplikace bude pokračovat až uživatel zadá (Pokud již v mezipaměti přihlašovacích údajů):

```csharp
public static async Task<string> GetAuthenticationTokenAsync()
{
    var authContext = new AuthenticationContext(AuthorityUri);

    // Acquire the authentication token from Azure AD.
    var authResult = await authContext.AcquireTokenAsync(BatchResourceUri, 
                                                        ClientId, 
                                                        new Uri(RedirectUri), 
                                                        new PlatformParameters(PromptBehavior.Auto));

    return authResult.AccessToken;
}
```

Vytvoření **BatchTokenCredentials** objekt, který přebírá jako parametr delegátu. Použijte své přihlašovací údaje otevřít **BatchClient** objektu. Můžete ji použít **BatchClient** objekt pro následné operace na službu Batch:

```csharp
public static async Task PerformBatchOperations()
{
    Func<Task<string>> tokenProvider = () => GetAuthenticationTokenAsync();

    using (var client = await BatchClient.OpenAsync(new BatchTokenCredentials(BatchAccountUrl, tokenProvider)))
    {
        await client.JobOperations.ListJobs().ToListAsync();
    }
}
```

### <a name="code-example-using-an-azure-ad-service-principal-with-batch-net"></a>Příklad: použití instančního objektu služby Azure AD pomocí rozhraní Batch .NET

Ověřování pomocí instančního objektu v Batch .NET, odkazovat [Azure Batch .NET](https://www.nuget.org/packages/Azure.Batch/) balíčku a [ADAL](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/) balíčku.

Patří `using` příkazů v kódu:

```csharp
using Microsoft.Azure.Batch;
using Microsoft.Azure.Batch.Auth;
using Microsoft.IdentityModel.Clients.ActiveDirectory;
```

Odkazovat na koncový bod Azure AD ve vašem kódu, včetně ID tenanta. Při použití instančního objektu, je nutné zadat koncový bod specifickým pro tenanta. Pokud chcete načíst ID tenanta, postupujte podle kroků uvedených v [získání ID tenanta pro Azure Active Directory](#get-the-tenant-id-for-your-active-directory):

```csharp
private const string AuthorityUri = "https://login.microsoftonline.com/<tenant-id>";
```

Koncový bod prostředků služby Batch – reference:  

```csharp
private const string BatchResourceUri = "https://batch.core.windows.net/";
```

Odkazovat na účtu Batch:

```csharp
private const string BatchAccountUrl = "https://myaccount.mylocation.batch.azure.com";
```

Zadejte ID aplikace (ID klienta) pro vaši aplikaci. ID aplikace je k dispozici z registrace vaší aplikace na webu Azure Portal:

```csharp
private const string ClientId = "<application-id>";
```

Zadejte tajný klíč, který jste zkopírovali z portálu Azure portal:

```csharp
private const string ClientKey = "<secret-key>";
```

Zápis metody zpětného volání pro získání tokenu ověřování ze služby Azure AD. **GetAuthenticationTokenAsync** metoda zpětného volání je vidět tady volání ADAL pro bezobslužné ověření:

```csharp
public static async Task<string> GetAuthenticationTokenAsync()
{
    AuthenticationContext authContext = new AuthenticationContext(AuthorityUri);
    AuthenticationResult authResult = await authContext.AcquireTokenAsync(BatchResourceUri, new ClientCredential(ClientId, ClientKey));

    return authResult.AccessToken;
}
```

Vytvoření **BatchTokenCredentials** objekt, který přebírá jako parametr delegátu. Použijte své přihlašovací údaje otevřít **BatchClient** objektu. Potom používat **BatchClient** objekt pro následné operace na službu Batch:

```csharp
public static async Task PerformBatchOperations()
{
    Func<Task<string>> tokenProvider = () => GetAuthenticationTokenAsync();

    using (var client = await BatchClient.OpenAsync(new BatchTokenCredentials(BatchAccountUrl, tokenProvider)))
    {
        await client.JobOperations.ListJobs().ToListAsync();
    }
}
```
### <a name="code-example-using-an-azure-ad-service-principal-with-batch-python"></a>Příklad: instanční objekt Azure AD pomocí služby Batch Pythonu

K ověření pomocí hlavního názvu služby z Pythonu služby Batch, instalaci a odkazují [azure-batch](https://pypi.org/project/azure-batch/) a [azure běžné](https://pypi.org/project/azure-common/) moduly.


```python
from azure.batch import BatchServiceClient
from azure.common.credentials import ServicePrincipalCredentials
```

Při použití instančního objektu, je nutné zadat ID tenanta. Pokud chcete načíst ID tenanta, postupujte podle kroků uvedených v [získání ID tenanta pro Azure Active Directory](#get-the-tenant-id-for-your-active-directory):

```python
TENANT_ID = "<tenant-id>";
```

Koncový bod prostředků služby Batch – reference:  

```python
RESOURCE = "https://batch.core.windows.net/";
```

Odkazovat na účtu Batch:

```python
BATCH_ACCOUNT_URL = "https://myaccount.mylocation.batch.azure.com";
```

Zadejte ID aplikace (ID klienta) pro vaši aplikaci. ID aplikace je k dispozici z registrace vaší aplikace na webu Azure Portal:

```python
CLIENT_ID = "<application-id>";
```

Zadejte tajný klíč, který jste zkopírovali z portálu Azure portal:

```python
SECRET = "<secret-key>";
```

Vytvoření **ServicePrincipalCredentials** objektu:

```python
credentials = ServicePrincipalCredentials(
    client_id=CLIENT_ID,
    secret=SECRET,
    tenant=TENANT_ID,
    resource=RESOURCE
)
```

Pomocí přihlašovacích údajů instančního objektu služby otevřete **BatchServiceClient** objektu. Potom používat **BatchServiceClient** objekt pro následné operace na službu Batch.

```python
    batch_client = BatchServiceClient(
    credentials,
    base_url=BATCH_ACCOUNT_URL
)
```

## <a name="next-steps"></a>Další postup

* Další informace o službě Azure AD, najdete v článku [Azure Active Directory, dokumentace ke službě](https://docs.microsoft.com/azure/active-directory/). Podrobné příklady ukazující, jak pomocí knihovny ADAL najdete v [vzorových kódů Azure](https://azure.microsoft.com/resources/samples/?service=active-directory) knihovny.

* Další informace o instančních objektech najdete v tématu [aplikace a instanční objekty v Azure Active Directory](../active-directory/develop/app-objects-and-service-principals.md). Vytvoření instančního objektu pomocí webu Azure portal, najdete v článku [použití portálu k vytvoření služby Active Directory a instančního objektu, který má přístup k prostředkům](../resource-group-create-service-principal-portal.md). Můžete také vytvořit instanční objekt služby pomocí Powershellu nebo rozhraní příkazového řádku Azure.

* K ověření aplikace pro správu služby Batch pomocí Azure AD, najdete v článku [řešení pro správu ověřování služby Batch pomocí Active Directory](batch-aad-auth-management.md).

* Python příklad toho, jak vytvořit klienta služby Batch se ověřují pomocí tokenu Azure AD, najdete v článku [nasazení Azure Batch vlastní Image pomocí skriptu v jazyce Python](https://github.com/azurebigcompute/recipes/blob/master/Azure%20Batch/CustomImages/CustomImagePython.md) vzorku.

[aad_about]:../active-directory/fundamentals/active-directory-whatis.md "Co je Azure Active Directory?"
[aad_adal]: ../active-directory/active-directory-authentication-libraries.md
[aad_auth_scenarios]: ../active-directory/active-directory-authentication-scenarios.md "Scénáře ověřování pro službu Azure AD"
[aad_integrate]: ../active-directory/active-directory-integrating-applications.md "Integrace aplikací s Azure Active Directory"
[azure_portal]: http://portal.azure.com
