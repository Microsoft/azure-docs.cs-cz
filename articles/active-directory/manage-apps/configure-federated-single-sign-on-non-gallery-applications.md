---
title: Jak nakonfigurovat federované jednotné přihlašování pro aplikaci mimo Galerii | Dokumentace Microsoftu
description: Jak nakonfigurovat federované jednotné přihlašování pro vlastní aplikaci mimo galerii, kterou chcete integrovat s Azure AD
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
ms.collection: M365-identity-device-management
ms.openlocfilehash: f99fa8cbc211469185f2503718c84bc1b12db44c
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2019
ms.locfileid: "56178209"
---
# <a name="how-to-configure-federated-single-sign-on-for-a-non-gallery-application"></a>Jak nakonfigurovat federované jednotné přihlašování pro aplikaci mimo Galerii

Konfigurace jednotného přihlašování pro aplikaci mimo galerii *bez psaní kódu*, musíte mít předplatné nebo Azure AD Premium a aplikace musí podporovat protokol SAML 2.0. Další informace o verzích služby Azure AD, najdete [ceny služby Azure AD](https://azure.microsoft.com/pricing/details/active-directory/).

## <a name="overview-of-steps-required"></a>Přehled kroků potřebných
Tady je přehled kroky potřebnými ke konfiguraci federované jednotné přihlašování pomocí SAML 2.0 pro aplikaci mimo Galerii (například vlastní).

-   [Nakonfigurujte hodnoty metadat aplikace ve službě Azure AD (přihlašování adresu URL, identifikátoru, adresa URL odpovědi)](#_Configuring_single_sign-on)

-   [Vyberte identifikátor uživatele a přidat atributy uživatele k odeslání do aplikace](#select-user-identifier-and-add-user-attributes-to-be-sent-to-the-application)

-   [Načíst metadata služby Azure AD a certifikátů](#download-the-azure-ad-metadata-or-certificate)

-   [Nakonfigurujte hodnoty metadat služby Azure AD v aplikaci (přihlašování adresu URL, Vystavitel, odhlašovací adresa URL a certifikát)](#_Configuring_single_sign-on)

-   Přiřazení uživatelů k aplikaci

## <a name="configuring-single-sign-on-to-non-gallery-applications"></a>Konfigurace jednotného přihlašování pro aplikace mimo Galerii

Pokud chcete nakonfigurovat jednotné přihlašování pro aplikace, které nejsou v galerii Azure AD, postupujte podle následujících kroků:

1.  Otevřít [ **webu Azure portal** ](https://portal.azure.com/) a přihlaste se jako **globálního správce** nebo **Spolusprávce.**

2.  Otevřít **rozšíření Azure Active Directory** kliknutím **všechny služby** v horní části hlavní navigační nabídce vlevo.

3.  Zadejte **"Azure Active Directory**" do vyhledávacího pole filtrovat a vybrat **Azure Active Directory** položky.

4.  Klikněte na tlačítko **podnikové aplikace** levé navigační nabídce Azure Active Directory.

5.  Klikněte na tlačítko **přidat** tlačítko v pravém horním rohu **podnikové aplikace** podokně.

6.  Klikněte na tlačítko **aplikace mimo galerii** v **přidat vlastní aplikaci** oddílu

7.  Zadejte název aplikace **název** textového pole.

8.  Klikněte na tlačítko **přidat** tlačítko pro přidání aplikace.

9.  Po načtení aplikace, klikněte na tlačítko **jednotného přihlašování** levé navigační nabídce aplikace.

10. Vyberte **přihlašování na základě SAML** v **režimu** rozevíracího seznamu.

11. Zadejte požadované hodnoty v **domény a adresy URL.** Tyto hodnoty byste měli získat od dodavatele aplikace.

   1. Pokud chcete nakonfigurovat aplikace jako jednotné přihlašování zahájené pomocí IdP, zadejte příslušnou odpovědní adresu URL a identifikátoru.

   2. **Volitelné:** Konfigurace aplikace jako iniciovaného Zprostředkovatelem přihlašování jednotné přihlašování – adresa URL je povinná hodnota.

12. V **atributy uživatele**, vyberte jedinečný identifikátor pro vaši uživatelé v **identifikátor uživatele** rozevíracího seznamu.

13. **Volitelné:** klikněte na tlačítko **zobrazit a upravit všechny ostatní atributy uživatele** upravit atributy, které se odešlou na aplikaci v tokenu SAML, při přihlášení uživatele.

   Chcete-li přidat atribut:

   1. Klikněte na tlačítko **přidat atribut**. Zadejte **název** a vyberte položku **hodnota** z rozevíracího seznamu.

   2. Klikněte na tlačítko **uložit.** V tabulce se zobrazí nový atribut.

14. Klikněte na tlačítko **konfigurovat &lt;název_aplikace&gt;**  pro přístup k dokumentaci o tom, jak nakonfigurovat jednotné přihlašování v aplikaci. Kromě toho má adresy URL Azure AD a certifikát nutný pro aplikace.

15. [Přiřazení uživatelů k aplikaci.](#assign-users-to-the-application)

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

   2. Klikněte na tlačítko **uložit.** V tabulce se zobrazí nový atribut.

## <a name="download-the-azure-ad-metadata-or-certificate"></a>Stáhnout Azure AD metadata nebo certifikát

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

Azure AD také poskytuje adresu URL získat metadata. Postupujte podle tohoto vzoru získat adresu URL metadat specifické pro aplikace: https://login.microsoftonline.com/ <Directory ID>/federationmetadata/2007-06/federationmetadata.xml?appid=<Application ID>.

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
