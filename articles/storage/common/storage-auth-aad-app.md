---
title: Ověřování pomocí Azure Active Directory pro přístup k datům objektu blob a frontu z vašich aplikací (Preview) | Dokumentace Microsoftu
description: Pomocí Azure Active Directory k ověřování z v rámci aplikace a pak autorizaci požadavků na objekty BLOB a fronty (Preview).
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 11/21/2018
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: 4c39f47169071ceb9430f419a144e11f425d65b0
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/30/2019
ms.locfileid: "55240722"
---
# <a name="authenticate-with-azure-active-directory-from-an-application-for-access-to-blobs-and-queues-preview"></a>Ověřování pomocí Azure Active Directory z aplikace pro přístup k objektům BLOB a fronty (Preview)

Hlavní výhodou služby Azure Active Directory (Azure AD) pomocí služby Azure Storage je, že vaše přihlašovací údaje už nemusí být uloženy ve vašem kódu. Místo toho můžete požádat přístupového tokenu OAuth 2.0 ze služby Azure AD. Azure AD, zpracovává ověřování objektu zabezpečení (uživatele, skupiny nebo instanční objekt služby) spuštění aplikace. Pokud je ověřování úspěšné, Azure AD vrací přístupový token k aplikaci a aplikace můžete potom použít přístupový token k autorizaci požadavků ve službě Azure Storage.

Tento článek ukazuje, jak nakonfigurovat svoji aplikaci pro ověřování pomocí Azure AD. Funkce .NET příklad kódu, ale jiných jazycích používat podobný přístup.

Před ověření objektu zabezpečení z vaší aplikace Azure Storage, nakonfigurujte nastavení řízení přístupu na základě role pro tento objekt zabezpečení. Azure Storage definuje role RBAC, které zahrnuje oprávnění pro kontejnery a fronty. Pokud RBAC role je přiřazena k objektu zabezpečení, tento objekt zabezpečení se udělí přístup k prostředku. Další informace najdete v tématu [Správa přístupových práv k datům úložiště pomocí RBAC (Preview)](storage-auth-aad-rbac.md).

Přehled toku přidělení kódu OAuth 2.0, naleznete v tématu [autorizovat přístup k Azure Active Directory webovým aplikacím pomocí OAuth 2.0 kódu udělit tok](../../active-directory/develop/v1-protocols-oauth-code.md).

[!INCLUDE [storage-auth-aad-note-include](../../../includes/storage-auth-aad-note-include.md)]

## <a name="assign-an-rbac-role-to-an-azure-ad-security-principal"></a>Přiřaďte roli RBAC pro objekt zabezpečení Azure AD

K ověření objektu zabezpečení z vaší aplikace Azure Storage, nejprve nakonfigurujte nastavení řízení přístupu na základě rolí pro tento objekt zabezpečení. Azure Storage definuje role RBAC, které zahrnuje oprávnění pro kontejnery a fronty. Pokud RBAC role je přiřazena k objektu zabezpečení, tento objekt zabezpečení se udělí přístup k prostředku. Další informace najdete v tématu [Správa přístupových práv k datům objektů Blob v Azure a fronty pomocí RBAC (Preview)](storage-auth-aad-rbac.md).

## <a name="register-your-application-with-an-azure-ad-tenant"></a>Registrace aplikace pomocí tenanta služby Azure AD

Prvním krokem při používání služby Azure AD k autorizaci přístupu k prostředkům úložiště se registruje klientské aplikace v tenantovi Azure AD. Registrace aplikace umožňuje volat Azure [Active Directory Authentication Library](../../active-directory/active-directory-authentication-libraries.md) (ADAL) z uživatelského kódu. ADAL poskytuje rozhraní API pro ověřování pomocí Azure AD z vaší aplikace. Registrace aplikace také umožňuje povolit volání z aplikace API pro Azure Storage s přístupovým tokenem.

Při registraci vaší aplikace zadejte informace o aplikaci do služby Azure AD. Azure AD pak poskytuje ID klienta (také nazývané *ID aplikace*), který používáte k aplikaci přidružit k Azure AD za běhu. Další informace o ID klienta najdete v tématu [aplikace a instanční objekty v Azure Active Directory](../../active-directory/develop/app-objects-and-service-principals.md).

Pokud chcete zaregistrovat aplikaci Azure Storage, postupujte podle kroků v [přidáním aplikace](../../active-directory/develop/quickstart-v1-add-azure-ad-app.md) tématu [integrace aplikací s Azure Active Directory](../../active-directory/active-directory-integrating-applications.md). Když si zaregistrujete aplikaci jako nativní aplikaci, můžete zadat libovolný platný identifikátor URI pro **identifikátor URI pro přesměrování**. Hodnota nemusí být skutečný koncový bod.

![Snímek obrazovky ukazující, jak zaregistrovat svoji aplikaci storage v Azure AD](./media/storage-auth-aad-app/app-registration.png)

Po zaregistrování vaší aplikace, zobrazí se vám ID aplikace (nebo ID klienta) v části **nastavení**:

![Snímek obrazovky ukazující ID klienta](./media/storage-auth-aad-app/app-registration-client-id.png)

Další informace o registraci aplikace v Azure AD najdete v tématu [integrace aplikací s Azure Active Directory](../../active-directory/develop/quickstart-v1-integrate-apps-with-azure-ad.md). 

## <a name="grant-your-registered-app-permissions-to-azure-storage"></a>Registrované aplikaci udělit oprávnění ke službě Azure Storage

Dále je třeba udělit oprávnění aplikace volat rozhraní API služby Azure Storage. Tento krok umožňuje vaší aplikaci povolit volání do služby Azure Storage s Azure AD.

1. V levém navigačním podokně na webu Azure portal zvolte **všechny služby**a vyhledejte **registrace aplikací**.
2. Vyhledat název zaregistrovanou aplikaci, kterou jste vytvořili v předchozím kroku.
3. Vyberte registrovaný aplikaci a klikněte na tlačítko **nastavení**. V **přístup přes rozhraní API** vyberte **požadovaná oprávnění**.
4. V **požadovaná oprávnění** okna, klikněte na tlačítko **přidat** tlačítko.
5. V části **vyberte rozhraní API**, vyhledejte "Azure Storage" a vyberte **služby Azure Storage** ze seznamu výsledků.

    ![Snímek obrazovky znázorňující oprávnění pro úložiště](media/storage-auth-aad-app/registered-app-permissions-1.png)

6. V části **vyberte oprávnění**, zaškrtněte políčko vedle položky **přístup ke službě Azure Storage**a klikněte na tlačítko **vyberte**.
7. Klikněte na **Done** (Hotovo).

**Požadovaná oprávnění** windows teď zobrazuje, že vaše aplikace Azure AD má přístup k Azure Active Directory a Azure Storage. Jsou udělena oprávnění ke službě Azure AD automaticky při první registraci vaší aplikace s Azure AD.

![Snímek obrazovky znázorňující zprávu zaregistrovat oprávnění aplikace](media/storage-auth-aad-app/registered-app-permissions-2.png)

## <a name="net-code-example-create-a-block-blob"></a>Příklad kódu .NET: Vytvořit objekt blob bloku

Příklad kódu ukazuje, jak získat přístupový token ze služby Azure AD. Přístupový token se používá k ověřování zadaného uživatele a potom budete autorizovat požadavek na vytvoření objektu blob bloku. Pokud chcete získat to ukázka funguje, nejdřív postupujte podle kroků uvedených v předchozí části.

> [!NOTE]
> Jako vlastník účtu služby Azure Storage nejsou automaticky přiřadit oprávnění pro přístup k datům. Je nutné explicitně přiřadit sami roli RBAC pro Azure Storage. Ji můžete přiřadit na úrovni předplatného, skupinu prostředků, účet úložiště nebo kontejner nebo fronty. 
>
> Například pro spuštění vzorového kódu, kde jste vlastníkem účtu úložiště a v části vlastní identitu uživatele, musíte přiřadit roli RBAC pro Přispěvatel dat objektu Blob na vás. V opačném případě se nezdaří volání za účelem vytvoření objektu blob se stavovým kódem HTTP 403 (zakázáno). Další informace najdete v tématu [Správa přístupových práv k datům úložiště pomocí RBAC (Preview)](storage-auth-aad-rbac.md).

### <a name="well-known-values-for-authentication-with-azure-ad"></a>Známé hodnoty pro ověřování pomocí Azure AD

K ověření objektu zabezpečení s využitím Azure AD, budete muset zahrnout některé známé hodnoty v kódu.

#### <a name="azure-ad-authority"></a>Autorita Azure AD

Pro veřejný cloud Microsoftu základní Azure AD autority vypadá takto, kde *id tenanta* je ID tenanta Active Directory (nebo ID adresáře):

`https://login.microsoftonline.com/<tenant-id>/`

ID tenanta identifikuje tenanta Azure AD pro účely ověření. Pokud chcete načíst ID tenanta, postupujte podle kroků uvedených v **získání ID tenanta pro Azure Active Directory**.

#### <a name="storage-resource-id"></a>ID prostředku úložiště

ID prostředku Azure Storage použijte k získání tokenu pro ověření požadavků ve službě Azure Storage:

`https://storage.azure.com/`

### <a name="get-the-tenant-id-for-your-azure-active-directory"></a>Získání ID tenanta pro Azure Active Directory

K získání ID tenanta, postupujte podle těchto kroků:

1. Na webu Azure Portal vyberte služby Active Directory.
2. Klikněte na **Vlastnosti**.
3. Zkopírujte hodnotu identifikátoru GUID stanovené **ID adresáře**. Tato hodnota se označuje také jako ID tenanta.

![Snímek obrazovky ukazující zkopírování ID tenanta](./media/storage-auth-aad-app/aad-tenant-id.png)

### <a name="add-references-and-using-statements"></a>Přidání odkazů a příkazy using  

V sadě Visual Studio nainstalujte klientské knihovny Azure Storage ve verzi preview. Z **nástroje** nabídce vyberte možnost **Správce balíčků Nuget**, pak **Konzola správce balíčků**. Zadejte následující příkaz do konzoly nainstalovat nejnovější verzi klientské knihovny pro .NET:

```
Install-Package WindowsAzure.Storage
```

Také nainstalujte nejnovější verzi ADAL:

```
Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory
```

V dalším kroku přidejte následující příkazy using do kódu:

```dotnet
using System.Globalization;
using Microsoft.IdentityModel.Clients.ActiveDirectory; //ADAL client library for getting the access token
using Microsoft.WindowsAzure.Storage.Auth;
using Microsoft.WindowsAzure.Storage.Blob;
```

### <a name="get-an-oauth-token-from-azure-ad"></a>Získání tokenu OAuth z Azure AD

V dalším kroku přidáte metodu, která žádá token ze služby Azure AD. Chcete-li požádat o token, zavolejte [AuthenticationContext.AcquireTokenAsync](https://docs.microsoft.com/dotnet/api/microsoft.identitymodel.clients.activedirectory.authenticationcontext.acquiretokenasync) metody. Ujistěte se, že máte následující hodnoty z kroků, které jste používali v předchozích krocích:

- ID klienta (adresář)
- ID klienta (aplikace)
- Identifikátor URI pro přesměrování klienta

```dotnet
static string GetUserOAuthToken()
{
    const string ResourceId = "https://storage.azure.com/";
    const string AuthInstance = "https://login.microsoftonline.com/{0}/";
    const string TenantId = "<tenant-id>"; // Tenant or directory ID

    // Construct the authority string from the Azure AD OAuth endpoint and the tenant ID. 
    string authority = string.Format(CultureInfo.InvariantCulture, AuthInstance, TenantId);
    AuthenticationContext authContext = new AuthenticationContext(authority);

    // Acquire an access token from Azure AD. 
    AuthenticationResult result = authContext.AcquireTokenAsync(ResourceId, 
                                                                "<client-id>", 
                                                                new Uri(@"<client-redirect-uri>"), 
                                                                new PlatformParameters(PromptBehavior.Auto)).Result;

    return result.AccessToken;
}
```

### <a name="create-the-block-blob"></a>Vytvořit objekt blob bloku

Nakonec použijte přístupový token k vytvoření nových přihlašovacích údajů úložiště a použít tyto přihlašovací údaje k vytvoření objektu blob:

```dotnet
// Get the access token.
string accessToken = GetUserOAuthToken();

// Use the access token to create the storage credentials.
TokenCredential tokenCredential = new TokenCredential(accessToken);
StorageCredentials storageCredentials = new StorageCredentials(tokenCredential);

// Create a block blob using those credentials
CloudBlockBlob blob = new CloudBlockBlob(new Uri("https://storagesamples.blob.core.windows.net/sample-container/Blob1.txt"), storageCredentials);

blob.UploadTextAsync("Blob created by Azure AD authenticated user.");
```

> [!NOTE]
> Integrace Azure AD pomocí služby Azure Storage vyžaduje použití protokolu HTTPS pro operace služby Azure Storage.

V předchozím příkladu zpracovává klientské knihovny .NET autorizaci žádost o vytvoření objektů blob bloku. Ostatní klientské knihovny pro úložiště taky autorizaci žádosti za vás. Ale při volání operace služby Azure Storage k tokenu OAuth pomocí rozhraní REST API, pak bude nutné povolit tato žádost pomocí tokenu OAuth.   

K volání operací služby objektů Blob a fronty použitím přístupových tokenů OAuth, předejte přístupového tokenu v **autorizace** pomocí hlavičky **nosiče** schéma a 2017-11-09 nebo vyšší jako verzi služby můžete vidět v následujícím příkladu:

```
GET /container/file.txt HTTP/1.1
Host: mystorageaccount.blob.core.windows.net
x-ms-version: 2017-11-09
Authorization: Bearer eyJ0eXAiOnJKV1...Xd6j
```

Další informace o povolení operací služby Azure Storage z REST, naleznete v tématu [ověřování pomocí Azure Active Directory (Preview)](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory).

## <a name="next-steps"></a>Další postup

- Další informace o rolích RBAC pro Azure storage najdete v tématu [Správa přístupových práv k datům úložiště pomocí RBAC (Preview)](storage-auth-aad-rbac.md).
- Další informace o použití spravované identity pro prostředky Azure pomocí služby Azure Storage, najdete v článku [ověřit přístup k objektům BLOB a fronty Azure spravovaných identit pro prostředky Azure (Preview)](storage-auth-aad-msi.md).
- Zjistěte, jak k přihlášení do Powershellu a Azure CLI s Azure AD identity, najdete v článku [pomocí identity Azure AD pro přístup k Azure Storage s využitím rozhraní příkazového řádku nebo Powershellu (Preview)](storage-auth-aad-script.md).
- Další informace o integraci služby Azure AD pro objekty BLOB Azure a front, najdete v článku na blogu týmu Azure Storage účtovat, [oznamujeme vydání verze Preview služby Azure AD Authentication pro službu Azure Storage](https://azure.microsoft.com/blog/announcing-the-preview-of-aad-authentication-for-storage/).



