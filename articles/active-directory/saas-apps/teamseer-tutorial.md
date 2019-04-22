---
title: 'Kurz: Integrace Azure Active Directory s TeamSeer | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a TeamSeer.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 6ec4806f-fe0f-4ed7-8cfa-32d1c840433f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/07/2019
ms.author: jeedes
ms.openlocfilehash: ef11011ec37f72003dd6c238ce748d417bfcd505
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/18/2019
ms.locfileid: "59256613"
---
# <a name="tutorial-azure-active-directory-integration-with-teamseer"></a>Kurz: Integrace Azure Active Directory s TeamSeer

V tomto kurzu se dozvíte, jak integrovat TeamSeer s Azure Active Directory (Azure AD).
TeamSeer integraci se službou Azure AD poskytuje následující výhody:

* Můžete řídit ve službě Azure AD, který má přístup k TeamSeer.
* Můžete povolit uživatelům být automaticky přihlášeni k TeamSeer (Single Sign-On) s jejich účty Azure AD.
* Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s TeamSeer, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat [bezplatný účet](https://azure.microsoft.com/free/)
* TeamSeer jednotného přihlašování povolená předplatného

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu konfigurace a testování v testovacím prostředí Azure AD jednotného přihlašování.

* Podporuje TeamSeer **SP** jednotné přihlašování zahájené pomocí

## <a name="adding-teamseer-from-the-gallery"></a>Přidání TeamSeer z Galerie

Konfigurace integrace TeamSeer do služby Azure AD, budete muset přidat TeamSeer z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat TeamSeer z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte do **podnikové aplikace** a pak vyberte **všechny aplikace** možnost.

    ![V okně podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **TeamSeer**vyberte **TeamSeer** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

     ![TeamSeer v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části je konfigurace a testování Azure AD jednotné přihlašování pomocí TeamSeer podle testovacího uživatele volá **Britta Simon**.
Pro jednotné přihlašování pro práci je potřeba navázat vztah odkazu mezi uživatele služby Azure AD a související uživatelské v TeamSeer.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s TeamSeer, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Konfigurace TeamSeer Single Sign-On](#configure-teamseer-single-sign-on)**  – ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
3. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
4. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
5. **[Vytvořit testovacího uživatele TeamSeer](#create-teamseer-test-user)**  – Pokud chcete mít protějšek Britta Simon TeamSeer, který je propojený s Azure AD reprezentace uživatele.
6. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure portal.

Ke konfiguraci Azure AD jednotné přihlašování s TeamSeer, proveďte následující kroky:

1. V [webu Azure portal](https://portal.azure.com/)na **TeamSeer** integrace stránce aplikace vyberte **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz](common/select-sso.png)

2. Na **vybrat jedinou metodu přihlašování** dialogového okna, vyberte **SAML/WS-Fed** chcete povolit jednotné přihlašování.

    ![Jednotné přihlašování režim výběru](common/select-saml-option.png)

3. Na **nastavte si jednotné přihlašování pomocí SAML** klikněte na **upravit** ikony otevřete **základní konfiguraci SAML** dialogového okna.

    ![Upravit konfiguraci základní SAML](common/edit-urls.png)

4. Na **základní konfiguraci SAML** části, proveďte následující kroky:

    ![TeamSeer domény a adresy URL jednotného přihlašování – informace](common/sp-signonurl.png)

    V **přihlašovací adresa URL** textové pole, zadejte adresu URL, pomocí následujícího vzorce:  `https://www.teamseer.com/<companyid>`

    > [!NOTE]
    > Hodnota není skutečný. Aktualizujte příslušnou hodnotu skutečné přihlašovací adresa URL. Kontakt [tým podpory TeamSeer klienta](https://pages.theaccessgroup.com/solutions_business-suite_absence-management_contact.html) má být získána hodnota. Můžete také odkazovat na tyto vzory se dají ukazuje **základní konfiguraci SAML** části webu Azure Portal.

5. Na **nastavte si jednotné přihlašování pomocí SAML** stránku, **podpisový certifikát SAML** klikněte na tlačítko **Stáhnout** ke stažení **certifikát (Base64)** z se zadanými možnostmi podle vašich požadavků a uložit je ve vašem počítači.

    ![Odkaz ke stažení certifikátu](common/certificatebase64.png)

6. Na **nastavení TeamSeer** tématu, zkopírujte příslušné adresy URL podle vašich požadavků.

    ![Zkopírování adresy URL konfigurace](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor Azure AD

    c. Adresa URL – odhlášení

### <a name="configure-teamseer-single-sign-on"></a>Konfigurace TeamSeer jednotné přihlašování

1. V okně jiné webové prohlížeče Přihlaste se k webu společnosti TeamSeer jako správce.

1. Přejděte na **HR správce**.

    ![Správce HR](./media/teamseer-tutorial/ic789634.png "HR správce")

1. Klikněte na tlačítko **nastavení**.

    ![Instalační program](./media/teamseer-tutorial/ic789635.png "instalační program")

1. Klikněte na tlačítko **nastavit SAML zprostředkovatele podrobnosti**.

    ![Nastavení SAML](./media/teamseer-tutorial/ic789636.png "nastavení SAML")

1. V části Podrobnosti SAML zprostředkovatele proveďte následující kroky:

    ![Nastavení SAML](./media/teamseer-tutorial/ic789637.png "nastavení SAML")

    a. V **URL** vložit do textového pole **přihlašovací adresa URL** hodnotu, kterou jste zkopírovali z portálu Azure portal.

    b. V poznámkovém bloku otevřete certifikát kódováním base-64, obsah ho zkopírujte do schránky a vložte ho do **veřejný certifikát IdP** textového pole.

1. K dokončení konfigurace poskytovatele SAML, proveďte následující kroky:

    ![Nastavení SAML](./media/teamseer-tutorial/ic789638.png "nastavení SAML")

    a. V **testovací e-mailové adresy**, zadejte e-mailovou adresu testovacího uživatele.
  
    b. V **vystavitele** textového pole zadejte adresu URL vystavitele poskytovatele služeb.
  
    c. Klikněte na **Uložit**.

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

V této části je povolit Britta Simon k udělení přístupu k TeamSeer použití Azure jednotného přihlašování.

1. Na webu Azure Portal, vyberte **podnikové aplikace**vyberte **všechny aplikace**a pak vyberte **TeamSeer**.

    ![Okno aplikace organizace](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **TeamSeer**.

    ![Odkaz TeamSeer v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **přidat uživatele** tlačítko a pak vyberte **uživatelů a skupin** v **přidat přiřazení** dialogového okna.

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V **uživatelů a skupin** dialogové okno Vybrat **Britta Simon** v seznamu uživatelů, klikněte **vyberte** tlačítko v dolní části obrazovky.

6. Pokud očekáváte libovolnou hodnotu role v kontrolní výraz SAML a potom v **vybrat roli** dialogové okno vybrat vhodnou roli pro uživatele ze seznamu, klikněte **vyberte** tlačítko v dolní části obrazovky.

7. V **přidat přiřazení** dialogové okno kliknutím **přiřadit** tlačítko.

### <a name="create-teamseer-test-user"></a>Vytvoření TeamSeer testovacího uživatele

Povolení služby Azure AD uživatelům umožní přihlásit k TeamSeer, musí být zřízená v k ShiftPlanning. V případě TeamSeer zřizování se ruční úlohy.

**K poskytnutí uživatelského účtu, postupujte následovně:**

1. Přihlaste se k vaší **TeamSeer** společnosti serveru jako správce.

1. Přejděte na **HR správce \> uživatelé** a potom klikněte na tlačítko **spustit Průvodce nového uživatele**.

    ![Správce HR](./media/teamseer-tutorial/ic789640.png "HR správce")

1. V **podrobnosti o uživateli** části, proveďte následující kroky:

    ![Podrobnosti o uživateli](./media/teamseer-tutorial/ic789641.png "podrobnosti o uživateli")

    a. Typ **křestní jméno**, **příjmení**, **uživatelské jméno (e-mailovou adresu)** platný Azure AD účet, kterým chcete přidělit k související textová pole v.
  
    b. Klikněte na **Další**.

1. Použijte na obrazovce pokyny pro přidání nového uživatele a klikněte na tlačítko **Dokončit**.

> [!NOTE]
> Můžete použít jakékoli jiné TeamSeer uživatelského účtu nástrojů pro vytváření nebo rozhraní API poskytovaných TeamSeer zřízení uživatelských účtů služby Azure AD.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na dlaždici TeamSeer na přístupovém panelu, můžete by měl být automaticky přihlášeni k TeamSeer, u kterého nastavíte jednotné přihlašování. Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další prostředky

- [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
