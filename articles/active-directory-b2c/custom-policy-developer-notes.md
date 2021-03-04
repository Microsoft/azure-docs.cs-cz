---
title: Poznámky pro vývojáře pro vlastní zásady
titleSuffix: Azure AD B2C
description: Poznámky pro vývojáře při konfiguraci a údržbě Azure AD B2C s vlastními zásadami.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 05/19/2020
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 76dcb9aec935dd4ea4f57a1362953d9741d8eaf0
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/04/2021
ms.locfileid: "102095795"
---
# <a name="developer-notes-for-custom-policies-in-azure-active-directory-b2c"></a>Poznámky pro vývojáře pro vlastní zásady v Azure Active Directory B2C

Konfigurace vlastních zásad v Azure Active Directory B2C je teď všeobecně dostupná. Tato metoda konfigurace je cílem pokročilým vývojářům identity vytvářet složitá řešení identit. Vlastní zásady zpřístupňují výkon rozhraní identity Experience Framework v Azure AD B2Cch klientech.
Pokročilí vývojáři identity, kteří používají vlastní zásady, by měli mít na úmysl investovat určitou dobu při dokončování procházení a čtení referenčních dokumentů.

I když je většina dostupných vlastních možností zásad všeobecně dostupná, existují základní možnosti, jako jsou například typy technických profilů a rozhraní API definice obsahu, které jsou v různých fázích životního cyklu softwaru. Mnoho dalších přicházejí. Následující tabulka určuje úroveň dostupnosti na podrobnější úrovni.

## <a name="features-that-are-generally-available"></a>Funkce, které jsou všeobecně dostupné

- Vytváření a nahrávání vlastních cest uživatelů pro ověřování pomocí vlastních zásad.
    - Popište cesty uživatelů krok za krokem jako výměny mezi poskytovateli deklarací identity.
    - Definujte podmíněné větvení v cestě uživatele.
- Spolupracuje se službami s podporou REST API ve vašich vlastních ověřovacích jízdách uživatele.
- Federovat se zprostředkovateli identity, kteří jsou kompatibilní s protokolem OpenIDConnect.
- Federovat se zprostředkovateli identity, kteří dodržují protokol SAML 2,0.

## <a name="responsibilities-of-custom-policy-feature-set-developers"></a>Zodpovědnosti funkce vlastní zásady – nastavení vývojářů

Ruční konfigurace zásad uděluje přístup na nižší úrovni k základní platformě Azure AD B2C a má za následek vytvoření jedinečného, důvěryhodného rozhraní. Mnoho možných permutací vlastních zprostředkovatelů identity, vztahů důvěryhodnosti, integrace s externími službami a krok za krokem pracovní postupy vyžaduje přístup k návrhu a konfiguraci.

Vývojáři, kteří používají sadu funkcí vlastní zásady, by měli dodržovat následující pokyny:

- Seznamte se s jazykem konfigurace vlastních zásad a správou klíčů a tajných kódů. Další informace najdete v tématu [TrustFrameworkPolicy](trustframeworkpolicy.md).
- Převezměte vlastnictví scénářů a vlastních integrací. Zdokumentujte svou práci a informujte svou organizaci s živým webem.
- Proveďte testování scénáře metod.
- Řiďte se doporučenými postupy pro vývoj softwaru a přípravu. Doporučuje se minimálně jedno vývojové a testovací prostředí.
- Informujte se o novém vývoji od zprostředkovatelů identity a služeb, které do nástroje integrujete. Můžete například sledovat změny v tajných klíčích a naplánovaných a neplánovaných změnách služby.
- Nastavte aktivní monitorování a sledujte rychlost odezvy v produkčním prostředí. Další informace o integraci s Application Insights najdete v tématu [Azure Active Directory B2C: shromažďování protokolů](analytics-with-application-insights.md).
- Udržujte kontaktní e-mailové adresy aktuální v rámci předplatného Azure a zůstaňte reagovat na e-maily týmu živého webu společnosti Microsoft.
- Využijte včasnou akci, kterou by to měl udělat tým Microsoftu pro živý Web.

## <a name="terms-for-features-in-public-preview"></a>Výrazy pro funkce ve verzi Public Preview

- Doporučujeme používat funkce Public Preview jenom pro účely testování.
- Smlouvy o úrovni služeb (SLA) se nevztahují na funkce verze Public Preview.
- Žádosti o podporu pro veřejné funkce ve verzi Preview je možné dodávat prostřednictvím běžných kanálů podpory.

## <a name="features-by-stage-and-known-issues"></a>Funkce podle fáze a známé problémy

Možnosti vlastních zásad jsou v rámci Stálého vývoje. V následující tabulce je index funkcí a dostupnosti součástí.


### <a name="protocols-and-authorization-flows"></a>Protokoly a autorizační toky

| Funkce | Vývoj | Preview | GA | Poznámky |
|-------- | :-----------: | :-------: | :--: | ----- |
| [Autorizační kód OAuth2](authorization-code-flow.md) |  |  | × |  |
| OAuth2 autorizační kód s PKCE |  |  | × | [Veřejné klienty a jednostránkové aplikace](authorization-code-flow.md)  |
| [OAuth2 implicitní tok](implicit-flow-single-page-application.md) |  |  | × |  |
| [Přihlašovací údaje pro heslo vlastníka prostředku OAuth2](ropc-custom.md) |  | × |  |  |
| [OIDC připojit](openid-connect.md) |  |  | × |  |
| [TYPU Saml2](saml-service-provider.md)  |  |  |×  | Vystavení a přesměrování vazeb. |
| OAuth1 |  |  |  | Nepodporováno |
| WSFED | × |  |  |  |

### <a name="identify-providers-federation"></a>Identifikace federačních zprostředkovatelů 

| Funkce | Vývoj | Preview | GA | Poznámky |
|-------- | :-----------: | :-------: | :--: | ----- |
| [OpenID Connect](openid-connect-technical-profile.md) |  |  | × | Například Google +.  |
| [OAuth2](oauth2-technical-profile.md) |  |  | × | Například Facebook.  |
| [OAuth1](oauth1-technical-profile.md) |  | × |  | Například Twitter. |
| [TYPU Saml2](identity-provider-generic-saml.md) |  |   | × | Například Salesforce, ADFS. |
| WSFED| × |  |  |  |


### <a name="rest-api-integration"></a>Integrace REST API

| Funkce | Vývoj | Preview | GA | Poznámky |
|-------- | :-----------: | :-------: | :--: | ----- |
| [REST API se základním ověřováním](secure-rest-api.md#http-basic-authentication) |  |  | × |  |
| [REST API s ověřováním klientským certifikátem](secure-rest-api.md#https-client-certificate-authentication) |  |  | × |  |
| [REST API s ověřováním Bearer OAuth2](secure-rest-api.md#oauth2-bearer-authentication) |  | × |  |  |

### <a name="component-support"></a>Podpora komponent

| Funkce | Vývoj | Preview | GA | Poznámky |
| ------- | :-----------: | :-------: | :--: | ----- |
| [Ověřování pro telefonní faktor](phone-factor-technical-profile.md) |  |  | × |  |
| [Ověřování Azure AD MFA](multi-factor-auth-technical-profile.md) |  | × |  |  |
| [Jednorázové heslo](one-time-password-technical-profile.md) |  | × |  |  |
| [Azure Active Directory](active-directory-technical-profile.md) jako místní adresář |  |  | × |  |
| Podsystém e-mailů Azure pro ověření e-mailu |  |  | × |  |
| [Poskytovatelé e-mailových služeb třetích stran](custom-email-mailjet.md) |  |×  |  |  |
| [Podpora více jazyků](localization.md)|  |  | × |  |
| [Ověřování predikátů](predicates.md) |  |  | × | Například složitost hesla. |
| [Ovládací prvky zobrazení](display-controls.md) |  |×  |  |  |


### <a name="app-ief-integration"></a>Integrace aplikací – IEF

| Funkce | Vývoj | Preview | GA | Poznámky |
| ------- | :-----------: | :-------: | :--: | ----- |
| Parametr řetězce dotazu `domain_hint` |  |  | × | K dispozici jako deklarace identity, se dají předat do IDP. |
| Parametr řetězce dotazu `login_hint` |  |  | × | K dispozici jako deklarace identity, se dají předat do IDP. |
| Vložení formátu JSON do cesty uživatele prostřednictvím `client_assertion` | × |  |  | Bude zastaralá. |
| Vložení formátu JSON do cesty uživatele jako `id_token_hint` |  | × |  | Přejít k předávanému přístupu a předávat JSON. |
| [Předání tokenu identity provider do aplikace](idp-pass-through-user-flow.md) |  | × |  | Například z Facebooku do aplikace. |


### <a name="session-management"></a>Správa relací

| Funkce | Vývoj | Preview | GA | Poznámky |
| ------- | :-----------: | :-------: | :--: | ----- |
| [Výchozí zprostředkovatel relace jednotného přihlašování](custom-policy-reference-sso.md#defaultssosessionprovider) |  |  | × |  |
| [Externí zprostředkovatel přihlašovací relace](custom-policy-reference-sso.md#externalloginssosessionprovider) |  |  | × |  |
| [Zprostředkovatel relací SAML SSO](custom-policy-reference-sso.md#samlssosessionprovider) |  |  | × |  |
| [OAuthSSOSessionProvider](custom-policy-reference-sso.md#oauthssosessionprovider)  |  | × |  |  |
| [Jednotné odhlašování](session-behavior.md#sign-out)  |  | × |  |  |

### <a name="security"></a>Zabezpečení

| Funkce | Vývoj | Preview | GA | Poznámky |
|-------- | :-----------: | :-------: | :--: | ----- |
| Klíče zásad – vygenerování, ruční, nahrání |  |  | × |  |
| Klíče zásad – RSA/certifikát, tajné klíče |  |  | × |  |


### <a name="developer-interface"></a>Vývojové rozhraní

| Funkce | Vývoj | Preview | GA | Poznámky |
| ------- | :-----------: | :-------: | :--: | ----- |
| Portál Azure – IEF UX |  |  | × |  |
| Nahrávání zásad |  |  | × |  |
| [Protokoly cest uživatelů Application Insights](troubleshoot-with-application-insights.md) |  | × |  | Používá se pro řešení potíží během vývoje.  |
| [Protokoly událostí Application Insights](analytics-with-application-insights.md) |  | × |  | Slouží k monitorování toků uživatelů v produkčním prostředí. |


## <a name="next-steps"></a>Další kroky

- Ověřte [Microsoft Graph operací, které jsou k dispozici pro Azure AD B2C](microsoft-graph-operations.md)
- Přečtěte si další informace o [vlastních zásadách a rozdílech s toky uživatelů](custom-policy-overview.md).
