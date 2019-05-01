---
title: 'Kurz: Integrace Azure Active Directory systému SensoScientific bezdrátové teploty monitorování | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a SensoScientific bezdrátové teploty monitorování systému.
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
ms.openlocfilehash: 6dc228f3473a4ddca8b5b68cdd99f1fced1a04cd
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/30/2019
ms.locfileid: "64922228"
---
# <a name="tutorial-azure-active-directory-integration-with-sensoscientific-wireless-temperature-monitoring-system"></a>Kurz: Integrace Azure Active Directory s SensoScientific bezdrátové teploty monitorování systému

V tomto kurzu se dozvíte, jak integrovat SensoScientific bezdrátové teploty monitorování systému s Azure Active Directory (Azure AD).
Integrace SensoScientific bezdrátové teploty monitorování systému s Azure AD poskytuje následující výhody:

* Můžete řídit ve službě Azure AD, který má přístup k SensoScientific bezdrátové teploty monitorování systému.
* Uživatelům se automaticky přihlášeni k SensoScientific bezdrátové teploty monitorování systému (Single Sign-On) můžete povolit pomocí jejich účtů služby Azure AD.
* Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s SensoScientific bezdrátové teploty monitorování systému, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat [bezplatný účet](https://azure.microsoft.com/free/)
* Systém monitorování SensoScientific bezdrátové teploty jednotného přihlašování povolená předplatného

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu konfigurace a testování v testovacím prostředí Azure AD jednotného přihlašování.

* Podporuje systém monitorování SensoScientific bezdrátové teploty **IDP** jednotné přihlašování zahájené pomocí

## <a name="adding-sensoscientific-wireless-temperature-monitoring-system-from-the-gallery"></a>Přidání SensoScientific bezdrátové teploty monitorování systému z Galerie

Ke konfiguraci integrace SensoScientific bezdrátové teploty monitorování systému do služby Azure AD, budete muset přidat SensoScientific bezdrátové teploty monitorování systému z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat SensoScientific bezdrátové teploty monitorování systému z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte do **podnikové aplikace** a pak vyberte **všechny aplikace** možnost.

    ![V okně podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **SensoScientific bezdrátové teploty monitorování systému**vyberte **SensoScientific bezdrátové teploty monitorování systému** z panelu výsledků klikněte **přidat**  tlačítko pro přidání aplikace.

    ![SensoScientific bezdrátové teploty sledování systému v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části, konfigurace a testování Azure AD jednotné přihlašování s SensoScientific bezdrátové teploty monitorování systém založený na uživateli testu **Britta Simon**.
Pro jednotné přihlašování pro práci je potřeba navázat vztah odkazu mezi uživatele služby Azure AD a související uživatelské SensoScientific bezdrátové teploty monitorování systému.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s SensoScientific bezdrátové teploty monitorování systému, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Konfigurace SensoScientific bezdrátové teploty monitorování systému jednotného přihlašování](#configure-sensoscientific-wireless-temperature-monitoring-system-single-sign-on)**  – ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
3. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
4. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
5. **[Vytvořit testovacího uživatele SensoScientific bezdrátové teploty monitorování systému](#create-sensoscientific-wireless-temperature-monitoring-system-test-user)**  – Pokud chcete mít protějšek Britta Simon SensoScientific bezdrátové teploty monitorování systému, který je propojený s Azure AD reprezentace uživatele.
6. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure portal.

Ke konfiguraci Azure AD jednotné přihlašování s SensoScientific bezdrátové teploty monitorování systému, proveďte následující kroky:

1. V [webu Azure portal](https://portal.azure.com/)na **SensoScientific bezdrátové teploty monitorování systému** integrace stránce aplikace vyberte **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz](common/select-sso.png)

2. Na **vybrat jedinou metodu přihlašování** dialogového okna, vyberte **SAML/WS-Fed** chcete povolit jednotné přihlašování.

    ![Jednotné přihlašování režim výběru](common/select-saml-option.png)

3. Na **nastavte si jednotné přihlašování pomocí SAML** klikněte na **upravit** ikony otevřete **základní konfiguraci SAML** dialogového okna.

    ![Upravit konfiguraci základní SAML](common/edit-urls.png)

4. Na **základní konfiguraci SAML** oddílu, uživatel nebude muset provést libovolný krok, protože aplikace je už předem integrováno s Azure.

    ![SensoScientific bezdrátové teploty monitorování systému domény a adresy URL jednotného přihlašování – informace](common/preintegrated.png)

5. Na **nastavte si jednotné přihlašování pomocí SAML** stránku, **podpisový certifikát SAML** klikněte na tlačítko **Stáhnout** ke stažení **certifikát (Base64)** z se zadanými možnostmi podle vašich požadavků a uložit je ve vašem počítači.

    ![Odkaz ke stažení certifikátu](common/certificatebase64.png)

6. Na **nastavení SensoScientific bezdrátové teploty monitorování systému** tématu, zkopírujte příslušné adresy URL podle vašich požadavků.

    ![Zkopírování adresy URL konfigurace](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor Azure AD

    c. Adresa URL – odhlášení

### <a name="configure-sensoscientific-wireless-temperature-monitoring-system-single-sign-on"></a>Konfigurace bezdrátové SensoScientific teploty monitorování systému jednotného přihlašování

1. Přihlaste se k aplikaci SensoScientific bezdrátové teploty monitorování systému jako správce.

1. V navigační nabídce v horní části klikněte na tlačítko **konfigurace** a goto **konfigurovat** pod **Single Sign On** Otevřít nastavení jednotného přihlašování a proveďte následující kroky:

    ![Konfigurace jednotného přihlašování](./media/sensoscientific-tutorial/tutorial_sensoscientificwtms_admin.png)

    a. Vyberte **název vystavitele** jako Azure AD.

    b. V **URL vystavitele** vložit do textového pole **Azure AD identifikátor** zkopírovanou z webu Azure portal.

    c. V **jednotné přihlašování – adresa URL služby** vložit do textového pole **přihlašovací adresa URL** zkopírovanou z webu Azure portal.

    d. V **adresu URL jednotného odhlašování služby** vložit do textového pole **odhlašovací adresa URL** zkopírovanou z webu Azure portal.

    e. Vyhledejte certifikát, který jste stáhli z webu Azure portal a nahrajte ji sem.

    f. Klikněte na **Uložit**.

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

1. Na webu Azure Portal, v levém podokně vyberte **Azure Active Directory**vyberte **uživatelé**a pak vyberte **všichni uživatelé**.

    !["Uživatele a skupiny" a "Všechny uživatele" odkazy](common/users.png)

2. Vyberte **nového uživatele** v horní části obrazovky.

    ![Tlačítko Nový uživatel](common/new-user.png)

3. Ve vlastnosti uživatele proveďte následující kroky.

    ![Dialogové okno uživatele](common/user-properties.png)

    a. V **název** zadat **BrittaSimon**.
  
    b. V **uživatelské jméno** typ pole `brittasimon@yourcompanydomain.extension`. Například BrittaSimon@contoso.com.

    c. Vyberte **zobrazit heslo** zaškrtněte políčko a zapište si hodnotu, která se zobrazí v poli heslo.

    d. Klikněte na možnost **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části je povolit Britta Simon používat jednotné přihlašování Azure díky udělení přístupu k SensoScientific bezdrátové teploty monitorování systému.

1. Na webu Azure Portal, vyberte **podnikové aplikace**vyberte **všechny aplikace**a pak vyberte **SensoScientific bezdrátové teploty monitorování systému**.

    ![Okno aplikace organizace](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **SensoScientific bezdrátové teploty monitorování systému**.

    ![Odkaz SensoScientific bezdrátové teploty sledování systému v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **přidat uživatele** tlačítko a pak vyberte **uživatelů a skupin** v **přidat přiřazení** dialogového okna.

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V **uživatelů a skupin** dialogové okno Vybrat **Britta Simon** v seznamu uživatelů, klikněte **vyberte** tlačítko v dolní části obrazovky.

6. Pokud očekáváte libovolnou hodnotu role v kontrolní výraz SAML a potom v **vybrat roli** dialogové okno vybrat vhodnou roli pro uživatele ze seznamu, klikněte **vyberte** tlačítko v dolní části obrazovky.

7. V **přidat přiřazení** dialogové okno kliknutím **přiřadit** tlačítko.

### <a name="create-sensoscientific-wireless-temperature-monitoring-system-test-user"></a>Vytvořit testovacího uživatele SensoScientific bezdrátové teploty monitorování systému

Pokud chcete povolit Azure AD uživatelům umožní přihlásit k SensoScientific bezdrátové teploty monitorování systému, musí být poskytnuty do SensoScientific bezdrátové teploty monitorování systému. Práce s [tým podpory SensoScientific bezdrátové teploty monitorování systému](https://www.sensoscientific.com/contact-us/) přidat uživatele na platformě SensoScientific bezdrátové teploty monitorování systému. Uživatelé musí vytvořit a aktivovat, než použití jednotného přihlašování.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Když kliknete na dlaždici SensoScientific bezdrátové teploty monitorování systému, na přístupovém panelu, můžete by měl být automaticky přihlášeni SensoScientific bezdrátové teploty sledování systému SQL, pro kterou nastavíte jednotné přihlašování. Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další prostředky

- [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

