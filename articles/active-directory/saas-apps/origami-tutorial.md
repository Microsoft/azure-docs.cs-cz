---
title: 'Kurz: Integrace služby Azure Active Directory s origami | Dokumenty společnosti Microsoft'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Origami.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: a28bb0ba-b564-46ba-accc-e587699295d4
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/14/2019
ms.author: jeedes
ms.openlocfilehash: fd347f4eb5f77dacc3c9fd61d0e885e9b3ee7959
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "67095637"
---
# <a name="tutorial-azure-active-directory-integration-with-origami"></a>Kurz: Integrace služby Azure Active Directory s origami

V tomto kurzu se dozvíte, jak integrovat Origami s Azure Active Directory (Azure AD).
Integrace origami s Azure AD vám poskytuje následující výhody:

* Můžete řídit ve službě Azure AD, který má přístup k Origami.
* Uživatelům můžete povolit automatické přihlášení k origami (jednotné přihlašování) pomocí jejich účtů Azure AD.
* Své účty můžete spravovat v jednom centrálním umístění – na portálu Azure.

Pokud se chcete dozvědět více podrobností o integraci aplikací SaaS s Azure AD, přečtěte [si, co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud nemáte předplatné Azure, [vytvořte si bezplatný účet,](https://azure.microsoft.com/free/) než začnete.

## <a name="prerequisites"></a>Požadavky

Chcete-li nakonfigurovat integraci Azure AD s Origami, potřebujete následující položky:

* Předplatné Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční zkušební [verzi zde](https://azure.microsoft.com/pricing/free-trial/)
* Origami jednotné přihlášení povoleno předplatné

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Origami podporuje **SP** zahájila SSO

## <a name="adding-origami-from-the-gallery"></a>Přidání Origami z galerie

Chcete-li nakonfigurovat integraci origami do Azure AD, musíte přidat Origami z galerie do seznamu spravovaných aplikací SaaS.

**Chcete-li přidat Origami z galerie, proveďte následující kroky:**

1. Na **[portálu Azure](https://portal.azure.com)** klikněte na levém navigačním panelu na ikonu **Služby Azure Active Directory.**

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte do **podnikových aplikací** a pak vyberte možnost **Všechny aplikace.**

    ![Okno Aplikace Enterprise](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klepněte na tlačítko **Nová aplikace** v horní části dialogového okna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **Origami**, z panelu výsledků vyberte **origami** a pak klepněte na tlačítko **Přidat** a přidejte aplikaci.

     ![Origami v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD pomocí služby Origami na základě testovacího uživatele s názvem **Britta Simon**.
Aby jednotné přihlašování fungovalo, musí být vytvořen vztah propojení mezi uživatelem Azure AD a souvisejícím uživatelem v origami.

Chcete-li nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí služby Origami, musíte dokončit následující stavební bloky:

1. **[Nakonfigurujte azure ad jednotné přihlašování](#configure-azure-ad-single-sign-on)** – aby vaši uživatelé mohli používat tuto funkci.
2. **[Konfigurace Origami Single Sign-On](#configure-origami-single-sign-on)** - pro konfiguraci nastavení jednotného přihlášení na straně aplikace.
3. **[Vytvořte uživatele testu Azure AD](#create-an-azure-ad-test-user)** – k testování jednotného přihlášení Azure AD s Brittou Simonovou.
4. **[Přiřaďte testovacímu uživateli Azure AD](#assign-the-azure-ad-test-user)** – chcete-li Britta Simon ové povolit použití jednotného přihlášení azure ad.
5. **[Vytvořte origami test uživatele](#create-origami-test-user)** - mít protějšek Britta Simon v Origami, který je propojen s Reprezentací Azure AD uživatele.
6. **[Otestujte jednotné přihlašování](#test-single-sign-on)** - chcete-li ověřit, zda konfigurace funguje.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace jednotného přihlašování Azure AD

V této části povolíte jednotné přihlašování Azure AD na webu Azure Portal.

Chcete-li nakonfigurovat jednotné přihlašování Azure AD pomocí služby Origami, proveďte následující kroky:

1. Na [portálu Azure](https://portal.azure.com/)na stránce integrace aplikací **Origami** vyberte **Jednotné přihlašování**.

    ![Konfigurace odkazu pro jednotné přihlášení](common/select-sso.png)

2. V **dialogovém okně Vybrat metodu jednotného přihlašování** vyberte režim **SAML/WS-Fed,** abyste povolili jednotné přihlašování.

    ![Režim výběru jednotného přihlášení](common/select-saml-option.png)

3. Na stránce **Nastavit jednotné přihlašování pomocí saml** kliknutím na ikonu **Upravit** otevřete dialogové okno Základní **konfigurace SAML.**

    ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. V části **Základní konfigurace SAML** proveďte následující kroky:

    ![Origami Domény a adresy URL jednotné přihlašovací informace](common/sp-signonurl.png)

    Do textového pole **Přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru:`https://live.origamirisk.com/origami/account/login?account=<companyname>`

    > [!NOTE]
    > Hodnota není skutečná. Aktualizujte hodnotu skutečnou přihlašovací adresou URL. Obraťte se na [tým podpory klienta Origami](https://wordpress.org/support/theme/origami) získat hodnotu. Můžete také odkazovat na vzory uvedené v části **Základní konfigurace SAML** na webu Azure Portal.

5. Na stránce **Nastavit jednotné přihlašování pomocí saml** klikněte v části **Podpisový certifikát SAML** na **Stáhnout** a stáhněte si **certifikát (Base64)** z daných možností podle vašeho požadavku a uložte jej do počítače.

    ![Odkaz ke stažení certifikátu](common/certificatebase64.png)

6. V části **Nastavit origami** zkopírujte příslušnou adresu URL podle vašeho požadavku.

    ![Kopírování konfiguračních adres URL](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor azure reklamy

    c. Adresa URL odhlášení

### <a name="configure-origami-single-sign-on"></a>Konfigurace jednotného přihlášení origami

1. Přihlaste se k účtu Origami s administrátorskými právy.

2. V horní nabídce klikněte na **Admin**.
   
    ![Konfigurace jednotného přihlašování](./media/origami-tutorial/tutorial_origami_51.png)

3. Na stránce dialogového okna Při jednom přihlášení proveďte následující kroky:
   
    ![Konfigurace jednotného přihlašování](./media/origami-tutorial/tutorial_origami_531.png)

    a. Vyberte **možnost Povolit jednotné přihlašování**.

    b. V textovém poli **url přihlašovací stránky poskytovatele identity vložte** hodnotu přihlašovací adresy **URL**, kterou jste zkopírovali z webu Azure Portal.

    c. V textovém poli **Url stránky odhlášení poskytovatele identity** vložte hodnotu **adresy URL odhlášení**, kterou jste zkopírovali z webu Azure Portal.

    d. Kliknutím na **Procházet** nahrajte certifikát, který jste stáhli z webu Azure Portal.

    e. Klikněte na **Save Changes** (Uložit změny).

### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD 

Cílem této části je vytvořit testovacího uživatele na webu Azure portal s názvem Britta Simon.

1. Na webu Azure Portal v levém podokně vyberte **Azure Active Directory**, vyberte **Uživatelé**a pak vyberte **Všichni uživatelé**.

    ![Odkazy "Uživatelé a skupiny" a "Všichni uživatelé"](common/users.png)

2. V horní části obrazovky vyberte **Nový uživatel.**

    ![Tlačítko nového uživatele](common/new-user.png)

3. Ve vlastnostech User proveďte následující kroky.

    ![Dialogové okno Uživatel](common/user-properties.png)

    a. Do pole **Název** zadejte **BrittaSimon**.
  
    b. V poli **Uživatelské jméno** typ pole**brittasimon@yourcompanydomain.extension**  
    Například BrittaSimon@contoso.com.

    c. Zaškrtněte **políčko Zobrazit heslo** a poznamenejte si hodnotu, která se zobrazí v poli Heslo.

    d. Klikněte na **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte Britta Simon používat Azure jednotné přihlášení udělením přístupu k Origami.

1. Na portálu Azure vyberte **Podnikové aplikace**, vyberte **Všechny aplikace**a pak vyberte **Origami**.

    ![Okno podnikových aplikací](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **Možnost Origami**.

    ![Odkaz Origami v seznamu Aplikací](common/all-applications.png)

3. V nabídce vlevo vyberte **Možnost Uživatelé a skupiny**.

    ![Odkaz "Uživatelé a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **Přidat uživatele** a v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny.**

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V dialogovém okně **Uživatelé a skupiny** vyberte **brittu Simonovou** v seznamu Uživatelé a klikněte na tlačítko **Vybrat** v dolní části obrazovky.

6. Pokud očekáváte libovolnou hodnotu role v kontrolním výrazu SAML, vyberte v dialogovém okně **Vybrat roli** příslušnou roli pro uživatele ze seznamu a klepněte na tlačítko **Vybrat** v dolní části obrazovky.

7. V dialogovém okně **Přidat přiřazení** klepněte na tlačítko **Přiřadit.**

### <a name="create-origami-test-user"></a>Vytvořit Origami test uživatele

V této části vytvoříte uživatele s názvem Britta Simon v Origami. 

1. Přihlaste se k účtu Origami s administrátorskými právy.

2. V horní nabídce klikněte na **Admin**.
   
    ![Konfigurace jednotného přihlašování](./media/origami-tutorial/tutorial_origami_51.png)

3. V dialogovém okně **Uživatelé a zabezpečení** klepněte na položku **Uživatelé**.
   
    ![Konfigurace jednotného přihlašování](./media/origami-tutorial/tutorial_origami_54.png)

4. Klepněte na tlačítko **Přidat nového uživatele**.
   
    ![Konfigurace jednotného přihlašování](./media/origami-tutorial/tutorial_origami_55.png)

5. V dialogovém okně Přidat nového uživatele proveďte následující kroky:
   
    ![Konfigurace jednotného přihlašování](./media/origami-tutorial/tutorial_origami_56.png)

    a. Do textového pole **Uživatelské jméno** zadejte e-mail uživatele, jako **je brittasimon\@contoso.com**.

    b. Do textového pole **Heslo** zadejte heslo.

    c. Do textového pole **Potvrdit heslo** zadejte heslo znovu.

    d. Do textového pole **Křestní jméno** zadejte křestní jméno uživatele, jako **je Britta**.

    e. Do textového pole **Příjmení** zadejte příjmení uživatele, jako je **Simon**.

    f. Klikněte na **Uložit**.
   
    ![Konfigurace jednotného přihlašování](./media/origami-tutorial/tutorial_origami_57.png)

6. Přiřaďte uživateli **role uživatele** a **přístup klienta.** 
   
    ![Konfigurace jednotného přihlašování](./media/origami-tutorial/tutorial_origami_58.png)

### <a name="test-single-sign-on"></a>Test jednotného přihlašování 

V této části otestujete konfiguraci jednotného přihlášení Azure AD pomocí přístupového panelu.

Po kliknutí na dlaždici Origami na přístupovém panelu, měli byste být automaticky přihlášeni k Origami, pro které nastavíte sso. Další informace o přístupovém panelu naleznete [v tématu Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další zdroje

- [Seznam výukových programů o integraci aplikací SaaS s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup ve službě Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

