---
title: 'Kurz: Azure Active Directory integraci jednotného přihlašování s jednotným přihlašováním pro Skytap | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a jednotným přihlašováním pro Skytap.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: d6cb7ab2-da1a-4015-8e6f-c0c47bb6210f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 02/13/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 847534ee28880cd2206c6abef605e695cc968f80
ms.sourcegitcommit: f255f869c1dc451fd71e0cab340af629a1b5fb6b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/16/2020
ms.locfileid: "77370692"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-single-sign-on-for-skytap"></a>Kurz: Azure Active Directory integraci jednotného přihlašování s jednotným přihlašováním pro Skytap

V tomto kurzu se dozvíte, jak integrovat jednotné přihlašování pro Skytap s Azure Active Directory (Azure AD). Když integrujete jednotné přihlašování pro Skytap s Azure AD, můžete:

* Řízení ve službě Azure AD, která má přístup k jednotnému přihlašování pro Skytap.
* Umožněte, aby se vaši uživatelé automaticky přihlásili do jednotného přihlašování pro Skytap s účty Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.

Další informace o integraci aplikací SaaS s Azure AD najdete v tématu [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Požadavky

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* Jednotné přihlašování pro předplatné s povoleným Skytapm jednotným přihlašováním (SSO).

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Jednotné přihlašování pro Skytap podporuje **aktualizace SP a IDP, které** iniciovaly jednotné přihlašování.
* Po nakonfigurování jednotného přihlašování pro Skytap můžete vynutili řízení relace, které chrání exfiltrace a infiltraci citlivých dat vaší organizace v reálném čase. Řízení relace se rozšiřuje z podmíněného přístupu. [Přečtěte si, jak vynutili řízení relace pomocí Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-single-sign-on-for-skytap-from-the-gallery"></a>Přidání jednotného přihlašování pro Skytap z Galerie

Pokud chcete nakonfigurovat integraci jednotného přihlašování pro Skytap do služby Azure AD, musíte do seznamu spravovaných aplikací pro SaaS přidat jednotné přihlašování pro Skytap z galerie.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com) pomocí pracovního nebo školního účtu nebo osobního účtu Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** zadejte do vyhledávacího pole **jednotné přihlašování pro Skytap** .
1. Na panelu výsledků vyberte **jednotné přihlašování pro Skytap** a pak přidejte aplikaci. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.

## <a name="configure-and-test-azure-ad-single-sign-on-for-single-sign-on-for-skytap"></a>Konfigurace a testování jednotného přihlašování Azure AD pro jednotné přihlašování pro Skytap

Nakonfigurujte a otestujte jednotné přihlašování Azure AD s jednotným přihlašováním pro Skytap pomocí testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, musíte v rámci jednotného přihlašování pro Skytap vytvořit vztah propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD s jednotným přihlašováním pro Skytap, dokončete následující stavební bloky:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
    1. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí B. Simon.
    1. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD.
1. **[Konfigurace jednotného přihlašování pro SKYTAP SSO](#configure-single-sign-on-for-skytap-sso)** – pro konfiguraci nastavení jednotného přihlašování na straně aplikace
    1. **[Vytvořte jednotné přihlašování pro Skytap testovacího uživatele](#create-single-sign-on-for-skytap-test-user)** – můžete mít protějšek B. Simon v rámci jednotného přihlašování pro Skytap, které je propojené s reprezentací uživatele v Azure AD.
1. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

## <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V [Azure Portal](https://portal.azure.com/)na stránce **jednotné přihlašování pro** integraci aplikace Skytap najděte část **Správa** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jednotné přihlašování pomocí SAML** klikněte na ikonu Upravit/pero pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

1. Pokud chcete nakonfigurovat aplikaci v režimu iniciované **IDP** , proveďte v **základní části Konfigurace SAML** následující kroky:

    a. Do textového pole **identifikátor** zadejte adresu URL pomocí následujícího vzoru: `http://pingone.com/<custom EntityID>`

    b. Do textového pole **Adresa URL odpovědi** zadejte adresu URL pomocí následujícího vzoru: `https://sso.connect.pingidentity.com/sso/sp/ACS.saml2`

1. Klikněte na **nastavit další adresy URL** a proveďte následující kroky, pokud chcete nakonfigurovat aplikaci v režimu iniciované **SP** :

    d. Do textového pole **přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru: `https://sso.connect.pingidentity.com/sso/sp/initsso?saasid=<saasid>&idpid=<idpid>`

    
    e. Do textového pole **stav přenosu** zadejte adresu URL pomocí následujícího vzoru: `https://pingone.com/1.0/<custom ID>`

    > [!NOTE]
    > Tyto hodnoty nejsou reálné. Aktualizujte tyto hodnoty skutečným identifikátorem, adresou URL odpovědi, přihlašovací adresou URL a stavem přenosu. Pokud chcete získat tyto hodnoty, obraťte se na [jednotné přihlašování k Skytap týmu podpory klientů](mailto:support@skytap.com) . Můžete se také podívat na vzory uvedené v části **základní konfigurace SAML** v Azure Portal.

1. Na stránce **nastavit jednotné přihlašování pomocí SAML** v části **podpisový certifikát SAML** Najděte **XML metadata federace** a vyberte **Stáhnout** a Stáhněte si soubor metadat a uložte ho do svého počítače.

    ![Odkaz ke stažení certifikátu](common/metadataxml.png)

1. V části **nastavení jednotného přihlašování pro Skytap** zkopírujte na základě vašeho požadavku příslušné adresy URL.

    ![Kopírovat adresy URL konfigurace](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD

V této části vytvoříte testovacího uživatele ve Azure Portal s názvem B. Simon.

1. V levém podokně Azure Portal vyberte možnost **Azure Active Directory**, vyberte možnost **Uživatelé**a potom vyberte možnost **Všichni uživatelé**.
1. V horní části obrazovky vyberte **Nový uživatel** .
1. Ve vlastnostech **uživatele** proveďte následující kroky:
   1. Do pole **Název** zadejte `B.Simon`.  
   1. Do pole **uživatelské jméno** zadejte username@companydomain.extension. například `B.Simon@contoso.com`.
   1. Zaškrtněte políčko **Zobrazit heslo** a pak zapište hodnotu, která se zobrazí v poli **heslo** .
   1. Klikněte na možnost **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části povolíte B. Simon pro použití jednotného přihlašování Azure tím, že udělíte přístup k jednotnému přihlašování pro Skytap.

1. V Azure Portal vyberte **podnikové aplikace**a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte **jednotné přihlašování pro Skytap**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.

   ![Odkaz "Uživatele a skupiny"](common/users-groups-blade.png)

1. Vyberte **Přidat uživatele**a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Odkaz Přidat uživatele](common/add-assign-user.png)

1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

## <a name="configure-single-sign-on-for-skytap-sso"></a>Konfigurace jednotného přihlašování pro jednotné přihlašování Skytap

Chcete-li nakonfigurovat jednotné přihlašování při **jednotném přihlašování pro Skytap** stranu, je třeba odeslat stažené **federační metadata XML** a příslušné zkopírované adresy URL z Azure Portal do [jednotného přihlašování pro tým podpory klientů Skytap](mailto:support@skytap.com). Nastavují tohoto nastavení můžete mít správně nastavené na obou stranách připojení SAML SSO.


### <a name="create-single-sign-on-for-skytap-test-user"></a>Vytvoření jednotného přihlašování pro Skytap testovacího uživatele

V této části vytvoříte uživatele s názvem Britta Simon v rámci jednotného přihlašování pro Skytap. Pokud chcete přidat uživatele do jednotného přihlašování pro platformu Skytap, pracujte s [jednotným přihlašováním pro tým podpory klienta Skytap](mailto:support@skytap.com) . Uživatelé musí vytvořit a aktivovat, než použití jednotného přihlašování.

## <a name="test-sso"></a>Test SSO 

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na dlaždici jednotného přihlašování pro Skytap na přístupovém panelu byste měli být automaticky přihlášení do jednotného přihlašování pro Skytap, pro které jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další materiály a zdroje informací

- [Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Vyzkoušejte si časovou rezervu pomocí Azure AD](https://aad.portal.azure.com/)

