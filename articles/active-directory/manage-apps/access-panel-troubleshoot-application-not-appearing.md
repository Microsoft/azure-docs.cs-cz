---
title: Přiřazená aplikace se neobjevuje na přístupovém panelu | Dokumenty společnosti Microsoft
description: Poradce při potížích s tím, proč se na přístupovém panelu nezobrazuje aplikace
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
ms.topic: article
ms.date: 09/09/2018
ms.author: mimart
ms.reviwer: japere
ms.collection: M365-identity-device-management
ms.openlocfilehash: 10dfcf337dc75a202e781e931f38783291a72fe7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "67272742"
---
# <a name="an-assigned-application-is-not-appearing-on-the-access-panel"></a>Přiřazená aplikace se nezobrazuje na přístupovém panelu.

Přístupový panel je webový portál, který umožňuje uživateli s pracovním nebo školním účtem ve službě Azure Active Directory (Azure AD) zobrazit a spustit cloudové aplikace, ke kterým jim správce Azure AD udělil přístup. Tyto aplikace jsou konfigurovány jménem uživatele na portálu Azure AD. Aplikace musí být správně nakonfigurována a přiřazena uživateli nebo skupině, jejíž je uživatel členem, aby byla aplikace zobrazována na přístupovém panelu.

Typ aplikací, které může uživatel vidět, spadá do následujících kategorií:

-   Aplikace Office 365

-   Aplikace Microsoftu a třetích stran nakonfigurované pomocí přihlašování založeného na federaci

-   Aplikace s přihlašování pomocí hesla

-   Aplikace se stávajícími řešeními společného přihlašování

## <a name="general-issues-to-check-first"></a>Obecné problémy, které je třeba nejprve zkontrolovat

-   Pokud byla aplikace právě přidána uživateli, zkuste se po několika minutách znovu přihlásit a odhlásit na přístupovém panelu uživatele a zjistěte, zda je aplikace přidána.

-   Pokud byla licence právě odebrána uživateli nebo skupině, může to být jeho členem, může to trvat dlouho, v závislosti na velikosti a složitosti skupiny pro provedené změny. Před přihlášením na přístupový panel vyčkejte více času.

## <a name="problems-related-to-application-configuration"></a>Problémy související s konfigurací aplikace

Aplikace se pravděpodobně nezobrazuje na přístupovém panelu uživatele, protože není správně nakonfigurována. Níže jsou uvedeny některé způsoby řešení problémů souvisejících s konfigurací aplikace:

-   [Jak nakonfigurovat federované jednotné přihlašování pro aplikaci galerie Azure AD](#how-to-configure-federated-single-sign-on-for-an-azure-ad-gallery-application)

-   [Konfigurace federovaného jednotného přihlášení pro aplikaci mimo galerii](#how-to-configure-federated-single-sign-on-for-a-non-gallery-application)

-   [Jak nakonfigurovat aplikaci pro jednotné přihlašování hesla pro aplikaci galerie Azure AD](#how-to-configure-password-single-sign-on-for-a-non-gallery-application)

-   [Jak nakonfigurovat aplikaci pro jednotné přihlašování hesla pro aplikaci mimo galerii](#how-to-configure-password-single-sign-on-for-a-non-gallery-application)

### <a name="how-to-configure-federated-single-sign-on-for-an-azure-ad-gallery-application"></a>Jak nakonfigurovat federované jednotné přihlašování pro aplikaci galerie Azure AD

Všechny aplikace v galerii Azure AD povolené s funkcí Enterprise Single Sign-On mají k dispozici podrobný kurz. Můžete získat přístup [k seznamu kurzů o tom, jak integrovat aplikace SaaS s Azure Active Directory](https://azure.microsoft.com/documentation/articles/active-directory-saas-tutorial-list/) pro podrobné pokyny krok za krokem.

Chcete-li nakonfigurovat aplikaci z galerie Azure AD, musíte:

-   [Přidání aplikace z galerie Azure AD](#add-an-application-from-the-azure-ad-gallery)

-   [Konfigurace hodnot metadat aplikace ve službě Azure AD (přihlašování url, identifikátor, adresa URL pro odpověď)](#configure-single-sign-on-for-an-application-from-the-azure-ad-gallery)

-   [Vyberte identifikátor uživatele a přidejte atributy uživatele, které mají být odeslány do aplikace.](#select-user-identifier-and-add-user-attributes-to-be-sent-to-the-application)

-   [Načtení metadat azure a certifikátu služby Azure AD](#download-the-azure-ad-metadata-or-certificate)

-   [Konfigurace hodnot metadat Azure AD v aplikaci (Adresa URL přihlášení, Vydavatel, Adresa URL odhlášení a certifikát)](#configure-single-sign-on-for-an-application-from-the-azure-ad-gallery)

#### <a name="add-an-application-from-the-azure-ad-gallery"></a>Přidání aplikace z galerie Azure AD

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

Po krátké době budete moci zobrazit konfigurační podokno aplikace.

#### <a name="configure-single-sign-on-for-an-application-from-the-azure-ad-gallery"></a>Konfigurace jednotného přihlášení pro aplikaci z galerie Azure AD

Chcete-li nakonfigurovat jednotné přihlašování pro aplikaci, postupujte podle následujících kroků:

1. Otevřete [**portál Azure**](https://portal.azure.com/) a přihlaste se jako globální **správce** nebo **spolusprávce.**

2. Otevřete **rozšíření Azure Active Directory** kliknutím na Všechny **služby** v horní části hlavní nabídky navigace vlevo.

3. Do vyhledávacího pole filtru zadejte **"Azure Active Directory"** a vyberte položku **Služby Azure Active Directory.**

4. V levé navigační nabídce Azure Active Directory klikněte na **Podnikové aplikace.**

5. Kliknutím na **Všechny aplikace** zobrazíte seznam všech aplikací.

   * Pokud zde nevidíte aplikaci, kterou chcete zobrazit, použijte ovládací prvek **Filtr** v horní části **seznamu Všechny aplikace** a nastavte možnost **Zobrazit** na **všechny aplikace.**

6. Vyberte aplikaci, kterou chcete nakonfigurovat jednotné přihlašování.

7. Po načtení aplikace klikněte **na jednotné přihlašování** z levé navigační nabídky aplikace.

8. V rozevíracím souboru **Režim** vyberte **přihlašování založené na SAML.**

9. Zadejte požadované hodnoty do **polí Domény a adresy URL.** Tyto hodnoty byste měli získat od dodavatele aplikace.

   1. Chcete-li nakonfigurovat aplikaci jako sp-inicioval sso, Přihlašovací url je požadovaná hodnota. Pro některé aplikace identifikátor je také požadovanou hodnotu.

   2. Chcete-li nakonfigurovat aplikaci jako idP iniciované přihlašovat, adresa URL odpovědi je požadovaná hodnota. Pro některé aplikace identifikátor je také požadovanou hodnotu.

10. **Volitelné:** Pokud chcete zobrazit nepožadované hodnoty, klikněte na **Zobrazit upřesňující nastavení adresy URL.**

11. V **atributech Uživatele**vyberte jedinečný identifikátor pro uživatele v rozevíracím souboru **Identifikátor uživatele.**

12. **Volitelné:** klikněte na **Zobrazit a upravte všechny ostatní atributy uživatele** a upravte atributy, které mají být odeslány do aplikace v tokenu SAML, když se uživatelé přihlásí.

    Přidání atributu:

    1. klepněte na **tlačítko Přidat atribut**. Zadejte **název** a vrozenou **hodnotu** z rozevíracího souboru.

    2. klikněte na **Uložit.** V tabulce se zobrazí nový atribut.

13. Klikněte na **Konfigurovat &lt;název&gt; aplikace** pro přístup k dokumentaci o konfiguraci jednotného přihlášení v aplikaci. Také máte adresy URL metadat a certifikát potřebný k nastavení přihlašování k řízení pomocí aplikace.

14. Kliknutím na **Uložit** uložte konfiguraci.

15. Přiřaďte uživatelé k aplikaci.

#### <a name="select-user-identifier-and-add-user-attributes-to-be-sent-to-the-application"></a>Vyberte identifikátor uživatele a přidejte atributy uživatele, které mají být odeslány do aplikace.

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
   >Azure AD vybere formát atributu NameID (identifikátor uživatele) na základě vybrané hodnoty nebo formátu požadovaného aplikací v SAML AuthRequest. Další informace naleznete v článku [Protokol SAML jednotného přihlášení](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-on-protocol-reference#authnrequest) v části NameIDPolicy.
   >
   >

9. Chcete-li přidat atributy uživatele, klepněte na tlačítko **Zobrazit a upravit všechny ostatní atributy uživatele** a upravte atributy, které mají být odeslány do aplikace v tokenu SAML, když se uživatelé přihlásí.

   Přidání atributu:

   1. klepněte na **tlačítko Přidat atribut**. Zadejte **název** a vrozenou **hodnotu** z rozevíracího souboru.

   2. klikněte na **Uložit.** Nový atribut se zobrazí v tabulce.

#### <a name="download-the-azure-ad-metadata-or-certificate"></a>Stažení metadat nebo certifikátu Azure AD

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

### <a name="how-to-configure-federated-single-sign-on-for-a-non-gallery-application"></a>Konfigurace federovaného jednotného přihlášení pro aplikaci mimo galerii

Chcete-li nakonfigurovat aplikaci bez galerie, musíte mít Azure AD premium a aplikace podporuje SAML 2.0. Další informace o verzích Azure AD najdete na webu [stanovení cen Azure AD](https://azure.microsoft.com/pricing/details/active-directory/).

-   [Konfigurace hodnot metadat aplikace ve službě Azure AD (přihlašování url, identifikátor, adresa URL pro odpověď)](#configure-single-sign-on-for-an-application-from-the-azure-ad-gallery)

-   [Vyberte identifikátor uživatele a přidejte atributy uživatele, které mají být odeslány do aplikace.](#select-user-identifier-and-add-user-attributes-to-be-sent-to-the-application)

-   [Načtení metadat azure a certifikátu služby Azure AD](#download-the-azure-ad-metadata-or-certificate)

-   [Konfigurace hodnot metadat Azure AD v aplikaci (Adresa URL přihlášení, Vydavatel, Adresa URL odhlášení a certifikát)](#configure-the-application-for-password-single-sign-on-1)

#### <a name="configure-the-applications-metadata-values-in-azure-ad-sign-on-url-identifier-reply-url"></a>Konfigurace hodnot metadat aplikace ve službě Azure AD (přihlašování url, identifikátor, adresa URL pro odpověď)

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

10. V rozevíracím **souboru Režim** vyberte **přihlašování založené na SAML.**

11. Zadejte požadované hodnoty do **polí Domény a adresy URL.** Tyto hodnoty byste měli získat od dodavatele aplikace.

    1. Chcete-li nakonfigurovat aplikaci jako službu Zabezpečení iniciované protokolem IdP, zadejte adresu URL odpovědi a identifikátor.

    2.  **Nepovinné:** Chcete-li nakonfigurovat aplikaci jako sp-inicioval sso, Přihlašovací url je požadovaná hodnota.

12. V **atributech Uživatele**vyberte jedinečný identifikátor pro uživatele v rozevíracím souboru **Identifikátor uživatele.**

13. **Volitelné:** klikněte na **Zobrazit a upravte všechny ostatní atributy uživatele** a upravte atributy, které mají být odeslány do aplikace v tokenu SAML, když se uživatelé přihlásí.

    Přidání atributu:

    1. klepněte na **tlačítko Přidat atribut**. Zadejte **název** a vrozenou **hodnotu** z rozevíracího souboru.

    2. Klikněte na **Uložit.** V tabulce se zobrazí nový atribut.

14. Klikněte na **Konfigurovat &lt;název&gt; aplikace** pro přístup k dokumentaci o konfiguraci jednotného přihlášení v aplikaci. Také máte adresy URL Azure AD a certifikáty požadované pro aplikaci.

#### <a name="select-user-identifier-and-add-user-attributes-to-be-sent-to-the-application"></a>Vyberte identifikátor uživatele a přidejte atributy uživatele, které mají být odeslány do aplikace.

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
   >Azure AD vybere formát atributu NameID (identifikátor uživatele) na základě vybrané hodnoty nebo formátu požadovaného aplikací v SAML AuthRequest. Další informace naleznete v článku [Protokol SAML jednotného přihlášení](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-on-protocol-reference#authnrequest) v části NameIDPolicy.
   >
   >

9. Chcete-li přidat atributy uživatele, klepněte na tlačítko **Zobrazit a upravit všechny ostatní atributy uživatele** a upravte atributy, které mají být odeslány do aplikace v tokenu SAML, když se uživatelé přihlásí.

   Přidání atributu:

   1. klepněte na **tlačítko Přidat atribut**. Zadejte **název** a vrozenou **hodnotu** z rozevíracího souboru.

   2. Klikněte na **Uložit.** V tabulce se zobrazí nový atribut.

#### <a name="download-the-azure-ad-metadata-or-certificate"></a>Stažení metadat nebo certifikátu Azure AD

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

### <a name="how-to-configure-password-single-sign-on-for-an-azure-ad-gallery-application"></a>Jak nakonfigurovat jednotné přihlašování hesla pro aplikaci galerie Azure AD

Chcete-li nakonfigurovat aplikaci z galerie Azure AD, musíte:

-   [Přidání aplikace z galerie Azure AD](#add-an-application-from-the-azure-ad-gallery)

-   [Konfigurace aplikace pro jednotné přihlašování pomocí hesla](#configure-the-application-for-password-single-sign-on)

#### <a name="add-an-application-from-the-azure-ad-gallery"></a>Přidání aplikace z galerie Azure AD

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

#### <a name="configure-the-application-for-password-single-sign-on"></a>Konfigurace aplikace pro jednotné přihlašování pomocí hesla

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

9. [Přiřazení uživatelů k aplikaci](#how-to-assign-a-user-to-an-application-directly).

10. Kromě toho můžete také zadat pověření jménem uživatele výběrem řádků uživatelů a kliknutím na **aktualizovat pověření** a zadáním uživatelského jména a hesla jménem uživatelů. V opačném případě budou uživatelé při spuštění vyzváni k zadání pověření sami.

### <a name="how-to-configure-password-single-sign-on-for-a-non-gallery-application"></a>Konfigurace jednotného přihlašování hesla pro aplikaci mimo galerii

Chcete-li nakonfigurovat aplikaci z galerie Azure AD, musíte:

-   [Přidání aplikace bez galerie](#add-a-non-gallery-application)

-   [Konfigurace aplikace pro jednotné přihlašování pomocí hesla](#configure-the-application-for-password-single-sign-on)

#### <a name="add-a-non-gallery-application"></a>Přidání aplikace bez galerie

Pokud chcete přidat aplikaci z Galerie Azure AD, postupujte podle následujících kroků:

1.  Otevřete [portál Azure](https://portal.azure.com) a přihlaste se jako globální **správce** nebo **spolusprávce**.

2.  Otevřete **rozšíření Azure Active Directory** kliknutím na Všechny **služby** v horní části hlavní nabídky navigace vlevo.

3.  Do vyhledávacího pole filtru zadejte **"Azure Active Directory"** a vyberte položku **Služby Azure Active Directory.**

4.  V levé navigační nabídce Azure Active Directory klikněte na **Podnikové aplikace.**

5.  klikněte na tlačítko **Přidat** v pravém horním rohu v podokně **Podnikové aplikace.**

6.  Klikněte na **Aplikace bez galerie.**

7.  Do textového pole **Název** zadejte název aplikace. Vyberte **Přidat.**

Po krátké době budete moci zobrazit konfigurační podokno aplikace.

#### <a name="configure-the-application-for-password-single-sign-on"></a><a name="configure-the-application-for-password-single-sign-on-1"></a>Konfigurace aplikace pro jednotné přihlašování pomocí hesla

Chcete-li nakonfigurovat jednotné přihlašování pro aplikaci, postupujte podle následujících kroků:

1.  Otevřete [**portál Azure**](https://portal.azure.com/) a přihlaste se jako globální **správce** nebo **spolusprávce.**

2.  Otevřete **rozšíření Azure Active Directory** kliknutím na Všechny **služby** v horní části hlavní nabídky navigace vlevo.

3.  Do vyhledávacího pole filtru zadejte **"Azure Active Directory"** a vyberte položku **Služby Azure Active Directory.**

4.  V levé navigační nabídce Azure Active Directory klikněte na **Podnikové aplikace.**

5.  Kliknutím na **Všechny aplikace** zobrazíte seznam všech aplikací.

    1.  Pokud zde nevidíte aplikaci, kterou chcete zobrazit, použijte ovládací prvek **Filtr** v horní části **seznamu Všechny aplikace** a nastavte možnost **Zobrazit** na **všechny aplikace.**

6.  Vyberte aplikaci, kterou chcete nakonfigurovat jednotné přihlašování.

7.  Po načtení aplikace klikněte **na jednotné přihlašování** z levé navigační nabídky aplikace.

8.  Vyberte režim **Přihlašování na základě hesla.**

9.  Zadejte **přihlašovací adresu URL**. Toto je adresa URL, na které uživatelé zadávají své uživatelské jméno a heslo pro přihlášení. Ujistěte se, že přihlašovací pole jsou zobrazena na adrese URL.

10. [Přiřazení uživatelů k aplikaci](#how-to-assign-a-user-to-an-application-directly).

11. Kromě toho můžete také zadat pověření jménem uživatele výběrem řádků uživatelů a kliknutím na **aktualizovat pověření** a zadáním uživatelského jména a hesla jménem uživatelů. V opačném případě budou uživatelé při spuštění vyzváni k zadání pověření sami.

## <a name="problems-related-to-assigning-applications-to-users"></a>Problémy související s přiřazování aplikací uživatelům

Uživatel nemusí být vidět aplikace na jejich přístupový panel, protože nejsou přiřazeny k aplikaci. Níže jsou uvedeny některé způsoby, jak zkontrolovat:

-   [Kontrola, zda je uživatel přiřazen k aplikaci](#check-if-a-user-is-assigned-to-the-application)

-   [Jak přiřadit uživatele k aplikaci přímo](#how-to-assign-a-user-to-an-application-directly)

-   [Zkontrolujte, zda je uživatel přiřazen k licenci související s aplikací.](#check-if-a-user-is-under-a-license-related-to-the-application)

-   [Jak přiřadit licenci uživateli](#how-to-assign-a-user-a-license)

### <a name="check-if-a-user-is-assigned-to-the-application"></a>Kontrola, zda je uživatel přiřazen k aplikaci

Chcete-li zkontrolovat, zda je uživatel přiřazen k aplikaci, postupujte podle následujících kroků:

1. Otevřete [**portál Azure**](https://portal.azure.com/) a přihlaste se jako globální **správce.**

2. Otevřete **rozšíření Azure Active Directory** kliknutím na Všechny **služby** v horní části hlavní nabídky navigace vlevo.

3. Do vyhledávacího pole filtru zadejte **"Azure Active Directory"** a vyberte položku **Služby Azure Active Directory.**

4. V levé navigační nabídce Azure Active Directory klikněte na **Podnikové aplikace.**

5. Kliknutím na **Všechny aplikace** zobrazíte seznam všech aplikací.

6. **Vyhledejte** název dané žádosti.

7. klikněte na **Uživatelé a skupiny**.

8. Zkontrolujte, zda je uživatel přiřazen k aplikaci.

   * Pokud nepostupujte podle kroků v části "Jak přiřadit uživatele k aplikaci přímo", abyste tak učinili.

### <a name="how-to-assign-a-user-to-an-application-directly"></a>Jak přiřadit uživatele k aplikaci přímo

Chcete-li k aplikaci přiřadit jednoho nebo více uživatelů přímo, postupujte podle následujících kroků:

1. Otevřete [**portál Azure**](https://portal.azure.com/) a přihlaste se jako globální **správce**.

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

### <a name="check-if-a-user-is-under-a-license-related-to-the-application"></a>Zkontrolujte, zda uživatel nemá licenci související s aplikací

Chcete-li zkontrolovat přiřazené licence uživatele, postupujte podle následujících kroků:

1. Otevřete [**portál Azure**](https://portal.azure.com/) a přihlaste se jako globální **správce.**

2. Otevřete **rozšíření Azure Active Directory** kliknutím na Všechny **služby** v horní části hlavní nabídky navigace vlevo.

3. Do vyhledávacího pole filtru zadejte **"Azure Active Directory"** a vyberte položku **Služby Azure Active Directory.**

4. V navigační nabídce klikněte na **Uživatelé a skupiny.**

5. klepněte na položku **Všichni uživatelé**.

6. **Vyhledejte** uživatele, který vás zajímá, a **klikněte na řádek,** který chcete vybrat.

7. Chcete-li zjistit, které licence uživatel aktuálně přiřadil, klepněte na **položku Licence.**

   * Pokud je uživatel přiřazen k licenci sady Office, umožní se aplikace sady Office první strany zobrazit na přístupovém panelu uživatele.

### <a name="how-to-assign-a-user-a-license"></a>Jak přiřadit uživateli licenci 

Chcete-li uživateli přiřadit licenci, postupujte takto:

1.  Otevřete [**portál Azure**](https://portal.azure.com/) a přihlaste se jako globální **správce.**

2.  Otevřete **rozšíření Azure Active Directory** kliknutím na Všechny **služby** v horní části hlavní nabídky navigace vlevo.

3.  Do vyhledávacího pole filtru zadejte **"Azure Active Directory"** a vyberte položku **Služby Azure Active Directory.**

4.  V navigační nabídce klikněte na **Uživatelé a skupiny.**

5.  klepněte na položku **Všichni uživatelé**.

6.  **Vyhledejte** uživatele, který vás zajímá, a **klikněte na řádek,** který chcete vybrat.

7.  Chcete-li zjistit, které licence uživatel aktuálně přiřadil, klepněte na **položku Licence.**

8.  klikněte na tlačítko **Přiřadit.**

9.  Vyberte **jeden nebo více produktů** ze seznamu dostupných produktů.

10. **Volitelně** klepněte na položku **možností přiřazení,** chcete-li granularly přiřadit produkty. Po dokončení klepněte na **tlačítko Ok.**

11. Klepnutím na tlačítko **Přiřadit** přiřadíte tyto licence tomuto uživateli.

## <a name="problems-related-to-assigning-applications-to-groups"></a>Problémy související s přiřazování žádostí skupinám

Uživatel může být vidět aplikace na jejich přístupový panel, protože jsou součástí skupiny, která byla přiřazena aplikace. Níže jsou uvedeny některé způsoby, jak zkontrolovat:

-   [Kontrola členství uživatele ve skupinách](#check-a-users-group-memberships)

-   [Jak přiřadit aplikaci skupině přímo](#how-to-assign-an-application-to-a-group-directly)

-   [Kontrola, zda je uživatel součástí skupiny přiřazené k licenci](#check-if-a-user-is-part-of-group-assigned-to-a-license)

-   [Jak přiřadit licenci ke skupině](#how-to-assign-a-license-to-a-group)

### <a name="check-a-users-group-memberships"></a>Kontrola členství uživatele ve skupinách

Chcete-li zkontrolovat členství ve skupině, postupujte podle následujících kroků:

1. Otevřete [**portál Azure**](https://portal.azure.com/) a přihlaste se jako globální **správce.**

2. Otevřete **rozšíření Azure Active Directory** kliknutím na Všechny **služby** v horní části hlavní nabídky navigace vlevo.

3. Do vyhledávacího pole filtru zadejte **"Azure Active Directory"** a vyberte položku **Služby Azure Active Directory.**

4. V navigační nabídce klikněte na **Uživatelé a skupiny.**

5. klepněte na položku **Všichni uživatelé**.

6. **Vyhledejte** uživatele, který vás zajímá, a **klikněte na řádek,** který chcete vybrat.

7. klepněte na **položku Skupiny**.

8. Zkontrolujte, zda je uživatel součástí skupiny přiřazené k aplikaci.

   * Pokud chcete odebrat uživatele ze skupiny, **klikněte na řádek** skupiny a vyberte odstranit.

### <a name="how-to-assign-an-application-to-a-group-directly"></a>Jak přiřadit aplikaci skupině přímo

Chcete-li aplikaci přiřadit jednu nebo více skupin přímo, postupujte podle následujících kroků:

1. Otevřete [**portál Azure**](https://portal.azure.com/) a přihlaste se jako globální **správce**.

2. Otevřete **rozšíření Azure Active Directory** kliknutím na Všechny **služby** v horní části hlavní nabídky navigace vlevo.

3. Do vyhledávacího pole filtru zadejte **"Azure Active Directory"** a vyberte položku **Služby Azure Active Directory.**

4. V levé navigační nabídce Azure Active Directory klikněte na **Podnikové aplikace.**

5. Kliknutím na **Všechny aplikace** zobrazíte seznam všech aplikací.

   * Pokud zde nevidíte aplikaci, kterou chcete zobrazit, použijte ovládací prvek **Filtr** v horní části **seznamu Všechny aplikace** a nastavte možnost **Zobrazit** na **všechny aplikace.**

6. Ze seznamu vyberte aplikaci, ke které chcete přiřadit uživatele.

7. Po načtení aplikace klikněte na **Uživatelé a skupiny** z levé navigační nabídky aplikace.

8. Kliknutím na tlačítko **Přidat** v horní části seznamu **Uživatelé a skupiny** otevřete podokno **Přidat přiřazení.**

9. V podokně **Přidat přiřazení** klikněte na volič uživatelů **a skupin.**

10. Zadejte **úplný název skupiny** skupiny, kterou chcete přiřadit do vyhledávacího pole **Hledat podle jména nebo e-mailové adresy.**

11. Najeďte přes **skupinu** v seznamu a zvedejte **zaškrtávací políčko**. Kliknutím na zaškrtávací políčko vedle profilové fotky nebo loga skupiny přidáte uživatele do seznamu **Vybrané.**

12. **Nepovinné:** Pokud chcete **přidat více než jednu skupinu**, zadejte do vyhledávacího pole Hledat podle názvu nebo **e-mailové adresy** jiný úplný název **skupiny** a kliknutím na toto políčko přidejte tuto skupinu do seznamu **Vybrané.**

13. Po dokončení výběru skupin je klepnutím na tlačítko **Vybrat** přidejte do seznamu uživatelů a skupin, které mají být přiřazeny k aplikaci.

14. **Volitelné:** Klepnutím na volič **role v** podokně **Přidat přiřazení** vyberte roli, kterou chcete přiřadit vybraným skupinám.

15. Klepnutím na tlačítko **Přiřadit** přiřadíte aplikaci vybraným skupinám.

Po krátké době budou moci vybraní uživatelé tyto aplikace spustit na přístupovém panelu.

### <a name="check-if-a-user-is-part-of-group-assigned-to-a-license"></a>Kontrola, zda je uživatel součástí skupiny přiřazené k licenci

1. Otevřete [**portál Azure**](https://portal.azure.com/) a přihlaste se jako globální **správce.**

2. Otevřete **rozšíření Azure Active Directory** kliknutím na Všechny **služby** v horní části hlavní nabídky navigace vlevo.

3. Do vyhledávacího pole filtru zadejte **"Azure Active Directory"** a vyberte položku **Služby Azure Active Directory.**

4. V navigační nabídce klikněte na **Uživatelé a skupiny.**

5. klepněte na položku **Všichni uživatelé**.

6. **Vyhledejte** uživatele, který vás zajímá, a **klikněte na řádek,** který chcete vybrat.

7. klepněte na **položku Skupiny**.

8. klikněte na řádek určité skupiny.

9. Chcete-li zjistit, které licence k ní skupina přiřadila, klepněte na **položku Licence.**

   * Pokud je skupina přiřazena k licenci sady Office, může to umožnit, aby se některé aplikace sady Office první strany zobrazovaly na přístupovém panelu uživatele.

### <a name="how-to-assign-a-license-to-a-group"></a>Jak přiřadit licenci ke skupině

Chcete-li skupině přiřadit licenci, postupujte takto:

1.  Otevřete [**portál Azure**](https://portal.azure.com/) a přihlaste se jako globální **správce.**

2.  Otevřete **rozšíření Azure Active Directory** kliknutím na Všechny **služby** v horní části hlavní nabídky navigace vlevo.

3.  Do vyhledávacího pole filtru zadejte **"Azure Active Directory"** a vyberte položku **Služby Azure Active Directory.**

4.  V navigační nabídce klikněte na **Uživatelé a skupiny.**

5.  klepněte na **položku Všechny skupiny**.

6.  **Vyhledejte** skupinu, která vás zajímá, a **klikněte na řádek,** který chcete vybrat.

7.  Chcete-li zjistit, které licence skupina aktuálně přiřadila, klepněte na **položku Licence.**

8.  klikněte na tlačítko **Přiřadit.**

9.  Vyberte **jeden nebo více produktů** ze seznamu dostupných produktů.

10. **Volitelně** klepněte na položku **možností přiřazení,** chcete-li granularly přiřadit produkty. Po dokončení klepněte na **tlačítko Ok.**

11. Klepnutím na tlačítko **Přiřadit** přiřadíte tyto licence této skupině. To může trvat dlouhou dobu, v závislosti na velikosti a složitosti skupiny.

>[!NOTE]
>Chcete-li to provést rychleji, zvažte dočasné přiřazení licence přímo uživateli. 
>
>

## <a name="next-steps"></a>Další kroky
[Přidání nových uživatelů do služby Azure Active Directory](./../fundamentals/add-users-azure-active-directory.md)

