---
title: 'Kurz: Integrace služby Azure Active Directory se systémem monitorování bezdrátové teploty SensoScientific | Dokumenty společnosti Microsoft'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Službou Azure Active Directory a bezdrátovým systémem monitorování teploty SensoScientific.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: ee9a924d-ccde-45b0-ab40-877f82f5dfa2
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/10/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: ea2f5e33859852388357526052c39fa432471efb
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "67091275"
---
# <a name="tutorial-azure-active-directory-integration-with-sensoscientific-wireless-temperature-monitoring-system"></a>Kurz: Integrace Azure Active Directory se systémem monitorování bezdrátové teploty SensoScientific

V tomto kurzu se dozvíte, jak integrovat SensoScientific bezdrátový systém monitorování teploty s Azure Active Directory (Azure AD).
Integrace systému monitorování bezdrátové teploty SensoScientific s Azure AD vám poskytuje následující výhody:

* Můžete řídit ve službě Azure AD, který má přístup k SensoScientific bezdrátový systém monitorování teploty.
* Uživatelům můžete povolit automatické přihlášení k bezdrátovému systému monitorování teploty SensoScientific (jednotné přihlášení) pomocí svých účtů Azure AD.
* Své účty můžete spravovat v jednom centrálním umístění – na portálu Azure.

Pokud se chcete dozvědět více podrobností o integraci aplikací SaaS s Azure AD, přečtěte [si, co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud nemáte předplatné Azure, [vytvořte si bezplatný účet,](https://azure.microsoft.com/free/) než začnete.

## <a name="prerequisites"></a>Požadavky

Chcete-li nakonfigurovat integraci Azure AD se systémem monitorování bezdrátové teploty SensoScientific, potřebujete následující položky:

* Předplatné Azure AD. Pokud nemáte prostředí Azure AD, můžete získat [bezplatný účet](https://azure.microsoft.com/free/)
* SensoScientific Wireless Temperature Monitoring System jednotné přihlášení povoleno předplatné

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* SensoScientific Wireless Temperature Monitoring System podporuje **IDP** iniciované SSO

## <a name="adding-sensoscientific-wireless-temperature-monitoring-system-from-the-gallery"></a>Přidání bezdrátového systému sledování teploty SensoScientific z galerie

Chcete-li nakonfigurovat integraci systému monitorování bezdrátové teploty SensoScientific do služby Azure AD, je třeba přidat systém monitorování bezdrátové teploty SensoScientific z galerie do seznamu spravovaných aplikací SaaS.

**Chcete-li přidat systém sledování bezdrátové teploty SensoScientific z galerie, proveďte následující kroky:**

1. Na **[portálu Azure](https://portal.azure.com)** klikněte na levém navigačním panelu na ikonu **Služby Azure Active Directory.**

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte do **podnikových aplikací** a pak vyberte možnost **Všechny aplikace.**

    ![Okno Aplikace Enterprise](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klepněte na tlačítko **Nová aplikace** v horní části dialogového okna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **SensoScientific Wireless Temperature Monitoring System**, z panelu výsledků vyberte **SensoScientific Wireless Temperature Monitoring System** a pak klikněte na tlačítko **Přidat** a přidejte aplikaci.

    ![SensoScientific Bezdrátový systém sledování teploty v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD pomocí systému monitorování bezdrátové teploty SensoScientific na základě testovacího uživatele s názvem **Britta Simon**.
Aby jednotné přihlašování fungovalo, je třeba vytvořit vztah propojení mezi uživatelem Azure AD a souvisejícím uživatelem v systému monitorování bezdrátové teploty SensoScientific.

Chcete-li nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí systému monitorování bezdrátové teploty SensoScientific, musíte dokončit následující stavební bloky:

1. **[Nakonfigurujte azure ad jednotné přihlašování](#configure-azure-ad-single-sign-on)** – aby vaši uživatelé mohli používat tuto funkci.
2. **[Nakonfigurujte sensoScientific Wireless Temperature Monitoring System Single Sign-On](#configure-sensoscientific-wireless-temperature-monitoring-system-single-sign-on)** - pro konfiguraci nastavení jednotného přihlášení na straně aplikace.
3. **[Vytvořte uživatele testu Azure AD](#create-an-azure-ad-test-user)** – k testování jednotného přihlášení Azure AD s Brittou Simonovou.
4. **[Přiřaďte testovacímu uživateli Azure AD](#assign-the-azure-ad-test-user)** – chcete-li Britta Simon ové povolit použití jednotného přihlášení azure ad.
5. **[Vytvořte sensoScientific bezdrátové sledování teploty systému testovacího uživatele](#create-sensoscientific-wireless-temperature-monitoring-system-test-user)** – mít protějšek Britta Simon v SensoScientific bezdrátové sledování teploty systému, který je propojen s Azure AD reprezentace uživatele.
6. **[Otestujte jednotné přihlašování](#test-single-sign-on)** - chcete-li ověřit, zda konfigurace funguje.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace jednotného přihlašování Azure AD

V této části povolíte jednotné přihlašování Azure AD na webu Azure Portal.

Chcete-li nakonfigurovat jednotné přihlašování Azure AD pomocí systému monitorování bezdrátové teploty SensoScientific, proveďte následující kroky:

1. Na [portálu Azure](https://portal.azure.com/)na stránce integrace aplikace **SensoScientific Wireless Temperature Monitoring System** vyberte Jednotné **přihlašování**.

    ![Konfigurace odkazu pro jednotné přihlášení](common/select-sso.png)

2. V **dialogovém okně Vybrat metodu jednotného přihlašování** vyberte režim **SAML/WS-Fed,** abyste povolili jednotné přihlašování.

    ![Režim výběru jednotného přihlášení](common/select-saml-option.png)

3. Na stránce **Nastavit jednotné přihlašování pomocí saml** kliknutím na ikonu **Upravit** otevřete dialogové okno Základní **konfigurace SAML.**

    ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. V části **Základní konfigurace SAML** uživatel nemusí provádět žádný krok, protože aplikace je již předem integrovaná s Azure.

    ![SensoScientific Wireless Temperature Monitoring System Domain and URLs single sign-on information SensoScientific Wireless Temperature Monitoring System Domain and URLs single sign-on information SensoScientific Wireless Temperature Monitoring System Domain and URLs single sign-on information SensoScientific Wireless Temperature](common/preintegrated.png)

5. Na stránce **Nastavit jednotné přihlašování pomocí saml** klikněte v části **Podpisový certifikát SAML** na **Stáhnout** a stáhněte si **certifikát (Base64)** z daných možností podle vašeho požadavku a uložte jej do počítače.

    ![Odkaz ke stažení certifikátu](common/certificatebase64.png)

6. V části **Nastavit sensoScientific Wireless Temperature Monitoring System** zkopírujte příslušnou adresu URL podle vašeho požadavku.

    ![Kopírování konfiguračních adres URL](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor azure reklamy

    c. Adresa URL odhlášení

### <a name="configure-sensoscientific-wireless-temperature-monitoring-system-single-sign-on"></a>Konfigurace systému automatického monitorování teploty SensoScientific single sign-on

1. Přihlaste se k aplikaci SensoScientific Wireless Temperature Monitoring System jako správce.

1. V navigační nabídce v horní části klikněte na **Konfigurace** a přejděte na **Konfigurovat** v části **Jednotné přihlášení,** otevřete nastavení jednotného přihlášení a proveďte následující kroky:

    ![Konfigurace jednotného přihlašování](./media/sensoscientific-tutorial/tutorial_sensoscientificwtms_admin.png)

    a. Vyberte **Název vystavitisty** jako Azure AD.

    b. V textovém poli **Adresa URL vystavitistu** vložte **identifikátor Azure AD,** který jste zkopírovali z webu Azure Portal.

    c. Do textového pole **Adresa URL služby jednotného přihlašování** vložte **přihlašovací adresu URL,** kterou jste zkopírovali z webu Azure Portal.

    d. V textovém poli **Adresa URL služby jednotného odhlášení** vložte **adresu URL odhlášení,** kterou jste zkopírovali z webu Azure Portal.

    e. Projděte si certifikát, který jste stáhli z webu Azure Portal, a nahrajte ho tady.

    f. Klikněte na **Uložit**.

### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD

Cílem této části je vytvořit testovacího uživatele na webu Azure portal s názvem Britta Simon.

1. Na webu Azure Portal v levém podokně vyberte **Azure Active Directory**, vyberte **Uživatelé**a pak vyberte **Všichni uživatelé**.

    ![Odkazy "Uživatelé a skupiny" a "Všichni uživatelé"](common/users.png)

2. V horní části obrazovky vyberte **Nový uživatel.**

    ![Tlačítko nového uživatele](common/new-user.png)

3. Ve vlastnostech User proveďte následující kroky.

    ![Dialogové okno Uživatel](common/user-properties.png)

    a. Do pole **Název** zadejte **BrittaSimon**.
  
    b. V poli **Uživatelské** `brittasimon@yourcompanydomain.extension`jméno typ pole . Například BrittaSimon@contoso.com.

    c. Zaškrtněte **políčko Zobrazit heslo** a poznamenejte si hodnotu, která se zobrazí v poli Heslo.

    d. Klikněte na **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte Britta Simon používat Azure jednotné přihlášení udělením přístupu k SensoScientific bezdrátový systém sledování teploty.

1. Na portálu Azure vyberte **Podnikové aplikace**, vyberte **Všechny aplikace**a pak vyberte **SensoScientific Wireless Temperature Monitoring System**.

    ![Okno podnikových aplikací](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **SensoScientific Wireless Temperature Monitoring System**.

    ![Odkaz SensoScientific Wireless Temperature Monitoring System v seznamu Aplikace](common/all-applications.png)

3. V nabídce vlevo vyberte **Možnost Uživatelé a skupiny**.

    ![Odkaz "Uživatelé a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **Přidat uživatele** a v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny.**

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V dialogovém okně **Uživatelé a skupiny** vyberte **brittu Simonovou** v seznamu Uživatelé a klikněte na tlačítko **Vybrat** v dolní části obrazovky.

6. Pokud očekáváte libovolnou hodnotu role v kontrolním výrazu SAML, vyberte v dialogovém okně **Vybrat roli** příslušnou roli pro uživatele ze seznamu a klepněte na tlačítko **Vybrat** v dolní části obrazovky.

7. V dialogovém okně **Přidat přiřazení** klepněte na tlačítko **Přiřadit.**

### <a name="create-sensoscientific-wireless-temperature-monitoring-system-test-user"></a>Vytvořit SensoScientific Bezdrátový systém sledování teploty test uživatele

Aby se uživatelé Azure AD mohli přihlásit k systému monitorování bezdrátové teploty SensoScientific, musí být zřízeni do systému monitorování bezdrátové teploty SensoScientific. Spolupracujte s  [týmem podpory systému monitorování bezdrátových teplot SensoScientific](https://www.sensoscientific.com/contact-us/)a přidejte uživatele do platformy SensoScientific Wireless Temperature Monitoring System. Uživatelé musí být vytvořena a aktivována před použitím jednotného přihlášení.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části otestujete konfiguraci jednotného přihlášení Azure AD pomocí přístupového panelu.

Po kliknutí na dlaždici SensoScientific bezdrátové monitorování teploty na přístupovém panelu, měli byste být automaticky přihlášeni k SensoScientific bezdrátový systém sledování teploty, pro které nastavíte přispojené k sazbě. Další informace o přístupovém panelu naleznete [v tématu Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další zdroje

- [Seznam výukových programů o integraci aplikací SaaS s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup ve službě Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

