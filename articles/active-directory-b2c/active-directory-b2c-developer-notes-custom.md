---
title: Poznámky pro vývojáře na použití vlastní zásady v Azure Active Directory B2C | Microsoft Docs
description: Poznámky pro vývojáře o konfiguraci a údržbu Azure AD B2C pomocí vlastních zásad.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 10/13/2017
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: ce323972dcdbf673311b407f427bc452fbe6dc3a
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/04/2018
ms.locfileid: "34709722"
---
# <a name="release-notes-for-azure-active-directory-b2c-custom-policy-public-preview"></a>Poznámky k verzi pro verzi public preview služby Azure Active Directory B2C vlastních zásad
Sada funkcí vlastních zásad je nyní k dispozici pro vyhodnocení v rámci verze public preview pro všechny Azure Active Directory B2C zákazníků (Azure AD B2C). Tato sada funkcí je cílena na vývojáře pokročilé identity vytváření nejvíce komplexní řešení identity.  

Tato sada funkcí v současné době vyžadují vývojářům konfigurovat rozhraní Identity prostředí přímo prostřednictvím úpravy souborů XML. Tato metoda konfigurace je výkonná a komplexní. Pokročilé vývojáře, kteří používají rozhraní Identity prostředí měli naplánovat investovat chvíli dokončení návodů a čtení dokumenty referenční identity. 

## <a name="features-included-in-this-public-preview"></a>Funkce obsažené v této verzi public preview
Nové funkce, zavedená ve verzi public preview vývojáři můžete provádět následující úlohy:<br>

* Autor a nahrání vlastní ověřování uživatele cesty pomocí vlastních zásad. 
   * Popis uživatelské cesty podrobné jako výměny mezi zprostředkovatelem deklarací identity. 
   * Definujte podmíněného větvení v cesty uživatele. 
* Integrate povoleno rozhraní REST API služby ve vaší vlastní ověřování uživatele cesty.  
* Přidejte federaci se zprostředkovatelů identity, které jsou kompatibilní s standardní OpenIDConnect. <br>
* Přidejte federaci se zprostředkovatelů identity, které dodržovat protokolu SAML verze 2.0. 

## <a name="terms-of-the-public-preview"></a>Podmínky verze public Preview

* Doporučujeme vám používat nové funkce jenom pro účely hodnocení.<br>
* Nové funkce nejsou určeny pro použití v provozním prostředí.<br>
* Smlouvy o úrovni služeb (SLA) se nevztahují na nové funkce. <br>
* Žádosti o podporu můžete podává prostřednictvím regulární podpora kanálů. <br>
* Neexistuje žádné přislíbeném datum pro obecnou dostupnost.<br>
* Naše uvážení a z jakéhokoli důvodu můžete Microsoft příznak a odmítnout nebo omezit scénáře a cesty uživatele, které překračují oboru listinou produktu Azure AD B2C sloužit jako platforma správy (CIAM) přístupu a identit zákazníka.

## <a name="responsibilities-of-custom-policy-feature-set-developers"></a>Odpovědnosti vlastních zásad pro sadu funkcí vývojářů
Konfigurace zásad ruční uděluje nižší úrovně přístupu pro základní platformu Azure AD B2C a má za následek vytvoření rozhraní jedinečný plně přizpůsobitelná vztah důvěryhodnosti. Počet možných kombinací zprostředkovatelů vlastní identity, vztahy důvěryhodnosti, integrace s externích služeb a podrobné pracovních umístit větší nároky na pokročilé vývojáře využívají je.

Chcete-li plně využívat výhod verzi public preview, doporučujeme, aby vývojáři využívání sadu funkcí vlastní zásady dodržovat následující pokyny:
* Seznamte se s jazyk konfigurace Identity Framework prostředí a správu tajné klíče nebo klíče.
* Převzít vlastnictví scénáře a vlastní integrace.
* Proveďte testování metodický scénář.
* Postupujte podle vývoj softwaru a pracovní osvědčené postupy s minimálně jeden vývoj a testování prostředí a jeden produkčního prostředí.
* Udržení informovanosti o novém vývoji z poskytovatelů identit a službách, které můžete integrovat. Například sledovat určité změny v tajných klíčů a plánovaných a neplánovaných změn ke službě.
* Nastavte aktivní monitorování a sledování odezvy provozní prostředí.
* Aktuálnost kontaktní e-mailové adresy v rámci předplatného Azure a zůstat reaguje na e-mailů team Web live společnosti Microsoft.
* Trvat včas akce při nedoporučuje Uděláte to tak tým Microsoft live-site. 

## <a name="features-by-stage-and-known-issues"></a>Funkce ve fázi a známé problémy
Vlastní zásady/Identity rozhraní Framework možnosti jsou ve vývoji konstant a rychlé.  Tato tabulka je index dostupnost funkcí nebo součásti.

Ptejte se ve Stack Overflow v [https://aka.ms/aadb2cso](https://aka.ms/aadb2cso)


### <a name="identity-providers-tokens-protocols"></a>Protokoly poskytovatelů, tokeny, identity
Rozhraní s externí součásti a aplikace

| Funkce | Vývoj | Preview | GA | Poznámky |
|---------------------------------------------|-------------|---------|----|-------|
| Rozšíření IDP OpenIDConnect |  | x |  | například Google + |
| ROZŠÍŘENÍ IDP OAUTH2 |  | x |  | například Facebook  |
| ROZŠÍŘENÍ IDP OAUTH1 |  | x |  | například služby Twitter |
| ROZŠÍŘENÍ IDP SAML |  | x |  | například Salesforce, služba AD FS |
| IDP-WSFED | x |  |  |  |
| Předávající strany OAUTH |  | x |  |  |
| Předávající strany OIDC |  | x |  |  |
| Předávající strany SAML | x |  |  |  |
| Předávající strany WSFED | x |  |  |  |
| REST API s basic a certifikát ověřování |  | x |  | například Azure Functions |


### <a name="component-support"></a>Podpora součásti


| Funkce | Vývoj | Preview | GA | Poznámky |
|-------------------------------------------|-------------|---------|----|-------|
| Azure Multi Factor Authentication |  | x |  |  |
| Azure Active Directory jako místní adresář |  | x |  |  |
| Azure subsystém e-mailu pro 2FA |  | x |  |  |
| Podpora více jazyků|  | x |  |  |
| Složitost hesla | x |  |  |  |


### <a name="content-definition"></a>Definice obsahu

| Funkce | Vývoj | Preview | GA | Poznámky |
|-----------------------------------------------------------------------------|-------------|---------|----|-------|
|   Chybové stránky, api.error |  | x |  |  |
|   Stránka Výběr IDP, api.idpselections |  | x |  |  |
|   Výběr poskytovatelů identity pro registraci, api.idpselections.signup |  | x |  |  |
|   Zapomněli jste heslo, api.localaccountpasswordreset |  | x |  |  |
|   Místní účet přihlášení, api.localaccountsignin |  | x |  |  |
|   Registrace, api.localaccountsignup místní účet |  | x |  |  |
|   Stránka vícefaktorového ověřování, api.phonefactor |  | x |  |  |
|   Samoobslužné prohlašovanou – například api.selfasserted sig-up, sociálních účtu |  | x |  |  |
|   Samoobslužné prohlašovanou profil aktualizace api.selfasserted.profileupdate |  | x |  |  |
|   Jednotná registrace nebo přihlášení, api.signuporsignin |  | x |  |  |


### <a name="app-ief-integration"></a>Integrace aplikace IEF
| Funkce | Vývoj | Preview | GA | Poznámky |
|--------------------------------------------------|-------------|---------|----|-------------------------------------------------|
| Id_token_hint parametr řetězce dotazu | x |  |  |  |
| Domain_hint parametr řetězce dotazu |  | x |  | k dispozici jako deklarace identity, se dá předat do deklarací identity |
| Login_hint parametr řetězce dotazu |  | x |  | k dispozici jako deklarace identity, se dá předat do deklarací identity |
| Vložit JSON do UserJourney prostřednictvím client_assertion | x |  |  | přestanou |
| Vložit JSON do UserJourney jako id_token_hint | x |  |  | Přejděte do dalšího přístup k předávání JSON |


### <a name="session-management"></a>Správa relací

| Funkce | Vývoj | Preview | GA | Poznámky |
|---------------------------------|-------------|---------|----|-------|
| Zprostředkovatel relace jednotného přihlašování |  | x |  |  |
| Zprostředkovatel relací externí přihlášení |  | x |  |  |
| Zprostředkovatel relace jednotného přihlašování SAML |  | x |  |  |


### <a name="security"></a>Zabezpečení
| Funkce | Vývoj | Preview | GA | Poznámky |
|---------------------------------------------|-------------|---------|----|-------|
| Zásady klíče generování, ruční odeslání |  | x |  |  |
| Zásady klíče - RSA/Cert tajné klíče |  | x |  |  |


### <a name="developer-interface"></a>Rozhraní pro vývojáře
| Funkce | Vývoj | Preview | GA | Poznámky |
|---------------------------------------------|-------------|---------|----|-------|
| Azure Portal IEF UX |  | x |  |  |
| Application Insights UserJourney protokoly  |  | x |  |  |
| Protokoly událostí Application Insights |x|  |  |  |



## <a name="next-steps"></a>Další postup
[Začínáme s vlastními zásadami](active-directory-b2c-get-started-custom.md).
