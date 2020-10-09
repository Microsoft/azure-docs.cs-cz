---
title: 'Kurz: Azure Active Directory integrace s jmenovitým | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a konkrétně.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/04/2019
ms.author: jeedes
ms.openlocfilehash: 00ddd61209af6e0d16b7f7579a6e8729cde0cdf0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "88552462"
---
# <a name="tutorial-azure-active-directory-integration-with-namely"></a>Kurz: Azure Active Directory integrace s konkrétně

V tomto kurzu se naučíte, jak integrovat konkrétně s Azure Active Directory (Azure AD).
Integrace, konkrétně s Azure AD, poskytuje následující výhody:

* Můžete řídit v Azure AD, který má přístup k konkrétně.
* Uživatelům můžete povolit, aby se k účtům Azure AD automaticky přihlásili, a to konkrétně pomocí jednotného přihlašování.
* Účty můžete spravovat v jednom centrálním umístění – Azure Portal.

Pokud chcete získat další podrobnosti o integraci aplikace SaaS s Azure AD, přečtěte si téma [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud předplatné Azure ještě nemáte, napřed si [vytvořte bezplatný účet](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Požadavky

Ke konfiguraci integrace služby Azure AD s konkrétně potřebujete následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční zkušební verzi [tady](https://azure.microsoft.com/pricing/free-trial/) .
* Konkrétně odběr s povoleným jednotným přihlašováním

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Konkrétně podporuje jednotné přihlašování iniciované v **SP**

## <a name="adding-namely-from-the-gallery"></a>Přidání konkrétně z Galerie

Pokud chcete nakonfigurovat integraci konkrétně do služby Azure AD, musíte přidat konkrétně z Galerie do svého seznamu spravovaných aplikací SaaS.

**Chcete-li přidat konkrétně z Galerie, proveďte následující kroky:**

1. V **[Azure Portal](https://portal.azure.com)** na levém navigačním panelu klikněte na ikonu **Azure Active Directory** .

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte na **podnikové aplikace** a vyberte možnost **všechny aplikace** .

    ![Okno podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **Nová aplikace** v horní části dialogového okna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **konkrétně**, Select **konkrétně** z panelu výsledků a potom kliknutím na tlačítko **Přidat** přidejte aplikaci.

     ![Konkrétně v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD pomocí konkrétně na základě testovacího uživatele s názvem **Britta Simon**.
Aby jednotné přihlašování fungovalo, musí být navázán odkaz na vztah mezi uživatelem služby Azure AD a souvisejícím uživatelem v systému.

Ke konfiguraci a testování jednotného přihlašování Azure AD pomocí konkrétně je potřeba dokončit následující stavební bloky:

1. **[Nakonfigurujte jednotné přihlašování Azure AD](#configure-azure-ad-single-sign-on)** a Umožněte uživatelům používat tuto funkci.
2. **[Nakonfigurujte konkrétně jednotné přihlašování](#configure-namely-single-sign-on)** , abyste na straně aplikace nakonfigurovali nastavení jednoho Sign-On.
3. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí Britta Simon.
4. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – pro povolení Britta Simon pro použití jednotného přihlašování Azure AD.
5. **[Vytvořte konkrétně testovacího uživatele](#create-namely-test-user)** , který bude mít protějšek Britta Simon v, konkrétně propojený s reprezentací uživatele Azure AD.
6. **[Otestujte jednotné přihlašování](#test-single-sign-on)** – ověřte, jestli konfigurace funguje.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace jednotného přihlašování Azure AD

V této části povolíte jednotné přihlašování Azure AD v Azure Portal.

Pokud chcete nakonfigurovat jednotné přihlašování pomocí služby Azure AD, proveďte následující kroky:

1. V [Azure Portal](https://portal.azure.com/)na stránce **konkrétně** integrace aplikací vyberte **jednotné přihlašování**.

    ![Konfigurovat odkaz jednotného přihlašování](common/select-sso.png)

2. V dialogovém okně **Vyberte metodu jednotného přihlašování** vyberte možnost režim **SAML/WS** , čímž povolíte jednotné přihlašování.

    ![Režim výběru jednotného přihlašování](common/select-saml-option.png)

3. Na stránce **nastavit jeden Sign-On s SAML** klikněte na **Upravit** ikona a otevře se základní dialogové okno **Konfigurace SAML** .

    ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. V části **základní konfigurace SAML** proveďte následující kroky:

    ![Konkrétně informace o jednotném přihlašování k doménám a adresám URL](common/sp-identifier.png)

    a. Do textového pole **přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru: `https://<subdomain>.namely.com`

    b. Do textového pole **identifikátor (ID entity)** zadejte adresu URL pomocí následujícího vzoru: `https://<subdomain>.namely.com/saml/metadata`

    > [!NOTE]
    > Tyto hodnoty nejsou reálné. Aktualizujte tyto hodnoty skutečným přihlašovacím jménem a identifikátorem URL. Pro získání těchto hodnot kontaktujte [konkrétně tým podpory klienta](https://www.namely.com/contact/) . Můžete se také podívat na vzory uvedené v části **základní konfigurace SAML** v Azure Portal.

5. Na stránce **nastavit jeden Sign-On se** stránkou SAML v části **podpisový certifikát SAML** klikněte na **Stáhnout** a Stáhněte si **certifikát (Base64)** z daných možností podle vašich požadavků a uložte ho do svého počítače.

    ![Odkaz na stažení certifikátu](common/certificatebase64.png)

6. V části **Nastavení konkrétně** zkopírujte příslušné adresy URL podle vašeho požadavku.

    ![Kopírovat adresy URL konfigurace](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor Azure AD

    c. Odhlašovací adresa URL

### <a name="configure-namely-single-sign-on"></a>Konfigurovat konkrétně jednu Sign-On

1. V jiném okně prohlížeče se přihlaste k vaší organizaci jako správce.

2. Na panelu nástrojů v horní části klikněte na možnost **Společnost**.
   
    ![Konfigurace jednoho Sign-On](./media/namely-tutorial/tutorial_namely_06.png) 

3. Klikněte na kartu **Nastavení**.
   
    ![Konfigurace jednoho Sign-On](./media/namely-tutorial/tutorial_namely_07.png) 

4. Klikněte na **SAML**.
   
    ![Konfigurace jednoho Sign-On](./media/namely-tutorial/tutorial_namely_08.png) 

5. Na stránce **Nastavení SAML** proveďte následující kroky:
   
    ![Konfigurace jednoho Sign-On](./media/namely-tutorial/tutorial_namely_09.png)
 
    a. Klikněte na **Povolit SAML**. 

    b. Do textového pole **Adresa URL jednotného přihlašování zprostředkovatele identity** vložte hodnotu **přihlašovací adresa URL**, kterou jste zkopírovali z Azure Portal.
    
    c. Otevřete stažený certifikát v programu Poznámkový blok, zkopírujte obsah a vložte ho do textového pole **certifikát poskytovatele identity** .
     
    d. Klikněte na **Uložit**.

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

V této části povolíte Britta Simon pro použití jednotného přihlašování pomocí Azure tím, že udělíte přístup k konkrétně.

1. V Azure Portal vyberte možnost **podnikové aplikace**, vyberte možnost **všechny aplikace**a pak vyberte **konkrétně**.

    ![Okno Podnikové aplikace](common/enterprise-applications.png)

2. V seznamu aplikace vyberte **konkrétně**.

    ![Odkaz konkrétně v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **Uživatelé a skupiny**.

    ![Odkaz uživatelé a skupiny](common/users-groups-blade.png)

4. Klikněte na tlačítko **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Podokno přidat přiřazení](common/add-assign-user.png)

5. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **Britta Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.

6. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, pak v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.

7. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

### <a name="create-namely-test-user"></a>Vytvořit konkrétně testovacího uživatele

Cílem této části je vytvořit uživatele s názvem Britta Simon v, konkrétně.

**Chcete-li vytvořit uživatele s názvem Britta Simon v, konkrétně proveďte následující kroky:**

1. Přihlaste se k vaší organizaci jako správce.

2. Na panelu nástrojů v horní části klikněte na **lidé**.
   
    ![Konfigurace jednoho Sign-On](./media/namely-tutorial/tutorial_namely_10.png) 

3. Klikněte na kartu **adresář** .
   
    ![Konfigurace jednoho Sign-On](./media/namely-tutorial/tutorial_namely_11.png) 

4. Klikněte na **Přidat novou osobu**.

    ![Konfigurace jednoho Sign-On](./media/namely-tutorial/tutorial_namely_12.png)

5. V dialogovém okně **Přidat novou osobu** proveďte následující kroky:

    a. Do textového pole **jméno v prvním** poli zadejte **Britta**.

    b. Do textového pole **příjmení** zadejte **Simon**.

    c. Do textového pole **e-mail** zadejte **e-mailovou adresu** BrittaSimon.

    d. Klikněte na **Uložit**.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování 

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí přístupového panelu.

Když na přístupovém panelu kliknete na dlaždici, měli byste být automaticky přihlášeni k, ke kterému jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další materiály

- [Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

