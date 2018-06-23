---
title: Problém konfigurace hesla jednotné přihlašování pro aplikaci Galerie Azure AD | Microsoft Docs
description: Pochopení tučné osoby běžné problémy při konfiguraci hesla jednotné přihlašování pro aplikace, které jsou již uveden v galerii aplikací Azure AD
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: barbkess
ms.openlocfilehash: 37c2a50a3aca281596a06e0d666edff05347f660
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "36331151"
---
# <a name="problem-configuring-password-single-sign-on-for-an-azure-ad-gallery-application"></a>Problém konfigurace hesla jednotné přihlašování pro aplikaci Galerie Azure AD

Tento článek vám pomůže pochopit tučné osoby běžné problémy při konfiguraci **heslo jednotné přihlašování** pomocí aplikace pro galerii Azure AD.

## <a name="credentials-are-filled-in-but-the-extension-does-not-submit-them"></a>Přihlašovací údaje jsou vyplněna, ale je odeslat není rozšíření

Tento problém obvykle se stane, když má změnit jejich přihlašovací stránce nedávno Chcete-li přidat pole, změnit identifikátor používaný pro zjišťování pole uživatelské jméno a heslo nebo změnit, jak přihlašování funguje pro svou aplikaci dodavatele aplikace. V mnoha případech naštěstí Microsoft můžete pracovat s dodavateli aplikace rychle vyřešit tyto problémy.

Zatímco Microsoft obsahuje technologie, které automaticky rozpoznat, kdy se integrace přerušení, nemusí být možné najít problémy hned nebo problémy trvat delší dobu opravit. V případě, že pokud jeden z těchto integrace nefunguje správně, otevřete případu podpory, bude co nejdříve opravena.

**Pokud jste v kontaktu s od dodavatele této aplikace,** odesílal naše způsob, Microsoft mohli pracovat s nimi nativně svých aplikací integrovat Azure Active Directory. Můžete odeslat dodavatele, aby [výpis aplikace v galerii aplikací Azure Active Directory](./develop/active-directory-app-gallery-listing.md) získat je spuštěna.

## <a name="credentials-are-filled-in-and-submitted-but-the-page-indicates-the-credentials-are-incorrect"></a>Přihlašovací údaje jsou vyplněny a odeslání, ale stránky označuje, že přihlašovací údaje jsou nesprávné

Chcete-li vyřešit tento problém, vyzkoušejte tyto možnosti:

-   Uživatel, zkuste je napřed **přihlásit k webu aplikace přímo** s přihlašovací údaje uložené pro ně.

  * Pokud přihlášení funguje, pak mít uživatele, klikněte na tlačítko **aktualizovat přihlašovací údaje** na tlačítko **dlaždice aplikace** v **aplikace** části [přístup k aplikaci Panel](https://myapps.microsoft.com/) je aktualizovat na poslední známé funkční uživatelské jméno a heslo.

   * Pokud vy nebo jiný správce pověření pro tohoto uživatele, najděte uživatele nebo skupiny aplikací přiřazení přechodem na **uživatelé a skupiny** aplikace, vyberte přiřazení a klikněte na  **Aktualizujte přihlašovací údaje** tlačítko.

-   Pokud uživatel přiřazenou vlastní pověření, mít uživatel **zkontrolujte, ujistěte se, zda nevypršela platnost hesla v aplikaci** a pokud ano, **aktualizovat heslo, jehož platnost vypršela** po přihlášení k aplikaci přímo.

   * Po aktualizaci hesla v aplikaci, požadavku uživatele na tlačítko **aktualizovat přihlašovací údaje** na tlačítko **dlaždice aplikace** v **aplikace** části [Panel přístupu aplikace](https://myapps.microsoft.com/) je aktualizovat na poslední známé funkční uživatelské jméno a heslo.

   * Pokud vy nebo jiný správce pověření pro tohoto uživatele, najděte uživatele nebo skupiny aplikací přiřazení přechodem na **uživatelé a skupiny** aplikace, vyberte přiřazení a klikněte na  **Aktualizujte přihlašovací údaje** tlačítko.

-   Uživatel aktualizovat rozšíření prohlížeče panel přístupu podle pokynů níže v [postup instalace rozšíření přístup k panelu prohlížeče](#how-to-install-the-access-panel-browser-extension) části.

-   Zkontrolujte, zda rozšíření přístup k panelu prohlížeče běží a je povolený v prohlížeči uživatele.

-   Ujistěte se, že uživatelé nejsou pokouší přihlásit k aplikaci na přístupovém panelu při v **privátní, se službou inPrivate nebo incognito režimu**. Rozšíření přístup k panelu v těchto režimech nepodporuje.

V případě, že návrhy předchozích nefungují, může to být případě, že došlo ke změně na straně aplikace, který je dočasně rozdělit aplikace integraci s Azure AD. Například tato situace může nastat při zavádí dodavatele aplikace skript na jejich stránce, která pracuje odlišně pro ruční vs automatizovaný vstup, který spustí automatizované integrace, jako jsou vlastní, k přerušení. V mnoha případech naštěstí Microsoft můžete pracovat s dodavateli aplikace rychle vyřešit tyto problémy.

Zatímco Microsoft obsahuje technologie, které automaticky rozpoznat, kdy rozdělit integrace aplikací, nemusí být možné najít problémy hned nebo problémy, může trvat nějakou dobu opravit. Když integrační nefunguje správně, můžete otevřít případu podpory získat ji opravil co nejdříve. 

Kromě toho **Pokud jste v kontaktu s od dodavatele této aplikace,** **jejich odeslání naše způsob** , jsme mohli pracovat s nimi nativně svých aplikací integrovat Azure Active Directory. Můžete odeslat dodavatele, aby [výpis aplikace v galerii aplikací Azure Active Directory](./develop/active-directory-app-gallery-listing.md) získat je spuštěna.

## <a name="the-extension-works-in-chrome-and-firefox-but-not-in-internet-explorer"></a>Rozšíření funguje v Chrome a Firefox, ale ne v aplikaci Internet Explorer

Existují dvě hlavní příčiny tohoto problému:

-   V závislosti na nastavení zabezpečení v Internet Exploreru povolená, pokud web není součástí **zóny Důvěryhodné**, někdy naše skriptu blokovat spouštění pro aplikaci.

  *  To vyřešit, vyzvat uživatele k **přidat web aplikace** k **Důvěryhodné servery** seznamu v rámci jejich **nastavení zabezpečení aplikace Internet Explorer**. Můžete odeslat uživatelům [postup přidání serveru do seznamu důvěryhodných serverů](https://answers.microsoft.com/en-us/ie/forum/ie9-windows_7/how-do-i-add-a-site-to-my-trusted-sites-list/98cc77c8-b364-e011-8dfc-68b599b31bf5) podrobné pokyny najdete v článku.

-   Ve výjimečných případech může způsobit ověření zabezpečení aplikace Internet Explorer někdy na pomaleji než provedení naše skriptu načtení stránky.

   * Bohužel této situaci může lišit v závislosti na verze prohlížeče, rychlost počítače nebo navštívené lokality. V takovém případě doporučujeme kontaktovat podporu, takže jsme můžete řešit integrace pro tuto konkrétní aplikaci.

Kromě toho **Pokud jste v kontaktu s od dodavatele této aplikace,** **jejich odeslání naše způsob** , jsme mohli pracovat s nimi nativně svých aplikací integrovat Azure Active Directory. Můžete odeslat dodavatele, aby [výpis aplikace v galerii aplikací Azure Active Directory](./develop/active-directory-app-gallery-listing.md) získat je spuštěna.

## <a name="check-if-the-applications-login-page-has-changed-recently-or-requires-an-additional-field"></a>Zkontrolujte, jestli se nedávno změnila na přihlašovací stránku aplikace, nebo vyžaduje další pole

Výrazně se změnil na přihlašovací stránku aplikace, někdy způsobí, že naše integrace pro přerušení. Příkladem je při dodavatele aplikace přidá pole přihlášení, captcha nebo služby Multi-Factor authentication do jejich prostředí. V mnoha případech naštěstí Microsoft můžete pracovat s dodavateli aplikace rychle vyřešit tyto problémy.

Zatímco Microsoft obsahuje technologie, které automaticky rozpoznat, kdy rozdělit integrace aplikací, nemusí být možné najít problémy hned nebo problémy, může trvat nějakou dobu opravit. Když integrační nefunguje správně, můžete otevřít případu podpory získat ji opravil co nejdříve. 

Kromě toho **Pokud jste v kontaktu s od dodavatele této aplikace,** **jejich odeslání naše způsob** , jsme mohli pracovat s nimi nativně svých aplikací integrovat Azure Active Directory. Můžete odeslat dodavatele, aby [výpis aplikace v galerii aplikací Azure Active Directory](./develop/active-directory-app-gallery-listing.md) získat je spuštěna.

## <a name="how-to-install-the-access-panel-browser-extension"></a>Postup instalace rozšíření přístup k panelu prohlížeče

Chcete-li nainstalovat rozšíření přístup k panelu prohlížeče, postupujte následujícím způsobem:

1.  Otevřete [přístupový Panel](https://myapps.microsoft.com) v jednom z podporovaných prohlížečích a přihlaste se jako **uživatele** ve službě Azure AD.

2.  Klikněte na tlačítko **SSO heslo aplikace** na přístupovém panelu.

3.  V řádku, požádá o instalaci softwaru, vyberte **instalovat nyní**.

4.  Podle toho, prohlížeč, jsou směrované na odkaz ke stažení. **Přidat** rozšíření do prohlížeče.

5.  Pokud prohlížeč zobrazí dotaz, vyberte buď **povolit** nebo **povolit** rozšíření.

6.  Po instalaci **restartujte** relaci prohlížeče.

7.  Přihlaste se do přístupového panelu a zobrazit, pokud můžete **spusťte** aplikace heslo SSO

Rozšíření pro Chrome a Firefox může také stáhnout z přímé odkazy níže:

-   [Rozšíření pro Chrome přístup panely](https://chrome.google.com/webstore/detail/access-panel-extension/ggjhpefgjjfobnfoldnjipclpcfbgbhl)

-   [Rozšíření panelu Firefox přístup](https://addons.mozilla.org/firefox/addon/access-panel-extension/)

## <a name="next-steps"></a>Další postup
[Zadejte jednotné přihlašování pro vaše aplikace s Proxy aplikace](manage-apps/application-proxy-configure-single-sign-on-with-kcd.md)

