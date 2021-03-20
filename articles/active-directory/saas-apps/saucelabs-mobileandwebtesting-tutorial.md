---
title: 'Kurz: Azure Active Directory integrace s Omáčk Labs – mobilní a webové testování | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Omáčk Labs – mobilní a webové testování.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/22/2019
ms.author: jeedes
ms.openlocfilehash: 087e2d7f2db8f27378b54675095e97256d6aae9b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "92895097"
---
# <a name="tutorial-azure-active-directory-integration-with-sauce-labs---mobile-and-web-testing"></a>Kurz: Azure Active Directory integrace s Omáčk Labs – mobilní a webové testování

V tomto kurzu se naučíte integrovat program Omáčk Labs – mobilní a webové testování pomocí Azure Active Directory (Azure AD).
Integrace Omáčk Labs – mobilní a webové testování pomocí Azure AD poskytuje následující výhody:

* Můžete kontrolovat v Azure AD, kteří mají přístup k Omáčk Labs – mobilní a webové testování.
* Uživatelům můžete povolit, aby se automaticky přihlásili k programu Omáčk Labs – mobilním a webovým testováním (jednotné přihlašování) se svými účty Azure AD.
* Účty můžete spravovat v jednom centrálním umístění – Azure Portal.

Pokud chcete získat další podrobnosti o integraci aplikace SaaS s Azure AD, přečtěte si téma [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](../manage-apps/what-is-single-sign-on.md).
Pokud předplatné Azure ještě nemáte, napřed si [vytvořte bezplatný účet](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Předpoklady

Ke konfiguraci integrace služby Azure AD pomocí programu Omáčk Labs – mobilní a webové testování potřebujete následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat [bezplatný účet](https://azure.microsoft.com/free/) .
* Omáčka Labs – odběr mobilních a webových testů pro povolení jednotného přihlašování

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Omáčka Labs – mobilní a webové testování podporují jednotné přihlašování (SSO) iniciované **IDP**
* Omáčka Labs – mobilní a webové testování podporují zřizování uživatelů **jenom včas**

## <a name="adding-sauce-labs---mobile-and-web-testing-from-the-gallery"></a>Přidání programu Omáčk Labs – mobilní a webové testování z Galerie

Chcete-li nakonfigurovat integraci Omáčk Labs – mobilní a webové testování do služby Azure AD, je nutné přidat z Galerie Omáčk Labs – mobilní a webové testování do seznamu spravovaných aplikací SaaS.

**Chcete-li přidat Omáčk Labs – mobilní a webové testování z Galerie, proveďte následující kroky:**

1. V **[Azure Portal](https://portal.azure.com)** na levém navigačním panelu klikněte na ikonu **Azure Active Directory** .

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte na **podnikové aplikace** a vyberte možnost **všechny aplikace** .

    ![Okno podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **Nová aplikace** v horní části dialogového okna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte příkaz **Omáčk Labs – mobilní a webové testování**, vyberte možnost **omáčkové laboratoře – mobilní a webové testování** z panelu výsledků a potom kliknutím na tlačítko **Přidat** přidejte aplikaci.

    ![Omáčka Labs – mobilní a webové testování v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD pomocí programu Omáčk Labs – mobilní a webové testování na základě testovacího uživatele s názvem **Britta Simon**.
Aby bylo jednotné přihlašování fungovat, je třeba vytvořit odkaz na propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v prostředí omáčky Labs – mobilní a webové testování.

Chcete-li konfigurovat a testovat jednotné přihlašování Azure AD pomocí programu Omáčk Labs – mobilní a webové testování, je nutné dokončit následující stavební bloky:

1. **[Nakonfigurujte jednotné přihlašování Azure AD](#configure-azure-ad-single-sign-on)** a Umožněte uživatelům používat tuto funkci.
2. **[Konfigurace programu Omáčk Labs – mobilní a webové testování – jednotné přihlašování](#configure-sauce-labs---mobile-and-web-testing-single-sign-on)** – pro konfiguraci nastavení jediného Sign-On na straně aplikace
3. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí Britta Simon.
4. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – pro povolení Britta Simon pro použití jednotného přihlašování Azure AD.
5. **[Vytvořit testovací laboratoř – mobilní a webový test testování uživatele](#create-sauce-labs---mobile-and-web-testing-test-user)** – Pokud chcete mít protějšek Britta Simon v programu omáčk Labs – mobilní a webové testování, které je propojené s reprezentací uživatele v Azure AD.
6. **[Otestujte jednotné přihlašování](#test-single-sign-on)** – ověřte, jestli konfigurace funguje.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace jednotného přihlašování Azure AD

V této části povolíte jednotné přihlašování Azure AD v Azure Portal.

Pokud chcete nakonfigurovat jednotné přihlašování Azure AD pomocí programu Omáčk Labs – mobilní a webové testování, proveďte následující kroky:

1. V [Azure Portal](https://portal.azure.com/)na stránce **Omáčk Labs – mobilní a webové testování** aplikací vyberte **jednotné přihlašování**.

    ![Konfigurovat odkaz jednotného přihlašování](common/select-sso.png)

2. V dialogovém okně **Vyberte metodu jednotného přihlašování** vyberte možnost režim **SAML/WS** , čímž povolíte jednotné přihlašování.

    ![Režim výběru jednotného přihlašování](common/select-saml-option.png)

3. Na stránce **nastavit jeden Sign-On s SAML** klikněte na **Upravit** ikona a otevře se základní dialogové okno **Konfigurace SAML** .

    ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. V **základní části Konfigurace SAML** nemusí uživatel provádět žádný krok, protože aplikace už je předem integrovaná s Azure.

    ![Omáčka Labs – informace o jednotném přihlašování pro mobilní a webové testování v doméně a adresách URL](common/preintegrated.png)

5. Na stránce **nastavit jeden Sign-On se** stránkou SAML v části **podpisový certifikát SAML** klikněte na **Stáhnout** a Stáhněte si **XML federačních metadat** z daných možností podle vašich požadavků a uložte ho do svého počítače.

    ![Odkaz na stažení certifikátu](common/metadataxml.png)

6. V části **nastavit omáčku Labs – mobilní a webové testování** zkopírujte příslušné adresy URL podle vašich požadavků.

    ![Kopírovat adresy URL konfigurace](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor Azure AD

    c. Odhlašovací adresa URL

### <a name="configure-sauce-labs---mobile-and-web-testing-single-sign-on"></a>Konfigurace omáčky Labs – mobilní a webové testování v jednom Sign-On

1. V jiném okně webového prohlížeče se přihlaste k webu Omáčk Labs – mobilní a webové testování společnosti jako správce.

2. Klikněte na **ikonu uživatele** a vyberte kartu **Správa týmu** .

    ![Snímek obrazovky, který zobrazuje ikonu "uživatel" a "Správa týmu" vybrané rozevírací nabídky.](./media/saucelabs-mobileandwebtesting-tutorial/configure1.png)

3. Do textového pole zadejte **název domény** .

    ![Snímek obrazovky, který zobrazuje příklad názvu domény v textovém poli](./media/saucelabs-mobileandwebtesting-tutorial/configure2.png)

4. Klikněte na tlačítko **Konfigurovat** kartu.

    ![Snímek obrazovky zobrazující kartu konfigurovat vybraná v části jednotné přihlašování je povolené.](./media/saucelabs-mobileandwebtesting-tutorial/configure3.png)

5. V části **Konfigurace jednotného přihlašování** proveďte následující kroky.

    ![Konfigurace jednoho Sign-On](./media/saucelabs-mobileandwebtesting-tutorial/configure4.png)

    a. Klikněte na **Procházet** a nahrajte stažený soubor metadat z Azure AD.

    b. Zaškrtněte políčko pro **Povolení ZAJIŠŤOVAT za běhu** .

    c. Klikněte na **Uložit**.

### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD 

Cílem této části je vytvořit testovacího uživatele v Azure Portal s názvem Britta Simon.

1. V Azure Portal v levém podokně vyberte možnost **Azure Active Directory**, vyberte možnost **Uživatelé** a potom vyberte možnost **Všichni uživatelé**.

    ![Odkazy "uživatelé a skupiny" a "Všichni uživatelé"](common/users.png)

2. V horní části obrazovky vyberte **Nový uživatel** .

    ![Tlačítko pro nového uživatele](common/new-user.png)

3. Ve vlastnostech uživatele proveďte následující kroky.

    ![Uživatelský dialog](common/user-properties.png)

    a. Do pole **název** zadejte **BrittaSimon**.
  
    b. Do pole typ **uživatelského jména**`brittasimon@yourcompanydomain.extension`  
    Například BrittaSimon@contoso.com.

    c. Zaškrtněte políčko **Zobrazit heslo** a pak zapište hodnotu, která se zobrazí v poli heslo.

    d. Klikněte na **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte Britta Simon pro použití jednotného přihlašování pomocí Azure tím, že udělíte přístup k aplikaci Omáčk Labs – mobilní a webové testování.

1. V Azure Portal vyberte **podnikové aplikace**, vyberte **všechny aplikace** a pak vyberte **Omáčk Labs – mobilní a webové testování**.

    ![Okno Podnikové aplikace](common/enterprise-applications.png)

2. V seznamu aplikace vyberte možnost **omáčka Labs – mobilní a webové testování**.

    ![Odkaz omáčka Labs – mobilní a webové testování v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **Uživatelé a skupiny**.

    ![Odkaz uživatelé a skupiny](common/users-groups-blade.png)

4. Klikněte na tlačítko **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Podokno přidat přiřazení](common/add-assign-user.png)

5. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **Britta Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.

6. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, pak v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.

7. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

### <a name="create-sauce-labs---mobile-and-web-testing-test-user"></a>Vytvořit omáčku Labs – mobilní a webové testování testování uživatelů

V této části se v Omáčk Labs – mobilní a webové testování vytvoří uživatel s názvem Britta Simon. Omáčka Labs – mobilní a webové testování podporují zřizování uživatelů za běhu, což je ve výchozím nastavení povolené. V této části není žádná položka akce. Pokud uživatel ještě neexistuje v Omáčk Labs – mobilní a webové testování, vytvoří se nový, který se vytvoří po ověření.

> [!Note]
> Pokud potřebujete ručně vytvořit uživatele, kontaktujte aplikaci [Omáčk Labs – mobilní a webový testování tým](mailto:support@saucelabs.com).

### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí přístupového panelu.

Po kliknutí na dlaždici Omáčk Labs – mobilní a webové testování na přístupovém panelu byste se měli automaticky přihlášeni ke službě Omáčk Labs – mobilní a webové testování, pro které jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Další materiály

- [Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory](./tutorial-list.md)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

- [Co je podmíněný přístup v Azure Active Directory?](../conditional-access/overview.md)