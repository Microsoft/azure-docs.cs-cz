---
title: Potíže s konfigurací heslem jednotného přihlašování pro aplikaci Galerie Azure AD | Dokumentace Microsoftu
description: Vysvětlení běžných tváří lidí problémy při konfiguraci hesla jednotné přihlašování pro aplikace, které jsou již uveden v galerii aplikací Azure AD
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/11/2017
ms.author: celested
ms.collection: M365-identity-device-management
ms.openlocfilehash: afe467d56d0247e17f73bb85a39246c4c04629cd
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2019
ms.locfileid: "56173449"
---
# <a name="problem-configuring-password-single-sign-on-for-an-azure-ad-gallery-application"></a>Potíže s konfigurací heslem jednotného přihlašování pro aplikaci Galerie Azure AD

Tento článek vám pomůže pochopit běžné tváří lidí problémy při konfigurování **heslo Single Sign-on** s aplikaci Galerie Azure AD.

## <a name="credentials-are-filled-in-but-the-extension-does-not-submit-them"></a>Přihlašovací údaje jsou vyplněna, ale je odeslat není rozšíření

Tento problém obvykle proběhne, pokud má změnit jejich přihlašovací stránce nedávno chcete přidat pole, změnit identifikátor používaný pro zjišťování pole uživatelské jméno a heslo nebo změnit způsob přihlášení vyzkoušet funguje pro svou aplikaci na dodavatele aplikace. V mnoha případech naštěstí Microsoft můžete pracovat s dodavatele aplikací jak tyto problémy rychle vyřešit.

Zatímco Microsoft technologiemi, které automaticky rozpoznat, kdy se integrace přerušit, nemusí být možné najít problémy okamžitě nebo problémy trvat nějakou dobu opravit. V případě, pokud jeden z těchto integrace nebude fungovat správně, otevřete případ podpory abyste ho mohli opravit co nejrychleji.

**Pokud jste kontaktu s dodavatele této aplikace** odešlete nám tak, že Microsoft může spolupracovat s nimi nativně své aplikace integrovat s Azure Active Directory. Dodavatel, který můžete poslat [výpis vaší aplikace v galerii aplikací Azure Active Directory](../develop/howto-app-gallery-listing.md) zobrazíte jejich spuštění.

## <a name="credentials-are-filled-in-and-submitted-but-the-page-indicates-the-credentials-are-incorrect"></a>Přihlašovací údaje jsou vyplněna a odeslány, ale na stránce indikuje, že přihlašovací údaje jsou nesprávné

Chcete-li tento problém vyřešit, nejdřív pokusí použít tyto věci:

-   Požádejte uživatele, zkuste je napřed **přihlásit na web aplikaci přímo** přihlašovacími údaji pro ně.

  * Pokud přihlášení funguje, pak je mít uživatele, klikněte na tlačítko **aktualizovat přihlašovací údaje** tlačítko **dlaždice aplikace** v **aplikace** část [přístup k aplikaci Panel](https://myapps.microsoft.com/) je aktualizovat na poslední známé funkční uživatelské jméno a heslo.

   * Pokud vy nebo jiný správce dostali přihlašovací údaje pro tohoto uživatele, vyhledejte uživatele nebo skupiny přiřazení aplikací tak, že přejdete na **uživatelů a skupin** kartu aplikace výběrem přiřazení a kliknutím na  **Aktualizace přihlašovacích údajů** tlačítko.

-   Pokud uživatel přiřazenou svoje vlastní přihlašovací údaje, požádejte uživatele **kontrolu, abyste měli jistotu, že nevypršela platnost jejich hesla v aplikaci** a pokud ano, **aktualizovat své heslo služby vypršela platnost** přihlášením k aplikaci přímo.

   * Po aktualizaci hesla v aplikaci požádat o uživatel klepne na tlačítko **aktualizovat přihlašovací údaje** tlačítko **dlaždice aplikace** v **aplikace** část [Přístupového panelu aplikací](https://myapps.microsoft.com/) je aktualizovat na poslední známé funkční uživatelské jméno a heslo.

   * Pokud vy nebo jiný správce dostali přihlašovací údaje pro tohoto uživatele, vyhledejte uživatele nebo skupiny přiřazení aplikací tak, že přejdete na **uživatelů a skupin** kartu aplikace výběrem přiřazení a kliknutím na  **Aktualizace přihlašovacích údajů** tlačítko.

-   Požádejte uživatele rozšíření prohlížeče přístupového panelu aktualizovat pomocí následujících kroků v [instalace rozšíření prohlížeče Panel přístupu](#how-to-install-the-access-panel-browser-extension) oddílu.

-   Ujistěte se, že rozšíření prohlížeče přístupového panelu je spuštěné a povolený v prohlížeči uživatele.

-   Ujistěte se, že vaši uživatelé nejsou pokoušel se přihlásit do aplikace na přístupovém panelu při v **privátní, se službou inPrivate nebo anonymní režim**. V těchto režimech nepodporuje rozšíření přístupového panelu.

V případě, že předchozí návrhy, nebudou fungovat, může to být případ, že došlo ke změně na straně aplikace, která se má dočasně fungovat, aplikace integraci s Azure AD. Například tato situace může nastat při dodavatele aplikace představuje, že skript na své stránce, která se chová jinak pro ruční a automatizované vstup, který spustí automatizované integrace, jako jsou vlastní, přerušení. V mnoha případech naštěstí Microsoft můžete pracovat s dodavatele aplikací jak tyto problémy rychle vyřešit.

Zatímco Microsoft technologiemi, které automaticky rozpoznat, kdy se přerušit integrace aplikací, nemusí být možné najít problémy okamžitě nebo problémy může trvat nějakou dobu opravit. Při integraci nefunguje správně, můžete otevřít případ podpory, aby problém odstranil co nejrychleji. 

Kromě toho **kontaktu s dodavatele této aplikace, máte** **odešlete nám** umožní nám práci s nimi nativně své aplikace integrovat s Azure Active Directory. Dodavatel, který můžete poslat [výpis vaší aplikace v galerii aplikací Azure Active Directory](../develop/howto-app-gallery-listing.md) zobrazíte jejich spuštění.

## <a name="the-extension-works-in-chrome-and-firefox-but-not-in-internet-explorer"></a>Toto rozšíření funguje v Chrome a Firefox, ale ne v aplikaci Internet Explorer

Existují dvě hlavní příčiny tohoto problému:

-   V závislosti na nastavení zabezpečení v Internet Exploreru povolená, pokud web není součástí **zóny Důvěryhodné**, někdy skriptu blokovat spuštění aplikace.

  *  Tento problém vyřešit, požádejte uživatele, aby **přidat web aplikace** k **Důvěryhodné servery** seznamu v rámci jejich **nastavení zabezpečení aplikace Internet Explorer**. Uživatelům můžete odeslat [přidání webu do seznamu důvěryhodných webů](https://answers.microsoft.com/en-us/ie/forum/ie9-windows_7/how-do-i-add-a-site-to-my-trusted-sites-list/98cc77c8-b364-e011-8dfc-68b599b31bf5) najdete podrobné pokyny.

-   Ve výjimečných případech ověření zabezpečení aplikace Internet Explorer může způsobit stránky načítají se pomaleji než provádění skriptu.

   * Bohužel této situaci může lišit podle verze prohlížeče, rychlost počítače nebo webu nenavštívil. V takovém případě doporučujeme budete kontaktovat podporu, abychom mohli problém integrace pro tuto konkrétní aplikaci.

Kromě toho **kontaktu s dodavatele této aplikace, máte** **odešlete nám** umožní nám práci s nimi nativně své aplikace integrovat s Azure Active Directory. Dodavatel, který můžete poslat [výpis vaší aplikace v galerii aplikací Azure Active Directory](../develop/howto-app-gallery-listing.md) zobrazíte jejich spuštění.

## <a name="check-if-the-applications-login-page-has-changed-recently-or-requires-an-additional-field"></a>Zkontrolujte, jestli má nedávno změnili přihlašovací stránku vaší aplikace, nebo vyžaduje další pole

Pokud na přihlašovací stránku aplikace došlo ke změně výrazně, někdy to způsobí, že naše integrace pro přerušení. Příkladem tohoto je, pokud dodavatele aplikace přidá pole přihlášení, test captcha nebo ověřování službou Multi-Factor Authentication do jejich prostředí. V mnoha případech naštěstí Microsoft můžete pracovat s dodavatele aplikací jak tyto problémy rychle vyřešit.

Zatímco Microsoft technologiemi, které automaticky rozpoznat, kdy se přerušit integrace aplikací, nemusí být možné najít problémy okamžitě nebo problémy může trvat nějakou dobu opravit. Při integraci nefunguje správně, můžete otevřít případ podpory, aby problém odstranil co nejrychleji. 

Kromě toho **kontaktu s dodavatele této aplikace, máte** **odešlete nám** umožní nám práci s nimi nativně své aplikace integrovat s Azure Active Directory. Dodavatel, který můžete poslat [výpis vaší aplikace v galerii aplikací Azure Active Directory](../develop/howto-app-gallery-listing.md) zobrazíte jejich spuštění.

## <a name="how-to-install-the-access-panel-browser-extension"></a>Postup instalace rozšíření prohlížeče Panel přístupu

K instalaci rozšíření prohlížeče panelu přístup, postupujte podle následujících kroků:

1.  Otevřít [přístupového panelu](https://myapps.microsoft.com) v jednom z podporovaných prohlížečů a přihlaste se jako **uživatele** ve službě Azure AD.

2.  Klikněte na tlačítko **heslem jednotného přihlašování aplikace** na přístupovém panelu.

3.  V příkazovém řádku s výzvou k instalaci softwaru vyberte **nainstalovat**.

4.  Založené na prohlížeči, budete přesměrováni na odkaz ke stažení. **Přidat** rozšíření do prohlížeče.

5.  Pokud vás prohlížeč vyzve, vyberte buď **povolit** nebo **povolit** rozšíření.

6.  Po instalaci **restartovat** relace prohlížeče.

7.  Přihlaste se na přístupovém panelu a zobrazit, pokud můžete **spuštění** vaše heslo jednotného přihlašování aplikace

Rozšíření pro Chrome a Firefox lze také stáhnout z přímé odkazy níže:

-   [Rozšíření Chrome přístupového panelu](https://chrome.google.com/webstore/detail/access-panel-extension/ggjhpefgjjfobnfoldnjipclpcfbgbhl)

-   [Rozšíření Firefox přístupového panelu](https://addons.mozilla.org/firefox/addon/access-panel-extension/)

## <a name="next-steps"></a>Další postup
[Poskytovat jednotné přihlašování do aplikací pomocí Proxy aplikace](application-proxy-configure-single-sign-on-with-kcd.md)

