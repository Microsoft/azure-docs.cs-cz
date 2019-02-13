---
title: Potíže s konfigurací federované jednotné přihlašování pro aplikaci Galerie Azure AD | Dokumentace Microsoftu
description: Vyřešit některé z běžných problémů, kterým může dojít při konfiguraci federované jednotné přihlašování pro aplikace, které jsou uvedeny v galerii aplikací Azure AD pomocí SAML
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
ms.openlocfilehash: 5295f2d341ce104ae51776af5c4477304b1dacb1
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2019
ms.locfileid: "56187232"
---
# <a name="problem-configuring-federated-single-sign-on-for-an-azure-ad-gallery-application"></a>Potíže s konfigurací federované jednotné přihlašování pro aplikaci Galerie Azure AD

Pokud narazíte na problém při konfiguraci aplikace. Ověřte, že jste provedli všechny kroky v tomto kurzu pro aplikaci. V konfiguraci vaší aplikace máte vložená dokumentace o tom, jak nakonfigurovat aplikaci. Navíc dostanete [seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](https://azure.microsoft.com/documentation/articles/active-directory-saas-tutorial-list/) podrobností podrobné pokyny.

## <a name="cant-add-another-instance-of-the-application"></a>Nelze přidat další instanci aplikace

Přidání druhé instance aplikace, musíte být schopni:

-   Nakonfigurujte jedinečný identifikátor pro druhou instanci. Nebudete moct nakonfigurovat stejný identifikátor pro první instanci.

-   Nakonfigurujte jiným certifikátem, než jaký se používá pro první instanci.

Pokud aplikace nepodporuje některé z výše. Potom nebudete moci konfigurovat druhou instanci.

## <a name="cant-add-the-identifier-or-the-reply-url"></a>Nelze přidat identifikátor nebo adresa URL odpovědi

Pokud nejste schopni nakonfigurovat identifikátor nebo adresa URL odpovědi, potvrďte, že hodnoty identifikátor a adresa URL odpovědi odpovídající vzorům předem nakonfigurované pro aplikaci.

Tyto vzory se dají vědět předem nakonfigurované pro aplikaci:

1.  Otevřít [ **webu Azure portal** ](https://portal.azure.com/) a přihlaste se jako **globálního správce** nebo **Spolusprávce.** Přejděte ke kroku 7. Pokud už jste se v okně Konfigurace aplikací v Azure AD.

2.  Otevřít **rozšíření Azure Active Directory** kliknutím **všechny služby** v horní části hlavní navigační nabídce vlevo.

3.  Zadejte **"Azure Active Directory**" do vyhledávacího pole filtrovat a vybrat **Azure Active Directory** položky.

4.  Klikněte na tlačítko **podnikové aplikace** levé navigační nabídce Azure Active Directory.

5.  Klikněte na tlačítko **všechny aplikace** zobrazíte seznam všech aplikací.

   * Pokud nevidíte aplikaci, kterou má zobrazit tady, použijte **filtr** ovládacího prvku v horní části **seznam všech aplikací** a nastavit **zobrazit** umožňuje **všechny Aplikace.**

6.  Vyberte aplikaci, kterou chcete nakonfigurovat jednotné přihlašování.

7.  Po načtení aplikace, klikněte na tlačítko **jednotného přihlašování** levé navigační nabídce aplikace.

8.  Vyberte **přihlašování na základě SAML** z **režimu** rozevíracího seznamu.

9.  Přejděte **identifikátor** nebo **adresy URL odpovědi** textového pole v části **části domény a adresy URL.**

10. Existují tři způsoby znát podporované vzory pro aplikace:

   * V textovém poli se zobrazí podporované následujícím vzorům jako zástupný symbol *příklad:* <https://contoso.com>.

   * Pokud vzor není podporovaná, se zobrazí červený vykřičník při pokusu o zadejte hodnotu v textovém poli. Pokud myší najedete myší červený vykřičník, uvidíte podporované vzory.

   * V tomto kurzu pro aplikaci můžete také získat informace o podporovaných vzorů. V části **konfiguraci Azure AD jednotného přihlašování** oddílu. Přejděte na krok pro hodnoty v rámci nakonfigurované **domény a adresy URL** oddílu.

Pokud se hodnoty neshodují s vzory předem nakonfigurované v Azure AD. Můžete:

-   Práce se na dodavatele aplikace a získání hodnoty, které odpovídají vzoru, předem nakonfigurované v Azure AD

-   Nebo můžete kontaktovat tým Azure AD na adrese <aadapprequest@microsoft.com> nebo komentář v kurzu, který požadavek aktualizaci podporované vzory pro aplikaci

## <a name="where-do-i-set-the-entityid-user-identifier-format"></a>Kde můžu nastavit formát EntityID (identifikátor uživatele)

Nebudete moci vybrat formát EntityID (identifikátor uživatele), který Azure AD se odesílá do aplikace v odpovědi po ověření uživatele.

Azure AD vyberte formát pro atribut NameID (identifikátor uživatele) na základě hodnoty vybrané nebo formátu požadovaná aplikací v SAML AuthRequest. Další informace najdete v článku [protokol jednotné přihlašování SAML](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-on-protocol-reference#authnrequest) části NameIDPolicy,

## <a name="cant-find-the-azure-ad-metadata-to-complete-the-configuration-with-the-application"></a>Nelze najít metadata služby Azure AD k dokončení konfigurace s aplikací

Chcete-li stáhnout metadata aplikace nebo certifikát z Azure AD, postupujte takto:

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

## <a name="dont-know-how-to-customize-saml-claims-sent-to-an-application"></a>Nevím, jak k přizpůsobení deklarací identity SAML odesílá se do aplikace

Informace o přizpůsobení deklarací identity atributu SAML odeslán do vaší aplikace najdete v tématu [mapování deklarací v Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-claims-mapping) Další informace.

## <a name="next-steps"></a>Další postup
[Správa aplikací pomocí Azure Active Directory](what-is-application-management.md)
