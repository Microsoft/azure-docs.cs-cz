---
title: Jak nakonfigurovat federované jednotné přihlašování pro aplikaci Galerie Azure AD | Microsoft Docs
description: Jak nakonfigurovat federované jednotné přihlašování pro existující aplikaci Galerie Azure AD a použít kurzy k rychlému zprovoznění
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
ms.collection: M365-identity-device-management
ROBOTS: NOINDEX
ms.openlocfilehash: bb5d2c693047dd0aa53430ba531dfd246cc77be9
ms.sourcegitcommit: 198c3a585dd2d6f6809a1a25b9a732c0ad4a704f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/23/2019
ms.locfileid: "68422528"
---
# <a name="how-to-configure-federated-single-sign-on-for-an-azure-ad-gallery-application"></a>Jak nakonfigurovat federované jednotné přihlašování pro aplikaci Galerie Azure AD

Všechny aplikace v galerii Azure AD povolené s možností podnikového jednotného přihlašování mají k dispozici podrobný kurz. Můžete získat přístup k [seznamu kurzů, jak integrovat aplikace SaaS s Azure Active Directory](https://azure.microsoft.com/documentation/articles/active-directory-saas-tutorial-list/) pro podrobné podrobné pokyny.

## <a name="overview-of-steps-required"></a>Přehled požadovaných kroků
Ke konfiguraci aplikace z Galerie Azure AD potřebujete:

-   [Přidání aplikace z Galerie Azure AD](#add-an-application-from-the-azure-ad-gallery)

-   [Konfigurace hodnot metadat aplikace v Azure AD (přihlašovací adresa URL, identifikátor, adresa URL odpovědi)](#configure-single-sign-on-for-an-application-from-the-azure-ad-gallery)

-   [Vyberte identifikátor uživatele a přidejte atributy uživatele, které se mají odeslat do aplikace.](#select-user-identifier-and-add-user-attributes-to-be-sent-to-the-application)

-   [Načtení metadat a certifikátů Azure AD](#download-the-azure-ad-metadata-or-certificate)

-   [Konfigurace hodnot metadat Azure AD v aplikaci (přihlašovací adresa URL, vydavatel, adresa URL pro odhlášení a certifikát)](#configure-single-sign-on-for-an-application-from-the-azure-ad-gallery)

-   [Přiřazení uživatelů k aplikaci](#assign-users-to-the-application)

## <a name="add-an-application-from-the-azure-ad-gallery"></a>Přidání aplikace z Galerie Azure AD

Pokud chcete přidat aplikaci z Galerie Azure AD, postupujte podle následujících kroků:

1.  Otevřete [Azure Portal](https://portal.azure.com) a přihlaste se jako **globální správce** nebo **spolusprávce** .

2.  Otevřete **rozšíření Azure Active Directory** kliknutím na **všechny služby** v horní části hlavní navigační nabídky vlevo.

3.  Zadejte **"Azure Active Directory**" do vyhledávacího pole filtrovat a vybrat **Azure Active Directory** položky.

4.  v navigační nabídce Azure Active Directory vlevo klikněte na **podnikové aplikace** .

5.  klikněte na tlačítko **Přidat** v pravém horním rohu podokna **podnikové aplikace** .

6.  Do textového pole **Zadejte název** z části **Přidat z Galerie** zadejte název aplikace.

7.  Vyberte aplikaci, kterou chcete nakonfigurovat pro jednotné přihlašování.

8.  Před přidáním aplikace můžete změnit její název z textového pole **název** .

9.  Kliknutím na tlačítko **Přidat** přidejte aplikaci.

Po krátké době je možné zobrazit podokno konfigurace aplikace.

## <a name="configure-single-sign-on-for-an-application-from-the-azure-ad-gallery"></a>Konfigurace jednotného přihlašování pro aplikaci z Galerie Azure AD

Chcete-li nakonfigurovat jednotné přihlašování pro aplikaci, postupujte podle následujících kroků:

1. Otevřete [**Azure Portal**](https://portal.azure.com/) a přihlaste se jako **globální správce** nebo **spolusprávce**.

2. Otevřete **rozšíření Azure Active Directory** kliknutím na **všechny služby** v horní části hlavní navigační nabídky vlevo.

3. Zadejte **"Azure Active Directory**" do vyhledávacího pole filtrovat a vybrat **Azure Active Directory** položky.

4. v navigační nabídce Azure Active Directory vlevo klikněte na **podnikové aplikace** .

5. Klikněte na tlačítko **všechny aplikace** zobrazíte seznam všech aplikací.

   * Pokud nevidíte aplikaci, kterou má zobrazit tady, použijte **filtr** ovládacího prvku v horní části **seznam všech aplikací** a nastavit **zobrazit** umožňuje **všechny Aplikace.**

6. Vyberte aplikaci, pro kterou chcete nakonfigurovat jednotné přihlašování.

7. Po načtení aplikace klikněte na tlačítko **jednotného přihlašování** z nabídky navigace na levé straně aplikace.

8. V rozevíracím seznamu **režim** vyberte možnost přihlašování **založené na SAML** .

9. Zadejte požadované hodnoty do pole **doména a adresy URL.** Tyto hodnoty byste měli získat od dodavatele aplikace.

   1. Pokud chcete nakonfigurovat aplikaci jako jednotné přihlašování, přihlašovací adresa URL je požadovaná hodnota. U některých aplikací je identifikátor také povinná hodnota.

   2. Pokud chcete nakonfigurovat aplikaci jako IdP jednotného přihlašování, adresa URL odpovědi je požadovaná hodnota. U některých aplikací je identifikátor také povinná hodnota.

10. **Volitelné:** Pokud chcete zobrazit nepotřebné hodnoty, klikněte na **Zobrazit upřesňující nastavení adresy URL** .

11. V části **atributy uživatele**vyberte jedinečný identifikátor pro uživatele v rozevíracím seznamu **identifikátor uživatele** .

12. **Volitelné:** kliknutím na **Zobrazit a upravit všechny ostatní atributy uživatele** upravíte atributy, které se mají odeslat do aplikace v tokenu SAML, když se uživatelé přihlásí.

    Přidání atributu:
   
    1. klikněte na **Přidat atribut**. Zadejte **název** a vyberte **hodnotu** z rozevíracího seznamu.

    1. Klikněte na **Uložit.** V tabulce se zobrazí nový atribut.

13. Kliknutím **na &lt;konfigurovat název&gt; aplikace** získáte přístup k dokumentaci týkající se konfigurace jednotného přihlašování v aplikaci. K nastavení jednotného přihlašování v aplikaci máte také k dispozici adresy URL a certifikát metadat.

14. Kliknutím na **Uložit** uložte konfiguraci.

15. Přiřaďte uživatele k aplikaci.

## <a name="select-user-identifier-and-add-user-attributes-to-be-sent-to-the-application"></a>Vyberte identifikátor uživatele a přidejte atributy uživatele, které se mají odeslat do aplikace.

Chcete-li vybrat identifikátor uživatele nebo přidat atributy uživatele, postupujte podle následujících kroků:

1. Otevřete [**Azure Portal**](https://portal.azure.com/) a přihlaste se jako **globální správce** nebo **spolusprávce.**

2. Otevřete **rozšíření Azure Active Directory** kliknutím na **všechny služby** v horní části hlavní navigační nabídky vlevo.

3. Zadejte **"Azure Active Directory**" do vyhledávacího pole filtrovat a vybrat **Azure Active Directory** položky.

4. v navigační nabídce Azure Active Directory vlevo klikněte na **podnikové aplikace** .

5. Klikněte na tlačítko **všechny aplikace** zobrazíte seznam všech aplikací.

   * Pokud nevidíte aplikaci, kterou má zobrazit tady, použijte **filtr** ovládacího prvku v horní části **seznam všech aplikací** a nastavit **zobrazit** umožňuje **všechny Aplikace.**

6. Vyberte aplikaci, u které jste nakonfigurovali jednotné přihlašování.

7. Po načtení aplikace klikněte na tlačítko **jednotného přihlašování** z nabídky navigace na levé straně aplikace.

8. V části **atributy uživatele** vyberte jedinečný identifikátor pro uživatele v rozevíracím seznamu **identifikátor uživatele** . Aby bylo možné uživatele ověřit, musí vybraná možnost odpovídat očekávané hodnotě v aplikaci.

   >[!NOTE] 
   >Azure AD vyberte formát pro atribut NameID (identifikátor uživatele) na základě vybrané hodnoty nebo formátu, který požaduje aplikace v AuthRequest SAML. Další informace najdete v článku [protokol SAML jednotného přihlašování](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-on-protocol-reference#authnrequest) v části NameIDPolicy.
   >
   >

9. Pokud chcete přidat atributy uživatele, klikněte na **Zobrazit a upravit všechny ostatní atributy uživatele** a upravte atributy, které se mají odeslat do aplikace v tokenu SAML, když se uživatelé přihlásí.

   Přidání atributu:
  
   1. klikněte na **Přidat atribut**. Zadejte **název** a vyberte **hodnotu** z rozevíracího seznamu.

   2. Klikněte na **Uložit**. V tabulce se zobrazí nový atribut.

## <a name="download-the-azure-ad-metadata-or-certificate"></a>Stažení metadat nebo certifikátů Azure AD

Pokud chcete stáhnout metadata aplikace nebo certifikát z Azure AD, postupujte podle následujících kroků:

1. Otevřete [**Azure Portal**](https://portal.azure.com/) a přihlaste se jako **globální správce** nebo **spolusprávce.**

2. Otevřete **rozšíření Azure Active Directory** kliknutím na **všechny služby** v horní části hlavní navigační nabídky vlevo.

3. Zadejte **"Azure Active Directory**" do vyhledávacího pole filtrovat a vybrat **Azure Active Directory** položky.

4. v navigační nabídce Azure Active Directory vlevo klikněte na **podnikové aplikace** .

5. Klikněte na tlačítko **všechny aplikace** zobrazíte seznam všech aplikací.

   *  Pokud se vám nezobrazí aplikace, kterou chcete zobrazit, použijte ovládací prvek **filtru** v horní části **seznamu všechny aplikace** a nastavte možnost **Zobrazit** pro **všechny aplikace**.

6. Vyberte aplikaci, u které jste nakonfigurovali jednotné přihlašování.

7. Po načtení aplikace klikněte na tlačítko **jednotného přihlašování** z nabídky navigace na levé straně aplikace.

8. Přejděte do části **podpisový certifikát SAML** a pak klikněte na **Stáhnout** sloupec hodnota. V závislosti na tom, co aplikace vyžaduje konfiguraci jednotného přihlašování, se zobrazí buď možnost stáhnout metadata XML nebo certifikát.

Azure AD také poskytuje adresu URL pro získání metadat. Podle tohoto vzoru Získejte adresu URL metadat specifickou pro danou aplikaci:`https://login.microsoftonline.com/<Directory ID>/federationmetadata/2007-06/federationmetadata.xml?appid=<Application ID>`

## <a name="assign-users-to-the-application"></a>Přiřazení uživatelů k aplikaci

Jeden nebo více uživatelů přiřadit přímo k aplikaci, postupujte podle následujících kroků:

1. Otevřít [ **webu Azure portal** ](https://portal.azure.com/) a přihlaste se jako **globálního správce.**

2. Otevřete **rozšíření Azure Active Directory** kliknutím na **všechny služby** v horní části hlavní navigační nabídky vlevo.

3. Zadejte **"Azure Active Directory**" do vyhledávacího pole filtrovat a vybrat **Azure Active Directory** položky.

4. v navigační nabídce Azure Active Directory vlevo klikněte na **podnikové aplikace** .

5. Klikněte na tlačítko **všechny aplikace** zobrazíte seznam všech aplikací.

   * Pokud nevidíte aplikaci, kterou má zobrazit tady, použijte **filtr** ovládacího prvku v horní části **seznam všech aplikací** a nastavit **zobrazit** umožňuje **všechny Aplikace.**

6. Vyberte aplikaci, kterou chcete přiřadit uživatele ze seznamu.

7. Po načtení aplikace klikněte na **Uživatelé a skupiny** v nabídce navigace na levé straně aplikace.

8. Klikněte na tlačítko **přidat** tlačítko nahoře **uživatelů a skupin** seznamu a otevře **přidat přiřazení** podokně.

9. Klikněte na tlačítko **uživatelů a skupin** selektor z **přidat přiřazení** podokně.

10. Zadejte **celý název** nebo **e-mailová adresa** uživatele zájem o přiřazení do **hledat podle jména nebo e-mailové adresy** vyhledávacího pole.

11. Najeďte myší **uživatele** v seznamu zobrazíte **zaškrtávací políčko**. Klikněte na zaškrtávací políčko vedle profilové fotky uživatele nebo logo, které chcete přidat uživatele **vybrané** seznamu.

12. **Volitelné** Pokud chcete **Přidat více než jednoho uživatele**, zadejte do vyhledávacího pole **jméno nebo e** -mailová adresa další **jméno** nebo **e-mailovou adresu** a kliknutím na zaškrtávací políčko přidejte tohoto uživatele do **vybraného** seznamu.

13. Po dokončení výběru uživatelů, klikněte na tlačítko **vyberte** tlačítko pro přidání do seznamu uživatelů a skupin pro přiřazení k aplikaci.

14. **Volitelné:** klikněte na tlačítko **vybrat roli** oblasti pro výběr **přidat přiřazení** podokně vyberte roli, kterou chcete přiřadit uživatelům, které jste vybrali.

15. Klikněte na tlačítko **přiřadit** tlačítko přiřadit aplikaci do vybraného uživatele.

Po krátké době možné ke spouštění těchto aplikací pomocí metod popsaných v části popis řešení uživatele, které jste vybrali.

## <a name="customizing-the-saml-claims-sent-to-an-application"></a>Přizpůsobení deklarací SAML odeslaných do aplikace

Další informace o tom, jak přizpůsobit deklarace identity atributů SAML odeslané do vaší aplikace, najdete v tématu [mapování deklarací v Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-claims-mapping) .

## <a name="next-steps"></a>Další kroky
[Poskytovat jednotné přihlašování do aplikací pomocí Proxy aplikace](application-proxy-configure-single-sign-on-with-kcd.md)



