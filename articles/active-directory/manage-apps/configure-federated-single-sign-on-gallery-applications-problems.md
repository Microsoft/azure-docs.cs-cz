---
title: Problémy s konfigurací federovaného přiřazování služeb pro aplikace Azure AD Gallery
description: Řešení některých běžných problémů, se kterými se můžete setkat při konfiguraci federovaného jednotného přihlášení pomocí saml pro aplikace, které jsou uvedeny v Galerii aplikací Azure AD
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
ms.openlocfilehash: 87c2497a781b0d46b3b2f1e281a3d7b327b60952
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74274642"
---
# <a name="problem-configuring-federated-single-sign-on-for-an-azure-ad-gallery-application"></a>Problém s konfigurací federovaného jednotného přihlašování pro aplikaci Azure AD Gallery

Pokud narazíte na problém při konfiguraci aplikace. Ověřte, zda jste postupovali podle všech kroků v kurzu pro aplikaci. V konfiguraci aplikace máte vložkovou dokumentaci o konfiguraci aplikace. Můžete také získat přístup k [seznamu kurzů o tom, jak integrovat aplikace SaaS s Azure Active Directory](https://azure.microsoft.com/documentation/articles/active-directory-saas-tutorial-list/) pro podrobné pokyny krok za krokem.

## <a name="cant-add-another-instance-of-the-application"></a>Nelze přidat další instanci aplikace

Chcete-li přidat druhou instanci aplikace, musíte být schopni:

-   Nakonfigurujte jedinečný identifikátor pro druhou instanci. Nebudete moci nakonfigurovat stejný identifikátor, který byl použit pro první instanci.

-   Nakonfigurujte jiný certifikát, než který byl použit pro první instanci.

Pokud aplikace nepodporuje žádné z výše uvedených skutečností. Potom nebudete moci nakonfigurovat druhou instanci.

## <a name="cant-add-the-identifier-or-the-reply-url"></a>Nelze přidat identifikátor nebo adresu URL odpovědi

Pokud se vám nepodařilo nakonfigurovat identifikátor nebo adresu URL pro odpověď, potvrďte, že hodnoty adresy URL identifikátoru a odpovědi odpovídají vzorům předem nakonfigurovaným pro aplikaci.

Chcete-li znát vzory předem nakonfigurované pro aplikaci:

1. Otevřete [**portál Azure**](https://portal.azure.com/) a přihlaste se jako globální **správce** nebo **spolusprávce.** Přejděte ke kroku 7. Pokud jste již v okně konfigurace aplikace na Azure AD.

2. Otevřete **rozšíření Azure Active Directory** kliknutím na Všechny **služby** v horní části hlavní nabídky navigace vlevo.

3. Do vyhledávacího pole filtru zadejte **"Azure Active Directory"** a vyberte položku **Služby Azure Active Directory.**

4. V levé navigační nabídce Azure Active Directory klikněte na **Podnikové aplikace.**

5. Kliknutím na **Všechny aplikace** zobrazíte seznam všech aplikací.

   * Pokud zde nevidíte aplikaci, kterou chcete zobrazit, použijte ovládací prvek **Filtr** v horní části **seznamu Všechny aplikace** a nastavte možnost **Zobrazit** na **všechny aplikace.**

6. Vyberte aplikaci, kterou chcete nakonfigurovat jednotné přihlašování.

7. Po načtení aplikace klikněte **na jednotné přihlašování** z levé navigační nabídky aplikace.

8. V rozevíracím souboru **Režim** vyberte **přihlašování založené na SAML.**

9. Přejděte do textového pole **Identifikátor** nebo **Odpověď url** v **části Doména a adresy URL.**

10. Existují tři způsoby, jak znát podporované vzory pro aplikaci:

    * V textovém poli se podporované vzorky zobrazují jako zástupný *příklad:* <https://contoso.com>.

    * Pokud vzorek není podporován, zobrazí se při pokusu o zadání hodnoty do textového pole červený vykřičník. Pokud najedete myší na červený vykřičník, zobrazí se podporované vzorky.

    * V kurzu pro aplikaci můžete také získat informace o podporovaných vzorcích. V části **Konfigurace jednotného přihlašování Azure AD.** Přejděte ke kroku pro konfiguraci hodnot v části **Doména a adresy URL.**

Pokud hodnoty neodpovídají vzory předem nakonfigurované ve službě Azure AD. Můžete:

-   Spolupracujte s dodavatelem aplikace a získejte hodnoty, které odpovídají vzoru předem nakonfigurovanému ve službě Azure AD.

-   Nebo můžete kontaktovat tým Azure <aadapprequest@microsoft.com> AD na nebo zanechat komentář v kurzu požádat o aktualizaci podporovaných vzorů pro aplikaci

## <a name="where-do-i-set-the-entityid-user-identifier-format"></a>Kde mám nastavit formát EntityID (Identifikátor uživatele)

Nebudete moct vybrat formát EntityID (Identifikátor uživatele), který Azure AD odešle do aplikace v odpovědi po ověření uživatele.

Azure AD vybrat formát pro NameID atribut (Identifikátor uživatele) na základě vybrané hodnoty nebo formát požadovaný aplikací v SAML AuthRequest. Další informace naleznete v článku [Protokol SAML jednotného přihlášení](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-on-protocol-reference#authnrequest) v části NameIDPolicy,

## <a name="cant-find-the-azure-ad-metadata-to-complete-the-configuration-with-the-application"></a>Nelze najít metadata Azure AD k dokončení konfigurace s aplikací

Pokud chcete stáhnout metadata nebo certifikát aplikace z Azure AD, postupujte takto:

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

## <a name="dont-know-how-to-customize-saml-claims-sent-to-an-application"></a>Nevíte, jak přizpůsobit deklarace SAML odeslané do aplikace

Další informace najdete v tématu [Mapování deklarací identity ve službě Azure Active Directory.](https://docs.microsoft.com/azure/active-directory/active-directory-claims-mapping)

## <a name="next-steps"></a>Další kroky
[Správa aplikací pomocí služby Azure Active Directory](what-is-application-management.md)
