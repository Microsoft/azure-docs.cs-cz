---
title: 'Kurz: Integrace Azure Active Directory s jednotným Přihlašováním Vault.cloud Veritas Enterprise | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a jednotné přihlašování Vault.cloud Veritas Enterprise.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: c47894b1-f5df-4755-845d-f12f4c602dc4
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/28/2019
ms.author: jeedes
ms.openlocfilehash: 59012bf32a4e1f0532b4d42e510d431180c35730
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/08/2019
ms.locfileid: "59264178"
---
# <a name="tutorial-azure-active-directory-integration-with-veritas-enterprise-vaultcloud-sso"></a>Kurz: Integrace Azure Active Directory s jednotným Přihlašováním Vault.cloud Veritas Enterprise

V tomto kurzu se dozvíte, jak integrovat Veritas Enterprise Vault.cloud jednotného přihlašování s Azure Active Directory (Azure AD).
Integrace Veritas Enterprise Vault.cloud jednotného přihlašování s Azure AD poskytuje následující výhody:

* Můžete řídit ve službě Azure AD, který má přístup k Veritas Enterprise Vault.cloud jednotného přihlašování.
* Uživatelům se automaticky přihlášeni k Veritas Enterprise Vault.cloud jednotné přihlašování (Single Sign-On) můžete povolit pomocí jejich účtů služby Azure AD.
* Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s jednotným Přihlašováním Vault.cloud Enterprise Veritas, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat [bezplatný účet](https://azure.microsoft.com/free/)
* Jednotné přihlašování Vault.cloud Enterprise VERITAS jednotného přihlašování povolená předplatného

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu konfigurace a testování v testovacím prostředí Azure AD jednotného přihlašování.

* Podporuje jednotné přihlašování Vault.cloud Enterprise Veritas **SP** jednotné přihlašování zahájené pomocí

## <a name="adding-veritas-enterprise-vaultcloud-sso-from-the-gallery"></a>Přidání jednotného přihlašování Vault.cloud Enterprise Veritas z Galerie

Pokud chcete nakonfigurovat integraci jednotného přihlašování Vault.cloud Veritas organizace do služby Azure AD, budete muset přidat z Galerie Veritas Enterprise Vault.cloud jednotného přihlašování na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat Veritas Enterprise Vault.cloud jednotné přihlašování z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte do **podnikové aplikace** a pak vyberte **všechny aplikace** možnost.

    ![V okně podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **Veritas Enterprise Vault.cloud jednotného přihlašování**vyberte **Veritas Enterprise Vault.cloud jednotného přihlašování** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

     ![VERITAS Enterprise Vault.cloud jednotné přihlašování v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části, konfigurace a testování Azure AD jednotné přihlašování s Veritas Enterprise Vault.cloud jednotného přihlašování na základě testovací uživatele volá **Britta Simon**.
Pro jednotné přihlašování pro práci je potřeba navázat vztah odkazu mezi uživatele služby Azure AD a související uživatelské v Veritas Enterprise Vault.cloud jednotného přihlašování.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s jednotným Přihlašováním Vault.cloud Veritas organizace, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Konfigurace Veritas Enterprise Vault.cloud jednotného přihlašování Single Sign-On](#configure-veritas-enterprise-vaultcloud-sso-single-sign-on)**  – ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
3. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
4. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
5. **[Vytvořit testovacího uživatele Veritas Enterprise Vault.cloud SSO](#create-veritas-enterprise-vaultcloud-sso-test-user)**  – Pokud chcete mít protějšek Britta Simon Veritas Enterprise Vault.cloud SSO, který je propojený s Azure AD reprezentace uživatele.
6. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure portal.

Ke konfiguraci Azure AD jednotné přihlašování s jednotným Přihlašováním Vault.cloud Enterprise Veritas, proveďte následující kroky:

1. V [webu Azure portal](https://portal.azure.com/)na **Veritas Enterprise Vault.cloud SSO** integrace stránce aplikace vyberte **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz](common/select-sso.png)

2. Na **vybrat jedinou metodu přihlašování** dialogového okna, vyberte **SAML/WS-Fed** chcete povolit jednotné přihlašování.

    ![Jednotné přihlašování režim výběru](common/select-saml-option.png)

3. Na **nastavte si jednotné přihlašování pomocí SAML** klikněte na **upravit** ikony otevřete **základní konfiguraci SAML** dialogového okna.

    ![Upravit konfiguraci základní SAML](common/edit-urls.png)

4. Na **základní konfiguraci SAML** části, proveďte následující kroky:

    ![VERITAS Enterprise Vault.cloud jednotného přihlašování k doméně a adresy URL jednotného přihlašování – informace](common/sp-identifier-reply.png)

    a. V **přihlašovací adresa URL** textové pole, zadejte adresu URL, pomocí následujícího vzorce: `https://personal.ap.archive.veritas.com/CID=<CUSTOMERID>`

    b. V **identifikátor** pole, použijte adresu URL podle datového centra:

    | Datové centrum| zprostředkovatele identity |
    |----------|----|
    | Severní Amerika| `https://auth.lax.archivecloud.net` |
    | Evropa | `https://auth.ams.archivecloud.net` |
    | Asie a Tichomoří| `https://auth.syd.archivecloud.net`|

    c. V **adresy URL odpovědi** textové pole, použijte adresu URL podle datového centra:

    | Datové centrum| zprostředkovatele identity |
    |----------|----|
    | Severní Amerika| `https://auth.lax.archivecloud.net` |
    | Evropa | `https://auth.ams.archivecloud.net` |
    | Asie a Tichomoří| `https://auth.syd.archivecloud.net`|

    > [!NOTE]
    > Tato hodnota není skutečný. Aktualizujte tuto hodnotu skutečné přihlašovací adresa URL. Kontakt [tým podpory Veritas Enterprise Vault.cloud jednotného přihlašování Client](https://www.veritas.com/support/.html) tuto výhodu získáte. Můžete také odkazovat na tyto vzory se dají ukazuje **základní konfiguraci SAML** části webu Azure Portal.

5. Na **nastavte si jednotné přihlašování pomocí SAML** stránku, **podpisový certifikát SAML** klikněte na tlačítko **Stáhnout** ke stažení **certifikát (Base64)** z se zadanými možnostmi podle vašich požadavků a uložit je ve vašem počítači.

    ![Odkaz ke stažení certifikátu](common/certificatebase64.png)

6. Na **nastavit jednotné přihlašování Vault.cloud Enterprise Veritas** tématu, zkopírujte příslušné adresy URL podle vašich požadavků.

    ![Zkopírování adresy URL konfigurace](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor Azure AD

    c. Adresa URL – odhlášení

### <a name="configure-veritas-enterprise-vaultcloud-sso-single-sign-on"></a>Konfigurace Veritas podnikové jednotné přihlašování Vault.cloud jednotného přihlašování

Ke konfiguraci jednotného přihlašování na **Veritas Enterprise Vault.cloud SSO** straně, je nutné odeslat na stažený **certifikát (Base64)** a vhodné zkopírovaný adresy URL z webu Azure portal [Veritas Tým podpory jednotného přihlašování k podnikové Vault.cloud](https://www.veritas.com/support/.html). Nastavují tohoto nastavení můžete mít správně nastavené na obou stranách připojení SAML SSO.

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD 

Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

1. Na webu Azure Portal, v levém podokně vyberte **Azure Active Directory**vyberte **uživatelé**a pak vyberte **všichni uživatelé**.

    !["Uživatele a skupiny" a "Všechny uživatele" odkazy](common/users.png)

2. Vyberte **nového uživatele** v horní části obrazovky.

    ![Tlačítko Nový uživatel](common/new-user.png)

3. Ve vlastnosti uživatele proveďte následující kroky.

    ![Dialogové okno uživatele](common/user-properties.png)

    a. V **název** zadat **BrittaSimon**.
  
    b. V **uživatelské jméno** typ pole brittasimon@yourcompanydomain.extension. Například BrittaSimon@contoso.com.

    c. Vyberte **zobrazit heslo** zaškrtněte políčko a zapište si hodnotu, která se zobrazí v poli heslo.

    d. Klikněte na možnost **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části je povolit Britta Simon používat jednotné přihlašování Azure díky udělení přístupu k Veritas Enterprise Vault.cloud jednotného přihlašování.

1. Na webu Azure Portal, vyberte **podnikové aplikace**vyberte **všechny aplikace**a pak vyberte **Veritas Enterprise Vault.cloud SSO**.

    ![Okno aplikace organizace](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **Veritas Enterprise Vault.cloud SSO**.

    ![Odkaz Veritas Enterprise Vault.cloud jednotného přihlašování v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **přidat uživatele** tlačítko a pak vyberte **uživatelů a skupin** v **přidat přiřazení** dialogového okna.

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V **uživatelů a skupin** dialogové okno Vybrat **Britta Simon** v seznamu uživatelů, klikněte **vyberte** tlačítko v dolní části obrazovky.

6. Pokud očekáváte libovolnou hodnotu role v kontrolní výraz SAML a potom v **vybrat roli** dialogové okno vybrat vhodnou roli pro uživatele ze seznamu, klikněte **vyberte** tlačítko v dolní části obrazovky.

7. V **přidat přiřazení** dialogové okno kliknutím **přiřadit** tlačítko.

### <a name="create-veritas-enterprise-vaultcloud-sso-test-user"></a>Vytvořit testovacího uživatele Veritas Enterprise Vault.cloud jednotného přihlašování

V této části vytvořte uživatele Britta Simon v Veritas Enterprise Vault.cloud jednotného přihlašování. Práce s [tým podpory jednotného přihlašování Vault.cloud Enterprise Veritas](https://www.veritas.com/support/.html) přidat uživatele na platformě Veritas Enterprise Vault.cloud jednotného přihlašování. Uživatelé musí vytvořit a aktivovat, než použití jednotného přihlašování.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování 

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na dlaždici Veritas Enterprise Vault.cloud jednotného přihlašování na přístupovém panelu, můžete by měl být automaticky přihlášeni k Vault.cloud Veritas Enterprise, jednotné přihlašování u kterého nastavíte jednotné přihlašování. Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další prostředky

- [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

