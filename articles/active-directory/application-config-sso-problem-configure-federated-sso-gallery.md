---
title: Problém konfigurace federované jednotné přihlašování pro aplikaci Galerie Azure AD | Microsoft Docs
description: Některé běžné problémy, kterým může dojít při konfiguraci federovaný jeden adres přihlášení pomocí SAML pro aplikace, které jsou uvedeny v galerii aplikací Azure AD
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
ms.openlocfilehash: 7fcc8c24214465bc1e601ced69ee67df2c48274f
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "36334254"
---
# <a name="problem-configuring-federated-single-sign-on-for-an-azure-ad-gallery-application"></a>Problém konfigurace federované jednotné přihlašování pro aplikaci Galerie Azure AD

Pokud dojde k potížím při konfiguraci aplikace. Ověřte, že jste provedli všechny kroky v tomto kurzu pro aplikaci. V konfiguraci aplikace máte vložené dokumentaci o konfiguraci aplikace. Navíc se dostanete [seznam kurzů k integraci aplikací SaaS v Azure Active Directory](https://azure.microsoft.com/documentation/articles/active-directory-saas-tutorial-list/) podrobností podrobné pokyny.

## <a name="cant-add-another-instance-of-the-application"></a>Nelze přidat jiná instance aplikace

Přidat druhou instanci aplikace, musíte být schopni:

-   Jedinečný identifikátor pro druhou instanci nakonfigurujte. Nebudete moci konfigurovat stejný identifikátor použitý pro první instanci.

-   Nakonfigurujte jiný certifikát než ten, který používá pro první instanci.

Pokud aplikace nepodporuje žádné z výše. Potom nebudete moci konfigurovat druhou instanci.

## <a name="cant-add-the-identifier-or-the-reply-url"></a>Nelze přidat identifikátor nebo adresa URL odpovědi

Pokud si nejste moci konfigurovat identifikátor nebo adresa URL odpovědi, potvrzení hodnoty identifikátor a adresa URL odpovědi vzory předem nakonfigurované pro danou aplikaci.

Vzory vědět předem nakonfigurované pro aplikaci:

1.  Otevřete [ **portál Azure** ](https://portal.azure.com/) a přihlaste se jako **globálního správce** nebo **ko-správce.** Přejděte ke kroku 7. Pokud jste už v okně Konfigurace aplikace na Azure AD.

2.  Otevřete **rozšíření Azure Active Directory** kliknutím **všechny služby** v horní části hlavní levé navigační nabídce.

3.  Zadejte **"Azure Active Directory**" v filtru vyhledávacího pole a vyberte **Azure Active Directory** položky.

4.  Klikněte na tlačítko **podnikové aplikace, které** z levé navigační nabídce Azure Active Directory.

5.  Klikněte na tlačítko **všechny aplikace** Chcete-li zobrazit seznam všech aplikací.

   * Pokud aplikaci chcete, aby se zobrazí tady nevidíte, pomocí **filtru** ovládací prvek v horní části **seznam všech aplikací** a nastavte **zobrazit** možnost k **všechny Aplikace.**

6.  Vyberte aplikaci, kterou chcete konfigurovat jednotné přihlašování.

7.  Po načtení aplikace, klikněte na **jednotného přihlašování** z levé navigační nabídky.

8.  Vyberte **na základě SAML přihlašování** z **režimu** rozevíracího seznamu.

9.  Přejděte na **identifikátor** nebo **adresa URL odpovědi** textové pole, v části **oddílu domény a adresy URL.**

10. Existují tři způsoby, jak vědět podporované vzory pro aplikaci:

   * Do textového pole, jako zástupný znak zobrazí podporované nalezeny *příklad:* <https://contoso.com>.

   * Pokud vzor není podporován, zobrazí se při pokusu o do textového pole zadejte hodnotu červený vykřičník. Umístěte ukazatel myši nad červený vykřičník, uvidíte podporované vzory.

   * V tomto kurzu pro aplikaci můžete také získat informace o podporovaných vzory. V části **konfigurovat Azure AD jednotné přihlašování** části. Přejděte na krok pro hodnoty v části nakonfigurována **domény a adresy URL** části.

Pokud se hodnoty neshodují s vzory předem nakonfigurované na Azure AD. Můžete:

-   Spolupracujte s dodavatelem aplikace získat hodnoty, které se shodují se vzorem předem nakonfigurované na Azure AD

-   Nebo se obrátit na tým Azure AD v <aadapprequest@microsoft.com> nebo komentář v kurzu k vyžádání aktualizace podporované vzory pro aplikaci

## <a name="where-do-i-set-the-entityid-user-identifier-format"></a>Kde lze nastavit formát EntityID (identifikátor uživatele)

Nebudete moci vybrat formát EntityID (identifikátor uživatele), který odešle aplikaci v odpovědi po ověření uživatele Azure AD.

Azure AD vyberte formát pro atribut NameID (identifikátor uživatele) na základě hodnoty vybraných nebo formát požadovanou aplikaci v SAML AuthRequest. Další informace najdete v článku [protokolu SAML přihlašování](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-on-protocol-reference#authnrequest) části NameIDPolicy,

## <a name="cant-find-the-azure-ad-metadata-to-complete-the-configuration-with-the-application"></a>Nelze najít metadata, Azure AD k dokončení konfigurace s aplikací

Chcete-li stáhnout metadata aplikace nebo certifikát z Azure AD, postupujte takto:

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

## <a name="dont-know-how-to-customize-saml-claims-sent-to-an-application"></a>Nemusíte vědět, jak přizpůsobit SAML deklarace identity odeslat do aplikace

Naučte se přizpůsobovat deklarací identity atributu SAML odeslaných do vaší aplikace, najdete v tématu [deklarací mapování v Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-claims-mapping) Další informace.

## <a name="next-steps"></a>Další postup
[Správa aplikací pomocí Azure Active Directory](manage-apps/what-is-application-management.md)
