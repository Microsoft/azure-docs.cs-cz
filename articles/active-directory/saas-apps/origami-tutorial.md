---
title: 'Kurz: Azure Active Directory integrace s origami | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a origami.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/14/2019
ms.author: jeedes
ms.openlocfilehash: 02c79e8385c7a7e9d60a3dcbed603ca94cb1dc43
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/24/2020
ms.locfileid: "92522259"
---
# <a name="tutorial-azure-active-directory-integration-with-origami"></a>Kurz: Azure Active Directory integrace s origami

V tomto kurzu se dozvíte, jak integrovat origami s Azure Active Directory (Azure AD).
Integrace origami s Azure AD poskytuje následující výhody:

* Můžete kontrolovat v Azure AD, kteří mají přístup k origami.
* Můžete povolit, aby se vaši uživatelé automaticky přihlásili k origami (jednotné přihlašování) pomocí svých účtů Azure AD.
* Účty můžete spravovat v jednom centrálním umístění – Azure Portal.

Pokud chcete získat další podrobnosti o integraci aplikace SaaS s Azure AD, přečtěte si téma [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](../manage-apps/what-is-single-sign-on.md).
Pokud předplatné Azure ještě nemáte, napřed si [vytvořte bezplatný účet](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Předpoklady

Ke konfiguraci integrace služby Azure AD s origami potřebujete následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční zkušební verzi [tady](https://azure.microsoft.com/pricing/free-trial/) .
* Předplatné s povoleným Origamim jednotným přihlašováním

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Origami podporuje jednotné přihlašování iniciované v **SP**

## <a name="adding-origami-from-the-gallery"></a>Přidání origami z Galerie

Pokud chcete nakonfigurovat integraci origami do služby Azure AD, musíte přidat origami z Galerie do svého seznamu spravovaných aplikací SaaS.

**Pokud chcete přidat origami z Galerie, proveďte následující kroky:**

1. V **[Azure Portal](https://portal.azure.com)** na levém navigačním panelu klikněte na ikonu **Azure Active Directory** .

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte na **podnikové aplikace** a vyberte možnost **všechny aplikace** .

    ![Okno podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **Nová aplikace** v horní části dialogového okna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **origami**, vyberte **origami** z panelu výsledků a potom kliknutím na tlačítko **Přidat** přidejte aplikaci.

     ![Origami v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD pomocí origami na základě testovacího uživatele s názvem **Britta Simon**.
Aby jednotné přihlašování fungovalo, musí se zřídit vztah propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v origami.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí origami, musíte dokončit tyto stavební bloky:

1. **[Nakonfigurujte jednotné přihlašování Azure AD](#configure-azure-ad-single-sign-on)** a Umožněte uživatelům používat tuto funkci.
2. **[Nakonfigurujte jednotné přihlašování origami](#configure-origami-single-sign-on)** – ke konfiguraci nastavení jediného Sign-On na straně aplikace.
3. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí Britta Simon.
4. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – pro povolení Britta Simon pro použití jednotného přihlašování Azure AD.
5. **[Vytvoření origami Test User](#create-origami-test-user)** – pro Britta Simon v origami, který je propojený s reprezentací uživatele Azure AD.
6. **[Otestujte jednotné přihlašování](#test-single-sign-on)** – ověřte, jestli konfigurace funguje.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace jednotného přihlašování Azure AD

V této části povolíte jednotné přihlašování Azure AD v Azure Portal.

Pokud chcete nakonfigurovat jednotné přihlašování Azure AD pomocí origami, proveďte následující kroky:

1. V [Azure Portal](https://portal.azure.com/)na stránce integrace aplikací **origami** vyberte **jednotné přihlašování**.

    ![Konfigurovat odkaz jednotného přihlašování](common/select-sso.png)

2. V dialogovém okně **Vyberte metodu jednotného přihlašování** vyberte možnost režim **SAML/WS** , čímž povolíte jednotné přihlašování.

    ![Režim výběru jednotného přihlašování](common/select-saml-option.png)

3. Na stránce **nastavit jeden Sign-On s SAML** klikněte na **Upravit** ikona a otevře se základní dialogové okno **Konfigurace SAML** .

    ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. V části **základní konfigurace SAML** proveďte následující kroky:

    ![Informace o jednotném přihlašování v doméně origami a adresách URL](common/sp-signonurl.png)

    Do textového pole **přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru:  `https://live.origamirisk.com/origami/account/login?account=<companyname>`

    > [!NOTE]
    > Hodnota není reálné číslo. Aktualizujte hodnotu skutečnou adresou Sign-On. Pokud chcete získat hodnotu, obraťte se na [tým podpory klienta origami](https://wordpress.org/support/theme/origami) . Můžete se také podívat na vzory uvedené v části **základní konfigurace SAML** v Azure Portal.

5. Na stránce **nastavit jeden Sign-On se** stránkou SAML v části **podpisový certifikát SAML** klikněte na **Stáhnout** a Stáhněte si **certifikát (Base64)** z daných možností podle vašich požadavků a uložte ho do svého počítače.

    ![Odkaz na stažení certifikátu](common/certificatebase64.png)

6. V části **Nastavení origami** zkopírujte příslušné adresy URL podle vašich požadavků.

    ![Kopírovat adresy URL konfigurace](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor Azure AD

    c. Odhlašovací adresa URL

### <a name="configure-origami-single-sign-on"></a>Nakonfigurovat origami jednu Sign-On

1. Přihlaste se k účtu origami s právy správce.

2. V nabídce v horní části klikněte na **správce**.
   
    ![Snímek obrazovky zobrazující domovskou stránku origami s vybraným "admin".](./media/origami-tutorial/tutorial_origami_51.png)

3. Na stránce nastavení jednotného přihlašování proveďte následující kroky:
   
    ![Snímek obrazovky zobrazující stránku pro nastavení jednotného přihlašování s vybraným možností povolit jednotné přihlašování a zvýrazněná textová pole.](./media/origami-tutorial/tutorial_origami_531.png)

    a. Vyberte **Povolit jednotné přihlašování**.

    b. Do textového pole **Adresa URL přihlašovací stránky zprostředkovatele identity** vložte hodnotu **adresy URL pro přihlášení**, kterou jste zkopírovali z Azure Portal.

    c. Do textového pole **Adresa URL stránky** pro odhlášení zprostředkovatele identity vložte hodnotu **URL pro odhlášení**, kterou jste zkopírovali z Azure Portal.

    d. Kliknutím na tlačítko **Procházet** Nahrajte certifikát, který jste stáhli z Azure Portal.

    e. Klikněte na **Save Changes** (Uložit změny).

### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD 

Cílem této části je vytvořit testovacího uživatele v Azure Portal s názvem Britta Simon.

1. V Azure Portal v levém podokně vyberte možnost **Azure Active Directory**, vyberte možnost **Uživatelé**a potom vyberte možnost **Všichni uživatelé**.

    ![Odkazy "uživatelé a skupiny" a "Všichni uživatelé"](common/users.png)

2. V horní části obrazovky vyberte **Nový uživatel** .

    ![Tlačítko pro nového uživatele](common/new-user.png)

3. Ve vlastnostech uživatele proveďte následující kroky.

    ![Uživatelský dialog](common/user-properties.png)

    a. Do pole **název** zadejte **BrittaSimon**.
  
    b. Do pole typ **uživatelského jména****brittasimon@yourcompanydomain.extension**  
    Například BrittaSimon@contoso.com.

    c. Zaškrtněte políčko **Zobrazit heslo** a pak zapište hodnotu, která se zobrazí v poli heslo.

    d. Klikněte na **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte Britta Simon pro použití jednotného přihlašování pomocí Azure tím, že udělíte přístup k origami.

1. V Azure Portal vyberte **podnikové aplikace**, vyberte **všechny aplikace**a pak vyberte **origami**.

    ![Okno Podnikové aplikace](common/enterprise-applications.png)

2. V seznamu aplikace vyberte **origami**.

    ![Odkaz origami v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **Uživatelé a skupiny**.

    ![Odkaz uživatelé a skupiny](common/users-groups-blade.png)

4. Klikněte na tlačítko **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Podokno přidat přiřazení](common/add-assign-user.png)

5. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **Britta Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.

6. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, pak v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.

7. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

### <a name="create-origami-test-user"></a>Vytvořit testovacího uživatele origami

V této části vytvoříte uživatele s názvem Britta Simon v origami. 

1. Přihlaste se k účtu origami s právy správce.

2. V nabídce v horní části klikněte na **správce**.
   
    ![Snímek obrazovky zobrazující domovskou stránku účtu origami se zaškrtnutým "správcem".](./media/origami-tutorial/tutorial_origami_51.png)

3. V dialogovém okně **Uživatelé a zabezpečení** klikněte na **Uživatelé**.
   
    ![Snímek obrazovky, který zobrazuje dialogové okno Uživatelé a zabezpečení s vybranými možnostmi uživatelé](./media/origami-tutorial/tutorial_origami_54.png)

4. Klikněte na tlačítko **Přidat nového uživatele**.
   
    ![Snímek obrazovky, který zobrazuje vybrané tlačítko Přidat nového uživatele.](./media/origami-tutorial/tutorial_origami_55.png)

5. V dialogovém okně Přidat nového uživatele proveďte následující kroky:
   
    ![Snímek obrazovky zobrazující dialog Přidat nového uživatele se zvýrazněnými textovými poli "uživatelské jméno", "křestní jméno" a "příjmení".](./media/origami-tutorial/tutorial_origami_56.png)

    a. Do textového pole **uživatelské jméno** zadejte e-mail uživatele, jako je **brittasimon \@ contoso.com**.

    b. Do textového pole **heslo** zadejte heslo.

    c. Do textového pole **Potvrdit heslo** zadejte heslo znovu.

    d. Do textového pole **jméno a příjmení** zadejte jméno uživatele jako například **Britta**.

    e. Do textového pole **příjmení** zadejte jméno uživatele jako **Simon**.

    f. Klikněte na **Uložit**.
   
    ![Snímek obrazovky, který zobrazuje vybrané tlačítko Uložit.](./media/origami-tutorial/tutorial_origami_57.png)

6. Přiřaďte uživateli uživatelské **role** a **přístup klienta** . 
   
    ![Konfigurace jednoho Sign-On](./media/origami-tutorial/tutorial_origami_58.png)

### <a name="test-single-sign-on"></a>Test jednotného přihlašování 

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí přístupového panelu.

Když na přístupovém panelu kliknete na dlaždici origami, měli byste se automaticky přihlásit k origami, pro které jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Další zdroje

- [Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory](./tutorial-list.md)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

- [Co je podmíněný přístup v Azure Active Directory?](../conditional-access/overview.md)