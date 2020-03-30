---
title: Ověření řešení správy dávek pomocí služby Azure Active Directory
description: Prozkoumejte pomocí služby Azure Active Directory k ověřování z aplikací, které používají knihovnu Batch Management .NET.
services: batch
documentationcenter: .net
author: LauraBrenner
manager: evansma
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: batch
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 04/27/2017
ms.author: labrenne
ms.openlocfilehash: 5c217971bd213c97a2ee31a0a1f513b601d14df9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79472975"
---
# <a name="authenticate-batch-management-solutions-with-active-directory"></a>Ověření řešení správy dávek pomocí služby Active Directory

Aplikace, které volají službu Azure Batch Management, se ověřují pomocí [služby Azure Active Directory][aad_about] (Azure AD). Azure AD je služba Microsoft u cloudového cloudu a správy identit. Azure sám používá Azure AD pro ověřování svých zákazníků, správců služeb a uživatelů organizace.

Knihovna Správy dávek .NET zveřejňuje typy pro práci s dávkovými účty, klíči účtů, aplikacemi a balíčky aplikací. Knihovna Batch Management .NET je klientem zprostředkovatele prostředků Azure a používá se společně se [Správcem prostředků Azure][resman_overview] ke správě těchto prostředků programově. Azure AD je potřeba k ověření požadavků provedených prostřednictvím libovolného klienta zprostředkovatele prostředků Azure, včetně knihovny Batch Management .NET, a prostřednictvím [Správce prostředků Azure][resman_overview].

V tomto článku zkoumáme pomocí Azure AD k ověření z aplikací, které používají knihovnu Batch Management .NET. Ukazujeme, jak používat Azure AD k ověření správce předplatného nebo spolusprávce pomocí integrovaného ověřování. Pomocí Azure AD s knihovnou Batch Management .NET používáme ukázkový projekt [AccountManagement,][acct_mgmt_sample] který je k dispozici na GitHubu.

Další informace o používání knihovny Správy dávek .NET a ukázky AccountManagement najdete v [tématu Správa dávkových účtů a kvót pomocí klientské knihovny Správy dávek pro rozhraní .NET](batch-management-dotnet.md).

## <a name="register-your-application-with-azure-ad"></a>Registrace aplikace pomocí Azure AD

Azure [Active Directory Authentication Library][aad_adal] (ADAL) poskytuje programové rozhraní pro Azure AD pro použití ve vašich aplikacích. Chcete-li volat ADAL z vaší aplikace, musíte zaregistrovat aplikaci v tenantovi Azure AD. Když zaregistrujete svou aplikaci, dodáte Azure AD s informacemi o vaší aplikaci, včetně názvu pro ji v rámci klienta Azure AD. Azure AD pak poskytuje ID aplikace, které používáte k přidružení aplikace k Azure AD za běhu. Další informace o ID aplikace najdete v [tématu Objekty za registrovaných aplikací a služeb ve službě Azure Active Directory](../active-directory/develop/app-objects-and-service-principals.md).

Chcete-li zaregistrovat ukázkovou aplikaci AccountManagement, postupujte podle pokynů v části [Přidání aplikace](../active-directory/develop/quickstart-register-app.md) v části Integrace aplikací [se službou Azure Active Directory][aad_integrate]. Zadejte **nativní klientskou aplikaci** pro typ aplikace. Standardní identifikátor URI OAuth 2.0 pro `urn:ietf:wg:oauth:2.0:oob`identifikátor URI **přesměrování** je . Můžete však zadat libovolný platný `http://myaccountmanagementsample`identifikátor URI (například) pro **identifikátor URI přesměrování**, protože nemusí být skutečným koncovým bodem:

![](./media/batch-aad-auth-management/app-registration-management-plane.png)

Po dokončení procesu registrace se zobrazí ID aplikace a ID objektu (instančního objektu) uvedené pro vaši aplikaci.  

![](./media/batch-aad-auth-management/app-registration-client-id.png)

## <a name="grant-the-azure-resource-manager-api-access-to-your-application"></a>Udělení přístupu rozhraní API Azure Resource Manager u vaší aplikace

Dále budete muset delegovat přístup k vaší aplikaci do rozhraní API Azure Resource Manager. Identifikátor Azure AD pro rozhraní API správce prostředků je **rozhraní API pro správu služeb Windows Azure**.

Na webu Azure Portal postupujte podle těchto pokynů:

1. V levém navigačním podokně portálu Azure vyberte **Všechny služby**, klikněte na **Registrace aplikací**a klikněte na **Přidat**.
2. Vyhledejte název aplikace v seznamu registrací aplikací:

    ![Vyhledání názvu aplikace](./media/batch-aad-auth-management/search-app-registration.png)

3. Zobrazí okno **Nastavení.** V části **Přístup k rozhraní API** vyberte **Požadovaná oprávnění**.
4. Kliknutím na **Přidat** přidejte nové požadované oprávnění. 
5. V kroku 1 zadejte **rozhraní API pro správu služeb Windows Azure**, vyberte toto rozhraní API ze seznamu výsledků a klikněte na tlačítko **Vybrat.**
6. V kroku 2 zaškrtněte políčko vedle **modelu klasického nasazení Access Azure jako uživatelé organizace**a klikněte na tlačítko **Vybrat.**
7. Klikněte na tlačítko **Hotovo.**

Okno **Požadovaná oprávnění** nyní ukazuje, že oprávnění k vaší aplikaci jsou udělena pro adal a správce prostředků API. Oprávnění jsou udělena ADAL ve výchozím nastavení při první registraci aplikace s Azure AD.

![Delegování oprávnění do rozhraní API Správce prostředků Azure](./media/batch-aad-auth-management/required-permissions-management-plane.png)

## <a name="azure-ad-endpoints"></a>Koncové body Azure AD

K ověření řešení správy dávek pomocí Azure AD budete potřebovat dva známé koncové body.

- **Společný koncový bod Služby Azure AD** poskytuje obecné rozhraní pro shromažďování přihlašovacích údajů, když konkrétní klient není k dispozici, jako v případě integrovaného ověřování:

    `https://login.microsoftonline.com/common`

- Koncový **bod Správce prostředků Azure** se používá k získání tokenu pro ověřování požadavků na službu správy dávek:

    `https://management.core.windows.net/`

Ukázková aplikace AccountManagement definuje konstanty pro tyto koncové body. Ponechte tyto konstanty beze změny:

```csharp
// Azure Active Directory "common" endpoint.
private const string AuthorityUri = "https://login.microsoftonline.com/common";
// Azure Resource Manager endpoint 
private const string ResourceUri = "https://management.core.windows.net/";
```

## <a name="reference-your-application-id"></a>Odkaz na ID aplikace 

Vaše klientská aplikace používá ID aplikace (označované také jako ID klienta) pro přístup k Azure AD za běhu. Jakmile zaregistrujete svou aplikaci na webu Azure Portal, aktualizujte kód tak, aby používal ID aplikace poskytované službou Azure AD pro vaši registrovanou aplikaci. V ukázkové aplikaci AccountManagement zkopírujte ID aplikace z portálu Azure na příslušnou konstantu:

```csharp
// Specify the unique identifier (the "Client ID") for your application. This is required so that your
// native client application (i.e. this sample) can access the Microsoft Graph API. For information
// about registering an application in Azure Active Directory, please see "Register an application with the Microsoft identity platform" here:
// https://docs.microsoft.com/en-us/azure/active-directory/develop/quickstart-register-app
private const string ClientId = "<application-id>";
```
Zkopírujte také identifikátor URI přesměrování, který jste zadali během procesu registrace. Identifikátor URI přesměrování zadaný v kódu se musí shodovat s identifikátorem URI přesměrování, který jste zadali při registraci aplikace.

```csharp
// The URI to which Azure AD will redirect in response to an OAuth 2.0 request. This value is
// specified by you when you register an application with AAD (see ClientId comment). It does not
// need to be a real endpoint, but must be a valid URI (e.g. https://accountmgmtsampleapp).
private const string RedirectUri = "http://myaccountmanagementsample";
```

## <a name="acquire-an-azure-ad-authentication-token"></a>Získání ověřovacího tokenu Azure AD

Po registraci ukázky AccountManagement v tenantovi Azure AD a aktualizovat ukázkový zdrojový kód s vašimi hodnotami, ukázka je připravenko k ověření pomocí Azure AD. Při spuštění ukázky ADAL pokusí získat ověřovací token. V tomto kroku zobrazí výzvu k zadání pověření společnosti Microsoft: 

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

Po zadání pověření může ukázková aplikace přistoupit k vydání ověřených požadavků službě Správa dávek. 

## <a name="next-steps"></a>Další kroky

Další informace o spuštění [ukázkové aplikace AccountManagement][acct_mgmt_sample]naleznete v [tématu Správa dávkových účtů a kvót pomocí klientské knihovny Správy dávek pro rozhraní .NET](batch-management-dotnet.md).

Další informace o Azure AD najdete v tématu [Dokumentace služby Azure Active Directory](https://docs.microsoft.com/azure/active-directory/). Podrobné příklady, které ukazují, jak používat ADAL jsou k dispozici v knihovně [ukázky kódu Azure.](https://azure.microsoft.com/resources/samples/?service=active-directory)

Informace o ověřování aplikací dávkových služeb pomocí služby Azure AD naleznete v [tématu Ověření řešení dávkových služeb pomocí služby Active Directory](batch-aad-auth.md). 


[aad_about]:../active-directory/fundamentals/active-directory-whatis.md "Co je služba Azure Active Directory?"
[aad_adal]: ../active-directory/active-directory-authentication-libraries.md
[aad_auth_scenarios]:../active-directory/develop/authentication-scenarios.md "Scénáře ověřování pro Azure AD"
[aad_integrate]: ../active-directory/active-directory-integrating-applications.md "Integrace aplikací s Azure Active Directory"
[acct_mgmt_sample]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/AccountManagement
[azure_portal]: https://portal.azure.com
[resman_overview]: ../azure-resource-manager/management/overview.md
