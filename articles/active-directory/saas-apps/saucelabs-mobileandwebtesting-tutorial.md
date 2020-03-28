---
title: 'Kurz: Integrace azure active directory s Sauce Labs – testování mobilních zařízení a webu | Dokumenty společnosti Microsoft'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Sauce Labs – mobilní a webové testování.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 3142d947-70e5-4345-8a30-b92d8715fac9
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/22/2019
ms.author: jeedes
ms.openlocfilehash: 8933cb90672e49305cd0fb7dc5e4f8f04f94093e
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "67091558"
---
# <a name="tutorial-azure-active-directory-integration-with-sauce-labs---mobile-and-web-testing"></a>Kurz: Integrace Azure Active Directory s Sauce Labs – testování mobilních zařízení a webu

V tomto kurzu se dozvíte, jak integrovat Sauce Labs – mobilní a webové testování s Azure Active Directory (Azure AD).
Integrace Sauce Labs – mobilní a webové testování s Azure AD poskytuje následující výhody:

* Můžete řídit ve službě Azure AD, který má přístup k Sauce Labs – mobilní a webové testování.
* Uživatelům můžete povolit automatické přihlášení k testovacím laboratořím Sauce – testování pro mobilní zařízení a web (jednotné přihlašování) pomocí svých účtů Azure AD.
* Své účty můžete spravovat v jednom centrálním umístění – na portálu Azure.

Pokud se chcete dozvědět více podrobností o integraci aplikací SaaS s Azure AD, přečtěte [si, co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud nemáte předplatné Azure, [vytvořte si bezplatný účet,](https://azure.microsoft.com/free/) než začnete.

## <a name="prerequisites"></a>Požadavky

Chcete-li nakonfigurovat integraci Azure AD s Sauce Labs – mobilní a webové testování, potřebujete následující položky:

* Předplatné Azure AD. Pokud nemáte prostředí Azure AD, můžete získat [bezplatný účet](https://azure.microsoft.com/free/)
* Sauce Labs - Mobilní a webové testování jednotného přihlášení povoleno předplatné

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Omáčka Labs - Mobilní a webové testování podporuje **IDP** inicioval SSO
* Sauce Labs - Mobilní a webové testování podporuje **just in time** zřizování uživatelů

## <a name="adding-sauce-labs---mobile-and-web-testing-from-the-gallery"></a>Přidání Omáčka Labs - Mobilní a webové testování z galerie

Chcete-li nakonfigurovat integraci Sauce Labs – mobilní a webové testování do Azure AD, je třeba přidat Sauce Labs – mobilní a webové testování z galerie do seznamu spravovaných aplikací SaaS.

**Chcete-li přidat Sauce Labs - Mobilní a webové testování z galerie, proveďte následující kroky:**

1. Na **[portálu Azure](https://portal.azure.com)** klikněte na levém navigačním panelu na ikonu **Služby Azure Active Directory.**

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte do **podnikových aplikací** a pak vyberte možnost **Všechny aplikace.**

    ![Okno Aplikace Enterprise](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klepněte na tlačítko **Nová aplikace** v horní části dialogového okna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **Sauce Labs - Mobile and Web Testing**, vyberte Sauce **Labs - Mobile and Web Testing** z panelu výsledků a pak klikněte na tlačítko **Přidat** a přidejte aplikaci.

    ![Omáčka Labs - Mobilní a webové testování v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD pomocí testovacích laboratoří – mobilní a webové testování na základě testovacího uživatele s názvem **Britta Simon**.
Aby jednotné přihlašování fungovalo, musí být vytvořen vztah propojení mezi uživatelem Azure AD a souvisejícím uživatelem v testovacím prostředí Sauce – mobilní a webové testování.

Chcete-li nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí testovacích laboratoří sauce - mobilní a webové testování, musíte dokončit následující stavební bloky:

1. **[Nakonfigurujte azure ad jednotné přihlašování](#configure-azure-ad-single-sign-on)** – aby vaši uživatelé mohli používat tuto funkci.
2. **[Konfigurace Sauce Labs - Mobilní a webové testování jednotného přihlášení](#configure-sauce-labs---mobile-and-web-testing-single-sign-on)** - pro konfiguraci nastavení jednotného přihlášení na straně aplikace.
3. **[Vytvořte uživatele testu Azure AD](#create-an-azure-ad-test-user)** – k testování jednotného přihlášení Azure AD s Brittou Simonovou.
4. **[Přiřaďte testovacímu uživateli Azure AD](#assign-the-azure-ad-test-user)** – chcete-li Britta Simon ové povolit použití jednotného přihlášení azure ad.
5. **[Vytvořte Sauce Labs – mobilní a web testování testovací uživatel](#create-sauce-labs---mobile-and-web-testing-test-user)** - mít protějšek Britta Simon v omáčka Labs – mobilní a webové testování, které je propojeno s reprezentaci Azure AD uživatele.
6. **[Otestujte jednotné přihlašování](#test-single-sign-on)** - chcete-li ověřit, zda konfigurace funguje.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace jednotného přihlašování Azure AD

V této části povolíte jednotné přihlašování Azure AD na webu Azure Portal.

Chcete-li nakonfigurovat jednotné přihlašování Azure AD pomocí testovacích laboratoří sauce - mobilní a webové testování, proveďte následující kroky:

1. Na [portálu Azure](https://portal.azure.com/)na stránce integrace aplikací **Sauce Labs – mobilní a webové testování** vyberte Jednotné **přihlašování**.

    ![Konfigurace odkazu pro jednotné přihlášení](common/select-sso.png)

2. V **dialogovém okně Vybrat metodu jednotného přihlašování** vyberte režim **SAML/WS-Fed,** abyste povolili jednotné přihlašování.

    ![Režim výběru jednotného přihlášení](common/select-saml-option.png)

3. Na stránce **Nastavit jednotné přihlašování pomocí saml** kliknutím na ikonu **Upravit** otevřete dialogové okno Základní **konfigurace SAML.**

    ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. V části **Základní konfigurace SAML** uživatel nemusí provádět žádný krok, protože aplikace je již předem integrovaná s Azure.

    ![Sauce Labs - Mobilní a webové testování domény a adresy URL jednotné přihlašovací informace](common/preintegrated.png)

5. Na stránce **Nastavit jednotné přihlašování pomocí saml** klikněte v části **Podpisový certifikát SAML** na **Stáhnout** a stáhněte si xml **metadat federace** z daných možností podle vašeho požadavku a uložte ho do počítače.

    ![Odkaz ke stažení certifikátu](common/metadataxml.png)

6. V části **Nastavit omáčku - Mobilní a webové testování** zkopírujte příslušnou adresu URL podle vašeho požadavku.

    ![Kopírování konfiguračních adres URL](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor azure reklamy

    c. Adresa URL odhlášení

### <a name="configure-sauce-labs---mobile-and-web-testing-single-sign-on"></a>Konfigurace Sauce Labs - Mobilní a webové testování jednotného přihlášení

1. V jiném okně webového prohlížeče se přihlaste k webu Sauce Labs – Mobile and Web Testing company jako správce.

2. Klikněte na **ikonu Uživatel** a vyberte kartu **Správa týmu.**

    ![Konfigurace jednotného přihlašování](./media/saucelabs-mobileandwebtesting-tutorial/configure1.png)

3. Do textového pole zadejte **název domény.**

    ![Konfigurace jednotného přihlašování](./media/saucelabs-mobileandwebtesting-tutorial/configure2.png)

4. Klikněte na **Konfigurovat** kartu.

    ![Konfigurace jednotného přihlašování](./media/saucelabs-mobileandwebtesting-tutorial/configure3.png)

5. V části **Konfigurovat jednotné přihlášce** proveďte následující kroky.

    ![Konfigurace jednotného přihlašování](./media/saucelabs-mobileandwebtesting-tutorial/configure4.png)

    a. Klikněte na **Procházet** a nahrajte stažený soubor metadat z Azure AD.

    b. Zaškrtněte políčko **POVOLIT JUST-IN-TIME PROVISIONING.**

    c. Klikněte na **Uložit**.

### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD 

Cílem této části je vytvořit testovacího uživatele na webu Azure portal s názvem Britta Simon.

1. Na webu Azure Portal v levém podokně vyberte **Azure Active Directory**, vyberte **Uživatelé**a pak vyberte **Všichni uživatelé**.

    ![Odkazy "Uživatelé a skupiny" a "Všichni uživatelé"](common/users.png)

2. V horní části obrazovky vyberte **Nový uživatel.**

    ![Tlačítko nového uživatele](common/new-user.png)

3. Ve vlastnostech User proveďte následující kroky.

    ![Dialogové okno Uživatel](common/user-properties.png)

    a. Do pole **Název** zadejte **BrittaSimon**.
  
    b. V poli **Uživatelské jméno** typ pole`brittasimon@yourcompanydomain.extension`  
    Například BrittaSimon@contoso.com.

    c. Zaškrtněte **políčko Zobrazit heslo** a poznamenejte si hodnotu, která se zobrazí v poli Heslo.

    d. Klikněte na **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte Britta Simon používat Azure jednotné přihlášení udělením přístupu k omáčky Labs – mobilní a webové testování.

1. Na portálu Azure vyberte **Podnikové aplikace**, vyberte **Všechny aplikace**a pak vyberte **Sauce Labs – Mobilní a webové testování**.

    ![Okno podnikových aplikací](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **Sauce Labs - Mobile and Web Testing**.

    ![Sauce Labs - Mobilní a webové testování odkaz v seznamu aplikace](common/all-applications.png)

3. V nabídce vlevo vyberte **Možnost Uživatelé a skupiny**.

    ![Odkaz "Uživatelé a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **Přidat uživatele** a v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny.**

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V dialogovém okně **Uživatelé a skupiny** vyberte **brittu Simonovou** v seznamu Uživatelé a klikněte na tlačítko **Vybrat** v dolní části obrazovky.

6. Pokud očekáváte libovolnou hodnotu role v kontrolním výrazu SAML, vyberte v dialogovém okně **Vybrat roli** příslušnou roli pro uživatele ze seznamu a klepněte na tlačítko **Vybrat** v dolní části obrazovky.

7. V dialogovém okně **Přidat přiřazení** klepněte na tlačítko **Přiřadit.**

### <a name="create-sauce-labs---mobile-and-web-testing-test-user"></a>Vytvořit omáčka Labs - Mobilní a web testování test uživatele

V této části je uživatel s názvem Britta Simon vytvořen v Sauce Labs - Mobile a web Testing. Sauce Labs – mobilní a webové testování podporuje zřizování uživatelů just-in-time, které je ve výchozím nastavení povoleno. V této části pro vás není žádná položka akce. Pokud uživatel ještě neexistuje v Sauce Labs - Mobilní a webové testování, nový je vytvořen po ověření.

> [!Note]
> Pokud potřebujete vytvořit uživatele ručně, obraťte se [na Sauce Labs - Mobilní a web testování týmu podpory](mailto:support@saucelabs.com).

### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části otestujete konfiguraci jednotného přihlášení Azure AD pomocí přístupového panelu.

Po kliknutí na dlaždice Sauce Labs – mobilní a webové testování na přístupovém panelu, měli byste být automaticky přihlášeni k Sauce Labs - Mobile a web testování, pro které nastavíte přiřazovat služby . Další informace o přístupovém panelu naleznete [v tématu Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další zdroje

- [Seznam výukových programů o integraci aplikací SaaS s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup ve službě Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

