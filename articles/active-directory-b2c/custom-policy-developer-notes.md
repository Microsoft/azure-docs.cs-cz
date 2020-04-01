---
title: Poznámky pro vývojáře pro vlastní zásady
titleSuffix: Azure AD B2C
description: Poznámky pro vývojáře o konfiguraci a údržbě Azure AD B2C s vlastní zásady.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 403ca480bcf0743d81e375c122c888db96bbf543
ms.sourcegitcommit: 27bbda320225c2c2a43ac370b604432679a6a7c0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/31/2020
ms.locfileid: "80408712"
---
# <a name="developer-notes-for-custom-policies-in-azure-active-directory-b2c"></a>Poznámky vývojářů pro vlastní zásady ve službě Azure Active Directory B2C

Konfigurace vlastních zásad ve službě Azure Active Directory B2C je teď obecně dostupná. Tento způsob konfigurace je zaměřen na pokročilé vývojáře identit, kteří vytváří komplexní řešení identit. Vlastní zásady zpřístupní výkon rozhraní Identity Experience Framework v tenantech Azure AD B2C.
Pokročilí vývojáři identit, kteří používají vlastní zásady, by měli plánovat investovat nějaký čas dokončením procházky a čtením referenčních dokumentů.

Zatímco většina možností vlastních zásad, které jsou nyní k dispozici, jsou nyní obecně dostupné, existují základní funkce, jako jsou typy technických profilů a rozhraní API definice obsahu, které jsou v různých fázích životního cyklu softwaru. Mnoho dalších přichází. Níže uvedená tabulka určuje úroveň dostupnosti na podrobnější úrovni.

## <a name="features-that-are-generally-available"></a>Funkce, které jsou obecně dostupné

- Vytvářejte a nahrávejte vlastní cesty uživatelů ověřování pomocí vlastních zásad.
    - Popište cesty uživatele krok za krokem jako výměny mezi poskytovateli deklarací identity.
    - Definujte podmíněné větvení v cestách uživatelů.
- Spolupracujte se službami rest api ve vašich vlastních cestách uživatelů ověřování.
- Federate s poskytovateli identit, kteří jsou kompatibilní s protokolem OpenIDConnect.
- Federate s poskytovateli identity, které dodržují protokol SAML 2.0.

## <a name="responsibilities-of-custom-policy-feature-set-developers"></a>Odpovědnosti vývojářů sad funkcí vlastních zásad

Ruční konfigurace zásad uděluje přístup na nižší úrovni k základní platformě Azure AD B2C a má za následek vytvoření jedinečného rámce důvěryhodnosti. Mnoho možných permutací vlastních zprostředkovatelů identit, vztahů důvěryhodnosti, integrace s externími službami a postupných pracovních postupů vyžaduje metodický přístup k návrhu a konfiguraci.

Vývojáři, kteří spotřebovávají sadu vlastních funkcí zásad, by měli dodržovat následující pokyny:

- Seznamte se s konfiguračním jazykem vlastních zásad a správou klíčů a tajných klíčů. Další informace naleznete v tématu [TrustFrameworkPolicy](trustframeworkpolicy.md).
- Převzít vlastnictví scénářů a vlastní integrace. Zdokumentujte svou práci a informujte svou organizaci živého webu.
- Proveďte testování metodických scénářů.
- Sledujte vývoj softwaru a přípravu osvědčených postupů s minimálně jedním vývojovým a testovacím prostředím a jedním produkčním prostředím.
- Buďte informováni o novém vývoji od poskytovatelů identit a služeb, se kterými integrujete. Můžete například sledovat změny v tajných kódech a naplánované a neplánované změny služby.
- Nastavte aktivní monitorování a sledujte odezvu produkčních prostředí. Další informace o integraci s Application Insights najdete [v tématu Azure Active Directory B2C: Shromažďování protokolů](analytics-with-application-insights.md).
- Udržujte kontaktní e-mailové adresy aktuální v předplatném Azure a zůstaňte v kontaktu s týmovými e-maily microsoftu pro živé weby.
- Pokud vám to tým microsoftlive-site dopažuje, proveďte včasnou akci.

## <a name="terms-for-features-in-public-preview"></a>Podmínky pro funkce ve verzi Public Preview

- Doporučujeme vám používat funkce veřejného náhledu pouze pro účely hodnocení.
- Smlouvy o úrovni služeb (SLA) se nevztahují na funkce veřejné verze Preview.
- Žádosti o podporu pro funkce veřejného náhledu lze podat prostřednictvím běžných kanálů podpory.

## <a name="features-by-stage-and-known-issues"></a>Funkce podle fáze a známé problémy

Možnosti rozhraní Custom Policy/Identity Experience Framework jsou pod neustálým a rychlým vývojem. V následující tabulce je index dostupnosti funkcí a součástí.


### <a name="protocols-and-authorization-flows"></a>Protokoly a toky autorizace

| Funkce | Vývoj | Preview | GA | Poznámky |
|-------- | :-----------: | :-------: | :--: | ----- |
| [Autorizační kód OAuth2](authorization-code-flow.md) |  |  | × |  |
| Autorizační kód OAuth2 s PKCE |  |  | × | Pouze mobilní aplikace  |
| [Implicitní tok OAuth2](implicit-flow-single-page-application.md) |  |  | × |  |
| [Přihlašovací údaje vlastníka prostředku OAuth2](ropc-custom.md) |  | × |  |  |
| [OIDC připojení](openid-connect.md) |  |  | × |  |
| [SAML2](connect-with-saml-service-providers.md)  |  |×  |  | Vazby POST a Redirect. |
| OAuth1 |  |  |  | Není podporováno. |
| WSFED | × |  |  |  |

### <a name="identify-providers-federation"></a>Identifikace federace poskytovatelů 

| Funkce | Vývoj | Preview | GA | Poznámky |
|-------- | :-----------: | :-------: | :--: | ----- |
| [OpenID Connect](openid-connect-technical-profile.md) |  |  | × | Například Google+.  |
| [OAuth2](oauth2-technical-profile.md) |  |  | × | Například Facebook.  |
| [OAuth1](oauth1-technical-profile.md) |  | × |  | Například Twitter. |
| [SAML2](saml-technical-profile.md) |  |   | × | Například Salesforce, ADFS. |
| WSFED| × |  |  |  |


### <a name="rest-api-integration"></a>Integrace rozhraní REST API

| Funkce | Vývoj | Preview | GA | Poznámky |
|-------- | :-----------: | :-------: | :--: | ----- |
| [REST API se základním authem](secure-rest-api.md#http-basic-authentication) |  |  | × |  |
| [REST API s auth klientského certifikátu](secure-rest-api.md#https-client-certificate-authentication) |  |  | × |  |
| [REST API s ošetřovnou OAuth2](secure-rest-api.md#oauth2-bearer-authentication) |  | × |  |  |

### <a name="component-support"></a>Podpora komponent

| Funkce | Vývoj | Preview | GA | Poznámky |
| ------- | :-----------: | :-------: | :--: | ----- |
| [Ověření faktoru telefonu](phone-factor-technical-profile.md) |  |  | × |  |
| [Azure MFA ověřování](multi-factor-auth-technical-profile.md) |  | × |  |  |
| [Jednorázové heslo](one-time-password-technical-profile.md) |  | × |  |  |
| [Služba Azure Active Directory](active-directory-technical-profile.md) jako místní adresář |  |  | × |  |
| Podsystém e-mailu Azure pro ověření e-mailu |  |  | × |  |
| [Poskytovatelé e-mailových služeb třetích stran](custom-email.md) |  |×  |  |  |
| [Podpora ve více jazycích](localization.md)|  |  | × |  |
| [Predikátová ověření](predicates.md) |  |  | × | Například složitost hesla. |
| [Zobrazit ovládací prvky](display-controls.md) |  |×  |  |  |


### <a name="page-layout-versions"></a>Verze rozložení stránky

| Funkce | Vývoj | Preview | GA | Poznámky |
| ------- | :-----------: | :-------: | :--: | ----- |
| [2.0.0](page-layout.md#200) |  | × |  |  |
| [1.2.0](page-layout.md#120) |  | × |  |  |
| [1.1.0](page-layout.md#110) |  |  | × |  |
| [1.0.0](page-layout.md#100) |  |  | × |  |
| [Podpora pro JavaScript](javascript-samples.md) |  | × |  |  |

### <a name="app-ief-integration"></a>Integrace App-IEF

| Funkce | Vývoj | Preview | GA | Poznámky |
| ------- | :-----------: | :-------: | :--: | ----- |
| Parametr řetězce dotazu`domain_hint` |  |  | × | K dispozici jako deklarace, může být předána IDP. |
| Parametr řetězce dotazu`login_hint` |  |  | × | K dispozici jako deklarace, může být předána IDP. |
| Vložit JSON do cesty uživatele přes`client_assertion` | × |  |  | Bude zastaralé. |
| Vložit JSON do cesty uživatele jako`id_token_hint` |  | × |  | Go-vpřed přístup projít JSON. |
| [Předání tokenu zprostředkovatele identity do aplikace](idp-pass-through-custom.md) |  | × |  | Například z Facebooku do aplikace. |

### <a name="session-management"></a>Správa relací

| Funkce | Vývoj | Preview | GA | Poznámky |
| ------- | :-----------: | :-------: | :--: | ----- |
| [Výchozí zprostředkovatel relace připřipomene k sazí](custom-policy-reference-sso.md#defaultssosessionprovider) |  |  | × |  |
| [Externí zprostředkovatel elaze přihlášení](custom-policy-reference-sso.md#externalloginssosessionprovider) |  |  | × |  |
| [Zprostředkovatel relace přisymit služby SAML](custom-policy-reference-sso.md#samlssosessionprovider) |  |  | × |  |


### <a name="security"></a>Zabezpečení

| Funkce | Vývoj | Preview | GA | Poznámky |
|-------- | :-----------: | :-------: | :--: | ----- |
| Klíče zásad - generovat, ručně, nahrávat |  |  | × |  |
| Klíče zásad - RSA/Cert, tajné klíče |  |  | × |  |


### <a name="developer-interface"></a>Vývojářské rozhraní

| Funkce | Vývoj | Preview | GA | Poznámky |
| ------- | :-----------: | :-------: | :--: | ----- |
| Portál Azure-IEF UX |  |  | × |  |
| Nahrání zásad |  |  | × |  |
| [Protokoly cest uživatelů Application Insights](troubleshoot-with-application-insights.md) |  | × |  | Používá se pro řešení potíží během vývoje.  |
| [Protokoly událostí Application Insights](application-insights-technical-profile.md) |  | × |  | Slouží ke sledování toků uživatelů v produkčním prostředí. |


## <a name="next-steps"></a>Další kroky

- Kontrola [operací Microsoft Graphu dostupných pro Azure AD B2C](microsoft-graph-operations.md)
- Přečtěte si další informace o [vlastních zásadách a rozdílech s toky uživatelů](custom-policy-overview.md).
