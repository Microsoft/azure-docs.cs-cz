---
title: Ověření dávkových služeb Azure pomocí Služby Azure Active Directory
description: Batch podporuje Azure AD pro ověřování ze služby Batch. Přečtěte si, jak se ověřit jedním ze dvou způsobů.
services: batch
documentationcenter: .net
author: LauraBrenner
manager: evansma
editor: ''
tags: ''
ms.assetid: ''
ms.service: batch
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 01/28/2020
ms.author: labrenne
ms.openlocfilehash: f56c05f64086ac2e98e69d6b21fae7a0a63b5006
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77019515"
---
# <a name="authenticate-batch-service-solutions-with-active-directory"></a>Ověření řešení dávkových služeb pomocí služby Active Directory

Azure Batch podporuje ověřování pomocí [Azure Active Directory][aad_about] (Azure AD). Azure AD je služba Microsoft u cloudového cloudu a správy identit. Azure sám používá Azure AD k ověření svých zákazníků, správců služeb a uživatelů organizace.

Při použití ověřování Azure AD s Azure Batch, můžete ověřit jedním ze dvou způsobů:

- Pomocí **integrovaného ověřování** k ověření uživatele, který pracuje s aplikací. Aplikace využívající integrované ověřování shromažďuje pověření uživatele a používá tato pověření k ověření přístupu k dávkovým prostředkům.
- Pomocí **instančního objektu** k ověření bezobslužné aplikace. Instanční objekt definuje zásady a oprávnění pro aplikaci, aby reprezentoval aplikaci při přístupu k prostředkům za běhu.

Další informace o Azure AD najdete v tématu [Dokumentace služby Azure Active Directory](https://docs.microsoft.com/azure/active-directory/).

## <a name="endpoints-for-authentication"></a>Koncové body pro ověřování

K ověření dávkové aplikace pomocí Azure AD, musíte zahrnout některé známé koncové body ve vašem kódu.

### <a name="azure-ad-endpoint"></a>Koncový bod Azure AD

Základní koncový bod autority Azure AD je:

`https://login.microsoftonline.com/`

K ověření pomocí Azure AD, použijte tento koncový bod spolu s ID klienta (ID adresáře). ID klienta identifikuje klienta Azure AD, který se má použít pro ověřování. Pokud chcete načíst ID klienta, postupujte podle kroků popsaných v [části Získání ID klienta pro službu Azure Active Directory](#get-the-tenant-id-for-your-active-directory):

`https://login.microsoftonline.com/<tenant-id>`

> [!NOTE] 
> Koncový bod specifický pro klienta je vyžadován při ověřování pomocí instančního objektu. 
> 
> Koncový bod specifický pro klienta je volitelný při ověřování pomocí integrovaného ověřování, ale doporučuje. Můžete však také použít společný koncový bod Azure AD. Společný koncový bod poskytuje obecné rozhraní shromažďování pověření, pokud není k dispozici konkrétní klient. Běžným koncovým `https://login.microsoftonline.com/common`bodem je .
>
>

Další informace o koncových bodech Azure AD najdete v [tématu Scénáře ověřování pro Azure AD][aad_auth_scenarios].

### <a name="batch-resource-endpoint"></a>Koncový bod dávkového prostředku

Koncový **bod prostředků Azure Batch** slouží k získání tokenu pro ověřování požadavků na službu Batch:

`https://batch.core.windows.net/`

## <a name="register-your-application-with-a-tenant"></a>Registrace aplikace u tenanta

Prvním krokem při ověřování pomocí služby Azure AD je registrace vaší aplikace v tenantovi Azure AD. Registrace aplikace umožňuje volat Azure [Active Directory Authentication Library][aad_adal] (ADAL) z vašeho kódu. ADAL poskytuje rozhraní API pro ověřování pomocí Azure AD z vaší aplikace. Registrace aplikace je vyžadována bez ohledu na to, zda plánujete použít integrované ověřování nebo instanční objekt.

Při registraci aplikace zadáte informace o vaší aplikaci do Služby Azure AD. Azure AD pak poskytuje ID aplikace (označované také jako *ID klienta),* které používáte k přidružení aplikace k Azure AD za běhu. Další informace o ID aplikace najdete v [tématu Objekty za registrovaných aplikací a služeb ve službě Azure Active Directory](../active-directory/develop/app-objects-and-service-principals.md).

Pokud chcete zaregistrovat dávkovou aplikaci, postupujte podle pokynů v části [Přidání aplikace](../active-directory/develop/quickstart-register-app.md) v [části Integrace aplikací se službou Azure Active Directory][aad_integrate]. Pokud zaregistrujete aplikaci jako nativní aplikaci, můžete zadat libovolný platný identifikátor URI pro **identifikátor URI přesměrování**. Nemusí to být skutečný koncový bod.

Po registraci aplikace se zobrazí ID aplikace:

![Registrace dávkové aplikace pomocí Azure AD](./media/batch-aad-auth/app-registration-data-plane.png)

Další informace o registraci aplikace pomocí Azure AD najdete v [tématu Scénáře ověřování pro Azure AD](../active-directory/develop/authentication-scenarios.md).

## <a name="get-the-tenant-id-for-your-active-directory"></a>Získání ID klienta pro službu Active Directory

ID klienta identifikuje klienta Azure AD, který poskytuje ověřovací služby pro vaši aplikaci. Pokud chcete získat ID klienta, postupujte takto:

1. Na webu Azure Portal vyberte službu Active Directory.
1. Vyberte **vlastnosti**.
1. Zkopírujte hodnotu GUID zajišťovnou pro **ID adresáře**. Tato hodnota se také nazývá ID klienta.

![Kopírování ID adresáře](./media/batch-aad-auth/aad-directory-id.png)

## <a name="use-integrated-authentication"></a>Použití integrovaného ověřování

Chcete-li se ověřit pomocí integrovaného ověřování, musíte udělit oprávnění aplikace pro připojení k rozhraní API služby Batch. Tento krok umožňuje vaší aplikaci ověřit volání rozhraní API dávkové služby pomocí Azure AD.

Jakmile aplikaci zaregistrujete, udělte jí přístup ke službě Batch následujícím postupem na webu Azure Portal:

1. V levém navigačním podokně portálu Azure zvolte **Všechny služby**. Vyberte **Registrace aplikací**.
1. Vyhledejte název aplikace v seznamu registrací aplikací:

    ![Vyhledání názvu aplikace](./media/batch-aad-auth/search-app-registration.png)

1. Vyberte aplikaci a vyberte **oprávnění rozhraní API**.
1. V části **Oprávnění rozhraní API** vyberte Přidat **oprávnění**.
1. V **části Vybrat rozhraní API**vyhledejte dávkové rozhraní API. Hledejte každý z těchto řetězců, dokud nenajdete rozhraní API:
    1. **Microsoft Azure Batch**
    1. **ddbf3205-c6bd-46ae-8127-60eb93363864** je ID rozhraní API služby Batch.
1. Jakmile najdete dávkové rozhraní API, vyberte ho a vyberte **vybrat**.
1. V **části Vybrat oprávnění**zaškrtněte políčko vedle aplikace Access Azure Batch **Service** a pak vyberte Přidat **oprávnění**.

Část **oprávnění rozhraní API** teď ukazuje, že vaše aplikace Azure AD má přístup k Microsoft Graphu i k rozhraní API služby Batch. Oprávnění se microsoft graphu udělují automaticky při první registraci aplikace ve službě Azure AD.

![Udělení oprávnění rozhraní API](./media/batch-aad-auth/required-permissions-data-plane.png)

## <a name="use-a-service-principal"></a>Použití instančního objektu

K ověření aplikace, která běží bez obslužné, použijte instanční objekt. Po registraci aplikace nakonfigurujte instanční objekt následujícím postupem na webu Azure Portal:

1. Požádejte o tajný klíč pro vaši aplikaci.
1. Přiřaďte vaší aplikaci řízení přístupu na základě rolí (RBAC).

### <a name="request-a-secret-for-your-application"></a>Žádost o tajný klíč pro vaši aplikaci

Když se vaše aplikace ověří pomocí instančního objektu, odešle ID aplikace i tajný klíč do služby Azure AD. Budete muset vytvořit a zkopírovat tajný klíč pro použití z kódu.

Na webu Azure Portal postupujte podle těchto pokynů:

1. V levém navigačním podokně portálu Azure zvolte **Všechny služby**. Vyberte **Registrace aplikací**.
1. Vyberte aplikaci ze seznamu registrací aplikací.
1. Vyberte aplikaci a potom vyberte **certifikáty & tajných kódů**. V části **Tajné klíče klienta** vyberte **Nový tajný klíč klienta**.
1. Chcete-li vytvořit tajný klíč, zadejte popis tajného klíče. Potom vyberte vypršení platnosti pro tajný klíč buď jeden rok, dva roky nebo žádné vypršení platnosti..
1. Chcete-li vytvořit a zobrazit tajný klíč, vyberte **Přidat.** Zkopírujte tajnou hodnotu na bezpečné místo, protože po opuštění stránky k ní nebudete mít přístup znovu.

    ![Vytvoření tajného klíče](./media/batch-aad-auth/secret-key.png)

### <a name="assign-rbac-to-your-application"></a>Přiřazení RBAC k aplikaci

Chcete-li ověřit pomocí instančního objektu, musíte přiřadit RBAC k vaší aplikaci. Postupujte následovně:

1. Na webu Azure Portal přejděte na účet Batch používaný vaší aplikací.
1. V části **Nastavení** účtu Batch vyberte **řízení přístupu (IAM).**
1. Vyberte kartu **Přiřazení rolí.**
1. Vyberte **Přidat přiřazení role**.
1. V rozevíracím okně **Role** zvolte roli *Přispěvatel* nebo *Čtenář* pro vaši aplikaci. Další informace o těchto rolích najdete [v tématu Začínáme s řízením přístupu na základě rolí na webu Azure Portal](../role-based-access-control/overview.md).  
1. Do pole **Vybrat** zadejte název aplikace. Vyberte aplikaci ze seznamu a pak vyberte **Uložit**.

Vaše aplikace by se nyní měla zobrazit v nastavení řízení přístupu s přiřazenou rolí RBAC.

![Přiřazení role RBAC k aplikaci](./media/batch-aad-auth/app-rbac-role.png)

### <a name="assign-a-custom-role"></a>Přiřazení vlastní role

Vlastní role uděluje uživateli podrobná oprávnění pro odesílání úloh, úkolů a dalších. To poskytuje možnost zabránit uživatelům v provádění operací, které ovlivňují náklady, jako je například vytváření fondů nebo úpravy uzlů.

Vlastní roli můžete použít k udělení oprávnění uživateli, skupině nebo instančnímu objektu Azure AD pro následující operace RBAC:

- Microsoft.Batch/batchAccounts/pools/write
- Microsoft.Batch/batchAccounts/pools/delete
- Microsoft.Batch/batchAccounts/pools/read
- Microsoft.Batch/batchAccounts/jobSchedules/write
- Microsoft.Batch/batchAccounts/jobSchedules/delete
- Microsoft.Batch/batchAccounts/jobSchedules/read
- Microsoft.Batch/batchAccounts/jobs/write
- Microsoft.Batch/batchAccounts/jobs/delete
- Microsoft.Batch/batchAccounts/jobs/read
- Microsoft.Batch/batchAccounts/certificates/write
- Microsoft.Batch/batchAccounts/certificates/delete
- Microsoft.Batch/batchAccounts/certificates/read
- Microsoft.Batch/batchAccounts/read (pro všechny operace čtení)
- Microsoft.Batch/batchAccounts/listKeys/action (pro všechny operace)

Vlastní role jsou pro uživatele ověřené službou Azure AD, nikoli přihlašovací údaje účtu Batch (sdílený klíč). Všimněte si, že pověření účtu Batch udělit úplná oprávnění k účtu Batch. Všimněte si také, že úlohy používající automatické zapěrování vyžadují oprávnění na úrovni fondu.

Tady je příklad vlastní definice role:

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

Podrobnější informace o vytvoření vlastní role najdete v [tématu Vlastní role pro prostředky Azure](../role-based-access-control/custom-roles.md).

### <a name="get-the-tenant-id-for-your-azure-active-directory"></a>Získání ID klienta pro azure active directory

ID klienta identifikuje klienta Azure AD, který poskytuje ověřovací služby pro vaši aplikaci. Pokud chcete získat ID klienta, postupujte takto:

1. Na webu Azure Portal vyberte službu Active Directory.
1. Vyberte **vlastnosti**.
1. Zkopírujte hodnotu GUID zajišťovnou pro **ID adresáře**. Tato hodnota se také nazývá ID klienta.

![Kopírování ID adresáře](./media/batch-aad-auth/aad-directory-id.png)

## <a name="code-examples"></a>Příklady kódu

Příklady kódu v této části ukazují, jak se ověřit pomocí Služby Azure AD pomocí integrovaného ověřování a s instančním objektem. Většina z těchto příkladů kódu použít .NET, ale koncepty jsou podobné pro jiné jazyky.

> [!NOTE]
> Platnost ověřovacího tokenu Azure AD vyprší po jedné hodině. Při použití objektu **BatchClient** s dlouhou životností doporučujeme načíst token z ADAL na každém požadavku, abyste zajistili, že vždy máte platný token. 
>
>
> Chcete-li toho dosáhnout v rozhraní .NET, napište metodu, která načte token z Azure AD a předejte tuto metodu objektu **BatchTokenCredentials** jako delegát. Metoda delegáta je volána na každý požadavek na službu Batch, aby bylo zajištěno, že je k dispozici platný token. Ve výchozím nastavení adal mezipaměti tokeny, takže nový token se načte ze služby Azure AD pouze v případě potřeby. Další informace o tokenech ve službě Azure AD najdete v [tématu Scénáře ověřování pro Azure AD][aad_auth_scenarios].
>
>

### <a name="code-example-using-azure-ad-integrated-authentication-with-batch-net"></a>Příklad kódu: Použití integrovaného ověřování Azure AD s dávkovou .NET

Chcete-li se ověřit pomocí integrovaného ověřování z dávky .NET, odkazujte na balíček [Azure Batch .NET](https://www.nuget.org/packages/Microsoft.Azure.Batch/) a balíček [ADAL.](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/)

Do kódu `using` zahrňte následující příkazy:

```csharp
using Microsoft.Azure.Batch;
using Microsoft.Azure.Batch.Auth;
using Microsoft.IdentityModel.Clients.ActiveDirectory;
```

Odkaz na koncový bod Azure AD ve vašem kódu, včetně ID klienta. Pokud chcete načíst ID klienta, postupujte podle kroků popsaných v [části Získání ID klienta pro službu Azure Active Directory](#get-the-tenant-id-for-your-active-directory):

```csharp
private const string AuthorityUri = "https://login.microsoftonline.com/<tenant-id>";
```

Odkaz na koncový bod prostředku služby Batch:

```csharp
private const string BatchResourceUri = "https://batch.core.windows.net/";
```

Odkaz na svůj dávkový účet:

```csharp
private const string BatchAccountUrl = "https://myaccount.mylocation.batch.azure.com";
```

Zadejte ID aplikace (ID klienta) pro vaši aplikaci. ID aplikace je dostupné z registrace aplikace na webu Azure Portal:

```csharp
private const string ClientId = "<application-id>";
```

Také zkopírujte identifikátor URI přesměrování, který jste zadali, pokud jste zaregistrovali aplikaci jako nativní aplikaci. Identifikátor URI přesměrování zadaný v kódu se musí shodovat s identifikátorem URI přesměrování, který jste zadali při registraci aplikace:

```csharp
private const string RedirectUri = "http://mybatchdatasample";
```

Napište metodu zpětného volání k získání ověřovacího tokenu z Azure AD. Metoda zpětného volání **GetAuthenticationTokenAsync** zobrazená zde volá ADAL k ověření uživatele, který pracuje s aplikací. Metoda **AcquireTokenAsync** poskytnutá společností ADAL vyzve uživatele k zadání pověření a aplikace pokračuje, jakmile je uživatel zadá (pokud již pověření uložená v mezipaměti):

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

Vytvořte objekt **BatchTokenCredentials,** který přebírá delegáta jako parametr. Tato pověření slouží k otevření objektu **BatchClient.** Tento objekt **BatchClient** můžete použít pro následné operace proti službě Batch:

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

### <a name="code-example-using-an-azure-ad-service-principal-with-batch-net"></a>Příklad kódu: Použití instančního objektu služby Azure AD s batch .NET

Chcete-li se ověřit pomocí instančního objektu z batch .NET, odkazujte na balíček [Azure Batch .NET](https://www.nuget.org/packages/Azure.Batch/) a balíček [ADAL.](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/)

Do kódu `using` zahrňte následující příkazy:

```csharp
using Microsoft.Azure.Batch;
using Microsoft.Azure.Batch.Auth;
using Microsoft.IdentityModel.Clients.ActiveDirectory;
```

Odkaz na koncový bod Azure AD ve vašem kódu, včetně ID klienta. Při použití instančního objektu je nutné zadat koncový bod specifický pro klienta. Pokud chcete načíst ID klienta, postupujte podle kroků popsaných v [části Získání ID klienta pro službu Azure Active Directory](#get-the-tenant-id-for-your-active-directory):

```csharp
private const string AuthorityUri = "https://login.microsoftonline.com/<tenant-id>";
```

Odkaz na koncový bod prostředku služby Batch:  

```csharp
private const string BatchResourceUri = "https://batch.core.windows.net/";
```

Odkaz na svůj dávkový účet:

```csharp
private const string BatchAccountUrl = "https://myaccount.mylocation.batch.azure.com";
```

Zadejte ID aplikace (ID klienta) pro vaši aplikaci. ID aplikace je dostupné z registrace aplikace na webu Azure Portal:

```csharp
private const string ClientId = "<application-id>";
```

Zadejte tajný klíč, který jste zkopírovali z portálu Azure:

```csharp
private const string ClientKey = "<secret-key>";
```

Napište metodu zpětného volání k získání ověřovacího tokenu z Azure AD. Zde zobrazená metoda zpětného volání **GetAuthenticationTokenAsync** volá adal pro bezobslužné ověřování:

```csharp
public static async Task<string> GetAuthenticationTokenAsync()
{
    AuthenticationContext authContext = new AuthenticationContext(AuthorityUri);
    AuthenticationResult authResult = await authContext.AcquireTokenAsync(BatchResourceUri, new ClientCredential(ClientId, ClientKey));

    return authResult.AccessToken;
}
```

Vytvořte objekt **BatchTokenCredentials,** který přebírá delegáta jako parametr. Tato pověření slouží k otevření objektu **BatchClient.** Potom použijte tento objekt **BatchClient** pro následné operace proti službě Batch:

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

### <a name="code-example-using-an-azure-ad-service-principal-with-batch-python"></a>Příklad kódu: Použití instančního objektu služby Azure AD s dávkovým Pythonem

Chcete-li ověřit pomocí instančního objektu z Batch Python, nainstalujte a odkazujte na moduly [azure-batch](https://pypi.org/project/azure-batch/) a [azure-common.](https://pypi.org/project/azure-common/)

```python
from azure.batch import BatchServiceClient
from azure.common.credentials import ServicePrincipalCredentials
```

Při použití instančního objektu je nutné zadat ID klienta. Pokud chcete načíst ID klienta, postupujte podle kroků popsaných v [části Získání ID klienta pro službu Azure Active Directory](#get-the-tenant-id-for-your-active-directory):

```python
TENANT_ID = "<tenant-id>"
```

Odkaz na koncový bod prostředku služby Batch:  

```python
RESOURCE = "https://batch.core.windows.net/"
```

Odkaz na svůj dávkový účet:

```python
BATCH_ACCOUNT_URL = "https://myaccount.mylocation.batch.azure.com"
```

Zadejte ID aplikace (ID klienta) pro vaši aplikaci. ID aplikace je dostupné z registrace aplikace na webu Azure Portal:

```python
CLIENT_ID = "<application-id>"
```

Zadejte tajný klíč, který jste zkopírovali z portálu Azure:

```python
SECRET = "<secret-key>"
```

Vytvořte objekt **ServicePrincipalCredentials:**

```python
credentials = ServicePrincipalCredentials(
    client_id=CLIENT_ID,
    secret=SECRET,
    tenant=TENANT_ID,
    resource=RESOURCE
)
```

Pomocí pověření instančního objektu otevřete objekt **BatchServiceClient.** Potom použijte tento objekt **BatchServiceClient** pro následné operace proti službě Batch.

```python
    batch_client = BatchServiceClient(
    credentials,
    base_url=BATCH_ACCOUNT_URL
)
```

## <a name="next-steps"></a>Další kroky

- Další informace o Azure AD najdete v tématu [Dokumentace služby Azure Active Directory](https://docs.microsoft.com/azure/active-directory/). Podrobné příklady, které ukazují, jak používat ADAL jsou k dispozici v knihovně [ukázky kódu Azure.](https://azure.microsoft.com/resources/samples/?service=active-directory)

- Další informace o objektech zabezpečení služeb najdete [v tématu Objekty zaregistrovaných aplikace a služby ve službě Azure Active Directory](../active-directory/develop/app-objects-and-service-principals.md). Pokud chcete vytvořit instanční objekt pomocí portálu Azure, [přečtěte si informace o vytvoření instancí aplikace a služby Active Directory, které mají přístup k prostředkům , pomocí portálu](../active-directory/develop/howto-create-service-principal-portal.md). Můžete také vytvořit instanční objekt s PowerShell nebo Azure CLI.

- Informace o ověřování aplikací správy dávek pomocí služby Azure AD naleznete [v tématu Ověřování řešení správy dávek pomocí služby Active Directory](batch-aad-auth-management.md).

- Příklad Pythonu, jak vytvořit dávkového klienta ověřeného pomocí tokenu Azure AD, najdete [v tématu Nasazení vlastní image azure dávky s ukázkou skriptu Pythonu.](https://github.com/azurebigcompute/recipes/blob/master/Azure%20Batch/CustomImages/CustomImagePython.md)

[aad_about]:../active-directory/fundamentals/active-directory-whatis.md "Co je služba Azure Active Directory?"
[aad_adal]: ../active-directory/active-directory-authentication-libraries.md
[aad_auth_scenarios]: ../active-directory/active-directory-authentication-scenarios.md "Scénáře ověřování pro Azure AD"
[aad_integrate]: ../active-directory/active-directory-integrating-applications.md "Integrace aplikací s Azure Active Directory"
[azure_portal]: https://portal.azure.com
