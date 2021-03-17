---
title: Použití Azure Active Directory k ověřování řešení pro správu dávek
description: Prozkoumejte použití Azure Active Directory k ověření z aplikací, které používají knihovnu Batch Management .NET.
ms.topic: how-to
ms.date: 04/27/2017
ms.custom: has-adal-ref, devx-track-csharp
ms.openlocfilehash: ff49d5e88df7c56ed4dee0685f09e45eb372aa5c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "88930209"
---
# <a name="authenticate-batch-management-solutions-with-active-directory"></a>Ověřování řešení Batch Management se službou Active Directory

Aplikace, které volají službu správy Azure Batch, se ověřují pomocí [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md) (Azure AD). Azure AD je cloudová služba pro správu identit založená na cloudu od Microsoftu. Azure využívá Azure AD k ověřování svých zákazníků, správců služeb a uživatelů organizace.

Knihovna Batch Management .NET zpřístupňuje typy pro práci s účty Batch, klíči účtu, aplikacemi a balíčky aplikací. Knihovna Batch Management .NET je klientem poskytovatele prostředků Azure a používá se společně se [Azure Resource Manager](../azure-resource-manager/management/overview.md) ke správě těchto prostředků prostřednictvím kódu programu. Služba Azure AD se vyžaduje k ověřování požadavků provedených prostřednictvím libovolného klienta poskytovatele prostředků Azure, včetně knihovny Batch Management .NET Library a až po Azure Resource Manager.

V tomto článku se podíváme na použití Azure AD k ověřování z aplikací, které používají knihovnu Batch Management .NET. Ukážeme, jak používat Azure AD k ověřování Správce předplatného nebo spolusprávce s použitím integrovaného ověřování. Pomocí ukázkového projektu [službu AccountManagement](https://github.com/Azure/azure-batch-samples/tree/master/CSharp/AccountManagement) , který je k dispozici na GitHubu, si můžete projít pomocí Azure AD s knihovnou Batch Management .NET.

Další informace o používání knihovny Batch Management .NET a ukázky službu AccountManagement najdete v tématu [Správa účtů a kvót služby Batch pomocí klientské knihovny pro správu služby Batch pro .NET](batch-management-dotnet.md).

## <a name="register-your-application-with-azure-ad"></a>Registrace aplikace ve službě Azure AD

[Knihovna Azure Active Directory Authentication Library](../active-directory/azuread-dev/active-directory-authentication-libraries.md) (ADAL) poskytuje programový rozhraní pro Azure AD pro použití v rámci svých aplikací. Pokud chcete volat ADAL z vaší aplikace, musíte aplikaci zaregistrovat v tenantovi Azure AD. Při registraci aplikace zadáte Azure AD s informacemi o vaší aplikaci, včetně názvu v rámci tenanta Azure AD. Azure AD pak poskytuje ID aplikace, které použijete k přidružení aplikace k Azure AD za běhu. Další informace o ID aplikace najdete [v tématu aplikace a objekty zabezpečení služby v Azure Active Directory](../active-directory/develop/app-objects-and-service-principals.md).

Pokud chcete zaregistrovat ukázkovou aplikaci službu AccountManagement, postupujte podle kroků v části [Přidání aplikace](../active-directory/develop/quickstart-register-app.md) v tématu [integrace aplikací s Azure Active Directory](../active-directory/develop/quickstart-register-app.md). Zadejte **nativní klientskou aplikaci** pro daný typ aplikace. Oborový standard OAuth 2,0 identifikátor URI pro **identifikátor URI přesměrování** je `urn:ietf:wg:oauth:2.0:oob` . Můžete ale pro identifikátor URI přesměrování zadat jakýkoli platný identifikátor URI (například `http://myaccountmanagementsample` ) **Redirect URI**, protože nemusí být skutečným koncovým bodem.

![Přidání aplikace](./media/batch-aad-auth-management/app-registration-management-plane.png)

Po dokončení procesu registrace uvidíte ID aplikace a ID objektu (instanční objekt) uvedené pro vaši aplikaci.

![Dokončený proces registrace](./media/batch-aad-auth-management/app-registration-client-id.png)

## <a name="grant-the-azure-resource-manager-api-access-to-your-application"></a>Udělení přístupu k aplikaci Azure Resource Manager API

V dalším kroku budete muset delegovat přístup k vaší aplikaci na rozhraní Azure Resource Manager API. Identifikátor Azure AD pro rozhraní Správce prostředků API je **rozhraní API pro správu služeb Windows Azure**.

Na webu Azure Portal postupujte podle těchto pokynů:

1. V levém navigačním podokně Azure Portal vyberte **všechny služby**, klikněte na **Registrace aplikací**a pak klikněte na **Přidat**.
2. V seznamu registrací aplikací vyhledejte název vaší aplikace:

    ![Vyhledejte název vaší aplikace.](./media/batch-aad-auth-management/search-app-registration.png)

3. Zobrazte okno **Nastavení** . V části **přístup k rozhraní API** vyberte **požadovaná oprávnění**.
4. Kliknutím na **Přidat** přidejte nové požadované oprávnění.
5. V kroku 1 zadejte **Windows Azure rozhraní API pro správu služeb**, vyberte toto rozhraní API ze seznamu výsledků a klikněte na tlačítko **Vybrat** .
6. V kroku 2 zaškrtněte políčko vedle **přístupu k modelu nasazení Azure Classic jako uživatelé organizace**a klikněte na tlačítko **Vybrat** .
7. Klikněte na tlačítko **Hotovo** .

Okno **požadovaná oprávnění** teď zobrazuje, že oprávnění k vaší aplikaci se udělují rozhraním API ADAL i správce prostředků. Při první registraci aplikace v Azure AD jsou oprávnění ve výchozím nastavení udělena službě ADAL.

![Delegování oprávnění k rozhraní Azure Resource Manager API](./media/batch-aad-auth-management/required-permissions-management-plane.png)

## <a name="azure-ad-endpoints"></a>Koncové body služby Azure AD

K ověření vašich řešení pro správu služby Batch ve službě Azure AD budete potřebovat dva dobře známé koncové body.

- **Společný koncový bod Azure AD** poskytuje obecné rozhraní pro shromažďování přihlašovacích údajů, když není k dispozici konkrétní tenant, jako v případě integrovaného ověřování:

    `https://login.microsoftonline.com/common`

- **Koncový bod Azure Resource Manager** slouží k získání tokenu pro ověřování požadavků do služby Batch Management:

    `https://management.core.windows.net/`

Ukázková aplikace službu AccountManagement definuje konstanty pro tyto koncové body. Nechte tyto konstanty beze změny:

```csharp
// Azure Active Directory "common" endpoint.
private const string AuthorityUri = "https://login.microsoftonline.com/common";
// Azure Resource Manager endpoint
private const string ResourceUri = "https://management.core.windows.net/";
```

## <a name="reference-your-application-id"></a>Odkaz na ID aplikace

Klientská aplikace používá ID aplikace (také označované jako ID klienta) pro přístup k Azure AD za běhu. Po zaregistrování aplikace v Azure Portal aktualizujte kód tak, aby pro registrovanou aplikaci používal ID aplikace poskytované službou Azure AD. V ukázkové aplikaci službu AccountManagement zkopírujte ID aplikace z Azure Portal do příslušné konstanty:

```csharp
// Specify the unique identifier (the "Client ID") for your application. This is required so that your
// native client application (i.e. this sample) can access the Microsoft Graph API. For information
// about registering an application in Azure Active Directory, please see "Register an application with the Microsoft identity platform" here:
// https://docs.microsoft.com/azure/active-directory/develop/quickstart-register-app
private const string ClientId = "<application-id>";
```
Zkopírujte také identifikátor URI přesměrování, který jste zadali během procesu registrace. Identifikátor URI přesměrování zadaný ve vašem kódu se musí shodovat s identifikátorem URI přesměrování, který jste zadali při registraci aplikace.

```csharp
// The URI to which Azure AD will redirect in response to an OAuth 2.0 request. This value is
// specified by you when you register an application with AAD (see ClientId comment). It does not
// need to be a real endpoint, but must be a valid URI (e.g. https://accountmgmtsampleapp).
private const string RedirectUri = "http://myaccountmanagementsample";
```

## <a name="acquire-an-azure-ad-authentication-token"></a>Získání ověřovacího tokenu Azure AD

Když v tenantovi Azure AD zaregistrujete ukázku službu AccountManagement a aktualizujete ukázkový zdrojový kód pomocí vašich hodnot, je ukázka připravena k ověření pomocí Azure AD. Při spuštění ukázky se ADAL pokusí získat ověřovací token. V tomto kroku vás vyzve k zadání přihlašovacích údajů Microsoftu:

```csharp
// Obtain an access token using the "common" AAD resource. This allows the application
// to query AAD for information that lies outside the application's tenant (such as for
// querying subscription information in your Azure account).
AuthenticationContext authContext = new AuthenticationContext(AuthorityUri);
AuthenticationResult authResult = authContext.AcquireToken(ResourceUri,
                                                        ClientId,
                                                        new Uri(RedirectUri),
                                                        PromptBehavior.Auto);
```

Po zadání přihlašovacích údajů může ukázková aplikace pokračovat ve vydávání ověřených požadavků do služby Batch Management.

## <a name="next-steps"></a>Další kroky

- Další informace o spuštění [ukázkové aplikace službu AccountManagement](https://github.com/Azure/azure-batch-samples/tree/master/CSharp/AccountManagement)najdete v tématu [Správa účtů a kvót služby Batch pomocí klientské knihovny pro správu služby Batch pro .NET](batch-management-dotnet.md).
- Další informace o Azure AD najdete v dokumentaci k [Azure Active Directory](../active-directory/index.yml).
- Podrobné příklady ukazující, jak používat ADAL jsou k dispozici v knihovně [ukázek kódu Azure](https://azure.microsoft.com/resources/samples/?service=active-directory) .
- Pokud chcete ověřit aplikace služby Batch pomocí Azure AD, přečtěte si téma [ověřování řešení služby Batch pomocí služby Active Directory](batch-aad-auth.md).
