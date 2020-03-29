---
title: Problémy s přihlášením k aplikaci pomocí deeplink | Dokumenty společnosti Microsoft
description: Řešení problémů s přístupem k aplikaci z adresy URL deeplink pomocí služby Azure AD
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/11/2017
ms.author: mimart
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 44825f32a13db0a221252c042dc9f23ec43a9c8f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "65825415"
---
# <a name="problems-signing-in-to-an-application-using-a-deeplink"></a>Problémy s přihlášením k aplikaci pomocí deeplink

Přístupový panel je webový portál, který umožňuje uživateli s pracovním nebo školním účtem ve službě Azure Active Directory (Azure AD) zobrazit a spustit cloudové aplikace, ke kterým jim správce Azure AD udělil přístup. 

Tyto aplikace jsou konfigurovány jménem uživatele na portálu Azure AD. Aplikace musí být správně nakonfigurována a přiřazena uživateli nebo skupině, jejíž je uživatel členem, aby byla aplikace zobrazována na přístupovém panelu.

Přímé odkazy nebo adresy URL pro přístup uživatelů jsou odkazy, které mohou uživatelé použít pro přístup ke svým aplikacím s heslem a sondou přímo z panelů adres URL svých prohlížečů. Přechodem na tento odkaz budou uživatelé automaticky přihlášeni k aplikaci, aniž by museli nejprve přejít na přístupový panel. Jedná se o stejný odkaz, který uživatelé používají pro přístup k těmto aplikacím ze spouštěče aplikací Office 365.

## <a name="general-issues-to-check-first"></a>Obecné problémy, které je třeba nejprve zkontrolovat

-   Ujistěte se, že používáte **prohlížeč,** který splňuje minimální požadavky na přístupový panel.

-   Zkontrolujte, zda prohlížeč uživatele přidal adresu URL aplikace na důvěryhodné **servery**.

-   Zkontrolujte, zda je aplikace správně **nakonfigurována.**

-   Ujistěte se, že uživatelský účet je **povolen** pro přihlášení.

-   Ujistěte se, že uživatelský účet **není uzamčen.**

-   Ujistěte se, že vypršela platnost hesla uživatele nebo zda **není zapomenuto.**

-   Ujistěte se, že **vícefaktorové ověřování** neblokuje přístup uživatelů.

-   Ujistěte se, že **zásady podmíněného přístupu** nebo **zásady ochrany identity** neblokují přístup uživatelů.

-   Ujistěte se, že jsou informace **o ověřovacím kontaktu** uživatele aktuální, aby bylo možné vynucovat zásady vícefaktorového ověřování nebo podmíněného přístupu.

-   Zkuste také vymazat soubory cookie prohlížeče a znovu se přihlásit.

## <a name="checking-the-deeplink"></a>Kontrola hlubokého odkazu

Chcete-li zkontrolovat, zda máte správný deeplink, postupujte takto:

1. Otevřete [**portál Azure**](https://portal.azure.com/) a přihlaste se jako globální **správce** nebo **spolusprávce.**

2. Otevřete **rozšíření Azure Active Directory** kliknutím na Všechny **služby** v horní části hlavní nabídky navigace vlevo.

3. Do vyhledávacího pole filtru zadejte **"Azure Active Directory"** a vyberte položku **Služby Azure Active Directory.**

4. V levé navigační nabídce Azure Active Directory klikněte na **Podnikové aplikace.**

5. Kliknutím na **Všechny aplikace** zobrazíte seznam všech aplikací.

   * Pokud zde nevidíte aplikaci, kterou chcete zobrazit, použijte ovládací prvek **Filtr** v horní části **seznamu Všechny aplikace** a nastavte možnost **Zobrazit** na **všechny aplikace.**

6. Otevřete [**portál Azure**](https://portal.azure.com/) a přihlaste se jako globální **správce** nebo **spolusprávce.**

7. Otevřete **rozšíření Azure Active Directory** kliknutím na Všechny **služby** v horní části hlavní nabídky navigace vlevo.

8. Do vyhledávacího pole filtru zadejte **"Azure Active Directory"** a vyberte položku **Služby Azure Active Directory.**

9. V levé navigační nabídce Azure Active Directory klikněte na **Podnikové aplikace.**

10. Kliknutím na **Všechny aplikace** zobrazíte seznam všech aplikací.

    * Pokud zde nevidíte aplikaci, kterou chcete zobrazit, použijte ovládací prvek **Filtr** v horní části **seznamu Všechny aplikace** a nastavte možnost **Zobrazit** na **všechny aplikace.**

11. Vyberte aplikaci, pro kterou chcete zkontrolovat deeplink.

12. Vyhledejte adresu **URL přístupu uživatele**. Váš deeplink by měl odpovídat této adrese URL.

## <a name="how-to-install-the-access-panel-browser-extension"></a>Jak nainstalovat rozšíření prohlížeče přístupového panelu

Chcete-li nainstalovat rozšíření prohlížeče přístupového panelu, postupujte takto:

1.  Otevřete [přístupový panel](https://myapps.microsoft.com) v jednom z podporovaných prohlížečů a přihlaste se jako **uživatel** ve svém Azure AD.

2.  klikněte na **aplikaci s heslem a spřimaže** na přístupovém panelu.

3.  V řádku s žádostí o instalaci softwaru vyberte **možnost Nainstalovat .**

4.  Na základě vašeho prohlížeče budete přesměrováni na odkaz ke stažení. **Přidejte** rozšíření do prohlížeče.

5.  Pokud se váš prohlížeč zeptá, vyberte možnost **Povolit** nebo **Povolit** rozšíření.

6.  Po instalaci **restartujte** relaci prohlížeče.

7.  Přihlaste se na přístupový panel a zjistěte, jestli můžete **spustit** aplikace s heslem a semaforem

Rozšíření pro Chrome a Firefox si můžete také stáhnout z těchto přímých odkazů:

-   [Rozšíření přístupového panelu Chrome](https://chrome.google.com/webstore/detail/access-panel-extension/ggjhpefgjjfobnfoldnjipclpcfbgbhl)

-   [Rozšíření přístupového panelu Firefoxu](https://addons.mozilla.org/firefox/addon/access-panel-extension/)

## <a name="how-to-configure-password-single-sign-on-for-an-azure-ad-gallery-application"></a>Jak nakonfigurovat jednotné přihlašování hesla pro aplikaci galerie Azure AD

Chcete-li nakonfigurovat aplikaci z galerie Azure AD, musíte:

-   [Přidání aplikace z galerie Azure AD](#add-an-application-from-the-azure-ad-gallery)

-   [Konfigurace aplikace pro jednotné přihlašování pomocí hesla](#configure-the-application-for-password-single-sign-on)

### <a name="add-an-application-from-the-azure-ad-gallery"></a>Přidání aplikace z galerie Azure AD

Pokud chcete přidat aplikaci z Galerie Azure AD, postupujte takto:

1.  Otevřete [portál Azure](https://portal.azure.com) a přihlaste se jako globální **správce** nebo **spolusprávce**.

2.  Otevřete **rozšíření Azure Active Directory** kliknutím na Všechny **služby** v horní části hlavní nabídky navigace vlevo.

3.  Do vyhledávacího pole filtru zadejte **"Azure Active Directory"** a vyberte položku **Služby Azure Active Directory.**

4.  V levé navigační nabídce Azure Active Directory klikněte na **Podnikové aplikace.**

5.  klikněte na tlačítko **Přidat** v pravém horním rohu v podokně **Podnikové aplikace.**

6.  Do **textového** pole Zadejte název z **oddílu Přidat z galerie** zadejte název aplikace.

7.  Vyberte aplikaci, kterou chcete nakonfigurovat pro jednotné přihlašování.

8.  Před přidáním aplikace můžete změnit její název z textového pole **Název.**

9.  Chcete-li aplikaci přidat, klepněte na tlačítko **Přidat**.

Po krátké době se zobrazí konfigurační podokno aplikace.

### <a name="configure-the-application-for-password-single-sign-on"></a>Konfigurace aplikace pro jednotné přihlašování pomocí hesla

Chcete-li nakonfigurovat jednotné přihlašování pro aplikaci, postupujte takto:

1. Otevřete [**portál Azure**](https://portal.azure.com/) a přihlaste se jako globální **správce** nebo **spolusprávce.**

2. Otevřete **rozšíření Azure Active Directory** kliknutím na Všechny **služby** v horní části hlavní nabídky navigace vlevo.

3. Do vyhledávacího pole filtru zadejte **"Azure Active Directory"** a vyberte položku **Služby Azure Active Directory.**

4. V levé navigační nabídce Azure Active Directory klikněte na **Podnikové aplikace.**

5. Kliknutím na **Všechny aplikace** zobrazíte seznam všech aplikací.

   * Pokud zde nevidíte aplikaci, kterou chcete zobrazit, použijte ovládací prvek **Filtr** v horní části **seznamu Všechny aplikace** a nastavte možnost **Zobrazit** na **všechny aplikace.**

6. Vyberte aplikaci, kterou chcete nakonfigurovat jednotné přihlašování.

7. Po načtení aplikace klikněte **na jednotné přihlašování** z levé navigační nabídky aplikace.

8. Vyberte režim **Přihlašování na základě hesla.**

9. [Přiřazení uživatelů k aplikaci](#how-to-assign-a-user-to-an-application-directly).

10. Kromě toho můžete také zadat pověření jménem uživatele výběrem řádků uživatelů a kliknutím na **aktualizovat pověření** a zadáním uživatelského jména a hesla jménem uživatelů. V opačném případě budou uživatelé při spuštění vyzváni k zadání pověření sami.

## <a name="how-to-configure-password-single-sign-on-for-a-non-gallery-application"></a>Konfigurace jednotného přihlašování hesla pro aplikaci mimo galerii

Chcete-li nakonfigurovat aplikaci z galerie Azure AD, musíte:

-   [Přidání aplikace bez galerie](#add-a-non-gallery-application)

-   [Konfigurace aplikace pro jednotné přihlašování pomocí hesla](#configure-the-application-for-password-single-sign-on)

### <a name="add-a-non-gallery-application"></a>Přidání aplikace bez galerie

Pokud chcete přidat aplikaci z Galerie Azure AD, postupujte takto:

1.  Otevřete [portál Azure](https://portal.azure.com) a přihlaste se jako globální **správce** nebo **spolusprávce**.

2.  Otevřete **rozšíření Azure Active Directory** kliknutím na Všechny **služby** v horní části hlavní nabídky navigace vlevo.

3.  Do vyhledávacího pole filtru zadejte **"Azure Active Directory"** a vyberte položku **Služby Azure Active Directory.**

4.  V levé navigační nabídce Azure Active Directory klikněte na **Podnikové aplikace.**

5.  klikněte na tlačítko **Přidat** v pravém horním rohu v podokně **Podnikové aplikace.**

6.  Klikněte na **Aplikace bez galerie.**

7.  Do textového pole **Název** zadejte název aplikace. Vyberte **Přidat.**

Po krátké době se zobrazí konfigurační podokno aplikace.

### <a name="configure-the-application-for-password-single-sign-on"></a>Konfigurace aplikace pro jednotné přihlašování pomocí hesla

Chcete-li nakonfigurovat jednotné přihlašování pro aplikaci, postupujte takto:

1.  Otevřete [**portál Azure**](https://portal.azure.com/) a přihlaste se jako globální **správce** nebo **spolusprávce.**

2.  Otevřete **rozšíření Azure Active Directory** kliknutím na Všechny **služby** v horní části hlavní nabídky navigace vlevo.

3.  Do vyhledávacího pole filtru zadejte **"Azure Active Directory"** a vyberte položku **Služby Azure Active Directory.**

4.  V levé navigační nabídce Azure Active Directory klikněte na **Podnikové aplikace.**

5.  Kliknutím na **Všechny aplikace** zobrazíte seznam všech aplikací.

    1.  Pokud zde nevidíte aplikaci, kterou chcete zobrazit, použijte ovládací prvek **Filtr** v horní části **seznamu Všechny aplikace** a nastavte možnost **Zobrazit** na **všechny aplikace.**

6.  Vyberte aplikaci, kterou chcete nakonfigurovat jednotné přihlašování.

7.  Po načtení aplikace klikněte **na jednotné přihlašování** z levé navigační nabídky aplikace.

8.  Vyberte režim **Přihlašování na základě hesla.**

9.  Zadejte **přihlašovací adresu URL**, adresu URL, na které uživatelé zadají své uživatelské jméno a heslo pro přihlášení. Ujistěte se, že přihlašovací pole jsou zobrazena na adrese URL.

10. Přiřaďte uživatelé k aplikaci.

11. Kromě toho můžete také zadat pověření jménem uživatele výběrem řádků uživatelů a kliknutím na **aktualizovat pověření** a zadáním uživatelského jména a hesla jménem uživatelů. V opačném případě budou uživatelé při spuštění vyzváni k zadání pověření sami.

## <a name="how-to-assign-a-user-to-an-application-directly"></a>Jak přiřadit uživatele k aplikaci přímo

Chcete-li k aplikaci přiřadit jednoho nebo více uživatelů přímo, postupujte takto:

1. Otevřete [**portál Azure**](https://portal.azure.com/) a přihlaste se jako globální **správce.**

2. Otevřete **rozšíření Azure Active Directory** kliknutím na Všechny **služby** v horní části hlavní nabídky navigace vlevo.

3. Do vyhledávacího pole filtru zadejte **"Azure Active Directory"** a vyberte položku **Služby Azure Active Directory.**

4. V levé navigační nabídce Azure Active Directory klikněte na **Podnikové aplikace.**

5. Kliknutím na **Všechny aplikace** zobrazíte seznam všech aplikací.

   * Pokud zde nevidíte aplikaci, kterou chcete zobrazit, použijte ovládací prvek **Filtr** v horní části **seznamu Všechny aplikace** a nastavte možnost **Zobrazit** na **všechny aplikace.**

6. Ze seznamu vyberte aplikaci, ke které chcete přiřadit uživatele.

7. Po načtení aplikace klikněte na **Uživatelé a skupiny** z levé navigační nabídky aplikace.

8. Kliknutím na tlačítko **Přidat** v horní části seznamu **Uživatelé a skupiny** otevřete podokno **Přidat přiřazení.**

9. V podokně **Přidat přiřazení** klikněte na volič uživatelů **a skupin.**

10. Zadejte **celé jméno** nebo **e-mailovou adresu** uživatele, kterého chcete přiřadit do vyhledávacího pole **vyhledávání podle jména nebo e-mailové adresy.**

11. Najeďte na **uživatele** v seznamu a zvedejte **zaškrtávací políčko**. Chcete-li přidat uživatele do seznamu **Vybrané,** zaškrtněte políčko vedle profilové fotky nebo loga uživatele.

12. **Nepovinné:** Pokud chcete **přidat více uživatelů**, zadejte do vyhledávacího pole Hledat podle jména nebo **e-mailové adresy** jiné celé **jméno** nebo **e-mailovou adresu** a kliknutím na toto políčko přidejte tohoto uživatele do seznamu **Vybrané.**

13. Po dokončení výběru uživatelů je klepnutím na tlačítko **Vybrat** přidejte do seznamu uživatelů a skupin, které mají být přiřazeny k aplikaci.

14. **Volitelné:** Kliknutím na volič **role v** podokně **Přidat přiřazení** vyberte roli, kterou chcete přiřadit vybraným uživatelům.

15. Klepnutím na tlačítko **Přiřadit** přiřadíte aplikaci vybraným uživatelům.

Po krátké době budou moci vybraní uživatelé tyto aplikace spustit na přístupovém panelu.

## <a name="if-these-troubleshooting-steps-do-not-the-resolve-the-issue"></a>Pokud tyto kroky řešení potíží problém nevyřeší. 

otevřete lístek podpory s následujícími informacemi, pokud jsou k dispozici:

-   ID chyby korelace

-   UPN (e-mailová adresa uživatele)

-   ID tenanta

-   Typ prohlížeče

-   Časové pásmo a časový rámec během chyby

-   Fiddler stopy

## <a name="next-steps"></a>Další kroky
[Poskytněte svým aplikacím jednotné přihlašování pomocí proxy aplikace](application-proxy-configure-single-sign-on-with-kcd.md)
