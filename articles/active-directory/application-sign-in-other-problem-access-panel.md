---
title: Potíže při přihlašování k aplikaci na přístupovém panelu | Microsoft Docs
description: Řešení potíží s problémy, přístup k aplikaci z Microsoft Azure AD přístupovému panelu na adrese myapps.microsoft.com
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
ms.reviewer: japere
ms.openlocfilehash: 28076dc0234b91d80f8aac44a96b00001b96f2a0
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "36331867"
---
# <a name="problems-signing-in-to-an-application-from-the-access-panel"></a>Potíže při přihlašování k aplikaci na přístupovém panelu

Přístupový Panel je webový portál, který umožňuje uživatele, který má pracovní nebo školní účet ve službě Azure Active Directory (Azure AD) k zobrazení a spustit cloudové aplikace, aby správce Azure AD má je přístup povolen. 

Tyto aplikace jsou konfigurovány jménem uživatele na portálu Azure AD. Aplikace musí být správně nakonfigurovaná a přiřadit uživatele nebo skupiny, kterých je uživatel členem skupiny chcete vidět aplikaci na přístupovém panelu.

Typ aplikace, které zobrazuje uživatel možná spadají do následujících kategorií:

-   Aplikace Office 365

-   Společnost Microsoft a třetích stran aplikace, které jsou nakonfigurované pomocí jednotného přihlašování na základě federation

-   Aplikace založené na heslech jednotného přihlašování

-   Aplikace s stávajících řešení pro jednotné přihlašování

## <a name="general-issues-to-check-first"></a>Běžné problémy a proveďte nejprve kontrolu

-   Zajistěte, aby vaše použití **prohlížeče** , splňuje minimální požadavky na přístupový Panel.

-   Zajistěte, aby prohlížeče uživatele přidala adresu URL aplikace pro jeho **Důvěryhodné servery**.

-   Ujistěte se, zkontrolujte, že aplikace je **nakonfigurované** správně.

-   Zkontrolujte, zda je účet uživatele **povoleno** pro přihlášení.

-   Zkontrolujte, zda je účet uživatele **není uzamčen.**

-   Zkontrolujte, že uživatele **není platnost nebo zapomenete heslo.**

-   Zajistěte, aby **Multi-Factor Authentication** neblokuje přístup uživatelů.

-   Zajistěte, aby **zásady podmíněného přístupu** nebo **Identity Protection** zásad neblokuje přístup uživatelů.

-   Ujistěte se, že uživatele **ověřování kontaktní údaje** je aktuální povolit Multi-Factor Authentication nebo podmíněného přístupu zásad, která budou vynucena.

-   Zkontrolujte, zda také zkuste vymazání souborů cookie v prohlížeči a zkusit se přihlásit znovu.

## <a name="meeting-browser-requirements-for-the-access-panel"></a>Splňuje požadavky na prohlížeč pro přístup k panelu

Přístupový Panel vyžaduje prohlížeč, který podporuje jazyk JavaScript a aktivoval šablon stylů CSS. Pokud chcete používat založené na heslech jednotné přihlašování (SSO) na přístupovém panelu, musí být nainstalované rozšíření přístupového panelu v prohlížeče uživatele. Toto rozšíření je stažen automaticky, když uživatel vybere aplikaci, která je nakonfigurována pro jednotné přihlašování založené na heslech.

Pro jednotné přihlašování založené na heslech může být prohlížeče koncového uživatele:

-   Internet Explorer 8, 9, 10, 11 – v systému Windows 7 nebo novější

-   Hraniční Windows 10 Anniversary Edition nebo novější.

-   Chrome – V systému Windows 7 nebo novější a v systému MacOS X nebo novější

-   Firefox 26.0 nebo později – do systému Windows XP SP2 nebo novější a v systému Mac OS X 10,6 nebo novější

## <a name="how-to-install-the-access-panel-browser-extension"></a>Postup instalace rozšíření přístup k panelu prohlížeče

Chcete-li nainstalovat rozšíření přístup k panelu prohlížeče, postupujte následujícím způsobem:

1.  Otevřete [přístupový Panel](https://myapps.microsoft.com) v jednom z podporovaných prohlížečích a přihlaste se jako **uživatele** ve službě Azure AD.

2.  Klikněte na tlačítko **SSO heslo aplikace** na přístupovém panelu.

3.  V řádku, požádá o instalaci softwaru, vyberte **instalovat nyní**.

4.  Založené na prohlížeči, který budete přesměrováni odkaz ke stažení. **Přidat** rozšíření do prohlížeče.

5.  Pokud prohlížeč zobrazí dotaz, vyberte buď **povolit** nebo **povolit** rozšíření.

6.  Po instalaci **restartujte** relaci prohlížeče.

7.  Přihlaste se do přístupového panelu a zobrazit, pokud můžete **spusťte** aplikace heslo SSO

Rozšíření pro Chrome a hraniční může také stáhnout z přímé odkazy níže:

-   [Rozšíření pro Chrome přístup panely](https://chrome.google.com/webstore/detail/access-panel-extension/ggjhpefgjjfobnfoldnjipclpcfbgbhl)

-   [Rozšíření panelu přístup Edge](https://www.microsoft.com/store/apps/9pc9sckkzk84)

## <a name="how-to-configure-federated-single-sign-on-for-an-azure-ad-gallery-application"></a>Postup konfigurace federované jednotné přihlašování pro aplikaci Galerie Azure AD

Všechny aplikace v galerii Azure AD povolit funkce Enterprise Single Sign-On má podrobný kurz k dispozici. Dostanete [seznam kurzů k integraci aplikací SaaS v Azure Active Directory](https://azure.microsoft.com/documentation/articles/active-directory-saas-tutorial-list/) podrobností podrobné pokyny.

Pro konfiguraci aplikace z Galerie Azure AD, které potřebujete:

-   [Přidat aplikaci z Galerie Azure AD](#add-an-application)

-   [Nakonfigurujte hodnoty metadata aplikace ve službě Azure AD (přihlašování adresu URL, identifikátor, adresa URL odpovědi)](#configure-single-sign-on-for-an-application-from-the-azure-ad-gallery)

-   [Vyberte uživatelský identifikátor a přidejte atributy uživatele k odeslání do aplikace](#select-user-identifier-and-add-user-attributes-to-be-sent-to-the-application)

-   [Načtení metadat Azure AD a certifikátů](#download-the-azure-ad-metadata-or-certificate)

-   [Konfigurovat Azure AD metadata hodnoty v aplikaci (přihlašování adresu URL, vystavitele, adresy URL odhlašovací a certifikát)](#configure-single-sign-on-for-an-application-from-the-azure-ad-gallery)

-   [Přiřazení uživatelů k aplikaci](#assign-users-to-the-application)

### <a name="add-an-application-from-the-azure-ad-gallery"></a>Přidat aplikaci z Galerie Azure AD

Pokud chcete přidat aplikaci z Galerie Azure AD, postupujte podle následujících kroků:

1.  Otevřete [portál Azure](https://portal.azure.com) a přihlaste se jako **globálního správce** nebo **spolusprávce**

2.  Otevřete **rozšíření Azure Active Directory** kliknutím **všechny služby** v horní části hlavní levé navigační nabídce.

3.  Zadejte **"Azure Active Directory**" v filtru vyhledávacího pole a vyberte **Azure Active Directory** položky.

4.  Klikněte na tlačítko **podnikové aplikace, které** z levé navigační nabídce Azure Active Directory.

5.  klikněte **přidat** v pravém horním rohu na tlačítko **podnikové aplikace, které** podokně.

6.  V **zadejte název** textbox z **přidat z Galerie** části, zadejte název aplikace.

7.  Vyberte aplikaci, kterou chcete nakonfigurovat pro jednotné přihlašování.

8.  Před přidáním aplikace, můžete změnit její název ze **název** textové pole.

9.  Klikněte na tlačítko **přidat** tlačítko Přidat aplikaci.

Po krátké době najdete v podokně Konfigurace aplikace.

### <a name="configure-single-sign-on-for-an-application-from-the-azure-ad-gallery"></a>Konfigurovat jednotné přihlašování pro aplikace z Galerie Azure AD

Pokud chcete konfigurovat jednotné přihlašování pro aplikace, postupujte následujícím způsobem:

1.  <span id="_Hlk477187909" class="anchor"><span id="_Hlk477001983" class="anchor"></span></span>Otevřete [ **portál Azure** ](https://portal.azure.com/) a přihlaste se jako **globálního správce** nebo **ko-správce.**

2.  Otevřete **rozšíření Azure Active Directory** kliknutím **všechny služby** v horní části hlavní levé navigační nabídce.

3.  Zadejte **"Azure Active Directory**" v filtru vyhledávacího pole a vyberte **Azure Active Directory** položky.

4.  Klikněte na tlačítko **podnikové aplikace, které** z levé navigační nabídce Azure Active Directory.

5.  Klikněte na tlačítko **všechny aplikace** Chcete-li zobrazit seznam všech aplikací.

  * Pokud aplikaci chcete, aby se zobrazí tady nevidíte, pomocí **filtru** ovládací prvek v horní části **seznam všech aplikací** a nastavte **zobrazit** možnost k **všechny Aplikace.**

6.  Vyberte aplikaci, kterou chcete konfigurovat jednotné přihlašování.

7.  Po načtení aplikace, klikněte na **jednotného přihlašování** z levé navigační nabídky.

8.  Vyberte **na základě SAML přihlašování** z **režimu** rozevíracího seznamu.

9.  Zadejte požadované hodnoty v **domény a adresy URL.** Tyto hodnoty by měly získat od dodavatele aplikace.

   1. Konfigurace aplikace jako jednotné přihlašování iniciované SP, adresa URL přihlašování je požadovaná hodnota. Některé aplikace je identifikátor také požadovaná hodnota.

   2. Konfigurace aplikace jako jednotné přihlašování iniciované IdP, adresa URL odpovědi je požadovaná hodnota. Některé aplikace je identifikátor také požadovaná hodnota.

10. **Volitelné:** klikněte na tlačítko **zobrazit upřesňující nastavení adresy URL** Pokud budete chtít prohlédnout-požadované hodnoty.

11. V **uživatelské atributy**, vyberte jedinečný identifikátor pro uživatele v **uživatelský identifikátor** rozevíracího seznamu.

12. **Volitelné:** klikněte na tlačítko **zobrazit a upravit všechny ostatní atributy uživatele** upravit atributy zasílat na aplikaci v tokenu SAML, když se uživatelé přihlašují.

   Chcete-li přidat atribut:

   1. Klikněte na tlačítko **přidat atribut**. Zadejte **název** a vyberte položku **hodnotu** z rozevíracího seznamu.

   2. Klikněte na tlačítko **uložit.** Zobrazí nový atribut v tabulce.

13. Klikněte na tlačítko **konfigurace &lt;název aplikace&gt;**  dokumentace přístupu na tom, jak nakonfigurovat jednotné přihlašování v aplikaci. Kromě toho máte adres URL metadat a certifikát nutný k nastavení jednotného přihlašování k aplikaci.

14. Klikněte na tlačítko **Uložit** konfiguraci uložíte.

15. Přiřazení uživatelů k aplikaci.

### <a name="select-user-identifier-and-add-user-attributes-to-be-sent-to-the-application"></a>Vyberte uživatelský identifikátor a přidejte atributy uživatele k odeslání do aplikace

K výběru identifikátor uživatele nebo přidat atributy uživatele, postupujte podle následujících kroků:

1.  Otevřete [ **portál Azure** ](https://portal.azure.com/) a přihlaste se jako **globálního správce** nebo **ko-správce.**

2.  Otevřete **rozšíření Azure Active Directory** kliknutím **všechny služby** v horní části hlavní levé navigační nabídce.

3.  Zadejte **"Azure Active Directory**" v filtru vyhledávacího pole a vyberte **Azure Active Directory** položky.

4.  Klikněte na tlačítko **podnikové aplikace, které** z levé navigační nabídce Azure Active Directory.

5.  Klikněte na tlačítko **všechny aplikace** Chcete-li zobrazit seznam všech aplikací.

  * Pokud nevidíte aplikaci chcete zde zobrazí, použijte **filtru** ovládací prvek v horní části **seznam všech aplikací** a nastavte **zobrazit** možnost k  **Všechny aplikace.**

6.  Vyberte aplikaci, které jste nakonfigurovali jednotné přihlašování.

7.  Po načtení aplikace, klikněte na **jednotného přihlašování** z levé navigační nabídky.

8.  V části **uživatelské atributy** vyberte jedinečný identifikátor pro uživatele v **uživatelský identifikátor** rozevíracího seznamu. Vybraná možnost musí odpovídat očekávaná hodnota v aplikaci k ověření uživatele.

    >[!NOTE]
    >Azure AD vyberte formát pro atribut NameID (identifikátor uživatele) na základě hodnoty vybraných nebo formát požadovanou aplikaci v SAML AuthRequest. Další informace najdete v článku [protokolu SAML přihlašování](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-on-protocol-reference#authnrequest) části NameIDPolicy.
    >
    >

9.  Chcete-li přidat atributy uživatele, klikněte na tlačítko **zobrazení a upravovat všechny ostatní atributy uživatele** upravit atributy zasílat na aplikaci v tokenu SAML, když se uživatelé přihlašují.

   Chcete-li přidat atribut:

   1. Klikněte na tlačítko **přidat atribut**. Zadejte **název** a vyberte položku **hodnotu** z rozevíracího seznamu.

   2. Klikněte na tlačítko **uložit.** Zobrazí nový atribut v tabulce.

### <a name="download-the-azure-ad-metadata-or-certificate"></a>Stáhnout Azure AD metadata nebo certifikát

Chcete-li stáhnout metadata aplikace nebo certifikát z Azure AD, postupujte podle následujících kroků:

1.  Otevřete [ **portál Azure** ](https://portal.azure.com/) a přihlaste se jako **globálního správce** nebo **ko-správce.**

2.  Otevřete **rozšíření Azure Active Directory** kliknutím **všechny služby** v horní části hlavní levé navigační nabídce.

3.  Zadejte **"Azure Active Directory**" v filtru vyhledávacího pole a vyberte **Azure Active Directory** položky.

4.  Klikněte na tlačítko **podnikové aplikace, které** z levé navigační nabídce Azure Active Directory.

5.  Klikněte na tlačítko **všechny aplikace** Chcete-li zobrazit seznam všech aplikací.

  * Pokud aplikaci chcete, aby se zobrazí tady nevidíte, pomocí **filtru** ovládací prvek v horní části **seznam všech aplikací** a nastavte **zobrazit** možnost k **všechny Aplikace.**

6.  Vyberte aplikaci, které jste nakonfigurovali jednotné přihlašování.

7.  Po načtení aplikace, klikněte na **jednotného přihlašování** z levé navigační nabídky.

8.  Přejděte na **SAML podpisový certifikát** části a pak klikněte na **Stáhnout** hodnota sloupce. V závislosti na tom, jaké aplikace vyžaduje konfiguraci jednotné přihlašování uvidíte buď možnost Stáhnout soubor XML s metadaty nebo certifikát.

    Azure AD neposkytuje adresu URL se získat metadata. Metadata mohou být načteny pouze jako soubor XML.

## <a name="how-to-configure-federated-single-sign-on-for-a-non-gallery-application"></a>Postup konfigurace federované jednotné přihlašování pro aplikace bez Galerie

Konfigurace aplikace bez galerie, potřebujete Azure AD premium a aplikace podporuje SAML 2.0. Další informace o verzích služby Azure AD, najdete v článku [ceny služby Azure AD](https://azure.microsoft.com/pricing/details/active-directory/).

-   [Nakonfigurujte hodnoty metadata aplikace ve službě Azure AD (přihlašování adresu URL, identifikátor, adresa URL odpovědi)](#configuring-single-sign-on)

-   [Vyberte uživatelský identifikátor a přidejte atributy uživatele k odeslání do aplikace](#select-user-identifier-and-add-user-attributes-to-be-sent-to-the-application)

-   [Načtení metadat Azure AD a certifikátů](#download-the-azure-ad-metadata-or-certificate)

-   [Konfigurovat Azure AD metadata hodnoty v aplikaci (přihlašování adresu URL, vystavitele, adresy URL odhlašovací a certifikát)](#configuring-single-sign-on)

### <a name="configure-the-applications-metadata-values-in-azure-ad-sign-on-url-identifier-reply-url"></a>Nakonfigurujte hodnoty metadata aplikace ve službě Azure AD (přihlašování adresu URL, identifikátor, adresa URL odpovědi)

Pokud chcete konfigurovat jednotné přihlašování pro aplikace, která není v galerii Azure AD, postupujte podle následujících kroků:

1.  Otevřete [ **portál Azure** ](https://portal.azure.com/) a přihlaste se jako **globálního správce** nebo **ko-správce.**

2.  Otevřete **rozšíření Azure Active Directory** kliknutím **všechny služby** v horní části hlavní levé navigační nabídce.

3.  Zadejte **"Azure Active Directory**" v filtru vyhledávacího pole a vyberte **Azure Active Directory** položky.

4.  Klikněte na tlačítko **podnikové aplikace, které** z levé navigační nabídce Azure Active Directory.

5.  klikněte **přidat** v pravém horním rohu na tlačítko **podnikové aplikace, které** podokně.

6.  Klikněte na tlačítko **aplikace bez Galerie** v **přidat vlastní aplikaci** části.

7.  Zadejte název aplikace **název** textové pole.

8.  Klikněte na tlačítko **přidat** tlačítko Přidat aplikaci.

9.  Po načtení aplikace, klikněte na **jednotného přihlašování** z levé navigační nabídky.

10. Vyberte **na základě SAML přihlašování** v **režimu** rozevíracího seznamu

11. Zadejte požadované hodnoty v **domény a adresy URL.** Tyto hodnoty by měly získat od dodavatele aplikace.

  1. Chcete-li nakonfigurovat aplikaci jako jednotné přihlašování iniciované deklarací identity, zadejte adresa URL odpovědi a identifikátor.

  2. **Volitelné:** konfigurace aplikace jako jednotné přihlašování iniciované SP, adresa URL přihlašování je požadovaná hodnota.

12. V **uživatelské atributy**, vyberte jedinečný identifikátor pro uživatele v **uživatelský identifikátor** rozevíracího seznamu.

13. **Volitelné:** klikněte na tlačítko **zobrazit a upravit všechny ostatní atributy uživatele** upravit atributy zasílat na aplikaci v tokenu SAML, když se uživatelé přihlašují.

   Chcete-li přidat atribut:

   1. Klikněte na tlačítko **přidat atribut**. Zadejte **název** a vyberte položku **hodnotu** z rozevíracího seznamu.

   2. Klikněte na tlačítko **uložit.** Zobrazí nový atribut v tabulce.

14. Klikněte na tlačítko **konfigurace &lt;název aplikace&gt;**  dokumentace přístupu na tom, jak nakonfigurovat jednotné přihlašování v aplikaci. Máte také adresy URL Azure AD a certifikát nutný pro aplikaci.

### <a name="select-user-identifier-and-add-user-attributes-to-be-sent-to-the-application"></a>Vyberte uživatelský identifikátor a přidejte atributy uživatele k odeslání do aplikace

K výběru identifikátor uživatele nebo přidat atributy uživatele, postupujte podle následujících kroků:

1.  Otevřete [ **portál Azure** ](https://portal.azure.com/) a přihlaste se jako **globálního správce** nebo **ko-správce.**

2.  Otevřete **rozšíření Azure Active Directory** kliknutím **všechny služby** v horní části hlavní levé navigační nabídce.

3.  Zadejte **"Azure Active Directory**" v filtru vyhledávacího pole a vyberte **Azure Active Directory** položky.

4.  Klikněte na tlačítko **podnikové aplikace, které** z levé navigační nabídce Azure Active Directory.

5.  Klikněte na tlačítko **všechny aplikace** Chcete-li zobrazit seznam všech aplikací.

  * Pokud aplikaci chcete, aby se zobrazí tady nevidíte, pomocí **filtru** ovládací prvek v horní části **seznam všech aplikací** a nastavte **zobrazit** možnost k **všechny Aplikace.**

6.  Vyberte aplikaci, které jste nakonfigurovali jednotné přihlašování.

7.  Po načtení aplikace, klikněte na **jednotného přihlašování** z levé navigační nabídky.

8.  V části **uživatelské atributy** vyberte jedinečný identifikátor pro uživatele v **uživatelský identifikátor** rozevíracího seznamu. Vybraná možnost musí odpovídat očekávaná hodnota v aplikaci k ověření uživatele.

   >[!NOTE]
   >Azure AD vyberte formát pro atribut NameID (identifikátor uživatele) na základě hodnoty vybraných nebo formát požadovanou aplikaci v SAML AuthRequest. Další informace najdete v článku [protokolu SAML přihlašování](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-on-protocol-reference#authnrequest) části NameIDPolicy.
   >
   >

9.  Chcete-li přidat atributy uživatele, klikněte na tlačítko **zobrazení a upravovat všechny ostatní atributy uživatele** upravit atributy zasílat na aplikaci v tokenu SAML, když se uživatelé přihlašují.

   Chcete-li přidat atribut:

   1. klikněte na tlačítko **přidat atribut**. Zadejte **název** a vyberte položku **hodnotu** z rozevíracího seznamu.

   Klikněte na 2 **uložit.** Zobrazí nový atribut v tabulce.

### <a name="download-the-azure-ad-metadata-or-certificate"></a>Stáhnout Azure AD metadata nebo certifikát

Chcete-li stáhnout metadata aplikace nebo certifikát z Azure AD, postupujte podle následujících kroků:

1.  Otevřete [ **portál Azure** ](https://portal.azure.com/) a přihlaste se jako **globálního správce** nebo **ko-správce.**

2.  Otevřete **rozšíření Azure Active Directory** kliknutím **všechny služby** v horní části hlavní levé navigační nabídce.

3.  Zadejte **"Azure Active Directory**" v filtru vyhledávacího pole a vyberte **Azure Active Directory** položky.

4.  Klikněte na tlačítko **podnikové aplikace, které** z levé navigační nabídce Azure Active Directory.

5.  Klikněte na tlačítko **všechny aplikace** Chcete-li zobrazit seznam všech aplikací.

   * Pokud aplikaci chcete, aby se zobrazí tady nevidíte, pomocí **filtru** ovládací prvek v horní části **seznam všech aplikací** a nastavte **zobrazit** možnost k **všechny Aplikace.**

6.  Vyberte aplikaci, které jste nakonfigurovali jednotné přihlašování.

7.  Po načtení aplikace, klikněte na **jednotného přihlašování** z levé navigační nabídky.

8.  Přejděte na **SAML podpisový certifikát** části a pak klikněte na **Stáhnout** hodnota sloupce. V závislosti na tom, jaké aplikace vyžaduje konfiguraci jednotné přihlašování uvidíte buď možnost Stáhnout soubor XML s metadaty nebo certifikát.

    Azure AD neposkytuje adresu URL se získat metadata. Metadata mohou být načteny pouze jako soubor XML.

## <a name="how-to-configure-password-single-sign-on-for-an-azure-ad-gallery-application"></a>Postup konfigurace hesel jednotné přihlašování pro aplikaci Galerie Azure AD

Pro konfiguraci aplikace z Galerie Azure AD, které potřebujete:

-   [Přidat aplikaci z Galerie Azure AD](#add-an-application)

-   [Konfigurace aplikace pro heslo jednotné přihlašování](#configure-the-application)

### <a name="add-an-application-from-the-azure-ad-gallery"></a>Přidat aplikaci z Galerie Azure AD

Pokud chcete přidat aplikaci z Galerie Azure AD, postupujte podle následujících kroků:

1.  Otevřete [portál Azure](https://portal.azure.com) a přihlaste se jako **globálního správce** nebo **spolusprávce**

2.  Otevřete **rozšíření Azure Active Directory** kliknutím **všechny služby** v horní části hlavní levé navigační nabídce.

3.  Zadejte **"Azure Active Directory**" v filtru vyhledávacího pole a vyberte **Azure Active Directory** položky.

4.  Klikněte na tlačítko **podnikové aplikace, které** z levé navigační nabídce Azure Active Directory.

5.  klikněte **přidat** v pravém horním rohu na tlačítko **podnikové aplikace, které** podokně.

6.  V **zadejte název** textbox z **přidat z Galerie** části, zadejte název aplikace

7.  Vyberte aplikaci, kterou chcete nakonfigurovat pro jednotné přihlašování

8.  Před přidáním aplikace, můžete změnit její název ze **název** textové pole.

9.  Klikněte na tlačítko **přidat** tlačítko Přidat aplikaci.

Po krátké době najdete v podokně Konfigurace aplikace.

### <a name="configure-the-application-for-password-single-sign-on"></a>Konfigurace aplikace pro heslo jednotné přihlašování

Pokud chcete konfigurovat jednotné přihlašování pro aplikace, postupujte následujícím způsobem:

1.  Otevřete [ **portál Azure** ](https://portal.azure.com/) a přihlaste se jako **globálního správce** nebo **ko-správce.**

2.  Otevřete **rozšíření Azure Active Directory** kliknutím **všechny služby** v horní části hlavní levé navigační nabídce.

3.  Zadejte **"Azure Active Directory**" v filtru vyhledávacího pole a vyberte **Azure Active Directory** položky.

4.  Klikněte na tlačítko **podnikové aplikace, které** z levé navigační nabídce Azure Active Directory.

5.  Klikněte na tlačítko **všechny aplikace** Chcete-li zobrazit seznam všech aplikací.

 * Pokud aplikaci chcete, aby se zobrazí tady nevidíte, pomocí **filtru** ovládací prvek v horní části **seznam všech aplikací** a nastavte **zobrazit** možnost k **všechny Aplikace.**

6.  Vyberte aplikaci, kterou chcete konfigurovat jednotné přihlašování

7.  Po načtení aplikace, klikněte na **jednotného přihlašování** z levé navigační nabídky.

8.  Vyberte režim **založené na heslech přihlášení.**

9.  Přiřazení uživatelů k aplikaci.

10. Kromě toho můžete taky zadat přihlašovací údaje jménem uživatele výběrem řádky uživatelů a kliknete na **pověření aktualizace** a zadání uživatelského jména a hesla jménem uživatele. Uživatelé, jinak se výzva k zadání sami přihlašovací údaje při spuštění.

## <a name="how-to-configure-password-single-sign-on-for-a-non-gallery-application"></a>Postup konfigurace hesel jednotné přihlašování pro aplikace bez Galerie

Pro konfiguraci aplikace z Galerie Azure AD, které potřebujete:

-   [Přidání aplikace bez Galerie](#add-a-non-gallery-application)

-   [Konfigurace aplikace pro heslo jednotné přihlašování](#configure-the-application-for-password-single-sign-on)

### <a name="add-a-non-gallery-application"></a>Přidání aplikace bez Galerie

Pokud chcete přidat aplikaci z Galerie Azure AD, postupujte podle následujících kroků:

1.  Otevřete [portál Azure](https://portal.azure.com) a přihlaste se jako **globálního správce** nebo **spolusprávce**

2.  Otevřete **rozšíření Azure Active Directory** kliknutím **všechny služby** v horní části hlavní levé navigační nabídce.

3.  Zadejte **"Azure Active Directory**" v filtru vyhledávacího pole a vyberte **Azure Active Directory** položky.

4.  Klikněte na tlačítko **podnikové aplikace, které** z levé navigační nabídce Azure Active Directory.

5.  klikněte **přidat** v pravém horním rohu na tlačítko **podnikové aplikace, které** podokně.

6.  Klikněte na tlačítko **Galerie jiná aplikace.**

7.  Zadejte název vaší aplikace v **název** textové pole. Vyberte **přidat.**

Po krátké době nebudete moci zobrazit podokno Konfigurace aplikace.

### <a name="configure-the-application-for-password-single-sign-on"></a>Konfigurace aplikace pro heslo jednotné přihlašování

Pokud chcete konfigurovat jednotné přihlašování pro aplikace, postupujte následujícím způsobem:

1.  Otevřete [ **portál Azure** ](https://portal.azure.com/) a přihlaste se jako **globálního správce** nebo **ko-správce.**

2.  Otevřete **rozšíření Azure Active Directory** kliknutím **všechny služby** v horní části hlavní levé navigační nabídce.

3.  Zadejte **"Azure Active Directory**" v filtru vyhledávacího pole a vyberte **Azure Active Directory** položky.

4.  Klikněte na tlačítko **podnikové aplikace, které** z levé navigační nabídce Azure Active Directory.

5.  Klikněte na tlačítko **všechny aplikace** Chcete-li zobrazit seznam všech aplikací.

 * Pokud aplikaci chcete, aby se zobrazí tady nevidíte, pomocí **filtru** ovládací prvek v horní části **seznam všech aplikací** a nastavte **zobrazit** možnost k **všechny Aplikace.**

6.  Vyberte aplikaci, kterou chcete konfigurovat jednotné přihlašování.

7.  Po načtení aplikace, klikněte na **jednotného přihlašování** z levé navigační nabídky.

8.  Vyberte režim **založené na heslech přihlášení.**

9.  Zadejte **přihlašovací adresa URL**. Toto je adresa URL, kde uživatelé zadat svoje uživatelské jméno a heslo pro přihlášení. Zkontrolujte, zda že pole přihlášení jsou viditelné na adrese URL.

10. Přiřazení uživatelů k aplikaci.

11. Kromě toho můžete taky zadat přihlašovací údaje jménem uživatele výběrem řádky uživatelů a kliknete na **pověření aktualizace** a zadání uživatelského jména a hesla jménem uživatele. Uživatelé, jinak se výzva k zadání sami přihlašovací údaje při spuštění.

## <a name="how-to-assign-a-user-to-an-application-directly"></a>Postup přiřazení uživatele k aplikaci přímo

Jeden nebo více uživatelů přiřadit přímo k aplikaci, postupujte podle následujících kroků:

1.  Otevřete [ **portál Azure** ](https://portal.azure.com/) a přihlaste se jako **globálního správce.**

2.  Otevřete **rozšíření Azure Active Directory** kliknutím **všechny služby** v horní části hlavní levé navigační nabídce.

3.  Zadejte **"Azure Active Directory**" v filtru vyhledávacího pole a vyberte **Azure Active Directory** položky.

4.  Klikněte na tlačítko **podnikové aplikace, které** z levé navigační nabídce Azure Active Directory.

5.  Klikněte na tlačítko **všechny aplikace** Chcete-li zobrazit seznam všech aplikací.

  * Pokud aplikaci chcete, aby se zobrazí tady nevidíte, pomocí **filtru** ovládací prvek v horní části **seznam všech aplikací** a nastavte **zobrazit** možnost k **všechny Aplikace.**

6.  Vyberte aplikaci, kterou chcete přiřadit uživatele k ze seznamu.

7.  Po načtení aplikace, klikněte na **uživatelů a skupin** z levé navigační nabídky.

8.  Klikněte na tlačítko **přidat** tlačítko na **uživatelů a skupin** seznamu otevřete **přidat přiřazení** podokně.

9.  Klikněte na tlačítko **uživatelů a skupin** pro výběr **přidat přiřazení** podokně.

10. Zadejte **celý název** nebo **e-mailová adresa** uživatele vás zajímá přiřazení do **hledat podle jména nebo e-mailové adresy** vyhledávacího pole.

11. Najeďte myší **uživatele** v seznamu na nich **políčko**. Klikněte na zaškrtávací políčko vedle profilové fotky nebo logo pro přidání uživatelů do uživatele **vybrané** seznamu.

12. **Volitelné:** Pokud byste chtěli **přidat více než jeden uživatel**, zadejte v jiném **celý název** nebo **e-mailová adresa** do **vyhledávání podle názvu nebo e-mailová adresa** pole pro vyhledávání a klikněte na zaškrtávací políčko, chcete-li přidat tento uživatel **vybrané** seznamu.

13. Po dokončení výběru uživatelů klikněte na **vyberte** tlačítko, které chcete přidat do seznamu uživatelů a skupin, které chcete přiřadit k aplikaci.

14. **Volitelné:** klikněte na tlačítko **vybrat roli** selektor v **přidat přiřazení** podokně vyberte roli přiřadit uživatele, který jste vybrali.

15. Klikněte **přiřadit** tlačítko přiřadit aplikace pro vybraného uživatele.

Po krátké době uživatele, které jste vybrali moci spustit tyto aplikace na přístupovém panelu.

## <a name="if-these-troubleshooting-steps-do-not-the-resolve-the-issue"></a>Pokud tyto kroky řešení potíží se není vyřešit problém

Otevřete lístek podpory s následujícími informacemi, pokud je k dispozici:

-   ID chyby korelace

-   Hlavní název uživatele (e-mailovou adresu uživatele)

-   TenantID

-   Typ prohlížeče

-   Dojde k časové pásmo a čas nebo období při chybě

-   Fiddler trasování

## <a name="next-steps"></a>Další postup
[Zadejte jednotné přihlašování pro vaše aplikace s Proxy aplikace](manage-apps/application-proxy-configure-single-sign-on-with-kcd.md)

