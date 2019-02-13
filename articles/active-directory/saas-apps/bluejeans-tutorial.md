---
title: 'Kurz: Integrace Azure Active Directory s BlueJeans | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a BlueJeans.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: dfc634fd-1b55-4ba8-94a8-b8288429b6a9
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/31/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: b1e4b971c92da91c6a62a5d8b38292a1d5679deb
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2019
ms.locfileid: "56167214"
---
# <a name="tutorial-azure-active-directory-integration-with-bluejeans"></a>Kurz: Integrace Azure Active Directory s BlueJeans

V tomto kurzu se dozvíte, jak integrovat BlueJeans s Azure Active Directory (Azure AD).
BlueJeans integraci se službou Azure AD poskytuje následující výhody:

* Můžete řídit ve službě Azure AD, který má přístup k BlueJeans.
* Můžete povolit uživatelům být automaticky přihlášeni k BlueJeans (Single Sign-On) s jejich účty Azure AD.
* Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s BlueJeans, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční zkušební verze [zde](https://azure.microsoft.com/pricing/free-trial/)
* BlueJeans jednotného přihlašování povolená předplatného

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu konfigurace a testování v testovacím prostředí Azure AD jednotného přihlašování.

* Podporuje blueJeans **SP** jednotné přihlašování zahájené pomocí

* Podporuje blueJeans [ **automatizovaná** zřizování uživatelů](bluejeans-provisioning-tutorial.md)

## <a name="adding-bluejeans-from-the-gallery"></a>Přidání BlueJeans z Galerie

Konfigurace integrace BlueJeans do služby Azure AD, budete muset přidat BlueJeans z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat BlueJeans z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte do **podnikové aplikace** a pak vyberte **všechny aplikace** možnost.

    ![V okně podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **BlueJeans**vyberte **BlueJeans** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

     ![BlueJeans v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části je konfigurace a testování Azure AD jednotné přihlašování pomocí BlueJeans podle testovacího uživatele volá **Britta Simon**.
Pro jednotné přihlašování pro práci je potřeba navázat vztah odkazu mezi uživatele služby Azure AD a související uživatelské v BlueJeans.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s BlueJeans, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Konfigurace BlueJeans Single Sign-On](#configure-bluejeans-single-sign-on)**  – ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
3. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
4. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
5. **[Vytvořit testovacího uživatele BlueJeans](#create-bluejeans-test-user)**  – Pokud chcete mít protějšek Britta Simon v BlueJeans, který je propojený s Azure AD reprezentace uživatele.
6. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure portal.

Ke konfiguraci Azure AD jednotné přihlašování s BlueJeans, proveďte následující kroky:

1. V [webu Azure portal](https://portal.azure.com/)na **BlueJeans** integrace stránce aplikace vyberte **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz](common/select-sso.png)

2. Na **vybrat jedinou metodu přihlašování** dialogového okna, vyberte **SAML/WS-Fed** chcete povolit jednotné přihlašování.

    ![Jednotné přihlašování režim výběru](common/select-saml-option.png)

3. Na **nastavte si jednotné přihlašování pomocí SAML** klikněte na **upravit** ikony otevřete **základní konfiguraci SAML** dialogového okna.

    ![Upravit konfiguraci základní SAML](common/edit-urls.png)

4. Na **základní konfiguraci SAML** části, proveďte následující kroky:

    ![BlueJeans domény a adresy URL jednotného přihlašování – informace](common/sp-signonurl.png)

    V **přihlašovací adresa URL** textové pole, zadejte adresu URL, pomocí následujícího vzorce:  `https://<companyname>.BlueJeans.com`

    > [!NOTE]
    > Hodnota není skutečný. Aktualizujte příslušnou hodnotu skutečné přihlašovací adresa URL. Kontakt [tým podpory BlueJeans klienta](https://support.bluejeans.com/contact) má být získána hodnota. Můžete také odkazovat na tyto vzory se dají ukazuje **základní konfiguraci SAML** části webu Azure Portal.

4. Na **nastavte si jednotné přihlašování pomocí SAML** stránku, **podpisový certifikát SAML** klikněte na tlačítko **Stáhnout** ke stažení **certifikát (Base64)** z se zadanými možnostmi podle vašich požadavků a uložit je ve vašem počítači.

    ![Odkaz ke stažení certifikátu](common/certificatebase64.png)

6. Na **nastavení BlueJeans** tématu, zkopírujte příslušné adresy URL podle vašich požadavků.

    ![Zkopírování adresy URL konfigurace](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor služby Azure Ad

    c. Adresa URL – odhlášení

### <a name="configure-bluejeans-single-sign-on"></a>Konfigurace BlueJeans jednotného přihlašování

1. V okně jiné webové prohlížeče, přihlaste se k vaší **BlueJeans** společnosti serveru jako správce.

2. Přejděte na **správce \> nastavení skupiny \> zabezpečení**.

    ![Správce](./media/bluejeans-tutorial/IC785868.png "správce")

3. V **zabezpečení** části, proveďte následující kroky:

    ![Jednotné přihlašování SAML na](./media/bluejeans-tutorial/IC785869.png "SAML jednotného přihlašování")

    a. Vyberte **jednotného přihlašování SAML**.

    b. Vyberte **povolit automatické zřizování**.

4. Přesunout pomocí následujících kroků:

    ![Cesta k certifikátu](./media/bluejeans-tutorial/IC785870.png "cesta certifikátu")

    a. Klikněte na tlačítko **zvolit soubor**a nahrajte base-64 kódovaných certifikát, který jste si stáhli z portálu Azure portal.

    b. V **přihlašovací adresa URL** textového pole vložte hodnotu **přihlašovací adresa URL** zkopírovanou z webu Azure portal.

    c. V **heslo změnit adresu URL** textového pole vložte hodnotu **heslo změnit adresu URL** zkopírovanou z webu Azure portal.

    d. V **odhlašovací adresa URL** textového pole vložte hodnotu **odhlašovací adresa URL** zkopírovanou z webu Azure portal.

5. Přesunout pomocí následujících kroků:

    ![Uložit změny](./media/bluejeans-tutorial/IC785874.png "uložit změny")

    a. V **id uživatele** textové pole, typ `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`.

    b. V **e-mailu** textové pole, typ `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`.

    c. Klikněte na tlačítko **ULOŽTE změny**.

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

V této části je povolit Britta Simon k udělení přístupu k BlueJeans použití Azure jednotného přihlašování.

1. Na webu Azure Portal, vyberte **podnikové aplikace**vyberte **všechny aplikace**a pak vyberte **BlueJeans**.

    ![Okno aplikace organizace](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **BlueJeans**.

    ![Odkaz BlueJeans v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **přidat uživatele** tlačítko a pak vyberte **uživatelů a skupin** v **přidat přiřazení** dialogového okna.

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V **uživatelů a skupin** dialogové okno Vybrat **Britta Simon** v seznamu uživatelů, klikněte **vyberte** tlačítko v dolní části obrazovky.

6. Pokud očekáváte libovolnou hodnotu role v kontrolní výraz SAML a potom v **vybrat roli** dialogové okno vybrat vhodnou roli pro uživatele ze seznamu, klikněte **vyberte** tlačítko v dolní části obrazovky.

7. V **přidat přiřazení** dialogové okno kliknutím **přiřadit** tlačítko.

### <a name="create-bluejeans-test-user"></a>Create BlueJeans test user

Cílem této části je vytvořte uživatele Britta Simon v BlueJeans. BlueJeans podporuje automatické zřizování uživatelů, což je ve výchozím nastavení povolená. Další podrobnosti můžete najít [tady](bluejeans-provisioning-tutorial.md) o tom, jak nakonfigurovat automatické zřizování uživatelů.

**Pokud je potřeba ručně vytvořit uživatele, proveďte následující kroky:**

1. Přihlaste se k vaší **BlueJeans** společnosti serveru jako správce.

2. Přejděte na **správce \> MANAGE USERS \> přidat uživatele**.

    ![Správce](./media/bluejeans-tutorial/IC785877.png "správce")

    >[!IMPORTANT]
    >**Přidat uživatele** karta je dostupná pouze if, v **POLOŽENÍM kartu**, **povolit automatické zřizování** není zaškrtnuta. 

3. V **přidat uživatele** části, proveďte následující kroky:

    ![Přidání uživatele](./media/bluejeans-tutorial/IC785886.png "přidat uživatele")

    a. V **křestní jméno** textové pole, zadejte jméno uživatele, jako je **Britta**.

    b. V **příjmení** textové pole, zadejte příjmení uživatele, jako je **simon**.

    c. V **vyberte uživatelské jméno BlueJeans** textové pole, zadejte uživatelské jméno uživatele, jako je **Brittasimon**

    d. V **vytvořit heslo** textové pole, zadejte své heslo.

    e. V **společnosti** textové pole, zadejte vaší společnosti.

    f. V **e-mailovou adresu** textové pole, zadejte e-mailu uživatele, jako je **brittasimon@contoso.com**.

    g. V **vytvořit i d BlueJeans schůzky** textové pole, zadejte své ID schůzky.

    h. V **vybrat heslo moderátor** textové pole, zadejte své heslo.

    i. Klikněte na tlačítko **pokračovat**.

    ![Uživatel Addd](./media/bluejeans-tutorial/IC785887.png "Addd uživatele")

    J. Klikněte na tlačítko **přidat uživatele**.

> [!NOTE]
> Můžete použít jakékoli jiné BlueJeans uživatelského účtu nástrojů pro vytváření nebo rozhraní API poskytovaných BlueJeans zřízení uživatelských účtů služby Azure AD.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na dlaždici BlueJeans na přístupovém panelu, můžete by měl být automaticky přihlášeni k BlueJeans, pro které můžete nastavit jednotné přihlašování. Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další prostředky

- [ Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

