---
title: 'Kurz: Integrace Azure Active Directory s systém jednotného přihlašování GTNexus | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a systém GTNexus jednotného přihlašování.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: e677a161-1662-4eb3-b48a-b2835470b59e
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/08/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: b3caea8db02950f6e73ad8ed1657ae4b688af6ca
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2019
ms.locfileid: "56186569"
---
# <a name="tutorial-azure-active-directory-integration-with-gtnexus-sso-system"></a>Kurz: Integrace Azure Active Directory s GTNexus systém jednotného přihlašování

V tomto kurzu se dozvíte, jak integrovat GTNexus systém jednotného přihlašování s Azure Active Directory (Azure AD).
Integrace systému GTNexus jednotného přihlašování s Azure AD poskytuje následující výhody:

* Můžete řídit ve službě Azure AD, který má přístup k systému GTNexus jednotného přihlašování.
* Uživatelům se automaticky přihlášeni k GTNexus systém jednotného přihlašování (Single Sign-On) můžete povolit pomocí jejich účtů služby Azure AD.
* Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s GTNexus systém jednotného přihlašování, budete potřebovat následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční zkušební verze [zde](https://azure.microsoft.com/pricing/free-trial/)
* Systém jednotného přihlašování GTNexus jednotného přihlašování povolená předplatného

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu konfigurace a testování v testovacím prostředí Azure AD jednotného přihlašování.

* Podporuje systém jednotného přihlašování GTNexus **IDP** jednotné přihlašování zahájené pomocí

## <a name="adding-gtnexus-sso-system-from-the-gallery"></a>Přidání GTNexus systém jednotného přihlašování z Galerie

Konfigurace integrace systému GTNexus jednotné přihlašování do služby Azure AD, budete muset přidat GTNexus systém jednotného přihlašování na váš seznam spravovaných aplikací SaaS z galerie.

**Chcete-li přidat GTNexus systém jednotného přihlašování z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte do **podnikové aplikace** a pak vyberte **všechny aplikace** možnost.

    ![V okně podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **systém jednotného přihlašování GTNexus**vyberte **systém jednotného přihlašování GTNexus** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

     ![Systém GTNexus jednotného přihlašování v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části, konfigurace a testování služby Azure AD jednotné přihlašování s GTNexus jednotného přihlašování systém založený na uživateli testu **Britta Simon**.
Pro jednotné přihlašování pro práci je potřeba navázat vztah odkazu mezi uživatele služby Azure AD a souvisejících uživatelem v systému GTNexus jednotného přihlašování.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s GTNexus systém jednotného přihlašování, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Konfigurace GTNexus jednotného přihlašování systému Single Sign-On](#configure-gtnexus-sso-system-single-sign-on)**  – ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
3. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
4. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
5. **[Vytvořit testovacího uživatele systém jednotného přihlašování GTNexus](#create-gtnexus-sso-system-test-user)**  – Pokud chcete mít protějšek Britta Simon v systému GTNexus jednotného přihlašování, který je propojený s Azure AD reprezentace uživatele.
6. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure portal.

Chcete-li nakonfigurovat systém GTNexus jednotného přihlašování Azure AD jednotného přihlašování, postupujte následovně:

1. V [webu Azure portal](https://portal.azure.com/)na **systém jednotného přihlašování GTNexus** integrace stránce aplikace vyberte **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz](common/select-sso.png)

2. Na **vybrat jedinou metodu přihlašování** dialogového okna, vyberte **SAML/WS-Fed** chcete povolit jednotné přihlašování.

    ![Jednotné přihlašování režim výběru](common/select-saml-option.png)

3. Na **nastavte si jednotné přihlašování pomocí SAML** klikněte na **upravit** ikony otevřete **základní konfiguraci SAML** dialogového okna.

    ![Upravit konfiguraci základní SAML](common/edit-urls.png)

4. Na **základní konfiguraci SAML** části, pokud máte **soubor metadat poskytovatele služeb**, proveďte následující kroky:

    a. Klikněte na tlačítko **nahrát soubor metadat**.

    ![image](common/upload-metadata.png)

    b. Klikněte na **složky logo** vyberte soubor metadat a klikněte na **nahrát**.

    ![image](common/browse-upload-metadata.png)

    c. Po úspěšném odeslání souboru metadat **identifikátor** a **adresy URL odpovědi** hodnoty získat automaticky vyplní v textovém poli části GTNexus systém jednotného přihlašování:

    ![image](common/idp-intiated.png)

    > [!Note]
    > Pokud **identifikátor** a **adresy URL odpovědi** nejsou u nich použité polulated automatické hodnoty a potom zadejte hodnoty ručně podle vašich požadavků.

5. Na **nastavte si jednotné přihlašování pomocí SAML** stránku, **podpisový certifikát SAML** klikněte na tlačítko **Stáhnout** ke stažení **kód XML metadat federace**  z se zadanými možnostmi podle vašich požadavků a uložit je ve vašem počítači.

    ![Odkaz ke stažení certifikátu](common/metadataxml.png)

### <a name="configure-gtnexus-sso-system-single-sign-on"></a>Konfigurace jednotného přihlašování GTNexus systém jednotného přihlašování

Ke konfiguraci jednotného přihlašování na **systém jednotného přihlašování GTNexus** straně, je nutné odeslat **kód XML metadat federace** k [systém jednotného přihlašování GTNexus tým podpory](mailto:support@gtnexus.com). Nastavují tohoto nastavení můžete mít správně nastavené na obou stranách připojení SAML SSO.

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

V této části je povolit Britta Simon používat jednotné přihlašování Azure díky udělení přístupu k systému GTNexus jednotného přihlašování.

1. Na webu Azure Portal, vyberte **podnikové aplikace**vyberte **všechny aplikace**a pak vyberte **systém jednotného přihlašování GTNexus**.

    ![Okno aplikace organizace](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **systém jednotného přihlašování GTNexus**.

    ![Systém jednotného přihlašování GTNexus odkaz v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **přidat uživatele** tlačítko a pak vyberte **uživatelů a skupin** v **přidat přiřazení** dialogového okna.

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V **uživatelů a skupin** dialogové okno Vybrat **Britta Simon** v seznamu uživatelů, klikněte **vyberte** tlačítko v dolní části obrazovky.

6. Pokud očekáváte libovolnou hodnotu role v kontrolní výraz SAML a potom v **vybrat roli** dialogové okno vybrat vhodnou roli pro uživatele ze seznamu, klikněte **vyberte** tlačítko v dolní části obrazovky.

7. V **přidat přiřazení** dialogové okno kliknutím **přiřadit** tlačítko.

### <a name="create-gtnexus-sso-system-test-user"></a>Vytvořit systém jednotného přihlašování GTNexus testovacího uživatele

V této části vytvořte uživatele Britta Simon systému GTNexus jednotného přihlašování. Práce s [tým podpory systém jednotného přihlašování GTNexus](mailto:support@gtnexus.com) přidat uživatele na platformě GTNexus systém jednotného přihlašování. Uživatelé musí vytvořit a aktivovat, než použití jednotného přihlašování.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na dlaždici GTNexus systém jednotného přihlašování na přístupovém panelu, můžete by měl být automaticky přihlášeni k systému GTNexus jednotného přihlašování, u kterého nastavíte jednotné přihlašování. Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další prostředky

- [ Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
