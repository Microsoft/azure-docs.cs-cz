---
title: Ověřování v Microsoft Identity Platform | Azure
description: Seznamte se s ověřováním v Microsoft Identity Platform, modelu aplikace, rozhraní API, zřizování a nejběžnějších scénářů ověřování, které Microsoft Identity Platform podporuje.
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: 0c84e7d0-16aa-4897-82f2-f53c6c990fd9
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/23/2019
ms.author: ryanwi
ms.reviewer: saeeda, sureshja, hirsin
ms.custom: aaddev, identityplatformtop40
ms.collection: M365-identity-device-management
ms.openlocfilehash: 76c5214fc26d299c6abb72ed6cd448728903e78f
ms.sourcegitcommit: a6718e2b0251b50f1228b1e13a42bb65e7bf7ee2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2019
ms.locfileid: "71272545"
---
# <a name="what-is-authentication"></a>Co je ověřování?

*Ověřování* je vyzvání jedné strany k dodání legitimních přihlašovacích údajů, čímž vzniká základ pro vytvoření objektu zabezpečení sloužícího k řízení identity a přístupu. Jednodušeji řečeno jde o proces prokázání, že jste skutečně tím, kým tvrdíte, že jste. V angličtině se pro ověřování někdy používá zkrácené slovo AuthN.

*Autorizace* je udělení oprávnění ověřenému objektu zabezpečení, aby mohl něco provést. Určuje, ke kterým datům máte povolený přístup a co s nimi můžete dělat. V angličtině se pro autorizaci někdy používá zkrácené slovo AuthZ.

Microsoft Identity Platform usnadňuje ověřování pro vývojáře aplikací tím, že poskytuje identitu jako službu a podporuje standardní protokoly, jako je OAuth 2,0 a OpenID Connect, a také open source knihovny pro různé platformy. pomůže vám rychle začít vytvářet kódování.

V programovacím modelu Microsoft Identity Platform se nacházejí dva primární případy použití:

* Během toku udělení autorizace OAuth 2.0 – když vlastník prostředku udělí autorizaci klientské aplikaci, čímž klientovi umožní přístup k prostředkům vlastníka prostředku.
* Během přístupu k prostředkům ze strany klienta – podle implementace serverem prostředků, využívání hodnot deklarace identity přítomných v přístupovém tokenu k rozhodování o řízení přístupu na jejich základě.

## <a name="authentication-basics-in-microsoft-identity-platform"></a>Základy ověřování v platformě Microsoft Identity Platform

Představme si nejzákladnější scénář, ve kterém se vyžaduje identita: uživatel ve webovém prohlížeči se musí ověřit pro webovou aplikaci. Tento scénář je znázorněný na následujícím diagramu:

![Přehled přihlášení do webové aplikace](./media/authentication-scenarios/auth-basics-microsoft-identity-platform.svg)

O různých komponentách na obrázku potřebujete vědět tohle:

* Microsoft Identity Platform je poskytovatel identity. Zprostředkovatel identity je zodpovědný za ověření identity uživatelů a aplikací existujících v adresáři organizace a při úspěšném ověření těchto uživatelů a aplikací vydává tokeny zabezpečení.
* Aplikace, která chce externí ověřování na platformě Microsoft identity, musí být registrovaná v Azure Active Directory (Azure AD). Azure AD aplikaci zaregistruje a jednoznačně identifikuje v adresáři.
* Vývojáři mohou pomocí Open Source knihoven ověřování Microsoft Identity Platform provádět ověřování snadno pomocí zpracování detailů protokolu. Další informace najdete v tématu knihovny ověřování Microsoft Identity Platform [v 2.0](reference-v2-libraries.md) a [knihovny ověřování v 1.0](active-directory-authentication-libraries.md).
* Po ověření uživatele musí aplikace ověřit uživatelův token zabezpečení, aby bylo celé ověření úspěšné. K dispozici jsou rychlé starty, kurzy a ukázky kódu v různých jazycích a architekturách, které ukazují, co musí aplikace dělat.
  * Pokud chcete rychle vytvořit aplikaci a přidat funkce, jako jsou získání tokenů, aktualizace tokenů, přihlášení uživatele, zobrazení některých informací o uživateli a další, projděte si v dokumentaci sekci **Rychlé starty**.
  * Pokud chcete získat podrobné postupy založené na scénářích pro hlavní ověřovací úkoly pro vývojáře, jako jsou získání přístupových tokenů a jejich používání ve volání rozhraní API Microsoft Graph a dalších rozhraní API, implementace přihlášení s Microsoftem v tradiční aplikaci založené na webovém prohlížeči pomocí OpenID Connect a další úkoly, projděte si v dokumentaci sekci **Kurzy**.
  * Pokud si chcete stáhnout ukázky kódu, přejděte na [GitHub](https://github.com/Azure-Samples?q=active-directory).
* Tok požadavků a odpovědí pro proces ověřování závisí na použitém ověřovacím protokolu, jako je OAuth 2.0, OpenID Connect, WS-Federation nebo SAML 2.0. Další informace o protokolech najdete v části **koncepty > Authentication Protocol** v dokumentaci.

Ve výše popsaném příkladu scénáře můžete aplikace klasifikovat podle těchto dvou rolí:

* Aplikace, které potřebují zabezpečený přístup k prostředkům
* Aplikace, které hrají roli samotného prostředku

### <a name="how-each-flow-emits-tokens-and-codes"></a>Jak každý tok generuje tokeny a kódy

V závislosti na tom, jak je váš klient sestavený, může použít jeden (nebo několik) toků ověřování podporovaných platformou Microsoft identity.  Tyto toky můžou vytvářet různé tokeny (id_tokens, aktualizovat tokeny, přístupové tokeny) a také autorizační kódy a při práci vyžadovat jiné tokeny. Tento graf znázorňuje přehled:

|Tok | Nutné | id_token | přístupový token | aktualizovat token | Autorizační kód | 
|-----|----------|----------|--------------|---------------|--------------------|
|[Tok autorizačního kódu](v2-oauth2-auth-code-flow.md) | | x | x | x | x|  
|[Implicitní tok](v2-oauth2-implicit-grant-flow.md) | | x        | x    |      |                    |
|[Hybridní tok OIDC](v2-protocols-oidc.md#get-access-tokens)| | x  | |          |            x   |
|[Aktualizovat uplatnění tokenu](v2-oauth2-auth-code-flow.md#refresh-the-access-token) | aktualizovat token | x | x | x| |
|[Tok On-Behalf-Of](v2-oauth2-on-behalf-of-flow.md) | přístupový token| x| x| x| |
|[Tok kódu zařízení](v2-oauth2-device-code.md) | | x| x| x| |
|[Přihlašovací údaje klienta](v2-oauth2-client-creds-grant-flow.md) | | | x (jenom aplikace)| | |

**Poznámky**:

Tokeny vydané prostřednictvím implicitního režimu mají omezení délky, protože se předává zpátky do prohlížeče přes adresu URL (kde `response_mode` je `query` nebo `fragment`).  Některé prohlížeče mají omezení velikosti adresy URL, kterou lze umístit do panelu prohlížeče, a selhání, pokud je příliš dlouhé.  Proto tyto tokeny nejsou `groups` ani `wids` deklarace identity. 


Teď, když máte přehled základních informací, přečtěte si článek popisující model aplikace identity a rozhraní API, jak zřizování funguje na platformě Microsoft Identity Platform a odkazuje na podrobné informace o běžných scénářích, které Microsoft Identity Platform podporuje.

## <a name="application-model"></a>Aplikační model

Platforma Microsoft Identity reprezentuje aplikace podle konkrétního modelu, který je navržený tak, aby splňoval dvě hlavní funkce:

* **Identifikovat aplikaci podle ověřovacího protokolu, který podporuje** – to zahrnuje vytvoření výčtu všech identifikátorů, adres URL, tajných kódů a souvisejících informací, které jsou potřeba během ověřování. Platforma Microsoft Identity Platform:

    * Obsahuje všechna data potřebná pro podporu ověřování v době běhu.
    * Obsahuje všechna data pro rozhodování o tom, k jakým prostředkům může aplikace potřebovat přístup a jestli by se měl daný požadavek splnit a za jakých okolností.
    * Poskytuje infrastrukturu pro implementaci zřizování aplikace v rámci tenanta vývojáře aplikace a do jakéhokoli jiného tenanta Azure AD.

* **Zpracování souhlasu uživatele během doby žádosti o tokeny a usnadnění dynamického zřizování aplikací napříč klienty** – tady je platforma Microsoft Identity Platform:

    * Umožňuje uživatelům a správcům dynamicky udělovat nebo odepírat souhlas s tím, aby aplikace jejich jménem měla přístup k prostředkům.
    * Umožňuje správcům nakonec rozhodnout, co můžou aplikace provádět a kteří uživatelé můžou konkrétní aplikace používat a jak se přistupuje k prostředkům adresáře.

V rámci Microsoft Identity Platform **objekt aplikace** popisuje aplikaci jako abstraktní entitu. Vývojáři pracují s aplikacemi. V době nasazení používá platforma Microsoft Identity Platform daný objekt aplikace jako podrobný plán k vytvoření **instančního**objektu, který představuje konkrétní instanci aplikace v rámci adresáře nebo tenanta. Právě instanční objekt definuje, co aplikace v konkrétním cílovém adresáři ve skutečnosti může dělat, kdo ji může používat, k jakým prostředkům má přístup a tak dále. Platforma Microsoft Identity Platform vytvoří instanční objekt z objektu aplikace prostřednictvím **souhlasu**.

Následující diagram znázorňuje zjednodušený postup zřizování platformy Microsoft Identity Platform založený na základě souhlasu.  V tomto případě existují dva klienty (a a B), kde tenant vlastní aplikaci a tenant B vytváří instanci aplikace prostřednictvím instančního objektu.  

![Zjednodušený tok zřizování s využitím souhlasu](./media/authentication-scenarios/simplified-provisioning-flow-consent-driven.svg)

V tomto toku zřizování:

1. Uživatel z tenanta B se pokusí přihlásit k aplikaci, koncový bod autorizace požaduje token pro aplikaci.
1. Přihlašovací údaje uživatele se získávají a ověřují pro ověřování.
1. Uživateli se zobrazí výzva k poskytnutí souhlasu aplikace, aby získala přístup k tenantovi B.
1. Platforma Microsoft Identity Platform používá aplikační objekt v tenantovi A jako plán pro vytvoření instančního objektu v tenantovi B.
1. Uživatel obdrží požadovaný token.

Tento proces můžete libovolně opakovat pro další klienty (C, D a tak dále). Tenant A uchovává podrobný plán aplikace (objekt aplikace). Uživatelé a správci všech ostatních tenantů, kde aplikace obdrží souhlas, si zachovají kontrolu nad tím, co aplikace může dělat, prostřednictvím odpovídajícího instančního objektu v každém tenantovi. Další informace najdete v tématu [aplikační a instanční objekty v platformě Microsoft Identity Platform](app-objects-and-service-principals.md).

## <a name="claims-in-microsoft-identity-platform-security-tokens"></a>Deklarace identity v tokenech zabezpečení Microsoft Identity Platform

Tokeny zabezpečení (přístup a tokeny ID) vydané platformou Microsoft Identity Platform obsahují deklarace identity nebo kontrolní výrazy informací o subjektu, který byl ověřen. Aplikace můžou deklarace identity používat pro různé úkoly, včetně:

* Ověření tokenu
* Identifikace tenanta adresáře subjektu
* Zobrazení informací o uživateli
* Určení autorizace subjektu

Deklarace identity přítomné v jakémkoli tokenu zabezpečení jsou závislé na typu tokenu, typu přihlašovacích údajů pro ověření uživatele a konfiguraci aplikace.

V následující tabulce je uveden stručný popis každého typu deklarace vysílaný platformou Microsoft identity. Podrobnější informace najdete v tématu [přístupové tokeny](access-tokens.md) a [tokeny ID](id-tokens.md) vydané platformou Microsoft Identity Platform.

| Deklarace identity | Popis |
| --- | --- |
| ID aplikace | Identifikuje aplikaci, která token používá. |
| Cílová skupina | Identifikuje přijímající prostředek, pro který je token určený. |
| Reference třídy kontextu ověřování aplikace | Určuje, jak byl klient ověřen (veřejný klient nebo důvěrný klient). |
| Okamžik ověření | Zaznamenává datum a čas, kdy k ověření došlo. |
| Metoda ověření | Určuje, jak byl subjekt tokenu ověřen (heslo, certifikát atd.). |
| Jméno | Poskytuje křestní jméno uživatele, jak je nastavené v Azure AD. |
| Skupiny | Obsahuje ID objektů skupin Azure AD, kterých je uživatel členem. |
| Zprostředkovatel identity | Zaznamenává zprostředkovatele identity, který ověřil subjekt tokenu. |
| Vystaveno | Zaznamená čas, kdy byl token vystaven, což se často používá pro aktuálnost tokenu. |
| Vystavitel | Identifikuje službu tokenů zabezpečení, která token vygenerovala, a také tenanta Azure AD. |
| Příjmení | Poskytuje příjmení uživatele, jak je nastavené v Azure AD. |
| Name | Poskytuje lidsky čitelnou hodnotu, která identifikuje subjekt tokenu. |
| ID objektu | Obsahuje neměnný a jedinečný identifikátor subjektu v Azure AD. |
| Role | Obsahuje popisné názvy aplikačních rolí Azure AD, které byly uživateli uděleny. |
| Scope | Určuje oprávnění udělená klientské aplikaci. |
| Subjekt | Určuje objekt zabezpečení, o kterém token prosazuje informace. |
| ID tenanta | Obsahuje neměnný a jedinečný identifikátor tenanta adresáře, který token vydal. |
| Živostnost tokenu | Definuje časový interval, ve kterém je token platný. |
| Hlavní název uživatele | Obsahuje hlavní název uživatele subjektu. |
| Version | Obsahuje číslo verze tokenu. |

## <a name="next-steps"></a>Další kroky

* Seznamte se s [typy aplikací a scénáři podporovanými v platformě Microsoft Identity Platform](app-types.md) .
