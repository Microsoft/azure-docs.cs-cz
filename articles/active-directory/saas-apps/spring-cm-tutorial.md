---
title: 'Kurz: Integrace Azure Active Directory s SpringCM | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a SpringCM.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 4a42f797-ac58-4aca-a8e6-53bfe5529083
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04-08-2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 04ddc09d3d2475afe9be90cf1d5585697b9c36cf
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "65866856"
---
# <a name="tutorial-azure-active-directory-integration-with-springcm"></a>Kurz: Integrace Azure Active Directory s SpringCM

V tomto kurzu se dozvíte, jak integrovat SpringCM s Azure Active Directory (Azure AD).
SpringCM integraci se službou Azure AD poskytuje následující výhody:

* Můžete řídit ve službě Azure AD, který má přístup k SpringCM.
* Můžete povolit uživatelům být automaticky přihlášeni k SpringCM (Single Sign-On) s jejich účty Azure AD.
* Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s SpringCM, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat [bezplatný účet](https://azure.microsoft.com/free/)
* SpringCM jednotného přihlašování povolená předplatného

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu konfigurace a testování v testovacím prostředí Azure AD jednotného přihlašování.

* Podporuje SpringCM **SP** jednotné přihlašování zahájené pomocí

## <a name="adding-springcm-from-the-gallery"></a>Přidání SpringCM z Galerie

Konfigurace integrace SpringCM do služby Azure AD, budete muset přidat SpringCM z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat SpringCM z galerie, postupujte následovně:**

1. V  **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte **Azure Active Directory** ikonu.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte do **podnikové aplikace** a pak vyberte **všechny aplikace** možnost.

    ![V okně podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **SpringCM**vyberte **SpringCM** na panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

    ![SpringCM v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části je konfigurace a testování Azure AD jednotné přihlašování pomocí SpringCM podle testovacího uživatele volá **Britta Simon**.
Pro jednotné přihlašování pro práci je potřeba navázat vztah odkazu mezi uživatele služby Azure AD a související uživatelské v SpringCM.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s SpringCM, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Konfigurace SpringCM Single Sign-On](#configure-springcm-single-sign-on)**  – ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
3. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
4. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
5. **[Vytvořit testovacího uživatele SpringCM](#create-springcm-test-user)**  – Pokud chcete mít protějšek Britta Simon SpringCM, který je propojený s Azure AD reprezentace uživatele.
6. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure portal.

Ke konfiguraci Azure AD jednotné přihlašování s SpringCM, proveďte následující kroky:

1. V [webu Azure portal](https://portal.azure.com/)na **SpringCM** integrace stránce aplikace vyberte **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz](common/select-sso.png)

2. Na **vybrat jedinou metodu přihlašování** dialogového okna, vyberte **SAML/WS-Fed** chcete povolit jednotné přihlašování.

    ![Jednotné přihlašování režim výběru](common/select-saml-option.png)

3. Na **nastavte si jednotné přihlašování pomocí SAML** klikněte na **upravit** ikony otevřete **základní konfiguraci SAML** dialogového okna.

    ![Upravit konfiguraci základní SAML](common/edit-urls.png)

4. Na **základní konfiguraci SAML** části, proveďte následující kroky:

    ![SpringCM domény a adresy URL jednotného přihlašování – informace](common/sp-signonurl.png)

    V **přihlašovací adresa URL** textové pole, zadejte adresu URL, pomocí následujícího vzorce:  `https://na11.springcm.com/atlas/SSO/SSOEndpoint.ashx?aid=<identifier>`

    > [!NOTE]
    > Hodnota není skutečný. Aktualizujte příslušnou hodnotu skutečné přihlašovací adresa URL. Kontakt [tým podpory SpringCM klienta](https://knowledge.springcm.com/support) má být získána hodnota. Můžete také odkazovat na tyto vzory se dají ukazuje **základní konfiguraci SAML** části webu Azure Portal.

4. Na **nastavte si jednotné přihlašování pomocí SAML** stránku, **podpisový certifikát SAML** klikněte na tlačítko **Stáhnout** ke stažení **certifikát (Raw)** z se zadanými možnostmi podle vašich požadavků a uložit je ve vašem počítači.

    ![Odkaz ke stažení certifikátu](common/certificateraw.png)

6. Na **nastavení SpringCM** tématu, zkopírujte příslušné adresy URL podle vašich požadavků.

    ![Zkopírování adresy URL konfigurace](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor Azure AD

    c. Adresa URL – odhlášení

### <a name="configure-springcm-single-sign-on"></a>Konfigurace SpringCM jednotné přihlašování

1. V okně jiné webové prohlížeče, přihlaste se k vaší **SpringCM** společnosti serveru jako správce.

1. V nabídce v horní části klikněte na tlačítko **přejít na**, klikněte na tlačítko **Předvolby**a pak na **předvolby účtu** klikněte na tlačítko **SAML SSO**.
   
    ![JEDNOTNÉ PŘIHLAŠOVÁNÍ SAML](./media/spring-cm-tutorial/ic797051.png "SAML SSO")

1. V části Konfigurace zprostředkovatele Identity proveďte následující kroky:
   
    ![Konfigurace zprostředkovatele identity](./media/spring-cm-tutorial/ic797052.png "konfigurace zprostředkovatele Identity")
    
    a. Chcete-li nahrát svůj certifikát stažený Azure Active Directory, klikněte na tlačítko **vyberte certifikát, Issuer** nebo **změnu vystavitele certifikátu**.
    
    b. V **vystavitele** vložit do textového pole **Azure AD identifikátor** hodnotu, kterou jste zkopírovali z portálu Azure portal.
    
    c. V **koncový bod iniciované poskytovatele služeb (SP)** vložit do textového pole **přihlašovací adresa URL** hodnotu, kterou jste zkopírovali z portálu Azure portal.
            
    d. Vyberte **povoleno SAML** jako **povolit**.

    e. Klikněte na **Uložit**.

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

V této části je povolit Britta Simon k udělení přístupu k SpringCM použití Azure jednotného přihlašování.

1. Na webu Azure Portal, vyberte **podnikové aplikace**vyberte **všechny aplikace**a pak vyberte **SpringCM**.

    ![Okno aplikace organizace](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **SpringCM**.

    ![Odkaz SpringCM v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **přidat uživatele** tlačítko a pak vyberte **uživatelů a skupin** v **přidat přiřazení** dialogového okna.

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V **uživatelů a skupin** dialogové okno Vybrat **Britta Simon** v seznamu uživatelů, klikněte **vyberte** tlačítko v dolní části obrazovky.

6. Pokud očekáváte libovolnou hodnotu role v kontrolní výraz SAML a potom v **vybrat roli** dialogové okno vybrat vhodnou roli pro uživatele ze seznamu, klikněte **vyberte** tlačítko v dolní části obrazovky.

7. V **přidat přiřazení** dialogové okno kliknutím **přiřadit** tlačítko.

### <a name="create-springcm-test-user"></a>Vytvoření SpringCM testovacího uživatele

Pokud chcete povolit Azure Active Directory uživatelům umožní přihlásit k SpringCM, musí být poskytnuty do SpringCM. V případě SpringCM zřizování se ruční úlohy.

> [!NOTE]
> Další informace najdete v tématu [vytvářet a upravovat uživatele SpringCM](https://knowledge.springcm.com/create-and-edit-a-springcm-user). 

**Ke zřízení uživatelského účtu pro SpringCM, proveďte následující kroky:**

1. Přihlaste se k vaší **SpringCM** společnosti serveru jako správce.

1. Klikněte na tlačítko **GOTO**a potom klikněte na tlačítko **adresář**.
   
    ![Vytvoření uživatele](./media/spring-cm-tutorial/ic797054.png "vytvoření uživatele")

1. Klikněte na tlačítko **vytvořit uživatele**.

1. Vyberte **Role uživatele**.

1. Vyberte **poslat aktivační E-mail**.

1. Zadejte jméno, příjmení a e-mailovou adresu platného účtu uživatele Azure Active Directory, které chcete zřídit do související textových polí.

1. Přidejte uživatele do **skupiny zabezpečení**.

1. Klikněte na **Uložit**.

   > [!NOTE]
   > Můžete použít jakékoli jiné SpringCM uživatelského účtu nástrojů pro vytváření nebo rozhraní API poskytovaných SpringCM uživatelským účtům, zřídit AAD.

### <a name="test-single-sign-on"></a>Otestovat jednotné přihlašování 

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na dlaždici SpringCM na přístupovém panelu, můžete by měl být automaticky přihlášeni k SpringCM, u kterého nastavíte jednotné přihlašování. Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další materiály

- [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

