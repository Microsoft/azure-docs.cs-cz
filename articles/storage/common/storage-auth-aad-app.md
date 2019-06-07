---
title: Ověřování pomocí Azure Active Directory pro přístup k datům objektu blob a fronty z klientské aplikace
description: Pomocí Azure Active Directory k ověřování z v rámci klientské aplikace, získání tokenu OAuth 2.0 a autorizaci požadavků na úložiště Azure Blob storage a Queue storage.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 06/05/2019
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: e45fe20e93d81c1cfd1f868b40f76743558758bb
ms.sourcegitcommit: 45e4466eac6cfd6a30da9facd8fe6afba64f6f50
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/07/2019
ms.locfileid: "66754933"
---
# <a name="authenticate-with-azure-active-directory-from-an-application-for-access-to-blobs-and-queues"></a>Ověřování pomocí Azure Active Directory z aplikace pro přístup k objektům BLOB a fronty

Klíčovou výhodou Azure Blob storage nebo Queue storage pomocí Azure Active Directory (Azure AD) je, že vaše přihlašovací údaje už nemusí být uloženy ve vašem kódu. Přístupový token OAuth 2.0 můžete místo toho si vyžádat od platforma identit Microsoft (dříve Azure AD). Služba Azure AD ověřuje objektu zabezpečení (uživatele, skupiny nebo instanční objekt služby) spuštění aplikace. Pokud je ověřování úspěšné, Azure AD vrací přístupový token k aplikaci a aplikace můžete potom použít přístupový token k autorizaci požadavků na úložiště Azure Blob storage nebo Queue storage.

Tento článek ukazuje, jak konfigurovat vaše nativní aplikace nebo webové aplikace k ověřování pomocí Azure AD. Funkce .NET příklad kódu, ale jiných jazycích používat podobný přístup.

Přehled toku přidělení kódu OAuth 2.0, naleznete v tématu [autorizovat přístup k Azure Active Directory webovým aplikacím pomocí OAuth 2.0 kódu udělit tok](../../active-directory/develop/v2-oauth2-auth-code-flow.md).

## <a name="assign-an-rbac-role-to-an-azure-ad-security-principal"></a>Přiřaďte roli RBAC pro objekt zabezpečení Azure AD

K ověření objektu zabezpečení z vaší aplikace Azure Storage, nejprve nakonfigurujte nastavení řízení přístupu na základě rolí pro tento objekt zabezpečení. Azure Storage definuje předdefinované role RBAC, které zahrnuje oprávnění pro kontejnery a fronty. Pokud RBAC role je přiřazena k objektu zabezpečení, tento objekt zabezpečení se udělí přístup k prostředku. Další informace najdete v tématu [Správa přístupových práv k datům objektů Blob v Azure a fronty pomocí RBAC](storage-auth-aad-rbac.md).

## <a name="register-your-application-with-an-azure-ad-tenant"></a>Registrace aplikace pomocí tenanta služby Azure AD

Prvním krokem při používání služby Azure AD k autorizaci přístupu k prostředkům úložiště je registraci tenanta Azure AD z klientské aplikace [webu Azure portal](https://portal.azure.com). Když si zaregistrujete klientské aplikace, je třeba zadat informace o aplikaci do služby Azure AD. Azure AD pak poskytuje ID klienta (také nazývané *ID aplikace*), který používáte k aplikaci přidružit k Azure AD za běhu. Další informace o ID klienta najdete v tématu [aplikace a instanční objekty v Azure Active Directory](../../active-directory/develop/app-objects-and-service-principals.md).

Pokud chcete zaregistrovat aplikaci Azure Storage, postupujte podle kroků je znázorněno v [rychlý start: Registrace aplikace s platformou identity Microsoft](../../active-directory/develop/quickstart-configure-app-access-web-apis.md). Obecná nastavení pro registraci webové aplikace na následujícím obrázku:

![Snímek obrazovky ukazující, jak zaregistrovat svoji aplikaci storage v Azure AD](./media/storage-auth-aad-app/app-registration.png)

> [!NOTE]
> Když si zaregistrujete aplikaci jako nativní aplikaci, můžete zadat libovolný platný identifikátor URI pro **identifikátor URI pro přesměrování**. Pro nativní aplikace tato hodnota nemá být skutečná adresa URL. Pro webové aplikace identifikátor URI pro přesměrování musí být platný identifikátor URI, protože určuje adresu URL, ke které jsou k dispozici tokeny.

Po zaregistrování vaší aplikace, zobrazí se vám ID aplikace (nebo ID klienta) v části **nastavení**:

![Snímek obrazovky s ID klienta](./media/storage-auth-aad-app/app-registration-client-id.png)

Další informace o registraci aplikace v Azure AD najdete v tématu [integrace aplikací s Azure Active Directory](../../active-directory/develop/quickstart-v2-register-an-app.md).

## <a name="grant-your-registered-app-permissions-to-azure-storage"></a>Registrované aplikaci udělit oprávnění ke službě Azure Storage

V dalším kroku udělení oprávnění aplikace volat rozhraní API služby Azure Storage. Tento krok umožňuje vaší aplikaci k autorizaci požadavků ve službě Azure Storage s Azure AD.

1. Na **přehled** stránka pro registraci aplikace, vyberte **oprávnění k zobrazení rozhraní API**.
1. V **oprávnění k rozhraní API** vyberte **přidat oprávnění** a zvolte **naše organizace bude využívat rozhraní API**.
1. V části **naše organizace bude využívat rozhraní API** části, vyhledejte "Azure Storage" a vyberte **služby Azure Storage** ze seznamu výsledků k zobrazení **žádosti rozhraní API oprávnění** podokno.

    ![Snímek obrazovky znázorňující oprávnění pro úložiště](media/storage-auth-aad-app/registered-app-permissions-1.png)

1. V části **jaký typ oprávnění aplikace vyžaduje?** , Všimněte si, že je k dispozici oprávnění typu **delegovaná oprávnění**. Ve výchozím nastavení je vybraná tato možnost za vás.
1. V **vyberte oprávnění** část **žádosti rozhraní API oprávnění** podokně zaškrtněte políčko vedle položky **user_impersonation**, pak klikněte na tlačítko **přidat oprávnění**.
1. **Oprávnění k rozhraní API** podokně teď zobrazuje, že vaše aplikace Azure AD má přístup k Microsoft Graph a Azure Storage. Oprávnění jsou udělena pro Microsoft Graph automaticky při první registraci vaší aplikace s Azure AD.

    ![Snímek obrazovky registrace oprávnění aplikace](media/storage-auth-aad-app/registered-app-permissions-2.png)

## <a name="libraries-for-token-acquisition"></a>Knihovny pro získání tokenu

Jakmile vaši aplikaci zaregistrovali a udělil jí oprávnění pro přístup k datům v Azure Blob storage nebo Queue storage, můžete přidat kód do vaší aplikace k ověření objektu zabezpečení a získání tokenu OAuth 2.0. K ověření a získání tokenu, můžete použít jednu ze [knihovny ověřování platforma identit Microsoft](../../active-directory/develop/reference-v2-libraries.md) nebo jiné knihovny open source, který podporuje OpenID Connect 1.0. Aplikace pak může použít přístupový token k autorizaci požadavek Azure Blob storage nebo Queue storage.

Seznam scénářů, pro které získávání tokenů se podporuje, najdete v článku [scénáře](https://aka.ms/msal-net-scenarios) část [Microsoft Authentication Library (MSAL) pro .NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) úložiště GitHub.

## <a name="net-code-example-create-a-block-blob"></a>Příklad kódu .NET: Vytvořit objekt blob bloku

Příklad kódu ukazuje, jak získat přístupový token ze služby Azure AD. Přístupový token se používá k ověřování zadaného uživatele a potom budete autorizovat požadavek na vytvoření objektu blob bloku. Pokud chcete získat to ukázka funguje, nejdřív postupujte podle kroků uvedených v předchozí části.

> [!NOTE]
> Jako vlastník účtu služby Azure Storage nejsou automaticky přiřadit oprávnění pro přístup k datům. Je nutné explicitně přiřadit sami roli RBAC pro Azure Storage. Ji můžete přiřadit na úrovni předplatného, skupinu prostředků, účet úložiště nebo kontejner nebo fronty.
>
> Například pro spuštění vzorového kódu, kde jste vlastníkem účtu úložiště a v části vlastní identitu uživatele, musíte přiřadit roli RBAC pro Přispěvatel dat objektu Blob na vás. V opačném případě se nezdaří volání za účelem vytvoření objektu blob se stavovým kódem HTTP 403 (zakázáno). Další informace najdete v tématu [Správa přístupových práv k datům úložiště pomocí RBAC](storage-auth-aad-rbac.md).

### <a name="well-known-values-for-authentication-with-azure-ad"></a>Známé hodnoty pro ověřování pomocí Azure AD

K ověření objektu zabezpečení s využitím Azure AD, budete muset zahrnout některé známé hodnoty v kódu.

#### <a name="azure-ad-authority"></a>Autorita Azure AD

Pro veřejný cloud Microsoftu základní Azure AD autority vypadá takto, kde *id tenanta* je ID tenanta Active Directory (nebo ID adresáře):

`https://login.microsoftonline.com/<tenant-id>/`

ID tenanta identifikuje tenanta Azure AD pro účely ověření. Pokud chcete načíst ID tenanta, postupujte podle kroků uvedených v části s názvem **získání ID tenanta pro Azure Active Directory**.

#### <a name="storage-resource-id"></a>ID prostředku úložiště

ID prostředku Azure Storage použijte k získání tokenu pro ověřování požadavků ve službě Azure Storage:

`https://storage.azure.com/`

### <a name="get-the-tenant-id-for-your-azure-active-directory"></a>Získání ID tenanta pro Azure Active Directory

K získání ID tenanta, postupujte podle těchto kroků:

1. Na webu Azure Portal vyberte služby Active Directory.
2. Klikněte na **Vlastnosti**.
3. Zkopírujte hodnotu identifikátoru GUID stanovené **ID adresáře**. Tato hodnota se označuje také jako ID tenanta.

![Snímek obrazovky ukazující zkopírování ID tenanta](./media/storage-auth-aad-app/aad-tenant-id.png)

## <a name="set-up-a-basic-web-app-to-authenticate-to-azure-ad"></a>Nastavení základní webovou aplikaci pro ověření do služby Azure AD

Pokud vaše aplikace přistupuje k Azure storage, se podobně jménem uživatele. Pokud chcete vyzkoušet tento příklad kódu, je třeba webové aplikace, která se zobrazí výzva se můžete přihlásit pomocí identity Azure AD. Je možné stáhnout [příklad kódu](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2) otestovat základní webové aplikace, který se ověřuje pomocí svého účtu Azure AD.

### <a name="completed-sample"></a>Hotová ukázka

Kompletní funkční verzi ukázkový kód je znázorněno v tomto článku si můžete stáhnout z [Githubu](http://aka.ms/aadstorage). Prostudování a spuštění je kompletní ukázka může být užitečné k pochopení příklady kódu.

### <a name="add-references-and-using-statements"></a>Přidání odkazů a příkazy using  

Ze sady Visual Studio nainstalujte klientské knihovny Azure Storage. Z **nástroje** nabídce vyberte možnost **Správce balíčků Nuget**, pak **Konzola správce balíčků**. Zadejte následující příkazy do okna konzoly nainstalují potřebné balíčky v klientské knihovně Azure Storage pro .NET:

```console
Install-Package Microsoft.Azure.Storage.Blob
Install-Package Microsoft.Azure.Storage.Common
```

V dalším kroku přidejte následující příkazy using do souboru HomeController.cs:

```csharp
using System;
using Microsoft.Identity.Client; //MSAL library for getting the access token
using Microsoft.WindowsAzure.Storage.Auth;
using Microsoft.WindowsAzure.Storage.Blob;
```

### <a name="create-a-block-blob"></a>Vytvořit objekt blob bloku

Přidejte následující fragment kódu pro vytvoření objektu blob bloku:

```csharp
private static async Task<string> CreateBlob(string accessToken)
{
    // Create a blob on behalf of the user
    TokenCredential tokenCredential = new TokenCredential(accessToken);
    StorageCredentials storageCredentials = new StorageCredentials(tokenCredential);

    // Replace the URL below with your storage account URL
    CloudBlockBlob blob =
        new CloudBlockBlob(
            new Uri("https://<storage-account>.blob.core.windows.net/sample-container/Blob1.txt"),
            storageCredentials);
    await blob.UploadTextAsync("Blob created by Azure AD authenticated user.");
    return "Blob successfully created";
}
```

> [!NOTE]
> Povolit operace objektů blob a fronty k tokenu OAuth 2.0, musí používat protokol HTTPS.

V předchozím příkladu zpracovává klientské knihovny .NET autorizaci žádost o vytvoření objektů blob bloku. Klientské knihovny pro Azure Storage pro ostatní jazyky taky autorizaci žádosti za vás. Ale při volání operace služby Azure Storage k tokenu OAuth pomocí rozhraní REST API, pak bude nutné povolit tato žádost pomocí tokenu OAuth.

K volání operací služby objektů Blob a fronty použitím přístupových tokenů OAuth, předejte přístupového tokenu v **autorizace** pomocí hlavičky **nosiče** schéma a 2017-11-09 nebo vyšší jako verzi služby můžete vidět v následujícím příkladu:

```https
GET /container/file.txt HTTP/1.1
Host: mystorageaccount.blob.core.windows.net
x-ms-version: 2017-11-09
Authorization: Bearer eyJ0eXAiOnJKV1...Xd6j
```

### <a name="get-an-oauth-token-from-azure-ad"></a>Získání tokenu OAuth z Azure AD

V dalším kroku přidáte metodu, která žádá token ze služby Azure AD. Token, který požadujete bude jménem uživatele a budeme používat metodu GetTokenOnBehalfOfUser.

K vyžádání tokenu, budete potřebovat následující hodnoty z registrace vaší aplikace

- ID tenanta (nebo adresáře)
- ID klienta (nebo aplikace)
- Identifikátor URI pro přesměrování klienta

Mějte na paměti, pokud jste právě přihlášeni, a jsou žádosti o token `storage.azure.com` prostředků, budete muset uživateli zprostředkovali uživatelského rozhraní, ve kterém může uživatel souhlas taková akce jejich jménem. Pro usnadnění, že je potřeba zachytit `MsalUiRequiredException` a přidávat funkce pro vyžádání souhlasu uživatele, jak je znázorněno v následujícím příkladu:

```csharp
public async Task<IActionResult> Blob()
{
    var scopes = new string[] { "https://storage.azure.com/user_impersonation" };
    try
    {
        var accessToken =
            await _tokenAcquisition.GetAccessTokenOnBehalfOfUser(HttpContext, scopes);
        ViewData["Message"] = await CreateBlob(accessToken);
        return View();
    }
    catch (MsalUiRequiredException ex)
    {
        AuthenticationProperties properties = BuildAuthenticationPropertiesForIncrementalConsent(scopes, ex);
        return Challenge(properties);
    }
}
```

Souhlas se proces udělení povolení k aplikaci přístup k chráněným prostředkům jejich jménem uživatele. Platforma identit Microsoft 2.0 podporuje přírůstkové souhlasu, to znamená, že objekt zabezpečení můžete nejdřív požádat o minimální sadu oprávnění a podle potřeby přidejte oprávnění v čase. Pokud váš kód požádá o přístupový token, určit obor oprávnění, která vaše aplikace potřebuje v daném okamžiku podle v `scope` parametru. Následující metoda vytvoří vlastnosti ověřování pro požadování přírůstkové souhlasu:

```csharp
private AuthenticationProperties BuildAuthenticationPropertiesForIncrementalConsent(string[] scopes, MsalUiRequiredException ex)
{
    AuthenticationProperties properties = new AuthenticationProperties();

    // Set the scopes, including the scopes that ADAL.NET / MSAL.NET need for the Token cache.
    string[] additionalBuildInScopes = new string[] { "openid", "offline_access", "profile" };
    properties.SetParameter<ICollection<string>>(OpenIdConnectParameterNames.Scope, scopes.Union(additionalBuildInScopes).ToList());

    // Attempt to set the login_hint so that the logged-in user is not presented with an account selection dialog.
    string loginHint = HttpContext.User.GetLoginHint();
    if (!string.IsNullOrWhiteSpace(loginHint))
    {
        properties.SetParameter<string>(OpenIdConnectParameterNames.LoginHint, loginHint);

        string domainHint = HttpContext.User.GetDomainHint();
        properties.SetParameter<string>(OpenIdConnectParameterNames.DomainHint, domainHint);
    }

    // Specify any additional claims that are required (for instance, MFA).
    if (!string.IsNullOrEmpty(ex.Claims))
    {
        properties.Items.Add("claims", ex.Claims);
    }

    return properties;
}
```

## <a name="next-steps"></a>Další postup

- Další informace o platformě Microsoft identity, najdete v článku [platforma identit Microsoft](https://docs.microsoft.com/azure/active-directory/develop/).
- Další informace o rolích RBAC pro Azure storage najdete v tématu [Správa přístupových práv k datům úložiště pomocí RBAC](storage-auth-aad-rbac.md).
- Další informace o použití spravované identity pro prostředky Azure pomocí služby Azure Storage, najdete v článku [ověření přístupu k objektům BLOB a fronty s Azure Active Directory a spravovaným identitám pro prostředky Azure](storage-auth-aad-msi.md).
