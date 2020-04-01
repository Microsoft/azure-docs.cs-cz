---
title: 'Kurz: Integrace služby Azure Active Directory se sadou ForeSee CX Suite | Dokumenty společnosti Microsoft'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Službou Azure Active Directory a sadou ForeSee CX.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 5f4b7830-6186-4d17-b77b-504d4192bfde
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/01/2019
ms.author: jeedes
ms.openlocfilehash: c78fa8ec470f04bcd88ab403249f77a65120c707
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "67550392"
---
# <a name="tutorial-azure-active-directory-integration-with-foresee-cx-suite"></a>Kurz: Integrace Služby Azure Active Directory se sadou ForeSee CX Suite

V tomto kurzu se dozvíte, jak integrovat ForeSee CX Suite s Azure Active Directory (Azure AD).
Integrace sady ForeSee CX S Azure AD vám poskytuje následující výhody:

* Můžete řídit ve službě Azure AD, který má přístup k Sadě ForeSee CX.
* Můžete povolit, aby se uživatelé automaticky přihlašovali ke službě ForeSee CX Suite (jednotné přihlášení) pomocí svých účtů Azure AD.
* Své účty můžete spravovat v jednom centrálním umístění – na portálu Azure.

Pokud se chcete dozvědět více podrobností o integraci aplikací SaaS s Azure AD, přečtěte [si, co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud nemáte předplatné Azure, [vytvořte si bezplatný účet,](https://azure.microsoft.com/free/) než začnete.

## <a name="prerequisites"></a>Požadavky

Chcete-li nakonfigurovat integraci Azure AD s Aplikací ForeSee CX, potřebujete následující položky:

* Předplatné Azure AD. Pokud nemáte prostředí Azure AD, můžete získat [bezplatný účet](https://azure.microsoft.com/free/)
* Předplatné s povoleným jedním přihlášením ForeSee CX Suite

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* ForeSee CX Suite podporuje **SP** inicioval SSO

* ForeSee CX Suite podporuje **just in time** zřizování uživatelů

## <a name="adding-foresee-cx-suite-from-the-gallery"></a>Přidání ForeSee CX Suite z galerie

Chcete-li nakonfigurovat integraci sady ForeSee CX Suite do Azure AD, musíte přidat sadu ForeSee CX Suite z galerie do seznamu spravovaných aplikací SaaS.

**Chcete-li přidat sadu ForeSee CX Suite z galerie, proveďte následující kroky:**

1. Na **[portálu Azure](https://portal.azure.com)** klikněte na levém navigačním panelu na ikonu **Služby Azure Active Directory.**

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte do **podnikových aplikací** a pak vyberte možnost **Všechny aplikace.**

    ![Okno Aplikace Enterprise](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klepněte na tlačítko **Nová aplikace** v horní části dialogového okna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **ForeSee CX Suite**, vyberte **ForeSee CX Suite** z panelu výsledků a pak klikněte na **tlačítko Přidat** a přidejte aplikaci.

     ![ForeSee CX Suite v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD pomocí sady ForeSee CX Suite na základě testovacího uživatele s názvem **Britta Simon**.
Aby jednotné přihlašování fungovalo, musí být vytvořen vztah propojení mezi uživatelem Azure AD a souvisejícím uživatelem v sadě ForeSee CX Suite.

Chcete-li nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí sady ForeSee CX Suite, musíte dokončit následující stavební bloky:

1. **[Nakonfigurujte azure ad jednotné přihlašování](#configure-azure-ad-single-sign-on)** – aby vaši uživatelé mohli používat tuto funkci.
2. **[Konfigurace foreSee CX Suite Single Sign-On](#configure-foresee-cx-suite-single-sign-on)** - pro konfiguraci nastavení jednotného přihlášení na straně aplikace.
3. **[Vytvořte uživatele testu Azure AD](#create-an-azure-ad-test-user)** – k testování jednotného přihlášení Azure AD s Brittou Simonovou.
4. **[Přiřaďte testovacímu uživateli Azure AD](#assign-the-azure-ad-test-user)** – chcete-li Britta Simon ové povolit použití jednotného přihlášení azure ad.
5. **[Vytvořte testovacího uživatele Sady ForeSee CX](#create-foresee-cx-suite-test-user)** – chcete-li mít protějšek Britta Simon v sadě ForeSee CX, která je propojená s reprezentací uživatele Azure AD.
6. **[Otestujte jednotné přihlašování](#test-single-sign-on)** - chcete-li ověřit, zda konfigurace funguje.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace jednotného přihlašování Azure AD

V této části povolíte jednotné přihlašování Azure AD na webu Azure Portal.

Chcete-li nakonfigurovat jednotné přihlašování Azure AD pomocí sady ForeSee CX Suite, proveďte následující kroky:

1. Na [portálu Azure](https://portal.azure.com/)na stránce integrace aplikací **ForeSee CX Suite** vyberte **Jednotné přihlašování**.

    ![Konfigurace odkazu pro jednotné přihlášení](common/select-sso.png)

2. V **dialogovém okně Vybrat metodu jednotného přihlašování** vyberte režim **SAML/WS-Fed,** abyste povolili jednotné přihlašování.

    ![Režim výběru jednotného přihlášení](common/select-saml-option.png)

3. Na stránce **Nastavit jednotné přihlašování pomocí saml** kliknutím na ikonu **Upravit** otevřete dialogové okno Základní **konfigurace SAML.**

    ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. Pokud máte **soubor metadat poskytovatele služeb**, proveďte v části Základní konfigurace **SAML** následující kroky:

    a. Klikněte na **Nahrát soubor metadat**.

    ![Nahrát soubor metadat](common/upload-metadata.png)

    b. Kliknutím na **logo složky** vyberte soubor metadat a klepněte na tlačítko **Nahrát**.

    ![výběr souboru metadat](common/browse-upload-metadata.png)

    c. Po úspěšném nahrání souboru metadat se hodnota **Identifikátor** automaticky naplní v části Základní konfigurace SAML.

    ![ForeSee CX Suite domény a URL jednotné přihlašovací informace](common/sp-identifier.png)

    a. Do textového pole **Přihlašovací adresa URL** zadejte adresu URL:`https://cxsuite.foresee.com/`

    b. Do textového pole **Identifikátor** zadejte adresu URL\/pomocí\<následujícího vzoru: https: /www.okta.com/saml2/service-provider/ UniqueID>

    > [!Note]
    > Pokud se hodnota **Identifikátor** nezpoltuje, vyplňte hodnotu ručně podle výše uvedeného vzoru. Hodnota Identifikátor není skutečná. Aktualizujte tuto hodnotu skutečným identifikátorem. Chcete-li získat tuto hodnotu, obraťte se na [tým podpory klienta Sady ForeSee CX](mailto:support@foresee.com) Suite. Můžete také odkazovat na vzory uvedené v části **Základní konfigurace SAML** na webu Azure Portal.

5. Na stránce **Nastavit jednotné přihlašování pomocí saml** klikněte v části **Podpisový certifikát SAML** na **Stáhnout** a stáhněte si xml **metadat federace** z daných možností podle vašeho požadavku a uložte ho do počítače.

    ![Odkaz ke stažení certifikátu](common/metadataxml.png)

6. V části **Nastavit sadu ForeSee CX** zkopírujte příslušnou adresu URL podle vašeho požadavku.

    ![Kopírování konfiguračních adres URL](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor azure reklamy

    c. Adresa URL odhlášení

### <a name="configure-foresee-cx-suite-single-sign-on"></a>Konfigurace jednotného přihlašování sady ForeSee CX Suite

Chcete-li nakonfigurovat jednotné přihlašování na straně **ForeSee CX Suite,** musíte odeslat stažený **xml metadat federace** a příslušné zkopírované adresy URL z portálu Azure do týmu podpory [ForeSee CX Suite](mailto:support@foresee.com). Toto nastavení nastaví tak, aby bylo připojení s přizasazené k samovazbě SAML správně nastaveno na obou stranách.

### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD 

Cílem této části je vytvořit testovacího uživatele na webu Azure portal s názvem Britta Simon.

1. Na webu Azure Portal v levém podokně vyberte **Azure Active Directory**, vyberte **Uživatelé**a pak vyberte **Všichni uživatelé**.

    ![Odkazy "Uživatelé a skupiny" a "Všichni uživatelé"](common/users.png)

2. V horní části obrazovky vyberte **Nový uživatel.**

    ![Tlačítko nového uživatele](common/new-user.png)

3. Ve vlastnostech User proveďte následující kroky.

    ![Dialogové okno Uživatel](common/user-properties.png)

    a. Do pole **Název** zadejte **BrittaSimon**.
  
    b. V poli **Uživatelské** brittasimon@yourcompanydomain.extensionjméno typ pole . Například BrittaSimon@contoso.com.

    c. Zaškrtněte **políčko Zobrazit heslo** a poznamenejte si hodnotu, která se zobrazí v poli Heslo.

    d. Klikněte na **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte Britta Simon používat Azure jednotné přihlášení udělením přístupu k ForeSee CX Suite.

1. Na portálu Azure vyberte **Podnikové aplikace**, vyberte **Všechny aplikace**a pak vyberte **ForeSee CX Suite**.

    ![Okno podnikových aplikací](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **ForeSee CX Suite**.

    ![Odkaz ForeSee CX Suite v seznamu Aplikací](common/all-applications.png)

3. V nabídce vlevo vyberte **Možnost Uživatelé a skupiny**.

    ![Odkaz "Uživatelé a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **Přidat uživatele** a v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny.**

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V dialogovém okně **Uživatelé a skupiny** vyberte **brittu Simonovou** v seznamu Uživatelé a klikněte na tlačítko **Vybrat** v dolní části obrazovky.

6. Pokud očekáváte libovolnou hodnotu role v kontrolním výrazu SAML, vyberte v dialogovém okně **Vybrat roli** příslušnou roli pro uživatele ze seznamu a klepněte na tlačítko **Vybrat** v dolní části obrazovky.

7. V dialogovém okně **Přidat přiřazení** klepněte na tlačítko **Přiřadit.**

### <a name="create-foresee-cx-suite-test-user"></a>Vytvořit testovacího uživatele sady ForeSee CX

V této části vytvoříte uživatele s názvem Britta Simon v Sadě ForeSee CX. Spolupracujte s [týmem podpory ForeSee CX Suite](mailto:support@foresee.com) a přidejte uživatele nebo doménu, která musí být přidána do seznamu povolených položek pro platformu ForeSee CX Suite. Pokud je doména přidána týmem, uživatelé se automaticky zřídí na platformě ForeSee CX Suite. Uživatelé musí být vytvořena a aktivována před použitím jednotného přihlášení.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování 

V této části otestujete konfiguraci jednotného přihlášení Azure AD pomocí přístupového panelu.

Po kliknutí na dlaždici ForeSee CX Suite na přístupovém panelu, měli byste být automaticky přihlášeni k ForeSee CX Suite, u kterého nastavíte přihlašující. Další informace o přístupovém panelu naleznete [v tématu Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další zdroje

- [Seznam výukových programů o integraci aplikací SaaS s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup ve službě Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

