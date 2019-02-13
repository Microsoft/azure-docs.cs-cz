---
title: Přiřazená aplikace nezobrazuje na přístupovém panelu | Dokumentace Microsoftu
description: Řešit potíže způsobující aplikace nezobrazuje na přístupovém panelu
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
ms.topic: article
ms.date: 09/09/2018
ms.author: celested
ms.reviwer: japere
ms.collection: M365-identity-device-management
ms.openlocfilehash: 21ed169003caded8ef80689ceec61f6d2fc4305c
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2019
ms.locfileid: "56178987"
---
# <a name="an-assigned-application-is-not-appearing-on-the-access-panel"></a>Přiřazená aplikace nezobrazuje na přístupovém panelu

Přístupový Panel je webový portál, který umožňuje uživateli pomocí pracovního nebo školního účtu ve službě Azure Active Directory (Azure AD) k zobrazení a spuštění aplikace založené na cloudu, že správce Azure AD udělil jim přístup k. Tyto aplikace jsou konfigurovány jménem uživatele na portálu Azure AD. Aplikace musí být správně nakonfigurovali a přiřadili pro uživatele nebo skupiny, jejichž je uživatel členem skupiny a prohlédněte si aplikaci na přístupovém panelu.

Typ aplikace, které uživatel může zobrazit spadají do následujících kategorií:

-   Aplikace Office 365

-   Aplikace Microsoftu a třetích stran nakonfigurovat s jednotným Přihlašováním federační

-   Aplikace založené na heslech jednotného přihlašování

-   Aplikace se stávajícími řešeními jednotného přihlašování

## <a name="general-issues-to-check-first"></a>Obecné problémy a proveďte nejprve kontrolu

-   Pokud aplikace byla právě přidali na uživatele, zkuste přihlásit dovnitř a ven znovu do přístupového panelu uživatele po několika minutách ověřte, zda je aplikace přidána.

-   Pokud licenci byla odebrána pouze od uživatele nebo skupinu, kterou je uživatel že členem to může trvat dlouhou dobu, v závislosti na velikosti a složitosti skupiny změny má být provedeno. Povolit pro čas navíc před přihlášením na přístupovém panelu.

## <a name="problems-related-to-application-configuration"></a>Problémy související s konfigurací aplikace

Aplikace nemusí povolí uživatele přístupovém panelu, protože není správně nakonfigurována. Níže jsou uvedeny některé způsoby, jak můžete řešit problémy související s konfigurací aplikace:

-   [Jak nakonfigurovat federované jednotné přihlašování pro aplikaci Galerie Azure AD](#how-to-configure-federated-single-sign-on-for-an-azure-ad-gallery-application)

-   [Jak nakonfigurovat federované jednotné přihlašování pro aplikaci mimo Galerii](#how-to-configure-federated-single-sign-on-for-a-non-gallery-application)

-   [Postup konfigurace hesel jednotné přihlašování – aplikace pro aplikaci Galerie Azure AD](#how-to-configure-password-single-sign-on-for-a-non-gallery-application)

-   [Postup konfigurace hesel jednotné přihlašování – aplikace pro aplikaci mimo Galerii](#how-to-configure-password-single-sign-on-for-a-non-gallery-application)

### <a name="how-to-configure-federated-single-sign-on-for-an-azure-ad-gallery-application"></a>Jak nakonfigurovat federované jednotné přihlašování pro aplikaci Galerie Azure AD

Všechny aplikace v galerii Azure AD povolit možnost Enterprise Single Sign-On obsahuje podrobný návod k dispozici. Můžete přistupovat [seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](https://azure.microsoft.com/documentation/articles/active-directory-saas-tutorial-list/) podrobností podrobné pokyny.

Konfigurace aplikace z Galerie Azure AD, které je potřeba:

-   [Přidat aplikaci z Galerie Azure AD](#add-an-application-from-the-azure-ad-gallery)

-   [Nakonfigurujte hodnoty metadat aplikace ve službě Azure AD (přihlašování adresu URL, identifikátoru, adresa URL odpovědi)](#configure-single-sign-on-for-an-application-from-the-azure-ad-gallery)

-   [Vyberte identifikátor uživatele a přidat atributy uživatele k odeslání do aplikace](#select-user-identifier-and-add-user-attributes-to-be-sent-to-the-application)

-   [Načíst metadata služby Azure AD a certifikátů](#download-the-azure-ad-metadata-or-certificate)

-   [Nakonfigurujte hodnoty metadat služby Azure AD v aplikaci (přihlašování adresu URL, Vystavitel, odhlašovací adresa URL a certifikát)](#configure-single-sign-on-for-an-application-from-the-azure-ad-gallery)

#### <a name="add-an-application-from-the-azure-ad-gallery"></a>Přidat aplikaci z Galerie Azure AD

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

Po krátké době budete moci zobrazit podokno konfiguračních vaší aplikace.

#### <a name="configure-single-sign-on-for-an-application-from-the-azure-ad-gallery"></a>Konfigurace jednotného přihlašování pro aplikaci z Galerie Azure AD

Pokud chcete nakonfigurovat jednotné přihlašování pro aplikaci, použijte následující postup:

1.  Otevřít [ **webu Azure portal** ](https://portal.azure.com/) a přihlaste se jako **globálního správce** nebo **Spolusprávce.**

2.  Otevřít **rozšíření Azure Active Directory** kliknutím **všechny služby** v horní části hlavní navigační nabídce vlevo.

3.  Zadejte **"Azure Active Directory**" do vyhledávacího pole filtrovat a vybrat **Azure Active Directory** položky.

4.  Klikněte na tlačítko **podnikové aplikace** levé navigační nabídce Azure Active Directory.

5.  Klikněte na tlačítko **všechny aplikace** zobrazíte seznam všech aplikací.

  * Pokud nevidíte aplikaci, kterou má zobrazit tady, použijte **filtr** ovládacího prvku v horní části **seznam všech aplikací** a nastavit **zobrazit** umožňuje **všechny Aplikace.**

6.  Vyberte aplikaci, kterou chcete nakonfigurovat jednotné přihlašování.

7.  Po načtení aplikace, klikněte na tlačítko **jednotného přihlašování** levé navigační nabídce aplikace.

8.  Vyberte **přihlašování na základě SAML** z **režimu** rozevíracího seznamu.

9.  Zadejte požadované hodnoty v **domény a adresy URL.** Tyto hodnoty byste měli získat od dodavatele aplikace.

   1. Konfigurace aplikace jako iniciovaného Zprostředkovatelem přihlašování jednotné přihlášení na adrese URL je povinná hodnota. Pro některé aplikace identifikátor je také požadovaná hodnota.

   2. Konfigurace aplikace jako jednotné přihlašování iniciované zprostředkovatele identity, adresa URL odpovědi je povinná hodnota. Pro některé aplikace identifikátor je také požadovaná hodnota.

10. **Volitelné:** klikněte na tlačítko **zobrazit pokročilé nastavení URL** Pokud chcete zobrazit nepovinné hodnoty.

11. V **atributy uživatele**, vyberte jedinečný identifikátor pro vaši uživatelé v **identifikátor uživatele** rozevíracího seznamu.

12. **Volitelné:** klikněte na tlačítko **zobrazit a upravit všechny ostatní atributy uživatele** upravit atributy, které se odešlou na aplikaci v tokenu SAML, při přihlášení uživatele.

   Chcete-li přidat atribut:

   1. Klikněte na tlačítko **přidat atribut**. Zadejte **název** a vyberte položku **hodnota** z rozevíracího seznamu.

   2. Klikněte na tlačítko **uložit.** V tabulce se zobrazí nový atribut.

13. Klikněte na tlačítko **konfigurovat &lt;název_aplikace&gt;**  pro přístup k dokumentaci o tom, jak nakonfigurovat jednotné přihlašování v aplikaci. Máte také adres URL metadat a certifikát je potřebný pro nastavení jednotné přihlašování v aplikaci.

14. Klikněte na tlačítko **Uložit** uložte konfiguraci.

15. Přiřazení uživatelů k aplikaci.

#### <a name="select-user-identifier-and-add-user-attributes-to-be-sent-to-the-application"></a>Vyberte identifikátor uživatele a přidat atributy uživatele k odeslání do aplikace

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
   >Azure AD vybere ve formátu NameID atributu (identifikátor uživatele), na základě hodnoty vybrané nebo formátu požadovaná aplikací v SAML AuthRequest. Další informace najdete v článku [protokol jednotné přihlašování SAML](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-on-protocol-reference#authnrequest) části NameIDPolicy.
   >
   >

9.  Chcete-li přidat atributy uživatele, klikněte na tlačítko **zobrazit a upravit všechny ostatní atributy uživatele** upravit atributy, které se odešlou na aplikaci v tokenu SAML, při přihlášení uživatele.

   Chcete-li přidat atribut:

   1. Klikněte na tlačítko **přidat atribut**. Zadejte **název** a vyberte položku **hodnota** z rozevíracího seznamu.

   2. Klikněte na tlačítko **uložit.** Zobrazí se nový atribut v tabulce.

#### <a name="download-the-azure-ad-metadata-or-certificate"></a>Stáhnout Azure AD metadata nebo certifikát

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

### <a name="how-to-configure-federated-single-sign-on-for-a-non-gallery-application"></a>Jak nakonfigurovat federované jednotné přihlašování pro aplikaci mimo Galerii

Pokud chcete nakonfigurovat aplikaci mimo galerii, musíte mít Azure AD premium a aplikace podporuje SAML 2.0. Další informace o verzích služby Azure AD, najdete [ceny služby Azure AD](https://azure.microsoft.com/pricing/details/active-directory/).

-   [Nakonfigurujte hodnoty metadat aplikace ve službě Azure AD (přihlašování adresu URL, identifikátoru, adresa URL odpovědi)](#configure-single-sign-on-for-an-application-from-the-azure-ad-gallery)

-   [Vyberte identifikátor uživatele a přidat atributy uživatele k odeslání do aplikace](#select-user-identifier-and-add-user-attributes-to-be-sent-to-the-application)

-   [Načíst metadata služby Azure AD a certifikátů](#download-the-azure-ad-metadata-or-certificate)

-   [Nakonfigurujte hodnoty metadat služby Azure AD v aplikaci (přihlašování adresu URL, Vystavitel, odhlašovací adresa URL a certifikát)](#configure-the-application-for-password-single-sign-on-1)

#### <a name="configure-the-applications-metadata-values-in-azure-ad-sign-on-url-identifier-reply-url"></a>Nakonfigurujte hodnoty metadat aplikace ve službě Azure AD (přihlašování adresu URL, identifikátoru, adresa URL odpovědi)

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

10. Vyberte **přihlašování na základě SAML** v **režimu** rozevíracího seznamu.

11. Zadejte požadované hodnoty v **domény a adresy URL.** Tyto hodnoty byste měli získat od dodavatele aplikace.

   1. Pokud chcete nakonfigurovat aplikace jako jednotné přihlašování zahájené pomocí IdP, zadejte příslušnou odpovědní adresu URL a identifikátoru.

   2.  **Volitelné:** Konfigurace aplikace jako iniciovaného Zprostředkovatelem přihlašování jednotné přihlášení na adrese URL je povinná hodnota.

12. V **atributy uživatele**, vyberte jedinečný identifikátor pro vaši uživatelé v **identifikátor uživatele** rozevíracího seznamu.

13. **Volitelné:** klikněte na tlačítko **zobrazit a upravit všechny ostatní atributy uživatele** upravit atributy, které se odešlou na aplikaci v tokenu SAML, při přihlášení uživatele.

   Chcete-li přidat atribut:

   1. Klikněte na tlačítko **přidat atribut**. Zadejte **název** a vyberte položku **hodnota** z rozevíracího seznamu.

   2. Klikněte na tlačítko **uložit.** V tabulce se zobrazí nový atribut.

14. Klikněte na tlačítko **konfigurovat &lt;název_aplikace&gt;**  pro přístup k dokumentaci o tom, jak nakonfigurovat jednotné přihlašování v aplikaci. Máte také adresy URL Azure AD a certifikátů vyžadovaných pro aplikaci.

#### <a name="select-user-identifier-and-add-user-attributes-to-be-sent-to-the-application"></a>Vyberte identifikátor uživatele a přidat atributy uživatele k odeslání do aplikace

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
   >Azure AD vybere ve formátu NameID atributu (identifikátor uživatele), na základě hodnoty vybrané nebo formátu požadovaná aplikací v SAML AuthRequest. Další informace najdete v článku [protokol jednotné přihlašování SAML](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-on-protocol-reference#authnrequest) části NameIDPolicy.
   >
   >

9.  Chcete-li přidat atributy uživatele, klikněte na tlačítko **zobrazit a upravit všechny ostatní atributy uživatele** upravit atributy, které se odešlou na aplikaci v tokenu SAML, při přihlášení uživatele.

   Chcete-li přidat atribut:

   1. Klikněte na tlačítko **přidat atribut**. Zadejte **název** a vyberte položku **hodnota** z rozevíracího seznamu.

   2. Klikněte na tlačítko **uložit.** V tabulce se zobrazí nový atribut.

#### <a name="download-the-azure-ad-metadata-or-certificate"></a>Stáhnout Azure AD metadata nebo certifikát

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

### <a name="how-to-configure-password-single-sign-on-for-an-azure-ad-gallery-application"></a>Postup konfigurace hesel jednotného přihlašování pro aplikaci Galerie Azure AD

Konfigurace aplikace z Galerie Azure AD, které je potřeba:

-   [Přidat aplikaci z Galerie Azure AD](#add-an-application-from-the-azure-ad-gallery)

-   [Konfigurace aplikace pro heslo jednotného přihlašování](#configure-the-application-for-password-single-sign-on)

#### <a name="add-an-application-from-the-azure-ad-gallery"></a>Přidat aplikaci z Galerie Azure AD

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

#### <a name="configure-the-application-for-password-single-sign-on"></a>Konfigurace aplikace pro heslo jednotného přihlašování

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

9.  [Přiřazení uživatelů k aplikaci](#how-to-assign-a-user-to-an-application-directly).

10. Kromě toho můžete také zadat přihlašovací údaje jménem uživatele výběr řádků uživatele a kliknutím na **aktualizaci přihlašovacích údajů** a zadáním uživatelského jména a hesla jménem uživatele. V opačném případě uživatelé vyzváni k zadání samotné přihlašovací údaje při spuštění.

### <a name="how-to-configure-password-single-sign-on-for-a-non-gallery-application"></a>Postup konfigurace hesel jednotného přihlašování pro aplikaci mimo Galerii

Konfigurace aplikace z Galerie Azure AD, které je potřeba:

-   [Přidat aplikaci mimo Galerii](#add-a-non-gallery-application)

-   [Konfigurace aplikace pro heslo jednotného přihlašování](#configure-the-application-for-password-single-sign-on)

#### <a name="add-a-non-gallery-application"></a>Přidat aplikaci mimo Galerii

Přidat aplikaci z Galerie Azure AD, postupujte podle následujících kroků:

1.  Otevřít [webu Azure portal](https://portal.azure.com) a přihlaste se jako **globálního správce** nebo **spolusprávce**.

2.  Otevřít **rozšíření Azure Active Directory** kliknutím **všechny služby** v horní části hlavní navigační nabídce vlevo.

3.  Zadejte **"Azure Active Directory**" do vyhledávacího pole filtrovat a vybrat **Azure Active Directory** položky.

4.  Klikněte na tlačítko **podnikové aplikace** levé navigační nabídce Azure Active Directory.

5.  Klikněte na tlačítko **přidat** tlačítko v pravém horním rohu **podnikové aplikace** podokně.

6.  Klikněte na tlačítko **aplikace mimo galerii.**

7.  Zadejte název vaší aplikace v **název** textového pole. Vyberte **přidat.**

Po krátké době budete moci zobrazit podokno konfiguračních vaší aplikace.

#### <a name="configure-the-application-for-password-single-sign-on"></a>Konfigurace aplikace pro heslo jednotného přihlašování

Pokud chcete nakonfigurovat jednotné přihlašování pro aplikaci, použijte následující postup:

1.  Otevřít [ **webu Azure portal** ](https://portal.azure.com/) a přihlaste se jako **globálního správce** nebo **Spolusprávce.**

2.  Otevřít **rozšíření Azure Active Directory** kliknutím **všechny služby** v horní části hlavní navigační nabídce vlevo.

3.  Zadejte **"Azure Active Directory**" do vyhledávacího pole filtrovat a vybrat **Azure Active Directory** položky.

4.  Klikněte na tlačítko **podnikové aplikace** levé navigační nabídce Azure Active Directory.

5.  Klikněte na tlačítko **všechny aplikace** zobrazíte seznam všech aplikací.

    1.  Pokud nevidíte aplikaci, kterou má zobrazit tady, použijte **filtr** ovládacího prvku v horní části **seznam všech aplikací** a nastavit **zobrazit** umožňuje **všechny Aplikace.**

6.  Vyberte aplikaci, kterou chcete nakonfigurovat jednotné přihlašování.

7.  Po načtení aplikace, klikněte na tlačítko **jednotného přihlašování** levé navigační nabídce aplikace.

8.  Vyberte režim **přihlašování na základě heslo.**

9.  Zadejte **přihlašovací adresa URL**. Toto je adresa URL, kde uživatelé zadat svoje uživatelské jméno a heslo pro přihlášení k aplikaci. Ujistěte se, že pole přihlášení jsou viditelné na adrese URL.

10. [Přiřazení uživatelů k aplikaci](#how-to-assign-a-user-to-an-application-directly).

11. Kromě toho můžete také zadat přihlašovací údaje jménem uživatele výběr řádků uživatele a kliknutím na **aktualizaci přihlašovacích údajů** a zadáním uživatelského jména a hesla jménem uživatele. V opačném případě uživatelé vyzváni k zadání samotné přihlašovací údaje při spuštění.

## <a name="problems-related-to-assigning-applications-to-users"></a>Problémy související s přiřazením aplikace pro uživatele

Uživatel možná se nezobrazí aplikace na přístupovém panelu vzhledem k tomu, že nejsou přiřazené k aplikaci. Níže jsou uvedeny některé způsoby, jak zkontrolovat:

-   [Zkontrolujte, zda uživatel je přiřazena aplikace](#check-if-a-user-is-assigned-to-the-application)

-   [Jak přiřadit uživatele k aplikaci přímo](#how-to-assign-a-user-to-an-application-directly)

-   [Zkontrolujte, zda uživatel je přiřazena licence spojené s aplikací](#check-if-a-user-is-under-a-license-related-to-the-application)

-   [Přiřazení licence pro uživatele](#how-to-assign-a-user-a-license)

### <a name="check-if-a-user-is-assigned-to-the-application"></a>Zkontrolujte, zda uživatel je přiřazena aplikace

Pokud chcete zkontrolovat, zda uživatel je přiřazena aplikace, postupujte podle následujících kroků:

1.  Otevřít [ **webu Azure portal** ](https://portal.azure.com/) a přihlaste se jako **globálního správce.**

2.  Otevřít **rozšíření Azure Active Directory** kliknutím **všechny služby** v horní části hlavní navigační nabídce vlevo.

3.  Zadejte **"Azure Active Directory**" do vyhledávacího pole filtrovat a vybrat **Azure Active Directory** položky.

4.  Klikněte na tlačítko **podnikové aplikace** levé navigační nabídce Azure Active Directory.

5.  Klikněte na tlačítko **všechny aplikace** zobrazíte seznam všech aplikací.

6.  **Hledání** pro název dané žádosti.

7.  Klikněte na tlačítko **uživatelů a skupin**.

8.  Zkontrolujte, zda uživatel je přiřazena aplikace.

   * Pokud ne, postupujte podle kroků v "Jak se přímo přiřadit uživatele k aplikaci" Uděláte to tak.

### <a name="how-to-assign-a-user-to-an-application-directly"></a>Jak přiřadit uživatele k aplikaci přímo

Jeden nebo více uživatelů přiřadit přímo k aplikaci, postupujte podle následujících kroků:

1.  Otevřít [ **webu Azure portal** ](https://portal.azure.com/) a přihlaste se jako **globálního správce**.

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

### <a name="check-if-a-user-is-under-a-license-related-to-the-application"></a>Zkontrolujte, jestli je uživatel v rámci licence spojené s aplikací

Pokud chcete zkontrolovat přiřazené licence pro uživatele, postupujte podle kroků níže:

1.  Otevřít [ **webu Azure portal** ](https://portal.azure.com/) a přihlaste se jako **globálního správce.**

2.  Otevřít **rozšíření Azure Active Directory** kliknutím **všechny služby** v horní části hlavní navigační nabídce vlevo.

3.  Zadejte **"Azure Active Directory**" do vyhledávacího pole filtrovat a vybrat **Azure Active Directory** položky.

4.  Klikněte na tlačítko **uživatelů a skupin** v navigační nabídce.

5.  Klikněte na tlačítko **všichni uživatelé**.

6.  **Hledání** pro uživatele, které vás zajímají a **klikněte na odpovídající řádek** k výběru.

7.  Klikněte na tlačítko **licence** zobrazíte, která aktuálně licencí uživateli přiřadila.

  * Pokud uživateli přiřazena licence k Office to umožňuje aplikacím Office první strany zobrazovat na přístupovém panelu uživatele.

### <a name="how-to-assign-a-user-a-license"></a>Jak přiřadit uživateli licenci 

Chcete-li přiřadit licenci uživateli, použijte následující postup:

1.  Otevřít [ **webu Azure portal** ](https://portal.azure.com/) a přihlaste se jako **globálního správce.**

2.  Otevřít **rozšíření Azure Active Directory** kliknutím **všechny služby** v horní části hlavní navigační nabídce vlevo.

3.  Zadejte **"Azure Active Directory**" do vyhledávacího pole filtrovat a vybrat **Azure Active Directory** položky.

4.  Klikněte na tlačítko **uživatelů a skupin** v navigační nabídce.

5.  Klikněte na tlačítko **všichni uživatelé**.

6.  **Hledání** pro uživatele, které vás zajímají a **klikněte na odpovídající řádek** k výběru.

7.  Klikněte na tlačítko **licence** zobrazíte, která aktuálně licencí uživateli přiřadila.

8.  Klikněte na tlačítko **přiřadit** tlačítko.

9.  Vyberte **jeden nebo více produktů** ze seznamu dostupných produktů.

10. **Volitelné** klikněte na tlačítko **možnosti přiřazení** položku, kterou chcete zásady přiřazovat produktů. Klikněte na tlačítko **Ok** po dokončení.

11. Klikněte na tlačítko **přiřadit** tlačítko tyto licence přiřadit k tomuto uživateli.

## <a name="problems-related-to-assigning-applications-to-groups"></a>Problémy související se přiřazení aplikací do skupin

Uživatel může zobrazit aplikace na přístupovém panelu protože jsou součástí skupiny, které se přiřadila aplikace. Níže jsou uvedeny některé způsoby, jak zkontrolovat:

-   [Kontrola členství uživatele ve skupinách](#check-a-users-group-memberships)

-   [Jak přiřadit aplikace do skupiny přímo](#how-to-assign-an-application-to-a-group-directly)

-   [Zkontrolujte, jestli uživatel je součástí skupiny přiřazené k licenci](#check-if-a-user-is-part-of-group-assigned-to-a-license)

-   [Přiřazení licence ke skupině](#how-to-assign-a-license-to-a-group)

### <a name="check-a-users-group-memberships"></a>Kontrola členství uživatele ve skupinách

Pokud chcete zkontrolovat členství, postupujte podle kroků níže:

1.  Otevřít [ **webu Azure portal** ](https://portal.azure.com/) a přihlaste se jako **globálního správce.**

2.  Otevřít **rozšíření Azure Active Directory** kliknutím **všechny služby** v horní části hlavní navigační nabídce vlevo.

3.  Zadejte **"Azure Active Directory**" do vyhledávacího pole filtrovat a vybrat **Azure Active Directory** položky.

4.  Klikněte na tlačítko **uživatelů a skupin** v navigační nabídce.

5.  Klikněte na tlačítko **všichni uživatelé**.

6.  **Hledání** pro uživatele, které vás zajímají a **klikněte na odpovídající řádek** k výběru.

7.  Klikněte na tlačítko **skupiny**.

8.  Zaškrtněte, pokud chcete zjistit, zda uživatel je součástí skupiny přiřazené k aplikaci.

  * Pokud chcete odebrat uživatele ze skupiny, **klikněte na odpovídající řádek** skupinu a vyberte odstranit.

### <a name="how-to-assign-an-application-to-a-group-directly"></a>Jak přiřadit aplikace do skupiny přímo

Jednu nebo více skupin přiřadit přímo k aplikaci, postupujte podle následujících kroků:

1.  Otevřít [ **webu Azure portal** ](https://portal.azure.com/) a přihlaste se jako **globálního správce**.

2.  Otevřít **rozšíření Azure Active Directory** kliknutím **všechny služby** v horní části hlavní navigační nabídce vlevo.

3.  Zadejte **"Azure Active Directory**" do vyhledávacího pole filtrovat a vybrat **Azure Active Directory** položky.

4.  Klikněte na tlačítko **podnikové aplikace** levé navigační nabídce Azure Active Directory.

5.  Klikněte na tlačítko **všechny aplikace** zobrazíte seznam všech aplikací.

  * Pokud nevidíte aplikaci, kterou má zobrazit tady, použijte **filtr** ovládacího prvku v horní části **seznam všech aplikací** a nastavit **zobrazit** umožňuje **všechny Aplikace.**

6.  Vyberte aplikaci, kterou chcete přiřadit uživatele ze seznamu.

7.  Po načtení aplikace, klikněte na tlačítko **uživatelů a skupin** levé navigační nabídce aplikace.

8.  Klikněte na tlačítko **přidat** tlačítko nahoře **uživatelů a skupin** seznamu a otevře **přidat přiřazení** podokně.

9.  Klikněte na tlačítko **uživatelů a skupin** selektor z **přidat přiřazení** podokně.

10. Zadejte **název celé skupiny** zájem o přiřazení do skupiny **hledat podle jména nebo e-mailové adresy** vyhledávacího pole.

11. Najeďte myší **skupiny** v seznamu zobrazíte **zaškrtávací políčko**. Klikněte na zaškrtávací políčko vedle profilové fotky nebo logo, které chcete přidat uživatele do skupiny **vybrané** seznamu.

12. **Volitelné:** Pokud byste chtěli **přidat více než jednu skupinu**, typ v jiném **název celé skupiny** do **hledat podle jména nebo e-mailové adresy** vyhledávací pole a klikněte na zaškrtávací políčko a přidáním této skupiny Chcete **vybrané** seznamu.

13. Když jste hotovi s výběrem skupin, klikněte na tlačítko **vyberte** tlačítko pro přidání do seznamu uživatelů a skupin pro přiřazení k aplikaci.

14. **Volitelné:** klikněte na tlačítko **vybrat roli** oblasti pro výběr **přidat přiřazení** podokně vyberte roli, kterou chcete přiřadit ke zvoleným skupinám, které jste vybrali.

15. Klikněte na tlačítko **přiřadit** tlačítko a přiřazení aplikace k vybraným skupinám.

Po krátké době uživatele, které jste vybrali dát spustit tyto aplikace na přístupovém panelu.

### <a name="check-if-a-user-is-part-of-group-assigned-to-a-license"></a>Zkontrolujte, jestli uživatel je součástí skupiny přiřazené k licenci

1.  Otevřít [ **webu Azure portal** ](https://portal.azure.com/) a přihlaste se jako **globálního správce.**

2.  Otevřít **rozšíření Azure Active Directory** kliknutím **všechny služby** v horní části hlavní navigační nabídce vlevo.

3.  Zadejte **"Azure Active Directory**" do vyhledávacího pole filtrovat a vybrat **Azure Active Directory** položky.

4.  Klikněte na tlačítko **uživatelů a skupin** v navigační nabídce.

5.  Klikněte na tlačítko **všichni uživatelé**.

6.  **Hledání** pro uživatele, které vás zajímají a **klikněte na odpovídající řádek** k výběru.

7.  Klikněte na tlačítko **skupiny**.

8.  Klikněte na řádek konkrétní skupinu.

9.  Klikněte na tlačítko **licence** zobrazíte skupině licencí, které je přiřazeno k němu.

   * Pokud skupině je přiřazená licence k Office tomu některé aplikace Office první strany zobrazovat na přístupovém panelu uživatele.

### <a name="how-to-assign-a-license-to-a-group"></a>Přiřazení licence ke skupině

Chcete-li přiřadit licenci ke skupině, použijte následující postup:

1.  Otevřít [ **webu Azure portal** ](https://portal.azure.com/) a přihlaste se jako **globálního správce.**

2.  Otevřít **rozšíření Azure Active Directory** kliknutím **všechny služby** v horní části hlavní navigační nabídce vlevo.

3.  Zadejte **"Azure Active Directory**" do vyhledávacího pole filtrovat a vybrat **Azure Active Directory** položky.

4.  Klikněte na tlačítko **uživatelů a skupin** v navigační nabídce.

5.  Klikněte na tlačítko **všechny skupiny**.

6.  **Hledání** pro skupiny, které vás zajímají a **klikněte na odpovídající řádek** k výběru.

7.  Klikněte na tlačítko **licence** zobrazíte, které aktuálně licence skupině přiřadila.

8.  Klikněte na tlačítko **přiřadit** tlačítko.

9.  Vyberte **jeden nebo více produktů** ze seznamu dostupných produktů.

10. **Volitelné** klikněte na tlačítko **možnosti přiřazení** položku, kterou chcete zásady přiřazovat produktů. Klikněte na tlačítko **Ok** po dokončení.

11. Klikněte na tlačítko **přiřadit** tlačítko tyto licence přiřadit k této skupině. To může trvat dlouhou dobu, v závislosti na velikosti a složitosti skupiny.

>[!NOTE]
>Provedete to tak rychlejší, vezměte v úvahu dočasně přiřazování licencí pro uživatele přímo. 
>
>

## <a name="next-steps"></a>Další postup
[Přidání nových uživatelů do služby Azure Active Directory](./../fundamentals/add-users-azure-active-directory.md)

