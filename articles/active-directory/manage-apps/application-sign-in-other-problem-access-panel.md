---
title: Problémy s přihlášením k aplikaci z přístupového panelu | Dokumenty společnosti Microsoft
description: Řešení problémů s přístupem k aplikaci z přístupového panelu Microsoft Azure AD na myapps.microsoft.com
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
ms.reviewer: japere
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3b93ee38666b93253c7cda6c756d4f58daaea236
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74082146"
---
# <a name="problems-signing-in-to-an-application-from-the-access-panel"></a>Problémy s přihlášením k aplikaci z přístupového panelu

Přístupový panel je webový portál, který umožňuje uživateli s pracovním nebo školním účtem ve službě Azure Active Directory (Azure AD) zobrazit a spustit cloudové aplikace, ke kterým jim správce Azure AD udělil přístup. 

Tyto aplikace jsou konfigurovány jménem uživatele na portálu Azure AD. Aplikace musí být správně nakonfigurována a přiřazena uživateli nebo skupině, jejíž je uživatel členem, aby byla aplikace zobrazována na přístupovém panelu.

Typ aplikací, které může uživatel vidět, spadá do následujících kategorií:

-   Aplikace Office 365

-   Aplikace Microsoftu a třetích stran nakonfigurované pomocí přihlašování založeného na federaci

-   Aplikace s přihlašování pomocí hesla

-   Aplikace se stávajícími řešeními společného přihlašování

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

## <a name="meeting-browser-requirements-for-the-access-panel"></a>Splnění požadavků prohlížeče na přístupovém panelu

Přístupový panel vyžaduje prohlížeč, který podporuje JavaScript a má povoleno CSS. Chcete-li na přístupovém panelu používat jednotné přihlašování založené na heslech (SSO), musí být rozšíření přístupového panelu nainstalováno v prohlížeči uživatele. Toto rozšíření se stáhne automaticky, když uživatel vybere aplikaci, která je nakonfigurována pro zabezpečení založené na heslech.

Pro heslo založené na přisazože, prohlížeče koncového uživatele může být:

-   Internet Explorer 8, 9, 10, 11 -- ve Windows 7 nebo novějším

-   Microsoft Edge ve Windows 10 Anniversary Edition nebo novějším

-   Chrome – ve Windows 7 nebo novějším a v MacOS X nebo novějším

-   Firefox 26.0 nebo novější – v systému Windows XP SP2 nebo novějším a v systému Mac OS X 10.6 nebo novějším

## <a name="how-to-install-the-access-panel-browser-extension"></a>Jak nainstalovat rozšíření prohlížeče přístupového panelu

Chcete-li nainstalovat rozšíření prohlížeče přístupového panelu, postupujte podle následujících kroků:

1.  Otevřete [přístupový panel](https://myapps.microsoft.com) v jednom z podporovaných prohlížečů a přihlaste se jako **uživatel** ve svém Azure AD.

2.  Klikněte na **aplikaci hesel ASO** na přístupovém panelu.

3.  V řádku s žádostí o instalaci softwaru vyberte **možnost Nainstalovat .**

4.  Na základě vašeho prohlížeče budete přesměrováni na odkaz ke stažení. **Přidejte** rozšíření do prohlížeče.

5.  Pokud se váš prohlížeč zeptá, vyberte možnost **Povolit** nebo **Povolit** rozšíření.

6.  Po instalaci **restartujte** relaci prohlížeče.

7.  Přihlaste se na přístupový panel a zjistěte, jestli můžete **spustit** aplikace s heslem a semaforem

Rozšíření pro Chrome a Microsoft Edge si můžete také stáhnout z přímých odkazů níže:

-   [Rozšíření přístupového panelu Chrome](https://chrome.google.com/webstore/detail/access-panel-extension/ggjhpefgjjfobnfoldnjipclpcfbgbhl)

-   [Rozšíření přístupového panelu Microsoft Edge](https://www.microsoft.com/p/my-apps-secure-sign-in-extension/9pc9sckkzk84)

## <a name="how-to-configure-federated-single-sign-on-for-an-azure-ad-gallery-application"></a>Jak nakonfigurovat federované jednotné přihlašování pro aplikaci galerie Azure AD

Všechny aplikace v galerii Azure AD povolené s funkcí Enterprise Single Sign-On má podrobný kurz k dispozici. Můžete získat přístup [k seznamu kurzů o tom, jak integrovat aplikace SaaS s Azure Active Directory](https://azure.microsoft.com/documentation/articles/active-directory-saas-tutorial-list/) pro podrobné pokyny krok za krokem.

Chcete-li nakonfigurovat aplikaci z galerie Azure AD, musíte:

-   Přidání aplikace z galerie Azure AD

-   [Konfigurace hodnot metadat aplikace ve službě Azure AD (přihlašování url, identifikátor, adresa URL pro odpověď)](#configure-single-sign-on-for-an-application-from-the-azure-ad-gallery)

-   [Vyberte identifikátor uživatele a přidejte atributy uživatele, které mají být odeslány do aplikace.](#select-user-identifier-and-add-user-attributes-to-be-sent-to-the-application)

-   [Načtení metadat azure a certifikátu služby Azure AD](#download-the-azure-ad-metadata-or-certificate)

-   [Konfigurace hodnot metadat Azure AD v aplikaci (Adresa URL přihlášení, Vydavatel, Adresa URL odhlášení a certifikát)](#configure-single-sign-on-for-an-application-from-the-azure-ad-gallery)

-   Přiřazení uživatelů k aplikaci

### <a name="add-an-application-from-the-azure-ad-gallery"></a>Přidání aplikace z galerie Azure AD

Pokud chcete přidat aplikaci z Galerie Azure AD, postupujte podle následujících kroků:

1.  Otevření [portálu Azure](https://portal.azure.com) a přihlášení jako **globální správce** nebo **spolusprávce**

2.  Otevřete **rozšíření Azure Active Directory** kliknutím na Všechny **služby** v horní části hlavní nabídky navigace vlevo.

3.  Do vyhledávacího pole filtru zadejte **"Azure Active Directory"** a vyberte položku **Služby Azure Active Directory.**

4.  V levé navigační nabídce Azure Active Directory klikněte na **Podnikové aplikace.**

5.  klikněte na tlačítko **Přidat** v pravém horním rohu v podokně **Podnikové aplikace.**

6.  Do **textového** pole Zadejte název z **oddílu Přidat z galerie** zadejte název aplikace.

7.  Vyberte aplikaci, kterou chcete nakonfigurovat pro jednotné přihlašování.

8.  Před přidáním aplikace můžete změnit její název z textového pole **Název.**

9.  Kliknutím na **tlačítko Přidat** přidáte aplikaci.

Po krátké době se zobrazí konfigurační podokno aplikace.

### <a name="configure-single-sign-on-for-an-application-from-the-azure-ad-gallery"></a>Konfigurace jednotného přihlášení pro aplikaci z galerie Azure AD

Chcete-li nakonfigurovat jednotné přihlašování pro aplikaci, postupujte podle následujících kroků:

1. <span id="_Hlk477187909" class="anchor"><span id="_Hlk477001983" class="anchor"></span></span>Otevřete [**portál Azure**](https://portal.azure.com/) a přihlaste se jako globální **správce** nebo **spolusprávce.**

2. Otevřete **rozšíření Azure Active Directory** kliknutím na Všechny **služby** v horní části hlavní nabídky navigace vlevo.

3. Do vyhledávacího pole filtru zadejte **"Azure Active Directory"** a vyberte položku **Služby Azure Active Directory.**

4. V levé navigační nabídce Azure Active Directory klikněte na **Podnikové aplikace.**

5. Kliknutím na **Všechny aplikace** zobrazíte seznam všech aplikací.

   * Pokud zde nevidíte aplikaci, kterou chcete zobrazit, použijte ovládací prvek **Filtr** v horní části **seznamu Všechny aplikace** a nastavte možnost **Zobrazit** na **všechny aplikace.**

6. Vyberte aplikaci, kterou chcete nakonfigurovat jednotné přihlašování.

7. Po načtení aplikace klikněte **na jednotné přihlašování** z levé navigační nabídky aplikace.

8. V rozevíracím souboru **Režim** vyberte **přihlašování založené na SAML.**

9. Zadejte požadované hodnoty do **polí Domény a adresy URL.** Tyto hodnoty byste měli získat od dodavatele aplikace.

   1. Chcete-li nakonfigurovat aplikaci jako sp-inicioval sso, přihlašovací url je požadovaná hodnota. Pro některé aplikace identifikátor je také požadovanou hodnotu.

   2. Chcete-li nakonfigurovat aplikaci jako služby IdP iniciované přihlašovat, adresa URL odpovědi je požadovaná hodnota. Pro některé aplikace identifikátor je také požadovanou hodnotu.

10. **Volitelné:** Pokud chcete zobrazit nepožadované hodnoty, klikněte na **Zobrazit upřesňující nastavení adresy URL.**

11. V **atributech Uživatele**vyberte jedinečný identifikátor pro uživatele v rozevíracím souboru **Identifikátor uživatele.**

12. **Volitelné:** klikněte na **Zobrazit a upravte všechny ostatní atributy uživatele** a upravte atributy, které mají být odeslány do aplikace v tokenu SAML, když se uživatelé přihlásí.

    Přidání atributu:

    1. klepněte na **tlačítko Přidat atribut**. Zadejte **název** a vrozenou **hodnotu** z rozevíracího souboru.

    2. Klikněte na **Uložit.** V tabulce se zobrazí nový atribut.

13. Klikněte na **Konfigurovat &lt;název&gt; aplikace** pro přístup k dokumentaci o konfiguraci jednotného přihlášení v aplikaci. Také máte adresy URL metadat a certifikát potřebný k nastavení přiřazování k šití s aplikací.

14. Kliknutím na **Uložit** uložte konfiguraci.

15. Přiřaďte uživatelé k aplikaci.

### <a name="select-user-identifier-and-add-user-attributes-to-be-sent-to-the-application"></a>Vyberte identifikátor uživatele a přidejte atributy uživatele, které mají být odeslány do aplikace.

Chcete-li vybrat identifikátor uživatele nebo přidat atributy uživatele, postupujte podle následujících kroků:

1. Otevřete [**portál Azure**](https://portal.azure.com/) a přihlaste se jako globální **správce** nebo **spolusprávce.**

2. Otevřete **rozšíření Azure Active Directory** kliknutím na Všechny **služby** v horní části hlavní nabídky navigace vlevo.

3. Do vyhledávacího pole filtru zadejte **"Azure Active Directory"** a vyberte položku **Služby Azure Active Directory.**

4. V levé navigační nabídce Azure Active Directory klikněte na **Podnikové aplikace.**

5. Kliknutím na **Všechny aplikace** zobrazíte seznam všech aplikací.

   * Pokud zde nevidíte aplikaci, kterou chcete zobrazit, použijte ovládací prvek **Filtr** v horní části **seznamu Všechny aplikace** a nastavte možnost **Zobrazit** na **všechny aplikace.**

6. Vyberte aplikaci, kterou jste nakonfigurovali jednotné přihlašování.

7. Po načtení aplikace klikněte **na jednotné přihlašování** z levé navigační nabídky aplikace.

8. V části **Atributy uživatele** vyberte jedinečný identifikátor pro uživatele v rozevíracím článku **Identifikátor uživatele.** Vybraná možnost musí odpovídat očekávané hodnotě v aplikaci k ověření uživatele.

   >[!NOTE]
   >Azure AD vybrat formát pro NameID atribut (Identifikátor uživatele) na základě vybrané hodnoty nebo formát požadovaný aplikací v SAML AuthRequest. Další informace naleznete v článku [Protokol SAML jednotného přihlášení](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-on-protocol-reference#authnrequest) v části NameIDPolicy.
   >
   >

9. Chcete-li přidat atributy uživatele, klepněte na tlačítko **Zobrazit a upravit všechny ostatní atributy uživatele** a upravte atributy, které mají být odeslány do aplikace v tokenu SAML, když se uživatelé přihlásí.

   Přidání atributu:

   1. klepněte na **tlačítko Přidat atribut**. Zadejte **název** a vrozenou **hodnotu** z rozevíracího souboru.

   2. Klikněte na **Uložit.** V tabulce se zobrazí nový atribut.

### <a name="download-the-azure-ad-metadata-or-certificate"></a>Stažení metadat nebo certifikátu Azure AD

Pokud chcete stáhnout metadata nebo certifikát aplikace z Azure AD, postupujte podle následujících kroků:

1. Otevřete [**portál Azure**](https://portal.azure.com/) a přihlaste se jako globální **správce** nebo **spolusprávce.**

2. Otevřete **rozšíření Azure Active Directory** kliknutím na Všechny **služby** v horní části hlavní nabídky navigace vlevo.

3. Do vyhledávacího pole filtru zadejte **"Azure Active Directory"** a vyberte položku **Služby Azure Active Directory.**

4. V levé navigační nabídce Azure Active Directory klikněte na **Podnikové aplikace.**

5. Kliknutím na **Všechny aplikace** zobrazíte seznam všech aplikací.

   * Pokud zde nevidíte aplikaci, kterou chcete zobrazit, použijte ovládací prvek **Filtr** v horní části **seznamu Všechny aplikace** a nastavte možnost **Zobrazit** na **všechny aplikace.**

6. Vyberte aplikaci, kterou jste nakonfigurovali jednotné přihlašování.

7. Po načtení aplikace klikněte **na jednotné přihlašování** z levé navigační nabídky aplikace.

8. Přejděte do části **Podpisový certifikát SAML** a klikněte na **Stáhnout** hodnotu sloupce. V závislosti na tom, co aplikace vyžaduje konfiguraci jednotného přihlášení, se zobrazí možnost stažení xml metadat nebo certifikátu.

   Azure AD neposkytuje adresu URL získat metadata. Metadata lze načíst pouze jako soubor XML.

## <a name="how-to-configure-federated-single-sign-on-for-a-non-gallery-application"></a>Konfigurace federovaného jednotného přihlášení pro aplikaci mimo galerii

Chcete-li nakonfigurovat aplikaci bez galerie, musíte mít Azure AD premium a aplikace podporuje SAML 2.0. Další informace o verzích Azure AD najdete na webu [stanovení cen Azure AD](https://azure.microsoft.com/pricing/details/active-directory/).

-   Konfigurace hodnot metadat aplikace ve službě Azure AD (přihlašování url, identifikátor, adresa URL pro odpověď)

-   [Vyberte identifikátor uživatele a přidejte atributy uživatele, které mají být odeslány do aplikace.](#select-user-identifier-and-add-user-attributes-to-be-sent-to-the-application)

-   [Načtení metadat azure a certifikátu služby Azure AD](#download-the-azure-ad-metadata-or-certificate)

-   Konfigurace hodnot metadat Azure AD v aplikaci (Adresa URL přihlášení, Vydavatel, Adresa URL odhlášení a certifikát)

### <a name="configure-the-applications-metadata-values-in-azure-ad-sign-on-url-identifier-reply-url"></a>Konfigurace hodnot metadat aplikace ve službě Azure AD (přihlašování url, identifikátor, adresa URL pro odpověď)

Pokud chcete nakonfigurovat jednotné přihlašování pro aplikaci, která není v galerii Azure AD, postupujte podle následujících kroků:

1. Otevřete [**portál Azure**](https://portal.azure.com/) a přihlaste se jako globální **správce** nebo **spolusprávce.**

2. Otevřete **rozšíření Azure Active Directory** kliknutím na Všechny **služby** v horní části hlavní nabídky navigace vlevo.

3. Do vyhledávacího pole filtru zadejte **"Azure Active Directory"** a vyberte položku **Služby Azure Active Directory.**

4. V levé navigační nabídce Azure Active Directory klikněte na **Podnikové aplikace.**

5. klikněte na tlačítko **Přidat** v pravém horním rohu v podokně **Podnikové aplikace.**

6. V části **Přidat vlastní aplikaci** klikněte na **Aplikace mimo galerii.**

7. Do textového pole **Název** zadejte název aplikace.

8. Kliknutím na **tlačítko Přidat** přidáte aplikaci.

9. Po načtení aplikace klikněte **na jednotné přihlašování** z levé navigační nabídky aplikace.

10. V rozevíracím **nabídce Režim** uyberte **přihlašování založené na SAML.**

11. Zadejte požadované hodnoty do **polí Domény a adresy URL.** Tyto hodnoty byste měli získat od dodavatele aplikace.

    1. Chcete-li nakonfigurovat aplikaci jako službu Zabezpečení iniciované protokolem IdP, zadejte adresu URL odpovědi a identifikátor.

    2. **Nepovinné:** Chcete-li nakonfigurovat aplikaci jako sp-inicioval sso, přihlašovací url je požadovaná hodnota.

12. V **atributech Uživatele**vyberte jedinečný identifikátor pro uživatele v rozevíracím souboru **Identifikátor uživatele.**

13. **Volitelné:** klikněte na **Zobrazit a upravte všechny ostatní atributy uživatele** a upravte atributy, které mají být odeslány do aplikace v tokenu SAML, když se uživatelé přihlásí.

    Přidání atributu:

    1. klepněte na **tlačítko Přidat atribut**. Zadejte **název** a vrozenou **hodnotu** z rozevíracího souboru.

    2. Klikněte na **Uložit.** V tabulce se zobrazí nový atribut.

14. Klikněte na **Konfigurovat &lt;název&gt; aplikace** pro přístup k dokumentaci o konfiguraci jednotného přihlášení v aplikaci. Také máte adresy URL Azure AD a certifikát požadovaný pro aplikaci.

### <a name="select-user-identifier-and-add-user-attributes-to-be-sent-to-the-application"></a>Vyberte identifikátor uživatele a přidejte atributy uživatele, které mají být odeslány do aplikace.

Chcete-li vybrat identifikátor uživatele nebo přidat atributy uživatele, postupujte podle následujících kroků:

1. Otevřete [**portál Azure**](https://portal.azure.com/) a přihlaste se jako globální **správce** nebo **spolusprávce.**

2. Otevřete **rozšíření Azure Active Directory** kliknutím na Všechny **služby** v horní části hlavní nabídky navigace vlevo.

3. Do vyhledávacího pole filtru zadejte **"Azure Active Directory"** a vyberte položku **Služby Azure Active Directory.**

4. V levé navigační nabídce Azure Active Directory klikněte na **Podnikové aplikace.**

5. Kliknutím na **Všechny aplikace** zobrazíte seznam všech aplikací.

   * Pokud zde nevidíte aplikaci, kterou chcete zobrazit, použijte ovládací prvek **Filtr** v horní části **seznamu Všechny aplikace** a nastavte možnost **Zobrazit** na **všechny aplikace.**

6. Vyberte aplikaci, kterou jste nakonfigurovali jednotné přihlašování.

7. Po načtení aplikace klikněte **na jednotné přihlašování** z levé navigační nabídky aplikace.

8. V části **Atributy uživatele** vyberte jedinečný identifikátor pro uživatele v rozevíracím článku **Identifikátor uživatele.** Vybraná možnost musí odpovídat očekávané hodnotě v aplikaci k ověření uživatele.

   >[!NOTE]
   >Azure AD vybrat formát pro NameID atribut (Identifikátor uživatele) na základě vybrané hodnoty nebo formát požadovaný aplikací v SAML AuthRequest. Další informace naleznete v článku [Protokol SAML jednotného přihlášení](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-on-protocol-reference#authnrequest) v části NameIDPolicy.
   >
   >

9. Chcete-li přidat atributy uživatele, klepněte na tlačítko **Zobrazit a upravit všechny ostatní atributy uživatele** a upravte atributy, které mají být odeslány do aplikace v tokenu SAML, když se uživatelé přihlásí.

   Přidání atributu:

   1.klepněte na **tlačítko Přidat atribut**. Zadejte **název** a vrozenou **hodnotu** z rozevíracího souboru.

   2 Klikněte na **Uložit.** V tabulce se zobrazí nový atribut.

### <a name="download-the-azure-ad-metadata-or-certificate"></a>Stažení metadat nebo certifikátu Azure AD

Pokud chcete stáhnout metadata nebo certifikát aplikace z Azure AD, postupujte podle následujících kroků:

1. Otevřete [**portál Azure**](https://portal.azure.com/) a přihlaste se jako globální **správce** nebo **spolusprávce.**

2. Otevřete **rozšíření Azure Active Directory** kliknutím na Všechny **služby** v horní části hlavní nabídky navigace vlevo.

3. Do vyhledávacího pole filtru zadejte **"Azure Active Directory"** a vyberte položku **Služby Azure Active Directory.**

4. V levé navigační nabídce Azure Active Directory klikněte na **Podnikové aplikace.**

5. Kliknutím na **Všechny aplikace** zobrazíte seznam všech aplikací.

   * Pokud zde nevidíte aplikaci, kterou chcete zobrazit, použijte ovládací prvek **Filtr** v horní části **seznamu Všechny aplikace** a nastavte možnost **Zobrazit** na **všechny aplikace.**

6. Vyberte aplikaci, kterou jste nakonfigurovali jednotné přihlašování.

7. Po načtení aplikace klikněte **na jednotné přihlašování** z levé navigační nabídky aplikace.

8. Přejděte do části **Podpisový certifikát SAML** a klikněte na **Stáhnout** hodnotu sloupce. V závislosti na tom, co aplikace vyžaduje konfiguraci jednotného přihlášení, se zobrazí možnost stažení xml metadat nebo certifikátu.

   Azure AD neposkytuje adresu URL získat metadata. Metadata lze načíst pouze jako soubor XML.

## <a name="how-to-configure-password-single-sign-on-for-an-azure-ad-gallery-application"></a>Jak nakonfigurovat jednotné přihlašování hesla pro aplikaci galerie Azure AD

Chcete-li nakonfigurovat aplikaci z galerie Azure AD, musíte:

-   Přidání aplikace z galerie Azure AD

-   Konfigurace aplikace pro jednotné přihlašování pomocí hesla

### <a name="add-an-application-from-the-azure-ad-gallery"></a>Přidání aplikace z galerie Azure AD

Pokud chcete přidat aplikaci z Galerie Azure AD, postupujte podle následujících kroků:

1.  Otevření [portálu Azure](https://portal.azure.com) a přihlášení jako **globální správce** nebo **spolusprávce**

2.  Otevřete **rozšíření Azure Active Directory** kliknutím na Všechny **služby** v horní části hlavní nabídky navigace vlevo.

3.  Do vyhledávacího pole filtru zadejte **"Azure Active Directory"** a vyberte položku **Služby Azure Active Directory.**

4.  V levé navigační nabídce Azure Active Directory klikněte na **Podnikové aplikace.**

5.  klikněte na tlačítko **Přidat** v pravém horním rohu v podokně **Podnikové aplikace.**

6.  Do **textového** pole Zadejte název z **oddílu Přidat z galerie** zadejte název aplikace.

7.  Vyberte aplikaci, pro kterou chcete nakonfigurovat jednotné přihlašování.

8.  Před přidáním aplikace můžete změnit její název z textového pole **Název.**

9.  Kliknutím na **tlačítko Přidat** přidáte aplikaci.

Po krátké době se zobrazí konfigurační podokno aplikace.

### <a name="configure-the-application-for-password-single-sign-on"></a>Konfigurace aplikace pro jednotné přihlašování pomocí hesla

Chcete-li nakonfigurovat jednotné přihlašování pro aplikaci, postupujte podle následujících kroků:

1. Otevřete [**portál Azure**](https://portal.azure.com/) a přihlaste se jako globální **správce** nebo **spolusprávce.**

2. Otevřete **rozšíření Azure Active Directory** kliknutím na Všechny **služby** v horní části hlavní nabídky navigace vlevo.

3. Do vyhledávacího pole filtru zadejte **"Azure Active Directory"** a vyberte položku **Služby Azure Active Directory.**

4. V levé navigační nabídce Azure Active Directory klikněte na **Podnikové aplikace.**

5. Kliknutím na **Všechny aplikace** zobrazíte seznam všech aplikací.

   * Pokud zde nevidíte aplikaci, kterou chcete zobrazit, použijte ovládací prvek **Filtr** v horní části **seznamu Všechny aplikace** a nastavte možnost **Zobrazit** na **všechny aplikace.**

6. Vyberte aplikaci, kterou chcete nakonfigurovat jednotné přihlašování.

7. Po načtení aplikace klikněte **na jednotné přihlašování** z levé navigační nabídky aplikace.

8. Vyberte režim **Přihlašování na základě hesla.**

9. Přiřaďte uživatelé k aplikaci.

10. Kromě toho můžete také zadat pověření jménem uživatele výběrem řádků uživatelů a kliknutím na **aktualizovat pověření** a zadáním uživatelského jména a hesla jménem uživatelů. V opačném případě budou uživatelé při spuštění vyzváni k zadání pověření sami.

## <a name="how-to-configure-password-single-sign-on-for-a-non-gallery-application"></a>Konfigurace jednotného přihlašování hesla pro aplikaci mimo galerii

Chcete-li nakonfigurovat aplikaci z galerie Azure AD, musíte:

-   [Přidání aplikace bez galerie](#add-a-non-gallery-application)

-   [Konfigurace aplikace pro jednotné přihlašování pomocí hesla](#configure-the-application-for-password-single-sign-on)

### <a name="add-a-non-gallery-application"></a>Přidání aplikace bez galerie

Pokud chcete přidat aplikaci z Galerie Azure AD, postupujte podle následujících kroků:

1.  Otevření [portálu Azure](https://portal.azure.com) a přihlášení jako **globální správce** nebo **spolusprávce**

2.  Otevřete **rozšíření Azure Active Directory** kliknutím na Všechny **služby** v horní části hlavní nabídky navigace vlevo.

3.  Do vyhledávacího pole filtru zadejte **"Azure Active Directory"** a vyberte položku **Služby Azure Active Directory.**

4.  V levé navigační nabídce Azure Active Directory klikněte na **Podnikové aplikace.**

5.  klikněte na tlačítko **Přidat** v pravém horním rohu v podokně **Podnikové aplikace.**

6.  Klikněte na **Aplikace bez galerie.**

7.  Do textového pole **Název** zadejte název aplikace. Vyberte **Přidat.**

Po krátké době budete moci zobrazit konfigurační podokno aplikace.

### <a name="configure-the-application-for-password-single-sign-on"></a>Konfigurace aplikace pro jednotné přihlašování pomocí hesla

Chcete-li nakonfigurovat jednotné přihlašování pro aplikaci, postupujte podle následujících kroků:

1. Otevřete [**portál Azure**](https://portal.azure.com/) a přihlaste se jako globální **správce** nebo **spolusprávce.**

2. Otevřete **rozšíření Azure Active Directory** kliknutím na Všechny **služby** v horní části hlavní nabídky navigace vlevo.

3. Do vyhledávacího pole filtru zadejte **"Azure Active Directory"** a vyberte položku **Služby Azure Active Directory.**

4. V levé navigační nabídce Azure Active Directory klikněte na **Podnikové aplikace.**

5. Kliknutím na **Všechny aplikace** zobrazíte seznam všech aplikací.

   * Pokud zde nevidíte aplikaci, kterou chcete zobrazit, použijte ovládací prvek **Filtr** v horní části **seznamu Všechny aplikace** a nastavte možnost **Zobrazit** na **všechny aplikace.**

6. Vyberte aplikaci, kterou chcete nakonfigurovat jednotné přihlašování.

7. Po načtení aplikace klikněte **na jednotné přihlašování** z levé navigační nabídky aplikace.

8. Vyberte režim **Přihlašování na základě hesla.**

9. Zadejte **přihlašovací adresu URL**. Toto je adresa URL, na které uživatelé zadají své uživatelské jméno a heslo pro přihlášení. Ujistěte se, že přihlašovací pole jsou zobrazena na adrese URL.

10. Přiřaďte uživatelé k aplikaci.

11. Kromě toho můžete také zadat pověření jménem uživatele výběrem řádků uživatelů a kliknutím na **aktualizovat pověření** a zadáním uživatelského jména a hesla jménem uživatelů. V opačném případě budou uživatelé při spuštění vyzváni k zadání pověření sami.

## <a name="how-to-assign-a-user-to-an-application-directly"></a>Jak přiřadit uživatele k aplikaci přímo

Chcete-li k aplikaci přiřadit jednoho nebo více uživatelů přímo, postupujte podle následujících kroků:

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

11. Najeďte na **uživatele** v seznamu a zvedejte **zaškrtávací políčko**. Kliknutím na zaškrtávací políčko vedle profilové fotky nebo loga uživatele přidáte uživatele do seznamu **Vybrané.**

12. **Nepovinné:** Pokud chcete **přidat více uživatelů**, zadejte do vyhledávacího pole Hledat podle jména nebo **e-mailové adresy** jiné celé **jméno** nebo **e-mailovou adresu** a kliknutím na toto políčko přidejte tohoto uživatele do seznamu **Vybrané.**

13. Po dokončení výběru uživatelů je klepnutím na tlačítko **Vybrat** přidejte do seznamu uživatelů a skupin, které mají být přiřazeny k aplikaci.

14. **Volitelné:** Kliknutím na volič **role v** podokně **Přidat přiřazení** vyberte roli, kterou chcete přiřadit vybraným uživatelům.

15. Klepnutím na tlačítko **Přiřadit** přiřadíte aplikaci vybraným uživatelům.

Po krátké době budou moci vybraní uživatelé tyto aplikace spustit na přístupovém panelu.

## <a name="if-these-troubleshooting-steps-do-not-the-resolve-the-issue"></a>Pokud tyto kroky řešení potíží problém nevyřeší

otevřete lístek podpory s následujícími informacemi, pokud jsou k dispozici:

-   ID chyby korelace

-   UPN (e-mailová adresa uživatele)

-   ID tenanta

-   Typ prohlížeče

-   Časové pásmo a časový rámec během chyby

-   Fiddler stopy

## <a name="next-steps"></a>Další kroky
[Poskytněte svým aplikacím jednotné přihlašování pomocí proxy aplikace](application-proxy-configure-single-sign-on-with-kcd.md)

