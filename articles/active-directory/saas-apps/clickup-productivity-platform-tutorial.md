---
title: 'Kurz: Azure Active Directory integrace s platformou produktivity ClickUp | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directoryou a ClickUpou produkční platformou.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/21/2019
ms.author: jeedes
ms.openlocfilehash: 99ee85c569340d8f1d3cff27112436465238c05f
ms.sourcegitcommit: 9b8425300745ffe8d9b7fbe3c04199550d30e003
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/23/2020
ms.locfileid: "92455865"
---
# <a name="tutorial-azure-active-directory-integration-with-clickup-productivity-platform"></a>Kurz: Azure Active Directory integrace s platformou produktivity ClickUp

V tomto kurzu se naučíte integrovat platformu ClickUp produktivity s využitím služby Azure Active Directory (Azure AD).
Integrace ClickUp produktivity se službou Azure AD poskytuje následující výhody:

* Můžete kontrolovat v Azure AD, kteří mají přístup k produkční platformě ClickUp.
* Uživatelům můžete povolit, aby se automaticky přihlásili k ClickUp produktivní platformě (jednotné přihlašování) s účty Azure AD.
* Účty můžete spravovat v jednom centrálním umístění – Azure Portal.

Pokud chcete získat další podrobnosti o integraci aplikace SaaS s Azure AD, přečtěte si téma [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](../manage-apps/what-is-single-sign-on.md).
Pokud předplatné Azure ještě nemáte, napřed si [vytvořte bezplatný účet](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Požadavky

Ke konfiguraci integrace služby Azure AD s platformou produktivity ClickUp potřebujete následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční zkušební verzi [tady](https://azure.microsoft.com/pricing/free-trial/) .
* Předplatné ClickUp produktivity s povoleným jednotným přihlašováním pro platformu

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Platforma pro produktivitu ClickUp podporuje jednotné přihlašování na více **aktualizacích**

## <a name="adding-clickup-productivity-platform-from-the-gallery"></a>Přidání platformy produktivity ClickUp z Galerie

Pokud chcete nakonfigurovat integraci ClickUp produktivity platformy do Azure AD, musíte do seznamu spravovaných aplikací pro SaaS přidat ClickUpou produktivitu platformy z galerie.

**Pokud chcete do galerie přidat platformu ClickUp produktivity, proveďte následující kroky:**

1. V **[Azure Portal](https://portal.azure.com)** na levém navigačním panelu klikněte na ikonu **Azure Active Directory** .

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte na **podnikové aplikace** a vyberte možnost **všechny aplikace** .

    ![Okno podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **Nová aplikace** v horní části dialogového okna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **ClickUp produktivitu platformy**, vyberte **ClickUp produktivita platformy** z panelu výsledků a potom kliknutím na tlačítko **Přidat** přidejte aplikaci.

     ![ClickUp produktivní platforma v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD s platformou produktivity ClickUp na základě testovacího uživatele s názvem **Britta Simon**.
Aby jednotné přihlašování fungovalo, je potřeba zřídit vztah propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v rámci platformy ClickUp produktivity.

Pokud chcete konfigurovat a testovat jednotné přihlašování Azure AD pomocí platformy ClickUp produktivity, musíte dokončit tyto stavební bloky:

1. **[Nakonfigurujte jednotné přihlašování Azure AD](#configure-azure-ad-single-sign-on)** a Umožněte uživatelům používat tuto funkci.
2. **[Nakonfigurovat jednotné přihlašování pro ClickUp produktivitu platformy](#configure-clickup-productivity-platform-single-sign-on)** – ke konfiguraci nastavení jediného Sign-On na straně aplikace
3. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí Britta Simon.
4. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – pro povolení Britta Simon pro použití jednotného přihlašování Azure AD.
5. **[Vytvořte testovacího uživatele platformy pro ClickUp produktivitu](#create-clickup-productivity-platform-test-user)** – abyste měli protějšek Britta Simon na platformě ClickUp produktivity, která je propojená s reprezentací uživatele v Azure AD.
6. **[Otestujte jednotné přihlašování](#test-single-sign-on)** – ověřte, jestli konfigurace funguje.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace jednotného přihlašování Azure AD

V této části povolíte jednotné přihlašování Azure AD v Azure Portal.

Pokud chcete nakonfigurovat jednotné přihlašování Azure AD s platformou produktivity ClickUp, proveďte následující kroky:

1. V [Azure Portal](https://portal.azure.com/)na stránce integrace aplikací pro **produktivitu ClickUp** vyberte **jednotné přihlašování**.

    ![Konfigurovat odkaz jednotného přihlašování](common/select-sso.png)

2. V dialogovém okně **Vyberte metodu jednotného přihlašování** vyberte možnost režim **SAML/WS** , čímž povolíte jednotné přihlašování.

    ![Režim výběru jednotného přihlašování](common/select-saml-option.png)

3. Na stránce **nastavit jeden Sign-On s SAML** klikněte na **Upravit** ikona a otevře se základní dialogové okno **Konfigurace SAML** .

    ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. V části **základní konfigurace SAML** proveďte následující kroky:

    ![ClickUp informace o jednotném přihlašování pro doménu platformy a adresy URL](common/sp-identifier.png)

    a. Do textového pole **přihlašovací adresa URL** zadejte adresu URL: `https://app.clickup.com/login/sso`

    b. Do textového pole **identifikátor (ID entity)** zadejte adresu URL pomocí následujícího vzoru: `https://api.clickup.com/v1/team/<team_id>/microsoft`

    > [!NOTE]
    > Hodnota identifikátoru není reálné číslo. Aktualizujte tuto hodnotu skutečným identifikátorem, který je vysvětlen dále v tomto kurzu.

5. Na stránce **nastavit jeden Sign-On se** stránkou SAML klikněte v části **podpisový certifikát SAML** na Kopírovat tlačítko a zkopírujte **adresu URL federačních metadat aplikace** a uložte ji do svého počítače.

    ![Odkaz na stažení certifikátu](common/copy-metadataurl.png)

### <a name="configure-clickup-productivity-platform-single-sign-on"></a>Konfigurace ClickUp produktivity platformy s jednou Sign-On

1. V jiném okně webového prohlížeče se přihlaste do svého tenanta ClickUp produktivity vaší platformy jako správce.

2. Klikněte na **Profil uživatele**a pak vyberte **Nastavení**.

    ![Snímek obrazovky s vybraným ikonou nastavení zobrazuje tenant ClickUp produktivity.](./media/clickup-productivity-platform-tutorial/configure0.png)

    ![Snímek obrazovky se zobrazí nastavení.](./media/clickup-productivity-platform-tutorial/configure1.png)

3. V části zprostředkovatel jednotného Sign-On (SSO) vyberte **Microsoft**.

    ![Snímek obrazovky zobrazuje podokno ověřování s vybraným Microsoftem.](./media/clickup-productivity-platform-tutorial/configure2.png)

4. Na stránce **Konfigurovat jednotné přihlašování Microsoftu** proveďte následující kroky:

    ![Snímek obrazovky se zobrazí na stránce Konfigurovat jednotné přihlašování Microsoftu, kde můžete zkopírovat entitu I D a uložit federační metadata Azure U R L.](./media/clickup-productivity-platform-tutorial/configure3.png)

    a. Kliknutím na **Kopírovat** ZKOPÍRUJTE hodnotu ID entity a vložte ji do textového pole **identifikátor (ID entity)** v části **základní konfigurace SAML** v Azure Portal.
    
    b. V poli **Adresa URL federačních metadat Azure** vložte hodnotu adresy URL federačních metadat aplikace, kterou jste zkopírovali z Azure Portal, a pak klikněte na **Uložit**.

5. Instalaci dokončíte kliknutím na **ověřit u Microsoftu a dokončíte instalaci** a ověříte ho pomocí účtu Microsoft.

    ![Snímek obrazovky s tlačítkem ověřit s Microsoftem dokončit instalaci](./media/clickup-productivity-platform-tutorial/configure4.png)

### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD

Cílem této části je vytvořit testovacího uživatele v Azure Portal s názvem Britta Simon.

1. V Azure Portal v levém podokně vyberte možnost **Azure Active Directory**, vyberte možnost **Uživatelé**a potom vyberte možnost **Všichni uživatelé**.

    ![Odkazy "uživatelé a skupiny" a "Všichni uživatelé"](common/users.png)

2. V horní části obrazovky vyberte **Nový uživatel** .

    ![Tlačítko pro nového uživatele](common/new-user.png)

3. Ve vlastnostech uživatele proveďte následující kroky.

    ![Uživatelský dialog](common/user-properties.png)

    a. Do pole **název** zadejte **BrittaSimon**.
  
    b. Do pole **uživatelské jméno** zadejte **brittasimon \@ yourcompanydomain. extension.**  
    Například BrittaSimon@contoso.com.

    c. Zaškrtněte políčko **Zobrazit heslo** a pak zapište hodnotu, která se zobrazí v poli heslo.

    d. Klikněte na **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte Britta Simon pro použití jednotného přihlašování pomocí Azure tím, že udělíte přístup k platformě ClickUp produktivity.

1. V Azure Portal vyberte možnost **podnikové aplikace**, vyberte možnost **všechny aplikace**a pak vyberte **ClickUp produktivní platforma**.

    ![Okno Podnikové aplikace](common/enterprise-applications.png)

2. V seznamu aplikace vyberte **ClickUp produktivní platforma**.

    ![Odkaz na platformu pro produktivitu ClickUp v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **Uživatelé a skupiny**.

    ![Odkaz uživatelé a skupiny](common/users-groups-blade.png)

4. Klikněte na tlačítko **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Podokno přidat přiřazení](common/add-assign-user.png)

5. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **Britta Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.

6. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, pak v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.

7. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

### <a name="create-clickup-productivity-platform-test-user"></a>Vytvořit testovacího uživatele platformy pro ClickUp produktivitu

1. V jiném okně webového prohlížeče se přihlaste do svého tenanta ClickUp produktivity vaší platformy jako správce.

2. Klikněte na **Profil uživatele**a pak vyberte **lidé**.
   
    ![Snímek obrazovky se zobrazí jako tenant ClickUp produktivity.](./media/clickup-productivity-platform-tutorial/configure0.png)

    ![Snímek obrazovky se zobrazuje vybraný odkaz lidé.](./media/clickup-productivity-platform-tutorial/user1.png)

3. Do textového pole zadejte e-mailovou adresu uživatele a klikněte na **pozvat**.

    ![Snímek obrazovky se zobrazí v nastavení týmových uživatelů, kde můžete pozvat lidi e-mailem.](./media/clickup-productivity-platform-tutorial/user2.png)

    > [!NOTE]
    > Uživateli se zobrazí oznámení a musí přijmout pozvánku k aktivaci účtu.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí přístupového panelu.

Když kliknete na dlaždici ClickUp produktivita platformy na přístupovém panelu, měli byste se automaticky přihlásili k platformě ClickUp produktivity, pro kterou jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Další zdroje

- [Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory](./tutorial-list.md)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

- [Co je podmíněný přístup v Azure Active Directory?](../conditional-access/overview.md)