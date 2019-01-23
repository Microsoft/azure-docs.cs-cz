---
title: Jak nakonfigurovat federované jednotné přihlašování pro aplikaci Galerie Azure AD | Dokumentace Microsoftu
description: Jak nakonfigurovat federované jednotné přihlašování pro existující aplikaci Galerie Azure AD a použití kurzů k rychlé zahájení práce
services: active-directory
documentationcenter: ''
author: barbkess
manager: daveba
ms.assetid: ''
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/11/2017
ms.author: barbkess
ms.openlocfilehash: 00d6efccd5d74b4fb866d29780f3cbd3919f5c76
ms.sourcegitcommit: cf88cf2cbe94293b0542714a98833be001471c08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/23/2019
ms.locfileid: "54469334"
---
# <a name="how-to-configure-federated-single-sign-on-for-an-azure-ad-gallery-application"></a>Jak nakonfigurovat federované jednotné přihlašování pro aplikaci Galerie Azure AD

Všechny aplikace v galerii Azure AD povolit podnikové jednotné přihlašování – funkce obsahuje podrobný návod k dispozici. Můžete přistupovat [seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](https://azure.microsoft.com/documentation/articles/active-directory-saas-tutorial-list/) podrobný postup.

## <a name="overview-of-steps-required"></a>Přehled kroků potřebných
Konfigurace aplikace z Galerie Azure AD, které je potřeba:

-   [Přidat aplikaci z Galerie Azure AD](#add-an-application-from-the-azure-ad-gallery)

-   [Nakonfigurujte hodnoty metadat aplikace ve službě Azure AD (přihlašování adresu URL, identifikátoru, adresa URL odpovědi)](#configure-single-sign-on-for-an-application-from-the-azure-ad-gallery)

-   [Vyberte identifikátor uživatele a přidat atributy uživatele k odeslání do aplikace](#select-user-identifier-and-add-user-attributes-to-be-sent-to-the-application)

-   [Načíst metadata služby Azure AD a certifikátů](#download-the-azure-ad-metadata-or-certificate)

-   [Nakonfigurujte hodnoty metadat služby Azure AD v aplikaci (přihlašování adresu URL, Vystavitel, odhlašovací adresa URL a certifikát)](#configure-single-sign-on-for-an-application-from-the-azure-ad-gallery)

-   [Přiřazení uživatelů k aplikaci](#assign-users-to-the-application)

## <a name="add-an-application-from-the-azure-ad-gallery"></a>Přidat aplikaci z Galerie Azure AD

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

## <a name="configure-single-sign-on-for-an-application-from-the-azure-ad-gallery"></a>Konfigurace jednotného přihlašování pro aplikaci z Galerie Azure AD

Pokud chcete nakonfigurovat jednotné přihlašování pro aplikaci, použijte následující postup:

1.  Otevřít [ **webu Azure portal** ](https://portal.azure.com/) a přihlaste se jako **globálního správce** nebo **spolusprávce**.

2.  Otevřít **rozšíření Azure Active Directory** kliknutím **všechny služby** v horní části hlavní navigační nabídce vlevo.

3.  Zadejte **"Azure Active Directory**" do vyhledávacího pole filtrovat a vybrat **Azure Active Directory** položky.

4.  Klikněte na tlačítko **podnikové aplikace** levé navigační nabídce Azure Active Directory.

5.  Klikněte na tlačítko **všechny aplikace** zobrazíte seznam všech aplikací.

   * Pokud nevidíte aplikaci, kterou má zobrazit tady, použijte **filtr** ovládacího prvku v horní části **seznam všech aplikací** a nastavit **zobrazit** umožňuje **všechny Aplikace.**

6.  Vyberte aplikaci, kterou chcete nakonfigurovat jednotné přihlašování.

7.  Po načtení aplikace, klikněte na tlačítko **jednotného přihlašování** levé navigační nabídce aplikace.

8.  Vyberte **přihlašování na základě SAML** z **režimu** rozevíracího seznamu.

9.  Zadejte požadované hodnoty v **domény a adresy URL.** Tyto hodnoty byste měli získat od dodavatele aplikace.

   1. Konfigurace aplikace jako iniciovaného Zprostředkovatelem přihlašování jednotné přihlašování – adresa URL je povinná hodnota. Pro některé aplikace identifikátor je také požadovaná hodnota.

   2. Konfigurace aplikace jako jednotné přihlašování iniciované zprostředkovatele identity, adresa URL odpovědi je povinná hodnota. Pro některé aplikace identifikátor je také požadovaná hodnota.

10. **Volitelné:** klikněte na tlačítko **zobrazit pokročilé nastavení URL** Pokud chcete zobrazit nepovinné hodnoty.

11. V **atributy uživatele**, vyberte jedinečný identifikátor pro vaši uživatelé v **identifikátor uživatele** rozevíracího seznamu.

12. **Volitelné:** klikněte na tlačítko **zobrazit a upravit všechny ostatní atributy uživatele** upravit atributy, které se odešlou na aplikaci v tokenu SAML, při přihlášení uživatele.

  Chcete-li přidat atribut:
   
   1. Klikněte na tlačítko **přidat atribut**. Zadejte **název** a vyberte položku **hodnota** z rozevíracího seznamu.

   1. Klikněte na tlačítko **uložit.** V tabulce se zobrazí nový atribut.

13. Klikněte na tlačítko **konfigurovat &lt;název_aplikace&gt;**  pro přístup k dokumentaci o tom, jak nakonfigurovat jednotné přihlašování v aplikaci. Máte také adres URL metadat a certifikát nutný k nastavení jednotného přihlašování k aplikaci.

14. Klikněte na tlačítko **Uložit** uložte konfiguraci.

15. Přiřazení uživatelů k aplikaci.

## <a name="select-user-identifier-and-add-user-attributes-to-be-sent-to-the-application"></a>Vyberte identifikátor uživatele a přidat atributy uživatele k odeslání do aplikace

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
  
   1. Klikněte na tlačítko **přidat atribut**. Zadejte **název** a vyberte položku **hodnota** z rozevíracího seznamu.

   2. Klikněte na **Uložit**. V tabulce se zobrazí nový atribut.

## <a name="download-the-azure-ad-metadata-or-certificate"></a>Stáhnout Azure AD metadata nebo certifikát

Stáhnout metadata aplikace nebo certifikát z Azure AD, postupujte podle následujících kroků:

1.  Otevřít [ **webu Azure portal** ](https://portal.azure.com/) a přihlaste se jako **globálního správce** nebo **Spolusprávce.**

2.  Otevřít **rozšíření Azure Active Directory** kliknutím **všechny služby** v horní části hlavní navigační nabídce vlevo.

3.  Zadejte **"Azure Active Directory**" do vyhledávacího pole filtrovat a vybrat **Azure Active Directory** položky.

4.  Klikněte na tlačítko **podnikové aplikace** levé navigační nabídce Azure Active Directory.

5.  Klikněte na tlačítko **všechny aplikace** zobrazíte seznam všech aplikací.

  *  Pokud nevidíte aplikaci, kterou má zobrazit tady, použijte **filtr** ovládacího prvku v horní části **seznam všech aplikací** a nastavit **zobrazit** umožňuje **všechny Aplikace**.

6.  Vyberte aplikace, které jste nakonfigurovali jednotného přihlašování.

7.  Po načtení aplikace, klikněte na tlačítko **jednotného přihlašování** levé navigační nabídce aplikace.

8.  Přejděte na **podpisový certifikát SAML** části a pak klikněte na **Stáhnout** hodnota ve sloupci. V závislosti na tom, jaké aplikace vyžaduje konfiguraci jednotného přihlašování se zobrazí buď možnost stahovat kód XML metadat nebo certifikát.

Azure AD také poskytuje adresu URL získat metadata. Postupujte podle tohoto vzoru získat adresu URL metadat specifické pro aplikaci: `https://login.microsoftonline.com/<Directory ID>/federationmetadata/2007-06/federationmetadata.xml?appid=<Application ID>`

## <a name="assign-users-to-the-application"></a>Přiřazení uživatelů k aplikaci

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

Po krátké době možné ke spouštění těchto aplikací pomocí metod popsaných v části popis řešení uživatele, které jste vybrali.

## <a name="customizing-the-saml-claims-sent-to-an-application"></a>Přizpůsobování deklarací identity SAML, odesílá se do aplikace

Informace o přizpůsobení deklarací identity atributu SAML odeslán do vaší aplikace najdete v tématu [mapování deklarací v Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-claims-mapping) Další informace.

## <a name="next-steps"></a>Další postup
[Poskytovat jednotné přihlašování do aplikací pomocí Proxy aplikace](application-proxy-configure-single-sign-on-with-kcd.md)



