---
title: Jak nakonfigurovat federované jednotné přihlašování pro aplikaci mimo galerii
description: Jak nakonfigurovat federované jednotné přihlašování pro vlastní aplikaci bez galerie, kterou chcete integrovat s Azure AD
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
ms.openlocfilehash: c1d49ec5ef80b284aa6b1a305b037d19dae34870
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74274597"
---
# <a name="how-to-configure-federated-single-sign-on-for-a-non-gallery-application"></a>Konfigurace federovaného jednotného přihlášení pro aplikaci mimo galerii

Chcete-li nakonfigurovat jednotné přihlašování pro aplikaci bez galerie *bez psaní kódu*, musíte mít předplatné nebo Azure AD Premium a aplikace musí podporovat SAML 2.0. Další informace o verzích Azure AD najdete na webu [stanovení cen Azure AD](https://azure.microsoft.com/pricing/details/active-directory/).

## <a name="overview-of-steps-required"></a>Přehled požadovaných kroků
Níže je uveden přehled na vysoké úrovni kroků potřebných ke konfiguraci federovaného jednotného přihlášení pomocí saml 2.0 pro aplikaci, která není součástí galerie (např. vlastní).

-   Konfigurace hodnot metadat aplikace ve službě Azure AD (přihlašování url, identifikátor, adresa URL pro odpověď)

-   [Vyberte identifikátor uživatele a přidejte atributy uživatele, které mají být odeslány do aplikace.](#select-user-identifier-and-add-user-attributes-to-be-sent-to-the-application)

-   [Načtení metadat azure a certifikátu služby Azure AD](#download-the-azure-ad-metadata-or-certificate)

-   Konfigurace hodnot metadat Azure AD v aplikaci (Adresa URL přihlášení, Vydavatel, Adresa URL odhlášení a certifikát)

-   Přiřazení uživatelů k aplikaci

## <a name="configuring-single-sign-on-to-non-gallery-applications"></a>Konfigurace jednotného přihlašování k aplikacím, které nejsou součástí galerie

Pokud chcete nakonfigurovat jednotné přihlašování pro aplikaci, která není v galerii Azure AD, postupujte podle následujících kroků:

1. Otevřete [**portál Azure**](https://portal.azure.com/) a přihlaste se jako globální **správce** nebo **spolusprávce.**

2. Otevřete **rozšíření Azure Active Directory** kliknutím na Všechny **služby** v horní části hlavní nabídky navigace vlevo.

3. Do vyhledávacího pole filtru zadejte **"Azure Active Directory"** a vyberte položku **Služby Azure Active Directory.**

4. V levé navigační nabídce Azure Active Directory klikněte na **Podnikové aplikace.**

5. klikněte na tlačítko **Přidat** v pravém horním rohu v podokně **Podnikové aplikace.**

6. Klikněte na **Aplikaci mimo galerii** v části **Přidat vlastní aplikaci.**

7. Do textového pole **Název** zadejte název aplikace.

8. Kliknutím na **tlačítko Přidat** přidáte aplikaci.

9. Po načtení aplikace klikněte **na jednotné přihlašování** z levé navigační nabídky aplikace.

10. V rozevíracím **souboru Režim** vyberte **přihlašování založené na SAML.**

11. Zadejte požadované hodnoty do **polí Domény a adresy URL.** Tyto hodnoty byste měli získat od dodavatele aplikace.

    1. Chcete-li nakonfigurovat aplikaci jako službu Zabezpečení iniciované protokolem IdP, zadejte adresu URL odpovědi a identifikátor.

    2. **Nepovinné:** Chcete-li nakonfigurovat aplikaci jako sp-inicioval sso, přihlašovací url je požadovaná hodnota.

12. V **atributech Uživatele**vyberte jedinečný identifikátor pro uživatele v rozevíracím souboru **Identifikátor uživatele.**

13. **Volitelné:** klikněte na **Zobrazit a upravte všechny ostatní atributy uživatele** a upravte atributy, které mají být odeslány do aplikace v tokenu SAML, když se uživatelé přihlásí.

    Přidání atributu:

    1. klepněte na **tlačítko Přidat atribut**. Zadejte **název** a vrozenou **hodnotu** z rozevíracího souboru.

    2. Klikněte na **Uložit.** V tabulce se zobrazí nový atribut.

14. Klikněte na **Konfigurovat &lt;název&gt; aplikace** pro přístup k dokumentaci o konfiguraci jednotného přihlášení v aplikaci. Také máte adresy URL Azure AD a certifikát požadovaný pro aplikaci.

15. [Přiřaďte uživatelé k aplikaci.](#assign-users-to-the-application)

## <a name="select-user-identifier-and-add-user-attributes-to-be-sent-to-the-application"></a>Vyberte identifikátor uživatele a přidejte atributy uživatele, které mají být odeslány do aplikace.

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

## <a name="download-the-azure-ad-metadata-or-certificate"></a>Stažení metadat nebo certifikátu Azure AD

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

Azure AD také poskytuje adresu URL získat metadata. Podle tohoto vzoru získáte adresu URL metadat `https://login.microsoftonline.com/<Directory ID>/federationmetadata/2007-06/federationmetadata.xml?appid=<Application ID>`specifickou pro aplikaci: .

## <a name="assign-users-to-the-application"></a>Přiřazení uživatelů k aplikaci

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

Po krátké době budou moci vybraní uživatelé spustit tyto aplikace pomocí metod popsaných v části popis řešení.

## <a name="customizing-the-saml-claims-sent-to-an-application"></a>Přizpůsobení deklarací SAML odeslaných do aplikace

Další informace najdete v tématu [Mapování deklarací identity ve službě Azure Active Directory.](https://docs.microsoft.com/azure/active-directory/active-directory-claims-mapping)

## <a name="next-steps"></a>Další kroky
[Poskytněte svým aplikacím jednotné přihlašování pomocí proxy aplikace](application-proxy-configure-single-sign-on-with-kcd.md)
