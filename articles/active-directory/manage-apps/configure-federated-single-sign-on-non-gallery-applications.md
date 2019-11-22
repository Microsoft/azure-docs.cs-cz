---
title: Jak nakonfigurovat federované jednotné přihlašování pro aplikaci mimo galerii
description: Jak nakonfigurovat federované jednotné přihlašování pro vlastní aplikaci mimo galerii, kterou chcete integrovat se službou Azure AD
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
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/21/2019
ms.locfileid: "74274597"
---
# <a name="how-to-configure-federated-single-sign-on-for-a-non-gallery-application"></a>Jak nakonfigurovat federované jednotné přihlašování pro aplikaci mimo galerii

Chcete-li nakonfigurovat jednotné přihlašování pro aplikaci mimo galerii *bez psaní kódu*, je nutné mít předplatné nebo Azure AD Premium a aplikace musí podporovat SAML 2,0. Další informace o verzích Azure AD najdete na stránce [ceny Azure AD](https://azure.microsoft.com/pricing/details/active-directory/).

## <a name="overview-of-steps-required"></a>Přehled požadovaných kroků
Níže je uveden podrobný přehled kroků potřebných ke konfiguraci federovaného jednotného přihlašování s SAML 2,0 pro negalerii (např. vlastní) aplikace.

-   Konfigurace hodnot metadat aplikace v Azure AD (přihlašovací adresa URL, identifikátor, adresa URL odpovědi)

-   [Vyberte identifikátor uživatele a přidejte atributy uživatele, které se mají odeslat do aplikace.](#select-user-identifier-and-add-user-attributes-to-be-sent-to-the-application)

-   [Načtení metadat a certifikátů Azure AD](#download-the-azure-ad-metadata-or-certificate)

-   Konfigurace hodnot metadat Azure AD v aplikaci (přihlašovací adresa URL, vydavatel, adresa URL pro odhlášení a certifikát)

-   Přiřazení uživatelů k aplikaci

## <a name="configuring-single-sign-on-to-non-gallery-applications"></a>Konfigurace jednotného přihlašování k aplikacím mimo galerii

Pokud chcete nakonfigurovat jednotné přihlašování pro aplikaci, která není v galerii Azure AD, postupujte podle následujících kroků:

1. Otevřete [**Azure Portal**](https://portal.azure.com/) a přihlaste se jako **globální správce** nebo **spolusprávce.**

2. Otevřete **rozšíření Azure Active Directory** kliknutím na **všechny služby** v horní části hlavní navigační nabídky vlevo.

3. Zadejte **"Azure Active Directory**" do vyhledávacího pole filtrovat a vybrat **Azure Active Directory** položky.

4. v navigační nabídce Azure Active Directory vlevo klikněte na **podnikové aplikace** .

5. klikněte na tlačítko **Přidat** v pravém horním rohu podokna **podnikové aplikace** .

6. klikněte na **aplikaci mimo galerii** v části **Přidat vlastní aplikaci** .

7. Do textového pole **název** zadejte název aplikace.

8. Kliknutím na tlačítko **Přidat** přidejte aplikaci.

9. Po načtení aplikace klikněte na tlačítko **jednotného přihlašování** z nabídky navigace na levé straně aplikace.

10. V rozevíracím seznamu **režim** vyberte možnost **přihlašování založené na SAML** .

11. Zadejte požadované hodnoty do pole **doména a adresy URL.** Tyto hodnoty byste měli získat od dodavatele aplikace.

    1. Pokud chcete aplikaci nakonfigurovat jako IdP jednotné přihlašování, zadejte adresu URL odpovědi a identifikátor.

    2. **Volitelné:** Pokud chcete nakonfigurovat aplikaci jako jednotné přihlašování, přihlašovací adresa URL je požadovaná hodnota.

12. V části **atributy uživatele**vyberte jedinečný identifikátor pro uživatele v rozevíracím seznamu **identifikátor uživatele** .

13. **Volitelné:** kliknutím na **Zobrazit a upravit všechny ostatní atributy uživatele** upravíte atributy, které se mají odeslat do aplikace v tokenu SAML, když se uživatelé přihlásí.

    Přidání atributu:

    1. klikněte na **Přidat atribut**. Zadejte **název** a vyberte **hodnotu** z rozevíracího seznamu.

    2. Klikněte na **Uložit.** V tabulce se zobrazí nový atribut.

14. Kliknutím na **konfigurovat &lt;název aplikace&gt;** získáte přístup k dokumentaci týkající se konfigurace jednotného přihlašování v aplikaci. K disadrese jsou také adresy URL a certifikáty Azure AD, které aplikace vyžaduje.

15. [Přiřaďte uživatele k aplikaci.](#assign-users-to-the-application)

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

   2. Klikněte na **Uložit.** V tabulce se zobrazí nový atribut.

## <a name="download-the-azure-ad-metadata-or-certificate"></a>Stažení metadat nebo certifikátů Azure AD

Pokud chcete stáhnout metadata aplikace nebo certifikát z Azure AD, postupujte podle následujících kroků:

1. Otevřete [**Azure Portal**](https://portal.azure.com/) a přihlaste se jako **globální správce** nebo **spolusprávce.**

2. Otevřete **rozšíření Azure Active Directory** kliknutím na **všechny služby** v horní části hlavní navigační nabídky vlevo.

3. Zadejte **"Azure Active Directory**" do vyhledávacího pole filtrovat a vybrat **Azure Active Directory** položky.

4. v navigační nabídce Azure Active Directory vlevo klikněte na **podnikové aplikace** .

5. Klikněte na tlačítko **všechny aplikace** zobrazíte seznam všech aplikací.

   * Pokud nevidíte aplikaci, kterou má zobrazit tady, použijte **filtr** ovládacího prvku v horní části **seznam všech aplikací** a nastavit **zobrazit** umožňuje **všechny Aplikace.**

6. Vyberte aplikaci, u které jste nakonfigurovali jednotné přihlašování.

7. Po načtení aplikace klikněte na tlačítko **jednotného přihlašování** z nabídky navigace na levé straně aplikace.

8. Přejděte do části **podpisový certifikát SAML** a pak klikněte na **Stáhnout** sloupec hodnota. V závislosti na tom, co aplikace vyžaduje konfiguraci jednotného přihlašování, se zobrazí buď možnost stáhnout metadata XML nebo certifikát.

Azure AD také poskytuje adresu URL pro získání metadat. Podle tohoto vzoru Získejte adresu URL metadat, která je specifická pro aplikaci: `https://login.microsoftonline.com/<Directory ID>/federationmetadata/2007-06/federationmetadata.xml?appid=<Application ID>`.

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

12. **Volitelné:** Pokud byste chtěli **přidat více než jeden uživatel**, typ v jiném **celý název** nebo **e-mailová adresa** do **hledat podle názvu nebo e-mailová adresa** vyhledávací pole a klikněte na zaškrtávací políčko a přidáním tohoto uživatele do **vybrané** seznamu.

13. Po dokončení výběru uživatelů, klikněte na tlačítko **vyberte** tlačítko pro přidání do seznamu uživatelů a skupin pro přiřazení k aplikaci.

14. **Volitelné:** klikněte na tlačítko **vybrat roli** oblasti pro výběr **přidat přiřazení** podokně vyberte roli, kterou chcete přiřadit uživatelům, které jste vybrali.

15. Klikněte na tlačítko **přiřadit** tlačítko přiřadit aplikaci do vybraného uživatele.

Po krátké době možné ke spouštění těchto aplikací pomocí metod popsaných v části popis řešení uživatele, které jste vybrali.

## <a name="customizing-the-saml-claims-sent-to-an-application"></a>Přizpůsobení deklarací SAML odeslaných do aplikace

Další informace o tom, jak přizpůsobit deklarace identity atributů SAML odeslané do vaší aplikace, najdete v tématu [mapování deklarací v Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-claims-mapping) .

## <a name="next-steps"></a>Další kroky
[Poskytovat jednotné přihlašování do aplikací pomocí Proxy aplikace](application-proxy-configure-single-sign-on-with-kcd.md)
