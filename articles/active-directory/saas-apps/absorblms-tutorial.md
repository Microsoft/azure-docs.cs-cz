---
title: 'Kurz: Integrace Azure Active Directory s vyrovnat se s LMS | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a chránit před LMS.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: ba9f1b3d-a4a0-4ff7-b0e7-428e0ed92142
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/31/2018
ms.author: jeedes
ms.openlocfilehash: f7f96b4357e7db61d3b5d30b93eff8960e515c2d
ms.sourcegitcommit: 803e66de6de4a094c6ae9cde7b76f5f4b622a7bb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/02/2019
ms.locfileid: "53971286"
---
# <a name="tutorial-azure-active-directory-integration-with-absorb-lms"></a>Kurz: Integrace s LMS vyrovnat se s Azure Active Directory

V tomto kurzu se dozvíte, jak integrovat LMS vyrovnat se s Azure Active Directory (Azure AD).
Chránit před LMS integrace s Azure AD poskytuje následující výhody:

* Můžete řídit ve službě Azure AD, který má přístup k vyrovnat se s LMS.
* Uživatelům se automaticky přihlášeni k vyrovnat se s LMS (Single Sign-On) můžete povolit pomocí jejich účtů služby Azure AD.
* Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s vyrovnat se s LMS, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční zkušební verze [zde](https://azure.microsoft.com/pricing/free-trial/)
* Chránit před LMS jednotné přihlášení povolený předplatného

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu konfigurace a testování v testovacím prostředí Azure AD jednotného přihlašování.

* Chránit před LMS podporuje **IDP** jednotné přihlašování zahájené pomocí

## <a name="adding-absorb-lms-from-the-gallery"></a>Přidání vyrovnat se s LMS z Galerie

Ke konfiguraci integrace vyrovnat se s LMS do služby Azure AD, budete muset vyrovnat se s LMS přidat z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat vyrovnat se s LMS z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte do **podnikové aplikace** a pak vyberte **všechny aplikace** možnost.

    ![V okně podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **vyrovnat se s LMS**vyberte **vyrovnat se s LMS** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

     ![Chránit před LMS v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části, konfigurace a testování Azure AD jednotného přihlašování se podle testu uživateli LMS vyrovnat se s **Britta Simon**.
Pro jednotné přihlašování pro práci je potřeba navázat vztah odkazu mezi uživatele služby Azure AD a související uživatelské v vyrovnat se s LMS.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s vyrovnat se s LMS, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Konfigurace vyrovnat se s LMS Single Sign-On](#configure-absorb-lms-single-sign-on)**  – ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
3. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
4. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
5. **[Vytvořit testovacího uživatele vyrovnat se s LMS](#create-absorb-lms-test-user)**  – Pokud chcete mít protějšek Britta Simon vyrovnat se s LMS, který je propojený s Azure AD reprezentace uživatele.
6. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure portal.

Chcete-li nakonfigurovat LMS vyrovnat se s Azure AD jednotného přihlašování, postupujte následovně:

1. V [webu Azure portal](https://portal.azure.com/)na **vyrovnat se s LMS** integrace stránce aplikace vyberte **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz](common/select-sso.png)

2. Na **vybrat jedinou metodu přihlašování** dialogového okna, vyberte **SAML/WS-Fed** chcete povolit jednotné přihlašování.

    ![Jednotné přihlašování režim výběru](common/select-saml-option.png)

3. Na **nastavte si jednotné přihlašování pomocí SAML** klikněte na **upravit** ikony otevřete **základní konfiguraci SAML** dialogového okna.

    ![Upravit konfiguraci základní SAML](common/edit-urls.png)

4. Na **nastavte si jednotné přihlašování pomocí SAML** klikněte na **upravit** tlačítko Otevřít **základní konfiguraci SAML** dialogového okna.

    ![Chránit před LMS domény a adresy URL jednotné přihlašování – informace](common/idp-intiated.png)

    Pokud používáte **vyrovnat se s 5 - UI** použijte následující konfiguraci:

    a. V **identifikátor** textové pole, zadejte adresu URL, pomocí následujícího vzorce: `https://company.myabsorb.com/account/saml`

    b. V **adresy URL odpovědi** textové pole, zadejte adresu URL, pomocí následujícího vzorce: `https://company.myabsorb.com/account/saml`

    Pokud používáte **vyrovnat se s 5 – nové prostředí Learner** použijte následující konfiguraci:

    a. V **identifikátor** textové pole, zadejte adresu URL, pomocí následujícího vzorce: `https://company.myabsorb.com/api/rest/v2/authentication/saml`

    b. V **adresy URL odpovědi** textové pole, zadejte adresu URL, pomocí následujícího vzorce: `https://company.myabsorb.com/api/rest/v2/authentication/saml`

    > [!NOTE]
    > Tyto hodnoty nejsou skutečný. Aktualizujte tyto hodnoty se skutečné identifikátorem a adresa URL odpovědi. Kontakt [tým podpory vyrovnat se s klienta LMS](https://support.absorblms.com/hc/) k získání těchto hodnot. Můžete také odkazovat na tyto vzory se dají ukazuje **základní konfiguraci SAML** části webu Azure Portal.

5. Na **nastavte si jednotné přihlašování pomocí SAML** stránku, **podpisový certifikát SAML** klikněte na tlačítko **Stáhnout** ke stažení **soubor XML s metadaty**z se zadanými možnostmi podle vašich požadavků a uložit je ve vašem počítači.

    ![Odkaz ke stažení certifikátu](common/metadataxml.png)

6. Na **nastavení vyrovnat se s LMS** tématu, zkopírujte příslušné adresy URL podle vašich požadavků.

    ![Zkopírování adresy URL konfigurace](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor služby Azure Ad

    c. Adresa URL – odhlášení

### <a name="configure-absorb-lms-single-sign-on"></a>Konfigurace vyrovnat se s LMS jednotného přihlašování

1. V novém okně webového prohlížeče Přihlaste se k serveru vaší společnosti LMS vyrovnat se s jako správce.

2. Vyberte **účet** tlačítko v pravé horní části.

    ![Tlačítko účet](./media/absorblms-tutorial/1.png)

3. V podokně účtu vyberte **nastavení portálu**.

    ![Odkaz Nastavení portálu](./media/absorblms-tutorial/2.png)

4. Vyberte **spravovat nastavení jednotného přihlašování** kartu.

    ![Kartu uživatelé](./media/absorblms-tutorial/managesso.png)

5. Na **spravovat jednotné přihlašování – nastavení** stránce, postupujte takto:

    ![Na stránce Konfigurace jednotného přihlašování](./media/absorblms-tutorial/settings.png)

    a. V **název** textového pole zadejte název, jako je Azure AD Marketplace jednotného přihlašování.

    b. Vyberte **SAML** jako **metoda**.

    c. V poznámkovém bloku otevřete certifikát, který jste si stáhli z webu Azure portal. Odeberte **---BEGIN CERTIFICATE---** a **---END CERTIFICATE---** značky. Potom v **klíč** vložte zbývající obsah.

    d. V **režimu** vyberte **iniciované zprostředkovatele Identity**.

    e. V **Vlastnost Id** vyberte atribut, který jste nakonfigurovali jako identifikátor uživatele ve službě Azure AD. Například pokud *userPrincipalName* je vybrána ve službě Azure AD, vyberte **uživatelské jméno**.

    f. Vyberte **Sha256** jako **typ podpisu**.

    g. V **přihlašovací adresa URL** pole, vložte **adresa URL portálu User Access** z vaší aplikace **vlastnosti** stránky na webu Azure portal.

    h. V **odhlašovací adresa URL**, vložte **odhlašování URL** hodnotu, kterou jste zkopírovali z **nakonfigurovat přihlašování** okna na webu Azure portal.

    i. Přepnout **automaticky přesměrovat** k **na**.

6. Vyberte **uložit.**

    ![Přepnout pouze povolení přihlášení SSO](./media/absorblms-tutorial/save.png)

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

V této části je povolit Britta Simon používat jednotné přihlašování Azure díky udělení přístupu k LMS vyrovnat se s.

1. Na webu Azure Portal, vyberte **podnikové aplikace**vyberte **všechny aplikace**a pak vyberte **vyrovnat se s LMS**.

    ![Okno aplikace organizace](common/enterprise-applications.png)

2. V seznamu aplikace zadejte a vyberte **vyrovnat se s LMS**.

    ![Chránit před LMS odkaz v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **přidat uživatele** tlačítko a pak vyberte **uživatelů a skupin** v **přidat přiřazení** dialogového okna.

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V **uživatelů a skupin** dialogové okno Vybrat **Britta Simon** v seznamu uživatelů, klikněte **vyberte** tlačítko v dolní části obrazovky.

6. Pokud očekáváte libovolnou hodnotu role v kontrolní výraz SAML a potom v **vybrat roli** dialogové okno vybrat vhodnou roli pro uživatele ze seznamu, klikněte **vyberte** tlačítko v dolní části obrazovky.

7. V **přidat přiřazení** dialogové okno kliknutím **přiřadit** tlačítko.

### <a name="create-absorb-lms-test-user"></a>Vytvoření vyrovnat se s LMS testovacího uživatele

Přihlaste se k LMS vyrovnat se s Azure AD uživatelům musí být nastavené v LMS vyrovnat se s. V případě vyrovnat se s LMS zřizování je ruční úloha.

**Konfigurace zřizování uživatelů, proveďte následující kroky:**

1. Přihlaste se k webu společnosti LMS vyrovnat se s jako správce.

2. V **uživatelé** vyberte **uživatelé**.

    ![Odkaz uživatele](./media/absorblms-tutorial/absorblms_userssub.png)

3. Vyberte **uživatele** kartu.

    ![Přidat nový rozevírací seznam](./media/absorblms-tutorial/absorblms_createuser.png)

4. Na **přidat uživatele** stránce, postupujte takto:

    ![Stránka Přidat uživatel](./media/absorblms-tutorial/user.png)

    a. V **křestní jméno** zadejte jméno, například **Britta**.

    b. V **příjmení** zadejte příjmení, jako například **Simon**.

    c. V **uživatelské jméno** zadejte úplný název, jako například **Britta Simon**.

    d. V **heslo** pole, zadejte heslo uživatele.

    e. V **Potvrdit heslo** pole, zadejte heslo znovu.

    f. Nastavte **je aktivní** přepnutím **aktivní**.

5. Vyberte **uložit.**

    ![Přepnout pouze povolení přihlášení SSO](./media/absorblms-tutorial/save.png)

    > [!NOTE]
    > Ve výchozím nastavení zřizování uživatelů není povolena v jednotného přihlašování. Pokud zákazník chce tuto funkci povolit, mají nastavení až, jak je uvedeno v [to](https://support.absorblms.com/hc/en-us/articles/360014083294-Incoming-SAML-2-0-SSO-Account-Provisioning) dokumentaci. Také Upozorňujeme, že uživatel Provisioing je dostupný jenom u **vyrovnat se s 5 – nové prostředí Learner** s ACS URL -`https://company.myabsorb.com/api/rest/v2/authentication/saml`

### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na dlaždici vyrovnat se s LMS na přístupovém panelu, vám by měl být automaticky přihlášeni vyrovnat se s LMS, u kterého nastavíte jednotné přihlašování. Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další prostředky

- [ Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)