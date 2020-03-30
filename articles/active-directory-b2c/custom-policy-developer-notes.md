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
ms.date: 02/12/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: ee3b5bd3278412949074b77f9d1c53d63a467280
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78189391"
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

### <a name="identity-providers-tokens-protocols"></a>Poskytovatelé identit, tokeny, protokoly

| Funkce | Vývoj | Preview | GA | Poznámky |
|-------- | :-----------: | :-------: | :--: | ----- |
| IDP-OpenIDConnect |  |  | × | Například Google+.  |
| IDP-OAUTH2 |  |  | × | Například Facebook.  |
| IDP-OAUTH1 (twitter) |  | × |  | Například Twitter. |
| IDP-OAUTH1 (ex-twitter) |  |  |  | Nepodporuje se |
| IDP-SAML |  |   | × | Například Salesforce, ADFS. |
| IDP-WSFED | × |  |  |  |
| Předávající strana OAUTH1 |  |  |  | Není podporováno. |
| Předávající strana OAUTH2 |  |  | × |  |
| Předávající strana OIDC |  |  | × |  |
| Předávající strana SAML |  |×  |  |  |
| Předávající strana WSFED | × |  |  |  |
| REST API se základním a certifikačním utvářením |  |  | × | Například Azure Logic Apps. |

### <a name="component-support"></a>Podpora komponent

| Funkce | Vývoj | Preview | GA | Poznámky |
| ------- | :-----------: | :-------: | :--: | ----- |
| Azure vícefaktorové ověřování |  |  | × |  |
| Služba Azure Active Directory jako místní adresář |  |  | × |  |
| Podsystém Azure Email pro ověření e-mailu |  |  | × |  |
| Podpora ve více jazycích|  |  | × |  |
| Predikátová ověření |  |  | × | Například složitost hesla. |
| Používání poskytovatelů e-mailových služeb třetích stran |  |×  |  |  |

### <a name="content-definition"></a>Definice obsahu

| Funkce | Vývoj | Preview | GA | Poznámky |
| ------- | :-----------: | :-------: | :--: | ----- |
| Chybová stránka api.error |  |  | × |  |
| Stránka výběru IDP, api.idpselections |  |  | × |  |
| Výběr IDP pro registraci, api.idpselections.signup |  |  | × |  |
| Zapomenuté heslo, api.localaccountpasswordreset |  |  | × |  |
| Místní přihlášení k účtu, api.localaccountsignin |  |  | × |  |
| Registrace místního účtu, api.localaccountsignup |  |  | × |  |
| Stránka MFA, api.phonefactor |  |  | × |  |
| Self-tvrdil, sociální účet sign-up, api.selfasserted |  |  | × |  |
| Aktualizace profilu s vlastním uplatněním, api.selfasserted.profileupdate |  |  | × |  |
| Sjednocená registrační nebo přihlašovací stránka, api.signuporsignin, s parametrem "disableSignup" |  |  | × |  |
| Rozložení JavaScript / Stránka |  | × |  |  |

### <a name="app-ief-integration"></a>Integrace App-IEF

| Funkce | Vývoj | Preview | GA | Poznámky |
| ------- | :-----------: | :-------: | :--: | ----- |
| domain_hint parametru řetězce dotazu |  |  | × | K dispozici jako deklarace, může být předána IDP. |
| login_hint parametru řetězce dotazu |  |  | × | K dispozici jako deklarace, může být předána IDP. |
| Vložit JSON do UserJourney přes client_assertion | × |  |  | Bude zastaralé. |
| Vložit JSON do UserJourney jako id_token_hint |  | × |  | Go-vpřed přístup projít JSON. |
| Předat IDP TOKEN do aplikace |  | × |  | Například z Facebooku do aplikace. |

### <a name="session-management"></a>Správa relací

| Funkce | Vývoj | Preview | GA | Poznámky |
| ------- | :-----------: | :-------: | :--: | ----- |
| Zprostředkovatel relace přisážaní k zapisování dat |  |  | × |  |
| Externí zprostředkovatel elaze přihlášení |  |  | × |  |
| Zprostředkovatel relace přisychadla služby SAML |  |  | × |  |
| Výchozí zprostředkovatel relace připřijím a so |  |  | × |  |

### <a name="security"></a>Zabezpečení

| Funkce | Vývoj | Preview | GA | Poznámky |
|-------- | :-----------: | :-------: | :--: | ----- |
| Klíče zásad - generovat, ručně, nahrávat |  |  | × |  |
| Klíče zásad - RSA/Cert, tajné klíče |  |  | × |  |
| Nahrání zásad |  |  | × |  |

### <a name="developer-interface"></a>Vývojářské rozhraní

| Funkce | Vývoj | Preview | GA | Poznámky |
| ------- | :-----------: | :-------: | :--: | ----- |
| Portál Azure-IEF UX |  |  | × |  |
| Protokoly informací o informacích o aplikacích |  | × |  | Používá se pro řešení potíží během vývoje.  |
| Protokoly událostí Application Insights (pomocí kroků orchestrace) |  | × |  | Slouží ke sledování toků uživatelů v produkčním prostředí. |

## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o [vlastních zásadách a rozdílech s toky uživatelů](custom-policy-overview.md).
