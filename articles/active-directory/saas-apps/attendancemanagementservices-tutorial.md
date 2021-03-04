---
title: 'Kurz: Azure Active Directory integrace se službami pro správu docházky | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a službami pro správu docházky.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/15/2019
ms.author: jeedes
ms.openlocfilehash: 1f404d3613f9de8daadc4bb2ceb39282cf3b619e
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/03/2021
ms.locfileid: "101688939"
---
# <a name="tutorial-azure-active-directory-integration-with-attendance-management-services"></a>Kurz: Azure Active Directory integrace se službami pro správu docházky

V tomto kurzu se dozvíte, jak integrovat služby pro správu docházky s Azure Active Directory (Azure AD).
Integrace služeb pro správu docházky se službou Azure AD poskytuje následující výhody:

* Můžete kontrolovat v Azure AD, kteří mají přístup ke službám pro správu docházky.
* Můžete povolit, aby se vaši uživatelé automaticky přihlásili ke službám pro správu docházky (jednotné přihlašování) se svými účty Azure AD.
* Účty můžete spravovat v jednom centrálním umístění – Azure Portal.

Pokud chcete získat další podrobnosti o integraci aplikace SaaS s Azure AD, přečtěte si téma [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](../manage-apps/what-is-single-sign-on.md).
Pokud předplatné Azure ještě nemáte, napřed si [vytvořte bezplatný účet](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Požadavky

Ke konfiguraci integrace služby Azure AD se službami pro správu docházky potřebujete následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat [bezplatný účet](https://azure.microsoft.com/free/) .
* Předplatné s povoleným jednotným přihlašováním služeb pro správu docházky

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Služba pro správu docházky podporuje jednotné přihlašování iniciované v **SP**

## <a name="adding-attendance-management-services-from-the-gallery"></a>Přidávají se služby pro správu docházky z galerie.

Pokud chcete nakonfigurovat integraci služeb správy docházky do Azure AD, musíte do seznamu spravovaných aplikací SaaS přidat služby pro správu docházky z galerie.

**Pokud chcete do galerie přidat služby pro správu docházky, proveďte následující kroky:**

1. V **[Azure Portal](https://portal.azure.com)** na levém navigačním panelu klikněte na ikonu **Azure Active Directory** .

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte na **podnikové aplikace** a vyberte možnost **všechny aplikace** .

    ![Okno podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **Nová aplikace** v horní části dialogového okna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **služby pro správu docházky**, vyberte **služby pro správu docházky** z panelu výsledků a potom kliknutím na tlačítko **Přidat** přidejte aplikaci.

    ![Služby pro správu docházky v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD pomocí služeb správy docházky na základě testovacího uživatele s názvem **Britta Simon**.
Aby se jednotné přihlašování fungovalo, je potřeba zřídit vztah propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v rámci služby pro správu docházky.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí služeb pro správu docházky, musíte dokončit tyto stavební bloky:

1. **[Nakonfigurujte jednotné přihlašování Azure AD](#configure-azure-ad-single-sign-on)** a Umožněte uživatelům používat tuto funkci.
2. **[Nakonfigurovat jednotné přihlašování pro služby správy docházky](#configure-attendance-management-services-single-sign-on)** – ke konfiguraci nastavení jediného Sign-On na straně aplikace
3. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí Britta Simon.
4. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – pro povolení Britta Simon pro použití jednotného přihlašování Azure AD.
5. **[Vytvořte testovacího uživatele služby pro správu docházky](#create-attendance-management-services-test-user)** – pro Britta Simon ve službách pro správu docházky, které jsou propojené s reprezentací uživatele v Azure AD.
6. **[Otestujte jednotné přihlašování](#test-single-sign-on)** – ověřte, jestli konfigurace funguje.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace jednotného přihlašování Azure AD

V této části povolíte jednotné přihlašování Azure AD v Azure Portal.

Pokud chcete nakonfigurovat jednotné přihlašování Azure AD pomocí služeb pro správu docházky, proveďte následující kroky:

1. V [Azure Portal](https://portal.azure.com/)na stránce integrace aplikace **služby pro správu docházky** vyberte **jednotné přihlašování**.

    ![Konfigurovat odkaz jednotného přihlašování](common/select-sso.png)

2. V dialogovém okně **Vyberte metodu jednotného přihlašování** vyberte možnost režim **SAML/WS** , čímž povolíte jednotné přihlašování.

    ![Režim výběru jednotného přihlašování](common/select-saml-option.png)

3. Na stránce **nastavit jeden Sign-On s SAML** klikněte na **Upravit** ikona a otevře se základní dialogové okno **Konfigurace SAML** .

    ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. V části **základní konfigurace SAML** proveďte následující kroky:

    ![Informace o jednotném přihlašování k doméně a adresám URL služby správy docházky](common/sp-identifier.png)

    a. Do textového pole **přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru: `https://id.obc.jp/<tenant information >/`

    b. Do textového pole **identifikátor (ID entity)** zadejte adresu URL pomocí následujícího vzoru: `https://id.obc.jp/<tenant information >/`

    > [!NOTE]
    > Tyto hodnoty nejsou reálné. Aktualizujte tyto hodnoty skutečným přihlašovacím jménem a identifikátorem URL. Chcete-li získat tyto hodnoty, obraťte se na [tým podpory klientů služby správy docházky](https://www.obcnet.jp/) . Můžete se také podívat na vzory uvedené v části **základní konfigurace SAML** v Azure Portal.

5. Na stránce **nastavit jeden Sign-On se** stránkou SAML v části **podpisový certifikát SAML** klikněte na **Stáhnout** a Stáhněte si **certifikát (Base64)** z daných možností podle vašich požadavků a uložte ho do svého počítače.

    ![Odkaz na stažení certifikátu](common/certificatebase64.png)

6. V části **nastavení služby pro správu docházky** zkopírujte příslušné adresy URL podle vašich požadavků.

    ![Kopírovat adresy URL konfigurace](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor Azure AD

    c. Odhlašovací adresa URL

### <a name="configure-attendance-management-services-single-sign-on"></a>Konfigurovat službu pro správu docházky jeden Sign-On

1. V jiném okně prohlížeče se přihlaste k vašemu firemnímu webu služby pro správu docházky jako správce.

1. V **části Správa zabezpečení** klikněte na **ověřování SAML** .

    ![Snímek obrazovky ukazuje ověřování SAML vybrané na stránce, která používá jiné znaky než latinku.](./media/attendancemanagementservices-tutorial/user1.png)

1. Proveďte tyto kroky:

    ![Snímek obrazovky se zobrazí okno, kde můžete provádět úkoly popsané v tomto kroku.](./media/attendancemanagementservices-tutorial/user2.png)

    a. Vyberte možnost **použít ověřování SAML**.

    b. Do textového pole **identifikátoru** vložte hodnotu hodnoty **identifikátoru Azure AD** , kterou jste zkopírovali z Azure Portal.

    c. Do textového pole **Adresa URL koncového bodu ověřování** vložte hodnotu hodnoty **URL pro přihlášení** , kterou jste zkopírovali z Azure Portal.

    d. Kliknutím na **Vybrat soubor** odešlete certifikát, který jste stáhli ze služby Azure AD.

    e. Vyberte možnost **zakázat ověřování hesla**.

    f. Klikněte na **registrace** .

### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD

Cílem této části je vytvořit testovacího uživatele v Azure Portal s názvem Britta Simon.

1. V Azure Portal v levém podokně vyberte možnost **Azure Active Directory**, vyberte možnost **Uživatelé** a potom vyberte možnost **Všichni uživatelé**.

    ![Odkazy "uživatelé a skupiny" a "Všichni uživatelé"](common/users.png)

2. V horní části obrazovky vyberte **Nový uživatel** .

    ![Tlačítko pro nového uživatele](common/new-user.png)

3. Ve vlastnostech uživatele proveďte následující kroky.

    ![Uživatelský dialog](common/user-properties.png)

    a. Do pole **název** zadejte **BrittaSimon**.
  
    b. Do pole **uživatelské jméno** zadejte `brittasimon@yourcompanydomain.extension` . Například BrittaSimon@contoso.com.

    c. Zaškrtněte políčko **Zobrazit heslo** a pak zapište hodnotu, která se zobrazí v poli heslo.

    d. Klikněte na **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte Britta Simon pro použití jednotného přihlašování pomocí Azure tím, že udělíte přístup ke službám pro správu docházky.

1. V Azure Portal vyberte možnost **podnikové aplikace**, vyberte možnost **všechny aplikace** a pak vyberte možnost **služby pro správu docházky**.

    ![Okno Podnikové aplikace](common/enterprise-applications.png)

2. V seznamu aplikace vyberte možnost **služby pro správu docházky**.

    ![Odkaz služby pro správu docházky v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **Uživatelé a skupiny**.

    ![Odkaz uživatelé a skupiny](common/users-groups-blade.png)

4. Klikněte na tlačítko **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Podokno přidat přiřazení](common/add-assign-user.png)

5. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **Britta Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.

6. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, pak v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.

7. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

### <a name="create-attendance-management-services-test-user"></a>Vytvořit testovacího uživatele služeb pro správu docházky

Aby se uživatelé Azure AD mohli přihlašovat ke službám pro správu docházky, musí se zřídit ve službách pro správu docházky. V případě služeb pro správu docházky je zřizování ručním úkolem.

**Chcete-li zřídit uživatelský účet, proveďte následující kroky:**

1. Přihlaste se ke svému firemnímu webu služby pro správu docházky jako správce.

1. V **části Správa zabezpečení** klikněte na **Správa uživatelů** .

    ![Snímek obrazovky se zobrazí Správa uživatelů vybraná na stránce, která používá jiné znaky než latinku.](./media/attendancemanagementservices-tutorial/user5.png)

1. Klikněte na **Nová pravidla přihlášení**.

    ![Snímek obrazovky znázorňující výběr možnosti plus](./media/attendancemanagementservices-tutorial/user3.png)

1. V části **informace o OBCiD** proveďte následující kroky:

    ![Snímek obrazovky se zobrazí okno, kde můžete provádět popsané úkoly.](./media/attendancemanagementservices-tutorial/user4.png)

    a. Do textového pole **OBCiD** zadejte e-maily uživatele, jako je například `BrittaSimon@contoso.com` .

    b. Do textového pole **heslo** zadejte heslo uživatele.

    c. Klikněte na **registrace** .

### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí přístupového panelu.

Po kliknutí na dlaždici služby pro správu docházky na přístupovém panelu byste měli být automaticky přihlášeni ke službám pro správu docházky, pro které jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Další materiály

- [Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory](./tutorial-list.md)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

- [Co je podmíněný přístup v Azure Active Directory?](../conditional-access/overview.md)