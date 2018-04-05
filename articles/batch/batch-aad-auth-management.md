---
title: Pomocí Azure Active Directory k ověření řešení pro správu Batch | Microsoft Docs
description: Aplikace vytvořené s Azure resource manager a poskytovatelem prostředků Batch ověřování s Azure AD.
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
ms.openlocfilehash: a16d60dd48efeced7735562884acd26640d36483
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2018
---
# <a name="authenticate-batch-management-solutions-with-active-directory"></a>Ověření řešení pro správu Batch se službou Active Directory

Aplikace, které volají služby Azure Batch Management ověření pomocí [Azure Active Directory][aad_about] (Azure AD). Azure AD je víceklientské cloudový adresář společnosti Microsoft a služba identity management. Azure samotné používá Azure AD pro ověření jeho zákazníků, správci služeb a organizační uživatele.

Knihovna rozhraní Batch Management .NET poskytuje typy pro práci s účty Batch, klíče účtu, aplikace a balíčky aplikací. Knihovně rozhraní Batch Management .NET je klient poskytovatel prostředků Azure a je použít v kombinaci s [Azure Resource Manager] [ resman_overview] tyto zdroje spravovat prostřednictvím kódu programu. Azure AD je vyžadovaný k ověření požadavky prostřednictvím všechny klienty poskytovatele prostředků Azure, včetně knihovny Batch Management .NET a prostřednictvím [Azure Resource Manager][resman_overview].

V tomto článku jsme prozkoumat pomocí služby Azure AD k ověření z aplikací, které pomocí knihovny Batch Management .NET. Ukážeme, jak používat Azure AD k ověření předplatného správce nebo spolusprávce použití integrovaného ověřování. Používáme [AccountManagment] [ acct_mgmt_sample] ukázkový projekt, dostupná na Githubu, vás provede, Azure AD pomocí knihovny Batch Management .NET.

Další informace o používání knihovny Batch Management .NET a ukázkové AccountManagement najdete v tématu [Batch Správa účtů a kvót pomocí klientské knihovny správy Batch pro .NET](batch-management-dotnet.md).

## <a name="register-your-application-with-azure-ad"></a>Registrace vaší aplikace s Azure AD

Azure [Active Directory Authentication Library][aad_adal] (ADAL) poskytuje programovací rozhraní do služby Azure AD pro použití v rámci vaší aplikace. Pro volání ADAL z aplikace, je nutné zaregistrovat aplikaci v klientovi služby Azure AD. Při registraci vaší aplikace, je třeba zadat Azure AD s informacemi o aplikaci, včetně názvu pro něj v rámci klienta Azure AD. Potom Azure AD poskytuje ID aplikace, které použijete k aplikaci přidružit Azure AD v době běhu. Další informace o ID aplikace, najdete v části [aplikace a služby hlavní objekty ve službě Azure Active Directory](../active-directory/develop/active-directory-application-objects.md).

Pokud chcete zaregistrovat AccountManagement ukázkovou aplikaci, postupujte podle kroků v [přidání aplikace](../active-directory/develop/active-directory-integrating-applications.md#adding-an-application) kapitoly [integrace aplikací s Azure Active Directory][aad_integrate]. Zadejte **nativní klientská aplikace** pro typ aplikace. Odvětví standardní OAuth 2.0 identifikátor URI **identifikátor URI pro přesměrování** je `urn:ietf:wg:oauth:2.0:oob`. Ale můžete zadat libovolný platný identifikátor URI (například `http://myaccountmanagementsample`) pro **identifikátor URI pro přesměrování**, protože nemusí být skutečné koncový bod:

![](./media/batch-aad-auth-management/app-registration-management-plane.png)

Po dokončení procesu registrace se zobrazí ID aplikace a ID objektu (instanční objekt) uvedené pro vaši aplikaci.  

![](./media/batch-aad-auth-management/app-registration-client-id.png)

## <a name="grant-the-azure-resource-manager-api-access-to-your-application"></a>Udělení přístupu rozhraní API Správce prostředků Azure do vaší aplikace

Potom budete muset delegovat přístup k vaší aplikaci do rozhraní API Správce prostředků Azure. Identifikátor Azure AD pro rozhraní API Správce prostředků je **Windows Azure Service Management API**.

Pomocí těchto kroků na portálu Azure:

1. V levém navigačním podokně portálu Azure, zvolte **všechny služby**, klikněte na tlačítko **registrace aplikace**a klikněte na tlačítko **přidat**.
2. Vyhledejte název vaší aplikace v seznamu aplikace registrace:

    ![Vyhledávání pro název aplikace](./media/batch-aad-auth-management/search-app-registration.png)

3. Zobrazení **nastavení** okno. V **přístup pomocí rozhraní API** vyberte **požadovaná oprávnění**.
4. Klikněte na tlačítko **přidat** přidat nové požadovaná oprávnění. 
5. V kroku 1 zadejte **Windows Azure Service Management API**, vyberte v seznamu výsledků toto rozhraní API a klikněte **vyberte** tlačítko.
6. V kroku 2, zaškrtněte políčko vedle **Azure přístup k modelu nasazení classic jako uživatele organizaci**a klikněte na **vyberte** tlačítko.
7. Klikněte **provádí** tlačítko.

**Požadovaných oprávnění** okno nyní zobrazuje, že k vaší aplikaci oprávnění ADAL i Resource Manager rozhraní API. Jsou udělena oprávnění pro knihovnu ADAL ve výchozím nastavení při první registraci vaší aplikace s Azure AD.

![Delegování oprávnění k rozhraní API služby Azure Resource Manager](./media/batch-aad-auth-management/required-permissions-management-plane.png)

## <a name="azure-ad-endpoints"></a>Koncové body Azure AD

K ověření vašich řešení Batch Management s Azure AD, budete potřebovat dva dobře známé koncové body.

- **Běžné koncového bodu Azure AD** poskytuje obecné pověření shromažďování rozhraní při konkrétní klienta není k dispozici, protože v případě integrované ověřování:

    `https://login.microsoftonline.com/common`

- **Koncový bod Azure Resource Manager** slouží k získání tokenu pro ověřování požadavků na službu Batch management:

    `https://management.core.windows.net/`

Ukázkovou aplikaci AccountManagement definuje konstanty pro tyto koncové body. Ponechte tyto konstanty beze změny:

```csharp
// Azure Active Directory "common" endpoint.
private const string AuthorityUri = "https://login.microsoftonline.com/common";
// Azure Resource Manager endpoint 
private const string ResourceUri = "https://management.core.windows.net/";
```

## <a name="reference-your-application-id"></a>Referenční ID vaší aplikace 

Klientské aplikace používá pro přístup k Azure AD v době běhu ID aplikace (také označované jako ID klienta). Po registraci vaší aplikace na portálu Azure aktualizujte kód a použít zadané ID aplikace Azure AD pro zaregistrovanou aplikaci. V ukázkové aplikaci AccountManagement zkopírujte do příslušné konstanta vaše ID aplikace z portálu Azure:

```csharp
// Specify the unique identifier (the "Client ID") for your application. This is required so that your
// native client application (i.e. this sample) can access the Microsoft Azure AD Graph API. For information
// about registering an application in Azure Active Directory, please see "Adding an Application" here:
// https://azure.microsoft.com/documentation/articles/active-directory-integrating-applications/
private const string ClientId = "<application-id>";
```
Taky zkopírujte identifikátor URI, který jste zadali během procesu registrace přesměrování. Identifikátor URI, který jste zadali při registraci aplikace přesměrování musí odpovídat přesměrování, které URI zadaný v kódu.

```csharp
// The URI to which Azure AD will redirect in response to an OAuth 2.0 request. This value is
// specified by you when you register an application with AAD (see ClientId comment). It does not
// need to be a real endpoint, but must be a valid URI (e.g. https://accountmgmtsampleapp).
private const string RedirectUri = "http://myaccountmanagementsample";
```

## <a name="acquire-an-azure-ad-authentication-token"></a>Získání tokenu ověřování Azure AD

Po registraci ukázka AccountManagement v klientovi Azure AD a aktualizujte zdrojový kód ukázkové s hodnoty, ukázce je připraven k ověření pomocí služby Azure AD. Při spuštění ukázky, se pokusí získat ověřovací token knihovnu ADAL. V tomto kroku budete vyzváni k zadání pověření Microsoft: 

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

Po zadání přihlašovacích údajů můžete pokračovat ukázkovou aplikaci pro zasílání požadavků na ověření do služby Batch management. 

## <a name="next-steps"></a>Další postup

Další informace o spuštění [AccountManagement ukázkovou aplikaci][acct_mgmt_sample], najdete v části [Batch Správa účtů a kvót pomocí klientské knihovny správy Batch pro .NET](batch-management-dotnet.md).

Další informace o Azure AD, najdete v článku [Azure Active Directory dokumentaci](https://docs.microsoft.com/azure/active-directory/). Podrobné příklady znázorňující způsob použití knihovny ADAL jsou k dispozici v [ukázky kódu Azure](https://azure.microsoft.com/resources/samples/?service=active-directory) knihovny.

K ověření aplikace služby Batch pomocí služby Azure AD, najdete v části [řešení služby Batch ověření pomocí služby Active Directory](batch-aad-auth.md). 


[aad_about]: ../active-directory/active-directory-whatis.md "Co je Azure Active Directory?"
[aad_adal]: ../active-directory/active-directory-authentication-libraries.md
[aad_auth_scenarios]: ../active-directory/active-directory-authentication-scenarios.md "Scénáře ověřování pro Azure AD"
[aad_integrate]: ../active-directory/active-directory-integrating-applications.md "Integrace aplikací s Azure Active Directory"
[acct_mgmt_sample]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/AccountManagement
[azure_portal]: http://portal.azure.com
[resman_overview]: ../azure-resource-manager/resource-group-overview.md
