---
title: Problémy s konfigurací jednotného přihlašování k heslu pro aplikaci Galerie Azure AD
description: Seznamte se s běžnými problémy, které uživatelé čelí při konfiguraci jednotného přihlašování k heslům pro aplikace, které jsou už uvedené v galerii aplikací Azure AD.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "77159042"
---
# <a name="problem-configuring-password-single-sign-on-for-an-azure-ad-gallery-application"></a>Problém s konfigurací jednotného přihlašování k heslu pro aplikaci Galerie Azure AD

Tento článek vám pomůže pochopit běžné problémy, které lidé čelí při konfiguraci **jednotného přihlašování pomocí hesel** pomocí aplikace Galerie Azure AD.

## <a name="credentials-are-filled-in-but-the-extension-does-not-submit-them"></a>Přihlašovací údaje jsou vyplněné, ale rozšíření je neodesílají.

K tomuto problému obvykle dochází v případě, že dodavatel aplikace změnil svou přihlašovací stránku nedávno, aby přidal pole, změnil identifikátor používaný k detekci polí uživatelského jména a hesla nebo změnil způsob, jakým funguje přihlašování pro aplikaci. Naštěstí může společnost Microsoft v mnoha instancích spolupracovat s dodavateli aplikací a rychle tyto problémy vyřešit.

I když má společnost Microsoft technologie, aby automaticky zjistily, kdy integrace přeruší, nemusí být možné tyto problémy najít hned, jinak problémy vyřeší. V případě, že některá z těchto integrací nefunguje správně, otevřete případ podpory, aby bylo možné je opravit co nejrychleji.

**Pokud se obrátíte na dodavatele této aplikace,** pošlete jim náš způsob, aby Microsoft mohl s nimi pracovat, aby nativně integroval svou aplikaci s Azure Active Directory. Dodavatele můžete odeslat do [výpisu vaší aplikace v Azure Active Directory Galerie aplikací](../azuread-dev/howto-app-gallery-listing.md) , abyste je mohli začít.

## <a name="credentials-are-filled-in-and-submitted-but-the-page-indicates-the-credentials-are-incorrect"></a>Přihlašovací údaje jsou vyplněné a odeslané, ale stránka indikuje, že přihlašovací údaje nejsou správné.

Chcete-li tento problém vyřešit, vyzkoušejte nejprve tyto věci:

- Nejdřív se uživatel pokusí **přihlásit k webu aplikace přímo** s přihlašovacími údaji, které jsou pro ně uložené.

  * Pokud se přihlásíte, klikněte na tlačítko **Aktualizovat přihlašovací údaje** na **dlaždici aplikace** v části **aplikace** na [panelu přístup k aplikaci](https://myapps.microsoft.com/) a aktualizujte je na nejnovější známé funkční uživatelské jméno a heslo.

  * Pokud jste vy nebo jiný správce přiřadili přihlašovací údaje pro tohoto uživatele, vyhledejte přiřazení aplikace uživateli nebo skupině, a to tak, že přejdete na kartu **uživatelé & skupiny** aplikace, vyberete přiřazení a kliknete na tlačítko **Aktualizovat přihlašovací údaje** .

- Pokud uživatel přiřadil svoje vlastní přihlašovací údaje, musí **zkontrolovat, jestli v aplikaci nevypršela platnost hesla** , a pokud ano, **aktualizovat heslo s vypršenou platností** tak, že se přihlásí přímo k aplikaci.

  * Až se v aplikaci aktualizuje heslo, požádejte uživatele, aby po kliknutí na tlačítko **aktualizovat pověření** na **dlaždici aplikace** **v části aplikace** v [panelu přístup k aplikaci](https://myapps.microsoft.com/) aktualizoval na nejnovější známé funkční uživatelské jméno a heslo.

  * Pokud jste vy nebo jiný správce přiřadili přihlašovací údaje pro tohoto uživatele, vyhledejte přiřazení aplikace uživateli nebo skupině, a to tak, že přejdete na kartu **uživatelé & skupiny** aplikace, vyberete přiřazení a kliknete na tlačítko **Aktualizovat přihlašovací údaje** .

- Pomocí následujících kroků v části [Postup instalace rozšíření prohlížeče přístupového panelu](#how-to-install-the-access-panel-browser-extension) aktualizujte uživatele tak, aby aktualizovali rozšíření prohlížeče přístupového panelu.

- Zajistěte, aby v prohlížeči uživatele bylo spuštěné a povolené rozšíření prohlížeče přístupového panelu.

- Ujistěte se, že se vaši uživatelé nesnaží přihlašovat k aplikaci z přístupového panelu v **anonymním, InPrivate nebo privátním režimu**. V těchto režimech není rozšíření přístupového panelu podporováno.

V případě, že předchozí návrhy nefungují, může to být případ, kdy došlo ke změně na straně aplikace, která dočasně přerušila integraci aplikace s Azure AD. K tomu může dojít například v případě, že dodavatel aplikace zavádí skript na své stránce, který se chová jinak jako ruční vs automatizované zadání, což způsobí, že automatizovaná integrace, jako je naše vlastní, přeruší. Naštěstí může společnost Microsoft v mnoha instancích spolupracovat s dodavateli aplikací a rychle tyto problémy vyřešit.

I když má společnost Microsoft technologie, aby automaticky zjistily, kdy integrace aplikace přeruší, nemusí být možné tyto problémy najít hned, jinak může opravit problémy. Pokud integrace nefunguje správně, můžete otevřít případ podpory, abyste ho mohli co nejrychleji opravit. 

V případě, že se **s dodavatelem této aplikace obrátíte,** **pošlete je našemu způsobu** , abychom s nimi mohli s nimi pracovat nativně integrací aplikace s Azure Active Directory. Dodavatele můžete odeslat do [výpisu vaší aplikace v Azure Active Directory Galerie aplikací](../azuread-dev/howto-app-gallery-listing.md) , abyste je mohli začít.

## <a name="the-extension-works-in-chrome-and-firefox-but-not-in-internet-explorer"></a>Rozšíření funguje v Chrome a Firefox, ale ne v Internet Exploreru.

Existují dva hlavní příčiny tohoto problému:

- V závislosti na nastavení zabezpečení povoleném v aplikaci Internet Explorer, pokud web není součástí **důvěryhodné zóny**, je někdy pro něj blokováno spuštění skriptu.

  *  Chcete-li tento problém vyřešit, poskytněte uživateli pokyn k **Přidání webu aplikace** do seznamu **důvěryhodných webů** v rámci jejich **nastavení zabezpečení aplikace Internet Explorer**. Můžete odeslat uživatele do [seznamu Přidání webu do seznamu Důvěryhodné weby](https://answers.microsoft.com/en-us/ie/forum/ie9-windows_7/how-do-i-add-a-site-to-my-trusted-sites-list/98cc77c8-b364-e011-8dfc-68b599b31bf5) , kde najdete podrobné pokyny.

- Ve výjimečných případech může ověřování zabezpečení aplikace Internet Explorer někdy způsobit pomalejší načítání stránky, než je provádění našeho skriptu.

  * Tato situace se bohužel může lišit v závislosti na verzi prohlížeče, rychlosti počítače nebo navštíveném webu. V takovém případě doporučujeme, abyste kontaktovali podporu, abychom mohli opravit integraci pro tuto konkrétní aplikaci.

V případě, že se **s dodavatelem této aplikace obrátíte,** **pošlete je našemu způsobu** , abychom s nimi mohli s nimi pracovat nativně integrací aplikace s Azure Active Directory. Dodavatele můžete odeslat do [výpisu vaší aplikace v Azure Active Directory Galerie aplikací](../azuread-dev/howto-app-gallery-listing.md) , abyste je mohli začít.

## <a name="check-if-the-applications-login-page-has-changed-recently-or-requires-an-additional-field"></a>Zkontroluje, jestli se přihlašovací stránka aplikace v poslední době změnila, nebo jestli vyžaduje další pole.

Pokud se přihlašovací stránka aplikace významně změnila, někdy to způsobí, že naše integrace přeruší. Příkladem je, že dodavatel aplikace přidá do svých prostředí přihlašovací pole, CAPTCHA nebo Multi-Factor Authentication. Naštěstí může společnost Microsoft v mnoha instancích spolupracovat s dodavateli aplikací a rychle tyto problémy vyřešit.

I když má společnost Microsoft technologie, aby automaticky zjistily, kdy integrace aplikace přeruší, nemusí být možné tyto problémy najít hned, jinak může opravit problémy. Pokud integrace nefunguje správně, můžete otevřít případ podpory, abyste ho mohli co nejrychleji opravit. 

V případě, že se **s dodavatelem této aplikace obrátíte,** **pošlete je našemu způsobu** , abychom s nimi mohli s nimi pracovat nativně integrací aplikace s Azure Active Directory. Dodavatele můžete odeslat do [výpisu vaší aplikace v Azure Active Directory Galerie aplikací](../azuread-dev/howto-app-gallery-listing.md) , abyste je mohli začít.

## <a name="how-to-install-the-access-panel-browser-extension"></a>Postup instalace rozšíření prohlížeče přístupového panelu

Chcete-li nainstalovat rozšíření prohlížeče přístupového panelu, postupujte podle následujících kroků:

1.  Otevřete [přístupový panel](https://myapps.microsoft.com) v jednom z podporovaných prohlížečů a přihlaste se jako **uživatel** ve službě Azure AD.

2.  na přístupovém panelu klikněte na **aplikaci pro jednotné přihlašování pomocí hesla** .

3.  Na příkazovém řádku s výzvou k instalaci softwaru vyberte **nainstalovat hned**.

4.  Na základě vašeho prohlížeče budete přesměrováni na odkaz ke stažení. **Přidejte** rozšíření do prohlížeče.

5.  Pokud se v prohlížeči zobrazí dotaz, vyberte možnost **Povolit** nebo **Povolit** rozšíření.

6.  Po instalaci **restartujte** relaci prohlížeče.

7.  Přihlaste se na přístupový panel a podívejte se, jestli můžete **spouštět** aplikace pro jednotné přihlašování k heslům.

Rozšíření pro Chrome a Firefox si můžete stáhnout také z přímých odkazů níže:

-   [Rozšíření přístupového panelu Chrome](https://chrome.google.com/webstore/detail/access-panel-extension/ggjhpefgjjfobnfoldnjipclpcfbgbhl)

-   [Rozšíření přístupového panelu Firefox](https://addons.mozilla.org/firefox/addon/access-panel-extension/)

## <a name="next-steps"></a>Další kroky
[Zajištění jednotného přihlašování k aplikacím pomocí proxy aplikací](application-proxy-configure-single-sign-on-with-kcd.md)

