---
title: 'Kurz: Azure Active Directory integraci jednotného přihlašování s využitím inteligentního globálního řízení | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Smart Global zásad správného řízení.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 5c31613e-f30d-47d9-af51-001345b6db10
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 05/04/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: eb14f4c8dd498bf26ac093f7a491876cd2e73940
ms.sourcegitcommit: f57297af0ea729ab76081c98da2243d6b1f6fa63
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/06/2020
ms.locfileid: "82872632"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-smart-global-governance"></a>Kurz: Azure Active Directory integraci jednotného přihlašování pomocí inteligentního globálního řízení

V tomto kurzu se dozvíte, jak integrovat inteligentní řízení globálních zásad správného řízení s Azure Active Directory (Azure AD). Při integraci inteligentního řízení globálních zásad v Azure AD můžete:

* Řízení ve službě Azure AD, která má přístup k inteligentnímu globálnímu řízení.
* Umožněte, aby se vaši uživatelé automaticky přihlásili do inteligentního globálního řízení pomocí svých účtů Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.

Další informace o integraci aplikací SaaS s Azure AD najdete v tématu [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Požadavky

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* Automatické globální zásady správného řízení jednotného přihlašování (SSO).

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Smart Global zásad správného řízení podporuje **SP a IDP** iniciované jednotné přihlašování.
* Jakmile nakonfigurujete inteligentní zásady automatického řízení, můžete vynutili řízení relace, které chrání exfiltrace a infiltraci citlivých dat vaší organizace v reálném čase. Řízení relace se rozšiřuje z podmíněného přístupu. [Přečtěte si, jak vynutili řízení relace pomocí Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-smart-global-governance-from-the-gallery"></a>Přidání inteligentního řízení globálního řízení z Galerie

Pokud chcete nakonfigurovat integraci inteligentního globálního řízení do služby Azure AD, musíte do seznamu spravovaných aplikací SaaS přidat inteligentní řízení globálního řízení z galerie.

1. Přihlaste se k [Azure Portal](https://portal.azure.com) pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** zadejte do vyhledávacího pole **inteligentní řízení globálního řízení** .
1. Na panelu výsledků vyberte **inteligentní řízení** a pak přidejte aplikaci. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.

## <a name="configure-and-test-azure-ad-single-sign-on-for-smart-global-governance"></a>Konfigurace a testování jednotného přihlašování Azure AD pro Smart Global zásad správného řízení

Nakonfigurujte a otestujte jednotné přihlašování Azure AD pomocí inteligentního globálního řízení s využitím testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, je potřeba vytvořit vztah propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v inteligentním globálním řízení.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí inteligentního globálního řízení, dokončete následující stavební bloky:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
    1. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí B. Simon.
    1. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD.
1. **[Nakonfigurujte jednotné přihlašování inteligentního řízení](#configure-smart-global-governance-sso)** a nakonfigurujte nastavení jednotného přihlašování na straně aplikace.
    1. **[Vytvořte uživatele inteligentního globálního zásad správného řízení](#create-smart-global-governance-test-user)** – bude mít protějšek B. Simon v inteligentním globálním řízení, které je propojené s reprezentací uživatele Azure AD.
1. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

## <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V [Azure Portal](https://portal.azure.com/)na stránce inteligentní integrace aplikací **globálního řízení** pro správu najděte část **Správa** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jednotné přihlašování pomocí SAML** klikněte na ikonu Upravit/pero pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

1. Pokud chcete nakonfigurovat aplikaci v režimu iniciované **IDP** , zadejte v **základní části Konfigurace SAML** hodnoty následujících polí:

    a. V textovém poli **identifikátor** použijte jednu z těchto adres URL:

    | | |
    |-|-|
    | `https://eu-fr-south.console.smartglobalprivacy.com/platform/authentication-saml2/metadata`|
    | `https://eu-fr-south.console.smartglobalprivacy.com/dpo/authentication-saml2/metadata`|

    b. V textovém poli **Adresa URL odpovědi** použijte jednu z následujících adres URL:

    | | |
    |-|-|
    | `https://eu-fr-south.console.smartglobalprivacy.com/platform/authentication-saml2/acs`|
    | `https://eu-fr-south.console.smartglobalprivacy.com/dpo/authentication-saml2/acs`|

1. Klikněte na **nastavit další adresy URL** a proveďte následující krok, pokud chcete nakonfigurovat aplikaci v režimu iniciované **SP** :

    V textovém poli **Adresa URL pro přihlášení** použijte jednu z těchto adres URL:

    | | |
    |-|-|
    | `https://eu-fr-south.console.smartglobalprivacy.com/dpo`|
    | `https://eu-fr-south.console.smartglobalprivacy.com/platform`|

1. Na stránce **nastavit jednotné přihlašování pomocí SAML** v části **podpisový certifikát SAML** Najděte **certifikát (RAW)** a vyberte **Stáhnout** a Stáhněte certifikát a uložte ho do počítače.

    ![Odkaz na stažení certifikátu](common/certificateraw.png)

1. V části **Nastavení inteligentního globálního řízení \ globální zásady správného řízení** zkopírujte příslušné adresy URL na základě vašeho požadavku.

    ![Kopírovat adresy URL konfigurace](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD

V této části vytvoříte testovacího uživatele ve Azure Portal s názvem B. Simon.

1. V levém podokně Azure Portal vyberte možnost **Azure Active Directory**, vyberte možnost **Uživatelé**a potom vyberte možnost **Všichni uživatelé**.
1. V horní části obrazovky vyberte **Nový uživatel** .
1. Ve vlastnostech **uživatele** proveďte následující kroky:
   1. Do pole **Název** zadejte `B.Simon`.  
   1. Do pole **uživatelské jméno** zadejte username@companydomain.extension. Například, `B.Simon@contoso.com`.
   1. Zaškrtněte políčko **Zobrazit heslo** a pak zapište hodnotu, která se zobrazí v poli **heslo** .
   1. Klikněte na **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte B. Simon používat jednotné přihlašování pomocí Azure tím, že udělíte přístup k inteligentnímu řízení globálního řízení.

1. V Azure Portal vyberte **podnikové aplikace**a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte **inteligentní řízení globálního řízení**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.

   ![Odkaz uživatelé a skupiny](common/users-groups-blade.png)

1. Vyberte **Přidat uživatele**a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Odkaz Přidat uživatele](common/add-assign-user.png)

1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

## <a name="configure-smart-global-governance-sso"></a>Konfigurace jednotného přihlašování Smart Global zásad správného řízení

Chcete-li nakonfigurovat jednotné přihlašování na stránce **inteligentního globálního řízení** , je třeba odeslat stažený **certifikát (RAW)** a příslušné zkopírované adresy URL z Azure Portal do [týmu podpory inteligentního řízení](mailto:support.tech@smartglobal.com). Toto nastavení nastaví, aby bylo správně nastaveno připojení SAML SSO na obou stranách.

### <a name="create-smart-global-governance-test-user"></a>Vytvořit uživatele inteligentního řízení globálního řízení

V této části vytvoříte uživatele s názvem Britta Simon v inteligentním globálním řízení. Pokud chcete přidat uživatele na inteligentní globální platformě zásad správného řízení, pracujte s [týmem podpory inteligentního řízení globálního řízení](mailto:support.tech@smartglobal.com) . Před použitím jednotného přihlašování je nutné vytvořit a aktivovat uživatele.

## <a name="test-sso"></a>Test SSO 

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí přístupového panelu.

Když kliknete na dlaždici Inteligentní zásady správného řízení na přístupovém panelu, měli byste se automaticky přihlásit k inteligentnímu globálnímu řízení, pro které jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další zdroje

- [Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Vyzkoušejte si inteligentní řízení globálních věcí pomocí Azure AD](https://aad.portal.azure.com/)

- [Co je řízení relace v Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Ochrana inteligentního řízení globálního řízení pomocí pokročilých viditelností a ovládacích prvků](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)