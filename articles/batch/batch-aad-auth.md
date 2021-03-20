---
title: Ověřování služby Azure Batch Services pomocí Azure Active Directory
description: Služba Batch podporuje službu Azure AD k ověřování ze služby Batch. Přečtěte si, jak ověřit jedním ze dvou způsobů.
ms.topic: how-to
ms.date: 10/20/2020
ms.custom: has-adal-ref
ms.openlocfilehash: 2ceefa538c44208750da8986fcf3d161f0c0865f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "102180016"
---
# <a name="authenticate-batch-service-solutions-with-active-directory"></a>Ověřování řešení služby Batch ve službě Active Directory

Azure Batch podporuje ověřování pomocí [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md) (Azure AD). Azure AD je cloudová služba pro správu identit založená na cloudu od Microsoftu. Azure využívá Azure AD k ověřování svých zákazníků, správců služeb a uživatelů organizace.

Při použití ověřování Azure AD s Azure Batch se můžete ověřit jedním ze dvou způsobů:

- Použitím **integrovaného ověřování** k ověření uživatele, který pracuje s aplikací. Aplikace, která používá integrované ověřování, shromáždí přihlašovací údaje uživatele a použije tyto přihlašovací údaje k ověření přístupu k prostředkům služby Batch.
- K ověření bezobslužné aplikace pomocí **instančního objektu** . Instanční objekt definuje zásadu a oprávnění pro aplikaci, aby představovala aplikaci při přístupu k prostředkům za běhu.

Další informace o Azure AD najdete v dokumentaci k [Azure Active Directory](../active-directory/index.yml).

## <a name="endpoints-for-authentication"></a>Koncové body pro ověřování

Abyste mohli v rámci služby Azure AD ověřovat aplikace služby Batch, musíte do kódu zahrnout i některé známé koncové body.

### <a name="azure-ad-endpoint"></a>Koncový bod Azure AD

Základní koncový bod autority Azure AD je:

`https://login.microsoftonline.com/`

K ověřování ve službě Azure AD použijete tento koncový bod společně s ID klienta (ID adresáře). ID tenanta identifikuje tenanta Azure AD, který se má použít pro ověřování. Pokud chcete načíst ID tenanta, postupujte podle kroků uvedených v části [získání ID tenanta pro váš Azure Active Directory](#get-the-tenant-id-for-your-active-directory):

`https://login.microsoftonline.com/<tenant-id>`

> [!NOTE]
> Koncový bod specifický pro tenanta se vyžaduje při ověřování pomocí instančního objektu.
>
> Koncový bod specifický pro klienta je při ověřování pomocí integrovaného ověřování volitelný, ale doporučuje se. Můžete ale použít i společný koncový bod Azure AD. Společný koncový bod poskytuje obecné rozhraní pro shromažďování přihlašovacích údajů, když není k dispozici konkrétní tenant. Běžný koncový bod je `https://login.microsoftonline.com/common` .

Další informace o koncových bodech Azure AD najdete v tématu [ověřování vs. Authorization](../active-directory/develop/authentication-vs-authorization.md).

### <a name="batch-resource-endpoint"></a>Koncový bod prostředku Batch

Použijte **koncový bod prostředku Azure Batch** k získání tokenu pro ověřování požadavků do služby Batch:

`https://batch.core.windows.net/`

## <a name="register-your-application-with-a-tenant"></a>Registrace aplikace pomocí tenanta

Prvním krokem při ověřování pomocí služby Azure AD je registrace aplikace v tenantovi Azure AD. Registrace aplikace umožňuje volat službu Azure [Active Directory Authentication Library](../active-directory/azuread-dev/active-directory-authentication-libraries.md) (ADAL) z vašeho kódu. ADAL poskytuje rozhraní API pro ověřování ve službě Azure AD z vaší aplikace. Registrace aplikace se vyžaduje bez ohledu na to, jestli plánujete použít integrované ověřování nebo instanční objekt.

Při registraci aplikace zadáváte informace o vaší aplikaci do služby Azure AD. Azure AD pak poskytuje ID aplikace (označované taky jako *ID klienta*), které můžete použít k přidružení aplikace k Azure AD za běhu. Další informace o ID aplikace najdete [v tématu aplikace a objekty zabezpečení služby v Azure Active Directory](../active-directory/develop/app-objects-and-service-principals.md).

Pokud chcete zaregistrovat aplikaci Batch, postupujte podle kroků v části **Registrace aplikace** v [rychlém startu: registrace aplikace s platformou Microsoft Identity](../active-directory/develop/quickstart-register-app.md). Pokud aplikaci zaregistrujete jako nativní aplikaci, můžete pro **identifikátor URI přesměrování** zadat libovolný platný identifikátor URI. Nemusí se jednat o skutečný koncový bod.

Po zaregistrování aplikace se zobrazí ID aplikace:

![Snímek obrazovky s ID aplikace zobrazeným ve Azure Portal.](./media/batch-aad-auth/app-registration-data-plane.png)

## <a name="get-the-tenant-id-for-your-active-directory"></a>Získat ID tenanta pro vaši službu Active Directory

ID tenanta identifikuje tenanta Azure AD, který poskytuje služby ověřování pro vaši aplikaci. K získání ID tenanta použijte následující postup:

1. V Azure Portal vyberte svou službu Active Directory.
1. Vyberte **Vlastnosti**.
1. Zkopírujte hodnotu identifikátoru GUID poskytnutou pro **ID adresáře**. Tato hodnota se označuje taky jako ID tenanta.

![Snímek obrazovky s ID adresáře v Azure Portal.](./media/batch-aad-auth/aad-directory-id.png)

## <a name="use-integrated-authentication"></a>Použít integrované ověřování

Pokud chcete provést ověření pomocí integrovaného ověřování, musíte aplikaci udělit oprávnění pro připojení k rozhraní API služby Batch. Tento krok umožňuje vaší aplikaci ověřovat volání rozhraní API služby Batch ve službě Azure AD.

Po zaregistrování aplikace postupujte podle těchto kroků v Azure Portal a udělte jí přístup ke službě Batch:

1. V Azure Portal zvolte **všechny služby** a pak vyberte **Registrace aplikací**.
1. V seznamu registrací aplikací vyhledejte název vaší aplikace.
1. Vyberte aplikaci a vyberte **oprávnění rozhraní API**.
1. V části **oprávnění rozhraní API** vyberte **Přidat oprávnění**.
1. V části **Vybrat rozhraní API** vyhledejte rozhraní API služby Batch. Hledejte každý z těchto řetězců, dokud nenajdete rozhraní API:
    1. **Microsoft Azure Batch**
    1. **ddbf3205-c6bd-46ae-8127-60eb93363864** je ID rozhraní API služby Batch.
1. Jakmile najdete rozhraní API pro dávkové zpracování, vyberte ho a pak zvolte **Vybrat**.
1. V části **vybrat oprávnění** zaškrtněte políčko vedle pole přístup k **Azure Batch službě** a pak vyberte **Přidat oprávnění**.

V části **oprávnění API** se teď zobrazuje, že vaše aplikace Azure AD má přístup k oběma Microsoft Graph i k rozhraní API služby Batch. Oprávnění se udělují Microsoft Graph automaticky při první registraci vaší aplikace ve službě Azure AD.

## <a name="use-a-service-principal"></a>Použití instančního objektu

Pokud chcete ověřit aplikaci, která běží bez obsluhy, použijte instanční objekt. Po zaregistrování aplikace proveďte následující kroky v Azure Portal ke konfiguraci instančního objektu:

1. Vyžádejte si tajný klíč pro vaši aplikaci.
1. Přiřaďte aplikaci řízení přístupu na základě role Azure (Azure RBAC).

### <a name="request-a-secret-for-your-application"></a>Vyžádání tajného klíče pro vaši aplikaci

Když se vaše aplikace ověřuje pomocí instančního objektu, pošle ID aplikace i tajný kód do Azure AD. Budete muset vytvořit a zkopírovat tajný klíč, který chcete použít z kódu.

Na webu Azure Portal postupujte podle těchto pokynů:

1. V Azure Portal vyberte **všechny služby**. Vyberte **Registrace aplikací**.
1. Vyberte svou aplikaci ze seznamu registrací aplikací.
1. Vyberte aplikaci a pak vyberte **certifikáty & tajných** kódů. V části **tajné klíče klienta** vyberte **nový tajný klíč klienta**.
1. Pokud chcete vytvořit tajný klíč, zadejte popis tajného kódu. Pak vyberte vypršení platnosti tajného kódu buď za jeden rok, dva roky, nebo bez vypršení platnosti.
1. Vyberte **Přidat** a vytvořte a zobrazte tajný klíč. Zkopírujte tajnou hodnotu na bezpečné místo, protože po opuštění stránky už k ní nebudete mít přístup.

### <a name="assign-azure-rbac-to-your-application"></a>Přiřazení služby Azure RBAC do vaší aplikace

K ověřování pomocí instančního objektu je třeba přiřadit aplikaci Azure RBAC do vaší aplikace. Postupujte takto:

1. V Azure Portal přejděte na účet Batch používaný vaší aplikací.
1. V části **Nastavení** účtu Batch vyberte možnost **Access Control (IAM)**.
1. Vyberte kartu **přiřazení rolí** .
1. Vyberte **Přidat přiřazení role**.
1. V rozevíracím seznamu **role** vyberte roli *Přispěvatel* nebo *Čtenář* pro vaši aplikaci. Další informace o těchto rolích najdete [v tématu Začínáme s řízením přístupu na základě role Azure v Azure Portal](../role-based-access-control/overview.md).
1. Do pole **Vybrat** zadejte název vaší aplikace. V seznamu vyberte svou aplikaci a pak vyberte **Uložit**.

Vaše aplikace by se teď měla zobrazit v nastavení řízení přístupu s přiřazenou rolí Azure.

![Přiřazení role Azure k vaší aplikaci](./media/batch-aad-auth/app-rbac-role.png)

### <a name="assign-a-custom-role"></a>Přiřazení vlastní role

Vlastní role uděluje podrobné oprávnění uživateli k odesílání úloh, úloh a dalších funkcí. Díky tomu je možné zabránit uživatelům v provádění operací, které mají vliv na náklady, jako je vytváření fondů nebo změna uzlů.

Pomocí vlastní role můžete udělit oprávnění uživateli, skupině nebo instančnímu objektu služby Azure AD pro tyto operace Azure RBAC:

- Microsoft.Batch/batchAccounts/Pools/Write
- Microsoft.Batch/batchAccounts/Pools/DELETE
- Microsoft.Batch/batchAccounts/Pools/Read
- Microsoft.Batch/batchAccounts/jobSchedules/Write
- Microsoft.Batch/batchAccounts/jobSchedules/DELETE
- Microsoft.Batch/batchAccounts/jobSchedules/Read
- Microsoft.Batch/batchAccounts/Jobs/Write
- Microsoft.Batch/batchAccounts/Jobs/DELETE
- Microsoft.Batch/batchAccounts/Jobs/Read
- Microsoft.Batch/batchAccounts/certifikáty/zápis
- Microsoft.Batch/batchAccounts/certifikáty/odstranit
- Microsoft.Batch/batchAccounts/certifikáty/číst
- Microsoft.Batch/batchAccounts/Read (pro jakoukoliv operaci čtení)
- Microsoft.Batch/batchAccounts/klíče listkey/Action (pro všechny operace)

Vlastní role jsou pro uživatele ověřené službou Azure AD, nikoli přihlašovací údaje k účtu Batch (sdílený klíč). Všimněte si, že přihlašovací údaje účtu Batch poskytují úplný přístup k účtu Batch. Všimněte si také, že úlohy využívající [autopool](nodes-and-pools.md#autopools) vyžadují oprávnění na úrovni fondu.

> [!NOTE]
> V poli akce je potřeba zadat určitá přiřazení rolí, zatímco ostatní je potřeba zadat v poli dataaction. Další informace najdete v tématu [operace poskytovatele prostředků Azure](../role-based-access-control/resource-provider-operations.md#microsoftbatch).

Tady je příklad definice vlastní role:

```json
{
 "properties":{
    "roleName":"Azure Batch Custom Job Submitter",
    "type":"CustomRole",
    "description":"Allows a user to submit jobs to Azure Batch but not manage pools",
    "assignableScopes":[
      "/subscriptions/88888888-8888-8888-8888-888888888888"
    ],
    "permissions":[
      {
        "actions":[
          "Microsoft.Batch/*/read",
          "Microsoft.Authorization/*/read",
          "Microsoft.Resources/subscriptions/resourceGroups/read",
          "Microsoft.Support/*",
          "Microsoft.Insights/alertRules/*"
        ],
        "notActions":[

        ],
        "dataActions":[
          "Microsoft.Batch/batchAccounts/jobs/*",
          "Microsoft.Batch/batchAccounts/jobSchedules/*"
        ],
        "notDataActions":[

        ]
      }
    ]
  }
}
```

Další informace o vytvoření vlastní role najdete v tématu [vlastní role Azure](../role-based-access-control/custom-roles.md).

### <a name="get-the-tenant-id-for-your-azure-active-directory"></a>Získat ID tenanta pro vaši Azure Active Directory

ID tenanta identifikuje tenanta Azure AD, který poskytuje služby ověřování pro vaši aplikaci. K získání ID tenanta použijte následující postup:

1. V Azure Portal vyberte svou službu Active Directory.
1. Vyberte **Vlastnosti**.
1. Zkopírujte hodnotu identifikátoru GUID poskytnutou pro **ID adresáře**. Tato hodnota se označuje taky jako ID tenanta.

![Zkopírujte ID adresáře.](./media/batch-aad-auth/aad-directory-id.png)

## <a name="code-examples"></a>Příklady kódu

Příklady kódů v této části ukazují, jak provádět ověřování pomocí služby Azure AD pomocí integrovaného ověřování a s instančním objektem. Většina těchto příkladů kódu používá rozhraní .NET, ale koncepty jsou podobné pro jiné jazyky.

> [!NOTE]
> Platnost ověřovacího tokenu Azure AD vyprší za jednu hodinu. Při použití dlouhodobého objektu **BatchClient** doporučujeme, abyste při každém požadavku načetli token z knihovny ADAL a zajistili jste tak, že budete mít vždy platný token.
>
> Pokud to chcete dosáhnout v rozhraní .NET, napište metodu, která načte token z Azure AD, a předejte tuto metodu do objektu **BatchTokenCredentials** jako delegáta. Metoda Delegate je volána pro každý požadavek na službu Batch, aby bylo zajištěno, že bude poskytnut platný token. Ve výchozím nastavení knihovny ADAL ukládá tokeny do mezipaměti, takže nový token se načte z Azure AD jenom v případě potřeby. Další informace o tokenech v Azure AD najdete v tématu [tokeny zabezpečení](../active-directory/develop/security-tokens.md).

### <a name="code-example-using-azure-ad-integrated-authentication-with-batch-net"></a>Příklad kódu: použití integrovaného ověřování Azure AD s využitím služby Batch .NET

Pokud chcete provést ověření pomocí integrovaného ověřování ze služby Batch .NET, odkazujte na balíček [Azure Batch .NET](https://www.nuget.org/packages/Microsoft.Azure.Batch/) a na balíček [ADAL](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/) .

Do kódu zahrňte následující `using` příkazy:

```csharp
using Microsoft.Azure.Batch;
using Microsoft.Azure.Batch.Auth;
using Microsoft.IdentityModel.Clients.ActiveDirectory;
```

Odkazování na koncový bod Azure AD v kódu, včetně ID tenanta. Pokud chcete načíst ID tenanta, postupujte podle kroků uvedených v části [získání ID tenanta pro váš Azure Active Directory](#get-the-tenant-id-for-your-active-directory):

```csharp
private const string AuthorityUri = "https://login.microsoftonline.com/<tenant-id>";
```

Odkaz na koncový bod prostředku služby Batch:

```csharp
private const string BatchResourceUri = "https://batch.core.windows.net/";
```

Odkaz na účet Batch:

```csharp
private const string BatchAccountUrl = "https://myaccount.mylocation.batch.azure.com";
```

Zadejte ID aplikace (ID klienta) pro vaši aplikaci. ID aplikace je k dispozici z registrace vaší aplikace v Azure Portal:

```csharp
private const string ClientId = "<application-id>";
```

Také zkopírujte identifikátor URI přesměrování, který jste zadali, pokud jste aplikaci zaregistrovali jako nativní aplikaci. Identifikátor URI přesměrování zadaný ve vašem kódu musí odpovídat identifikátoru URI přesměrování, který jste zadali při registraci aplikace:

```csharp
private const string RedirectUri = "http://mybatchdatasample";
```

Napíšete metodu zpětného volání pro získání ověřovacího tokenu z Azure AD. Zde uvedená metoda zpětného volání **GetAuthenticationTokenAsync** volá ADAL k ověření uživatele, který interakci s aplikací. Metoda **AcquireTokenAsync** poskytovaná modulem ADAL vyzve uživatele k zadání přihlašovacích údajů a aplikace pokračuje, jakmile je uživatel poskytne (Pokud už neobsahuje přihlašovací údaje uložené v mezipaměti):

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

Vytvořte objekt **BatchTokenCredentials** , který převezme delegáta jako parametr. Pomocí těchto přihlašovacích údajů otevřete objekt **BatchClient** . Tento objekt **BatchClient** můžete použít pro následné operace se službou Batch:

```csharp
public static void PerformBatchOperations()
{
    Func<Task<string>> tokenProvider = () => GetAuthenticationTokenAsync();

    using (var client = BatchClient.Open(new BatchTokenCredentials(BatchAccountUrl, tokenProvider)))
    {
        client.JobOperations.ListJobs();
    }
}
```

### <a name="code-example-using-an-azure-ad-service-principal-with-batch-net"></a>Příklad kódu: použití instančního objektu služby Azure AD se službou Batch .NET

Pokud chcete provést ověření pomocí instančního objektu ze služby Batch .NET, odkazujte na balíček [Azure Batch .NET](https://www.nuget.org/packages/Azure.Batch/) a na balíček [ADAL](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/) .

Do kódu zahrňte následující `using` příkazy:

```csharp
using Microsoft.Azure.Batch;
using Microsoft.Azure.Batch.Auth;
using Microsoft.IdentityModel.Clients.ActiveDirectory;
```

Odkazování na koncový bod Azure AD v kódu, včetně ID tenanta. Při použití instančního objektu musíte zadat koncový bod specifický pro klienta. Pokud chcete načíst ID tenanta, postupujte podle kroků uvedených v části [získání ID tenanta pro váš Azure Active Directory](#get-the-tenant-id-for-your-active-directory):

```csharp
private const string AuthorityUri = "https://login.microsoftonline.com/<tenant-id>";
```

Odkaz na koncový bod prostředku služby Batch:

```csharp
private const string BatchResourceUri = "https://batch.core.windows.net/";
```

Odkaz na účet Batch:

```csharp
private const string BatchAccountUrl = "https://myaccount.mylocation.batch.azure.com";
```

Zadejte ID aplikace (ID klienta) pro vaši aplikaci. ID aplikace je k dispozici z registrace vaší aplikace v Azure Portal:

```csharp
private const string ClientId = "<application-id>";
```

Zadejte tajný klíč, který jste zkopírovali z Azure Portal:

```csharp
private const string ClientKey = "<secret-key>";
```

Napíšete metodu zpětného volání pro získání ověřovacího tokenu z Azure AD. Zde uvedená metoda zpětného volání **GetAuthenticationTokenAsync** volá ADAL pro bezobslužné ověřování:

```csharp
public static async Task<string> GetAuthenticationTokenAsync()
{
    AuthenticationContext authContext = new AuthenticationContext(AuthorityUri);
    AuthenticationResult authResult = await authContext.AcquireTokenAsync(BatchResourceUri, new ClientCredential(ClientId, ClientKey));

    return authResult.AccessToken;
}
```

Vytvořte objekt **BatchTokenCredentials** , který převezme delegáta jako parametr. Pomocí těchto přihlašovacích údajů otevřete objekt **BatchClient** . Pak tento objekt **BatchClient** použijte pro následné operace se službou Batch:

```csharp
public static void PerformBatchOperations()
{
    Func<Task<string>> tokenProvider = () => GetAuthenticationTokenAsync();

    using (var client = BatchClient.Open(new BatchTokenCredentials(BatchAccountUrl, tokenProvider)))
    {
        client.JobOperations.ListJobs();
    }
}
```

### <a name="code-example-using-an-azure-ad-service-principal-with-batch-python"></a>Příklad kódu: použití instančního objektu služby Azure AD se službou Batch Python

Pokud chcete provést ověření pomocí instančního objektu ze služby Batch Python, nainstalujte a prokažte moduly [Azure-Batch](https://pypi.org/project/azure-batch/) a [Azure-Common](https://pypi.org/project/azure-common/) .

```python
from azure.batch import BatchServiceClient
from azure.common.credentials import ServicePrincipalCredentials
```

Při použití instančního objektu musíte zadat ID tenanta. Pokud chcete načíst ID tenanta, postupujte podle kroků uvedených v části [získání ID tenanta pro váš Azure Active Directory](#get-the-tenant-id-for-your-active-directory):

```python
TENANT_ID = "<tenant-id>"
```

Odkaz na koncový bod prostředku služby Batch:

```python
RESOURCE = "https://batch.core.windows.net/"
```

Odkaz na účet Batch:

```python
BATCH_ACCOUNT_URL = "https://myaccount.mylocation.batch.azure.com"
```

Zadejte ID aplikace (ID klienta) pro vaši aplikaci. ID aplikace je k dispozici z registrace vaší aplikace v Azure Portal:

```python
CLIENT_ID = "<application-id>"
```

Zadejte tajný klíč, který jste zkopírovali z Azure Portal:

```python
SECRET = "<secret-key>"
```

Vytvořte objekt **ServicePrincipalCredentials** :

```python
credentials = ServicePrincipalCredentials(
    client_id=CLIENT_ID,
    secret=SECRET,
    tenant=TENANT_ID,
    resource=RESOURCE
)
```

Pomocí přihlašovacích údajů instančního objektu otevřete objekt **BatchServiceClient** . Pak tento objekt **BatchServiceClient** použijte pro následné operace se službou Batch.

```python
    batch_client = BatchServiceClient(
    credentials,
    batch_url=BATCH_ACCOUNT_URL
)
```

## <a name="next-steps"></a>Další kroky

- Přečtěte si [dokumentaci k Azure Active Directory](../active-directory/index.yml). Podrobné příklady ukazující, jak používat ADAL jsou k dispozici v knihovně [ukázek kódu Azure](https://azure.microsoft.com/resources/samples/?service=active-directory) .
- Přečtěte si o [aplikacích a instančních objektech v Azure Active Directory](../active-directory/develop/app-objects-and-service-principals.md) a [o tom, jak vytvořit aplikaci a instanční objekt služby Azure AD, který má přístup k prostředkům](../active-directory/develop/howto-create-service-principal-portal.md).
- Přečtěte si o [ověřování řešení pro správu služby Batch pomocí služby Active Directory](batch-aad-auth-management.md).
- Příklad vytvoření klienta služby Batch ověřeného pomocí tokenu Azure AD v Pythonu najdete v tématu [nasazení Azure Batch vlastní image pomocí skriptu Pythonu](https://github.com/azurebigcompute/recipes/blob/master/Azure%20Batch/CustomImages/CustomImagePython.md) .

