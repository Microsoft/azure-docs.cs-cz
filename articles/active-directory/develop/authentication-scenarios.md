---
title: Ověřování v Microsoft identity platform | Azure
description: Další informace o ověřování v Microsoft identity platform aplikace modelu rozhraní API, zřizování, a nejběžnější scénáře ověřování tohoto platforma identit Microsoft podporuje.
services: active-directory
documentationcenter: dev-center-name
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 0c84e7d0-16aa-4897-82f2-f53c6c990fd9
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/05/2019
ms.author: celested
ms.reviewer: saeeda, sureshja, hirsin
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: b1d54347b9a3ccc72cfd5b88400d699d93132fbf
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60299865"
---
# <a name="what-is-authentication"></a>Co je ověřování?

*Ověřování* je vyzvání jedné strany k dodání legitimních přihlašovacích údajů, čímž vzniká základ pro vytvoření objektu zabezpečení sloužícího k řízení identity a přístupu. Jednodušeji řečeno jde o proces prokázání, že jste skutečně tím, kým tvrdíte, že jste. V angličtině se pro ověřování někdy používá zkrácené slovo AuthN.

*Autorizace* je udělení oprávnění ověřenému objektu zabezpečení, aby mohl něco provést. Určuje, ke kterým datům máte povolený přístup a co s nimi můžete dělat. V angličtině se pro autorizaci někdy používá zkrácené slovo AuthZ.

Platforma identit Microsoft zjednodušuje tím, že poskytuje identitu jako služba, se podpora pro standardní protokoly jako OAuth 2.0 a OpenID Connect, stejně jako open source knihoven pro různé platformy k ověřování pro vývojáře aplikací vám pomohou v začátcích psaní kódu rychle.

Existují dva hlavní případy použití v Microsoft identity platform programovací model:

* Během toku udělení autorizace OAuth 2.0 – když vlastník prostředku udělí autorizaci klientské aplikaci, čímž klientovi umožní přístup k prostředkům vlastníka prostředku.
* Během přístupu k prostředkům ze strany klienta – podle implementace serverem prostředků, využívání hodnot deklarace identity přítomných v přístupovém tokenu k rozhodování o řízení přístupu na jejich základě.

## <a name="authentication-basics-in-microsoft-identity-platform"></a>Základy ověřování v Microsoft identity platform

Představme si nejzákladnější scénář, ve kterém se vyžaduje identita: uživatel ve webovém prohlížeči se musí ověřit pro webovou aplikaci. Tento scénář je znázorněný na následujícím diagramu:

![Přehled přihlášení do webové aplikace](./media/authentication-scenarios/auth-basics-microsoft-identity-platform.svg)

O různých komponentách na obrázku potřebujete vědět tohle:

* Platforma identit Microsoft je zprostředkovatel identity. Zprostředkovatel identity je zodpovědný za ověření identity uživatelů a aplikací existujících v adresáři organizace a při úspěšném ověření těchto uživatelů a aplikací vydává tokeny zabezpečení.
* Aplikace, která chce externí ověřování k platformě Microsoft identity musí být zaregistrovaný ve službě Azure Active Directory (Azure AD). Azure AD aplikaci zaregistruje a jednoznačně identifikuje v adresáři.
* Mohou vývojáři ověřování knihovny open-source Microsoft identity platform usnadňují ověřování pomocí zpracování podrobnosti protokolu za vás. Další informace najdete v tématu platforma identit Microsoft [knihovny pro ověřování v2.0](reference-v2-libraries.md) a [knihovny ověřování v1.0](active-directory-authentication-libraries.md).
* Po ověření uživatele musí aplikace ověřit uživatelův token zabezpečení, aby bylo celé ověření úspěšné. K dispozici jsou rychlé starty, kurzy a ukázky kódu v různých jazycích a architekturách, které ukazují, co musí aplikace dělat.
  * Pokud chcete rychle vytvořit aplikaci a přidat funkce, jako jsou získání tokenů, aktualizace tokenů, přihlášení uživatele, zobrazení některých informací o uživateli a další, projděte si v dokumentaci sekci **Rychlé starty**.
  * Pokud chcete získat podrobné postupy založené na scénářích pro hlavní ověřovací úkoly pro vývojáře, jako jsou získání přístupových tokenů a jejich používání ve volání rozhraní API Microsoft Graph a dalších rozhraní API, implementace přihlášení s Microsoftem v tradiční aplikaci založené na webovém prohlížeči pomocí OpenID Connect a další úkoly, projděte si v dokumentaci sekci **Kurzy**.
  * Pokud si chcete stáhnout ukázky kódu, přejděte na [GitHub](https://github.com/Azure-Samples?q=active-directory).
* Tok požadavků a odpovědí pro proces ověřování závisí na použitém ověřovacím protokolu, jako je OAuth 2.0, OpenID Connect, WS-Federation nebo SAML 2.0. Další informace o protokolech najdete v sekci dokumentace **Koncepty > Protokoly**.

Ve výše popsaném příkladu scénáře můžete aplikace klasifikovat podle těchto dvou rolí:

* Aplikace, které potřebují zabezpečený přístup k prostředkům
* Aplikace, které hrají roli samotného prostředku

Teď, když máte přehled o základy, pokračujte ve čtení a pochopení modelu identit aplikace a rozhraní API, jak zřizování funguje v Microsoft identity platform a obsahuje odkazy na podrobné informace o běžných scénářích této platforma identit Microsoft podporuje.

## <a name="application-model"></a>Aplikační model

Platforma identit Microsoft představuje aplikací podle konkrétního modelu, která je navržená pro splnění dvě hlavní funkce:

* **Identifikovat aplikaci podle ověřovacího protokolu, který podporuje** – to zahrnuje vytvoření výčtu všech identifikátorů, adres URL, tajných kódů a souvisejících informací, které jsou potřeba během ověřování. Platforma identit Microsoft zde:

    * Obsahuje všechna data potřebná pro podporu ověřování v době běhu.
    * Obsahuje všechna data pro rozhodování o tom, k jakým prostředkům může aplikace potřebovat přístup a jestli by se měl daný požadavek splnit a za jakých okolností.
    * Poskytuje infrastrukturu pro implementaci zřizování aplikace v rámci tenanta vývojáře aplikace a do jakéhokoli jiného tenanta Azure AD.

* **Souhlas uživatele během žádosti o token doby zpracování a usnadnění dynamické zřizování aplikace mezi tenanty** – tady, platforma identit Microsoft:

    * Umožňuje uživatelům a správcům dynamicky udělovat nebo odepírat souhlas s tím, aby aplikace jejich jménem měla přístup k prostředkům.
    * Umožňuje správcům nakonec rozhodnout, co můžou aplikace provádět a kteří uživatelé můžou konkrétní aplikace používat a jak se přistupuje k prostředkům adresáře.

V Microsoft identity platform **aplikační objekt** popisuje aplikace jako abstraktní entity. Vývojáři pracují s aplikacemi. V době nasazení, platforma Microsoft identity používá daný aplikační objekt jako podrobný plán k vytvoření **instanční objekt služby**, která představuje konkrétní instanci aplikace v rámci tenanta a adresář. Právě instanční objekt definuje, co aplikace v konkrétním cílovém adresáři ve skutečnosti může dělat, kdo ji může používat, k jakým prostředkům má přístup a tak dále. Platforma identit Microsoft vytvoří instanční objekt z objektu aplikace prostřednictvím **souhlas**.

Následující diagram znázorňuje zjednodušenou platforma identit Microsoft zřizování toku s využitím souhlas.  V něm existují dva tenanty (A a B), kde tenanta A vlastní aplikace a tenanta B je vytvoření instance pomocí instančního objektu.  

![Zjednodušený tok zřizování s využitím souhlasu](./media/authentication-scenarios/simplified-provisioning-flow-consent-driven.svg)

V tomto toku zřizování:

|   |   |
|---|---|
| 1 | Uživatel z tenanta B se pokusí přihlásit aplikace |
| 2 | Jsou získány a ověřeny přihlašovací údaje uživatele. |
| 3 | Uživatel je vyzván k vyjádření souhlasu, aby aplikace získala přístup k tenantovi B. |
| 4 | Platforma Microsoft identity používá objekt aplikace a jako podrobný plán pro vytváření instančního objektu v tenantu B |
| 5 | Uživatel obdrží požadovaný token. |
|   |   |

Tento proces můžete libovolně opakovat pro další klienty (C, D a tak dále). Tenanta A zachová podrobný plán pro aplikaci (objekt aplikace). Uživatelé a správci všech ostatních tenantů, kde aplikace obdrží souhlas, si zachovají kontrolu nad tím, co aplikace může dělat, prostřednictvím odpovídajícího instančního objektu v každém tenantovi. Další informace najdete v tématu [aplikace a instanční objekty v Microsoft identity platform](app-objects-and-service-principals.md).

## <a name="claims-in-microsoft-identity-platform-security-tokens"></a>Deklarace identity v tokenech zabezpečení platforma identit Microsoft

Tokeny zabezpečení (přístup a ID tokeny) vydané platforma identit Microsoft obsahují deklarace identity nebo kontrolní výrazy s informací o subjektu, který byl ověřen. Aplikace můžou deklarace identity používat pro různé úkoly, včetně:

* Ověření tokenu
* Identifikace tenanta adresáře subjektu
* Zobrazení informací o uživateli
* Určení autorizace subjektu

Deklarace identity přítomné v jakémkoli tokenu zabezpečení jsou závislé na typu tokenu, typu přihlašovacích údajů pro ověření uživatele a konfiguraci aplikace.

Stručný popis každého typu deklarace identity, protože ho vygeneroval platforma identit Microsoft najdete v následující tabulce. Podrobnější informace najdete v článku [přístupové tokeny](access-tokens.md) a [tokeny typu ID](id-tokens.md) vydané platforma identit Microsoft.

| Deklarovat | Popis |
| --- | --- |
| ID aplikace | Identifikuje aplikaci, která token používá. |
| Cílová skupina | Identifikuje přijímající prostředek, pro který je token určený. |
| Reference třídy kontextu ověřování aplikace | Určuje, jak byl klient ověřen (veřejný klient nebo důvěrný klient). |
| Okamžik ověření | Zaznamenává datum a čas, kdy k ověření došlo. |
| Metoda ověřování | Určuje, jak byl subjekt tokenu ověřen (heslo, certifikát atd.). |
| Jméno | Poskytuje křestní jméno uživatele, jak je nastavené v Azure AD. |
| Skupiny | Obsahuje ID objektů skupin Azure AD, kterých je uživatel členem. |
| Zprostředkovatel identity | Zaznamenává zprostředkovatele identity, který ověřil subjekt tokenu. |
| Vystaveno | Zaznamená čas, kdy byl token vystaven, což se často používá pro aktuálnost tokenu. |
| Vystavitel | Identifikuje službu tokenů zabezpečení, která token vygenerovala, a také tenanta Azure AD. |
| Příjmení | Poskytuje příjmení uživatele, jak je nastavené v Azure AD. |
| Name | Poskytuje lidsky čitelnou hodnotu, která identifikuje subjekt tokenu. |
| ID objektu | Obsahuje neměnný a jedinečný identifikátor subjektu v Azure AD. |
| Role | Obsahuje popisné názvy aplikačních rolí Azure AD, které byly uživateli uděleny. |
| Rozsah | Určuje oprávnění udělená klientské aplikaci. |
| Subjekt | Určuje objekt zabezpečení, o kterém token prosazuje informace. |
| ID tenanta | Obsahuje neměnný a jedinečný identifikátor tenanta adresáře, který token vydal. |
| Živostnost tokenu | Definuje časový interval, ve kterém je token platný. |
| Hlavní název uživatele | Obsahuje hlavní název uživatele subjektu. |
| Version | Obsahuje číslo verze tokenu. |

## <a name="next-steps"></a>Další postup

* Další informace o [typy aplikací a scénáře podporované v Microsoft identity platform](app-types.md)
