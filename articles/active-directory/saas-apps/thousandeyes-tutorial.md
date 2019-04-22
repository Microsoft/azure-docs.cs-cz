---
title: 'Kurz: Integrace Azure Active Directory s ThousandEyes | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a ThousandEyes.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 790e3f1e-1591-4dd6-87df-590b7bf8b4ba
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/27/2019
ms.author: jeedes
ms.openlocfilehash: 587d6f8ecdc447672c14ce400ebc64609e4d472d
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/18/2019
ms.locfileid: "59273902"
---
# <a name="tutorial-azure-active-directory-integration-with-thousandeyes"></a>Kurz: Integrace Azure Active Directory s ThousandEyes

V tomto kurzu se dozvíte, jak integrovat ThousandEyes s Azure Active Directory (Azure AD).
ThousandEyes integraci se službou Azure AD poskytuje následující výhody:

* Můžete řídit ve službě Azure AD, který má přístup k ThousandEyes.
* Můžete povolit uživatelům být automaticky přihlášeni k ThousandEyes (Single Sign-On) s jejich účty Azure AD.
* Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s ThousandEyes, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční zkušební verze [zde](https://azure.microsoft.com/pricing/free-trial/)
* ThousandEyes jednotného přihlašování povolená předplatného

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu konfigurace a testování v testovacím prostředí Azure AD jednotného přihlašování.

* Podporuje ThousandEyes **SP** jednotné přihlašování zahájené pomocí

* Podporuje ThousandEyes [ **automatizovaná** zřizování uživatelů](https://docs.microsoft.com/azure/active-directory/saas-apps/thousandeyes-provisioning-tutorial)

## <a name="adding-thousandeyes-from-the-gallery"></a>Přidání ThousandEyes z Galerie

Konfigurace integrace ThousandEyes do služby Azure AD, budete muset přidat ThousandEyes z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat ThousandEyes z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte do **podnikové aplikace** a pak vyberte **všechny aplikace** možnost.

    ![V okně podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **ThousandEyes**vyberte **ThousandEyes** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

     ![ThousandEyes v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části je konfigurace a testování Azure AD jednotné přihlašování pomocí ThousandEyes podle testovacího uživatele volá **Britta Simon**.
Pro jednotné přihlašování pro práci je potřeba navázat vztah odkazu mezi uživatele služby Azure AD a související uživatelské v ThousandEyes.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s ThousandEyes, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Konfigurace ThousandEyes Single Sign-On](#configure-thousandeyes-single-sign-on)**  – ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
3. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
4. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
5. **[Vytvořit testovacího uživatele ThousandEyes](#create-thousandeyes-test-user)**  – Pokud chcete mít protějšek Britta Simon ThousandEyes, který je propojený s Azure AD reprezentace uživatele.
6. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure portal.

Ke konfiguraci Azure AD jednotné přihlašování s ThousandEyes, proveďte následující kroky:

1. V [webu Azure portal](https://portal.azure.com/)na **ThousandEyes** integrace stránce aplikace vyberte **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz](common/select-sso.png)

2. Na **vybrat jedinou metodu přihlašování** dialogového okna, vyberte **SAML/WS-Fed** chcete povolit jednotné přihlašování.

    ![Jednotné přihlašování režim výběru](common/select-saml-option.png)

3. Na **nastavte si jednotné přihlašování pomocí SAML** klikněte na **upravit** ikony otevřete **základní konfiguraci SAML** dialogového okna.

    ![Upravit konfiguraci základní SAML](common/edit-urls.png)

4. Na **základní konfiguraci SAML** části, proveďte následující kroky:

    ![ThousandEyes domény a adresy URL jednotného přihlašování – informace](common/sp-signonurl.png)

    V **přihlašovací adresa URL** textové pole, zadejte adresu URL:  `https://app.thousandeyes.com/login/sso`

5. Na **nastavte si jednotné přihlašování pomocí SAML** stránku, **podpisový certifikát SAML** klikněte na tlačítko **Stáhnout** ke stažení **certifikát (Base64)** z se zadanými možnostmi podle vašich požadavků a uložit je ve vašem počítači.

    ![Odkaz ke stažení certifikátu](common/certificatebase64.png)

6. Na **nastavení ThousandEyes** tématu, zkopírujte příslušné adresy URL podle vašich požadavků.

    ![Zkopírování adresy URL konfigurace](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor Azure AD

    c. Adresa URL – odhlášení

### <a name="configure-thousandeyes-single-sign-on"></a>Konfigurace ThousandEyes jednotné přihlašování

1. V okně jiné webové prohlížeče, přihlaste se k vaší **ThousandEyes** společnosti serveru jako správce.

2. V nabídce v horní části klikněte na tlačítko **nastavení**.

    ![Nastavení](./media/thousandeyes-tutorial/ic790066.png "nastavení")

3. Klikněte na tlačítko **účtu**

    ![Účet](./media/thousandeyes-tutorial/ic790067.png "účtu")

4. Klikněte na tlačítko **zabezpečení a ověřování** kartu.

    ![Zabezpečení a ověřování](./media/thousandeyes-tutorial/ic790068.png "zabezpečení a ověřování")

5. V **nastavení jednotného přihlašování** části, proveďte následující kroky:

    ![Nastavení jednotného přihlašování](./media/thousandeyes-tutorial/ic790069.png "nastavit jednotné přihlašování")

    a. Vyberte **povolit jednotné přihlašování**.

    b. V **adresu URL přihlašovací stránky** vložit do textového pole **přihlašovací adresa URL**, který jste zkopírovali z portálu Azure portal.

    c. V **adresy URL odhlašovací stránky** vložit do textového pole **odhlašovací adresa URL**, který jste zkopírovali z portálu Azure portal.

    d. **Vystavitel zprostředkovatele identity** vložit do textového pole **Azure AD identifikátor**, který jste zkopírovali z portálu Azure portal.

    e. V **ověřovací certifikát**, klikněte na tlačítko **zvolte soubor**a pak nahrajte certifikát, který jste si stáhli z webu Azure portal.

    f. Klikněte na **Uložit**.

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

V této části je povolit Britta Simon k udělení přístupu k ThousandEyes použití Azure jednotného přihlašování.

1. Na webu Azure Portal, vyberte **podnikové aplikace**vyberte **všechny aplikace**a pak vyberte **ThousandEyes**.

    ![Okno aplikace organizace](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **ThousandEyes**.

    ![Odkaz ThousandEyes v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **přidat uživatele** tlačítko a pak vyberte **uživatelů a skupin** v **přidat přiřazení** dialogového okna.

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V **uživatelů a skupin** dialogové okno Vybrat **Britta Simon** v seznamu uživatelů, klikněte **vyberte** tlačítko v dolní části obrazovky.

6. Pokud očekáváte libovolnou hodnotu role v kontrolní výraz SAML a potom v **vybrat roli** dialogové okno vybrat vhodnou roli pro uživatele ze seznamu, klikněte **vyberte** tlačítko v dolní části obrazovky.

7. V **přidat přiřazení** dialogové okno kliknutím **přiřadit** tlačítko.

### <a name="create-thousandeyes-test-user"></a>Vytvoření ThousandEyes testovacího uživatele

Cílem této části je vytvořte uživatele Britta Simon v ThousandEyes. ThousandEyes podporuje automatické zřizování uživatelů, což je ve výchozím nastavení povolená. Další podrobnosti můžete najít [tady](thousandeyes-provisioning-tutorial.md) o tom, jak nakonfigurovat automatické zřizování uživatelů.

**Pokud je potřeba ručně vytvořit uživatele, proveďte následující kroky:**

1. Přihlaste se k webu společnosti ThousandEyes jako správce.

2. Klikněte na tlačítko **nastavení**.

    ![Nastavení](./media/thousandeyes-tutorial/IC790066.png "nastavení")

3. Klikněte na tlačítko **účet**.

    ![Účet](./media/thousandeyes-tutorial/IC790067.png "účtu")

4. Klikněte na tlačítko **účty a uživatelé** kartu.

    ![Účty a uživatelé](./media/thousandeyes-tutorial/IC790073.png "účty a uživatelé")

5. V **přidat uživatele a účty** části, proveďte následující kroky:

    ![Přidejte uživatelské účty](./media/thousandeyes-tutorial/IC790074.png "přidejte uživatelské účty")

    a. V **název** textového pole, zadejte jméno uživatele, jako jsou **Britta Simon**.

    b. V **e-mailu** , jako je textové pole, typ e-mailu uživatele brittasimon@contoso.com.

    b. Klikněte na tlačítko **přidání nového uživatele do účtu**.

    > [!NOTE]
    > Držitel účtu Azure Active Directory se zobrazí e-mailu včetně odkazu na potvrzení a aktivovat účet.

> [!NOTE]
> Můžete použít jakékoli jiné ThousandEyes uživatelského účtu nástrojů pro vytváření nebo rozhraní API poskytovaných ThousandEyes ke zřízení služby Azure Active Directory uživatelské účty.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování 

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na dlaždici ThousandEyes na přístupovém panelu, můžete by měl být automaticky přihlášeni k ThousandEyes, u kterého nastavíte jednotné přihlašování. Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další prostředky

- [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Konfigurace zřizování uživatelů](https://docs.microsoft.com/azure/active-directory/saas-apps/thousandeyes-provisioning-tutorial)
