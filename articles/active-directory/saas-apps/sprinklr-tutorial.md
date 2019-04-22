---
title: 'Kurz: Integrace Azure Active Directory s Sprinklr | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Sprinklr.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: b33938a1-25a5-484c-8e75-7dc6de2d534d
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/07/2019
ms.author: jeedes
ms.openlocfilehash: 1c3b95686b8c91552615a9014102fd6a14f8c385
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/18/2019
ms.locfileid: "59277064"
---
# <a name="tutorial-azure-active-directory-integration-with-sprinklr"></a>Kurz: Integrace Azure Active Directory s Sprinklr

V tomto kurzu se dozvíte, jak integrovat Sprinklr s Azure Active Directory (Azure AD).
Sprinklr integraci se službou Azure AD poskytuje následující výhody:

* Můžete řídit ve službě Azure AD, který má přístup k Sprinklr.
* Můžete povolit uživatelům být automaticky přihlášeni k Sprinklr (Single Sign-On) s jejich účty Azure AD.
* Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s Sprinklr, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční zkušební verze [zde](https://azure.microsoft.com/pricing/free-trial/)
* Sprinklr jednotného přihlašování povolená předplatného

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu konfigurace a testování v testovacím prostředí Azure AD jednotného přihlašování.

* Podporuje Sprinklr **SP** jednotné přihlašování zahájené pomocí

## <a name="adding-sprinklr-from-the-gallery"></a>Přidání Sprinklr z Galerie

Konfigurace integrace Sprinklr do služby Azure AD, budete muset přidat Sprinklr z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat Sprinklr z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte do **podnikové aplikace** a pak vyberte **všechny aplikace** možnost.

    ![V okně podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **Sprinklr**vyberte **Sprinklr** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

     ![Sprinklr v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části je konfigurace a testování Azure AD jednotné přihlašování pomocí Sprinklr podle testovacího uživatele volá **Britta Simon**.
Pro jednotné přihlašování pro práci je potřeba navázat vztah odkazu mezi uživatele služby Azure AD a související uživatelské v Sprinklr.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Sprinklr, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Konfigurace Sprinklr Single Sign-On](#configure-sprinklr-single-sign-on)**  – ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
3. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
4. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
5. **[Vytvořit testovacího uživatele Sprinklr](#create-sprinklr-test-user)**  – Pokud chcete mít protějšek Britta Simon Sprinklr, který je propojený s Azure AD reprezentace uživatele.
6. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure portal.

Ke konfiguraci Azure AD jednotné přihlašování s Sprinklr, proveďte následující kroky:

1. V [webu Azure portal](https://portal.azure.com/)na **Sprinklr** integrace stránce aplikace vyberte **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz](common/select-sso.png)

2. Na **vybrat jedinou metodu přihlašování** dialogového okna, vyberte **SAML/WS-Fed** chcete povolit jednotné přihlašování.

    ![Jednotné přihlašování režim výběru](common/select-saml-option.png)

3. Na **nastavte si jednotné přihlašování pomocí SAML** klikněte na **upravit** ikony otevřete **základní konfiguraci SAML** dialogového okna.

    ![Upravit konfiguraci základní SAML](common/edit-urls.png)

4. Na **základní konfiguraci SAML** části, proveďte následující kroky:

    ![Sprinklr domény a adresy URL jednotného přihlašování – informace](common/sp-identifier.png)

    a. V **přihlašovací adresa URL** textové pole, zadejte adresu URL, pomocí následujícího vzorce: `https://<subdomain>.sprinklr.com`

    b. V **identifikátor (Entity ID)** textové pole, zadejte adresu URL, pomocí následujícího vzorce: `https://<subdomain>.sprinklr.com`

    > [!NOTE]
    > Tyto hodnoty nejsou skutečný. Aktualizujte tyto hodnoty skutečné přihlašovací adresu URL a identifikátor. Kontakt [tým podpory Sprinklr klienta](https://www.sprinklr.com/contact-us/) k získání těchto hodnot. Můžete také odkazovat na tyto vzory se dají ukazuje **základní konfiguraci SAML** části webu Azure Portal.

5. Na **nastavte si jednotné přihlašování pomocí SAML** stránku, **podpisový certifikát SAML** klikněte na tlačítko **Stáhnout** ke stažení **certifikát (Base64)** z se zadanými možnostmi podle vašich požadavků a uložit je ve vašem počítači.

    ![Odkaz ke stažení certifikátu](common/certificatebase64.png)

6. Na **nastavení Sprinklr** tématu, zkopírujte příslušné adresy URL podle vašich požadavků.

    ![Zkopírování adresy URL konfigurace](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor Azure AD

    c. Adresa URL – odhlášení

### <a name="configure-sprinklr-single-sign-on"></a>Konfigurace Sprinklr jednotné přihlašování

1. V okně jiné webové prohlížeče Přihlaste se k webu společnosti Sprinklr jako správce.

1. Přejděte na **správu \> nastavení**.

    ![Správa](./media/sprinklr-tutorial/ic782907.png "správy")

1. Přejděte na **správa partnerů \> jednotného přihlašování** na v levém podokně.

    ![Správa partnera](./media/sprinklr-tutorial/ic782908.png "správa partnerů")

1. Klikněte na tlačítko **+ doplňky jednotného přihlašování**.

    ![Jednotné přihlašování](./media/sprinklr-tutorial/ic782909.png "jednotného přihlašování")

1. Na **jednotného přihlašování** stránce, proveďte následující kroky:

    ![Jednotné přihlašování](./media/sprinklr-tutorial/ic782910.png "jednotného přihlašování")

    a. V **název** textového pole zadejte název pro vaši konfiguraci (například: *WAADSSOTest*).

    b. Vyberte **povolené**.

    c. Vyberte **používat nový certifikát jednotného přihlašování**.

    d. V poznámkovém bloku otevřete certifikát kódováním base-64, zkopírujte obsah ho do schránky a a vložte ho do **certifikát poskytovatele Identity** textového pole.

    e. Vložit **Azure AD identifikátor** hodnotu, která jste zkopírovali z portálu Azure Portal do **Entity Id** textového pole.

    f. Vložit **přihlašovací adresa URL** hodnotu, která jste zkopírovali z portálu Azure Portal do **přihlašovací adresa URL zprostředkovatele Identity** textového pole.

    g. Vložit **odhlašovací adresa URL** hodnotu, která jste zkopírovali z portálu Azure Portal do **odhlašovací adresa URL zprostředkovatele Identity** textového pole.

    h. Jako **typ ID uživatele SAML**vyberte **kontrolní výraz obsahuje uživatelské jméno sprinklr.com**.

    i. Jako **umístění ID uživatele SAML**vyberte **ID uživatele není v elementu identifikátor názvu subjektu příkazu**.

    j. Klikněte na **Uložit**.

    ![SAML](./media/sprinklr-tutorial/ic782911.png "SAML")

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

V této části je povolit Britta Simon k udělení přístupu k Sprinklr použití Azure jednotného přihlašování.

1. Na webu Azure Portal, vyberte **podnikové aplikace**vyberte **všechny aplikace**a pak vyberte **Sprinklr**.

    ![Okno aplikace organizace](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **Sprinklr**.

    ![Odkaz Sprinklr v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **přidat uživatele** tlačítko a pak vyberte **uživatelů a skupin** v **přidat přiřazení** dialogového okna.

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V **uživatelů a skupin** dialogové okno Vybrat **Britta Simon** v seznamu uživatelů, klikněte **vyberte** tlačítko v dolní části obrazovky.

6. Pokud očekáváte libovolnou hodnotu role v kontrolní výraz SAML a potom v **vybrat roli** dialogové okno vybrat vhodnou roli pro uživatele ze seznamu, klikněte **vyberte** tlačítko v dolní části obrazovky.

7. V **přidat přiřazení** dialogové okno kliknutím **přiřadit** tlačítko.

### <a name="create-sprinklr-test-user"></a>Vytvoření Sprinklr testovacího uživatele

1. Přihlaste se na web společnosti Sprinklr jako správce.

1. Přejděte na **správu \> nastavení**.

    ![Správa](./media/sprinklr-tutorial/ic782907.png "správy")

1. Přejděte na **spravovat klienta \> uživatelé** v levém podokně.

    ![Nastavení](./media/sprinklr-tutorial/ic782914.png "nastavení")

1. Klikněte na tlačítko **přidat uživatele**.

    ![Nastavení](./media/sprinklr-tutorial/ic782915.png "nastavení")

1. Na **upravit uživatele** dialogového okna, proveďte následující kroky:

    ![Upravit uživatele](./media/sprinklr-tutorial/ic782916.png "úpravy uživatele")

    a. V **e-mailu**, **křestní jméno** a **příjmení** textová pole, zadejte informace o uživatelský účet služby Azure AD, které chcete zřídit.

    b. Vyberte **heslo zakázáno**.

    c. Vyberte **jazyka**.

    d. Vyberte **typ uživatele**.

    e. Klikněte na tlačítko **aktualizace**.

    > [!IMPORTANT]
    > **Heslo zakázáno** musí být vybrána a povolit tak uživateli při přihlašování přes zprostředkovatele Identity. 

1. Přejděte na **Role**a pak proveďte následující kroky:

    ![Partner role](./media/sprinklr-tutorial/ic782917.png "Partner role")

    a. Z **globální** seznamu vyberte **ALL_Permissions**.  

    b. Klikněte na tlačítko **aktualizace**.

> [!NOTE]
> Můžete použít jakékoli jiné Sprinklr uživatelského účtu nástrojů pro vytváření nebo rozhraní API poskytovaných Sprinklr zřízení uživatelských účtů služby Azure AD.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na dlaždici Sprinklr na přístupovém panelu, můžete by měl být automaticky přihlášeni k Sprinklr, u kterého nastavíte jednotné přihlašování. Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další prostředky

- [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
