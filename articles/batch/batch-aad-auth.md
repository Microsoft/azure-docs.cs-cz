---
title: Použití Azure Active Directory k ověřování řešení služby Azure Batch | Microsoft Docs
description: Služba Batch podporuje službu Azure AD k ověřování ze služby Batch.
services: batch
documentationcenter: .net
author: laurenhughes
manager: gwallace
editor: ''
tags: ''
ms.assetid: ''
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 04/18/2018
ms.author: lahugh
ms.openlocfilehash: 64921a2ab69306df0b7c3d968055e698dd6995e7
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/18/2019
ms.locfileid: "68323944"
---
# <a name="authenticate-batch-service-solutions-with-active-directory"></a>Ověřování řešení služby Batch ve službě Active Directory

Azure Batch podporuje ověřování pomocí [Azure Active Directory][aad_about] (Azure AD). Azure AD je cloudová služba pro správu identit založená na cloudu od Microsoftu. Azure využívá Azure AD k ověřování svých zákazníků, správců služeb a uživatelů organizace.

Při použití ověřování Azure AD s Azure Batch se můžete ověřit jedním ze dvou způsobů:

- Použitím **integrovaného ověřování** k ověření uživatele, který pracuje s aplikací. Aplikace, která používá integrované ověřování, shromáždí přihlašovací údaje uživatele a použije tyto přihlašovací údaje k ověření přístupu k prostředkům služby Batch.
- K ověření bezobslužné aplikace pomocí **instančního objektu** . Instanční objekt definuje zásadu a oprávnění pro aplikaci, aby představovala aplikaci při přístupu k prostředkům za běhu.

Další informace o Azure AD najdete v dokumentaci k [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/).

## <a name="endpoints-for-authentication"></a>Koncové body pro ověřování

Abyste mohli v rámci služby Azure AD ověřovat aplikace služby Batch, musíte do kódu zahrnout i některé známé koncové body.

### <a name="azure-ad-endpoint"></a>Koncový bod Azure AD

Základní koncový bod autority Azure AD je:

`https://login.microsoftonline.com/`

K ověřování ve službě Azure AD použijete tento koncový bod společně s ID klienta (ID adresáře). ID tenanta identifikuje tenanta Azure AD pro účely ověření. Pokud chcete načíst ID tenanta, postupujte podle kroků uvedených v části [získání ID tenanta pro váš Azure Active Directory](#get-the-tenant-id-for-your-active-directory):

`https://login.microsoftonline.com/<tenant-id>`

> [!NOTE] 
> Koncový bod specifický pro tenanta se vyžaduje při ověřování pomocí instančního objektu. 
> 
> Koncový bod specifický pro klienta je při ověřování pomocí integrovaného ověřování volitelný, ale doporučuje se. Můžete ale použít i společný koncový bod Azure AD. Společný koncový bod poskytuje obecné rozhraní pro shromažďování přihlašovacích údajů, když není k dispozici konkrétní tenant. Běžný koncový bod je `https://login.microsoftonline.com/common`.
>
>

Další informace o koncových bodech Azure AD najdete v tématu [scénáře ověřování pro Azure AD][aad_auth_scenarios].

### <a name="batch-resource-endpoint"></a>Koncový bod prostředku Batch

Použijte **koncový bod prostředku Azure Batch** k získání tokenu pro ověřování požadavků do služby Batch:

`https://batch.core.windows.net/`

## <a name="register-your-application-with-a-tenant"></a>Registrace aplikace pomocí tenanta

Prvním krokem při ověřování pomocí služby Azure AD je registrace aplikace v tenantovi Azure AD. Registrace aplikace umožňuje volat Azure [Active Directory Authentication Library][aad_adal] (ADAL) z uživatelského kódu. ADAL poskytuje rozhraní API pro ověřování pomocí Azure AD z vaší aplikace. Registrace aplikace se vyžaduje bez ohledu na to, jestli plánujete použít integrované ověřování nebo instanční objekt.

Při registraci vaší aplikace zadejte informace o aplikaci do služby Azure AD. Azure AD pak poskytuje ID aplikace (označované taky jako *ID klienta*), které můžete použít k přidružení aplikace k Azure AD za běhu. Další informace o ID aplikace najdete [v tématu aplikace a objekty zabezpečení služby v Azure Active Directory](../active-directory/develop/app-objects-and-service-principals.md).

Pokud chcete zaregistrovat aplikaci Batch, postupujte podle kroků v části [Přidání aplikace](../active-directory/develop/quickstart-register-app.md) v tématu [integrace aplikací s Azure Active Directory][aad_integrate]. Pokud aplikaci zaregistrujete jako nativní aplikaci, můžete pro **identifikátor URI přesměrování**zadat libovolný platný identifikátor URI. Nemusí se jednat o skutečný koncový bod.

Po zaregistrování aplikace se zobrazí ID aplikace:

![Registrace aplikace Batch pomocí Azure AD](./media/batch-aad-auth/app-registration-data-plane.png)

Další informace o registraci aplikace v Azure AD najdete v tématu [scénáře ověřování pro Azure AD](../active-directory/develop/authentication-scenarios.md).

## <a name="get-the-tenant-id-for-your-active-directory"></a>Získat ID tenanta pro vaši službu Active Directory

ID tenanta identifikuje tenanta Azure AD, který poskytuje služby ověřování pro vaši aplikaci. K získání ID tenanta, postupujte podle těchto kroků:

1. Na webu Azure Portal vyberte služby Active Directory.
2. Klikněte na **Vlastnosti**.
3. Zkopírujte hodnotu identifikátoru GUID stanovené **ID adresáře**. Tato hodnota se označuje také jako ID tenanta.

![Zkopírujte ID adresáře.](./media/batch-aad-auth/aad-directory-id.png)


## <a name="use-integrated-authentication"></a>Použít integrované ověřování

Pokud chcete provést ověření pomocí integrovaného ověřování, musíte aplikaci udělit oprávnění pro připojení k rozhraní API služby Batch. Tento krok umožňuje vaší aplikaci ověřovat volání rozhraní API služby Batch ve službě Azure AD.

Po zaregistrování aplikace postupujte podle těchto kroků v Azure Portal a udělte jí přístup ke službě Batch:

1. V levém navigačním podokně Azure Portal vyberte **všechny služby**. Klikněte na **Registrace aplikací**.
2. V seznamu registrací aplikací vyhledejte název vaší aplikace:

    ![Vyhledejte název vaší aplikace.](./media/batch-aad-auth/search-app-registration.png)

3. Klikněte na aplikaci a pak klikněte na **Nastavení**. V **přístup přes rozhraní API** vyberte **požadovaná oprávnění**.
4. V **požadovaná oprávnění** okna, klikněte na tlačítko **přidat** tlačítko.
5. V části **Vybrat rozhraní API**vyhledejte rozhraní API služby Batch. Hledejte každý z těchto řetězců, dokud nenajdete rozhraní API:
    1. **MicrosoftAzureBatch**.
    2. **Microsoft Azure Batch**. Novější tenanti služby Azure AD mohou používat tento název.
    3. **ddbf3205-c6bd-46ae-8127-60eb93363864** je ID rozhraní API služby Batch. 
6. Jakmile najdete rozhraní API pro dávku, vyberte ho a klikněte na **Vybrat**.
7. V části **vybrat oprávnění**zaškrtněte políčko vedle pole přístup k **Azure Batch službě** a klikněte na **Vybrat**.
8. Klikněte na **Done** (Hotovo).

Okna **požadovaná oprávnění** teď zobrazí, že vaše aplikace Azure AD má přístup ke knihovně ADAL i k rozhraní API služby Batch. Při první registraci aplikace v Azure AD jsou oprávnění automaticky udělena pro ADAL.

![Udělení oprávnění rozhraní API](./media/batch-aad-auth/required-permissions-data-plane.png)

## <a name="use-a-service-principal"></a>Použití instančního objektu 

Pokud chcete ověřit aplikaci, která běží bez obsluhy, použijte instanční objekt. Po zaregistrování aplikace proveďte následující kroky v Azure Portal ke konfiguraci instančního objektu:

1. Vyžádejte si tajný klíč pro vaši aplikaci.
2. Přiřaďte aplikaci roli RBAC.

### <a name="request-a-secret-key-for-your-application"></a>Vyžádání tajného klíče pro vaši aplikaci

Když se vaše aplikace ověřuje pomocí instančního objektu, pošle ID aplikace i tajný klíč do Azure AD. Budete muset vytvořit a zkopírovat tajný klíč, který chcete použít z kódu.

Postupujte podle následujících kroků v Azure Portal:

1. V levém navigačním podokně Azure Portal vyberte **všechny služby**. Klikněte na **Registrace aplikací**.
2. V seznamu registrací aplikací vyhledejte název vaší aplikace.
3. Klikněte na aplikaci a pak klikněte na **Nastavení**. V části **přístup k rozhraní API** vyberte **klíče**.
4. Pokud chcete vytvořit klíč, zadejte popis klíče. Pak vyberte dobu trvání klíče jednoho nebo dvou let. 
5. Kliknutím na tlačítko **Uložit** vytvoříte a zobrazíte klíč. Zkopírujte klíčovou hodnotu na bezpečné místo, protože po opuštění okna už k ní nebudete mít přístup. 

    ![Vytvoření tajného klíče](./media/batch-aad-auth/secret-key.png)

### <a name="assign-an-rbac-role-to-your-application"></a>Přiřazení role RBAC vaší aplikaci

K ověřování pomocí instančního objektu je třeba přiřadit roli RBAC vaší aplikaci. Postupujte následovně:

1. V Azure Portal přejděte na účet Batch používaný vaší aplikací.
2. V okně **Nastavení** pro účet Batch vyberte **Access Control (IAM)** .
3. Klikněte na kartu **přiřazení rolí** .
4. Klikněte na tlačítko **Přidat přiřazení role** . 
5. V rozevíracím seznamu **role** vyberte roli _Přispěvatel_ nebo _Čtenář_ pro vaši aplikaci. Další informace o těchto rolích najdete v tématu [Začínáme s Access Control na základě rolí v Azure Portal](../role-based-access-control/overview.md).  
6. Do pole **Vybrat** zadejte název vaší aplikace. Vyberte aplikaci ze seznamu a klikněte na **Uložit**.

Vaše aplikace by se teď měla zobrazit v nastavení řízení přístupu s přiřazenou rolí RBAC. 

![Přiřazení role RBAC vaší aplikaci](./media/batch-aad-auth/app-rbac-role.png)

### <a name="get-the-tenant-id-for-your-azure-active-directory"></a>Získání ID tenanta pro Azure Active Directory

ID tenanta identifikuje tenanta Azure AD, který poskytuje služby ověřování pro vaši aplikaci. K získání ID tenanta, postupujte podle těchto kroků:

1. Na webu Azure Portal vyberte služby Active Directory.
2. Klikněte na **Vlastnosti**.
3. Zkopírujte hodnotu identifikátoru GUID stanovené **ID adresáře**. Tato hodnota se označuje také jako ID tenanta.

![Zkopírujte ID adresáře.](./media/batch-aad-auth/aad-directory-id.png)


## <a name="code-examples"></a>Příklady kódu

Příklady kódů v této části ukazují, jak provádět ověřování pomocí služby Azure AD pomocí integrovaného ověřování a s instančním objektem. Většina těchto příkladů kódu používá rozhraní .NET, ale koncepty jsou podobné pro jiné jazyky.

> [!NOTE]
> Platnost ověřovacího tokenu Azure AD vyprší za jednu hodinu. Při použití dlouhodobého objektu **BatchClient** doporučujeme, abyste při každém požadavku načetli token z knihovny ADAL a zajistili jste tak, že budete mít vždy platný token. 
>
>
> Pokud to chcete dosáhnout v rozhraní .NET, napište metodu, která načte token z Azure AD, a předejte tuto metodu do objektu **BatchTokenCredentials** jako delegáta. Metoda Delegate je volána pro každý požadavek na službu Batch, aby bylo zajištěno, že bude poskytnut platný token. Ve výchozím nastavení knihovny ADAL ukládá tokeny do mezipaměti, takže nový token se načte z Azure AD jenom v případě potřeby. Další informace o tokenech v Azure AD najdete v tématu [scénáře ověřování pro Azure AD][aad_auth_scenarios].
>
>

### <a name="code-example-using-azure-ad-integrated-authentication-with-batch-net"></a>Příklad kódu: Použití integrovaného ověřování Azure AD s využitím služby Batch .NET

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
public static async Task PerformBatchOperations()
{
    Func<Task<string>> tokenProvider = () => GetAuthenticationTokenAsync();

    using (var client = await BatchClient.OpenAsync(new BatchTokenCredentials(BatchAccountUrl, tokenProvider)))
    {
        await client.JobOperations.ListJobs().ToListAsync();
    }
}
```

### <a name="code-example-using-an-azure-ad-service-principal-with-batch-net"></a>Příklad kódu: Použití instančního objektu Azure AD se službou Batch .NET

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
public static async Task PerformBatchOperations()
{
    Func<Task<string>> tokenProvider = () => GetAuthenticationTokenAsync();

    using (var client = await BatchClient.OpenAsync(new BatchTokenCredentials(BatchAccountUrl, tokenProvider)))
    {
        await client.JobOperations.ListJobs().ToListAsync();
    }
}
```
### <a name="code-example-using-an-azure-ad-service-principal-with-batch-python"></a>Příklad kódu: Použití instančního objektu Azure AD se službou Batch Python

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
    base_url=BATCH_ACCOUNT_URL
)
```

## <a name="next-steps"></a>Další postup

* Další informace o Azure AD najdete v dokumentaci k [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/). Podrobné příklady ukazující, jak používat ADAL jsou k dispozici v knihovně [ukázek kódu Azure](https://azure.microsoft.com/resources/samples/?service=active-directory) .

* Další informace o instančních objektech naleznete [v tématu Application and Service Principal Objects in Azure Active Directory](../active-directory/develop/app-objects-and-service-principals.md). Pokud chcete vytvořit instanční objekt pomocí Azure Portal, přečtěte si téma [použití portálu k vytvoření aplikace služby Active Directory a instančního objektu, který má přístup k prostředkům](../active-directory/develop/howto-create-service-principal-portal.md). Instanční objekt můžete vytvořit také pomocí PowerShellu nebo rozhraní příkazového řádku Azure CLI.

* Pokud chcete ověřit aplikace služby Batch Management pomocí Azure AD, přečtěte si téma [ověřování řešení Batch Management se službou Active Directory](batch-aad-auth-management.md).

* Příklad vytvoření klienta služby Batch ověřeného pomocí tokenu Azure AD v Pythonu najdete v tématu [nasazení Azure Batch vlastní image pomocí skriptu Pythonu](https://github.com/azurebigcompute/recipes/blob/master/Azure%20Batch/CustomImages/CustomImagePython.md) .

[aad_about]:../active-directory/fundamentals/active-directory-whatis.md "Co je Azure Active Directory?"
[aad_adal]: ../active-directory/active-directory-authentication-libraries.md
[aad_auth_scenarios]: ../active-directory/active-directory-authentication-scenarios.md "Scénáře ověřování pro Azure AD"
[aad_integrate]: ../active-directory/active-directory-integrating-applications.md "Integrace aplikací s Azure Active Directory"
[azure_portal]: https://portal.azure.com
