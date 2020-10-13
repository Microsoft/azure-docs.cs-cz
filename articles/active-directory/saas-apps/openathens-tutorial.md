---
title: 'Kurz: Azure Active Directory integraci jednotného přihlašování s OpenAthens | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a OpenAthens.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/24/2019
ms.author: jeedes
ms.openlocfilehash: da8ae35ce85ca9ffb031511e81270afd8529681d
ms.sourcegitcommit: 83610f637914f09d2a87b98ae7a6ae92122a02f1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/13/2020
ms.locfileid: "91994180"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-openathens"></a>Kurz: Azure Active Directory integraci jednotného přihlašování (SSO) s OpenAthens

V tomto kurzu se dozvíte, jak integrovat OpenAthens s Azure Active Directory (Azure AD). Když integrujete OpenAthens s Azure AD, můžete:

* Řízení ve službě Azure AD, která má přístup k OpenAthens.
* Umožněte, aby se vaši uživatelé automaticky přihlásili k OpenAthens svým účtům Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.

Další informace o integraci aplikací SaaS s Azure AD najdete v tématu [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Požadavky

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* OpenAthens odběr s povoleným jednotným přihlašováním (SSO).

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* OpenAthens podporuje jednotné přihlašování **IDP** .
* OpenAthens podporuje zřizování uživatelů **jenom v čase** .

## <a name="adding-openathens-from-the-gallery"></a>Přidání OpenAthens z Galerie

Pokud chcete nakonfigurovat integraci OpenAthens do služby Azure AD, musíte přidat OpenAthens z Galerie do svého seznamu spravovaných aplikací SaaS.

1. Přihlaste se k [Azure Portal](https://portal.azure.com) pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** do vyhledávacího pole zadejte **OpenAthens** .
1. Na panelu výsledků vyberte **OpenAthens** a pak aplikaci přidejte. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.

## <a name="configure-and-test-azure-ad-single-sign-on-for-openathens"></a>Konfigurace a testování jednotného přihlašování Azure AD pro OpenAthens

Nakonfigurujte a otestujte jednotné přihlašování Azure AD pomocí OpenAthens pomocí testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, je potřeba vytvořit propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v OpenAthens.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí OpenAthens, dokončete následující stavební bloky:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
    * **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí B. Simon.
    * **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD.
1. **[Nakonfigurujte OPENATHENS SSO](#configure-openathens-sso)** – pro konfiguraci nastavení jednotného přihlašování na straně aplikace.
    * **[Vytvořte OpenAthens Test User](#create-openathens-test-user)** -to, abyste měli protějšek B. Simon v OpenAthens, která je propojená s reprezentací uživatele v Azure AD.
1. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

## <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V [Azure Portal](https://portal.azure.com/)na stránce integrace aplikací **OpenAthens** Najděte oddíl **Spravovat** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jednotné přihlašování pomocí SAML** klikněte na ikonu Upravit/pero pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

1. V části **základní konfigurace SAML** nahrajte **soubor metadat poskytovatele služby**. Postup najdete dále v tomto kurzu.

    a. Klikněte na **nahrát soubor metadat**.

    ![metadata pro nahrávání openathens](common/upload-metadata.png)

    b. Kliknutím na **logo složky** vyberte soubor metadat a klikněte na **nahrát**.

    ![Openathens procházet metadata pro odesílání](common/browse-upload-metadata.png)

    c. Po úspěšném nahrání souboru metadat se hodnota **identifikátoru** automaticky naplní v **základním** TEXTOVÉM poli konfiguračního oddílu SAML:

    ![Informace o jednotném přihlašování v doméně OpenAthens a adresách URL](common/idp-identifier.png)

1. Na stránce **nastavit jednotné přihlašování pomocí SAML** v části **podpisový certifikát SAML** Najděte **XML metadata federace** a vyberte **Stáhnout** a Stáhněte certifikát a uložte ho do svého počítače.

    ![Odkaz na stažení certifikátu](common/metadataxml.png)

1. V části **Nastavení OpenAthens** zkopírujte na základě vašeho požadavku příslušné adresy URL.

    ![Kopírovat adresy URL konfigurace](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD

V této části vytvoříte testovacího uživatele ve Azure Portal s názvem B. Simon.

1. V levém podokně Azure Portal vyberte možnost **Azure Active Directory**, vyberte možnost **Uživatelé**a potom vyberte možnost **Všichni uživatelé**.
1. V horní části obrazovky vyberte **Nový uživatel** .
1. Ve vlastnostech **uživatele** proveďte následující kroky:
   1. Do pole **Název** zadejte `B.Simon`.  
   1. Do pole **uživatelské jméno** zadejte username@companydomain.extension . Například, `B.Simon@contoso.com`.
   1. Zaškrtněte políčko **Zobrazit heslo** a pak zapište hodnotu, která se zobrazí v poli **heslo** .
   1. Klikněte na **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte B. Simon pro použití jednotného přihlašování Azure tím, že udělíte přístup k OpenAthens.

1. V Azure Portal vyberte **podnikové aplikace**a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte **OpenAthens**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.

   ![Odkaz uživatelé a skupiny](common/users-groups-blade.png)

1. Vyberte **Přidat uživatele**a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Odkaz Přidat uživatele](common/add-assign-user.png)

1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

## <a name="configure-openathens-sso"></a>Konfigurace jednotného přihlašování OpenAthens

1. V jiném okně webového prohlížeče se přihlaste k webu OpenAthens společnosti jako správce.

1. V seznamu vyberte **připojení** na kartě **Správa** .

    ![Snímek obrazovky zobrazující stránku webu společnosti "OpenAthens" se zvolenými možnostmi "připojení" na kartě Správa.](./media/openathens-tutorial/tutorial_openathens_application1.png)

1. Vyberte **SAML 1.1/2.0**a pak klikněte na tlačítko **Konfigurovat** .

    ![Snímek obrazovky zobrazující typ místního ověřování systému dialog s "S A M L 1.1/2.0" a vybraným tlačítkem "konfigurovat".](./media/openathens-tutorial/tutorial_openathens_application2.png)

1. Chcete-li přidat konfiguraci, vyberte tlačítko **Procházet** a odešlete soubor metadata. XML, který jste stáhli z Azure Portal, a pak vyberte **Přidat**.

    ![Snímek obrazovky zobrazující "Přidání S A M L Authentication System" Dialogové okno s vybranou akcí Procházet a tlačítkem Přidat.](./media/openathens-tutorial/tutorial_openathens_application3.png)

1. Na kartě **Podrobnosti** proveďte následující kroky.

    ![Konfigurace jednotného přihlašování](./media/openathens-tutorial/tutorial_openathens_application4.png)

    a. V **mapování zobrazovaný název**vyberte **použít atribut**.

    b. Do textového pole **atribut zobrazovaného názvu** zadejte hodnotu `http://schema.microsoft.com/identity/claims/displayname` .

    c. V **mapování jedinečného uživatele**vyberte **použít atribut**.

    d. Do textového pole **jedinečný atribut uživatele** zadejte hodnotu `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name` .

    e. V části **stav**zaškrtněte políčko všechna tři zaškrtávací políčka.

    f. V nástroji **vytvořit místní účty**vyberte možnost **automaticky**.

    například Vyberte **Uložit změny**.

    h. Na kartě **</> předávající strana** zkopírujte **adresu URL metadat** a otevřete ji v prohlížeči a Stáhněte si soubor **XML s metadaty SP** . Nahrajte tento soubor metadat SP v části **základní konfigurační oddíl SAML** ve službě Azure AD.

    ![Snímek obrazovky, který zobrazuje vybranou kartu předávající strana a zvýrazní se metadata U R L.](./media/openathens-tutorial/tutorial_openathens_application5.png)

### <a name="create-openathens-test-user"></a>Vytvořit testovacího uživatele OpenAthens

V této části se v OpenAthens vytvoří uživatel s názvem Britta Simon. OpenAthens podporuje **zřizování uživatelů za běhu**, což je ve výchozím nastavení povolené. V této části není žádná položka akce. Pokud uživatel ještě v OpenAthens neexistuje, vytvoří se po ověření nový.

## <a name="test-sso"></a>Test SSO

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí přístupového panelu.

Když na přístupovém panelu kliknete na dlaždici OpenAthens, měli byste se automaticky přihlásit k OpenAthens, pro které jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další zdroje informací

- [ Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Vyzkoušejte si OpenAthens s Azure AD](https://aad.portal.azure.com/)