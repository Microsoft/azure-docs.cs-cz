---
title: Problémy s konfigurací hesla Přisuzované k šití pro aplikaci Azure AD Gallery
description: Seznamte se s běžnými problémy, kterým lidé čelí při konfiguraci jednotného přihlašování hesla pro aplikace, které jsou už uvedené v Galerii aplikací Azure AD
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/11/2017
ms.author: mimart
ms.collection: M365-identity-device-management
ms.openlocfilehash: e933c82229415a71182096a6aca9a2e535934d89
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77159042"
---
# <a name="problem-configuring-password-single-sign-on-for-an-azure-ad-gallery-application"></a>Problém s konfigurací jednotného přihlašování hesla pro aplikaci Azure AD Gallery

Tento článek vám pomůže pochopit běžné problémy, kterým lidé čelí při konfiguraci **jednotného přihlašování heslem** pomocí aplikace Azure AD Gallery.

## <a name="credentials-are-filled-in-but-the-extension-does-not-submit-them"></a>Pověření jsou vyplněna, ale rozšíření neodešle je

K tomuto problému obvykle dochází, pokud dodavatel aplikace nedávno změnil svou přihlašovací stránku, aby přidal pole, změnil identifikátor používaný pro zjišťování polí uživatelského jména a hesla nebo změnil způsob, jakým prostředí přihlašování funguje pro jejich aplikaci. Naštěstí v mnoha případech může společnost Microsoft spolupracovat s dodavateli aplikací, aby tyto problémy rychle vyřešila.

Zatímco společnost Microsoft má technologie, které automaticky zjišťují, kdy dojde k přerušení integrace, nemusí být možné problémy najít hned nebo problémy nějakou dobu trvat. V případě, že jedna z těchto integrací nefunguje správně, otevřete případ podpory, aby jej bylo možné opravit co nejrychleji.

**Pokud jste v kontaktu s dodavatelem této aplikace,** pošlete jim naši cestu, aby s nimi Microsoft mohl pracovat a nativně integrovat jejich aplikaci s Azure Active Directory. Můžete odeslat dodavatele do [výpisu vaší aplikace v galerii aplikací Azure Active Directory,](../azuread-dev/howto-app-gallery-listing.md) abyste je mohli začít.

## <a name="credentials-are-filled-in-and-submitted-but-the-page-indicates-the-credentials-are-incorrect"></a>Pověření jsou vyplněna a odeslána, ale stránka označuje, že pověření jsou nesprávná

Chcete-li tento problém vyřešit, vyzkoušejte nejprve tyto věci:

- Nechte uživatele nejprve se přihlásit **k webu aplikace přímo** s přihlašovacími údaji uloženými pro ně.

  * Pokud přihlášení funguje, pak klikněte na tlačítko **Aktualizovat přihlašovací údaje** na **dlaždici aplikace** v části **Aplikace** [na panelu Přístup k aplikaci](https://myapps.microsoft.com/) a aktualizujte je na nejnovější známé pracovní uživatelské jméno a heslo.

  * Pokud jste vy nebo jiný správce přiřadili pověření tomuto uživateli, vyhledejte přiřazení aplikace uživatele nebo skupiny tak, že přejdete na kartu **Uživatelé & skupiny** aplikace, vyberete přiřazení a kliknete na tlačítko **Aktualizovat pověření.**

- Pokud uživatel přiřadil vlastní pověření, nechte uživatele **zkontrolovat, zda jeho heslo v aplikaci nevypršelo,** a pokud ano, **aktualizujte jejich heslo, jehož platnost vypršela,** přímým přihlášením k aplikaci.

  * Po aktualizaci hesla v aplikaci požádejte uživatele, aby kliknul na tlačítko **Aktualizovat přihlašovací údaje** na **dlaždici aplikace** v části **Aplikace** na [panelu přístupu k aplikacím](https://myapps.microsoft.com/) a aktualizoval je na nejnovější známé pracovní uživatelské jméno a heslo.

  * Pokud jste vy nebo jiný správce přiřadili pověření tomuto uživateli, vyhledejte přiřazení aplikace uživatele nebo skupiny tak, že přejdete na kartu **Uživatelé & skupiny** aplikace, vyberete přiřazení a kliknete na tlačítko **Aktualizovat pověření.**

- Chcete, aby uživatel aktualizoval rozšíření prohlížeče přístupového panelu podle následujících kroků v části [Jak nainstalovat rozšíření prohlížeče přístupového panelu.](#how-to-install-the-access-panel-browser-extension)

- Ujistěte se, že je v prohlížeči uživatele spuštěno a povoleno rozšíření prohlížeče přístupového panelu.

- Ujistěte se, že se uživatelé nepokoušejí přihlásit k aplikaci z přístupového panelu **v anonymním, nevlastním nebo soukromém režimu**. Rozšíření přístupového panelu není v těchto režimech podporováno.

V případě, že předchozí návrhy nefungují, může to být případ, že došlo ke změně na straně aplikace, která dočasně přerušila integraci aplikace s Azure AD. Například k tomu může dojít, když dodavatel aplikace zavede skript na své stránce, který se chová odlišně pro ruční vs automatizovaný vstup, který způsobí, že automatická integrace, jako je naše vlastní, přerušit. Naštěstí v mnoha případech může společnost Microsoft spolupracovat s dodavateli aplikací, aby tyto problémy rychle vyřešila.

Zatímco společnost Microsoft má technologie, které automaticky zjišťují, kdy se dojde k přerušení integrace aplikací, nemusí být možné problémy najít hned nebo může trvat nějakou dobu, než se problémy opraví. Pokud integrace nefunguje správně, můžete otevřít případ podpory, abyste ji co nejrychleji opravili. 

Kromě toho **pokud jste v kontaktu s dodavatelem této aplikace,** **pošlete jim naši cestu,** abychom s nimi mohli pracovat nativně integrovat jejich aplikaci s Azure Active Directory. Můžete odeslat dodavatele do [výpisu vaší aplikace v galerii aplikací Azure Active Directory,](../azuread-dev/howto-app-gallery-listing.md) abyste je mohli začít.

## <a name="the-extension-works-in-chrome-and-firefox-but-not-in-internet-explorer"></a>Rozšíření funguje v Prohlížeči Chrome a Firefox, ale ne v aplikaci Internet Explorer

Existují dvě hlavní příčiny tohoto problému:

- V závislosti na nastavení zabezpečení povoleném v aplikaci Internet Explorer, pokud web není součástí **důvěryhodné zóny**, je někdy našemu skriptu zablokováno spuštění aplikace.

  *  Chcete-li tento problém vyřešit, dejte pokyn uživateli, aby v rámci nastavení zabezpečení aplikace Internet Explorer **přidal web aplikace** do seznamu Důvěryhodné **servery** . **Internet Explorer security settings** Uživatelům můžete poslat do článku o přidání webu do seznamu [důvěryhodných webů,](https://answers.microsoft.com/en-us/ie/forum/ie9-windows_7/how-do-i-add-a-site-to-my-trusted-sites-list/98cc77c8-b364-e011-8dfc-68b599b31bf5) kde najdete podrobné pokyny.

- Ve výjimečných případech může ověření zabezpečení aplikace Internet Explorer někdy způsobit, že se stránka načítá pomaleji než provádění našeho skriptu.

  * Tato situace se bohužel může lišit v závislosti na verzi prohlížeče, rychlosti počítače nebo navštívené lokalitě. V takovém případě doporučujeme kontaktovat podporu, abychom mohli opravit integraci pro tuto konkrétní aplikaci.

Kromě toho **pokud jste v kontaktu s dodavatelem této aplikace,** **pošlete jim naši cestu,** abychom s nimi mohli pracovat nativně integrovat jejich aplikaci s Azure Active Directory. Můžete odeslat dodavatele do [výpisu vaší aplikace v galerii aplikací Azure Active Directory,](../azuread-dev/howto-app-gallery-listing.md) abyste je mohli začít.

## <a name="check-if-the-applications-login-page-has-changed-recently-or-requires-an-additional-field"></a>Zkontrolujte, zda se přihlašovací stránka aplikace nedávno nezměnila nebo nevyžadovala další pole.

Pokud se přihlašovací stránka aplikace drasticky změnila, někdy to způsobí, že se naše integrace přeruší. Příkladem toho je, když dodavatel aplikace přidá přihlašovací pole, captcha nebo vícefaktorové ověřování jejich prostředí. Naštěstí v mnoha případech může společnost Microsoft spolupracovat s dodavateli aplikací, aby tyto problémy rychle vyřešila.

Zatímco společnost Microsoft má technologie, které automaticky zjišťují, kdy se dojde k přerušení integrace aplikací, nemusí být možné problémy najít hned nebo může trvat nějakou dobu, než se problémy opraví. Pokud integrace nefunguje správně, můžete otevřít případ podpory, abyste ji co nejrychleji opravili. 

Kromě toho **pokud jste v kontaktu s dodavatelem této aplikace,** **pošlete jim naši cestu,** abychom s nimi mohli pracovat nativně integrovat jejich aplikaci s Azure Active Directory. Můžete odeslat dodavatele do [výpisu vaší aplikace v galerii aplikací Azure Active Directory,](../azuread-dev/howto-app-gallery-listing.md) abyste je mohli začít.

## <a name="how-to-install-the-access-panel-browser-extension"></a>Jak nainstalovat rozšíření prohlížeče přístupového panelu

Chcete-li nainstalovat rozšíření prohlížeče přístupového panelu, postupujte podle následujících kroků:

1.  Otevřete [přístupový panel](https://myapps.microsoft.com) v jednom z podporovaných prohlížečů a přihlaste se jako **uživatel** ve svém Azure AD.

2.  klikněte na **aplikaci s heslem a spřimaže** na přístupovém panelu.

3.  V řádku s žádostí o instalaci softwaru vyberte **možnost Nainstalovat .**

4.  Na základě vašeho prohlížeče budete přesměrováni na odkaz ke stažení. **Přidejte** rozšíření do prohlížeče.

5.  Pokud se váš prohlížeč zeptá, vyberte možnost **Povolit** nebo **Povolit** rozšíření.

6.  Po instalaci **restartujte** relaci prohlížeče.

7.  Přihlaste se na přístupový panel a zjistěte, jestli můžete **spustit** aplikace s heslem a semaforem

Rozšíření pro Chrome a Firefox si můžete také stáhnout z přímých odkazů níže:

-   [Rozšíření přístupového panelu Chrome](https://chrome.google.com/webstore/detail/access-panel-extension/ggjhpefgjjfobnfoldnjipclpcfbgbhl)

-   [Rozšíření přístupového panelu Firefoxu](https://addons.mozilla.org/firefox/addon/access-panel-extension/)

## <a name="next-steps"></a>Další kroky
[Poskytněte svým aplikacím jednotné přihlašování pomocí proxy aplikace](application-proxy-configure-single-sign-on-with-kcd.md)

