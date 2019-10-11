---
title: Ověřování Azure Active Directory a Správce prostředků | Microsoft Docs
description: Příručka pro vývojáře k ověřování pomocí rozhraní Azure Resource Manager API a Azure Active Directory pro integraci aplikace s jinými předplatnými Azure.
author: dushyantgill
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 04/05/2019
ms.author: dugill
ms.openlocfilehash: 033f3ca9ca79903f884c625dc694b06a3e4fd04c
ms.sourcegitcommit: b4665f444dcafccd74415fb6cc3d3b65746a1a31
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2019
ms.locfileid: "72263010"
---
# <a name="use-resource-manager-authentication-api-to-access-subscriptions"></a>Použití rozhraní API pro ověřování Správce prostředků pro přístup k předplatným

Pokud jste vývojář softwaru, který potřebuje vytvořit aplikaci, která spravuje prostředky Azure, v tomto článku se dozvíte, jak ověřit pomocí rozhraní Azure Resource Manager API a získat přístup k prostředkům v jiných předplatných.

Vaše aplikace má přístup k rozhraním API Správce prostředků několika způsoby:

1. **Přístup uživatele + aplikace**: pro aplikace, které přistupují k prostředkům přihlášeného uživatele. Tento přístup funguje pro aplikace, jako jsou webové aplikace a nástroje příkazového řádku, které se týkají pouze interaktivní správy prostředků Azure.
2. **Přístup jen pro aplikace**: pro aplikace, které spouštějí služby démon a naplánované úlohy. Identitě aplikace je udělen přímý přístup k prostředkům. Tento přístup funguje pro aplikace, které potřebují dlouhodobě bezproblémový přístup k Azure.

Tento článek poskytuje podrobné pokyny k vytvoření aplikace, která využívá obě tyto metody autorizace. Ukazuje, jak provádět jednotlivé kroky pomocí REST API nebo C#. Kompletní aplikace ASP.NET MVC je k dispozici na adrese [https://github.com/dushyantgill/VipSwapper/tree/master/CloudSense](https://github.com/dushyantgill/VipSwapper/tree/master/CloudSense).

## <a name="what-the-web-app-does"></a>Co dělá webová aplikace

Webová aplikace:

1. Přihlaste se jako uživatel Azure.
2. Požádá uživatele o udělení přístupu webové aplikaci k Správce prostředků.
3. Získá přístupový token uživatele + aplikace pro přístup k Správce prostředků.
4. Používá token (z kroku 3) k přiřazení instančního objektu aplikace k roli v předplatném. Tento krok poskytuje aplikaci dlouhodobě přístup k předplatnému.
5. Získá přístupový token jenom pro aplikaci.
6. Používá token (z kroku 5) ke správě prostředků v rámci předplatného prostřednictvím Správce prostředků.

Zde je tok webové aplikace.

![Správce prostředků tok ověřování](./media/resource-manager-api-authentication/Auth-Swim-Lane.png)

Jako uživatel zadejte ID předplatného pro předplatné, které chcete použít:

![zadat ID předplatného](./media/resource-manager-api-authentication/sample-ux-1.png)

Vyberte účet, který se má použít pro přihlášení.

![vybrat účet](./media/resource-manager-api-authentication/sample-ux-2.png)

Zadejte své přihlašovací údaje.

![zadat přihlašovací údaje](./media/resource-manager-api-authentication/sample-ux-3.png)

Udělte aplikaci přístup k předplatným Azure:

![Udělit přístup](./media/resource-manager-api-authentication/sample-ux-4.png)

Správa připojených předplatných:

![Připojit předplatné](./media/resource-manager-api-authentication/sample-ux-7.png)

## <a name="register-application"></a>Registrovat aplikaci
Než začnete s psaním kódu, zaregistrujte svou webovou aplikaci pomocí Azure Active Directory (AD). Registrace aplikace vytvoří centrální identitu vaší aplikace ve službě Azure AD. Obsahuje základní informace o vaší aplikaci, jako je ID klienta OAuth, adresy URL odpovědí a přihlašovací údaje, které vaše aplikace používá k ověřování a přístup k rozhraním API Azure Resource Manager. Registrace aplikace také zaznamenává různá delegovaná oprávnění, která vaše aplikace potřebuje při přístupu k rozhraním API Microsoftu pro uživatele.

Informace o registraci vaší aplikace najdete v tématu [rychlý Start: registrace aplikace s platformou Microsoft Identity](../active-directory/develop/quickstart-register-app.md). Zadejte název aplikace a pro podporované typy účtů vyberte **účty v libovolném organizačním adresáři** . Jako adresu URL pro přesměrování zadejte doménu přidruženou k vašemu Azure Active Directory.

Abyste se mohli přihlásit jako aplikace AD, budete potřebovat ID aplikace a tajný klíč. ID aplikace se zobrazí v přehledu pro aplikaci. Informace o vytvoření tajného klíče a vyžádání oprávnění rozhraní API najdete v tématu [rychlý Start: Konfigurace klientské aplikace pro přístup k webovým rozhraním API](../active-directory/develop/quickstart-configure-app-access-web-apis.md). Zadejte nový tajný klíč klienta. V případě oprávnění rozhraní API vyberte **Azure Service Management**. Vyberte **delegovaná oprávnění** a **user_impersonation**.

### <a name="optional-configuration---certificate-credential"></a>Nepovinná konfigurace-přihlašovací údaje certifikátu
Azure AD také podporuje přihlašovací údaje certifikátů pro aplikace: vytvoříte certifikát podepsaný svým držitelem, zachováte privátní klíč a přidáte veřejný klíč do registrace aplikace služby Azure AD. Pro ověřování vaše aplikace pošle do Azure AD podepsané pomocí privátního klíče malou datovou část a služba Azure AD ověří podpis pomocí veřejného klíče, který jste zaregistrovali.

Informace o vytvoření aplikace AD s certifikátem najdete v tématu [použití Azure PowerShell k vytvoření instančního objektu pro přístup k prostředkům](../active-directory/develop/howto-authenticate-service-principal-powershell.md#create-service-principal-with-certificate-from-certificate-authority) nebo použití rozhraní příkazového [řádku Azure k vytvoření instančního objektu pro přístup k prostředkům](resource-group-authenticate-service-principal-cli.md).

## <a name="get-tenant-id-from-subscription-id"></a>Získat ID tenanta z ID předplatného
Aby bylo možné požádat o token, který se dá použít k volání Správce prostředků, musí vaše aplikace znát ID tenanta tenanta Azure AD, který hostuje předplatné Azure. Nejpravděpodobnější, že vaši uživatelé znají ID předplatných, ale nemusí znát jejich ID tenantů Azure Active Directory. Pokud chcete získat ID tenanta uživatele, požádejte uživatele o ID předplatného. Zadejte ID předplatného, když posíláte žádost o předplatné:

    https://management.azure.com/subscriptions/{subscription-id}?api-version=2015-01-01

Požadavek se nezdařil, protože uživatel ještě není přihlášen, ale z odpovědi můžete získat ID tenanta. V takovém případě načtěte ID tenanta z hodnoty hlavičky odpovědi pro ověřování pomocí služby **www**. Tato implementace se zobrazí v metodě [GetDirectoryForSubscription](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureResourceManagerUtil.cs#L20) .

## <a name="get-user--app-access-token"></a>Získání přístupového tokenu uživatele + aplikace
Vaše aplikace přesměruje uživatele na Azure AD pomocí žádosti OAuth 2,0 autorizovat požadavek-k ověření přihlašovacích údajů uživatele a vrácení autorizačního kódu. Vaše aplikace používá autorizační kód k získání přístupového tokenu pro Správce prostředků. Metoda [ConnectSubscription](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/Controllers/HomeController.cs#L42) vytvoří žádost o autorizaci.

Tento článek ukazuje REST API žádosti o ověření uživatele. Můžete také použít pomocné knihovny k ověření ve vašem kódu. Další informace o těchto knihovnách naleznete v tématu [Azure Active Directory Authentication Libraries](../active-directory/active-directory-authentication-libraries.md). Pokyny k integraci správy identit v aplikaci najdete v tématu [Azure Active Directory příručka pro vývojáře](../active-directory/develop/v1-overview.md).

### <a name="auth-request-oauth-20"></a>Žádost o ověření (OAuth 2,0)
Vydejte požadavek na otevření ID Connect/OAuth 2.0 k autorizaci koncového bodu autorizace Azure AD:

    https://login.microsoftonline.com/{tenant-id}/OAuth2/Authorize

Parametry řetězce dotazu, které jsou k dispozici pro tuto žádost, jsou popsány v článku [žádost o autorizační kód](../active-directory/develop/v1-protocols-oauth-code.md#request-an-authorization-code) .

Následující příklad ukazuje, jak požádat o autorizaci OAuth 2.0:

    https://login.microsoftonline.com/{tenant-id}/OAuth2/Authorize?client_id=a0448380-c346-4f9f-b897-c18733de9394&response_mode=query&response_type=code&redirect_uri=http%3a%2f%2fwww.vipswapper.com%2fcloudsense%2fAccount%2fSignIn&resource=https%3a%2f%2fgraph.windows.net%2f&domain_hint=live.com

Azure AD ověří uživatele a v případě potřeby požádá uživatele o udělení oprávnění aplikaci. Vrátí autorizační kód k adrese URL odpovědi vaší aplikace. V závislosti na požadované response_mode Azure AD buď pošle zpět data v řetězci dotazu, nebo jako data post.

    code=AAABAAAAiL****FDMZBUwZ8eCAA&session_state=2d16bbce-d5d1-443f-acdf-75f6b0ce8850

### <a name="auth-request-open-id-connect"></a>Žádost o ověření (otevřené ID připojit)
Pokud nechcete pro uživatele získat přístup Azure Resource Manager, ale taky povolit uživatelům přihlášení k aplikaci pomocí svého účtu Azure AD, vydejte požadavek na otevřené ID připojit autorizaci. S otevřeným ID Connect aplikace taky přijímá id_token ze služby Azure AD, kterou vaše aplikace může použít k přihlášení uživatele.

Parametry řetězce dotazu, které jsou k dispozici pro tuto žádost, jsou popsány v článku [odeslání žádosti o přihlášení](../active-directory/develop/v1-protocols-openid-connect-code.md#send-the-sign-in-request) .

Příkladem otevřeného ID žádosti o připojení je:

     https://login.microsoftonline.com/{tenant-id}/OAuth2/Authorize?client_id=a0448380-c346-4f9f-b897-c18733de9394&response_mode=form_post&response_type=code+id_token&redirect_uri=http%3a%2f%2fwww.vipswapper.com%2fcloudsense%2fAccount%2fSignIn&resource=https%3a%2f%2fgraph.windows.net%2f&scope=openid+profile&nonce=63567Dc4MDAw&domain_hint=live.com&state=M_12tMyKaM8

Azure AD ověří uživatele a v případě potřeby požádá uživatele o udělení oprávnění aplikaci. Vrátí autorizační kód k adrese URL odpovědi vaší aplikace. V závislosti na požadované response_mode Azure AD buď pošle zpět data v řetězci dotazu, nebo jako data post.

Příkladem otevřené odpovědi s ID připojení je:

    code=AAABAAAAiL*****I4rDWd7zXsH6WUjlkIEQxIAA&id_token=eyJ0eXAiOiJKV1Q*****T3GrzzSFxg&state=M_12tMyKaM8&session_state=2d16bbce-d5d1-443f-acdf-75f6b0ce8850

### <a name="token-request-oauth20-code-grant-flow"></a>Požadavek tokenu (tok udělení kódu OAuth 2.0)
Teď, když vaše aplikace získala autorizační kód z Azure AD, je čas získat přístupový token pro Azure Resource Manager.  Odeslání žádosti o token pro udělení kódu OAuth 2.0 na koncový bod tokenu služby Azure AD:

    https://login.microsoftonline.com/{tenant-id}/OAuth2/Token

Parametry řetězce dotazu, které jsou k dispozici pro tuto žádost, jsou popsány v článku [použití autorizačního kódu](../active-directory/develop/v1-protocols-oauth-code.md#use-the-authorization-code-to-request-an-access-token) .

Následující příklad ukazuje požadavek na token pro udělení kódu s přihlašovacími údaji hesla:

    POST https://login.microsoftonline.com/7fe877e6-a150-4992-bbfe-f517e304dfa0/oauth2/token HTTP/1.1

    Content-Type: application/x-www-form-urlencoded
    Content-Length: 1012

    grant_type=authorization_code&code=AAABAAAAiL9Kn2Z*****L1nVMH3Z5ESiAA&redirect_uri=http%3A%2F%2Flocalhost%3A62080%2FAccount%2FSignIn&client_id=a0448380-c346-4f9f-b897-c18733de9394&client_secret=olna84E8*****goScOg%3D

Při práci s přihlašovacími údaji certifikátu vytvořte JSON Web Token (JWT) a podepište (RSA SHA256) pomocí privátního klíče přihlašovacích údajů k certifikátu vaší aplikace. Sestavení tohoto tokenu se zobrazí v [toku přihlašovacích údajů klienta](../active-directory/develop/v1-oauth2-client-creds-grant-flow.md#second-case-access-token-request-with-a-certificate).

Podrobnosti o ověřování klientů najdete v tématu věnovaném [specifikaci Open ID Connect](https://openid.net/specs/openid-connect-core-1_0.html#ClientAuthentication) .

Následující příklad ukazuje požadavek na token pro udělení kódu s přihlašovacími údaji certifikátu:

    POST https://login.microsoftonline.com/7fe877e6-a150-4992-bbfe-f517e304dfa0/oauth2/token HTTP/1.1

    Content-Type: application/x-www-form-urlencoded
    Content-Length: 1012

    grant_type=authorization_code&code=AAABAAAAiL9Kn2Z*****L1nVMH3Z5ESiAA&redirect_uri=http%3A%2F%2Flocalhost%3A62080%2FAccount%2FSignIn&client_id=a0448380-c346-4f9f-b897-c18733de9394&client_assertion_type=urn%3Aietf%3Aparams%3Aoauth%3Aclient-assertion-type%3Ajwt-bearer&client_assertion=eyJhbG*****Y9cYo8nEjMyA

Ukázková odpověď pro token pro udělení kódu:

    HTTP/1.1 200 OK

    {"token_type":"Bearer","expires_in":"3599","expires_on":"1432039858","not_before":"1432035958","resource":"https://management.core.windows.net/","access_token":"eyJ0eXAiOiJKV1Q****M7Cw6JWtfY2lGc5A","refresh_token":"AAABAAAAiL9Kn2Z****55j-sjnyYgAA","scope":"user_impersonation","id_token":"eyJ0eXAiOiJKV*****-drP1J3P-HnHi9Rr46kGZnukEBH4dsg"}

#### <a name="handle-code-grant-token-response"></a>Zpracovat odpověď tokenu pro udělení kódu
Úspěšná odpověď tokenu obsahuje přístupový token (uživatel + aplikace) pro Azure Resource Manager. Vaše aplikace používá tento přístupový token pro přístup k Správce prostředků pro uživatele. Doba života přístupových tokenů vydaných službou Azure AD je jedna hodina. Je pravděpodobné, že vaše webová aplikace potřebuje obnovit přístupový token (uživatel + aplikace). Pokud je potřeba obnovit přístupový token, použijte obnovovací token, který vaše aplikace obdrží v odpovědi na token. Odeslání žádosti o token OAuth 2.0 do koncového bodu tokenu služby Azure AD:

    https://login.microsoftonline.com/{tenant-id}/OAuth2/Token

Parametry pro použití s žádostí o aktualizaci jsou popsány v tématu [aktualizace přístupového tokenu](../active-directory/develop/v1-protocols-oauth-code.md#refreshing-the-access-tokens).

Následující příklad ukazuje, jak použít obnovovací token:

    POST https://login.microsoftonline.com/7fe877e6-a150-4992-bbfe-f517e304dfa0/oauth2/token HTTP/1.1

    Content-Type: application/x-www-form-urlencoded
    Content-Length: 1012

    grant_type=refresh_token&refresh_token=AAABAAAAiL9Kn2Z****55j-sjnyYgAA&client_id=a0448380-c346-4f9f-b897-c18733de9394&client_secret=olna84E8*****goScOg%3D

I když lze tokeny aktualizace použít k získání nových přístupových tokenů pro Azure Resource Manager, nejsou vhodné pro vaši aplikaci offline přístup. Doba platnosti obnovovacích tokenů je omezená a aktualizace tokenů jsou vázané na uživatele. Pokud uživatel odejde z organizace, aplikace používající obnovovací token ztratí přístup. Tento přístup není vhodný pro aplikace, které týmy používají ke správě prostředků Azure.

## <a name="check-if-user-can-assign-access-to-subscription"></a>Ověřte, jestli uživatel může přiřazovat přístup k předplatnému.
Vaše aplikace má teď token pro přístup k Azure Resource Manager pro uživatele. Dalším krokem je připojení aplikace k předplatnému. Po připojení může vaše aplikace spravovat tato předplatná i v případě, že uživatel není přítomen (dlouhodobě offline přístup).

U každého předplatného, které se má připojit, zavolejte rozhraní API pro přístup k [seznamu Správce prostředků](https://docs.microsoft.com/rest/api/authorization/permissions) , abyste zjistili, jestli má uživatel oprávnění pro správu přístupu k předplatnému.

Metoda [UserCanManagerAccessForSubscription](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureResourceManagerUtil.cs#L44) ukázkové aplikace ASP.NET MVC implementuje toto volání.

Následující příklad ukazuje, jak požádat uživatele o oprávnění k předplatnému. 83cfe939-2402-4581-b761-4f59b0a041e4 je ID předplatného.

    GET https://management.azure.com/subscriptions/83cfe939-2402-4581-b761-4f59b0a041e4/providers/microsoft.authorization/permissions?api-version=2015-07-01 HTTP/1.1

    Authorization: Bearer eyJ0eXAiOiJKV1QiLC***lwO1mM7Cw6JWtfY2lGc5A

Příkladem odpovědi pro získání oprávnění uživatele k předplatnému je:

    HTTP/1.1 200 OK

    {"value":[{"actions":["*"],"notActions":["Microsoft.Authorization/*/Write","Microsoft.Authorization/*/Delete"]},{"actions":["*/read"],"notActions":[]}]}

Rozhraní API pro oprávnění vrací více oprávnění. Každé oprávnění se skládá z povolených akcí (**Akce**) a zakázaných akcí (**notactions**). Pokud je akce přítomna v povolených akcích jakéhokoliv oprávnění a není přítomna v nepovolených akcích tohoto oprávnění, uživatel může tuto akci provést. **Microsoft. Authorization/RoleAssignments/Write** je akce, která uděluje oprávnění ke správě přístupu. Vaše aplikace musí analyzovat výsledek oprávnění, aby vypadala shoda regulárního výrazu na tomto řetězci akce v **akcích** a **notactions** jednotlivých oprávnění.

## <a name="get-app-only-access-token"></a>Získat přístupový token jenom pro aplikaci
Nyní víte, jestli uživatel může přiřazovat přístup k předplatnému Azure. Následující kroky:

1. Přiřaďte k identitě vaší aplikace příslušné role RBAC v předplatném.
2. Ověřte přiřazení přístupu pomocí dotazování pro oprávnění aplikace v předplatném nebo přístupem k Správce prostředků pomocí tokenu jenom pro aplikace.
3. Poznamenejte si připojení ve struktuře dat "připojení k předplatným" v aplikacích – zachovejte ID předplatného.

Pojďme se v prvním kroku podívat blíž. Chcete-li přiřadit příslušné role RBAC k identitě aplikace, je nutné určit:

* ID objektu identity vaší aplikace v Azure Active Directory uživatele
* Identifikátor role RBAC, kterou vaše aplikace vyžaduje v předplatném

Když vaše aplikace ověřuje uživatele ze služby Azure AD, vytvoří v této službě Azure AD objekt instančního objektu pro vaši aplikaci. Azure umožňuje přiřazení rolí RBAC k instančním objektům, aby bylo možné udělit přímý přístup k odpovídajícím aplikacím v prostředcích Azure. Tato akce je přesně to, co chcete udělat. Proveďte dotaz na Graph API Azure AD a určete identifikátor instančního objektu vaší aplikace v Azure AD přihlášeného uživatele.

Máte přístup jenom k přístupovému tokenu pro Azure Resource Manager – pro volání Graph API Azure AD potřebujete nový přístupový token. Každá aplikace v Azure AD má oprávnění k dotazování vlastního instančního objektu, takže přístupový token jenom pro aplikace je dostatečný.

<a id="app-azure-ad-graph" />

### <a name="get-app-only-access-token-for-azure-ad-graph-api"></a>Získat přístupový token jenom pro aplikaci pro Azure AD Graph API

K ověření vaší aplikace a získání tokenu pro Graph API Azure AD vydejte požadavek na přihlašovací údaje klienta udělení žádosti o token toku OAuth 2.0 do koncového bodu tokenu služby Azure AD (**https: \//Login. microsoftonline. com/{directory_domain_name}/OAuth2/token**).

Metoda [GetObjectIdOfServicePrincipalInOrganization](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureADGraphAPIUtil.cs) ukázkové aplikace ASP.NET MVC získá přístupový token jenom pro aplikaci pro Graph API pomocí Active Directory Authentication Library pro .NET.

Parametry řetězce dotazu, které jsou k dispozici pro tuto žádost, jsou popsány v článku [vyžádání přístupového tokenu](../active-directory/develop/v1-oauth2-client-creds-grant-flow.md#request-an-access-token) .

Příklad žádosti o token pro udělení přihlašovacích údajů klienta:

    POST https://login.microsoftonline.com/62e173e9-301e-423e-bcd4-29121ec1aa24/oauth2/token HTTP/1.1
    Content-Type: application/x-www-form-urlencoded
    Content-Length: 187</pre>
    <pre>grant_type=client_credentials&client_id=a0448380-c346-4f9f-b897-c18733de9394&resource=https%3A%2F%2Fgraph.windows.net%2F &client_secret=olna8C*****Og%3D

Ukázková odpověď tokenu udělení přihlašovacích údajů klienta:

    HTTP/1.1 200 OK

    {"token_type":"Bearer","expires_in":"3599","expires_on":"1432039862","not_before":"1432035962","resource":"https://graph.windows.net/","access_token":"eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWmNBVGZNNXBPWWlKSE1iYTlnb0VLWSIsImtpZCI6Ik1uQ19WWmNBVGZNNXBPWWlKSE1iYTlnb0VLWSJ9.eyJhdWQiOiJodHRwczovL2dyYXBoLndpbmRv****G5gUTV-kKorR-pg"}

### <a name="get-objectid-of-application-service-principal-in-user-azure-ad"></a>Získat ObjectId objektu aplikační služby v uživatelském Azure AD
Nyní použijte token přístupu pouze pro aplikace k dotazování rozhraní API [objektů služby Azure AD Graph](/previous-versions/azure/ad/graph/api/entity-and-complex-type-reference#serviceprincipal-entity) k určení ID objektu instančního objektu aplikace v adresáři.

Metoda [GetObjectIdOfServicePrincipalInOrganization](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureADGraphAPIUtil.cs#) ukázkové aplikace ASP.NET MVC implementuje toto volání.

Následující příklad ukazuje, jak vyžádat instanční objekt aplikace. a0448380-c346-4f9f-b897-c18733de9394 je ID klienta aplikace.

    GET https://graph.windows.net/62e173e9-301e-423e-bcd4-29121ec1aa24/servicePrincipals?api-version=1.5&$filter=appId%20eq%20'a0448380-c346-4f9f-b897-c18733de9394' HTTP/1.1

    Authorization: Bearer eyJ0eXAiOiJK*****-kKorR-pg

Následující příklad ukazuje odpověď na požadavek instančního objektu aplikace.

    HTTP/1.1 200 OK

    {"odata.metadata":"https://graph.windows.net/62e173e9-301e-423e-bcd4-29121ec1aa24/$metadata#directoryObjects/Microsoft.DirectoryServices.ServicePrincipal","value":[{"odata.type":"Microsoft.DirectoryServices.ServicePrincipal","objectType":"ServicePrincipal","objectId":"9b5018d4-6951-42ed-8a92-f11ec283ccec","deletionTimestamp":null,"accountEnabled":true,"appDisplayName":"CloudSense","appId":"a0448380-c346-4f9f-b897-c18733de9394","appOwnerTenantId":"62e173e9-301e-423e-bcd4-29121ec1aa24","appRoleAssignmentRequired":false,"appRoles":[],"displayName":"CloudSense","errorUrl":null,"homepage":"http://www.vipswapper.com/cloudsense","keyCredentials":[],"logoutUrl":null,"oauth2Permissions":[{"adminConsentDescription":"Allow the application to access CloudSense on behalf of the signed-in user.","adminConsentDisplayName":"Access CloudSense","id":"b7b7338e-683a-4796-b95e-60c10380de1c","isEnabled":true,"type":"User","userConsentDescription":"Allow the application to access CloudSense on your behalf.","userConsentDisplayName":"Access CloudSense","value":"user_impersonation"}],"passwordCredentials":[],"preferredTokenSigningKeyThumbprint":null,"publisherName":"vipswapper"quot;,"replyUrls":["http://www.vipswapper.com/cloudsense","http://www.vipswapper.com","http://vipswapper.com","http://vipswapper.azurewebsites.net","http://localhost:62080"],"samlMetadataUrl":null,"servicePrincipalNames":["http://www.vipswapper.com/cloudsense","a0448380-c346-4f9f-b897-c18733de9394"],"tags":["WindowsAzureActiveDirectoryIntegratedApp"]}]}

### <a name="get-azure-rbac-role-identifier"></a>Získat identifikátor role Azure RBAC
K přiřazení příslušné role RBAC k instančnímu objektu musíte určit identifikátor role Azure RBAC.

Správná role RBAC pro vaši aplikaci:

* Pokud vaše aplikace monitoruje jenom předplatné, aniž by došlo k žádným změnám, vyžaduje v předplatném jenom oprávnění čtenářů. Přiřaďte roli **Čtenář** .
* Pokud vaše aplikace spravuje předplatné Azure, vytváření, úpravy a odstraňování entit, vyžaduje jedno z oprávnění přispěvatele.
  * Pokud chcete spravovat konkrétní typ prostředku, přiřaďte role přispěvatele pro konkrétní prostředek (Přispěvatel virtuálních počítačů, přispěvatel Virtual Network, přispěvatele účtu úložiště atd.).
  * Pokud chcete spravovat libovolný typ prostředku, přiřaďte roli **Přispěvatel** .

Přiřazení role vaší aplikace je uživatelům viditelné, takže vyberte nejméně požadované oprávnění.

Voláním [rozhraní API definice role správce prostředků](https://docs.microsoft.com/rest/api/authorization/roledefinitions) zobrazíte seznam všech rolí Azure RBAC a potom Iterujte podle výsledku, abyste našli definici role podle názvu.

Metoda [GetRoleId](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureResourceManagerUtil.cs#L246) ukázkové aplikace ASP.NET MVC implementuje toto volání.

Následující příklad žádosti ukazuje, jak získat identifikátor role RBAC Azure. 09cbd307-aa71-4aca-b346-5f253e6e3ebb je ID předplatného.

    GET https://management.azure.com/subscriptions/09cbd307-aa71-4aca-b346-5f253e6e3ebb/providers/Microsoft.Authorization/roleDefinitions?api-version=2015-07-01 HTTP/1.1

    Authorization: Bearer eyJ0eXAiOiJKV*****fY2lGc5

Odpověď má následující formát:

    HTTP/1.1 200 OK

    {"value":[{"properties":{"roleName":"API Management Service Contributor","type":"BuiltInRole","description":"Lets you manage API Management services, but not access to them.","scope":"/","permissions":[{"actions":["Microsoft.ApiManagement/Services/*","Microsoft.Authorization/*/read","Microsoft.Resources/subscriptions/resources/read","Microsoft.Resources/subscriptions/resourceGroups/read","Microsoft.Resources/subscriptions/resourceGroups/resources/read","Microsoft.Resources/subscriptions/resourceGroups/deployments/*","Microsoft.Insights/alertRules/*","Microsoft.Support/*"],"notActions":[]}]},"id":"/subscriptions/09cbd307-aa71-4aca-b346-5f253e6e3ebb/providers/Microsoft.Authorization/roleDefinitions/312a565d-c81f-4fd8-895a-4e21e48d571c","type":"Microsoft.Authorization/roleDefinitions","name":"312a565d-c81f-4fd8-895a-4e21e48d571c"},{"properties":{"roleName":"Application Insights Component Contributor","type":"BuiltInRole","description":"Lets you manage Application Insights components, but not access to them.","scope":"/","permissions":[{"actions":["Microsoft.Insights/components/*","Microsoft.Insights/webtests/*","Microsoft.Authorization/*/read","Microsoft.Resources/subscriptions/resources/read","Microsoft.Resources/subscriptions/resourceGroups/read","Microsoft.Resources/subscriptions/resourceGroups/resources/read","Microsoft.Resources/subscriptions/resourceGroups/deployments/*","Microsoft.Insights/alertRules/*","Microsoft.Support/*"],"notActions":[]}]},"id":"/subscriptions/09cbd307-aa71-4aca-b346-5f253e6e3ebb/providers/Microsoft.Authorization/roleDefinitions/ae349356-3a1b-4a5e-921d-050484c6347e","type":"Microsoft.Authorization/roleDefinitions","name":"ae349356-3a1b-4a5e-921d-050484c6347e"}]}

Toto rozhraní API nemusíte průběžně volat. Jakmile určíte známý identifikátor GUID definice role, můžete vytvořit ID definice role jako:

    /subscriptions/{subscription_id}/providers/Microsoft.Authorization/roleDefinitions/{well-known-role-guid}

Tady jsou identifikátory běžně používaných předdefinovaných rolí:

| Role | GUID |
| --- | --- |
| Modulu |acdd72a7-3385-48ef-bd42-f606fba81ae7 |
| Skupinou |b24988ac-6180-42a0-ab88-20f7382dd24c |
| Přispěvatel virtuálních počítačů |d73bb868-a0df-4d4d-bd69-98a00b01fccb |
| Přispěvatel Virtual Network |b34d265f-36f7-4a0d-a4d4-e158ca92e90f |
| Přispěvatel účtu úložiště |86e8f5dc-a6e9-4c67-9d15-de283e8eac25 |
| Přispěvatel webu |de139f84-1756-47ae-9be6-808fbbe84772 |
| Přispěvatel webového plánu |2cc479cb-7b4d-49a8-b449-8c00fd0f0a4b |
| Přispěvatel SQL Server |6d8ee4ec-f05a-4a1d-8b00-a9b17e38b437 |
| Přispěvatel databáze SQL |9b7fa17d-e63e-47b0-bb0a-15c516ac86ec |

### <a name="assign-rbac-role-to-application"></a>Přiřazení role RBAC k aplikaci
Máte všechno, co potřebujete k přiřazení příslušné role RBAC k instančnímu objektu, a to pomocí [Správce prostředků vytvořit](https://docs.microsoft.com/rest/api/authorization/roleassignments) rozhraní API pro přiřazení rolí.

Metoda [GrantRoleToServicePrincipalOnSubscription](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureResourceManagerUtil.cs#L170) ukázkové aplikace ASP.NET MVC implementuje toto volání.

Příklad žádosti o přiřazení role RBAC k aplikaci:

    PUT https://management.azure.com/subscriptions/09cbd307-aa71-4aca-b346-5f253e6e3ebb/providers/microsoft.authorization/roleassignments/4f87261d-2816-465d-8311-70a27558df4c?api-version=2015-07-01 HTTP/1.1

    Authorization: Bearer eyJ0eXAiOiJKV1QiL*****FlwO1mM7Cw6JWtfY2lGc5
    Content-Type: application/json
    Content-Length: 230

    {"properties": {"roleDefinitionId":"/subscriptions/09cbd307-aa71-4aca-b346-5f253e6e3ebb/providers/Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c","principalId":"c3097b31-7309-4c59-b4e3-770f8406bad2"}}

V žádosti se použijí následující hodnoty:

| Hlavních | Popis |
| --- | --- |
| 09cbd307-aa71-4aca-b346-5f253e6e3ebb |ID předplatného |
| c3097b31-7309-4c59-b4e3-770f8406bad2 |ID objektu pro instanční objekt aplikace |
| b24988ac-6180-42a0-ab88-20f7382dd24c |ID role přispěvatele |
| 4f87261d-2816-465d-8311-70a27558df4c |Vytvoří se nový identifikátor GUID pro přiřazení nové role. |

Odpověď má následující formát:

    HTTP/1.1 201 Created

    {"properties":{"roleDefinitionId":"/subscriptions/09cbd307-aa71-4aca-b346-5f253e6e3ebb/providers/Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c","principalId":"c3097b31-7309-4c59-b4e3-770f8406bad2","scope":"/subscriptions/09cbd307-aa71-4aca-b346-5f253e6e3ebb"},"id":"/subscriptions/09cbd307-aa71-4aca-b346-5f253e6e3ebb/providers/Microsoft.Authorization/roleAssignments/4f87261d-2816-465d-8311-70a27558df4c","type":"Microsoft.Authorization/roleAssignments","name":"4f87261d-2816-465d-8311-70a27558df4c"}

### <a name="get-app-only-access-token-for-azure-resource-manager"></a>Získat přístupový token jenom pro aplikaci pro Azure Resource Manager
Pokud chcete ověřit, jestli má aplikace přístup k předplatnému, proveďte testovací úlohu v předplatném pomocí tokenu jenom pro aplikace.

Pokud chcete získat přístupový token jenom pro aplikaci, postupujte podle pokynů v části [získání přístupového tokenu jenom pro aplikaci pro Azure AD Graph API](#app-azure-ad-graph)s jinou hodnotou parametru prostředku:

    https://management.core.windows.net/

Metoda [ServicePrincipalHasReadAccessToSubscription](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureResourceManagerUtil.cs#L110) ukázkové aplikace ASP.NET MVC získá přístupový token jenom pro aplikaci pro Azure Resource Manager pomocí Active Directory Authentication Library pro .NET.

#### <a name="get-applications-permissions-on-subscription"></a>Získat oprávnění aplikace v předplatném
Pokud chcete zjistit, jestli vaše aplikace má přístup k předplatnému Azure, můžete také volat rozhraní API pro [Správce prostředků oprávnění](https://docs.microsoft.com/rest/api/authorization/permissions) . Tento přístup se podobá tomu, jak jste zjistili, jestli má uživatel práva pro správu přístupu k předplatnému. Tentokrát ale zavolejte rozhraní API pro přístup k přístupovému tokenu jenom pro aplikaci, který jste dostali v předchozím kroku.

Metoda [ServicePrincipalHasReadAccessToSubscription](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureResourceManagerUtil.cs#L110) ukázkové aplikace ASP.NET MVC implementuje toto volání.

## <a name="manage-connected-subscriptions"></a>Spravovat připojená předplatná
Když je příslušná role RBAC přiřazená k instančnímu objektu vaší aplikace v předplatném, může vaše aplikace zůstat monitorovat nebo spravovat pomocí přístupových tokenů jenom pro aplikace pro Azure Resource Manager.

Pokud vlastník předplatného odebere přiřazení role vaší aplikace pomocí portálu nebo nástrojů příkazového řádku, aplikace už nebude mít přístup k tomuto předplatnému. V takovém případě byste měli uživatele informující o tom, že připojení k předplatnému bylo z vnějšku aplikace těžké, a dát jim možnost opravit připojení. Možnost opravit způsobí opětovné vytvoření přiřazení role, které bylo odstraněno offline.

Stejně jako uživatel povolil připojení předplatných k vaší aplikaci, musíte povolit, aby uživatel odpojil i předplatná. Z bodu správy přístupu v zobrazení odpojení znamená odebrání přiřazení role, které má instanční objekt aplikace v předplatném. Volitelně je možné také odebrat jakýkoli stav v aplikaci pro odběr.
Předplatné můžou odpojit jenom uživatelé, kteří mají oprávnění ke správě přístupu u předplatného.

[Metoda RevokeRoleFromServicePrincipalOnSubscription](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureResourceManagerUtil.cs#L200) ukázkové aplikace ASP.NET MVC implementuje toto volání.

To je to, že uživatelé teď můžou snadno připojit a spravovat svoje předplatná Azure pomocí vaší aplikace.
