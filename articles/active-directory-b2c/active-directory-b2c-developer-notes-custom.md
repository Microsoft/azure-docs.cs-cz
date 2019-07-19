---
title: Poznámky pro vývojáře pro vlastní zásady – Azure Active Directory B2C | Microsoft Docs
description: Poznámky pro vývojáře při konfiguraci a údržbě Azure AD B2C s vlastními zásadami.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/18/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 3f8d1ac217647ee292338da875671ef8bd3f79db
ms.sourcegitcommit: 920ad23613a9504212aac2bfbd24a7c3de15d549
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/15/2019
ms.locfileid: "68227198"
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
- Nastavte aktivní monitorování a sledujte rychlost odezvy v produkčním prostředí. Další informace o integraci s Application Insights najdete v tématu [Azure Active Directory B2C: Shromažďování protokolů](active-directory-b2c-custom-guide-eventlogger-appins.md).
- Udržujte kontaktní e-mailové adresy aktuální v rámci předplatného Azure a zůstaňte reagovat na e-maily týmu živého webu společnosti Microsoft.
- Využijte včasnou akci, kterou by to měl udělat tým Microsoftu pro živý Web.

## <a name="terms-for-features-in-public-preview"></a>Výrazy pro funkce ve verzi Public Preview

- Doporučujeme používat funkce Public Preview jenom pro účely testování.
- Smlouvy o úrovni služeb (SLA) se nevztahují na funkce verze Public Preview.
- Žádosti o podporu pro veřejné funkce ve verzi Preview je možné dodávat prostřednictvím běžných kanálů podpory.

## <a name="features-by-stage-and-known-issues"></a>Funkce podle fáze a známé problémy

Možnosti architektury prostředí vlastních zásad a identit jsou v rámci stálého a rychlého vývoje. V následující tabulce je index funkcí a dostupnosti součástí.

### <a name="identity-providers-tokens-protocols"></a>Zprostředkovatelé identity, tokeny, protokoly

| Funkce | Vývoj | Náhled | GA | Poznámky |
|-------- | :-----------: | :-------: | :--: | ----- |
| IDP-OpenIDConnect |  |  | X | Například Google +.  |
| IDP – OAUTH2 |  |  | X | Například Facebook.  |
| IDP-OAUTH1 (Twitter) |  | X |  | Například Twitter. |
| IDP-OAUTH1 (ex-twitter) |  |  |  | Nepodporuje se |
| IDP-SAML |  |   | X | Například Salesforce, ADFS. |
| IDP-WSFED | X |  |  |  |
| OAUTH1 předávající strany |  |  |  | Nepodporuje se. |
| OAUTH2 předávající strany |  |  | X |  |
| OIDC předávající strany |  |  | X |  |
| SAML předávající strany | X |  |  |  |
| WSFED předávající strany | X |  |  |  |
| REST API se základními a ověřovacími certifikáty |  |  | X | Například Azure Logic Apps. |

### <a name="component-support"></a>Podpora komponent

| Funkce | Vývoj | Náhled | GA | Poznámky |
| ------- | :-----------: | :-------: | :--: | ----- |
| Azure Multi Factor Authentication |  |  | X |  |
| Azure Active Directory jako místní adresář |  |  | X |  |
| Podsystém e-mailů Azure pro ověření e-mailu |  |  | X |  |
| Podpora více jazyků|  |  | X |  |
| Ověřování predikátů |  |  | X | Například složitost hesla. |
| Používání poskytovatelů e-mailových služeb třetích stran | X |  |  |  |

### <a name="content-definition"></a>Definice obsahu

| Funkce | Vývoj | Náhled | GA | Poznámky |
| ------- | :-----------: | :-------: | :--: | ----- |
| Chybová stránka, rozhraní API. Chyba |  |  | X |  |
| IDP výběr stránky, API. idpselections |  |  | X |  |
| IDP výběr pro registraci, API. idpselections. signup |  |  | X |  |
| Zapomenuté heslo, API. localaccountpasswordreset |  |  | X |  |
| Přihlášení k místnímu účtu, API. localaccountsignin |  |  | X |  |
| Registrace místního účtu, API. localaccountsignup |  |  | X |  |
| Stránka MFA, API. PhoneFactor |  |  | X |  |
| Přihlášený účet sociální sítě s vlastním kontrolním účtem, API. selfasserted |  |  | X |  |
| Aktualizace profilu s vlastním uplatněním, API. selfasserted. profileupdate |  |  | X |  |
| Sjednocené registrační nebo přihlašovací stránka, API. signuporsignin s parametrem "disableSignup" |  |  | X |  |
| Rozložení JavaScriptu/stránky |  | X |  |  |

### <a name="app-ief-integration"></a>Integrace aplikací – IEF

| Funkce | Vývoj | Náhled | GA | Poznámky |
| ------- | :-----------: | :-------: | :--: | ----- |
| Parametr řetězce dotazu domain_hint |  |  | X | K dispozici jako deklarace identity, se dají předat do IDP. |
| Parametr řetězce dotazu login_hint |  |  | X | K dispozici jako deklarace identity, se dají předat do IDP. |
| Vložení formátu JSON do UserJourney prostřednictvím client_assertion | X |  |  | Bude zastaralá. |
| Vložení formátu JSON do UserJourney jako id_token_hint |  | X |  | Přejít k předávanému přístupu a předávat JSON. |
| Předat TOKEN IDP aplikaci |  | X |  | Například z Facebooku do aplikace. |

### <a name="session-management"></a>Správa relací

| Funkce | Vývoj | Náhled | GA | Poznámky |
| ------- | :-----------: | :-------: | :--: | ----- |
| Zprostředkovatel relace jednotného přihlašování |  |  | X |  |
| Externí zprostředkovatel přihlašovací relace |  |  | X |  |
| Zprostředkovatel relací SAML SSO |  |  | X |  |
| Výchozí zprostředkovatel relace jednotného přihlašování |  |  | X |  |

### <a name="security"></a>Zabezpečení

| Funkce | Vývoj | Náhled | GA | Poznámky |
|-------- | :-----------: | :-------: | :--: | ----- |
| Klíče zásad – vygenerování, ruční, nahrání |  |  | X |  |
| Klíče zásad – RSA/certifikát, tajné klíče |  |  | X |  |
| Nahrávání zásad |  |  | X |  |

### <a name="developer-interface"></a>Vývojové rozhraní

| Funkce | Vývoj | Náhled | GA | Poznámky |
| ------- | :-----------: | :-------: | :--: | ----- |
| Portál Azure – IEF UX |  |  | X |  |
| Application Insights UserJourney Logs |  | X |  | Používá se pro řešení potíží během vývoje.  |
| Protokoly událostí Application Insights (prostřednictvím kroků orchestrace) |  | X |  | Slouží k monitorování toků uživatelů v produkčním prostředí. |

## <a name="next-steps"></a>Další postup

Přečtěte si další informace o [vlastních zásadách a rozdílech s toky uživatelů](active-directory-b2c-overview-custom.md).
