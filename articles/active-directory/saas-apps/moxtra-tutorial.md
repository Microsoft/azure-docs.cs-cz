---
title: 'Kurz: Azure Active Directory integrace s Moxtra | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Moxtra.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 2aed2d4b-1dcd-4839-8fed-9419d107c61c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/01/2019
ms.author: jeedes
ms.openlocfilehash: 7f64597d8da183a24bcf87543a448442052e5f77
ms.sourcegitcommit: 124c3112b94c951535e0be20a751150b79289594
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/10/2019
ms.locfileid: "68944266"
---
# <a name="tutorial-azure-active-directory-integration-with-moxtra"></a>Kurz: Integrace Azure Active Directory s Moxtra

V tomto kurzu se dozvíte, jak integrovat Moxtra s Azure Active Directory (Azure AD).
Integrace Moxtra s Azure AD poskytuje následující výhody:

* Můžete kontrolovat v Azure AD, kteří mají přístup k Moxtra.
* Můžete povolit, aby se vaši uživatelé automaticky přihlásili k Moxtra (jednotné přihlašování) pomocí svých účtů Azure AD.
* Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete získat další podrobnosti o integraci aplikace SaaS s Azure AD, přečtěte si téma [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Ke konfiguraci integrace služby Azure AD s Moxtra potřebujete následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční zkušební verzi [tady](https://azure.microsoft.com/pricing/free-trial/) .
* Předplatné s povoleným Moxtram jednotným přihlašováním

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Moxtra podporuje jednotné přihlašování iniciované v **SP**

## <a name="adding-moxtra-from-the-gallery"></a>Přidání Moxtra z Galerie

Pokud chcete nakonfigurovat integraci Moxtra do služby Azure AD, musíte přidat Moxtra z Galerie do svého seznamu spravovaných aplikací SaaS.

**Pokud chcete přidat Moxtra z Galerie, proveďte následující kroky:**

1. V **[webu Azure portal](https://portal.azure.com)** , v levém navigačním panelu klikněte na **Azure Active Directory** ikonu.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte na **podnikové aplikace** a vyberte možnost **všechny aplikace** .

    ![V okně podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **Moxtra**, vyberte **Moxtra** z panelu výsledků a potom kliknutím na tlačítko **Přidat** přidejte aplikaci.

     ![Moxtra v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD pomocí Moxtra na základě testovacího uživatele s názvem **Britta Simon**.
Aby jednotné přihlašování fungovalo, musí se zřídit vztah propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v Moxtra.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí Moxtra, musíte dokončit tyto stavební bloky:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Nakonfigurujte jednotné přihlašování Moxtra](#configure-moxtra-single-sign-on)** – ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
3. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
4. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
5. **[Vytvoření Moxtra Test User](#create-moxtra-test-user)** – pro Britta Simon v Moxtra, který je propojený s reprezentací uživatele Azure AD.
6. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části povolíte jednotné přihlašování Azure AD v Azure Portal.

Pokud chcete nakonfigurovat jednotné přihlašování Azure AD pomocí Moxtra, proveďte následující kroky:

1. V [Azure Portal](https://portal.azure.com/)na stránce integrace aplikací **Moxtra** vyberte **jednotné přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz](common/select-sso.png)

2. V dialogovém okně **Vyberte metodu jednotného přihlašování** vyberte možnost režim **SAML/WS** , čímž povolíte jednotné přihlašování.

    ![Režim výběru jednotného přihlašování](common/select-saml-option.png)

3. Na **nastavte si jednotné přihlašování pomocí SAML** klikněte na **upravit** ikony otevřete **základní konfiguraci SAML** dialogového okna.

    ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. V části **základní konfigurace SAML** proveďte následující kroky:

    ![Informace o jednotném přihlašování v doméně Moxtra a adresách URL](common/sp-signonurl.png)

    Do textového pole **přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru:`https://www.moxtra.com/service/#login`

5. Moxtra aplikace očekává kontrolní výrazy SAML v určitém formátu, což vyžaduje přidání mapování vlastních atributů do konfigurace atributů tokenu SAML. Následující snímek obrazovky ukazuje seznam výchozích atributů. Kliknutím na tlačítko **Upravit** ikonu otevřete dialogové okno **atributy uživatele** .

    ![image](common/edit-attribute.png)

6. Kromě výše očekává aplikace Moxtra několik dalších atributů, které se vrátí zpátky v odpovědi SAML. V části **deklarace identity uživatelů** v dialogovém okně **atributy uživatele** proveďte následující kroky pro přidání atributu tokenu SAML, jak je znázorněno v následující tabulce: 

    | Name | Zdrojový atribut|
    | ------------------- | -------------------- |    
    | FirstName | user.givenname |
    | polím | user.surname |
    | idpid    | < > Identifikátoru Azure AD

    > [!Note]
    > Hodnota atributu **idpid** není reálné číslo. Aktuální hodnotu můžete získat z části **Nastavení Moxtra** z kroku č. 8. 

    a. Kliknutím na **Přidat novou deklaraci identity** otevřete dialogové okno **Spravovat deklarace identity uživatelů** .

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. Do textového pole **název** zadejte název atributu zobrazeného pro tento řádek.

    c. Ponechte **obor názvů** prázdný.

    d. Jako **atribut**vyberte zdroj.

    e. V seznamu **zdrojový atribut** zadejte hodnotu atributu zobrazenou pro tento řádek.

    f. Klikněte na tlačítko **Ok**

    g. Klikněte na **Uložit**.

7. Na stránce **nastavit jednotné přihlašování pomocí SAML** v části **podpisový certifikát SAML** klikněte na **Stáhnout** a Stáhněte si **certifikát (Base64)** z daných možností podle vašich požadavků a uložte ho do svého počítače.

    ![Odkaz ke stažení certifikátu](common/certificatebase64.png)

8. V části **Nastavení Moxtra** zkopírujte příslušné adresy URL podle vašich požadavků.

    ![Kopírovat adresy URL konfigurace](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor Azure AD

    c. Adresa URL – odhlášení

### <a name="configure-moxtra-single-sign-on"></a>Konfigurace jednotného přihlašování Moxtra

1. V jiném okně prohlížeče se přihlaste k webu Moxtra společnosti jako správce.

2. Na panelu nástrojů na levé straně klikněte na **Konzola pro správu > jednotné přihlašování SAML**a potom klikněte na **Nový**.
   
    ![Konfigurace jednotného přihlašování](./media/moxtra-tutorial/tutorial_moxtra_06.png) 

3. Na stránce **SAML** proveďte následující kroky:
   
    ![Konfigurace jednotného přihlašování](./media/moxtra-tutorial/tutorial_moxtra_08.png)   
 
    a. Do textového pole **název** zadejte název vaší konfigurace (např.: *SAML*). 
  
    b. Do textového pole **ID entity IDP** vložte hodnotu **identifikátoru služby Azure AD** , který jste zkopírovali z Azure Portal. 
 
    c. Do textového pole **Adresa URL pro přihlášení** vložte hodnotu **adresy URL pro přihlášení** , kterou jste zkopírovali z Azure Portal. 
 
    d. Do textového pole **AuthnContextClassRef** zadejte **urn: Oasis: names: TC: SAML: 2.0: AC: Classes: Password**. 
 
    e. Do textového pole **Formát NameId** zadejte **urn: Oasis: names: TC: SAML: 1.1: NameId-Format: EmailAddress**. 
 
    f. Otevřete certifikát, který jste si stáhli z Azure Portal v programu Poznámkový blok, zkopírujte obsah a vložte ho do textového pole **certifikátu** .    
 
    g. Do textového pole e-mailová doména SAML zadejte svou e-mailovou doménu SAML.    
  
    >[!NOTE]
    >Chcete-li zobrazit kroky pro ověření domény, klikněte na tlačítko "**i**" níže.

    h. Klikněte na tlačítko **aktualizace**.

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD 

Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

1. Na webu Azure Portal, v levém podokně vyberte **Azure Active Directory**vyberte **uživatelé**a pak vyberte **všichni uživatelé**.

    !["Uživatele a skupiny" a "Všechny uživatele" odkazy](common/users.png)

2. Vyberte **nového uživatele** v horní části obrazovky.

    ![Tlačítko pro nového uživatele](common/new-user.png)

3. Ve vlastnosti uživatele proveďte následující kroky.

    ![Dialogové okno uživatele](common/user-properties.png)

    a. Do pole **název** zadejte **BrittaSimon**.
  
    b. Do pole **uživatelské jméno** zadejte **brittasimon\@yourcompanydomain. extension.**  
    Například BrittaSimon@contoso.com.

    c. Zaškrtněte políčko **Zobrazit heslo** a pak zapište hodnotu, která se zobrazí v poli heslo.

    d. Klikněte na možnost **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části povolíte Britta Simon pro použití jednotného přihlašování pomocí Azure tím, že udělíte přístup k Moxtra.

1. V Azure Portal vyberte **podnikové aplikace**, vyberte **všechny aplikace**a pak vyberte **Moxtra**.

    ![Okno podnikových aplikací](common/enterprise-applications.png)

2. V seznamu aplikace vyberte **Moxtra**.

    ![Odkaz Moxtra v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **Uživatelé a skupiny**.

    ![Odkaz "Uživatele a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V **uživatelů a skupin** dialogové okno Vybrat **Britta Simon** v seznamu uživatelů, klikněte **vyberte** tlačítko v dolní části obrazovky.

6. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, pak v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.

7. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

### <a name="create-moxtra-test-user"></a>Vytvořit testovacího uživatele Moxtra

Cílem této části je vytvořit uživatele s názvem Britta Simon v Moxtra.

**Chcete-li vytvořit uživatele s názvem Britta Simon v Moxtra, proveďte následující kroky:**

1. Přihlaste se k webu Moxtra společnosti jako správce.

1. Na panelu nástrojů na levé straně klikněte na **Konzola pro správu > Správa uživatelů**a pak na **Přidat uživatele**.
   
    ![Konfigurace jednotného přihlašování](./media/moxtra-tutorial/tutorial_moxtra_10.png) 

1. V dialogovém okně **Přidat uživatele** proveďte následující kroky:
  
    a. Do textového pole **jméno v prvním** poli zadejte **Britta**.
  
    b. Do textového pole **příjmení** zadejte **Simon**.
  
    c. Do textového pole **e-mail** zadejte e-mailovou adresu Britta stejné jako u Azure Portal.
  
    d. Do textového pole **dělení** zadejte **dev**.
  
    e. Do textového pole **oddělení** zadejte.
  
    f. Vyberte **správce**.
  
    g. Klikněte na **Přidat**.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování 

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Když na přístupovém panelu kliknete na dlaždici Moxtra, měli byste se automaticky přihlásit k Moxtra, pro které jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další prostředky

- [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

