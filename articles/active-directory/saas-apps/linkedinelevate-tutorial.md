---
title: 'Kurz: Azure Active Directory integrace se zvýšením úrovně LinkedInu | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a se zvýšením úrovně LinkedInu.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 2ad9941b-c574-42c3-bd0f-5d6ec68537ef
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/17/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5b7cb8d6ab34a632e36ea2fd1c87005a038bc523
ms.sourcegitcommit: 3073581d81253558f89ef560ffdf71db7e0b592b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/06/2019
ms.locfileid: "68823710"
---
# <a name="tutorial-azure-active-directory-integration-with-linkedin-elevate"></a>Kurz: Azure Active Directory integrace se zvýšením úrovně LinkedInu

V tomto kurzu se naučíte integrovat zvýšení úrovně LinkedInu pomocí Azure Active Directory (Azure AD).
Integrace zvýšení úrovně LinkedInu pomocí Azure AD poskytuje následující výhody:

* Můžete kontrolovat v Azure AD, kteří mají přístup ke zvýšení oprávnění LinkedInu.
* Uživatelům můžete povolit, aby se automaticky přihlásili ke zvýšení úrovně LinkedInu (jednotné přihlašování) se svými účty Azure AD.
* Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete získat další podrobnosti o integraci aplikace SaaS s Azure AD, přečtěte si téma [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Ke konfiguraci integrace služby Azure AD se zvýšením úrovně LinkedInu potřebujete následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat [bezplatný účet](https://azure.microsoft.com/free/) .
* Předplatné LinkedIn úrovně Standard s povoleným jednotným přihlašováním

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Zvýšení oprávnění LinkedInu podporuje **SP a IDP** iniciované jednotné přihlašování

* Zvýšení oprávnění LinkedInu podporuje **jenom při** zřizování uživatelů

* Zvýšení oprávnění LinkedInu podporuje [ **automatizované** zřizování uživatelů](linkedinelevate-provisioning-tutorial.md)

## <a name="adding-linkedin-elevate-from-the-gallery"></a>Přidání zvýšení oprávnění LinkedInu z Galerie

Pokud chcete nakonfigurovat integraci LinkedInu v Azure AD, musíte do seznamu spravovaných aplikací SaaS přidat zvýšení úrovně LinkedInu z galerie.

**Pokud chcete přidat zvýšení oprávnění LinkedInu z Galerie, proveďte následující kroky:**

1. V **[webu Azure portal](https://portal.azure.com)** , v levém navigačním panelu klikněte na **Azure Active Directory** ikonu.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte na **podnikové aplikace** a vyberte možnost **všechny aplikace** .

    ![V okně podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **zvýšení oprávnění LinkedInu**, vyberte možnost povýšení **LinkedInu** z panelu výsledků a potom kliknutím na tlačítko **Přidat** přidejte aplikaci.

    ![Zvýšení úrovně LinkedInu v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD se zvýšením úrovně LinkedInu na základě testovacího uživatele s názvem **Britta Simon**.
Aby jednotné přihlašování fungovalo, musí být navázán vztah odkazu mezi uživatelem služby Azure AD a souvisejícím uživatelem v rámci zvýšení oprávnění LinkedInu.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD se zvýšením úrovně LinkedInu, je potřeba, abyste dokončili tyto stavební bloky:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Nakonfigurujte jednotné přihlašování](#configure-linkedin-elevate-single-sign-on)** na LinkedInu a nakonfigurujete nastavení jednotného přihlašování na straně aplikace.
3. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
4. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
5. **[Vytvoření testovacího uživatele zvýšení úrovně LinkedInu](#create-linkedin-elevate-test-user)** – Chcete-li mít protějšek Britta Simon ve službě LinkedIn, který je propojený s reprezentací uživatele Azure AD.
6. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části povolíte jednotné přihlašování Azure AD v Azure Portal.

Pokud chcete nakonfigurovat jednotné přihlašování Azure AD pomocí zvýšení úrovně LinkedInu, proveďte následující kroky:

1. V [Azure Portal](https://portal.azure.com/)na stránce pro integraci aplikace na webu **LinkedIn – zvýšení úrovně** vyberte **jednotné přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz](common/select-sso.png)

2. V dialogovém okně **Vyberte metodu jednotného přihlašování** vyberte možnost režim **SAML/WS** , čímž povolíte jednotné přihlašování.

    ![Režim výběru jednotného přihlašování](common/select-saml-option.png)

3. Na **nastavte si jednotné přihlašování pomocí SAML** klikněte na **upravit** ikony otevřete **základní konfiguraci SAML** dialogového okna.

    ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. Pokud chcete nakonfigurovat aplikaci v režimu iniciované **IDP** , proveďte v **základní části Konfigurace SAML** následující kroky:

    ![Informace o jednotném přihlašování v doméně a adrese WebSign-on pro LinkedIn](common/idp-intiated.png)

    a. Do textového pole **Identifier (identifikátor** ) zadejte hodnotu **ID entity** , kterou zkopírujete z portálu LinkedIn, který se vysvětluje dále v tomto kurzu.

    b. Do textového pole **Adresa URL odpovědi** zadejte hodnotu **adresy URL pro řízení přístupu uživatele (ACS)** . na portálu LinkedIn je vysvětleno později v tomto kurzu.

5. Klikněte na **nastavit další adresy URL** a proveďte následující krok, pokud chcete nakonfigurovat aplikaci v režimu iniciované **SP** :

    ![Informace o jednotném přihlašování v doméně a adrese WebSign-on pro LinkedIn](common/metadata-upload-additional-signon.png)

    Do textového pole **přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru:`https://www.linkedin.com/checkpoint/enterprise/login/<AccountId>?application=elevate&applicationInstanceId=<InstanceId>`

6. Žádost o zvýšení úrovně LinkedInu očekává, že se kontrolní výrazy SAML v určitém formátu, což vyžaduje přidání mapování vlastních atributů do konfigurace atributů tokenu SAML. Následující snímek obrazovky ukazuje seznam výchozích atributů, kde **NameIdentifier** je mapován pomocí **User. userPrincipalName**. Žádost o zvýšení úrovně LinkedInu očekává, že NameIdentifier budou namapovány pomocí **User. mail**, takže potřebujete upravit mapování atributů kliknutím na ikonu Upravit a změnit mapování atributů.

    ![image](common/edit-attribute.png)

7. Kromě toho bude aplikace LinkedIn zvýšení oprávnění v odpovědi SAML v reakci na několik atributů předána zpátky. V části deklarace identity uživatelů v dialogovém okně **atributy uživatele** proveďte následující kroky pro přidání atributu tokenu SAML, jak je znázorněno v následující tabulce:

    | Name | Zdrojový atribut|
    | -------| -------------|
    | Oddělení | User. Department |

    a. Kliknutím na **Přidat novou deklaraci identity** otevřete dialogové okno **Spravovat deklarace identity uživatelů** .

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. Do textového pole **název** zadejte název atributu zobrazeného pro tento řádek.

    c. Ponechte **obor názvů** prázdný.

    d. Jako **atribut**vyberte zdroj.

    e. V seznamu **zdrojový atribut** zadejte hodnotu atributu zobrazenou pro tento řádek.

    f. Klikněte na tlačítko **Ok**

    g. Klikněte na **Uložit**.

8. Na stránce **nastavit jednotné přihlašování pomocí SAML** v části **podpisový certifikát SAML** klikněte na **Stáhnout** a Stáhněte si **XML federačních metadat** z daných možností podle vašich požadavků a uložte ho do svého počítače.

    ![Odkaz ke stažení certifikátu](common/metadataxml.png)

9. V části **nastavit zvýšení oprávnění LinkedInu** zkopírujte příslušné adresy URL podle vašich požadavků.

    ![Kopírovat adresy URL konfigurace](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor Azure AD

    c. Adresa URL – odhlášení

### <a name="configure-linkedin-elevate-single-sign-on"></a>Konfigurace jednotného přihlašování na LinkedInu

1. V jiném okně webového prohlížeče se přihlaste do svého tenanta LinkedInu jako správce.

1. V **centru účtů**klikněte na **globální nastavení** v části **Nastavení**. Také v rozevíracím seznamu vyberte **zvýšit úroveň testu AAD** .

    ![Konfigurace jednotného přihlašování](./media/linkedinelevate-tutorial/tutorial_linkedin_admin_01.png)

1. Kliknutím **nebo kliknutím sem načtěte a zkopírujte jednotlivá pole z formuláře** a proveďte následující kroky:

    ![Konfigurace jednotného přihlašování](./media/linkedinelevate-tutorial/tutorial_linkedin_admin_03.png)

    a. Zkopírujte **ID entity** a vložte ho do textového pole **identifikátor** v **základní konfiguraci SAML** v Azure Portal.

    b. Zkopírujte **adresu URL pro přístup uživatelů kontrolního výrazu (ACS)** a vložte ji do textového pole **Adresa URL odpovědi** v **základní konfiguraci SAML** v Azure Portal.

1. Přejít na část **Nastavení správce LinkedIn** . Kliknutím na možnost nahrát soubor XML nahrajte soubor XML, který jste stáhli z Azure Portal.

    ![Konfigurace jednotného přihlašování](./media/linkedinelevate-tutorial/tutorial_linkedin_metadata_03.png)

1. Kliknutím na povolíte jednotné přihlašování. Stav jednotného přihlašování se změní z nepřipojeného k **připojenému**

    ![Konfigurace jednotného přihlašování](./media/linkedinelevate-tutorial/tutorial_linkedin_admin_05.png)

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

1. Na webu Azure Portal, v levém podokně vyberte **Azure Active Directory**vyberte **uživatelé**a pak vyberte **všichni uživatelé**.

    !["Uživatele a skupiny" a "Všechny uživatele" odkazy](common/users.png)

2. Vyberte **nového uživatele** v horní části obrazovky.

    ![Tlačítko pro nového uživatele](common/new-user.png)

3. Ve vlastnosti uživatele proveďte následující kroky.

    ![Dialogové okno uživatele](common/user-properties.png)

    a. Do pole **název** zadejte **BrittaSimon**.
  
    b. Do pole **uživatelské jméno** zadejte `brittasimon@yourcompanydomain.extension`. Například BrittaSimon@contoso.com.

    c. Zaškrtněte políčko **Zobrazit heslo** a pak zapište hodnotu, která se zobrazí v poli heslo.

    d. Klikněte na možnost **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části povolíte Britta Simon pro použití jednotného přihlašování pomocí Azure tím, že udělíte přístup ke zvýšení oprávnění LinkedInu.

1. V Azure Portal vyberte možnost **podnikové aplikace**, vyberte možnost **všechny aplikace**a pak vyberte možnost **zvýšit úroveň LinkedInu**.

    ![Okno podnikových aplikací](common/enterprise-applications.png)

2. V seznamu aplikace vyberte možnost **Prozvýšit úroveň LinkedInu**.

    ![Odkaz na zvýšení oprávnění LinkedInu v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **Uživatelé a skupiny**.

    ![Odkaz "Uživatele a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V **uživatelů a skupin** dialogové okno Vybrat **Britta Simon** v seznamu uživatelů, klikněte **vyberte** tlačítko v dolní části obrazovky.

6. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, pak v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.

7. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

### <a name="create-linkedin-elevate-test-user"></a>Vytvořit testovacího uživatele ve zvýšení úrovně LinkedIn

Aplikace pro zvýšení úrovně LinkedIn podporuje při zřizování uživatelů jenom v čase a po ověření, že se uživatelé budou automaticky vytvářet v aplikaci. Na stránce nastavení pro správu na portálu pro přístup LinkedInu na webu překlopte přepínač **automaticky přiřazovat licence** k aktivnímu způsobu zřizování a k tomu se taky přiřadí licence uživateli. Zvýšení oprávnění LinkedInu podporuje taky Automatické zřizování uživatelů. Další podrobnosti najdete [tady](linkedinelevate-provisioning-tutorial.md) , jak nakonfigurovat automatické zřizování uživatelů.

   ![Vytváří se testovací uživatele služby Azure AD](./media/linkedinelevate-tutorial/LinkedinUserprovswitch.png)

### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Když kliknete na dlaždici zvýšení úrovně LinkedInu na přístupovém panelu, měli byste se automaticky přihlásit ke zvýšení oprávnění LinkedInu, pro které jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další prostředky

- [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)