---
title: 'Kurz: Integrace Azure Active Directory se sadou nástrojů GoodPractice | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a GoodPractice Toolkit.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 65b2979d-9e2f-4530-bc08-546975269ebc
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/19/2018
ms.author: jeedes
ms.openlocfilehash: 699a46dbb404e39c1f2005140b88a773832eb50c
ms.sourcegitcommit: 295babdcfe86b7a3074fd5b65350c8c11a49f2f1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/27/2018
ms.locfileid: "53797209"
---
# <a name="tutorial-azure-active-directory-integration-with-goodpractice-toolkit"></a>Kurz: Integrace se sadou nástrojů GoodPractice Azure Active Directory

V tomto kurzu se dozvíte, jak integrovat GoodPractice Toolkit s Azure Active Directory (Azure AD).
Integrace nástrojů GoodPractice s Azure AD poskytuje následující výhody:

* Můžete řídit ve službě Azure AD, který má přístup k GoodPractice Toolkit.
* Uživatelům se automaticky přihlášeni k GoodPractice Toolkit (Single Sign-On) můžete povolit pomocí jejich účtů služby Azure AD.
* Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD se sadou nástrojů GoodPractice, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční zkušební verze [zde](https://azure.microsoft.com/pricing/free-trial/)
* Sada nástrojů GoodPractice jednotného přihlašování povolená předplatného

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu konfigurace a testování v testovacím prostředí Azure AD jednotného přihlašování.

* Podporuje GoodPractice Toolkit **SP** jednotné přihlašování zahájené pomocí
* Podporuje GoodPractice Toolkit **JIT** zřizování uživatelů

## <a name="adding-goodpractice-toolkit-from-the-gallery"></a>Přidání GoodPractice Toolkit z Galerie

Ke konfiguraci integration GoodPractice Toolkit do služby Azure AD, budete muset přidat GoodPractice Toolkit z Galerie na váš seznam spravovaných aplikací SaaS.

**Sada nástrojů GoodPractice přidat z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte do **podnikové aplikace** a pak vyberte **všechny aplikace** možnost.

    ![V okně podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **GoodPractice Toolkit**vyberte **GoodPractice Toolkit** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

     ![Sada nástrojů GoodPractice v seznamu výsledků](common/search-new-app.png)
## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části nakonfigurujete a testovací služby Azure AD jednotného přihlašování se sadou nástrojů GoodPractice podle testovacího uživatele volá **Britta Simon**.
Pro jednotné přihlašování pro práci je potřeba navázat vztah odkazu mezi uživatele služby Azure AD a souvisejících uživatelů v sadě nástrojů GoodPractice.

Nakonfigurovat a otestovat Azure AD jednotného přihlašování se sadou nástrojů GoodPractice, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Konfigurace GoodPractice Toolkit Single Sign-On](#configure-goodpractice-toolkit-single-sign-on)**  – ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
3. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
4. **[Vytvořit testovacího uživatele GoodPractice Toolkit](#create-goodpractice-toolkit-test-user)**  – Pokud chcete mít protějšek Britta Simon v sadě nástrojů GoodPractice, který je propojený s Azure AD reprezentace uživatele.
5. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
6. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure portal.

Ke konfiguraci Azure AD jednotného přihlašování se sadou nástrojů GoodPractice, proveďte následující kroky:

1. V [webu Azure portal](https://portal.azure.com/)na **GoodPractice Toolkit** integrace stránce aplikace vyberte **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz](common/select-sso.png)

2. Na **vybrat jedinou metodu přihlašování** dialogového okna, vyberte **SAML/WS-Fed** chcete povolit jednotné přihlašování.

    ![Jednotné přihlašování režim výběru](common/select-saml-option.png)

3. Na **nastavte si jednotné přihlašování pomocí SAML** klikněte na **upravit** ikony otevřete **základní konfiguraci SAML** dialogového okna.

    ![Upravit konfiguraci základní SAML](common/edit-urls.png)

4. Na **základní konfiguraci SAML** části, proveďte následující kroky:

    ![GoodPractice sada domény a adresy URL jednotného přihlašování – informace](common/sp-intiated.png)

    V **přihlašovací adresa URL** textové pole, zadejte adresu URL, pomocí následujícího vzorce: `https://app.goodpractice.net/#/<subscriptionUrl>/s/<locationId> `.

    > [!Note]
    > Hodnota přihlašovací adresa URL není skutečný. Aktualizujte příslušnou hodnotu skutečné přihlašovací adresa URL. Kontakt [tým podpory GoodPractice Toolkit klienta](mailto:support@goodpractice.com) má být získána hodnota.

5. Na **nastavte si jednotné přihlašování pomocí SAML** stránku, **podpisový certifikát SAML** klikněte na tlačítko **Stáhnout** ke stažení **kód XML metadat federace**  z se zadanými možnostmi podle vašich požadavků a uložit je ve vašem počítači.

    ![Odkaz ke stažení certifikátu](common/metadataxml.png)

6. Na **nastavení GoodPractice Toolkit** tématu, zkopírujte příslušné adresy URL podle vašich požadavků.

    ![Zkopírování adresy URL konfigurace](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor služby Azure Ad

    c. Adresa URL – odhlášení

### <a name="configure-goodpractice-toolkit-single-sign-on"></a>Konfigurace nástrojů GoodPractice jednotného přihlašování

Ke konfiguraci jednotného přihlašování na **GoodPractice Toolkit** straně, je nutné odeslat na stažený **kód XML metadat federace** a vhodné zkopírovaný adresy URL z webu Azure portal [GoodPractice Tým podpory Toolkit](mailto:support@goodpractice.com). Nastavují tohoto nastavení můžete mít správně nastavené na obou stranách připojení SAML SSO.

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD 

Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

1. Na webu Azure Portal, v levém podokně vyberte **Azure Active Directory**vyberte **uživatelé**a pak vyberte **všichni uživatelé**.

    !["Uživatele a skupiny" a "Všechny uživatele" odkazy](common/users.png)

2. Vyberte **nového uživatele** v horní části obrazovky.

    ![Tlačítko Nový uživatel](common/new-user.png)

3. Ve vlastnosti uživatele proveďte následující kroky.

    ![Dialogové okno uživatele](common/user-properties.png)

    a. V **název** zadat **BrittaSimon**.
  
    b. V **uživatelské jméno** typ pole **brittasimon@yourcompanydomain.extension**  
    Například BrittaSimon@contoso.com.

    c. Vyberte **zobrazit heslo** zaškrtněte políčko a zapište si hodnotu, která se zobrazí v poli heslo.

    d. Klikněte na možnost **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části je povolit Britta Simon používat jednotné přihlašování Azure díky udělení přístupu k GoodPractice Toolkit.

1. Na webu Azure Portal, vyberte **podnikové aplikace**vyberte **všechny aplikace**a pak vyberte **GoodPractice Toolkit**.

    ![Okno aplikace organizace](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **GoodPractice Toolkit**.

    ![Odkaz GoodPractice Toolkit v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **přidat uživatele** tlačítko a pak vyberte **uživatelů a skupin** v **přidat přiřazení** dialogového okna.

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V **uživatelů a skupin** dialogové okno Vybrat **Britta Simon** v seznamu uživatelů, klikněte **vyberte** tlačítko v dolní části obrazovky.

6. Pokud očekáváte libovolnou hodnotu role v kontrolní výraz SAML a potom v **vybrat roli** dialogové okno vybrat vhodnou roli pro uživatele ze seznamu, klikněte **vyberte** tlačítko v dolní části obrazovky.

7. V **přidat přiřazení** dialogové okno kliknutím **přiřadit** tlačítko.

### <a name="create-goodpractice-toolkit-test-user"></a>Vytvořit testovacího uživatele GoodPractice Toolkit

V této části je uživateli Britta Simon vytvořené v sadě nástrojů GoodPractice. Podporuje GoodPractice Toolkit **just-in-time zřizování**, který je ve výchozím nastavení povolené. Neexistuje žádná položka akce pro vás v této části. Pokud uživatel ještě neexistuje v sadě nástrojů GoodPractice, je vytvořen nový při pokusu o přístup k GoodPractice Toolkit.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování 

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na dlaždici GoodPractice Toolkit na přístupovém panelu, můžete by měl být automaticky přihlášeni k GoodPractice Toolkit, u kterého nastavíte jednotné přihlašování. Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další prostředky

- [ Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

