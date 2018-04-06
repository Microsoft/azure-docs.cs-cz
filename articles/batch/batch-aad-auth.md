---
title: Pomocí Azure Active Directory k ověření řešení služby Azure Batch | Microsoft Docs
description: Batch podporuje Azure AD pro ověřování ze služby Batch.
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
ms.date: 09/28/2017
ms.author: danlep
ms.openlocfilehash: 2479701174086ddf680e0ecb1da4a9d13b32b76e
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/05/2018
---
# <a name="authenticate-batch-service-solutions-with-active-directory"></a>Ověření řešení služby Batch se službou Active Directory

Azure Batch podporuje ověřování s [Azure Active Directory][aad_about] (Azure AD). Azure AD je víceklientské cloudový adresář společnosti Microsoft a služba identity management. Azure samotné používá Azure AD ověřit jeho zákazníků, správci služeb a organizační uživatele.

Pokud používáte ověřování Azure AD pomocí služby Azure Batch, můžete ověřovat v jednom ze dvou způsobů:

- Pomocí **integrované ověřování** k ověření uživatele, který komunikuje s aplikací. Aplikace prostřednictvím integrovaného ověřování přihlašovacích údajů uživatele shromáždí a použije tyto přihlašovací údaje k ověření přístupu k prostředkům Batch.
- Pomocí **instanční objekt** k ověření bezobslužné aplikace. Objekt služby definuje zásady a oprávnění pro aplikaci, aby mohl představovat aplikace při přístupu k prostředkům v době běhu.

Další informace o Azure AD, najdete v článku [Azure Active Directory dokumentaci](https://docs.microsoft.com/azure/active-directory/).

## <a name="endpoints-for-authentication"></a>Koncové body pro ověřování

K ověření aplikace Batch s Azure AD, budete muset obsahovat některé známé koncové body v kódu.

### <a name="azure-ad-endpoint"></a>Koncový bod Azure AD

Základní koncový bod autority Azure AD:

`https://login.microsoftonline.com/`

K ověření s Azure AD, použijte tento koncový bod společně s ID klienta (ID adresáře). ID klienta identifikuje klienta Azure AD pro ověřování. Pokud chcete načíst ID klienta, postupujte podle kroků uvedených v [získání ID tenanta pro Azure Active Directory](#get-the-tenant-id-for-your-active-directory):

`https://login.microsoftonline.com/<tenant-id>`

> [!NOTE] 
> Koncový bod konkrétního klienta je potřeba při ověření pomocí objektu služby. 
> 
> Koncový bod konkrétního klienta je volitelný při ověřování pomocí integrovaného ověřování, ale doporučujeme. Však můžete také použít běžné koncového bodu Azure AD. Běžné koncový bod poskytuje obecné pověření shromažďování rozhraní, když není k dispozici konkrétní klienta. Běžné koncový bod je `https://login.microsoftonline.com/common`.
>
>

Další informace o koncové body Azure AD najdete v tématu [scénáře ověřování pro Azure AD][aad_auth_scenarios].

### <a name="batch-resource-endpoint"></a>Koncový bod prostředků batch

Použití **koncový bod prostředků Azure Batch** získat token pro ověřování požadavků na službu Batch:

`https://batch.core.windows.net/`

## <a name="register-your-application-with-a-tenant"></a>Registrace aplikace pomocí klienta

Prvním krokem při používání služby Azure AD k ověření je registrace vaší aplikace v klient služby Azure AD. Registrace aplikace umožňuje volat Azure [Active Directory Authentication Library][aad_adal] (ADAL) z vašeho kódu. Knihovnu ADAL poskytuje rozhraní API pro ověřování s Azure AD z vaší aplikace. Registrace aplikace je vyžadován plánujete použít integrované ověřování nebo hlavní název služby.

Při registraci vaší aplikace, můžete zadat informace o vaší aplikaci do služby Azure AD. Potom Azure AD poskytuje ID aplikace, které použijete k aplikaci přidružit Azure AD v době běhu. Další informace o ID aplikace, najdete v části [aplikace a služby hlavní objekty ve službě Azure Active Directory](../active-directory/develop/active-directory-application-objects.md).

Pro registraci aplikace Batch, postupujte podle kroků v [přidání aplikace](../active-directory/develop/active-directory-integrating-applications.md#adding-an-application) kapitoly [integrace aplikací s Azure Active Directory][aad_integrate]. Když si zaregistrujete aplikaci jako nativní aplikaci, můžete zadat libovolný platný identifikátor URI pro **identifikátor URI pro přesměrování**. Nemusí být skutečné koncový bod.

Poté, co jste registrováni vaší aplikace, se zobrazí ID aplikace:

![Registrace aplikace Batch pomocí Azure AD](./media/batch-aad-auth/app-registration-data-plane.png)

Další informace o registraci aplikace s Azure AD najdete v tématu [scénáře ověřování pro Azure AD](../active-directory/develop/active-directory-authentication-scenarios.md).

## <a name="get-the-tenant-id-for-your-active-directory"></a>Získání ID klienta pro vaši službu Active Directory

ID klienta identifikuje klienta Azure AD, která poskytuje služby ověřování do vaší aplikace. Chcete-li získat ID klienta, postupujte takto:

1. Na portálu Azure vyberte vaší služby Active Directory.
2. Klikněte na **Vlastnosti**.
3. Zkopírujte GUID hodnota zadaná pro ID adresáře. Tato hodnota se označuje taky jako ID klienta.

![Zkopírujte ID adresáře sady](./media/batch-aad-auth/aad-directory-id.png)


## <a name="use-integrated-authentication"></a>Použít integrované ověřování

K ověřování pro integrované ověřování, budete muset udělit oprávnění vaší aplikace pro připojení k rozhraní API služby Batch. Tento krok povoluje aplikace k ověření volání do rozhraní API služby Batch s Azure AD.

Jakmile jste [zaregistrovat aplikaci](#register-your-application-with-an-azure-ad-tenant), postupujte podle těchto kroků na portálu Azure jí udělit přístup do služby Batch:

1. V levém navigačním podokně portálu Azure, zvolte **všechny služby**. Klikněte na tlačítko **registrace aplikace**.
2. Vyhledejte název vaší aplikace v seznamu aplikace registrace:

    ![Vyhledávání pro název aplikace](./media/batch-aad-auth/search-app-registration.png)

3. Otevřete **nastavení** okno pro vaši aplikaci. V **přístup pomocí rozhraní API** vyberte **požadovaná oprávnění**.
4. V **požadovaná oprávnění** okně klikněte **přidat** tlačítko.
5. V kroku 1 vyhledejte rozhraní API služby Batch. Hledejte každý z těchto řetězců, dokud nenajdete rozhraní API:
    1. **MicrosoftAzureBatch**.
    2. **Microsoft Azure Batch**. Novější tenanti služby Azure AD mohou používat tento název.
    3. **ddbf3205-c6bd-46ae-8127-60eb93363864** je ID rozhraní API služby Batch. 
6. Jakmile zjistíte, rozhraní API služby Batch, vyberte ho a klikněte na **vyberte** tlačítko.
6. V kroku 2, zaškrtněte políčko vedle **služby Azure Batch přístup** a klikněte na tlačítko **vyberte** tlačítko.
7. Klikněte **provádí** tlačítko.

**Požadovaných oprávnění** okno teď zobrazuje, vaše aplikace Azure AD má přístup k ADAL a dávky služby rozhraní API. Jsou udělena oprávnění pro knihovnu ADAL automaticky při první registraci vaší aplikace s Azure AD.

![Rozhraní API udělení oprávnění](./media/batch-aad-auth/required-permissions-data-plane.png)

## <a name="use-a-service-principal"></a>Použít hlavní název služby 

K ověření aplikace, která spouští bezobslužné, použijte objekt služby. Poté, co jste registrováni vaší aplikace, pomocí těchto kroků na portálu Azure ke konfiguraci hlavní název služby:

1. Požádat o tajný klíč pro vaši aplikaci.
2. Přiřazení role RBAC do vaší aplikace.

### <a name="request-a-secret-key-for-your-application"></a>Žádost o tajný klíč pro vaši aplikaci

Když se aplikace ověřuje s hlavní službou, odešle do služby Azure AD ID aplikace a tajný klíč. Budete muset vytvořit a zkopírovat tajný klíč pro použití v kódu.

Pomocí těchto kroků na portálu Azure:

1. V levém navigačním podokně portálu Azure, zvolte **všechny služby**. Klikněte na tlačítko **registrace aplikace**.
2. Vyhledejte název vaší aplikace v seznamu aplikace registrací.
3. Zobrazení **nastavení** okno. V **přístup pomocí rozhraní API** vyberte **klíče**.
4. Vytvoření klíče, zadejte popis pro klíč. Pak vyberte dobu trvání pro klíč jeden nebo dva roky. 
5. Klikněte **Uložit** tlačítko Vytvořit a zobrazit klíč. Zkopírujte hodnotu klíče na bezpečné místo, nebudou mít přístup k ho znovu po opuštění okna. 

    ![Vytvoření tajný klíč](./media/batch-aad-auth/secret-key.png)

### <a name="assign-an-rbac-role-to-your-application"></a>Přiřazení role RBAC do vaší aplikace

K ověření s hlavní službou, musíte přiřadit role RBAC do vaší aplikace. Postupujte následovně:

1. Na portálu Azure přejděte k účtu Batch používá vaše aplikace.
2. V **nastavení** okno účtu Batch, vyberte **řízení přístupu (IAM)**.
3. Klikněte na tlačítko **Přidat**. 
4. Z **Role** rozevíracího seznamu, vyberte buď _Přispěvatel_ nebo _čtečky_ role pro vaši aplikaci. Další informace o těchto rolí najdete v tématu [Začínáme s řízením přístupu na základě rolí na portálu Azure](../active-directory/role-based-access-control-what-is.md).  
5. V **vyberte** pole, zadejte název vaší aplikace. Vybrat aplikaci ze seznamu a klikněte na tlačítko **Uložit**.

Aplikace by se měla zobrazit v nastavení řízení přístupu s přiřazenou RBAC roli. 

![Přiřazení role RBAC do vaší aplikace](./media/batch-aad-auth/app-rbac-role.png)

### <a name="get-the-tenant-id-for-your-azure-active-directory"></a>Získání ID tenanta pro Azure Active Directory

ID klienta identifikuje klienta Azure AD, která poskytuje služby ověřování do vaší aplikace. Chcete-li získat ID klienta, postupujte takto:

1. Na portálu Azure vyberte vaší služby Active Directory.
2. Klikněte na **Vlastnosti**.
3. Zkopírujte GUID hodnota zadaná pro ID adresáře. Tato hodnota se označuje taky jako ID klienta.

![Zkopírujte ID adresáře sady](./media/batch-aad-auth/aad-directory-id.png)


## <a name="code-examples"></a>Příklady kódu

Příklady kódu v této části ukazují, jak k ověření s pomocí integrovaného ověřování služby Azure AD a instanční objekt. Tyto příklady kódu pomocí rozhraní .NET, ale koncepty jsou podobné jako u ostatních jazyků.

> [!NOTE]
> Azure AD ověřovací token platnost vyprší po jedné hodině. Při použití dlohotrvající **BatchClient** objektu, doporučujeme, abyste u každého požadavku vždy máte platný token načtení tokenu z ADAL. 
>
>
> To můžete udělat v rozhraní .NET, napíše metoda, která načte tokenu z Azure AD a předat tuto metodu **BatchTokenCredentials** objektu jako delegáta. U každého požadavku na službu Batch zajistit, že je k dispozici platný token je volání metody delegáta. Ve výchozím nastavení ADAL ukládá do mezipaměti tokenů, aby nový token se načítají z Azure AD pouze v případě potřeby. Další informace o tokenů ve službě Azure AD najdete v tématu [scénáře ověřování pro Azure AD][aad_auth_scenarios].
>
>

### <a name="code-example-using-azure-ad-integrated-authentication-with-batch-net"></a>Příklad kódu: používání služby Azure AD integrované ověřování pomocí rozhraní Batch .NET

K ověření pomocí integrovaného ověřování z Batch .NET, odkazovat [Azure Batch .NET](https://www.nuget.org/packages/Azure.Batch/) balíček a [ADAL](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/) balíčku.

Patří `using` příkazů v kódu:

```csharp
using Microsoft.Azure.Batch;
using Microsoft.Azure.Batch.Auth;
using Microsoft.IdentityModel.Clients.ActiveDirectory;
```

Referenční dokumentace Azure AD koncového bodu v kódu, včetně ID klienta. Pokud chcete načíst ID klienta, postupujte podle kroků uvedených v [získání ID tenanta pro Azure Active Directory](#get-the-tenant-id-for-your-active-directory):

```csharp
private const string AuthorityUri = "https://login.microsoftonline.com/<tenant-id>";
```

Reference pro koncový bod prostředků služby Batch.

```csharp
private const string BatchResourceUri = "https://batch.core.windows.net/";
```

Referenční vašeho účtu Batch:

```csharp
private const string BatchAccountUrl = "https://myaccount.mylocation.batch.azure.com";
```

Zadejte ID aplikace (ID klienta) pro vaši aplikaci. ID aplikace je k dispozici z vaší aplikace registrace na portálu Azure:

```csharp
private const string ClientId = "<application-id>";
```

Taky zkopírujte identifikátor URI, který jste zadali během procesu registrace přesměrování. Přesměrování, které URI zadaný v kódu musí odpovídat přesměrování identifikátor URI, který jste zadali při registraci aplikace:

```csharp
private const string RedirectUri = "http://mybatchdatasample";
```

Zápis metody zpětného volání získat ověřovací token ze služby Azure AD. **GetAuthenticationTokenAsync** metoda zpětného volání, které jsou tady uvedené volání ADAL k ověření uživatele, který komunikuje s aplikací. **AcquireTokenAsync** metoda poskytované ADAL vyzve uživatele k jejich přihlašovacích údajů a aplikace bude pokračovat po uživatel nezadá (Pokud již v mezipaměti přihlašovacích údajů):

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

Vytvořit **BatchTokenCredentials** objekt, který přebírá delegáta jako parametr. Použít tyto přihlašovací údaje k otevření **BatchClient** objektu. Můžete ji použít **BatchClient** objekt pro následující operace proti služba Batch:

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

### <a name="code-example-using-an-azure-ad-service-principal-with-batch-net"></a>Příklad kódu: objektu služby Azure AD pomocí rozhraní Batch .NET

K ověření pomocí objektu služby z Batch .NET, odkazovat [Azure Batch .NET](https://www.nuget.org/packages/Azure.Batch/) balíček a [ADAL](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/) balíčku.

Patří `using` příkazů v kódu:

```csharp
using Microsoft.Azure.Batch;
using Microsoft.Azure.Batch.Auth;
using Microsoft.IdentityModel.Clients.ActiveDirectory;
```

Referenční dokumentace Azure AD koncového bodu v kódu, včetně ID klienta. Pokud používáte hlavní název služby, je nutné zadat koncový bod konkrétního klienta. Pokud chcete načíst ID klienta, postupujte podle kroků uvedených v [získání ID tenanta pro Azure Active Directory](#get-the-tenant-id-for-your-active-directory):

```csharp
private const string AuthorityUri = "https://login.microsoftonline.com/<tenant-id>";
```

Reference pro koncový bod prostředků služby Batch.  

```csharp
private const string BatchResourceUri = "https://batch.core.windows.net/";
```

Referenční vašeho účtu Batch:

```csharp
private const string BatchAccountUrl = "https://myaccount.mylocation.batch.azure.com";
```

Zadejte ID aplikace (ID klienta) pro vaši aplikaci. ID aplikace je k dispozici z vaší aplikace registrace na portálu Azure:

```csharp
private const string ClientId = "<application-id>";
```

Zadejte tajný klíč, který jste zkopírovali z portálu Azure:

```csharp
private const string ClientKey = "<secret-key>";
```

Zápis metody zpětného volání získat ověřovací token ze služby Azure AD. **GetAuthenticationTokenAsync** metoda zpětného volání, které jsou tady uvedené volání ADAL pro bezobslužné ověřování:

```csharp
public static async Task<string> GetAuthenticationTokenAsync()
{
    AuthenticationContext authContext = new AuthenticationContext(AuthorityUri);
    AuthenticationResult authResult = await authContext.AcquireTokenAsync(BatchResourceUri, new ClientCredential(ClientId, ClientKey));

    return authResult.AccessToken;
}
```

Vytvořit **BatchTokenCredentials** objekt, který přebírá delegáta jako parametr. Použít tyto přihlašovací údaje k otevření **BatchClient** objektu. Můžete jej pak použít **BatchClient** objekt pro následující operace proti služba Batch:

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

## <a name="next-steps"></a>Další postup

* Další informace o Azure AD, najdete v článku [Azure Active Directory dokumentaci](https://docs.microsoft.com/azure/active-directory/). Podrobné příklady znázorňující způsob použití knihovny ADAL jsou k dispozici v [ukázky kódu Azure](https://azure.microsoft.com/resources/samples/?service=active-directory) knihovny.

* Další informace o objektech služby najdete v tématu [aplikace a služby hlavní objekty ve službě Azure Active Directory](../active-directory/develop/active-directory-application-objects.md). Pokud chcete vytvořit objekt služby pomocí portálu Azure, najdete v části [použití portálu k vytvoření služby Active Directory objekt zabezpečení aplikací a služeb, který mají přístup k prostředkům](../resource-group-create-service-principal-portal.md). Hlavní název služby můžete vytvořit také pomocí prostředí PowerShell nebo rozhraní příkazového řádku Azure.

* K ověření aplikace Batch správy pomocí služby Azure AD, najdete v části [řešení pro správu Batch ověření pomocí služby Active Directory](batch-aad-auth-management.md).

* Python příklad toho, jak vytvořit klienta Batch ověřen pomocí tokenu Azure AD najdete v tématu [nasazení Azure Batch vlastní obrázek s skript v jazyce Python](https://github.com/azurebigcompute/recipes/blob/master/Azure%20Batch/CustomImages/CustomImagePython.md) ukázka.

[aad_about]: ../active-directory/active-directory-whatis.md "Co je Azure Active Directory?"
[aad_adal]: ../active-directory/active-directory-authentication-libraries.md
[aad_auth_scenarios]: ../active-directory/active-directory-authentication-scenarios.md "Scénáře ověřování pro Azure AD"
[aad_integrate]: ../active-directory/active-directory-integrating-applications.md "Integrace aplikací s Azure Active Directory"
[azure_portal]: http://portal.azure.com
