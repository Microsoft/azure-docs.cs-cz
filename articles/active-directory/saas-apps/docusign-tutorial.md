---
title: 'Kurz: Azure Active Directory integrace s DocuSign | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a DocuSign.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: a691288b-84c1-40fb-84bd-5b06878865f0
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/01/2019
ms.author: jeedes
ms.openlocfilehash: 5c7d6116ed2925e57f094a67f27a11f9e2d61831
ms.sourcegitcommit: a0b37e18b8823025e64427c26fae9fb7a3fe355a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/25/2019
ms.locfileid: "68499244"
---
# <a name="tutorial-azure-active-directory-integration-with-docusign"></a>Kurz: Integrace Azure Active Directory s DocuSign

V tomto kurzu se dozvíte, jak integrovat DocuSign s Azure Active Directory (Azure AD).
Integrace DocuSign s Azure AD poskytuje následující výhody:

* Můžete kontrolovat v Azure AD, kteří mají přístup k DocuSign.
* Můžete povolit, aby se vaši uživatelé automaticky přihlásili k DocuSign (jednotné přihlašování) pomocí svých účtů Azure AD.
* Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete získat další podrobnosti o integraci aplikace SaaS s Azure AD, přečtěte si téma [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Ke konfiguraci integrace služby Azure AD s DocuSign potřebujete následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat [bezplatný účet](https://azure.microsoft.com/free/) .
* Předplatné s povoleným DocuSignm jednotným přihlašováním

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* DocuSign podporuje jednotné přihlašování iniciované v **SP**

* DocuSign podporuje [Automatické zřizování uživatelů](https://docs.microsoft.com/azure/active-directory/saas-apps/dropboxforbusiness-provisioning-tutorial) .

## <a name="adding-docusign-from-the-gallery"></a>Přidání DocuSign z Galerie

Pokud chcete nakonfigurovat integraci DocuSign do služby Azure AD, musíte přidat DocuSign z Galerie do svého seznamu spravovaných aplikací SaaS.

**Pokud chcete přidat DocuSign z Galerie, proveďte následující kroky:**

1. V **[webu Azure portal](https://portal.azure.com)** , v levém navigačním panelu klikněte na **Azure Active Directory** ikonu.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte na **podnikové aplikace** a vyberte možnost **všechny aplikace** .

    ![V okně podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **Docusign**, vyberte **Docusign** z panelu výsledků a potom kliknutím na tlačítko **Přidat** přidejte aplikaci.

     ![DocuSign v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD pomocí DocuSign na základě testovacího uživatele s názvem **Britta Simon**.
Aby jednotné přihlašování fungovalo, musí se zřídit vztah propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v DocuSign.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí DocuSign, musíte dokončit tyto stavební bloky:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Nakonfigurujte jednotné přihlašování Docusign](#configure-docusign-single-sign-on)** – ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
3. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
4. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
5. **[Vytvoření Docusign Test User](#create-docusign-test-user)** – pro Britta Simon v Docusign, který je propojený s reprezentací uživatele Azure AD.
6. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části povolíte jednotné přihlašování Azure AD v Azure Portal.

Pokud chcete nakonfigurovat jednotné přihlašování Azure AD pomocí DocuSign, proveďte následující kroky:

1. V [Azure Portal](https://portal.azure.com/)na stránce integrace aplikací **Docusign** vyberte **jednotné přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz](common/select-sso.png)

2. V dialogovém okně **Vyberte metodu jednotného přihlašování** vyberte možnost režim **SAML/WS** , čímž povolíte jednotné přihlašování.

    ![Režim výběru jednotného přihlašování](common/select-saml-option.png)

3. Na **nastavte si jednotné přihlašování pomocí SAML** klikněte na **upravit** ikony otevřete **základní konfiguraci SAML** dialogového okna.

    ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. V části **základní konfigurace SAML** proveďte následující kroky:

    ![Informace o jednotném přihlašování v doméně DocuSign a adresách URL](common/sp-identifier.png)

    a. Do textového pole **přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru:`https://<subdomain>.docusign.com/organizations/<OrganizationID>/saml2/login/sp/<IDPID>`

    b. Do textového pole **identifikátor (ID entity)** zadejte adresu URL pomocí následujícího vzoru:`https://<subdomain>.docusign.com/organizations/<OrganizationID>/saml2`

    > [!NOTE]
    > Tyto hodnoty nejsou reálné. Aktualizujte tyto hodnoty pomocí skutečné přihlašovací adresy URL a identifikátoru, který je vysvětlen později v tomto kurzu v části **koncové body SAML 2,0** .

5. Na stránce **nastavit jednotné přihlašování pomocí SAML** v části **podpisový certifikát SAML** klikněte na **Stáhnout** a Stáhněte si **certifikát (Base64)** z daných možností podle vašich požadavků a uložte ho do svého počítače.

    ![Odkaz ke stažení certifikátu](common/certificatebase64.png)

6. V části **Nastavení Docusign** zkopírujte příslušné adresy URL podle vašich požadavků.

    ![Kopírovat adresy URL konfigurace](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor Azure AD

    c. Adresa URL – odhlášení

### <a name="configure-docusign-single-sign-on"></a>Konfigurace jednotného přihlašování DocuSign

1. V jiném okně webového prohlížeče se přihlaste k **portálu pro správu Docusign** jako správce.

2. V pravém horním rohu stránky klikněte na **logo** Profile a potom klikněte na **Přejít na správce**.
  
    ![Konfiguruje se jednotné přihlašování.][51]

3. Na stránce řešení domény klikněte na **domény** .

    ![Konfiguruje se jednotné přihlašování.][50]

4. V části **domény** klikněte na možnost **doména deklarací identity**.

    ![Konfiguruje se jednotné přihlašování.][52]

5. V dialogovém okně **deklarace identity a domény** zadejte do textového pole **název domény** doménu vaší společnosti a potom klikněte na **deklarace identity**. Ujistěte se, že jste ověřili doménu a je stav aktivní.

    ![Konfiguruje se jednotné přihlašování.][53]

6. Na stránce řešení domény klikněte na **Zprostředkovatelé identity**.
  
    ![Konfiguruje se jednotné přihlašování.][54]

7. V části **Zprostředkovatelé identity** klikněte na **Přidat zprostředkovatele identity**. 

    ![Konfiguruje se jednotné přihlašování.][55]

8. Na stránce **nastavení zprostředkovatele identity** proveďte následující kroky:

    ![Konfiguruje se jednotné přihlašování.][56]

    a. Do textového pole **název** zadejte jedinečný název pro vaši konfiguraci. Nepoužívejte mezery.

    b. Do **textového pole vystavitele zprostředkovatele identity**vložte hodnotu **identifikátoru Azure AD**, kterou jste zkopírovali z Azure Portal.

    c. Do textového pole **Adresa URL pro přihlášení zprostředkovatele identity** vložte hodnotu **adresy URL pro přihlášení**, kterou jste zkopírovali z Azure Portal.

    d. Do textového pole **Adresa URL pro odhlášení zprostředkovatele identity** vložte hodnotu **URL**pro odhlášení, kterou jste zkopírovali z Azure Portal.

    e. Vyberte **Sign Authn Request (podepsat požadavek na podpis**).

    f. Jako **odešlete žádost o Authn podle**, vyberte **post**(Odeslat).

    g. Jako **Odeslat žádost o odhlášení**vyberte **získat**.

    h. V oddílu **mapování vlastních atributů** klikněte na **Přidat nové mapování**.

    ![Konfiguruje se jednotné přihlašování.][62]

    i. Vyberte pole, které chcete namapovat na deklaraci identity Azure AD. V tomto příkladu je deklarace identity **EmailAddress** namapovaná s hodnotou **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress** . Jedná se o výchozí název deklarace identity z Azure AD pro e-mailovou deklaraci identity a pak klikněte na **Uložit**.

    ![Konfiguruje se jednotné přihlašování.][57]

    > [!NOTE]
    > Použijte odpovídající **identifikátor uživatele** k mapování uživatele z Azure AD na Docusign mapování uživatelů. Vyberte správné pole a zadejte odpovídající hodnotu na základě nastavení vaší organizace.

    j. V části **certifikáty poskytovatele identity** klikněte na **Přidat certifikát**a pak Nahrajte certifikát, který jste stáhli z portálu Azure AD, a klikněte na **Uložit**.

    ![Konfiguruje se jednotné přihlašování.][58]

    k. V části **Zprostředkovatelé identity** klikněte na **Akce**a potom na **koncové body**.

    ![Konfiguruje se jednotné přihlašování.][59]

    l. V části **zobrazení koncových bodů SAML 2,0** na **portálu pro správu Docusign**proveďte následující kroky:

    ![Konfiguruje se jednotné přihlašování.][60]

    * Zkopírujte **adresu URL vystavitele poskytovatele služby**a vložte ji do textového  pole identifikátoru v **základní části Konfigurace SAML** na Azure Portal.

    * Zkopírujte **přihlašovací adresu URL poskytovatele služby**a vložte ji do textového pole pro **přihlašovací adresu URL** v **základní části Konfigurace SAML** na Azure Portal.

    * Klikněte na **Zavřít** .

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

V této části povolíte Britta Simon pro použití jednotného přihlašování pomocí Azure tím, že udělíte přístup k DocuSign.

1. V Azure Portal vyberte **podnikové aplikace**, vyberte **všechny aplikace**a pak vyberte **Docusign**.

    ![Okno podnikových aplikací](common/enterprise-applications.png)

2. V seznamu aplikace vyberte **Docusign**.

    ![Odkaz DocuSign v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **Uživatelé a skupiny**.

    ![Odkaz "Uživatele a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V **uživatelů a skupin** dialogové okno Vybrat **Britta Simon** v seznamu uživatelů, klikněte **vyberte** tlačítko v dolní části obrazovky.

6. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, pak v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.

7. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

### <a name="create-docusign-test-user"></a>Vytvořit testovacího uživatele DocuSign

V této části se v DocuSign vytvoří uživatel s názvem Britta Simon. DocuSign podporuje zřizování uživatelů za běhu, což je ve výchozím nastavení povolené. V této části není žádná položka akce. Pokud uživatel ještě v DocuSign neexistuje, vytvoří se po ověření nový.

>[!Note]
>Pokud potřebujete ručně vytvořit uživatele, obraťte se na [tým podpory Docusign](https://support.docusign.com/).

### <a name="test-single-sign-on"></a>Test jednotného přihlašování 

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Když na přístupovém panelu kliknete na dlaždici DocuSign, měli byste se automaticky přihlásit k DocuSign, pro které jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další prostředky

- [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

<!--Image references-->

[50]: ./media/docusign-tutorial/tutorial_docusign_18.png
[51]: ./media/docusign-tutorial/tutorial_docusign_21.png
[52]: ./media/docusign-tutorial/tutorial_docusign_22.png
[53]: ./media/docusign-tutorial/tutorial_docusign_23.png
[54]: ./media/docusign-tutorial/tutorial_docusign_19.png
[55]: ./media/docusign-tutorial/tutorial_docusign_20.png
[56]: ./media/docusign-tutorial/tutorial_docusign_24.png
[57]: ./media/docusign-tutorial/tutorial_docusign_25.png
[58]: ./media/docusign-tutorial/tutorial_docusign_26.png
[59]: ./media/docusign-tutorial/tutorial_docusign_27.png
[60]: ./media/docusign-tutorial/tutorial_docusign_28.png
[61]: ./media/docusign-tutorial/tutorial_docusign_29.png
[62]: ./media/docusign-tutorial/tutorial_docusign_30.png
