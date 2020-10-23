---
title: 'Kurz: Azure Active Directory integrace s prohlížečem instavování | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a prohlížečem instavování.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/17/2019
ms.author: jeedes
ms.openlocfilehash: e60e8c73c9f1da617851cc67fb2dbab7171f1cb0
ms.sourcegitcommit: 9b8425300745ffe8d9b7fbe3c04199550d30e003
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/23/2020
ms.locfileid: "92459998"
---
# <a name="tutorial-azure-active-directory-integration-with-instavr-viewer"></a>Kurz: Azure Active Directory integrace s prohlížečem instavů

V tomto kurzu se dozvíte, jak integrovat prohlížeč instaver s Azure Active Directory (Azure AD).
Integrace instav Vieweru s Azure AD poskytuje následující výhody:

* Můžete řídit v Azure AD, kteří mají přístup k prohlížeči instavování.
* Uživatelům můžete povolit, aby se automaticky přihlásili do prohlížeče instavů (jednotné přihlašování) se svými účty Azure AD.
* Účty můžete spravovat v jednom centrálním umístění – Azure Portal.

Pokud chcete získat další podrobnosti o integraci aplikace SaaS s Azure AD, přečtěte si téma [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](../manage-apps/what-is-single-sign-on.md).
Pokud předplatné Azure ještě nemáte, napřed si [vytvořte bezplatný účet](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Požadavky

Ke konfiguraci integrace služby Azure AD s prohlížečem pro instavování potřebujete následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční zkušební verzi [tady](https://azure.microsoft.com/pricing/free-trial/) .
* Odběr s povoleným jednotným přihlašováním pro prohlížeč instavů

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Prohlížeč instavů podporuje jednotné přihlašování (SSO) iniciované v **SP**
* Prohlížeč instavů podporuje **jenom čas** zřizování uživatelů.

## <a name="adding-instavr-viewer-from-the-gallery"></a>Přidání prohlížeče instavů z Galerie

Pokud chcete nakonfigurovat integraci instav Vieweru do služby Azure AD, musíte do seznamu spravovaných aplikací SaaS přidat prohlížeč instavů z galerie.

**Chcete-li přidat prohlížeč instavů z Galerie, proveďte následující kroky:**

1. V **[Azure Portal](https://portal.azure.com)** na levém navigačním panelu klikněte na ikonu **Azure Active Directory** .

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte na **podnikové aplikace** a vyberte možnost **všechny aplikace** .

    ![Okno podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **Nová aplikace** v horní části dialogového okna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **prohlížeč pro instav**, vyberte **prohlížeč instavů** z panelu výsledků a potom kliknutím na tlačítko **Přidat** přidejte aplikaci.

     ![Prohlížeč instavů v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD pomocí prohlížeče instavů založeného na testovacím uživateli s názvem **Britta Simon**.
Aby jednotné přihlašování fungovalo, musí být navázán vztah odkazu mezi uživatelem služby Azure AD a souvisejícím uživatelem v prohlížeči inactiveer.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí prohlížeče inactiveer, musíte dokončit tyto stavební bloky:

1. **[Nakonfigurujte jednotné přihlašování Azure AD](#configure-azure-ad-single-sign-on)** a Umožněte uživatelům používat tuto funkci.
2. **[Nakonfigurovat jednotné přihlašování prohlížeče pro Instavování](#configure-instavr-viewer-single-sign-on)** – ke konfiguraci nastavení jediného Sign-On na straně aplikace
3. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí Britta Simon.
4. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – pro povolení Britta Simon pro použití jednotného přihlašování Azure AD.
5. **[Vytvořit testovacího uživatele prohlížeče Instavů](#create-instavr-viewer-test-user)** – Pokud má protějšek Britta Simon v prohlížeči instavů, který je propojený s reprezentací uživatele v Azure AD.
6. **[Otestujte jednotné přihlašování](#test-single-sign-on)** – ověřte, jestli konfigurace funguje.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace jednotného přihlašování Azure AD

V této části povolíte jednotné přihlašování Azure AD v Azure Portal.

Pokud chcete nakonfigurovat jednotné přihlašování Azure AD pomocí prohlížeče inactiveer, proveďte následující kroky:

1. V [Azure Portal](https://portal.azure.com/)na stránce integrace aplikací **prohlížeče Instav** vyberte **jednotné přihlašování**.

    ![Konfigurovat odkaz jednotného přihlašování](common/select-sso.png)

2. V dialogovém okně **Vyberte metodu jednotného přihlašování** vyberte možnost režim **SAML/WS** , čímž povolíte jednotné přihlašování.

    ![Režim výběru jednotného přihlašování](common/select-saml-option.png)

3. Na stránce **nastavit jeden Sign-On s SAML** klikněte na **Upravit** ikona a otevře se základní dialogové okno **Konfigurace SAML** .

    ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. V části **základní konfigurace SAML** proveďte následující kroky:

    ![Informace o jednotném přihlašování k doméně a adresám URL prohlížeče pro instavování](common/sp-identifier.png)

    a. Do textového pole **přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru: `https://console.instavr.co/auth/saml/login/<WEBPackagedURL>`

    > [!NOTE]
    > Neexistuje žádný pevný vzor pro přihlašovací adresu URL. Je generována, když zákazník instavování provede webové sbalení. Je jedinečný pro každého zákazníka a balíček. Pro získání přesného přihlašovacího adresy URL musíte se přihlásit ke své instanci prohlížeče pro instavování a provádět webové balíčky.

    b. Do textového pole **identifikátor (ID entity)** zadejte adresu URL pomocí následujícího vzoru: `https://console.instavr.co/auth/saml/sp/<WEBPackagedURL>`

    > [!NOTE]
    > Hodnota identifikátoru není reálné číslo. Aktualizujte tuto hodnotu skutečnou hodnotou identifikátoru, která je vysvětlena dále v tomto kurzu.

5. Na stránce **nastavit jednu Sign-On s SAML** v části **podpisový certifikát SAML** klikněte na **Stáhnout** a Stáhněte si **certifikát (Base64)** a **soubor federačních metadat** z daných možností podle vašich požadavků a uložte ho do svého počítače.

    ![Odkaz na stažení certifikátu](common/metadata-certificatebase64.png)

6. V části **nastavit prohlížeč Instavů** zkopírujte příslušné adresy URL podle vašeho požadavku.

    ![Kopírovat adresy URL konfigurace](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor Azure AD

    c. Odhlašovací adresa URL

### <a name="configure-instavr-viewer-single-sign-on"></a>Nakonfigurovat jeden Sign-On prohlížeče pro instavování

1. Otevřete nové okno webového prohlížeče a přihlaste se do svého firemního prohlížeče inlog Viewer jako správce.

2. Klikněte na **ikona uživatele** a vyberte **účet**.

    ![Snímek obrazovky s vybraným uživatelem zobrazuje web prohlížeče pro instavování.](media/instavr-viewer-tutorial/tutorial-instavr-viewer-account.png)

3. Posuňte se dolů k **ověření SAML** a proveďte následující kroky:

    ![Snímek obrazovky se zobrazí stránka ověřování SAML, kde můžete zadat hodnoty popsané v tomto kroku.](media/instavr-viewer-tutorial/tutorial-instavr-viewer-configure.png)

    a. Do textového pole **URL jednotného přihlašování** vložte hodnotu **URL pro přihlášení** , kterou jste zkopírovali z Azure Portal.

    b. Do textového pole **Adresa URL pro odhlášení** vložte hodnotu **URL pro odhlášení** , kterou jste zkopírovali z Azure Portal.

    c. Do textového pole **ID entity** vložte hodnotu **identifikátoru Azure AD** , kterou jste zkopírovali z Azure Portal.

    d. Pokud chcete nahrát stažený soubor certifikátu, klikněte na **aktualizovat**.

    e. Pokud chcete nahrát stažený soubor federačních metadat, klikněte na **aktualizovat**.

    f. Zkopírujte hodnotu **ID entity** a vložte ji do textového pole **identifikátor (ID entity)** v části **základní konfigurace SAML** v Azure Portal.

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

V této části povolíte Britta Simon pro použití jednotného přihlašování pomocí Azure tím, že udělíte přístup k prohlížeči instavování.

1. V Azure Portal vyberte možnost **podnikové aplikace**, vyberte možnost **všechny aplikace**a pak vyberte možnost **prohlížeč pro instavování**.

    ![Okno Podnikové aplikace](common/enterprise-applications.png)

2. V seznamu aplikace zadejte a vyberte prohlížeč pro **Instavování**.

    ![Odkaz na prohlížeč instavování v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **Uživatelé a skupiny**.

    ![Odkaz uživatelé a skupiny](common/users-groups-blade.png)

4. Klikněte na tlačítko **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Podokno přidat přiřazení](common/add-assign-user.png)

5. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **Britta Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.

6. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, pak v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.

7. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

### <a name="create-instavr-viewer-test-user"></a>Vytvořit testovacího uživatele prohlížeče pro instavování

V této části se v prohlížeči instavů vytvoří uživatel s názvem Britta Simon. Prohlížeč instavů podporuje zřizování uživatelů za běhu, což je ve výchozím nastavení povolené. V této části není žádná položka akce. Pokud uživatel v prohlížeči instavů ještě neexistuje, vytvoří se po ověření nový. Pokud se setkáte s případnými problémy, kontaktujte prosím [tým podpory pro Instavování](mailto:contact@instavr.co).

### <a name="test-single-sign-on"></a>Test jednotného přihlašování

1. Otevřete nové okno webového prohlížeče a přihlaste se do svého firemního prohlížeče inlog Viewer jako správce.

2. V levém navigačním panelu vyberte **balíček** a vyberte **vytvořit balíček pro web**.

    ![Snímek obrazovky zobrazuje web s prohlížečem pro instavování s vybraným balíčkem a vytvořením balíčku pro web.](media/instavr-viewer-tutorial/tutorial-instavr-viewer-testing1.png)

3. Vyberte **Stáhnout**.

    ![Snímek obrazovky zobrazuje vybranou ikonu pro stažení.](media/instavr-viewer-tutorial/tutorial-instavr-viewer-testing2.png)

4. Vyberte **otevřít hostovanou stránku** , po které se přesměruje do služby Azure AD pro přihlášení.

    ![Snímek obrazovky znázorňující vybranou možnost otevřít hostovanou stránku.](media/instavr-viewer-tutorial/tutorial-instavr-viewer-testing3.png)

5. Zadejte svoje přihlašovací údaje Azure AD pro úspěšné přihlášení k Azure AD prostřednictvím jednotného přihlašování.

## <a name="additional-resources"></a>Další zdroje

- [Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory](./tutorial-list.md)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

- [Co je podmíněný přístup v Azure Active Directory?](../conditional-access/overview.md)