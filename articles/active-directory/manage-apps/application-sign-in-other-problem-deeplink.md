---
title: Problémy s přihlášením k aplikaci pomocí odkazu deeplink | Microsoft Docs
description: Řešení potíží s přístupem k aplikaci z adresy URL odkazu deeplink pomocí Azure AD
services: active-directory
documentationcenter: ''
author: kenwith
manager: celestedg
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 07/11/2017
ms.author: kenwith
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3c68143dabd9b047a8d6cc37ccac770b7d954656
ms.sourcegitcommit: bc943dc048d9ab98caf4706b022eb5c6421ec459
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/14/2020
ms.locfileid: "84759483"
---
# <a name="problems-signing-in-to-an-application-using-a-deeplink"></a>Problémy s přihlášením k aplikaci pomocí odkazu deeplink

Přístupový panel je webový portál, který umožňuje uživateli s pracovním nebo školním účtem ve službě Azure Active Directory (Azure AD) zobrazovat a spouštět cloudové aplikace, kterým správce Azure AD udělil přístup k systému. 

Tyto aplikace jsou nakonfigurované jménem uživatele na portálu Azure AD. Aplikace musí být správně nakonfigurována a přiřazena uživateli nebo skupině, které je uživatel členem, aby bylo možné aplikaci zobrazit na přístupovém panelu.

Přímé odkazy nebo adresy URL pro přístup uživatelů jsou odkazy, které uživatelé můžou použít pro přístup k svým aplikacím jednotného přihlašování k heslům přímo z jejich panelů adresa URL prohlížeče. Když přejdete na tento odkaz, uživatelé se do aplikace automaticky přihlásí, aniž by museli nejprve přejít na přístupový panel. Jedná se o stejný odkaz, který uživatelé používají pro přístup k těmto aplikacím ze Spouštěče aplikací Office 365.

## <a name="general-issues-to-check-first"></a>Obecné problémy k první kontrole

-   Ujistěte se, že používáte **prohlížeč** , který splňuje minimální požadavky na přístupovém panelu.

-   Ujistěte se, že prohlížeč uživatele přidal adresu URL aplikace do jejích **důvěryhodných webů**.

-   Přesvědčte se, zda je aplikace správně **nakonfigurována** .

-   Ujistěte se, že je **povolený** účet uživatele pro přihlášení.

-   Ujistěte se, že účet uživatele není **uzamčený.**

-   Ujistěte se, že **heslo uživatele není prošlé nebo zapomenuté.**

-   Ujistěte se, že **Multi-Factor Authentication** neblokuje přístup uživatelů.

-   Zajistěte, aby **zásady podmíněného přístupu** nebo zásady **ochrany identit** neblokovaly přístup uživatelů.

-   Ujistěte se, že **kontaktní údaje pro ověření** uživatele jsou aktuální, aby bylo možné vyhovět Multi-Factor Authentication nebo zásadám podmíněného přístupu.

-   Nezapomeňte taky vymazat soubory cookie v prohlížeči a zkusit se znovu přihlásit.

## <a name="checking-the-deeplink"></a>Kontrola odkazu deeplink

Pokud chcete zjistit, jestli máte správný odkaz deeplink, postupujte podle těchto kroků:

1. Otevřete [**Azure Portal**](https://portal.azure.com/) a přihlaste se jako **globální správce** nebo **spolusprávce.**

2. Otevřete **rozšíření Azure Active Directory** kliknutím na **všechny služby** v horní části hlavní navigační nabídky vlevo.

3. Do vyhledávacího pole filtru zadejte **"Azure Active Directory**" a vyberte položku **Azure Active Directory** .

4. v navigační nabídce Azure Active Directory vlevo klikněte na **podnikové aplikace** .

5. Kliknutím na **všechny aplikace** zobrazíte seznam všech aplikací.

   * Pokud se vám nezobrazí aplikace, kterou chcete zobrazit, použijte ovládací prvek **filtru** v horní části **seznamu všechny aplikace** a nastavte možnost **Zobrazit** pro **všechny aplikace.**

6. Otevřete [**Azure Portal**](https://portal.azure.com/) a přihlaste se jako **globální správce** nebo **spolusprávce.**

7. Otevřete **rozšíření Azure Active Directory** kliknutím na **všechny služby** v horní části hlavní navigační nabídky vlevo.

8. Do vyhledávacího pole filtru zadejte **"Azure Active Directory**" a vyberte položku **Azure Active Directory** .

9. v navigační nabídce Azure Active Directory vlevo klikněte na **podnikové aplikace** .

10. Kliknutím na **všechny aplikace** zobrazíte seznam všech aplikací.

    * Pokud se vám nezobrazí aplikace, kterou chcete zobrazit, použijte ovládací prvek **filtru** v horní části **seznamu všechny aplikace** a nastavte možnost **Zobrazit** pro **všechny aplikace.**

11. Vyberte aplikaci, kterou chcete, aby kontrolovala deeplink.

12. Vyhledejte **adresu URL přístupu uživatele**. Váš odkazů deeplink by se měl shodovat s touto adresou URL.

## <a name="how-to-install-the-access-panel-browser-extension"></a>Postup instalace rozšíření prohlížeče přístupového panelu

K instalaci rozšíření prohlížeče přístupového panelu použijte následující postup:

1.  Otevřete [přístupový panel](https://myapps.microsoft.com) v jednom z podporovaných prohlížečů a přihlaste se jako **uživatel** ve službě Azure AD.

2.  na přístupovém panelu klikněte na **aplikaci pro jednotné přihlašování pomocí hesla** .

3.  Na příkazovém řádku s výzvou k instalaci softwaru vyberte **nainstalovat hned**.

4.  Na základě vašeho prohlížeče směrovaného na odkaz ke stažení. **Přidejte** rozšíření do prohlížeče.

5.  Pokud se v prohlížeči zobrazí dotaz, vyberte možnost **Povolit** nebo **Povolit** rozšíření.

6.  Po instalaci **restartujte** relaci prohlížeče.

7.  Přihlaste se na přístupový panel a podívejte se, jestli můžete **spouštět** aplikace pro jednotné přihlašování k heslům.

Můžete si také stáhnout rozšíření pro Chrome a Firefox z těchto přímých odkazů:

-   [Rozšíření přístupového panelu Chrome](https://chrome.google.com/webstore/detail/access-panel-extension/ggjhpefgjjfobnfoldnjipclpcfbgbhl)

-   [Rozšíření přístupového panelu Firefox](https://addons.mozilla.org/firefox/addon/access-panel-extension/)

## <a name="how-to-configure-password-single-sign-on-for-an-azure-ad-gallery-application"></a>Jak nakonfigurovat jednotné přihlašování k heslům pro aplikaci Galerie Azure AD

Pokud chcete nakonfigurovat aplikaci z Galerie Azure AD, musíte:

-   [Přidání aplikace z Galerie Azure AD](#add-an-application-from-the-azure-ad-gallery)

-   [Konfigurace aplikace pro jednotné přihlašování k heslům](#configure-the-application-for-password-single-sign-on)

### <a name="add-an-application-from-the-azure-ad-gallery"></a>Přidání aplikace z Galerie Azure AD

Pokud chcete přidat aplikaci z Galerie Azure AD, postupujte podle těchto kroků:

1.  Otevřete [Azure Portal](https://portal.azure.com) a přihlaste se jako **globální správce** nebo **spolusprávce**.

2.  Otevřete **rozšíření Azure Active Directory** kliknutím na **všechny služby** v horní části hlavní navigační nabídky vlevo.

3.  Do vyhledávacího pole filtru zadejte **"Azure Active Directory**" a vyberte položku **Azure Active Directory** .

4.  v navigační nabídce Azure Active Directory vlevo klikněte na **podnikové aplikace** .

5.  klikněte na tlačítko **Přidat** v pravém horním rohu podokna **podnikové aplikace** .

6.  Do textového pole **Zadejte název** z části **Přidat z Galerie** zadejte název aplikace.

7.  Vyberte aplikaci, kterou chcete nakonfigurovat pro jednotné přihlašování.

8.  Před přidáním aplikace můžete změnit její název z textového pole **název** .

9.  Chcete-li přidat aplikaci, klikněte na tlačítko **Přidat**.

Po krátké době se vám zobrazí podokno konfigurace aplikace.

### <a name="configure-the-application-for-password-single-sign-on"></a>Konfigurace aplikace pro jednotné přihlašování k heslům

Pro konfiguraci jednotného přihlašování pro aplikaci použijte následující postup:

1. Otevřete [**Azure Portal**](https://portal.azure.com/) a přihlaste se jako **globální správce** nebo **spolusprávce.**

2. Otevřete **rozšíření Azure Active Directory** kliknutím na **všechny služby** v horní části hlavní navigační nabídky vlevo.

3. Do vyhledávacího pole filtru zadejte **"Azure Active Directory**" a vyberte položku **Azure Active Directory** .

4. v navigační nabídce Azure Active Directory vlevo klikněte na **podnikové aplikace** .

5. Kliknutím na **všechny aplikace** zobrazíte seznam všech aplikací.

   * Pokud se vám nezobrazí aplikace, kterou chcete zobrazit, použijte ovládací prvek **filtru** v horní části **seznamu všechny aplikace** a nastavte možnost **Zobrazit** pro **všechny aplikace.**

6. Vyberte aplikaci, pro kterou chcete nakonfigurovat jednotné přihlašování.

7. Po načtení aplikace klikněte na tlačítko **jednotného přihlašování** z nabídky navigace na levé straně aplikace.

8. Vyberte možnost režim **přihlašování založené na heslech.**

9. [Přiřaďte uživatele k aplikaci](#how-to-assign-a-user-to-an-application-directly).

10. Kromě toho můžete přihlašovací údaje jménem uživatele zadat také tak, že vyberete řádky uživatelů a kliknete na **Aktualizovat přihlašovací údaje** a zadáte uživatelské jméno a heslo jménem uživatele. V opačném případě se uživatelům zobrazí výzva k zadání přihlašovacích údajů sami při spuštění.

## <a name="how-to-configure-password-single-sign-on-for-a-non-gallery-application"></a>Jak nakonfigurovat jednotné přihlašování k heslům pro aplikaci mimo galerii

Pokud chcete nakonfigurovat aplikaci z Galerie Azure AD, musíte:

-   [Přidat aplikaci mimo galerii](#add-a-non-gallery-application)

-   [Konfigurace aplikace pro jednotné přihlašování k heslům](#configure-the-application-for-password-single-sign-on)

### <a name="add-a-non-gallery-application"></a>Přidat aplikaci mimo galerii

Pokud chcete přidat aplikaci z Galerie Azure AD, postupujte podle těchto kroků:

1.  Otevřete [Azure Portal](https://portal.azure.com) a přihlaste se jako **globální správce** nebo **spolusprávce**.

2.  Otevřete **rozšíření Azure Active Directory** kliknutím na **všechny služby** v horní části hlavní navigační nabídky vlevo.

3.  Do vyhledávacího pole filtru zadejte **"Azure Active Directory**" a vyberte položku **Azure Active Directory** .

4.  v navigační nabídce Azure Active Directory vlevo klikněte na **podnikové aplikace** .

5.  klikněte na tlačítko **Přidat** v pravém horním rohu podokna **podnikové aplikace** .

6.  klikněte na **aplikaci mimo galerii.**

7.  Do textového pole **název** zadejte název vaší aplikace. Vyberte **Přidat.**

Po krátké době se vám zobrazí podokno konfigurace aplikace.

### <a name="configure-the-application-for-password-single-sign-on"></a>Konfigurace aplikace pro jednotné přihlašování k heslům

Pro konfiguraci jednotného přihlašování pro aplikaci použijte následující postup:

1.  Otevřete [**Azure Portal**](https://portal.azure.com/) a přihlaste se jako **globální správce** nebo **spolusprávce.**

2.  Otevřete **rozšíření Azure Active Directory** kliknutím na **všechny služby** v horní části hlavní navigační nabídky vlevo.

3.  Do vyhledávacího pole filtru zadejte **"Azure Active Directory**" a vyberte položku **Azure Active Directory** .

4.  v navigační nabídce Azure Active Directory vlevo klikněte na **podnikové aplikace** .

5.  Kliknutím na **všechny aplikace** zobrazíte seznam všech aplikací.

    1.  Pokud se vám nezobrazí aplikace, kterou chcete zobrazit, použijte ovládací prvek **filtru** v horní části **seznamu všechny aplikace** a nastavte možnost **Zobrazit** pro **všechny aplikace.**

6.  Vyberte aplikaci, pro kterou chcete nakonfigurovat jednotné přihlašování.

7.  Po načtení aplikace klikněte na tlačítko **jednotného přihlašování** z nabídky navigace na levé straně aplikace.

8.  Vyberte možnost režim **přihlašování založené na heslech.**

9.  Zadejte **přihlašovací adresu URL**, adresu URL, kam uživatelé zadají své uživatelské jméno a heslo, aby se mohli přihlásit. Ujistěte se, že jsou přihlašovací pole na adrese URL viditelná.

10. Přiřaďte uživatele k aplikaci.

11. Kromě toho můžete přihlašovací údaje jménem uživatele zadat také tak, že vyberete řádky uživatelů a kliknete na **Aktualizovat přihlašovací údaje** a zadáte uživatelské jméno a heslo jménem uživatele. V opačném případě se uživatelům zobrazí výzva k zadání přihlašovacích údajů sami při spuštění.

## <a name="how-to-assign-a-user-to-an-application-directly"></a>Postup přiřazení uživatele k aplikaci přímo

Chcete-li přiřadit jednoho nebo více uživatelů k aplikaci přímo, postupujte podle následujících kroků:

1. Otevřete [**Azure Portal**](https://portal.azure.com/) a přihlaste se jako **globální správce.**

2. Otevřete **rozšíření Azure Active Directory** kliknutím na **všechny služby** v horní části hlavní navigační nabídky vlevo.

3. Do vyhledávacího pole filtru zadejte **"Azure Active Directory**" a vyberte položku **Azure Active Directory** .

4. v navigační nabídce Azure Active Directory vlevo klikněte na **podnikové aplikace** .

5. Kliknutím na **všechny aplikace** zobrazíte seznam všech aplikací.

   * Pokud se vám nezobrazí aplikace, kterou chcete zobrazit, použijte ovládací prvek **filtru** v horní části **seznamu všechny aplikace** a nastavte možnost **Zobrazit** pro **všechny aplikace.**

6. Ze seznamu vyberte aplikaci, ze které chcete uživatele přiřadit.

7. Po načtení aplikace klikněte na **Uživatelé a skupiny** v nabídce navigace na levé straně aplikace.

8. Kliknutím na tlačítko **Přidat** v horní části seznamu **uživatelů a skupin** otevřete podokno **Přidat přiřazení** .

9. v podokně **Přidat přiřazení** klikněte na selektor **Uživatelé a skupiny** .

10. Do pole **Hledat podle jména nebo e-mailové adresy** zadejte jméno a **příjmení** nebo **e-mailovou adresu** uživatele, kterého se zajímáte o přiřazení.

11. Pokud chcete zobrazit **zaškrtávací políčko**, najeďte myší na **uživatele** v seznamu. Pokud chcete přidat uživatele do **vybraného** seznamu, klikněte na zaškrtávací políčko vedle fotografie profilu nebo loga uživatele.

12. **Volitelné:** Pokud chcete **Přidat více než jednoho uživatele**, zadejte do vyhledávacího pole **jméno nebo e-mailová** adresa další **jméno** nebo **e-mailovou adresu** a kliknutím na zaškrtávací políčko přidejte tohoto uživatele do **vybraného** seznamu.

13. Po dokončení výběru uživatelů klikněte na tlačítko **Vybrat** a přidejte je do seznamu uživatelů a skupin, které chcete přiřadit k aplikaci.

14. **Volitelné:** kliknutím na výběr **role** v podokně **Přidat přiřazení** vyberte roli, kterou chcete přiřadit vybraným uživatelům.

15. Kliknutím na tlačítko **přiřadit** aplikaci přiřadíte vybraným uživatelům.

Po krátké době uživatelé, které jste vybrali, budou moci spouštět tyto aplikace na přístupovém panelu.

## <a name="if-these-troubleshooting-steps-do-not-the-resolve-the-issue"></a>Pokud tyto kroky pro řešení potíží problém nevyřeší. 

Otevřete lístek podpory s následujícími informacemi, pokud jsou k dispozici:

-   ID chyby korelace

-   UPN (uživatelská e-mailová adresa)

-   TenantID

-   Typ prohlížeče

-   Časové pásmo a čas/časový rámec při výskytu chyby

-   Fiddler trasování

## <a name="next-steps"></a>Další kroky
[Zajištění jednotného přihlašování k aplikacím pomocí proxy aplikací](application-proxy-configure-single-sign-on-with-kcd.md)
