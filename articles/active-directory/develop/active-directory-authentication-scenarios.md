---
title: Scénáře ověřování pro Azure AD | Microsoft Docs
description: Poskytuje přehled pět nejběžnějších scénářů ověřování pro Azure Active Directory (Azure AD)
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
ms.date: 04/24/2018
ms.author: celested
ms.reviewer: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 0f535e8a74822319d472c62fe1b65e5145e58a74
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/11/2018
ms.locfileid: "35261640"
---
# <a name="authentication-scenarios-for-azure-ad"></a>Scénáře ověřování pro Azure AD

Azure Active Directory (Azure AD) zjednodušuje tím, že poskytuje identity jako služby, se podpora pro standardní protokoly, jako je například OAuth 2.0 a OpenID Connect, stejně jako open-source knihovny pro různé platformy, které vám pomohou ověřování pro vývojáře psaní se rychle. Tento článek vám pomůže pochopit podporuje různé scénáře služby Azure AD a ukazují, jak začít pracovat. Je rozdělený do následujících částí:

* [Základní informace o ověřování ve službě Azure AD](#basics-of-authentication-in-azure-ad)
* [Deklarace identity v tokenech zabezpečení Azure AD](#claims-in-azure-ad-security-tokens)
* [Základní informace o registraci aplikace ve službě Azure AD](#basics-of-registering-an-application-in-azure-ad)
* [Typy aplikací a scénáře](#application-types-and-scenarios)

  * [Webový prohlížeč, aby webové aplikace](#web-browser-to-web-application)
  * [Jednostránkové aplikace (SPA)](#single-page-application-spa)
  * [Nativní aplikace za účelem webového rozhraní API](#native-application-to-web-api)
  * [Webovou aplikaci pro webové rozhraní API](#web-application-to-web-api)
  * [Démon procesu nebo server aplikace webového rozhraní API](#daemon-or-server-application-to-web-api)

## <a name="basics-of-authentication-in-azure-ad"></a>Základní informace o ověřování ve službě Azure AD

Pokud jste obeznámeni s základní koncepty ověřování ve službě Azure AD, přečtěte si v této části. V ostatních případech můžete chtít přejděte dolů k [typy aplikací a scénáře](#application-types-and-scenarios).

Pojďme se nejzákladnější scénář, kdy je potřeba identity: uživatel ve webovém prohlížeči potřebuje ke svému ověření u webové aplikace. Tento scénář je podrobně popsané v větší [webový prohlížeč k webové aplikaci](#web-browser-to-web-application) části, ale je užitečné výchozí bod pro ilustraci možnosti Azure AD a conceptualize fungování tohoto scénáře. Vezměte v úvahu následující diagram pro tento scénář:

![Přehled přihlášení do webové aplikace](./media/active-directory-authentication-scenarios/basics_of_auth_in_aad.png)

S diagramu výše na paměti zde je, co potřebujete vědět o jeho různé komponenty:

* Azure AD je zprostředkovatel identity, který je zodpovědný za ověření identity uživatelů a aplikací, které existují v adresáři organizace a nakonec vystavování tokenů zabezpečení po úspěšném ověření těchto uživatelů a aplikací.
* Aplikace, která chce externí ověřování do služby Azure AD musí být zaregistrované ve službě Azure AD, která registruje a jednoznačně identifikuje aplikaci v adresáři.
* Vývojářům můžete použít knihovny ověřování open-source Azure AD nastavit ověřování snadno tak, že zpracování podrobnosti protokolu. Další informace najdete v tématu [knihovny Azure Active Directory Authentication](active-directory-authentication-libraries.md).
* Po ověření uživatele aplikace musíte ověřit token zabezpečení uživatele k zajištění, že ověření proběhlo úspěšně. Máme ukázky co musíte udělat aplikace v různých jazyků a rozhraní na [Githubu](https://github.com/Azure-Samples?q=active-directory). Pokud vytváříte webovou aplikaci v technologii ASP.NET, najdete v článku [přidání přihlášení příručce webové aplikace ASP.NET](https://docs.microsoft.com/en-us/azure/active-directory/develop/guidedsetups/active-directory-aspnetwebapp). Pokud vytváříte webové rozhraní API prostředků v technologii ASP.NET, najdete v článku [webové rozhraní API Příručka Začínáme](https://docs.microsoft.com/en-us/azure/active-directory/develop/active-directory-devquickstarts-webapi-dotnet).
* Tok požadavky a odpovědi pro proces ověřování je dáno ověřovací protokol, který byl použit, jako je například OAuth 2.0, OpenID Connect, WS-Federation nebo SAML 2.0. Tyto protokoly jsou podrobněji popsána v [protokoly pro ověřování Azure Active Directory](active-directory-authentication-protocols.md) článku a v následujících částech.

> [!NOTE]
> Azure AD podporuje OAuth 2.0 a OpenID Connect standardy, které rozsáhlé použití tokenů nosiče, včetně nosné tokeny vyjádřené tokeny Jwt. A *tokenu nosiče* je token lightweight zabezpečení, která uděluje "nosiče" přístup k chráněnému prostředku. V tomto smyslu je "nosiče" libovolné strany, který může být token. I když může strana musí nejprve ověřit pomocí Azure AD pro příjem token nosiče, nebudou přijata požadované kroky k zabezpečení token v přenos a ukládání, může být zachyceny a použity nezamýšleným strana. I když některé tokeny zabezpečení má integrovanou mechanismus, který brání neoprávněným stranám jejich používání, nosné tokeny nemají tento mechanismus a musí být přenosu v zabezpečený kanál, jako je například transport layer security (HTTPS). Pokud je token nosiče přenesen v nešifrované podobě, lze škodlivý stranou útok man-in-the-middle získat token a používat ho pro neoprávněný přístup k chráněnému prostředku. Při ukládání nebo ukládání do mezipaměti nosné tokeny pro pozdější použití platí stejné zásady zabezpečení. Vždy zajistěte, aby vaše aplikace odesílá a ukládá nosné tokeny zabezpečeným způsobem. Další aspekty zabezpečení na nosné tokeny, najdete v části [RFC 6750 část 5](http://tools.ietf.org/html/rfc6750).

Teď, když máte přehled o základní informace, přečtěte si následující části obsahují pochopit, jak zřizování funguje ve službě Azure AD a běžné scénáře, které podporuje Azure AD.

## <a name="claims-in-azure-ad-security-tokens"></a>Deklarace identity v tokenech zabezpečení Azure AD

Tokeny zabezpečení (přístup a tokeny typu ID) vydané službou Azure AD obsahují deklarace identity nebo tvrzení informace o subjektu, který byl ověřen. Tyto deklarace lze aplikací pro různé úlohy. Aplikace můžete například použít deklarace identity Pokud chcete ověřit token, identifikovat klienta directory daného subjektu, zobrazit informace o uživateli, určit autorizační daného subjektu a tak dále. V jakékoli dané zabezpečení token deklarace identity jsou závislé na typ tokenu, typ pověření použité k ověření uživatele a konfiguraci aplikace. Stručný popis každého typu deklarace identity vygenerované službou Azure AD najdete v následující tabulce. Další informace najdete v části [podporované typy tokenů a deklarací identity](active-directory-token-and-claims.md).

| Deklarovat | Popis |
| --- | --- |
| ID aplikace | Určuje aplikace, která je pomocí tokenu. |
| Cílová skupina | Identifikuje příjemce token je určená pro prostředek. |
| Application Authentication Context Class Reference | Určuje, jak klient se ověřený (veřejné klient oproti důvěrné klienta). |
| Ověřování prostřednictvím rychlých | Zaznamenává datum a čas, kdy došlo k chybě ověřování. |
| Metoda ověřování | Určuje, jak byla ověřena předmět tokenu (heslo, certifikát, atd.). |
| Jméno | Poskytuje sadu křestní jméno uživatele ve službě Azure AD. |
| Skupiny | Obsahuje objekt skupiny ID Azure AD, jíž je uživatel členem. |
| Zprostředkovatel identity | Zaznamenává poskytovatele identit, předmět token k ověření. |
| Vydané v | Zaznamenává dobu, kdy byl token vydán, často používají pro token aktuálnosti. |
| Vystavitel | Identifikuje služby tokenů zabezpečení, které vygenerované token, jakož i klient Azure AD. |
| Příjmení | Poskytuje sadu Přezdívka uživatele ve službě Azure AD. |
| Název | Poskytuje lidského čitelný hodnotu, která identifikuje předmět tokenu. |
| ID objektu | Obsahuje neměnné jedinečný identifikátor subjektu ve službě Azure AD. |
| Role | Obsahuje popisné názvy aplikační role Azure AD, kterému byla udělena uživatele. |
| Rozsah | Určuje oprávnění udělená aplikaci klienta. |
| Předmět | Určuje objekt o tom, které vyhodnotí token informace. |
| ID tenanta | Obsahuje neměnné jedinečný identifikátor adresáře klienta, která vydala token. |
| Životnost tokenu | Definuje časový interval, ve kterém je token platný. |
| Hlavní název uživatele (UPN) | Obsahuje hlavní název uživatele subjektu. |
| Verze | Obsahuje číslo verze tokenu. |

## <a name="basics-of-registering-an-application-in-azure-ad"></a>Základní informace o registraci aplikace ve službě Azure AD

Jakékoli aplikace, která outsources ověřování do služby Azure AD musí být zaregistrován v adresáři. Tento krok zahrnuje informacemi o vaší aplikaci, včetně adresu URL, kde je umístěn, adresu URL pro odeslání odpovědi po ověření, identifikátor URI k identifikaci aplikace a další služby Azure AD. Tyto informace jsou nezbytné pro klíče z několika důvodů:

* Azure AD musí komunikovat s aplikací při zpracování přihlášení nebo výměnou tokeny. Informace se předávají mezi Azure AD a aplikace zahrnuje následující:
  
  * **Identifikátor ID URI aplikace** -identifikátor pro aplikaci. Tato hodnota je odeslána do služby Azure AD při ověřování k označení, která aplikace chce volající token pro. Kromě toho tato hodnota je obsažena v tokenu, aby aplikace věděla, že byl zamýšleného cílového.
  * **Adresa URL odpovědi** a **identifikátor URI pro přesměrování** -webového rozhraní API nebo webovou aplikaci, adresa URL odpovědi je umístění, kde Azure AD odešle odpověď ověřování, a to včetně token, pokud ověření bylo úspěšné. Identifikátor URI přesměrování pro nativní aplikace je jedinečný identifikátor, ke kterému se Azure AD přesměruje uživatelského agenta v jednom požadavku OAuth 2.0.
  * **ID aplikace** -ID pro aplikaci, která se generují pomocí Azure AD při registraci aplikace. Při žádosti autorizační kód nebo na tokenu, ID aplikace a klíč se odešlou do služby Azure AD během ověřování.
  * **Klíč** – klíč, který je odeslán společně s ID aplikace při ověřování do služby Azure AD volání webového rozhraní API.
* Azure AD musí zajistit, že aplikace má požadovaná oprávnění pro přístup k datům vašeho adresáře, jinými aplikacemi ve vaší organizaci a tak dále.

Zřizování se změní jasnější, pokud víte, že existují dvě kategorie aplikací, které je možné vyvinuté a integrovat se službou Azure AD:

* **Jedna aplikace klienta** -aplikace jednoho klienta je určena pro použití v jedné organizaci. Toto jsou obvykle-obchodní (LoB) aplikace napsané pomocí vývojář enterprise. Jednoho klienta aplikace potřebuje pouze mít přístup uživatelé v jednom adresáři, a v důsledku toho se pouze musí být zřízená v jednom adresáři. Tyto aplikace jsou běžně registrovány vývojáři v organizaci.
* **Víceklientské aplikace** -víceklientské aplikace určena pro použití v mnoha organizacích není právě jedné organizace. Toto jsou obvykle softwaru jako služba (SaaS) aplikace napsané pomocí nezávislý dodavatel softwaru (ISV). Víceklientské aplikace potřebují v každý adresář, kde se bude používat, který vyžaduje souhlas uživatele nebo správce k registraci jejich zřídit. Tento proces souhlasu spustí, když aplikace je zaregistrován v adresáři a je poskytnut přístup k rozhraní Graph API nebo možná jiné webové rozhraní API. Pokud uživatel nebo správce z jiné organizace zaregistruje k používání aplikace, zobrazí se dialogové okno se zobrazí oprávnění, která aplikace vyžaduje. Uživatel nebo správce může pak souhlas k aplikaci, která poskytuje přístup k aplikaci stanovené dat a nakonec zaregistruje aplikaci do svého adresáře. Další informace najdete v tématu [přehled rozhraní souhlas](active-directory-integrating-applications.md#overview-of-the-consent-framework).

### <a name="additional-considerations-when-developing-single-tenant-or-multi-tenant-apps"></a>Další aspekty při vývoji jednotné klienta nebo víceklientské aplikace
Některé další aspekty nastat při vývoji aplikace víceklientské místo jednoho klienta aplikace. Například pokud bude se vaše aplikace k dispozici uživatelům v několika adresářích, potřebujete mechanismus k určení klientů, které jsou ve. Jednoho klienta aplikace potřebuje pouze hledat v její vlastní adresář pro uživatele, zatímco víceklientské aplikace potřebuje k identifikaci konkrétního uživatele ze všech adresářů ve službě Azure AD. K provedení této úlohy, Azure AD poskytuje společný koncový bod ověřování kde jakékoli víceklientské aplikace můžete nastavit žádostí o přihlášení, místo konkrétního klienta endpoint. Tento koncový bod je https://login.microsoftonline.com/common pro všechny adresáře ve službě Azure AD, že koncový bod konkrétního klienta může být https://login.microsoftonline.com/contoso.onmicrosoft.com. Běžné koncový bod je obzvláště důležité vzít v úvahu při vývoji aplikace, protože budete potřebovat pomocí potřebné logiky pro zpracování více klientů při přihlášení, odhlášení a ověření tokenu.

Pokud jsou aktuálně vývoj aplikace s jednoho klienta, ale mají být k dispozici v mnoha organizacích, můžete snadno provedete změny aplikace a jeho konfigurace ve službě Azure AD, aby víceklientské podporující. Kromě toho Azure AD používá stejný podpisový klíč pro všechny tokeny ve všech adresářích, zda jste poskytli ověřování na jednoho klienta nebo v aplikaci víceklientské.

Každý scénář uvedené v tomto dokumentu zahrnuje část, která popisuje požadavky na jeho zřizování. Podrobnější informace o zřizování aplikace ve službě Azure AD a rozdíly mezi jednou či více klientů aplikace najdete v tématu [integrace aplikací s Azure Active Directory](active-directory-integrating-applications.md) Další informace. Pokračujte ve čtení pochopit běžné scénáře aplikací ve službě Azure AD.

## <a name="application-types-and-scenarios"></a>Typy aplikací a scénáře

Všechny scénáře popsaného mohou být vytvořeny pomocí různé jazyky a platformy. Všechny jsou zajišťované pomocí ukázky dokončení kódu, které jsou k dispozici v [Průvodce ukázky kódu](active-directory-code-samples.md), nebo přímo z odpovídající [ukázka úložišť GitHub](https://github.com/Azure-Samples?q=active-directory). Kromě toho pokud aplikace potřebuje konkrétní nebo segmentu začátku do konce scénáře, ve většině případů této funkce lze přidat nezávisle. Například pokud máte nativní aplikaci, která volá webové rozhraní API, můžete snadno přidat webovou aplikaci, která volá webové rozhraní API také. Následující diagram znázorňuje tyto scénáře a typy aplikací, a jak jiné součásti, lze přidat:

![Typy aplikací a scénáře](./media/active-directory-authentication-scenarios/application_types_and_scenarios.png)

Jsou to pěti scénáře primární aplikace podporované službou Azure AD:

* [Webový prohlížeč k webové aplikaci](#web-browser-to-web-application): uživatel potřebuje k přihlášení k webové aplikaci, která je zabezpečená službou Azure AD.
* [Jedna stránka aplikace (SPA)](#single-page-application-spa): uživatel musí pro přihlášení k jednostránkové aplikace, která je zabezpečená službou Azure AD.
* [Nativní aplikace za účelem webového rozhraní API](#native-application-to-web-api): nativní aplikaci, která běží na telefon, tablet nebo počítač potřebuje k ověření uživatele k získání prostředky z webového rozhraní API, která je zabezpečená službou Azure AD.
* [Webovou aplikaci pro webové rozhraní API](#web-application-to-web-api): webová aplikace musí získat prostředky z webového rozhraní API, které jsou zabezpečené službou Azure AD.
* [Démon procesu nebo server aplikace webového rozhraní API](#daemon-or-server-application-to-web-api): démon aplikaci nebo serveru bez webového uživatelského rozhraní musí získat prostředky z webového rozhraní API, které jsou zabezpečené službou Azure AD.

### <a name="web-browser-to-web-application"></a>Webový prohlížeč, aby webové aplikace

Tato část popisuje aplikaci, která ověřuje uživatele ve webovém prohlížeči k webové aplikaci. V tomto scénáři bude směrovat webové aplikace prohlížeče uživatele pro jejich přihlášení k Azure AD. Azure AD vrátí odpověď přihlášení prostřednictvím prohlížeče uživatele, který obsahuje deklarace identity o uživateli v tokenu zabezpečení. Tento scénář podporuje přihlašování pomocí protokolů WS-Federation, SAML 2.0 a OpenID Connect.

#### <a name="diagram"></a>Diagram

![Tok ověření pro prohlížeče do webové aplikace](./media/active-directory-authentication-scenarios/web_browser_to_web_api.png)

#### <a name="description-of-protocol-flow"></a>Popis protokolu toku

1. Pokud uživatel navštíví aplikace a musí se přihlásit, je přesměrován prostřednictvím přihlašovací požadavek na koncový bod ověřování ve službě Azure AD.
1. Na stránce přihlášení přihlášení uživatele.
1. Pokud je ověření úspěšné, Azure AD vytvoří ověřovací token a vrátí odpověď přihlašovací adresa URL odpovědi aplikace, který byl nakonfigurován na portálu Azure. Pro produkční aplikace musí být tato adresa URL odpovědi protokolu HTTPS. Vrácený token obsahuje deklarace identity o uživateli a Azure AD, které jsou požadované aplikací k ověření tokenu.
1. Aplikace ověří token pomocí veřejného podpisového klíče a vystavitele informace, které jsou k dispozici na dokument federačních metadat pro Azure AD. Po aplikaci ověří token, spustí novou relaci s uživatelem. Tato relace umožňuje uživatelům přístup k aplikaci do vypršení jeho platnosti.

#### <a name="code-samples"></a>Ukázky kódů

Další informace najdete v ukázky kódu pro webový prohlížeč scénáře webových aplikací. A, kontrolujte pravidelně – často jsou přidávány nové ukázky. [Webovou aplikaci](active-directory-code-samples.md#web-applications).

#### <a name="registering"></a>Registrace

* Jednoho klienta: Pokud vytváříte aplikace právě pro vaši organizaci, se musí být zaregistrován v adresáři vaší společnosti pomocí portálu Azure.
* Víceklientské: Pokud vytváříte aplikace, která mohou být využívána uživatelé mimo vaši organizaci, musí být zaregistrovaný v adresáři vaší společnosti, ale také musí být zaregistrovaný v adresáři každé organizace, který bude používat aplikace. Chcete-li k dispozici v jejich adresář vaší aplikace, můžete zahrnout procesu registrace pro vaše zákazníky, které umožňuje, aby souhlas do vaší aplikace. Při registraci vaší aplikace, zobrazí se dialogové okno, které zobrazuje oprávnění, která aplikace vyžaduje a možnost vyjádřit souhlas. V závislosti na požadovaných oprávnění správce v jiné organizaci může být nutné vyjádřit svůj souhlas. Když uživatel nebo správce souhlasí, aplikace se registruje v jejich adresáře. Další informace najdete v tématu [integrace aplikací s Azure Active Directory](active-directory-integrating-applications.md).

#### <a name="token-expiration"></a>Vypršení platnosti tokenu

Relace uživatele vyprší po vypršení platnosti tokenem vydaným službou Azure AD. Aplikace můžete zmenšit tohoto časového období, v případě potřeby, jako je například odhlášení uživatele založené na období nečinnosti. Když vyprší platnost relace, uživatel se vyzve k znovu přihlásit.

### <a name="single-page-application-spa"></a>Jednostránkové aplikace (SPA)

Tato část popisuje ověřování pro jednu aplikaci, stránka, která používá Azure AD a implicitní autorizace OAuth 2.0 udělit zabezpečení jeho webového rozhraní API zpět ukončení. Jednostránkové aplikace mají obvykle strukturu prezentační vrstvou JavaScript (front-end), která běží v prohlížeči a webového rozhraní API back-end, který běží na serveru a implementuje obchodní logiku aplikace. Další informace o udělení implicitní autorizace a vám pomohou rozhodnout, zda je správný pro váš scénář aplikací najdete v tématu [pochopení OAuth2 implicitní tok v Azure Active Directory poskytování](active-directory-dev-understanding-oauth2-implicit-grant.md).

V tomto scénáři, když se uživatel přihlásí, JavaScript front end používá [Active Directory Authentication Library pro jazyk JavaScript (ADAL. JS)](https://github.com/AzureAD/azure-activedirectory-library-for-js) a přidělit implicitní autorizace k získání tokenu ID (požadavku id_token) z Azure AD. Token se uloží do mezipaměti a klient připojí jej k žádosti o jako token nosiče při volání jeho webového rozhraní API back-end, který zabezpečené pomocí middlewaru OWIN. 

#### <a name="diagram"></a>Diagram

![Diagram jedné stránky aplikace](./media/active-directory-authentication-scenarios/single_page_app.png)

#### <a name="description-of-protocol-flow"></a>Popis protokolu toku

1. Uživatel přejde na webové aplikace.
1. Aplikace vrací JavaScript front-endu (prezentační vrstva) do prohlížeče.
1. Uživatel spustí přihlášení, například kliknutím na odkaz přihlášení. Prohlížeč odesílá GET na koncový bod autorizace Azure AD k vyžádání tokenu ID. Tato žádost obsahuje adresu URL ID a odpovědi aplikace v parametry dotazu.
1. Azure AD ověří adresa URL odpovědi pro registrované adresa URL odpovědi, který byl nakonfigurován na portálu Azure.
1. Na stránce přihlášení přihlášení uživatele.
1. Pokud je ověření úspěšné, Azure AD vytvoří ID token a vrátí se adresa URL odpovědi aplikace jako fragment adresy URL (#). Pro produkční aplikace musí být tato adresa URL odpovědi protokolu HTTPS. Vrácený token obsahuje deklarace identity o uživateli a Azure AD, které jsou požadované aplikací k ověření tokenu.
1. Kód jazyka JavaScript klienta spuštěná v prohlížeči extrahuje token z odpovědi pro použití v zabezpečení volání do webové aplikace že API zpět ukončení.
1. V prohlížeči volá webové aplikace API zpět končit přístupový token v hlavičce autorizace.

#### <a name="code-samples"></a>Ukázky kódů

Najdete v části Ukázky kódu pro scénáře jedné stránky aplikace (SPA). Zkontrolujte často – často jsou přidávány nové ukázky. [Jedna stránka aplikace (SPA)](active-directory-code-samples.md#single-page-applications).

#### <a name="registering"></a>Registrace

* Jednoho klienta: Pokud vytváříte aplikace právě pro vaši organizaci, se musí být zaregistrován v adresáři vaší společnosti pomocí portálu Azure.
* Víceklientské: Pokud vytváříte aplikace, která mohou být využívána uživatelé mimo vaši organizaci, musí být zaregistrovaný v adresáři vaší společnosti, ale také musí být zaregistrovaný v adresáři každé organizace, který bude používat aplikace. Chcete-li k dispozici v jejich adresář vaší aplikace, můžete zahrnout procesu registrace pro vaše zákazníky, které umožňuje, aby souhlas do vaší aplikace. Při registraci vaší aplikace, zobrazí se dialogové okno, které zobrazuje oprávnění, která aplikace vyžaduje a možnost vyjádřit souhlas. V závislosti na požadovaných oprávnění správce v jiné organizaci může být nutné vyjádřit svůj souhlas. Když uživatel nebo správce souhlasí, aplikace se registruje v jejich adresáře. Další informace najdete v tématu [integrace aplikací s Azure Active Directory](active-directory-integrating-applications.md).

Po registraci aplikace, musí být nakonfigurován pro použití protokolu OAuth 2.0 implicitní Grant. Ve výchozím nastavení je tento protokol zakázaná pro aplikace. Pokud chcete povolit protokol OAuth2 implicitní udělte pro aplikaci, upravit jeho manifest aplikace z portálu Azure a nastavte hodnotu "oauth2AllowImplicitFlow" na hodnotu true. Podrobné pokyny najdete v tématu [povolení OAuth 2.0 implicitní Grant pro jednostránkové aplikace](active-directory-integrating-applications.md).

#### <a name="token-expiration"></a>Vypršení platnosti tokenu

Používání ADAL.js pomáhá s:

* Aktualizace tokenu vypršela platnost
* požaduje přístupového tokenu k volání webového rozhraní API prostředků

Po úspěšném ověření Azure AD se zapisují do souboru cookie prohlížeče uživatele vytvořit relaci. Všimněte si, že existuje relace mezi uživatelem a Azure AD (není mezi uživatelem a webové aplikace). Když vyprší platnost tokenu, používá ADAL.js bezobslužně získat další token tuto relaci. ADAL.js používá skrytá iFrame odesílat a přijímat žádosti pomocí protokolu OAuth implicitní Grant. ADAL.js můžete také použít tento stejný mechanismus bezobslužně získat přístupové tokeny pro jiné webové rozhraní API prostředky, které aplikace volá tak dlouho, dokud tyto prostředky podporu (CORS), sdílení prostředků různého původu jsou zaregistrované v adresáři uživatele a všechny požadované souhlasu se Zadaný uživatel během přihlašování.

### <a name="native-application-to-web-api"></a>Nativní aplikace za účelem webového rozhraní API

Tato část popisuje nativní aplikaci, která volá webové rozhraní API jménem uživatele. Tento scénář je založený na typ udělení kódu autorizace OAuth 2.0 pomocí veřejného klienta, jak je popsáno v části 4.1 [specifikace OAuth 2.0](http://tools.ietf.org/html/rfc6749). Nativní aplikace získá přístupový token pro uživatele s použitím protokolu OAuth 2.0. Tento přístupový token se pak odešlou v požadavku na webové rozhraní API, která opravňují uživatele a vrátí požadovaný prostředek.

#### <a name="diagram"></a>Diagram

![Nativní aplikace za účelem webové rozhraní API Diagram](./media/active-directory-authentication-scenarios/native_app_to_web_api.png)

#### <a name="description-of-protocol-flow"></a>Popis protokolu toku

Pokud používáte knihovny ověřování AD, jsou zpracovávány většinu podrobnosti protokolu popsané níže, například automaticky otevírané okno prohlížeče, ukládání tokenu do mezipaměti a zpracování obnovovacích tokenů.

1. Pomocí prohlížeče automaticky otevírané okno, že nativní aplikace odešle požadavek na koncový bod autorizace ve službě Azure AD. Tato žádost obsahuje ID aplikace a identifikátor URI nativní aplikace přesměrování, jak je znázorněno v portálu Azure a identifikátor ID URI aplikace pro webové rozhraní API. Pokud uživatel nebyl ještě přihlášeni, budou vyzváni k přihlášení znovu
1. Azure AD ověřuje uživatele. Pokud je víceklientské aplikace a je požadován k používání aplikace souhlas, uživatel bude nutné nutné vyjádřit souhlas. Pokud se tak již neučinili. Po udělení souhlasu a po úspěšném ověření Azure AD vydá autorizační kód odpověď zpět identifikátor URI přesměrování klienta aplikace.
1. Pokud Azure AD vydá autorizační kód odpověď zpět identifikátor URI přesměrování, klientská aplikace přestane interakce prohlížeče a extrahuje autorizační kód z odpovědi. Pomocí tohoto kódu autorizace, klientská aplikace odešle požadavek na token koncový bod Azure AD, která zahrnuje autorizační kód, podrobnosti o aplikaci klienta (ID aplikace a identifikátor URI pro přesměrování) a požadovaný prostředek (aplikace identifikátor ID URI pro webové rozhraní API).
1. Autorizační kód a informace o rozhraní API pro aplikace a webové klient se ověří pomocí Azure AD. Po úspěšném ověření Azure AD vrátí dva tokeny: přístupový token JWT a obnovovací token JWT. Kromě toho Azure AD vrátí základní informace o uživateli, jako jejich zobrazovaný název a klienta ID.
1. Přes protokol HTTPS klientská aplikace používá přístupový token vrácená JWT k přidání JWT řetězec s označením "Nosiče" v hlavičce autorizace požadavku do webové rozhraní API. Webové rozhraní API pak ověří JWT token a pokud je ověření úspěšné, vrátí požadovaného prostředku.
1. Když vyprší platnost přístupového tokenu, klientská aplikace obdrží chybu, která označuje, že uživatel musí provést ověření znovu. Pokud aplikace má platný obnovovací token, můžete použít k získání nového tokenu přístupu bez výzvy pro uživatele se znovu přihlásit. Pokud token obnovení vyprší, aplikace bude nutné znovu interaktivně ověření uživatele.

> [!NOTE]
> Obnovovací token vydán Azure AD můžete použít pro přístup k více prostředkům. Například pokud máte klientské aplikace, který má oprávnění k volání dva webové rozhraní API, obnovovací token slouží k získání tokenu pro jiné webové rozhraní API také přístupu.

#### <a name="code-samples"></a>Ukázky kódů

Nativní aplikace za účelem scénáře webového rozhraní API najdete v části Ukázky kódu. A, kontrolujte pravidelně – často přidáme nové ukázky. [Nativní aplikace za účelem webového rozhraní API](active-directory-code-samples.md#desktop-and-mobile-public-client-applications-calling-microsoft-graph-or-a-web-api).

#### <a name="registering"></a>Registrace

* Jednoho klienta: Nativního obě aplikace a webové rozhraní API musí být zaregistrovaný ve stejném adresáři v Azure AD. Webové rozhraní API se dá nakonfigurovat vystavit sadu oprávnění, která se používá k omezení nativní aplikaci přístup k jeho prostředky. Klientská aplikace potom vybere požadované oprávnění z rozevírací nabídky "Oprávnění k další aplikace" na webu Azure portal.
* Víceklientské: Nejdřív nativní aplikace vždy jen zaregistrovat ve vývojář nebo adresář vydavatele. Druhý nativní aplikace je nakonfigurovaná k označení oprávnění, která se vyžaduje, aby se funkční. Tento seznam požadovaných oprávnění se zobrazí v dialogové okno, pokud uživatel nebo správce v cílovém adresáři dává souhlasu pro aplikaci, že bude k dispozici pro jejich organizaci. Některé aplikace vyžadují jenom oprávnění na úrovni uživatele, které může každý uživatel v organizaci souhlas. Jiné aplikace vyžadují oprávnění na úrovni správce, které nelze souhlas uživatele v organizaci. Pouze správce adresáře může udělit souhlas k aplikacím, které vyžadují tato úroveň oprávnění. Když uživatel nebo správce souhlasí, jenom webové rozhraní API se registruje v jejich adresáře. Další informace najdete v tématu [integrace aplikací s Azure Active Directory](active-directory-integrating-applications.md).

#### <a name="token-expiration"></a>Vypršení platnosti tokenu

Pokud nativní aplikace používá jeho autorizační kód pro získání tokenu přístupu JWT, dále přijímá obnovovací token JWT. Když vyprší platnost přístupového tokenu, token obnovení lze opakované ověření uživatele bez nutnosti jejich se znovu přihlásit. Tento token obnovení se pak použije k ověření uživatele, což vede nový přístupový token a obnovovací token.

### <a name="web-application-to-web-api"></a>Webovou aplikaci pro webové rozhraní API

Tato část popisuje webové aplikace, které potřebuje získat prostředky z webového rozhraní API. V tomto scénáři jsou dva typy identity, které webové aplikace můžete použít k ověřování a volání webového rozhraní API: Identita aplikace, nebo delegovaný uživatel identity.

*Identita aplikace:* tento scénář používá k ověření jako aplikace a přístup k webové rozhraní API udělení pověření klienta OAuth 2.0. Při použití identity aplikací, webové rozhraní API pouze může zjistit, že webová aplikace je volání, jako webové rozhraní API neobdrží žádné informace o uživateli. Pokud aplikace obdrží informace o uživateli, bude odeslána prostřednictvím protokolu aplikace a není podepsán serverem Azure AD. Webové rozhraní API důvěřuje, že webová aplikace ověřil daného uživatele. Z tohoto důvodu se tento vzor nazývá důvěryhodný subsystém.

*Delegované identity uživatele:* tento scénář lze provést dvěma způsoby: OpenID Connect a udělení autorizačního kódu OAuth 2.0 pomocí důvěrné klienta. Webová aplikace získá přístupový token pro uživatele, který prokáže webové rozhraní API, které uživatel úspěšně ověřen do webové aplikace a které webové aplikace bylo možné získat identitu delegovaný uživatel k volání webového rozhraní API. Tento přístupový token je odeslaných v požadavku na webové rozhraní API, která opravňují uživatele a vrátí požadovaný prostředek.

#### <a name="diagram"></a>Diagram

![Webovou aplikaci na diagram webového rozhraní API](./media/active-directory-authentication-scenarios/web_app_to_web_api.png)

#### <a name="description-of-protocol-flow"></a>Popis protokolu toku

Identita aplikace i delegovaný uživatel identity typy jsou popsané v následující postup. Klíčovým rozdílem mezi nimi je, že identita uživatele delegovaný musíte nejprve získat autorizační kód předtím, než uživatel může přihlásit a získat přístup k webové rozhraní API.

##### <a name="application-identity-with-oauth-20-client-credentials-grant"></a>Udělit identita aplikace pomocí přihlašovacích údajů klienta OAuth 2.0

1. Uživatel je přihlášený do služby Azure AD ve webové aplikaci (viz [webový prohlížeč k webové aplikaci](#web-browser-to-web-application) výše).
1. Webová aplikace musí získat přístupový token, aby mohli ověřit webové rozhraní API a načíst požadovaný prostředek. Vytvoří žádost na Azure AD koncovému bodu tokenu, poskytuje pověření, ID aplikace a aplikace webového rozhraní API je identifikátor ID URI.
1. Azure AD ověřuje aplikace a vrátí přístupový token JWT, který se používá k volání webového rozhraní API.
1. Přes protokol HTTPS webová aplikace používá přístupový token vrácená JWT přidat řetězec JWT s označením "Nosiče" v hlavičce autorizace požadavku do webové rozhraní API. Webové rozhraní API pak ověří JWT token a pokud je ověření úspěšné, vrátí požadovaného prostředku.

##### <a name="delegated-user-identity-with-openid-connect"></a>Identita uživatele delegovaný s OpenID Connect

1. Uživatel je přihlášený k webové aplikaci pomocí služby Azure AD (najdete v článku [webový prohlížeč k webové aplikaci](#web-browser-to-web-application) část výše). Pokud uživatel webové aplikace nebyla dosud dá souhlas, povolení k volání webového rozhraní API jeho jménem webové aplikace, uživatel bude muset souhlas. Aplikace se zobrazí oprávnění, která vyžaduje, a pokud některá z těchto oprávnění na úrovni správce, nebude možné souhlas normální uživatele v adresáři. Tento proces souhlasu platí jenom pro víceklientské aplikace, aplikace nejsou jednoho klienta, aplikace bude již mít nezbytná oprávnění. Při přihlášení uživatele přijat webové aplikace token ID s informacemi o uživateli, jakož i autorizační kód.
1. Pomocí autorizační kód vydané službou Azure AD, webové aplikace odešle požadavek na token koncový bod Azure AD, která zahrnuje autorizační kód, podrobnosti o aplikaci klienta (ID aplikace a identifikátor URI pro přesměrování) a požadovaný prostředek (aplikace identifikátor ID URI pro webové rozhraní API).
1. Autorizační kód a informace o webové aplikace a webového rozhraní API se ověří pomocí Azure AD. Po úspěšném ověření Azure AD vrátí dva tokeny: přístupový token JWT a obnovovací token JWT.
1. Přes protokol HTTPS webová aplikace používá přístupový token vrácená JWT přidat řetězec JWT s označením "Nosiče" v hlavičce autorizace požadavku do webové rozhraní API. Webové rozhraní API pak ověří JWT token a pokud je ověření úspěšné, vrátí požadovaného prostředku.

##### <a name="delegated-user-identity-with-oauth-20-authorization-code-grant"></a>Identita uživatele delegovaný s udělení autorizačního kódu OAuth 2.0

1. Uživatel je již přihlášení k webové aplikaci, jejíž mechanismus ověřování je nezávislé na Azure AD.
1. Webové aplikace vyžaduje autorizační kód získat přístupový token, takže ho vydává požadavek prostřednictvím prohlížeče pro koncový bod autorizace Azure AD, poskytuje ID aplikace a identifikátor URI přesměrování pro webovou aplikaci po úspěšném ověření. Uživatel se přihlásí ke službě Azure AD.
1. Pokud uživatel webové aplikace nebyla dosud dá souhlas, povolení k volání webového rozhraní API jeho jménem webové aplikace, uživatel bude muset souhlas. Aplikace se zobrazí oprávnění, která vyžaduje, a pokud některá z těchto oprávnění na úrovni správce, nebude možné souhlas normální uživatele v adresáři. Svůj souhlas se vztahuje na jeden a více klientů aplikace. V případě jednoho klienta může správce provést správce souhlas k souhlasu jménem uživatelů. To lze provést pomocí `Grant Permissions` v tlačítko [portálu Azure](https://portal.azure.com). 
1. Poté, co uživatel souhlasí, webová aplikace přijímá autorizační kód, který musí získat přístupový token.
1. Pomocí autorizační kód vydané službou Azure AD, webové aplikace odešle požadavek na token koncový bod Azure AD, která zahrnuje autorizační kód, podrobnosti o aplikaci klienta (ID aplikace a identifikátor URI pro přesměrování) a požadovaný prostředek (aplikace identifikátor ID URI pro webové rozhraní API).
1. Autorizační kód a informace o webové aplikace a webového rozhraní API se ověří pomocí Azure AD. Po úspěšném ověření Azure AD vrátí dva tokeny: přístupový token JWT a obnovovací token JWT.
1. Přes protokol HTTPS webová aplikace používá přístupový token vrácená JWT přidat řetězec JWT s označením "Nosiče" v hlavičce autorizace požadavku do webové rozhraní API. Webové rozhraní API pak ověří JWT token a pokud je ověření úspěšné, vrátí požadovaného prostředku.

#### <a name="code-samples"></a>Ukázky kódů

Najdete v části Ukázky kódu pro webovou aplikaci pro scénáře webového rozhraní API. A, kontrolujte pravidelně – často jsou přidávány nové ukázky. Webové [aplikace webového rozhraní API](active-directory-code-samples.md#web-applications-signing-in-users-calling-microsoft-graph-or-a-web-api-with-the-users-identity).

#### <a name="registering"></a>Registrace

* Jednoho klienta: Identita aplikace i delegovaný uživatel identity případů, webové aplikace a rozhraní web API musí být zaregistrován ve stejném adresáři v Azure AD. Webové rozhraní API se dá nakonfigurovat vystavit sadu oprávnění, které se používají k omezení přístupu webové aplikace k její prostředky. Pokud se používá typ identity delegované uživatele, vyberte z rozevírací nabídky "Oprávnění k další aplikace" na webu Azure portal požadované oprávnění musí webové aplikace. Tento krok se nevyžaduje, pokud se používá typ identity aplikace.
* Víceklientské: Nejdřív webové aplikace je nakonfigurovaná k označení oprávnění, která se vyžaduje, aby se funkční. Tento seznam požadovaných oprávnění se zobrazí v dialogové okno, pokud uživatel nebo správce v cílovém adresáři dává souhlasu pro aplikaci, že bude k dispozici pro jejich organizaci. Některé aplikace vyžadují jenom oprávnění na úrovni uživatele, které může každý uživatel v organizaci souhlas. Jiné aplikace vyžadují oprávnění na úrovni správce, které nelze souhlas uživatele v organizaci. Pouze správce adresáře může udělit souhlas k aplikacím, které vyžadují tato úroveň oprávnění. Když uživatel nebo správce souhlasí, webové aplikace a webového rozhraní API jsou obě registrovány svého adresáře.

#### <a name="token-expiration"></a>Vypršení platnosti tokenu

Pokud webová aplikace používá jeho autorizační kód k získání tokenu přístupu JWT, dále přijímá obnovovací token JWT. Když vyprší platnost přístupového tokenu, token obnovení lze opakované ověření uživatele bez nutnosti jejich se znovu přihlásit. Tento token obnovení se pak použije k ověření uživatele, což vede nový přístupový token a obnovovací token.

### <a name="daemon-or-server-application-to-web-api"></a>Démon procesu nebo server aplikace webového rozhraní API

Tato část popisuje proces démon nebo server aplikace, která je potřeba získat prostředky z webového rozhraní API. Existují dva dílčí scénáře, které se vztahují k této části: démon, které potřebuje volat webového rozhraní API, založený na typ udělení pověření klienta OAuth 2.0; a serverová aplikace (například webové rozhraní API), které potřebuje volat webového rozhraní API, založený na specifikace koncept OAuth 2.0 On-Behalf-Of.

U scénáře, když démon aplikace potřebuje k volání webového rozhraní API, je důležité si uvědomit, pár věcí. Nejprve interakci s uživatelem není možné pomocí démon aplikace, který vyžaduje aplikací mít svůj vlastní identity. Příkladem démon aplikace je dávkové úlohy nebo služby operační systém spuštěný na pozadí. Tento typ aplikace požaduje přístupový token pomocí svou identitu aplikace a prezentování jeho ID aplikace, přihlašovací údaje (heslo nebo certifikát) a aplikace identifikátor ID URI do služby Azure AD. Po úspěšném ověření démon obdrží token přístupu z Azure AD, která se pak použije k volání webového rozhraní API.

Pro scénář při serverová aplikace potřebuje k volání webového rozhraní API, je vhodné použít příklad. Představte si, že byl uživatel ověřen na nativní aplikaci a tato nativní aplikace musí volání webového rozhraní API. Azure AD vydá token JWT přístup k volání webového rozhraní API. Pokud webového rozhraní API potřebuje volat podřízené webové rozhraní API, může použít tok on-behalf-of delegovat identitu uživatele a ověření sekundární webové rozhraní API.

#### <a name="diagram"></a>Diagram

![Démon procesu nebo serverové aplikace diagram webového rozhraní API](./media/active-directory-authentication-scenarios/daemon_server_app_to_web_api.png)

#### <a name="description-of-protocol-flow"></a>Popis protokolu toku

##### <a name="application-identity-with-oauth-20-client-credentials-grant"></a>Udělit identita aplikace pomocí přihlašovacích údajů klienta OAuth 2.0

1. Nejprve server potřebuje k ověření pomocí Azure AD jako samostatně, bez jakékoli zásahem ze strany například dialogovým interaktivní přihlašování. Vytvoří žádost na Azure AD koncovému bodu tokenu, poskytnutí přihlašovacích údajů, ID aplikace a identifikátor ID URI aplikace.
1. Azure AD ověřuje aplikace a vrátí přístupový token JWT, který se používá k volání webového rozhraní API.
1. Přes protokol HTTPS webová aplikace používá přístupový token vrácená JWT přidat řetězec JWT s označením "Nosiče" v hlavičce autorizace požadavku do webové rozhraní API. Webové rozhraní API pak ověří JWT token a pokud je ověření úspěšné, vrátí požadovaného prostředku.

##### <a name="delegated-user-identity-with-oauth-20-on-behalf-of-draft-specification"></a>Identita uživatele delegovaný s specifikace koncept On-Behalf-Of OAuth 2.0

Tok popsané níže předpokládá, že uživatel byl ověřen na jinou aplikaci (například nativní aplikace), a jejich identitu uživatele byla použita k získání přístupového tokenu k první vrstvu webového rozhraní API.

1. Nativní aplikace odešle token přístupu do první vrstvu webového rozhraní API.
1. První vrstvu webového rozhraní API odešle požadavek do služby Azure AD koncovému bodu tokenu, poskytuje jeho ID aplikace a přihlašovací údaje, stejně jako přístupový token uživatele. Kromě toho je zaslán požadavek s on_behalf_of parametr, který určuje webového rozhraní API požaduje nové tokeny volat podřízené webové rozhraní API jménem původního uživatele.
1. Azure AD ověří, že první vrstvu webového rozhraní API, má oprávnění k přístupu sekundární webové rozhraní API a ověří žádost, vrácení přístupový token JWT, a aktualizovat token JWT token, který má první vrstvu webového rozhraní API.
1. První vrstvu webového rozhraní API přes protokol HTTPS, pak zavolá sekundární webové rozhraní API připojením řetězec tokenu v hlavičce autorizace v požadavku. První vrstvu webového rozhraní API můžete nadále volání sekundární webové rozhraní API, dokud jsou platné přístupový token a obnovovacích tokenů.

#### <a name="code-samples"></a>Ukázky kódů

Démon procesu nebo serverové aplikace scénáře webového rozhraní API najdete v části Ukázky kódu. A, kontrolujte pravidelně – často jsou přidávány nové ukázky. [Server nebo démon aplikaci pro webové rozhraní API](active-directory-code-samples.md#daemon-applications-accessing-web-apis-with-the-applications-identity)

#### <a name="registering"></a>Registrace

* Jednoho klienta: Identita aplikace i delegovaný uživatel identity případy, aplikace démon nebo server musí být zaregistrované ve stejném adresáři v Azure AD. Webové rozhraní API se dá nakonfigurovat vystavit sadu oprávnění, která se používá k omezení démon nebo serveru pro přístup k jeho prostředky. Pokud se používá typ identity delegované uživatele, musí aplikace serveru vyberte z rozevírací nabídky "Oprávnění k další aplikace" na webu Azure portal požadované oprávnění. Tento krok se nevyžaduje, pokud se používá typ identity aplikace.
* Víceklientské: Nejdřív démon nebo server aplikace je nakonfigurovaná k označení oprávnění, která se vyžaduje, aby se funkční. Tento seznam požadovaných oprávnění se zobrazí v dialogové okno, pokud uživatel nebo správce v cílovém adresáři dává souhlasu pro aplikaci, že bude k dispozici pro jejich organizaci. Některé aplikace vyžadují jenom oprávnění na úrovni uživatele, které může každý uživatel v organizaci souhlas. Jiné aplikace vyžadují oprávnění na úrovni správce, které nelze souhlas uživatele v organizaci. Pouze správce adresáře může udělit souhlas k aplikacím, které vyžadují tato úroveň oprávnění. Když uživatel nebo správce souhlasí, jsou v jejich adresář zaregistrované i webové rozhraní API.

#### <a name="token-expiration"></a>Vypršení platnosti tokenu

Při první aplikaci jeho autorizační kód používá k získání tokenu přístupu JWT, dále přijímá obnovovací token JWT. Když vyprší platnost přístupového tokenu, token obnovení lze opakované ověření uživatele bez zobrazení výzvy k zadání pověření. Tento token obnovení se pak použije k ověření uživatele, což vede nový přístupový token a obnovovací token.

## <a name="see-also"></a>Viz také

[Příručka pro vývojáře Azure Active Directory](active-directory-developers-guide.md)

[Ukázky kódu Azure Active Directory](active-directory-code-samples.md)

[Důležité informace o podepisování výměna klíče ve službě Azure AD](active-directory-signing-key-rollover.md)

[OAuth 2.0 ve službě Azure AD](https://msdn.microsoft.com/library/azure/dn645545.aspx)
