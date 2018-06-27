---
title: Azure Resource Manager a ověřování služby Active Directory | Microsoft Docs
description: Příručka pro vývojáře k ověřování pomocí rozhraní API Správce prostředků Azure a Azure Active Directory pro integraci aplikace s jiných předplatných Azure.
services: azure-resource-manager,active-directory
documentationcenter: na
author: dushyantgill
manager: timlt
editor: tysonn
ms.assetid: 17b2b40d-bf42-4c7d-9a88-9938409c5088
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/15/2017
ms.author: dugill
ms.openlocfilehash: ba2466f58b3af0ef208474adb3e4c7ff184ceccc
ms.sourcegitcommit: 0fa8b4622322b3d3003e760f364992f7f7e5d6a9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/27/2018
ms.locfileid: "37018642"
---
# <a name="use-resource-manager-authentication-api-to-access-subscriptions"></a>Ověřování pomocí Správce prostředků rozhraní API pro odběry přístup
## <a name="introduction"></a>Úvod
Pokud jste vývojář softwaru, který potřebuje vytvořit aplikaci, která spravuje prostředky Azure zákazníka, tento článek ukazuje, jak k ověření prostřednictvím rozhraní API Správce prostředků Azure a získat přístup k prostředkům v jiných předplatných.

Aplikaci můžete získat přístup k rozhraní API Resource Manager v několika způsoby:

1. **Uživatel + přístup k aplikaci**: pro aplikace, která přistupují k prostředkům jménem přihlášeného uživatele. Tento postup funguje pro aplikace, jako třeba webové aplikace a nástroje příkazového řádku, které pracují s pouze "interaktivní Správa" prostředků Azure.
2. **Přístup jen aplikace**: pro aplikace, které běží démon služeb a naplánované úlohy. Identity aplikace jsou udělena přímý přístup k prostředkům. Tento postup funguje pro aplikace, které potřebují dlouhodobé bezobslužných (bezobslužná instalace) přístup k Azure.

Tento článek obsahuje podrobné pokyny k vytvoření aplikace, která aktivuje obě tyto metody ověřování. Ukazuje, jak provádět každý krok REST API nebo C#. Kompletní aplikace ASP.NET MVC je k dispozici na [ https://github.com/dushyantgill/VipSwapper/tree/master/CloudSense ](https://github.com/dushyantgill/VipSwapper/tree/master/CloudSense).

## <a name="what-the-web-app-does"></a>Jaké jsou webové aplikace
Webové aplikace:

1. Přihlásí uživatele služby Azure.
2. Požádá uživatele a udělit přístup k webové aplikaci do Resource Manager.
3. Získá uživatele + přístupový token aplikace pro přístup k Resource Manager.
4. Používá token (z kroku 3) k přiřazení objektu aplikace služby do role v rámci předplatného. Tento krok umožňuje dlouhodobé přístup aplikace k předplatnému.
5. Získá jen aplikace přístupový token.
6. Token (z kroku 5) se používá ke správě prostředků v předplatném prostřednictvím Resource Manager.

Zde je postup webové aplikace.

![Tok ověření správce prostředků](./media/resource-manager-api-authentication/Auth-Swim-Lane.png)

Jako uživatel zadejte ID odběru pro odběr, který chcete použít:

![Zadejte ID předplatného](./media/resource-manager-api-authentication/sample-ux-1.png)

Vyberte účet, který chcete použít pro přihlášení.

![Vyberte účet](./media/resource-manager-api-authentication/sample-ux-2.png)

Zadejte svoje přihlašovací údaje.

![Zadejte přihlašovací údaje](./media/resource-manager-api-authentication/sample-ux-3.png)

Aplikaci udělte přístup k vašemu předplatnému Azure:

![Udělení přístupu](./media/resource-manager-api-authentication/sample-ux-4.png)

Správa odběrů připojené:

![Připojení odběru](./media/resource-manager-api-authentication/sample-ux-7.png)

## <a name="register-application"></a>Zaregistrovat aplikaci
Než začnete, kódování, zaregistrujte vaší webové aplikace s Azure Active Directory (AD). Registrace aplikace vytvoří centrální identitu pro vaši aplikaci ve službě Azure AD. Že obsahuje základní informace o vaší aplikaci jako ID klienta OAuth, adresy URL odpovědí a přihlašovací údaje, které vaše aplikace používá k ověření a přístup k rozhraní API Správce Azure Resource Manager. Registrace aplikací taky zaznamenává různé delegovaných oprávnění, které aplikace potřebuje při přístupu k Microsoft APIs jménem uživatele.

Vzhledem k tomu, že vaše aplikace přístup k jiné předplatné, musíte ho nakonfigurovat jako víceklientské aplikace. K provedení ověření, zadejte domény přidružené k vaší služby Azure Active Directory. Pokud chcete zobrazit domény přidružené k vaší služby Azure Active Directory, přihlaste se k portálu.

Následující příklad ukazuje, jak zaregistrovat aplikaci pomocí Azure PowerShell. Musíte mít nejnovější verzi prostředí Azure PowerShell pro tento příkaz fungovat (srpna 2016).

    $app = New-AzureRmADApplication -DisplayName "{app name}" -HomePage "https://{your domain}/{app name}" -IdentifierUris "https://{your domain}/{app name}" -Password "{your password}" -AvailableToOtherTenants $true

Přihlásit se jako aplikace AD, musíte aplikaci ID a hesla. Pokud chcete zobrazit ID aplikace, která je vrácena z předchozí příkaz, použijte:

    $app.ApplicationId

Následující příklad ukazuje, jak pro registraci aplikace pomocí rozhraní příkazového řádku Azure.

    azure ad app create --name {app name} --home-page https://{your domain}/{app name} --identifier-uris https://{your domain}/{app name} --password {your password} --available true

Výsledky budou zahrnovat AppId, který je třeba při ověřování jako aplikace.

### <a name="optional-configuration---certificate-credential"></a>Volitelné konfigurace – certifikát přihlašovacích údajů
Azure AD podporuje přihlašovací údaje certifikátu pro aplikace: vytvoření certifikátu podepsaného svým držitelem, zachovat privátní klíč a přidejte veřejný klíč registrace aplikace Azure AD. Pro ověřování vaše aplikace odešle malé datové části do služby Azure AD podepsaná pomocí soukromého klíče a Azure AD ověří podpis pomocí veřejného klíče, který je zaregistrovaný.

Informace o vytvoření aplikace AD pomocí certifikátu najdete v tématu [použití Azure PowerShell k vytvoření objektu služby pro přístup k prostředkům](resource-group-authenticate-service-principal.md#create-service-principal-with-certificate-from-certificate-authority) nebo [použití Azure CLI pro vytvoření objektu služby pro přístup k prostředkům](resource-group-authenticate-service-principal-cli.md) .

## <a name="get-tenant-id-from-subscription-id"></a>Získání ID klienta z ID předplatného
K žádosti o token, který slouží k volání Resource Manager, aplikace musí znát ID klienta Azure AD klienta, který je hostitelem předplatné Azure. S největší pravděpodobností vaši uživatelé věděli, jejich ID předplatného, ale nemusí věděli, jejich klienta ID pro Azure Active Directory. Chcete-li získat ID uživatele klienta, požádejte uživatele pro ID předplatného. Zadejte toto předplatné ID při odesílání žádosti o předplatném:

    https://management.azure.com/subscriptions/{subscription-id}?api-version=2015-01-01

Žádost se nezdaří, protože uživatel nebyl ještě přihlášen, ale můžete načíst ID klienta z odpovědi. V této výjimky načíst ID klienta z hodnotu hlavičky odpovědi pro **WWW-Authenticate**. Zobrazí tuto implementaci v [GetDirectoryForSubscription](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureResourceManagerUtil.cs#L20) metoda.

## <a name="get-user--app-access-token"></a>Získat uživatele + přístupový token aplikace
Vaše aplikace přesměruje uživatele do služby Azure AD s OAuth 2.0 autorizovat požadavek – k ověření přihlašovacích údajů uživatele a získat zpátky autorizační kód. Vaše aplikace používá autorizační kód se získat přístupový token pro Resource Manager. [ConnectSubscription](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/Controllers/HomeController.cs#L42) metoda vytvoří žádost o ověření.

Tento článek popisuje požadavky REST API pro ověření uživatele. Pomocné knihovny můžete použít také k provedení ověření v kódu. Další informace o tyto knihovny najdete v tématu [knihovny Azure Active Directory Authentication](../active-directory/active-directory-authentication-libraries.md). Pokyny k integraci správy identit v aplikaci najdete v tématu [Příručka pro vývojáře Azure Active Directory](../active-directory/active-directory-developers-guide.md).

### <a name="auth-request-oauth-20"></a>Žádost o ověření (OAuth 2.0)
Vydejte Open ID Connect/OAuth2.0 autorizovat požadavku koncového bodu Azure AD Authorize:

    https://login.microsoftonline.com/{tenant-id}/OAuth2/Authorize

Jsou popsané v tématu parametrů řetězce dotazu, které jsou k dispozici pro tento požadavek [autorizační kód požadavku](../active-directory/develop/active-directory-protocols-oauth-code.md#request-an-authorization-code) článku.

Následující příklad ukazuje, jak požádat o OAuth2.0 autorizace:

    https://login.microsoftonline.com/{tenant-id}/OAuth2/Authorize?client_id=a0448380-c346-4f9f-b897-c18733de9394&response_mode=query&response_type=code&redirect_uri=http%3a%2f%2fwww.vipswapper.com%2fcloudsense%2fAccount%2fSignIn&resource=https%3a%2f%2fgraph.windows.net%2f&domain_hint=live.com

Azure AD ověřuje uživatele a v případě potřeby požádá uživatele a udělit oprávnění k aplikaci. Adresa URL odpovědi aplikace vrátí autorizační kód. V závislosti na požadované response_mode, Azure AD buď odešle zpět data v řetězci dotazu nebo jako následná data.

    code=AAABAAAAiL****FDMZBUwZ8eCAA&session_state=2d16bbce-d5d1-443f-acdf-75f6b0ce8850

### <a name="auth-request-open-id-connect"></a>Žádost o ověření (Open ID Connect)
Pokud chcete nejen pro přístup k Azure Resource Manager jménem uživatele, ale také umožnit uživatelům přihlášení k aplikaci pomocí svého účtu Azure AD, vydejte Open ID připojení autorizaci požadavků. Aplikace s Open ID Connect také přijímá požadavku id_token z Azure AD, které aplikace můžete používat k přihlášení uživatele.

Jsou popsané v tématu parametrů řetězce dotazu, které jsou k dispozici pro tento požadavek [odeslání žádosti o přihlášení](../active-directory/develop/active-directory-protocols-openid-connect-code.md#send-the-sign-in-request) článku.

Jedná se o příklad Open ID Connect žádost:

     https://login.microsoftonline.com/{tenant-id}/OAuth2/Authorize?client_id=a0448380-c346-4f9f-b897-c18733de9394&response_mode=form_post&response_type=code+id_token&redirect_uri=http%3a%2f%2fwww.vipswapper.com%2fcloudsense%2fAccount%2fSignIn&resource=https%3a%2f%2fgraph.windows.net%2f&scope=openid+profile&nonce=63567Dc4MDAw&domain_hint=live.com&state=M_12tMyKaM8

Azure AD ověřuje uživatele a v případě potřeby požádá uživatele a udělit oprávnění k aplikaci. Adresa URL odpovědi aplikace vrátí autorizační kód. V závislosti na požadované response_mode, Azure AD buď odešle zpět data v řetězci dotazu nebo jako následná data.

Příklad Open ID Connect odpověď je:

    code=AAABAAAAiL*****I4rDWd7zXsH6WUjlkIEQxIAA&id_token=eyJ0eXAiOiJKV1Q*****T3GrzzSFxg&state=M_12tMyKaM8&session_state=2d16bbce-d5d1-443f-acdf-75f6b0ce8850

### <a name="token-request-oauth20-code-grant-flow"></a>Žádosti o token (OAuth2.0 kód Grant toku)
Teď, když vaše aplikace přijal autorizační kód z Azure AD, je čas k získání tokenu přístupu pro Azure Resource Manager.  POST OAuth2.0 kódu Grant tokenu požadavek na koncový bod Azure AD Token:

    https://login.microsoftonline.com/{tenant-id}/OAuth2/Token

Jsou popsané v tématu parametrů řetězce dotazu, které jsou k dispozici pro tento požadavek [použít autorizační kód](../active-directory/develop/active-directory-protocols-oauth-code.md#use-the-authorization-code-to-request-an-access-token) článku.

Následující příklad ukazuje žádost o token grant kód s pověřením heslo:

    POST https://login.microsoftonline.com/7fe877e6-a150-4992-bbfe-f517e304dfa0/oauth2/token HTTP/1.1

    Content-Type: application/x-www-form-urlencoded
    Content-Length: 1012

    grant_type=authorization_code&code=AAABAAAAiL9Kn2Z*****L1nVMH3Z5ESiAA&redirect_uri=http%3A%2F%2Flocalhost%3A62080%2FAccount%2FSignIn&client_id=a0448380-c346-4f9f-b897-c18733de9394&client_secret=olna84E8*****goScOg%3D

Při práci s přihlašovací údaje certifikátu, vytvořte Token pro webové JSON (JWT) a přihlašovací (RSA SHA256) pomocí soukromého klíče certifikátu pověření vaší aplikace. Typy deklarací identity pro daný token, jsou uvedeny v [deklarace tokenů JWT](../active-directory/develop/active-directory-protocols-oauth-code.md#jwt-token-claims). Odkaz, najdete v článku [knihovna ověřování Active Directory (.NET) kód](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/blob/dev/src/ADAL.PCL.Desktop/CryptographyHelper.cs) k podepisování tokenů JWT Assertion klienta.

Najdete v článku [Open ID Connect specifikace](http://openid.net/specs/openid-connect-core-1_0.html#ClientAuthentication) podrobnosti o ověření klienta.

Následující příklad ukazuje žádost o token grant kód s certifikát přihlašovacích údajů:

    POST https://login.microsoftonline.com/7fe877e6-a150-4992-bbfe-f517e304dfa0/oauth2/token HTTP/1.1

    Content-Type: application/x-www-form-urlencoded
    Content-Length: 1012

    grant_type=authorization_code&code=AAABAAAAiL9Kn2Z*****L1nVMH3Z5ESiAA&redirect_uri=http%3A%2F%2Flocalhost%3A62080%2FAccount%2FSignIn&client_id=a0448380-c346-4f9f-b897-c18733de9394&client_assertion_type=urn%3Aietf%3Aparams%3Aoauth%3Aclient-assertion-type%3Ajwt-bearer&client_assertion=eyJhbG*****Y9cYo8nEjMyA

Odpověď příklad pro kód udělit token:

    HTTP/1.1 200 OK

    {"token_type":"Bearer","expires_in":"3599","expires_on":"1432039858","not_before":"1432035958","resource":"https://management.core.windows.net/","access_token":"eyJ0eXAiOiJKV1Q****M7Cw6JWtfY2lGc5A","refresh_token":"AAABAAAAiL9Kn2Z****55j-sjnyYgAA","scope":"user_impersonation","id_token":"eyJ0eXAiOiJKV*****-drP1J3P-HnHi9Rr46kGZnukEBH4dsg"}

#### <a name="handle-code-grant-token-response"></a>Zpracování odpovědi tokenu grant kódu
Úspěšné odpovědi tokenu obsahuje (uživatele + aplikace) přístupový token pro Azure Resource Manager. Vaše aplikace používá token přístupu pro přístup k prostředku správce jménem uživatele. Životnost přístupové tokeny vydané službou Azure AD je jedna hodina. Není pravděpodobné, že vaše webová aplikace musí obnovit (uživatele + aplikace) přístupový token. Pokud je potřeba obnovit přístupový token, použijte token obnovení, které aplikace přijímá v odpovědi tokenu. POST OAuth2.0 tokenu požadavek na koncový bod Azure AD Token:

    https://login.microsoftonline.com/{tenant-id}/OAuth2/Token

Parametry, které chcete použít s požadavkem, aktualizace jsou popsané v [aktualizovat přístupový token](../active-directory/develop/active-directory-protocols-oauth-code.md#refreshing-the-access-tokens).

Následující příklad ukazuje způsob použití aktualizace tokenu:

    POST https://login.microsoftonline.com/7fe877e6-a150-4992-bbfe-f517e304dfa0/oauth2/token HTTP/1.1

    Content-Type: application/x-www-form-urlencoded
    Content-Length: 1012

    grant_type=refresh_token&refresh_token=AAABAAAAiL9Kn2Z****55j-sjnyYgAA&client_id=a0448380-c346-4f9f-b897-c18733de9394&client_secret=olna84E8*****goScOg%3D

Tokeny obnovení lze získat nové přístupové tokeny pro Azure Resource Manager, ale nejsou vhodné pro offline přístup k vaší aplikací. Doba platnosti tokenů aktualizace je omezený a tokeny obnovení je vázána na uživatele. Pokud uživatel opustí organizaci, zruší aplikace pomocí obnovovacího tokenu přístupu. Tento přístup není vhodný pro aplikace, které jsou používány týmy ke správě svých prostředků Azure.

## <a name="check-if-user-can-assign-access-to-subscription"></a>Zkontrolujte, pokud uživatele lze přiřadit přístup k předplatnému
Aplikace nyní obsahuje token pro přístup k Azure Resource Manager jménem uživatele. Dalším krokem je připojení aplikace k předplatnému. Po připojení, aplikace spravovat těchto předplatných, i v případě, že uživatel není přítomen (dlouhodobé offline přístup).

Pro každé předplatné pro připojení, volejte [seznamu oprávnění správce prostředků](https://docs.microsoft.com/rest/api/authorization/permissions) rozhraní API k určení, zda má uživatel práva pro správu přístupu pro předplatné.

[UserCanManagerAccessForSubscription](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureResourceManagerUtil.cs#L44) metoda ukázkovou aplikaci ASP.NET MVC implementuje toto volání.

Následující příklad ukazuje, jak požádat o oprávnění uživatele na předplatném. 83cfe939-2402-4581-b761-4f59b0a041e4 je ID předplatného.

    GET https://management.azure.com/subscriptions/83cfe939-2402-4581-b761-4f59b0a041e4/providers/microsoft.authorization/permissions?api-version=2015-07-01 HTTP/1.1

    Authorization: Bearer eyJ0eXAiOiJKV1QiLC***lwO1mM7Cw6JWtfY2lGc5A

Příklad odpovědi a získejte oprávnění uživatele na základě předplatného je:

    HTTP/1.1 200 OK

    {"value":[{"actions":["*"],"notActions":["Microsoft.Authorization/*/Write","Microsoft.Authorization/*/Delete"]},{"actions":["*/read"],"notActions":[]}]}

Oprávnění rozhraní API vrátí více oprávnění. Každé oprávnění se skládá z povolených akcí (**akce**) a zakázané akce (**notactions**). Pokud je přítomen v povolených akcí všechna oprávnění a nejsou k dispozici v nepovoleném akce tohoto oprávnění, má uživatel k provedení této akce. **Microsoft.Authorization/RoleAssignments/Write** je akce uděluje oprávnění správy. Aplikace musí analyzovat výsledek oprávnění pro hledání shody regex na tento řetězec akce v **akce** a **notactions** jednotlivých oprávnění.

## <a name="get-app-only-access-token"></a>Získání tokenu přístupu jen aplikace
Teď víte, pokud uživatele lze přiřadit přístup k předplatnému Azure. Další kroky jsou:

1. Přiřadíte příslušné role RBAC identity aplikace v předplatném.
2. Ověření přiřazení přístupu pomocí dotazů na oprávnění aplikace v předplatném nebo přístupem k Resource Manager pomocí tokenu jen aplikace.
3. Zaznamenejte připojení ve struktuře vaší aplikace "připojené odběry" data - uložením ID předplatného.

Podíváme blíže v prvním kroku. Pokud chcete přiřadit příslušné role RBAC identitu aplikace, je třeba určit:

* ID objektu identity aplikace v uživatele Azure Active Directory
* Identifikátor role RBAC, který vaše aplikace vyžaduje na předplatné

Když se aplikace ověřuje uživatele z Azure AD, vytvoří objekt služby zabezpečení pro vaši aplikaci v této službě Azure AD. Azure umožňuje role RBAC přiřazení objekty služby udělit přímý přístup k odpovídající aplikace na prostředky Azure. Tato akce je přesně co chcete udělat. Dotaz rozhraní Azure AD Graph API k určení identifikátor objektu služby vaší aplikace v přihlášeného uživatele je Azure AD.

Můžete mít pouze přístupový token pro Azure Resource Manager – budete potřebovat nový přístupový token pro volání rozhraní Azure AD Graph API. Každá aplikace ve službě Azure AD má oprávnění k dotazování vlastní objekt zabezpečení služby, stačí jen aplikace přístupový token.

<a id="app-azure-ad-graph" />

### <a name="get-app-only-access-token-for-azure-ad-graph-api"></a>Získání tokenu přístupu jen pro aplikace pro Azure AD Graph API
K ověření vaší aplikace a získat token do Azure AD Graph API, vydávat OAuth2.0 udělení pověření klienta žádosti o token toku koncový bod tokenu Azure AD (**https://login.microsoftonline.com/{directory_domain_name}/OAuth2/Token**).

[GetObjectIdOfServicePrincipalInOrganization](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureADGraphAPIUtil.cs) metoda ukázkové aplikace ASP.net MVC získá přístup jen aplikace tokenu pro rozhraní Graph API pomocí knihovny Active Directory Authentication Library pro .NET.

Jsou popsané v tématu parametrů řetězce dotazu, které jsou k dispozici pro tento požadavek [žádosti o Token k přístupu](../active-directory/develop/active-directory-protocols-oauth-service-to-service.md#request-an-access-token) článku.

Požadavek příklad token udělení pověření klienta:

    POST https://login.microsoftonline.com/62e173e9-301e-423e-bcd4-29121ec1aa24/oauth2/token HTTP/1.1
    Content-Type: application/x-www-form-urlencoded
    Content-Length: 187</pre>
    <pre>grant_type=client_credentials&client_id=a0448380-c346-4f9f-b897-c18733de9394&resource=https%3A%2F%2Fgraph.windows.net%2F &client_secret=olna8C*****Og%3D

Odpověď příklad token udělení pověření klienta:

    HTTP/1.1 200 OK

    {"token_type":"Bearer","expires_in":"3599","expires_on":"1432039862","not_before":"1432035962","resource":"https://graph.windows.net/","access_token":"eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWmNBVGZNNXBPWWlKSE1iYTlnb0VLWSIsImtpZCI6Ik1uQ19WWmNBVGZNNXBPWWlKSE1iYTlnb0VLWSJ9.eyJhdWQiOiJodHRwczovL2dyYXBoLndpbmRv****G5gUTV-kKorR-pg"}

### <a name="get-objectid-of-application-service-principal-in-user-azure-ad"></a>Získejte ObjectId aplikace instanční objekt v uživatele Azure AD
Nyní, použijte jen aplikace přístupového tokenu k dotazu [objekty služby Azure AD Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#serviceprincipal-entity) rozhraní API pro zjištění objektu ID aplikace instančního objektu v adresáři.

[GetObjectIdOfServicePrincipalInOrganization](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureADGraphAPIUtil.cs#) metoda ukázkové aplikace ASP.net MVC implementuje toto volání.

Následující příklad ukazuje, jak požádat o aplikace instanční objekt. a0448380-c346-4f9f-b897-c18733de9394 je ID klienta aplikace.

    GET https://graph.windows.net/62e173e9-301e-423e-bcd4-29121ec1aa24/servicePrincipals?api-version=1.5&$filter=appId%20eq%20'a0448380-c346-4f9f-b897-c18733de9394' HTTP/1.1

    Authorization: Bearer eyJ0eXAiOiJK*****-kKorR-pg

Následující příklad ukazuje hlavní odpověď na žádost o služby aplikace

    HTTP/1.1 200 OK

    {"odata.metadata":"https://graph.windows.net/62e173e9-301e-423e-bcd4-29121ec1aa24/$metadata#directoryObjects/Microsoft.DirectoryServices.ServicePrincipal","value":[{"odata.type":"Microsoft.DirectoryServices.ServicePrincipal","objectType":"ServicePrincipal","objectId":"9b5018d4-6951-42ed-8a92-f11ec283ccec","deletionTimestamp":null,"accountEnabled":true,"appDisplayName":"CloudSense","appId":"a0448380-c346-4f9f-b897-c18733de9394","appOwnerTenantId":"62e173e9-301e-423e-bcd4-29121ec1aa24","appRoleAssignmentRequired":false,"appRoles":[],"displayName":"CloudSense","errorUrl":null,"homepage":"http://www.vipswapper.com/cloudsense","keyCredentials":[],"logoutUrl":null,"oauth2Permissions":[{"adminConsentDescription":"Allow the application to access CloudSense on behalf of the signed-in user.","adminConsentDisplayName":"Access CloudSense","id":"b7b7338e-683a-4796-b95e-60c10380de1c","isEnabled":true,"type":"User","userConsentDescription":"Allow the application to access CloudSense on your behalf.","userConsentDisplayName":"Access CloudSense","value":"user_impersonation"}],"passwordCredentials":[],"preferredTokenSigningKeyThumbprint":null,"publisherName":"vipswapper"quot;,"replyUrls":["http://www.vipswapper.com/cloudsense","http://www.vipswapper.com","http://vipswapper.com","http://vipswapper.azurewebsites.net","http://localhost:62080"],"samlMetadataUrl":null,"servicePrincipalNames":["http://www.vipswapper.com/cloudsense","a0448380-c346-4f9f-b897-c18733de9394"],"tags":["WindowsAzureActiveDirectoryIntegratedApp"]}]}

### <a name="get-azure-rbac-role-identifier"></a>Získat identifikátor role Azure RBAC
Přiřadit příslušné role RBAC k hlavní službě, je třeba určit identifikátor role Azure RBAC.

Pravé role RBAC pro aplikaci:

* Pokud vaše aplikace sleduje pouze odběru, beze změn, vyžaduje pouze oprávnění čtečky u předplatného. Přiřazení **čtečky** role.
* Pokud vaše aplikace spravuje Azure předplatného, vytváření, úpravy nebo odstranění entity, vyžaduje oprávnění přispěvatele.
  * Pokud chcete spravovat konkrétní typ prostředku, přiřadíte role Přispěvatel konkrétní prostředky (Přispěvatel virtuálních počítačů, Přispěvatel sítě, Přispěvatel účet úložiště, atd.)
  * Ke správě libovolného typu prostředku, přiřadit **Přispěvatel** role.

Přiřazení role pro vaši aplikaci se zobrazí uživatelům, takže vyberte nejmenší požadované oprávnění.

Volání [definice role správce prostředků rozhraní API](https://docs.microsoft.com/rest/api/authorization/roledefinitions) seznamu role Azure RBAC a hledání pak iterace v výsledek, který má najít definice požadované role podle názvu.

[GetRoleId](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureResourceManagerUtil.cs#L246) metoda ukázkovou aplikaci ASP.net MVC implementuje toto volání.

Následující příklad žádost ukazuje, jak získat identifikátor role Azure RBAC. 09cbd307-aa71-4aca-b346-5f253e6e3ebb je ID předplatného.

    GET https://management.azure.com/subscriptions/09cbd307-aa71-4aca-b346-5f253e6e3ebb/providers/Microsoft.Authorization/roleDefinitions?api-version=2015-07-01 HTTP/1.1

    Authorization: Bearer eyJ0eXAiOiJKV*****fY2lGc5

Odpověď je v následujícím formátu:

    HTTP/1.1 200 OK

    {"value":[{"properties":{"roleName":"API Management Service Contributor","type":"BuiltInRole","description":"Lets you manage API Management services, but not access to them.","scope":"/","permissions":[{"actions":["Microsoft.ApiManagement/Services/*","Microsoft.Authorization/*/read","Microsoft.Resources/subscriptions/resources/read","Microsoft.Resources/subscriptions/resourceGroups/read","Microsoft.Resources/subscriptions/resourceGroups/resources/read","Microsoft.Resources/subscriptions/resourceGroups/deployments/*","Microsoft.Insights/alertRules/*","Microsoft.Support/*"],"notActions":[]}]},"id":"/subscriptions/09cbd307-aa71-4aca-b346-5f253e6e3ebb/providers/Microsoft.Authorization/roleDefinitions/312a565d-c81f-4fd8-895a-4e21e48d571c","type":"Microsoft.Authorization/roleDefinitions","name":"312a565d-c81f-4fd8-895a-4e21e48d571c"},{"properties":{"roleName":"Application Insights Component Contributor","type":"BuiltInRole","description":"Lets you manage Application Insights components, but not access to them.","scope":"/","permissions":[{"actions":["Microsoft.Insights/components/*","Microsoft.Insights/webtests/*","Microsoft.Authorization/*/read","Microsoft.Resources/subscriptions/resources/read","Microsoft.Resources/subscriptions/resourceGroups/read","Microsoft.Resources/subscriptions/resourceGroups/resources/read","Microsoft.Resources/subscriptions/resourceGroups/deployments/*","Microsoft.Insights/alertRules/*","Microsoft.Support/*"],"notActions":[]}]},"id":"/subscriptions/09cbd307-aa71-4aca-b346-5f253e6e3ebb/providers/Microsoft.Authorization/roleDefinitions/ae349356-3a1b-4a5e-921d-050484c6347e","type":"Microsoft.Authorization/roleDefinitions","name":"ae349356-3a1b-4a5e-921d-050484c6347e"}]}

Není nutné volat toto rozhraní API průběžně. Jakmile jste zjistili, dobře známé GUID definice role, můžete vytvořit ID definice role jako:

    /subscriptions/{subscription_id}/providers/Microsoft.Authorization/roleDefinitions/{well-known-role-guid}

Zde jsou identifikátory běžně používané předdefinovaných rolí:

| Role | GUID |
| --- | --- |
| Čtenář |acdd72a7-3385-48EF-bd42-f606fba81ae7 |
| Přispěvatel |b24988ac-6180-42A0-ab88-20f7382dd24c |
| Přispěvatel virtuálních počítačů |d73bb868-a0df-4d4d-bd69-98a00b01fccb |
| Přispěvatel virtuální sítě |b34d265f-36f7-4a0d-a4d4-e158ca92e90f |
| Přispěvatel účtů úložiště |86e8f5dc-a6e9-4c67-9d15-de283e8eac25 |
| Přispěvatel webů |de139f84-1756-47ae-9be6-808fbbe84772 |
| Přispěvatel webových plánů |2cc479cb-7b4d-49a8-b449-8c00fd0f0a4b |
| Přispěvatel SQL Serveru |6d8ee4ec-f05a-4a1d-8b00-a9b17e38b437 |
| Přispěvatel databází SQL |9b7fa17d-e63e-47b0-bb0a-15c516ac86ec |

### <a name="assign-rbac-role-to-application"></a>Přiřazení RBAC role aplikace
Budete mít všechno, co je potřeba přiřadit příslušné role RBAC instanční objekt pomocí [Resource Manager vytvořit přiřazení role](https://docs.microsoft.com/rest/api/authorization/roleassignments) rozhraní API.

[GrantRoleToServicePrincipalOnSubscription](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureResourceManagerUtil.cs#L170) metoda ukázkovou aplikaci ASP.net MVC implementuje toto volání.

Příklad požadavek přiřazení RBAC role aplikace:

    PUT https://management.azure.com/subscriptions/09cbd307-aa71-4aca-b346-5f253e6e3ebb/providers/microsoft.authorization/roleassignments/4f87261d-2816-465d-8311-70a27558df4c?api-version=2015-07-01 HTTP/1.1

    Authorization: Bearer eyJ0eXAiOiJKV1QiL*****FlwO1mM7Cw6JWtfY2lGc5
    Content-Type: application/json
    Content-Length: 230

    {"properties": {"roleDefinitionId":"/subscriptions/09cbd307-aa71-4aca-b346-5f253e6e3ebb/providers/Microsoft.Authorization/roleDefinitions/acdd72a7-3385-48ef-bd42-f606fba81ae7","principalId":"c3097b31-7309-4c59-b4e3-770f8406bad2"}}

V požadavku budou použity následující hodnoty:

| Guid | Popis |
| --- | --- |
| 09cbd307-aa71-4aca-b346-5f253e6e3ebb |ID předplatného |
| c3097b31-7309-4C59-b4e3-770f8406bad2 |ID objektu objektu služby aplikace |
| acdd72a7-3385-48EF-bd42-f606fba81ae7 |ID role čtenáře |
| 4f87261d-2816-465D-8311-70a27558df4c |nový identifikátor guid vytvořit nové přiřazení role |

Odpověď je v následujícím formátu:

    HTTP/1.1 201 Created

    {"properties":{"roleDefinitionId":"/subscriptions/09cbd307-aa71-4aca-b346-5f253e6e3ebb/providers/Microsoft.Authorization/roleDefinitions/acdd72a7-3385-48ef-bd42-f606fba81ae7","principalId":"c3097b31-7309-4c59-b4e3-770f8406bad2","scope":"/subscriptions/09cbd307-aa71-4aca-b346-5f253e6e3ebb"},"id":"/subscriptions/09cbd307-aa71-4aca-b346-5f253e6e3ebb/providers/Microsoft.Authorization/roleAssignments/4f87261d-2816-465d-8311-70a27558df4c","type":"Microsoft.Authorization/roleAssignments","name":"4f87261d-2816-465d-8311-70a27558df4c"}

### <a name="get-app-only-access-token-for-azure-resource-manager"></a>Získání jen aplikace tokenu přístupu pro Azure Resource Manager
K ověření aplikaci má požadovaný přístup na předplatné, provést úlohu test na odběru pomocí tokenu jen aplikace.

Chcete-li získat token přístupu jenom aplikace, postupujte podle pokynů z oddílu [získání tokenu přístupu jen pro aplikace pro Azure AD Graph API](#app-azure-ad-graph), s jinou hodnotu pro parametr prostředků:

    https://management.core.windows.net/

[ServicePrincipalHasReadAccessToSubscription](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureResourceManagerUtil.cs#L110) metoda ukázkové aplikace ASP.NET MVC získá přístup jen aplikace token pro Azure Resource Manager pomocí knihovny Active Directory Authentication Library pro .net.

#### <a name="get-applications-permissions-on-subscription"></a>Získat oprávnění aplikace v předplatném
Pokud chcete zkontrolovat, že aplikace má požadovaný přístup na základě předplatného služby Azure, můžete také zavolat [oprávnění správce prostředků](https://docs.microsoft.com/rest/api/authorization/permissions) rozhraní API. Tento přístup je podobná jak můžete určit, zda má uživatel práva pro správu přístupu pro předplatné. Tentokrát však volejte oprávnění rozhraní API s tímto tokenem přístupu jenom aplikace, který jste obdrželi v předchozím kroku.

[ServicePrincipalHasReadAccessToSubscription](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureResourceManagerUtil.cs#L110) metoda ukázkovou aplikaci ASP.NET MVC implementuje toto volání.

## <a name="manage-connected-subscriptions"></a>Správa připojené odběrů
Pokud příslušné role RBAC je přiřazen k objektu v předplatném služby vaší aplikace, aplikace můžete zachovat monitorování nebo správa pomocí jen aplikace přístupových tokenů pro Azure Resource Manager.

Pokud vlastník předplatného Odebere přiřazení role vaší aplikace pomocí portálu nebo nástroje příkazového řádku, vaše aplikace není nadále přístup k tomuto předplatnému. V takovém případě by měl upozornit uživatele, který připojení k předplatnému bylo porušeno z mimo aplikaci a jim poskytnout možnost "Opravit" připojení. "Opravit" by znovu vytvořit přiřazení role, který byl odstraněn do offline režimu.

Stejně jako jste povolili uživatelům připojit odběry do vaší aplikace, musíte povolit uživatelům příliš odpojit odběry. Z přístup správu hlediska odpojte znamená odebrání přiřazení role, který má aplikace instanční objekt na předplatné. Volitelně může být jakýkoli stav v aplikaci pro předplatné odstraněna příliš.
Pouze uživatelé s oprávněním správy přístupu u předplatného je moct odpojit předplatné.

[RevokeRoleFromServicePrincipalOnSubscription metoda](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureResourceManagerUtil.cs#L200) rozhraní ASP.net MVC implementuje ukázkovou aplikaci toto volání.

Je to – uživatelé teď můžete snadno připojit a spravovat svá předplatná Azure s vaší aplikací.
