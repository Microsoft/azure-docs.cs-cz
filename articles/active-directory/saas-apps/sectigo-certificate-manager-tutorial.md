---
title: 'Kurz: Integrace s Azure Active Directory pomocí Správce certifikátů Sectigo | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Sectigo správce certifikátů.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 62cd6987-3373-4b58-b1ff-589f4a3d70a9
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 15-04-2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: e3f6f76806cfe7625416e79d2489a80d2778fda7
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/17/2019
ms.locfileid: "59685136"
---
# <a name="tutorial-azure-active-directory-integration-with-sectigo-certificate-manager"></a>Kurz: Integrace s Azure Active Directory pomocí Správce certifikátů Sectigo

V tomto kurzu se dozvíte, jak integrovat Sectigo správce certifikátů Azure Active Directory (Azure AD).
Správce certifikátů Sectigo integrace s Azure AD poskytuje následující výhody:

* Můžete řídit ve službě Azure AD, který má přístup k Sectigo správce certifikátů.
* Uživatelům se automaticky přihlášeni do Správce certifikátů (Single Sign-On) Sectigo můžete povolit pomocí jejich účtů služby Azure AD.
* Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD pomocí Správce certifikátů Sectigo, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat [bezplatný účet](https://azure.microsoft.com/free/)
* Správce certifikátů Sectigo jednotného přihlašování povolená předplatného

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu konfigurace a testování v testovacím prostředí Azure AD jednotného přihlašování.

* Podporuje správce certifikátů Sectigo **SP a zprostředkovatele identity** jednotné přihlašování zahájené pomocí

## <a name="adding-sectigo-certificate-manager-from-the-gallery"></a>Přidání správce certifikátů Sectigo z Galerie

Konfigurace integrace nástroje Správce certifikátů Sectigo do služby Azure AD, musíte přidat správce certifikátů Sectigo z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat správce certifikátů Sectigo z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte do **podnikové aplikace** a pak vyberte **všechny aplikace** možnost.

    ![V okně podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **správce certifikátů Sectigo**vyberte **správce certifikátů Sectigo** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

    ![Správce certifikátů Sectigo v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části nakonfigurujete a otestovat Azure AD jednotné přihlašování pomocí Sectigo správce certifikátů na základě testovací uživatele volá **Britta Simon**.
Pro jednotné přihlašování pro práci je potřeba navázat vztah odkazu mezi uživatele služby Azure AD a související uživatelské ve Správci certifikátů Sectigo.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování pomocí Správce certifikátů Sectigo, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Konfigurace správce certifikátů Sectigo Single Sign-On](#configure-sectigo-certificate-manager-single-sign-on)**  – ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
3. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
4. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
5. **[Vytvořit testovacího uživatele správce certifikátů Sectigo](#create-sectigo-certificate-manager-test-user)**  – Pokud chcete mít protějšek Britta Simon v Sectigo správce certifikátů, který je propojený s Azure AD reprezentace uživatele.
6. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure portal.

Ke konfiguraci Azure AD jednotné přihlašování pomocí Správce certifikátů Sectigo, proveďte následující kroky:

1. V [webu Azure portal](https://portal.azure.com/)na **správce certifikátů Sectigo** integrace stránce aplikace vyberte **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz](common/select-sso.png)

2. Na **vybrat jedinou metodu přihlašování** dialogového okna, vyberte **SAML/WS-Fed** chcete povolit jednotné přihlašování.

    ![Jednotné přihlašování režim výběru](common/select-saml-option.png)

3. Na **nastavte si jednotné přihlašování pomocí SAML** klikněte na **upravit** ikony otevřete **základní konfiguraci SAML** dialogového okna.

    ![Upravit konfiguraci základní SAML](common/edit-urls.png)

4. Na **základní konfiguraci SAML** části, pokud chcete nakonfigurovat aplikace v **IDP** iniciované režimu, proveďte následující kroky:

    ![Správce certifikátů Sectigo domény a adresy URL jednotného přihlašování – informace](common/idp-relay.png)

    a. V **identifikátor** textové pole, zadejte některou z adresy URL:
    
    | |
    |--|
    | `https://cert-manager.com/shibboleth` |
    | `https://hard.cert-manager.com/shibboleth` |

    b. V **adresy URL odpovědi** textové pole, zadejte některou z adresy URL:

    | |
    |--|
    | `https://cert-manager.com/Shibboleth.sso/SAML2/POST` |
    | `https://hard.cert-manager.com/Shibboleth.sso/SAML2/POST` |

    c. Klikněte na tlačítko **nastavit další adresy URL**.

    d. V **stav přenosu** textové pole, zadejte některou z adresy URL:
    
    | |
    |--|
    | `https://cert-manager.com/customer/SSLSupport/idp` |
    | `https://hard.cert-manager.com/customer/SSLSupport/idp` |

5.  Pokud chcete nakonfigurovat aplikace v **SP** iniciované režimu, proveďte následující kroky:

    ![Správce certifikátů Sectigo domény a adresy URL jednotného přihlašování – informace](common/both-signonurl.png)

    V **přihlašovací adresa URL** textové pole, zadejte některou z adresy URL:
    
    | |
    |--|
    | `https://cert-manager.com/Shibboleth.sso/Login`|
    | `https://hard.cert-manager.com/Shibboleth.sso/Login`|

6. Na **nastavte si jednotné přihlašování pomocí SAML** stránku, **podpisový certifikát SAML** klikněte na tlačítko **Stáhnout** ke stažení **certifikát (Base64)** z se zadanými možnostmi podle vašich požadavků a uložit je ve vašem počítači.

    ![Odkaz ke stažení certifikátu](common/certificatebase64.png)

7. Na **nastavte Sectigo certifikát Manager** tématu, zkopírujte příslušné adresy URL podle vašich požadavků.

    ![Zkopírování adresy URL konfigurace](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor Azure AD

    c. Adresa URL – odhlášení

### <a name="configure-sectigo-certificate-manager-single-sign-on"></a>Konfigurace Sectigo správce certifikátů jednotné přihlašování

Ke konfiguraci jednotného přihlašování na **správce certifikátů Sectigo** straně, je nutné odeslat na stažený **certifikát (Base64)** a vhodné zkopírovaný adresy URL z webu Azure portal [Sectigo Správce certifikátů tým podpory](https://sectigo.com/support). Nastavují tohoto nastavení můžete mít správně nastavené na obou stranách připojení SAML SSO.

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

V této části je povolit Britta Simon používat jednotné přihlašování Azure díky udělení přístupu k Sectigo správce certifikátů.

1. Na webu Azure Portal, vyberte **podnikové aplikace**vyberte **všechny aplikace**a pak vyberte **správce certifikátů Sectigo**.

    ![Okno aplikace organizace](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **správce certifikátů Sectigo**.

    ![Správce certifikátů Sectigo odkaz v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **přidat uživatele** tlačítko a pak vyberte **uživatelů a skupin** v **přidat přiřazení** dialogového okna.

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V **uživatelů a skupin** dialogové okno Vybrat **Britta Simon** v seznamu uživatelů, klikněte **vyberte** tlačítko v dolní části obrazovky.

6. Pokud očekáváte libovolnou hodnotu role v kontrolní výraz SAML a potom v **vybrat roli** dialogové okno vybrat vhodnou roli pro uživatele ze seznamu, klikněte **vyberte** tlačítko v dolní části obrazovky.

7. V **přidat přiřazení** dialogové okno kliknutím **přiřadit** tlačítko.

### <a name="create-sectigo-certificate-manager-test-user"></a>Vytvoření správce certifikátů Sectigo testovacího uživatele

V této části vytvoříte uživatele volá ve Správci certifikátů Sectigo Britta Simon. Práce s [tým podpory správce certifikátů Sectigo](https://sectigo.com/support) přidat uživatele na platformě Sectigo správce certifikátů. Uživatelé musí vytvořit a aktivovat, než použití jednotného přihlašování.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování 

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na dlaždici správce certifikátů Sectigo na přístupovém panelu, které by měl být automaticky přihlášeni do Sectigo certifikát správce, u kterého nastavíte jednotné přihlašování. Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další prostředky

- [ Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

