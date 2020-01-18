---
title: 'Kurz: Azure Active Directory integraci jednotného přihlašování s Spotinst | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Spotinst.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 2f6dbd70-c2db-4ae9-99ee-976c3090d214
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 01/03/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5490ff6c6143dff258d74e013bb9d4c821aab625
ms.sourcegitcommit: 2a2af81e79a47510e7dea2efb9a8efb616da41f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/17/2020
ms.locfileid: "76263281"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-spotinst"></a>Kurz: Azure Active Directory integraci jednotného přihlašování (SSO) s Spotinst

V tomto kurzu se dozvíte, jak integrovat Spotinst s Azure Active Directory (Azure AD). Když integrujete Spotinst s Azure AD, můžete:

* Řízení ve službě Azure AD, která má přístup k Spotinst.
* Umožněte, aby se vaši uživatelé automaticky přihlásili k Spotinst svým účtům Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.

Další informace o integraci aplikací SaaS s Azure AD najdete v tématu [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Požadavky

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* Spotinst odběr s povoleným jednotným přihlašováním (SSO).

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Spotinst podporuje jednotné přihlašování (SSO) **a IDP** .

## <a name="adding-spotinst-from-the-gallery"></a>Přidání Spotinst z Galerie

Pokud chcete nakonfigurovat integraci Spotinst do služby Azure AD, musíte přidat Spotinst z Galerie do svého seznamu spravovaných aplikací SaaS.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com) pomocí pracovního nebo školního účtu nebo osobního účtu Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** do vyhledávacího pole zadejte **Spotinst** .
1. Na panelu výsledků vyberte **Spotinst** a pak aplikaci přidejte. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.

## <a name="configure-and-test-azure-ad-single-sign-on-for-spotinst"></a>Konfigurace a testování jednotného přihlašování Azure AD pro Spotinst

Nakonfigurujte a otestujte jednotné přihlašování Azure AD pomocí Spotinst pomocí testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, je potřeba vytvořit propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v Spotinst.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí Spotinst, dokončete následující stavební bloky:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
    * **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí B. Simon.
    * **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD.
1. **[Nakonfigurujte SPOTINST SSO](#configure-spotinst-sso)** – pro konfiguraci nastavení jednotného přihlašování na straně aplikace.
    * **[Vytvořte Spotinst Test User](#create-spotinst-test-user)** -to, abyste měli protějšek B. Simon v Spotinst, která je propojená s reprezentací uživatele v Azure AD.
1. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

## <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V [Azure Portal](https://portal.azure.com/)na stránce integrace aplikací **Spotinst** Najděte oddíl **Spravovat** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jednotné přihlašování pomocí SAML** klikněte na ikonu Upravit/pero pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

1. Pokud chcete nakonfigurovat aplikaci v režimu iniciované **IDP** , proveďte v **základní části Konfigurace SAML** následující kroky:

    a. Ověřte **nastavení dalších adres URL**.

    b. Do textového pole **stav přenosu** zadejte hodnotu: `<ID>`

1. Klikněte na **nastavit další adresy URL** a proveďte následující kroky, pokud chcete nakonfigurovat aplikaci v režimu iniciované **SP** :

    Do textového pole **přihlašovací adresa URL** zadejte adresu url: `https://console.spotinst.com/auth/saml`

    > [!NOTE]
    > Hodnota stavu přenosu není v reálném čase. Hodnotu stavu přenosu aktualizujete skutečnou hodnotou stavu přenosu, která je vysvětlena dále v tomto kurzu.

1. Klikněte na možnost **Uložit**.

1. Spotinst aplikace očekává kontrolní výrazy SAML v určitém formátu, což vyžaduje přidání mapování vlastních atributů do konfigurace atributů tokenu SAML. Následující snímek obrazovky ukazuje seznam výchozích atributů.

    ![image](common/default-attributes.png)

1. Kromě toho očekává aplikace Spotinst několik dalších atributů, které se vrátí zpátky v odpovědi SAML, které jsou uvedené níže. Tyto atributy jsou také předem vyplněné, ale můžete je zkontrolovat podle vašich požadavků.

    | Name (Název) | Zdrojový atribut|
    | -----| --------------- |
    | E-mail | user.mail |
    | Jméno | user.givenname |
    | Příjmení | user.surname |

1. Na stránce **nastavit jednotné přihlašování pomocí SAML** v části **podpisový certifikát SAML** Najděte **XML metadata federace** a vyberte **Stáhnout** a Stáhněte certifikát a uložte ho do svého počítače.

    ![Odkaz ke stažení certifikátu](common/metadataxml.png)

1. V části **Nastavení Spotinst** zkopírujte na základě vašeho požadavku příslušné adresy URL.

    ![Kopírovat adresy URL konfigurace](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD

V této části vytvoříte testovacího uživatele ve Azure Portal s názvem B. Simon.

1. V levém podokně Azure Portal vyberte možnost **Azure Active Directory**, vyberte možnost **Uživatelé**a potom vyberte možnost **Všichni uživatelé**.
1. Vyberte **nového uživatele** v horní části obrazovky.
1. Ve vlastnostech **uživatele** proveďte následující kroky:
   1. Do pole **Název** zadejte `B.Simon`.  
   1. Do pole **uživatelské jméno** zadejte username@companydomain.extension. Například, `B.Simon@contoso.com`.
   1. Zaškrtněte políčko **Zobrazit heslo** a pak zapište hodnotu, která se zobrazí v poli **heslo** .
   1. Klikněte na **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části povolíte B. Simon pro použití jednotného přihlašování Azure tím, že udělíte přístup k Spotinst.

1. V Azure Portal vyberte **podnikové aplikace**a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte **Spotinst**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.

   ![Odkaz "Uživatele a skupiny"](common/users-groups-blade.png)

1. Vyberte **Přidat uživatele**a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Odkaz Přidat uživatele](common/add-assign-user.png)

1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

## <a name="configure-spotinst-sso"></a>Konfigurace jednotného přihlašování Spotinst

1. V jiném okně webového prohlížeče se přihlaste k Spotinst jako správce zabezpečení.

2. Klikněte na **ikonu uživatele** v pravém horním rohu obrazovky a pak klikněte na **Nastavení**.

    ![Nastavení Spotinst](./media/spotinst-tutorial/tutorial_spotinst_settings.png)

3. V horní části klikněte na kartu **zabezpečení** a pak vyberte **Zprostředkovatelé identity** a proveďte následující kroky:

    ![Zabezpečení Spotinst](./media/spotinst-tutorial/tutorial_spotinst_security.png)

    a. Zkopírujte hodnotu **stavu přenosu** pro instanci a vložte ji do textového pole **stav přenosu** v **základní části Konfigurace SAML** na Azure Portal.

    b. Klikněte na tlačítko **Procházet** a nahrajte soubor XML s metadaty, který jste stáhli z Azure Portal

    c. Klikněte na **ULOŽIT**.

### <a name="create-spotinst-test-user"></a>Vytvořit testovacího uživatele Spotinst

Cílem této části je vytvořit uživatele s názvem Britta Simon v Spotinst.

1. Pokud jste aplikaci nakonfigurovali v režimu iniciované v **SP** , proveďte následující kroky:

   a. V jiném okně webového prohlížeče se přihlaste k Spotinst jako správce zabezpečení.

   b. Klikněte na **ikonu uživatele** v pravém horním rohu obrazovky a pak klikněte na **Nastavení**.

    ![Nastavení Spotinst](./media/spotinst-tutorial/tutorial_spotinst_settings.png)

    c. Klikněte na **Uživatelé** a vyberte **Přidat uživatele**.

    ![Nastavení Spotinst](./media/spotinst-tutorial/adduser1.png)

    d. V části Přidat uživatele proveďte následující kroky:

    ![Nastavení Spotinst](./media/spotinst-tutorial/adduser2.png)

    * Do textového pole **celé jméno** zadejte jméno a příjmení uživatele, jako je **BrittaSimon**.

    * Do textového pole **e-mail** zadejte e-mailovou adresu uživatele, jako `brittasimon\@contoso.com`.

    * Pro **role organizace, role účtu a účty**vyberte podrobnosti specifické pro vaši organizaci.

2. Pokud jste aplikaci nakonfigurovali v režimu iniciované **IDP** , v této části neexistuje žádná položka akce. Spotinst podporuje zřizování za běhu, které je ve výchozím nastavení povolené. Nový uživatel se vytvoří během pokusu o přístup k Spotinst, pokud ještě neexistuje.

## <a name="test-sso"></a>Test SSO 

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Když na přístupovém panelu kliknete na dlaždici Spotinst, měli byste se automaticky přihlásit k Spotinst, pro které jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další zdroje informací:

- [Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Vyzkoušejte si Spotinst s Azure AD](https://aad.portal.azure.com/)

