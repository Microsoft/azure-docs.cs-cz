---
title: Ověřování služby Active Directory a Azure Resource Manageru | Dokumentace Microsoftu
description: Příručka pro vývojáře k ověřování pomocí rozhraní API Azure Resource Manageru a Azure Active Directory pro integraci aplikace s jiných předplatných Azure.
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
ms.date: 07/12/2018
ms.author: dugill
ms.openlocfilehash: a81c1d20e0f7b58c132a5ece04f05d6740c2308f
ms.sourcegitcommit: f715dcc29873aeae40110a1803294a122dfb4c6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/14/2019
ms.locfileid: "56266989"
---
# <a name="use-resource-manager-authentication-api-to-access-subscriptions"></a>Ověřování pomocí Správce prostředků rozhraní API pro přístup k předplatným

Pokud jste vývojář softwaru, který je potřeba vytvořit aplikaci, která spravuje prostředky Azure zákazníka, tento článek popisuje, jak k ověření pomocí rozhraní API Azure Resource Manageru a získání přístupu k prostředkům v jiných předplatných.

Vaše aplikace neměly přístup k rozhraní API Resource Manageru v několika způsoby:

1. **Přístup uživatele a aplikace**: pro aplikace, které přístup k prostředkům jménem přihlášeného uživatele. Tento postup funguje u aplikací, jako jsou webové aplikace a nástroje příkazového řádku, která se týkají pouze "interaktivní management" prostředků Azure.
2. **Přístup jen pro aplikace**: pro aplikace, které běží služby démonů a naplánované úlohy. Přímý přístup k prostředkům, je udělen identitě aplikace. Tento postup funguje pro aplikace, které potřebují dlouhodobé bezobslužného (bezobslužné) přístup k Azure.

Tento článek obsahuje podrobné pokyny k vytvoření aplikace, která používá obě tyto metody ověřování. Ukazuje, jak provést jednotlivé kroky pomocí rozhraní REST API nebo C#. Dokončení aplikace ASP.NET MVC je k dispozici na [ https://github.com/dushyantgill/VipSwapper/tree/master/CloudSense ](https://github.com/dushyantgill/VipSwapper/tree/master/CloudSense).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="what-the-web-app-does"></a>Co dělá webové aplikace

Webové aplikace:

1. Přihlásí uživatele Azure.
2. Požádá uživatele a udělit přístup k webovým aplikacím na Resource Manager.
3. Získá uživatele a aplikace přístupový token pro přístup k Resource Manageru.
4. Používá token (z kroku 3) k přiřazení instančního objektu aplikace k roli v předplatném. Tento krok umožňuje dlouhodobé přístup aplikace k předplatnému.
5. Přístup jen pro aplikace získá token.
6. Používá token (z kroku 5) ke správě prostředků v předplatném prostřednictvím Resource Manageru.

Tady je tok webové aplikace.

![Tok ověřování Resource Manageru](./media/resource-manager-api-authentication/Auth-Swim-Lane.png)

Jako uživatel zadejte ID předplatného pro předplatné, které chcete použít:

![Zadejte ID předplatného](./media/resource-manager-api-authentication/sample-ux-1.png)

Vyberte účet, který chcete použít pro přihlášení.

![Vyberte účet](./media/resource-manager-api-authentication/sample-ux-2.png)

Zadejte svoje přihlašovací údaje.

![Zadejte přihlašovací údaje](./media/resource-manager-api-authentication/sample-ux-3.png)

Udělte přístup k aplikacím pro vaše předplatná Azure:

![Udělení přístupu](./media/resource-manager-api-authentication/sample-ux-4.png)

Správa vašich připojených předplatných:

![Připojit předplatné](./media/resource-manager-api-authentication/sample-ux-7.png)

## <a name="register-application"></a>Zaregistrovat aplikaci
Předtím, než se pustíte do psaní kódu, zaregistrujte webovou aplikaci s Azure Active Directory (AD). Registrace aplikace vytvoří centrální identitu pro vaši aplikaci ve službě Azure AD. Obsahuje základní informace o vaší aplikaci, jako je ID klienta OAuth, adresy URL odpovědí a přihlašovací údaje, které vaše aplikace používá k ověřování a přístup k rozhraní API Azure Resource Manageru. Registrace aplikace taky zaznamenává různých delegovaná oprávnění, které vaše aplikace potřebuje při přístupu k Microsoft APIs jménem uživatele.

Vzhledem k tomu, že vaše aplikace nemá přístup k jiné předplatné, musíte ho nakonfigurovat jako aplikaci s více tenanty. Chcete-li projít ověřením, zadejte domény přidružené k Azure Active Directory. Pokud chcete zobrazit domény přidružené k Azure Active Directory, přihlaste se k portálu.

Následující příklad ukazuje, jak zaregistrovat aplikaci pomocí Azure Powershellu. Musíte mít nejnovější verze Azure Powershellu pro tento příkaz fungovat (srpen 2016).

```azurepowershell-interactive
$app = New-AzADApplication -DisplayName "{app name}" -HomePage "https://{your domain}/{app name}" -IdentifierUris "https://{your domain}/{app name}" -Password "{your password}" -AvailableToOtherTenants $true
```

Přihlásit se jako aplikace AD, budete potřebovat ID aplikace a heslo. Pokud chcete zobrazit ID aplikace, která je vrácena z předchozího příkazu, použijte:

```azurepowershell-interactive
$app.ApplicationId
```

Následující příklad ukazuje, jak zaregistrovat aplikaci pomocí Azure CLI.

```azurecli-interactive
az ad app create --display-name {app name} --homepage https://{your domain}/{app name} --identifier-uris https://{your domain}/{app name} --password {your password} --available-to-other-tenants true
```

Výsledky budou zahrnovat AppId, který budete potřebovat při ověřování jako aplikace.

### <a name="optional-configuration---certificate-credential"></a>Volitelné konfigurace – certifikát přihlašovacích údajů
Certifikát přihlašovacích údajů Azure AD podporuje také pro aplikace: vytvoření certifikátu podepsaného svým držitelem, zachovat privátní klíč a přidejte veřejný klíč, registrace aplikace Azure AD. Pro ověřování vaše aplikace odešle do služby Azure AD podepsán pomocí privátního klíče malé datové části a Azure AD ověří podpis pomocí veřejného klíče, které jste zaregistrovali.

Informace o vytvoření aplikace AD s certifikátem, naleznete v tématu [pomocí prostředí Azure PowerShell k vytvoření instančního objektu pro přístup k prostředkům](../active-directory/develop/howto-authenticate-service-principal-powershell.md#create-service-principal-with-certificate-from-certificate-authority) nebo [pomocí Azure CLI k vytvoření instančního objektu pro přístup k prostředkům](resource-group-authenticate-service-principal-cli.md) .

## <a name="get-tenant-id-from-subscription-id"></a>Získání ID tenanta z ID předplatného
Chcete-li požádat o token, který slouží k volání Resource Manageru, vaše aplikace potřebuje znát ID tenanta Azure AD tenanta, který je hostitelem předplatné Azure. Pravděpodobně uživatelé budou vědět, ID předplatného, ale nemusí ví jejich tenant ID pro Azure Active Directory. Pokud chcete získat ID tenanta daného uživatele, požádejte uživatele, pro ID předplatného. Zadejte toto předplatné ID při odesílání žádosti o předplatném:

    https://management.azure.com/subscriptions/{subscription-id}?api-version=2015-01-01

Požadavek selže, protože uživatel není přihlášen ještě, ale můžete získat ID tenanta z odpovědi. V tuto výjimku načíst ID tenanta z hodnota hlavičky odpovědi pro **WWW-Authenticate**. Se zobrazí v této implementaci [GetDirectoryForSubscription](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureResourceManagerUtil.cs#L20) metody.

## <a name="get-user--app-access-token"></a>Získání uživatele a token přístupu aplikace
Vaše aplikace přesměruje uživatele do služby Azure AD pomocí OAuth 2.0 povolit požadavek – k ověření přihlašovacích údajů uživatele a získat zpět autorizační kód. Vaše aplikace používá autorizační kód k získání přístupového tokenu pro Resource Manager. [ConnectSubscription](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/Controllers/HomeController.cs#L42) metoda vytvoří žádost o autorizaci.

Tento článek popisuje požadavky rozhraní REST API pro ověření uživatele. Pomocné rutiny knihovny používat taky k provedení ověřování ve vašem kódu. Další informace o těchto knihoven, naleznete v tématu [knihovny Azure Active Directory Authentication](../active-directory/active-directory-authentication-libraries.md). Pokyny k integraci správy identit do aplikace najdete v tématu [Příručka pro vývojáře Azure Active Directory](../active-directory/develop/v1-overview.md).

### <a name="auth-request-oauth-20"></a>Žádost o ověření (OAuth 2.0)
Vydejte Open ID Connect a OAuth 2.0 autorizaci požadavků na koncový bod ověření Azure AD:

    https://login.microsoftonline.com/{tenant-id}/OAuth2/Authorize

Parametry řetězce dotazu, které jsou k dispozici pro tento požadavek jsou popsány v [požádat o autorizační kód](../active-directory/develop/v1-protocols-oauth-code.md#request-an-authorization-code) článku.

Následující příklad ukazuje, jak požádat o autorizaci OAuth 2.0:

    https://login.microsoftonline.com/{tenant-id}/OAuth2/Authorize?client_id=a0448380-c346-4f9f-b897-c18733de9394&response_mode=query&response_type=code&redirect_uri=http%3a%2f%2fwww.vipswapper.com%2fcloudsense%2fAccount%2fSignIn&resource=https%3a%2f%2fgraph.windows.net%2f&domain_hint=live.com

Azure AD ověřuje uživatele a v případě potřeby vyzve uživatele a udělit oprávnění k aplikaci. Adresa URL odpovědi aplikace vrátí autorizační kód. V závislosti na požadované response_mode, Azure AD buď odešle zpět data v řetězci dotazu nebo jako příspěvek data.

    code=AAABAAAAiL****FDMZBUwZ8eCAA&session_state=2d16bbce-d5d1-443f-acdf-75f6b0ce8850

### <a name="auth-request-open-id-connect"></a>Žádost o ověření (Open ID Connect)
Pokud není pouze chcete přístup k Azure Resource Manageru jménem uživatele, ale také umožnit uživatelům přihlášení k aplikaci pomocí svého účtu Azure AD, vydejte Open ID připojení autorizaci požadavků. Vaše aplikace s Open ID Connect, také obdrží tokentu id_token z Azure AD, která vaše aplikace může používat k přihlášení uživatele.

Parametry řetězce dotazu, které jsou k dispozici pro tento požadavek jsou popsány v [odeslat žádost o přihlášení](../active-directory/develop/v1-protocols-openid-connect-code.md#send-the-sign-in-request) článku.

Příklad Open ID Connect žádosti je:

     https://login.microsoftonline.com/{tenant-id}/OAuth2/Authorize?client_id=a0448380-c346-4f9f-b897-c18733de9394&response_mode=form_post&response_type=code+id_token&redirect_uri=http%3a%2f%2fwww.vipswapper.com%2fcloudsense%2fAccount%2fSignIn&resource=https%3a%2f%2fgraph.windows.net%2f&scope=openid+profile&nonce=63567Dc4MDAw&domain_hint=live.com&state=M_12tMyKaM8

Azure AD ověřuje uživatele a v případě potřeby vyzve uživatele a udělit oprávnění k aplikaci. Adresa URL odpovědi aplikace vrátí autorizační kód. V závislosti na požadované response_mode, Azure AD buď odešle zpět data v řetězci dotazu nebo jako příspěvek data.

Příklad Open ID Connect odpověď je:

    code=AAABAAAAiL*****I4rDWd7zXsH6WUjlkIEQxIAA&id_token=eyJ0eXAiOiJKV1Q*****T3GrzzSFxg&state=M_12tMyKaM8&session_state=2d16bbce-d5d1-443f-acdf-75f6b0ce8850

### <a name="token-request-oauth20-code-grant-flow"></a>Žádosti o token (OAuth2.0 kód tok udělování)
Teď, když aplikace obdrží autorizační kód z Azure AD, je čas získání tokenu přístupu pro Azure Resource Manageru.  Účtování OAuth2.0 udělení tokenu žádost o kódu koncovému bodu tokenu Azure AD:

    https://login.microsoftonline.com/{tenant-id}/OAuth2/Token

Parametry řetězce dotazu, které jsou k dispozici pro tento požadavek jsou popsány v [použít autorizační kód](../active-directory/develop/v1-protocols-oauth-code.md#use-the-authorization-code-to-request-an-access-token) článku.

Následující příklad ukazuje požadavek pro token grant kódu pomocí hesla k přihlášení:

    POST https://login.microsoftonline.com/7fe877e6-a150-4992-bbfe-f517e304dfa0/oauth2/token HTTP/1.1

    Content-Type: application/x-www-form-urlencoded
    Content-Length: 1012

    grant_type=authorization_code&code=AAABAAAAiL9Kn2Z*****L1nVMH3Z5ESiAA&redirect_uri=http%3A%2F%2Flocalhost%3A62080%2FAccount%2FSignIn&client_id=a0448380-c346-4f9f-b897-c18733de9394&client_secret=olna84E8*****goScOg%3D

Při práci s přihlašovacími údaji certifikát, vytvořte JSON Web Token (JWT) a znak (RSA SHA256) pomocí soukromého klíče přihlašovacích údajů pro certifikát vaší aplikace. Vytváření tento token se zobrazí v [tok přihlašovacích údajů klienta](../active-directory/develop/v1-oauth2-client-creds-grant-flow.md#second-case-access-token-request-with a-certificate).  Odkaz, najdete v článku [Active Directory ověřování Library (.NET) kód](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/blob/dev/src/ADAL.PCL.Desktop/CryptographyHelper.cs) k podepisování tokenů JWT kontrolní výraz klienta.

Zobrazit [Open ID Connect specifikace](https://openid.net/specs/openid-connect-core-1_0.html#ClientAuthentication) podrobnosti o ověření klienta.

Následující příklad ukazuje požadavek pro token grant kódu s certifikát přihlašovacích údajů:

    POST https://login.microsoftonline.com/7fe877e6-a150-4992-bbfe-f517e304dfa0/oauth2/token HTTP/1.1

    Content-Type: application/x-www-form-urlencoded
    Content-Length: 1012

    grant_type=authorization_code&code=AAABAAAAiL9Kn2Z*****L1nVMH3Z5ESiAA&redirect_uri=http%3A%2F%2Flocalhost%3A62080%2FAccount%2FSignIn&client_id=a0448380-c346-4f9f-b897-c18733de9394&client_assertion_type=urn%3Aietf%3Aparams%3Aoauth%3Aclient-assertion-type%3Ajwt-bearer&client_assertion=eyJhbG*****Y9cYo8nEjMyA

Příklad odpovědi pro token grant kódu:

    HTTP/1.1 200 OK

    {"token_type":"Bearer","expires_in":"3599","expires_on":"1432039858","not_before":"1432035958","resource":"https://management.core.windows.net/","access_token":"eyJ0eXAiOiJKV1Q****M7Cw6JWtfY2lGc5A","refresh_token":"AAABAAAAiL9Kn2Z****55j-sjnyYgAA","scope":"user_impersonation","id_token":"eyJ0eXAiOiJKV*****-drP1J3P-HnHi9Rr46kGZnukEBH4dsg"}

#### <a name="handle-code-grant-token-response"></a>Zpracování odpovědi tokenu grant kódu
Úspěšné odpovědi tokenu obsahuje (uživatele a aplikace) přístupový token pro Azure Resource Manageru. Aplikace použije tento přístupový token pro přístup k prostředku správce jménem uživatele. Životnost přístupové tokeny vydané službou Azure AD je jedna hodina. Není pravděpodobné, že vaše webová aplikace je potřeba obnovit (uživatele a aplikace) přístupový token. Pokud je potřeba obnovit přístupový token, použijte token obnovení, které aplikace přijímá v odpovědi tokenu. Účtování OAuth 2.0 Token požadavek na koncový bod Azure AD tokenu:

    https://login.microsoftonline.com/{tenant-id}/OAuth2/Token

Parametry se mají použít s žádostí o aktualizace jsou popsané v [aktualizaci tokenu přístupu](../active-directory/develop/v1-protocols-oauth-code.md#refreshing-the-access-tokens).

Následující příklad ukazuje, jak použít aktualizaci tokenu:

    POST https://login.microsoftonline.com/7fe877e6-a150-4992-bbfe-f517e304dfa0/oauth2/token HTTP/1.1

    Content-Type: application/x-www-form-urlencoded
    Content-Length: 1012

    grant_type=refresh_token&refresh_token=AAABAAAAiL9Kn2Z****55j-sjnyYgAA&client_id=a0448380-c346-4f9f-b897-c18733de9394&client_secret=olna84E8*****goScOg%3D

Ačkoli obnovovací tokeny lze použít k získání nových přístupových tokenů pro Azure Resource Manager, nejsou vhodné pro offline přístup k vaší aplikací. Životnost tokenů aktualizace je omezený a obnovovací tokeny, které jsou vázány na uživatele. Pokud uživatel opustí organizaci, aplikace pomocí obnovovacího tokenu ztratí přístup. Tento přístup není vhodné pro aplikace, které jsou používány týmů ke správě svých prostředků Azure.

## <a name="check-if-user-can-assign-access-to-subscription"></a>Zaškrtněte, pokud uživatele lze přiřadit přístup k předplatnému
Vaše aplikace nyní obsahuje token pro přístup k Azure Resource Manageru jménem uživatele. Dalším krokem je připojení aplikace k předplatnému. Po připojení se vaše aplikace může spravovat předplatná i v případě, že uživatel není k dispozici (dlouhodobé offline přístup).

Pro každé předplatné pro připojení, zavolejte [Resource Manageru vypsat seznam oprávnění](https://docs.microsoft.com/rest/api/authorization/permissions) rozhraní API slouží k určení, zda uživatel má práva pro správu přístupu pro předplatné.

[UserCanManagerAccessForSubscription](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureResourceManagerUtil.cs#L44) metoda ukázkovou aplikaci ASP.NET MVC implementuje toto volání.

Následující příklad ukazuje, jak požádat o oprávnění uživatele v rámci předplatného. 83cfe939-2402-4581-b761-4f59b0a041e4 je ID předplatného.

    GET https://management.azure.com/subscriptions/83cfe939-2402-4581-b761-4f59b0a041e4/providers/microsoft.authorization/permissions?api-version=2015-07-01 HTTP/1.1

    Authorization: Bearer eyJ0eXAiOiJKV1QiLC***lwO1mM7Cw6JWtfY2lGc5A

Příklad odpovědi k získání oprávnění uživatele u předplatného je:

    HTTP/1.1 200 OK

    {"value":[{"actions":["*"],"notActions":["Microsoft.Authorization/*/Write","Microsoft.Authorization/*/Delete"]},{"actions":["*/read"],"notActions":[]}]}

Oprávnění rozhraní API vrátí více oprávnění. Každé oprávnění se skládá z povolených akcí (**akce**) a nepovolené akce (**notactions**). Pokud je k dispozici v povolených akcí všechna oprávnění, akci a není k dispozici v nepovolené akce toto oprávnění, uživatel může k provedení této akce. **Microsoft.Authorization/RoleAssignments/Write** akce, která uděluje přístup management rights. Aplikace musí analyzovat oprávnění výsledek hledání regulárního výrazu shody na tento řetězec akce v **akce** a **notactions** každého oprávnění.

## <a name="get-app-only-access-token"></a>Získání tokenu přístupu jen pro aplikace
Teď víte, pokud uživatel můžete přiřadit přístup k předplatnému Azure. Jsou tyto další kroky:

1. Přiřadíte příslušné role RBAC identity aplikace v rámci předplatného.
2. Ověření přiřazení přístupu pomocí dotazu pro oprávnění aplikace v rámci předplatného nebo přechodem k Resource Manageru pomocí tokenu pouze pro aplikace.
3. Zaznamenejte připojení do struktury vaší aplikace "propojené předplatné" data - uchování ID předplatného.

Pojďme se podívat podrobněji v prvním kroku. Pokud chcete přiřadit příslušné role RBAC identitu aplikace, je třeba určit:

* ID objektu vaší aplikace identity v Azure Active Directory uživatele
* Identifikátor role RBAC, která vaše aplikace vyžaduje na předplatné

Když se aplikace ověřuje uživatele z Azure AD, vytvoří instanční objekt pro vaši aplikaci v této službě Azure AD. Azure umožňuje role RBAC pro přiřazení instanční objekty udělit přímý přístup k příslušné aplikace pro prostředky Azure. Tato akce je přesně to chcete provést. Dotaz rozhraní Azure AD Graph API k určení identifikátor vaší aplikace v přihlášený uživatel instančního objektu vaší služby Azure AD.

Pouze máte přístupový token pro Azure Resource Manager – budete potřebovat nový přístupový token pro volání rozhraní Azure AD Graph API. Každá aplikace ve službě Azure AD má oprávnění k dotazování vlastní instanční objekt, takže stačí jen pro aplikace přístupový token.

<a id="app-azure-ad-graph" />

### <a name="get-app-only-access-token-for-azure-ad-graph-api"></a>Získání tokenu přístupu jen pro aplikace pro Azure AD Graph API
K ověření vaší aplikace a získání tokenu Azure AD Graph API, vydat požadavek tokenu toku OAuth 2.0 udělení přihlašovacích údajů klienta na koncový bod tokenu Azure AD (**https://login.microsoftonline.com/{directory_domain_name}/OAuth2/Token**).

[GetObjectIdOfServicePrincipalInOrganization](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureADGraphAPIUtil.cs) metoda ukázkovou aplikaci ASP.net MVC získá přístup jen pro aplikace tokenu pro rozhraní Graph API pomocí Active Directory Authentication Library pro .NET.

Parametry řetězce dotazu, které jsou k dispozici pro tento požadavek jsou popsány v [žádosti přístupový Token](../active-directory/develop/v1-oauth2-client-creds-grant-flow.md#request-an-access-token) článku.

Příklad žádosti pro token udělení přihlašovacích údajů klienta:

    POST https://login.microsoftonline.com/62e173e9-301e-423e-bcd4-29121ec1aa24/oauth2/token HTTP/1.1
    Content-Type: application/x-www-form-urlencoded
    Content-Length: 187</pre>
    <pre>grant_type=client_credentials&client_id=a0448380-c346-4f9f-b897-c18733de9394&resource=https%3A%2F%2Fgraph.windows.net%2F &client_secret=olna8C*****Og%3D

Příklad odpovědi pro token udělení přihlašovacích údajů klienta:

    HTTP/1.1 200 OK

    {"token_type":"Bearer","expires_in":"3599","expires_on":"1432039862","not_before":"1432035962","resource":"https://graph.windows.net/","access_token":"eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWmNBVGZNNXBPWWlKSE1iYTlnb0VLWSIsImtpZCI6Ik1uQ19WWmNBVGZNNXBPWWlKSE1iYTlnb0VLWSJ9.eyJhdWQiOiJodHRwczovL2dyYXBoLndpbmRv****G5gUTV-kKorR-pg"}

### <a name="get-objectid-of-application-service-principal-in-user-azure-ad"></a>Získejte ObjectId instančního objektu aplikace v uživatele Azure AD
Teď pomocí jen pro aplikace přístupového tokenu k dotazu [instanční objekty Azure AD Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#serviceprincipal-entity) rozhraní API k určení ID objektu aplikace instančního objektu v adresáři.

[GetObjectIdOfServicePrincipalInOrganization](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureADGraphAPIUtil.cs#) metoda ukázkovou aplikaci ASP.net MVC implementuje toto volání.

Následující příklad ukazuje, jak požádat o instančního objektu aplikace. a0448380-c346-4f9f-b897-c18733de9394 je ID klienta aplikace.

    GET https://graph.windows.net/62e173e9-301e-423e-bcd4-29121ec1aa24/servicePrincipals?api-version=1.5&$filter=appId%20eq%20'a0448380-c346-4f9f-b897-c18733de9394' HTTP/1.1

    Authorization: Bearer eyJ0eXAiOiJK*****-kKorR-pg

Následující příklad ukazuje odpověď na žádost o služby aplikace instančního objektu

    HTTP/1.1 200 OK

    {"odata.metadata":"https://graph.windows.net/62e173e9-301e-423e-bcd4-29121ec1aa24/$metadata#directoryObjects/Microsoft.DirectoryServices.ServicePrincipal","value":[{"odata.type":"Microsoft.DirectoryServices.ServicePrincipal","objectType":"ServicePrincipal","objectId":"9b5018d4-6951-42ed-8a92-f11ec283ccec","deletionTimestamp":null,"accountEnabled":true,"appDisplayName":"CloudSense","appId":"a0448380-c346-4f9f-b897-c18733de9394","appOwnerTenantId":"62e173e9-301e-423e-bcd4-29121ec1aa24","appRoleAssignmentRequired":false,"appRoles":[],"displayName":"CloudSense","errorUrl":null,"homepage":"http://www.vipswapper.com/cloudsense","keyCredentials":[],"logoutUrl":null,"oauth2Permissions":[{"adminConsentDescription":"Allow the application to access CloudSense on behalf of the signed-in user.","adminConsentDisplayName":"Access CloudSense","id":"b7b7338e-683a-4796-b95e-60c10380de1c","isEnabled":true,"type":"User","userConsentDescription":"Allow the application to access CloudSense on your behalf.","userConsentDisplayName":"Access CloudSense","value":"user_impersonation"}],"passwordCredentials":[],"preferredTokenSigningKeyThumbprint":null,"publisherName":"vipswapper"quot;,"replyUrls":["http://www.vipswapper.com/cloudsense","http://www.vipswapper.com","http://vipswapper.com","http://vipswapper.azurewebsites.net","http://localhost:62080"],"samlMetadataUrl":null,"servicePrincipalNames":["http://www.vipswapper.com/cloudsense","a0448380-c346-4f9f-b897-c18733de9394"],"tags":["WindowsAzureActiveDirectoryIntegratedApp"]}]}

### <a name="get-azure-rbac-role-identifier"></a>Získat identifikátor role Azure RBAC
Přiřadit příslušné role RBAC pro váš objekt služby, je třeba určit identifikátor role Azure RBAC.

Tu správnou roli RBAC pro vaši aplikaci:

* Pokud vaše aplikace pouze sleduje předplatného, bez jakýchkoli změn, vyžaduje pouze oprávnění Čtenář předplatného. Přiřazení **čtečky** role.
* Pokud vaše aplikace spravuje Azure předplatného, vytvoření nebo úprava/odstranění entity, vyžaduje nejméně jedno oprávnění přispěvatele.
  * Ke správě konkrétní typ prostředku, přiřaďte role Přispěvatel specifické podle prostředků (Přispěvatel virtuálních počítačů, Přispěvatel sítě, Přispěvatel účtů úložiště, atd.)
  * Chcete-li spravovat libovolný typ prostředku, přiřaďte **Přispěvatel** role.

Přiřazení role aplikace je viditelné pro uživatele, takže vyberte vyžaduje alespoň oprávnění.

Volání [definice role správce prostředků rozhraní API](https://docs.microsoft.com/rest/api/authorization/roledefinitions) na seznamu všechny role Azure RBAC a hledání a iterovat výsledek vyhledáte definice požadované role podle názvu.

[GetRoleId](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureResourceManagerUtil.cs#L246) metoda ukázkovou aplikaci ASP.net MVC implementuje toto volání.

Následující příklad požadavku ukazuje, jak získat identifikátor role Azure RBAC. 09cbd307-aa71-4aca-b346-5f253e6e3ebb je ID předplatného.

    GET https://management.azure.com/subscriptions/09cbd307-aa71-4aca-b346-5f253e6e3ebb/providers/Microsoft.Authorization/roleDefinitions?api-version=2015-07-01 HTTP/1.1

    Authorization: Bearer eyJ0eXAiOiJKV*****fY2lGc5

Odpověď je v následujícím formátu:

    HTTP/1.1 200 OK

    {"value":[{"properties":{"roleName":"API Management Service Contributor","type":"BuiltInRole","description":"Lets you manage API Management services, but not access to them.","scope":"/","permissions":[{"actions":["Microsoft.ApiManagement/Services/*","Microsoft.Authorization/*/read","Microsoft.Resources/subscriptions/resources/read","Microsoft.Resources/subscriptions/resourceGroups/read","Microsoft.Resources/subscriptions/resourceGroups/resources/read","Microsoft.Resources/subscriptions/resourceGroups/deployments/*","Microsoft.Insights/alertRules/*","Microsoft.Support/*"],"notActions":[]}]},"id":"/subscriptions/09cbd307-aa71-4aca-b346-5f253e6e3ebb/providers/Microsoft.Authorization/roleDefinitions/312a565d-c81f-4fd8-895a-4e21e48d571c","type":"Microsoft.Authorization/roleDefinitions","name":"312a565d-c81f-4fd8-895a-4e21e48d571c"},{"properties":{"roleName":"Application Insights Component Contributor","type":"BuiltInRole","description":"Lets you manage Application Insights components, but not access to them.","scope":"/","permissions":[{"actions":["Microsoft.Insights/components/*","Microsoft.Insights/webtests/*","Microsoft.Authorization/*/read","Microsoft.Resources/subscriptions/resources/read","Microsoft.Resources/subscriptions/resourceGroups/read","Microsoft.Resources/subscriptions/resourceGroups/resources/read","Microsoft.Resources/subscriptions/resourceGroups/deployments/*","Microsoft.Insights/alertRules/*","Microsoft.Support/*"],"notActions":[]}]},"id":"/subscriptions/09cbd307-aa71-4aca-b346-5f253e6e3ebb/providers/Microsoft.Authorization/roleDefinitions/ae349356-3a1b-4a5e-921d-050484c6347e","type":"Microsoft.Authorization/roleDefinitions","name":"ae349356-3a1b-4a5e-921d-050484c6347e"}]}

Není potřeba průběžně volat toto rozhraní API. Jakmile potvrdíte dobře známý identifikátor GUID definice role, můžete vytvořit ID definice role jako:

    /subscriptions/{subscription_id}/providers/Microsoft.Authorization/roleDefinitions/{well-known-role-guid}

Tady jsou identifikátory běžně používaných předdefinované role:

| Role | GUID |
| --- | --- |
| Čtenář |acdd72a7-3385-48ef-bd42-f606fba81ae7 |
| Přispěvatel |b24988ac-6180-42a0-ab88-20f7382dd24c |
| Přispěvatel virtuálních počítačů |d73bb868-a0df-4d4d-bd69-98a00b01fccb |
| Přispěvatel virtuální sítě |b34d265f-36f7-4a0d-a4d4-e158ca92e90f |
| Přispěvatel účtů úložiště |86e8f5dc-a6e9-4c67-9d15-de283e8eac25 |
| Přispěvatel webů |de139f84-1756-47ae-9be6-808fbbe84772 |
| Přispěvatel webových plánů |2cc479cb-7b4d-49a8-b449-8c00fd0f0a4b |
| Přispěvatel SQL Serveru |6d8ee4ec-f05a-4a1d-8b00-a9b17e38b437 |
| Přispěvatel databází SQL |9b7fa17d-e63e-47b0-bb0a-15c516ac86ec |

### <a name="assign-rbac-role-to-application"></a>Přiřaďte roli RBAC do aplikace
Budete mít všechno, co je potřeba přiřadit příslušné role RBAC pomocí instančního objektu služby [Resource Manageru vytvořit přiřazení role](https://docs.microsoft.com/rest/api/authorization/roleassignments) rozhraní API.

[GrantRoleToServicePrincipalOnSubscription](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureResourceManagerUtil.cs#L170) metoda ukázkovou aplikaci ASP.net MVC implementuje toto volání.

Příklad žádosti přiřazení RBAC role pro aplikace:

    PUT https://management.azure.com/subscriptions/09cbd307-aa71-4aca-b346-5f253e6e3ebb/providers/microsoft.authorization/roleassignments/4f87261d-2816-465d-8311-70a27558df4c?api-version=2015-07-01 HTTP/1.1

    Authorization: Bearer eyJ0eXAiOiJKV1QiL*****FlwO1mM7Cw6JWtfY2lGc5
    Content-Type: application/json
    Content-Length: 230

    {"properties": {"roleDefinitionId":"/subscriptions/09cbd307-aa71-4aca-b346-5f253e6e3ebb/providers/Microsoft.Authorization/roleDefinitions/acdd72a7-3385-48ef-bd42-f606fba81ae7","principalId":"c3097b31-7309-4c59-b4e3-770f8406bad2"}}

V požadavku se používají následující hodnoty:

| Guid | Popis |
| --- | --- |
| 09cbd307-aa71-4aca-b346-5f253e6e3ebb |ID předplatného |
| c3097b31-7309-4c59-b4e3-770f8406bad2 |ID objektu instanční objekt služby aplikace |
| acdd72a7-3385-48ef-bd42-f606fba81ae7 |ID role čtenáře |
| 4f87261d-2816-465d-8311-70a27558df4c |nový identifikátor guid vytvořit nové přiřazení role |

Odpověď je v následujícím formátu:

    HTTP/1.1 201 Created

    {"properties":{"roleDefinitionId":"/subscriptions/09cbd307-aa71-4aca-b346-5f253e6e3ebb/providers/Microsoft.Authorization/roleDefinitions/acdd72a7-3385-48ef-bd42-f606fba81ae7","principalId":"c3097b31-7309-4c59-b4e3-770f8406bad2","scope":"/subscriptions/09cbd307-aa71-4aca-b346-5f253e6e3ebb"},"id":"/subscriptions/09cbd307-aa71-4aca-b346-5f253e6e3ebb/providers/Microsoft.Authorization/roleAssignments/4f87261d-2816-465d-8311-70a27558df4c","type":"Microsoft.Authorization/roleAssignments","name":"4f87261d-2816-465d-8311-70a27558df4c"}

### <a name="get-app-only-access-token-for-azure-resource-manager"></a>Získat jen pro aplikace přístupový token pro Azure Resource Manageru
K ověření aplikace má požadovaný přístup v rámci předplatného, provedení úkolu Vytvoření testu v rámci předplatného pomocí tokenu pouze pro aplikace.

Pokud chcete získat token přístupu jen pro aplikace, postupujte podle pokynů v části [získání tokenu přístupu jen pro aplikace pro Azure AD Graph API](#app-azure-ad-graph), zadat jinou hodnotu pro parametr prostředku:

    https://management.core.windows.net/

[ServicePrincipalHasReadAccessToSubscription](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureResourceManagerUtil.cs#L110) metoda ukázkovou aplikaci ASP.NET MVC získá přístup jen pro aplikace token pro Azure Resource Manageru pomocí Active Directory Authentication Library pro .net.

#### <a name="get-applications-permissions-on-subscription"></a>Zajistěte si oprávnění aplikace na předplatné
Pokud chcete zkontrolovat, že vaše aplikace má požadovaný přístup na předplatné Azure, může také volat [oprávnění správce prostředků](https://docs.microsoft.com/rest/api/authorization/permissions) rozhraní API. Tento přístup je podobný jak určit, zda má uživatel práva pro správu přístupu pro předplatné. Tentokrát ale volání oprávnění rozhraní API s tokenem přístupu jen pro aplikace, který jste získali v předchozím kroku.

[ServicePrincipalHasReadAccessToSubscription](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureResourceManagerUtil.cs#L110) metoda ukázkovou aplikaci ASP.NET MVC implementuje toto volání.

## <a name="manage-connected-subscriptions"></a>Spravovat připojená předplatná
Pokud příslušné role RBAC je přiřazena k objektu v předplatném služby vaší aplikace, aplikace můžete zachovat monitorování/správa pomocí tokenů přístupu jen pro aplikace pro Azure Resource Manageru.

Pokud vlastník předplatného Odebere přiřazení role aplikace pomocí portálu nebo nástroje příkazového řádku, aplikace je už nebude mít přístup k tomuto předplatnému. V takovém případě by měl upozornit uživatele, které připojení se předplatné bylo porušeno z mimo aplikaci a můžete svým uživatelům umožnit možnost "Opravit" připojení. "Opravy" by znovu vytvořit přiřazení role, který byl odstraněn v režimu offline.

Stejně jako jste povolili uživateli připojit předplatných k vaší aplikaci, musíte také povolit uživatele příliš odpojit předplatných. Z aplikace access management hlediska odpojte znamená odebrání přiřazení role, která má instančního objektu aplikace v rámci předplatného. Jakýkoli stav aplikace pro předplatné může být volitelně příliš odstraněna.
Jenom uživatelé s oprávněním řízení přístupu v rámci předplatného budou moct odpojit předplatné.

[RevokeRoleFromServicePrincipalOnSubscription metoda](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureResourceManagerUtil.cs#L200) ukázkovou aplikaci rozhraní ASP.net MVC implementuje toto volání.

To je vše – uživatelé teď můžete snadno připojit a spravovat svá předplatná Azure s vaší aplikací.
