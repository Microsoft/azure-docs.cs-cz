---
title: Potíže při přihlašování do aplikace na přístupovém panelu | Dokumentace Microsoftu
description: Jak řešit problémy přístupu k aplikaci z Microsoft Azure AD panelu na adrese myapps.microsoft.com
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/11/2017
ms.author: celested
ms.reviewer: japere
ms.collection: M365-identity-device-management
ms.openlocfilehash: a05efbe3feb81b11d547cd50d8ba3f87c2ff0e5b
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2019
ms.locfileid: "56164715"
---
# <a name="problems-signing-in-to-an-application-from-the-access-panel"></a>Potíže při přihlašování do aplikace na přístupovém panelu

Přístupový Panel je webový portál, který umožňuje uživateli pomocí pracovního nebo školního účtu ve službě Azure Active Directory (Azure AD) k zobrazení a spuštění aplikace založené na cloudu, že správce Azure AD udělil jim přístup k. 

Tyto aplikace jsou konfigurovány jménem uživatele na portálu Azure AD. Aplikace musí být správně nakonfigurovali a přiřadili pro uživatele nebo skupiny, jejichž je uživatel členem skupiny a prohlédněte si aplikaci na přístupovém panelu.

Typ aplikace, které uživatel může zobrazit spadají do následujících kategorií:

-   Aplikace Office 365

-   Aplikace Microsoftu a třetích stran nakonfigurovat s jednotným Přihlašováním federační

-   Aplikace založené na heslech jednotného přihlašování

-   Aplikace se stávajícími řešeními jednotného přihlašování

## <a name="general-issues-to-check-first"></a>Obecné problémy a proveďte nejprve kontrolu

-   Ujistěte se, že vaše používání **prohlížeče** , který splňuje minimální požadavky na přístupový Panel.

-   Ujistěte se, že v prohlížeči uživatele bylo přidáno adresu URL aplikace do jeho **Důvěryhodné servery**.

-   Ujistěte se, že chcete zkontrolovat, že aplikace je **nakonfigurované** správně.

-   Ujistěte se, že účet uživatele je **povolené** pro přihlášení.

-   Ujistěte se, že účet uživatele je **není uzamčen.**

-   Ujistěte se, že uživatele **není platnost vypršela nebo zapomněli heslo.**

-   Ujistěte se, že **ověřování službou Multi-Factor Authentication** neblokuje přístup uživatelů.

-   Ujistěte se, že **zásady podmíněného přístupu** nebo **Identity Protection** zásad neblokuje přístup uživatelů.

-   Ujistěte se, že uživatele **kontaktní údaje pro ověření** neustále aktuální, aby ověřování službou Multi-Factor Authentication nebo podmíněného přístupu zásady vynucení.

-   Ujistěte se, že k také zkuste vymazání souborů cookie v prohlížeči a pokoušel se přihlásit znovu.

## <a name="meeting-browser-requirements-for-the-access-panel"></a>Splňuje požadavky na prohlížeč pro na přístupovém panelu

Přístupový Panel vyžaduje prohlížeč, který podporuje JavaScript a má povolený šablon stylů CSS. Chcete-li použít založené na heslech jednotné přihlašování (SSO) na přístupovém panelu, musí být nainstalovaná rozšíření přístupového panelu v prohlížeči uživatele. Toto rozšíření se automaticky stáhne, když uživatel vybere, který je nakonfigurovaný pro jednotné přihlašování pomocí hesla aplikace.

Pro jednotné přihlašování založené na heslech může být koncového uživatele prohlížeče:

-   Aplikace Internet Explorer 8, 9, 10, 11 – ve Windows 7 nebo novější

-   Microsoft Edge ve Windows 10 Anniversary Edition nebo novější

-   Chrome – Ve Windows 7 nebo novější a v systému MacOS X nebo novější

-   Firefox 26.0 nebo později – na Windows XP SP2 nebo novější a v systému Mac OS X 10,6 nebo novější

## <a name="how-to-install-the-access-panel-browser-extension"></a>Postup instalace rozšíření prohlížeče Panel přístupu

K instalaci rozšíření prohlížeče panelu přístup, postupujte podle následujících kroků:

1.  Otevřít [přístupového panelu](https://myapps.microsoft.com) v jednom z podporovaných prohlížečů a přihlaste se jako **uživatele** ve službě Azure AD.

2.  Klikněte na tlačítko **heslem jednotného přihlašování aplikace** na přístupovém panelu.

3.  V příkazovém řádku s výzvou k instalaci softwaru vyberte **nainstalovat**.

4.  Založené na prohlížeči, které jsou směrované na odkaz ke stažení. **Přidat** rozšíření do prohlížeče.

5.  Pokud vás prohlížeč vyzve, vyberte buď **povolit** nebo **povolit** rozšíření.

6.  Po instalaci **restartovat** relace prohlížeče.

7.  Přihlaste se na přístupovém panelu a zobrazit, pokud můžete **spuštění** vaše heslo jednotného přihlašování aplikace

Rozšíření lze stáhnout z následující přímé odkazy taky pro Chrome a Microsoft Edge:

-   [Rozšíření Chrome přístupového panelu](https://chrome.google.com/webstore/detail/access-panel-extension/ggjhpefgjjfobnfoldnjipclpcfbgbhl)

-   [Rozšíření Microsoft Edge přístupového panelu](https://www.microsoft.com/store/apps/9pc9sckkzk84)

## <a name="how-to-configure-federated-single-sign-on-for-an-azure-ad-gallery-application"></a>Jak nakonfigurovat federované jednotné přihlašování pro aplikaci Galerie Azure AD

Všechny aplikace v galerii Azure AD povolit možnost Enterprise Single Sign-On obsahuje podrobný návod k dispozici. Můžete přistupovat [seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](https://azure.microsoft.com/documentation/articles/active-directory-saas-tutorial-list/) podrobností podrobné pokyny.

Konfigurace aplikace z Galerie Azure AD, které je potřeba:

-   Přidat aplikaci z Galerie Azure AD

-   [Nakonfigurujte hodnoty metadat aplikace ve službě Azure AD (přihlašování adresu URL, identifikátoru, adresa URL odpovědi)](#configure-single-sign-on-for-an-application-from-the-azure-ad-gallery)

-   [Vyberte identifikátor uživatele a přidat atributy uživatele k odeslání do aplikace](#select-user-identifier-and-add-user-attributes-to-be-sent-to-the-application)

-   [Načíst metadata služby Azure AD a certifikátů](#download-the-azure-ad-metadata-or-certificate)

-   [Nakonfigurujte hodnoty metadat služby Azure AD v aplikaci (přihlašování adresu URL, Vystavitel, odhlašovací adresa URL a certifikát)](#configure-single-sign-on-for-an-application-from-the-azure-ad-gallery)

-   Přiřazení uživatelů k aplikaci

### <a name="add-an-application-from-the-azure-ad-gallery"></a>Přidat aplikaci z Galerie Azure AD

Přidat aplikaci z Galerie Azure AD, postupujte podle následujících kroků:

1.  Otevřít [webu Azure portal](https://portal.azure.com) a přihlaste se jako **globálního správce** nebo **spolusprávce**

2.  Otevřít **rozšíření Azure Active Directory** kliknutím **všechny služby** v horní části hlavní navigační nabídce vlevo.

3.  Zadejte **"Azure Active Directory**" do vyhledávacího pole filtrovat a vybrat **Azure Active Directory** položky.

4.  Klikněte na tlačítko **podnikové aplikace** levé navigační nabídce Azure Active Directory.

5.  Klikněte na tlačítko **přidat** tlačítko v pravém horním rohu **podnikové aplikace** podokně.

6.  V **zadejte název** textové pole z **přidat z Galerie** části, zadejte název aplikace.

7.  Vyberte aplikaci, kterou chcete konfigurovat pro jednotné přihlašování.

8.  Před přidáním aplikace, můžete změnit její název ze **název** textového pole.

9.  Klikněte na tlačítko **přidat** tlačítko pro přidání aplikace.

Po krátké době zobrazí se podokno konfiguračních vaší aplikace.

### <a name="configure-single-sign-on-for-an-application-from-the-azure-ad-gallery"></a>Konfigurace jednotného přihlašování pro aplikaci z Galerie Azure AD

Pokud chcete nakonfigurovat jednotné přihlašování pro aplikaci, použijte následující postup:

1.  <span id="_Hlk477187909" class="anchor"><span id="_Hlk477001983" class="anchor"></span></span>Otevřít [ **webu Azure portal** ](https://portal.azure.com/) a přihlaste se jako **globálního správce** nebo **Spolusprávce.**

2.  Otevřít **rozšíření Azure Active Directory** kliknutím **všechny služby** v horní části hlavní navigační nabídce vlevo.

3.  Zadejte **"Azure Active Directory**" do vyhledávacího pole filtrovat a vybrat **Azure Active Directory** položky.

4.  Klikněte na tlačítko **podnikové aplikace** levé navigační nabídce Azure Active Directory.

5.  Klikněte na tlačítko **všechny aplikace** zobrazíte seznam všech aplikací.

  * Pokud nevidíte aplikaci, kterou má zobrazit tady, použijte **filtr** ovládacího prvku v horní části **seznam všech aplikací** a nastavit **zobrazit** umožňuje **všechny Aplikace.**

6.  Vyberte aplikaci, kterou chcete nakonfigurovat jednotné přihlašování.

7.  Po načtení aplikace, klikněte na tlačítko **jednotného přihlašování** levé navigační nabídce aplikace.

8.  Vyberte **přihlašování na základě SAML** z **režimu** rozevíracího seznamu.

9.  Zadejte požadované hodnoty v **domény a adresy URL.** Tyto hodnoty byste měli získat od dodavatele aplikace.

   1. Konfigurace aplikace jako iniciovaného Zprostředkovatelem přihlašování jednotného přihlašování, přihlašovací adresa URL je povinná hodnota. Pro některé aplikace identifikátor je také požadovaná hodnota.

   2. Konfigurace aplikace jako jednotné přihlašování zahájené pomocí IdP, adresa URL odpovědi je povinná hodnota. Pro některé aplikace identifikátor je také požadovaná hodnota.

10. **Volitelné:** klikněte na tlačítko **zobrazit pokročilé nastavení URL** Pokud chcete zobrazit nepovinné hodnoty.

11. V **atributy uživatele**, vyberte jedinečný identifikátor pro vaši uživatelé v **identifikátor uživatele** rozevíracího seznamu.

12. **Volitelné:** klikněte na tlačítko **zobrazit a upravit všechny ostatní atributy uživatele** upravit atributy, které se odešlou na aplikaci v tokenu SAML, při přihlášení uživatele.

   Chcete-li přidat atribut:

   1. Klikněte na tlačítko **přidat atribut**. Zadejte **název** a vyberte položku **hodnota** z rozevíracího seznamu.

   2. Klikněte na tlačítko **uložit.** V tabulce se zobrazí nový atribut.

13. Klikněte na tlačítko **konfigurovat &lt;název_aplikace&gt;**  pro přístup k dokumentaci o tom, jak nakonfigurovat jednotné přihlašování v aplikaci. Máte také adres URL metadat a certifikát nutný k nastavení jednotného přihlašování k aplikaci.

14. Klikněte na tlačítko **Uložit** uložte konfiguraci.

15. Přiřazení uživatelů k aplikaci.

### <a name="select-user-identifier-and-add-user-attributes-to-be-sent-to-the-application"></a>Vyberte identifikátor uživatele a přidat atributy uživatele k odeslání do aplikace

Vyberte identifikátor uživatele nebo přidat atributy uživatele, postupujte podle následujících kroků:

1.  Otevřít [ **webu Azure portal** ](https://portal.azure.com/) a přihlaste se jako **globálního správce** nebo **Spolusprávce.**

2.  Otevřít **rozšíření Azure Active Directory** kliknutím **všechny služby** v horní části hlavní navigační nabídce vlevo.

3.  Zadejte **"Azure Active Directory**" do vyhledávacího pole filtrovat a vybrat **Azure Active Directory** položky.

4.  Klikněte na tlačítko **podnikové aplikace** levé navigační nabídce Azure Active Directory.

5.  Klikněte na tlačítko **všechny aplikace** zobrazíte seznam všech aplikací.

  * Pokud nevidíte aplikaci chcete zobrazit sem, použijte **filtr** ovládacího prvku v horní části **seznam všech aplikací** a nastavit **zobrazit** umožňuje  **Všechny aplikace.**

6.  Vyberte aplikace, které jste nakonfigurovali jednotného přihlašování.

7.  Po načtení aplikace, klikněte na tlačítko **jednotného přihlašování** levé navigační nabídce aplikace.

8.  V části **atributy uživatele** vyberte jedinečný identifikátor pro vaši uživatelé v **identifikátor uživatele** rozevíracího seznamu. Vybrané možnosti je potřeba neodpovídá očekávané hodnotě. v aplikaci pro ověření uživatele.

    >[!NOTE]
    >Azure AD vyberte formát pro atribut NameID (identifikátor uživatele) na základě hodnoty vybrané nebo formátu požadovaná aplikací v SAML AuthRequest. Další informace najdete v článku [protokol jednotné přihlašování SAML](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-on-protocol-reference#authnrequest) části NameIDPolicy.
    >
    >

9.  Chcete-li přidat atributy uživatele, klikněte na tlačítko **zobrazit a upravit všechny ostatní atributy uživatele** upravit atributy, které se odešlou na aplikaci v tokenu SAML, při přihlášení uživatele.

   Chcete-li přidat atribut:

   1. Klikněte na tlačítko **přidat atribut**. Zadejte **název** a vyberte položku **hodnota** z rozevíracího seznamu.

   2. Klikněte na tlačítko **uložit.** V tabulce se zobrazí nový atribut.

### <a name="download-the-azure-ad-metadata-or-certificate"></a>Stáhnout Azure AD metadata nebo certifikát

Stáhnout metadata aplikace nebo certifikát z Azure AD, postupujte podle následujících kroků:

1.  Otevřít [ **webu Azure portal** ](https://portal.azure.com/) a přihlaste se jako **globálního správce** nebo **Spolusprávce.**

2.  Otevřít **rozšíření Azure Active Directory** kliknutím **všechny služby** v horní části hlavní navigační nabídce vlevo.

3.  Zadejte **"Azure Active Directory**" do vyhledávacího pole filtrovat a vybrat **Azure Active Directory** položky.

4.  Klikněte na tlačítko **podnikové aplikace** levé navigační nabídce Azure Active Directory.

5.  Klikněte na tlačítko **všechny aplikace** zobrazíte seznam všech aplikací.

  * Pokud nevidíte aplikaci, kterou má zobrazit tady, použijte **filtr** ovládacího prvku v horní části **seznam všech aplikací** a nastavit **zobrazit** umožňuje **všechny Aplikace.**

6.  Vyberte aplikace, které jste nakonfigurovali jednotného přihlašování.

7.  Po načtení aplikace, klikněte na tlačítko **jednotného přihlašování** levé navigační nabídce aplikace.

8.  Přejděte na **podpisový certifikát SAML** části a pak klikněte na **Stáhnout** hodnota ve sloupci. V závislosti na tom, jaké aplikace vyžaduje konfiguraci jednotného přihlašování se zobrazí buď možnost stahovat kód XML metadat nebo certifikát.

    Azure AD neposkytuje adresu URL získat metadata. Metadata se dá načíst jenom jako soubor XML.

## <a name="how-to-configure-federated-single-sign-on-for-a-non-gallery-application"></a>Jak nakonfigurovat federované jednotné přihlašování pro aplikaci mimo Galerii

Pokud chcete nakonfigurovat aplikaci mimo galerii, musíte mít Azure AD premium a aplikace podporuje SAML 2.0. Další informace o verzích služby Azure AD, najdete [ceny služby Azure AD](https://azure.microsoft.com/pricing/details/active-directory/).

-   [Nakonfigurujte hodnoty metadat aplikace ve službě Azure AD (přihlašování adresu URL, identifikátoru, adresa URL odpovědi)](#configuring-single-sign-on)

-   [Vyberte identifikátor uživatele a přidat atributy uživatele k odeslání do aplikace](#select-user-identifier-and-add-user-attributes-to-be-sent-to-the-application)

-   [Načíst metadata služby Azure AD a certifikátů](#download-the-azure-ad-metadata-or-certificate)

-   [Nakonfigurujte hodnoty metadat služby Azure AD v aplikaci (přihlašování adresu URL, Vystavitel, odhlašovací adresa URL a certifikát)](#configuring-single-sign-on)

### <a name="configure-the-applications-metadata-values-in-azure-ad-sign-on-url-identifier-reply-url"></a>Nakonfigurujte hodnoty metadat aplikace ve službě Azure AD (přihlašování adresu URL, identifikátoru, adresa URL odpovědi)

Pokud chcete nakonfigurovat jednotné přihlašování pro aplikace, které nejsou v galerii Azure AD, postupujte podle následujících kroků:

1.  Otevřít [ **webu Azure portal** ](https://portal.azure.com/) a přihlaste se jako **globálního správce** nebo **Spolusprávce.**

2.  Otevřít **rozšíření Azure Active Directory** kliknutím **všechny služby** v horní části hlavní navigační nabídce vlevo.

3.  Zadejte **"Azure Active Directory**" do vyhledávacího pole filtrovat a vybrat **Azure Active Directory** položky.

4.  Klikněte na tlačítko **podnikové aplikace** levé navigační nabídce Azure Active Directory.

5.  Klikněte na tlačítko **přidat** tlačítko v pravém horním rohu **podnikové aplikace** podokně.

6.  Klikněte na tlačítko **aplikace mimo galerii** v **přidat vlastní aplikaci** oddílu.

7.  Zadejte název aplikace **název** textového pole.

8.  Klikněte na tlačítko **přidat** tlačítko pro přidání aplikace.

9.  Po načtení aplikace, klikněte na tlačítko **jednotného přihlašování** levé navigační nabídce aplikace.

10. Vyberte **přihlašování na základě SAML** v **režimu** rozevíracího seznamu

11. Zadejte požadované hodnoty v **domény a adresy URL.** Tyto hodnoty byste měli získat od dodavatele aplikace.

  1. Pokud chcete nakonfigurovat aplikace jako jednotné přihlašování zahájené pomocí IdP, zadejte příslušnou odpovědní adresu URL a identifikátoru.

  2. **Volitelné:** Konfigurace aplikace jako iniciovaného Zprostředkovatelem přihlašování jednotného přihlašování, přihlašovací adresa URL je povinná hodnota.

12. V **atributy uživatele**, vyberte jedinečný identifikátor pro vaši uživatelé v **identifikátor uživatele** rozevíracího seznamu.

13. **Volitelné:** klikněte na tlačítko **zobrazit a upravit všechny ostatní atributy uživatele** upravit atributy, které se odešlou na aplikaci v tokenu SAML, při přihlášení uživatele.

   Chcete-li přidat atribut:

   1. Klikněte na tlačítko **přidat atribut**. Zadejte **název** a vyberte položku **hodnota** z rozevíracího seznamu.

   2. Klikněte na tlačítko **uložit.** V tabulce se zobrazí nový atribut.

14. Klikněte na tlačítko **konfigurovat &lt;název_aplikace&gt;**  pro přístup k dokumentaci o tom, jak nakonfigurovat jednotné přihlašování v aplikaci. Máte také adresy URL Azure AD a certifikát nutný pro aplikace.

### <a name="select-user-identifier-and-add-user-attributes-to-be-sent-to-the-application"></a>Vyberte identifikátor uživatele a přidat atributy uživatele k odeslání do aplikace

Vyberte identifikátor uživatele nebo přidat atributy uživatele, postupujte podle následujících kroků:

1.  Otevřít [ **webu Azure portal** ](https://portal.azure.com/) a přihlaste se jako **globálního správce** nebo **Spolusprávce.**

2.  Otevřít **rozšíření Azure Active Directory** kliknutím **všechny služby** v horní části hlavní navigační nabídce vlevo.

3.  Zadejte **"Azure Active Directory**" do vyhledávacího pole filtrovat a vybrat **Azure Active Directory** položky.

4.  Klikněte na tlačítko **podnikové aplikace** levé navigační nabídce Azure Active Directory.

5.  Klikněte na tlačítko **všechny aplikace** zobrazíte seznam všech aplikací.

  * Pokud nevidíte aplikaci, kterou má zobrazit tady, použijte **filtr** ovládacího prvku v horní části **seznam všech aplikací** a nastavit **zobrazit** umožňuje **všechny Aplikace.**

6.  Vyberte aplikace, které jste nakonfigurovali jednotného přihlašování.

7.  Po načtení aplikace, klikněte na tlačítko **jednotného přihlašování** levé navigační nabídce aplikace.

8.  V části **atributy uživatele** vyberte jedinečný identifikátor pro vaši uživatelé v **identifikátor uživatele** rozevíracího seznamu. Vybrané možnosti je potřeba neodpovídá očekávané hodnotě. v aplikaci pro ověření uživatele.

   >[!NOTE]
   >Azure AD vyberte formát pro atribut NameID (identifikátor uživatele) na základě hodnoty vybrané nebo formátu požadovaná aplikací v SAML AuthRequest. Další informace najdete v článku [protokol jednotné přihlašování SAML](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-on-protocol-reference#authnrequest) části NameIDPolicy.
   >
   >

9.  Chcete-li přidat atributy uživatele, klikněte na tlačítko **zobrazit a upravit všechny ostatní atributy uživatele** upravit atributy, které se odešlou na aplikaci v tokenu SAML, při přihlášení uživatele.

   Chcete-li přidat atribut:

   1 klikněte na tlačítko **přidat atribut**. Zadejte **název** a vyberte položku **hodnota** z rozevíracího seznamu.

   Klikněte na 2 **uložit.** V tabulce se zobrazí nový atribut.

### <a name="download-the-azure-ad-metadata-or-certificate"></a>Stáhnout Azure AD metadata nebo certifikát

Stáhnout metadata aplikace nebo certifikát z Azure AD, postupujte podle následujících kroků:

1.  Otevřít [ **webu Azure portal** ](https://portal.azure.com/) a přihlaste se jako **globálního správce** nebo **Spolusprávce.**

2.  Otevřít **rozšíření Azure Active Directory** kliknutím **všechny služby** v horní části hlavní navigační nabídce vlevo.

3.  Zadejte **"Azure Active Directory**" do vyhledávacího pole filtrovat a vybrat **Azure Active Directory** položky.

4.  Klikněte na tlačítko **podnikové aplikace** levé navigační nabídce Azure Active Directory.

5.  Klikněte na tlačítko **všechny aplikace** zobrazíte seznam všech aplikací.

   * Pokud nevidíte aplikaci, kterou má zobrazit tady, použijte **filtr** ovládacího prvku v horní části **seznam všech aplikací** a nastavit **zobrazit** umožňuje **všechny Aplikace.**

6.  Vyberte aplikace, které jste nakonfigurovali jednotného přihlašování.

7.  Po načtení aplikace, klikněte na tlačítko **jednotného přihlašování** levé navigační nabídce aplikace.

8.  Přejděte na **podpisový certifikát SAML** části a pak klikněte na **Stáhnout** hodnota ve sloupci. V závislosti na tom, jaké aplikace vyžaduje konfiguraci jednotného přihlašování se zobrazí buď možnost stahovat kód XML metadat nebo certifikát.

    Azure AD neposkytuje adresu URL získat metadata. Metadata se dá načíst jenom jako soubor XML.

## <a name="how-to-configure-password-single-sign-on-for-an-azure-ad-gallery-application"></a>Postup konfigurace hesel jednotného přihlašování pro aplikaci Galerie Azure AD

Konfigurace aplikace z Galerie Azure AD, které je potřeba:

-   Přidat aplikaci z Galerie Azure AD

-   Konfigurace aplikace pro heslo jednotného přihlašování

### <a name="add-an-application-from-the-azure-ad-gallery"></a>Přidat aplikaci z Galerie Azure AD

Přidat aplikaci z Galerie Azure AD, postupujte podle následujících kroků:

1.  Otevřít [webu Azure portal](https://portal.azure.com) a přihlaste se jako **globálního správce** nebo **spolusprávce**

2.  Otevřít **rozšíření Azure Active Directory** kliknutím **všechny služby** v horní části hlavní navigační nabídce vlevo.

3.  Zadejte **"Azure Active Directory**" do vyhledávacího pole filtrovat a vybrat **Azure Active Directory** položky.

4.  Klikněte na tlačítko **podnikové aplikace** levé navigační nabídce Azure Active Directory.

5.  Klikněte na tlačítko **přidat** tlačítko v pravém horním rohu **podnikové aplikace** podokně.

6.  V **zadejte název** textové pole z **přidat z Galerie** části, zadejte název aplikace

7.  Vyberte aplikaci, kterou chcete konfigurovat pro jednotné přihlašování

8.  Před přidáním aplikace, můžete změnit její název ze **název** textového pole.

9.  Klikněte na tlačítko **přidat** tlačítko pro přidání aplikace.

Po krátké době zobrazí se podokno konfiguračních vaší aplikace.

### <a name="configure-the-application-for-password-single-sign-on"></a>Konfigurace aplikace pro heslo jednotného přihlašování

Pokud chcete nakonfigurovat jednotné přihlašování pro aplikaci, použijte následující postup:

1.  Otevřít [ **webu Azure portal** ](https://portal.azure.com/) a přihlaste se jako **globálního správce** nebo **Spolusprávce.**

2.  Otevřít **rozšíření Azure Active Directory** kliknutím **všechny služby** v horní části hlavní navigační nabídce vlevo.

3.  Zadejte **"Azure Active Directory**" do vyhledávacího pole filtrovat a vybrat **Azure Active Directory** položky.

4.  Klikněte na tlačítko **podnikové aplikace** levé navigační nabídce Azure Active Directory.

5.  Klikněte na tlačítko **všechny aplikace** zobrazíte seznam všech aplikací.

 * Pokud nevidíte aplikaci, kterou má zobrazit tady, použijte **filtr** ovládacího prvku v horní části **seznam všech aplikací** a nastavit **zobrazit** umožňuje **všechny Aplikace.**

6.  Vyberte aplikaci, kterou chcete nakonfigurovat jednotné přihlašování

7.  Po načtení aplikace, klikněte na tlačítko **jednotného přihlašování** levé navigační nabídce aplikace.

8.  Vyberte režim **přihlašování na základě heslo.**

9.  Přiřazení uživatelů k aplikaci.

10. Kromě toho můžete také zadat přihlašovací údaje jménem uživatele výběr řádků uživatele a kliknutím na **aktualizaci přihlašovacích údajů** a zadáním uživatelského jména a hesla jménem uživatele. V opačném případě uživatelé vyzváni k zadání samotné přihlašovací údaje při spuštění.

## <a name="how-to-configure-password-single-sign-on-for-a-non-gallery-application"></a>Postup konfigurace hesel jednotného přihlašování pro aplikaci mimo Galerii

Konfigurace aplikace z Galerie Azure AD, které je potřeba:

-   [Přidat aplikaci mimo Galerii](#add-a-non-gallery-application)

-   [Konfigurace aplikace pro heslo jednotného přihlašování](#configure-the-application-for-password-single-sign-on)

### <a name="add-a-non-gallery-application"></a>Přidat aplikaci mimo Galerii

Přidat aplikaci z Galerie Azure AD, postupujte podle následujících kroků:

1.  Otevřít [webu Azure portal](https://portal.azure.com) a přihlaste se jako **globálního správce** nebo **spolusprávce**

2.  Otevřít **rozšíření Azure Active Directory** kliknutím **všechny služby** v horní části hlavní navigační nabídce vlevo.

3.  Zadejte **"Azure Active Directory**" do vyhledávacího pole filtrovat a vybrat **Azure Active Directory** položky.

4.  Klikněte na tlačítko **podnikové aplikace** levé navigační nabídce Azure Active Directory.

5.  Klikněte na tlačítko **přidat** tlačítko v pravém horním rohu **podnikové aplikace** podokně.

6.  Klikněte na tlačítko **aplikace mimo galerii.**

7.  Zadejte název vaší aplikace v **název** textového pole. Vyberte **přidat.**

Po krátké době budete moci zobrazit podokno konfiguračních vaší aplikace.

### <a name="configure-the-application-for-password-single-sign-on"></a>Konfigurace aplikace pro heslo jednotného přihlašování

Pokud chcete nakonfigurovat jednotné přihlašování pro aplikaci, použijte následující postup:

1.  Otevřít [ **webu Azure portal** ](https://portal.azure.com/) a přihlaste se jako **globálního správce** nebo **Spolusprávce.**

2.  Otevřít **rozšíření Azure Active Directory** kliknutím **všechny služby** v horní části hlavní navigační nabídce vlevo.

3.  Zadejte **"Azure Active Directory**" do vyhledávacího pole filtrovat a vybrat **Azure Active Directory** položky.

4.  Klikněte na tlačítko **podnikové aplikace** levé navigační nabídce Azure Active Directory.

5.  Klikněte na tlačítko **všechny aplikace** zobrazíte seznam všech aplikací.

 * Pokud nevidíte aplikaci, kterou má zobrazit tady, použijte **filtr** ovládacího prvku v horní části **seznam všech aplikací** a nastavit **zobrazit** umožňuje **všechny Aplikace.**

6.  Vyberte aplikaci, kterou chcete nakonfigurovat jednotné přihlašování.

7.  Po načtení aplikace, klikněte na tlačítko **jednotného přihlašování** levé navigační nabídce aplikace.

8.  Vyberte režim **přihlašování na základě heslo.**

9.  Zadejte **přihlašovací adresa URL**. Toto je adresa URL, kde uživatelé zadat svoje uživatelské jméno a heslo pro přihlášení. Ujistěte se, že pole přihlášení jsou viditelné na adrese URL.

10. Přiřazení uživatelů k aplikaci.

11. Kromě toho můžete také zadat přihlašovací údaje jménem uživatele výběr řádků uživatele a kliknutím na **aktualizaci přihlašovacích údajů** a zadáním uživatelského jména a hesla jménem uživatele. V opačném případě uživatelé vyzváni k zadání samotné přihlašovací údaje při spuštění.

## <a name="how-to-assign-a-user-to-an-application-directly"></a>Jak přiřadit uživatele k aplikaci přímo

Jeden nebo více uživatelů přiřadit přímo k aplikaci, postupujte podle následujících kroků:

1.  Otevřít [ **webu Azure portal** ](https://portal.azure.com/) a přihlaste se jako **globálního správce.**

2.  Otevřít **rozšíření Azure Active Directory** kliknutím **všechny služby** v horní části hlavní navigační nabídce vlevo.

3.  Zadejte **"Azure Active Directory**" do vyhledávacího pole filtrovat a vybrat **Azure Active Directory** položky.

4.  Klikněte na tlačítko **podnikové aplikace** levé navigační nabídce Azure Active Directory.

5.  Klikněte na tlačítko **všechny aplikace** zobrazíte seznam všech aplikací.

  * Pokud nevidíte aplikaci, kterou má zobrazit tady, použijte **filtr** ovládacího prvku v horní části **seznam všech aplikací** a nastavit **zobrazit** umožňuje **všechny Aplikace.**

6.  Vyberte aplikaci, kterou chcete přiřadit uživatele ze seznamu.

7.  Po načtení aplikace, klikněte na tlačítko **uživatelů a skupin** levé navigační nabídce aplikace.

8.  Klikněte na tlačítko **přidat** tlačítko nahoře **uživatelů a skupin** seznamu a otevře **přidat přiřazení** podokně.

9.  Klikněte na tlačítko **uživatelů a skupin** selektor z **přidat přiřazení** podokně.

10. Zadejte **celý název** nebo **e-mailová adresa** uživatele zájem o přiřazení do **hledat podle jména nebo e-mailové adresy** vyhledávacího pole.

11. Najeďte myší **uživatele** v seznamu zobrazíte **zaškrtávací políčko**. Klikněte na zaškrtávací políčko vedle profilové fotky uživatele nebo logo, které chcete přidat uživatele **vybrané** seznamu.

12. **Volitelné:** Pokud byste chtěli **přidat více než jeden uživatel**, typ v jiném **celý název** nebo **e-mailová adresa** do **hledat podle jména nebo e-mailové adresy** vyhledávací pole a klikněte na zaškrtávací políčko a přidáním tohoto uživatele do **vybrané** seznamu.

13. Po dokončení výběru uživatelů, klikněte na tlačítko **vyberte** tlačítko pro přidání do seznamu uživatelů a skupin pro přiřazení k aplikaci.

14. **Volitelné:** klikněte na tlačítko **vybrat roli** oblasti pro výběr **přidat přiřazení** podokně vyberte roli, kterou chcete přiřadit uživatelům, které jste vybrali.

15. Klikněte na tlačítko **přiřadit** tlačítko přiřadit aplikaci do vybraného uživatele.

Po krátké době uživatele, které jste vybrali dát spustit tyto aplikace na přístupovém panelu.

## <a name="if-these-troubleshooting-steps-do-not-the-resolve-the-issue"></a>Pokud tyto kroky pro řešení potíží není vyřešit problém

vytvořit lístek podpory s použitím následujících informací, pokud je k dispozici:

-   Chyba ID korelace

-   Hlavní název uživatele (uživatel e-mailová adresa)

-   ID Tenanta

-   Typ prohlížeče

-   Vyvolá se časové pásmo a čas nebo časový rámec při chybě

-   Trasování Fiddler

## <a name="next-steps"></a>Další postup
[Poskytovat jednotné přihlašování do aplikací pomocí Proxy aplikace](application-proxy-configure-single-sign-on-with-kcd.md)

