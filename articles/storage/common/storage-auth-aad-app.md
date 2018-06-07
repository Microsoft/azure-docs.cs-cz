---
title: Ověření pomocí Azure AD z aplikace úložiště (Preview) | Microsoft Docs
description: Ověření pomocí Azure AD z aplikace Azure Storage (Preview).
services: storage
author: tamram
manager: jeconnoc
ms.service: storage
ms.topic: article
ms.date: 05/18/2018
ms.author: tamram
ms.openlocfilehash: 1bf4a8bba3b93c16f67d46f65292709ef2a1bba2
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34660743"
---
# <a name="authenticate-with-azure-ad-from-an-azure-storage-application-preview"></a>Ověření pomocí Azure AD z aplikace Azure Storage (Preview)

Hlavní výhodou použití služby Azure Active Directory (Azure AD) s Azure Storage je, že vaše přihlašovací údaje již nepotřebujete k uložení do vašeho kódu. Místo toho můžete požádat přístupový token OAuth 2.0, z Azure AD. Azure AD zpracovává ověření objektu zabezpečení (uživatele, skupiny nebo instanční objekt) spustíte aplikaci. Pokud je ověřování úspěšné, Azure AD vrátí přístupový token do aplikace a aplikace můžete potom použít přístupový token k autorizaci požadavků na úložiště Azure.

Tento článek ukazuje, jak nakonfigurovat svoji aplikaci pro ověřování s Azure AD. Funkce .NET příklad kódu, ale jiné jazyky pomocí podobný postup.

Než z vaší aplikace Azure Storage, můžete ověřovat objekt zabezpečení, nakonfigurujte nastavení přístupu na základě role řízení (RBAC) pro tento objekt zabezpečení. Úložiště Azure definuje role RBAC, které zahrnuje oprávnění pro kontejnery a fronty. Pokud RBAC role je přiřazená objekt zabezpečení, že objekt zabezpečení se udělí přístup k prostředku. Další informace najdete v tématu [spravovat přístupová práva k úložišti dat pomocí RBAC (Preview)](storage-auth-aad-rbac.md).

Přehled toku grant kódu OAuth 2.0, naleznete v části [tok poskytování udělit oprávnění k přístupu k Azure Active Directory webových aplikací pomocí OAuth 2.0 kód](../../active-directory/develop/active-directory-protocols-oauth-code.md).

> [!IMPORTANT]
> Tato verze preview je určeno pouze pro nevýrobní prostředí. Produkční smlouvy o úrovni služeb (SLA) nebudou dostupné, dokud integrace Azure AD pro Azure Storage je deklarovaná všeobecně dostupná. Pokud pro váš scénář není dosud podporován integrace Azure AD, nadále používat sdílený klíč autorizace nebo tokeny SAS ve svých aplikacích. Další informace o verzi preview najdete v tématu [ověření přístupu k úložišti Azure pomocí služby Azure Active Directory (Preview)](storage-auth-aad.md).
>
> Ve verzi Preview přiřazení rolí pro RBAC může trvat až pět minut, aby se rozšířily změny.

## <a name="register-your-application-with-an-azure-ad-tenant"></a>Registrace aplikace pomocí klienta Azure AD

Prvním krokem při autorizaci přístupu k prostředkům úložiště pomocí Azure AD je registrace klientskou aplikaci v klientovi služby Azure AD. Registrace aplikace umožňuje volat Azure [Active Directory Authentication Library](../../active-directory/active-directory-authentication-libraries.md) (ADAL) z vašeho kódu. Knihovnu ADAL poskytuje rozhraní API pro ověřování s Azure AD z vaší aplikace. Registrace aplikace také umožňuje autorizovat volání z aplikace rozhraní API úložiště Azure se přístupový token.

Při registraci vaší aplikace, můžete zadat informace o vaší aplikaci do služby Azure AD. Potom Azure AD poskytuje ID klienta (také nazývané *ID aplikace*) k přidružení aplikace Azure AD za běhu použít. Další informace o ID klienta, najdete v části [aplikace a služby hlavní objekty ve službě Azure Active Directory](../../active-directory/develop/active-directory-application-objects.md).

Pro registraci aplikace Azure Storage, postupujte podle kroků v [přidání aplikace](../../active-directory/develop/active-directory-integrating-applications.md#adding-an-application) kapitoly [integrace aplikací s Azure Active Directory](../../active-directory/active-directory-integrating-applications.md). Když si zaregistrujete aplikaci jako nativní aplikaci, můžete zadat libovolný platný identifikátor URI pro **identifikátor URI pro přesměrování**. Hodnota nemusí být skutečné koncový bod.

![Snímek obrazovky znázorňující postup registrace aplikace úložiště s Azure AD](./media/storage-auth-aad-app/app-registration.png)

Po aplikaci jste registrováni, uvidíte ID aplikace (nebo ID klienta) v části **nastavení**:

![Snímek obrazovky znázorňující ID klienta](./media/storage-auth-aad-app/app-registration-client-id.png)

Další informace o registraci aplikace s Azure AD najdete v tématu [integrace aplikací s Azure Active Directory](../../active-directory/develop/active-directory-integrating-applications.md). 

## <a name="grant-your-registered-app-permissions-to-azure-storage"></a>Udělení oprávnění vaše registrovaná aplikace do úložiště Azure.

Dále musíte udělit oprávnění aplikace volat rozhraní API úložiště Azure. Tento krok umožňuje vaší aplikaci autorizovat volání do úložiště Azure s Azure AD.

1. V levém navigačním podokně portálu Azure, zvolte **všechny služby**a vyhledejte **registrace aplikace**.
2. Vyhledávání pro název zaregistrovanou aplikaci, kterou jste vytvořili v předchozím kroku.
3. Vyberte registrovaný aplikace a klikněte na **nastavení**. V **přístup pomocí rozhraní API** vyberte **požadovaná oprávnění**.
4. V **požadovaná oprávnění** okně klikněte **přidat** tlačítko.
5. V části **vybrat rozhraní API**, vyhledejte "Azure Storage" a vyberte **Azure Storage** ze seznamu výsledků.

    ![Snímek obrazovky znázorňující oprávnění pro úložiště](media/storage-auth-aad-app/registered-app-permissions-1.png)

6. V části **vyberte oprávnění**, zaškrtněte políčko vedle **přístup k úložišti Azure**a klikněte na tlačítko **vyberte**.
7. Klikněte na **Done** (Hotovo).

**Požadovaných oprávnění** windows nyní zobrazuje, že vaše aplikace Azure AD má přístup k Azure Active Directory a Azure Storage. Oprávnění ke službě Azure AD automaticky při první registraci vaší aplikace s Azure AD.

![Snímek obrazovky znázorňující zprávu zaregistrovat oprávnění aplikací](media/storage-auth-aad-app/registered-app-permissions-2.png)

## <a name="net-code-example-create-a-block-blob"></a>Příklad kódu .NET: Vytvořte objekt blob bloku

Příklad kódu ukazuje, jak získat přístup tokenu z Azure AD. Přístupový token se používá k ověřování zadaného uživatele a potom budete autorizovat požadavek na vytvoření objekt blob bloku. Tato ukázka pracovní získáte nejdřív postupujte podle kroků uvedených v předchozí části.

> [!NOTE]
> Jako vlastníka účtu Azure Storage nejsou automaticky přiřadit oprávnění pro přístup k datům. Je nutné explicitně přiřadit sami role RBAC pro Azure Storage. Můžete ji přiřadit na úrovni předplatného, skupinu prostředků, účet úložiště, nebo kontejneru nebo fronty. 
>
> Například pokud chcete spustit ukázkový kód na účet úložiště, kde jsou vlastníka a v části vlastní identitu uživatele, je nutné přiřadit RBAC role pro přispěvatele Data objektů Blob sami sobě. Volání za účelem vytvoření objektu blob, jinak selže s kódem stavu HTTP 403 (zakázáno). Další informace najdete v tématu [spravovat přístupová práva k úložišti dat pomocí RBAC (Preview)](storage-auth-aad-rbac.md).

### <a name="well-known-values-for-authentication-with-azure-ad"></a>Známé hodnoty pro ověřování s Azure AD

Chcete-li ověřit objekt zabezpečení s Azure AD, zahrnují některé známé hodnoty v kódu.

#### <a name="azure-ad-oauth-endpoint"></a>Koncový bod Azure AD OAuth

Základní Azure AD autority koncový bod OAuth 2.0 je následující, kde *id klienta* je ID tenanta služby Active Directory (nebo ID adresáře):

`https://login.microsoftonline.com/<tenant-id>/oauth2/token`

ID klienta identifikuje klienta Azure AD pro ověřování. Pokud chcete načíst ID klienta, postupujte podle kroků uvedených v **získání ID tenanta pro Azure Active Directory**.

#### <a name="storage-resource-id"></a>ID prostředku úložiště

ID prostředku Azure Storage použijte k získání tokenu pro ověřování požadavků na úložiště Azure:

`https://storage.azure.com/`

### <a name="get-the-tenant-id-for-your-azure-active-directory"></a>Získání ID tenanta pro Azure Active Directory

Chcete-li získat ID klienta, postupujte takto:

1. Na portálu Azure vyberte vaší služby Active Directory.
2. Klikněte na **Vlastnosti**.
3. Zkopírujte hodnotu GUID pro zadaný **ID adresáře**. Tato hodnota se označuje taky jako ID klienta.

![Snímek obrazovky znázorňující postup kopírování ID klienta](./media/storage-auth-aad-app/aad-tenant-id.png)

### <a name="add-references-and-using-statements"></a>Přidejte odkazy a pomocí příkazů  

V sadě Visual Studio instalace verze preview Klientská knihovna pro úložiště Azure. Z **nástroje** nabídce vyberte možnost **Správce balíčků Nuget**, pak **Konzola správce balíčků**. Zadejte následující příkaz do konzoly:

```
Install-Package https://www.nuget.org/packages/WindowsAzure.Storage/9.2.0  
```

V dalším kroku přidejte následující příkazy using do vašeho kódu:

```dotnet
using Microsoft.IdentityModel.Clients.ActiveDirectory; //ADAL client library for getting the access token
using Microsoft.WindowsAzure.Storage.Auth;
using Microsoft.WindowsAzure.Storage.Blob;
```

### <a name="get-an-oauth-token-from-azure-ad"></a>Získání tokenu OAuth z Azure AD

Dále přidejte metodu, která požaduje tokenu z Azure AD. Chcete-li požádat o token, zavolejte [AuthenticationContext.AcquireTokenAsync](https://docs.microsoft.com/dotnet/api/microsoft.identitymodel.clients.activedirectory.authenticationcontext.acquiretokenasync) metoda.

```dotnet
static string GetUserOAuthToken()
{
    const string ResourceId = "https://storage.azure.com/"; // Storage resource endpoint
    const string AuthEndpoint = "https://login.microsoftonline.com/{0}/oauth2/token"; // Azure AD OAuth endpoint
    const string TenantId = "<tenant-id>"; // Tenant or directory ID

    // Construct the authority string from the Azure AD OAuth endpoint and the tenant ID. 
    string authority = string.Format(CultureInfo.InvariantCulture, AuthEndpoint, TenantId);
    AuthenticationContext authContext = new AuthenticationContext(authority);

    // Acquire an access token from Azure AD. 
    AuthenticationResult result = authContext.AcquireTokenAsync(ResourceId, 
                                                                "<client-id>", 
                                                                new Uri(@"<client-redirect-uri>"), 
                                                                new PlatformParameters(PromptBehavior.Auto)).Result;

    return result.AccessToken;
}
```

### <a name="create-the-block-blob"></a>Vytvořte objekt blob bloku

Nakonec použijte přístupový token pro vytvoření nové přihlašovací údaje úložiště a použít tyto přihlašovací údaje k vytvoření objektu blob:

```dotnet
// Get the access token.
string accessToken = GetUserOAuthToken();

// Use the access token to create the storage credentials.
TokenCredential tokenCredential = new TokenCredential(accessToken);
StorageCredentials storageCredentials = new StorageCredentials(tokenCredential);

// Create a block blob using those credentials
CloudBlockBlob blob = new CloudBlockBlob(new Uri("https://storagesamples.blob.core.windows.net/sample-container/Blob1.txt"), storageCredentials);
```

> [!NOTE]
> Integrace služby Azure AD s Azure Storage vyžaduje pomocí protokolu HTTPS pro operace úložiště Azure.

## <a name="next-steps"></a>Další postup

- Další informace o rolích RBAC pro Azure storage najdete v tématu [spravovat přístupová práva k úložišti dat pomocí RBAC (Preview)](storage-auth-aad-rbac.md).
- Další informace o použití identita spravované služby s Azure Storage najdete v tématu [ověřit s Azure AD z Azure spravované služby Identity (Preview)](storage-auth-aad-msi.md).
- Další postupy k přihlášení do příkazového řádku Azure CLI a prostředí PowerShell s Azure AD identity najdete v tématu [používat Azure AD identity pro přístup k Azure Storage pomocí rozhraní CLI nebo Powershellu (Preview)](storage-auth-aad-script.md).
- Další informace o integraci služby Azure AD pro front a objektů BLOB služby Azure najdete v tématu blog týmu Azure Storage, post, [uvedení ověřování Preview služby Azure AD pro Azure Storage](https://azure.microsoft.com/blog/announcing-the-preview-of-aad-authentication-for-storage/).



