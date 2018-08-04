---
title: Scénáře ověřování pro službu Azure AD | Dokumentace Microsoftu
description: Poskytuje přehled o pět nejběžnější scénáře ověřování pro Azure Active Directory (Azure AD)
services: active-directory
documentationcenter: dev-center-name
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 0c84e7d0-16aa-4897-82f2-f53c6c990fd9
ms.service: active-directory
ms.component: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/26/2018
ms.author: celested
ms.reviewer: jmprieur, andret, nacanuma, hirsin
ms.custom: aaddev
ms.openlocfilehash: c6429a6fb762e9eb2bac59788c5c4eb0f9c031b2
ms.sourcegitcommit: 9222063a6a44d4414720560a1265ee935c73f49e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/03/2018
ms.locfileid: "39505480"
---
# <a name="authentication-scenarios-for-azure-ad"></a>Scénáře ověřování pro službu Azure AD

Azure Active Directory (Azure AD) zjednodušuje tím, že poskytuje identitu jako služba s podpora standardních oborových protokolů, jako jsou OAuth 2.0 a OpenID Connect, stejně jako open source knihoven pro různé platformy, abychom vám ověřování pro vývojáře Pusťte se do programování rychle. Tento článek vám pomůže pochopit různé scénáře služby Azure AD podporuje a ukážeme vám, jak začít pracovat. Je rozdělený do následujících částí:

* [Základní informace o ověřování ve službě Azure AD](#basics-of-authentication-in-azure-ad)
* [Deklarace identity do tokenů zabezpečení Azure AD](#claims-in-azure-ad-security-tokens)
* [Základní informace o registraci aplikace ve službě Azure AD](#basics-of-registering-an-application-in-azure-ad)
* [Typy aplikací a scénářů](#application-types-and-scenarios)

  * [Webový prohlížeč na webovou aplikaci](#web-browser-to-web-application)
  * [Jednostránkové aplikace (SPA)](#single-page-application-spa)
  * [Nativní aplikace pro webové rozhraní API](#native-application-to-web-api)
  * [Webové aplikace k webovému rozhraní API](#web-application-to-web-api)
  * [Proces démon nebo server aplikace webového rozhraní API](#daemon-or-server-application-to-web-api)

## <a name="basics-of-authentication-in-azure-ad"></a>Základní informace o ověřování ve službě Azure AD

Pokud nejste obeznámeni se základními koncepty ověřování ve službě Azure AD, v tomto tématu. V opačném případě můžete chtít přejděte dolů k [typy aplikací a scénářů](#application-types-and-scenarios).

Uvažujme základní scénáře, ve kterém jsou vyžadována identita: uživatel ve webovém prohlížeči musí ověřit do webové aplikace. Tento scénář je popsaný v podrobněji [webového prohlížeče a webové aplikace](#web-browser-to-web-application) oddílu, ale je vytvoření užitečného počátečního bodu pohodlným fungování scénáře a ukazují možnosti služby Azure AD. Vezměte v úvahu následující diagram pro tento scénář:

![Přehled přihlašování do webové aplikace](./media/authentication-scenarios/basics_of_auth_in_aad.png)

S diagramem výše v úvahu zde je, co potřebujete vědět o jeho různé součásti:

* Azure AD je poskytovatele identity, který je zodpovědný za ověření identity uživatelů a aplikací, které existují v adresáři vaší organizace a nakonec vystavování tokenů zabezpečení po úspěšném ověření těchto uživatelů a aplikací.
* Aplikace, která chce externí ověřování do služby Azure AD musí být zaregistrovaný ve službě Azure AD, která registruje a jednoznačně identifikuje aplikaci v adresáři.
* Mohou vývojáři knihovny ověřování služby Azure AD open-source usnadňují ověřování pomocí zpracování podrobnosti protokolu za vás. Další informace najdete v tématu [knihovny Azure Active Directory Authentication](active-directory-authentication-libraries.md).
* Po ověření uživatele, aplikace musí ověřit token zabezpečení uživatele chcete zajistit, že ověřování bylo úspěšné. Nabízíme ukázky, nástroje co aplikace musíte udělat v různých jazycích a architekturách na [Githubu](https://github.com/Azure-Samples?q=active-directory). Pokud vytváříte webovou aplikaci v ASP.NET, najdete v článku [přidání přihlašování pro je Průvodce webové aplikace ASP.NET](https://docs.microsoft.com/en-us/azure/active-directory/develop/guidedsetups/active-directory-aspnetwebapp). Pokud vytváříte prostředek webového rozhraní API v ASP.NET, najdete v článku [webového rozhraní API – Příručka Začínáme](https://docs.microsoft.com/en-us/azure/active-directory/develop/active-directory-devquickstarts-webapi-dotnet).
* Tok požadavků a odpovědí pro proces ověřování se určuje podle ověřovací protokol, který se použil, jako jsou OAuth 2.0, OpenID Connect, WS-Federation, SAML 2.0 nebo. Tyto protokoly jsou popsány podrobněji [protokoly pro ověřování Azure Active Directory](active-directory-authentication-protocols.md) článku a v následujících částech.

> [!NOTE]
> Azure AD podporuje OAuth 2.0 a OpenID Connect standardy, které usnadňují rozsáhlé pomocí nosných tokenů, včetně nosné tokeny, které jsou reprezentovány jako tokeny Jwt. A *nosný token* je zjednodušené bezpečnostní token, který uděluje "nosiče" přístup k chráněnému prostředku. V tomto smyslu "nosiče" je každá strana, která může představovat token. Když strana musí nejdřív ověřit s Azure AD pro příjem nosný token, pokud nejsou požadované kroky k zabezpečení token v přenos a ukládání, můžete zachytit a používat nežádoucí osobou. I když některé tokeny zabezpečení má předdefinovaný mechanismus pro brání jejich používání neoprávněnými osobami, nosné tokeny nemají tento mechanismus a nutné přenášet v zabezpečený kanál, jako je zabezpečení transportní vrstvy (HTTPS). Pokud nosný token je přenesen v nezašifrované podobě, lze škodlivý stranou útok man-in-the-middle získat token a používat ho pro neoprávněný přístup k chráněnému prostředku. Stejné zásady zabezpečení platí při ukládání nebo ukládání do mezipaměti nosné tokeny pro pozdější použití. Vždy zajistěte, aby vaše aplikace odesílá a ukládá nosné tokeny bezpečným způsobem. Další informace o zabezpečení na nosné tokeny, naleznete v tématu [5 část dokumentu RFC 6750](http://tools.ietf.org/html/rfc6750).

Teď, když máte přehled o základní informace, přečtěte si následující části, abyste pochopit, jak zřizování funguje ve službě Azure AD a běžné scénáře, které podporuje Azure AD.

## <a name="claims-in-azure-ad-security-tokens"></a>Deklarace identity do tokenů zabezpečení Azure AD

Tokeny zabezpečení (přístup a ID tokeny) vydané službou Azure AD obsahují deklarace identity nebo kontrolní výrazy s informací o subjektu, který byl ověřen. Aplikace pro různé úkoly, je možné tyto deklarace. Aplikace může například používat deklarace identity token ověří, zjišťovat předmětu adresář tenanta, zobrazit informace o uživateli, autorizaci předmětu a tak dále. K dispozici v žádný token zabezpečení deklarace identity jsou závislé na typ tokenu, typ přihlašovacích údajů pro ověření uživatele a konfiguraci aplikace. Stručný popis každého typu deklarace identity, protože ho vygeneroval Azure AD najdete v následující tabulce. Další informace najdete v [podporované typy tokenů a deklarací identity](active-directory-token-and-claims.md).

| Deklarovat | Popis |
| --- | --- |
| ID aplikace | Určuje aplikace, která používá token. |
| Cílová skupina | Identifikuje příjemce token je určená pro prostředek. |
| Application Authentication Context Class Reference | Určuje, jak byl klient ověřeného (veřejným klientem vs. důvěrnému klientovi). |
| Ověřování rychlých zpráv | Zaznamenává data a času, kdy došlo k ověření. |
| Metoda ověřování | Určuje, jak byl předmět tokenu ověření (hesla, certifikát, atd.). |
| Jméno | Poskytuje křestní jméno uživatele, jak ve službě Azure AD. |
| Skupiny | Obsahuje ID služby Azure AD skupiny objektů, kterých je uživatel členem. |
| Zprostředkovatel identity | Zaznamenává zprostředkovatele identity, která ověřena předmětem token. |
| Vystaveno | Zaznamená čas, kdy byl vydán token, často používají pro token aktuálnosti. |
| Vystavitel | Identifikuje službu STS, které emitovány token, jakož i tenanta Azure AD. |
| Příjmení | Poskytuje příjmení uživatele, jak ve službě Azure AD. |
| Název | Poskytuje lidské čitelné hodnotu, která identifikuje předmětem token. |
| ID objektu | Obsahuje neměnné a jedinečný identifikátor předmětu ve službě Azure AD. |
| Role | Obsahuje popisné názvy aplikací role služby Azure AD, která uživateli byl udělen. |
| Rozsah | Určuje oprávnění udělená do klientské aplikace. |
| Předmět | Určuje objekt o tom, které token vyhodnocuje informace. |
| ID tenanta | Obsahuje neměnné jedinečný identifikátor tenantu Active directory, která token vydala. |
| Životnost tokenu | Definuje časový interval, ve kterém je token platný. |
| Hlavní název uživatele (UPN) | Obsahuje hlavní název uživatele pro předmět. |
| Verze | Obsahuje číslo verze tokenu. |

## <a name="basics-of-registering-an-application-in-azure-ad"></a>Základní informace o registraci aplikace ve službě Azure AD

Všechny aplikace, které outsources ověřování do služby Azure AD musí být zaregistrovaný v adresáři. Tento krok zahrnuje informacemi o vaší aplikaci, včetně adresy URL, kde je umístěn, adresa URL pro odeslání odpovědi po ověření, identifikátor URI k identifikaci vaší aplikace a další služby Azure AD. Tyto informace jsou nezbytné pro několik zásadních důvodů:

* Služba Azure AD potřebuje ke komunikaci s aplikací při zpracování přihlašování nebo výměnou tokenů. Předávat mezi službami Azure AD a aplikace zahrnují následující:
  
  * **Identifikátor URI ID aplikace** -identifikátor pro aplikaci. Tato hodnota posílá do služby Azure AD během ověřování k označení, kterou aplikaci volající požaduje token. Kromě toho tato hodnota zahrnuje v tokenu, aby aplikace věděla, že byla zamýšleným cílem.
  * **Adresa URL pro odpověď** a **identifikátor URI pro přesměrování** – pro webové rozhraní API nebo webovou aplikaci, adresa URL odpovědi je umístění, kam Azure AD pošle odpověď ověřování, včetně token, pokud je ověření proběhlo úspěšně. Pro nativní aplikace identifikátor URI pro přesměrování je jedinečný identifikátor, na který Azure AD přesměruje uživatelského agenta v požadavku OAuth 2.0.
  * **ID aplikace** – ID aplikace, který je generován při registraci aplikace Azure AD. Pokud se požaduje token nebo autorizační kód, ID aplikace a klíč se odesílají do služby Azure AD během ověřování.
  * **Klíč** – klíč, který se zasílá společně s ID aplikace při ověřování do služby Azure AD k volání webového rozhraní API.
* Azure AD je potřeba zajistit, že aplikace má požadovaná oprávnění pro přístup k datům adresáře, jiné aplikace ve vaší organizaci a tak dále.

Zřizování se změní volbu, pokud víte, že existují dvě kategorie aplikací, které mohou být vyvinuté a integrovat se službou Azure AD:

* **Jednoduché aplikace s tenanty** -jednoho tenanta aplikaci je určena pro použití v jedné organizaci. Toto jsou obvykle-obchodní (LoB) aplikace napsané tak podnikový vývojář. Jednoho tenanta aplikaci pouze musí mít přístup uživatelé v jednom adresáři, a proto potřebuje pouze zřídit v jednom adresáři. Tyto aplikace jsou běžně registrovány vývojáři, kteří v organizaci.
* **Aplikace s více tenanty** – aplikace s více tenanty určena pro použití v mnoha organizacích, ne jenom jedné organizace. Toto jsou obvykle software-as-a-service (SaaS) aplikací autorem nezávislý výrobce softwaru (ISV). Aplikace s více tenanty potřeba ho zřídit v každém adresáři, kde se využijí, který vyžaduje souhlas správce nebo uživatele k registraci je. Tento proces souhlas začne, když aplikace byl zaregistrován v adresáři a je jim přístup k rozhraní Graph API nebo možná jiného webového rozhraní API. Pokud uživatel nebo správce z jiné organizace zaregistruje k používání aplikace, zobrazí se dialogové okno se zobrazí oprávnění, která aplikace požaduje instalaci. Uživatel nebo správce můžete pak souhlas s aplikací, které poskytuje přístup k aplikaci do uvedeného data a nakonec zaregistruje aplikaci do svého adresáře. Další informace najdete v tématu [přehled architektury souhlas](quickstart-v1-integrate-apps-with-azure-ad.md#overview-of-the-consent-framework).

### <a name="additional-considerations-when-developing-single-tenant-or-multi-tenant-apps"></a>Další aspekty při vývoji jeden tenant nebo víceklientské aplikace
Několik dalších důležitých informací ke kterým dochází při vývoji aplikace s více tenanty místo jednoho tenanta aplikaci. Například pokud vaše aplikace jsou zpřístupníte uživatelům v různých adresářích, je třeba mechanismus pro určení kterého tenanta ve kterých se nacházejí. Jednoho tenanta aplikace potřebuje pouze hledat v jeho vlastní adresáře pro uživatele, zatímco aplikace s více tenanty je potřeba určit konkrétní uživatele ze všech adresářů ve službě Azure AD. Chcete-li provést tuto úlohu, Azure AD poskytuje společný koncový bod ověřování kde jakékoli aplikace s více tenanty může směrovat požadavky na přihlášení, místo konkrétního klienta endpoint. Tento koncový bod je https://login.microsoftonline.com/common pro všechny adresáře ve službě Azure AD, že koncový bod specifickým pro tenanta může být https://login.microsoftonline.com/contoso.onmicrosoft.com. Společný koncový bod je obzvláště důležité vzít v úvahu při vývoji vaší aplikace, protože je budete potřebovat logiku potřebnou pro zpracování více tenantů během přihlášení, odhlášení a ověření tokenu.

Pokud jsou aktuálně vývoj aplikace s jedním tenantem, ale mají být k dispozici pro mnoho organizací, můžete snadno provedete změny aplikace a jeho konfigurace ve službě Azure AD, aby více tenantů podporuje. Kromě toho Azure AD používá stejný podpisový klíč pro všechny tokeny ve všech adresářích, jestli poskytujete ověřování v jednom tenantovi nebo aplikace s více tenanty.

Každý scénář v tomto dokumentu obsahuje dílčí část, která popisuje požadavky na jeho zřizování. Podrobnější informace o zřizování aplikace ve službě Azure AD a jaký je rozdíl mezi aplikací jeden a více tenantů, naleznete v tématu [integrace aplikací s Azure Active Directory](quickstart-v1-integrate-apps-with-azure-ad.md) Další informace. Pokračujte ve čtení o běžných scénářů aplikací ve službě Azure AD.

## <a name="application-types-and-scenarios"></a>Typy aplikací a scénářů

Jednotlivé scénáře popsané v tomto poli může být vyvinuto s použitím různé jazyky a platformy. Tyto jsou všechno se opírá kompletní kód ukázky, které jsou k dispozici v [příručka k ukázkám kódu](active-directory-code-samples.md), nebo přímo z odpovídající [ukázkové úložiště GitHub](https://github.com/Azure-Samples?q=active-directory). Kromě toho zda vaše aplikace potřebuje konkrétní nebo segment začátku do konce scénáře, ve většině případů, které tuto funkci lze přidat nezávisle na sobě. Například pokud máte nativní aplikaci, která volá webové rozhraní API, můžete snadno přidat webovou aplikaci, která volá webové rozhraní API také. Následující diagram znázorňuje tyto scénáře a typy aplikací, a jak je možné přidat různé součásti:

![Typy aplikací a scénářů](./media/authentication-scenarios/application_types_and_scenarios.png)

Toto jsou pěti primární aplikace scénáře podporované službou Azure AD:

* [Webový prohlížeč na webovou aplikaci](#web-browser-to-web-application): uživatel potřebuje pro přihlášení k webové aplikaci, která je zabezpečena pomocí služby Azure AD.
* [Jedna stránka aplikace (SPA)](#single-page-application-spa): uživatel potřebuje přihlásit jednostránkové aplikaci, která je zabezpečena pomocí služby Azure AD.
* [Nativní aplikace pro webové rozhraní API](#native-application-to-web-api): nativní aplikaci, která běží na telefonu, tabletu nebo počítači potřebuje ověřit uživatele k získání zdroje z webového rozhraní API, která je zabezpečena pomocí služby Azure AD.
* [Webové aplikace k webovému rozhraní API](#web-application-to-web-api): webová aplikace je potřeba získat prostředky z webového rozhraní API zabezpečené pomocí Azure AD.
* [Proces démon nebo server aplikace webového rozhraní API](#daemon-or-server-application-to-web-api): aplikace proces démon nebo serverovou aplikaci s žádné webové uživatelské rozhraní je potřeba získat prostředky z webového rozhraní API zabezpečené pomocí Azure AD.

### <a name="web-browser-to-web-application"></a>Webový prohlížeč na webovou aplikaci

Tato část popisuje aplikace, která ověřuje uživatele ve webovém prohlížeči na webovou aplikaci. V tomto scénáři webová aplikace přesměruje prohlížeč uživatele pro jejich přihlášení k Azure AD. Azure AD vrátí odpověď přihlášení prostřednictvím webového prohlížeče, který obsahuje deklarace identity o uživateli v tokenu zabezpečení. Tento scénář podporuje přihlašování pomocí protokolů WS-Federation, SAML 2.0 a OpenID Connect.

#### <a name="diagram"></a>Diagram

![Tok ověření pro prohlížeče a webové aplikace](./media/authentication-scenarios/web_browser_to_web_api.png)

#### <a name="description-of-protocol-flow"></a>Popis protokolu toku

1. Když uživatel navštíví aplikace a potřebuje přihlásit, bude přesměrován prostřednictvím přihlašovací požadavek na koncový bod ověřování ve službě Azure AD.
1. Uživatel se přihlásí na přihlašovací stránku.
1. Pokud je ověření úspěšné, Azure AD vytvoří ověřovacího tokenu a vrátí odpověď přihlašovací adresa URL odpovědi aplikace, který byl nakonfigurován na webu Azure Portal. Pro produkční aplikace tato adresa URL odpovědi by mělo být HTTPS. Vrácený token obsahuje deklarace identity o uživateli a Azure AD, které jsou vyžadované aplikací k ověření tokenu.
1. Aplikace ověří daný token pomocí veřejného podpisového klíče a vydavatele informace, které jsou k dispozici v dokumentu federačních metadat pro službu Azure AD. Když aplikace ověří token, spustí novou relaci s uživatelem. Tato relace povolí uživateli přístup k aplikaci do vypršení jeho platnosti.

#### <a name="code-samples"></a>Ukázky kódů

Najdete v ukázkách kódu pro webový prohlížeč na scénáře webových aplikací. A, vraťte se často – jsou přidávány nové ukázky. [Webová aplikace](active-directory-code-samples.md#web-applications).

#### <a name="registering"></a>Registrace

* Jedním Tenantem: Pokud vytváříte aplikaci pouze pro vaši organizaci, to musí být zaregistrovaný v adresáři vaší společnosti pomocí webu Azure portal.
* Víceklientské: Pokud vytváříte aplikaci, která je možné uživatele mimo vaši organizaci, to musí být zaregistrovaný v adresáři vaší společnosti, ale také musí být zaregistrovaný v adresáři každé organizace, který bude používat aplikace. K zajištění dostupnosti v jejich adresáři aplikace, můžete zahrnout procesu registrace pro vaše zákazníky, díky kterým souhlas s vaší aplikací. Při registraci pro vaši aplikaci, se zobrazí se dialogové okno zobrazující oprávnění, která aplikace požaduje instalaci a pak možnost vyjádřit souhlas. V závislosti na požadovaných oprávnění může být správcem v druhé organizaci nutné vyjádřit souhlas. Když uživatel nebo správce souhlasí, aplikace je zaregistrovaný v jejich adresáře. Další informace najdete v tématu [integrace aplikací s Azure Active Directory](quickstart-v1-integrate-apps-with-azure-ad.md).

#### <a name="token-expiration"></a>Vypršení platnosti tokenu

Relace uživatele vyprší po vypršení platnosti tokenu vydaného službou Azure AD. Aplikace můžete zkrátit toto časové období, v případě potřeby, jako je například odhlašování uživatelů podle určité době nečinnosti. Po vypršení platnosti relace, uživatel se vyzve se znovu přihlásit.

### <a name="single-page-application-spa"></a>Jednostránkové aplikace (SPA)

Tato část popisuje ověřování pro jednu aplikaci stránky, která používá Azure AD a implicitní autorizace OAuth 2.0 udělit zabezpečit své webové rozhraní API zpět ukončit. Jednostránkové aplikace jsou obvykle strukturované jako JavaScript prezentační vrstvy (front-end), která běží v prohlížeči a webového rozhraní API back-end, který běží na serveru a implementuje obchodní logiku aplikace. Další informace o implicitním udělení autorizace a vám pomohou rozhodnout, jestli je vhodná pro váš scénář aplikace najdete v tématu [Principy OAuth2 implicitní tok ve službě Azure Active Directory udělit](active-directory-dev-understanding-oauth2-implicit-grant.md).

V tomto scénáři, když se uživatel přihlásí, JavaScript front-end využívá [Active Directory Authentication Library pro JavaScript (ADAL. JS)](https://github.com/AzureAD/azure-activedirectory-library-for-js) a implicitním udělení autorizace k získání tokenu ID (id_token) ze služby Azure AD. Token se uloží do mezipaměti a klient připojí ho k požadavku jako nosný token při volání do své webové rozhraní API back-end, který je zabezpečený pomocí middlewaru OWIN. 

#### <a name="diagram"></a>Diagram

![Diagram jedné stránky aplikace](./media/authentication-scenarios/single_page_app.png)

#### <a name="description-of-protocol-flow"></a>Popis protokolu toku

1. Uživatel přejde na webovou aplikaci.
1. Aplikace vrátí front-endu JavaScriptu (prezentační vrstva) do prohlížeče.
1. Uživatel inicializuje přihlášení, například klepnutím na odkaz přihlášení. Prohlížeč odesílá GET na koncový bod autorizace Azure AD k vyžádání tokenu ID. Tato žádost obsahuje adresu URL pro ID a odpověď aplikace v parametrech dotazu.
1. Azure AD ověří příslušnou odpovědní adresu URL vůči registrovaná adresa URL odpovědi, který byl nakonfigurován na webu Azure Portal.
1. Uživatel se přihlásí na přihlašovací stránku.
1. Pokud je ověření úspěšné, Azure AD vytvoří ID token a vrátí ji do adresy URL odpovědi aplikace jako fragment adresy URL (#). Pro produkční aplikace tato adresa URL odpovědi by mělo být HTTPS. Vrácený token obsahuje deklarace identity o uživateli a Azure AD, které jsou vyžadované aplikací k ověření tokenu.
1. Kód jazyka JavaScript klienta, spouštění v prohlížeči extrahuje token z odpovědi používané k zabezpečení volání aplikace webového že rozhraní API zpět ukončit.
1. Prohlížeč volání aplikace webového rozhraní API zpět končit ID tokenu v autorizační hlavičce. Ověřování služby Azure AD vydá token ID, který může sloužit jako nosný token, pokud prostředek je stejné jako ID klienta (v tomto případě to platí je webové rozhraní API back-endu vaší aplikace vlastní). 

#### <a name="code-samples"></a>Ukázky kódů

Zobrazit ukázky kódu pro scénáře jedné stránky aplikace (SPA). Nezapomeňte se podívat často – jsou přidávány nové ukázky. [Jedna stránka aplikace (SPA)](active-directory-code-samples.md#single-page-applications).

#### <a name="registering"></a>Registrace

* Jedním Tenantem: Pokud vytváříte aplikaci pouze pro vaši organizaci, to musí být zaregistrovaný v adresáři vaší společnosti pomocí webu Azure portal.
* Víceklientské: Pokud vytváříte aplikaci, která je možné uživatele mimo vaši organizaci, to musí být zaregistrovaný v adresáři vaší společnosti, ale také musí být zaregistrovaný v adresáři každé organizace, který bude používat aplikace. K zajištění dostupnosti v jejich adresáři aplikace, můžete zahrnout procesu registrace pro vaše zákazníky, díky kterým souhlas s vaší aplikací. Při registraci pro vaši aplikaci, se zobrazí se dialogové okno zobrazující oprávnění, která aplikace požaduje instalaci a pak možnost vyjádřit souhlas. V závislosti na požadovaných oprávnění může být správcem v druhé organizaci nutné vyjádřit souhlas. Když uživatel nebo správce souhlasí, aplikace je zaregistrovaný v jejich adresáře. Další informace najdete v tématu [integrace aplikací s Azure Active Directory](quickstart-v1-integrate-apps-with-azure-ad.md).

Po registraci aplikace, musí být nakonfigurován pro použití protokolu implicitní Grant OAuth 2.0. Ve výchozím nastavení je tento protokol zakázán pro aplikace. Pokud chcete povolit implicitní Grant OAuth2 protokolu pro vaši aplikaci, úprava manifestu aplikace z portálu Azure portal a nastavte hodnotu "oauth2AllowImplicitFlow" na hodnotu true. Podrobné pokyny najdete v tématu [povolení OAuth 2.0 implicitní Grant pro jednostránkové aplikace](quickstart-v1-integrate-apps-with-azure-ad.md).

#### <a name="token-expiration"></a>Vypršení platnosti tokenu

Použití ADAL.js pomáhá s:

* aktualizaci vypršenou platností tokenu
* žádání o přístupový token k volání webového rozhraní API prostředku

Po úspěšném ověření Azure AD zapisuje do souboru cookie v prohlížeči uživatele k vytvoření relace. Všimněte si, že relace existuje mezi uživatelem a Azure AD (není mezi uživatelem a webové aplikace). Když vyprší platnost tokenu, ADAL.js používá tuto relaci tiše získat nový token. ADAL.js používá skrytý element iFrame pro odesílání a přijímání požadavků pomocí protokolu implicitní Grant OAuth. ADAL.js můžete také použít tento stejný mechanismus tiše získat přístupové tokeny pro prostředky webového rozhraní API, které aplikace volá tak dlouho, dokud prostředků mezi zdroji (CORS) pro sdílení obsahu podporují tyto prostředky jsou registrovány v adresáři uživatele a všechny požadované souhlasu se zadaný uživatelem při přihlášení.

### <a name="native-application-to-web-api"></a>Nativní aplikace pro webové rozhraní API

Tato část popisuje nativní aplikaci, která volá webové rozhraní API jménem uživatele. Tento scénář je založený na typ udělení kódu autorizace OAuth 2.0 s veřejným klientem, jak je popsáno v části 4.1 [specifikaci OAuth 2.0](http://tools.ietf.org/html/rfc6749). Nativní aplikace získá přístupový token pro uživatele s použitím protokolu OAuth 2.0. Tento přístupový token se pak odešle přes požadavek do webového rozhraní API, které opravňují uživatele a vrátí požadovaný prostředek.

#### <a name="diagram"></a>Diagram

![Nativní aplikace přes webové rozhraní API diagramu](./media/authentication-scenarios/native_app_to_web_api.png)

#### <a name="description-of-protocol-flow"></a>Popis protokolu toku

Pokud používáte knihoven ověřování služby AD, většinu níže popsaných podrobností protokolu jsou zpracovány za vás, jako je například automaticky otevírané okno prohlížeče, ukládání tokenu do mezipaměti a manipulace s tokeny obnovení.

1. Pomocí prohlížeče automaticky otevíraném okně že nativní aplikace odešle požadavek na koncový bod autorizace ve službě Azure AD. Tato žádost obsahuje ID aplikace a identifikátor URI nativní aplikace pro přesměrování, jak je znázorněno na webu Azure portal a identifikátor URI ID aplikace pro webové rozhraní API. Pokud uživatel ještě přihlášeni, zobrazí se výzva k akci
1. Azure AD ověřuje uživatele. Pokud je aplikace s více tenanty a souhlas se vyžaduje souhlas s použitím aplikace, uživatel muset vyjádřit souhlas, pokud se tak již neučinili. Po udělení souhlasu a po úspěšném ověření Azure AD vydá kód odpovědi ověřování. zpět do klientské aplikace identifikátor URI přesměrování.
1. Když Azure AD vydá kód odpovědi zpět na identifikátor URI pro přesměrování ověřování, klientská aplikace přestane interakce prohlížeče a extrahuje autorizační kód z odpovědi. Pomocí tohoto kódu autorizace, klientská aplikace odešle požadavek na token koncového bodu Azure AD, který obsahuje autorizační kód, podrobností o aplikaci klienta (ID aplikace a identifikátor URI pro přesměrování) a požadovaný prostředek (identifikátor ID URI aplikace pro webové rozhraní API).
1. Autorizační kód a informace o klientských aplikací a webového rozhraní API jsou ověření pomocí Azure AD. Po úspěšném ověření Azure AD vrací dva tokeny: přístupový token JWT a aktualizační token JWT. Kromě toho Azure AD vrátí základní informace o uživateli, jako je například jejich zobrazovaného názvu a tenanta ID.
1. Přes protokol HTTPS že klientská aplikace používá vrácené přístupový token JWT přidat token JWT řetězec s označením "Nosiče" v hlavičce autorizace požadavku do webového rozhraní API. Webové rozhraní API pak ověří JWT token a pokud je ověření úspěšné, vrátí požadovaný prostředek.
1. Když vyprší platnost přístupového tokenu, klientská aplikace obdrží chybu, která indikuje, že uživatel musí znovu provést ověření. Pokud má aplikace platné obnovovací token, můžete použít k získání nového tokenu přístupu bez výzvy pro uživatele se znovu přihlásit. Pokud vyprší platnost tokenu obnovení, aplikace bude nutné znovu interaktivně ověření uživatele.

> [!NOTE]
> Aktualizace tokenu vydaného službou Azure AD umožňuje přístup k více prostředkům. Například pokud máte klientské aplikace, který má oprávnění k volání dvě webové rozhraní API, obnovovací token slouží k získání přístupového tokenu pro jiné webové rozhraní API také.

#### <a name="code-samples"></a>Ukázky kódů

Pro nativní aplikace pro scénáře webového rozhraní API najdete v ukázkách kódu. A vraťte se často – často přidáváme nové ukázky. [Nativní aplikace pro webové rozhraní API](active-directory-code-samples.md#desktop-and-mobile-public-client-applications-calling-microsoft-graph-or-a-web-api).

#### <a name="registering"></a>Registrace

* Jedním Tenantem: Nativní aplikace a webové rozhraní API musí být zaregistrovaný ve stejném adresáři, ve službě Azure AD. Webové rozhraní API je možné nakonfigurovat k vystavení sady oprávnění, které slouží k omezení přístupu k nativní aplikaci na prostředky. Klientská aplikace potom vybere požadovaná oprávnění z rozevírací nabídky "Oprávnění do jiných aplikací" na webu Azure Portal.
* Víceklientské: Nejprve nativní aplikace vždy jen registrované v vývojář nebo adresář vydavatele. Za druhé nativní aplikace je nakonfigurovaná k označení oprávnění, která ho, aby byl funkční. Tento seznam požadovaných oprávnění se zobrazí v dialogovém okně, když uživatel nebo správce v cílovém adresáři dává souhlas pro aplikaci, které zpřístupní jejich organizace. Některé aplikace vyžadují jenom oprávnění na úrovni uživatele, které každý uživatel v organizaci můžou udělit souhlas s. Jiné aplikace vyžadují oprávnění na úrovni správce, které nelze vyjádřit souhlas uživatele v organizaci. Pouze správce adresáře lze udělit souhlas aplikacím, které vyžadují tuto úroveň oprávnění. Když uživatel nebo správce souhlasí, pouze webová rozhraní API je zaregistrovaný v jejich adresáře. Další informace najdete v tématu [integrace aplikací s Azure Active Directory](quickstart-v1-integrate-apps-with-azure-ad.md).

#### <a name="token-expiration"></a>Vypršení platnosti tokenu

Když nativní aplikace používá jeho autorizační kód k získání tokenu JWT přístupu, také obdrží obnovovací token JWT. Když vyprší platnost přístupového tokenu, token obnovení je možné opakované ověření uživatele bez nutnosti jejich nutnost znovu přihlásit. Tento aktualizační token se pak použije k ověření uživatele, jehož výsledkem nový přístupový token a aktualizační token.

### <a name="web-application-to-web-api"></a>Webové aplikace k webovému rozhraní API

Tato část popisuje webovou aplikaci, kterou je potřeba získat prostředky z webového rozhraní API. V tomto scénáři existují dva typy identit, které webové aplikace můžete použít k ověření a zavolání webového rozhraní API: Identita aplikace nebo delegovaný uživatel identity.

*Identita aplikace:* tento scénář využívá udělování přihlašovacích údajů klienta OAuth 2.0 k ověření jako aplikace a přístup k webovému rozhraní API. Při použití identita aplikace, webové rozhraní API může jenom zjistit, že webová aplikace je volat, jak se webové rozhraní API nepřijímá žádné informace o uživateli. Pokud aplikace obdrží informace o uživateli, se pošle přes protokoly aplikací a není podepsán pomocí Azure AD. Webové rozhraní API důvěřuje, že webová aplikace ověřil daného uživatele. Z tohoto důvodu tento model se nazývá důvěryhodný subsystém.

*Delegovaná identita uživatele:* tento scénář lze provést dvěma způsoby: OpenID Connect a udělení autorizačního kódu OAuth 2.0 s důvěrnému klientovi. Webová aplikace získá přístupový token pro uživatele, která prokáže vaše oprávnění k webovému rozhraní API, které se uživatel úspěšně ověřen na webovou aplikaci a které webové aplikace se podařilo získat identitu delegovaný uživatel k volání webového rozhraní API. Tento přístupový token se posílá ve požadavek do webového rozhraní API, které opravňují uživatele a vrátí požadovaný prostředek.

#### <a name="diagram"></a>Diagram

![Webové aplikace do diagramu webového rozhraní API](./media/authentication-scenarios/web_app_to_web_api.png)

#### <a name="description-of-protocol-flow"></a>Popis protokolu toku

Identita aplikace i delegovaný uživatel identity typy jsou popsány v níže toku. Klíčovým rozdílem mezi nimi je, že delegovaný uživatel identity musíte nejprve získat autorizační kód předtím, než uživatel může přihlásit a získat přístup k webovému rozhraní API.

##### <a name="application-identity-with-oauth-20-client-credentials-grant"></a>Udělit aplikaci identity pomocí přihlašovacích údajů klienta OAuth 2.0

1. Uživatel je přihlášený do služby Azure AD ve webové aplikaci (viz [webového prohlížeče a webové aplikace](#web-browser-to-web-application) výše).
1. Webová aplikace musí získat přístupový token, takže ho můžete ověřit do webového rozhraní API a získat požadovaný prostředek. Odešle požadavek do služby Azure AD koncový bod tokenu, zadejte přihlašovací údaje, ID aplikace a identifikátor URI ID aplikace webového rozhraní API.
1. Azure AD ověřuje aplikace a vrátí přístupový token JWT, který se používá k volání webového rozhraní API.
1. Přes protokol HTTPS webová aplikace používá vrácené přístupový token JWT přidat token JWT řetězec s označením "Nosiče" v hlavičce autorizace požadavku do webového rozhraní API. Webové rozhraní API pak ověří JWT token a pokud je ověření úspěšné, vrátí požadovaný prostředek.

##### <a name="delegated-user-identity-with-openid-connect"></a>Delegovaný uživatel identity s OpenID Connect

1. Uživatel je přihlášený do webové aplikace pomocí Azure AD (viz [webového prohlížeče a webové aplikace](#web-browser-to-web-application) výše). Pokud uživatel webové aplikace nevyjádřil dosud povolení webové aplikace k volání webového rozhraní API na jejím jménem, uživateli muset vyjádřit souhlas. Aplikace se zobrazí oprávnění, která vyžaduje, a pokud některý z těchto oprávnění na úrovni správce, běžného uživatele v adresáři, nebude možné vyjádřit souhlas. Tento proces souhlasu platí jenom pro aplikace s více tenanty, aplikace není jednoho tenanta, protože aplikace se už máte potřebná oprávnění. Když uživatel přihlášen, webové aplikace dostala token ID s informacemi o uživateli, jakož i autorizační kód.
1. Použití autorizační kód vydané službou Azure AD, webová aplikace odešle požadavek na token koncového bodu Azure AD, který obsahuje autorizační kód, podrobnosti o aplikaci klienta (ID aplikace a identifikátor URI pro přesměrování) a požadovaný prostředek (ID aplikace Identifikátor URI pro webové rozhraní API).
1. Autorizační kód a informace o webové aplikace a webového rozhraní API jsou ověření pomocí Azure AD. Po úspěšném ověření Azure AD vrací dva tokeny: přístupový token JWT a aktualizační token JWT.
1. Přes protokol HTTPS webová aplikace používá vrácené přístupový token JWT přidat token JWT řetězec s označením "Nosiče" v hlavičce autorizace požadavku do webového rozhraní API. Webové rozhraní API pak ověří JWT token a pokud je ověření úspěšné, vrátí požadovaný prostředek.

##### <a name="delegated-user-identity-with-oauth-20-authorization-code-grant"></a>Delegovaný uživatel identitu pomocí udělení autorizačního kódu OAuth 2.0

1. Uživatel je už přihlášení do webové aplikace, jejichž mechanismus ověřování je nezávislý na Azure AD.
1. Webová aplikace vyžaduje autorizační kód k získání přístupového tokenu, takže vydává žádosti přes prohlížeč pro koncový bod autorizace Azure AD, poskytuje ID aplikace a identifikátor URI přesměrování pro webovou aplikaci po úspěšném ověření. Uživatel se přihlásí ke službě Azure AD.
1. Pokud uživatel webové aplikace nevyjádřil dosud povolení webové aplikace k volání webového rozhraní API na jejím jménem, uživateli muset vyjádřit souhlas. Aplikace se zobrazí oprávnění, která vyžaduje, a pokud některý z těchto oprávnění na úrovni správce, běžného uživatele v adresáři, nebude možné vyjádřit souhlas. Toto vyjádření souhlasu se vztahuje na aplikace i s více tenanty. V tomto případě jednoho tenanta můžete provádět správce souhlasu pro vyjádření souhlasu správce jménem uživatele. To lze provést pomocí `Grant Permissions` tlačítko [webu Azure Portal](https://portal.azure.com). 
1. Po uživatelem, webová aplikace obdrží autorizační kód potřebný k získání přístupového tokenu.
1. Použití autorizační kód vydané službou Azure AD, webová aplikace odešle požadavek na token koncového bodu Azure AD, který obsahuje autorizační kód, podrobnosti o aplikaci klienta (ID aplikace a identifikátor URI pro přesměrování) a požadovaný prostředek (ID aplikace Identifikátor URI pro webové rozhraní API).
1. Autorizační kód a informace o webové aplikace a webového rozhraní API jsou ověření pomocí Azure AD. Po úspěšném ověření Azure AD vrací dva tokeny: přístupový token JWT a aktualizační token JWT.
1. Přes protokol HTTPS webová aplikace používá vrácené přístupový token JWT přidat token JWT řetězec s označením "Nosiče" v hlavičce autorizace požadavku do webového rozhraní API. Webové rozhraní API pak ověří JWT token a pokud je ověření úspěšné, vrátí požadovaný prostředek.

#### <a name="code-samples"></a>Ukázky kódů

Zobrazit ukázky kódu pro webovou aplikaci do scénáře webového rozhraní API. A, vraťte se často – jsou přidávány nové ukázky. Web [aplikace webového rozhraní API](active-directory-code-samples.md#web-applications-signing-in-users-calling-microsoft-graph-or-a-web-api-with-the-users-identity).

#### <a name="registering"></a>Registrace

* Jedním Tenantem: Identita aplikace i případy identity delegovaný uživatel, webové aplikace a webového rozhraní API musí být registrována ve stejném adresáři, ve službě Azure AD. Webové rozhraní API je možné nakonfigurovat k vystavení sady oprávnění, které slouží k omezení přístupu k webové aplikaci na prostředky. Pokud typ identity delegovaný uživatel webové aplikace musí z rozevírací nabídky "Oprávnění do jiných aplikací" na webu Azure Portal vyberte požadovaná oprávnění. Tento krok není povinný, pokud se používá typ identity aplikace.
* Víceklientské: Nejprve, webové aplikace je nakonfigurován k označení oprávnění, která ho, aby byl funkční. Tento seznam požadovaných oprávnění se zobrazí v dialogovém okně, když uživatel nebo správce v cílovém adresáři dává souhlas pro aplikaci, které zpřístupní jejich organizace. Některé aplikace vyžadují jenom oprávnění na úrovni uživatele, které každý uživatel v organizaci můžou udělit souhlas s. Jiné aplikace vyžadují oprávnění na úrovni správce, které nelze vyjádřit souhlas uživatele v organizaci. Pouze správce adresáře lze udělit souhlas aplikacím, které vyžadují tuto úroveň oprávnění. Když uživatel nebo správce souhlasí, webové aplikace a webového rozhraní API jsou obě registrované v jejich adresáře.

#### <a name="token-expiration"></a>Vypršení platnosti tokenu

Pokud webová aplikace používá jeho autorizační kód k získání tokenu JWT přístupu, také obdrží obnovovací token JWT. Když vyprší platnost přístupového tokenu, token obnovení je možné opakované ověření uživatele bez nutnosti jejich nutnost znovu přihlásit. Tento aktualizační token se pak použije k ověření uživatele, jehož výsledkem nový přístupový token a aktualizační token.

### <a name="daemon-or-server-application-to-web-api"></a>Proces démon nebo server aplikace webového rozhraní API

Tato část popisuje proces démon nebo server aplikaci, která je potřeba získat prostředky z webového rozhraní API. Existují dva dílčí scénáře, které se vztahují k této sekci: proces démon, kterou je potřeba volat webové rozhraní API, založená na typ udělení přihlašovacích údajů klienta OAuth 2.0; a serverové aplikace (například webové rozhraní API), kterou je potřeba volat webové rozhraní API, založená na specifikace konceptu OAuth 2.0 On-Behalf-Of.

Pro scénář při démon aplikace je potřeba volat webové rozhraní API, je důležité pochopit pár věcí. Nejprve interakci s uživatelem není možné aplikaci démon, který vyžaduje aplikaci mít svou vlastní identitu. Příklad aplikace démon je úlohu služby batch, nebo službu operační systém běží na pozadí. Tento typ aplikace vyžádá přístupový token pomocí jeho identita aplikace a nabízí ten samý jeho ID aplikace, přihlašovací údaje (heslo nebo certifikát) a aplikace identifikátor URI ID do služby Azure AD. Po úspěšném ověření démona přijímá přístupového tokenu z Azure AD, která se pak použije k volání webového rozhraní API.

Pro scénář při serverové aplikace je potřeba volat webové rozhraní API, je vhodné použít příklad. Představte si, že uživatel se ověřil na nativní aplikaci a tuto nativní aplikaci je potřeba volat webové rozhraní API. Azure AD vydá přístupový token JWT volání webového rozhraní API. Pokud webové rozhraní API je potřeba volat podřízené webové rozhraní API, můžete tok on-behalf-of pro delegování identity uživatele a pro ověření do druhé úrovně webového rozhraní API.

#### <a name="diagram"></a>Diagram

![Proces démon nebo serverová aplikace webové rozhraní API diagramu](./media/authentication-scenarios/daemon_server_app_to_web_api.png)

#### <a name="description-of-protocol-flow"></a>Popis protokolu toku

##### <a name="application-identity-with-oauth-20-client-credentials-grant"></a>Udělit aplikaci identity pomocí přihlašovacích údajů klienta OAuth 2.0

1. Serverová aplikace musí nejprve ověřování pomocí Azure AD za sebe, bez zásahu člověka jako je například interaktivní dialogu přihlašování. Odešle požadavek do služby Azure AD koncový bod tokenu, zadejte přihlašovací údaje, ID aplikace a identifikátor URI ID aplikace.
1. Azure AD ověřuje aplikace a vrátí přístupový token JWT, který se používá k volání webového rozhraní API.
1. Přes protokol HTTPS webová aplikace používá vrácené přístupový token JWT přidat token JWT řetězec s označením "Nosiče" v hlavičce autorizace požadavku do webového rozhraní API. Webové rozhraní API pak ověří JWT token a pokud je ověření úspěšné, vrátí požadovaný prostředek.

##### <a name="delegated-user-identity-with-oauth-20-on-behalf-of-draft-specification"></a>Delegovaný uživatel identitu pomocí specifikace konceptu On-Behalf-Of OAuth 2.0

Viz následující popis toku předpokládá, že uživatel byl ověřen na jinou aplikaci (například nativní aplikace) a jejich identitu uživatele se použil k získání přístupového tokenu do první úrovně webového rozhraní API.

1. Nativní aplikace odesílá přístupového tokenu do první úrovně webového rozhraní API.
1. První úrovně webového rozhraní API odešle požadavek do služby Azure AD koncový bod tokenu, poskytuje jeho ID aplikace a přihlašovací údaje, stejně jako přístupový token uživatele. Kromě toho je žádost odeslána pomocí on_behalf_of parametr, který označuje, webové rozhraní API vyžaduje nové tokeny pro volání podřízené webové rozhraní API jménem původního uživatele.
1. Azure AD ověří, jestli webové rozhraní API první úrovně má oprávnění pro přístup k druhé úrovně webového rozhraní API a ověří žádost vrácení přístupový token JWT, token JWT obnovovací token do první úrovně webového rozhraní API.
1. První úrovně webového rozhraní API přes protokol HTTPS, pak zavolá druhé úrovně webového rozhraní API přidáním řetězec tokenu v autorizační hlavičky v požadavku. První úrovně webového rozhraní API můžete nadále volají druhé úrovně webového rozhraní API jako přístupový token a obnovovací tokeny jsou platné.

#### <a name="code-samples"></a>Ukázky kódů

Zobrazit ukázky kódu pro proces démon nebo serverové aplikace na scénáře webového rozhraní API. A, vraťte se často – jsou přidávány nové ukázky. [Server nebo proces démon aplikace webového rozhraní API](active-directory-code-samples.md#daemon-applications-accessing-web-apis-with-the-applications-identity)

#### <a name="registering"></a>Registrace

* Jedním Tenantem: Identita aplikace i případy identity delegovaný uživatel, proces démon nebo server aplikace musí být zaregistrovaný ve stejném adresáři, ve službě Azure AD. Webové rozhraní API je možné nakonfigurovat k vystavení sady oprávnění, které slouží k omezení proces démon nebo serveru pro přístup k jeho prostředkům. Pokud typ identity delegovaný uživatel serverová aplikace musí z rozevírací nabídky "Oprávnění do jiných aplikací" na webu Azure Portal vyberte požadovaná oprávnění. Tento krok není povinný, pokud se používá typ identity aplikace.
* Víceklientské: Nejprve proces démon nebo serveru aplikace je nakonfigurovaná k označení oprávnění, která ho, aby byl funkční. Tento seznam požadovaných oprávnění se zobrazí v dialogovém okně, když uživatel nebo správce v cílovém adresáři dává souhlas pro aplikaci, které zpřístupní jejich organizace. Některé aplikace vyžadují jenom oprávnění na úrovni uživatele, které každý uživatel v organizaci můžou udělit souhlas s. Jiné aplikace vyžadují oprávnění na úrovni správce, které nelze vyjádřit souhlas uživatele v organizaci. Pouze správce adresáře lze udělit souhlas aplikacím, které vyžadují tuto úroveň oprávnění. Když uživatel nebo správce souhlasí, i webové rozhraní API jsou registrovány v jejich adresáře.

#### <a name="token-expiration"></a>Vypršení platnosti tokenu

Při první aplikace používá jeho autorizační kód k získání tokenu JWT přístupu, také obdrží obnovovací token JWT. Když vyprší platnost přístupového tokenu, token obnovení je možné znovu ověřovat uživatele bez vás vyzve k zadání přihlašovacích údajů. Tento aktualizační token se pak použije k ověření uživatele, jehož výsledkem nový přístupový token a aktualizační token.

## <a name="see-also"></a>Viz také

[Příručka pro vývojáře Azure Active Directory](azure-ad-developers-guide.md)

[Ukázky kódu Azure Active Directory](active-directory-code-samples.md)

[Důležité informace o podepisování výměny klíčů ve službě Azure AD](active-directory-signing-key-rollover.md)

[OAuth 2.0 ve službě Azure AD](https://msdn.microsoft.com/library/azure/dn645545.aspx)
