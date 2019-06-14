---
title: 'Kurz: Integrace Azure Active Directory s Sugar CRM | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Sugar CRM.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 3331b9fc-ebc0-4a3a-9f7b-bf20ee35d180
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/22/2019
ms.author: jeedes
ms.openlocfilehash: 5c46b096b1182b3f2a998f992f72e6127dfe0888
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "67089560"
---
# <a name="tutorial-azure-active-directory-integration-with-sugar-crm"></a>Kurz: Integrace Azure Active Directory s Sugar CRM

V tomto kurzu se dozvíte, jak integrovat Sugar CRM s Azure Active Directory (Azure AD).
Integrace s Azure AD Sugar CRM poskytuje následující výhody:

* Můžete řídit ve službě Azure AD, který má přístup k Sugar CRM.
* Uživatelům se automaticky přihlášeni k Sugar CRM (Single Sign-On) můžete povolit pomocí jejich účtů služby Azure AD.
* Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s Sugar CRM, budete potřebovat následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat [bezplatný účet](https://azure.microsoft.com/free/)
* Sugar CRM jednotného přihlašování povolená předplatného

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu konfigurace a testování v testovacím prostředí Azure AD jednotného přihlašování.

* Cukr CRM podporuje **SP** jednotné přihlašování zahájené pomocí

## <a name="adding-sugar-crm-from-the-gallery"></a>Přidání Sugar CRM z Galerie

Ke konfiguraci integrace Sugar CRM do služby Azure AD, budete muset přidat Sugar CRM na váš seznam spravovaných aplikací SaaS z galerie.

**Sugar CRM přidat z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)** , v levém navigačním panelu klikněte na **Azure Active Directory** ikonu.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte do **podnikové aplikace** a pak vyberte **všechny aplikace** možnost.

    ![V okně podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **Sugar CRM**vyberte **Sugar CRM** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

     ![Sugar CRM v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části je konfigurace a testování Azure AD jednotné přihlašování pomocí Sugar CRM na základě testovací uživatele volá **Britta Simon**.
Pro jednotné přihlašování pro práci je potřeba navázat vztah odkazu mezi uživatele služby Azure AD a související uživatelské Sugar CRM.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Sugar CRM, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Konfigurace Sugar CRM Single Sign-On](#configure-sugar-crm-single-sign-on)**  – ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
3. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
4. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
5. **[Vytvořit testovacího uživatele Sugar CRM](#create-sugar-crm-test-user)**  – Pokud chcete mít protějšek Britta Simon Sugar CRM, který je propojený s Azure AD reprezentace uživatele.
6. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure portal.

Ke konfiguraci Azure AD jednotné přihlašování s Sugar CRM, proveďte následující kroky:

1. V [webu Azure portal](https://portal.azure.com/)na **Sugar CRM** integrace stránce aplikace vyberte **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz](common/select-sso.png)

2. Na **vybrat jedinou metodu přihlašování** dialogového okna, vyberte **SAML/WS-Fed** chcete povolit jednotné přihlašování.

    ![Jednotné přihlašování režim výběru](common/select-saml-option.png)

3. Na **nastavte si jednotné přihlašování pomocí SAML** klikněte na **upravit** ikony otevřete **základní konfiguraci SAML** dialogového okna.

    ![Upravit konfiguraci základní SAML](common/edit-urls.png)

4. Na **základní konfiguraci SAML** části, proveďte následující kroky:

    ![Sugar CRM domény a adresy URL jednotného přihlašování – informace](common/sp-signonurl.png)

    V **přihlašovací adresa URL** textové pole, zadejte adresu URL, pomocí následujícího vzorce:
    
    | |
    |--|
    | `https://<companyname>.sugarondemand.com`|
    | `https://<companyname>.trial.sugarcrm`|

    > [!NOTE]
    > Hodnota není skutečný. Aktualizujte příslušnou hodnotu skutečné přihlašovací adresa URL. Kontakt [tým podpory klienta CRM Sugar](https://support.sugarcrm.com/) má být získána hodnota. Můžete také odkazovat na tyto vzory se dají ukazuje **základní konfiguraci SAML** části webu Azure Portal.

5. Na **nastavte si jednotné přihlašování pomocí SAML** stránku, **podpisový certifikát SAML** klikněte na tlačítko **Stáhnout** ke stažení **certifikát (Base64)** z se zadanými možnostmi podle vašich požadavků a uložit je ve vašem počítači.

    ![Odkaz ke stažení certifikátu](common/certificatebase64.png)

6. Na **nastavení Sugar CRM** tématu, zkopírujte příslušné adresy URL podle vašich požadavků.

    ![Zkopírování adresy URL konfigurace](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor Azure AD

    c. Adresa URL – odhlášení

### <a name="configure-sugar-crm-single-sign-on"></a>Konfigurace Sugar CRM jednotného přihlašování

1. V okně jiné webové prohlížeče Přihlaste se k webu společnosti Sugar CRM jako správce.

1. Přejděte na **správce**.

    ![Správce](./media/sugarcrm-tutorial/ic795888.png "správce")

1. V **správu** klikněte na tlačítko **Správa hesel**.

    ![Správa](./media/sugarcrm-tutorial/ic795889.png "správy")

1. Vyberte **povolit ověřování SAML**.

    ![Správa](./media/sugarcrm-tutorial/ic795890.png "správy")

1. V **ověřování SAML** části, proveďte následující kroky:

    ![Ověřování SAML](./media/sugarcrm-tutorial/ic795891.png "ověřování SAML")  

    a. V **přihlašovací adresa URL** textového pole vložte hodnotu **přihlašovací adresa URL**, který jste zkopírovali z portálu Azure portal.
  
    b. V **adresa URL cíle na úrovni služby** textového pole vložte hodnotu **odhlašovací adresa URL**, který jste zkopírovali z portálu Azure portal.
  
    c. Otevřete váš certifikát base-64 kódovaných v poznámkovém bloku, zkopírujte obsah ho do schránky a vložte celý certifikát do **certifikát X.509** textového pole.
  
    d. Klikněte na **Uložit**.

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

1. Na webu Azure Portal, v levém podokně vyberte **Azure Active Directory**vyberte **uživatelé**a pak vyberte **všichni uživatelé**.

    !["Uživatele a skupiny" a "Všechny uživatele" odkazy](common/users.png)

2. Vyberte **nového uživatele** v horní části obrazovky.

    ![Tlačítko Nový uživatel](common/new-user.png)

3. Ve vlastnosti uživatele proveďte následující kroky.

    ![Dialogové okno uživatele](common/user-properties.png)

    a. V **název** zadat **BrittaSimon**.
  
    b. V **uživatelské jméno** typ pole `brittasimon@yourcompanydomain.extension`  
    Například BrittaSimon@contoso.com.

    c. Vyberte **zobrazit heslo** zaškrtněte políčko a zapište si hodnotu, která se zobrazí v poli heslo.

    d. Klikněte na možnost **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části je povolit Britta Simon používat jednotné přihlašování Azure díky udělení přístupu k Sugar CRM.

1. Na webu Azure Portal, vyberte **podnikové aplikace**vyberte **všechny aplikace**a pak vyberte **Sugar CRM**.

    ![Okno aplikace organizace](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **Sugar CRM**.

    ![Odkaz Sugar CRM v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **přidat uživatele** tlačítko a pak vyberte **uživatelů a skupin** v **přidat přiřazení** dialogového okna.

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V **uživatelů a skupin** dialogové okno Vybrat **Britta Simon** v seznamu uživatelů, klikněte **vyberte** tlačítko v dolní části obrazovky.

6. Pokud očekáváte libovolnou hodnotu role v kontrolní výraz SAML a potom v **vybrat roli** dialogové okno vybrat vhodnou roli pro uživatele ze seznamu, klikněte **vyberte** tlačítko v dolní části obrazovky.

7. V **přidat přiřazení** dialogové okno kliknutím **přiřadit** tlačítko.

### <a name="create-sugar-crm-test-user"></a>Vytvořit testovacího uživatele Sugar CRM

Chcete-li povolit Azure AD uživatelům umožní přihlásit k Sugar CRM, musí být zřízená s Sugar CRM. V případě Sugar CRM je zřizování úlohu.

**K poskytnutí uživatelského účtu, postupujte následovně:**

1. Přihlaste se k vaší **Sugar CRM** společnosti serveru jako správce.

1. Přejděte na **správce**.

    ![Správce](./media/sugarcrm-tutorial/ic795888.png "správce")

1. V **správu** klikněte na tlačítko **Správa uživatelů**.

    ![Správa](./media/sugarcrm-tutorial/ic795893.png "správy")

1. Přejděte na **uživatelé \> vytvořit nového uživatele**.

    ![Vytvoření nového uživatele](./media/sugarcrm-tutorial/ic795894.png "vytvořit nového uživatele")

1. Na **profilu uživatele** kartu, proveďte následující kroky:

    ![Nový uživatel](./media/sugarcrm-tutorial/ic795895.png "nového uživatele")

    * Typ **uživatelské jméno**, **příjmení**, a **e-mailová adresa** platné uživatele Azure Active Directory do související textových polí.
  
1. Jako **stav**vyberte **aktivní**.

1. Na kartě heslo proveďte následující kroky:

    ![Nový uživatel](./media/sugarcrm-tutorial/ic795896.png "nového uživatele")

    a. Do souvisejícího textového pole zadejte heslo.

    b. Klikněte na **Uložit**.

> [!NOTE]
> Můžete použít jakékoli jiné Sugar CRM uživatele účtu nástrojů pro vytváření nebo rozhraní API poskytovaných Sugar CRM uživatelským účtům, zřídit AAD.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na dlaždici Sugar CRM na přístupovém panelu, můžete by měl být automaticky přihlášeni do CRM Sugar, u kterého nastavíte jednotné přihlašování. Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další prostředky

- [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

