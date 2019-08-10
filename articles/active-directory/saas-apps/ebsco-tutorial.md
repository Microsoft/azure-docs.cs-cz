---
title: 'Kurz: Azure Active Directory integrace s EBSCO | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a EBSCO.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 144f7f65-69e9-4016-a151-fe1104fd6ba8
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/01/2019
ms.author: jeedes
ms.openlocfilehash: 35cb408473da8c6397c5034ae20ac0a50b0953ea
ms.sourcegitcommit: 124c3112b94c951535e0be20a751150b79289594
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/10/2019
ms.locfileid: "68944725"
---
# <a name="tutorial-azure-active-directory-integration-with-ebsco"></a>Kurz: Integrace Azure Active Directory s EBSCO

V tomto kurzu se dozvíte, jak integrovat EBSCO s Azure Active Directory (Azure AD).
Integrace EBSCO s Azure AD poskytuje následující výhody:

* Můžete kontrolovat v Azure AD, kteří mají přístup k EBSCO.
* Můžete povolit, aby se vaši uživatelé automaticky přihlásili k EBSCO (jednotné přihlašování) pomocí svých účtů Azure AD.
* Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete získat další podrobnosti o integraci aplikace SaaS s Azure AD, přečtěte si téma [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Ke konfiguraci integrace služby Azure AD s EBSCO potřebujete následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat [bezplatný účet](https://azure.microsoft.com/free/) .
* Předplatné s povoleným EBSCOm jednotným přihlašováním

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* EBSCO podporuje jednotné přihlašování (SSO) a **IDP** .

* EBSCO podporuje zřizování uživatelů **jenom v čase** .

## <a name="adding-ebsco-from-the-gallery"></a>Přidání EBSCO z Galerie

Pokud chcete nakonfigurovat integraci EBSCO do služby Azure AD, musíte přidat EBSCO z Galerie do svého seznamu spravovaných aplikací SaaS.

**Pokud chcete přidat EBSCO z Galerie, proveďte následující kroky:**

1. V **[Azure Portal](https://portal.azure.com)** na levém navigačním panelu klikněte na ikonu **Azure Active Directory** .

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte na **podnikové aplikace** a vyberte možnost **všechny aplikace** .

    ![V okně podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **Nová aplikace** v horní části dialogového okna.

    ![Tlačítko nové aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **EBSCO**, v panelu výsledek vyberte **EBSCO** a potom kliknutím na tlačítko **Přidat** přidejte aplikaci.

     ![EBSCO v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD pomocí EBSCO na základě testovacího uživatele s názvem **Britta Simon**.
Aby jednotné přihlašování fungovalo, musí se zřídit vztah propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v EBSCO.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí EBSCO, musíte dokončit tyto stavební bloky:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Nakonfigurujte jednotné přihlašování EBSCO](#configure-ebsco-single-sign-on)** – ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
3. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
4. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
5. **[Vytvoření EBSCO Test User](#create-ebsco-test-user)** – pro Britta Simon v EBSCO, který je propojený s reprezentací uživatele Azure AD.
6. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části povolíte jednotné přihlašování Azure AD v Azure Portal.

Pokud chcete nakonfigurovat jednotné přihlašování Azure AD pomocí EBSCO, proveďte následující kroky:

1. V [Azure Portal](https://portal.azure.com/)na stránce integrace aplikací **EBSCO** vyberte **jednotné přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz](common/select-sso.png)

2. V dialogovém okně **Vyberte metodu jednotného přihlašování** vyberte možnost režim **SAML/WS** , čímž povolíte jednotné přihlašování.

    ![Režim výběru jednotného přihlašování](common/select-saml-option.png)

3. Na stránce **nastavit jednotné přihlašování pomocí SAML** kliknutím na ikonu **Upravit** otevřete základní dialogové okno **Konfigurace SAML** .

    ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. Pokud chcete nakonfigurovat aplikaci v režimu iniciované **IDP** , proveďte v **základní části Konfigurace SAML** následující krok:

    ![Informace o jednotném přihlašování v doméně EBSCO a adresách URL](common/idp-identifier.png)

    Do textového pole **identifikátor** zadejte adresu URL:`pingsso.ebscohost.com`

5. Klikněte na **nastavit další adresy URL** a proveďte následující krok, pokud chcete nakonfigurovat aplikaci v režimu iniciované **SP** :

    ![image](common/both-preintegrated-signon.png)

    Do textového pole **přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru:`https://search.ebscohost.com/login.aspx?authtype=sso&custid=<unique EBSCO customer ID>&profile=<profile ID>`

    > [!NOTE]
    > Hodnota přihlašovací adresy URL není reálné číslo. Aktualizujte hodnotu skutečnou přihlašovací adresou URL. Pokud chcete získat tyto hodnoty, obraťte se na [tým podpory klienta EBSCO](mailto:sso@ebsco.com) . Můžete se také podívat na vzory uvedené v části **základní konfigurace SAML** v Azure Portal.

    o **jedinečných prvcích:**  

    o **custid** = zadejte jedinečné ID zákazníka EBSCO 

    **Profiling** = klienti můžou přizpůsobit odkaz na směrování uživatelů na konkrétní profil (v závislosti na tom, co kupují z EBSCO). Můžou zadat konkrétní ID profilu. Hlavní ID jsou EDS (služba EBSCO Discovery) a ehost (databáze EBSOCOhost). Pokyny pro stejné jsou uvedeny [zde](https://help.ebsco.com/interfaces/EBSCOhost/EBSCOhost_FAQs/How_do_I_set_up_direct_links_to_EBSCOhost_profiles_and_or_databases#profile).

6. EBSCO aplikace očekává kontrolní výrazy SAML v určitém formátu, což vyžaduje přidání mapování vlastních atributů do konfigurace atributů tokenu SAML. Následující snímek obrazovky ukazuje seznam výchozích atributů. Kliknutím na tlačítko **Upravit** ikonu otevřete dialogové okno **atributy uživatele** .

    ![image](common/edit-attribute.png)

     > [!Note]
    > Atribut **Name** je povinný a v aplikaci EBSCO se namapuje na **hodnotu identifikátoru názvu** . Toto je ve výchozím nastavení přidáno, takže je nemusíte přidávat ručně.

7. Kromě výše očekává aplikace EBSCO několik dalších atributů, které se vrátí zpátky v odpovědi SAML. V části **deklarace identity uživatelů** v dialogovém okně **atributy uživatele** proveďte následující kroky pro přidání atributu tokenu SAML, jak je znázorněno v následující tabulce: 

    | Name | Zdrojový atribut|
    | ---------------| --------------- |    
    | FirstName   | user.givenname |
    | LastName   | user.surname |
    | Email   | user.mail |

    a. Kliknutím na **Přidat novou deklaraci identity** otevřete dialogové okno **Spravovat deklarace identity uživatelů** .

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. Do textového pole **název** zadejte název atributu zobrazeného pro tento řádek.

    c. Ponechte **obor názvů** prázdný.

    d. Jako **atribut**vyberte zdroj.

    e. V seznamu **zdrojový atribut** zadejte hodnotu atributu zobrazenou pro tento řádek.

    f. Klikněte na **Uložit**.

8. Na stránce **nastavit jednotné přihlašování pomocí SAML** v části **podpisový certifikát SAML** klikněte na **Stáhnout** a Stáhněte si **XML federačních metadat** z daných možností podle vašich požadavků a uložte ho do svého počítače.

    ![Odkaz ke stažení certifikátu](common/metadataxml.png)

9. V části **Nastavení EBSCO** zkopírujte příslušné adresy URL podle vašich požadavků.

    ![Kopírovat adresy URL konfigurace](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor Azure AD

    c. Adresa URL – odhlášení

### <a name="configure-ebsco-single-sign-on"></a>Konfigurace jednotného přihlašování EBSCO

Ke konfiguraci jednotného přihlašování na straně **EBSCO** je potřeba odeslat stažená **metadata XML** a příslušné zkopírované adresy URL z Azure Portal do [týmu podpory EBSCO](mailto:sso@ebsco.com). Nastavují tohoto nastavení můžete mít správně nastavené na obou stranách připojení SAML SSO.

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD 

Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

1. Na webu Azure Portal, v levém podokně vyberte **Azure Active Directory**vyberte **uživatelé**a pak vyberte **všichni uživatelé**.

    !["Uživatele a skupiny" a "Všechny uživatele" odkazy](common/users.png)

2. Vyberte **nového uživatele** v horní části obrazovky.

    ![Tlačítko pro nového uživatele](common/new-user.png)

3. Ve vlastnosti uživatele proveďte následující kroky.

    ![Dialogové okno uživatele](common/user-properties.png)

    a. Do pole **název** zadejte **BrittaSimon**.
  
    b. Do pole **uživatelské jméno** zadejte brittasimon@yourcompanydomain.extension. Například BrittaSimon@contoso.com.

    c. Zaškrtněte políčko **Zobrazit heslo** a pak zapište hodnotu, která se zobrazí v poli heslo.

    d. Klikněte na možnost **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části povolíte Britta Simon pro použití jednotného přihlašování pomocí Azure tím, že udělíte přístup k EBSCO.

1. V Azure Portal vyberte **podnikové aplikace**, vyberte **všechny aplikace**a pak vyberte **EBSCO**.

    ![Okno podnikových aplikací](common/enterprise-applications.png)

2. V seznamu aplikace vyberte **EBSCO**.

    ![Odkaz EBSCO v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **Uživatelé a skupiny**.

    ![Odkaz "Uživatele a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V **uživatelů a skupin** dialogové okno Vybrat **Britta Simon** v seznamu uživatelů, klikněte **vyberte** tlačítko v dolní části obrazovky.

6. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, pak v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.

7. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

### <a name="create-ebsco-test-user"></a>Vytvořit testovacího uživatele EBSCO

V případě EBSCO je zřizování uživatelů automatické.

**Chcete-li zřídit uživatelský účet, proveďte následující kroky:**

Azure AD předá požadovaná data EBSCO aplikaci. Zřizování uživatelů EBSCO může být automatické nebo vyžadovat jednorázový formulář. Záleží na tom, jestli má klient mnoho již existujících účtů EBSCOhost s uloženým osobním nastavením. Totéž můžete v rámci implementace projednávat s [týmem podpory EBSCO](mailto:sso@ebsco.com) . V obou případech klient nemusí vytvářet žádné účty EBSCOhost před testováním.

   >[!Note]
   >Můžete automatizovat EBSCOhost zřizování a přizpůsobení uživatelů. Obraťte se na [tým podpory EBSCO](mailto:sso@ebsco.com) o zřizování uživatelů za běhu. 

### <a name="test-single-sign-on"></a>Test jednotného přihlašování 

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

1. Když na přístupovém panelu kliknete na dlaždici EBSCO, měli byste se automaticky přihlásili ke své aplikaci EBSCO.
Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](../user-help/active-directory-saas-access-panel-introduction.md).

2. Po přihlášení k aplikaci klikněte v pravém horním rohu na tlačítko **Přihlásit** se.

    ![Přihlášení EBSCO v seznamu aplikací](./media/ebsco-tutorial/tutorial_ebsco_signin.png)
 
3. Zobrazí se jednorázová výzva k párování místního přihlášení a přihlašovacího jména SAML s odkazem na **váš stávající účet MyEBSCOhost k vašemu účtu instituce** nebo **vytvořte nový účet MyEBSCOhost a propojte ho s účtem instituce**. Účet se používá pro přizpůsobení aplikace EBSCOhost. Vyberte možnost **vytvořit nový účet** a uvidíte, že formulář pro přizpůsobení je předem dokončen s hodnotami z odpovědi SAML, jak je znázorněno na snímku obrazovky níže. Kliknutím na **pokračovat** uložte tento výběr.
    
     ![Uživatel EBSCO v seznamu aplikací](./media/ebsco-tutorial/tutorial_ebsco_user.png)

1. Po dokončení výše uvedeného nastavení vymažte soubory cookie/mezipaměť a znovu se přihlaste. Nebudete se muset ručně přihlašovat znovu a nastavení přizpůsobení se zapamatuje.

## <a name="additional-sesources"></a>Další sesources

- [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

