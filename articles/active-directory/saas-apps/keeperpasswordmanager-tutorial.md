---
title: 'Kurz: Integrace Azure Active Directory pomocí správce hesel držitel & digitální trezor | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a heslo správce držitel & digitální trezoru.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: e1a98f6a-2dae-4734-bdbf-4fba742a61d2
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/02/2019
ms.author: jeedes
ms.openlocfilehash: 70dfc03c37b2e7a93218ff7e710dbd22f622d418
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/23/2019
ms.locfileid: "54813300"
---
# <a name="tutorial-azure-active-directory-integration-with-keeper-password-manager--digital-vault"></a>Kurz: Integrace Azure Active Directory pomocí správce hesel držitel & digitální trezoru

V tomto kurzu se dozvíte, jak integrovat správce hesel držitel & digitální trezor s Azure Active Directory (Azure AD).
Integrace správce hesel držitel & digitální trezor s Azure AD poskytuje následující výhody:

* Můžete řídit ve službě Azure AD, který má přístup správce hesel držitel & digitální trezoru.
* Uživatelům se automaticky přihlášeni do správce hesel držitel & digitální trezoru (Single Sign-On) můžete povolit pomocí jejich účtů služby Azure AD.
* Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Integrace služby Azure AD nakonfigurovat správce hesel držitel & digitální trezoru, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční zkušební verze [zde](https://azure.microsoft.com/pricing/free-trial/)
* Správce hesel držitel & digitální trezor jednotného přihlašování povolená předplatného

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu konfigurace a testování v testovacím prostředí Azure AD jednotného přihlašování.

* Správce hesel držitel & digitální trezor podporuje **SP** jednotné přihlašování zahájené pomocí

* Správce hesel držitel & digitální trezor podporuje **JIT** zřizování uživatelů

## <a name="adding-keeper-password-manager--digital-vault-from-the-gallery"></a>Přidání správce hesel držitel & digitální trezoru z Galerie

Ke konfiguraci správce hesel držitel & digitální trezor integraci do služby Azure AD, musíte přidat správce hesel držitel & digitální trezoru z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat správce hesel držitel & digitální trezoru z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte do **podnikové aplikace** a pak vyberte **všechny aplikace** možnost.

    ![V okně podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **správce hesel držitel & digitální trezor**vyberte **správce hesel držitel & digitální trezor** z panelu výsledků klikněte **přidat** tlačítko přidáte aplikaci.

     ![Správce hesel držitel & digitální trezoru v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části, nakonfigurovat a otestovat Azure AD jednotné přihlašování s držitel heslo správce, & digitální trezor založený na uživateli testu **Britta Simon**.
Pro jednotné přihlašování pro práci je potřeba navázat vztah odkazu mezi uživatele služby Azure AD a související uživatel správce hesel držitel & digitální trezoru.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování pomocí služby správce hesel držitel & digitální trezor, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Nakonfigurovat správce hesel držitel & digitální trezor Single Sign-On](#configure-keeper-password-manager-&-digital-vault-single-sign-on)**  – ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
3. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
4. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
5. **[Vytvoření správce hesel držitel & digitální trezor testovacího uživatele](#create-keeper-password-manager--digital-vault-test-user)**  – Pokud chcete mít protějšek Britta Simon správce hesel držitel & digitální trezor, který je propojený s Azure AD reprezentace uživatele.
6. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure portal.

Ke konfiguraci Azure AD jednotné přihlašování pomocí služby správce hesel držitel & digitální trezoru, proveďte následující kroky:

1. V [webu Azure portal](https://portal.azure.com/)na **správce hesel držitel & digitální trezor** integrace stránce aplikace vyberte **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz](common/select-sso.png)

2. Na **vybrat jedinou metodu přihlašování** dialogového okna, vyberte **SAML/WS-Fed** chcete povolit jednotné přihlašování.

    ![Jednotné přihlašování režim výběru](common/select-saml-option.png)

3. Na **nastavte si jednotné přihlašování pomocí SAML** klikněte na **upravit** ikony otevřete **základní konfiguraci SAML** dialogového okna.

    ![Upravit konfiguraci základní SAML](common/edit-urls.png)

4. Na **základní konfiguraci SAML** části, proveďte následující kroky:

    ![Správce hesel držitel & digitální trezor domény a adresy URL jednotného přihlašování – informace](common/sp-identifier-reply.png)

    a. V **přihlašovací adresa URL** textové pole, zadejte adresu URL, pomocí následujícího vzorce: `https://{SSO CONNECT SERVER}/sso-connect/saml/login`

    b. V **identifikátor (Entity ID)** textové pole, zadejte adresu URL, pomocí následujícího vzorce: `https://{SSO CONNECT SERVER}/sso-connect`

    c. V **adresy URL odpovědi** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://{SSO CONNECT SERVER}/sso-connect/saml/sso`

    > [!NOTE]
    > Tyto hodnoty nejsou skutečný. Aktualizujte tyto hodnoty skutečné přihlašovací adresa URL, identifikátoru a adresa URL odpovědi. Kontakt [tým podpory pro správce hesel držitel & digitální klient trezoru](https://keepersecurity.com/contact.html) k získání těchto hodnot. Můžete také odkazovat na tyto vzory se dají ukazuje **základní konfiguraci SAML** části webu Azure Portal.

5. Na **nastavte si jednotné přihlašování pomocí SAML** stránku, **podpisový certifikát SAML** klikněte na tlačítko **Stáhnout** ke stažení **Fedderation soubor XML s metadaty**  z se zadanými možnostmi podle vašich požadavků a uložit je ve vašem počítači.

    ![Odkaz ke stažení certifikátu](common/metadataxml.png)

6. Na **nastavení správce hesel držitel & digitální trezor** tématu, zkopírujte příslušné adresy URL podle vašich požadavků.

    ![Zkopírování adresy URL konfigurace](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor služby Azure Ad

    c. Adresa URL – odhlášení

### <a name="configure-keeper-password-manager--digital-vault-single-sign-on"></a>Nakonfigurovat správce hesel držitel & digitální trezor jednotného přihlašování

Ke konfiguraci jednotného přihlašování na **správce hesel držitel & digitální konfigurace trezoru** straně, postupujte podle pokynů uvedených v [průvodce podpory držitel](https://keepersecurity.com/assets/pdf/KeeperSSOConnect_v11.pdf).

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

V této části je povolit Britta Simon používat jednotné přihlašování Azure díky udělení přístupu správce hesel držitel & digitální trezoru.

1. Na webu Azure Portal, vyberte **podnikové aplikace**vyberte **všechny aplikace**a pak vyberte **správce hesel držitel & digitální trezor**.

    ![Okno aplikace organizace](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **správce hesel držitel & digitální trezor**.

    ![Správce hesel držitel & digitální trezor odkaz v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **přidat uživatele** tlačítko a pak vyberte **uživatelů a skupin** v **přidat přiřazení** dialogového okna.

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V **uživatelů a skupin** dialogové okno Vybrat **Britta Simon** v seznamu uživatelů, klikněte **vyberte** tlačítko v dolní části obrazovky.

6. Pokud očekáváte libovolnou hodnotu role v kontrolní výraz SAML a potom v **vybrat roli** dialogové okno vybrat vhodnou roli pro uživatele ze seznamu, klikněte **vyberte** tlačítko v dolní části obrazovky.

7. V **přidat přiřazení** dialogové okno kliknutím **přiřadit** tlačítko.

### <a name="create-keeper-password-manager--digital-vault-test-user"></a>Vytvoření správce hesel držitel & digitální trezor testovacího uživatele

Povolit Azure AD uživatelům přihlášení do správce hesel držitel & digitální trezoru, musí být poskytnuty do správce hesel držitel & digitální trezoru. Aplikace podporuje pouze v době zřizování uživatelů a po ověření uživatele budou vytvořeny v aplikaci automaticky. Můžete kontaktovat [držitel podporu](https://keepersecurity.com/contact.html), pokud chcete nastavit uživatele ručně.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na dlaždici správce hesel držitel & digitální trezoru na přístupovém panelu, můžete by měl být automaticky přihlášeni do správce hesel držitel & digitální trezor, u kterého nastavíte jednotné přihlašování. Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další prostředky

- [ Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

