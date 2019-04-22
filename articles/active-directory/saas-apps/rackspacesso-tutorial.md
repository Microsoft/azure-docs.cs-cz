---
title: 'Kurz: Integrace Azure Active Directory s jednotným Přihlašováním Rackspace | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a jednotné přihlašování Rackspace.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 36b398be-2f7e-4ce8-9031-53587299bc4a
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/15/2019
ms.author: jeedes
ms.openlocfilehash: ca6667a5353d253743a45d5db742811d8e15ed31
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/17/2019
ms.locfileid: "59682113"
---
# <a name="tutorial-azure-active-directory-integration-with-rackspace-sso"></a>Kurz: Integrace Azure Active Directory s jednotným Přihlašováním Rackspace

V tomto kurzu se dozvíte, jak integrovat Rackspace jednotného přihlašování s Azure Active Directory (Azure AD).
Integrace Rackspace jednotného přihlašování s Azure AD poskytuje následující výhody:

* Můžete řídit ve službě Azure AD, který má přístup k Rackspace jednotného přihlašování.
* Uživatelům se automaticky přihlášeni k Rackspace jednotné přihlašování (Single Sign-On) můžete povolit pomocí jejich účtů služby Azure AD.
* Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s jednotným Přihlašováním Rackspace, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat [bezplatný účet](https://azure.microsoft.com/free/)
* Jednotné přihlašování Rackspace jednotného přihlašování povolená předplatného

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu konfigurace a testování v testovacím prostředí Azure AD jednotného přihlašování.

* Podporuje jednotné přihlašování Rackspace **SP** jednotné přihlašování zahájené pomocí

## <a name="adding-rackspace-sso-from-the-gallery"></a>Přidání jednotného přihlašování Rackspace z Galerie

Konfigurace integrace Rackspace jednotné přihlašování do služby Azure AD, budete muset přidat Rackspace jednotného přihlašování na váš seznam spravovaných aplikací SaaS z galerie.

**Chcete-li přidat Rackspace jednotné přihlašování z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte do **podnikové aplikace** a pak vyberte **všechny aplikace** možnost.

    ![V okně podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **jednotného přihlašování Rackspace**vyberte **jednotného přihlašování Rackspace** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

     ![Jednotné přihlašování Rackspace v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části nakonfigurujete a testovací služby Azure AD jednotné přihlašování s jednotným Přihlašováním Rackspace podle testovacího uživatele volá **Britta Simon**.
Pro jednotné přihlašování pro práci je potřeba navázat vztah odkazu mezi uživatele služby Azure AD a související uživatelské v Rackspace jednotného přihlašování.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s jednotným Přihlašováním Rackspace, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Konfigurace Rackspace jednotného přihlašování Single Sign-On](#configure-rackspace-sso-single-sign-on)**  – ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
3. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
4. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
5. **[Vytvořit testovacího uživatele jednotného přihlašování Rackspace](#create-rackspace-sso-test-user)**  – Pokud chcete mít protějšek Britta Simon v Rackspace jednotného přihlašování, který je propojený s Azure AD reprezentace uživatele.
6. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure portal.

Ke konfiguraci Azure AD jednotné přihlašování s jednotným Přihlašováním Rackspace, proveďte následující kroky:

1. V [webu Azure portal](https://portal.azure.com/)na **jednotného přihlašování Rackspace** integrace stránce aplikace vyberte **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz](common/select-sso.png)

2. Na **vybrat jedinou metodu přihlašování** dialogového okna, vyberte **SAML/WS-Fed** chcete povolit jednotné přihlašování.

    ![Jednotné přihlašování režim výběru](common/select-saml-option.png)

3. Na **nastavte si jednotné přihlašování pomocí SAML** klikněte na **upravit** ikony otevřete **základní konfiguraci SAML** dialogového okna.

    ![Upravit konfiguraci základní SAML](common/edit-urls.png)

4. Na **základní konfiguraci SAML** části nahrávání **soubor metadat poskytovatele služeb** kterou si můžete stáhnout z [URL](https://login.rackspace.com/federate/sp.xml) a proveďte následující kroky:

    a. Klikněte na tlačítko **nahrát soubor metadat**.

    ![image](common/upload-metadata.png)

    b. Klikněte na **složky logo** vyberte soubor metadat a klikněte na **nahrát**.

    ![image](common/browse-upload-metadata.png)

    c. Po úspěšném odeslání souboru metadat získat potřebné adresy URL automaticky vyplní automaticky.

    d. V **přihlašovací adresa URL** textové pole, zadejte adresu URL: `https://login.rackspace.com/federate/`

    ![Rackspace jednotného přihlašování k doméně a adresy URL jednotného přihlašování – informace](common/sp-signonurl.png)   

5. Na **nastavte si jednotné přihlašování pomocí SAML** stránku, **podpisový certifikát SAML** klikněte na tlačítko **Stáhnout** ke stažení **kód XML metadat federace**  z se zadanými možnostmi podle vašich požadavků a uložit je ve vašem počítači.

    ![Odkaz ke stažení certifikátu](common/metadataxml.png)

6. Na **nastavit jednotné přihlašování Rackspace** tématu, zkopírujte příslušné adresy URL podle vašich požadavků.

    ![Zkopírování adresy URL konfigurace](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor Azure AD

    c. Adresa URL – odhlášení

### <a name="configure-rackspace-sso-single-sign-on"></a>Konfigurace jednotného přihlašování Rackspace jednotného přihlašování

Ke konfiguraci jednotného přihlašování na **jednotného přihlašování Rackspace** straně, je nutné odeslat na stažený **kód XML metadat federace** a vhodné zkopírovaný adresy URL z webu Azure portal [tým podpory Rackspace jednotného přihlašování ](https://support.rackspace.com/). Nastavují tohoto nastavení můžete mít správně nastavené na obou stranách připojení SAML SSO.

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

V této části je povolit Britta Simon používat jednotné přihlašování Azure díky udělení přístupu k Rackspace jednotného přihlašování.

1. Na webu Azure Portal, vyberte **podnikové aplikace**vyberte **všechny aplikace**a pak vyberte **jednotného přihlašování Rackspace**.

    ![Okno aplikace organizace](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **jednotného přihlašování Rackspace**.

    ![Jednotné přihlašování Rackspace odkaz v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **přidat uživatele** tlačítko a pak vyberte **uživatelů a skupin** v **přidat přiřazení** dialogového okna.

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V **uživatelů a skupin** dialogové okno Vybrat **Britta Simon** v seznamu uživatelů, klikněte **vyberte** tlačítko v dolní části obrazovky.

6. Pokud očekáváte libovolnou hodnotu role v kontrolní výraz SAML a potom v **vybrat roli** dialogové okno vybrat vhodnou roli pro uživatele ze seznamu, klikněte **vyberte** tlačítko v dolní části obrazovky.

7. V **přidat přiřazení** dialogové okno kliknutím **přiřadit** tlačítko.

### <a name="create-rackspace-sso-test-user"></a>Vytvořit testovacího uživatele Rackspace jednotného přihlašování

V této části vytvořte uživatele Britta Simon v Rackspace jednotného přihlašování. Práce s [tým podpory jednotného přihlašování Rackspace](https://support.rackspace.com/) přidat uživatele na platformě Rackspace jednotného přihlašování. Uživatelé musí vytvořit a aktivovat, než použití jednotného přihlašování.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování 

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na dlaždici Rackspace jednotného přihlašování na přístupovém panelu, můžete by měl být automaticky přihlášeni na jednotné přihlašování Rackspace, u kterého nastavíte jednotné přihlašování. Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další prostředky

- [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

