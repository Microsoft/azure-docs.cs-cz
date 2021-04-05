---
title: 'Kurz: Azure Active Directory integrace s Signagelive | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Signagelive.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 1/11/2019
ms.author: jeedes
ms.openlocfilehash: 78324cfa58a8ac015b085052bdec7e3793befc1b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "96348394"
---
# <a name="tutorial-azure-active-directory-integration-with-signagelive"></a>Kurz: Azure Active Directory integrace s Signagelive

V tomto kurzu se dozvíte, jak integrovat Signagelive s Azure Active Directory (Azure AD).
Integrace Signagelive s Azure AD poskytuje následující výhody:

* Můžete kontrolovat v Azure AD, kteří mají přístup k Signagelive.
* Uživatelům můžete povolit, aby se automaticky přihlásili k Signagelive (jednotné přihlašování) pomocí svých účtů Azure AD.
* Účty můžete spravovat v jednom centrálním umístění: Azure Portal.

Další informace o integraci aplikací SaaS s Azure AD najdete v tématu [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](../manage-apps/what-is-single-sign-on.md). Pokud předplatné Azure ještě nemáte, napřed si [vytvořte bezplatný účet](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Požadavky

Ke konfiguraci integrace služby Azure AD s Signagelive potřebujete následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční [zkušební verzi](https://azure.microsoft.com/pricing/free-trial/).
* Signagelive odběr, který je s podporou jednotného přihlašování.

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Signagelive podporuje jednotné přihlašování iniciované v SP.

## <a name="add-signagelive-from-the-gallery"></a>Přidání Signagelive z Galerie

Pokud chcete nakonfigurovat integraci Signagelive do služby Azure AD, nejdřív přidejte Signagelive z Galerie do svého seznamu spravovaných aplikací SaaS.

Pokud chcete přidat Signagelive z Galerie, proveďte následující kroky:

1. V [Azure Portal](https://portal.azure.com)v levém podokně vyberte ikonu **Azure Active Directory** .

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. V části **podnikové aplikace** vyberte možnost **všechny aplikace** .

    ![Okno podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, vyberte tlačítko  **Nová aplikace** v horní části dialogového okna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **Signagelive**. 

     ![Signagelive v seznamu výsledků](common/search-new-app.png)

5. V podokně výsledků vyberte **Signagelive** a potom přidejte aplikaci kliknutím na tlačítko **Přidat** .

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD pomocí Signagelive na základě testovacího uživatele s názvem **Britta Simon**.
Aby jednotné přihlašování fungovalo, musíte vytvořit propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v Signagelive.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí Signagelive, nejdřív dokončete následující stavební bloky:

1. [Nakonfigurujte jednotné přihlašování Azure AD](#configure-azure-ad-single-sign-on) , aby mohli vaši uživatelé používat tuto funkci.
2. [Nakonfigurujte jednotné přihlašování Signagelive](#configure-signagelive-single-sign-on) ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
3. [Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user) pro testování jednotného přihlašování Azure AD pomocí Britta Simon.
4. [Přiřaďte testovacímu uživateli Azure AD](#assign-the-azure-ad-test-user) , aby mohl Britta Simon používat jednotné přihlašování Azure AD.
5. [Vytvořte Signagelive testovacího uživatele](#create-a-signagelive-test-user) , který bude mít protějšek Britta Simon v Signagelive, který je propojený s reprezentací uživatele Azure AD.
6. [Otestujte jednotné přihlašování](#test-single-sign-on) a ověřte, jestli konfigurace funguje.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace jednotného přihlašování Azure AD

V této části povolíte jednotné přihlašování Azure AD v Azure Portal.

Pokud chcete nakonfigurovat jednotné přihlašování Azure AD pomocí Signagelive, proveďte následující kroky:

1. V [Azure Portal](https://portal.azure.com/)na stránce integrace aplikací **Signagelive** vyberte **jednotné přihlašování**.

    ![Konfigurovat odkaz jednotného přihlašování](common/select-sso.png)

2. V dialogovém okně **Vybrat metodu jednotného přihlašování** vyberte možnost **SAML** pro povolení jednotného přihlašování.

    ![Režim výběru jednotného přihlašování](common/select-saml-option.png)

3. Na stránce **nastavit jednotné přihlašování pomocí SAML** vyberte **Upravit** a otevřete tak základní dialogové okno **Konfigurace SAML** .

    ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. V části **základní konfigurace SAML** proveďte následující kroky:

    ![Informace o jednotném přihlašování v doméně Signagelive a adresách URL](common/sp-signonurl.png)

    Do pole **přihlašovací adresa URL** zadejte adresu URL, která používá následující vzor:  `https://login.signagelive.com/sso/<ORGANIZATIONALUNITNAME>`

    > [!NOTE]
    > Hodnota není reálné číslo. Aktualizujte hodnotu skutečnou přihlašovací adresou URL. Pokud chcete získat hodnotu, obraťte se na [tým podpory klienta Signagelive](mailto:support@signagelive.com). Můžete se také podívat na vzory, které jsou uvedené v části **základní konfigurace SAML** v Azure Portal.

5. Na stránce **nastavit jednu Sign-On s SAML** v části **podpisový certifikát SAML** vyberte **Stáhnout** a Stáhněte si **certifikát (RAW)** z daných možností podle vašeho požadavku. Pak ho uložte na svém počítači.

    ![Odkaz na stažení certifikátu](common/certificateraw.png)

6. V části **Nastavení Signagelive** zkopírujte adresy URL, které potřebujete.

    ![Kopírovat adresy URL konfigurace](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor Azure AD

    c. Odhlašovací adresa URL

### <a name="configure-signagelive-single-sign-on"></a>Konfigurace jednotného přihlašování Signagelive

Pokud chcete nakonfigurovat jednotné přihlašování na straně Signagelive, pošlete stažený **certifikát (RAW)** a zkopírované adresy url z Azure Portal do [týmu podpory Signagelive](mailto:support@signagelive.com). Zajišťují, že připojení SAML SSO je na obou stranách správně nastavené.

### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD 

Cílem této části je vytvořit testovacího uživatele v Azure Portal s názvem Britta Simon.

1. V Azure Portal v levém podokně vyberte možnost **Azure Active Directory**, vyberte možnost **Uživatelé** a potom vyberte možnost **Všichni uživatelé**.

    ![Odkazy "uživatelé a skupiny" a "Všichni uživatelé"](common/users.png)

2. V horní části obrazovky vyberte **Nový uživatel** .

    ![Tlačítko pro nového uživatele](common/new-user.png)

3. V dialogovém okně **uživatel** proveďte následující kroky.

    ![Uživatelský dialog](common/user-properties.png)

    a. Do pole **název** zadejte **BrittaSimon**.
  
    b. Do pole **uživatelské jméno** zadejte " brittasimon@yourcompanydomain.extension ". V takovém případě můžete třeba zadat " BrittaSimon@contoso.com ".

    c. Zaškrtněte políčko **Zobrazit heslo** a potom si poznamenejte hodnotu zobrazenou v poli heslo.

    d. Vyberte **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte Britta Simon pro použití jednotného přihlašování pomocí Azure tím, že udělíte přístup k Signagelive.

1. V Azure Portal vyberte možnost **podnikové aplikace**, vyberte možnost **všechny aplikace** a pak vyberte možnost **Signagelive**.

    ![Okno Podnikové aplikace](common/enterprise-applications.png)

2. V seznamu aplikace vyberte **Signagelive**.

    ![Odkaz Signagelive v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **Uživatelé a skupiny**.

    ![Odkaz uživatelé a skupiny](common/users-groups-blade.png)

4. Klikněte na tlačítko **Přidat uživatele** . Pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny**.

    ![Podokno přidat přiřazení](common/add-assign-user.png)

5. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu **Uživatelé** možnost **Britta Simon**. Pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.

6. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, pak v dialogovém okně **Vybrat roli** vyberte příslušnou roli uživatele ze seznamu. Potom klikněte na tlačítko **Vybrat** v dolní části obrazovky.

7. V dialogovém okně **Přidat přiřazení** vyberte tlačítko **přiřadit** .

### <a name="create-a-signagelive-test-user"></a>Vytvořit testovacího uživatele v Signagelive

V této části vytvoříte uživatele s názvem Britta Simon v Signagelive. Pracujte s [týmem podpory Signagelive](mailto:support@signagelive.com) a přidejte uživatele na platformě Signagelive. Než začnete používat jednotné přihlašování, musíte uživatele vytvořit a aktivovat.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování 

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí portálu MyApp.

Když vyberete dlaždici **Signagelive** na portálu Mojeapl, měli byste se automaticky přihlásit. Další informace o portálu MyApp najdete v tématu [co je to portál MyApp?](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Další zdroje informací

- [Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory](./tutorial-list.md)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

- [Co je podmíněný přístup v Azure Active Directory?](../conditional-access/overview.md)