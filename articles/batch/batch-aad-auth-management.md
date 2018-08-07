---
title: Použití Azure Active Directory k ověření řešení pro správu služby Batch | Dokumentace Microsoftu
description: Aplikace vytvořené pomocí Azure resource Manageru a poskytovatele prostředků služby Batch ověřování ve službě Azure AD.
services: batch
documentationcenter: .net
author: dlepow
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 04/27/2017
ms.author: danlep
ms.openlocfilehash: 698212ce1f4e88cda741a78030023f3acdeee9f0
ms.sourcegitcommit: 615403e8c5045ff6629c0433ef19e8e127fe58ac
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/06/2018
ms.locfileid: "39576079"
---
# <a name="authenticate-batch-management-solutions-with-active-directory"></a>Ověření řešení pro správu služby Batch pomocí Active Directory

Aplikace, které volají služby Azure Batch Management ověření pomocí [Azure Active Directory][aad_about] (Azure AD). Azure AD je Microsoftu pro více tenantů cloudový adresář a služba pro správu identit. Azure sama používá k ověření své zákazníky, správci služeb a organizace uživatele Azure AD.

Knihovna Batch Management .NET poskytuje typy pro práci s účty Batch, klíče účtu, aplikace a balíčky aplikací. Knihovna Batch Management .NET je klient poskytovatele prostředků Azure a používá se společně s [Azure Resource Manageru] [ resman_overview] tyto zdroje spravovat prostřednictvím kódu programu. Azure AD je potřeba ověřit požadavky provedené přes všechny klienty poskytovatele prostředků Azure, včetně knihovny Batch Management .NET, prostřednictvím [Azure Resource Manageru][resman_overview].

V tomto článku se podíváme na používání služby Azure AD pro ověření z aplikace, které používají knihovny Batch Management .NET. Vám ukážeme, jak používat Azure AD pro ověření předplatného správce nebo spolusprávce, pomocí integrovaného ověřování. Používáme [AccountManagment] [ acct_mgmt_sample] ukázkového projektu a k dispozici na na Githubu, který provede pomocí knihovny Batch Management .NET služby Azure AD.

Další informace o použití knihovny Batch Management .NET a AccountManagement ukázku najdete v tématu [Správa služby Batch účtů a kvót pomocí klientské knihovny správy služby Batch pro .NET](batch-management-dotnet.md).

## <a name="register-your-application-with-azure-ad"></a>Registrace aplikace v Azure AD

Azure [Active Directory Authentication Library][aad_adal] (ADAL) poskytuje programovací rozhraní do služby Azure AD pro použití v rámci vaší aplikace. Pro volání ADAL z aplikace, je nutné zaregistrovat aplikaci v tenantovi Azure AD. Při registraci vaší aplikace, je třeba zadat Azure AD s informacemi o vaší aplikaci, včetně názvu, v rámci tenanta Azure AD. Azure AD pak poskytuje ID aplikace, který použijete k aplikaci přidružit k Azure AD za běhu. Další informace o ID aplikace, najdete v článku [aplikace a instanční objekty v Azure Active Directory](../active-directory/develop/app-objects-and-service-principals.md).

Zaregistrovat ukázkovou aplikaci AccountManagement, postupujte podle kroků v [přidáním aplikace](../active-directory/develop/quickstart-v1-integrate-apps-with-azure-ad.md#adding-an-application) tématu [integrace aplikací s Azure Active Directory] [ aad_integrate]. Zadejte **nativní klientská aplikace** pro typ aplikace. V oboru standardní identifikátor URI OAuth 2.0 pro **identifikátor URI pro přesměrování** je `urn:ietf:wg:oauth:2.0:oob`. Ale můžete zadat libovolný platný identifikátor URI (například `http://myaccountmanagementsample`) pro **identifikátor URI pro přesměrování**, protože nemusí být skutečný koncový bod:

![](./media/batch-aad-auth-management/app-registration-management-plane.png)

Po dokončení procesu registrace uvidíte ID aplikace a ID objektu (instanční objekt) uvedené pro vaši aplikaci.  

![](./media/batch-aad-auth-management/app-registration-client-id.png)

## <a name="grant-the-azure-resource-manager-api-access-to-your-application"></a>Udělit přístup k rozhraní API Azure Resource Manageru pro vaši aplikaci

V dalším kroku budete potřebovat pro delegování přístupu k aplikaci API Azure Resource Manageru. Identifikátor služby Azure AD pro rozhraní API Resource Manageru je **Windows Azure Service Management API**.

Postupujte podle těchto kroků na webu Azure Portal:

1. V levém navigačním podokně na webu Azure portal zvolte **všechny služby**, klikněte na tlačítko **registrace aplikací**a klikněte na tlačítko **přidat**.
2. Hledat název vaší aplikace v seznamu registrací aplikace:

    ![Vyhledejte název vaší aplikace](./media/batch-aad-auth-management/search-app-registration.png)

3. Zobrazení **nastavení** okno. V **přístup přes rozhraní API** vyberte **požadovaná oprávnění**.
4. Klikněte na tlačítko **přidat** přidat nové požadované oprávnění. 
5. V kroku 1, zadejte **Windows Azure Service Management API**, toto rozhraní API vyberte ze seznamu výsledků a klikněte na tlačítko **vyberte** tlačítko.
6. V kroku 2, zaškrtněte políčko vedle položky **modelu nasazení classic přístup k Azure jako uživatelé organizace**a klikněte na tlačítko **vyberte** tlačítko.
7. Klikněte na tlačítko **provádí** tlačítko.

**Požadovaná oprávnění** okno se teď zobrazí, že k ADAL a rozhraní API Resource Manageru jsou udělena oprávnění pro vaši aplikaci. Oprávnění jsou udělena adal ve výchozím nastavení při první registraci vaší aplikace s Azure AD.

![Delegování oprávnění k rozhraní API Azure Resource Manageru](./media/batch-aad-auth-management/required-permissions-management-plane.png)

## <a name="azure-ad-endpoints"></a>Koncové body Azure AD

Pro ověření vašeho řešení pro správu služby Batch pomocí Azure AD, budete potřebovat dva koncové body služby dobře známé.

- **Společný koncový bod Azure AD** poskytuje obecný pověření shromažďování rozhraní, když není k dispozici konkrétního tenanta, třeba v případě integrované ověřování:

    `https://login.microsoftonline.com/common`

- **Koncový bod Azure Resource Manageru** slouží k získání tokenu pro ověřování požadavků na službu Batch management:

    `https://management.core.windows.net/`

Ukázková aplikace AccountManagement definuje konstanty pro tyto koncové body. Ponechte tyto konstanty beze změny:

```csharp
// Azure Active Directory "common" endpoint.
private const string AuthorityUri = "https://login.microsoftonline.com/common";
// Azure Resource Manager endpoint 
private const string ResourceUri = "https://management.core.windows.net/";
```

## <a name="reference-your-application-id"></a>Referenční ID vaší aplikace 

Klientská aplikace používá ID aplikace (také označované jako ID klienta) pro přístup k Azure AD za běhu. Po registraci vaší aplikace na webu Azure Portal aktualizujte kód Refaktorovat pro použití ID aplikace Azure AD pro registraci aplikace k dispozici. V ukázkové aplikaci AccountManagement zkopírujte si ID aplikace z portálu Azure portal k příslušné – konstanta:

```csharp
// Specify the unique identifier (the "Client ID") for your application. This is required so that your
// native client application (i.e. this sample) can access the Microsoft Azure AD Graph API. For information
// about registering an application in Azure Active Directory, please see "Adding an Application" here:
// https://azure.microsoft.com/documentation/articles/active-directory-integrating-applications/
private const string ClientId = "<application-id>";
```
Zkopírujte také přesměrovat identifikátor URI, který jste zadali během procesu registrace. Zadaný identifikátor URI ve vašem kódu pro přesměrování musí odpovídat přesměrování identifikátor URI, který jste zadali při registraci aplikace.

```csharp
// The URI to which Azure AD will redirect in response to an OAuth 2.0 request. This value is
// specified by you when you register an application with AAD (see ClientId comment). It does not
// need to be a real endpoint, but must be a valid URI (e.g. https://accountmgmtsampleapp).
private const string RedirectUri = "http://myaccountmanagementsample";
```

## <a name="acquire-an-azure-ad-authentication-token"></a>Získání tokenu ověřování Azure AD

Po registraci AccountManagement ukázku v tenantovi Azure AD a aktualizovat zdrojový kód ukázkové hodnoty, vzorek je připraven k ověření pomocí služby Azure AD. Při spuštění ukázky ADAL se pokouší získat ověřovací token. V tomto kroku se vás vyzve k zadání přihlašovacích údajů Microsoft: 

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

Po zadání údajů ukázkové aplikace můžete přejít k zasílání požadavků na ověření do služby Batch management. 

## <a name="next-steps"></a>Další postup

Další informace o spuštění [AccountManagement ukázkovou aplikaci][acct_mgmt_sample], naleznete v tématu [Správa služby Batch účtů a kvót pomocí klientské knihovny správy služby Batch pro .NET](batch-management-dotnet.md) .

Další informace o službě Azure AD, najdete v článku [Azure Active Directory, dokumentace ke službě](https://docs.microsoft.com/azure/active-directory/). Podrobné příklady ukazující, jak pomocí knihovny ADAL najdete v [vzorových kódů Azure](https://azure.microsoft.com/resources/samples/?service=active-directory) knihovny.

K ověření aplikace služby Batch pomocí Azure AD, najdete v článku [ověřování řešení služby Batch pomocí Active Directory](batch-aad-auth.md). 


[aad_about]:../active-directory/fundamentals/active-directory-whatis.md "Co je Azure Active Directory?"
[aad_adal]: ../active-directory/active-directory-authentication-libraries.md
[aad_auth_scenarios]:../active-directory/develop/authentication-scenarios.md "Scénáře ověřování pro službu Azure AD"
[aad_integrate]: ../active-directory/active-directory-integrating-applications.md "Integrace aplikací s Azure Active Directory"
[acct_mgmt_sample]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/AccountManagement
[azure_portal]: http://portal.azure.com
[resman_overview]: ../azure-resource-manager/resource-group-overview.md
