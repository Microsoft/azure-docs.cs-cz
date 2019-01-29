---
title: Poznámky pro vývojáře o použití vlastních zásad v Azure Active Directory B2C | Dokumentace Microsoftu
description: Poznámky pro vývojáře na konfiguraci a správu Azure AD B2C s vlastními zásadami.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 10/13/2017
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 0a5255974c7399f9307d8b06a58f4f977be89829
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55172880"
---
# <a name="release-notes-for-azure-active-directory-b2c-custom-policy-public-preview"></a>Zpráva k vydání verze pro verzi public preview pro Azure Active Directory B2C vlastních zásad
Funkce sadu vlastních zásad je nyní k dispozici pro vyhodnocování v rámci verze public preview pro všechny Azure Active Directory B2C (Azure AD B2C) zákazníkům. Tato sada funkcí je cílena na vývojáře identity pokročilé vytváření nejsložitější řešení identit.  

Tato sada funkcí v současné době vyžaduje vývojářům konfigurovat architekturu rozhraní identit přímo pomocí úpravy souboru XML. Tato metoda konfigurace je výkonná a komplexní. Pokročilé identity vývojáře, kteří používají architekturu rozhraní identit měli naplánovat investovat nějaký čas dokončení kurzy a referenční dokumenty pro čtení. 

## <a name="features-included-in-this-public-preview"></a>Funkce obsažené v této verzi public preview
S novými funkcemi zavedené ve verzi public preview vývojáři mohou provádět následující úlohy:<br>

* Autor a nahrát vlastní ověřování uživatele jízdy pomocí vlastních zásad. 
   * Popis cesty uživatele krok za krokem jako výměny mezi zprostředkovatelem deklarací identity. 
   * Definuje podmíněné větvení v centrech uživatele. * Integrace službám rozhraní REST API v centrech vaše vlastní ověřování uživatele.  
* Přidání federace pomocí zprostředkovatelů identity, které jsou kompatibilní se standardní OpenIDConnect. <br>
* Přidání federace pomocí zprostředkovatelů identity, které používají protokol SAML 2.0. 

## <a name="terms-of-the-public-preview"></a>Podmínky verze preview pro veřejnost

* Doporučujeme vám používat nové funkce jenom pro účely vyhodnocení.<br>
* Nové funkce nejsou určeny pro použití v produkčním prostředí.<br>
* Smlouvy o úrovni služeb (SLA) se nevztahují na nové funkce. <br>
* Žádosti o podporu může zaznamenaná prostřednictvím kanálů podpory regulárních. <br>
* Není žádné datum přislíbeném pro obecnou dostupnost.<br>
* Podle našeho uvážení a z jakéhokoli důvodu může Microsoft příznak a odmítnout nebo zakázání scénáře a uživatel cesty, které přesahují rozsah gossmanem produktu Azure AD B2C sloužit jako zákazník identit a přístupu (ciam od) platforma pro správu.

## <a name="responsibilities-of-custom-policy-feature-set-developers"></a>Odpovědnosti vývojáře vlastních zásad pro sadu funkcí
Konfigurace zásad ruční uděluje přístup k nižší úrovně k základní platformě Azure AD B2C a má za následek vytvoření framework jedinečný plně přizpůsobitelná vztah důvěryhodnosti. Možná bude připadat z vlastních poskytovatelů identit, vztahy důvěryhodnosti, integraci s externími službami a podrobné pracovní postupy umístit větší nároky na pokročilé vývojáře jejich použitím.

Pokud chcete naplno využívat verze preview pro veřejnost, doporučujeme, aby vývojáři využívání funkce sadu vlastních zásad dodržovat následující pokyny:
* Seznámení s jazykem konfigurace architekturu rozhraní identit a řízení klíči nebo tajnými kódy.
* Převzít vlastnictví scénáře a vlastní integraci.
* Proveďte testování metodický scénář.
* Postupujte podle vývoj softwaru a přípravu, osvědčené postupy s minimálně jeden vývojové a testovací prostředí a jeden produkčního prostředí.
* Udržujte si přehled o novém vývoji ze zprostředkovatelů identity a služby, které můžete integrovat s. Například sledovat, změny v tajných kódů a z plánovaných a neplánovaných změny ve službě.
* Nastavit aktivní monitorování a monitorovat rychlost reakce produkční prostředí.
* Udržujte si kontaktní e-mailové adresy v rámci předplatného Azure a aby byla schopná reagovat na e-mailů týmu živého webu společnosti Microsoft.
* Proveďte aktuální akci při doporučujeme to tak, že tým živého webu společnosti Microsoft. 

## <a name="features-by-stage-and-known-issues"></a>Funkce podle fáze a známé problémy
Vlastní architekturu rozhraní identit zásad možnosti jsou ve vývoji konstantní a rychlé.  Tato tabulka je index dostupnost funkce a součásti.

Ptejte se ve na webu Stack Overflow [https://aka.ms/aadb2cso](https://aka.ms/aadb2cso)


### <a name="identity-providers-tokens-protocols"></a>Poskytovatelé tokenů, protokoly identity
Rozhraní s externí komponenty a aplikace

| Funkce | Vývoj | Preview | GA | Poznámky |
|---------------------------------------------|-------------|---------|----|-------|
| IDP-OpenIDConnect |  | x |  | například Google + |
| IDP-OAUTH2 |  | x |  | například Facebook  |
| IDP-OAUTH1 |  | x |  | například na Twitteru |
| IDP-SAML |  | x |  | například Salesforce, služby AD FS |
| IDP-WSFED | x |  |  |  |
| Předávající strany OAUTH |  | x |  |  |
| Předávající strany OIDC |  | x |  |  |
| Přijímající strany SAML | x |  |  |  |
| Předávající strany WSFED | x |  |  |  |
| Rozhraní REST API pomocí ověřování basic a certifikát |  | x |  | například Azure Functions |


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
|   Stránka výběru zprostředkovatele identity, api.idpselections |  | x |  |  |
|   Výběr zprostředkovatele identity pro registraci, api.idpselections.signup |  | x |  |  |
|   Zapomněli jste heslo, api.localaccountpasswordreset |  | x |  |  |
|   Místní účet přihlášení, api.localaccountsignin |  | x |  |  |
|   Místní účet pro zápis, api.localaccountsignup |  | x |  |  |
|   Stránka vícefaktorového ověřování, api.phonefactor |  | x |  |  |
|   Samoobslužné s prohlašovanou – například registrace, api.selfasserted účtu na sociální síti |  | x |  |  |
|   Samoobslužné s prohlašovanou aktualizace profilu api.selfasserted.profileupdate |  | x |  |  |
|   Sjednocené registrace nebo přihlašovací stránku, api.signuporsignin |  | x |  |  |


### <a name="app-ief-integration"></a>Integrace aplikace IEF
| Funkce | Vývoj | Preview | GA | Poznámky |
|--------------------------------------------------|-------------|---------|----|-------------------------------------------------|
| Id_token_hint parametr řetězce dotazu | x |  |  |  |
| Domain_hint parametr řetězce dotazu |  | x |  | k dispozici jako deklarace identity, může být předán zprostředkovatel identity |
| Login_hint parametr řetězce dotazu |  | x |  | k dispozici jako deklarace identity, může být předán zprostředkovatel identity |
| Příkaz Insert JSON UserJourney prostřednictvím client_assertion | x |  |  | se přestanou používat |
| Vložit JSON do UserJourney jako id_token_hint | x |  |  | Přejít vpřed přístup k předání JSON |


### <a name="session-management"></a>Správa relací

| Funkce | Vývoj | Preview | GA | Poznámky |
|---------------------------------|-------------|---------|----|-------|
| Poskytovatel relace jednotného přihlašování |  | x |  |  |
| Zprostředkovatel relací externí přihlášení |  | x |  |  |
| Zprostředkovatel relací SAML SSO |  | x |  |  |


### <a name="security"></a>Zabezpečení
| Funkce | Vývoj | Preview | GA | Poznámky |
|---------------------------------------------|-------------|---------|----|-------|
| Zásady klíče generovat, manuální, nahrávání |  | x |  |  |
| Zásady klíče - RSA/Cert tajných kódů |  | x |  |  |


### <a name="developer-interface"></a>Rozhraní pro vývojáře
| Funkce | Vývoj | Preview | GA | Poznámky |
|---------------------------------------------|-------------|---------|----|-------|
| Azure Portal IEF uživatelského prostředí |  | x |  |  |
| Application Insights UserJourney Logs  |  | x |  |  |
| Protokoly událostí Application Insights |x|  |  |  |



## <a name="next-steps"></a>Další postup
[Začínáme s vlastními zásadami](active-directory-b2c-get-started-custom.md).
