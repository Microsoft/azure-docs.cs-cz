---
title: 'Kurz: Azure Active Directory integrace se sadou odQuestetrach BPM | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Questetra BPM.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/27/2019
ms.author: jeedes
ms.openlocfilehash: 943aba9c25df6cd279dc651cfad343a6509c069b
ms.sourcegitcommit: ae6e7057a00d95ed7b828fc8846e3a6281859d40
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/16/2020
ms.locfileid: "92108673"
---
# <a name="tutorial-azure-active-directory-integration-with-questetra-bpm-suite"></a>Kurz: Azure Active Directory integrace se sadou BPM pro Questetra

V tomto kurzu se naučíte integrovat sadu Questetra BPM pomocí služby Azure Active Directory (Azure AD).
Integrace Questetra BPM Suite s Azure AD poskytuje následující výhody:

* Můžete kontrolovat v Azure AD, kteří mají přístup k sadě Questetra BPM.
* Uživatelům můžete povolit, aby se automaticky přihlásili do sady Questetra BPM (jednotné přihlašování) pomocí svých účtů Azure AD.
* Účty můžete spravovat v jednom centrálním umístění – Azure Portal.

Pokud chcete získat další podrobnosti o integraci aplikace SaaS s Azure AD, přečtěte si téma [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud předplatné Azure ještě nemáte, napřed si [vytvořte bezplatný účet](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Předpoklady

Ke konfiguraci integrace služby Azure AD se sadou Questetra BPM potřebujete následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční zkušební verzi [tady](https://azure.microsoft.com/pricing/free-trial/) .
* Odběr povoleného jednotného přihlašování Questetra BPM Suite

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Sada Questetra BPM podporuje jednotné přihlašování v **SP** .

## <a name="adding-questetra-bpm-suite-from-the-gallery"></a>Přidání sady BPM Questetra z Galerie

Pokud chcete nakonfigurovat integraci Questetra BPM Suite do Azure AD, musíte do seznamu spravovaných aplikací pro SaaS přidat z Galerie Questetray BPM Suite.

**Chcete-li přidat sadu BPM Questetra z Galerie, proveďte následující kroky:**

1. V **[Azure Portal](https://portal.azure.com)** na levém navigačním panelu klikněte na ikonu **Azure Active Directory** .

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte na **podnikové aplikace** a vyberte možnost **všechny aplikace** .

    ![Okno podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **Nová aplikace** v horní části dialogového okna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **QUESTETRA BPM Suite**, vyberte **Questetray BPM Suite** z panelu výsledků a potom kliknutím na tlačítko **Přidat** přidejte aplikaci.

     ![Sada Questetra BPM v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD se sadou Questetra BPM na základě testovacího uživatele s názvem **Britta Simon**.
Aby jednotné přihlašování fungovalo, je potřeba zřídit vztah odkazu mezi uživatelem služby Azure AD a souvisejícím uživatelem v sadě Questetra BPM.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD se sadou Questetra BPM, musíte dokončit tyto stavební bloky:

1. **[Nakonfigurujte jednotné přihlašování Azure AD](#configure-azure-ad-single-sign-on)** a Umožněte uživatelům používat tuto funkci.
2. **[Nakonfigurujte jednotné přihlašování QUESTETRA BPM Suite](#configure-questetra-bpm-suite-single-sign-on)** – ke konfiguraci nastavení jediného Sign-On na straně aplikace.
3. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí Britta Simon.
4. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – pro povolení Britta Simon pro použití jednotného přihlašování Azure AD.
5. **[Vytvořte testovacího uživatele QUESTETRA BPM Suite](#create-questetra-bpm-suite-test-user)** – abyste měli protějšek Britta Simon v sadě Questetra BPM, který je propojený s reprezentací uživatele Azure AD.
6. **[Otestujte jednotné přihlašování](#test-single-sign-on)** – ověřte, jestli konfigurace funguje.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace jednotného přihlašování Azure AD

V této části povolíte jednotné přihlašování Azure AD v Azure Portal.

Pokud chcete nakonfigurovat jednotné přihlašování Azure AD pomocí sady Questetra BPM, proveďte následující kroky:

1. V [Azure Portal](https://portal.azure.com/)na stránce integrace aplikace **Questetra BPM Suite** vyberte **jednotné přihlašování**.

    ![Konfigurovat odkaz jednotného přihlašování](common/select-sso.png)

2. V dialogovém okně **Vyberte metodu jednotného přihlašování** vyberte možnost režim **SAML/WS** , čímž povolíte jednotné přihlašování.

    ![Režim výběru jednotného přihlašování](common/select-saml-option.png)

3. Na stránce **nastavit jeden Sign-On s SAML** klikněte na **Upravit** ikona a otevře se základní dialogové okno **Konfigurace SAML** .

    ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. V části **základní konfigurace SAML** proveďte následující kroky:

    ![Informace o jednotném přihlašování k doméně a adresám URL pro Questetra BPM Suite](common/sp-identifier.png)

    a. Do textového pole **přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru: `https://<subdomain>.questetra.net/saml/SSO/alias/bpm`

    b. Do textového pole **identifikátor (ID entity)** zadejte adresu URL pomocí následujícího vzoru: `https://<subdomain>.questetra.net/`

    > [!NOTE]
    > Tyto hodnoty nejsou reálné. Aktualizujte tyto hodnoty skutečným Sign-On URL a identifikátorem. Tyto hodnoty můžete získat z části **informace o SP** na webu společnosti **Questetra BPM Suite** , který je vysvětlen dále v kurzu, nebo se obraťte na [tým podpory pro klienty podpory Questetra](https://www.questetra.com/contact/). Můžete se také podívat na vzory uvedené v části **základní konfigurace SAML** v Azure Portal.

5. Na stránce **nastavit jeden Sign-On se** stránkou SAML v části **podpisový certifikát SAML** klikněte na **Stáhnout** a Stáhněte si **certifikát (Base64)** z daných možností podle vašich požadavků a uložte ho do svého počítače.

    ![Odkaz na stažení certifikátu](common/certificatebase64.png)

6. V části **Set the QUESTETRA BPM Suite** zkopírujte příslušné adresy URL podle vašich požadavků.

    ![Kopírovat adresy URL konfigurace](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor Azure AD

    c. Odhlašovací adresa URL

### <a name="configure-questetra-bpm-suite-single-sign-on"></a>Konfigurace Questetra BPM Suite Single Sign-On

1. V jiném okně webového prohlížeče se přihlaste k webu společnosti **QUESTETRA BPM Suite** jako správce.

2. V nabídce v horní části klikněte na **nastavení systému**. 
   
    ![Snímek obrazovky zobrazuje nastavení systému vybraná z webu společnosti Questetra BPM Suite.][10]

3. Pokud chcete otevřít stránku **SingleSignOnSAML** , klikněte na **SSO (SAML)**. 
   
    ![Snímek obrazovky S vybraným výstupem (SAML) zobrazuje.][11]

4. Na webu společnosti **QUESTETRA BPM Suite** v části **informace o SP** proveďte následující kroky:

    a. Zkopírujte **adresu URL služby ACS**a vložte ji do textového pole **přihlašovací adresa URL** v základní části **Konfigurace SAML** z Azure Portal.
    
    b. Zkopírujte **ID entity**a vložte ji do textového pole **identifikátor** v **základní části Konfigurace SAML** z Azure Portal.

5. Na webu společnosti **QUESTETRA BPM Suite** proveďte následující kroky: 
   
    ![Konfigurace jednoho Sign-On][15]
   
    a. Vyberte **Povolit jednotné přihlašování**.
   
    b. Do textového pole **ID entity** vložte hodnotu **identifikátoru služby Azure AD** , který jste zkopírovali z Azure Portal.
    
    c. Do textového pole **Adresa URL přihlašovací stránky** vložte hodnotu **adresy URL pro přihlášení** , kterou jste zkopírovali z Azure Portal.
    
    d. Do textového pole **Adresa URL stránky** pro odhlášení vložte hodnotu **adresy URL pro odhlášení** , kterou jste zkopírovali z Azure Portal.
    
    e. Do textového pole **Formát NameId** zadejte `urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress` .

    f. Otevřete v programu Poznámkový blok certifikát s kódováním **Base-64** stažený z Azure Portal, zkopírujte jeho obsah do schránky a vložte ho do textového pole pro **ověření certifikátu** . 

    například Klikněte na **Uložit**.

### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD 

Cílem této části je vytvořit testovacího uživatele v Azure Portal s názvem Britta Simon.

1. V Azure Portal v levém podokně vyberte možnost **Azure Active Directory**, vyberte možnost **Uživatelé**a potom vyberte možnost **Všichni uživatelé**.

    ![Odkazy "uživatelé a skupiny" a "Všichni uživatelé"](common/users.png)

2. V horní části obrazovky vyberte **Nový uživatel** .

    ![Tlačítko pro nového uživatele](common/new-user.png)

3. Ve vlastnostech uživatele proveďte následující kroky.

    ![Uživatelský dialog](common/user-properties.png)

    a. Do pole **název** zadejte **BrittaSimon**.
  
    b. Do pole **uživatelské jméno** zadejte brittasimon@yourcompanydomain.extension . Například BrittaSimon@contoso.com.

    c. Zaškrtněte políčko **Zobrazit heslo** a pak zapište hodnotu, která se zobrazí v poli heslo.

    d. Klikněte na **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte Britta Simon pro použití jednotného přihlašování pomocí Azure tím, že udělíte přístup k sadě Questetra BPM.

1. V Azure Portal vyberte možnost **podnikové aplikace**, vyberte možnost **všechny aplikace**a pak vyberte možnost **Questetra BPM Suite**.

    ![Okno Podnikové aplikace](common/enterprise-applications.png)

2. V seznamu aplikace vyberte **QUESTETRA BPM Suite**.

    ![Odkaz na sadu Questetra BPM v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **Uživatelé a skupiny**.

    ![Odkaz uživatelé a skupiny](common/users-groups-blade.png)

4. Klikněte na tlačítko **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Podokno přidat přiřazení](common/add-assign-user.png)

5. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **Britta Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.

6. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, pak v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.

7. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

### <a name="create-questetra-bpm-suite-test-user"></a>Vytvořit testovacího uživatele sady BPM Questetra

Cílem této části je vytvořit uživatele s názvem Britta Simon v sadě Questetra BPM.

**Pokud chcete v sadě Questetra BPM vytvořit uživatele s názvem Britta Simon, proveďte následující kroky:**

1. Přihlaste se k webu společnosti Questetra BPM Suite jako správce.

2. Přejít na **nastavení systému > seznam uživatelů > nový uživatel**.
 
3. V dialogovém okně Nový uživatel proveďte následující kroky: 
   
    ![Vytvořit testovacího uživatele][300] 
   
    a. Do textového pole **název** zadejte **jméno** uživatele britta.simon@contoso.com .
   
    b. Do textového pole **e-mail** zadejte **e-mail** uživatele britta.simon@contoso.com .
   
    c. Do textového pole **heslo** zadejte **heslo** uživatele.
    
    d. Klikněte na tlačítko **Přidat nového uživatele**.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování 

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí přístupového panelu.

Po kliknutí na dlaždici Questetra BPM Suite na přístupovém panelu byste měli být automaticky přihlášení do sady Questetra BPM, pro kterou jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další materiály

- [Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

<!--Image references-->

[10]: ./media/questetra-bpm-suite-tutorial/questera_bpm_suite_03.png
[11]: ./media/questetra-bpm-suite-tutorial/questera_bpm_suite_04.png
[15]: ./media/questetra-bpm-suite-tutorial/questera_bpm_suite_08.png
[300]: ./media/questetra-bpm-suite-tutorial/questera_bpm_suite_11.png