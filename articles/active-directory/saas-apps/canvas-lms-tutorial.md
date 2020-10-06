---
title: 'Kurz: Azure Active Directory integrace s plátnem | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a plátnem.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/02/2018
ms.author: jeedes
ms.openlocfilehash: 6c75103f74ee648c46b805df9495bc668ef4f5bd
ms.sourcegitcommit: 6a4687b86b7aabaeb6aacdfa6c2a1229073254de
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/06/2020
ms.locfileid: "91761562"
---
# <a name="tutorial-azure-active-directory-integration-with-canvas"></a>Kurz: Azure Active Directory integrace s plátnem

V tomto kurzu se naučíte integrovat plátno s Azure Active Directory (Azure AD).
Integrace plátna se službou Azure AD poskytuje následující výhody:

* Můžete kontrolovat v Azure AD, kteří mají přístup k plátnu.
* Uživatelům můžete povolit automatické přihlášení k plátnu (jednotné přihlašování) pomocí svých účtů Azure AD.
* Účty můžete spravovat v jednom centrálním umístění – Azure Portal.

Pokud chcete získat další podrobnosti o integraci aplikace SaaS s Azure AD, přečtěte si téma [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud předplatné Azure ještě nemáte, napřed si [vytvořte bezplatný účet](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Předpoklady

Ke konfiguraci integrace služby Azure AD s plátnem potřebujete následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční zkušební verzi [tady](https://azure.microsoft.com/pricing/free-trial/) .
* Odběr povoleného jednotného přihlašování plátna

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Plátno podporuje jednotné přihlašování na webu **SP**

## <a name="adding-canvas-from-the-gallery"></a>Přidání plátna z Galerie

Pokud chcete nakonfigurovat integraci plátna do služby Azure AD, musíte přidat plátno z Galerie do seznamu spravovaných aplikací SaaS.

**Pokud chcete přidat plátno z Galerie, proveďte následující kroky:**

1. V **[Azure Portal](https://portal.azure.com)** na levém navigačním panelu klikněte na ikonu **Azure Active Directory** .

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte na **podnikové aplikace** a vyberte možnost **všechny aplikace** .

    ![Okno podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **Nová aplikace** v horní části dialogového okna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **plátno**, vyberte **plátno** z panelu výsledků a potom kliknutím na tlačítko **Přidat** přidejte aplikaci.

     ![Plátno v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD pomocí plátna na základě testovacího uživatele s názvem **Britta Simon**.
Aby jednotné přihlašování fungovalo, musí být navázán odkaz na vztah mezi uživatelem služby Azure AD a souvisejícím uživatelem na plátně.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí plátna, musíte dokončit tyto stavební bloky:

1. **[Nakonfigurujte jednotné přihlašování Azure AD](#configure-azure-ad-single-sign-on)** a Umožněte uživatelům používat tuto funkci.
2. **[Nakonfigurujte jednotné přihlašování na plátně](#configure-canvas-single-sign-on)** a nakonfigurujte nastavení jednotného přihlašování na straně aplikace.
3. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí Britta Simon.
4. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – pro povolení Britta Simon pro použití jednotného přihlašování Azure AD.
5. **[Vytvořte si testovacího uživatele plátna](#create-canvas-test-user)** , abyste měli protějšek Britta Simon na plátně, která je propojená s reprezentací uživatele v Azure AD.
6. **[Otestujte jednotné přihlašování](#test-single-sign-on)** – ověřte, jestli konfigurace funguje.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace jednotného přihlašování Azure AD

V této části povolíte jednotné přihlašování Azure AD v Azure Portal.

Pokud chcete nakonfigurovat jednotné přihlašování pomocí služby Azure AD, proveďte následující kroky:

1. V [Azure Portal](https://portal.azure.com/)na stránce integrace aplikace na **plátně** vyberte **jednotné přihlašování**.

    ![Konfigurovat odkaz jednotného přihlašování](common/select-sso.png)

2. V dialogovém okně **Vyberte metodu jednotného přihlašování** vyberte možnost režim **SAML/WS** , čímž povolíte jednotné přihlašování.

    ![Režim výběru jednotného přihlašování](common/select-saml-option.png)

3. Na stránce **nastavit jednotné přihlašování pomocí SAML** klikněte na **Upravit** ikona a otevře se základní dialogové okno **Konfigurace SAML** .

    ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. V části **základní konfigurace SAML** proveďte následující kroky:

    ![Informace o jednotném přihlašování domény plátna a adresy URL](common/sp-identifier.png)

    a. Do textového pole **přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru: `https://<tenant-name>.instructure.com`

    b. Do textového pole **identifikátor (ID entity)** zadejte adresu URL pomocí následujícího vzoru: `https://<tenant-name>.instructure.com/saml2`

    > [!NOTE]
    > Tyto hodnoty nejsou reálné. Aktualizujte tyto hodnoty skutečným přihlašovacím jménem a identifikátorem URL. Pro získání těchto hodnot [tým podpory pro klienty](https://community.canvaslms.com/community/help) s kontaktními osobami. Můžete se také podívat na vzory uvedené v části **základní konfigurace SAML** v Azure Portal.

5. V části **podpisový certifikát SAML** kliknutím na tlačítko **Upravit** otevřete dialogové okno **podpisový certifikát SAML** .

    ![Upravit podpisový certifikát SAML](common/edit-certificate.png)

6. V části **podpisový certifikát SAML** zkopírujte **kryptografický otisk** a uložte ho do svého počítače.

    ![Kopírovat hodnotu kryptografického otisku](common/copy-thumbprint.png)

7. V části **nastavit plátno** zkopírujte příslušné adresy URL podle vašich požadavků.

    ![Kopírovat adresy URL konfigurace](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor Azure AD

    c. Odhlašovací adresa URL

### <a name="configure-canvas-single-sign-on"></a>Konfigurace jednotného přihlašování na plátně

1. V jiném okně webového prohlížeče se přihlaste k webu plátna jako správce.

2. Přejít na **kurzy \> spravované účty \> Microsoft**.

    ![Plátno](./media/canvas-lms-tutorial/ic775990.png "Plátno")

3. V navigačním podokně na levé straně vyberte **ověřování**a pak klikněte na **Přidat novou konfiguraci SAML**.

    ![Authentication](./media/canvas-lms-tutorial/ic775991.png "Authentication")

4. Na stránce aktuální integrace proveďte následující kroky:

    ![Aktuální integrace](./media/canvas-lms-tutorial/ic775992.png "Aktuální integrace")

    a. Do textového pole **ID entity IDP** vložte hodnotu **identifikátoru služby Azure AD** , který jste zkopírovali z Azure Portal.

    b. Do textového pole **Adresa URL pro přihlášení** vložte hodnotu **adresy URL pro přihlášení** , kterou jste zkopírovali z Azure Portal.

    c. Do textového pole **Adresa URL** pro odhlášení vložte hodnotu **adresy URL pro odhlášení** , kterou jste zkopírovali z Azure Portal.

    d. Do textového pole **změnit odkaz na heslo** vložte hodnotu **Adresa URL pro změnu hesla** , kterou jste zkopírovali z Azure Portal.

    e. Do textového pole **otisku certifikátu** vložte hodnotu **kryptografického otisku** certifikátu, který jste zkopírovali z Azure Portal.

    f. V seznamu **atribut přihlášení** vyberte **NameId**.

    například V seznamu **Formát identifikátoru** vyberte možnost **EmailAddress**.

    h. Klikněte na **Uložit nastavení ověřování**.

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

    d. Klikněte na možnost **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte Britta Simon pro použití jednotného přihlašování pomocí Azure tím, že udělíte přístup k plátnu.

1. V Azure Portal vyberte **podnikové aplikace**, vyberte **všechny aplikace**a pak vyberte **plátno**.

    ![Okno Podnikové aplikace](common/enterprise-applications.png)

2. V seznamu aplikace vyberte **plátno**.

    ![Odkaz na plátno v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **Uživatelé a skupiny**.

    ![Odkaz uživatelé a skupiny](common/users-groups-blade.png)

4. Klikněte na tlačítko **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Podokno přidat přiřazení](common/add-assign-user.png)

5. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **Britta Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.

6. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, pak v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.

7. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

### <a name="create-canvas-test-user"></a>Vytvořit testovacího uživatele plátna

Aby se uživatelé Azure AD mohli přihlásit k plátnu, musí se zřídit do plátna. V případě plátna je zřizování uživatelů ručním úkolem.

**Chcete-li zřídit uživatelský účet, proveďte následující kroky:**

1. Přihlaste se k tenantovi **plátna** .

2. Přejít na **kurzy \> spravované účty \> Microsoft**.

   ![Plátno](./media/canvas-lms-tutorial/ic775990.png "Plátno")

3. Klikněte na **Uživatelé**.

   ![Snímek obrazovky s vybranými uživateli zobrazí nabídku plátna.](./media/canvas-lms-tutorial/ic775995.png "Uživatelé")

4. Klikněte na tlačítko **Přidat nového uživatele**.

   ![Snímek obrazovky se zobrazí tlačítko Přidat nového uživatele.](./media/canvas-lms-tutorial/ic775996.png "Uživatelé")

5. Na stránce Přidat nový uživatelský dialog proveďte následující kroky:

   ![Přidat uživatele](./media/canvas-lms-tutorial/ic775997.png "Přidání uživatele")

   a. Do textového pole **celé jméno** zadejte jméno uživatele, jako je **BrittaSimon**.

   b. Do textového pole **e-mail** zadejte e-maily uživatele, jako je **brittasimon \@ contoso.com**.

   c. Do textového pole pro **přihlášení** zadejte e-mailovou adresu Azure AD, jako je **brittasimon \@ contoso.com**.

   d. Vyberte **Odeslat e-mail uživateli o vytvoření tohoto účtu**.

   e. Klikněte na **Add User** (Přidat uživatele).

> [!NOTE]
> K zřizování uživatelských účtů Azure AD můžete použít jakékoli nástroje pro vytváření uživatelských účtů nebo rozhraní API, které poskytuje plátno.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí přístupového panelu.

Když kliknete na dlaždici plátno na přístupovém panelu, měli byste být automaticky přihlášeni k plátnu, pro které jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další materiály

- [Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

