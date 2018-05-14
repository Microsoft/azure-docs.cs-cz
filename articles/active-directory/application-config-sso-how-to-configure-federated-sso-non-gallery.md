---
title: Postup konfigurace federované jednotné přihlašování pro aplikace bez Galerie | Microsoft Docs
description: Postup konfigurace federované jednotné přihlašování pro vlastní aplikace bez galerie, která chcete integrovat se službou Azure AD
services: active-directory
documentationcenter: ''
author: ajamess
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: asteen
ms.openlocfilehash: 65df07b6fca62476953ad98dc9e17d9a82c97a53
ms.sourcegitcommit: c52123364e2ba086722bc860f2972642115316ef
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/11/2018
---
# <a name="how-to-configure-federated-single-sign-on-for-a-non-gallery-application"></a>Postup konfigurace federované jednotné přihlašování pro aplikace bez Galerie

Konfigurace jednotného přihlašování pro aplikace bez Galerie *bez nutnosti psaní kódu*, musíte mít předplatné nebo Azure AD Premium a aplikace musí podporovat SAML 2.0. Další informace o verzích služby Azure AD, najdete v článku [ceny služby Azure AD](https://azure.microsoft.com/pricing/details/active-directory/).

## <a name="overview-of-steps-required"></a>Přehled kroků potřebných
Níže je podrobný přehled kroků nezbytných k nakonfigurovat federované jednotné přihlašování SAML 2.0 pro aplikaci (například vlastní) bez galerie.

-   [Nakonfigurujte hodnoty metadata aplikace ve službě Azure AD (přihlašování adresu URL, identifikátor, adresa URL odpovědi)](#_Configuring_single_sign-on)

-   [Vyberte uživatelský identifikátor a přidejte atributy uživatele k odeslání do aplikace](#select-user-identifier-and-add-user-attributes-to-be-sent-to-the-application)

-   [Načtení metadat Azure AD a certifikátů](#download-the-azure-ad-metadata-or-certificate)

-   [Konfigurovat Azure AD metadata hodnoty v aplikaci (přihlašování adresu URL, vystavitele, adresy URL odhlašovací a certifikát)](#_Configuring_single_sign-on)

-   [Přiřazení uživatelů k aplikaci](#_Assign_users_to_the_application)

## <a name="configuring-single-sign-on-to-non-gallery-applications"></a>Konfigurace jednotného přihlašování k aplikacím jiných Galerie

Pokud chcete konfigurovat jednotné přihlašování pro aplikace, která není v galerii Azure AD, postupujte podle následujících kroků:

1.  Otevřete [ **portál Azure** ](https://portal.azure.com/) a přihlaste se jako **globálního správce** nebo **ko-správce.**

2.  Otevřete **rozšíření Azure Active Directory** kliknutím **všechny služby** v horní části hlavní levé navigační nabídce.

3.  Zadejte **"Azure Active Directory**" v filtru vyhledávacího pole a vyberte **Azure Active Directory** položky.

4.  Klikněte na tlačítko **podnikové aplikace, které** z levé navigační nabídce Azure Active Directory.

5.  klikněte **přidat** v pravém horním rohu na tlačítko **podnikové aplikace, které** podokně.

6.  Klikněte na tlačítko **aplikace bez Galerie** v **přidat vlastní aplikaci** části

7.  Zadejte název aplikace **název** textové pole.

8.  Klikněte na tlačítko **přidat** tlačítko Přidat aplikaci.

9.  Po načtení aplikace, klikněte na **jednotného přihlašování** z levé navigační nabídky.

10. Vyberte **na základě SAML přihlašování** v **režimu** rozevíracího seznamu.

11. Zadejte požadované hodnoty v **domény a adresy URL.** Tyto hodnoty by měly získat od dodavatele aplikace.

   1. Chcete-li nakonfigurovat aplikaci jako jednotné přihlašování iniciované deklarací identity, zadejte adresa URL odpovědi a identifikátor.

   2. **Volitelné:** jako adresa URL přihlašování iniciované SP SSO konfigurace aplikace je požadovaná hodnota.

12. V **uživatelské atributy**, vyberte jedinečný identifikátor pro uživatele v **uživatelský identifikátor** rozevíracího seznamu.

13. **Volitelné:** klikněte na tlačítko **zobrazit a upravit všechny ostatní atributy uživatele** upravit atributy zasílat na aplikaci v tokenu SAML, když se uživatelé přihlašují.

   Chcete-li přidat atribut:

   1. Klikněte na tlačítko **přidat atribut**. Zadejte **název** a vyberte položku **hodnotu** z rozevíracího seznamu.

   2. Klikněte na tlačítko **uložit.** Zobrazí nový atribut v tabulce.

14. Klikněte na tlačítko **konfigurace &lt;název aplikace&gt;**  dokumentace přístupu na tom, jak nakonfigurovat jednotné přihlašování v aplikaci. Navíc má Azure AD adresy URL a certifikát nutný pro aplikaci.

15. [Přiřazení uživatelů k aplikaci.](#assign-users-to-the-application)

## <a name="select-user-identifier-and-add-user-attributes-to-be-sent-to-the-application"></a>Vyberte uživatelský identifikátor a přidejte atributy uživatele k odeslání do aplikace

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

 >[! Poznámka} Azure AD vyberte formát požadovanou aplikaci v SAML AuthRequest nebo formát pro atribut NameID (identifikátor uživatele) na základě vybrané hodnoty. Další informace najdete v článku [protokolu SAML přihlašování](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-on-protocol-reference#authnrequest) části NameIDPolicy.
 >
 >

9.  Chcete-li přidat atributy uživatele, klikněte na tlačítko **zobrazení a upravovat všechny ostatní atributy uživatele** upravit atributy zasílat na aplikaci v tokenu SAML, když se uživatelé přihlašují.

   Chcete-li přidat atribut:

   1. Klikněte na tlačítko **přidat atribut**. Zadejte **název** a vyberte položku **hodnotu** z rozevíracího seznamu.

   2. Klikněte na tlačítko **uložit.** Zobrazí nový atribut v tabulce.

## <a name="download-the-azure-ad-metadata-or-certificate"></a>Stáhnout Azure AD metadata nebo certifikát

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

## <a name="assign-users-to-the-application"></a>Přiřazení uživatelů k aplikaci

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

Po krátké době uživatele, které jste vybrali moci spustit tyto aplikace pomocí metody popsané v části popis řešení.

## <a name="customizing-the-saml-claims-sent-to-an-application"></a>Přizpůsobují se deklarace identity SAML, odeslané do aplikace

Naučte se přizpůsobovat deklarací identity atributu SAML odeslaných do vaší aplikace, najdete v tématu [deklarací mapování v Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-claims-mapping) Další informace.

## <a name="next-steps"></a>Další postup
[Zadejte jednotné přihlašování pro vaše aplikace s Proxy aplikace](manage-apps/application-proxy-configure-single-sign-on-with-kcd.md)
