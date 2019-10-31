---
title: 'Kurz: Azure Active Directory integrace s Coralogix | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Coralogix.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: ba79bfc1-992e-4924-b76a-8eb0dfb97724
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 1/2/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8589c366c029ab51c7cd740a1b63cff7c0481a51
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2019
ms.locfileid: "73158465"
---
# <a name="tutorial-azure-active-directory-integration-with-coralogix"></a>Kurz: Azure Active Directory integrace s Coralogix

V tomto kurzu se dozvíte, jak integrovat Coralogix s Azure Active Directory (Azure AD).
Integrace Coralogix s Azure AD poskytuje následující výhody:

* Můžete kontrolovat v Azure AD, kteří mají přístup k Coralogix.
* Uživatelům můžete povolit, aby se automaticky přihlásili k Coralogix (jednotné přihlašování) pomocí svých účtů Azure AD.
* Účty můžete spravovat v jednom centrálním umístění: Azure Portal.

Další informace o integraci aplikací SaaS s Azure AD najdete v tématu [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Předpoklady

Ke konfiguraci integrace služby Azure AD s Coralogix potřebujete následující položky:

- Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční [zkušební verzi](https://azure.microsoft.com/pricing/free-trial/).
- Předplatné s povoleným jedním přihlašováním (Coralogix). 

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Coralogix podporuje jednotné přihlašování iniciované v SP.

## <a name="add-coralogix-from-the-gallery"></a>Přidání Coralogix z Galerie

Pokud chcete nakonfigurovat integraci Coralogix do služby Azure AD, nejdřív přidejte Coralogix z Galerie do svého seznamu spravovaných aplikací SaaS.

Pokud chcete přidat Coralogix z Galerie, proveďte následující kroky:

1. V [Azure Portal](https://portal.azure.com)v levém podokně vyberte ikonu **Azure Active Directory** .

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Vyberte možnost **podnikové aplikace**a pak vyberte **všechny aplikace**.

    ![Okno podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, vyberte tlačítko **Nová aplikace** v horní části dialogového okna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **Coralogix**. V podokně výsledků vyberte **Coralogix** a potom přidejte aplikaci kliknutím na tlačítko **Přidat** .

     ![Coralogix v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD pomocí Coralogix na základě testovacího uživatele s názvem Britta Simon.
Aby jednotné přihlašování fungovalo, musíte vytvořit propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v Coralogix.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí Coralogix, nejdřív dokončete následující stavební bloky:

1. [Nakonfigurujte jednotné přihlašování Azure AD](#configure-azure-ad-single-sign-on) , aby mohli vaši uživatelé používat tuto funkci.
2. [Nakonfigurujte jednotné přihlašování Coralogix](#configure-coralogix-single-sign-on) ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
3. [Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user) pro testování jednotného přihlašování Azure AD pomocí Britta Simon.
4. [Přiřaďte testovacímu uživateli Azure AD](#assign-the-azure-ad-test-user) , aby mohl Britta Simon používat jednotné přihlašování Azure AD.
5. [Vytvořte uživatele Coralogix test](#create-a-coralogix-test-user) , který bude mít protějšek Britta Simon v Coralogix, který je propojený s reprezentací uživatele Azure AD.
6. [Otestujte jednotné přihlašování](#test-single-sign-on) a ověřte, jestli konfigurace funguje.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace jednotného přihlašování Azure AD

V této části povolíte jednotné přihlašování Azure AD v Azure Portal.

Pokud chcete nakonfigurovat jednotné přihlašování Azure AD pomocí Coralogix, proveďte následující kroky:

1. V [Azure Portal](https://portal.azure.com/)na stránce integrace aplikací **Coralogix** vyberte **jednotné přihlašování**.

    ![Konfigurovat odkaz jednotného přihlašování](common/select-sso.png)

2. V dialogovém okně **Vybrat metodu jednotného přihlašování** vyberte možnost **SAML** pro povolení jednotného přihlašování.

    ![Režim výběru jednotného přihlašování](common/select-saml-option.png)

3. Na stránce **nastavit jednotné přihlašování pomocí SAML** vyberte ikonu **Upravit** a otevřete tak základní dialogové okno **Konfigurace SAML** .

    ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. V dialogovém okně **základní konfigurace SAML** proveďte následující kroky:

    ![Informace o jednotném přihlašování v doméně Coralogix a adresách URL](common/sp-identifier.png)

    a. Do pole **přihlašovací adresa URL** zadejte adresu URL s následujícím vzorem: `https://<SUBDOMAIN>.coralogix.com`

    b. Do textového pole **identifikátor (ID entity)** zadejte adresu URL, například:
    
    `https://api.coralogix.com/saml/metadata.xml`

    nebo

    `https://aws-client-prod.coralogix.com/saml/metadata.xml` 

    > [!NOTE]
    > Hodnota přihlašovací adresy URL není skutečná. Aktualizujte hodnotu skutečnou přihlašovací adresou URL. Pokud chcete získat hodnotu, obraťte se na [tým podpory klienta Coralogix](mailto:info@coralogix.com) . Můžete se také podívat na vzory v části **základní konfigurace SAML** v Azure Portal.

5. Aplikace Coralogix očekává kontrolní výrazy SAML v určitém formátu. Pro tuto aplikaci nakonfigurujte následující deklarace identity. Hodnoty těchto atributů můžete spravovat v části **atributy uživatele** na stránce integrace aplikací. Na stránce **nastavit jednotné přihlašování pomocí SAML** vyberte tlačítko **Upravit** a otevřete tak dialogové okno **atributy uživatele** .

    ![image](common/edit-attribute.png)

6. V části **deklarace identity uživatelů** v dialogovém okně **atributy uživatele** upravte deklarace identity pomocí ikony **Upravit** . Deklarace identity můžete také přidat pomocí **Přidat novou deklaraci identity** a nakonfigurovat atribut tokenu SAML, jak je znázorněno na předchozím obrázku. Pak proveďte následující kroky:
    
    a. Výběrem **ikony upravit** otevřete dialogové okno **Spravovat deklarace identity uživatele** .

    Obrázek ![](./media/coralogix-tutorial/tutorial_usermail.png) ![obrázku](./media/coralogix-tutorial/tutorial_usermailedit.png)

    b. V seznamu **zvolit formát identifikátoru názvu** vyberte možnost **e-mailová adresa**.

    c. V seznamu **zdrojový atribut** vyberte **User. mail**.

    d. Vyberte **Save** (Uložit).

7. Na stránce **nastavit jednotné přihlašování pomocí SAML** v části **podpisový certifikát SAML** vyberte **Stáhnout** a Stáhněte si **XML federačních metadat** z daných možností podle vašich požadavků. Pak ho uložte na svém počítači.

    ![Odkaz na stažení certifikátu](common/metadataxml.png)

8. V části **Nastavení Coralogix** zkopírujte příslušné adresy URL.

    ![Kopírovat adresy URL konfigurace](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor Azure AD

    c. Odhlašovací adresa URL

### <a name="configure-coralogix-single-sign-on"></a>Konfigurace jednotného přihlašování Coralogix

Ke konfiguraci jednotného přihlašování na straně **Coralogix** odešlete stažené **federační metadata XML** a zkopírované adresy URL z Azure Portal do [týmu podpory Coralogix](mailto:info@coralogix.com). Zajišťují, že připojení SAML SSO je na obou stranách správně nastavené.

### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD 

Cílem této části je vytvořit testovacího uživatele v Azure Portal s názvem Britta Simon.

1. V Azure Portal v levém podokně vyberte možnost **Azure Active Directory**, vyberte možnost **Uživatelé**a potom vyberte možnost **Všichni uživatelé**.

    ![Odkazy "uživatelé a skupiny" a "Všichni uživatelé"](common/users.png)

2. V horní části obrazovky vyberte **Nový uživatel**.

    ![Tlačítko pro nového uživatele](common/new-user.png)

3. V dialogovém okně **uživatel** proveďte následující kroky.

    ![Uživatelský dialog](common/user-properties.png)

    a. Do pole **název** zadejte **BrittaSimon**.
  
    b. Do pole **uživatelské jméno** zadejte "brittasimon@yourcompanydomain.extension." V takovém případě můžete třeba zadat "brittasimon@contoso.com."

    c. Zaškrtněte políčko **Zobrazit heslo** a potom si poznamenejte hodnotu zobrazenou v poli **heslo** .

    d. Vyberte **Create** (Vytvořit).

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte Britta Simon pro použití jednotného přihlašování pomocí Azure tím, že udělíte přístup k Coralogix.

1. V Azure Portal vyberte možnost **podnikové aplikace**, vyberte možnost **všechny aplikace**a pak vyberte možnost **Coralogix**.

    ![Okno podnikových aplikací](common/enterprise-applications.png)

2. V seznamu aplikace vyberte **Coralogix**.

    ![Odkaz Coralogix v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **Uživatelé a skupiny**.

    ![Odkaz uživatelé a skupiny](common/users-groups-blade.png)

4. Klikněte na tlačítko **Přidat uživatele** . Pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Podokno přidat přiřazení](common/add-assign-user.png)

5. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **Britta Simon** . Pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.

6. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele. Pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.

7. V dialogovém okně **Přidat přiřazení** vyberte tlačítko **přiřadit** .

### <a name="create-a-coralogix-test-user"></a>Vytvořit testovacího uživatele v Coralogix

V této části vytvoříte uživatele s názvem Britta Simon v Coralogix. Pracujte s [týmem podpory Coralogix](mailto:info@coralogix.com) a přidejte uživatele na platformě Coralogix. Než začnete používat jednotné přihlašování, musíte uživatele vytvořit a aktivovat.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování 

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí portálu MyApp.

Když vyberete dlaždici Coralogix na portálu Mojeapl, měli byste se automaticky přihlásit k Coralogix. Další informace o portálu MyApp najdete v tématu [co je to portál MyApp?](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další zdroje informací:

- [Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

