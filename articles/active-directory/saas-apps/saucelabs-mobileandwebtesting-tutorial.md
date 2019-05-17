---
title: 'Kurz: Integrace Azure Active Directory s praktickými cvičeními úpravu – mobilní a webové testování | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a úpravu Labs – mobilní a webové testování.
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
ms.openlocfilehash: 41b35324ccca8cf40edbc53ed25a2d8615a9294e
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/16/2019
ms.locfileid: "65813633"
---
# <a name="tutorial-azure-active-directory-integration-with-sauce-labs---mobile-and-web-testing"></a>Kurz: Integrace Azure Active Directory s praktickými cvičeními úpravu - Mobile a testování webů

V tomto kurzu se dozvíte, jak integrovat úpravu Labs – mobilní a webové testování pomocí služby Azure Active Directory (Azure AD).
Integrace úpravu Labs – mobilní a webové testování pomocí služby Azure AD poskytuje následující výhody:

* Můžete řídit ve službě Azure AD, který má přístup k testovacím prostředím úpravu - Mobile a testování webů.
* Můžete povolit uživatelům, aby se automaticky přihlášeni k testovacím prostředím úpravu – mobilní a webové testování (Single Sign-On) pomocí svých účtů služby Azure AD.
* Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s praktickými cvičeními úpravu – mobilní a webové testování, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat [bezplatný účet](https://azure.microsoft.com/free/)
* Co Labs – mobilní zařízení a testování webové jednotné přihlašování povoleno předplatné

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu konfigurace a testování v testovacím prostředí Azure AD jednotného přihlašování.

* Sauce Labs – mobilní a webové testování podporuje **IDP** jednotné přihlašování zahájené pomocí
* Sauce Labs – mobilní a webové testování podporuje **JIT** zřizování uživatelů

## <a name="adding-sauce-labs---mobile-and-web-testing-from-the-gallery"></a>Přidání úpravu Labs – mobilní a webové testy z Galerie

Konfigurace integrace úpravu Labs – mobilní a webové testování do služby Azure AD, budete muset přidat úpravu Labs – mobilní a webové testy z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat úpravu Labs – mobilní a webové testy z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte do **podnikové aplikace** a pak vyberte **všechny aplikace** možnost.

    ![V okně podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **úpravu Labs – mobilní a webové testování**vyberte **úpravu Labs – mobilní a webové testování** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

    ![Co Labs – mobilní zařízení a v seznamu výsledků testování webů](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části, konfiguraci a testování Azure AD jednotné přihlašování s praktickými cvičeními úpravu – mobilní a webové testování založené na test uživateli **Britta Simon**.
Pro jednotné přihlašování k práci, vztah odkazu mezi uživatele služby Azure AD a související uživatelské v co Labs – mobilní a webové testování je potřeba navázat.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s praktickými cvičeními úpravu – mobilní zařízení a testování webů, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Konfigurace úpravu Labs – mobilní a webové testování Single Sign-On](#configure-sauce-labs---mobile-and-web-testing-single-sign-on)**  – ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
3. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
4. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
5. **[Vytvářet testovací prostředí úpravu – mobilní a webové testování testovací uživatele](#create-sauce-labs---mobile-and-web-testing-test-user)**  – Pokud chcete mít protějšek Britta Simon úpravu Labs – mobilní a webové testování, který je propojený s Azure AD reprezentace uživatele.
6. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure portal.

Ke konfiguraci Azure AD jednotné přihlašování s praktickými cvičeními úpravu - Mobile a testování webů, postupujte následovně:

1. V [webu Azure portal](https://portal.azure.com/)na **úpravu Labs – mobilní a webové testování** integrace stránce aplikace vyberte **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz](common/select-sso.png)

2. Na **vybrat jedinou metodu přihlašování** dialogového okna, vyberte **SAML/WS-Fed** chcete povolit jednotné přihlašování.

    ![Jednotné přihlašování režim výběru](common/select-saml-option.png)

3. Na **nastavte si jednotné přihlašování pomocí SAML** klikněte na **upravit** ikony otevřete **základní konfiguraci SAML** dialogového okna.

    ![Upravit konfiguraci základní SAML](common/edit-urls.png)

4. Na **základní konfiguraci SAML** oddílu, uživatel nebude muset provést libovolný krok, protože aplikace je už předem integrováno s Azure.

    ![Co Labs – mobilní a webové testování domény a adresy URL jednotné přihlašování – informace](common/preintegrated.png)

5. Na **nastavte si jednotné přihlašování pomocí SAML** stránku, **podpisový certifikát SAML** klikněte na tlačítko **Stáhnout** ke stažení **kód XML metadat federace**  z se zadanými možnostmi podle vašich požadavků a uložit je ve vašem počítači.

    ![Odkaz ke stažení certifikátu](common/metadataxml.png)

6. Na **nastavení úpravu Labs – mobilní a webové testování** tématu, zkopírujte příslušné adresy URL podle vašich požadavků.

    ![Zkopírování adresy URL konfigurace](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor Azure AD

    c. Adresa URL – odhlášení

### <a name="configure-sauce-labs---mobile-and-web-testing-single-sign-on"></a>Konfigurace úpravu Labs – mobilní a webové testování jednotného přihlašování

1. V okně jiné webové prohlížeče Přihlaste se k co Labs – mobilní a webové testování webu společnosti jako správce.

2. Klikněte na **ikonu uživatele** a vyberte **Správa týmu** kartu.

    ![Konfigurace jednotného přihlašování](./media/saucelabs-mobileandwebtesting-tutorial/configure1.png)

3. Zadejte vaše **název domény** do textového pole.

    ![Konfigurace jednotného přihlašování](./media/saucelabs-mobileandwebtesting-tutorial/configure2.png)

4. Klikněte na tlačítko **konfigurovat** kartu.

    ![Konfigurace jednotného přihlašování](./media/saucelabs-mobileandwebtesting-tutorial/configure3.png)

5. V **konfigurovat Single Sign On** části, proveďte následující kroky.

    ![Konfigurace jednotného přihlašování](./media/saucelabs-mobileandwebtesting-tutorial/configure4.png)

    a. Klikněte na tlačítko **Procházet** a nahrajte soubor stažený metadat ze služby Azure AD.

    b. Vyberte **povolit zřizování za běhu** zaškrtávací políčko.

    c. Klikněte na **Uložit**.

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD 

Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

1. Na webu Azure Portal, v levém podokně vyberte **Azure Active Directory**vyberte **uživatelé**a pak vyberte **všichni uživatelé**.

    !["Uživatele a skupiny" a "Všechny uživatele" odkazy](common/users.png)

2. Vyberte **nového uživatele** v horní části obrazovky.

    ![Tlačítko Nový uživatel](common/new-user.png)

3. Ve vlastnosti uživatele proveďte následující kroky.

    ![Dialogové okno uživatele](common/user-properties.png)

    a. V **název** zadat **BrittaSimon**.
  
    b. V **uživatelské jméno** typ pole `brittasimon@yourcompanydomain.extension`  
    Například BrittaSimon@contoso.com.

    c. Vyberte **zobrazit heslo** zaškrtněte políčko a zapište si hodnotu, která se zobrazí v poli heslo.

    d. Klikněte na možnost **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části je povolit Britta Simon používat jednotné přihlašování Azure tak, že udělíte přístup k testovacím prostředím úpravu – mobilní a webové testování.

1. Na webu Azure Portal, vyberte **podnikové aplikace**vyberte **všechny aplikace**a pak vyberte **úpravu Labs – mobilní a webové testování**.

    ![Okno aplikace organizace](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **úpravu Labs – mobilní a webové testování**.

    ![Co Labs – mobilní a webové testování na odkaz v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **přidat uživatele** tlačítko a pak vyberte **uživatelů a skupin** v **přidat přiřazení** dialogového okna.

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V **uživatelů a skupin** dialogové okno Vybrat **Britta Simon** v seznamu uživatelů, klikněte **vyberte** tlačítko v dolní části obrazovky.

6. Pokud očekáváte libovolnou hodnotu role v kontrolní výraz SAML a potom v **vybrat roli** dialogové okno vybrat vhodnou roli pro uživatele ze seznamu, klikněte **vyberte** tlačítko v dolní části obrazovky.

7. V **přidat přiřazení** dialogové okno kliknutím **přiřadit** tlačítko.

### <a name="create-sauce-labs---mobile-and-web-testing-test-user"></a>Vytvářet testovací prostředí úpravu – mobilní a webové testování testovací uživatele

V této části se vytvoří uživateli Britta Simon v co Labs – mobilní a webové testování. Sauce Labs – mobilní zařízení a testování webů podporuje zřizování uživatelů v čase, který je ve výchozím nastavení povolené. Neexistuje žádná položka akce pro vás v této části. Pokud uživatel již neexistuje mezi úpravu Labs – mobilní a webové testování, vytvoří se nový po ověření.

> [!Note]
> Pokud je potřeba ručně vytvořit uživatele, obraťte se na [úpravu Labs – mobilní a webové testování tým podpory](mailto:support@saucelabs.com).

### <a name="test-single-sign-on"></a>Otestovat jednotné přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Při kliknutí na úpravu Labs – mobilní a webové testování dlaždici na přístupovém panelu, můžete by měl být automaticky přihlášeni na úpravu Labs – mobilní a webové testování, u kterého nastavíte jednotné přihlašování. Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další prostředky

- [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

