---
title: Poznámky pro vývojáře pro vlastní zásady
titleSuffix: Azure AD B2C
description: Poznámky pro vývojáře při konfiguraci a údržbě Azure AD B2C s vlastními zásadami.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/12/2020
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 6439393b72eb724ca017edc17ce7a7c36c275fca
ms.sourcegitcommit: bdf31d87bddd04382effbc36e0c465235d7a2947
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/12/2020
ms.locfileid: "77166966"
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

Ruční konfigurace zásad uděluje přístup na nižší úrovni k základní platformě Azure AD B2C a má za následek vytvoření jedinečného, důvěryhodného rozhraní. Mnoho možných permutací vlastních zprostředkovatelů identity, vztahů důvěryhodnosti, integrace s externími službami a podrobných pracovních postupů vyžaduje přístup k návrhu a konfiguraci.

Vývojáři, kteří používají sadu funkcí vlastní zásady, by měli dodržovat následující pokyny:

- Seznamte se s jazykem konfigurace vlastních zásad a správou klíčů a tajných kódů. Další informace najdete v tématu [TrustFrameworkPolicy](trustframeworkpolicy.md).
- Převezměte vlastnictví scénářů a vlastních integrací. Zdokumentujte svou práci a informujte svou organizaci s živým webem.
- Proveďte testování scénáře metod.
- Dodržujte osvědčené postupy vývoje softwaru a přípravy s minimálním nasazením jednoho vývojového a testovacího prostředí a jednoho provozního prostředí.
- Informujte se o novém vývoji od zprostředkovatelů identity a služeb, které do nástroje integrujete. Můžete například sledovat změny v tajných klíčích a naplánovaných a neplánovaných změnách služby.
- Nastavte aktivní monitorování a sledujte rychlost odezvy v produkčním prostředí. Další informace o integraci s Application Insights najdete v tématu [Azure Active Directory B2C: shromažďování protokolů](analytics-with-application-insights.md).
- Udržujte kontaktní e-mailové adresy aktuální v rámci předplatného Azure a zůstaňte reagovat na e-maily týmu živého webu společnosti Microsoft.
- Využijte včasnou akci, kterou by to měl udělat tým Microsoftu pro živý Web.

## <a name="terms-for-features-in-public-preview"></a>Výrazy pro funkce ve verzi Public Preview

- Doporučujeme používat funkce Public Preview jenom pro účely testování.
- Smlouvy o úrovni služeb (SLA) se nevztahují na funkce verze Public Preview.
- Žádosti o podporu pro veřejné funkce ve verzi Preview je možné dodávat prostřednictvím běžných kanálů podpory.

## <a name="features-by-stage-and-known-issues"></a>Funkce podle fáze a známé problémy

Možnosti architektury prostředí vlastních zásad a identit jsou v rámci stálého a rychlého vývoje. V následující tabulce je index funkcí a dostupnosti součástí.

### <a name="identity-providers-tokens-protocols"></a>Zprostředkovatelé identity, tokeny, protokoly

| Funkce | Vývoj | Preview | GA | Poznámky: |
|-------- | :-----------: | :-------: | :--: | ----- |
| IDP-OpenIDConnect |  |  | × | Například Google +.  |
| IDP – OAUTH2 |  |  | × | Například Facebook.  |
| IDP-OAUTH1 (Twitter) |  | × |  | Například Twitter. |
| IDP-OAUTH1 (ex-twitter) |  |  |  | Nepodporuje se |
| IDP-SAML |  |   | × | Například Salesforce, ADFS. |
| IDP-WSFED | × |  |  |  |
| OAUTH1 předávající strany |  |  |  | Nepodporuje se. |
| OAUTH2 předávající strany |  |  | × |  |
| OIDC předávající strany |  |  | × |  |
| SAML předávající strany |  |×  |  |  |
| WSFED předávající strany | × |  |  |  |
| REST API se základními a ověřovacími certifikáty |  |  | × | Například Azure Logic Apps. |

### <a name="component-support"></a>Podpora komponent

| Funkce | Vývoj | Preview | GA | Poznámky: |
| ------- | :-----------: | :-------: | :--: | ----- |
| Azure Multi-Factor Authentication |  |  | × |  |
| Azure Active Directory jako místní adresář |  |  | × |  |
| Podsystém e-mailů Azure pro ověření e-mailu |  |  | × |  |
| Podpora více jazyků|  |  | × |  |
| Ověřování predikátů |  |  | × | Například složitost hesla. |
| Používání poskytovatelů e-mailových služeb třetích stran |  |×  |  |  |

### <a name="content-definition"></a>Definice obsahu

| Funkce | Vývoj | Preview | GA | Poznámky: |
| ------- | :-----------: | :-------: | :--: | ----- |
| Chybová stránka, rozhraní API. Chyba |  |  | × |  |
| IDP výběr stránky, API. idpselections |  |  | × |  |
| IDP výběr pro registraci, API. idpselections. signup |  |  | × |  |
| Zapomenuté heslo, API. localaccountpasswordreset |  |  | × |  |
| Přihlášení k místnímu účtu, API. localaccountsignin |  |  | × |  |
| Registrace místního účtu, API. localaccountsignup |  |  | × |  |
| Stránka MFA, API. PhoneFactor |  |  | × |  |
| Přihlášený účet sociální sítě s vlastním kontrolním účtem, API. selfasserted |  |  | × |  |
| Aktualizace profilu s vlastním uplatněním, API. selfasserted. profileupdate |  |  | × |  |
| Sjednocené registrační nebo přihlašovací stránka, API. signuporsignin s parametrem "disableSignup" |  |  | × |  |
| Rozložení JavaScriptu/stránky |  | × |  |  |

### <a name="app-ief-integration"></a>Integrace aplikací – IEF

| Funkce | Vývoj | Preview | GA | Poznámky: |
| ------- | :-----------: | :-------: | :--: | ----- |
| Parametr řetězce dotazu domain_hint |  |  | × | K dispozici jako deklarace identity, se dají předat do IDP. |
| Parametr řetězce dotazu login_hint |  |  | × | K dispozici jako deklarace identity, se dají předat do IDP. |
| Vložení formátu JSON do UserJourney přes client_assertion | × |  |  | Bude zastaralá. |
| Vložit JSON do UserJourney jako id_token_hint |  | × |  | Přejít k předávanému přístupu a předávat JSON. |
| Předat TOKEN IDP aplikaci |  | × |  | Například z Facebooku do aplikace. |

### <a name="session-management"></a>Správa relací

| Funkce | Vývoj | Preview | GA | Poznámky: |
| ------- | :-----------: | :-------: | :--: | ----- |
| Zprostředkovatel relace jednotného přihlašování |  |  | × |  |
| Externí zprostředkovatel přihlašovací relace |  |  | × |  |
| Zprostředkovatel relací SAML SSO |  |  | × |  |
| Výchozí zprostředkovatel relace jednotného přihlašování |  |  | × |  |

### <a name="security"></a>Zabezpečení

| Funkce | Vývoj | Preview | GA | Poznámky: |
|-------- | :-----------: | :-------: | :--: | ----- |
| Klíče zásad – vygenerování, ruční, nahrání |  |  | × |  |
| Klíče zásad – RSA/certifikát, tajné klíče |  |  | × |  |
| Nahrávání zásad |  |  | × |  |

### <a name="developer-interface"></a>Vývojové rozhraní

| Funkce | Vývoj | Preview | GA | Poznámky: |
| ------- | :-----------: | :-------: | :--: | ----- |
| Portál Azure – IEF UX |  |  | × |  |
| Application Insights UserJourney Logs |  | × |  | Používá se pro řešení potíží během vývoje.  |
| Protokoly událostí Application Insights (prostřednictvím kroků orchestrace) |  | × |  | Slouží k monitorování toků uživatelů v produkčním prostředí. |

## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o [vlastních zásadách a rozdílech s toky uživatelů](custom-policy-overview.md).
