---
title: Řešení potíží s jednotným přihlašováním založeným na SAML v Azure Active Directory
description: Řešení potíží s aplikací Azure AD, která je nakonfigurovaná pro jednotné přihlašování založené na SAML
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: troubleshooting
ms.date: 07/11/2017
ms.author: kenwith
ms.openlocfilehash: b169616042892c379196dd1d38c2343704aa1030
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "99257522"
---
# <a name="troubleshoot-saml-based-single-sign-on-in-azure-active-directory"></a>Řešení potíží s jednotným přihlašováním založeným na SAML v Azure Active Directory
Pokud narazíte na problém při konfiguraci aplikace. Ověřte, že jste postupovali podle všech kroků v kurzu pro aplikaci. V konfiguraci aplikace máte vloženou dokumentaci týkající se konfigurace aplikace. Můžete také získat přístup k [seznamu kurzů, jak integrovat aplikace SaaS s Azure Active Directory](../saas-apps/tutorial-list.md) pro podrobné pokyny.

## <a name="cant-add-another-instance-of-the-application"></a>Nejde přidat další instanci aplikace.
Chcete-li přidat druhou instanci aplikace, je nutné mít tyto možnosti:
-   Nakonfigurujte jedinečný identifikátor pro druhou instanci. Nebudete moct konfigurovat stejný identifikátor, který se používá pro první instanci.
-   Nakonfigurujte jiný certifikát než ten, který se používá pro první instanci.

Pokud aplikace nepodporuje žádnou z výše uvedených verzí. Pak nebudete moct nakonfigurovat druhou instanci.

## <a name="cant-add-the-identifier-or-the-reply-url"></a>Nelze přidat identifikátor nebo adresu URL odpovědi.
Pokud nemůžete nakonfigurovat identifikátor nebo adresu URL odpovědi, potvrďte, že hodnoty identifikátoru a adresy URL odpovědi odpovídají vzorům předem nakonfigurovaným pro aplikaci.

Chcete-li zjistit, jaké jsou vzory předem nakonfigurované pro aplikaci:
1. Otevřete [**Azure Portal**](https://portal.azure.com/) a přihlaste se jako **globální správce** nebo **spolusprávce.** Přejít ke kroku 7. Pokud už jste v okně Konfigurace aplikace v Azure AD.
2. Otevřete **rozšíření Azure Active Directory** kliknutím na **všechny služby** v horní části hlavní navigační nabídky vlevo.
3. Do vyhledávacího pole filtru zadejte **"Azure Active Directory**" a vyberte položku **Azure Active Directory** .
4. v navigační nabídce Azure Active Directory vlevo klikněte na **podnikové aplikace** .
5. Kliknutím na **všechny aplikace** zobrazíte seznam všech aplikací.
   * Pokud se vám nezobrazí aplikace, kterou chcete zobrazit, použijte ovládací prvek **filtru** v horní části **seznamu všechny aplikace** a nastavte možnost **Zobrazit** pro **všechny aplikace.**
6. Vyberte aplikaci, pro kterou chcete nakonfigurovat jednotné přihlašování.
7. Po načtení aplikace klikněte na tlačítko **jednotného přihlašování** z nabídky navigace na levé straně aplikace.
8. V rozevíracím seznamu **režim** vyberte možnost **přihlašování založené na SAML** .
9. Do textového pole **identifikátor** nebo **Adresa URL odpovědi** v části **doména a adresy URL.**
10. Existují tři způsoby, jak znát podporované vzory pro aplikaci:
    * V textovém poli se zobrazí podporované vzory jako zástupný *Příklad:* <https://contoso.com> .
    * Pokud není tento vzor podporován, zobrazí se při pokusu o zadání hodnoty do textového pole červený vykřičník. Pokud najedete myší na červený vykřičník, uvidíte podporované vzory.
    * V tomto kurzu pro aplikaci můžete získat také informace o podporovaných vzorcích. V části **Konfigurace jednotného přihlašování Azure AD** . Pro konfiguraci hodnot v části **doména a adresy URL** použijte krok.

Pokud se hodnoty neshodují se vzorci předem konfigurovanými v Azure AD. Další možnosti:
-   Spolupracovat s dodavatelem aplikace a získat hodnoty, které odpovídají vzoru předkonfigurovanému ve službě Azure AD
-   Nebo můžete kontaktovat tým Azure AD na adrese <aadapprequest@microsoft.com> nebo v tomto kurzu zanechat komentář, který požádá o aktualizaci podporovaných vzorů pro aplikaci.

## <a name="where-do-i-set-the-entityid-user-identifier-format"></a>Kde mám nastavit formát EntityID (uživatelský identifikátor)
Nebudete moct vybrat formát EntityID (uživatelský identifikátor), který Azure AD pošle do aplikace v reakci po ověření uživatele.

Azure AD vyberte formát pro atribut NameID (identifikátor uživatele) na základě vybrané hodnoty nebo formátu, který požaduje aplikace v AuthRequest SAML. Další informace najdete v článku [jediný Sign-On protokol SAML](../develop/single-sign-on-saml-protocol.md#authnrequest) v části NameIDPolicy,

## <a name="cant-find-the-azure-ad-metadata-to-complete-the-configuration-with-the-application"></a>Nejde najít metadata Azure AD, aby se mohla dokončit konfigurace aplikace.
Pokud chcete stáhnout metadata aplikace nebo certifikát z Azure AD, postupujte takto:
1. Otevřete [**Azure Portal**](https://portal.azure.com/) a přihlaste se jako **globální správce** nebo **spolusprávce.**
2. Otevřete **rozšíření Azure Active Directory** kliknutím na **všechny služby** v horní části hlavní navigační nabídky vlevo.
3. Do vyhledávacího pole filtru zadejte **"Azure Active Directory**" a vyberte položku **Azure Active Directory** .
4. v navigační nabídce Azure Active Directory vlevo klikněte na **podnikové aplikace** .
5. Kliknutím na **všechny aplikace** zobrazíte seznam všech aplikací.
   * Pokud se vám nezobrazí aplikace, kterou chcete zobrazit, použijte ovládací prvek **filtru** v horní části **seznamu všechny aplikace** a nastavte možnost **Zobrazit** pro **všechny aplikace.**
6. Vyberte aplikaci, u které jste nakonfigurovali jednotné přihlašování.
7. Po načtení aplikace klikněte na tlačítko **jednotného přihlašování** z nabídky navigace na levé straně aplikace.
8. Přejděte do části **podpisový certifikát SAML** a pak klikněte na **Stáhnout** sloupec hodnota. V závislosti na tom, co aplikace vyžaduje konfiguraci jednotného přihlašování, se zobrazí buď možnost stáhnout metadata XML nebo certifikát.

Azure AD neposkytuje adresu URL pro získání metadat. Metadata lze načíst pouze jako soubor XML.

## <a name="customize-saml-claims-sent-to-an-application"></a>Přizpůsobení deklarací identit SAML odesílaných do aplikace
Další informace o tom, jak přizpůsobit deklarace identity atributů SAML odeslané do vaší aplikace, najdete v tématu [mapování deklarací v Azure Active Directory](../develop/active-directory-claims-mapping.md) .

## <a name="next-steps"></a>Další kroky
* [Série rychlý Start při správě aplikací](view-applications-portal.md)