---
title: 'Kurz: Azure Active Directory integrace s Cisco zastřešující | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Cisco zastřešující.
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
ms.openlocfilehash: dde618b28e004e87edc2783bc44c5e7dd9f0ebba
ms.sourcegitcommit: d79513b2589a62c52bddd9c7bd0b4d6498805dbe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/18/2020
ms.locfileid: "97670617"
---
# <a name="tutorial-azure-active-directory-integration-with-cisco-umbrella"></a>Kurz: Azure Active Directory integrace s Cisco deštníkem

V tomto kurzu se dozvíte, jak integrovat Cisco deštník s Azure Active Directory (Azure AD).
Integrace Cisco zastřešující s Azure AD poskytuje následující výhody:

* Můžete kontrolovat v Azure AD, kteří mají přístup k Cisco zastřešující.
* Uživatelům můžete povolit, aby se automaticky přihlásili k Cisco zastřešující (jednotné přihlašování) pomocí svých účtů Azure AD.
* Účty můžete spravovat v jednom centrálním umístění – Azure Portal.

Pokud chcete získat další podrobnosti o integraci aplikace SaaS s Azure AD, přečtěte si téma [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](../manage-apps/what-is-single-sign-on.md).
Pokud předplatné Azure ještě nemáte, napřed si [vytvořte bezplatný účet](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Požadavky

Ke konfiguraci integrace služby Azure AD s Cisco zastřešující budete potřebovat následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční zkušební verzi [tady](https://azure.microsoft.com/pricing/free-trial/) .
* Předplatné Cisco deštník jednotného přihlašování s povolenými možnostmi

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Cisco deštník podporuje **aktualizace SP a IDP, které** iniciovaly jednotné přihlašování.

## <a name="adding-cisco-umbrella-from-the-gallery"></a>Přidání Cisco zastřešující z Galerie

Pokud chcete nakonfigurovat integraci Cisco deštníku do služby Azure AD, musíte do seznamu spravovaných aplikací SaaS přidat Cisco zastřešující z galerie.

**Pokud chcete přidat Cisco zastřešující z Galerie, proveďte následující kroky:**

1. V **[Azure Portal](https://portal.azure.com)** na levém navigačním panelu klikněte na ikonu **Azure Active Directory** .

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte na **podnikové aplikace** a vyberte možnost **všechny aplikace** .

    ![Okno podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **Nová aplikace** v horní části dialogového okna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **Cisco deštník**, vyberte **Cisco deštník** z panelu výsledků a potom kliknutím na **Přidat** přidejte aplikaci.

    ![Cisco deštník v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD pomocí [název aplikace] na základě testovacího uživatele s názvem **Britta Simon**.
Aby jednotné přihlašování fungovalo, musí být navázán vztah odkazu mezi uživatelem služby Azure AD a souvisejícím uživatelem v [název aplikace].

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí [název aplikace], musíte dokončit tyto stavební bloky:

1. **[Nakonfigurujte jednotné přihlašování Azure AD](#configure-azure-ad-single-sign-on)** a Umožněte uživatelům používat tuto funkci.
2. **[Nakonfigurujte jednotné přihlašování Cisco deštník](#configure-cisco-umbrella-single-sign-on)** – ke konfiguraci nastavení jediného Sign-On na straně aplikace.
3. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí Britta Simon.
4. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – pro povolení Britta Simon pro použití jednotného přihlašování Azure AD.
5. **[Vytvořte uživatele Cisco deštník Test User](#create-cisco-umbrella-test-user)** – abyste měli protějšek Britta Simon v Cisco zastřešující, který je propojený s reprezentací uživatele v Azure AD.
6. **[Otestujte jednotné přihlašování](#test-single-sign-on)** – ověřte, jestli konfigurace funguje.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace jednotného přihlašování Azure AD

V této části povolíte jednotné přihlašování Azure AD v Azure Portal.

Pokud chcete nakonfigurovat jednotné přihlašování Azure AD pomocí [název aplikace], proveďte následující kroky:

1. V [Azure Portal](https://portal.azure.com/)na stránce integrace aplikace **Cisco zastřešující** vyberte **jednotné přihlašování**.

    ![Konfigurovat odkaz jednotného přihlašování](common/select-sso.png)

2. V dialogovém okně **Vyberte metodu jednotného přihlašování** vyberte možnost režim **SAML/WS** , čímž povolíte jednotné přihlašování.

    ![Režim výběru jednotného přihlašování](common/select-saml-option.png)

3. Na stránce **nastavit jeden Sign-On s SAML** klikněte na **Upravit** ikona a otevře se základní dialogové okno **Konfigurace SAML** .

    ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. V **základní části Konfigurace SAML** nemusí uživatel provádět žádný krok, protože aplikace už je předem integrovaná s Azure.

    ![Informace o jednotném přihlašování Cisco zastřešující domény a adresy URL](common/both-preintegrated-signon.png)

    a. Pokud chcete nakonfigurovat aplikaci v režimu **SP** intiated, proveďte následující kroky:

    b. Klikněte na **nastavit další adresy URL**.

    c. Do textového pole **přihlašovací adresa URL** zadejte adresu URL: `https://login.umbrella.com/sso`

5. Na stránce **nastavit jednu Sign-On s SAML** v části **podpisový certifikát SAML** klikněte na **Stáhnout** a stáhněte **XML metadat** z daných možností podle vašeho požadavku a uložte ho do svého počítače.

    ![Odkaz na stažení certifikátu](common/metadataxml.png)

6. V části **Nastavení Cisco zastřešující** zkopírujte příslušné adresy URL podle vašich požadavků.

    ![Kopírovat adresy URL konfigurace](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor Azure AD

    c. Odhlašovací adresa URL

### <a name="configure-cisco-umbrella-single-sign-on"></a>Konfigurace Cisco zastřešující Single Sign-On

1. V jiném okně prohlížeče se přihlaste k webu Cisco zastřešující společnosti jako správce.

2. V levé části nabídky klikněte na **správce** , přejděte na **ověřování** a potom klikněte na **SAML**.

    ![Správce](./media/cisco-umbrella-tutorial/tutorial_cisco-umbrella_admin.png)

3. Vyberte **jiný** a klikněte na **Další**.

    ![Druhá](./media/cisco-umbrella-tutorial/tutorial_cisco-umbrella_other.png)

4. Na stránce **metadata Cisco zastřešující** klikněte na **Další**.

    ![Metadata](./media/cisco-umbrella-tutorial/tutorial_cisco-umbrella_metadata.png)

5. V případě, že jste předem nakonfigurovali SAML, vyberte na kartě **metadata pro nahrávání** možnost **kliknutím sem je můžete změnit** a postupovat podle následujících kroků.

    ![Další](./media/cisco-umbrella-tutorial/tutorial_cisco-umbrella_next.png)

6. V **Možnosti a: nahrání souboru XML** nahrajte soubor **XML s federačními metadaty** , který jste stáhli z Azure Portal a po nahrání metadat, že se automaticky naplní následující hodnoty, a pak klikněte na **Další**.

    ![Choosefile](./media/cisco-umbrella-tutorial/tutorial_cisco-umbrella_choosefile.png)

7. V části **ověřit konfiguraci SAML** klikněte na **otestovat konfiguraci SAML**.

    ![Test](./media/cisco-umbrella-tutorial/tutorial_cisco-umbrella_test.png)

8. Klikněte na **Uložit**.

### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD

Cílem této části je vytvořit testovacího uživatele v Azure Portal s názvem Britta Simon.

1. V Azure Portal v levém podokně vyberte možnost **Azure Active Directory**, vyberte možnost **Uživatelé** a potom vyberte možnost **Všichni uživatelé**.

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

V této části povolíte Britta Simon pro použití jednotného přihlašování pomocí Azure tím, že udělíte přístup k Cisco zastřešující.

1. V Azure Portal vyberte možnost **podnikové aplikace**, vyberte možnost **všechny aplikace** a pak vyberte **Cisco deštník**.

    ![Okno Podnikové aplikace](common/enterprise-applications.png)

2. V seznamu aplikace zadejte a vyberte **Cisco deštník**.

    ![Odkaz Cisco zastřešující v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **Uživatelé a skupiny**.

    ![Odkaz uživatelé a skupiny](common/users-groups-blade.png)

4. Klikněte na tlačítko **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Podokno přidat přiřazení](common/add-assign-user.png)

5. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **Britta Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.

6. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, pak v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.

7. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

### <a name="create-cisco-umbrella-test-user"></a>Vytvořit uživatele Cisco zastřešující test

Aby se uživatelé Azure AD mohli přihlásit k Cisco deštníku, musí se zřídit v Cisco zastřešující.  
V případě Cisco zastřešující je zřizování ručním úkolem.

**Chcete-li zřídit uživatelský účet, proveďte následující kroky:**

1. V jiném okně prohlížeče se přihlaste k webu Cisco zastřešující společnosti jako správce.

2. V levé části nabídky klikněte na **správce** a přejděte na **účty**.

    ![Účet](./media/cisco-umbrella-tutorial/tutorial_cisco-umbrella_account.png)

3. Na stránce **účty** klikněte na **Přidat** v pravém horním rohu stránky a proveďte následující kroky.

    ![Uživatel](./media/cisco-umbrella-tutorial/tutorial_cisco-umbrella_createuser.png)

    a. Do pole **jméno** zadejte jméno, jako je **Britta**.

    b. Do pole **příjmení** zadejte příjmení, jako je **Simon**.

    c. Z **role zvolte delegovaného správce** vyberte svou roli.

    d. Do pole **e-mailová adresa** zadejte EmailAddress uživatele, jako je **brittasimon \@ contoso.com**.

    e. Do pole **heslo** zadejte své heslo.

    f. V poli **Potvrdit heslo** zadejte znovu heslo.

    například Klikněte na **vytvořit**.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí přístupového panelu.

Když kliknete na dlaždici Cisco deštník na přístupovém panelu, měli byste se automaticky přihlásit k Cisco zastřešující, pro který jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Další materiály

- [Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory](./tutorial-list.md)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

- [Co je podmíněný přístup v Azure Active Directory?](../conditional-access/overview.md)