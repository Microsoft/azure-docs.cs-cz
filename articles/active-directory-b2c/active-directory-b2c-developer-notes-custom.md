---
title: Poznámky pro vývojáře pro vlastní zásady – Azure Active Directory B2C | Dokumentace Microsoftu
description: Poznámky pro vývojáře na konfiguraci a správu Azure AD B2C s vlastními zásadami.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/18/2019
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: cf9c6f6a54c38f00e477e2a9d62e72ab5faccdef
ms.sourcegitcommit: 72cc94d92928c0354d9671172979759922865615
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/25/2019
ms.locfileid: "58418920"
---
# <a name="developer-notes-for-custom-policies-in-azure-active-directory-b2c"></a>Poznámky pro vývojáře pro vlastní zásady v Azure Active Directory B2C

Vlastní zásady konfigurace v Azure Active Directory B2C je teď obecně dostupná. Tato metoda konfigurace je cílená na pokročilé identity vývojáře, kteří vytvářejí řešení složitých identity. Vlastní zásady nutí sílu architekturu rozhraní identit k dispozici v Azure AD B2C tenanti. Pokročilé identity vývojáře, kteří používají vlastní zásady měli naplánovat investovat nějaký čas dokončení kurzy a referenční dokumenty pro čtení.

Zatímco většina možností vlastní zásady k dispozici jsou teď obecně dostupné, jsou základní funkce, jako jsou typy technický profil a obsahu definice rozhraní API, která jsou v různých fázích v životního cyklu softwaru. Mnoho dalších se chystá. Následující tabulka určuje úroveň dostupnosti na podrobnější úrovni.  

## <a name="features-that-are-generally-available"></a>Funkce, které jsou obecně dostupné

- Vytvořit a nahrát vlastní ověřování uživatele jízdy pomocí vlastních zásad.  
    - Popis cesty uživatele krok za krokem jako výměny mezi zprostředkovatelem deklarací identity.  
    - Definuje podmíněné větvení v centrech uživatele.  
- Spolupracovat s službám rozhraní REST API v centrech vaše vlastní ověřování uživatele.  
- Vytvoření federace pomocí zprostředkovatelů identity, které jsou kompatibilní s protokolu OpenIDConnect.  
- Vytvoření federace pomocí zprostředkovatelů identity, které používají protokol SAML 2.0.   

## <a name="responsibilities-of-custom-policy-feature-set-developers"></a>Odpovědnosti vývojáře vlastních zásad pro sadu funkcí

Konfigurace zásad ruční uděluje přístup nižší úrovně k základní platformy Azure AD B2C a má za následek vytvoření jedinečné, architektura vztahu důvěryhodnosti. Mnoho možná bude připadat vlastních poskytovatelů identit, vztahy důvěryhodnosti, integraci s externími službami a podrobné pracovní postupy vyžadují metodický přístup k návrhu a konfigurace. 

Vývojáři využívající sadu vlastních zásad pro funkce by měl splňovat následující pokyny:

- Seznámení s jazykem configuration management vlastní zásady a klíči nebo tajnými kódy. Další informace najdete v tématu [TrustFrameworkPolicy](trustframeworkpolicy.md). 
- Převzít vlastnictví scénáře a vlastní integraci. Zdokumentujte svou práci a informovat organizaci živého webu.  
- Proveďte testování metodický scénář. 
- Postupujte podle vývoj softwaru a přípravu, osvědčené postupy s minimálně jeden vývojové a testovací prostředí a jeden produkčního prostředí. 
- Udržujte si přehled o novém vývoji ze zprostředkovatelů identity a služby, které můžete integrovat s. Například sledovat, změny v tajných kódů a z plánovaných a neplánovaných změny ve službě. 
- Nastavit aktivní monitorování a monitorovat rychlost reakce produkční prostředí. Další informace o integraci se službou Application Insights najdete v tématu [Azure Active Directory B2C: Shromažďování protokolů](active-directory-b2c-custom-guide-eventlogger-appins.md). 
- Udržujte si kontaktní e-mailové adresy v rámci předplatného Azure a aby byla schopná reagovat na e-mailů týmu živého webu společnosti Microsoft. 
- Proveďte aktuální akci při doporučujeme to tak, že tým živého webu společnosti Microsoft.

## <a name="terms-for-features-in-public-preview"></a>Podmínky pro funkce ve verzi public preview

- Doporučujeme vám používat jenom pro účely hodnocení funkce ve verzi public preview. 
- Smlouvy o úrovni služeb (SLA) se nevztahují na funkce ve verzi public preview.
- Žádosti o podporu pro funkce ve verzi public preview můžete prostřednictvím kanálů podpory regulární podává. 

## <a name="features-by-stage-and-known-issues"></a>Funkce podle fáze a známé problémy

Rozhraní prostředí pro vlastní zásady/identit. Tyto funkce jsou ve vývoji konstantní a rychlé. V následující tabulce je index funkce a dostupnost součásti.

### <a name="identity-providers-tokens-protocols"></a>Poskytovatelé tokenů, protokoly identity

| Funkce | Vývoj | Preview | GA | Poznámky |
|-------- | ----------- | ------- | -- | ----- |
| IDP-OpenIDConnect |  |  | X | Například Google +.  |
| IDP-OAUTH2 |  |  | X | Například Facebook.  |
| IDP-OAUTH1 (twitter) |  | X |  | Například na Twitteru. |
| IDP-OAUTH1 (ex-twitter) |  |  |  | Nepodporuje se |
| IDP-SAML |  |   | X | Například Salesforce, služby AD FS. |
| IDP-WSFED | X |  |  |  |
| Předávající strany OAUTH1 |  |  |  | Nepodporuje se. |
| Předávající strany OAUTH2 |  |  | X |  |
| Předávající strany OIDC |  |  | X |  |
| Přijímající strany SAML | X |  |  |  |
| Předávající strany WSFED | X |  |  |  |
| Rozhraní REST API služby Basic a ověřování certifikátů |  |  | X | Například Azure Logic Apps. |

### <a name="component-support"></a>Podpora součásti

| Funkce | Vývoj | Preview | GA | Poznámky |
| ------- | ----------- | ------- | -- | ----- |
| Azure Multi Factor Authentication |  |  | X |  |
| Azure Active Directory jako místní adresář |  |  | X |  |
| Azure subsystém e-mailu pro ověření e-mailu |  |  | X |  |
| Podpora více jazyků|  |  | X |  |
| Predikátu ověření |  |  | X | Například složitost hesla. |
| Použití poskytovatelů služeb e-mailu třetích stran | X |  |  |  |

### <a name="content-definition"></a>Definice obsahu

| Funkce | Vývoj | Preview | GA | Poznámky |
| ------- | ----------- | ------- | -- | ----- |
| Chybové stránky, api.error |  |  | X |  |
| Stránka výběru zprostředkovatele identity, api.idpselections |  |  | X |  |
| Výběr zprostředkovatele identity pro registraci, api.idpselections.signup |  |  | X |  |
| Zapomněli jste heslo, api.localaccountpasswordreset |  |  | X |  |
| Místní účet přihlášení, api.localaccountsignin |  |  | X |  |
| Místní účet pro zápis, api.localaccountsignup |  |  | X |  |
| Stránka vícefaktorového ověřování, api.phonefactor |  |  | X |  |
| Registrace, api.selfasserted s vlastním potvrzením účtu na sociální síti |  |  | X |  |
| Samoobslužné s prohlašovanou aktualizace profilu api.selfasserted.profileupdate |  |  | X |  |
| Sjednocené registrace nebo přihlášení stránce api.signuporsignin s parametrem "disableSignup" |  |  | X |  |
| JavaScript / stránku smlouvy |  | X |  |  |

### <a name="app-ief-integration"></a>Integrace aplikace IEF

| Funkce | Vývoj | Preview | GA | Poznámky |
| ------- | ----------- | ------- | -- | ----- |
| Domain_hint parametr řetězce dotazu |  |  | X | K dispozici jako deklarace identity, může být předán zprostředkovatel identity. |
| Login_hint parametr řetězce dotazu |  |  | X | K dispozici jako deklarace identity, může být předán zprostředkovatel identity. |
| Příkaz Insert JSON UserJourney prostřednictvím client_assertion | X |  |  | Se přestanou používat. |
| Vložit JSON do UserJourney jako id_token_hint |  | X |  | Přejít vpřed přístup k předání JSON. |
| Předat TOKEN zprostředkovatele identity do aplikace |  | X |  | Například ze sítě Facebook pro aplikaci. |

### <a name="session-management"></a>Správa relací

| Funkce | Vývoj | Preview | GA | Poznámky |
| ------- | ----------- | ------- | -- | ----- |
| Poskytovatel relace jednotného přihlašování |  |  | X |  |
| Zprostředkovatel relací externí přihlášení |  |  | X |  |
| Zprostředkovatel relací SAML SSO |  |  | X |  |
| Výchozí zprostředkovatel relace jednotného přihlašování |  |  | X |  |

### <a name="security"></a>Zabezpečení

| Funkce | Vývoj | Preview | GA | Poznámky |
|-------- | ----------- | ------- | -- | ----- |
| Zásady klíče generovat, manuální, nahrávání |  |  | X |  |
| Zásady klíče - RSA/Cert tajných kódů |  |  | X |  |
| Nahrání zásad |  |  | X |  |

### <a name="developer-interface"></a>Rozhraní pro vývojáře

| Funkce | Vývoj | Preview | GA | Poznámky |
| ------- | ----------- | ------- | -- | ----- |
| Azure Portal IEF uživatelského prostředí |  |  | X |  |
| Application Insights UserJourney Logs |  | X |  | Používá se pro řešení potíží během vývoje.  |
| Protokoly událostí Application Insights (pomocí kroků Orchestrace) |  | X |  | Používá k monitorování toky uživatelů v produkčním prostředí. |

## <a name="next-steps"></a>Další postup
[Další informace o vlastních zásad a rozdíl s toky uživatelů](active-directory-b2c-overview-custom.md).
