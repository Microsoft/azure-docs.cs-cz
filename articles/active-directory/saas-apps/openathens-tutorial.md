---
title: 'Kurz: Azure Active Directory integrace s OpenAthens | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a OpenAthens.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: dd4adfc7-e238-41d5-8b25-1811f08078b6
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 1/4/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 81e37e78246a8c021cfdea015a9c4fe7695f5e44
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/25/2019
ms.locfileid: "72933986"
---
# <a name="tutorial-azure-active-directory-integration-with-openathens"></a>Kurz: Azure Active Directory integrace s OpenAthens

V tomto kurzu se dozvíte, jak integrovat OpenAthens s Azure Active Directory (Azure AD).
Integrace OpenAthens s Azure AD poskytuje následující výhody:

* Můžete kontrolovat v Azure AD, kteří mají přístup k OpenAthens.
* Můžete povolit, aby se vaši uživatelé automaticky přihlásili k OpenAthens (jednotné přihlašování) pomocí svých účtů Azure AD.
* Účty můžete spravovat v jednom centrálním umístění – Azure Portal.

Pokud chcete získat další podrobnosti o integraci aplikace SaaS s Azure AD, přečtěte si téma [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Předpoklady

Ke konfiguraci integrace služby Azure AD s OpenAthens potřebujete následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční zkušební verzi [tady](https://azure.microsoft.com/pricing/free-trial/) .
* Předplatné s povoleným OpenAthensm jednotným přihlašováním

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* OpenAthens podporuje jednotné přihlašování **IDP** .

* OpenAthens podporuje zřizování uživatelů **jenom v čase** .

## <a name="adding-openathens-from-the-gallery"></a>Přidání OpenAthens z Galerie

Pokud chcete nakonfigurovat integraci OpenAthens do služby Azure AD, musíte přidat OpenAthens z Galerie do svého seznamu spravovaných aplikací SaaS.

**Pokud chcete přidat OpenAthens z Galerie, proveďte následující kroky:**

1. V **[Azure Portal](https://portal.azure.com)** na levém navigačním panelu klikněte na ikonu **Azure Active Directory** .

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte na **podnikové aplikace** a vyberte možnost **všechny aplikace** .

    ![Okno podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **Nová aplikace** v horní části dialogového okna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **OpenAthens**, vyberte **OpenAthens** z panelu výsledků a potom kliknutím na tlačítko **Přidat** přidejte aplikaci.

     ![OpenAthens v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD pomocí OpenAthens na základě testovacího uživatele s názvem **Britta Simon**.
Aby jednotné přihlašování fungovalo, musí se zřídit vztah propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v OpenAthens.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí OpenAthens, musíte dokončit tyto stavební bloky:

1. **[Nakonfigurujte jednotné přihlašování Azure AD](#configure-azure-ad-single-sign-on)** a Umožněte uživatelům používat tuto funkci.
2. **[Nakonfigurujte jednotné přihlašování OpenAthens](#configure-openathens-single-sign-on)** – ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
3. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí Britta Simon.
4. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – pro povolení Britta Simon pro použití jednotného přihlašování Azure AD.
5. **[Vytvoření OpenAthens Test User](#create-openathens-test-user)** – pro Britta Simon v OpenAthens, který je propojený s reprezentací uživatele Azure AD.
6. **[Otestujte jednotné přihlašování](#test-single-sign-on)** – ověřte, jestli konfigurace funguje.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace jednotného přihlašování Azure AD

V této části povolíte jednotné přihlašování Azure AD v Azure Portal.

Pokud chcete nakonfigurovat jednotné přihlašování Azure AD pomocí OpenAthens, proveďte následující kroky:

1. V [Azure Portal](https://portal.azure.com/)na stránce integrace aplikací **OpenAthens** vyberte **jednotné přihlašování**.

    ![Konfigurovat odkaz jednotného přihlašování](common/select-sso.png)

2. V dialogovém okně **Vyberte metodu jednotného přihlašování** vyberte možnost režim **SAML/WS** , čímž povolíte jednotné přihlašování.

    ![Režim výběru jednotného přihlašování](common/select-saml-option.png)

3. Na stránce **nastavit jednotné přihlašování pomocí SAML** klikněte na **Upravit** ikona a otevře se základní dialogové okno **Konfigurace SAML** .

    ![Upravit základní konfiguraci SAML](common/edit-urls.png)

5. V části **základní konfigurace SAML** nahrajte **soubor metadat poskytovatele služby**. Postup najdete dále v tomto kurzu.

    a. Klikněte na **nahrát soubor metadat**.

    ![metadata pro nahrávání openathens](common/upload-metadata.png)

    b. Kliknutím na **logo složky** vyberte soubor metadat a klikněte na **nahrát**.

    ![Openathens procházet metadata pro odesílání](common/browse-upload-metadata.png)

    c. Po úspěšném nahrání souboru metadat se hodnota **identifikátoru** automaticky naplní v **základním** TEXTOVÉM poli konfiguračního oddílu SAML:

    ![Informace o jednotném přihlašování v doméně OpenAthens a adresách URL](common/idp-identifier.png)

6. Na stránce **nastavit jednotné přihlašování pomocí SAML** v části **podpisový certifikát SAML** klikněte na **Stáhnout** a Stáhněte si **XML federačních metadat** z daných možností podle vašich požadavků a uložte ho do svého počítače.

    ![Odkaz na stažení certifikátu](common/metadataxml.png)

### <a name="configure-openathens-single-sign-on"></a>Konfigurace jednotného přihlašování OpenAthens

1. V jiném okně webového prohlížeče se přihlaste k webu OpenAthens společnosti jako správce.

2. V seznamu vyberte **připojení** na kartě **Správa** . 

    ![Konfigurace jednotného přihlašování](./media/openathens-tutorial/tutorial_openathens_application1.png)

3. Vyberte **SAML 1.1/2.0**a pak klikněte na tlačítko **Konfigurovat** .

    ![Konfigurace jednotného přihlašování](./media/openathens-tutorial/tutorial_openathens_application2.png)
    
4. Chcete-li přidat konfiguraci, vyberte tlačítko **Procházet** a odešlete soubor metadata. XML, který jste stáhli z Azure Portal, a pak vyberte **Přidat**.

    ![Konfigurace jednotného přihlašování](./media/openathens-tutorial/tutorial_openathens_application3.png)

5. Na kartě **Podrobnosti** proveďte následující kroky.

    ![Konfigurace jednotného přihlašování](./media/openathens-tutorial/tutorial_openathens_application4.png)

    a. V **mapování zobrazovaný název**vyberte **použít atribut**.

    b. Do textového pole **atribut zobrazovaného názvu** zadejte hodnotu `http://schemas.microsoft.com/identity/claims/displayname`.
    
    c. V **mapování jedinečného uživatele**vyberte **použít atribut**.

    d. Do textového pole **jedinečný atribut uživatele** zadejte hodnotu `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`.

    e. V části **stav**zaškrtněte políčko všechna tři zaškrtávací políčka.

    f. V nástroji **vytvořit místní účty**vyberte možnost **automaticky**.

    g. Vyberte **Uložit změny**.

    h. Na kartě **</> předávající strana** zkopírujte **adresu URL metadat** a otevřete ji v prohlížeči a Stáhněte si soubor **XML s metadaty SP** . Nahrajte tento soubor metadat SP v části **základní konfigurační oddíl SAML** ve službě Azure AD.

    ![Konfigurace jednotného přihlašování](./media/openathens-tutorial/tutorial_openathens_application5.png)

### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD 

Cílem této části je vytvořit testovacího uživatele v Azure Portal s názvem Britta Simon.

1. V Azure Portal v levém podokně vyberte možnost **Azure Active Directory**, vyberte možnost **Uživatelé**a potom vyberte možnost **Všichni uživatelé**.

    ![Odkazy "uživatelé a skupiny" a "Všichni uživatelé"](common/users.png)

2. V horní části obrazovky vyberte **Nový uživatel** .

    ![Tlačítko pro nového uživatele](common/new-user.png)

3. Ve vlastnostech uživatele proveďte následující kroky.

    ![Uživatelský dialog](common/user-properties.png)

    a. Do pole **název** zadejte **BrittaSimon**.
  
    b. Do pole **uživatelské jméno** zadejte **brittasimon\@yourcompanydomain. extension.**  
    Například BrittaSimon@contoso.com.

    c. Zaškrtněte políčko **Zobrazit heslo** a pak zapište hodnotu, která se zobrazí v poli heslo.

    d. Klikněte na **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte Britta Simon pro použití jednotného přihlašování pomocí Azure tím, že udělíte přístup k OpenAthens.

1. V Azure Portal vyberte **podnikové aplikace**, vyberte **všechny aplikace**a pak vyberte **OpenAthens**.

    ![Okno podnikových aplikací](common/enterprise-applications.png)

2. V seznamu aplikace zadejte a vyberte **OpenAthens**.

    ![Odkaz OpenAthens v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **Uživatelé a skupiny**.

    ![Odkaz uživatelé a skupiny](common/users-groups-blade.png)

4. Klikněte na tlačítko **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Podokno přidat přiřazení](common/add-assign-user.png)

5. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **Britta Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.

6. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, pak v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.

7. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

### <a name="create-openathens-test-user"></a>Vytvořit testovacího uživatele OpenAthens

V této části se v OpenAthens vytvoří uživatel s názvem Britta Simon. OpenAthens podporuje **zřizování uživatelů za běhu**, což je ve výchozím nastavení povolené. V této části není žádná položka akce. Pokud uživatel ještě v OpenAthens neexistuje, vytvoří se po ověření nový.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování 

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí přístupového panelu.

Když na přístupovém panelu kliknete na dlaždici OpenAthens, měli byste se automaticky přihlásit k OpenAthens, pro které jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další materiály

- [Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

