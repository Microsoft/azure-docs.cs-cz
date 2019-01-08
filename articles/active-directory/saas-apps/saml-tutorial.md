---
title: 'Kurz: OBCHODNÍ aplikace s povoleným integrace Azure Active Directory pomocí tokenu SAML 1.1 | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a SAML 1.1 Token obchodní aplikace s povolenou.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: ced1d88d-0e48-40d5-9aea-ef991cd9d270
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/24/2018
ms.author: jeedes
ms.openlocfilehash: 32fa6f7c13180179a49c656763e58d1b915fb607
ms.sourcegitcommit: 3ab534773c4decd755c1e433b89a15f7634e088a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/07/2019
ms.locfileid: "54064657"
---
# <a name="tutorial-azure-active-directory-integration-with-saml-11-token-enabled-lob-app"></a>Kurz: OBCHODNÍ aplikace s povoleným integrace Azure Active Directory pomocí tokenu SAML 1.1

V tomto kurzu se dozvíte, jak integrovat SAML 1.1 Token aplikace s povoleným LOB s Azure Active Directory (Azure AD).
Integrace tokenu SAML 1.1 obchodní aplikace s Azure AD poskytuje následující výhody:

* Můžete řídit v Azure AD, který má přístup k tokenu SAML 1.1 obchodní aplikace s povolenou.
* Můžete povolit uživatelům zajistit možnost být automaticky přihlášeni k tokenu SAML 1.1 povolené obchodní aplikace (Single Sign-On) s jejich účty Azure AD.
* Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Ke konfiguraci Azure obchodní aplikace s povoleným integrace AD se SAML 1.1 Token, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční zkušební verze [zde](https://azure.microsoft.com/pricing/free-trial/)
* SAML 1.1 Token povolené obchodní aplikaci pro jedno přihlášení povolený předplatné

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu konfigurace a testování v testovacím prostředí Azure AD jednotného přihlašování.

* SAML 1.1 Token podporuje obchodní aplikaci povoleno **SP** jednotné přihlašování zahájené pomocí

## <a name="adding-saml-11-token-enabled-lob-app-from-the-gallery"></a>Přidání tokenu SAML 1.1 povolené obchodní aplikaci z Galerie

Chcete-li konfigurovat integraci SAML 1.1 Token povolena obchodní aplikaci do služby Azure AD, je třeba přidat SAML 1.1 Token povolené obchodní aplikaci z Galerie váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat SAML 1.1 Token povolené obchodní aplikaci z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte do **podnikové aplikace** a pak vyberte **všechny aplikace** možnost.

    ![V okně podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **tokenu SAML 1.1 obchodní aplikace s povoleným**vyberte **tokenu SAML 1.1 obchodní aplikace s povoleným** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

     ![SAML 1.1 Token povolené obchodní aplikaci v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části nakonfigurujete a testovací služby Azure AD jednotné přihlašování pomocí SAML 1.1 Token povolené obchodní aplikaci na základě testovací uživatele volá **Britta Simon**.
Vztah odkazu mezi uživatele služby Azure AD a souvisejících uživatele v tokenu SAML 1.1 pro jednotné přihlašování pro práci, povolené obchodní aplikaci pro je potřeba navázat.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování pomocí SAML 1.1 Token obchodní aplikace s povolenou, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Konfigurace tokenů SAML 1.1 povolené obchodní aplikace Single Sign-On](#configure-saml-11-token-enabled-lob-app-single-sign-on)**  – ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
3. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
4. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
5. **[Vytvoření SAML 1.1 Token povolené obchodní aplikaci pro testovacího uživatele](#create-saml-11-token-enabled-lob-app-test-user)**  – Pokud chcete mít obchodní aplikaci, který je propojený s Azure AD reprezentace uživatele povoleno protějšek Britta Simon v tokenu SAML 1.1.
6. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure portal.

Ke konfiguraci Azure AD jednotné přihlašování pomocí SAML 1.1 Token povolena obchodní aplikaci, proveďte následující kroky:

1. V [webu Azure portal](https://portal.azure.com/)na **tokenu SAML 1.1 obchodní aplikace s povoleným** integrace stránce aplikace vyberte **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz](common/select-sso.png)

2. Na **vybrat jedinou metodu přihlašování** dialogového okna, vyberte **SAML/WS-Fed** chcete povolit jednotné přihlašování.

    ![Jednotné přihlašování režim výběru](common/select-saml-option.png)

3. Na **nastavte si jednotné přihlašování pomocí SAML** klikněte na **upravit** ikony otevřete **základní konfiguraci SAML** dialogového okna.

    ![Upravit konfiguraci základní SAML](common/edit-urls.png)

4. Na **základní konfiguraci SAML** části, proveďte následující kroky:

    ![SAML 1.1 Token povolené obchodní domény aplikace a adresy URL jednotné přihlašování – informace](common/sp-identifier.png)

    a. V **přihlašovací adresa URL** textové pole, zadejte adresu URL, pomocí následujícího vzorce: `https://your-app-url`

    b. V **identifikátor (Entity ID)** textové pole, zadejte adresu URL, pomocí následujícího vzorce: `https://your-app-url`

    > [!NOTE]
    > Tyto hodnoty nejsou skutečný. Aktualizujte tyto hodnoty skutečné přihlašovací adresu URL a identifikátor. Kontaktní SAML 1.1 Token povolit klienta aplikace LOB tým podpory k získání těchto hodnot. Můžete také odkazovat na tyto vzory se dají ukazuje **základní konfiguraci SAML** části webu Azure Portal.

4. Na **nastavte si jednotné přihlašování pomocí SAML** stránku, **podpisový certifikát SAML** klikněte na tlačítko **Stáhnout** ke stažení **certifikát (Base64)** z se zadanými možnostmi podle vašich požadavků a uložit je ve vašem počítači.

    ![Odkaz ke stažení certifikátu](common/certificatebase64.png)

6. Na **obchodní aplikace s povoleným nastavit SAML 1.1 Token** tématu, zkopírujte příslušné adresy URL podle vašich požadavků.

    ![Zkopírování adresy URL konfigurace](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor služby Azure Ad

    c. Adresa URL – odhlášení

### <a name="configure-saml-11-token-enabled-lob-app-single-sign-on"></a>Konfigurace tokenů SAML 1.1 povolené obchodní aplikace Single Sign-On

Ke konfiguraci jednotného přihlašování na **tokenu SAML 1.1 obchodní aplikace s povoleným** straně, je nutné odeslat na stažený **certifikát (Base64)** a obchodní aplikace s povolenou odpovídající zkopírovaný adresy URL z webu Azure portal do tokenu SAML 1.1 tým podpory. Nastavují tohoto nastavení můžete mít správně nastavené na obou stranách připojení SAML SSO.

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

V této části povolíte Britta Simon do Azure můžete používat jednotné přihlašování tak, že udělíte přístup k tokenu SAML 1.1 obchodní aplikace s povolenou.

1. Na webu Azure Portal, vyberte **podnikové aplikace**vyberte **všechny aplikace**a pak vyberte **tokenu SAML 1.1 obchodní aplikace s povoleným**.

    ![Okno aplikace organizace](common/enterprise-applications.png)

2. V seznamu aplikace zadejte a vyberte **tokenu SAML 1.1 obchodní aplikace s povoleným**.

    ![Tokenu SAML 1.1 obchodní aplikaci pro odkaz v seznamu aplikací povoleno](common/all-applications.png)

3. V nabídce na levé straně vyberte **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **přidat uživatele** tlačítko a pak vyberte **uživatelů a skupin** v **přidat přiřazení** dialogového okna.

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V **uživatelů a skupin** dialogové okno Vybrat **Britta Simon** v seznamu uživatelů, klikněte **vyberte** tlačítko v dolní části obrazovky.

6. Pokud očekáváte libovolnou hodnotu role v kontrolní výraz SAML a potom v **vybrat roli** dialogové okno vybrat vhodnou roli pro uživatele ze seznamu, klikněte **vyberte** tlačítko v dolní části obrazovky.

7. V **přidat přiřazení** dialogové okno kliknutím **přiřadit** tlačítko.

### <a name="create-saml-11-token-enabled-lob-app-test-user"></a>Vytvoření SAML 1.1 Token povolené obchodní aplikaci pro testovacího uživatele

V této části, vytvořte uživatele Britta Simon tokenu SAML 1.1 obchodní aplikace s povolenou. Práce se SAML 1.1 Token povolené obchodní aplikaci pro tým podpory pro přidání uživatele v tokenu SAML 1.1 povolené obchodní aplikaci pro platformu. Uživatelé musí vytvořit a aktivovat, než použití jednotného přihlašování.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování 

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na SAML 1.1 Token povolena obchodní aplikaci pro dlaždice na přístupovém panelu, které by měly být automaticky přihlášeni k obchodní aplikaci, u kterého nastavíte jednotné přihlašování SAML 1.1 Token povolena. Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další prostředky

- [ Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

