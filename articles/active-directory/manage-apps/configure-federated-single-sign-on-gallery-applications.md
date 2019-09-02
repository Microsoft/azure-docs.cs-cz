---
title: Jak nakonfigurovat federované jednotné přihlašování pro aplikaci Galerie Azure AD | Microsoft Docs
description: Jak nakonfigurovat federované jednotné přihlašování pro existující aplikaci Galerie Azure AD a jak rychle začít pracovat s kurzy
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
ms.openlocfilehash: 8e50a495e1b0406e0c935ac31111dc6b5d0c0821
ms.sourcegitcommit: 5f67772dac6a402bbaa8eb261f653a34b8672c3a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/01/2019
ms.locfileid: "70207133"
---
# <a name="how-to-configure-federated-single-sign-on-for-an-azure-ad-gallery-application"></a>Jak nakonfigurovat federované jednotné přihlašování pro aplikaci Galerie Azure AD

K dispozici je podrobný kurz pro všechny aplikace v galerii Azure Active Directory (Azure AD), které mají schopnost podnikového jednotného přihlašování. Můžete získat přístup k [seznamu kurzů, jak integrovat aplikace SaaS s Azure Active Directory](https://azure.microsoft.com/documentation/articles/active-directory-saas-tutorial-list/) pro podrobné podrobné pokyny.

## <a name="overview-of-steps-required"></a>Přehled požadovaných kroků
Ke konfiguraci aplikace z Galerie Azure AD potřebujete:

-   [Přidání aplikace z Galerie Azure AD](#add-an-application-from-the-azure-ad-gallery)

-   [Konfigurace hodnot metadat aplikace v Azure AD (přihlašovací adresa URL, identifikátor, adresa URL odpovědi)](#configure-single-sign-on-for-an-application-from-the-azure-ad-gallery)

-   [Vyberte identifikátor uživatele a přidejte atributy uživatele, které se mají odeslat do aplikace.](#select-user-identifier-and-add-user-attributes-to-be-sent-to-the-application)

-   [Načtení metadat a certifikátů Azure AD](#download-the-azure-ad-metadata-or-certificate)

-   [Konfigurace hodnot metadat Azure AD v aplikaci (přihlašovací adresa URL, vydavatel, adresa URL pro odhlášení a certifikát)](#configure-single-sign-on-for-an-application-from-the-azure-ad-gallery)

-   [Přiřazení uživatelů k aplikaci](#assign-users-to-the-application)

## <a name="add-an-application-from-the-azure-ad-gallery"></a>Přidání aplikace z Galerie Azure AD

Pokud chcete přidat aplikaci z Galerie Azure AD, postupujte podle těchto kroků:

1.  Otevřete [Azure Portal](https://portal.azure.com) a přihlaste se jako **globální správce** nebo **spolusprávce**.

2.  Otevřete **rozšíření Azure Active Directory** výběrem možnosti **všechny služby** v horní části hlavní navigační nabídky na levé straně.

3.  Do vyhledávacího pole zadejte "Azure Active Directory" a vyberte **Azure Active Directory**.

4.  Vyberte **podnikové aplikace** z navigační nabídky na levé straně Azure AD.

5.  V pravém horním rohu podokna **podnikové aplikace** vyberte **Přidat** .

6.  Do pole **Zadejte název** v části **Přidat z Galerie** zadejte název aplikace.

7.  Vyberte aplikaci, kterou chcete nakonfigurovat pro jednotné přihlašování.

8.  Před přidáním aplikace můžete změnit její název v poli **název** .

9.  Chcete-li přidat aplikaci, vyberte možnost **Přidat** .

Po krátké době byste měli být schopni zobrazit podokno konfigurace aplikace.

## <a name="configure-single-sign-on-for-an-application-from-the-azure-ad-gallery"></a>Konfigurace jednotného přihlašování pro aplikaci z Galerie Azure AD

Pro konfiguraci jednotného přihlašování pro aplikaci použijte následující postup:

1. Otevřete [Azure Portal](https://portal.azure.com/) a přihlaste se jako **globální správce** nebo **spolusprávce**.

2. Otevřete **rozšíření Azure Active Directory** výběrem možnosti **všechny služby** v horní části hlavní navigační nabídky na levé straně.

3. Do vyhledávacího pole zadejte "Azure Active Directory" a vyberte **Azure Active Directory**.

4. Vyberte **podnikové aplikace** z navigační nabídky Azure Active Directory vlevo.

5. Výběrem **všech aplikací** zobrazíte seznam všech aplikací.

   * Pokud zde požadovanou aplikaci nevidíte, použijte ovládací prvek **filtru** v horní části **seznamu všechny aplikace** a nastavte možnost **Zobrazit** na **všechny aplikace.**

6. Vyberte aplikaci, pro kterou chcete nakonfigurovat jednotné přihlašování.

7. Po načtení aplikace vyberte **jednotné přihlašování** z navigační nabídky aplikace na levé straně.

8. V rozevíracím seznamu **režim** vyberte možnost přihlašování **založené na SAML** .

9. Zadejte požadované hodnoty do pole **doména a adresy URL.** Tyto hodnoty byste měli získat od dodavatele aplikace.

   1. Pokud chcete nakonfigurovat aplikaci jako jednotné přihlašování, přihlašovací adresa URL je požadovaná hodnota. U některých aplikací je identifikátor také povinná hodnota.

   2. Pokud chcete nakonfigurovat aplikaci jako IdP jednotného přihlašování, adresa URL odpovědi je požadovaná hodnota. U některých aplikací je identifikátor také povinná hodnota.

10. **Volitelné**: Pokud chcete zobrazit nepotřebné hodnoty, vyberte **Zobrazit upřesňující nastavení adresy URL** .

11. V části **atributy uživatele**vyberte jedinečný identifikátor pro uživatele v rozevíracím seznamu **identifikátor uživatele** .

12. **Volitelné**: Vyberte **Zobrazit a upravit všechny ostatní atributy uživatele** a upravte atributy, které se mají odeslat do aplikace v TOKENu SAML, když se uživatelé přihlásí.

    Přidání atributu:
   
    1. Vyberte **Přidat atribut**. Zadejte **název** a vyberte **hodnotu** z rozevíracího seznamu.

    1. Vyberte **Uložit.** V tabulce se zobrazí nový atribut.

13. Vyberte **Konfigurovat &lt;název&gt; aplikace** pro přístup k dokumentaci týkající se konfigurace jednotného přihlašování v aplikaci. K nastavení jednotného přihlašování v aplikaci máte taky potřebné adresy URL a certifikát metadat.

14. Kliknutím na **Uložit** uložte konfiguraci.

15. Přiřaďte uživatele k aplikaci.

## <a name="select-user-identifier-and-add-user-attributes-to-be-sent-to-the-application"></a>Vyberte identifikátor uživatele a přidejte atributy uživatele, které se mají odeslat do aplikace.

Chcete-li vybrat identifikátor uživatele nebo přidat atributy uživatele, použijte následující postup:

1. Otevřete [Azure Portal](https://portal.azure.com/) a přihlaste se jako **globální správce** nebo **spolusprávce.**

2. Otevřete **rozšíření Azure Active Directory** výběrem možnosti **všechny služby** v horní části hlavní navigační nabídky na levé straně.

3. Do vyhledávacího pole zadejte "Azure Active Directory" a vyberte **Azure Active Directory**.

4. Vyberte **podnikové aplikace** z navigační nabídky Azure Active Directory vlevo.

5. Výběrem **všech aplikací** zobrazíte seznam všech aplikací.

   * Pokud zde požadovanou aplikaci nevidíte, použijte ovládací prvek **filtru** v horní části **seznamu všechny aplikace** a nastavte možnost **Zobrazit** na **všechny aplikace.**

6. Vyberte aplikaci, kterou jste nakonfigurovali s jednotným přihlašováním.

7. Po načtení aplikace vyberte **jednotné přihlašování** z navigační nabídky aplikace na levé straně.

8. V části **atributy uživatele** vyberte jedinečný identifikátor pro uživatele v rozevíracím seznamu **identifikátor uživatele** . Aby bylo možné uživatele ověřit, musí vybraná možnost odpovídat očekávané hodnotě v aplikaci.

   >[!NOTE] 
   >Azure AD vybere formát pro atribut NameID (identifikátor uživatele) na základě vybrané hodnoty nebo formátu, který vyžádala aplikace v AuthRequest SAML. Další informace najdete v části [protokol SAML jednotného přihlašování](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-on-protocol-reference#authnrequest) v oddílu NameIDPolicy.
   >
   >

9. Chcete-li přidat atributy uživatele, vyberte možnost **Zobrazit a upravit všechny ostatní atributy uživatele** a upravte atributy, které se mají odeslat do aplikace v tokenu SAML, když se uživatelé přihlásí.

   Přidání atributu:
  
   1. Vyberte **Přidat atribut**. Zadejte **název** a vyberte **hodnotu** z rozevíracího seznamu.

   2. Vyberte **Uložit**. V tabulce se zobrazí nový atribut.

## <a name="download-the-azure-ad-metadata-or-certificate"></a>Stažení metadat nebo certifikátů Azure AD

Pokud chcete stáhnout metadata aplikace nebo certifikát z Azure AD, postupujte takto:

1. Otevřete [Azure Portal](https://portal.azure.com/) a přihlaste se jako **globální správce** nebo **spolusprávce.**

2. Otevřete **rozšíření Azure Active Directory** výběrem možnosti **všechny služby** v horní části hlavní navigační nabídky na levé straně.

3. Do vyhledávacího pole zadejte "Azure Active Directory" a vyberte **Azure Active Directory**.

4. Vyberte **podnikové aplikace** z navigační nabídky Azure Active Directory vlevo.

5. Výběrem **všech aplikací** zobrazíte seznam všech aplikací.

   *  Pokud zde požadovanou aplikaci nevidíte, použijte ovládací prvek **filtru** v horní části **seznamu všechny aplikace** a nastavte možnost **Zobrazit** na **všechny aplikace**.

6. Vyberte aplikaci, kterou jste nakonfigurovali s jednotným přihlašováním.

7. Po načtení aplikace vyberte **jednotné přihlašování** z navigační nabídky aplikace na levé straně.

8. V části přejít na **podpisový certifikát SAML** vyberte **Stáhnout** hodnotu sloupce. V závislosti na tom, co aplikace vyžaduje konfiguraci jednotného přihlašování, se zobrazí buď možnost stáhnout metadata XML nebo certifikát.

Azure AD taky poskytuje adresu URL pro přístup k metadatům. K získání adresy URL metadat specifické pro aplikaci použijte následující model:`https://login.microsoftonline.com/<Directory ID>/federationmetadata/2007-06/federationmetadata.xml?appid=<Application ID>`

## <a name="assign-users-to-the-application"></a>Přiřazení uživatelů k aplikaci

Chcete-li přiřadit jednoho nebo více uživatelů k aplikaci přímo, postupujte podle následujících kroků:

1. Otevřete [Azure Portal](https://portal.azure.com/) a přihlaste se jako **globální správce.**

2. Otevřete **rozšíření Azure Active Directory** výběrem možnosti **všechny služby** v horní části hlavní navigační nabídky na levé straně.

3. Do vyhledávacího pole zadejte "Azure Active Directory" a vyberte **Azure Active Directory**.

4. Vyberte **podnikové aplikace** z navigační nabídky Azure Active Directory vlevo.

5. Výběrem **všech aplikací** zobrazíte seznam všech aplikací.

   * Pokud zde požadovanou aplikaci nevidíte, použijte ovládací prvek **filtru** v horní části **seznamu všechny aplikace** a nastavte možnost **Zobrazit** na **všechny aplikace.**

6. Vyberte aplikaci, kterou chcete přiřadit uživatele ze seznamu.

7. Po načtení aplikace vyberte **Uživatelé a skupiny** z navigační nabídky na levé straně aplikace.

8. Kliknutím na tlačítko **Přidat** v horní části seznamu **uživatelů a skupin** otevřete podokno **Přidat přiřazení** .

9. V podokně **Přidat přiřazení** vyberte selektor **Uživatelé a skupiny** .

10. Do vyhledávacího pole **jméno nebo e-mailová adresa** zadejte **celé jméno** nebo **e-mailovou adresu** uživatele, kterého chcete přiřadit.

11. Pokud chcete zobrazit **zaškrtávací**políčko, najeďte myší na **uživatele** v seznamu. Zaškrtněte políčko vedle fotografie profilu uživatele nebo logo a přidejte uživatele do **vybraného** seznamu.

12. **Volitelné**: Pokud chcete **Přidat více než jednoho uživatele**, zadejte do pole **Hledat podle jména nebo e-mailové adresy** jiné **celé jméno** nebo **e-mailovou adresu** a zaškrtnutím políčka přidejte tohoto uživatele do **vybraného** seznamu.

13. Až budete s výběrem uživatelů hotovi, vyberte tlačítko **Vybrat** a přidejte je do seznamu uživatelů a skupin, které chcete přiřadit k aplikaci.

14. **Volitelné**: Vyberte výběr **role vybrat** v podokně **Přidat přiřazení** a vyberte roli, kterou chcete přiřadit vybraným uživatelům.

15. Vyberte tlačítko **přiřadit** a přiřaďte aplikaci k vybraným uživatelům.

Po krátké době možné ke spouštění těchto aplikací pomocí metod popsaných v části popis řešení uživatele, které jste vybrali.

## <a name="customizing-the-saml-claims-sent-to-an-application"></a>Přizpůsobení deklarací SAML odeslaných do aplikace

Informace o tom, jak přizpůsobit deklarace atributů SAML odeslané do vaší aplikace, najdete [v tématu Mapování deklarací v Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-claims-mapping).

## <a name="next-steps"></a>Další postup
[Poskytovat jednotné přihlašování do aplikací pomocí Proxy aplikace](application-proxy-configure-single-sign-on-with-kcd.md)



