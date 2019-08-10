---
title: 'Kurz: Azure Active Directory integrace s iQualify LMS | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a iQualify LMS.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 8a3caaff-dd8d-4afd-badf-a0fd60db3d2c
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/14/2019
ms.author: jeedes
ms.openlocfilehash: a1db4784eb63df14b7e7971d0273512ba657df96
ms.sourcegitcommit: 124c3112b94c951535e0be20a751150b79289594
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/10/2019
ms.locfileid: "68944998"
---
# <a name="tutorial-azure-active-directory-integration-with-iqualify-lms"></a>Kurz: Integrace Azure Active Directory s iQualify LMS

V tomto kurzu se dozvíte, jak integrovat iQualify LMS pomocí Azure Active Directory (Azure AD).
Integrování iQualify LMS s Azure AD poskytuje následující výhody:

* Můžete kontrolovat v Azure AD, kteří mají přístup k iQualify LMS.
* Můžete povolit, aby se vaši uživatelé automaticky přihlásili k iQualify LMS (jednotné přihlašování) pomocí svých účtů Azure AD.
* Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete získat další podrobnosti o integraci aplikace SaaS s Azure AD, přečtěte si téma [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Ke konfiguraci integrace služby Azure AD s iQualify LMS budete potřebovat následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční zkušební verzi [tady](https://azure.microsoft.com/pricing/free-trial/) .
* předplatné iQualify LMS s povoleným jednotným přihlašováním

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* iQualify LMS podporuje **aktualizace SP a IDP, které** iniciovaly jednotné přihlašování.
* iQualify LMS podporuje zřizování uživatelů **jenom v čase**

## <a name="adding-iqualify-lms-from-the-gallery"></a>Přidání LMS iQualify z Galerie

Pokud chcete nakonfigurovat integraci iQualify LMS do služby Azure AD, musíte do seznamu spravovaných aplikací pro SaaS přidat iQualify LMS z galerie.

**Pokud chcete přidat LMS iQualify z Galerie, proveďte následující kroky:**

1. V **[webu Azure portal](https://portal.azure.com)** , v levém navigačním panelu klikněte na **Azure Active Directory** ikonu.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte na **podnikové aplikace** a vyberte možnost **všechny aplikace** .

    ![V okně podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **IQUALIFY LMS**, vyberte **iQualify LMS** z panelu výsledků a pak klikněte na **Přidat** tlačítko pro přidání aplikace.

     ![iQualify LMS v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD pomocí iQualify LMS založeného na testovacím uživateli s názvem **Britta Simon**.
Aby jednotné přihlašování fungovalo, musí se zřídit vztah propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v iQualify LMS.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí iQualify LMS, musíte dokončit tyto stavební bloky:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Nakonfigurujte jednotné přihlašování IQUALIFY LMS](#configure-iqualify-lms-single-sign-on)** – ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
3. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
4. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
5. **[Vytvořit testovacího uživatele pro IQUALIFY LMS](#create-iqualify-lms-test-user)** – abyste měli protějšek Britta Simon v iQualify LMS, který je propojený s reprezentací uživatele v Azure AD.
6. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části povolíte jednotné přihlašování Azure AD v Azure Portal.

Pokud chcete nakonfigurovat jednotné přihlašování Azure AD pomocí iQualify LMS, proveďte následující kroky:

1. V [Azure Portal](https://portal.azure.com/)na stránce integrace aplikace **IQualify LMS** vyberte **jednotné přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz](common/select-sso.png)

2. V dialogovém okně **Vyberte metodu jednotného přihlašování** vyberte možnost režim **SAML/WS** , čímž povolíte jednotné přihlašování.

    ![Režim výběru jednotného přihlašování](common/select-saml-option.png)

3. Na **nastavte si jednotné přihlašování pomocí SAML** klikněte na **upravit** ikony otevřete **základní konfiguraci SAML** dialogového okna.

    ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. Pokud chcete nakonfigurovat aplikaci v režimu iniciované **IDP** , proveďte v **základní části Konfigurace SAML** následující kroky:

    ![informace o jednotném přihlašování iQualify LMS domény a adresy URL](common/idp-intiated.png)

    a. Do textového pole **identifikátor** zadejte adresu URL pomocí následujícího vzoru:
    | |
    |--|--|
    | Provozní prostředí:`https://<yourorg>.iqualify.com/`|
    | Testovací prostředí:`https://<yourorg>.iqualify.io`|

    b. Do textového pole **Adresa URL odpovědi** zadejte adresu URL pomocí následujícího vzoru:
    | |
    |--|--|
    | Provozní prostředí:`https://<yourorg>.iqualify.com/auth/saml2/callback` |
    | Testovací prostředí:`https://<yourorg>.iqualify.io/auth/saml2/callback` |

5. Klikněte na **nastavit další adresy URL** a proveďte následující krok, pokud chcete nakonfigurovat aplikaci v režimu iniciované **SP** :

    ![informace o jednotném přihlašování iQualify LMS domény a adresy URL](common/metadata-upload-additional-signon.png)

    Do textového pole **přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru:
    | |
    |--|--|
    | Provozní prostředí:`https://<yourorg>.iqualify.com/login` |
    | Testovací prostředí:`https://<yourorg>.iqualify.io/login` |

    > [!NOTE]
    > Tyto hodnoty nejsou reálné. Aktualizujte tyto hodnoty skutečným identifikátorem, adresou URL odpovědi a přihlašovací adresou URL. Pokud chcete získat tyto hodnoty, obraťte se na [tým podpory klienta IQUALIFY LMS](https://www.iqualify.com/) . Můžete se také podívat na vzory uvedené v části **základní konfigurace SAML** v Azure Portal.

6. Vaše aplikace iQualify LMS očekává kontrolní výrazy SAML v určitém formátu, což vyžaduje přidání mapování vlastních atributů do konfigurace atributů tokenu SAML. Následující snímek obrazovky ukazuje seznam výchozích atributů. Kliknutím na tlačítko **Upravit** ikonu otevřete dialogové okno **atributy uživatele** .

    ![image](common/edit-attribute.png)

7. V části **deklarace identity uživatelů** v dialogovém okně **atributy uživatele** upravte deklarace pomocí **ikony upravit** nebo přidejte deklarace pomocí **Přidat novou deklaraci identity** , jak je znázorněno na obrázku výše, a proveďte následující kroky:

    | Name | Zdrojový atribut|
    | --- | --- |
    | email | user.userprincipalname |
    | křestní_jméno | user.givenname |
    | příjmení | user.surname |
    | person_id | "atribut" |

    a. Kliknutím na **Přidat novou deklaraci identity** otevřete dialogové okno **Spravovat deklarace identity uživatelů** .

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. Do textového pole **název** zadejte název atributu zobrazeného pro tento řádek.

    c. Ponechte **obor názvů** prázdný.

    d. Jako **atribut**vyberte zdroj.

    e. V seznamu **zdrojový atribut** zadejte hodnotu atributu zobrazenou pro tento řádek.

    f. Klikněte na tlačítko **Ok**

    g. Klikněte na **Uložit**.

    > [!Note]
    > Atribut **person_id** je nepovinný.

8. Na stránce **nastavit jednotné přihlašování pomocí SAML** v části **podpisový certifikát SAML** klikněte na **Stáhnout** a Stáhněte si **certifikát (Base64)** z daných možností podle vašich požadavků a uložte ho do svého počítače.

    ![Odkaz ke stažení certifikátu](common/certificatebase64.png)

9. V části **set LMS iQualify** zkopírujte příslušné adresy URL podle vašich požadavků.

    ![Kopírovat adresy URL konfigurace](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor Azure AD

    c. Adresa URL – odhlášení

### <a name="configure-iqualify-lms-single-sign-on"></a>Konfigurace jednotného přihlašování pro iQualify LMS

1. Otevřete nové okno prohlížeče a přihlaste se k iQualify prostředí jako správce.

1. Po přihlášení klikněte na miniaturu v pravém horním rohu a pak klikněte na **Nastavení účtu** .

    ![Nastavení účtu](./media/iqualify-tutorial/setting1.png)

1. V oblasti nastavení účtu klikněte na pásu karet na levé straně a pak klikněte na **integrace** .

    ![INTEGRACE](./media/iqualify-tutorial/setting2.png)

1. V části integrace klikněte na ikonu **SAML** .

    ![Ikona SAML](./media/iqualify-tutorial/setting3.png)

1. V dialogovém okně **nastavení ověřování SAML** proveďte následující kroky:

    ![Nastavení ověřování SAML](./media/iqualify-tutorial/setting4.png)

    a. Do pole **Adresa URL služby jednotného přihlašování SAML** vložte hodnotu URL pro **přihlášení** zkopírovanou z okna konfigurace aplikace Azure AD.

    b. Do pole **Adresa URL pro odhlášení SAML** vložte hodnotu **URL** pro odhlášení zkopírovanou z okna konfigurace aplikace Azure AD.

    c. Otevřete stažený soubor certifikátu v programu Poznámkový blok, zkopírujte jeho obsah a vložte ho do pole **veřejný certifikát** .

    d. V části **tlačítko pro přihlášení** zadejte název tlačítka, který se má zobrazit na přihlašovací stránce.

    e. Klikněte na **ULOŽIT**.

    f. Klikněte na **aktualizovat**.

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

1. Na webu Azure Portal, v levém podokně vyberte **Azure Active Directory**vyberte **uživatelé**a pak vyberte **všichni uživatelé**.

    !["Uživatele a skupiny" a "Všechny uživatele" odkazy](common/users.png)

2. Vyberte **nového uživatele** v horní části obrazovky.

    ![Tlačítko pro nového uživatele](common/new-user.png)

3. Ve vlastnosti uživatele proveďte následující kroky.

    ![Dialogové okno uživatele](common/user-properties.png)

    a. Do pole **název** zadejte **BrittaSimon**.
  
    b. Do pole typ **uživatelského jména** **brittasimon@yourcompanydomain.extension**  
    Například BrittaSimon@contoso.com.

    c. Zaškrtněte políčko **Zobrazit heslo** a pak zapište hodnotu, která se zobrazí v poli heslo.

    d. Klikněte na možnost **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části povolíte Britta Simon pro použití jednotného přihlašování pomocí Azure tím, že udělíte přístup k iQualify LMS.

1. V Azure Portal vyberte možnost **podnikové aplikace**, vyberte možnost **všechny aplikace**a pak vyberte **iQualify LMS**.

    ![Okno podnikových aplikací](common/enterprise-applications.png)

2. V seznamu aplikace vyberte **IQUALIFY LMS**.

    ![Odkaz na iQualify LMS v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **Uživatelé a skupiny**.

    ![Odkaz "Uživatele a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V **uživatelů a skupin** dialogové okno Vybrat **Britta Simon** v seznamu uživatelů, klikněte **vyberte** tlačítko v dolní části obrazovky.

6. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, pak v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.

7. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

### <a name="create-iqualify-lms-test-user"></a>Vytvořit testovacího uživatele pro iQualify LMS

V této části se v iQualify LMS vytvoří uživatel s názvem Britta Simon. iQualify LMS podporuje zřizování uživatelů za běhu, což je ve výchozím nastavení povolené. V této části není žádná položka akce. Pokud uživatel ještě v iQualify LMS neexistuje, vytvoří se po ověření nový.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Když kliknete na dlaždici iQualify LMS na přístupovém panelu, měli byste získat přihlašovací stránku vaší aplikace iQualify LMS. 

   ![přihlašovací stránka](./media/iqualify-tutorial/login.png) 

Klikněte na tlačítko **Přihlásit se pomocí Azure AD** a měli byste se automaticky přihlašovat k aplikaci iQualify LMS.

Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Další prostředky

- [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)